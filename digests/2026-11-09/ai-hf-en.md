# Hugging Face Trending Models Weekly 2026-11-09

> Source: [Hugging Face Hub](https://huggingface.co/) | 30 models | Generated: 2026-11-08 23:13 UTC

---

# 🤗 Hugging Face Trending Models Digest
**As of 2026-11-09 · 30 models by weekly likes**

---

## 1. Today's Highlights

This week's leaderboard is dominated by two forces: **Qwen's Qwen3.8-27B** (19,410 likes, 7.7M downloads), which anchors an expansive ecosystem of derivatives, and **Lightricks' LTX-2.5** (11,123 likes, 2.66M downloads), the breakout video-generation release of the cycle. **Meta's SAM 3** (6,104 likes) signals continued momentum in segmentation and vision foundation models, while **DeepSeek-V4.1-Flash** and **Mistral-Large-4-1T-A52B** push the frontier of open-weight multimodal LLMs. A striking pattern is the sheer volume of derivative activity around Qwen3.8 — from "heretic" uncensored fine-tunes to 3D-coding specialists and GGUF quantizations — turning it into the de facto base-model standard this week. Meanwhile, ComfyUI-ready single-file diffusion models (FLUX-3, PrunaAI's P-H3) confirm that the creative-tooling pipeline is now a first-class distribution channel.

---

## 2. Trending Models

### 🧠 Language Models (LLMs, chat models, instruction-tuned)

| Model | Author | Likes | Downloads | Summary |
| :--- | :--- | ---: | ---: | :--- |
| [Qwen3.8-27B](https://huggingface.co/Qwen/Qwen3.8-27B) | Qwen | 19,410 | 7,668,187 | Qwen's flagship 27B multimodal conversational model, topping the board by a wide margin. Its massive download volume makes it the base model everyone else is building on this week. |
| [Kimi-K3.1-Pro](https://huggingface.co/moonshotai/Kimi-K3.1-Pro) | moonshotai | 1,725 | 25,569 | Moonshot AI's compressed-tensor image-text-to-text model for text generation. It's trending as a compact, efficient frontier contender in the multimodal chat space. |
| [Mistral-Large-4-1T-A52B](https://huggingface.co/mistralai/Mistral-Large-4-1T-A52B) | mistralai | 1,638 | 11,628 | Mistral's trillion-parameter MoE (52B active) multimodal large model with vLLM/SGLang support. It represents an aggressive open-weight push toward frontier scale. |
| [LFM3-1.7B](https://huggingface.co/LiquidAI/LFM3-1.7B) | LiquidAI | 1,557 | 73,562 | Liquid AI's compact 1.7B text-generation model in the LFM3 family. It's gaining traction as a lightweight, efficient option for edge and local deployment. |
| [Ripple-65B-Preview](https://huggingface.co/ricursiveAI/Ripple-65B-Preview) | ricursiveAI | 399 | 5,087 | A 65B preview model with custom code and an Apache-2.0 license. It's drawing attention as a fresh, permissively licensed large-model entrant. |
| [Arete-27B](https://huggingface.co/NousResearch/Arete-27B) | NousResearch | 280 | 1,747 | Nous Research's supervised fine-tune built on the Qwen3.5 architecture. It's trending among researchers seeking a curated, research-oriented 27B assistant. |
| [ERNIE-5.5-Flash-Base](https://huggingface.co/baidu/ERNIE-5.5-Flash-Base) | baidu | 230 | 1,400 | Baidu's ERNIE 5.5 MoE base model for text generation. It marks the continued open release cadence from a major Chinese lab despite low download volume so far. |

### 🎨 Multimodal & Generation (image, video, audio, text-to-X)

| Model | Author | Likes | Downloads | Summary |
| :--- | :--- | ---: | ---: | :--- |
| [LTX-2.5](https://huggingface.co/Lightricks/LTX-2.5) | Lightricks | 11,123 | 2,661,026 | Lightricks' single-file diffusion model spanning image-to-video, text-to-video, and video-to-video. Its 2.66M downloads make it the week's dominant video-generation release. |
| [sam3](https://huggingface.co/facebook/sam3) | facebook | 6,104 | 1,982,669 | Meta's third-generation Segment Anything model for mask generation and video segmentation. Nearly 2M downloads confirm SAM's status as core vision infrastructure. |
| [Qwen-Image-2.1-Turbo-Quantized](https://huggingface.co/abenzerps/Qwen-Image-2.1-Turbo-Quantized) | abenzerps | 5,725 | 76,804 | A GGUF-quantized, ComfyUI-ready Turbo variant of Qwen-Image 2.1. It's trending for bringing fast, accessible image generation to consumer hardware. |
| [DeepSeek-V4.1-Flash-1017](https://huggingface.co/deepseek-ai/DeepSeek-V4.1-Flash-1017) | deepseek-ai | 5,407 | 295,747 | DeepSeek's official V4.1 Flash multimodal model for text and image-text-to-text. It's the upstream source for a wave of community quantizations and coder variants. |
| [NVIDIA-Nemotron-3.5-Transcribe-30B-A4B](https://huggingface.co/nvidia/NVIDIA-Nemotron-3.5-Transcribe-30B-A4B) | nvidia | 3,107 | 67,230 | NVIDIA's 30B (A4B active) speech-recognition model from the NeMo SpeechLM family. It's trending as a strong open ASR option with efficient MoE routing. |
| [flux-3-image-klein-comfy](https://huggingface.co/black-forest-labs/flux-3-image-klein-comfy) | black-forest-labs | 2,753 | 1,167,959 | Black Forest Labs' FLUX-3 single-file model packaged for ComfyUI image generation and editing. Over 1.1M downloads show FLUX remains a creative-workflow staple. |
| [Pika-H3-Flex](https://huggingface.co/Pika-Labs/Pika-H3-Flex) | Pika-Labs | 516 | 1,083 | Pika's diffusers-based image-to-video model with audio-video generation support. It's a notable entry combining video with synchronized audio generation. |
| [P-H3-Image-Comfy](https://huggingface.co/PrunaAI/P-H3-Image-Comfy) | PrunaAI | 250 | 625,136 | PrunaAI's ComfyUI-optimized text-to-image and image-to-image single-file model. Its 625K downloads highlight demand for optimized, ready-to-run image pipelines. |
| [P-H3-Image](https://huggingface.co/PrunaAI/P-H3-Image) | PrunaAI | 170 | 893 | The base diffusers release of PrunaAI's P-H3 image model for text-to-image and editing. It serves as the upstream for the more popular ComfyUI variant. |
| [idol](https://huggingface.co/speridlabs/idol) | speridlabs | 1,369 | 22,854 | An Apache-2.0 image-to-3D model using Gaussian splatting for 3D asset generation. It's trending as an accessible open tool for single-image-to-3D reconstruction. |
| [Viggle-Remake](https://huggingface.co/Viggle/Viggle-Remake) | Viggle | 194 | 76,308 | A video-to-video editing model for character replacement built on MiniMax-H3. It's gaining attention for practical, effects-oriented video editing workflows. |

### 🔧 Specialized Models (code, math, medical, embeddings)

| Model | Author | Likes | Downloads | Summary |
| :--- | :--- | ---: | ---: | :--- |
| [Qwen3.5-4B-3D-Coder](https://huggingface.co/ccore/Qwen3.5-4B-3D-Coder) | ccore | 916 | 254,053 | A 4B code model specialized for Three.js and 3D coding, offered in safetensors and GGUF. Its 254K downloads show strong niche demand for domain-specific coding assistants. |
| [Qwen3.5-4B-GLB-3D-Coder](https://huggingface.co/ccore/Qwen3.5-4B-GLB-3D-Coder) | ccore | 270 | 33,641 | A companion 3D coder variant focused on GLB output for Three.js workflows. It extends ccore's specialized 3D-coding lineup with a text-generation pipeline. |
| [DeepSeek-V4.1-Flash-1017-GSQ-RCO-Coder-GGUF](https://huggingface.co/ISTA-DASLab/DeepSeek-V4.1-Flash-1017-GSQ-RCO-Coder-GGUF) | ISTA-DASLab | 205 | 258,719 | A GGUF coder-focused quantization of DeepSeek-V4.1-Flash with vision support. Its 258K downloads reflect appetite for efficient, code-tuned DeepSeek derivatives. |
| [GLM-5.3-SLIM-GGUF](https://huggingface.co/Scantist/GLM-5.3-SLIM-GGUF) | Scantist | 1,173 | 4,938 | A llama.cpp-ready GGUF of GLM-5.3 with MoE and neural-architecture-search tuning. It's trending as a slimmed-down, locally runnable GLM variant. |

### 📦 Fine-tunes & Quantizations (community fine-tunes, GGUF, AWQ)

| Model | Author | Likes | Downloads | Summary |
| :--- | :--- | ---: | ---: | :--- |
| [Qwen3.8-27B-TURBO-Fable-Cold-Fusion-...-GGUF](https://huggingface.co/DavidAU/Qwen3.8-27B-TURBO-Fable-Cold-Fusion-735-882-Heretic-Uncensored-NEO-CODER-MAX-MTP-GGUF) | DavidAU | 2,706 | 1,553,252 | A heavily merged, uncensored GGUF fine-tune of Qwen3.8-27B targeting coder use cases. Its 1.55M downloads make it the standout community derivative of the week. |
| [Qwen3.8-Flash-Next-GGUF](https://huggingface.co/unsloth/Qwen3.8-Flash-Next-GGUF) | unsloth | 1,532 | 1,548,621 | Unsloth's GGUF quantization of Qwen3.8-Flash-Next for image-text-to-text. It pairs with the base Qwen surge to serve local-inference users at scale. |
| [DeepSeek-V4-Pro-Qwen3.5-9B-UNCENSORED-heretic](https://huggingface.co/xtools-at/DeepSeek-V4-Pro-Qwen3.5-9B-UNCENSORED-heretic) | xtools-at | 828 | 7,088 | An uncensored "heretic" fine-tune blending DeepSeek-V4-Pro naming with the Qwen3.5-9B base. It's trending in the uncensored-model community for text and image-text tasks. |
| [DeepSeek-V4.1-Flash-1017-Uncensored-GSQ-RCO-GGUF](https://huggingface.co/AtomicChat/DeepSeek-V4.1-Flash-1017-Uncensored-GSQ-RCO-GGUF) | AtomicChat | 390 | 130,020 | An uncensored GGUF quantization of DeepSeek-V4.1-Flash. Its 130K downloads show steady demand for unfiltered, locally runnable DeepSeek variants. |
| [numbat](https://huggingface.co/burner22/numbat) | burner22 | 391 | 10,197 | An 8-bit, modelopt-optimized model with custom code. It's drawing interest as an experimental, efficiency-focused community release. |
| [Sulphur-3](https://huggingface.co/FusionCow/Sulphur-3) | FusionCow | 418 | 1,003 | A fine-tune built on MiniMaxAI/MiniMax-H3. It's a small but notable derivative extending the MiniMax-H3 family into community territory. |
| [TwIL-8B](https://huggingface.co/webAI-Official/TwIL-8B) | webAI-Official | 231 | 22,737 | An 8B text-generation model built on the Granite architecture, available in transformers and GGUF. It's trending as a compact, permissively licensed instruction model. |
| [DeepSeek-V4-Pro-Qwen3.5-9B-UNCENSORED-heretic-i1-GGUF](https://huggingface.co/mradermacher/DeepSeek-V4-Pro-Qwen3.5-9B-UNCENSORED-heretic-i1-GGUF) | mradermacher | 183 | 136,706 | mradermacher's i1-GGUF quantization of the xtools-at heretic fine-tune. Its 136K downloads show quantization houses rapidly packaging popular uncensored releases. |

---

## 3. Ecosystem Signal

**Qwen has become the ecosystem's center of gravity.** Qwen3.8-27B's dominance isn't just its own 7.7M downloads — it's the gravitational pull it exerts: coder variants, "heretic" uncensored merges, GGUF quantizations, and even the Flash-Next sibling all trace back to the Qwen3.5/3.8 lineage. This mirrors how Llama once functioned as the default base, and it signals that Qwen now owns that role in the open-weight world.

**Open-weight frontier scale is accelerating.** Mistral's 1T-parameter MoE, DeepSeek-V4.1-Flash, Kimi-K3.1-Pro, and ERNIE-5.5 all shipped openly this cycle — a clear trend of labs releasing frontier-class multimodal weights rather than gating them. Meanwhile, generative media (LTX-2.5, FLUX-3, Pika-H3) and vision infrastructure (SAM 3) show that multimodal generation is where the download volume concentrates.

**Quantization and fine-tuning remain the ecosystem's circulatory system.** Unsloth, mradermacher, DavidAU, and ISTA-DASLab collectively moved millions of downloads by repackaging base models into GGUF and uncensored/heretic variants — often outpacing the originals in raw download count. ComfyUI-ready single-file releases are now a parallel distribution track, confirming that tooling compatibility drives adoption as much as benchmark performance.

---

## 4. Worth Exploring

1. **[Qwen3.8-27B](https://huggingface.co/Qwen/Qwen3.8-27B)** — The week's undisputed anchor. Studying it is essential to understanding the derivative ecosystem: nearly every other trending fine-tune, quant, or coder variant builds on this lineage, making it the single most consequential release on the board.

2. **[LTX-2.5](https://huggingface.co/Lightricks/LTX-2.5)** — The clearest signal in generative video. With 2.66M downloads and coverage of image-to-video, text-to-video, and video-to-video in one single-file diffusion package, it's the model to benchmark if you're working in video generation.

3. **[sam3](https://huggingface.co/facebook/sam3)** — SAM 3's near-2M downloads and video segmentation capability make it foundational infrastructure rather than a novelty. Worth studying for anyone building vision pipelines, given its likely role as a downstream dependency across many applications.

---

*Digest compiled from Hugging Face Hub trending data as of 2026-11-09. All links point to the original model repositories.*

---
*This digest is auto-generated by [agents-radar](https://github.com/zhangzuxuan2023-hub/agents-radar).*