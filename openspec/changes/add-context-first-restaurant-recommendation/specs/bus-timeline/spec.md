# Spec Delta

## Purpose

為每間候選餐廳取得臺中公車方案，並建立以同一時間基準計算的完整時間線，供營業與搭乘條件查核（DESIGN.md §4、§4.1、§7）。

## ADDED Requirements

### Requirement: 逐餐廳取得公車方案
系統 SHALL 以出發地、出發日期時間與餐廳座標，為每間候選餐廳取得公車與必要步行路線；同一餐廳 MAY 有多條方案。

#### Scenario: 多條路線
- **WHEN** 某餐廳有三條可用路線
- **THEN** 三條路線都進入後續查核

### Requirement: 上車時刻落在候車窗口內
系統 SHALL 以 `ready_time = departure_time + initial_walk + boarding_buffer` 計算準備完成時刻，且只有符合 `ready_time <= bus_time <= ready_time + W_max` 的班次可視為可上車。

#### Scenario: 走到站牌時車已離站
- **WHEN** 公車預估 3 分鐘後到站，使用者走到站牌需 6 分鐘
- **THEN** 該班次不視為可上車；API 回傳正常狀態不改變此結果

### Requirement: 轉乘須滿足時間順序
每一轉乘點 SHALL 滿足 `previous_arrival + transfer_walk + transfer_buffer <= next_departure`，且站位、方向、子路線與站序 MUST 能對齊。

#### Scenario: 轉乘接不上
- **WHEN** 前段抵達加步行與緩衝晚於後段發車
- **THEN** 該轉乘不成立

#### Scenario: 只有平均班距
- **WHEN** 後段只有平均班距，無法對齊特定班次
- **THEN** 該轉乘不視為已成立

### Requirement: 抵達與離店時間
系統 SHALL 以 `arrival = departure + initial_walk + sum(wait + ride) + sum(transfer_walk) + final_walk` 與 `leave = arrival + meal_duration` 計算；API 已包含的路段 MUST NOT 重複相加。

#### Scenario: 即時資訊更新
- **WHEN** 候車時間因即時資料改變
- **THEN** 後續抵達與離店時間依相同時間基準重算

### Requirement: 用餐時長優先由使用者提供
`D_meal` SHALL 優先使用使用者提供的值；使用預設值時 MUST 在輸出中揭露且可修改。

#### Scenario: 使用預設用餐時長
- **WHEN** 使用者未提供用餐時長
- **THEN** 輸出標示所採用的預設時長為假設

### Requirement: 倒數 ETA 需參考時刻
預估到站為倒數秒數時，系統 SHALL 以來源提供的參考時刻換算絕對時刻；缺少可確認的參考時刻時 MUST NOT 與使用者時間直接比較。

#### Scenario: 缺參考時刻
- **WHEN** ETA 只有倒數值且無來源更新時刻
- **THEN** 受影響的上車條件判為 U

### Requirement: 交通工具失敗不等於無路線
交通查詢逾時或失敗時，系統 SHALL 保存失敗狀態，MUST NOT 視為已證明沒有路線。

#### Scenario: TDX 逾時
- **WHEN** 某餐廳的路線查詢逾時
- **THEN** 該餐廳記錄為工具失敗，系統繼續處理其他候選
