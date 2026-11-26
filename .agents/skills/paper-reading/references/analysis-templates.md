# Dynamic Adaptation Guide for the 12-Section Framework

Always preserve the 12-question backbone from `SKILL.md`, while tailoring
Sections 5, 6, and 7 to the paper's genre so no section is mechanically padded.

## 1. Method / Model Paper (Default)
- **Section 5 (具体方法与真实案例 pipeline)**: Walk through a concrete sample
  (e.g., a single image, sequence, or graph input with explicit dimensions)
  across every neural/algorithmic module from raw input to final prediction and
  loss computation.
- **Section 6 (零基础数学推导)**: Explain the intuitive math background first
  (e.g., why KL divergence, why graph Laplacian, why attention scaling), then
  derive the core objective/theorem step by step with full symbol definitions.

## 2. Dataset / Benchmark Paper
- **Section 3 & 4 (思考路径与 Intuition)**: Focus on why existing benchmarks
  failed to test real-world generalization and the core design philosophy of the
  capture/alignment/split protocol.
- **Section 5 (具体方法与真实案例 pipeline)**: Trace **one concrete physical
  recording event** (e.g., an athlete's single landing or video clip) through
  multi-sensor capture $\rightarrow$ clock synchronization $\rightarrow$ quality
  gating $\rightarrow$ segmented tensor input $\rightarrow$ baseline model output.
- **Section 6 (零基础数学推导)**: If the paper introduces calibration, alignment,
  or metric equations, explain their intuition and formulas; if there are no
  core mathematical derivations, explicitly state so and skip.

## 3. Theoretical Paper
- **Section 5 (具体方法与真实案例 pipeline)**: Trace a concrete toy system or
  1D/2D mathematical example through the definitions, assumptions, lemmas, and
  main theorem.
- **Section 6 (零基础数学推导)**: This is the centerpiece. Build up the required
  math intuition from scratch before walking through the proof structure and
  key inequalities.
- **Section 7 (实验验证)**: Summarize numerical simulations or synthetic checks
  in the `问题 -> 实验 -> 答案` format; if purely theoretical without experiments,
  note so explicitly.

## 4. Survey / Review Paper
- **Section 5**: Present the survey's taxonomy and walk through how a concrete
  representative task is solved across different historical paradigms.
- **Section 6**: Compare the unifying mathematical formulations across paradigms
  (or skip if purely qualitative).
- **Section 7**: Summarize the survey's meta-analysis or benchmark comparisons
  using `问题 -> 对比维度 -> 结论`.
