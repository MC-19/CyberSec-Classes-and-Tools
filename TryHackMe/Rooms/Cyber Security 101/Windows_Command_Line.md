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

---

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

---


