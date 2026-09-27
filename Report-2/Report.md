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

### 2.3 Radiomics 放射組學
第二個案例為利用形態定量與放射組學分析，預測兒童幕上低級別膠質瘤相關癲癇。
Radiomics 的概念是將醫學影像轉換為大量可以量化的數值特徵，例如：
- Shape features：形狀特徵
- Intensity features：強度特徵
- Texture features：紋理特徵
- Location features：位置特徵

研究先利用 T2-FLAIR MRI 找出腫瘤 ROI，接著進行 Spatial Normalization、Resampling、Resegmentation、Discretization 與 Intensity Normalization，再進行 Radiomics Feature Computation。
研究總共由 218 個特徵中進行特徵選擇，其中包含 10 個腫瘤位置特徵及 208 個放射組學特徵。
較重要的特徵包含：
- Temporal lobe
- Midbrain
- High Dependence High Gray Level Emphasis
- Elongation
- Area Density
- Information Correlation
- Normalized Inverse Difference
- Intensity Range

研究結果顯示，單獨使用腫瘤位置或 Radiomics 已具有一定預測能力，而將兩者結合後可以進一步提高對癲癇發生的預測效果。
其中 Linear SVM 的表現達到：
- Precision：0.955
- Recall：0.913
- Specificity：0.960
- Accuracy：0.938
- F1-score：0.933
- AUC：0.950

可以看出，影像除了能讓醫師進行視覺判讀之外，也能轉換成大量量化特徵，再透過機器學習找出與疾病相關的重要資訊。

### 2.4 EEG 與機器學習
第三個案例為使用腦電圖與機器學習預測憂鬱症藥物的長期療效。
研究包含 77 位重度憂鬱症（Major Depressive Disorder, MDD）患者，利用 Week 0 與 Week 1 的 EEG 資料進行特徵擷取，再預測 Week 4、Week 6 及 Week 8 的藥物治療效果。
EEG 前處理流程包含：
- FIR Bandpass Filtering
- Common Average Re-referencing
- ICA 去除眼動干擾
- δ、θ、α、β 頻帶分離

擷取的 EEG 特徵主要分為兩大類：
Power Analysis
- Absolute Power（AP）
- Relative Power（RP）

Phase Synchronization
- Phase Lock Value（PLV）
- Phase Lag Index（PLI）
- weighted Phase Lag Index（wPLI）

研究同時加入部分臨床特徵，最後利用機器學習建立治療反應預測模型，並使用 Leave-One-Out Cross Validation（LOOCV） 進行驗證。

模型預測準確率分別為：
- Week 4：83.1%
- Week 6：73.3%
- Week 8：80.0%

研究指出 Functional Connectivity Analysis 與 Phase Synchronization 是提升預測準確度的重要因素。
此外，EEG 分析過程使用 ICA 分離腦波訊號成分，並透過 ICLabel 協助判定各 Independent Component 可能屬於 Brain、Eye、Muscle、Heart、Line Noise 或其他雜訊。
此研究說明 EEG 這類非侵入式生理訊號，可以透過資料分析與機器學習，協助醫師提早判斷患者是否可能對藥物治療產生反應，減少長時間嘗試不同藥物所造成的負擔。

### 2.5 nnU-Net 醫學影像自動分割
第四個案例主要研究聽神經瘤（Vestibular Schwannoma, VS）。
聽神經瘤為源自第八對顱神經的良性腫瘤，常見於橋小腦角區。患者可能出現聽力下降、耳鳴、眩暈等症狀，若腫瘤持續增大，也可能壓迫其他神經，因此需要利用 MRI 長期追蹤。
研究使用 nnU-Net 建立腫瘤自動分割模型。nnU-Net 屬於 U-Net 架構的自動化醫學影像分割方法，主要包含：
- Encoder / Downsampling：擷取深層特徵。
- Decoder / Upsampling：恢復影像空間資訊。
- Skip Connection：保留影像細節，提高邊界分割能力。

研究的自動分割訓練資料共有 577 例，並另外使用 178 例 MRI 作為外部獨立測試資料。
自動分割結果如下：

| 測試資料 | DSC | Precision | Recall |
|---|---:|---:|---:|
| Internal Test | 91.54% ± 0.55 | 92.90% ± 1.84 | 91.37% ± 1.76 |
| External Test | 91.16% ± 0.47 | 92.08% ± 1.41 | 90.75% ± 1.46 |

### 2.6 聽神經瘤治療反應預測

研究根據 Gamma Knife 治療後的腫瘤體積變化進行分類。

體積變化百分比公式為：

ΔVolume(%) = (Vₜ - V₀) / V₀ × 100%

其中：

- \(V_0\)：治療前腫瘤體積
- \(V_t\)：追蹤時間點的腫瘤體積

治療後反應分為：

- Growth
- Stable
- Delayed Regression
- Direct Regression

155 位患者中：

- Delayed Regression：83 例
- Direct Regression：53 例
- Stable：9 例
- Growth：10 例

研究進一步建立兩階段機器學習模型。

### 第一階段判斷

**Regression vs. Non-Regression**

### 第二階段判斷

**Direct Regression vs. Delayed Regression**

比較的機器學習模型包含：

- ExtraTrees
- Random Forest
- Gradient Boosting
- LightGBM
- CatBoost
- XGBoost
- Logistic Regression
- LDA
- QDA
- KNN
- GaussianNB
- Balanced Random Forest
- SVM

因各類別數量差異較大，研究利用 **SMOTE** 與 **Class Weight Balancing** 處理類別不平衡問題。

最終 ExtraTrees 在兩階段模型中具有較穩定的表現。

### 第一階段模型表現

| 指標 | 結果 |
|---|---:|
| Accuracy | 82.58 ± 5.40% |
| Precision | 83.37 ± 5.36% |
| Recall | 82.58 ± 5.40% |
| F1-score | 82.34 ± 4.33% |
| AUC | 71.43 ± 7.74% |

### 第二階段模型表現

| 指標 | 結果 |
|---|---:|
| Accuracy | 63.31 ± 9.90% |
| Precision | 64.52 ± 9.57% |
| Recall | 63.31 ± 9.90% |
| F1-score | 63.31 ± 9.90% |
| AUC | 68.17 ± 12.48% |

### 2.7 SHAP 模型解釋

為提升機器學習模型的可解釋性，研究使用 **SHAP（Shapley Additive Explanations）** 進行特徵重要性分析。

SHAP 可以量化每項特徵對模型預測結果的影響程度，並利用 **Mean Absolute SHAP Value** 作為整體特徵重要性指標。

### 第一階段的重要特徵

- `wavelet-HLL_firstorder_Maximum`
- `wavelet-LHL_firstorder_Mean`
- `Mean dose (Gy)`
- `wavelet-HLL_firstorder_Skewness`

### 第二階段的重要特徵

- `log-sigma-1-0-mm-3D_firstorder_Minimum`
- `log-sigma-1-0-mm-3D_firstorder_Median`
- `gradient_firstorder_InterquartileRange`
- `wavelet-HLH_firstorder_Skewness`
- `Mean dose (Gy)`

這些結果顯示腫瘤影像中的灰階強度、紋理異質性以及放射治療劑量，都可能與患者後續治療反應有關。

研究最後也將 MRI、自動分割、腫瘤體積計算及治療反應預測整合至圖形化使用者介面（GUI），提高未來臨床使用的便利性。

## 三、心得報告
這次講座讓我更了解人工智慧在醫療領域中的實際應用，原本我認為 AI 醫療主要是利用影像辨識協助判斷疾病，但透過這次介紹的 OCT、MRI、EEG 與聽神經瘤等研究案例，我發現 AI 已經可以進一步應用在手術預後、藥物療效及長期治療反應的預測。其中讓我印象最深的是資料前處理與 Radiomics，因為原始醫療資料不能直接拿來訓練模型，還需要經過裁切、標準化、雜訊移除、ROI 分割及特徵擷取等步驟，而 Radiomics 更能將影像中的形狀、強度與紋理轉換成數值特徵，找出肉眼不容易察覺的差異。另外，聽神經瘤案例從 nnU-Net 自動分割腫瘤、計算體積與特徵，到利用機器學習預測治療結果，最後整合成 GUI，也讓我看到一個較完整的 AI 臨床應用流程。講座中介紹的 Grad-CAM 與 SHAP 也讓我了解到，醫療 AI 除了追求 Accuracy 之外，模型的可解釋性、資料品質、泛化能力與實際臨床使用方式也很重要。整體而言，我認為 AI 在醫療上的價值並不是取代醫師，而是結合醫師的專業知識與醫療資料，成為協助診斷、預測及治療決策的重要工具。

