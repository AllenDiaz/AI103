Intra-Agent ID
Course Overview

This unit introduces Intra-Agent ID and explains why agents require their own identities rather than operating under a developer's identity. The lesson covers service principals, agent registration, authentication flows, managed identities, access control, conditional access, sponsorship, auditing, and secure identity management practices for Azure AI agents.

Learning Objectives

By the end of this unit, you will understand:

Why agents need their own identities.
How service principals are used for agent identities.
How to register agents using Entra Agent ID.
The purpose of client IDs, secrets, and certificates.
The client credentials authentication flow.
How DefaultAzureCredential simplifies authentication across environments.
How managed identities work.
How identity blueprints and permission assignments function.
How conditional access policies can secure agents.
How access packages provide time-limited access.
The sponsor model and agent accountability.
How agent auditing and monitoring work.
Best practices for secure identity management.
Prerequisites
Understanding of Azure AI Foundry hubs and projects.
Basic familiarity with Azure identity concepts.
Agent Identity Fundamentals
Why Agents Need Their Own Identity
Transcript

Without an identity, an agent operates using the identity of the developer who wrote the code.

This becomes problematic when multiple agents are created by the same developer because:

Agent actions become indistinguishable from developer actions.
Auditing becomes difficult.
Permission management becomes more complex.
Accountability is reduced.

Providing each agent with its own identity enables:

Independent auditing
Independent permissions
Individual accountability
Improved security management
Key Concepts
Agent identity
Accountability
Auditing
Permission isolation
Service Principals
What Is a Service Principal?
Transcript

A service principal is the Azure identity that represents an application or agent rather than a human user.

It can be thought of as a digital driver's license for software that proves:

The agent exists
The agent has permission to act

A human user typically authenticates using:

Username
Password

A service principal authenticates using:

Client ID
Secret or certificate
Key Concepts
Service principal
Human identity
Agent identity
Authentication
Best Practices
Assign each agent its own service principal.
Avoid sharing identities between agents.
Registering Agent Identities
Entra Agent ID Registration
Transcript

Agent registration is performed through Entra Agent ID.

When an agent is registered:

The agent is registered through Foundry project settings.
Entra Agent ID is enabled.
Foundry automatically creates a service principal in the Entra tenant.
A client ID is generated.
A secret is generated or a certificate is uploaded for authentication.

This establishes:

One agent
One identity

No two agents should share the same identity.

Procedures
Open Foundry project settings.
Enable Entra Agent ID.
Allow Foundry to create the service principal.
Obtain the client ID.
Generate a secret or upload a certificate.
Use the credentials for agent authentication.
Key Concepts
Entra Agent ID
Registration
Service principal creation
Agent authentication
Best Practices
Use one service principal per agent.
Maintain separate identities for auditing and permissions.
Client IDs, Secrets, and Certificates
Authentication Credentials
Transcript

A client ID is a public identifier for the agent.

Example:

A1B2C3D4


The client ID:

Identifies the requesting agent
Is not a secret
Can appear in logs

A secret acts similarly to a password.

Example:

8GH4...


The secret:

Proves the agent's identity
Is submitted during authentication
Can expire

Certificates provide an alternative authentication mechanism.

Certificates:

Contain cryptographic keys
Are generally more secure than secrets
Cannot be accidentally copied as plain text
Key Concepts
Client ID
Secret
Certificate
Authentication credentials
Best Practices
Prefer certificates when appropriate.
Protect secrets carefully.
Rotate credentials when required.
Authentication Flow
Client Credentials Flow
Transcript

The primary authentication method for agents is the client credentials flow.

The flow operates as follows:

The agent sends its client ID.
The agent sends its secret or certificate.
Entra ID verifies the credentials.
An access token is returned.
Procedures
Agent submits client ID.
Agent submits secret or certificate.
Entra ID verifies authenticity.
Entra ID issues an access token.
Agent uses the token when calling APIs.
Key Concepts
Client credentials flow
Authentication
Access token
Access Tokens
Transcript

An access token is a time-limited digital pass proving successful authentication.

Characteristics:

Typically expires after approximately one hour.
Is included in the authorization header of API requests.
Is often handled automatically by SDKs.
Key Concepts
Access token
Authorization
Token expiration
DefaultAzureCredential
Multi-Environment Authentication
Transcript

DefaultAzureCredential automatically tries multiple authentication methods until one succeeds.

Authentication attempts may include:

Environment variables
Managed identities
Visual Studio login
Other supported authentication mechanisms

This allows the same application code to function across environments.

Examples include:

Local development on a laptop
Testing environments
Production deployments
Examples

Local development:

Visual Studio Login


Production deployment:

Managed Identity

Key Concepts
DefaultAzureCredential
Environment-specific authentication
Credential abstraction
Best Practices
Use DefaultAzureCredential to avoid modifying authentication code across environments.
Managed Identities
What Is a Managed Identity?
Transcript

A managed identity is an Azure feature that automatically creates and rotates credentials without requiring secrets to be stored.

When enabled:

Azure creates a service principal.
Azure manages credentials automatically.
Credentials rotate automatically.
Secrets do not need to be stored manually.

The agent never directly sees the credential.

Azure injects authentication into the runtime environment.

Procedures
Open Foundry agent service settings.
Enable system-assigned managed identity.
Allow Azure to manage credentials automatically.
Key Concepts
Managed identity
Automatic credential rotation
Secretless authentication
Best Practices
Prefer managed identities whenever possible.
Avoid hard-coded credentials.
Agent Identity Blueprints
Reusable Permission Templates
Transcript

An agent identity blueprint is a reusable template that defines access permissions for Azure resources.

Examples include permissions for:

Storage accounts
Databases
APIs

A blueprint may contain role assignments such as:

Storage Blob Data Reader
Cosmos DB Reader

Once assigned to a service principal, the agent immediately receives all permissions defined within the blueprint.

Blueprints can also be version controlled.

Key Concepts
Identity blueprint
Role assignments
Permission templates
Versioning
Best Practices
Version-control blueprints.
Reuse standardized permission configurations.
Assigning Permissions
Role-Based Access Control (RBAC)
Transcript

Permissions are granted by assigning Azure roles to an agent's service principal.

A common approach is Role-Based Access Control (RBAC).

Examples include:

Reader
Contributor

A role assignment can be created by:

Opening the resource.
Navigating to Access Control.
Selecting Add Role Assignment.
Choosing the role.
Selecting the agent's service principal.
Completing the assignment.
Procedures
Open the Azure resource.
Navigate to Access Control (IAM).
Select Add Role Assignment.
Choose an Azure role.
Select the target service principal.
Save the assignment.
Key Concepts
RBAC
Reader role
Contributor role
Service principal permissions
Best Practices
Principle of Least Privilege

Only grant the permissions the agent requires.

Example:

Allow reading customer names.
Do not allow deletion unless necessary.
Conditional Access for Agents
Security Policies
Transcript

Conditional access policies add security requirements that must be satisfied before an agent can act.

Examples include:

Network Restrictions

An agent may only access customer information from approved corporate IP addresses.

Time-Based Restrictions

An agent may only process refund requests between specific hours.

Example:

9 AM to 5 PM

Risk-Based Restrictions

Entra ID can identify unusual behavior such as:

Rapid deletion requests
Suspicious activity

Actions may then be blocked pending sponsor approval.

Key Concepts
Conditional access
Network restrictions
Time-based controls
Risk-based controls
Best Practices
Apply multiple layers of access restrictions.
Restrict sensitive operations whenever possible.
Access Packages
Temporary Permission Assignments
Transcript

An access package is a bundle of permissions granted for a limited duration.

Example package:

Sensitive Data Access


Permissions might include:

Read access to HR databases
Write access to logs

Workflow:

Developer requests access for an agent.
Manager approves.
Agent receives permissions.
Access expires automatically.

Example:

24-hour access

Key Concepts
Access package
Temporary permissions
Automatic revocation
Benefits
Reduces long-term permissions.
Eliminates manual cleanup.
Sponsors and Accountability
Sponsor Responsibilities
Transcript

A sponsor is the human accountable for an agent's actions.

During registration:

Every agent must have a sponsor.
Sponsorship cannot be empty.
Registration fails without a sponsor.

Sponsor responsibilities include:

Reviewing weekly logs
Approving access requests
Responding to security events

Agents may have:

Primary sponsors
Backup sponsors
Key Concepts
Sponsor
Accountability
Governance
Sponsor Departure Process
Transcript

If a sponsor leaves the organization:

The agent is automatically suspended.
Suspension typically occurs within approximately 24 hours.
Requests are rejected until reassignment occurs.

Example error:

Agent has been suspended.
No sponsor assigned.


The agent can resume operation once a new sponsor is assigned through the Foundry management portal.

Key Concepts
Sponsor lifecycle
Automatic suspension
Agent reassignment
Auditing and Monitoring
Agent Activity Auditing
Transcript

Entra Agent ID enables detailed auditing of agent activity.

Actions recorded may include:

API calls
Data access
Permission changes

Audit logs may contain:

Client ID
Action performed
Timestamp
Source IP address

Agent logs remain separate from user logs.

When a user invokes an agent:

A user activity record is created.
An agent activity record is created.

This creates separation between user intent and agent execution.

Key Concepts
Audit logs
User activity
Agent activity
Traceability
Azure Monitor Integration
Transcript

Audit information can be sent to Azure Monitor.

Azure Monitor allows:

Log analysis
Investigation of incidents
Filtering by client ID

This allows investigation of everything a specific agent has performed.

Key Concepts
Azure Monitor
Log analytics
Incident investigation
Coding Pattern Recommendations
Authentication Strategy
Transcript

DefaultAzureCredential can be used across all major deployment scenarios.

It works with:

Local development
Managed identities
Secrets
Production environments

It automatically selects the appropriate authentication mechanism.

Best Practices
Use DefaultAzureCredential wherever possible.
Avoid hard-coding authentication logic for different environments.
Final Recap

This unit covered the fundamentals of Intra-Agent ID and secure identity management for Azure agents.

Key takeaways include:

Every agent should have its own service principal.
Every agent should have its own permissions.
Every agent should have a human sponsor.
Client credentials flow is the primary authentication method for agents.
Managed identities provide the most secure authentication approach.
DefaultAzureCredential simplifies authentication across environments.
Identity blueprints help standardize permissions.
Conditional access policies add additional security layers.
Access packages provide temporary permission assignments.
Auditing enables visibility into every agent action.
Agents become suspended when sponsors leave until reassignment occurs.
Secrets, API keys, and credentials should never be hard coded.
Azure Key Vault and managed identities should be used for secure credential management.

