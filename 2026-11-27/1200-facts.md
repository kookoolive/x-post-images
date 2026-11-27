# 2026-11-27 12:00 facts — Anthropic Messages API Python → Rust

## Sources (fetched 2026-11-27 ~10:05 Asia/Shanghai)
- Official blog: https://www.anthropic.com/research/migrating-api-to-rust (dated 2026-11-25)
- Official post: https://x.com/AnthropicAI/status/2125627317263466739 (2026-11-25 16:26 UTC; 11-26 00:26 Beijing)
  Retrieved ~10:05+08: likes 14339, bookmarks 7126, impressions 4673543
- Do not use second-hand "memory down 80%" as an official prose figure. Figure 15 caption mentions a memory panel (all of production) but the body repeatedly states only ~70% CPU and ~20% median TTFT.

## Product / migration
- Messages API is the primary way customers and Anthropic apps (Claude Code, Cowork) reach Claude. Built in 2023 in Python.
- From early 2024 to fall 2026, API traffic grew roughly 1,800 times.
- Claude wrote a first version of the API's core request path in about a week.
- Official framing: verification, not generation, became the bottleneck.
- Reasons parity was hard: the Python API kept changing; Claude replies nondeterministically, so a difference is not necessarily a bug; they could not read customer requests, so they could not easily see where the two versions diverged.

## Incremental rewrite
- Rewrote model-output parsing in Rust and called it from Python: CPU down about 8% to 10%.
- Each reply token then crossed between Python and Rust three times. Gains did not justify the complexity; they went to a full migration.
- By May 2026, Claude was writing more than 80% of the code Anthropic merged.

## Tests were not enough
- About 700 existing integration tests. Tests ran the Python API in-process and mocked services (billing, rate limiting) in memory. A "pass" could mean the test had been loosened.
- When they audited ported tests, they found more than 100 weakened assertions.
- In April, Rust passed about 98% of its tests, yet only about a quarter of inputs matched on production traffic.

## Production as oracle
- Used the existing production Messages API as the verifier / oracle. Shadowed production traffic.
- Ran the model once and replayed the model output to Rust, so nondeterminism would not be scored as a bug.
- Compared every stage before and after the model call; results had to match exactly.
- Privacy: recorded the shape of each difference (step, prompt piece, first differing byte, lengths), not the request or reply text. Grouped by fingerprint.
- A script every 10 to 30 minutes grouped nearly identical records; a coordinating Claude agent was alerted only for a new kind of record, then assigned a worker and opened a Slack thread for engineers.

## Loops
- Inner loop: cloud sandbox, local input/output checks (input check does not call the model; often under a second). Agent sees only the fingerprint, writes its own request/reply to reproduce, then edits Rust until both versions match and adds a test.
- Outer loop: only after PR review. Compare shadowed production traffic with and without the fix, by fingerprint, on similar request kinds (model, features), without logging request text.
- Memory loop: review corrections become rules. Mid-May web-search tool description: an agent proposed copying the text into files; an engineer rejected it because Rust would still need to call the internal service before launch. In June, an agent grouped about 60 review comments and proposed rules an engineer edited into CLAUDE.md. Later a merge check required anyone changing Python to make a matching Rust change or request an exemption.

## Results and denominators
- About 70% less CPU per request; median time to first token down about 20%. No downtime, no feature freeze.
- Public traffic moved to Rust in September 2026.
- Figure 15 caption: lines are seven-day averages; 100 is about the level just before the move; CPU is one region; memory is all of production; time to first token is customers' Claude Code requests over a shorter period. Do not invent a memory percentage.

## Angle
- Verification, not writing the first draft, was the hard part.
- Tests passing is not production parity.
- The production system can be turned into a dispatchable verifier, but a verifier cannot tell you whether a fix is shippable.
- Do not write that Claude alone rewrote and shipped the production API.
