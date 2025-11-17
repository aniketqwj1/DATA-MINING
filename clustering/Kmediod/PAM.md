# PARTITION AROUND MEDOIDS (PAM) - Complete Step-by-Step Explanation

---

## **WHAT IS A MEDOID?**

Before understanding PAM, let's clarify **medoid** vs **centroid**:

### **Centroid (used in K-means):**
```
Centroid = Average of all points in cluster
         = Can be a "virtual" point (doesn't have to be an actual data point)

Example:
Points: (1,2), (3,4), (5,6)
Centroid = ((1+3+5)/3, (2+4+6)/3) = (3, 4)
          ↑ This happens to be a data point, but usually isn't!
```

### **Medoid (used in PAM/K-medoids):**
```
Medoid = The most centrally located ACTUAL data point
       = MUST be one of the existing data points in the dataset
       = The point that minimizes total distance to all other points in cluster

Example:
Points: (1,2), (3,4), (5,6)
Medoid = (3,4)  ← Must choose from these 3 points
```

---

## **WHY USE MEDOIDS INSTEAD OF CENTROIDS?**

### **Advantages of Medoids:**

1. **Robust to Outliers:**
   ```
   K-means with outlier:
   Points: 1, 2, 3, 4, 100
   Centroid = (1+2+3+4+100)/5 = 22  ← Pulled by outlier!
   
   PAM with outlier:
   Points: 1, 2, 3, 4, 100
   Medoid = 3  ← Stays in the center!
   ```

2. **Works with ANY Distance Metric:**
   - K-means requires calculating means (needs numerical data)
   - PAM only needs pairwise distances (works with categorical data, graphs, strings!)

3. **Interpretable:**
   - Medoid is an actual data point
   - Easy to interpret (e.g., "this customer is the typical customer")

---

## **PAM ALGORITHM - COMPLETE STEP-BY-STEP**

According to **Tan et al., Chapter 8**, PAM has **two phases**:

---

## **PHASE 1: BUILD (Initialization) - Select Initial Medoids**

### **Goal:** Find k good initial medoids

### **Steps:**

**Step 1:** Select the first medoid
```
Choose the point that minimizes the sum of distances to all other points

For each point p:
  Calculate: Σ distance(p, all other points)
  
First medoid = point with minimum sum
```

**Step 2:** Select remaining (k-1) medoids
```
For each medoid position (2 to k):
  For each non-medoid point:
    Calculate: Gain from adding this point as medoid
    Gain = Reduction in total distance
  
  Select point with maximum gain
```

---

## **PHASE 2: SWAP (Optimization) - Improve Medoids**

### **Goal:** Iteratively swap medoids with non-medoids to minimize total cost

### **Steps:**

**Step 1:** For each medoid m and each non-medoid point o:
```
Calculate: Cost change if we swap m with o
If cost decreases: Make the swap
```

**Step 2:** Repeat until no swap improves the cost

---

## **DETAILED EXAMPLE: PAM ALGORITHM**

Let me work through a complete example from scratch!

### **Given Data:**

```
Points: A, B, C, D, E, F
k = 2 (we want 2 clusters)
```

### **Distance Matrix:**

```
     A    B    C    D    E    F
A    0    2    6    8    10   12
B    2    0    5    7    9    11
C    6    5    0    3    5    8
D    8    7    3    0    4    6
E    10   9    5    4    0    3
F    12   11   8    6    3    0
```

---

## **PHASE 1: BUILD - Initialize Medoids**

### **Step 1: Select First Medoid**

Calculate sum of distances for each point:

```
Point A: 0 + 2 + 6 + 8 + 10 + 12 = 38
Point B: 2 + 0 + 5 + 7 + 9 + 11 = 34
Point C: 6 + 5 + 0 + 3 + 5 + 8 = 27
Point D: 8 + 7 + 3 + 0 + 4 + 6 = 28
Point E: 10 + 9 + 5 + 4 + 0 + 3 = 31
Point F: 12 + 11 + 8 + 6 + 3 + 0 = 40
```

**First Medoid = C** (minimum sum = 27)

---

### **Step 2: Select Second Medoid**

Now we need to find which point, when added as second medoid, minimizes total cost.

**Current state:**
- Medoid: C
- Non-medoids: A, B, D, E, F

**For each non-medoid, calculate the cost if we add it as second medoid:**

#### **Try adding A as second medoid:**

For each point, find distance to nearest medoid:

```
Point A: min(dist(A,C), dist(A,A)) = min(6, 0) = 0  [A is medoid]
Point B: min(dist(B,C), dist(B,A)) = min(5, 2) = 2
Point C: min(dist(C,C), dist(C,A)) = min(0, 6) = 0  [C is medoid]
Point D: min(dist(D,C), dist(D,A)) = min(3, 8) = 3
Point E: min(dist(E,C), dist(E,A)) = min(5, 10) = 5
Point F: min(dist(F,C), dist(F,A)) = min(8, 12) = 8

Total cost with {C, A} = 0 + 2 + 0 + 3 + 5 + 8 = 18
```

#### **Try adding B as second medoid:**

```
Point A: min(dist(A,C), dist(A,B)) = min(6, 2) = 2
Point B: min(dist(B,C), dist(B,B)) = min(5, 0) = 0  [B is medoid]
Point C: min(dist(C,C), dist(C,B)) = min(0, 5) = 0  [C is medoid]
Point D: min(dist(D,C), dist(D,B)) = min(3, 7) = 3
Point E: min(dist(E,C), dist(E,B)) = min(5, 9) = 5
Point F: min(dist(F,C), dist(F,B)) = min(8, 11) = 8

Total cost with {C, B} = 2 + 0 + 0 + 3 + 5 + 8 = 18
```

#### **Try adding D as second medoid:**

```
Point A: min(dist(A,C), dist(A,D)) = min(6, 8) = 6
Point B: min(dist(B,C), dist(B,D)) = min(5, 7) = 5
Point C: min(dist(C,C), dist(C,D)) = min(0, 3) = 0  [C is medoid]
Point D: min(dist(D,C), dist(D,D)) = min(3, 0) = 0  [D is medoid]
Point E: min(dist(E,C), dist(E,D)) = min(5, 4) = 4
Point F: min(dist(F,C), dist(F,D)) = min(8, 6) = 6

Total cost with {C, D} = 6 + 5 + 0 + 0 + 4 + 6 = 21
```

#### **Try adding E as second medoid:**

```
Point A: min(dist(A,C), dist(A,E)) = min(6, 10) = 6
Point B: min(dist(B,C), dist(B,E)) = min(5, 9) = 5
Point C: min(dist(C,C), dist(C,E)) = min(0, 5) = 0  [C is medoid]
Point D: min(dist(D,C), dist(D,E)) = min(3, 4) = 3
Point E: min(dist(E,C), dist(E,E)) = min(5, 0) = 0  [E is medoid]
Point F: min(dist(F,C), dist(F,E)) = min(8, 3) = 3

Total cost with {C, E} = 6 + 5 + 0 + 3 + 0 + 3 = 17  ← MINIMUM!
```

#### **Try adding F as second medoid:**

```
Point A: min(dist(A,C), dist(A,F)) = min(6, 12) = 6
Point B: min(dist(B,C), dist(B,F)) = min(5, 11) = 5
Point C: min(dist(C,C), dist(C,F)) = min(0, 8) = 0  [C is medoid]
Point D: min(dist(D,C), dist(D,F)) = min(3, 6) = 3
Point E: min(dist(E,C), dist(E,F)) = min(5, 3) = 3
Point F: min(dist(F,C), dist(F,F)) = min(8, 0) = 0  [F is medoid]

Total cost with {C, F} = 6 + 5 + 0 + 3 + 3 + 0 = 17  ← Also minimum!
```

**Second Medoid = E** (or F, both give cost = 17)

**Initial Medoids: {C, E}**

---

## **PHASE 2: SWAP - Optimize Medoids**

### **Current Configuration:**
- Medoids: {C, E}
- Total cost: 17
- Clusters:
  - Cluster 1 (C): {A, B, C, D}
  - Cluster 2 (E): {E, F}

### **Try All Possible Swaps:**

#### **Swap 1: Replace C with A**

New medoids: {A, E}

```
Point A: min(dist(A,A), dist(A,E)) = min(0, 10) = 0
Point B: min(dist(B,A), dist(B,E)) = min(2, 9) = 2
Point C: min(dist(C,A), dist(C,E)) = min(6, 5) = 5
Point D: min(dist(D,A), dist(D,E)) = min(8, 4) = 4
Point E: min(dist(E,A), dist(E,E)) = min(10, 0) = 0
Point F: min(dist(F,A), dist(F,E)) = min(12, 3) = 3

Total cost with {A, E} = 0 + 2 + 5 + 4 + 0 + 3 = 14  ← BETTER!
```

**This swap improves the cost! Accept it.**

**New medoids: {A, E}**
**New cost: 14**

---

#### **Continue with new configuration {A, E}:**

**Swap 2: Replace A with B**

```
New medoids: {B, E}

Point A: min(dist(A,B), dist(A,E)) = min(2, 10) = 2
Point B: min(dist(B,B), dist(B,E)) = min(0, 9) = 0
Point C: min(dist(C,B), dist(C,E)) = min(5, 5) = 5
Point D: min(dist(D,B), dist(D,E)) = min(7, 4) = 4
Point E: min(dist(E,B), dist(E,E)) = min(9, 0) = 0
Point F: min(dist(F,B), dist(F,E)) = min(11, 3) = 3

Total cost with {B, E} = 2 + 0 + 5 + 4 + 0 + 3 = 14  ← Same cost
```

**No improvement. Keep {A, E}.**

**Swap 3: Replace A with C**

```
This would give us {C, E}, which we already evaluated = 17
Cost increases. Reject.
```

**Swap 4: Replace E with F**

```
New medoids: {A, F}

Point A: min(dist(A,A), dist(A,F)) = min(0, 12) = 0
Point B: min(dist(B,A), dist(B,F)) = min(2, 11) = 2
Point C: min(dist(C,A), dist(C,F)) = min(6, 8) = 6
Point D: min(dist(D,A), dist(D,F)) = min(8, 6) = 6
Point E: min(dist(E,A), dist(E,F)) = min(10, 3) = 3
Point F: min(dist(F,A), dist(F,F)) = min(12, 0) = 0

Total cost with {A, F} = 0 + 2 + 6 + 6 + 3 + 0 = 17  ← Worse
```

**No improvement.**

---

### **After checking all swaps:**

No swap improves cost beyond 14.

**FINAL RESULT:**
- **Medoids: {A, E}**
- **Total cost: 14**
- **Clusters:**
  - **Cluster 1 (around A):** {A, B}
  - **Cluster 2 (around E):** {C, D, E, F}

---

## **PAM ALGORITHM PSEUDOCODE**

```python
PAM Algorithm:

Input: 
  - Dataset with n points
  - k = number of clusters
  - Distance matrix D[i][j]

Output:
  - k medoids
  - Cluster assignments

# PHASE 1: BUILD
# ================
Step 1: Select first medoid
  For each point i:
    Calculate sum_dist[i] = Σ D[i][j] for all j
  medoid[1] = point with minimum sum_dist

Step 2: Select remaining medoids
  For m = 2 to k:
    For each non-medoid point i:
      Calculate gain[i] = reduction in cost if i becomes medoid
    medoid[m] = point with maximum gain

# PHASE 2: SWAP
# ================
Repeat:
  improved = False
  For each medoid m in medoids:
    For each non-medoid point o:
      Calculate cost_change = cost_after_swap - current_cost
      If cost_change < 0:  # Cost decreased
        Swap m with o
        improved = True
        Break
Until NOT improved

Return medoids and clusters
```

---

## **COST CALCULATION FORMULA**

The **total cost** (objective function) that PAM minimizes:

```
Cost = Σ dist(point, nearest_medoid)
       for all points

Where:
dist(point, nearest_medoid) = min(dist(point, medoid₁), 
                                  dist(point, medoid₂), 
                                  ..., 
                                  dist(point, medoidₖ))
```

---

## **COMPUTATIONAL COMPLEXITY**

### **BUILD Phase:**
```
- Select first medoid: O(n²)
- Select k-1 remaining medoids: O(k × n²)
Overall BUILD: O(k × n²)
```

### **SWAP Phase:**
```
- Each iteration: Check k medoids × (n-k) non-medoids = O(k(n-k))
- For each swap candidate: Calculate new cost = O(n)
- Per iteration: O(k(n-k) × n) = O(kn²)
- Iterations: typically converges quickly, but worst case O(n)
Overall SWAP: O(kn² × iterations)
```

### **Total Complexity:**
```
O(k × n²)  in practice
O(k × n² × iterations) worst case
```

**Much slower than K-means which is O(nkl) where l = iterations!**

---

## **COMPARISON: K-MEANS vs PAM**

| Aspect | K-means | PAM (K-medoids) |
|--------|---------|-----------------|
| **Cluster representative** | Centroid (mean) | Medoid (actual point) |
| **Robustness to outliers** | ❌ Sensitive | ✅ Robust |
| **Data types** | Numerical only | Any with distance metric |
| **Computational cost** | O(nkl) - Fast | O(kn²) - Slow |
| **Cluster shapes** | Spherical | More flexible |
| **Interpretability** | Virtual point | Actual data point |

---

## **WHEN TO USE PAM?**

✅ **Use PAM when:**
1. Dataset has outliers
2. Non-numerical data (categorical, mixed)
3. Custom distance metrics needed
4. Small to medium datasets (n < 10,000)
5. Need interpretable cluster centers

❌ **Don't use PAM when:**
1. Very large datasets (too slow)
2. Clean numerical data without outliers
3. Speed is critical
4. Spherical clusters are appropriate

---

## **EXAMPLE FROM PREVIOUS YEAR QUESTIONS**

This is similar to **PYQ 2022-23, 10 marks question:**

> "Given documents with distance matrix where C and E are current medoids, determine next medoids using PAM algorithm with full explanation."

The solution would follow the SWAP phase we demonstrated above!

---

## **KEY TAKEAWAYS**

1. **Medoid = Most central ACTUAL data point**
2. **PAM has 2 phases: BUILD (initialize) + SWAP (optimize)**
3. **Cost function: Sum of distances from points to nearest medoid**
4. **More robust than K-means but slower**
5. **Works with any distance metric, not just Euclidean**

