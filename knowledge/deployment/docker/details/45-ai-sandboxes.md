---
title: "Docker AI - 沙箱环境"
source: "https://docs.docker.com/ai/sandboxes/"
version: "latest"
---

# Docker AI - 沙箱环境

> 原始文档来源：https://docs.docker.com/ai/sandboxes/

---

Home
/
Manuals
/
Docker Sandboxes
Docker Sandboxes
Ask Gordon
Copy Markdown
View Markdown

Docker Sandboxes run AI coding agents in isolated microVM sandboxes. Each sandbox gets its own Docker daemon, filesystem, and network — the agent can build containers, install packages, and modify files without touching your host system.

Note

The sbx CLI is free to use, including for commercial work. Only organization governance requires a separate paid subscription.

Organization admins can centrally manage sandbox network and filesystem policies from the Docker Admin Console, so the same rules apply uniformly across every developer's machine. Available on a separate paid subscription.

Get started

For complete system requirements, see the get started prerequisites.

Install the sbx CLI and sign in:

macOS Windows Linux (Ubuntu)
$ brew trust docker/tap

$ brew install docker/tap/sbx

$ sbx login

Then launch an agent in a sandbox:

$ cd ~/my-project

$ sbx run claude

See the get started guide for a full walkthrough, or jump to the usage guide for common patterns.

Learn more
Agents — supported agents and per-agent configuration
Customize — reusable templates and declarative kits for extending or tailoring sandboxes
Architecture — microVM isolation, workspace mounting, networking
Security — isolation model, credential handling, and network policies
CLI reference — full list of sbx commands and options
Troubleshooting — common issues and fixes
FAQ — login requirements, telemetry, etc
Feedback

Your feedback shapes what gets built next. If you run into a bug, hit a missing feature, or have a suggestion, open an issue at github.com/docker/sbx-releases/issues.
