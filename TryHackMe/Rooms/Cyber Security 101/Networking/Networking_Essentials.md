# Resumen: DHCP — Obtén mis ajustes de red (DORA)

Cuando conectas un dispositivo a una red (por ejemplo, el WiFi de una cafetería) suele configurarse **automáticamente** sin que tengas que introducir direcciones IP a mano. Esto es gracias a **DHCP** (Dynamic Host Configuration Protocol).

---

## ¿Qué configura DHCP?
Al menos necesita asignarse automáticamente:
- **IP** y **máscara de subred**  
- **Puerta de enlace (gateway)**  
- **Servidor DNS**

DHCP evita conflictos de direcciones y facilita la movilidad (ordenadores portátiles, móviles, etc.).

---

## ¿Cómo funciona DHCP? — DORA (Discover, Offer, Request, Acknowledge)

DHCP es un protocolo de aplicación que usa **UDP**:
- Servidor DHCP escucha en **UDP puerto 67**
- Cliente envía desde **UDP puerto 68**

El flujo típico (DORA):

1. **DHCP Discover** (cliente → broadcast):  
   El cliente, sin configuración IP, envía un paquete de broadcast (`0.0.0.0 → 255.255.255.255`) buscando servidores DHCP.

2. **DHCP Offer** (servidor → cliente):  
   El/los servidor(es) responden con una oferta (`DHCPOFFER`) que incluye una IP disponible y parámetros de red.

3. **DHCP Request** (cliente → broadcast):  
   El cliente acepta una oferta enviando `DHCPREQUEST` indicando la oferta elegida.

4. **DHCP Acknowledge** (servidor → cliente):  
   El servidor confirma la asignación con `DHCPACK`. A partir de aquí el cliente configura la interfaz con la IP y demás parámetros.

---

## Ejemplo (salida de `tshark` en un pcap)

```
user@TryHackMe$ tshark -r DHCP-G5000.pcap -n
    1   0.000000      0.0.0.0 → 255.255.255.255 DHCP 342 DHCP Discover - Transaction ID 0xfb92d53f
    2   0.013904 192.168.66.1 → 192.168.66.133 DHCP 376 DHCP Offer    - Transaction ID 0xfb92d53f
    3   4.115318      0.0.0.0 → 255.255.255.255 DHCP 342 DHCP Request  - Transaction ID 0xfb92d53f
    4   4.228117 192.168.66.1 → 192.168.66.133 DHCP 376 DHCP ACK      - Transaction ID 0xfb92d53f
```

Observaciones:
- En los pasos 1 y 3 el cliente todavía no tiene IP, por eso envía desde `0.0.0.0` y usa broadcast (`255.255.255.255`) y MAC de broadcast `ff:ff:ff:ff:ff:ff`.
- La transacción se identifica con un **Transaction ID** común.
- Al finalizar, el cliente recibe la IP (ej. `192.168.66.133`) y demás parámetros.

---

## ¿Qué entrega típicamente el servidor DHCP?

- **Dirección IP** (con duración de *lease* o arrendamiento).  
- **Máscara de subred**.  
- **Puerta de enlace por defecto** (gateway).  
- **Servidores DNS**.  
- Opcionalmente: **NTP**, opciones de dominio, rutas, etc.

---

## Notas y buenas prácticas

- Los **servidores** suelen tener reservas/leases para no cambiar IPs críticas (p. ej. servidores o impresoras).  
- En redes pequeñas, el router doméstico suele actuar como servidor DHCP.  
- Para servidores o equipos que deben tener IP fija, se configura **estáticamente** (no DHCP), o se asigna una **reserva DHCP** por MAC.  
- DHCP facilita la administración y evita conflictos humanos de configuración.

---

## Resumen rápido

- **DHCP** automatiza la configuración de red: IP, máscara, gateway y DNS.  
- Funciona con un flujo **DORA**: Discover → Offer → Request → Acknowledge.  
- Usa **UDP 67 (server)** y **68 (client)**.  
- Ideal para dispositivos móviles y redes con muchos clientes; en servidores suele preferirse una IP estática o reserva DHCP.

------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

# Resumen: ARP — Vinculando direcciones de Capa 3 con Capa 2

## 🔹 Introducción

Cuando dos hosts se comunican en la misma red local (LAN), un **paquete IP (Capa 3)** debe viajar dentro de una **trama Ethernet o WiFi (Capa 2)**.  
Aunque el host conoce la dirección **IP** del destino, necesita también su **dirección MAC** para construir el encabezado de la trama.

El **Protocolo de Resolución de Direcciones (ARP, Address Resolution Protocol)** cumple esta función:  
👉 Traducir **direcciones IP (Capa 3)** en **direcciones MAC (Capa 2)**.

---

## 📘 Concepto clave

- Una **dirección MAC** es un identificador físico de 48 bits (por ejemplo: `7C:DF:A1:D3:8C:5C`).
- En Ethernet/WiFi, los dispositivos **no conocen todas las MACs** de la red; solo aprenden las necesarias cuando necesitan comunicarse.

Por ejemplo:
- Tu equipo sabe la IP del **router** (gateway) y del **DNS**, pero no sus MACs hasta que intenta enviarles un paquete.  
- ARP se encarga de descubrirlas en ese momento.

---

## 🧩 Estructura de una trama Ethernet

Una **trama Ethernet** incluye:
- **MAC destino**
- **MAC origen**
- **Tipo** (por ejemplo, `IPv4`)
- **Datos** (el paquete IP encapsulado)

Wireshark muestra estos tres campos claramente cuando se analiza una comunicación local.

---

## ⚙️ Funcionamiento de ARP

### Escenario
Un host con IP `192.168.66.89` necesita enviar datos a otro host con IP `192.168.66.1`.

### Pasos:

1. **ARP Request (petición):**
   - El host pregunta: *“¿Quién tiene 192.168.66.1? Respóndeme a 192.168.66.89”*  
   - Se envía desde la MAC de origen al **broadcast MAC** `ff:ff:ff:ff:ff:ff` (todos los dispositivos de la red).

2. **ARP Reply (respuesta):**
   - El host con IP `192.168.66.1` responde: *“192.168.66.1 está en 44:df:65:d8:fe:6c”*  
   - A partir de ahora, el emisor puede enviar tramas directamente a esa MAC.

---

## 📡 Ejemplo de captura (Wireshark / tshark)

```
user@TryHackMe$ tshark -r arp.pcapng -Nn
1 0.000000000 cc:5e:f8:02:21:a7 → ff:ff:ff:ff:ff:ff ARP 42 Who has 192.168.66.1? Tell 192.168.66.89
2 0.003566632 44:df:65:d8:fe:6c → cc:5e:f8:02:21:a7 ARP 42 192.168.66.1 is at 44:df:65:d8:fe:6c
```

O en **tcpdump**:

```
user@TryHackMe$ tcpdump -r arp.pcapng -n -v
17:23:44.506615 ARP, Ethernet (len 6), IPv4 (len 4), Request who-has 192.168.66.1 tell 192.168.66.89, length 28
17:23:44.510182 ARP, Ethernet (len 6), IPv4 (len 4), Reply 192.168.66.1 is-at 44:df:65:d8:fe:6c, length 28
```

---

## 📦 Importante

- Los mensajes ARP **no se encapsulan** dentro de IP o UDP, sino directamente en una **trama Ethernet**.  
- Por ello, se considera un protocolo **de Capa 2** (aunque se asocia con funciones de Capa 3).

---

## 🧠 Conclusión

| Concepto | Capa | Función |
|-----------|------|----------|
| **IP** | Capa 3 (Red) | Direccionamiento lógico y enrutamiento |
| **MAC** | Capa 2 (Enlace de datos) | Identificación física en la red local |
| **ARP** | 2 ↔ 3 | Traduce direcciones IP en direcciones MAC |

> ARP permite que los dispositivos de una misma red local **se encuentren a nivel físico** usando direcciones IP lógicas.

------------------------------------------------------------------------------------------------------------------------------------------------------

# Resumen: ICMP — Diagnóstico de Redes

El **Internet Control Message Protocol (ICMP)** se utiliza principalmente para **diagnóstico de redes** y reporte de errores. Dos herramientas populares que se apoyan en ICMP son `ping` y `traceroute` (en Windows `tracert`), fundamentales para solucionar problemas de conectividad y analizar rutas de red.

---

## 🔹 ICMP: ¿para qué sirve?

- Envío de mensajes de error y control entre dispositivos de red.  
- Soporte para herramientas de diagnóstico (p. ej., `ping`, `traceroute`).  
- No transmite datos de aplicación; opera para control y señalización en la capa de red.

---

## 🏓 Ping (Echo Request / Echo Reply)

- `ping` envía **ICMP Echo Request** (Tipo 8) y espera **ICMP Echo Reply** (Tipo 0).  
- Permite comprobar si un host está **activo** y medir el **RTT (Round-Trip Time)**.

**Ejemplo (Linux):**
```bash
ping 192.168.11.1 -c 4
```
**Salida típica:**
```
64 bytes from 192.168.11.1: icmp_seq=1 ttl=63 time=11.2 ms
...
--- 192.168.11.1 ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 3003ms
rtt min/avg/max/mdev = 3.805/10.596/23.366/7.956 ms
```
- `-c 4` limita a 4 solicitudes.  
- El resultado muestra **pérdida de paquetes** y estadísticas RTT (mín, avg, max, mdev).  
- Un firewall puede bloquear ICMP, impidiendo respuestas aun cuando el host esté en línea.

---

## 🧭 Traceroute / tracert

`traceroute` (Unix/Linux) o `tracert` (Windows) se usa para **descubrir la ruta** desde tu equipo hasta un destino final.  
Funciona explotando el campo **TTL (Time-to-Live)** de los paquetes:

- Se envían paquetes con TTL incrementales (1, 2, 3, ...).  
- Cada router que decremente el TTL a 0 envía un **ICMP Time Exceeded** (Tipo 11) de vuelta, revelando su identidad.  
- Así se van registrando los saltos intermedios hasta alcanzar el destino.

**Ejemplo:**
```bash
traceroute example.com
```
**Salida abreviada:**
```
 1  _gateway (192.168.66.1)  4.414 ms  4.342 ms  4.320 ms
 2  192.168.11.1 (192.168.11.1)  5.849 ms  5.830 ms  5.811 ms
 3  100.104.0.1 (100.104.0.1)  11.130 ms  11.111 ms  11.093 ms
 5  * * *
...
16  93.184.215.14 (93.184.215.14)  140.574 ms  140.543 ms  140.514 ms
```
- `* * *` indica que el router no respondió o que los mensajes ICMP fueron bloqueados.  
- Algunos routers pueden mostrar IPs privadas (ej. 10.x, 172.16.x, 192.168.x) o públicas, y en algunos casos nombres de dominio asociados.

---

## ⚠️ Limitaciones y consideraciones

- **Filtros y firewalls:** Muchos dispositivos bloquean ICMP o limitan respuestas (especialmente en Internet público).  
- **Asimetría de rutas:** La ruta de ida puede diferir de la de vuelta; traceroute muestra la ruta que envía los paquetes con TTL manipulado, no necesariamente la ruta de retorno.  
- **Rutas cambiantes:** El camino entre origen y destino puede variar entre ejecuciones.  
- **Privacidad:** Algunos routers ocultan su identidad por razones de seguridad (no responden al TTL expirado).

---

## ✅ Uso combinado para diagnóstico rápido

- `ping <IP/host>` → ¿está vivo el host? ¿RTT aceptable? ¿Hay pérdida de paquetes?  
- `traceroute <host>` → ¿qué routers intermedios existen entre origen y destino? ¿dónde se producen pérdidas o latencias altas?

Ejemplo de flujo de diagnóstico:
1. `ping -c 4 <host>` → si falla, probar con `traceroute <host>` para localizar el salto problemático.  
2. Verificar políticas de firewall o filtros ICMP si los comandos no devuelven información.  

---

## 🧠 Resumen rápido

- **ICMP** es clave para diagnóstico y control en redes.  
- **ping** (Echo Request/Reply) mide conectividad y RTT.  
- **traceroute** usa TTL e ICMP Time Exceeded para mapear la ruta.  
- Bloqueos de ICMP y routers que no responden pueden limitar la información obtenida.

------------------------------------------------------------------------------------------------------------------------------------------------

# Resumen: Enrutamiento (Routing)

El **enrutamiento (routing)** es el proceso mediante el cual los routers determinan el mejor camino que deben seguir los paquetes para viajar desde un origen hasta un destino a través de una red — o múltiples redes interconectadas (como Internet).

---

## 🌐 Concepto básico

Cada router actúa como un **nodo de decisión**, eligiendo la mejor ruta entre varias posibles.  
Para lograr esto, los routers mantienen **tablas de enrutamiento** que les indican qué interfaz deben usar para llegar a una red o dirección IP determinada.

---

## 🧩 Ejemplo simplificado

Imagina tres redes conectadas a Internet mediante sus propios routers. Para que los paquetes viajen entre ellas, cada router debe saber **a qué otro router** enviar los datos.

En una red real (con millones de routers y miles de millones de dispositivos), existen **múltiples caminos posibles** entre dos puntos. Por eso se utilizan **protocolos de enrutamiento**, que permiten a los routers compartir información y determinar las rutas más eficientes.

---

## 🧠 Tipos de enrutamiento

1. **Estático:**  
   - Las rutas son configuradas manualmente por un administrador.  
   - Útil en redes pequeñas o simples.  
   - No se adapta automáticamente a cambios o fallos en la red.

2. **Dinámico:**  
   - Los routers intercambian información y actualizan sus tablas de forma automática.  
   - Usa **protocolos de enrutamiento dinámico**.  
   - Se adapta a los cambios de topología o congestión.

---

## 🔄 Protocolos de enrutamiento más comunes

### 1. 🧭 **OSPF (Open Shortest Path First)**
- Tipo: **Protocolo de gateway interior (IGP)**.  
- Abierto y estandarizado.  
- Utiliza el algoritmo de **estado de enlace (link-state)**.  
- Los routers intercambian información sobre sus enlaces y construyen un **mapa completo de la red**.  
- Cada router calcula las mejores rutas mediante el **algoritmo Dijkstra (Shortest Path First)**.

**Ventajas:**
- Escalable y eficiente.  
- Detecta rápidamente cambios en la red.  
- Evita bucles de enrutamiento.

---

### 2. ⚙️ **EIGRP (Enhanced Interior Gateway Routing Protocol)**
- Tipo: **IGP propietario de Cisco**.  
- Combina técnicas de **distancia vectorial y estado de enlace** (híbrido).  
- Usa un algoritmo llamado **DUAL (Diffusing Update Algorithm)** para calcular las mejores rutas.  
- Mide métricas como **ancho de banda, retardo y confiabilidad**.

**Ventajas:**
- Convergencia rápida.  
- Bajo consumo de ancho de banda para actualizaciones.  
- Ideal para entornos Cisco.

---

### 3. 🌍 **BGP (Border Gateway Protocol)**
- Tipo: **Protocolo de gateway exterior (EGP)** — es el protocolo principal de **Internet**.  
- Se usa para **interconectar redes grandes o autónomas (AS, Autonomous Systems)**.  
- Los routers BGP intercambian información de rutas entre organizaciones (como proveedores de Internet).  
- Decide las rutas basándose en **políticas** y **prefijos de red**, no solo en distancia.

**Ventajas:**
- Escalable a nivel mundial.  
- Permite control granular sobre el tráfico de entrada y salida.  
- Soporta equilibrio de carga y redundancia.

---

### 4. 🧱 **RIP (Routing Information Protocol)**
- Tipo: **IGP basado en distancia vectorial**.  
- Métrica: número de **saltos (hops)** hasta el destino.  
- Actualiza las tablas de enrutamiento cada 30 segundos.  
- Máximo de 15 saltos; más allá de eso, se considera inalcanzable.

**Ventajas:**
- Simple y fácil de configurar.  
- Ideal para redes pequeñas o de laboratorio.

**Desventajas:**
- Escasa escalabilidad.  
- Convergencia lenta.  
- No considera ancho de banda ni latencia, solo cantidad de saltos.

---

## ⚡ Resumen comparativo

| Protocolo | Tipo | Métrica principal | Escalabilidad | Propietario | Uso principal |
|------------|------|------------------|----------------|--------------|----------------|
| **RIP** | IGP (distancia vectorial) | Saltos (hops) | Baja | No | Redes pequeñas |
| **OSPF** | IGP (estado de enlace) | Coste de enlace | Alta | No | Redes empresariales medianas y grandes |
| **EIGRP** | IGP (híbrido) | Ancho de banda, retardo | Media | Cisco | Redes Cisco |
| **BGP** | EGP | Políticas de prefijos | Muy alta | No | Internet / ISPs |

---

## 🧭 Conclusión

El **enrutamiento** permite que los paquetes encuentren su camino óptimo a través de múltiples redes.  
Sin protocolos como **OSPF**, **BGP**, **RIP** y **EIGRP**, Internet simplemente **no podría funcionar** como la red global que conocemos.

> 🗺️ En pocas palabras: los routers son los "carteros" de Internet, y los protocolos de enrutamiento son las reglas que les indican **por dónde deben entregar cada paquete**.


---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------


# Resumen: NAT — Network Address Translation

## 🌍 Contexto

El **espacio de direcciones IPv4** es limitado: permite un máximo teórico de alrededor de **4.000 millones de direcciones** (2³²).  
Con el crecimiento exponencial de dispositivos conectados (ordenadores, móviles, cámaras, IoT, etc.), este espacio se volvió insuficiente.

Una de las soluciones más efectivas para extender la vida útil de IPv4 es **NAT (Network Address Translation)**.

---

## 🔹 ¿Qué es NAT?

**Network Address Translation** permite que **varios dispositivos privados compartan una única dirección IP pública** para acceder a Internet.  
El router que implementa NAT se encarga de **traducir direcciones internas ↔ externas** de forma automática y transparente.

**Ejemplo:**
> Una empresa con 20 ordenadores puede acceder a Internet usando **una sola IP pública**, en lugar de necesitar 20.

---

## ⚙️ Cómo funciona

Cuando un dispositivo dentro de una red privada (por ejemplo, 192.168.0.129) envía una solicitud a Internet:

1. El router **NAT** cambia la dirección **IP de origen** (y a menudo el puerto TCP/UDP).  
2. Envía el paquete al servidor externo (por ejemplo, un sitio web).  
3. El servidor responde al **IP público del router NAT**.  
4. El router traduce de nuevo la respuesta al **IP privado** original y la entrega al dispositivo.

De este modo, el router mantiene una **tabla de traducción NAT**, que vincula cada conexión saliente con su correspondiente conexión interna.

---

## 🧩 Ejemplo ilustrativo

Supongamos la siguiente red:

| Dispositivo | IP interna | Puerto origen | IP pública (router NAT) | Puerto asignado | IP destino (web) | Puerto destino |
|--------------|-------------|---------------|--------------------------|------------------|------------------|----------------|
| Laptop| 192.168.0.129 | 15401 | 212.3.4.5 | 19273 | 203.0.113.10 | 80 |
| PC | 192.168.0.130 | 15405 | 212.3.4.5 | 19274 | 203.0.113.10 | 80 |

El router **reemplaza las IP y puertos internos** por una IP pública compartida y puertos únicos.  
Cuando llegan las respuestas del servidor web, el router usa su **tabla de traducción** para redirigirlas al dispositivo correcto.

---

## 🔧 Tipos de NAT

| Tipo | Descripción | Uso común |
|------|--------------|------------|
| **Static NAT (1:1)** | Traduce una IP privada a una IP pública fija. | Servidores accesibles desde Internet. |
| **Dynamic NAT (m:1)** | Asigna dinámicamente una IP pública libre a un dispositivo privado. | Redes medianas, acceso controlado. |
| **PAT (Port Address Translation)** o **NAT Overload** | Usa una sola IP pública y distingue conexiones por puertos. | Hogares y empresas: varios usuarios, una IP pública. |

---

## 🔒 Beneficios de NAT

- ✅ **Ahorra direcciones IPv4 públicas.**  
- ✅ **Oculta la red interna**, añadiendo una capa básica de seguridad.  
- ✅ **Permite conexión simultánea** de múltiples dispositivos con una sola IP pública.  
- ✅ **Reduce conflictos de IP** entre redes internas y externas.

---

## ⚠️ Desventajas y limitaciones

- ❌ Dificulta conexiones entrantes (por ejemplo, servidores o juegos P2P).  
- ❌ Complica protocolos que incrustan direcciones IP en el contenido (ej. SIP, FTP).  
- ❌ Añade una pequeña latencia por el proceso de traducción.  
- ❌ No soluciona la escasez de IP de manera definitiva (IPv6 es la verdadera solución).

---

## 🧠 Resumen rápido

| Concepto | NAT |
|-----------|-----|
| **Propósito** | Permitir que múltiples dispositivos compartan una IP pública. |
| **Capa** | Red (Capa 3) |
| **Ventaja principal** | Conserva direcciones IPv4 y mejora la seguridad. |
| **Desventaja** | Dificulta conexiones entrantes y depuración de red. |
| **Alternativa moderna** | IPv6 (direcciones únicas para cada dispositivo). |

---

## 📘 Conclusión

**NAT** es una técnica esencial que ha extendido la vida útil de IPv4 y permitido la expansión de Internet.  
Gracias a NAT, redes domésticas y empresariales pueden funcionar con un solo **IP público**, mientras mantienen internamente un **espacio de direcciones privadas** (por ejemplo, 192.168.x.x o 10.x.x.x).

> 🔁 En resumen: NAT traduce direcciones y puertos para permitir que una red privada “hable” con Internet usando una única identidad pública.

---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------


