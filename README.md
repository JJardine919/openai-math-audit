# Corrections to OpenAI's math release (openai/math)

Independent audit by [Lattice24](mailto:jim@lattice24.com) of the manuscripts OpenAI released in [openai/math](https://github.com/openai/math) (October 2026, Apache-2.0). That repository has issues and discussions turned off, so we are publishing these corrections here. Archived copy: [doi:10.5281/zenodo.23422995](https://doi.org/10.5281/zenodo.23422995).

**Status (October 16, 2026):** 467 of the 547 manuscripts without a Lean formalization have been reviewed so far. 463 showed no gap, 1 could not be assessed, 2 have confirmed gaps (described below), and 1 dependent paper inherits the first gap. The audit is ongoing. We publish only findings that have survived independent verification.

| # | Manuscript | Severity | Kind |
| --- | --- | --- | --- |
| 1 | Square-lattice FK interfaces and nested loops for 1 ≤ q < 4 | Critical | Proof gap: the main theorem is not established as written |
| 1a | Natural Occupation Measures for Critical Square-Lattice FK Interfaces | Inherits #1 | Uses #1's results as inputs |
| 2 | The maximum number of mutually unbiased bases in dimension six | Major | Verifiability gap: the computer proof's code and run records are not released |

## Finding 1: Square-lattice FK interfaces (critical)

Manuscript: preprints/Square-lattice-FK-interfaces-and-nested-loops-for-1-leq-q-lt-4-September-23-2026 (observable.tex, preliminaries.tex)

Summary
The holomorphicity proof (Theorem hol:disk, and through it Theorem intro:main and the Cardy corollary) depends on the strict power saving established in Sec. 7.3, "The complete cost of the norm diagrams" (hol:strict-budget, hol:baseline). As written, that estimate never uses the mixed stencil, so the same argument also bounds the undifferenced function, which leads to a contradiction with the paper's own results. At least one ingredient of Sec. 7.3 must therefore be false as stated.

Details
1. The arithmetic in hol:strict-budget is correct as written. Odd copies have expected count 1/2. From hol:wrong-costs the averaged extra cost is 3P + D_* + (1/2) sum_j log(F_-(a_j)/F_+(a_j)). Applying exp:charge-budget gives <= 2P + D_*, exp:rectangle-budget (C_i >= 0) gives <= P + o(log n), and exp:strict-arm gives <= -c0 log n, relative to the baseline log(Z_g^cap E_{mu_g}[U T_g]). That baseline is exactly the normalization in wall:H-normalized.
2. Nothing in this accounting uses the stencil or any extraction. Sec. 7.3 says explicitly: "No decay from an unextracted insertion is used; its finitely many local terms cost only a constant." The leaf bound also covers unextracted markers directly.
3. The reflection inequality needs only an axis cut vacant of solids, endpoints and stencil boxes. The undifferenced Xi_g(z,u) is itself a tensor vacuum expectation (wall:diagram-normalization), and the secondary gaps supply the vacant cuts. So the same reflection tree, with at least one split, applies to Xi_g, and Cauchy-Schwarz gives |H_g(z,u)| <= n^{-c0+o(1)} at fixed gaps.
4. Now apply Prop. gap:closure (H_g = b_n H_f + o(1), with b_n bounded above and below) and Prop. wall:factorization (H_full = a_n h_n J_n + o(1), with |a_n| bounded away from 0), and let R_0 -> infinity. This forces h(z)J(u) = 0 for all eligible probe pairs. That contradicts the proof of Theorem hol:disk, where h is a nondegenerate conformal map and J is nonconstant with J(d_0) != 0.
5. The saving cannot be dropped: the angular extraction alone gives (-2+2eps) log n (hol:extracted-cost), which is O(n^{2eps}) after dividing by (2/n)^2, not o(1).

So at least one of the following is false as stated: (a) the absolute leaf bound with wrong-expansion weights on odd copies; (b) the wrong-copy costs in hol:wrong-costs; (c) the identification of the averaged baseline with the normalization. The holomorphicity proof depends on all three.

Downstream
"Natural Occupation Measures for Critical Square-Lattice FK Interfaces" uses this paper's chordal:trace and loop:plane as standing inputs (02-inputs.tex, Inputs 2.1 and 2.4) and states its Theorem 1 unconditionally for all q in [1,4). It inherits the gap. Other papers in the same family may also depend on these inputs; we have not finished checking them.

How this was found
Independent audit by Lattice24. Big J (Lattice24's hive organism) ranked the unformalized manuscripts and steered the order of review. Claude (Anthropic) review agents audited the proofs. Finding 1 was then checked by three independent skeptic passes, each instructed to refute it. All three failed to refute it, and each rated it critical. We would welcome a pointer to the step we have misread, if there is one.

Jim Jardine, Lattice24 (jim@lattice24.com)

## Finding 2: Mutually unbiased bases in dimension six (major)

Manuscript: preprints/The-maximum-number-of-mutually-unbiased-bases-in-dimension-six-September-24-2026 (execution.tex, conclusion.tex)

Summary
The paper claims N(6) = 3: no four mutually unbiased bases exist in C^6. The upper bound is computer-assisted, and the paper states that it is conditional on one recorded execution (Theorem execute:finite-theorem, used in the proof of Theorem 1.1). The code and records that execution depends on are not in the public release, so the upper bound cannot be checked from what OpenAI has published. This is a verifiability gap, not a demonstrated mathematical error.

Details
1. Section 7 (execution.tex) cites an accompanying verification/code/ directory with seven C++ sources (including stageC.cpp), extraction.json with their SHA-256 digests, proof_run.py, build/build-manifest.json, verification/computation/README.md, verification/REPRODUCE.md and the execution archive full-run-01/.
2. None of these files is in openai/math (checked against main at commit 1581d4c, October 12, 2026, the latest as of October 16). The paper folder holds only README.md, the PDF and the LaTeX sources. Other papers in the same release do ship their verification/ directories.
3. Theorem execute:finite-theorem assumes that the cited sources were used and that every run met its terminal, provenance and batch conditions. Without the code and records, none of these hypotheses, the counts in the execution tables or the hashes can be checked.
4. OpenAI's own Lean notes (lean/docs/266.md) say the formalization proves only that a family has at most five members, and "does not establish the paper's upper bound of three or its computer-assisted exclusion of four arbitrary bases."
5. The written mathematics in Sections 2 to 6 was reviewed and no error was found. The floating-point appendix was spot-checked only, since it refers to routines that cannot be inspected.

What would close it: releasing the verification/ directory and the full-run-01 archive the paper cites.

## Evidence

The `evidence/` folder holds the original review and every verification pass, exactly as they were produced:

- `review_035_square_lattice_fk.json`: the original referee review.
- `skeptic1_035.json`, `skeptic2_035.json`, `skeptic3_035.json`: three independent passes, each instructed to refute the claim. All three failed to refute it, and all three rated it critical.
- `review_052_natural_occupation_measures.json`, `skeptic1_052.json`: the dependent paper, which inherits the gap.
- `review_373_mub_dimension_six.json`: the original review of the dimension-six paper.
- `skeptic1_373.json`: an independent pass instructed to refute it. It failed to refute it and kept the severity at major.

## Method

1. **Ranking.** Big J, Lattice24's hive organism, scored all 547 unformalized manuscripts and set the order of review. It ran as 14 parallel branches sharing state through Redis.
2. **Review.** Claude (Anthropic) agents audited each proof as referees. Each branch shared its findings with sibling papers in the same family through the shared board.
3. **Verification.** Every critical or major claim went to independent skeptic agents whose job was to refute it. A finding is published only if it survives.

If we have misread a step, we would welcome a pointer to it, and we will publish the correction here.

Contact: Jim Jardine, Lattice24, jim@lattice24.com
