# Summary of Open Source AI Tools, Models, and Agents (Linux/Windows)

## Execution and Interface Tools
List of `TUI` or `GUI` interface tools for local large language models (LLMs).

| Tool                      | Type      | Description                                                                        | Website                                            | GitHub                                             |
|:--------------------------|:----------|:-----------------------------------------------------------------------------------|:---------------------------------------------------|:---------------------------------------------------|
| **Ollama**                | Engine    | Command-line tool to run models easily. Supports Windows, Linux, macOS.            | https://ollama.com                                 | https://github.com/ollama/ollama                   |
| **LM Studio**             | Interface | GUI to search, download, and run GGUF models locally.                              | https://lmstudio.ai                                | https://github.com/lmstudio-ai                     |
| **KoboldCPP**             | Executor  | Single-binary executor optimized for GGUF, lightweight and performant.             | https://github.com/LostRuins/koboldcpp             | https://github.com/LostRuins/koboldcpp             |
| **Text Generation WebUI** | Interface | Advanced web interface (Oobabooga) supporting numerous backends and extensions.    | https://github.com/oobabooga/text-generation-webui | https://github.com/oobabooga/text-generation-webui |
| **Open WebUI**            | Interface | ChatGPT-like interface connecting to Ollama or other API backends.                 | https://openwebui.com                              | https://github.com/open-webui/open-webui           |
| **LocalAI**               | Server    | Open source drop-in replacement for the OpenAI API, compatible with many backends. | https://localai.io                                 | https://github.com/mudler/LocalAI                  |
| **Jan**                   | Interface | Desktop alternative to LM Studio, fully local and open source.                     | https://jan.ai                                     | https://github.com/janhq/jan                       |
| **OpenCode**              | Interface | Desktop interface to run local LLMs via Ollama or compatible APIs.                 | https://github.com/OpenCodeAI/opencode             | https://github.com/OpenCodeAI/opencode             |


## Open Source Language Models (LLMs)
Recommended format for local use: `GGUF` (via Ollama, LM Studio, etc.)

| Model                      | Publisher  | Size (Params)         | Status / Use Case                                                                        | Hugging Face Link                  |
|:---------------------------|:-----------|:----------------------|:-----------------------------------------------------------------------------------------|:-----------------------------------|
| **Llama 3.1 / 3.2**        | Meta       | 1B, 3B, 8B, 70B, 405B | Current SOTA, General purpose, Coding                                                    | https://huggingface.co/meta-llama  |
| **Gemma 2 / Gemma 3**      | Google     | 2B, 9B, 27B, 32B      | Lightweight, High efficiency                                                             | https://huggingface.co/google      |
| **Mistral / Mixtral**      | Mistral AI | 7B, 8x7B, 8x22B       | Efficiency, Multilingual, Reasoning                                                      | https://huggingface.co/mistralai   |
| **Qwen 2.5 / 3**           | Alibaba    | 0.5B to 72B           | Coding, Math, Multilingual                                                               | https://huggingface.co/Qwen        |
| **Phi-3 / Phi-4**          | Microsoft  | 3.8B, 14B, 120B       | Small but capable, Education                                                             | https://huggingface.co/microsoft   |
| **Falcon 2 / 3**           | TII UAE    | 7B, 11B, 180B         | General purpose, Coding                                                                  | https://huggingface.co/tiiuae      |
| **StarCoder2 / 3**         | BigCode    | 3B, 7B, 15B, 20B      | Code generation and completion                                                           | https://huggingface.co/bigcode     |
| **DeepSeek-Coder**         | DeepSeek   | 1.3B, 6.7B, 33B       | Specialized in Coding                                                                    | https://huggingface.co/deepseek-ai |
| **Gemma 4 (Fork/Preview)** | Community  | Variable              | *Future/Community:* Anticipated next-gen Google model (Unofficial).                      | Search "Gemma 4" on HuggingFace    |
| **Qwen 3.6 (Preview)**     | Community  | Variable              | *Future/Community:* Anticipated successor to Qwen 3 (Unofficial).                        | Search "Qwen 3.6" on HuggingFace   |
| **GPT-OSS**                | Community  | Variable              | *Future/Community:* Open Source forks mimicking GPT architecture (e.g., OpenChat, Orca). | Search "GPT-OSS" on HuggingFace    |


## AI Agents and Workflows
Tools for orchestrating complex tasks (search, code execution, tool usage)

| Tool                | Type      | Description                                                                        | Website                                     | GitHub                                       |
|:--------------------|:----------|:-----------------------------------------------------------------------------------|:--------------------------------------------|:---------------------------------------------|
| **LangChain**       | Framework | Python/JS library for building applications with LLMs and agents.                  | https://www.langchain.com                   | https://github.com/langchain-ai/langchain    |
| **LlamaIndex**      | Framework | Framework for connecting LLMs to custom data (RAG).                                | https://www.llamaindex.ai                   | https://github.com/run-llama/llama_index     |
| **AutoGen**         | Framework | Microsoft framework for creating collaborative multi-agent conversational systems. | https://microsoft.github.io/autogen/        | https://github.com/microsoft/autogen         |
| **CrewAI**          | Framework | Framework for orchestrating role-playing autonomous agents.                        | https://www.crewai.com                      | https://github.com/joaomdmoura/crewai        |
| **Haystack**        | Framework | Modular framework for semantic search and RAG applications.                        | https://haystack.deepset.ai                 | https://github.com/deepset-ai/haystack       |
| **Semantic Kernel** | SDK       | Microsoft SDK (C#, Python, Java, JS) for integrating AI into enterprise apps.      | https://learn.microsoft.com/semantic-kernel | https://github.com/microsoft/semantic-kernel |


## Resources for Downloading Models
- **Hugging Face**: The primary repository for models and datasets. (https://huggingface.co)
- **Civitai**: Specialized in image generation models (Stable Diffusion) and some LLMs. (https://civitai.com)
- **Papers With Code**: Tracks state-of-the-art models with associated code. (https://paperswithcode.com)
- **TheBloke / Bartowski**: Popular users on Hugging Face who specialize in converting models to GGUF format for local use.

---

# LLM Parameters
LLM parameters are the settings that control and optimize a large language model’s (LLM) output and behavior.
Trainable parameters include weights and biases and are configured as a large language model (LLM) learns from its training dataset.
Hyperparameters are external to the model, guiding its learning process, determining its structure and shaping its output.

| Parameter             | What It Does                          | Low Value Effect             | High Value Effect                 | Typical Range             |
|:----------------------|:--------------------------------------|:-----------------------------|:----------------------------------|:--------------------------|
| **Temperature**       | Randomness / Creativity               | Safe, factual, repetitive    | Creative, diverse, risky          | 0.0 – 2.0                 |
| **Top P** (Nucleus)   | Cumulative Probability Cutoff         | Only top words considered    | Broader word selection            | 0.1 – 1.0 (Std: 0.9)      |
| **Top K**             | Fixed Number of Candidates            | Restricted to top K words    | Selects from top K words          | 10 – 100 (Std: 40)        |
| **Frequency Penalty** | Reduces repetition based on frequency | No penalty on repeated words | Strongly penalizes frequent words | -2.0 – 2.0 (Std: 0.0–0.5) |
| **Presence Penalty**  | Penalizes new topics/repetition       | No penalty on new topics     | Discourages repeating topics      | -2.0 – 2.0 (Std: 0.0–0.5) |
| **Repeat Penalty**    | Specific to local GGUF tools          | No penalty                   | Discourages exact phrase loops    | 1.0 – 1.3 (Std: 1.1)      |
| **Max Tokens**        | Output Length Limit                   | Short answers                | Long, detailed answers            | 100 – 128k+               |
| **Stop Sequences**    | Manual Stop Triggers                  | N/A                          | Stops generation on specific text | Custom strings            |

> **Note:** A **Token** is a fragment of a word (e.g., "running" = `run` + `ning`). **Context Window** is the model's total memory limit for input + output.


## Quick Reference for Local Tools (Ollama/LM Studio)
*   **Temperature:** Controls creativity.
*   **Top P:** Controls vocabulary diversity.
*   **Repeat Penalty:** Essential for local models to stop them from looping.
*   **Context Window:** The total memory size (e.g., 4k, 8k, 32k tokens).

More information : [What are LLM parameters?](https://www.ibm.com/think/topics/llm-parameters).


# LLM Quantization
**Quantization** is the process of reducing the precision of the numbers (weights) used to store a model.
This shrinks the model size and reduces RAM/VRAM usage significantly, allowing large models to run on consumer hardware with minimal loss in intelligence.


## Quantization Levels (GGUF Format)
**Quantization levels** in Large Language Models (LLMs) refer to the bit-depth used to represent weights and activations,
balancing model size, inference speed, and accuracy. Lower bit counts significantly reduce memory requirements and increase throughput but may incur accuracy losses,
while higher bit counts preserve quality at the cost of efficiency.

| Format              | Bits | Size (Approx. for 7B Model) | Quality      | Best Use Case                                       |
|:--------------------|:-----|:----------------------------|:-------------|:----------------------------------------------------|
| **FP16** (Original) | 16   | ~14 GB                      | Perfect      | Servers, Research, Fine-tuning                      |
| **Q8_0**            | 8    | ~8 GB                       | Excellent    | High-end GPUs, Maximum local quality                |
| **Q6_K**            | ~6   | ~6 GB                       | Very Good    | Best balance for 16GB+ RAM systems                  |
| **Q5_K_M**          | ~5   | ~5 GB                       | Good         | Recommended for 12GB+ RAM systems                   |
| **Q4_K_M**          | ~4   | ~4.5 GB                     | **Standard** | **Default choice** for most users (8-12GB RAM)      |
| **Q3_K_M**          | ~3   | ~3.5 GB                     | Moderate     | Limited RAM (6-8GB), faster inference               |
| **Q2_K**            | ~2   | ~2.5 GB                     | Low          | Very limited hardware, noticeable intelligence drop |


## How It Works
*   **Original Model (FP16):** Uses 16-bit floating-point numbers for every weight. High precision, huge file size.
*   **Quantized Model (e.g., Q4):** Compresses weights to 4-bit integers. The file becomes 3-4x smaller, and RAM usage drops proportionally.
*   **Impact:** The model "thinks" with slightly less precision, but for most tasks (chat, coding, summarization), the difference is imperceptible compared to the performance gain.


## Selection Guide
| Your Hardware                | Recommended Quantization | Why?                                             |
|:-----------------------------|:-------------------------|:-------------------------------------------------|
| **< 8 GB RAM / VRAM**        | **Q3_K_M** or **Q4_K_M** | Fits in memory; Q2 is too dumb.                  |
| **8 GB - 12 GB RAM / VRAM**  | **Q4_K_M** or **Q5_K_M** | Sweet spot for speed and quality.                |
| **16 GB - 24 GB RAM / VRAM** | **Q6_K** or **Q8_0**     | Minimal quality loss; utilizes available memory. |
| **> 24 GB RAM / VRAM**       | **FP16** (if needed)     | Only for specific research or fine-tuning tasks. |


## Where to Find It
*   **LM Studio:** Search for models ending in `.gguf`. Look for `Q4_K_M` in the filename (e.g., `Llama-3-8B-Instruct.Q4_K_M.gguf`).
*   **Hugging Face:** Filter results by "GGUF" and check the file size or filename for the quantization level.
*   **Ollama:** Usually handles quantization automatically (default is often Q4 or Q5), but you can pull specific tags if available.

**Rule of Thumb:** If you are unsure, start with **Q4_K_M**. It offers the best balance of speed, memory usage, and intelligence for local inference.
