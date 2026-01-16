
## What Is Microsoft Foundry?

> **Note**  
> This document was created with the help of ChatGPT and is based on content from the following source:  
> [Microsoft Foundry Documentation](https://learn.microsoft.com/en-us/azure/ai-foundry/what-is-foundry?view=foundry-classic)  
> This refers to the **Microsoft Foundry (classic) portal**.  
> 🔄 Switch to the Microsoft Foundry (new) documentation if you're using the new portal.

Microsoft Foundry is a **unified Azure platform-as-a-service** (PaaS) for enterprise AI operations, model building, and application development. It combines production-grade infrastructure with developer-friendly interfaces, allowing developers to focus on building applications rather than managing infrastructure.

Microsoft Foundry unifies **agents, models, and tools** under a single management system with enterprise-readiness capabilities, including tracing, monitoring, evaluations, and customizable configurations. The platform provides **streamlined management** through unified **Role-based Access Control (RBAC)**, networking, and policies under one Azure resource provider namespace.

> **Tip:** Azure AI Foundry is now Microsoft Foundry. Screenshots in documentation are being updated.

---

## Microsoft Foundry Portals

There are **two portals** for Microsoft Foundry:

| Portal | Banner Display | When to Use |
|--------|----------------|-------------|
| Microsoft Foundry (classic) | – | Use when working with multiple resource types: Azure OpenAI, Foundry resources, hub-based projects, or Foundry projects. |
| Microsoft Foundry (new) | – | Use for a seamless experience combining simplicity with secure tools to build and manage multi-agent applications. Only Foundry projects are visible. |

> **Tip:** All links open the portal version you last used.

---

## Microsoft Foundry (classic)

Designed for developers to:

- Build generative AI applications and AI agents on an **enterprise-grade platform**
- Explore, build, test, and deploy using AI models and tools grounded in **responsible AI practices**
- Collaborate throughout the full **application development lifecycle**
- Work across **model providers** with a consistent API

Microsoft Foundry facilitates **scalability**, turning proofs of concept into production applications, with **continuous monitoring and refinement** for long-term success.

---

## Foundry Projects

A **Foundry project** is the primary workspace for development:

- Can be accessed via the **Foundry portal** or SDK
- Provides **self-serve capabilities** to create new environments for prototyping and exploration
- Acts as a **secure unit of isolation** for collaboration, sharing file storage, conversation threads, and search indexes
- Supports **bring-your-own Azure resources** for compliance and sensitive data control

---

## Microsoft Foundry API and SDKs

The Foundry API enables building **agentic applications** with a consistent contract across model providers. SDKs simplify integration into applications.

**Available SDKs:**
- Python
- C#
- JavaScript/TypeScript (preview)
- Java (preview)

The **Foundry VS Code Extension** helps developers explore models and build agents directly in their development environment.

---

## Types of Projects

Classic Foundry supports **two project types**:

1. **Foundry Project**  
   - Managed under a Microsoft Foundry resource  
   - Container for access management, data upload, integration, and monitoring  
   - Recommended for building agents and working with models

2. **Hub-Based Project**  
   - Hosted by a Microsoft Foundry hub  
   - Suitable if your organization has administrative hubs  
   - New default hub is automatically created if working independently

> **Note:** New agents and model-centric capabilities are only available on **Foundry projects**.

**Feature Comparison:**

| Capability | Foundry Project | Hub-Based Project |
|------------|----------------|-----------------|
| Agents | ✅ GA | ✅ Preview only |
| Azure Models (OpenAI, DeepSeek, xAI, etc.) | ✅ | Via connections |
| Partner/Community Models (Stability, Cohere, etc.) | ✅ | Via connections |
| Managed compute deployments (e.g. HuggingFace) | ✅ | – |
| Foundry SDK & API | ✅ | Limited |
| OpenAI SDK & API | ✅ | Via connections |
| Evaluations | ✅ Preview | ✅ |
| Playgrounds | ✅ | ✅ |
| Content Understanding | ✅ | ✅ |
| Model Router | ✅ | ✅ |
| Datasets | ✅ | ✅ |
| Indexes | ✅ | ✅ |
| Project Files API | ✅ | Limited |
| Project-Level Isolation | ✅ | Limited |
| Bring-Your-Own Key Vault | ✅ | ✅ |
| Bring-Your-Own Storage | ✅ | ✅ |
| Prompt Flow | ✅ | – |

---

## Identifying Your Project Type

- **Breadcrumb Navigation**  
  - Foundry project: displays `(Foundry)` on the second line  
  - Hub-based project: displays `(Hub)` on the second line

- **All Resources Page**  
  - Foundry project: `(Foundry)` as parent resource  
  - Hub-based project: `(Hub)` as parent resource

---

## Navigating the Foundry (classic) Portal

Use **breadcrumbs** for quick navigation among resources.  
The **left pane** is organized around development goals:

1. **Define and Explore** – Define goals, explore models and services
2. **Build and Customize** – Build solutions, customize models, fine-tune, or ground in your data
3. **Observe and Improve** – Monitor, debug, evaluate, and integrate safety/security before production

**Admins** can manage resources, quotas, access, and permissions via the **Management Center**.

---

## Customizing the Left Pane

- Pin/unpin items to show frequently used tools
- Each project has its own left pane; changes are **user-specific**
- Access additional items via `...More` menu

---

## Management Center

The Management Center streamlines governance by allowing admins to manage:

- Projects and resources
- Quotas and usage metrics
- Access and permissions

For more information, see **Management Center overview**.


Below are **practical Python code examples** with **step-by-step explanations**, showing how a developer would actually use **Microsoft Foundry** in real workflows.

---

## 1. Create a Foundry Project Client (Base Setup)

This is the **entry point** for everything in Foundry.

```python
from azure.identity import DefaultAzureCredential
from azure.ai.projects import AIProjectClient

project = AIProjectClient(
    endpoint="https://<YOUR-FOUNDRY-PROJECT-ENDPOINT>",
    credential=DefaultAzureCredential()
)
```

### Explanation

* `AIProjectClient` connects your app to a **Foundry project**
* `DefaultAzureCredential` automatically handles:

  * Local dev login (Azure CLI)
  * Managed Identity (Azure VM, App Service, AKS)
  * CI/CD authentication
* The **project endpoint scopes access** to models, agents, tools, and evaluations

👉 This client becomes your **control plane** for AI resources.

---

## 2. Call an OpenAI Model via the Foundry Project (Recommended)

This is the **preferred way** when using agents, evaluations, or governance.

```python
openai_client = project.get_openai_client(
    api_version="2024-10-01-preview"
)

response = openai_client.responses.create(
    model="gpt-4.1-mini",
    input="Explain the CAP theorem in simple terms."
)

print(response.output_text)
```

### Explanation

* The OpenAI client is **derived from the project**
* Authentication uses **Microsoft Entra ID**
* Model access is **project-governed**
* You can switch models **without changing code** if the project config changes

✅ Best for:

* Enterprise apps
* Agent-based systems
* Auditing, tracing, and safety

---

## 3. Direct Azure OpenAI Call (Model-Only, No Foundry Features)

Use this when you **only need inference**, not agents or tools.

```python
from openai import OpenAI
from azure.identity import DefaultAzureCredential, get_bearer_token_provider

token_provider = get_bearer_token_provider(
    DefaultAzureCredential(),
    "https://cognitiveservices.azure.com/.default"
)

client = OpenAI(
    base_url="https://<YOUR-RESOURCE-NAME>.openai.azure.com/openai/v1/",
    api_key=token_provider
)

response = client.responses.create(
    model="gpt-4o-mini",
    input="Summarize this text in one sentence."
)

print(response.output_text)
```

### Explanation

* Bypasses the Foundry project
* Still uses **secure Entra ID authentication**
* Faster and simpler, but:

  * ❌ No agents
  * ❌ No evaluations
  * ❌ No centralized tracing

✅ Best for:

* Lightweight services
* Existing OpenAI-based apps
* Simple APIs

---

## 4. Why the Project Client Matters (Architecture View)

```text
Your App
  |
  v
AIProjectClient  ← authentication, governance, tracing
  |
  ├── OpenAI Models
  ├── Agents v2
  ├── Evaluations
  ├── Fine-tuning
  └── Foundry Tools (Vision, Speech, Language)
```

**Key benefit:** You don’t hardcode service-specific endpoints everywhere.

---

## 5. Using Foundry Tools (Example: Document Intelligence)

Foundry Tools are accessed via **Azure AI Services endpoints**, not OpenAI.

```python
from azure.ai.documentintelligence import DocumentIntelligenceClient
from azure.core.credentials import AzureKeyCredential

doc_client = DocumentIntelligenceClient(
    endpoint="https://<YOUR-RESOURCE>.services.ai.azure.com/",
    credential=AzureKeyCredential("<YOUR-KEY>")
)

poller = doc_client.begin_analyze_document(
    "prebuilt-layout",
    document=open("sample.pdf", "rb")
)

result = poller.result()

for page in result.pages:
    print(f"Page {page.page_number} has {len(page.lines)} lines")
```

### Explanation

* Foundry **does not replace** individual AI SDKs
* It **organizes and connects them**
* Tools like Vision, Language, Speech each have their own SDKs
* Foundry helps you **discover and manage endpoints**

---

## 6. Typical Developer Workflow

```text
1. az login
2. Create Foundry Project
3. Copy project endpoint
4. Create AIProjectClient
5. Get OpenAI / Agent / Tool client
6. Build & iterate
7. Add evaluations + tracing
8. Deploy with Managed Identity
```

---

## 7. When to Use What (Quick Decision Guide)

| Need                            | Use                    |
| ------------------------------- | ---------------------- |
| Agents, evaluations, governance | Foundry Project SDK    |
| Simple LLM call                 | Azure OpenAI SDK       |
| Vision, Speech, Language        | Foundry Tools SDKs     |
| Enterprise production app       | Foundry + Entra ID     |
| Rapid prototype                 | Direct OpenAI endpoint |

---

If you want, I can next:

* Show a **multi-step agent example**
* Add **evaluation + tracing code**
* Provide a **clean project template**
* Explain **how to deploy this in Azure (App Service / AKS)**


## Speech Endpoints

| Foundry Tool                 | Endpoint |
|-----------------------------|----------|
| Speech to Text (Standard)   | `https://<YOUR-RESOURCE-REGION>.stt.speech.microsoft.com` |
| Text to Speech (Neural)     | `https://<YOUR-RESOURCE-REGION>.tts.speech.microsoft.com` |
| Custom Voice                | `https://<YOUR-RESOURCE-NAME>.cognitiveservices.azure.com/` |

## Translation Endpoints

| Foundry Tool            | Endpoint |
|-------------------------|----------|
| Text Translation        | `https://api.cognitive.microsofttranslator.com/` |
| Document Translation    | `https://<YOUR-RESOURCE-NAME>.cognitiveservices.azure.com/` |


