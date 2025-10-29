# Resumen: DNS — Recordando direcciones

El **Domain Name System (DNS)** es el servicio que convierte **nombres de dominio** en **direcciones IP**, evitando que tengamos que recordar números como `93.184.215.14`. DNS opera en la **capa de aplicación (Capa 7)** del modelo OSI.

---

## 🔌 Puertos y transporte
- **UDP puerto 53** (predeterminado para consultas rápidas).  
- **TCP puerto 53** (fallback o para transferencias de zona y respuestas grandes).

---

## 📄 Registros DNS importantes

- **A (Address)**: Mapea un nombre de host a una dirección **IPv4**.  
  Ej.: `example.com → 172.17.2.172`

- **AAAA (quad-A)**: Mapea un nombre de host a una dirección **IPv6**.  
  (Recuerda: AAAA, no confundir con AA o AAA).

- **CNAME (Canonical Name)**: Alias que apunta un dominio a **otro dominio**.  
  Ej.: `www.example.com → example.com`

- **MX (Mail Exchange)**: Define los **servidores de correo** responsables de un dominio.

---

## 🔎 ¿Cómo funciona una resolución de nombre?

1. Tu navegador solicita la IP asociada a `www.example.com`.  
2. Se envía una consulta DNS (tipo A para IPv4).  
3. El servidor DNS responde con la dirección IP (si está en caché o es autoritativo).  
4. Si también hay IPv6, el cliente puede hacer una consulta AAAA separada.

---

## 🛠 Herramientas de línea de comandos

- **nslookup**: Consulta rápida de registros DNS.
  ```bash
  nslookup www.example.com
  ```
  Ejemplo de salida:
  ```
  Name:   www.example.com
  Address: 93.184.215.14
  Address: 2606:2800:21f:cb07:6820:80da:af6b:8b2c
  ```

- **tshark / tcpdump**: Para inspeccionar las consultas/respuestas DNS en paquetes.
  Ejemplo (`tshark` mostrando A y AAAA queries):
  ```
  user@TryHackMe$ tshark -r dns-query.pcapng -Nn
      1 0.000000000 192.168.66.89 → 192.168.66.1 DNS 86 Standard query 0x2e0f A www.example.com OPT
      2 0.059049584 192.168.66.1 → 192.168.66.89 DNS 102 Standard query response 0x2e0f A www.example.com A 93.184.215.14 OPT
      3 0.059721705 192.168.66.89 → 192.168.66.1 DNS 86 Standard query 0x96e1 AAAA www.example.com OPT
      4 0.101568276 192.168.66.1 → 192.168.66.89 DNS 114 Standard query response 0x96e1 AAAA www.example.com AAAA 2606:2800:21f:cb07:6820:80da:af6b:8b2c OPT
  ```

---

## 💡 Notas prácticas

- Los **resolvers** locales (ISP o router) suelen cachear las respuestas para acelerar futuras consultas.  
- Una consulta DNS puede involucrar varios servidores si no está en caché (root → TLD → autoritativo).  
- Para depurar problemas de correo, revisa el registro **MX**.  
- Para depurar conectividad IP/IPv6, consulta tanto **A** como **AAAA**.

---

## 🧠 Resumen rápido

- **DNS** traduce dominios legibles por humanos a **IPs**.  
- Registros clave: **A**, **AAAA**, **CNAME**, **MX**.  
- Usa **UDP:53** y en algunos casos **TCP:53**.  
- Herramientas útiles: `nslookup`, `dig`, `tshark`, `tcpdump`.

------------------------------------------------------------------------------------------------------------------------------------------------

# Resumen: WHOIS — Información de registros de dominio

El comando y protocolo **WHOIS** permite consultar información pública sobre quién registró un **nombre de dominio**, quién lo administra y otros datos técnicos o de registro.

---

## 🌍 ¿Qué es WHOIS?

Cuando registras un dominio (por ejemplo, `example.com`), debes proporcionar información de contacto y técnica.  
Esta información se almacena en una **base de datos pública WHOIS**, administrada por registradores y organismos regionales (como ICANN, RIPE, ARIN, etc.).

---

## 🧠 ¿Qué información muestra WHOIS?

Un registro WHOIS puede incluir:

- 📅 **Fechas**:
  - Creación del dominio.
  - Última actualización.
  - Fecha de expiración del registro.

- 👤 **Datos del registrante**:
  - Nombre, organización y dirección.
  - Correo electrónico y teléfono de contacto.

- 🏢 **Registrador**:
  - Empresa que gestionó el registro (GoDaddy, Namecheap, Google Domains, etc.).
  - Contactos de abuso o soporte.

- 🌐 **Servidores DNS** asociados al dominio.

---

## 🧰 Uso del comando `whois`

Ejemplo de uso desde terminal Linux:
```bash
whois example.com
```

Salida (simplificada):
```
Domain Name: EXAMPLE.COM
Registrar: GoDaddy.com, LLC
Creation Date: 1993-04-02T00:00:00Z
Updated Date: 2017-07-05T16:02:43Z
Registry Expiry Date: 2026-10-20T14:56:17Z
Registrant Name: Registration Private
Registrant Organization: Domains By Proxy, LLC
Registrar Abuse Contact Email: abuse@godaddy.com
```

---

## 🔒 Protección de privacidad

Los usuarios pueden **ocultar su información personal** utilizando servicios como **Domains By Proxy** o **Whois Privacy Protection**.

En esos casos, los datos del registrante real no se muestran, y en su lugar aparecen los del **servicio intermediario**, protegiendo la identidad del propietario del dominio.

---

## 📌 Importancia de WHOIS

- 🔍 **Transparencia**: permite verificar quién posee un dominio.  
- 🛠 **Seguridad**: útil en investigaciones de phishing o abuso de dominios.  
- 💼 **Gestión**: ayuda a comprobar fechas de expiración o renovaciones.

---

## ⚠️ Nota

Aunque WHOIS está siendo reemplazado progresivamente por protocolos más modernos como **RDAP (Registration Data Access Protocol)**, sigue siendo ampliamente usado para consultas rápidas y análisis básicos.


----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------


# Resumen: HTTP(S) — Acceso a la Web

El **Protocolo de Transferencia de Hipertexto (HTTP)** y su versión segura **HTTPS** son los pilares de la comunicación web.  
Permiten que los navegadores se comuniquen con los servidores web a través de la red usando **TCP**.

---

## 🌐 ¿Qué es HTTP(S)?

- **HTTP (Hypertext Transfer Protocol)**: protocolo sin cifrar usado para transferir datos web.  
- **HTTPS (HTTP Secure)**: versión segura de HTTP, cifrada mediante **TLS/SSL**.

**Puertos comunes:**
- HTTP → **80**
- HTTPS → **443**
- Alternativos: **8080**, **8443**

---

## 🔧 Métodos HTTP principales

| Método | Descripción |
|--------|--------------|
| **GET** | Solicita datos del servidor (por ejemplo, un archivo HTML o una imagen). |
| **POST** | Envía nuevos datos al servidor (formularios, subidas de archivos, etc.). |
| **PUT** | Crea o actualiza un recurso en el servidor. |
| **DELETE** | Elimina un recurso específico del servidor. |

Ejemplo de petición manual:
```http
GET / HTTP/1.1
Host: example.com
```

---

## ⚙️ Comunicación cliente-servidor

1. El navegador envía una **petición HTTP** al servidor (por ejemplo, `GET /index.html`).  
2. El servidor procesa la petición y responde con una **cabecera HTTP** y el **contenido solicitado**.  
3. El navegador interpreta el contenido y lo renderiza visualmente al usuario.  

Ejemplo simplificado de intercambio:
```
Cliente → GET / HTTP/1.1
           Host: example.com

Servidor → HTTP/1.1 200 OK
           Date: Wed, 29 Oct 2025 10:00:00 GMT
           Server: nginx/1.25.2
           Content-Type: text/html
```

---

## 🧠 HTTP vs HTTPS

| Característica | HTTP | HTTPS |
|----------------|------|--------|
| **Cifrado** | ❌ No | ✅ Sí (TLS/SSL) |
| **Puerto predeterminado** | 80 | 443 |
| **Seguridad** | Baja | Alta |
| **Uso recomendado** | Solo redes internas o pruebas | Producción e Internet público |

---

## 🔍 Análisis con herramientas

- **Wireshark**: permite observar peticiones y respuestas HTTP.  
  Muestra encabezados como `User-Agent`, `Server`, `Content-Type`, etc.

- **Telnet o Netcat**: útiles para probar manualmente conexiones HTTP:
  ```bash
  telnet 10.10.159.30 80
  GET / HTTP/1.1
  Host: anything
  ```

- **curl o wget**: herramientas modernas para enviar peticiones HTTP.
  ```bash
  curl -v http://10.10.159.30
  ```

---

## 💡 Curiosidades

- HTTP/1.1 es la versión más extendida; HTTP/2 y HTTP/3 mejoran la velocidad y eficiencia.  
- HTTPS garantiza confidencialidad, autenticidad e integridad mediante certificados digitales.  
- Puedes usar **developer tools (F12)** del navegador para ver las cabeceras HTTP en tiempo real.

---

## 🧾 Resumen rápido

- **HTTP(S)** es el lenguaje entre navegador y servidor.  
- Funciona sobre **TCP** (capa 4).  
- Métodos clave: `GET`, `POST`, `PUT`, `DELETE`.  
- HTTPS añade **cifrado TLS/SSL**.  
- Herramientas útiles: `Wireshark`, `curl`, `telnet`, `nc`, `browser devtools`.


----------------------------------------------------------------------------------------------------------------------------------------------------


# Resumen: FTP — Transferencia de Archivos

El **File Transfer Protocol (FTP)** es un protocolo de nivel de aplicación diseñado específicamente para **transferir archivos** entre un cliente y un servidor a través de una red.  
A diferencia de HTTP, FTP está optimizado para la **subida y descarga de archivos**, logrando velocidades más altas en entornos controlados.

---

## ⚙️ Características principales

- **Protocolo de capa de aplicación** (usa TCP).
- **Puerto predeterminado:** `21` (control).
- **Conexión de datos:** usa un segundo canal TCP para transferir los archivos.
- **Modos de transferencia:**
  - **ASCII:** para archivos de texto.
  - **Binary:** para archivos binarios (imágenes, ejecutables, etc.).

---

## 🔑 Comandos básicos de FTP

| Comando | Descripción |
|----------|-------------|
| `USER` | Enviar el nombre de usuario. |
| `PASS` | Enviar la contraseña del usuario. |
| `RETR` | Descargar (Retrieve) un archivo desde el servidor al cliente. |
| `STOR` | Subir (Store) un archivo desde el cliente al servidor. |
| `LIST` | Listar los archivos disponibles en el directorio remoto. |
| `QUIT` | Cerrar la conexión. |

---

## 🧠 Ejemplo de sesión FTP

Conexión al servidor `10.10.159.30`:

```bash
ftp 10.10.159.30
```

Salida de ejemplo:
```
Connected to 10.10.159.30 (10.10.159.30).
220 (vsFTPd 3.0.5)
Name (10.10.159.30:user): anonymous
331 Please specify the password.
Password:
230 Login successful.
ftp> ls
ftp> type ascii
ftp> get coffee.txt
ftp> quit
```

Pasos destacados:
1. Se inicia sesión con usuario `anonymous` (sin contraseña).  
2. `ls` muestra los archivos disponibles.  
3. `type ascii` cambia el modo de transferencia.  
4. `get coffee.txt` descarga el archivo.  
5. `quit` cierra la sesión.

---

## 🔍 Estructura de la comunicación

FTP utiliza **dos canales TCP separados**:

1. **Canal de control (puerto 21):** para comandos (`USER`, `PASS`, `LIST`, etc.).  
2. **Canal de datos:** para transferencias de archivos o listados de directorios.

El cliente puede usar dos modos:

| Modo | Descripción |
|-------|--------------|
| **Activo** | El servidor inicia la conexión de datos hacia el cliente. |
| **Pasivo (PASV)** | El cliente inicia ambas conexiones, útil para redes con firewalls. |

Ejemplo de modo pasivo:
```
227 Entering Passive Mode (10,10,41,192,134,10).
```

---

## 📡 Análisis con Wireshark

En Wireshark se puede ver:

- **Mensajes del cliente** (en rojo): comandos como `USER`, `PASS`, `LIST`, `RETR`, `STOR`.  
- **Mensajes del servidor** (en azul): respuestas numéricas (`220`, `230`, `226`, etc.).  
- **Canales de datos separados** para listados y descargas de archivos.

Ejemplo de respuesta del servidor:
```
150 Opening BINARY mode data connection for coffee.txt (1480 bytes)
226 Transfer complete
```

---

## 🔐 Consideraciones de seguridad

- FTP transmite **credenciales y datos en texto plano**, sin cifrado.  
- Para entornos seguros, se recomienda usar:
  - **FTPS (FTP Secure):** FTP sobre TLS/SSL.  
  - **SFTP (SSH File Transfer Protocol):** basado en SSH, completamente cifrado.

---

## 🧾 Resumen rápido

- **Puerto:** 21 (control), otro dinámico (datos).  
- **Modos:** Activo / Pasivo.  
- **Comandos clave:** `USER`, `PASS`, `LIST`, `RETR`, `STOR`.  
- **No cifrado por defecto → usar FTPS o SFTP.**


---------------------------------------------------------------------------------------------------------------------------------


# Resumen: SMTP — Envío de Correos Electrónicos

El **Simple Mail Transfer Protocol (SMTP)** define cómo se envían los correos electrónicos a través de la red.  
Opera en la **capa de aplicación (OSI capa 7)** y usa **TCP puerto 25** por defecto.

---

## ✉️ ¿Qué es SMTP?

SMTP establece las reglas que permiten a un **cliente de correo (MUA)** comunicarse con un **servidor de correo (MTA)** y también cómo los **servidores de correo intercambian mensajes** entre sí.

📬 **Analogía:**  
Enviar un correo postal — el cliente entrega el mensaje al servidor (oficina postal), indicando remitente y destinatario antes de que el mensaje sea entregado.

---

## 🔧 Comandos básicos de SMTP

| Comando | Descripción |
|----------|--------------|
| **HELO / EHLO** | Inicia la sesión SMTP e identifica al cliente. |
| **MAIL FROM:** | Define el remitente del mensaje. |
| **RCPT TO:** | Define el destinatario del mensaje. |
| **DATA** | Indica el inicio del contenido del mensaje (encabezados + cuerpo). |
| **.** | Línea única que marca el final del mensaje. |
| **QUIT** | Termina la sesión SMTP. |

---

## 💻 Ejemplo de sesión SMTP con Telnet

Conexión al servidor SMTP (puerto 25):

```bash
telnet 10.10.159.30 25
```

Ejemplo de intercambio:
```
220 example.thm ESMTP Exim 4.95 Ubuntu
HELO client.thm
250 example.thm Hello client.thm [10.11.81.126]
MAIL FROM:<user@client.thm>
250 OK
RCPT TO:<strategos@server.thm>
250 Accepted
DATA
354 Enter message, ending with "." on a line by itself
From: user@client.thm
To: strategos@server.thm
Subject: Telnet email

Hello. I am using telnet to send you an email!
.
250 OK id=1sMrpq-0001Ah-UT
QUIT
221 example.thm closing connection
```

---

## 🧠 Funcionamiento general

1. El cliente inicia sesión con `HELO` o `EHLO`.  
2. Envía el remitente y destinatario (`MAIL FROM`, `RCPT TO`).  
3. Escribe el contenido tras `DATA`.  
4. Finaliza con un punto `.` en una línea vacía.  
5. Cierra con `QUIT`.

---

## 🧩 Puertos comunes

| Protocolo | Puerto | Uso |
|------------|--------|-----|
| **SMTP (sin cifrar)** | 25 | Comunicación estándar servidor-servidor |
| **SMTP con STARTTLS** | 587 | Cliente a servidor (cifrado opcional) |
| **SMTPS (SSL/TLS directo)** | 465 | Cliente a servidor (cifrado desde el inicio) |

---

## 🔍 Análisis con Wireshark

- Mensajes del **cliente (rojo):** comandos SMTP (`HELO`, `MAIL FROM`, etc.).  
- Respuestas del **servidor (azul):** códigos de estado (`250 OK`, `354`, `221`, etc.).  
- Cada mensaje viaja en texto plano, permitiendo ver todo el contenido (a menos que se use TLS).

---

## ⚠️ Seguridad

- SMTP **no cifra los datos por defecto**.  
- Recomendado usar:
  - **STARTTLS (puerto 587)** para cifrado posterior a la conexión.  
  - **SMTPS (puerto 465)** para cifrado inmediato.  
- Sin cifrado, las credenciales y mensajes pueden ser interceptados.

---

## 🧾 Resumen rápido

- **Capa:** Aplicación (OSI 7)  
- **Puerto:** 25 (predeterminado), 465/587 (seguros)  
- **Propósito:** Envío de correos electrónicos  
- **Protocolos relacionados:** POP3 e IMAP (recepción)  
- **Seguridad:** Requiere TLS/SSL para transmisión segura  


----------------------------------------------------------------------------------------------------------------------------------------------------------------

# Resumen: POP3 — Recepción de Correo

**POP3 (Post Office Protocol v3)** es un protocolo sencillo usado para **descargar correos** desde un servidor al cliente de correo local. Opera en la **capa de aplicación (OSI 7)** y usa **TCP puerto 110** por defecto (puede usar TLS en su variante segura, POP3S, en el puerto 995).

---

## 📬 Concepto básico

- POP3 permite que un cliente se conecte a su **buzón remoto**, liste mensajes, descargue correos y marque mensajes para eliminación.  
- Comparación: **SMTP** = enviar al "correo" (oficina), **POP3** = revisar tu **buzón personal** y llevarte las cartas a casa.

---

## 🔑 Comandos comunes de POP3

| Comando | Descripción |
|--------|-------------|
| `USER <usuario>` | Identifica el nombre de usuario. |
| `PASS <contraseña>` | Envía la contraseña del usuario. |
| `STAT` | Solicita número de mensajes y tamaño total (`+OK num size`). |
| `LIST` | Lista mensajes con su tamaño (`msgnum size`). |
| `RETR <msgnum>` | Recupera el mensaje indicado. |
| `DELE <msgnum>` | Marca el mensaje para eliminación al cerrar sesión. |
| `QUIT` | Finaliza la sesión y aplica los cambios (p. ej., eliminaciones). |

---

## 🧾 Ejemplo de sesión POP3 (telnet al puerto 110)

```bash
telnet 10.10.159.30 110
```

Intercambio ejemplo:
```
+OK [XCLIENT] Dovecot (Ubuntu) ready.
USER strategos
+OK
PASS <password>
+OK Logged in.
STAT
+OK 3 1264
LIST
+OK 3 messages:
1 407
2 412
3 445
.
RETR 3
+OK 445 octets
[Contenido del mensaje...]
QUIT
+OK Logging out.
Connection closed by foreign host.
```

- `STAT` indica que hay 3 mensajes con 1264 octetos totales.  
- `LIST` muestra tamaños por mensaje.  
- `RETR 3` descarga el tercer mensaje.  
- `DELE` eliminaría un mensaje marcado una vez que se ejecute `QUIT`.

---

## ⚠️ Seguridad y privacidad

- **POP3 envía credenciales y mensajes en texto plano** por defecto. Cualquiera que capture el tráfico puede leer contraseñas y correos.  
- Usa **POP3S (TLS en puerto 995)** o **STARTTLS** cuando sea posible para cifrar la sesión.  
- POP3 descarga mensajes al cliente; algunos usuarios prefieren **IMAP** si quieren mantener los mensajes sincronizados en múltiples dispositivos.

---

## 🧠 Diferencias clave con IMAP

- **POP3**: diseñado para descargar y (opcionalmente) eliminar del servidor; simple y ligero.  
- **IMAP**: mantiene los mensajes en el servidor y sincroniza carpetas/estados entre múltiples clientes; más adecuado para acceso desde varios dispositivos.

---

## ✅ Resumen rápido

- **Protocolo:** POP3 (Post Office Protocol v3)  
- **Puerto:** 110 (no cifrado), 995 (POP3S cifrado)  
- **Función principal:** descargar correos desde el servidor al cliente  
- **Riesgo:** tráfico y contraseñas en texto plano si no se cifra  
- **Alternativa para sincronización:** IMAP


----------------------------------------------------------------------------------------------------------------------------------------------------------------


# Resumen: IMAP — Sincronización de Correos Electrónicos

El **Internet Message Access Protocol (IMAP)** permite a los usuarios **sincronizar y gestionar sus correos electrónicos** directamente en el servidor, manteniendo la misma vista de la bandeja de entrada desde múltiples dispositivos.  
Opera en la **capa de aplicación (OSI 7)** y usa **TCP puerto 143** por defecto (o **993** para la versión segura IMAPS).

---

## 📬 Concepto básico

- IMAP está diseñado para mantener los correos en el servidor y permitir que varios clientes accedan y sincronicen los mensajes.  
- A diferencia de **POP3**, que descarga los mensajes localmente y puede eliminarlos del servidor, **IMAP los conserva**.  
- Ideal cuando se accede al correo desde múltiples dispositivos (ordenador, móvil, tablet, etc.).

---

## 🔑 Comandos comunes de IMAP

| Comando | Descripción |
|----------|-------------|
| `LOGIN <usuario> <contraseña>` | Autentica al usuario. |
| `SELECT <bandeja>` | Selecciona la carpeta o buzón de correo (por ejemplo, `inbox`). |
| `FETCH <número> body[]` | Recupera el contenido del mensaje especificado. |
| `MOVE <número> <bandeja>` | Mueve mensajes a otra carpeta. |
| `COPY <número> <bandeja>` | Copia mensajes a otra carpeta. |
| `LOGOUT` | Cierra la sesión. |

---

## 💻 Ejemplo de sesión IMAP con Telnet

Conexión al servidor IMAP en el puerto 143:

```bash
telnet 10.10.41.192 143
```

Ejemplo de intercambio:
```
* OK [CAPABILITY IMAP4rev1 STARTTLS AUTH=PLAIN] Dovecot ready.
A LOGIN strategos
A OK Logged in
B SELECT inbox
* 4 EXISTS
B OK [READ-WRITE] Select completed.
C FETCH 3 body[]
* 3 FETCH (BODY[] {445}
Return-path: <user@client.thm>
From: user@client.thm
To: strategos@server.thm
Subject: Telnet email

Hello. I am using telnet to send you an email!
)
C OK Fetch completed.
D LOGOUT
* BYE Logging out
D OK Logout completed.
Connection closed by foreign host.
```

---

## 🧠 Funcionamiento general

1. El cliente se autentica con `LOGIN usuario contraseña`.  
2. Selecciona una carpeta (`SELECT inbox`).  
3. Recupera los mensajes con `FETCH`.  
4. Puede copiar, mover o borrar correos.  
5. Cierra la sesión con `LOGOUT`.  

El servidor mantiene la bandeja sincronizada, actualizando el estado de los mensajes (leído, borrado, movido, etc.) en todos los dispositivos.

---

## ⚠️ Seguridad y cifrado

- **IMAP envía credenciales en texto plano** si no se cifra la conexión.  
- Usa **IMAPS (puerto 993)** para cifrado mediante SSL/TLS.  
- Alternativamente, se puede usar **STARTTLS** sobre el puerto 143 para iniciar el cifrado después de la conexión.

---

## 📊 Comparativa rápida: IMAP vs POP3

| Característica | IMAP | POP3 |
|----------------|------|------|
| Correos se mantienen en el servidor | ✅ Sí | ❌ No |
| Sincronización entre dispositivos | ✅ Sí | ❌ No |
| Ideal para varios dispositivos | ✅ Sí | ⚠️ Limitado |
| Descarga local y eliminación | ⚠️ Opcional | ✅ Predeterminado |
| Puerto predeterminado | 143 (993 seguro) | 110 (995 seguro) |

---

## ✅ Resumen rápido

- **Protocolo:** IMAP (Internet Message Access Protocol)  
- **Puerto:** 143 (no cifrado), 993 (IMAPS)  
- **Función:** Sincronizar y gestionar correos en el servidor  
- **Ventaja:** Permite acceso desde múltiples dispositivos sin pérdida de datos  
- **Seguridad:** Recomendado usar TLS/SSL (IMAPS o STARTTLS)  


----------------------------------------------------------------------------------------------------------------------------------------------------------------------------
