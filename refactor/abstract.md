# 摘要（重構版，A–F 六段式）

> 依《期末專題提案作業規範》Abstract A–F 結構。和原提案相比，補上 **E（評估）** 段，A 段加入 2026 年臺中公車運量。引用編號見 [sources.md](../sources.md)。
> 提案階段尚未實作，以下沒有任何實驗數值。

## 中文摘要

**A 動機**：臺中市政府於 2025 年發布的家庭外食分析指出，2024 年底臺中市餐飲業營利事業家數達 22,612 家 [1]；臺中市政府交通局 2026 年的報告則指出，市區公車每月運量約 680 萬人次 [2]。對約會、慶生或朋友聚餐的使用者而言，多樣的餐廳選擇除了要符合用餐情境，也要實際方便前往。

**B 挑戰**：然而，評論與用餐情境在語意上相符，不代表餐廳在交通上適合使用者。若餐廳需要長時間候車、步行或多次轉乘，推薦結果可能並不實用；若選定餐廳後才附交通路線，交通條件也無法回饋到餐廳的取捨。因此，本研究關注如何在語意候選形成後，把交通可行性與便利性納入餐廳的最終選擇。

**C 解法**：為此，本研究提出一個考量臺中公車交通便利性的情境感知餐廳推薦 AI Agent System。系統先將使用者情境與餐廳評論語意對齊，再取得候選餐廳的公車搭乘方案，篩選並排序最適合的餐廳。

**D 方法**：系統以語言模型整理需求，結合向量或混合檢索召回餐廳，以知識圖譜保存餐廳屬性與來源片段，並透過 TDX 取得路線與可用的公車到站資料。分析引擎計算步行、候車、乘車與轉乘負擔，建立抵達及用餐時間線，再以通過、不通過、不確定三值查核預算、營業與搭乘條件。最終輸出餐廳、適合該情境的評論依據、交通比較與搭乘方案；推薦理由依判定紀錄產生。

**E 評估**：評估規劃以四個研究問題對應四組比較。
- **RQ1 檢索**：以臺中美食影片建立的情境語料及人工標註的小型查詢集，比較關鍵字、向量與混合檢索的召回表現（Recall@K、nDCG@K）。
- **RQ2 交通**：比較「只依語意排名、最後才補路線」與「排序前納入交通條件」的推薦結果，觀察交通負擔與情境相關性。
- **RQ3 查核**：比較二值判定與三值查核在「違反必要條件」及「證據不足卻宣稱符合」的比例，並同時報告可回答查詢的覆蓋率。
- **RQ4 時間線**：以 TDX 預估到站紀錄重播，比較「只看出發時營業」與「完整時間線」在上車、轉乘與打烊判定的差異。

重播可驗證判定流程，但不能單獨證明實際減少錯過公車的比例。

**F 預期**：本研究目前為提案階段，尚無實驗結果。預期建立一個能說明情境依據與交通取捨的餐廳推薦原型，在保留語意相符餐廳的前提下，結合交通篩選與排序，提供適合用餐情境且方便前往的推薦。

**關鍵詞**：餐廳推薦、評論語意、情境感知推薦、臺中公車、向量檢索、知識圖譜、三值查核

## English Abstract

A report released by the Taichung City Government in 2025 recorded 22,612 food-service businesses at the end of 2024, and a 2026 report by the Taichung Transportation Bureau states that city buses carry about 6.8 million passengers per month [1][2]. For users planning a date, birthday celebration, or gathering, a suitable restaurant should match the dining context and be convenient to reach.

However, semantic relevance between restaurant reviews and a dining context does not imply convenient transportation. Long walking or waiting times and multiple transfers may make a relevant restaurant impractical. Adding a route only after selecting a restaurant prevents transportation conditions from influencing the selection.

This project proposes a context-aware restaurant recommendation AI agent system that considers Taichung bus accessibility. It first aligns the user's dining context with restaurant-review semantics to retrieve candidates, then obtains bus itineraries and uses their feasibility and convenience to filter and rank restaurants.

The system uses a language model to structure user requirements, vector or hybrid retrieval to recall restaurants, and a knowledge graph to associate attributes with supporting text. TDX services provide itinerary candidates and available arrival estimates. A programmatic analysis engine computes walking, waiting, riding, transfer, arrival, and dining times. Budget, opening hours, and boarding conditions are checked as pass, fail, or uncertain. Outputs include restaurants, supporting review evidence, transportation comparisons, and bus itineraries; explanations are generated from decision records.

The planned evaluation maps the four research questions to four comparisons. Using a context corpus built from Taichung food videos and a small manually labeled query set, keyword, vector, and hybrid retrieval are compared by Recall@K and nDCG@K. Rankings that add routes only after semantic ranking are compared with rankings that incorporate transportation before the final order, in terms of travel burden and contextual relevance. Binary and three-valued checking are compared by the rates of constraint violations and of unsupported claims of compliance, reported together with query coverage. Replayed TDX arrival-estimate records are used to compare departure-time-only checks with the full timeline for boarding, transfer, and closing-time decisions; such replays validate the decision procedure but cannot by themselves show a reduction in actual missed buses.

This work is at the proposal stage and has no experimental findings. The expected outcome is a prototype that explains contextual relevance and transportation trade-offs, helping users select restaurants that suit the dining occasion and are convenient to reach.

**Keywords**: restaurant recommendation, review semantics, context awareness, Taichung buses, vector retrieval, knowledge graph, three-valued checking
