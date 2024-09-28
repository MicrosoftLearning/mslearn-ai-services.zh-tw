---
lab:
  title: 管理 Azure AI 服務安全性
  module: Module 2 - Developing AI Apps with Azure AI Services
---

# 管理 Azure AI 服務安全性

安全性是任何應用程式的重要考量事項，而身為開發人員，您應該確保資源的存取 (例如 Azure AI 服務) 僅限制於需要的人員。

Azure AI 服務的存取通常是透過驗證金鑰來控制，這會在您一開始建立 Azure AI 服務資源時產生。

## 在 Visual Studio Code 中，複製您的存放庫

您將使用 Visual Studio Code 開發您的程式碼。 您應用程式的程式碼檔案已在 GitHub 存放庫中提供。

> **秘訣**：如果您最近已複製 **mslearn-ai-language** 存放庫，請在 Visual Studio 程式碼中開啟它。 否則，請遵循下列步驟將其複製到您的開發環境。

1. 啟動 Visual Studio Code。
2. 開啟選擇區 (SHIFT+CTRL+P) 並執行 **Git：複製 ** 命令，將 `https://github.com/MicrosoftLearning/mslearn-ai-services` 存放庫複製到本機資料夾 (哪個資料夾無關緊要)。
3. 複製存放庫後，請在 Visual Studio Code 中開啟此資料夾。
4. 等候其他檔案安裝以在需要時支援存放庫中的 C# 程式碼專案

    > **注意**：如果系統提示您新增必要的資產來組建和偵錯，請選取 [現在不要]****。

5. 展開 `Labfiles/02-ai-services-security` 資料夾。

已提供 C# 和 Python 的程式碼。 展開慣用語言的資料夾。

## 佈建 Azure AI 服務資源

如果您的訂用帳戶中還沒有 **Azure AI 服務**資源，則必須加以佈建。

1. 開啟 Azure 入口網站 (位於 `https://portal.azure.com`)，使用與您的 Azure 訂用帳戶相關聯的 Microsoft 帳戶進行登入。
2. 在頂端搜尋列中，搜尋 *Azure AI 服務*、選取 [Azure AI 服務]****，並使用下列設定建立 Azure AI 服務多服務帳戶資源：
    - **訂用帳戶**：您的 Azure 訂用帳戶**
    - **資源群組**：*選擇或建立資源群組 (如果您使用受限制的訂用帳戶，則您可能沒有建立新資源群組的權限 - 請使用所提供的資源群組)*
    - **區域**：*選擇任何可用的區域*
    - **名稱**：輸入唯一名稱**
    - **定價層**:標準 S0
3. 選取必要的核取方塊並建立資源。
4. 等候部署完成，然後檢視部署的詳細資料。

## 管理驗證金鑰

當您建立 Azure AI 服務資源時，會產生兩個驗證金鑰。 您可以在 Azure 入口網站中，或使用 Azure 命令列介面 (CLI) 來管理這些金鑰。 

1. 選擇取得驗證金鑰和端點的方法： 

    **使用 Azure 入口網站**，在 Azure 入口網站中移至您的 Azure AI 服務資源，並檢視其**金鑰和端點**頁面。 此頁面包含您需要連線到資源的資訊，並從您開發的應用程式加以使用。 具體而言：
    - 用戶端應用程式可以傳送要求的 HTTP *端點*。
    - 兩個可用於驗證的*金鑰* (用戶端應用程式可以使用任一金鑰。 常見的做法是將一個金鑰用於開發，而將另一個金鑰用於實際執行環境。 開發人員完成其工作後，您可以輕鬆重新產生開發金鑰，以防止繼續存取)。
    - 裝載資源的*位置*。 這對於某些 API (但非所有) 的要求而言是必要的。

    **使用命令列**：或者，您可以使用下列命令來取得 Azure AI 服務金鑰的清單。 在 Visual Studio Code 中，開啟新終端。 然後貼上下列命令；將 *&lt;resourceName&gt;* 替換為您的 Azure AI 服務資源名稱，並將 *&lt;resourceGroup&gt;* 替換為您在其中建立的資源群組名稱。

    ```
    az cognitiveservices account keys list --name <resourceName> --resource-group <resourceGroup>
    ```

    此命令會傳回 Azure AI 服務資源的金鑰清單 - 有兩個金鑰，名為 **key1** 和 **key2**。

    > **提示**：如果您尚未驗證 Azure CLI，請先執行 `az login` 並登入您的帳戶。

2. 若要測試 Azure AI 服務，您可以使用 **curl** - HTTP 要求的命令列工具。 在 **02-ai-services-security** 資料夾中，開啟 **rest-test.cmd** 並編輯其中包含的 **curl** 命令 (如下所示)，將 *&lt;yourEndpoint&gt;* 和 *&lt;yourKey&gt;* 取代為您的端點 URI 和 **Key1** 金鑰，以在 Azure AI 服務資源中使用分析文字 API。

    ```bash
    curl -X POST "<yourEndpoint>/language/:analyze-text?api-version=2023-04-01" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: <your-key>" --data-ascii "{'analysisInput':{'documents':[{'id':1,'text':'hello'}]}, 'kind': 'LanguageDetection'}"
    ```

3. 儲存您的變更。 在終端機中，瀏覽至「02-ai-services-security」資料夾。 （**注意**：您可以在總管中以滑鼠右鍵按一下「02-ai-services-security」資料夾，然後選取 *在整合式終端中開啟*來執行此動作）。 然後執行下列命令：

    ```
    ./rest-test.cmd
    ```

此命令會傳回 JSON 文件，其中包含在輸入資料中偵測到的語言相關資訊 (應該是英文)。

4. 如果金鑰遭到入侵，或擁有金鑰的開發人員不再需要存取權，則您可以在入口網站或使用 Azure CLI 重新產生金鑰。 執行下列命令來重新產生 **key1** 金鑰 (針對資源，取代 *&lt;resourceName&gt;* 和 *&lt;resourceGroup&gt;*)。

    ```
    az cognitiveservices account keys regenerate --name <resourceName> --resource-group <resourceGroup> --key-name key1
    ```

將傳回 Azure AI 服務資源的金鑰清單 - 請注意，**key1** 自上次擷取後已變更。

5. 使用舊金鑰重新執行 **rest-test** 命令 (您可以使用鍵盤上的 **^** 箭號來循環先前的命令)，並驗證其現在是否失敗。
6. 在 *rest-test.cmd* 中編輯 **curl** 命令，將金鑰取代為新的 **key1** 值，並儲存變更。 然後重新執行 **rest-test** 命令，並驗證其是否成功。

> **秘訣**：在此練習中，您已使用 Azure CLI 參數的完整名稱，例如 **--resource-group**。  您也可以使用較短的替代名稱 (例如 **-g**)，讓您的命令變得較不冗長 (但較難理解)。  [Azure AI 服務 CLI 命令參考](https://docs.microsoft.com/cli/azure/cognitiveservices?view=azure-cli-latest)會列出每個 Azure AI 服務 CLI 命令的參數選項。

## 使用 Azure KeyVault 保護金鑰存取

您可以使用金鑰進行驗證，來開發取用 Azure AI 服務的應用程式。 不過，這表示應用程式程式碼必須能夠取得金鑰。 其中一個選項是將金鑰儲存在環境變數或部署應用程式的組態檔中，但此方法會使金鑰容易遭受未經授權的存取。 在 Azure 上開發應用程式時，更好的方法是將金鑰安全儲存在 Azure Key Vault 中，並透過*受控識別*提供金鑰的存取權 (亦即應用程式本身所使用的使用者帳戶)。

### 建立金鑰保存庫並新增秘密

您必須先為 Azure AI 服務金鑰建立金鑰保存庫並新增*祕密*。

1. 記下 Azure AI 服務資源的 **key1** 值 (或將其複製到剪貼簿)。
2. 在 Azure 入口網站的 [首頁]**** 頁面上，選取 [&#65291;建立資源]**** 按鈕、搜尋*金鑰保存庫*，並使用下列設定建立**金鑰保存庫**資源：

    - **[基本] 索引標籤**
        - **訂用帳戶**：您的 Azure 訂用帳戶**
        - **資源群組**：*與您 Azure AI 服務資源相同的資源群組*
        - **金鑰保存庫名稱**：*輸入唯一名稱*
        - **區域**：*與您 Azure AI 服務資源相同的區域*
        - **定價層**：標準
    
    - [存取組態]**** 索引標籤
        -  **權限模型**：保存庫存取原則
        - 向下捲動至 [存取原則]**** 區段，然後使用左側的核取方塊選取您的使用者。 然後選取 [檢閱 + 建立]****，然後選取 [建立]**** 以建立您的資源。
     
3. 等候部署完成，然後移至您的金鑰保存庫資源。
4. 在左側瀏覽窗格中，選取 [物件] 區段中的 [祕密]****。
5. 選取 [+ 產生/匯入]****，並使用下列設定新增秘密：
    - **上傳選項**：手動
    - **名稱**：AI-Services-Key *(請務必完全符合此名稱，因為稍後您將根據此名稱來執行程式碼擷取祕密)*
    - **祕密值**： *您的 **key1** Azure AI 服務金鑰*
6. 選取 **建立**。

### 建立服務主體

若要存取金鑰保存庫中的秘密，您的應用程式必須使用具有秘密存取權的服務主體。 您將使用 Azure 命令列介面 (CLI) 來建立服務主體、尋找其物件識別碼，以及授與 Azure Valut 中祕密的存取權。

1. 執行下列 Azure CLI 命令，將 *&lt;spName&gt;* 取代為應用程式識別碼的唯一適當名稱 (例如，*ai-app* 加上附加在結尾的縮寫；名稱在您的租用戶中必須是唯一的)。 也請將 *&lt;subscriptionId&gt;* 和 *&lt;resourceGroup&gt;* 取代為您訂用帳戶識別碼的正確值，以及包含 Azure AI 服務和金鑰保存庫資源的資源群組：

    > **秘訣**：如果您不確定訂用帳戶識別碼，請使用 **az account show** 命令來擷取您的訂用帳戶資訊 - 訂用帳戶識別碼是輸出中的 **id** 屬性。 如果您看到有關物件已存在的錯誤，請選擇不同的唯一名稱。

    ```
    az ad sp create-for-rbac -n "api://<spName>" --role owner --scopes subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>
    ```

此命令的輸出包含新服務主體的相關資訊。 其內容應該會如下所示：

    ```
    {
        "appId": "abcd12345efghi67890jklmn",
        "displayName": "api://ai-app-",
        "password": "1a2b3c4d5e6f7g8h9i0j",
        "tenant": "1234abcd5678fghi90jklm"
    }
    ```

記下 **appId**、**password** 和 **tenant** 值 - 稍後將需要這些值 (如果您關閉此終端，則將無法擷取密碼；因此，請務必立即記下這些值 - 您可以將輸出貼至本機電腦上的新文字檔，以確保稍後您可以找到所需的值！)

2. 若要取得服務主體的**物件識別碼**，請執行下列 Azure CLI 命令，以將 *&lt;appId&gt;* 取代為服務主體應用程式識別碼的值。

    ```
    az ad sp show --id <appId>
    ```

3. 複製回應中所傳回 JSON 中的 `id` 值。 
3. 若要為新的服務主體指派權限以存取金鑰保存庫中的祕密，請執行下列 Azure CLI 命令，將 *&lt;keyVaultName&gt;* 取代為您的 Azure Key Vault 資源名稱，並將 *&lt;objectId&gt;* 取代為您剛才所複製服務主體的識別碼值。

    ```
    az keyvault set-policy -n <keyVaultName> --object-id <objectId> --secret-permissions get list
    ```

### 在應用程式中使用服務主體

現在您已準備好在應用程式中使用服務主體識別，因此其可以存取金鑰保存庫中的祕密 Azure AI 服務金鑰，並用來連線至您的 Azure AI 服務資源。

> **注意**：在此練習中，我們會將服務主體認證儲存在應用程式組態中，並使用來驗證應用程式程式碼中的 **ClientSecretCredential** 識別。 這適用於開發和測試，但在實際執行環境的應用程式中，系統管理員會將*受控識別*指派給應用程式，讓其使用服務主體識別來存取資源，而無須快取或儲存密碼。

1. 在您的終端中，執行 `cd C-Sharp` 或 `cd Python`，根據語言喜好設定切換至 **C-Sharp** 或 **Python** 資料夾。 然後執行 `cd keyvault_client` 以瀏覽至應用程式資料夾。
2. 針對您的語言偏好設定，執行適當的命令來安裝您必須在 Azure AI 服務資源中用於 Azure Key Vault 和文字分析 API 的套件：

    **C#**

    ```
    dotnet add package Azure.AI.TextAnalytics --version 5.3.0
    dotnet add package Azure.Identity --version 1.12.0
    dotnet add package Azure.Security.KeyVault.Secrets --version 4.6.0
    ```

    **Python**

    ```
    pip install azure-ai-textanalytics==5.3.0
    pip install azure-identity==1.17.1
    pip install azure-keyvault-secrets==4.8.0
    ```

3. 檢視 **keyvault-client** 資料夾的內容，並注意其中包含組態設定的檔案：
    - **C#**：appsettings.json
    - **Python**：.env

    開啟組態檔並更新其所包含的組態值，以反映下列設定：
    
    - Azure AI 服務資源的**端點**
    - **Azure Key Vault** 資源的名稱
    - 服務主體的 **tenant**
    - 服務主體的 **appId**
    - 服務主體的 **password**

     按 **CTRL+S** 儲存變更。
4. 請注意，**keyvault-client** 資料夾包含用戶端應用程式的程式碼檔案：

    - **C#**：Program.cs
    - **Python**：keyvault-client.py

    開啟程式碼檔案並檢閱其所包含的程式碼，並注意下列詳細資料：
    - 已匯入您安裝的 SDK 命名空間
    - **Main** 函式中的程式碼會擷取應用程式組態設定，然後使用服務主體認證來取得金鑰保存庫中的 Azure AI 服務金鑰。
    - **GetLanguage** 函式會使用 SDK 來建立服務的用戶端，然後使用此用戶端來偵測所輸入文字的語言。
5. 輸入下列命令以執行程式：

    **C#**

    ```
    dotnet run
    ```

    **Python**

    ```
    python keyvault-client.py
    ```

6. 出現提示時，輸入一些文字並檢閱服務偵測到的語言。 例如，嘗試輸入 "Hello"、"Bonjour" 和 "Gracias"。
7. 當您完成應用程式的測試後，輸入 "quit" 可停止程式。

## 清除資源

如果您不將本實驗中所建立的 Azure 資源用於其他訓練模組，則可以將其刪除以避免產生進一步的費用。

1. 開啟 Azure 入口網站 (位於 `https://portal.azure.com`)，然後在頂端搜尋列中搜尋您在此實驗中所建立的資源。

2. 在資源頁面上，選取 [刪除]**** 並依照指示刪除該資源。 或者，您也可以刪除整個資源群組以同時清理所有資源。

## 其他相關資訊

如需保護 Azure AI 服務的詳細資訊，請參閱 [Azure AI 服務安全性文件](https://docs.microsoft.com/azure/ai-services/security-features)。
