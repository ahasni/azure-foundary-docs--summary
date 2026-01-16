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


