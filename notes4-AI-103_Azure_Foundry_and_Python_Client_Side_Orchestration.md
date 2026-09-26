# AI-103 Study Notes: Azure Foundry and Python Client-Side Orchestration

> **Lesson focus:** Provision Azure Foundry/OpenAI resources with Infrastructure as Code (Bicep), then use Python to directly call a deployed large language model (LLM) through **client-side orchestration**.

## 1. Core Learning Objectives

This walkthrough demonstrates an end-to-end, code-first workflow:

1. Set up a Python development environment in VS Code.
2. Create and activate an isolated Python virtual environment.
3. Install Python dependencies.
4. Install and authenticate with Azure CLI.
5. Install or upgrade Bicep.
6. Create an Azure resource group.
7. Deploy Foundry resources and an LLM deployment through Bicep.
8. Retrieve Bicep deployment outputs, including the OpenAI endpoint and model deployment name.
9. Connect Python to the Azure-hosted model using Azure identity authentication.
10. Maintain conversation history locally and send it to the model.
11. Extract the assistant response from the SDK response object.
12. Clean up Azure resources, including handling soft-deleted Foundry resources.

---

## 2. Development Environment: VS Code and Python

The walkthrough uses **Visual Studio Code on Windows**. Commands and environment activation details may differ on macOS or Linux.

### Open the VS Code terminal

If a terminal is not already visible:

- Select **Terminal** → **New Terminal** in VS Code.

### Use a virtual environment

A Python virtual environment isolates packages for a particular project or lesson.

**Why use one?**

- Different projects can require different package versions.
- It helps avoid package and path conflicts.
- It keeps the local Python environment cleaner.
- The walkthrough recommends creating a separate environment for each unit and optionally deleting it after the unit to save disk space.

After creating and activating an environment, the terminal prompt indicates the active environment—for example, the walkthrough showed the environment name in green.

### Important VS Code behavior

After restarting VS Code, the intended virtual environment might no longer be selected. If this happens:

- Reactivate the virtual environment.
- Ensure dependencies are installed into the active environment, rather than the base Python environment.
- Configure VS Code to run the correct virtual-environment `Python.exe` when debugging.

---

## 3. Python Dependencies

The lesson’s `requirements.txt` contains three packages:

```text
PyYAML
azure-identity
openai
```

| Package | Purpose in this lesson |
|---|---|
| `PyYAML` | Loads a YAML configuration file, including the system prompt. |
| `azure-identity` | Provides Azure authentication support, including `DefaultAzureCredential`. |
| `openai` | Provides the OpenAI SDK used to call the Azure-hosted model endpoint. |

Install dependencies:

```bash
pip install -r requirements.txt
```

Using `requirements.txt` is preferable to manually installing each dependency because it makes the environment repeatable.

---

## 4. Azure CLI and Authentication

### Azure CLI Purpose

Azure CLI enables local command-line interaction with Azure. In this workflow, it is used to:

- Authenticate to Azure.
- Create a resource group.
- Install or upgrade Bicep.
- Deploy Bicep templates.
- Delete resources.

After installing Azure CLI, close and reopen VS Code if the CLI is not recognized in the integrated terminal.

### Sign In

```bash
az login
```

Sign in with the Azure account associated with the target subscription.

### Subscription and Billing Note

An Azure free trial may work initially, but some scenarios may require a pay-as-you-go subscription. If subscription-related errors occur, verify the account, subscription, and billing configuration.

---

## 5. Bicep and Infrastructure as Code

**Bicep** is Azure’s Infrastructure as Code (IaC) language. It lets you define and deploy Azure resources in repeatable source files instead of configuring everything manually in the portal.

Bicep compiles to and deploys through an **Azure Resource Manager (ARM) template**.

### Benefits of IaC

- Repeatable deployments.
- Consistent configuration.
- Less manual portal work.
- Easier recreation and cleanup of environments.
- Better alignment with production cloud-engineering practices.

### Install or Upgrade Bicep

```bash
az bicep install
az bicep upgrade
```

---

## 6. Create an Azure Resource Group

A resource group is the logical Azure container for related resources in the lesson.

The walkthrough creates it through Azure CLI:

```bash
az group create
```

Important inputs include:

- A resource group name.
- An Azure region/location that supports the needed services.

A successful command returns JSON indicating successful provisioning.

> **Study point:** Azure resources can be created and managed entirely by code and command-line tooling; the Azure portal can be used for verification.

---

## 7. Deploy Azure Foundry Resources with Bicep

The Bicep deployment provisions:

- A **Foundry resource**.
- A **Foundry project**.
- A deployed **large language model**.

The deployment is executed at resource-group scope using:

```bash
az deployment group create
```

The deployment command identifies:

- The target resource group.
- The Bicep template file.
- Parameters such as a course or resource prefix.

### Unique Naming

Azure resource names may need to be globally unique. Customize the provided prefix or number.

If a naming collision occurs:

- Use a different prefix/suffix.
- Use a longer, more unique string.

### Deployment States

- **Accepted** — Azure has validated/scanned the deployment request.
- **Running** — Resources are being provisioned.
- **Succeeded** — Provisioning completed successfully.

---

## 8. Deployment Outputs

After deployment, retrieve the Bicep output values.

The lesson identifies two especially important outputs:

1. **OpenAI endpoint**.
2. **LLM deployment name**.

These values are required by the Python application.

### Endpoint Versus Deployment Name

Do not confuse:

- The underlying base model identity, such as `gpt5-mini`, with
- The **deployment name** assigned in Azure.

Application code calls the Azure **deployment name**, not merely the base model name.

### Fallback If Bicep Deployment Fails

If an IaC deployment failure blocks progress, the lesson suggests manually deploying the resources through the portal/lab process and using the resulting OpenAI endpoint in Python configuration. This allows the client-side portion to continue while resolving IaC problems separately.

---

## 9. VS Code Debug Configuration and Environment Variables

The lesson uses a VS Code `launch.json` configuration to run the Python script in the debugger.

The configuration specifies:

- The Python script to execute.
- The virtual-environment Python interpreter.
- Environment variables.

Environment variables include values obtained from Bicep outputs, especially:

- The Azure OpenAI/Foundry endpoint.
- The model deployment name.

> **Key idea:** IaC deployment outputs can feed application configuration, avoiding unnecessary hardcoding of infrastructure details in source code.

---

## 10. Client-Side vs. Server-Side Orchestration

### Client-Side Orchestration

This lesson introduces **client-side orchestration**.

Characteristics:

- The Python application calls the deployed LLM directly.
- The developer manages interaction state, including conversation history.
- No hosted Foundry agent is used in the basic example.
- More orchestration logic resides in application code.

Client-side orchestration is more manual and flexible, but requires more developer work.

### Server-Side Orchestration

In the lesson’s comparison:

- Server-side orchestration typically uses agents hosted in Foundry.
- The service handles more orchestration and state-related work.
- It usually needs less application code.
- It is described as generally more secure and increasingly common.

| Topic | Client-side orchestration | Server-side orchestration |
|---|---|---|
| LLM interaction | Application calls the model directly. | Application interacts through a hosted agent/service layer. |
| Conversation history | Managed by application code. | More can be managed by the agent/service. |
| Developer responsibility | Higher. | Lower for common orchestration scenarios. |
| Flexibility | Useful for custom behavior or limitations. | Useful for managed orchestration and reduced implementation burden. |
| This lesson | Direct model call; no agent deployed. | Discussed conceptually; covered later. |

---

## 11. Configuration File and System Prompt

The application loads a configuration file, described as YAML or JSON. The walkthrough uses YAML and stores the **system prompt** in it.

The system prompt can define:

- The model’s role.
- Expected response behavior.
- A maximum response length.

Example concept:

```yaml
system_prompt: |
  You are a large language model.
  Respond to user queries based on the information provided.
  Make sure your maximum output is 100 words.
```

Externalizing configuration separates adjustable behavior, such as prompts, from application logic.

---

## 12. Authentication: `DefaultAzureCredential` and Bearer Tokens

### No API Key in This Walkthrough

The lesson does **not** use an API key. Instead, it uses Azure identity-based authentication, such as:

- A system-assigned managed identity, or
- A service principal behind the scenes.

### `DefaultAzureCredential`

The Python code uses `DefaultAzureCredential` from `azure-identity`:

```python
credential = DefaultAzureCredential()
```

`DefaultAzureCredential` tries appropriate credential sources available in the environment. For local development, Azure CLI authentication is relevant because the user has previously signed in with `az login`.

The credential is used to obtain a **bearer token** for the Azure OpenAI/Foundry endpoint.

### Security Benefit

Identity-based authentication avoids embedding static API keys in source code.

---

## 13. Python Client Setup

The application uses:

- The Azure endpoint.
- Azure identity / bearer-token authentication.
- An OpenAI SDK client.
- The Azure model deployment name.

Conceptual flow:

1. Load environment variables.
2. Load the YAML configuration file.
3. Create `DefaultAzureCredential`.
4. Obtain bearer-token capability/provider for Azure OpenAI.
5. Create an OpenAI client configured for the Azure endpoint.
6. Call the model using the deployment name and conversation history.

---

## 14. Conversation History in Client-Side Orchestration

In client-side orchestration, the script manages state locally with a **list of dictionaries** containing messages.

The conversation begins with a system message:

```python
conversation_history = [
    {
        "role": "system",
        "content": system_prompt
    }
]
```

Append a user message:

```python
conversation_history.append(
    {
        "role": "user",
        "content": user_message
    }
)
```

Append the assistant response:

```python
conversation_history.append(
    {
        "role": "assistant",
        "content": assistant_reply
    }
)
```

### Why Include Both Roles?

Passing previous user and assistant messages lets future requests include the current conversational context.

### Session-Only Memory

The history is stored in a Python variable, so it:

- Exists only while the program runs.
- Is deleted when the program ends.
- Is not durable memory.
- Is not database-backed.

The lesson does not implement tools, MCP servers, or persistent memory.

---

## 15. Interactive Conversation Loop

The program uses an interactive `while` loop:

1. Prompt the user for input.
2. Stop if the user enters `exit` or `quit`.
3. Append the user message to history.
4. Send the full history to the model.
5. Extract and display the assistant response.
6. Append the assistant response to history.
7. Repeat.

When the loop ends, the in-memory conversation history is lost.

---

## 16. OpenAI SDK Request Pattern

The key request pattern is:

```python
client.chat.completions.create(
    model=deployment_name,
    messages=conversation_history
)
```

| Input | Meaning |
|---|---|
| `model` | The Azure deployment name for the LLM. |
| `messages` | The accumulated system, user, and assistant messages. |

This represents the model invocation point in the lesson’s Python client-side workflow.

---

## 17. Response Handling

The SDK response is an object, not directly a plain-text string.

Extract the assistant response from the first choice:

```python
assistant_reply = response.choices[0].message.content
```

Then display it:

```python
print(assistant_reply)
```

The response can also contain metadata, such as:

- Prompt/input token count.
- Completion/output token count.
- Total token usage.
- Reasoning-token information.
- Duration in milliseconds.

For a simple chat application:

1. Call the model.
2. Extract `response.choices[0].message.content`.
3. Display it.
4. Add it to history as an `assistant` message.

---

## 18. Security Practices

### Prefer Identity-Based Authentication

- Use `DefaultAzureCredential`.
- Obtain bearer-token authentication.
- Use Azure CLI sign-in for applicable local development.
- Avoid hardcoding API keys.

### Keep Infrastructure Values Configurable

Use deployment outputs and environment variables for values such as:

- Endpoint.
- Model deployment name.

### Protect Credentials

Do not expose account credentials, bearer tokens, API keys, or other authentication details.

### Understand State Boundaries

Conversation history is locally maintained. A complete application must explicitly decide whether, how, and where to persist it.

---

## 19. Troubleshooting

| Symptom | Likely area | Lesson-aligned action |
|---|---|---|
| Azure CLI commands are not recognized | CLI installation/path | Close and reopen VS Code after Azure CLI installation. |
| Python packages/imports are missing | Wrong environment or missing dependencies | Activate the intended virtual environment and run `pip install -r requirements.txt`. |
| VS Code runs the wrong interpreter | Debug configuration/environment | Verify the active virtual environment and `launch.json` Python path. |
| Azure authentication fails | Sign-in/account/subscription | Run `az login` and verify the correct Azure subscription. |
| Subscription/account errors | Trial or billing configuration | Verify account and subscription; some scenarios may require pay-as-you-go. |
| Bicep deployment fails due to a name collision | Resource naming | Change to a more unique course/resource prefix. |
| Python cannot reach the model | Endpoint, deployment name, identity, or token configuration | Verify Bicep outputs, environment variables, identity setup, endpoint, and deployment name. |
| First debugger run fails | Local VS Code/Python behavior | Retry and verify interpreter configuration. |
| Conversation context is lost | Expected in-memory behavior | The list exists only until the script exits. |
| Foundry artifacts remain in deletion listings | Soft delete | Use the relevant purge command for each soft-deleted resource name. |

---

## 20. Cost and Cleanup

The lesson recommends deleting lab resources after use to minimize costs and keep the environment clean.

### Cost Model

- **Fixed cost** — ongoing cost for a provisioned resource, where applicable.
- **Usage cost** — cost based on activity, such as model prompts.

The walkthrough states that Foundry generally does not have a normal deployment cost but does incur model-usage costs.

### Delete the Resource Group

Deleting the resource group removes contained resources:

```bash
az group delete
```

### Soft Delete

Foundry resources may be soft-deleted instead of immediately permanently removed.

- Soft-deleted resources can still appear in resource listings.
- The walkthrough states they have no cost while soft-deleted.
- A purge operation may be needed to permanently remove each deleted resource.
- Verify cleanup by listing resources again after purge operations.

---

## 21. End-to-End Workflow Summary

```text
1. Open the project in VS Code.
2. Create and activate a Python virtual environment.
3. Install PyYAML, azure-identity, and openai from requirements.txt.
4. Install Azure CLI and reopen VS Code if needed.
5. Authenticate with az login.
6. Install or upgrade Bicep.
7. Create an Azure resource group.
8. Deploy Foundry resources and the model with Bicep.
9. Retrieve the OpenAI/Foundry endpoint and model deployment name.
10. Put deployment outputs into debug environment variables/configuration.
11. Load system instructions from a YAML file.
12. Use DefaultAzureCredential and bearer-token authentication.
13. Create an SDK client for the Azure endpoint.
14. Initialize message history with the system prompt.
15. Repeatedly collect user input, call the model, extract the response, and append both roles to history.
16. Exit with exit or quit.
17. Delete the resource group.
18. Check for and purge soft-deleted Foundry resources if required.
19. Optionally delete the local virtual environment.
```

---

## 22. AI-103 Exam Takeaways

- **Bicep** is Azure’s declarative Infrastructure as Code language and deploys through ARM templates.
- **Azure CLI** supports authentication, resource-group management, Bicep deployment, and cleanup.
- **Resource groups** provide lifecycle boundaries for related resources; deleting the group removes contained resources.
- Use **Bicep outputs** to supply application values such as endpoints and model deployment names.
- Applications call the Azure **deployment name**, not merely a generic base-model name.
- **DefaultAzureCredential** supports identity-based authentication and avoids embedding API keys.
- In **client-side orchestration**, the application directly calls the LLM and manages state, messages, and flow.
- In **server-side orchestration**, hosted agents/services assume more orchestration responsibility.
- Chat requests contain a model/deployment name plus role-based messages.
- Extract assistant output from `response.choices[0].message.content`.
- Maintaining chat context client-side requires sending prior system, user, and assistant messages in later requests.
- A Python in-memory message list is not persistent memory.
- Keep endpoints and deployment names in environment/configuration values rather than hardcoding them throughout code.
- Azure resource deletion can involve **soft delete** and may require purge operations.
- Remove unneeded development resources to control costs.
