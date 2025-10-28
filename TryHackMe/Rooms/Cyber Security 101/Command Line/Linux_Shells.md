# Resumen: Linux Shells

## Acceso al VM y credenciales (TryHackMe)

Una vez abierta la máquina en vista dividida, tendrás el prompt listo:  
```
user@tryhackme:~$
```

---

## ¿Qué es una shell?
Una **shell** es un intérprete de comandos. En la mayoría de distribuciones Linux la shell por defecto es **Bash (Bourne Again Shell)**, aunque hay otras (zsh, fish, sh, etc.). La shell que se use puede variar según la distribución o preferencias del usuario.

---

## Comandos básicos esenciales

- `pwd` — Muestra el directorio de trabajo actual (Print Working Directory).  
  ```bash
  user@tryhackme:~$ pwd
  /home/user
  ```

- `cd` — Cambia de directorio (Change Directory).  
  ```bash
  user@tryhackme:~$ cd Desktop
  user@tryhackme:~/Desktop$
  ```

- `ls` — Lista los contenidos de un directorio.  
  ```bash
  user@tryhackme:~$ ls
  Desktop  Documents  Downloads  Music  Pictures  Public  Templates  Videos
  ```

- `cat` — Muestra el contenido de un archivo.  
  ```bash
  user@tryhackme:~$ cat filename.txt
  this is a sample file
  this is the second line of the file
  ```

- `grep` — Busca patrones o palabras dentro de archivos (muy útil para extraer información en ficheros grandes). Soporta expresiones regulares.  
  ```bash
  user@tryhackme:~$ grep THM dictionary.txt
  The flag is THM
  ```

---

## Consejos rápidos
- Al abrir una shell normalmente empiezas en tu **home** (`/home/user`).  
- Usa `cd ..` para subir un nivel y `cd -` para volver al directorio anterior.  
- `ls -la` muestra archivos ocultos y permisos.  
- Combina comandos con pipes (`|`) para procesar salida (p. ej. `ls -la | grep key`).

------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

# Resumen: Tipos de Shells en Linux

Linux, al igual que Windows con **Command Prompt** y **PowerShell**, dispone de múltiples **shells** con distintas características y funcionalidades.

---

## 1. Ver la shell actual
Para saber qué shell estás usando:
```bash
echo $SHELL
```
**Ejemplo de salida:**
```
/bin/bash
```

---

## 2. Ver todas las shells instaladas
El archivo `/etc/shells` lista las shells disponibles en tu sistema:
```bash
cat /etc/shells
```
**Ejemplo de salida:**
```
/bin/sh
/bin/bash
/usr/bin/bash
/bin/zsh
/usr/bin/zsh
/bin/dash
/usr/bin/dash
/usr/bin/tmux
/usr/bin/screen
```

---

## 3. Cambiar de shell
Para abrir otra shell temporalmente:
```bash
zsh
```
**Ejemplo:**
```
user@tryhackme:~$ zsh
tryhackme%
```
Para cambiarla de forma permanente:
```bash
chsh -s /usr/bin/zsh
```

---

## 4. Principales shells de Linux

### 🟢 Bourne Again Shell (Bash)
La shell por defecto en la mayoría de distribuciones Linux.  
Mezcla características de shells anteriores (`sh`, `ksh`, `csh`) con mejoras propias.

**Características:**
- Altamente compatible y extensible mediante *scripting*.
- Autocompletado con **TAB**.
- Historial de comandos (`history` o flechas ↑ ↓).
- Amplia documentación y soporte.

---

### 🟣 Friendly Interactive Shell (Fish)
Diseñada para ser **más amigable y visual**. No suele venir instalada por defecto.

**Características:**
- Sintaxis sencilla (ideal para principiantes).
- **Corrección ortográfica automática** de comandos.
- Personalización del *prompt* con temas.
- **Resaltado de sintaxis** (colores para detectar errores).
- Soporta scripting, autocompletado e historial.

---

### 🔵 Z Shell (Zsh)
Considerada una shell moderna y avanzada. Combina lo mejor de Bash y Fish.

**Características:**
- Autocompletado avanzado y scripting potente.
- **Corrección automática** de comandos.
- Gran personalización (plugins como *oh-my-zsh*).
- Puede ser más lenta por su nivel de personalización.
- Soporta *tab completion*, historial y temas visuales.

---

## 5. Comparativa rápida

| Característica | **Bash** | **Fish** | **Zsh** |
|----------------|-----------|-----------|----------|
| **Nombre completo** | Bourne Again Shell | Friendly Interactive Shell | Z Shell |
| **Scripting** | Amplio y muy compatible | Limitado | Avanzado y flexible |
| **Autocompletado** | Básico | Avanzado (contextual) | Muy avanzado (plugins) |
| **Personalización** | Básica | Buena | Excelente (oh-my-zsh) |
| **Facilidad de uso** | Tradicional, menos amigable | Muy fácil e intuitiva | Alta, con personalización |
| **Resaltado de sintaxis** | No | Sí (integrado) | Sí (mediante plugins) |

------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

# Resumen: Scripting en Linux (Bash)

El **scripting** permite automatizar tareas en Linux agrupando varios comandos en un solo archivo ejecutable.  
En lugar de ejecutar los comandos uno por uno, se pueden escribir en un archivo con extensión `.sh` y ejecutarlos todos a la vez.

---

## 1. Crear un script
1. Abre la terminal y usa un editor de texto, por ejemplo `nano`:
   ```bash
   nano first_script.sh
   ```

2. Todo script debe comenzar con una **shebang** (`#!`) que indica el intérprete:
   ```bash
   #!/bin/bash
   ```

---

## 2. Variables
Las **variables** permiten almacenar valores para reutilizarlos en el script.

**Ejemplo:**
```bash
#!/bin/bash
echo "Hey, what’s your name?"
read name
echo "Welcome, $name"
```

### Ejecución del script
1. Dar permisos de ejecución:
   ```bash
   chmod +x first_script.sh
   ```
2. Ejecutarlo:
   ```bash
   ./first_script.sh
   ```

**Nota:** El prefijo `./` indica al shell que el script está en el directorio actual.

**Ejemplo de salida:**
```
Hey, What's your name?
John
Welcome, John
```

---

## 3. Bucles (Loops)
Los **loops** permiten repetir tareas automáticamente.

**Ejemplo:** mostrar los números del 1 al 10.
```bash
#!/bin/bash
for i in {1..10};
do
  echo $i
done
```
**Salida esperada:**
```
1
2
3
...
10
```

- `do` → indica el inicio del bloque de instrucciones.  
- `done` → indica el final del bucle.

---

## 4. Condicionales (if/else)
Las **condiciones** ejecutan código solo si se cumple una cierta situación.

**Ejemplo:**
```bash
#!/bin/bash
echo "Please enter your name first:"
read name

if [ "$name" = "Stewart" ]; then
  echo "Welcome Stewart! Here is the secret: THM_Script"
else
  echo "Sorry! You are not authorized to access the secret."
fi
```

**Ejemplo de salida (caso autorizado):**
```
Please enter your name first:
Stewart
Welcome Stewart! Here is the secret: THM_Script
```

**Ejemplo (caso no autorizado):**
```
Please enter your name first:
Alex
Sorry! You are not authorized to access the secret.
```

---

## 5. Comentarios
Los **comentarios** ayudan a documentar el código sin afectar su ejecución.

Se escriben con el carácter `#` seguido del texto del comentario.

**Ejemplo con comentarios:**
```bash
# Definiendo el intérprete
#!/bin/bash

# Solicitamos el nombre del usuario
echo "Please enter your name first:"

# Guardamos el valor ingresado en una variable
read name

# Comprobamos si el nombre coincide con el autorizado
if [ "$name" = "Stewart" ]; then
  # Si coincide, mostramos el mensaje secreto
  echo "Welcome Stewart! Here is the secret: THM_Script"
else
  # Si no coincide, negamos el acceso
  echo "Sorry! You are not authorized to access the secret."
fi
```

💡 Los comentarios son esenciales para mantener scripts claros, especialmente en proyectos largos o colaborativos.

------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------



