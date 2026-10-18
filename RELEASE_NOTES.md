# Codex FastPilot v0.2.1 — 2026-10-18

本次完成本地完整更名后重新发布。

- 源码与解压目录：`codex-fastpilot/`
- 插件与市场：`codex-fastpilot@codex-fastpilot`
- 技能目录与调用：`skills/codex-fastpilot/`、`$codex-fastpilot`
- 命令入口：`scripts/run_fastpilot.py`
- Python 包：`scripts/fastpilot/`
- 运行配置：`~/.codex/fastpilot/`
- 环境变量前缀：`CODEX_FASTPILOT_`
- 安装包：`codex-fastpilot-v0.2.1-20261018.zip`
- 补丁：`codex-fastpilot.patch`

等待价值、实际收益和读材料规划三项选档逻辑保持。更名后的完整本地模拟测试 117 项通过，用时 165.158 秒；没有进行真实模型速度或计费基准测试。

原 MIT 许可证与上游署名保留。[来源说明](https://github.com/wei-er582/codex-auto-orchestrator)；包内 UPSTREAM.md 与历史发布记录说明派生关系。
补丁可在上游提交 b3b789d0a05a8c295da32c27c43893fbcd731b87 上应用，已逐文件核对还原结果。
