---
name: paper-reading
description: >-
  Conduct deep, verifiable academic paper reading and structured analysis using
  the local Zotero library via the `zotero-cli` skill. Use when the user
  provides a paper title, DOI, Zotero Item Key, or keywords and asks to read,
  analyze, summarize, or take reading notes on a paper.
---

# Paper Reading & Deep Analysis

This skill orchestrates end-to-end academic paper reading on top of the
`zotero-cli` skill. **Do not re-implement `zotero-cli` commands**; load and
follow the `zotero-cli` skill for all Zotero search, metadata, PDF reading,
image rendering, and note-writing operations.

## Workflow

### 1. Locate the Target Paper in Zotero
1. Activate/follow the `zotero-cli` skill to search the local Zotero library
   by the user's input (8-character Item Key, DOI, exact/partial title, or
   keywords).
2. Always use `--json` when parsing `zotero-cli` output.
3. **Disambiguation & Missing Items**:
   - If multiple items match, list the candidates (key, title, authors, year) and
     ask the user to confirm before deep reading.
   - If no local match is found, report that the paper is not in the local
     Zotero library. Only search externally if the user asks, and never import
     into Zotero without user confirmation.

### 2. Acquire Metadata, Existing Notes, Annotations & Full Text
1. Retrieve item metadata (`get metadata`), child attachments (`get children`),
   existing Zotero notes (`notes list`), and PDF annotations
   (`annotations list --item-key`).
2. **Check PDF availability**:
   - If no PDF attachment exists, clearly inform the user that only metadata/abstract
     is available. **Never** present an abstract-only summary as a full-text
     analysis.
3. **Read efficiently**:
   - Start with `outline` and page-range `read` (`--start-page`, `--end-page`)
     or `--find` for targeted sections rather than dumping huge documents blindly.
4. **Multimodal Fallback for Formulas, Figures & Tables**:
   - PDF text extraction often drops math symbols or scrambles tables (watch for
     `read` warnings like `"Garbled in this text"` or broken equations/tables).
   - When equations, figures, or tables are garbled or incomplete:
     1. Render the affected pages as PNGs via `zotero-cli read ... --format image`
        (and `--rect` to zoom in if needed), then inspect the images using
        `read_file`.
     2. If image rendering is unavailable (e.g., missing `PyMuPDF`), locate the
        local PDF via `zotero-cli path ATTACHMENT_KEY` and read the PDF directly
        with `read_file` (which provides page screenshots and OCR) or available
        local PDF tools.
   - If a formula, figure, or table **still** cannot be reliably parsed,
     explicitly state which element could not be parsed and why. **Never guess
     or fabricate symbols, numbers, or experimental results.**

### 3. Deep Analysis Structure (12 Core Sections)
Analyze the paper in **Chinese** (retaining necessary English technical terms),
using **LaTeX** (`$...$` / `$$...$$`) for all mathematics and structured
Markdown. Follow this 12-section problem-and-thinking-driven framework (adapt
naturally by paper type; see [analysis-templates.md](./references/analysis-templates.md)):

1. **研究问题、背景补充与核心价值**
   - 论文提出并解决的研究问题是什么？（调研并补充必要的领域背景）
   - 为什么这个问题是重要的？解决这个问题能带来哪些学术或实际应用价值？
2. **既有研究进展与不足（Before This Paper）**
   - 这个问题之前被解决了吗？如果被部分解决，现有主流方案是什么？
   - 之前的研究为什么存在不足？（深入剖析已有方法的底层瓶颈与失效原因）
3. **【重要】作者思考路径重建（Thought Trajectory Reconstruction）**
   - **严禁使用论文自己的贡献作为前提**（不要用“因为作者提出了 X 所以…”的后见之明视角）。
   - **仅使用该论文发表前已有的背景知识、前人失败模式、经验观察和相关工作**，模拟并还原作者本人的思考推演过程：作者看到了什么异常/痛点 $\rightarrow$ 联想到了什么既有线索或跨领域灵感（inspiration） $\rightarrow$ 形成了什么直觉（intuition） $\rightarrow$ 如何一步步顺理成章地推导出这篇论文的 idea。
4. **核心方法的 Intuition（Core Essence）**
   - 这篇论文提出方法的 Intuition 是什么？用通俗易懂、清晰精炼（concise）的语言一针见血地讲透核心 idea 的本质。
5. **具体方法与真实案例全流程详解（Concrete Method & End-to-End Example）**
   - 这篇论文的具体方法/系统架构是什么？（明确输入、输出、各模块功能与数据流）
   - **必须结合一个具体的真实/具象化例子**（例如给定一段具体的输入张量/样本数据），完整走通“输入 $\rightarrow$ 各阶段中间处理与变换 $\rightarrow$ 最终输出”的完整 pipeline。
6. **零基础视角的数学原理与核心推导（Zero-Background Math & Theory）**
   - **前置理论铺垫**：面向数学基础薄弱的读者，先补充理解公式所需的理论背景、理论基础与物理/几何/概率直觉。
   - **核心公式与推导**：给出符号定义、一步步推导过程和直观解释。
   - **动态跳过**：如果论文不包含核心数学建模或公式推导（如纯数据集/系统描述部分），明确说明原因并跳过本节，切勿生造公式。
7. **问题驱动的实验设计与验证逻辑（Question-Driven Experimental Design）**
   - 不堆砌琐碎的数据细节，聚焦实验验证的核心逻辑，严格按照以下三段式总结每一组关键实验（仍需附上对应的图表/章节定位）：
     - **提出了什么问题（Question / Claim）** $\rightarrow$ **设计了什么实验验证这个问题（Experimental Setup）** $\rightarrow$ **问题的答案是什么（Answer / Conclusion）**
8. **核心收获总结（Key Take-aways）**
   - 提炼这篇论文最值得记住的 3–5 条核心 take-aways（方法论启示、实验发现或工程经验）。
9. **【重要】最脆弱的假设（Most Fragile Assumptions）**
   - 深入剖析这篇论文成立所依赖的最脆弱、最容易在真实场景中崩塌的显式或隐含假设（包括实验公平性盲区、实际部署成本及方法最薄弱的环节）。
10. **一周最小复现与验证实验（1-Week Minimal Verification Experiment）**
    - 如果只有 1 周时间和有限算力/数据，应该做一个怎样的**最小复现实验（MVP）**来验证论文最核心的 claim 或最可疑的瓶颈？（给出具体的数据构造/子集、对比设计、评价指标与预期观察）。
11. **【重要】反例构造与反驳设计（Adversarial Counterexample Design）**
    - 如果站在审稿人或反对者的立场反驳这篇论文，会如何设计一个**具体反例（Counterexample）或极端压力测试场景**，使得该论文的方法失效或核心结论不再成立？
12. **非增量式的新颖后续研究构想（Novel Non-Incremental Follow-up Idea）**
    - 基于上述方法缺陷（Limitations）、脆弱假设与真实应用场景需求，经过调研与深度思考，提出一个**真正具有新颖性（novel）、非增量式（拒绝简单的 A+B 换模块或调参）**的后续研究 idea（明确新问题定义、核心突破思路与预期价值）。

#### Evidence Grounding & Epistemic Hygiene
- **Verifiable citations**: Attach 原文定位 (source location) to key facts,
  claims, formulas, and experimental setups, e.g., `[Section 3.2, p. 5, Eq. (4)]`
  or `[Table 2, p. 7]`. If a location cannot be verified, state so explicitly.
- **Strict attribution**: Clearly distinguish between:
  - **原文结论** (findings/results stated in the paper)
  - **作者观点** (author claims/interpretations)
  - **Agent 推断** (your own reconstruction, critique, counterexample, or inference)
  - **研究假设** (unverified hypotheses, 1-week test plans, and follow-up ideas)
- **Preserve existing annotations**: If the paper already has user notes or
  PDF annotations in Zotero, incorporate or reference them in an appendix/subsection
  without altering them.

### 4. Save Markdown & Confirm Before Writing to Zotero
1. Save the complete structured analysis as a `.md` file in the current
   workspace (e.g., `notes/<Antigra_AuthorYear_ShortTitle>.md` or the relevant topic
   subfolder's `notes/` directory).
2. Present a concise summary and the file link to the user, and **ask for
   explicit confirmation** before writing anything to Zotero.
3. Only after the user confirms:
   - Create a new Zotero note (`zotero-cli notes create`) or append to an
     existing note (`zotero-cli notes update --append`) via the `zotero-cli`
     skill.
   - **Never** overwrite or delete existing user notes or annotations.
