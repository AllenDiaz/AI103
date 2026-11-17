Bicep for Azure AI Foundry
Course overview

This unit is dedicated to Bicep, a tool for implementing infrastructure as code in Azure. The course covers Bicep fundamentals, deploying Azure AI Foundry resources, working with templates, troubleshooting deployment issues, and improving Bicep scripts for maintainability and flexibility.

Key topics include:

Provisioning Azure AI Foundry resources using Bicep
Reading, customizing, and deploying Bicep templates
Understanding parameters, resources, and outputs
Deploying templates with Azure CLI and Bicep commands
Comparing standard, debug, latest-model, and improved Bicep scripts
Troubleshooting deployment and deletion issues
Parameterizing and organizing templates
Prerequisites
An Azure subscription with permissions to create resources
Owner permissions on the subscription, or equivalent access rights
Azure CLI installed locally
Signed in through the terminal using:
az login

Optional: Bicep CLI installation
Introduction to Bicep
Transcript

Bicep is a powerful tool for the cloud that enables infrastructure as code. It provides many advantages but can be difficult to set up and troubleshoot. Because infrastructure deployment is foundational to cloud projects, understanding Bicep is important for successfully deploying resources.

This unit introduces Bicep for provisioning Azure resources with AI Foundry. Learners will:

Read, customize, and deploy Bicep templates.
Create AI Foundry accounts, projects, and model deployments.
Learn parameters, resources, and outputs.
Understand how AI Foundry and Cognitive Services resources are modeled in Bicep.
Deploy templates using Azure CLI and Bicep commands.
Compare standard, debug, latest-model, and improved Bicep files.
Learn practical improvements such as parameterization and template organization.
Key concepts
Infrastructure as code
Azure AI Foundry deployment
Bicep templates
Azure CLI
Parameters, resources, outputs
Deploying AI Foundry Without Bicep
Manual Portal Deployment
Transcript

The instructor demonstrates creating an AI Foundry resource directly through the Azure portal.

Steps shown:

Search for Microsoft Foundry in Azure.
Select Create Resource.
Choose a subscription.
Create or select a resource group:
AI Agent CourseRG
Provide a Foundry resource name.
Select a globally unique subdomain name.
Choose a region:
Australia East
Accept default project creation:
Project Default
Skip optional configuration such as:
Key Vault
Networking
Encryption
Tags
Review and create the resource.

The deployment automatically creates:

The resource group
The AI Foundry account
A default project

The instructor notes that while deployment through the portal is straightforward, repeating the process frequently becomes inefficient. Infrastructure as code provides a more repeatable and automated alternative.

Key concepts
Resource groups
AI Foundry accounts
Projects
Azure portal deployment
Globally unique subdomain names
Accessing AI Foundry and Deploying a Model
Transcript

After deployment completes:

Open the Foundry portal.
Access the default project.
Review:
API key
Endpoint information

To deploy a model:

Navigate to: Build → Deployments
Select: Deploy a base model
Review available models.

The instructor searches for:

GPT-5
GPT-5 Mini
GPT-5 Nano
GPT-5.1
GPT-5.4 Mini

The example uses:

GPT-5 Mini

Important deployment details noted:

Publisher (e.g., Azure OpenAI/OpenAI)
Model version number
Model deployment name

The model is deployed using default settings and tested successfully in the playground.

Key concepts
Model deployment
Model catalog
GPT-5 Mini
Azure OpenAI models
Playground testing
Examples
Deploying GPT-5 Mini through the portal
Verifying deployment using the playground
Deleting Resources
Transcript

To remove all deployed resources:

Open the resource group.
Select: Delete Resource Group
Enter the resource group name:
AI Agent CourseRG
Confirm deletion.

Deleting a resource group removes all contained resources.

Key concepts
Resource group deletion
Infrastructure lifecycle management
Understanding the Standard Bicep Script
Microsoft Sample Template
Transcript

The instructor uses a Microsoft-provided sample Bicep template for AI Foundry deployment.

Reasons for starting with Microsoft's template:

Known to work
Uses supported resource versions
Provides a stable deployment foundation
Reduces errors compared to generating templates from scratch

The sample template creates:

An AI Foundry account
A project
A model deployment

The sample deploys:

GPT-4.1 Mini

The instructor notes that GPT-4.1 Mini is an older model and may be deprecated, but it remains useful because it is stable and widely available, including in many trial environments.

Key concepts
Microsoft reference templates
Stable API versions
GPT-4.1 Mini deployment
Template Customization
Transcript

The instructor adds a new parameter:

coursePrefix


Default value:

my course


The prefix is applied to resource names throughout the deployment.

The script contains:

Parameters

Configuration values supplied before deployment.

Resources

The deployment defines:

AI Foundry account
Project
Model deployment

The AI Foundry account uses:

API version: 2025-06-01
SKU: S0 Standard

Deployment order is determined by resource relationships and parent-child configuration.

The model deployment specifies:

Model name
Publisher
Version
Outputs

Outputs expose useful deployment values such as endpoints and deployment names for later use.

Key concepts
Parameters
Resources
Outputs
Parent-child resource hierarchy
Resource sequencing
Deploying Bicep with Azure CLI
Creating the Resource Group
Transcript

Before deploying Bicep resources:

Log into Azure CLI.
Create the resource group.

The instructor verifies creation in the Azure Portal and confirms that the resource group exists but contains no resources.

Key concepts
Azure CLI authentication
Resource group creation
Bicep and ARM Templates
Transcript

Bicep does not directly deploy resources.

Instead:

Bicep is compiled into an ARM template.
Azure deploys the ARM template.

The instructor explains:

ARM templates are the original deployment mechanism.
ARM templates have more complex syntax.
Bicep simplifies ARM template authoring.

Example command:

az bicep build


This generates a JSON ARM template.

The generated template contains:

Metadata
Parameters
Resources
Outputs

The instructor demonstrates that the generated ARM template contains:

Cognitive Services Foundry account
Foundry project
GPT-4.1 Mini deployment
Outputs
Key concepts
ARM templates
Bicep compilation
JSON deployment templates
Deploying Resources
Transcript

Resources are deployed using:

az deployment group create


The deployment command:

References the Bicep file.
Supplies parameter overrides.

Prior to deployment Azure validates the file.

Common Error: Duplicate Subdomain

The instructor intentionally triggers an error caused by a non-unique subdomain name.

Error cause:

Foundry subdomain already in use.

Solution:

Change the resource prefix to a unique value.

Example:

123


After changing the prefix, deployment starts successfully.

Key concepts
Deployment validation
Unique subdomain requirements
Parameter overrides
Examples
Duplicate subdomain deployment failure
Successful deployment after changing prefix
Reviewing Outputs
Transcript

After a successful deployment:

Outputs display values such as:

Deployment name
Endpoint

These values can also be viewed through the Foundry portal.

The instructor verifies:

Foundry account
Project
Endpoint
API key
Model deployment
Key concepts
Deployment outputs
Endpoint retrieval
Resource verification
Resource Deletion and Soft Delete Behavior
Cognitive Services Soft Delete
Transcript

Deleting the resource group does not fully remove Cognitive Services resources.

The instructor demonstrates:

Delete the resource group.
Verify deletion.
Query Cognitive Services resources.

Previously deployed resources still appear because they are soft deleted.

Each resource must be permanently removed individually.

The instructor notes:

Soft-deleted resources typically do not incur cost.
They can prevent reuse of names and prefixes.
They may cause deployment failures if left undeleted.
Key concepts
Soft delete
Cognitive Services cleanup
Resource name conflicts
Action items
Verify soft-deleted resources after deleting a resource group.
Permanently remove soft-deleted Foundry resources when necessary.
Debugging Bicep Deployments
Debug Script Strategy
Transcript

The instructor introduces a simplified debugging Bicep script.

Purpose:

Isolate deployment issues.
Reduce complexity during troubleshooting.

Approach:

Deploy only one resource.
Verify deployment.
Delete everything completely.
Add the next resource.
Repeat.

Suggested progression:

Foundry account
Project
Model deployment
Outputs

The instructor states that outputs themselves can sometimes cause deployment issues.

Key concepts
Isolation-based debugging
Incremental deployment
Verification after each step
Action items
Deploy resources individually when debugging.
Confirm successful deployment before adding dependencies.
Verify complete cleanup between tests.
Bicep Troubleshooting Advice
Transcript

The instructor emphasizes:

Expect deployment issues.
Be patient.
Avoid deploying many resources simultaneously while troubleshooting.

Recommendations:

Create dedicated debug templates.
Test resources independently.
Use Microsoft templates as the starting point.
Modify working templates incrementally.

The instructor notes that AI tools can generate incorrect Bicep files and recommends validating changes against Microsoft-provided examples.

Key concepts
Incremental troubleshooting
Template validation
Reliable deployment practices
Deploying the Latest Model
Updating the Model Deployment
Transcript

The instructor manually deploys Foundry through the portal to obtain model information needed for a newer deployment.

Selected model:

GPT-5 Mini

Information recorded:

Model name
Version number
Provider

Observed version:

2025-08-07


The Bicep script is updated to use:

GPT-5 Mini
Version: 2025-08-07

The deployment succeeds after:

Creating the resource group
Running the deployment
Waiting for completion
Key concepts
Model version tracking
Updating Bicep deployments
GPT-5 Mini deployment
Improving the Bicep Script
Improved Template Structure
Transcript

The instructor introduces an improved Bicep template featuring:

Additional comments
Better organization
More parameters

Parameter categories include:

General Parameters

Shared deployment settings.

Account Parameters

Foundry-specific settings.

Model Deployment Parameters

Model-specific configuration such as:

Model name
Model version

Tags are also parameterized.

Example tag:

development

Key concepts
Parameterization
Template organization
Resource tagging
Updating API Versions
Transcript

The improved script updates Cognitive Services API versions.

The instructor references current API versions and uses:

20260501


for Cognitive Services resources.

Updated versions are applied to:

Foundry account
Project
Model deployment
Key concepts
API version management
Cognitive Services updates
Deploying the Improved Script
Transcript

Initial deployment fails because soft-deleted resources were not removed.

The instructor:

Deletes the resource group.
Deletes the soft-deleted Foundry resources.
Recreates the resource group.
Redeploys the template.

The deployment succeeds.

Verification includes:

Opening the Foundry portal
Confirming project creation
Confirming API endpoint availability
Confirming model deployment
Testing GPT-5 Mini in the playground
Key concepts
Deployment recovery
Soft-delete remediation
Validation testing
Examples
Soft-delete deployment failure
Successful redeployment after cleanup
Final Recap
Transcript

Key takeaways:

Bicep can be difficult to work with initially.
Deployment issues are normal.
Soft-deleted resources can create unexpected problems.
Debugging should be done incrementally.
Microsoft templates provide reliable starting points.
Parameterized templates are easier to maintain.
Newer API versions and model versions can be adopted, but stable versions should be kept available as fallback options.

The instructor encourages learners to:

Experiment with newer Cognitive Services API versions.
Test newer model versions.
Use stable configurations when newer versions fail.

The unit concludes by emphasizing that Bicep deployment skills are foundational for working effectively with Azure resources.

Key concepts
Infrastructure as code
AI Foundry deployment
Bicep debugging
Incremental deployment
Resource lifecycle management
Template maintenance
API version management
Action items
Install and configure Azure CLI.
Use Microsoft sample templates as a starting point.
Parameterize deployments where possible.
Deploy resources incrementally when troubleshooting.
Check for soft-deleted Cognitive Services resources after deletion.
Validate deployments through the Foundry portal and playground.
Keep known-good stable versions available when experimenting with newer APIs or models.