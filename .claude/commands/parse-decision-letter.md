---
description: "将原始决定信解析为规范化的 comment-letter-clean.md，自动识别结构、编号、分类"
---

# Parse Decision Letter — 原始决定信解析

将用户粘贴的原始决定信（从邮件或投稿系统复制）解析为规范化的意见列表，生成 `revision/comment-letter-clean.md`。

## 输入说明

用户输入 `$ARGUMENTS`，格式为：
- 无参数：直接执行解析
- 可选附加指令：`R2 没有分 Major/Minor，请按内容判断` 或 `审稿人编号是 #2, #3, #5，不是连续的`

## 步骤 0：前置检查

1. **检查输入文件**：确认 `revision/comment-letter.md` 存在且非空
2. **检查输出文件**：如果 `revision/comment-letter-clean.md` 已存在，询问用户是否覆盖

## 步骤 1：读取原始决定信

读取 `revision/comment-letter.md` 的完整内容。

## 步骤 2：识别结构

自动检测以下结构（适应不同投稿系统格式）：

### 2a. 识别角色边界

检测并分离：
- **Editor**：通常包含 "Decision"、"Editor's comments" 等关键词
- **Associate Editor**：通常包含 "Associate Editor"、"AE" 等关键词
- **Reviewer #N**：通常包含 "Reviewer #1"、"Reviewer 2"、"Reviewer #3" 等模式

### 2b. 识别意见结构

对每位 Reviewer，检测：
- 是否已分 Major/Minor（如 "Major Comments"、"Minor Comments" 标题）
- 是否已编号（如 "1.", "2.", "Comment 1" 等模式）
- 是否为连续段落（无编号、无分隔）

### 2c. 识别 Q&A 区域

检测标准化问题区域（如 ASCE EM、Elsevier EES 的 Q&A 表格），提取包含实质性回答的问题。

## 步骤 3：删除样板文字

删除以下内容：
- 邮件头（From/To/Subject/Date 等）
- 系统页脚（ unsubscribe、privacy policy 等）
- 日历附件说明（ .ics 文件说明）
- 重复的系统通知

**保留**所有实质性内容，包括：
- Editor/AE/Reviewer 的完整意见
- 审稿人的原始措辞（包括拼写错误）
- 审稿人的段落组织

## 步骤 4：编号处理

### 4a. 审稿人已编号

保留原始编号，转换为标准格式：
- `1.` → `R1-1`
- `Comment 2` → `R1-2`
- `Major Comment 1` → `R1-1`

### 4b. 审稿人已分 Major/Minor

使用 `RN-K` / `RN-mK` 格式：
- Major Comment 1 → `R1-1`
- Major Comment 2 → `R1-2`
- Minor Comment 1 → `R1-m1`

### 4c. 审稿人未分 Major/Minor

**不强行分类**，使用连续编号 `RN-1, RN-2, ...`

判断是否需要拆分为独立 Comment：
1. **一个独立关切 = 一条 Comment**：每个段落讨论不同话题时拆分
2. **同一段内多个独立问题**：涉及不同位置或不同性质的问题拆分
3. **保留原文**：每条 Comment 保留完整的原始英文文本，不改写、不缩减

### 4d. 审稿人编号保留

如果期刊系统编号为 #2, #3, #5（非连续），保留原始编号：
- Reviewer #2 → R2-1, R2-2, ...
- Reviewer #3 → R3-1, R3-2, ...
- 不重编为 #1, #2, #3

## 步骤 5：处理 Q&A 区域

如果决定信包含 Q&A 部分：

1. **提取表格**：识别问题行和审稿人回答列
2. **筛选实质性回答**：回答不仅仅是 "Yes"/"No"，包含额外解释或建议
3. **标注来源**：在对应的 Comment 中标注 "(from Q&A)"

## 步骤 6：生成输出文件

### 输出路径

`revision/comment-letter-clean.md`

### 输出格式

严格遵循以下结构：

```markdown
# 决定信（规范化） — [JOURNAL_ABBREVIATION]-[MANUSCRIPT_ID]

> **本文件由 `/parse-decision-letter` 自动生成。** 如需调整解析结果，请修改 `revision/comment-letter.md` 后重新执行。

- **决定**: [Major Revision / Minor Revision / Reject & Resubmit]
- **收到日期**: [DATE]
- **截止日期**: [DATE]
- **Editor**: [NAME]
- **Associate Editor**: [NAME] (如果没有 AE，删除此行)

---

## Editor 意见

[Editor 意见原文]

---

## Associate Editor 意见

> 如果决定信中没有 AE，删除此 section。

[AE 意见原文]

---

## Reviewer #1 意见

### Major Comments

**R1-1.** [意见原文]

**R1-2.** [意见原文]

### Minor Comments

**R1-m1.** [意见原文]

---

## Reviewer #2 意见

**R2-1.** [意见原文]

**R2-2.** [意见原文]

---

## 意见编号约定

| 来源 | 格式 | 示例 |
|--------|--------|---------|
| Editor | `Editor` | `Editor` |
| Associate Editor | `AE` | `AE` |
| Reviewer #N, Major K | `RN-K` | `R1-1`, `R2-3` |
| Reviewer #N, Minor K | `RN-mK` | `R1-m1`, `R3-m2` |
| Reviewer #N, General | `RN-0` | `R1-0` |

---

## 初步分类工作区

> 完成聚类分析并填入 `revision-guide.md` 后，本节可删除。

| ID | 类型 | 优先级 | 初步 Cluster | 备注 |
|----|------|--------|-------------|------|
| R1-1 | [Modify/Explain/Supplement] | [Highest/High/Medium/Low] | [C?] | |
| R1-2 | | | | |
| R1-m1 | | | | |
| R2-1 | | | | |
| ... | | | | |

**类型说明**: Modify = 需改原稿 | Explain = 仅需回复信解释 | Supplement = 新增内容
**优先级说明**: Highest = 全局影响 | High = 核心关切 | Medium = 改善展示 | Low = 微小修改
```

## 步骤 7：向用户展示

1. **摘要统计**：告知用户解析结果
   - 识别到几位审稿人
   - 每位审稿人有多少条 Major/Minor 意见
   - 是否有 Q&A 区域被提取
   - 审稿人编号是否连续

2. **输出路径**：告知文件已保存到 `revision/comment-letter-clean.md`

3. **下一步提示**：
   > 解析完成。下一步：按 `guide.md` Chapter 4 进行逐条意见分类（类型 + 优先级），然后 Chapter 5 进行 Cluster 聚类分析。

## 质量检查清单

- [ ] 所有审稿人均被识别并编号
- [ ] Editor/AE 意见完整提取
- [ ] 审稿人原始编号被保留（不重编）
- [ ] 审稿人已分 Major/Minor → 使用 RN-K/RN-mK 格式
- [ ] 审稿人未分 → 使用连续编号 RN-1, RN-2, ...
- [ ] Q&A 区域的实质性回答已提取
- [ ] 初步分类工作区表格已按解析结果预填 ID 列
