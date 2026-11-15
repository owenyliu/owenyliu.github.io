# CS197 Project 3 — Findings Write-up
**Evaluating Numerical Preprocessing Methods for Tabular Representation Learning**
Team: Max Lee, Owen Liu, Rita Xiang · Data: eICU demo (full-data re-run pending) · Date: 2026-10-29, revised 2026-10-31

**Correction (2026-10-31).** The team's selected six methods are: **standardized scalar, quantile-normalized scalar, mode-specific normalization (Gaussian mixtures), quantile binning, PLE, and periodic**. An earlier version of this write-up (and of progress report 6) used a six-list transcribed incorrectly from progress report 5, which put log and min–max binning in the six and demoted quantile binning and periodic to "extras." All runs already existed in the same 36-run grid, so every number below was re-aggregated for the corrected six; log, min–max binning, and Yeo-Johnson are now reported as the extra conditions.

---

## TL;DR

1. **The standardized baseline is genuinely good.** No encoding beat it overall — it has the best latent geometry (Spearman 0.890) and near-best feature recovery (R² 0.649). Last report's open question is answered: yes, a standardized-input VAE is a good representation.
2. **Prediction and fidelity dissociate.** Mode-specific normalization produces the *worst* latent structure (recovery 0.229, geometry 0.176, near-zero KL) yet the *best* ranking of the rare outcome (probe AUPRC 0.137, ~1.9× the baseline). "Good representation" depends on which metric you mean.
3. **Binning collapses inside a VAE** — quantile binning, the binning method among the six (recovery 0.053, geometry 0.056, KL 0.013), and the extra equal-width (min–max) binning fails identically, under both fairness settings and every seed. This is a property of binning here, not of the binning scheme or of training luck.
4. **The skew story is narrower than textbook intuition.** Standard scaling already recovers creatinine, glucose, and BUN best. Only the single most extreme feature (WBC) actually needs a rank-based transform — within the six, quantile-normalized rescues it (R² 0.34 vs −0.05 for standard), with PLE (0.30) and mode-specific (0.19) also clearing the +0.10 rescue threshold.
5. **All headline effects are demo-scale.** The test split has 11 deaths; AUPRC gaps (especially mode-specific's, SD 0.051) are suggestive, not conclusive. The full-data re-run is the deciding test.

## 1. Question and setup

**Question.** For tabular representation learning with a VAE, does the numerical preprocessing (encoding) of input features change the quality of the learned latent representation — and if so, on which notion of quality?

**Setup (frozen before any comparison runs).** eICU demo: 1,722 stays, 48-hour mortality 3.31%. Frozen Week-2 feature set: 20 features (12 numerical, 5 binary admission-diagnosis indicators, 3 categorical), encoded input 37 dimensions for scalar encodings. Stratified splits (seed 42): train 1,101 / validation 276 (9 deaths) / test 345 (11 deaths). Week-3 VAE configuration frozen (latent 8, 100 epochs, Adam). Mortality at 48 h is the target Y — predicted from Z, never an input (no leakage).

**Comparison protocol.** 9 encodings × 3 seeds, parameter-fair (hidden width adjusted so every model has ≈14,700 parameters, matching the standard baseline), plus fixed-width (hidden 64) contrasts — 36 runs total. The team's six: **standardized scalar, quantile-normalized scalar, mode-specific (Gaussian mixtures), quantile binning, PLE, periodic**. Extra conditions: log, min–max binning, Yeo-Johnson. Metrics (test): downstream prediction (logistic probe on Z: AUROC, AUPRC), feature recovery (per-feature linear R² from Z, averaged), geometry preservation (Spearman correlation of pairwise distances, Z vs X).

## 2. Headline results

Mean ± SD over 3 seeds, parameter-fair:

| Encoding | Probe AUROC | Probe AUPRC | Recovery R² | Geometry (Spearman) |
|---|---|---|---|---|
| Standard | 0.661 ± 0.020 | 0.072 ± 0.015 | **0.649 ± 0.034** | **0.890 ± 0.021** |
| Quantile-normalized | 0.686 ± 0.015 | 0.074 ± 0.014 | 0.611 ± 0.014 | 0.641 ± 0.032 |
| Mode-specific | 0.669 ± 0.051 | **0.137 ± 0.051** | 0.229 ± 0.015 | 0.176 ± 0.012 |
| Quantile binning | 0.522 ± 0.050 | 0.043 ± 0.008 | 0.053 ± 0.015 | 0.056 ± 0.014 |
| PLE | 0.663 ± 0.013 | 0.056 ± 0.001 | 0.345 ± 0.014 | 0.251 ± 0.025 |
| Periodic | **0.705 ± 0.008** | 0.081 ± 0.002 | 0.352 ± 0.006 | 0.374 ± 0.042 |
| *Extra: log* | *0.659* | *0.083* | *0.594* | *0.743* |
| *Extra: min–max binning* | *0.592* | *0.078* | *0.074* | *0.126* |
| *Extra: Yeo-Johnson* | *0.666* | *0.057* | *0.662* | *0.745* |

Two patterns dominate the table: (i) the two scalar encodings among the six (standard, quantile-normalized) cluster together on structure metrics, with standard on top; (ii) every high-dimensional encoding (quantile binning, PLE, mode-specific, periodic) sacrifices structure — but two of them (mode-specific, periodic) buy probe performance with the sacrifice.

## 3. Findings

**F1 — The baseline is strong (H5: mostly supported).** Across all non-binning encodings, probe AUROC spans only 0.66–0.71. Standard scaling leads on recovery (0.649) and geometry (0.890) and is within noise of the best probe scores. Any claim that preprocessing "unlocks" representation quality is not supported at this scale; the honest summary is that the choice of encoding moves specific metrics, not overall quality.

**F2 — Prediction and fidelity dissociate (H2: supported, with a twist).** Mode-specific normalization is the structural worst (recovery 0.229, geometry 0.176) and its latent space barely departs from the prior (final validation KL ≈ 0.03, vs 2.69 for standard) — yet it ranks the rare outcome best (AUPRC 0.137 vs 0.072 standard). A plausible mechanism: per-mode rescaling sharpens a few clinically extreme values that matter for mortality ranking, while the many-to-one mode assignment destroys the fine-grained distances that recovery and geometry measure. Whatever the mechanism, evaluating representations on fidelity alone would have discarded the best predictor, and vice versa.

**F3 — Binning collapses in the VAE (H3: supported, stronger than stated).** Quantile binning — the binning method among the six — fails outright: recovery 0.053, geometry 0.056, probe at chance (AUROC 0.522), final KL 0.013 (posterior collapse), across all seeds and both capacity settings; the extra min–max binning scheme fails the same way (recovery 0.074, geometry 0.126). Because equal-frequency and equal-width bins fail identically, the cause is binning itself under this VAE: one-hot targets make reconstruction easy to satisfy with an uninformative posterior, and within-bin information is discarded before the model ever sees it. Binning's tabular-learning reputation comes from gradient-boosted trees; it does not transfer to this VAE.

**F4 — Skew handling matters for exactly one feature (H1: mostly not supported).** Per-feature recovery contradicts the blanket claim that skewed features need rank/log transforms: standard scaling recovers creatinine (0.80), glucose (0.80), and BUN (0.78) *better* than quantile-normalized (0.48/0.65/0.64). The exception is the most extreme feature, WBC (skew an order of magnitude larger): standard −0.05, vs quantile-normalized 0.34, PLE 0.30, mode-specific 0.19, periodic 0.08. Rule of thumb supported by the data: transforms rescue the single worst tail, not skew in general.

**F5 — The bounded-feature intuition is wrong (H4: not supported).** Oxygen saturation (bounded 0–100) is recovered best by the scalar encodings among the six (standard 0.50, quantile-normalized 0.49) and worst by the "specialized" choices (quantile binning 0.02, mode-specific 0.09, periodic 0.19). Boundedness alone does not call for a specialized encoding.

**F6 — Periodic is a probe-only win, and the most consistent one.** Best probe AUROC of the six (0.705), ahead of standard on all three seeds (paired Δ +0.044; 95% CI −0.005 to 0.093) — but mediocre recovery (0.352) and geometry (0.374). A second instance of the F2 dissociation, and a reminder that probe AUROC alone would crown a representation that preserves little of the input.

**F7 — Among the extras, log is the quiet all-rounder.** Second-best AUPRC overall (0.083), second-best geometry (0.743), rescues WBC (0.36), and healthy posterior use (KL 2.72); the extra Yeo-Johnson posts the best recovery overall (0.662). Neither is among the team's six, so both are context only — but if the team ever adds a sensitivity condition next to the standard baseline on full data, log is the best candidate.

## 4. Why we trust these numbers (diagnostics)

- **No failed runs.** All 36 runs converged; no NaNs; the two "collapses" (binning, mode-specific KL) repeat identically across seeds and capacity settings, so they are properties of the encodings, not training accidents.
- **Seed variability is small** for structure metrics (SD ≤ 0.03) and moderate for probe AUPRC — which is why AUPRC claims are hedged.
- **Validation losses are not compared across encodings** (each encoding changes the reconstruction target's scale; mode-specific's "lowest" val loss of 10.46 is an artifact of its target scale, not better fit). All comparisons use the downstream metrics above.
- **Parameter fairness** was enforced (hidden width per encoding adjusted to ≈14,700 total parameters) and cross-checked with fixed-width runs; conclusions are the same under both.

## 5. Caveats — what could change on the full data

1. **Sample size.** 11 test deaths make AUPRC the noisiest metric; mode-specific's advantage (0.137 ± 0.051) could shrink or vanish at full scale. AUROC and structure metrics are more stable.
2. **Demo ≠ full eICU.** The demo is a 10-hospital subset with its own case mix; full data (~200k stays) changes both the statistics and the feature distributions (e.g., genuinely multimodal features may appear that the demo lacks — mode-specific's home turf).
3. **Our frozen feature spec** (20 features incl. categoricals) differs from the team's 15-numerical + 5-binary spec used in the Week-4 report; absolute numbers are not directly comparable across the two setups, though the within-study comparisons are valid.
4. **Course-skeleton bugs** found and fixed in our working copy (two encoder bugs, a Week-3 label-alignment trap) mean our pipeline is the *corrected* reference; results from the uncorrected skeleton would differ.

## 6. Recommendations

1. **Backbone:** carry standard scaling into the final evaluation as the primary representation; within the six, add quantile-normalized as the single sensitivity condition (the extra log is the strongest non-six all-rounder).
2. **Deciding experiment:** on full data, test the mode-specific AUPRC advantage first — it is the only result that, if real, changes the project's conclusion.
3. **Negative result worth reporting:** quantile binning (with min–max binning, an extra, failing identically) — a clean, replicated failure with a mechanism (posterior collapse), valuable in the final report precisely because binning is popular elsewhere.
4. **Mechanism checks** (if time): a KL/prior sweep for mode-specific, and a target-scaling control for binning, to convert the F2/F3 observations from patterns into explanations.

*Artifacts: full run grid and summaries in the team repo (branch `cs197-weeks-1-6`); comparison figure `fig_week6_all_encodings.png`; the encoding-comparison progress report is in Overleaf as `progress_report6.tex`.*

---

# CS197 项目 3 — 研究发现报告（中文版）

**表格表示学习中的数值预处理方法评估**
团队：Max Lee、Owen Liu、Rita Xiang · 数据：eICU demo（完整数据重跑待进行）· 日期：2026-10-29，2026-10-31 修订

**更正（2026-10-31）。** 团队选定的六个方法是：**标准化标量（standardized scalar）、quantile 归一化标量（quantile-normalized scalar）、mode-specific 归一化（Gaussian 混合）、quantile 分箱（quantile binning）、PLE、periodic**。本报告（及进展报告 6）的早期版本把六人名单从进展报告 5 中转录错误，误把 log 和 min–max 分箱放进六人组、把 quantile 分箱和 periodic 降为"额外条件"。所有运行本就在同一个 36 次网格里，下面的全部数字已按更正后的六人组重新汇总；log、min–max 分箱和 Yeo-Johnson 改列为额外条件。

## 摘要

1. **标准化基线确实很强。** 没有任何编码在整体上击败它 — 它的潜在几何最好（Spearman 0.890）、特征恢复近乎最佳（R² 0.649）。上期报告的开放问题有了答案：是的，标准化输入的 VAE 就是一个好的表示。
2. **预测与保真度相互分离。** Mode-specific 归一化的潜在结构最差（恢复 0.229、几何 0.176、KL 近零），却对罕见结局的排序最好（探针 AUPRC 0.137，约为基线的 1.9 倍）。"好表示"取决于你用哪个指标。
3. **分箱在 VAE 中会崩溃** — quantile 分箱是六人组里的分箱方法（恢复 0.053、几何 0.056、KL 0.013），额外条件等宽（min–max）分箱以相同方式失败，两种公平设置、所有种子下都一样。这是分箱本身在此 VAE 下的性质，与分箱方式或训练运气无关。
4. **偏态的故事比教科书直觉窄得多。** 标准化已经能最好地恢复肌酐、血糖和尿素氮。只有最极端的那一个特征（白细胞）真正需要秩变换 — 六人组内 quantile 归一化能救回它（R² 0.34，标准化为 −0.05），PLE（0.30）与 mode-specific（0.19）也越过了 +0.10 的救回阈值。
5. **所有头条效应都只是 demo 规模。** 测试集只有 11 例死亡；AUPRC 的差距（尤其 mode-specific 的，标准差 0.051）只能算提示性证据，不是定论。完整数据重跑才是决定性检验。

## 1. 问题与设置

**问题。** 在用 VAE 做表格表示学习时，数值特征的预处理（编码）方式会改变学到的潜在表示的质量吗？如果会，改变的是哪一种"质量"？

**设置（在任何对比实验之前冻结）。** eICU demo：1,722 例住院，48 小时死亡率 3.31%。Week 2 冻结的特征集：20 个特征（12 个数值、5 个二值入院诊断指示、3 个类别），标量编码的输入维度为 37。分层划分（种子 42）：训练 1,101 / 验证 276（9 例死亡）/ 测试 345（11 例死亡）。Week 3 的 VAE 配置冻结（潜在维度 8、100 轮、Adam）。48 小时死亡率是目标 Y — 由 Z 预测，从不作为输入（无泄漏）。

**对比协议。** 9 种编码 × 3 个种子，参数公平（调整隐藏宽度使每个模型总参数 ≈14,700，与标准基线一致），外加固定宽度（隐藏 64）对照 — 共 36 次运行。团队的六种：**标准化标量、quantile 归一化标量、mode-specific（Gaussian 混合）、quantile 分箱、PLE、periodic**。额外条件：log、min–max 分箱、Yeo-Johnson。指标（测试集）：下游预测（Z 上的逻辑回归探针：AUROC、AUPRC）、特征恢复（由 Z 线性恢复每个特征的 R² 取平均）、几何保持（Z 与 X 成对距离的 Spearman 相关）。

## 2. 头条结果

3 个种子的均值 ± 标准差，参数公平：

| 编码 | 探针 AUROC | 探针 AUPRC | 恢复 R² | 几何 (Spearman) |
|---|---|---|---|---|
| Standard | 0.661 ± 0.020 | 0.072 ± 0.015 | **0.649 ± 0.034** | **0.890 ± 0.021** |
| Quantile 归一化 | 0.686 ± 0.015 | 0.074 ± 0.014 | 0.611 ± 0.014 | 0.641 ± 0.032 |
| Mode-specific | 0.669 ± 0.051 | **0.137 ± 0.051** | 0.229 ± 0.015 | 0.176 ± 0.012 |
| Quantile 分箱 | 0.522 ± 0.050 | 0.043 ± 0.008 | 0.053 ± 0.015 | 0.056 ± 0.014 |
| PLE | 0.663 ± 0.013 | 0.056 ± 0.001 | 0.345 ± 0.014 | 0.251 ± 0.025 |
| Periodic | **0.705 ± 0.008** | 0.081 ± 0.002 | 0.352 ± 0.006 | 0.374 ± 0.042 |
| *额外：log* | *0.659* | *0.083* | *0.594* | *0.743* |
| *额外：min–max 分箱* | *0.592* | *0.078* | *0.074* | *0.126* |
| *额外：Yeo-Johnson* | *0.666* | *0.057* | *0.662* | *0.745* |

表中两个主导模式：(i) 六人组里的两个标量编码（standard、quantile 归一化）在结构指标上聚成一团，standard 居首；(ii) 所有高维编码（quantile 分箱、PLE、mode-specific、periodic）都牺牲结构 — 但其中两个（mode-specific、periodic）用牺牲换来了探针性能。

## 3. 研究发现

**F1 — 基线很强（H5：基本成立）。** 所有非分箱编码的探针 AUROC 只在 0.66–0.71 之间。标准化在恢复（0.649）和几何（0.890）上领先，探针分数也与最佳者在噪声范围内相当。"预处理能解锁表示质量"这一说法在当前规模下不成立；诚实的总结是：编码的选择移动的是个别指标，而不是整体质量。

**F2 — 预测与保真度分离（H2：成立，但有转折）。** Mode-specific 归一化结构最差（恢复 0.229、几何 0.176），潜在空间几乎不偏离先验（最终验证 KL ≈ 0.03，standard 为 2.69）— 却对罕见结局的排序最好（AUPRC 0.137 vs standard 0.072）。可能的机制：按众数缩放会锐化少数对死亡排序重要的临床极端值，而多对一的众数分配抹掉了恢复和几何所度量的细粒度距离。无论机制如何，只用保真度评估表示会丢掉最好的预测器，反之亦然。

**F3 — 分箱在 VAE 中崩溃（H3：成立，且比预想更强）。** Quantile 分箱 — 六人组里的分箱方法 — 彻底失败：恢复 0.053、几何 0.056、探针接近随机（AUROC 0.522）、最终 KL 0.013（后验坍塌），所有种子、两种容量设置下都如此；额外的 min–max 分箱以相同方式失败（恢复 0.074、几何 0.126）。等频与等宽分箱失败方式相同，说明原因是分箱本身在此 VAE 下的问题：one-hot 目标让重构很容易用无信息的后验来满足，且箱内信息在进入模型之前就被丢弃。分箱在表格学习中的名声来自梯度提升树；它迁移不到这个 VAE 上。

**F4 — 偏态处理只对一个特征重要（H1：基本不成立）。** 逐特征恢复与"偏态特征需要秩/log 变换"的笼统说法相反：标准化恢复肌酐（0.80）、血糖（0.80）、尿素氮（0.78）比 quantile 归一化（0.48/0.65/0.64）*更好*。例外是最极端的特征白细胞（偏度大一个数量级）：标准化 −0.05，而 quantile 归一化 0.34、PLE 0.30、mode-specific 0.19、periodic 0.08。数据支持的经验法则：变换能救的是最严重的那条尾巴，而不是偏态本身。

**F5 — "有界特征"的直觉是错的（H4：不成立）。** 血氧饱和度（有界 0–100）由六人组里的标量编码恢复得最好（standard 0.50、quantile 归一化 0.49），由"专门"编码恢复得最差（quantile 分箱 0.02、mode-specific 0.09、periodic 0.19）。仅仅"有界"并不构成使用专门编码的理由。

**F6 — Periodic 是"只赢探针"的胜利，而且是六人组里最一致的一个。** 六人组中探针 AUROC 最佳（0.705），三个种子全部高于 standard（配对 Δ +0.044；95% CI −0.005 到 0.093）— 但恢复（0.352）和几何（0.374）平庸。这是 F2 分离现象的第二个实例，也提醒我们：只看探针 AUROC 会给一个几乎不保留输入信息的表示加冕。

**F7 — 额外条件里，log 是低调的全能选手。** AUPRC 整体第二（0.083）、几何第二（0.743）、能救回白细胞（0.36）、后验使用健康（KL 2.72）；额外的 Yeo-Johnson 则有整体最佳恢复（0.662）。两者都不在团队六人组内，只作背景 — 但若团队今后要在完整数据上给标准基线加一个敏感性条件，log 是最佳候选。

## 4. 为什么这些数字可信（诊断）

- **没有失败的运行。** 36 次运行全部收敛、无 NaN；两处"坍塌"（分箱、mode-specific 的 KL）在各种子和容量设置下完全一致地复现，是编码的性质而非训练事故。
- **种子间波动小**：结构指标标准差 ≤ 0.03；探针 AUPRC 波动中等 — 这正是 AUPRC 相关结论留有余地的原因。
- **不跨编码比较验证损失**（每种编码都改变重构目标的尺度；mode-specific "最低"的验证损失 10.46 是其目标尺度的产物，不是拟合更好）。所有比较都用上面的下游指标。
- **参数公平已强制执行**（按编码调整隐藏宽度至总参数 ≈14,700），并用固定宽度运行交叉验证；两种设置下结论相同。

## 5. 注意事项 — 完整数据上可能变化的

1. **样本量。** 测试集 11 例死亡使 AUPRC 成为噪声最大的指标；mode-specific 的优势（0.137 ± 0.051）在完整规模下可能缩小或消失。AUROC 和结构指标更稳定。
2. **Demo ≠ 完整 eICU。** Demo 是 10 家医院的子集，有自己的病例构成；完整数据（约 20 万例）会同时改变统计量和特征分布（例如可能出现 demo 里没有的真正多峰特征 — 那是 mode-specific 的主场）。
3. **我们冻结的特征规格**（20 个特征，含类别变量）与团队 Week 4 报告使用的 15 数值 + 5 二值规格不同；两套设置的绝对数字不能直接比较，但本研究内部的比较是有效的。
4. **课程骨架的 bug** 已在我们的副本中发现并修复（两个编码器 bug、一个 Week 3 标签对齐陷阱），所以我们的管线是*修正后*的参照；用未修正骨架跑出的结果会不同。

## 6. 建议

1. **主干：** 最终评估以标准化为主要表示；六人组内以 quantile 归一化作为唯一的敏感性条件（额外的 log 是非六人组里最强的全能选手）。
2. **决定性实验：** 在完整数据上先检验 mode-specific 的 AUPRC 优势 — 这是唯一一个若属实就会改变项目结论的结果。
3. **值得报告的负结果：** quantile 分箱（额外的 min–max 分箱以相同方式失败）— 一个干净、可复现、带机制（后验坍塌）的失败，正因为分箱在别处很流行，它在最终报告里才有价值。
4. **机制检验**（如有时间）：对 mode-specific 做 KL/先验扫描、对分箱做目标尺度对照，把 F2/F3 从"模式"推进为"解释"。

*相关产物：完整运行网格与汇总在团队仓库（分支 `cs197-weeks-1-6`）；对比图 `fig_week6_all_encodings.png`；编码对比进展报告已在 Overleaf 中存为 `progress_report6.tex`。*
