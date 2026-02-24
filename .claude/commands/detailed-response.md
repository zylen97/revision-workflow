---
description: "为审稿回复信生成逐条详细回复（含分析、LaTeX回复文本、原稿修改建议），输出三部分到 drafts/Comment_X-Y.md"
---

# Detailed Response — 逐条详细回复生成

为审稿回复信的具体详细意见（R1-1, R1-2, R2-1, R3-1 等）生成完整的回复，包含分析、LaTeX格式回复文本、原稿修改建议三个部分。

## 输入说明

用户输入 `$ARGUMENTS`，格式为：
- Comment 编号：`R1-1` / `R1-m2` / `R2-3` / `R3-5` 等
- 可选附加指令用 `|` 分隔：`R1-1 | 重点说明我们已经修改了Eq.2` 或 `R2-5 | 参考文献：Smith2020, Jones2023`
- 如果 `$ARGUMENTS` 为空，停止并提示用户指定 Comment 编号

编号约定（映射到 response-letter.tex 中的标记）：
- `R1-1` = Reviewer 1, Major Comment 1 → 搜索 `Comment \#1-1`
- `R1-2` = Reviewer 1, Major Comment 2 → 搜索 `Comment \#1-2`
- `R1-m1` = Reviewer 1, Minor Comment 1 → 搜索 `Comment \#1-m1`
- `R2-3` = Reviewer 2, Comment 3 → 搜索 `Comment \#2-3`
- `R3-5` = Reviewer 3, Comment 5 → 搜索 `Comment \#3-5`

## 步骤 0：解析输入与上下文读取

### 0a. 解析 Comment 编号
从 `$ARGUMENTS` 中提取 Reviewer 编号和 Comment 编号，转换为 response-letter.tex 中的对应格式。同时提取用户附加指令（如果有）。

### 0b. 读取审稿意见
读取 `response-letter.tex`，定位到对应的 `\reviewercomment{}` 块，提取审稿人的完整评论文本。

### 0c. 读取修改上下文
读取 `revision/revision-guide.md`，找到该 Comment 所属的 **Cluster**（C1-C10），了解：
- 该 Cluster 的核心问题
- 同 Cluster 中的其他相关 Comments
- 修改要点和影响的 Section

### 0d. 读取 manuscript.tex
读取 `manuscript.tex` 的完整内容，重点关注：
- 与该 Comment 直接相关的 section/行号（从 revision-guide 中获取）
- 当前文本中存在的问题（审稿人指出的）
- 相关的方程、表格、图表

### 0e. 读取参考文献
读取项目的 `.bib` 文件（从 CLAUDE.md 中的文件结构说明或 `manuscript.tex` 的 `\bibliography{}` 命令中确认文件名），了解可用的引用资源。

### 0f. 检查已有回复
如果 `revision/drafts/` 为空或无同 Cluster 的回复文件（如本 Comment 是该 Cluster 的锚点且是第一条处理的），跳过本步。

检查 `revision/drafts/` 目录中是否已存在同 Cluster 其他 Comment 的回复文件，避免重复修改或矛盾表述。如果有，读取其 Part 2（回复文本）了解已承诺的修改。
- 如果已有同 Cluster 但**不同 Reviewer** 的回复，额外确保：
  (a) 引用相同的修改稿内容（一致性）
  (b) 回复角度针对该审稿人的具体提问（不重复锚点回复的组织方式）
  (c) 不包含跨审稿人的直接引用（如"As discussed in our response to Comment #1-1..."）

## 步骤 1：深入分析（→ Part 1 of 输出）

对审稿人的评论进行深入分析：

### 分析框架
1. **核心关切**：审稿人本质上在问什么？（1-2句话）
2. **原稿中的问题**：引用具体行号和文本，说明当前存在什么问题
3. **集群语境**：这条意见与同 Cluster 其他意见的关系
   - **引用类型判断**：当前 Comment 与同 Cluster 中已有/待写回复是同审稿人内引用（可直接引用）还是跨审稿人引用（必须自含式回复）
4. **回复策略**：
   - 如果是"需要修改原稿"类型：具体说明要改什么、在哪里改
   - 如果是"需要解释说明"类型：列出论证逻辑和可引用的支持材料
   - 如果是"需要补充内容"类型：列出需要添加的内容及位置
5. **潜在风险**：回复中需要避免的陷阱（如过度承诺、与其他回复矛盾等）

## 步骤 2：生成 LaTeX 回复文本（→ Part 2 of 输出）

### 科技写作规范（铁律 — 适用于所有生成文本）

**每一句**回复文本必须通过以下检查：
1. **简单主动句式** — 拆分 "We revised X, which also addresses Y" 为两句；"We added X" 而非 "X was added"
2. **逻辑清晰** — 句间用 "First,...Second,..." 或因果连接词，不堆叠 "Moreover,...Furthermore,...Additionally,..."
3. **短句** — 目标 15-20 词，超过 25 词必须拆分
4. **克制修饰** — 删除 "significantly"、"comprehensively" 等不传递新信息的副词；形容词同理
5. **中式英语防治** — 检查每句的动宾搭配是否为直译（"improve the level" → enhance, "provide reference" → inform, "play an important role" → is critical to）；中式句型整句重写（"With the development of..." → 具体因果句）。**完整搭配禁忌表见 CLAUDE.md Rule 5。**
6. **压缩冗余** — 删除名词化冗余（"carry out an investigation" → investigate）和空洞修饰语（very/extremely/basically/novel/important → 删除或量化）。**完整黑名单见 CLAUDE.md Rule 6。**
7. **破折号纪律** — em dash (`---`) 每页至多 1-2 处；用逗号/括号/冒号/拆句替换 `---such as/including/from---` 模式。连续段落同一句型不超过 2 次。**见 CLAUDE.md Rule 7。**

❌ BAD:
```
We have comprehensively revised the definition section to thoroughly
address the reviewer's concern regarding the lack of conceptual
clarity, which was indeed a significant limitation.
```

✅ GOOD:
```
We revised the definition section. We now distinguish three concepts:
X, Y, and Z. Each has an operational definition (Lines 101--115).
```

> 感谢语句允许一个描述性形容词（如 "constructive suggestion"），紧随其后的正文严格执行。

### 回复结构（严格遵循）

```latex
\responseheader

\response{[感谢/认同开头 — 1-2句，措辞独特]}

\response{First, [第一个方面的回应]. [展开说明2-3句].}

\manuscriptquote{[如果对原稿做了修改，引用修改后的文本] \lineref{Lines XXX--YYY}}

\response{Second, [第二个方面的回应]. [展开说明].}

\manuscriptquote{[引用修改后的文本] \lineref{Lines XXX--YYY}}

\response{[总结句 — 说明这些修改如何回应了审稿人的关切]}
```

**关键格式规则**：
- `\response{}`、`\manuscriptquote{}` **不可嵌套**
- `\response{}` 在 `\manuscriptquote{}` 之前关闭，之后重新打开
- **`\response{}` 内部不能有空行**（会导致 `\par` 错误）。段落分隔用关闭再重开 `\response{}` 实现
- `\lineref{}` 放在 `\manuscriptquote{}` 的**内容末尾**：`\manuscriptquote{...text. \lineref{Lines XXX--YYY}}`
- 如果某一点不涉及原稿修改，不需要 `\manuscriptquote{}` 和 `\lineref{}`，直接在 `\response{}` 内继续

### 引用格式（response-letter.tex 专用）
**response-letter.tex 不使用 natbib 或任何引用包**，所有引用均为纯文本：
- `\citep{}` 风格 → 直接写 `(Author et al., Year)`
- `\citet{}` 风格 → 直接写 `Author et al. (Year)`
- 多引用用分号分隔：`(A et al., Year; B and C, Year)`
- 两位作者写全：`(Smith and Jones, 2023)`；三位及以上用 et al.：`(Wang et al., 2024)`
- 从项目的 `.bib` 文件中查找实际作者和年份，确保姓名和年份准确
- `\response{}` 和 `\manuscriptquote{}` 内均使用相同的纯文本引用格式

### 感谢/开头措辞（每个 Comment 不同）

从以下变体中选取或改编，**禁止**与同一会话中已生成的其他回复重复。
每条感谢**限一个**描述性形容词，紧跟具体话题：

- "We thank the reviewer for this comment on [具体话题]."
- "We appreciate the reviewer's attention to [具体方面]."
- "This is a constructive observation. We address it as follows."
- "The reviewer raises a valid concern about [具体问题]. We respond below."
- "We are grateful for this suggestion on [具体内容]."
- "Thank you for the careful examination of [具体方面]."
- "This comment helped us improve [具体内容]. We respond as follows."
- "We welcome this feedback on [具体话题] and respond below."

### 逐点回应要求
- 使用 **"First, ... Second, ... Third, ..."** 结构（而非 "Moreover, Furthermore, Additionally"）
- 每个点要具体到：**做了什么修改** / **为什么这样做** / **效果是什么**
- 引用具体 section 或 subsection 时，使用 **``Section名称'' section** 格式（section 名称首字母大写），如 `the ``Introduction'' section`、`the ``Literature Review'' section`。不要裸写 section 名：❌ `the Introduction`，✅ `the ``Introduction'' section`
- 如果涉及原稿修改，紧接其后放 `\manuscriptquote{}` 和 `\lineref{}`
- `\manuscriptquote{}` 中的内容应该是**修改后**的文本（如果原稿已修改）或**当前**文本（如果只是解释）
- `\lineref{}` 中的行号写入**当前稿件**的行号。如果尚未修改原稿，标注为 `\lineref{Lines [TBD]}`
  - **注意**：后续 Cluster 的修改可能导致行号偏移，不必回头更新旧的 lineref。用户按需自行核对

### 论证策略
- **不要只说"我们改了"**，要说明**为什么这样改**以及**这个改动如何解决审稿人的问题**
- 如果审稿人的建议无法完全采纳，要礼貌但坚定地解释原因，并提出替代改进
- 引用文献支持论证时，使用具体的作者和年份（如 "Following Smith and Jones (2023), we..."），不要说泛泛的 "as the literature suggests"
- 对于方法论/假设类问题，要从**学科理论基础**和**文献先例**两个角度论证
- **同审稿人内引用**：同一 Reviewer 的后续 Comment 可引用该 Reviewer 的前面回复（"As discussed in our response to Comment #1-1 above, ..."）。例如 R1-4 可引用 R1-3，R1-m2 可引用 R1-2。
- **跨审稿人引用**：**禁止**直接引用其他 Reviewer 的回复（审稿人只阅读自己那部分）。如果同 Cluster 的问题分布在多位审稿人中，每条回复必须**自含式 (self-contained)**：
  - 包含足够的实质内容让该审稿人独立满意
  - 根据该审稿人的**具体提问角度**重新组织论证（不是复制粘贴锚点回复）
  - 可引用相同的 `\manuscriptquote{}` 和 `\lineref{}`（指向同一份修改稿）
  - 比锚点回复更精简，但核心论证完整
  - 可选用软性措辞："This concern was also noted by other reviewers, and we have addressed it in the revised manuscript."——但实质内容不能依赖此句

### 情境意识（关键！）
在撰写回复时，始终牢记：
1. **Comment 所涉及内容的情境和位置**：这条意见针对的是 Introduction、Methods、Results、还是 Discussion？不同位置的回复侧重点不同
2. **本研究的情境**：从 `revision/revision-guide.md` 的论文基本信息和 `manuscript.tex` 的摘要中获取研究主题和核心框架描述
3. **投稿期刊**：从 CLAUDE.md 或 `revision/revision-guide.md` 中获取期刊名称，始终考虑该期刊读者群关心的重点（如可操作性、实践意义、管理启示、理论贡献等）

## 步骤 3：原稿修改建议（→ Part 3 of 输出）

如果该 Comment 需要修改 `manuscript.tex`，提供具体的修改建议：

### 修改说明格式
```
### 原稿修改

#### 修改 1: [简要描述]
- **位置**: manuscript.tex Lines XXX--YYY
- **类型**: 新增 / 修订 / 删除 / 重组
- **当前文本**:
  ```latex
  [当前原文]
  ```
- **修改为**:
  ```latex
  [修改后的文本]
  ```
- **理由**: [为什么这样改]

#### 修改 2: [简要描述]
...
```

### 修改原则
- **只列出与当前 Comment 直接相关的修改**，不要越界修改其他问题
- 如果修改与其他 Cluster 重叠（如 C2 记号审计影响多处），注明 "This modification also addresses Comment #X-Y (Cluster CX)"
- 如果不需要修改原稿（纯解释类回复），明确声明 "No manuscript modifications are required for this comment."
- `manuscript.tex` 的行号以当前版本为准

### manuscript.tex 修改文本规范
Part 3 建议的修改文本同样遵循科技写作七条规范（见本文件"科技写作规范"一节）。方法描述中的被动语态（如 "samples were collected"）属学科惯例，可保留。
- 如果涉及新增引用，在修改建议中列出需要添加到项目 `.bib` 文件的 BibTeX 条目
- 如果涉及 MATLAB/图表修改（用户负责的部分），标注 "**需要用户操作**"

## 步骤 4：组装输出文件

将三个部分组装到 `revision/drafts/Comment_{X-Y}.md`（如 `revision/drafts/Comment_R1-1.md`）。如果 `revision/drafts/` 目录不存在，先创建。

```markdown
# Comment {X-Y} 回复

> **审稿人**: Reviewer #{X}
> **意见编号**: {Comment编号和简短标题}
> **所属集群**: C{N} — {Cluster名称}
> **日期**: {当前日期}

---

## Part 1: 分析与策略

{步骤1的完整分析内容，结构语言用中文，专有名词和引用内容用英文}

---

## Part 2: 回复正文 (LaTeX)

以下文本可直接粘贴到 `response-letter.tex`，替换对应位置的 `\response{[TO BE FILLED]}`。

```latex
{步骤2的完整LaTeX回复文本}
```

---

## Part 3: 原稿修改建议

{步骤3的修改建议，或"本条意见无需修改原稿。"}

---

## 交叉引用

- **同审稿人相关意见**: {同一 Reviewer 的其他 Comment，可直接引用}
- **跨审稿人相关意见**: {其他 Reviewer 的 Comment，必须自含式回复，不可直接引用}
- **潜在冲突**: {如果有与其他回复可能冲突的地方}
- **依赖关系**: {如果需要先完成其他修改才能确定本条回复}
```

## 步骤 5：向用户展示

1. 在对话中展示 **Part 1** 的分析摘要（关键判断和策略选择）
2. 在对话中展示 **Part 2** 的 LaTeX 回复文本（可直接复制粘贴）
3. 如果有 **Part 3** 的原稿修改，列出修改摘要
4. 提醒用户：
   - 检查 `\lineref{}` 中的行号是否为当前值或已标注 [TBD]
   - 检查 `\manuscriptquote{}` 中引用的文本是否与最新 manuscript.tex 一致
   - 如果涉及 MATLAB/图表修改（用户负责的部分），明确标注
5. 告知完整输出已保存到 `revision/drafts/Comment_{X-Y}.md`

## 质量检查清单

在完成每个回复前，自检以下要点：

- [ ] 感谢措辞与本会话中其他已生成的回复不重复
- [ ] 每个论证点都有具体的支撑（文献/数学推导/逻辑论证）
- [ ] `\response{}`、`\manuscriptquote{}`、`\lineref{}` 格式正确且不嵌套
- [ ] 回复直接回应了审稿人的核心关切，没有答非所问
- [ ] 如果涉及原稿修改，Part 2 和 Part 3 的内容一致
- [ ] 行号引用准确（或已标注为 [TBD]）
- [ ] 没有过度承诺（用 "We addressed this concern" 而非 "We have completely resolved this issue"）
- [ ] 保持与同 Cluster 其他回复的一致性
- [ ] 回复体现了对目标期刊读者群的关注（从 CLAUDE.md 或 revision-guide.md 中确认期刊名称和读者群特征）
- [ ] 回复中使用了论文的核心术语（从 revision-guide.md 或 manuscript.tex 摘要中提取关键术语）
- [ ] **科技写作规范**：每句不超 25 词、主动语态、无不必要修饰词、句间逻辑连接清晰、动宾搭配无直译、无名词化冗余和空洞修饰语、em dash 每页≤2 处
- [ ] **感谢语句**：开头最多一个描述性形容词，无堆叠修饰
- [ ] **交叉引用合规**：如果是跨审稿人的同 Cluster 回复，确认文中无任何形式的 "see our response to Comment #X-Y"、"as discussed in Comment #X-Y" 等对其他 Reviewer Comment 的引用。仅允许软性措辞（"This concern was also noted by other reviewers"）且实质内容完全自含
