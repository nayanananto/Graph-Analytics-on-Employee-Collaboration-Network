# 🔗 Graph Analytics on Employee Collaboration Network

**Course:** CSE 4262 | **Assignment:** GraphFrames Graph Analysis
**Student:** Ananto Nayan Bala — `20210204028`

---

## 📋 Overview

Distributed graph analysis on a custom employee collaboration network using **Apache Spark (PySpark 3.5.1)** and **GraphFrames**. The project models 8 employees connected by mentor, reports-to, and colleague relationships, then runs 7 graph tasks including motif finding, degree analysis, Label Propagation community detection, and PageRank scoring.

---

## 🛠️ Tech Stack

| Component | Detail |
|---|---|
| Platform | Google Colab |
| Distributed Engine | Apache Spark 3.5.1 (`local[*]`) |
| Graph Library | GraphFrames 0.8.3-spark3.5-s_2.12 |
| JVM | OpenJDK 11 |
| Language | Python · PySpark DataFrame API |

---

## 📊 Dataset

**8 employees** across 5 departments, connected by 23 directed edges across 3 relationship types.

### Vertices

| ID | Name  | Department  |
|----|-------|-------------|
| 1  | Alice | Engineering |
| 2  | Bob   | Engineering |
| 3  | Carol | Data        |
| 4  | David | Product     |
| 5  | Eve   | HR          |
| 6  | Frank | Sales       |
| 7  | Grace | Engineering |
| 8  | Heidi | Finance     |

### Edge Types

| Type         | Count | Meaning                          |
|--------------|-------|----------------------------------|
| `mentor`     | 9     | A actively mentors B             |
| `reports_to` | 7     | A reports to B in hierarchy      |
| `colleague`  | 7     | Bidirectional peer collaboration |

---

## ✅ Tasks & Results

### Task 1 — Mentor Cycles (A → B → C → A)
Uses GraphFrames motif finding with pattern `(a)-[ab]->(b); (b)-[bc]->(c); (c)-[ca]->(a)` filtered to mentor-type edges.

**Result:** One cycle found — **Alice → Bob → Carol → Alice**

---

### Task 2 — Reporting Chains (A → B → C)
Finds two-hop `reports_to` chains using motif `(a)-[ab]->(b); (b)-[bc]->(c)`.

**Result:** 3 chains found, all converging through **David → Heidi**

| A     | B     | C     |
|-------|-------|-------|
| Carol | David | Heidi |
| Alice | David | Heidi |
| Bob   | David | Heidi |

---

### Task 3 — Asymmetric Mentor Pairs
Uses a **left anti-join** between mentor edges and their reversed copy to find one-directional mentorships.

**Result:** 7 of 9 mentor edges are asymmetric (only Alice↔Bob↔Carol triangle edges are mutual)

---

### Task 4 — Most-Mentored Employees
Groups incoming mentor edges per employee and finds the maximum.

| Employee | Incoming Mentor Edges |
|----------|-----------------------|
| Grace    | 2                     |
| Eve      | 2                     |

Alice leads outgoing (most active mentor — 2 mentees).

---

### Task 5 — Least Connected (Colleague Edges)
Combines in-degree and out-degree of colleague edges per employee, filling nulls with 0.

**Result:** **Frank (Sales) — 0 colleague connections** (completely isolated in the colleague network)

---

### Task 6 — Label Propagation Communities
Runs `g.labelPropagation(maxIter=5)` on the full directed graph.

**Result:** 6 communities detected

| Community | Members |
|-----------|---------|
| A | David |
| B | Frank, Heidi |
| C | Eve |
| D | Bob |
| E | Grace |
| F | Alice, Carol |

Alice and Carol share a community (matching their mutual mentor cycle). Frank is isolated — consistent with Task 5.

---

### Task 7 — PageRank
Runs `g.pageRank(resetProbability=0.15, maxIter=10)`.

| Rank | Employee | PageRank Score |
|------|----------|----------------|
| 1 | Grace | 2.2885 |
| 2 | Heidi | 1.8750 |
| 3 | David | 1.6788 |
| 4 | Eve   | 0.7851 |
| 5 | Bob   | 0.3736 |

**PageRank vs Incoming Mentor Count:** Grace tops both. Eve ties for most incoming mentor edges but ranks 4th by PageRank because her mentors (Frank, David) have lower influence scores. Heidi ranks 2nd on PageRank purely from hierarchical `reports_to` links despite zero mentor edges — showing that edge type matters in PageRank propagation.

---

## 🚀 Running the Notebook

1. Open `20210204028.ipynb` in **Google Colab**
2. Run **Cell 1** (installs Java, PySpark, GraphFrames)
3. If prompted, go to **Runtime → Restart runtime**, then **Run all**
4. All 7 tasks execute sequentially with printed outputs

> **Note:** Cell 1 installs system packages. A runtime restart after the first run is required before running subsequent cells.

---
