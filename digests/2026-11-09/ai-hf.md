# Hugging Face 热门模型周报 2026-11-09

> 数据来源: [Hugging Face Hub](https://huggingface.co/) | 共 30 个模型 | 生成时间: 2026-11-08 23:13 UTC

---

# 🤗 Hugging Face 热门模型摘要
**截至 2026-11-09 · 按每周点赞数排序的 30 个模型**

---

## 1. 今日亮点

本周榜单由两股力量主导：**Qwen 的 Qwen3.8-27B**（19,410 个赞，770 万次下载），它支撑起一个庞大的衍生模型生态；以及 **Lightricks 的 LTX-2.5**（11,123 个赞，266 万次下载），本轮周期中脱颖而出的视频生成发布。**Meta 的 SAM 3**（6,104 个赞）表明分割与视觉基础模型的势头持续不减，而 **DeepSeek-V4.1-Flash** 和 **Mistral-Large-4-1T-A52B** 则推动开放权重多模态大模型的前沿。一个显著的模式是围绕 Qwen3.8 的衍生模型活动之庞大——从“异端”无审查微调，到 3D 编程专用模型和 GGUF 量化——使其成为本周事实上的基础模型标准。与此同时，可直接用于 ComfyUI 的单文件扩散模型（FLUX-3、PrunaAI 的 P-H3）证实，创意工具链如今已成为一流的发行渠道。

---

## 2. 热门模型

### 🧠 语言模型（LLM、对话模型、指令微调）

| 模型 | 作者 | 点赞数 | 下载量 | 摘要 |
| :--- | :--- | ---: | ---: | :--- |
| [Qwen3.8-27B](https://huggingface.co/Qwen/Qwen3.8-27B) | Qwen | 19,410 | 7,668,187 | Qwen 的旗舰级 27B 多模态对话模型，以巨大优势登顶榜单。其庞大的下载量使其成为本周所有人都在其之上构建的基础模型。 |
| [Kimi-K3.1-Pro](https://huggingface.co/moonshotai/Kimi-K3.1-Pro) | moonshotai | 1,725 | 25,569 | Moonshot AI 的压缩张量图文转文本生成模型。它作为多模态对话领域中紧凑、高效的前沿竞争者而走红。 |
| [Mistral-Large-4-1T-A52B](https://huggingface.co/mistralai/Mistral-Large-4-1T-A52B) | mistralai | 1,638 | 11,628 | Mistral 的万亿参数 MoE（520 亿激活）多模态大模型，支持 vLLM/SGLang。它代表了向开放权重前沿规模的一次激进推进。 |
| [LFM3-1.7B](https://huggingface.co/LiquidAI/LFM3-1.7B) | LiquidAI | 1,557 | 73,562 | Liquid AI 的 LFM3 系列中紧凑的 1.7B 文本生成模型。它作为边缘和本地部署的轻量高效选项正获得关注。 |
| [Ripple-65B-Preview](https://huggingface.co/ricursiveAI/Ripple-65B-Preview) | ricursiveAI | 399 | 5,087 | 一个带有自定义代码和 Apache-2.0 许可证的 65B 预览模型。它作为一个全新的、采用宽松许可证的大模型新入者而受到关注。 |
| [Arete-27B](https://huggingface.co/NousResearch/Arete-27B) | NousResearch | 280 | 1,747 | Nous Research 基于 Qwen3.5 架构构建的监督微调模型。它在寻求精心策划、面向研究的 27B 助手的研究人员中走红。 |
| [ERNIE-5.5-Flash-Base](https://huggingface.co/baidu/ERNIE-5.5-Flash-Base) | baidu | 230 | 1,400 | 百度的 ERNIE 5.5 MoE 文本生成基础模型。它标志着这家中国主要实验室持续开放的发布节奏，尽管目前下载量较低。 |

### 🎨 多模态与生成（图像、视频、音频、文本到 X）

| 模型 | 作者 | 点赞数 | 下载量 | 摘要 |
| :--- | :--- | ---: | ---: | :--- |
| [LTX-2.5](https://huggingface.co/Lightricks/LTX-2.5) | Lightricks | 11,123 | 2,661,026 | Lightricks 的单文件扩散模型，涵盖图像到视频、文本到视频和视频到视频。其 266 万次下载使其成为本周主导性的视频生成发布。 |
| [sam3](https://huggingface.co/facebook/sam3) | facebook | 6,104 | 1,982,669 | Meta 的第三代 Segment Anything 模型，用于掩码生成和视频分割。近 200 万次下载证实了 SAM 作为核心视觉基础设施的地位。 |
| [Qwen-Image-2.1-Turbo-Quantized](https://huggingface.co/abenzerps/Qwen-Image-2.1-Turbo-Quantized) | abenzerps | 5,725 | 76,804 | Qwen-Image 2.1 的 GGUF 量化、可直接用于 ComfyUI 的 Turbo 变体。它因将快速、易用的图像生成带到消费级硬件上而走红。 |
| [DeepSeek-V4.1-Flash-1017](https://huggingface.co/deepseek-ai/DeepSeek-V4.1-Flash-1017) | deepseek-ai | 5,407 | 295,747 | DeepSeek 官方的 V4.1 Flash 多模态模型，用于文本和图文转文本。它是一波社区量化和编程变体的上游来源。 |
| [NVIDIA-Nemotron-3.5-Transcribe-30B-A4B](https://huggingface.co/nvidia/NVIDIA-Nemotron-3.5-Transcribe-30B-A4B) | nvidia | 3,107 | 67,230 | NVIDIA 的 NeMo SpeechLM 系列 30B（A4B 激活）语音识别模型。它作为具有高效 MoE 路由的强开放 ASR 选项而走红。 |
| [flux-3-image-klein-comfy](https://huggingface.co/black-forest-labs/flux-3-image-klein-comfy) | black-forest-labs | 2,753 | 1,167,959 | Black Forest Labs 的 FLUX-3 单文件模型，为 ComfyUI 图像生成和编辑而打包。超过 110 万次下载表明 FLUX 仍是创意工作流的主力。 |
| [Pika-H3-Flex](https://huggingface.co/Pika-Labs/Pika-H3-Flex) | Pika-Labs | 516 | 1,083 | Pika 基于 diffusers 的图像到视频模型，支持音视频生成。它是一个值得注意的、将视频与同步音频生成相结合的条目。 |
| [P-H3-Image-Comfy](https://huggingface.co/PrunaAI/P-H3-Image-Comfy) | PrunaAI | 250 | 625,136 | PrunaAI 的 ComfyUI 优化文本到图像和图像到图像单文件模型。其 62.5 万次下载凸显了对优化、开箱即用图像流水线的需求。 |
| [P-H3-Image](https://huggingface.co/PrunaAI/P-H3-Image) | PrunaAI | 170 | 893 | PrunaAI 的 P-H3 图像模型的基础 diffusers 发布版，用于文本到图像和编辑。它是更受欢迎的 ComfyUI 变体的上游。 |
| [idol](https://huggingface.co/speridlabs/idol) | speridlabs | 1,369 | 22,854 | 一个 Apache-2.0 的图像到 3D 模型，使用高斯泼溅进行 3D 资产生成。它作为一个易用的开放单图像到 3D 重建工具而走红。 |
| [Viggle-Remake](https://huggingface.co/Viggle/Viggle-Remake) | Viggle | 194 | 76,308 | 一个基于 MiniMax-H3 构建的、用于角色替换的视频到视频编辑模型。它因实用、面向特效的视频编辑工作流而受到关注。 |

### 🔧 专用模型（代码、数学、医疗、嵌入）

| 模型 | 作者 | 点赞数 | 下载量 | 摘要 |
| :--- | :--- | ---: | ---: | :--- |
| [Qwen3.5-4B-3D-Coder](https://huggingface.co/ccore/Qwen3.5-4B-3D-Coder) | ccore | 916 | 254,053 | 一个专精于 Three.js 和 3D 编程的 4B 代码模型，提供 safetensors 和 GGUF 格式。其 25.4 万次下载表明对领域专用编程助手的强劲利基需求。 |
| [Qwen3.5-4B-GLB-3D-Coder](https://huggingface.co/ccore/Qwen3.5-4B-GLB-3D-Coder) | ccore | 270 | 33,641 | 一个配套的 3D 编程变体，专注于 Three.js 工作流的 GLB 输出。它通过文本生成流水线扩展了 ccore 的专用 3D 编程产品线。 |
| [DeepSeek-V4.1-Flash-1017-GSQ-RCO-Coder-GGUF](https://huggingface.co/ISTA-DASLab/DeepSeek-V4.1-Flash-1017-GSQ-RCO-Coder-GGUF) | ISTA-DASLab | 205 | 258,719 | DeepSeek-V4.1-Flash 的 GGUF 编程导向量化版本，支持视觉。其 25.8 万次下载反映了对高效、代码调优的 DeepSeek 衍生模型的兴趣。 |
| [GLM-5.3-SLIM-GGUF](https://huggingface.co/Scantist/GLM-5.3-SLIM-GGUF) | Scantist | 1,173 | 4,938 | 一个可直接用于 llama.cpp 的 GLM-5.3 GGUF，具有 MoE 和神经架构搜索调优。它作为一个精简、可本地运行的 GLM 变体而走红。 |

### 📦 微调与量化（社区微调、GGUF、AWQ）

| 模型 | 作者 | 点赞数 | 下载量 | 摘要 |
| :--- | :--- | ---: | ---: | :--- |
| [Qwen3.8-27B-TURBO-Fable-Cold-Fusion-...-GGUF](https://huggingface.co/DavidAU/Qwen3.8-27B-TURBO-Fable-Cold-Fusion-735-882-Heretic-Uncensored-NEO-CODER-MAX-MTP-GGUF) | DavidAU | 2,706 | 1,553,252 | 一个重度合并、无审查的 Qwen3.8-27B GGUF 微调，面向编程用例。其 155 万次下载使其成为本周最突出的社区衍生模型。 |
| [Qwen3.8-Flash-Next-GGUF](https://huggingface.co/unsloth/Qwen3.8-Flash-Next-GGUF) | unsloth | 1,532 | 1,548,621 | Unsloth 的 Qwen3.8-Flash-Next GGUF 量化，用于图文转文本。它与基础 Qwen 的热潮相辅相成，大规模服务本地推理用户。 |
| [DeepSeek-V4-Pro-Qwen3.5-9B-UNCENSORED-heretic](https://huggingface.co/xtools-at/DeepSeek-V4-Pro-Qwen3.5-9B-UNCENSORED-heretic) | xtools-at | 828 | 7,088 | 一个无审查的“异端”微调，将 DeepSeek-V4-Pro 的命名与 Qwen3.5-9B 基础相结合。它在无审查模型社区中因文本和图文任务而走红。 |
| [DeepSeek-V4.1-Flash-1017-Uncensored-GSQ-RCO-GGUF](https://huggingface.co/AtomicChat/DeepSeek-V4.1-Flash-1017-Uncensored-GSQ-RCO-GGUF) | AtomicChat | 390 | 130,020 | DeepSeek-V4.1-Flash 的无审查 GGUF 量化。其 13 万次下载表明对未过滤、可本地运行的 DeepSeek 变体的稳定需求。 |
| [numbat](https://huggingface.co/burner22/numbat) | burner22 | 391 | 10,197 | 一个 8 位、modelopt 优化的模型，带有自定义代码。它作为一个实验性、注重效率的社区发布而引发兴趣。 |
| [Sulphur-3](https://huggingface.co/FusionCow/Sulphur-3) | FusionCow | 418 | 1,003 | 一个基于 MiniMaxAI/MiniMax-H3 构建的微调。它是一个虽小但值得注意的衍生模型，将 MiniMax-H3 系列延伸到社区领域。 |
| [TwIL-8B](https://huggingface.co/webAI-Official/TwIL-8B) | webAI-Official | 231 | 22,737 | 一个基于 Granite 架构构建的 8B 文本生成模型，提供 transformers 和 GGUF 格式。它作为一个紧凑、采用宽松许可证的指令模型而走红。 |
| [DeepSeek-V4-Pro-Qwen3.5-9B-UNCENSORED-heretic-i1-GGUF](https://huggingface.co/mradermacher/DeepSeek-V4-Pro-Qwen3.5-9B-UNCENSORED-heretic-i1-GGUF) | mradermacher | 183 | 136,706 | mradermacher 对 xtools-at 异端微调的 i1-GGUF 量化。其 13.6 万次下载表明量化机构正在快速打包流行的无审查发布。 |

---

## 3. 生态信号

**Qwen 已成为生态的重心。** Qwen3.8-27B 的主导地位不仅仅在于其自身的 770 万次下载——还在于它所产生的引力：编程变体、“异端”无审查合并、GGUF 量化，甚至 Flash-Next 兄弟模型都追溯到 Qwen3.5/3.8 谱系。这 mirrors 了 Llama 曾经作为默认基础模型的方式，并表明 Qwen 现在在开放权重世界中占据了这一角色。

**开放权重前沿规模正在加速。** Mistral 的万亿参数 MoE、DeepSeek-V4.1-Flash、Kimi-K3.1-Pro 和 ERNIE-5.5 都在本周期公开亮相——这是一个明确的趋势，即实验室发布前沿级多模态权重而非将其封闭。与此同时，生成媒体（LTX-2.5、FLUX-3、Pika-H3）和视觉基础设施（SAM 3）表明，多模态生成是下载量集中的领域。

**量化与微调仍是生态的循环系统。** Unsloth、mradermacher、DavidAU 和 ISTA-DASLab 通过将基础模型重新打包为 GGUF 和无审查/异端变体，共同推动了数百万次下载——在原始下载量上往往超过原版。可直接用于 ComfyUI 的单文件发布如今是一条并行的发行轨道，证实了工具兼容性对采用率的推动不亚于基准性能。

---

## 4. 值得探索

1. **[Qwen3.8-27B](https://huggingface.co/Qwen/Qwen3.8-27B)** —— 本周无可争议的锚点。研究它对于理解衍生态至关重要：几乎所有其他热门微调、量化或编程变体都建立在这一谱系之上，使其成为榜单上最具影响力的单一发布。

2. **[LTX-2.5](https://huggingface.co/Lightricks/LTX-2.5)** —— 生成视频中最清晰的信号。凭借 266 万次下载和在一个单文件扩散包中覆盖图像到视频、文本到视频和视频到视频，如果你从事视频生成工作，它是值得基准测试的模型。

3. **[sam3](https://huggingface.co/facebook/sam3)** —— SAM 3 近 200 万次下载和视频分割能力使其成为基础基础设施而非新奇事物。鉴于其可能作为众多应用的下游依赖，值得任何构建视觉流水线的人研究。

---

*本摘要根据截至 2026-11-09 的 Hugging Face Hub 热门数据编制。所有链接均指向原始模型仓库。*

---
*本日报由 [agents-radar](https://github.com/zhangzuxuan2023-hub/agents-radar) 自动生成。*