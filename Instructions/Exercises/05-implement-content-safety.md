---
lab:
  title: 關於 Azure AI 內容安全
---

# 關於 Azure AI 內容安全

在此練習中，您將佈建 Content Safety 資源、在 Azure AI Studio 中測試資源，以及在程式碼中測試資源。

## 佈建*內容安全*資源

如果您還沒有，則需要在 Azure 訂用帳戶中佈建**內容安全**資源。

1. 開啟 Azure 入口網站 (位於 `https://portal.azure.com`)，使用與您的 Azure 訂用帳戶相關聯的 Microsoft 帳戶進行登入。
1. 選取 **[建立資源]**。
1. 在搜尋欄位中，搜尋**內容安全**。 然後，在結果中，選取 **[Azure AI 內容安全]** 底下的 **[建立]**。
1. 使用下列設定佈建資源：
    - **訂用帳戶**：*您的 Azure 訂用帳戶*。
    - **資源群組**：*選擇或建立資源群組*。
    - **區域**：選取 **[美國東部]**。
    - **名稱**：*輸入唯一名稱*。
    - **定價層**：如果 F0 無法使用，請選取 **F0** (*免費*)，或 **S** (*標準*)。
1. 選取 **[檢閱 + 建立]，** 然後選取 **[建立]**，以佈建資源。
1. 等候部署完成，然後移至資源。
1. 選取左側瀏覽列中的 **[存取控制]**，然後選取 **[+ 新增]** 和 **[新增角色指派]**。
1. 向下捲動並選擇 **[認知服務使用者]** 角色，然後選取 **[下一步]**。
1. 將您的帳戶新增至此角色，然後選取 **[檢閱 + 指派]**。
1. 在左側瀏覽列中選取 **[資源管理]**，然後選取 **[金鑰與端點]**。 讓此頁面保持開啟狀態，以便稍後複製金鑰。

## 使用 Azure AI 內容安全提示盾

在本練習中，您將使用 Azure AI Studio 測試內容安全提示盾，並搭配兩個範例輸入。 其中一個會模擬使用者提示，另一個模擬內嵌有潛在不安全文字的文件。

1. 在另一個瀏覽器索引標籤中，開啟 [Azure AI Studio](https://ai.azure.com/explore/contentsafety) 的 [內容安全] 頁面並登入。
1. 在 **[仲裁文字內容]** 下選取 **[試用]**。
1. 在 **[仲裁文字內容]** 頁面上，於 **[Azure AI 服務]** 下選取您稍早建立的內容安全資源。
1. 選取 **[一個句子中的多個風險類別]**。 檢閱文件文字中是否有潛在問題。
1. 選取 **[執行測試]** 並檢閱結果。
1. 您可以選擇性地改變閾值層級，然後再次選取 **[執行測試]**。
1. 在左側導覽列上，選取 **[文字的受(版權)保護素材偵測]**。
1. 選取 **[受保護的歌詞]**，並注意這些是已發行歌曲的歌詞。
1. 選取 **[執行測試]** 並檢閱結果。
1. 在左側導覽列上，選取 **[仲裁影像內容]**。
1. 選取 **[自我傷害內容]**。
1. 請注意，根據預設，AI Studio 中的所有影像都為模糊狀態。 您也應該知道範例中的性內容非常溫和。
1. 選取 **[執行測試]** 並檢閱結果。
1. 在左側導覽列上，選取 **[提示盾]**。
1. 在** [提示盾] 頁面上**，於 **[Azure AI 服務]** 下選取您稍早建立的內容安全資源。
1. 選取 **[提示和文件攻擊內容]**。 檢閱使用者提示和文件文字中是否有潛在問題。
1. 選取 [執行測試]****。
1. 在 **[檢視結果]** 中，確認已在使用者提示和文件中偵測到越獄攻擊。

    > [!TIP]
    > 程式代碼適用於 AI Studio 中的所有範例。

1. 在 **[後續步驟]** 下的 **[檢視程式碼]** 下，選取 [**[檢視程式碼]**。 **[範例程式碼]** 視窗隨即顯示。
1. 使用向下箭號選取 Python 或 C#，然後選取 **[複製]** 將範例程式碼複製到剪貼簿。
1. 關閉 **[範例程式碼]** 畫面。

### 設定您的應用程式

您現在將在 C# 或 Python 中建立應用程式。

#### C#

##### 必要條件

* 其中一個[支援平台](https://code.visualstudio.com/docs/supporting/requirements#_platforms)上的 [Visual Studio Code](https://code.visualstudio.com/)。
* [.NET 8](https://dotnet.microsoft.com/en-us/download/dotnet/8.0) 是此練習的目標 Framework。
* 適用於 Visual Studio Code 的 [C# 延伸模組](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)。

##### 設定

執行下列步驟來準備 Visual Studio Code 以進行練習。

1. 啟動 Visual Studio Code，然後在 [總管] 檢視中，按一下 [建立 .NET 專案]**** 並選取 [主控台應用程式]****。
1. 選取電腦上的資料夾，並指定專案名稱。 選取 **[建立專案]**，並確認警告訊息。
1. 在 [總管] 窗格中，展開 [方案總管]，然後選取 [Program.cs]****。
1. 選取 **[執行]** -> **[執行而不**偵錯] 來建置並執行專案。 
1. 在 [方案總管] 中，以滑鼠右鍵按一下 C# 專案，然後選取 **[新增 NuGet 套件]**。
1. 搜尋 **Azure.AI.TextAnalytics**，然後選取最新版本。
1. 搜尋第二個 NuGet 套件：**Microsoft.Extensions.Configuration.Json 8.0.0**。 專案檔現在應該會列出兩個 NuGet 套件。

##### 新增程式碼

1. 貼上您稍早在 **ItemGroup** 區段下複製的範例程式碼。
1. 向下捲動以尋找 *[以您自己的訂用帳戶 _key 和端點取代]*。
1. 在 Azure 入口網站的 [金鑰和端點] 頁面上，複製其中一個 [金鑰]（1 或 2）。 以此值取代 **<your_subscription_key>**。
1. 在 Azure 入口網站的 [金鑰和端點] 頁面上，複製 [端點]。 將此值貼到您的程式碼中，以取代 **<your_endpoint_value>**。
1. 在 **Azure AI Studio** 中，複製 **[使用者提示]** 值。 將此內容貼到您的程式碼中，以取代 **<test_user_prompt>**。
1. 向下捲動至 **<this_is_a_document_source>** 並刪除這一行程式碼。
1. 在 **Azure AI Studio** 中，複製 **[文件]** 值。
1. 向下捲動至 **<this_is_another_document_source>** 並貼上您的文件值。
1. 選取 **[執行]** -> **[執行而不偵錯**]，並確認偵測到攻擊。 

#### Python

##### 必要條件

* 其中一個[支援平台](https://code.visualstudio.com/docs/supporting/requirements#_platforms)上的 [Visual Studio Code](https://code.visualstudio.com/)。

* 已安裝適用於 Visual Studio Code 的 [Python 延伸模組](https://marketplace.visualstudio.com/items?itemName=ms-python.python)。

* 已安裝[要求模組](https://pypi.org/project/requests/)。

1. 建立具有 **.py** 副檔名的新 Python 檔案，並為其指定適當的名稱。
1. 貼上您稍早複製的範例程式碼。
1. 向下捲動以尋找標題為 *[以您自己的訂用帳戶 _key 和端點取代]* 的區段。
1. 在 Azure 入口網站的 [金鑰和端點] 頁面上，複製其中一個 [金鑰]（1 或 2）。 以此值取代 **<your_subscription_key>**。
1. 在 Azure 入口網站的 [金鑰和端點] 頁面上，複製 [端點]。 將此值貼到您的程式碼中，以取代 **<your_endpoint_value>**。
1. 在 **Azure AI Studio** 中，複製 **[使用者提示]** 值。 將此內容貼到您的程式碼中，以取代 **<test_user_prompt>**。
1. 向下捲動至 **<this_is_a_document_source>** 並刪除這一行程式碼。
1. 在 **Azure AI Studio** 中，複製 **[文件]** 值。
1. 向下捲動至 **<this_is_another_document_source>** 並貼上您的文件值。
1. 從檔案的整合式終端執行程式，例如：

    - `.\prompt-shield.py`

1. 驗證偵測到攻擊。
1. 您可以選擇性地試驗不同的測試內容和文件值。
