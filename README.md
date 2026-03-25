# 🔗 DiGraph Probe
### Directed Graph Generation & Degree Sequence Analysis in C

> A performance-focused implementation of core graph theory concepts from Discrete Mathematics — generating large-scale random digraphs and empirically verifying fundamental theorems at scale.

---

## 📌 What Is This?

**DiGraph Probe** generates random directed graphs (digraphs) of varying sizes and computes their **in-degree** and **out-degree** sequences using an optimized adjacency matrix approach. It then verifies a cornerstone theorem of discrete mathematics:

> *For every directed graph, the total in-degree across all vertices always equals the total out-degree.*

Benchmarks are run across graphs ranging from **1,000 to 5,000 nodes**, making the O(n²) growth of the algorithm empirically observable.

---

## 🧮 Discrete Math Concepts

| Concept | Description |
|---|---|
| **Directed Graph (Digraph)** | A graph `G = (V, E)` where every edge `(u, v)` has a direction from `u` to `v` |
| **Adjacency Matrix** | An `n × n` binary matrix where `A[i][j] = 1` denotes a directed edge from node `i` → `j` |
| **In-degree** `indeg(v)` | Count of edges directed *into* vertex `v` |
| **Out-degree** `outdeg(v)` | Count of edges directed *out of* vertex `v` |
| **Degree-Sum Theorem** | `Σ indeg(v) = Σ outdeg(v) = |E|` — equals the total number of edges |
| **Random Graph Model** | Each non-diagonal edge exists independently with probability `p = 0.5` |

---

## ⚙️ Algorithm Deep Dive

### 1. Graph Generation — `generateGraph()`

An `n × n` adjacency matrix is populated randomly:
- **No self-loops**: diagonal entries are always `0`
- **Random edges**: every off-diagonal entry is `0` or `1` with equal probability

```
for i in [0, n):
    for j in [0, n):
        graph[i][j] = (i == j) ? 0 : rand() % 2
```

Expected edge count: `~n(n-1)/2`

---

### 2. Degree Computation — `computeDegrees()`

A single O(n²) pass updates both degree arrays simultaneously, avoiding the redundant double-loop of a naïve implementation:

```
initialize indegree[], outdegree[] to 0

for i in [0, n):
    for j in [0, n):
        if graph[i][j] == 1:
            outdegree[i]++   ← i has an outgoing edge
            indegree[j]++    ← j receives an incoming edge
```

**Why this matters:** A naïve approach loops over all `j` twice per node — once for out-degree and once for in-degree. This merged pass cuts constant-factor work in half while maintaining the same asymptotic complexity.

| Metric | Value |
|---|---|
| Time Complexity | `O(n²)` |
| Space Complexity | `O(n²)` — adjacency matrix |
| Memory (n=5000) | ~100 MB static allocation |

---

### 3. Verification

After computation, the program checks `Σ indeg == Σ outdeg` and prints a pass/fail result — providing an empirical sanity check on every run.

---

## 📊 Performance Benchmarks

Measured on randomized graphs (`p ≈ 0.5`) using `clock()`:

| Graph Size `n` | Approx. Edges | Computation Time |
|---|---|---|
| 1,000 | ~500,000 | ~5 ms |
| 2,000 | ~2,000,000 | ~20 ms |
| 3,000 | ~4,500,000 | ~45 ms |
| 4,000 | ~8,000,000 | ~80 ms |
| 5,000 | ~12,500,000 | ~125 ms |

> ⚠️ Timings are approximate and hardware-dependent. The quadratic growth pattern is consistent with the expected **O(n²)** complexity.

---

## 🚀 Getting Started

### Prerequisites

- GCC or any **C99-compatible** compiler
- Unix/Linux/macOS terminal — or Windows with MinGW/WSL

### Compile

```bash
gcc -O2 -o digraph_probe main.c
```

### Run

```bash
./digraph_probe
```

### Sample Output

```
For n = 1000:
  Sum of In-degrees  = 499812
  Sum of Out-degrees = 499812
  Verified: Sum(In) == Sum(Out)
  Computational Time = 4.721 ms

For n = 2000:
  Sum of In-degrees  = 1999634
  Sum of Out-degrees = 1999634
  Verified: Sum(In) == Sum(Out)
  Computational Time = 19.503 ms

For n = 3000:
  Sum of In-degrees  = 4498201
  Sum of Out-degrees = 4498201
  Verified: Sum(In) == Sum(Out)
  Computational Time = 44.812 ms
```

---

## 🗂️ Project Structure

```
.
├── main.c        # Graph generation, degree computation, benchmarking
└── README.md     # Project documentation
```

---

## 🛠️ Implementation Notes

**Static Memory Allocation**
The adjacency matrix is declared `static int graph[5000][5000]`, placing ~100 MB in the BSS segment rather than the stack — avoiding a stack overflow that a local declaration would cause.

**Fixed Array Dimensions**
Function parameters use `graph[MAX_N][MAX_N]` instead of VLAs (`graph[n][n]`). This ensures the compiler uses the correct row stride when indexing — using `n` as the stride on a `MAX_N`-width array causes silent, incorrect memory access.

**Reproducibility**
The RNG is seeded with `srand(time(0))`, so each run produces a different graph. For reproducible results, replace `time(0)` with a fixed seed like `srand(42)`.

---

## 📚 References

- Rosen, K. H. — *Discrete Mathematics and Its Applications*, 8th Edition
- Diestel, R. — *Graph Theory*, 5th Edition
- Cormen et al. — *Introduction to Algorithms (CLRS)*, 3rd Edition

---

## 📄 License

This project is intended for **academic and educational purposes**.  
Feel free to reference, fork, or build upon it with attribution.
