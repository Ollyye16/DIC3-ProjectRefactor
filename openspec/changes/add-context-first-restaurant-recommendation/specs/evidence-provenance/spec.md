# Spec Delta

## Purpose

保存每筆資料的來源與時間欄位，使每項判定與推薦聲明都能回溯到來源片段或計算記錄（DESIGN.md §6、§11）。

## ADDED Requirements

### Requirement: 區分四種時間欄位
每筆來源 SHALL 分別保存 `published_at`、`observed_at`（或來源更新時間）、`retrieved_at` 與適用日期區間；缺少來源時間時 MUST 保留空值，不以取得時間代替。

#### Scenario: 缺來源更新時間
- **WHEN** 某價格資料沒有來源更新時間
- **THEN** `observed_at` 為空，`retrieved_at` 只記錄系統取得時間

### Requirement: 影片發布日期不證明資訊仍有效
影片發布日期 SHALL 只描述發布事件，MUST NOT 作為其中價格或營業資訊至今有效的證據。

#### Scenario: 舊影片價格
- **WHEN** 影片於 2024 年發布並提到價格
- **THEN** 價格保存為參考並標示觀測時間，不視為目前價格

### Requirement: 區分來源類型
來源 SHALL 標示類型：影片本文、影片留言、人工字幕、自動字幕或語音轉文字；影片留言 MUST 與影片本文分開，不等同實際到店評論。

#### Scenario: 留言片段
- **WHEN** 支持片段來自影片留言
- **THEN** 輸出標示其來源類型為留言

### Requirement: 衝突證據並存
不同來源互相衝突時 SHALL 並存，MUST NOT 直接覆寫。

#### Scenario: 兩個價格來源
- **WHEN** 兩支影片提到不同價格
- **THEN** 兩筆價格證據都保存，查核依 constraint-checking 規則處理

### Requirement: 推薦聲明可回溯
每項推薦聲明 SHALL 連結至來源片段或計算記錄。

#### Scenario: 追查推薦理由
- **WHEN** 使用者查看「適合慶生」的理由
- **THEN** 系統能顯示對應的原文片段與其來源影片

### Requirement: 鄰近站位不代表可搭乘
餐廳與站位的鄰近關係 SHALL 只作為交通候選，MUST NOT 作為存在可搭乘班次的證據。

#### Scenario: 站牌就在門口
- **WHEN** 餐廳旁有公車站，但查詢時段無對齊的班次
- **THEN** 上車條件不因鄰近而判為 T
