# 提示词优化助手（prompt-optimizer）Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** 在本项目内构建一个 Claude Code 项目级 skill，把用户的草稿提示词或一句话需求优化成结构化高质量提示词，供复制到各种 AI 使用。

**Architecture:** 纯 Markdown skill，不写代码、不调外部 API。一个精简的 `SKILL.md` 承载触发条件、决策逻辑、输出格式；知识库拆到 `references/` 三个文档（通用原则、AI 适配、场景模板），按需加载。

**Tech Stack:** Claude Code Skill（Markdown + YAML frontmatter）。

---

## File Structure

```
<项目根>/.claude/skills/prompt-optimizer/
├── SKILL.md              # 主文件：frontmatter + 工作流 + 输出格式
└── references/
    ├── principles.md     # 通用提示词工程原则
    ├── ai-profiles.md    # ChatGPT / 豆包 / Claude / 通用 适配要点
    └── scenarios.md      # 6 个场景结构模板
```

每个文件单一职责：主文件只放"怎么做"的逻辑，参考文档只放"知识"。新增 AI/场景只改对应参考文档。

**关于 git：** 当前项目非 git 仓库。Task 1 会 `git init` 以便每个任务后提交。若用户不想要版本管理，可跳过所有 `git commit` 步骤，其余不变。

---

### Task 1: 初始化项目与目录结构

**Files:**
- Create: `.claude/skills/prompt-optimizer/references/`（目录）

- [ ] **Step 1: 初始化 git（可选，若用户不要版本管理则跳过本步与后续 commit）**

Run:
```bash
cd /Users/pc2026/Documents/DevTools/Prompt-Engineering && git init && printf '.DS_Store\n' > .gitignore
```
Expected: `Initialized empty Git repository ...`

- [ ] **Step 2: 创建 skill 目录结构**

Run:
```bash
mkdir -p /Users/pc2026/Documents/DevTools/Prompt-Engineering/.claude/skills/prompt-optimizer/references
```
Expected: 无报错；目录存在。

- [ ] **Step 3: 提交已存在的设计/计划文档**

```bash
cd /Users/pc2026/Documents/DevTools/Prompt-Engineering
git add docs .gitignore
git commit -m "docs: add prompt-optimizer design and plan"
```

---

### Task 2: 编写通用原则参考文档 principles.md

**Files:**
- Create: `.claude/skills/prompt-optimizer/references/principles.md`

- [ ] **Step 1: 写入完整内容**

文件 `.claude/skills/prompt-optimizer/references/principles.md` 内容：

````markdown
# 通用提示词工程原则

优化任何提示词时，依次检查并补全这些维度。不是每条都要硬塞，按需取用，目标是消除歧义、让 AI 准确理解意图。

## 1. 角色 / 视角
给 AI 一个合适的专家身份，让它用对应的知识和语气作答。
- 弱："帮我分析市场" → 强："你是一位资深行业分析师，擅长用数据支撑结论"

## 2. 背景 / 上下文
补全 AI 不知道但完成任务必需的信息：是谁、为什么、给谁看、已有什么。
- 加上"这份报告给不懂技术的管理层看"会显著改变输出。

## 3. 明确任务
把模糊诉求拆成具体、可执行的指令；用动词开头，一次聚焦一个主目标。
- 弱："写点关于 AI 的东西" → 强："写一篇 800 字、面向初学者、解释什么是大语言模型的科普短文"

## 4. 约束与要求
范围、长度、语气、风格、必须项、禁止项。
- 例："不超过 5 条；每条一句话；不要专业术语；中文输出"

## 5. 输出格式
明确结构：表格 / 有序列表 / 分段标题 / JSON / Markdown 等。
- 例："用 Markdown 表格输出，列为：方案 | 优点 | 风险"

## 6. 示例（Few-shot）
当格式或风格难以言说时，给 1–2 个输入→输出示例，胜过长篇描述。

## 7. 推理引导
复杂任务加一句"请分步推理后再给结论"或"先列要点再展开"，能提升准确度。

## 8. 去歧义 / 补隐含需求
找出原始需求里没说但显然需要的东西，主动补上或在反问里确认。
- 用户说"翻译这段"，但没说目标语言、语气正式还是口语——这就是要确认或合理默认的点。

## 优化时的取舍
- 短任务别硬套全部 8 条，避免提示词臃肿。
- 保留用户原意，不要替用户改变需求本身。
- 优先补"最影响结果"的维度（通常是任务清晰度、输出格式、关键约束）。
````

- [ ] **Step 2: 校验内容写入**

Run:
```bash
head -5 /Users/pc2026/Documents/DevTools/Prompt-Engineering/.claude/skills/prompt-optimizer/references/principles.md
```
Expected: 显示 `# 通用提示词工程原则` 等开头内容。

- [ ] **Step 3: Commit**

```bash
cd /Users/pc2026/Documents/DevTools/Prompt-Engineering
git add .claude/skills/prompt-optimizer/references/principles.md
git commit -m "feat: add prompt-optimizer principles reference"
```

---

### Task 3: 编写 AI 适配参考文档 ai-profiles.md

**Files:**
- Create: `.claude/skills/prompt-optimizer/references/ai-profiles.md`

- [ ] **Step 1: 写入完整内容**

文件 `.claude/skills/prompt-optimizer/references/ai-profiles.md` 内容：

````markdown
# 各 AI 适配要点

默认走"通用"。仅当用户明确指定目标 AI 时，叠加对应要点。这些是倾向性建议，不是硬规则。

## 通用（默认）
- 结构清晰：角色 + 任务 + 约束 + 输出格式，分段或用标题。
- 不依赖任何单一模型的私有语法。
- 适合粘贴到任何主流 AI。

## ChatGPT（GPT 系）
- 对 system/role 设定敏感，开头用"你是……"效果好。
- 喜欢明确的分步指令和编号列表。
- 要结构化数据时，直接要求"输出 JSON / 表格"通常很听话。
- 长任务可加"Let's think step by step / 请分步思考"。

## 豆包（Doubao）
- 中文语境表现好，提示词用自然流畅的中文即可。
- 偏好直接、口语化但信息完整的指令。
- 深度任务里明确要求"分点、给出处、结构化"能提升质量。
- 避免堆砌过多英文术语。

## Claude
- 擅长长上下文和复杂指令，可以给较丰富的背景。
- 对 XML 风格标签（如 `<context>` `<task>` `<output_format>`）响应好，利于分隔结构。
- 倾向于谨慎、全面；要简洁时需明确"简短作答"。
- 适合要求"先思考再回答"的复杂推理任务。

## 扩展方式
新增一个 AI：在本文件加一个 `## 名称` 小节，列 3–5 条该模型的提示词倾向即可，SKILL.md 无需改动。
````

- [ ] **Step 2: 校验内容写入**

Run:
```bash
grep -c '^## ' /Users/pc2026/Documents/DevTools/Prompt-Engineering/.claude/skills/prompt-optimizer/references/ai-profiles.md
```
Expected: `5`（通用、ChatGPT、豆包、Claude、扩展方式）。

- [ ] **Step 3: Commit**

```bash
cd /Users/pc2026/Documents/DevTools/Prompt-Engineering
git add .claude/skills/prompt-optimizer/references/ai-profiles.md
git commit -m "feat: add prompt-optimizer ai-profiles reference"
```

---

### Task 4: 编写场景模板参考文档 scenarios.md

**Files:**
- Create: `.claude/skills/prompt-optimizer/references/scenarios.md`

- [ ] **Step 1: 写入完整内容**

文件 `.claude/skills/prompt-optimizer/references/scenarios.md` 内容：

````markdown
# 场景结构模板

用户指定场景时，按对应骨架组织优化后的提示词。默认（未指定）用"一般搜索问答"或最贴近的通用结构。

## 一般搜索问答
目标：快速拿到准确、直接的答案。
骨架：明确问题 + 限定范围/时效 + 要点式输出 + 必要时要出处。
要点：别让 AI 展开太长；要求"先给结论再补充"。

## 深度调研
目标：系统、可信、有结构的研究性回答。
骨架：
- 角色：相关领域资深研究者/分析师
- 任务：围绕 X 做系统调研
- 维度：要求覆盖背景 / 现状 / 关键数据 / 不同观点 / 风险或局限 / 结论
- 证据：关键结论需给出处或注明不确定性
- 输出：分章节的结构化报告
要点：强调"多角度、标注来源、区分事实与推测"。

## 写作 / 文案
目标：成稿可用的文字。
骨架：体裁 + 受众 + 语气风格 + 字数 + 核心信息/卖点 + 结构要求。
要点：给出"要避免的风格"往往和"想要的风格"同样有用。

## 代码 / 技术
目标：可运行、可维护的技术方案或代码。
骨架：语言/框架/版本 + 要解决的问题 + 输入输出 + 约束（性能/风格/依赖）+ 是否要解释。
要点：要求"先说思路再给代码""给出关键注释""指出边界情况"。

## 角色扮演
目标：稳定、沉浸的角色对话。
骨架：角色设定（身份/性格/说话风格）+ 场景背景 + 互动规则 + 不出戏约束。
要点：明确"始终保持角色""不要解释自己是 AI"。

## 数据提取 / 结构化输出
目标：从文本中抽取信息为固定结构。
骨架：明确字段及类型 + 给出目标 JSON/表格 schema + 缺失值如何处理 + 只输出结构不要多余文字。
要点：强烈建议给一个示例输入→输出；要求"严格按 schema，不要额外解释"。

## 扩展方式
新增场景：在本文件加一个 `## 场景名` 小节，写清目标 + 骨架 + 要点，SKILL.md 无需改动。
````

- [ ] **Step 2: 校验内容写入**

Run:
```bash
grep -c '^## ' /Users/pc2026/Documents/DevTools/Prompt-Engineering/.claude/skills/prompt-optimizer/references/scenarios.md
```
Expected: `7`（6 个场景 + 扩展方式）。

- [ ] **Step 3: Commit**

```bash
cd /Users/pc2026/Documents/DevTools/Prompt-Engineering
git add .claude/skills/prompt-optimizer/references/scenarios.md
git commit -m "feat: add prompt-optimizer scenarios reference"
```

---

### Task 5: 编写主文件 SKILL.md

**Files:**
- Create: `.claude/skills/prompt-optimizer/SKILL.md`

- [ ] **Step 1: 写入完整内容**

文件 `.claude/skills/prompt-optimizer/SKILL.md` 内容：

````markdown
---
name: prompt-optimizer
description: 优化提示词，把用户的草稿或一句话需求改写成结构化高质量提示词，供复制到 ChatGPT/豆包/Claude 等 AI 使用。当用户说"优化提示词/帮我写提示词/润色 prompt/把这段需求变成提示词/给某AI写个提示词"时使用。
---

# 提示词优化助手

把用户的草稿提示词或一句话需求，优化成结构化、可直接复制使用的高质量提示词。

## 工作流程

### ① 判断信息是否充分
检查 4 个关键点：**任务目标 / 受众或用途 / 输出格式 / 关键约束**。
- 缺 2 个及以上 → 先反问（见②）。
- 基本齐全 → 直接优化（跳到③）。

### ② 必要时反问（最多一轮）
集中一次性问完，尽量用多选项形式，简洁不啰嗦。只问真正影响结果的点。
若输入太模糊连问都难，先问一句："你想让 AI 帮你做什么？"

### ③ 识别可选参数
- **目标 AI**：用户提到 ChatGPT / 豆包 / Claude 时，读取 `references/ai-profiles.md` 对应小节叠加适配；未提到走"通用"。
- **场景**：用户提到 搜索 / 深度调研 / 写作 / 代码 / 角色扮演 / 数据提取 时，读取 `references/scenarios.md` 对应骨架；未提到用通用结构。

### ④ 套用优化方法
读取 `references/principles.md`，按 8 个维度（角色/上下文/任务/约束/格式/示例/推理引导/去歧义）补全。按需取用，别把短任务撑臃肿。保留用户原意，不替用户改变需求本身。

### ⑤ 输出
严格按下面格式：

最终提示词放在独立代码块里（方便一键复制）：

```
（优化后的最终提示词）
```

代码块下方附：

**改动要点**
- （3–5 条：改了什么 + 为什么）

> 适配：若用了某 AI/场景适配，附一句说明（无则省略）。

## 迭代
用户对结果不满意时，基于上一版做针对性修改，不要从零重写。

## 语言
默认跟随用户输入语言（通常中文）。
````

- [ ] **Step 2: 校验 frontmatter 合法**

Run:
```bash
head -4 /Users/pc2026/Documents/DevTools/Prompt-Engineering/.claude/skills/prompt-optimizer/SKILL.md
```
Expected: 第 1 行为 `---`，第 2 行 `name: prompt-optimizer`，第 3 行 `description:` 开头，第 4 行 `---`。

- [ ] **Step 3: Commit**

```bash
cd /Users/pc2026/Documents/DevTools/Prompt-Engineering
git add .claude/skills/prompt-optimizer/SKILL.md
git commit -m "feat: add prompt-optimizer SKILL.md main file"
```

---

### Task 6: 验证 skill（3 个用例）

**Files:** 无（验证任务）

> **重要：** Claude Code 在会话启动时发现项目级 skill。要实际测试，需在项目目录下**新开一个 Claude Code 会话**，让它加载 `.claude/skills/prompt-optimizer/`。下面用例可在该新会话中手动执行。

- [ ] **Step 1: 验证目录结构完整**

Run:
```bash
find /Users/pc2026/Documents/DevTools/Prompt-Engineering/.claude/skills/prompt-optimizer -type f | sort
```
Expected: 列出 4 个文件——SKILL.md、references/ai-profiles.md、references/principles.md、references/scenarios.md。

- [ ] **Step 2: 用例 A — 模糊输入应触发反问**

输入："帮我写个提示词"
Expected: skill 触发，并反问缺失的关键信息（任务目标/用途/格式/约束），而不是直接瞎编一段提示词。

- [ ] **Step 3: 用例 B — 清晰输入应直接改写**

输入："帮我优化这个提示词：写一篇 800 字、给初学者看的、解释什么是大模型的科普文，分 3 段"
Expected: 不反问，直接输出——独立代码块里的优化提示词 + 下方"改动要点"。

- [ ] **Step 4: 用例 C — 带 AI + 场景应正确适配**

输入："给豆包写一个做深度调研用的提示词，主题是国产新能源车出海"
Expected: 输出体现深度调研骨架（多维度/标注来源/结构化报告）与豆包适配（自然中文、分点），格式同上。

- [ ] **Step 5: 记录验证结果**

在三个用例都通过后，本任务完成。若某用例不达标，回到对应 Task（②反问逻辑→Task 5；场景/AI 适配→Task 3/4）修正后重测。

---

## Self-Review

**Spec coverage（对照设计文档逐节核对）：**
- §1 目录结构 → Task 1 + 各文件创建任务 ✅
- §2 触发方式 → Task 5 SKILL.md frontmatter description ✅
- §3 核心工作流（智能判断/反问规则）→ Task 5 工作流 ①②③④⑤ ✅
- §4 优化方法 8 维度 → Task 2 principles.md ✅
- §5 参数分发（AI + 场景）→ Task 3 + Task 4 + Task 5 ③ ✅
- §6 输出格式（代码块 + 改动要点）→ Task 5 ⑤ ✅
- §7 异常处理（太模糊/很长/迭代/语言）→ Task 5 ②、迭代、语言小节 ✅
- §8 验证 3 用例 → Task 6 ✅
- §9 YAGNI（不写代码/不调 API/不进全局）→ 全程纯 Markdown，建在项目 `.claude/skills/` ✅

**Placeholder scan:** 无 TBD/TODO；每个文件内容均完整给出。✅

**一致性:** skill 名 `prompt-optimizer` 在 frontmatter、目录名、所有 commit/路径中一致；引用路径 `references/principles.md`、`references/ai-profiles.md`、`references/scenarios.md` 与创建任务一致。✅
````
