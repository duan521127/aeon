# 研究框架启用说明

## 当前交付

基于现有公开仓库 https://github.com/duan521127/aeon 制作，不创建第二个实例。
新增三个技能，以及 STRATEGY.md、soul/SOUL.md、soul/STYLE.md；同步生成 AGENTS.md 和两个 catalog。
三个新条目均为 enabled: false、schedule: "workflow_dispatch"、var: "dry-run"。
现有 heartbeat 的每日 UTC 08:00 配置保持原样；它不是本次新增计划。是否真实运行取决于现有 Actions 与模型连接，本次没有验证。

这份交付完成的是仓库文件框架与静态校验，没有替代真实模型运行、通知投递或研究质量验收。没有收集或读取任何密钥。

## 安全接入

最快路径是 https://www.aeon.fun/connect ，选择已有的 duan521127/aeon，检查 GitHub App 仅安装在该实例上，并在 Connect 中连接模型与用户自己的通知通道。不要新建同名实例，不要把密钥粘贴到聊天。
若使用 CLI，先安装 GitHub CLI 并在用户终端完成 gh auth login；进入实例仓库后执行：

```sh
gh repo set-default duan521127/aeon
gh repo view --json nameWithOwner -q .nameWithOwner
gh auth status
```

必须确认输出为 duan521127/aeon，不能为 aeonfun/aeon。用户亲自通过 Connect 的安全界面，或 `./aeon secrets set NAME --stdin` 输入凭据。凭据不出现在命令参数、聊天、报告或 git 提交中。不需要给这三个技能额外的研究 API 密钥。

## 应用文件

本框架通过独立分支和 PR 审阅，完成 CI 后再合并；不要直接覆盖现有主分支。
只使用 ZIP 内的文件覆盖时，catalog 中的 git 来源 SHA 会改变；应先提交三个 SKILL.md，再重新生成两个 catalog 后提交。
若使用 git 补丁，在与交付说明所列 base 相同的干净仓库上应用 aeon-framework.patch（git am），可以保留目录来源 SHA。遇到冲突时先停止并检查，不强制覆盖。

## 手动验收

文件合并到实例默认分支后，可用 Aeon 面板 Run now 或以下 CLI。无需打开定时计划：

```sh
./aeon skills run spodumene-reverse-flotation --var dry-run
./aeon skills run oil-china-chem-georisk --var dry-run
./aeon skills run research-persona-review --var dry-run
./aeon runs ls
./aeon runs logs RUN_ID
```

每个技能分别验收，不能只凭 Actions 显示成功：

| 场景 | 必须达到的结果 |
| --- | --- |
| 首次运行 | baseline_only，注明覆盖缺口；有来源和事实/推断/未知分层；不把首次发现当今天发生 |
| 重复运行 | 已报事件和多来源转载去重，no_change 不通知 |
| 跨 UTC 日期 | 滚动72小时去重，覆盖边界前的日期文件 |
| 旧事件新进展 | 只保留新证据并写清前后变化 |
| 来源不可达 | partial/blocked，不能写 no_change；相同故障不反复提醒 |
| 缺全文/缺行情 | 相应细节写未知，不补造数据；不误报突破或涨跌 |
| 通知失败 | failed，不记 sent，下次可重试 |
| dry-run 后首次 live | dry-run 不能抑制真实重要变化的首次通知 |
| 分身规则复核 | 无日志就说证据不足；只提出改法，不自动重写身份或规则 |

首次基线后，再以 live 手动运行研究技能；只有确实有重要变化才会发消息。验证通知通道可用应使用 Aeon 自带通道测试，不让研究技能伪造一条新闻。用户未授权的外部收件人不能加入通知。

## 通过后再启用计划

目前不保存任何未来 cron，也不自动启用。建议后续分别选择：技术追踪每日一次、市场风险交易日一次、规则复核每周一次；市场技能内部继续核对中国交易所节假日。具体时间由用户决定。
通过真实研究、重复静默和通知投递验收后，用 `./aeon skills set NAME --var live`、`./aeon skills schedule NAME "UTC_CRON"`、`./aeon skills enable NAME` 配置并推送。所有 cron 为 UTC；用户客户端时区为 Africa/Harare（UTC+2），中国行情时区为 Asia/Shanghai（UTC+8），二者不可混淆。
修改后检查 `./aeon skills ls --enabled --json`，核对接下来三次当地触发时间。未通过的技能继续保持关闭。
