---
title: "Docker 企业部署"
source: "https://docs.docker.com/enterprise/"
version: "latest"
---

# Docker 企业部署

> 原始文档来源：https://docs.docker.com/enterprise/

---

## Docker Desktop in Microsoft Dev Box

# Docker Desktop in Microsoft Dev Box

Docker Desktop is available as a pre-configured image in the Microsoft Azure Marketplace for use with Microsoft Dev Box, allowing developers to quickly set up consistent development environments in the cloud.

Microsoft Dev Box provides cloud-based, pre-configured developer workstations that allow you to code, build, and test applications without configuring a local development environment. The Docker Desktop image for Microsoft Dev Box comes with Docker Desktop and its dependencies pre-installed, giving you a ready-to-use containerized development environment.

## Key benefits

- Docker Desktop, WSL2, and dependencies pre-installed
- Identical environment for every team member
- More compute and storage than a typical local machine
- Session state persists between uses
- Works with your existing Docker subscription

## Setup

### Prerequisites

- An Azure subscription
- Access to Microsoft Dev Box
- A Docker subscription (Pro, Team, or Business). You can use Docker Desktop in Microsoft Dev Box with any of the following subscription options:
  - An existing or new Docker subscription
  - A new Docker subscription purchased through Azure Marketplace
  - A Docker Business subscription with SSO configured for your organization

### Set up Docker Desktop in Dev Box

1. Navigate to the [Docker Desktop for Microsoft Dev Box](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/dockerinc1694120899427.devbox_azuremachine?tab=Overview) listing in Azure Marketplace.
2. Select **Get It Now** to add the virtual machine image to your subscription.
3. Follow the Azure workflow to complete the setup.
4. Use the image to create VMs, assign to Dev Centers, or create Dev Box Pools according to your organization's setup.

### Activate Docker Desktop

Once your Dev Box is provisioned with the Docker Desktop image:

1. Start your Dev Box instance.
2. Launch Docker Desktop.
3. Sign in with your Docker ID.

## Support

For issues related to:

- Docker Desktop configuration, usage, or licensing: Create a support ticket through [Docker Support](https://hub.docker.com/support).
- Dev Box creation, Azure portal configuration, or networking: Contact Azure Support.

## Limitations

- Microsoft Dev Box is only available on Windows 10 and 11 (Linux VMs are not supported).
- Performance may vary based on your Dev Box configuration and network conditions.

---

## Enterprise deployment FAQs

# Enterprise deployment FAQs

## MSI

Common questions about installing Docker Desktop using the MSI installer.

### What happens to user data if they have an older Docker Desktop installation (i.e. `.exe`)?

Users must [uninstall](/desktop/uninstall/) older `.exe` installations before using the new MSI version. The `.exe` installer includes a `-keep-data` flag that removes Docker Desktop while preserving underlying resources such as the container VMs:

```powershell
# For all-user installations
& 'C:\Program Files\Docker\Docker\Docker Desktop Installer.exe' uninstall -keep-data

# For per-user installations
& '%LOCALAPPDATA%\Programs\DockerDesktop\Docker Desktop Installer.exe' uninstall -keep-data

```

### What happens if the user's machine has an older `.exe` installation?

The MSI installer detects older `.exe` installations and blocks the installation until the previous version is uninstalled. It prompts the user to uninstall their current/old version first, before retrying to install the MSI version.

### My installation failed, how do I find out what happened?

MSI installations may fail silently, offering little diagnostic feedback.

To debug a failed installation, run the install again with verbose logging enabled:

```powershell
msiexec /i "DockerDesktop.msi" /L*V ".\msi.log"
```

After the installation has failed, open the log file and search for occurrences of `value 3`. This is the exit code Windows Installer outputs when it has failed. Just above the line, you will find the reason for the failure.

### Why does the installer prompt for a reboot at the end of every fresh installation?

The installer prompts for a reboot because it assumes that changes have been made to the system that require a reboot to finish their configuration.

For example, if you select the WSL engine, the installer adds the required Windows features. After these features are installed, the system reboots to complete configurations so the WSL engine is functional.

You can suppress reboots by using the `/norestart` option when launching the installer from the command line:

```powershell
msiexec /i "DockerDesktop.msi" /L*V ".\msi.log" /norestart
```

### Why isn't the `docker-users` group populated when the MSI is installed with Intune or another MDM solution?

It's common for MDM solutions to install applications in the context of the system account. This means that the `docker-users` group isn't populated with the user's account, as the system account doesn't have access to the user's context.

As an example, you can reproduce this by running the installer with `psexec` in an elevated command prompt:

```powershell
psexec -i -s msiexec /i "DockerDesktop.msi"
```
The installation should complete successfully, but the `docker-users` group won't be populated.

As a workaround, you can create a script that runs in the context of the user account. 

The script would be responsible for creating the `docker-users` group and populating it with the correct user.

Here's an example script that creates the `docker-users` group and adds the current user to it (requirements may vary depending on environment):

```powershell
$Group = "docker-users"
$CurrentUser = [System.Security.Principal.WindowsIdentity]::GetCurrent().Name

# Create the group
New-LocalGroup -Name $Group

# Add the user to the group
Add-LocalGroupMember -Group $Group -Member $CurrentUser
```

> [!NOTE]
>
> After adding a new user to the `docker-users` group, the user must sign out and then sign back in for the changes to take effect.

## MDM

Common questions about deploying Docker Desktop using mobile device management
(MDM) tools such as Jamf, Intune, or Workspace ONE.

### Why doesn't my MDM tool apply all Docker Desktop configuration settings at once?

Some MDM tools, such as Workspace ONE, may not support applying multiple
configuration settings in a single XML file. In these cases, you may need to
deploy each setting in a separate XML file.

Refer to your MDM provider's documentation for specific deployment
requirements or limitations.

---

## Install Docker Desktop from the Microsoft Store on Windows

# Install Docker Desktop from the Microsoft Store on Windows

You can deploy Docker Desktop for Windows through the [Microsoft app store](https://apps.microsoft.com/detail/xp8cbj40xlbwkx?hl=en-GB&gl=GB).

The Microsoft Store version of Docker Desktop provides the same functionality as the standard installer but has a different update behavior depending on whether your developers install it themselves or if installation is handled by an MDM tool such as Intune. This is described in the following section. 

Choose the installation method that best aligns with your environment's requirements and management practices.

## Update behavior

### Developer-managed installations

For developers who install Docker Desktop directly:

- The Microsoft Store does not automatically update Win32 apps like Docker Desktop for most users.
- Only a subset of users (approximately 20%) may receive update notifications on the Microsoft Store page.
- Most users must manually check for and apply updates within the Store.

### Intune-managed installations

In environments managed with Intune:
- Intune checks for updates approximately every 8 hours.
- When a new version is detected, Intune triggers a `winget` upgrade.  
- If appropriate policies are configured, updates can occur automatically without user intervention. 
- Updates are handled by Intune's management infrastructure rather than the Microsoft Store itself.

## WSL considerations

Docker Desktop for Windows integrates closely with WSL. When updating Docker Desktop installed from the Microsoft Store:
- Make sure you have quit Docker Desktop and that it is no longer running so updates can complete successfully
- In some environments, virtual hard disk (VHDX) file locks may prevent the update from completing.

## Recommendations for Intune management

If using Intune to manage Docker Desktop for Windows:
- Ensure your Intune policies are configured to handle application updates
- Be aware that the update process uses WinGet APIs rather than direct Store mechanisms
- Consider testing the update process in a controlled environment to verify proper functionality

---

## MSI installer

# MSI installer

The MSI package supports various MDM (Mobile Device Management) solutions, making it ideal for bulk installations and eliminating the need for manual setups by individual users. With this package, IT administrators can ensure standardized, policy-driven installations of Docker Desktop, enhancing efficiency and software management across their organizations.

## Install interactively

1. In [Docker Home](http://app.docker.com), choose your organization.
2. Select **Admin Console**, then **Enterprise deployment**.
3. From the **Windows OS** tab, select the **Download MSI installer** button.
4. Once downloaded, double-click `Docker Desktop Installer.msi` to run the installer.
5. After accepting the license agreement, choose the install location. By default, Docker Desktop is installed at `C:\Program Files\Docker\Docker`(all-user installations) or `%LOCALAPPDATA%\Programs\DockerDesktop` (per-user installations)
6. Configure the Docker Desktop installation. You can:
   - Create a desktop shortcut

   - Set the Docker Desktop service startup type to automatic

   - Disable Windows Container usage

   - Select the Docker Desktop backend: WSL or Hyper-V. If only one is supported by your system, you won't be able to choose.

7. Follow the instructions on the installation wizard to authorize the installer and proceed with the install.
8. When the installation is successful, select **Finish** to complete the installation process.

If your administrator account is different from your user account, you must add the user to the **docker-users** group to access features that require higher privileges, such as creating and managing the Hyper-V VM, or using Windows containers:

1. Run **Computer Management** as an **administrator**.
2. Navigate to **Local Users and Groups** > **Groups** > **docker-users**.
3. Right-click to add the user to the group.
4. Sign out and sign back in for the changes to take effect.

> [!NOTE]
>
> When installing Docker Desktop with the MSI, in-app updates are automatically disabled by default. This ensures organizations can maintain version consistency and prevent unapproved updates.
> Starting with Docker Desktop version 4.60 and later, in-app updates from an MSI installation can be enabled by changing the `disableUpdate` setting to `false` through [Settings Management](/enterprise/security/hardened-desktop/settings-management).
>
> Docker Desktop notifies you when an update is available. To update Docker Desktop, download the latest installer from the Docker Admin Console. Navigate to the **Enterprise deployment** page.
>
> To keep up to date with new releases, check the [release notes](/desktop/release-notes/) page.

## Install from the command line

This section covers command line installations of Docker Desktop using PowerShell. It provides common installation commands that you can run. You can also add additional arguments which are outlined in [configuration options](#configuration-options).

When installing Docker Desktop, you can choose between interactive or non-interactive installations.

Interactive installations, without specifying `/quiet` or `/qn`, display the user interface and let you select your own properties.

When installing via the user interface it's possible to:

- Choose the destination folder
- Create a desktop shortcut
- Configure the Docker Desktop service startup type
- Disable Windows Containers
- Choose between the WSL or Hyper-V engine

Non-interactive installations are silent and any additional configuration must be passed as arguments.

### Common installation commands

Admin rights are required to run any of the following commands.

#### Install interactively with verbose logging

```powershell
msiexec /i "DockerDesktop.msi" /L*V ".\msi.log"
```

#### Install interactively without verbose logging

```powershell
msiexec /i "DockerDesktop.msi"
```

#### Install non-interactively with verbose logging

```powershell
msiexec /i "DockerDesktop.msi" /L*V ".\msi.log" /quiet
```

#### Install non-interactively and suppressing reboots

```powershell
msiexec /i "DockerDesktop.msi" /L*V ".\msi.log" /quiet /norestart
```

#### Install non-interactively with admin settings

```powershell
msiexec /i "DockerDesktop.msi" /L*V ".\msi.log" /quiet /norestart ADMINSETTINGS="{""configurationFileVersion"":2,""enhancedContainerIsolation"":{""value"":true,""locked"":false}}" ALLOWEDORG="your-organization"
```

#### Install interactively and allow users to switch to Windows containers without admin rights

```powershell
msiexec /i "DockerDesktop.msi" /L*V ".\msi.log" /quiet /norestart ALLOWEDORG="your-organization" ALWAYSRUNSERVICE=1
```

#### Install interactively specifying a PAC file

```powershell
PowerShell
 msiexec --% /i "DockerDesktop.msi" /L*V ".\msi.log"  PROXYHTTPMODE="manual" OVERRIDEPROXYPAC="http://localhost:8080/myproxy.pac"
```

#### Install interactively specifying a PAC script

```powershell
PowerShell
 msiexec --% /i "DockerDesktop.msi" /L*V ".\msi.log"  PROXYHTTPMODE="manual" OVERRIDEPROXYEMBEDDEDPAC="function FindProxyForURL(url,host) {return ""DIRECT"" ;; }"
```

#### Install with the passive display option

You can use the `/passive` display option instead of `/quiet` when you want to perform a non-interactive installation but show a progress dialog.

In passive mode the installer doesn't display any prompts or error messages to the user and the installation cannot be cancelled.

For example:

```powershell
msiexec /i "DockerDesktop.msi" /L*V ".\msi.log" /passive /norestart
```

> [!TIP]
>
> When creating a value that expects a JSON string:
>
> - The property expects a JSON formatted string
> - The string should be wrapped in double quotes
> - The string shouldn't contain any whitespace
> - Property names are expected to be in double quotes

### Common uninstall commands

When uninstalling Docker Desktop, you need to use the same `.msi` file that was originally used to install the application.

If you no longer have the original `.msi` file, you need to use the product code associated with the installation. To find the product code, run:

```powershell
Get-WmiObject Win32_Product | Select-Object IdentifyingNumber, Name | Where-Object {$_.Name -eq "Docker Desktop"}
```

It should return output similar to the following:

```text
IdentifyingNumber                      Name
-----------------                      ----
{10FC87E2-9145-4D7D-B493-2E99E8D8E103} Docker Desktop
```

> [!NOTE]
>
> This command may take some time, depending on the number of installed applications.

`IdentifyingNumber` is the applications product code and can be used to uninstall Docker Desktop. For example:

```powershell
msiexec /x {10FC87E2-9145-4D7D-B493-2E99E8D8E103} /L*V ".\msi.log" /quiet
```

#### Uninstall interactively with verbose logging

```powershell
msiexec /x "DockerDesktop.msi" /L*V ".\msi.log"
```

#### Uninstall interactively without verbose logging

```powershell
msiexec /x "DockerDesktop.msi"
```

#### Uninstall non-interactively with verbose logging

```powershell
msiexec /x "DockerDesktop.msi" /L*V ".\msi.log" /quiet
```

#### Uninstall non-interactively without verbose logging

```powershell
msiexec /x "DockerDesktop.msi" /quiet
```

### Configuration options

In addition to the following custom properties, the Docker Desktop MSI installer also supports the standard [Windows Installer command line options](https://learn.microsoft.com/en-us/windows/win32/msi/standard-installer-command-line-options).

| Property                           | Description                                                                                                                                                                                                                                                                                   | Default                 |
| :--------------------------------- | :-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | :---------------------- |
| `ENABLEDESKTOPSHORTCUT`            | Creates a desktop shortcut.                                                                                                                                                                                                                                                                   | 1                       |
| `INSTALLFOLDER`                    | Specifies a custom location where Docker Desktop will be installed.                                                                                                                                                                                                                           | C:\Program Files\Docker |
| `ADMINSETTINGS`                    | Automatically creates an `admin-settings.json` file which is used to [control certain Docker Desktop settings](/enterprise/security/hardened-desktop/settings-management/) on client machines within organizations. It must be used together with the `ALLOWEDORG` property. | None                    |
| `ALLOWEDORG`                       | Requires the user to sign in and be part of the specified Docker Hub organization when running the application. This creates a registry key called `allowedOrgs` in `HKLM\Software\Policies\Docker\Docker Desktop`.                                                                           | None                    |
| `ALWAYSRUNSERVICE`                 | Lets users switch to Windows containers without needing admin rights                                                                                                                                                                                                                          | 0                       |
| `DISABLEWINDOWSCONTAINERS`         | Disables the Windows containers integration                                                                                                                                                                                                                                                   | 0                       |
| `ENGINE`                           | Sets the Docker Engine that's used to run containers. This can be either `wsl` , `hyperv`, or `windows`                                                                                                                                                                                       | `wsl`                   |
| `PROXYENABLEKERBEROSNTLM`          | When set to 1, enables support for Kerberos and NTLM proxy authentication.                                                                                                                                                                                                                    | 0                       |
| `PROXYHTTPMODE`                    | Sets the HTTP Proxy mode. This can be either `system` or `manual`                                                                                                                                                                                                                             | `system`                |
| `OVERRIDEPROXYHTTP`                | Sets the URL of the HTTP proxy that must be used for outgoing HTTP requests.                                                                                                                                                                                                                  | None                    |
| `OVERRIDEPROXYHTTPS`               | Sets the URL of the HTTP proxy that must be used for outgoing HTTPS requests.                                                                                                                                                                                                                 | None                    |
| `OVERRIDEPROXYEXCLUDE`             | Bypasses proxy settings for the hosts and domains. Uses a comma-separated list.                                                                                                                                                                                                               | None                    |
| `OVERRIDEPROXYPAC`                 | Sets the PAC file URL. This setting takes effect only when using `manual` proxy mode.                                                                                                                                                                                                         | None                    |
| `OVERRIDEPROXYEMBEDDEDPAC`         | Specifies an embedded PAC (Proxy Auto-config) script. This setting takes effect only when using `manual` proxy mode and has precedence over the `OVERRIDEPROXYPAC` flag.                                                                                                                      | None                    |
| `HYPERVDEFAULTDATAROOT`            | Specifies the default location for the Hyper-V VM disk.                                                                                                                                                                                                                                       | None                    |
| `WINDOWSCONTAINERSDEFAULTDATAROOT` | Specifies the default location for Windows containers.                                                                                                                                                                                                                                        | None                    |
| `WSLDEFAULTDATAROOT`               | Specifies the default location for the WSL distribution disk.                                                                                                                                                                                                                                 | None                    |
| `DISABLEANALYTICS`                 | When set to 1, analytics collection will be disabled for the MSI. For more information, see [Analytics](#analytics).                                                                                                                                                                          | 0                       |
| `REMOVEEXISTINGINSTALL`            | When set to 1, any existing EXE installations are removed. Existing settings and content are preserved. Available with Docker Desktop version 4.61 and later.   | 1 |

Additionally, you can also use `/norestart` or `/forcerestart` to control reboot behaviour.

By default, the installer reboots the machine after a successful installation. When run silently, the reboot is automatic and the user is not prompted.

## Analytics

The MSI installer collects anonymous usage statistics relating to installation only. This is to better understand user behaviour and to improve the user experience by identifying and addressing issues or optimizing popular features.

### How to opt-out

**From the GUI**

When you install Docker Desktop from the default installer GUI, select the **Disable analytics** checkbox located on the bottom-left corner of the **Welcome** dialog.

**From the command line**

When you install Docker Desktop from the command line, use the `DISABLEANALYTICS` property.

```powershell
msiexec /i "win\msi\bin\en-US\DockerDesktop.msi" /L*V ".\msi.log" DISABLEANALYTICS=1
```

### Persistence

If you decide to disable analytics for an installation, your choice is persisted in the registry and honoured across future upgrades and uninstalls.

However, the key is removed when Docker Desktop is uninstalled and must be configured again via one of the previous methods.

The registry key is as follows:

```powershell
SOFTWARE\Docker Inc.\Docker Desktop\DisableMsiAnalytics
```

When analytics is disabled, this key is set to `1`.

## Additional resources

- [Explore the FAQs](/enterprise/enterprise-deployment/faq/)

---

## PKG installer

# PKG installer

The PKG package supports various MDM (Mobile Device Management) solutions, making it ideal for bulk installations and eliminating the need for manual setups by individual users. With this package, IT administrators can ensure standardized, policy-driven installations of Docker Desktop, enhancing efficiency and software management across their organizations.

## Install interactively

1. In [Docker Home](http://app.docker.com), choose your organization.
2. Select **Admin Console**, then **Enterprise deployment**.
3. From the **macOS** tab, select the **Download PKG installer** button.
4. Once downloaded, double-click `Docker.pkg` to run the installer.
5. Follow the instructions on the installation wizard to authorize the installer and proceed with the installation.
   - **Introduction**: Select **Continue**.
   - **License**: Review the license agreement and select **Agree**.
   - **Destination Select**: This step is optional. It is recommended that you keep the default installation destination (usually `Macintosh HD`). Select **Continue**.
   - **Installation Type**: Select **Install**.
   - **Installation**: Authenticate using your administrator password or Touch ID.
   - **Summary**: When the installation completes, select **Close**.

> [!NOTE]
>
> When installing Docker Desktop with the PKG, in-app updates are automatically disabled. This ensures organizations can maintain version consistency and prevent unapproved updates. For Docker Desktop installed with the `.dmg` installer, in-app updates remain supported.
>
> Docker Desktop notifies you when an update is available. To update Docker Desktop, download the latest installer from the Docker Admin Console. Navigate to the **Enterprise deployment** page.
>
> To keep up to date with new releases, check the [release notes](/desktop/release-notes/) page.

## Install from the command line

1. In [Docker Home](http://app.docker.com), choose your organization.
2. Select **Admin Console**, then **Enterprise deployment**.
3. From the **macOS** tab, select the **Download PKG installer** button.
4. From your terminal, run the following command:

   ```console
   $ sudo installer -pkg "/path/to/Docker.pkg" -target /Applications
   ```

## Additional resources

- See how you can deploy Docker Desktop for Mac using [Intune](/enterprise/enterprise-deployment/pkg-install-and-configure/use-intune/) or [Jamf Pro](/enterprise/enterprise-deployment/pkg-install-and-configure/use-jamf-pro/)
- Explore how to [Enforce sign-in](/enterprise/security/enforce-sign-in/methods/#plist-method-mac-only) for your users.

---

## Deploy with Intune

# Deploy with Intune

Learn how to deploy Docker Desktop on Windows and macOS devices using Microsoft Intune. It covers app creation, installer configuration, and assignment to users or devices.

**Windows**

1. Sign in to your Intune admin center.
2. Add a new app. Select **Apps**, then **Windows**, then **Add**.
3. For the app type, select **Windows app (Win32)**
4. Select the `intunewin` package. 
5. Fill in the required details, such as the description, publisher, or app version and then select **Next**. 
6. Optional: On the **Program** tab, you can update the **Install command** field to suit your needs. The field is pre-populated with `msiexec /i "DockerDesktop.msi" /qn`. See the [Common installation scenarios](/enterprise/enterprise-deployment/use-intune/msi-install-and-configure/) for examples on the changes you can make. 

   > [!TIP]
   >
   > It's recommended you configure the Intune deployment to schedule a reboot of the machine on successful installs.
   >
   > This is because the Docker Desktop installer installs Windows features depending on your engine selection and also updates the membership of the `docker-users` local group.
   >
   > You may also want to set Intune to determine behaviour based on return codes and watch for a return code of `3010`. Return code 3010 means the installation succeeded but a reboot is required.

7. Complete the remaining tabs, then review and create the app. 

**Mac**

First, upload the package:

1. Sign in to your Intune admin center.
2. Add a new app. Select **Apps**, then **macOS**, then **Add**.
3. Select **Line-of-business app** and then **Select**.
4. Upload the `Docker.pkg` file and fill in the required details.

Next, assign the app:

1. Once the app is added, navigate to **Assignments** in Intune.
2. Select **Add group** and choose the user or device groups you want to assign the app to.
3. Select **Save**.

## Additional resources

- [Explore the FAQs](/enterprise/enterprise-deployment/use-intune/faq/).
- Learn how to [enforce sign-in](/enterprise/security/enforce-sign-in/) for your users.

---

## Deploy with Jamf Pro

# Deploy with Jamf Pro

Learn how to deploy Docker Desktop for Mac using Jamf Pro, including uploading the installer and creating a deployment policy.

First, upload the package:

1. From the Jamf Pro console, navigate to **Computers** > **Management Settings** > **Computer Management** > **Packages**.
2. Select **New** to add a new package.
3. Upload the `Docker.pkg` file.

Next, create a policy for deployment:

1. Navigate to **Computers** > **Policies**.
2. Select **New** to create a new policy.
3. Enter a name for the policy, for example "Deploy Docker Desktop".
4. Under the **Packages** tab, add the Docker package you uploaded.
5. Configure the scope to target the devices or device groups on which you want to install Docker.
6. Save the policy and deploy.

For more information, see [Jamf Pro's official documentation](https://learn.jamf.com/en-US/bundle/jamf-pro-documentation-current/page/Policies.html). 

## Additional resources

- Learn how to [enforce sign-in](/enterprise/security/enforce-sign-in/) for your users.

---

## Organization access tokens

# Organization access tokens

Organization access tokens (OATs) provide secure, programmatic access to Docker Hub for automated systems, CI/CD pipelines, and other business-critical tasks. Unlike personal access tokens tied to individual users, OATs are associated with your organization and can be managed by any organization owner.

> [!WARNING]
>
> Organization access tokens are incompatible with Docker Desktop and Image Access Management. If you use these features, use [personal access tokens](/security/access-tokens/) instead.

## Who should use organization access tokens?

Use OATs for automated systems that need Docker Hub access without depending on individual user accounts:

- CI/CD pipelines: Build and deployment systems that push and pull images
- Production systems: Applications that pull images during deployment
- Monitoring tools: Systems that need to check repository status or pull images
- Backup systems: Tools that periodically pull images for archival
- Integration services: Third-party tools that integrate with your Docker Hub repositories

## Key benefits

Benefits of using organization access tokens include:

- Organizational ownership: Not tied to individual users who might leave the company
- Shared management: All organization owners can create and manage OATs
- Separate usage limits: OATs have their own Docker Hub rate limits, not counting against personal accounts
- Better security audit: Track when tokens were last used and identify suspicious activity
- Granular permissions: Limit access to specific repositories and operations

## Prerequisites

To create and use organization access tokens, you must have:

- A Docker Team or Business subscription
- Owner permissions
- Repositories you want to grant access to

## Create an organization access token

Owners can create tokens with these limits:

- Team subscription: Up to 10 OATs per organization
- Business subscription: Up to 100 OATs per organization

Expired tokens count toward your total limit.

To create an OAT:

1. Sign in to [Docker Home](https://app.docker.com/) and select your
organization.
1. Select **Admin Console**, then **Access tokens**.
1. Select **Generate access token**.
1. Configure token details:
    - Label: Descriptive name indicating the token's purpose
    - Description (optional): Additional details
    - Expiration date: When the token should expire
1. Expand the **Repository** drop-down to set access permissions:
    1. Optional. Select **Read public repositories** for access to public repositories.
    1. Select **Add repository** and choose a repository from the drop-down.
    1. Set permissions for each repository: **Image Pull** or **Image Push**.
    1. Add up to 50 repositories as needed.
1. Optional. Configure organization management permissions by expanding the **Organization** drop-down and selecting the **Allow management access to this organization's resources**:
    - **Member Edit**: Edit members of the organization
    - **Member Read**: Read members of the organization
    - **Invite Edit**: Invite members to the organization
    - **Invite Read**: Read invites to the organization
    - **Group Edit**: Edit groups of the organization
    - **Group Read**: Read groups of the organization
1. Select **Generate token**. Copy the token that appears on the screen and save it. You won't be able to retrieve the token once you exit the screen.

> [!IMPORTANT]
>
> Treat organization access tokens like passwords. Store them securely in a credential manager and never commit them to source code repositories.

## Use organization access tokens

Sign in to the Docker CLI using your organization access token:

```console
$ docker login --username <YOUR_ORGANIZATION_NAME>
Password: [paste your OAT here]
```

When prompted for a password, enter your organization access token.

## Modify existing tokens

To manage existing tokens:

1. Sign in to [Docker Home](https://app.docker.com/) and select your
organization.
1. Select **Admin Console**, then **Access tokens**.
1. Select the actions menu in the token row, you can:
    - **Edit**
    - **Deactivate**
    - **Delete**
1. Select **Save** after making changes to a token.

## Organization access token best practices

- Regular token rotation: Set reasonable expiration dates and rotate tokens regularly to minimize security risks.
- Principle of least privilege: Grant only the minimum repository access and permissions needed for each use case.
- Monitor token usage: Regularly review when tokens were last used to identify unused or suspicious tokens.
- Secure storage: Store tokens in secure credential management systems, never in plain text or source code.
- Immediate revocation: Deactivate or delete tokens immediately if they're compromised or no longer needed.

---

## Add and manage domains

# Add and manage domains

Domain management lets you add and verify domains for your organization, then enable auto-provisioning to automatically add users when they sign in with email addresses that match your verified domains. This approach simplifies user management, ensures consistent security settings, and reduces the risk of unmanaged users accessing Docker without visibility or control.

This page provides steps to add and delete domains, configure auto-provisioning, and audit uncaptured users.

## Add and verify a domain

Adding a domain requires verification to confirm ownership. The verification process uses DNS records to prove you control the domain.

### Add a domain

1. Sign in to [Docker Home](https://app.docker.com) and select
   your organization. If your organization is part of a company, select the company
   and configure the domain for the organization at the company level.
1. Select **Admin Console**, then **Domain management**.
1. Select **Add a domain**.
1. Enter your domain and select **Add domain**.
1. In the pop-up modal, copy the **TXT Record Value** to verify your domain.

### Verify a domain

Verification confirms that you own the domain by adding a TXT record to your Domain Name System (DNS) host. It can take up to 72 hours for the DNS change to propagate. Docker automatically checks for the record and confirms ownership once the change is recognized.

> [!TIP]
>
> The record name field determines where the TXT record is added in your domain (root or subdomain). For root domains like `example.com`, use `@` or leave the record name empty, depending on your provider. Don't enter values like docker, `docker-verification`, `www`, or your domain name, as these may direct to the wrong place. Check your DNS provider's documentation to verify record name requirements.

Follow the steps for your DNS provider to add the **TXT Record Value**. If
your provider isn't listed, use the steps for "Other providers":

**AWS Route 53**

1. Add your TXT record to AWS by following [Creating records by using the Amazon Route 53 console](https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/resource-record-sets-creating.html).
1. Wait up to 72 hours for TXT record verification.
1. Return to the **Domain management** page of the
   [Admin Console](https://app.docker.com/admin) and select **Verify** next to
   your domain name.

**Google Cloud DNS**

1. Add your TXT record to Google Cloud DNS by following [Verifying your domain with a TXT record](https://cloud.google.com/identity/docs/verify-domain-txt).
1. Wait up to 72 hours for TXT record verification.
1. Return to the **Domain management** page of the
   [Admin Console](https://app.docker.com/admin) and select **Verify** next to
   your domain name.

**GoDaddy**

1. Add your TXT record to GoDaddy by following [Add a TXT record](https://www.godaddy.com/help/add-a-txt-record-19232).
1. Wait up to 72 hours for TXT record verification.
1. Return to the **Domain management** page of the
   [Admin Console](https://app.docker.com/admin) and select **Verify** next to
   your domain name.

**Other providers**

1. Sign in to your domain host.
1. Add a TXT record to your DNS settings using the **TXT Record Value** from Docker.
1. Wait up to 72 hours for TXT record verification.
1. Return to the **Domain management** page of the
   [Admin Console](https://app.docker.com/admin) and select **Verify** next to
   your domain name.

## Audit domains for uncaptured users

Domain audit identifies uncaptured users. Uncaptured users are Docker users who have authenticated using an email address associated with your verified domains but aren't members of your Docker organization.

### Limitations

Domain audit can't identify:

- Users who access Docker Desktop without authenticating
- Users who authenticate using an account that doesn't have an
  email address associated with one of your verified domains

To prevent unidentifiable users from accessing Docker Desktop, [enforce sign-in](/enterprise/security/enforce-sign-in/).

### Run a domain audit

1. Sign in to [Docker Home](https://app.docker.com) and choose your
   company.
1. Select **Admin Console**, then **Domain management**.
1. In **Domain audit**, select **Export Users** to export a CSV file
   of uncaptured users.

The CSV file contains the following columns:

- Name: Docker user's display name
- Username: Docker ID of the user
- Email: Email address of the user

### Invite uncaptured users

You can bulk invite uncaptured users to your organization using the exported
CSV file. For more information on bulk inviting users, see
[Manage organization members](/admin/organization/manage/members/).

## Auto-provisioning

[Auto-provisioning](/enterprise/security/provisioning/auto-provisioning/) uses verified domains to associate organization members with email address that match the verified domains. To override auto-provisioning, you can configure one of the two alternative methods:

- [Just-in-Time (JIT)](/enterprise/security/provisioning/just-in-time/) provisioning
- [System for Cross-domain Identity Management (SCIM)](/enterprise/security/provisioning/scim/)

## Delete a domain

Deleting a domain removes its TXT record value and disables any associated auto-provisioning.

> [!WARNING]
>
> Deleting a domain will disable auto-provisioning for that domain and remove verification. This action cannot be undone.

To delete a domain:

1. Sign in to [Docker Home](https://app.docker.com) and select
   your organization. If your organization is part of a company, select the company
   and configure the domain for the organization at the company level.
1. Select **Admin Console**, then **Domain management**.
1. For the domain you want to delete, select the **Actions** menu, then
   **Delete domain**.
1. To confirm, select **Delete domain** in the pop-up modal.

---

## Configure sign-in enforcement

# Configure sign-in enforcement

You can enforce sign-in for Docker Desktop using several methods. Choose the method that best fits your organization's infrastructure and security requirements.

## Choose your method

| Method | Platform |
|:-------|:---------|
| Registry key | Windows only |
| Configuration profiles | Mac only |
| `plist` file | Mac only |
| `registry.json` | All platforms |

> [!TIP]
>
> For Mac, configuration profiles offer the highest security because they're
protected by Apple's System Integrity Protection (SIP).

## Windows: Registry key method

**Manual setup**

To configure the registry key method manually:

1. Create the registry key:

   ```console
   $ HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Docker\Docker Desktop
   ```
1. Create a multi-string value name `allowedOrgs`.
1. Use your organization names as string data. You can add multiple organizations:
   - Use lowercase letters only
   - Add each organization on a separate line
   - Do not use spaces or commas as separators
1. Restart Docker Desktop.
1. Verify the **Sign in required!** prompt appears in Docker Desktop.

**Group Policy deployment**

Deploy the registry key across your organization using Group Policy:

1. Create a registry script with the following structure:
   - Path: `HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Docker\Docker Desktop`
   - Value name: `allowedOrgs` (multi-string)
   - Value data: Your organization names, one per line, in lowercase only
1. In Group Policy Management, create or edit a GPO.
1. Navigate to **Computer Configuration** > **Preferences** > **Windows Settings** > **Registry**.
1. Right-click **Registry** > **New** > **Registry Item**.
1. Configure the registry item:
   - Action: **Update**
   - Path: `HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Docker\Docker Desktop`
   - Value name: `allowedOrgs`
   - Value data: Your organization names
1. Link the GPO to the target Organizational Unit.
1. Test on a small group using `gpupdate/force`.
1. Deploy organization-wide after verification.

## Mac: Configuration profiles method (recommended)

Configuration profiles provide the most secure enforcement method for Mac, as they're protected by Apple's System Integrity Protection.

The payload is a dictionary of key-values. Docker Desktop supports the following keys:

- `allowedOrgs`: Sets a list of organizations in one single string, where each organization is in lowercase only and is separated by a semi-colon. 
- `overrideProxyHTTP`: Sets the URL of the HTTP proxy that must be used for outgoing HTTP requests.
- `overrideProxyHTTPS`: Sets the URL of the HTTP proxy that must be used for outgoing HTTPS requests.
- `overrideProxyExclude`: Bypasses proxy settings for the specified hosts and domains. Uses a comma-separated list.
- `overrideProxyPAC`: Sets the file path where the PAC file is located. It has precedence over the remote PAC file on the selected proxy.
- `overrideProxyEmbeddedPAC`: Sets the content of an in-memory PAC file. It has precedence over `overrideProxyPAC`.

Overriding at least one of the proxy settings via Configuration profiles will automatically lock the settings as they're managed by Mac.

1. Create a file named `docker.mobileconfig` and include the following content:
   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
   <plist version="1.0">
   <dict>
      <key>PayloadContent</key>
      <array>
         <dict>
            <key>PayloadType</key>
            <string>com.docker.config</string>
            <key>PayloadVersion</key>
            <integer>1</integer>
            <key>PayloadIdentifier</key>
            <string>com.docker.config</string>
            <key>PayloadUUID</key>
            <string>eed295b0-a650-40b0-9dda-90efb12be3c7</string>
            <key>PayloadDisplayName</key>
            <string>Docker Desktop Configuration</string>
            <key>PayloadDescription</key>
            <string>Configuration profile to manage Docker Desktop settings.</string>
            <key>PayloadOrganization</key>
            <string>Your company name</string>
            <key>allowedOrgs</key>
            <string>first_org;second_org</string>
            <key>overrideProxyHTTP</key>
            <string>http://company.proxy:port</string>
            <key>overrideProxyHTTPS</key>
            <string>https://company.proxy:port</string>
         </dict>
      </array>
      <key>PayloadType</key>
      <string>Configuration</string>
      <key>PayloadVersion</key>
      <integer>1</integer>
      <key>PayloadIdentifier</key>
      <string>com.yourcompany.docker.config</string>
      <key>PayloadUUID</key>
      <string>0deedb64-7dc9-46e5-b6bf-69d64a9561ce</string>
      <key>PayloadDisplayName</key>
      <string>Docker Desktop Config Profile</string>
      <key>PayloadDescription</key>
      <string>Config profile to enforce Docker Desktop settings for allowed organizations.</string>
      <key>PayloadOrganization</key>
      <string>Your company name</string>
   </dict>
   </plist>
   ```
1. Replace placeholders:
   - Change `com.yourcompany.docker.config` to your company identifier
   - Replace `Your company name` with your organization name making sure it is all lowercase
   - Replace `PayloadUUID` with a randomly generated UUID
   - Update the `allowedOrgs` value with your organization names (separated by semicolons)
   - Replace `company.proxy:port` with http/https proxy server host(or IP address) and port
1. Deploy the profile using your MDM solution.
1. Verify the profile appears in **System Settings** > **General** > **Device Management** under **Device (Managed)**. Ensure the profile is listed with the correct name and settings.

Some MDM solutions let you specify the payload as a plain dictionary of key-value settings without the full `.mobileconfig` wrapper:

```xml
<dict>
   <key>allowedOrgs</key>
   <string>first_org;second_org</string>
   <key>overrideProxyHTTP</key>
   <string>http://company.proxy:port</string>
   <key>overrideProxyHTTPS</key>
   <string>https://company.proxy:port</string>
</dict>
```

## Mac: plist file method

**Manual creation**

1. Create the file `/Library/Application Support/com.docker.docker/desktop.plist`.
1. Add this content, replacing `myorg1` and `myorg2` with your organization names and making sure they have lowercase letters only:
   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
   <plist version="1.0">
     <dict>
	     <key>allowedOrgs</key>
	     <array>
             <string>myorg1</string>
             <string>myorg2</string>
         </array>
     </dict>
   </plist>
   ```
1. Set file permissions to prevent editing by non-administrator users.
1. Restart Docker Desktop.
1. Verify the `Sign in required!` prompt appears in Docker Desktop.

**Shell script deployment**

Create and deploy a script for organization-wide distribution:

```bash
#!/bin/bash

# Create directory if it doesn't exist
sudo mkdir -p "/Library/Application Support/com.docker.docker"

# Write the plist file
sudo defaults write "/Library/Application Support/com.docker.docker/desktop.plist" allowedOrgs -array "myorg1" "myorg2"

# Set appropriate permissions
sudo chmod 644 "/Library/Application Support/com.docker.docker/desktop.plist"
sudo chown root:admin "/Library/Application Support/com.docker.docker/desktop.plist"
```

Deploy this script using SSH, remote support tools, or your preferred deployment method.

## All platforms: registry.json method

The registry.json method works across all platforms and offers flexible deployment options.

### File locations

Create the `registry.json` file (UTF-8 without BOM) at the appropriate location:

| Platform | Location |
| --- | --- |
| Windows | `/ProgramData/DockerDesktop/registry.json` |
| Mac | `/Library/Application Support/com.docker.docker/registry.json` |
| Linux | `/usr/share/docker-desktop/registry/registry.json` |

### Basic setup

**Manual creation**

1. Ensure users are members of your Docker organization.
1. Create the `registry.json` file at the appropriate location for your platform.
1. Add this content, replacing organization names with your own and making sure they have lowercase letters only:
      ```json
      {
         "allowedOrgs": ["myorg1", "myorg2"]
      }
      ```
1. Set file permissions to prevent user editing.
1. Restart Docker Desktop.
1. Verify the `Sign in required!` prompt appears in Docker Desktop.

> [!TIP]
>
> If users have issues starting Docker Desktop after enforcing sign-in,
> they may need to update to the latest version.

**Command line setup**

#### Windows (PowerShell as Administrator)

```shell
Set-Content /ProgramData/DockerDesktop/registry.json '{"allowedOrgs":["myorg1","myorg2"]}'
```

#### Mac

```console
sudo mkdir -p "/Library/Application Support/com.docker.docker"
echo '{"allowedOrgs":["myorg1","myorg2"]}' | sudo tee "/Library/Application Support/com.docker.docker/registry.json"
```

#### Linux

```console
sudo mkdir -p /usr/share/docker-desktop/registry
echo '{"allowedOrgs":["myorg1","myorg2"]}' | sudo tee /usr/share/docker-desktop/registry/registry.json
```

**Installation-time setup**

Create the registry.json file during Docker Desktop installation:

#### Windows

```shell
# PowerShell
Start-Process '.\Docker Desktop Installer.exe' -Wait 'install --allowed-org=myorg'

# Command Prompt
"Docker Desktop Installer.exe" install --allowed-org=myorg1
```

> [!NOTE]
>
> The `--allowed-org` flag accepts only one organization. To enforce sign-in for multiple organizations on Mac, configure the `registry.json` file after installation.

#### Mac

```console
sudo hdiutil attach Docker.dmg
sudo /Volumes/Docker/Docker.app/Contents/MacOS/install --allowed-org=myorg
sudo hdiutil detach /Volumes/Docker
```
> [!NOTE]
>
> The `--allowed-org` flag accepts only one organization. To enforce sign-in for multiple organizations on Mac, configure the `registry.json` file after installation.

## Method precedence

When multiple configuration methods exist on the same system, Docker Desktop uses this precedence order:

1. Registry key (Windows only)
1. Configuration profiles (Mac only)
1. plist file (Mac only)
1. registry.json file

## Troubleshoot sign-in enforcement

If sign-in enforcement doesn't work:

- Verify file locations and permissions
- Check that organization names use lowercase letters
- Restart Docker Desktop or reboot the system
- Confirm users are members of the specified organizations
- Update Docker Desktop to the latest version

---

## Air-gapped containers

# Air-gapped containers

Air-gapped containers let you restrict container network access by controlling where containers can send and receive data. This feature applies custom proxy rules to container network traffic, helping secure environments where containers shouldn't have unrestricted internet access.

Docker Desktop can configure container network traffic to accept connections, reject connections, or tunnel through HTTP or SOCKS proxies. You control which TCP ports the policy applies to and whether to use a single proxy or per-destination policies via Proxy Auto-Configuration (PAC) files.

## Who should use air-gapped containers?

Use air-gapped containers if:

- Your organization requires containers to communicate only with approved internal services
- You need to meet compliance standards that mandate network isolation (such as SOC 2, ISO 27001, or PCI DSS)
- You want to prevent containers from leaking data or reaching unapproved external endpoints during builds or at runtime

## How air-gapped containers work

Air-gapped containers operate by intercepting container network traffic and applying proxy rules:

1. Traffic interception: Docker Desktop intercepts all outgoing network connections from containers
1. Port filtering: Only traffic on specified ports (`transparentPorts`) is subject to proxy rules
1. Rule evaluation: PAC file rules or static proxy settings determine how to handle each connection
1. Connection handling: Traffic is allowed directly, routed through a proxy, or blocked based on the rules

Some important considerations include:

- The existing `proxy` setting continues to apply to Docker Desktop application traffic on the host
- If PAC file download fails, containers block requests to target URLs
- Hostname is available for ports 80 and 443, but only IP addresses for other ports

## Prerequisites

Before configuring air-gapped containers, you must have:

- [Enforce sign-in](/enterprise/security/enforce-sign-in/) enabled to ensure users authenticate with your organization
- A Docker Business subscription
- Configured [Settings Management](/enterprise/security/hardened-desktop/settings-management/) with the `admin-settings.json` file to manage organization policies

## Configure air-gapped containers

Add the container proxy to your [`admin-settings.json` file](/enterprise/security/hardened-desktop/settings-management/configure-json-file/). For example:

```json
{
  "configurationFileVersion": 2,
  "containersProxy": {
    "locked": true,
    "mode": "manual",
    "http": "",
    "https": "",
    "exclude": [],
    "pac": "http://192.168.1.16:62039/proxy.pac",
    "transparentPorts": "*"
  }
}
```

### Configuration parameters

The `containersProxy` setting controls network policies applied to container traffic:

| Parameter | Description | Value |
|-----------|-------------|-------|
| `locked` | Prevents developers from overriding settings | `true` (locked), `false` (default) |
| `mode` | Proxy configuration method | `system` (use system proxy), `manual` (custom) |
| `http` | HTTP proxy server | URL (e.g., `"http://proxy.company.com:8080"`) |
| `https` | HTTPS proxy server | URL (e.g., `"https://proxy.company.com:8080"`) |
| `exclude` | Bypass proxy for these addresses | Array of hostnames/IPs |
| `pac` | Proxy Auto-Configuration file URL | URL to PAC file |
| `transparentPorts` | Ports subject to proxy rules | Comma-separated ports or wildcard (`"*"`) |

### Configuration examples

Block all external access:

```json
"containersProxy": {
  "locked": true,
  "mode": "manual",
  "http": "",
  "https": "",
  "exclude": [],
  "transparentPorts": "*"
}
```

Allow specific internal services:

```json
"containersProxy": {
  "locked": true,
  "mode": "manual",
  "http": "",
  "https": "",
  "exclude": ["internal.company.com", "10.0.0.0/8"],
  "transparentPorts": "80,443"
}
```

Route through corporate proxy:

```json
"containersProxy": {
  "locked": true,
  "mode": "manual",
  "http": "http://corporate-proxy.company.com:8080",
  "https": "http://corporate-proxy.company.com:8080",
  "exclude": ["localhost", "*.company.local"],
  "transparentPorts": "*"
}
```

## Proxy Auto-Configuration (PAC) files

PAC files provide fine-grained control over container network access by defining rules for different destinations.

### Basic PAC file structure

```javascript
function FindProxyForURL(url, host) {
	if (localHostOrDomainIs(host, 'internal.corp')) {
		return "PROXY 10.0.0.1:3128";
	}
	if (isInNet(host, "192.168.0.0", "255.255.255.0")) {
	    return "DIRECT";
	}
    return "PROXY reject.docker.internal:1234";
}
```

### General considerations

 - `FindProxyForURL` function URL parameter format is `http://host_or_ip:port` or `https://host_or_ip:port`
 - If you have an internal container trying to access `https://docs.docker.com/enterprise/security/hardened-desktop/air-gapped-containers` the Docker proxy service will submit docs.docker.com for the host value and https://docs.docker.com:443 for the url value to `FindProxyForURL`, if you are using `shExpMatch` function in your PAC file as follows:

   ```console
   if(shExpMatch(url, "https://docs.docker.com:443/enterprise/security/*")) return "DIRECT";
   ```

   `shExpMatch` function will fail, instead use:

   ```console
   if (host == docs.docker.com && url.indexOf(":443") > 0) return "DIRECT";
   ```

### PAC file return values

| Return value | Action |
|--------------|--------|
| `PROXY host:port` | Route through HTTP proxy at specified host and port |
| `SOCKS5 host:port` | Route through SOCKS5 proxy at specified host and port |
| `DIRECT` | Allow direct connection without proxy |
| `PROXY reject.docker.internal:any_port` | Block the request completely |

### Advanced PAC file example

```javascript
function FindProxyForURL(url, host) {
  // Allow access to Docker Hub for approved base images
  if (dnsDomainIs(host, ".docker.io") || host === "docker.io") {
    return "PROXY corporate-proxy.company.com:8080";
  }

  // Allow internal package repositories
  if (localHostOrDomainIs(host, 'nexus.company.com') ||
      localHostOrDomainIs(host, 'artifactory.company.com')) {
    return "DIRECT";
  }

  // Allow development tools on specific ports
  if (url.indexOf(":3000") > 0 || url.indexOf(":8080") > 0) {
    if (isInNet(host, "10.0.0.0", "255.0.0.0")) {
      return "DIRECT";
    }
  }

  // Block access to developer's localhost
  if (host === "host.docker.internal" || host === "localhost") {
    return "PROXY reject.docker.internal:1234";
  }

  // Block all other external access
  return "PROXY reject.docker.internal:1234";
}
```

## Verify air-gapped container configuration

After applying the configuration, test that container network restrictions work:

Test blocked access:

```console
$ docker run --rm alpine wget -O- https://www.google.com
# Should fail or timeout based on your proxy rules
```

Test allowed access:

```console
$ docker run --rm alpine wget -O- https://internal.company.com
# Should succeed if internal.company.com is in your exclude list or PAC rules
```

Test proxy routing:

```console
$ docker run --rm alpine wget -O- https://docker.io
# Should succeed if routed through approved proxy
```

## Security considerations

- Network policy enforcement: Air-gapped containers work at the Docker Desktop level. Advanced users might bypass restrictions through various means, so consider additional network-level controls for high-security environments.
- Development workflow impact: Overly restrictive policies can break legitimate development workflows. Test thoroughly and provide clear exceptions for necessary services.
- PAC file management: Host PAC files on reliable internal infrastructure. Failed PAC downloads result in blocked container network access.
- Performance considerations: Complex PAC files with many rules may impact container network performance. Keep rules simple and efficient.

## Next steps

- [Explore Enhanced Container Isolation](/enterprise/security/hardened-desktop/enhanced-container-isolation/) to further restrict what containers can do at runtime
- [Understand how Docker Desktop handles host and container networking](/desktop/features/networking/)

---

## Configure Docker socket exceptions and advanced settings

# Configure Docker socket exceptions and advanced settings

This page shows you how to configure Docker socket exceptions and other advanced settings for Enhanced Container Isolation (ECI). These configurations enable trusted tools like Testcontainers to work with ECI while maintaining security.

## Docker socket mount permissions

By default, Enhanced Container Isolation blocks containers from mounting the Docker socket to prevent malicious access to Docker Engine. However, some tools require Docker socket access.

Common scenarios requiring Docker socket access include:

- Testing frameworks: Testcontainers, which manages test containers
- Build tools: Paketo buildpacks that create ephemeral build containers
- CI/CD tools: Tools that manage containers as part of deployment pipelines
- Development utilities: Docker CLI containers for container management

## Configure socket exceptions

Configure Docker socket exceptions using Settings Management:

**Admin Console**

1. Sign in to [Docker Home](https://app.docker.com) and select your organization from the top-left account drop-down.
1. Go to **Admin Console** > **Desktop Settings Management**.
1. [Create or edit a setting policy](/enterprise/security/hardened-desktop/settings-management/configure-admin-console/).
1. Find **Enhanced Container Isolation** settings.
1. Configure **Docker socket access control** with your trusted images and
command restrictions.

**JSON file**

Create an [`admin-settings.json` file](/enterprise/security/hardened-desktop/settings-management/configure-json-file/) and add:

```json
{
  "configurationFileVersion": 2,
  "enhancedContainerIsolation": {
    "locked": true,
    "value": true,
    "dockerSocketMount": {
      "imageList": {
        "images": [
          "docker.io/localstack/localstack:*",
          "docker.io/testcontainers/ryuk:*",
          "docker:cli"
        ],
        "allowDerivedImages": true
      },
      "commandList": {
        "type": "deny",
        "commands": ["push", "build"]
      }
    }
  }
}
```

## Image allowlist configuration

The `imageList` defines which container images can mount the Docker socket.

### Image reference formats

| Format  | Description |
| :---------------------- | :---------- |
| `<image_name>[:<tag>]`  | Name of the image, with optional tag. If the tag is omitted, the `:latest` tag is used. If the tag is the wildcard `*`, then it means "any tag for that image." |
| `<image_name>@<digest>` | Name of the image, with a specific repository digest (e.g., as reported by `docker buildx imagetools inspect <image>`). This means only the image that matches that name and digest is allowed. |

### Example configurations

Basic allowlist for testing tools:

```json
"imageList": {
  "images": [
    "docker.io/testcontainers/ryuk:*",
    "docker:cli",
    "alpine:latest"
  ]
}
```

Wildcard allowlist (Docker Desktop 4.36 and later):

```json
"imageList": {
  "images": ["*"]
}
```

> [!WARNING]
>
> Using `"*"` allows all containers to mount the Docker socket, which reduces security. Only use this when explicitly listing allowed images isn't feasible.

### Security validation

Docker Desktop validates allowed images by:

1. Downloading image digests from registries for allowed images
1. Comparing container image digests against the allowlist when containers start
1. Blocking containers whose digests don't match allowed images

This prevents bypassing restrictions by re-tagging unauthorized images:

```console
$ docker tag malicious-image docker:cli
$ docker run -v /var/run/docker.sock:/var/run/docker.sock docker:cli
# This fails because the digest doesn't match the real docker:cli image
```

## Derived images support

For tools like Paketo buildpacks that create ephemeral local images, you can
allow images derived from trusted base images.

### Enable derived images

```json
"imageList": {
  "images": [
    "paketobuildpacks/builder:base"
  ],
  "allowDerivedImages": true
}
```

When `allowDerivedImages` is true, local images built from allowed base images (using `FROM` in Dockerfile) also gain Docker socket access.

### Derived images requirements

- Local images only: Derived images must not exist in remote registries
- Base image available: The parent image must be pulled locally first
- Performance impact: Adds up to 1 second to container startup for validation
- Version compatibility: Full wildcard support requires Docker Desktop 4.36+

## Command restrictions

### Deny list (recommended)

Blocks specified commands while allowing all others:

```json
"commandList": {
  "type": "deny",
  "commands": ["push", "build", "image*"]
}
```

### Allow list

Only allows specified commands while blocking all others:

```json
"commandList": {
  "type": "allow",
  "commands": ["ps", "container*", "volume*"]
}
```

### Command wildcards

| Wildcard | Blocks/allows |
| :---------------- | :---------- |
| `"container\*"`     | All "docker container ..." commands |
| `"image\*"`         | All "docker image ..." commands |
| `"volume\*"`        | All "docker volume ..." commands |
| `"network\*"`       | All "docker network ..." commands |
| `"build\*"`         | All "docker build ..." commands |
| `"system\*"`        | All "docker system ..." commands |

### Command blocking example

When a blocked command is executed:

```console
/ # docker push myimage
Error response from daemon: enhanced container isolation: docker command "/v1.43/images/myimage/push?tag=latest" is blocked; if you wish to allow it, configure the docker socket command list in the Docker Desktop settings.
```

## Common configuration examples

### Testcontainers setup

For Java/Python testing with Testcontainers:

```json
"dockerSocketMount": {
  "imageList": {
    "images": [
      "docker.io/testcontainers/ryuk:*",
      "testcontainers/*:*"
    ]
  },
  "commandList": {
    "type": "deny",
    "commands": ["push", "build"]
  }
}
```

### CI/CD pipeline tools

For controlled CI/CD container management:

```json
"dockerSocketMount": {
  "imageList": {
    "images": [
      "docker:cli",
      "your-registry.com/ci-tools/*:*"
    ]
  },
  "commandList": {
    "type": "allow",
    "commands": ["ps", "container*", "image*"]
  }
}
```

### Development environments

For local development with Docker-in-Docker:

```json
"dockerSocketMount": {
  "imageList": {
    "images": [
      "docker:dind",
      "docker:cli"
    ]
  },
  "commandList": {
    "type": "deny",
    "commands": ["system*"]
  }
}
```

## Security recommendations

### Image allowlist best practices

- Be restrictive: Only allow images you absolutely trust and need
- Use wildcards carefully: Tag wildcards (`*`) are convenient but less secure than specific tags
- Regular reviews: Periodically review and update your allowlist
- Digest pinning: Use digest references for maximum security in critical environments

### Command restrictions

- Default to deny: Start with a deny list blocking dangerous commands like `push` and `build`
- Principle of least privilege: Only allow commands your tools actually need
- Monitor usage: Track which commands are being blocked to refine your configuration

### Monitoring and maintenance

- Regular validation: Test your configuration after Docker Desktop updates, as image digests may change.
- Handle digest mismatches: If allowed images are unexpectedly blocked:
    ```console
    $ docker image rm <image>
    $ docker pull <image>
    ```

This resolves digest mismatches when upstream images are updated.

## Next steps

- Review [Enhanced Container Isolation limitations](/enterprise/security/hardened-desktop/enhanced-container-isolation/limitations/).
- Review [Enhanced Container Isolation FAQs](/enterprise/security/hardened-desktop/enhanced-container-isolation/faq/).

---

## Enable Enhanced Container Isolation

# Enable Enhanced Container Isolation

ECI prevents malicious containers from compromising Docker Desktop while maintaining full developer productivity.

This page shows you how to turn on Enhanced Container Isolation (ECI) and verify it's working correctly.

## Prerequisites

Before you begin, you must have:

- A Docker Business subscription
- [Enforced sign-in](/enterprise/security/enforce-sign-in/) (for administrators managing organization-wide settings only)

## Enable Enhanced Container Isolation

### For developers

Turn on ECI in your Docker Desktop settings:

1. Sign in to your organization in Docker Desktop. Your organization must have
a Docker Business subscription.
1. Stop and remove all existing containers:

    ```console
    $ docker stop $(docker ps -q)
    $ docker rm $(docker ps -aq)
    ```

1. In Docker Desktop, go to **Settings** > **General**.
1. Select the **Use Enhanced Container Isolation** checkbox.
1. Select **Apply and restart**.

> [!IMPORTANT]
>
> ECI doesn't protect containers created before turning on the feature. Remove existing containers before turning on ECI.

### For administrators

Configure Enhanced Container Isolation organization-wide using Settings Management:

**Admin Console**

1. Sign in to [Docker Home](https://app.docker.com) and select your organization from the top-left account drop-down.
1. Go to **Admin Console** > **Desktop Settings Management**.
1. [Create or edit a setting policy](/enterprise/security/hardened-desktop/settings-management/configure-admin-console/).
1. Set **Enhanced Container Isolation** to **Always enabled**.

**JSON file**

1. Create an [`admin-settings.json` file](/enterprise/security/hardened-desktop/settings-management/configure-json-file/) and add:

      ```json
      {
        "configurationFileVersion": 2,
        "enhancedContainerIsolation": {
          "value": true,
          "locked": true
        }
      }
      ```

1. Configure the following as needed:
    - `"value": true`: Turns on ECI by default (required)
    - `"locked": true`: Prevents developers from turning off ECI
    - `"locked": false`: Allows developers to control the setting

### Apply the configuration

For ECI settings to take effect:

- New installations: Users launch Docker Desktop and sign in
- Existing installations: Users must fully quit Docker Desktop and relaunch

> [!IMPORTANT]
>
> Restarting from the Docker Desktop menu isn't sufficient. Users must completely quit and reopen Docker Desktop.

You can also configure [Docker socket mount permissions](/enterprise/security/hardened-desktop/enhanced-container-isolation/config/) for trusted images that need Docker API access.

## Verify Enhanced Container Isolation is active

After turning on ECI, verify it's working correctly using these methods.

### Check user namespace mapping

Run a container and examine the user namespace mapping:

```console
$ docker run --rm alpine cat /proc/self/uid_map
```

With ECI turned on:

```text
0     100000      65536
```

This shows the container's root user (0) maps to an unprivileged user (100000) in the Docker Desktop VM, with a range of 64K user IDs. Each container gets an exclusive user ID range for isolation.

With ECI turned off:

```text
0          0 4294967295
```

This shows the container root user (0) maps directly to the VM root user (0), providing less isolation.

### Check container runtime

Verify the container runtime being used:

```console
$ docker inspect --format='{{.HostConfig.Runtime}}' <container_name>
```

With ECI turned on, it turns `sysbox-runc`. With ECI turned off, it returns
`runc`.

### Test security restrictions

Verify that ECI security restrictions are active.

Test namespace sharing:

```console
$ docker run -it --rm --pid=host alpine
```

With ECI turned on, this command fails with an error about Sysbox containers
not being able to share namespaces with the host.

Test Docker socket access:

```console
$ docker run -it --rm -v /var/run/docker.sock:/var/run/docker.sock alpine
```

With ECI turned on, this command fails unless you've configured Docker socket exceptions for trusted images.

## What users see with enforced ECI

When administrators enforce Enhanced Container Isolation through
Settings Management:

- The **Use Enhanced Container Isolation** setting appears turned on in
Docker Desktop settings.
- If set to `"locked": true`, the setting is locked and greyed out.
- All new containers automatically use Linux user namespaces.
- Existing development workflows continue to work without modification.
- Users see `sysbox-runc` as the container runtime in `docker inspect` output.

## Next steps

- Review [Configure Docker socket exceptions and advanced settings](/enterprise/security/hardened-desktop/enhanced-container-isolation/config/).
- Review [Enhanced Container Isolation limitations](/enterprise/security/hardened-desktop/enhanced-container-isolation/limitations/).

---

## Enhanced Container Isolation FAQs

# Enhanced Container Isolation FAQs

This page answers common questions about Enhanced Container Isolation (ECI) that aren't covered in the main documentation.

## Do I need to change the way I use Docker when ECI is switched on?

No. ECI works automatically in the background by creating more secure containers. You can continue using all your existing Docker commands, workflows, and development tools without any changes.

## Do all container workloads work well with ECI?

Most container workloads run without issues when ECI is turned on. However, some advanced workloads that require specific kernel-level access may not work. For details about which workloads are affected, see [ECI limitations](/enterprise/security/hardened-desktop/enhanced-container-isolation/limitations/).

## Why not just restrict usage of the `--privileged` flag?

Privileged containers serve legitimate purposes like Docker-in-Docker, Kubernetes-in-Docker, and accessing hardware devices. ECI provides a better solution by allowing these advanced workloads to run securely while preventing them from compromising the Docker Desktop VM.

## Does ECI affect container performance?

ECI has minimal impact on container performance. The only exception is containers that perform many `mount` and `umount` system calls, as these are inspected by the Sysbox runtime for security. Most development workloads see no noticeable performance difference.

## Can I override the container runtime with ECI turned on?

No. When ECI is turned on, all containers use the Sysbox runtime regardless of any `--runtime` flags:

```console
$ docker run --runtime=runc alpine echo "test"
# This still uses sysbox-runc, not runc
```

The `--runtime` flag is ignored to prevent users from bypassing ECI security by running containers as true root in the Docker Desktop VM.

## Does ECI protect containers created before turning it on?

No. ECI only protects containers created after it's turned on. Remove existing containers before turning on ECI:

```console
$ docker stop $(docker ps -q)
$ docker rm $(docker ps -aq)
```

For more details, see [Enable Enhanced Container Isolation](/enterprise/security/hardened-desktop/enhanced-container-isolation/enable-eci/).

## Which containers does ECI protect?

ECI protection varies by container type and Docker Desktop version:

### Always protected

- Containers created with `docker run` and `docker create`
- Containers using the `docker-container` build driver
- Kubernetes with the Kind provisioner

### Platform dependent

- Docker Build: Protected in Docker Desktop for Mac, Linux, and Windows with Hyper-V backend

### Not protected

- Docker Extensions
- Docker Debug containers
- Kubernetes with Kubeadm provisioner

For complete details, see [ECI limitations](/enterprise/security/hardened-desktop/enhanced-container-isolation/limitations/).

## Can I mount the Docker socket with ECI turned on?

By default, no. ECI blocks Docker socket bind mounts for security. However, you can configure exceptions for trusted images like Testcontainers.

For configuration details, see [Configure Docker socket exceptions](/enterprise/security/hardened-desktop/enhanced-container-isolation/config/).

## What bind mounts does ECI restrict?

ECI restricts bind mounts of Docker Desktop VM directories but allows host directory mounts configured in Docker Desktop Settings.

---

## Enhanced Container Isolation limitations

# Enhanced Container Isolation limitations

Enhanced Container Isolation has some platform-specific limitations and feature constraints. Understanding these limitations helps you plan your security strategy and set appropriate expectations.

## WSL 2 security considerations

> [!NOTE]
>
> Docker Desktop requires WSL 2 version 2.1.5 or later. ECI on the WSL 2 backend
> requires WSL version 2.6 or later because ECI depends on a Linux kernel version
> of at least 6.3.0. Check your version with `wsl --version` and update with
> `wsl --update` if needed.

Enhanced Container Isolation provides different security levels depending on your Windows backend configuration.

The following table compares ECI on WSL 2 and ECI on Hyper-V:

| Security feature                                   | ECI on WSL   | ECI on Hyper-V   | Comment               |
| -------------------------------------------------- | ------------ | ---------------- | --------------------- |
| Strongly secure containers                         | Yes          | Yes              | Makes it harder for malicious container workloads to breach the Docker Desktop Linux VM and host. |
| Docker Desktop Linux VM protected from user access | No           | Yes              | On WSL, users can access Docker Engine directly or bypass Docker Desktop security settings. |
| Docker Desktop Linux VM has a dedicated kernel     | No           | Yes              | On WSL, Docker Desktop can't guarantee the integrity of kernel level configs. |

WSL 2 security gaps include:

- Direct VM access: Users can bypass Docker Desktop security by accessing the VM directly: `wsl -d docker-desktop`. This gives users root access to modify Docker Engine settings and bypass
Settings Management configurations.
- Shared kernel vulnerability: All WSL 2 distributions share the same Linux kernel instance. Other WSL distributions can modify kernel settings that affect Docker Desktop's security.

### Recommendation

Use Hyper-V backend for maximum security. WSL 2 offers better performance and resource
utilization, but provides reduced security isolation.

## Windows containers not supported

ECI only works with Linux containers (Docker Desktop's default mode). Native Windows
containers mode isn't supported.

## Docker Build protection varies

Docker Build protection depends on the driver and Docker Desktop version:

| Build drive | Protection | Version requirements |
|:------------|:-----------|:---------------------|
| `docker` (default) | Protected | Docker Desktop 4.30 and later (except WSL 2) |
| `docker` (legacy) | Not protected | Docker Desktop versions before 4.30 |
| `docker-container` | Always protected | All Docker Desktop versions |

The following Docker Build features don't work with ECI:

- `docker build --network=host`
- Docker Buildx entitlements: `network.host`, `security.insecure`

### Recommendation

Use `docker-container` build driver for builds requiring these features:

```console
$ docker buildx create --driver docker-container --use
$ docker buildx build --network=host .
```

## Docker Desktop Kubernetes not protected in Kubeadm mode

The integrated Kubernetes feature, when used with the legacy Kubeadm provisioner, doesn't benefit from ECI protection. Malicious or privileged pods can compromise the Docker Desktop VM and bypass security controls.

### Recommendation

Use the newer Docker Desktop Kubernetes "KinD" provisioner (see [Cluster provisioning method](/desktop/use-desktop/kubernetes/#cluster-provisioning-method)). In this mode, and with ECI turned on, each Kubernetes node runs in an ECI-protected container, providing stronger isolation from the Docker Desktop VM. The KinD provisioner is also faster and allows for multi-node Kubernetes clusters.

## Unprotected container types

These container types currently don't benefit from ECI protection:

- Docker Extensions: Extension containers run without ECI protection
- Kubernetes pods: When using Docker Desktop's integrated Kubernetes with the old Kubeadm provisioner.

### Recommendation

Only use extensions from trusted sources in security-sensitive environments.

## Global command restrictions

Command lists apply to all containers allowed to mount the Docker socket. You can't configure different command restrictions per container image.

## Local-only images not supported

You can't allow arbitrary local-only images (images not in a registry) to mount the Docker socket, unless they're:

- Derived from an allowed base image (with `allowDerivedImages: true`)
- Using the wildcard allowlist (`"*"`, Docker Desktop 4.36 and later)

## Unsupported Docker commands

These Docker commands aren't yet supported in command list restrictions:

- `compose`: Docker Compose commands
- `dev`: Development environment commands
- `extension`: Docker Extensions management
- `feedback`: Docker feedback submission
- `init`: Docker initialization commands
- `manifest`: Image manifest management
- `plugin`: Plugin management
- `sbom`: Software Bill of Materials
- `scout`: Docker Scout commands
- `trust`: Image trust management

## Performance considerations

### Derived images impact

Enabling `allowDerivedImages: true` adds approximately 1 second to container startup time for image validation.

### Registry dependencies

- Docker Desktop periodically fetches image digests from registries for validation
- Initial container starts require registry access to validate allowed images
- Network connectivity issues may cause delays in container startup

### Image digest validation

When allowed images are updated in registries, local containers may be unexpectedly blocked until you refresh the local image:

```console
$ docker image rm <image>
$ docker pull <image>
```

## Production compatibility

### Container behavior differences

Most containers run identically with and without ECI. However, some advanced workloads may behave differently:

- Containers requiring kernel module loading
- Workloads modifying global kernel settings (BPF, sysctl)
- Applications expecting specific privilege escalation behavior
- Tools requiring direct hardware device access

Test advanced workloads with ECI in development environments before production deployment to ensure compatibility.

### Runtime considerations

Containers using the Sysbox runtime (with ECI) may have subtle differences compared to standard OCI runc runtime in production. These differences typically only affect privileged or system-level operations.

---

## Image Access Management

# Image Access Management

Image Access Management lets administrators control which types of images developers can pull from Docker Hub. This prevents developers from accidentally using untrusted community images that could pose security risks to your organization.

With Image Access Management, you can restrict access to:

- Docker Official Images: Curated images maintained by Docker
- Docker Verified Publisher Images: Images from trusted commercial publishers
- Organization images: Your organization's private repositories
- Community images: Public images from individual developers

You can also use a repository allowlist to approve specific repositories that bypass all other access controls.

## Who should use Image Access Management?

Image Access Management helps prevent supply chain attacks by ensuring developers only use trusted container images. For example, a developer building a new application might accidentally use a malicious community image as a component. Image Access Management prevents this by restricting access to only approved image types.

Common security scenarios include:

- Prevent use of unmaintained or malicious community images
- Ensure developers use only vetted, official base images
- Control access to commercial third-party images
- Maintain consistent security standards across development teams

Use the repository allowlist when you need to:

- Grant access to specific vetted community images
- Allow essential third-party tools that don't fall under official categories
- Provide exceptions to general image access policies for specific business requirements

## Prerequisites

Before configuring Image Access Management, you must:

- [Enforce sign-in](/enterprise/security/enforce-sign-in/). Image Access Management only takes effect when users are signed in to Docker Desktop with organization credentials.
- Use [personal access tokens (PATs)](/security/access-tokens/) for authentication (Organization access tokens aren't supported)
- Have a Docker Business subscription

## Configure image access

> [!NOTE]
>
> Image Access Management is turned off by default for organization members. Organization owners always have access to all images regardless of policy settings.

To configure Image Access Management:

1. Sign in to [Docker Home](https://app.docker.com) and select your organization from the top-left account drop-down.
1. Select **Admin Console**, then **Image access**.
1. Use the **toggle** to enable image access.
1. Select which image types to allow:
    - **Organization images**: Images from your organization (always allowed by default). These can be public or private images created by members within your organization.
    - **Community images**: Images contributed by various users that may pose security risks. This category includes Docker-Sponsored Open Source images and is turned off by default.
    - **Docker Verified Publisher Images**: Images from Docker partners in the Verified Publisher program, qualified for secure supply chains.
    - **Docker Official Images**: Curated Docker repositories that provide OS repositories, best practices for Dockerfiles, drop-in solutions, and timely security updates.
    - **Repository allowlist**: A list of specific repositories that should be
      allowed. Configure in the next step.
1. If **Repository allowlist** is enabled in the previous step,
   you can add or remove specific repositories in the allow list:
    - To add repositories, in the **Repository allowlist** section, select
      **Add repositories to allowlist** and follow the on-screen instructions.
    - To remove a repository, in the **Repository allowlist** section, select
      the trashcan icon next to it.

    Repositories in the allow list are accessible to all organization members regardless of the image type restrictions configured in the previous steps.

After restrictions are applied, organization members can view the permissions page in read-only format.

## Verify access restrictions

After configuring Image Access Management, test that restrictions work correctly.

When developers pull allowed image types:

```console
$ docker pull nginx  # Docker Official Image
# Pull succeeds if Docker Official Images are allowed
```

When developers pull blocked image types:

```console
$ docker pull someuser/custom-image  # Community image
Error response from daemon: image access denied: community images not allowed
```

Image access restrictions apply to all Docker Hub operations including pulls, builds using `FROM` instructions, and Docker Compose services.

## Best practices

- Start with the most restrictive policy and gradually expand based on legitimate business needs:
   1. Start with Docker Official Images and Organization images
   2. If needed, add Docker Verified Publisher Images for commercial tools
   3. Carefully evaluate community images only for specific, vetted use cases
   4. Use the repository allowlist sparingly. Only add repositories that have been thoroughly vetted and approved through your organization's security review process
- Monitor usage patterns: Review which images developers are attempting to pull, identify legitimate requests for additional image types, regularly audit approved image categories for continued relevance, and use Docker Desktop analytics to monitor usage patterns.
- Regularly review the repository allow list: Periodically audit the repositories in your allowlist to ensure they remain necessary and trustworthy, and remove any that are no longer needed or maintained.

## Scope and bypass considerations

- Image Access Management only controls access to Docker Hub images. Images from other registries aren't affected by these policies. Use [Registry Access Management](/enterprise/security/hardened-desktop/registry-access-management/) to control access to other registries.
- Users can potentially bypass Image Access Management by signing out of Docker Desktop (unless sign-in is enforced), using images from other registries that aren't restricted, or using registry mirrors or proxies. Enforce sign-in and combine with Registry Access Management for comprehensive control.
- Image restrictions apply to Dockerfile `FROM` instructions, Docker Compose services using restricted images will fail, multi-stage builds may be affected if intermediate images are restricted, and CI/CD pipelines using diverse image types may be impacted.

## Next steps

- Layer security controls: Image Access Management works best with [Registry Access Management](/enterprise/security/hardened-desktop/image-access-management/registry-access-management/) to control which registries developers can access, [Enhanced Container Isolation](/enterprise/security/hardened-desktop/image-access-management/enhanced-container-isolation/) to secure containers at runtime, and [Settings Management](/enterprise/security/hardened-desktop/image-access-management/settings-management/) to control Docker Desktop configuration.

---

## Namespace access control

# Namespace access control

Namespace access control lets organization administrators control whether all
members of an organization can push content to their personal namespaces on
Docker Hub. This prevents organizations from accidentally publishing images
outside of approved, governed locations.

When namespace access control is enabled, organization members can still view and pull images
from their personal namespaces and continue accessing all existing repositories
and content. However, they're unable to create new repositories or
push new images to their personal namespace.

> [!IMPORTANT]
>
> For users in multiple organizations, if namespace access control is enabled in
> any organization, that user cannot push to their personal namespace and cannot
> create new repositories in their personal namespace.

### Configure namespace access control

To configure namespace access control:

1. Sign in to [Docker Home](https://app.docker.com/) and select your
   organization from the top-left account drop-down.
2. Select **Admin Console**, then **Namespace access**.
3. Use the toggle to enable or disable namespace access control.
4. Select **Save changes**.

Once namespace access control is enabled, organization members can still view their
personal namespace and existing repositories but they are not able to create
any new repositories or push any new images to existing repositories.

### Verify access restrictions

After configuring namespace access control, test that restrictions work correctly.

After any attempt to push to an existing repository in your personal namespace,
you'll see an error message like the following:

```console
$ docker push <personal-namespace>/<image>:<tag>
Unavailable
authentication required - namespace access restriction from an organization you belong to prevents pushing new content in your personal namespace. Restriction applied by: <organizations>. Please contact your organization administrator
```

---

## Registry Access Management

# Registry Access Management

Registry Access Management (RAM) lets administrators control which container registries developers can access through Docker Desktop. This DNS-level filtering ensures developers only pull and push images from approved registries, improving supply chain security.

RAM works with all registry types including cloud services, on-premises registries, and registry mirrors. You can allow any hostname or domain, but must include redirect domains (like `s3.amazonaws.com` for some registries) in your allowlist.

## Supported registries

Registry Access Management works with any container registry, including:

- Docker Hub (allowed by default)
- Cloud registries: Amazon ECR, Google Artifact Registry, Azure Container Registry
- Git-based registries: GitHub Container Registry, GitLab Container Registry
- On-premises solutions: Nexus, Artifactory, Harbor
- Registry mirrors: Including Docker Hub mirrors

## Prerequisites

Before configuring Registry Access Management, you must:

- [Enforce sign-in](/enterprise/security/enforce-sign-in/). Registry Access Management only takes effect when users are signed in to Docker Desktop with organization credentials.
- Use [personal access tokens (PATs)](/security/access-tokens/) for authentication (Organization access tokens aren't supported)
- Have a Docker Business subscription

## Configure registry permissions

To configure registry permissions:

1. Sign in to [Docker Home](https://app.docker.com) and select your organization from the top-left account drop-down.
1. Select **Admin Console**, then **Registry access**.
1. Use the **toggle** to enable registry access. By default, Docker Hub is enabled
in the registry list.
1. To add additional registries, select **Add registry** and provide
a **Registry address** and **Registry nickname**.
1. Select **Create**. You can add up to 100 registries.
1. Verify your registry appears in the registry list and select **Save changes**.
   >[!NOTE]
   >
   > Policy changes can take up to 24 hours to propagate. To apply changes immediately, ask developers to sign out and back in to Docker Desktop.

If a developer belongs to multiple organizations with different RAM policies, only the policy for the first organization in the configuration file is enforced.

> [!TIP]
>
> RAM restrictions also apply to Dockerfile `ADD` instructions that fetch content via URL. Include trusted registry domains in your allowlist when using `ADD` with URLs.
>
> RAM is designed for container registries, not general-purpose URLs like package mirrors or storage services. Adding too many domains may cause errors or hit system limits.

## Verify restrictions are working

After users sign in to Docker Desktop with their organization credentials, Registry Access Management takes effect immediately.

When users try to pull from a blocked registry:

```console
$ docker pull blocked-registry.com/image:tag
Error response from daemon: registry access to blocked-registry.com is not allowed
```

Allowed registry access works normally:

```console
$ docker pull allowed-registry.com/image:tag
# Pull succeeds
```

Registry restrictions apply to all Docker operations including pulls, pushes,
and builds that reference external registries.

## Registry limits and platform constraints

Registry Access Management has these limits and platform-specific behaviors:

- Maximum allowlist size: 100 registries or domains per organization
- DNS-based filtering: Restrictions work at the hostname level, not IP addresses
- Redirect domains required: Must include all domains a registry redirects to (CDN endpoints, storage services)
- Windows containers: Windows image operations aren't restricted by default. Turn on **Use proxy for Windows Docker daemon** in Docker Desktop settings to apply restrictions
- WSL 2 requirements: Requires Linux kernel 5.4 or later, restrictions apply to all WSL 2 distributions

## Build and deployment restrictions

These scenarios are not restricted by Registry Access Management:

- Docker buildx with Kubernetes driver
- Docker buildx with custom Docker-container driver
- Some Docker Debug and Kubernetes image pulls (even if Docker Hub is blocked)
- Images previously cached by registry mirrors may still be blocked if the source registry is restricted

## Security bypass considerations

Users can potentially bypass Registry Access Management through:

- Local proxies or DNS manipulation
- Signing out of Docker Desktop (unless sign-in is enforced)
- Network-level modifications outside Docker Desktop's control

To maximize security effectiveness:

- [Enforce sign-in](/enterprise/security/enforce-sign-in/) to prevent bypass through sign-out
- Implement additional network-level controls for complete protection
- Use Registry Access Management as part of a broader security strategy

## Registry allowlist best practices

- Include all registry domains: Some registries redirect to multiple
domains. For AWS ECR, include:

    ```text
    your-account.dkr.ecr.us-west-2.amazonaws.com
    amazonaws.com
    s3.amazonaws.com
    ```

- Practice regular allowlist maintenance:
    - Remove unused registries periodically
    - Add newly approved registries as needed
    - Update domain names that may have changed
    - Monitor registry usage through Docker Desktop analytics
- Test configuration changes:
    - Verify registry access after making allowlist updates
    - Check that all necessary redirect domains are included
    - Ensure development workflows aren't disrupted
    - Combine with [Enhanced Container Isolation](/enterprise/security/hardened-desktop/enhanced-container-isolation/) for comprehensive protection
    

---

## Desktop settings reporting

# Desktop settings reporting

Desktop settings reporting tracks user compliance with Docker Desktop settings policies. Use this feature to monitor policy application across your organization and identify users who need assistance with compliance.

## Prerequisites

Before you can use Docker Desktop settings reporting, make sure you have:

- [Docker Desktop](/desktop/release-notes/) installed across your organization
- [A verified domain](/enterprise/security/single-sign-on/connect/)
- [Enforced sign-in](/enterprise/security/enforce-sign-in/) for your organization
- A Docker Business subscription
- At least one settings policy configured

## Access the reporting dashboard

To view compliance reporting:

1. Sign in to [Docker Home](https://app.docker.com) and select
your organization.
1. Select **Admin Console**, then **Desktop settings reporting**.

The reporting dashboard provides these tools:

- A search field to find users by username or email address
- Filter options to show users assigned to specific policies
- Toggles to hide or un-hide compliant users
- Compliance status indicators
- CSV export option to download compliance data

## User compliance statuses

Docker Desktop evaluates three types of status to determine overall compliance:

### Compliance status

This is the primary status shown in the dashboard:

| Compliance status | What it means |
|-------------------|---------------|
| Compliant | The user fetched and applied the latest assigned policy. |
| Non-compliant | The user fetched the correct policy, but hasn't applied it. |
| Outdated | The user fetched a previous version of the policy. |
| No policy assigned | The user does not have any policy assigned to them. |
| Uncontrolled domain | The user's email domain is not verified. |

### Domain status

Shows how the user's email domain relates to your organization:

| Domain status | What it means |
|---------------|---------------|
| Verified | The user鈥檚 email domain is verified. |
| Guest user | The user's email domain is not verified. |
| Domainless | Your organization has no verified domains, and the user's domain is unknown. |

### Settings status

Indicates the user's policy assignment:

| Settings status | What it means |
|-----------------|---------------|
| Global policy | The user is assigned your organization's default policy. |
| User policy | The user is assigned a specific custom policy. |
| No policy assigned | The user is not assigned to any policy. |

## Monitor compliance

From the **Desktop settings reporting** dashboard, you can:

- Review organization-wide compliance at a glance
- Turn on **Hide compliant users** to focus on issues
- Filter by specific policies to check targeted compliance
- Export compliance data
- Select any user's name for detailed status and resolution steps

When you select a user's name, you'll see their detailed compliance information including current status, domain verification, assigned policy, last policy fetch time, and Docker Desktop version.

## Resolve compliance issues

You can select a non-compliant user's name in the dashboard for recommended status resolution steps. The following sections are general resolution steps for non-compliant statuses:

### Non-compliant or outdated users

- Ask the user to fully quit and relaunch Docker Desktop
- Verify the user is signed in to Docker Desktop
- Confirm the user has Docker Desktop 4.40 or later

### Uncontrolled domain users

- Verify the user's email domain in your organization settings
- If the domain should be controlled, add and verify it, then wait for verification
- If the user is a guest and shouldn't be controlled, no action is needed

### No policy assigned users

- Assign the user to an existing policy
- Create a new user-specific policy for them
- Verify they're included in your organization's default policy scope

After users take corrective action, refresh the reporting dashboard to verify status changes.

## Policy update timing

Docker Desktop checks for policy updates:

- At startup
- Every 60 minutes while Docker Desktop is running
- When users restart Docker Desktop

Changes to policies in the Admin Console are available immediately, but users must restart Docker Desktop to apply them.

---

## Configure Settings Management with the Admin Console

# Configure Settings Management with the Admin Console

Use the Docker Admin Console to create and manage settings policies for Docker Desktop across your organization. Settings policies let you standardize configurations, enforce security requirements, and maintain consistent Docker Desktop environments.

## Prerequisites

Before you begin, make sure you have:

- [Docker Desktop](/desktop/release-notes/) installed
- [A verified domain](/enterprise/security/single-sign-on/connect/#step-1-add-a-domain)
- [Enforced sign-in](/enterprise/security/enforce-sign-in/) for your organization
- A Docker Business subscription

> [!IMPORTANT]
>
> You can create settings management policies at any time, but your organization needs to verify a domain before the policies take effect.

## Create a settings policy

To create a new settings policy:

1. Sign in to [Docker Home](https://app.docker.com/) and select
   your organization.
1. Select **Admin Console**, then **Desktop Settings Management**.
1. Select **Create a settings policy**.
1. Provide a name and optional description.

   > [!TIP]
   >
   > You can upload an existing `admin-settings.json` file to pre-fill the form.
   > Admin Console policies override local `admin-settings.json` files.

1. Choose who the policy applies to:
   - All users
   - Specific users

     > [!NOTE]
     >
     > User-specific policies override global default policies. Test your policy with a small group before applying it organization-wide.

1. Configure each setting using a state:

     | Admin Console state | Description                        | `admin-settings.json` equivalent   |
     | :------------------ | :--------------------------------- |:---------------------------------- |
     | **User-defined**    | Users can change the setting       | Omit the setting                   |
     | **Always enabled**  | Setting is on and locked           | `"value": true`, `"locked": true`  |
     | **Enabled**         | Setting is on but can be changed   | `"value": true`, `"locked": false` |
     | **Always disabled** | Setting is off and locked          | `"value": false`, `"locked": true` |
     | **Disabled**        | Setting is off but can be changed  | `"value": false`, `"locked": false`|

     > [!TIP]
     >
     > For a complete list of configurable settings, supported platforms, and configuration methods, see the [Settings reference](/enterprise/security/hardened-desktop/settings-management/configure-admin-console/settings-reference/).

1. Select **Create** to save your policy.

## Apply the policy

Settings policies take effect after Docker Desktop restarts and users sign in.

For new installations:

1. Launch Docker Desktop.
1. Sign in with your Docker account.

For existing installations:

1. Quit Docker Desktop completely.
1. Relaunch Docker Desktop.

> [!IMPORTANT]
>
> Users must fully quit and reopen Docker Desktop. Restarting from the Docker Desktop menu isn't sufficient.

Docker Desktop checks for policy updates when it launches and every 60 minutes while running.

## Verify applied settings

After you apply policies:

- Docker Desktop displays most settings as greyed out
- Some settings, particularly Enhanced Container Isolation configurations,
  may not appear in the GUI
- You can verify all applied settings by checking the [`settings-store.json`
  file](/desktop/settings-and-maintenance/settings/) on your system

## Manage existing policies

From the **Desktop Settings Management** page in the Admin Console, use the **Actions** menu to:

- Edit or delete an existing settings policy
- Export a settings policy as an `admin-settings.json` file
- Promote a user-specific policy to be the new global default

## Roll back policies

To roll back a settings policy:

- Complete rollback: Delete the entire policy.
- Partial rollback: Set specific settings to **User-defined**.

When you roll back settings, users regain control over those settings configurations.

---
