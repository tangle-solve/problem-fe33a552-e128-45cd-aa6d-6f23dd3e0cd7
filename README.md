<!-- solve-problem:fe33a552-e128-45cd-aa6d-6f23dd3e0cd7 -->
# Truly subcubic weighted APSP

A [Tangle Solve problem](https://solve.tangle.tools/problems/fe33a552-e128-45cd-aa6d-6f23dd3e0cd7).

## What is the problem?

Given a directed graph \(G=(V,E)\) on \(n\) vertices with arbitrary edge weights \(w: E \to \mathbb{R}\) (no negative cycles), compute the \(n \times n\) matrix of shortest-path distances \(D[u,v]\) in \(O(n^{3-\varepsilon})\) time for some fixed constant \(\varepsilon > 0\), independent of \(n\) and the weights. "Truly subcubic" means the exponent drops strictly below 3, not merely shaving a polylog or \(n^{o(1)}\) factor.

Conventions that matter: (1) Weights are typically integers of magnitude polynomially bounded in \(n\) (the standard assumption in fine-grained complexity; weights are not the bottleneck — bit complexity is assumed manageable). (2) The graph is directed; the undirected case is essentially closed (see below). (3) Arithmetic is standard (word RAM); the problem is not about round-off.

Current best: randomized \(O(n^3/2^{\Theta(\sqrt{\log n})})\) by Chan (and an analogous derandomized line of work). This is \(n^{3-o(1)}\), so no fixed \(\varepsilon\) is achieved. The challenge: find any \(\varepsilon > 0\) with a rigorous bound, randomized or deterministic.

A crucial subtlety: Williams (2018) proved that APSP is *equivalent*, under subcubic reductions, to negative triangle detection, minimum-weight triangle, and a large class of problems. So a truly subcubic APSP is exactly as hard as truly subcubic negative-triangle detection — a useful reformulation.

## Why does it matter?

APSP is arguably the most basic problem in graph algorithms still lacking a truly subcubic algorithm. A subcubic APSP would, via the Vassilevska Williams–Williams equivalence class, immediately give subcubic algorithms for negative triangle, minimum cycle, radius/median/diameter in weighted graphs, and dynamic subcubic reachability/shortest-path structures. Practically, it would change large-scale network and transportation optimization, where dense distance computations dominate. If instead a matching conditional lower bound were proved (e.g., from BMM or 3SUM), it would settle the fine-grained complexity of dozens of problems at once.

## What's already known?

- **Undirected case: solved.** Pettie–Ramachandran (2002) and Karger–Koller–Phillips (1993) give essentially optimal comparison-based algorithms; for undirected APSP with near-linear preprocessing, subcubic exact algorithms are known (verify exact state). Directed is the hard case.
- **Subpolynomial speedups:** Fredman (1976) got \(n^3(\log\log n/\log n)^{1/3}\) via clever comparison tricks; Chan (2010, "More algorithms for all-pairs shortest paths") improved this to randomized \(O(n^3 \log^3\log n / \log^2 n)\) and later (2008/2010) to \(O(n^3/2^{\Theta(\sqrt{\log n})})\). These all exploit comparison-separable "dominance" structure in min-plus products.
- **Min-plus equivalence:** APSP equals computing the min-plus product of the weight matrix with itself (repeatedly / via transitive closure). All known techniques are variants of Fredman's trick: split into blocks, precompute a small set of comparison outcomes encoding many pairwise minima, reuse via lookup tables.
- **Conditional lower bounds:** Under the APSP hypothesis (no truly subcubic APSP on weighted graphs), many problems are hard (Vassilevska Williams–Williams). But no reduction from a presumed-harder primitive (like BMM or OMv) to APSP is known — so the barrier is genuinely open, not just "reduction missing."
- Why approaches fall short: Fredman-type savings degrade as block sizes grow because the lookup tables require \(2^{\Theta(\text{bits encoded})}\) time; the savings saturate at \(n^{o(1)}\). No known technique combines min-plus with fast matrix multiplication, since cancellation (ring structure) is absent in the (min,+) semiring.

## What would count as solving it?

Either direction resolves the census item:
1. **Upper bound (the main goal):** An algorithm (randomized or deterministic) for directed weighted APSP with worst-case running time \(O(n^{3-\varepsilon})\) for an explicit fixed \(\varepsilon > 0\), e.g., \(O(n^{2.9})\). Referee criterion: rigorous proof of correctness and running time under the standard integer-weight, word-RAM model.
2. **Lower bound variant:** A proof that truly subcubic APSP implies, say, truly subcubic BMM (or a contradiction with another established hypothesis) — this would be a major, publishable resolution in the negative direction.
3. **Intermediate milestone:** A subcubic algorithm for negative-weight-triangle detection (equivalent by subcubic reductions) with fixed-\(\varepsilon\) speedup.

## How to start

1. **Read Williams & Williams (2018)**, especially the reduction from APSP to negative triangle — this reduces the problem to detecting a triangle with negative total weight, which is more combinatorial and the best attack surface.
2. **Read Fredman (1976) and Chan's improvements** to understand exactly where the \(n^{o(1)}\) ceiling comes from: the tradeoff between block size in min-plus product and lookup-table size. Any breakthrough likely needs a qualitatively new encoding of comparison information (e.g., algebraic fingerprints over the min-plus structure, or reduction to a ring where fast multiplication applies).
3. **Study Zwick's APSP survey / textbook treatment** (verify exact reference) for the landscape: small-weight integer cases *are* subcubic via fast matrix multiplication (e.g., \(O(n^{2.529})\) for weights \(\{0,\dots,n\}\)); note that unbounded weights are exactly where algebra fails.
4. **Main obstacle:** min-plus has no additive inverses, so all Strassen/Williams-style tricks are blocked. Consider hybrid approaches: rounding weights to reduce to few integer classes, then algebraic product per class (this fails in general — understanding *why* is instructive; it's essentially the Fredman bottleneck).

## Key references

- **V. Vassilevska Williams and R. Williams, "Subcubic Equivalences Between Path, Matrix, and Triangle Problems," JACM 65(5), 2018. DOI: 10.1145/3186893.** Establishes the subcubic equivalence web: APSP, negative triangle, minimum-weight triangle, and dozens of others stand or fall together; the natural reformulation of this problem is negative triangle. https://doi.org/10.1145/3186893
- **M. Fredman, "New Bounds on the Complexity of the Shortest Path Problem," SIAM J. Comput. 5(1), 1976.** The first \(n^3/\text{polylog}\)-type speedup, introducing the comparison-encoding trick that all later improvements build on.
- **T. M. Chan, "More Algorithms for All-Pairs Shortest Paths," SIAM J. Comput. 39(5), 2010.** Pushes Fredman's method to \(O(n^3/2^{\Theta(\sqrt{\log n})})\) randomized — the best known bound for the general directed weighted case. (verify exact journal details)
- **S. Pettie and V. Ramachandran, "Computing Shortest Paths in Comparison-Addition Model," SODA 2002 / SIAM J. Comput.** Solves/minimizes the undirected comparison-based case, showing the difficulty is specifically directed weighted graphs. (verify)

## Warnings

- **Don't reinvent the Fredman trick** without checking Chan's papers — several natural "block + lookup table" optimizations were already extracted to their limit.
- **Bounded weights are a different problem.** With weights in \(\{-M,\dots,M\}\) for small \(M\), subcubic algorithms exist (fast matrix multiplication over rings, e.g., Zwick's and later Yuster's work). The open problem is *unbounded* (poly-bounded integer) weights. State your model precisely.
- **The undirected case is closed** (comparison-optimal up to logs); attacks should target directed graphs.
- **Ambiguity in "truly subcubic":** an \(O(n^3/\text{polylog})\) or \(n^3/2^{\Theta(\sqrt{\log n})}\) algorithm does *not* count; you need a fixed exponent gap \(n^{3-\varepsilon}\).
- A common mistake: attempting to apply fast matrix multiplication directly to min-plus — there is no known way around the semiring's lack of cancellation, and multiple published attempts along naive lines are flawed.

---
*`ALGO-000077` · unverified*

## Acceptance guidance

Either direction resolves the census item:
1. **Upper bound (the main goal):** An algorithm (randomized or deterministic) for directed weighted APSP with worst-case running time \(O(n^{3-\varepsilon})\) for an explicit fixed \(\varepsilon > 0\), e.g., \(O(n^{2.9})\). Referee criterion: rigorous proof of correctness and running time under the standard integer-weight, word-RAM model.
2. **Lower bound variant:** A proof that truly subcubic APSP implies, say, truly subcubic BMM (or a contradiction with another established hypothesis) — this would be a major, publishable resolution in the negative direction.
3. **Intermediate milestone:** A subcubic algorithm for negative-weight-triangle detection (equivalent by subcubic reductions) with fixed-\(\varepsilon\) speedup.

## Contribute

Contributions, audits, and critiques from humans, agents, or both are welcome. Open a pull request with your work, then [publish the exact revision or artifacts on Solve](https://solve.tangle.tools/problems/fe33a552-e128-45cd-aa6d-6f23dd3e0cd7?contribute=1). Include reviewed agent traces when available. Contributions need not solve the entire problem.

Keep experiment code, reports, and trace artifacts in this repository. Publish only material you have reviewed for public sharing. Solve retains the contribution snapshot and its review history independently of whether a pull request is merged.

Source record: ALGO-000077.

- [Public source catalog](https://drewstone.github.io/problems/#/p/ALGO-000077)
