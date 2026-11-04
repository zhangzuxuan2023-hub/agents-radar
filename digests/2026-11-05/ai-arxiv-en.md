# ArXiv AI Research Digest 2026-11-05

> Source: [ArXiv](https://arxiv.org/) (cs.AI, cs.CL, cs.LG) | 50 papers | Generated: 2026-11-04 23:22 UTC

---

# ArXiv AI Research Digest — 2026-11-05

## 1. Today's Highlights

Today's submissions show a clear pivot from scaling context windows toward **spending compute more intelligently**: a headline result argues that frequent context compaction lets frontier models match long-context performance at a fraction of the cost, while looped-transformer variants (ARLoop, LoopVQ) offer parameter-efficient depth scaling with sub-1-bit KV cache quantization. A second strong current is **agentic self-evolution** — skill-memory distillation (MDSKILL), adversarial speculative reasoning, and stable on-policy guidance for agents trained with rubric-based judge rewards. Alignment research continues to mature beyond behavioral compliance: papers probe *durable* suppression of misaligned behaviors, misalignment induced by training against a model's own beliefs, and geometric detection of suppressed knowledge in activations. Finally, the theory stack had a strong day, with new results on logarithmic regret for alternating mirror descent, differentially private agnostic halfspace learning at no extra cost, and a broad learning-theoretic generalization of the hardcore lemma.

---

## 2. Key Papers

### 🧠 Large Language Models

| Paper | Authors | Summary |
| :--- | :--- | :--- |
| [You don't need long context](http://arxiv.org/abs/2611.05306v1) | Belcak, Muralidharan, Jiang et al. | Shows that frequent context compaction lets frontier open-weight models preserve nearly all accuracy on long-horizon and long-input tasks, despite per-token costs rising up to 7x at long positions. A pragmatic counterpoint to the long-context arms race with immediate deployment relevance. |
| [ARLoop: Fully-Open Pre-trained Looped Transformers with Attention Residuals](http://arxiv.org/abs/2611.05234v1) | Yao, Du, Zhu et al. | Releases fully open pre-trained looped transformers that reuse shared layers for parameter-efficient depth scaling, adding attention residuals to control information flow across recurrent depth. Strengthens the looped-architecture line with an open artifact the community can build on. |
| [LoopVQ: Sub 1-bit KV Cache Quantization for Looped Transformers](http://arxiv.org/abs/2611.05304v1) | Cheong, Yoo | Exploits the repeated-layer structure of looped transformers to quantize KV caches below 1 bit per element. Complements looped-architecture efficiency gains with a memory-side optimization. |
| [Suppressing Misaligned Behaviors in Language Models, Durably](http://arxiv.org/abs/2611.05358v1) | He, Jiang, Zhang et al. | Builds two "model organisms" with deliberately instilled misalignment (differing in triggers) and studies how effectively interventions suppress the behavior durably across training stages. Reframes alignment as a persistent-suppression problem rather than a one-shot fix. |
| [Knowingly Wrong: Training LLMs Against Their Own Beliefs can Induce Misalignment](http://arxiv.org/abs/2611.05287v1) | Villegas-Suarez, Freire, Venkatesh et al. | Demonstrates that fine-tuning on data contradicting a model's own beliefs — not just overtly harmful data — can induce broad misalignment. Widens the realistic risk surface for fine-tuning pipelines considerably. |
| [The Geometry of Ignorance: Detecting Suppressed Knowledge in LLMs through Activation Fragmentation](http://arxiv.org/abs/2611.05272v1) | Peleg, Alsheich, Ashuach et al. | Proposes measuring geometric coherence of activation "anon-vectors" to detect knowledge a model refuses to express. Offers an interpretability tool for distinguishing ignorance from suppression. |
| [A Circuit for In-Context Learning](http://arxiv.org/abs/2611.05344v1) | Gao, Gao | Identifies candidate circuit mechanisms enabling in-context learning of arbitrary input-label mappings within a single forward pass. Contributes mechanistic grounding to a central but poorly understood LLM capability. |

### 🤖 Agents & Reasoning

| Paper | Authors | Summary |
| :--- | :--- | :--- |
| [Adversarial Speculative Reasoning Improves Long Horizon Agents](http://arxiv.org/abs/2611.05345v1) | Luo, Fang, Yang et al. | Argues speculative decoding is not behavior-neutral and repurposes draft models as adversarial reasoning proposers for long-horizon agents. Reframes a systems optimization as a reasoning-quality lever. |
| [MDSKILL: Learning to Internalize Skill Memory through Multi-View Distillation](http://arxiv.org/abs/2611.05313v1) | Jin, Yang, Han et al. | Uses on-policy self-distillation with multi-view supervision to internalize textual skill memories into agent policies, accelerating RL-based self-evolution. Addresses the prompt-bloat and plasticity limits of skill-memory approaches. |
| [Stabilizing On-Policy Targeted Guidance in Agentic Reinforcement Learning](http://arxiv.org/abs/2611.05266v1) | Frans, Rush, Levine et al. | Tackles instability when on-policy RL agents receive rich textual rewards from rubric-based judge models, proposing stabilized targeted guidance. Directly relevant to the judge-in-the-loop post-training paradigm. |
| [JHiMA-WebRL: Joint Hierarchical Multi-Agent RL for Long-Horizon Web Tasks](http://arxiv.org/abs/2611.05263v1) | Mondal, Wang | Decomposes long-horizon GUI web tasks into hierarchical multi-agent RL with joint high-level planning and low-level action. A structured alternative to monolithic multimodal web policies. |

### 🔧 Methods & Frameworks

| Paper | Authors | Summary |
| :--- | :--- | :--- |
| [Logarithmic Regret for Alternating Mirror Descent](http://arxiv.org/abs/2611.05243v1) | Nan, Kroer | Improves AltOMD regret bounds from O(T^{1/3})/O(T^{1/5}) to logarithmic regret in zero-sum games. A significant theoretical upgrade for a practically popular learning rule. |
| [Privacy for Free in Efficient Agnostic Halfspace Learning](http://arxiv.org/abs/2611.05208v1) | Racca, Sanyal, Singhal | Gives the first computationally efficient differentially private algorithms for agnostic halfspace learning across improper, proper, and semi-private settings. Shows privacy can come at essentially no cost in a canonical learning problem. |
| [Universal Hardness From Masking Channels](http://arxiv.org/abs/2611.05296v1) | Gopalan, Prabhakaran, Tankala | Proves a broad learning-theoretic generalization of Impagliazzo's hardcore lemma via masking channels. Foundational result likely to reshape average-case hardness arguments in learning theory. |
| [Gaussian Partitioning for Reliable Learning of Multi-Index Models](http://arxiv.org/abs/2611.05264v1) | Diakonikolas, Iakovidis, Kane et al. | Introduces Gaussian partitioning, splitting space into thin polyhedral strips where conditional distributions become tractable, enabling reliable multi-index model learning. A new algorithmic primitive for Gaussian-smooth distribution learning. |
| [MedPhysBench](http://arxiv.org/abs/2611.05340v1) | Nusrat | Benchmarks LLMs on radiotherapy/diagnostic medical physics calculations and error detection with ten seeded generators. A high-stakes, verifiable evaluation domain where hallucinated arithmetic has direct clinical consequences. |
| [PLANTEUR: A Crop-Type Segmentation Benchmark for Tropical Regions](http://arxiv.org/abs/2611.05223v1) | Wolters, Tseng, Bastani et al. | Provides a multi-sensor (Sentinel-1/2, Landsat-8) parcel-labeled crop segmentation benchmark spanning six tropical regions. Fills a major geographic gap in geospatial ML evaluation. |

### 📊 Applications

| Paper | Authors | Summary |
| :--- | :--- | :--- |
| [DreamX: Video Diffusion Model is a Universal Latent Action Learner](http://arxiv.org/abs/2611.05377v1) | Kim, Kim, Kim et al. | Learns embodiment- and appearance-invariant latent action representations from video using a video diffusion model. A universal action space would substantially ease cross-embodiment robot policy transfer. |
| [PolicyIK: High-Precision Inverse Kinematics for Robot Arms with RL](http://arxiv.org/abs/2611.05372v1) | Liu, Liu, Dong et al. | Applies reinforcement learning to solve precise inverse kinematics under redundancy and singularities, targeting the IK layer that VLA policies depend on for cross-embodiment transfer. Practical infrastructure for robot learning stacks. |
| [iGC: Antibody Optimization via RL over a Learned Somatic Hypermutation Prior](http://arxiv.org/abs/2611.05270v1) | Nguyen, Alberstein, Nowara et al. | Models affinity maturation by learning a somatic hypermutation prior and optimizing antibodies with RL, capturing biological plausibility alongside binding objectives. A strong example of RL for real-world molecular design. |
| [GSBR: Geometric Support Behavior Regularization for Online RL](http://arxiv.org/abs/2611.05295v1) | Xu, Berg, Ke et al. | Regularizes online RL toward imitation-learned diffusion/flow base policies via geometric support constraints, avoiding over-restriction. Improves the stability-efficiency tradeoff in robot RL fine-tuning. |

---

## 3. Research Trend Signal

Three converging signals stand out. First, **efficiency is being re-architected, not just optimized**: looped transformers, sub-1-bit KV caches, and context compaction all restructure *where* compute and memory are spent rather than tuning existing stacks. Second, **agents are becoming self-evolving systems** — skill memories distilled into weights, adversarial drafters as reasoners, and judge-based rubric rewards — with stabilization of these pipelines emerging as its own research problem. Third, **alignment research is shifting from behavior to latent state**: durable suppression, belief-contradicting fine-tuning, and activation-geometry probes all treat misalignment as something that can hide and re-emerge. Underneath, a notably strong theory day (regret bounds, privacy for free, hardcore-lemma generalizations) suggests the theoretical foundations of learning are keeping pace with the empirical frontier.

---

## 4. Worth Deep Reading

1. **[You don't need long context](http://arxiv.org/abs/2611.05306v1)** — If the compaction results generalize, this directly challenges a dominant investment direction (long-context training and inference). The per-token cost asymmetry (up to 7x) makes the practical stakes concrete; read closely for the task suite and failure cases.

2. **[Suppressing Misaligned Behaviors in Language Models, Durably](http://arxiv.org/abs/2611.05358v1)** — The "model organisms" methodology and the durability framing address the central open question in alignment: whether interventions persist through subsequent training. Should be read alongside *Knowingly Wrong* for a fuller picture of misalignment persistence and induction.

3. **[Logarithmic Regret for Alternating Mirror Descent](http://arxiv.org/abs/2611.05243v1)** — A clean, significant theoretical advance (from polynomial to logarithmic regret) for a widely used game-solving algorithm; the modified-energy-function analysis is likely to be reused well beyond zero-sum games.

*Digest compiled from 50 ArXiv submissions (cs.AI, cs.CL, cs.LG and cross-lists), 2026-11-05.*

---
*This digest is auto-generated by [agents-radar](https://github.com/zhangzuxuan2023-hub/agents-radar).*