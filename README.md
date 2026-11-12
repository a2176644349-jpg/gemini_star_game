這是一份為您精心整理的完整技術與臨床應用文檔。您可以直接將其複製並保存為 Markdown（.md）文件，用於專案文檔、研究報告或 GitHub 專案說明（README.md）。

數位生物標記認知反應力評估工具（MCI-CRT Test）技術與臨床應用文檔

一、 臨床背景與篩查原理

輕度認知障礙（Mild Cognitive Impairment, MCI）是介於正常老化與失智症（如阿茲海默症）之間的過渡階段。臨床研究表明，MCI 患者早期往往伴隨前額葉執行功能下降、注意力不穩定（Attentional Lapses）以及運動規劃與決策遲緩。

傳統的紙筆測驗（如 MMSE、MoCA）存在主觀性強、受教育程度干擾大且無法捕捉微觀動力學變化的缺口。本專案將經典的「中心向外伸展任務（Center-Out Reaching Task）」與「選擇反應時（Choice Reaction Time, CRT）」相結合，開發了基於網頁端（HTML5 Canvas）的數位生物標記（Digital Biomarkers）評估工具，透過高精度的時間戳與空間軌跡追蹤，客觀量化受試者的早期認知與運動功能退化。

二、 遊戲玩法與互動架構

1. 基礎設定：
  ⚬ 採用單文件響應式架構（HTML/CSS/JS），支援電腦端（滑鼠按住拖曳）與行動端/平板（虛擬搖桿觸控）。
  ⚬ 遊戲區域採用固定邏輯坐標系（300 \times 150 像素）進行等比縮放渲染，確保任何設備上物理運動軌跡的一致性。
2. 核心互動流程（Center-Out 範式）：
  ⚬ 玩家操作畫面中央的方塊。
  ⚬ 每局開始前引入隨機等待延遲（Foreperiod / ISI，500ms ~ 1300ms），徹底打破受試者憑節奏盲目預判的可能性。
  ⚬ 畫面隨機位置突然生成目標「星星」（自動避開正前方 45° 盲撞扇形區）。
  ⚬ 玩家需迅速判斷方向並將方塊推向星星；捕捉成功後方塊短暫變綠，經隨機延遲後自動生成下一顆星。正式測試限時 30 秒。
3. 雙模式選擇：
  ⚬ 正式測試 (START)：限時 30 秒，自動收集與過濾數據並支援匯出雙層結構的臨床研究 CSV。
  ⚬ 練習模式 (PRACTICE)：不限時、不計分、無倒數，供高齡受試者或初次使用者熟悉操作，並將返回按鈕安全放置於設備最下方以防遮擋遊戲畫面。

三、 檢測數據與臨床生物標記公式詳解

本系統在每一次有效的試次（Trial）中精確捕捉四個關鍵時間戳：

⚬ T_0：目標星星生成的瞬間（刺激呈現）。
⚬ T_1：受試者首次推動搖桿、方塊產生有效位移的瞬間（運動啟動）。
⚬ T_2：受試者移動向量與目標指向向量的餘弦相似度 \cos\theta > 0.85（夾角 < 31.8^\circ）且持續穩定 2 幀以上的瞬間（方向決策完成）。
⚬ T_3：方塊成功碰撞並捕獲星星的瞬間（任務完成）。

1. 核心認知決策反應時（Decision CRT）

⚬ 公式：
  
  $$\text{Decision RT}_i = T_{2}^{(i)} - T_{0}^{(i)}$$
  
  (註：統計時自動剔除第 1 次 Trial 以消除「首試效應/習慣化效應」)
⚬ 臨床關聯：反映大腦視覺搜尋、空間定位與方向選擇的純認知處理速度（Cognitive Decision Phase）。MCI 患者的大腦皮質與前額葉訊息傳導效率下降，其 Mean CRT 會顯著長於健康同齡人。

2. 反應時標準差（SD-RT）

⚬ 公式：
  
  $$\text{SD-RT} = \sqrt{\frac{\sum_{i=1}^{n} (\text{RT}_i - \overline{\text{RT}})^2}{n - 1}}$$
  
  (其中 \text{RT}_i = T_2 - T_0，n 為有效試次總數)
⚬ 臨床關聯：反映大腦神經雜訊（Neural Noise）與注意力穩定性。MCI 患者常出現突發性注意力閃失，導致每次反應時間起伏劇烈，SD-RT 顯著增大，這是早期認知退化極為敏感的指標。

3. 反應時變異係數（CV, Coefficient of Variation）

⚬ 公式：
  
  $$\text{CV} = \frac{\text{SD-RT}}{\overline{\text{RT}}}$$
⚬ 臨床關聯：將標準差對平均值進行歸一化，消除了「本來就比較慢」的基線速度干擾。CV 越高，代表受試者相對於自身平均水平的認知波動越大，神經調節能力越差。

4. 運動執行時間與標準化推進速度（Mean_Norm_Speed）

⚬ 公式：
  
  $$\text{Norm\_Speed}_i = \frac{D_{0}^{(i)}}{T_{3}^{(i)} - T_{2}^{(i)}}$$
  
  (其中 D_0 為星星與方塊的初始直線距離，T_3 - T_2 為運動執行期)
⚬ 臨床關聯：透過初始距離 D_0 標準化運動速度，能客觀評估受試者的精細運動控制與小腦/運動皮質協調能力。這有助於臨床上將「腦子想得慢（Decision RT 長）」與「手部運動協調差（運動速度慢）」進行鑑別診斷。

5. 漏報率（Omission Errors）

⚬ 公式與判定：
  當星星在 T_0 生成後，若受試者超過 2 秒（2000ms） 仍完全未推動搖桿（即 T_1 超時且 T_1 = \text{null}），系統自動記錄一次漏報錯誤。
⚬ 臨床關聯：衡量嚴重的注意力崩潰與警覺性缺損（Attentional Lapses）。MCI 或早期失智症患者在連續表現測試中常出現高頻率的漏報現象。

四、 導出資料結構（CSV 格式）

評估結束後導出的 CSV 檔案採用雙層結構，方便直接匯入 Excel、Python（Pandas/Scikit-learn）或 SPSS 進行特徵工程與機器學習建模：

1. 第一部分：會話級 MCI 生物標記總結（Session Summary）
  ⚬ 包含：Timestamp, Age_Group, Gender, Final_Score, Valid_Trials_Analyzed, Omission_Errors_gt_2s, Mean_Decision_CRT_ms, SD_Decision_RT_ms, CV_Decision_RT, Mean_Execution_Time_ms, Mean_Norm_Speed_px_s, Rating
2. 第二部分：逐次試次運動學與時間軸明細（Trial-by-Trial Data）
  ⚬ 包含：Trial_No, Session_T0_ms, T0_Baseline_ms, T1_Initiation_ms, T2_Decision_Aligned_ms, T3_Capture_ms, Decision_Phase_T2_T0_ms, Execution_Phase_T3_T2_ms, Initial_Dist_D0_px, Norm_Exec_Speed_px_s, Trial_Status
