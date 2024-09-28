---
lab:
  title: 實驗室環境設定
  module: Setup
---

# 實驗室環境設定

練習應在託管的實驗室環境中完成。 如果您想要在自己的電腦上完成這些練習，您可以藉由安裝下列軟體達成。 當您使用自己的環境時，可能會遇到未預料的對話方塊和行為。 由於可能發生的本機設定範圍十分廣泛，課程小組無法支援您在自己的環境中可能會遇到的問題。

> **注意**：下列指示適用於 Windows 11 電腦。 您也可以使用 Linux 或 MacOS。 您可能需要為您所選的 OS 調整實驗室指示。

### 基本作業系統 (Windows 11)

#### Windows 11

安裝 Windows 11 並套用所有更新。

#### Edge

安裝 [Edge (Chromium)](https://microsoft.com/edge)

### .NET Core SDK

1. 從 https://dotnet.microsoft.com/download 下載並安裝 (下載 .NET Core SDK - 不只是執行階段)。 如果您要在自己的機器上執行本課程中的實驗室，應該要有 .NET 8.0。

### C++ 可轉散發套件

1. 從 https://aka.ms/vs/16/release/vc_redist.x64.exe 下載並安裝 Visual C++ 可轉散發套件 (x64)。

### Node.JS

1. 從 https://nodejs.org/en/download/ 下載最新的 LTS 版本 
2. 使用預設選項進行安裝

### Python (和必要套件)

1. 從 https://docs.conda.io/en/latest/miniconda.html 下載 3.11 版本 
2. 執行設定以開始安裝 - **重要事項**：選取該選項把 Miniconda 新增到 PATH 變數，並將 Miniconda 註冊為預設的 Python 環境。
3. 完成安裝後，開啟 Anaconda 提示字元，然後輸入下列命令來安裝套件： 

```
pip install flask requests python-dotenv pylint matplotlib pillow
pip install --upgrade numpy
```

### Azure CLI

1. 下載自 https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest 
2. 使用預設選項進行安裝

### Git

1. 使用預設選項從 https://git-scm.com/download.html 下載，並安裝


### Visual Studio Code (和延伸模組)

1. 下載自 https://code.visualstudio.com/Download 
2. 使用預設選項進行安裝 
3. 完成安裝後，開啟 Visual Studio Code 並在 [擴充套件] **** 索引標籤 (CTRL+SHIFT+X) 上，搜尋並安裝 Microsoft 中的下列擴充套件：
    - Python
    - C#
    - Azure Functions
    - PowerShell
