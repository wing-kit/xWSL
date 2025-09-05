# [xWSL.cmd (版本 20240425)](https://github.com/DesktopECHO/xWSL)

[English README](README.md) | 繁體中文

## 為什麼需要 xWSL？為什麼它適合開發？

- **一行指令，建立可重現的 Linux GUI 開發環境**：幾分鐘內在 Windows 上自動佈署 Ubuntu 22.04/24.04、XFCE 與 xRDP，團隊成員之間一致且可重現。
- **無須笨重 VM 的完整 Linux 桌面**：在 WSL1/WSL2 上以 RDP 提供完整 GUI，啟動快速、資源開銷更低，勝過傳統虛擬機。
- **天生遠端友善**：啟用 H.264 RDP 與音訊，無論本機或跨網路皆有流暢體驗；可用 `mstsc.exe` 或 Chrome Remote Desktop 連線。
- **務實預設、開箱即用**：預先設定常用套件、媒體、主題與字型，並調校 WSL 相關環境變數，讓你更快進入生產力。
- **適用受限環境**：支援 Windows Server 2019、Windows 10 1809+ 與 Hyper‑V Core；即使無法使用 Microsoft Store 或 GUI 也可安裝。
- **團隊化與可自訂**：Fork 後只改少數變數即可擁有自家佈署腳本，符合企業影像與政策。
- **類服務運作**：可設定開機自動啟動的排程工作，登入即是乾淨狀態；也提供簡易啟停指令。
- **網路可發現性**：透過 Multicast DNS，輕鬆在區網中尋找 WSL2 執行個體。

### 適用對象

- **Windows 開發者**：需要穩定的 Linux 桌面以使用相關工具鏈與 GUI 應用。
- **DevOps／SRE**：需要快速、可拋棄且可重現的除錯環境。
- **團隊**：希望以最少摩擦統一開發工作站設定。

在 WSL1 或 WSL2 上，透過網路自動部署 **Ubuntu 22.04** 或 **24.04**、**Xfce 4.18** 並啟用 **xRDP** 套件。  
其他發行版請參考 **KDE Neon** [**(kWSL)**](https://github.com/DesktopECHO/kWSL) 與 **Kali Linux** [**(Kali-xRDP)**](https://github.com/DesktopECHO/Kali-xRDP)。

---

### 快速開始

請自提升權限的 PowerShell 視窗執行：

```powershell
PowerShell -executionpolicy bypass -command "Invoke-WebRequest https://github.com/DesktopECHO/xWSL/raw/master/xWSL.cmd -UseBasicParsing -OutFile xWSL.cmd ; .\xWSL.cmd"
```

安裝程式會詢問發行版名稱、RDP/SSH 埠號、DPI 值等，並自動下載所需元件。完成後即可透過遠端桌面連線到你的 Linux 桌面。

---

### 自訂與團隊化

- 登入 GitHub 並 fork 本專案
- 編輯 `xWSL.cmd`，將 `SET GITORG=DesktopECHO` 改為你自己的組織名稱
- 使用你的儲存庫網址啟動安裝指令，即可為團隊提供一致的工作站環境

---

### 其他資訊

- 可設定以排程工作在開機時啟動，並能於 Task Scheduler 或命令列啟停
- WSL1/WSL2 可相互轉換，並能透過 `-xWSL.local` 主機名搭配 RDP 連線
- 內建套件與佈署流程會持續更新，旨在提供穩定、順暢的開發體驗

更多詳細說明、限制與截圖，請參考英文版 `README.md` 與 `docs/` 中的文件。