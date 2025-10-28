# Resumen: Modelo OSI (Open Systems Interconnection)

El **Modelo OSI** (Open Systems Interconnection) fue desarrollado por la **ISO** como un marco conceptual para entender cómo los sistemas se comunican en una red.  
Se compone de **7 capas**, desde la transmisión física hasta la aplicación del usuario.

---

## 🧩 Capas del Modelo OSI

| N° | Capa | Función principal | Ejemplos |
|----|------|------------------|-----------|
| **7** | **Aplicación** | Proporciona servicios de red directamente a las aplicaciones del usuario. | HTTP, FTP, DNS, SMTP, IMAP, POP3 |
| **6** | **Presentación** | Formatea, cifra y comprime los datos para que la capa de aplicación los entienda. | Unicode, MIME, JPEG, PNG, MPEG |
| **5** | **Sesión** | Establece, mantiene y sincroniza las comunicaciones entre aplicaciones. | NFS, RPC |
| **4** | **Transporte** | Proporciona comunicación extremo a extremo, segmentación y control de errores. | TCP, UDP |
| **3** | **Red** | Gestiona el direccionamiento lógico y el enrutamiento entre redes. | IP, ICMP, IPSec, VPN |
| **2** | **Enlace de datos** | Transfiere datos de forma confiable entre nodos del mismo segmento de red. | Ethernet (802.3), WiFi (802.11), MAC |
| **1** | **Física** | Transmite los bits a través del medio físico (eléctrico, óptico o inalámbrico). | Cables, radiofrecuencia, fibra óptica |

---

## 📚 Detalle por capas

### **Capa 1: Física**
- Es la base del modelo. Define **el medio físico** de transmisión (cables, señales, radio, etc.).  
- Ejemplos: cable Ethernet, fibra óptica, WiFi (2.4GHz, 5GHz, 6GHz).  
- Se encarga de representar los bits (0s y 1s) físicamente.

---

### **Capa 2: Enlace de datos**
- Define cómo los dispositivos se comunican dentro del **mismo segmento de red**.  
- Utiliza direcciones **MAC** (Media Access Control), de 6 bytes en formato hexadecimal (`00:1A:2B:3C:4D:5E`).  
- Ejemplos: Ethernet (802.3), WiFi (802.11).  
- Se pueden observar dos direcciones MAC en un paquete: **origen** y **destino**.

---

### **Capa 3: Red**
- Se encarga del **direccionamiento lógico** y del **enrutamiento** entre redes diferentes.  
- Permite la comunicación entre múltiples redes y determina la mejor ruta.  
- Ejemplos: IP, ICMP, IPSec, VPN (SSL/TLS).

---

### **Capa 4: Transporte**
- Gestiona la **comunicación extremo a extremo** entre aplicaciones.  
- Divide los datos en segmentos, controla errores y flujo.  
- Ejemplos: **TCP** (confiable, orientado a conexión) y **UDP** (rápido, sin conexión).

---

### **Capa 5: Sesión**
- Controla las **sesiones** entre aplicaciones en diferentes equipos.  
- Permite abrir, mantener y cerrar conexiones, además de sincronizar datos.  
- Ejemplos: **NFS**, **RPC**.

---

### **Capa 6: Presentación**
- Asegura que los datos sean legibles para la aplicación.  
- Se encarga de la **codificación**, **compresión** y **encriptación**.  
- Ejemplo: al enviar una imagen por correo, el archivo (JPEG, PNG) se adjunta usando **MIME** para ser interpretado correctamente.

---

### **Capa 7: Aplicación**
- Es la interfaz más cercana al usuario.  
- Proporciona servicios de red como transferencia de archivos, correo o navegación web.  
- Ejemplos: **HTTP, FTP, DNS, POP3, SMTP, IMAP**.

---

## 🧠 Mnemotecnia para recordar las capas
De abajo hacia arriba (1 → 7):  
**“Please Do Not Throw Spinach Pizza Away.”**  
(Physical, Data Link, Network, Transport, Session, Presentation, Application)

------------------------------------------------------------------------------------------------------------------------------------------------

# Resumen: Modelo TCP/IP

El **modelo TCP/IP** (Transmission Control Protocol / Internet Protocol) fue desarrollado en la década de 1970 por el **Departamento de Defensa de EE.UU. (DoD)**.  
Su propósito era crear una red **resiliente y confiable**, capaz de continuar funcionando incluso si partes de la infraestructura fallaban (por ejemplo, tras un ataque militar).  
Gracias a su diseño adaptable, los protocolos de enrutamiento permiten que la red siga operando cuando cambian las rutas o topologías.

---

## 🧩 Estructura del modelo TCP/IP

El modelo TCP/IP agrupa las **7 capas del modelo OSI** en **4 capas principales**, según la RFC 1122.

| N° | Capa TCP/IP | Capas equivalentes OSI | Protocolos y ejemplos |
|----|--------------|------------------------|-----------------------|
| **4** | **Application (Aplicación)** | Capas 5, 6 y 7 (Aplicación, Presentación, Sesión) | HTTP, HTTPS, FTP, SMTP, IMAP, POP3, Telnet, SSH |
| **3** | **Transport (Transporte)** | Capa 4 | TCP, UDP |
| **2** | **Internet** | Capa 3 (Red) | IP, ICMP, IPSec |
| **1** | **Link (Enlace)** | Capas 1 y 2 (Física + Enlace de datos) | Ethernet (802.3), WiFi (802.11) |

---

## 🧱 Modelo TCP/IP vs. Modelo OSI

| **Modelo OSI (7 capas)** | **Modelo TCP/IP (4 capas)** |
|---------------------------|-----------------------------|
| 7. Aplicación             | 4. Aplicación |
| 6. Presentación           | ↑ |
| 5. Sesión                 | ↑ |
| 4. Transporte             | 3. Transporte |
| 3. Red                    | 2. Internet |
| 2. Enlace de datos        | 1. Enlace |
| 1. Física                 | ↑ (agrupada con Enlace) |

🔸 **Nota:** En algunos libros y cursos modernos (como *Computer Networking: A Top-Down Approach* de Kurose y Ross), se describe una **versión de 5 capas**, separando la capa **Física** de la de Enlace:

1. Física  
2. Enlace  
3. Red (Internet)  
4. Transporte  
5. Aplicación

---

## 🧠 Capa por capa (visión descendente)

### 1. **Application Layer (Capa de Aplicación)**
Combina las funciones de las capas 5, 6 y 7 del modelo OSI.  
Proporciona servicios de red directamente a las aplicaciones del usuario final.  
**Ejemplos:** HTTP, HTTPS, FTP, DNS, SMTP, POP3, SSH, Telnet.

---

### 2. **Transport Layer (Capa de Transporte)**
Gestiona la comunicación extremo a extremo entre aplicaciones.  
Encargada del control de flujo, segmentación y corrección de errores.  
**Protocolos:**  
- **TCP (Transmission Control Protocol)** → conexión confiable.  
- **UDP (User Datagram Protocol)** → conexión rápida, sin control de errores.

---

### 3. **Internet Layer (Capa de Internet)**
Se ocupa del **direccionamiento lógico y el enrutamiento** de paquetes entre redes.  
Define cómo se mueven los datos a través de Internet.  
**Protocolos:** IP, ICMP, IPSec.

---

### 4. **Link Layer (Capa de Enlace)**
Incluye las funciones de las capas física y de enlace del modelo OSI.  
Se encarga de la **transmisión física** y del **acceso al medio**.  
**Protocolos:** Ethernet, WiFi (802.11), PPP.

---

## 💬 En resumen

| Aspecto | Modelo OSI | Modelo TCP/IP |
|----------|-------------|----------------|
| **Creador** | ISO | Departamento de Defensa (DoD) |
| **Capas** | 7 | 4 (o 5 en algunos textos) |
| **Uso actual** | Modelo teórico | Modelo práctico (base de Internet) |
| **Enfoque** | Conceptual y educativo | Implementación funcional de red |
| **Ejemplo de protocolos** | HTTP, TCP, IP, Ethernet | HTTP, TCP, IP, Ethernet |

------------------------------------------------------------------------------------------------------------------------------------------------

# Resumen: Direcciones IP y Enrutamiento

## 🧠 ¿Qué es una dirección IP?

Una **dirección IP (Internet Protocol)** identifica de forma **única** a cada dispositivo dentro de una red.  
Sin una dirección IP, los equipos no podrían comunicarse entre sí.

**Analogía:**  
Una dirección IP es como tu **dirección postal**: permite que otros te envíen información y sepan exactamente a dónde entregarla.

---

## 🧩 IPv4 y su estructura

El **IPv4 (versión 4 del Protocolo IP)** es el formato más usado actualmente.

Una dirección IPv4 consta de **4 octetos (32 bits)**, cada uno con un valor entre **0 y 255**.  
Ejemplo:
```
192.168.1.1
```

Cada octeto equivale a 8 bits → `4 × 8 = 32 bits`.

**Reservas especiales:**
- `192.168.1.0` → Dirección de red  
- `192.168.1.255` → Dirección de difusión (*broadcast*)

👉 En total existen aproximadamente **4.3 mil millones (2³²)** de direcciones únicas.  
Sin embargo, algunas están reservadas para redes privadas o funciones especiales.

---

## ⚙️ Ver la configuración de red

### 🪟 En Windows
```bash
ipconfig
```

### 🐧 En Linux / Unix
```bash
ifconfig
ip address show    # o más corto: ip a s
```

**Ejemplo (Linux):**
```
inet 192.168.66.89  netmask 255.255.255.0  broadcast 192.168.66.255
```
- **IP del host:** 192.168.66.89  
- **Máscara de red:** 255.255.255.0  
- **Dirección de broadcast:** 192.168.66.255

---

## 📏 Máscaras de subred y notación CIDR

Una máscara de subred indica qué parte de la dirección identifica la red y cuál identifica el host.

Ejemplo:
```
192.168.66.89/24
```
El `/24` significa que los **primeros 24 bits** (3 octetos) son la parte de red.  
Por tanto, el rango de direcciones válidas sería:
```
192.168.66.1 – 192.168.66.254
```
y las direcciones `192.168.66.0` y `192.168.66.255` son de red y broadcast, respectivamente.

---

## 🏠 Tipos de direcciones IP

### 🔹 **Públicas**
- Son únicas en Internet.  
- Se utilizan para identificar dispositivos accesibles globalmente.

### 🔸 **Privadas**
- Se usan en redes locales (LAN).  
- No pueden acceder directamente a Internet.  
- Definidas en la **RFC 1918**:

| Rango | Prefijo CIDR | Total aproximado de IPs |
|--------|----------------|------------------------|
| 10.0.0.0 – 10.255.255.255 | /8 | 16,777,216 |
| 172.16.0.0 – 172.31.255.255 | /12 | 1,048,576 |
| 192.168.0.0 – 192.168.255.255 | /16 | 65,536 |

💡 Para que un equipo con IP privada acceda a Internet, se utiliza **NAT (Network Address Translation)** en el router.

---

## 🚦 Enrutamiento (Routing)

Un **router** actúa como una oficina de correos: recibe los datos y decide **a qué red enviarlos**.  
Cuando un paquete debe viajar fuera de la red local, el router lo **reenvía** hacia su destino final.

### Características del router:
- Opera en la **capa 3 (Red)** del modelo OSI.
- Analiza las **direcciones IP** de origen y destino.
- Decide la **mejor ruta** posible según su tabla de enrutamiento.
- Puede reenviar el paquete a múltiples routers antes de llegar al destino.

📬 En resumen, **el router conecta redes distintas** y **dirige el tráfico** para que los paquetes lleguen correctamente.

---

## 🧠 Resumen rápido

| Concepto | Descripción |
|-----------|--------------|
| **IPv4** | Protocolo más usado, 32 bits (4 octetos) |
| **IPv6** | Nueva versión, 128 bits, más direcciones disponibles |
| **Máscara / CIDR** | Indica qué parte es red y cuál host |
| **IP Pública** | Identifica equipos accesibles desde Internet |
| **IP Privada** | Solo válida dentro de una red local |
| **NAT** | Traduce IPs privadas a públicas |
| **Router** | Encaminador que conecta redes y reenvía paquetes |

------------------------------------------------------------------------------------------------------------------------------------------------

# Resumen: Protocolos de Transporte — UDP y TCP

El **protocolo IP** permite llegar a un **host** dentro de la red (identificado por su dirección IP).  
Sin embargo, para que **procesos o aplicaciones** dentro de esos hosts se comuniquen, se necesitan **protocolos de transporte**.

Los dos protocolos principales de la **capa 4 (Transporte)** son **UDP** y **TCP**.

---

## 🔹 UDP (User Datagram Protocol)

UDP es un **protocolo de transporte sin conexión (connectionless)**.  
No requiere establecer una conexión antes de enviar datos ni garantiza su entrega.

### Características principales:
- **Capa:** 4 (Transporte)
- **Tipo:** *Connectionless* (sin conexión)
- **Confirmación:** ❌ No garantiza entrega ni orden
- **Velocidad:** 🚀 Alta (menos sobrecarga)
- **Fiabilidad:** Baja
- **Encabezado:** Ligero
- **Identificación de procesos:** mediante **números de puerto**

Cada puerto usa **2 octetos (16 bits)** → rango de **1 a 65535**.  
El **puerto 0** está **reservado**.

### Ejemplo comparativo:
UDP es como enviar una carta **sin confirmación de entrega**.  
No sabes si el destinatario la recibió, pero el envío es **más rápido y barato**.

**Ejemplos de protocolos que usan UDP:**
- DNS
- DHCP
- SNMP
- TFTP
- VoIP y streaming (por su baja latencia)

---

## 🔸 TCP (Transmission Control Protocol)

TCP es un **protocolo orientado a la conexión (connection-oriented)**.  
A diferencia de UDP, **garantiza la entrega, el orden y la integridad** de los datos enviados.

### Características principales:
- **Capa:** 4 (Transporte)
- **Tipo:** *Connection-oriented* (orientado a conexión)
- **Confirmación:** ✅ Sí (entrega garantizada)
- **Fiabilidad:** Alta
- **Velocidad:** Más lenta que UDP
- **Control:** Maneja errores, orden y congestión
- **Identificación de procesos:** mediante **números de puerto** (1–65535)

Cada paquete TCP incluye **números de secuencia y acuse de recibo (ACK)** que aseguran que los datos lleguen correctamente.

---

## 🤝 Establecimiento de conexión: Three-Way Handshake

TCP establece una conexión mediante un **intercambio de tres mensajes**:

| Paso | Dirección | Descripción | Bandera (Flag) |
|------|------------|--------------|----------------|
| 1 | Cliente → Servidor | El cliente inicia la conexión enviando un paquete **SYN** con un número de secuencia inicial. | SYN |
| 2 | Servidor → Cliente | El servidor responde con un paquete **SYN-ACK**, confirmando y enviando su propio número de secuencia. | SYN + ACK |
| 3 | Cliente → Servidor | El cliente envía un **ACK** final, confirmando la recepción del SYN-ACK. | ACK |

Después de este proceso, la conexión TCP queda establecida y puede comenzar el intercambio de datos.

**Ilustración:**
```
Cliente → SYN → Servidor
Cliente ← SYN+ACK ← Servidor
Cliente → ACK → Servidor
```

---

## 📦 Comparación entre UDP y TCP

| Característica | **UDP** | **TCP** |
|----------------|----------|----------|
| Tipo de conexión | Sin conexión (*connectionless*) | Con conexión (*connection-oriented*) |
| Confirmación de entrega | No | Sí |
| Fiabilidad | Baja | Alta |
| Orden de los datos | No garantizado | Garantizado |
| Control de flujo | No | Sí |
| Velocidad | Más rápida | Más lenta |
| Uso típico | Streaming, DNS, juegos online | Web, correo, transferencias de archivos |

---

## 🧠 Resumen

- **UDP** → Rápido, sin confirmación, ideal para aplicaciones en tiempo real.  
- **TCP** → Confiable, con control de errores, ideal para transferencias críticas.

Ambos protocolos **usan puertos** para identificar los procesos o servicios que envían y reciben datos (rango **1–65535**).

---

## 💡 Ejemplo de uso típico

| Protocolo | Puerto común | Descripción |
|------------|---------------|-------------|
| HTTP (TCP) | 80 | Navegación web |
| HTTPS (TCP) | 443 | Navegación segura |
| DNS (UDP/TCP) | 53 | Resolución de nombres |
| FTP (TCP) | 21 | Transferencia de archivos |
| SSH (TCP) | 22 | Acceso remoto seguro |
| DHCP (UDP) | 67–68 | Asignación de direcciones IP |

------------------------------------------------------------------------------------------------------------------------------------------------

# Resumen: Encapsulación y la Vida de un Paquete

## 🔹 ¿Qué es la encapsulación?

**Encapsulación** es el proceso mediante el cual cada capa del modelo de red **añade su propia cabecera (y, a veces, un tráiler)** a la unidad de datos que recibe de la capa superior.  
Este mecanismo permite que cada capa cumpla su función de forma independiente, sin interferir con las demás.

---

## 🧱 Proceso de Encapsulación (de arriba hacia abajo)

| Etapa | Capa | Unidad de datos resultante | Descripción |
|--------|------|-----------------------------|--------------|
| 1️⃣ | **Aplicación** | **Datos** | El usuario genera la información (por ejemplo, escribe un mensaje o realiza una búsqueda web). |
| 2️⃣ | **Transporte (TCP/UDP)** | **Segmento (TCP)** o **Datagrama (UDP)** | Se agrega una cabecera que incluye los puertos de origen y destino. |
| 3️⃣ | **Red (IP)** | **Paquete IP** | Se agrega la cabecera IP con direcciones de origen y destino. |
| 4️⃣ | **Enlace de Datos (Ethernet/WiFi)** | **Trama (Frame)** | Se añaden cabeceras y tráileres físicos (direcciones MAC, CRC, etc.). |

🧩 En resumen:
```
Datos de aplicación
↓
+ Cabecera TCP/UDP → Segmento
↓
+ Cabecera IP → Paquete
↓
+ Cabecera y Tráiler de Enlace → Trama
```

Cada capa “encapsula” la información anterior dentro de su propio formato antes de enviarla a la siguiente.

---

## 🔄 Proceso inverso (Desencapsulación)

En el receptor, el proceso se **invierte** capa por capa:
1. La **capa de enlace** elimina su cabecera y tráiler.  
2. La **capa de red** analiza la dirección IP y quita su cabecera.  
3. La **capa de transporte** identifica el puerto y entrega los datos a la aplicación correspondiente.  
4. Finalmente, la **aplicación** interpreta los datos originales.

---

## 🌐 Ejemplo práctico: La vida de un paquete

Supongamos que realizas una búsqueda en TryHackMe.

### Paso a paso:

1. 🧍‍♂️ **Entrada del usuario:**  
   Escribes tu consulta en el navegador y presionas **Enter**.

2. 🌍 **Capa de aplicación (HTTP/HTTPS):**  
   El navegador genera una **solicitud HTTP** y la envía a la capa de transporte.

3. 🚦 **Capa de transporte (TCP):**  
   TCP establece una conexión con el servidor web mediante el **three-way handshake (SYN, SYN-ACK, ACK)**.  
   Luego encapsula la solicitud HTTP en **segmentos TCP**.

4. 🧭 **Capa de red (IP):**  
   El sistema añade las direcciones IP **de origen (tu PC)** y **de destino (servidor TryHackMe)**, creando un **paquete IP**.

5. 🔗 **Capa de enlace de datos (Ethernet/WiFi):**  
   Se agregan las direcciones **MAC** y un **tráiler de comprobación** (CRC).  
   El paquete se convierte en una **trama** y se envía al **router**.

6. 📡 **Enrutamiento:**  
   El router elimina la cabecera de enlace, examina la dirección IP y reenvía el paquete hacia el destino correcto.  
   Este proceso se repite en cada router intermedio hasta llegar al servidor de TryHackMe.

7. 🖥️ **Servidor destino:**  
   El router del destino entrega el paquete al servidor, que realiza el proceso inverso (**desencapsulación**) hasta que la aplicación recibe los datos originales.

---

## 🧠 Conclusión

- Cada capa **añade información (encapsula)** antes de transmitir los datos.  
- En el destino, las capas **eliminan la información (desencapsulan)** para recuperar el mensaje original.  
- Este proceso garantiza que la comunicación sea **modular, confiable y estructurada**.

📦 En resumen:
> Los datos viajan encapsulados dentro de varias capas (Aplicación → Transporte → Red → Enlace) y se desencapsulan en el orden inverso al llegar a su destino.
