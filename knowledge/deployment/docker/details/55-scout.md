---
title: "Docker Scout"
source: "https://docs.docker.com/scout/"
version: "latest"
---

# Docker Scout

> 原始文档来源：https://docs.docker.com/scout/

---

## Advisory database sources and matching service

# Advisory database sources and matching service

Reliable information sources are key for Docker Scout's ability to
surface relevant and accurate assessments of your software artifacts.
Given the diversity of sources and methodologies in the industry,
discrepancies in vulnerability assessment results can and do happen.
This page describes how the Docker Scout advisory database
and its CVE-to-package matching approach works to deal with these discrepancies.

## Advisory database sources

Docker Scout aggregates vulnerability data from multiple sources.
The data is continuously updated to ensure that your security posture
is represented using the latest available information, in real-time.

Docker Scout uses the following package repositories and security trackers:

<!-- vale off -->

- [AlmaLinux Security Advisory](https://errata.almalinux.org/)
- [Alpine secdb](https://secdb.alpinelinux.org/)
- [Amazon Linux Security Center](https://alas.aws.amazon.com/)
- [Bitnami Vulnerability Database](https://github.com/bitnami/vulndb)
- [CISA Known Exploited Vulnerability Catalog](https://www.cisa.gov/known-exploited-vulnerabilities-catalog)
- [CISA Vulnrichment](https://github.com/cisagov/vulnrichment)
- [Chainguard Security Feed](https://packages.cgr.dev/chainguard/osv/all.json)
- [Debian Security Bug Tracker](https://security-tracker.debian.org/tracker/)
- [Exploit Prediction Scoring System (EPSS)](https://api.first.org/epss/)
- [GitHub Advisory Database](https://github.com/advisories/)
- [GitLab Advisory Database](https://gitlab.com/gitlab-org/advisories-community/)
- [Golang VulnDB](https://github.com/golang/vulndb)
- [National Vulnerability Database](https://nvd.nist.gov/)
- [Oracle Linux Security](https://linux.oracle.com/security/)
- [Photon OS 3.0 Security Advisories](https://github.com/vmware/photon/wiki/Security-Updates-3)
- [Python Packaging Advisory Database](https://github.com/pypa/advisory-database)
- [RedHat Security Data](https://www.redhat.com/security/data/metrics/)
- [Rocky Linux Security Advisory](https://errata.rockylinux.org/)
- [RustSec Advisory Database](https://github.com/rustsec/advisory-db)
- [SUSE Security CVRF](http://ftp.suse.com/pub/projects/security/cvrf/)
- [Ubuntu CVE Tracker](https://people.canonical.com/~ubuntu-security/cve/)
- [Wolfi Security Feed](https://packages.wolfi.dev/os/security.json)
- [inTheWild, a community-driven open database of vulnerability exploitation](https://github.com/gmatuz/inthewilddb)

<!-- vale on -->

When you enable Docker Scout for your Docker organization,
a new database instance is provisioned on the Docker Scout platform.
The database stores the Software Bill of Materials (SBOM) and other metadata about your images.
When a security advisory has new information about a vulnerability,
your SBOM is cross-referenced with the CVE information to detect how it affects you.

For more details on how image analysis works, see the [image analysis page](/scout/explore/analysis/).

## Severity and scoring priority

Docker Scout uses two main principles when determining severity and scoring for
CVEs:

   - Source priority
   - CVSS version preference

For source priority, Docker Scout follows this order:

  1. Vendor advisories: Scout always uses the severity and scoring data from the
     source that matches the package and version. For example, Debian data for
     Debian packages.

  2. NIST scoring data: If the vendor doesn't provide scoring data for a CVE,
     Scout falls back to NIST scoring data.

For CVSS version preference, once Scout has selected a source, it prefers CVSS
v4 over v3 when both are available, as v4 is the more modern and precise scoring
model.

## Vulnerability matching

Traditional tools often rely on broad [Common Product Enumeration (CPE)](https://en.wikipedia.org/wiki/Common_Platform_Enumeration) matching,
which can lead to many false-positive results.

Docker Scout uses [Package URLs (PURLs)](https://github.com/package-url/purl-spec)
to match packages against CVEs, which yields more precise identification of vulnerabilities.
PURLs significantly reduce the chances of false positives, focusing only on genuinely affected packages.

## Supported package ecosystems

Docker Scout supports the following package ecosystems:

- .NET
- GitHub packages
- Go
- Java
- JavaScript
- PHP
- Python
- RPM
- Ruby
- `alpm` (Arch Linux)
- `apk` (Alpine Linux)
- `deb` (Debian Linux and derivatives)

---

## Data collection and storage in Docker Scout

# Data collection and storage in Docker Scout

Docker Scout's image analysis works by collecting metadata from the container
images that you analyze. This metadata is stored on the Docker Scout platform.

## Data transmission

This section describes the data that Docker Scout collects and sends to the
platform.

### Image metadata

Docker Scout collects the following image metadata:

- Image creation timestamp
- Image digest
- Ports exposed by the image
- Environment variable names and values
- Name and value of image labels
- Order of image layers
- Hardware architecture
- Operating system type and version
- Registry URL and type

Image digests are created for each layer of an image when the image is built
and pushed to a registry. They are SHA256 digests of the contents of a layer.
Docker Scout doesn't create the digests; they're read from the image manifest.

The digests are matched against your own private images and Docker's database
of public images to identify images that share the same layers. The image that
shares most of the layers is considered a base image match for the image that's
currently being analyzed.

### SBOM metadata

Software Bill of Material (SBOM) metadata is used to match package types
and versions with vulnerability data to infer whether an image is affected.
When the Docker Scout platform receives information from security advisories
about new CVEs or other risk factors, such as leaked secrets, it cross-references
this information with the SBOM. If there's a match, Docker Scout displays the
results in the user interfaces where Docker Scout data is surfaced,
such as the Docker Scout Dashboard and in Docker Desktop.

Docker Scout collects the following SBOM metadata:

- Package URLs (PURL)
- Package author and description
- License IDs
- Package name and namespace
- Package scheme and size
- Package type and version
- Filepath within the image
- The type of direct dependency
- Total package count

The PURLs in Docker Scout follow the
[purl-spec](https://github.com/package-url/purl-spec) specification. Package
information is derived from the contents of image, including OS-level programs
and packages, and application-level packages such as maven, npm, and so on.

### Environment metadata

If you integrate Docker Scout with your runtime environment via the
[Sysdig integration](/scout/integrations/environment/sysdig/),
Docker Scout collects the following data points about your deployments:

- Kubernetes namespace
- Workload name
- Workload type (for example, DaemonSet)

### Local analysis

For images analyzed locally on a developer's machine, Docker Scout only
transmits PURLs and layer digests. This data isn't persistently stored on the
Docker Scout platform; it's only used to run the analysis.

### Provenance

For images with [provenance attestations](/build/metadata/attestations/slsa-provenance/),
Docker Scout stores the following data in addition to the SBOM:

- Materials
- Base image
- VCS information
- Dockerfile

## Data storage

For the purposes of providing the Docker Scout service, data is stored using:

- Amazon Web Services (AWS) on servers located in US East
- Google Cloud Platform (GCP) on servers located in US East

Data is used according to the processes described at
[docker.com/legal](https://www.docker.com/legal/) to provide the key
capabilities of Docker Scout.

---

## Docker Scout image analysis

# Docker Scout image analysis

When you activate image analysis for a repository,
Docker Scout automatically analyzes new images that you push to that repository.

Image analysis extracts the Software Bill of Material (SBOM)
and other image metadata,and evaluates it against vulnerability data from
[security advisories](/scout/deep-dive/advisory-db-sources/).

If you run image analysis as a one-off task using the CLI or Docker Desktop,
Docker Scout won't store any data about your image.
If you enable Docker Scout for your container image repositories however,
Docker Scout saves a metadata snapshot of your images after the analysis.
As new vulnerability data becomes available, Docker Scout recalibrates the analysis using the metadata snapshot, which means your security status for images is updated in real-time.
This dynamic evaluation means there's no need to re-analyze images when new CVE information is disclosed.

Docker Scout image analysis is available by default for Docker Hub repositories.
You can also integrate third-party registries and other services. To learn more,
see [Integrating Docker Scout with other systems](/scout/integrations/).

## Activate Docker Scout on a repository

See [Subscriptions and features](https://www.docker.com/pricing?ref=Docs&refAction=DocsScoutAnalysis)
to learn how many Scout-enabled repositories come with each subscription tier.

Before you can activate image analysis on a repository in a third-party registry,
the registry must be integrated with Docker Scout for your Docker organization.
Docker Hub is integrated by default. For more information, see
See [Container registry integrations](/scout/integrations/#container-registries)

> [!NOTE]
>
> You must have the **Editor** or **Owner** role in the Docker organization to
> activate image analysis on a repository.

To activate image analysis:

1. Go to [Repository settings](https://scout.docker.com/settings/repos) in the Docker Scout Dashboard.
2. Select the repositories that you want to enable.
3. Select **Enable image analysis**.

If your repositories already contain images,
Docker Scout pulls and analyzes the latest images automatically.

## Analyze registry images

To trigger image analysis for an image in a registry, push the image to a
registry that's integrated with Docker Scout, to a repository where image
analysis is activated.

> [!NOTE]
>
> Image analysis on the Docker Scout platform has a maximum image file size
> limit of 10 GB, unless the image has an SBOM attestation.
> See [Maximum image size](#maximum-image-size).

1. Sign in with your Docker ID, either using the `docker login` command or the
   **Sign in** button in Docker Desktop.
2. Build and push the image that you want to analyze.

   ```console
   $ docker build --push --tag <org>/<image:tag> --provenance=true --sbom=true .
   ```

   Building with the `--provenance=true` and `--sbom=true` flags attaches
   [build attestations](/build/metadata/attestations/) to the image. Docker
   Scout uses attestations to provide more fine-grained analysis results.

   > [!NOTE]
   >
   > The default `docker` driver only supports build attestations if you use the
   > [containerd image store](/desktop/features/containerd/).

3. Go to the [Images page](https://scout.docker.com/reports/images) in the Docker Scout Dashboard.

   The image appears in the list shortly after you push it to the registry.
   It may take a few minutes for the analysis results to appear.

## Analyze images locally

You can analyze local images with Docker Scout using Docker Desktop or the
`docker scout` commands for the Docker CLI.

### Docker Desktop

> [!NOTE]
>
> Docker Desktop background indexing supports images up to 10 GB in size.
> See [Maximum image size](#maximum-image-size).

To analyze an image locally using the Docker Desktop GUI:

1. Pull or build the image that you want to analyze.
2. Go to the **Images** view in the Docker Dashboard.
3. Select one of your local images in the list.

   This opens the [Image details view](/scout/explore/image-details-view/), showing a
   breakdown of packages and vulnerabilities found by the Docker Scout analysis
   for the image you selected.

### CLI

The `docker scout` CLI commands provide a command line interface for using Docker
Scout from your terminal.

- `docker scout quickview`: summary of the specified image, see [Quickview](#quickview)
- `docker scout cves`: local analysis of the specified image, see [CVEs](#cves)
- `docker scout compare`: analyzes and compares two images

By default, the results are printed to standard output.
You can also export results to a file in a structured format,
such as Static Analysis Results Interchange Format (SARIF).

#### Quickview

The `docker scout quickview` command provides an overview of the
vulnerabilities found in a given image and its base image.

```console
$ docker scout quickview traefik:latest
    鉁� SBOM of image already cached, 311 packages indexed

  Your image  traefik:latest  鈹�    0C     2H     8M     1L
  Base image  alpine:3        鈹�    0C     0H     0M     0L
```

If your the base image is out of date, the `quickview` command also shows how
updating your base image would change the vulnerability exposure of your image.

```console
$ docker scout quickview postgres:13.1
    鉁� Pulled
    鉁� Image stored for indexing
    鉁� Indexed 187 packages

  Your image  postgres:13.1                 鈹�   17C    32H    35M    33L
  Base image  debian:buster-slim            鈹�    9C    14H     9M    23L
  Refreshed base image  debian:buster-slim  鈹�    0C     1H     6M    29L
                                            鈹�    -9    -13     -3     +6
  Updated base image  debian:stable-slim    鈹�    0C     0H     0M    17L
                                            鈹�    -9    -14     -9     -6
```

#### CVEs

The `docker scout cves` command gives you a complete view of all the
vulnerabilities in the image. This command supports several flags that lets you
specify more precisely which vulnerabilities you're interested in, for example,
by severity or package type:

```console
$ docker scout cves --format only-packages --only-vuln-packages \
  --only-severity critical postgres:13.1
    鉁� SBOM of image already cached, 187 packages indexed
    鉁� Detected 10 vulnerable packages with a total of 17 vulnerabilities

     Name            Version         Type        Vulnerabilities
鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€
  dpkg        1.19.7                 deb      1C     0H     0M     0L
  glibc       2.28-10                deb      4C     0H     0M     0L
  gnutls28    3.6.7-4+deb10u6        deb      2C     0H     0M     0L
  libbsd      0.9.1-2                deb      1C     0H     0M     0L
  libksba     1.3.5-2                deb      2C     0H     0M     0L
  libtasn1-6  4.13-3                 deb      1C     0H     0M     0L
  lz4         1.8.3-1                deb      1C     0H     0M     0L
  openldap    2.4.47+dfsg-3+deb10u5  deb      1C     0H     0M     0L
  openssl     1.1.1d-0+deb10u4       deb      3C     0H     0M     0L
  zlib        1:1.2.11.dfsg-1        deb      1C     0H     0M     0L
```

For more information about these commands and how to use them, refer to the CLI
reference documentation:

- [`docker scout quickview`](/reference/cli/docker/scout/quickview/)
- [`docker scout cves`](/reference/cli/docker/scout/cves/)

## Vulnerability severity assessment

Docker Scout assigns a severity rating to vulnerabilities based on
vulnerability data from [advisory sources](/scout/deep-dive/advisory-db-sources/).
Advisories are ranked and prioritized depending on the type of package that's
affected by a vulnerability. For example, if a vulnerability affects an OS
package, the severity level assigned by the distribution maintainer is
prioritized.

If the preferred advisory source has assigned a severity rating to a CVE, but
not a CVSS score, Docker Scout falls back to displaying a CVSS score from
another source. The severity rating from the preferred advisory and the CVSS
score from the fallback advisory are displayed together. This means a
vulnerability can have a severity rating of `LOW` with a CVSS score of 9.8, if
the preferred advisory assigns a `LOW` rating but no CVSS score, and a fallback
advisory assigns a CVSS score of 9.8.

Vulnerabilities that haven't been assigned a CVSS score in any source are
categorized as **Unspecified** (U).

Docker Scout doesn't implement a proprietary vulnerability metrics system. All
metrics are inherited from security advisories that Docker Scout integrates
with. Advisories may use different thresholds for classifying vulnerabilities,
but most of them adhere to the CVSS v3.0 specification, which maps CVSS scores
to severity ratings according to the following table:

| CVSS score | Severity rating  |
| ---------- | ---------------- |
| 0.1 鈥� 3.9  | **Low** (L)      |
| 4.0 鈥� 6.9  | **Medium** (M)   |
| 7.0 鈥� 8.9  | **High** (H)     |
| 9.0 鈥� 10.0 | **Critical** (C) |

For more information, see [Vulnerability Metrics (NIST)](https://nvd.nist.gov/vuln-metrics/cvss).

Note that, given the advisory prioritization and fallback mechanism described
earlier, severity ratings displayed in Docker Scout may deviate from this
rating system.

## Maximum image size

Image analysis on the Docker Scout platform, and analysis triggered by background
indexing in Docker Desktop, has an image file size limit of 10 GB (uncompressed).
To analyze images larger than that:

- Attach an [SBOM attestation](/build/metadata/attestations/sbom/) at build-time. When an image includes an SBOM attestation, Docker Scout uses it instead of generating one, so the 10 GB limit doesn鈥檛 apply.
- Alternatively, you can use the [CLI](#cli) to analyze the image locally. The 10 GB limit doesn鈥檛 apply when using the CLI. If the image includes an SBOM attestation, the CLI uses it to complete the analysis faster.

---

## Dashboard

# Dashboard

The [Docker Scout Dashboard](https://scout.docker.com/) helps you share the
analysis of images in an organization with your team. Developers can see an
overview of their security status across all their images from Docker Hub, and
get remediation advice at their fingertips. It helps team members in roles such
as security, compliance, and operations to know what vulnerabilities and issues
they need to focus on.

## Overview

![A screenshot of the Docker Scout Dashboard overview](/scout/images/dashboard-overview.webp?border=true)

The **Overview** tab provides a summary for the repositories in the selected
organization.

At the top of this page, you can select which **Environment** to view.
By default, the most recently pushed images are shown. To learn more about
environments, see [Environment monitoring](/scout/integrations/environment/).

The **Policy** boxes show your current compliance rating for each policy, and a
trend indication for the selected environment. The trend describes the policy
delta for the most recent images compared to the previous version.
For more information about policies, see [Policy Evaluation](/scout/policy/).

The vulnerability chart shows the total number of vulnerabilities for images in
the selected environment over time. You can configure the timescale for the
chart using the drop-down menu.

Use the header menu at the top of the website to access the different main
sections of the Docker Scout Dashboard:

- **Policies**: shows the policy compliance for the organization, see [Policies](#policies)
- **Images**: lists all Docker Scout-enabled repositories in the organization, see [Images](#images)
- **Base images**: lists all base images used by repositories in an organization
- **Packages**: lists all packages across repositories in the organization
- **Vulnerabilities**: lists all CVEs in the organization's images, see [Vulnerabilities](#vulnerabilities)
- **Integrations**: create and manage third-party integrations, see [Integrations](#integrations)
- **Settings**: manage repository settings, see [Settings](#settings)

## Policies

The **Policies** view shows a breakdown of policy compliance for all of the
images in the selected organization and environment. You can use the **Image**
drop-down menu to view a policy breakdown for a specific environment.

For more information about policies, see [Policy Evaluation](/scout/policy/).

## Images

The **Images** view shows all images in Scout-enabled repositories for the selected environment.
You can filter the list by selecting a different environment, or by repository name using the text filter.

![Screenshot of the images view](/scout/images/dashboard-images.webp)

For each repository, the list displays the following details:

- The repository name (image reference without the tag or digest)
- The most recent tag of the image in the selected environment
- Operating systems and architectures for the most recent tag
- Vulnerabilities status for the most recent tag
- Policy status for the most recent tag

Selecting a repository link takes you to a list of all images in that repository that have been analyzed.
From here you can view the full analysis results for a specific image,
and compare tags to view the differences in packages and vulnerabilities

Selecting an image link takes you to a details view for the selected tag or digest.
This view contains two tabs that detail the composition and policy compliance for the image:

- **Policy status** shows the policy evaluation results for the selected image.
  Here you also have links for details about the policy violations.

  For more information about policy, see [Policy Evaluation](/scout/policy/).

- **Image layers** shows a breakdown of the image analysis results.
  You can get a complete view of the vulnerabilities your image contains
  and understand how they got in.

## Vulnerabilities

The **Vulnerabilities** view shows a list of all vulnerabilities for images in the organization.
This list includes details about CVE such as the severity and Common Vulnerability Scoring System (CVSS) score,
as well as whether there's a fix version available.
The CVSS score displayed here is the highest score out of all available [sources](/scout/deep-dive/advisory-db-sources/).

Selecting the links on this page opens the vulnerability details page,
This page is a publicly visible page, and shows detailed information about a CVE.
You can share the link to a particular CVE description with other people
even if they're not a member of your Docker organization or signed in to Docker Scout.

If you are signed in, the **My images** tab on this page lists all of your images
affected by the CVE.

## Integrations

The **Integrations** page lets you create and manage your Docker Scout
integrations, such as environment integrations and registry integrations. For
more information on how to get started with integrations, see
[Integrating Docker Scout with other systems](/scout/integrations/).

## Settings

The settings menu in the Docker Scout Dashboard contains:

- [**Repository settings**](#repository-settings) for enabling and disabling repositories
- [**Notifications**](#notification-settings) for managing your notification preferences for Docker Scout.

### Repository settings

When you enable Docker Scout for a repository,
Docker Scout analyzes new tags automatically when you push to that repository.
To enable repositories in Amazon ECR, Azure ACR, or other third-party registries,
you first need to integrate them.
See [Container registry integrations](/scout/integrations/#container-registries)

### Notification settings

The [Notification settings](https://scout.docker.com/settings/notifications)
page is where you can change the preferences for receiving notifications from
Docker Scout. Notification settings are personal, and changing notification
settings only affects your personal account, not the entire organization.

The purpose of notifications in Docker Scout is to raise awareness about
upstream changes that affect you. Docker Scout will notify you about when a new
vulnerability is disclosed in a security advisory, and it affects one or more
of your images. You will not receive notifications about changes to
vulnerability exposure or policy compliance as a result of pushing a new image.

> [!NOTE]
>
> Notifications are only triggered for the _last pushed_ image tags for each
> repository. "Last pushed" refers to the image tag that was most recently
> pushed to the registry and analyzed by Docker Scout. If the last pushed image
> is not affected by a newly disclosed CVE, then no notification will be
> triggered.

The available notification settings are:

- **Repository scope**

  Here you can select whether you want to enable notifications for all
  repositories, or only for specific repositories. These settings apply to the
  currently selected organization, and can be changed for each organization you
  are a member of.
  - **All repositories**: select this option to receive notifications for all
    repositories that you have access to.
  - **Specific repositories**: select this option to receive notifications for
    specific repositories. You can then enter the names of repositories you
    want to receive notifications for.

- **Delivery preferences**

  These settings control how you receive notifications from Docker Scout. They
  apply to all organizations that you're a member of.
  - **Notification pop-ups**: select this check-box to receive notification
    pop-up messages in the Docker Scout Dashboard.
  - **OS notifications**: select this check-box to receive OS-level notifications
    from your browser if you have the Docker Scout Dashboard open in a browser
    tab.

  To enable OS notifications, Docker Scout needs permissions to send
  notifications using the browser API.

From this page, you can also go to the settings for Team collaboration
integrations, such as the [Slack](/scout/integrations/team-collaboration/slack/)
integration.

You can also configure your notification settings in Docker Desktop by going
to **Settings** > **Notifications**.

---

## Manage vulnerability exceptions

# Manage vulnerability exceptions

Vulnerabilities found in container images sometimes need additional context.
Just because an image contains a vulnerable package, it doesn't mean that the
vulnerability is exploitable. **Exceptions** in Docker Scout lets you
acknowledge accepted risks or address false positives in image analysis.

By negating non-applicable vulnerabilities, you can make it easier for yourself
and downstream consumers of your images to understand the security implications
of a vulnerability in the context of an image.

In Docker Scout, exceptions are automatically factored into the results.
If an image contains an exception that flags a CVE as non-applicable,
then that CVE is excluded from analysis results.

## Create exceptions

To create an exception for an image, you can:

- Create an exception in the [GUI](/scout/how-tos/create-exceptions-gui/) of
  Docker Scout Dashboard or Docker Desktop.
- Create a [VEX](/scout/how-tos/create-exceptions-vex/) document and attach
  it to the image.

The recommended way to create exceptions is to use Docker Scout Dashboard or
Docker Desktop. The GUI provides a user-friendly interface for creating
exceptions. It also lets you create exceptions for multiple images, or your
entire organization, all at once.

## View exceptions

To view exceptions for images, you need to have the appropriate permissions.

- Exceptions created [using the GUI](/scout/how-tos/create-exceptions-gui/)
  are visible to members of your Docker organization. Unauthenticated users or
  users who aren't members of your organization cannot see these exceptions.
- Exceptions created [using VEX documents](/scout/how-tos/create-exceptions-vex/)
  are visible to anyone who can pull the image, since the VEX document is
  stored in the image manifest or on filesystem of the image.

### View exceptions in Docker Scout Dashboard or Docker Desktop

The [**Exceptions** tab](https://scout.docker.com/reports/vulnerabilities/exceptions)
of the Vulnerabilities page in Docker Scout Dashboard lists all exceptions for
for all images in your organization. From here, you can see more details about
each exception, the CVEs being suppressed, the images that exceptions apply to,
the type of exception and how it was created, and more.

For exceptions created using the [GUI](/scout/how-tos/create-exceptions-gui/),
selecting the action menu lets you edit or remove the exception.

To view all exceptions for a specific image tag:

**Docker Scout Dashboard**

1. Go to the [Images page](https://scout.docker.com/reports/images).
2. Select the tag that you want to inspect.
3. Open the **Exceptions** tab.

**Docker Desktop**

1. Open the **Images** view in Docker Desktop.
2. Open the **Hub** tab.
3. Select the tag you want to inspect.
4. Open the **Exceptions** tab.

### View exceptions in the CLI

Vulnerability exceptions are highlighted in the CLI when you run `docker scout
cves <image>`. If a CVE is suppressed by an exception, a `SUPPRESSED` label
appears next to the CVE ID. Details about the exception are also displayed.

![SUPPRESSED label in the CLI output](/scout/images/suppressed-cve-cli.png)

> [!IMPORTANT]
> In order to view exceptions in the CLI, you must configure the CLI to use
> the same Docker organization that you used to create the exceptions.
>
> To configure an organization for the CLI, run:
>
> ```console
> $ docker scout configure organization <organization>
> ```
>
> Replace `<organization>` with the name of your Docker organization.
>
> You can also set the organization on a per-command basis by using the
> `--org` flag:
>
> ```console
> $ docker scout cves --org <organization> <image>
> ```

To exclude suppressed CVEs from the output, use the `--ignore-suppressed` flag:

```console
$ docker scout cves --ignore-suppressed <image>
```

---

## Image details view

# Image details view

The image details view shows a breakdown of the Docker Scout analysis. You can
access the image view from the Docker Scout Dashboard, the Docker Desktop
**Images** view, and from the image tag page on Docker Hub. The image details
show a breakdown of the image hierarchy (base images), image layers, packages,
and vulnerabilities.

![The image details view in Docker Desktop](/scout/images/dd-image-view.png)

Docker Desktop first analyzes images locally, where it generates a software bill of materials (SBOM).
Docker Desktop, Docker Hub, and the Docker Scout Dashboard and CLI all use the [package URL (PURL) links](https://github.com/package-url/purl-spec)
in this SBOM to query for matching Common Vulnerabilities and Exposures (CVEs) in [Docker Scout's advisory database](/scout/deep-dive/advisory-db-sources/).

## Image hierarchy

The image you inspect may have one or more base images represented under
**Image hierarchy**. This means the author of the image used other images as
starting points when building the image. Often these base images are either
operating system images such as Debian, Ubuntu, and Alpine, or programming
language images such as PHP, Python, and Java.

Selecting each image in the chain lets you see which layers originate from each
base image. Selecting the **ALL** row selects all layers and base images.

One or more of the base images may have updates available, which may include
updated security patches that remove vulnerabilities from your image. Any base
images with available updates are noted to the right of **Image hierarchy**.

## Layers

A Docker image consists of layers. Image layers are listed from top to bottom,
with the earliest layer at the top and the most recent layer at the bottom.
Often, the layers at the top of the list originate from a base image, and the
layers towards the bottom added by the image author, often using
commands in a Dockerfile. Selecting a base image under **Image hierarchy** 
highlights with layers originate from a base image.

Selecting individual or multiple layers filters the packages and vulnerabilities
on the right-hand side to show what the selected layers added.

## Vulnerabilities

The **Vulnerabilities** tab displays a list of vulnerabilities and exploits detected in the image. The list is grouped by package, and sorted in order of severity.

You can find further information on the vulnerability or exploit, including if a fix is available, by expanding the list item.

## Remediation recommendations

When you inspect an image in Docker Desktop or Docker Hub,
Docker Scout can provide recommendations for improving the security of that image.

### Recommendations in Docker Desktop

To view security recommendations for an image in Docker Desktop:

1. Go to the **Images** view in Docker Desktop.
2. Select the image tag that you want to view recommendations for.
3. Near the top, select the **Recommended fixes** drop-down button.

The drop-down menu lets you choose whether you want to see recommendations for
the current image or any base images used to build it:

- [**Recommendations for this image**](#recommendations-for-current-image)
  provides recommendations for the current image that you're inspecting.
- [**Recommendations for base image**](#recommendations-for-base-image) provides
  recommendations for base images used to build the image.

If the image you're viewing has no associated base images, the drop-down menu only 
shows the option to view recommendations for the current image.

### Recommendations in Docker Hub

To view security recommendations for an image in Docker Hub:

1. Go to the repository page for an image where you have activated Docker Scout
   image analysis.
2. Open the **Tags** tab.
3. Select the tag that you want to view recommendations for.
4. Select the **View recommended base image fixes** button.

   This opens a window which gives you recommendations for you can improve the
   security of your image by using better base images. See
   [Recommendations for base image](#recommendations-for-base-image) for more
   details.

### Recommendations for current image

The recommendations for the current image view helps you determine whether the image
version that you're using is out of date. If the tag you're using is referencing an
old digest, the view shows a recommendation to update the tag by pulling the
latest version.

Select the **Pull new image** button to get the updated version. Check the
checkbox to remove the old version after pulling the latest.

### Recommendations for base image

The base image recommendations view contains two tabs for toggling between
different types of recommendations:

- **Refresh base image**
- **Change base image**

These base image recommendations are only actionable if you're the author of the
image you're inspecting. This is because changing the base image for an image
requires you to update the Dockerfile and re-build the image.

#### Refresh base image

This tab shows if the selected base image tag is the latest available version,
or if it's outdated.

If the base image tag used to build the current image isn't the latest, then the
delta between the two versions shows in this window. The delta information
includes:

- The tag name, and aliases, of the recommended (newer) version
- The age of the current base image version
- The age of the latest available version
- The number of CVEs affecting each version

At the bottom of the window, you also receive command snippets that you can 
run to re-build the image using the latest version.

#### Change base image

This tab shows different alternative tags that you can use, and outlines the
benefits and disadvantages of each tag version. Selecting the base image shows
recommended options for that tag.

For example, if the image you're inspecting is using an old version of `debian`
as a base image, it shows recommendations for newer and more secure versions
of `debian` to use. By providing more than one alternative to choose from, you
can see for yourself how the options compare with each other, and decide which
one to use.

![Base image recommendations](/scout/images/change-base-image.png)

Select a tag recommendation to see further details of the recommendation.
It shows the benefits and potential disadvantages of the tag, why it's a
recommended, and how to update your Dockerfile to use this version.

---

## Docker Scout metrics exporter

# Docker Scout metrics exporter

Docker Scout exposes a metrics HTTP endpoint that lets you scrape vulnerability
and policy data from Docker Scout, using Prometheus or Datadog. With this you
can create your own, self-hosted Docker Scout dashboards for visualizing supply
chain metrics.

## Metrics

The metrics endpoint exposes the following metrics:

| Metric                          | Description                                         | Labels                            | Type  |
| ------------------------------- | --------------------------------------------------- | --------------------------------- | ----- |
| `scout_stream_vulnerabilities`  | Vulnerabilities in a stream                         | `streamName`, `severity`          | Gauge |
| `scout_policy_compliant_images` | Compliant images for a policy in a stream           | `id`, `displayName`, `streamName` | Gauge |
| `scout_policy_evaluated_images` | Total images evaluated against a policy in a stream | `id`, `displayName`, `streamName` | Gauge |

> **Streams**
>
> In Docker Scout, the streams concept is a superset of [environments](/scout/integrations/environment/).
> Streams include all runtime environments that you've defined,
> as well as the special `latest-indexed` stream.
> The `latest-indexed` stream contains the most recently pushed (and analyzed) tag for each repository.
>
> Streams is mostly an internal concept in Docker Scout,
> with the exception of the data exposed through this metrics endpoint.
{ #stream }

## Creating an access token

To export metrics from your organization, first make sure your organization is enrolled in Docker Scout.
Then, create a Personal Access Token (PAT) - a secret token that allows the exporter to authenticate with the Docker Scout API.

The PAT does not require any specific permissions, but it must be created by a user who is an owner of the Docker organization.
To create a PAT, follow the steps in [Create an access token](/security/access-tokens/).

Once you have created the PAT, store it in a secure location.
You will need to provide this token to the exporter when scraping metrics.

## Prometheus

This section describes how to scrape the metrics endpoint using Prometheus.

### Add a job for your organization

In the Prometheus configuration file, add a new job for your organization.
The job should include the following configuration;
replace `ORG` with your organization name:

```yaml
scrape_configs:
  - job_name: <ORG>
    metrics_path: /v1/exporter/org/<ORG>/metrics
    scheme: https
    static_configs:
      - targets:
          - api.scout.docker.com
```

The address in the `targets` field is set to the domain name of the Docker Scout API, `api.scout.docker.com`.
Make sure that there's no firewall rule in place preventing the server from communicating with this endpoint.

### Add bearer token authentication

To scrape metrics from the Docker Scout Exporter endpoint using Prometheus, you need to configure Prometheus to use the PAT as a bearer token.
The exporter requires the PAT to be passed in the `Authorization` header of the request.

Update the Prometheus configuration file to include the `authorization` configuration block.
This block defines the PAT as a bearer token stored in a file:

```yaml
scrape_configs:
  - job_name: $ORG
    authorization:
      type: Bearer
      credentials_file: /etc/prometheus/token
```

The content of the file should be the PAT in plain text:

```console
dckr_pat_...
```

If you are running Prometheus in a Docker container or Kubernetes pod, mount the file into the container using a volume or secret.

Finally, restart Prometheus to apply the changes.

### Prometheus sample project

If you don't have a Prometheus server set up, you can run a [sample project](https://github.com/dockersamples/scout-metrics-exporter) using Docker Compose.
The sample includes a Prometheus server that scrapes metrics for a Docker organization enrolled in Docker Scout,
alongside Grafana with a pre-configured dashboard to visualize the vulnerability and policy metrics.

1. Clone the starter template for bootstrapping a set of Compose services
   for scraping and visualizing the Docker Scout metrics endpoint:

   ```console
   $ git clone git@github.com:dockersamples/scout-metrics-exporter.git
   $ cd scout-metrics-exporter/prometheus
   ```

2. [Create a Docker access token](/security/access-tokens/)
   and store it in a plain text file at `/prometheus/prometheus/token` under the template directory.

   ```plaintext {title=token}
   $ echo $DOCKER_PAT > ./prometheus/token
   ```

3. In the Prometheus configuration file at `/prometheus/prometheus/prometheus.yml`,
   replace `ORG` in the `metrics_path` property on line 6 with the namespace of your Docker organization.

   ```yaml {title="prometheus/prometheus.yml",hl_lines="6",linenos=true}
   global:
     scrape_interval: 60s
     scrape_timeout: 40s
   scrape_configs:
     - job_name: Docker Scout policy
       metrics_path: /v1/exporter/org/<ORG>/metrics
       scheme: https
       static_configs:
         - targets:
             - api.scout.docker.com
       authorization:
         type: Bearer
         credentials_file: /etc/prometheus/token
   ```

4. Start the compose services.

   ```console
   docker compose up -d
   ```

   This command starts two services: the Prometheus server and Grafana.
   Prometheus scrapes metrics from the Docker Scout endpoint,
   and Grafana visualizes the metrics using a pre-configured dashboard.

To stop the demo and clean up any resources created, run:

```console
docker compose down -v
```

### Access to Prometheus

After starting the services, you can access the Prometheus expression browser by visiting <http://localhost:9090>.
The Prometheus server runs in a Docker container and is accessible on port 9090.

After a few seconds, you should see the metrics endpoint as a target in the
Prometheus UI at <http://localhost:9090/targets>.

![Docker Scout metrics exporter Prometheus target](/scout/images/scout-metrics-prom-target.png "Docker Scout metrics exporter Prometheus target")

### Viewing the metrics in Grafana

To view the Grafana dashboards, go to <http://localhost:3000/dashboards>,
and sign in using the credentials defined in the Docker Compose file (username: `admin`, password: `grafana`).

![Vulnerability dashboard in Grafana](/scout/images/scout-metrics-grafana-vulns.png "Vulnerability dashboard in Grafana")

![Policy dashboard in Grafana](/scout/images/scout-metrics-grafana-policy.png "Policy dashboard in Grafana")

The dashboards are pre-configured to visualize the vulnerability and policy metrics scraped by Prometheus.

## Datadog

This section describes how to scrape the metrics endpoint using Datadog.
Datadog pulls data for monitoring by running a customizable
[agent](https://docs.datadoghq.com/agent/?tab=Linux) that scrapes available
endpoints for any exposed metrics. The OpenMetrics and Prometheus checks are
included in the agent, so you don鈥檛 need to install anything else on your
containers or hosts.

This guide assumes you have a Datadog account and a Datadog API Key. Refer to
the [Datadog documentation](https://docs.datadoghq.com/agent) to get started.

### Configure the Datadog agent

To start collecting the metrics, you will need to edit the agent鈥檚
configuration file for the OpenMetrics check. If you're running the agent as a
container, such file must be mounted at
`/etc/datadog-agent/conf.d/openmetrics.d/conf.yaml`.

The following example shows a Datadog configuration that:

- Specifies the OpenMetrics endpoint targeting the `dockerscoutpolicy` Docker organization
- A `namespace` that all collected metrics will be prefixed with
- The [`metrics`](#metrics) you want the agent to scrape (`scout_*`)
- An `auth_token` section for the Datadog agent to authenticate to the Metrics
  endpoint, using a Docker PAT as a Bearer token.

```yaml
instances:
  - openmetrics_endpoint: "https://api.scout.docker.com/v1/exporter/org/dockerscoutpolicy/metrics"
    namespace: "scout-metrics-exporter"
    metrics:
      - scout_*
    auth_token:
      reader:
        type: file
        path: /var/run/secrets/scout-metrics-exporter/token
      writer:
        type: header
        name: Authorization
        value: Bearer <TOKEN>
```

> [!IMPORTANT]
>
> Do not replace the `<TOKEN>` placeholder in the previous configuration
> example. It must stay as it is. Only make sure the Docker PAT is correctly
> mounted into the Datadog agent in the specified filesystem path. Save the
> file as `conf.yaml` and restart the agent.

When creating a Datadog agent configuration of your own, make sure to edit the
`openmetrics_endpoint` property to target your organization, by replacing
`dockerscoutpolicy` with the namespace of your Docker organization.

### Datadog sample project

If you don't have a Datadog server set up, you can run a [sample project](https://github.com/dockersamples/scout-metrics-exporter)
using Docker Compose. The sample includes a Datadog agent, running as a
container, that scrapes metrics for a Docker organization enrolled in Docker
Scout. This sample project assumes that you have a Datadog account, an API key,
and a Datadog site.

1. Clone the starter template for bootstrapping a Datadog Compose service for
   scraping the Docker Scout metrics endpoint:

   ```console
   $ git clone git@github.com:dockersamples/scout-metrics-exporter.git
   $ cd scout-metrics-exporter/datadog
   ```

2. [Create a Docker access token](/security/access-tokens/)
   and store it in a plain text file at `/datadog/token` under the template directory.

   ```plaintext {title=token}
   $ echo $DOCKER_PAT > ./token
   ```

3. In the `/datadog/compose.yaml` file, update the `DD_API_KEY` and `DD_SITE` environment variables
   with the values for your Datadog deployment.

   ```yaml {hl_lines="5-6"}
     datadog-agent:
       container_name: datadog-agent
       image: gcr.io/datadoghq/agent:7
       environment:
         - DD_API_KEY=${DD_API_KEY} # e.g. 1b6b3a42...
         - DD_SITE=${DD_SITE} # e.g. datadoghq.com
         - DD_DOGSTATSD_NON_LOCAL_TRAFFIC=true
       volumes:
         - /var/run/docker.sock:/var/run/docker.sock:ro
         - ./conf.yaml:/etc/datadog-agent/conf.d/openmetrics.d/conf.yaml:ro
         - ./token:/var/run/secrets/scout-metrics-exporter/token:ro
   ```

   The `volumes` section mounts the Docker socket from the host to the
   container. This is required to obtain an accurate hostname when running as a
   container ([more details here](https://docs.datadoghq.com/agent/troubleshooting/hostname_containers/)).

   It also mounts the agent's config file and the Docker access token.

4. Edit the `/datadog/config.yaml` file by replacing the placeholder `<ORG>` in
   the `openmetrics_endpoint` property with the namespace of the Docker
   organization that you want to collect metrics for.

   ```yaml {hl_lines=2}
   instances:
     - openmetrics_endpoint: "https://api.scout.docker.com/v1/exporter/org/<<ORG>>/metrics"
       namespace: "scout-metrics-exporter"
   # ...
   ```

5. Start the Compose services.

   ```console
   docker compose up -d
   ```

If configured properly, you should see the OpenMetrics check under Running
Checks when you run the agent鈥檚 status command whose output should look similar
to:

```text
openmetrics (4.2.0)
-------------------
  Instance ID: openmetrics:scout-prometheus-exporter:6393910f4d92f7c2 [OK]
  Configuration Source: file:/etc/datadog-agent/conf.d/openmetrics.d/conf.yaml
  Total Runs: 1
  Metric Samples: Last Run: 236, Total: 236
  Events: Last Run: 0, Total: 0
  Service Checks: Last Run: 1, Total: 1
  Average Execution Time : 2.537s
  Last Execution Date : 2024-05-08 10:41:07 UTC (1715164867000)
  Last Successful Execution Date : 2024-05-08 10:41:07 UTC (1715164867000)
```

For a comprehensive list of options, take a look at this [example config file](https://github.com/DataDog/integrations-core/blob/master/openmetrics/datadog_checks/openmetrics/data/conf.yaml.example) for the generic OpenMetrics check.

### Visualizing your data

Once the agent is configured to grab Prometheus metrics, you can use them to build comprehensive Datadog graphs, dashboards, and alerts.

Go into your [Metric summary page](https://app.datadoghq.com/metric/summary?filter=scout_prometheus_exporter)
to see the metrics collected from this example. This configuration will collect
all exposed metrics starting with `scout_` under the namespace
`scout_metrics_exporter`.

![datadog_metrics_summary](/scout/images/datadog_metrics_summary.png)

The following screenshots show examples of a Datadog dashboard containing
graphs about vulnerability and policy compliance for a specific [stream](#stream).

![datadog_dashboard_1](/scout/images/datadog_dashboard_1.png)
![datadog_dashboard_2](/scout/images/datadog_dashboard_2.png)

> The reason why the lines in the graphs look flat is due to the own nature of
> vulnerabilities (they don't change too often) and the short time interval
> selected in the date picker.

## Scrape interval

By default, Prometheus and Datadog scrape metrics at a 15 second interval.
Because of the own nature of vulnerability data, the metrics exposed through this API are unlikely to change at a high frequency.
For this reason, the metrics endpoint has a 60-minute cache by default,
which means a scraping interval of 60 minutes or higher is recommended.
If you set the scrape interval to less than 60 minutes, you will see the same data in the metrics for multiple scrapes during that time window.

To change the scrape interval:

- Prometheus: set the `scrape_interval` field in the Prometheus configuration
  file at the global or job level.
- Datadog: set the `min_collection_interval` property in the Datadog agent
  configuration file, see [Datadog documentation](https://docs.datadoghq.com/developers/custom_checks/write_agent_check/#updating-the-collection-interval).

## Revoke an access token

If you suspect that your PAT has been compromised or is no longer needed, you can revoke it at any time.
To revoke a PAT, follow the steps in the [Create and manage access tokens](/security/access-tokens/).

Revoking a PAT immediately invalidates the token, and prevents Prometheus from scraping metrics using that token.
You will need to create a new PAT and update the Prometheus configuration to use the new token.

---

## Use Scout with different artifact types

# Use Scout with different artifact types

Some of the Docker Scout CLI commands support prefixes for specifying
the location or type of artifact that you would like to analyze.

By default, image analysis with the `docker scout cves` command
targets images in the local image store of the Docker Engine.
The following command always uses a local image if it exists:

```console
$ docker scout cves <image>
```

If the image doesn't exist locally, Docker pulls the image before running the analysis.
Analyzing the same image again would use the same local version by default,
even if the tag has since changed in the registry.

By adding a `registry://` prefix to the image reference,
you can force Docker Scout to analyze the registry version of the image:

```console
$ docker scout cves registry://<image>
```

## Supported prefixes

The supported prefixes are:

| Prefix               | Description                                                          |
| -------------------- | -------------------------------------------------------------------- |
| `image://` (default) | Use a local image, or fall back to a registry lookup                 |
| `local://`           | Use an image from the local image store (don't do a registry lookup) |
| `registry://`        | Use an image from a registry (don't use a local image)               |
| `oci-dir://`         | Use an OCI layout directory                                          |
| `archive://`         | Use a tarball archive, as created by `docker save`                   |
| `fs://`              | Use a local directory or file                                        |

You can use prefixes with the following commands:

- `docker scout compare`
- `docker scout cves`
- `docker scout quickview`
- `docker scout recommendations`
- `docker scout sbom`

## Examples

This section contains a few examples showing how you can use prefixes
to specify artifacts for `docker scout` commands.

### Analyze a local project

The `fs://` prefix lets you analyze local source code directly,
without having to build it into a container image.
The following `docker scout quickview` command gives you an
at-a-glance vulnerability summary of the source code in the current working directory:

```console
$ docker scout quickview fs://.
```

To view the details of vulnerabilities found in your local source code, you can
use the `docker scout cves --details fs://.` command. Combine it with
other flags to narrow down the results to the packages and vulnerabilities that
you're interested in.

```console
$ docker scout cves --details --only-severity high fs://.
    鉁� File system read
    鉁� Indexed 323 packages
    鉁� Detected 1 vulnerable package with 1 vulnerability

鈥�## Overview

                    鈹�        Analyzed path
鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹尖攢鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€
  Path              鈹�  /Users/david/demo/scoutfs
    vulnerabilities 鈹�    0C     1H     0M     0L

鈥�## Packages and Vulnerabilities

   0C     1H     0M     0L  fastify 3.29.0
pkg:npm/fastify@3.29.0

    鉁� HIGH CVE-2022-39288 [OWASP Top Ten 2017 Category A9 - Using Components with Known Vulnerabilities]
      https://scout.docker.com/v/CVE-2022-39288

      fastify is a fast and low overhead web framework, for Node.js. Affected versions of
      fastify are subject to a denial of service via malicious use of the Content-Type
      header. An attacker can send an invalid Content-Type header that can cause the
      application to crash. This issue has been addressed in commit  fbb07e8d  and will be
      included in release version 4.8.1. Users are advised to upgrade. Users unable to
      upgrade may manually filter out http content with malicious Content-Type headers.

      Affected range : <4.8.1
      Fixed version  : 4.8.1
      CVSS Score     : 7.5
      CVSS Vector    : CVSS:3.1/AV:N/AC:L/PR:N/UI:N/S:U/C:N/I:N/A:H

1 vulnerability found in 1 package
  LOW       0
  MEDIUM    0
  HIGH      1
  CRITICAL  0
```

### Compare a local project to an image

With `docker scout compare`, you can compare the analysis of source code on
your local filesystem with the analysis of a container image.
The following example compares local source code (`fs://.`)
with a registry image `registry://docker/scout-cli:latest`.
In this case, both the baseline and target for the comparison use prefixes.

```console
$ docker scout compare fs://. --to registry://docker/scout-cli:latest --ignore-unchanged
WARN 'docker scout compare' is experimental and its behaviour might change in the future
    鉁� File system read
    鉁� Indexed 268 packages
    鉁� SBOM of image already cached, 234 packages indexed

  ## Overview

                           鈹�              Analyzed File System              鈹�              Comparison Image
  鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹尖攢鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹尖攢鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€
    Path / Image reference 鈹�  /Users/david/src/docker/scout-cli-plugin      鈹�  docker/scout-cli:latest
                           鈹�                                                鈹�  bb0b01303584
      platform             鈹�                                                鈹� linux/arm64
      provenance           鈹� https://github.com/dvdksn/scout-cli-plugin.git 鈹� https://github.com/docker/scout-cli-plugin
                           鈹�  6ea3f7369dbdfec101ac7c0fa9d78ef05ffa6315      鈹�  67cb4ef78bd69545af0e223ba5fb577b27094505
      vulnerabilities      鈹�    0C     0H     1M     1L                     鈹�    0C     0H     1M     1L
                           鈹�                                                鈹�
      size                 鈹� 7.4 MB (-14 MB)                                鈹� 21 MB
      packages             鈹� 268 (+34)                                      鈹� 234
                           鈹�                                                鈹�

  ## Packages and Vulnerabilities

    +   55 packages added
    -   21 packages removed
       213 packages unchanged
```

The previous example is truncated for brevity.

### View the SBOM of an image tarball

The following example shows how you can use the `archive://` prefix
to get the SBOM of an image tarball, created with `docker save`.
The image in this case is `docker/scout-cli:latest`,
and the SBOM is exported to file `sbom.spdx.json` in SPDX format.

```console
$ docker pull docker/scout-cli:latest
latest: Pulling from docker/scout-cli
257973a141f5: Download complete 
1f2083724dd1: Download complete 
5c8125a73507: Download complete 
Digest: sha256:13318bb059b0f8b0b87b35ac7050782462b5d0ac3f96f9f23d165d8ed68d0894
$ docker save docker/scout-cli:latest -o scout-cli.tar
$ docker scout sbom --format spdx -o sbom.spdx.json archive://scout-cli.tar
```

## Learn more

Read about the commands and supported flags in the CLI reference documentation:

- [`docker scout quickview`](/reference/cli/docker/scout/quickview/)
- [`docker scout cves`](/reference/cli/docker/scout/cves/)
- [`docker scout compare`](/reference/cli/docker/scout/compare/)

---

## Configure Docker Scout with environment variables

# Configure Docker Scout with environment variables

The following environment variables are available to configure the Docker Scout
CLI commands, and the corresponding `docker/scout-cli` container image:

| Name                                    | Format  | Description                                                                                 |
| :-------------------------------------- | ------- | :------------------------------------------------------------------------------------------ |
| DOCKER_SCOUT_CACHE_FORMAT               | String  | Format of the local image cache; can be `oci` or `tar` (default: `oci`)                     |
| DOCKER_SCOUT_CACHE_DIR                  | String  | Directory where the local SBOM cache is stored (default: `$HOME/.docker/scout`)             |
| DOCKER_SCOUT_NO_CACHE                   | Boolean | When set to `true`, disables the use of local SBOM cache                                    |
| DOCKER_SCOUT_OFFLINE                    | Boolean | Use [offline mode](#offline-mode) when indexing SBOM                                        |
| DOCKER_SCOUT_REGISTRY_TOKEN             | String  | Token for authenticating to a registry when pulling images                                  |
| DOCKER_SCOUT_REGISTRY_USER              | String  | Username for authenticating to a registry when pulling images                               |
| DOCKER_SCOUT_REGISTRY_PASSWORD          | String  | Password or personal access token for authenticating to a registry when pulling images      |
| DOCKER_SCOUT_HUB_USER                   | String  | Docker Hub username for authenticating to the Docker Scout backend                          |
| DOCKER_SCOUT_HUB_PASSWORD               | String  | Docker Hub password or personal access token for authenticating to the Docker Scout backend |
| DOCKER_SCOUT_NEW_VERSION_WARN           | Boolean | Warn about new versions of the Docker Scout CLI                                             |
| DOCKER_SCOUT_EXPERIMENTAL_WARN          | Boolean | Warn about experimental features                                                            |
| DOCKER_SCOUT_EXPERIMENTAL_POLICY_OUTPUT | Boolean | Disable experimental output for policy evaluation                                           |

## Offline mode

Under normal operation, Docker Scout cross-references external systems, such as
npm, NuGet, or proxy.golang.org, to retrieve additional information about
packages found in your image.

When `DOCKER_SCOUT_OFFLINE` is set to `true`, Docker Scout image analysis runs
in offline mode. Offline mode means Docker Scout doesn't make outbound requests
to external systems.

To use offline mode:

```console
$ export DOCKER_SCOUT_OFFLINE=true
```

---

## Create an exception using the GUI

# Create an exception using the GUI

The Docker Scout Dashboard and Docker Desktop provide a user-friendly interface
for creating [exceptions](/scout/explore/exceptions/) for
vulnerabilities found in container images. Exceptions let you acknowledge
accepted risks or address false positives in image analysis.

## Prerequisites

To create an in the Docker Scout Dashboard or Docker Desktop, you need a Docker
account with **Editor** or **Owner** permissions for the Docker organization
that owns the image.

## Steps

To create an exception for a vulnerability in an image using the Docker Scout
Dashboard or Docker Desktop:

**Docker Scout Dashboard**

1. Go to the [Images page](https://scout.docker.com/reports/images).
2. Select the image tag that contains the vulnerability you want to create an
   exception for.
3. Open the **Image layers** tab.
4. Select the layer that contains the vulnerability you want to create an
   exception for.
5. In the **Vulnerabilities** tab, find the vulnerability you want to create an
   exception for. Vulnerabilities are grouped by package. Find the package that
   contains the vulnerability you want to create an exception for, and then
   expand the package.
6. Select the **Create exception** button next to the vulnerability.

Selecting the **Create exception** button opens the **Create exception** side panel.
In this panel, you can provide the details of the exception:

- **Exception type**: The type of exception. The only supported types are:

  - **Accepted risk**: The vulnerability is not addressed due to its minimal
    security risk, high remediation costs, dependence on an upstream fix, or
    similar.
  - **False positive**: The vulnerability presents no security risk in your
    specific use case, configuration, or because of measures in place that
    block exploitation

    If you select **False positive**, you must provide a justification for why
    the vulnerability is a false positive:

- **Additional details**: Any additional information that you want to
  provide about the exception.

- **Scope**: The scope of the exception. The scope can be:

  - **Image**: The exception applies to the selected image.
  - **All images in repository**: The exception applies to all images in the
    repository.
  - **Specific repository**: The exception applies to all images in the
    specified repositories.
  - **All images in my organization**: The exception applies to all images in
    your organization.

- **Package scope**: The scope of the exception. The package scope can be:

  - **Selected package**: The exception applies to the selected package.
  - **Any packages**: The exception applies to all packages vulnerable to this
    CVE.

When you've filled in the details, select the **Create** button to create the
exception.

The exception is now created and factored into the analysis results for the
images that you selected. The exception is also listed on the **Exceptions**
tab of the [Vulnerabilities page](https://scout.docker.com/reports/vulnerabilities/exceptions)
in the Docker Scout Dashboard.

**Docker Desktop**

1. Open the **Images** view in Docker Desktop.
2. Open the **Hub** tab.
3. Select the image tag that contains the vulnerability you want to create an
   exception for.
4. Select the layer that contains the vulnerability you want to create an
   exception for.
5. In the **Vulnerabilities** tab, find the vulnerability you want to create an
   exception for.
6. Select the **Create exception** button next to the vulnerability.

Selecting the **Create exception** button opens the **Create exception** side panel.
In this panel, you can provide the details of the exception:

- **Exception type**: The type of exception. The only supported types are:

  - **Accepted risk**: The vulnerability is not addressed due to its minimal
    security risk, high remediation costs, dependence on an upstream fix, or
    similar.
  - **False positive**: The vulnerability presents no security risk in your
    specific use case, configuration, or because of measures in place that
    block exploitation

    If you select **False positive**, you must provide a justification for why
    the vulnerability is a false positive:

- **Additional details**: Any additional information that you want to
  provide about the exception.

- **Scope**: The scope of the exception. The scope can be:

  - **Image**: The exception applies to the selected image.
  - **All images in repository**: The exception applies to all images in the
    repository.
  - **Specific repository**: The exception applies to all images in the
    specified repositories.
  - **All images in my organization**: The exception applies to all images in
    your organization.

- **Package scope**: The scope of the exception. The package scope can be:

  - **Selected package**: The exception applies to the selected package.
  - **Any packages**: The exception applies to all packages vulnerable to this
    CVE.

When you've filled in the details, select the **Create** button to create the
exception.

The exception is now created and factored into the analysis results for the
images that you selected. The exception is also listed on the **Exceptions**
tab of the [Vulnerabilities page](https://scout.docker.com/reports/vulnerabilities/exceptions)
in the Docker Scout Dashboard.

---

## Create an exception using the VEX

# Create an exception using the VEX

Vulnerability Exploitability eXchange (VEX) is a standard format for
documenting vulnerabilities in the context of a software package or product.
Docker Scout supports VEX documents to create
[exceptions](/scout/explore/exceptions/) for vulnerabilities in images.

> [!NOTE]
> You can also create exceptions using the Docker Scout Dashboard or Docker
> Desktop. The GUI provides a user-friendly interface for creating exceptions,
> and it's easy to manage exceptions for multiple images. It also lets you
> create exceptions for multiple images, or your entire organization, all at
> once. For more information, see [Create an exception using the GUI](/scout/how-tos/create-exceptions-gui/).

## Prerequisites

To create exceptions using OpenVEX documents, you need:

- The latest version of Docker Desktop or the Docker Scout CLI plugin
- The [`vexctl`](https://github.com/openvex/vexctl) command line tool.

Additional requirements depend on how you attach the VEX document:

- The [containerd image store](/desktop/features/containerd/)
  must be enabled to attach the document as an attestation.
- Write permissions to the registry repository where the image is stored
  are required to attach the document as an attestation.

## Introduction to VEX

The VEX standard is defined by a working group by the United States
Cybersecurity and Infrastructure Security Agency (CISA). At the core of VEX are
exploitability assessments. These assessments describe the status of a given
CVE for a product. The possible vulnerability statuses in VEX are:

- Not affected: No remediation is required regarding this vulnerability.
- Affected: Actions are recommended to remediate or address this vulnerability.
- Fixed: These product versions contain a fix for the vulnerability.
- Under investigation: It is not yet known whether these product versions are affected by the vulnerability. An update will be provided in a later release.

There are multiple implementations and formats of VEX. Docker Scout supports
the [OpenVex](https://github.com/openvex/spec) implementation. Regardless of
the specific implementation, the core idea is the same: to provide a framework
for describing the impact of vulnerabilities. Key components of VEX regardless
of implementation includes:

VEX document
: A type of security advisory for storing VEX statements.
  The format of the document depends on the specific implementation.

VEX statement
: Describes the status of a vulnerability in a product,
  whether it's exploitable, and whether there are ways to remediate the issue.

Justification and impact
: Depending on the vulnerability status, statements include a justification
  or impact statement describing why a product is or isn't affected.

Action statements
: Describe how to remediate or mitigate the vulnerability.

## `vexctl` example

The following example command creates a VEX document stating that:

- The software product described by this VEX document is the Docker image
  `example/app:v1`
- The image contains the npm package `express@4.17.1`
- The npm package is affected by a known vulnerability: `CVE-2022-24999`
- The image is unaffected by the CVE, because the vulnerable code is never
  executed in containers that run this image

```console
$ vexctl create \
  --author="author@example.com" \
  --product="pkg:docker/example/app@v1" \
  --subcomponents="pkg:npm/express@4.17.1" \
  --vuln="CVE-2022-24999" \
  --status="not_affected" \
  --justification="vulnerable_code_not_in_execute_path" \
  --file="CVE-2022-24999.vex.json"
```

Here's a description of the options in this example:

`--author`
: The email of the author of the VEX document.

`--product`
: Package URL (PURL) of the Docker image. A PURL is an identifier
  for the image in a standardized format, defined in the PURL
  [specification](https://github.com/package-url/purl-spec/blob/master/PURL-TYPES.rst#docker).

  Docker image PURL strings begin with a `pkg:docker` type prefix, followed by
  the image repository and version (the image tag or SHA256 digest). Unlike
  image tags, where the version is specified like `example/app:v1`, in PURL the
  image repository and version are separated by an `@`.

`--subcomponents`
: PURL of the vulnerable package in the image. In this example, the
  vulnerability exists in an npm package, so the `--subcomponents` PURL is the
  identifier for the npm package name and version (`pkg:npm/express@4.17.1`).
  
  If the same vulnerability exists in multiple packages, `vexctl` lets you
  specify the `--subcomponents` flag multiple times for a single `create`
  command.

  You can also omit `--subcomponents`, in which case the VEX statement applies
  to the entire image.

`--vuln`
: ID of the CVE that the VEX statement addresses.

`--status`
: This is the status label of the vulnerability. This describes the
  relationship between the software (`--product`) and the CVE (`--vuln`).
  The possible values for the status label in OpenVEX are:

  - `not_affected`
  - `affected`
  - `fixed`
  - `under_investigation`

  In this example, the VEX statement asserts that the Docker image is
  `not_affected` by the vulnerability. The `not_affected` status is the only
  status that results in CVE suppression, where the CVE is filtered out of the
  analysis results. The other statuses are useful for documentation purposes,
  but they do not work for creating exceptions. For more information about all
  the possible status labels, see [Status Labels](https://github.com/openvex/spec/blob/main/OPENVEX-SPEC.md#status-labels)
  in the OpenVEX specification.

`--justification`
: Justifies the `not_affected` status label, informing why the product is not
  affected by the vulnerability. In this case, the justification given is
  `vulnerable_code_not_in_execute_path`, signalling that the vulnerability
  can't be executed as used by the product.

  In OpenVEX, status justifications can have one of the five possible values:

  - `component_not_present`
  - `vulnerable_code_not_present`
  - `vulnerable_code_not_in_execute_path`
  - `vulnerable_code_cannot_be_controlled_by_adversary`
  - `inline_mitigations_already_exist`

  For more information about these values and their definitions, see
  [Status Justifications](https://github.com/openvex/spec/blob/main/OPENVEX-SPEC.md#status-justifications)
  in the OpenVEX specification.

`--file`
: Filename of the VEX document output

## Example JSON document

Here's the OpenVEX JSON generated by this command:

```json
{
  "@context": "https://openvex.dev/ns/v0.2.0",
  "@id": "https://openvex.dev/docs/public/vex-749f79b50f5f2f0f07747c2de9f1239b37c2bda663579f87a35e5f0fdfc13de5",
  "author": "author@example.com",
  "timestamp": "2024-05-27T13:20:22.395824+02:00",
  "version": 1,
  "statements": [
    {
      "vulnerability": {
        "name": "CVE-2022-24999"
      },
      "timestamp": "2024-05-27T13:20:22.395829+02:00",
      "products": [
        {
          "@id": "pkg:docker/example/app@v1",
          "subcomponents": [
            {
              "@id": "pkg:npm/express@4.17.1"
            }
          ]
        }
      ],
      "status": "not_affected",
      "justification": "vulnerable_code_not_in_execute_path"
    }
  ]
}
```

Understanding how VEX documents are supposed to be structured can be a bit of a
mouthful. The [OpenVEX specification](https://github.com/openvex/spec)
describes the format and all the possible properties of documents and
statements. For the full details, refer to the specification to learn more
about the available fields and how to create a well-formed OpenVEX document.

To learn more about the available flags and syntax of the `vexctl` CLI tool and
how to install it, refer to the [`vexctl` GitHub repository](https://github.com/openvex/vexctl).

## Verifying VEX documents

To test whether the VEX documents you create are well-formed and produce the
expected results, use the `docker scout cves` command with the `--vex-location`
flag to apply a VEX document to a local image analysis using the CLI.

The following command invokes a local image analysis that incorporates all VEX
documents in the specified location, using the `--vex-location` flag. In this
example, the CLI is instructed to look for VEX documents in the current working
directory.

```console
$ docker scout cves <IMAGE> --vex-location .
```

The output of the `docker scout cves` command displays the results with any VEX
statements found in under the `--vex-location` location factored into the
results. For example, CVEs assigned a status of `not_affected` are filtered out
from the results. If the output doesn't seem to take the VEX statements into
account, that's an indication that the VEX documents might be invalid in some
way.

Things to look out for include:

- The PURL of a Docker image must begin with `pkg:docker/` followed by the image name.
- In a Docker image PURL, the image name and version is separated by `@`.
  An image named `example/myapp:1.0` has the following PURL: `pkg:docker/example/myapp@1.0`.
- Remember to specify an `author` (it's a mandatory field in OpenVEX)
- The [OpenVEX specification](https://github.com/openvex/spec) describes how
  and when to use `justification`, `impact_statement`, and other fields in the
  VEX documents. Specifying these in an incorrect way results in an invalid
  document. Make sure your VEX documents comply with the OpenVEX specification.

## Attach VEX documents to images

When you've created a VEX document,
you can attach it to your image in the following ways:

- Attach the document as an [attestation](#attestation)
- Embed the document in the [image filesystem](#image-filesystem)

You can't remove a VEX document from an image once it's been added. For
documents attached as attestations, you can create a new VEX document and
attach it to the image again. Doing so will overwrite the previous VEX document
(but it won't remove the attestation). For images where the VEX document has
been embedded in the image's filesystem, you need to rebuild the image to
change the VEX document.

### Attestation

To attach VEX documents as an attestation, you can use the `docker scout
attestation add` CLI command. Using attestations is the recommended option for
attaching exceptions to images when using VEX. This method requires the
[containerd image store](/desktop/features/containerd/) and write
access to the registry repository where the image is stored.

You can attach attestations to images that have already been pushed to a
registry. You don't need to build or push the image again. Additionally, having
the exceptions attached to the image as attestations means consumers can
inspect the exceptions for an image, directly from the registry.

To attach an attestation to an image:

1. Build the image and push it to a registry.

   ```console
   $ docker build --provenance=true --sbom=true --tag <IMAGE> --push .
   ```

2. Attach the exception to the image as an attestation.

   ```console
   $ docker scout attestation add \
     --file <cve-id>.vex.json \
     --predicate-type https://openvex.dev/ns/v0.2.0 \
     <IMAGE>
   ```

   The options for this command are:

   - `--file`: the location and filename of the VEX document
   - `--predicate-type`: the in-toto `predicateType` for OpenVEX

### Image filesystem

Embedding VEX documents directly on the image filesystem is a good option if
you know the exceptions ahead of time, before you build the image. And it's
relatively easy; just `COPY` the VEX document to the image in your Dockerfile.
Unlike attestations, this method doesn't require the containerd image store or
write access to a registry before the image is pushed.

The downside with this approach is that you can't change or update the
exception later. Image layers are immutable, so anything you put in the image's
filesystem is there forever. Attaching the document as an
[attestation](#attestation) provides better flexibility.

> [!NOTE]
> VEX documents embedded in the image filesystem are not considered for images
> that have attestations. If your image has **any** attestations, Docker Scout
> will only look for exceptions in the attestations, and not in the image
> filesystem.
>
> If you want to use the VEX document embedded in the image filesystem, you
> must remove the attestation from the image. Note that provenance attestations
> may be added automatically for images. To ensure that no attestations are
> added to the image, you can explicitly disable both SBOM and provenance
> attestations using the `--provenance=false` and `--sbom=false` flags when
> building the image.

To embed a VEX document on the image filesystem, `COPY` the file into the image
as part of the image build. The following example shows how to copy all VEX
documents under `.vex/` in the build context, to `/var/lib/db` in the image.

```dockerfile
# syntax=docker/dockerfile:1

FROM alpine
COPY .vex/* /var/lib/db/
```

The filename of the VEX document must match the `*.vex.json` glob pattern.
It doesn't matter where on the image's filesystem you store the file.

Note that the copied files must be part of the filesystem of the final image,
For multi-stage builds, the documents must persist in the final stage.

---

## Docker Scout SBOMs

# Docker Scout SBOMs

[Image analysis](/scout/explore/analysis/) uses image SBOMs to understand what packages and versions an image contains.
Docker Scout uses SBOM attestations if available on the image (recommended).
If no SBOM attestation is available, Docker Scout creates one by indexing the image contents.

## View from CLI

To view the contents of the SBOM that Docker Scout generates, you can use the
`docker scout sbom` command.

```console
$ docker scout sbom [IMAGE]
```

By default, this prints the SBOM in a JSON format to stdout.
The default JSON format produced by `docker scout sbom` isn't SPDX-JSON.
To output SPDX, use the `--format spdx` flag:

```console
$ docker scout sbom --format spdx [IMAGE]
```

To generate a human-readable list, use the `--format list` flag:

```console
$ docker scout sbom --format list alpine

           Name             Version    Type
鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€
  alpine-baselayout       3.4.3-r1     apk
  alpine-baselayout-data  3.4.3-r1     apk
  alpine-keys             2.4-r1       apk
  apk-tools               2.14.0-r2    apk
  busybox                 1.36.1-r2    apk
  busybox-binsh           1.36.1-r2    apk
  ca-certificates         20230506-r0  apk
  ca-certificates-bundle  20230506-r0  apk
  libc-dev                0.7.2-r5     apk
  libc-utils              0.7.2-r5     apk
  libcrypto3              3.1.2-r0     apk
  libssl3                 3.1.2-r0     apk
  musl                    1.2.4-r1     apk
  musl-utils              1.2.4-r1     apk
  openssl                 3.1.2-r0     apk
  pax-utils               1.3.7-r1     apk
  scanelf                 1.3.7-r1     apk
  ssl_client              1.36.1-r2    apk
  zlib                    1.2.13-r1    apk
```

For more information about the `docker scout sbom` command, refer to the [CLI
reference](/reference/cli/docker/scout/sbom/).

## Attach as build attestation {#attest}

You can generate the SBOM and attach it to the image at build-time as an
[attestation](/build/metadata/attestations/). BuildKit provides a default
SBOM generator which is different from what Docker Scout uses.
You can configure BuildKit to use the Docker Scout SBOM generator
using the `--attest` flag for the `docker build` command.
The Docker Scout SBOM indexer provides richer results
and ensures better compatibility with the Docker Scout image analysis.

```console
$ docker build --tag <org>/<image> \
  --attest type=sbom,generator=docker/scout-sbom-indexer:latest \
  --push .
```

To build images with SBOM attestations, you must use either the [containerd
image store](/desktop/features/containerd/) feature, or use a `docker-container`
builder together with the `--push` flag to push the image (with attestations)
directly to a registry. The classic image store doesn't support manifest lists
or image indices, which is required for adding attestations to an image.

## Extract to file

The command for extracting the SBOM of an image to an SPDX JSON file is
different depending on whether the image has been pushed to a registry or if
it's a local image.

### Remote image

To extract the SBOM of an image and save it to a file, you can use the `docker
buildx imagetools inspect` command. This command only works for images in a
registry.

```console
$ docker buildx imagetools inspect <image> --format "{{ json .SBOM }}" > sbom.spdx.json
```

### Local image

To extract the SPDX file for a local image, build the image with the `local`
exporter and use the `scout-sbom-indexer` SBOM generator plugin.

The following command saves the SBOM to a file at `build/sbom.spdx.json`.

```console
$ docker build --attest type=sbom,generator=docker/scout-sbom-indexer:latest \
  --output build .
```

---

## Install Docker Scout

# Install Docker Scout

The Docker Scout CLI plugin comes pre-installed with Docker Desktop.

If you run Docker Engine without Docker Desktop,
Docker Scout doesn't come pre-installed,
but you can install it as a standalone binary.

## Installation script

To install the latest version of the plugin, run the following commands:

```console
$ curl -fsSL https://raw.githubusercontent.com/docker/scout-cli/main/install.sh -o install-scout.sh
$ sh install-scout.sh
```

> [!NOTE]
>
> Always examine scripts downloaded from the internet before running them
> locally. Before installing, make yourself familiar with potential risks and
> limitations of the convenience script.

## Manual installation

**Linux**

1. Download the latest release from the [releases page](https://github.com/docker/scout-cli/releases).
2. Create a subdirectory under `$HOME/.docker` called `scout`.

   ```console
   $ mkdir -p $HOME/.docker/scout
   ```

3. Extract the archive and move the `docker-scout` binary to the `$HOME/.docker/scout` directory.
4. Make the binary executable: `chmod +x $HOME/.docker/scout/docker-scout`.
5. Add the `scout` subdirectory to your `.docker/config.json` as a plugin directory:

   ```json
   {
     "cliPluginsExtraDirs": [
       "/home/<USER>/.docker/scout"
     ]
   }
   ```

   Substitute `<USER>` with your username on the system.

   > [!NOTE]
   > The path for `cliPluginsExtraDirs` must be an absolute path.

**macOS**

1. Download the latest release from the [releases page](https://github.com/docker/scout-cli/releases).
2. Create a subdirectory under `$HOME/.docker` called `scout`.

   ```console
   $ mkdir -p $HOME/.docker/scout
   ```

3. Extract the archive and move the `docker-scout` binary to the `$HOME/.docker/scout` directory.
4. Make the binary executable:

   ```console
   $ chmod +x $HOME/.docker/scout/docker-scout
   ```

5. Authorize the binary to be executable on macOS:

   ```console
   xattr -d com.apple.quarantine $HOME/.docker/scout/docker-scout
   ```

6. Add the `scout` subdirectory to your `.docker/config.json` as a plugin directory:

   ```json
   {
     "cliPluginsExtraDirs": [
       "/Users/<USER>/.docker/scout"
     ]
   }
   ```

   Substitute `<USER>` with your username on the system.

   > [!NOTE]
   > The path for `cliPluginsExtraDirs` must be an absolute path.

**Windows**

1. Download the latest release from the [releases page](https://github.com/docker/scout-cli/releases).
2. Create a subdirectory under `%USERPROFILE%/.docker` called `scout`.

   ```console
   % mkdir %USERPROFILE%\.docker\scout
   ```

3. Extract the archive and move the `docker-scout.exe` binary to the `%USERPROFILE%\.docker\scout` directory.
4. Add the `scout` subdirectory to your `.docker\config.json` as a plugin directory:

   ```json
   {
     "cliPluginsExtraDirs": [
       "C:\Users\<USER>\.docker\scout"
     ]
   }
   ```

   Substitute `<USER>` with your username on the system.

   > [!NOTE]
   > The path for `cliPluginsExtraDirs` must be an absolute path.

## Container image

The Docker Scout CLI plugin is also available as a [container image](https://hub.docker.com/r/docker/scout-cli).
Use the `docker/scout-cli` to run `docker scout` commands without installing the CLI plugin on your host.

```console
$ docker run -it \
  -e DOCKER_SCOUT_HUB_USER=<your Docker Hub user name> \
  -e DOCKER_SCOUT_HUB_PASSWORD=<your Docker Hub PAT>  \
  docker/scout-cli <command>
```

## GitHub Action

The Docker Scout CLI plugin is also available as a [GitHub action](https://github.com/docker/scout-action).
You can use it in your GitHub workflows to automatically analyze images and evaluate policy compliance with each push.

Docker Scout also integrates with many more CI/CD tools, such as Jenkins, GitLab, and Azure DevOps.
Learn more about the [integrations](/scout/integrations/) available for Docker Scout.

---

## Integrate Docker Scout with Microsoft Azure DevOps Pipelines

# Integrate Docker Scout with Microsoft Azure DevOps Pipelines

The following examples runs in an Azure DevOps-connected repository containing
a Docker image's definition and contents. Triggered by a commit to the main
branch, the pipeline builds the image and uses Docker Scout to create a CVE
report.

First, set up the rest of the workflow and set up the variables available to all
pipeline steps. Add the following to an _azure-pipelines.yml_ file:

```yaml
trigger:
  - main

resources:
  - repo: self

variables:
  tag: "$(Build.BuildId)"
  image: "vonwig/nodejs-service"
```

This sets up the workflow to use a particular container image for the
application and tag each new image build with the build ID.

Add the following to the YAML file:

```yaml
stages:
  - stage: Build
    displayName: Build image
    jobs:
      - job: Build
        displayName: Build
        pool:
          vmImage: ubuntu-latest
        steps:
          - task: Docker@2
            displayName: Build an image
            inputs:
              command: build
              dockerfile: "$(Build.SourcesDirectory)/Dockerfile"
              repository: $(image)
              tags: |
                $(tag)
          - task: CmdLine@2
            displayName: Find CVEs on image
            inputs:
              script: |
                # Install the Docker Scout CLI
                curl -sSfL https://raw.githubusercontent.com/docker/scout-cli/main/install.sh | sh -s --
                # Login to Docker Hub required for Docker Scout CLI
                echo $(DOCKER_HUB_PAT) | docker login -u $(DOCKER_HUB_USER) --password-stdin
                # Get a CVE report for the built image and fail the pipeline when critical or high CVEs are detected
                docker scout cves $(image):$(tag) --exit-code --only-severity critical,high
```

This creates the flow mentioned previously. It builds and tags the image using
the checked-out Dockerfile, downloads the Docker Scout CLI, and then runs the
`cves` command against the new tag to generate a CVE report. It only shows
critical or high-severity vulnerabilities.

---

## Integrate Docker Scout with Circle CI

# Integrate Docker Scout with Circle CI

The following examples runs when triggered in CircleCI. When triggered, it
checks out the "docker/scout-demo-service:latest" image and tag and then uses
Docker Scout to create a CVE report.

Add the following to a _.circleci/config.yml_ file.

First, set up the rest of the workflow. Add the following to the YAML file:

```yaml
version: 2.1

jobs:
  build:
    docker:
      - image: cimg/base:stable
    environment:
      IMAGE_TAG: docker/scout-demo-service:latest
```

This defines the container image the workflow uses and an environment variable
for the image.

Add the following to the YAML file to define the steps for the workflow:

```yaml
steps:
  # Checkout the repository files
  - checkout
  
  # Set up a separate Docker environment to run `docker` commands in
  - setup_remote_docker:
      version: 20.10.24

  # Install Docker Scout and login to Docker Hub
  - run:
      name: Install Docker Scout
      command: |
        env
        curl -sSfL https://raw.githubusercontent.com/docker/scout-cli/main/install.sh | sh -s -- -b /home/circleci/bin
        echo $DOCKER_HUB_PAT | docker login -u $DOCKER_HUB_USER --password-stdin

  # Build the Docker image
  - run:
      name: Build Docker image
      command: docker build -t $IMAGE_TAG .
  
  # Run Docker Scout          
  - run:
      name: Scan image for CVEs
      command: |
        docker scout cves $IMAGE_TAG --exit-code --only-severity critical,high
```

This checks out the repository files and then sets up a separate Docker
environment to run commands in.

It installs Docker Scout, logs into Docker Hub, builds the Docker image, and
then runs Docker Scout to generate a CVE report. It only shows critical or
high-severity vulnerabilities.

Finally, add a name for the workflow and the workflow's jobs:

```yaml
workflows:
  build-docker-image:
    jobs:
      - build
```

---

## Integrate Docker Scout with GitHub Actions

# Integrate Docker Scout with GitHub Actions

The following example shows how to set up a Docker Scout workflow with GitHub
Actions. Triggered by a pull request, the action builds the image and uses
Docker Scout to compare the new version to the version of that image running in
production.

This workflow uses the
[docker/scout-action](https://github.com/docker/scout-action) GitHub Action to
run the `docker scout compare` command to visualize how images for pull request
stack up against the image you run in production.

## Prerequisites

- This example assumes that you have an existing image repository, in Docker Hub
  or in another registry, where you've enabled Docker Scout.
- This example makes use of [environments](/scout/integrations/environment/), to compare
  the image built in the pull request with a different version of the same image
  in an environment called `production`.

## Steps

First, set up the GitHub Action workflow to build an image. This isn't specific
to Docker Scout here, but you'll need to build an image to have
something to compare with.

Add the following to a GitHub Actions YAML file:

```yaml
name: Docker

on:
  push:
    tags: ["*"]
    branches:
      - "main"
  pull_request:
    branches: ["**"]

env:
  # Hostname of your registry
  REGISTRY: docker.io
  # Image repository, without hostname and tag
  IMAGE_NAME: ${{ github.repository }}
  SHA: ${{ github.event.pull_request.head.sha || github.event.after }}

jobs:
  build:
    runs-on: ubuntu-latest
    permissions:
      pull-requests: write

    steps:
      # Authenticate to the container registry
      - name: Authenticate to registry ${{ env.REGISTRY }}
        uses: docker/login-action@v4
        with:
          registry: ${{ env.REGISTRY }}
          username: ${{ secrets.REGISTRY_USER }}
          password: ${{ secrets.REGISTRY_TOKEN }}
      
      - name: Setup Docker buildx
        uses: docker/setup-buildx-action@v4

      # Extract metadata (tags, labels) for Docker
      - name: Extract Docker metadata
        id: meta
        uses: docker/metadata-action@v6
        with:
          images: ${{ env.REGISTRY }}/${{ env.IMAGE_NAME }}
          labels: |
            org.opencontainers.image.revision=${{ env.SHA }}
          tags: |
            type=edge,branch=$repo.default_branch
            type=semver,pattern=v{{version}}
            type=sha,prefix=,suffix=,format=short

      # Build and push Docker image with Buildx
      # (don't push on PR, load instead)
      - name: Build and push Docker image
        id: build-and-push
        uses: docker/build-push-action@v7
        with:
          sbom: ${{ github.event_name != 'pull_request' }}
          provenance: ${{ github.event_name != 'pull_request' }}
          push: ${{ github.event_name != 'pull_request' }}
          load: ${{ github.event_name == 'pull_request' }}
          tags: ${{ steps.meta.outputs.tags }}
          labels: ${{ steps.meta.outputs.labels }}
          cache-from: type=gha
          cache-to: type=gha,mode=max
```

This creates workflow steps to:

1. Set up Docker buildx.
2. Authenticate to the registry.
3. Extract metadata from Git reference and GitHub events.
4. Build and push the Docker image to the registry.

> [!NOTE]
>
> This CI workflow runs a local analysis and evaluation of your image. To
> evaluate the image locally, you must ensure that the image is loaded the
> local image store of your runner.
>
> This comparison doesn't work if you push the image to a registry, or if you
> build an image that can't be loaded to the runner's local image store. For
> example, multi-platform images or images with SBOM or provenance attestation
> can't be loaded to the local image store.

With this setup out of the way, you can add the following steps to run the
image comparison:

```yaml
      # You can skip this step if Docker Hub is your registry
      # and you already authenticated before
      - name: Authenticate to Docker
        uses: docker/login-action@v4
        with:
          username: ${{ secrets.DOCKER_USER }}
          password: ${{ secrets.DOCKER_PAT }}

      # Compare the image built in the pull request with the one in production
      - name: Docker Scout
        id: docker-scout
        if: ${{ github.event_name == 'pull_request' }}
        uses: docker/scout-action@v1
        with:
          command: compare
          image: ${{ steps.meta.outputs.tags }}
          to-env: production
          ignore-unchanged: true
          only-severities: critical,high
          github-token: ${{ secrets.GITHUB_TOKEN }}
```

The compare command analyzes the image and evaluates policy compliance, and
cross-references the results with the corresponding image in the `production`
environment. This example only includes critical and high-severity
vulnerabilities, and excludes vulnerabilities that exist in both images,
showing only what's changed.

The GitHub Action outputs the comparison results in a pull request comment by
default.

![A screenshot showing the results of Docker Scout output in a GitHub Action](/scout/images/gha-output.webp)

Expand the **Policies** section to view the difference in policy compliance
between the two images. Note that while the new image in this example isn't
fully compliant, the output shows that the standing for the new image has
improved compared to the baseline.

![GHA policy evaluation output](/scout/images/gha-policy-eval.webp)

---

## Integrate Docker Scout with GitLab CI/CD

# Integrate Docker Scout with GitLab CI/CD

The following examples runs in GitLab CI in a repository containing a Docker
image's definition and contents. Triggered by a commit, the pipeline builds the
image. If the commit was to the default branch, it uses Docker Scout to get a
CVE report. If the commit was to a different branch, it uses Docker Scout to
compare the new version to the current published version.

## Steps

First, set up the rest of the workflow. There's a lot that's not specific to
Docker Scout but needed to create the images to compare.

Add the following to a `.gitlab-ci.yml` file at the root of your repository.

```yaml
docker-build:
  image: docker:latest
  stage: build
  services:
    - docker:dind
  before_script:
    - docker login -u "$CI_REGISTRY_USER" -p "$CI_REGISTRY_PASSWORD" $CI_REGISTRY

    # Install curl and the Docker Scout CLI
    - |
      apk add --update curl
      curl -sSfL https://raw.githubusercontent.com/docker/scout-cli/main/install.sh | sh -s -- 
      apk del curl 
      rm -rf /var/cache/apk/*
    # Login to Docker Hub required for Docker Scout CLI
    - echo "$DOCKER_HUB_PAT" | docker login -u "$DOCKER_HUB_USER" --password-stdin
```

This sets up the workflow to build Docker images with Docker-in-Docker mode,
running Docker inside a container.

It then downloads `curl` and the Docker Scout CLI plugin, logs into the Docker
registry using environment variables defined in your repository's settings.

Add the following to the YAML file:

```yaml
script:
  - |
    if [[ "$CI_COMMIT_BRANCH" == "$CI_DEFAULT_BRANCH" ]]; then
      tag=""
      echo "Running on default branch '$CI_DEFAULT_BRANCH': tag = 'latest'"
    else
      tag=":$CI_COMMIT_REF_SLUG"
      echo "Running on branch '$CI_COMMIT_BRANCH': tag = $tag"
    fi
  - docker build --pull -t "$CI_REGISTRY_IMAGE${tag}" .
  - |
    if [[ "$CI_COMMIT_BRANCH" == "$CI_DEFAULT_BRANCH" ]]; then
      # Get a CVE report for the built image and fail the pipeline when critical or high CVEs are detected
      docker scout cves "$CI_REGISTRY_IMAGE${tag}" --exit-code --only-severity critical,high    
    else
      # Compare image from branch with latest image from the default branch and fail if new critical or high CVEs are detected
      docker scout compare "$CI_REGISTRY_IMAGE${tag}" --to "$CI_REGISTRY_IMAGE:latest" --exit-code --only-severity critical,high --ignore-unchanged
    fi

  - docker push "$CI_REGISTRY_IMAGE${tag}"
```

This creates the flow mentioned previously. If the commit was to the default
branch, Docker Scout generates a CVE report. If the commit was to a different
branch, Docker Scout compares the new version to the current published version.
It only shows critical or high-severity vulnerabilities and ignores
vulnerabilities that haven't changed since the last analysis.

Add the following to the YAML file:

```yaml
rules:
  - if: $CI_COMMIT_BRANCH
    exists:
      - Dockerfile
```

These final lines ensure that the pipeline only runs if the commit contains a
Dockerfile and if the commit was to the CI branch.

## Video walkthrough

The following is a video walkthrough of the process of setting up the workflow with GitLab.

<iframe class="border-0 w-full aspect-video mb-8" allow="fullscreen" src="https://www.loom.com/embed/451336c4508c42189532108fc37b2560?sid=f912524b-276d-417d-b44a-c2d39719aa1a"></iframe>

---

## Integrate Docker Scout with Jenkins

# Integrate Docker Scout with Jenkins

You can add the following stage and steps definition to a `Jenkinsfile` to run
Docker Scout as part of a Jenkins pipeline. The pipeline needs a `DOCKER_HUB`
credential containing the username and password for authenticating to Docker
Hub. It also needs an environment variable defined for the image and tag.

```groovy
pipeline {
    agent {
        // Agent details
    }

    environment {
        DOCKER_HUB = credentials('jenkins-docker-hub-credentials')
        IMAGE_TAG  = 'myorg/scout-demo-service:latest'
    }

    stages {
        stage('Analyze image') {
            steps {
                // Install Docker Scout
                sh 'curl -sSfL https://raw.githubusercontent.com/docker/scout-cli/main/install.sh | sh -s -- -b /usr/local/bin'

                // Log into Docker Hub
                sh 'echo $DOCKER_HUB_PSW | docker login -u $DOCKER_HUB_USR --password-stdin'

                // Analyze and fail on critical or high vulnerabilities
                sh 'docker scout cves $IMAGE_TAG --exit-code --only-severity critical,high'
            }
        }
    }
}
```

This installs Docker Scout, logs into Docker Hub, and then runs Docker Scout to
generate a CVE report for an image and tag. It only shows critical or
high-severity vulnerabilities.

> [!NOTE]
>
> If you're seeing a `permission denied` error related to the image cache, try
> setting the [`DOCKER_SCOUT_CACHE_DIR`](/scout/how-tos/configure-cli/) environment
> variable to a writable directory. Or alternatively, disable local caching
> entirely with `DOCKER_SCOUT_NO_CACHE=true`.

---

## Integrate Docker Scout with SonarQube

# Integrate Docker Scout with SonarQube

The SonarQube integration enables Docker Scout to surface SonarQube quality
gate checks through Policy Evaluation, under a new [SonarQube Quality Gates
Policy](/scout/policy/#sonarqube-quality-gates-policy).

## How it works

This integration uses [SonarQube
webhooks](https://docs.sonarsource.com/sonarqube/latest/project-administration/webhooks/)
to notify Docker Scout of when a SonarQube project analysis has completed. When
the webhook is called, Docker Scout receives the analysis results, and stores
them in the database.

When you push a new image to a repository, Docker Scout evaluates the results
of the SonarQube analysis record corresponding to the image. Docker Scout uses
Git provenance metadata on the images, from provenance attestations or an OCI
annotations, to link image repositories with SonarQube analysis results.

> [!NOTE]
>
> Docker Scout doesn't have access to historic SonarQube analysis records. Only
> analysis results recorded after the integration is enabled will be available
> to Docker Scout.

Both self-managed SonarQube instances and SonarCloud are supported.

## Prerequisites

To integrate Docker Scout with SonarQube, ensure that:

- Your image repository is [integrated with Docker Scout](/scout/code-quality/#container-registries).
- Your images are built with [provenance attestations](/build/metadata/attestations/slsa-provenance/),
  or the `org.opencontainers.image.revision` annotation,
  containing information about the Git repository.

## Enable the SonarQube integration

1. Go to the [SonarQube integrations page](https://scout.docker.com/settings/integrations/sonarqube/)
   on the Docker Scout Dashboard.
2. In the **How to integrate** section, enter a configuration name for this
   integration. Docker Scout uses this label as a display name for the
   integration, and to name the webhook.
3. Select **Next**.
4. Enter the configuration details for your SonarQube instance. Docker Scout
   uses this information to create SonarQube webhook.

   In SonarQube, [generate a new **User token**](https://docs.sonarsource.com/sonarqube/latest/user-guide/user-account/generating-and-using-tokens/#generating-a-token).
   The token requires 'Administer' permission on the specified project, or
   global 'Administer' permission.

   Enter the token, your SonarQube URL, and the ID of your SonarQube
   organization. The SonarQube organization is required if you're using
   SonarCloud.

5. Select **Enable configuration**.

   Docker Scout performs a connection test to verify that the provided details
   are correct, and that the token has the necessary permissions.

6. After a successful connection test, you're redirected to the SonarQube
   integration overview, which lists all your SonarQube integrations and their
   statuses.

From the integration overview page, you can go directly to the
**SonarQube Quality Gates Policy**.
This policy will have no results initially. To start seeing evaluation results
for this policy, trigger a new SonarQube analysis of your project and push the
corresponding image to a repository. For more information, refer to the
[policy description](/scout/policy/#sonarqube-quality-gates).

---

## Generic environment integration with CLI

# Generic environment integration with CLI

You can create a generic environment integration by running the Docker Scout
CLI client in your CI workflows. The CLI client is available as a binary on
GitHub and as a container image on Docker Hub. Use the client to invoke the
`docker scout environment` command to assign your images to environments.

For more information about how to use the `docker scout environment` command,
refer to the [CLI reference](/reference/cli/docker/scout/environment/).

## Examples

Before you start, set the following environment variables in your CI system:

- `DOCKER_SCOUT_HUB_USER`: your Docker Hub username
- `DOCKER_SCOUT_HUB_PASSWORD`: your Docker Hub personal access token

Make sure the variables are accessible to your project.

**Circle CI**

```yaml
version: 2.1

jobs:
  record_environment:
    machine:
      image: ubuntu-2204:current
    image: namespace/repo
    steps:
      - run: |
          if [[ -z "$CIRCLE_TAG" ]]; then
            tag="$CIRCLE_BRANCH"
            echo "Running on branch '$CIRCLE_BRANCH'"
          else
            tag="$CIRCLE_TAG"
            echo "Running tag '$CIRCLE_TAG'"
          fi
          echo "tag = $tag"
      - run: docker run -it \
          -e DOCKER_SCOUT_HUB_USER=$DOCKER_SCOUT_HUB_USER \
          -e DOCKER_SCOUT_HUB_PASSWORD=$DOCKER_SCOUT_HUB_PASSWORD \
          docker/scout-cli:1.0.2 environment \
          --org "<MY_DOCKER_ORG>" \
          "<ENVIRONMENT>" ${image}:${tag}
```

**GitLab**

The following example uses the [Docker executor](https://docs.gitlab.com/runner/executors/docker.html).

```yaml
variables:
  image: namespace/repo

record_environment:
  image: docker/scout-cli:1.0.2
  script:
    - |
      if [[ -z "$CI_COMMIT_TAG" ]]; then
        tag="$CI_COMMIT_REF_SLUG"
        echo "Running on branch '$CI_COMMIT_BRANCH'"
      else
        tag="$CI_COMMIT_TAG"
        echo "Running tag '$CI_COMMIT_TAG'"
      fi
      echo "tag = $tag"
    - environment --org <MY_DOCKER_ORG> "PRODUCTION" ${image}:${tag}
```

**Azure DevOps**

```yaml
trigger:
  - main

resources:
  - repo: self

variables:
  tag: "$(Build.BuildId)"
  image: "namespace/repo"

stages:
  - stage: Docker Scout
    displayName: Docker Scout environment integration
    jobs:
      - job: Record
        displayName: Record environment
        pool:
          vmImage: ubuntu-latest
        steps:
          - task: Docker@2
          - script: docker run -it \
              -e DOCKER_SCOUT_HUB_USER=$DOCKER_SCOUT_HUB_USER \
              -e DOCKER_SCOUT_HUB_PASSWORD=$DOCKER_SCOUT_HUB_PASSWORD \
              docker/scout-cli:1.0.2 environment \
              --org "<MY_DOCKER_ORG>" \
              "<ENVIRONMENT>" $(image):$(tag)
```

**Jenkins**

```groovy
stage('Record environment') {
    steps {
        // Install Docker Scout
        sh 'curl -sSfL https://raw.githubusercontent.com/docker/scout-cli/main/install.sh | sh -s -- -b /usr/local/bin'
        
        // Log into Docker Hub
        sh 'echo $DOCKER_SCOUT_HUB_PASSWORD | docker login -u $DOCKER_SCOUT_HUB_USER --password-stdin'

        // Record image to environment
        sh 'docker-scout environment --org "<MY_DOCKER_ORG>" "<ENVIRONMENT>" $IMAGE_TAG'
    }
}
```

---
