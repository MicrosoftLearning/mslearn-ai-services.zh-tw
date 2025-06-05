---
lab:
  title: 使用 Azure AI 服務容器
  module: Module 2 - Developing AI Apps with Azure AI Services
---

# 使用 Azure AI 服務容器

使用 Azure 中裝載的 Azure AI 服務可讓應用程式開發人員專注於自己的程式碼基礎結構，同時也受益於由 Microsoft 管理的可調整服務。 然而，在眾多情況下，組織需要更充分掌控其服務基礎結構，以及服務之間傳遞的資料。

許多 Azure AI 服務 API 都可以進行封裝，並部署在*容器*中，讓組織能夠在自己的基礎結構中裝載 Azure AI 服務；例如，在本地 Docker 伺服器、Azure 容器執行個體或 Azure Kubernetes Services 叢集中。 容器化 Azure AI 服務需要與 Azure 型 Azure AI 服務帳戶進行通訊，以支援計費；但應用程式資料不會傳遞至後端服務，而且組織對容器的部署設定有更大的掌控權，可啟用用於驗證、可擴縮性及其他考量的自訂解決方案。

> **注意**：目前正在調查一個問題，某些使用者會叫用容器無法正確部署的位置，而對這些容器的呼叫會失敗。 此實驗室的更新會在問題解決後立即進行。

## 在 Visual Studio Code 中，複製您的存放庫。

您將使用 Visual Studio Code 開發您的程式碼。 您應用程式的程式碼檔案已在 GitHub 存放庫中提供。

> **秘訣**：如果您已複製 **mslearn-ai-language** 存放庫，請在 Visual Studio 程式碼中開啟它。 否則，請遵循下列步驟將其複製到您的開發環境。

1. 啟動 Visual Studio Code。
2. 開啟選擇區 (SHIFT+CTRL+P) 並執行 **Git：複製 ** 命令，將 `https://github.com/MicrosoftLearning/mslearn-ai-services` 存放庫複製到本機資料夾 (哪個資料夾無關緊要)。
3. 複製存放庫後，請在 Visual Studio Code 中開啟此資料夾。
4. 等候其他檔案安裝以在需要時支援存放庫中的 C# 程式碼專案

    > **注意**：如果系統提示您新增必要的資產來組建和偵錯，請選取 [現在不要]****。

5. 展開 `Labfiles/04-use-a-container` 資料夾。

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
5. 部署資源後，請移至該資源並檢視其 [金鑰和端點] **** 頁面。 在下一個程序中，您需要此頁面中的端點和其中一個金鑰。

## 部署並執行情感分析容器

容器映像中提供許多常用的 Azure AI 服務 API。 如需完整清單，請參閱 [Azure AI 服務文件](https://learn.microsoft.com/en-us/azure/ai-services/cognitive-services-container-support#containers-in-azure-ai-services)。 在此練習中，您將使用適用於文字分析*情感分析*API 的容器映射；但所有可用映射的準則都相同。

1. 在 Azure 入口網站的 [首頁] **** 頁面上，選取 [&#65291;建立資源] **** 按鈕、搜尋 [容器執行個體] **，並使用下列設定建立 [容器執行個體] **** 資源：

    - **基本**：
        - **訂用帳戶**：您的 Azure 訂用帳戶**
        - 資源群組：選擇包含您 Azure AI 服務資源的資源群組
        - **容器名稱**：*輸入唯一名稱*
        - **區域**：*選擇任何可用的區域*
        - **可用性區域**：無
        - **SKU**：標準
        - **映像來源**：其他登錄
        - **映像類型**：公用
        - **映像**：`mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment:latest`
        - **OS 類型**：Linux
        - **尺寸**：1 vCPU，8 GB 記憶體
    - **網路**：
        - **網路類型**：公用
        - **DNS 名稱標籤**：*為容器端點輸入唯一的名稱*
        - **連接埠**：*將 TCP 連接埠 從 80 變更為 **5000***
    - **進階：**
        - **重新啟動原則**：失敗時
        - **環境變數**：

            | 標示為安全 | 機碼 | 值 |
            | -------------- | --- | ----- |
            | Yes | `ApiKey` | *Azure AI 服務資源的任一金鑰* |
            | Yes | `Billing` | *Azure AI 服務資源的端點 URI* |
            | No | `Eula` | `accept` |

        - **命令覆寫**：[]
        - **金鑰管理**：Microsoft 管理的金鑰 (MMK)
    - **標籤：**
        - *請勿新增任何標籤*

2. 選取 [檢閱 + 建立]****,然後選取 [建立]****。 等候部署完成，然後移至資源。
    > **注意** 請注意，將 Azure AI 容器部署至 Azure 容器執行個體通常需要 5-10 分鐘 (佈建)，才能準備好使用。
3. 在其 [概觀] **** 頁面上觀察容器執行個體資源的下列屬性：
    - **狀態**：這個應該在*運行中*。
    - **IP 位址**：此為您可以用於存取您容器執行個體的公開 IP 位址。
    - **FQDN**：這是容器執行個體資源的*完整功能變數名稱*，您可以使用其來存取容器執行個體，而不是 IP 位址。

    > **注意**：在此練習中，您已將 Azure AI 服務容器映像部署為情感分析至 Azure 容器執行個體 (ACI) 資源。 您可以使用類似的方法將其部署到您自己的電腦或網路上的 *[Docker](https://www.docker.com/products/docker-desktop)* 主機，方法是在單一行上執行下列命令，將情感分析容器部署到本機 Docker 執行個體，並將 *&lt;yourEndpoint&gt;* 和 *&lt;yourKey&gt;* 取代為端點 URI，以及 Azure AI 服務資源的其中一個金鑰。
    > 該命令會在本機電腦上尋找映射，如果其找不到該映射，則會從 *mcr.microsoft.com* 映射登錄提取映射，並將其部署至 Docker 執行個體。 部署完成時，容器會啟動並接聽連接埠 5000 上的傳入要求。

    ```
    docker run --rm -it -p 5000:5000 --memory 8g --cpus 1 mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment:latest Eula=accept Billing=<yourEndpoint> ApiKey=<yourKey>
    ```

## 使用該容器

1. 在您的編輯器中，開啟 **rest-test.cmd**，並編輯它包含的 **curl** 命令 (如下所示)，以容器的 IP 位址或 FQDN 取代 *&lt;your_ACI_IP_address_or_FQDN&gt;*。

    ```
    curl -X POST "http://<your_ACI_IP_address_or_FQDN>:5000/text/analytics/v3.1/sentiment" -H "Content-Type: application/json" --data-ascii "{'documents':[{'id':1,'text':'The performance was amazing! The sound could have been clearer.'},{'id':2,'text':'The food and service were unacceptable. While the host was nice, the waiter was rude and food was cold.'}]}"
    ```

2. 按 **[Ctrl+S]** 儲存指令碼變更。 請注意，您不需要指定 Azure AI 服務端點或金鑰 - 要求是由容器化服務進行處理。 該容器接著會定期與 Azure 中的服務通訊，以報告計費使用量，但不會傳送要求資料。
3. 輸入下列命令以執行此指令碼：

    ```
    ./rest-test.cmd
    ```

4. 驗證該命令會傳回 JSON 文件，其中包含在兩個輸入資料中偵測到的情感相關資訊 (按順序應該是積極和消極的)。

## 疑難排解

> **注意**：  
> 將位於 Azure 入口網站上的容器視為 [執行中] **並不**一定代表已準備好要處理要求。 如果您嘗試使用 `curl`，就會得到以下這類錯誤碼：
>
> ```text
> curl: (52) Empty reply from server
> curl: (56) Recv failure: Connection reset by peer
> ```
>
> 這可能表示未能以正確的參數或環境來啟動容器，或者可能遺失模型檔案，或是模型下載的網際網路存取。

### 替代方案：使用 Azure CLI 來部署容器

您可以嘗試下列命令（使用您的數值來取代`rg-name`、`container-name`、`ApiKey` 和 `Billing`端點）：

```bash
az container create \
  --resource-group <rg-name> \
  --name <container-name> \
  --image mcr.microsoft.com/azure-cognitive-services/sentiment \
  --ports 5000 \
  --dns-name-label ai102sentimentdemo \
  --environment-variables Eula=accept \
  --secure-environment-variables ApiKey=<your_api_key> Billing=<your_billing_endpoint> \
  --cpu 2 --memory 4 \
  --os-type Linux
```
部署之後，您可以測試端點（會視需要取代<ACI_IP>）：
```bash
curl -X POST "http://<ACI_IP>:5000/text/analytics/v3.0/sentiment?model-version=latest" \
-H "Content-Type: application/json" \
-d '{
  "documents": [
    {
      "id": "1-en",
      "language": "en",
      "text": "The performance was amazing! The sound could have been clearer."
    },
    {
      "id": "2-en",
      "language": "en",
      "text": "The food and service were unacceptable. While the host was nice, the waiter was rude and food was cold."
    }
  ]
}'
```
**歡迎試用**：

    If v3.1 endpoints do not work, try using v3.0 or v3.1-preview.1 with above cmd.

    Always check the /status endpoint and container logs for model loading messages (look for: Model loaded from /input/TextAnalytics/v3.x/Sentiment).

    If you still encounter issues, running the container locally with Docker may help to diagnose local vs. cloud issues.

## 清除

如果您已完成容器執行個體的實驗，您應該將其刪除。

1. 在 Azure 入口網站中，開啟您為此練習建立資源的資源群組。
2. 選取容器執行個體資源並加以刪除。

## 清除資源

如果您不將本實驗中所建立的 Azure 資源用於其他訓練模組，則可以將其刪除以避免產生進一步的費用。

1. 開啟 Azure 入口網站 (位於 `https://portal.azure.com`)，然後在頂端搜尋列中搜尋您在此實驗中所建立的資源。

2. 在資源頁面上，選取 [刪除]**** 並依照指示刪除該資源。 或者，您也可以刪除整個資源群組以同時清理所有資源。

## 其他相關資訊

如需容器化 Azure AI 服務的詳細資訊，請參閱[Azure AI 服務容器文件](https://learn.microsoft.com/azure/ai-services/cognitive-services-container-support)。
