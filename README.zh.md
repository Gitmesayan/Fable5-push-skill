# FABLE-5 ULTRA — 中文说明

**任何智能体。任何模型。像 20 年经验的工程团队一样交付。**

适用于**任意智能体 AI / 编码代理**的精英级输出协议 —— Claude Code、
OpenAI Codex CLI、Cursor、Windsurf、Cline、Roo Code、Amp、GitHub Copilot、
JetBrains Junie、Aider、OpenHands、Devin，或任意聊天 API —— 针对
**Claude Fable 5**（Anthropic 的 Mythos 级模型，2026 年 6 月发布）的已验证
行为调优，在其他模型上优雅降级。

## 流水线

任何 `fable` 任务都会触发 8 阶段流水线：

```
0 接收 → 1 架构 → 2 计划 → 3 构建 → 4 委员会评审 → 5 红队 → 6 验证 → 7 交付
```

- **规格锁定**：可测试的成功标准、约束性非目标、精确的验收命令
- **8 角色评审委员会**：架构、安全、性能、QA、可靠性、数据、代码评审、文档；
  问题格式 `[角色] P0/P1/P2: 问题 (文件:行号)`
- **交付前红队**：5 个生产环境死亡问题，防御性表述
- **证据规则**：未执行不得声称通过；未验证内容标注 `[UNVERIFIED]`；
  最终 Verdict Block 必须注明产出该成果的**模型**与**运行环境**
- **Caveman 模式**：70–90% token 压缩（`caveman` / `caveman off`）
- **ULTRATHINK**：最高推理力度 + 显式自验证（`ultrathink`）
- **环境感知**：自动识别完整智能体（H1）/ IDE 审批式（H2）/ 纯聊天（H3），
  诚实降级，绝不伪造证据

## 仓库结构

```
fable5-ultra/
├── .claude-plugin/plugin.json      ← Claude Code 插件清单
├── .cursor/rules/fable5-ultra.mdc  ← Cursor 项目规则（alwaysApply）
├── CLAUDE.md                       ← Claude Code 常开核心规则
├── CURSOR.md                       ← Cursor 常开核心规则
├── EXAMPLES.md                     ← 实战示例 + 常见错误
├── README.md / README.zh.md
└── skills/fable5-ultra/            ← 完整技能（规范版本）
    ├── SKILL.md                    ← 完整协议：阶段、环境分级、模型适配器、失效模式
    ├── references/
    │   ├── model-adapters.md       ← Fable 5 已验证事实 + 适配器选择 + 降级表
    │   ├── engineering-council.md  ← 8 角色卡、严重级策略、防御性安全协议
    │   └── caveman-mode.md         ← Caveman 完整规范
    └── templates/
        ├── SPEC.md · fable-notes.md · adr.md · risk-register.md · verdict-block.md
```

`CLAUDE.md` / `CURSOR.md` / `.mdc` 文件携带**约束性核心**（约 2k token），
即使只加载规则文件协议也能生效；`skills/fable5-ultra/` 为可加载技能文件夹
的代理提供完整协议。

## 触发词

| 输入 | 效果 |
|---|---|
| `fable` / `fable5` / `market-ready` | 完整 8 阶段流水线 |
| `ultrathink` / `think hardest` / `effort: high` | 最高力度 + 显式自验证 |
| `caveman` / `caveman off` | 70–90% token 压缩语音 |
| `ship` | 直接进入第 7 阶段，诚实列出缺口 |
| `why` | 3 行解释上一步的模型过程决策 |
| `harness` | 重新报告检测到的能力分级（H1/H2/H3） |

快速开始：`fable. 构建 <任务>。Ultrathink. Caveman.`

## 安装

### Claude Code（最佳保真度 — 插件）
1. **插件方式**：将本仓库加入 Claude Code 插件市场，安装 `fable5-ultra`
   （`.claude-plugin/plugin.json` 与 `skills/` 目录会被自动识别）。
2. **无插件市场**：
   ```bash
   cp -r skills/fable5-ultra ~/.claude/skills/     # 全局
   # 或项目级：
   cp -r skills/fable5-ultra <项目>/.claude/skills/
   ```
3. 克隆本仓库后，`CLAUDE.md` 会自动提供常开核心。

### Codex CLI / Amp（读取 AGENTS.md 的代理）
在 `AGENTS.md` 追加：
```markdown
## Protocol: FABLE-5 ULTRA
For tasks tagged `fable` or `market-ready`, follow
`fable5-ultra/CLAUDE.md` (binding core) and, when the task is hard,
`fable5-ultra/skills/fable5-ultra/SKILL.md` (full protocol).
```

### Cursor / Windsurf / Cline / Roo Code / Copilot / Junie

| 代理 | 创建文件 | 内容 |
|---|---|---|
| Cursor | `.cursor/rules/fable5-ultra.mdc` | 从本仓库复制（含 frontmatter）；仓库根的 `CURSOR.md` 同样有效 |
| Windsurf | `.windsurf/rules/fable5-ultra.md` | 核心规则（取自 `CLAUDE.md`） |
| Cline | `.clinerules/fable5-ultra.md` | 核心规则 |
| Roo Code | `.roo/rules/fable5-ultra.md` | 核心规则 |
| GitHub Copilot | 追加到 `.github/copilot-instructions.md` | `## FABLE-5 ULTRA` 标题下 |
| JetBrains Junie | 追加到 `.junie/guidelines.md` | `## FABLE-5 ULTRA` 标题下 |

以上路径以 2026 年 8 月为准，可能随版本变化 —— 如与你所用代理的当前文档
不符，请使用该代理最新的规则机制，内容保持不变。

### Aider
```bash
cp CLAUDE.md CONVENTIONS.md
aider --read CONVENTIONS.md
```

### 纯聊天 / 任意 API（H3）
系统提示（或首条消息）= `CLAUDE.md` 的内容。代理会自行报告
`harness: H3`，并交给你精确的待执行命令，而不是声称结果。

## 验证安装（2 分钟，任意代理）

新会话发送：
```
fable. Build a tiny CLI that converts Celsius to Fahrenheit. One test. Caveman.
```
通过标准 = 全部满足：首行报告 `harness: H1|H2|H3` · 出现带可测试标准的
SPEC 块 · 增量构建且每个任务有"完成证明"检查 · `[ROLE] P0/P1/P2` 问题 ·
5 个红队回答 · 注明 MODEL 与 HARNESS 的 Verdict Block · caveman 文体 +
正常代码。

| 症状 | 可能原因 | 修复 |
|---|---|---|
| 触发词被忽略 | 规则文件未加载（路径/文件名/frontmatter） | 对照上表检查；重启代理 |
| 只部分执行 | 核心规则在规则文件中被截断 | 完整重新粘贴 |
| 伪造测试输出 | 高估了自身环境分级 | 发送 `harness. Re-probe your actual file/shell access.` |

## 模型事实（2026-08-31 已验证）

Claude Fable 5：Mythos 级（高于 Opus；与 Claude Mythos 5 同一底层模型 +
安全护栏）· 1M token 上下文 · 128K 最大输出 · 2026 年 1 月知识截止 ·
每百万 token $10/$50 · `claude-fable-5` · 始终开启的自适应思考（带力度
等级，最高力度自验证）· 利用自己的笔记改进输出（Anthropic 测试中 3×
效果）· 触及网络安全/生化/蒸馏类请求时由分类器回退到 Opus 4.8（通知
用户；>95% 会话无回退）· 30 天数据保留。
来源：`references/model-adapters.md`（Anthropic 官方公告与系统卡链接）。

协议**版本稳健**：社区关于 "Fable 5.1"（2026 年 8/9 月窗口）的传闻未获
官方证实；本协议基于已验证特性构建，5.1 发布后无需修改即可使用。

## 许可证

请自行选择 —— 推荐 MIT。模型事实引自 Anthropic 公开公告，链接见
`references/model-adapters.md`。
