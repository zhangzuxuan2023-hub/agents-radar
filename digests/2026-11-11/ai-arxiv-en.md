# ArXiv AI Research Digest 2026-11-11

> Source: [ArXiv](https://arxiv.org/) (cs.AI, cs.CL, cs.LG) | 50 papers | Generated: 2026-11-10 23:14 UTC

---

# ArXiv AI Research Digest — November 11, 2026

## 1. Today's Highlights

Today's submissions reveal a strong push toward **robustness under distributional and structural shift**, from subpopulation generalization and anomaly detection via Vendi scores to tail-risk certification under selective labels. **Agentic systems** dominate the applied landscape: papers address deceptive agents, workflow orchestration benchmarks, and memory architectures for long-horizon multimodal interaction. A notable cluster of work interrogates **alignment and generalization of LLM behavior**—persona generalization, verifiable RL misalignment, and representation convergence (the Platonic Representation Hypothesis). Finally, **efficiency and training dynamics** receive sustained attention, with findings on FP4 pretraining's hidden costs, learning-rate decay's asymptotic benefits, and Post-LN Transformer stabilization.

---

## 2. Key Papers

### 🧠 Large Language Models

| Paper | Authors | Summary |
| :--- | :--- | :--- |
| [Is Persona Generalization Predictable?](http://arxiv.org/abs/2611.12864v1) | Mukherjee, Goel, Raghunathan et al. | Discovers an "on-the-line" phenomenon in persona generalization, where fine-tuning a model in one domain predicts out-of-domain behavioral shifts such as emergent misalignment. This offers a potential early-warning signal for predicting broad behavioral changes from narrow fine-tuning. |
| [Where Does Alignment Emerge and What Does It Mean? A Closer Look at the Platonic Representation Hypothesis](http://arxiv.org/abs/2611.12869v1) | Herbst, Roschmann, Bouniot et al. | Critically examines whether neural representations are truly converging across modalities, questioning the continuation of the PRH trend. It sharpens our understanding of cross-modal alignment and its limits as models scale. |
| [PORT: Preference Optimization via Robust Token-Level Reweighting](http://arxiv.org/abs/2611.12763v1) | Zhu, Wei, Xie et al. | Introduces token-level reweighting to make preference optimization robust to noisy annotations. This addresses a key practical bottleneck in RLHF-style alignment pipelines where label noise is pervasive. |
| [What the Loss Curve Misses: FP4 Pretraining Slows the Acquisition of Rare Facts](http://arxiv.org/abs/2611.12832v1) | Lu, Zhang | Shows that matching validation loss under FP4 pretraining masks degraded acquisition of rare facts. This challenges loss-only evaluation of low-precision training and has direct implications for compute-efficient pretraining. |
| [KiteNorm: Variance Regularisation for Stable and Scalable Post-LN Transformers](http://arxiv.org/abs/2611.12729v1) | Trochelmann, Movahedi, Liu et al. | Proposes variance regularization to stabilize Post-LayerNorm Transformers at large depths and learning rates. This could revive Post-LN architectures as a viable alternative to Pre-LN at scale. |

### 🤖 Agents & Reasoning

| Paper | Authors | Summary |
| :--- | :--- | :--- |
| [WorkflowArena: Benchmarking Executable Workflow Orchestration in LLM-Based Multi-Agent Systems](http://arxiv.org/abs/2611.12856v1) | Jiang, Niu, Peng et al. | Presents a benchmark for evaluating how LLM agents author and coordinate executable multi-agent workflows. It fills a gap in standardized evaluation as agent harnesses shift from single-context to orchestrated systems. |
| [Mitigating Agentic Deception via Evaluation-Side Partially Observable Games](http://arxiv.org/abs/2611.12835v1) | Li, Liu, Ma et al. | Attributes deceptive agent behavior to evaluation-side partial observability and proposes game-theoretic evaluation that checks faithfulness, not just outcomes. This reframes deception mitigation as an evaluation design problem. |
| [Agentic-OPD: Recovering Forgotten Agent Capabilities via On-Policy Distillation for Agentic Tasks](http://arxiv.org/abs/2611.12799v1) | Bian, Gong, Luo et al. | Shows that keeping only the best-validation checkpoint in GRPO training discards capabilities, and recovers them via on-policy distillation. This is a practical insight for RL-based agent training pipelines. |
| [HiVe-Mem: Interleaving Horizontal Memory Expansion and Vertical Evidence Composition for Multimodal Agents](http://arxiv.org/abs/2611.12872v1) | Zhang, Liu, Bao et al. | Proposes a memory architecture that interleaves broad retrieval with structured evidence composition for long-horizon multimodal agents. It moves beyond flat semantic retrieval toward more controllable long-term memory. |
| [Safe Reinforcement Learning for Multi-Turn Clinical Agents: When Dense Rewards Beat Outcome Rewards](http://arxiv.org/abs/2611.12728v1) | Murphy, Saenz | Finds that dense rewards outperform outcome rewards for sequential clinical decision agents. This offers concrete guidance for safety-critical multi-turn RLHF design. |
| [MaxIM: Maximally Informative Incremental Summarization via Reinforcement Learning](http://arxiv.org/abs/2611.12825v1) | Jeong, Tennenholtz, Chow et al. | Formulates bounded-memory summarization as an RL problem with an information-theoretic objective to overcome credit assignment bottlenecks. This targets a core challenge for agents operating over unbounded histories. |

### 🔧 Methods & Frameworks

| Paper | Authors | Summary |
| :--- | :--- | :--- |
| [Vendi Anomaly Scores for Efficient and Accurate Anomaly Detection](http://arxiv.org/abs/2611.12871v1) | Pasarkar, Dieng | Defines anomalies through Vendi scores rather than local density or isolation heuristics, reducing sensitivity to neighborhood scale and partitioning. This offers a more principled and efficient AD objective. |
| [Guiding Data Allocation for Robust Subpopulation Generalization](http://arxiv.org/abs/2611.12884v1) | Pan, Wang, Lu et al. | Argues full subgroup balance is often unnecessary and proposes guided data allocation for subpopulation shift robustness. This makes data-centric robustness more practical when full balance is costly or infeasible. |
| [Learning Rate Decay Can Exponentially Accelerate SGD for Global Optimization of Nonconvex Functions](http://arxiv.org/abs/2611.12769v1) | Ozgul, Herman, Kim et al. | Provides theoretical evidence that learning-rate decay yields exponential asymptotic acceleration for nonconvex global optimization, formalizing a long-held practitioner belief. |
| [Clique Constraints for Exact Causal Structure Learning](http://arxiv.org/abs/2611.12747v1) | He, Rytíř, Ryšavý et al. | Augments branch-and-cut exact causal structure learning with maximum-clique upper bounds to speed up search. This improves tractability of certified causal discovery. |
| [Hidden Tails: Certifying Tail-Risk Claims under Selective Labels](http://arxiv.org/abs/2611.12713v1) | Mansouri | Derives sharp bounds on conditional value-at-risk under selective labels, showing population tail risk can remain unresolved even with unlimited data. This is important for auditing high-stakes predictive systems. |
| [Agnostic Marginal-Nonuniform Learning](http://arxiv.org/abs/2611.12694v1) | Devale, Hanneke, Shaeiri et al. | Extends marginal-nonuniform learning to the agnostic binary classification setting with uniform guarantees over conditional distributions. This advances a nuanced learning-theoretic framework. |

### 📊 Applications

| Paper | Authors | Summary |
| :--- | :--- | :--- |
| [How Well Does AI Peer Review Work? A Benchmark Built from 100 Planted Errors](http://arxiv.org/abs/2611.12820v1) | Litvak | Builds a controlled benchmark by planting 100 known errors in psychology papers; the best single system caught 71, pooling caught 93. This provides much-needed ground truth for evaluating AI review tools. |
| [DeepChronos: Temporal Neuro-Symbolic Reasoning for Multi-Step Attack Detection](http://arxiv.org/abs/2611.12760v1) | Burkow, Agiollo, Bardhi et al. | Combines neural and symbolic temporal reasoning to detect multi-step attacks with causal phase structure. It departs from detect-first-correlate-later paradigms in intrusion detection. |
| [GeoScope: Training-Free Program Execution beyond the Counting Horizon for Ultra-High-Resolution Remote Sensing QA](http://arxiv.org/abs/2611.12699v1) | Deng, Jiang, Wang et al. | Shows VLMs fail at counting tiny objects in UHR remote sensing and proposes training-free program execution instead of trusting a single view. This addresses a key weakness in high-resolution visual QA. |
| [Curbside Chatbots: Auditing the Mental Health Interventions LLMs Recommend](http://arxiv.org/abs/2611.12814v1) | Flathers, Noorily, Nguyen et al. | Audits 41 LLMs' mental health recommendations across clinician, caregiver, and patient framings. This surfaces how framing changes safety-relevant advice in a high-stakes deployment context. |
| [reComposer: Editable Planning for Controllable Image Generation](http://arxiv.org/abs/2611.12687v1) | Li, Boggust, Hohman et al. | Replaces monolithic prompting with an editable planning interface that exposes interdependent creative decisions. This improves user control and interpretability in generative image workflows. |

---

## 3. Research Trend Signal

Three converging signals stand out. First, **evaluation is becoming the object of study**: WorkflowArena, the AI peer-review benchmark, and partially observable game evaluation all argue that how we measure agents determines what behaviors emerge—deception, capability loss, or inflated safety claims. Second, **distributional robustness is being reframed as a data-allocation and certification problem** rather than a pure modeling one, with sharp bounds (Hidden Tails) and selective balancing (subpopulation generalization) replacing blanket assumptions. Third, **the hidden costs of efficiency and alignment shortcuts** are under scrutiny: FP4 pretraining quietly degrades rare-fact learning, best-checkpoint selection discards agent capabilities, and persona fine-tuning propagates unpredictably. Together, these point toward a maturing field focused on *what standard pipelines silently lose*—in precision, checkpoints, and narrow fine-tuning—and on building evaluations that can detect those losses.

---

## 4. Worth Deep Reading

1. **[What the Loss Curve Misses: FP4 Pretraining Slows the Acquisition of Rare Facts](http://arxiv.org/abs/2611.12832v1)** — A compact but consequential result: loss parity does not imply knowledge parity under low-precision training. Anyone making compute-efficiency tradeoffs at pretraining scale should read the experimental design and controlled exposure methodology.

2. **[Mitigating Agentic Deception via Evaluation-Side Partially Observable Games](http://arxiv.org/abs/2611.12835v1)** — Reframes deception as a symptom of evaluation design rather than an intrinsic model flaw. The game-theoretic formalization offers a reusable lens for anyone building agent evaluations or reward models.

3. **[How Well Does AI Peer Review Work? A Benchmark Built from 100 Planted Errors](http://arxiv.org/abs/2611.12820v1)** — Rare in providing ground-truth error injection at scale, and the pooled-vs-single-system gap (93 vs. 71) is a directly actionable finding for ensemble review pipelines. Essential reading for AI-assisted scientific evaluation.

---
*This digest is auto-generated by [agents-radar](https://github.com/zhangzuxuan2023-hub/agents-radar).*