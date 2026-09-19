### 日期:2026/09/15
### 報告主題:網路診斷與容錯
### 講師:王大進
---
### 一、簡介
隨著多處理器系統、多核心架構與大型互連網路的發展，系統中所包含的處理器與節點數量持續增加。雖然單一處理器本身可能具有相當高的可靠度，但當大量處理器共同組成一個系統時，只要其中任一處理器發生故障，都可能影響整體系統的可靠性。因此，如何在大型系統中快速且正確地找出故障節點，便成為網路診斷與容錯技術的重要研究問題。
以課堂投影片中的例子而言，假設單一處理器的可靠度為 **99.99%**，也就是 `0.9999`。若系統由 100 個沒有備援的處理器所組成，整體可靠度約為：

$$
0.9999^{100} \approx 99.01\%
$$

若增加至 1,000 個處理器，可靠度約下降為：

$$
0.9999^{1000} \approx 90.48\%
$$

而當系統具有 10,000 個處理器時：

$$
0.9999^{10000} \approx 36.79\%
$$

由此可以看出，即使單一節點具有非常高的可靠度，當系統規模持續增加時，整體系統出現故障的機率仍會明顯提高。因此，大型分散式系統除了提升單一元件的可靠度之外，更需要具備故障偵測、故障定位與容錯能力。

**System-Level Diagnosis（系統級故障診斷）** 便是在此需求下發展的重要技術。其核心概念是讓系統中的節點彼此進行測試，透過不同節點所回報的測試結果，找出系統中的故障節點。由於執行測試的節點本身也可能發生故障，因此如何從可能包含錯誤資訊的測試結果中推導真正的故障狀態，是系統診斷中的重要問題。

本報告主要介紹網路節點診斷的基本概念、**Syndrome**、**PMC Model**、**t-Diagnosability**、**Distinguishability** 以及 **Conditional Diagnosability**，並介紹 **Hypercube**、**Star Network**、**k-ary n-cube** 與 **Bubble-sort Graph** 等互連網路在系統診斷上的相關內容。

---

## 二、相關技術說明

### 1.1 System-Level Diagnosis

System-Level Diagnosis 的主要概念為：

**mutually check**

也就是讓系統中的節點彼此進行測試，藉由測試結果找出故障節點。

這種方法不是只觀察單一節點，而是利用節點之間互相檢查所得到的資訊進行故障診斷。

---

### 1.2 Diagnosis of Network Nodes

在網路節點診斷中，節點可以對鄰近節點進行測試。

例如節點 `i` 可以測試其鄰居 `j`，並判斷節點 `j` 是：

- good，也就是 fault-free
- bad，也就是 faulty

測試結果可以用 0 或 1 表示。

當節點 `i` 認為節點 `j` 正常時：

$$
i\rightarrow j=0
$$

當節點 `i` 認為節點 `j` 故障時：

$$
i\rightarrow j=1
$$

但是，執行測試的節點 `i` 本身也可能是一個 faulty node。

如果測試節點本身正常，測試結果可以被信任；如果測試節點本身發生故障，則其測試結果可能為 0，也可能為 1，因此結果是不可靠的。

也就是說，當 tester 發生故障時：

$$
a_{ij}=0 \text{ 或 } 1
$$

因此，進行系統診斷時不能只依靠單一節點的測試結果，而需要綜合多個節點之間的測試資訊。

---

## 2、Syndrome

系統中不同節點之間可以產生許多測試結果。

一整組完整的節點測試結果稱為：

**Syndrome**

例如在三個節點 `a、b、c` 組成的網路中，測試方向可以包括：

$$
a\rightarrow b
$$

$$
b\rightarrow a
$$

$$
a\rightarrow c
$$

$$
c\rightarrow a
$$

$$
b\rightarrow c
$$

$$
c\rightarrow b
$$

將這些測試結果組合起來，就可以形成一組 Syndrome。

由於故障節點本身執行測試時，結果可能為 0 或 1，因此同一種故障狀況可能產生不同的 Syndrome。

例如，若某個節點已經發生故障，則由正常節點對它進行測試時，結果會指出其為 faulty；但該故障節點對其他節點進行測試時，其結果可能是任意值。

因此，一個故障集合可能對應多組不同的 Syndrome。

---

## 3、PMC Model

PMC Model 的完整名稱為：

**Preparata-Metze-Chien Model**

在 PMC Model 中，節點之間互相進行測試，並以：

$$
a_{ij}
$$

表示節點 `i` 對節點 `j` 所進行的測試結果。

若測試節點正常，而且被測試節點也正常：

$$
a_{ij}=0
$$

若測試節點正常，但被測試節點故障：

$$
a_{ij}=1
$$

若測試節點本身發生故障，則結果可能為：

$$
a_{ij}=0 \text{ 或 } 1
$$

因此，在 PMC Model 中，故障節點所產生的測試結果是不可信的。

系統會利用一個 **Centralized Arbiter（中央仲裁者）** 來解讀整體 Syndrome，並判斷每一個 processor 是：

- Faulty
- Fault-free

因此，PMC Model 的基本流程可以理解為：

1. 節點彼此進行測試
2. 產生多個測試結果
3. 將測試結果形成 Syndrome
4. Centralized Arbiter 解讀 Syndrome
5. 判斷系統中的故障節點

---

## 4、Diagnosable 與 Non-diagnosable

不同故障節點組合可能產生不同的 Syndrome。

如果不同故障情況所產生的 Syndrome 能夠被區分，系統就可以根據測試結果判斷目前真正的故障節點。

如果兩個不同的故障集合可能產生相同的 Syndrome，則在觀察到該 Syndrome 時，便可能無法判斷真正發生的是哪一種故障情況。

因此，故障情況可以分為：

- **Diagnosable**
- **Non-diagnosable**

若不同的故障集合可以從 Syndrome 中明確區分，則具有可診斷性。

若不同故障集合可能產生相同的 Syndrome，使系統無法唯一判斷故障狀態，則屬於 non-diagnosable。

---

## 5、Distinguishable

假設第一組故障節點為：

$$
F_1=\{f_{11},f_{12},\dots,f_{1x}\}
$$

其 Syndrome Table Size 為：

$$
2^{deg(f_{11})+deg(f_{12})+\cdots+deg(f_{1x})}
$$

另外一組故障節點為：

$$
F_2=\{f_{21},f_{22},\dots,f_{2y}\}
$$

其 Syndrome Table Size 為：

$$
2^{deg(f_{21})+deg(f_{22})+\cdots+deg(f_{2y})}
$$

如果 `F1` 與 `F2` 的 Syndrome Tables 沒有重疊，則稱：

$$
F_1 \text{ and } F_2
$$

為：

**Distinguishable**

也就是這兩種故障情況可以透過 Syndrome 加以區分。

相反地，如果 Syndrome Tables 發生重疊，則可能無法根據測試結果唯一判斷真正的故障集合。

---

## 6、t-Diagnosability 診斷度

如果系統中存在太多故障節點，就可能有太多測試結果來自 faulty node，使得有效且可信的資訊逐漸減少。

當故障節點數量太多時，Centralized Arbiter 便可能無法正確判斷所有故障節點。

因此，對一個特定的網路而言，存在一個允許的最大故障節點數量：

$$
t
$$

只要故障節點數量沒有超過 `t`，系統仍然可以根據節點之間的互測結果正確辨識所有故障節點。

這個：

$$
t
$$

稱為該網路的：

**Diagnosability**

也就是診斷度。

如果一個網路最多可以在 `t` 個故障節點的情況下仍然正確進行診斷，則稱該網路為：

**t-diagnosable**

當故障節點數量超過 `t` 時，節點互測的診斷方式便可能失效。

---

### 7.1 三節點網路的診斷度

假設有三個節點：

$$
a,b,c
$$

形成一個三角形網路。

不同的故障情況包括：

- `a` 故障
- `b` 故障
- `c` 故障

此網路的診斷度為：

$$
t=1
$$

因此，此網路為：

**1-diagnosable**

也就是當系統中最多只有一個節點故障時，可以正確辨識故障節點。

---

### 7.2 t-Diagnosable 的條件

對於網路：

$$
G=(V,E)
$$

要成為 `t-diagnosable`，其條件包含：

$$
|V|\geq2t+1
$$

以及：

$$
\kappa(G)\geq t
$$

其中：

$$
|V|
$$

代表網路中的節點總數。

而：

$$
\kappa(G)
$$

代表網路 `G` 的 min degree。

因此，網路若要具有較高的診斷度，就必須具備足夠的節點數量與連接程度。

---

## 8、Hypercube 的診斷度

n 維 Hypercube：

$$
Q_n
$$

為：

$$
n\text{-diagnosable}
$$

因此其診斷度為：

$$
t(Q_n)=n
$$

以三維 Hypercube：

$$
Q_3
$$

為例，其節點包括：

- `000`
- `001`
- `010`
- `011`
- `100`
- `101`
- `110`
- `111`

共八個節點。

`Q3` 的診斷度為：

$$
3
$$

因此：

$$
Q_3
$$

為：

**3-diagnosable**

---

## 9、Star Network 的診斷度

n 維 Star Network：

$$
S_n
$$

為：

$$
(n-1)\text{-diagnosable}
$$

因此其診斷度為：

$$
t(S_n)=n-1
$$

以：

$$
S_4
$$

為例：

$$
4-1=3
$$

因此：

$$
S_4
$$

為：

**3-diagnosable**

---

## 10、Conditional Diagnosability

一般的 Diagnosability 對故障節點的分布沒有進行限制，也就是故障節點可以任意分布。

如果對故障節點的分布加入合理限制，便可能提高整個網路的診斷能力。

其中一個重要的限制為：

> the neighboring nodes of any one node cannot be all faulty simultaneously.

也就是：

**任意一個節點的所有鄰居不能同時全部發生故障。**

如果某個節點周圍所有鄰居同時故障，便會產生大量不確定的測試結果，增加系統診斷的困難。

因此，在 Conditional Diagnosability 中，這種故障分布會被排除。

排除這些情況之後，可以減少許多不確定的測試結果，使節點互測能夠檢測出更多的故障節點。

---

## 11、條件診斷度

加入故障分布條件後，系統的 Diagnosability 可以明顯提高，甚至可能比原本高出數倍。

在這種條件假設下所定義的 Diagnosability 稱為：

**Conditional Diagnosability**

中文為：

**條件診斷度**

其核心概念是：

- 一般 Diagnosability：故障節點可以任意分布
- Conditional Diagnosability：對故障節點分布加入限制

透過排除某些特殊的故障分布，可以讓系統在存在更多故障節點的情況下仍然維持診斷能力。

---

## 12、PMC Model 下的 Conditional Diagnosability

以 Hypercube：

$$
Q_n
$$

為例。

在 PMC Model 下，當：

$$
n\geq5
$$

時，`Qn` 的 Conditional Diagnosability 為：

$$
4n-7
$$

因此可以表示為：

$$
t_c(Q_n)=4n-7,\quad n\geq5
$$

相較之下，原本沒有加入條件限制時，`Qn` 的 Diagnosability 為：

$$
n
$$

也就是：

$$
t(Q_n)=n
$$

因此兩者可以整理為：

| 類型 | Hypercube \(Q_n\) 的診斷度 |
|---|---|
| Diagnosability | \(n\) |
| Conditional Diagnosability | \(4n-7\)，\(n\geq5\) |

由此可以看出，在加入合理的故障分布條件後，可以明顯提升系統能夠診斷的故障節點數量。

---

## 13、Interconnection Network Examples

### 13.1 k-ary n-cube

k-ary n-cube 是一種互連網路。

其中一個範例中的節點包括：

- `00、10、20、30`
- `01、11、21、31`
- `02、12、22、32`
- `03、13、23、33`

各節點透過多條連線形成具有規則性的網路結構。

此類網路可以利用節點之間的連接關係進行互測與故障診斷。

---

### 13.2 Bubble-sort Graph

另一種互連網路為：

**Bubble-sort Graph**

例如：

$$
B_4
$$

其節點可以利用數字排列表示，例如：

- `1234`
- `1324`
- `2134`
- `2314`
- `2341`
- `3241`
- `3421`

各節點透過特定的連接方式形成 Bubble-sort Graph。

---

## 三、心得報告

透過這次網路診斷與容錯的內容，我了解到大型系統即使單一處理器具有很高的可靠度，隨著節點數量增加，整體系統仍可能更容易發生故障。PMC Model 讓我印象最深的是測試節點本身也可能故障，因此不能直接相信所有測試結果，而必須利用 Syndrome 綜合判斷。了解 t-Diagnosability 後，也明白診斷度代表系統在一定故障數量下仍能正確找出故障節點的能力。Conditional Diagnosability 說明，若對故障分布加入合理限制，可以提升系統的診斷能力。這次的講座讓我更熟悉網路故障診斷的基本原理，以及不同互連網路在容錯設計上的重要性。

---

## 四、關鍵字

`網路診斷`、`容錯`、`System-Level Diagnosis`、`PMC Model`、`Syndrome`、`Diagnosability`、`t-Diagnosability`、`Distinguishable`、`Conditional Diagnosability`、`Hypercube`、`Star Network`、`k-ary n-cube`、`Bubble-sort Graph`

---

## 五、參考文獻

1. F. P. Preparata, G. Metze, R. T. Chien, “On the connection assignment problem of diagnosable systems,” *IEEE Trans. Comput.*, 16(1967), 448–454.

2. 網路診斷與容錯課程簡報資料
