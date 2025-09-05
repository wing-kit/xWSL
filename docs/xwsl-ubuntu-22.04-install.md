### xWSL Ubuntu 22.04 (Jammy) on Windows 11 — Installation Guide

This guide installs xWSL with Ubuntu 22.04 LTS, Xfce 4.18, and xRDP on Windows 11 using the provided `xWSL.cmd` bootstrap script.

- **Prerequisites**
  - **Windows 11** with admin rights
  - Internet access
  - Optional: Hardware virtualization if you plan to switch to WSL2 later

---

### 1) Open an elevated PowerShell

- Click Start, type "PowerShell", right‑click Windows PowerShell, choose "Run as administrator".

---

### 2) Download and run the xWSL installer

Run the following command in the elevated PowerShell. It fetches and launches the `xWSL.cmd` installer from this repository.

```powershell
PowerShell -executionpolicy bypass -command "Invoke-WebRequest https://github.com/DesktopECHO/xWSL/raw/master/xWSL.cmd -UseBasicParsing -OutFile xWSL.cmd ; .\xWSL.cmd"
```

Notes:
- The script enables the Windows Subsystem for Linux feature if it is disabled.
- You will be prompted for several values. For Ubuntu 22.04 (Jammy), type `2` when asked for the Ubuntu version.

Example prompts:
```
[xWSL Installer 20240425]
Enter '2' for Ubuntu 22.04 (Jammy) or '4' for 24.04 (Noble) [4]: 2
Enter a unique name for your distro [xWSL]: XFCE-Jammy
Port number for xRDP [3399]: 3399
Port number for SSHd [3322]: 3322
Set a custom display scale or Enter for default [<auto-detected>]:
Not recommended! Hit X to eXclude distro from Windows Defender:
```

---

### 3) Wait while the installer provisions Ubuntu and desktop components

What happens automatically:
- Downloads an Ubuntu 22.04 rootfs and registers the distro using LxRunOffline
- Installs required packages, Xfce 4.18, xRDP, audio support, and supporting utilities
- Tunes DPI and desktop settings per your scaling choice
- Enables firewall rules for xRDP and SSH
- Creates a non‑root user you specify and adds it to sudoers
- Generates an RDP shortcut and a console shortcut on your Windows desktop

The process typically takes several minutes depending on network and disk speed.

---

### 4) First run and login

- When finished, the script starts the instance and launches the Remote Desktop Connection automatically or places an `.rdp` file on your desktop named like `YourDistro (YourUser) Desktop.rdp`.
- Open that `.rdp` file and log in using the Linux user and password you set during install.

---

### 5) Start xWSL automatically at boot (optional)

To run headless at startup via Task Scheduler:
- Open Task Scheduler, locate the task named after your distro (e.g., `XFCE-Jammy`).
- Right‑click → Properties → check "Run whether user is logged on or not" → OK.
- Provide your Windows credentials when prompted and reboot.

---

### 6) Daily operations

- **Start or relaunch services**: run the scheduled task or execute the generated `Init.cmd` inside the distro folder.
  - Command example:
    ```cmd
    schtasks /run /tn YourDistroName
    ```
- **Stop the instance**:
  ```cmd
  wslconfig /t YourDistroName
  ```
- **Connect via RDP**: use the desktop `.rdp` shortcut or `mstsc.exe /v:localhost:3399` if you kept the default port.

---

### 7) Optional: Convert to WSL2 and use mDNS

If your PC supports virtualization and you prefer WSL2:
```cmd
wsl --shutdown
wsl --set-version YourDistroName 2
schtasks /run /tn YourDistroName
```

For RDP host when on WSL2, use `COMPUTERNAME-YourDistroName.local:3399` (replace port if you chose another).

---

### 8) Uninstall

Inside the distro folder, run the generated uninstaller as Administrator to remove the distro, firewall rules, and task:
```cmd
Uninstall YourDistroName.cmd
```

---

### FAQ / Tips

- **Ubuntu version prompt**: enter `2` for Ubuntu 22.04 (Jammy). Enter `4` for 24.04 (Noble).
- **Windows Defender exclusions**: you can press `X` when prompted to add exclusions for performance. This reduces security, use with caution.
- **Browser support on WSL1**: Seamonkey and Falkon are included. Chrome/Firefox may be limited on WSL1.
- **RDP codec**: H.264 codec is enabled for better performance.
- **File locations**: The distro is installed under the folder you selected when running `xWSL.cmd`.

---

### Advanced customization

- Install additional packages after setup (inside the distro):
  ```bash
  sudo apt-get update
  sudo apt-get install -y build-essential git curl
  ```

- Install from Windows using `wsl.exe` (non-interactive):
  ```powershell
  wsl -d YourDistroName -u root -- bash -lc "apt-get update && apt-get install -y htop"
  ```

- Persist package installs by extending `xWSL.cmd` during provisioning:
  - In `xWSL.cmd`, locate the line that installs Xfce/xRDP (search for `Xfce desktop environment`). Immediately after it, add another invocation that installs your packages within the target distro using the `%GO%` helper. Example:
    ```cmd
    %GO% "DEBIAN_FRONTEND=noninteractive apt-fast -qqy install build-essential git curl --no-install-recommends"
    ```
  - This ensures packages are present in the golden image before the first login.

- Run a one-time post-install script on first boot:
  - Place your script under `/tmp/xWSL/custom.sh` in this repo and make it executable during provisioning:
    ```cmd
    %GO% "chmod +x /tmp/xWSL/custom.sh && /tmp/xWSL/custom.sh"
    ```
  - Example `custom.sh` contents:
    ```bash
    #!/usr/bin/env bash
    set -euo pipefail
    apt-get update
    apt-get install -y vim jq
    ```

- Switch APT mirror for speed:
  ```bash
  sudo sed -i 's|archive.ubuntu.com|mirror.example.com|g' /etc/apt/sources.list
  sudo apt-get update
  ```

- Manage services within the xWSL session:
  - Start/stop xRDP from the init menu (runlevel prompt) or directly:
    ```bash
    sudo service xrdp restart
    ```
  - The provided `initwsl` wrapper starts runlevel services suitable for WSL.

