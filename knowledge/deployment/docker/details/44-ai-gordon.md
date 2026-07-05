---
title: "Docker AI - Gordon 助手"
source: "https://docs.docker.com/ai/gordon/"
version: "latest"
---

# Docker AI - Gordon 助手

> 原始文档来源：https://docs.docker.com/ai/gordon/

---

Home
/
Manuals
/
Gordon
Gordon
Ask Gordon
Copy Markdown
View Markdown
Requires:
Docker Desktop 4.74.0 or later

Gordon is an AI-powered assistant that takes action on your Docker workflows. It analyzes your environment, proposes solutions, and executes commands with your permission.

What Gordon does

Gordon takes action to help you with Docker tasks:

Explains Docker concepts and commands
Searches Docker documentation and web resources for solutions
Writes and modifies Dockerfiles following best practices
Debugs container failures by reading logs and proposing fixes
Manages containers, images, volumes, and networks

Gordon proposes every action before executing. You approve what it does.

Where to use Gordon

Gordon is available on four surfaces:

Open the Gordon view from the Docker Desktop sidebar to run Docker commands with your approval. See Using Gordon in Docker Desktop.
Run docker ai in the terminal to use the full assistant from the command line. See Using Gordon via CLI.
Select the Gordon icon on any repository page at hub.docker.com to ask about a repository's images, tags, and metadata. Hand off to Docker Desktop to take action.
Select the Gordon icon on any page at docs.docker.com to ask Docker questions.

Docker Desktop and the CLI count against your Gordon plan's usage limits. Gordon on Docker Hub and docs.docker.com is free and does not require a Docker account or a Docker Desktop install. It has its own shared public usage limit and does not access your Docker environment.

Get started
Prerequisites

Before you begin:

Docker Desktop 4.74 or later
Sign in to your Docker account
Note

Gordon is enabled by default for signed-in Docker users. If your account belongs to an organization with a Business subscription, access requires two additional steps:

Contact Docker Support to activate Gordon for your organization. Docker will confirm when activation is complete.
Once confirmed, an organization administrator must turn on Gordon via Settings Management. Set Enable Gordon to Enabled or Always enabled. Ensure all Settings Management prerequisites are met for the setting to take effect on Docker Desktop clients.
Quick start
Docker Desktop CLI

Open Docker Desktop.

Select Gordon in the sidebar.

Select your project directory.

Type a question: "What containers are running?"

Review Gordon's proposed actions and approve.

Permissions

By default, Gordon asks for approval before executing actions. You can approve individual actions or allow all actions for the current session.

Permissions reset for each session. To configure default permissions or enable auto-approve mode, see Permissions.

Try these examples

Container inspection:

$ docker ai "show me logs from my nginx container"

Dockerfile review:

$ docker ai "review my Dockerfile for best practices"

Image management:

$ docker ai "list my local images and their sizes"
