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

| 參數名稱    | 必填 | 資料類型 | 說明 |
| :----------| :--: | :------ | :--- |
| student_no | V    | String  | 帳號，也是學生的學號 |
| password   | V    | String  | 密碼 |

註:兩個欄位皆為必填

- 範例
    ```
    {
        "student_no":"111703888",
        "password":"test01"
    }
    ```  

- 回傳參數

| 參數名稱    | 類型          | 說明                          | 範例 |
| :---------- | :----------- | :---------------------------- | :-- |
| StatusCode  | Int          | API執行狀態代碼                | 200 |
| Message     | String       | API執行狀態說明                | success |
| Data        | object       | 回傳資料以及判斷是哪位學生的參數 | "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.xxx.yyy","student_number": "111703888","name": "王小明" |

- 範例
    ```
    {
        "StatusCode":200,
        "Message":"success",
        "Data": {
            "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.xxx.yyy",
            "student_number": "111703888",
            "name": "王小明"
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
- API:`/api/dashboard`
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
| Data        | `List<object>` | 回傳學生資料    | `請看下面範例` |

- 範例
    ```
    {
        "StatusCode":200,
        "Message":"success",
        "Data": {
            "student_info": {
                "name": "王小明",
                "main_department": "資訊科學學系",
                "secondary_department": None,
                "sub_main1_department": "數位內容學程",
                "sub_main2_department": None
            },
            "general_education_graduation_credits": {
                "general_education_required_credits": 28,
                "general_education_credits_taken": 18
            },
            "core_general_education": {
                "required_courses":3,
                "taken_courses": 2
            },
            "humanities":{
                "humanities_maximum_credits":7,
                "humanities_required_credits":3,
                "humanities_credit_taken": 2
            },
            "social":{
                "social_maximum_credits":7,
                "social_required_credits":3,
                "social_credit_taken": 2
            },
            "sciences":{
                "sciences_maximum_credits":7,
                "sciences_required_credits":3,
                "sciences_credit_taken": 2
            },
            "computer":{
                "computer_maximum_credits":0,
                "computer_required_credits":0,
                "computer_credit_taken": 0
            },
            "residential":{
                "residential_maximum_credits":3,
                "residential_required_credits":0,
                "residential_credit_taken": 1
            },
            "foreign":{
                "foreign_maximum_credits":6,
                "foreign_required_credits":6,
                "foreign_credit_taken": 3
            },
            "chinese":{
                "chinese_maximum_credits":6,
                "chinese_required_credits":3,
                "chinese_credit_taken": 3
            },
            "pe":{
                "pe_maximum_credits":4,
                "pe_required_credits":4,
                "pe_credit_taken": 3
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
| type            | V    | String  | 篩選類別 (all, humanities, social, sciences, computer, residential, chinese, foreign, PE) |

- 回傳參數

| 參數名稱            | 類型                | 說明             | 範例 |
| :-------------------| :----------------- | :--------------- | :-- |
| StatusCode          | Int                | API執行狀態代碼   | 200 |
| Message             | String             | API執行狀態說明   | success |
| Data                | Object             | 回傳資料物件      | 請看下方 |
| ├─type              | String             | 修課類別          | 篩選類別 (all, humanities, social, sciences, computer, residential, chinese, foreign, PE) |
| ├─selected_classes  | `List<object>`       | 該類別下的修課清單 | `請看下面範例` |

- 範例
    ```
    {
        "StatusCode": 200,
        "Message": "success",
        "Data": {
            "type": "humanities",
            "selected_classes":[
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

