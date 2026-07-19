# BatchCard URL Integration API 說明文件

本文件介紹如何透過網址（URL）查詢參數將文章與設定載入至 BatchCard 中，甚至直接啟動 AI 智慧卡片生成。

---

## 基礎網址 (Base URL)

```
https://enkaichang.github.io/batch_card/
```

---

## 支援的網址參數 (Query/Hash Parameters)

您可以使用以下參數進行客製化跳轉：

> [!IMPORTANT]
> **長文章與「URI Too Long (414)」錯誤排除**：
> 當傳入的文章內容較長時，若使用網址查詢參數（即以 `?` 連接，例如 `?text=...`），網頁伺服器可能會因為 URL 長度超出限制而回傳 `Error: URI Too Long (414)` 錯誤。
> 
> **強烈建議**：將網址連接符號改為 **網址錨點（Hash, `#`）**（例如 `#text=...`）。Hash 參數純粹在瀏覽器端解析，不會發送至伺服器，因此能支援數萬字以上的長文章傳遞。BatchCard 支援以相同語法從 Hash 讀取以下所有參數。

| 參數名稱 (與別名) | 資料類型 | 預設值 | 說明 |
| :--- | :--- | :--- | :--- |
| `article` <br>或 `text` | `string` | *(無)* | **文章內容**：要生成社群卡片的文章本文。必須進行 URL 編碼 (URL Encoded)。 |
| `auto` <br>或 `auto_generate` <br>或 `autogen` | `string` | `"false"` | **自動生成開關**：設為 `"true"` 或 `"1"` 時，若已儲存 API 金鑰，會在網頁載入後**自動開始 AI 生成卡片**。 |
| `count` <br>或 `targetCount` <br>或 `cardCount` <br>或 `card_count` | `number` | `5` | **預估卡片張數**：限制生成的卡片數量，有效範圍為 `1` 至 `10` 張。 |
| `model` | `string` | `"gemini-3.5-flash"` | **指定 Gemini 模型**：例如 `gemini-3.5-flash`、`gemini-2.5-flash`、`gemini-3.1-flash-lite`。若傳入非預設模型，會自動切換為「自訂模型名稱」。 |
| `apiKey` <br>或 `api_key` | `string` | *(讀取本地儲存)* | **Gemini API 金鑰**：臨時覆蓋並儲存使用者的 API 金鑰 (以 `AIzaSy` 開頭的字串)。 |
| `mode` <br>或 `genMode` | `string` | `"replace"` | **生成模式**：<br>- `replace`：覆蓋並清空現有卡片。<br>- `append`：將生成的新卡片追加到現有卡片後方。 |

---

## 網址建構範例 (推薦使用 Hash 傳遞)

### 1. 僅傳入文章內容 (推薦：使用 Hash `#` 傳遞以避免網址過長錯誤)
```
https://enkaichang.github.io/batch_card/#text=%E9%85%B8%E8%BE%A3%E6%B1%A4%E7%9A%84%E6%95%85%E4%BA%8B...
```

### 2. 傳入文章、限制 4 張卡片，並在載入後立即自動生成
```
https://enkaichang.github.io/batch_card/#text=%E6%B8%AC%E8%A9%A5%E5%85%A5%E5%8F%A3&count=4&auto=true
```

### 3. 指定極速 Lite 模型、追加模式、載入後自動生成
```
https://enkaichang.github.io/batch_card/#text=%E7%A4%BE%E7%BE%A4%E5%BD%B1%E9%9F%B3%E6%BD%AE&model=gemini-3.1-flash-lite&mode=append&auto=true
```

---

## 快速測試工具：瀏覽器一鍵書籤 (Bookmarklet)

您可以將以下 JavaScript 程式碼新增為瀏覽器書籤。在任何網站選取文字後點選書籤，即可將選取文字一鍵送入 BatchCard 進行卡片生成（已採用 `#` 錨點傳遞以避免長文字錯誤）：

```javascript
javascript:(function(){
  var text = window.getSelection().toString();
  if (!text) {
    // 若沒有選取任何文字，自動抓取該網頁的標題與前 3000 字主要內文
    text = document.title + '\n' + (document.querySelector('article')?.innerText || document.body.innerText).substring(0, 3000);
  }
  var baseUrl = 'https://enkaichang.github.io/batch_card/'; 
  var url = baseUrl + '#auto=true&text=' + encodeURIComponent(text);
  window.open(url, '_blank');
})();
```
