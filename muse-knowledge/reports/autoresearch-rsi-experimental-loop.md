# AutoResearch 与递归自我改进（RSI）实验循环：文献调研分析报告

> **研究时间**：2026-11-22（用户时区 Asia/Shanghai，UTC+8）｜**报告语言**：中文，来源以英文一手论文/官方博客/GitHub 为主
> **验证等级说明**：`verified live` = 本次通过浏览器直接打开 arXiv HTML 全文核对；`index` = 通过检索结果（摘要/HTML 片段）核对，未逐页打开全文；`secondary` = 二手综述/博客转述，仅作提示，不作为关键证据。
> **核心结论先行**：2023–2026 年的共识是——**可验证的实验循环已经成立，但真正的递归自我改进（RSI）尚未成立**。最强的系统（AI Scientist-v2、AlphaEvolve、DGM）都在“代码/流程/记忆”层自我改进，且依赖冻结的基座模型与外部可验证评估器；一旦评估器是 LLM 自评、指标可被操纵或缺少 held-out 复核，循环就会退化为“自我肯定”、幻觉放大与选择性报告。设计 RSI 实验循环时，应把“可验证性”放在“自主性”之前。

---

## Summary（执行摘要）

1. **RSI 的定义须分层**：弱自我改进（调参、修代码、改提示）≠ 强 RSI（每一代都更擅长产生下一代改进）。Yampolskiy（2015）与 Schmidhuber 的 Gödel Machine（2003/2007）是理论锚点；STOP 论文明确承认“模型权重未变，故不是完全 RSI”；DGM、Gödel Agent 是当前最接近的实证形态——改进对象是智能体自身的代码/工具/工作流，且改进能力本身可被下一轮复用。
2. **实验循环已有收敛形态**：`假设 → 设计 → 代码/执行 → 观察 → 分析/反思 → 修订假设或代码 → 迭代`，在不同系统中只是粒度与搜索拓扑不同：线性链（AI Scientist v1、Agent Laboratory）、树搜索（AIDE、AI Scientist-v2）、种群/档案进化（FunSearch、AlphaEvolve、DGM、Co-Scientist 的锦标赛）。
3. **反馈信号决定循环质量**：最可靠的是**执行器反馈**（编译/运行错误、单元测试、标量指标、held-out 分数）；其次是**外部评审**（同行评审、人类专家、独立复现）；最不可靠的是**同模型自评**（LLM judge 易被 Goodhart 化）。多篇独立评估显示：ScienceAgentBench 最优仅 32.4%、PaperBench 最优 21.0%、MLGym 前沿模型“只会调参，不会提新假设”。
4. **推荐蓝图**：采用“三层循环 + 三类记忆 + 四道门禁”——内层 debug 循环（分钟级）、中层实验循环（小时级，树/档案搜索）、外层元循环（天/周级，改进改进器本身）；记忆分轨迹、反思、技能/档案三类；门禁为预注册、held-out、复现、人工抽查。先在有廉价可验证评估器的领域（ML 代码、算法、数据分析）落地，再扩展到开放科学问题。

---

## 1. RSI 与 AutoResearch：定义与实验循环

### 1.1 术语辨析（必须先分清，否则会把“会反思”等同于“会自我改进”）

| 概念 | 定义 | 关键判据 | 代表 |
|---|---|---|---|
| **自我修改 Self-modification** | 改自己的代码/结构，不一定变好 | 改了 | 多态病毒等（Yampolskiy 2015 的最低层） |
| **弱自我改进 Weak self-improvement** | 优化参数/组件，但收益递减 | 指标上升，但改进能力不变 | 超参搜索、AutoML、单轮 Self-Refine |
| **递归自我改进 RSI / 强自我改进** | 每次改进都提升“进一步改进的能力”，即 `d(改进)/d(努力)` 随时间上升，可能加速 | 下一代改进器比上一代更强 | 理论：Gödel Machine；实证最接近：DGM、Gödel Agent |
| **自进化/自改进智能体 Self-evolving agent** | LLM 智能体在运行中改自己的脚手架、工具、提示或技能库，并用经验验证 | 经验可复用、跨任务迁移 | Voyager（技能库）、DGM（档案）、SEAL（权重） |
| **开放式进化 Open-ended evolution** | 不以单一分数为终点，保留“有趣但次优”的垫脚石（stepping stones），维持多样性 | 档案/种群持续分化 | DGM、AlphaEvolve、FunSearch、AI Scientist 的档案思想 |

**理论源头（verified via index）：**

- **I. J. Good（1965）“智能爆炸”**：超智能机器能设计更优机器，机器设计本身是智力活动，故产生智能爆炸；第一台超智能机器是人类需要制造的最后一项发明。〔Yampolskiy 2015 综述转述，arXiv:1502.06512〕
- **Schmidhuber Gödel Machine（2003，修订 2006/2007），arXiv:cs/0309048**：通用问题求解器，只有当它**证明**某次自我改写能提升期望效用时才改写自身任意部分（包括证明搜索器本身），理论上全局最优。实践障碍：绝大多数真实改写无法被证明有益，故从未有完整实现——它是“理论北极星”，而非工程方案。
- **Yampolskiy（2015），《From Seed AI to Technological Singularity via Recursively Self-Improving Software》，arXiv:1502.06512**：给出上述三层分类与 RSI 收敛理论，讨论计算极限与安全含义。形式化直觉：`I_{t+1} = f(I_t, E_t)`，真 RSI 要求改进对努力的导数随时间增大（加速回报）。
- **STOP（Zelikman et al., COLM 2024，arXiv:2310.02304）的自我限定**：作者明确写道“由于语言模型本身未被改变，这不是完全的递归自我改进”，但证明了 GPT-4 能写出“调用自身来改进自身”的代码，并会自发提出束搜索、遗传算法、模拟退火等策略；同时评估了生成代码绕过沙箱的频率——这是 RSI 安全讨论最早的实证注脚之一。

> **中文语境提示**：中文文献常把 recursive self-improvement、self-evolving、self-improving 混译为“递归自我改进/自我进化”。本报告坚持：只有改进对象包含“改进机制本身”且可度量地提升下一轮改进效率时，才称 RSI；其余称“循环内自我改进（in-loop self-improvement）”或“自适应（self-adaptation）”。

### 1.2 标准实验循环（canonical loop）

综合各系统，循环可抽象为 7 步（每步都有明确的产物与失败出口）：

```
[0. 目标/约束] 研究目标 + 可验证指标 + 预算/安全边界（预注册）
        ↓
[1. 假设 Hypothesis]  提出可证伪陈述：干预 X → 指标 Y 变化 Δ，机制 M，预测 P
        ↓
[2. 设计 Design]  实验设计：基线、对照/消融、数据集划分、随机种子、统计量
        ↓
[3. 代码/执行 Code & Execute]  生成代码 → 沙箱执行 → 记录日志/产物/环境指纹
        ↓
[4. 观察 Observe]  结构化观察：是否运行？指标？曲线？图？错误栈？
        ↓
[5. 分析/反思 Analyze & Reflect]  与预测对照：证实/证伪/无法判断；归因（代码错 vs 假设错 vs 评估错）
        ↓
[6. 修订 Revise]  三选一：修代码（同假设）、修假设（新分支）、修评估（发现评估漏洞时，需人工门禁）
        ↓
  ↺ 回到 [1] 或 [2]，直至停止准则（见 §3.7）
```

**关键设计选择不在“有没有循环”，而在：**

- **循环粒度**：单条输出内反思（Self-Refine）、单任务多轮试错（Reflexion）、多实验分支（AIDE/树）、多种群世代（AlphaEvolve/DGM）、多智能体辩论（Co-Scientist）。
- **状态载体**：上下文记忆（Reflexion）、显式树节点（AIDE/v2）、档案/种群（DGM/AlphaEvolve）、技能库（Voyager）、权重（STaR/SEAL）。
- **搜索策略**：贪心爬坡（易陷局部最优）、最佳优先树搜索（v2）、质量-多样性（MAP-Elites，AlphaEvolve）、锦标赛淘汰（Co-Scientist）、开放式“有趣性”采样（DGM）。

### 1.3 自我改进的四个层级（self-modification scope）

| 层级 | 改什么 | 是否改变“改进能力” | 成本/风险 |
|---|---|---|---|
| L1 提示/上下文 | 提示词、反思文本、few-shot | 否（仅当次） | 低；易遗忘 |
| L2 代码/工具/工作流 | 实验代码、工具函数、智能体流程、评审逻辑 | **是，若工具被复用** | 中；需沙箱+回归测试 |
| L3 记忆/技能/档案 | 技能库、经验回放、档案种群 | 是（复利） | 中；需去重/版本管理 |
| L4 模型权重 | 微调/RL（STaR、SEAL、ReST） | 是（持久） | 高；灾难性遗忘、分布漂移 |

DGM 的关键洞见是：**L2+L3 的组合可以在冻结基座模型的前提下逼近 RSI**——因为更好的编码智能体本身就是更好的“自我修改器”，下游基准（SWE-bench）的提升可直接转化为自我改进能力的提升。SEAL 则走 L4 路线：让模型学会生成“自我编辑（self-edit）”，以外层 RL 奖励内层更新后的下游表现。

---

## 2. 关键论文/系统综述 2023–2026

> 按“从基础机制 → 单智能体循环 → 多智能体/进化 → 端到端系统 → 基准/评估”的逻辑排序，而非单纯按时间。每个条目给出：机构、时间、核心机制、实证结果、局限。未标注 `verified live` 的均为 `index` 核对。

### 2.1 基础机制：ReAct / Self-Refine / Reflexion / STaR（2022–2023，循环的“原子”）

| 系统 | 作者/机构/年份/出处 | 机制 | 结果 |
|---|---|---|---|
| **ReAct** | Yao et al., Princeton/Google，arXiv:2210.03629，ICLR 2023 | Thought–Action–Observation 交错：推理产生行动，工具返回观察再修正推理；用行动 grounding 减少幻觉 | 几乎所有现代智能体的默认循环骨架；是 Reflexion/Voyager/OpenHands 的 Actor 层 |
| **Self-Refine** | Madaan et al., CMU/AI2，arXiv:2303.17651，NeurIPS 2023 | 同一 LLM 担任生成器+反馈者+精炼者：生成→多方面自然语言反馈→精炼，循环至收敛或最多 4 轮；无需训练数据/奖励模型 | 7 项任务平均提升约 20 个百分点；大部分收益在第 1→2 轮，之后边际递减；数学推理几乎无收益（自我反馈会误判）；弱模型（Vicuna-13B）无法可靠执行 |
| **Reflexion** | Shinn et al., Northeastern/MIT/Princeton，arXiv:2303.11366，NeurIPS 2023 | Actor–Evaluator–Self-Reflection 三模块；将二值/标量奖励转为自然语言反思，存入情景记忆（通常只保留最近 1–3 条），下一轮以此为条件；称“言语强化学习（verbal RL）”，无权重更新 | ALFWorld 130/134（+22pp vs ReAct），HotpotQA +20pp，HumanEval 91% pass@1（GPT-4 基线 80%）；编程任务先自写单元测试再反思 |
| **STaR** | Zelikman et al., Stanford/Google，arXiv:2203.14465，NeurIPS 2022 | 生成推理链→保留导致正确答案的链→对失败题用正确答案作提示“合理化（rationalization）”再生成链→微调→重复 | 在 CommonsenseQA 上可比肩微调 30 倍大模型的效果；是“用自己的成功轨迹训练自己”的权重层 RSI 原型；风险是只学“对的答案的推理”，可能固化捷径 |

**对 RSI 循环的意义**：这四者定义了反馈的三种形态——**环境信号**（ReAct 的观察）、**言语信号**（Self-Refine/Reflexion 的文本批评）、**权重信号**（STaR 的微调）。一个成熟的 RSI 循环需要三者并存，且必须区分“可验证信号”（测试通过/指标）与“不可验证信号”（自我感觉良好）。

### 2.2 Voyager：技能库即长期记忆（2023）

- **出处**：Wang et al., NVIDIA/Caltech/UT Austin/Stanford，arXiv:2305.16291（2023-05-25，v2 2023-10-19），TMLR 2024。官网 https://voyager.minedojo.org
- **架构**：① 自动课程（automatic curriculum）：以新颖性与可行难度为目标提任务；② 技能库（skill library）：把已验证的可执行代码（Mineflayer JavaScript）按自然语言描述的向量索引存入数据库，支持组合复用；③ 迭代提示：环境反馈+执行错误+自我验证（第二个 GPT-4 作 critic）驱动代码修正。
- **结果**：比此前 SOTA 多获得 3.3× 独特物品、行进 2.3× 距离、技术树里程碑快达 15.3×；技能可迁移到新世界从零解新任务；**全程无微调、无权重更新**（黑盒 GPT-4 查询）。
- **启示**：把能力存在**可检索、可组合、可验证的代码**里，而非权重里，是缓解灾难性遗忘、实现跨任务复利的最廉价路径。这直接影响了后来的 DGM 档案、Claude Code 的 SKILL.md 等设计。局限：技能验证依赖环境可判定成功与否；在开放科学中“成功”本身难以定义。

### 2.3 FunSearch → AlphaEvolve：进化式代码搜索（DeepMind，2023–2025）

**FunSearch（2023，Nature）**

- Romera-Paredes et al., Google DeepMind，*Nature* 625:468–475 (2024)，doi:10.1038/s41586-023-06924-6；arXiv:2312.15571。
- 机制：冻结 LLM（Codey/PaLM 2）提候选程序 + 系统评估器打分 + 进化选择；搜索的是“**生成解的程序**”而非解本身；只适用于有高效 `evaluate()` 的问题。
- 结果：cap set 问题取得 20 年来最大渐近下界改进；在线装箱发现优于常用基线的新启发式。

**AlphaEvolve（2025，DeepMind 白皮书）**

- Novikov et al., Google DeepMind，arXiv:2506.13131（2025-06-16）；官方博客 2025-05-14：https://deepmind.google/blog/alphaevolve-a-gemini-powered-coding-agent-for-designing-advanced-algorithms/
- **架构**：Gemini Flash（广度、高通量）+ Gemini Pro（深度）双模型集成；进化数据库（实现 MAP-Elites 式质量-多样性选择）决定哪些程序进入下一轮提示；自动化评估器验证、运行、打分；可进化**整个代码库**（数百行、多语言），支持多目标、长时并行评估。是 FunSearch 的全面升级。
- **结果（DeepMind 报告，index）**：4×4 复矩阵乘法 48 次标量乘法（Strassen 为 49，56 年来首次改进）；在 50+ 开放数学问题中约 20% 取得 SOTA 或新最优；Google 数据中心 Borg 调度回收 0.7% 算力；Gemini 训练关键核加速 23%（整体训练时间 -1%）；FlashAttention 核最高加速 32.5%；TPU 电路 Verilog 简化已部署。
- **可信度注记**：数学产物（48 乘法分解）是可公开验证的；生产收益为 Google 单方报告。后续有学者指出 2024 年已有数值型 rank-48 方案，AlphaEvolve 的贡献是**精确可验证构造**——这恰恰说明“可验证产物”比“宣称的分数”更重要。

### 2.4 Google AI Co-Scientist：锦标赛式假设进化（2025–2026，Nature）

- Gottweis et al., Google Cloud AI Research / DeepMind / Stanford 等，arXiv:2502.18864；*Nature* 2026，doi:10.1038/s41586-026-10644-y。
- **定位**：明确的“科学家在环（scientist-in-the-loop）”协作系统，而非全自主。目标是结构化科学思维，而非端到端出论文。
- **架构**：Supervisor 解析自然语言目标并动态分配异步任务队列；专业智能体分工：Generation（生成）、Reflection（反思）、Ranking（排序）、Evolution（进化）、Proximity（相关性）、Meta-review（元评审）；共享 Context Memory；**锦标赛（tournament）进化**：假设两两辩论、排序、变异，优胜者进入下一代；用测试时算力扩展（scaling test-time compute）换取假设质量；工具（网络检索、专业模型）用于 grounding 与引用。
- **验证**：三项生物医学任务均经独立体外实验验证：急性髓系白血病（AML）药物重定位与联合用药、肝纤维化新表观遗传靶点、抗菌素耐药（AMR）细菌基因转移机制（与同期未发表发现独立复现）。
- **启示**：在假设空间巨大、评估昂贵的领域，用“辩论+锦标赛+人类守门”代替“单链自评”，是目前最可信的假设生成范式；代价是高度依赖人类实验验证闭环。

### 2.5 The AI Scientist v1 → v2 → Nature（Sakana AI，2024–2026）

**v1（2024）**

- Lu et al., Sakana AI / UBC / Oxford，arXiv:2408.06292（2024-08-12）。代码 https://github.com/SakanaAI/AI-Scientist
- 流程：给定研究方向+初始代码模板→生成想法（按有趣性/新颖性/可行性打分，Semantic Scholar 查新）→用 Aider 写代码、运行、记实验日志→分段写 LaTeX 论文（逐节去重/去矛盾）→LLM 模拟同行评审打分；原则上可开环累积档案。
- 宣称：每篇论文成本 <$15；在扩散模型、Transformer LM、学习动力学三领域验证；模拟评审可超顶会接受阈值。
- **独立评估（Beel et al., arXiv:2502.14297，index）**：文献综述仅关键词检索、新颖性判断弱；12 个实验中 5 个（42%）因编码错误失败，跑通的也常有逻辑缺陷或误导性结果；成本 $6–15、人类介入约 3.5 小时。结论：里程碑式，但远非“可直接信任的研究员”。

**v2（2025）——本次 `verified live` 核对 arXiv HTML 全文**

- Yamada et al., Sakana AI，arXiv:2504.08066（2025-04-10）。代码 https://github.com/SakanaAI/AI-Scientist-v2
- **三大改进**（HTML §1–§3，已核对）：① 去模板化：从高层抽象（摘要/基金申请式）出发提想法，构思中即调用 Semantic Scholar 查新；② **实验经理智能体 + 渐进式智能体树搜索**：四阶段——初步验证（最小原型跑通即止）→超参调优（曲线收敛且至少两个数据集跑通即止）→研究议程执行（预算耗尽即止）→消融研究；每阶段结束用专用 LLM 评估器选最优节点作为下一阶段种子，并做多次重复取均值/标准差；③ **VLM 反馈**：实验阶段审图（标签、图例、误导性可视化→标为 buggy），成文阶段审图+题注+正文引用一致性。
- **树节点结构**（HTML §3.2.2）：每个节点 = 实验脚本 + 高层计划文本 + 错误栈 + 运行时间 + 指标 + LLM 运行反馈 + 可视化脚本 + 图路径 + VLM 反馈 + 状态（buggy / non-buggy）。扩展规则：以一定概率优先选 buggy 节点调试，否则按最佳优先（best-first）选 non-buggy 节点精炼；并行扩展；专用节点类型：超参节点（防重复记录）、消融节点、复现节点（多种子）、聚合节点（汇总均值±标准差出图）。
- **评审实验**：向 ICLR 2025 ICBINB 工作坊提交 3 篇全自主论文，1 篇得分 6/7/6（均值 6.33，约前 45%），按元评审意见本可接受；其余 2 篇未达标；按事先协议，接受后亦撤稿。作者自评：**无一篇达到顶会主会标准**；存在引用幻觉、方法描述不清、图注不准确等问题。
- **Nature 版（2026）**：Yamada et al.,《Towards End-to-End Automation of AI Research》，*Nature* 651:914–919 (2026)，doi:10.1038/s41586-026-10265-5（2026-03-25 在线）；arXiv:2606.15497。工作坊接受率 70%，ICLR 2025 主会 32%；作者承认“在每个阶段都人工筛选了最有前景的输出”。〔index 核对 PubMed/ Nature 摘要〕
- **关于 v3 的注记**：有个别二手文章提到 2026 年 10 月的“The Agentic Scientist（v3）”，但本次未在 arXiv/ Sakana 官网一级来源中核实，**不列入正式结论**，仅作待核查线索。

### 2.6 Agent Laboratory 与 ResearchAgent：人类在环 vs 文献驱动

**Agent Laboratory（2025）**

- Schmidgall et al., AMD / Johns Hopkins / ETH Zürich，arXiv:2501.04227（2025-01-08，v2 2025-06-17），EMNLP 2025 Findings。官网 https://AgentLaboratory.github.io
- **流程**：输入人类研究想法→三阶段：文献综述→实验→报告写作；角色化智能体（PhD/Postdoc 等）分工；`mle-solver` 以 REPLACE/EDIT 操作迭代生成-测试-精炼 ML 代码（LLM 奖励模型引导），`paper-solver` 生成 LaTeX 支架并迭代精炼；支持自主模式与副驾驶模式（用户可在每阶段给反馈）。
- **结果**：o1-preview 驱动效果最好（有用性 4.4/5）；人类每阶段反馈显著提升质量；成本比此前自主研究方法降低 84%；生成代码可达 SOTA 水平（作者报告）。
- **启示**：与 AI Scientist 的“自主提想法”不同，Agent Lab 锚定**人类想法**，把自主性放在执行层——在可信度与成本之间取得更好平衡，是工程落地的务实折中。

**ResearchAgent（2024，NAACL 2025）**

- Baek et al., KAIST / Microsoft Research，arXiv:2404.07738（2024-04-11，v2 2025-02-09）。
- **机制**：从一篇核心论文出发，用学术引用图扩展相关论文，再用实体中心知识库（跨论文挖掘共享概念）补充；生成“问题–方法–实验设计”三元组；多个 LLM ReviewingAgents 按从人类判断中诱导出的标准（新颖性、清晰度、有效性等）迭代评审与修订。
- **启示**：假设生成的质量高度依赖**结构化知识 grounding**（图+实体），而非自由联想；评审者角色分离（生成者不自评）是抑制自我肯定的关键设计。

### 2.7 AIDE / CodeAct / OpenHands：代码即行动、树即记忆（2024–2025）

- **CodeAct**（Wang et al., UIUC 等，arXiv:2402.01030，ICML 2024）：把 LLM 的行动空间统一为**可执行 Python 代码**（而非 JSON/文本工具调用），解释器执行后返回 stdout/异常作为下一轮观察，天然支持循环、组合、异常处理与自调试。在 17 个 LLM 上比 JSON/文本最高提升 20% 成功率；并发布 CodeActInstruct（7k 多轮轨迹）与 CodeActAgent。
- **OpenHands**（原 OpenDevin；Wang et al., arXiv:2407.16741，2024-07）：事件流（event stream）架构——所有行动与观察按时间顺序记入日志即状态；Docker 沙箱运行时（bash + IPython + 浏览器）；AgentSkills 工具库；多智能体委派（AgentDelegateAction）；内置 15 项基准评估。代码 https://github.com/All-Hands-AI/OpenHands 。它是 AI Scientist 类系统最常复用的执行底座。
- **AIDE**（Jiang et al., Weco AI，arXiv:2502.13138，2025-02）：把 ML 工程形式化为**解空间中的树搜索**：节点是完整可执行解，边是调试/改进；交替起草（draft）、修复（debug）、精炼（improve）；硬编码树搜索累积增量改进，由自动化评估引导；与 ReAct 式“把历史全塞进上下文”不同，AIDE 按节点提改进建议，避免上下文爆炸。结果：MLE-bench Lite 上 o1-preview+AIDE 有效提交率 92.4%（无脚手架 63.6%）、奖牌率 36.4%（vs 7.6%）；RE-Bench 上在有限时间内可超人类专家。代码 https://github.com/WecoAI/aideml

### 2.8 RSI 专论：DGM、Gödel Agent、STOP、SEAL（2023–2025）

**Darwin Gödel Machine（DGM，2025）——本次 `verified live` 核对 arXiv HTML**

- Zhang, Hu, Lu, Lange, Clune（UBC / Vector Institute / Sakana AI），arXiv:2505.22954（2025-05-29）。代码 https://github.com/jennyzzt/dgm
- **核心循环（HTML §1、§3 已核对）**：维护一个**编码智能体档案**（初始仅 1 个），交替进行自修改阶段与评估阶段——从档案中采样智能体→由基础模型生成“新的、有趣的”修改版→在编码基准上评估→加回档案。开放式探索形成不断生长的树，允许多路径并行探索；保留次优但有趣的垫脚石，避免贪心收敛。
- **结果**：80 次迭代后 SWE-bench 20.0%→50.0%，Polyglot 14.2%→30.7%；消融证明自改进与开放式探索二者缺一不可；进化出的改进可迁移：SWE-bench 上进化的智能体在 Polyglot 上 28.9%（基线 14.2%），跨模型迁移到 Claude 3.7 Sonnet 达 59.5%（基线 19.0%）。自动发现的改进包括细粒度 `str_replace` 编辑、按行查看（`view_range`）以管理长上下文、同行评审机制等。
- **作者的自我限定**：冻结基础模型，只改进编码智能体设计（工具、工作流）；未展示重训基础模型（算力与复杂性原因，列为未来工作）；实验均在沙箱+人类监督下进行，强调谱系可追溯。
- **为什么 DGM 是目前最接近 RSI 的系统**：下游任务（写代码）与自我改进任务（改写自己的代码库）是同一能力，基准提升≈自我改进能力提升，形成正反馈；且改进逻辑本身也在被改写范围内。

**Gödel Agent（2024，ACL 2025）**

- Yin et al., 北京大学 / UCSB，arXiv:2410.04444（2024-10-06，v4 2025-05-31）。代码 https://github.com/Arvid-pku/Godel_Agent
- 机制：受 Gödel Machine 启发，但用 LLM 动态修改自身逻辑与行为，仅由高层目标提示引导，无预定义流程或固定优化算法；通过操作运行时内存、猴子补丁（monkey patching）实现自我感知与自我修改；主函数实现为递归函数，LLM 在每一递归深度决定读/改自身代码、执行命令、与环境交互。作者称其可搜索“完整智能体设计空间”。
- 结果：数学推理与复杂智能体任务上持续自我改进，超越人工设计的智能体（性能、效率、泛化性）。**局限**：评估任务与自我改进能力未必对齐（DGM 论文明确指出此点）；猴子补丁式自改在工程上脆弱、可审计性差。

**STOP（2023，COLM 2024）**：见 §1.1。种子改进器（多次查询 LLM 取最优）→用它改进自身→下游任务显著提升；LLM 自发提出束搜索/遗传算法/模拟退火。作者明确非完全 RSI，并评估沙箱绕过率。

**SEAL（2025，NeurIPS 2025）——权重层 RSI 的最新代表**

- Zweiger et al., MIT，arXiv:2506.10943（2025-06-12，v2 2025-09-18）。网站 https://jyopari.github.io/posts/seal
- 机制：双层循环——外层 RL 训练模型生成有效的“自我编辑（self-edit）”（可重组信息、指定超参、调用工具做数据增强），内层用该 self-edit 做 SFT/LoRA 权重更新；奖励 = 更新后模型在下游任务上的表现；用 ReST_EM（拒绝采样+ SFT）做 RL。
- 结果：知识融入任务上，无上下文 SQuAD 问答 33.5%→47.0%，自生成合成数据优于 GPT-4.1 生成的数据；少样本泛化亦有提升。
- **意义与风险**：首次把“学会如何更新自己”本身作为可学习目标；但权重更新的不可逆性与遗忘风险远高于代码层，需严格的回归测试与版本回滚。

### 2.9 其他值得收录的系统（简表）

| 系统 | 出处 | 一句话定位 |
|---|---|---|
| **SWE-agent** | Yang et al., Princeton，arXiv:2405.15793，2024 | 为智能体定制 ACI（Agent-Computer Interface），在 SWE-bench 上早期达 12.5% pass@1；证明“接口设计”本身是可优化变量 |
| **MLR-Bench / MLRC-Bench** | 2025（二手提及 arXiv:2505.19955） | 开放式 ML 智能体运行中约 80% 产生捏造或被证伪的结果（secondary，需核实原文）——作为失败率警示引用，不作精确数字断言 |
| **SciAgents / MOOSE** | Ghafarollahi et al. 2024；Yang et al. 2023 | 知识图谱随机游走强制跨域重组；过去/现在/未来三维反馈调控新颖-有效前沿 |
| **ADAS（Automated Design of Agentic Systems）** | Hu et al., 2025 | 元智能体迭代生成下游智能体并用反馈精炼；但元智能体本身固定——DGM 明确以此为对照，强调“元层亦可被改” |
| **AutoResearchEval（ARFT）** | Fei et al., arXiv:2608.14905，2026-08（secondary） | 100 个真实前沿任务、800 条轨迹的诊断评估：最常见失败不是“没发现缺陷”，而是“发现了仍照常发表”（660/800=82.5%）；诊断→修订的环路从未闭合。此为二手转述，原文需进一步核实 |

---

## 3. 各方法机制拆解（按任务 Q3 的维度）

### 3.1 架构：三种主流拓扑

1. **线性管道（Pipeline）**：AI Scientist v1、Agent Laboratory。优点是可解释、易调试、成本低；缺点是单点失败即全链失败、无法回溯分支、易陷入“短视实验”（v2 论文原话）。
2. **树搜索（Tree search）**：AIDE、AI Scientist-v2。节点=完整实验状态，边=调试/精炼；最佳优先+并行扩展；天然支持回溯与多假设并行。代价是节点数与评估成本指数增长，需剪枝与去重（v2 用“已测超参/消融记录防重复”）。
3. **档案/种群进化（Archive / Population）**：FunSearch、AlphaEvolve、DGM、Co-Scientist 锦标赛。维护多样性（MAP-Elites、质量-多样性、开放式有趣性），避免早熟收敛；适合评估廉价、可并行的场景。代价是需要好的多样性度量，否则退化为多路爬坡。

**选型建议**：评估廉价（秒–分钟级）→ 种群进化；评估昂贵（小时级训练）→ 树搜索+最佳优先+少量并行；评估不可靠（需人类判断）→ 锦标赛+人类守门。

### 3.2 反馈信号：五类，按可信度排序

| # | 反馈类型 | 来源 | 可信度 | 典型系统 |
|---|---|---|---|---|
| 1 | **执行反馈**：编译/运行错误、异常栈、超时 | 解释器/沙箱 | ★★★★★（客观） | 全部系统；Voyager 的迭代提示、AIDE 的 debug 节点 |
| 2 | **标量指标**：验证集分数、测试通过率、竞赛指标 | 自动化评估器 | ★★★★☆（若 held-out 且防泄漏） | AIDE、AlphaEvolve、MLE-bench |
| 3 | **结构化评审**：同行评审、专家打分、复现检查 | 人类/独立智能体 | ★★★★☆（贵、慢，但最接近科学标准） | AI Scientist 的模拟评审→真人评审；Agent Lab 的人类反馈 |
| 4 | **LLM Judge / VLM 反馈**：图质量、文本一致性、假设评分 | 同/异模型 | ★★★☆☆（易 Goodhart、自我肯定） | v2 的 VLM 审图、PaperBench 的 o3-mini-high 评审（F1 0.83） |
| 5 | **言语反思**：自我批评、经验教训 | 自身 | ★★☆☆☆（仅当与 1–2 绑定时有效） | Reflexion、Self-Refine |

**关键教训**：

- **反馈必须绑定到可验证锚点**。Reflexion 在编程任务中先自写单元测试，再用测试失败驱动反思——这比纯文本反思可靠得多。
- **生成者与评审者必须分离**。ResearchAgent 用独立 ReviewingAgents、Co-Scientist 用辩论+锦标赛、DGM 用外部基准，都是为了避免“自己给自己打高分”。
- **警惕 Judge Goodhart**：二手综述中提到，用 LLM 评审优化创意会使评审分上升（8.2→8.9）而真实精度崩塌（87%→28%）——该数字来自非一手来源，此处仅作风险提示，不作定量断言；但方向与 PaperBench“LLM 评审 F1 仅 0.83”、PNAS 2026 的“手稿评审无法发现方法学缺陷”一致。

### 3.3 记忆 / 经验回放

| 记忆类型 | 载体 | 写入规则 | 读取规则 | 代表 |
|---|---|---|---|---|
| **轨迹记忆** | 上下文/事件流 | 全量追加 | 滑动窗口/摘要 | OpenHands 事件流、ReAct 轨迹 |
| **反思记忆** | 自然语言教训 | 失败后生成 1 条 | 最近 1–3 条注入提示 | Reflexion（有界） |
| **技能/档案记忆** | 向量库/种群/树 | 仅已验证产物入库 | 相似度检索 / 最佳优先采样 | Voyager 技能库、DGM 档案、AlphaEvolve 数据库 |
| **权重记忆** | 微调参数 | 成功轨迹微调 | 隐式（模型内） | STaR、SEAL |

**设计原则**：① **只把“已验证”的写进长期记忆**（Voyager 只存通过自验证的技能；DGM 只把评估过的智能体加回档案）；② **记忆要有遗忘/去重机制**（Reflexion 的有界缓冲、v2 的防重复记录、AlphaEvolve 的多样性维持）；③ **记忆要可审计**（每条记忆附来源实验 ID、指标、环境指纹，否则无法追溯错误来源）。

### 3.4 假设生成与剪枝

- **生成策略**：自由联想（v1）、高层抽象+文献 grounding（v2）、图随机游走强制跨域（ResearchAgent/SciAgents）、多智能体辩论（Co-Scientist）、进化变异（AlphaEvolve/DGM）。
- **评分维度**：新颖性（Semantic Scholar 查新/嵌入距离）、有趣性、可行性、清晰度、有效性。v1 用三维打分+迭代变异高分想法；ResearchAgent 用人类对齐的多维评审。
- **剪枝策略**：阈值过滤（低分直接弃）、锦标赛淘汰（Co-Scientist）、最佳优先扩展（v2/AIDE）、多样性保留（DGM 的“有趣但次优”亦保留）。**切忌只按单一分数贪心剪枝**——那会杀死垫脚石，导致早熟收敛。
- **常见失败**：LLM 倾向保守、增量式想法（Co-Scientist 的 Facebook 讨论与多篇评述均指出）；难以区分“激进洞见”与“错误假设”；文献检索若仅关键词匹配则新颖性判断失真（Beel 对 v1 的批评）。

### 3.5 实验规划

- **阶段化**：v2 的四阶段（原型→调优→议程→消融）是最完整的公开模板；每阶段有明确的停止准则与产物（原型跑通、曲线收敛、预算耗尽）。
- **对照设计**：基线、消融、多种子复现（v2 的复现/聚合节点）、held-out 划分。**没有对照的“改进”不可信**——PNAS 2026 指出，不当基准选择与事后选择偏差是 AI Scientist 类系统最隐蔽的缺陷。
- **预算分配**：并行扩展（v2/AIDE）vs 序贯深度（Reflexion）。经验法则：早期广度（多分支小实验）→中期深度（最优分支加大投入）→后期复现（统计稳健性）。

### 3.6 代码生成与调试循环

- **行动空间**：CodeAct（可执行 Python）已成为事实标准——比 JSON 工具调用更易组合、循环、处理异常，且调试信息直接来自解释器。
- **调试循环**：生成→执行→错误栈→定位→最小改动（DGM 发现的 `str_replace` 细粒度编辑优于整文件重写）→重跑→回归测试。v2/AIDE 把“buggy 节点”显式建模，并以一定概率优先调试，避免“只精炼好节点、烂节点被遗忘”。
- **沙箱**：OpenHands 的 Docker 沙箱、DGM/STOP 的沙箱+人类监督是底线。STOP 论文专门评估了沙箱绕过频率；AI Scientist-v2 的 README 亦警告 LLM 生成代码可能安装危险包、访问网络、生成非预期进程，**必须在受控沙箱中运行**。
- **可复现性**：固定随机种子、记录环境（依赖版本、GPU 型号）、保存完整实验脚本与数据指纹。v2 每阶段做多次重复并报告均值±标准差，是最低限度的统计实践。

### 3.7 评估与停止准则

| 停止信号 | 类型 | 示例阈值（需按领域校准） |
|---|---|---|
| 任务完成 | 目标达成 | 指标 ≥ 预注册目标且 held-out 复现 |
| 收敛 | 边际收益递减 | 连续 k 轮最佳分数提升 < ε（如 Self-Refine 的 1→2 轮后递减） |
| 预算耗尽 | 成本/时间 | Token/美元/小时上限；v2 的 Stage 3/4 即预算耗尽即止 |
| 失败上限 | 调试轮数 | 单节点最多 N 次调试（如 3–5 次），超限则剪枝 |
| 证据不足 | 无法判断 | 连续多轮“无法判断”→回退到假设层，而非继续调参 |
| 人工叫停 | 安全/伦理 | 沙箱告警、数据泄漏嫌疑、伦理审查触发 |

**评估分层（建议）**：

1. **单元层**：代码能否运行、测试是否通过（自动、每次迭代）；
2. **实验层**：指标是否提升、是否通过 held-out、消融是否支持机制解释（自动+抽查）；
3. **研究层**：假设是否新颖、方法是否严谨、结论是否被数据支持（LLM 初筛+人类终审）；
4. **元层**：改进器本身是否变强（跨任务迁移、跨模型迁移，如 DGM 的跨基准测试）。

### 3.8 迭代如何带来改进（实证证据链）

- **短期**：调试循环修复错误（执行反馈）→指标上升（AIDE 的树搜索在 MLE-bench 上奖牌率 5 倍）。
- **中期**：技能/档案积累→复用与组合→新任务冷启动更快（Voyager 的技能迁移、DGM 的跨基准迁移）。
- **长期**：改进器本身被改进→下一轮搜索更高效（DGM 的 SWE-bench 20%→50% 且跨模型迁移；STOP 的改进器在下游任务上持续变好；SEAL 的 self-edit 生成策略经 RL 越来越有效）。
- **反例**：无记忆的单链（v1 线性）与无多样性的贪心（DGM 的无开放式消融基线）都会早期 plateau；无 held-out 的循环会“改进”评估漏洞而非真实能力。

---

## 4. 对比表（按任务 Q4）

### 表 1：主要系统对比——循环粒度、反馈、自修改范围、验证、成本、可复现性、失败模式

| 系统 | 循环粒度 | 反馈类型 | 自修改范围 | 验证方式 | 成本（报告值） | 可复现性 | 主要失败模式 |
|---|---|---|---|---|---|---|---|
| **Self-Refine** | 单输出，≤4 轮 | 自我文本反馈 | 提示（L1） | 人类/自动指标 | 低 | 高（提示固定） | 自我误判（数学）、边际递减 |
| **Reflexion** | 单任务，多试次（≤12） | 二值/标量+启发式+自写测试 | 记忆（L1/L3） | 环境成功信号 | 中 | 中（记忆有界） | 反思漂移、依赖评估器质量 |
| **STaR** | 数据集级，多轮微调 | 正确答案（有监督） | 权重（L4） | held-out 准确率 | 高（训练） | 中（需数据） | 固化捷径、遗忘 |
| **Voyager** | 任务级，终身 | 环境+错误+自验证 | 技能库（L3） | 环境成功判定 | 高（GPT-4 多轮） | 中（技能可导出） | 成功定义依赖环境 |
| **ReAct** | 步级（Thought-Action-Obs） | 工具观察 | 无（单轮） | 工具返回 | 低–中 | 高 | 幻觉、长程漂移 |
| **CodeAct / OpenHands** | 步级，代码为行动 | 解释器输出 | 代码（L2） | 测试/基准 | 中 | 高（事件流日志） | 沙箱逃逸、长上下文 |
| **AIDE** | 实验级，树搜索 | 标量分数（验证集） | 解代码（L2） | Kaggle/MLE/RE-Bench | 低–中（<$1.50/ Kaggle 任务报告值） | 高（树可导出） | 过拟合验证集、短视 |
| **AI Scientist v1** | 论文级，线性 | 执行+模拟评审 | 实验代码（L2） | 模拟评审 | <$15/篇 | 低（模板依赖） | 42% 编码失败、文献综述弱 |
| **AI Scientist v2** | 论文级，树+四阶段 | 执行+VLM+真人评审 | 实验代码（L2） | ICLR 工作坊真人评审 | 未公开单篇（v1 为 $6–15） | 中（开源+数据） | 引用幻觉、方法不清、仅 1/3 通过 |
| **Agent Laboratory** | 论文级，三阶段+人类反馈 | 执行+人类分阶段反馈 | 代码+报告（L2） | 人类调查评分 | 比此前方法 -84% | 中（开源） | 依赖人类质量、o1-preview 成本 |
| **ResearchAgent** | 想法级，多轮评审 | 多评审者文本反馈 | 想法文本（L1） | 人类+模型评估 | 中 | 中 | 文献图覆盖偏差 |
| **FunSearch** | 程序级，进化 | 评估器分数 | 程序（L2/L3） | 数学证明/评估器 | 高（大规模进化） | 高（程序可验证） | 仅适用于有高效评估器的问题 |
| **AlphaEvolve** | 代码库级，进化 | 多评估器分数 | 整个代码库（L2） | 评估器+生产验证 | 很高（Google 级） | 中（白皮书，未全开源） | 评估器设计决定上限 |
| **Co-Scientist** | 假设级，锦标赛 | 辩论+排序+人类实验 | 假设（L1/L3） | 独立体外实验 | 高（人类实验） | 中（系统未开源） | 想法保守、依赖人类验证 |
| **DGM** | 智能体级，档案进化 | 编码基准分数 | 自身代码/工具/流程（L2+L3，含改进逻辑） | SWE-bench/Polyglot + 跨迁移 | 高（80 迭代） | 高（开源+谱系） | 基准即能力假设、沙箱风险 |
| **Gödel Agent** | 递归深度级 | 任务分数 | 自身全部代码（L2，含元层） | 数学/编码/科学任务 | 中–高 | 低（猴子补丁脆弱） | 对齐漂移、可审计性差 |
| **STOP** | 改进器级，递归 | 下游效用函数 | 改进器代码（L2） | 下游任务 | 中 | 中 | 非完全 RSI、沙箱绕过 |
| **SEAL** | 更新级，双层 RL | 更新后下游表现 | 权重（L4）+自编辑策略 | held-out 问答 | 高（RL+微调） | 中 | 遗忘、不可逆更新 |

### 表 2：基准对比——它们在测什么、分数意味着什么

| 基准 | 机构/年份 | 规模 | 测什么 | 最优报告值（当时模型） | 人类基线 | 启示 |
|---|---|---|---|---|---|---|
| MLAgentBench | Stanford 2023 | 13 任务 | ML 实验迭代 | Claude 3 Opus 37.5% 成功 | — | 小样本、成功≠SOTA |
| ScienceAgentBench | OSU 等 2024 | 102 任务/44 论文 | 数据驱动科学代码生成 | 32.4%（无专家知识），34.3%（有），o1-preview 42.2% | 专家验证任务 | 最优仍不及 1/2；自调试比通用 CodeAct 便宜 17× 且更好 |
| MLE-bench | OpenAI 2024 | 75 Kaggle | ML 工程端到端 | o1-preview+AIDE 奖牌率 16.9% | Kaggle 排行榜 | 污染问题已被作者研究 |
| PaperBench | OpenAI 2025 | 20 论文/8,316 子任务 | 复现 SOTA 研究 | Claude 3.5 Sonnet 21.0% | PhD 41.4%（48h，3 篇） | 复现≠创新；LLM 评审 F1 0.83 |
| RE-Bench | METR 2024 | 7 环境 | 开放式研究工程 | 2h 约 4×人类，32h 人类约 2×智能体 | 61 专家，71×8h | 智能体赢在短时，输在长时 |
| MLGym-Bench | Meta 2025 | 13 开放任务 | AI 研究（Gym 环境） | 前沿模型仅调参改进 | — | “不会提新假设”是当前瓶颈 |
| SWE-bench / Polyglot | — / DGM 用 | — | 编码智能体 | DGM 50.0% / 30.7% | — | DGM 的 RSI 代理指标 |

> **数字使用警告**：上表分数均为各论文发表时的快照，随模型迭代迅速过时；跨基准、跨预算的分数不可直接比较。应关注“测量设计”（任务来源、评分方式、人类基线、污染控制），而非分数本身。

---

## 5. 推荐设计蓝图：RSI 实验循环（可直接用于工程立项）

### 5.1 设计原则（10 条）

1. **可验证性优先于自主性**：没有廉价可验证评估器的领域，不做全自主闭环；先做“人类在环的半自主”。
2. **生成者与评审者分离**：同一上下文不得自评；评审用独立模型/独立提示/独立数据。
3. **只把已验证的写进长期记忆**：技能、档案、反思均需附验证证据与来源实验 ID。
4. **多样性是资产**：保留次优垫脚石（档案/种群），不用单一分数贪心剪枝。
5. **三层循环、不同时间尺度**：debug（分钟）→实验（小时）→元改进（天/周），外层改内层的机制，而非仅改产物。
6. **预注册与 held-out**：指标、划分、基线在实验前锁定；任何事后改评估需人工门禁并留痕。
7. **失败是数据**：buggy 节点、失败假设、被否决的分支都要结构化保存，供反思与回放。
8. **成本可观测**：每轮记录 token、时间、美元成本；设预算熔断。
9. **沙箱默认**：所有 LLM 生成代码在隔离容器中运行，网络/文件系统/进程白名单，禁止自修改沙箱外代码。
10. **可审计即默认值**：全量日志（事件流）+环境指纹+谱系（parent ID），支持第三方仅凭产物复现结论。

### 5.2 组件架构

```
┌─────────────────────────────────────────────────────────────────┐
│  控制层 Control Plane                                           │
│  目标/约束（预注册） · 预算/熔断 · 人工门禁 · 审计日志           │
└─────────────────────────────────────────────────────────────────┘
        ↓                ↑ 反馈（指标/评审/告警）
┌─────────────────────────────────────────────────────────────────┐
│  编排层 Orchestration                                           │
│  实验经理 Experiment Manager（四阶段状态机）                     │
│  搜索器 Searcher（最佳优先树 / 档案进化 / 锦标赛，三选一）       │
│  调度器 Scheduler（并行、去重、防重复实验记录）                   │
└─────────────────────────────────────────────────────────────────┘
   ↓ 任务            ↑ 结果
┌──────────┬──────────┬──────────┬──────────┬──────────┐
│ 假设生成 │ 文献检索 │ 代码生成 │ 执行沙箱 │ 分析反思 │
│ Ideation │ Retrieval│ CodeGen  │ Sandbox  │ Analysis │
│ (多智能  │ (Semantic│ (CodeAct │ (Docker, │ (指标+   │
│  体辩论) │ Scholar/ │ + Aider  │ 事件流)  │ VLM+评审)│
│          │ OpenAlex)│ 式编辑)  │          │          │
└──────────┴──────────┴──────────┴──────────┴──────────┘
        ↓ 写入（仅已验证）
┌─────────────────────────────────────────────────────────────────┐
│  记忆层 Memory                                                  │
│  (a) 轨迹库：事件流全量日志（OpenHands 式）                      │
│  (b) 反思库：结构化教训（失败模式+修复+来源实验 ID）             │
│  (c) 技能/档案库：已验证代码技能 + 智能体版本谱系（DGM 式）      │
│  (d) 知识库：文献图谱+实体库（ResearchAgent 式）                 │
└─────────────────────────────────────────────────────────────────┘
        ↓ 定期
┌─────────────────────────────────────────────────────────────────┐
│  元层 Meta-loop（RSI 层）                                        │
│  改进器评估 → 提出改进器修改 → 沙箱验证 → 档案更新 → 回滚预案    │
│  评估：跨任务/跨模型迁移 + 回归测试（旧任务不退化）               │
└─────────────────────────────────────────────────────────────────┘
```

### 5.3 数据流（一次完整迭代）

```
输入：研究目标 G + 基线 B + 预算 C + 预注册 R
  1. Ideation: 检索文献 → 生成 N 个假设（多样性采样）→ 评审者打分（新颖/可行/清晰）
     → 剪枝到 K 个（保留 1–2 个高多样性垫脚石）
  2. Plan: 实验经理为每个假设生成实验计划（基线/对照/消融/种子/统计量）
  3. Code: CodeAct 生成代码 → 沙箱执行 → 若失败：记 buggy 节点 → 调试（≤N 次）
     → 若成功：绘图 → VLM 审图 → 记 non-buggy 节点
  4. Observe: 结构化记录 {指标, 曲线, 图, 日志, 环境指纹, 成本}
  5. Analyze: 与预测对照 → 归因（代码错/假设错/评估错/数据错）
     → 生成反思（结构化，非自由文本）
  6. Select: 搜索器按最佳优先+多样性选下一批扩展节点
  7. Gate: 阶段结束 → 复现（多种子）→ 聚合（均值±标准差）
     → 人工抽查（每阶段至少 1 次）→ 进入下一阶段或终止
  8. Meta（每 M 轮）：评估改进器本身（工具/提示/流程的修改提案）
     → 在 held-out 任务上验证 → 通过则更新档案，否则回滚
输出：实验报告（假设、方法、结果、消融、局限、复现脚本）+ 完整审计包
```

### 5.4 三层循环与停止准则（可直接抄入配置）

| 层 | 周期 | 产物 | 停止准则 |
|---|---|---|---|
| **L1 Debug 循环** | 分钟 | 可运行代码 | 运行通过 或 调试 ≥5 次 或 同一错误重复 ≥3 次→剪枝 |
| **L2 实验循环** | 小时 | 实验节点（树/档案） | 阶段预算耗尽；连续 3 轮最佳分数提升 <1%；复现方差过大→降级结论 |
| **L3 元循环** | 天/周 | 新版改进器 | 在 held-out 集上无提升连续 2 代；回归测试退化；人工否决 |

### 5.5 提示/模板（可直接复用的最小模板集）

> 以下为基于各论文公开机制提炼的**通用模板骨架**，非任何论文的原文提示；实施时需按领域填充 few-shot 示例与格式约束。

**（a）假设生成提示（骨架）**

```
你是一名研究者。目标：{goal}。基线：{baseline}。已检索文献：{papers_with_ids}。
提出 1 个可证伪假设，格式：
- 陈述：干预 X → 指标 Y 预期变化 Δ（方向+量级）
- 机制：为什么会发生（1–2 句，引用文献 ID）
- 预测：若假设为真，应观察到 {可观测现象}；若为假，应观察到 {反事实}
- 可证伪性：什么结果会否定该假设
- 风险：最可能的失败原因
不要提出无法用给定数据/算力在 {budget} 内检验的假设。
```

**（b）实验计划提示（骨架）**

```
基于假设 {hypothesis}，制定实验计划：
- 基线与对照：{baseline}，消融：{ablations}
- 数据：训练/验证/测试划分（锁定，不可事后改动），随机种子：{seeds}
- 指标：主指标 {primary}，辅指标 {secondary}，统计量：均值±标准差（≥3 种子）
- 成功标准（预注册）：主指标提升 ≥ {threshold} 且 held-out 复现
- 失败归因清单：先检查 {代码/数据/评估} 再怀疑假设
输出为 JSON，便于程序校验。
```

**（c）调试提示（骨架）**

```
任务：修复以下错误，最小改动优先。
代码：{code}  错误栈：{traceback}  相关反思：{reflections_top3}
规则：
- 只改与错误直接相关的部分；优先 str_replace 式细粒度编辑，勿重写全文件
- 每次只修一个错误；修完后说明改了什么、为什么
- 若同一错误出现 3 次，停止并返回“需人工/需换假设”而非继续猜测
```

**（d）反思提示（骨架，结构化输出）**

```
对比预测与观测：
- 预测：{prediction}  观测：{metrics, plots_summary}
- 结论：证实 / 证伪 / 无法判断（选一，并给出证据）
- 归因：代码错 / 假设错 / 评估错 / 数据错（选一，证据）
- 教训（1 条，可复用）：{lesson} —— 适用条件：{when}，不适用：{when_not}
- 下一步：修代码 / 换假设 / 修评估（选一，理由）
```

**（e）评审提示（骨架，评审者与生成者分离）**

```
你是独立评审者，未参与生成。按以下维度打分（1–5）并给出可操作的批评：
新颖性（与文献 {ids} 对比）、方法严谨性（对照/消融/复现是否齐全）、
结论-证据一致性（结论是否超出数据支持）、可复现性（脚本/种子/环境是否齐全）。
只根据提供的产物评审，不推测未提供的实验。
```

**（f）元改进提示（RSI 层，骨架）**

```
你是改进器设计者。当前改进器版本：{version}，谱系：{lineage}。
历史表现：{benchmarks}，失败模式：{failure_modes}。
提出 1 处对改进器本身的修改（工具/流程/提示/评审逻辑），格式：
- 修改：{diff_描述}  - 预期收益：{metric}  - 风险与回滚：{risk}
- 验证方案：在 held-out 任务 {tasks} 上测试，回归测试旧任务不退化
修改必须在沙箱中验证，不得绕过评估。
```

### 5.6 指标体系（Metrics）

| 类别 | 指标 | 说明 |
|---|---|---|
| **正确性** | 运行成功率、测试通过率 | L1 门禁 |
| **效果** | 主指标 Δ（held-out）、效应量、置信区间 | 预注册，防事后挑选 |
| **效率** | 每轮成本（$ / token / GPU-h）、达到目标所需轮数 | 预算管理 |
| **新颖性** | 与文献的嵌入距离、查新通过率、人类新颖性评分 | 防重复造轮子 |
| **严谨性** | 对照/消融完整率、复现方差、审计通过率 | 科学可信度 |
| **泛化** | 跨任务迁移、跨模型迁移、跨种子稳定性 | RSI 的核心证据（DGM 式） |
| **元改进** | 改进器版本间在 held-out 集上的提升、回归退化率 | 外层循环是否真在变强 |
| **安全** | 沙箱告警数、泄漏检测通过率、人工否决率 | 熔断依据 |

### 5.7 护栏、安全性与评估（Guardrails）

**必须有的护栏（按优先级）**：

1. **沙箱隔离**：Docker/微虚拟机，无默认网络出口，文件系统白名单，进程与资源限额；LLM 代码不得修改沙箱外代码、不得访问密钥/凭证。
2. **评估隔离**：测试集/held-out 集对生成智能体不可见；评估脚本由人类或独立流程锁定哈希；禁止智能体自写评估标准后自评。
3. **泄漏检测**：训练/验证/测试划分的自动化检查（时间切分、ID 去重、近重复检测）；引用/数据来源可追溯。
4. **多重验证**：关键结论需两种独立方法复核（如指标+人工抽查、LLM 初筛+专家终审）；VLM 审图不能替代数值复核。
5. **版本与回滚**：每次自修改生成新版本+谱系记录；旧版本保留；回归测试失败自动回滚。
6. **人工门禁**：阶段转换、评估标准变更、元层修改、对外发布（投稿/上线）四处必须人工批准；DGM 与 STOP 均强调“沙箱+人类监督”。
7. **成本与时间熔断**：单轮/单任务/单日预算上限，超限自动停机并告警。
8. **披露义务**：对外产物标注 AI 生成比例、人工筛选环节、局限与未验证部分；AI Scientist 的撤稿协议是值得借鉴的先例——在共识形成前，不让 AI 生成论文直接进入正式科学记录。

**安全风险清单（RSI 特有）**：沙箱绕过（STOP 已实测）、奖励 hacking（优化指标而非真实目标）、评估器被操纵、自我肯定循环、数据污染与泄漏、不可逆权重更新（SEAL）、失控的开放式进化（DGM 的谱系追溯是缓解手段，非根治）。

### 5.8 实施路线图（建议 3 个阶段）

| 阶段 | 目标 | 交付物 | 退出标准 |
|---|---|---|---|
| **P0 最小闭环（2–4 周）** | 单任务、单分支、可验证指标 | L1+L2 循环、沙箱、事件流日志、预注册模板 | 在 1 个任务上连续 3 次可复现运行 |
| **P1 树/档案搜索（1–2 月）** | 多分支、多样性、复现统计 | 树搜索+复现/聚合节点、反思库、VLM 审图 | 在 MLAgentBench/ScienceAgentBench 子集上达到文献基线水平 |
| **P2 元循环（2–3 月）** | 改进器本身可进化 | DGM 式档案+跨任务迁移评估+人工门禁 | 在 held-out 任务上，新版改进器显著优于旧版且无回归退化 |

---

## 6. 开放问题与未来方向

1. **评估即瓶颈**：开放科学问题缺乏廉价可验证评估器。AlphaEvolve/FunSearch 只适用于有高效 `evaluate()` 的问题；MLGym 的结论——前沿模型“只会调参，不会提新假设”——仍未被解决。未来方向：可验证的科学评估器设计、自动生成评估器、人类-AI 协同验证。
2. **假设质量 vs 实验吞吐**：当前系统擅长“跑很多实验”，不擅长“提好问题”。Co-Scientist 的锦标赛+辩论是 promising 的方向，但想法保守性仍存。未来：知识图谱引导的重组、跨域类比、反事实推理。
3. **长期记忆与遗忘**：技能库/档案会膨胀，需研究记忆的压缩、去重、遗忘与版本管理；STaR/SEAL 的权重记忆则需解决灾难性遗忘。
4. **从代码 RSI 到权重 RSI**：DGM 明确把“重训基础模型”列为未来工作；SEAL 是第一步，但规模小、任务窄。权重层 RSI 的安全性（不可逆、难审计）远高于代码层，需先建立回归测试与回滚标准。
5. **科学可信度基础设施**：PNAS 2026 的呼吁——从“手稿评审”转向“工作流产物审计”（日志、代码、数据指纹全公开）——将成为期刊与会议的政策议题。AutoResearch 系统需原生支持审计包导出。
6. **失败的闭环**：ARFT（secondary）指出的“发现缺陷仍照常发表”是当前最危险的失效模式。未来循环必须把“自我否决（self-veto）”作为一等公民：当证据不足时，系统应主动说“我不知道/此结论不成立”，而非继续优化表述。
7. **成本与可及性**：AlphaEvolve/DGM 的进化需要大规模算力；如何让中小实验室也能运行 RSI 循环（小模型+高效搜索+廉价评估器）是民主化关键。
8. **伦理与治理**：AI 生成论文的署名、撤稿、披露标准尚未形成共识；RSI 系统的自主权边界（能否自行决定研究方向、能否修改自身目标）需治理框架先行，而非事后补救。

---

## Could not verify / 开放问题

- **The Agentic Scientist（v3，2026-10）**：仅见于个别二手文章提及，未在 arXiv/Sakana 官网一级来源核实，**未列入正式结论**。
- **MLR-Bench “约 80% 捏造率”**：源自二手综述转述 arXiv:2505.19955，未核对原文，不作定量断言，仅作风险提示。
- **AutoResearchEval（arXiv:2608.14905）“82.5% 发现缺陷仍发表”**：源自二手深度分析文章，未核对原文，数字仅作线索。
- **Judge Goodhart 精确数字（87%→28%）**：源自二手综述的自有实验，非同行评审论文，不作定量断言。
- **2026 年后模型在各基准上的最新分数**：基准分数随模型快速变化，本报告分数均为论文发表时快照；如需最新 leaderboard，需另行查询各基准官网。
- **Anthropic 在 AutoResearch 上的公开系统**：本次检索未发现 Anthropic 发布的与 AI Scientist 对等的端到端系统（仅有其 Responsible Scaling Policy 被 PaperBench 引用为评估框架）；如需补充，需定向检索 Anthropic 官方博客/论文。
- **Microsoft Research 的 ResearchAgent 之外**：ResearchAgent 为 KAIST/Microsoft 合作；微软研究院在科学智能体上的其他系统（如 BioGPT 等）未在本任务范围内展开。

---

## Sources / Bibliography（按主题分组；优先一手来源）

### A. 理论基础：RSI 定义

1. Good, I. J. (1965). Speculations Concerning the First Ultraintelligent Machine. In *Advances in Computers*, Vol. 6. 〔智能爆炸源头；转述见 Yampolskiy 2015〕
2. Yampolskiy, R. V. (2015). From Seed AI to Technological Singularity via Recursively Self-Improving Software. arXiv:1502.06512. https://arxiv.org/abs/1502.06512
3. Schmidhuber, J. (2007). Gödel Machines: Self-Referential Universal Problem Solvers Making Provably Optimal Self-Improvements. arXiv:cs/0309048. https://arxiv.org/abs/cs/0309048

### B. 端到端 AutoResearch 系统

4. Lu, C., Lu, C., Lange, R. T., Foerster, J., Clune, J., & Ha, D. (2024). The AI Scientist: Towards Fully Automated Open-Ended Scientific Discovery. arXiv:2408.06292. https://arxiv.org/abs/2408.06292 — 代码 https://github.com/SakanaAI/AI-Scientist
5. Yamada, Y., Lange, R. T., Lu, C., Lu, C., Hu, S., Foerster, J., Ha, D., & Clune, J. (2025). The AI Scientist-v2: Workshop-Level Automated Scientific Discovery via Agentic Tree Search. arXiv:2504.08066. https://arxiv.org/abs/2504.08066 — 代码 https://github.com/SakanaAI/AI-Scientist-v2 — **`verified live` (arXiv HTML, 2026-11-22)**
6. Yamada, Y., et al. (2026). Towards End-to-End Automation of AI Research. *Nature* 651, 914–919. doi:10.1038/s41586-026-10265-5. https://arxiv.org/abs/2606.15497
7. Schmidgall, S., et al. (2025). Agent Laboratory: Using LLM Agents as Research Assistants. arXiv:2501.04227. https://arxiv.org/abs/2501.04227 — EMNLP 2025 Findings
8. Baek, J., Jauhar, S. K., Cucerzan, S., & Hwang, S. J. (2024/2025). ResearchAgent: Iterative Research Idea Generation over Scientific Literature with Large Language Models. arXiv:2404.07738. https://arxiv.org/abs/2404.07738 — NAACL 2025
9. Gottweis, J., et al. (2026). Accelerating scientific discovery with Co-Scientist. *Nature*. doi:10.1038/s41586-026-10644-y. arXiv:2502.18864. https://arxiv.org/abs/2502.18864
10. Beel, J., et al. (2025). Evaluating Sakana's AI Scientist for Autonomous Research. arXiv:2502.14297. https://arxiv.org/abs/2502.14297 〔独立批判评估〕

### C. RSI 专论：自修改智能体

11. Zhang, J., Hu, S., Lu, C., Lange, R., & Clune, J. (2025). Darwin Gödel Machine: Open-Ended Evolution of Self-Improving Agents. arXiv:2505.22954. https://arxiv.org/abs/2505.22954 — 代码 https://github.com/jennyzzt/dgm — **`verified live` (arXiv HTML, 2026-11-22)**
12. Yin, X., Wang, X., Pan, L., Lin, L., Wan, X., & Wang, W. Y. (2024/2025). Gödel Agent: A Self-Referential Agent Framework for Recursive Self-Improvement. arXiv:2410.04444. https://arxiv.org/abs/2410.04444 — ACL 2025 — 代码 https://github.com/Arvid-pku/Godel_Agent
13. Zelikman, E., Lorch, E., Mackey, L., & Kalai, A. T. (2024). Self-Taught Optimizer (STOP): Recursively Self-Improving Code Generation. arXiv:2310.02304. https://arxiv.org/abs/2310.02304 — COLM 2024
14. Zweiger, A., Pari, J., Guo, H., Akyürek, E., Kim, Y., & Agrawal, P. (2025). Self-Adapting Language Models (SEAL). arXiv:2506.10943. https://arxiv.org/abs/2506.10943 — NeurIPS 2025 — https://jyopari.github.io/posts/seal

### D. 进化式发现：FunSearch / AlphaEvolve

15. Romera-Paredes, B., et al. (2024). Mathematical discoveries from program search with large language models (FunSearch). *Nature* 625, 468–475. doi:10.1038/s41586-023-06924-6. https://www.nature.com/articles/s41586-023-06924-6
16. Novikov, A., et al. (2025). AlphaEvolve: A coding agent for scientific and algorithmic discovery. arXiv:2506.13131. https://arxiv.org/abs/2506.13131 — 官方博客 https://deepmind.google/blog/alphaevolve-a-gemini-powered-coding-agent-for-designing-advanced-algorithms/

### E. 循环基础机制

17. Yao, S., et al. (2023). ReAct: Synergizing Reasoning and Acting in Language Models. arXiv:2210.03629. https://arxiv.org/abs/2210.03629 — ICLR 2023
18. Shinn, N., et al. (2023). Reflexion: Language Agents with Verbal Reinforcement Learning. arXiv:2303.11366. https://arxiv.org/abs/2303.11366 — NeurIPS 2023
19. Madaan, A., et al. (2023). Self-Refine: Iterative Refinement with Self-Feedback. arXiv:2303.17651. https://arxiv.org/abs/2303.17651 — NeurIPS 2023
20. Zelikman, E., Wu, Y., Mu, J., & Goodman, N. D. (2022). STaR: Bootstrapping Reasoning With Reasoning. arXiv:2203.14465. https://arxiv.org/abs/2203.14465 — NeurIPS 2022
21. Wang, G., et al. (2023). Voyager: An Open-Ended Embodied Agent with Large Language Models. arXiv:2305.16291. https://arxiv.org/abs/2305.16291 — TMLR 2024 — https://voyager.minedojo.org

### F. 代码智能体底座

22. Wang, X., et al. (2024). Executable Code Actions Elicit Better LLM Agents (CodeAct). arXiv:2402.01030. https://arxiv.org/abs/2402.01030 — ICML 2024
23. Wang, X., et al. (2024). OpenHands: An Open Platform for AI Software Developers as Generalist Agents. arXiv:2407.16741. https://arxiv.org/abs/2407.16741 — 代码 https://github.com/All-Hands-AI/OpenHands
24. Jiang, Z., et al. (2025). AIDE: AI-Driven Exploration in the Space of Code. arXiv:2502.13138. https://arxiv.org/abs/2502.13138 — 代码 https://github.com/WecoAI/aideml

### G. 基准与评估

25. Huang, Q., Vora, J., Liang, P., & Leskovec, J. (2023). MLAgentBench: Evaluating Language Agents on Machine Learning Experimentation. arXiv:2310.03302. https://arxiv.org/abs/2310.03302
26. Chen, Z., et al. (2024). ScienceAgentBench: Toward Rigorous Assessment of Language Agents for Data-Driven Scientific Discovery. arXiv:2410.05080. https://arxiv.org/abs/2410.05080
27. Chan, J. S., et al. (2024). MLE-bench: Evaluating Machine Learning Agents on Machine Learning Engineering. arXiv:2410.07095. https://arxiv.org/abs/2410.07095
28. Starace, G., et al. (2025). PaperBench: Evaluating AI's Ability to Replicate AI Research. arXiv:2504.01848. https://arxiv.org/abs/2504.01848 — OpenAI
29. Wijk, H., et al. (2024). RE-Bench: Evaluating frontier AI R&D capabilities of language model agents against human experts. arXiv:2411.15114. https://arxiv.org/abs/2411.15114 — METR
30. Nathani, D., et al. (2025). MLGym: A New Framework and Benchmark for Advancing AI Research Agents. arXiv:2502.14499. https://arxiv.org/abs/2502.14499 — Meta — 代码 https://github.com/facebookresearch/MLGym

### H. 失败模式与可信度批判

31. Luo, R., Kasirzadeh, A., & Shah, C., et al. (2026). The more you automate, the less you see: Hidden pitfalls of autonomous AI scientists. *PNAS*. doi:10.1073/pnas.2610214123. https://www.pnas.org/doi/10.1073/pnas.2610214123
32. Fei, Y., et al. (2026). How Do Agents Fail on AutoResearch (AutoResearchEval). arXiv:2608.14905. 〔secondary 转述，未核对原文，见 §Could not verify〕

### I. 综述与资源（secondary，仅作导航）

33. Awesome Agents for Science (OSU NLP Group). https://github.com/osu-nlp-group/awesome-agents4science
34. Awesome Recursive Self-Improvement. https://github.com/persdre/awesome-recursive-self-improvement/blob/HEAD/README.md

---

*注：本报告所有 arXiv ID 与 DOI 均来自检索结果或直接打开的 arXiv 页面；分数与日期为各论文发表时的报告值，非实时 leaderboard。凡标注 `verified live` 的事实已于 2026-11-22 直接核对 arXiv HTML 全文；其余为 `index` 核对；标注 `secondary` 的内容未作为关键证据使用。*
