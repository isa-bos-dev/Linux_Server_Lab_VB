# Creación de Laboratorio de Servidores Linux con VirtualBox

[![English Version](https://img.shields.io/badge/Version-English-blue)](README.md)

Esta guia detalla el proceso completo para crear dos máquinas virtuales (VMs) de servidores Linux (Ubuntu Server y Rocky Linux) y configurarlas para ser administradas remotamente desde Windows usando PowerShell y SSH.

## 📜 Índice

- [Prerrequisitos](#prerrequisitos)
- [Parte 1: Servidor Ubuntu (`servidor-ubuntu`)](#parte-1-servidor-ubuntu-servidor-ubuntu)
  - [1.1. Creación de la Máquina Virtual](#11-creación-de-la-máquina-virtual)
  - [1.2. Instalación del Sistema Operativo](#12-instalación-del-sistema-operativo)
  - [1.3. Primer Inicio y Actualización](#13-primer-inicio-y-actualización)
- [Parte 2: Servidor Rocky Linux (`servidor-rocky`)](#parte-2-servidor-rocky-linux-servidor-rocky)
  - [2.1. Creación de la Máquina Virtual](#21-creación-de-la-máquina-virtual)
  - [2.2. Instalación del Sistema Operativo](#22-instalación-del-sistema-operativo)
- [Parte 3: Conexión Remota con PowerShell (SSH)](#parte-3-conexión-remota-con-powershell-ssh)
  - [3.1. Cambiar la Red de la VM](#31-cambiar-la-red-de-la-vm)
  - [3.2. Conectarse vía SSH](#32-conectarse-vía-ssh)

---

## Prerrequisitos

- **Software:**
    - [Oracle VM VirtualBox](https://www.virtualbox.org/wiki/Downloads) instalado en tu PC (Windows 11).
    - Un terminal de Windows (como **PowerShell** o **Windows Terminal**).
- **Archivos de Instalación (ISOs):**
    - [**Ubuntu Server LTS](https://ubuntu.com/download/server):** (Ej: `ubuntu-24.04-live-server-amd64.iso`).
    - [**Rocky Linux Minimal](https://rockylinux.org/download):** (Ej: `Rocky-10.0-x86_64-minimal.iso`).

---

## Parte 1: Servidor Ubuntu (`servidor-ubuntu`)

### 1.1. Creación de la Máquina Virtual

1. Abre VirtualBox y haz clic en el icono **"Nueva"**.
2. **Virtual machine name and operating system:**
    - **Name:** `servidor-ubuntu` (o el nombre original `Servidor PostgreSQL Ubuntu`).
    - **ISO Image:** Selecciona el archivo `.iso` de Ubuntu Server que descargaste.
    - **Importante:** **NO** marques la casilla `Proceed with Unattended Installation` (Omitir instalación desatendida).
3. **Specify virtual hardware:**
    - **Base Memory:** `4096 MB` (4 GB).
    - **Processors:** `2` CPUs.
    - **Importante:** Marca la casilla `Use EFI`.
4. **Specify virtual hard disk:**
    - Selecciona `Create a new Virtual Hard Disk`.
    - **Disk Size:** `25.00 GB` (o el tamaño que prefieras).
    - **Importante:** **NO** marques la casilla `Reservar completamente` (Pre-allocate Full Size) para usar la reserva dinámica.
5. Haz clic en **"Terminar"** para crear la VM.

### 1.2. Instalación del Sistema Operativo

1. Selecciona la VM `servidor-ubuntu` y haz clic en **"Iniciar"**.
2. En el primer menú, selecciona **`Try or Install Ubuntu Server`** y presiona `Enter`.
3. **Idioma:** Selecciona `English` (recomendado para servidores).
4. **Teclado:** Configura tu distribución de teclado (ej: `Spanish`).
5. **Tipo de Instalación:** Deja la opción por defecto `(X) Ubuntu Server`.
6. **Red:** Confirma que la red (`enp0s3`) haya obtenido una IP por DHCP (ej: `10.0.2.15`). Selecciona `Done`.
7. **Proxy:** Déjalo en blanco. Selecciona `Done`.
8. **Mirror (Espejo):** Acepta la dirección por defecto. Selecciona `Done`.
9. **Almacenamiento (Storage):**
    - Deja marcada `(X) Use an entire disk`.
    - **Importante:** **DESMARCA** la casilla `Set up this disk as an LVM group`.
    - Selecciona `Done`.
    - En la pantalla de resumen, selecciona `Done` y confirma la acción destructiva eligiendo `Continue`.
10. **Perfil de Usuario (Profile setup):**
    - Rellena tu nombre (`Your name`), el nombre del servidor (`Your server's name`), tu nombre de usuario (`Pick a username`, ej: `isabosdev`) y una contraseña segura.
11. **SSH Setup:**
    - **Importante:** Marca la casilla **`[X] Install OpenSSH server`**.
12. **Featured Server Snaps:** No selecciones nada. Selecciona `Done`.
13. **Instalación:** El sistema se instalará. Esto tardará unos minutos.
14. **Reinicio:** Cuando veas `Installation complete!`, selecciona **`[ Reboot Now ]`**.
15. La máquina te pedirá `Please remove the installation medium, then press ENTER`. VirtualBox suele expulsar el ISO automáticamente. Simplemente presiona `Enter`.

### 1.3. Primer Inicio y Actualización

1. En el *prompt* `servidor-ubuntu login:`, inicia sesión con el usuario y contraseña que creaste.
2. Actualiza el sistema. (Esto es opcional para el laboratorio, pero es una buena práctica).Bash
    
    ```bash
    sudo apt update
    sudo apt upgrade
    ```
    

---

## Parte 2: Servidor Rocky Linux (`servidor-rocky`)

### 2.1. Creación de la Máquina Virtual

El proceso es idéntico al de Ubuntu, solo cambian el nombre y el ISO.

1. En VirtualBox, haz clic en **"Nueva"**.
2. **Virtual machine name and operating system:**
    - **Name:** `servidor-rocky` (o `Servidor Rocky RHEL`).
    - **ISO Image:** Selecciona el archivo `.iso` de Rocky Linux Minimal que descargaste.
    - VirtualBox lo detectará como `Red Hat (64-bit)` o similar.
    - **Importante:** **NO** marques la instalación desatendida.
3. **Specify virtual hardware:**
    - **Base Memory:** `4096 MB` (4 GB).
    - **Processors:** `2` CPUs.
    - **Importante:** Marca la casilla `Use EFI`.
4. **Specify virtual hard disk:**
    - **Disk Size:** `25.00 GB`.
    - **Importante:** **NO** marques `Reservar completamente`.
5. Haz clic en **"Terminar"**.

### 2.2. Instalación del Sistema Operativo

1. Selecciona la VM `servidor-rocky` y haz clic en **"Iniciar"**.
2. En el primer menú, selecciona **`Install Rocky Linux 10`** y presiona `Enter`.
3. **Idioma:** Selecciona `Español` (o el idioma que prefieras para el instalador). Haz clic en `Continuar`.
4. **Resumen de la Instalación:** Debemos completar los apartados con advertencias.
    - **Red y nombre de equipo:**
        - Haz clic en el interruptor de `enp0s3` para ponerlo en **"ON"**.
        - En la esquina inferior izquierda, en `Nombre de equipo:`, escribe `servidor-rocky`.
        - Haz clic en `Aplicar` y luego en `Hecho`.
    - **Destino de la instalación:**
        - Haz clic sobre el icono del disco de 25 GB para que muestre un `✓`.
        - Deja la configuración en `Automática`.
        - Haz clic en `Hecho`.
    - **Selección de software:**
        - Asegúrate de que esté marcada **`Instalación mínima`**.
        - Haz clic en `Hecho`.
    - **Cuenta de root:**
        - Marca `Habilitar la cuenta de root`.
        - Establece una contraseña segura para `root`.
        - Haz clic en `Hecho`.
    - **Creación de usuario:**
        - Rellena tu nombre de usuario (ej: `isabosdev`) y contraseña.
        - **Importante:** Marca la casilla **`Hacer de este usuario un administrador`**.
        - Haz clic en `Hecho`.
5. **Comenzar la instalación:** Una vez que no queden advertencias, el botón se activará. Haz clic en él.
6. **Reinicio:** Cuando la instalación termine, haz clic en **`Reiniciar el sistema`**.
7. **Expulsar ISO:** Si al reiniciar vuelves a ver el instalador, "expulsa" el ISO manualmente desde el menú `Dispositivos` > `Unidades ópticas` > `Eliminar disco de la unidad virtual` y reinicia la VM (`Máquina` > `Reiniciar`).

---

## Parte 3: Conexión Remota con PowerShell (SSH)

Este proceso es **idéntico para ambas VMs** y es el que permite controlarlas desde Windows.

### 3.1. Cambiar la Red de la VM

1. Asegúrate de que la VM (ej: `servidor-ubuntu`) esté **"Apagada"**.
2. En VirtualBox, selecciona la VM y haz clic en **"Configuración"**.
3. Ve a la sección **"Red"**.
4. En "Adaptador 1", cambia "Conectado a:" de `NAT` a **`Adaptador puente`**.
5. En "Nombre", asegúrate de que esté seleccionada tu tarjeta de red principal (la Wi-Fi o Ethernet de tu PC).
6. Haz clic en **"Aceptar"**.

### 3.2. Conectarse vía SSH

1. **Inicia** la VM.
2. Inicia sesión en la consola de la VM (la ventana de VirtualBox).
3. Averigua la nueva dirección IP. El comando es el mismo en ambos sistemas:Bash
    
    ```bash
    ip a
    ```
    
    Busca la IP en la sección `enp0s3`, bajo `inet`. Tendrá un formato como `192.168.1.X`.
    
4. **Minimiza** la ventana de VirtualBox (déjala corriendo).
5. Abre **PowerShell** en tu Windows.
6. Escribe el comando `ssh` con tu usuario y la IP de la VM:PowerShell
    
    ```bash
    ssh isabosdev@192.168.1.X
    ```
    
7. La primera vez, te preguntará si confías en la conexión. Escribe **`yes`** y presiona `Enter`.
8. Escribe tu contraseña de usuario de esa VM.

¡Listo! Ahora controlas tu servidor desde PowerShell y puedes copiar y pegar comandos.
