# Resumen: TLS — Seguridad en las Comunicaciones en Red

**TLS (Transport Layer Security)** es un protocolo criptográfico que proporciona **confidencialidad** e **integridad** en las comunicaciones a través de redes inseguras (como Internet).  
Opera en la **capa de transporte (OSI capa 4)** y es la evolución de **SSL (Secure Sockets Layer)**.

---

## 🔒 Breve historia

| Año | Versión | Descripción |
|------|----------|-------------|
| 1995 | **SSL 2.0** | Primer lanzamiento público por Netscape. |
| 1996 | **SSL 3.0** | Base para TLS, más segura. |
| 1999 | **TLS 1.0** | Sustituye a SSL; mejora criptografía y autenticación. |
| 2006 | **TLS 1.1** | Añade protección contra ataques de reinyección. |
| 2008 | **TLS 1.2** | Mejora los algoritmos de cifrado. |
| 2018 | **TLS 1.3** | Rediseño completo; más rápido y seguro. |

---

## ⚙️ Funcionamiento general

1. **Negociación (Handshake):**  
   - El cliente y el servidor se autentican e intercambian claves criptográficas.  
   - El servidor presenta un **certificado digital** para demostrar su identidad.  
   - A partir de TLS 1.3, el handshake es más rápido y eficiente.

2. **Cifrado de la comunicación:**  
   - Los datos transmitidos son cifrados, asegurando **confidencialidad**.  
   - Se usan algoritmos como AES, ChaCha20, o RSA/ECDSA para intercambio de claves.

3. **Integridad y autenticación:**  
   - Se incluyen mecanismos para verificar que los datos no han sido modificados.  
   - Los certificados son emitidos por **Autoridades de Certificación (CA)** confiables.

---

## 🧾 Certificados digitales

- Un **certificado TLS** vincula una clave pública con la identidad del servidor o cliente.  
- Es emitido por una **CA (Certificate Authority)** tras verificar la solicitud (CSR).  
- Los navegadores confían en una lista de CAs preinstaladas para validar certificados.  
- También pueden existir **certificados autofirmados**, que no garantizan autenticidad.  

🔸 Ejemplo: *Let’s Encrypt* ofrece certificados TLS gratuitos y automáticos.

---

## 🔐 Beneficios de TLS

- 🔒 **Confidencialidad:** Los datos viajan cifrados.  
- ✅ **Integridad:** Se detectan alteraciones o manipulaciones.  
- 🧑‍💻 **Autenticación:** Verifica que el servidor (y opcionalmente el cliente) es legítimo.  
- 🌍 **Confianza:** Permite actividades críticas como banca, comercio electrónico o mensajería.

---

## 🌐 Protocolos que usan TLS

Muchos protocolos han adoptado TLS para volverse seguros:

| Protocolo original | Versión segura con TLS | Puerto común |
|--------------------|------------------------|---------------|
| HTTP | **HTTPS** | 443 |
| SMTP | **SMTPS** | 465 |
| POP3 | **POP3S** | 995 |
| IMAP | **IMAPS** | 993 |
| DNS | **DoT (DNS over TLS)** | 853 |
| MQTT | **MQTTS** | 8883 |

En todos los casos, la “S” final indica que el protocolo utiliza **SSL/TLS** para asegurar la comunicación.

---

## ⚠️ Riesgos y consideraciones

- TLS requiere **configuración correcta** del servidor: certificados válidos, versiones seguras y suites de cifrado modernas.  
- **Certificados caducados o autofirmados** pueden causar advertencias de seguridad.  
- Las versiones antiguas de SSL/TLS (SSL 2.0, SSL 3.0, TLS 1.0/1.1) están **obsoletas** por vulnerabilidades.

---

## ✅ Resumen rápido

- **Nombre completo:** Transport Layer Security  
- **Capa OSI:** 4 (Transporte)  
- **Función:** Cifrar y autenticar la comunicación entre cliente y servidor  
- **Versión actual:** TLS 1.3  
- **Certificados:** Emitidos por Autoridades de Certificación (CA) o autofirmados  
- **Usos comunes:** HTTPS, IMAPS, SMTPS, POP3S, VPNs, etc.  

---

**Sin TLS, Internet moderno sería inseguro e inutilizable para la mayoría de las aplicaciones sensibles.**

------------------------------------------------------------------------------------------------------------------------------------------------------------------


# Resumen: HTTPS — HTTP sobre TLS

**HTTPS (Hypertext Transfer Protocol Secure)** es la versión segura de **HTTP**, en la que el tráfico web se cifra mediante el protocolo **TLS (Transport Layer Security)**.  
Opera en la **capa de aplicación (OSI 7)** y usa por defecto el **puerto 443** (en lugar del 80 usado por HTTP).

---

## 🌐 HTTP: comunicación sin cifrar

- HTTP se basa en **TCP** y usa el puerto **80**.  
- Todo el tráfico —incluyendo formularios, contraseñas y cookies— se transmite en **texto plano**.  
- Cualquiera con un sniffer (como Wireshark) puede **leer, modificar o robar** la información transmitida.  

### Flujo básico de HTTP:

1. El cliente resuelve el nombre de dominio a una dirección IP.  
2. Se establece una **conexión TCP (three-way handshake)**.  
3. El cliente envía una solicitud HTTP (por ejemplo, `GET / HTTP/1.1`).  
4. El servidor responde con el contenido solicitado (HTML, imágenes, etc.).  
5. La conexión TCP se cierra.  

---

## 🔒 HTTPS: HTTP sobre TLS

HTTPS añade una capa de **seguridad criptográfica** entre HTTP y TCP mediante TLS.  
Esto garantiza **confidencialidad**, **integridad** y **autenticación** en las comunicaciones.

### Flujo de conexión HTTPS:

1. **Handshake TCP:** igual que HTTP.  
2. **Handshake TLS:** cliente y servidor negocian algoritmos, intercambian certificados y establecen claves de sesión seguras.  
3. **Comunicación HTTP cifrada:** todo el tráfico HTTP viaja dentro del túnel TLS.

---

## 🧠 Funcionamiento técnico

- El cliente solicita al servidor iniciar una sesión segura.  
- El servidor presenta su **certificado TLS**, que valida su identidad.  
- Ambos negocian un conjunto de algoritmos y generan una **clave simétrica compartida**.  
- A partir de ese momento, los datos viajan **cifrados** y no pueden ser leídos por terceros.  

---

## 🧾 Certificados y autenticación

- Los **certificados digitales** son emitidos por **Autoridades de Certificación (CA)** como Let’s Encrypt o DigiCert.  
- Permiten verificar que el sitio web es **auténtico** y que no hay un atacante “man-in-the-middle”.  
- Los navegadores muestran un **candado** 🔒 si el certificado es válido.  

Ejemplo de certificado:
```
Issued to: www.example.com
Issued by: Let's Encrypt
Valid until: 2026-04-20
```

---

## 🔍 Captura en Wireshark

### HTTP
- Todo el tráfico (cabeceras, peticiones y respuestas) aparece en texto legible.
- Ejemplo: `GET /index.html HTTP/1.1`

### HTTPS
- El tráfico aparece como “**Application Data**” porque está **cifrado**.
- Solo se puede descifrar si se tiene acceso a la **clave privada** del servidor o las **claves de sesión**.

---

## 🔐 Ventajas principales de HTTPS

| Propiedad | Descripción |
|------------|--------------|
| 🔒 **Confidencialidad** | El tráfico se cifra, impidiendo que terceros lo lean. |
| ✅ **Integridad** | Evita la modificación de los datos en tránsito. |
| 🧾 **Autenticación** | Garantiza que el servidor es quien dice ser. |
| 🌍 **Confianza** | Navegadores y usuarios confían más en sitios HTTPS. |

---

## ⚙️ Comparativa rápida: HTTP vs HTTPS

| Característica | HTTP | HTTPS |
|----------------|-------|--------|
| Puerto por defecto | 80 | 443 |
| Cifrado | ❌ No | ✅ Sí (TLS) |
| Integridad | ❌ No | ✅ Sí |
| Autenticación | ❌ No | ✅ Sí |
| Seguridad | Baja | Alta |
| Uso recomendado | No sensible | Todo sitio moderno |

---

## ✅ Resumen rápido

- **Protocolo:** HTTPS (HTTP + TLS)  
- **Puerto:** 443  
- **Función:** Transmitir datos web de forma segura.  
- **Capa OSI:** 7 (Aplicación).  
- **Seguridad:** Basada en cifrado TLS 1.3 y certificados digitales.  
- **Beneficio principal:** Protege la privacidad y autenticidad de las comunicaciones web.  

---

**En resumen:** HTTPS permitió la evolución segura de la web moderna. Sin él, el comercio electrónico, la banca online y la privacidad digital no serían posibles.


------------------------------------------------------------------------------------------------------------------------------------------------------------------------------



# Resumen: SMTPS, POP3S e IMAPS — Protocolos de Correo Seguro

Cuando se añade **TLS (Transport Layer Security)** a los protocolos de correo tradicionales, estos pasan a ser sus versiones seguras:

| Protocolo original | Versión segura | Descripción |
|--------------------|----------------|--------------|
| SMTP | **SMTPS** | Envío de correos de forma segura |
| POP3 | **POP3S** | Recepción de correos descargándolos del servidor |
| IMAP | **IMAPS** | Sincronización y acceso remoto seguro al buzón |

---

## 🔐 ¿Qué cambia con la “S”?

La “S” al final de los nombres (por ejemplo, **SMTPS**) significa **Secure**, e indica que el protocolo usa **TLS** para cifrar la comunicación.  
El funcionamiento lógico del protocolo no cambia, solo la forma en que se transmite la información.

Esto asegura que:
- Las **credenciales** (usuario y contraseña) no viajen en texto plano.  
- El **contenido de los correos** esté protegido frente a interceptaciones.  
- Se garantice la **integridad y autenticidad** de la comunicación.  

---

## ⚙️ Puertos predeterminados

| Protocolo | Puerto inseguro | Puerto seguro (TLS) |
|------------|-----------------|---------------------|
| HTTP | 80 | 443 (HTTPS) |
| SMTP | 25 | 465 o 587 (SMTPS) |
| POP3 | 110 | 995 (POP3S) |
| IMAP | 143 | 993 (IMAPS) |

Los puertos seguros pueden variar ligeramente dependiendo del servidor, pero los anteriores son los más comunes.

---

## 🧠 Cómo funciona el cifrado TLS en correo electrónico

1. El cliente y el servidor establecen una conexión TCP.  
2. Se inicia una **negociación TLS** para intercambiar certificados y claves seguras.  
3. Una vez establecida la sesión cifrada, se transmite el tráfico SMTP, POP3 o IMAP normalmente.  

TLS actúa como una **capa intermedia** que protege los datos sin alterar el protocolo de aplicación.

---

## 📤 SMTPS (Secure SMTP)

- Se utiliza para **enviar correos electrónicos** desde el cliente al servidor de correo saliente.  
- Usa **TLS** para cifrar el contenido y las credenciales.  
- Puerto predeterminado: **465** (o **587** con STARTTLS).

Ejemplo de flujo:
```
Cliente → Servidor SMTPS → Servidor SMTP remoto → Buzón de destino
```

---

## 📥 POP3S (Secure POP3)

- Se utiliza para **descargar correos** del servidor al cliente.  
- Una vez descargados, normalmente se eliminan del servidor (aunque puede configurarse lo contrario).  
- Puerto predeterminado: **995**.

Ventajas:
- Sencillo y rápido.  
- Ideal para usuarios que usan un único dispositivo.  

---

## 🔁 IMAPS (Secure IMAP)

- Permite **sincronizar correos** entre múltiples dispositivos (móvil, PC, tablet).  
- Los correos permanecen en el servidor.  
- Puerto predeterminado: **993**.

Ventajas:
- Sincronización completa de bandejas.  
- Permite leer, mover o eliminar correos desde cualquier dispositivo.  

---

## ✅ Beneficios de usar versiones seguras (TLS)

| Propiedad | Descripción |
|------------|-------------|
| 🔒 **Confidencialidad** | Los datos viajan cifrados. |
| 🧾 **Autenticación** | Verifica la identidad del servidor. |
| 🧱 **Integridad** | Evita alteraciones en tránsito. |
| 🚫 **Prevención de espionaje** | Protege credenciales y mensajes. |

---

## ⚠️ Consideraciones

- Los certificados del servidor deben ser **válidos y actualizados**.  
- Las versiones antiguas de SSL/TLS están **obsoletas** y deben evitarse.  
- Algunos servicios de correo permiten **STARTTLS**, que inicia cifrado tras una conexión inicial sin cifrar.  

---

## 🧩 Resumen final

| Protocolo | Función principal | Puerto seguro | Capa OSI | Cifrado |
|------------|-------------------|----------------|-----------|----------|
| **SMTPS** | Enviar correos | 465 / 587 | Capa 7 | TLS |
| **POP3S** | Descargar correos | 995 | Capa 7 | TLS |
| **IMAPS** | Sincronizar correos | 993 | Capa 7 | TLS |

---

**En resumen:**  
SMTPS, POP3S e IMAPS proporcionan los mismos servicios que sus versiones originales, pero con **confidencialidad, integridad y autenticación** garantizadas gracias a **TLS**.



-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------v

# Resumen: SSH — Secure Shell

**SSH (Secure Shell)** es un protocolo seguro para acceder y administrar sistemas remotos a través de una red. Surgió como reemplazo de **Telnet**, que enviaba toda la información (incluyendo contraseñas) en texto plano.

---

## 🕰️ Breve historia

- **1995:** Tatu Ylönen desarrolla **SSH-1** como freeware.  
- **1996:** Se publica **SSH-2**, más seguro y eficiente.  
- **1999:** Nace **OpenSSH**, una implementación libre creada por los desarrolladores de OpenBSD.  
- Hoy en día, la mayoría de los clientes/servidores SSH usan **OpenSSH**.

---

## 🔐 Características principales

- **Autenticación segura:** soporte para contraseña, claves públicas y 2FA.  
- **Cifrado:** protege la confidencialidad de los datos en tránsito.  
- **Integridad:** evita la manipulación de datos durante la transmisión.  
- **Tunelización (port forwarding):** permite redirigir puertos y crear túneles seguros (similar a una VPN ligera).  
- **Reenvío X11 (X11 Forwarding):** ejecutar aplicaciones gráficas remotas en el cliente local mediante `-X` o `-Y`.

---

## ⚙️ Puertos y uso

- **Puerto por defecto:** `22` (TCP)  
- **Comando de conexión:** `ssh usuario@host`  
- **Con X11 forwarding:** `ssh -X usuario@host`

---

## 🔑 Autenticación con claves públicas (pasos básicos)

1. Generar el par de claves en el cliente:
   ```bash
   ssh-keygen -t rsa -b 4096 -f ~/.ssh/id_rsa
   ```
2. Copiar la clave pública al servidor (o añadirla a `~/.ssh/authorized_keys`):
   ```bash
   ssh-copy-id usuario@host
   ```
3. Conectarse sin contraseña:
   ```bash
   ssh usuario@host
   ```

---

## 🔁 Tunelización / Port forwarding (ejemplos)

- **Local forwarding:** redirige un puerto local hacia un puerto remoto a través del servidor SSH.
  ```bash
  ssh -L 8080:localhost:80 usuario@host
  ```
  Accede en `http://localhost:8080` y verás el servicio remoto `host:80` protegido por SSH.

- **Remote forwarding:** expone un puerto del servidor hacia el cliente/local.
  ```bash
  ssh -R 9090:localhost:3000 usuario@host
  ```

- **Dynamic forwarding (SOCKS proxy):**
  ```bash
  ssh -D 1080 usuario@host
  ```
  Configura tu navegador para usar `localhost:1080` como proxy SOCKS y enruta tráfico por SSH.

---

## 🔐 Buenas prácticas de seguridad

- Deshabilitar autenticación por contraseña y usar solo claves públicas.  
- Usar claves con passphrase para proteger la clave privada.  
- Restrict user logins (`AllowUsers`) y deshabilitar `root` si no es necesario.  
- Mantener OpenSSH actualizado.  
- Usar `Fail2Ban` o mecanismos similares para mitigar intentos de fuerza bruta.

---

## ✅ Resumen rápido

- **Protocolo:** SSH (Secure Shell)  
- **Puerto:** 22 (TCP)  
- **Propósito:** Administración remota segura, túneles, y transferencia segura de archivos (SCP/SFTP).  
- **Implementación común:** OpenSSH.  
- **Ventajas:** cifrado, autenticación con claves, tunelización, reenvío X11.  

---

**Ejemplo rápido:** conectar y ejecutar un comando remoto
```bash
ssh usuario@host 'uname -a && uptime'
```

-------------------------------------------------------------------------------------------------------------------------------------------------


# Resumen: VPN — Virtual Private Network

Una **VPN (Virtual Private Network)** permite crear una red privada segura sobre una red pública como Internet.  
Su propósito principal es **proteger la confidencialidad, integridad y privacidad de los datos** al establecer un túnel cifrado entre el cliente y el servidor VPN.

---

## 🧩 Concepto básico

Una VPN crea un **túnel cifrado** entre el dispositivo del usuario (cliente) y un servidor VPN remoto.  
Todo el tráfico pasa por este túnel, asegurando que terceros —como el proveedor de Internet (ISP) o atacantes— no puedan leer ni modificar los datos.

---

## 🏢 Escenario empresarial

En una empresa con varias sedes o empleados remotos, una VPN permite que todos los dispositivos se conecten como si estuvieran físicamente en la misma red local.

- **VPN Site-to-Site:** conecta redes completas (por ejemplo, sucursales con la oficina central).  
- **VPN Remote Access:** conecta usuarios individuales (por ejemplo, empleados trabajando desde casa).

El servidor VPN se ubica en la sede principal, y los clientes VPN (en otras oficinas o equipos remotos) establecen el túnel cifrado hacia él.

---

## 🔒 Beneficios principales

- **Privacidad:** oculta la IP real del usuario, mostrando la IP del servidor VPN.  
- **Seguridad:** el tráfico viaja cifrado, evitando espionaje o manipulación.  
- **Acceso remoto seguro:** empleados pueden acceder a recursos internos desde cualquier lugar.  
- **Evasión de censura o geobloqueos:** el usuario puede parecer estar en otro país.  

---

## 🌍 Funcionamiento general

1. El cliente VPN se conecta al servidor VPN.
2. Se establece un **túnel cifrado** mediante protocolos seguros como **OpenVPN**, **IPSec**, **WireGuard**, o **L2TP**.  
3. Todo el tráfico del cliente se redirige a través del túnel.
4. Los servicios externos solo ven la IP del **servidor VPN**, no la del usuario.

---

## ⚙️ Tipos de VPN

| Tipo | Descripción |
|------|--------------|
| **Site-to-Site** | Conecta dos redes completas mediante túneles VPN (por ejemplo, dos oficinas). |
| **Remote Access** | Permite a un usuario individual acceder a la red privada de la empresa. |
| **SSL/TLS VPN** | Utiliza HTTPS (puerto 443) para conexiones seguras y compatibilidad con firewalls. |
| **IPSec VPN** | Usa el protocolo IPsec para asegurar los datos a nivel de red. |
| **WireGuard** | VPN moderna, rápida y ligera con cifrado avanzado. |

---

## 🌐 Efectos en la conexión

- Todo el tráfico puede pasar por el túnel VPN (**Full Tunnel**).  
- Solo parte del tráfico (por ejemplo, hacia la red corporativa) puede pasar por la VPN (**Split Tunnel**).  
- Algunos VPNs pueden **filtrar la IP o las DNS** si están mal configurados. Se recomienda realizar pruebas de fugas.

---

## ⚠️ Aspectos legales

En algunos países, el uso de VPNs está **restringido o penalizado**.  
Antes de conectarte a un servidor VPN fuera de tu país, asegúrate de revisar las leyes locales.

---

## 🧭 Resumen rápido

| Elemento | Descripción |
|-----------|--------------|
| **Propósito** | Crear un túnel cifrado entre cliente y servidor |
| **Seguridad** | Cifrado, integridad, autenticación |
| **Protocolos comunes** | OpenVPN, IPSec, WireGuard, L2TP, PPTP |
| **Puertos típicos** | 443 (HTTPS), 500/4500 (IPSec), 51820 (WireGuard) |
| **Tipos** | Site-to-Site, Remote Access |
| **Ventajas** | Privacidad, acceso remoto, protección, anonimato |

---

**Ejemplo práctico:**  
Un empleado en Madrid se conecta mediante VPN al servidor corporativo en Barcelona.  
Todo su tráfico viaja cifrado y los recursos de la empresa (bases de datos, archivos, intranet) se comportan como si estuviera físicamente allí.


----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

# Closing Notes — Secure Network Traffic

En esta sección final se resumen los tres principales métodos para **asegurar el tráfico de red** que se estudiaron en el curso.

---

## 1️⃣ TLS (Transport Layer Security)

TLS permite cifrar la comunicación entre cliente y servidor en muchos protocolos.  
Es la forma más común de proteger servicios en Internet y garantizar **confidencialidad e integridad** de los datos.

- Se aplica fácilmente a protocolos existentes.  
- Los protocolos seguros suelen añadir una “S” a su nombre, por ejemplo:  
  - **HTTP → HTTPS**  
  - **SMTP → SMTPS**  
  - **POP3 → POP3S**  
- Usa certificados digitales firmados por **Autoridades de Certificación (CA)**.  
- Opera en la capa de transporte (OSI capa 4).

---

## 2️⃣ SSH (Secure Shell)

SSH es un protocolo seguro principalmente usado para **acceso remoto** a sistemas.  
También permite **transferencia segura de archivos (SCP/SFTP)** y **creación de túneles cifrados (port forwarding)**.

- Cifra todas las comunicaciones.  
- Soporta autenticación por **clave pública o contraseña**.  
- Puede encapsular protocolos inseguros, como VNC, dentro de un túnel SSH.  
- Escucha por defecto en el **puerto TCP 22**.

> ✅ Ideal para administración remota y encapsular conexiones inseguras dentro de un canal cifrado.

---

## 3️⃣ VPN (Virtual Private Network)

Una VPN crea una red privada sobre Internet mediante **túneles cifrados** entre cliente y servidor.  
Permite conectar sedes de una empresa o usuarios remotos como si estuvieran en la misma red local.

- Puede redirigir **todo el tráfico** o solo el corporativo.  
- Oculta la IP real del usuario (muestra la del servidor VPN).  
- Protocolos comunes: **OpenVPN, IPSec, WireGuard**.  
- Típicamente usa puertos como **443, 500, 4500, 51820**.

> ✅ Ideal para conectar redes completas (site-to-site) o usuarios individuales (remote access).

---

## 🧪 Desafío práctico (Wireshark)

El reto final consiste en **analizar tráfico TLS** con Wireshark y descifrarlo usando claves registradas por el navegador.

1. Iniciar la máquina virtual.  
2. Localizar el archivo `randy-chromium.pcapng` en la carpeta `Documents`.  
3. Configurar Wireshark para usar las claves TLS almacenadas en `ssl-key.log`.  
4. Seguir la ruta:  
   `Right click → Protocol Preferences → Transport Layer Security → Open Preferences`  
5. Buscar el archivo `ssl-key.log` y pulsar **OK**.  
6. Wireshark mostrará el tráfico TLS **descifrado**, incluyendo posibles credenciales.

---

## 🧭 Conclusión general

| Método | Propósito | Nivel de protección | Uso típico |
|--------|------------|---------------------|-------------|
| **TLS** | Cifrado punto a punto de protocolos | Alto | HTTPS, SMTPS, POP3S |
| **SSH** | Acceso remoto seguro y tunelización | Muy alto | Administración y túneles |
| **VPN** | Red privada sobre Internet | Muy alto | Conexión entre redes o usuarios |

---

🔐 **Resumen final:**  
Estos tres mecanismos —TLS, SSH y VPN— representan las **bases modernas de la seguridad en red**, ofreciendo protección adaptable desde la conexión web hasta redes corporativas completas.


----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------


