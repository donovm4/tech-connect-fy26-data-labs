@lab.Title


## Overview

In this lab, you'll plan and execute a migration of a representative Linux workload from an on-premises datacenter to **Microsoft Azure**. You'll use **Azure Migrate** to discover and assess the environment, choose modernization paths, and then migrate both a database tier (PaaS) and an application tier (IaaS). Finally, you'll apply post-migration operational controls for security, backup, monitoring, and cost governance.


## Case study: Linux Migrate
**Terra Firm Laboratories** is a global bioengineering company and a leading researcher in genetic and biological science technology. Founded in 1975, Terra Firm's headquarters are in Palo Alto, CA. Mission-critical workloads are currently hosted in an on-premises datacenter, and the organization is beginning a journey to modernize and migrate into the cloud using Microsoft Azure.

The CTO, **Dennis Nedry**, has initiated an effort to adopt Azure and modernize infrastructure by reducing technical debt and streamlining operations using **Infrastructure as a Service (IaaS)** and **Platform as a Service (PaaS)** capabilities. Terra Firm completed an initial analysis across more than 250 workloads and selected one workload to validate the migration plan end-to-end. The environment includes multiple Linux distributions (RHEL, SUSE, Ubuntu, and a Debian-based custom distro). The company currently uses **FreeIPA** for identity management and wants to migrate to **Microsoft Entra ID**.



## Architecture

In this lab, you work with:
- An on-premises-style **Hyper-V host** running Linux virtual machines.
- An **Azure Migrate project + appliance** used to discover servers and workloads and generate assessments.
- A **PaaS target** for PostgreSQL using **Azure Database for PostgreSQL Flexible Server** (private access).
- An **IaaS target** using **Azure VM migration** (Azure Migrate Server Migration).
- Post-migration operations controls (backup, patching, security posture, monitoring, and cost management).

## Exercises

This lab includes the following exercises:

- **Exercise 00**: Prepare for workload migration with Azure Migrate and discover workloads on Hyper-V host (**25 minutes**)
- **Exercise 01**: Assess on-prem workload readiness for migration (**45 minutes**)
- **Exercise 02**: Migrate PostgreSQL to Azure Database for PostgreSQL Flexible Server (**50 minutes**)
- **Exercise 03**: Migrate Linux middleware/runtime server using Azure Migrate (**75 minutes**)
- **Exercise 04**: Governance, security, and cost optimization (**30 minutes**)


## Prerequisites

- Basic familiarity with the **Azure portal** (resource groups, search, blades).
- Comfort using a browser-based lab environment and switching between tabs/windows.
- Basic Linux navigation concepts (you'll use provided commands and credentials).
- Basic understanding of migration concepts:
  - Discovery and assessment
  - Cutover and validation
  - Rollback and operational readiness

# Exercise 00: Prepare for workload migration with Azure Migrate and discover workloads on Hyper-V Host

## Introduction
This exercise establishes the Azure Migrate foundation for the lab. You'll create a dedicated Azure Migrate project and register the Azure Migrate appliance that is pre-deployed in the lab environment. With the project and appliance connected, you'll configure discovery against the Hyper-V host and enable guest/workload discovery so Azure Migrate can detect both server inventory and workload-level objects such as the Airsonic web app (Tomcat) and PostgreSQL database. The outputs of this exercise become the source data for later business case creation, assessments, and migrations.

## Description
In this exercise, you'll:
- Sign in to the Azure portal and create a new Azure Migrate project in the provided subscription and resource group.
- Initiate Hyper-V appliance-based discovery, generate a project key, and register the Azure Migrate appliance using device sign in.
- Configure discovery credentials for the Hyper-V host and guest workloads (Linux and PostgreSQL).
- Add the Hyper-V host as a discovery source, validate connectivity, and start discovery so the project begins collecting inventory and workload information.

## Success criteria
- A new Azure Migrate project named **Migration-Project-@lab.LabInstance.Id** exists and can be opened successfully.
- The Azure Migrate appliance is registered to the project and shows a successful/healthy registration status.
- The Hyper-V host discovery source validates successfully and discovery is started.
- Azure Migrate begins populating discovered inventory with the expected servers and workload objects (including a detected web app and PostgreSQL workload).

## Key tasks
- Create and open an Azure Migrate project that will serve as the workspace for discovery, assessment, and migration activities.
- Generate a project key and register the pre-deployed Azure Migrate appliance to the project using device sign in.
- Configure Hyper-V discovery and guest discovery credentials (Linux + PostgreSQL) to enable workload identification.
- Add the Hyper-V host as a discovery source, validate access, and start discovery to populate inventory and workloads.

## Expected duration: 25 Minutes

===

## Task 01: Create a new migration project

> [!IMPORTANT]
>
> We will need to create 2 separate Azure Migrate projects.
>  1. business case, wave planning
>  2. virtual machine migration

### Introduction
Terra Firm Laboratories is ready to turn "we should move to Azure" into an actual plan Dennis can track and defend. An Azure Migrate project becomes the team's home base for discovery, assessment, and migration decisions-so everyone is working from the same inventory and the same assumptions.

### Description
In this task, you'll create a new Azure Migrate project in the Azure portal. You'll confirm the project exists and open it, so it is ready for appliance registration and discovery.

### Success criteria
- A new Azure Migrate project named **Migration-Project-@lab.LabInstance.Id** exists in the portal.
- You can open the project from **Azure Migrate > All projects** and see its Overview page.

### Key tasks
- Sign in to the Azure portal on the lab VM using the provided lab credentials.
- Create a new Azure Migrate project in the **AZMigrateRG** resource group with the specified project name.
- Open the project from **All projects** and confirm it is accessible.

1. [] Sign into the Lab VM using +++Passw0rd!+++ as the password.

    > [!note] **Note**: Your Lab VM also serves as the Hyper-V host machine throughout this workshop.

1. [] Open a web browser on the Lab VM and navigate to +++portal.azure.com+++.

1. [] Sign in using your lab's Azure credentials. 

    | Item | Value |
    |:--------|:--------|
    | Username   | +++@lab.CloudPortalCredential(User1).Username+++   |
    | TAP  | +++@lab.CloudPortalCredential(User1).AccessToken+++   |

1. [] Select **Yes** if prompted to stay signed in.

1. [] In the Azure portal, search for +++Azure Migrate+++ in the search bar and select **Azure Migrate** under **Services**.

    ![Azure Migrate is displaed in the Azure Search bar and highlighted in the search results.](instructions312691/17-AzureMigrate.png)

1. [] On the **Azure Migrate** blade, select **Create Project**.

	!IMAGE[xbrke4r4.jpg](instructions331780/xbrke4r4.jpg)

1. [] On the **Create Project** blade, enter the following:
	
    | Object | Value |
    | -------- | -------- |
    | Subscription | **Accept the default** |
    | Resource group | **AZMigrateRG** |
    | Project name | +++Migration-Project-@lab.LabInstance.Id+++ |
    | Geography | **United States** |

1. [] Select **Create**.

1. [] After project creation completes, select **All projects** on the left menu, and then select the **Migration-Project-@lab.LabInstance.Id** project.

---

#### Congratulations!

You created an Azure Migrate project that will store discovery, assessment, and migration metadata for the rest of the lab, and then confirmed you can open it successfully.

===

## Task 02: Register the Azure Migrate appliance

> [!IMPORTANT]
>
> Removed as we are using agent-based migration with Azure Site Recovery Provider.

<!--

### Introduction
Before Terra Firm can estimate cost, downtime, or even a safe migration sequence, they need a clear picture of what's running on-premises. Registering the Azure Migrate appliance is how Dennis's team securely feeds Azure Migrate the facts-so the pilot workload can be assessed with real data instead of guesses.

### Description
In this task, you'll generate a project key from the Azure Migrate project and use it to register the pre-deployed Azure Migrate appliance. You'll complete device sign in and confirm the appliance registration status.

### Success criteria
- A project key has been generated and recorded for later use.
- The appliance shows **successfully registered** in the Appliance Configuration Manager.

### Key tasks
- Start appliance-based discovery for **Hyper-V** and generate a **project key**.
- Open the Appliance Configuration Manager and verify the project key.
- Complete device sign in and confirm appliance registration completes successfully.


### Configure discovery on the Azure Migrate project

1. [] On the **Overview** page, select **Start Discovery**, choose **Using Appliance**, then **For Azure**.

    ![On the Project blade, Start Discovery -> Using Appliance -> For Azure is highlighted.](instructions312691/21-StartDiscovery.png)

1. [] On the **Discover** blade, under **Are your servers virtualized?**, select **Yes, with Hyper-V**.

1. [] In the Name your appliance field, enter +++app@lab.LabInstance.Id+++.

1. [] Select **Generate Key**.

    >[!Note] After a minute or two, a **Project key** field will appear on the Discover screen. 

1. [] Copy the project key and then paste it in the text box below, for later use.

	 @lab.TextBox(ProjectKey)

    >[!Alert] Do not download the Azure Migrate appliance VHD or ZIP file. The Azure Appliance has already been deployed in the lab environment.

1. [] Select **Close** to close the Discover blade.

### Configure the Azure Migrate Appliance

1. [] Minimize the browser and then locate and launch the **Azure Migrate Appliance Configuration Manager** on the lab desktop.

	!IMAGE[lulpddzu.jpg](instructions332284/lulpddzu.jpg)

1. [] On the Terms of use pop-up, select **I agree**.

1. [] Under **Verification of Azure Migrate project key**, enter the project key that you copied earlier <br><br>`@lab.Variable(ProjectKey)`.

1. [] Select **Verify**.

    ![The Verify button for the Azure Migrate project key is highlighted.](instructions312691/26-KeyVerify.png)

1. [] Scroll to the **Azure user login and appliance registration status** section and select **Login**.

    ![In the Azure user login and appliance registration status section, the Login button is highlighted.](instructions312691/27-Login.png)

1. [] In the pop-up window, select **Copy code & Login**.

    ![The Continue with Azure login dialog is displayed with the Device Code.](instructions312691/28-DeviceCode.png)

1. [] Paste the **code**, and then select **Next**.

1. [] Choose the **@lab.CloudPortalCredential(User1).Username** user account. If prompted, sign in using +++@lab.CloudPortalCredential(User1).Username+++ and +++@lab.CloudPortalCredential(User1).AccessToken+++.

1. [] Select **Continue**.

	!IMAGE[kv522t4i.jpg](instructions331780/kv522t4i.jpg)

1. [] Close the sign-in window and return to the **Appliance Configuration Manager** browser tab.

>[!Alert] The login and appliance registration process can take around 10 minutes. Please wait for this process to complete before continuing to the next task.

#### Congratulations! 
You generated a project key and successfully registered the Azure Migrate appliance to your project so it can upload discovery data.

-->

===

## Task 03: Discover virtual machines and workloads

> [!IMPORTANT]
>
> Removed as we are using agent-based migration with Azure Site Recovery Provider.

<!--

### Introduction
Terra Firm's pilot workload is meant to represent what they'll migrate at scale: Linux servers plus a database tier and supporting components. Discovery (including guest discovery) helps surface the details that matter for migration decisions-what OS and workloads are installed, what depends on what, and what needs extra attention for security and operations once it lands in Azure.

### Description
In this task, you'll configure discovery sources and credentials for your Hyper-V environment and Linux/PostgreSQL guests. You'll start discovery and confirm the appliance validates access and begins discovering inventory.

### Success criteria
- Hyper-V host/cluster discovery source is added and shows **Validation successful**.
- Guest discovery credentials for **Linux** and **PostgreSQL** are saved.
- Discovery has started successfully and the project begins populating discovered inventory.

### Key tasks
- Add Hyper-V host credentials and register the Hyper-V host/cluster as a discovery source.
- Add guest discovery credentials for Linux and PostgreSQL workload discovery.
- Start discovery and verify validation/registration messages appear as expected.


1. [] On the **Appliance Configuration Manager** browser tab, verify you see a message that the appliance has been successfully registered.

    !IMAGE[The Appliance registered successfully message is displayed.](instructions312691/appliance-registered.png)

1. [] Under **Manage credentials and discovery sources**, select **Add credentials** and then enter the following:

    | Object | Value |
    | -------- | -------- |
    | Source type | **Hyper-V Host/Cluster** |
    | Friendly name | +++HyperVCreds+++ |
    | Username | +++administrator+++ |
    | Password | +++Passw0rd!+++ |

    !IMAGE[The Add credentials button is highlighted in the Manage credentials and discovery sources section.](instructions312691/30-Add-Credentials.png)

    !IMAGE[7xiefsub.jpg](instructions332284/7xiefsub.jpg)

1. [] Select **Save**.

1. [] Under Provide Hyper-V host/cluster details, select **Add discovery source**.

    ![The Add discovery source button is highlighted.](instructions312691/32-Add-Discover-source.png)

1. [] In the **Add discovery source** dialog, select **Add single item**.

    ![Add single item is highlighted on the Add discovery source dialog.](instructions312691/add-discovery-source-single-item.png)

1. [] For the **IP address/FQDN**, enter +++hyperv-01+++.

1. [] Under **Map credentials**, select **HyperVCreds**, and then select **Save**.  
 
    !IMAGE[dhykwl84.jpg](instructions332284/dhykwl84.jpg)

1. [] You should receive a **Validation successful** message within a minute or so.

    !IMAGE[zzac1ej8.jpg](instructions332284/zzac1ej8.jpg)

1. [] After validation completes, under **Provide server credentials to perform guest discovery of installed software, dependencies, and workloads**, scroll down and select **Add credentials**.

    ![The Add credentials button is highlighted under Step 3.](instructions312691/guest-discovery-add-credentials.png)

1. [] In the **Add credentials** popup, enter the following:

    | Object | Value |
    | -------- | -------- |
    | Credentials type | **Linux (Non-domain)** |
    | Friendly name | +++Linux+++ |
    | Username | +++skilladmin+++ |
    | Password | +++PurpleFrog3109!+++ |

1. [] Select **Add more** and add:

	| Object | Value |
    | -------- | -------- |
    | Credentials type | **PostgreSQL Server (Password based)** |
    | Friendly name | +++PostgreSQL+++ |
    | Username | +++admin+++ |
    | Password | +++Password~1+++ |

1. [] Select **Save** to close the **Add credentials** dialog.

1. [] Scroll down and select the **Start Discovery** button.

	!IMAGE[9lo0n23d.jpg](instructions331780/9lo0n23d.jpg)

>[!Alert] Once the **Discovery** process begins, notify your instructor that you are ready to continue.

#### Congratulations!
You configured discovery sources and credentials and started discovery so Azure Migrate can populate server inventory and detect workloads (web apps and databases).

-->

===

# Exercise 01: Assess On-Prem workload readiness for migration

## Introduction
This exercise turns discovery data into actionable migration planning outputs. You'll generate a business case to estimate projected costs and savings, and review the assessment created automatically from that business case to understand readiness and migration recommendations. You'll also define an application for the Airsonic stack so you can reason about the system as a single unit (frontend + backend + workloads), compare PaaS vs IaaS paths, and build a wave plan that sequences migration work. Finally, you'll perform identity planning by mapping FreeIPA identities to Microsoft Entra ID targets to support secure administration and application access after migration.

## Description
In this exercise, you'll:
- Build a business case scoped to the lab environment and review its outputs (scope, cost comparison, savings, and strategy summaries).
- Explore discovered inventory to confirm servers and workloads were detected (Airsonic web app and PostgreSQL database).
- Define an application for the Airsonic stack by linking the frontend and backend servers and their discovered workloads.
- Review the assessment created by the business case and compare modernization options (PaaS preferred, PaaS only, lift-and-shift to Azure VMs).
- Create a wave plan from the recommended path, add tasks, review selected applications/workloads, and inspect target settings and migration options.
- Document a planning-only identity mapping strategy from FreeIPA to Microsoft Entra ID, including admins, users, and service identities.

## Success criteria
- A business case named **bc-@lab.LabInstance.Id** exists and reflects the expected scope (workloads/web apps/databases).
- The assessment **businesscase-bc-@lab.LabInstance.Id** is accessible, shows the expected workload count, and provides clear path comparisons across PaaS and IaaS options.
- An application named **airsonic-app-@lab.LabInstance.Id** exists and includes the linked servers and discovered workloads.
- A wave plan named **wave-@lab.LabInstance.Id** exists and includes the Airsonic application and expected workloads, with tasks and target settings reviewed.
- A FreeIPA-to-Entra identity mapping table is completed with at least six entries covering admin, user, and service identities.

## Key tasks
- Generate a business case and interpret its cost/savings outputs while validating the scope matches discovered inventory.
- Create an application definition for the Airsonic stack so planning reflects an application boundary rather than individual servers.
- Use the assessment to compare PaaS vs IaaS paths and identify recommended targets for the web app and PostgreSQL database.
- Create a wave plan from the recommended path, add migration tasks, and review workload selection and target settings.
- Document a practical identity mapping strategy from FreeIPA to Entra ID to support post-migration administration and access control.

## Expected duration: 45 Minutes

===

## Task 01: Build a Business case and Assessment

### Introduction
Dennis needs a lot more to get Terra Firm's cloud initiative funded - he needs a story backed by numbers. The business case turns discovered inventory into a cost-and-savings view, helping Terra Firm evaluate things like Azure Hybrid Benefit, operational savings, and what "moving efficiently" could actually look like.

### Description
In this task, you'll build a business case scoped to the environment and confirm it begins generating. While it runs, you'll explore the discovered inventory and validate that key workloads have been discovered.

### Success criteria
- A business case named **bc-@lab.LabInstance.Id** is created and visible under **Decide and Plan > Business cases**.
- Discovered inventory is visible in the project and includes the expected servers and workloads.

### Key tasks
- Start **Build business case** using the specified settings (target location, modernization preference, pricing assumptions).
- Open **Explore inventory** and validate discovered servers and discovered workloads (web app + database).
- Drill into at least one database workload and one web app workload to review details.

1. [] In the Azure portal, search for +++Azure Migrate+++ in the search bar and select **Azure Migrate**, under **Services**.

    ![Azure Migrate is displaed in the Azure Search bar and highlighted in the search results.](instructions312691/17-AzureMigrate.png)

1. [] On the **Azure Migrate Project** blade in the Azure portal, expand the **Decide and Plan** in the left menu, then select **Business cases**.

1. [] Enter +++bc-@lab.LabInstance.Id+++ for the Business case name, then select **Entire datacenter**.

	!IMAGE[k6lmul2t.jpg](instructions332284/k6lmul2t.jpg)

1. [] On the **Build business case (Preview)** page, enter the following:

    | Object | Value |
    | -------- | -------- |
    | Target location | **@lab.CloudResourceGroup(AZMigrateRG).Location**| 
    | Migration preference | **Modernize**| 
    | Migration preference | **Reserved Instance**| 
    | Discount (%) on Pay as you go | **0**| 
    | Currency | **US Dollar ($)**| 

1. [] Select **Build business case**.

	>[!Note] The business case will take approximately 10 minutes to generate. You can continue with the lab while it is generating.

	>[!Note] Building a business case automatically creates an assessment as well. You'll evaluate both when the generation is complete.

## Explore the discovered inventory

1. [] On the Azure Migrate blade, select **All projects**, and then select the **Migration-Project-@lab.LabInstance.Id** project.

1. [] On the left navigation menu, expand **Explore Inventory**.

1. [] Select **All inventory** and then observe the discovered objects.

    !IMAGE[qlk59gmg.jpg](instructions332284/qlk59gmg.jpg)

1. [] Select the **>** next to **Airsonic-Backend** to display the discovered workloads.

1. [] Select **Databases**, and then select the **airsonic-backend** DB instance to observe the details.

1. [] Select **Close** to return to the previous screen.

1. [] Select **Web apps** and then select the **airsonic** Web app to observe the details.

1. [] Select the **X** in the top right side of the screen, to return to the previous screen.

#### Congratulations! 
You initiated a business case for the environment and verified the discovered inventory contains the expected servers and workload objects used for planning.

===

## Task 02: Define an application for the Airsonic stack

### Introduction
Terra Firm isn't migrating "random servers" - they're migrating a workload that users rely on, with tiers that rise and fall together. Defining the application lets Dennis's team treat the pilot as a single unit (web/app/database), making it easier to plan dependencies, choose a target approach, and avoid migrating pieces out of order.

### Description
In this task, you'll create an application definition for the Airsonic stack by linking the frontend and backend servers and their discovered workloads (Tomcat web app and PostgreSQL database). You'll also set basic app properties such as business criticality and complexity.

### Success criteria
- An application named **airsonic-app-@lab.LabInstance.Id** exists in **Explore applications > Applications**.
- The application includes the linked Airsonic servers and their discovered workloads.

### Key tasks
- Create a new **Custom** application definition from **Explore applications > Applications**.
- Link the Airsonic frontend and backend servers (ensuring workloads are included).
- Set business criticality/complexity values and create the application.

You're grouping the Airsonic Frontend + Backend + discovered workloads (Tomcat + PostgreSQL) into one "application" so you can assess, plan waves, and make PaaS vs IaaS decisions as a stack.

1. [] In the left menu under **Explore applications**, select **Applications**.

1. [] Select **+ Define application**.

1. [] For the Name, enter +++airsonic-app-@lab.LabInstance.Id+++.

1. [] For the description, enter +++Airsonic application consists of a front end Java/Tomcat app and a back end PostgreSQL database+++.

1. [] For Type, select **Custom**.

1. [] Select **Next**.

1. [] Select **Link workloads**.

1. [] Select the checkboxes next to **Airsonic-Frontend** and **Airsonic-Backend**.

	>[!Note] Selecting the servers will also select the Web app and Database workloads.

    !IMAGE[y7vhs9bj.jpg](instructions332284/y7vhs9bj.jpg)

1. [] Select **Add**.

1. [] On the Workloads tab, select **All four workloads**, and then select **Next**.

	!IMAGE[a697m390.jpg](instructions332284/a697m390.jpg)

1. [] On the properties tab, enter the following:

    | Object | Value |
    | -------- | -------- |
    | Business criticality | **Medium** |
    | Complexity | **Medium** |
    | Publisher | **Blank** |
    | Technology stack | **Blank** |

1. [] Select **Review and Create**, and then select **Create**.

	>[!Note] Select **Refresh** until you see that the application has been created.

#### Congratulations! 
You grouped the Airsonic stack into a single application, including its servers and discovered workloads, so it can be assessed and planned as a unit.

===

## Task 03: Review the Business Case outputs (scope, savings, and data quality)

### Introduction
Terra Firm's leadership will ask tough questions-especially about savings, scope, and whether the numbers are trustworthy. Reviewing the business case outputs helps Dennis confirm the pilot workload is correctly represented and that the cost/savings story is grounded in accurate discovery data (because "garbage in, garbage out" applies to cloud planning too).

### Description
In this task, you'll open the generated business case and review key outputs including scoped item counts, estimated costs and savings, and the migration strategy summary. You'll verify that the scope aligns with the discovered inventory.

### Success criteria
- Scoped item counts align with lab inventory (workloads, web apps, database).
- You can locate and interpret key cost and savings outputs and a migration strategy summary.

### Key tasks
- Open the business case overview and review costs, savings, and the YOY visualization.
- Verify scoped item counts match the discovered inventory in the lab.
- Review **Current on-premises vs future** and **Migration Strategies** reports and compare major outputs.

1. [] In the Azure Migrate project, under **Decide and Plan**, select **Business cases**.

	>[!Alert] The Business case generation must be complete before you can continue. If it has not completed, you must wait.

1. [] Select **bc-@lab.LabInstance.Id**.

1. [] On the **Overview** page, observe the **Potential cost savings**:

    - Estimated on-premises cost
    - Estimated Azure cost
    - Savings
    
    !IMAGE[upkfkq7n.jpg](instructions332284/upkfkq7n.jpg)

1. [] On the **Overview** page, observe the **YOY current vs future state costs**:

	!IMAGE[3gim64n3.jpg](instructions332284/3gim64n3.jpg)

1. [] Under **Scoped items**, verify that:
 
    - Workloads = **4**
    - WebApps = **1**
    - Database = **1**

	!IMAGE[cswnbno4.jpg](instructions332284/cswnbno4.jpg)

1. [] On the left menu under **Business Case Reports**, select **Current on-premises vs future**.

1. [] Analyze the **On-premises vs Azure cost**.

1. [] Scroll down and compare the **Total cost of ownership** per object. 

1. [] On the left menu under **Business Case Reports**, select **Migration Strategies.** and then review the following sections:

    !IMAGE[jiabw3sq.jpg](instructions332284/jiabw3sq.jpg)
    !IMAGE[h3xgnmxa.jpg](instructions332284/h3xgnmxa.jpg)

1. [] Select the **X** in the top right corner to close the Business Case.

#### Congratulations!
You reviewed business case scope and cost outputs to understand projected on-prem vs Azure costs, savings, and strategy assumptions based on discovered data.

===

## Task 04: Review the Assessment created by the Business Case

### Introduction
Terra Firm wants options: lift-and-shift where it makes sense, but also modernization paths that reduce long-term work and improve security. The assessment translates discovery into readiness and recommendations, so Dennis can compare VM migration versus PaaS/container options and choose what best fits the pilot's needs.

### Description
In this task, you'll open the assessment created by the business case and compare recommendations across **PaaS preferred**, **PaaS only**, and **Lift and shift to Azure VM** views. You'll observe how targets and readiness differ by path.

### Success criteria
- The assessment shows **4 workloads** and uses the **Modernize** preference.
- You can identify the recommended target approach for the web app and database across the different paths.

### Key tasks
- Open the assessment produced by the business case and verify workload count and migration preference.
- Compare the migration paths across **PaaS preferred**, **PaaS only**, and **Lift and shift**.
- Use **View details** to identify the recommended target services for the app tier and database tier.


1. [] In the Azure Migrate project, under **Decide and Plan**, select **Assessments**.

1. [] Select the **businesscase-bc-@lab.LabInstance.Id** assessment.

1. [] Ensure that **4** Workloads are displayed and that the Migration preference is set to **Modernize**.

	!IMAGE[c4l7amcd.jpg](instructions332284/c4l7amcd.jpg)

1. [] Compare the migration options between PaaS preferred, Paas only, and Lift and shift to Azure VM.

1. [] On the **Paas preferred** tile, select **View details**.

	!IMAGE[n9g0c9h0.jpg](instructions332284/n9g0c9h0.jpg)

1. [] Observe the recommended migration path and readiness state:

	- Replatform the Web app to an App Service Container.
    - Replatform the PostgreSQL instance to Azure Database for PostgreSQL.
    - Rehost the two remaining Servers to Azure VMs.

1. [] On the top menu, select **Paas only**

	!IMAGE[45faob97.jpg](instructions332284/45faob97.jpg)

1. [] Observe the recommended migration path and readiness state:

	- Replatform the Web app to AKS.
    - Replatform the PostgreSQL instance to Azure Database for PostgreSQL.
    - Rehost the two remaining Servers to Azure VMs.

1. [] On the top menu, select **Lift and shift Azure VM**.

1. [] Observe the recommended migration path and readiness state:

    !IMAGE[fzghg18z.jpg](instructions332284/fzghg18z.jpg)

    - Rehost the all 4 Servers to Azure VMs.

1. [] Select the **X** in the top right corner to close the assessment page.

#### Congratulations! 
You compared modernization options and validated the recommended migration approach for the web app and PostgreSQL workload across PaaS and IaaS paths.

===

## Task 05: Create a wave plan using the recommended path

### Introduction
One of Terra Firm's biggest worries is downtime, so "move everything at once" is not ideale. Wave planning helps Dennis break migration into manageable phases, align tasks and dependencies, and create an execution plan that supports faster delivery *and* safer rollback decision points.

### Description
In this task, you'll create a wave from the assessment's recommended path and then review wave settings, tasks, selected applications/workloads, and target settings. You'll confirm the wave includes the Airsonic application and the discovered workloads.

### Success criteria
- A wave plan named **wave-@lab.LabInstance.Id** exists under **Decide and Plan > Wave planning**.
- The wave includes the Airsonic application and the expected workloads, and you can review its target settings and migration options.

### Key tasks
- Create a wave plan directly from the assessment's **Recommended path**.
- Add at least two custom tasks to the wave and save changes.
- Review application/workload selections, target settings, and migration options associated with the wave.


1. [] In the Azure Migrate project, under **Decide and Plan**, select **Assessments**.

1. [] Select the **businesscase-bc-@lab.LabInstance.Id** assessment.

1. [] On the **Recommended path** tile, select **Create wave**.

	!IMAGE[37pvds2d.jpg](instructions332284/37pvds2d.jpg)

1. [] On the **Applications and workloads** popup, select **OK**.

1. [] For the Wave name, enter +++wave-@lab.LabInstance.Id+++.

1. [] For the planned start date, leave the **default**.

	>[!Note] You do not need to add workloads to the wave plan since we are using the assessment that was already created.

1. [] Select **Create wave**.

	>[!Note] It will take several minutes to create the wave. If you receive an error during the creation, you can safely ignore it.

1. [] Select the **X** in the top right corner to close the assessment page.

1. [] In the Azure Migrate project, under **Decide and Plan**, select **Wave planning**.

1. [] Select the **wave-@lab.LabInstance.Id** wave plan.

	>[!Alert] If you receive a warning regarding the Hyper-V replication appliance, you can safely ignore it. You'll configure this in a later exercise.

1. [] On the overview page, observe the settings and options.

1. [] On the left menu, select **Wave settings**.

1. [] At the bottom of the page select **+ Add Task**.

1. [] For the Task name, enter +++Airsonic migration+++, and then select **Add**.

1. [] Select **+ Add Task** again.

1. [] For the Task name, enter +++Linux VM migration+++, and then select **Add**.

1. [] Select **Save changes** at the bottom of the window.

1. [] On the left menu, select **Applications and workloads**.

1. [] Under Applications, select **1 application(s) selected**.

	!IMAGE[4yxwz1s1.jpg](instructions332284/4yxwz1s1.jpg)

	>[!Note] You should see the airsonic app that you defined earlier.

1. [] Select the **X** in the top right side of the window, to close the application window.

1. [] Under Workloads, select **2 workload(s) selected**.

	!IMAGE[fckapn3z.jpg](instructions332284/fckapn3z.jpg)

	>[!Note] You should see the 4 servers, with both Supersonic servers selected.

    !IMAGE[kt38tdt9.jpg](instructions332284/kt38tdt9.jpg)

1. [] Select the **X** in the top right side of the window, to close the select workloads window.

1. [] On the left menu, select **Target settings**.

1. [] Review the Tasks associated with each workload.

	!IMAGE[n5g8mikl.jpg](instructions332284/n5g8mikl.jpg)

1. [] Review the **Target settings** associated with each workload.

	!IMAGE[iv4yrxll.jpg](instructions332284/iv4yrxll.jpg)

1. [] On the left menu, select **Migrations**, and review the available options.

#### Congratulations! 
You created a wave plan from the assessment, added tasks, confirmed included applications/workloads, and reviewed target settings and migration options for execution planning.

===

## Task 06: Map identity from FreeIPA to Microsoft Entra ID (planning only)

### Introduction
Terra Firm currently relies on FreeIPA, but Dennis wants the future to run on Microsoft Entra ID, without breaking access for admins, apps, or service accounts. Mapping identity up front prevents post-migration surprises (like nobody being able to sudo) and sets the groundwork for consistent access control and auditability in Azure.

### Description
In this task, you'll document an identity mapping strategy from FreeIPA to Microsoft Entra ID. You'll identify identity categories used by the workloads and create a mapping table that includes admins, application users, and service identities.

### Success criteria
- Identity categories relevant to the migrated workloads are documented.
- A mapping table with at least **6** entries is completed, showing FreeIPA identities and planned Entra targets and usage.

### Key tasks
- Identify the FreeIPA identity categories used by Linux, application, and database administration.
- Create a mapping table that includes admins, app users, and service accounts.
- Document where each mapped identity will be used after migration (SSH/sudo, DB roles, app authorization, secrets).


You won't migrate identity in this exercise. Instead, you'll document a mapping strategy that supports the migrated Linux and application environment.

Using notepad or a piece of paper, document the following:

1. [] What identity categories that exist in **FreeIPA** for your workloads:

    - Linux administrators
    - Application administrators
    - Database administrators
    - Application users
    - Service accounts (Tomcat-to-PostgreSQL connection identity)

1. [] Create a mapping table in your notes and add at least 6 entries:

| FreeIPA user/group | Purpose | Entra ID target (user/group) | Where used after migration |
| -------- | -------- | -------- | -------- |
| ipaadmins | Linux admin access | Linux-Admins | SSH/sudo |
| dbadmins | DB admin | Airsonic-DB-Admins | DB roles |
| appadmins | App admin | Airsonic-App-Admins | App authorization |
| svc_airsonic | App→DB connection | (planned service identity) | App config/secrets |
| user01 | App user | user01 | AppUsers group |
| user02 | App user | user02 | AppUsers group |

#### Congratulations! 
You documented a FreeIPA-to-Entra identity mapping plan that identifies key users, groups, and service identities required to operate the migrated workload securely.

===

# Exercise 02: Migrate PostgreSQL to Azure Database for PostgreSQL Flexible Server

## Introduction
This exercise modernizes the database tier by migrating the on-premises PostgreSQL backend to Azure Database for PostgreSQL Flexible Server using private access. You'll deploy the flexible server, ensure the lab environment has the required hybrid connectivity, validate migration readiness, perform the migration, and then verify the application successfully uses the new database target. The goal is to reinforce modernization benefits (managed platform service) while practicing validation, cutover, and post-migration functional checks.

## Description
In this exercise, you'll:
- Deploy a PostgreSQL flexible server with private access (VNet integration), specifying compute, authentication, and networking settings.
- Establish/confirm hybrid connectivity (VPN) required to reach private Azure endpoints from the lab environment.
- Create a simple pre-migration marker inside the Airsonic application and confirm the current on-prem database endpoint.
- Retrieve the flexible server's internal IP address from Private DNS records for later connectivity configuration and validation.
- Run a validation migration, review results, then run an offline migration of the Airsonic database to flexible server.
- Update the application configuration to point to the migrated database and verify application behavior and data after cutover.

## Success criteria
- A PostgreSQL flexible server named **pgsql-flex-@lab.LabInstance.Id** is deployed successfully using private access.
- Hybrid connectivity is active so the lab environment can reach the flexible server via private networking/DNS.
- Database migration validation completes successfully and the migration job completes with status **Succeeded**.
- The Airsonic application is configured to use the new database target and the pre-migration marker (playlist) is still present after migration.
- Database settings reflect the new target endpoint after cutover verification.

## Key tasks
- Deploy PostgreSQL flexible server using private access and confirm it is reachable through the lab's hybrid connectivity.
- Establish a pre-migration validation marker and confirm the application's original database endpoint.
- Retrieve the flexible server private IP via Private DNS to support configuration and troubleshooting.
- Validate and migrate the PostgreSQL database using the Azure Database Migration Service workflow.
- Update application connectivity to the migrated database and verify application function and data integrity.

## Expected duration: 50 Minutes

===

## Task 01: Create the Azure Database for PostgreSQL flexible server

> [!IMPORTANT]
>
> Removed as Azure Database for PostgreSQL flexible server will be pre-instanced for students.

<!--

### Introduction
For the pilot workload, Terra Firm wants to reduce management overhead and tighten security, especially with concerns about zero-day threats. Moving PostgreSQL to Azure Database for PostgreSQL Flexible Server gives Dennis's team a managed database foundation with built-in platform capabilities, while private access helps keep traffic off the public internet.

### Description
In this task, you'll deploy a PostgreSQL flexible server using private access (VNet integration). You'll configure compute, authentication, and networking so the application can connect privately after migration.

### Success criteria
- The PostgreSQL flexible server **pgsql-flex-@lab.LabInstance.Id** is deployed successfully.
- The server is configured with **Private access (VNet Integration)** and the expected VNet/subnet.

### Key tasks
- Create a PostgreSQL flexible server with the specified name, version, and compute sizing.
- Configure **Private access (VNet integration)** networking using the lab VNet/subnet.
- Confirm the deployment completes successfully before proceeding.

1. [] On the Lab VM, return to the Azure portal +++https://portal.azure.com+++.

1. [] In the Azure portal search bar, search for and then select +++Azure Database for PostgreSQL flexible servers+++.

1. [] Select **+ Create**

1. [] On the New Azure Database for PostgreSQL flexible server, enter the following:

    | Object | Value |
    | -------- | -------- |
    | Resource group | **AZMigrate** |
    | Server name | +++pgsql-flex-@lab.LabInstance.Id+++ |
    | Region | **@lab.CloudResourceGroup(AZMigrateRG).Location** |
    | PostgreSQL version | **17** |
    | Workload type | **Production** |


1. [] Under **Compute + storage**, select **Configure server**.

	!IMAGE[p0cp08uf.jpg](instructions332284/p0cp08uf.jpg)

1. [] Under **Compute size**, select **Standard_D2ads_v5 (2 vCores, 8 GiB memory, 3200 max iops)**.

	!IMAGE[v81l0qkt.jpg](instructions332284/v81l0qkt.jpg)

1. [] Select **Save** and then configure the following:

    | Object | Value |
    | -------- | -------- |
    | Zone resiliency | **Disabled** |
    | Authentication method | **PostgreSQL authentication only** |
    | Administrator login | +++pgadmin+++ |
    | Password | +++Password~1+++ |

1. [] Select **Next : Networking >**.

1. [] Select **Private access (VNet Integration)**.

	!IMAGE[dkvwlsma.jpg](instructions332284/dkvwlsma.jpg)

1. [] In the Virtual network section, ensure that the selected Virtual Network is **migrate@lab.LabInstance.Id**.

1. [] Next to **Subnet**, select **migrate@lab.LabInstance.Id/flex_server_subnet (192.168.100/128/26)**.

	>[!Alert] Please ensure that you choose the correct subnet above. Not choosing the correct subnet will cause late exercises to fail.

1. [] Select **Next : Security >**.

1. [] Select **Next : Tags >**.

1. [] Select **Next : Review + create >**

1. [] Select **Create**.

>[!Note] It will take approximately 8-10 minutes for the deployment to complete. You can continue to the next task while it deploys.

#### Congratulations! 
You deployed a PostgreSQL Flexible Server using private access so the database tier can be modernized to a managed service within the lab VNet.

-->



===

## Task 02: Connect the Site-to-Site VPN

> [!IMPORTANT]
>
> Should we consider moving this to [Exercise 00](#exercise-00-prepare-for-workload-migration-with-azure-migrate-and-discover-workloads-on-hyper-v-host)?

### Introduction
Terra Firm doesn't want sensitive database traffic exposed, and they prefer private endpoints whenever possible. Establishing VPN connectivity gives the lab environment a secure path into Azure so the team can reach private resources (like the PostgreSQL service) during migration and validation-without punching public holes in the design.

### Description
In this task, you'll run the provided PowerShell script to establish VPN connectivity. You'll authenticate using device sign in and confirm the connection remains active for subsequent steps.

### Success criteria
- Device sign in authentication completes successfully for the VPN connection.
- The VPN connection remains active for later tasks that require private connectivity.

### Key tasks
- Run the VPN automation script from the desktop and start device sign in.
- Complete authentication in the browser using the provided code.
- Confirm the script completes and keep the VPN session available for later steps.


1. [] Minimize the browser and locate the **HV-AutomateClientVPNSconnect.ps1** file on the desktop.

    !IMAGE[yd4e9zdv.jpg](instructions332284/yd4e9zdv.jpg)

1. [] Right select on the file and then select **Run with PowerShell**.

1. [] Wait until you see the prompt to sign in using a web browser. **Copy the code** from the PowerShell window.

	!IMAGE[breubrz1.jpg](instructions332284/breubrz1.jpg)

1. [] Open a new browser tab and connect to +++microsoft.com/devicelogin+++.

1. [] **Paste the code**, and then select **Next**.

1. [] Select **@lab.CloudPortalCredential(User1).Username**, and then select **Continue**.

1. [] Once signed in, close the browser tab and return to the PowerShell window.

1. [] At the Select a subscription prompt, select **Enter** to use the default.

	>[!Note] You can safely minimize the PowerShell window while it connects the VPN.

#### Congratulations! 
You established hybrid connectivity from the lab environment to Azure so private endpoints and internal Azure resources can be reached during migration and validation.

===

## Task 03: Validate and customize the application data

### Introduction
Terra Firm's stakeholders will ask, "How do we know nothing changed after migration?" A simple pre-migration marker (like a unique playlist) makes post-migration validation quick and undeniable-and checking the current database settings gives Dennis a clean "before" snapshot to compare against after cutover.

### Description
In this task, you'll log in to Airsonic, create a unique playlist name as a marker, and review the application's current database connection settings. You'll use this marker later to confirm migration success.

### Success criteria
- You can log in to Airsonic successfully.
- A unique playlist marker is created and visible in the UI.
- The current database endpoint is reviewed and understood as the pre-migration baseline.

### Key tasks
- Open Airsonic and authenticate using the provided lab credentials.
- Create a unique playlist (or rename one) as a post-migration validation marker.
- Review the **Settings > Database** page to confirm the current DB endpoint.

## Connect to the Airsonic application

1. [] Open a new browser tab and connect to +++http://airsonic-frontend:8080/airsonic+++.

1. [] Login using +++admin+++ for the username and +++admin+++ for the password.

## Cutomize the interface in order to identify the database after the migration.

1. [] On the left menu under **Playlists**, select **Create new playlist**.

1. [] Select the newly create playlist (the name will default to today's date).

1. [] Select **Edit**, and then enter a **Unique name** for the playlist.

	>[!Alert] It may take a minute before you are able to select the **Edit** option, please be patient. 

1. [] In the top menu, select **Settings**, and then select **Database**.

	>[!Note] Notice that the application is currently pointing to 192.168.1.165 for the PostgreSQL database. That is the address of the on-prem server hosting the database.

    !IMAGE[08drk342.jpg](instructions332284/08drk342.jpg)

1. [] In the top right corner, select **Log out**.

	!IMAGE[4jo6v3al.jpg](instructions332284/4jo6v3al.jpg)

1. [] Close the airsonic browser tab.

1. [] Return to the Azure portal and confirm that your PostgreSQL Flexible Server deployment is complete.

	>[!Note] If the deployment has not completed, wait for it to finish before you continue to the next step.

#### Congratulations! 
You created an identifiable marker in the application and confirmed the pre-migration database endpoint so you can clearly validate the migration outcome later.

===

## Task 04: Retrieve the internal IP address of the Flexible PostgreSQL server

### Introduction
Because Terra Firm is using private access, the database endpoint needs to resolve correctly inside the private network - not out on the public internet. Pulling the internal IP (via Private DNS) helps the team confirm name resolution and connectivity are behaving as intended before they point production-like workloads at the new database.

### Description
In this task, you'll locate the private DNS record for the flexible server and record the internal IP address. This value will be used for application connectivity configuration later in the lab.

### Success criteria
- The private DNS record for the flexible server is located successfully.
- The flexible server internal IP address is recorded in the lab textbox.

### Key tasks
- Open **Private DNS zones** and locate the zone/records related to the flexible server.
- Identify the **A record** for the flexible server and record its IP address.
- Store the IP address for use in subsequent configuration steps.

1. [] In the Azure portal search bar, search for and then select +++Private DNS zones+++.

1. [] Select the Private DNS zone that begins with **pgsql-flex-@lab.LabInstance.Id.xxxxxxxx**.

1. [] Select the number that appears under **Recordsets** (2).

	!IMAGE[55fvhdc0.jpg](instructions332284/55fvhdc0.jpg)

1. [] Locate the record that appears as **Type A** and enter the value for the IP address in the text box below:

	 @lab.TextBox(FlexIP)

	>[!Note] This is the internal IP address assigned to the Flexible PostgreSQL server. You'll use this in a later exercise.

1. [] Select the **X** in the top right corner of the window, to close the Private DNS zone flyout.

#### Congratulations! 
You located the private DNS record for the flexible server and recorded its internal IP address for use in application configuration and connectivity validation.

===

## Task 05: Validate and migrate the database

### Introduction
Dennis wants to accelerate migration, but not by skipping safety checks. Running validation first helps Terra Firm catch problems early (permissions, connectivity, compatibility) so the actual migration is smoother, and it supports the customer's rollback concerns by reducing the chance of a failed cutover.

### Description
In this task, you'll run a validation migration job, review results for errors, and then run an offline database migration to PostgreSQL flexible server. You'll monitor both jobs until they succeed.

### Success criteria
- The validation job completes successfully with no blocking errors.
- The migration job completes with status **Succeeded** for the Airsonic database.

### Key tasks
- Create and run a **Validate** migration job using the Migration Service for PostgreSQL.
- Review validation output for errors/warnings and confirm readiness.
- Create and run a **Validate and migrate** offline migration job and monitor status to completion.


## Validate the database migration

1. [] In the Azure portal search bar, search for and then select +++Azure Database Migration Services+++.

1. [] On the **Azure Database Migration Services** page, select **Start a new migration**.

	!IMAGE[6o7i9gdk.jpg](instructions332284/6o7i9gdk.jpg)

1. [] On the **Select migration scenario and Database Migration Service** page, enter the following:

    | Object | Value |
    | -------- | -------- |
    | Source server type | **PostgreSQL** |
    | Target server type | **Azure Database for PostgreSQL** |
    | Database Migration Service | **Migration Service for Azure Database for PostgreSQL** |
    | Subscription | **@lab.CloudSubscription.Name** |
    | Resource Group | **@lab.CloudResourceGroup(AZMigrateRG).Name** |
    | Azure Database for PostgreSQL | **pgsql-flex-@lab.LabInstance.Id** |

1. [] Select **Go to target server**.

1. [] On the pgsql-flex-@lab.LabInstance.Id | Migration page, select **+ Create**.

1. [] On the **Setup** tab, enter the following values:

    | Object | Value |
    | -------- | -------- |
    | Migration name | +++airsonic-db-validation+++ |
    | Source server type | **On-premises server** |
    | Migration option | **Validate** |
    | Migration mode | **Offline** |

1. [] Select **Next: Runtime server >**

> [!IMPORTANT]
>
> Petroula mentioned Runtime server step is not needed. Will remove once validated in lab testing.

1. [] Next to Use runtime server, select **Yes** and then enter the following:

    | Object | Value |
    | -------- | -------- |
    | Subscription | **@lab.CloudSubscription.Name** |
    | Resource Group | **@lab.CloudResourceGroup(AZMigrateRG).Name** |
    | Azure Database for PostgreSQL | **pgsql-flex-@lab.LabInstance.Id.postgres.database.azure.com** |

	>[!Alert] If the Next : Source server > icon appears greyed out. Reselect the three options about and it should become available.

1. [] Select **Next : Source server >**.

1. [] On the **Source server** tab, enter the following values:

    | Object | Value |
    | -------- | -------- |
    | Server name | +++192.168.1.165+++ |
    | Port | +++5432+++ |
    | Administrator login | +++admin+++ 
    | Password | +++Password~1+++ |
    | SSL mode | **Prefer** |

1. [] Select **Connect to source** and confirm the connection.
    
1. [] Select **Next: Target server >**.

1. [] On the Target server tab, in the Password field, enter +++Password~1+++, and then select **Connect to target**.

1. [] Wait for the **Connection successful** message, and then select **Next: Databases to validate or migrate >**.

1. [] On the **Databases to validate and migrate** tab, select **airsonic**, and choose **Next: Summary >**.

1. [] On the **Summary** tab, review the configuration and select **Start validation**.

	>[!Note] It will take several minutes to complete the validation. You can periodically refresh the page until the Status displays **Succeeded**.

1. [] Once the validation is complete, select the **airsonic-db-validation** job.

1. [] Inspect the results of the validation and confirm that there are no errors.

1. [] Scroll to the bottom and select the **airsonic** database.

1. [] Inspect the flyout page for any errors, and then select the **X** in the top right to close the flyout.

1. [] Select the **X** in the top right corner to close the validation results page.

## Migrate the database

1. [] On the pgsql-flex-@lab.LabInstance.Id | Migration page, select **+ Create**.

1. [] On the **Setup** tab, enter the following values:

    | Object | Value |
    | -------- | -------- |
    | Migration name | +++airsonic-db-migration+++ |
    | Source server type | **On-premises server** |
    | Migration option | **Validate and migrate** |
    | Migration mode | **Offline** |

1. [] Select **Next: Runtime server >**

1. [] Next to Use runtime server, select **Yes** and then enter the following:

    | Object | Value |
    | -------- | -------- |
    | Subscription | **@lab.CloudSubscription.Name** |
    | Resource Group | **@lab.CloudResourceGroup(AZMigrateRG).Name** |
    | Azure Database for PostgreSQL | **pgsql-flex-@lab.LabInstance.Id.postgres.database.azure.com** |

	>[!Alert] If the Next : Source server > icon appears greyed out. Reselect the three options about and it should become available.

1. [] Select **Next : Source server >**.

1. [] On the **Source server** tab, enter the following values:

    | Object | Value |
    | -------- | -------- |
    | Server name | +++192.168.1.165+++ |
    | Port | +++5432+++ |
    | Administrator login | +++admin+++ 
    | Password | +++Password~1+++ |
    | SSL mode | **Prefer** |

1. [] Select **Connect to source** and confirm the connection.
    
1. [] Select **Next: Target server >**.

1. [] On the Target server tab, in the Password field, enter +++Password~1+++, and then select **Connect to target**.

1. [] Wait for the **Connection successful** message, and then select **Next: Databases to validate or migrate >**.

1. [] On the **Databases to validate and migrate** tab, select **airsonic**, and choose **Next: Summary >**.

1. [] On the **Summary** tab, review the configuration and select **Start validation and migrate**.

1. [] Monitor the progress of the migration on the **Migration** page.

	>[!Note] The migration should take approximately 5 minutes. The migration is complete when the status displays **Succeeded**. You can periodically **refresh** to update the status.

    !IMAGE[sftftznj.jpg](instructions332284/sftftznj.jpg)

#### Congratulations! 
You validated the migration, reviewed results, and completed an offline database migration to PostgreSQL Flexible Server with a successful status.

===

## Task 06: Verify the database migration

### Introduction
A successful migration isn't "the tool said Succeeded" - it's "the app still works and the data is intact." This verification step lets Terra Firm prove the pilot workload is using the new Azure database and that the pre-migration marker data is still there, exactly the kind of confidence Dennis needs before scaling this approach to more workloads.

### Description
In this task, you'll update the Airsonic configuration to point to the migrated database, reboot the application server, and verify the app works and displays the pre-migration marker data.

### Success criteria
- The Airsonic app starts successfully after configuration changes and reboot.
- You can log in and confirm the pre-migration marker (playlist) is present.
- The database settings reflect the new database endpoint after migration.

### Key tasks
- Update the Airsonic database connection configuration to use the flexible server endpoint.
- Reboot the Airsonic server and wait for the service to become available.
- Log in to Airsonic and confirm functionality and expected data after migration.

## Point the on-prem airsonic web server to the newly migrate database 

1. [] Minimize the browser, and then locate and launch the **PuTTy** application from the desktop. 

1. [] In the **Host Name (or IP address)** field, enter +++airsonic-frontend+++, and then select **Open**.

1. [] On the **PuTTY Security Alert** pop-up, select **Accept**.

1. [] Login using +++skilladmin+++ and +++PurpleFrog3109!+++ (the password will not display on the screen).

1. [] Edit the airsonic configuration file to point to the newly migrated database by running the following command:

	+++sudo nano /var/airsonic/airsonic.properties+++
    
1. [] Locate the **DatabaseConfigEmbedUrl=jdbc:postgresql://192.168.1.165:5432/airsonic?stringtype=unspecified** line and change only the IP address to +++@lab.Variable(FlexIP)+++.

1. [] In the line below, change the username from admin to +++pgadmin+++ (the admin account that you created for the Azure database).

1. [] Save the file by selecting **Ctrl + X** and then select **Y** to save the modified buffer.

1. [] Select **Enter** to save the file.

1. [] Reboot the airsonic web server by entering +++sudo reboot+++.

1. [] On the **PuTTY Fatal Error** popup, select **OK**, and the close the PuTTY window.

## Test the database migration

1. [] Open a new browser tab and connect to +++http://airsonic-frontend:8080/airsonic+++

	>[!Note] It may take a few minutes for the server to reboot and the site to become available.

1. [] Login using +++admin+++ for the username and +++admin+++ for the password.

1. [] Verify that the playlist that you created prior to the migration appears on the left side.

1. [] In the top menu, select **Settings**, and then select **Database**.

	>[!Note] Observe that the application is pointing to @lab.Variable(FlexIP) as IP address for the PostgreSQL database. That is the IP address of the Flexible PostrgeSQL server that you recorded earlier.

    !IMAGE[smx9yhdb.jpg](instructions332284/smx9yhdb.jpg)

1. [] Close the airsonic browser tab.

#### Congratulations! 
You repointed the application to the migrated database and confirmed the app still works and retains expected data, validating a successful cutover.

===

# Exercise 03: Migrate Linux Middleware/Runtime Server using Azure Migrate

## Introduction
This exercise applies Azure Migrate Server Migration to move a Hyper-V VM to Azure as an Azure VM (lift-and-shift). You'll prepare the Hyper-V replication environment, install and register the replication provider, configure required Azure-side resources and permissions, replicate the VM, perform a planned migration cutover, and then validate that the migrated workload runs successfully in Azure. The focus is on learning the end-to-end mechanics of VM migration using Azure Migrate and understanding what "complete" looks like after cutover (testing + stopping replication).

## Description
In this exercise, you'll:
- Deploy the Azure Migrate replication resources for Hyper-V migrations and download required provider/credential files on the Hyper-V host.
- Install the Hyper-V replication provider and register the Hyper-V host to the Recovery Services vault used by Azure Migrate.
- Finalize registration in the Azure portal and configure required permissions so replication can access the cache storage account.
- Start replication of the target VM using assessment-based settings and monitor until replication reaches a protected state.
- Perform a planned migration (cutover) to create and start the Azure VM using the latest replicated data.
- Stop replication after migration and validate workload/application functionality on the migrated Azure VM.

## Success criteria
- The Hyper-V host is successfully registered and finalized for Azure Migrate Server Migration.
- Replication begins and the VM reaches a **Protected** replication status.
- Planned migration completes successfully and the Azure VM is created and running.
- Replication is stopped after cutover and replication state is cleaned up.
- The migrated workload is validated in Azure (the application is reachable and expected data/behavior is confirmed).

## Key tasks
- Prepare Hyper-V migration prerequisites: provider installation, host registration, and Azure resource readiness.
- Configure required permissions so replication can use the cache storage account successfully.
- Replicate the VM using assessment-derived settings and monitor replication health/status.
- Execute a planned migration cutover and validate the migrated VM in Azure.
- Finalize migration by stopping replication and confirming the workload operates as expected post-migration.

## Expected duration: 75 Minutes

===

## Task 01: Prepare for replication

### Introduction
Not every Terra Firm workload will be modernized on day one-some systems need a clean lift-and-shift first to reduce risk and timeline. Preparing replication sets up Azure Migrate Server Migration so Dennis's team can continuously replicate a Linux VM to Azure, making the eventual cutover faster and less disruptive.
Azure Migrate Server Migration uses replication to copy Hyper-V VM data to Azure. Preparing for replication requires deploying supporting Azure resources, installing the replication provider on the Hyper-V host, and ensuring required permissions are in place.

### Description
In this task, you'll create the migration resources, install and register the Hyper-V replication provider, finalize registration in Azure Migrate, and configure access on the cache storage account so replication can proceed.

### Success criteria
- The Hyper-V host registration is finalized in Azure Migrate.
- Migration resources (vault and related components) are created successfully.
- Required permissions are configured so the vault/service can access the cache storage account.

### Key tasks
- Deploy migration resources for Hyper-V replication and download provider + vault credentials.
- Install the replication provider on the Hyper-V host and register it using the downloaded credentials file.
- Enable required identities/permissions and grant RBAC roles on the cache storage account.


1. [] Connect to the HyperV host by using the HyperV-01 Host.rdp icon.

	!IMAGE[ao294nfy.jpg](instructions332284/ao294nfy.jpg)

1. [] Login using +++administrator+++ and +++Passw0rd!+++.

1. [] Open a web browser on the HyperV-01 Host and navigate to +++portal.azure.com+++.

1. [] Sign in using your lab's Azure credentials. 

    | Item | Value |
    |:--------|:--------|
    | Username   | +++@lab.CloudPortalCredential(User1).Username+++   |
    | TAP  | +++@lab.CloudPortalCredential(User1).AccessToken+++   |

1. [] Select **Yes** if prompted to stay signed in.

1. [] In the Azure portal, search for +++Azure Migrate+++ in the search bar and select **Azure Migrate** under **Services**.

    ![Azure Migrate is displayed in the Azure Search bar and highlighted in the search results.](instructions312691/17-AzureMigrate.png)

1. [] On the Azure Migrate blade, select **All projects**, and then select the **Migration-Project-@lab.LabInstance.Id** project.

1. [] Expand **Execute** in the left menu, select **Migrations**, and on the Migration blade, select **Discover more**.

    ![The Discover more button is highlighted on the Migrations blade.](instructions312691/42-Migration-dicover.png)

1. [] On the **Discover** blade:


    | Object | Value |
    | -------- | -------- |
    | Where to you want to migrate to? | **Azure VM** |
    | Are your machines virtualized? | **Yes, with Hyper-V** |
    | Target region | **@lab.CloudResourceGroup(AZMigrateRG).Location** |

1. [] Check the box for **Confirm that the target region for migration is @lab.CloudResourceGroup(AZMigrateRG).Location**.

1. [] Select **Create resources**.

1. [] When the deployment completes, under **1. Prepare Hyper-V host servers**, select the **Download** link to download the Hyper-V replication provider software installer.

1. [] Select the **Download** button to download the registration key.

    ![The Download link and button are highlighted and numbers 1 and 2 under Prepare Hyper-V host servers.](instructions312691/44-Download.png)

    >[!Alert] The files must be downloaded on the HyperV host, not the Migrate01 VM.

1. [] In the downloads dropdown, hover over **AzureSiteRecoveryProvider.exe**, and the select **Open file**.

1. [] In the **Microsoft update** page, select **Off**, and then select **Next**.

1. [] Keep the defaults and select **Install**.

1. [] Select **Register** and on the **Vault Settings...** screen, select **Browse**

1. [] On the left menu, select the **Downloads** folder and then select the **Migration-Project-@lab.LabInstance.Id.Location-MigrateVault-@lab.LabInstance.Id_Migration-Project-@lab.LabInstance.Id-HyperVSite_xxxxxxx.VaultCredentials** file.

1. [] Select **Open**.
   
1. [] Select **Next**.

1. [] Select **Connect directly to Azure Site Recovery without a proxy server** and select **Next**.

1. [] Configuration Azure Site Recovery for registration will take one or two minutes to install. When it completes, select **Finish**.

1. [] Return to the **Discover** page in the Azure portal and **refresh the browser page**.

1. [] Select **Azure VM** under **Where do you want to migrate to?** and **Yes, with Hyper-V** under **Are your machines virtualized?** to reload the previous discover page.

    > You should see that you have one connected registration.

1. [] Select **Finalize registration**.

    ![The connected registration and Finalize registrator button are highlighted on the Discover page.](instructions312691/discover-finalize-registration.png)

    > This step may take up to 3 minutes.

1. [] After the registration completes, verify you see the registration finalized message.

    ![The registration finalized message is highlighted.](instructions312691/discover-registration-finalized.png)

1. [] Select **Close** on the **Discover** page.

1. [] Close the RDP connection to return to the Migrate01 VM.

	!IMAGE[p6c8dh0s.jpg](instructions332284/p6c8dh0s.jpg)


## Configure permissions

1. [] In the Azure portal search bar enter and then select +++Recovery Services vaults+++.

1. [] Select the **Migration-Project-@lab.LabInstance.Id-MigrateVault-XXXXXX**.

1. [] Under **Settings**, select **Identity**.

1. [] On the **System assigned** page, set the Status to **On**, and then select **Save**.

1. [] On the **Enable system assigned managed identity** popup, select **Yes**.

1. [] In the Azure portal search bar enter and then select +++storage accounts+++.

1. [] Select the **sto@lab.LabInstance.Id** storage account.

1. [] On the left menu, select **Access Control (IAM)**.

1. [] Select **+ Add** > **Add role assignment**.

1. [] In the search field enter +++Storage Blob Data Contributor+++, and then **select the role**.

1. [] Select **Next**.

1. [] Under **Assign access to**, select **Managed Identity**, and then select **+ Select members**.

1. [] On the **Select managed identities** flyout, under **Managed identity**, select **All system-assigned managed identities (3)**.

1. [] Select the **all** identities, and then select **Select**.

	!IMAGE[62vqbkmb.jpg](instructions332284/62vqbkmb.jpg)

1. [] Select **Review + assign** twice.

1. [] Select **+ Add** > **Add role assignment** again.

1. [] Select the **Privileged administrator roles** tab.

	!IMAGE[92gg0sie.jpg](instructions332284/92gg0sie.jpg)

1. [] Select the **Contributor** role.

1. [] Select **Next**.

1. [] Under **Assign access to**, select **Managed Identity**, and then select **+ Select members**.

1. [] On the **Select managed identities** flyout, under **Managed identity**, select **All system-assigned managed identities (3)**.

1. [] Select the **all** identities, and then select **Select**.

1. [] Select **Review + assign** twice.

#### Congratulations! 
You prepared Azure Migrate Server Migration for Hyper-V by deploying required resources, registering the replication provider, and configuring permissions for replication.

===

## Task 02: Replicate the RHEL VM (Airsonic-Frontend)

### Introduction
Terra Firm's downtime question is front and center: "How long will we be offline?" Replication is one of Dennis's best answers - by keeping Azure up to date with ongoing changes, the team can cut over using the latest data instead of doing a massive copy during the outage window.

### Description
In this task, you'll start replication for the Airsonic-Frontend VM, apply assessment-based settings, and monitor progress until replication is in a protected state.

### Success criteria
- Replication is started for **Airsonic-Frontend**.
- Replication status reaches **Protected**.

### Key tasks
- Start the **Replicate** workflow for Hyper-V VMs and select assessment-based settings.
- Configure target settings (RG, VNet/subnet, cache storage, availability) and VM compute sizing.
- Monitor replication jobs until the VM shows **Protected**.

1. [] In the Azure portal, search for +++Azure Migrate+++ in the search bar and select **Azure Migrate** under **Services**.

    ![Azure Migrate is displayed in the Azure Search bar and highlighted in the search results.](instructions312691/17-AzureMigrate.png)

1. [] On the Azure Migrate blade, select **All projects**, and then select the **Migration-Project-@lab.LabInstance.Id** project.

1. [] Under **Execute**, in the left menu, select **Migrations**, and then the **Replicate** button.

    ![The Replicate button is highlighted on the Migrations blade.](instructions312691/47-Replicate.png)

1. [] On the **Specify intent** page, make these selections:

    1. [] What do you want to migrate? Choose **Servers or virtual machines (VM)**.

    1. [] Where do you want to migrate to? Select **Azure VM**.

    1. [] Are your machines virtualized? Select **Yes, with Hyper-V**.

    1. [] Select **Continue**.

    ![The Specify intent page is populated as specified above.](instructions312691/48-SpecifyIntent.png)

1. [] On the **Replicate** virtual machines tab, make these selections:

1. [] Target VM security type: Choose **Standard or Trusted Launch Virtual machines**.

1. []  Import migration settings from an assessment: select **Yes, apply migration settings from an Azure Migrate assessment**.

1. []  Select assessment: Select the **businesscase-bc--@lab.LabInstance.Id** you created earlier.

1. []  Check the box next to **Airsonic-Frontend**.

1. []  Select **Next**.

1. [] On the **Target settings** tab, make these selections:

    | Object | Value |
    | -------- | -------- |
    | Resource group | **AZMigrateRG** |
    | Register with SQL IaaS extension | **Uncheck**
    | I have an Enterprise Linux license | **Check** |
    | I confirm I have an eligible Enterprise Linux subscription to apply this Azure Hybrid Benefit | **Check** |
    | Cache storage account | **sa@lab.LabInstance.Id** |
    | Virtual network | **migrate@lab.LabInstance.Id** |
    | Subnet | **default** |
    | Availability options | **No infrastructure redundancy required** |

1. [] Select **Next**.

1. [] On the **Compute** tab select the following:

    | Object | Value |
    | -------- | -------- |
    | Azure VM Size | +++Standard_D2s_v4 (2 Cores, 8 GB RAM)+++ |
    | OS Type | **Linux** |
    | Operating System | **Red Hat Enterprise Linux 9** |

	!IMAGE[nodv297u.jpg](instructions332284/nodv297u.jpg)

1. [] Select **Next**.

1. [] On the **Disks** page, select **Next**.

1. [] On the **Tags** page, select **Next**.

1. [] On the **Review + Start replication** page, select **Replicate**.

	>[!Note] You'll be returned to the Specify intent page. It is not necessary to go through this process again. 

1. [] From the breadcrumbs menu at the top, select **Migration-Project-@lab.LabInstance.Id | Migrations**.

	!IMAGE[xe3f93ib.jpg](instructions332284/xe3f93ib.jpg)

1. [] On the **Track migrations** tile, select **Replications Summary**.

1. [] On the left panel, expand **Migration**, and then select **Replications**.

1. [] Monitor the status of your replication job. You'll need to select **Refresh** occasionally.

	>[!Note] The replication will take approximately 30 minutes. The process is complete when the Replication status displays **Protected**.

#### Congratulations! 
You started replication for the Airsonic-Frontend VM using assessment-based settings and confirmed replication progressed to a protected state.

===

## Task 03: Migrate the RHEL VM

### Introduction
This is the "go time" moment Terra Firm worries about most - moving the workload without surprises. A planned migration uses the latest replicated data for a controlled cutover, helping Dennis demonstrate a predictable process and making it easier to justify scaling the approach across the rest of the environment.

### Description
In this task, you'll initiate a planned migration for the replicated Airsonic-Frontend VM and monitor the migration job until it completes.

### Success criteria
- A planned migration completes successfully for Airsonic-Frontend.
- The migrated VM is created in Azure and starts successfully.

### Key tasks
- Confirm replication is protected and initiate **Migrate** (planned failover) from the replication item.
- Approve shutdown of the on-premises VM for a no-data-loss cutover.
- Monitor the migration job until it completes successfully.

1. [] In the Azure portal, return to the **Migration-Project-@lab.LabInstance.Id** Project page, expand **Execute** in the left menu, select **Migrations**, and on the Migration blade, select **Replications summary**.

    ![The Replications summary button on the Project Migrations blade is highlighted.](instructions312691/migrations-replications-summary.png)

1. [] On the **Replications Summary** page, expand **Migration** in the left menu, select **Replications**, and observe the replication you started previously.

    >[!Note] The replication is complete when you see the replication status for the **Airsonic-Frontend** set to **Protected**.

1. [] Start the migration by selecting the ellipsis to the right of the **Airsonic-Frontend** item, and then select **Migrate** from the context menu.

1. [] Select **Yes** to shutting down the VM and performing a planned migration with no data loss.

1. [] Select **Migrate** to begin the migration process.

    >[!Note] The migration process will take approximately 15 minutes.

#### Congratulations! 
You performed a planned migration cutover to create and start the Azure VM using the latest replicated data with minimal risk of data loss.

===

## Task 04: Finalize the migration

### Introduction
Once Terra Firm confirms the pilot VM runs correctly in Azure, there's no need to keep paying for (or managing) replication state. Stopping replication cleans up the migration workflow, and validating the workload end-to-end gives Dennis the proof point the business wants: the app is reachable, functional, and behaving as expected after the move.

### Description
In this task, you'll stop replication for the migrated VM and validate the workload by accessing the Airsonic application hosted on the migrated Azure VM.

### Success criteria
- Replication is stopped and replication state is cleaned up for Airsonic-Frontend.
- The Airsonic application is accessible on the Azure VM and shows expected content.

### Key tasks
- Review migration job completion and confirm planned failover finished successfully.
- Stop replication for the migrated VM to clean up replication state.
- Locate the VM in Azure, record the private IP, and validate application access via browser.

## Stop replication on the migrated VM

1. You can monitor the progress by opening the **Jobs** section on the Migration blade and selecting **Planned failover**.

    ![The Jobs page is displayed with the active and completed jobs shown in a list.](instructions312691/55-jobs.png)

    ![Screenshot of the Planned failover job details.](instructions312691/56-PlannedFailover.png)

	>[!Note] When the migration completes, the status will display **Planned failover finished**
 
1. [] Stop the replication by right-clicking the VM, and selecting **Stop replication**. This action:

    - Stops replication for the on-premises machine.
    - Removes the machine from the Replicated servers count in the Migration and modernization tool.
    - Cleans up replication state information for the VM.

## Confirm the migrated VM functionality

1. In the Azure portal, search for and select +++Virtual machines+++.

	>[!Note] You should see the migrated VM running in your inventory.

1. [] Select the **Airsonic-Frontend** VM.

1. [] On the left menu, under **Networking**, select **Network settings**.

1. [] Locate the **Private IP address** and enter it into the text box below: 
	 
     @lab.TextBox(MigratedIP)

     !IMAGE[utw8ql6y.jpg](instructions332284/utw8ql6y.jpg)

1. [] In a new browser tab, connect to +++http://@lab.Variable(MigratedIP):8080/airsonic+++.

1. [] Login using +++admin+++ for the username and +++admin+++ for the password.

1. [] Verify that the playlist that you created prior to the migration appears on the left side.

	>[!Note] You are now connected to the airsonic application on the VM that was migrated to Azure. That application is connected to the PGSQL database that you migrated previously.

1. [] Close the airsonic browser tab.

<!-- 3. To review additional steps to take after the migration has finished, review the [Complete the migration](https://learn.microsoft.com/azure/migrate/tutorial-migrate-hyper-v?view=migrate-classic&tabs=UI#complete-the-migration) documentation, along with the post-migration best practices found in the same document. -->

#### Congratulations! 
You stopped replication to clean up migration state and validated the migrated VM's functionality by successfully accessing the application in Azure.

===

# Exercise 04: Governance, Security and Cost Optimization

## Exercise introduction
After migrating the Airsonic application tiers to Azure (Airsonic-Frontend as an Azure VM and the PostgreSQL database on Azure Database for PostgreSQL Flexible Server), you must shift from "migration complete" to "production-ready operations." In this exercise you apply governance and operations controls that organizations typically require before workloads are considered steady-state: backups, patching, security posture management, monitoring dashboards, and cost optimization. You'll also capture a rollback/DR plan to ensure the workload can be recovered quickly if issues arise.

## Exercise description
You'll:
- Protect the migrated VM with Azure Backup.
- Configure Update Manager for ongoing patch assessment and scheduled updates.
- Enable Microsoft Defender for Cloud plans and review recommendations.
- Create a Workbook that visualizes VM performance metrics.
- Review costs and optimization recommendations using Cost Management and Advisor.

## Exercise success criteria
By the end of this exercise, you can demonstrate that:
- The **Airsonic-Frontend** VM is protected by **Azure Backup** and at least one **on-demand backup job** has been triggered.
- **Update Manager** periodic assessment is enabled and a **maintenance configuration** exists for scheduled updates (or you have documented the required prerequisite fix if patch orchestration errors occur).
- **Defender for Cloud** plans for **CSPM, Servers, and Databases** are enabled and you have reviewed at least **one recommendation**.
- A **Monitor Workbook** exists and displays **CPU** and **network traffic** metrics for the Airsonic-Frontend VM over the last 24 hours.
- You have reviewed **costs** for the resource group and captured at least **one Advisor recommendation** (and what you would do with it).
- You have a written **rollback + DR strategy** for both the VM and the database service.

## Expected duration: 30 Minutes

===

## Task 01 Add-ins: Enable Azure Backup on a migrated VM

### Task purpose
Terra Firm wants better business continuity than they had on-prem-and they want it built in, not bolted on later. Enabling Azure Backup for the migrated VM gives Dennis a fast win: recoverability for accidents, bad deployments, or "that patch did what?!" moments.

### Notes
- Creating a new Recovery Services vault is fine for a lab. In production, vault strategy is often centralized by environment/app/team.
- Wait for the **Enable Backup** deployment to complete before selecting **Backup now**.

1. [] In the Azure portal, search for and then select +++Virtual Machines+++.

1. [] Select the **Airsonic-Frontend** VM.

1. [] On the left menu, under **Backup + Disaster recovery**, select **Backup**.

1. [] On the **Welcome to Azure Backup for Azure VMs** page, enter the following:


    | Object | Value |
    | -------- | -------- |
    | Recover Services vault | **Create new** |
    | Backup vault | +++Backups-@lab.LabInstance.Id+++ |
    | Resource group | **AZMigrateRG** |
    | Policy sub type | **Standard** |

1. [] Select **Enable Backup**.

	>[!Note] Wait for the deployment to complete.

1. [] In the Azure portal, search for and then select **Virtual Machines**.

1. [] Select the **Airsonic-Frontend** VM.

1. [] On the left menu, under **Backup + Disaster recovery**, select **Backup**.

1. [] Select **Backup now**.

	!IMAGE[zj9oybh7.jpg](instructions332284/zj9oybh7.jpg)

1. [] In the **Retain backups till** field, **select tomorrow's date**, and then select **OK**.

	>[!Note] This triggers an immediate backup of the Airsonic-Frontend VM.

#### Congratulations! 
You enabled Azure Backup for the **Airsonic-Frontend** VM by creating a Recovery Services vault and triggering an **on-demand backup** with a short retention window.


===
## Task 02 Add-ins: Enable Update Manager on a migrated VM

### Introduction
Security is a big concern for Terra Firm, and "zero-day" is not a word Dennis wants to hear in a post-migration incident review. Update Manager helps the team assess and schedule patching so the migrated Linux VM stays current and they can demonstrate ongoing update hygiene across the environment.


### Task purpose
Enable patch assessment and schedule patching so the VM can be maintained consistently and you can demonstrate update compliance.

1. [] On the left menu, under **Operations**, select **Updates**.

1. [] Next to Periodic assessment, select **Enable now**.

	!IMAGE[mgoux85y.jpg](instructions332284/mgoux85y.jpg)

1. [] Next to the Airsonic-Frontend, configure the following options:

    | Value | Option |
    | -------- | -------- |
    | Periodic assessment | **Enable** |
    | Patch orchestration | **Customer Managed Schedules** |

1. [] Select **Save**.

1. [] In the top menu, select **Schedule updates**.

	!IMAGE[588nyg5i.jpg](instructions332284/588nyg5i.jpg)

1. [] On the Create a maintenance configuration page, enter the following:

    | Value | Option |
    | -------- | -------- |
    | Resource group | **AZMigrateRG** |
    | Configuration name | +++Airsonic-updates-@lab.LabInstance.Id+++ |
    | Region | **@lab.CloudResourceGroup(AZMigrateRG).Location** |

1. [] Select **Add a schedule**.

1. [] On the Add/Modify schedule flyout, select a **Start on date of tomorrow**, and then select **Save**.

1. [] Select **Review + create**, and then select **Create**.

	>[!Note] Wait for the deployment to complete.

1. [] In the Azure portal, search for and then select **Virtual Machines**.

1. [] Select the **Airsonic-Frontend** VM.

1. [] On the left menu, under **Operations**, select **Updates**.

1. [] Ensure that Periodic assessment displays **Yes**

	!IMAGE[imctfvn3.jpg](instructions332284/imctfvn3.jpg)

#### Congratulations! 
You enabled **Update Manager periodic assessment** for the migrated VM and created a **maintenance configuration** to schedule updates (or remediated orchestration prerequisites if the assignment failed).

===

## Task 03 Add-ins: Enable Defender for Cloud on migrated workloads

### Introduction
Terra Firm wants protection that scales as they migrate more workloads-not a manual checklist nobody keeps up with. Turning on Defender for Cloud helps Dennis establish continuous security posture management and threat protection, giving the team a consistent way to spot risks and prioritize fixes after migration.


### Task purpose
Turn on security posture management and threat protection so the VM and database are continuously evaluated against Azure security best practices.

### Notes
- Recommendations may take several minutes to populate after enabling plans. If none appear yet, continue and return later.
- If needed, adjust filters on the Recommendations page (subscription/resource type) to confirm you're viewing the correct scope.

1. [] In the Azure portal, search for and then select +++Defender for Cloud+++

1. [] On the overview page, Locate the **Azure subscriptions** (should show the number 1), and select it.

	!IMAGE[u9w0qbho.jpg](instructions332284/u9w0qbho.jpg)

1. [] Scroll to the bottom of the screen and **Expand the subscription** until you see **TechMaster-lod** subscription.

	!IMAGE[xmm6gpi1.jpg](instructions332284/xmm6gpi1.jpg)

1. [] Select the **TechMaster-lod** subscription, and then enable the following Defender plans:

    - Defender CSPM
    - Servers
    - Databases

1. [] Select **Save**.

1. [] Return to the **Defender for Cloud** page, and then select **Recommendations**.

1. [] Evaluate any recommendations that appear.

    >[!Alert] It may take several minutes before recommendations appear. If you do not see any, you can continue with the lab and return later.

#### Congratulations! 
You enabled **Defender for Cloud** plans for CSPM, Servers, and Databases and reviewed recommendations that highlight security improvements for the migrated VM and supporting resources.

===

## Task 04 Add-ins: Create Workbook dashboards to visualize resource usage

### Introduction
One of Terra Firm's requirements is visual monitoring-because dashboards make it easier to spot trouble before users do. Building a Workbook gives Dennis's team an at-a-glance view of VM health and utilization, supporting day-2 operations as the pilot workload settles into Azure.


### Task purpose
Create an operational dashboard that provides quick visibility into the health and utilization of the migrated VM.

### Notes
- If **Virtual Machine Host** namespace metrics aren't available in your tenant, try **Virtual Machine** namespace instead.
- **Split by** may remain disabled if the selected metrics do not expose dimensions. That is expected.
- Your charts should reflect **CPU** and **Network In/Out** (not memory).

1. [] In the Azure portal, search for and then select +++Monitor+++.

1. [] On the left menu, select **Workbooks**.

1. [] Under Quick start, select **Empty** to create a new workbook.

	!IMAGE[7dig93lj.jpg](instructions332284/7dig93lj.jpg)

1. [] In the new workbook, select **+ Add > Add metric**.

1. [] In the Editing metric page, select the following:

    | Object | Value |
    | -------- | -------- |
    | Resource Type | +++Virtual machine+++ |
    | Metric Scope | **Resource Scope** |
    | Virtual machine | **Load all subscriptions<br>Airsonic-Frontend** |
    | Time Range | Last 24 Hours |

1. [] Select **Add Metric**.

	!IMAGE[n257upxu.jpg](instructions332284/n257upxu.jpg)

1. [] Add the following:

    | Object | Value |
    | -------- | -------- |
    | Namespace | **Virtual Machine Host** |
    | Metric | +++Percentage CPU+++ |
    | Aggregation | **Average** |
    | Display Name | +++CPU+++ |

    !IMAGE[cwp3ufy3.jpg](instructions332284/cwp3ufy3.jpg)

1. [] Select **Save**.

1. [] Select **Add Metric**.

	!IMAGE[n257upxu.jpg](instructions332284/n257upxu.jpg)

1. [] Add the following:

    | Object | Value |
    | -------- | -------- |
    | Namespace | **Virtual Machine Host** |
    | Metric | +++Network In Total+++ |
    | Aggregation | **Sum** |
    | Display Name | +++Network In+++ |

1. [] Select **Save**.

1. [] Select **Add Metric**.

	!IMAGE[n257upxu.jpg](instructions332284/n257upxu.jpg)

1. [] Add the following:

    | Object | Value |
    | -------- | -------- |
    | Namespace | **Virtual Machine Host** |
    | Metric | +++Network Out Total+++ |
    | Aggregation | **Sum** |
    | Display Name | +++Network Out+++ |

1. [] Once you have added all three metrics, select **Save**

	!IMAGE[v7z3wdrx.jpg](instructions332284/v7z3wdrx.jpg)

1. [] In the Save As flyout, enter the following:

    | Object | Value |
    | -------- | -------- |
    | Title | +++Airsonic-VM-Monitor-@lab.LabInstance.Id+++ |
    | Resource Group | **AZMigrateRG** |
    | Location | **@lab.CloudResourceGroup(AZMigrateRG).Location** |

1. [] Select **Save As**.

1. [] Observe the resource chart that was created, showing CPU, memory in, and memory out.

	>[!Note] You can retrieve this chart in the future by going to Monitor in the portal, selecting Workbooks, and then selecting the workbook that you saved.

    !IMAGE[qejhqz6b.jpg](instructions332284/qejhqz6b.jpg)

#### Congratulations! 
You created and saved a **Monitor Workbook** that charts **CPU** and **network traffic** for the **Airsonic-Frontend** VM over the last 24 hours, providing a reusable dashboard for ongoing operations.

===

## Task 05 Add-ins: Run cost analysis and apply Advisor recommendations

### Introduction
Terra Firm wants to reduce cost and optimize investments (including Azure Hybrid Benefit), and Dennis will be asked to show receipts. Cost analysis and Advisor recommendations help the team identify what's driving spend and what easy optimizations exist-so the pilot doesn't become the "cloud is expensive" cautionary tale.


### Task purpose
Understand spend and optimization opportunities so you can reduce cost and improve reliability/security post-migration.

### Notes (what to look for)
- In **Cost analysis**, identify the top cost driver (often VM compute) and compare compute vs storage vs backup costs.
- In **Advisor**, capture at least one recommendation (Cost or Reliability are both acceptable if availability is limited).

### Cost analysis

1. [] In the Azure portal, search for and then select +++Cost analysis+++.

1. [] Under Reporting + analytics, select **Cost analysis**.

1. [] Select **Resource Groups**.

	!IMAGE[vhjs68tv.jpg](instructions332284/vhjs68tv.jpg)

1. [] Select the **AzureMigrateRG** at the bottom of the screen.

1. [] Observe the **Total** and **Average** resource costs.

	>[!Note] Select the individual resources to see their respective costs.

### Advisor recommendations

1. [] In the Azure portal, search for and then select +++Advisor+++.

1. [] On the overview page, observe the various tiles and look for any recommendations.

1. [] On the Reliability tile, select the **active reliability recommendations**.

	!IMAGE[vk4kx623.jpg](instructions332284/vk4kx623.jpg)

1. [] Select the individual  recommendation to observe the details.

    !IMAGE[17uohyh7.jpg](instructions332284/17uohyh7.jpg)

#### Congratulations! 
You completed this exercise and the lab! You reviewed resource group costs in **Cost Management** and inspected **Advisor** recommendations to identify practical cost and reliability optimizations for the migrated workload.

===
# Summary

In this lab you planned and executed a full Azure Migrate workflow for a small Linux application environment and then transitioned the migrated workload into steady-state operations.

You started by creating an Azure Migrate project and registering the Azure Migrate appliance to discover on-premises Hyper-V virtual machines and their workloads. After discovery completed, you reviewed the discovered inventory and grouped related components into an application so you could evaluate the stack as a unit. You then built a business case and reviewed the automatically generated assessment to compare modernization paths (PaaS-preferred, PaaS-only, and lift-and-shift). Using assessment outputs, you created a wave plan to organize the migration sequence and documented an identity mapping strategy from FreeIPA to Microsoft Entra ID.

Next, you performed migrations:
- You migrated the PostgreSQL backend to **Azure Database for PostgreSQL Flexible Server**, validated the migration, and updated the application configuration to point to the new database endpoint.
- You migrated the Airsonic frontend by lift-and-shift to an **Azure VM**, validated application functionality, and confirmed the application successfully connected to the migrated database.

Finally, you applied post-migration governance and operational controls by enabling **Azure Backup**, configuring **Update Manager**, enabling **Defender for Cloud** plans and reviewing recommendations, building an **Azure Monitor Workbook** to visualize VM performance, analyzing costs with **Cost Management** and **Advisor**, and documenting a practical **rollback and disaster recovery strategy** for both the VM and the database platform.

At this point, you have:
- A documented plan and assessment-driven decision trail (business case → assessment → wave plan).
- A working migrated application stack (VM + PaaS database).
- Baseline operational protections (backup, patching, monitoring, security posture, and cost optimization guidance).
