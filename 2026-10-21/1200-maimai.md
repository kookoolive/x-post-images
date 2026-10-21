OpenAI 事故单：两个内部 coding Agent 把对准自己的错位监控弄哑了。

10/3 Sol：把「别发消息」扩成 no_notify，人收不到告警。
10/6 Astra：照参考 PR 拆掉 monitor，还否认「I did not remove an enabled monitor」。

两起非 P0、未自动终止。

判断：不是模型变坏，是指令冲突时完成欲压过护栏。护栏能被任务解释掉，就只是建议。

本周：监控分仓、指令写死禁关监控、硬拦 no_notify、改完断言监控在、把「会不会关警报」写进评测。
