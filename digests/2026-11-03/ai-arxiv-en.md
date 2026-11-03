# ArXiv AI Research Digest 2026-11-03

> Source: [ArXiv](https://arxiv.org/) (cs.AI, cs.CL, cs.LG) | 50 papers | Generated: 2026-11-03 10:36 UTC

---

# ArXiv AI Research Digest — 2026-11-03

## Today's Highlights

Today's submissions show a strong push toward **closing the loop between data infrastructure and policy learning in robotics**, with the paired Pixel-UMI and Pixel papers presenting an integrated hardware–retargeting–policy stack for whole-body mobile manipulation. **Agentic systems dominate across domains** — from meta-agents that evolve their own inference strategies (Strategist) and forward-deployed engineer agents for enterprise text-to-SQL to agents tackling Earth system model parameterizations and power grid connection studies. **Safety and evaluation of agents** is emerging as a first-class concern, evidenced by deception measurement in multi-agent games, harmful-success benchmarking for coding agents, and cross-framework measurement of MCP tool poisoning. On the optimization and training side, results on Muon vs. AdamW (advantage reversing with pretraining scale) and a scaling theory of multi-epoch pretraining signal a maturing focus on **data-constrained regimes**. Methodologically, **neuro-symbolic and physics-informed hybrids** continue to gain traction, from exact-cover solvers for logic puzzles to variational functional neural networks and spectral-temporal graph forecasting.

---

## Key Papers

### 🧠 Large Language Models (architecture, training, alignment, evaluation)

| Paper | Authors | Summary |
| :--- | :--- | :--- |
| [Pretraining Data Scale Reverses the Muon-AdamW Fine-Tuning Gap](http://arxiv.org/abs/2611.03633v1) | Catherine Li, Jingchu Gai, Andrej Risteski et al. | Shows that Muon's pretraining advantage over AdamW does not straightforwardly persist into fine-tuning and that the gap reverses with pretraining data scale. This has direct implications for optimizer choice in the data-bound pretraining regimes the field is entering. |
| [A Scaling Theory of Multi-Epoch Pretraining: Late-Time Overfitting and Ensembling](http://arxiv.org/abs/2611.03590v1) | Zhen Yang, Yidi Miao, Blake Bordelon | Develops scaling-law theory for multi-epoch pretraining, characterizing late-time overfitting and the benefit of ensembling as data becomes the binding constraint. Provides theoretical grounding for practitioners planning repeated-epoch training runs. |
| [Sonar-MoE: In-Backward Expert Updates for Memory-Efficient Mixture-of-Experts Training](http://arxiv.org/abs/2611.03577v1) | Avni Mittal, Avinash Anand, Ashutosh Kumar et al. | Updates each expert during the backward pass to avoid holding all expert gradients in GPU memory until the optimizer step. Directly reduces the memory bottleneck that scales with expert count and size in MoE training. |
| [Library of Layers (LoL): Adaptively Ordering Transformer Layers at Runtime](http://arxiv.org/abs/2611.03526v1) | Abdellah Rahmani, Sean McLeish, Ang Li et al. | Introduces runtime-adaptive ordering of reusable transformer layers, analogous to how programs compose primitives in different orders. Challenges the fixed-layer-order assumption of standard LLM architectures. |
| [VIAS: Value-Isolating Activation Steering for Multi-Value Alignment and Compliance for Organisations](http://arxiv.org/abs/2611.03518v1) | Avni Mittal, Avinash Anand, Dikshant Kukreja et al. | Applies activation steering with value-isolating vectors to improve organizational value compliance beyond prompting, without weight changes. Offers a practical lightweight alignment mechanism for enterprise deployments with multiple competing values. |
| [Tracing the Scientific Discovery Capacity of LLMs to Their Training Data](http://arxiv.org/abs/2611.03617v1) | Arda Uzunoglu, Mahler Revsine, Michael Schatz et al. | Frames scientific discovery capacity as a missing-probability-mass problem and traces it back to training data. Provides a theoretical lens for understanding when LLM "discovery" reflects genuine capability versus memorization. |

### 🤖 Agents & Reasoning (planning, tool use, multi-agent, chain-of-thought)

| Paper | Authors | Summary |
| :--- | :--- | :--- |
| [Strategist: Evolving Reasoning with Agents](http://arxiv.org/abs/2611.03618v1) | Taiming Lu, Qi Xu, Zhigang Hua et al. | Proposes a meta-agent that automatically evolves inference strategies — the previously hand-written reasoning procedures that turn model calls into responses. Shifts agent engineering from manual prompt/procedure design to automated strategy search. |
| [From Natural Language to Exact Cover: A Neuro-Symbolic Approach to Zebra Puzzles](http://arxiv.org/abs/2611.03641v1) | Paulius Skaisgiris, Thomas Pammer, Veronika Semmelrock et al. | Combines LLM parsing with an exact-cover symbolic solver to overcome pure neural failures on elementary logical reasoning. A concrete demonstration that hybrid architectures outperform CoT-only LLMs on constrained logic tasks. |
| [Four Bridges: Measuring Deception Propensity in a Multi-Agent Game](http://arxiv.org/abs/2611.03600v1) | Raphael Casey, Michael Simeone, Alberto Tosato et al. | Measures deception that emerges in multi-agent interaction without explicit instruction to deceive, moving beyond single-turn single-agent honesty benchmarks. Important for anticipating emergent misalignment in agent populations. |
| [Not All Reasoning Generalizes: A Case Study on Learning Generalizable Reasoning Strategies in RLVR](http://arxiv.org/abs/2611.03627v1) | Ziqi Pang, Yu-Xiong Wang | Uses a controllable equation-solving framework to study when RLVR produces generalizable reasoning strategies versus task-specific gains. Clarifies the boundary conditions for RLVR as a generalization mechanism. |
| [FADE: Factored Ability–Difficulty Filtering for Online RLVR Scheduling](http://arxiv.org/abs/2611.03625v1) | Tomas Duncun, Jonah Ellison, Mateo Ruiz et al. | Proposes an adaptive scheduler that factors model ability and prompt difficulty to steer rollouts toward informative (disagreeing) groups. Addresses the wasted generation budget from all-pass/all-fail groups in group-based RLVR. |
| [Ask, Don't Equip: Question-Driven Coding Agents for Deep Kernel Optimization](http://arxiv.org/abs/2611.03517v1) | Gabriele Oliaro, Jaeseong Lee, Zhaoyuan Su et al. | Positions question-driven agents between scripted and open-ended extremes for GPU kernel optimization, gaining sample efficiency without recipe rigidity. Offers a middle path in the structure-freedom tradeoff for agent design. |

### 🔧 Methods & Frameworks (new techniques, benchmarks, efficiency improvements)

| Paper | Authors | Summary |
| :--- | :--- | :--- |
| [VeriHarm-Bench: Measuring Harmful Success in LLM Coding Agents Verifiably](http://arxiv.org/abs/2611.03575v1) | Wei-Lin Chen, Zhepei Wei, Xinyu Zhu et al. | Introduces a verifiable benchmark for cases where coding agents achieve user goals while causing collateral harm (deleted files, weakened permissions). Fills a critical gap as agents gain shell access in production. |
| [MCP Tool Poisoning in Agentic AI Frameworks: A Cross-Framework Measurement Study](http://arxiv.org/abs/2611.03555v1) | Farooq Shaikh, Biagio Montaruli | Measures how attacker-controlled tool descriptions, schemas, and responses propagate into model context across agentic frameworks using MCP. Extends prior single-model compliance benchmarks to a cross-framework security measurement. |
| [DistDebug-Bench: Can LLM Agents Diagnose Bugs in Distributed Systems?](http://arxiv.org/abs/2611.03523v1) | Yuxuan Jiang, Chenglin Liu, John Rose et al. | Provides a benchmark for LLM agent debugging in distributed systems, moving beyond self-contained Python modules. Targets a high-value production setting that existing software-engineering benchmarks largely ignore. |
| [WebHarbor: Docking Real Websites for Evolving GUI Agent Environments](http://arxiv.org/abs/2611.03634v1) | Zhaoyang Wang, Qianhui Wu, Boyu Gou et al. | Docks real websites into stable, interactive environments for both GUI agent evaluation and training, addressing live-site drift and synthetic-environment mismatch. A practical infrastructure contribution for web agent research. |
| [BISECT: Bilevel Spectral Decomposition for Low-Rank Continual Learning](http://arxiv.org/abs/2611.03574v1) | Tom Saliencro, Wen-Hsuan Liu, Rohan Desai et al. | Replaces strict subspace isolation in LoRA-based continual learning with bilevel spectral decomposition of the update space. Improves plasticity–stability tradeoffs for exemplar-free ViT continual learning. |
| [Variational Functional Neural Networks (vfNN)](http://arxiv.org/abs/2611.03542v1) | Chinmay Katke, C. Nadir Kaplan | Optimizes physically motivated continuum functionals in integral form directly, bypassing Euler-Lagrange equations of motion. A physics-native learning paradigm with broad potential in materials and soft-matter simulation. |

### 📊 Applications (domain-specific, multimodal, code generation)

| Paper | Authors | Summary |
| :--- | :--- | :--- |
| [Pixel-UMI: Learning Environment-Aware Whole-Body Manipulation from Sensorized Human Demonstrations](http://arxiv.org/abs/2611.03647v1) | Jason Jingzhou Liu, Tony Tao, Deepak Pathak et al. | Introduces Environment-Aware Retargeting to learn whole-body mobile manipulation policies from sensorized human demonstrations despite embodiment gaps. Advances scalable human-data-driven robot learning. |
| [Pixel: A Co-Designed Full-Stack Robot System for Learning General-Purpose Mobile Manipulation](http://arxiv.org/abs/2611.03648v1) | Tony Tao, Jason Jingzhou Liu, Ruslan Salakhutdinov et al. | Co-designs hardware, demonstration collection, whole-body control, and policy learning as an integrated stack rather than independently developed components. Argues component mismatch is a primary limiter of general-purpose mobile manipulation. |
| [D&A: Decoupling Description and Assessment Enables VLMs as Zero-Shot Robotic Reward Models](http://arxiv.org/abs/2611.03622v1) | Marius Memmel, Shawn Yin, Urvi Rutia et al. | Decouples task description from progress assessment to mitigate confirmation bias and hallucinated progress in VLM-based reward models. Improves reliability of zero-shot video task-progress estimation for robot learning. |
| [NitiBench+: A Temporal Thai Legal Question Answering Benchmark Across 17 Domains](http://arxiv.org/abs/2611.03576v1) | Pawitsapak Akarajaradwong, Sarana Nutanong, Chompakorn Chaksangchaichot | Builds a temporally aware Thai legal QA benchmark after finding up to 67% of court decisions cite since-amended or repealed law. Exposes a systematic flaw in static legal QA benchmarks that grade against current law. |
| [DiabAgent: Toward Agentic Physiological Control](http://arxiv.org/abs/2611.03601v1) | Chengxi Li, Shaoxuan Li, Deyao Hong et al. | Systematically evaluates medical LLMs on sustained physiological control using type 1 diabetes insulin management as a concrete testbed. Shifts medical LLM evaluation from QA accuracy to closed-loop intervention effectiveness. |
| [ufakzeka-kulak: Building and Evaluating a 31.6M-Parameter Turkish Speech Recognizer from Scratch](http://arxiv.org/abs/2611.03605v1) | Sait Furkan Teke | Releases a compact (31.6M) FastConformer-TDT Turkish ASR model trained from scratch on ~5k hours under Apache-2.0. Demonstrates that capable, openly licensed ASR is achievable at small scale for under-resourced languages. |
| [PlantVLM-Edge: Quantized Vision-Language Models for On-Device Crop Health Diagnosis](http://arxiv.org/abs/2611.03557v1) | Gaurab Wagle, Bishal Adhikari, Jiajia Li et al. | Quantizes VLMs for on-device crop health diagnosis where expert knowledge is scarce. Brings multimodal reasoning to agricultural edge deployment constraints. |

---

## Research Trend Signal

Three converging signals stand out. **First, the agent safety stack is being built out rapidly**: tool-poisoning measurement (MCP), harmful-success benchmarks (VeriHarm), emergent deception (Four Bridges), and distributed-system debugging (DistDebug) collectively treat agents as deployed systems with attack surfaces, not just models. **Second, infrastructure for agents is maturing**: WebHarbor for stable GUI environments, Strategist for automated inference-strategy evolution, and forward-deployed-engineer studies indicate the field is shifting from "can agents do X" to "how do we operationalize and govern agents doing X." **Third, the data-constrained regime is reshaping training theory**: the Muon-AdamW reversal, multi-epoch scaling laws, and memory-efficient MoE training all respond to a world where compute outpaces data. Meanwhile, robotics is converging on co-designed full-stack systems and human-demonstration pipelines, and neuro-symbolic/physics-informed hybrids are establishing themselves as the default for tasks where pure neural approaches fail on formal correctness.

---

## Worth Deep Reading

1. **[Pretraining Data Scale Reverses the Muon-AdamW Fine-Tuning Gap](http://arxiv.org/abs/2611.03633v1)** — This paper challenges an increasingly common assumption (Muon's superiority carries through) with a scale-dependent reversal. Anyone choosing optimizers for pretraining-then-fine-tuning pipelines should understand the mechanism, and it pairs naturally with the multi-epoch scaling theory paper for a fuller picture of the data-bound regime.

2. **[Pixel: A Co-Designed Full-Stack Robot System](http://arxiv.org/abs/2611.03648v1)** (read alongside [Pixel-UMI](http://arxiv.org/abs/2611.03647v1)) — The co-design thesis — that independently developed hardware, data collection, control, and learning components create limiting mismatches — is a strong architectural argument with implications beyond robotics (e.g., agent frameworks and evaluation stacks). The paired papers provide both system-level and method-level views.

3. **[VeriHarm-Bench](http://arxiv.org/abs/2611.03575v1)** — Verifiable measurement of harmful success in shell-equipped coding agents is exactly the kind of benchmark that shapes deployment policy. Its methodology (verifying collateral harm rather than judging outputs) is likely to be reused across agent-safety work, making it valuable reading for both safety researchers and practitioners shipping coding agents.

---
*This digest is auto-generated by [agents-radar](https://github.com/zhangzuxuan2023-hub/agents-radar).*