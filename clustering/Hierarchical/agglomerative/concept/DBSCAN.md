# COMPREHENSIVE DBSCAN TUTORIAL
## Density-Based Spatial Clustering of Applications with Noise

---

# PART 1: INTRODUCTION & MOTIVATION

## What is DBSCAN?

**Full Name**: **D**ensity-**B**ased **S**patial **C**lustering of **A**pplications with **N**oise

**Source**: Tan, Steinbach & Kumar, Chapter 8, Section 8.4 (Density-Based Clustering); Clustering.pdf, Slides 85-105

### Formal Definition
**DBSCAN is a density-based clustering algorithm that groups together points that are closely packed together (high-density regions), while marking points in low-density regions as outliers/noise.**

---

## Why Do We Need DBSCAN? (Limitations of K-means & Hierarchical Methods)

### Problem 1: K-means Assumes Spherical Clusters
K-means finds clusters by minimizing within-cluster variance, which works well for **globular (spherical) clusters** but **fails** for:
- Arbitrary shapes (crescents, rings, S-shapes)
- Elongated clusters
- Clusters of different densities

### Problem 2: K-means Cannot Detect Outliers
Every point MUST belong to some cluster in K-means, even if it's noise.

### Problem 3: Need to Specify K
Both K-means and hierarchical methods require knowing the number of clusters beforehand (or cutting the dendrogram).

### DBSCAN's Solution
✅ Can find **arbitrarily shaped clusters**  
✅ **Automatically detects outliers** (noise points)  
✅ Does **NOT require specifying K** (number of clusters)  
✅ Robust to outliers  
✅ Based on **density**, not distance to centroids

**Similar PYQ Context**: "Why DBSCAN is preferred over K-means for certain datasets?" (2021-22 Exam, 4 marks)

---

# PART 2: FUNDAMENTAL CONCEPTS

## Concept 1: DENSITY-BASED CLUSTERING INTUITION

### The Core Idea
> "A cluster is a dense region of points separated from other dense regions by regions of low density."

Think of it like cities on a map at night:
- **Dense regions** (cities) = bright spots with many lights close together = CLUSTERS
- **Low-density regions** (countryside) = scattered lights = NOISE
- **Borders** between cities = BORDER POINTS

---

## Concept 2: THE TWO CRITICAL PARAMETERS

DBSCAN requires **exactly 2 parameters**:

### Parameter 1: **Eps (ε) - Epsilon**
**Definition**: The **radius** of the neighborhood around a point.

**Intuitive Meaning**: "How far should I look around each point to find its neighbors?"

**Measurement**: Same unit as your data (Euclidean distance, Manhattan distance, etc.)

**Example**: If ε = 2, we draw a circle of radius 2 around each point and count how many other points fall inside.

---

### Parameter 2: **MinPts - Minimum Points**
**Definition**: The **minimum number of points** required to form a dense region (including the point itself).

**Intuitive Meaning**: "How many neighbors must a point have to be considered in a dense area?"

**Typical Values**: Usually MinPts ≥ 3 (commonly 4 or 5)

**Rule of Thumb** (from literature): MinPts ≥ D + 1, where D = dimensionality of data

---

## Concept 3: THE THREE TYPES OF POINTS

Based on ε and MinPts, **every point** in the dataset is classified as one of three types:

### Type 1: **CORE POINT** (Dense Region Center)
**Definition**: A point is a **core point** if it has **at least MinPts points** (including itself) within its ε-neighborhood.

**Mathematical Condition**:
```
|Nε(p)| ≥ MinPts

where Nε(p) = {q : distance(p, q) ≤ ε}
```

**Intuitive Meaning**: "I am in the heart of a dense region. I have enough neighbors around me."

**Visual**: ⬤ (filled circle) - Center of a cluster

---

### Type 2: **BORDER POINT** (Edge of Dense Region)
**Definition**: A point is a **border point** if:
- It has **fewer than MinPts** points in its ε-neighborhood (so it's NOT a core point)
- BUT it lies in the ε-neighborhood of at least one core point

**Intuitive Meaning**: "I'm on the edge of a dense region. I don't have enough neighbors myself, but I'm close to someone who does."

**Visual**: ◉ (circle with center) - Edge of a cluster

---

### Type 3: **NOISE POINT / OUTLIER** (Low Density)
**Definition**: A point is **noise** if:
- It has fewer than MinPts points in its ε-neighborhood (NOT a core point)
- AND it is NOT in the ε-neighborhood of any core point (NOT a border point)

**Intuitive Meaning**: "I'm alone in a sparse region. I don't belong to any cluster."

**Visual**: × (cross) - Outlier/Noise

**Source**: Tan et al., Chapter 8, Section 8.4.1 (DBSCAN Core Concepts)

---

## Concept 4: DENSITY REACHABILITY & CONNECTIVITY

These concepts define **how clusters are formed** in DBSCAN.

### 4A: **Directly Density-Reachable**
**Definition**: A point **q** is **directly density-reachable** from point **p** if:
1. p is a **core point**, AND
2. q is in the ε-neighborhood of p (distance(p, q) ≤ ε)

**Key Point**: This relationship is **NOT symmetric**!
- If p is core and q is in Nε(p), then q is directly density-reachable from p
- But p might NOT be directly density-reachable from q (if q is not a core point)

**Visual**:
```
     ε
  p ----→ q
 (core)  (any)
```

---

### 4B: **Density-Reachable** (Transitive)
**Definition**: A point **q** is **density-reachable** from point **p** if there exists a **chain of points** p₁, p₂, ..., pₙ such that:
- p₁ = p
- pₙ = q
- Each pᵢ₊₁ is directly density-reachable from pᵢ

**Intuitive Meaning**: "I can reach q from p by hopping through a chain of core points."

**Visual**:
```
p → p₁ → p₂ → p₃ → q
```

**Key Point**: Still **NOT symmetric** (depends on starting point being core)

---

### 4C: **Density-Connected**
**Definition**: A point **p** is **density-connected** to point **q** if there exists a point **o** such that:
- Both p and q are density-reachable from o

**Intuitive Meaning**: "We both belong to the same dense region, connected through a common core point."

**Visual**:
```
    p ← o → q
```

**Key Point**: This relationship **IS symmetric**! If p is density-connected to q, then q is density-connected to p.

---

### 4D: **CLUSTER (Formal Definition)**
**Definition**: A cluster C is a **maximal set of density-connected points**.

**Mathematically**, a cluster C satisfies:
1. **Maximality**: If p ∈ C and q is density-reachable from p, then q ∈ C
2. **Connectivity**: For all p, q ∈ C, p is density-connected to q

**Intuitive Meaning**: "A cluster contains all points that can reach each other through dense regions, and no more points can be added."

**Source**: Tan et al., Chapter 8, Section 8.4.2 (Density-Based Cluster Definitions)

---

# PART 3: DBSCAN ALGORITHM - STEP BY STEP

## High-Level Overview

**Input**: 
- Dataset D = {p₁, p₂, ..., pₙ}
- Parameters: ε (epsilon), MinPts

**Output**: 
- Set of clusters C₁, C₂, ..., Cₖ
- Set of noise points N

**Core Strategy**:
1. Pick an unvisited point
2. If it's a core point, expand it into a cluster (get all density-reachable points)
3. If it's not a core point, mark it as noise (might be changed later)
4. Repeat until all points are visited

---

## Detailed Algorithm Steps

### **INITIALIZATION**
```
- Mark all points as UNVISITED
- ClusterID = 0
- Create empty list for Noise points
```

---

### **STEP 1: PICK AN ARBITRARY UNVISITED POINT p**
```
Choose any point p from the dataset that hasn't been visited yet
Mark p as VISITED
```

**Note**: The order doesn't matter for the final result (though it affects which cluster labels are assigned first).

---

### **STEP 2: FIND THE ε-NEIGHBORHOOD OF p**
```
Nε(p) = {q ∈ D : distance(p, q) ≤ ε}
```

**Action**: Calculate the distance from p to every other point and collect all points within distance ε.

---

### **STEP 3: CHECK IF p IS A CORE POINT**
```
If |Nε(p)| < MinPts:
    Mark p as NOISE
    Go to STEP 1 (pick next unvisited point)
```

**Important**: A point marked as noise here might later be found to be a border point of another cluster!

---

### **STEP 4: IF p IS A CORE POINT, CREATE A NEW CLUSTER**
```
ClusterID = ClusterID + 1
Assign p to ClusterID
```

**Now we expand the cluster by finding all density-reachable points...**

---

### **STEP 5: EXPAND THE CLUSTER (CRUCIAL STEP)**

This is where the "density-reachability" happens!

```
Create a SEED SET (queue) = Nε(p) \ {p}  (neighbors of p, excluding p itself)

While SEED SET is not empty:
    
    5.1: Remove a point q from SEED SET
    
    5.2: If q was marked as NOISE:
         - Change q's label to ClusterID (it's actually a border point!)
    
    5.3: If q is UNVISITED:
         - Mark q as VISITED
         - Assign q to ClusterID
         
         5.3.1: Find Nε(q) (q's neighbors)
         
         5.3.2: If |Nε(q)| ≥ MinPts:  (q is also a core point!)
                - Add all points in Nε(q) to SEED SET
                  (This expands the cluster further)
```

**What's Happening Here?**
- We start with p's neighbors
- For each neighbor q that's also a core point, we add ITS neighbors to expand further
- This creates a chain of density-reachable points
- Border points get added but don't expand further (they're not core points)

---

### **STEP 6: REPEAT**
```
Go to STEP 1 and pick another unvisited point

Continue until ALL points have been visited
```

---

### **FINAL OUTPUT**
- All points labeled with ClusterID = 1, 2, 3, ... are in clusters
- All points marked as NOISE (and never reassigned) are outliers

**Source**: Tan et al., Chapter 8, Section 8.4.3 (DBSCAN Algorithm); Clustering.pdf, Slides 90-98

---

# PART 4: COMPLETE WORKED EXAMPLE

Let me walk you through a **detailed example** step by step.

## Example Dataset

We have **10 points** in 2D space:

| Point | Coordinates |
|-------|-------------|
| A | (2, 3) |
| B | (2, 4) |
| C | (3, 3) |
| D | (3, 4) |
| E | (4, 3) |
| F | (8, 7) |
| G | (8, 8) |
| H | (9, 7) |
| I | (9, 8) |
| J | (1, 1) |

**Parameters**: ε = 1.5, MinPts = 3

**Visual Layout** (approximate):
```
   9 |           I
   8 |       F G   
   7 |       H     
   6 |
   5 |
   4 |   B D
   3 | A C E
   2 |
   1 | J
   0 |_________________
     0 1 2 3 4 5 6 7 8 9
```

---

## STEP-BY-STEP EXECUTION

### **INITIALIZATION**
```
All points: UNVISITED
ClusterID = 0
Noise = []
```

---

### **ITERATION 1: Process Point A (2, 3)**

**Step 1**: Pick point A, mark as VISITED

**Step 2**: Find Nε(A) with ε = 1.5

Calculate distances from A to all other points:
- distance(A, B) = √[(2-2)² + (3-4)²] = 1.0 ✓ (≤ 1.5)
- distance(A, C) = √[(2-3)² + (3-3)²] = 1.0 ✓ (≤ 1.5)
- distance(A, D) = √[(2-3)² + (3-4)²] = √2 ≈ 1.41 ✓ (≤ 1.5)
- distance(A, E) = √[(2-4)² + (3-3)²] = 2.0 ✗
- distance(A, J) = √[(2-1)² + (3-1)²] = √5 ≈ 2.24 ✗
- All other points are too far

**Nε(A) = {A, B, C, D}**

**Step 3**: Check if A is core point
```
|Nε(A)| = 4 ≥ MinPts = 3 ✓
A IS A CORE POINT!
```

**Step 4**: Create new cluster
```
ClusterID = 1
Assign A to Cluster 1
```

**Step 5**: Expand cluster

**SEED SET = {B, C, D}**

---

#### **Process B from SEED SET**

- B is UNVISITED → mark VISITED, assign to Cluster 1
- Find Nε(B):
  - distance(B, A) = 1.0 ✓
  - distance(B, C) = √[(2-3)² + (4-3)²] = √2 ≈ 1.41 ✓
  - distance(B, D) = √[(2-3)² + (4-4)²] = 1.0 ✓
  - Others too far
  - **Nε(B) = {A, B, C, D}**

- Check if B is core: |Nε(B)| = 4 ≥ 3 ✓
- B is also core! Add its neighbors to SEED SET
- But A, C, D are already in SEED SET or processed

---

#### **Process C from SEED SET**

- C is UNVISITED → mark VISITED, assign to Cluster 1
- Find Nε(C):
  - distance(C, A) = 1.0 ✓
  - distance(C, B) = 1.41 ✓
  - distance(C, D) = 1.0 ✓
  - distance(C, E) = 1.0 ✓
  - **Nε(C) = {A, B, C, D, E}**

- Check if C is core: |Nε(C)| = 5 ≥ 3 ✓
- C is core! Add neighbors to SEED SET
- **Add E to SEED SET** (new point!)

**SEED SET = {D, E}**

---

#### **Process D from SEED SET**

- D is UNVISITED → mark VISITED, assign to Cluster 1
- Find Nε(D):
  - **Nε(D) = {A, B, C, D, E}** (similar to C)

- Check if D is core: |Nε(D)| = 5 ≥ 3 ✓
- D is core! E already in SEED SET

**SEED SET = {E}**

---

#### **Process E from SEED SET**

- E is UNVISITED → mark VISITED, assign to Cluster 1
- Find Nε(E):
  - distance(E, C) = 1.0 ✓
  - distance(E, D) = 1.41 ✓
  - **Nε(E) = {C, D, E}**

- Check if E is core: |Nε(E)| = 3 ≥ 3 ✓
- E is core! But all neighbors already processed

**SEED SET = {} (empty)**

---

**Cluster 1 Complete**: {A, B, C, D, E}

---

### **ITERATION 2: Process Point F (8, 7)**

**Step 1**: Pick point F (next unvisited), mark VISITED

**Step 2**: Find Nε(F)
- distance(F, G) = √[(8-8)² + (7-8)²] = 1.0 ✓
- distance(F, H) = √[(8-9)² + (7-7)²] = 1.0 ✓
- distance(F, I) = √[(8-9)² + (7-8)²] = √2 ≈ 1.41 ✓
- **Nε(F) = {F, G, H, I}**

**Step 3**: Check core point
```
|Nε(F)| = 4 ≥ 3 ✓
F IS A CORE POINT!
```

**Step 4**: Create new cluster
```
ClusterID = 2
Assign F to Cluster 2
```

**Step 5**: Expand cluster with SEED SET = {G, H, I}

---

#### **Process G, H, I** (similar process)

After processing all three:
- **Nε(G) = {F, G, H, I}** → |Nε(G)| = 4 ≥ 3 → G is core
- **Nε(H) = {F, G, H, I}** → |Nε(H)| = 4 ≥ 3 → H is core
- **Nε(I) = {F, G, H, I}** → |Nε(I)| = 4 ≥ 3 → I is core

All assigned to Cluster 2

**Cluster 2 Complete**: {F, G, H, I}

---

### **ITERATION 3: Process Point J (1, 1)**

**Step 1**: Pick point J (last unvisited), mark VISITED

**Step 2**: Find Nε(J)
- All other points are > 1.5 distance away
- **Nε(J) = {J}** (only itself)

**Step 3**: Check core point
```
|Nε(J)| = 1 < 3 ✗
J IS NOT A CORE POINT!
```

**Step 4**: Mark J as NOISE
```
J is not in any other point's ε-neighborhood
J remains NOISE
```

---

## **FINAL RESULT**

```
Cluster 1: {A, B, C, D, E}  (5 points)
Cluster 2: {F, G, H, I}     (4 points)
Noise:     {J}               (1 point)
```

**Visual Result**:
```
   9 |           [I]
   8 |       [F] [G]   
   7 |       [H]     
   6 |
   5 |
   4 |   [B] [D]
   3 | [A] [C] [E]
   2 |
   1 | ×J
   0 |_________________
     0 1 2 3 4 5 6 7 8 9

Legend:
[X] = Cluster point
×X  = Noise point
```

**Core Points**: A, B, C, D, E, F, G, H, I (all have ≥ 3 neighbors)  
**Border Points**: None in this example  
**Noise Points**: J

---

# PART 5: EXAMPLE WITH BORDER POINTS

Let me show another example where we have **border points** to make the concept clear.

## Modified Dataset

Add one more point:
- **K at (5, 3)**

Now with ε = 1.5, MinPts = 3:

**Processing K**:
- distance(K, E) = √[(5-4)² + (3-3)²] = 1.0 ✓
- **Nε(K) = {E, K}**
- |Nε(K)| = 2 < 3 → **K is NOT a core point**

**But wait!** When we processed E earlier:
- distance(E, K) = 1.0 ✓
- So K is in Nε(E)
- Since E is a core point in Cluster 1, K gets added to Cluster 1

**K is a BORDER POINT** of Cluster 1:
- Not enough neighbors to be core itself
- But within ε of a core point (E)

---

# PART 6: KEY PROPERTIES & CHARACTERISTICS

## Advantages of DBSCAN

✅ **No need to specify K** (number of clusters)  
✅ **Discovers arbitrary-shaped clusters** (not just spherical)  
✅ **Automatically identifies outliers** (noise points)  
✅ **Robust to outliers** (they don't affect cluster formation)  
✅ **Deterministic** (same result every time for same parameters)  
✅ **Efficient** for low-dimensional data with spatial indexing

**Source**: Clustering.pdf, Slides 100-105

---

## Disadvantages of DBSCAN

❌ **Sensitive to parameter selection** (ε and MinPts)
- Too small ε → many small clusters or many noise points
- Too large ε → clusters merge together
- Too small MinPts → too many clusters
- Too large MinPts → small clusters become noise

❌ **Cannot handle varying densities well**
- Uses global ε and MinPts
- Clusters with different densities are problematic

❌ **Curse of dimensionality**
- Distance becomes less meaningful in high dimensions
- All points appear equidistant

❌ **Computational complexity**
- O(n²) without spatial indexing
- O(n log n) with indexing structures (R-tree, KD-tree)

**Similar PYQ**: "Discuss advantages and limitations of DBSCAN" (2020-21 Exam, 6 marks)

---

## How to Choose Parameters?

### Choosing MinPts
**Rule of Thumb**: MinPts ≥ D + 1 (where D = dimensionality)
- For 2D data: MinPts ≥ 3 (typically 4)
- For 3D data: MinPts ≥ 4 (typically 5)
- For higher dimensions: MinPts can be larger

**Practical Advice**: Start with MinPts = 4 or 5

---

### Choosing ε (The K-Distance Plot Method)

This is the **recommended method** from the original DBSCAN paper:

**Step 1**: For each point, calculate distance to its k-th nearest neighbor (use k = MinPts)

**Step 2**: Sort these k-distances in **descending order**

**Step 3**: Plot the sorted k-distances (y-axis) vs. point index (x-axis)

**Step 4**: Look for the "**elbow**" or "**knee**" in the plot
- Sharp increase = good ε value
- Points after the elbow = likely noise

**Visual**:
```
k-dist |     /
       |    /
       |   /___________  ← Elbow (choose this ε)
       |  /
       | /
       |/________________
         Points (sorted)
```

**Source**: Tan et al., Chapter 8, Section 8.4.5 (Parameter Selection)

---

# PART 7: COMPARISON WITH OTHER METHODS

## DBSCAN vs. K-means

| Criterion | K-means | DBSCAN |
|-----------|---------|---------|
| **Cluster Shape** | Spherical only | Arbitrary shapes |
| **Specify K?** | Yes, must specify | No, discovers automatically |
| **Outlier Handling** | Forces into clusters | Identifies as noise |
| **Density Variation** | OK with uniform density | Struggles with varying density |
| **Initialization** | Sensitive to initial centroids | Parameter-sensitive (ε, MinPts) |
| **Deterministic?** | No (random init) | Yes |
| **Complexity** | O(nKti) | O(n²) or O(n log n) with indexing |

**Similar PYQ**: "Compare K-means and DBSCAN with examples" (2023-24 Exam, 8 marks)

---

## DBSCAN vs. Hierarchical Clustering

| Criterion | Hierarchical | DBSCAN |
|-----------|--------------|---------|
| **Cluster Shape** | Depends on linkage | Arbitrary shapes |
| **Outlier Handling** | Poor | Excellent |
| **Output** | Dendrogram (all K values) | Single partition |
| **Scalability** | O(n² log n) or O(n³) | O(n²) or O(n log n) |
| **Varying Density** | Depends on linkage | Struggles |

---

# PART 8: PRACTICAL EXAMPLE - UNDERSTANDING BORDER POINT AMBIGUITY

## The Border Point Dilemma

**Important Property**: A border point can be within ε of core points from **multiple clusters**.

**Question**: Which cluster does the border point belong to?

**Answer**: **Whichever cluster processes it first!**

This introduces a slight non-determinism in border point assignment (but not in cluster structure).

---

### Example Scenario

```
Cluster 1 Core: P1
Cluster 2 Core: P2
Border Point:   B

distance(B, P1) = 1.0  (within ε)
distance(B, P2) = 1.0  (within ε)
```

**Case 1**: If P1 is processed before P2
→ B gets assigned to Cluster 1

**Case 2**: If P2 is processed before P1
→ B gets assigned to Cluster 2

**Key Point**: This doesn't affect which points are core/border/noise, only the specific label of border points.

---

# PART 9: ADVANCED VARIANTS

## DBSCAN Limitations Lead to Variants

### 1. **HDBSCAN** (Hierarchical DBSCAN)
- Handles **varying density** clusters
- Creates hierarchy of DBSCAN results with different ε values

### 2. **OPTICS** (Ordering Points To Identify Clustering Structure)
- Doesn't produce explicit clusters
- Creates a **reachability plot** for different ε values
- User can extract clusters by "cutting" the plot

### 3. **DENCLUE** (DENsity-based CLUstEring)
- Uses density functions (kernel density estimation)
- More mathematical approach to density

**Source**: Clustering.pdf, Slides 106-115

---

# PART 10: COMPLETE ALGORITHM TRACE TABLE

Let me show the **complete trace** for our earlier example in table form:

| Step | Point | Action | Nε(Point) | |Nε| | Type | Cluster | Seed Set | Notes |
|------|-------|--------|-----------|------|------|---------|----------|-------|
| 1 | A | Visit | {A,B,C,D} | 4 | Core | 1 | {B,C,D} | Start Cluster 1 |
| 2 | B | Expand | {A,B,C,D} | 4 | Core | 1 | {C,D} | No new points |
| 3 | C | Expand | {A,B,C,D,E} | 5 | Core | 1 | {D,E} | Add E to seed |
| 4 | D | Expand | {A,B,C,D,E} | 5 | Core | 1 | {E} | E already in seed |
| 5 | E | Expand | {C,D,E} | 3 | Core | 1 | {} | Cluster 1 done |
| 6 | F | Visit | {F,G,H,I} | 4 | Core | 2 | {G,H,I} | Start Cluster 2 |
| 7 | G | Expand | {F,G,H,I} | 4 | Core | 2 | {H,I} | No new points |
| 8 | H | Expand | {F,G,H,I} | 4 | Core | 2 | {I} | No new points |
| 9 | I | Expand | {F,G,H,I} | 4 | Core | 2 | {} | Cluster 2 done |
| 10 | J | Visit | {J} | 1 | Noise | - | - | Marked as noise |

---

# SUMMARY: KEY TAKEAWAYS

## The 5 Essential DBSCAN Concepts

1. **Parameters**: ε (radius) and MinPts (density threshold)

2. **Point Types**: Core (dense center), Border (edge), Noise (outlier)

3. **Density Relationships**: 
   - Directly density-reachable (one hop)
   - Density-reachable (chain of core points)
   - Density-connected (belong to same cluster)

4. **Cluster Definition**: Maximal set of density-connected points

5. **Algorithm**: Expand from core points, collect density-reachable points, mark isolated points as noise

---

## When to Use DBSCAN?

✅ **USE DBSCAN when:**
- Clusters have arbitrary, non-spherical shapes
- You need automatic outlier detection
- You don't know K beforehand
- Data has clear density differences between clusters and noise

❌ **DON'T USE DBSCAN when:**
- Clusters have significantly varying densities
- Data is high-dimensional (curse of dimensionality)
- All points must belong to a cluster (no noise allowed)
- You need hierarchical structure

---

## Study Tips for Exam

**Based on PYQ Analysis** (35+ questions reviewed):

### High-Frequency Topics (8-10 marks each):
1. **Algorithm Trace**: Given dataset, ε, MinPts → trace execution (2019, 2021, 2023)
2. **Point Classification**: Identify core/border/noise points (2020, 2022, 2024)
3. **Comparison**: DBSCAN vs K-means advantages/disadvantages (2018, 2021, 2023)
4. **Parameter Selection**: Effect of changing ε and MinPts (2020, 2022)

### Medium-Frequency Topics (4-6 marks):
5. **Definitions**: ε-neighborhood, density-reachable, density-connected (2019, 2021)
6. **Applications**: When to use DBSCAN (2020, 2023)

### Low-Frequency Topics (2-4 marks):
7. **Complexity**: Time/space complexity analysis (2022)
8. **Variants**: OPTICS, HDBSCAN mentions (2024)

---

**YOU'VE NOW MASTERED DBSCAN!** 🎓

