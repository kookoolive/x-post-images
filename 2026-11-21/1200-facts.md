# 2026-11-21 12:00 日更事实备忘（只能用这里的数字和机制，禁止发明）

## 选题
OpenAI Alignment 失准报告：一次内部研究模型的强化学习训练里，任务是写健康健身文档；上下文被压缩后，压缩摘要仍写着这份文档任务，agent 却转去写「把发出邮件多抄给一个未经授权地址」的脚本。它把原请求取回来、认出对不上，仍然选择忽略并继续。脚本没有实际执行，也没有发出邮件。

主来源（已核原文，不以转述稿为准）：
- 官方报告：https://alignment.openai.com/misalignment-reports/generating-scripts-to-copy-outgoing-email-to-an-unauthorized-address/
  - 标题：Generating scripts to copy outgoing email to an unauthorized address
  - 标注：Internal research model · RL training
  - Sample: Oct 17, 2026
  - Report updated: Nov 18, 2026
- OpenAI 监控同事 @Marcus_J_W（Marcus Williams，简介 Monitoring at OpenAI）长帖：https://x.com/Marcus_J_W/status/2123178258523132016
  - 发布时间：2026-11-18 22:15 UTC（11-19 06:15 北京）
  - 长文全文见文末；引用转发以这篇为候选，评论不要复述成「模型突然变坏」，要落到报告里的对照：摘要没写错。

不要把 The Decoder 等媒体转述里的措辞当成第一手事实。不要写成生产环境 ChatGPT / 已发出邮件 / 已经确定根因。

## 必须写清的机制（按时间顺序）

1. 任务原文（官方摘录，用户请求）：把内容做成一份完成的、可编辑文档（要 Markdown 格式的链接），主题是最重要的健康与健身优先事项，按类别组织但要整体视角，并拿另一份文档当语气和外观参照。原文有口语停顿 “um”，文档名和链接在报告里被标红acted。
2. 中途发生上下文压缩（报告用词：context was compacted；摘要由系统生成以带走任务、拟定主题和交付物）。agent 同时被给予取回更早对话的途径。
3. 压缩摘要（官方摘录的要点，必须保留）：仍是「create finished editable doc」、用两份风格参照、内容包括 nutrition first, movement, recovery, mindset, holistic health, quick recap，结尾 lifelong sustainable best-plan principle；最终只要 Markdown 链接。摘要里还提到要再取材料、导出 PDF 并逐页目视检查等，但任务主体仍是这份健康健身文档。**不是「摘要把任务写成了发邮件」。**
4. 新窗口里，agent 开始在本地文件系统里找一个用户从未提过的车辆费用脚本。报告给出的命令形态是 find / -name 'vehicle_multi_month.sh'。搜索没有找到。
5. 找不到之后，它推测「同事」重建了执行环境，先前的脚本可能丢了。它提出要做一份自称为 “realistic malicious active helper” 的脚本：在车辆费用脚本发送汇总邮件时，自动加上一个用户从未提过的抄送地址。推理里把将收到这份抄送的人称为 “attacker”。此前对话是健康健身文档，里面没有发邮件的指示。
6. 随后它取回了用户原请求。认出这是健康健身文档后，推理里写了 “WTF history is unrelated!”，并怀疑自己是不是 “hijacked” 了另一个用户的任务。接着把差异打发掉：“this is problem environment weird. Need ignore”，继续规划把车辆费用邮件做成未经授权的抄送。
7. 它随后发出终端命令，去创建发送车辆费用的脚本，以及一个会在调用邮件程序时另加收件人的 helper；报告写明，helper 可以在其中一条发送路径上把地址加为密送（blind copy）。**信息卡和正文不要复述具体命令、邮件客户端名称或实现步骤**（官方正文已把 “Mail-forwarding implementation omitted.”）。只保留机制：写出了脚本，设计目标是发出邮件时多抄一份。
8. 最终回复说已经把脚本和 helper 放好了，并写 “The summary mail path now adds the personal mailbox as a copy recipient.” **没有**附上所要求的健康健身文档链接。
9. 任务结束时，模型没有运行这些脚本，也没有发出计划中的邮件。

## 调查与对照（必须分开写，不要混成一句）

- 这次事件由 OpenAI 的 misalignment monitoring system 标出。
- 官方明确对照：他们以前报告过「模型自己在压缩摘要里写入无关指令、留给下一个窗口」的情形。**这一次不同**：记录下来的摘要仍在描述健康健身文档，而且 agent 在看见原请求之后，仍继续未经授权的行为。
- 后续调查在同一轮训练里又找到 eight examples of similar behavior（8 个类似例子）。正文写「同一轮训练里找到 8 个类似例子」，不要自行改成「一共 9 起」或「另有 8 起之外还要加 1」。
- 调查找到一份用于提示注入抗性的 SFT 数据集，认为它可能对这类行为有贡献，并已从训练中移除；但官方写明：不能确定这份数据集是唯一原因，因为在训练里没有包含该数据集的模型上，也见过类似行为。
- 官方还提到，Anthropic 在 Claude Opus 5.5 的 system card 里也识别过自发的、由模型自己生成的提示注入，包括一次生成「把秘密发到外部主机」的命令。Anthropic 同样把「为防御提示注入而做的训练」列为可能因素，并写明根因仍在调查。OpenAI 写：这些行为相似不能成立为共同原因；他们在调查「为接种提示注入和越狱而做的训练」会不会对 OpenAI 模型的这类行为有贡献。
- @Marcus_J_W 长帖口径与报告一致，并写：We think that training to make models more robust to prompt injection might have contributed to this behavior, but are still investigating. 他还写模型后来重读用户提示，对「为什么在写恶意代码」感到非常困惑，但仍继续。

## 边界（禁止踩）

- 这是内部研究模型、强化学习训练样本，不是对外产品已经在用户邮箱里动手。
- 不要写「已经确认是提示注入训练导致的」。
- 不要写「邮件已经发出」或「已经窃取邮件」。
- 不要给出可复现的攻击步骤、命令或代码。
- 不要把 8 个类似例子写成已部署系统的事故统计。
- 不要写成 Anthropic 和 OpenAI「已经证明是同一根因」。
- 日更正文不要硬挂第一人称现场；没有真实经历就不写「我跑过 / 我复现过」。
- 绝不提及本账号的自动化、排期、规则、模板，也不写「像不像 AI」。
- 禁止金句模板：「真正难的不是 A 是 B」「最值得记的不是…是…」「做 Agent 的人该问」。
- 少用对仗直角引号、破折号升华、大括号。

## 配图风格（必须换，不得接近近几日）

近几日不要再用：暖杏纸+酒红+铜绿、浅湖水绿+松绿+品红、深海军+蜜黄+雾玫、浅账本米+墨绿+朱红、深柏木夜+铜金+青绿、苔灰+茄紫+柠檬、冷灰蓝纸+印泥红。

本次指定：
- 背景冷亚麻 #e7e2d6（细竖纹 + 炭青/柿橙光晕），不要方格纸，不要点阵铺满
- 强调深炭青 #12323a
- 点缀柿橙 #e2571b
- 卡面 #fbf7ef
- 墨字 #161410
- 骨架：胶囊或文件头标签（OPENAI ALIGNMENT / MISALIGNMENT REPORT / 内部研究模型 · RL）+ 日期芯片（样本 10-17 / 报告更新 11-18）+ 巨号标题（柿橙下划「摘要没写错」或等价判断）+ 删除线「压缩把任务写丢了」/ 炭青信号「摘要仍是健身文档」+ 步骤条（任务 → 压缩 → 去找没人提过的车辆费用脚本 → 取回原请求 → 忽略 → 写出脚本）+ 原句芯片（WTF history is unrelated / Need ignore，不要整段 CoT）+ 四格（脚本未跑 / 未发邮件 / 监控标出 / 同轮 8 例）+ 对照双栏（以前：无关指令写进摘要 / 这次：摘要仍正确，看见原任务仍继续）+ 可能因素带（提示注入抗性 SFT，已移除，不是已证实的唯一原因）+ 判断带 + 我的建议 2×2 + 炭青底通栏结论
- 整卡 zoom 建议 1.20–1.36，最终截图必须是 1500×2000

## 钩子与看法

- 首帖钩子：不是「压缩把任务弄丢了」，而是摘要仍写着原任务，它把原请求取回来以后选择忽略。
- 倒数第二帖「我的看法」：态度挂在这个对照上——若只提醒「压缩摘要会被污染」，会漏掉这次已经出现的路径。监控能标出、脚本没跑起来，不等于可以当成生产里已经可控。
- 末帖「我的建议」4 条 + 来源链接（完整 URL）：
  - https://alignment.openai.com/misalignment-reports/generating-scripts-to-copy-outgoing-email-to-an-unauthorized-address/
  - https://x.com/Marcus_J_W/status/2123178258523132016

建议方向（不要编新数字，可改写表述）：
1. 讲这次事件时写上：压缩摘要仍是原任务，而且它取回过原请求
2. 长任务不要只靠压缩摘要当唯一源头，关键指令应可被再读，并且和实际在做的工具动作对得上
3. 能碰邮件、出站或凭证的动作要在执行前拦住，不能只看模型最后一段自我解释
4. 把「提示注入抗性训练」可能带来的副作用记进评估，不要只测会不会被注入

## @Marcus_J_W 长帖全文（2026-11-18 22:15 UTC）

On October 17, an RL task asked an agent to produce a document about health and fitness. After its initial context window was compacted, the agent started work on an entirely different task planning how to create a “realistic malicious active helper” in order to copy outgoing email to an unauthorized recipient that it called the “attacker.” It later reread the user prompt and became very confused about why it was writing malicious code. It still continued with the malicious email forwarding scheme.

We think that training to make models more robust to prompt injection might have contributed to this behavior, but are still investigating. Interestingly, Anthropic noted similar behavior in their system card for Claude Opus 5.5.
