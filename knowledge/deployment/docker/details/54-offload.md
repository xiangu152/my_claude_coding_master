---
title: "Docker Offload"
source: "https://docs.docker.com/offload/"
version: "latest"
---

# Docker Offload

> 原始文档来源：https://docs.docker.com/offload/

---

## About Docker Offload

# About Docker Offload

Docker Offload is a fully managed service for building and running containers in
the cloud using the Docker tools you already know, including Docker Desktop, the
Docker CLI, and Docker Compose. It extends your local development workflow into a
scalable, cloud-powered environment, enabling developers to work efficiently even
in virtual desktop infrastructure (VDI) environments or systems that don't support
nested virtualization.

## Key features

Docker Offload includes the following capabilities to support modern container
workflows:

- Ephemeral cloud runners: Automatically provision and tear down cloud
  environments for each container session.
- Secure communication: Use encrypted tunnels between Docker Desktop and cloud
  environments with support for secure secrets and image pulling.
- Port forwarding and bind mounts: Retain a local development experience even
  when running containers in the cloud.
- VDI-friendly: [Use Docker Desktop](/desktop/setup/vm-vdi/) in virtual
  desktop environments or systems that don't support nested virtualization.

For more information, see the [Docker Offload product
page](https://www.docker.com/products/docker-offload/).

## How Docker Offload works

Docker Offload replaces the need to build or run containers locally by connecting
Docker Desktop to secure, dedicated cloud resources.

### Running containers with Docker Offload

When you use Docker Offload to build or run containers, Docker Desktop creates a secure
SSH tunnel to a Docker daemon running in the cloud. Your containers are started
and managed entirely in that remote environment.

Here's what happens:

1. Docker Desktop connects to the cloud and triggers container creation.
2. Docker Offload builds or pulls the required images and starts containers in the cloud.
3. The connection stays open while the containers run and you remain active.
4. When the containers stop running, the environment shuts down and is cleaned
   up automatically.

This setup avoids the overhead of running containers locally and enables fast,
reliable containers even on low-powered machines, including machines that do not
support nested virtualization. This makes Docker Offload ideal for developers
using environments such as virtual desktops, cloud-hosted development machines,
or older hardware.

Despite running remotely, features like bind mounts and port forwarding continue
to work seamlessly, providing a local-like experience from within Docker Desktop
and the CLI.

### Cloud resources

Docker Offload uses cloud hosts with 4 vCPUs and 8 GiB of memory. If you have
different requirements, <a href="https://www.docker.com/pricing/contact-sales/" id="dkr_docs_cs_offload_about" class="link" rel="noopener">contact Docker</a> to explore options.

### Session management and idle state

Docker Offload uses session management and idle state policies to ensure fair use of cloud resources across all users, see [Fair use](#fair-use).

Each user can run one Docker Offload session at a time. When Docker Desktop is in an **Offload idle** state, it waits for activity on the Docker API and only connects to a cloud environment when needed. Once connected, the session moves to an **Offload running** state and stays connected as long as Docker detects activity. Activity includes any Docker API call, a running container, or an active build.

#### When you'll see a prompt

While Docker Offload is running, Docker Desktop shows prompts in the Dashboard to check if you're still active. Prompts appear in two cases:

1. No activity is detected for more than 3 minutes.
2. The session has been running for a long time.

When a prompt appears, you can:
   - Select **Ask me again later** to confirm you're still active and continue your session.
   - Select **Idle now** to return to an idle state immediately.
   - Do nothing, and the session returns to an idle state automatically.

#### What happens when your session goes idle

After your session returns to an idle state, there is a 5-minute grace period. You can resume the session during this time by running any Docker command.

> [!IMPORTANT]
> If the idle period exceeds 5 minutes without activity, the session is terminated. Docker Offload environments are ephemeral, so the remote environment and any containers, images, or volumes in it are deleted. To keep work between sessions, push images to a registry such as [Docker Hub](/docker-hub/) before your session ends.

#### Long session prompts

Long session prompts appear every 3 hours during a session. After 8 hours of cumulative usage in a day, prompts appear every hour. The 8-hour counter resets at the start of each day.

## Fair use

Docker Offload enforces a fair use policy to prevent resource abuse. Fair use is
defined as up to 8 compute hours per named user per day, totaled across all user
sessions. Usage in excess of this threshold may be subject to session management
at Docker's discretion.

## What's next

Get hands-on with Docker Offload by following the [Docker Offload quickstart](/offload/quickstart/).

---

## Configure Docker Offload

# Configure Docker Offload

You can configure Docker Offload settings at different levels depending on your
role. Organization owners can manage settings for all users in their
organization, while individual developers can configure their own Docker Desktop
settings when allowed by their organization.

## Manage settings for your organization

For organization owners, you can manage Docker Offload settings for all users in
your organization. For more details, see [Manage Docker
products](/admin/organization/manage/manage-products/). To view usage for Docker
Offload, see [Docker Offload usage](/offload/usage/).

## Configure settings in Docker Desktop

For developers, you can enable or disable Docker Offload in Docker Desktop if
allowed by your organization. To manage this setting:

1. Open the Docker Desktop Dashboard and sign in.
2. Select the settings icon in the Docker Desktop Dashboard header.
3. In **Settings**, select **Docker Offload**.
4. Toggle **Enable Docker Offload**. When enabled, you can start Offload sessions.

---

## Give feedback

# Give feedback

There are several ways you can provide feedback on Docker Offload.

## Quick survey

The fastest way to share your thoughts is to fill out this short
[Docker Offload feedback
survey](https://docker.qualtrics.com/jfe/form/SV_br8Ki4CCdqeIYl0). It only takes
a minute and helps the Docker Team improve your experience.

## In-product feedback

On each Docker Desktop Dashboard view, there is a **Give feedback** link. This
opens a feedback form where you can share ideas directly with the Docker Team.

## Report bugs or problems on GitHub

To report bugs or problems, visit the [Docker Desktop issue tracker](https://github.com/docker/desktop-feedback).

---

## Optimize Docker Offload usage

# Optimize Docker Offload usage

Docker Offload builds and runs your containers remotely, not on the machine where you invoke the
commands. This means that files must be transferred from your local system to the
cloud over the network.

Transferring files over the network introduces higher latency and lower
bandwidth compared to local transfers.

Even with optimizations, large projects or slower network connections can lead to longer transfer times. Here are
several ways to optimize your setup for Docker Offload:

- [Use `.dockerignore` files](#dockerignore-files)
- [Choose slim base images](#slim-base-images)
- [Use multi-stage builds](#multi-stage-builds)
- [Fetch remote files during the build](#fetch-remote-files-in-build)
- [Leverage multi-threaded tools](#multi-threaded-tools)

For general Dockerfile tips, see [Building best practices](/build/building/best-practices/).

## dockerignore files

A [`.dockerignore` file](/build/concepts/context/#dockerignore-files)
lets you specify which local files should *not* be included in the build
context. Files excluded by these patterns won鈥檛 be uploaded to Docker Offload
during a build.

Typical items to ignore:

- `.git` 鈥� avoids transferring your version history. (Note: you won鈥檛 be able to run `git` commands in the build.)
- Build artifacts or locally generated binaries.
- Dependency folders such as `node_modules`, if those are restored in the build
  process.

As a rule of thumb, your `.dockerignore` should be similar to your `.gitignore`.

## Slim base images

Smaller base images in your `FROM` instructions can reduce final image size and
improve build performance. The [`alpine`](https://hub.docker.com/_/alpine) image
is a good example of a minimal base.

For fully static binaries, you can use [`scratch`](https://hub.docker.com/_/scratch), which is an empty base image.

## Multi-stage builds

[Multi-stage builds](/build/building/multi-stage/) let you separate build-time
and runtime environments in your Dockerfile. This not only reduces the size of
the final image but also allows for parallel stage execution during the build.

Use `COPY --from` to copy files from earlier stages or external images. This
approach helps minimize unnecessary layers and reduce final image size.

## Fetch remote files in build

When possible, download large files from the internet during the build itself
instead of bundling them in your local context. This avoids network transfer
from your client to Docker Offload.

You can do this using:

- The Dockerfile [`ADD` instruction](/reference/dockerfile/#add)
- `RUN` commands like `wget`, `curl`, or `rsync`

### Multi-threaded tools

Some build tools, such as `make`, are single-threaded by default. If the tool
supports it, configure it to run in parallel. For example, use `make --jobs=4`
to run four jobs simultaneously.

Taking advantage of available CPU resources in the cloud can significantly
improve build time.

---

## Docker Offload quickstart

# Docker Offload quickstart

[Docker Offload](/offload/about/) lets you build and run containers in the cloud
while using your local Docker Desktop tools and workflow. This means faster
builds, access to powerful cloud resources, and a seamless development
experience.

This quickstart covers the steps developers need to get started with Docker Offload.

> [!NOTE]
>
> If you're an organization owner, to get started you must <a href="https://www.docker.com/pricing/contact-sales/" id="dkr_docs_cs_offload_quickstart" class="link" rel="noopener">contact sales</a> and subscribe your
> organization to use Docker Offload. After subscribing, see [Manage Docker
> products](/admin/organization/manage/manage-products/) to learn how to manage
> access for the developers in your organization.

## Prerequisites

- You must have [Docker Desktop](/desktop/) installed. Docker recommends using
  the latest version of Docker Desktop to access the newest features and
  improvements in Docker Offload.
- You must have a Docker Business subscription and a Docker Offload subscription.

## Step 1: Verify access to Docker Offload

To access Docker Offload, you must be part of an organization that has
subscribed to Docker Offload. As a developer, you can verify this by checking if
the Docker Offload toggle appears in the Docker Desktop Dashboard header.

1. Start Docker Desktop and sign in.
2. In the Docker Desktop Dashboard header, look for the Docker Offload toggle.

![Offload toggle](/offload/images/offload-toggle.png)

If you see the Docker Offload toggle, you have access to Docker Offload and can
proceed to the next step. If you don't see the Docker Offload toggle, check if
Docker Offload is disabled in your [Docker Desktop
settings](/offload/configuration/), and then contact your administrator to verify
that your organization has subscribed to Docker Offload and that they have
enabled access for your organization.

## Step 2: Start Docker Offload

You can start Docker Offload from the CLI or in the header of the Docker Desktop
Dashboard. The following steps describe how to start Docker Offload using the
CLI.

1. Start Docker Desktop and sign in.
2. Open a terminal and run the following command to start Docker Offload:

   ```console
   $ docker offload start
   ```

   > [!TIP]
   >
   > To learn more about the Docker Offload CLI commands, see the [Docker Offload CLI
   > reference](/reference/cli/docker/offload/).

3. If you are a member of multiple organizations that have access to Docker
   Offload, you have the option to select a profile. Your usage will be
   associated with the organization of the selected profile.

When Docker Offload is started, you'll see a cloud icon (

![Offload mode icon](/offload/images/cloud-mode.png)) in the Docker Desktop
Dashboard header, and the Docker Desktop Dashboard appears purple. You can run
`docker offload status` in a terminal to check the status of Docker Offload.

## Step 3: Run a container with Docker Offload

After starting Docker Offload, Docker Desktop connects to a secure cloud
environment that mirrors your local experience. When you run builds or
containers, they execute remotely, but behave just like local ones.

To verify that Docker Offload is working, run a container:

```console
$ docker run --rm hello-world
```

If Docker Offload is working, you'll see `Hello from Docker!` in the terminal output.

## Step 4: Monitor your Offload session

When Docker Offload is started and you have started session (for example, you've
ran a container), then you can see current session duration estimate in the
Docker Desktop Dashboard footer next to the hourglass icon (

![Offload session duration](/offload/images/hourglass-icon.png)).

Also, when Docker Offload is started, you can view detailed session information
by selecting **Docker Offload** > **Insights** in the left navigation of the
Docker Desktop Dashboard.

## Step 5: Stop Docker Offload

Docker Offload automatically
[idles](/offload/about/#session-management-and-idle-state) if you do not respond to
periodic prompts that appear in the Docker Desktop Dashboard. You can stop your
Docker Offload session at any time. To stop Docker Offload:

```console
$ docker offload stop
```

When you stop Docker Offload, the cloud environment is terminated and all
running containers and images are removed. When Docker Offload has been idle for
5 minutes, the environment is also terminated and all running containers and
images are removed.

To start Docker Offload again, run the `docker offload start` command.

---

## Troubleshoot Docker Offload

# Troubleshoot Docker Offload

Docker Offload requires:

- Authentication
- An active internet connection
- No restrictive proxy or firewall blocking traffic to Docker Cloud
- Access to Docker Offload
- Docker Desktop 4.68 or later

Docker Desktop uses Offload to run both builds and containers in the cloud.
If builds or containers are failing to run, falling back to local, or reporting
session errors, use the following steps to help resolve the issue.

1. Ensure Docker Offload is enabled in Docker Desktop:

   1. Open Docker Desktop and sign in.
   2. Go to **Settings** > **Docker Offload**.
   3. Ensure that **Enable Docker Offload** is toggled on.

2. Use the following command to check if the connection is active:

   ```console
   $ docker offload status
   ```

3. To get more information, run the following command:

   ```console
   $ docker offload diagnose
   ```

4. If you're not connected, start a new session:

   ```console
   $ docker offload start
   ```

5. Verify authentication with `docker login`.

6. If needed, you can sign out and then sign in again:

   ```console
   $ docker logout
   $ docker login
   ```

7. Verify your usage. For more information, see [Docker Offload usage](/offload/usage/).

---

## Docker Offload usage

# Docker Offload usage

The **Offload activity** page in Docker Home provides visibility into user
activity and session metrics for Docker Offload.

To monitor your usage:

1. Sign in to [Docker Home](https://app.docker.com/).
2. If you have access to multiple organizations, select the organization
   associated with your Docker Offload subscription.
3. Select **Offload** > **Offload activity**.

### Overview metrics

Key metrics at the top of the page summarize your Docker Offload usage:

- **Total duration**: The total time spent in Offload sessions
- **Average duration**: The average time per Offload session
- **Total sessions**: The total number of Offload sessions
- **Unique images used**: The number of distinct container images used across
  sessions
- **Unique users**: The number of different users in Docker Offload sessions

### Filter and export your data

You can filter the Offload activity data by:

- **Period**: Select a preset time period or choose a custom date range
- **Users**: Organization owners and members with analytics permissions can
  filter by specific users
- **Additional Filters**: Filter by active sessions and session duration.

Export your session data by selecting the **Download CSV** button. The exported
file includes:

- Session ID
- Username
- Image
- Started time
- Ended time
- Duration (in seconds)
- Status
- Container count

The CSV export includes data for your selected date range and user filters,
letting you download exactly what you're viewing.

### Activity cards

The following cards provide insights into your Docker Offload usage:

- **Offload usage**: Shows your usage trends over time and cloud resource
  consumption patterns.
- **Popular images**: Shows the top 4 most frequently used container images in
  your Docker Offload sessions. Select the card to see more images.
- **Top Offload users**: Shows the top 4 users by session count and duration. Select
  the card to see more users.

### Offload sessions

A detailed list of Offload sessions appears following the activity cards. The list:

- Starts with any currently active sessions
- Shows session details including start time, duration, images used, and user
  information
- Can be filtered using the date and user filters described previously
- Displays **Offload sessions** if you have organization-wide analytics
  permissions, or **My Offload sessions** if viewing only your own data

Select any session to view more details in a side panel.

---
