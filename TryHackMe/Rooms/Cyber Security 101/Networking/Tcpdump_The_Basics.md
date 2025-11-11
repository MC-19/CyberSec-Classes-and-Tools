# Tcpdump — Basic Packet Capture

## 🧠 Introducción

**Tcpdump** es una herramienta de línea de comandos utilizada para **capturar y analizar tráfico de red**.  
Permite observar los paquetes que viajan por una interfaz, guardarlos para análisis posterior o filtrarlos en tiempo real.

---

## 🌐 Especificar la Interfaz de Red

El primer paso es indicar **qué interfaz escuchar** con `-i INTERFACE`.

📘 Ejemplos:

| Opción | Descripción |
|--------|--------------|
| `-i any` | Escucha en **todas las interfaces disponibles**. |
| `-i eth0` | Escucha en la interfaz Ethernet. |
| `-i wlan0` | Escucha en la interfaz WiFi. |

Para listar las interfaces disponibles:
```bash
ip address show
# o más corto
ip a s
```

Salida de ejemplo:
```
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN
2: ens5: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 9001 qdisc mq state UP
```

---

## 💾 Guardar los Paquetes Capturados

Puedes guardar el tráfico capturado en un archivo con `-w FILE`.

📘 Ejemplo:
```bash
sudo tcpdump -i eth0 -w captura.pcap
```

> El archivo se suele guardar con extensión `.pcap` y se puede abrir luego con **Wireshark** u otra herramienta similar.

⚠️ **Nota:**  
Cuando usas `-w`, **no se muestran los paquetes en pantalla** durante la captura.

---

## 📂 Leer Paquetes desde un Archivo

Puedes analizar un archivo previamente capturado con `-r FILE`.

📘 Ejemplo:
```bash
sudo tcpdump -r captura.pcap
```

💡 Útil para:
- Analizar comportamientos de protocolo.  
- Estudiar ataques o incidentes registrados en capturas antiguas.  
- Aplicar filtros sin volver a capturar tráfico.

---

## 🔢 Limitar el Número de Paquetes

Si no especificas un límite, Tcpdump **seguirá capturando indefinidamente** hasta presionar `CTRL + C`.  
Puedes limitar la cantidad de paquetes con `-c COUNT`.

📘 Ejemplo:
```bash
sudo tcpdump -i eth0 -c 10
```
➡️ Captura **solo 10 paquetes** y luego se detiene automáticamente.

---

## 🚫 Evitar Resolución de IPs y Puertos

Por defecto, Tcpdump intenta **resolver IPs y nombres de puerto** (por ejemplo, convierte `80` en `http`).  
Esto puede ralentizar el análisis y generar tráfico DNS adicional.

Usa:
| Opción | Descripción |
|---------|-------------|
| `-n` | No resolver nombres de dominio. |
| `-nn` | No resolver nombres ni puertos. |

📘 Ejemplo:
```bash
sudo tcpdump -i ens5 -c 5 -n
```

Salida típica:
```
tcpdump: verbose output suppressed, use -v or -vv for full protocol decode
listening on ens5, link-type EN10MB (Ethernet)
08:55:18.989213 IP 10.10.117.2.22 > 10.11.81.126.53378: Flags [P.], length 196
08:55:18.989446 IP 10.10.117.2.22 > 10.11.81.126.53378: Flags [P.], length 228
08:55:18.989576 IP 10.10.117.2.22 > 10.11.81.126.53378: Flags [P.], length 196
08:55:18.989839 IP 10.10.117.2.22 > 10.11.81.126.53378: Flags [P.], length 196
08:55:18.989958 IP 10.10.117.2.22 > 10.11.81.126.53378: Flags [P.], length 196
```

---

## 🗣️ Modo Verbose (Salida Detallada)

Puedes obtener **más información** de los paquetes con la opción `-v`.

📈 Niveles de detalle:

| Opción | Nivel de detalle |
|---------|------------------|
| `-v` | Muestra TTL, ID, longitud total, opciones IP. |
| `-vv` | Aumenta la cantidad de información mostrada. |
| `-vvv` | Nivel máximo de detalle. |

📘 Ejemplo:
```bash
sudo tcpdump -i eth0 -c 10 -vv
```

---

## 🧩 Resumen de Comandos

| Comando | Descripción |
|----------|--------------|
| `tcpdump -i INTERFACE` | Captura paquetes en una interfaz específica. |
| `tcpdump -w FILE` | Guarda los paquetes capturados en un archivo `.pcap`. |
| `tcpdump -r FILE` | Lee paquetes desde un archivo `.pcap`. |
| `tcpdump -c COUNT` | Captura un número limitado de paquetes. |
| `tcpdump -n` | No resuelve IPs. |
| `tcpdump -nn` | No resuelve IPs ni puertos. |
| `tcpdump -v` | Muestra más detalles; puede aumentarse con `-vv` y `-vvv`. |

---

## 🧠 Ejemplos Prácticos

```bash
# Capturar 50 paquetes en la interfaz eth0 con salida detallada
sudo tcpdump -i eth0 -c 50 -v

# Capturar en la interfaz WiFi (wlo1) y guardar en data.pcap
sudo tcpdump -i wlo1 -w data.pcap

# Capturar en todas las interfaces sin resolver nombres ni puertos
sudo tcpdump -i any -nn
```

---

## ✅ Conclusión

Tcpdump es una herramienta fundamental para **analizar y entender el tráfico de red**.  
Permite capturar, guardar, y revisar paquetes en detalle, con un control completo sobre:

- Qué interfaces escuchar.  
- Cuántos paquetes capturar.  
- Qué nivel de detalle mostrar.  
- Cómo evitar resoluciones innecesarias.

> 💡 *Consejo:* Usa `-w` para guardar las capturas y analizarlas luego con Wireshark; combina `-nn` y `-vv` para un análisis más limpio y detallado.


------------------------------------------------------------------------------------------------------------------------------------------------------------------------


# Tcpdump — Filtering Expressions

## 🧠 Introducción

Aunque `tcpdump` puede ejecutarse sin filtros, **capturaría demasiado tráfico para ser útil**.  
En una red activa, verías miles de paquetes por segundo, por lo que lo ideal es **filtrar solo lo relevante**.  

> 🎯 *“No puedes escuchar a todos a la vez; céntrate en una sola conversación.”*

Las **expresiones de filtrado** permiten definir qué tráfico quieres capturar:  
por **host**, **puerto**, **protocolo** o combinaciones de ellos con operadores lógicos.

---

## 🖥️ Filtrado por Host

Permite capturar **solo los paquetes asociados a una IP o dominio específico**.

📘 Ejemplo básico:
```bash
sudo tcpdump host example.com -w http.pcap
```
🔹 Captura todo el tráfico hacia o desde `example.com` y lo guarda en `http.pcap`.

También puedes ser más específico:
| Filtro | Descripción |
|---------|-------------|
| `src host IP` | Solo los paquetes cuyo origen sea esa IP. |
| `dst host IP` | Solo los paquetes cuyo destino sea esa IP. |

📘 Ejemplo:
```bash
sudo tcpdump src host 192.168.1.10
sudo tcpdump dst host example.org
```

---

## ⚓ Filtrado por Puerto

Permite capturar tráfico de un **puerto o servicio concreto**.  
Por ejemplo, el DNS utiliza los **puertos 53 (UDP/TCP)**.

📘 Ejemplo:
```bash
sudo tcpdump -i ens5 port 53 -n
```
🔹 Muestra consultas DNS (IPv4/IPv6) capturadas por la tarjeta de red.

También puedes filtrar por origen o destino:
| Filtro | Descripción |
|---------|-------------|
| `src port 53` | Solo tráfico desde el puerto 53. |
| `dst port 443` | Solo tráfico hacia el puerto HTTPS. |

📘 Ejemplo:
```bash
sudo tcpdump src port 53
sudo tcpdump dst port 443
```

---

## 🌐 Filtrado por Protocolo

Filtra por **tipo de protocolo** de nivel 3 o 4 (IP, TCP, UDP, ICMP…).

| Filtro | Descripción |
|---------|-------------|
| `ip` | Paquetes IPv4 |
| `ip6` | Paquetes IPv6 |
| `udp` | Tráfico UDP |
| `tcp` | Tráfico TCP |
| `icmp` | Mensajes ICMP (ping, traceroute, etc.) |

📘 Ejemplo:
```bash
sudo tcpdump -i ens5 icmp -n
```
🔹 Muestra solicitudes y respuestas ICMP (`echo request`, `echo reply`, `time exceeded`, etc.).

---

## ⚙️ Operadores Lógicos

Puedes **combinar condiciones** para afinar aún más tus filtros.

| Operador | Descripción | Ejemplo |
|-----------|--------------|----------|
| `and` | Ambos deben cumplirse | `tcpdump host 1.1.1.1 and tcp` |
| `or` | Cualquiera puede cumplirse | `tcpdump udp or icmp` |
| `not` | Excluye una condición | `tcpdump not tcp` |

📘 Ejemplos prácticos:
```bash
# Captura solo tráfico TCP hacia el host 1.1.1.1
sudo tcpdump host 1.1.1.1 and tcp

# Captura solo UDP o ICMP
sudo tcpdump udp or icmp

# Captura todo excepto TCP (mostrará UDP, ICMP, ARP…)
sudo tcpdump not tcp
```

---

## 📊 Resumen de Comandos

| Comando | Descripción |
|----------|-------------|
| `tcpdump host IP` o `tcpdump host HOSTNAME` | Filtra por IP o dominio. |
| `tcpdump src host IP` | Filtra por IP de origen. |
| `tcpdump dst host IP` | Filtra por IP de destino. |
| `tcpdump port PORT_NUMBER` | Filtra por número de puerto. |
| `tcpdump src port PORT_NUMBER` | Filtra por puerto de origen. |
| `tcpdump dst port PORT_NUMBER` | Filtra por puerto de destino. |
| `tcpdump PROTOCOL` | Filtra por protocolo (ip, ip6, udp, tcp, icmp). |

---

## 🧠 Ejemplos Completos

```bash
# Captura tráfico SSH (puerto 22) en todas las interfaces
sudo tcpdump -i any tcp port 22

# Captura tráfico NTP (puerto 123 UDP) en interfaz WiFi
sudo tcpdump -i wlo1 udp port 123

# Captura tráfico HTTPS con example.com y guarda en archivo
sudo tcpdump -i eth0 host example.com and tcp port 443 -w https.pcap

# Leer archivo pcap, mostrar los 5 primeros paquetes sin resolver IPs
tcpdump -r traffic.pcap -c 5 -n

# Contar cuántos paquetes tienen una IP de origen específica
tcpdump -r traffic.pcap src host 192.168.124.1 -n | wc
```

---

## ✅ Conclusión

Las **expresiones de filtrado de Tcpdump** permiten capturar exactamente lo que necesitas, evitando ruido innecesario.  
Combinando filtros por host, puerto y protocolo, junto con operadores lógicos, puedes aislar eventos clave y analizar tráfico de manera eficiente.

> 💡 *Consejo:* Antes de capturar, define con precisión **qué evento o protocolo** te interesa para reducir la cantidad de datos y mejorar la claridad del análisis.


How many packets in traffic.pcap use the ICMP protocol?
  tcpdump -nn -r traffic.pcap icmp | wc -l

What is the IP address of the host that asked for the MAC address of 192.168.124.137?
  tcpdump -nn -r traffic.pcap arp

What hostname (subdomain) appears in the first DNS query?
  tcpdump -nn -r traffic.pcap port 53 and udp

--------------------------------------------------------------------------------------------------------------------------------------------------------------------------



# Tcpdump — Advanced Filtering

## 🧠 Introducción

En análisis de red real, es común manejar **miles o millones de paquetes**.  
Por ello, Tcpdump permite aplicar **filtros avanzados** para aislar tráfico muy específico.  

Podemos, por ejemplo, capturar **solo paquetes mayores o menores a cierto tamaño**, o bien filtrar por **banderas TCP**, **operaciones binarias**, o **contenido de encabezados**.

---

## 📏 Filtrar por Tamaño de Paquete

| Filtro | Descripción |
|---------|-------------|
| `greater LENGTH` | Muestra paquetes con longitud **mayor o igual** a LENGTH. |
| `less LENGTH` | Muestra paquetes con longitud **menor o igual** a LENGTH. |

📘 Ejemplo:
```bash
sudo tcpdump greater 1500
sudo tcpdump less 100
```

---

## ⚙️ Operaciones Binarias

Los filtros avanzados pueden usar **operaciones binarias** (`&`, `|`, `!`) para evaluar bits en encabezados.

| Operador | Nombre | Descripción | Ejemplo binario |
|-----------|---------|-------------|------------------|
| `&` | AND | Devuelve 1 solo si ambos bits son 1 | `1 & 1 = 1` |
| `|` | OR | Devuelve 1 si al menos un bit es 1 | `1 | 0 = 1` |
| `!` | NOT | Invierte el bit | `!1 = 0` |

Estas operaciones se aplican sobre los **bytes del encabezado del protocolo** para comprobar condiciones.

---

## 📦 Referencias a Encabezados

Tcpdump permite acceder al contenido de **bytes específicos de un encabezado** usando esta sintaxis:

```
proto[expr:size]
```

| Elemento | Descripción |
|-----------|-------------|
| `proto` | Protocolo (arp, ether, icmp, ip, ip6, tcp, udp, etc.) |
| `expr` | Posición del byte dentro del encabezado (0 = primer byte) |
| `size` | Cantidad de bytes (opcional: 1, 2 o 4) |

📘 Ejemplo 1  
```bash
ether[0] & 1 != 0
```
➡️ Filtra paquetes enviados a direcciones **multicast**.

📘 Ejemplo 2  
```bash
ip[0] & 0xf != 5
```
➡️ Captura paquetes IP con **opciones adicionales** (no estándar).

---

## 🚩 Filtrado por Banderas TCP

Cada paquete TCP puede tener **banderas** (flags) que indican su estado en la conexión.  
Tcpdump permite filtrar por ellas usando `tcp[tcpflags]`.

| Flag | Nombre | Descripción |
|------|----------|-------------|
| `tcp-syn` | SYN | Solicitud de conexión |
| `tcp-ack` | ACK | Confirmación de recepción |
| `tcp-fin` | FIN | Finalización de conexión |
| `tcp-rst` | RST | Restablecimiento de conexión |
| `tcp-push` | PSH | Envío inmediato de datos |

---

## 🧩 Ejemplos de Filtros por TCP Flags

📘 Solo paquetes con la bandera **SYN**:
```bash
tcpdump "tcp[tcpflags] == tcp-syn"
```

📘 Paquetes con al menos la bandera **SYN** activada:
```bash
tcpdump "tcp[tcpflags] & tcp-syn != 0"
```

📘 Paquetes con al menos las banderas **SYN o ACK** activadas:
```bash
tcpdump "tcp[tcpflags] & (tcp-syn|tcp-ack) != 0"
```

📘 Paquetes con **solo la bandera RST (Reset)**:
```bash
tcpdump "tcp[tcpflags] == tcp-rst"
```

---

## 🔍 Ejemplos Combinados

Puedes combinar expresiones avanzadas con otros filtros.

```bash
# Paquetes TCP con la bandera SYN hacia el puerto 80
sudo tcpdump "tcp[tcpflags] & tcp-syn != 0" and dst port 80

# Paquetes mayores de 15000 bytes
sudo tcpdump greater 15000

# Paquetes ICMP más pequeños de 100 bytes
sudo tcpdump icmp and less 100
```

---

## 🧠 Comandos de Análisis (Ejemplo de TryHackMe)

```bash
# Leer captura y contar paquetes con bandera RST
tcpdump -r traffic.pcap "tcp[tcpflags] == tcp-rst" | wc -l

# Ver el host que envió paquetes > 15000 bytes
tcpdump -r traffic.pcap greater 15000 -n
```

> 💡 *Sugerencia:* El parámetro `-n` evita la resolución DNS y acelera el análisis.

---

## ✅ Conclusión

Los **filtros avanzados de Tcpdump** te permiten ir mucho más allá de los filtros básicos.  
Puedes aislar tráfico por tamaño, analizar bits de encabezado, o buscar eventos específicos de protocolos.

> 🧩 *Dominar los TCP flags es clave para comprender las fases de conexión (SYN, ACK, FIN, RST) y detectar comportamientos sospechosos o ataques.*


How many packets have only the TCP Reset (RST) flag set?
  tcpdump -nn -r traffic.pcap 'tcp[13] == 0x04'
  tcpdump -nn -r traffic.pcap 'tcp[13] == 0x04' | wc -l


What is the IP address of the host that sent packets larger than 15000 bytes?
  tcpdump -nn -r traffic.pcap 'greater 15000'
  tcpdump -nn -r traffic.pcap 'greater 15000' | awk '{print $3}' | cut -d. -f1-4 | sort | uniq


------------------------------------------------------------------------------------------------------------------------------------------------------------------------

# Tcpdump — Displaying Packets

## 🧠 Introducción

`tcpdump` no solo sirve para capturar paquetes, también permite **personalizar cómo se muestran**.  
Esto es clave cuando quieres ver:

- Salida más corta y legible
- Direcciones MAC (capa 2)
- Datos en texto plano (ASCII)
- Datos en hexadecimal
- O **ambas cosas a la vez** (hex + ASCII)

En este tema se cubren 5 opciones muy útiles:

- `-q` — salida breve
- `-e` — cabecera de nivel de enlace (MAC)
- `-A` — datos en ASCII
- `-xx` — datos en hexadecimal
- `-X` — datos en hex y ASCII

---

## 🔍 Ejemplo base (sin opciones de visualización)

```bash
tcpdump -r TwoPackets.pcap
```

Salida típica:

```text
reading from file TwoPackets.pcap, link-type EN10MB (Ethernet), snapshot length 262144
18:59:59.979771 IP 104.18.12.149.https > g5000.45248: Flags [P.], seq 2695955324:2695955349, ack 2856007037, win 16, options [nop,nop,TS val 412758285 ecr 3959057198], length 25
18:59:59.980574 IP g5000.45248 > 104.18.12.149.https: Flags [P.], seq 1:30, ack 25, win 2175, options [nop,nop,TS val 3959057384 ecr 412758285], length 29
```

Muestra:

- Timestamp
- Protocolo (IP)
- Origen y destino (IP:puerto)
- Flags, seq/ack, ventana, opciones, longitud…

---

## ⏩ `-q` — Quick Output (salida breve)

Si quieres una salida **más compacta**, usa `-q`:

```bash
tcpdump -r TwoPackets.pcap -q
```

Salida:

```text
18:59:59.979771 IP 104.18.12.149.https > g5000.45248: tcp 25
18:59:59.980574 IP g5000.45248 > 104.18.12.149.https: tcp 29
```

Solo muestra:

- Timestamp
- IP origen/destino + puerto
- Protocolo + tamaño de datos

Ideal cuando te interesa el flujo general y no los detalles finos del encabezado.

---

## 🧬 `-e` — Mostrar Cabecera de Enlace (MAC)

Para ver **direcciones MAC y datos de capa 2**, añade `-e`:

```bash
tcpdump -r TwoPackets.pcap -e
```

Salida (resumida):

```text
18:59:59.979771 44:df:65:d8:fe:6c > 02:83:1e:40:5d:17, ethertype IPv4 (0x0800), length 91: 104.18.12.149.https > g5000.45248: ...
18:59:59.980574 02:83:1e:40:5d:17 > 44:df:65:d8:fe:6c, ethertype IPv4 (0x0800), length 95: g5000.45248 > 104.18.12.149.https: ...
```

Verás:

- MAC origen y destino
- Tipo de protocolo de capa 3 (IPv4, IPv6, ARP…)
- Longitud total a nivel Ethernet

Muy útil para:

- Analizar **ARP, DHCP** y otros protocolos de capa 2
- Localizar **qué host físico** envía tráfico raro

---

## 🔡 `-A` — Mostrar Datos en ASCII

ASCII = representación de texto legible.  
Con `-A`, `tcpdump` intentará mostrar el contenido como texto:

```bash
tcpdump -r TwoPackets.pcap -A
```

Salida (parte de los datos):

```text
E..M..@.5..)h.....BY.......|.;5}...........
..1...k......j.3.2.....&9a.....-L
```

✅ Bueno para:

- Ver contenido de **peticiones HTTP**, cabeceras, textos en claro
- Revisar protocolos no cifrados

❌ No útil cuando:

- El tráfico está **cifrado** (TLS/HTTPS, SSH, etc.)
- El contenido no es texto (binario, comprimido, etc.)

---

## 🧮 `-xx` — Mostrar Paquetes en Hexadecimal

Para ver **cada byte** del paquete en formato hexadecimal:

```bash
tcpdump -r TwoPackets.pcap -xx
```

Salida (resumida):

```text
0x0000:  0283 1e40 5d17 44df 65d8 fe6c 0800 4500
0x0010:  004d fbd8 4000 3506 d229 6812 0c95 c0a8
...
```

- Cada línea muestra:
  - Offset (`0x0000`, `0x0010`, …)
  - 16 bytes en hex
- Es posible analizar con precisión:
  - Encabezado IP
  - Encabezado TCP/UDP
  - Payload

Es el formato más **crudo y completo**.

---

## 🔀 `-X` — Hex + ASCII (Lo Mejor de Ambos Mundos)

`-X` combina **hexadecimal y ASCII** en paralelo:

```bash
tcpdump -r TwoPackets.pcap -X
```

Salida (resumida):

```text
0x0000:  4500 004d fbd8 4000 3506 d229 6812 0c95  E..M..@.5..)h...
0x0010:  c0a8 4259 01bb b0c0 a0b1 037c aa3b 357d  ..BY.......|.;5}
...
```

Verás:

- A la izquierda ➜ bytes en **hex**
- A la derecha ➜ interpretación **ASCII** (cuando es imprimible)

Ideal para:

- Ver tráfico binario pero localizar texto dentro (p.ej., partes de HTTP, comandos, rutas).
- Analizar protocolos no cifrados con mayor contexto.

---

## 📊 Resumen de Opciones de Visualización

| Comando | Descripción |
|----------|-------------|
| `tcpdump -q` | Salida breve; menos detalles, visión rápida. |
| `tcpdump -e` | Muestra cabecera de enlace (MAC, tipo, longitud). |
| `tcpdump -A` | Muestra datos en formato ASCII. |
| `tcpdump -xx` | Muestra datos en formato hexadecimal. |
| `tcpdump -X` | Muestra datos en hexadecimal **y** ASCII. |

---

## ✅ Conclusión

Las opciones de visualización de `tcpdump` te permiten elegir **el nivel de detalle** que necesitas:

- ¿Solo quieres ver quién habla con quién? 👉 `-q`
- ¿Te interesa la capa 2/MAC? 👉 `-e`
- ¿Buscas texto plano (HTTP, comandos)? 👉 `-A`
- ¿Necesitas ver todo byte a byte? 👉 `-xx` o `-X`

> 💡 *Truco práctico:* combina visualización con filtros (`host`, `port`, `tcp`, `icmp`, etc.) para acotar el tráfico **y** verlo en el formato más útil para tu análisis.


What is the MAC address of the host that sent an ARP request?
  tcpdump -nn -e -r traffic.pcap arp


------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
