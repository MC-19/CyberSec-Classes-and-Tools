# Introduction — Wireshark Basics

**Wireshark** es una herramienta de análisis de paquetes (sniffer) **de código abierto y multiplataforma** que permite capturar, visualizar y analizar tráfico de red en tiempo real o a partir de archivos de captura (PCAP/PCAPNG).  
Es una de las herramientas más utilizadas por analistas de redes, ingenieros y profesionales de ciberseguridad.

---

## 🎯 Objetivos de aprendizaje

- **Navegar y configurar Wireshark.**  
  Aprender la interfaz, menús, y paneles de información.

- **Inspeccionar paquetes.**  
  Analizar información contenida en las distintas capas del modelo **TCP/IP (Ethernet, IP, TCP/UDP, Aplicación)**.

- **Aplicar filtros de visualización.**  
  Usar **Display Filters** para encontrar tráfico relevante entre miles de paquetes.

---

## 🧭 Propósito del módulo

Este módulo servirá como introducción práctica a **Wireshark** y su uso para el **análisis de tráfico real**, con un enfoque en la comprensión de cómo los datos viajan por la red y cómo filtrarlos para detectar información clave.

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

# Wireshark — Tool Overview

## 🧩 Use Cases

**Wireshark** es una de las herramientas más potentes para el análisis de tráfico de red.  
Sus principales usos incluyen:

- **Detección y resolución de problemas de red**, como puntos de fallo o congestión.  
- **Detección de anomalías de seguridad**, como hosts no autorizados, uso anómalo de puertos o tráfico sospechoso.  
- **Investigación y aprendizaje de protocolos**, permitiendo observar encabezados, códigos de respuesta y datos transmitidos.

> ⚠️ *Nota:* Wireshark **no es un IDS (Intrusion Detection System)**. Solo captura y muestra paquetes; no los modifica ni actúa sobre ellos.  
> La detección de amenazas o problemas depende de la **interpretación del analista**.

---

## 🖥️ GUI Overview — Interfaz principal

La interfaz gráfica de Wireshark se compone de una única ventana que permite investigar el tráfico en múltiples formas.  
A primera vista, se distinguen **cinco secciones principales:**

| Sección | Descripción |
|----------|--------------|
| **Toolbar (Barra de herramientas)** | Incluye menús y accesos rápidos para capturar, filtrar, ordenar, resumir, exportar o combinar paquetes. |
| **Display Filter Bar** | Zona principal de consulta y filtrado de paquetes. |
| **Recent Files** | Lista de archivos analizados recientemente (doble clic para reabrirlos). |
| **Capture Filters & Interfaces** | Muestra filtros de captura e interfaces de red disponibles (e.g. `lo`, `eth0`, `ens33`). |
| **Status Bar** | Muestra el estado de la herramienta, perfil actual y número total de paquetes capturados. |

---

## 📂 Cargar Archivos PCAP

Al iniciar Wireshark, se muestra una interfaz vacía. Para cargar un archivo de captura (`http1.pcapng` o `Exercise.pcapng`), puedes usar:

- Menú: **File → Open**
- **Arrastrar y soltar** el archivo en la ventana
- **Doble clic** sobre el archivo `.pcap` o `.pcapng`

Al abrirlo, Wireshark mostrará la información en **tres paneles principales:**

| Panel | Descripción |
|--------|--------------|
| **Packet List Pane** | Muestra el resumen de cada paquete (origen, destino, protocolo, descripción). |
| **Packet Details Panel** | Desglose detallado por capas del protocolo seleccionado. |
| **Packet Bytes Pane** | Representación hexadecimal y ASCII del paquete. Se sincroniza con los campos seleccionados. |

---

## 🎨 Colores de Paquetes

Wireshark colorea los paquetes según su tipo de tráfico o protocolo para facilitar el análisis visual.

- **Reglas temporales:** solo activas durante la sesión actual.  
- **Reglas permanentes:** guardadas en el perfil del usuario y disponibles en futuras sesiones.

📘 **Accesos útiles:**
- Crear reglas: `View → Coloring Rules`  
- Activar/desactivar colores: `View → Colorize Packet List`  
- Aplicar filtros de conversación (color temporal): `View → Conversation Filter`

> 💡 Puedes crear tus propias reglas de color usando **display filters** para resaltar eventos específicos.

---

## 🦈 Captura de Tráfico (Sniffing)

Wireshark puede capturar tráfico en tiempo real desde las interfaces de red.

| Botón | Acción |
|--------|---------|
| 🟦 Azul | Inicia la captura |
| 🔴 Rojo | Detiene la captura |
| 🟩 Verde | Reinicia la captura |

La barra de estado mostrará:
- La **interfaz usada**
- El **número de paquetes capturados**

> ⚠️ Para capturar tráfico en todas las interfaces, puede requerirse ejecutar Wireshark con **privilegios administrativos**.

---

## 🔀 Fusionar Archivos (Merge PCAP Files)

Wireshark permite combinar dos archivos `.pcap` o `.pcapng`:

1. Abre el primer archivo.  
2. Ve a **File → Merge**.  
3. Selecciona el segundo archivo.  
4. Wireshark mostrará el número total de paquetes combinados.  
5. Haz clic en **Open** → ambos archivos se unirán.  
6. Guarda el nuevo archivo fusionado antes de continuar el análisis.

---

## 📊 Ver Detalles del Archivo

Consultar los detalles del archivo es útil para identificar y clasificar múltiples capturas.

### Información mostrada:
- Hash del archivo  
- Tiempo de captura  
- Comentarios del archivo  
- Interfaz utilizada  
- Estadísticas generales

📍 **Accesos:**
- Menú: `Statistics → Capture File Properties`  
- Icono del archivo `.pcap` (parte inferior izquierda de la ventana)

---

## 🧭 Conclusión

Wireshark proporciona una interfaz completa para analizar tráfico de red desde múltiples perspectivas.  
Su potencia radica en la capacidad de combinar filtrado, visualización y colorización para descubrir comportamientos anómalos o diagnosticar problemas con precisión.

------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

# Wireshark — Packet Dissection

## 🧩 Concepto General

**Packet dissection** (o **protocol dissection**) es el proceso mediante el cual **Wireshark analiza y decodifica los protocolos y campos de cada paquete** capturado.  
Wireshark soporta **cientos de protocolos** y permite incluso crear **scripts personalizados** para nuevas disecciones.

> 🔎 Esta sección explica cómo Wireshark utiliza las **capas del modelo OSI** para descomponer los paquetes y analizarlos.  
> Se asume que ya tienes conocimientos previos del modelo OSI y su funcionamiento.

---

## 🧠 ¿Qué es la disección de paquetes?

Cada paquete contiene información estructurada en **capas** según el modelo OSI (de 5 a 7 capas dependiendo del protocolo).  
Wireshark permite explorar cada capa individualmente desde el **Panel de Detalles**.

📘 **Cómo visualizarlo:**
- Haz clic sobre un paquete en el panel principal.  
- En el panel inferior se muestran los **detalles por capa**.  
- Si haces doble clic, se abre una ventana dedicada.  
- Cada vez que seleccionas un campo, su parte correspondiente se resalta en el **panel de bytes** (vista hexadecimal/ASCII).

---

## 🧩 Capas del Paquete (Ejemplo HTTP)

A continuación, se detalla cómo Wireshark presenta las **capas** de un paquete HTTP (por ejemplo, el número 27 de una captura):

| Capa | Nombre | Descripción | Capa OSI |
|------|---------|-------------|-----------|
| **1️⃣ Frame** | *Frame / Packet* | Información del paquete físico capturado. Incluye el número de frame y tiempo de captura. | **Capa 1 — Física** |
| **2️⃣ Source [MAC]** | *Ethernet Header* | Muestra las direcciones MAC de origen y destino. | **Capa 2 — Enlace de Datos** |
| **3️⃣ Source [IP]** | *IP Header* | Contiene direcciones IPv4 o IPv6 de origen y destino, TTL, versión y checksum. | **Capa 3 — Red** |
| **4️⃣ Protocol** | *TCP / UDP Header* | Indica el protocolo de transporte, puertos de origen y destino, y flags TCP. | **Capa 4 — Transporte** |
| **5️⃣ Protocol Errors** | *Reassembly Info* | Detalla fragmentos o segmentos TCP que necesitan ser reensamblados. | **Extensión de Capa 4** |
| **6️⃣ Application Protocol** | *HTTP / FTP / SMB...* | Muestra el protocolo de aplicación en uso, junto con los encabezados del mensaje. | **Capa 7 — Aplicación** |
| **7️⃣ Application Data** | *Payload / Content* | Contenido transmitido por la aplicación, como HTML, JSON, archivos, etc. | **Extensión de Capa 7** |

---

## 🔬 Ejemplo Visual — Flujo de Análisis

1. **Selecciona un paquete** en el “Packet List Pane”.  
2. Observa su **desglose por capas** en el “Packet Details Panel”.  
3. Cada campo que selecciones se **resalta automáticamente** en el “Packet Bytes Pane”.  
   Esto permite correlacionar los datos visuales (hexadecimales) con sus valores interpretados.

---

## 🧠 Interpretación de Capas

| Tipo | Qué muestra | Ejemplo de campo |
|------|--------------|------------------|
| **Frame (L1)** | Información física del paquete | Frame nº, tiempo, tamaño, interfaz |
| **Ethernet (L2)** | Comunicación directa entre dispositivos | MAC de origen y destino |
| **IP (L3)** | Dirección lógica y enrutamiento | IP origen/destino, TTL, ID |
| **TCP/UDP (L4)** | Transporte de datos | Puertos, flags, número de secuencia |
| **Reassembly** | Control de flujo TCP | “TCP segment of a reassembled PDU” |
| **Application (L7)** | Protocolo de aplicación | HTTP GET / POST, FTP commands |
| **Data** | Contenido | Texto o binario (payload) |

---

## 🧭 Conclusión

Wireshark descompone cada paquete según el **modelo OSI**, facilitando la comprensión del flujo de red desde la capa física hasta la aplicación.  
Esto permite **detectar errores, analizar comportamiento de protocolos** y visualizar **datos en tiempo real o históricos** de forma granular.

> 💡 *Consejo:* Familiarízate con la estructura de cada capa para poder identificar fácilmente anomalías o comportamientos sospechosos durante el análisis.


--------------------------------------------------------------------------------------------------------------------------------------------------------------------v

# Wireshark — Packet Numbers & Analysis Tools

## 🔢 Packet Numbers

Wireshark asigna un **número único** a cada paquete capturado.  
Esto permite identificar y regresar fácilmente a un punto específico dentro de una captura extensa.

### 🧭 Ventajas:
- Facilita la navegación en capturas grandes.  
- Permite **referenciar paquetes específicos** en análisis colaborativos.  
- Se usa para el seguimiento y correlación de eventos en conversaciones.

---

## 🚀 Go to Packet

Wireshark permite navegar entre paquetes de varias formas:

- **Go → Go to Packet…** (Ctrl + G)  
- Ir al **siguiente/anterior** paquete dentro de una conversación.  
- Saltar a un paquete específico introduciendo su número.

📘 Esto ayuda a:
- Rastrear el flujo de una sesión TCP.  
- Ver la siguiente solicitud/respuesta dentro del mismo stream.  
- Saltar directamente al paquete relevante en un análisis largo.

---

## 🔍 Find Packets

Además de los números, Wireshark permite **buscar paquetes por contenido**.  
Se accede desde: **Edit → Find Packet**.

### 🧠 Tipos de búsqueda:
| Tipo | Descripción |
|------|--------------|
| **Display Filter** | Usa el mismo formato de filtros que la barra de visualización. |
| **Hex Value** | Busca secuencias hexadecimales específicas. |
| **String** | Busca texto dentro de los paquetes. |
| **Regex** | Búsqueda avanzada mediante expresiones regulares. |

> ⚙️ Las búsquedas son **case-insensitive** por defecto, pero puedes activar la sensibilidad a mayúsculas.

### 🔎 Ámbitos de búsqueda:
- **Packet List Pane:** cabeceras visibles (número, dirección, protocolo, info).  
- **Packet Details Pane:** información estructurada por capa.  
- **Packet Bytes Pane:** contenido hexadecimal o ASCII del paquete.  

> Si buscas en el panel incorrecto, Wireshark **no encontrará resultados**, aunque el dato esté presente.

---

## 🏷️ Mark Packets

Puedes **marcar paquetes** relevantes para volver fácilmente a ellos.

📌 **Cómo marcar/desmarcar:**
- Menú **Edit → Mark/Unmark Packet**
- Clic derecho sobre el paquete → **Mark Packet**

🖤 Los paquetes marcados se muestran **en negro**, sin importar su color original.

> ⚠️ Las marcas **no se guardan** al cerrar el archivo. Se pierden al finalizar la sesión.

---

## 🗒️ Packet Comments

Wireshark permite agregar **comentarios personalizados** a paquetes.  
Esto es útil para anotar observaciones o resaltar eventos sospechosos.

📘 **Ventajas:**
- Los comentarios **sí se guardan** dentro del archivo `.pcapng`.  
- Otros analistas podrán verlos al abrir el mismo archivo.

🧭 Acceso:  
Clic derecho sobre el paquete → **Packet Comment** → Añadir texto.

---

## 📤 Export Packets

En análisis grandes, puede ser necesario **exportar solo los paquetes relevantes**.  
Esto evita compartir información redundante o sensible.

📦 **Acceso:** `File → Export Specified Packets`

Opciones de exportación:
- Todos los paquetes visibles.  
- Solo los **marcados**.  
- Solo los **filtrados** mediante un display filter.

💡 Ideal para aislar tráfico sospechoso o compartir fragmentos de evidencia.

---

## 📁 Export Objects (Files)

Wireshark puede **extraer archivos transferidos** en la captura.  
Solo funciona con protocolos que transportan objetos, como:

| Protocolo | Descripción |
|------------|--------------|
| **HTTP** | Páginas, imágenes, archivos subidos o descargados |
| **SMB** | Archivos compartidos en redes Windows |
| **TFTP** | Transferencia de archivos ligera |
| **IMF / DICOM** | Correo electrónico o imágenes médicas |

📍 Acceso: `File → Export Objects → [Protocolo]`

---

## 🕒 Time Display Format

Por defecto, Wireshark muestra el tiempo como  
**“Seconds Since Beginning of Capture”**.

Puedes cambiarlo a **UTC** u otros formatos para correlacionar eventos entre capturas:

📍 Menú: `View → Time Display Format`

Opciones comunes:
- UTC (tiempo absoluto)
- Desde el inicio de la captura
- Desde el último paquete
- Personalizado

💡 Usar UTC es lo más práctico para análisis forense o correlación con logs del sistema.

---

## ⚙️ Expert Info

Wireshark detecta estados específicos de protocolos y muestra advertencias o errores.  
Aunque son **solo sugerencias**, ayudan a detectar posibles anomalías.

📊 **Niveles de severidad:**

| Severidad | Color | Descripción |
|------------|--------|--------------|
| **Chat** | Azul | Información de flujo normal. |
| **Note** | Cian | Eventos notables o códigos de aplicación. |
| **Warn** | Amarillo | Advertencias o errores leves. |
| **Error** | Rojo | Paquetes dañados o graves inconsistencias. |

### 🔍 Categorías comunes:

| Grupo | Descripción |
|--------|--------------|
| **Checksum** | Errores en sumas de verificación |
| **Comment** | Detección de comentarios de paquete |
| **Deprecated** | Uso de protocolos obsoletos |
| **Malformed** | Paquetes con estructura incorrecta |

📍 **Acceso:**  
- Barra inferior izquierda (resumen de expertos)  
- Menú: `Analyze → Expert Information`

Permite ver:
- Número de paquete  
- Descripción del evento  
- Protocolo afectado  
- Frecuencia total

---

## 🧭 Conclusión

Wireshark ofrece un conjunto de herramientas que permiten **buscar, marcar, comentar y exportar** paquetes para un análisis preciso.  
Gracias a funciones como **Expert Info**, **Export Objects** y **Time Formatting**, los analistas pueden **detectar anomalías, correlacionar eventos y documentar hallazgos** con eficiencia.

> 💡 *Consejo:* Usa comentarios y marcas junto con filtros para mantener un flujo de trabajo claro y reproducible.


---------------------------------------------------------------------------------------------------------------------------------------------------------------------------

# Wireshark — Packet Filtering

## 🎯 Introducción

Wireshark incluye un motor de **filtrado muy potente** que permite a los analistas centrarse solo en el tráfico relevante.  
Existen **dos tipos principales de filtros**:

| Tipo de filtro | Función | Cuándo se usa |
|----------------|----------|----------------|
| **Capture Filter** | Limita los paquetes **que se capturan** en tiempo real. | Antes de iniciar la captura. |
| **Display Filter** | Muestra solo los paquetes **relevantes** dentro de una captura existente. | Durante el análisis. |

> 💡 En este módulo se abordan los **display filters**, ya que son los más utilizados en la fase de análisis.

---

## 🧩 Filtros y Métodos de Aplicación

Wireshark ofrece dos formas de aplicar filtros:

1. **Mediante consultas (queries)** — usando la barra de filtros o combinaciones lógicas (`and`, `or`, `not`).
2. **Mediante el menú contextual (clic derecho)** — aplicando filtros de forma gráfica sin escribir comandos.

> 📜 **Regla de oro:** “Si puedes hacer clic en ello, puedes filtrarlo o copiarlo.”

---

## 🔎 Apply as Filter

Este es el método **más básico y directo** para filtrar tráfico.

📍 **Pasos:**
1. Haz clic derecho sobre un campo dentro del panel de detalles.  
2. Selecciona **Apply as Filter → Selected / ...and / ...or / ...not**  
3. Wireshark generará automáticamente la consulta del filtro y lo aplicará.

🔧 El filtro se aplicará de inmediato, mostrando solo los paquetes que cumplan la condición.  
El número total y visible de paquetes siempre se muestra en la **barra de estado inferior**.

---

## 💬 Conversation Filter

Sirve para **filtrar todo el tráfico relacionado** con una conversación o sesión específica (por IP y puertos).

📘 **Ejemplo de uso:**
Si seleccionas un paquete TCP entre dos hosts, el filtro mostrará todos los paquetes de esa conversación, tanto de cliente como de servidor.

📍 Acceso:
- Clic derecho → **Conversation Filter → TCP / UDP / IP**
- Menú superior → **Analyse → Conversation Filter**

> 🧠 Ideal para seguir una sesión completa sin escribir filtros manuales.

---

## 🎨 Colourise Conversation

Esta opción **resalta** (colorea) los paquetes pertenecientes a una misma conversación **sin aplicar un filtro**.

📍 Acceso:
- Clic derecho → **Colourise Conversation**
- Menú superior → **View → Colourise Conversation**

🔹 Los paquetes relacionados se colorean automáticamente.  
🔸 Puedes restaurar los colores originales desde:  
**View → Colourise Conversation → Reset Colourisation**

> 💡 Muy útil para analizar varias sesiones simultáneamente sin ocultar el resto del tráfico.

---

## 🧮 Prepare as Filter

Funciona como “Apply as Filter”, pero **no ejecuta el filtro inmediatamente**.  
Solo **prepara** la consulta en la barra de filtros, permitiendo combinarla manualmente.

📍 Acceso:
- Clic derecho sobre el campo → **Prepare as Filter → Selected / ...and / ...or / ...not**

🧠 Ejemplo: puedes preparar dos filtros distintos y unirlos con `and` o `or` antes de aplicarlos.

---

## 📊 Apply as Column

Por defecto, Wireshark muestra columnas básicas (nº de paquete, hora, origen, destino, protocolo, info).  
Con **Apply as Column** puedes **añadir campos personalizados** al panel principal.

📍 Acceso:
- Clic derecho → **Apply as Column**
- Menú superior → **Analyse → Apply as Column**

📘 Ejemplo:
- Si haces clic en “http.user_agent” y aplicas esta opción, verás una nueva columna con todos los valores del *User-Agent* en la lista de paquetes.

> Puedes activar o desactivar columnas haciendo clic en el encabezado del panel principal.

---

## 🔁 Follow Stream

Wireshark divide el tráfico en **fragmentos de paquetes**, pero puedes **reconstruir conversaciones completas** (streams).

📍 Acceso:
- Clic derecho → **Follow → TCP/UDP/HTTP Stream**
- Menú superior → **Analyse → Follow Stream**

💡 Características:
- Muestra el tráfico **reconstruido a nivel de aplicación**.  
- Los datos del **cliente** aparecen en **rojo** y los del **servidor** en **azul**.  
- Permite visualizar datos no cifrados como credenciales, mensajes o archivos.

> ⚠️ Cuando sigues un stream, Wireshark aplica automáticamente un filtro para mostrar solo esa conversación.  
Usa el **botón X** de la barra de filtros para limpiar el filtro y volver a ver todos los paquetes.

---

## 🧭 Conclusión

Los filtros de Wireshark son herramientas esenciales para **aislar, analizar y visualizar tráfico relevante**.  
Desde los filtros simples de campo hasta la reconstrucción completa de sesiones, estas funciones permiten:

- Enfocarse rápidamente en incidentes o anomalías.  
- Correlacionar tráfico entre hosts.  
- Visualizar datos de aplicación sin ruido innecesario.

> 💡 *Consejo:* Usa “Apply as Column” y “Follow Stream” combinados:  
> primero filtra una conversación, luego sigue el stream para analizar el contenido de aplicación.


