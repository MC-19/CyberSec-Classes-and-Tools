# Nmap — Host Discovery (The Basics)

**Goal:** find which hosts are _online_ (responding) on a network using Nmap.

---

## Quick summary / cheat‑sheet

- `nmap -sn <target>`  
  Ping‑scan / host discovery only (no port/service scan). Formerly `-sP`.
- `nmap -sL <target>`  
  List targets only (no probes). Good to verify target expansion.
- Run **as root** (or with `sudo`) for best discovery results — Nmap can use ARP, raw packets and privileged probes.
- Common discovery probes:
  - ARP (used automatically on directly connected Ethernet/Wi‑LAN — fastest & most reliable for local subnets)
  - ICMP echo (ping)
  - TCP SYN to common ports (e.g. 80, 443)
  - TCP ACK to common ports
  - UDP to common ports
- Control probe types explicitly:
  - `-PS[ports]` TCP SYN discovery (e.g. `-PS80,443`)
  - `-PA[ports]` TCP ACK discovery
  - `-PU[ports]` UDP discovery
  - `-PP` ICMP timestamp, `-PE` ICMP echo (can mix and match)

---

## Target specification examples

- Single IP: `nmap -sn 10.0.0.5`
- Range: `nmap -sn 192.168.1.1-50`
- CIDR: `nmap -sn 192.168.1.0/24`
- Hostname: `nmap -sn example.local`

---

## Local vs Remote scanning behaviour

### Scanning a **local** directly‑connected subnet
- Nmap will prefer **ARP** probes on Ethernet/Wi‑Fi.  
  ARP requests are answered by devices on the same L2 segment even if IP stack is partially down → very reliable.
- Example:
```bash
sudo nmap -sn 192.168.66.0/24
```
Output includes MAC addresses (because ARP is used).

### Scanning a **remote** subnet (one or more routers away)
- ARP can't be used, so Nmap uses other probes (ICMP, TCP/UDP).
- Some hosts or routers may drop ICMP or certain TCP probes. Nmap will try multiple probe types unless you constrain it.
- Example:
```bash
sudo nmap -sn 192.168.11.0/24
```

---

## Typical Nmap output explained (excerpt)
```
Nmap scan report for MyRouter (192.168.66.1)
Host is up (0.0069s latency).
MAC Address: 44:DF:65:D8:FE:6C (Vendor)
```
- `Host is up` → host responded to one of the discovery probes.
- `MAC Address` present only when scanning L2-local network (ARP responses).

---

## Useful tips & gotchas

- Use `-n` to skip DNS resolution and speed up output when scanning many hosts.
- `-Pn` disables host discovery (assume hosts up) — useful when discovery is blocked but you still want to attempt scans.
- `-v` or `-vv` increases verbosity to help debug what probe types Nmap used.
- Firewalls often block ICMP and some TCP probes → try different probe types (`-PS`, `-PA`, `-PU`) or scan specific ports you expect open.
- Use `--reason` to see why Nmap decided a host is up (which packet/response triggered it).
- Use packet capture (Wireshark/tcpdump) while running a scan to learn which probes and responses are happening:
  - Local subnet: look for ARP requests/replies.
  - Remote subnet: look for ICMP echo/replies, TCP SYN/ACK or RST, ICMP unreachable messages.

---

## Quick examples

Discover live hosts in /24 (local):
```bash
sudo nmap -sn -n 192.168.1.0/24
```

List targets only:
```bash
nmap -sL 10.0.0.0/24
```

SYN probe discovery to ports 80 and 443 only:
```bash
sudo nmap -sn -PS80,443 192.168.11.0/24
```

Show why each host is considered up:
```bash
sudo nmap -sn --reason 192.168.11.0/24
```

Assume hosts are up (skip discovery) and directly scan ports:
```bash
sudo nmap -Pn 192.168.11.5
```

---

## Short walkthrough: interpret a noisy result
If `nmap -sn` reports `Host is up` but your traceroute/tcpdump shows ICMP unreachable from an intermediate router:
- Nmap may have used TCP probes that elicited responses from an intermediate device, or the router generated ICMP responses which Nmap interpreted.
- Use `--reason` and `-vv` + packet capture to confirm true endpoint responses vs intermediate devices.

---

## Further reading
- `man nmap` → full options
- Nmap book / official docs: https://nmap.org/book/  
- pcap analysis with `tcpdump` / `wireshark` to observe actual probe traffic.

---

*Generated: concise Nmap host discovery guide (markdown) — ready for study or inclusion in notes.*


------------------------------------------------------------------------------------------------------------------------------------------------------------------------

# Port Scanning — ¿Quién está escuchando?

Guía práctica (resumen) sobre escaneo de puertos con **Nmap** — cómo descubrir qué servicios escuchan en hosts de red.

---

## 1. Objetivo

Descubrir servicios (procesos) que estén *escuchando* en puertos TCP/UDP de hosts en una red.

---

## 2. Consideraciones previas

- TCP y UDP disponen de 65.535 puertos (1–65535).
- Escanear redes puede generar logs y tráfico evidente: obtén permiso si no es tu red.
- Ejecutar Nmap con privilegios (sudo/root) permite técnicas más potentes (p.ej. SYN scan).

---

## 3. Escaneos TCP principales

### 3.1 Connect scan (`-sT`)

- Intenta completar la *handshake* TCP (3 vías) con cada puerto objetivo.
- Fácil de detectar en logs (conexiones completas).
- Uso:

```bash
sudo nmap -sT TARGET
# Ejemplo: sudo nmap -sT 192.168.1.10
```

### 3.2 SYN scan (escaneo "stealth") (`-sS`)

- Envía SYN y analiza respuesta sin completar la conexión (Nmap responde con RST).
- Menos ruidoso en algunos sistemas; requiere privilegios.
- Uso:

```bash
sudo nmap -sS TARGET
# Ejemplo: sudo nmap -sS 192.168.1.10
```

**Interpretación rápida de respuestas**
- SYN-ACK -> puerto **open** (servicio escuchando)
- RST (inmediato) -> puerto **closed**
- Sin respuesta / ICMP unreachable -> filtrado o bloqueo

---

## 4. Escaneo UDP (`-sU`)

- UDP no es orientado a conexión; Nmap envía datagramas y observa respuestas ICMP "port unreachable".
- Tendrá más falsos positivos/negativos (firewalls, rate limiting).
- Uso (típico):

```bash
sudo nmap -sU TARGET
# Para UDP + TCP simultáneo: sudo nmap -sS -sU TARGET
```

---

## 5. Limitación de puertos y modos rápidos

- `-F` : Fast mode — escanea los 100 puertos más comunes.
- `-p` : especifica puertos o rangos.
  - `-p-` => escanea **todos** los puertos TCP (1–65535).
  - `-p 1-1024` => rango 1–1024.
  - `-p 22,80,443` => puertos específicos.

**Ejemplos**:

```bash
# Escanear los 1000 puertos por defecto (comportamiento estándar)
sudo nmap TARGET

# Escanear todos los puertos TCP
sudo nmap -p- TARGET

# Escaneo rápido de los 100 puertos más comunes
sudo nmap -F TARGET

# Escanear puertos TCP 1-1024 con SYN scan y mostrar servicio/versiones
sudo nmap -sS -sV -p1-1024 TARGET
```

---

## 6. Detección de servicios y versiones

- `-sV` : intenta identificar servicios y versiones (banner grabbing).
- `-A` : detección agresiva (OS, versiones, scripts, traceroute).

```bash
sudo nmap -sS -sV -A TARGET
```

---

## 7. Escaneo silencioso y tácticas para reducir ruido

- Evita `-sT` si quieres menos evidencia en logs — `-sS` (requiere sudo) suele generar menos entradas.
- Usa `--scan-delay` o `--max-retries` para reducir velocidad y evitar IDS/IPS.
- Usa `-Pn` si sabes que el host está up y quieres saltarte la detección de hosts (no enviar pings previos).

Ejemplo de escaneo más lento y sigiloso:

```bash
sudo nmap -sS -p22,80,443 --max-retries 2 --scan-delay 500ms TARGET
```

---

## 8. Escaneo UDP práctico con puertos comunes

```bash
sudo nmap -sU -p 53,67,69,123,161 TARGET
```

Nota: el escaneo UDP suele ser lento — considera combinar `-F` o listar puertos específicos.

---

## 9. Interpretación de resultados (salida típica)

- **open** — hay un servicio escuchando en el puerto.
- **closed** — puerto accesible pero no hay servicio.
- **filtered** — Nmap no puede determinar si está abierto por filtrado (firewall/ACL).

Ejemplo de interpretación:

```
PORT    STATE    SERVICE
22/tcp  open     ssh
80/tcp  filtered http   # un firewall está bloqueando/filtrando
53/udp  open     domain  # DNS servicio UDP detectado
```

---

## 10. Combinar descubrimiento de hosts y escaneo de puertos

Escanea una red completa: descubre hosts y luego escanea puertos comunes en los vivos.

```bash
# 1) Descubrir hosts (ping/arp)
sudo nmap -sn 192.168.1.0/24

# 2) Escanear servicios en hosts detectados (p.ej. todos los hosts vivos)
sudo nmap -sS -sV -p22,80,443 --open 192.168.1.0/24
```

---

## 11. Buenas prácticas y seguridad

- Pide permiso antes de escanear redes que no administras.
- Documenta tus comandos y resultados (auditoría).
- Usa `-oN`/`-oX` para guardar resultados (texto/ XML) y compartirlos.

Ejemplo de salida a archivo:

```bash
sudo nmap -sS -sV -p- TARGET -oN salida_nmap.txt
```

---

## 12. Comandos de ayuda rápidos

- `nmap --help` — ayuda general.
- `man nmap` — manual completo.
- `nmap -h` — ayuda corta.

---

## 13. Referencias útiles

- https://nmap.org/book/ — documentación oficial de Nmap
- `man nmap` — página de manual local

---

----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

# Nmap — Version & OS Detection (Archivo .md)

## Resumen
Este documento recoge las opciones de Nmap para **detección de sistema operativo** y **detección de servicios/versión**, además de combinaciones prácticas y cómo forzar un escaneo cuando el host aparece como "down".

---

## Detección de Sistema Operativo (`-O`)
- `-O` intenta identificar el sistema operativo mediante heurísticas y firmas de red.
- Resultado típico muestra: **Device type**, **Running** (rango estimado de kernels), **OS CPE**, **OS details** y **Network Distance**.

**Ejemplo de salida**:

```txt
root@tryhackme:~# nmap -sS -O 192.168.124.211
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-08-13 13:37 EEST
Nmap scan report for ubuntu22lts-vm (192.168.124.211)
Host is up (0.00043s latency).
Not shown: 999 closed tcp ports (reset)
PORT   STATE SERVICE
22/tcp open  ssh
MAC Address: 52:54:00:54:FA:4E (QEMU virtual NIC)
Device type: general purpose
Running: Linux 4.X|5.X
OS CPE: cpe:/o:linux:linux_kernel:4 cpe:/o:linux:linux_kernel:5
OS details: Linux 4.15 - 5.8
Network Distance: 1 hop

OS detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 1.44 seconds
```

> Nota: la detección de OS **no es 100% fiable**; siempre hay margen de error. Nmap sugiere enviar falsos positivos/negativos a su web si detectas errores.

---

## Detección de Servicio y Versión (`-sV`)
- `-sV` intenta identificar qué servicio está escuchando y su versión (ej. OpenSSH 8.9p1 ...).
- Útil para enumeración rápida y priorizar pruebas de vulnerabilidades basadas en versiones conocidas.

**Ejemplo de salida**:

```txt
root@tryhackme:~# nmap -sS -sV 192.168.124.211
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-08-13 13:33 EEST
Nmap scan report for ubuntu22lts-vm (192.168.124.211)
Host is up (0.000046s latency).
Not shown: 999 closed tcp ports (reset)
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.9p1 Ubuntu 3ubuntu0.10 (Ubuntu Linux; protocol 2.0)
MAC Address: 52:54:00:54:FA:4E (QEMU virtual NIC)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 0.25
```

---

## Modo Combinado: `-A`
- `-A` activa varias características en bloque: OS detection, version detection (`-sV`), traceroute y algunos scripts NSE básicos.
- Conveniente para auditorías rápidas, pero **más ruidoso** y potencialmente más detectado por IDS/IPS.

---

## Forzar escaneo de hosts marcados como "down": `-Pn`
- Si Nmap no consigue detectar un host (por ejemplo ICMP bloqueado), no lanzará el escaneo de puertos por defecto.
- `-Pn` obliga a Nmap a **tratar todos los hosts como online** y procede a escanearlos.

```bash
# Ejemplo: escanear sin discovery (forzar puertos y versión)
sudo nmap -Pn -sS -sV 10.0.0.5
```

---

## Resumen rápido de opciones
- `-O`  — OS detection
- `-sV` — Service/version detection
- `-A`  — OS detection + version detection + traceroute + NSE scripts
- `-Pn` — No host discovery (tratar todos como "up")

---

## Buenas prácticas
- Ejecuta detección de versiones y OS desde una máquina con permisos adecuados (sudo) para mejores resultados.
- Ten en cuenta el ruido: `-A` y `-sV` generan más tráfico que `-sS` simple.
- Corrobora resultados manualmente si dependes de ellos para acciones críticas (falsos positivos posibles).

---

Si quieres, añado:
- un ejemplo con `-A` y salida real desmontada,
- una tabla comparativa de `-sS` vs `-sT` vs `-sV` vs `-A`,
- o un apartado sobre cómo usar `--script` para scripts NSE específicos relacionados con identificación.


