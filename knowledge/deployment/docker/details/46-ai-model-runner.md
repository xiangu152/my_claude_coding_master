---
title: "Docker AI - 模型运行器"
source: "https://docs.docker.com/ai/model-runner/"
version: "latest"
---

# Docker AI - 模型运行器

> 原始文档来源：https://docs.docker.com/ai/model-runner/

---

Home
/
Manuals
/
Model Runner
Docker Model Runner
Ask Gordon
Copy Markdown
View Markdown
Requires:
Docker Engine or Docker Desktop (Windows) 4.41+ or Docker Desktop (MacOS) 4.40+
For:
See Requirements section below

Docker Model Runner (DMR) makes it easy to manage, run, and deploy AI models using Docker. Designed for developers, Docker Model Runner streamlines the process of pulling, running, and serving large language models (LLMs) and other AI models directly from Docker Hub, any OCI-compliant registry, or Hugging Face.

With seamless integration into Docker Desktop and Docker Engine, you can serve models via OpenAI and Ollama-compatible APIs, package GGUF files as OCI Artifacts, and interact with models from both the command line and graphical interface.

Whether you're building generative AI applications, experimenting with machine learning workflows, or integrating AI into your software development lifecycle, Docker Model Runner provides a consistent, secure, and efficient way to work with AI models locally.

Key features
Pull and push models to and from Docker Hub or any OCI-compliant registry
Pull models from Hugging Face
Serve models on OpenAI and Ollama-compatible APIs for easy integration with existing apps
Support for llama.cpp, vLLM, and Diffusers inference engines (vLLM and Diffusers on Linux with NVIDIA GPUs)
Generate images from text prompts using Stable Diffusion models with the Diffusers backend
Package GGUF and Safetensors files as OCI Artifacts and publish them to any Container Registry
Run and interact with AI models directly from the command line or from the Docker Desktop GUI
Connect to AI coding tools like Cline, Continue, Cursor, and Aider
Configure context size and model parameters to tune performance
Set up Open WebUI for a ChatGPT-like web interface
Manage local models and display logs
Display prompt and response details
Conversational context support for multi-turn interactions
Requirements

Docker Model Runner is supported on the following platforms:

Windows MacOS Linux

Windows(amd64):

NVIDIA GPUs
NVIDIA drivers 576.57+

Windows(arm64):

OpenCL for Adreno

Qualcomm Adreno GPU (6xx series and later)

Note

Some llama.cpp features might not be fully supported on the 6xx series.

How Docker Model Runner works

Models are pulled from Docker Hub, an OCI-compliant registry, or Hugging Face the first time you use them and are stored locally. They load into memory only at runtime when a request is made, and unload when not in use to optimize resources. Because models can be large, the initial pull may take some time. After that, they're cached locally for faster access. You can interact with the model using OpenAI and Ollama-compatible APIs.

Inference engines

Docker Model Runner supports three inference engines:

Engine	Best for	Model format
llama.cpp	Local development, resource efficiency	GGUF (quantized)
vLLM	Production, high throughput	Safetensors
Diffusers	Image generation (Stable Diffusion)	Safetensors

llama.cpp is the default engine and works on all platforms. vLLM requires NVIDIA GPUs and is supported on Linux x86_64 and Windows with WSL2. Diffusers enables image generation and requires NVIDIA GPUs on Linux (x86_64 or ARM64). See Inference engines for detailed comparison and setup.

Context size

Models have a configurable context size (context length) that determines how many tokens they can process. The default varies by model but is typically 2,048-8,192 tokens. You can adjust this per-model:

$ docker model configure --context-size 8192 ai/qwen2.5-coder

See Configuration options for details on context size and other parameters.

Tip

Using Testcontainers or Docker Compose? Testcontainers for Java and Go, and Docker Compose support Docker Model Runner.

Security and isolation
Execution environment

Docker Model Runner isolates inference engines from your host:

On Linux, Docker Model Runner and its inference engines, such as Diffusers, run inside a container, which provides the isolation boundary.
On macOS and Windows, the engines don't run inside a container, so Docker Model Runner runs them in a sandboxed environment (seatbelt/sandbox-exec and Job Objects respectively)
Networking

The Model Runner API is not authenticated. Any client that can reach it, including other containers on the same Docker network, can pull, load, and run models, and send inference requests.

Known issues
docker model is not recognised

If you run a Docker Model Runner command and see:

docker: 'model' is not a docker command

It means Docker can't find the plugin because it's not in the expected CLI plugins directory.

To fix this, create a symlink so Docker can detect it:

$ ln -s /Applications/Docker.app/Contents/Resources/cli-plugins/docker-model ~/.docker/cli-plugins/docker-model

Once linked, rerun the command.

Privacy and data collection

Docker Model Runner respects your privacy settings in Docker Desktop. Data collection is controlled by the Send usage statistics setting:

Disabled: No usage data is collected
Enabled: Only minimal, non-personal data is collected:
Model names (via HEAD requests to Docker Hub)
User agent information
Whether requests originate from the host or containers

When using Docker Model Runner with Docker Engine, HEAD requests to Docker Hub are made to track model names, regardless of any settings.

No prompt content, responses, or personally identifiable information is ever collected.

Share feedback

Thanks for trying out Docker Model Runner. To report bugs or request features, open an issue on GitHub. You can also give feedback through the Give feedback link next to the Enable Docker Model Runner setting.

Next steps
Get started with DMR - Enable DMR and run your first model
API reference - OpenAI and Ollama-compatible API documentation
Configuration options - Context size and runtime parameters
Inference engines - llama.cpp, vLLM, and Diffusers details
IDE integrations - Connect Cline, Continue, Cursor, and more
Open WebUI integration - Set up a web chat interface
