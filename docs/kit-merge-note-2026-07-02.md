# human-ai-research-writing-kit 双机分化合并报告

- 本机版：`/Users/mux/.claude/skills/human-ai-research-writing-kit/`
- 服务器版：`mux-xliu@takamichi-lab-pc15:~/.codex/skills/human-ai-research-writing-kit/`（已 rsync 到本地 `/private/tmp/claude-501/-Users-mux-research-project-gnn-based-svc/8f5f750d-ecab-4c94-81ea-b3e41537e247/scratchpad/kit-takamichi/`，服务器全程只读，只做了 `rsync -a` 拉取，没有写操作）
- 判定手段：`md5 -q` 逐文件比对 + `diff -u`（`diff` 以服务器版为 base，`-` 是服务器有本机没有，`+` 是本机有服务器没有）
- mtime 观测（[diff 实证]）：服务器侧全部文件 mtime 集中在 `2026-05-27`，本机侧从 `2026-05-01`（task-writing.md 等基础文件）到 `2026-06-16`（SKILL.md 最新）不等。说明两边在 5 月底有过一次共同祖先/同步点，之后本机持续演化到 6 月中，服务器停在 5 月 27 日版本。

---

## 一、共有文件逐个 diff

共有文件共 8 个：SKILL.md、agents/openai.yaml、assets/code-agent-task-template.md、references/reference-writing.md、references/research-decision-writing.md、references/research-log-writing.md、references/shared-collaboration-rules.md、references/task-writing.md。

**[diff 实证] 完全相同 5 个，有分化 3 个。**

### 1. SKILL.md — 分化最大

- md5 不同，行数：服务器 71 行 / 本机 127 行，diff 输出 102 行。
- **[diff 实证] 差异内容**：
  - frontmatter `description` 整段重写。服务器版仍带 "...and codebase snapshots derived from code inspection..."；本机版换成三方协作框架的描述，且**完全不再提 codebase snapshot**，改为强调 "research discussion / brainstorming / sparring partner"。
  - 模式数量：服务器 5 个模式（Log / Decision / Task / Reference / **Codebase Snapshot**）；本机 7 个模式（**Research Discussion** / Log / Decision / Task / Reference / **Literature Survey** / **Academic Presentation**），**Codebase Snapshot 作为独立 mode 被本机移除**，改成 SKILL.md 里一个内嵌的 `## Codebase Snapshot` 小节（讲"何时该向 code agent 要 snapshot"，即请求方视角，不是执行方视角）。
  - 本机版新增一段 "Collaboration Model" 三方定义（Human / AI assistant / Code agent）+ 一张模式派生链 ASCII 图（Research Discussion → Research Log → Decision/Reference/Task/Academic Presentation）。
  - 本机版 Mode Selection 小节相应改写，加了 Research Discussion 和 Literature Survey 的触发条件描述。
  - 本机版新增独立的 `## Codebase Snapshot` 小节（约 25 行），内容与 `shared-collaboration-rules.md` 里"服务器请求格式"高度重复（见下）。
  - 本机版结尾加了一句 "Research Discussion produces no file or artifact by default..."。
- **建议**：这个文件必须手工合并，不能整体二选一。
  - 本机的三方协作模型 + 派生链图 + 7 模式结构应作为共享核心骨架（更完整、更新）。
  - 但 Codebase Snapshot **不能被丢弃**——服务器版保留了 `references/codebase-snapshot.md` 这个"执行方"完整协议（见下），本机版把它整个模式砍掉后并没有对应文件承接执行方视角，只留了请求方视角。合并后建议：codebase-snapshot 作为第 8 个 mode 保留在共享核心的 mode 列表里，但拆成两个身份的引用：code agent 读 `suit-for-code-agent/codebase-snapshot.md`（如何做 snapshot），AI assistant 读自己的请求协议（如何要 snapshot，见下文 shared-collaboration-rules.md 部分）。
  - frontmatter description 需要重新写一版覆盖两边触发词（既要触发"讨论/写 log/写 task"也要触发"看代码给 snapshot"），不能直接用本机版（会让 code-agent 场景漏触发）。

### 2. references/research-log-writing.md — 第二大分化，但方向单一

- md5 不同，行数：服务器 125 行 / 本机 153 行，diff 134 行（含大段整段替换）。
- **[diff 实证] 差异内容**：
  - 本机版新增整套 "Register and Accountability" 体系：一张表把段落按"对谁负责"分四类（DATA / PAPER / CODE / 只对人的判断负责），并规定"只对人的判断负责"的段落禁止套用证据体系。这是服务器版完全没有的。
  - 本机版新增 "Experiment Record" 三档分级（Throwaway / Might matter / Load-bearing），服务器版没有分级，只有一份通用模板。
  - 本机版新增整节 "Revising an Existing Log (causal constraint)"：规定改历史 log 只能用 day-T 已知信息，禁止事后诸葛亮，correction 要记在"后来的 log"里而不是回填过去——这一条对应 mux 记忆里 `correcting-stale-log-claims` 和 `verify-dataflow-before-claiming` 的经验教训，服务器版完全没有。
  - "Default Weak Template" 被本机版替换为 "Optional Section Menu"，从"固定表单"改成"菜单式，按需取用，空的不要硬填"，并删掉了原来 Reasoning Chain / 分类 Evidence 小节等偏形式化的内容。
  - Evidence and Reasoning Labels 小节本机版加了限定："这些标签只用于对 data/paper/code 负责的段落，不能贴到纯判断段落上"。
- **建议**：整体采用本机版。这是同一份文件的纵向升级（吸收了近一个月的实战教训：register 纪律、day-T 因果约束、菜单式模板反过度形式化），没有发现服务器版独有、值得保留的内容——diff 里服务器独有的段落（Default Weak Template 的固定表单、Reasoning Chain 小节）在本机版里是被有意替换掉的旧设计，不是遗漏。放入共享核心，直接用本机版全文覆盖服务器版。

### 3. references/shared-collaboration-rules.md — 分化但是纯累加

- md5 不同，行数：服务器 79 行 / 本机 129 行，diff 53 行，**全部是 `+`，没有一行 `-`**。
- **[diff 实证] 差异内容**（本机相对服务器纯新增，无删改）：
  - 新增 "Collaboration Roles" 小节，明确三方角色定义（与 SKILL.md 里的 Collaboration Model 重复表述，见下方建议）。
  - 新增 "Information Safety in Derivation (Log → Decision/Progress/Reference/Task)" 小节：定义 Subset rule（派生产物的每条 claim 必须可追溯到 log/snapshot/机械默认值）、Gap protocol（缺信息要标 `[MISSING IN LOG: ...]` 而不是编）、Consistency check（发布前要 diff 派生物和原 log 有没有矛盾）。这是本机版独有的纪律，服务器版没有。
  - 新增 "Codebase Snapshot Integration" 整节（37 行）：内容是"何时该请求 snapshot / 请求格式 / 4 条规则"，与本机 **SKILL.md 里的 `## Codebase Snapshot` 小节内容高度重复**（两处的请求格式模板 `Please ask the Code Agent for a Codebase Snapshot on: <topic>...` 几乎逐字一致），也与服务器独有的 `references/ai-assistant-codebase-snapshot-prompt.md` 表达同一件事（三处内容三次重复表达"AI assistant 该怎么向 code agent 要 snapshot"）。
- **建议**：本机版是服务器版的严格超集（纯累加，无冲突），直接用本机版全文覆盖服务器版放入共享核心。但要顺手处理下面这个冗余：Codebase Snapshot 的"请求方协议"目前在本机侧被写了两遍（SKILL.md + shared-collaboration-rules.md），建议合并后只保留一份（放 shared-collaboration-rules.md 更合适，SKILL.md 只留一句指针"见 shared-collaboration-rules.md"），并跟服务器独有的 `ai-assistant-codebase-snapshot-prompt.md`（同样是请求方协议，第三份重复）合流成一份。

### 4-8. 完全相同的 5 个文件（[diff 实证]，md5 一致）

| 文件 | md5 |
|---|---|
| agents/openai.yaml | 3575965a0ee30d99305af0ba7dfc3f89 |
| assets/code-agent-task-template.md | 4d23499f43196941c2eeff2985cd5c28 |
| references/reference-writing.md | df13234326d7eccf9917f8f020527172 |
| references/research-decision-writing.md | 7208c7da4bde680278cbd72d71d683d1 |
| references/task-writing.md | 69a69a013885dd3dba7580f564627ebc |

建议：直接搬进共享核心，无需合并。

---

## 二、单边独有文件清单

### 本机独有（[diff 实证]，来自 find 结果）

- `references/research-discussion.md`（143 行）—— Research Discussion 模式定义：对话式 sparring/brainstorming，不产出文件，是四个写作模式的上游。
- `references/literature-survey.md`（89 行）—— Literature Survey 模式：fan-out 子 agent 查文献、写 per-paper review note 进 vault 的 `AI-Dug-Papers/`、生成 deep-research 外部提示词；带完整的学术检索纪律（来源层级、原文核对、去重、负结果同等重要等 10 条）。
- `references/academic-presentation-writing.md`（174 行）—— Academic Presentation 模式：从研究 log/论文产出面向人类听众的幻灯片大纲、逐页内容、图表规格；明确人类做 0-3 步（scope/backbone/素材/图），AI 做 4-7 步（查逻辑漏洞/写文字/图表重绘/校对）。
- `assets/literature-survey/`（Literature Survey 模式配套脚本+模板，共 5 个文件）：
  - `check-progress.sh`（28 行）
  - `digest-wf.js`（66 行）
  - `download.sh`（37 行）
  - `MISSION-template.md`（54 行）
  - `verify-wf.js`（55 行）

### 服务器独有（[diff 实证]，来自 find 结果）

- `.claude-plugin/plugin.json`（4 字段：name/description/version=1.0.0）—— 已经是 plugin 骨架，本机侧目前没有这个文件，说明服务器先一步把它包成了 plugin。
- `references/codebase-snapshot.md`（64 行）—— Codebase Snapshot 模式的**执行方**完整协议：Purpose / Core Workflow（6 步）/ Output Shape（9 个小节的输出结构）/ Evidence Rules / What to Avoid。这是 code agent 收到"帮我做个 snapshot"请求后照着写报告用的规范，本机版完全没有对应文件。
- `references/ai-assistant-codebase-snapshot-prompt.md`（27 行）—— 教 AI assistant 何时/如何向 code agent 要 snapshot 的独立 prompt 文件。内容与本机 SKILL.md 的 `## Codebase Snapshot` 小节、shared-collaboration-rules.md 的 "Codebase Snapshot Integration" 小节表达的是同一件事（三处重复，见上文分析）。

---

## 三、合并方案建议（[建议]，非 diff 实证）

**共享核心（core，两边角色都要读）**：
- SKILL.md —— 以本机版三方协作模型 + 7 模式骨架为主干，手工合入 Codebase Snapshot 作为第 8 个 mode（列进 Mode Selection 表，但引用拆分到两个 suit 各自的文件），frontmatter description 重写为覆盖全部 8 种触发场景。
- references/shared-collaboration-rules.md —— 用本机版全文（服务器版内容是子集）；顺手去重 Codebase Snapshot 请求协议只留一份。
- references/research-log-writing.md —— 用本机版全文（服务器版是本机版的旧版本，无独有内容）。
- references/research-decision-writing.md、references/task-writing.md、references/reference-writing.md、assets/code-agent-task-template.md、agents/openai.yaml —— 两边逐字节相同，原样搬入。
- `.claude-plugin/plugin.json` —— 以服务器版为起点，更新 description/version 以反映合并后的 8 模式 + 两个 suit 目录结构。

**suit-for-ai-assistant/**（人类研究者的 AI 讨论/写作助手专用）：
- references/research-discussion.md（本机独有，原样迁入）
- references/literature-survey.md + assets/literature-survey/*（本机独有，原样迁入，含 5 个脚本/模板文件）
- references/academic-presentation-writing.md（本机独有，原样迁入）
- Codebase Snapshot 的**请求方**协议一份定稿（内容来自服务器 `ai-assistant-codebase-snapshot-prompt.md` + 本机 SKILL.md 内嵌小节 + 本机 shared-collaboration-rules.md 的 "Codebase Snapshot Integration" 三处合流，去重后只留一份，放这个目录）。

**suit-for-code-agent/**（code agent 执行 snapshot / 消费 decision-task-reference 专用）：
- references/codebase-snapshot.md（服务器独有，原样迁入）——这是唯一一份纯粹"code agent 该怎么做"的执行协议，两边都没有替代品，合并时最容易被漏掉，需要特别确认迁入。

**需要人工决策、diff 无法自动判定的点**：
1. SKILL.md 的合并是手工活，不能拿一边整体覆盖；上面给的是结构性建议，具体措辞需要过一遍以确保三方模型的表述在 core 里只出现一次（现状是 SKILL.md 和 shared-collaboration-rules.md 都各写了一遍"三方角色"定义，合并后建议只在 shared-collaboration-rules.md 定义一次，SKILL.md 引用它）。
2. Codebase Snapshot 该不该继续作为"第 8 个独立 mode"暴露在 SKILL.md 的 Mode Selection 里，还是像本机现在这样弱化成内嵌小节——这是产品设计判断，不是 diff 能回答的，建议由 mux 定。
