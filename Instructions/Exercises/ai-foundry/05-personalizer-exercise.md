---
lab:
  title: 使用 AI 個人化服務工具搭配 Visual Studio Code Notebook 來模擬迴圈
---
> **重要** 自 2023 年秋季起，無法再建立新的「個人化服務工具」資源。 只在您有現有的「個人化服務工具」資源時才使用此存放庫作為參考。

在本練習中，您將使用「Azure AI 個人化服務工具」搭配 Visual Studio Code 中的筆記本來模擬學習迴圈。

## 使用 Azure 入口網站建立「Azure AI 個人化服務工具」資源

1. 在 Azure 入口網站中，搜尋 **Azure AI 服務**。 然後在結果清單中的 [個人化服務工具]**** 底下選取 [建立]****。

   ![Azure 入口網站的螢幕擷取畫面，其中顯示如何建立「Azure AI 個人化服務工具」資源](../media/ai-personalizer/create-personalizer-portal.png)

1. 選取您的訂用帳戶、輸入資源資源群組，以及您的資源的名稱。 對於定價層，請選擇 [免費 F0]****。
1. 選取 [檢閱 + 建立]**** 以檢閱您的選擇，然後選取 [建立]**** 以建立您的資源。
1. 移至您新建立的「Azure AI 個人化服務工具」資源，然後在 [金鑰和端點] 窗格中，複製**金鑰**和**端點**並貼到安全的位置以供稍後使用：

   ![顯示 [金鑰和端點] 窗格的螢幕擷取畫面。](../media/ai-personalizer/copy-key-endpoint.png)

1. 在瀏覽窗格中選取 [設定]，然後將 [奬勵等待時間]**** 設定為 **10 分鐘** (若尚未設定的話)，並將 [模型更新頻率]**** 設定為 **15 秒**。
1. 選取 [儲存]。

## 設定筆記本

1. 在 Visual Studio Code 編輯器中，按 **Ctrl+Shift+P** 並選取 **Create:新 Jupyter Notebook**。
1. 將該檔案儲存在您的裝置上並將其命名為 **my-notebook**。
1. 現在您需要安裝必要的延伸模組。 在筆記本右上方中選取 [選取核心]****。 然後選取 [安裝/啟用建議的延伸模組]****。

   ![顯示如何安裝延伸模組的螢幕擷取畫面。](../media/ai-personalizer/8-install-enable-extensions-code.png)

   > [!NOTE]
   > 如果您之前已經執行過此動作，則不會看到此選項，可以跳過此步驟。

1. 等待延伸模組安裝完畢，然後在出現的下拉式清單中選取 [Python 環境...]****。
1. 接著選取最上面建議的環境。

## 準備資料

為了本練習的目的，我們將建立一個使用者清單和一個咖啡產品清單，我們可以將其用作「Azure AI 個人化服務工具」的基礎資料。

1. 將以下 JSON 程式碼複製到一個空白檔案中，並將該檔案命名為 `users.json` 儲存在與您的筆記本檔案相同的資料夾中。

   ```json
   {
     "Alice": {
       "Sunny": {
         "Morning": "Cold brew",
         "Afternoon": "Iced mocha",
         "Evening": "Cold brew"
       },
       "Rainy": {
         "Morning": "Latte",
         "Afternoon": "Cappucino",
         "Evening": "Latte"
       },
       "Snowy": {
         "Morning": "Cappucino",
         "Afternoon": "Cappucino",
         "Evening": "Cappucino"
       }
     },
     "Bob": {
       "Sunny": {
         "Morning": "Cappucino",
         "Afternoon": "Iced mocha",
         "Evening": "Cold brew"
       },
       "Rainy": {
         "Morning": "Latte",
         "Afternoon": "Latte",
         "Evening": "Latte"
       },
       "Snowy": {
         "Morning": "Iced mocha",
         "Afternoon": "Iced mocha",
         "Evening": "Iced mocha"
       }
     },
     "Cathy": {
       "Sunny": {
         "Morning": "Latte",
         "Afternoon": "Cold brew",
         "Evening": "Cappucino"
       },
       "Rainy": {
         "Morning": "Cappucino",
         "Afternoon": "Latte",
         "Evening": "Iced mocha"
       },
       "Snowy": {
         "Morning": "Cold brew",
         "Afternoon": "Iced mocha",
         "Evening": "Cappucino"
       }
     },
     "Dave": {
       "Sunny": {
         "Morning": "Iced mocha",
         "Afternoon": "Iced mocha",
         "Evening": "Iced mocha"
       },
       "Rainy": {
         "Morning": "Latte",
         "Afternoon": "Latte",
         "Evening": "Latte"
       },
       "Snowy": {
         "Morning": "Cappucino",
         "Afternoon": "Cappucino",
         "Evening": "Cappucino"
       }
     }
   }
   ```

1. 接下來，複製以下程式碼並將其儲存到名為 `coffee.json` 的檔案中：

   ```json
   [
     {
       "id": "Cappucino",
       "features": [
         {
           "type": "hot",
           "origin": "kenya",
           "organic": "yes",
           "roast": "dark"
         }
       ]
     },
     {
       "id": "Cold brew",
       "features": [
         {
           "type": "cold",
           "origin": "brazil",
           "organic": "yes",
           "roast": "light"
         }
       ]
     },
     {
       "id": "Iced mocha",
       "features": [
         {
           "type": "cold",
           "origin": "ethiopia",
           "organic": "no",
           "roast": "light"
         }
       ]
     },
     {
       "id": "Latte",
       "features": [
         {
           "type": "hot",
           "origin": "brazil",
           "organic": "no",
           "roast": "dark"
         }
       ]
     }
   ]
   ```

1. 將以下程式碼複製並貼上到一個檔案中，並將其儲存為 `example-rankrequest.json`：

   ```json
   {
     "contextFeatures": [],
     "actions": [],
     "excludedActions": [],
     "eventId": "",
     "deferActivation": false
   }
   ```

## 設定您的端點和金鑰

1. 在您的筆記本頂部，新增以下程式碼以包含所需的模組：

   ```python
   import json
   import matplotlib.pyplot as plt
   import random
   import requests
   import time
   import uuid
   import datetime
   ```

1. 選取儲存格，然後選取儲存格左邊的 [執行] 按鈕：

   ![顯示 [執行] 按鈕的螢幕擷取畫面。](../media/ai-personalizer/8-press-run.png)

   > [!NOTE]
   > 確保每次將資料填入新儲存格時都選取 [執行] 按鈕。 如果系統提示您安裝 ipykernel 套件，請選取 [安裝]****。

1. 選取您的筆記本頂部的 [+ 程式碼]**** 以建立新的程式碼儲存格。 然後，新增下列程式碼：

   ```python
   # Replace 'personalization_base_url' and 'resource_key' with your valid endpoint values.
   personalization_base_url = "<your-endpoint>"
   resource_key = "<your-resource-key>"
   ```

1. 將 **personalization_base_url** 值取代為您複製的端點，並將 **resource_key** 值取代為您的金鑰。

## 追蹤迭代

1. 接下來，您建立的程式碼有助於記錄稍後您將使用的迭代函式的開始和結束時間。 將以下程式碼新增至新的儲存格中：

   ```python
   # Print out current datetime
   def currentDateTime():
       currentDT = datetime.datetime.now()
       print (str(currentDT))

   # ititialize variable for model's last modified date
   modelLastModified = ""

   def get_last_updated(currentModifiedDate):

       print('-----checking model')

       # get model properties
       response = requests.get(personalization_model_properties_url, headers = headers, params = None)

       print(response)
       print(response.json())

       # get lastModifiedTime
       lastModifiedTime = json.dumps(response.json()["lastModifiedTime"])

       if (currentModifiedDate != lastModifiedTime):
           currentModifiedDate = lastModifiedTime
           print(f'-----model updated: {lastModifiedTime}')
   ```

1. 新增程式碼之後，請勿忘記執行新的儲存格。

## 取得原則和服務設定

1. 接下來，您需要透過取得原則和服務組態來驗證服務狀態。 若要這樣做，請將以下程式碼新增至新的儲存格中：

   ```python
   def get_service_settings():

       print('-----checking service settings')

       # get learning policy
       response = requests.get(personalization_model_policy_url, headers = headers, params = None)

       print(response)
       print(response.json())

       # get service settings
       response = requests.get(personalization_service_configuration_url, headers = headers, params = None)

       print(response)
       print(response.json())
   ```

1. 請確保執行新的程式碼儲存格。

該程式碼是由一個對該服務 API 進行兩次呼叫的函式所組成。 呼叫該函式時，它會使用回應傳回服務值。

## 設定呼叫的 URL 並讀取 JSON 檔案

現在您將新增程式碼以：

- 建置 REST 呼叫中使用的 URL
- 使用您的「個人化服務工具」資源金鑰設定安全性標頭
- 設定排名 (Rank) 事件識別碼的隨機種子
- 讀入 JSON 資料檔
- 呼叫 get_last_updated 方法 - 學習原則已在範例輸出中移除
- 呼叫 get_service_settings 方法

1. 若要這樣做，請將以下程式碼新增至新的儲存格並加以執行：

   ```python
   # build URLs
   personalization_rank_url = personalization_base_url + "personalizer/v1.0/rank"
   personalization_reward_url = personalization_base_url + "personalizer/v1.0/events/" #add "{eventId}/reward"
   personalization_model_properties_url = personalization_base_url + "personalizer/v1.0/model/properties"
   personalization_model_policy_url = personalization_base_url + "personalizer/v1.0/configurations/policy"
   personalization_service_configuration_url = personalization_base_url + "personalizer/v1.0/configurations/service"

   headers = {'Ocp-Apim-Subscription-Key' : resource_key, 'Content-Type': 'application/json'}

   # context
   users = "users.json"

   # action features
   coffee = "coffee.json"

   # empty JSON for Rank request
   requestpath = "example-rankrequest.json"

   # initialize random
   random.seed(time.time())

   userpref = None
   rankactionsjsonobj = None
   actionfeaturesobj = None

   with open(users) as handle:
       userpref = json.loads(handle.read())

   with open(coffee) as handle:
       actionfeaturesobj = json.loads(handle.read())

   with open(requestpath) as handle:
       rankactionsjsonobj = json.loads(handle.read())

   get_last_updated(modelLastModified)
   get_service_settings()

   print(f'User count {len(userpref)}')
   print(f'Coffee count {len(actionfeaturesobj)}')
   ```

1. 該呼叫應傳回類似以下內容的回應：

   ```bash
   -----checking model
   <Response [200]>
   {'creationTime': '2023-09-22T14:58:45+00:00', 'lastModifiedTime': '2023-09-22T14:58:45+00:00'}
   -----model updated: "2023-09-22T14:58:45+00:00"
   -----checking service settings
   <Response [200]>
   {'name': '917554355a3347a1af3d2935d521426a', 'arguments': '--cb_explore_adf --epsilon 0.20000000298023224 --power_t 0 -l 0.001 --cb_type mtr -q ::'}
   <Response [200]>
   {'rewardWaitTime': 'PT10M', 'defaultReward': 0.0, 'rewardAggregation': 'earliest', 'explorationPercentage': 0.2, 'modelExportFrequency': 'PT15S', 'logRetentionDays': 90, 'lastConfigurationEditDate': '2021-01-01T00:00:00', 'learningMode': 'Online'}
   User count 4
   Coffee count 4
   ```

1. 回應碼應為 `<Response [200]>` 以指示呼叫成功。 **rewardWaitTime** 應顯示為 10 分鐘，而 **modelExportFrequency** 應顯示為 15 秒。

## 在 Azure 入口網站中設定圖表

您的程式碼會向 API 發出要求。 若要取得符合您的要求的良好計量，您可以在 Azure 入口網站中建立計量圖表：

1. 在 Azure 入口網站中，移至您的「Azure AI 個人化服務工具」資源。

1. 在瀏覽窗格中的 [監視] 底下，選取 [計量]****。

   ![計量圖表的螢幕擷取畫面。](../media/ai-personalizer/8-create-metric-chart.png)

1. **範圍**和**計量**命名空間已為您設定。 您只需選取**成功呼叫**的**計量**以及**總和**的**彙總**。

1. 將時間篩選條件變更為過去四個小時。

## 建立唯一的事件識別碼

接下來，您將新增程式碼，以為每個排名 API 呼叫產生唯一的識別碼。 您可以使用此識別碼來識別要求的排名和獎勵呼叫資訊。

1. 若要這樣做，請在您的筆記本中建立一個新的程式碼儲存格，並新增以下內容：

   ```python
   def add_event_id(rankjsonobj):
       eventid = uuid.uuid4().hex
       rankjsonobj["eventId"] = eventid
       return eventid
   ```

1. 請記得執行新的程式碼儲存格。

> [!NOTE]
> 在真實的場景中，您應將這個設為類似於購買交易的識別碼。

## 取得使用者、一天中的時間和天氣

現在您可以新增一個函式以：

- 選取唯一的使用者、一天中的時間和天氣組合。
- 將這些選定的項目新增至 JSON 物件中，該物件將透過呼叫被傳送至排名 API。

若要這樣做，請將以下程式碼新增至新的儲存格並加以執行：

```python
def add_random_user_and_contextfeatures(namesoption, weatheropt, timeofdayopt, rankjsonobj):
    name = namesoption[random.randint(0,3)]
    weather = weatheropt[random.randint(0,2)]
    timeofday = timeofdayopt[random.randint(0,2)]
    rankjsonobj['contextFeatures'] = [{'timeofday': timeofday, 'weather': weather, 'name': name}]
    return [name, weather, timeofday]
```

## 新增咖啡資料

接下來，您將建立一個函式來將整個咖啡產品清單傳到 JSON 物件中，然後將其傳送至排名 API。

若要這樣做，請將以下程式碼新增至新的儲存格並加以執行：

```python
def add_action_features(rankjsonobj):
    rankjsonobj["actions"] = actionfeaturesobj
```

## 將預測與已知的使用者偏好進行比較

然後，您可以建立一個函式來比較使用者對特定咖啡的偏好，並考慮天氣和一天中的時間等詳細資料，以及「Azure AI 個人化服務工具」的建議內容。

1. 若要這樣做，請建立一個新的儲存格、新增以下程式碼，然後執行它：

   ```python
   def get_reward_from_simulated_data(name, weather, timeofday, prediction):
       if(userpref[name][weather][timeofday] == str(prediction)):
           return 1
       return 0
   ```

1. 此函式旨在每次呼叫排名 API 之後執行。 如果建議相符，則會在回應中傳回 `1` 的分數。 如果不相符，則會傳回 `0`。

## 建立一個呼叫排名和獎勵 API 的迴圈

前面的儲存格會用來設定您的筆記本以進行迴圈作業。 您現在將設定您的迴圈。 該迴圈涵蓋了您的筆記本中的主要工作內容。 它會取得一個隨機使用者、取得咖啡清單，然後將這兩項傳送至排名 API。 它會將「Azure AI 個人化服務工具」中的預測與該使用者的已知偏好進行比較，然後將獎勵再次傳回到「Azure AI 個人化服務工具」。

若要建立您的迴圈，請將以下程式碼新增至新的儲存格並加以執行：

```python
def iterations(n, modelCheck, jsonFormat):

    i = 1

    # default reward value - assumes failed prediction
    reward = 0

    # Print out dateTime
    currentDateTime()

    # collect results to aggregate in graph
    total = 0
    rewards = []
    count = []

    # default list of user, weather, time of day
    namesopt = ['Alice', 'Bob', 'Cathy', 'Dave']
    weatheropt = ['Sunny', 'Rainy', 'Snowy']
    timeofdayopt = ['Morning', 'Afternoon', 'Evening']


    while(i <= n):

        # create unique id to associate with an event
        eventid = add_event_id(jsonFormat)

        # generate a random sample
        [name, weather, timeofday] = add_random_user_and_contextfeatures(namesopt, weatheropt, timeofdayopt, jsonFormat)

        # add action features to rank
        add_action_features(jsonFormat)

        # show JSON to send to Rank
        print('To: ', jsonFormat)

        # choose an action - get prediction from Personalizer
        response = requests.post(personalization_rank_url, headers = headers, params = None, json = jsonFormat)

        # show Rank prediction
        print ('From: ',response.json())

        # compare personalization service recommendation with the simulated data to generate a reward value
        prediction = json.dumps(response.json()["rewardActionId"]).replace('"','')
        reward = get_reward_from_simulated_data(name, weather, timeofday, prediction)*10

        # show result for iteration
        print(f'   {i} {currentDateTime()} {name} {weather} {timeofday} {prediction} {reward}')

        # send the reward to the service
        response = requests.post(personalization_reward_url + eventid + "/reward", headers = headers, params= None, json = { "value" : reward })

        # for every N rank requests, compute total correct  total
        total =  total + reward

        # every N iteration, get last updated model date and time
        if(i % modelCheck == 0):

            print("**** 10% of loop found")

            get_last_updated(modelLastModified)

        # aggregate so chart is easier to read
        if(i % 10 == 0):
            rewards.append( total)
            count.append(i)
            total = 0

        i = i + 1

    # Print out dateTime
    currentDateTime()

    return [count, rewards]
```

以下是該函式傳送至排名 API 的 JSON 結構範例：

```python
{
    'contextFeatures':[
      {
          'timeofday':'Evening',
          'weather':'Snowy',
          'name':'Alice'
      }
    ],
    'actions':[
      {
          'id':'Cappucino',
          'features':[
            {
                'type':'hot',
                'origin':'kenya',
                'organic':'yes',
                'roast':'dark'
            }
          ]
      }
        ...rest of the coffee list
    ],
    'excludedActions':[

    ],
    'eventId':'b5c4ef3e8c434f358382b04be8963f62',
    'deferActivation':False
}
```

排名 API 會使用如下結構的回應進行回應：

```python
{
    'ranking': [
        {'id': 'Latte', 'probability': 0.85 },
        {'id': 'Iced mocha', 'probability': 0.05 },
        {'id': 'Cappucino', 'probability': 0.05 },
        {'id': 'Cold brew', 'probability': 0.05 }
    ],
    'eventId': '5001bcfe3bb542a1a238e6d18d57f2d2',
    'rewardActionId': 'Latte'
}
```

每個迴圈迭代都會顯示隨機選取的使用者、天氣和一天中的時間，以及適當確定的獎勵：

```bash
1 Alice Rainy Morning Latte 1
```

`1` 的獎勵表示您的「Azure AI 個人化服務工具」資源已針對使用者、天氣和一天中時間的這個特定組合選取了正確的咖啡類型。

## 執行迴圈並檢視表結果

「Azure AI 個人化服務工具」需要對排名 API 和獎勵 API 進行數千次呼叫才能建立模型。 您將針對設定的迭代次數執行您的迴圈。

1. 若要這樣做，請建立一個新的儲存格、新增以下程式碼，然後執行它：

   ```python
   # max iterations
   num_requests = 150

   # check last mod date N% of time - currently 10%
   lastModCheck = int(num_requests * .10)

   jsonTemplate = rankactionsjsonobj

   # main iterations
   [count, rewards] = iterations(num_requests, lastModCheck, jsonTemplate)
   ```

1. 在 Azure 入口網站中，每隔一段時間重新整理您的計量圖表，以查看該服務的總呼叫次數。
1. 此事件可能會執行一段時間。 在您的筆記本完成之前，請勿關閉它。 當該迴圈進行大約 20,000 次呼叫 (該迴圈的每次迭代都會進行一次排名和獎勵呼叫) 時，該迴圈將完成。

1. 接下來，您將在筆記本中建立一個圖表來繪製排名事件的批次，以及針對每個批次提出了多少個正確的建議。 若要這樣做，請在新的儲存格中新增以下程式碼並加以執行：

   ```python
   def createChart(x, y):
       plt.plot(x, y)
       plt.xlabel("Batch of rank events")
       plt.ylabel("Correct recommendations per batch")
       plt.show()

   createChart(count,rewards)
   ```

1. 您的筆記本將建立一個圖表：

   ![圖表的螢幕擷取畫面。](../media/ai-personalizer/7-chart2.png)

> **提示：** 理想情況下，測試完成後，您的迴圈平均應以 100% 減去探索值 (預設為 20%) 的比率提出正確的建議數，所以此處您的目標比率為 80%。 實現這一目標的方法是將迭代次數增加到至少 10,000 次。

此圖表顯示了根據預設的學習原則，您的模型的成功率。 此圖表顯示該學習原則可以改進。 您可以在執行評估之後變更該原則來實現此目的。

## 執行離線評估

> [!NOTE]
> 本練習中的這一個章節是選擇性的，因為它只能在您對您的「Azure AI 個人化服務工具」資源進行至少 50,000 次呼叫之後執行。 但是，如果在某個時刻您對您的資源的呼叫達到了 50,000 次，您可以回到這一個章節。

您可以執行離線評估，為至少有 50,000 次呼叫的「Azure AI 個人化服務工具」資源找到更好的學習原則。

1. 在 Azure 入口網站中，移至您的「Azure AI 個人化服務工具」資源的 [最佳化] 窗格，然後選取 [建立評估]****。
1. 提供評估名稱，然後為您的迴圈評估選取開始和結束日期範圍。 您的日期範圍應僅包括您關注評估的那幾天：

   ![顯示評估表單的螢幕擷取畫面。](../media/ai-personalizer/7-evaluation-form.png)

1. 選取 [開始評估]**** 以開始您的評估。

1. 評估完成後，請從 [最佳化] 窗格上的評估清單中選取它。 然後，根據學習原則的平均獎勵、信賴區間等詳細資料來檢閱您的學習原則的效能：

   ![顯示評估結果的螢幕擷取畫面。](../media/ai-personalizer/7-offline-eval-result.png)

1. 您會看到幾個原則，包括:

   - **[Online]** - Azure AI 個人化服務工具目前的原則。
   - **[Baseline1]** - 應用程式的基準原則
   - **[BaselineRand]** - 隨機採取動作的原則。
   - **[Inter-len#]** 或 **[Hyper#]** - 最佳化探索所建立的原則。

1. 選取最能改進模型的原則上的 [套用]****。

## 清除資源

如果您不將本實驗中所建立的 Azure 資源用於其他訓練模組，則可以將其刪除以避免產生進一步的費用。

1. 開啟 Azure 入口網站 (位於 `https://portal.azure.com`)，然後在頂端搜尋列中搜尋您在此實驗中所建立的資源。

2. 在資源頁面上，選取 [刪除]**** 並依照指示刪除該資源。 或者，您也可以刪除整個資源群組以同時清理所有資源。
