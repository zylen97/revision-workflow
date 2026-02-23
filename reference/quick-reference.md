# Quick Reference

Cheat sheets for cluster analysis, closed-loop execution, and submission verification.

---

## Response Letter LaTeX Macro Cheat Sheet

```
──────────────────────────────────────────────────────────────
  RESPONSE LETTER — LaTeX MACRO QUICK REFERENCE
──────────────────────────────────────────────────────────────

MACROS:
  \response{text}              Blue author response text
  \manuscriptquote{text}       Green italic indented manuscript quote
  \lineref{Lines X--Y}         Red line reference (PDF line numbers!)
  \smref{Section SN}           Red supplemental material reference
  \reviewercomment{text}       Bold reviewer comment heading
  \responseheader              Blue "- Response:" heading

NESTING RULES:
  OK   \response{} → close → \manuscriptquote{} → close → \response{}
  OK   \manuscriptquote{...text. \lineref{Lines X--Y}}
  BAD  \response{\manuscriptquote{...}}           (no nesting!)
  BAD  \response{paragraph1\n\nparagraph2}        (no blank lines!)

CORRECT PATTERN:
  \response{Our revision addresses this concern.}

  \manuscriptquote{Revised text here... \lineref{Lines 100--105}}

  \response{This modification clarifies the methodology.}

CITATIONS (plain text only — no natbib):
  Parenthetical:  (Author et al., Year)
  Textual:        Author et al. (Year)
  Two authors:    (Smith and Jones, 2023)
  3+ authors:     (Smith et al., 2023)
  Multiple:       (Smith et al., 2023; Wang and Lee, 2024)

──────────────────────────────────────────────────────────────
```

---

## Scientific Writing Style Quick Reference

```
──────────────────────────────────────────────────────────────
  SCIENTIFIC WRITING RULES — ALL GENERATED TEXT
──────────────────────────────────────────────────────────────

  SIX RULES:

  1. SIMPLE ACTIVE STYLE    One idea per sentence. Active voice.
                            "We revised X" not "X was revised"
  2. LOGICAL FLOW           "First,...Second,..." not
                            "Moreover,...Furthermore,..."
                            Every sentence connects by logic,
                            not by filler.
  3. SHORT SENTENCES        Target 15-20 words. Hard cap: 25.
  4. MINIMAL MODIFIERS      Only adjectives/adverbs that carry
                            new information. No stacking.

  CHINGLISH PREVENTION (Rule 5):
  5. NO LITERAL TRANSLATION Check every verb-noun & adj-noun pair:
     "improve the level"     → enhance
     "provide reference for" → inform, offer insights for
     "play an important role"→ is critical to, contributes to
     "promote the development"→ foster, advance, drive
     "make a discussion"     → discuss (use verb directly)
     "big influence"         → significant impact
     "deep research"         → in-depth investigation
     Chinese sentence patterns → rewrite entirely:
       "With the development of X..." → "X has transformed Y"
       "More and more..."            → "An increasing number of"

  COMPRESS REDUNDANCY (Rule 6):
  6. NOMINALIZATION → VERB   "carry out an investigation" → investigate
                             "due to the fact that" → because
                             "it is worth noting that" → (delete)
     EMPTY MODIFIERS → DELETE very/extremely/basically/novel/important
     SYNONYM STACKS → PICK ONE "critical and essential" → essential

  THANKS EXCEPTION:
    ONE descriptive adjective allowed in opening thanks:
      OK   "constructive feedback"  "careful examination"
      BAD  "highly insightful and extremely valuable comment"

  SELF-CHECK PER SENTENCE:
    [ ] Can I split this?              → Split it.
    [ ] Is the subject doing the verb? → Rewrite if not.
    [ ] Over 25 words?                 → Shorten.
    [ ] Any adverb I can delete?       → Delete it.
    [ ] Does it connect logically?     → Add transition if not.
    [ ] Verb-noun literally translated? → Use idiomatic English.
    [ ] Any nominalization to compress? → Use direct verb.
    [ ] Chinese sentence pattern?      → Rewrite to SVO.

  ❌  "We have comprehensively addressed the reviewer's
      insightful concern regarding the significant lack of
      conceptual clarity in the definition section."

  ✅  "We revised the definition section. We now define
      three concepts: X, Y, and Z (Lines 101--115)."

──────────────────────────────────────────────────────────────
```

---

## Cluster Analysis Checklist

```
──────────────────────────────────────────────────────────────
  CLUSTER ANALYSIS CHECKLIST
──────────────────────────────────────────────────────────────

  [ ] Original decision letter pasted to comment-letter.md
  [ ] /parse-decision-letter executed → comment-letter-clean.md generated
  [ ] Each comment assigned an ID in comment-letter-clean.md:
        Major: R1-1, R1-2, ...  Minor: R1-m1, R1-m2, ...
  [ ] Comments grouped into thematic clusters (C1, C2, ...)
  [ ] Each cluster has a designated anchor response
  [ ] Cross-reviewer situations identified and marked
      "self-contained"
  [ ] Priority levels assigned:
        Highest / High / Medium / Low
  [ ] Dependencies between clusters mapped
  [ ] revision-guide.md created with full cluster analysis
  [ ] Each comment classified by type (Modify/Explain/Supplement)
      and priority
  [ ] cluster-progress.md initialized (all clusters → ⬜)
  [ ] response-progress.md initialized (all comments → ⬜)
  [ ] manuscript-changelog.md initialized (empty log)
  [ ] response-letter.tex skeleton built
      (grep -c "TO BE FILLED" = expected count)
  [ ] revision-guide.md Sections 6-9 completed
      (no [placeholder] remaining)

──────────────────────────────────────────────────────────────
```

---

## Six-Step Closed Loop — Per Cluster

```
──────────────────────────────────────────────────────────────
  SIX-STEP CLOSED LOOP — PER CLUSTER
──────────────────────────────────────────────────────────────

  Cluster: C___    Priority: ___    Anchor: ___

  [ ] Step 1  DRAFT
      Invoke /detailed-response for each comment in cluster
      Output → revision/drafts/Comment_X-Y.md

  [ ] Step 2  MODIFY MANUSCRIPT
      Apply Part 3 suggestions to manuscript.tex
      (if changes affect supplemental material,
       also modify supplemental-materials.tex)
      Record changes in manuscript-changelog.md

  [ ] Step 3  FILL RESPONSE LETTER
      Paste Part 2 output into response-letter.tex
      Replace \response{[TO BE FILLED]} placeholders

  [ ] Step 4  UPDATE PROGRESS
      cluster-progress.md:  C__ → ✅
      response-progress.md: each comment → ✅
      manuscript-changelog.md: log changes

  [ ] Step 5  COMPILE & VERIFY
      latexmk manuscript.tex              (no errors)
      latexmk -pvc- -pv- response-letter.tex  (no errors)
      Check auto-diff output              (if configured)

  [ ] Step 6  GIT COMMIT
      git add manuscript.tex response-letter.tex \
               supplemental-materials.tex revision/
      git commit -m "Complete cluster C__: [description]"

──────────────────────────────────────────────────────────────
```

---

## Pre-Submission Final Checklist

```
──────────────────────────────────────────────────────────────
  PRE-SUBMISSION FINAL CHECKLIST
──────────────────────────────────────────────────────────────

  COMPLETENESS
  [ ] All clusters completed
      (cluster-progress.md: every item is ✅)
  [ ] All responses filled
      (response-progress.md: every item is ✅)
  [ ] No [TO BE FILLED] or [TBD] placeholders remain
      grep -n "TO BE FILLED\|TBD" response-letter.tex
  [ ] No (ref) placeholders remain in manuscript
      grep -n "(ref)" manuscript.tex

  COMPILATION
  [ ] manuscript.tex compiles without errors
  [ ] response-letter.tex compiles without errors
  [ ] supplemental-materials.tex compiles without errors
  [ ] Tracked-changes PDF generated and reviewed

  ACCURACY
  [ ] Every \manuscriptquote{} matches current manuscript
  [ ] All \lineref{} line numbers verified against current manuscript
  [ ] Figure/table numbers correct in response letter
  [ ] Bibliography complete (no "?" in compiled PDF)

  WRITING STYLE
  [ ] Spot-check 5 response paragraphs: no sentence > 25 words
  [ ] Spot-check for passive voice — rewrite to active
  [ ] No stacked modifiers in response text
      (grep for: significantly, comprehensively, thoroughly,
       extremely, highly, greatly)
  [ ] Spot-check for Chinglish collocations
      (grep for: improve the level, play.*role, provide reference,
       with the development, more and more, carry out)

  FORMATTING
  [ ] Journal formatting requirements met
      (spacing, title case, abstract length, etc.)
  [ ] All figures meet resolution requirements (≥ 300 dpi)
  [ ] Data Availability Statement present

  ARCHIVE
  [ ] Git committed with descriptive message
  [ ] Git tagged (e.g., v1.1-revision)
  [ ] Git pushed (including tags)

  DELIVERABLES
  [ ] response-letter.pdf
  [ ] manuscript.pdf
  [ ] manuscript-track-changes.pdf
  [ ] supplemental-materials.pdf (if applicable)

──────────────────────────────────────────────────────────────
```

---

## Comment Type Classification

| Type | Definition | Example |
|------|------------|---------|
| **Modify Manuscript** | Requires changes to `manuscript.tex` | "The definition in Section 2 is unclear and should be rewritten." |
| **Explain/Clarify** | Only needs explanation in response; no manuscript changes | "Why did you choose method X over method Y?" |
| **Supplement** | Add new content: paragraphs, tables, figures, references, appendix | "Please add a sensitivity analysis for parameter α." |

---

## Priority Levels

| Priority | Criteria |
|----------|----------|
| **Highest** | Global impact — changes propagate through entire manuscript (e.g., notation overhaul, key definition changes) |
| **High** | Significant manuscript modifications; core technical concerns |
| **Medium** | Presentation improvements, additional explanations, figure/table enhancements |
| **Low** | Typo fixes, phrasing suggestions, formatting issues |

---

## Thematic Categories for Clustering

| Category | Covers | Example Comments |
|----------|--------|------------------|
| Conceptual Definitions | Key terms, constructs, variables | "Define X more clearly" / "What exactly is Y?" |
| Notation Consistency | Symbols, subscripts, equation formatting | "Notation in Eq. 3 vs Eq. 7 conflict" |
| Model Assumptions | Simplifications, boundary conditions, limitations | "Why assume linear costs?" / "Is this realistic?" |
| Method Transparency | Derivation steps, parameter choices, reproducibility | "How was α calibrated?" / "Show derivation" |
| Robustness/Sensitivity | Alternative parameters, edge cases, validation | "What if β changes?" / "Sensitivity analysis needed" |
| Literature Coverage | Missing citations, positioning, comparisons | "Compare with Smith (2020)" / "Cite Jones (2019)" |
| Structural Improvements | Section order, flow, length | "Move Section 4 before Section 3" |
| Figures/Tables | Quality, labeling, readability | "Figure 2 is hard to read" / "Add units to Table 3" |
| Practical Implications | Real-world applicability, managerial insights | "How would a practitioner use this?" |
| Generalizability | Scope limitations, external validity | "Does this apply to other contexts?" |

---

## Non-Standard Review Format — Quick Reference

```
──────────────────────────────────────────────────────────────
  NON-STANDARD REVIEW FORMAT — QUICK REFERENCE
──────────────────────────────────────────────────────────────

  NO ASSOCIATE EDITOR?
  → Delete AE section from response-letter.tex + ToC

  REVIEWER NUMBERS NOT SEQUENTIAL? (e.g., #2, #3, #4)
  → Keep original numbering in response letter
  → Use R2-1, R3-1, R4-1 (not R1-1, R2-1, R3-1)

  REVIEWER DIDN'T SPLIT MAJOR/MINOR?
  → Use continuous numbering: RN-1, RN-2, ...
  → Classify type (Modify/Explain/Supplement) and priority
    in the analysis workspace instead

  Q&A SECTION HAS SUBSTANTIVE FEEDBACK?
  → Extract as separate Comment (note source: "from Q&A")
  → Or merge into most relevant existing Comment

  REVIEWER SUSPECTS AI USE?
  → Highest priority — compliance issue
  → User must confirm response strategy
  → Check journal's AI policy URL
  → Never draft AI disclosure response without user approval

  ONE COMMENT BLOCK CONTAINS MULTIPLE ISSUES?
  → If truly independent topics → split into separate Comments
  → If sub-aspects of one concern → keep as one, note sub-issues

──────────────────────────────────────────────────────────────
```

---

## Execution Order Strategy

1. **Global impact first**: Notation audit, key definition changes go first
2. **Respect dependency chains**: C5 depends on C2+C4 → complete C2, C4 first
3. **Batch related clusters**: Adjacent clusters without dependencies can be combined
4. **Anchor-first within cluster**: Draft anchor response before satellites
5. **Interleave simple tasks**: Schedule easy tasks between heavy clusters to maintain momentum

Typical order: C2(notation) → C1(definitions) → C3-C4-C5 → C6+C7 → C8 → C9+C10 → final audit

---

## Status Symbol Legend

| Symbol | Meaning | Used in |
|--------|---------|---------|
| ⬜ | Not started | cluster-progress, response-progress |
| 🔶 | In progress / Waiting for user action | cluster-progress |
| ✅ | Completed | cluster-progress, response-progress |
| ✅📝 | Completed, revised after user review | response-progress |
| 🔄 | Cross-consistency fix applied | response-progress |
