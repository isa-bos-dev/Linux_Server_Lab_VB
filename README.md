# Linux Server Lab Setup with VirtualBox

[![Versión en Español](https://img.shields.io/badge/Versi%C3%B3n-Espa%C3%B1ol-blue)](README_ES.md)

This guide details the complete process for creating two Linux server virtual machines (VMs) (Ubuntu Server and Rocky Linux) and configuring them to be managed remotely from Windows using PowerShell and SSH.


## 📜 Table of Contents

- [Prerequisites](#prerequisites)
- [Part 1: Ubuntu Server (`server-ubuntu`)](#part-1-ubuntu-server-server-ubuntu)
  - [1.1. Virtual Machine Creation](#11-virtual-machine-creation)
  - [1.2. Operating System Installation](#12-operating-system-installation)
  - [1.3. First Boot and Update](#13-first-boot-and-update)
- [Part 2: Rocky Linux Server (`server-rocky`)](#part-2-rocky-linux-server-server-rocky)
  - [2.1. Virtual Machine Creation](#21-virtual-machine-creation)
  - [2.2. Operating System Installation](#22-operating-system-installation)
- [Part 3: Remote Connection with PowerShell (SSH)](#part-3-remote-connection-with-powershell-ssh)
  - [3.1. Change the VM Network Configuration](#31-change-the-vm-network-configuration)
  - [3.2. Connect via SSH](#32-connect-via-ssh)

---

## Prerequisites

- **Software:**
    - [Oracle VM VirtualBox](https://www.virtualbox.org/wiki/Downloads) installed on your PC (Windows 11).
    - A Windows terminal (like **PowerShell** or **Windows Terminal**).
- **Installation Files (ISOs):**
    - [**Ubuntu Server LTS**](https://ubuntu.com/download/server): (e.g., `ubuntu-24.04-live-server-amd64.iso`).
    - [**Rocky Linux Minimal**](https://rockylinux.org/download): (e.g., `Rocky-10.0-x86_64-minimal.iso`).

---

## Part 1: Ubuntu Server (`server-ubuntu`)

### 1.1. Virtual Machine Creation

1. Open VirtualBox and click the **"New"** icon.
2. **Virtual machine name and operating system:**
    - **Name:** `server-ubuntu` (or the original name `PostgreSQL Server Ubuntu`).
    - **ISO Image:** Select the Ubuntu Server `.iso` file you downloaded.
    - **Important:** **DO NOT** check the `Proceed with Unattended Installation` box.
3. **Specify virtual hardware:**
    - **Base Memory:** `4096 MB` (4 GB).
    - **Processors:** `2` CPUs.
    - **Important:** Check the `Use EFI` box.
4. **Specify virtual hard disk:**
    - Select `Create a new Virtual Hard Disk`.
    - **Disk Size:** `25.00 GB` (or your preferred size).
    - **Important:** **DO NOT** check the `Pre-allocate Full Size` box (to use dynamic allocation).
5. Click **"Finish"** to create the VM.

### 1.2. Operating System Installation

1. Select the `server-ubuntu` VM and click **"Start"**.
2. In the first menu, select **`Try or Install Ubuntu Server`** and press `Enter`.
3. **Language:** Select `English` (recommended for servers).
4. **Keyboard:** Configure your keyboard layout (e.g., `Spanish`).
5. **Type of Installation:** Leave the default option `(X) Ubuntu Server`.
6. **Network:** Confirm that the network (`enp0s3`) has obtained an IP via DHCP (e.g., `10.0.2.15`). Select `Done`.
7. **Proxy:** Leave it blank. Select `Done`.
8. **Mirror:** Accept the default address. Select `Done`.
9. **Storage:**
    - Leave `(X) Use an entire disk` checked.
    - **Important:** **UNCHECK** the `Set up this disk as an LVM group` box.
    - Select `Done`.
    - On the summary screen, select `Done` and confirm the destructive action by choosing `Continue`.
10. **Profile setup:**
    - Fill in `Your name`, `Your server's name`, `Pick a username` (e.g., `isabosdev`), and a secure password.
11. **SSH Setup:**
    - **Important:** Check the **`[X] Install OpenSSH server`** box.
12. **Featured Server Snaps:** Do not select any. Select `Done`.
13. **Installation:** The system will install. This will take a few minutes.
14. **Reboot:** When you see `Installation complete!`, select **`[ Reboot Now ]`**.
15. The machine will ask you to `Please remove the installation medium, then press ENTER`. VirtualBox usually ejects the ISO automatically. Simply press `Enter`.

### 1.3. First Boot and Update

1. At the `server-ubuntu login:` prompt, log in with the user and password you created.
2. Update the system (optional for the lab, but good practice).
    ```bash
    sudo apt update
    sudo apt upgrade
    ```

---

## Part 2: Rocky Linux Server (`server-rocky`)

### 2.1. Virtual Machine Creation

The process is identical to Ubuntu's, only the name and ISO change.

1. In VirtualBox, click **"New"**.
2. **Virtual machine name and operating system:**
    - **Name:** `server-rocky` (or `Rocky RHEL Server`).
    - **ISO Image:** Select the Rocky Linux Minimal `.iso` file you downloaded.
    - VirtualBox will detect it as `Red Hat (64-bit)` or similar.
    - **Important:** **DO NOT** check unattended installation.
3. **Specify virtual hardware:**
    - **Base Memory:** `4096 MB` (4 GB).
    - **Processors:** `2` CPUs.
    - **Important:** Check the `Use EFI` box.
4. **Specify virtual hard disk:**
    - **Disk Size:** `25.00 GB`.
    - **Important:** **DO NOT** check `Pre-allocate Full Size`.
5. Click **"Finish"**.

### 2.2. Operating System Installation

1. Select the `server-rocky` VM and click **"Start"**.
2. In the first menu, select **`Install Rocky Linux 10`** and press `Enter`.
3. **Language:** Select your preferred installer language (e.g., `English` -> `English (United States)`). Click `Continue`.
4. **Installation Summary:** We must complete all items with warnings.
    - **Network & Host Name:**
        - Click the switch for `enp0s3` to turn it **"ON"**.
        - In the bottom left corner, in `Host Name:`, type `server-rocky`.
        - Click `Apply` and then `Done`.
    - **Installation Destination:**
        - Click on the 25 GB disk icon so a `✓` appears.
        - Leave the configuration on `Automatic`.
        - Click `Done`.
    - **Software Selection:**
        - Ensure **`Minimal Install`** is selected.
        - Click `Done`.
    - **Root Account:**
        - Check `Enable root account`.
        - Set a strong password for `root`.
        - Click `Done`.
    - **User Creation:**
        - Fill in your username (e.g., `isabosdev`) and password.
        - **Important:** Check the **`Make this user an administrator`** box.
        - Click `Done`.
5. **Begin Installation:** Once no warnings remain, the button will become active. Click it.
6. **Reboot:** When the installation finishes, click **`Reboot System`**.
7. **Eject ISO:** If you see the installer again on reboot, manually eject the ISO from the `Devices` > `Optical Drives` > `Remove disk from virtual drive` menu and restart the VM (`Machine` > `Reset`).

---

## Part 3: Remote Connection with PowerShell (SSH)

This process is **identical for both VMs** and is what allows you to control them from Windows.

### 3.1. Change the VM Network Configuration

1. Ensure the VM (e.g., `server-ubuntu`) is **"Powered Off"**.
2. In VirtualBox, select the VM and click **"Settings"**.
3. Go to the **"Network"** section.
4. On "Adapter 1", change "Attached to:" from `NAT` to **`Bridged Adapter`**.
5. Under "Name", ensure your main network adapter (your PC's Wi-Fi or Ethernet) is selected.
6. Click **"OK"**.

### 3.2. Connect via SSH

1. **Start** the VM.
2. Log in to the VM's console (the VirtualBox window).
3. Find the new IP address. The command is the same on both systems:
    ```bash
    ip a
    ```
    Look for the IP in the `enp0s3` section, under `inet`. It will have a format like `192.168.1.X`.

4. **Minimize** the VirtualBox window (leave it running).
5. Open **PowerShell** on your Windows.
6. Type the `ssh` command with your user and the VM's IP:
    ```powershell
    ssh isabosdev@192.168.1.X
    ```
7. The first time, it will ask if you trust the connection. Type **`yes`** and press `Enter`.
8. Type your user password for that VM.

Done! You now control your server from PowerShell and can copy/paste commands.