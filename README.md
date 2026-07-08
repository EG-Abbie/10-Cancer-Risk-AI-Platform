# AI 癌症風險互動評估平台

這是一個可部署的互動式癌症風險評估平台，將既有問卷轉成健康探索式流程，並透過後端中繼 API 將結構化資料送往 Power Automate。

## 本機開發

正式串接請不要直接用 `file://` 開啟 `index.html`，請使用本機 Node 服務：

```bash
npm start
```

然後開啟：

```text
http://localhost:3000
```

若要測試送出到 Power Automate，需先設定環境變數：

```bash
export POWER_AUTOMATE_WEBHOOK_URL="Power Automate 完整 webhook URL"
npm start
```

## 目前包含

- 歡迎頁與非診斷免責說明
- AI Health Guide 角色區塊、真人感角色素材、浮動提示 chips、狀態光點與微互動動畫
- 健康探索式進度條、題目切換動效與模組完成回饋
- 個資告知與三項知情同意確認
- 基本資料、女性相關資訊、菸草與環境暴露、心理健康、飲食習慣、病史與家族史題組
- 近期身體狀況自由文字填答
- 自由文字即時整理、追問句與段落結束提示
- Email 收集
- 單選、多選、數字輸入、Email 輸入與條件式跳題
- 使用者可讀的資料確認列表
- 完成送出頁，不在前端呈現風險結果
- 每份填答會在瀏覽器端整理成表格列格式，存於 `window.latestSubmission`、`localStorage.assessment_submissions`，並在完成頁生成隱藏節點 `#submissionRowsJson`
- 每份填答會另外生成參考 `10Cancer_AI_structure_data_v1.xlsx` 的 71 欄最佳化寬表資料，包含單位轉換、多選編碼、缺失值與矛盾提醒，存於隱藏節點 `#structuredFeaturesJson`
- 前端送出至同站台 `/api/submit`
- `server.js` 由環境變數讀取 Power Automate webhook URL，避免把簽章 URL 暴露在公開 JavaScript

## 正式部署到 Render

1. 建立 GitHub repository，將本資料夾內容上傳。
2. 到 Render 建立 Web Service，連接該 GitHub repository。
3. Runtime 選 Node。
4. Start command 使用：

```text
npm start
```

5. 在 Render Environment Variables 新增：

```text
POWER_AUTOMATE_WEBHOOK_URL=Power Automate 完整 webhook URL
```

6. 部署完成後會取得公開網址，例如：

```text
https://eg-biomed-cancer-risk-assessment.onrender.com
```

7. 將公開網址產生 QR code，即可供使用者掃描填寫。

## 上線資料流

```text
使用者瀏覽器
→ Render /api/submit
→ Power Automate
→ Excel Office Script 寫入結構化資料
→ 模型 API
→ Email 報告
```

## 注意事項

- Power Automate webhook URL 含簽章，正式版請只放在 Render 環境變數，不要放進 `app.js`。
- 若更新 Power Automate trigger URL，需同步更新 Render 的 `POWER_AUTOMATE_WEBHOOK_URL` 並重新部署或重啟服務。
- Email 報告內容與模型 API key 也應放在 Power Automate 或後端安全環境，不建議公開在前端。
