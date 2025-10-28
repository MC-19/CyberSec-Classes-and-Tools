# Windows Commands - Cybersecurity 101

#Basic System Information

## 🧠 Conceptos clave

- Los **comandos solo funcionan si están en el “Windows Path”**, es decir, en las rutas donde Windows busca los ejecutables.

### 🔍 Ver las rutas del PATH
```cmd
set
```
Busca la línea que empieza con `Path=` para ver las rutas donde se ejecutan los comandos.

---

## 💻 Información del sistema

### Ver la versión de Windows
```cmd
ver
```

### Ver información detallada del sistema
```cmd
systeminfo
```
Muestra datos como el nombre del host, versión del sistema operativo, procesador, memoria, etc.

---

## 📄 Manejo de salidas largas

Si el resultado de un comando es muy largo, puedes verlo **por páginas** usando `| more`:

```cmd
driverquery | more
```

- Avanzar una página → `Espacio`
- Salir → `Ctrl + C`

---

## ⚙️ Comandos útiles

| Comando | Descripción |
|----------|--------------|
| `help` | Muestra ayuda sobre un comando específico |
| `cls` | Limpia la pantalla de la terminal |

------------------------------------------------------------------------------

# Network Troubleshooting

# Windows Networking Commands - Cybersecurity 101

## 🌐 Configuración de red

Puedes ver la información de red con el comando:
```cmd
ipconfig
```
Muestra:
- Dirección **IPv4**
- **Máscara de subred**
- **Puerta de enlace predeterminada**

Para más detalles (DNS, DHCP, etc.):
```cmd
ipconfig /all
```
Incluye:
- Servidores DNS
- Dirección MAC
- Estado de DHCP
- Fecha de concesión (lease)

---

## 🧭 Resolución y diagnóstico de red

### 🏓 Probar conectividad con `ping`
Envía paquetes ICMP para comprobar si hay conexión con un destino.
```cmd
ping example.com
```
Muestra:
- Si el destino responde
- Tiempo promedio de ida y vuelta (**RTT**) en milisegundos

---

### 🛣️ Rastrear la ruta con `tracert`
Muestra todos los routers por los que pasan los paquetes hasta el destino.
```cmd
tracert example.com
```
- Indica la cantidad de **saltos (hops)**  
- Cada línea representa un router o punto intermedio

---

### 🧭 Consultar DNS con `nslookup`
Sirve para traducir nombres de dominio a direcciones IP.
```cmd
nslookup example.com
```
También puedes especificar un servidor DNS:
```cmd
nslookup example.com 1.1.1.1
```
Muestra:
- Servidor usado para la consulta
- Dirección IPv4 e IPv6 del dominio

---

## 🔒 Conexiones de red y puertos

### Ver conexiones activas con `netstat`
```cmd
netstat
```
Muestra las conexiones **establecidas** y **puertos en escucha**.

### Opciones útiles
| Opción | Descripción |
|---------|--------------|
| `-a` | Muestra todas las conexiones y puertos en escucha |
| `-b` | Muestra el programa asociado a cada puerto |
| `-o` | Muestra el **PID** (ID del proceso) |
| `-n` | Muestra direcciones y puertos en formato numérico |

Puedes combinar todas:
```cmd
netstat -abon
```
Esto permite ver:
- Los programas asociados (ej. `sshd.exe`)
- Los puertos que están escuchando (ej. `22` para SSH)
- Los **PIDs** de cada conexión

---------------------------------------------------------------------------

# Windows File and Disk Management - Cybersecurity 101

## 📁 Trabajando con directorios

### Ver el directorio actual
```cmd
cd
```
Muestra la **unidad y carpeta actual** (equivalente a “¿Dónde estoy?”).

### Ver el contenido del directorio
```cmd
dir
```
Muestra los archivos y subcarpetas del directorio actual.

#### Opciones útiles
| Opción | Descripción |
|---------|-------------|
| `/a` | Muestra archivos ocultos y de sistema |
| `/s` | Muestra archivos del directorio actual y subdirectorios |

### Ver estructura visual de carpetas
```cmd
tree
```
Muestra un árbol con las carpetas y subcarpetas.

### Cambiar de directorio
```cmd
cd nombre_carpeta
```
Equivalente a hacer doble clic sobre una carpeta.  
Para subir un nivel:
```cmd
cd ..
```

### Crear y eliminar directorios
```cmd
mkdir nombre_carpeta   # Crea una carpeta
rmdir nombre_carpeta   # Elimina una carpeta
```

---

## 📄 Trabajando con archivos

### Ver contenido de archivos de texto
```cmd
type archivo.txt
```
Muestra el contenido completo del archivo.  
Para archivos largos:
```cmd
more archivo.txt
```
- **Espacio** → Avanza una página  
- **Enter** → Avanza una línea

### Copiar archivos
```cmd
copy origen destino
```
Ejemplo:
```cmd
copy test.txt test2.txt
```

### Mover archivos
```cmd
move archivo destino
```
Ejemplo:
```cmd
move test2.txt ..
```
(Mueve el archivo un nivel arriba en el árbol de carpetas)

### Eliminar archivos
```cmd
del archivo.txt
```
o
```cmd
erase archivo.txt
```

### Usar comodines (wildcards)
```cmd
copy *.md C:\Markdown
```
Copia **todos los archivos .md** al directorio especificado.

----------------------------------------------------------------------------

# Windows Task and Process Management

## ⚙️ Gestión de tareas y procesos

En Windows, además del **Administrador de tareas (Task Manager)**, puedes controlar los procesos desde la **línea de comandos**.

---

## 📋 Ver procesos activos

### Listar todos los procesos
```cmd
tasklist
```
Muestra una tabla con:
- **Image Name** → nombre del proceso (ej. `svchost.exe`)
- **PID** → identificador del proceso
- **Mem Usage** → memoria utilizada

Ejemplo de salida:
```
Image Name                     PID Session Name        Session#    Mem Usage
========================= ======== ================ =========== ============
System Idle Process              0 Services                   0          8 K
System                           4 Services                   0         88 K
...
```
---

## 🔍 Filtrar procesos específicos

Puedes filtrar resultados para mostrar solo los procesos que coincidan con un criterio.

### Sintaxis:
```cmd
tasklist /FI "imagename eq nombre_proceso.exe"
```
Ejemplo:
```cmd
tasklist /FI "imagename eq sshd.exe"
```
Salida:
```
Image Name                     PID Session Name        Session#    Mem Usage
========================= ======== ================ =========== ============
sshd.exe                      2116 Services                   0      6,992 K
sshd.exe                      2712 Services                   0      7,668 K
sshd.exe                      4752 Services                   0      7,372 K
```

📘 *El parámetro `/FI` significa “Filter Input” (filtro de entrada).*

---

## ❌ Finalizar procesos

Una vez identificado el proceso (por su **PID**), puedes detenerlo con:

```cmd
taskkill /PID <id_proceso>
```
Ejemplo:
```cmd
taskkill /PID 4567
```
Esto **termina el proceso con PID 4567**.

----------------------------------------------------------------------------


