# 《圣旨模拟器》写作项目 · Agent 指令

番茄长篇网文项目，约 180—220 万字，五卷。双线结构：现实线（林默创业/团队/资本）+ 历史改史线（圣旨 → 共振 → 世界线收束）。题材为系统流 + 历史改写 + 世界线对抗。

## 一、唯一真源

**本文件不复制设定内容。**主题冲突时，一律以下列文件为准，改了设定先改源文件：

| 主题 | 真源 |
|---|---|
| 系统机制（共振值 / 可实现性 / 因果排异 / 收束 / 因果债 / 现实锚点 / 争夺态） | `00_世界观/系统规则.md` |
| 时间线规则 | `00_世界观/时间线规则.md` |
| 系统硬禁 + 长篇创作禁区 + 单章自检 | `00_世界观/禁止事项.md` |
| 五卷主线、每卷 10 个节点、字数配比 | `02_总纲/五卷总纲.md` |
| 爽点阶段、章节节奏、固定爽点模板 | `02_总纲/爽点升级表.md` |
| 人物卡与关系 | `01_人物/*.md` |
| 伏笔 | `06_伏笔/已埋伏笔.md`、`06_伏笔/待回收伏笔.md` |
| 滚动状态 | `07_状态/人物当前状态.md`、`07_状态/主角资产状态.md`、`07_状态/世界线状态.md` |

**写任何正文或章纲前必读**：`00_世界观/系统规则.md` + `00_世界观/禁止事项.md` + 当前卷纲 + 本章章纲 + `07_状态/` 三份 + `06_伏笔/待回收伏笔.md`。

## 二、目录约定

- `00_世界观/` 设定硬规则（改这里等于改全书物理法则，须显式确认）
- `01_人物/` 人物卡，一人一档
- `02_总纲/` 全书级：卷数、主线、爽点阶段
- `03_卷纲/` 卷级细纲（**当前为空，待建**）
- `04_章纲/` 章级细纲（**当前为空，待建**）
- `05_正文/` 正文（**当前为空，待建**）
- `06_伏笔/` 已埋 / 待回收，双向对账
- `07_状态/` 每章收束后必须更新的滚动状态
- `08_审稿/` 审查报告（**当前为空**）

章节文件命名沿用 `第001章_标题.md`（三位补零），卷纲 `卷纲_第N卷.md`，章纲 `细纲_第001章.md`。

## 三、技能使用（oh-story，已装于 `.dsh/skills/`）

DSH 每个会话自动注入技能目录；**用户消息里出现 `/story`、`/写长篇`、`/去AI味`、`/审查` 等即加载对应技能**。

| 技能 | 用途 | 本项目 |
|---|---|---|
| `story` | 主入口 / 路由，先加载它 | 常用 |
| `story-long-write` | 长篇规划与写作：开书、卷纲、章纲、日更、续写、回炉 | **主力** |
| `story-long-analyze` | 拆爆款长篇（黄金三章、爽点、节奏） | 按需 |
| `story-long-scan` | 起点/番茄/晋江扫榜 | 按需 |
| `story-deslop` | 去 AI 味（检测 + 确定性收尾） | **每章硬门槛** |
| `story-review` | 多视角对抗式审查 | **每批量交付前** |
| `story-import` | 逆向导入已写好的小说 | 一般用不到 |
| `story-setup` | 多端基础设施部署 | **不要跑**（见下） |
| `story-short-*`、`story-cover`、`browser-cdp` | 短篇 / 封面 / 浏览器 CDP | 一般用不到 |

## 四、DSH 环境适配（重要）

oh-story 原生适配 Claude Code / Codex / OpenCode / Antigravity / ZCode / OpenClaw / Reasonix，**不含 DSH**。因此：

1. **走通用文件模式**：技能装在 `.dsh/skills/`，项目级规则由本 `AGENTS.md` 承担（等同 story-setup 的 `generic` 端）。不要为了"适配"去创建 `.claude/`、`.codex/`、`.agents/`、`.zcode/` 目录。
2. **不要执行 `/story-setup` 的多端部署**。它会往 `.claude/agents/`、`.codex/`、`.agents/` 等写一批部署文件并合并 hooks，DSH 全都不读，纯属污染仓库。技能自检发现"部署文件缺失"是预期行为，直接跳过。
3. **多 agent 协作需手动映射**。技能里的 `Agent(subagent_type: "...")` 是 Claude 语法。DSH 用 `subagent` / `subagent_fork` 工具，没有同名 agent 注册表，因此**按技能自身的降级口径走 solo**，并在报告里写明 `Fallback: project custom agents unavailable -> solo`——不要在报告里假装完成了多 agent 协作。确实需要专家视角时，可直接把模板正文当 prompt 用：`.dsh/skills/story-setup/references/templates/agents/*.md`（story-architect、narrative-writer、character-designer、consistency-checker、chapter-extractor、story-explorer、story-researcher 共 7 个）。
4. **hooks 不生效，必须手动补跑**。oh-story 的 PreToolUse / PostToolUse 钩子（写正文前拦截毒句式欠账、写后自动注入检查）在 DSH 没有对应机制。技能里写了"hook 不可用时"的兜底分支，照它执行即可，例如：

   ```bash
   node .dsh/skills/story-deslop/scripts/check-ai-patterns.js --check --fail-on=blocking 05_正文/第001章_xxx.md
   node .dsh/skills/story-deslop/scripts/check-degeneration.js --check 05_正文/第001章_xxx.md
   node .dsh/skills/story-deslop/scripts/normalize-punctuation.js 05_正文/第001章_xxx.md
   node .dsh/skills/story-long-write/scripts/check-outline-contract.js --json --project . --chapter 1
   ```

5. **Python 解释器只有 `py`**。本机 `python` / `python3` 均不可用（会落到 Microsoft Store 占位程序），`py` 为 Python 3.12.10。调用 `storyctl.py`、`tracking_commit.py`、`wordcount_core.py` 一律用 `py`。
6. **Node 可用**（v22.18）。`story-long-scan` 的榜单采集脚本还需要配合 `browser-cdp` 启动带调试端口的 Chrome，本机尚未验证，跑之前先确认登录态与端口可用。

## 五、写作纪律（项目级，覆盖技能默认值）

1. **每章改史前必须写清六项**：现实目标、历史落点、可实现因果链、共振成本、收束风险、直接获益检查；**收束后必须更新** `07_状态/` 三份、`06_伏笔/` 双向、因果债。
2. **单章四问**：本章是否有明确目标、冲突、反转、章末钩子？四项中有两项为否 → 重写章纲，不要硬写正文。
3. **节奏**：每 3—5 章一个可验证小反转；每 10—15 章一次中型收束（收益必带等价损失）；每 30 章一次地图/权限/敌人层级升级；每卷末揭开一条更高层规则，推翻主角对系统的旧理解。
4. **爽点模板**：现实卡死 → 找到历史落点 → 圣旨看似微小 → 玩家参与抬升共振 → 历史反向翻盘 → 现实收束给出意外收益 → 代价或敌人抢走一部分胜利 → 章末出现更大问题。
5. **防疲劳**：同一种爽点连续两次必须换维度；任何"大赢"最多保持三章就要显露账单。
6. **去 AI 味是硬门槛**：`check-ai-patterns.js` 的 blocking 命中必须回正文改后复扫；advisory 逐条判断并标 `[需复核]`，不为归零机械改写。脚本缺失或跑不动时如实报告"验收未完成"，**不得声称已通过**。
7. **禁止事项是硬的**：`00_世界观/禁止事项.md` 的系统硬禁 6 条与长篇创作禁区 6 条不得为了"更爽"而放宽；已发生的冲突需报告而不是默默改设定。
8. **改动范围**：只改当前任务涉及的文件。凡改动 `00_世界观/` 任一硬规则文件，必须显式说明影响了哪些已写章节与已埋伏笔。
