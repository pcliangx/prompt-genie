# 🧞 提示词精灵 · prompt-genie

> 一个 [Claude Code](https://claude.com/claude-code) **项目级 Skill**：把你的草稿提示词或一句话需求，优化成结构化、可直接复制使用的高质量提示词，供你粘贴到 ChatGPT、豆包、Claude 等各种 AI 里使用。


<img width="3562" height="2318" alt="image" src="https://github.com/user-attachments/assets/70065411-2721-46e6-b6b8-803bcea54b9c" />


---

## 这是什么

你有没有过这种体验：让 AI 帮你写东西，结果总是不对？多半不是 AI 笨，而是**指令没说清**，它只能靠猜。

「提示词精灵」就是帮你把模糊诉求补成清晰指令的助手。你随口说要什么，它产出一段打磨好的提示词——你拿去喂给任何 AI，结果都会更贴近你想要的。

## 特性

- **智能判断**：需求清楚就直接改写；信息不够就先反问几个关键点，不瞎猜。
- **默认通用，可选适配**：默认产出哪个 AI 都能用的版本；你也可以指定**目标 AI**（ChatGPT / 豆包 / Claude）和**场景**（一般搜索 / 深度调研 / 写作 / 代码 / 角色扮演 / 数据提取），它会按对应特点调整。
- **结构化方法**：按角色 / 上下文 / 任务 / 约束 / 输出格式 / 示例 / 推理引导 / 去歧义 八个维度补全。
- **拿来即用**：最终提示词放在独立代码块里，一键复制；下方附「改动要点」，必要时再给一句实用建议。

## 安装

把 `prompt-genie` 这个 skill 目录放进你项目的 `.claude/skills/` 下即可：

```bash
# 在你的项目根目录
mkdir -p .claude/skills
# 方式一：克隆本仓库后复制到项目级
git clone https://github.com/pcliangx/prompt-genie.git /tmp/prompt-genie
cp -r /tmp/prompt-genie/.claude/skills/prompt-genie .claude/skills/
```

**想全局可用（用户级）**，放到 `~/.claude/skills/` 即可，所有项目都能用：

```bash
mkdir -p ~/.claude/skills
git clone https://github.com/pcliangx/prompt-genie.git /tmp/prompt-genie
cp -r /tmp/prompt-genie/.claude/skills/prompt-genie ~/.claude/skills/
```

放好后**新开一个 Claude Code 会话**（skill 在会话启动时被发现），就能用了。

## 怎么用

**最可靠：点名调用**

```
/prompt-genie
```

或在对话里提到它的名字：

```
用提示词精灵帮我优化：帮我写一封催客户付款的邮件，老客户、拖了一个月、别搞僵关系
```

**自然触发**

直接说出意图，通常也会触发：

```
给豆包写一个做深度调研的提示词，主题是国产新能源车出海
```

> 小贴士：Claude（尤其是强模型）经常会直接帮你把提示词改了，不一定主动加载 skill。想稳，就**点名**。

## 输出长什么样

````
（优化后的最终提示词，独立代码块，拿走即用）
````

**改动要点**
- 改了什么 + 为什么（3–5 条）

> 适配：若指定了 AI / 场景，会说明做了哪些调整
> 💡 建议：有价值时附一句实战提示

更多「原话 → 优化结果」的完整范例见 [`.claude/skills/prompt-genie/references/examples.md`](.claude/skills/prompt-genie/references/examples.md)。

## 目录结构

```
.claude/skills/prompt-genie/
├── SKILL.md              # 触发条件 + 工作流程 + 输出格式
└── references/
    ├── principles.md     # 通用提示词工程原则（8 维度）
    ├── ai-profiles.md    # ChatGPT / 豆包 / Claude 适配要点
    ├── scenarios.md      # 各场景结构模板
    └── examples.md       # 黄金示例
```

## 它是怎么做出来的

本 skill 用 [Anthropic 官方 skill-creator](https://github.com/anthropics/claude-code) 的方法迭代打磨：

- **质量验证**：用真实提示词做「带 skill vs 不带 skill」对比，针对差距补了黄金示例。
- **触发优化**：用 20 条触发测试用例跑数据驱动的 description 优化，在零误触发的前提下提升触发率。

---

如果它帮到你，欢迎 ⭐ Star。
