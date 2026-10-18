# Codex FastPilot

**Codex 智能调速**：根据任务、等待价值和实际收益，自动选择 Fast 或 Standard。

本项目基于 [wei-er582/codex-auto-orchestrator](https://github.com/wei-er582/codex-auto-orchestrator) 修改，保留原项目的 MIT 许可证和作者署名。上游基线提交：`b3b789d0a05a8c295da32c27c43893fbcd731b87`。本地构建版本：`0.2.0+local.20261018`。

**本仓库存放分发包；完整源码、技能、文档和测试均在 ZIP 内。**

## 下载

- [完整安装包（含源码与测试）](https://github.com/daidai1215/codex-fastpilot/raw/refs/heads/main/codex-auto-orchestrator-adaptive-v0.2.0-20261018.zip)
- [相对上游的运行代码补丁](adaptive-material-planning.patch)
- [SHA-256 校验值](SHA256SUMS.txt)
- [发布说明](RELEASE_NOTES.md)

补丁对应自适应运行逻辑、文档、测试和版本号；ZIP 另外包含本次分发所需的本地插件市场目录。

## 增加的三个规则

| 规则 | 行为 |
| --- | --- |
| 按等待价值选档 | 用户正在等结果时，允许原矩阵中的 Fast 组合继续参与选择；明确的后台、隔夜、非阻塞任务使用 Standard。 |
| 根据实际收益调整 | 只利用正常完成且通过校验的可比记录；样本充足、Fast 收益稳定不足时，后续调用降为 Standard。证据不足保留原矩阵，不额外发起测速或重复任务。 |
| 读材料的规划 | 仅当规划确实需要读取输入材料，且图片超过 5 张（6 张起）或含至少一个非图片附件时，该规划调用及其校验重试使用 Standard。 |

无需读取材料的普通规划继续使用原方案。规划结束后，执行与复核恢复“原矩阵 + 等待价值 + 实际收益”选档，**不会强制 Fast**。原来为 Standard 的组合不会自动升级。显式 `all-fast` / `follow-entry` 仍优先。

保留原有规划、任务拆分、模型及推理等级、复核和重试流程；没有加入“减少编排开销”的跳过规划功能。

## 安装

项目名称为 Codex FastPilot；插件标识、包内目录及原安装包文件名沿用已有名称，安装命令保持兼容。

需要 Python 3.11+、已配置并登录的 Codex CLI，以及当前账号可用的相应模型。

1. 下载 ZIP，解压得到 `codex-auto-orchestrator` 文件夹。
2. 注册解压后的本地插件市场，再安装插件。以下路径应替换为你实际解压的位置：

```powershell
codex plugin marketplace add "C:/path/to/codex-auto-orchestrator"
codex plugin add codex-auto-orchestrator@codex-auto-orchestrator-adaptive
```

3. 重启 Codex 并在新对话中使用。首次运行沿用原有速度矩阵设置流程，需要保存一个命名配置作为默认值；本包不包含任何机器的个人配置。

技能已允许自动匹配复杂、多步骤任务。也可以明确指定：

```text
自动编排：你的完整任务内容
```

自动匹配由模型根据任务判断，不保证每条消息都调用该技能。已开始的编排作业会在后续调用中持续应用选档规则。

## 手工调用与材料清单

在解压后的插件根目录运行：

```powershell
python scripts/orchestrate.py start --workspace "C:/project" --task-file "C:/input/task.txt" --wait-value blocking
```

后台任务使用 `--wait-value background`。如果规划需要读取用户材料，再传入 `--materials-file "C:/input/materials.json"`。该文件示例：

```json
{
  "planning_reads_materials": true,
  "attachments": [
    {"path": "C:/input/report.pdf", "mime_type": "application/pdf"}
  ]
}
```

通用规划不需要读取材料时，将 `planning_reads_materials` 设为 `false`。入口技能会根据任务和附件元数据传入清单，不额外调用模型分类；手工使用脚本时需自行提供。保存任务文字的 `--task-file`、普通工作区文件和输出文件不算用户附件。材料仍需位于已有权限允许读取的位置。

`--no-adaptive-fast` 只关闭收益调整，等待价值和材料规划规则仍有效。详细行为见 ZIP 内的 `docs/reference/CONFIGURATION.md`。

## 生效边界

- 档位在编排器发起每次模型调用之前确定。
- 无法在同一次请求的输入、思考、输出阶段分别切换。
- 无法追溯修改桌面入口已经开始的那次请求，也不靠修改全局速度设置模拟切换。
- 附件数量/类型是用户选择的启发式规则；不保证节省固定额度或达到固定加速倍率。
- 收益比较采用保守观察性记录，不构成因果提速或计费基准测试。

## 验证

- 117 项本地自动化测试通过，使用 `fake_codex`；未用真实模型做速度或计费基准测试。
- 技能格式、Python 编译和 20 份托管文档检查通过。
- 补丁已在上游原始源码上通过 `git apply --check --whitespace=error-all`，应用后逐文件核对。
- 分发包以已验证、已安装的版本为来源；本次打包保持全部已有运行文件不变，只增加本地市场分发元数据。

解压后可在插件根目录复现本地测试：

```powershell
python -m unittest discover -s tests -v
python scripts/check_docs.py
```

## 许可证

[MIT](LICENSE)。上游版权：Copyright (c) 2026 wei-er582。此仓库为派生分发版本，不代表上游或 OpenAI 官方发布。
