---
title: "Docker Hardened Images"
source: "https://docs.docker.com/dhi/"
version: "latest"
---

# Docker Hardened Images

> 原始文档来源：https://docs.docker.com/dhi/

---

## Attestations

# Attestations

Docker Hardened Images (DHIs) and charts include comprehensive, signed security
attestations that verify the image's build process, contents, and security
posture. These attestations are a core part of secure software supply chain
practices and help users validate that an image is trustworthy and
policy-compliant.

## What is an attestation?

An attestation is a signed statement that provides verifiable information
about an image or chart, such as how it was built, what's inside it, and what security
checks it has passed. Attestations are typically signed using Sigstore tooling
(such as Cosign), making them tamper-evident and cryptographically verifiable.

Attestations follow standardized formats (like [in-toto](https://in-toto.io/),
[CycloneDX](https://cyclonedx.org/), and [SLSA](https://slsa.dev/)) and are
attached to the image or chart as OCI-compliant metadata. They can be generated
automatically during image builds or added manually to document extra tests,
scan results, or custom provenance.

## Why are attestations important?

Attestations provide critical visibility into the software supply chain by:

- Documenting *what* went into an image (e.g., SBOMs)
- Verifying *how* it was built (e.g., build provenance)
- Capturing *what security scans* it has passed or failed (e.g., CVE reports,
  secrets scans, test results)
- Helping organizations enforce compliance and security policies
- Supporting runtime trust decisions and CI/CD policy gates

They are essential for meeting industry standards such as SLSA,
and help teams reduce the risk of supply chain attacks by making build and
security data transparent and verifiable.

## How Docker Hardened Images and charts use attestations

All DHIs and charts are built using [SLSA Build Level
3](https://slsa.dev/spec/latest/levels) practices, and each image variant is
published with a full set of signed attestations. These attestations allow users
to:

- Verify that the image or chart was built from trusted sources in a secure environment
- View SBOMs in multiple formats to understand component-level details
- Review scan results to check for vulnerabilities or embedded secrets
- Confirm the build and deployment history of each image

Attestations are automatically published and associated with each  DHI
and chart. They can be inspected using tools like [Docker
Scout](/dhi/how-to/verify/) or
[Cosign](https://docs.sigstore.dev/cosign/overview), and are consumable by CI/CD
tooling or security platforms.

## Image attestations

While every DHI variant includes a set of attestations, the attestations may
vary based on the image variant. For example, some images may include a STIG
scan attestation. The following table is a comprehensive list of all
attestations that may be included with a DHI. To see which attestations are
available for a specific image variant, including the specific predicate type URIs,
use Docker Scout:

```console
$ docker scout attest list dhi.io/<image>:<tag>
```

For more details, see [Verify image attestations](/dhi/how-to/verify/#verify-image-attestations).

| Attestation type           | Description                                                                                                                                                                                                                     |
|----------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| CycloneDX SBOM             | A software bill of materials in [CycloneDX](https://cyclonedx.org/) format, listing components, libraries, and versions.                                                                                                      |
| STIG scan                  | Results of a STIG scan, with output in HTML and XCCDF formats.                                                                                                                           |
| CVEs (In-Toto format)      | A list of known vulnerabilities (CVEs) affecting the image's components, based on package and distribution scanning.                                                                           |
| VEX                        | A [Vulnerability Exploitability eXchange (VEX)](https://openvex.dev/) document that identifies vulnerabilities that do not apply to the image and explains why (e.g., not reachable or not present).                         |
| Scout health score         | A signed attestation from Docker Scout that summarizes the overall security and quality posture of the image.                                                                           |
| Scout provenance           | Provenance metadata generated by Docker Scout, including the source Git commit, build parameters, and environment details.                                                               |
| Scout SBOM                 | An SBOM generated and signed by Docker Scout, including additional Docker-specific metadata.                                                                                             |
| Secrets scan               | Results of a scan for accidentally included secrets, such as credentials, tokens, or private keys.                                                                                       |
| Tests                      | A record of automated tests run against the image, such as functional checks or validation scripts.                                                                                      |
| Virus scan                 | Results of antivirus scans performed on the image layers. For details, see [Malware scanning](/dhi/explore/malware-scanning/).                                                            |
| CVEs (Scout format)        | A vulnerability report generated by Docker Scout, listing known CVEs and severity data.                                                                                                  |
| SLSA provenance            | A standard [SLSA](https://slsa.dev/) provenance statement describing how the image was built, including build tool, parameters, and source.                                               |
| SLSA verification summary  | A summary attestation indicating the image's compliance with SLSA requirements.                                                                                                          |
| SPDX SBOM                  | An SBOM in [SPDX](https://spdx.dev/) format, widely adopted in open-source ecosystems.                                                                                                   |
| FIPS compliance            | An attestation that verifies the image uses FIPS 140-validated cryptographic modules.                              |
| DHI Image Sources          | Links to a corresponding source image containing all materials used to build the image, including package source code, Git repositories, and local files, ensuring compliance with open source license requirements. |

## Package attestations

In addition to image-level attestations, Docker hardened packages also include
their own attestations. These package-level attestations provide provenance and
build information for individual packages within an image, allowing you to
trace the supply chain at a granular level.

Package attestations include similar information as image attestations, such as
SLSA provenance, showing how each package was built and what materials were
used. You can extract package information from an image's attestations and then
retrieve the package's own attestations recursively.

For detailed instructions on how to access and verify package attestations, see
[Package attestations](/dhi/how-to/hardened-packages/#package-attestations).

## Helm chart attestations

Docker Hardened Image (DHI) charts also include comprehensive signed attestations
that provide transparency and verification for your Kubernetes deployments. Like
DHI container images, these charts are built following SLSA Build Level 3
practices and include extensive security metadata.

DHI Helm charts include the following attestations. To view the specific predicate
type URIs for these attestations, use Docker Scout:

```console
$ docker scout attest list dhi.io/<chart>:<version>
```

For more details, see [Verify Helm chart attestations](/dhi/how-to/verify/#verify-helm-chart-attestations-with-docker-scout).

| Attestation type           | Description                                                                                                                                                                                                                     |
|----------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| CycloneDX SBOM             | A software bill of materials in [CycloneDX](https://cyclonedx.org/) format, listing the chart itself and all container images and tools referenced by the chart.                                                              |
| CVEs (In-Toto format)      | A list of known vulnerabilities (CVEs) affecting the container images and components referenced by the chart.                                                                                                                   |
| Scout health score         | A signed attestation from Docker Scout that summarizes the overall security and quality posture of the chart and its referenced images.                                                                                        |
| Scout provenance           | Provenance metadata generated by Docker Scout, including the chart source repository, build images used, and build parameters.                                                                                                  |
| Scout SBOM                 | An SBOM generated and signed by Docker Scout, including the chart and container images it references, with additional Docker-specific metadata.                                                                                |
| Secrets scan               | Results of a scan for accidentally included secrets, such as credentials, tokens, or private keys, in the chart package.                                                                                                       |
| Tests                      | A record of automated tests run against the chart to validate functionality and compatibility with referenced images.                                                                                                          |
| Virus scan                 | Results of antivirus scans performed on the chart package. For details, see [Malware scanning](/dhi/explore/malware-scanning/).                                                                                                |
| CVEs (Scout format)        | A vulnerability report generated by Docker Scout, listing known CVEs and severity data for the chart's referenced images.                                                                                                      |
| SLSA provenance            | A standard [SLSA](https://slsa.dev/) provenance statement describing how the chart was built, including build tool, source repository, referenced images, and build materials.                                                 |
| SPDX SBOM                  | An SBOM in [SPDX](https://spdx.dev/) format, listing the chart and all container images and tools it references.                                                                                                              |

## View and verify attestations

To view and verify attestations, see [Verify a Docker Hardened
Image](/dhi/how-to/verify/).

## Add your own attestations

In addition to the comprehensive attestations provided by Docker Hardened
Images, you can add your own signed attestations when building derivative
images. This is especially useful if you鈥檙e building new applications on top of
a DHI and want to maintain transparency, traceability, and trust in your
software supply chain.

By attaching attestations such as SBOMs, build provenance, or custom metadata,
you can meet compliance requirements, pass security audits, and support policy
evaluation tools like Docker Scout.

These attestations can then be verified downstream using tools
like Cosign or Docker Scout.

To learn how to attach custom attestations during the build process, see [Build
attestations](/build/metadata/attestations/).

---

## CIS Benchmark

# CIS Benchmark

## What is the CIS Docker Benchmark?

The [CIS Docker Benchmark](https://www.cisecurity.org/benchmark/docker) is part
of the globally recognized CIS Benchmarks, developed by the [Center for
Internet Security (CIS)](https://www.cisecurity.org/). It defines recommended secure
configurations for all aspects of the Docker container ecosystem, including the
container host, Docker daemon, container images, and the container runtime.

## Why CIS Benchmark compliance matters

Following the CIS Docker Benchmark helps organizations:

- Reduce security risk with widely recognized hardening guidance.
- Meet regulatory or contractual requirements that reference CIS controls.
- Standardize image and Dockerfile practices across teams.
- Demonstrate audit readiness with configuration decisions grounded in a public standard.

## How Docker Hardened Images comply with the CIS Benchmark

Docker Hardened Images (DHIs) are designed with security in mind and are
verified to be compliant with the relevant controls from the CIS Docker
Benchmark for the scope that applies to container images and Dockerfile
configuration.

CIS-compliant DHIs are compliant with all controls in Section 4, with the sole
exception of the control requiring Docker Content Trust (DCT), which [Docker
officially retired](https://www.docker.com/blog/retiring-docker-content-trust/).
Instead, DHIs are [signed](/dhi/core-concepts/signatures/) using
Cosign, providing an even higher level of authenticity and integrity. By
starting from a CIS-compliant DHI, teams can adopt image-level best practices
from the benchmark more quickly and confidently.

> [!NOTE]
>
> The CIS Docker Benchmark also includes controls for the host, daemon, and
> runtime. CIS-compliant DHIs address only the image and Dockerfile scope (Section
> 4). Overall compliance still depends on how you configure and operate the
> broader environment.

## Identify CIS-compliant images

CIS-compliant images are labeled as **CIS** in the Docker Hardened Images catalog.
To find them, [search the catalog](/dhi/how-to/explore/) and look for the **CIS**
designation on individual listings.

## Get the benchmark

Download the latest CIS Docker Benchmark directly from CIS:
https://www.cisecurity.org/benchmark/docker

---

## Common Vulnerabilities and Exposures (CVEs)

# Common Vulnerabilities and Exposures (CVEs)

## What are CVEs?

CVEs are publicly disclosed cybersecurity flaws in software or hardware. Each
CVE is assigned a unique identifier (e.g., CVE-2024-12345) and includes a
standardized description, allowing organizations to track and address
vulnerabilities consistently.

In the context of Docker, CVEs often pertain to issues within base images, or
application dependencies. These vulnerabilities can range from minor bugs to
critical security risks, such as remote code execution or privilege escalation.

## Why are CVEs important?

Regularly scanning and updating Docker images to mitigate CVEs is crucial for
maintaining a secure and compliant environment. Ignoring CVEs can lead to severe
security breaches, including:

- Unauthorized access: Exploits can grant attackers unauthorized access to
  systems.
- Data breaches: Sensitive information can be exposed or stolen.
- Service disruptions: Vulnerabilities can be leveraged to disrupt services or
  cause downtime.
- Compliance violations: Failure to address known vulnerabilities can lead to
  non-compliance with industry regulations and standards.

## How Docker Hardened Images help mitigate CVEs

Docker Hardened Images (DHIs) are crafted to minimize the risk of CVEs from the
outset. By adopting a security-first approach, DHIs offer several advantages in
CVE mitigation:

- Reduced attack surface: DHIs are built using a distroless approach, stripping
  away unnecessary components and packages. This reduction in image size, up to
  95% smaller than traditional images, limits the number of potential
  vulnerabilities, making it harder for attackers to exploit unneeded software.

- Faster CVE remediation: Maintained by Docker with an [enterprise-grade SLA](https://docs.docker.com/go/dhi-sla/),
  DHIs are continuously updated to address known vulnerabilities. Critical and
  high-severity CVEs are patched quickly, ensuring that your containers remain
  secure without manual intervention.

- Proactive vulnerability management: By utilizing DHIs, organizations can
  proactively manage vulnerabilities. The images come with CVE and Vulnerability
  Exposure (VEX) feeds, enabling teams to stay informed about potential threats
  and take necessary actions promptly.

## Scan images for CVEs

Regularly scanning Docker images for CVEs is essential for maintaining a secure
containerized environment. While Docker Scout is integrated into Docker Desktop
and the Docker CLI, tools like Grype and Trivy offer alternative scanning
capabilities. The following are instructions for using each tool to scan Docker
images for CVEs.

### Docker Scout

Docker Scout is integrated into Docker Desktop and the Docker CLI. It provides
vulnerability insights, CVE summaries, and direct links to remediation guidance.

#### Scan a DHI using Docker Scout

To scan a Docker Hardened Image using Docker Scout, run the following
command:

```console
$ docker scout cves dhi.io/<image>:<tag> --platform <platform>
```

Example output:

```plaintext
    v SBOM obtained from attestation, 101 packages found
    v Provenance obtained from attestation
    v VEX statements obtained from attestation
    v No vulnerable package detected
    ...
```

For more detailed filtering and JSON output, see [Docker Scout CLI reference](/reference/cli/docker/scout/).

### Grype

[Grype](https://github.com/anchore/grype) is an open-source scanner that checks
container images against vulnerability databases like the NVD and distro
advisories.

#### Scan a DHI using Grype

After installing Grype, you can scan a Docker Hardened Image by pulling
the image and running the scan command. Grype requires you to export the VEX
attestation to a file first:

```console
$ docker pull dhi.io/<image>:<tag>
$ docker scout vex get dhi.io/<image>:<tag> --output vex.json
$ grype dhi.io/<image>:<tag> --vex vex.json
```

Example output:

```plaintext
NAME               INSTALLED              FIXED-IN     TYPE  VULNERABILITY     SEVERITY    EPSS%  RISK
libperl5.36        5.36.0-7+deb12u2       (won't fix)  deb   CVE-2023-31484    High        79.45    1.1
perl               5.36.0-7+deb12u2       (won't fix)  deb   CVE-2023-31484    High        79.45    1.1
perl-base          5.36.0-7+deb12u2       (won't fix)  deb   CVE-2023-31484    High        79.45    1.1
...
```

### Trivy

[Trivy](https://github.com/aquasecurity/trivy) is an open-source vulnerability
scanner for containers and other artifacts. It detects vulnerabilities in OS
packages and application dependencies.

#### Scan a DHI using Trivy

After installing Trivy, you can scan a Docker Hardened Image by pulling
the image and running the scan command:

```console
$ docker pull dhi.io/<image>:<tag>
$ trivy image --scanners vuln --vex repo dhi.io/<image>:<tag>
```

Example output:

```plaintext
Report Summary

鈹屸攢鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹攢鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹攢鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹攢鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹�
鈹�                                    Target                                    鈹�    Type    鈹� Vulnerabilities 鈹� Secrets 鈹�
鈹溾攢鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹尖攢鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹尖攢鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹尖攢鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹�
鈹� dhi.io/<image>:<tag> (debian 12.11)                                          鈹�   debian   鈹�       66        鈹�    -    鈹�
鈹溾攢鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹尖攢鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹尖攢鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹尖攢鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹�
鈹� opt/python-3.13.4/lib/python3.13/site-packages/pip-25.1.1.dist-info/METADATA 鈹� python-pkg 鈹�        0        鈹�    -    鈹�
鈹斺攢鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹粹攢鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹粹攢鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹粹攢鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹€鈹�
```

## Use VEX to filter known non-exploitable CVEs

Docker Hardened Images include signed [VEX (Vulnerability Exploitability
eXchange)](/dhi/core-concepts/vex/) attestations that identify vulnerabilities not relevant to the image鈥檚
runtime behavior.

When using Docker Scout or Trivy, these VEX statements are automatically
applied using the previous examples, and no manual configuration needed.

To manually retrieve the VEX attestation for tools that support it:

```console
$ docker scout vex get dhi.io/<image>:<tag> --output vex.json
```

> [!NOTE]
>
> If the image exists locally on your device, you must prefix the image name with `registry://`. For example, use
> `registry://dhi.io/python:3.13` instead of `dhi.io/python:3.13`.

For example:

```console
$ docker scout vex get dhi.io/python:3.13 --output vex.json
```

This creates a `vex.json` file containing the VEX statements for the specified
image. You can then use this file with tools that support VEX to filter out known non-exploitable CVEs.

---

## Image digests

# Image digests

## What are Docker image digests?

A Docker image digest is a unique, cryptographic identifier (SHA-256 hash)
representing the content of a Docker image. Unlike tags, which can be reused or
changed, a digest is immutable and ensures that the exact same image is pulled
every time. This guarantees consistency across different environments and
deployments.

For example, the digest for the `nginx:latest` image might look like:

```text
sha256:94a00394bc5a8ef503fb59db0a7d0ae9e1110866e8aee8ba40cd864cea69ea1a
```

This digest uniquely identifies the specific version of the `nginx:latest` image,
ensuring that any changes to the image content result in a different digest.

## Why are image digests important?

Using image digests instead of tags offers several advantages:

- Immutability: Once an image is built and its digest is generated, the content
  tied to that digest cannot change. This means that if you pull an image using
  its digest, you can be confident that you are retrieving exactly the same
  image that was originally built.

- Security: Digests help prevent supply chain attacks by ensuring that the image
  content has not been tampered with. Even a small change in the image content
  will result in a completely different digest.

- Consistency: Using digests ensures that the same image is used across
  different environments, reducing the risk of discrepancies between
  development, staging, and production environments.

## Docker Hardened Image digests

By using image digests to reference DHIs, you can ensure that your applications are
always using the exact same secure image version, enhancing security and
compliance

## View an image digest

### Use the Docker CLI

To view the image digest of a Docker image, you can use the following command. Replace
`<image-name>:<tag>` with the image name and tag.

```console
$ docker buildx imagetools inspect <image-name>:<tag>
```

### Use the Docker Hub UI

1. Go to [Docker Hub](https://hub.docker.com/) and sign in.
2. Navigate to your organization's namespace and open the mirrored DHI repository.
3. Select the **Tags** tab to view image variants.
4. Each tag in the list includes a **Digest** field showing the image's SHA-256 value.

## Pull an image by digest

Pulling an image by digest ensures that you are pulling the exact image version
identified by the specified digest.

To pull a Docker image using its digest, use the following command. Replace
`<image-name>` with the image name and `<digest>` with the image digest.

```console
$ docker pull <image-name>@sha256:<digest>
```

For example, to pull a `docs/dhi-python:3.13` image using its digest of
`94a00394bc5a8ef503fb59db0a7d0ae9e1110866e8aee8ba40cd864cea69ea1a`, you would
run:

```console
$ docker pull docs/dhi-python@sha256:94a00394bc5a8ef503fb59db0a7d0ae9e1110866e8aee8ba40cd864cea69ea1a
```

## Multi-platform images and manifests

Docker Hardened Images are published as multi-platform images, which means
a single image tag (like `docs/dhi-python:3.13`) can support multiple operating
systems and CPU architectures, such as `linux/amd64`, `linux/arm64`, and more.

Instead of pointing to a single image, a multi-platform tag points to a manifest
list (also called an index), which is a higher-level object that references
multiple image digests, one for each supported platform.

When you inspect a multi-platform image using `docker buildx imagetools inspect`, you'll see something like this:

```text
Name:      docs/dhi-python:3.13
MediaType: application/vnd.docker.distribution.manifest.list.v2+json
Digest:    sha256:6e05...d231

Manifests:
  Name:        docs/dhi-python:3.13@sha256:94a0...ea1a
  Platform:    linux/amd64
  ...

  Name:        docs/dhi-python:3.13@sha256:7f1d...bc43
  Platform:    linux/arm64
  ...
```

- The manifest list digest (`sha256:6e05...d231`) identifies the overall
  multi-platform image.
- Each platform-specific image has its own digest (e.g., `sha256:94a0...ea1a`
  for `linux/amd64`).

### Why this matters

- Reproducibility: If you're building or running containers on different
  architectures, using a tag alone will resolve to the appropriate image digest
  for your platform.
- Verification: You can pull and verify a specific image digest for your
  platform to ensure you're using the exact image version, not just the manifest
  list.
- Policy enforcement: When enforcing digest-based policies with Docker Scout,
  each platform variant is evaluated individually using its digest.

---

## Minimal or distroless images

# Minimal or distroless images

Minimal images, sometimes called distroless images, are container images
stripped of unnecessary components such as package managers, shells, or even the
underlying operating system distribution. Docker Hardened Images (DHI) embrace
this minimal approach to reduce vulnerabilities and enforce secure software
delivery. [Docker Official
Images](/docker-hub/image-library/trusted-content/#docker-official-images)
and [Docker Verified Publisher
Images](/docker-hub/image-library/trusted-content/#verified-publisher-images)
follow similar best practices for minimalism and security but may not be as
stripped down to ensure compatibility with a wider range of use cases.

## What are minimal or distroless images?

Traditional container images include a full OS, often more than what is needed
to run an application. In contrast, minimal or distroless images include only:

- The application binary
- Its runtime dependencies (e.g., libc, Java, Python)
- Any explicitly required configuration or metadata

They typically exclude:

- OS tools (e.g., `ls`, `ps`, `cat`)
- Shells (e.g., `sh`, `bash`)
- Package managers (e.g., `apt`, `apk`)
- Debugging utilities (e.g., `curl`, `wget`, `strace`)

Docker Hardened Images are based on this model, ensuring a smaller and more
secure runtime surface.

## What you gain

| Benefit                | Description                                                                   |
|------------------------|-------------------------------------------------------------------------------|
| Smaller attack surface | Fewer components mean fewer vulnerabilities and less exposure to CVEs         |
| Faster startup         | Smaller image sizes result in faster pull and start times                     |
| Improved security      | Lack of shell and package manager limits what attackers can do if compromised |
| Better compliance      | Easier to audit and verify, especially with SBOMs and attestations            |

## Addressing common tradeoffs

Minimal and distroless images offer strong security benefits, but they can
change how you work with containers. Docker Hardened Images are designed to
maintain productivity while enhancing security.

| Concern           | How Docker Hardened Images help                                                                                                                                                                                         |
|-------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Debuggability | Hardened images exclude shells and CLI tools by default. Use [Docker Debug](/reference/cli/docker/debug/) to temporarily attach a debug sidecar for troubleshooting without modifying the original container. |
| Familiarity   | DHI supports multiple base images, including Alpine and Debian variants, so you can choose a familiar environment while still benefiting from hardening practices.                                                        |
| Flexibility   | Runtime immutability helps secure your containers. Use multi-stage builds and CI/CD to control changes, and optionally use dev-focused base images during development.                                                  |

By balancing minimalism with practical tooling, Docker Hardened Images support
modern development workflows without compromising on security or reliability.

## Best practices for using minimal images

- Use multi-stage builds to separate build-time and runtime environments
- Validate image behavior using CI pipelines, not interactive inspection
- Include runtime-specific dependencies explicitly in your Dockerfile
- Use Docker Scout to continuously monitor for CVEs, even in minimal images

By adopting minimal or distroless images through Docker Hardened Images, you
gain a more secure, predictable, and production-ready container environment
that's designed for automation, clarity, and reduced risk.

---

## FIPS

# FIPS

## What is FIPS 140?

[FIPS 140](https://csrc.nist.gov/publications/detail/fips/140/3/final) is a U.S.
government standard that defines security requirements for cryptographic modules
that protect sensitive information. It is widely used in regulated environments
such as government, healthcare, and financial services.

FIPS certification is managed by the [NIST Cryptographic Module Validation
Program
(CMVP)](https://csrc.nist.gov/projects/cryptographic-module-validation-program),
which ensures cryptographic modules meet rigorous security standards.

## Why FIPS compliance matters

FIPS 140 compliance is required or strongly recommended in many regulated
environments where sensitive data must be protected, such as government,
healthcare, finance, and defense. These standards ensure that cryptographic
operations are performed using vetted, trusted algorithms implemented in secure
modules.

Using software components that rely on validated cryptographic modules can help organizations:

- Satisfy federal and industry mandates, such as FedRAMP, which require or
  strongly recommend FIPS 140-validated cryptography.
- Demonstrate audit readiness, with verifiable evidence of secure,
  standards-based cryptographic practices.
- Reduce security risk, by blocking unapproved or unsafe algorithms (e.g., MD5)
  and ensuring consistent behavior across environments.

## How Docker Hardened Images support FIPS compliance

While Docker Hardened Images are available to all, the FIPS variant requires a
paid Docker Hardened Images subscription.

Docker Hardened Images (DHIs) include variants that use cryptographic modules
validated under FIPS 140. These images are intended to help organizations meet
compliance requirements by incorporating components that meet the standard.

- FIPS image variants use cryptographic modules that are already validated under
  FIPS 140.
- These variants are built and maintained by Docker to support environments with
  regulatory or compliance needs.
- Docker provides signed test attestations that document the use of validated
  cryptographic modules. These attestations can support internal audits and
  compliance reporting.
- Entropy sources (random number generation for cryptographic operations) vary
  by base image. Debian-based images use the OpenSSL entropy source, while
  Alpine-based images source entropy from the host kernel.

> [!NOTE]
>
> Using a FIPS image variant helps meet compliance requirements but does not
> make an application or system fully compliant. Compliance depends on how the
> image is integrated and used within the broader system.

## Identify images that support FIPS

Docker Hardened Images that support FIPS are marked as **FIPS** compliant
in the Docker Hardened Images catalog.

To find DHI repositories with FIPS image variants, [search the catalog](/dhi/how-to/explore/) and:

- Use the **FIPS** filter on the catalog page
- Look for **FIPS** compliant on individual image listings

These indicators help you quickly locate repositories that support FIPS-based
compliance needs. Image variants that include FIPS support will have a tag
ending with `-fips`, such as `3.13-fips`.

## Use a FIPS variant

To use a FIPS variant, you must [mirror](/dhi/how-to/mirror/) the repository
and then pull the FIPS image from your mirrored repository.

## View the FIPS attestation

The FIPS variants of Docker Hardened Images contain a FIPS attestation that
lists the actual cryptographic modules included in the image.

You can retrieve and inspect the FIPS attestation using the Docker Scout CLI:

```console
$ docker scout attest get \
  --predicate-type https://docker.com/dhi/fips/v0.1 \
  --predicate \
  dhi.io/<image>:<tag>
```

For example:

```console
$ docker scout attest get \
  --predicate-type https://docker.com/dhi/fips/v0.1 \
  --predicate \
  dhi.io/python:3.13-fips
```

The attestation output is a JSON array describing the cryptographic modules
included in the image and their compliance status. For example:

```json
[
  {
    "certification": "CMVP #4985",
    "certificationUrl": "https://csrc.nist.gov/projects/cryptographic-module-validation-program/certificate/4985",
    "name": "OpenSSL FIPS Provider",
    "package": "pkg:dhi/openssl-provider-fips@3.1.2",
    "standard": "FIPS 140-3",
    "status": "active",
    "sunsetDate": "2030-03-10",
    "version": "3.1.2"
  }
]
```

---

## glibc and musl support in Docker Hardened Images

# glibc and musl support in Docker Hardened Images

Docker Hardened Images (DHI) are built to prioritize security without
sacrificing compatibility with the broader open source and enterprise software
ecosystem. A key aspect of this compatibility is support for common Linux
standard libraries: `glibc` and `musl`.

## What are glibc and musl?

When you run Linux-based containers, the image's C library plays a key role in
how applications interact with the operating system. Most modern Linux
distributions rely on one of the following standard C libraries:

- `glibc` (GNU C Library): The standard C library on mainstream distributions
  like Debian, Ubuntu, and Red Hat Enterprise Linux. It is widely supported and
  typically considered the most compatible option across languages, frameworks,
  and enterprise software.

- `musl`: A lightweight alternative to `glibc`, commonly used in minimal
  distributions like Alpine Linux. While it offers smaller image sizes and
  performance benefits, `musl` is not always fully compatible with software that
  expects `glibc`.

## DHI compatibility

DHI images are available in both `glibc`-based (e.g., Debian) and `musl`-based
(e.g., Alpine) variants. For enterprise applications and language runtimes where
compatibility is critical, we recommend using DHI images based on glibc.

## What to choose, glibc or musl?

Docker Hardened Images are available in both glibc-based (Debian) and musl-based
(Alpine) variants, allowing you to choose the best fit for your workload.

Choose Debian-based (`glibc`) images if:

- You need broad compatibility with enterprise workloads, language runtimes, or
  proprietary software.
- You're using ecosystems like .NET, Java, or Python with native extensions that
  depend on `glibc`.
- You want to minimize the risk of runtime errors due to library
  incompatibilities.

Choose Alpine-based (`musl`) images if:

- You want a minimal footprint with smaller image sizes and reduced surface
  area.
- You're building a custom or tightly controlled application stack where
  dependencies are known and tested.
- You prioritize startup speed and lean deployments over maximum compatibility.

If you're unsure, start with a Debian-based image to ensure compatibility, and
evaluate Alpine once you're confident in your application's dependencies.

---

## Base image hardening

# Base image hardening

## What is base image hardening?

Base image hardening is the process of securing the foundational layers of a
container image by minimizing what they include and configuring them with
security-first defaults. A hardened base image removes unnecessary components,
like shells, compilers, and package managers, which limits the available attack
surface, making it more difficult for an attacker to gain control or escalate
privileges inside the container.

Hardening also involves applying best practices like running as a non-root user,
reducing writable surfaces, and ensuring consistency through immutability. While
[Docker Official
Images](/docker-hub/image-library/trusted-content/#docker-official-images)
and [Docker Verified Publisher
Images](/docker-hub/image-library/trusted-content/#verified-publisher-images)
follow best practices for security, they may not be as hardened as Docker
Hardened Images, as they are designed to support a broader range of use cases.

## Why is it important?

Most containers inherit their security posture from the base image they use. If
the base image includes unnecessary tools or runs with elevated privileges,
every container built on top of it is exposed to those risks.

Hardening the base image:

- Reduces the attack surface by removing tools and libraries that could be exploited
- Enforces least privilege by dropping root access and restricting what the container can do
- Improves reliability and consistency by avoiding runtime changes and drift
- Aligns with secure software supply chain practices and helps meet compliance standards

Using hardened base images is a critical first step in securing the software you
build and run in containers.

## What's removed and why

Hardened images typically exclude common components that are risky or unnecessary in secure production environments:

| Removed component                                | Reason                                                                           |
|--------------------------------------------------|----------------------------------------------------------------------------------|
| Shells (e.g., `sh`, `bash`)                      | Prevents users or attackers from executing arbitrary commands inside containers  |
| Package managers (e.g., `apt`, `apk`)            | Disables the ability to install software post-build, reducing drift and exposure |
| Compilers and interpreters                       | Avoids introducing tools that could be used to run or inject malicious code      |
| Debugging tools (e.g., `strace`, `curl`, `wget`) | Reduces risk of exploitation or information leakage                              |
| Unused libraries or locales                      | Shrinks image size and minimizes attack vectors                                  |

## How Docker Hardened Images apply base image hardening

Docker Hardened Images (DHIs) apply base image hardening principles by design.
Each image is constructed to include only what is necessary for its specific
purpose, whether that鈥檚 building applications (with `-dev` or `-sdk` tags) or
running them in production.

### Docker Hardened Image traits

Docker Hardened Images are built to be:

- Minimal: Only essential libraries and binaries are included
- Immutable: Images are fixed at build time鈥攏o runtime installations
- Non-root by default: Containers run as an unprivileged user unless configured otherwise
- Purpose-scoped: Different tags are available for development (`-dev`), SDK-based builds (`-sdk`), and production runtime

These characteristics help enforce consistent, secure behavior across development, testing, and production environments.

### Docker Hardened Image compatibility considerations

Because Docker Hardened Images strip out many common tools, they may not work out of the box for all use cases. You may need to:

- Use multi-stage builds to compile code or install dependencies in a `-dev` image and copy the output into a hardened runtime image
- Replace shell scripts with equivalent entrypoint binaries or explicitly include a shell if needed
- Use [Docker Debug](/reference/cli/docker/debug/) to temporarily inspect or troubleshoot containers without altering the base image

These trade-offs are intentional and help support best practices for building secure, reproducible, and production-ready containers.

---

## Immutable infrastructure

# Immutable infrastructure

Immutable infrastructure is a security and operations model where components
such as servers, containers, and images are never modified after deployment.
Instead of patching or reconfiguring live systems, you replace them entirely
with new versions.

When using Docker Hardened Images, immutability is a best practice that
reinforces the security posture of your software supply chain.

## Why immutability matters

Mutable systems are harder to secure and audit. Live patching or manual updates
introduce risks such as:

- Configuration drift
- Untracked changes
- Inconsistent environments
- Increased attack surface

Immutable infrastructure solves this by making changes only through controlled,
repeatable builds and deployments.

## How Docker Hardened Images support immutability

Docker Hardened Images are built to be minimal, locked-down, and
non-interactive, which discourages in-place modification. For example:

- Many DHI images exclude shells, package managers, and debugging tools
- DHI images are designed to be scanned and signed before deployment
- DHI users are encouraged to rebuild and redeploy images rather than patch running containers

This design aligns with immutable practices and ensures that:

- Updates go through the CI/CD pipeline
- All changes are versioned and auditable
- Systems can be rolled back or reproduced consistently

## Immutable patterns in practice

Some common patterns that leverage immutability include:

- Container replacement: Instead of logging into a container to fix a bug or
  apply a patch, rebuild the image and redeploy it.
- Infrastructure as Code (IaC): Define your infrastructure and image
  configurations in version-controlled files.
- Blue/Green or Canary deployments: Roll out new images alongside old ones and
  gradually shift traffic to the new version.

By combining immutable infrastructure principles with hardened images, you
create a predictable and secure deployment workflow that resists tampering and
minimizes long-term risk.

---

## Image provenance

# Image provenance

## What is image provenance?

Image provenance refers to metadata that traces the origin, authorship, and
integrity of a container image. It answers critical questions such as:

- Where did this image come from?
- Who built it?
- Has it been tampered with?

Provenance establishes a chain of custody, helping you verify that the image
you're using is the result of a trusted and verifiable build process.

## Why image provenance matters

Provenance is foundational to securing your software supply chain. Without it, you risk:

- Running unverified or malicious images
- Failing to meet internal or regulatory compliance requirements
- Losing visibility into the components and workflows that produce your containers

With reliable provenance, you gain:

- Trust: Know that your images are authentic and unchanged.
- Traceability: Understand the full build process and source inputs.
- Auditability: Provide verifiable evidence of compliance and build integrity.

Provenance also supports automated policy enforcement and is a key requirement
for frameworks like SLSA (Supply-chain Levels for Software Artifacts).

## How Docker Hardened Images support provenance

Docker Hardened Images (DHIs) are designed with built-in provenance to help you
adopt secure-by-default practices and meet supply chain security standards.

### Attestations

DHIs include [attestations](/dhi/core-concepts/attestations/)鈥攎achine-readable metadata that
describe how, when, and where the image was built. These are generated using
industry standards such as [in-toto](https://in-toto.io/) and align with [SLSA
provenance](https://slsa.dev/spec/v1.0/provenance/).

Attestations allow you to:

- Validate that builds followed the expected steps
- Confirm that inputs and environments meet policy
- Trace the build process across systems and stages

### Code signing

Each Docker Hardened Image is cryptographically [signed](/dhi/core-concepts/signatures/) and
stored in the registry alongside its digest. These signatures are verifiable
proofs of authenticity and are compatible with tools like `cosign`, Docker
Scout, and Kubernetes admission controllers.

With image signatures, you can:

- Confirm that the image was published by Docker
- Detect if an image has been modified or republished
- Enforce signature validation in CI/CD or production deployments

## Additional resources

- [Provenance attestations](/build/metadata/attestations/slsa-provenance/)
- [Image signatures](/dhi/core-concepts/signatures/)
- [Attestations overview](/dhi/core-concepts/attestations/)

---

## Software Bill of Materials (SBOMs)

# Software Bill of Materials (SBOMs)

## What is an SBOM?

An SBOM is a detailed inventory that lists all components, libraries, and
dependencies used in building a software application. It provides transparency
into the software supply chain by documenting each component's version, origin,
and relationship to other components. Think of it as a "recipe" for your
software, detailing every ingredient and how they come together.

Metadata included in an SBOM for describing software artifacts may include:

- Name of the artifact
- Version
- License type
- Authors
- Unique package identifier

## Why are SBOMs important?

In today's software landscape, applications often comprise numerous components
from various sources, including open-source libraries, third-party services, and
proprietary code. This complexity can obscure visibility into potential
vulnerabilities and complicate compliance efforts. SBOMs address these
challenges by providing a detailed inventory of all components within an
application.

The significance of SBOMs is underscored by several key factors:

- Enhanced transparency: SBOMs offer a comprehensive view of all components that
  constitute an application, enabling organizations to identify and assess risks
  associated with third-party libraries and dependencies.

- Proactive vulnerability management: By maintaining an up-to-date SBOM,
  organizations can swiftly identify and address vulnerabilities in software
  components, reducing the window of exposure to potential exploits.

- Regulatory compliance: Many regulations and industry standards now require
  organizations to maintain control over the software components they use. An
  SBOM facilitates compliance by providing a clear and accessible record.

- Improved incident response: In the event of a security breach, an SBOM
  enables organizations to quickly identify affected components and take
  appropriate action, minimizing potential damage.

## Docker Hardened Image SBOMs

Docker Hardened Images have SBOMs, ensuring that every component
in the image is documented and verifiable. These SBOMs are cryptographically
signed, providing a tamper-evident record of the image's contents. This
integration simplifies audits and enhances trust in the software supply chain.

## View SBOMs in Docker Hardened Images

To view the SBOM of a Docker Hardened Image, you can use the `docker scout sbom`
command. Replace `<image-name>:<tag>` with the image name and tag.

```console
$ docker scout sbom dhi.io/<image-name>:<tag>
```

## Verify the SBOM of a Docker Hardened Image

Since Docker Hardened Images come with signed SBOMs, you can use Docker Scout to
verify the authenticity and integrity of the SBOM attached to the image. This
ensures that the SBOM has not been tampered with and that the image's contents
are trustworthy.

To verify the SBOM of a Docker Hardened Image using Docker Scout, use the following command:

```console
$ docker scout attest get dhi.io/<image-name>:<tag> \
   --predicate-type https://scout.docker.com/sbom/v0.1 --verify --platform <platform>
```

For example, to verify the SBOM attestation for the `node:20.19-debian12` image:

```console
$ docker scout attest get dhi.io/node:20.19-debian12 \
   --predicate-type https://scout.docker.com/sbom/v0.1 --verify --platform linux/amd64
```

## Resources

For more details about SBOM attestations and Docker Build, see [SBOM
attestations](/build/metadata/attestations/sbom/).

To learn more about Docker Scout and working with SBOMs, see [Docker Scout SBOMs](/scout/how-tos/view-create-sboms/).

---

## Code signing

# Code signing

## What is code signing?

Code signing is the process of applying a cryptographic signature to software
artifacts, such as Docker images, to verify their integrity and authenticity. By
signing an image, you ensure that it has not been altered since it was signed
and that it originates from a trusted source.

In the context of Docker Hardened Images (DHIs), code signing is achieved using
[Cosign](https://docs.sigstore.dev/), a tool developed by the Sigstore project.
Cosign enables secure and verifiable signing of container images, enhancing
trust and security in the software supply chain.

## Why is code signing important?

Code signing plays a crucial role in modern software development and
cybersecurity:

- Authenticity: Verifies that the image was created by a trusted source.
- Integrity: Ensures that the image has not been tampered with since it was
  signed.
- Compliance: Helps meet regulatory and organizational security requirements.

## Docker Hardened Image code signing

Each DHI is cryptographically signed using Cosign, ensuring that the images have
not been tampered with and originate from a trusted source.

## Why sign your own images?

Docker Hardened Images are signed by Docker to prove their origin and integrity,
but if you're building application images that extend or use DHIs as a base, you
should sign your own images as well.

By signing your own images, you can:

- Prove the image was built by your team or pipeline
- Ensure your build hasn't been tampered with after it's pushed
- Support software supply chain frameworks like SLSA
- Enable image verification in deployment workflows

This is especially important in CI/CD environments where you build and push
images frequently, or in any scenario where image provenance must be auditable.

## How to view and use code signatures

### View signatures

You can verify that a Docker Hardened Image is signed and trusted using either Docker Scout or Cosign.

To lists all attestations, including signature metadata, attached to the image, use the following command:

```console
$ docker scout attest list <image-name>:<tag>
```

> [!NOTE]
>
> If the image exists locally on your device, you must prefix the image name with `registry://`. For example, use
> `registry://dhi.io/python` instead of `dhi.io/python`.

To verify a specific signed attestation (e.g., SBOM, VEX, provenance):

```console
$ docker scout attest get \
  --predicate-type <predicate-uri> \
  --verify \
  <image-name>:<tag>
```

> [!NOTE]
>
> If the image exists locally on your device, you must prefix the image name with `registry://`. For example, use
> `registry://dhi.io/python:3.13` instead of `dhi.io/python:3.13`.

For example:

```console
$ docker scout attest get \
  --predicate-type https://openvex.dev/ns/v0.2.0 \
  --verify \
  dhi.io/python:3.13
```

If valid, Docker Scout will confirm the signature and display signature payload, as well as the equivalent Cosign command to verify the image.

### Sign images

To sign a Docker image, use [Cosign](https://docs.sigstore.dev/). Replace
`<image-name>:<tag>` with the image name and tag.

```console
$ cosign sign <image-name>:<tag>
```

This command will prompt you to authenticate via an OIDC provider (such as
GitHub, Google, or Microsoft). Upon successful authentication, Cosign will
generate a short-lived certificate and sign the image. The signature will be
stored in a transparency log and associated with the image in the registry.

---

## Supply-chain Levels for Software Artifacts (SLSA)

# Supply-chain Levels for Software Artifacts (SLSA)

## What is SLSA?

Supply-chain Levels for Software Artifacts (SLSA) is a security framework
designed to enhance the integrity and security of software supply chains.
Developed by Google and maintained by the Open Source Security Foundation
(OpenSSF), SLSA provides a set of guidelines and best practices to prevent
tampering, improve integrity, and secure packages and infrastructure in software
projects.

SLSA defines [four build levels (0鈥�3)](https://slsa.dev/spec/latest/build-track-basics) of
increasing security rigor, focusing on areas such as build provenance, source
integrity, and build environment security. Each level builds upon the previous
one, offering a structured approach to achieving higher levels of software
supply chain security.

## Why is SLSA important?

SLSA is crucial for modern software development due to the increasing complexity
and interconnectedness of software supply chains. Supply chain attacks, such as
the SolarWinds breach, have highlighted the vulnerabilities in software
development processes. By implementing SLSA, organizations can:

- Ensure artifact integrity: Verify that software artifacts have not been
  tampered with during the build and deployment processes.

- Enhance build provenance: Maintain verifiable records of how and when software
  artifacts were produced, providing transparency and accountability.

- Secure build environments: Implement controls to protect build systems from
  unauthorized access and modifications.

- Mitigate supply chain risks: Reduce the risk of introducing vulnerabilities or
  malicious code into the software supply chain.

## What is SLSA Build Level 3?

SLSA Build Level 3, Hardened Builds, is the highest of four progressive levels in
the SLSA framework. It introduces strict requirements to ensure that software
artifacts are built securely and traceably. To meet Level 3, a build must:

- Be fully automated and scripted to prevent manual tampering
- Use a trusted build service that enforces source and builder authentication
- Generate a signed, tamper-resistant provenance record describing how the artifact was built
- Capture metadata about the build environment, source repository, and build steps

This level provides strong guarantees that the software was built from the
expected source in a controlled, auditable environment, which significantly
reduces the risk of supply chain attacks.

## Docker Hardened Images and SLSA

Docker Hardened Images (DHIs) are secure-by-default container images
purpose-built for modern production environments. Each DHI is cryptographically
signed and complies with the [SLSA Build Level 3
standard](https://slsa.dev/spec/latest/build-track-basics#build-l3), ensuring
verifiable build provenance and integrity.

By integrating SLSA-compliant DHIs into your development and deployment processes, you can:

- Achieve higher security levels: Utilize images that meet stringent security
  standards, reducing the risk of vulnerabilities and attacks.

- Simplify compliance: Leverage built-in features like signed Software Bills of
  Materials (SBOMs) and vulnerability exception (VEX) statements to facilitate
  compliance with regulations such as FedRAMP.

- Enhance transparency: Access detailed information about the components and
  build process of each image, promoting transparency and trust.

- Streamline audits: Utilize verifiable build records and signatures to simplify
  security audits and assessments.

## Get and verify SLSA provenance for Docker Hardened Images

Each Docker Hardened Image (DHI) is cryptographically signed and includes
attestations. These attestations provide verifiable build provenance and
demonstrate adherence to SLSA Build Level 3 standards.

To get and verify SLSA provenance for a DHI, you can use Docker Scout.

```console
$ docker scout attest get dhi.io/<image>:<tag> \
  --predicate-type https://slsa.dev/provenance/v0.2 \
  --verify
```

For example:

```console
$ docker scout attest get dhi.io/node:20.19-debian12 \
  --predicate-type https://slsa.dev/provenance/v0.2 \
  --verify
```

## Resources

For more details about SLSA definitions and Docker Build, see [SLSA
definitions](/build/metadata/attestations/slsa-definitions/).

---

## Software Supply Chain Security

# Software Supply Chain Security

## What is Software Supply Chain Security (SSCS)?

SSCS encompasses practices and strategies designed to safeguard the entire
lifecycle of software development from initial code creation to deployment and
maintenance. It focuses on securing all components. This includes code,
dependencies, build processes, and distribution channels in order to prevent
malicious actors from compromising the software supply chain. Given the
increasing reliance on open-source libraries and third-party components,
ensuring the integrity and security of these elements is paramount

## Why is SSCS important?

The significance of SSCS has escalated due to sophisticated cyberattacks
targeting software supply chains. High-profile supply chain attacks and the
exploitation of vulnerabilities in open-source components underscore the
critical need for robust supply chain security measures. Compromises at any
stage of the software lifecycle can lead to widespread vulnerabilities, data
breaches, and significant financial losses.

## How Docker Hardened Images contribute to SSCS

Docker Hardened Images (DHI) are purpose-built container images designed with
security at their core, addressing the challenges of modern software supply
chain security. By integrating DHI into your development and deployment
pipelines, you can enhance your organization's SSCS posture through the
following features:

- Minimal attack surface: DHIs are engineered to be ultra-minimal, stripping
  away unnecessary components and reducing the attack surface by up to 95%. This
  distroless approach minimizes potential entry points for malicious actors.

- Cryptographic signing and provenance: Each DHI is cryptographically signed,
  ensuring authenticity and integrity. Build provenance is maintained, providing
  verifiable evidence of the image's origin and build process, aligning with
  standards like SLSA (Supply-chain Levels for Software Artifacts).

- Software Bill of Materials (SBOM): DHIs include a comprehensive SBOM,
  detailing all components and dependencies within the image. This transparency
  aids in vulnerability management and compliance tracking, enabling teams to
  assess and mitigate risks effectively.

- Continuous maintenance and rapid CVE remediation: Docker maintains DHIs with
  regular updates and security patches, backed by an [SLA for addressing critical
  and high-severity vulnerabilities](https://docs.docker.com/go/dhi-sla/). This proactive approach helps ensure that
  images remain secure and compliant with enterprise standards.

---

## Secure Software Development Lifecycle

# Secure Software Development Lifecycle

## What is a Secure Software Development Lifecycle?

A Secure Software Development Lifecycle (SSDLC) integrates security practices
into every phase of software delivery, from design and development to deployment
and monitoring. It鈥檚 not just about writing secure code, but about embedding
security throughout the tools, environments, and workflows used to build and
ship software.

SSDLC practices are often guided by compliance frameworks, organizational
policies, and supply chain security standards such as SLSA (Supply-chain Levels
for Software Artifacts) or NIST SSDF.

## Why SSDLC matters

Modern applications depend on fast, iterative development, but rapid delivery
often introduces security risks if protections aren鈥檛 built in early. An SSDLC
helps:

- Prevent vulnerabilities before they reach production
- Ensure compliance through traceable and auditable workflows
- Reduce operational risk by maintaining consistent security standards
- Enable secure automation in CI/CD pipelines and cloud-native environments

By making security a first-class citizen in each stage of software delivery,
organizations can shift left and reduce both cost and complexity.

## How Docker supports a secure SDLC

Docker provides tools and secure content that make SSDLC practices easier to
adopt across the container lifecycle. With [Docker Hardened
Images](/core-concepts/) (DHIs), [Docker
Debug](/reference/cli/docker/debug/), and [Docker
Scout](/../scout/), teams can add security without losing
velocity.

### Plan and design

During planning, teams define architectural constraints, compliance goals, and
threat models. Docker Hardened Images help at this stage by providing:

- Secure-by-default base images for common languages and runtimes
- Verified metadata including SBOMs, provenance, and VEX documents
- Support for both glibc and musl across multiple Linux distributions

You can use DHI metadata and attestations to support design reviews, threat
modeling, or architecture sign-offs.

### Develop

In development, security should be transparent and easy to apply. Docker
Hardened Images support secure-by-default development:

- Dev variants include shells, package managers, and compilers for convenience
- Minimal runtime variants reduce attack surface in final images
- Multi-stage builds let you separate build-time tools from runtime environments

[Docker Debug](/reference/cli/docker/debug/) helps developers:

- Temporarily inject debugging tools into minimal containers
- Avoid modifying base images during troubleshooting
- Investigate issues securely, even in production-like environments

### Build and test

Build pipelines are an ideal place to catch issues early. Docker Scout
integrates with Docker Hub and the CLI to:

- Scan for known CVEs using multiple vulnerability databases
- Trace vulnerabilities to specific layers and dependencies
- Interpret signed VEX data to suppress known-irrelevant issues
- Export JSON scan reports for CI/CD workflows

Build pipelines that use Docker Hardened Images benefit from:

- Reproducible, signed images
- Minimal build surfaces to reduce exposure
- Built-in compliance with SLSA Build Level 3 standards

### Release and deploy

Security automation is critical as you release software at scale. Docker
supports this phase by enabling:

- Signature verification and provenance validation before deployment
- Policy enforcement gates using Docker Scout
- Safe, non-invasive container inspection using Docker Debug

DHIs ship with the metadata and signatures required to automate image
verification during deployment.

### Monitor and improve

Security continues after release. With Docker tools, you can:

- Continuously monitor image vulnerabilities through Docker Hub
- Get CVE remediation guidance and patch visibility using Docker Scout
- Receive updated DHI images with rebuilt and re-signed secure layers
- Debug running workloads with Docker Debug without modifying the image

## Summary

Docker helps teams embed security throughout the SSDLC by combining secure
content (DHIs) with developer-friendly tooling (Docker Scout and Docker Debug).
These integrations promote secure practices without introducing friction, making
it easier to adopt compliance and supply chain security across your software
delivery lifecycle.

---

## STIG

# STIG

## What is STIG?

[Security Technical Implementation Guides
(STIGs)](https://public.cyber.mil/stigs/) are configuration standards published
by the U.S. Defense Information Systems Agency (DISA). They define security
requirements for operating systems, applications, databases, and other
technologies used in U.S. Department of Defense (DoD) environments.

STIGs help ensure that systems are configured securely and consistently to
reduce vulnerabilities. They are often based on broader requirements like the
DoD's General Purpose Operating System Security Requirements Guide (GPOS SRG).

## Why STIG guidance matters

Following STIG guidance is critical for organizations that work with or support
U.S. government systems. It demonstrates alignment with DoD security standards
and helps:

- Accelerate Authority to Operate (ATO) processes for DoD systems
- Reduce the risk of misconfiguration and exploitable weaknesses
- Simplify audits and reporting through standardized baselines

Even outside of federal environments, STIGs are used by security-conscious
organizations as a benchmark for hardened system configurations.

STIGs are derived from broader NIST guidance, particularly [NIST Special
Publication 800-53](https://csrc.nist.gov/publications/sp800), which defines a
catalog of security and privacy controls for federal systems. Organizations
pursuing compliance with 800-53 or related frameworks (such as FedRAMP) can use
STIGs as implementation guides that help meet applicable control requirements.

## How Docker Hardened Images help apply STIG guidance

Docker Hardened Images (DHIs) include STIG variants that are scanned against
custom STIG-based profiles and include signed STIG scan attestations. These
attestations can support audits and compliance reporting.

While Docker Hardened Images are available to all, the STIG variant requires a
Docker subscription.

Docker creates custom STIG-based profiles for images based on the GPOS SRG and
DoD Container Hardening Process Guide. Because DISA has not published a STIG
specifically for containers, these profiles help apply STIG-like guidance to
container environments in a consistent, reviewable way and are designed to
reduce false positives common in container images.

## Identify images that include STIG scan results

Docker Hardened Images that include STIG scan results are labeled as **STIG** in
the Docker Hardened Images catalog.

To find DHI repositories with STIG image variants, [explore
images](/dhi/how-to/explore/#image-variants) and:

- Use the **STIG** filter on the catalog page
- Look for **STIG** labels on individual image listings

To find a STIG image variant within a repository, go to the **Tags** tab in the
repository, and find images labeled with **STIG** in the **Compliance** column.

## Use a STIG variant

To use a STIG variant, you must [mirror](/dhi/how-to/mirror/) the repository
and then pull the STIG image from your mirrored repository.

## View and verify STIG scan results

Docker provides a signed [STIG scan
attestation](/dhi/core-concepts/attestations/) for each STIG-ready image.
These attestations include:

- A summary of the scan results, including the number of passed, failed, and not
  applicable checks
- The name and version of the STIG profile used
- Full output in both HTML and XCCDF (XML) formats

### View STIG scan attestations

You can retrieve and inspect a STIG scan attestation using the Docker Scout CLI:

```console
$ docker scout attest get \
  --predicate-type https://docker.com/dhi/stig/v0.1 \
  --verify \
  --predicate \
  dhi.io/<image>:<tag>
```

### Extract HTML report

To extract and view the human-readable HTML report:

```console
$ docker scout attest get dhi.io/<image>:<tag> \
  --predicate-type https://docker.com/dhi/stig/v0.1 \
  --verify \
  --predicate \
  | jq -r '.[0].output[] | select(.format == "html").content | @base64d' > stig_report.html
```

### Extract XCCDF report

To extract the XML (XCCDF) report for integration with other tools:

```console
$ docker scout attest get dhi.io/<image>:<tag> \
  --predicate-type https://docker.com/dhi/stig/v0.1 \
  --verify \
  --predicate \
  | jq -r '.[0].output[] | select(.format == "xccdf").content | @base64d' > stig_report.xml
```

### View STIG scan summary

To view just the scan summary without the full reports:

```console
$ docker scout attest get dhi.io/<image>:<tag> \
  --predicate-type https://docker.com/dhi/stig/v0.1 \
  --verify \
  --predicate \
  | jq -r '.[0] | del(.output)'
```

---

## Vulnerability Exploitability eXchange (VEX)

# Vulnerability Exploitability eXchange (VEX)

## What is VEX?

Vulnerability Exploitability eXchange (VEX) is a specification for documenting
the exploitability status of vulnerabilities within software components. VEX is
primarily defined through industry standards such as CSAF (OASIS) and CycloneDX
VEX, with the U.S. Cybersecurity and Infrastructure Security Agency (CISA)
encouraging its adoption. VEX complements CVE (Common Vulnerabilities and
Exposures) identifiers by adding producer-asserted status information,
indicating whether a vulnerability is exploitable in the product as shipped.
This helps organizations prioritize remediation efforts by identifying
vulnerabilities that do not affect their specific product configurations.

For how VEX affects vulnerability counts and scanner selection, see [Scanner
integrations](/dhi/explore/scanner-integrations/). To scan a DHI with
VEX support, see [Scan Docker Hardened Images](/dhi/how-to/scan/).

## VEX status reference

Each VEX statement includes a `status` field that records Docker's
exploitability assessment for a given CVE and image. OpenVEX defines four
status values. DHI uses three of them:

| Status | Meaning |
|---|---|
| `not_affected` | The CVE was reported against a package in the image, but Docker has assessed it is not exploitable as shipped |
| `under_investigation` | Docker is aware of the CVE and is actively evaluating whether it affects the image |
| `affected` | Docker has confirmed the CVE is exploitable in the image and a fix is not yet available |
| `fixed` | The vulnerability has been remediated in this version. DHI does not use this status (see below). |

You can view the VEX statements for any DHI using Docker Scout. See [Scan Docker
Hardened Images](/dhi/how-to/scan/).

### `not_affected` justification codes

`not_affected` statements include a machine-readable `justification` field
explaining why the vulnerability does not apply:

| Justification | Meaning |
|---|---|
| `component_not_present` | The vulnerable component is not present in this image; the CVE matched by name against a different package |
| `vulnerable_code_not_present` | The vulnerable code path was not compiled into this build |
| `vulnerable_code_not_in_execute_path` | The vulnerable code exists in the package but is not called in this image's runtime configuration |
| `vulnerable_code_cannot_be_controlled_by_adversary` | The vulnerable code exists but an attacker cannot trigger it in this configuration |
| `inline_mitigations_already_exist` | Docker has applied a backport or patch that addresses the CVE |

### Why DHI does not use `fixed`

DHI does not use `fixed`. VEX-enabled scanners may not handle `fixed`
consistently, so when Docker backports an upstream patch where the version
number alone would not reflect the fix, it uses `not_affected` with
`inline_mitigations_already_exist` justification instead.

---

## Available types of Docker Hardened Images

# Available types of Docker Hardened Images

Docker Hardened Images (DHI) is a comprehensive catalog of
security-hardened container images built to meet diverse
development and production needs.

You can explore the DHI catalog on [Docker Hub](https://hub.docker.com/hardened-images/catalog) or use the [DHI CLI](/dhi/how-to/cli/) to browse
available images, tags, and metadata from the command line.

## Framework and application images

DHI includes a selection of popular frameworks and application images, each
hardened and maintained to ensure security and compliance. These images
integrate seamlessly into existing workflows, allowing developers to focus on
building applications without compromising on security.

For example, you might find repositories like the following in the DHI catalog:

- `node`: framework for Node.js applications
- `python`: framework for Python applications
- `nginx`: web server image

## Base image distributions

Docker Hardened Images are available in different base image options, giving you
flexibility to choose the best match for your environment and workload
requirements:

- Debian-based images: A good fit if you're already working in glibc-based
  environments. Debian is widely used and offers strong compatibility across
  many language ecosystems and enterprise systems.

- Alpine-based images: A smaller and more lightweight option using musl libc.
  These images tend to be small and are therefore faster to pull and have a
  reduced footprint.

Each image maintains a minimal and secure runtime layer by removing
non-essential components like shells, package managers, and debugging tools.
This helps reduce the attack surface while retaining compatibility with common
runtime environments. To maintain this lean, secure foundation, DHI standardizes
on Debian for glibc-based images, which provides broad compatibility while
minimizing complexity and maintenance overhead.

Example tags include:

- `3.9.23-alpine3.21`: Alpine-based image for Python 3.9.23
- `3.9.23-debian12`: Debian-based image for Python 3.9.23

If you're not sure which to choose, start with the base you're already familiar
with. Debian tends to offer the broadest compatibility.

## Development and runtime variants

To accommodate different stages of the application lifecycle, DHI offers all
language framework images and select application images in two variants:

- Development (dev) images: Equipped with necessary development tools and
libraries, these images facilitate the building and testing of applications in a
secure environment. They include a shell, package manager, a root user, and
other tools needed for development.

- Runtime images: Stripped of development tools, these images contain only the
essential components needed to run applications, ensuring a minimal attack
surface in production.

This separation supports multi-stage builds, enabling developers to compile code
in a secure build environment and deploy it using a lean runtime image.

For example, you might find tags like the following in a DHI repository:

- `3.9.23-debian12`: runtime image for Python 3.9.23
- `3.9.23-debian12-dev`: development image for Python 3.9.23

## FIPs and STIG variants

Some Docker Hardened Images include a `-fips` variant. These variants use
cryptographic modules that have been validated under [FIPS
140](/dhi/core-concepts/fips/), a U.S. government standard for secure
cryptographic operations.

FIPS variants are designed to help organizations meet regulatory and compliance
requirements related to cryptographic use in sensitive or regulated
environments.

You can recognize FIPS variants by their tag that includes `-fips`.

For example:
- `3.13-fips`: FIPS variant of the Python 3.13 image
- `3.9.23-debian12-fips`: FIPS variant of the Debian-based Python 3.9.23 image

FIPS variants can be used in the same way as any other Docker Hardened Image and
are ideal for teams operating in regulated industries or under compliance
frameworks that require cryptographic validation.

In addition to FIPS variants, some Docker Hardened Images also include
STIG-ready variants. These images are scanned against custom STIG-based
profiles and come with signed STIG scan attestations to support audits and
compliance reporting. To identify STIG-ready variants, look for the **STIG**
in the **Compliance** column of the image tags list in the Docker Hub catalog.

## Compatibility variants

Some Docker Hardened Images include a compatibility variant. These variants
provide additional tools and configurations for specific use cases without
bloating the minimal base images.

Compatibility variants are created to support:

- Helm chart compatibility: Applications deployed via Helm charts and
  Kubernetes that require specific runtime configurations or utilities for
  seamless integration with popular Helm charts.

- Special application use-cases: Applications that need optional tools not
  included in the minimal image.

By offering these as separate image flavors, DHI ensures that the minimal images
remain lean and secure, while providing the tools you need in dedicated
variants. This approach maintains a minimal attack surface for standard
deployments while supporting specialized requirements when needed.

You can recognize compatibility variants by their tag that includes `-compat`.

Use compatibility variants when your deployment requires additional tools beyond
the minimal runtime, such as when using Helm charts or applications with
specific tooling requirements.

## Socket Firewall variants

Some Docker Hardened Images include Socket Firewall variants. These are `dev`
variants that come with [Socket](https://socket.dev/) preinstalled to monitor
package manager activity and block malicious packages during development and CI
builds.

Two tiers are available, identified by their tag suffix:

- `-sfw-dev`: Socket Firewall Free. No API key required.
- `-sfw-ent-dev`: Socket Firewall Enterprise. Requires an API key from Socket.

Not all images offer both tiers.

## Image-specific variants

Some images include variants that go beyond the general `dev`, `compat`, and
`sfw` patterns. These represent distinct editions, bundled tooling, or
runtime configurations specific to that image. Examples include a PHP-FPM variant
for web server integration, a native binary build for faster startup, or a
specific edition of a database.

You can identify these variants by their tag suffix. The image name in the tag
suffix typically reflects what's included or different.

---

## How Docker Hardened Images are built

# How Docker Hardened Images are built

Docker Hardened Images are built through an automated pipeline that monitors
upstream sources, applies security updates, and publishes signed artifacts.
This page explains the build process for both base DHI images and customized
images available with DHI Select and DHI Enterprise subscriptions.

With DHI Select or DHI Enterprise subscriptions, the automated security update pipeline for
both base and customized images is backed by [SLA commitments](https://docs.docker.com/go/dhi-sla/), including a 7-day
SLA for critical and high severity vulnerabilities. DHI Community offers a secure baseline
but no guaranteed remediation timelines.

## Build transparency

Docker Hardened Images provide transparency into how images are built through
publicly available definitions and verifiable attestations.

### Image definitions

All image definitions are publicly available in the [catalog
repository](https://github.com/docker-hardened-images/catalog).

Each image definition is a declarative YAML specification that includes metadata,
contents, build pipeline steps, security configurations, and runtime settings.

### SLSA attestations

Every Docker Hardened Image includes a SLSA Build Level 3 attestation that
provides verifiable build provenance. For details on SLSA attestations and how to
verify them, see [SLSA](/dhi/core-concepts/slsa/).

## Build triggers

Builds start automatically. You don't trigger them manually. The system monitors
for changes and starts builds in two scenarios:

- [Upstream updates](#upstream-updates)
- [Customization changes](#customization-changes)

### Upstream updates

New releases, package updates, or CVE fixes from upstream projects trigger base
image rebuilds. These builds go through quality checks to ensure security and
reliability.

#### Monitoring for updates

Docker continuously monitors upstream projects for new releases, package
updates, and security advisories. When changes are detected, the system
automatically queues affected images for rebuild using a SLSA Build Level
3-compliant build system.

Docker uses three strategies to track updates:

- GitHub releases: Monitors specific GitHub repositories for new releases and
  automatically updates the image definition when a new version is published.
- GitHub tags: Tracks tags in GitHub repositories to detect new versions.
- Package repositories: Monitors Alpine Linux, Debian, and Ubuntu package
  repositories through Docker Scout's package database to detect updated
  packages.

In addition to explicit upstream tracking, Docker also monitors transitive
dependencies. When a package update is detected (for example, a security patch
for a library), Docker automatically identifies and rebuilds all images within
the support window that use that package.

### Customization changes

Updates to your OCI artifact customizations trigger rebuilds of your customized
images.

When you customize a DHI image with DHI Select or DHI Enterprise, your changes are packaged as
OCI artifacts that layer on top of the base image. Docker monitors your artifact
repositories and automatically rebuilds your customized images whenever you push
updates.

The rebuild process fetches the current base image, applies your OCI artifacts,
signs the result, and publishes it automatically. You don't need to manage
builds or maintain CI pipelines for your customized images.

Customized images are also rebuilt automatically when the base DHI image they
depend on receives updates, ensuring your images always include the latest
security patches.

## Build pipeline

The following sections describe the build pipeline architecture and workflow for
Docker Hardened Images based on:

- [Base image pipeline](#base-image-pipeline)
- [Customized image pipeline](#customized-image-pipeline)

### Base image pipeline

Each Docker Hardened Image is built through an automated pipeline:

1. Monitoring: Docker monitors upstream sources for updates (new releases,
   package updates, security advisories).
2. Rebuild trigger: When changes are detected, an automated rebuild starts.
3. AI guardrail: An AI system fetches upstream diffs and scans them with
   language-aware checks. The guardrail focuses on high-leverage issues that can
   cause significant problems, such as inverted error checks, ignored failures,
   resource mishandling, or suspicious contributor activity. When it spots
   potential risks, it blocks the PR from auto-merging.
4. Human review: If the AI identifies risks with high confidence,
   Docker engineers review the flagged code, reproduce the issue, and decide on
   the appropriate action. Engineers often contribute fixes back to upstream
   projects, improving the code for the entire community. When fixes are accepted
   upstream, the DHI build pipeline applies the patch immediately to protect
   customers while the fix moves through the upstream release process.
5. Testing and scanning: Images undergo comprehensive
   [testing](/dhi/explore/build-process/test/) for compatibility and functionality, and are
   [scanned for malware](/dhi/explore/build-process/malware-scanning/), secrets, and
   vulnerabilities.
6. Signing and attestations: Docker signs each image and generates
   attestations (SBOMs, VEX documents, build provenance).
7. Publishing: The signed image is published to the DHI registry and the
   attestations are published to the Docker Scout registry.
8. Cascade rebuilds: If any customized images use this base, their rebuilds
   are automatically triggered.

Docker responds quickly to critical vulnerabilities. By building essential
components from source rather than waiting for packaged updates, Docker can
patch critical and high severity CVEs within days of upstream fixes and publish
updated images with new attestations. For DHI Enterprise subscriptions, this
rapid response is backed by a [7-day SLA for critical and high severity
vulnerabilities](https://docs.docker.com/go/dhi-sla/).

The following diagram shows the base image build flow:

```goat {class="text-sm"}
.-------------------.      .-------------------.      .-------------------.      .-------------------.
| Docker monitors   |----->| Trigger rebuild   |----->| AI guardrail      |----->| Human review      |
| upstream sources  |      |                   |      | scans changes     |      |                   |
'-------------------'      '-------------------'      '-------------------'      '-------------------'
                                                                                           |
                                                                                           v
.-------------------.      .-------------------.      .-------------------.      .-------------------.
| Cascade rebuilds  |<-----| Publish to        |<-----| Sign & generate   |<-----| Testing &         |
| (if needed)       |      | DHI registry      |      | attestations      |      | scanning          |
'-------------------'      '-------------------'      '-------------------'      '-------------------'
```

### Customized image pipeline

When you customize a DHI image with DHI Select or DHI Enterprise, the build process is simplified:

1. Monitoring: Docker monitors your OCI artifact repositories for changes.
2. Rebuild trigger: When you push updates to your OCI artifacts, or when the base
   DHI image is updated, an automated rebuild starts.
3. Fetch base image: The latest base DHI image is fetched.
4. Apply customizations: Your OCI artifacts are applied to the base image.
5. Scanning: The customized image is [scanned for
   malware](/dhi/explore/build-process/malware-scanning/), secrets, and vulnerabilities.
6. Signing and attestations: Docker signs the customized image and generates
   attestations (SBOMs, VEX documents, build provenance).
7. Publishing: The signed customized image is published to Docker Hub and the
   attestations are published to the Docker Scout registry.

Docker handles the entire process automatically, so you don't need to manage
builds for your customized images. However, you're responsible for testing your
customized images and managing any CVEs introduced by your OCI artifacts.

The following diagram shows the customized image build flow:

```goat {class="text-sm"}
.-------------------.      .-------------------.      .-------------------.      .-------------------.
| Docker monitors   |----->| Trigger rebuild   |----->| Fetch base        |----->| Apply             |
| OCI artifacts     |      |                   |      | DHI image         |      | customizations    |
'-------------------'      '-------------------'      '-------------------'      '-------------------'
                                                                                           |
                                                                                           v
                           .-------------------.      .-------------------.      .-------------------.
                           | Publish to        |<-----| Sign & generate   |<-----| Scanning          |
                           | Docker Hub        |      | attestations      |      |                   |
                           '-------------------'      '-------------------'      '-------------------'
```

---

## Give feedback

# Give feedback

Committed to maintaining the quality, security, and reliability of the Docker Hardened Images (DHI)
a repository has been created as a point of contact to encourage the community to collaborate
in improving the Hardened Images ecosystem.

## Questions or discussions

You can use the [GitHub Discussions
board](https://github.com/orgs/docker-hardened-images/discussions) to engage
with the DHI team for:

- General questions about DHIs
- Best practices and recommendations
- Security tips and advice
- Show and tell your implementations
- Community announcements

## Reporting bugs or issues

You can [open a new issue](https://github.com/docker-hardened-images/catalog/issues) for topics such as:

- Bug reports
- Feature requests
- Documentation improvements
- Security vulnerabilities (see security policy)

It's encouraged to first search existing issues to see if it鈥檚 already been reported.
The DHI team reviews reports regularly and appreciates clear, actionable feedback.

## Responsible security disclosure

It is forbidden to post details of vulnerabilities before coordinated disclosure and resolution.

If you discover a security vulnerability, report it responsibly by following Docker鈥檚 [security disclosure](https://www.docker.com/trust/vulnerability-disclosure-policy/).

---
