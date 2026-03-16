# Multi-Graph Matching via k-Core and Transitive Closure

This repository contains code to reproduce experiments on pairwise graph matching using the k-core estimator, boosted by transitive closure across multiple graphs.

## Problem Setup

Given a parent graph $G$ on $n = 10^4$ nodes, we independently subsample edges with probability $s$ to obtain $m$ child graphs $G_1, \dots, G_m$. For each pair $(i, j)$, we compute the intersection graph and extract its k-core as the estimated matching. We then boost all pairwise matchings via transitive closure: if a node can be traced through a chain of pairwise matchings connecting graphs $i$ and $j$, it is added to the boosted matching for that pair.

We measure the fraction of matched nodes (before and after transitive closure) as a function of $m$, averaged over independent trials.

## Graph Models

| Model | Parameters | $m$ range |
|---|---|---|
| **Erdos-Renyi** | $n=10^4$, $p=0.003$, $s=0.8$, $k=13$ | 2 - 14 |
| **Stochastic Block Model** | $n=10^4$, 5 balanced communities, $p=0.04$, $q=0.01$, $s=0.36$, $k=14$ | 2 – 14 |
| **Random Geometric Graph** | $n=10^4$, $r=0.2$, $p=0.15$, $s=0.4$, $k=14$ | 2 - 14 |



## Usage

### Loading saved results (no computation needed)

Each notebook includes a `load_results` function. Set `RUN_EXPERIMENTS = False` at the top of the notebook to skip computation and load the pre-computed `.pkl` files directly, then run the plotting cells.

### Running from scratch

Set `RUN_EXPERIMENTS = True` and run the notebook end to end. Results will be saved to the corresponding `.pkl` file. Note that the RGG experiments (up to $m = 14$) are the slowest.

## Requirements

```
pip install numpy matplotlib tqdm
```

## Method

1. **Generate** a parent graph under one of the three models.
2. **Subsample** $m$ child graphs independently.
3. **Pairwise matching**: for each pair, compute the k-core of the intersection graph.
4. **Transitive closure**: for each node, build a meta-graph over the $m$ graphs (edge if the node is matched between that pair). All pairs in the same connected component receive the node in their boosted matching.
5. **Aggregate**: pool matched fractions across all pairs and trials, report mean ± std.
