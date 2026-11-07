# TokenScope｜語言模型實驗桌

TokenScope 是一張在瀏覽器裡運算的實驗桌：你改一個條件，就能直接看到語言模型的計算跟著怎麼變。

三個互動實驗分別對應因果注意力、token 取樣和 BPE 合併。介面有繁中與英文兩種語言，每個數值都能逐項檢查，也能匯出成 JSON。使用時不需要帳號，也不需要 API 金鑰。

[開始實驗](https://f0909172434.github.io/tokenscope/?lang=zh-Hant) · [English](README.md) · [數學慣例](docs/MATH.md)

![TokenScope 注意力實驗桌](docs/screenshots/attention.png)

## 可以做什麼

| 實驗     | 動手調整                               | 觀察結果                                         |
| -------- | -------------------------------------- | ------------------------------------------------ |
| 注意力   | 查詢位置、Q 向量、因果遮罩、最後一個 V | 訊號連線、完整權重矩陣、分數、輸出向量與擾動差值 |
| 生成取樣 | logits、溫度、top-k、top-p、隨機種子   | 篩選前後機率、熵、候選數和可重現的獨立抽樣       |
| BPE 分詞 | 英文、繁中、重疊配對範例或自訂語料     | 配對頻率、逐步合併、復原、符號數與完整合併歷史   |

每個實驗都附一道觀念題。切換語言或實驗分頁時，目前的設定會保留；重新整理頁面才會重設。網址只記住語言和實驗分頁，不會記下你輸入的語料。

## 先試這個

1. 在注意力實驗選第三個 token。
2. 開啟「因果遮罩」，再開啟「擾動最後一個 V」。這時輸出不會改變。
3. 關閉遮罩。未來的位置開始參與計算，輸出也跟著改變。
4. 匯出 JSON，檢查這次實驗用到的輸入向量與權重。

## 保存與重播

匯出 JSON 之後，可以用「匯入實驗 JSON」恢復設定，所有結果都會重新計算。匯入器支援既有的第 1 版匯出格式，檔案上限 1 MB。檔案無效時，目前的實驗不會被改動。完整規則見[保存與重播說明](docs/REPLAY.md)。

## 本機執行

需要 Node.js **22.12 以上**，Node 24 也可以使用。

```sh
git clone https://github.com/f0909172434/tokenscope.git
cd tokenscope
npm ci
npm run dev
```

接著開啟 Vite 印出的本機網址。正式建置用 `npm run build`，預覽用 `npm run preview`。

每個 Release 也附有已建置好的靜態網站 ZIP。解壓縮後可以用 `python3 -m http.server 8080` 啟動；直接用 `file://` 開啟 HTML 則無法運作。字型已隨網站打包，執行時不會連到外部 API 或字型服務。只有第一次安裝套件時需要網路。

## 驗證方式

```sh
npm test
npm run build
npx playwright install chromium
npm run test:e2e
npm run format:check
```

測試包含 43 項數值與演算法測試，以及桌面和手機尺寸的瀏覽器測試。瀏覽器測試會實際操作介面，檢查種子重現、舊結果清除、Unicode 與空語料、匯出、語言切換、鍵盤切換分頁、水平溢出，並執行 axe 自動可及性規則。手機測試是在 Chromium 裡模擬手機視窗，不是在 Safari 實機上跑。自動掃描通過，也不代表符合完整的 WCAG 標準。

## 使用邊界

- **注意力**實驗只有一個注意力頭、五個位置，向量是手工設定的二維數值。它沒有訓練、沒有位置編碼，也不是完整的 Transformer。詞語標籤只是示意，不代表模型學到的語意。
- **取樣**實驗是對固定的 logits 反覆獨立抽樣，每次抽樣都不更新上下文，所以它不是完整的自回歸生成器。
- **BPE** 從 Unicode code point 開始，只在以空白分隔的詞內合併。它沒有空白 token，也沒有詞尾標記，因此不等同 GPT 的 byte-level tokenizer。介面最多接受 2,000 個 UTF-16 code units 和 50 步合併。
- 所有設定都只存在目前頁面的記憶體中，重新整理就會重設。匯出的檔案會包含你輸入的語料；匯入後設定會恢復，結果會重新計算。

原始文獻與程式結構寫在[英文 README](README.md)。TokenScope 是獨立的教育專案，與 Stanford 或任何模型供應商都沒有隸屬關係。

## 開源

原始程式與教學文字採用 [MIT License](LICENSE)。字型保留各自的 SIL Open Font License，詳見[第三方授權](docs/THIRD_PARTY.md)。想回報問題或提交修改，請先看[貢獻說明](CONTRIBUTING.md)。
