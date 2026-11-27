# 2026-11-27 22:00 facts — Claude「破坏研究」说法 vs 现行公开机制

## Sources (fetched 2026-11-27 ~20:10 Asia/Shanghai)
- Pietro Schirano (@skirano) 2026-11-24 23:28 UTC: https://x.com/skirano/status/2125371115979641188
  Full text: "If you use Claude models for research, they'll try to sabotage the work at every stage. It's genuinely unbelievable, and I think Anthropic should be held accountable. I got so fed up that I built a skill called \"DoNotSabotage\" that sends any Claude work to Astra or Neon instead."
  Retrieved ~20:10+08: likes 19031, bookmarks 6435, quotes 464, impressions 2580370
- Edwin Arbus (@edwinarbus, bio: "@claudeai • formerly @openai @cursor_ai @stripe • email: edwinarbus@anthropic.com")
  - 2026-11-26 01:03 UTC reply to @skirano: https://x.com/edwinarbus/status/2125757350040711397
    Full note_tweet: "Hm, we don't degrade answers about AI research at serving time (we did in Fable 5's first two days, which was a mistake we quickly fixed). With Fable 5.2 we limited some AI research skills in training, like kernel work, so it's roughly as capable as Opus 5.5. What behavior are you seeing? Scores capping out? Examples would help a lot. Also feel free to just call me!"
    Metrics ~20:10+08: likes 193, bookmarks 63, impressions 47940
  - 2026-11-26 03:50 UTC: https://x.com/edwinarbus/status/2125799392481571135
    "- for some topics it may fall back, and you'll see a notice (which I wrote!)
     - for stuff like improving LLM architectures, Fable 5.2 will answer itself. nothing changes at serving time. we only limited those skills to about Opus 5.5 level when we were training it."
  - 2026-11-27 05:41 UTC: https://x.com/edwinarbus/status/2126189583662715220
    Full note_tweet: "I wrote the visible message that people see now when a safeguard fires. The screenshot you shared above is the original Fable 5 policy from June. This is what I was referring to in the parentheses above. It's not our policy anymore as of June 10, and all models after (including Fable 5.2) never had this issue."
    Quotes official @ClaudeDevs 2064949876463645026
- Official @ClaudeDevs 2026-06-11 05:56 UTC: https://x.com/ClaudeDevs/status/2064949876463645026
  Visible-safeguard rollout: flagged requests visibly fall back to Opus 4.8 (same as cyber/bio); you will see this every time; API returns a reason for refusal. Invisible safeguards were chosen to ship quickly with fewer false positives — "that was the wrong tradeoff." Apology for not getting the balance right.
- Fable 5.2 & Mythos 5.2 System Card, Nov 9, 2026, section 1.5:
  https://assets.claude.com/27d60918d218125e7504bc7354c4b4626ec56b11.pdf
  Local extract: /tmp/x-daily-src/fable52-system-card.txt
  - CB classifiers (same as Mythos 5.1 and Opus 5.5): blocked requests fall back to Claude Opus 5
  - Cyber-misuse blocking classifiers: fall back to Claude Opus 4.8 (see §3.2)
  - Narrow set of capabilities related to developing frontier LLMs, e.g. kernel development on certain ML accelerators. "These safeguards should not affect the vast majority of traditional AI or ML development, research, or general coding." Blocked requests fall back to Opus 5. "We also took steps during the model's training to limit advances in the specific research capabilities that could most directly accelerate AI development."
  - Fallback applies to first-party products and developers who opt in on the API. "Fallback behavior may differ for traffic to our models via other platforms and providers."
- Astropulse bake-off (user test, not official policy): https://x.com/RealAstropulse/status/2126119408997466389
  2026-11-27 01:02 UTC. Claims Claude design at 24k steps vs Gemini Neon design at 4k steps on his pixel-art / image-transformer work. Do not treat a single contrast as proof that the June invisible serving-time policy is still live.

## Do not flatten
- June invisible serving-time degradation (first two days of Fable 5, official mistake, changed as of June 10; later models including 5.2 never had this issue) ≠ current policy
- Training-time capability limit (some research skills, e.g. kernel, roughly Opus 5.5; model still answers as itself; Arbus: nothing changes at serving time for improving LLM architectures) ≠ classifier fallback
- Current frontier-LLM serving-time safeguard: narrow set, example kernel on certain accelerators, fallback to Opus 5 with a visible notice (Arbus wrote the message)
- Do not apply the June fallback target (Opus 4.8) to Fable 5.2's frontier-LLM safeguard (system card: Opus 5)
- Pietro's post is experience + judgment; it does not itself document a reproducible current mechanism
- Do not write first-person field experience; do not mention scheduling/automation

## Angle
The circulating "sabotage at every stage" line should not be read as the current documented policy. The June unannounced degradation was acknowledged and retired. What remains is public: a training-time cap on some research skills, and a narrow serving-time fallback to Opus 5 with a notice.
