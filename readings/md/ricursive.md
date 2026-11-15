# 公司研究包：Ricursive Intelligence ＋ Recursive Superintelligence（消歧版）

为 Owen 面试准备整理 · 2026-11-09 · 数据均来自公开来源，关键数字标注口径与日期

---

## 0. 先消歧：有两家名字几乎一样的公司，别搞混 ⚠️

| | **Ricursive Intelligence** | **Recursive**（Recursive Superintelligence） |
|---|---|---|
| 创始人 | Anna Goldie（CEO）+ Azalia Mirhoseini（CTO），AlphaChip 作者 | **Richard Socher**（前 Salesforce 首席科学家）任 CEO |
| 做什么 | AI 设计芯片（AI for chip design） | AI 自动做研究（automated AI research），"Eureka Machine" 系统 |
| 首个产品 | Ripple-65B（2026-11-04） | Flint 本地编程模型（2026-10-21） |
| 融资 | $35M seed（Sequoia 领投，2025-12）+ $300M Series A（Lightspeed 领投，2026-01），估值 ~$4B | 2026-05 出 stealth：$650M，估值 $4.65B（GV、NVIDIA、AMD Ventures） |
| 总部 | Palo Alto | San Francisco |
| 人数 | 见 §2（约 10–23，口径不一） | LinkedIn 列出 **61 employees**，公司自填规模 11–50 |

⚠️ 数据污染警告：个别第三方数据库（如 siliconvalleyinvestclub）把 Recursive 的「$650M / $4.65B / May 2026」错安到 Ricursive 头上。引用 Ricursive 融资时只用 $335M / ~$4B 口径（TechCrunch 等多家证实）。

两家都在讲 "recursive self-improvement"（递归自我改进），面试时主动点破区别，是加分项。

---

## 1. Ricursive Intelligence 一页纸

- **一句话**：frontier AI lab，用 AI 设计芯片，在「AI ↔ 养育 AI 的硬件」之间闭环，递归加速通往超级智能之路（官网原话：closing the loop between AI and the hardware that fuels it, recursively accelerating the path to artificial superintelligence）。
- **成立**：2025 年，2025-12-02 公开亮相；Palo Alto。
- **创始团队履历**：AlphaChip（Nature 2021）、RL-CCD（DAC Best Paper 2023）、INSTA（DAC Best Paper 2025）、C3PO（ASP-DAC Best Paper 2026）；做过 Gemini、Claude、TPU、Apple Silicon。
- **投资人**：Sequoia（seed）、Lightspeed（A 轮领投）、DST Global、NVIDIA NVentures、Felicis、49 Palms、Radical Ventures。
- **荣誉**：两位创始人入选 TIME100 AI 2026；TechCrunch Disruptors60；Breakout List。

## 2. 人数与董事会（Ricursive）

人数（没有单一权威数字，按时间排列）：
- LinkedIn 公司页自填规模：**2–10 employees**（明显滞后）；followers 9,772
- PitchBook：10 人
- 2026-01 A 轮报道：公布时 fewer than 10 人；专题报道按 ~19 人（2025-12 至 2026-02 口径）
- Liftoff Jobs：23 人 + **16 个在招岗位**
- Ripple 模型小队本身：项目期间从 1 人长到 5 人
→ 对外稳妥说法：**约 20 人上下，正在快速扩招**。

董事会：私有公司未公布完整名单。公开可证实的外部董事只有 **Stephanie Zhan**（Sequoia 合伙人，领投第一轮，2025-11 起任董事）。Crunchbase 公司页只列「Board Members and Advisors: 1」。Lightspeed 领投 A 轮（合伙人 Guru Chahal 在公告中发言），但其董事席位无公开证实，不要当事实讲。

## 3. 两位创始人与代表论文（一条主线：让 AI 学会自己做决定）

| 年份 | 论文/成果 | 一句话 |
|---|---|---|
| 2017 | Device Placement（ICML；分层版 ICLR 2018） | 让 AI 学会把大程序分工到多台机器上（Azalia 主导，Anna 共同一作） |
| 2017 | Sparsely-Gated MoE（Shazeer et al.，Azalia 合著） | 「专家分工」架构，今天大模型的标配 |
| 2021 | **AlphaChip**（Nature，两人共同一作） | RL 做芯片布局，几小时胜过人类几个月；用于多代 Google TPU |
| 2022 | Constitutional AI（Anthropic，Anna 合著） | 给 AI 一本「宪法」让它自我监督 |
| 2024 | RAPTOR（ICLR，Anna + 导师 Manning） | 递归摘要树做检索（arXiv:2401.18059） |
| 2025 | SWiRL（Anna） | 小模型自造数据、多步 RL 练推理 — 自我改进的最新一步 |

链接：
- AlphaChip Nature：https://www.nature.com/articles/s41586-021-03544-w
- DeepMind 博客版：https://deepmind.google/blog/how-alphachip-transformed-computer-chip-design/
- Constitutional AI：https://arxiv.org/abs/2212.08073
- RAPTOR：https://arxiv.org/abs/2401.18059
- Anna 论文全表：https://www.annagoldie.com
- Azalia Google Scholar：https://scholar.google.com/citations?user=I1nZzWsAAAAJ
- Anna Google Scholar：Scholar 对自动访问有拦截，精确链接待人工打开作者搜索确认；alphaXiv 记其引用 ~18k

引用量参考（alphaXiv 口径）：Anna ~18k，Azalia ~12k。

## 4. 它解决的问题 & 更大的愿景

问题：
- 一颗新芯片从设计到流片要 2–3 年（复杂的可达 3–5 年）、几百名工程师；AI 模型几个月就换一代 — 硬件永远在为上一代 AI 服务。
- 算力需求爆炸（Goldie：未来五年 compute 需求或涨 10,000 倍），摩尔定律放缓；瓶颈不在工厂，在**设计本身**。

解法：端到端 AI 自动化芯片设计全部环节（架构、布局、布线、验证），目标把周期从「年」压缩到「周/天」。

愿景（三层）：
1. 近：芯片设计 年 → 周；
2. 中：设计民主化 — 任何团队都能快速定制专用芯片，不再只能用通用 GPU；
3. 远：递归循环 — 更好的 AI 设计更好的芯片，更好的芯片训出更强的 AI，指数加速通往 ASI。

## 5. 产品：Ripple-65B Preview（2026-11-04 发布）

- 65B dense（非 MoE）开源权重模型，Apache 2.0；12T tokens 从零训练；1M context。
- 自报成绩：Terminal-Bench 2.1 **80.5**（美国开源模型 SOTA，超 Reflection Beam 501B 的 80.1、Meta Muse Spark Open 816B）；SWE-Bench Pro v2.0-Hard 79.1；DeepSWE 44.3。
- 自报成本：全程 R&D $32M（称约为上一代 SOTA 的 1/10）；1.4M RL rollouts（称比 Reflection 少约 70×）；团队 1→5 人。
- 定位：Ripple 是其芯片设计平台底层模型的通用版，发布意在让外界评判平台技术。
- ⚠️ 全部数字为公司自报，**尚无独立复现**；post-training 较轻，官方自称 Preview。

## 6. 竞争格局（三圈）

第一圈 — EDA 三巨头（主战场，合计约占 EDA 收入 75–90%，握有流程、格式、晶圆厂认证）：
- Synopsys（~31–36%）：Synopsys.ai / DSO.ai；2025 年 $35B 收购 Ansys；2026-09 推 GPT-Synopsys
- Cadence（~30%）：Cerebrus / JedAI；收购 ChipStack（AI Super Agent）
- Siemens EDA（~13%）

第二圈 — 同叙事创业公司：
- Cognichip：共融资 $93M；物理感知基础模型 ACI；称降本 75%、周期减半；Intel CEO 陈立武入董事会；同样尚无流片芯片
- ChipAgents：$74M（2026-02 扩展 A 轮）；在现有工具之上的 agentic 设计+验证
- TenX Semi：seed（Khosla / South Park Commons）；赌「验证」是新瓶颈；联创含 Stanford 教授 Subhasish Mitra
- Normal Computing 等；DAC 2026 光 agentic 层就约 22 家

第三圈 — 隐形对手：NVIDIA、Google、Samsung 等大客户自建内部 AI 设计团队。

EE Times 的 DAC 2026 产业地图把 Ricursive 归为最激进的「自研硅基础模型」类，称其为真正的 engine-replacer：「from fabless to designless」。

## 7. 市场大小（分层，别混口径）

| 层 | 数字 | 来源 |
|---|---|---|
| EDA 软件总市场 | 2025 年 $17.8B → 2026 年 $19.5B → 2033 年 $37.7B，CAGR ~9.9% | Grand View Research |
| 其中 AI EDA | 2026 年 $4.27B → 2032 年 $15.85B，CAGR 24.4%；前五名占 70–85% | MarketsandMarkets |
| Ricursive 的大饼逻辑 | 一颗先进芯片设计成本数亿美元量级（人力×年）；若 AI 接管「设计工作本身」，可争夺的是人力+服务支出，数倍于软件市场 — 这是 $4B 估值的支点，不是已统计市场 | 推理口径，非统计数字 |

## 8. Google 现在做到哪一步（对照组）

- AlphaChip 已用于最近三代 TPU，包括第七代 **Ironwood**（2026-03-31 GA）；TPU 第八代 8t/8i 已于 2026-04 宣布。
- **AlphaEvolve**（2025-05）更进一步：改写了 TPU 算术电路的 Verilog 代码并通过完整验证 — 从「排布局」进到「改电路」。
- 定性：Google 内部已生产级交付，但仍只是设计流程中的一步（+电路一小块）；端到端自动设计未实现；外部可复现性仍有争议（UCSD Kahng 团队的复现质疑 vs Google 的回应，Nature 编辑注已于 2024-09 移除）。
- 分界线：Google = 把一步做到生产级；Ricursive = 赌全部步骤 AI 化。

## 9. 质疑与风险（面试前必须心里有数）

1. AlphaChip 的外部复现争议未完全平息（批评方：Kahng/Cheng 团队、Igor Markov 的 meta-analysis）。
2. Ricursive 与 Cognichip 都**还没有一颗公开流片验证的芯片** — 故事兑现度待证。
3. Ripple 的全部 benchmark 与成本数字为自报，无独立复现。
4. 验证（verification）是公认最难 AI 化的一环，也是周期大头 — TenX 等对手正攻此点。
5. 三巨头的认证/流程护城河 + 大客户自建团队，构成上下夹击。

## 10. 面试弹药：你的连接点 & 可以问的问题

你的天然连接点（真实、具体，别泛泛而谈）：
- **Terminal-Bench**：Ripple 的头条成绩就来自 Terminal-Bench 2.1（80.5）；你之前的公司正是通过 Terminal Bench 向 frontier labs 提供 evals / 数据 — 这个交叉点极少有人有。
- **决策模型与把关**：你的 CS329Z 项目 Medical MythTriage 做的正是「快模型先判断、置信门控、慢模型复核」 — 与她们 SWiRL / 自我改进主线、以及「何时信任直觉、何时停下来验证」的问题意识同源。
- **Azalia 本人**：你在 AGI House 见过她，她是 CS329A《Self-Improving AI Agents》授课人、Scaling Intelligence Lab 主任。

可以反问对方的高质量问题：
1. Ripple 的通用模型与芯片设计平台的专用模型，共享多少训练栈？平台模型何时会以可验证的形式（流片/客户案例）公开？
2. 验证环节目前自动化到什么程度？团队认为端到端里最难闭环的是哪一步？
3. 与三巨头是合作（跑在他们的 flow 上）还是替换？foundry 认证怎么解决？
4. 约 20 人的团队接下来翻倍时，优先补的是芯片老兵还是 ML 研究员？为什么？
5. AlphaChip 的复现争议，公司内部怎么看待「可复现性」作为对外沟通标准？

---

## 附：Recursive Superintelligence（Socher 那家）速记

- CEO Richard Socher（前 Salesforce 首席科学家、You.com 创始人）；2026-05 出 stealth，$650M 融资、$4.65B 估值（GV、NVIDIA、AMD Ventures 等）。
- 核心系统 **Eureka Machine**：自动化 AI 研究系统，目标是让 AI 改进 AI 的研发全流程（数据、训练配方、基础设施、优化）。
- **Flint**（2026-10-21）：40B MoE、每 token 只激活 2.5B；为本地运行设计（M4 Pro 上 125+ tok/s）；Apache 2.0 开源权重；号称由 Eureka Machine 参与构建、不到四个月完成。
- 人数：LinkedIn 列 **61 employees**，公司自填规模 11–50；总部旧金山；followers 约 16k。
- 与 Ricursive 的关系：无股权/业务关系，只是名字与叙事相近 — 面试中切勿混淆。

---

## 12. Anna 的公开演讲/对谈汇总（2026-11-09 补）

| # | 场合 | 时间 | 形式 | 核心内容 |
|---|---|---|---|---|
| 1 | Sequoia《Training Data》播客（= AI Ascent 视频版，YouTube 55LT52eVArM） | 2026-01-14 | 37 min，双人+Zhan+Huang | 瓶颈论、AlphaChip 2018 起步、逐代 TPU 采用扩大、超人表现、训练数据是约束 |
| 2 | Sequoia 活动演讲（StartupHub 记为 "AI Designs Its Own Chips with Ricursive"） | 2026 年内 | 演讲 | **STA 引擎：全图时序分析毫秒级，与商用工具相关性 0.999+，快 1000x**（疑为 NVIDIA DAC 1000x 之所指）；LLM 驱动 PPA 优化；三阶段：加速设计→端到端协同设计→递归自主 |
| 3 | The Information《AI Deep Dive》（TI TV） | 2026-09-29/30 | 访谈，双人 | RSI=整个系统（软+硬+机架+数据中心）；GPU/TPU 的 MFU 仅 ~30%，拉到近 100% = 训练快 ~3x；RSI 是光谱不是开关；赢家通吃；芯片获取与中美竞争 |
| 4 | Radical Talks Masterclass Edition | 2026-10-22 | 31 min 播客，Anna 单人 × Rob Toews | 创业三条件；Google 曾给 spin-out 方案被拒、选独立；先啃最难的；LLM 单独解不了芯片设计；选长线投资人 |
| 5 | TechCrunch Disrupt | 2026-10 | 双人对谈 | 递归闭环讲解；芯片有硬指标（面积/功耗/速度）所以适合 AI——可验证的领域 |
| 6 | WTF is AI（Nikhil Kamath） | 2026-10 | 圆桌（含 Notion COO 等） | Anna 约在 1:24 段；AI 泡沫/循环投资辩论背景下的闭环讲解 |

**贯穿所有演讲的五条主线**：① 设计是瓶颈（2–3 年 vs 模型数月一迭代）；② AlphaChip 证明单步可超人（逐代采用扩大）；③ 三步走：加速设计 → 端到端定制硅寒武纪爆发 → 闭合递归环（自有芯片+自有模型）；④ 闭环 = AI 设计更好芯片、芯片训练更强 AI；⑤ 民主化：fabless → designless。

**新技术细节（面试可用）**：STA 引擎 1000x + 0.999 相关性；MFU 30%→100% 的 3x 训练加速论证；RSI 是 spectrum 不是 binary。

---

## 13. 一手文件：Anna & Azalia 的 2023 年公开声明（Owen 2026-11-09 分享的 Drive 链接）

- 文件：《Statement on Reinforcement Learning for Chip Design》，Anna Goldie & Azalia Mirhoseini，2023-03-24；Drive 中的原件所有者即 Anna Goldie 本人。本地存档：~/workspace/your_files/ricursive-sources/statement-rl-chip-design.pdf
- 背景：回应 ISPD 2023 invited paper（UCSD Kahng/Cheng 等）对 AlphaChip / Circuit Training 的复现性质疑——即 AlphaChip 争议的核心交锋文件。
- 她们的防线：① 生产部署即终极验证（多代 TPU 已流片、在数据中心运行，sub-10nm）；② Nature 7 个月透明同行评审 + TF-Agents 团队独立复现；③ 2022-01 已开源（EDA 行业罕见，AlphaGo 都没开源）。
- 技术反驳要点：批评方未做预训练（她们在 20 个 block 上预训练后再测 held-out）；算力只有一半 GPU、少一个数量级的 RL 环境且训练被提前截断；即便如此其 Table 1 里仍胜 RePlAce（对方在 6 个用例中 2 个直接无结果）；对方拿 2023 年的 AutoDMP 和闭源商用 CMP 比 2020 年的方法；"physical synthesis 聚类"是所有方法都跑的标准预处理，她们的方法只放 macros、不放 standard cells。
- 面试用法：评估方法学之争（预训练/算力/harness 条件如何改变结论）与 Owen 的 MythTriage gate 评估、TB Terminus-2 口径是同一主题；提及时用"生产验证 > 基准复现"的框架，别站队攻击批评者（Kahng 是 EDA 界元老）。
- 补（2026-11-09 逐帧转录 Sequoia AI Ascent 视频）：STA 图表精确数据 — 设计 MEMPOOL_TILE_WRAP（ICCAD '25 Benchmark，657,934 pins / 904,348 arcs / 38,284 endpoints），R = 0.999720，RMSE = 8.63 ps，runtime = 183.7 ms。另：LLM-driven PPA 图 — HPWL reduction 4.6%，在 ~0.8M tokens 后进入平台期；AlphaChip 线长缩减：TPU v5e 3.2%、v5p 4.5%、Trillium 6.2%。整套 13 页 slides 转录存于 ~/workspace/your_files/ricursive-sources/sequoia-ai-ascent-slides-reconstructed.md。注意：视频版团队页为 10 人头像、标题 "Many thanks to the amazing Ricursive team!"，与 Owen 截图版（"Our Team"，16 人，页码 13）是不同版本。
