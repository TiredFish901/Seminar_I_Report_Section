### 日期:2026/09/22
### 報告主題:從數據驅動到臨床轉譯:人工智慧於醫學訊號診斷與預後評估之最新進展
### 講師:彭徐鈞
---
## 一、簡介
本次講座主要介紹人工智慧（Artificial Intelligence, AI）如何結合醫學影像、腦電訊號與臨床資料，進行疾病診斷、治療反應預測及術後預後評估。隨著醫療資料數位化程度提高，人工智慧除了能夠協助分析大量資料，也可以從醫學影像或生理訊號中擷取傳統人工判讀較難量化的特徵，進一步建立預測模型，提供臨床醫師作為決策參考。  

第一個案例以特發性黃斑上膜（Idiopathic Epiretinal Membrane, ERM）為研究對象，利用術前 OCT 影像建立深度學習模型，預測患者接受手術後的視力改善程度。第二個案例則針對兒童幕上低級別膠質瘤，利用 MRI 的腫瘤位置與放射組學特徵，預測患者是否會伴隨癲癇。第三個案例使用腦電圖（EEG）搭配機器學習，預測重度憂鬱症患者使用藥物後的長期治療反應。第四個案例則將 nnU-Net 自動影像分割、MRI 放射組學與機器學習結合，用於預測聽神經瘤患者接受 Gamma Knife 治療後的長期腫瘤反應。  

這些案例顯示，目前醫療人工智慧已不只停留在疾病辨識，而是逐漸發展至預後預測、治療規劃、影像自動化處理，以及臨床決策輔助等方向。

## 二、相關技術說明
### 2.1 機器學習基本流程
Machine Learning Workflow 分為以下六個步驟：
1. Access and load the data：取得並載入資料。
2. Preprocess the data：進行資料前處理。
3. Derive features：從處理後的資料中擷取特徵。
4. Train models：使用特徵建立及訓練模型。
5. Iterate：反覆測試及調整，找出較合適的模型。
6. Integrate：將訓練完成的模型整合至實際系統。

醫療資料在進行人工智慧分析前，通常需要經過影像裁切、正規化、重新取樣、ROI 擷取等處理，避免不同影像設備或資料格式對模型造成過大的影響。

### 2.2 深度學習與 OCT 影像預後預測
第一個研究案例為使用深度學習模型預測特發性黃斑上膜手術後的視覺預後。
黃斑上膜（ERM）是常見的視網膜疾病，患者可能接受手術治療，但即使術後黃斑厚度改善，視力恢復程度仍可能有所不同，因此研究嘗試從術前 OCT 影像直接預測患者術後的視力改善程度。
研究使用的深度學習模型包括：
- Inception-V3
- ResNet-101
- VGG-19
OCT 影像經過 ROI cropping、resize 與 data augmentation 後輸入模型進行訓練，並透過 5-fold cross validation 評估模型表現。
研究將術後一年視力改善分成兩類：
- Pronounced Visual Improvement（P-IM）：術後 Snellen 視力表的視力表現較術前提升至少兩個行級。
- Limited Visual Improvement（L-IM）：術後提升少於兩個行級。
模型評估指標包含 Recall、Specificity、Precision、F1-score、Accuracy 與 AUC。研究結果顯示，ResNet-101 的整體預測表現較為穩定，外部驗證中 Accuracy 約為 0.92，而 Recall、Precision 及 F1-score皆約為 0.93。
此外，研究使用 Grad-CAM（Gradient-weighted Class Activation Mapping） 產生 Heatmap，觀察模型在進行預測時關注 OCT 影像的哪些區域。透過熱圖可以將 AI 的判斷與視網膜微結構特徵進行對照，提高模型的可解釋性。
