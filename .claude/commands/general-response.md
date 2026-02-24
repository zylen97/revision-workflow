---
description: "为审稿回复信生成概括性回复（Editor/AE/Reviewer的总体评价），简短感谢+改进方向概述"
---

# General Response — 概括性回复生成

为审稿回复信的概括性评论（Editor、Associate Editor、各 Reviewer 的 Comment #X-0）生成回复文本。

## 输入说明

用户输入 `$ARGUMENTS`，格式为：
- 直接指定目标：`Editor` / `AE` / `R1` / `R2` / `R3`
- 可以一次指定多个：`Editor, AE, R1`
- 如果 `$ARGUMENTS` 为空，停止并提示用户指定要回复的对象

## 步骤 1：读取上下文

1. **读取 response-letter.tex**：找到对应的 `\section*{}` 和 `\reviewercomment{}`，提取审稿人的原始评论文本。如果用户指定了某个对象（如 AE）但 response-letter.tex 中找不到对应的 section，通知用户并跳过该对象
2. **读取修改上下文**：读取 `revision/revision-guide.md`，了解修改集群和总体修改策略
3. **读取 manuscript.tex**：快速浏览论文标题、摘要、关键词，了解研究主题和核心术语

## 步骤 2：确定回复策略

根据不同的回复对象，采取不同的策略：

### Editor 回复策略
- 感谢编辑的考虑和安排审稿（1-2句）
- 简要说明已认真对待所有意见并做了全面修改（1句）
- 针对编辑特别提出的要求（参考 Editor 决定信中的具体指示，如引用期刊近期文献、强化 Body of Knowledge 贡献声明、添加 Practical Applications section 等），概括性回应（1-2句）
- 表示感谢并期待再次审稿（1句）

### Associate Editor 回复策略
- 感谢AE的协调和综合评价（1-2句）
- 针对AE概括的核心改进方向，简要说明已做的对应改进（2-3句）
- 不需要详细列举，但要让AE看到"这些意见我们都理解了"

### Reviewer #X-0（General Assessment）回复策略
- 感谢审稿人的时间和专业评审（1-2句，每位审稿人措辞不同！）
- 对审稿人的总体评价做出回应：如果审稿人给了正面评价，表示感谢；如果指出了核心问题，概括性承认并说明已改进（2-3句）
- 引导到后续逐条回复：说明以下将逐一回应各条意见（1句）

## 步骤 3：生成回复文本

### 语言要求
- **英文**，学术正式语气但不生硬
- **每个回复的感谢措辞必须不同**（这是关键要求！）
- 使用 `\response{}` 包裹所有回复文本
- 不使用 `\manuscriptquote{}` 或 `\lineref{}`（概括性回复不需要引用原文）
- 总长度：3-6句话（约80-150 words）

### 科技写作规范（铁律）
1. **简单主动句式**：一句一意，主动语态，目标 15-20 词/句
2. **逻辑清晰**：句间用 "First,...Second,..." 而非 "Moreover,...Furthermore,..."
3. **克制修饰**：删除不传递新信息的副词/形容词；感谢语句允许一个描述性形容词，正文部分严格执行
4. **中式英语防治 + 压缩冗余**：检查动宾搭配避免直译（"provide reference" → inform, "play a role" → is critical to），删除名词化冗余和空洞修饰语（very/extremely → 删除）。**完整禁忌表见 CLAUDE.md Rule 5-6。**
5. **破折号纪律**：em dash (`---`) 每页至多 1-2 处；用逗号/括号替换 `---such as/including---` 列举模式。**见 CLAUDE.md Rule 7。**

### 感谢措辞库（每次选取或改编，不得重复）
参考以下变体，也可以创造新的（每条限一个描述性形容词）：
- "We thank [the Editor/the Associate Editor/Reviewer #X] for [the constructive feedback / the thorough evaluation / the comments and suggestions]."
- "We appreciate [the time and effort / the careful review] by [the Editor/...]."
- "We are grateful for [the detailed feedback / the professional evaluation] provided by [...]."
- "[The Editor's/The reviewer's] comments helped us improve [the clarity / the rigor] of our manuscript."
- "The [constructive / thorough] feedback from [the Editor/...] strengthened our work."
- "Thank you for [considering our manuscript / the time dedicated to reviewing our work]."

### 关键写作原则
1. **科技写作纪律**：每句简短（不超 25 词）、主动语态、一句一意。删除不传递新信息的副词/形容词。避免中式英语直译和名词化冗余。此规则优先级最高。
2. **避免空洞套话**：不要写"We have carefully revised the manuscript"就完事。要具体到改进了什么方向（如"We clarified the modeling assumptions and unified the notation throughout the manuscript"）
3. **感知评审人的态度**：如果审稿人语气积极（如 "well-structured and theoretically grounded"），回复中要呼应认可；如果审稿人严厉或直接列出问题，回复中要展示对问题的深刻理解
4. **不要过度承诺**：不需要说 "all issues have been fully resolved"，用 "we have addressed..." 即可
5. **保持与研究主题的关联**：提到具体改进方向时，使用论文的核心术语（从 `revision/revision-guide.md` 或 `manuscript.tex` 摘要中提取），不要说太泛泛的话
6. **意识到投稿期刊**：从 CLAUDE.md 或 `revision/revision-guide.md` 中确认投稿期刊名称和读者群特征，回复中体现对该期刊读者群的关注
7. **引用格式**：response-letter.tex **不使用 natbib 或任何引用包**，所有引用均为纯文本。`(Author et al., Year)` 替代 `\citep{}`；`Author et al. (Year)` 替代 `\citet{}`。两位作者写全，三位及以上用 et al.。从项目的 `.bib` 文件中查找作者信息

## 步骤 4：生成输出

### 输出到对话
直接在对话中展示生成的回复文本，格式为：

```
## {Editor / AE / Reviewer #X-0} 回复

### 原始评论摘要
{审稿人原始评论的1-2句概括}

### 生成的回复
\response{回复文本...}

### 使用说明
将上述 `\response{...}` 中的内容替换 response-letter.tex 中对应位置的 `\response{[TO BE FILLED]}`
```

### 同时保存到文件
将回复保存到 `revision/drafts/Response_{target}.md`（如 `revision/drafts/Response_Editor.md`、`revision/drafts/Response_R1-0.md`）。如果 `revision/drafts/` 目录不存在，先创建。

## 步骤 5：批量处理

如果用户指定了多个目标（如 `Editor, AE, R1, R2, R3`），依次生成所有回复，确保：
- 每个回复的感谢措辞**互不相同**
- 每个回复的改进方向概述**各有侧重**（不是复制粘贴）
- 最后汇总展示所有回复
