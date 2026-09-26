# Getting Started with Microsoft Azure AI Foundry — Study Notes

## Lesson Scope

This lesson walks through deploying Microsoft Azure AI Foundry through the Azure portal, creating its hub and initial project, deploying a model, testing it in the playground, requesting quota, and comparing models.

> **Terminology note:** The lesson refers to the experience as **Microsoft Foundry** / **Foundry** in the portal walkthrough.

---

## Prerequisites and Cost Model

- Use the **Azure portal**.
- Either a **pay-as-you-go** subscription or a **free trial credit** version can be used for the walkthrough.
- There is **no charge to deploy Foundry itself**.
  - The lesson states that you can spin it up and spin it down through the portal or code without a deployment cost.
- Charges arise from activity **within Foundry**, such as calling large language models.
- Model pricing information can be viewed when selecting a model, including:
  - Context window.
  - Token limits.
  - Pricing.
- The lesson’s displayed example described GPT-5 mini pricing as **USD $0.25 per 1 million input tokens**. Treat pricing as something to review in the portal because models and rates vary.

---

## Azure Portal Workflow: Create Foundry, Hub, and Project

### 1. Locate the Resource

1. In the Azure portal, go to **Resources**.
2. Search for **Foundry**.
3. Select **Microsoft Foundry**.
4. Choose to create a resource.

### 2. Create or Choose a Resource Group

- Azure uses **resource groups** to group similar resources.
- In the lesson, a new resource group was created rather than using an existing one.
- Example lesson value: `AI course resource group`.

### 3. Name the Foundry Resource

- Create a Foundry resource and provide a valid resource name.
- The portal indicates acceptance with a green check mark.
- Example lesson value: `AI Foundry course one`.

### 4. Select a Region

- The instructor recommends using the region in which you are physically located.
- Example used in the lesson: **Australia East**.

### 5. Understand the Hub/Project Structure

- Creating the Foundry resource technically creates the **Foundry hub**.
- The **Foundry hub** is the first layer.
- A **Foundry project** is the second layer.
- The creation experience provides a default project name; the lesson accepts that default name.

### 6. Review and Create

1. The lesson does not configure unrelated areas such as networking.
2. Select **Review + create**.
3. Wait for validation to complete.
4. Select **Create**.
5. Azure deploys:
   - The resource group.
   - The Foundry hub.
   - The Foundry project.

### Relevant Platform Detail

- The lesson notes that Foundry is technically part of a **Cognitive Services account**.
- This is highlighted as useful for later infrastructure-as-code work.

---

## Open the Foundry Portal

Two portal paths were shown.

### Path A: From the Deployment Result

1. Select **Go to resource**.
2. Select **Go to Foundry portal**.

### Path B: From the Resource Group

1. Return to Azure portal home.
2. Open **Resource groups**.
3. Open the resource group you created.
4. Select either the Foundry resource or the Foundry project.
5. Use the displayed option to open the Foundry portal.

The lesson states that selecting either the Foundry resource or project provides the same relevant pop-up/path into Foundry.

---

## Projects, Endpoints, and Connection Details

Once inside Foundry:

- The active hub and project are shown.
- The project selector is in the upper-left area.
- You can create and work with multiple projects.

### If the Project Does Not Connect

A project connection issue may simply be a timing issue after provisioning.

Suggested remedy from the lesson:

- Refresh Foundry, or
- Close and reopen Foundry.

### Available Connection Details

The project view displays:

- An **API key**.
- A **project endpoint**.
- An **OpenAI endpoint**.

Depending on how code connects to Foundry, it may require one or both endpoints.

---

## Deploying a Base Model

### Navigation

1. Go to **Build**.
2. Select **Deployments**.
3. Select **Deploy**.
4. Select **Deploy a base model**.

If no models have yet been deployed, the deployments list is empty.

### Model Catalog

- Selecting **Deploy a base model** opens the **model catalog**.
- The catalog contains many model families and types, including GPT models and other providers/models referenced in the lesson.
- Not every model is immediately usable: some require a quota request.

### Choosing a Model

- Filter or search the catalog for the desired model family.
- The walkthrough selects **GPT-5 mini**.
- The lesson notes there may be multiple model variants and versions.

### Information Available on a Model Page

When a model is selected, review:

- Context window.
- Token limits.
- Pricing.

For infrastructure-as-code scenarios, the lesson says it can be necessary to know:

- The model version number.
- Whether it came from OpenAI or another source.

For the portal-only walkthrough, these details are not required to complete deployment.

---

## Deployment Choices and Settings

The lesson identifies two deployment options:

- **Default settings**.
- **Custom settings**.

### Default Settings

- Choosing **Default settings** deploys the model directly.

### Custom Settings

Custom settings expose options including the following.

#### Tokens-Per-Limit Setting

- You can set the token-per-limit value lower or raise it to the available maximum.
- The lesson presents this as a spending-control or safety measure.
- Lower limits can help prevent unexpectedly high model-usage costs.

#### Model Version Settings

- You may be able to choose prior or later model versions when available.

#### Upgrade Policy

The lesson indicates that upgrade behavior can be configured, such as upgrading:

- When a new default version becomes available, or
- When the current version expires.

#### Guardrails

- Guardrails are already set by default in the walkthrough.

### Complete the Deployment

- Select **Deploy**.
- The lesson says deployment is normally quick.
- After it completes, the model appears as deployed and can be tested.

---

## Playground Testing

The playground lets you test a deployed model without writing code.

### System Instructions

The **Instructions** area contains what the lesson calls system instructions.

- In code, these instructions normally need to be defined programmatically.
- In the playground, they can be entered directly.

Example concept from the lesson:

- Configure the model as a customer-support bot that answers only product questions.

### Why Test Instructions

The lesson demonstrates that system instructions change the model’s behavior:

- A non-product question was refused because the instruction limited responses to products.
- A product-related question produced an answer.

Use the playground to test:

- Model behavior.
- System instructions.
- Prompt responses.
- The suitability of a model before integrating it in code.

### Privacy and Cost Warning

The lesson characterizes the deployed model as your own local or cloud-copied version, giving privacy benefits. However:

- Usage is still chargeable.
- Costs can accumulate with extensive use or broad organizational usage.

---

## Call Model: Generated Code Assistance

The **Call model** option provides code examples for connecting to the deployed model.

The lesson describes examples that include:

- Project endpoint automatically populated.
- Model name automatically populated.
- Authentication setup.
- A Python example using an OpenAI client.
- A request example using `client.responses.create`.
- An alternative mentioned: `client.chat.completions`.

### Authentication Examples Shown

The lesson mentions two authentication approaches:

- **Microsoft Entra ID**.
- **API key**.

For key authentication, the generated example expects an API key, which can be obtained from the displayed project API key information.

---

## Quotas and Capacity

Quota is a major operational concern in Foundry.

### What Quota Means

A quota request asks Microsoft for access/capacity for a specific combination of:

- Model.
- Region.
- Account or subscription.
- Token capacity per minute.

### Why Quota Matters

Some models have quota by default, but not all.

Potential symptoms of insufficient quota:

- A model deploys, but calls to it return errors.
- Error messages occur after only a few prompts.
- Rate-limiting errors occur during use.

These situations likely require requesting quota.

### Quota-Request Strategy

The instructor’s recommendation:

1. Request the **smallest amount** initially.
2. Request more later if necessary.
3. Avoid initially requesting the largest amount, because larger requests may be rejected.

The lesson cites a personal recommendation of requesting a value between **20 and 200**, with **20** previously working for the instructor. This is instructor guidance, not a guaranteed capacity level.

### How to Request Quota

1. Open the deployed model.
2. Go to **Details**.
3. Select **Request quota**.
4. Complete the form with:
   - First name.
   - Last name.
   - Email.
   - Company information or personal details as applicable.
   - Address.
   - City.
   - Postcode.
   - Country.
   - Subscription ID.
   - A justification.

### Get the Subscription ID

1. Return to the Azure portal.
2. Search for **Subscription**.
3. Open **Subscriptions**.
4. Copy the subscription ID for the account.
5. Paste it into the quota request form.

### Quota Request Inputs Highlighted in the Lesson

- A short justification is required.
  - Example concept: learning Foundry and testing models and agents.
- The instructor recommends choosing the **model deployment** option for the walkthrough.
- The walkthrough identifies the model provider as **Azure OpenAI**.
- The deployment type shown was **Global Standard**.
- Select the desired model manually from the list.
- Enter the requested capacity number.

### Approval Timing Warning

Quota approval may be:

- Almost immediate.
- About an hour.
- Up to a day.

Request quota as early as possible so capacity may be available by the next day.

---

## Monitoring Model Usage

The lesson notes that Foundry provides information on model token usage, including:

- Input tokens.
- Output tokens.

This information is useful for evaluating consumption and cost implications.

---

## Comparing Models in the Playground

Foundry supports direct side-by-side model comparison.

### Side-by-Side Comparison Workflow

1. Go to **Compare models**.
2. Deploy or select two models for comparison.
3. Enter one prompt.
4. Send the prompt to both models.
5. Compare their responses.

### What to Compare

The lesson demonstrates comparing:

- Response speed.
- Output length.
- Output token count.
- Likely cost implications.

In the walkthrough’s comparison:

- GPT-4.1 mini was described as faster and returned less output.
- GPT-5 mini was described as slower and returned more output.
- The differing output-token counts illustrated that model choice can change usage and cost.

---

## Model Leaderboards and Trade-Off Analysis

A separate comparison route was shown:

1. Go to **Home**.
2. Find **Compare models**.

The model-comparison area includes leaderboards and selection tools.

### Comparison Dimensions Mentioned

Models can be assessed by:

- Output quality.
- Safety.
- Token throughput / response speed.
- Cost.

Additional evaluation dimensions referenced include:

- Harmful behavior.
- Attack success rate.
- Copyright violations.
- Knowledge in sensitive domains.
- Toxicity detection.
- Reasoning.
- Coding accuracy.
- General knowledge.
- Question answering.
- Grounded-information handling.

### Trade-Off Chart

The lesson strongly recommends using the trade-off chart.

- Select models to compare them visually.
- Assess where models sit relative to cost and quality.
- Use the chart to identify potentially better-value choices rather than choosing solely by model name or generation.

---

## Warnings and Practical Considerations

- **No deployment fee does not mean no usage cost.** Model calls incur costs.
- **Review token limits and pricing** before selecting and deploying models.
- **Use custom deployment limits** when appropriate to constrain spending.
- **Quota may be required even if deployment succeeds.** A model can deploy but still fail during use if capacity is unavailable.
- **Start quota requests small.** Larger requests may be rejected; increase later as needed.
- **Request quota early.** Approval can take up to a day.
- **Project connection issues may be timing-related.** Refresh or reopen Foundry before assuming a configuration problem.
- **System instructions materially affect outputs.** Test them in the playground before implementing the model in an application.
- **Model selection affects speed, output volume, and cost.** Compare models rather than assuming the newest or largest option is always best.

---

## AI-103 Exam Takeaways

- Azure AI Foundry is provisioned in Azure through a resource-creation flow that produces:
  - A Foundry hub.
  - A Foundry project.
- Foundry is technically associated with a **Cognitive Services account**.
- Azure resource groups organize related Azure resources.
- The Foundry project exposes items relevant to application integration:
  - API key.
  - Project endpoint.
  - OpenAI endpoint.
- Use **Build** → **Deployments** → **Deploy** → **Deploy a base model** to deploy catalog models.
- The **model catalog** is the source for finding available foundation/base models.
- Deployment may use default or custom settings.
- Custom deployment settings can include token limits, version selection, upgrade policy, and guardrails.
- Quota is tied to model access/capacity and can depend on model, region, and subscription.
- Insufficient quota can cause errors or rate limiting, including after a model has deployed successfully.
- Quota requests require subscription information and may take time to process.
- The playground supports no-code testing of:
  - Deployed models.
  - System instructions.
  - Prompt behavior.
- Model comparison features support selection based on quality, safety, speed/throughput, cost, and task-specific evaluation criteria.
- Compare output tokens as well as response quality and speed, because output volume can affect cost.
