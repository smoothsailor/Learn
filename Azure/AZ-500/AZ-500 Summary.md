# AZ-500

# Secure Azure solutions

## Azure Active Directory (AD)

- redifining how businesses operate in today's interconnected landscape

### Features

- Single Sign-on (SSO)
    - one password unlocks multiple doors
    - enhances user experience and productivity
- Application Management
    - cloud and on-premises application identity protection
- Identity Protection
    - Azure AD's Sentinel
    - employs sophisticated algorithms
    - logging activities to instantly spot and react to anomalous behaviors
    - proactive safeguarding of user identities
- Monitoring
    - comprehensive dashboard tracking real-time activities
    - ensuring transparant and controlled environment
- Multi-Factor Authentication (MFA)
    - amplifies security by introducing multiple layers of auth checks
    - not just 1 password anymore, but a duo or trio of verification steps
- B2B and B2C
    - partner with external organisations
    - seamless interactions
- Hybrid Identities
    - bridging the gap between on-premises and cloud identities
    - consistency in identity management, regardless where it resides
- Seamless Integration with Azure services & 3rd party services
    - interconnected ecosystem

## Azure AD vs Azure AD DS vs ADDS - Comparing Services

### Azure Active Directory

- cloud-based identity and access management service
- user authentication and authorization
- offers multiple features
- enables collaboration
- integrates with other Azure services

### Azure AD Domain Services

- fully managed domain service
- offers compatibility
- enables domain join
- ideal for lift-and-shift scenarios
- offers secure and seamless integration

### AD Domain Services

- on-premises Windows Server-based directory service
- provides authentication and authorization
- offers mutiple features
- requires infra setup
- suitable for traditional on-premises environments

## Azure AD Roles

### Azure AD-specific roles

- roles granting permissions to manage resources within Azure AD
- e.g. User Administrator, Application Administrator, Groups Administrator

### Service-specific roles

- roles specific to major M365 services (NOT Azure AD)
- grant permissions to manage all features within their respective services
- e.g. Exchange Administrator, Intune Administrator, SharePoint Administrator, Teams Administrator

### Cross-service roles

- roles that span multiple services
- e.g. Security Administrator for managing M365 Defender, Microsoft Defender Advanced Threat Protection, Microsoft Defender for Cloud Apps
- global roles
    - e.g. Global Administrator, Global Reader
        - applicable across ALL M365 services
- security-related roles
    - e.g. Security Administrator, Security Reader
        - grant access across multiple security services
- compliance roles
    - e.g. Compliance Administrator
        - for managing compliance-related settings across various services

### Azure AD built-in roles

- Global Administrator
    - highest level of access and control in Azure AD
    - can manage all aspects of Azure AD
    - ability to delegate administrative tasks to other users
- Security Administrator
    - focuses on managing security-related aspects of Azure AD
    - configures security settings, security policies, monitors security events
    - responsible for ensuring security of Azure AD resources and protecting against potential threats
- Billing Administrator
    - manages billing and subscription-related tasks in Azure AD
    - views and manages billing information, subs, invoices
    - responsible for monitoring costs, managing budgets, optimizing resource usage
- Global Reader
    - read-only access to Azure AD resources
    - view configurations, settings, reports
    - cannot maky any changes
    - userful for auditors or stakeholders

## Deploying Azure AD Domain Services

### Advantages

- simplified domain services without deploying and managing domain controllers
- seamless transition for legacy applications in the cloud
- integration with existing Azure AD tenant for user sign-in and access control
- centralized identity services and support for both cloud-only and hybrid environments
- scalability and disaster recovery with multiple replica sets in different Azure regions

### Limitations

- limited administrative access
- restricted customization
- limited application compatibility
- no domain trust support
- no LDAP write access
- limited OS support
- limited network integration
- limited availability
- cost

## Azure AD DS and Self-Managed AD DS

- Microsoft creates and manages required resources
- core service components deployed and maintained by Microsoft
- no need to deploy, manage, patch, or secure AD DS infra yourself
- provides managed domain experience with reduced complexity
- ideal for cloud-based applications needing traditional authentication mechanisms

### Standalone Cloud-only AD DS

- Azure VM's configured as domain controllers
- independent cloud-only AD DS environment not integrated with on-premises AD DS
- separate credentials used for VM sign-in and administration

### Hybrid Deployments

- Azure VM's configured as domain controllers
- AD DS domain created within existing forest
- trust relationship configured with on-premises AD DS
- Azure VM's can join resource forest
- user authentication over VPN/ExpressRoute to on-premises AD DS environment

## Creating and Managing Azure AD Users

- user accounts are used for authentication and authorization
- all users must have an account
- optional properties like address, department, etc.
- all users can be accessed from Microsoft Entra ID > Users > All Users
- support for bulk operations like invite, delete, etc.
- sign in and audit log can be tracked

### User Types

- Cloud Identities
- Guest Accounts
    - invited for collaboration
- Directory-Synchronized Users
    - from on-premises Windows AD
    - cannot be created, need to be synced

## Managing Users with Azure AD Groups

### Group Types

- Security groups
    - manage access & permissions
    - bundle users together
    - basic group
- Microsoft 365 groups
    - all about collaboration
    - e.g. shared e-mail inboxes, shared SharePoint sites, etc.

### Assignment Types

- Assigned
    - classic groups
    - manually add members
- Dynamic user
    - automatically include users based on rules
    - requires Azure AD Premium P2 license
- Dynamic device
    - automatically include devices based on rules
    - only for Security group type
    - requires Azure AD Premium P2 license

## Managing Devices in Azure AD

### Features

- device identity and access management
    - give a device an identity and control its access to corporate resources
- bring-your-own-device (BYOD) support
    - users can bring their own device, and it can get an identity to access corporate resources
- device authentication and resource access
    - ensures secure authentication and authorization is used to access corporate resources
- mobile device management (MDM) integration
    - centralized device management and policy enforcement
    - e.g. Microsoft Intune
- enhanced security and access control
    - manage device compliance
    - e.g. restrict access to corporate resources to only devices running a particular OS version

### Why join devices?

- single sign-on (SSO)
    - convenient access to applications secured by Azure AD
    - users can sign in once and access multiple applications seamlessly
- user setting roaming
    - enterprise policy-compliant roaming of user settings across devices
    - consistent user experience and personalized settings on different devices
- Windows Store for Business
    - access Windows Store for Business using corporate credentials
    - simplified app management and deployment for organisations
    - private store, only for your organisation
- Windows Hello for Business
    - passwordless authentication
    - use face recognition or finger print to sign into corporate devices
- policy-compliant access
    - make sure access is always aligned to corporate policies
    - protects sensitive data by allowing access only to approved devices
    - e.g. only allow access to devices running iOS and version 17.1+

## Azure AD Administrative Units

- giving Authentication Administrator role to a user allows them to manage the entire tenant
- Administrative Units provides the ability to give more granular permissions by only allowing them to manage a specific group of users

### Available Roles

- Authentication Administrator
    - view, set, and reset authentication method info for non-admin users in the assigned administrative unit only
- Groups Administrator
    - manage groups and group settings in the assigned administrative unit only
        - e.g. naming, expiration policies, etc.
- Helpdesk Administrator
    - reset passwords for non-admin users and helpdesk admins in the assigned administrative unit only
- License Administrator
    - assign, remove, update license assignments in the assigned administrative unit only
- Password Administrator
    - reset passwords of non-admin users in the assigned administrative unit only
- User Administrator
    - manage users, groups, and resetting passwords for limited admin users (Helpdesk Administrator, License Administrator, Password Administrator, etc.) in the assigned administrative unit only

## Passwordless Authentication

- modern approach to authentication
- eliminates need of passwords
- enhanced security and user convenience
- something you are/know
    - e.g. biometric, PIN code, etc.
- something you have
    - e.g. Windows 10 device, phone, security key, etc.

### Windows Hello for Business

- for employees with designated Windows PC's
- biometric and PIN credentials tied to employee's PC
- PKI integration and built-in support for SSO
- convenient access to corporate resources (on-prem and cloud)

### Microsoft Authenticator

- turn employee's phone into passwordless authentication method
- can be used for multi-factor and passwordless authentication
- convenient and secure using phone as trusted device

### FIDO2 Security Keys

- open standard for passwordless authentication
- non-phishable and standards-based
- USB, Bluetooth, or NFC
- increased security with hardware-based authentication
- sign in to Azure AD, hybrid Azure AD joined Windows 10 devices, and supported browsers
- suitable for security-sensitive enterprises or scenarios without phone-based authentication

# Hybrid Identity

# Identity Protection

# Azure AD Privileged Identity Maangement (PIM)

## Zero-Trust Model

- trust may never be assumed
- demands verification before granting access
- assumes breach
- verify each request as if it originates from an open network

## Azure AD PIM

- service that enables orgs to manage, control, and monitor access to corporate resources
- minimize number of users with access to secure data

### How Does PIM Work?

- provides just-in-time (JIT) privileged access to users
    - reducing risk of unauthorized / accidental access
- offers time- and approval-based role activation to manage access permissions on critical resources

### Why Use PIM?

- risk management
    - reduces risks involved with excessive permanent user access permissions to sensitive corporate data
    - employees are only granted access to what they actually need
    - lowering chances of internal and external security threats
- compliance and governance
    - aligns with company compliance needs
    - maintaining compliance stance in increased regulated environment
        - e.g. GDPR
- cost-effective
    - automating & centralizing access control cuts down costs and time

## Configure PIM Scope

### Scopes

- Azure AD Roles
    - assigns users to a role
    - focuses on safeguarding Azure AD roles
    - e.g. user can request Global Administrator access when necessary, subject to approval
- Azure Resources
    - determines key groups and subscriptions
    - essential resources
- PIM for Groups
    - e.g. when a user needs to be Owner of a certain Azure AD Group

### When To Apply PIM?

- wherever you feel like users are overprivileged 

## Implement PIM Onboarding

### License Requirements

- Azure AD Premium P2
- or Enterprise Mobility + Security (EMS) E5
- or Microsoft 365 M5 license

### Administrative Access

- grants access to other administrators to manage PIM
- first PIM user is assigned Secuzity Administrator and Privileged Role Administrator roles
- Global Administrator Role is responsible for assigning others to Privileged Administrator Role

### PIM Activation And Effects

- PIM is automatically enabled for the tenant when a privileged role user with a Premium P2 license accesses "Roles and administrators" in Azure AD
- PIM activation introduces additional assignment options
    - active vs eligible
    - start and end times
    - enables defining scope for role assignments using Administrative Units and custom roles
- PIM activation has minimal impact on org's workflow
    - may trigger additional notifications
        - e.g. PIM weekly digest

## Explore PIM Configuration Settings

### Activation

- set maximum duration in hours
- require MFA yes/no
- require justification on activation yes/no
- require ticket information on activation yes/no
- require approval to activate yes/no
- select approver(s)

### Assignment

- allow permanent eligible assignment yes/no
    - set expiration time on eligible assignment if not permanent
- allow permanent active assignment yes/no
    - set expiration time on active assignment if not permanent
- require MFA yes/no
- require justification on active assignment yes/no

### Notifications

- send notifications when members are assigned as eligible to a role
- send notifications when members are assigned as active to a role
- send notifications when eligible members activate a role

## Implement PIM Workflow

1. Plan (*PIM Administrator*)
    - Determine users and roles that will be managed by PIM
2. Assign (*PIM Administrator*)
    - Assign users or current admins for specific Azure AD roles, so they have access only when necessary
3. Activate (*PIM User*)
    - Activate your eligible admin roles so they can get limited access to the privileged identity
4. Approve (*PIM Approver*)
    - View and approve all activation requests for specific Azure AD roles that you are configured to approve
5. Audit (*PIM Administrator*)
    - View and export a history of all privileged identity assignments and activations so you can identify attacks and stay compliant

# Enterprise Governance

## Shared Responsibility Model

- both you and the Cloud Service Provider (CSP), Microsoft in this case, are responsible for security
    - information & data, devices, accounts & identities
        - always managed by the customer
    - physical hosts, physical network, physical datacenter
        - responsibility handed over to CSP when moved to the cloud
    - rest
        - depends on SaaS/PaaS/IaaS

| Responsibility                        | SaaS   | PaaS   | IaaS | On-premises |
|---------------------------------------|--------|--------|------|-------------|
| Information & data                  | YOU    | YOU    | YOU  | YOU         |
| Devices                               | YOU    | YOU    | YOU  | YOU         |
| Accounts & identities               | YOU    | YOU    | YOU  | YOU         |
| Identity & directory infrastructure | SHARED | SHARED | YOU  | YOU         |
| Applications                          | MS     | SHARED | YOU  | YOU         |
| Network controls                      | MS     | SHARED | YOU  | YOU         |
| OS                                    | MS     | MS     | YOU  | YOU         |
| Physical hosts                        | MS     | MS     | MS   | YOU         |
| Physical network                      | MS     | MS     | MS   | YOU         |
| Physical datacenter                   | MS     | MS     | MS   | YOU         |

## Review Azure Hierarchy Of Systems

- very useful to apply policies, cost management, RBAC, etc.

### Overview

- Management Group
    - scope above subscriptions
    - to group subscriptions together
    - root Management Group is created by default
    - up to 6 levels of nested groups possible
        - excluding the root group
- Subscription
    - contains one or more resource groups
- Resource Group
    - logically grouping resources like VM's, databases, etc.

## Enable Azure RBAC

- always apply principle of least privilege

### Overview

- who?
    - identity
        - user
        - group
        - service principal
        - managed identity
- what?
    - role definition
        - set of operations a particular role can perform
        - written in JSON
        - can use pre-defined or define custom roles
- where?
    - scope
        - defines boundary (management group, subscription, resource group, resource)
- assignment
    - role + identity + scope = role assignment
        - max 2000 per subscription

### Built-in Roles

- Owner
    - full access to all resources
    - can delegate access to other users
- Contributor
    - create and manage all type of resources
    - cannot grant access to others
- Reader
    - read access to all resources
    - no permission to make changes to resources
- User Access Administrator
    - give access to other users
    - like the Owner role but without the permission to manage resources

## Configure Azure Policies

- policies can be used to define org standards
- identify non-compliant resources

### Overview

- definition
    - define policy and effect
    - can use pre-defined or define custom policies
    - JSON format
- scope
    - defines boundary (management group, subscription, resource group, resource)
- assignment
    - policy + scope = policy assignment
- compliance
    - evaluate compliance after assigning policy
    - compliant and non-compliant resources
        - resources created before policy was assigned can be non-compliant

### Use Cases

- require tags
    - enforce tags that need to be added to resources
- inherit tags
    - tags applied to subscription or resource group are not inherited to the resources by default
- allowed locations
    - define set of cloud locations where resources can be deployed
- allowed SKU's
    - define SKU's that can be used
- allowed resource types
    - define which resources can be created

### Initiatives

- multiple policies grouped together
- can be assigned as a single unit

## Enable Resource Locks



## Deploy Azure Blueprints



## Design Azure Subscription Management Plan



# Perimeter Security

# Network Security

# Host Security

# Container Security

# Key Vault

# App Security

# Storage Security

# Database Security

# Azure Monitor

# Microsoft Defender

# Microsoft Sentinel

