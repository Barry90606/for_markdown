API 規格書
===

文件版本修正紀錄表
---
| 版號 | 編號 | 日期 | 修改者 | 項目 | 內容 |
| :-- | :-- | :-- | :-- | :-- | :--|
| v1.0  | 1 | 2026/5/24 | Barry | 新增 | add login         |
| v1.1  | 2 | 2026/5/25 | Barry | 新增 | add student data  |
| v1.2  | 3 | 2026/5/25 | Paul  | 新增 | add course detail |

---


01-v1.0 · 登入 API  
---

### 使用對象
- 頁面上的 login

### 規格
- API：`/api/login`
- HTTP Method：POST
- 呼叫參數

| 參數名稱        | 必填 | 資料類型 | 說明 |
| :-------------- | :--: | :------ | :--- |
| StudentNumber   | V    | String  | 帳號，也是學生的學號 |
| StudentPassword | V    | String  | 密碼 |

註:兩個欄位皆為必填

- 範例
    ```
    {
        "StudentNumber":"111703888",
        "StudentPassword":"test01"
    }
    ```  

- 回傳參數

| 參數名稱    | 類型          | 說明                          | 範例 |
| :---------- | :----------- | :---------------------------- | :-- |
| StatusCode  | Int          | API執行狀態代碼                | 200 |
| Message     | String       | API執行狀態說明                | success |
| Data        | object       | 回傳資料以及判斷是哪位學生的參數 | "Token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.xxx.yyy","StudentNumber": "111703888","Name": "王小明" |

- 範例
    ```
    {
        "StatusCode":200,
        "Message":"success",
        "Data": {
            "Token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.xxx.yyy",
            "StudentNumber": "111703888",
            "Name": "王小明"
        }
    }
    ```

- 狀態說明

| 狀態碼 | 狀態描述 |
| :----- | :------ |
| 200    | 取得資料成功 |
| 400    | 參數錯誤（缺少帳號或密碼） |
| 401    | 帳號或密碼錯誤 |


---

02-v1.1 · 學生基本資料 API
---

### 使用對象
取得登入學生的名字、科系、雙主修、輔修，已拿多少通識學分，以及該類型最多拿多少，所有類型的通識學分資訊

### 規格
- API:`/api/student/dashboard`
- HTTP Method：GET
- 呼叫參數

| 參數名稱      | 必填 | 資料類型 | 說明 |
| :-------------| :--: | :------ | :--- |
| Authorization |  V   | String  | Bearer JWT Token |

註1:透過此Token判斷是哪一位學生  
註2:只需要使用Token，不需要body

- 回傳參數

| 參數名稱 | 類型 | 說明 | 範例 |
| :-- | :-- | :-- | :-- |
| StatusCode  | Int                | API執行狀態代碼 | 200 |
| Message     | String             | API執行狀態說明 | success |
| Data        | `List<object>` | 回傳學生資料    | 請看下面範例 |

- 範例
    ```
    {
        "StatusCode":200,
        "Message":"success",
        "Data": {
            "Student_Info": {
                "Name": "王小明",
                "main_department": "資訊科學學系",
                "secondary_department": None,
                "sub_main1_department": "數位內容學程",
                "sub_main2_department": None
            },
            "General_Education_Graduation_Credits": {
                "General_Education_Required_Credits": 28,
                "General_Education_Taken": 18
            },
            "CoreGeneral_Education": {
                "Required":3,
                "Taken": 2
            },
            "Humanities":{
                "Humanities_Maximum_Credits":7,
                "Humanities_Required_Credits":3,
                "Humanities_Credit_Taken": 2
            },
            "Social":{
                "Social_Maximum_Credits":7,
                "Social_Required_Credits":3,
                "Social_Credit_Taken": 2
            },
            "Sciences":{
                "Sciences_Maximum_Credits":7,
                "Sciences_Required_Credits":3,
                "Sciences_Credit_Taken": 2
            },
            "Computer":{
                "Computer_Maximum_Credits":0,
                "Computer_Required_Credits":0,
                "Computer_Credit_Taken": 0
            },
            "Residential":{
                "Residential_Maximum_Credits":3,
                "Residential_Required_Credits":0,
                "Residential_Credit_Taken": 1
            },
            "Foreign":{
                "Foreign_Maximum_Credits":6,
                "Foreign_Required_Credits":6,
                "Foreign_Credit_Taken": 3
            },
            "Chinese":{
                "Chinese_Maximum_Credits":6,
                "Chinese_Required_Credits":3,
                "Chinese_Credit_Taken": 3
            },
            "PE":{
                "PE_Maximum_Credits":4,
                "PE_Required_Credits":4,
                "PE_Credit_Taken": 3
            }
        }
    }
    ```

- 狀態說明

| 狀態碼 | 狀態描述 |
| :----- | :------  |
| 200    | 登入成功  |


---

03-v1.2 · 修課紀錄 API
---

### 使用對象
- 主畫面「查看修課紀錄」按鈕點擊後，渲染修課清單頁面

### 規格
- API：`api/details`
- HTTP Method：POST
- 呼叫參數

| 參數名稱        | 必填 | 資料類型 | 說明 |
| :-------------- | :--: | :------ | :--- |
| Authorization   | V    | String  | Bearer JWT Token |
| Type            | V    | String  | 篩選類別 (all, humanities, social, sciences, computer, residential, chinese, foreign, PE) |

- 回傳參數

| 參數名稱    | 類型                | 說明             | 範例 |
| :---------- | :----------------- | :--------------- | :-- |
| StatusCode  | Int                | API執行狀態代碼   | 200 |
| Message     | String             | API執行狀態說明   | success |
| Data        | Object             | 回傳資料物件      | 請看下方 |
| ├─type      | String             | 修課類別          | 篩選類別 (all, humanities, social, sciences, computer, residential, chinese, foreign, PE) |
| ├─courses   | `List<object>`       | 該類別下的修課清單 | `請看下面範例` |

- 範例
    ```
    {
        "StatusCode": 200,
        "Message": "success",
        "Data": {
            "type": "humanities",
            "courses":[
                {
                "class_name":"哲學概論",
                "credits":3,
                "remark":"人文通識",
                "core":True,
                "ispassed":True
                }
            ]
        }
    }
    ```  

- 狀態說明

| 狀態碼 | 狀態描述 |
| :----- | :------ |
| 200    | 查詢成功 |

