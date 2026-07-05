---
title: "Desktop 安装"
source: "https://docs.docker.com/desktop/install/"
version: "latest"
---

# Desktop 安装

> 原始文档来源：https://docs.docker.com/desktop/install/

---

Manuals

Docker Desktop

Setup

Install

Mac
Install Docker Desktop on Mac

Docker Desktop terms

Commercial use of Docker Desktop in larger enterprises (more than 250 employees or more than $10 million USD in annual revenue) requires a paid subscription.

This page provides download links, system requirements, and step-by-step installation instructions for Docker Desktop on Mac.

Docker Desktop for Mac with Apple silicon Docker Desktop for Mac with Intel chip

For checksums, see Release notes.

System requirements
Mac with Apple silicon Mac with Intel chip

A supported version of macOS.

Important

Docker Desktop is supported on the current and two previous major macOS releases. As new major versions of macOS are made generally available, Docker stops supporting the oldest version and supports the newest version of macOS (in addition to the previous two releases).

At least 4 GB of RAM.

For the best experience, it's recommended that you install Rosetta 2. Rosetta 2 is no longer strictly required, however there are a few optional command line tools that still require Rosetta 2 when using Darwin/AMD64. See Known issues. To install Rosetta 2 manually from the command line, run the following command:

$ softwareupdate --install-rosetta

Before you install or update

Quit tools that might call Docker in the background (Visual Studio Code, terminals, agent apps).

If you manage fleets or install via MDM, use the PKG installer.

Keep the installer volume mounted until the installation completes.

If you encounter a "Docker.app is damaged" dialog, see Fix "Docker.app is damaged" on macOS.

Install and run Docker Desktop on Mac
Tip

See the FAQs on how to install and run Docker Desktop without needing administrator privileges.

Install interactively

Download the installer using the download buttons at the top of the page, or from the release notes.

Double-click Docker.dmg to open the installer, then drag the Docker icon to the Applications folder. By default, Docker Desktop is installed at /Applications/Docker.app.

Double-click Docker.app in the Applications folder to start Docker.

The Docker menu displays the Docker Subscription Service Agreement.

Here’s a summary of the key points:

Docker Desktop is free for small businesses (fewer than 250 employees AND less than $10 million in annual revenue), personal use, education, and non-commercial open source projects.
Otherwise, it requires a paid subscription for professional use.
Paid subscriptions are also required for government entities.
Docker Pro, Team, and Business subscriptions include commercial use of Docker Desktop.

Select Accept to continue.

Note that Docker Desktop won't run if you do not agree to the terms. You can choose to accept the terms at a later date by opening Docker Desktop.

For more information, see Docker Desktop Subscription Service Agreement. It is recommended that you also read the FAQs.

From the installation window, select either:

Use recommended settings (Requires password). This lets Docker Desktop automatically set the necessary configuration settings.
Use advanced settings. You can then set the location of the Docker CLI tools either in the system or user directory, enable the default Docker socket, and enable privileged port mapping. See Settings, for more information and how to set the location of the Docker CLI tools.

Select Finish. If you have applied any of the previous configurations that require a password in step 6, enter your password to confirm your choice.

Install from the command line

After downloading Docker.dmg from either the download buttons at the top of the page or from the release notes, run the following commands in a terminal to install Docker Desktop in the Applications folder:

$ sudo hdiutil attach Docker.dmg

$ sudo /Volumes/Docker/Docker.app/Contents/MacOS/install

$ sudo hdiutil detach /Volumes/Docker

By default, Docker Desktop is installed at /Applications/Docker.app. As macOS typically performs security checks the first time an application is used, the install command can take several minutes to run.

Installer flags

The install command accepts the following flags:

Installation behavior
--accept-license: Accepts the Docker Subscription Service Agreement now, rather than requiring it to be accepted when the application is first run.
--user=<username>: Performs the privileged configurations once during installation. This removes the need for the user to grant root privileges on first run. For more information, see Privileged helper permission requirements. To find the username, enter ls /Users in the CLI.
Security and access
--allowed-org=<org name>: Requires the user to sign in and be part of the specified Docker Hub organization when running the application
--user=<username>: Performs the privileged configurations once during installation. This removes the need for the user to grant root privileges on first run. For more information, see Privileged helper permission requirements. To find the username, enter ls /Users in the CLI.
--admin-settings: Automatically creates an admin-settings.json file which is used by administrators to control certain Docker Desktop settings on client machines within their organization. For more information, see Settings Management.
It must be used together with the --allowed-org=<org name> flag.
For example: --allowed-org=<org name> --admin-settings="{'configurationFileVersion': 2, 'enhancedContainerIsolation': {'value': true, 'locked': false}}"
Proxy configuration
--proxy-http-mode=<mode>: Sets the HTTP Proxy mode. The two modes are system (default) or manual.
--override-proxy-http=<URL>: Sets the URL of the HTTP proxy that must be used for outgoing HTTP requests. It requires --proxy-http-mode to be manual.
--override-proxy-https=<URL>: Sets the URL of the HTTP proxy that must be used for outgoing HTTPS requests, requires --proxy-http-mode to be manual
--override-proxy-exclude=<hosts/domains>: Bypasses proxy settings for the hosts and domains. It's a comma-separated list.
--override-proxy-pac=<PAC file URL>: Sets the PAC file URL. This setting takes effect only when using manual proxy mode.
--override-proxy-embedded-pac=<PAC script>: Specifies an embedded PAC (Proxy Auto-Config) script. This setting takes effect only when using manual proxy mode and has precedence over the --override-proxy-pac flag.
Example of specifying PAC file
$ sudo /Applications/Docker.app/Contents/MacOS/install --user testuser --proxy-http-mode="manual" --override-proxy-pac="http://localhost:8080/myproxy.pac"

Example of specifying PAC script
$ sudo /Applications/Docker.app/Contents/MacOS/install --user testuser --proxy-http-mode="manual" --override-proxy-embedded-pac="function FindProxyForURL(url, host) { return \"DIRECT\"; }"

Tip

As an IT administrator, you can use endpoint management (MDM) software to identify the number of Docker Desktop instances and their versions within your environment. This can provide accurate license reporting, help ensure your machines use the latest version of Docker Desktop, and enable you to enforce sign-in.

Intune
Jamf
Kandji
Kolide
Workspace One
Where to go next
Explore Docker's subscriptions to see what Docker can offer you.
Get started with Docker.
Explore Docker Desktop and all its features.
Troubleshooting describes common problems, workarounds, how to run and submit diagnostics, and submit issues.
FAQs provide answers to frequently asked questions.
Release notes lists component updates, new features, and improvements associated with Docker Desktop releases.
Back up and restore data provides instructions on backing up and restoring data related to Docker.

---

Manuals

Docker Desktop

Setup

Install

Windows
Install Docker Desktop on Windows

Docker Desktop terms

Commercial use of Docker Desktop in larger enterprises (more than 250 employees OR more than $10 million USD in annual revenue) requires a paid subscription.

This page provides download links, system requirements, and step-by-step installation instructions for Docker Desktop on Windows.

Docker Desktop for Windows - x86_64 Docker Desktop for Windows - x86_64 on the Microsoft Store Docker Desktop for Windows - Arm (Early Access)

For checksums, see Release notes

Installation modes

Docker Desktop supports two installation modes. Per-user installation (Beta) is recommended for most users. It does not require administrator privileges to install or update, and the WSL 2 backend it uses covers the needs of the vast majority of Docker Desktop users.

	Per-user (recommended)	All users
Install location	%LOCALAPPDATA%\Programs\DockerDesktop	C:\Program Files\Docker\Docker
Registry keys	Current User (HKCU)	Local Machine (HKLM)
Admin rights to install	Not required	Required
Admin rights to update	Not required	Required
Linux containers backend	WSL 2 only	WSL 2 or Hyper-V
Windows containers	Not supported	Supported
Security	Smaller attack surface; no privileged system service installed	Requires privileged system service; broader access to host resources

For more information, see Understand permission requirements for Windows.

System requirements
Tip

Should I use Hyper-V or WSL?

Docker Desktop's functionality remains consistent on both WSL and Hyper-V, without a preference for either architecture. Hyper-V and WSL have their own advantages and disadvantages, depending on your specific setup and your planned use case. Note that Hyper-V is only available with all-users installation. If you install Docker Desktop in per-user mode, WSL 2 is the only supported backend.

WSL 2 backend, x86_64 Hyper-V backend, x86_64 WSL 2 backend, Arm (Early Access)
WSL version 2.1.5 or later. To check your version, see WSL: Verification and setup
If you intend to use Enhanced Container Isolation, ensure you’re using WSL version 2.6 or later. This is required because ECI depends on a Linux kernel version of at least 6.3.0, and WSL 2.6+ bundles Linux kernel version 6.6.
Windows 10 64-bit: Enterprise, Pro, or Education version 22H2 (build 19045).
Windows 11 64-bit: Enterprise, Pro, or Education version 23H2 (build 22631) or higher.
The Windows Server service (LanmanServer) must be enabled and its start mode set to Automatic.
Turn on the WSL 2 feature on Windows. For detailed instructions, refer to the Microsoft documentation.
The following hardware prerequisites are required to successfully run WSL 2 on Windows 10 or Windows 11:
64-bit processor with Second Level Address Translation (SLAT)
8GB system RAM
Enable hardware virtualization in BIOS/UEFI. For more information, see Virtualization.

For more information on setting up WSL 2 with Docker Desktop, see WSL.

Note

Docker only supports Docker Desktop on Windows for those versions of Windows that are still within Microsoft’s servicing timeline. Docker Desktop is not supported on server versions of Windows, such as Windows Server 2019 or Windows Server 2022. For more information on how to run containers on Windows Server, see Microsoft's official documentation.

Important

To run Windows containers, you need Windows 10 or Windows 11 Professional or Enterprise edition. Windows Home or Education editions only allow you to run Linux containers.

Containers and images created with Docker Desktop are shared between all user accounts on machines where it is installed. This is because all Windows accounts use the same VM to build and run containers. Note that it is not possible to share containers and images between user accounts when using the Docker Desktop WSL 2 backend.

Running Docker Desktop inside a VMware ESXi or Azure VM is supported for Docker Business customers. It requires enabling nested virtualization on the hypervisor first. For more information, see Running Docker Desktop in a VM or VDI environment.

Install Docker Desktop on Windows
Install interactively

Download the installer using the download button at the top of the page, or from the release notes.

Double-click Docker Desktop Installer.exe to run the installer. The installer will ask which installation mode you prefer. Choosing per-user installs to %LOCALAPPDATA%\Programs\DockerDesktop and requires no administrator privileges. Choosing all users will prompt for elevation.

Note

If you want to switch installation mode at a later date, you need to uninstall and reinstall Docker Desktop.

When prompted, ensure the Use WSL 2 instead of Hyper-V option on the Configuration page is selected or not depending on your choice of backend.

On systems that support only one backend, Docker Desktop automatically selects the available option.

Follow the instructions on the installation wizard to authorize the installer and proceed with the installation.

When the installation is successful, select Close to complete the installation process.

Start Docker Desktop.

Install from the command line

After downloading Docker Desktop Installer.exe, run the following command in a terminal to install Docker Desktop to %LOCALAPPDATA%\Programs\DockerDesktop.

For per-user installation, run:

$ "Docker Desktop Installer.exe" install --user

To install for all users on the machine (requires administrator privileges):

$ "Docker Desktop Installer.exe" install

If you're using PowerShell you should run it as:

# Per-user installation (no admin required)

Start-Process 'Docker Desktop Installer.exe' -Wait -ArgumentList 'install', '--user'

 

# All-users installation (run as administrator)

Start-Process 'Docker Desktop Installer.exe' -Wait install

If using the Windows Command Prompt:

# Per-user installation (no admin required)

start /w "" "Docker Desktop Installer.exe" install --user

 

# All-users installation (run as administrator)

start /w "" "Docker Desktop Installer.exe" install

If using all-users installation and your administrator account is different to your user account, you must add the user to the docker-users group to access features that require higher privileges, such as creating and managing the Hyper-V VM, or using Windows containers:

$ net localgroup docker-users <user> /add

See the Installer flags section to see what flags the install command accepts.

Note

If you want to switch installation mode at a later date, you need to uninstall and reinstall Docker Desktop.

Start Docker Desktop

Docker Desktop does not start automatically after installation. To start Docker Desktop:

Search for Docker, and select Docker Desktop in the search results.

The Docker menu (  ) displays the Docker Subscription Service Agreement.

Here’s a summary of the key points:

Docker Desktop is free for small businesses (fewer than 250 employees AND less than $10 million in annual revenue), personal use, education, and non-commercial open source projects.
Otherwise, it requires a paid subscription for professional use.
Paid subscriptions are also required for government entities.
The Docker Pro, Team, and Business subscriptions include commercial use of Docker Desktop.

Select Accept to continue. Docker Desktop starts after you accept the terms.

Note that Docker Desktop won't run if you do not agree to the terms. You can choose to accept the terms at a later date by opening Docker Desktop.

For more information, see Docker Desktop Subscription Service Agreement. It is recommended that you read the FAQs.

Tip

As an IT administrator, you can use endpoint management (MDM) software to identify the number of Docker Desktop instances and their versions within your environment. This can provide accurate license reporting, help ensure your machines use the latest version of Docker Desktop, and enable you to enforce sign-in.

Intune
Jamf
Kandji
Kolide
Workspace One
Advanced system configuration and installation options
WSL: Verification and setup

If you have chosen to use WSL, first verify that your installed version meets system requirements by running the following command in your terminal:

wsl --version

If version details do not appear, you are likely using the inbox version of WSL. This version does not support modern capabilities and must be updated.

You can update or install WSL using one of the following methods:

Option 1: Install or update WSL via the terminal
Open PowerShell or Windows Command Prompt in administrator mode.
Run either the install or update command. You may be prompted to restart your machine. For more information, refer to Install WSL.
wsl --install

wsl --update

Option 2: Install WSL via the MSI package

If Microsoft Store access is blocked due to security policies:

Go to the official WSL GitHub Releases page.
Download the .msi installer from the latest stable release (under the Assets drop-down).
Run the downloaded installer and follow the setup instructions.
Installer flags
Note

If you're using PowerShell, you need to use the ArgumentList parameter before any flags. For example:

Start-Process 'Docker Desktop Installer.exe' -Wait -ArgumentList 'install', '--accept-license'
Installation behavior
--user: Installs Docker Desktop in per-user mode, to %LOCALAPPDATA%\Programs\DockerDesktop. No administrator privileges are required. This is the recommended mode for most users. See Installation modes.
--quiet: Suppresses information output when running the installer
--accept-license: Accepts the Docker Subscription Service Agreement now, rather than requiring it to be accepted when the application is first run
--installation-dir=<path>: Changes the default installation location (C:\Program Files\Docker\Docker)
--backend=<backend name>: Selects the default backend to use for Docker Desktop, hyper-v, windows or wsl-2 (default)
--always-run-service: After installation completes, starts com.docker.service and sets the service startup type to Automatic. This circumvents the need for administrator privileges, which are otherwise necessary to start com.docker.service. com.docker.service is required by Windows containers and Hyper-V backend.
Security and access control
--allowed-org=<org name>: Requires the user to sign in and be part of the specified Docker Hub organization when running the application
--admin-settings: Automatically creates an admin-settings.json file which is used by admins to control certain Docker Desktop settings on client machines within their organization. For more information, see Settings Management.
It must be used together with the --allowed-org=<org name> flag.
For example:--allowed-org=<org name> --admin-settings="{'configurationFileVersion': 2, 'enhancedContainerIsolation': {'value': true, 'locked': false}}"
--no-windows-containers: Disables the Windows containers integration. This can improve security. For more information, see Windows containers.
Proxy configuration
--proxy-http-mode=<mode>: Sets the HTTP Proxy mode, system (default) or manual
--override-proxy-http=<URL>: Sets the URL of the HTTP proxy that must be used for outgoing HTTP requests, requires --proxy-http-mode to be manual
--override-proxy-https=<URL>: Sets the URL of the HTTP proxy that must be used for outgoing HTTPS requests, requires --proxy-http-mode to be manual
--override-proxy-exclude=<hosts/domains>: Bypasses proxy settings for the hosts and domains. Uses a comma-separated list.
--proxy-enable-kerberosntlm: Enables Kerberos and NTLM proxy authentication. If you are enabling this, ensure your proxy server is properly configured for Kerberos/NTLM authentication. Available with Docker Desktop 4.32 and later.
--override-proxy-pac=<PAC file URL>: Sets the PAC file URL. This setting takes effect only when using manual proxy mode.
--override-proxy-embedded-pac=<PAC script>: Specifies an embedded PAC (Proxy Auto-Config) script. This setting takes effect only when using manual proxy mode and has precedence over the --override-proxy-pac flag.
Example of specifying PAC file
"Docker Desktop Installer.exe" install --proxy-http-mode="manual" --override-proxy-pac="http://localhost:8080/myproxy.pac"

Example of specifying PAC script
"Docker Desktop Installer.exe" install --proxy-http-mode="manual" --override-proxy-embedded-pac="function FindProxyForURL(url, host) { return \"DIRECT\"; }"

Data root and disk location
--hyper-v-default-data-root=<path>: Specifies the default location for the Hyper-V VM disk.
--windows-containers-default-data-root=<path>: Specifies the default location for the Windows containers.
--wsl-default-data-root=<path>: Specifies the default location for the WSL distribution disk.
Administrator privileges

In per-user mode, Docker Desktop can be installed and updated without administrator privileges. Some settings still require elevation and are marked Requires password in the Settings UI. Enabling WSL 2 for the first time also requires administrator privileges, but this is a one-time, per-machine operation.

In all-users mode, installing Docker Desktop requires administrator privileges. However, once installed, it can be used without administrative access. Some actions, though, still need elevated permissions. See Understand permission requirements for Windows for more detail.

See the FAQs on how to install and run Docker Desktop without needing administrator privileges.

If you're an IT admin and your users do not have administrator rights and plan to perform operations that require elevated privileges, be sure to install Docker Desktop using the --always-run-service installer flag. This ensures those actions can still be executed without prompting for User Account Control (UAC) elevation. See Installer Flags for more detail.

Windows containers
Note

Windows containers are only supported in all-users installation mode. They are not available when Docker Desktop is installed per-user.

From the Docker Desktop menu, you can toggle which daemon (Linux or Windows) the Docker CLI talks to. Select Switch to Windows containers to use Windows containers, or select Switch to Linux containers to use Linux containers (the default).

For more information on Windows containers, refer to the following documentation:

Microsoft documentation on Windows containers.

Build and Run Your First Windows Server Container (Blog Post) gives a quick tour of how to build and run native Docker Windows containers on Windows 10 and Windows Server 2016 evaluation releases.

Getting Started with Windows Containers (Lab) shows you how to use the MusicStore application with Windows containers. The MusicStore is a standard .NET application and, forked here to use containers, is a good example of a multi-container application.

To understand how to connect to Windows containers from the local host, see I want to connect to a container from Windows

Note

When you switch to Windows containers, Settings only shows those tabs that are active and apply to your Windows containers.

If you set proxies or daemon configuration in Windows containers mode, these apply only on Windows containers. If you switch back to Linux containers, proxies and daemon configurations return to what you had set for Linux containers. Your Windows container settings are retained and become available again when you switch back.

Where to go next
Explore Docker's subscriptions to see what Docker can offer you.
Get started with Docker.
Explore Docker Desktop and all its features.
Troubleshooting describes common problems, workarounds, and how to get support.
FAQs provide answers to frequently asked questions.
Release notes lists component updates, new features, and improvements associated with Docker Desktop releases.
Back up and restore data provides instructions on backing up and restoring data related to Docker.

---

Manuals

Docker Desktop

Setup

Install

Linux
Install Docker Desktop on Linux

Docker Desktop terms

Commercial use of Docker Desktop in larger enterprises (more than 250 employees or more than $10 million USD in annual revenue) requires a paid subscription.

This page contains information about general system requirements, supported platforms, and instructions on how to install Docker Desktop for Linux.

Important

Docker Desktop on Linux runs a Virtual Machine (VM) which creates and uses a custom docker context, desktop-linux, on startup.

This means images and containers deployed on the Linux Docker Engine (before installation) are not available in Docker Desktop for Linux.

Docker Desktop vs Docker Engine: What's the difference?
Supported platforms

Docker provides .deb and .rpm packages for the following Linux distributions and architectures:

Platform	x86_64 / amd64
Ubuntu	✅
Debian	✅
Red Hat Enterprise Linux (RHEL)	✅
Fedora	✅

An experimental package is available for Arch-based distributions. Docker has not tested or verified the installation.

Docker supports Docker Desktop on the current and previous LTS releases of the aforementioned distributions, as well as the most recent version.

General system requirements

To install Docker Desktop successfully, your Linux host must meet the following general requirements:

64-bit kernel and CPU support for virtualization.
KVM virtualization support. Follow the KVM virtualization support instructions to check if the KVM kernel modules are enabled and how to provide access to the KVM device.
QEMU must be version 5.2 or later. We recommend upgrading to the latest version.
systemd init system.
GNOME, KDE, or MATE desktop environments are supported but others may work.
For many Linux distributions, the GNOME environment does not support tray icons. To add support for tray icons, you need to install a GNOME extension. For example, AppIndicator.
At least 4 GB of RAM.
Enable configuring ID mapping in user namespaces, see File sharing. Note that for Docker Desktop version 4.35 and later, this is not required anymore.
Recommended: Initialize pass for credentials management.

Docker Desktop for Linux runs a Virtual Machine (VM). For more information on why, see Why Docker Desktop for Linux runs a VM.

Note

Docker does not provide support for running Docker Desktop for Linux in nested virtualization scenarios. We recommend that you run Docker Desktop for Linux natively on supported distributions.

KVM virtualization support

Docker Desktop runs a VM that requires KVM support.

The kvm module should load automatically if the host has virtualization support. To load the module manually, run:

$ modprobe kvm

Depending on the processor of the host machine, the corresponding module must be loaded:

$ modprobe kvm_intel  # Intel processors

$ modprobe kvm_amd    # AMD processors

If the above commands fail, you can view the diagnostics by running:

$ kvm-ok

To check if the KVM modules are enabled, run:

$ lsmod | grep kvm

kvm_amd               167936  0

ccp                   126976  1 kvm_amd

kvm                  1089536  1 kvm_amd

irqbypass              16384  1 kvm

Set up KVM device user permissions

To check ownership of /dev/kvm, run :

$ ls -al /dev/kvm

Add your user to the kvm group in order to access the kvm device:

$ sudo usermod -aG kvm $USER

Sign out and sign back in so that your group membership is re-evaluated.

Using Docker SDKs with Docker Desktop

Docker Desktop for Linux uses a per-user socket instead of the system-wide /var/run/docker.sock. Docker SDKs and tools that connect directly to the Docker daemon need the DOCKER_HOST environment variable set to connect to Docker Desktop. For configuration details, see How do I use Docker SDKs with Docker Desktop for Linux?.

Where to go next
Install Docker Desktop for Linux for your specific Linux distribution:
Install on Ubuntu
Install on Debian
Install on Red Hat Enterprise Linux (RHEL)
Install on Fedora
Install on Arch

---
