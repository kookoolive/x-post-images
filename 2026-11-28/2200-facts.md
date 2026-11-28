# 2026-11-28 22:00 facts — Microsoft DRA：补丁变大，多半不是乱改

## Sources (fetched 2026-11-28 ~20:15 Asia/Shanghai)
- Goel, Steenhoek, Hu (Microsoft), "How Much Do Coding Agents Over-Edit, and Why? An Empirical Study of Unneeded Edits in Repository-Level Agent Patches", arXiv 2611.34875, dated November 25, 2026.
  https://arxiv.org/abs/2611.34875
  https://arxiv.org/html/2611.34875
- Preprint, not peer-reviewed. Authors: Aakash Goel, Benjamin Steenhoek, Yu Hu, all list Microsoft.

## Method (do not simplify away)
- Delta Residue Attribution (DRA): replay a resolved trajectory, split the final patch into units (contiguous blocks of changed lines that can be reverted alone), trace each unit to the step that wrote it, revert the unit, rerun the task tests. A unit is needed if reverting it causes the tests to reject the patch.
- NP_code = share of needed units among code units (test-file units graded separately / ungraded in the composition analysis).
- NP is 1-minimal: a larger group of units may still be removable together. Unneeded share is treated as an upper bound on true excess, because tests can miss needed behavior.
- "Unneeded" means the task tests still pass without that unit; it is not "a maintainer would delete it." Human labels were left for future work.
- Oracle used for main results: official evaluation images under O_test. Behavioral probes and a strengthened oracle O+ are additional checks.
- O_test alone calls 33% of developer code units unneeded; O+ calls 3% unneeded. The sign of the SWE-bench Verified agent-vs-developer gap depends on the oracle: under O+ it flips from +2.3 to -4.5 percentage points.
- Role and probe judges: GPT-6.1 Sol. Second judge Claude Opus 5.5 on 10,000 sampled steps: 84.4% role agreement (κ=0.69), 89.1% debug vs non-debug (κ=0.55). No human audit of role labels.

## Data
- 18 models from four providers, all in GitHub Copilot CLI. Other harnesses only in appendices; do not treat main numbers as harness-agnostic.
- SWE-bench Verified: 500 Python tasks; resolve rates 64.0%–89.4%. 6,728 resolved patches that replay exactly (30,265 units; 482 distinct instances, 114 shared by all 18 models).
- DeepSWE: 113 long-horizon tasks in Go, TypeScript, Python, Rust, and JavaScript. 932 resolved patches (26,209 units). Model-level DeepSWE stats use 17 models with at least 10 graded tasks.
- Developer baselines: Verified 470 analyzed instances (1,317 code units); DeepSWE 103 graded tasks (four developer patches fail the task tests and are dropped).
- Only successful / resolved trajectories. Unsuccessful runs are out of scope.
- 3.3% of Verified replays are not exact (228 mismatches, 6 self-check failures, 5 binary patches). 33.4% of DeepSWE patches (311/932) have at least one unit without provenance (14.9% of units, 12.7% of code units).
- SWE-bench Verified is Python-only and may be contaminated.

## Key numbers (quote exactly, with denominators)
- Verified, median model: 2.33× as many lines as the developer patch (all lines, in line with Luo et al. 2026). Do not call this a mean over all patches.
- Code units alone: ratio 0.86; per-task median 1.00.
- Outside test files: median ratio 1.09.
- Finding 1 wording: "Agent patches are larger than developers’ (2.33 times the lines). Outside tests, agents change 1.09 times the lines, yet most of their code is needed."
- Extra-edit composition, Verified: tests 61%, code 21%, documentation 13%, comment or formatting 5%. 81.5% of agent patches edit a test file.
- DeepSWE composition flips: ~6× more units per patch; code 58% of extra edits, tests 26%.
- Mean NP_code, Verified: agents 0.887 vs developers 0.864 on the same instances. 11 of 18 models leave significantly less unneeded code than developers (GPT-6 Astra only marginally, CI lower bound 0.002); four no significant difference (Kimi K3, Claude Haiku 4.5, GPT-6 Sol, GPT-6.1 Sol); three leave significantly more: Gemini 3.8, 3.7, and 3.6 Flash.
- DeepSWE: mean NP_code 0.797 vs 0.785 for 103 developer patches; only two models significantly below the developer baseline, none significantly above (mean gap −0.005). Figure 2b uses 914 agent patches from 17 models.
- On average, 11% of an agent patch’s code units on Verified are unneeded.
- Among 3,987 unneeded code units on Verified, probes on 3,559 show 89% change behavior (e.g. raising or swallowing an exception on nearby inputs); 11% are inert guards or duplicate checks.
- Mutation testing, 271 Validated Verified instances, 1,962 mutants (mutmut): agent-added tests catch 68.1%; existing regression tests in the same files 45.6%; task tests 77.1%. They catch 73.2% of mutants in needed code and 31.2% in unneeded code. Of mutants that slip past the task tests, 22.7% (102) are caught by tests the agent added or strengthened = 5.2% of all mutants.
- Debug steps write 154 of 3,987 unneeded code units (in 55 patches), ρ_code=0.039. 4,676 of 4,898 debug-step residue units are test-file hunks written before the fix. Fix steps write 83% of unneeded code; revision steps 11%. Revision code is unneeded only slightly more often than first-fix code (0.29 vs 0.25).
- Resolve rate vs NP_code: Verified Spearman −0.10 (raw p=0.69; 95% CI [−0.28, +0.05]). At similar resolve rates, Sonnet 5 (80.6%) NP_code=0.922 vs Gemini 3.7 Flash (81.4%) 0.789; unneeded code units per task 0.34 vs 1.23 (~3.6×). DeepSWE Spearman −0.59, p=0.01 (−0.52, p=0.03, task-adjusted), 17 models. Ranking agrees moderately between benchmarks (+0.59).
- Cleanup, SWE-bench Verified only, under O+:
  - First green (3,031 patches / 7,419 units): early patch still passes task tests in 2,948/3,031 (97.3%); NP_code 0.700 → 0.710.
  - Self-audit (17 of 18 models; 6,345 patches / 13,729 units): precision 65.7%, recall 35.5% (κ=0.39); drops break 1.8% of resolved patches; NP_code 0.924 → 0.944.
  - DRA + agent tests + PASS_TO_PASS (2,495 eligible of 6,724, because others have no standalone test command or fail the baseline): recall 67.3%, precision 46.3%; NP_code 0.936 → 0.975; 89.1% still pass task tests.
  - DRA + agent tests alone: 39.6% precision, 66.5% recall, 86.6% still pass.

## Angle
- The news is not "agents don't over-edit." It is that patch size is a lazy proxy, and the 2.33× figure is mostly tests/docs on Verified.
- Do not bury the 11% unneeded code, 89% of which changes behavior, or the DeepSWE flip (58% of extra is code).
- Do not say agents are cleaner than humans without the 11/4/3 split, the DeepSWE "on par / none above," and the O+ sign flip.
- Self-review missing ~64% (100−35.5) is recall, not "64% of the code is unnecessary."
