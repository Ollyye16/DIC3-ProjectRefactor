# Spec Delta

## Purpose

離線從開發者挑選的臺中美食 YouTube 影片建立可重現的語料，抽取有原文依據的情境標籤，並將影片提及的店名對應到唯一餐廳（DESIGN.md §4、§9.1、§9.2；D014、D015、D017）。

## ADDED Requirements

### Requirement: 語料限於開發者挑選的影片
初版語料 SHALL 只來自開發者挑選的影片；系統 MUST 只推薦語料中提及的餐廳。

#### Scenario: 語料外餐廳
- **WHEN** 某餐廳未出現在任何已收錄影片中
- **THEN** 該餐廳不會成為候選

### Requirement: 逐字稿依序取得並記錄方式
系統 SHALL 依序嘗試人工 CC 字幕、自動字幕、Whisper 語音轉文字取得逐字稿，並記錄實際取得方式；語音轉文字另記模型名稱與版本。逐字稿原文 MUST NOT 公開散布。

#### Scenario: 無人工字幕
- **WHEN** 影片沒有人工字幕但有自動字幕
- **THEN** 系統使用自動字幕，來源紀錄標示為自動字幕

#### Scenario: 完全無逐字稿
- **WHEN** 三種方式都無法取得逐字稿
- **THEN** 系統只使用標題、說明與留言，並於來源紀錄標示缺少逐字稿

### Requirement: 情境標籤使用固定版本化標籤表
抽取結果的情境標籤 SHALL 屬於 DESIGN.md §9.1 的版本化標籤表；需求解析與片段抽取 MUST 使用同一份標籤表。

#### Scenario: 標籤不在表內
- **WHEN** 抽取輸出的標籤不在標籤表中
- **THEN** 該筆抽取結果被丟棄，不重試猜測

### Requirement: 抽取結果須附原文引文
每筆抽取 SHALL 包含〈餐廳名稱、面向、情境標籤、情感、原文引文〉，且引文 MUST 為來源原文的子字串。

#### Scenario: 引文不是原文子字串
- **WHEN** 模型輸出的引文在原文中找不到
- **THEN** 該筆抽取結果被丟棄

### Requirement: 負向情感保存為反證
情感為負向的片段 SHALL 照樣保存，作為該標籤的反證，MUST NOT 視為未提及。

#### Scenario: 負評
- **WHEN** 片段寫「太吵了不適合約會」
- **THEN** 系統保存一筆「約會」標籤的負向支持關係

### Requirement: 片段只歸屬單一餐廳
一支影片提及多間餐廳時，每個片段 SHALL 對應到單一餐廳；無法判斷者 MUST NOT 歸屬。

#### Scenario: 歸屬不明
- **WHEN** 片段無法判斷在描述哪一間餐廳
- **THEN** 該片段不歸屬任何餐廳

### Requirement: 店名對應到 Place ID 與座標
系統 SHALL 先合併同店不同寫法為別名，再以 Places 取得 Place ID 與座標；只持久保存 Place ID，MUST NOT 保存 Places 評論建立向量索引。歇業或不在臺中者排除。

#### Scenario: 字幕錯字
- **WHEN** 店名來自自動字幕或語音轉文字，且無法以影片標題與說明交叉確認
- **THEN** 該店名不歸屬，記錄原因供人工確認

#### Scenario: 無法對應 Place ID
- **WHEN** 影片提及的餐廳無法對應到 Place ID
- **THEN** 不納入語料，記錄原因，不自行猜測分店
