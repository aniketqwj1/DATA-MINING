# **METHOD 5: ENTROPY AND PURITY (EXTERNAL MEASURES) - COMPLETE GUIDE**

---

## **5.1 CONCEPT: SYNOPSIS**

**Entropy and Purity** are external validation measures that evaluate clustering quality by comparing cluster assignments against ground truth class labels. They measure how "pure" or "homogeneous" each cluster is with respect to the known classes.

**Key Difference from Internal Measures:**
```
Internal measures (SSE, Silhouette, WSS/BSS):
- Don't require ground truth labels
- Measure geometric quality only
- Can't tell if clustering is "correct"

External measures (Entropy, Purity):
- Require ground truth class labels
- Measure agreement with true classes
- Can validate if clustering is "correct"
```

**PURITY:**
```
Measures the extent to which clusters contain a single class
High purity = Most clusters dominated by one class (good!)
Low purity = Clusters contain mixed classes (poor!)

Range: [0, 1]
Optimal: 1 (perfect purity)
```

**ENTROPY:**
```
Measures the disorder/uncertainty within clusters
Low entropy = Clusters are homogeneous (good!)
High entropy = Clusters are mixed/uncertain (poor!)

Range: [0, log₂(k)] where k = number of classes
Optimal: 0 (no disorder)
```

**Visual Concept:**
```
PURE CLUSTER (High Purity, Low Entropy):
Cluster contains mostly one class:
    ●●●●●●●     (90% Class A)
    ●●○         (10% Class B)

IMPURE CLUSTER (Low Purity, High Entropy):
Cluster contains mixed classes:
    ●●○○●       (40% Class A)
    ○●○●○       (60% Class B)
    
The more mixed, the higher entropy, the lower purity
```

---

## **5.2 MATHEMATICAL REASONING WITH PROOF**

### **5.2.1 Purity - Formal Definition**

**For a single cluster Cⱼ:**
```
Purity(Cⱼ) = (1/nⱼ) × max_i |Cⱼ ∩ Lᵢ|

Where:
- nⱼ = number of points in cluster Cⱼ
- Lᵢ = set of points in class i
- |Cⱼ ∩ Lᵢ| = number of points in cluster j that belong to class i
- max_i = take maximum over all classes
```

**Interpretation:**
```
Purity = fraction of dominant class in the cluster

Example:
Cluster has: 8 points from Class A, 2 points from Class B
Purity = 8/10 = 0.8
```

---

**Overall Purity (weighted average):**
```
Purity(overall) = (1/n) × Σ max_i |Cⱼ ∩ Lᵢ|
                          j=1 to k

Where:
- n = total number of data points
- k = number of clusters
- Sum over all clusters, weighted by cluster size
```

**Alternative formulation:**
```
Purity = Σ (nⱼ/n) × Purity(Cⱼ)
         j=1 to k

Weighted average of individual cluster purities
```

---

### **5.2.2 Entropy - Formal Definition**

**For a single cluster Cⱼ:**
```
Entropy(Cⱼ) = -Σ pᵢⱼ × log₂(pᵢⱼ)
               i=1 to c

Where:
- c = number of classes
- pᵢⱼ = |Cⱼ ∩ Lᵢ| / nⱼ (proportion of class i in cluster j)
- log₂ = logarithm base 2 (for bits of information)
- By convention: 0 × log₂(0) = 0
```

**Interpretation:**
```
Entropy measures uncertainty/disorder in the cluster
- All points from one class: Entropy = 0 (no uncertainty)
- Points evenly distributed across classes: Entropy = max (high uncertainty)
```

---

**Overall Entropy (weighted average):**
```
Entropy(overall) = Σ (nⱼ/n) × Entropy(Cⱼ)
                   j=1 to k

Weighted by cluster size
```

---

### **5.2.3 Properties and Theorems**

#### **Property 1: Range of Purity**

**Theorem:** 0 ≤ Purity ≤ 1

**Proof:**
```
For cluster Cⱼ:
Purity(Cⱼ) = (1/nⱼ) × max_i |Cⱼ ∩ Lᵢ|

Lower bound:
- min(max_i |Cⱼ ∩ Lᵢ|) = 1 (at least 1 point from some class)
- If cluster has nⱼ points: Purity ≥ 1/nⱼ
- For n → ∞: Purity → 0+ (but practically ≥ 1/n)

Upper bound:
- max(max_i |Cⱼ ∩ Lᵢ|) = nⱼ (all points from same class)
- Purity = nⱼ/nⱼ = 1

Therefore: 0 < Purity ≤ 1

For overall purity:
- Weighted average of individual purities
- Same bounds apply: 0 < Purity ≤ 1  ✓
```

**Special cases:**
```
Purity = 1: Perfect clustering (each cluster contains only one class)
Purity = 1/c: Random clustering (each cluster has equal proportion of all classes)
            where c = number of classes
```

---

#### **Property 2: Range of Entropy**

**Theorem:** 0 ≤ Entropy ≤ log₂(c) where c = number of classes

**Proof:**

**Lower bound (minimum entropy = 0):**
```
Entropy is minimized when cluster is pure (one class)

If all points in Cⱼ belong to class i:
pᵢⱼ = 1, all other pₖⱼ = 0 (k ≠ i)

Entropy(Cⱼ) = -[1 × log₂(1) + 0 × log₂(0) + ... + 0 × log₂(0)]
            = -[0 + 0 + ... + 0]
            = 0  ✓ (minimum)
```

**Upper bound (maximum entropy = log₂(c)):**
```
Entropy is maximized when all classes are equally represented

For c classes with equal proportions:
pᵢⱼ = 1/c for all i

Entropy(Cⱼ) = -Σ (1/c) × log₂(1/c)
               i=1 to c
            
            = -c × (1/c) × log₂(1/c)
            
            = -log₂(1/c)
            
            = -log₂(c⁻¹)
            
            = log₂(c)  ✓ (maximum)
```

**Example:**
```
For c = 2 classes:
- Min entropy = 0 (all points one class)
- Max entropy = log₂(2) = 1 bit (50-50 split)

For c = 4 classes:
- Min entropy = 0
- Max entropy = log₂(4) = 2 bits (25-25-25-25 split)
```

---

#### **Property 3: Relationship Between Purity and Entropy**

**Theorem:** Purity and Entropy are inversely related (but not perfectly)

```
High Purity → Low Entropy (but not always)
Low Purity → High Entropy (generally)
```

**Proof by example:**

**Case 1: Pure cluster**
```
Cluster: {A, A, A, A, A}
Purity = 5/5 = 1.0 (high)
Entropy = 0 (low)

Inverse relationship holds ✓
```

**Case 2: Completely mixed (2 classes)**
```
Cluster: {A, A, A, B, B, B}
Purity = 3/6 = 0.5 (low)
p_A = 3/6 = 0.5, p_B = 0.5
Entropy = -[0.5×log₂(0.5) + 0.5×log₂(0.5)]
        = -[0.5×(-1) + 0.5×(-1)]
        = 1.0 (high)

Inverse relationship holds ✓
```

**Case 3: Why not perfect inverse?**
```
Consider two clusters with same purity but different entropy:

Cluster 1: {A:6, B:4}
Purity = 6/10 = 0.6
Entropy = -[0.6×log₂(0.6) + 0.4×log₂(0.4)]
        ≈ 0.97

Cluster 2: {A:6, B:2, C:2}
Purity = 6/10 = 0.6 (same!)
Entropy = -[0.6×log₂(0.6) + 0.2×log₂(0.2) + 0.2×log₂(0.2)]
        ≈ 1.37 (higher!)

Same purity, different entropy!
Entropy is more sensitive to distribution across multiple classes.
```

---

#### **Property 4: Purity and Number of Clusters**

**Theorem:** Purity increases monotonically with number of clusters

```
As k → n: Purity → 1 (trivially)
```

**Proof:**
```
Extreme case: Each point is its own cluster
- Every cluster has exactly 1 point
- That point belongs to exactly 1 class
- Purity of each cluster = 1/1 = 1
- Overall purity = 1

This is a limitation of purity!
Cannot use purity alone to determine optimal k.
```

**Similarly for Entropy:**
```
As k → n: Entropy → 0

Each singleton cluster has entropy = 0
Overall entropy → 0

Also a limitation!
```

---

### **5.2.4 Normalized Measures**

To address the monotonicity issue, normalized versions exist:

**Normalized Mutual Information (NMI):**
```
NMI = I(Clusters; Classes) / [H(Clusters) + H(Classes)] / 2

Where I is mutual information, H is entropy
Range: [0, 1]
Optimal: 1
Does NOT increase trivially with k
```

**Adjusted Rand Index (ARI):**
```
Accounts for chance agreement
Penalizes random clustering
Can be negative (worse than random)
```

We'll discuss these in detail in later sections.

---

## **5.3 CONCEPT EXPLAINED**

### **5.3.1 Intuitive Understanding**

**Purity Analogy: Sorting Fruit**

```
You have 3 baskets (clusters) and 2 types of fruit (classes): Apples and Oranges

PURE BASKETS (High Purity):
Basket 1: 🍎🍎🍎🍎🍎 (100% apples)
Basket 2: 🍎🍎🍎🍎🍊 (80% apples)
Basket 3: 🍊🍊🍊🍊🍊 (100% oranges)

Overall Purity = (5 + 4 + 5) / 15 = 14/15 = 0.93 (excellent!)

IMPURE BASKETS (Low Purity):
Basket 1: 🍎🍊🍎🍊🍎 (60% apples)
Basket 2: 🍊🍎🍊🍎🍊 (60% oranges)
Basket 3: 🍎🍎🍊🍊🍊 (60% oranges)

Overall Purity = (3 + 3 + 3) / 15 = 9/15 = 0.60 (poor!)
```

---

**Entropy Analogy: Information Uncertainty**

```
You reach into a basket blindfolded. 
How uncertain are you about what fruit you'll pick?

HOMOGENEOUS BASKET:
All apples: 🍎🍎🍎🍎🍎
Uncertainty = 0 (you KNOW it's an apple)
Entropy = 0

PERFECTLY MIXED BASKET:
50-50: 🍎🍎🍊🍊
Uncertainty = maximum (could be either!)
Entropy = 1 bit

SLIGHTLY BIASED BASKET:
75-25: 🍎🍎🍎🍊
Uncertainty = moderate (probably apple, but might be orange)
Entropy ≈ 0.81 bits
```

---

### **5.3.2 Step-by-Step Calculation Example**

**Simple Example:**

```
Dataset: 6 documents
Classes (Ground Truth):
- Class A (Sports): {D1, D2, D3}
- Class B (Politics): {D4, D5, D6}

K-means clustering result (k=2):
- Cluster 1: {D1, D2, D4}
- Cluster 2: {D3, D5, D6}
```

---

#### **Calculate Purity:**

**Step 1: Create Confusion Matrix**

```
              Cluster 1   Cluster 2   Total
Class A          2           1          3
Class B          1           2          3
Total            3           3          6
```

**Step 2: Calculate Purity per Cluster**

**Cluster 1:**
```
Contains: 2 from Class A, 1 from Class B
n₁ = 3
Dominant class: A (2 points)

Purity(C₁) = max{2, 1} / 3
           = 2 / 3
           = 0.667
```

**Cluster 2:**
```
Contains: 1 from Class A, 2 from Class B
n₂ = 3
Dominant class: B (2 points)

Purity(C₂) = max{1, 2} / 3
           = 2 / 3
           = 0.667
```

**Step 3: Calculate Overall Purity**

```
Purity = (n₁/n) × Purity(C₁) + (n₂/n) × Purity(C₂)
       = (3/6) × 0.667 + (3/6) × 0.667
       = 0.5 × 0.667 + 0.5 × 0.667
       = 0.333 + 0.333
       = 0.667
```

**Alternative calculation:**
```
Purity = (1/n) × Σ max_i |Cⱼ ∩ Lᵢ|
       = (1/6) × [max{2,1} + max{1,2}]
       = (1/6) × [2 + 2]
       = 4/6
       = 0.667
```

---

#### **Calculate Entropy:**

**Step 1: Calculate proportions per cluster**

**Cluster 1:**
```
p₁₁ = |C₁ ∩ Class A| / n₁ = 2/3 = 0.667
p₁₂ = |C₁ ∩ Class B| / n₁ = 1/3 = 0.333
```

**Cluster 2:**
```
p₂₁ = |C₂ ∩ Class A| / n₂ = 1/3 = 0.333
p₂₂ = |C₂ ∩ Class B| / n₂ = 2/3 = 0.667
```

---

**Step 2: Calculate Entropy per Cluster**

**Cluster 1:**
```
Entropy(C₁) = -[p₁₁×log₂(p₁₁) + p₁₂×log₂(p₁₂)]
            = -[0.667×log₂(0.667) + 0.333×log₂(0.333)]
            = -[0.667×(-0.585) + 0.333×(-1.585)]
            = -[-0.390 + -0.528]
            = -[-0.918]
            = 0.918 bits
```

**Cluster 2:**
```
Entropy(C₂) = -[0.333×log₂(0.333) + 0.667×log₂(0.667)]
            = -[0.333×(-1.585) + 0.667×(-0.585)]
            = -[-0.528 + -0.390]
            = 0.918 bits
```

---

**Step 3: Calculate Overall Entropy**

```
Entropy = (n₁/n) × Entropy(C₁) + (n₂/n) × Entropy(C₂)
        = (3/6) × 0.918 + (3/6) × 0.918
        = 0.5 × 0.918 + 0.5 × 0.918
        = 0.459 + 0.459
        = 0.918 bits
```

---

**Summary:**
```
Purity = 0.667 (66.7%)
Entropy = 0.918 bits (out of max 1.0 for 2 classes)

Interpretation:
- Moderate purity (67% correct)
- High entropy (91.8% of maximum)
- Clustering captures some structure but not perfect
- Classes and clusters don't align well
```

---

### **5.3.3 Visual Interpretation: Confusion Matrix**

**Understanding through Confusion Matrix:**

```
Good Clustering (High Purity, Low Entropy):
              C1    C2    C3
Class A      [10]   1     0
Class B       0   [10]    1
Class C       1     0   [10]

Diagonal dominance!
Each cluster mostly contains one class
Purity ≈ 0.91
Entropy ≈ 0.3


Bad Clustering (Low Purity, High Entropy):
              C1    C2    C3
Class A       4     3     3
Class B       3     4     3
Class C       3     3     4

Uniform distribution!
Each cluster contains all classes equally
Purity ≈ 0.36
Entropy ≈ 1.58 (high)
```

---

## **5.4 METHOD: STEP-BY-STEP ALGORITHM**

### **ALGORITHM:**

```
INPUT:
  - Cluster assignments: C = {C₁, C₂, ..., Cₖ}
  - Ground truth class labels: L = {L₁, L₂, ..., Lc}
  - n = total number of points

OUTPUT:
  - Overall Purity
  - Overall Entropy
  - Per-cluster Purity and Entropy

ALGORITHM:

STEP 1: Build Confusion Matrix
    For each cluster j and class i:
        count[j][i] = |Cⱼ ∩ Lᵢ|
        (number of points in cluster j from class i)

STEP 2: Calculate Purity
    2.1: For each cluster j:
         nⱼ = total points in cluster j
         Purity(Cⱼ) = max_i(count[j][i]) / nⱼ
    
    2.2: Overall Purity:
         Purity = (1/n) × Σ max_i(count[j][i])
                          j=1 to k

STEP 3: Calculate Entropy
    3.1: For each cluster j:
         For each class i:
             pᵢⱼ = count[j][i] / nⱼ
         
         Entropy(Cⱼ) = -Σ pᵢⱼ × log₂(pᵢⱼ)
                        i=1 to c
                        (skip if pᵢⱼ = 0)
    
    3.2: Overall Entropy:
         Entropy = Σ (nⱼ/n) × Entropy(Cⱼ)
                   j=1 to k

STEP 4: Return Purity, Entropy, per-cluster values

COMPLEXITY:
Time: O(n + k×c)
  - Build confusion matrix: O(n)
  - Calculate purity: O(k×c)
  - Calculate entropy: O(k×c)
Space: O(k×c) for confusion matrix
```

---

### **DETAILED PSEUDOCODE:**

```python
FUNCTION calculate_purity_entropy(cluster_labels, true_labels):
    """
    Calculate purity and entropy for clustering evaluation
    
    Args:
        cluster_labels: array of cluster assignments [n]
        true_labels: array of ground truth class labels [n]
    
    Returns:
        overall_purity, overall_entropy, per_cluster_metrics
    """
    n = LENGTH(cluster_labels)
    k = NUMBER_OF_UNIQUE(cluster_labels)
    c = NUMBER_OF_UNIQUE(true_labels)
    
    # Step 1: Build confusion matrix
    confusion_matrix = ZEROS(k, c)
    
    FOR i IN 0 TO n-1:
        cluster_id = cluster_labels[i]
        class_id = true_labels[i]
        confusion_matrix[cluster_id][class_id] += 1
    
    # Step 2: Calculate Purity
    total_correct = 0
    purity_per_cluster = []
    
    FOR j IN 0 TO k-1:
        cluster_size = SUM(confusion_matrix[j, :])
        
        IF cluster_size == 0:
            purity_per_cluster.APPEND(0)
            CONTINUE
        
        # Find dominant class
        max_class_count = MAX(confusion_matrix[j, :])
        total_correct += max_class_count
        
        purity_j = max_class_count / cluster_size
        purity_per_cluster.APPEND(purity_j)
    
    overall_purity = total_correct / n
    
    # Step 3: Calculate Entropy
    entropy_per_cluster = []
    overall_entropy = 0
    
    FOR j IN 0 TO k-1:
        cluster_size = SUM(confusion_matrix[j, :])
        
        IF cluster_size == 0:
            entropy_per_cluster.APPEND(0)
            CONTINUE
        
        entropy_j = 0
        FOR i IN 0 TO c-1:
            IF confusion_matrix[j][i] > 0:
                p_ij = confusion_matrix[j][i] / cluster_size
                entropy_j -= p_ij × LOG2(p_ij)
        
        entropy_per_cluster.APPEND(entropy_j)
        
        # Weight by cluster size
        weight = cluster_size / n
        overall_entropy += weight × entropy_j
    
    RETURN overall_purity, overall_entropy, {
        'purity_per_cluster': purity_per_cluster,
        'entropy_per_cluster': entropy_per_cluster,
        'confusion_matrix': confusion_matrix
    }


FUNCTION interpret_purity(purity):
    """Interpret purity score"""
    IF purity >= 0.9:
        RETURN "Excellent"
    ELIF purity >= 0.7:
        RETURN "Good"
    ELIF purity >= 0.5:
        RETURN "Moderate"
    ELSE:
        RETURN "Poor"


FUNCTION interpret_entropy(entropy, num_classes):
    """Interpret entropy score"""
    max_entropy = LOG2(num_classes)
    normalized_entropy = entropy / max_entropy
    
    IF normalized_entropy <= 0.2:
        RETURN "Excellent (low disorder)"
    ELIF normalized_entropy <= 0.4:
        RETURN "Good"
    ELIF normalized_entropy <= 0.6:
        RETURN "Moderate"
    ELSE:
        RETURN "Poor (high disorder)"
```

---

## **5.5 DEMONSTRATE METHOD WITH EXAMPLES**

### **EXAMPLE 1: Complete Calculation (From Textbook Pattern)**

**Problem:**

A document clustering system produces the following results:

```
Documents: D1, D2, D3, D4, D5, D6, D7, D8, D9

Ground Truth Classes:
- Sports: {D1, D2, D3, D4}
- Politics: {D5, D6, D7}
- Technology: {D8, D9}

Clustering Result (k=3):
- Cluster 1: {D1, D2, D3, D5}
- Cluster 2: {D4, D6, D7}
- Cluster 3: {D8, D9}
```

Calculate:
(a) Confusion matrix
(b) Purity for each cluster and overall
(c) Entropy for each cluster and overall
(d) Interpret the results

---

#### **COMPLETE SOLUTION:**

**(a) Confusion Matrix**

**Step 1: Count memberships**

```
Cluster 1 {D1, D2, D3, D5}:
- Sports: D1, D2, D3 → 3 documents
- Politics: D5 → 1 document
- Technology: none → 0 documents

Cluster 2 {D4, D6, D7}:
- Sports: D4 → 1 document
- Politics: D6, D7 → 2 documents
- Technology: none → 0 documents

Cluster 3 {D8, D9}:
- Sports: none → 0 documents
- Politics: none → 0 documents
- Technology: D8, D9 → 2 documents
```

**Confusion Matrix:**

```
                Sports  Politics  Technology  Total
Cluster 1         3        1          0         4
Cluster 2         1        2          0         3
Cluster 3         0        0          2         2
Total             4        3          2         9
```

---

**(b) Calculate Purity**

**Cluster 1:**
```
n₁ = 4 documents
Class distribution: Sports=3, Politics=1, Technology=0
Dominant class: Sports (3 documents)

Purity(C₁) = 3/4 = 0.75
```

**Cluster 2:**
```
n₂ = 3 documents
Class distribution: Sports=1, Politics=2, Technology=0
Dominant class: Politics (2 documents)

Purity(C₂) = 2/3 = 0.667
```

**Cluster 3:**
```
n₃ = 2 documents
Class distribution: Sports=0, Politics=0, Technology=2
Dominant class: Technology (2 documents)

Purity(C₃) = 2/2 = 1.0
```

**Overall Purity:**
```
Method 1 (sum of dominant):
Purity = (1/n) × Σ max_i |Cⱼ ∩ Lᵢ|
       = (1/9) × [3 + 2 + 2]
       = 7/9
       = 0.778

Method 2 (weighted average):
Purity = (4/9)×0.75 + (3/9)×0.667 + (2/9)×1.0
       = 0.333 + 0.222 + 0.222
       = 0.778

Both methods give same result ✓
```

**ANSWER: Overall Purity = 0.778 (77.8%)**

---

**(c) Calculate Entropy**

**Cluster 1:**

```
Proportions:
p₁ (Sports) = 3/4 = 0.75
p₂ (Politics) = 1/4 = 0.25
p₃ (Technology) = 0/4 = 0

Entropy(C₁) = -[p₁×log₂(p₁) + p₂×log₂(p₂) + p₃×log₂(p₃)]
            = -[0.75×log₂(0.75) + 0.25×log₂(0.25) + 0×log₂(0)]

Calculate each term:
0.75×log₂(0.75) = 0.75×(-0.415) = -0.311
0.25×log₂(0.25) = 0.25×(-2.0) = -0.500
0×log₂(0) = 0 (by convention)

Entropy(C₁) = -[-0.311 + -0.500 + 0]
            = -[-0.811]
            = 0.811 bits
```

**Cluster 2:**

```
Proportions:
p₁ (Sports) = 1/3 = 0.333
p₂ (Politics) = 2/3 = 0.667
p₃ (Technology) = 0/3 = 0

Entropy(C₂) = -[0.333×log₂(0.333) + 0.667×log₂(0.667) + 0]

Calculate:
0.333×log₂(0.333) = 0.333×(-1.585) = -0.528
0.667×log₂(0.667) = 0.667×(-0.585) = -0.390

Entropy(C₂) = -[-0.528 + -0.390]
            = -[-0.918]
            = 0.918 bits
```

**Cluster 3:**

```
Proportions:
p₁ (Sports) = 0/2 = 0
p₂ (Politics) = 0/2 = 0
p₃ (Technology) = 2/2 = 1.0

Entropy(C₃) = -[0 + 0 + 1.0×log₂(1.0)]
            = -[1.0×0]
            = 0 bits

Perfect purity → zero entropy!
```

**Overall Entropy:**

```
Entropy = Σ (nⱼ/n) × Entropy(Cⱼ)
        = (4/9)×0.811 + (3/9)×0.918 + (2/9)×0
        = 0.444×0.811 + 0.333×0.918 + 0.222×0
        = 0.360 + 0.306 + 0
        = 0.666 bits
```

**ANSWER: Overall Entropy = 0.666 bits**

---

**Summary Table:**

| Cluster | Size | Dominant Class | Purity | Entropy (bits) | Quality |
|---------|------|----------------|--------|----------------|---------|
| C1 | 4 | Sports (3/4) | 0.750 | 0.811 | Good |
| C2 | 3 | Politics (2/3) | 0.667 | 0.918 | Moderate |
| C3 | 2 | Technology (2/2) | 1.000 | 0.000 | Perfect |
| **Overall** | **9** | - | **0.778** | **0.666** | **Good** |

---

**(d) Interpretation**

**Purity Analysis:**

```
Overall Purity = 0.778 (77.8%)

Interpretation:
✓ Good clustering quality (>75%)
✓ Most clusters dominated by one class
✓ 7 out of 9 documents correctly placed

Breakdown:
- Cluster 1: 75% pure (3 Sports, 1 Politics misplaced)
- Cluster 2: 67% pure (2 Politics, 1 Sports misplaced)
- Cluster 3: 100% pure (perfect Technology cluster)

Issues:
- D5 (Politics) misclassified into Sports cluster
- D4 (Sports) misclassified into Politics cluster
```

**Entropy Analysis:**

```
Overall Entropy = 0.666 bits (out of max log₂(3) = 1.585 bits)

Normalized entropy = 0.666 / 1.585 = 0.42 (42% of maximum)

Interpretation:
✓ Moderate-to-low disorder (42% of max)
✓ Clusters reasonably homogeneous
✓ 58% better than random assignment

Breakdown:
- Cluster 1: 0.811 bits (some mixing with Politics)
- Cluster 2: 0.918 bits (highest disorder - most mixed)
- Cluster 3: 0.000 bits (perfect - no disorder)

Cluster 3 is perfect (all Technology docs)
Cluster 2 has highest entropy (most mixed)
```

**Overall Assessment:**

```
Clustering Quality: GOOD

Strengths:
✓ High purity (77.8%)
✓ Technology documents perfectly clustered
✓ Most documents in dominant-class clusters
✓ Low-moderate entropy

Weaknesses:
✗ Cross-contamination between Sports and Politics
✗ Cluster 2 relatively impure (67%)
✗ 2 documents misclassified

Likely Reason for Confusion:
Sports and Politics documents may share vocabulary
(e.g., "Olympic politics", "sports legislation")
Leading to some overlap in feature space

Recommendation:
Good enough for many applications
Could improve with better features or different k
```

---

### **EXAMPLE 2: Perfect vs Random Clustering**

**Problem:**

Compare three different clusterings of 9 documents (3 classes, 3 docs each):

```
Ground Truth:
Class A: {D1, D2, D3}
Class B: {D4, D5, D6}
Class C: {D7, D8, D9}

Clustering 1 (Perfect):
Cluster 1: {D1, D2, D3}
Cluster 2: {D4, D5, D6}
Cluster 3: {D7, D8, D9}

Clustering 2 (Good):
Cluster 1: {D1, D2, D4}
Cluster 2: {D3, D5, D6}
Cluster 3: {D7, D8, D9}

Clustering 3 (Random):
Cluster 1: {D1, D4, D7}
Cluster 2: {D2, D5, D8}
Cluster 3: {D3, D6, D9}
```

Calculate purity and entropy for all three.

---

#### **COMPLETE SOLUTION:**

**CLUSTERING 1: Perfect Alignment**

**Confusion Matrix:**
```
           A  B  C
Cluster 1  3  0  0
Cluster 2  0  3  0
Cluster 3  0  0  3
```

**Purity:**
```
Each cluster: max{3,0,0}/3 = 3/3 = 1.0
Overall: (3+3+3)/9 = 9/9 = 1.0 (100%)
```

**Entropy:**
```
Each cluster: All points from one class
p = {1, 0, 0}
Entropy = -[1×log₂(1)] = 0

Overall: (1/3)×0 + (1/3)×0 + (1/3)×0 = 0 bits
```

**Result:**
```
Purity = 1.0 (perfect!)
Entropy = 0.0 (perfect!)
```

---

**CLUSTERING 2: Good But Not Perfect**

**Confusion Matrix:**
```
           A  B  C
Cluster 1  2  1  0
Cluster 2  1  2  0
Cluster 3  0  0  3
```

**Purity:**
```
Cluster 1: max{2,1,0}/3 = 2/3 = 0.667
Cluster 2: max{1,2,0}/3 = 2/3 = 0.667
Cluster 3: max{0,0,3}/3 = 3/3 = 1.0

Overall: (2+2+3)/9 = 7/9 = 0.778
```

**Entropy:**
```
Cluster 1: p = {2/3, 1/3, 0}
Entropy = -[0.667×(-0.585) + 0.333×(-1.585)]
        = 0.918 bits

Cluster 2: Same distribution
Entropy = 0.918 bits

Cluster 3: p = {0, 0, 1}
Entropy = 0 bits

Overall: (1/3)×0.918 + (1/3)×0.918 + (1/3)×0
       = 0.612 bits
```

**Result:**
```
Purity = 0.778 (good)
Entropy = 0.612 bits
```

---

**CLUSTERING 3: Random (One from Each Class)**

**Confusion Matrix:**
```
           A  B  C
Cluster 1  1  1  1
Cluster 2  1  1  1
Cluster 3  1  1  1
```

**Purity:**
```
Each cluster: max{1,1,1}/3 = 1/3 = 0.333
Overall: (1+1+1)/9 = 3/9 = 0.333
```

**Entropy:**
```
Each cluster: p = {1/3, 1/3, 1/3} (uniform!)
Entropy = -3×[1/3×log₂(1/3)]
        = -3×[1/3×(-1.585)]
        = 1.585 bits (maximum for 3 classes!)

Overall: (1/3)×1.585 + (1/3)×1.585 + (1/3)×1.585
       = 1.585 bits (maximum!)
```

**Result:**
```
Purity = 0.333 (worst possible!)
Entropy = 1.585 bits (worst possible!)
```

---

**COMPARISON TABLE:**

| Clustering | Purity | Entropy | Quality |
|------------|--------|---------|---------|
| Perfect | 1.000 (100%) | 0.000 bits | Perfect ⭐⭐⭐ |
| Good | 0.778 (78%) | 0.612 bits | Good ⭐⭐ |
| Random | 0.333 (33%) | 1.585 bits | Worst ⭐ |

**Key Observations:**

```
1. Perfect clustering:
   - Purity = 1.0 (all clusters 100% pure)
   - Entropy = 0 (no uncertainty)
   
2. Good clustering:
   - Purity = 0.778 (most points correctly placed)
   - Entropy = 0.612 (moderate disorder)
   - 2 misclassifications out of 9

3. Random clustering:
   - Purity = 0.333 (1/c for c classes)
   - Entropy = log₂(c) = 1.585 (maximum possible)
   - Worst case: uniform distribution in each cluster

The metrics correctly distinguish quality levels!
```

---
# **METHOD 5: ENTROPY AND PURITY - CONTINUED**

---

## **5.6 HOW IS PURITY/ENTROPY AFFECTED BY VARIOUS FACTORS?**

### **FACTOR 1: Number of Clusters (k)**

#### **Effect:**
```
As k increases:
- Purity increases monotonically → 1.0
- Entropy decreases monotonically → 0
- Both become trivially perfect at k=n
```

#### **Mathematical Explanation:**

```
As k increases, clusters become smaller and purer.

Extreme case (k = n):
- Each point is its own cluster
- Each "cluster" has 1 point from 1 class
- Purity = 1.0 (trivial perfection)
- Entropy = 0 (trivial perfection)

This is the MAJOR LIMITATION of purity and entropy!
Cannot use them alone to determine optimal k.
```

---

#### **Detailed Example:**

**Dataset:** 9 documents, 3 classes (3 each)
```
Class A: {D1, D2, D3}
Class B: {D4, D5, D6}
Class C: {D7, D8, D9}
```

---

**k=1: Single cluster (worst)**

```
Cluster 1: {D1, D2, D3, D4, D5, D6, D7, D8, D9}

Confusion Matrix:
           A  B  C
Cluster 1  3  3  3

Purity = max{3,3,3}/9 = 3/9 = 0.333 (minimum for 3 classes)

Entropy = -3×[3/9×log₂(3/9)]
        = -3×[0.333×(-1.585)]
        = 1.585 bits (maximum for 3 classes)

Quality: WORST (no separation)
```

---

**k=3: Natural clustering**

```
Cluster 1: {D1, D2, D4}  (2 from A, 1 from B)
Cluster 2: {D3, D5, D6}  (1 from A, 2 from B)
Cluster 3: {D7, D8, D9}  (3 from C)

Purity = (2+2+3)/9 = 7/9 = 0.778

Entropy ≈ 0.612 bits

Quality: GOOD (reasonable separation)
```

---

**k=6: Over-clustering**

```
Cluster 1: {D1, D2}      (2 from A)
Cluster 2: {D3}          (1 from A)
Cluster 3: {D4, D5}      (2 from B)
Cluster 4: {D6}          (1 from B)
Cluster 5: {D7, D8}      (2 from C)
Cluster 6: {D9}          (1 from C)

Purity = (2+1+2+1+2+1)/9 = 9/9 = 1.0 ✓

Entropy:
- Clusters 1,3,5: 100% pure → entropy = 0
- Clusters 2,4,6: singletons → entropy = 0
Overall entropy = 0 ✓

Quality: TRIVIALLY PERFECT (but meaningless!)
```

---

**k=9: Every point its own cluster**

```
9 singleton clusters, each 100% pure

Purity = 9/9 = 1.0 (trivially perfect)
Entropy = 0 (trivially perfect)

But this clustering is useless!
No grouping, no insights.
```

---

**Summary Plot:**

```
Purity
    │
1.0 │                    •────•────• (k≥6)
    │                  ╱
    │                ╱
0.78│              •           (k=3) ← Meaningful optimum
    │            ╱
    │          ╱
    │        ╱
0.33│•                        (k=1)
    └────────────────────────────── k
    1   2   3   4   5   6   7   8   9

Entropy
    │
1.58│•                        (k=1)
    │ ╲
    │  ╲
    │   ╲
0.61│    ╲
    │     •               (k=3) ← Meaningful optimum
    │      ╲___
    │          ╲___
0.0 │              •────•────• (k≥6)
    └────────────────────────────── k
    1   2   3   4   5   6   7   8   9
```

**Key Insight:**

```
Purity and entropy CANNOT determine optimal k alone!
They always improve with more clusters.

Must use additional constraints:
1. Set maximum k (domain knowledge)
2. Use normalized measures (NMI, ARI)
3. Combine with internal measures (silhouette, WSS)
4. Apply minimum cluster size constraint
5. Use statistical significance tests

Best practice:
- Use purity/entropy to COMPARE clusterings with same k
- Use other methods to SELECT optimal k
- Then validate chosen k with purity/entropy
```

---

### **FACTOR 2: Class Imbalance**

#### **Effect:**
```
Balanced classes → Purity/Entropy reflect true quality
Imbalanced classes → Purity can be artificially high
```

#### **Example:**

**Balanced Classes:**
```
Dataset: 90 documents
Class A: 30 docs
Class B: 30 docs
Class C: 30 docs

Clustering (k=3):
Cluster 1: 25 from A, 5 from B → Purity = 25/30 = 0.833
Cluster 2: 5 from A, 25 from B → Purity = 25/30 = 0.833
Cluster 3: 30 from C → Purity = 30/30 = 1.0

Overall Purity = (25+25+30)/90 = 80/90 = 0.889
Good performance! ✓
```

**Imbalanced Classes:**
```
Dataset: 90 documents
Class A: 70 docs (dominant)
Class B: 10 docs
Class C: 10 docs

Bad clustering (puts most in one cluster):
Cluster 1: 70 from A, 5 from B, 5 from C → Purity = 70/80 = 0.875
Cluster 2: 5 from B → Purity = 5/5 = 1.0
Cluster 3: 5 from C → Purity = 5/5 = 1.0

Overall Purity = (70+5+5)/90 = 80/90 = 0.889

Same purity as balanced case!
But this clustering is much worse (just grouped majority class)
```

---

**Problem:**

```
With imbalanced classes, high purity can be achieved by:
1. Creating one large cluster of majority class
2. Creating small clusters for minority classes

This looks good by purity metric but doesn't reflect true quality!

Example:
Dataset: 100 points
Class A: 95 points
Class B: 5 points

Trivial clustering:
Cluster 1: All 95 from A → Purity = 95/95 = 1.0
Cluster 2: All 5 from B → Purity = 5/5 = 1.0

Overall Purity = 100/100 = 1.0 (perfect!)

But this just separated majority from minority!
Didn't discover any structure within Class A.
```

---

**Mitigation:**

```
1. Use balanced accuracy:
   Average purity per class (not weighted by size)
   
2. Use F-measure (precision + recall):
   Accounts for both true positives and false positives
   
3. Use Normalized Mutual Information (NMI):
   Accounts for class distribution
   
4. Use Adjusted Rand Index (ARI):
   Corrects for chance agreement
   
5. Stratified evaluation:
   Report purity for each class separately
```

---

### **FACTOR 3: Number of Classes (c)**

#### **Effect:**
```
More classes → Lower baseline purity
More classes → Higher maximum entropy
Must normalize entropy by log₂(c)
```

#### **Example:**

**2 Classes:**
```
Random clustering (50-50 split in each cluster):
Purity = 0.5
Max Entropy = log₂(2) = 1.0 bit

Actual entropy = 1.0 bit
Normalized = 1.0/1.0 = 100% of max (worst)
```

**10 Classes:**
```
Random clustering (10% each class in each cluster):
Purity = 0.1 (much lower!)
Max Entropy = log₂(10) = 3.32 bits

Actual entropy = 3.32 bits
Normalized = 3.32/3.32 = 100% of max (worst)

But absolute entropy is 3.3× higher!
```

---

**Normalized Entropy:**

```
Entropy_normalized = Entropy_actual / log₂(c)

This puts entropy on [0, 1] scale regardless of c:
- 0 = perfect (no disorder)
- 1 = worst (uniform distribution)

Comparable across different numbers of classes!
```

---

**Comparison:**

```
Scenario A: 2 classes, entropy = 0.5 bits
Normalized = 0.5/1.0 = 0.5 (50% of max)

Scenario B: 8 classes, entropy = 1.5 bits
Normalized = 1.5/3.0 = 0.5 (50% of max)

Both have same relative disorder (50%)
Even though absolute entropies differ (0.5 vs 1.5)

Use normalized entropy to compare across different c!
```

---

### **FACTOR 4: Cluster Size Variation**

#### **Effect:**
```
Imbalanced cluster sizes → Purity/Entropy weighted by large clusters
Can hide problems in small clusters
```

#### **Example:**

**Balanced Clusters:**
```
Cluster 1: 30 docs, purity = 0.8
Cluster 2: 30 docs, purity = 0.8
Cluster 3: 30 docs, purity = 0.8

Overall purity = 0.8 (clear interpretation)
```

**Imbalanced Clusters:**
```
Cluster 1: 85 docs, purity = 0.9
Cluster 2: 10 docs, purity = 0.4  ← Poor!
Cluster 3: 5 docs, purity = 0.2   ← Very poor!

Overall purity = (85×0.9 + 10×0.4 + 5×0.2) / 100
                = (76.5 + 4 + 1) / 100
                = 81.5 / 100
                = 0.815

Looks good overall (0.815)
But Clusters 2 and 3 are terrible!
Dominated by large Cluster 1.
```

---

**Mitigation:**

```
1. Report per-cluster metrics:
   Show purity/entropy for each cluster
   Identify problematic clusters
   
2. Use unweighted average:
   Average_purity = mean(purity of each cluster)
   Treats all clusters equally
   
3. Set minimum cluster size:
   Ignore or penalize very small clusters
   
4. Visual inspection:
   Heatmap of confusion matrix
   Identify patterns and problems
```

---

### **FACTOR 5: Misclassification Pattern**

#### **Effect:**
```
Concentrated errors vs distributed errors
Same overall purity, different error patterns
```

#### **Example:**

**Pattern A: Errors Concentrated in One Cluster**
```
Cluster 1: 27 from A, 3 from B → Purity = 0.9
Cluster 2: 30 from B → Purity = 1.0
Cluster 3: 30 from C → Purity = 1.0

Overall: (27+30+30)/90 = 0.967
Error: 3 misclassifications, all in Cluster 1
```

**Pattern B: Errors Distributed Across Clusters**
```
Cluster 1: 29 from A, 1 from B → Purity = 0.967
Cluster 2: 29 from B, 1 from C → Purity = 0.967
Cluster 3: 29 from C, 1 from A → Purity = 0.967

Overall: (29+29+29)/90 = 0.967
Error: 3 misclassifications, spread across clusters
```

**Comparison:**
```
Same overall purity (0.967)
But different error patterns:

Pattern A: One cluster has all errors
- Easier to debug (problem isolated)
- May indicate specific issue with one cluster
- Other clusters perfect

Pattern B: Errors distributed
- Harder to identify problem
- Suggests systematic issue
- More balanced but harder to fix

Purity alone doesn't distinguish these patterns!
Need to look at confusion matrix.
```

---

### **FACTOR 6: Ground Truth Quality**

#### **Effect:**
```
Noisy labels → Lower purity/entropy even for good clustering
Subjective classes → Harder to achieve high purity
Well-defined classes → Easier to achieve high purity
```

#### **Example:**

**Well-Defined Classes:**
```
Task: Cluster animals
Classes: {Mammals, Birds, Fish}

These are clearly distinct!
Good clustering should achieve high purity (>0.9)
```

**Fuzzy Classes:**
```
Task: Cluster news articles
Classes: {Sports, Business, Politics}

Overlap exists:
- "Olympic economics" (Sports or Business?)
- "Sports legislation" (Sports or Politics?)
- "Corporate lobbying" (Business or Politics?)

Good clustering might only achieve purity 0.7-0.8
Not because clustering is bad, but because classes overlap!
```

**Noisy Labels:**
```
Dataset has labeling errors: 10% wrong labels

Even perfect clustering will have:
Purity ≤ 0.9 (limited by label noise)

Example:
Ground truth says: Document D5 is "Sports"
Actually it's: "Politics" (labeling error)

Clustering correctly puts D5 with Politics docs
But this looks like an error according to noisy labels!
Purity decreases even though clustering is correct.
```

---

**Implications:**

```
1. Purity/Entropy are upper-bounded by label quality
   
2. In domains with fuzzy boundaries:
   - Don't expect purity > 0.8
   - Lower thresholds for "good" performance
   
3. With noisy labels:
   - Purity/Entropy underestimate true quality
   - Consider label cleaning
   - Use multiple labelers for consensus
   
4. Always consider domain characteristics:
   - Well-separated: purity > 0.9 expected
   - Some overlap: purity 0.7-0.8 acceptable
   - Highly subjective: purity 0.6-0.7 may be good
```

---

### **FACTOR 7: Clustering Algorithm Choice**

#### **Effect:**
```
Different algorithms produce different purity/entropy
Even with same k and same data
```

#### **Example:**

**Dataset:** Text documents with 3 classes

---

**K-means Clustering:**
```
Assumes spherical clusters
Works well if classes form compact groups in feature space

Result:
Purity = 0.75
Entropy = 0.65 bits

Good for well-separated, compact classes
```

**Hierarchical Clustering (Single Linkage):**
```
Creates chain-like clusters
Can capture elongated structures

Result:
Purity = 0.68
Entropy = 0.78 bits

Worse than K-means for this data
Single linkage prone to chaining
```

**DBSCAN:**
```
Density-based, finds arbitrary shapes
Marks outliers as noise (not in any cluster)

Result:
Purity = 0.82 (higher!)
Entropy = 0.52 bits (lower!)

But: 15% of points marked as noise (excluded from clusters)

Higher purity because:
- Outliers removed (not counted)
- Remaining points in denser, purer regions
- But at cost of not clustering everything
```

---

**Key Insight:**

```
Algorithm affects purity/entropy through:

1. Cluster shape assumptions:
   - K-means: spherical → good for compact classes
   - DBSCAN: arbitrary → good for complex shapes
   
2. Outlier handling:
   - K-means: assigns all points → lower purity
   - DBSCAN: marks noise → higher purity (but incomplete)
   
3. Number of clusters:
   - Hierarchical: can produce unbalanced k
   - K-means: always produces exactly k clusters
   
4. Initialization:
   - K-means: sensitive to initialization
   - Hierarchical: deterministic
   
When comparing algorithms:
- Use same k
- Use same data
- Consider outlier handling
- Report what % of data is clustered
```

---

### **FACTOR 8: Feature Representation**

#### **Effect:**
```
Different features → Different cluster boundaries → Different purity
Feature engineering greatly impacts external measures
```

#### **Example:**

**Text Classification:**

**Feature Set A: Bag of Words (unigrams)**
```
Features: {word1, word2, word3, ...}
No word order, no context

Clustering result:
Purity = 0.65
Entropy = 0.85 bits

Problem: Similar words appear in multiple classes
Hard to separate based on individual words
```

**Feature Set B: TF-IDF weighted unigrams**
```
Features: {TF-IDF(word1), TF-IDF(word2), ...}
Weights words by discriminative power

Clustering result:
Purity = 0.78
Entropy = 0.68 bits

Better: Class-specific words weighted higher
Easier to separate classes
```

**Feature Set C: Word embeddings (Word2Vec)**
```
Features: {semantic_dim1, semantic_dim2, ..., semantic_dim300}
Captures semantic meaning

Clustering result:
Purity = 0.85
Entropy = 0.52 bits

Best: Semantically similar docs cluster together
Aligns better with human-defined classes
```

---

**Key Insight:**

```
Purity/Entropy measure:
1. Quality of clustering algorithm
2. Quality of features
3. Alignment of features with ground truth labels

Low purity could mean:
- Bad clustering algorithm
- Bad features
- Classes not separable in feature space

High purity requires:
- Good algorithm
- Good features
- Classes separable in feature space
```

---

### **SUMMARY TABLE: FACTORS AFFECTING PURITY/ENTROPY**

| Factor | Effect on Purity | Effect on Entropy | Severity | Mitigation |
|--------|-----------------|-------------------|----------|------------|
| **Increasing k** | ↑ Monotonic (always) | ↓ Monotonic (always) | --- | Cannot use alone to select k; combine with other methods |
| **Class imbalance** | Can be artificially high | Can be misleading | -- | Use F-measure, NMI, per-class metrics |
| **More classes (c)** | Lower baseline | Higher absolute value | -- | Use normalized entropy = E/log₂(c) |
| **Cluster size variation** | Weighted by large clusters | Weighted by large clusters | -- | Report per-cluster metrics |
| **Error concentration** | Same overall, different pattern | Same overall, different pattern | - | Inspect confusion matrix |
| **Noisy ground truth** | ↓ Upper bound limited | ↑ Lower bound raised | --- | Clean labels, consensus labeling |
| **Fuzzy classes** | ↓ Lower achievable max | ↑ Higher achievable min | -- | Adjust expectations per domain |
| **Algorithm choice** | Varies significantly | Varies significantly | --- | Compare multiple algorithms |
| **Feature quality** | ↑ with better features | ↓ with better features | --- | Feature engineering, embeddings |
| **Outlier handling** | ↑ if removed | ↓ if removed | - | Report % clustered |

---

## **5.7 COMPARISON WITH OTHER CLUSTER VALIDATION METHODS**

### **5.7.1 PURITY/ENTROPY vs SILHOUETTE**

| Aspect | Purity/Entropy | Silhouette |
|--------|----------------|-----------|
| **Type** | External (needs labels) | Internal (no labels) |
| **What it measures** | Agreement with ground truth | Geometric quality |
| **Can validate correctness** | Yes ✓ | No |
| **Can determine optimal k** | No | Yes (has maximum) |
| **Ground truth required** | Yes | No |
| **Per-point scores** | No | Yes |
| **Outlier detection** | No | Yes (negative s) |
| **Monotonic with k** | Yes (limitation) | No |
| **Interpretability** | Very intuitive | Intuitive |
| **Best for** | Supervised evaluation | Unsupervised optimization |

---

#### **When They Agree:**

**Example: Good Clustering**
```
Data: {1,2,3,10,11,12}
Classes: {A,A,A,B,B,B}
Clustering: {1,2,3} vs {10,11,12}

Purity: 1.0 (perfect)
Entropy: 0 (perfect)
→ Perfect match with ground truth ✓

Silhouette: 0.87 (excellent)
→ Geometrically well-separated ✓

Both agree: Excellent clustering!
```

---

#### **When They Disagree:**

**Example 1: Good Internal, Poor External**
```
Data: 2D points
Classes: By color (arbitrary, not related to position)
  Red: scattered across space
  Blue: scattered across space

K-means finds geometric clusters:
  Left region vs Right region

Silhouette: 0.85 (excellent geometric separation)
Purity: 0.52 (poor match with color classes)

Disagreement because:
- Clusters are geometrically good
- But don't align with arbitrary color labels
- Classes not separable in feature space
```

**Example 2: Poor Internal, Good External**
```
Data: Overlapping clusters
Classes: Two classes with significant overlap
Clustering: Somehow matches classes despite overlap

Silhouette: 0.35 (poor geometric separation)
Purity: 0.88 (good match with classes)

Disagreement because:
- Geometric quality poor (overlapping)
- But happened to match class boundaries
- Lucky alignment or prior knowledge used
```

---

#### **Complementary Use:**

```
Best practice: Use BOTH!

Internal measures (Silhouette):
1. Find geometrically optimal k
2. Assess cluster cohesion/separation
3. Identify outliers

External measures (Purity/Entropy):
4. Validate against ground truth
5. Assess if clustering is "correct"
6. Compare to baseline

Workflow:
Step 1: Use Silhouette to find k* (geometric optimum)
Step 2: Use Purity to validate k* (matches classes?)
Step 3: If disagreement, investigate why:
        - Are classes geometrically separable?
        - Is feature space appropriate?
        - Are labels correct?
```

---

### **5.7.2 PURITY/ENTROPY vs F-MEASURE**

**F-Measure (External):**
```
Treats clustering as classification problem
Precision: How many pairs correctly clustered?
Recall: How many same-class pairs found?

F-measure = 2 × (Precision × Recall) / (Precision + Recall)
```

| Aspect | Purity | Entropy | F-Measure |
|--------|--------|---------|-----------|
| **Considers** | Dominant class only | All classes | All pairs |
| **Precision-focus** | Yes | No | Yes |
| **Recall-focus** | No | No | Yes |
| **Balanced** | No | Somewhat | Yes ✓ |
| **Sensitive to k** | Yes (monotonic) | Yes (monotonic) | Less |
| **Interpretability** | Very easy | Moderate | Easy |
| **Computation** | O(n) | O(n·c) | O(n²) |

---

#### **Example:**

**Clustering:**
```
Ground truth: Class A={1,2,3,4,5}, Class B={6,7,8,9,10}
Result: Cluster 1={1,2,3,6}, Cluster 2={4,5,7,8,9,10}
```

**Purity:**
```
Cluster 1: max{3,1}/4 = 0.75
Cluster 2: max{2,4}/6 = 0.67
Overall: (3+4)/10 = 0.70
```

**Entropy:**
```
Cluster 1: H = 0.81 bits
Cluster 2: H = 0.92 bits
Overall: 0.87 bits
```

**F-Measure:**
```
Precision: Of pairs in same cluster, how many from same class?
  Cluster 1: 3 choose 2 = 3 same-class pairs out of 4 choose 2 = 6 total
  Cluster 2: 4 choose 2 = 6 same-class pairs out of 6 choose 2 = 15 total
  Precision = 9/21 = 0.43

Recall: Of same-class pairs, how many in same cluster?
  Class A: 3 pairs in C1, 1 pair in C2 = 4 out of 5 choose 2 = 10
  Class B: 0 pairs in C1, 6 pairs in C2 = 6 out of 5 choose 2 = 10
  Recall = 10/20 = 0.50

F-measure = 2×(0.43×0.50)/(0.43+0.50) = 0.46
```

**Comparison:**
```
Purity: 0.70 (moderate)
F-measure: 0.46 (lower, more critical)

F-measure penalizes:
- Splitting Class A (low recall for A)
- Mixing classes in clusters (low precision)

More balanced assessment!
```

---

### **5.7.3 PURITY/ENTROPY vs RAND INDEX**

**Rand Index:**
```
Measures agreement between clusterings as:
RI = (a + b) / (a + b + c + d)

Where:
a = pairs same in both (TP)
b = pairs different in both (TN)
c = pairs same in clustering, different in truth (FP)
d = pairs different in clustering, same in truth (FN)

Range: [0, 1], but doesn't correct for chance
```

**Adjusted Rand Index (ARI):**
```
ARI = (RI - Expected_RI) / (max_RI - Expected_RI)

Range: [-1, 1]
  -1: worst (complete disagreement)
   0: random clustering
  +1: perfect agreement

Corrects for chance agreement!
```

| Aspect | Purity | ARI |
|--------|--------|-----|
| **Corrects for chance** | No | Yes ✓ |
| **Handles imbalance** | Poor | Good ✓ |
| **Monotonic with k** | Yes | No ✓ |
| **Interpretability** | Easy | Moderate |
| **Baseline** | 1/c | 0 |
| **Can be negative** | No | Yes (worse than random) |
| **Recommended** | For simple cases | For rigorous evaluation |

---

#### **Example:**

```
Dataset: 12 points, 3 classes (4 each)
Ground truth: {A,A,A,A, B,B,B,B, C,C,C,C}

Clustering 1 (Good):
Result: {A,A,A,B} {B,B,B,C} {C,C,C,A}

Purity = (3+3+3)/12 = 0.75
ARI = 0.61 (good, well above random)

Clustering 2 (Random-like):
Result: {A,B,C,A} {B,C,A,B} {C,A,B,C}

Purity = (1+1+1)/12 = 0.25 (but this is random for 3 classes!)
ARI = -0.03 (negative! worse than random)

Clustering 3 (Trivial k=12):
Result: Each point its own cluster

Purity = 1.0 (trivially perfect)
ARI = 0.0 (no better than random for k=n)

ARI correctly identifies trivial clustering as meaningless!
Purity is fooled.
```

---

### **5.7.4 PURITY/ENTROPY vs NORMALIZED MUTUAL INFORMATION (NMI)**

**Normalized Mutual Information:**
```
NMI = 2 × I(Clusters; Classes) / [H(Clusters) + H(Classes)]

Where:
I = Mutual Information (shared information)
H = Entropy

Range: [0, 1]
  0: No mutual information
  1: Perfect agreement

Does NOT increase trivially with k!
```

| Aspect | Entropy | NMI |
|--------|---------|-----|
| **Raw vs normalized** | Raw bits | Normalized [0,1] |
| **Monotonic with k** | Yes ↓ | No ✓ |
| **Information theoretic** | Yes | Yes |
| **Comparable across c** | With normalization | Yes ✓ |
| **Optimal k detection** | No | Yes ✓ |
| **Complexity** | O(n) | O(n·k·c) |
| **Standard in literature** | Less | More ✓ |

---

#### **Relationship:**

```
Entropy measures disorder within clusters
NMI measures shared information between clusterings

Low entropy → clusters homogeneous
High NMI → clusters align with classes

NMI uses entropy in its calculation:
- Entropy of cluster distribution
- Entropy of class distribution  
- Mutual information between them

NMI is more robust and recommended for serious evaluation!
```

---

### **5.7.5 COMPREHENSIVE COMPARISON TABLE**

| Metric | Type | Needs Labels | Handles k Selection | Corrects Chance | Range | Best For |
|--------|------|--------------|-------------------|----------------|-------|----------|
| **Purity** | External | Yes | No (monotonic) | No | [0,1] | Simple validation |
| **Entropy** | External | Yes | No (monotonic) | No | [0, log c] | Disorder measurement |
| **F-measure** | External | Yes | Somewhat | No | [0,1] | Balanced precision/recall |
| **Rand Index** | External | Yes | No | No | [0,1] | Simple agreement |
| **ARI** | External | Yes | Yes | Yes ✓ | [-1,1] | Rigorous evaluation |
| **NMI** | External | Yes | Yes | Yes ✓ | [0,1] | Standard evaluation |
| **Silhouette** | Internal | No | Yes ✓ | N/A | [-1,1] | Geometric quality |
| **WSS/BSS** | Internal | No | With elbow | N/A | Various | Variance decomposition |

---

### **5.7.6 RECOMMENDATION MATRIX**

| Scenario | Primary Metric | Secondary | Third |
|----------|---------------|-----------|-------|
| **No ground truth** | Silhouette | WSS/BSS | CH Index |
| **Have ground truth, simple** | Purity | Entropy | - |
| **Have ground truth, rigorous** | NMI | ARI | F-measure |
| **Imbalanced classes** | ARI | NMI | F-measure |
| **Need to select k** | Silhouette + NMI | ARI | Gap Statistic |
| **Academic publication** | NMI | ARI | Silhouette |
| **Industry report** | Purity | Silhouette | Confusion matrix |
| **Comparing algorithms** | NMI + Silhouette | ARI | F-measure |

---

## **5.8 PYQ QUESTIONS AND DETAILED SOLUTIONS**

### **PYQ QUESTION 1: Basic Purity and Entropy Calculation (8 marks)**

**Question:**

A text clustering system produces the following results for 12 documents:

```
Ground Truth Classes:
- Sports: {D1, D2, D3, D4}
- Politics: {D5, D6, D7, D8}
- Technology: {D9, D10, D11, D12}

Clustering Result (k=3):
- Cluster 1: {D1, D2, D3, D5}
- Cluster 2: {D4, D6, D7, D8, D9}
- Cluster 3: {D10, D11, D12}
```

(a) Construct the confusion matrix [2 marks]
(b) Calculate the purity of each cluster and overall purity [3 marks]
(c) Calculate the entropy of each cluster and overall entropy [3 marks]

---

#### **COMPLETE SOLUTION:**

**(a) Confusion Matrix [2 marks]**

**Step 1: Count memberships for each cluster-class pair**

**Cluster 1: {D1, D2, D3, D5}**
```
Sports: D1, D2, D3 → 3 documents
Politics: D5 → 1 document
Technology: none → 0 documents
```

**Cluster 2: {D4, D6, D7, D8, D9}**
```
Sports: D4 → 1 document
Politics: D6, D7, D8 → 3 documents
Technology: D9 → 1 document
```

**Cluster 3: {D10, D11, D12}**
```
Sports: none → 0 documents
Politics: none → 0 documents
Technology: D10, D11, D12 → 3 documents
```

---

**Confusion Matrix:**

```
                Sports  Politics  Technology  Total
Cluster 1         3        1          0         4
Cluster 2         1        3          1         5
Cluster 3         0        0          3         3
Total             4        4          4        12
```

[2 marks: 1 for structure, 1 for correct counts]

---

**(b) Calculate Purity [3 marks]**

**Cluster 1:**
```
n₁ = 4 documents
Class counts: Sports=3, Politics=1, Technology=0
Dominant class: Sports (3 documents)

Purity(C₁) = max{3, 1, 0} / 4
           = 3 / 4
           = 0.75
```

**Cluster 2:**
```
n₂ = 5 documents
Class counts: Sports=1, Politics=3, Technology=1
Dominant class: Politics (3 documents)

Purity(C₂) = max{1, 3, 1} / 5
           = 3 / 5
           = 0.60
```

**Cluster 3:**
```
n₃ = 3 documents
Class counts: Sports=0, Politics=0, Technology=3
Dominant class: Technology (3 documents)

Purity(C₃) = max{0, 0, 3} / 3
           = 3 / 3
           = 1.00
```

---

**Overall Purity:**

**Method 1: Sum of dominant counts**
```
Purity = (1/n) × Σ max_i |Cⱼ ∩ Lᵢ|
       = (1/12) × [3 + 3 + 3]
       = 9/12
       = 0.75
```

**Method 2: Weighted average**
```
Purity = Σ (nⱼ/n) × Purity(Cⱼ)
       = (4/12)×0.75 + (5/12)×0.60 + (3/12)×1.00
       = 0.333×0.75 + 0.417×0.60 + 0.250×1.00
       = 0.250 + 0.250 + 0.250
       = 0.75
```

**ANSWER: Overall Purity = 0.75 (75%)**

**Summary:**
| Cluster | Size | Dominant Class | Purity |
|---------|------|----------------|--------|
| C1 | 4 | Sports (3/4) | 0.75 |
| C2 | 5 | Politics (3/5) | 0.60 |
| C3 | 3 | Technology (3/3) | 1.00 |
| **Overall** | **12** | - | **0.75** |

[3 marks: 1 for per-cluster purities, 1 for overall calculation, 1 for final answer]

---

**(c) Calculate Entropy [3 marks]**

**Cluster 1:**

```
Proportions:
p(Sports) = 3/4 = 0.75
p(Politics) = 1/4 = 0.25
p(Technology) = 0/4 = 0

Entropy(C₁) = -Σ p × log₂(p)
            = -[0.75×log₂(0.75) + 0.25×log₂(0.25) + 0×log₂(0)]

Calculate each term:
0.75 × log₂(0.75) = 0.75 × (-0.415) = -0.311
0.25 × log₂(0.25) = 0.25 × (-2.0) = -0.500
0 × log₂(0) = 0 (by convention)

Entropy(C₁) = -[-0.311 + (-0.500) + 0]
            = -[-0.811]
            = 0.811 bits
```

---

**Cluster 2:**

```
Proportions:
p(Sports) = 1/5 = 0.20
p(Politics) = 3/5 = 0.60
p(Technology) = 1/5 = 0.20

Entropy(C₂) = -[0.20×log₂(0.20) + 0.60×log₂(0.60) + 0.20×log₂(0.20)]

Calculate:
0.20 × log₂(0.20) = 0.20 × (-2.322) = -0.464
0.60 × log₂(0.60) = 0.60 × (-0.737) = -0.442
0.20 × log₂(0.20) = 0.20 × (-2.322) = -0.464

Entropy(C₂) = -[-0.464 + (-0.442) + (-0.464)]
            = -[-1.370]
            = 1.370 bits
```

---

**Cluster 3:**

```
Proportions:
p(Sports) = 0/3 = 0
p(Politics) = 0/3 = 0
p(Technology) = 3/3 = 1.0

Entropy(C₃) = -[0 + 0 + 1.0×log₂(1.0)]
            = -[1.0 × 0]
            = 0 bits

Perfect purity → zero entropy!
```

---

**Overall Entropy:**

```
Entropy = Σ (nⱼ/n) × Entropy(Cⱼ)
        = (4/12)×0.811 + (5/12)×1.370 + (3/12)×0
        = 0.333×0.811 + 0.417×1.370 + 0.250×0
        = 0.270 + 0.571 + 0
        = 0.841 bits
```

**Maximum possible entropy for 3 classes:**
```
Max entropy = log₂(3) = 1.585 bits
Normalized entropy = 0.841 / 1.585 = 0.53 (53% of maximum)
```

**ANSWER: Overall Entropy = 0.841 bits**

**Summary:**
| Cluster | Size | Entropy (bits) | Quality |
|---------|------|----------------|---------|
| C1 | 4 | 0.811 | Good (some mixing) |
| C2 | 5 | 1.370 | Poor (high mixing) |
| C3 | 3 | 0.000 | Perfect (pure) |
| **Overall** | **12** | **0.841** | **Moderate** |

[3 marks: 1 for per-cluster entropies, 1 for overall calculation, 1 for final answer]

---

**INTERPRETATION:**

```
Purity = 0.75 (75%):
✓ Good overall performance
✓ 9 out of 12 documents correctly grouped
✓ 3 misclassifications (D5, D4, D9)

Entropy = 0.841 bits (53% of maximum):
✓ Moderate disorder
✓ Clusters reasonably homogeneous
✓ 47% better than random

Per-cluster analysis:
- Cluster 1: Good (75% pure, low entropy)
- Cluster 2: Weakest (60% pure, high entropy 1.37)
- Cluster 3: Perfect (100% pure, zero entropy)

Cluster 2 has highest entropy because:
- Most mixed (3 classes present)
- No dominant class (3/5 = 60%)
- Two classes with 1 document each (noise)

Overall: Good clustering, acceptable for most applications
```

---

### **PYQ QUESTION 2: Comparing Multiple Clusterings (10 marks)**

**Question:**

Given 9 documents from 3 classes (3 documents each), compare three different clustering algorithms based on purity and entropy:

```
Ground Truth:
- Class A: {D1, D2, D3}
- Class B: {D4, D5, D6}
- Class C: {D7, D8, D9}

Algorithm 1 (K-means):
- Cluster 1: {D1, D2, D3}
- Cluster 2: {D4, D5, D6}
- Cluster 3: {D7, D8, D9}

Algorithm 2 (Hierarchical):
- Cluster 1: {D1, D2, D4}
- Cluster 2: {D3, D5, D6, D7}
- Cluster 3: {D8, D9}

Algorithm 3 (DBSCAN with noise):
- Cluster 1: {D1, D2, D3, D4}
- Cluster 2: {D5, D6, D7, D8}
- Noise: {D9} (not in any cluster)
```

(a) Calculate purity and entropy for each algorithm [6 marks]
(b) Which algorithm performs best? Consider both purity and the fact that Algorithm 3 doesn't cluster all points [2 marks]
(c) How would you modify purity calculation if some points are marked as noise? [2 marks]

---

#### **COMPLETE SOLUTION:**

**(a) Calculate Purity and Entropy [6 marks]**

---

**ALGORITHM 1: K-means (Perfect clustering)**

**Confusion Matrix:**
```
           A  B  C
Cluster 1  3  0  0
Cluster 2  0  3  0
Cluster 3  0  0  3
```

**Purity:**
```
Each cluster: 3/3 = 1.0
Overall: (3+3+3)/9 = 1.0 (100%)
```

**Entropy:**
```
Each cluster: All from one class
p = {1, 0, 0}
Entropy = -[1×log₂(1)] = 0

Overall: 0 bits
```

**Result:** Purity = 1.0, Entropy = 0.0 ✓

---

**ALGORITHM 2: Hierarchical (Some errors)**

**Confusion Matrix:**
```
           A  B  C
Cluster 1  2  1  0
Cluster 2  1  2  1
Cluster 3  0  0  2
```

**Purity:**

```
Cluster 1 (n=3):
Dominant: A (2 docs)
Purity = 2/3 = 0.667

Cluster 2 (n=4):
Dominant: B (2 docs)
Purity = 2/4 = 0.50

Cluster 3 (n=2):
Dominant: C (2 docs)
Purity = 2/2 = 1.0

Overall: (2+2+2)/9 = 6/9 = 0.667
```

**Entropy:**

```
Cluster 1: p = {2/3, 1/3, 0}
Entropy = -[0.667×(-0.585) + 0.333×(-1.585)]
        = 0.918 bits

Cluster 2: p = {1/4, 2/4, 1/4}
Entropy = -[0.25×(-2) + 0.5×(-1) + 0.25×(-2)]
        = -[-0.5 + -0.5 + -0.5]
        = 1.5 bits

Cluster 3: p = {0, 0, 1}
Entropy = 0 bits

Overall: (3/9)×0.918 + (4/9)×1.5 + (2/9)×0
       = 0.306 + 0.667 + 0
       = 0.973 bits
```

**Result:** Purity = 0.667, Entropy = 0.973 bits

---

**ALGORITHM 3: DBSCAN (with noise)**

**Confusion Matrix (excluding noise):**
```
           A  B  C
Cluster 1  3  1  0
Cluster 2  0  2  2
Noise      0  0  1  (D9)
```

**Purity (excluding noise):**

```
Cluster 1 (n=4):
Dominant: A (3 docs)
Purity = 3/4 = 0.75

Cluster 2 (n=4):
Dominant: B or C (2 each, tie)
Purity = 2/4 = 0.50

Overall (considering only clustered points, n=8):
(3+2)/8 = 5/8 = 0.625
```

**Entropy (excluding noise):**

```
Cluster 1: p = {3/4, 1/4, 0}
Entropy = -[0.75×(-0.415) + 0.25×(-2)]
        = 0.811 bits

Cluster 2: p = {0, 2/4, 2/4}
Entropy = -[0.5×(-1) + 0.5×(-1)]
        = 1.0 bit

Overall: (4/8)×0.811 + (4/8)×1.0
       = 0.406 + 0.500
       = 0.906 bits
```

**Result (excluding noise):** 
```
Purity = 0.625 (for 8 clustered points)
Entropy = 0.906 bits
Note: 1 point (D9) marked as noise
```

**Alternative (including noise as misclassification):**
```
If we count noise as "wrong cluster":
Overall purity considering all 9 points = 5/9 = 0.556
```

[6 marks: 2 marks per algorithm]

---

**Summary Table:**

| Algorithm | Purity | Entropy (bits) | Points Clustered | Quality |
|-----------|--------|----------------|------------------|---------|
| K-means | 1.000 | 0.000 | 9/9 (100%) | Perfect ⭐⭐⭐ |
| Hierarchical | 0.667 | 0.973 | 9/9 (100%) | Moderate ⭐ |
| DBSCAN | 0.625* | 0.906* | 8/9 (89%) | Moderate* ⭐ |

*Excluding noise point

---

**(b) Best Algorithm Analysis [2 marks]**

**Ranking by Purity and Entropy:**

```
1st: K-means (Purity=1.0, Entropy=0.0)
2nd: DBSCAN (Purity=0.625, Entropy=0.906, but 89% coverage)
3rd: Hierarchical (Purity=0.667, Entropy=0.973)
```

**However, must consider coverage:**

**K-means:**
```
✓ Perfect purity (1.0)
✓ Zero entropy
✓ Clusters all points (100% coverage)
✓ Perfect alignment with ground truth

Winner: K-means is clearly best!
```

**DBSCAN vs Hierarchical:**
```
DBSCAN:
- Lower purity (0.625 vs 0.667)
- Lower entropy (0.906 vs 0.973) ← Better!
- Only clusters 89% of data
- D9 marked as noise (may be legitimate outlier)

Hierarchical:
- Higher purity (0.667)
- Higher entropy (0.973) ← Worse
- Clusters all data (100%)
- More errors distributed across clusters

Which is better depends on context:
- If outlier detection valuable: DBSCAN better
  (correctly identified D9 as different?)
- If must cluster everything: Hierarchical better
  (even if some errors)

For this problem: Hierarchical slightly better
(0.667 purity, all points clustered)
```

**FINAL RANKING:**
```
1. K-means: Best (perfect clustering)
2. Hierarchical: Second (67% purity, all points)
3. DBSCAN: Third (lower purity, incomplete)

But if D9 is truly an outlier/noise point:
1. K-means: Best (if D9 should be with C)
2. DBSCAN: Second (correctly flagged D9)
3. Hierarchical: Third (forced D9 into wrong cluster)
```

[2 marks: 1 for ranking, 1 for considering coverage]

---

**(c) Modifying Purity for Noise Points [2 marks]**

**Three Approaches:**

**Approach 1: Exclude noise from calculation**
```
Purity = (# correctly clustered) / (# of clustered points)

For DBSCAN:
Purity = 5 / 8 = 0.625

Pros:
✓ Fair to algorithms that detect noise
✓ Measures quality of actual clustering

Cons:
✗ Ignores that some points not clustered
✗ Can artificially inflate purity
```

---

**Approach 2: Count noise as errors**
```
Purity = (# correctly clustered) / (total # of points)

For DBSCAN:
Purity = 5 / 9 = 0.556

Pros:
✓ Penalizes incomplete clustering
✓ Comparable across algorithms

Cons:
✗ Unfair if noise points are truly outliers
✗ Penalizes good outlier detection
```

---

**Approach 3: Separate metrics**
```
Report three metrics:
1. Purity (of clustered points): 5/8 = 0.625
2. Coverage: 8/9 = 0.889
3. Effective purity: Purity × Coverage = 0.625 × 0.889 = 0.556

Or:
- Clustering quality (purity): 0.625
- Noise rate: 1/9 = 11%
- Both reported separately

Pros:
✓ Complete picture
✓ Separates clustering quality from coverage
✓ Fair to all algorithms

Cons:
✗ More complex
✗ Two metrics to consider
```

---

**RECOMMENDATION:**

```
Best practice: Use Approach 3 (separate metrics)

Report:
- Purity of clustered points: 0.625
- Coverage (% clustered): 89%
- Noise detection rate: 11%

This gives complete picture:
- How good is the clustering? (purity)
- How much data is clustered? (coverage)
- How much is marked as noise? (noise rate)

Formula:
Purity_clustered = (correct assignments) / (clustered points)
Coverage = (clustered points) / (total points)
Effective_purity = Purity × Coverage

For DBSCAN example:
Purity_clustered = 5/8 = 0.625
Coverage = 8/9 = 0.889  
Effective_purity = 0.625 × 0.889 = 0.556

Compare algorithms using both metrics!
```

[2 marks: 1 for approaches, 1 for recommendation]

---

### **PYQ QUESTION 3: Theoretical Understanding (6 marks)**

**Question:**

(a) Explain why purity and entropy cannot be used alone to determine the optimal number of clusters. Provide a mathematical example. [3 marks]

(b) A clustering has overall purity of 0.8. Does this mean the clustering is good? What additional information would you need to assess the quality? [3 marks]

---

#### **COMPLETE SOLUTION:**

**(a) Why Purity/Entropy Cannot Determine Optimal k [3 marks]**

**Reason: Monotonicity**

```
Purity increases monotonically as k increases
Entropy decreases monotonically as k increases

As k → n: Purity → 1.0 (trivially perfect)
As k → n: Entropy → 0 (trivially perfect)

This means:
- Purity always says "use more clusters!"
- Entropy always says "use more clusters!"
- Both reach perfection at k=n (useless)

Cannot identify optimal k from purity/entropy alone!
```

---

**Mathematical Example:**

```
Dataset: 6 documents, 2 classes
Class A: {D1, D2, D3}
Class B: {D4, D5, D6}

Test different k:
```

**k=1 (worst):**
```
Cluster: {D1, D2, D3, D4, D5, D6}
Purity = max{3,3}/6 = 0.5
Entropy = -[0.5×(-1) + 0.5×(-1)] = 1.0 bit
```

**k=2 (optimal):**
```
Clusters: {D1,D2,D3}, {D4,D5,D6}
Purity = (3+3)/6 = 1.0
Entropy = 0 bits
```

**k=3 (over-clustering):**
```
Clusters: {D1,D2}, {D3,D4}, {D5,D6}
Purity = (2+1+1)/6 = 0.667  ← Wait, decreased?

Actually, let me recalculate correctly:
If split is: {D1,D2,D3}, {D4,D5}, {D6}

Cluster 1: 3 from A → Purity = 3/3 = 1.0
Cluster 2: 2 from B → Purity = 2/2 = 1.0  
Cluster 3: 1 from B → Purity = 1/1 = 1.0

Overall: (3+2+1)/6 = 1.0 ✓

Purity still 1.0! Same as k=2.
Entropy still 0! Same as k=2.
```

**k=6 (trivial):**
```
Each point its own cluster
Purity = 1.0
Entropy = 0

Same as k=2 and k=3!
```

---

**The Problem:**

```
Purity and Entropy values:
k=1: Purity=0.5, Entropy=1.0
k=2: Purity=1.0, Entropy=0  ← Optimal
k=3: Purity=1.0, Entropy=0  ← Same metrics!
k=6: Purity=1.0, Entropy=0  ← Same metrics!

Cannot distinguish k=2 (meaningful) from k=6 (trivial)!

Both have perfect purity and zero entropy,
but k=2 is useful while k=6 is meaningless.

Purity/Entropy alone cannot find optimal k.
```

---

**What to Use Instead:**

```
Combine external and internal measures:

1. Internal measures (Silhouette, WSS) to find k*
2. External measures (Purity, NMI) to validate k*

Or use corrected external measures:
- Normalized Mutual Information (NMI)
- Adjusted Rand Index (ARI)

These do NOT increase trivially with k!
```

[3 marks: 1 for explanation, 1 for example, 1 for solution]

---

**(b) Interpreting Purity = 0.8 [3 marks]**

**Basic Interpretation:**

```
Purity = 0.8 means:
- 80% of documents in dominant class
- 20% misclassified or in wrong cluster

Sounds good! But need more context...
```

---

**Additional Information Needed:**

**1. Number of clusters (k) [0.5 marks]**
```
k=2: Purity=0.8 is excellent!
k=100: Purity=0.8 is terrible!
  (should be near 1.0 with so many clusters)

Must know k to assess if 0.8 is good.
```

**2. Number of classes (c) [0.5 marks]**
```
c=2: Purity=0.8 is good
  (random would give 0.5)
  
c=10: Purity=0.8 is excellent!
  (random would give 0.1)

Baseline purity = 1/c
Compare actual to baseline!
```

**3. Class imbalance [0.5 marks]**
```
Balanced classes (33-33-33):
Purity=0.8 is good progress

Imbalanced classes (80-10-10):
Purity=0.8 might just be majority class!
Not impressive.

Example:
Put all points in one cluster with majority class
→ Purity = 0.8 (trivial)

Need to check if clustering is meaningful.
```

**4. Per-cluster breakdown [0.5 marks]**
```
Overall purity=0.8 could mean:

Scenario A: All clusters around 0.8
- Cluster 1: 0.8
- Cluster 2: 0.8
- Cluster 3: 0.8
Consistent quality

Scenario B: Mix of good and bad
- Cluster 1: 0.95 (excellent)
- Cluster 2: 0.95 (excellent)
- Cluster 3: 0.3 (terrible)
Overall: 0.8

Very different! Need per-cluster analysis.
```

**5. Comparison to baselines [0.5 marks]**
```
Need to compare against:
- Random clustering: purity ≈ 1/c
- Majority class baseline: purity = largest class %
- Previous clustering attempts
- Published results on same data

Is 0.8 good for this specific problem?
```

**6. Application requirements [0.5 marks]**
```
Depends on use case:

Medical diagnosis: 0.8 might be too low (need >0.95)
Document organization: 0.8 might be fine
Recommendation system: 0.8 might be acceptable

Domain requirements matter!
```

---

**Complete Assessment:**

```
To properly assess Purity = 0.8, need:

Essential:
✓ Number of clusters (k)
✓ Number of classes (c)
✓ Class distribution (balanced vs imbalanced)
✓ Per-cluster purity breakdown

Helpful:
✓ Confusion matrix (error patterns)
✓ Comparison to baselines
✓ Domain requirements
✓ Entropy or other complementary metrics
✓ Internal quality measures (silhouette)

Only with this context can we say if 0.8 is:
- Excellent (if c=10, k=3, balanced)
- Good (if c=3, k=3, balanced)
- Mediocre (if c=2, k=2, balanced)
- Poor (if c=2, k=10, imbalanced)
```

[3 marks: 0.5 per additional information point, up to 3 marks total]

---

This completes the comprehensive coverage of **METHOD 5: ENTROPY AND PURITY (External Measures)**.

