# Resumen de PowerShell

## ¿Qué es PowerShell?

PowerShell es una herramienta de automatización y gestión de
configuraciones creada por Microsoft. Combina una **interfaz de línea de
comandos** y un **lenguaje de scripting** basado en .NET. A diferencia
de los antiguos comandos de texto, PowerShell es **orientado a
objetos**, lo que le permite manejar datos complejos e interactuar mejor
con los componentes del sistema. Aunque inicialmente fue exclusivo de
Windows, ahora también funciona en **macOS y Linux**.

## Breve historia

En los años 2000, las herramientas tradicionales de Windows como
`cmd.exe` y los scripts por lotes eran insuficientes para las tareas
administrativas avanzadas. **Jeffrey Snover**, ingeniero de Microsoft,
propuso una solución basada en objetos que aprovechara el poder de .NET.
Así nació PowerShell, lanzado en **2006**, para facilitar la
automatización y administración de sistemas.\
En **2016**, Microsoft lanzó **PowerShell Core**, una versión **open
source y multiplataforma**, ampliando su uso a Linux y macOS.

## La potencia de PowerShell

PowerShell trabaja con **objetos**, que contienen propiedades (datos) y
métodos (acciones).\
Por ejemplo, un objeto "archivo" puede tener propiedades como `Nombre` o
`Tamaño`, y métodos como `Copiar()` o `Eliminar()`.

Mientras que los comandos tradicionales trabajan con texto plano, los
**cmdlets** de PowerShell devuelven **objetos estructurados**, lo que
permite manipular datos de forma más potente y flexible sin necesidad de
analizar texto manualmente.

---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------v

# PowerShell Basics

# Resumen: Conexión SSH con Remmina y conceptos básicos de PowerShell

## Lanzar PowerShell desde el símbolo del sistema (cuando sólo hay acceso a CMD)
Si sólo tienes acceso al *Command Prompt* del equipo remoto, escribe:
```
powershell
```
y se iniciará PowerShell mostrando el prompt `PS C:\Users\captain>`.

Otras formas comunes de abrir PowerShell en Windows (GUI):
- Menú Inicio → escribir `powershell` → seleccionar PowerShell.
- Ejecutar (Win + R) → `powershell` → Enter.
- Desde el Explorador de archivos: escribir `powershell` en la barra de direcciones.
- Task Manager → File > Run new task → `powershell`.
- Desde `cmd.exe` escribiendo `powershell`.

---

## Sintaxis básica: Verb-Noun
Los comandos de PowerShell se llaman **cmdlets** y siguen la convención `Verbo-Nombre` (Verb-Noun), lo que facilita entender su función. Ejemplos:
- `Get-Content` — obtiene el contenido de un archivo.  
- `Set-Location` — cambia el directorio de trabajo.

---

## Cmdlets esenciales

### `Get-Command`
Lista cmdlets, funciones, alias y scripts disponibles en la sesión actual. Ejemplo básico:
```powershell
Get-Command
Get-Command -CommandType Function
```

### `Get-Help`
Proporciona documentación sobre un cmdlet (uso, parámetros, ejemplos):
```powershell
Get-Help Get-Date
Get-Help Get-Date -Examples
Get-Help Get-Date -Detailed
Get-Help Get-Date -Online
```

### `Get-Alias`
Muestra alias para cmdlets (atajos tipo `dir` → `Get-ChildItem`, `cd` → `Set-Location`):
```powershell
Get-Alias
```

---

## Extender PowerShell: módulos y cmdlets descargables
PowerShell permite instalar módulos desde repositorios (p. ej. PowerShell Gallery). Comandos útiles:
- `Find-Module -Name "PowerShell*"` — buscar módulos en repositorios.  
- `Install-Module -Name "PowerShellGet"` — instalar un módulo.

**Advertencia**: si el repositorio no está configurado como confiable, PowerShell pedirá confirmación (mensaje `Untrusted repository`).

> Importante: en entornos sin acceso a Internet (como muchas máquinas de laboratorio) estos comandos no funcionarán.


---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

# Resumen: Navegación y gestión de archivos en PowerShell

PowerShell ofrece cmdlets orientados a objetos para navegar por el sistema de archivos y gestionar archivos y carpetas, reemplazando o ampliando comandos tradicionales del CLI de Windows.

---

## Listar contenido: `Get-ChildItem`
- Equivalente a `dir` (Windows) o `ls` (Unix).
- Muestra archivos y directorios del directorio especificado (por defecto, el directorio actual).
```powershell
Get-ChildItem
Get-ChildItem -Path ".\captain-cabin\"
```

---

## Cambiar de directorio: `Set-Location`
- Equivalente a `cd`.
```powershell
Set-Location -Path ".\Documents"
# Prompt: PS C:\Users\captain\Documents>
```

---

## Crear archivos y directorios: `New-Item`
- Crea tanto archivos como carpetas indicando `-ItemType`.
```powershell
New-Item -Path ".\captain-cabin\captain-wardrobe" -ItemType "Directory"
New-Item -Path ".\captain-cabin\captain-wardrobe\captain-boots.txt" -ItemType "File"
```

---

## Eliminar elementos: `Remove-Item`
- Elimina archivos o directorios (reemplaza `del` y `rmdir`):
```powershell
Remove-Item -Path ".\captain-cabin\captain-wardrobe\captain-boots.txt"
Remove-Item -Path ".\captain-cabin\captain-wardrobe"
```

---

## Copiar y mover: `Copy-Item` / `Move-Item`
- Copia o mueve archivos y carpetas.
```powershell
Copy-Item -Path .\captain-cabin\captain-hat.txt -Destination .\captain-cabin\captain-hat2.txt
Move-Item -Path .\origen\archivo.txt -Destination .\destino\archivo.txt
```

---

## Leer contenido de archivos: `Get-Content`
- Muestra el contenido de un archivo (equivalente a `type` o `cat`).
```powershell
Get-Content -Path ".\captain-hat.txt"
# Ejemplo de salida:
#  _           _   
# | |         | |
# | |__   __ _| |_
# ...
# Don't touch my hat!
```

---

## Ventajas principales
- **Unificación de operaciones**: los mismos cmdlets manejan archivos y directorios (p. ej., `New-Item`, `Remove-Item`).
- **Orientado a objetos**: los resultados son objetos con propiedades y métodos, lo que facilita el procesamiento sin parsear texto.
- **Consistencia**: convención `Verb-Noun` para los cmdlets, fácil de aprender y predecible.

---

## Comandos rápidos de referencia
- `Get-ChildItem` — listar contenidos  
- `Set-Location` — cambiar directorio  
- `New-Item` — crear archivo/carpeta  
- `Remove-Item` — borrar archivo/carpeta  
- `Copy-Item` — copiar  
- `Move-Item` — mover  
- `Get-Content` — leer archivo

---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

# Resumen: Piping, Filtering y Sorting en PowerShell

## 1. Piping (tuberías)
El **piping** permite enviar la salida de un comando como entrada a otro usando el símbolo `|`.  
A diferencia del CMD o Unix, PowerShell pasa **objetos** en lugar de texto, lo que conserva sus propiedades y métodos.

**Ejemplo:**
```powershell
Get-ChildItem | Sort-Object Length
```
- `Get-ChildItem` obtiene los archivos como objetos.
- El pipe `|` pasa esos objetos a `Sort-Object`.
- `Sort-Object Length` ordena los archivos por tamaño.

---

## 2. Sorting (ordenamiento)
**Cmdlet:** `Sort-Object`  
Permite ordenar objetos según una o más propiedades.

**Ejemplo:**
```powershell
Get-ChildItem | Sort-Object Length
```

---

## 3. Filtering (filtrado)
### `Where-Object`
Filtra los objetos según condiciones sobre sus propiedades.

**Ejemplo: mostrar solo archivos .txt**
```powershell
Get-ChildItem | Where-Object -Property "Extension" -eq ".txt"
```

### Operadores de comparación
| Operador | Significado                |
|-----------|----------------------------|
| `-eq`     | Igual a                   |
| `-ne`     | No igual a                |
| `-gt`     | Mayor que (estricto)      |
| `-ge`     | Mayor o igual             |
| `-lt`     | Menor que (estricto)      |
| `-le`     | Menor o igual             |
| `-like`   | Coincide con patrón (wildcards) |

**Ejemplo: filtrar archivos cuyo nombre empiece por “ship”**
```powershell
Get-ChildItem | Where-Object -Property "Name" -like "ship*"
```

---

## 4. Selección de propiedades
### `Select-Object`
Permite mostrar propiedades específicas o limitar el número de objetos devueltos.

**Ejemplo:**
```powershell
Get-ChildItem | Select-Object Name, Length
```

**Salida:**
```
Name              Length
----              ------
captain-wardrobe
captain-boots.txt 0
captain-hat.txt   264
...
```

---

## 5. Buscar texto dentro de archivos
### `Select-String`
Busca patrones de texto (soporta **regex**) dentro de archivos, similar a `grep` o `findstr`.

**Ejemplo:**
```powershell
Select-String -Path ".\captain-hat.txt" -Pattern "hat"
```
**Salida:**
```
captain-hat.txt:8:Don't touch my hat!
```

---

## 6. Ejemplo combinado (reto)
Mostrar el archivo más grande en el directorio:
```powershell
Get-ChildItem | Sort-Object Length -Descending | Select-Object -First 1
```

---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

# Resumen: System and Network Information en PowerShell

PowerShell incluye una amplia gama de **cmdlets** para obtener información detallada del **sistema** y la **red**, diseñados para facilitar la administración y automatización de entornos IT.

---

## 1. Información del sistema
### `Get-ComputerInfo`
Obtiene una vista completa de la configuración del sistema, incluyendo:
- Versión del sistema operativo  
- Edición de Windows  
- Información del BIOS  
- Fecha de instalación  
- Detalles de hardware

**Ejemplo:**
```powershell
Get-ComputerInfo
```
Salida (parcial):
```
WindowsEditionId        : ServerDatacenter
WindowsProductName      : Windows Server 2022 Datacenter
WindowsInstallDateFromRegistry : 4/23/2024 6:36:29 PM
...
```
🟢 *Comparación:* el comando clásico `systeminfo` muestra menos detalles.

---

## 2. Cuentas de usuario locales
### `Get-LocalUser`
Lista todos los usuarios locales del sistema, mostrando nombre, estado (Enabled) y descripción.

**Ejemplo:**
```powershell
Get-LocalUser
```
Salida:
```
Name               Enabled Description 
----               ------- -----------
Administrator      True    Built-in account for administering the computer/domain
captain            True    The beloved captain of this pirate ship.
DefaultAccount     False   Managed by the system.
Guest              False   Built-in account for guest access.
```
Permite revisar usuarios activos y configuración básica de seguridad local.

---

## 3. Información de red
### `Get-NetIPConfiguration`
Muestra detalles de las interfaces de red: dirección IP, puerta de enlace y DNS.

**Ejemplo:**
```powershell
Get-NetIPConfiguration
```
Salida (resumida):
```
InterfaceAlias       : Ethernet
IPv4Address          : 10.10.178.209
IPv4DefaultGateway   : 10.10.0.1
DNSServer            : 10.0.0.2
```
🟢 *Equivalente a:* `ipconfig` en CMD, pero más detallado y estructurado.

---

### `Get-NetIPAddress`
Proporciona detalles de **todas las direcciones IP** configuradas, incluyendo IPv4 e IPv6, activas o no.

**Ejemplo:**
```powershell
Get-NetIPAddress
```
Salida (resumida):
```
IPAddress      : 10.10.178.209
InterfaceAlias : Ethernet
AddressFamily  : IPv4

IPAddress      : 127.0.0.1
InterfaceAlias : Loopback Pseudo-Interface 1
AddressFamily  : IPv4
```
Ideal para diagnósticos avanzados o scripts de auditoría de red.

---

## 4. Utilidad práctica
Estos cmdlets son esenciales para:
- Auditar sistemas y configuraciones de red.  
- Verificar IPs, DNS y puertas de enlace.  
- Identificar usuarios locales y configuraciones de seguridad.  
- Obtener información de hardware y software de forma automatizada.

------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

# Resumen: Monitoreo del sistema y red en PowerShell

PowerShell no solo permite obtener información estática del sistema, sino también **datos dinámicos** sobre procesos, servicios y conexiones de red. Estos cmdlets son esenciales para la **administración**, el **análisis forense** y la **respuesta a incidentes**.

---

## 1. Procesos activos
### `Get-Process`
Muestra todos los procesos en ejecución junto con su uso de CPU, memoria y otros detalles.

**Ejemplo:**
```powershell
Get-Process
```
**Salida:**
```
Handles  NPM(K)    PM(K)      WS(K)     CPU(s)     Id  SI ProcessName 
-------  ------    -----      -----     ------     --  -- -----------
   309      13    18312       1256       0.52   1524   0 amazon-ssm-agent
    78       6     4440        944       0.02    516   0 cmd
    94       7     1224       1744       0.31    568   0 conhost
```
📊 Permite identificar procesos que consumen demasiados recursos o comportamientos sospechosos.

---

## 2. Servicios del sistema
### `Get-Service`
Lista los servicios instalados y su estado (**Running**, **Stopped**, **Paused**).  
Ideal para la administración o el análisis forense.

**Ejemplo:**
```powershell
Get-Service
```
**Salida:**
```
Status   Name               DisplayName                           
------   ----               -----------
Stopped  Amazon EC2Launch   Amazon EC2Launch
Running  AmazonSSMAgent     Amazon SSM Agent
Running  BFE                Base Filtering Engine
Stopped  ClipSVC            Client License Service (ClipSVC)
```
🔍 Los analistas pueden detectar servicios desconocidos o potencialmente maliciosos.

---

## 3. Conexiones de red activas
### `Get-NetTCPConnection`
Muestra las conexiones TCP actuales, incluyendo IPs locales/remotas, puertos y estado.

**Ejemplo:**
```powershell
Get-NetTCPConnection
```
**Salida:**
```
LocalAddress   LocalPort  RemoteAddress   RemotePort  State      OwningProcess
-------------  ---------  --------------  ----------  -----      --------------
0.0.0.0        22         0.0.0.0         0           Listen     1444
10.10.178.209  22         10.14.87.60     53523       Established 1444
0.0.0.0        3389       0.0.0.0         0           Listen     980
```
🌐 Útil para identificar conexiones activas, backdoors o tráfico sospechoso durante un incidente.

---

## 4. Verificar integridad de archivos
### `Get-FileHash`
Calcula el **hash** de un archivo para verificar su integridad o detectar alteraciones.  
Compatible con algoritmos como SHA256, SHA1 o MD5.

**Ejemplo:**
```powershell
Get-FileHash -Path ".\ship-flag.txt"
```
**Salida:**
```
Algorithm       Hash                      Path 
---------       ----                      ----
SHA256          54D2EC3C12BF3D[...]       C:\Users\captain\Documents\captain-cabin\ship-flag.txt
```
🛡️ Fundamental en análisis de malware y validación de integridad.

---

## 5. Uso combinado para análisis
Ejemplo de uso conjunto para una sesión de respuesta a incidentes:
```powershell
Get-Process | Sort-Object CPU -Descending | Select-Object -First 10
Get-Service | Where-Object Status -eq "Running"
Get-NetTCPConnection | Where-Object State -eq "Established"
Get-FileHash -Path "C:\Users\captain\Documents\captain-cabin\ship-flag.txt"
```
Permite:
- Ver los procesos más activos.  
- Comprobar servicios en ejecución.  
- Analizar conexiones de red abiertas.  
- Validar la integridad de archivos críticos.

---------------------------------------------------------------------------------------------------------------------------------------------------------------------

# Resumen: PowerShell Scripting

## 1. ¿Qué es el scripting?
**Scripting** consiste en escribir una serie de comandos en un archivo de texto (script) para automatizar tareas que normalmente se realizarían manualmente.  
Cada línea representa una instrucción que el sistema ejecuta de forma automática.

Ventajas principales:
- 💡 Ahorra tiempo y reduce errores humanos.  
- ⚙️ Permite realizar tareas complejas o repetitivas.  
- 🔁 Facilita la administración, el análisis y la respuesta ante incidentes.

Ejemplo de un script simple:
```powershell
# script.ps1
Get-Date
Get-ChildItem C:\Users\captain\Documents
```
Para ejecutarlo:
```powershell
.\script.ps1
```

---

## 2. Importancia del scripting en ciberseguridad

### 🔵 Blue Team (defensivo)
Profesionales como **incident responders**, **analistas de malware** o **threat hunters** usan scripts para:
- Analizar logs automáticamente.  
- Detectar anomalías o IOCs (indicadores de compromiso).  
- Automatizar escaneos de seguridad.  
- Revertir o analizar código malicioso.

### 🔴 Red Team (ofensivo)
Pentesters y hackers éticos usan PowerShell para:
- Enumerar sistemas y ejecutar comandos remotos.  
- Automatizar exploits o ataques.  
- Crear scripts ofuscados para evadir defensas.  
- Simular ataques reales y probar la resiliencia de un sistema.

### ⚙️ Administración de sistemas
Los administradores pueden automatizar:
- Verificación de integridad.  
- Configuración y políticas de seguridad.  
- Supervisión del estado de sistemas y redes.  
- Respuestas automáticas ante incidentes.

👉 En conjunto, PowerShell scripting mejora la **eficiencia y seguridad** de entornos empresariales.

---

## 3. Ejecución remota con `Invoke-Command`
El cmdlet **`Invoke-Command`** permite ejecutar comandos o scripts en **equipos remotos**.  
Es clave para la administración, la automatización distribuida y también para tareas ofensivas o de respuesta a incidentes.

### Ejemplo 1 — Ejecutar un script remoto
```powershell
Invoke-Command -FilePath "C:\scripts\test.ps1" -ComputerName Server01
```
Ejecuta el script `test.ps1` en `Server01` y devuelve los resultados al equipo local.

### Ejemplo 2 — Ejecutar un comando remoto
```powershell
Invoke-Command -ComputerName Server01 -Credential Domain01\User01 -ScriptBlock { Get-Culture }
```
- `-ComputerName`: equipo remoto.  
- `-Credential`: credenciales para autenticarse.  
- `-ScriptBlock`: bloque de comandos a ejecutar remotamente.

💬 El resultado es como si se escribieran los comandos directamente en la PowerShell del equipo remoto.

---

## 4. Beneficios clave del scripting en PowerShell
| Rol | Uso principal | Beneficio |
|------|----------------|------------|
| Blue Team | Automatización defensiva y análisis de incidentes | Detección rápida y respuesta eficiente |
| Red Team | Automatización de pruebas de intrusión y evasión | Evaluación de seguridad realista |
| SysAdmins | Gestión masiva de sistemas y configuraciones | Escalabilidad y control centralizado |

---------------------------------------------------------------------------------------------------------------------------------------------------------------------




