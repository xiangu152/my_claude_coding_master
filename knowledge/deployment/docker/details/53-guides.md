---
title: "Docker 指南"
source: "https://docs.docker.com/guides/"
version: "latest"
---

# Docker 指南

> 原始文档来源：https://docs.docker.com/guides/

---

## Set up your company for success with Docker

# Set up your company for success with Docker

Docker's tools provide a scalable, secure platform that empowers your
developers to create, ship, and run applications faster. As an administrator,
you can streamline workflows, standardize development environments, and ensure
smooth deployments across your organization.

By configuring Docker products to suit your company's needs, you can optimize
performance, simplify user management, and maintain control over resources.
This guide helps you set up and configure Docker products to maximize
productivity and success for your team while meeting compliance and security
policies.

## Who鈥檚 this for?

- Administrators responsible for managing Docker environments within their
  organization
- IT leaders looking to streamline development and deployment workflows
- Teams aiming to standardize application environments across multiple users
- Organizations seeking to optimize their use of Docker products for greater
  scalability and efficiency
- Organizations with a
  [Docker Business subscription](https://www.docker.com/pricing?ref=DocsGuides&refAction=DocsGuidesCTAClicked)

## What you鈥檒l learn

- Why signing into your company's Docker organization provides access to usage
  data and enhanced functionality
- How to standardize Docker Desktop versions and settings to create a consistent
  baseline for all users, while allowing flexibility for advanced developers
- Strategies for implementing Docker's security configurations to meet company
  IT and software development security requirements without hindering developer productivity

## Features covered

This guide covers the following Docker features:

- [Organizations](/admin/organization/): The core structure
  for managing your Docker environment, grouping users, teams, and image
  repositories. Your organization was created with your subscription and is
  managed by one or more owners. Users signed into the organization are
  assigned seats based on the purchased subscription.
- [Enforce sign-in](/enterprise/security/enforce-sign-in/):
  By default, Docker Desktop doesn't require sign-in. You can configure
  settings to enforce this and ensure your developers sign in to your
  Docker organization.
- [SSO](/enterprise/security/single-sign-on/): Without SSO,
  user management in a Docker organization is manual. Setting
  up an SSO connection between your identity provider and Docker ensures
  compliance with your security policy and automates user provisioning. Adding
  SCIM further automates user provisioning and de-provisioning.
- General and security settings: Configuring key settings ensures smooth
  onboarding and usage of Docker products within your environment. You can also
  enable security features based on your company's specific security needs.

## Who needs to be involved

- Docker organization owner: Must be involved in the process and is required
  for several key steps
- DNS team: Needed during the SSO setup to verify the company domain
- MDM team: Responsible for distributing Docker-specific configuration files to
  developer machines
- Identity Provider team: Required for configuring the identity provider and
  establishing the SSO connection during setup
- Development lead: A development lead with knowledge of Docker configurations
  to help establish a baseline for developer settings
- IT team: An IT representative familiar with company desktop policies to
  assist with aligning Docker configuration to those policies
- Infosec: A security team member with knowledge of company development
  security policies to help configure security features
- Docker testers: A small group of developers to test the new settings and
  configurations before full deployment

## Tools integration

This guide covers integration with:

- Okta
- Entra ID SAML 2.0
- Azure Connect (OIDC)
- MDM solutions like Intune

## Communication and information gathering

### Communicate with your developers and IT teams

Before rolling out Docker Desktop across your organization, coordinate with key stakeholders to ensure a smooth transition.

#### Notify Docker Desktop users

You may already have Docker Desktop users within your company. Some steps in
this onboarding process may affect how they interact with the platform.

Communicate early with users to inform them that:

- They'll be upgraded to a supported version of Docker Desktop as part of the subscription onboarding
- Settings will be reviewed and optimized for productivity
- They'll need to sign in to the company's Docker organization using their
  business email to access subscription benefits

#### Engage with your MDM team

Device management solutions, such as Intune and Jamf, are commonly used for
software distribution across enterprises. These tools are typically managed by a dedicated MDM team.

Engage with this team early in the process to:

- Understand their requirements and lead time for deploying changes
- Coordinate the distribution of configuration files

Several setup steps in this guide require JSON files, registry keys, or .plist
files to be distributed to developer machines. Use MDM tools to deploy these configuration files and ensure their integrity.

### Identify Docker organizations

Some companies may have more than one
[Docker organization](/admin/organization/) created. These
organizations may have been created for specific purposes, or may not be
needed anymore.

If you suspect your company has multiple Docker organizations:

- Survey your teams to see if they have their own organizations
- Contact your Docker Support to get a list of organizations with users whose
  emails match your domain name

### Gather requirements

[Settings Management](/enterprise/security/hardened-desktop/settings-management/) lets you preset numerous configuration parameters for Docker Desktop.

Work with the following stakeholders to establish your company's baseline
configuration:

- Docker organization owner
- Development lead
- Information security representative

Review these areas together:

- Security features and
  [enforcing sign-in](/enterprise/security/enforce-sign-in/)
  for Docker Desktop users
- Additional Docker products included in your subscriptions

To view the parameters that can be preset, see [Configure Settings Management](/enterprise/security/hardened-desktop/settings-management/configure-json-file/#step-two-configure-the-settings-you-want-to-lock-in).

### Optional: Meet with the Docker Implementation team

The Docker Implementation team can help you set up your organization,
configure SSO, enforce sign-in, and configure Docker Desktop.

To schedule a meeting, email successteam@docker.com.

## Finalize plans and begin setup

### Send finalized settings files to the MDM team

After reaching an agreement with the relevant teams about your baseline and
security configurations as outlined in the previous section, configure Settings Management using either the [Docker Admin Console](/enterprise/security/hardened-desktop/settings-management/configure-admin-console/) or an
[`admin-settings.json` file](/enterprise/security/hardened-desktop/settings-management/configure-json-file/).

Once the file is ready, collaborate with your MDM team to deploy your chosen
settings, along with your chosen method for [enforcing sign-in](/enterprise/security/enforce-sign-in/).

> [!IMPORTANT]
>
> Test this first with a small number of Docker Desktop developers to verify the functionality works as expected before deploying more widely.

### Manage your organizations

If you have more than one organization, consider either [consolidating them
into one organization](/admin/organization/setup/orgs/) or creating a
[Docker company](/admin/company/) to manage multiple
organizations.

### Begin setup

#### Set up single sign-on and domain verification

Single sign-on (SSO) lets developers authenticate using their identity
providers (IdPs) to access Docker. SSO is available for a whole company and all associated organizations, or an individual organization that has a Docker
Business subscription. For more information, see the
[documentation](/enterprise/security/single-sign-on/).

You can also enable [SCIM](/enterprise/security/provisioning/scim/)
for further automation of provisioning and deprovisioning of users.

#### Set up Docker product entitlements included in the subscription

[Docker Build Cloud](/build-cloud/) significantly reduces
build times, both locally and in CI, by providing a dedicated remote builder
and shared cache. Powered by the cloud, developer time and local resources are
freed up so your team can focus on more important things, like innovation.
To get started, [set up a cloud builder](https://app.docker.com/build/).

[Docker Scout](/guides/admin-set-up/scout/) is a solution for proactively enhancing
your software supply chain security. By analyzing your images, Docker Scout
compiles an inventory of components, also known as a Software Bill of Materials
(SBOM). The SBOM is matched against a continuously updated vulnerability
database to pinpoint security weaknesses. To get started, see
[Quickstart](/scout/quickstart/).

[Testcontainers Cloud](https://testcontainers.com/cloud/docs/) allows
developers to run containers in the cloud, removing the need to run heavy
containers on your local machine.

[Docker Hardened Images](/dhi/) are minimal, secure, and production-ready container base and application images maintained by Docker.
Designed to reduce vulnerabilities and simplify compliance, DHIs integrate
easily into your existing Docker-based workflows with little to no retooling
required.

#### Ensure you're running a supported version of Docker Desktop

> [!WARNING]
>
> This step could affect the experience for users on older versions of Docker
> Desktop.

Existing users may be running outdated or unsupported versions of
Docker Desktop. All users should update to a supported version. Docker Desktop
versions released within the past 6 months from the latest release are supported.

Use an MDM solution to manage the version of Docker Desktop for users. Users
may also get Docker Desktop directly from Docker or through a company software
portal.

## Testing

### SSO and SCIM testing

Test SSO and SCIM by signing in to Docker Desktop or Docker Hub with the email
address linked to a Docker account that is part of the verified domain.
Developers who sign in using their Docker usernames remain unaffected by the
SSO and SCIM setup.

> [!IMPORTANT]
>
> Some users may need CLI based logins to Docker Hub, and for this they will
> need a [personal access token (PAT)](/security/access-tokens/).

### Test Registry Access Management and Image Access Management

> [!WARNING]
>
> Communicate with your users before proceeding, as this step will impact all
> existing users signing into your Docker organization.

If you plan to use [Registry Access Management (RAM)](/enterprise/security/hardened-desktop/registry-access-management/) and/or [Image Access Management (IAM)](/enterprise/security/hardened-desktop/image-access-management/):

1. Ensure your test developer signs in to Docker Desktop using their
   organization credentials
2. Have them attempt to pull an unauthorized image or one from a disallowed
   registry via the Docker CLI
3. Verify they receive an error message indicating that the registry is
   restricted by the organization

### Deploy settings and enforce sign in to test group

Deploy the Docker settings and enforce sign-in for a small group of test users
via MDM. Have this group test their development workflows with containers on
Docker Desktop and Docker Hub to ensure all settings and the sign-in enforcement
function as expected.

### Test Docker Build Cloud capabilities

Have one of your Docker Desktop testers [connect to the cloud builder you created and use it to build](/build-cloud/usage/).

### Test Testcontainers Cloud

Have a test developer [connect to Testcontainers Cloud](https://testcontainers.com/cloud/docs/#getting-started) and run a container in
the cloud to verify the setup is working correctly.

### Verify Docker Scout monitoring of repositories

Check the [Docker Scout dashboard](https://scout.docker.com/) to confirm that
data is being properly received for the repositories where Docker Scout has
been enabled.

### Verify access to Docker Hardened Images

Have a test developer attempt to [pull a Docker Hardened Image](/dhi/get-started/) to confirm that
the team has proper access and can integrate these images into their workflows.

## Deploy your Docker setup

> [!WARNING]
>
> Communicate with your users before proceeding, and confirm that your IT and
> MDM teams are prepared to handle any unexpected issues, as these steps will
> affect all existing users signing into your Docker organization.

### Enforce SSO

Enforcing SSO means that anyone who has a Docker profile with an email address
that matches your verified domain must sign in using your SSO connection. Make
sure the Identity provider groups associated with your SSO connection cover all
the developer groups that you want to have access to the Docker subscription.

For instructions on how to enforce SSO, see [Enforce SSO](/enterprise/security/single-sign-on/connect/).

### Deploy configuration settings and enforce sign-in to users

Have the MDM team deploy the configuration files for Docker to all users.

### Next steps

Congratulations, you've successfully completed the admin implementation process
for Docker.

To continue optimizing your Docker environment:

- Review your [organization's usage data](/admin/insights/) to track adoption
- Monitor [Docker Scout findings](/scout/explore/analysis/) for security insights
- Explore [additional security features](/enterprise/security/) to enhance your configuration

---

## Mastering user and access management

# Mastering user and access management

Managing roles and permissions is key to securing your Docker environment while enabling easy collaboration and operational efficiency. This guide walks IT administrators through the essentials of user and access management, offering strategies for assigning roles, provisioning users, and using tools like activity logs and Insights to monitor and optimize Docker usage.

## Who's this for?

- IT teams tasked with configuring and maintaining secure user access
- Security professionals focused on enforcing secure access practices
- Project managers overseeing team collaboration and resource management

## What you'll learn

- How to assess and manage Docker user access and align accounts with organizational needs
- When to use team configurations for scalable access control
- How to automate and streamline user provisioning with SSO, SCIM, and JIT
- How to get the most out of Docker's monitoring tools

## Tools integration

This guide covers integration with:

- Okta
- Entra ID SAML 2.0
- Azure Connect (OIDC)

## Setting up roles and permissions in Docker

With the right configurations, you can ensure your developers have easy access to necessary resources while preventing unauthorized access. This page guides you through identifying Docker users so you can allocate subscription seats efficiently within your Docker organization, and assigning roles to align with your organization's structure.

### Identify your Docker users and accounts

Before setting up roles and permissions, it's important to have a clear understanding of who in your organization requires Docker access. Focus on gathering a comprehensive view of active users, their roles within projects, and how they interact with Docker resources. This process can be supported by tools like device management software or manual assessments. Encourage all users to update their Docker accounts to use organizational email addresses, ensuring seamless integration with your subscription.

For steps on how you can do this, see [step 1 of onboarding your organization](/admin/organization/setup/onboard/).

### Assign roles strategically

When you invite members to join your Docker organization, you assign them a role.

Docker's predefined roles offer flexibility for various organizational needs. Assigning roles effectively ensures a balance of accessibility and security.

- Member: Non-administrative role. Members can view other members that are in the same organization.
- Editor: Partial administrative access to the organization. Editors can create, edit, and delete repositories. They can also edit an existing team's access permissions.
- Owner: Full organization administrative access. Owners can manage organization repositories, teams, members, settings, and billing.

For more information, see [Roles and permissions](/enterprise/security/roles-and-permissions/).

#### Enhance with teams

Teams in Docker provide a structured way to manage member access and they provide an additional level of permissions. They simplify permission management and enable consistent application of policies.

- Organize users into teams aligned with projects, departments, or functional roles. This approach helps streamline resource allocation and ensures clarity in access control.
- Assign permissions at the team level rather than individually. For instance, a development team might have "Read & Write" access to certain repositories, while a QA team has "Read-only" access.
- As teams grow or responsibilities shift, you can easily update permissions or add new members, maintaining consistency without reconfiguring individual settings.

For more information, see [Create and manage a team](/admin/organization/manage/manage-a-team/).

#### Example scenarios

- Development teams: Assign the member role to developers, granting access to the repositories needed for coding and testing.
- Team leads: Assign the editor role to team leads for resource management and repository control within their teams.
- Organizational oversight: Restrict the organization owner or company owner roles to a select few trusted individuals responsible for billing and security settings.

#### Best practices

- Apply the principle of least privilege. Assign users only the minimum permissions necessary for their roles.
- Conduct regular reviews of role assignments to ensure they align with evolving team structures and organizational responsibilities.

## Onboarding and managing roles and permissions in Docker

This page guides you through onboarding owners and members, and using tools like SSO and SCIM to future-proof onboarding going forward.

### Invite owners

When you create a Docker organization, you automatically become its sole owner. While optional, adding additional owners can significantly ease the process of onboarding and managing your organization by distributing administrative responsibilities. It also ensures continuity and prevents blockers if the primary owner is unavailable.

For detailed information on owners, see [Roles and permissions](/enterprise/security/roles-and-permissions/).

### Invite members and assign roles

Members are granted controlled access to resources and enjoy enhanced organizational benefits. When you invite members to join your Docker organization, you immediately assign them a role.

#### Benefits of inviting members

- Enhanced visibility: Gain insights into user activity, making it easier to monitor access and enforce security policies.
- Streamlined collaboration: Help members collaborate effectively by granting access to shared resources and repositories.
- Improved resource management: Organize and track users within your organization, ensuring optimal allocation of resources.
- Access to enhanced features: Members benefit from organization-wide perks, such as increased pull limits and access to premium Docker features.
- Security control: Apply and enforce security settings at an organizational level, reducing risks associated with unmanaged accounts.

For detailed information, see [Manage organization members](/admin/organization/manage/members/).

### Future-proof user management

A robust, future-proof approach to user management combines automated provisioning, centralized authentication, and dynamic access control. Implementing these practices ensures a scalable, secure, and efficient environment.

#### Secure user authentication with single sign-on (SSO)

Integrating Docker with your identity provider streamlines user access and enhances security.

SSO:

- Simplifies sign in, as users sign in with their organizational credentials.
- Reduces password-related vulnerabilities.
- Simplifies onboarding as it works seamlessly with SCIM and group mapping for automated provisioning.

For more information, see the [SSO documentation](/enterprise/security/single-sign-on/).

#### Automate onboarding with SCIM and JIT provisioning

Streamline user provisioning and role management with [SCIM](/enterprise/security/provisioning/scim/) and [Just-in-Time (JIT) provisioning](/enterprise/security/provisioning/just-in-time/).

With SCIM you can:

- Sync users and roles automatically with your identity provider.
- Automate adding, updating, or removing users based on directory changes.

With JIT provisioning you can:

- Automatically add users upon first sign in based on [group mapping](#simplify-access-with-group-mapping).
- Reduce overhead by eliminating pre-invite steps.

#### Simplify access with group mapping

Group mapping automates permissions management by linking identity provider groups to Docker roles and teams.

It also:

- Reduces manual errors in role assignments.
- Ensures consistent access control policies.
- Help you scale permissions as teams grow or change.

For more information on how it works, see [Group mapping](/enterprise/security/provisioning/scim/group-mapping/).

## Monitoring and insights

Activity logs and Insights are useful tools for user and access management in Docker. They provide visibility into user actions, team workflows, and organizational trends, helping enhance security, ensure compliance, and boost productivity.

### Activity logs

Activity logs track events at the organization and repository levels, offering a clear view of activities like repository changes, team updates, and billing adjustments.

Activity logs are available for Docker Team or Docker Business plans, with data retained for three months.

#### Key features

- Change tracking: View what changed, who made the change, and when.
- Comprehensive reporting: Monitor critical events such as repository creation, deletion, privacy changes, and role assignments.

#### Example scenarios

- Audit trail for security: A repository鈥檚 privacy settings were updated unexpectedly. The activity logs reveal which user made the change and when, helping administrators address potential security risks.
- Team collaboration review: Logs show which team members pushed updates to a critical repository, ensuring accountability during a development sprint.
- Billing adjustments: Track who added or removed subscription seats to maintain budgetary control and compliance.

For more information, see [Activity logs](/admin/activity-logs/).

### Insights

Insights provide data-driven views of Docker usage to improve team productivity and resource allocation.

#### Key benefits

- Standardized environments: Ensure consistent configurations and enforce best practices across teams.
- Improved visibility: Monitor metrics like Docker Desktop usage, builds, and container activity to understand team workflows and engagement.
- Optimized resources: Track license usage and feature adoption to maximize the value of your Docker subscription.

#### Example scenarios

- Usage trends: Identify underutilized licenses or resources, allowing reallocation to more active teams.
- Build efficiency: Track average build times and success rates to pinpoint bottlenecks in development processes.
- Container utilization: Analyze container activity across departments to ensure proper resource distribution and cost efficiency.

For more information, see [Insights](/admin/insights/).

### Next steps

Now that you've mastered user and access management in Docker, you can:

- Review your [activity logs](/admin/activity-logs/) regularly to maintain security awareness
- Check your [Insights dashboard](/admin/insights/) to identify opportunities for optimization
- Explore [advanced security features](/enterprise/security/) to further enhance your Docker environment
- Share best practices with your team to ensure consistent adoption of security policies

---

## Build and run agentic AI applications with Docker

# Build and run agentic AI applications with Docker

> [!TIP]
>
> This guide uses the familiar Docker Compose workflow to orchestrate agentic AI
> applications. For a smoother development experience, check out
> [Docker Agent](/ai/docker-agent/), a purpose-built agent runtime that
> simplifies running and managing AI agents.

## Introduction

Agentic applications are transforming how software gets built. These apps don't
just respond, they decide, plan, and act. They're powered by models,
orchestrated by agents, and integrated with APIs, tools, and services in real
time.

All these new agentic applications, no matter what they do, share a common
architecture. It's a new kind of stack, built from three core components:

- Models: These are your GPTs, CodeLlamas, Mistrals. They're doing the
  reasoning, writing, and planning. They're the engine behind the intelligence.

- Agent: This is where the logic lives. Agents take a goal, break it down, and
  figure out how to get it done. They orchestrate everything. They talk to the
  UI, the tools, the model, and the gateway.

- MCP gateway: This is what links your agents to the outside world, including
  APIs, tools, and services. It provides a standard way for agents to call
  capabilities via the Model Context Protocol (MCP).

Docker makes this AI-powered stack simpler, faster, and more secure by unifying
models, and tool gateways into a developer-friendly workflow that uses Docker
Compose.

![A diagram of the agentic stack](/guides/images/agentic-ai-diagram.webp)

This guide walks you through the core components of agentic development and
shows how Docker ties them all together with the following tools:

- [Docker Model Runner](/ai/model-runner/) lets you run LLMs
  locally with simple command and OpenAI-compatible APIs.
- [Docker MCP Catalog and
  Toolkit](/ai/mcp-catalog-and-toolkit/) helps you discover
  and securely run external tools, like APIs and databases, using the Model
  Context Protocol (MCP).
- [Docker MCP Gateway](/ai/mcp-catalog-and-toolkit/mcp-gateway/) lets you orchestrate and manage MCP servers.
- [Docker Compose](/ai/compose/models-and-compose/) is the tool that ties it all
  together, letting you define and run multi-container applications with a
  single file.

For this guide, you'll use the same Compose workflow you're already familiar
with. Then, you'll dig into the Compose file, Dockerfile, and app to see how it
all works together.

## Prerequisites

To follow this guide, you need to:

- [Install Docker Desktop 4.43 or later](/get-started/get-docker/)
- [Enable Docker Model Runner](/ai/model-runner/#enable-dmr-in-docker-desktop)
- At least the following hardware specifications:
  - VRAM: 3.5 GB
  - Storage: 2.31 GB

## Step 1: Clone the sample application

You'll use an existing sample application that demonstrates how to connect a
model to an external tool using Docker's AI features.

```console
$ git clone https://github.com/docker/compose-for-agents.git
$ cd compose-for-agents/adk/
```

## Step 2: Run the application locally

Your machine must meet the necessary hardware requirements to run the
entire application stack locally using Docker Compose. This lets you test the
application end-to-end, including the model and MCP gateway, without needing to
run in the cloud. This particular example uses the [Gemma 3 4B
model](https://hub.docker.com/r/ai/gemma3) with a context size of `10000`.

Hardware requirements:

- VRAM: 3.5 GB
- Storage: 2.31 GB

If your machine exceeds those requirements, consider running the application with a larger
context size or a larger model to improve the agents performance. You can easily
update model and context size in the `compose.yaml` file.

To run the application locally, follow these steps:

1. In the `adk/` directory of the cloned repository, run the following command in a
   terminal to build and run the application:

   ```console
   $ docker compose up
   ```

   The first time you run this command, Docker pulls the
   model from Docker Hub, which may take some time.

2. Visit [http://localhost:8080](http://localhost:8080). Enter a correct or
   incorrect fact in the prompt and hit enter. An agent searches DuckDuckGo to
   verify it and another agent revises the output.

   ![Screenshot of the application](/guides/images/agentic-ai-app.png)

3. Press ctrl-c in the terminal to stop the application when you're done.

## Step 3: Review the application environment

You can find the `compose.yaml` file in the `adk/` directory. Open it in a text
editor to see how the services are defined.

```yaml {collapse=true,title=compose.yaml}
services:
  adk:
    build:
      context: .
    ports:
      # expose port for web interface
      - "8080:8080"
    environment:
      # point adk at the MCP gateway
      - MCPGATEWAY_ENDPOINT=http://mcp-gateway:8811/sse
    depends_on:
      - mcp-gateway
    models:
      gemma3:
        endpoint_var: MODEL_RUNNER_URL
        model_var: MODEL_RUNNER_MODEL

  mcp-gateway:
    # mcp-gateway secures your MCP servers
    image: docker/mcp-gateway:latest
    use_api_socket: true
    command:
      - --transport=sse
      # add any MCP servers you want to use
      - --servers=duckduckgo

models:
  gemma3:
    # pre-pull the model when starting Docker Model Runner
    model: ai/gemma3:4B-Q4_0
    context_size: 10000 # 3.5 GB VRAM
    # increase context size to handle search results
    # context_size: 131000 # 7.6 GB VRAM
```

The app consists of three main components:

- The `adk` service, which is the web application that runs the agentic AI
  application. This service talks to the MCP gateway and model.
- The `mcp-gateway` service, which is the MCP gateway that connects the app
  to external tools and services.
- The `models` block, which defines the model to use with the application.

When you examine the `compose.yaml` file, you'll notice two notable elements for the model:

- A service鈥憀evel `models` block in the `adk` service
- A top-level `models` block

These two blocks together let Docker Compose automatically start and connect
your ADK web app to the specified LLM.

> [!TIP]
>
> Looking for more models to use? Check out the [Docker AI Model
> Catalog](https://hub.docker.com/catalogs/models/).

When examining the `compose.yaml` file, you'll notice the gateway service is a
Docker-maintained image,
[`docker/mcp-gateway:latest`](https://hub.docker.com/r/docker/agents_gateway).
This image is Docker's open source [MCP
Gateway](https://github.com/docker/docker-mcp/) that enables your application to
connect to MCP servers, which expose tools that models can call. In this
example, it uses the [`duckduckgo` MCP
server](https://hub.docker.com/mcp/server/duckduckgo/overview) to perform web
searches.

> [!TIP]
>
> Looking for more MCP servers to use? Check out the [Docker MCP
> Catalog](https://hub.docker.com/catalogs/mcp/).

With only a few lines of instructions in a Compose file, you're able to run and
connect all the necessary services of an agentic AI application.

In addition to the Compose file, the Dockerfile and the
`entrypoint.sh` script it creates, play a role in wiring up the AI stack at build and
runtime. You can find the `Dockerfile` in the `adk/` directory. Open it in a
text editor.

```dockerfile {collapse=true,title=Dockerfile}
# Use Python 3.11 slim image as base
FROM python:3.13-slim
ENV PYTHONUNBUFFERED=1

RUN pip install uv

WORKDIR /app
# Install system dependencies
COPY pyproject.toml uv.lock ./
RUN --mount=type=cache,target=/root/.cache/uv \
    UV_COMPILE_BYTECODE=1 UV_LINK_MODE=copy \
    uv pip install --system .
# Copy application code
COPY agents/ ./agents/
RUN python -m compileall -q .

COPY <<EOF /entrypoint.sh
#!/bin/sh
set -e

if test -f /run/secrets/openai-api-key; then
    export OPENAI_API_KEY=$(cat /run/secrets/openai-api-key)
fi

if test -n "\${OPENAI_API_KEY}"; then
    echo "Using OpenAI with \${OPENAI_MODEL_NAME}"
else
    echo "Using Docker Model Runner with \${MODEL_RUNNER_MODEL}"
    export OPENAI_BASE_URL=\${MODEL_RUNNER_URL}
    export OPENAI_MODEL_NAME=openai/\${MODEL_RUNNER_MODEL}
    export OPENAI_API_KEY=cannot_be_empty
fi
exec adk web --host 0.0.0.0 --port 8080 --log_level DEBUG
EOF
RUN chmod +x /entrypoint.sh

# Create non-root user
RUN useradd --create-home --shell /bin/bash app \
    && chown -R app:app /app
USER app

ENTRYPOINT [ "/entrypoint.sh" ]
```

The `entrypoint.sh` has five key environment variables:

- `MODEL_RUNNER_URL`: Injected by Compose (via the service-level `models:`
  block) to point at your Docker Model Runner HTTP endpoint.

- `MODEL_RUNNER_MODEL`: Injected by Compose to select which model to launch in
  Model Runner.

- `OPENAI_API_KEY`: If you define an `openai-api-key` secret in your Compose
  file, Compose will mount it at `/run/secrets/openai-api-key`. The entrypoint
  script reads that file and exports it as `OPENAI_API_KEY`, causing the app to
  use hosted OpenAI instead of Model Runner.

- `OPENAI_BASE_URL`: When no real key is present, this is set to
  `MODEL_RUNNER_URL` so the ADK's OpenAI-compatible client sends requests to
  Docker Model Runner.

- `OPENAI_MODEL_NAME`: When falling back to Model Runner, the model is prefixed
  with `openai/` so the client picks up the right model alias.

Together, these variables let the same ADK web server code seamlessly target either:

- Hosted OpenAI: if you supply `OPENAI_API_KEY` (and optionally `OPENAI_MODEL_NAME`)
- Model Runner: by remapping `MODEL_RUNNER_URL` and `MODEL_RUNNER_MODEL` into the OpenAI client鈥檚 expected variables

## Step 4: Review the application

The `adk` web application is an agent implementation that connects to the MCP
gateway and a model through environment variables and API calls. It uses the
[ADK (Agent Development Kit)](https://github.com/google/adk-python) to define a
root agent named Auditor, which coordinates two sub-agents, Critic and Reviser,
to verify and refine model-generated answers.

The three agents are:

- Critic: Verifies factual claims using the toolset, such as DuckDuckGo.
- Reviser: Edits answers based on the verification verdicts provided by the Critic.
- Auditor: A higher-level agent that sequences the
  Critic and Reviser. It acts as the entry point, evaluating LLM-generated
  answers, verifying them, and refining the final output.

All of the application's behavior is defined in Python under the `agents/`
directory. Here's a breakdown of the notable files:

- `agents/agent.py`: Defines the Auditor, a SequentialAgent that chains together
  the Critic and Reviser agents. This agent is the main entry point of the
  application and is responsible for auditing LLM-generated content using
  real-world verification tools.

- `agents/sub_agents/critic/agent.py`: Defines the Critic agent. It loads the
  language model (via Docker Model Runner), sets the agent鈥檚 name and behavior,
  and connects to MCP tools (like DuckDuckGo).

- `agents/sub_agents/critic/prompt.py`: Contains the Critic prompt, which
  instructs the agent to extract and verify claims using external tools.

- `agents/sub_agents/critic/tools.py`: Defines the MCP toolset configuration,
  including parsing `mcp/` strings, creating tool connections, and handling MCP
  gateway communication.

- `agents/sub_agents/reviser/agent.py`: Defines the Reviser agent, which takes
  the Critic鈥檚 findings and minimally rewrites the original answer. It also
  includes callbacks to clean up the LLM output and ensure it's in the right
  format.

- `agents/sub_agents/reviser/prompt.py`: Contains the Reviser prompt, which
  instructs the agent to revise the answer text based on the verified claim
  verdicts.

The MCP gateway is configured via the `MCPGATEWAY_ENDPOINT` environment
variable. In this case, `http://mcp-gateway:8811/sse`. This allows the app to
use Server-Sent Events (SSE) to communicate with the MCP gateway container,
which itself brokers access to external tool services like DuckDuckGo.

## Summary

Agent-based AI applications are emerging as a powerful new software
architecture. In this guide, you explored a modular, chain-of-thought system
where an Auditor agent coordinates the work of a Critic and a Reviser to
fact-check and refine model-generated answers. This architecture shows how to
combine local model inference with external tool integrations in a structured,
modular way.

You also saw how Docker simplifies this process by providing a suite of tools
that support agentic AI development:

- [Docker Model Runner](/ai/model-runner/): Run and serve
  open-source models locally via OpenAI-compatible APIs.
- [Docker MCP Catalog and
  Toolkit](/ai/mcp-catalog-and-toolkit/): Launch and manage
  tool integrations that follow the Model Context Protocol (MCP) standard.
- [Docker MCP Gateway](/ai/mcp-catalog-and-toolkit/mcp-gateway/): Orchestrate and manage
  MCP servers to connect agents to external tools and services.
- [Docker Compose](/ai/compose/models-and-compose/): Define and run
  multi-container agentic AI applications with a single file, using the same
  workflow.

With these tools, you can develop and test agentic AI applications efficiently,
using the same consistent workflow throughout.

---

## Angular language-specific guide

# Angular language-specific guide

The Angular language-specific guide shows you how to containerize an Angular application using Docker, following best practices for creating efficient, production-ready containers.

[Angular](https://angular.dev/) is a robust and widely adopted framework for building dynamic, enterprise-grade web applications. However, managing dependencies, environments, and deployments can become complex as applications scale. Docker streamlines these challenges by offering a consistent, isolated environment for development and production.

> **Acknowledgment**
>
> Docker extends its sincere gratitude to [Kristiyan Velkov](https://www.linkedin.com/in/kristiyan-velkov-763130b3/) for authoring this guide. As a Docker Captain and experienced Front-end engineer, his expertise in Docker, DevOps, and modern web development has made this resource essential for the community, helping developers navigate and optimize their Docker workflows.

---

## What will you learn?

In this guide, you will learn how to:

- Containerize and run an Angular application using Docker.
- Set up a local development environment for Angular inside a container.
- Run tests for your Angular application within a Docker container.

You'll start by containerizing an existing Angular application and work your way up to production-level deployments.

---

## Prerequisites

Before you begin, ensure you have a working knowledge of:

- Basic understanding of [TypeScript](https://www.typescriptlang.org/) and [JavaScript](https://developer.mozilla.org/en-US/docs/Web/JavaScript).
- Familiarity with [Node.js](https://nodejs.org/en) and [npm](https://docs.npmjs.com/about-npm) for managing dependencies and running scripts.
- Familiarity with [Angular](https://angular.io/) fundamentals.
- Understanding of core Docker concepts such as images, containers, and Dockerfiles. If you're new to Docker, start with the [Docker basics](/get-started/docker-concepts/the-basics/what-is-a-container/) guide.

Once you've completed the Angular getting started modules, you鈥檒l be fully prepared to containerize your own Angular application using the detailed examples and best practices outlined in this guide.

## Containerize an Angular Application

### Prerequisites

Before you begin, make sure the following tools are installed and available on your system:

- You have installed the latest version of [Docker Desktop](/get-started/get-docker/).
- You have a [git client](https://git-scm.com/downloads). The examples in this section use a command-line based git client, but you can use any client.

> **New to Docker?**  
> Start with the [Docker basics](/get-started/docker-concepts/the-basics/what-is-a-container/) guide to get familiar with key concepts like images, containers, and Dockerfiles.

---

### Overview

This guide walks you through the complete process of containerizing an Angular application with Docker. You鈥檒l learn how to create a production-ready Docker image using best practices that improve performance, security, scalability, and deployment efficiency.

By the end of this guide, you will:

- Containerize an Angular application using Docker.
- Create and optimize a Dockerfile for production builds.
- Use multi-stage builds to minimize image size.
- Serve the application efficiently with a custom Nginx configuration.
- Build secure and maintainable Docker images by following best practices.

---

### Get the sample application

Clone the sample application to use with this guide. Open a terminal, navigate to the directory where you want to work, and run the following command
to clone the git repository:

```console
$ git clone https://github.com/kristiyan-velkov/docker-angular-sample
```

---

### Build the Docker image

Angular is a front-end framework that compiles into static assets, so the Dockerfile uses a multi-stage build: one stage compiles the app with Node.js, and a second minimal stage serves the static output with Nginx.

> [!TIP]
>
> [Gordon](/ai/gordon/), Docker's AI assistant, can generate Docker assets for your project. Ask Gordon to create a Dockerfile, Compose file, and `.dockerignore` tailored to your application.

#### Step 1: Create the Dockerfile

Before creating a Dockerfile, you need to choose a base image. You can either use the [Node.js Official Image](https://hub.docker.com/_/node) or a Docker Hardened Image (DHI) from the [Hardened Image catalog](https://hub.docker.com/hardened-images/catalog).

Choosing DHI offers the advantage of a production-ready image that is lightweight and secure. For more information, see [Docker Hardened Images](https://docs.docker.com/dhi/).

> [!IMPORTANT]
> This guide uses a stable Node.js LTS image tag that is considered secure when the guide is written. Because new releases and security patches are published regularly, the tag shown here may no longer be the safest option when you follow the guide. Always review the latest available image tags and select a secure, up-to-date version before building or deploying your application.
>
> Official Node.js Docker Images: https://hub.docker.com/_/node

**Using Docker Hardened Images**

Docker Hardened Images (DHIs) are available for Node.js in the [Docker Hardened Images catalog](https://hub.docker.com/hardened-images/catalog/dhi/node). Docker Hardened Images are freely available to everyone with no subscription required. You can pull and use them like any other Docker image after signing in to the DHI registry. For more information, see the [DHI quickstart](/dhi/get-started/) guide.

1. Sign in to the DHI registry:

   ```console
   $ docker login dhi.io
   ```

2. Pull the Node.js DHI (check the catalog for available versions):
   ```console
   $ docker pull dhi.io/node:24-alpine3.22-dev
   ```

In the following Dockerfile, the `FROM` instruction uses `dhi.io/node:24-alpine3.22-dev` as the base image.

```dockerfile
# =========================================
# Stage 1: Build the Angular Application
# =========================================

# Use a lightweight DHI Node.js image for building
FROM dhi.io/node:24-alpine3.22-dev AS builder

# Set the working directory inside the container
WORKDIR /app

# Copy package-related files first to leverage Docker's caching mechanism
COPY package.json package-lock.json* ./

# Install project dependencies using npm ci (ensures a clean, reproducible install)
RUN --mount=type=cache,target=/root/.npm npm ci

# Copy the rest of the application source code into the container
COPY . .

# Build the Angular application
RUN npm run build

# =========================================
# Stage 2: Prepare Nginx to Serve Static Files
# =========================================

FROM dhi.io/nginx:1.28.0-alpine3.21-dev AS runner

# Copy custom Nginx config
COPY nginx.conf /etc/nginx/nginx.conf

# Copy the static build output from the build stage to Nginx's default HTML serving directory
COPY --chown=nginx:nginx --from=builder /app/dist/*/browser /usr/share/nginx/html

# Use a non-root user for security best practices
USER nginx

# Expose port 8080 to allow HTTP traffic
# Note: The default Nginx container now listens on port 8080 instead of 80
EXPOSE 8080

# Start Nginx directly with custom config
ENTRYPOINT ["nginx", "-c", "/etc/nginx/nginx.conf"]
CMD ["-g", "daemon off;"]
```

**Using the Docker Official Image**

Create a file named `Dockerfile` with the following contents:

```dockerfile
# =========================================
# Stage 1: Build the Angular Application
# =========================================
ARG NODE_VERSION=24.12.0-alpine
ARG NGINX_VERSION=alpine3.22

# Use a lightweight Node.js image for building (customizable via ARG)
FROM node:${NODE_VERSION} AS builder

# Set the working directory inside the container
WORKDIR /app

# Copy package-related files first to leverage Docker's caching mechanism
COPY package.json *package-lock.json* ./

# Install project dependencies using npm ci (ensures a clean, reproducible install)
RUN --mount=type=cache,target=/root/.npm npm ci

# Copy the rest of the application source code into the container
COPY . .

# Build the Angular application
RUN npm run build

# =========================================
# Stage 2: Prepare Nginx to Serve Static Files
# =========================================

FROM nginxinc/nginx-unprivileged:${NGINX_VERSION} AS runner

# Copy custom Nginx config
COPY nginx.conf /etc/nginx/nginx.conf

# Copy the static build output from the build stage to Nginx's default HTML serving directory
COPY --chown=nginx:nginx --from=builder /app/dist/*/browser /usr/share/nginx/html

# Use a built-in non-root user for security best practices
USER nginx

# Expose port 8080 to allow HTTP traffic
# Note: The default Nginx container now listens on port 8080 instead of 80
EXPOSE 8080

# Start Nginx directly with custom config
ENTRYPOINT ["nginx", "-c", "/etc/nginx/nginx.conf"]
CMD ["-g", "daemon off;"]
```

> [!NOTE]
> We are using nginx-unprivileged instead of the standard Nginx image to follow security best practices.
> Running as a non-root user in the final image:
>
> - Reduces the attack surface
> - Aligns with Docker鈥檚 recommendations for container hardening
> - Helps comply with stricter security policies in production environments

#### Step 2: Create the compose.yaml file

Create a file named `compose.yaml` with the following contents:

```yaml {collapse=true,title=compose.yaml}
services:
  server:
    build:
      context: .
    ports:
      - 8080:8080
```

#### Step 3: Create the .dockerignore file

The `.dockerignore` file tells Docker which files and folders to exclude when building the image.

> [!NOTE]
> This helps:
>
> - Reduce image size
> - Speed up the build process
> - Prevent sensitive or unnecessary files (like `.env`, `.git`, or `node_modules`) from being added to the final image.
>
> To learn more, visit the [.dockerignore reference](/reference/dockerfile/#dockerignore-file).

Create a file named `.dockerignore` with the following contents:

```dockerignore
# ================================
# Node and build output
# ================================
node_modules
dist
out-tsc
.angular
.cache
.tmp

# ================================
# Testing & Coverage
# ================================
coverage
jest
cypress
cypress/screenshots
cypress/videos
reports
playwright-report
.vite
.vitepress

# ================================
# Environment & log files
# ================================
*.env*
!*.env.production
*.log
*.tsbuildinfo

# ================================
# IDE & OS-specific files
# ================================
.vscode
.idea
.DS_Store
Thumbs.db
*.swp

# ================================
# Version control & CI files
# ================================
.git
.gitignore

# ================================
# Docker & local orchestration
# ================================
Dockerfile
Dockerfile.*
.dockerignore
docker-compose.yml
docker-compose*.yml

# ================================
# Miscellaneous
# ================================
*.bak
*.old
*.tmp
```

#### Step 4: Create the `nginx.conf` file

To serve your Angular application efficiently inside the container, you鈥檒l configure Nginx with a custom setup. This configuration is optimized for performance, browser caching, gzip compression, and support for client-side routing.

Create a file named `nginx.conf` in the root of your project directory, and add the following content:

> [!NOTE]
> To learn more about configuring Nginx, see the [official Nginx documentation](https://nginx.org/en/docs/).

```nginx
worker_processes auto;

pid /tmp/nginx.pid;

events {
    worker_connections 1024;
}

http {
    include       /etc/nginx/mime.types;
    default_type  application/octet-stream;

    client_body_temp_path /tmp/client_temp;
    proxy_temp_path       /tmp/proxy_temp_path;
    fastcgi_temp_path     /tmp/fastcgi_temp;
    uwsgi_temp_path       /tmp/uwsgi_temp;
    scgi_temp_path        /tmp/scgi_temp;

    # Logging
    access_log off;
    error_log  /dev/stderr warn;

    # Performance
    sendfile        on;
    tcp_nopush      on;
    tcp_nodelay     on;
    keepalive_timeout  65;
    keepalive_requests 1000;

    # Compression
    gzip on;
    gzip_vary on;
    gzip_proxied any;
    gzip_min_length 256;
    gzip_comp_level 6;
    gzip_types
        text/plain
        text/css
        text/xml
        text/javascript
        application/javascript
        application/x-javascript
        application/json
        application/xml
        application/xml+rss
        font/ttf
        font/otf
        image/svg+xml;

    server {
        listen       8080;
        server_name  localhost;

        root /usr/share/nginx/html;
        index index.html;

        # Angular Routing
        location / {
            try_files $uri $uri/ /index.html;
        }

        # Static Assets Caching
        location ~* \.(?:ico|css|js|gif|jpe?g|png|woff2?|eot|ttf|svg|map)$ {
            expires 1y;
            access_log off;
            add_header Cache-Control "public, immutable";
        }

        # Optional: Explicit asset route
        location /assets/ {
            expires 1y;
            add_header Cache-Control "public, immutable";
        }
    }
}
```

#### Step 5: Build the Angular application image

With your custom configuration in place, you're now ready to build the Docker image for your Angular application.

The updated setup includes:

- The updated setup includes a clean, production-ready Nginx configuration tailored specifically for Angular.
- Efficient multi-stage Docker build, ensuring a small and secure final image.

After completing the previous steps, your project directory should now contain the following files:

```text
鈹溾攢鈹€ docker-angular-sample/
鈹� 鈹溾攢鈹€ Dockerfile
鈹� 鈹溾攢鈹€ .dockerignore
鈹� 鈹溾攢鈹€ compose.yaml
鈹� 鈹斺攢鈹€ nginx.conf
```

Now that your Dockerfile is configured, you can build the Docker image for your Angular application.

> [!NOTE]
> The `docker build` command packages your application into an image using the instructions in the Dockerfile. It includes all necessary files from the current directory (called the [build context](/build/concepts/context/#what-is-a-build-context)).

Run the following command from the root of your project:

```console
$ docker build --tag docker-angular-sample .
```

What this command does:

- Uses the Dockerfile in the current directory (.)
- Packages the application and its dependencies into a Docker image
- Tags the image as docker-angular-sample so you can reference it later

#### Step 6: View local images

After building your Docker image, you can check which images are available on your local machine using either the Docker CLI or [Docker Desktop](/desktop/use-desktop/images/). Since you're already working in the terminal, let's use the Docker CLI.

To list all locally available Docker images, run the following command:

```console
$ docker images
```

Example Output:

```shell
REPOSITORY                TAG               IMAGE ID       CREATED         SIZE
docker-angular-sample     latest            34e66bdb9d40   14 seconds ago   76.4MB
```

This output provides key details about your images:

- **Repository** 鈥� The name assigned to the image.
- **Tag** 鈥� A version label that helps identify different builds (e.g., latest).
- **Image ID** 鈥� A unique identifier for the image.
- **Created** 鈥� The timestamp indicating when the image was built.
- **Size** 鈥� The total disk space used by the image.

If the build was successful, you should see `docker-angular-sample` image listed.

---

### Run the containerized application

In the previous step, you created a Dockerfile for your Angular application and built a Docker image using the docker build command. Now it鈥檚 time to run that image in a container and verify that your application works as expected.

Inside the `docker-angular-sample` directory, run the following command in a
terminal.

```console
$ docker compose up --build
```

Open a browser and view the application at [http://localhost:8080](http://localhost:8080). You should see a simple Angular web application.

Press `ctrl+c` in the terminal to stop your application.

#### Run the application in the background

You can run the application detached from the terminal by adding the `-d`
option. Inside the `docker-angular-sample` directory, run the following command
in a terminal.

```console
$ docker compose up --build -d
```

Open a browser and view the application at [http://localhost:8080](http://localhost:8080). You should see your Angular application running in the browser.

To confirm that the container is running, use `docker ps` command:

```console
$ docker ps
```

This will list all active containers along with their ports, names, and status. Look for a container exposing port 8080.

Example Output:

```shell
CONTAINER ID   IMAGE                          COMMAND                  CREATED             STATUS             PORTS                    NAMES
eb13026806d1   docker-angular-sample-server   "nginx -c /etc/nginx鈥�"   About a minute ago  Up About a minute  0.0.0.0:8080->8080/tcp   docker-angular-sample-server-1
```

To stop the application, run:

```console
$ docker compose down
```

> [!NOTE]
> For more information about Compose commands, see the [Compose CLI
> reference](/reference/cli/docker/compose/).

---

## Use containers for Angular development

### Prerequisites

Complete [Containerize Angular application](/guides).

---

### Overview

In this section, you'll learn how to set up both production and development environments for your containerized Angular application using Docker Compose. This setup allows you to serve a static production build via Nginx and to develop efficiently inside containers using a live-reloading dev server with Compose Watch.

You鈥檒l learn how to:

- Configure separate containers for production and development
- Enable automatic file syncing using Compose Watch in development
- Debug and live-preview your changes in real-time without manual rebuilds

---

### Automatically update services (development mode)

Use Compose Watch to automatically sync source file changes into your containerized development environment. This provides a seamless, efficient development experience without restarting or rebuilding containers manually.

### Step 1: Create a development Dockerfile

Create a file named `Dockerfile.dev` in your project root with the following content:

```dockerfile
# =========================================
# Stage 1: Development - Angular Application
# =========================================

# Define the Node.js version to use (Alpine for a small footprint)
ARG NODE_VERSION=24.12.0-alpine

# Set the base image for development
FROM node:${NODE_VERSION} AS dev

# Set environment variable to indicate development mode
ENV NODE_ENV=development

# Set the working directory inside the container
WORKDIR /app

# Copy only the dependency files first to optimize Docker caching
COPY package.json package-lock.json* ./

# Install dependencies using npm with caching to speed up subsequent builds
RUN --mount=type=cache,target=/root/.npm npm install

# Copy all application source files into the container
COPY . .

# Expose the port Angular uses for the dev server (default is 4200)
EXPOSE 4200

# Start the Angular dev server and bind it to all network interfaces
CMD ["npm", "start", "--", "--host=0.0.0.0"]

```

This file sets up a lightweight development environment for your Angular application using the dev server.

#### Step 2: Update your `compose.yaml` file

Open your `compose.yaml` file and define two services: one for production (`angular-prod`) and one for development (`angular-dev`).

Here鈥檚 an example configuration for an Angular application:

```yaml
services:
  angular-prod:
    build:
      context: .
      dockerfile: Dockerfile
    image: docker-angular-sample
    ports:
      - "8080:8080"

  angular-dev:
    build:
      context: .
      dockerfile: Dockerfile.dev
    ports:
      - "4200:4200"
    develop:
      watch:
        - action: sync
          path: .
          target: /app
```

- The `angular-prod` service builds and serves your static production app using Nginx.
- The `angular-dev` service runs your Angular development server with live reload and hot module replacement.
- `watch` triggers file sync with Compose Watch.

> [!NOTE]
> For more details, see the official guide: [Use Compose Watch](/compose/how-tos/file-watch/).

After completing the previous steps, your project directory should now contain the following files:

```text
鈹溾攢鈹€ docker-angular-sample/
鈹� 鈹溾攢鈹€ Dockerfile
鈹� 鈹溾攢鈹€ Dockerfile.dev
鈹� 鈹溾攢鈹€ .dockerignore
鈹� 鈹溾攢鈹€ compose.yaml
鈹� 鈹斺攢鈹€ nginx.conf
```

#### Step 4: Start Compose Watch

Run the following command from the project root to start the container in watch mode

```console
$ docker compose watch angular-dev
```

#### Step 5: Test Compose Watch with Angular

To verify that Compose Watch is working correctly:

1. Open the `src/app/app.component.html` file in your text editor.

2. Locate the following line:

   ```html
   <h1>Docker Angular Sample Application</h1>
   ```

3. Change it to:

   ```html
   <h1>Hello from Docker Compose Watch</h1>
   ```

4. Save the file.

5. Open your browser at [http://localhost:4200](http://localhost:4200).

You should see the updated text appear instantly, without needing to rebuild the container manually. This confirms that file watching and automatic synchronization are working as expected.

---

## Run Angular tests in a container

### Prerequisites

Complete all the previous sections of this guide, starting with [Containerize Angular application](/guides).

### Overview

Testing is a critical part of the development process. In this section, you'll learn how to:

- Run Jasmine unit tests using the Angular CLI inside a Docker container.
- Use Docker Compose to isolate your test environment.
- Ensure consistency between local and container-based testing.

The `docker-angular-sample` project comes pre-configured with Jasmine, so you can get started quickly without extra setup.

---

### Run tests during development

The `docker-angular-sample` application includes a sample test file at the following location:

```console
$ src/app/app.component.spec.ts
```

This test uses Jasmine to validate the AppComponent logic.

#### Step 1: Update compose.yaml

Add a new service named `angular-test` to your `compose.yaml` file. This service allows you to run your test suite in an isolated, containerized environment.

```yaml {hl_lines="22-26",linenos=true}
services:
  angular-dev:
    build:
      context: .
      dockerfile: Dockerfile.dev
    ports:
      - "5173:5173"
    develop:
      watch:
        - action: sync
          path: .
          target: /app

  angular-prod:
    build:
      context: .
      dockerfile: Dockerfile
    image: docker-angular-sample
    ports:
      - "8080:8080"

  angular-test:
    build:
      context: .
      dockerfile: Dockerfile.dev
    command: ["npm", "run", "test"]
```

The angular-test service reuses the same `Dockerfile.dev` used for [development](#use-containers-for-angular-development) and overrides the default command to run tests with `npm run test`. This setup ensures a consistent test environment that matches your local development configuration.

After completing the previous steps, your project directory should contain the following files:

```text
鈹溾攢鈹€ docker-angular-sample/
鈹� 鈹溾攢鈹€ Dockerfile
鈹� 鈹溾攢鈹€ Dockerfile.dev
鈹� 鈹溾攢鈹€ .dockerignore
鈹� 鈹溾攢鈹€ compose.yaml
鈹� 鈹斺攢鈹€ nginx.conf
```

#### Step 2: Run the tests

To execute your test suite inside the container, run the following command from your project root:

```console
$ docker compose run --rm angular-test
```

This command will:

- Start the `angular-test` service defined in your `compose.yaml` file.
- Execute the `npm run test` script using the same environment as development.
- Automatically removes the container after tests complete, using the [`docker compose run --rm`](/reference/cli/docker/compose/run/) command.

You should see output similar to the following:

```shell
Test Suites: 1 passed, 1 total
Tests:       3 passed, 3 total
Snapshots:   0 total
Time:        1.529 s
```

> [!NOTE]
> For more information about Compose commands, see the [Compose CLI
> reference](/reference/cli/docker/compose/).

---

### Summary

In this section, you learned how to run unit tests for your Angular application inside a Docker container using Jasmine and Docker Compose.

What you accomplished:

- Created a `angular-test` service in `compose.yaml` to isolate test execution.
- Reused the development `Dockerfile.dev` to ensure consistency between dev and test environments.
- Ran tests inside the container using `docker compose run --rm angular-test`.
- Ensured reliable, repeatable testing across environments without depending on your local machine setup.

---

### Related resources

Explore official references and best practices to sharpen your Docker testing workflow:

- [Dockerfile reference](/reference/dockerfile/) 鈥� Understand all Dockerfile instructions and syntax.
- [Best practices for writing Dockerfiles](/develop/develop-images/dockerfile_best-practices/) 鈥� Write efficient, maintainable, and secure Dockerfiles.
- [Compose file reference](/compose/compose-file/) 鈥� Learn the full syntax and options available for configuring services in `compose.yaml`.
- [`docker compose run` CLI reference](/reference/cli/docker/compose/run/) 鈥� Run one-off commands in a service container.

---

## Introduction to Azure Pipelines with Docker

# Introduction to Azure Pipelines with Docker

> This guide is a community contribution. Docker would like to thank [Kristiyan Velkov](https://www.linkedin.com/in/kristiyan-velkov-763130b3/) for his valuable contribution.

## Prerequisites

Before you begin, ensure you have the following requirements:

- A [Docker Hub account](https://hub.docker.com) with a generated access token.
- An active [Azure DevOps project](https://dev.azure.com/) with a connected [Git repository](https://learn.microsoft.com/en-us/azure/devops/repos/git/?view=azure-devops).
- A project that includes a valid [`Dockerfile`](https://docs.docker.com/engine/reference/builder/) at its root or appropriate build context.

## Overview

This guide walks you through building and pushing Docker images using [Azure Pipelines](https://azure.microsoft.com/en-us/products/devops/pipelines), enabling a streamlined and secure CI workflow for containerized applications. You鈥檒l learn how to:

- Configure Docker authentication securely.
- Set up an automated pipeline to build and push images.

## Set up Azure DevOps to work with Docker Hub

### Step 1: Configure a Docker Hub service connection

To securely authenticate with Docker Hub using Azure Pipelines:

1. Navigate to **Project Settings > Service Connections** in your Azure DevOps project.
2. Select **New service connection > Docker Registry**.
3. Choose **Docker Hub** and provide your Docker Hub credentials or access token.
4. Give the service connection a recognizable name, such as `my-docker-registry`.
5. Grant access only to the specific pipeline(s) that require it for improved security and least privilege.

> [!IMPORTANT]
>
> Avoid selecting the option to grant access to all pipelines unless absolutely necessary. Always apply the principle of least privilege.

### Step 2: Create your pipeline

Add the following `azure-pipelines.yml` file to the root of your repository:

```yaml
# Trigger pipeline on commits to the main branch
trigger:
  - main

# Trigger pipeline on pull requests targeting the main branch
pr:
  - main

# Define variables for reuse across the pipeline
variables:
  imageName: 'docker.io/$(dockerUsername)/my-image'
  buildTag: '$(Build.BuildId)'
  latestTag: 'latest'

stages:
  - stage: BuildAndPush
    displayName: Build and Push Docker Image
    jobs:
      - job: DockerJob
        displayName: Build and Push
        pool:
          vmImage: ubuntu-latest
          demands:
            - docker
        steps:
          - checkout: self
            displayName: Checkout Code

          - task: Docker@2
            displayName: Docker Login
            inputs:
              command: login
              containerRegistry: 'my-docker-registry'  # Service connection name

          - task: Docker@2
            displayName: Build Docker Image
            inputs:
              command: build
              repository: $(imageName)
              tags: |
                $(buildTag)
                $(latestTag)
              dockerfile: './Dockerfile'
              arguments: |
                --sbom=true
                --attest type=provenance
                --cache-from $(imageName):latest
            env:
              DOCKER_BUILDKIT: 1

          - task: Docker@2
            displayName: Push Docker Image
            condition: eq(variables['Build.SourceBranch'], 'refs/heads/main')
            inputs:
              command: push
              repository: $(imageName)
              tags: |
                $(buildTag)
                $(latestTag)

          # Optional: logout for self-hosted agents
          - script: docker logout
            displayName: Docker Logout (Self-hosted only)
            condition: ne(variables['Agent.OS'], 'Windows_NT')
```

## What this pipeline does

This pipeline automates the Docker image build and deployment process for the main branch. It ensures a secure and efficient workflow with best practices like caching, tagging, and conditional cleanup. Here's what it does:

- Triggers on commits and pull requests targeting the `main` branch.
- Authenticates securely with Docker Hub using an Azure DevOps service connection.
- Builds and tags the Docker image using Docker BuildKit for caching.
- Pushes both buildId and latest tags to Docker Hub.
- Logs out from Docker if running on a self-hosted Linux agent.

## How the pipeline works

### Step 1: Define pipeline triggers 

```yaml
trigger:
  - main

pr:
  - main
```

This pipeline is triggered automatically on:
- Commits pushed to the `main` branch
- Pull requests targeting `main` main branch

> [!TIP]
> Learn more: [Define pipeline triggers in Azure Pipelines](https://learn.microsoft.com/en-us/azure/devops/pipelines/build/triggers?view=azure-devops)

### Step 2: Define common variables

```yaml
variables:
  imageName: 'docker.io/$(dockerUsername)/my-image'
  buildTag: '$(Build.BuildId)'
  latestTag: 'latest'
```

These variables ensure consistent naming, versioning, and reuse throughout the pipeline steps:

- `imageName`: your image path on Docker Hub
- `buildTag`: a unique tag for each pipeline run
- `latestTag`: a stable alias for your most recent image

> [!IMPORTANT]
>
> The variable `dockerUsername` is not set automatically.  
> Set it securely in your Azure DevOps pipeline variables:  
>   1. Go to **Pipelines > Edit > Variables**  
>   2. Add `dockerUsername` with your Docker Hub username  
>
> Learn more: [Define and use variables in Azure Pipelines](https://learn.microsoft.com/en-us/azure/devops/pipelines/process/variables?view=azure-devops&tabs=yaml%2Cbatch)
 
### Step 3: Define pipeline stages and jobs

```yaml
stages:
  - stage: BuildAndPush
    displayName: Build and Push Docker Image
```

This stage executes only if the source branch is `main`.

> [!TIP]
>
> Learn more: [Stage conditions in Azure Pipelines](https://learn.microsoft.com/en-us/azure/devops/pipelines/process/stages?view=azure-devops&tabs=yaml)

### Step 4: Job configuration

```yaml
jobs:
  - job: DockerJob
  displayName: Build and Push
  pool:
    vmImage: ubuntu-latest
    demands:
      - docker
```

This job utilizes the latest Ubuntu VM image with Docker support, provided by Microsoft-hosted agents. It can be replaced with a custom pool for self-hosted agents if necessary.

> [!TIP]
>
> Learn more: [Specify a pool in your pipeline](https://learn.microsoft.com/en-us/azure/devops/pipelines/agents/pools-queues?view=azure-devops&tabs=yaml%2Cbrowser)

#### Step 4.1: Checkout code

```yaml
steps:
  - checkout: self
    displayName: Checkout Code
```

This step pulls your repository code into the build agent, so the pipeline can access the Dockerfile and application files.

> [!TIP]
>
> Learn more: [checkout step documentation](https://learn.microsoft.com/en-us/azure/devops/pipelines/yaml-schema/steps-checkout?view=azure-pipelines)

#### Step 4.2: Authenticate to Docker Hub

```yaml
- task: Docker@2
  displayName: Docker Login
  inputs:
    command: login
    containerRegistry: 'my-docker-registry'  # Replace with your service connection name
```

Uses a pre-configured Azure DevOps Docker registry service connection to authenticate securely without exposing credentials directly.

> [!TIP]
>
> Learn more: [Use service connections for Docker Hub](https://learn.microsoft.com/en-us/azure/devops/pipelines/library/service-endpoints?view=azure-devops#docker-hub-or-others)

#### Step 4.3: Build the Docker image

```yaml
 - task: Docker@2
    displayName: Build Docker Image
    inputs:
      command: build
      repository: $(imageName)
      tags: |
          $(buildTag)
          $(latestTag)
      dockerfile: './Dockerfile'
      arguments: |
          --sbom=true
          --attest type=provenance
          --cache-from $(imageName):latest
    env:
      DOCKER_BUILDKIT: 1
```

This builds the image with:

- Two tags: one with the unique Build ID and one as latest
- Docker BuildKit enabled for faster builds and efficient layer caching
- Cache pull from the most recent pushed latest image
- Software Bill of Materials (SBOM) for supply chain transparency
- Provenance attestation to verify how and where the image was built

> [!TIP]
>
> Learn more: 
> - [Docker task for Azure Pipelines](https://learn.microsoft.com/en-us/azure/devops/pipelines/tasks/reference/docker-v2?view=azure-pipelines&tabs=yaml)
> - [Docker SBOM Attestations](/build/metadata/attestations/slsa-provenance/)

#### Step 4.4: Push the Docker image

```yaml
- task: Docker@2
  displayName: Push Docker Image
  condition: eq(variables['Build.SourceBranch'], 'refs/heads/main')
  inputs:
      command: push
      repository: $(imageName)
      tags: |
        $(buildTag)
        $(latestTag)
```

By applying this condition, the pipeline builds the Docker image on every run to ensure early detection of issues, but only pushes the image to the registry when changes are merged into the main branch鈥攌eeping your Docker Hub clean and focused

This uploads both tags to Docker Hub:
- `$(buildTag)` ensures traceability per run.
- `latest` is used for most recent image references.

#### Step 4.5  Logout of Docker (self-hosted agents)

```yaml
- script: docker logout
  displayName: Docker Logout (Self-hosted only)
  condition: ne(variables['Agent.OS'], 'Windows_NT')
```

Executes docker logout at the end of the pipeline on Linux-based self-hosted agents to proactively clean up credentials and enhance security posture.

## Summary

With this Azure Pipelines CI setup, you get:

- Secure Docker authentication using a built-in service connection.
- Automated image building and tagging triggered by code changes.
- Efficient builds leveraging Docker BuildKit cache.
- Safe cleanup with logout on persistent agents.
- Build images that meet modern software supply chain requirements with SBOM and attestation

## Learn more

- [Azure Pipelines Documentation](https://learn.microsoft.com/en-us/azure/devops/pipelines/?view=azure-devops): Comprehensive guide to configuring and managing CI/CD pipelines in Azure DevOps.
- [Docker Task for Azure Pipelines](https://learn.microsoft.com/en-us/azure/devops/pipelines/tasks/build/docker): Detailed reference for using the Docker task in Azure Pipelines to build and push images.
- [Docker Buildx Bake](/build/bake/): Explore Docker's advanced build tool for complex, multi-stage, and multi-platform build setups. See also the [Mastering Buildx Bake Guide](/guides/bake/) for practical examples and best practices.
- [Docker Build Cloud](/guides/docker-build-cloud/): Learn about Docker's managed build service for faster, scalable, and multi-platform image builds in the cloud.

---

## Mastering multi-platform builds, testing, and more with Docker Buildx Bake

# Mastering multi-platform builds, testing, and more with Docker Buildx Bake

This guide demonstrates how to simplify and automate the process of building
images, testing, and generating build artifacts using Docker Buildx Bake. By
defining build configurations in a declarative `docker-bake.hcl` file, you can
eliminate manual scripts and enable efficient workflows for complex builds,
testing, and artifact generation.

## Assumptions

This guide assumes that you're familiar with:

- Docker
- [Buildx](/build/concepts/overview/#buildx)
- [BuildKit](/build/concepts/overview/#buildkit)
- [Multi-stage builds](/build/building/multi-stage/)
- [Multi-platform builds](/build/building/multi-platform/)

## Prerequisites

- You have a recent version of Docker installed on your machine.
- You have Git installed for cloning repositories.
- You're using the [containerd](/desktop/features/containerd/) image store.

## Introduction

This guide uses an example project to demonstrate how Docker Buildx Bake can
streamline your build and test workflows. The repository includes both a
Dockerfile and a `docker-bake.hcl` file, giving you a ready-to-use setup to try
out Bake commands.

Start by cloning the example repository:

```bash
git clone https://github.com/dvdksn/bakeme.git
cd bakeme
```

The Bake file, `docker-bake.hcl`, defines the build targets in a declarative
syntax, using targets and groups, allowing you to manage complex builds
efficiently.

Here's what the Bake file looks like out-of-the-box:

```hcl
target "default" {
  target = "image"
  tags = [
    "bakeme:latest",
  ]
  attest = [
    "type=provenance,mode=max",
    "type=sbom",
  ]
  platforms = [
    "linux/amd64",
    "linux/arm64",
    "linux/riscv64",
  ]
}
```

The `target` keyword defines a build target for Bake. The `default` target
defines the target to build when no specific target is specified on the command
line. Here's a quick summary of the options for the `default` target:

- `target`: The target build stage in the Dockerfile.
- `tags`: Tags to assign to the image.
- `attest`: [Attestations](/build/metadata/attestations/) to attach to the image.

  > [!TIP]
  > The attestations provide metadata such as build provenance, which tracks
  > the source of the image's build, and an SBOM (Software Bill of Materials),
  > useful for security audits and compliance.

- `platforms`: Platform variants to build.

To execute this build, run the following command in the root of the
repository:

```console
$ docker buildx bake
```

With Bake, you avoid long, hard-to-remember command-line incantations,
simplifying build configuration management by replacing manual, error-prone
scripts with a structured configuration file.

For contrast, here's what this build command would look like without Bake:

```console
$ docker buildx build \
  --target=image \
  --tag=bakeme:latest \
  --provenance=true \
  --sbom=true \
  --platform=linux/amd64,linux/arm64,linux/riscv64 \
  .
```

## Testing and linting

Bake isn't just for defining build configurations and running builds. You can
also use Bake to run your tests, effectively using BuildKit as a task runner.
Running your tests in containers is great for ensuring reproducible results.
This section shows how to add two types of tests:

- Unit testing with `go test`.
- Linting for style violations with `golangci-lint`.

In Test-Driven Development (TDD) fashion, start by adding a new `test` target
to the Bake file:

```hcl
target "test" {
  target = "test"
  output = ["type=cacheonly"]
}
```

> [!TIP]
> Using `type=cacheonly` ensures that the build output is effectively
> discarded; the layers are saved to BuildKit's cache, but Buildx will not
> attempt to load the result to the Docker Engine's image store.
>
> For test runs, you don't need to export the build output 鈥� only the test
> execution matters.

To execute this Bake target, run `docker buildx bake test`. At this time,
you'll receive an error indicating that the `test` stage does not exist in the
Dockerfile.

```console
$ docker buildx bake test
[+] Building 1.2s (6/6) FINISHED
 => [internal] load local bake definitions
...
ERROR: failed to solve: target stage "test" could not be found
```

To satisfy this target, add the corresponding Dockerfile target. The `test`
stage here is based on the same base stage as the build stage.

```dockerfile
FROM base AS test
RUN --mount=target=. \
    --mount=type=cache,target=/go/pkg/mod \
    go test .
```

> [!TIP]
> The [`--mount=type=cache` directive](/build/cache/optimize/#use-cache-mounts)
> caches Go modules between builds, improving build performance by avoiding the
> need to re-download dependencies. This shared cache ensures that the same
> dependency set is available across build, test, and other stages.

Now, running the `test` target with Bake will evaluate the unit tests for this
project. If you want to verify that it works, you can make an arbitrary change
to `main_test.go` to cause the test to fail.

Next, to enable linting, add another target to the Bake file, named `lint`:

```hcl
target "lint" {
  target = "lint"
  output = ["type=cacheonly"]
}
```

And in the Dockerfile, add the build stage. This stage will use the official
`golangci-lint` image on Docker Hub.

> [!TIP]
> Because this stage relies on executing an external dependency, it's generally
> a good idea to define the version you want to use as a build argument. This
> lets you manage version upgrades in the future by collocating
> dependency versions to the beginning of the Dockerfile.

```dockerfile {hl_lines=[2,"6-8"]}
ARG GO_VERSION="1.23"
ARG GOLANGCI_LINT_VERSION="1.61"

#...

FROM golangci/golangci-lint:v${GOLANGCI_LINT_VERSION}-alpine AS lint
RUN --mount=target=.,rw \
    golangci-lint run
```

Lastly, to enable running both tests simultaneously, you can use the `groups`
construct in the Bake file. A group can specify multiple targets to run with a
single invocation.

```hcl
group "validate" {
  targets = ["test", "lint"]
}
```

Now, running both tests is as simple as:

```console
$ docker buildx bake validate
```

## Building variants

Sometimes you need to build more than one version of a program. The following
example uses Bake to build separate "release" and "debug" variants of the
program, using [matrices](/build/bake/matrices/). Using matrices lets
you run parallel builds with different configurations, saving time and ensuring
consistency.

A matrix expands a single build into multiple builds, each representing a
unique combination of matrix parameters. This means you can orchestrate Bake
into building both the production and development build of your program in
parallel, with minimal configuration changes.

The example project for this guide is set up to use a build-time option to
conditionally enable debug logging and tracing capabilities.

- If you compile the program with `go build -tags="debug"`, the additional
  logging and tracing capabilities are enabled (development mode).
- If you build without the `debug` tag, the program is compiled with a default
  logger (production mode).

Update the Bake file by adding a matrix attribute which defines the variable
combinations to build:

```diff {title="docker-bake.hcl"}
 target "default" {
+  matrix = {
+    mode = ["release", "debug"]
+  }
+  name = "image-${mode}"
   target = "image"
```

The `matrix` attribute defines the variants to build ("release" and "debug").
The `name` attribute defines how the matrix gets expanded into multiple
distinct build targets. In this case, the matrix attribute expands the build
into two workflows: `image-release` and `image-debug`, each using different
configuration parameters.

Next, define a build argument named `BUILD_TAGS` which takes the value of the
matrix variable.

```diff {title="docker-bake.hcl"}
   target = "image"
+  args = {
+    BUILD_TAGS = mode
+  }
   tags = [
```

You'll also want to change how the image tags are assigned to these builds.
As written, both matrix paths would generate the same image tag names, and
overwrite each other. Update the `tags` attribute use a conditional operator to
set the tag depending on the matrix variable value.

```diff {title="docker-bake.hcl"}
   tags = [
-    "bakeme:latest",
+    mode == "release" ? "bakeme:latest" : "bakeme:dev"
   ]
```

- If `mode` is `release`, the tag name is `bakeme:latest`
- If `mode` is `debug`, the tag name is `bakeme:dev`

Finally, update the Dockerfile to consume the `BUILD_TAGS` argument during the
compilation stage. When the `-tags="${BUILD_TAGS}"` option evaluates to
`-tags="debug"`, the compiler uses the `configureLogging` function in the
[`debug.go`](https://github.com/dvdksn/bakeme/blob/75c8a41e613829293c4bd3fc3b4f0c573f458f42/debug.go#L1)
file.

```diff {title=Dockerfile}
 # build compiles the program
 FROM base AS build
-ARG TARGETOS TARGETARCH
+ARG TARGETOS TARGETARCH BUILD_TAGS
 ENV GOOS=$TARGETOS
 ENV GOARCH=$TARGETARCH
 RUN --mount=target=. \
        --mount=type=cache,target=/go/pkg/mod \
-       go build -o "/usr/bin/bakeme" .
+       go build -tags="${BUILD_TAGS}" -o "/usr/bin/bakeme" .
```

That's all. With these changes, your `docker buildx bake` command now builds
two multi-platform image variants. You can introspect the canonical build
configuration that Bake generates using the `docker buildx bake --print`
command. Running this command shows that Bake will run a `default` group with
two targets with different build arguments and image tags.

```json {collapse=true}
{
  "group": {
    "default": {
      "targets": ["image-release", "image-debug"]
    }
  },
  "target": {
    "image-debug": {
      "attest": ["type=provenance,mode=max", "type=sbom"],
      "context": ".",
      "dockerfile": "Dockerfile",
      "args": {
        "BUILD_TAGS": "debug"
      },
      "tags": ["bakeme:dev"],
      "target": "image",
      "platforms": ["linux/amd64", "linux/arm64", "linux/riscv64"]
    },
    "image-release": {
      "attest": ["type=provenance,mode=max", "type=sbom"],
      "context": ".",
      "dockerfile": "Dockerfile",
      "args": {
        "BUILD_TAGS": "release"
      },
      "tags": ["bakeme:latest"],
      "target": "image",
      "platforms": ["linux/amd64", "linux/arm64", "linux/riscv64"]
    }
  }
}
```

Factoring in all of the platform variants as well, this means that the build
configuration generates 6 different images.

```console
$ docker buildx bake
$ docker image ls --tree

IMAGE                   ID             DISK USAGE   CONTENT SIZE   USED
bakeme:dev              f7cb5c08beac       49.3MB         28.9MB
鈹溾攢 linux/riscv64        0eae8ba0367a       9.18MB         9.18MB
鈹溾攢 linux/arm64          56561051c49a         30MB         9.89MB
鈹斺攢 linux/amd64          e8ca65079c1f        9.8MB          9.8MB

bakeme:latest           20065d2c4d22       44.4MB         25.9MB
鈹溾攢 linux/riscv64        7cc82872695f       8.21MB         8.21MB
鈹溾攢 linux/arm64          e42220c2b7a3       27.1MB         8.93MB
鈹斺攢 linux/amd64          af5b2dd64fde       8.78MB         8.78MB
```

## Exporting build artifacts

Exporting build artifacts like binaries can be useful for deploying to
environments without Docker or Kubernetes. For example, if your programs are
meant to be run on a user's local machine.

> [!TIP]
> The techniques discussed in this section can be applied not only to build
> output like binaries, but to any type of artifacts, such as test reports.

With programming languages like Go and Rust where the compiled binaries are
usually portable, creating alternate build targets for exporting only the
binary is trivial. All you need to do is add an empty stage in the Dockerfile
containing nothing but the binary that you want to export.

First, let's add a quick way to build a binary for your local platform and
export it to `./build/local` on the local filesystem.

In the `docker-bake.hcl` file, create a new `bin` target. In this stage, set
the `output` attribute to a local filesystem path. Buildx automatically detects
that the output looks like a filepath, and exports the results to the specified
path using the [local exporter](/build/exporters/local-tar/).

```hcl
target "bin" {
  target = "bin"
  output = ["build/bin"]
  platforms = ["local"]
}
```

Notice that this stage specifies a `local` platform. By default, if `platforms`
is unspecified, builds target the OS and architecture of the BuildKit host. If
you're using Docker Desktop, this often means builds target `linux/amd64` or
`linux/arm64`, even if your local machine is macOS or Windows, because Docker
runs in a Linux VM. Using the `local` platform forces the target platform to
match your local environment.

Next, add the `bin` stage to the Dockerfile which copies the compiled binary
from the build stage.

```dockerfile
FROM scratch AS bin
COPY --from=build "/usr/bin/bakeme" /
```

Now you can export your local platform version of the binary with `docker
buildx bake bin`. For example, on macOS, this build target generates an
executable in the [Mach-O format](https://en.wikipedia.org/wiki/Mach-O) 鈥� the
standard executable format for macOS.

```console
$ docker buildx bake bin
$ file ./build/bin/bakeme
./build/bin/bakeme: Mach-O 64-bit executable arm64
```

Next, let's add a target to build all of the platform variants of the program.
To do this, you can [inherit](/build/bake/inheritance/) the `bin`
target that you just created, and extend it by adding the desired platforms.

```hcl
target "bin-cross" {
  inherits = ["bin"]
  platforms = [
    "linux/amd64",
    "linux/arm64",
    "linux/riscv64",
  ]
}
```

Now, building the `bin-cross` target creates binaries for all platforms.
Subdirectories are automatically created for each variant.

```console
$ docker buildx bake bin-cross
$ tree build/
build/
鈹斺攢鈹€ bin
    鈹溾攢鈹€ bakeme
    鈹溾攢鈹€ linux_amd64
    鈹�   鈹斺攢鈹€ bakeme
    鈹溾攢鈹€ linux_arm64
    鈹�   鈹斺攢鈹€ bakeme
    鈹斺攢鈹€ linux_riscv64
        鈹斺攢鈹€ bakeme

5 directories, 4 files
```

To also generate "release" and "debug" variants, you can use a matrix just like
you did with the default target. When using a matrix, you also need to
differentiate the output directory based on the matrix value, otherwise the
binary gets written to the same location for each matrix run.

```hcl
target "bin-all" {
  inherits = ["bin-cross"]
  matrix = {
    mode = ["release", "debug"]
  }
  name = "bin-${mode}"
  args = {
    BUILD_TAGS = mode
  }
  output = ["build/bin/${mode}"]
}
```

```console
$ rm -r ./build/
$ docker buildx bake bin-all
$ tree build/
build/
鈹斺攢鈹€ bin
    鈹溾攢鈹€ debug
    鈹�   鈹溾攢鈹€ linux_amd64
    鈹�   鈹�   鈹斺攢鈹€ bakeme
    鈹�   鈹溾攢鈹€ linux_arm64
    鈹�   鈹�   鈹斺攢鈹€ bakeme
    鈹�   鈹斺攢鈹€ linux_riscv64
    鈹�       鈹斺攢鈹€ bakeme
    鈹斺攢鈹€ release
        鈹溾攢鈹€ linux_amd64
        鈹�   鈹斺攢鈹€ bakeme
        鈹溾攢鈹€ linux_arm64
        鈹�   鈹斺攢鈹€ bakeme
        鈹斺攢鈹€ linux_riscv64
            鈹斺攢鈹€ bakeme

10 directories, 6 files
```

## Conclusion

Docker Buildx Bake streamlines complex build workflows, enabling efficient
multi-platform builds, testing, and artifact export. By integrating Buildx Bake
into your projects, you can simplify your Docker builds, make your build
configuration portable, and wrangle complex configurations.

Experiment with different configurations and extend your Bake files to suit
your project's needs. You might consider integrating Bake into your CI/CD
pipelines to automate builds, testing, and artifact deployment. The flexibility
and power of Buildx Bake can significantly improve your development and
deployment processes.

### Further reading

For more information about how to use Bake, check out these resources:

- [Bake documentation](/build/bake/)
- [Matrix targets](/build/bake/matrices/)
- [Bake file reference](/build/bake/reference/)
- [Bake GitHub Action](https://github.com/docker/bake-action)

---

## Bun language-specific guide

# Bun language-specific guide

The Bun getting started guide teaches you how to create a containerized Bun application using Docker.

> **Acknowledgment**
>
> Docker would like to thank [Pradumna Saraf](https://twitter.com/pradumna_saraf) for his contribution to this guide.

## What will you learn?

- Containerize and run a Bun application using Docker
- Set up a local environment to develop a Bun application using containers

## Prerequisites

- Basic understanding of JavaScript is assumed.
- You must have familiarity with Docker concepts like containers, images, and Dockerfiles. If you are new to Docker, you can start with the [Docker basics](/get-started/docker-concepts/the-basics/what-is-a-container/) guide.

After completing the Bun getting started modules, you should be able to containerize your own Bun application based on the examples and instructions provided in this guide.

Start by containerizing an existing Bun application.

## Containerize a Bun application

### Prerequisites

- You have a [Git client](https://git-scm.com/downloads). The examples in this section use a command-line based Git client, but you can use any client.

### Overview

For a long time, Node.js has been the de-facto runtime for server-side
JavaScript applications. Recent years have seen a rise in new alternative
runtimes in the ecosystem, including [Bun website](https://bun.sh/). Like
Node.js, Bun is a JavaScript runtime. Bun is a comparatively lightweight
runtime that is designed to be fast and efficient.

Why develop Bun applications with Docker? Having multiple runtimes to choose
from is great. But as the number of runtimes increases, it becomes challenging
to manage the different runtimes and their dependencies consistently across
environments. This is where Docker comes in. Creating and destroying containers
on demand is a great way to manage the different runtimes and their
dependencies. Also, as it's fairly a new runtime, getting a consistent
development environment for Bun can be challenging. Docker can help you set up
a consistent development environment for Bun.

### Get the sample application

Clone the sample application to use with this guide. Open a terminal, change
directory to a directory that you want to work in, and run the following
command to clone the repository:

```console
$ git clone https://github.com/dockersamples/bun-docker.git && cd bun-docker
```

You should now have the following contents in your `bun-docker` directory.

```text
鈹溾攢鈹€ bun-docker/
鈹� 鈹溾攢鈹€ compose.yml
鈹� 鈹溾攢鈹€ Dockerfile
鈹� 鈹溾攢鈹€ LICENSE
鈹� 鈹溾攢鈹€ server.js
鈹� 鈹斺攢鈹€ README.md
```

### Create a Dockerfile

Before creating a Dockerfile, you need to choose a base image. You can either use the [Bun Docker Official Image](https://hub.docker.com/r/oven/bun) or a Docker Hardened Image (DHI) from the [Hardened Image catalog](https://hub.docker.com/hardened-images/catalog).

Choosing DHI offers the advantage of a production-ready image that is lightweight and secure. For more information, see [Docker Hardened Images](https://docs.docker.com/dhi/).

**Using Docker Hardened Images**

Docker Hardened Images (DHIs) are available for Bun in the [Docker Hardened Images catalog](https://hub.docker.com/hardened-images/catalog/dhi/bun). You can pull DHIs directly from the `dhi.io` registry.

1. Sign in to the DHI registry:

   ```console
   $ docker login dhi.io
   ```

2. Pull the Bun DHI as `dhi.io/bun:1`. The tag (`1`) in this example refers to the version to the latest 1.x version of Bun.

   ```console
   $ docker pull dhi.io/bun:1
   ```

For other available versions, refer to the [catalog](https://hub.docker.com/hardened-images/catalog/dhi/bun).

```dockerfile
# Use the DHI Bun image as the base image
FROM dhi.io/bun:1

# Set the working directory in the container
WORKDIR /app

# Copy the current directory contents into the container at /app
COPY . .

# Expose the port on which the API will listen
EXPOSE 3000

# Run the server when the container launches
CMD ["bun", "server.js"]
```

**Using the official image**

Using the Docker Official Image is straightforward. In the following Dockerfile, you'll notice that the `FROM` instruction uses `oven/bun` as the base image.

You can find the image on [Docker Hub](https://hub.docker.com/r/oven/bun). This is the Docker Official Image for Bun created by Oven, the company behind Bun, and it's available on Docker Hub.

```dockerfile
# Use the official Bun image
FROM oven/bun:latest

# Set the working directory in the container
WORKDIR /app

# Copy the current directory contents into the container at /app
COPY . .

# Expose the port on which the API will listen
EXPOSE 3000

# Run the server when the container launches
CMD ["bun", "server.js"]
```

In addition to specifying the base image, the Dockerfile also:

- Sets the working directory in the container to `/app`.
- Copies the content of the current directory to the `/app` directory in the container.
- Exposes port 3000, where the API is listening for requests.
- And finally, starts the server when the container launches with the command `bun server.js`.

### Run the application

Inside the `bun-docker` directory, run the following command in a terminal.

```console
$ docker compose up --build
```

Open a browser and view the application at [http://localhost:3000](http://localhost:3000). You will see a message `{"Status" : "OK"}` in the browser.

In the terminal, press `ctrl`+`c` to stop the application.

#### Run the application in the background

You can run the application detached from the terminal by adding the `-d`
option. Inside the `bun-docker` directory, run the following command
in a terminal.

```console
$ docker compose up --build -d
```

Open a browser and view the application at [http://localhost:3000](http://localhost:3000).

In the terminal, run the following command to stop the application.

```console
$ docker compose down
```

## Use containers for Bun development

### Prerequisites

Complete [Containerize a Bun application](/guides).

### Overview

In this section, you'll learn how to set up a development environment for your containerized application. This includes:

- Configuring Compose to automatically update your running Compose services as you edit and save your code

### Get the sample application

Clone the sample application to use with this guide. Open a terminal, change directory to a directory that you want to work in, and run the following command to clone the repository:

```console
$ git clone https://github.com/dockersamples/bun-docker.git && cd bun-docker
```

### Automatically update services

Use Compose Watch to automatically update your running Compose services as you
edit and save your code. For more details about Compose Watch, see [Use Compose
Watch](/compose/how-tos/file-watch/).

Open your `compose.yml` file in an IDE or text editor and then add the Compose Watch instructions. The following example shows how to add Compose Watch to your `compose.yml` file.

```yaml {hl_lines="9-12",linenos=true}
services:
  server:
    image: bun-server
    build:
      context: .
      dockerfile: Dockerfile
    ports:
      - "3000:3000"
    develop:
      watch:
        - action: rebuild
          path: .
```

Run the following command to run your application with Compose Watch.

```console
$ docker compose watch
```

Now, if you modify your `server.js` you will see the changes in real time without re-building the image.

To test it out, open the `server.js` file in your favorite text editor and change the message from `{"Status" : "OK"}` to `{"Status" : "Updated"}`. Save the file and refresh your browser at `http://localhost:3000`. You should see the updated message.

Press `ctrl+c` in the terminal to stop your application.

### Summary

In this section, you also learned how to use Compose Watch to automatically rebuild and run your container when you update your code.

Related information:

- [Compose file reference](/reference/compose-file/)
- [Compose file watch](/compose/how-tos/file-watch/)
- [Multi-stage builds](/build/building/multi-stage/)

---

## Use Claude Code with Docker Model Runner

# Use Claude Code with Docker Model Runner

This guide shows how to run Claude Code with Docker Model Runner as the backend
model provider. You'll point Claude Code at the local Anthropic-compatible API,
run a coding model, and package `gpt-oss` with a larger context window for
longer repository prompts.

> **Acknowledgment**
>
> Docker would like to thank [Pradumna Saraf](https://twitter.com/pradumna_saraf) for his contribution to this guide.

In this guide, you'll learn how to:

- Pull a coding model and start Claude Code with Docker Model Runner
- Make the endpoint configuration persistent
- Verify the local API endpoint and inspect requests
- Package `gpt-oss` with a larger context window for longer prompts

## Prerequisites

Before you start, make sure you have:

- [Docker Desktop](/get-started/get-docker/) or Docker Engine installed
- [Docker Model Runner enabled](/ai/model-runner/get-started/#enable-docker-model-runner)
- [Claude Code installed](https://code.claude.com/docs/en/quickstart)

If you use Docker Desktop, turn on TCP access in **Settings** > **AI**, or run:

```console
$ docker desktop enable model-runner --tcp 12434
```

## Step 1: Pull a coding model

Pull a model before you start Claude Code:

```console
$ docker model pull ai/devstral-small-2
```

You can also use `ai/qwen3-coder` if you want another coding-focused model with
a large context window.

## Step 2: Start Claude Code with Docker Model Runner

Set `ANTHROPIC_BASE_URL` to your local Docker Model Runner endpoint when you run
Claude Code.

On macOS or Linux:

```console
$ ANTHROPIC_BASE_URL=http://localhost:12434 claude --model ai/devstral-small-2
```

On Windows PowerShell:

```powershell
$env:ANTHROPIC_BASE_URL="http://localhost:12434"
claude --model ai/devstral-small-2
```

Claude Code now sends requests to Docker Model Runner instead of Anthropic's
hosted API.

## Step 3: Troubleshoot your first launch

If Claude Code can't connect, check Docker Model Runner status:

```console
$ docker model status
```

If Claude Code can't find the model, list local models:

```console
$ docker model ls
```

If the model is missing, pull it first. If needed, use the fully qualified
model name, such as `ai/devstral-small-2`.

## Step 4: Make the endpoint persistent

To avoid setting the environment variable each time, add it to your shell
profile:

```bash {title="~/.bashrc or ~/.zshrc"}
export ANTHROPIC_BASE_URL=http://localhost:12434
```

On Windows PowerShell, add it to your PowerShell profile:

```powershell {title="$PROFILE"}
$env:ANTHROPIC_BASE_URL = "http://localhost:12434"
```

After you reload your shell, you can run Claude Code with only the model flag:

```console
$ claude --model ai/devstral-small-2
```

## Step 5: Verify the API endpoint

Send a test request to confirm the Anthropic-compatible API is reachable:

```console
$ curl http://localhost:12434/v1/messages \
  -H "Content-Type: application/json" \
  -d '{
    "model": "ai/devstral-small-2",
    "max_tokens": 32,
    "messages": [{"role": "user", "content": "Say hello"}]
  }'
```

For more details about the request format, see the
[Anthropic-compatible API reference](/ai/model-runner/api-reference/#anthropic-compatible-api).

## Step 6: Inspect Claude Code requests

To inspect the requests Claude Code sends to Docker Model Runner, run:

```console
$ docker model requests --model ai/devstral-small-2 | jq .
```

This helps you debug prompts, context usage, and compatibility issues.

## Step 7: Package `gpt-oss` with a larger context window

`ai/gpt-oss` defaults to a smaller context window than coding-focused models. If
you want to use it for repository-scale prompts, package a larger variant:

```console
$ docker model pull ai/gpt-oss
$ docker model package --from ai/gpt-oss --context-size 32000 gpt-oss:32k
```

Then run Claude Code with the packaged model:

```console
$ ANTHROPIC_BASE_URL=http://localhost:12434 claude --model gpt-oss:32k
```

## Learn more

- [Docker Model Runner overview](/ai/model-runner/)
- [Docker Model Runner API reference](/ai/model-runner/api-reference/)
- [IDE and tool integrations](/ai/model-runner/ide-integrations/)

---

## Run Claude Code in a Docker Sandbox with Docker Model Runner

# Run Claude Code in a Docker Sandbox with Docker Model Runner

This guide shows how to run Claude Code inside a Docker Sandbox with Docker
Model Runner as the backend model provider. You'll keep the agent isolated
from your host in a microVM, point it at a local model on your machine, and
keep all model traffic on-device.

> **Acknowledgment**
>
> Docker would like to thank [Pradumna Saraf](https://twitter.com/pradumna_saraf) for his contribution to this guide.

In this guide, you'll learn how to:

- Pull a coding model and start Docker Model Runner with TCP enabled
- Allow the sandbox to reach Docker Model Runner on your host
- Create a Claude Code sandbox and set the local endpoint persistently
- Launch Claude Code with a local model and verify the connection
- Package `gpt-oss` with a larger context window for longer prompts

## How the pieces fit together

Three components cooperate at runtime:

- **Docker Model Runner** runs on your host and serves an
  Anthropic-compatible API at `http://localhost:12434`.
- **The Docker Sandbox** runs Claude Code inside an isolated microVM. The
  microVM has its own network and can't reach your host's `localhost`
  directly.
- **The sandbox proxy** sits on your host and brokers every outbound
  request from the sandbox. It enforces network policy and translates the
  special hostname `host.docker.internal` to `localhost`.

Claude Code inside the sandbox sends requests to
`http://host.docker.internal:12434`. The proxy rewrites the destination to
`localhost:12434`, which Docker Model Runner answers. No model traffic
leaves your machine.

## Prerequisites

Before you start, make sure you have:

- [Docker Desktop](/get-started/get-docker/) or Docker Engine installed
- [Docker Model Runner enabled](/ai/model-runner/get-started/#enable-docker-model-runner)
- [Docker Sandboxes (`sbx`) installed and signed in](/ai/sandboxes/get-started/#install-and-sign-in)

If you use Docker Desktop, turn on TCP access in **Settings** > **AI**, or
run:

```console
$ docker desktop enable model-runner --tcp 12434
```

## Step 1: Pull a coding model

Pull a model on your host before you create the sandbox:

```console
$ docker model pull ai/devstral-small-2
```

You can also use `ai/qwen3-coder` if you want another coding-focused model
with a large context window.

## Step 2: Allow the sandbox to reach Docker Model Runner

Sandboxes are network-isolated by default, so you need a policy rule before
the sandbox can reach Docker Model Runner.

The rule is matched against the destination the proxy forwards to, not the
hostname the sandbox uses. Because the proxy rewrites
`host.docker.internal` to `localhost` before forwarding, the rule allows
`localhost:12434` even though Claude Code will use `host.docker.internal`
in its requests:

```console
$ sbx policy allow network localhost:12434
```

For background on host access from sandboxes, see
[Accessing host services from a sandbox](/ai/sandboxes/usage/#accessing-host-services-from-a-sandbox).

## Step 3: Create a Claude Code sandbox

From your project directory, create a sandbox without launching the agent:

```console
$ cd ~/my-project
$ sbx create claude --name claude-dmr .
```

`sbx run` would also work, but it launches Claude Code immediately. Without
`ANTHROPIC_BASE_URL` set, Claude Code points at `api.anthropic.com` and
either prompts for OAuth or errors out before you can fix the endpoint.
Creating the sandbox first lets you write the local endpoint into it before
the agent starts.

You don't need to set an Anthropic API key or run `sbx secret set
anthropic`. Docker Model Runner doesn't authenticate the local endpoint,
and the sandbox proxy only injects credentials for requests bound for
`api.anthropic.com`. See
[Credentials](/ai/sandboxes/security/credentials/) for the full
list of services the proxy authenticates.

## Step 4: Set the local endpoint inside the sandbox

Append `ANTHROPIC_BASE_URL` to the sandbox's persistent environment file so
Claude Code reads it on every launch:

```console
$ sbx exec -d claude-dmr bash -c "echo 'export ANTHROPIC_BASE_URL=http://host.docker.internal:12434' >> /etc/sandbox-persistent.sh"
```

The `bash -c` wrapper ensures the `>>` redirect runs inside the sandbox, not
on your host. For details on this approach, see
[How do I set custom environment variables inside a sandbox?](/ai/sandboxes/faq/#how-do-i-set-custom-environment-variables-inside-a-sandbox).

To confirm the variable is set, open a shell in the sandbox:

```console
$ sbx exec -it claude-dmr bash
$ echo $ANTHROPIC_BASE_URL
http://host.docker.internal:12434
```

## Step 5: Verify connectivity to Docker Model Runner

Still inside the sandbox shell, send a test request to the host endpoint:

```console
$ curl http://host.docker.internal:12434/v1/messages \
  -H "Content-Type: application/json" \
  -d '{
    "model": "ai/devstral-small-2",
    "max_tokens": 32,
    "messages": [{"role": "user", "content": "Say hello"}]
  }'
```

A successful response confirms the policy rule and base URL are correct.
Type `exit` to leave the shell. For more details about the request format,
see the
[Anthropic-compatible API reference](/ai/model-runner/api-reference/#anthropic-compatible-api).

## Step 6: Launch Claude Code with the local model

Run Claude Code in the sandbox and pass the model flag through to the agent:

```console
$ sbx run claude-dmr -- --model ai/devstral-small-2
```

Everything after `--` is forwarded to the Claude Code CLI. Because
`ANTHROPIC_BASE_URL` is set in the sandbox's persistent environment, Claude
Code routes requests to Docker Model Runner on your host instead of
`api.anthropic.com`.

## Step 7: Inspect Claude Code requests

To inspect the requests Claude Code sends, run on your host:

```console
$ docker model requests --model ai/devstral-small-2 | jq .
```

This helps you debug prompts, context usage, and compatibility issues
without attaching to the sandbox.

## Step 8: Package `gpt-oss` with a larger context window

`ai/gpt-oss` defaults to a smaller context window than coding-focused
models. To use it for repository-scale prompts, package a larger variant on
the host:

```console
$ docker model pull ai/gpt-oss
$ docker model package --from ai/gpt-oss --context-size 32000 gpt-oss:32k
```

Then point Claude Code at the packaged model the next time you run the
sandbox:

```console
$ sbx run claude-dmr -- --model gpt-oss:32k
```

## Clean up

Sandboxes persist after Claude Code exits. To stop the sandbox without
deleting it:

```console
$ sbx stop claude-dmr
```

To remove the sandbox and everything inside, including the persistent
environment file:

```console
$ sbx rm claude-dmr
```

Files in your workspace are unaffected.

## Learn more

- [Use Claude Code with Docker Model Runner](/guides/claude-code-sandbox-model-runner/claude-code-model-runner/)
- [Get started with Docker Sandboxes](/ai/sandboxes/get-started/)
- [Claude Code in Docker Sandboxes](/ai/sandboxes/agents/claude-code/)
- [Docker Model Runner overview](/ai/model-runner/)
- [Docker Model Runner API reference](/ai/model-runner/api-reference/)

---

## Building Compose projects with Bake

# Building Compose projects with Bake

This guide explores how you can use Bake to build images for Docker Compose
projects with multiple services.

[Docker Buildx Bake](/build/bake/) is a build orchestration
tool that enables declarative configuration for your builds, much like Docker
Compose does for defining runtime stacks. For projects where Docker Compose is
used to spin up services for local development, Bake offers a way of seamlessly
extending the project with a production-ready build configuration.

## Prerequisites

This guide assumes that you're familiar with

- Docker Compose
- [Multi-stage builds](/build/building/multi-stage/)
- [Multi-platform builds](/build/building/multi-platform/)

## Orientation

This guide will use the
[dvdksn/example-voting-app](https://github.com/dvdksn/example-voting-app)
repository as an example of a monorepo using Docker Compose that can be
extended with Bake.

```console
$ git clone https://github.com/dvdksn/example-voting-app.git
$ cd example-voting-app
```

This repository uses Docker Compose to define the runtime configurations for
running the application, in the `compose.yaml` file. This app consists of the
following services:

| Service  | Description                                                            |
| -------- | ---------------------------------------------------------------------- |
| `vote`   | A front-end web app in Python which lets you vote between two options. |
| `result` | A Node.js web app which shows the results of the voting in real time.  |
| `worker` | A .NET worker which consumes votes and stores them in the database.    |
| `db`     | A Postgres database backed by a Docker volume.                         |
| `redis`  | A Redis instance which collects new votes.                             |
| `seed`   | A utility container that seeds the database with mock data.            |

The `vote`, `result`, and `worker` services are built from code in this
repository, whereas `db` and `redis` use pre-existing Postgres and Redis images
from Docker Hub. The `seed` service is a utility that invokes requests against
the front-end service to populate the database, for testing purposes.

## Build with Compose

When you spin up a Docker Compose project, any services that define the `build`
property are automatically built before the service is started. Here's the
build configuration for the `vote` service in the example repository:

```yaml {title="compose.yaml"}
services:
  vote:
    build:
      context: ./vote # Build context
      target: dev # Dockerfile stage
```

The `vote`, `result`, and `worker` services all have a build configuration
specified. Running `docker compose up` will trigger a build of these services.

Did you know that you can also use Compose just to build the service images?
The `docker compose build` command lets you invoke a build using the build
configuration specified in the Compose file. For example, to build the `vote`
service with this configuration, run:

```console
$ docker compose build vote
```

Omit the service name to build all services at once:

```console
$ docker compose build
```

The `docker compose build` command is useful when you only need to build images
without running services.

The Compose file format supports a number of properties for defining your
build's configuration. For example, to specify the tag name for the images, set
the `image` property on the service.

```yaml
services:
  vote:
    image: username/vote
    build:
      context: ./vote
      target: dev
    #...

  result:
    image: username/result
    build:
      context: ./result
    #...

  worker:
    image: username/worker
    build:
      context: ./worker
    #...
```

Running `docker compose build` creates three service images with fully
qualified image names that you can push to Docker Hub.

The `build` property supports a [wide range](/reference/compose-file/build/)
of options for configuring builds. However, building production-grade images
are often different from images used in local development. To avoid cluttering
your Compose file with build configurations that might not be desirable for
local builds, consider separating the production builds from the local builds
by using Bake to build images for release. This approach separates concerns:
using Compose for local development and Bake for production-ready builds, while
still reusing service definitions and fundamental build configurations.

## Build with Bake

Like Compose, Bake parses the build definition for a project from a
configuration file. Bake supports HashiCorp Configuration Language (HCL), JSON,
and the Docker Compose YAML format. When you use Bake with multiple files, it
will find and merge all of the applicable configuration files into one unified
build configuration. The build options defined in your Compose file are
extended, or in some cases overridden, by options specified in the Bake file.

The following section explores how you can use Bake to extend the build options
defined in your Compose file for production.

### View the build configuration

Bake automatically creates a build configuration from the `build` properties of
your services. Use the `--print` flag for Bake to view the build configuration
for a given Compose file. This flag evaluates the build configuration and
outputs the build definition in JSON format.

```console
$ docker buildx bake --print
```

The JSON-formatted output shows the group that would be executed, and all the
targets of that group. A group is a collection of builds, and a target
represents a single build.

```json
{
  "group": {
    "default": {
      "targets": [
        "vote",
        "result",
        "worker",
        "seed"
      ]
    }
  },
  "target": {
    "result": {
      "context": "result",
      "dockerfile": "Dockerfile",
    },
    "seed": {
      "context": "seed-data",
      "dockerfile": "Dockerfile",
    },
    "vote": {
      "context": "vote",
      "dockerfile": "Dockerfile",
      "target": "dev",
    },
    "worker": {
      "context": "worker",
      "dockerfile": "Dockerfile",
    }
  }
}
```

As you can see, Bake has created a `default` group that includes four targets:

- `seed`
- `vote`
- `result`
- `worker`

This group is created automatically from your Compose file; it includes all of
your services containing a build configuration. To build this group of services
with Bake, run:

```console
$ docker buildx bake
```

### Customize the build group

Start by redefining the default build group that Bake executes. The current
default group includes a `seed` target 鈥� a Compose service used solely to
populate the database with mock data. Since this target doesn't produce a
production image, it doesn't need to be included in the build group.

To customize the build configuration that Bake uses, create a new file at the
root of the repository, alongside your `compose.yaml` file, named
`docker-bake.hcl`.

```console
$ touch docker-bake.hcl
```

Open the Bake file and add the following configuration:

```hcl {title=docker-bake.hcl}
group "default" {
  targets = ["vote", "result", "worker"]
}
```

Save the file and print your Bake definition again.

```console
$ docker buildx bake --print
```

The JSON output shows that the `default` group only includes the targets you
care about.

```json
{
  "group": {
    "default": {
      "targets": ["vote", "result", "worker"]
    }
  },
  "target": {
    "result": {
      "context": "result",
      "dockerfile": "Dockerfile",
      "tags": ["username/result"]
    },
    "vote": {
      "context": "vote",
      "dockerfile": "Dockerfile",
      "tags": ["username/vote"],
      "target": "dev"
    },
    "worker": {
      "context": "worker",
      "dockerfile": "Dockerfile",
      "tags": ["username/worker"]
    }
  }
}
```

Here, the build configuration for each target (context, tags, etc.) is picked
up from the `compose.yaml` file. The group is defined by the `docker-bake.hcl`
file.

### Customize targets

The Compose file currently defines the `dev` stage as the build target for the
`vote` service. That's appropriate for the image that you would run in local
development, because the `dev` stage includes additional development
dependencies and configurations. For the production image, however, you'll want
to target the `final` image instead.

To modify the target stage used by the `vote` service, add the following
configuration to the Bake file:

```hcl
target "vote" {
  target = "final"
}
```

This overrides the `target` property specified in the Compose file with a
different value when you run the build with Bake. The other build options in
the Compose file (tag, context) remain unmodified. You can verify by inspecting
the build configuration for the `vote` target with `docker buildx bake --print
vote`:

```json
{
  "group": {
    "default": {
      "targets": ["vote"]
    }
  },
  "target": {
    "vote": {
      "context": "vote",
      "dockerfile": "Dockerfile",
      "tags": ["username/vote"],
      "target": "final"
    }
  }
}
```

### Additional build features

Production-grade builds often have different characteristics from development
builds. Here are a few examples of things you might want to add for production
images.

Multi-platform
: For local development, you only need to build images for your local platform,
since those images are just going to run on your machine. But for images that
are pushed to a registry, it's often a good idea to build for multiple
platforms, arm64 and amd64 in particular.

Attestations
: [Attestations](/build/metadata/attestations/) are manifests
attached to the image that describe how the image was created and what
components it contains. Attaching attestations to your images helps ensure that
your images follow software supply chain best practices.

Annotations
: [Annotations](/build/metadata/annotations/) provide descriptive
metadata for images. Use annotations to record arbitrary information and attach
it to your image, which helps consumers and tools understand the origin,
contents, and how to use the image.

> [!TIP]
> Why not just define these additional build options in the Compose file
> directly?
>
> The `build` property in the Compose file format does not support all build
> features. Additionally, some features, like multi-platform builds, can
> drastically increase the time it takes to build a service. For local
> development, you're better off keeping your build step simple and fast,
> saving the bells and whistles for release builds.

To add these properties to the images you build with Bake, update the Bake file
as follows:

```hcl
group "default" {
  targets = ["vote", "result", "worker"]
}

target "_common" {
  annotations = ["org.opencontainers.image.authors=username"]
  platforms = ["linux/amd64", "linux/arm64"]
  attest = [
    "type=provenance,mode=max",
    "type=sbom"
  ]
}

target "vote" {
  inherits = ["_common"]
  target = "final"
}

target "result" {
  inherits = ["_common"]
}

target "worker" {
  inherits = ["_common"]
}
```

This defines a new `_common` target that defines reusable build configuration
for adding multi-platform support, annotations, and attestations to your
images. The reusable target is inherited by the build targets.

With these changes, building the project with Bake produces three sets of
multi-platform images for the `linux/amd64` and `linux/arm64` architectures.
Each image is decorated with an author annotation, and both SBOM and provenance
attestation records.

## Conclusions

The pattern demonstrated in this guide provides a useful approach for managing
production-ready Docker images in projects using Docker Compose. Using Bake
gives you access to all the powerful features of Buildx and BuildKit, and also
helps separate your development and build configuration in a reasonable way.

### Further reading

For more information about how to use Bake, check out these resources:

- [Bake documentation](/build/bake/)
- [Building with Bake from a Compose file](/build/bake/compose-file/)
- [Bake file reference](/build/bake/reference/)
- [Mastering multi-platform builds, testing, and more with Docker Buildx Bake](/guides/bake/)
- [Bake GitHub Action](https://github.com/docker/bake-action)

---

## Faster development and testing with container-supported development

# Faster development and testing with container-supported development

Containers offer a consistent way to build, share, and run applications across different environments. While containers are typically used to containerize your application, they also make it incredibly easy to run essential services needed for development. Instead of installing or connecting to a remote database, you can easily launch your own database. But the possibilities don't stop there.

With container-supported development, you use containers to enhance your development environment by emulating or running your own instances of the services your app needs. This provides faster feedback loops, less coupling with remote services, and a greater ability to test error states.

And best of all, you can have these benefits regardless of whether the main app under development is running in containers.

## What you'll learn

- The meaning of container-supported development
- How to connect non-containerized applications to containerized services
- Several examples of using containers to emulate or run local instances of services
- How to use containers to add additional troubleshooting and debugging tools to your development environment

## Who's this for?

- Teams that want to reduce the coupling they have on shared or deployed infrastructure or remote API endpoints
- Teams that want to reduce the complexity and costs associated with using cloud services directly during development
- Developers that want to make it easier to visualize what's going on in their databases, queues, etc.
- Teams that want to reduce the complexity of setting up their development environment without impacting the development of the app itself

## Tools integration

Works well with Docker Compose and Testcontainers.

## Modules

### What is container-supported development?

Container-supported development is the idea of using containers to enhance your development environment by running local instances or emulators of the services your application relies on. Once you're using containers, it's easy to add additional services to visualize or troubleshoot what's going on in your services.

### Demo: running databases locally

With container-supported development, it's easy to run databases locally. In this demo, you'll see how to do so, as well as how to connect a non-containerized application to the database.

> [!TIP]
>
> Learn more about running databases in containers in the [Use containerized databases](/guides/databases/) guide.

### Demo: mocking API endpoints

Many APIs require data from other data endpoints. In development, this adds complexities such as the sharing of credentials, uptime/availability, and rate limiting. Instead of relying on those services directly, your application can interact with a mock API server.

This demo will demonstrate how using WireMock can make it easy to develop and test an application, including the APIs various error states.

> [!TIP]
>
> Learn more about using WireMock to mock API in the [Mocking API services with WireMock](/guides/wiremock/) guide.

### Demo: developing the cloud locally

When developing apps, it's often easier to outsource aspects of the application to cloud services, such as Amazon S3. However, connecting to those services in local development introduces IAM policies, networking constraints, and provisioning complications. While these requirements are important in a production setting, they complicate development environments significantly.

With container-supported development, you can run local instances of these services during development and testing, removing the need for complex setups. In this demo, you'll see how LocalStack makes it easy to develop and test applications entirely from the developer's workstation.

> [!TIP]
>
> Learn more about using LocalStack in the [Develop and test AWS Cloud applications using LocalStack](/guides/localstack/) guide.

### Demo: adding additional debug and troubleshooting tools

Once you start using containers in your development environment, it becomes much easier to add additional containers to visualize the contents of the databases or message queues, seed document stores, or event publishers. In this demo, you'll see a few of these examples, as well as how you can connect multiple containers together to make testing even easier.

<div id="lp-survey-anchor"></div>

---

## C++ language-specific guide

# C++ language-specific guide

The C++ getting started guide teaches you how to create a containerized C++ application using Docker. In this guide, you'll learn how to:

> **Acknowledgment**
>
> Docker would like to thank [Pradumna Saraf](https://twitter.com/pradumna_saraf) and [Mohammad-Ali A'r芒bi](https://twitter.com/MohammadAliEN) for their contribution to this guide.

- Containerize and run a C++ application using a multi-stage Docker build
- Build and run a C++ application using Docker Compose
- Set up a local environment to develop a C++ application using containers

After completing the C++ getting started modules, you should be able to containerize your own C++ application based on the examples and instructions provided in this guide.

Start by containerizing an existing C++ application.

## Create a multi-stage build for your C++ application

### Prerequisites

- You have a [Git client](https://git-scm.com/downloads). The examples in this section use a command-line based Git client, but you can use any client.

### Overview

This section walks you through creating a multi-stage Docker build for a C++ application.
A multi-stage build is a Docker feature that allows you to use different base images for different stages of the build process,
so you can optimize the size of your final image and separate build dependencies from runtime dependencies.

The standard practice for compiled languages like C++ is to have a build stage that compiles the code and a runtime stage that runs the compiled binary,
because the build dependencies are not needed at runtime.

### Get the sample application

Let's use a simple C++ application that prints `Hello, World!` to the terminal. To do so, clone the sample repository to use with this guide:

```bash
$ git clone https://github.com/dockersamples/c-plus-plus-docker.git
```

The example for this section is under the `hello` directory in the repository. Get inside it and take a look at the files:

```bash
$ cd c-plus-plus-docker/hello
$ ls
```

You should see the following files:

```text
Dockerfile  hello.cpp
```

### Check the Dockerfile

Open the `Dockerfile` in an IDE or text editor. The `Dockerfile` contains the instructions for building the Docker image.

```Dockerfile
# Stage 1: Build stage
FROM ubuntu:latest AS build

# Install build-essential for compiling C++ code
RUN apt-get update && apt-get install -y build-essential

# Set the working directory
WORKDIR /app

# Copy the source code into the container
COPY hello.cpp .

# Compile the C++ code statically to ensure it doesn't depend on runtime libraries
RUN g++ -o hello hello.cpp -static

# Stage 2: Runtime stage
FROM scratch

# Copy the static binary from the build stage
COPY --from=build /app/hello /hello

# Command to run the binary
CMD ["/hello"]
```

The `Dockerfile` has two stages:

1. **Build stage**: This stage uses the `ubuntu:latest` image to compile the C++ code and create a static binary.
2. **Runtime stage**: This stage uses the `scratch` image, which is an empty image, to copy the static binary from the build stage and run it.

### Build the Docker image

To build the Docker image, run the following command in the `hello` directory:

```bash
$ docker build -t hello .
```

The `-t` flag tags the image with the name `hello`.

### Run the Docker container

To run the Docker container, use the following command:

```bash
$ docker run hello
```

You should see the output `Hello, World!` in the terminal.

Because the final image uses an empty `scratch` base, it contains only the
static binary and none of the build dependencies or usual OS tools. For
example, you can't run a simple `ls` command in the container:

```bash
$ docker run hello ls
```

The absence of a shell and other tools keeps the image small and reduces its
attack surface.

## Containerize a C++ application

### Prerequisites

- You have a [Git client](https://git-scm.com/downloads). The examples in this section use a command-line based Git client, but you can use any client.

### Overview

This section walks you through containerizing and running a C++ application, using Docker Compose.

### Get the sample application

We're using the same sample repository that you used in the previous sections of this guide. If you haven't already cloned the repository, clone it now:

```console
$ git clone https://github.com/dockersamples/c-plus-plus-docker.git
```

You should now have the following contents in your `c-plus-plus-docker` (root)
directory.

```text
鈹溾攢鈹€ c-plus-plus-docker/
鈹� 鈹溾攢鈹€ compose.yml
鈹� 鈹溾攢鈹€ Dockerfile
鈹� 鈹溾攢鈹€ LICENSE
鈹� 鈹溾攢鈹€ ok_api.cpp
鈹� 鈹斺攢鈹€ README.md

```

To learn more about the files in the repository, see the following:

- [Dockerfile](/reference/dockerfile/)
- [.dockerignore](/reference/dockerfile/#dockerignore-file)
- [compose.yml](/reference/compose-file/)

### Run the application

Inside the `c-plus-plus-docker` directory, run the following command in a
terminal.

```console
$ docker compose up --build
```

Open a browser and view the application at [http://localhost:8080](http://localhost:8080). You will see a message `{"Status" : "OK"}` in the browser.

In the terminal, press `ctrl`+`c` to stop the application.

#### Run the application in the background

You can run the application detached from the terminal by adding the `-d`
option. Inside the `c-plus-plus-docker` directory, run the following command
in a terminal.

```console
$ docker compose up --build -d
```

Open a browser and view the application at [http://localhost:8080](http://localhost:8080).

In the terminal, run the following command to stop the application.

```console
$ docker compose down
```

For more information about Compose commands, see the [Compose CLI
reference](/reference/cli/docker/compose/).

## Use containers for C++ development

### Prerequisites

Complete [Containerize a C++ application](/guides).

### Overview

In this section, you'll learn how to set up a development environment for your containerized application. This includes:

- Configuring Compose to automatically update your running Compose services as you edit and save your code

### Get the sample application

Clone the sample application to use with this guide. Open a terminal, change directory to a directory that you want to work in, and run the following command to clone the repository:

```console
$ git clone https://github.com/dockersamples/c-plus-plus-docker.git && cd c-plus-plus-docker
```

### Automatically update services

Use Compose Watch to automatically update your running Compose services as you
edit and save your code. For more details about Compose Watch, see [Use Compose
Watch](/compose/how-tos/file-watch/).

Open your `compose.yml` file in an IDE or text editor and then add the Compose Watch instructions. The following example shows how to add Compose Watch to your `compose.yml` file.

```yaml {hl_lines="11-14",linenos=true}
services:
  ok-api:
    image: ok-api
    build:
      context: .
      dockerfile: Dockerfile
    ports:
      - "8080:8080"
    develop:
      watch:
        - action: rebuild
          path: .
```

Run the following command to run your application with Compose Watch.

```console
$ docker compose watch
```

Now, if you modify your `ok_api.cpp` you will see the changes in real time without re-building the image.

To test it out, open the `ok_api.cpp` file in your favorite text editor and change the message from `{"Status" : "OK"}` to `{"Status" : "Updated"}`. Save the file and refresh your browser at [http://localhost:8080](http://localhost:8080). You should see the updated message.

Press `ctrl+c` in the terminal to stop your application.

### Summary

In this section, you also learned how to use Compose Watch to automatically rebuild and run your container when you update your code.

Related information:

- [Compose file reference](/reference/compose-file/)
- [Compose file watch](/compose/how-tos/file-watch/)
- [Multi-stage builds](/build/building/multi-stage/)

---

## Use containerized databases

# Use containerized databases

Using a local containerized database offers flexibility and ease of setup,
letting you mirror production environments closely without the overhead of
traditional database installations. Docker simplifies this process, enabling you
to deploy, manage, and scale databases in isolated containers with just a few
commands.

In this guide, you'll learn how to:

- Run a local containerized database
- Access the shell of a containerized database
- Connect to a containerized database from your host
- Connect to a containerized database from another container
- Persist database data in a volume
- Build a customized database image
- Use Docker Compose to run a database

This guide uses the MySQL image for examples, but the concepts can be applied to other database images.

## Prerequisites

To follow along with this guide, you must have Docker installed. To install Docker, see [Get Docker](/get-started/get-docker/).

## Run a local containerized database

Most popular database systems, including MySQL, PostgreSQL, and MongoDB, have a
Docker Official Image available on Docker Hub. These images are a curated set
images that follow best practices, ensuring that you have access to the latest
features and security updates. To get started, visit
[Docker Hub](https://hub.docker.com) and search for the database you're
interested in. Each image's page provides detailed instructions on how to run
the container, customize your setup, and configure the database according to
your needs. For more information about the MySQL image used in this guide, see the Docker Hub [MySQL image](https://hub.docker.com/_/mysql) page.

To run a database container, you can use either the Docker Desktop GUI or
CLI.

**CLI**

To run a container using the CLI, run the following command in a terminal:

```console
$ docker run --name my-mysql -e MYSQL_ROOT_PASSWORD=my-secret-pw -e MYSQL_DATABASE=mydb -d mysql:latest
```

In this command:

- `--name my-mysql` assigns the name my-mysql to your container for easier
  reference.
- `-e MYSQL_ROOT_PASSWORD=my-secret-pw` sets the root password for MySQL to
  my-secret-pw. Replace my-secret-pw with a secure password of your choice.
- `-e MYSQL_DATABASE=mydb` optionally creates a database named mydb. You can
  change mydb to your desired database name.
- `-d` runs the container in detached mode, meaning it runs in the background.
- `mysql:latest` specifies that you want to use the latest version of the MySQL
  image.

To verify that your container is running, run `docker ps` in a terminal

**GUI**

To run a container using the GUI:

1. In the Docker Desktop Dashboard, select the global search at the top of the window.
2. Specify `mysql` in the search box, and select the `Images` tab if not already
   selected.
3. Hover over the `mysql` image and select `Run`.
   The **Run a new container** modal appears.
4. Expand **Optional settings**.
5. In the optional settings, specify the following:

   - **Container name**: `my-mysql`
   - **Environment variables**:
     - `MYSQL_ROOT_PASSWORD`:`my-secret-pw`
     - `MYSQL_DATABASE`:`mydb`

   ![The optional settings screen with the options specified.](/guides/databases/images/databases-1.webp)

6. Select `Run`.
7. Open the **Container** view in the Docker Desktop Dashboard to verify that your
   container is running.

## Access the shell of a containerized database

When you have a database running inside a Docker container, you may need to
access its shell to manage the database, execute commands, or perform
administrative tasks. Docker provides a straightforward way to do this using the
`docker exec` command. Additionally, if you prefer a graphical interface, you
can use Docker Desktop's GUI.

If you don't yet have a database container running, see
[Run a local containerized database](#run-a-local-containerized-database).

**CLI**

To access the terminal of a MySQL container using the CLI, you can use the
following `docker exec` command.

```console
$ docker exec -it my-mysql bash
```

In this command:

- `docker exec` tells Docker you want to execute a command in a running
  container.
- `-it` ensures that the terminal you're accessing is interactive, so you can
  type commands into it.
- `my-mysql` is the name of your MySQL container. If you named your container
  differently when you ran it, use that name instead.
- `bash` is the command you want to run inside the container. It opens up a bash
  shell that lets you interact with the container's file system and installed
  applications.

After executing this command, you will be given access to the bash shell inside
your MySQL container, from which you can manage your MySQL server directly. You
can run `exit` to return to your terminal.

**GUI**

1. Open the Docker Desktop Dashboard and select the **Containers** view.
2. In the **Actions** column for your container, select **Show container
   actions** and then select **Open in terminal**.

In this terminal you can access to the shell inside your MySQL container, from
which you can manage your MySQL server directly.

Once you've accessed the container's terminal, you can run any tools available
in that container. The following example shows using `mysql` in the container to
list the databases.

```console
# mysql -u root -p
Enter password: my-secret-pw

mysql> SHOW DATABASES;

+--------------------+
| Database           |
+--------------------+
| information_schema |
| mydb               |
| mysql              |
| performance_schema |
| sys                |
+--------------------+
5 rows in set (0.00 sec)
```

## Connect to a containerized database from your host

Connecting to a containerized database from your host machine involves mapping a
port inside the container to a port on your host machine. This process ensures
that the database inside the container is accessible via the host machine's
network. For MySQL, the default port is 3306. By exposing this port, you can use
various database management tools or applications on your host machine to
interact with your MySQL database.

Before you begin, you must remove any containers you previously ran for this
guide. To stop and remove a container, either:

- In a terminal, run `docker rm --force my-mysql` to remove the container
  named `my-mysql`.
- Or, in the Docker Desktop Dashboard, select the **Delete** icon next to your
  container in the **Containers** view.

Next, you can use either the Docker Desktop GUI or CLI to run the container with
the port mapped.

**CLI**

Run the following command in a terminal.

```console
$ docker run -p 3307:3306 --name my-mysql -e MYSQL_ROOT_PASSWORD=my-secret-pw -e MYSQL_DATABASE=mydb -d mysql:latest
```

In this command, `-p 3307:3306` maps port 3307 on the host to port 3306 in the container.

To verify the port is mapped, run the following command.

```console
$ docker ps
```

You should see output like the following.

```console
CONTAINER ID   IMAGE          COMMAND                  CREATED          STATUS          PORTS                               NAMES
6eb776cfd73c   mysql:latest   "docker-entrypoint.s鈥�"   17 minutes ago   Up 17 minutes   33060/tcp, 0.0.0.0:3307->3306/tcp   my-mysql
```

**GUI**

To run a container using the GUI:

1. In the Docker Desktop Dashboard, select the global search at the top of the window.
2. Specify `mysql` in the search box, and select the `Images` tab if not already
   selected.
3. Hover over the `mysql` image and select `Run`.
   The **Run a new container** modal appears.
4. Expand **Optional settings**.
5. In the optional settings, specify the following:

   - **Container name**: `my-mysql`
   - **Host port** for the **3306/tcp** port: `3307`
   - **Environment variables**:
     - `MYSQL_ROOT_PASSWORD`:`my-secret-pw`
     - `MYSQL_DATABASE`:`mydb`

   ![The optional settings screen with the options specified.](/guides/databases/images/databases-2.webp)

6. Select `Run`.
7. In the **Containers** view, verify that the port is mapped under the
   **Port(s)** column. You should see `3307:3306` for the `my-mysql`
   container.

At this point, any application running on your host can access the MySQL service in the container at `localhost:3307`.

## Connect to a containerized database from another container

Connecting to a containerized database from another container is a common
scenario in microservices architecture and during development processes.
Docker's networking capabilities make it easy to establish this connection
without having to expose the database to the host network. This is achieved by
placing both the database container and the container that needs to access it on
the same Docker network.

Before you begin, you must remove any containers you previously ran for this
guide. To stop and remove a container, either:

- In a terminal, run `docker rm --force my-mysql` to remove the container
  named `my-mysql`.
- Or, in the Docker Desktop Dashboard, select the **Delete** icon next to your
  container in the **Containers** view.

To create a network and run containers on it:

1. Run the following command to create a Docker network named my-network.

   ```console
   $ docker network create my-network
   ```

2. Run your database container and specify the network using the `--network`
   option. This runs the container on the my-network network.

   ```console
   $ docker run --name my-mysql -e MYSQL_ROOT_PASSWORD=my-secret-pw -e MYSQL_DATABASE=mydb --network my-network -d mysql:latest
   ```

3. Run your other containers and specify the network using the `--network`
   option. For this example, you'll run a phpMyAdmin container that can connect
   to your database.

   1. Run a phpMyAdmin container. Use the `--network` option to specify the
      network, the `-p` option to let you access the container from your host
      machine, and the `-e` option to specify a required environment variable
      for this image.

      ```console
      $ docker run --name my-phpmyadmin -d --network my-network -p 8080:80 -e PMA_HOST=my-mysql phpmyadmin
      ```

4. Verify that the containers can communicate. For this example, you'll access
   phpMyAdmin and verify that it connects to the database.

   1. Open [http://localhost:8080](http://localhost:8080) to access your phpMyAdmin container.
   2. Log in using `root` as the username and `my-secret-pw` as the password.
      You should connect to the MySQL server and see your database listed.

At this point, any application running on your `my-network` container network
can access the MySQL service in the container at `my-mysql:3306`.

## Persist database data in a volume

Persisting database data in a Docker volume is necessary for ensuring that your
data survives container restarts and removals. A Docker volume lets you store
database files outside the container's writable layer, making it possible to
upgrade the container, switch bases, and share data without losing it. Here鈥檚
how you can attach a volume to your database container using either the Docker
CLI or the Docker Desktop GUI.

Before you begin, you must remove any containers you previously ran for this
guide. To stop and remove a container, either:

- In a terminal, run `docker rm --force my-mysql` to remove the container
  named `my-mysql`.
- Or, in the Docker Desktop Dashboard, select the **Delete** icon next to your
  container in the **Containers** view.

Next, you can use either the Docker Desktop GUI or CLI to run the container with a volume.

**CLI**

To run your database container with a volume attached, include the `-v` option
with your `docker run` command, specifying a volume name and the path where the
database stores its data inside the container. If the volume doesn't exist,
Docker automatically creates it for you.

To run a database container with a volume attached, and then verify that the
data persists:

1. Run the container and attach the volume.

   ```console
   $ docker run --name my-mysql -e MYSQL_ROOT_PASSWORD=my-secret-pw -e MYSQL_DATABASE=mydb -v my-db-volume:/var/lib/mysql -d mysql:latest
   ```

   This command mounts the volume named `my-db-volume` to the `/var/lib/mysql` directory in the container.

2. Create some data in the database. Use the `docker exec` command to run
   `mysql` inside the container and create a table.

   ```console
   $ docker exec my-mysql mysql -u root -pmy-secret-pw -e "CREATE TABLE IF NOT EXISTS mydb.mytable (column_name VARCHAR(255)); INSERT INTO mydb.mytable (column_name) VALUES ('value');"
   ```

   This command uses the `mysql` tool in the container to create a table named
   `mytable` with a column named `column_name`, and finally inserts a value of
   `value`.

3. Stop and remove the container. Without a volume, the table you created would
   be lost when removing the container.

   ```console
   $ docker rm --force my-mysql
   ```

4. Start a new container with the volume attached. This time, you don't need to
   specify any environment variables as the configuration is saved in the
   volume.

   ```console
   $ docker run --name my-mysql -v my-db-volume:/var/lib/mysql -d mysql:latest
   ```

5. Verify that the table you created still exists. Use the `docker exec` command
   again to run `mysql` inside the container.

   ```console
   $ docker exec my-mysql mysql -u root -pmy-secret-pw -e "SELECT * FROM mydb.mytable;"
   ```

   This command uses the `mysql` tool in the container to select all the
   records from the `mytable` table.

   You should see output like the following.

   ```console
   column_name
   value
   ```

**GUI**

To run a database container with a volume attached, and then verify that the
data persists:

1. Run a container with a volume attached.

   1. In the Docker Desktop Dashboard, select the global search at the top of the window.
   2. Specify `mysql` in the search box, and select the **Images** tab if not
      already selected.
   3. Hover over the **mysql** image and select **Run**.
      The **Run a new container** modal appears.
   4. Expand **Optional settings**.
   5. In the optional settings, specify the following:

      - **Container name**: `my-mysql`
      - **Environment variables**:
        - `MYSQL_ROOT_PASSWORD`:`my-secret-pw`
        - `MYSQL_DATABASE`:`mydb`
      - **Volumes**:
        - `my-db-volume`:`/var/lib/mysql`

      ![The optional settings screen with the options specified.](/guides/databases/images/databases-3.webp)

      Here, the name of the volume is `my-db-volume` and it is mounted in the
      container at `/var/lib/mysql`.

   6. Select `Run`.

2. Create some data in the database.

   1. In the **Containers** view, next to your container select the **Show
      container actions** icon, and then select **Open in terminal**.
   2. Run the following command in the container's terminal to add a table.

      ```console
      # mysql -u root -pmy-secret-pw -e "CREATE TABLE IF NOT EXISTS mydb.mytable (column_name VARCHAR(255)); INSERT INTO mydb.mytable (column_name) VALUES ('value');"
      ```

      This command uses the `mysql` tool in the container to create a table
      named `mytable` with a column named `column_name`, and finally inserts a
      value of value`.

3. In the **Containers** view, select the **Delete** icon next to your
   container, and then select **Delete forever**. Without a volume, the table
   you created would be lost when deleting the container.
4. Run a container with a volume attached.

   1. In the Docker Desktop Dashboard, select the global search at the top of the window.
   2. Specify `mysql` in the search box, and select the **Images** tab if not
      already selected.
   3. Hover over the **mysql** image and select **Run**.
      The **Run a new container** modal appears.
   4. Expand **Optional settings**.
   5. In the optional settings, specify the following:

      - **Container name**: `my-mysql`
      - **Environment variables**:
        - `MYSQL_ROOT_PASSWORD`:`my-secret-pw`
        - `MYSQL_DATABASE`:`mydb`
      - **Volumes**:
        - `my-db-volume`:`/var/lib/mysql`

      ![The optional settings screen with the options specified.](/guides/databases/images/databases-3.webp)

   6. Select `Run`.

5. Verify that the table you created still exists.

   1. In the **Containers** view, next to your container select the **Show
      container actions** icon, and then select **Open in terminal**.
   2. Run the following command in the container's terminal to verify that table
      you created still exists.

      ```console
      # mysql -u root -pmy-secret-pw -e "SELECT * FROM mydb.mytable;"
      ```

      This command uses the `mysql` tool in the container to select all the
      records from the `mytable` table.

      You should see output like the following.

      ```console
      column_name
      value
      ```

At this point, any MySQL container that mounts the `my-db-volume` will be able
to access and save persisted data.

## Build a customized database image

Customizing your database image lets you include additional configuration,
scripts, or tools alongside the base database server. This is particularly
useful for creating a Docker image that matches your specific development or
production environment needs. The following example outlines how to build and
run a custom MySQL image that includes a table initialization script.

Before you begin, you must remove any containers you previously ran for this
guide. To stop and remove a container, either:

- In a terminal, run `docker rm --force my-mysql` to remove the container
  named `my-mysql`.
- Or, in the Docker Desktop Dashboard, select the **Delete** icon next to your
  container in the **Containers** view.

To build and run your custom image:

1. Create a Dockerfile.

   1. Create a file named `Dockerfile` in your project directory. For this
      example, you can create the `Dockerfile` in an empty directory of your
      choice. This file will define how to build your custom MySQL image.
   2. Add the following content to the `Dockerfile`.

      ```dockerfile
      # syntax=docker/dockerfile:1

      # Use the base image mysql:latest
      FROM mysql:latest

      # Set environment variables
      ENV MYSQL_DATABASE mydb

      # Copy custom scripts or configuration files from your host to the container
      COPY ./scripts/ /docker-entrypoint-initdb.d/
      ```

      In this Dockerfile, you've set the environment variable for the MySQL
      database name. You can also use the `COPY` instruction to add custom
      configuration files or scripts into the container. In this
      example, files from your host's `./scripts/` directory are copied into the
      container's `/docker-entrypoint-initdb.d/` directory. In this directory,
      `.sh`, `.sql`, and `.sql.gz` scripts are executed when the container is
      started for the first time. For more details about Dockerfiles, see the
      [Dockerfile reference](/reference/dockerfile/).

   3. Create a script file to initialize a table in the database. In the
      directory where your `Dockerfile` is located, create a subdirectory named
      `scripts`, and then create a file named `create_table.sql` with the
      following content.

   ```text
   CREATE TABLE IF NOT EXISTS mydb.myothertable (
     column_name VARCHAR(255)
   );

   INSERT INTO mydb.myothertable (column_name) VALUES ('other_value');
   ```

   You should now have the following directory structure.

   ```text
   鈹溾攢鈹€ your-project-directory/
   鈹� 鈹溾攢鈹€ scripts/
   鈹� 鈹� 鈹斺攢鈹€ create_table.sql
   鈹� 鈹斺攢鈹€ Dockerfile
   ```

2. Build your image.

   1. In a terminal, change directory to the directory where your `Dockerfile`
      is located.
   2. Run the following command to build the image.

      ```console
      $ docker build -t my-custom-mysql .
      ```

      In this command, `-t my-custom-mysql` tags (names) your new image as
      `my-custom-mysql`. The period (.) at the end of the command specifies the
      current directory as the context for the build, where Docker looks for the
      Dockerfile and any other files needed for the build.

3. Run your image as you did in [Run a local containerized
   database](#run-a-local-containerized-database). This time, specify your
   image's name instead of `mysql:latest`. Also, you no longer need to specify
   the `MYSQL_DATABASE` environment variable as it's now defined by your
   Dockerfile.

   ```console
   $ docker run --name my-mysql -e MYSQL_ROOT_PASSWORD=my-secret-pw -d my-custom-mysql
   ```

4. Verify that your container is running with the following command.

   ```console
   $ docker ps
   ```

   You should see output like the following.

   ```console
   CONTAINER ID   IMAGE              COMMAND                  CREATED        STATUS          PORTS                 NAMES
   f74dcfdb0e59   my-custom-mysql   "docker-entrypoint.s鈥�"    2 hours ago    Up 51 minutes   3306/tcp, 33060/tcp   my-mysql
   ```

5. Verify that your initialization script was ran. Run the following command in
   a terminal to show the contents of the `myothertable` table.

   ```console
   $ docker exec my-mysql mysql -u root -pmy-secret-pw -e "SELECT * FROM mydb.myothertable;"
   ```

   You should see output like the following.

   ```console
   column_name
   other_value
   ```

Any container ran using your `my-custom-mysql` image will have the table
initialized when first started.

## Use Docker Compose to run a database

Docker Compose is a tool for defining and running multi-container Docker
applications. With a single command, you can configure all your application's
services (like databases, web apps, etc.) and manage them. In this example,
you'll create a Compose file and use it to run a MySQL database container and a phpMyAdmin container.

To run your containers with Docker Compose:

1. Create a Docker Compose file.

   1. Create a file named `compose.yaml` in your project directory. This file
      will define the services, networks, and volumes.
   2. Add the following content to the `compose.yaml` file.

      ```yaml
      services:
        db:
          image: mysql:latest
          environment:
            MYSQL_ROOT_PASSWORD: my-secret-pw
            MYSQL_DATABASE: mydb
          ports:
            - 3307:3306
          volumes:
            - my-db-volume:/var/lib/mysql

        phpmyadmin:
          image: phpmyadmin/phpmyadmin:latest
          environment:
            PMA_HOST: db
            PMA_PORT: 3306
            MYSQL_ROOT_PASSWORD: my-secret-pw
          ports:
            - 8080:80
          depends_on:
            - db

      volumes:
        my-db-volume:
      ```

      For the database service:

      - `db` is the name of the service.
      - `image: mysql:latest` specifies that the service uses the latest MySQL
        image from Docker Hub.
      - `environment` lists the environment variables used by MySQL to
        initialize the database, such as the root password and the database
        name.
      - `ports` maps port 3307 on the host to port 3306 in the container,
        allowing you to connect to the database from your host machine.
      - `volumes` mounts `my-db-volume` to `/var/lib/mysql` inside the container
        to persist database data.

      In addition to the database service, there is a phpMyAdmin service. By
      default Compose sets up a single network for your app. Each container for
      a service joins the default network and is both reachable by other
      containers on that network, and discoverable by the service's name.
      Therefore, in the `PMA_HOST` environment variable, you can specify the
      service name, `db`, in order to connect to the database service. For more details about Compose, see the [Compose file reference](/reference/compose-file/).

2. Run Docker Compose.

   1. Open a terminal and change directory to the directory where your
      `compose.yaml` file is located.
   2. Run Docker Compose using the following command.

      ```console
      $ docker compose up
      ```

      You can now access phpMyAdmin at
      [http://localhost:8080](http://localhost:8080) and connect to your
      database using `root` as the username and `my-secret-pw` as the password.

   3. To stop the containers, press `ctrl`+`c` in the terminal.

Now, with Docker Compose you can start your database and app, mount volumes,
configure networking, and more, all with a single command.

## Summary

This guide introduced you to the essentials of using containerized databases,
specifically focusing on MySQL, to enhance flexibility, ease of setup, and
consistency across your development environments. The use-cases covered in
this guide not only streamline your development workflows but also prepare you
for more advanced database management and deployment scenarios, ensuring your
data-driven applications remain robust and scalable.

Related information:

- [Docker Hub database images](https://hub.docker.com/search?q=database&type=image)
- [Dockerfile reference](/reference/dockerfile/)
- [Compose file reference](/reference/compose-file/)
- [CLI reference](/reference/cli/docker/)
- [Database samples](/../reference/samples/#databases)

---

## Deno language-specific guide

# Deno language-specific guide

The Deno getting started guide teaches you how to create a containerized Deno application using Docker.

> **Acknowledgment**
>
> Docker would like to thank [Pradumna Saraf](https://twitter.com/pradumna_saraf) for his contribution to this guide.

## What will you learn?

- Containerize and run a Deno application using Docker
- Set up a local environment to develop a Deno application using containers
- Use Docker Compose to run the application.

## Prerequisites

- Basic understanding of JavaScript is assumed.
- You must have familiarity with Docker concepts like containers, images, and Dockerfiles. If you are new to Docker, you can start with the [Docker basics](/get-started/docker-concepts/the-basics/what-is-a-container/) guide.

After completing the Deno getting started modules, you should be able to containerize your own Deno application based on the examples and instructions provided in this guide.

Start by containerizing an existing Deno application.

## Containerize a Deno application

### Prerequisites

- You have a [Git client](https://git-scm.com/downloads). The examples in this section use a command-line based Git client, but you can use any client.

### Overview

For a long time, Node.js has been the go-to runtime for server-side JavaScript applications. However, recent years have introduced new alternative runtimes, including [Deno](https://deno.land/). Like Node.js, Deno is a JavaScript and TypeScript runtime, but it takes a fresh approach with modern security features, a built-in standard library, and native support for TypeScript.

Why develop Deno applications with Docker? Having a choice of runtimes is exciting, but managing multiple runtimes and their dependencies consistently across environments can be tricky. This is where Docker proves invaluable. Using containers to create and destroy environments on demand simplifies runtime management and ensures consistency. Additionally, as Deno continues to grow and evolve, Docker helps establish a reliable and reproducible development environment, minimizing setup challenges and streamlining the workflow.

### Get the sample application

Clone the sample application to use with this guide. Open a terminal, change
directory to a directory that you want to work in, and run the following
command to clone the repository:

```console
$ git clone https://github.com/dockersamples/docker-deno.git && cd docker-deno
```

You should now have the following contents in your `deno-docker` directory.

```text
鈹溾攢鈹€ deno-docker/
鈹� 鈹溾攢鈹€ compose.yml
鈹� 鈹溾攢鈹€ Dockerfile
鈹� 鈹溾攢鈹€ LICENSE
鈹� 鈹溾攢鈹€ server.ts
鈹� 鈹斺攢鈹€ README.md
```

### Understand the sample application

The sample application is a simple Deno application that uses the Oak framework to create a simple API that returns a JSON response. The application listens on port 8000 and returns a message `{"Status" : "OK"}` when you access the application in a browser.

```typescript
// server.ts
import { Application, Router } from "https://deno.land/x/oak@v12.0.0/mod.ts";

const app = new Application();
const router = new Router();

// Define a route that returns JSON
router.get("/", (context) => {
  context.response.body = { Status: "OK" };
  context.response.type = "application/json";
});

app.use(router.routes());
app.use(router.allowedMethods());

console.log("Server running on http://localhost:8000");
await app.listen({ port: 8000 });
```

### Create a Dockerfile

Before creating a Dockerfile, you need to choose a base image. You can either use the [Deno Docker Official Image](https://hub.docker.com/r/denoland/deno) or a Docker Hardened Image (DHI) from the [Hardened Image catalog](https://hub.docker.com/hardened-images/catalog).

Choosing DHI offers the advantage of a production-ready image that is lightweight and secure. For more information, see [Docker Hardened Images](https://docs.docker.com/dhi/).

**Using Docker Hardened Images**

Docker Hardened Images (DHIs) are available for Deno in the [Docker Hardened Images catalog](https://hub.docker.com/hardened-images/catalog/dhi/deno). You can pull DHIs directly from the `dhi.io` registry.

1. Sign in to the DHI registry:

   ```console
   $ docker login dhi.io
   ```

2. Pull the Deno DHI as `dhi.io/deno:2`. The tag (`2`) in this example refers to the version to the latest 2.x version of Deno.

   ```console
   $ docker pull dhi.io/deno:2
   ```

For other available versions, refer to the [catalog](https://hub.docker.com/hardened-images/catalog/dhi/deno).

```dockerfile
# Use the DHI Deno image as the base image
FROM dhi.io/deno:2

# Set the working directory
WORKDIR /app

# Copy server code into the container
COPY server.ts .

# Set permissions (optional but recommended for security)
USER deno

# Expose port 8000
EXPOSE 8000

# Run the Deno server
CMD ["run", "--allow-net", "server.ts"]
```

**Using the official image**

Using the Docker Official Image is straightforward. In the following Dockerfile, you'll notice that the `FROM` instruction uses `denoland/deno:latest` as the base image.

This is the official image for Deno. This image is [available on the Docker Hub](https://hub.docker.com/r/denoland/deno).

```dockerfile
# Use the official Deno image
FROM denoland/deno:latest

# Set the working directory
WORKDIR /app

# Copy server code into the container
COPY server.ts .

# Set permissions (optional but recommended for security)
USER deno

# Expose port 8000
EXPOSE 8000

# Run the Deno server
CMD ["run", "--allow-net", "server.ts"]
```

In addition to specifying the base image, the Dockerfile also:

- Sets the working directory in the container to `/app`.
- Copies `server.ts` into the container.
- Sets the user to `deno` to run the application as a non-root user.
- Exposes port 8000 to allow traffic to the application.
- Runs the Deno server using the `CMD` instruction.
- Uses the `--allow-net` flag to allow network access to the application. The `server.ts` file uses the Oak framework to create a simple API that listens on port 8000.

### Run the application

Make sure you are in the `deno-docker` directory. Run the following command in a terminal to build and run the application.

```console
$ docker compose up --build
```

Open a browser and view the application at [http://localhost:8000](http://localhost:8000). You will see a message `{"Status" : "OK"}` in the browser.

In the terminal, press `ctrl`+`c` to stop the application.

#### Run the application in the background

You can run the application detached from the terminal by adding the `-d`
option. Inside the `deno-docker` directory, run the following command
in a terminal.

```console
$ docker compose up --build -d
```

Open a browser and view the application at [http://localhost:8000](http://localhost:8000).

In the terminal, run the following command to stop the application.

```console
$ docker compose down
```

## Use containers for Deno development

### Prerequisites

Complete [Containerize a Deno application](/guides).

### Overview

In this section, you'll learn how to set up a development environment for your containerized application. This includes:

- Configuring Compose to automatically update your running Compose services as you edit and save your code

### Get the sample application

Clone the sample application to use with this guide. Open a terminal, change directory to a directory that you want to work in, and run the following command to clone the repository:

```console
$ git clone https://github.com/dockersamples/docker-deno.git && cd docker-deno
```

### Automatically update services

Use Compose Watch to automatically update your running Compose services as you
edit and save your code. For more details about Compose Watch, see [Use Compose
Watch](/compose/how-tos/file-watch/).

Open your `compose.yml` file in an IDE or text editor and then add the Compose Watch instructions. The following example shows how to add Compose Watch to your `compose.yml` file.

```yaml {hl_lines="9-12",linenos=true}
services:
  server:
    image: deno-server
    build:
      context: .
      dockerfile: Dockerfile
    ports:
      - "8000:8000"
    develop:
      watch:
        - action: rebuild
          path: .
```

Run the following command to run your application with Compose Watch.

```console
$ docker compose watch
```

Now, if you modify your `server.ts` you will see the changes in real time without re-building the image.

To test it out, open the `server.ts` file in your favorite text editor and change the message from `{"Status" : "OK"}` to `{"Status" : "Updated"}`. Save the file and refresh your browser at `http://localhost:8000`. You should see the updated message.

Press `ctrl+c` in the terminal to stop your application.

### Summary

In this section, you also learned how to use Compose Watch to automatically rebuild and run your container when you update your code.

Related information:

- [Compose file reference](/reference/compose-file/)
- [Compose file watch](/compose/how-tos/file-watch/)
- [Multi-stage builds](/build/building/multi-stage/)

---

## Mocking OAuth services in testing with Dex

# Mocking OAuth services in testing with Dex

Dex is an open-source OpenID Connect (OIDC) and OAuth 2.0 identity provider that can be configured to authenticate against various backend identity providers, such as LDAP, SAML, and OAuth. Running Dex in a Docker container allows developers to simulate an OAuth 2.0 server for testing and development purposes. This guide will walk you through setting up Dex as an OAuth mock server using Docker containers.

Nowadays OAuth is the preferred choice to authenticate in web services, the highest part of them give the possibility to access using popular OAuth services like GitHub, Google or Apple. Using OAuth guarantees a higher level of security and simplification since it is not necessary to create new profiles for each service. This means that, by allowing applications to access resources on behalf of users without sharing passwords, OAuth minimizes the risk of credential exposure.

In this guide, you'll learn how to:

- Use Docker to launch up a Dex container.
- Use mock OAuth in the GitHub Action (GHA) without relying on an external OAuth provider.

## Using Dex with Docker

The official [Docker image for Dex](https://hub.docker.com/r/dexidp/dex/) provides a convenient way to deploy and manage Dex instances. Dex is available for various CPU architectures, including amd64, armv7, and arm64, ensuring compatibility with different devices and platforms. You can learn more about Dex standalone on the [Dex docs site](https://dexidp.io/docs/getting-started/).

### Prerequisites

[Docker Compose](/compose/): Recommended for managing multi-container Docker applications.

### Setting up Dex with Docker

Begin by creating a directory for your Dex project:

```bash
mkdir dex-mock-server
cd dex-mock-server
```
Organize your project with the following structure:

```bash
dex-mock-server/
鈹溾攢鈹€ config.yaml
鈹斺攢鈹€ compose.yaml
```

Create the Dex Configuration File:
The config.yaml file defines Dex's settings, including connectors, clients, and storage. For a mock server setup, you can use the following minimal configuration:

```yaml
# config.yaml
issuer: http://localhost:5556/dex
storage:
  type: memory
web:
  http: 0.0.0.0:5556
staticClients:
  - id: example-app
    redirectURIs:
      - 'http://localhost:5555/callback'
    name: 'Example App'
    secret: ZXhhbXBsZS1hcHAtc2VjcmV0
enablePasswordDB: true
staticPasswords:
  - email: "admin@example.com"
    hash: "$2a$10$2b2cU8CPhOTaGrs1HRQuAueS7JTT5ZHsHSzYiFPm1leZck7Mc8T4W"
    username: "admin"
    userID: "1234"
```

Explanation:
- issuer: The public URL of Dex.

- storage: Using in-memory storage for simplicity.

- web: Dex will listen on port 5556.

- staticClients: Defines a client application (example-app) with its redirect URI and secret.

- enablePasswordDB: Enables static password authentication.

- staticPasswords: Defines a static user for authentication. The hash is a bcrypt hash of the password.

> [!NOTE]
>
> Ensure the hash is a valid bcrypt hash of your desired password. You can generate this using tools like [bcrypt-generator.com](https://bcrypt-generator.com/).
or use CLI tools like [htpasswd](https://httpd.apache.org/docs/2.4/programs/htpasswd.html) like in this following example:`echo password | htpasswd -BinC 10 admin | cut -d: -f2`

With Docker Compose configured, start Dex:
```yaml
# compose.yaml

services:
  dex:
    image: dexidp/dex:latest
    container_name: dex
    ports:
      - "5556:5556"
    volumes:
      - ./config.yaml:/etc/dex/config.yaml
    command: ["dex", "serve", "/etc/dex/config.yaml"]
```

Now it is possible to run the container using the `docker compose` command.
```bash
docker compose up -d
```

This command will download the Dex Docker image (if not already available) and start the container in detached mode.

To verify that Dex is running, check the logs to ensure Dex started successfully:
```bash
docker compose logs -f dex
```
You should see output indicating that Dex is listening on the specified port.

### Using Dex OAuth testing in GHA

To test the OAuth flow, you'll need a client application configured to authenticate against Dex. One of the most typical use cases is to use it inside GitHub Actions. Since Dex supports mock authentication, you can predefine test users as suggested in the [docs](https://dexidp.io/docs). The `config.yaml` file should looks like:

```yaml
issuer: http://127.0.0.1:5556/dex

storage:
  type: memory

web:
  http: 0.0.0.0:5556

oauth2:
  skipApprovalScreen: true

staticClients:
  - name: TestClient
    id: client_test_id
    secret: client_test_secret
    redirectURIs:
      - http://{ip-your-app}/path/to/callback/ # example: http://localhost:5555/callback

connectors:
# mockCallback connector always returns the user 'kilgore@kilgore.trout'.
- type: mockCallback
  id: mock
  name: Mock
```
Now you can insert the Dex service inside your `~/.github/workflows/ci.yaml` file:

```yaml
[...]
jobs:
  test-oauth:
    runs-on: ubuntu-latest
    steps:
      - name: Install Dex
        run: |
          curl -L https://github.com/dexidp/dex/releases/download/v2.37.0/dex_linux_amd64 -o dex
          chmod +x dex

      - name: Start Dex Server
        run: |
          nohup ./dex serve config.yaml > dex.log 2>&1 &
          sleep 5  # Give Dex time to start
[...]
```

### Conclusion

By following this guide, you've set up Dex as an OAuth mock server using Docker. This setup is invaluable for testing and development, allowing you to simulate OAuth flows without relying on external identity providers. For more advanced configurations and integrations, refer to the [Dex documentation](https://dexidp.io/docs/).

---

## Secure a Backstage application with Docker Hardened Images

# Secure a Backstage application with Docker Hardened Images

This guide shows how to secure a Backstage application using Docker Hardened Images (DHI). Backstage is a CNCF open source developer portal used by thousands of organizations to manage their software catalogs, templates, and developer tooling.

By the end of this guide, you'll have a Backstage container image that is distroless, runs as a non-root user by default, and has dramatically fewer CVEs than the standard `node:24-trixie-slim` base image while still supporting the native module compilation that Backstage requires.

## Prerequisites

- Docker Desktop or Docker Engine with BuildKit enabled
- A Docker Hub account authenticated with `docker login` and `docker login dhi.io`
- A Backstage project created with `@backstage/create-app`

## Why Backstage needs customization

The DHI migration examples cover applications where you can swap the base image and everything works. Backstage is different. It uses `better-sqlite3` and other packages that compile native Node.js modules at install time, which means the build stage needs `g++`, `make`, `python3`, and `sqlite-dev` 鈥� none of which are in the base `dhi.io/node` image. The runtime image only needs the shared library (`sqlite-libs`) that the compiled native module links against.

This is a common pattern. Any Node.js application that depends on native addons (such as `bcrypt`, `sharp`, `sqlite3`, or `node-canvas`) faces the same challenge. The approach in this guide applies to all of them.

## Step 1: Examine the original Dockerfile

The official Backstage documentation recommends a multi-stage Dockerfile using `node:24-trixie-slim` (Debian). A typical setup looks like this:

```dockerfile
# Stage 1 - Create yarn install skeleton layer
FROM node:24-trixie-slim AS packages
WORKDIR /app
COPY backstage.json package.json yarn.lock ./
COPY .yarn ./.yarn
COPY .yarnrc.yml ./
COPY packages packages
COPY plugins plugins
RUN find packages \! -name "package.json" -mindepth 2 -maxdepth 2 \
    -exec rm -rf {} \+

# Stage 2 - Install dependencies and build packages
FROM node:24-trixie-slim AS build
ENV PYTHON=/usr/bin/python3
RUN --mount=type=cache,target=/var/cache/apt,sharing=locked \
    --mount=type=cache,target=/var/lib/apt,sharing=locked \
    apt-get update && \
    apt-get install -y --no-install-recommends python3 g++ build-essential && \
    rm -rf /var/lib/apt/lists/*
RUN --mount=type=cache,target=/var/cache/apt,sharing=locked \
    --mount=type=cache,target=/var/lib/apt,sharing=locked \
    apt-get update && \
    apt-get install -y --no-install-recommends libsqlite3-dev && \
    rm -rf /var/lib/apt/lists/*
USER node
WORKDIR /app
COPY --from=packages --chown=node:node /app .
RUN --mount=type=cache,target=/home/node/.cache/yarn,sharing=locked,uid=1000,gid=1000 \
    yarn install --immutable
COPY --chown=node:node . .
RUN yarn tsc
RUN yarn --cwd packages/backend build
RUN mkdir packages/backend/dist/skeleton packages/backend/dist/bundle \
    && tar xzf packages/backend/dist/skeleton.tar.gz \
       -C packages/backend/dist/skeleton \
    && tar xzf packages/backend/dist/bundle.tar.gz \
       -C packages/backend/dist/bundle

# Stage 3 - Build the actual backend image
FROM node:24-trixie-slim
ENV PYTHON=/usr/bin/python3
RUN --mount=type=cache,target=/var/cache/apt,sharing=locked \
    --mount=type=cache,target=/var/lib/apt,sharing=locked \
    apt-get update && \
    apt-get install -y --no-install-recommends python3 g++ build-essential && \
    rm -rf /var/lib/apt/lists/*
RUN --mount=type=cache,target=/var/cache/apt,sharing=locked \
    --mount=type=cache,target=/var/lib/apt,sharing=locked \
    apt-get update && \
    apt-get install -y --no-install-recommends libsqlite3-dev && \
    rm -rf /var/lib/apt/lists/*
USER node
WORKDIR /app
COPY --from=build --chown=node:node /app/.yarn ./.yarn
COPY --from=build --chown=node:node /app/.yarnrc.yml ./
COPY --from=build --chown=node:node /app/backstage.json ./
COPY --from=build --chown=node:node /app/yarn.lock \
     /app/package.json \
     /app/packages/backend/dist/skeleton/ ./
RUN --mount=type=cache,target=/home/node/.cache/yarn,sharing=locked,uid=1000,gid=1000 \
    yarn workspaces focus --all --production
COPY --from=build --chown=node:node /app/packages/backend/dist/bundle/ ./
CMD ["node", "packages/backend", "--config", "app-config.yaml"]
```

Run this image and inspect what's available inside the container:

```console
docker build -t backstage:init .
docker run -d \
    -e APP_CONFIG_backend_database_client='better-sqlite3' \
    -e APP_CONFIG_backend_database_connection=':memory:' \
    -e APP_CONFIG_auth_providers_guest_dangerouslyAllowOutsideDevelopment='true' \
    -p 7007:7007 \
    -u 1000 \
    --cap-drop=ALL \
    --read-only \
    --tmpfs /tmp \
    backstage:init
```

This works, but the runtime container has a shell, a package manager, and yarn. None of these are needed to run Backstage. Run `docker exec` to see what's accessible inside:

```console
docker exec -it <container-id> sh
$ cat /etc/shells
# /etc/shells: valid login shells
/bin/sh
/usr/bin/sh
/bin/bash
/usr/bin/bash
/bin/rbash
/usr/bin/rbash
/usr/bin/dash
$ yarn --version
4.12.0
$ dpkg --version
dpkg version 1.22.11 (arm64).
$ whoami
node
$ id
uid=1000(node) gid=1000(node) groups=1000(node)
```

The `node:24-trixie-slim` image ships with three shells (`dash`, `bash`, and `rbash`), a package manager (`dpkg`), and `yarn`. Each of these tools increases the attack surface. An attacker who gains access to this container could use them for lateral movement across your infrastructure.

## Step 2: Switch the build stages to DHI

Replace all three stages with DHI equivalents. DHI Node.js images are available in both 
Alpine and Debian variants. This guide uses the Alpine variant (`dhi.io/node:24-alpine3.23`) 
because it produces a smaller image. If you need to stay on Debian for compatibility reasons, 
use `dhi.io/node:24-bookworm` and keep `apt-get` instead of `apk`.

```dockerfile
# Stage 1: prepare packages
FROM --platform=$BUILDPLATFORM dhi.io/node:24-alpine3.23-dev AS packages
WORKDIR /app
COPY backstage.json package.json yarn.lock ./
COPY .yarn ./.yarn
COPY .yarnrc.yml ./
COPY packages packages
COPY plugins plugins
RUN find packages \! -name "package.json" -mindepth 2 -maxdepth 2 \
    -exec rm -rf {} \+

# Stage 2: build the application
FROM --platform=$BUILDPLATFORM dhi.io/node:24-alpine3.23-dev AS build
ENV PYTHON=/usr/bin/python3
RUN apk add --no-cache g++ make python3 sqlite-dev && \
    rm -rf /var/lib/apk/lists/*
WORKDIR /app
COPY --from=packages --chown=node:node /app .
RUN --mount=type=cache,target=/home/node/.cache/yarn,sharing=locked,uid=1000,gid=1000 \
    yarn install --immutable
COPY --chown=node:node . .
RUN yarn tsc
RUN yarn --cwd packages/backend build
RUN mkdir packages/backend/dist/skeleton packages/backend/dist/bundle \
    && tar xzf packages/backend/dist/skeleton.tar.gz \
       -C packages/backend/dist/skeleton \
    && tar xzf packages/backend/dist/bundle.tar.gz \
       -C packages/backend/dist/bundle

# Final Stage: create the runtime image
FROM dhi.io/node:24-alpine3.23-dev
ENV PYTHON=/usr/bin/python3
RUN apk add --no-cache g++ make python3 sqlite-dev && \
    rm -rf /var/lib/apk/lists/*
WORKDIR /app
COPY --from=build --chown=node:node /app/.yarn ./.yarn
COPY --from=build --chown=node:node /app/.yarnrc.yml ./
COPY --from=build --chown=node:node /app/backstage.json ./
COPY --from=build --chown=node:node /app/yarn.lock \
     /app/package.json \
     /app/packages/backend/dist/skeleton/ ./
RUN --mount=type=cache,target=/home/node/.cache/yarn,sharing=locked,uid=1000,gid=1000 \
    yarn workspaces focus --all --production \
    && rm -rf "$(yarn cache clean)"
COPY --from=build --chown=node:node /app/packages/backend/dist/bundle/ ./
CMD ["node", "packages/backend", "--config", "app-config.yaml"]
```

Build and tag this version:

```console
docker build -t backstage:dhi-dev .
```

> [!NOTE]
>
> The `-dev` variant includes a shell and package manager, which is why `apk add` works. Backstage requires `python3` and native build tools in the runtime image because `yarn workspaces focus --all --production` recompiles native modules during the production install. This is specific to Backstage's build process 鈥� most Node.js applications can use the standard (non-dev) DHI runtime variant without additional packages.

The DHI images come with attestations that the original `node:24-trixie-slim` images don't have. Check what's attached:

```console
docker scout attest list dhi.io/node:24-alpine3.23
```

DHI images ship with 15 attestations including CycloneDX SBOM, SLSA provenance, OpenVEX, Scout health reports, secret scans, virus/malware reports, and an SLSA verification summary.

## Step 3: Add Socket Firewall protection

DHI provides `-sfw` (Socket Firewall) variants for Node.js images. Socket Firewall intercepts `npm` and `yarn` commands during the build to detect and block malicious packages before they execute install scripts.

To enable Socket Firewall, change the `-dev` tags to `-sfw-dev` in all three stages. The SFW version of the Dockerfile:

```dockerfile
# Stage 1: prepare packages
FROM --platform=$BUILDPLATFORM dhi.io/node:24-alpine3.23-sfw-dev AS packages
WORKDIR /app
COPY backstage.json package.json yarn.lock ./
COPY .yarn ./.yarn
COPY .yarnrc.yml ./
COPY packages packages
COPY plugins plugins
RUN find packages \! -name "package.json" -mindepth 2 -maxdepth 2 \
    -exec rm -rf {} \+

# Stage 2: build the packages
FROM --platform=$BUILDPLATFORM dhi.io/node:24-alpine3.23-sfw-dev AS build-packages
ENV PYTHON=/usr/bin/python3
RUN apk add --no-cache g++ make python3 sqlite-dev && \
    rm -rf /var/lib/apk/lists/*
WORKDIR /app
COPY --from=packages --chown=node:node /app .
RUN --mount=type=cache,target=/home/node/.cache/yarn,sharing=locked,uid=1000,gid=1000 \
    yarn install --immutable
COPY --chown=node:node . .
RUN yarn tsc
RUN yarn --cwd packages/backend build
RUN mkdir packages/backend/dist/skeleton packages/backend/dist/bundle \
    && tar xzf packages/backend/dist/skeleton.tar.gz \
       -C packages/backend/dist/skeleton \
    && tar xzf packages/backend/dist/bundle.tar.gz \
       -C packages/backend/dist/bundle

# Final Stage: create the runtime image
FROM dhi.io/node:24-alpine3.23-sfw-dev
ENV PYTHON=/usr/bin/python3
RUN apk add --no-cache g++ make python3 sqlite-dev && \
    rm -rf /var/lib/apk/lists/*
WORKDIR /app
COPY --from=build-packages --chown=node:node /app/.yarn ./.yarn
COPY --from=build-packages --chown=node:node /app/.yarnrc.yml ./
COPY --from=build-packages --chown=node:node /app/backstage.json ./
COPY --from=build-packages --chown=node:node /app/yarn.lock \
     /app/package.json \
     /app/packages/backend/dist/skeleton/ ./
RUN --mount=type=cache,target=/home/node/.cache/yarn,sharing=locked,uid=1000,gid=1000 \
    yarn workspaces focus --all --production \
    && rm -rf "$(yarn cache clean)"
COPY --from=build-packages --chown=node:node /app/packages/backend/dist/bundle/ ./
CMD ["node", "packages/backend", "--config", "app-config.yaml"]
```

Build this version:

```console
docker build -t backstage:dhi-sfw-dev .
```

When you build, you'll see Socket Firewall messages in the build output: `Protected by Socket Firewall` for any `yarn` and `npm` commands executed in the Dockerfile or in the running containers.

> [!TIP]
>
> The `-sfw-dev` variant is larger (1.9 GB versus 1.72 GB) because Socket Firewall adds monitoring tooling. The security benefit during `yarn install` outweighs the size increase.

## Step 4: Remove the shell and the package manager with DHI customizations

The previous steps still use the `-dev` or `-sfw-dev` variant as the runtime image, which includes a shell and package manager. DHI customizations let you start from the base (non-dev) image 鈥� which has no shell and no package manager 鈥� and add only the runtime libraries and language runtimes your application needs.

> [!IMPORTANT]
>
> When creating a customization, only add what your application needs at runtime:
>
> - **System packages** - add shared libraries (such as `sqlite-libs`) and
>   language runtimes from the DHI catalog (such as `python-3.14`).
>   Do not add build tools (such as `g++`, `make`, or `python3` from Alpine).
> - **Build tools** - keep these in the `-dev` build stage only. Never add them
>   to the runtime customization.
>
> Language runtimes installed from the DHI hardened package feed are patched and
> tracked in the image SBOM, which is why they are acceptable as system packages.
> Build tools from Alpine or Debian package feeds are not hardened and should
> never appear in the runtime image.

For Backstage, the runtime image needs:

- **sqlite-libs** - the shared library that the compiled `better-sqlite3` native module links against (added as a system package).
- **Python** - if your Backstage plugins or configuration require Python at runtime. Added as the `python-3.14` system package from the DHI catalog. Unlike `python3` installed via `apk`, this package is patched by Docker and tracked in the image SBOM.

Docker will continuously build with SLSA Level 3 compliance and patch these customized images within the guaranteed SLA for CVE patching.

To create the customization, use one of the following methods.

**Docker Hub UI**

After you mirror the Node.js DHI repository to your organization's namespace:

1. Open the mirrored Node.js repository in Docker Hub.
2. Select **Customize** and choose the `node:24-alpine3.23` tag.
3. Under **Packages**, add `sqlite-libs` and `python-3.14`.
4. Create the customization.

For more information, see [Customize an image](/dhi/how-to/customize/).

**dhictl CLI**

`dhictl` is Docker's command-line tool for managing Docker Hardened Images. It lets you browse the DHI catalog, mirror images, and create customizations directly from your terminal. You can integrate `dhictl` into CI/CD pipelines and infrastructure-as-code workflows. You can install `dhictl` as a standalone binary or as a Docker CLI plugin (`docker dhi`); for installation instructions, see [Use the DHI CLI](/dhi/how-to/cli/).

Rather than writing the customization YAML by hand, use `dhictl` to scaffold a starting point:

```console
dhictl customization prepare --org YOUR_ORG node 24-alpine3.23 \
    --destination YOUR_ORG/dhi-node \
    --name "backstage" \
    --tag-suffix "_backstage" \
    --output node-backstage.yaml
```

Edit the generated file to add the runtime libraries:

```yaml
name: backstage

source: dhi/node
tag_definition_id: node/alpine-3.23/24

destination: YOUR_ORG/dhi-node
tag_suffix: _backstage

platforms:
  - linux/amd64
  - linux/arm64

contents:
  packages:
    - sqlite-libs
    - python-3.14

accounts:
  root: true
  runs-as: node
  users:
    - name: node
      uid: 1000
  groups:
    - name: node
      gid: 1000

```

Then create the customization:

```console
dhictl customization create --org YOUR_ORG node-backstage.yaml
```

Monitor the build progress:

```console
dhictl customization build list --org YOUR_ORG YOUR_ORG/dhi-node "backstage"
```

Docker builds the customized image on its secure infrastructure and publishes it as `YOUR_ORG/dhi-node:24-alpine3.23_backstage`.

> [!NOTE]
>
> If your Backstage configuration does not require Python at runtime, you can omit the `python-3.14` from the packages list. The `sqlite-libs` package alone is sufficient to run Backstage with `better-sqlite3`.

### Update the Dockerfile

Update only the final stage of your Dockerfile to use the customized image:

```dockerfile
# Final Stage: create the runtime image
FROM YOUR_ORG/dhi-node:24-alpine3.23_backstage
WORKDIR /app
COPY --from=build --chown=node:node /app/node_modules ./node_modules
COPY --from=build --chown=node:node /app/packages/backend/dist/bundle/ ./
CMD ["node", "packages/backend", "--config", "app-config.yaml"]
```

Build this version:

```console
docker build -t backstage:dhi .
```

Since the customization includes only runtime libraries and OCI artifacts 鈥� no build tools, no package manager, no shell 鈥� the resulting image is distroless:

```console
docker run --rm YOUR_ORG/dhi-node:24-alpine3.23_backstage sh -c "echo hello"
docker: Error response from daemon: ... exec: "sh": executable file not found in $PATH
```

With the Enterprise customization:

- The runtime image is distroless 鈥� no shell, no package manager.
- Docker automatically rebuilds your customized image when the base Node.js image or any of its packages receive a security patch.
- The full chain of trust is maintained, including SLSA Build Level 3 provenance.
- Both the Node.js and Python runtimes are tracked in the image SBOM.

Confirm the container no longer has shell access:

```console
docker exec -it <container-id> sh
OCI runtime exec failed: exec failed: unable to start container process: ...
```

Use [Docker Debug](/dhi/troubleshoot/#general-debugging) if you need to troubleshoot a running distroless container.

> [!NOTE]
>
> If your organization requires FIPS/STIG compliant images, that's also an option in DHI Enterprise.

## Step 5: Verify the results

Compare the DHI-based image against the original using Docker Scout:

```console
docker scout compare backstage:dhi \
    --to backstage:init \
    --platform linux/amd64 \
    --ignore-unchanged
```

A typical comparison across the approaches shows results similar to the following:

| Metric | Original | DHI -dev | DHI -sfw-dev | Enterprise |
|--------|----------|----------|--------------|------------|
| Disk usage | 1.61 GB | 1.72 GB | 1.9 GB | 1.49 GB |
| Content size | 268 MB | 288 MB | 328 MB | 247 MB |
| Shell in runtime | Yes | Yes | Yes | No |
| Package manager | Yes | Yes | Yes | No |
| Non-root default | No | No | No | Yes |
| Socket Firewall | No | No | Yes (build) | Yes (build) / No (runtime) |
| SLSA provenance | No | Base only | Base only | Full (Level 3) |

> [!NOTE]
>
> The `-sfw-dev` variant is larger because Socket Firewall adds monitoring tooling to the image. The additional size is in the build stages, and the security benefit during `yarn install` outweighs the size increase.

For a more thorough assessment, scan with multiple tools:

```console
trivy image backstage:dhi
grype backstage:dhi
docker scout quickview backstage:dhi
```

Different scanners detect different issues. Running all three gives you the most complete view of your security posture.

## What's next

- [Customize an image](/dhi/how-to/customize/) 鈥� complete reference on the Enterprise customization UI.
- [Create and build a DHI](/dhi/how-to/build/) 鈥� learn how to write a DHI definition file, build images locally.
- [Use the DHI CLI](/dhi/how-to/cli/) 鈥� manage DHI images, mirrors, and customizations from the command line.
- [Migrate to DHI](/dhi/migration/) 鈥� for applications that work with standard DHI images without additional packages.
- [Compare images](/dhi/how-to/explore/#compare-and-evaluate-images) 鈥� evaluate security improvements between your original and hardened images.
- [Docker Debug](/dhi/troubleshoot/#general-debugging) 鈥� troubleshoot distroless containers that have no shell.

---

## Use Docker Hardened Images with Red Hat OpenShift

# Use Docker Hardened Images with Red Hat OpenShift

Docker Hardened Images (DHI) can be deployed on Red Hat OpenShift Container
Platform, but OpenShift鈥檚 security model differs from standard Kubernetes in
ways that require specific configuration. Because OpenShift runs containers
with an arbitrarily assigned user ID rather than the image鈥檚 default, you must
adjust file ownership and group permissions in your Dockerfiles to ensure
writable paths remain accessible.

This guide explains how to deploy Docker Hardened Images in OpenShift
environments, covering Security Context Constraints (SCCs), arbitrary user ID
assignment, file permission requirements, and best practices for both runtime
and development image variants.

## How OpenShift security differs from Kubernetes

OpenShift extends Kubernetes with Security Context Constraints (SCCs), which
control what actions a pod can perform and what resources it can access. While
vanilla Kubernetes uses Pod Security Standards (PSS) for similar purposes, SCCs
are more granular and enforced by default.

The key differences that affect DHI deployments:

**Arbitrary user IDs.** By default, OpenShift runs containers using an
arbitrarily assigned user ID (UID) from a range allocated to each project. The
default `restricted-v2` SCC (introduced in OpenShift 4.11) uses the
`MustRunAsRange` strategy, which overrides the `USER` directive in the container
image with a UID from the project鈥檚 allocated range (typically starting higher than
1000000000). This means even though a DHI image specifies a non-root user
(UID 65532), OpenShift will run the container as a different, unpredictable UID.

**Root group requirement.** OpenShift assigns the arbitrary UID to the root
group (GID 0). The container process always runs with `gid=0(root)`. Any
directories or files that the process needs to write to must be owned by the
root group (GID 0) with group read/write permissions. This is documented in the
[Red Hat guidelines for creating images](https://docs.openshift.com/container-platform/4.14/openshift_images/create-images.html#use-uid_create-images).

> [!IMPORTANT]
> 
> DHI images set file ownership to `nonroot:nonroot` (65532:65532) by default.
> Because the OpenShift arbitrary UID is NOT in the `nonroot` group (65532), it
> cannot write to those files 鈥� even though the pod is admitted by the SCC and
> the container starts. You must change group ownership to GID 0 for any
> writable path. This is the most common source of permission errors when
> deploying DHI on OpenShift.

**Capability restrictions.** The `restricted-v2` SCC drops all Linux
capabilities by default and enforces `allowPrivilegeEscalation: false`,
`runAsNonRoot: true`, and a `seccompProfile` of type `RuntimeDefault`. DHI
runtime images already satisfy these constraints because they run as a non-root
user and don鈥檛 require elevated capabilities.

## Pull DHI images into OpenShift

Before deploying, create an image pull secret so your OpenShift cluster can
authenticate to the DHI registry or your mirrored repository on Docker Hub.

### Create an image pull secret

```console
oc create secret docker-registry dhi-pull-secret \
    --docker-server=docker.io \
    --docker-username=<your-docker-username> \
    --docker-password=<your-docker-access-token> \
    --docker-email=<your-email>
```

If you鈥檙e pulling directly from `dhi.io` instead of a mirrored repository, set
`--docker-server=dhi.io`.

### Link the secret to a service account

Link the pull secret to the `default` service account in your project so that
all deployments can pull DHI images automatically:

```console
oc secrets link default dhi-pull-secret --for=pull
```

To use the secret with a specific service account instead:

```console
oc secrets link <service-account-name> dhi-pull-secret --for=pull
```

## Build OpenShift-compatible images from DHI

DHI runtime images are distroless 鈥� they contain no shell, no package manager,
and no `RUN`-capable environment. This means you **cannot use `RUN` commands in
the runtime stage** of your Dockerfile. All file permission adjustments for
OpenShift must happen in the `-dev` build stage, and the results must be copied
into the runtime stage using `COPY --chown`.

The core pattern for OpenShift compatibility:

1. Use a DHI `-dev` variant as the build stage (it has a shell).
1. Build your application and set GID 0 ownership in the build stage.
1. Copy the results into the DHI runtime image using `COPY --chown=<UID>:0`.

### Example: Nginx for OpenShift

```dockerfile
# Build stage 鈥� has a shell, can run commands
FROM YOUR_ORG/dhi-nginx:1.29-alpine3.23-dev AS build

# Copy custom config and set root group ownership
COPY nginx.conf /tmp/nginx.conf
COPY default.conf /tmp/default.conf

# Prepare writable directories with GID 0
# (Nginx needs to write to cache, logs, and PID file locations)
RUN mkdir -p /tmp/nginx-cache /tmp/nginx-run && \
    chgrp -R 0 /tmp/nginx-cache /tmp/nginx-run && \
    chmod -R g=u /tmp/nginx-cache /tmp/nginx-run

# Runtime stage 鈥� distroless, NO shell, NO RUN commands
FROM YOUR_ORG/dhi-nginx:1.29-alpine3.23

COPY --from=build --chown=65532:0 /tmp/nginx.conf /etc/nginx/nginx.conf
COPY --from=build --chown=65532:0 /tmp/default.conf /etc/nginx/conf.d/default.conf
COPY --from=build --chown=65532:0 /tmp/nginx-cache /var/cache/nginx
COPY --from=build --chown=65532:0 /tmp/nginx-run /var/run
```

> [!IMPORTANT]
> 
> Always use `--chown=<UID>:0` (user:root-group) when copying files into the
> runtime stage. This ensures the arbitrary UID that OpenShift assigns can
> access the files through root group membership. Never use `RUN` in the runtime
> stage 鈥� distroless DHI images have no shell.

> [!NOTE]
> 
> The UID for DHI images varies by image. Most use 65532 (`nonroot`), but some
> (like the Node.js image) may use a different UID. Verify with:
> `docker inspect dhi.io/<image>:<tag> --format '{{.Config.User}}'`

Deploy to OpenShift:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-dhi
spec:
  replicas: 1
  selector:
    matchLabels:
      app: nginx-dhi
  template:
    metadata:
      labels:
        app: nginx-dhi
    spec:
      containers:
        - name: nginx
          image: YOUR_ORG/dhi-nginx:1.29-alpine3.23
          ports:
            - containerPort: 8080
          securityContext:
            allowPrivilegeEscalation: false
            runAsNonRoot: true
            seccompProfile:
              type: RuntimeDefault
            capabilities:
              drop:
                - ALL
      imagePullSecrets:
        - name: dhi-pull-secret
```

DHI Nginx listens on port 8080 by default (not 80), which is compatible with
the non-root requirement. No SCC changes are needed.

### Example: Node.js application for OpenShift

```dockerfile
# Build stage 鈥� dev variant has shell and npm
FROM YOUR_ORG/dhi-node:24-alpine3.23-dev AS build
WORKDIR /app
COPY package*.json ./
RUN npm ci
COPY . .
RUN npm run build

# Set GID 0 on everything the runtime needs to write
RUN chgrp -R 0 /app/dist /app/node_modules && \
    chmod -R g=u /app/dist /app/node_modules

# Runtime stage 鈥� distroless, NO shell
FROM YOUR_ORG/dhi-node:24-alpine3.23
WORKDIR /app
COPY --from=build --chown=65532:0 /app/dist ./dist
COPY --from=build --chown=65532:0 /app/node_modules ./node_modules
CMD ["node", "dist/index.js"]
```

Deploy:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: node-app
spec:
  replicas: 2
  selector:
    matchLabels:
      app: node-app
  template:
    metadata:
      labels:
        app: node-app
    spec:
      containers:
        - name: app
          image: YOUR_ORG/dhi-node-app:latest
          ports:
            - containerPort: 3000
          securityContext:
            allowPrivilegeEscalation: false
            runAsNonRoot: true
            seccompProfile:
              type: RuntimeDefault
            capabilities:
              drop:
                - ALL
      imagePullSecrets:
        - name: dhi-pull-secret
```

## Handle arbitrary user IDs

OpenShift鈥檚 `restricted-v2` SCC assigns a random UID to the container process.
This UID won鈥檛 exist in `/etc/passwd` inside the image, but the container will
still run 鈥� the process just won鈥檛 have a username associated with it.

This can cause issues with applications that:

- Look up the current user鈥檚 home directory or username
- Write to directories owned by a specific UID
- Check `/etc/passwd` for the running user

### Add a `passwd` entry for the arbitrary UID

Some applications (notably those using certain Python or Java libraries) require
a valid `/etc/passwd` entry for the running user. You can handle this with a
wrapper entrypoint script.

Because this pattern requires a shell, it only works with DHI `-dev` variants or
with a DHI Enterprise customized image that includes a shell. Prepare the image
in the build stage:

```dockerfile
FROM YOUR_ORG/dhi-python:3.13-alpine3.23-dev AS build
# ... build your application ...

# Make /etc/passwd group-writable so the entrypoint can append to it
RUN chgrp 0 /etc/passwd && chmod g=u /etc/passwd

# Create the entrypoint wrapper
RUN printf '#!/bin/sh\n\
if ! whoami > /dev/null 2>&1; then\n\
  if [ -w /etc/passwd ]; then\n\
    echo "${USER_NAME:-appuser}:x:$(id -u):0:dynamic user:/tmp:/sbin/nologin" >> /etc/passwd\n\
  fi\n\
fi\n\
exec "$@"\n' > /entrypoint.sh && chmod +x /entrypoint.sh

# This pattern requires a -dev variant as runtime (has shell)
FROM YOUR_ORG/dhi-python:3.13-alpine3.23-dev
COPY --from=build --chown=65532:0 /app ./app
COPY --from=build --chown=65532:0 /entrypoint.sh /entrypoint.sh
COPY --from=build --chown=65532:0 /etc/passwd /etc/passwd
USER 65532
ENTRYPOINT ["/entrypoint.sh"]
CMD ["python", "app/main.py"]
```

> [!NOTE]
> 
> For distroless runtime images (no shell), the passwd-injection pattern is not
> possible. Instead, use the `nonroot` SCC (described in the following section) to run with the
> image鈥檚 built-in UID so the existing `/etc/passwd` entry matches the running
> process. Alternatively, OpenShift 4.x automatically injects the arbitrary UID
> into `/etc/passwd` in most cases, which resolves this for many applications.

## Use the non-root SCC for fixed UIDs

If your application requires running as the specific UID defined in the image
(typically 65532 for DHI), you can use the `nonroot` SCC instead of the default
`restricted-v2`. The `nonroot` SCC uses the `MustRunAsNonRoot` strategy, which
allows any non-zero UID.

> [!IMPORTANT]
> 
> For the `nonroot` SCC to work, the image鈥檚 `USER` directive must specify a
> **numeric** UID (for example, `65532`), not a username string like `nonroot`.
> OpenShift cannot verify that a username maps to a non-zero UID. Verify your
> DHI image with:
> `docker inspect YOUR_ORG/dhi-node:24-alpine3.23 --format '{{.Config.User}}'`
> If the output is a string rather than a number, set `runAsUser` explicitly in
> the pod spec.

Create a service account and grant it the `nonroot` SCC:

```console
oc create serviceaccount dhi-nonroot
oc adm policy add-scc-to-user nonroot -z dhi-nonroot
```

Reference the service account in your deployment:

```yaml
spec:
  template:
    spec:
      serviceAccountName: dhi-nonroot
      containers:
        - name: app
          image: YOUR_ORG/dhi-node:24-alpine3.23
          securityContext:
            runAsUser: 65532
            runAsNonRoot: true
            allowPrivilegeEscalation: false
            seccompProfile:
              type: RuntimeDefault
            capabilities:
              drop:
                - ALL
```

Verify the SCC assignment after deployment:

```console
oc get pod <pod-name> -o jsonpath='{.metadata.annotations.openshift\.io/scc}'
```

This should return `nonroot`.

When using the `nonroot` SCC with a fixed UID, the process runs as 65532
(matching the image鈥檚 file ownership), so the GID 0 adjustments are not strictly
required for paths already owned by 65532. However, applying `chown <UID>:0` is
still recommended for portability across both `restricted-v2` and `nonroot` SCCs.

## Use DHI dev variants in OpenShift

DHI `-dev` variants include a shell, package manager, and development tools.
They run as root (UID 0) by default, which conflicts with OpenShift鈥檚
`restricted-v2` SCC. There are three approaches:

### Option 1: Use dev variants only in build stages (recommended)

Use `-dev` variants only in Dockerfile build stages and never deploy them
directly to OpenShift:

```dockerfile
FROM YOUR_ORG/dhi-node:24-alpine3.23-dev AS build
WORKDIR /app
COPY package*.json ./
RUN npm ci
COPY . .
RUN npm run build

# Set root group ownership for OpenShift compatibility
RUN chgrp -R 0 /app/dist /app/node_modules && \
    chmod -R g=u /app/dist /app/node_modules

FROM YOUR_ORG/dhi-node:24-alpine3.23
WORKDIR /app
COPY --from=build --chown=65532:0 /app/dist ./dist
COPY --from=build --chown=65532:0 /app/node_modules ./node_modules
CMD ["node", "dist/index.js"]
```

The final runtime image is non-root and distroless, fully compatible with
`restricted-v2`.

### Option 2: Grant the `anyuid` SCC for debugging

If you need to run a `-dev` variant directly in OpenShift for debugging, grant
the `anyuid` SCC to a dedicated service account:

```console
oc create serviceaccount dhi-debug
oc adm policy add-scc-to-user anyuid -z dhi-debug
```

Then reference it in your pod:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: dhi-debug
spec:
  serviceAccountName: dhi-debug
  containers:
    - name: debug
      image: YOUR_ORG/dhi-node:24-alpine3.23-dev
      command: ["sleep", "infinity"]
  imagePullSecrets:
    - name: dhi-pull-secret
```

> [!IMPORTANT]
> 
> The `anyuid` SCC allows running as any UID including root. Only use this for
> temporary debugging 鈥� never in production workloads.

### Option 3: Use `oc debug` or ephemeral containers

For distroless runtime images with no shell, use OpenShift-native debugging
tools instead of `docker debug` (which only works with Docker Engine, not with
CRI-O on OpenShift).

Use `oc debug` to create a copy of a pod with a debug shell:

```console
# Create a debug pod based on a deployment
oc debug deployment/nginx-dhi

# Override the image to use a -dev variant with a shell
oc debug deployment/nginx-dhi --image=YOUR_ORG/dhi-node:24-alpine3.23-dev
```

Use ephemeral containers (OpenShift 4.12+ / Kubernetes 1.25+):

```console
kubectl debug -it <pod-name> --image=YOUR_ORG/dhi-node:24-alpine3.23-dev \
    --target=app -- sh
```

This attaches a temporary debug container to a running pod without restarting
it, sharing the pod鈥檚 process namespace.

> [!NOTE]
> 
> `docker debug` is a Docker Desktop/CLI feature for local development. It is
> not available on OpenShift clusters, which use CRI-O as their container
> runtime.

## Deploy DHI Helm charts on OpenShift

DHI provides pre-configured Helm charts for popular applications. When deploying
these charts on OpenShift, you may need to adjust security context settings.

### Inspect chart values first

Before installing, check what security context values the chart exposes:

```console
helm registry login dhi.io

helm show values oci://dhi.io/<chart-name> --version <version> | grep -A 20 securityContext
```

The available value paths vary by chart, so always check `values.yaml` before
setting overrides.

### Install with OpenShift overrides

The following example shows a typical installation pattern. Adjust the `--set`
paths based on what `helm show values` returns for your specific chart:

```console
helm install my-release oci://dhi.io/<chart-name> \
    --version <version> \
    --set "imagePullSecrets[0].name=dhi-pull-secret" \
    -f openshift-values.yaml
```

Create an `openshift-values.yaml` with security context overrides appropriate
for your chart:

```yaml
# Example 鈥� adjust keys based on `helm show values` output
podSecurityContext:
  runAsNonRoot: true
  seccompProfile:
    type: RuntimeDefault

securityContext:
  allowPrivilegeEscalation: false
  capabilities:
    drop:
      - ALL
```

> [!NOTE]
> 
> DHI Helm chart value paths are not standardized across charts. For example,
> one chart may use `image.imagePullSecrets`, while another uses
> `global.imagePullSecrets`. Always consult the specific chart鈥檚 documentation
> or `values.yaml`.

## Verify your deployment

After deploying a DHI image to OpenShift, verify the security configuration.

### Check the assigned SCC

```console
oc get pods -o 'custom-columns=NAME:.metadata.name,SCC:.metadata.annotations.openshift\.io/scc'
```

Runtime DHI images should show `restricted-v2` (or `nonroot` if you configured
it).

### Check the running UID

```console
oc exec <pod-name> -- id
```

With the `restricted-v2` SCC, you should see output like:

```text
uid=1000650000 gid=0(root) groups=0(root),1000650000
```

The UID is from the project鈥檚 allocated range, and the primary GID is always 0
(root group). With the `nonroot` SCC and `runAsUser: 65532`, you would see
`uid=65532`.

### Confirm the image is distroless

```console
oc exec <pod-name> -- sh -c "echo hello"
```

For runtime (non-dev) DHI images, this command should fail with an error
indicating that `sh` was not found in `$PATH`. The exact error format varies
between CRI-O versions.

### Scan the deployed image

Use Docker Scout to verify the security posture of the deployed image (run this
from your local machine, not on the cluster):

```console
docker scout cves YOUR_ORG/dhi-nginx:1.29-alpine3.23
docker scout quickview YOUR_ORG/dhi-nginx:1.29-alpine3.23
```

## Common issues and solutions

**Pod fails to start with 鈥渃ontainer has runAsNonRoot and image has group or
user ID set to root.鈥�** This happens when deploying a DHI `-dev` variant with
the default `restricted-v2` SCC. Either use the runtime variant instead, or
grant the `anyuid` SCC to the service account.

**Application cannot write to a directory.** The arbitrary UID assigned by
OpenShift doesn鈥檛 have write permissions. This is the most common issue with DHI
on OpenShift. All writable paths must be owned by GID 0 with group write
permissions. Fix this in the build stage:
`chgrp -R 0 /path && chmod -R g=u /path`, then `COPY --chown=<UID>:0` into the
runtime stage.

**Application fails with 鈥渦ser not found鈥� or 鈥渘o matching entries in passwd
file.鈥�** Some applications require a valid `/etc/passwd` entry. OpenShift 4.x
automatically injects the arbitrary UID into `/etc/passwd` in most cases. If
your application still fails, use the passwd-injection pattern (requires a `-dev`
variant) or use the `nonroot` SCC to run with the image鈥檚 built-in UID.

**Pod fails to bind to port 80 or 443.** Ports lower than 1024 require root
privileges. DHI images use unprivileged ports by default (for example, Nginx
uses 8080). Configure your OpenShift Service to map the external port to the
container鈥檚 unprivileged port:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: nginx-dhi
spec:
  ports:
    - port: 80
      targetPort: 8080
  selector:
    app: nginx-dhi
```

**ImagePullBackOff with 鈥渦nauthorized: authentication required.鈥�** Verify the
pull secret is correctly configured and linked to the service account. Check
with `oc get secret dhi-pull-secret` and `oc describe sa default`.

**Dockerfile build fails with 鈥渆xec: not found鈥� in runtime stage.** You are
using `RUN` in a distroless runtime stage. DHI runtime images have no shell, so
`RUN` commands cannot execute. Move all `RUN` commands to the `-dev` build stage
and use `COPY --chown` to transfer results.

## DHI and OpenShift compatibility summary

|Feature                      |DHI runtime                      |DHI `-dev`          |DHI with Enterprise customization|
|-----------------------------|---------------------------------|--------------------|---------------------------------|
|Default SCC (`restricted-v2`)|Yes, with GID 0 permissions      |Requires `anyuid`   |Yes, with GID 0 permissions      |
|Non-root by default          |Yes (UID 65532)                  |No (root)           |Yes (configurable UID)           |
|Arbitrary UID support        |Yes, with `chown <UID>:0`        |Yes                 |Yes, with `chown <UID>:0`        |
|Distroless (no shell)        |Yes 鈥� no `RUN` in Dockerfile     |No                  |Yes 鈥� no `RUN` in Dockerfile     |
|Unprivileged ports           |Yes (higher than 1024)                 |Configurable        |Yes (higher than 1024)                 |
|SLSA Build Level 3           |Yes                              |Yes                 |Yes                              |
|Debug on cluster             |`oc debug` / ephemeral containers|`oc exec` with shell|`oc debug` / ephemeral containers|

## What鈥檚 next

- [Use an image in Kubernetes](/dhi/how-to/k8s/) 鈥� general DHI Kubernetes deployment guide.
- [Customize an image](/dhi/how-to/customize/) 鈥� add packages to DHI images using Enterprise customization.
- [Debug a container](/dhi/troubleshoot/#general-debugging) 鈥� troubleshoot distroless containers with Docker Debug (local development).
- [Managing SCCs](https://docs.openshift.com/container-platform/4.14/authentication/managing-security-context-constraints.html) 鈥� Red Hat鈥檚 reference documentation on Security Context Constraints.
- [Creating images for OpenShift](https://docs.openshift.com/container-platform/4.14/openshift_images/create-images.html) 鈥� Red Hat鈥檚 guidelines for building OpenShift-compatible container images.

---

## Explore VEX statements in Docker Hardened Images

# Explore VEX statements in Docker Hardened Images

Standard vulnerability scanners report CVEs against packages present in an
image. With Docker Hardened Images, those packages are there by design, but each
reported CVE has a VEX statement explaining whether it is exploitable in this
specific product configuration. This guide walks through scanning a Docker
Hardened Image with and without VEX and auditing the justification behind every
suppression.

## Prerequisites

- [Docker Desktop](https://www.docker.com/products/docker-desktop/), authenticated
  to `dhi.io`. Sign in with `docker login dhi.io`. Docker Desktop includes
  Docker Scout, which fetches the VEX attestation.
- A vulnerability scanner. This guide shows examples for Docker Scout,
  [Trivy](https://trivy.dev/), and [Grype](https://github.com/anchore/grype).
  Trivy and Grype can also run as Docker containers with no installation needed.
- `jq` (optional), for filtering the VEX file.

## Expose daemon for Windows users

The `-v /var/run/docker.sock:/var/run/docker.sock` socket mount used in the
containerized scanner commands throughout this guide does not work on Docker
Desktop for Windows. To use containerized scanners on Windows, go to
**Settings > General** in Docker Desktop and turn on **Expose daemon on
tcp://localhost:2375 without TLS**. Then replace
`-v /var/run/docker.sock:/var/run/docker.sock` with
`-e DOCKER_HOST=tcp://host.docker.internal:2375` in every containerized
scanner command.

> [!WARNING]
>
> Exposing the daemon on TCP without TLS makes your system vulnerable to
> remote code execution attacks. Turn off the setting when you are done
> testing.

## Step 1: Scan without VEX

Sign in to the Docker Hardened Images registry:

```console
$ docker login dhi.io
```

Then pull the image:

```console
$ docker pull dhi.io/python:3.13
```

Then scan without VEX to see the raw CVE count. Docker Scout automatically
applies VEX on Docker Hardened Images. To see the unfiltered CVE baseline,
use Trivy or Grype.

**Trivy**

```console
$ trivy image --scanners vuln dhi.io/python:3.13
```

If Trivy isn't installed, run it in a container:

```console
$ docker run --rm \
  -v /var/run/docker.sock:/var/run/docker.sock \
  aquasec/trivy:latest image --scanners vuln dhi.io/python:3.13
```

Example output:

```plaintext
Total: 30 (UNKNOWN: 0, LOW: 15, MEDIUM: 11, HIGH: 4, CRITICAL: 0)
```

**Grype**

```console
$ grype dhi.io/python:3.13
```

If Grype isn't installed, run it in a container:

```console
$ docker run --rm \
  -v /var/run/docker.sock:/var/run/docker.sock \
  anchore/grype:latest docker:dhi.io/python:3.13
```

Example output:

```plaintext
NAME          INSTALLED              FIXED IN     TYPE  VULNERABILITY       SEVERITY
libc6         2.41-12+deb13u2                     deb   CVE-2018-20796      Negligible
libc6         2.41-12+deb13u2        (won't fix)  deb   CVE-2026-4437       High
libc6         2.41-12+deb13u2        (won't fix)  deb   CVE-2026-5450       Critical
...
```

The output lists CVEs across `libc6`, `libncursesw6`, `libsqlite3-0`, `libuuid1`,
`zlib1g`, and others, all runtime dependencies that Python needs to function.
These packages are present by design.

A scan result like this doesn't mean every reported CVE requires patching. It means
these CVEs have been reported against packages present in the image. Whether any of
those CVEs are actually exploitable in this configuration is a separate
question, and that's exactly what VEX answers.

## Step 2: Fetch the VEX attestation

Export the VEX attestation to a local file:

```console
$ docker scout vex get registry://dhi.io/python:3.13 --output python-vex.json
```

The `registry://` prefix tells Scout to fetch the attestation from the registry
rather than the local image store. Because you pulled the image in Step 1, it
already exists locally, and without this prefix Scout would find no attestation
there.

This fetches a signed OpenVEX document from `registry.scout.docker.com`,
Docker's supply chain metadata registry for all Docker Hardened Images. The
document records Docker's exploitability assessment for every CVE found in the
image's SBOM.

> [!NOTE]
>
> Docker Scout fetches this file automatically when scanning. You only need to
> download it explicitly for scanners that don't natively integrate it, or to
> run the `jq` queries in Steps 5 and 6.

## Step 3: Scan with VEX applied

**Docker Scout**

Docker Scout automatically fetches and applies the VEX attestation with no local
file needed:

```console
$ docker scout cves dhi.io/python:3.13
```

Example output:

```plaintext
    鉁� SBOM obtained from attestation, 47 packages indexed
    鉁� Provenance obtained from attestation
    鉁� VEX statements obtained from attestation
    鉁� No vulnerable package detected
```

**Trivy**

Pass the VEX file with the `--vex` flag:

```console
$ trivy image --scanners vuln --vex python-vex.json dhi.io/python:3.13
```

If Trivy isn't installed, run it in a container:

```console
$ docker run --rm \
  -v /var/run/docker.sock:/var/run/docker.sock \
  -v "$(pwd)/python-vex.json:/tmp/vex.json" \
  aquasec/trivy:latest image --scanners vuln --vex /tmp/vex.json dhi.io/python:3.13
```

Example output:

```plaintext
Total: 0 (UNKNOWN: 0, LOW: 0, MEDIUM: 0, HIGH: 0, CRITICAL: 0)

Some vulnerabilities have been ignored/suppressed. Use the '--show-suppressed' flag to display them.
```

**Grype**

Pass the VEX file with the `--vex` flag:

```console
$ grype dhi.io/python:3.13 --vex python-vex.json
```

If Grype isn't installed, run it in a container:

```console
$ docker run --rm \
  -v /var/run/docker.sock:/var/run/docker.sock \
  -v "$(pwd)/python-vex.json:/tmp/vex.json" \
  anchore/grype:latest docker:dhi.io/python:3.13 --vex /tmp/vex.json
```

Example output:

```plaintext
No vulnerabilities found
```

Same image, same packages, same CVE database. The only difference is context.
The scanner matched each CVE against the VEX file and suppressed every one that
Docker assessed as not exploitable.

The packages are still there. Check the SBOM and you will see `libc6`,
`libsqlite3-0`, and every other package from Step 1. Zero CVEs does not mean
the packages were removed. It means each reported CVE has a documented reason
why it does not apply to this product configuration.

VEX is an open standard: the attestation travels with the image and any
compliant scanner reads the same reasoning.

## Step 4: Inspect every suppression and its justification

Docker Scout and Grype suppress VEX-matched CVEs but do not surface the
justification code in their output. Use Trivy's `--show-suppressed` flag to see
every suppressed CVE alongside its per-CVE justification code.

```console
$ trivy image --scanners vuln --vex python-vex.json --show-suppressed dhi.io/python:3.13
```

If Trivy isn't installed, run it in a container:

```console
$ docker run --rm \
  -v /var/run/docker.sock:/var/run/docker.sock \
  -v "$(pwd)/python-vex.json:/tmp/vex.json" \
  aquasec/trivy:latest image --scanners vuln --vex /tmp/vex.json --show-suppressed dhi.io/python:3.13
```

Example output:

```plaintext
Suppressed Vulnerabilities (Total: 28)
======================================
鈹屸攢鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹攢鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹攢鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹攢鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹攢鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹�
鈹�   Library    鈹�  Vulnerability   鈹� Severity 鈹�    Status    鈹�                     Statement                     鈹�
鈹溾攢鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹尖攢鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹尖攢鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹尖攢鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹尖攢鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹�
鈹� libc6        鈹� CVE-2010-4756    鈹� LOW      鈹� not_affected 鈹� vulnerable_code_cannot_be_controlled_by_adversary 鈹�
鈹� libsqlite3-0 鈹� CVE-2025-70873   鈹� LOW      鈹� not_affected 鈹� vulnerable_code_not_present                       鈹�
鈹� ...          鈹� ...              鈹� ...      鈹� ...          鈹� ...                                               鈹�
鈹斺攢鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹粹攢鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹粹攢鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹粹攢鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹粹攢鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹�
```

The `Statement` column shows the machine-readable justification code from the
VEX file.

The justification codes have precise meanings:

- `vulnerable_code_cannot_be_controlled_by_adversary`: the vulnerable code
  path exists in the package, but an attacker cannot trigger it in this
  configuration.
- `vulnerable_code_not_present`: the vulnerable code was not compiled into
  this build or is otherwise absent.
- `inline_mitigations_already_exist`: Docker has applied a backport or patch
  that addresses the CVE in this image.

For the full list of justification codes, see [VEX status
reference](/dhi/core-concepts/vex/#not_affected-justification-codes).

Every suppression is documented, auditable, and verifiable with any VEX-enabled
scanner.

## Step 5: Read Docker's reasoning for a specific CVE

The justification codes are machine-readable; the `status_notes` field in the
VEX file contains Docker's human-readable reasoning. Use `jq` to look up a
specific CVE:

```console
$ jq '.statements[] | select(.vulnerability.name == "CVE-2010-4756") | {status, justification, status_notes}' python-vex.json
```

Example output:

```json
{
  "status": "not_affected",
  "justification": "vulnerable_code_cannot_be_controlled_by_adversary",
  "status_notes": "Standard POSIX behavior in glibc. Applications using glob need to impose limits themselves. Requires authenticated access and is considered unimportant by Debian."
}
```

The `status_notes` field explains Docker's reasoning in plain language. For
CVE-2010-4756, the glob behavior described by the CVE is standard POSIX
behavior, requires authenticated access, and is classified as unimportant by
the Debian security team.

Each statement also lists the affected products as Package URLs (PURLs), for
example `pkg:deb/debian/glibc@2.41-12%2Bdeb13u2?os_distro=trixie&os_name=debian&os_version=13`.
Trivy matched this statement to `libc6` in the image's SBOM by comparing that
PURL against the packages recorded in the SBOM.

> [!IMPORTANT]
>
> PURL matching is strict. Scanners must match VEX statements to packages
> using the full PURL string, including the `os_name`, `os_version`, and
> `os_distro` qualifiers. Matching on package name alone risks applying a
> suppression from one OS version to a different version where the CVE *is*
> exploitable.

## Step 6: Filter VEX statements by status

Once you have `python-vex.json`, use `jq` to query it directly.

Count statements by status:

```console
$ jq '[.statements[].status] | group_by(.) | map({status: .[0], count: length})' python-vex.json
```

List all CVEs under active investigation:

```console
$ jq '[.statements[] | select(.status == "under_investigation") | {cve: .vulnerability.name, products: [.products[]."@id"]}]' python-vex.json
```

List any CVEs with `affected` status:

```console
$ jq '[.statements[] | select(.status == "affected") | {cve: .vulnerability.name, action: .action_statement}]' python-vex.json
```

The `affected` query returns an empty array for the current `dhi.io/python:3.13`
image, which is the expected result for an actively maintained tag. To see `affected`
entries across all DHI Python versions, query the full VEX feed:

```console
$ curl -s https://raw.githubusercontent.com/docker-hardened-images/advisories/main/vex/python/dhi-python.vex.json \
  | jq '[.statements[] | select(.status == "affected") | {cve: .vulnerability.name, action: .action_statement}]'
```

For status definitions and justification codes, see the [VEX status
reference](/dhi/core-concepts/vex/#vex-status-reference).

## What's next

- Scan with other tools: Learn how to apply DHI VEX statements with Trivy
  (VEX Hub) and Grype in [Scan Docker Hardened
  Images](/dhi/how-to/scan/).
- Write your own VEX for child images: If you build on top of a DHI and
  want to suppress CVEs in packages you add, see [Create an exception using
  VEX](/scout/how-tos/create-exceptions-vex/).
- VEX status reference: For status definitions, justification codes, and
  why DHI does not use `fixed`, see [Vulnerability Exploitability eXchange
  (VEX)](/dhi/core-concepts/vex/#vex-status-reference).
- Browse the VEX feed directly: The raw VEX data is published at
  [github.com/docker-hardened-images/advisories](https://github.com/docker-hardened-images/advisories/tree/main/vex),
  organized by image name.

---

## Containerize a Django application

# Containerize a Django application

## Prerequisites

- You have installed the latest version of [Docker
  Desktop](/get-started/get-docker/).
- You have [uv](https://docs.astral.sh/uv/) installed, or you can use Docker to
  scaffold the project without a local Python or uv installation.

> [!TIP]
>
> If you're new to Docker, start with the [Docker
> basics](/get-started/docker-concepts/the-basics/what-is-a-container/) guide
> to get familiar with key concepts like images, containers, and Dockerfiles.

---

## Overview

This guide walks you through containerizing a Django application with Docker. By
the end, you will:

- Initialize a Django project using uv, either locally or inside a Docker
  Hardened Image container.
- Create a production-ready Dockerfile using [Docker Hardened Images
  (DHI)](/dhi/).
- Add a `development` stage to your Dockerfile and configure Compose Watch for
  automatic code syncing.

---

## Create the Django project

You can bootstrap the project with a local uv installation, or entirely inside a
container using the same DHI image the Dockerfile uses, with no local Python
required.

**Local (uv)**

1. Initialize the project pinned to Python 3.14, then navigate into it:

   ```console
   $ uv init --python 3.14 django-docker
   $ cd django-docker
   ```

2. Add Django and Gunicorn, then scaffold the Django project:

   ```console
   $ uv add django gunicorn
   $ uv run django-admin startproject myapp .
   ```

**Container (DHI)**

The DHI dev image already has Python 3.14, so the bootstrapped project will
match the Dockerfile exactly.

1. Create the project directory and navigate into it:

   ```console
   $ mkdir django-docker && cd django-docker
   ```

2. Initialize the project, add dependencies, and scaffold. All in one container
   run:

   ```console
   $ docker run --rm -v $PWD:$PWD -w $PWD \
     -e UV_LINK_MODE=copy \
     dhi.io/python:3.14-alpine3.23-dev \
     sh -c "pip install --quiet --root-user-action=ignore uv && uv init --name django-docker --python 3.14 . && uv add django gunicorn && uv run django-admin startproject myapp ."
   ```

   > [!NOTE]
   >
   > The previous command uses Mac/Linux shell syntax. On Windows, adjust
   > the path: PowerShell uses `${PWD}`, Command Prompt uses `%cd%`, Git Bash
   > requires `MSYS_NO_PATHCONV=1` with `$(pwd -W)`.

Your directory should now contain the following files:

```text
鈹溾攢鈹€ .python-version
鈹溾攢鈹€ main.py
鈹溾攢鈹€ manage.py
鈹溾攢鈹€ myapp/
鈹� 鈹溾攢鈹€ __init__.py
鈹� 鈹溾攢鈹€ asgi.py
鈹� 鈹溾攢鈹€ settings.py
鈹� 鈹溾攢鈹€ urls.py
鈹� 鈹斺攢鈹€ wsgi.py
鈹溾攢鈹€ pyproject.toml
鈹溾攢鈹€ uv.lock
鈹斺攢鈹€ README.md
```

---

## Create a production Dockerfile

Docker Hardened Images are production-ready base images maintained by Docker
that minimize attack surface. For more details, see [Docker Hardened
Images](/dhi/).

1. Sign in to the DHI registry:

   ```console
   $ docker login dhi.io
   ```

2. Create a `.dockerignore` file to exclude local artifacts from the build
   context:

   ```text {title=".dockerignore"}
   .venv/
   __pycache__/
   *.pyc
   .git/
   ```

3. Create a `Dockerfile` with the following contents:

   ```dockerfile {title="Dockerfile"}
   # syntax=docker/dockerfile:1

   # Build stage: the -dev image includes tools needed to install packages.
   FROM dhi.io/python:3.14-alpine3.23-dev AS builder

   # Prevent Python from writing .pyc files to disk.
   ENV PYTHONDONTWRITEBYTECODE=1
   # Prevent Python from buffering stdout/stderr so logs appear immediately.
   ENV PYTHONUNBUFFERED=1

   RUN pip install --quiet --root-user-action=ignore uv
   # Use copy mode since the cache and build filesystem are on different volumes.
   ENV UV_LINK_MODE=copy

   WORKDIR /app

   # Install dependencies into a virtual environment using cache and bind mounts
   # so neither uv nor the lock files need to be copied into the image.
   RUN --mount=type=cache,target=/root/.cache/uv \
       --mount=type=bind,source=uv.lock,target=uv.lock \
       --mount=type=bind,source=pyproject.toml,target=pyproject.toml \
       uv sync --frozen --no-install-project

   # Runtime stage: minimal DHI image with no shell or package manager,
   # already runs as the nonroot user.
   FROM dhi.io/python:3.14-alpine3.23

   # Prevent Python from buffering stdout/stderr so logs appear immediately.
   ENV PYTHONUNBUFFERED=1
   # Activate the virtual environment copied from the build stage.
   ENV PATH="/app/.venv/bin:$PATH"

   WORKDIR /app

   # Copy the pre-built virtual environment and application source code.
   COPY --from=builder /app/.venv /app/.venv
   COPY . .

   EXPOSE 8000

   # Run Gunicorn as the production WSGI server.
   CMD ["gunicorn", "myapp.wsgi:application", "--bind", "0.0.0.0:8000"]
   ```

4. Create a `compose.yaml` file:

   ```yaml {title="compose.yaml"}
   services:
     web:
       build: .
       ports:
         - "8000:8000"
   ```

### Run the application

From the `django-docker` directory, run:

```console
$ docker compose up --build
```

Open a browser and navigate to [http://localhost:8000](http://localhost:8000).
You should see the Django welcome page.

Press `ctrl`+`c` to stop the application.

---

## Set up a development environment

The production setup uses Gunicorn and requires a full image rebuild to pick up
code changes. For development, you can add a `development` stage to your
Dockerfile that uses Django's built-in server, and configure Compose Watch to
automatically sync code changes into the running container without a rebuild.

### Update the Dockerfile

Replace your `Dockerfile` with a multi-stage version that adds a `development`
stage alongside `production`:

```dockerfile {title="Dockerfile"}
# syntax=docker/dockerfile:1

# Build stage: the -dev image includes tools needed to install packages.
FROM dhi.io/python:3.14-alpine3.23-dev AS builder

# Prevent Python from writing .pyc files to disk.
ENV PYTHONDONTWRITEBYTECODE=1
# Prevent Python from buffering stdout/stderr so logs appear immediately.
ENV PYTHONUNBUFFERED=1

RUN pip install --quiet --root-user-action=ignore uv
# Use copy mode since the cache and build filesystem are on different volumes.
ENV UV_LINK_MODE=copy

WORKDIR /app

# Install dependencies into a virtual environment using cache and bind mounts
# so neither uv nor the lock files need to be copied into the image.
RUN --mount=type=cache,target=/root/.cache/uv \
    --mount=type=bind,source=uv.lock,target=uv.lock \
    --mount=type=bind,source=pyproject.toml,target=pyproject.toml \
    uv sync --frozen --no-install-project

# The development stage inherits the -dev image and virtual environment from
# the builder. Django's built-in server reloads when Compose Watch syncs files.
FROM builder AS development

ENV PATH="/app/.venv/bin:$PATH"

COPY . .
EXPOSE 8000
CMD ["python", "manage.py", "runserver", "0.0.0.0:8000"]

# The production stage uses the minimal runtime image, which has no shell,
# no package manager, and already runs as the nonroot user.
FROM dhi.io/python:3.14-alpine3.23 AS production

# Prevent Python from buffering stdout/stderr so logs appear immediately.
ENV PYTHONUNBUFFERED=1
# Activate the virtual environment copied from the build stage.
ENV PATH="/app/.venv/bin:$PATH"

WORKDIR /app

# Copy only the pre-built virtual environment and application source code.
COPY --from=builder /app/.venv /app/.venv
COPY . .

EXPOSE 8000

# Run Gunicorn as the production WSGI server.
CMD ["gunicorn", "myapp.wsgi:application", "--bind", "0.0.0.0:8000"]
```

### Update the Compose file

Replace your `compose.yaml` with the following. It targets the `development`
stage, adds a PostgreSQL database, and configures Compose Watch:

```yaml {title="compose.yaml"}
services:
  web:
    build:
      context: .
      # Build the development stage from the multi-stage Dockerfile.
      target: development
    ports:
      - "8000:8000"
    environment:
      # Enable Django's verbose debug error pages (the dev server always auto-reloads).
      - DEBUG=1
      # Database connection settings passed to Django via environment variables.
      - POSTGRES_DB=myapp
      - POSTGRES_USER=postgres
      - POSTGRES_PASSWORD=password
      - POSTGRES_HOST=db
      - POSTGRES_PORT=5432
    # Wait for the database to pass its healthcheck before starting the web service.
    depends_on:
      db:
        condition: service_healthy
    develop:
      watch:
        # Sync source file changes directly into the container so Django's
        # dev server can reload them without a full image rebuild.
        - action: sync
          path: .
          target: /app
          ignore:
            - __pycache__/
            - "*.pyc"
            - .git/
            - .venv/
        # Rebuild the image when dependencies change.
        - action: rebuild
          path: pyproject.toml
        - action: rebuild
          path: uv.lock
  db:
    image: dhi.io/postgres:18
    restart: always
    volumes:
      # Persist database data across container restarts.
      - db-data:/var/lib/postgresql
    environment:
      - POSTGRES_DB=myapp
      - POSTGRES_USER=postgres
      - POSTGRES_PASSWORD=password
    # Expose the port only to other services on the Compose network,
    # not to the host machine.
    expose:
      - 5432
    # Only report healthy once PostgreSQL is ready to accept connections,
    # so the web service doesn't start before the database is available.
    healthcheck:
      test: ["CMD", "pg_isready"]
      interval: 10s
      timeout: 5s
      retries: 5
volumes:
  db-data:
```

The `sync` action pushes file changes directly into the running container so
Django's dev server reloads them automatically. A change to `pyproject.toml` or
`uv.lock` triggers a full image rebuild instead.

> [!NOTE]
>
> To learn more about Compose Watch, see [Use Compose
> Watch](/compose/how-tos/file-watch/).

### Add the PostgreSQL driver

Add the `psycopg` adapter to your project:

**Local (uv)**

```console
$ uv add 'psycopg[binary]'
```

**Container (DHI)**

```console
$ docker run --rm -v $PWD:$PWD -w $PWD \
  -e UV_LINK_MODE=copy \
  dhi.io/python:3.14-alpine3.23-dev \
  sh -c "pip install --quiet --root-user-action=ignore uv && uv add 'psycopg[binary]'"
```

Then update `myapp/settings.py` to read `DEBUG` and `DATABASES` from environment
variables:

```python {title="myapp/settings.py"}
import os

DEBUG = os.environ.get('DEBUG', '0') == '1'

DATABASES = {
    "default": {
        "ENGINE": "django.db.backends.postgresql",
        "NAME": os.environ.get("POSTGRES_DB", "myapp"),
        "USER": os.environ.get("POSTGRES_USER", "postgres"),
        "PASSWORD": os.environ.get("POSTGRES_PASSWORD", "password"),
        "HOST": os.environ.get("POSTGRES_HOST", "localhost"),
        "PORT": os.environ.get("POSTGRES_PORT", "5432"),
    }
}
```

### Run with Compose Watch

Start the development stack:

```console
$ docker compose watch
```

Open a browser and navigate to [http://localhost:8000](http://localhost:8000).

Try editing a file, for example add a view to `myapp/views.py`. Compose Watch
syncs the change into the container and Django's dev server reloads
automatically. If you update `pyproject.toml` or `uv.lock`, Compose Watch
triggers a full image rebuild.

Press `ctrl`+`c` to stop.

---

## Summary

In this guide, you:

- Bootstrapped a Django project using uv, with options for both local and
  containerized setup.
- Created a production-ready Dockerfile using Docker Hardened Images and uv for
  dependency management.
- Added a `development` stage to the `Dockerfile` and configured Compose Watch
  for fast iterative development with a PostgreSQL database.

Related information:

- [Dockerfile reference](/reference/dockerfile/)
- [Compose file reference](/reference/compose-file/)
- [Use Compose Watch](/compose/how-tos/file-watch/)
- [Docker Hardened Images](/dhi/)
- [Multi-stage builds](/build/building/multi-stage/)
- [uv documentation](https://docs.astral.sh/uv/)

---

## Docker Build Cloud: Reclaim your time with fast, multi-architecture builds

# Docker Build Cloud: Reclaim your time with fast, multi-architecture builds

<!-- vale Vale.Spelling = NO -->

98% of developers spend up to an hour every day waiting for builds to finish
([Incredibuild: 2022 Big Dev Build Times](https://www.incredibuild.com/survey-report-2022)).
Heavy, complex builds can become a major roadblock for development teams,
slowing down both local development and CI/CD pipelines.

<!-- vale Vale.Spelling = YES -->

Docker Build Cloud speeds up image build times to improve developer
productivity, reduce frustrations, and help you shorten the release cycle.

## Who鈥檚 this for?

- Anyone who wants to tackle common causes of slow image builds: limited local
  resources, slow emulation, and lack of build collaboration across a team.
- Developers working on older machines who want to build faster.
- Development teams working on the same repository who want to cut wait times
  with a shared cache.
- Developers performing multi-architecture builds who don鈥檛 want to spend hours
  configuring and rebuilding for emulators.

## What you鈥檒l learn

- Building container images faster locally and in CI
- Accelerating builds for multi-platform images
- Reusing pre-built images to expedite workflows

## Tools integration

Works well with Docker Compose, GitHub Actions, and other CI solutions

<div id="dbc-lp-survey-anchor"></div>

## Why Docker Build Cloud?

Docker Build Cloud is a service that lets you build container images faster,
both locally and in CI. Builds run on cloud infrastructure optimally
dimensioned for your workloads, with no configuration required. The service
uses a remote build cache, ensuring fast builds anywhere and for all team
members.

Docker Build Cloud provides several benefits over local builds:

- Improved build speed
- Shared build cache
- Native multi-platform builds

There鈥檚 no need to worry about managing builders or infrastructure 鈥� simply
connect to your builders and start building. Each cloud builder provisioned to
an organization is completely isolated to a single Amazon EC2 instance, with a
dedicated EBS volume for build cache and encryption in transit. That means
there are no shared processes or data between cloud builders.

<div id="dbc-lp-survey-anchor"></div>

## Demo: set up and use Docker Build Cloud in development

With Docker Build Cloud, you can easily shift the build workload from local machines
to the cloud, helping you achieve faster build times, especially for multi-platform builds.

In this demo, you'll see:

- How to setup the builder locally
- How to use Docker Build Cloud with Docker Compose
- How the image cache speeds up builds for others on your team

<div id="dbc-lp-survey-anchor"></div>

## Demo: Using Docker Build Cloud in CI

Docker Build Cloud can significantly decrease the time it takes for your CI builds
take to run, saving you time and money.

Since the builds run remotely, your CI runner can still use the Docker tooling CLI
without needing elevated permissions, making your builds more secure by default.

In this demo, you will see:

- How to integrate Docker Build Cloud into a variety of CI platforms
- How to use Docker Build Cloud in GitHub Actions to build multi-architecture images
- Speed differences between a workflow using Docker Build Cloud and a workflow running natively
- How to use Docker Build Cloud in a GitLab Pipeline

<div id="dbc-lp-survey-anchor"></div>

## Common challenges and questions

#### Is Docker Build Cloud a standalone product or a part of Docker Desktop?

Docker Build Cloud is a service that can be used both with Docker Desktop and
standalone. It lets you build your container images faster, both locally and in
CI, with builds running on cloud infrastructure. The service uses a remote
build cache, ensuring fast builds anywhere and for all team members.

When used with Docker Desktop, the [Builds view](/desktop/use-desktop/builds/)
works with Docker Build Cloud out-of-the-box. It shows information about your
builds and those initiated by your team members using the same builder,
enabling collaborative troubleshooting.

To use Docker Build Cloud without Docker Desktop, you must
[download and install](/build-cloud/setup/#use-docker-build-cloud-without-docker-desktop)
a version of Buildx with support for Docker Build Cloud (the `cloud` driver).
If you plan on building with Docker Build Cloud using the `docker compose
build` command, you also need a version of Docker Compose that supports Docker
Build Cloud.

#### How does Docker Build Cloud work with Docker Compose?

Docker Compose works out of the box with Docker Build Cloud. Install the Docker
Build Cloud-compatible client (buildx) and it works with both commands.

#### How many minutes are included in Docker Build Cloud Team plans?

Pricing details for Docker Build Cloud can be found on the [pricing page](https://www.docker.com/pricing?ref=Docs&refAction=DocsGuidesBuildCloudFaq).

#### I鈥檓 a Docker personal user. Can I try Docker Build Cloud?

Docker subscribers (Pro, Team, Business) receive a set number of minutes each
month, shared across the account, to use Build Cloud.

If you do not have a Docker subscription, you may sign up for a free Personal
account and start a trial of Docker Build Cloud. Personal accounts are limited to a
single user.

For teams to receive the shared cache benefit, they must either be on a Docker
Team or Docker Business subscription.

#### Does Docker Build Cloud support CI platforms? Does it work with GitHub Actions?

Yes, Docker Build Cloud can be used with various CI platforms including GitHub
Actions, CircleCI, Jenkins, and others. It can speed up your build pipelines,
which means less time spent waiting and context switching.

Docker Build Cloud can be used with GitHub Actions to automate your build,
test, and deployment pipeline. Docker provides a set of official GitHub Actions
that you can use in your workflows.

Using GitHub Actions with Docker Build Cloud is straightforward. With a
one-line change in your GitHub Actions configuration, everything else stays the
same. You don't need to create new pipelines. Learn more in the [CI
documentation](/build-cloud/ci/) for Docker Build Cloud.

<div id="dbc-lp-survey-anchor"></div>

---
