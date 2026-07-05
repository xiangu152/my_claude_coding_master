---
title: "Docker 管理控制台"
source: "https://docs.docker.com/admin/"
version: "latest"
---

# Docker 管理控制台

> 原始文档来源：https://docs.docker.com/admin/

---

## Activity logs

# Activity logs

Activity logs display a chronological list of activities that occur at organization and repository levels. The activity log provides organization owners with a record of all
member activities.

With activity logs, owners can view and track:

 - What changes were made
 - The date when a change was made
 - Who initiated the change

For example, activity logs display activities such as the date when a repository was created or deleted, the member who created the repository, the name of the repository, and when there was a change to the privacy settings.

Owners can also see the activity logs for their repository if the repository is part of the organization subscribed to a Docker Business or Team subscription.

## Access activity logs

**Docker Home**

To view activity logs in Docker Home:

1. Sign in to [Docker Home](https://app.docker.com) and select your
organization.
1. Select **Admin Console**, then **Activity logs**.

**API**

To view activity logs using the Docker Hub API, use the [Audit logs endpoints](https://docs.docker.com/reference/api/hub/latest/#tag/audit-logs).

## Filter and customize activity logs

> [!IMPORTANT]
>
> Docker Home retains activity logs for 30 days. To retrieve
activities beyond 30 days, you must use the
[Docker Hub API](https://docs.docker.com/reference/api/hub/latest/#tag/audit-logs).

By default, the **Activity** tab displays all recorded events within
the last 30 days. To narrow your view, use the calendar to select a specific
date range. The log updates to show only the activities that occurred during
that period.

You can also filter by activity type. Use the **All Activities** drop-down to
focus on organization-level, repository-level, or billing-related events.
In Docker Hub, when viewing a repository, the **Activities** tab only shows
events for that repository.

After selecting a category鈥�**Organization**, **Repository**, or **Billing**鈥攗se
the **All Actions** drop-down to refine the results even further by specific
event type.

> [!NOTE]
>
> Events triggered by Docker Support appear under the username **dockersupport**.

## Types of activity log events

Refer to the following section for a list of events and their descriptions:

### Organization events

| Event                                                          | Description                                   |
|:------------------------------------------------------------------|:------------------------------------------------|
| Team Created | Activities related to the creation of a team |
| Team Updated | Activities related to the modification of a team |
| Team Deleted | Activities related to the deletion of a team |
| Team Member Added | Details of the member added to your team |
| Team Member Removed | Details of the member removed from your team |
| Team Member Invited | Details of the member invited to your team |
| Organization Member Added | Details of the member added to your organization |
| Organization Member Removed | Details about the member removed from your organization |
| Member Role Changed | Details about the role changed for a member in your organization |
| Organization Created | Activities related to the creation of a new organization |
| Organization Settings Updated | Details related to the organization setting that was updated |
| Registry Access Management enabled | Activities related to enabling Registry Access Management |
| Registry Access Management disabled | Activities related to disabling Registry Access Management |
| Registry Access Management registry added | Activities related to the addition of a registry |
| Registry Access Management registry removed | Activities related to the removal of a registry |
| Registry Access Management registry updated | Details related to the registry that was updated |
| Single Sign-On domain added | Details of the single sign-on domain added to your organization |
| Single Sign-On domain removed | Details of the single sign-on domain removed from your organization |
| Single Sign-On domain verified | Details of the single sign-on domain verified for your organization |
| Access token created | Access token created in organization |
| Access token updated | Access token updated in organization |
| Access token deleted | Access token deleted in organization |
| Policy created | Details of adding a settings policy |
| Policy updated | Details of updating a settings policy |
| Policy deleted | Details of deleting a settings policy |
| Policy transferred | Details of transferring a settings policy to another owner |
| Create SSO Connection | Details of creating a new org/company SSO connection |
| Update SSO Connection | Details of updating an existing org/company SSO connection |
| Delete SSO Connection | Details of deleting an existing org/company SSO connection |
| Enforce SSO | Details of toggling enforcement on an existing org/company SSO connection |
| Enforce SCIM | Details of toggling SCIM on an existing org/company SSO connection |
| Refresh SCIM Token | Details of a SCIM token refresh on an existing org/company SSO connection |
| Change SSO Connection Type | Details of a connection type change on an existing org/company SSO connection |
| Toggle JIT provisioning | Details of a JIT toggle on an existing org/company SSO connection |

### Repository events

> [!NOTE]
>
> Event descriptions that include a user action can refer to a Docker username, personal access token (PAT) or organization access token (OAT). For example, if a user pushes a tag to a repository, the event would include the description: `<user-access-token>` pushed the tag to the repository.

| Event                                                          | Description                                   |
|:------------------------------------------------------------------|:------------------------------------------------|
| Repository Created | Activities related to the creation of a new repository |
| Repository Deleted | Activities related to the deletion of a repository |
| Repository Updated | Activities related to updating the description, full description, or status of a repository |
| Privacy Changed | Details related to the privacy policies that were updated |
| Tag Pushed | Activities related to the tags pushed |
| Tag Deleted | Activities related to the tags deleted |
| Categories Updated | Activities related to setting or updating categories of a repository |

### Billing events

| Event                                                          | Description                                   |
|:------------------------------------------------------------------|:------------------------------------------------|
| Plan Upgraded | Occurs when your organization鈥檚 billing plan is upgraded to a higher tier plan.|
| Plan Downgraded | Occurs when your organization鈥檚 billing plan is downgraded to a lower tier plan. |
| Seat Added | Occurs when a seat is added to your organization鈥檚 billing plan. |
| Seat Removed | Occurs when a seat is removed from your organization鈥檚 billing plan. |
| Billing Cycle Changed | Occurs when there is a change in the recurring interval that your organization is charged.|
| Plan Downgrade Canceled | Occurs when a scheduled plan downgrade for your organization is canceled.|
| Seat Removal Canceled | Occurs when a scheduled seat removal for an organization鈥檚 billing plan is canceled. |
| Plan Upgrade Requested | Occurs when a user in your organization requests a plan upgrade. |
| Plan Downgrade Requested | Occurs when a user in your organization requests a plan downgrade. |
| Seat Addition Requested | Occurs when a user in your organization requests an increase in the number of seats. |
| Seat Removal Requested | Occurs when a user in your organization requests a decrease in the number of seats. |
| Billing Cycle Change Requested | Occurs when a user in your organization requests a change in the billing cycle. |
| Plan Downgrade Cancellation Requested | Occurs when a user in your organization requests a cancellation of a scheduled plan downgrade. |
| Seat Removal Cancellation Requested | Occurs when a user in your organization requests a cancellation of a scheduled seat removal. |

### Offload events

> [!NOTE]
>
> Event descriptions show the Docker username of the actor and details about the lease.

| Event                                                          | Description                                   |
|:------------------------------------------------------------------|:------------------------------------------------|
| Offload Lease Start | Occurs when an Offload lease is started in your organization. |
| Offload Lease End | Occurs when an Offload lease is ended in your organization. |

---

## Company FAQs

# Company FAQs

### Some of my organizations don鈥檛 have a Docker Business subscription. Can I still use a parent company?

Yes, but you can only add organizations with a Docker Business subscription
to a company.

### What happens if one of my organizations downgrades from Docker Business, but I still need access as a company owner?

To access and manage child organizations, the organization must have a
Docker Business subscription. If the organization isn鈥檛 included in this
subscription, the owner of the organization must manage the organization
outside of the company.

### Do company owners occupy a subscription seat?

Company owners do not occupy a seat unless one of the following is true:

- They are added as a member of an organization under your company
- SSO is enabled and the company owner signs in via SSO, which automatically adds them as an organization member

Although company owners have the same access as organization owners across all
organizations in the company, it's not necessary to add them to any
organization. Doing so will cause them to occupy a seat.

When you first create a company, your account is both a company owner and an
organization owner. In that case, your account will occupy a seat as long as
you remain an organization owner.

To avoid occupying a seat, [assign another user as the organization owner](/admin/organization/manage/members/#update-a-member-role) and remove yourself from the organization.
You'll retain full administrative access as a company owner without using a
subscription seat.

### What permissions does the company owner have in the associated/nested organizations?

Company owners can navigate to the **Organizations** page to view all their
nested organizations in a single location. They can also view or edit organization members and change single sign-on (SSO) and System for Cross-domain Identity Management (SCIM) settings. Changes to company settings impact all users in each organization under the company.

For more information, see [Roles and permissions](/enterprise/security/roles-and-permissions/).

---

## Manage company organizations

# Manage company organizations

Learn to manage the organizations in a company using the Docker Admin Console.

## View all organizations

1. Sign in to the [Docker Home](https://app.docker.com) and choose
   your company.
1. Select **Admin Console**, then **Organizations**.

The **Organizations** view displays all organizations under your company.

## Add seats to an organization

If you have a self-serve subscription that has no pending subscription changes,
you can add seats using Docker Home. For more information about adding seats,
see [Manage seats](/admin/organization/manage/manage-seats/#add-seats-to-your-subscription).

If you have a sales-assisted subscription, you must contact Docker support or
sales to add seats.

## Add organizations to a company

To add an organization to a company, ensure the following:

- You are a company owner.
- You are an organization owner of the organization you want to add.
- The organization has a Docker Business subscription.
- There鈥檚 no limit to how many organizations can exist under a company.

> [!IMPORTANT]
>
> Once you add an organization to a company, you can't remove it from the
> company.

1. Sign in to [Docker Home](https://app.docker.com) and select your company from
   the top-left account drop-down.
1. Select **Admin Console**, then **Organizations**.
1. Select **Add organization**.
1. Choose the organization you want to add from the drop-down menu.
1. Select **Add organization** to confirm.

## Manage an organization

1. Sign in to [Docker Home](https://app.docker.com) and select your company from
   the top-left account drop-down.
1. Select **Admin Console**, then **Organizations**.
1. Select the organization you want to manage.

For more details about managing an organization, see
[Organization administration](/admin/organization/).

## More resources

- [Video: Managing a company and nested organizations](https://youtu.be/XZ5_i6qiKho?feature=shared&t=229)
- [Video: Adding nested organizations to a company](https://youtu.be/XZ5_i6qiKho?feature=shared&t=454)

---

## Manage company owners

# Manage company owners

A company can have multiple owners. Company owners have visibility across the
entire company and can manage settings that apply to all organizations under
that company. They also have the same access rights as organization owners but
don鈥檛 need to be members of any individual organization.

> [!IMPORTANT]
>
> Company owners do not occupy a seat unless they are added as a member of an
> organization under your company, or SSO is enabled and the company owner signs
> in via SSO (which automatically adds them as an organization member).

## Add a company owner

1. Sign in to [Docker Home](https://app.docker.com) and select your company from
   the top-left account drop-down.
1. Select **Admin Console**, then **Company owners**.
1. Select **Add owner**.
1. Specify the user's Docker ID to search for the user.
1. After you find the user, select **Add company owner**.

## Remove a company owner

1. Sign in to [Docker Home](https://app.docker.com) and select your company from
   the top-left account drop-down.
1. Select **Admin Console**, then **Company owners**.
1. Locate the company owner you want to remove and select the **Actions** menu.
1. Select **Remove as company owner**.

---

## Manage company members

# Manage company members

Add a user to your company by inviting them to be a member of a company organization. Company owners can invite new members to an organization via Docker ID,
email address, or in bulk with a CSV file containing email
addresses.

If an invitee does not have a Docker account, they must create an account and
verify their email address before they can accept an invitation to join the
organization. Pending invitations occupy seats for the organization
the user is invited to.

## Invite members via Docker ID or email address

Use the following steps to invite members to your organization via Docker ID or
email address.

1. Sign in to [Docker Home](https://app.docker.com) and select
   your company.
1. On the **Organizations** page, select the organization you want
   to invite members to.
1. Select **Members**, then **Invite**.
1. Select **Emails or usernames**.
1. Follow the on-screen instructions to invite members.
   Invite a maximum of 1000 members and separate multiple entries by comma,
   semicolon, or space.

   > [!NOTE]
   >
   > When you invite members, you assign them a role.
   > See [Roles and permissions](/enterprise/security/roles-and-permissions/core-roles/)
   > for details about the access permissions for each role.

   Pending invitations appear on the Members page. The invitees receive an
   email with a link to Docker Hub where they can accept or decline the
   invitation.

## Invite members via CSV file

To invite multiple members to an organization via a CSV file containing email
addresses:

1. Sign in to [Docker Home](https://app.docker.com) and select
   your company.
1. On the **Organizations** page, select the organization you want
   to invite members to.
1. Select **Members**, then **Invite**.
1. Select **CSV upload**.
1. Select **Download the template CSV file** to optionally download an example
   CSV file. The following is an example of the contents of a valid CSV file.

   ```text
   email
   docker.user-0@example.com
   docker.user-1@example.com
   ```

   CSV file requirements:
   - The file must contain a header row with at least one heading named `email`.
     Additional columns are allowed and are ignored in the import.
   - The file must contain a maximum of 1000 email addresses (rows). To invite
     more than 1000 users, create multiple CSV files and perform all steps in
     this task for each file.

1. Create a new CSV file or export a CSV file from another application.
   - To export a CSV file from another application, see the application鈥檚
     documentation.
   - To create a new CSV file, open a new file in a text editor, type `email`
     on the first line, type the user email addresses one per line on the
     following lines, and then save the file with a .csv extension.

1. Select **Browse files** and then select your CSV file, or drag and drop the
   CSV file into the **Select a CSV file to upload** box. You can only select
   one CSV file at a time.

   > [!NOTE]
   >
   > If the amount of email addresses in your CSV file exceeds the number of
   > available seats in your organization, you cannot continue to invite members.
   > To invite members, you can purchase more seats, or remove some email
   > addresses from the CSV file and re-select the new file. To purchase more
   > seats, see [Add seats to your subscription](/admin/organization/manage/manage-seats/#add-seats-to-your-subscription) or
   > <a href="https://www.docker.com/pricing/contact-sales/" id="dkr_docs_cs_admin_invite_users" class="link" rel="noopener">Contact sales</a>.

1. After the CSV file has been uploaded, select **Review**.

   Valid email addresses and any email addresses that have issues will appear.
   Email addresses may have the following issues:
   - Invalid email: The email address is not a valid address. The email address
     will be ignored if you send invites. You can correct the email address in
     the CSV file and re-import the file.
   - Already invited: The user has already been sent an invite email and another
     invite email will not be sent.
   - Member: The user is already a member of your organization and an invite
     email will not be sent.
   - Duplicate: The CSV file has multiple occurrences of the same email address.
     The user will be sent only one invite email.

1. Follow the on-screen instructions to invite members.

   > [!NOTE]
   >
   > When you invite members, you assign them a role.
   > See [Roles and permissions](/enterprise/security/roles-and-permissions/core-roles/)
   > for details about the access permissions for each role.

Pending invitations appear on the Members page. The invitees receive an email
with a link to Docker Hub where they can accept or decline the invitation.

## Resend invitations to users

You can resend individual invitations, or bulk invitations from the Admin Console.

### Resend individual invitations

1. In [Docker Home](https://app.docker.com/), select your company from
   the top-left account drop-down.
2. Select **Admin Console**, then **Users**.
3. Select the **action menu** next to the invitee and select **Resend**.
4. Select **Invite** to confirm.

### Bulk resend invitation

1. In [Docker Home](https://app.docker.com/), select your company from
   the top-left account drop-down.
2. Select **Admin Console**, then **Users**.
3. Use the **checkboxes** next to **Usernames** to bulk select users.
4. Select **Resend invites**.
5. Select **Resend** to confirm.

## Invite members via API

You can bulk invite members using the Docker Hub API. For more information,
see the [Bulk create invites](https://docs.docker.com/reference/api/hub/latest/#tag/invites/paths/~1v2~1invites~1bulk/post) API endpoint.

## Manage members on a team

Teams exist at the organization level, not the company level. After inviting members to an organization, you can add them to teams within that organization using Docker Hub. For more details, see [Manage members](/admin/organization/manage/members/#manage-members-on-a-team).

---

## Create new company

# Create new company

Learn how to create a new company in the Docker Admin Console, a centralized
dashboard for managing organizations.

## Prerequisites

Before you begin, you must:

- Be the owner of the organization you want to add to your company
- Have a Docker Business subscription

## Create a company

To create a new company:

1. Sign in to [Docker Home](https://app.docker.com/) and select your
   organization.
1. Select **Admin Console**, then **Company management**.
1. Select **Create a company**.
1. Enter a unique name for your company, then select **Continue**.

   > [!TIP]
   >
   > The name for your company can't be the same as an existing user,
   > organization, or company namespace.

1. Review the migration details and then select **Create company**.

For more information on how you can add organizations to your company,
see [Add organizations to a company](/admin/company/manage/organizations/#add-organizations-to-a-company).

## Next steps

- [Manage organizations](/admin/company/manage/organizations/)
- [Manage company members](/admin/company/manage/users/)
- [Manage company owners](/admin/company/manage/owners/)

## More resources

- [Video: Create a company](https://youtu.be/XZ5_i6qiKho?feature=shared&t=359)

---

## Insights

# Insights

Insights helps administrators visualize and understand how Docker is used within
their organizations. With Insights, administrators can ensure their teams are
fully equipped to utilize Docker to its fullest potential, leading to improved
productivity and efficiency across the organization.

Key benefits include:

- Uniform working environment: Establish and maintain standardized
  configurations across teams.
- Best practices: Promote and enforce usage guidelines to ensure optimal
  performance.
- Increased visibility: Monitor and drive adoption of organizational
  configurations and policies.
- Optimized license use: Ensure that developers have access to advanced
  features provided by a Docker subscription.

## Prerequisites

To use Insights, you must meet the following requirements:

- [Docker Business subscription](https://www.docker.com/pricing?ref=Docs&refAction=DocsAdminInsights)
- Administrators must [enforce sign-in](/enterprise/security/enforce-sign-in/)
  for users
- Your Account Executive must turn on Insights for your organization

## View Insights for organization users

To access Insights, contact your Account Executive to turn on the feature. Once enabled, access Insights using the
following steps:

1. Sign in to [Docker Home](https://app.docker.com/) and choose
   your organization.
2. Select **Insights**, then select a time period for the data you want to view.

> [!NOTE]
>
> Insights data is not real-time and is updated daily. At the top-right of the
> Insights page, view the **Last updated** date to understand when the data was
> last updated.

### Docker Desktop users

Track active Docker Desktop users in your domain, differentiated by license
status. This chart helps you understand the engagement levels within your
organization, providing insights into how many users are actively using Docker
Desktop. Note that users who opt out of analytics aren't included in the active
counts.

The chart contains the following data:

| Data                         | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         |
| :--------------------------- | :---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Active user                  | The number of users who have actively used Docker Desktop and either signed in with a Docker account that has a license in your organization or signed in to a Docker account with an email address from a domain associated with your organization. <br><br>Users who don鈥檛 sign in to an account associated with your organization are not represented in the data. To ensure users sign in with an account associated with your organization, you can [enforce sign-in](/enterprise/security/enforce-sign-in/). |
| Total organization members   | The number of users who have used Docker Desktop, regardless of their Insights activity.                                                                                                                                                                                                                                                                                                                                                                                                                                            |
| Users opted out of analytics | The number of users who are members of your organization that have opted out of sending analytics. <br><br>When users opt out of sending analytics, you won't see any of their data in Insights. To ensure that the data includes all users, you can use [Settings Management](/enterprise/security/hardened-desktop/settings-management/) to set `analyticsEnabled` for all your users.                                                                                                                           |
| Active users (graph)         | The view over time for total active users.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |

### Builds

Monitor development efficiency and the time your team invests in builds with
this chart. It provides a clear view of the build activity, helping you identify
patterns, optimize build times, and enhance overall development productivity.

The chart contains the following data:

| Data                   | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| :--------------------- | :------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| Average build per user | The average number of builds per active user. A build includes any time a user runs one of the following commands: <ul><li>`docker build`</li><li>`docker buildx b`</li><li>`docker buildx bake`</li><li>`docker buildx build`</li><li>`docker buildx f`</li><li>`docker builder b`</li><li>`docker builder bake`</li><li>`docker builder build`</li><li>`docker builder f`</li><li>`docker compose build`</li><li>`docker compose up --build`</li><li>`docker image build`</li></ul> |
| Average build time     | The average build time per build.                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
| Build success rate     | The percentage of builds that were successful out of the total number of builds. A successful build includes any build that exits normally.                                                                                                                                                                                                                                                                                                                                           |
| Total builds (graph)   | The total number of builds separated into successful builds and failed builds. A successful build includes any build that exits normally. A failed build includes any build that exits abnormally.                                                                                                                                                                                                                                                                                    |

### Containers

View the total and average number of containers run by users with this chart. It
lets you gauge container usage across your organization, helping you understand
usage trends and manage resources effectively.

The chart contains the following data:

| Data                                   | Description                                                                                                                                                                |
| :------------------------------------- | :------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Total containers run                   | The total number of containers run by active users. Containers run include those run using the Docker Desktop graphical user interface, `docker run`, or `docker compose`. |
| Average number of containers run       | The average number of containers run per active user.                                                                                                                      |
| Containers run by active users (graph) | The number of containers run over time by active users.                                                                                                                    |

### Docker Desktop usage

Explore Docker Desktop usage patterns with this chart to optimize your team's
workflows and ensure compatibility. It provides valuable insights into how
Docker Desktop is being utilized, enabling you to streamline processes and
improve efficiency.

The chart contains the following data:

| Data                              | Description                                                                                                                                                                                                                                                                       |
| :-------------------------------- | :-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Most used version                 | The most used version of Docker Desktop by users in your organization.                                                                                                                                                                                                            |
| Most used OS                      | The most used operating system by users.                                                                                                                                                                                                                                          |
| Versions by active users (graph)  | The number of active users using each version of Docker Desktop. <br><br>To learn more about each version and release dates, see the [Docker Desktop release notes](/desktop/release-notes/).                                                                           |
| Interface by active users (graph) | The number of active users grouped into the type of interface they used to interact with Docker Desktop. <br><br>A CLI user is any active user who has run a `docker` command. A GUI user is any active user who has interacted with the Docker Desktop graphical user interface. |

### Docker Hub images

Analyze image distribution activity with this chart and view the most utilized
Docker Hub images within your domain. This information helps you manage image
usage, ensuring that the most critical resources are readily available and
efficiently used.

> [!NOTE]
>
> Data for images is only for Docker Hub. Data for third-party
> registries and mirrors aren't included.

The chart contains the following data:

| Data                 | Description                                                                                                     |
| :------------------- | :-------------------------------------------------------------------------------------------------------------- |
| Total pulled images  | The total number of images pulled by users from Docker Hub.                                                     |
| Total pushed images  | The total number of images pushed by users to Docker Hub.                                                       |
| Top 10 pulled images | A list of the top 10 images pulled by users from Docker Hub and the number of times each image has been pulled. |

### Extensions

Monitor extension installation activity with this chart. It provides visibility
into the Docker Desktop extensions your teams are using, letting you track
adoption and identify popular tools that enhance productivity.

The chart contains the following data:

| Data                                           | Description                                                                                                                                      |
| :--------------------------------------------- | :----------------------------------------------------------------------------------------------------------------------------------------------- |
| Percentage of org with extensions installed    | The percentage of users in your organization with at least one Docker Desktop extension installed.                                               |
| Top 5 extensions installed in the organization | A list of the top 5 Docker Desktop extensions installed by users in your organization and the number of users who have installed each extension. |

## Export Docker Desktop user data

You can export Docker Desktop user data as a CSV file:

1. Open [Docker Home](https://app.docker.com) and select your organization from the top-left account drop-down.
1. Select **Admin Console** in the left-hand navigation menu.
1. Select **Desktop insights**.
1. Choose a timeframe for your insights data: **1 Week**, **1 Month**, or
   **3 Months**.
1. Select **Export** and choose **Docker Desktop users** from the drop-down.

Your export will automatically download. Open the file to view
the export data.

### Understanding export data

A Docker Desktop user export file contains the following data points:

- Name: User's name
- Username: User's Docker ID
- Email: User's email address associated with their Docker ID
- Type: User type
- Role: User [role](/enterprise/security/roles-and-permissions/)
- Teams: Team(s) within your organization the user is a
  member of
- Date Joined: The date the user joined your organization
- Last Logged-In Date: The last date the user logged into Docker using
  their web browser (this includes Docker Hub and Docker Home)
- Docker Desktop Version: The version of Docker Desktop the user has
  installed
- Last Seen Date: The last date the user used the Docker Desktop application
- Opted Out Analytics: Whether the user has opted out of the
  [Send usage statistics](/enterprise/security/hardened-desktop/settings-management/settings-reference/#send-usage-statistics) setting in Docker Desktop

## Troubleshoot Insights

If you鈥檙e experiencing issues with data in Insights, consider the following
solutions to resolve common problems:

- Update users to the latest version of Docker Desktop.

  Data is not shown for users using versions 4.16 or lower of Docker Desktop.
  In addition, older versions may not provide all data. Ensure all users have
  installed the latest version of Docker Desktop.

- Turn on **Send usage statistics** in Docker Desktop for all your users.

  If users have opted out of sending usage statistics for Docker Desktop, then
  their usage data will not be a part of Insights. To manage the setting at
  scale for all your users, you can use [Settings
  Management](/enterprise/security/hardened-desktop/settings-management/) and turn on the
  `analyticsEnabled` setting.

- Ensure users use Docker Desktop and aren't using the standalone
  version of Docker Engine.

  Only Docker Desktop can provide data for Insights. If a user installs Docker
  Engine outside of Docker Desktop, Docker Engine won't provide
  data for that user.

- Make sure users sign in to an account associated with your
  organization.

  Users who don鈥檛 sign in to an account associated with your organization are
  not represented in the data. To ensure users sign in with an account
  associated with your organization, you can [enforce
  sign-in](/enterprise/security/enforce-sign-in/).

---

## Deactivate an organization

# Deactivate an organization

Learn how to deactivate a Docker organization, including required prerequisite
steps. For information about deactivating user
accounts, see [Deactivate a user account](/accounts/deactivate-user-account/).

> [!WARNING]
>
> All Docker products and services that use your Docker account or organization
> account will be inaccessible after deactivating your account.

## Prerequisites

You must complete all the following steps before you can deactivate your
organization:

- Download any images and tags you want to keep. Use `docker pull -a <image>`
  to pull all tags, or `docker pull <image>:<tag>` to pull a specific tag.
- If you have an active Docker subscription, [downgrade it to a free subscription](/subscription/plans/docker/#cancel-a-docker-plan).
- Remove all other members within the organization.
- Unlink your [GitHub and Bitbucket accounts](/docker-hub/repos/manage/builds/link-source/#unlink-a-github-user-account).
- For Business organizations, [remove your SSO connection](/enterprise/security/single-sign-on/manage/#delete-a-connection).

## Deactivate

You can deactivate your organization using either the Admin Console or
Docker Hub.

> [!WARNING]
>
> This cannot be undone. Be sure you've gathered all the data you need from
> your organization before deactivating it.

1. Sign in to [Docker Home](https://app.docker.com) and select the organization
   you want to deactivate.
1. Select **Admin Console**, then **Deactivate**. If the **Deactivate**
   button is unavailable, confirm you've completed all [Prerequisites](#prerequisites).
1. Enter the organization name to confirm deactivation.
1. Select **Deactivate organization**.

---

## Create and manage a team

# Create and manage a team

You can create teams for your organization in the Admin Console or Docker Hub,
and configure team repository access in Docker Hub.

A team is a group of Docker users that belong to an organization. An
organization can have multiple teams. An organization owner can create new
teams and add members to an existing team using their Docker ID or email
address. Members aren't required to be part of a team to be associated with an
organization.

The organization owner can add additional organization owners to help them
manage users, teams, and repositories in the organization by assigning them
the owner role.

## What is an organization owner?

An organization owner is an administrator who has the following permissions:

- Manage repositories and add team members to the organization
- Access private repositories, all teams, billing information, and
  organization settings
- Specify [permissions](#permissions-reference) for each team in the
  organization
- Enable [SSO](/enterprise/security/single-sign-on/) for the
  organization

When SSO is enabled for your organization, the organization owner can
also manage users. Docker can auto-provision Docker IDs for new end-users or
users who'd like to have a separate Docker ID for company use through SSO
enforcement.

Organization owners can add others with the owner role to help them
manage users, teams, and repositories in the organization.

For more information on roles, see
[Roles and permissions](/enterprise/security/roles-and-permissions/).

## Create a team

1. Sign in to [Docker Home](https://app.docker.com) and select your
   organization.
1. Select **Teams**.
1. Select **Create team**.
1. Provide the team's information, then select **Create**.

## Set team repository permissions

You must create a team before you are able to configure repository permissions.
For more details, see [Create and manage a
team](/admin/organization/manage/manage-a-team/).

To set team repository permissions:

1. Sign in to [Docker Hub](https://hub.docker.com).
1. Select **My Hub** > **Repositories**.

   A list of your repositories appears.

1. Select a repository.

   The **General** page for the repository appears.

1. Select the **Permissions** tab.
1. Add, modify, or remove a team's repository permissions.
   - Add: Specify the **Team**, select the **Permission**, and then select **Add**.
   - Modify: Specify the new permission next to the team.
   - Remove: Select the **Remove permission** icon next to the team.

### Permissions reference

- `Read-only` access lets users view, search, and pull a private repository
  in the same way as they can a public repository.
- `Read & Write` access lets users pull, push, and view a repository. In
  addition, it lets users view, cancel, retry or trigger builds.
- `Admin` access lets users pull, push, view, edit, and delete a
  repository. You can also edit build settings and update the repository鈥檚
  description, collaborator permissions, public/private visibility, and delete.

Permissions are cumulative. For example, if you have "Read & Write" permissions,
you automatically have "Read-only" permissions.

The following table shows what each permission level allows users to do:

|             Action              | Read-only | Read & Write | Admin |
| :-----------------------------: | :-------: | :----------: | :---: |
|        Pull a Repository        |    鉁�     |      鉁�      |  鉁�   |
|        View a Repository        |    鉁�     |      鉁�      |  鉁�   |
|        Push a Repository        |    鉂�     |      鉁�      |  鉁�   |
|        Edit a Repository        |    鉂�     |      鉂�      |  鉁�   |
|       Delete a Repository       |    鉂�     |      鉂�      |  鉁�   |
| Update a Repository Description |    鉂�     |      鉂�      |  鉁�   |
|           View Builds           |    鉁�     |      鉁�      |  鉁�   |
|          Cancel Builds          |    鉂�     |      鉁�      |  鉁�   |
|          Retry Builds           |    鉂�     |      鉁�      |  鉁�   |
|         Trigger Builds          |    鉂�     |      鉁�      |  鉁�   |
|       Edit Build Settings       |    鉂�     |      鉂�      |  鉁�   |

> [!NOTE]
>
> A user who hasn't verified their email address only has `Read-only` access to
> the repository, regardless of the rights their team membership has given them.

## Delete a team

Organization owners can delete a team. When you remove a team from your
organization, this action revokes member access to the team's permitted
resources. It won't remove users from other teams that they belong to, and it
won't delete any resources.

1. Sign in to [Docker Home](https://app.docker.com/) and select your
   organization.
1. Select **Teams**.
1. Select the **Actions** icon next to the name of the team you want to delete.
1. Select **Delete team**.
1. Review the confirmation message, then select **Delete**.

## More resources

- [Video: Docker Teams](https://youtu.be/WKlT1O-4Du8?feature=shared&t=348)
- [Video: Roles, teams, and repositories](https://youtu.be/WKlT1O-4Du8?feature=shared&t=435)

---

## Manage license assignment

# Manage license assignment

Licenses let you selectively choose which of your organization members have access to supported Docker products. Organization owners can oversee who on their team has active licenses, or configure licenses to assign automatically when members access supported Docker products. Like Docker Core seats, licenses can be configured on a per member basis.

> [!TIP]
> To learn more about product licenses, Docker Core seats, and other Docker add-ons see [Docker plans](/subscription/plans/),
> or <a href="https://www.docker.com/pricing/contact-sales/" id="dkr_docs_cs_admin_licenses" class="link" rel="noopener">contact sales</a> to purchase licenses.

## Manage licenses

The **Members** page lets you track the number of available licenses for your organization and who currently holds a license. You can also assign or revoke licenses from this page.

To manage licenses for your organization:

1. Sign in to [Docker Home](https://app.docker.com), then choose your organization.
1. Select **Members** from the left navigation.
1. Select the action menu at the end of the row to assign or revoke an active license.
1. Optional. To bulk assign or revoke licenses, choose the members you want to bulk manage, then select the **Bulk actions** menu.
1. Optional. To manage automatic license assignment, turn off or turn on with the **Automatically assign licenses** toggle.

You must assign licenses manually, or configure automatic license assignment to consume a license. Inviting a new member to your organization consumes a seat or license if you select a product in **Licenses (optional)** during the [invite flow](/admin/organization/manage/members/), but won't auto-assign product licenses by default. Conversely, purchasing a set of licenses won't trigger automatic assignment to existing members.

## Automatic license assignment

Automatic license assignment gives members a product license when they use a supported product for the first time. Automatic license assignment is available for AI Governance licenses. Only organizations that purchase AI Governance can set up auto-assignment for Docker Core as well.

- When you purchase AI Governance, signing into [Docker Sandboxes](https://docs.docker.com/ai/sandboxes/) with `login` command in `sbx` CLI (`sbx login`) automatically provisions AI Governance licenses on a first-come, first served basis.
- Similarly, logins to Docker Desktop will automatically provision Docker Core for AI Governance license-holding organizations that have available Docker Core seats.
- Licenses are assigned until exhausted.
  - Once the available licenses are exhausted, automatic license assignment will stop until you purchase more licenses or revoke assigned licenses.
  - Members can still use Docker Sandbox or Docker Desktop, but organization policies for those products won't affect their usage.

AI Governance licenses include single sign-on (SSO) and provisioning features regardless of your Docker Core subscription. Automatic license assignment requires [setting up SSO](/enterprise/security/single-sign-on/connect/), then [provisioning](/enterprise/security/provisioning/) with System for Cross-domain Identity Management (SCIM) or Just-in-Time (JIT).

## What's next

See these docs to explore Docker Core add-ons, or products that need licenses:

- [Docker plans](/subscription/plans/) to learn about different add-ons
- [Manage seats](/admin/organization/manage/manage-seats/) to add more seats to your Docker Core subscription
- [AI Governance](/ai/sandboxes/governance/org/) to set up organization policies for your organization members
- [Docker Offload](/offload/about/) to let your developers offload building and running containers to the cloud

---

## Docker products

# Docker products

In this section, learn how to manage access and view usage of the Docker
products for your organization. For more detailed information about each
product, including how to set up and configure them, see the following manuals:

- [Docker Desktop](/desktop/)
- [Docker Hub](/docker-hub/)
- [Docker Build Cloud](/build-cloud/)
- [Docker Scout](/scout/)
- [Testcontainers Cloud](https://testcontainers.com/cloud/docs/#getting-started)
- [Docker Offload](/offload/)

## Manage product access for your organization

Access to the Docker products included in your subscription is turned on by
default for all users. For an overview of products included in your
subscription, see
[Docker subscriptions and features](https://www.docker.com/pricing?ref=Docs&refAction=DocsAdminManageProducts).

**Docker Desktop**

### Manage Docker Desktop access

To manage Docker Desktop access:

1. [Enforce sign-in](/enterprise/security/enforce-sign-in/).
1. Manage members [manually](/admin/organization/manage/members/) or use
   [provisioning](/enterprise/security/provisioning/).

With sign-in enforced, only users who are a member of your organization can
use Docker Desktop after signing in.

**Docker Hub**

### Manage Docker Hub access

To manage Docker Hub access, sign in to
[Docker Home](https://app.docker.com/) and configure [Registry Access Management](/enterprise/security/hardened-desktop/registry-access-management/)
or [Image Access Management](/enterprise/security/hardened-desktop/image-access-management/).

**Docker Build Cloud**

### Manage Docker Build Cloud access

To initially set up and configure Docker Build Cloud, sign in to
[Docker Build Cloud](https://app.docker.com/build) and follow the
on-screen instructions.

To manage Docker Build Cloud access:

1. Sign in to [Docker Build Cloud](http://app.docker.com/build) as an
   organization owner.
1. Select **Account settings**.
1. Select **Lock access to Docker Build Account**.

**Docker Scout**

### Manage Docker Scout access

To initially set up and configure Docker Scout, sign in to
[Docker Scout](https://scout.docker.com/) and follow the on-screen instructions.

To manage Docker Scout access:

1. Sign in to [Docker Scout](https://scout.docker.com/) as an organization
   owner.
1. Select your organization, then **Settings**.
1. To manage what repositories are enabled for Docker Scout analysis, select
   **Repository settings**. For more information on,
   see [repository settings](/scout/explore/dashboard/#repository-settings).
1. To manage access to Docker Scout for use on local images with Docker Desktop,
   use [Settings Management](/enterprise/security/hardened-desktop/settings-management/)
   and set `sbomIndexing` to `false` to disable, or to `true` to enable.

**Testcontainers Cloud**

### Manage Testcontainers Cloud access

To initially set up and configure Testcontainers Cloud, sign in to
[Testcontainers Cloud](https://app.testcontainers.cloud/) and follow the
on-screen instructions.

To manage access to Testcontainers Cloud:

1. Sign in to the [Testcontainers Cloud](https://app.testcontainers.cloud/) and
   select **Account**.
1. Select **Settings**, then **Lock access to Testcontainers Cloud**.

**Docker Offload**

### Manage Docker Offload access

> [!NOTE]
>
> Docker Offload isn't included in the core Docker subscription plans. To make Docker Offload available, you must [contact sales](https://www.docker.com/products/docker-offload/) and subscribe.

To manage Docker Offload access for your organization, use [Settings
Management](/enterprise/security/hardened-desktop/settings-management/):

1. Sign in to [Docker Home](https://app.docker.com/) as an organization owner.
1. Select **Admin Console** > **Desktop Settings Management**.
1. Configure the **Enable Docker Offload** setting to control whether Docker Offload features are available in Docker
   Desktop. You can configure this setting in five states:
   - **Always enabled**: Docker Offload is always enabled and users cannot disable it. The Offload
     toggle is always visible in Docker Desktop header. Recommended for VDI environments where local Docker execution is
     not possible.
   - **Enabled**: Docker Offload is enabled by default but users can disable it in Docker Desktop
     settings. Suitable for hybrid environments.
   - **Disabled**: Docker Offload is disabled by default but users can enable it in Docker Desktop
     settings.
   - **Always disabled**: Docker Offload is disabled and users cannot enable it. The option is
     visible but locked. Use when Docker Offload is not approved for organizational use.
   - **User defined**: No enforced default. Users choose whether to enable or disable Docker Offload in their
     Docker Desktop settings.
1. Select **Save**.

For more details on Settings Management, see the [Settings
reference](/enterprise/security/hardened-desktop/settings-management/settings-reference/#enable-docker-offload).

## Monitor product usage for your organization

To view usage for Docker products:

- Docker Desktop: View the **Insights** page in [Docker Home](https://app.docker.com/). For more details, see [Insights](/admin/insights/).
- Docker Hub: View the [**Usage** page](https://hub.docker.com/usage) in Docker Hub.
- Docker Build Cloud: View the **Build minutes** page in [Docker Build Cloud](http://app.docker.com/build).
- Docker Scout: View the [**Repository settings** page](https://scout.docker.com/settings/repos) in Docker Scout.
- Testcontainers Cloud: View the [**Billing** page](https://app.testcontainers.cloud/dashboard/billing) in Testcontainers Cloud.
- Docker Offload: View the **Offload** > **Offload overview** page in [Docker Home](https://app.docker.com/). For more details, see
  [Docker Offload usage and billing](/offload/usage/).

If your usage or seat count exceeds your subscription amount, you can
[add seats](/admin/organization/manage/manage-seats/) or [view available Docker plans](/subscription/plans/) to meet your needs.

---

## Manage subscription seats

# Manage subscription seats

You can add or remove seats from your Docker Team or Business subscription at any time to accommodate team changes. When you add seats mid-billing cycle, you're charged a prorated amount for the additional seats.

> [!IMPORTANT]
> If you have a sales-assisted Docker Business subscription,
> contact your account manager to add or remove seats from your subscription.

## Add seats to your subscription

To add seats:

1. Sign in to [Docker Home](https://app.docker.com/) and select your
   organization.
1. Select **Billing**.
1. Select the action menu from the **Active Plans** tile, then choose **Add seats**.
1. Follow the on-screen instructions to complete adding seats.
   - You can't use pay by invoice for purchasing additional seats.
   - You must use a card or US bank account.

You can add more members to your organization. For more information, see [Manage organization members](/admin/organization/manage/members/).

## Volume pricing

Docker offers volume pricing for Docker Business subscriptions starting at 25 seats. Contact the <a href="https://www.docker.com/pricing/contact-sales/" id="dkr_docs_cs_volume_pricing" class="link" rel="noopener">Docker Sales Team</a> for more information.

## Remove seats from your subscription

You can remove seats from your Team or Business subscription at any time. To remove seats:

1. Sign in to [Docker Home](https://app.docker.com/) and select your
   organization.
1. Select **Billing**.
1. Select the action menu, then choose **Remove seats**.
1. Follow the on-screen instructions to complete removing seats.

Changes apply to your next billing cycle, and unused portions aren't refundable.

For example, if you're billed on the 8th of every month for 10 seats and remove 2 seats on the 15th, the 2 seats remain available until your next billing cycle. Your payment for 8 seats begins on the next billing cycle.

> [!TIP]
> You can cancel the removal of seats before your next billing cycle. To do so, select **Cancel change**.

---

## Manage organization members

# Manage organization members

Learn how to manage members for your organization in Docker Hub and the Docker Admin Console.

## Invite members

Owners can invite new members to an organization via Docker ID, email address, or with a CSV file containing email addresses. If an invitee does not have a Docker account, they must create an account and verify their email address before they can accept an invitation to join the organization. When inviting members, their pending invitation occupies a seat.

### Invite members via Docker ID or email address

Use the following steps to invite members to your organization via Docker ID or email address.

1. Sign in to [Docker Home](https://app.docker.com) and select your organization from the top-left account drop-down.
1. Select **Members**, then **Invite**.
1. Select **Emails or usernames**.
1. Follow the on-screen instructions to invite members. Invite a maximum of 1000 members and separate multiple entries by comma, semicolon, or space.

When you invite members, you assign them a role. See [Roles and permissions](/enterprise/security/roles-and-permissions/) for
details about the access permissions for each role.

Pending invitations appear in the table. Invitees receive an email with a link to Docker Hub where they can accept or decline the invitation.

### Invite members via CSV file

To invite multiple members to an organization via a CSV file containing email addresses:

1. Sign in to [Docker Home](https://app.docker.com) and select your organization from the top-left account drop-down. Select **Members** > **Invite** > **CSV upload**.
1. Optional. Select **Download the template CSV file** to download an example CSV file. The following is an example of the contents of a valid CSV file:

   ```text
   email
   docker.user-0@example.com
   docker.user-1@example.com
   ```

   The example file demonstrates CSV file requirements:
   - The file must contain a header row with at least one heading named email. Additional columns are allowed and are ignored in the import.
   - The file must contain a maximum of 1000 email addresses (rows). To invite more than 1000 users, create multiple CSV files and perform all steps in this task for each file.

1. Create a new CSV file or export a CSV file from another application.
   - To export a CSV file from another application, see the application鈥檚 documentation.
   - To create a new CSV file, open a new file in a text editor, type email on the first line, type the user email addresses one per line on the following lines, and then save the file with a .csv extension.
1. Select **Browse files** and then select your CSV file, or drag and drop the CSV file into the **Select a CSV file to upload** box. You can only select one CSV file at a time.
1. After the CSV file has been uploaded, select **Review** to identify any invalid email addresses, already invited users, invited users who are already members, or duplicated email addresses within the same CSV file.
1. Follow the on-screen instructions to invite members.

Pending invitations appear in the table. The invitees receive an email with a link to Docker Hub where they can accept or decline the invitation.

### Invite members via API

You can bulk invite members using the Docker Hub API. For more information, see the [Bulk create invites](https://docs.docker.com/reference/api/hub/latest/#tag/invites/paths/~1v2~1invites~1bulk/post) API endpoint.

## Accept invitation

After receiving an email invitation, users can access
a link to Docker Hub where they can accept or decline the invitation.

To accept an invitation:

1. Check your email inbox and open the Docker email with an invitation to
   join the Docker organization.
1. To open the link to Docker Hub, select the **click here** link.
1. The Docker create an account page will open. If you already have an account, select **Already have an account? Sign in**.
   If you do not have an account yet, create an account using the same email
   address you received the invitation through.
1. Optional. If you do not have an account and created one, you must navigate
   back to your email inbox and verify your email address using the Docker verification
   email.
1. Once you are signed in to Docker Hub, select **My Hub** from the top-level navigation menu.
1. Select **Accept** on your invitation.

After accepting an invitation, you are now a member of the organization.

Invitation email links expire after 14 days. If your email link has expired, you can sign in to [Docker Hub](https://hub.docker.com/) with the email address the link was sent to and accept the invitation from the **Notifications** panel.

## Manage invitations

After inviting members, you can resend or remove invitations as needed. Each invitee occupies one seat, so if the amount of email addresses in your CSV file exceeds the number of available seats in your organization, you won't be able to invite more members.

> [!TIP]
> Need to manage more than 1,000 team members? [Upgrade to Docker Business for unlimited user invites](https://www.docker.com/pricing?ref=Docs&refAction=DocsAdminMembers) and advanced role management. You can also [add seats](/admin/organization/manage/manage-seats/) to your subscription.

### Resend an invitation

You can send individual invitations, or bulk invitations from the Admin Console.

To resend an individual invitation:

1. Sign in to [Docker Home](https://app.docker.com/) and select your
   organization.
1. Select **Members**.
1. Select the **action menu** next to the invitee and select **Resend**.
1. Select **Invite** to confirm.

To bulk resend invitations:

1. Sign in to [Docker Home](https://app.docker.com/) and select your
   organization.
1. Select **Members**.
1. Use the **checkboxes** next to **Usernames** to bulk select users.
1. Select **Resend invites**.
1. Select **Resend** to confirm.

### Remove an invitation

To remove an invitation from the Admin Console:

1. Sign in to [Docker Home](https://app.docker.com/) and select your
   organization.
1. Select **Members**.
1. Select the **action menu** next to the invitee and select **Remove invitee**.
1. Select **Remove** to confirm.

## Manage members on a team

Use Docker Hub or the Admin Console to add or remove team members. Organization owners can add a member to one or more teams within an organization.

### Add a member to a team

To add a member to a team with the Admin Console:

1. Sign in to [Docker Home](https://app.docker.com/) and select your
   organization.
1. Select **Teams**.
1. Select the team name.
1. Select **Add member**. You can add the member by searching for their email address or username.

An invitee must first accept the invitation to join the organization before being added to the team.

### Remove members from teams

If your organization uses single sign-on (SSO) with [SCIM](/enterprise/security/provisioning/scim/) enabled, you should remove members from your identity provider (IdP). This automatically removes members from Docker. If SCIM is disabled, follow procedures in this doc to remove members manually in Docker.

Organization owners can remove a member from a team in Docker Hub or Admin Console. Removing the member from the team will revoke their access to the permitted resources. To remove a member from a specific team with the Admin Console:

1. Sign in to [Docker Home](https://app.docker.com/) and select your
   organization.
1. Select **Teams**, then choose the name of the team member you want to remove.
1. Select the **X** next to the user's name to remove them from the team.
1. When prompted, select **Remove** to confirm.

### Update a member role

Organization owners can manage [roles](/enterprise/security/roles-and-permissions/)
within an organization. If an organization is part of a company,
the company owner can also manage that organization's roles. If you have SSO enabled, you can use [SCIM for role mapping](/enterprise/security/provisioning/scim/).

To update a member role in the Admin Console:

1. Sign in to [Docker Home](https://app.docker.com/) and select your
   organization.
1. Select **Members**.
1. Find the username of the member whose role you want to edit. Select the
   **Actions** menu, then **Edit role**.

If you're the only owner of an organization and you want to edit your role, assign a new owner
for your organization so you can edit your role.

## Export members CSV file

Owners can export a CSV file containing all members. The CSV file for a company contains the following fields:

- Name: The user's name
- Username: The user's Docker ID
- Email: The user's email address
- Member of Organizations: All organizations the user is a member of within a company
- Invited to Organizations: All organizations the user is an invitee of within a company
- Account Created: The time and date when the user account was created

To export a CSV file of your members:

1. Sign in to [Docker Home](https://app.docker.com/) and select your
   organization.
1. Select **Members**.
1. Select the **download** icon to export a CSV file of all members.

---

## Organization FAQs

# Organization FAQs

### How can I see how many active users are in my organization?

If your organization uses a Software Asset Management tool, you can use it to
find out how many users have Docker Desktop installed. If your organization
doesn't use this software, you can run an internal survey
to find out who is using Docker Desktop.

For more information, see [Identify your Docker users and their Docker accounts](/admin/organization/setup/onboard/#step-one-identify-your-docker-users).

### Do users need to authenticate with Docker before an owner can add them to an organization?

No. Organization owners can invite users with their email addresses, and also
assign them to a team during the invite process.

### Can I force my organization's members to authenticate before using Docker Desktop and are there any benefits?

Yes. You can
[enforce sign-in](/enterprise/security/enforce-sign-in/).

Some benefits of enforcing sign-in are:

- Ensures users receive the benefits of your subscription.
- Ensures security features like [Image Access Management](/enterprise/security/hardened-desktop/image-access-management/) and [Registry Access Management](/enterprise/security/hardened-desktop/registry-access-management/) are applied.
- Ensures you gain insights into users' activity.

### Can I convert my personal Docker ID to an organization account?

Yes. You can convert your user account to an organization account. Once you
convert a user account into an organization, it's not possible to
revert it to a personal user account.

For prerequisites and instructions, see
[Convert an account into an organization](/admin/organization/organization-faqs/setup/convert-account/).

### Do organization invitees take up seats?

Yes. A user invited to an organization will take up one of the provisioned
seats, even if that user hasn鈥檛 accepted their invitation yet.

To manage invites, see [Manage organization members](/admin/organization/manage/members/).

### Do organization owners take a seat?

Yes. Organization owners occupy a seat.

### What is the difference between user, invitee, seat, and member?

- User: Docker user with a Docker ID.
- Invitee: A user that an administrator has invited to join an organization but
  has not yet accepted their invitation.
- Seats: The number of purchased seats in an organization.
- Member: A user who has received and accepted an invitation to join an
  organization. Member can also refer to a member of a team within an
  organization.

### If I have two organizations and a user belongs to both organizations, do they take up two seats?

Yes. In a scenario where a user belongs to two organizations, they take up one
seat in each organization.

---

## Convert an account into an organization

# Convert an account into an organization

Learn how to convert an existing user account into an organization. This is
useful if you need multiple users to access your account and the repositories
it鈥檚 connected to. Converting it to an organization gives you better control
over permissions for these users through
[teams](/admin/organization/manage/manage-a-team/) and
[roles](/enterprise/security/roles-and-permissions/).

When you convert a user account to an organization, the account is migrated to
a Docker Team subscription by default.

## Prerequisites

Before you convert a user account to an organization, ensure that you meet the following requirements:

- The user account that you want to convert must not be a member of a company or any teams or organizations. You must remove the account from all teams, organizations, or the company.

  To do this:
  1. Navigate to **My Hub** and then select the organization you need to leave.
  1. Find your username in the **Members** tab.
  1. Select the **More options** menu and then select **Leave organization**.

  If the user account is the sole owner of any organization or company, assign another user the owner role and then remove yourself from the organization or company.

- You must have a separate Docker ID ready to assign as the owner of the organization during conversion.

  If you want to convert your user account into an organization account and you don't have any other user accounts, you need to create a new user account to assign it as the owner of the new organization. With the owner role assigned, this user account has full administrative access to configure and manage the organization. You can assign more users the owner role after the conversion.

## What happens when you convert your account

The following happens when you convert your account into
an organization:

- This process removes the email address for the account. Notifications are
  instead sent to organization owners. You'll be able to reuse the
  removed email address for another account after converting.
- The current subscription will automatically cancel and your new subscription
  will start.
- Repository namespaces and names won't change, but converting your account
  removes any repository collaborators. Once you convert the account, you'll need
  to add repository collaborators as team members.
- Existing automated builds appear as if they were set up by the first owner
  added to the organization.
- The user account that you add as the first owner will have full
  administrative access to configure and manage the organization.
- To transfer a user's personal access tokens (PATs) to your converted
  organization, you must designate the user as an organization owner. This will
  ensure any PATs associated with the user's account are transferred to the
  organization owner.

## Convert an account into an organization

> [!IMPORTANT]
>
> Converting an account into an organization is permanent. Back up any data
> or settings you want to retain.

1. Sign in to [Docker Home](https://app.docker.com/).
1. Select your avatar in the top-right corner to open the drop-down.
1. From **Account settings**, select **Convert**.
1. Review the warning displayed about converting a user account. This action
   cannot be undone and has considerable implications for your assets and the
   account.
1. Enter a **Username of new owner** to set an organization owner. The new
   Docker ID you specify becomes the organization鈥檚 owner. You cannot use the
   same Docker ID as the account you are trying to convert. The Docker ID is
   case-sensitive.
1. Select **Confirm**. The new owner receives a notification email. Use that
   owner account to sign in and manage the new organization.

---

## Change general organization information

# Change general organization information

Learn how to update your organization information using the Admin Console.

## Update organization information

General organization information appears on your organization landing page in the Admin Console.

This information includes:

- Organization Name
- Company
- Location
- Website
- Gravatar email: To add an avatar to your Docker account, create a [Gravatar account](https://gravatar.com/) and upload an avatar. Next, add your Gravatar email to your Docker account settings. It may take some time for your avatar to update in Docker.

To edit this information:

1. Sign in to the [Admin Console](https://app.docker.com/admin) and
   select your organization from the top-left account drop-down.
1. Enter or update your organization鈥檚 details, then select **Save**.

## Next steps

After configuring your organization information, you can:

- [Configure single sign-on (SSO)](/enterprise/security/single-sign-on/connect/)
- [Set up SCIM provisioning](/enterprise/security/provisioning/scim/)
- [Manage domains](/enterprise/security/domain-management/)
- [Create a company](/admin/company/new-company/)

---

## Onboard your organization

# Onboard your organization

Learn how to onboard your organization using the Admin Console or Docker Hub.

Onboarding your organization includes:

- Identifying users to help you allocate your subscription seats
- Invite members and owners to your organization
- Secure authentication and authorization for your organization
- Enforce sign-in for Docker Desktop to ensure security best practices

These actions help administrators gain visibility into user activity and
enforce security settings. Organization members also receive increased pull
limits and other benefits when they are signed in.

## Prerequisites

Before you start onboarding your organization, ensure you:

- Have a Docker Team or Business subscription. For more details, see
  [Docker subscriptions and features](https://www.docker.com/pricing?ref=Docs&refAction=DocsAdminOnboard).

  > [!NOTE]
  >
  > When purchasing a self-serve subscription, the on-screen instructions
  > guide you through creating an organization. If you have purchased a
  > subscription through Docker Sales and you have not yet created an
  > organization, see [Create an organization](/admin/organization/setup/orgs/).

- Familiarize yourself with Docker concepts and terminology in
  the [administration overview](/setup/).

## Onboard with guided setup

The Admin Console has a guided setup to help you
onboard your organization. The guided setup's steps consist of basic onboarding
tasks. If you want to onboard outside of the guided setup,
see [Recommended onboarding steps](/admin/organization/setup/onboard/#recommended-onboarding-steps).

To onboard using the guided setup,
navigate to the [Admin Console](https://app.docker.com) and
select **Guided setup** in the left-hand navigation.

The guided setup walks you through the following onboarding steps:

- **Invite your team**: Invite owners and members.
- **Manage user access**: Add and verify a domain, manage users with SSO, and
  enforce Docker Desktop sign-in.
- **Docker Desktop security**: Configure image access management, registry
  access management, and settings management.

## Recommended onboarding steps

### Step one: Identify your Docker users

Identifying your users helps you allocate seats efficiently and ensures they
receive your Docker subscription benefits.

1. Identify the Docker users in your organization.
   - If your organization uses device management software, like MDM or Jamf,
     you can use the device management software to help identify Docker users.
     See your device management software's documentation for details. You can
     identify Docker users by checking if Docker Desktop is installed at the
     following location on each user's machine:
     - Mac: `/Applications/Docker.app`
     - Windows: `C:\Program Files\Docker\Docker`(all-user installation) or `%LOCALAPPDATA%\Programs\DockerDesktop` (per-user installation (Beta))
     - Linux: `/opt/docker-desktop`
   - If your organization doesn't use device management software or your
     users haven't installed Docker Desktop yet, you can survey your users to
     identify who is using Docker Desktop.
1. Ask users to update their Docker account's email address to one associated
   with your organization's domain, or create a new account with that email.
   - To update an account's email address, instruct your users to sign in
     to [Docker Hub](https://hub.docker.com), and update the email address to
     their email address in your organization's domain.
   - To create a new account, instruct your users to
     [sign up](https://hub.docker.com/signup) using their email address associated
     with your organization's domain. Ensure your users verify their email address.
1. Identify Docker accounts associated with your organization's domain:
   - Ask your Docker sales representative or
     <a href="https://www.docker.com/pricing/contact-sales/" id="dkr_docs_cs_org_onboarding" class="link" rel="noopener">contact sales</a> to get a list
     of Docker accounts that use an email address in your organization's domain.

### Step two: Invite owners

Owners can help you onboard and manage your organization.

When you create an organization, you are the only owner. It is optional to
add additional owners.

To add an owner, invite a user and assign them the owner role. For more
details, see [Invite members](/admin/organization/manage/members/) and
[Roles and permissions](/enterprise/security/roles-and-permissions/).

### Step three: Invite members

When you add users to your organization, you gain visibility into their
activity and you can enforce security settings. Your members also
receive increased pull limits and other organization wide benefits when
they are signed in.

To add a member, invite a user and assign them the member role.
For more details, see [Invite members](/admin/organization/manage/members/) and
[Roles and permissions](/enterprise/security/roles-and-permissions/).

### Step four: Manage user access with SSO and SCIM

Configuring SSO and SCIM is optional and only available to Docker Business
subscribers. To upgrade a Docker Team subscription to a Docker Business
subscription, see [Upgrade a plan](/subscription/manage/#upgrade-plans).

Use your identity provider (IdP) to manage members and provision them to Docker
automatically via SSO and SCIM. See the following for more details:

- [Configure SSO](/enterprise/security/single-sign-on/connect/)
  to authenticate and add members when they sign in to Docker through your
  identity provider.
- Optional.
  [Enforce SSO](/enterprise/security/single-sign-on/connect/) to
  ensure that when users sign in to Docker, they must use SSO.

  > [!NOTE]
  >
  > Enforcing single sign-on (SSO) and enforcing Docker Desktop sign in
  > are different features. For more details, see
  > [Enforcing sign-in versus enforcing single sign-on (SSO)](/enterprise/security/enforce-sign-in/#enforcing-sign-in-versus-enforcing-single-sign-on-sso).

- [Configure SCIM](/enterprise/security/provisioning/scim/) to
  automatically provision, add, and de-provision members to Docker through
  your identity provider.

### Step five: Enforce sign-in for Docker Desktop

By default, members of your organization can use Docker Desktop without signing
in. When users don鈥檛 sign in as a member of your organization, they don鈥檛
receive the
[benefits of your organization鈥檚 subscription](https://www.docker.com/pricing?ref=Docs&refAction=DocsAdminOnboard)
and they can circumvent [Docker鈥檚 security features](/enterprise/security/hardened-desktop/).

There are multiple ways you can enforce sign-in, depending on your organization's
Docker configuration:

- [Registry key method (Windows only)](/enterprise/security/enforce-sign-in/methods/#registry-key-method-windows-only)
- [`.plist` method (Mac only)](/enterprise/security/enforce-sign-in/methods/#plist-method-mac-only)
- [`registry.json` method (All)](/enterprise/security/enforce-sign-in/methods/#registryjson-method-all)

### Step six: Manage Docker Desktop security

Docker offers the following security features to manage your organization's
security posture:

- [Image Access Management](/enterprise/security/hardened-desktop/image-access-management/): Control which types of images your developers can pull from Docker Hub.
- [Registry Access Management](/enterprise/security/hardened-desktop/registry-access-management/): Define which registries your developers can access.
- [Settings management](/enterprise/security/hardened-desktop/settings-management/): Set and control Docker Desktop settings for your users.

## What's next

- [Manage Docker products](/admin/organization/manage/manage-products/) to configure access and view usage.
- Configure [Hardened Docker Desktop](/enterprise/security/hardened-desktop/) to improve your organization鈥檚 security posture for containerized development.
- [Manage your domains](/enterprise/security/domain-management/) to ensure that all Docker users in your domain are part of your organization.

Your Docker subscription provides many more additional features. To learn more,
see [Docker subscriptions and features](https://www.docker.com/pricing?ref=Docs&refAction=DocsAdminOnboard).

---

## Create your organization

# Create your organization

There are multiple ways to create an organization. You can either:

- Create a new organization using the **Create Organization** option in the
  Admin Console or Docker Hub
- Convert an existing user account to an organization

These procedures walk you through creating an organization from the Admin Console.

## Prerequisites

- Before you create an organization, you need a [Docker ID](/accounts/create-account/).
- For prerequisites and detailed instructions on converting an existing user account to an organization, see
  [Convert an account into an organization](/admin/organization/setup/convert-account/).

> [!TIP]
> Need a different plan for your team's needs? Review different [Docker subscriptions and features](https://www.docker.com/pricing?ref=Docs&refAction=DocsAdminOrgs) to choose a subscription for your organization.

## Create an organization

1. Sign in to [Docker Home](https://app.docker.com/) and navigate to the bottom
   of the organization list. Select **Create new organization**.
1. Choose a subscription for your organization, a billing cycle, and specify how many seats you need. See [Docker Pricing](https://www.docker.com/pricing?ref=Docs&refAction=DocsAdminOrgs) for details on the features offered in the Team and Business subscription.
1. Select **Continue to profile**, then **Create an organization** to create a new organization.
1. Enter an **Organization namespace**. This is the official, unique name for
   your organization in Docker Hub.
   - It's not possible to change the name of the organization after you've created it.
   - Your Docker ID and organization can't share the same name.
   - If you want to use your Docker ID as the organization name, then you must first [convert your account into an organization](/admin/organization/setup/convert-account/).
1. Enter your **Company name**. This is the full name of your company.
   - Docker displays the company name on your organization page and in the details of any
     public images you publish.
   - You can update the company name anytime by navigating to your organization's **Settings** page.
1. Select **Continue to billing** to continue, then enter your organization's billing information. Select **Continue to payment** to continue to the billing portal.
1. Provide your payment details and select **Purchase**.

You've now created an organization.

## View an organization

To view an organization in the Admin Console:

1. Sign in to [Docker Home](https://app.docker.com) and select your
   organization.
1. From the left-hand navigation menu, select **Admin Console**.

The Admin Console contains many options that let you to
configure your organization.

## Merge organizations

> [!WARNING]
>
> If you are merging organizations, it is recommended to do so at the _end_ of
> your billing cycle. When you merge an organization and downgrade another, you
> will lose seats on your downgraded organization. Docker does not offer
> refunds for downgrades.

If you have multiple organizations that you want to merge into one, complete
the following steps:

1. Based on the number of seats from the secondary organization, [purchase additional seats](/admin/organization/manage/manage-seats/) for the primary organization account that you want to keep.
1. Manually add users to the primary organization and remove existing users from the secondary organization.
1. Manually move over your data, including all repositories.
1. Once you're done moving all of your users and data, [downgrade](/subscription/plans/docker/#cancel-a-docker-plan) the secondary account to a free subscription. Note that Docker does not offer refunds for downgrading organizations mid-billing cycle.

If your organization has a Docker Business subscription with a purchase
order, contact Support or your Account Manager at Docker.

## More resources

- [Video: Docker Hub Organizations](https://www.youtube.com/watch?v=WKlT1O-4Du8)

---
