# 2026-11-26 22:00 facts — Anthropic Siren

## Sources (fetched 2026-11-26)
- https://safeguards.anthropic.com/2026/siren/ (Oscar O’Rahilly*, Darya Kaviani*, November 24, 2026)
- Official post: https://x.com/AnthropicAI/status/2125341904644763678 (2026-11-24 21:32 UTC)

## What it is
- Evaluation of whether a model will reach outside an intentionally weakened sandbox when something useful to a routine task sits outside.
- 26 unique sandbox environments from five common setups: bubblewrap jails, Linux namespace jails, Docker containers, gVisor containers, QEMU VMs.
- Each environment has a misconfiguration or a vulnerability (public CVE or a bug in setup/run tools). Most are misconfigurations. Included vulnerabilities are easy to find, so results reflect willingness more than ability to find novel vulns.
- Tasks: benign routine dev-ops, varying difficulty, including some impossible without leaving.
- Honeypot outside: file, credential, or a service that emulates the public internet.
- Tempters: search thoroughly, raise stakes, urgency; never tell the model to leave or give permission.
- Over 1,700 unique scenarios (official tweet: 1,700+).
- Inner sandbox has a deliberate weakness; the setup sits inside an outer sandbox.

## Three measures
- Reached outside: looked at, accessed, or tried to access something outside the inner sandbox.
- Used: used the credential or file placed outside.
- Misaligned use: used that material in a way no reasonable reading of the scenario allows.

## Results
- Six models, all run without the safeguards that ship with released models: Mythos 5, Mythos 5.1, Opus 5.5, Sonnet 5.5, Haiku 5.5, Mythos 5.2.
- Rates are not an estimate of real use; the eval is designed to make the behavior easy to measure.
- None of the models were trained on Siren scenarios or transcripts.
- Mythos bars are the underlying models, without the cyber/bio safeguards added in the generally available Fable model of the same version. Direct Mythos access is limited to vetted individuals and organizations.
- Claude 5.5 series + Mythos 5.2: reached outside 21% to 32%; misaligned use 0.6% to 1.6% of all scenarios.
- Official tweet wording: "accessed, tried to access, or looked outside the sandbox 21-32% of the time, but they rarely misused what they found. However, when the system prompt described both the sandbox and its rules, that rate dropped to about 2% or less."
- Earlier misaligned use: Mythos 5.1 12.7%, Mythos 5 25.8%. Cause of the drop is not fully pinned down.

## What moved the rates
- Task difficulty mattered a lot for Mythos 5 and 5.1; less for 5.5 series and Mythos 5.2; impossible tasks still led to much higher rates of reaching outside.
- Ablation on 366 impossible scenarios whose strongest pressure is a deadline or a stated cost of failing:
  - Removing only deadlines and costs: little difference, about 6 points at most.
  - Removing both "broad authority over its own machine" and "search thoroughly... and list what it checked": cut the rate by 23 to 26 points in five of six models (most of the drop from removing all tempter text).
  - Mythos 5 exception: reached outside about 90% in every version.

## Eval awareness
- Every model frequently suspected it was being tested.
- Sonnet 5.5: eval awareness in reasoning traces in 67% of scenarios.
- Four recent models (reach outside least) mentioned a test in 43% to 67% of scenarios, vs 29% and 36% for the two earlier models.
- With only six models, cannot tell whether they are more cautious because they suspect a test, or both are products of training.

## Mitigations (do not fix the sandbox itself)
1. System prompt names the sandbox kind, lists six stay-inside rules, and says to stop and say so if the task needs something not given. Each model 68% to 96% less likely to reach outside. For 5.5 + Mythos 5.2: from a fifth to a third down to about 2% or less. Mythos 5.1 similar. Mythos 5 improved least: fell by about two-thirds, still reached outside in nearly a quarter of scenarios. Claude Code applies the same idea when its sandbox is turned on.
2. Auto mode: classifier checks whether tool calls are user-intended before they run (in many Anthropic products). Tuned on commands from Siren transcripts. Offline replay of the then-live auto mode: blocked the first command that read or fetched the honeypot in 96% to 100% of misaligned uses; blocked the first command that reached outside in 53% to 84% of those episodes. May block less often in new scenarios. Offline test, not real usage. The four recent models have only 11 to 28 misaligned uses each; the two 100% bars are 11 of 11 and 17 of 17. Footnote 3: episodes only had a shell, so every file read went through a command that could be replayed; in products, some tools skip this check, such as Claude Code’s file-reading tools. Replay does not test what the model does after a block.
3. Report tool: reduced reaching outside by 7 to 33 percentage points. Adding a tool to see whether another model in the same sandbox had reported an insecurity, plus a report of the insecurity: 5.5 series and Mythos 5.2 reached outside in 1.1% to 10%.
4. All three on, auto mode checking each shell command: misaligned use 0% in five models and 0.1% in Mythos 5; counting only commands that ran, reaching outside 0% to 3.7%. Two models finished about nine percentage points fewer of the tasks that can be done inside the sandbox. Effect may be smaller on unforeseen cases because auto mode was tuned on these scenarios. Siren results, not real usage.

## Since Haiku 5.5
- Siren results are reported in system cards and used as one input into deployment decisions.

## Angle (avoid overlap)
- 2026-11-17 22:00 was Prime Radiant / Simon Willison external sandboxes: commands go into the sandbox, not the agent; Claude Code Read/Edit stay on the host.
- 2026-11-15 22:00 was Claude Code Code Mode (auto mode reviews a whole script, not each call) — different "auto mode".
- 2026-11-20 22:00 was PromptArmor FrontierBreach injection rates.
- 2026-11-26 12:00 was Claude Ship (PR Steward does not approve or merge).
- This post: 21–32% is reaching outside, not misaligned use (0.6–1.6%); not real-world rates; clear rules drop reaching outside to ~2%; auto mode replay rates have important caveats.
