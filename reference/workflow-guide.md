# Workflow Operational Reference

Detailed operational procedures for the revision execution phase.
For the **complete guide** (including initialization and template population), see **`guide.md`** (in the revision-workflow-guide root).

This file covers: closed-loop execution details, feedback mechanisms, pre-submission checklists, recovery procedures, and skill definitions.

---

## Six-Step Closed Loop Per Cluster

Every cluster follows identical steps in order. Skipping steps creates technical debt.

### Step 1: Generate Response Drafts

Invoke `/detailed-response R1-1`. Output to `revision/drafts/Comment_R1-1.md`:
- **Part 1**: Analysis (reviewer's real question, strategy)
- **Part 2**: LaTeX response (complete `\response{}` + `\manuscriptquote{}` + `\lineref{}`)
- **Part 3**: Manuscript modifications (specific text and locations)

Execution order: Anchor first → Satellites (share modifications, independent framing) → Consistency check

### Step 2: Modify manuscript.tex

Apply Part 3 suggestions. Rules:
- Only modify `manuscript.tex`, **never** `manuscript-original.tex`
- Complete entire cluster before compiling
- Record each modification: line number, type, content summary, related comment

### Step 3: Fill response-letter.tex

Paste Part 2 into `\response{[TO BE FILLED]}` locations. Verify:
- No macro nesting
- No blank lines in `\response{}`
- `\manuscriptquote{}` properly separated

**Key macro definitions** (see `templates/response-letter.tex` for the authoritative versions):

```latex
\newcommand{\response}[1]{\textcolor{responseblue}{#1}}

\newcommand{\manuscriptquote}[1]{%
  \par
  {\leftskip=2em\noindent\textcolor{quotegreen}{\textit{#1}}\par}%
}

\newcommand{\lineref}[1]{%
  \textup{\textcolor{linerefred}{ (Please see #1 in the revised manuscript)}}%
}

\newcommand{\reviewercomment}[1]{\noindent\textbf{#1}\par\medskip}
```

**Formatting rules:**
1. Macros cannot nest (except `\lineref{}` at end of `\manuscriptquote{}`)
2. `\response{}` cannot contain blank lines (causes `\par` error)
3. Use plain text citations: `(Author et al., Year)` not `\citep{}`

### Step 4: Update Progress Tracking

Update all three files:
- `cluster-progress.md`: Check completed items, update status
- `response-progress.md`: Mark Completed
- `manuscript-changelog.md`: Record modifications

### Step 5: Compile & Verify

```bash
latexmk manuscript.tex
latexmk -pvc- -pv- response-letter.tex
```

Verify: no compilation errors, no `\par` errors (check blank lines), PDF renders correctly.

### Step 6: Git Commit

```bash
git add manuscript.tex response-letter.tex supplemental-materials.tex revision/
git commit -m "Complete cluster C[N]: [theme]"
```

Format: `Complete cluster C2: notation consistency audit`. Don't commit build artifacts or `manuscript-original.tex`.

---

## Feedback Loop

AI-generated drafts are starting points. Each response needs human review.

### Feedback Mechanism

**Option A**: Direct conversation — Tell Claude what to change.

**Option B**: Structured feedback via `revision/issue.md`:

```markdown
# Review Feedback — C[N]: [Cluster Theme]

## R1-1 (anchor)
- Para 2: Too verbose — condense
- The justification for X is weak — add Y argument

## R2-1
- Framing doesn't match R2's concern
- Add connection to Section 5
```

### Cross-Consistency Fix After Anchor Modification

When anchor response is modified, satellite responses in same cluster must be reviewed for consistency.

In `response-progress.md`, mark satellites needing fix:
```markdown
| R2-1 | [brief] | C1 | Cross-fix needed | L[TBD] | drafts/Comment_R2-1.md |
```

After fix applied:
```markdown
| R2-1 | [brief] | C1 | Completed (cross-fix applied) | L156 | drafts/Comment_R2-1.md |
```

### Termination Conditions

**Per response**: Maximum 2 revision rounds unless logical error or factual inaccuracy found.

**Per cluster**: All responses finalized, cross-consistency verified, modifications applied and logged, compiled and verified.

**Complete revision**: All clusters marked Completed, all responses marked Completed, end-to-end response letter read, final compilation successful, tracked-changes PDF generated and reviewed.

### Common Feedback Patterns

- **"Too verbose"** → Identify core argument (1-2 sentences), rebuild around it. Cut restatement, filler phrases, unnecessary transitions.

- **"Wrong focus"** → Re-read original comment. What is reviewer really worried about? Lead with that concern.

- **"Over-promising"** → Use qualified language: "provides a foundation for" not "solves"; "results suggest" not "results prove".

- **"Inconsistent with other response"** → Cross-consistency fix. Identify correct version (usually anchor), update others to align.

- **"Add specific citation"** → Find in `.bib`, add `\cite{}` in manuscript, add plain text citation in response, verify author names match.

- **"Doesn't match what was actually done"** → Factual error, fix immediately. Either update response to match actual work, or do the work the response describes.

---

## Pre-Submission Checklist

Before uploading, verify every item. A missed placeholder or mismatched line number can cause confusion or rejection.

### Compilation Verification

- [ ] `latexmk manuscript.tex` no errors
- [ ] `latexmk -pvc- -pv- response-letter.tex` no errors
- [ ] `latexmk -pvc- -pv- supplemental-materials.tex` no errors (if applicable)
- [ ] PDFs open correctly, figures render, tables don't overflow

### Tracked Changes Generation

- [ ] `bash tools/make-diff.sh` succeeds
- [ ] Additions blue, deletions red strikethrough
- [ ] No `\noalign` errors from booktabs
- [ ] Diff coverage complete (check against changelog)
- [ ] Spot-check custom environments and multi-line math for spurious markup (see `make-diff.sh` header for known limitations)

### manuscriptquote Consistency

- [ ] Every `\manuscriptquote{}` matches current `manuscript.tex` exactly
- [ ] Quotes complete (not truncated), special characters escaped
- [ ] Spot-check 5: search first 10 words, confirm match

### Response Completeness

- [ ] `grep -n "TO BE FILLED" response-letter.tex` returns zero
- [ ] `response-progress.md` all ✅
- [ ] `cluster-progress.md` all ✅
- [ ] Cross-consistency fixes applied
- [ ] Comment-response counts match original for each reviewer

### Journal-Specific Compliance

- [ ] Title case convention correct
- [ ] Author info matches submission system
- [ ] Abstract, keywords within limits
- [ ] Reference format correct
- [ ] Figures ≥300 dpi, correct format
- [ ] Tables consistent formatting
- [ ] Data Availability Statement present and matches system
- [ ] Page/word count, spacing requirements met

### Reference Completeness

- [ ] No `?` unresolved citations
- [ ] All `\citep{}`/`\citet{}` valid
- [ ] New publications have complete metadata (volume, issue, page, DOI)
- [ ] `grep -n "(ref)" manuscript.tex` returns zero
- [ ] No duplicate entries (check `.blg` warnings)
- [ ] DOIs clickable (spot-check 3-5)

---

## Submission Package Assembly

### Deliverables

Standard revision submission contains four documents:

| # | Document | Filename | Description |
|---|----------|----------|-------------|
| 1 | Response Letter | `response-letter.pdf` | Point-by-point responses |
| 2 | Revised Manuscript | `manuscript.pdf` | Clean version |
| 3 | Tracked Changes | `manuscript-track-changes.pdf` | Diff showing all changes |
| 4 | Supplemental | `supplemental-materials.pdf` | Extra tables, proofs, data |

Some journals also require:
- Source files (`.tex` + `.bib` + figures)
- Separate high-resolution figures
- Cover letter (distinct from response letter)

### Final Compilation Order

```bash
# 1. Clean previous artifacts
latexmk -c

# 2. Compile manuscript
latexmk manuscript.tex

# 3. Generate tracked changes
bash tools/make-diff.sh

# 4. Compile response letter
latexmk -pvc- -pv- response-letter.tex

# 5. Compile supplemental (if applicable)
latexmk -pvc- -pv- supplemental-materials.tex

# 6. Verify all PDFs
ls -la manuscript.pdf response-letter.pdf manuscript-track-changes.pdf
```

### Final Git Operations

```bash
git add manuscript.tex response-letter.tex supplemental-materials.tex revision/
git commit -m "Final submission package: [MANUSCRIPT_ID] R1"
git tag -a v1.1-revision -m "Major revision submission [DATE]"
git push origin main --tags
```

### Version Naming Convention

| Tag | Meaning |
|-----|---------|
| `v1.0` | Original submission |
| `v1.1-revision` | First revision (R1) |
| `v1.2-revision` | Second revision (R2) if needed |
| `v2.0` | Resubmission to different journal |

### Post-Submission Protocol

**Preserve project state:**
- Keep entire project directory including `revision/`, `revision/drafts/`, and all progress files
- Do **not** modify `manuscript-original.tex` — may need it for R2
- Do **not** delete `build/` until certain no more revisions needed

**If R2 required:**

```bash
# Save R1 version
cp manuscript.tex manuscript-original-r1.tex

# Option A: R1 as new diff baseline (most common)
cp manuscript.tex manuscript-original.tex

# Option B: Keep R0 as baseline for cumulative changes
# - The diff will show ALL changes since original submission
# - Old \lineref{} numbers in R1 response letter become invalid
# - Useful when editor/journal wants to see complete revision history
# - Note: latexdiff may struggle with very large cumulative changes

# Archive R1 reviewer comments
mkdir -p revision/r1-archive
cp revision/comment-letter.md revision/r1-archive/
cp revision/comment-letter-clean.md revision/r1-archive/  # if exists
cp revision/revision-guide.md revision/r1-archive/
cp revision/response-progress.md revision/r1-archive/
cp revision/cluster-progress.md revision/r1-archive/
cp revision/manuscript-changelog.md revision/r1-archive/
cp -r revision/drafts/ revision/r1-archive/drafts/

# Reset progress files for R2
```

---

## Appendix A: Recovery Procedures

### Accidental Modification of manuscript-original.tex

If modified but not committed:
```bash
git checkout -- manuscript-original.tex
```

If committed:
```bash
git log --oneline manuscript-original.tex
git checkout <commit-hash> -- manuscript-original.tex
```

**Prevention:**
1. CLAUDE.md explicit rule
2. File permissions: `chmod 444 manuscript-original.tex`
3. Git pre-commit hook:
   ```bash
   if git diff --cached --name-only | grep -q "manuscript-original.tex"; then
       echo "ERROR: manuscript-original.tex is frozen."
       exit 1
   fi
   ```
4. Naming convention ("original" signals "don't touch")

---

## Appendix B: Skill Definitions

Skills are provided as separate files in `.claude/commands/`:

### detailed-response.md
- **Invoke**: `/detailed-response R1-1`
- **Output**: Three parts (Analysis + LaTeX Response + Manuscript Modifications)
- **Behavior**: Cross-reviewer self-contained, plain text citations, `\lineref{Lines [TBD]}` placeholders

### general-response.md
- **Invoke**: `/general-response Editor, AE, R1`
- **Output**: Brief summary response (thanks + scope overview), no `\manuscriptquote{}`

### Installation

```bash
mkdir -p .claude/commands/
cp detailed-response.md .claude/commands/
cp general-response.md .claude/commands/
```
