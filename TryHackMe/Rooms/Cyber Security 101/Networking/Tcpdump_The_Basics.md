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


--------------------------------------------------------------------------------------------------------------------------------------------------------------------------
