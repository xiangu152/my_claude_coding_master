---
title: "Docker 订阅"
source: "https://docs.docker.com/subscription/"
version: "latest"
---

# Docker 订阅

> 原始文档来源：https://docs.docker.com/subscription/

---

## Docker Desktop license agreement

# Docker Desktop license agreement

Docker Desktop is licensed under the [Docker Subscription Service Agreement](https://www.docker.com/legal/docker-subscription-service-agreement). When you download and install Docker Desktop, you're asked to agree to these terms.

The Docker Subscription Service Agreement states:

- Docker Desktop is free for:
  - Small businesses (fewer than 250 employees AND less than $10 million in annual revenue)
  - Personal use
  - Education
  - Non-commercial open source projects
- Docker Desktop requires a paid subscription for:
  - Professional use in larger organizations
  - Government entities
  - Commercial use beyond the free tier limits
- Paid subscriptions that include Docker Desktop:
  - Docker Pro, Team, and Business subscriptions

## Understanding licensing terms

For detailed information about how these terms may affect your organization, see:

- [Subscription updates blog post](https://www.docker.com/blog/updating-product-subscriptions/)
- [Docker subscription FAQs](https://www.docker.com/pricing/faq) to learn how this may affect companies using Docker Desktop.

> [!NOTE]
>
> The licensing and distribution terms for Docker and Moby open-source projects, such as Docker Engine, aren't changing.

Docker Desktop is built using open-source software. For information about the licensing of open-source components in Docker Desktop, select the whale menu > **About Docker Desktop** > **Acknowledgements**.

## Open source components

Docker Desktop distributes some components that are licensed under the
GNU General Public License. [Download the source code for these components here](https://download.docker.com/opensource/License.tar.gz).

---

## Plan FAQs

# Plan FAQs

For more information on Docker subscriptions, see [Docker subscription overview](/subscription/).  

## Can I transfer my subscription from one user or organization account to another?

Subscriptions are non-transferable between accounts or organizations.

## Can I pause or delay my Docker subscription?

You can't pause or delay a subscription, but you can downgrade your subscription. If a subscription invoice isn't paid by the due date, there's a 15-day grace period starting from the due date.

## Does Docker offer academic pricing?

Contact the [Docker Sales Team](https://www.docker.com/company/contact) for information about academic pricing options.

## How can I contribute to Docker content?

Docker offers two content contribution programs:

- [Docker-Sponsored Open Source Program (DSOS)](/docker-hub/repos/manage/trusted-content/dsos-program/) for open source projects
- [Docker Verified Publisher (DVP)](/docker-hub/repos/manage/trusted-content/dvp-program/) for commercial publishers

You can also join the [Developer Preview Program](https://www.docker.com/community/get-involved/developer-preview/) or sign up for early access programs to participate in research and try new features.

> [!TIP]
> 
> Need to upgrade? <a href="https://www.docker.com/pricing?ref=Docs&refAction=DocsSubscriptionFaq" id="pricing-link" class="link" rel="noopener">Compare Docker Team and Docker Business</a> to choose the plan that best fits your team's needs.

---

## Manage plans

# Manage plans

You can add and manage your plans from the billing portal in Docker Home. Within the billing portal, you use the product catalog to view
self-serve Docker products, while the Overview page shows your active plans with options to upgrade or cancel.

## Set up a new plan

You can purchase Docker plans through the product catalog:

1. Sign in to [Docker Home](https://app.docker.com/), then choose your personal
   account or your organization account.
1. Go to **Billing**.
1. Select **Browse products**.
   - The **Products** page contains products and upgrades available for self-serve.
   - Some products in the catalog may apply to personal accounts, organization accounts, or both.
   - Each product tile uses an account-type flag so you know the difference.
1. Select **View plans** to add a plan to your Docker account.
1. Verify your billing details, continue to payment, and complete checkout.

## Upgrade plans

You can upgrade active plans from the billing Overview page. 

1. Sign in to [Docker Home](https://app.docker.com/), then choose your personal
   account or your organization account.
1. Go to **Billing** to view the Overview page. 
   - The Overview page contains your active plans and payment details.
   - **Active plans** contains information about plan type, renewal cadence, and usage.
1. Top up or upgrade from the **Active plans** section. 
    - **Active plans** is where you complete all management actions.
    - Depending on the plan type, you can select the action menu button or the **Manage** button to choose from available actions.
1. Verify your billing details, continue to payment, and complete checkout.

> [!TIP]
> Billing behaviors vary from plan to plan. Learn more about usage, downgrading, or canceling plans 
> from the relevant
> [product page](/subscription/plans/).

## Sales-led products

Some products are sales-led. You must
<a href="https://www.docker.com/pricing/contact-sales/" id="dkr_docs_cs_plans_manage" class="link" rel="noopener">contact sales</a> to opt in.

## What's next

- [Learn about available plans](/subscription/plans/)
- [Set up payment information](/billing/payment-method/)
- [View invoices](/billing/history/)
- To learn more about managing your billing details, see [Billing](/billing/).

---

## AI Governance plan

# AI Governance plan

[AI Governance](/ai/sandboxes/governance/) lets organization owners enforce policies for members across supported Docker AI products, like [Docker Sandbox](/ai/sandboxes/). When you add an AI Governance plan, you work with your Docker sales representative to determine the number of licenses needed, which are applied to your account.

> [!TIP]
> To subscribe to AI Governance, <a href="https://www.docker.com/pricing/contact-sales/" id="dkr_docs_cs_plans_aigov" class="link" rel="noopener">contact sales</a>.

## Usage

AI Governance lets organization owners enforce [governance policies](/ai/sandboxes/governance/org/) to license-holding members. Organization policies override the local policies of a license-holding member.

You can [assign AI Governance licenses](/admin/organization/manage/manage-licenses/) to any organization member, even if they don't occupy a Docker Team or Docker Business seat. For best practice, review available licenses as you add new members since members without an AI Governance license can still use Docker AI products.

## Billing behaviors

AI Governance plan pricing is based on the number of licenses purchased. To add or remove licenses, contact your Docker sales representative.

---

## DHI plans

# DHI plans

[Docker Hardened Images (DHI)](/dhi/) are secure, minimal, production-ready container images maintained by Docker.

- DHI Community is free and available to every developer.
- DHI Select is a paid plan for organizations that need compliance-ready images and SLA-backed patching. You can self-serve it in the billing portal.
- DHI Enterprise is for organizations with advanced security and customization requirements. To subscribe, <a href="https://www.docker.com/pricing/contact-sales/" id="dkr_docs_cs_plans_dhi_enterprise" class="link" rel="noopener">contact sales</a>.

For a full plan comparison, see the [Docker pricing page](https://www.docker.com/pricing/).

## Usage

DHI Community gives you access to hardened base images from a public registry at no cost or additional setup. Any organization can pull hardened base images directly from `dhi.io`.

When you upgrade from DHI Community to DHI Select, you purchase a set number of repositories that are mirrored into your organization's namespace. Entitlements are scoped to the organization account that you assign them to during checkout. All organization members can then pull from those mirrored repositories.

DHI Enterprise extends DHI Select with unlimited customizations, optional full catalog access, the Hardened System Packages repository, and an Extended Lifecycle Support add-on.

For details on setting up and managing repositories, see [Get started with DHI Select and Enterprise](/dhi/how-to/select-enterprise/).

## Billing behaviors

DHI Select is an annual plan billed per repository from the date your plan starts. Repositories added mid-cycle are prorated for the remainder of the billing period. You can add more repositories to your DHI Select plan by going to **Active plans** in the billing portal. For steps, see [Manage plans](/subscription/manage/#upgrade-plans).

## Disable auto-renewal

If you want to revert your plan to DHI Community, you must disable auto-renewal. Disabling auto-renewal is deferred to the end of the current billing cycle and your repository access remains active until then. To disable auto-renewal:

1. Sign in to [Docker Home](https://app.docker.com/) and go to **Billing**.
1. From **Active plans**, select **Manage** next to **Hardened Images**.
1. Select **Disable auto-renewal**.

## Remove repositories

You may also remove repositories from your plan. Repository removals are deferred to the end of the current billing cycle. You can remove repositories at any time, but you cannot stop a plan mid-cycle to receive a partial refund. Repository access remains active until the cycle ends.

To remove repositories:

1. Sign in to [Docker Home](https://app.docker.com/) and go to **Billing**.
1. From **Active plans**, select **Manage** next to **Hardened Images**.
    - Select **Remove repositories** to adjust your repository count.
    - To keep your current repository count after renewal, select **Cancel scheduled change**. 
    - Cancellations and repository removals take effect at the end of the current annual billing cycle.

If you're subscribed to DHI Enterprise, reach out to your sales representative to change your DHI plan.

---

## Docker plans

# Docker plans

Docker plans refer to plans that upgrade your account type from the basic free plan to a paid plan. Paid Docker plans come with higher usage limits, commercial
licensing, and expanded feature sets.

- Docker Personal is free for individual developers. Docker Pro adds unlimited private repositories, Docker Build Cloud, and commercial Docker Desktop use.
- Docker Team and Docker Business are plans for organizations, with Team adding audit logs and role-based access control, and Business adding SSO, SCIM, hardened Docker Desktop, and image access management.

To upgrade your free Docker plan in the billing portal, see [Manage plans](/subscription/manage/).

## Usage

Docker Personal and Docker Pro are Docker plans for individual account types while Docker Team and Docker Business are Docker plans for organization account types. For a full feature and pricing breakdown, see the
<a href="https://www.docker.com/pricing/" id="dkr_docs_index_pricing_docker_plans" class="link" rel="noopener">Docker pricing page</a>. 

> [!TIP]
> If you're upgrading from a Personal plan to a Team plan
> and want to keep your username,
> [convert your user account into an organization](/admin/organization/setup/convert-account/).

## Billing behaviors

Docker individual and organization plans are billed at a flat rate per user per month, with monthly or
annual billing options.
Upgrading your plan immediately extends access to all features
and entitlements.

### Organization seats

For Docker Team and Docker Business, you can purchase more seats for new members to extend access to your paid Docker plan. To add or remove seats from your Docker plan:

1. Sign in to [Docker Home](https://app.docker.com/), then choose your organization account.
1. Go to **Billing** to view the Overview page, then go to **Active plans**.
1. From the Docker Team or Docker Business tile, select the action menu.
   - Select **Add seats** or **Remove seats** from the drop-down menu.
   - When you add or remove seats, review your current seats against your new total seats.
   - When you remove seats, you must remove members from your organization.
1. Verify your billing details, continue to payment, and complete checkout.

To learn how to manage seats from the Admin Console, see
[Manage seats](/admin/organization/manage/manage-seats/).

### Docker Offload licenses

[Docker Offload](/offload/) licenses are available for Docker Team and Docker Business plans. Once assigned to your account, organization owners can [manage license assignments](/admin/organization/manage/manage-licenses/) in the Admin Console.

To add Docker Offload licenses, you must <a href="https://www.docker.com/pricing/contact-sales/" id="dkr_docs_cs_plans_docker_offload" class="link" rel="noopener">contact sales</a>.

### Docker Build Cloud minutes

Each plan includes a base allocation of [Docker Build Cloud](/build-cloud/) build minutes per
month. Base minutes reset on an annual or monthly cadence, and don't accumulate. Additional purchased
minutes expire at the end of your billing period.

To purchase additional minutes:

1. From [Docker Home](https://app.docker.com/), choose your organization.
1. Select Build Cloud, then Build minutes.
1. From the **Minute breakdown** table, select **Add minutes**.
1. Choose your additional minute amount.
1. Verify your billing details, continue to payment, and complete checkout.

Your additional minutes appear on the Build minutes page immediately.

### Testcontainers Cloud minutes

Each plan includes a base allocation of Testcontainers Cloud runtime minutes
per month. Base minutes reset monthly and don't accumulate.

You can add Testcontainers Cloud runtime minutes in two ways:

- <a href="https://www.docker.com/pricing/contact-sales/" id="dkr_docs_cs_plans_docker_testcontainers" class="link" rel="noopener">Contact sales</a> to
  pre-purchase runtime minutes at $3 per 100 minutes. Pre-purchased minutes
  expire at the end of your billing period.
- Use on-demand runtime minutes at $4 per 100 minutes, billed at the end of
  each monthly cycle.

## Cancel a Docker plan

> [!NOTE]
> If you have a sales-assisted Docker Business plan,
> you must contact your account manager to cancel.

You can cancel at any time before your renewal date, but you can't pause or delay a plan. If an invoice isn't paid by the due date, there's a 15-day grace period starting from the due date. While the unused portion is not refundable, you still retain access to paid features until the end of the current billing cycle. Canceling your paid plans may have implications for collaborators or organization members:

- Docker Pro private repository collaborators are removed and additional private repositories are locked.
- Docker Team or Docker Business members provisioned through SCIM without a password will be locked out. Remove SSO connections and verified domains if your organization uses single sign-on.
- For paid individual and organization plans, you must convert private repositories to fit your new plan limits.

Canceling a paid plan returns your account to Docker Personal or a basic organization account.
To cancel your plan:

1. Sign in to [Docker Home](https://app.docker.com/) and go to **Billing**.
2. From **Active plans**, select the action menu next to your Docker plan.
3. Select **Cancel plan** and complete the feedback survey.

---

## Gordon plans

# Gordon plans

[Gordon](/ai/gordon/) is an AI assistant for Docker workflows. For pricing details, see the [Gordon product page](https://www.docker.com/products/gordon/).

- Gordon Base is included at no cost with Docker plans. It covers everyday Docker workflow questions with a baseline monthly usage allowance.
- Gordon Plus is for developers who use Gordon regularly. It increases your monthly usage allowance above Base.
- Gordon Max is for power users who rely on Gordon throughout their workflow. It offers a significantly higher usage allowance than Plus.
- Gordon Ultra is for developers with the highest usage needs. It provides the maximum monthly allowance available on a self-serve plan.

To upgrade to a Gordon paid plan, see [Manage plans](/subscription/manage/).

## Usage

Gordon usage is measured in questions. When you upgrade from Gordon Base to a paid plan, your usage multiplies the Base allowance. Usage is capped across three time windows: 4-hour, daily, and monthly. For full limit details, see [Gordon usage limits](/ai/gordon/usage-limits/).

> [!IMPORTANT]
> Gordon plans apply to personal Docker accounts only. If you purchase a Gordon
> plan while signed in with an organization account, the plan applies
> to your personal account automatically.

## Billing behaviors

Gordon plans are billed monthly at the first of the month. If you purchase after the first of the month, your invoice reflects prorated charges for the remaining days in the cycle. Subscribing to a Gordon plan charges you at the time of purchase. When you upgrade your Gordon plan, usage limits are immediately effective after payment. 

## Downgrade or disable auto-renewal

When you downgrade a Gordon plan, the change takes effect on the 1st of the following month. Disabling auto-renewal for a Gordon plan returns your paid Gordon plan to Gordon Base in the same pattern. For example, if you downgrade from Gordon Ultra to Gordon Plus on April 15:

- From April 15 through April 30, you retain Ultra access and usage limits.
- On May 1, you are charged the Gordon Plus plan rate and Gordon Plus usage limits take effect.

To downgrade or disable auto-renewal:

1. Sign in to [Docker Home](https://app.docker.com/) and go to **Billing**.
1. From **Active plans**, select **Manage** next to your Gordon plan.
1. Select **Downgrade plan** to switch to a lower tier, or **Disable auto renewal** to end your plan after the current billing cycle.

> [!IMPORTANT]
> Docker does not issue refunds or credits
> for remaining days in the current cycle.

---
