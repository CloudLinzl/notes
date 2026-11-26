---
name: read-summary
description: >-
  Consolidate paper reading results and interactive discussion conclusions into
  the user's existing PDF/Zotero-linked Markdown note file using the workspace
  template at `.agents/templates/notes.md`. Use after reading and discussing a
  paper when the user asks to summarize, refine, or complete their existing
  linked note file.
---

# Read Summary & Linked Note Refinement

This skill is completely decoupled from `paper-reading` and handles **only the
post-reading, post-discussion note consolidation stage**: refining the user's
existing PDF/Zotero-linked `.md` note file in-place according to
[.agents/templates/notes.md](../../templates/notes.md).

## Workflow

### 1. Load the Canonical Note Template
1. Read [.agents/templates/notes.md](../../templates/notes.md) in the workspace
   to obtain the exact, up-to-date section headings and sub-bullets.
2. Always use the current structure of `.agents/templates/notes.md` as the
   authoritative schema.

### 2. Locate the Target Linked Note & Gather Context
1. Search the relevant topic's `notes/` directory (e.g., `<topic>/notes/`) for
   the user's **existing PDF/Zotero-linked Markdown note file**:
   - Identify the user's linked note by its PDF/paper short title or Better
     Notes naming pattern (e.g., `<ShortTitle>-<NoteItemKey>.md` or
     `<PDFName>.md`) and its YAML frontmatter (`parent`, `$itemKey`,
     `$libraryID`, `$version`).
   - **Distinguish from `Antigra_*.md`**: Files prefixed with `Antigra_` (e.g.,
     `Antigra_<AuthorYear_ShortTitle>.md`) are initial reading outputs from
     `paper-reading`. Read `Antigra_*.md` as reference material if it exists,
     but **always target the user's linked note file** (not `Antigra_*.md`) for
     refinement.
2. Read the entire target linked note file before making any edits.
3. If no existing linked note file is found, or if multiple files match
   ambiguously, list the candidates and ask the user to confirm the target path
   before writing.

### 3. Strictly Preserve Frontmatter, Title & User's Handwritten Notes
1. **YAML Frontmatter Protection**:
   - If the target file starts with a YAML frontmatter block (`--- ... ---`,
     containing fields such as `tags`, `parent`, `collections`, `$version`,
     `$libraryID`, `$itemKey` used by Zotero / Better Notes sync), **preserve
     the entire frontmatter block verbatim at the very top of the file**.
2. **H1 Title Protection**:
   - Keep the user's existing `# Title` line intact; place the metadata summary
     bullets (`Zotero Key / DOI`, `年份/发表`, `代码/数据`, `一句话概括 (TL;DR)`)
     immediately below the H1 title.
3. **Handwritten Content & Doubts Protection**:
   - **Never delete, overwrite, or dilute** any text, thoughts, or questions the
     user has already written in the file (especially under `## 疑点`).
   - Preserve the user's original points verbatim, and integrate the verified
     answers, deep explanations, and discussion conclusions alongside or right
     beneath them.

### 4. Populate and Refine Sections Using `.agents/templates/notes.md`
Synthesize the paper's full-text findings, any existing `Antigra_*.md` analysis,
and all insights/Q&A from the interactive discussion with the user into the
template sections:

1. **顶部元信息 & 一句话概括 (TL;DR)**:
   - Fill in `Zotero Key / DOI`, `年份/发表`, `代码/数据`, and a concise one-sentence
     summary of the paper's core contribution.
2. **希望解决的问题与问题背景**:
   - Define the exact research problem, domain background, and real-world/academic
     value.
3. **目前方案的局限**:
   - Summarize prior mainstream approaches and their fundamental bottlenecks or
     failure modes.
4. **作者思考路径与解决的方法**:
   - **思考路径重建**: Reconstruct how the authors arrived at the idea strictly
     from prior limitations, empirical observations, and existing knowledge—**never
     use the paper's own contribution as a premise**.
   - **核心直觉 (Intuition)**: State the essence of the core idea clearly and
     concisely.
   - **方法概述**: High-level summary of the proposed solution.
5. **算法架构与单样本数据流**:
   - **整体架构**: Inputs, module functions, data flow, and outputs.
   - **真实样本全流程走通**: Walk through **one concrete sample** end-to-end
     (including explicit coordinate systems, tensor shapes/dimensions, physical
     units, intermediate transformations, and final output extraction),
     explicitly incorporating all algorithmic details clarified during the
     discussion with the user.
6. **其中的主要数学原理**:
   - **理论基础与物理/几何直觉 (零基础视角)**: Build up foundational intuition
     in plain language before showing equations.
   - **核心公式、符号定义与推导**: Define every symbol and explain derivation steps.
   - *Dynamic adaptation*: If the paper contains no formal math derivations,
     explicitly state why and skip rather than fabricating equations.
7. **达成了怎样的效果**:
   - **核心验证逻辑**: Summarize each key experiment strictly as
     `提出了什么问题 -> 设计了什么实验 -> 答案是什么 [图表/页码定位]`.
   - **消融实验与关键 Take-aways**: Key ablation findings and 3–5 core takeaways.
8. **与现有方案的对比**:
   - **横向对比核心优势与劣势**: Structured comparison against prior baselines/datasets.
   - **实验公平性与实际部署成本**: Hidden preprocessing tricks, hardware/compute
     overhead, and real-world deployment constraints.
9. **疑点、脆弱假设与 Follow-up 思考**:
   - **底层实现疑点与追问**: Keep the user's original written doubts verbatim AND
     consolidate all technical Q&A from the discussion session.
   - **最脆弱的隐含假设**: Pinpoint the most fragile explicit or implicit
     assumptions where the method breaks down in reality.
   - **反例构造**: Concrete adversarial scenarios or counterexamples that
     invalidate the paper's method or claims.
   - **非增量 Follow-up Idea / 对我课题的启发**: A novel, non-incremental
     research direction born from the paper's limitations and real-world needs.

### 5. Epistemic Hygiene & Verifiable Grounding
- **Verifiable citations**: Attach source locations (`[Sec. X, p. Y]`,
  `[Table/Fig. Z, p. Y]`, `[Eq. (N), p. Y]`) to every key claim, metric, and
  experimental result. State explicitly if a location cannot be verified.
- **Strict attribution**: Clearly distinguish between **原文结论**, **作者观点**,
  **Agent 推断**, and **研究假设**.
- **In-place edit & Zotero sync**: Update the user's local linked `.md` note
  file in-place using `edit_file` / `write_file`. Do **not** directly modify the
  Zotero database via `zotero-cli notes update` unless the user explicitly asks
  and confirms.
