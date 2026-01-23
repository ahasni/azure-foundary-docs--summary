# Azure AI Search

Azure AI Search is a fully managed, cloud-hosted search service that connects enterprise and web data to AI applications. It enables agents, chatbots, and large language models (LLMs) to generate reliable, grounded answers using indexed and remote content, contextual signals, and chat history.

It supports both traditional search experiences and modern retrieval-augmented generation (RAG) through agentic retrieval.

## Key Capabilities

Two search engines
Classic search for low-latency, predictable queries
Agentic retrieval for complex, multi-step, LLM-assisted retrieval
Query types: full-text, vector, hybrid, and multimodal
AI enrichment for chunking, embedding, and transforming content
Relevance tuning to improve result quality
Enterprise-grade security, compliance, and scalability
Native integrations with Azure OpenAI, Microsoft Foundry, and Azure data platforms

## Rest API

Endpoint : https://{search-service-name}.search.windows.net/indexes?api-version=2024-07-01

### List existing indexes by name
__GET__ {{baseUrl}}/**indexes**?api-version=2025-09-01

### Create a new index
**POST** {{baseUrl}}/**indexes**?api-version=2025-09-01

### Upload documents
**POST** {{baseUrl}}/**indexes**/hotels-quickstart/docs/index?api-version=2025-09-01

## Python SDK

**Connect to a search service Example**
Used Classes : azure.identity.**DefaultAzureCredential**, azure.search.documents.indexes.**SearchIndexClient**
Used methods :  client.**list_indexes()** (List existing indexes)

**Agentic Retrieval (RAG) Example**
Used Classes : azure.search.documents.indexes.**SearchIndexClient**, azure.search.documents.**SearchIndexingBufferedSender**, azure.search.documents.knowledgebases.**KnowledgeBaseRetrievalClient**
Used methods : index_client.**create_or_update_index(index)** (create index), index_client.**create_or_update_knowledge_source(knowledge_source=ks)** (Create a knowledge source), index_client.**create_or_update_knowledge_base(knowledge_base)**,
agent_client.retrieve(retrieval_request=req), ndex_client.delete_knowledge_base(knowledge_base_name), index_client.delete_knowledge_source(knowledge_source=knowledge_source_name),
index_client.delete_index(index_name)

**Upload and Run vector Search Example
Used Classes : **SearchClient**
Used methods : search_client.**upload_documents(documents=documents)**,  results = **search_client.search(...)**

-----------------------------------------------------------------------------------------------------------------------------------------

# Foundry Agent Service

Foundry Agent Service is the core runtime of Microsoft Foundry, designed to help businesses move from AI prototypes to secure, scalable, production-ready intelligent agents. It enables automation beyond chatbots by orchestrating models, tools, workflows, and governance in a unified platform.

## An AI agent:

Makes decisions
Invokes tools
Participates in workflows
Works independently or collaboratively with humans or other agents

## Limitation :
Limit name 	Limit value
Maximum number of files per agent/thread 	**10,000**
Maximum file size for agents 	**512 MB**
Maximum size for all uploaded files for agents 	**300 GB**
Maximum file size in tokens for attaching to a vector store 	**2,000,000 tokens**
Maximum number of messages per thread 	**100,000**
Maximum size of text content per message 	**1,500,000 characters**
Maximum number of tools registered per agent 	**128**

## REST API

{endpoint} : Project endpoint in the form of: **https://AI_SERVICE_ID.services.ai.azure.com/api/projects/PROJECT_NAME**

Create Agent Endpoint

POST {endpoint}/**assistants**?api-version=v1
{
"model": "naod"
}

Create Thread And Run Endpoint

POST {endpoint}/**threads/runs**?api-version=v1
{
"assistant_id": "asst_abc123"
}

Messages - Create Message Endpoint

POST {endpoint}/**threads/thread_abc123/messages**?api-version=v1
{
"role": "user",
"content": "Hello, how can you help me today?"
}

## Python SDK

AI Search tool for agents Example
Used Classes : azure.ai.projects import AIProjectClient, azure.ai.projects.models import ( AzureAISearchAgentTool, PromptAgentDefinition, AzureAISearchToolResource, AISearchIndexResource, AzureAISearchQueryType )
Used methods : openai_client = project_client.get_openai_client(), agent = project_client.agents.create_version(agent_name="MyAgent", definition=PromptAgentDefinition(model=model_deploy, instructions="you are ...", tools=[ ])), stream_response = openai_client.responses.create(), project_client.agents.delete_version()


File Search tool Example
Used methods : 
Create vector store for file search
vector_store = **openai_client.vector_stores.create(name="ProductInfoStore")**

Upload file to vector store
file = **openai_client.vector_stores.files.upload_and_poll(
vector_store_id=vector_store.id, file=open(asset_file_path, "rb")
)**


Create agent with file search tool
agent = **project_client.agents.create_version(...)**

Create a conversation for the agent interaction
conversation = **openai_client.conversations.create()**

Send a query to search through the uploaded file
response = **openai_client.responses.create(...)**

Create an Agent with the MCP Tool Example

Used Classes : azure.ai.projects import **AIProjectClient**
azure.ai.projects.models import **PromptAgentDefinition, MCPTool, Tool**
openai.types.responses.response_input_param import **McpApprovalResponse, ResponseInputParam**
Used methods :	
Create a prompt-based agent with MCP capabilities
agent = **project_client.agents.create_version()**

Create a conversation
conversation = **openai_client.conversations.create()**

Trigger MCP tool usage
response = **openai_client.responses.create(**
**conversation=conversation.id,
**input="What is my username in Github profile?",
**extra_body={"agent": {"name": agent.name, "type": "agent_reference"}},
**)**

-----------------------------------------------------------------------------------------------------------------------------------

# Azure AI Content Safety

Azure AI Content Safety is an AI service designed to detect and moderate harmful content in both user-generated and AI-generated text and images. It helps applications comply with regulations, maintain safe environments, and manage content risks at scale.

The service provides APIs for automated detection and an interactive Content Safety Studio for testing, configuration, and monitoring—without requiring custom model development.


## Product Features

Azure AI Content Safety offers multiple APIs for text and image analysis:
Key APIs

Prompt Shields: Detects jailbreak and prompt-injection attacks on LLMs
Groundedness Detection (Preview): Verifies whether LLM responses are grounded in provided sources
Protected Material Detection: Identifies known copyrighted or protected text
Custom Categories (Preview):
Standard: Train custom harmful-content categories
Rapid: Detect emerging harmful patterns in text and images
Analyze Text API: Detects sexual content, violence, hate, and self-harm
Analyze Image API: Detects harmful visual content
Task Adherence API: Identifies misaligned or unintended AI tool usage

## Input Requirements (Highlights)

Analyze Text: **Up to 10K characters
Analyze Image:
Max size: **4 MB
Formats: **JPEG, PNG, GIF, BMP, TIFF, WEBP
Dimensions: **50×50 to 7200×7200
Prompt Shields: **10K characters, up to 5 documents
Groundedness Detection:
**55K characters for sources
**7.5K characters for queries
Task Adherence: **Up to 100K characters

## REST API

Analyze Image Endpoint

**POST** {endpoint}/**contentsafety/image:analyze?api-version=2024-09-01**

Analyze Text Endpoint

**POST** {endpoint}/**contentsafety/text:analyze?api-version=2024-09-01**

Detect Text Protected Material Endpoint

**POST** {endpoint}/**contentsafety/text:detectProtectedMaterial?api-version=2024-09-01**

Shield Prompt Endpoint

**POST** {endpoint}/**contentsafety/text:shieldPrompt?api-version=2024-09-01**

## Python SDK

Analys Text Content Example
Used Classes : 
from azure.ai.contentsafety import **ContentSafetyClient
from azure.core.credentials import **AzureKeyCredential
from azure.core.exceptions import **HttpResponseError
from azure.ai.contentsafety.models **import AnalyzeTextOptions, TextCategory

Enum:
**TextCategory.HATE
**TextCategory.SELF_HARM
**TextCategory.SEXUAL	
**TextCategory.VIOLENCE

Used Methods : 

Create an Azure AI Content Safety client
client = **ContentSafetyClient(endpoint, AzureKeyCredential(key))**

Contruct request
request = **AnalyzeTextOptions(text="Your input text")**

Analyze text
response = **client.analyze_text(request)**

Text Blocklist Example
Used Classes :
from azure.ai.contentsafety import **BlocklistClient
from azure.ai.contentsafety import **ContentSafetyClient
from azure.core.credentials import **AzureKeyCredential
from azure.core.exceptions import **HttpResponseError
from azure.ai.contentsafety.models import (
**TextBlocklist, AddOrUpdateTextBlocklistItemsOptions, TextBlocklistItem, AnalyzeTextOptions
)


Used Methods :
Create a Blocklist client
client = **BlocklistClient(endpoint, AzureKeyCredential(key))


blocklist = **client.create_or_update_text_blocklist(
**blocklist_name=blocklist_name,
**options=TextBlocklist(blocklist_name=blocklist_name, description=blocklist_description),
**	)
Create a Content Safety client
client = **ContentSafetyClient(endpoint, AzureKeyCredential(key))


After you edit your blocklist, it usually takes effect in 5 minutes, please wait some time before analyzing
with blocklist after editing.
analysis_result = **client.analyze_text(
**AnalyzeTextOptions(text=input_text, blocklist_names=[blocklist_name], halt_on_blocklist_hit=False)
**)

Analyze Image Content Example
Used Classes :
from azure.ai.contentsafety import **ContentSafetyClient
from azure.ai.contentsafety.models import **AnalyzeImageOptions, ImageData, ImageCategory
from azure.core.credentials import **AzureKeyCredential
from azure.core.exceptions import **HttpResponseError

Enum:
**ImageCategory.HATE
**ImageCategory.SELF_HARM
**ImageCategory.SEXUAL
**ImageCategory.VIOLENCE

Useed Methods:

Create an Azure AI Content Safety client
client = **ContentSafetyClient(endpoint, AzureKeyCredential(key))

Build request
with open(image_path, "rb") as file:
request = **AnalyzeImageOptions(image=ImageData(content=file.read()))**

Analyze image
response = **client.analyze_image(request)**					

----------------------------------------------------------------------------------------------------------------------

# Azure Content Understanding

Azure Content Understanding is a generally available (GA) Foundry Tool that uses generative AI to process and ingest unstructured and multimodal content—such as documents, images, video, and audio—into structured, user-defined outputs. It accelerates time-to-value by transforming raw content into actionable data that integrates easily with automation, analytics, search, and agentic workflows.


## Key Use Cases

Intelligent Document Processing (IDP): Convert complex documents into structured data with validation and confidence scoring.
Agentic applications: Produce clean markdown or schema-aligned outputs for reliable reasoning and automation.
Search & RAG ingestion: Enhance retrieval with figure analysis, layout preservation, and multimodal support.
Robotic Process Automation (RPA): Enable end-to-end automation of document-driven workflows.
Analytics & reporting: Generate structured outputs for deeper insights and decision-making.
Workflow optimization: Classify content before extraction to improve routing and efficiency.


## Resource limits (Standard S0)

Max analyzers: **100,000
Max analysis/min: **1,000 pages/images
Audio & Video: **up to 4 hours each
Max operations/min: **3,000

Supported generative models

Chat completion: **GPT-4o, GPT-4.1, mini & nano variants
Embeddings: **text-embedding-3-small, text-embedding-3-large, text-embedding-ada-002

Input file limits

Documents & text: **PDF, Office (docx, xlsx, pptx), TXT, HTML, Markdown, RTF, email, XML
**≤ 200 MB / ≤ 300 pages or ≤ 1M characters
**Pro mode (preview): only PDF, TIFF, images; max 100 MB / 150 pages**
Images: **JPEG, PNG, BMP, HEIF/HEIC; ≤ 200 MB, 50×50 to 10,000×10,000 px
Audio: **WAV, MP3, MP4, Opus/OGG, FLAC, WMA, AAC, AMR, 3GP, WebM, M4A, SPX; ≤ 300 MB / 2 h†
**†Supports up to 1 GB / 4 h, faster processing for ≤ 300 MB / ≤ 2 h
Video: **MP4, M4V, FLV, WMV, ASF, AVI, MKV, MOV; min 320×240 px, max 1920×1080 px
**Direct upload: ≤ 200 MB / 30 min
**URL reference: ≤ 4 GB / 2 h
**Frame sampling: ~1 frame/sec, frames scaled to 512×512 px


## REST API

Extract content and fields from input.

**POST** {endpoint}/**contentunderstanding/analyzers/{analyzerId}:analyze?api-version=2025-11-01

Content Analyzers - Get Operation Status Endpoint

Get the status of an analyzer creation operation.

**GET** {endpoint}/**contentunderstanding/analyzers/{analyzerId}/operations/{operationId}?api-version=2025-11-01

Get Result Endpoint

Get the result of an analysis operation.

**GET** {endpoint}/**contentunderstanding/analyzerResults/{operationId}?api-version=2025-11-01

Content Analyzers - Get Result File Endpoint

Get a file associated with the result of an analysis operation.

**GET** {endpoint}/**contentunderstanding/analyzerResults/{operationId}/files/{path}?api-version=2025-11-01


------------------------------------------------------------------------------------------------------------------------------------------

# Azure Document Intelligence

Azure Document Intelligence is a cloud-based AI service that enables applications to analyze, extract, and understand information from documents. It supports a wide range of document types and formats, including structured, semi-structured, and unstructured content. The service integrates with Microsoft Foundry, REST APIs, and client libraries to support both low-code and pro-code development.

## Core Capabilities

Azure Document Intelligence uses advanced machine learning models to extract text, layout, key-value pairs, tables, and structured data from documents such as PDFs, images, forms, and receipts.

Key capabilities include:

Optical Character Recognition (OCR)
Document layout analysis
Structured data extraction
Custom model training

## Supported Document Types
| Document Type                               | Read | Layout | Prebuilt Models | Custom Models | Add-on Capabilities |
|--------------------------------------------|------|--------|-----------------|---------------|--------------------|
| PDF                                        | ✔️   | ✔️     | ✔️              | ✔️            | ✔️                 |
| Images (JPEG, PNG, BMP, TIFF, HEIF)        | ✔️   | ✔️     | ✔️              | ✔️            | ✔️                 |
| Microsoft Office (DOCX, PPTX, XLS)         | ✔️   | ✔️     | ✖️              | ✖️            | ✖️                 |


## General Quotas
| Limit                          | Free (F0) | Standard (S0) | Adjustable |
|--------------------------------|-----------|---------------|------------|
| Analyze transactions / sec    | **1         | 15            | No / Yes   |
| Get operations / sec          | **1         | 50            | No / Yes   |
| Model management ops / sec    | **1         | 5             | No / Yes   |
| List operations / sec         | **1         | 10            | No / Yes   |
| Max document size             | **4 MB      | 500 MB        | No         |
| Max pages (analysis)          | **2         | 2,000         | No         |
| Max size labels file          | **10 MB     | 10 MB         | No         |
| Max OCR JSON response         | **500 MB    | 500 MB        | No         |
| Max template models           | **500       | 5,000         | No         |
| Max neural models             | **100       | 500           | No         |



## REST API

{endpoint} : The Cognitive Services endpoint, including protocol and hostname (for example, **https://RESOURCE_NAME.cognitiveservices.azure.com**).

Analyze Batch Documents Endpoint

Analyzes batch documents with document model.

**POST** {endpoint}/**documentintelligence/documentModels/{modelId}:analyzeBatch?api-version=2024-11-30**


Analyze Document From Stream Endpoint

Analyzes document with document model.

**POST** {endpoint}/**documentintelligence/documentModels/{modelId}:analyze?api-version=2024-11-30**

Analyze Document Endpoint

Analyzes document with document model.

**POST** {endpoint}/**documentintelligence/documentModels/{modelId}:analyze?_overload=analyzeDocument&api-version=2024-11-30

## Python SDK

Layout modele Example

Used Classes : 
from azure.core.credentials import **AzureKeyCredential
from azure.ai.documentintelligence import **DocumentIntelligenceClient
from azure.ai.documentintelligence.models import **AnalyzeResult
from azure.ai.documentintelligence.models import **AnalyzeDocumentRequest

Used Methods: 
document_intelligence_client = **DocumentIntelligenceClient(
**endpoint=endpoint, credential=AzureKeyCredential(key)
**)

poller = document_intelligence_client.**begin_analyze_document(
**"prebuilt-layout", AnalyzeDocumentRequest(url_source=formUrl
))**

result: AnalyzeResult = poller.**result()**

Prebuilt model(prebuilt-invoice) Example

Used Classess:
from azure.core.credentials import **AzureKeyCredential
from azure.ai.documentintelligence **import DocumentIntelligenceClient
from azure.ai.documentintelligence.models import **AnalyzeResult
from azure.ai.documentintelligence.models import **AnalyzeDocumentRequest

Used Methods :

document_intelligence_client = **DocumentIntelligenceClient(
**endpoint=endpoint, credential=AzureKeyCredential(key)
**)

poller = document_intelligence_client.**begin_analyze_document(
**"prebuilt-invoice", AnalyzeDocumentRequest(url_source=invoiceUrl)
**)

invoices = poller.**result()

Extract Figures from Documents Example

Used Classess:
from azure.core.credentials import **AzureKeyCredential
from azure.ai.documentintelligence import **DocumentIntelligenceClient
from azure.ai.documentintelligence.models import **AnalyzeOutputOption, AnalyzeResult

Used Methods:

document_intelligence_client = **DocumentIntelligenceClient(endpoint=endpoint, credential=AzureKeyCredential(key))

with open(path_to_sample_documents, "rb") as f:
poller = document_intelligence_client.**begin_analyze_document(
**"prebuilt-layout",
**analyze_request=f,
**output=[AnalyzeOutputOption.FIGURES],
**content_type="application/octet-stream",
**)
result: AnalyzeResult = poller.**result()

--------------------------------------------------------------------------------------------------------------------------------------------------------------

# Microsoft Foundry

Microsoft Foundry is a unified Azure platform-as-a-service (PaaS) for enterprise AI operations, model building, and application development. It combines production-grade infrastructure with developer-friendly interfaces, allowing developers to focus on building applications rather than managing infrastructure.

Microsoft Foundry unifies agents, models, and tools under a single management system with enterprise-readiness capabilities, including tracing, monitoring, evaluations, and customizable configurations. The platform provides streamlined management through unified Role-based Access Control (RBAC), networking, and policies under one Azure resource provider namespace.

Tip: Azure AI Foundry is now Microsoft Foundry.

## Python SDK

Create a Foundry Project Client (Base Setup)

This is the preferred way when using agents, evaluations, or governance.

openai_client = project.**get_openai_client(
**api_version="2024-10-01-preview"
**)

response = openai_client.**responses.create(
**model="gpt-4.1-mini",
**input="Explain the CAP theorem in simple terms."
)**

print(**response.output_text**)


Direct Azure OpenAI Call (Model-Only, No Foundry Features)

Use this when you only need inference, not agents or tools.

from openai import OpenAI
from azure.identity import DefaultAzureCredential, get_bearer_token_provider

token_provider = **get_bearer_token_provider(
**DefaultAzureCredential(),
**"https://cognitiveservices.azure.com/.default"
**)

client = **OpenAI(
**base_url="https://<YOUR-RESOURCE-NAME>.openai.azure.com/openai/v1/",
**api_key=token_provider
**)

response = client.**responses.create(
**model="gpt-4o-mini",
**input="Summarize this text in one sentence."
**)

print(**response.output_text**)

----------------------------------------------------------------------------------------------------------------------------------------------------------------

# Azure Language

Azure Language is a cloud-based Natural Language Processing (NLP) service for understanding and analyzing text. It integrates with Microsoft Foundry, REST APIs, client libraries, and AI agents. Capabilities are also available through the Azure Language MCP (Model Context Protocol) server, supporting both cloud-hosted and self-hosted deployments.

## Available Features

Azure Language unifies previously separate services:

**Text Analytics
**QnA Maker
**Language Understanding (LUIS)

## Preconfigured Features

Named Entity Recognition (NER) – Identifies and categorizes entities in text
PII / PHI Detection (Preview) – Detects and anonymizes sensitive personal and health data
Language Detection – Identifies the language and dialect of text
Sentiment Analysis & Opinion Mining – Determines sentiment and related opinions
Summarization – Extractive and abstractive summaries for text and conversations
Key Phrase Extraction – Identifies key concepts in unstructured text
Entity Linking (Retiring September 1, 2028) – Links entities to Wikipedia
Text Analytics for Health – Extracts and labels medical information

## Customizable Features

Custom Text Classification – Classifies documents into custom categories
Custom Named Entity Recognition (Custom NER) – Extracts custom entity types
Conversational Language Understanding (CLU) – Predicts intent and extracts information
Question Answering – Builds conversational AI applications
Orchestration Workflow – Connects CLU, LUIS, and Question Answering apps

## Maximum Characters per Document

Feature Type 	Limit
Text Analytics for Health 	**125,000 characters
Other synchronous features 	**5,120 characters
Other asynchronous features 	**125,000 characters total (max 25 documents)
Behavior When Exceeded

Synchronous: oversized documents are skipped; others still processed.
Asynchronous: any oversized document causes the entire request to fail (HTTP 400).

## Maximum Request Size

All preconfigured features: **1 MB per request

Maximum Documents per Request
Feature 	Max Documents
Conversation summarization 	**1
Language detection 	**1000
Sentiment analysis 	**10
Opinion mining 	**10
Key phrase extraction 	**10
NER 	**5
PII detection 	**5
Document summarization 	**25
Entity linking 	**5
Text Analytics for Health 	**25 (Web API), 1000 (Container)

Async requests always allow a maximum of **25 documents.


## Rate Limits (per feature, per pricing tier)
Tier 	Requests / Second 	Requests / Minute
S / Multi-service 	**1000 	1000
S0 / F0 	**100 	300				

## REST API

{Endpoint} : The Cognitive Services endpoint, including protocol and hostname (for example, **https://RESOURCE_NAME.cognitiveservices.azure.com**).

Analyze Conversations Endpoint

**POST** {Endpoint}/**language/:analyze-conversations?api-version=2024-11-01**

Analyze Text Endpoint

**POST** {Endpoint}/**language/:analyze-text?api-version=2025-11-01**

Analyze Text Input Types

Name 	Description
**AnalyzeTextEntityLinkingInput** 	Contains the analyze text entity linking input.
**AnalyzeTextEntityRecognitionInput** 	Represents the analyze text entity recognition task request.
**AnalyzeTextKeyPhraseExtractionInput** 	Contains the analyze text key phrase extraction task input.
**AnalyzeTextLanguageDetectionInput** 	Contains the language detection document analysis task input.
**AnalyzeTextPiiEntitiesRecognitionInput** 	Contains the analyze text PII entity recognition task input.
**AnalyzeTextSentimentAnalysisInput** 	Contains the analyze text sentiment analysis task input.

Question Answering - Get Answers Endpoint

Answers the specified question using your knowledge base.

**POST** {Endpoint}/**language/:query-knowledgebases?api-version=2023-04-01&projectName={projectName}&deploymentName={deploymentName}**

Question Answering - Get Answers From Text Endpoint

Answers the specified question using the provided text in the body.

**POST** {Endpoint}/**language/:query-text?api-version=2023-04-01**

## Python SDK

Custom Question Answering Example

Used Classes: 
from azure.core.credentials import **AzureKeyCredential
from azure.ai.language.questionanswering import **QuestionAnsweringClient
from azure.ai.language.questionanswering import **models as qna

Used Methods:

client = **QuestionAnsweringClient(endpoint, credential)
with client:
question="How long does it takes to charge a surface?"
input = **qna.AnswersFromTextOptions(
**question=question,
**text_documents=[
"Power and charging. It takes two to four hours to charge the Surface Pro 4 battery fully from an empty state. " +
"It can take longer if you're using your Surface for power-intensive activities like gaming or video streaming while you're charging it.",
"You can use the USB port on your Surface Pro 4 power supply to charge other devices, like a phone, while your Surface charges. " +
"The USB port on the power supply is only for charging, not for data transfer. If you want to use a USB device, plug it into the USB port on your Surface.",
**]
**)


output = **client.get_answers_from_text(input)
...


Entity Linking Example

Used Classes:
from azure.ai.textanalytics import **TextAnalyticsClient
from azure.core.credentials import **AzureKeyCredential

Used Methods:

ta_credential = **AzureKeyCredential(language_key)
text_analytics_client = **TextAnalyticsClient(
**endpoint=language_endpoint, 
**credential=ta_credential)

documents = ["""Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, 
to develop and sell BASIC interpreters for the Altair 8800. 
During his career at Microsoft, Gates held the positions of chairman,
chief executive officer, president and chief software architect, 
while also being the largest individual shareholder until May 2014."""]

result = **text_analytics_client.recognize_linked_entities(documents = documents)[0]

Language Detection

Used Classes:
from azure.ai.textanalytics import **TextAnalyticsClient
from azure.core.credentials import **AzureKeyCredential

Used Methods:
ta_credential = **AzureKeyCredential(language_key)
text_analytics_client = **TextAnalyticsClient(
**endpoint=language_endpoint,
**credential=ta_credential)


documents = ["Ce document est rédigé en Français."]
response = **text_analytics_client.detect_language(documents = documents, country_hint = 'us')[0]
print("Language: ", response.primary_language.name)

Key Phrase Extraction

Used Classes:
from azure.ai.textanalytics import **TextAnalyticsClient
from azure.core.credentials import **AzureKeyCredential

Used Methods:

ta_credential = **AzureKeyCredential(language_key)
text_analytics_client = **TextAnalyticsClient(
**endpoint=language_endpoint, 
**credential=ta_credential)

documents = ["Dr. Smith has a very modern medical office, and she has great staff."]

response = **text_analytics_client.extract_key_phrases(documents = documents)[0])

Detecting named entities (NER) Example

Used Classes:
from azure.ai.textanalytics import **TextAnalyticsClient
from azure.core.credentials import **AzureKeyCredential

Used Methods:
ta_credential = **AzureKeyCredential(language_key)
text_analytics_client = **TextAnalyticsClient(
**endpoint=language_endpoint, 
**credential=ta_credential)

documents = ["I had a wonderful trip to Seattle last week."]

result = **text_analytics_client.recognize_entities(documents = documents)[0]

Detect Personally Identifiable Information (PII) Example

Used Classes:
from azure.ai.textanalytics import **TextAnalyticsClient
from azure.core.credentials import **AzureKeyCredential

Used Methods:
ta_credential = **AzureKeyCredential(language_key)
text_analytics_client = **TextAnalyticsClient(
**endpoint=language_endpoint, 
**credential=ta_credential)

documents = [
"The employee's SSN is 859-98-0987.",
"The employee's phone number is 555-555-5555."
]

response = **text_analytics_client.recognize_pii_entities(documents, language="en")

result = [doc for doc in response if not doc.is_error]


Sentiment analysis and opinion mining Example

Used Classes:
from azure.ai.textanalytics import **TextAnalyticsClient
from azure.core.credentials import **AzureKeyCredential

Used Methods:

ta_credential = **AzureKeyCredential(language_key)
text_analytics_client = **TextAnalyticsClient(
**endpoint=language_endpoint, 
**credential=ta_credential)

documents = [
"The food and service were unacceptable. The concierge was nice, however."
]

result = **client.analyze_sentiment(documents, show_opinion_mining=True)

Text, document and conversation summarization Example

Used Classes:

from azure.ai.textanalytics import (**TextAnalyticsClient , ExtractiveSummaryAction)
from azure.core.credentials import **AzureKeyCredential

Used Methods:

ta_credential = **AzureKeyCredential(key)
text_analytics_client = **TextAnalyticsClient(
**endpoint=endpoint, 
**credential=ta_credential)

document = [
"The extractive summarization feature uses natural language processing techniques to locate key sentences in an unstructured text document. "
"These sentences collectively convey the main idea of the document. This feature is provided as an API for developers. " 
"They can use it to build intelligent solutions based on the relevant information extracted to support various use cases. "
"Extractive summarization supports several languages. It is based on pretrained multilingual transformer models, part of our quest for holistic representations. "
"It draws its strength from transfer learning across monolingual and harness the shared nature of languages to produce models of improved quality and efficiency. "
]

poller = text_analytics_client.**begin_analyze_actions(
**document,
**actions=[
**	ExtractiveSummaryAction(max_sentence_count=4)
**],
**)

document_results = poller.**result()


Text Analytics for health example

Used Classes:
from azure.ai.textanalytics import **TextAnalyticsClient
from azure.core.credentials import **AzureKeyCredential

Used Methods:

ta_credential = **AzureKeyCredential(key)
text_analytics_client = **TextAnalyticsClient(
**endpoint=endpoint, 
**credential=ta_credential)

documents = [
"""
Patient needs to take 50 mg of ibuprofen.
"""
]

poller = text_analytics_client.**begin_analyze_healthcare_entities(documents)
result = poller.result()

--------------------------------------------------------------------------------------------------------------------------------------------------------------

# Azure Speech Service

The Azure Speech service provides powerful speech-to-text, text-to-speech, speech translation, and AI voice conversation capabilities through a Speech resource. It supports high-accuracy transcription, natural-sounding neural voices, multilingual translation, and live conversational experiences.

Speech can run in the cloud or at the edge (via containers) and integrates easily using the Speech SDK, Speech CLI, and REST APIs. It supports many languages, regions, and pricing tiers.


## Core Capabilities

**Speech to Text

Real-time transcription for streaming audio
Fast transcription for recorded files
Batch transcription for large audio volumes
Support for custom speech models to improve accuracy in noisy or domain-specific audio

**Text to Speech

Neural voices with human-like quality
Fine-grained control using SSML
Standard voices (out of the box)
Custom neural voices for brand-specific experiences (private and unique)

**Speech Translation

Real-time multilingual translation
Supports speech-to-speech and speech-to-text scenarios

## What is SSML?

SSML is an XML-based language used to control how text is spoken in text-to-speech, such as pitch, speed, volume, pronunciation, and voice style.			


SSML Roles and Styles — Examples

```xml
<mstts:express-as style="cheerful">
I'm really happy to see you today!
</mstts:express-as>

<mstts:express-as role="SeniorMale" style="calm">
Welcome back. It’s good to see you again.
</mstts:express-as>
```

Style Degree Example

Adjust how strong the emotion sounds:

```xml
<mstts:express-as style="sad" styledegree="2">
I'm really going to miss you.
</mstts:express-as>
```

## Rest API

Speech to Text from file Endpoint

curl --location --request **POST** "https://%SPEECH_REGION%.**stt**.speech.microsoft.com/**speech/recognition/conversation/cognitiveservices/v1?language=en-US&format=detailed" ^
--header "Ocp-Apim-Subscription-Key: %SPEECH_KEY%" ^
--header "Content-Type: audio/wav" ^
--data-binary "@YourAudioFile.wav"


Text to Speech to a file Endpoint

curl --location --request **POST** "https://%SPEECH_REGION%.**tts**.speech.microsoft.com/**cognitiveservices/v1" ^
--header "Ocp-Apim-Subscription-Key: %SPEECH_KEY%" ^
--header "Content-Type: application/ssml+xml" ^
--header "X-Microsoft-OutputFormat: audio-16khz-128kbitrate-mono-mp3" ^
--header "User-Agent: curl" ^
--data-raw "**<speak version='1.0' xml:lang='en-US'><voice xml:lang='en-US' xml:gender='Female' name='en-US-Ava:DragonHDLatestNeural'>my voice is my passport verify me</voice></speak>**" --output output.mp3



## Python SDK

Speech to Text Example

Used Classes:
import **azure.cognitiveservices.speech** as speechsdk

Used Methods:

speech_config = **speechsdk.SpeechConfig(subscription=os.environ.get('SPEECH_KEY'), endpoint=os.environ.get('ENDPOINT'))
**speech_config.speech_recognition_language="en-US"

audio_config = **speechsdk.audio.AudioConfig(use_default_microphone=True)
speech_recognizer = **speechsdk.SpeechRecognizer(speech_config=speech_config, audio_config=audio_config)

print("Speak into your microphone.")
speech_recognition_result = **speech_recognizer.recognize_once_async().get()

Text to Speech Example

Used Classes:
import **azure.cognitiveservices.speech** as speechsdk

Used Methods:

# This example requires environment variables named "SPEECH_KEY" and "ENDPOINT"
# Replace with your own subscription key and endpoint, the endpoint is like : "https://YourServiceRegion.api.cognitive.microsoft.com"
speech_config = **speechsdk.SpeechConfig(subscription=os.environ.get('SPEECH_KEY'), endpoint=os.environ.get('ENDPOINT'))
audio_config = **speechsdk.audio.AudioOutputConfig(use_default_speaker=True)

# The neural multilingual voice can speak different languages based on the input text.
speech_config.**speech_synthesis_voice_name='en-US-Ava:DragonHDLatestNeural'

speech_synthesizer = **speechsdk.SpeechSynthesizer(speech_config=speech_config, audio_config=audio_config)

# Get text from the console and synthesize to the default speaker.
print("Enter some text that you want to speak >")
text = input()

speech_synthesis_result = **speech_synthesizer.speak_text_async(text).get()

Speech translation

Used Classes:
import **azure.cognitiveservices.speech** as speechsdk


Used Methods:

# This example requires environment variables named "SPEECH_KEY" and "ENDPOINT"
# Replace with your own subscription key and endpoint, the endpoint is like : "https://YourServiceRegion.api.cognitive.microsoft.com"
speech_translation_config = **speechsdk.translation.SpeechTranslationConfig(subscription=os.environ.get('SPEECH_KEY'), endpoint=os.environ.get('ENDPOINT'))
**speech_translation_config.speech_recognition_language="en-US"

to_language ="it"
**speech_translation_config.add_target_language(to_language)

audio_config = **speechsdk.audio.AudioConfig(use_default_microphone=True)
translation_recognizer = **speechsdk.translation.TranslationRecognizer(translation_config=speech_translation_config, audio_config=audio_config)

print("Speak into your microphone.")
translation_recognition_result = **translation_recognizer.recognize_once_async().get()

---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

# Azure Translator

Azure Translator in Foundry Tools is a cloud-based neural machine translation service within the Microsoft Foundry Tools ecosystem. It enables developers to build intelligent, multilingual applications across all supported languages and operating systems. The service is widely used across Microsoft products and by enterprises worldwide for reliable language translation and related language-processing tasks.


## Key Capabilities

**Text Translation
Supports real-time multilingual translation.
Latest 2025-10-01-preview introduces optional large language model (LLM) selection, adaptive custom translation, and enhanced request parameters.
Available via REST APIs, SDKs, the Foundry (classic) portal, and containers.

**Document Translation
Asynchronous (Batch): Translates large or complex document sets while preserving structure and formatting, using Azure Blob Storage.
Synchronous (Single file): Translates individual documents (optionally with glossaries) without requiring Blob Storage, returning results directly.

**Custom Translator
Enables creation of customized translation models for domain- or industry-specific terminology, style, and language usage.
Supports custom dictionaries for phrases or sentences.


## Asynchronous (Batch) Document Translation: Supported document and glossary formats

Batch document formats
Supports common document types including:
**PDF (with OCR for scanned PDFs)
**Microsoft Office files (Word, Excel, PowerPoint, Outlook)
**OpenDocument formats
**Markdown
**HTML and MHTML
**Images (JPEG, PNG, BMP, WebP – preview)
**CSV, TSV/TAB
**TXT and RTF
**XLIFF

Legacy format handling
Some source formats are converted during translation:
**.doc, .odt, .rtf → .docx
**.xls, .ods → .xlsx
**.ppt, .odp → .pptx

Glossary formats
Supported glossary file types:
**CSV
**TSV/TAB
**XLIFF (XLF, XLIFF)

## Synchronous document and glossary formats

Synchronous document formats
Supported file types include:
**Plain text (.txt)
**Tab-separated values (.tsv, .tab)
**Comma-separated values (.csv)
**HTML (.html, .htm)
**MHTML (.mhtml, .mht)
**Microsoft Office formats:
**PowerPoint (.pptx)
**Excel (.xlsx)
**Word (.docx)
**Outlook messages (.msg)
**XML Localization Interchange formats (.xlf, .xliff)


## Text Translation Limitations

Billing: Based on character count, not request frequency.
Request limit: Each translate request can include up to 50,000 characters across all target languages.
Example: 3,000 characters to 3 languages = 9,000 characters counted.

## Array Element and Character Limits per Operation
| Operation              | Max Array Element Size                        | Max Array Elements | Max Request Size (characters) |
|------------------------|-----------------------------------------------|--------------------|-------------------------------|
| Translate              | **50,000                                        | 1,000              | 50,000                        |
| Transliterate          | **5,000                                         | 10                 | 5,000                         |
| Detect                 | **50,000                                        | 100                | 50,000                        |
| BreakSentence          | **50,000                                        | 100                | 50,000                        |
| Dictionary Lookup      | **100                                           | 10                 | 1,000                         |
| Dictionary Examples    | **100 text + 100 translation (200 total)        | 10                 | 2,000                         |


## REST API

Languages List Endpoint

Returns a list of languages supported by Translate, Transliterate, and Dictionary Lookup operations. This request doesn't require authentication; just copy and paste the following GET request into your favorite REST API tool or browser:

**https://api.cognitive.microsofttranslator.com/languages?api-version=3.0

Translate to multiple languages Endpoint

curl -X **POST** "**https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=zh-Hans&to=de**" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'Hello, what is your name?'}]"


Transliterate Endpoint

The Text transliteration API maps your source language script or alphabet to a target language script or alphabet.

**POST https://api.cognitive.microsofttranslator.com/transliterate?api-version=3.0

Detect Endpoint

**POST https://api.cognitive.microsofttranslator.com/detect?api-version=3.0

Dictionary Lookup Endpoint

This example shows how to look up alternative translations in Spanish of the English term fly

curl -X **POST "https://api.cognitive.microsofttranslator.com/dictionary/lookup?api-version=3.0&from=en&to=es"** -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'fly'}]"

Dictionary Examples Endpoint

Provides examples that show how terms in the dictionary are used in context. This operation is used in tandem with Dictionary lookup.

curl -X **POST "https://api.cognitive.microsofttranslator.com/dictionary/examples?api-version=3.0&from=en&to=es"** -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'fly', 'Translation':'volar'}]"

Translate Document (Asynchron)

**POST https://{your-document-translation-endpoint}/translator/document:translate?api-version=2024-05-01&sourceLanguage=en&targetLanguage=fr

## Python SDK

Used Classes: 

from azure.ai.translation.text import **TextTranslationClient, TranslatorCredential
from azure.ai.translation.text.models import **InputTextItem

Used Methods:

credential = **TranslatorCredential(key, region)
text_translator = **TextTranslationClient(endpoint=endpoint, credential=credential)

source_language = "en"
target_languages = ["es", "it"]
input_text_elements = [ InputTextItem(text = "This is a test") ]

response = **text_translator.translate(content = input_text_elements, to = target_languages, from_parameter = source_language)
translation = response[0] if response else None

if translation:
for translated_text in translation.translations:
print(f"Text was translated to: '{translated_text.to}' and the result is: '{translated_text.text}'.")


------------------------------------------------------------------------------------------------------------------------------------------------------------------

# Azure AI Video Indexer

Azure AI Video Indexer is a full-featured video and audio intelligence service that applies advanced machine learning and generative AI to extract structured insights from both live and recorded media. It supports transcription, translation, object and face detection, summarization, scene understanding, sentiment analysis, and metadata generation. The service is designed for enterprise use, offering flexible deployment models, deep multimodal analytics, and strong governance and compliance controls.

## Core AI Capabilities

### Video Intelligence

Face detection and grouping, celebrity recognition, and account-based face identification
Object detection and tracking, observed people detection with bounding boxes and timestamps
OCR for extracting on-screen text (signs, products, logos)
Scene, shot, and keyframe detection for navigation and editing
Visual moderation, logo and slate detection, editorial shot tagging

### Audio Intelligence

Speech-to-text transcription in 50+ languages
Automatic language detection and multilingual transcription
Speaker diarization and speaker statistics
Closed caption generation (VTT, TTML, SRT)
Emotion, sentiment, and acoustic event detection (sirens, applause, gunshots, silence)
Custom speech models for domain-specific vocabularies

### Multimodal & NLP Insights

Keyword extraction and named entity recognition (people, brands, locations)
Topic inference using multiple ontologies
Sentiment analysis across speech and on-screen text
Artifacts and rich metadata for downstream analytics and applications


------------------------------------------------------------------------------------------------------------------------------------------------------------------------


# Azure Vision

Azure Vision provides advanced AI algorithms for analyzing images and extracting meaningful information based on visual features. It enables developers to process images and return structured insights such as text, objects, faces, and descriptive metadata.

Azure Vision is commonly used for document processing, visual understanding, and identity-related scenarios, and is accessible through SDKs, APIs, and Vision Studio.


## Core Services
Optical Character Recognition (OCR)

Extracts printed and handwritten text from images and documents
Uses deep learning models for high accuracy
Works across diverse surfaces such as invoices, receipts, posters, business cards, and whiteboards
Supports multiple languages
Implemented using the Read API

Image Analysis

Detects visual features such as:
Objects
Faces
Adult content
Auto-generated image descriptions
Designed for general-purpose image understanding

Face

Detects, recognizes, and analyzes human faces in images
Supports use cases such as:
Identity verification
Touchless access control
Face blurring for privacy

## Image Requirements

To be analyzed by Azure Vision, **images** must:

Be in **JPEG, PNG, GIF, or BMP format
Be smaller than **4 MB
Have dimensions **greater than 50 × 50 pixels
For the **Read** (OCR) API: **50 × 50 up to 10,000 × 10,000 pixels


## REST API

{endpoint} : The Cognitive Services endpoint, including protocol and hostname (for example, **https://RESOURCE_NAME.cognitiveservices.azure.com**).

Face Detection Operations - Detect Endpoint

**POST {endpoint}/face/{apiVersion}/detect


Face Recognition Operations - Find Similar From Large Face List Endpoint

**POST {endpoint}/face/{apiVersion}/findsimilars

Face Recognition Operations - Verify Face To Face Endpoint

Verify whether two faces belong to a same person.

**POST {endpoint}/face/{apiVersion}/verify

Face Recognition Operations - Identify From Large Person Group Endpoint

**POST {endpoint}/face/{apiVersion}/identify

Image Analysis - Analyze Image Endpoint

**POST /imageanalysis:analyze?api-version=2023-04-01-preview

OCR Read Endpoint

**POST {Endpoint}/vision/v3.1/read/analyze

Tag Image Endpoint

This operation generates a list of words, or tags, that are relevant to the content of the supplied image. The Computer Vision API can return tags based on objects, living beings, scenery or actions found in images.

**POST {Endpoint}/vision/v3.1/tag

Detect Objects Endpoint

Performs object detection on the specified image. Two input methods are supported -- (1) Uploading an image or (2) specifying an image URL.

**POST {Endpoint}/vision/v3.1/detect

## Python SDK

OCR Read (v3.2 GA)Example

Used Classes: 
from azure.cognitiveservices.vision.computervision import **ComputerVisionClient
from azure.cognitiveservices.vision.computervision.models import **OperationStatusCodes
from azure.cognitiveservices.vision.computervision.models import **VisualFeatureTypes
from msrest.authentication import **CognitiveServicesCredentials

Used Methods: 

# Get an image with text
read_image_url = "https://learn.microsoft.com/azure/ai-services/computer-vision/media/quickstarts/presentation.png"

# Call API with URL and raw response (allows you to get the operation location)
read_response = **computervision_client.read(read_image_url,  raw=True)

# Get the operation location (URL with an ID at the end) from the response
read_operation_location = **read_response.headers["Operation-Location"]
# Grab the ID from the URL
operation_id = **read_operation_location.split("/")[-1]

# Call the "GET" API and wait for it to retrieve the results 
while True:
read_result = **computervision_client.get_read_result(operation_id)
if read_result.status not in ['notStarted', 'running']:
break
time.sleep(1)

# Print the detected text, line by line
if read_result.status == OperationStatusCodes.succeeded:
for text_result in read_result.analyze_result.read_results:
for line in text_result.lines:
print(line.text)
print(line.bounding_box)
print()

Image Analysis (4.0) Example

Used Classes: 
from azure.ai.vision.imageanalysis import **ImageAnalysisClient
from azure.ai.vision.imageanalysis.models import **VisualFeatures
from azure.core.credentials import **AzureKeyCredential
Used Methods :

# Create an Image Analysis client
client = **ImageAnalysisClient(
**endpoint=endpoint,
**credential=AzureKeyCredential(key)
**)

# Get a caption for the image. This will be a synchronously (blocking) call.
result = **client.analyze_from_url(
**image_url="https://learn.microsoft.com/azure/ai-services/computer-vision/media/quickstarts/presentation.png",
**visual_features=[VisualFeatures.CAPTION, VisualFeatures.READ],
**gender_neutral_caption=True,  # Optional (default is False)
**)


Face Example

Used Classes: 

from azure.core.credentials import **AzureKeyCredential
from azure.ai.vision.face import **FaceAdministrationClient, FaceClient
from azure.ai.vision.face.models import **FaceAttributeTypeRecognition04, FaceDetectionModel, FaceRecognitionModel, QualityForRecognition

Used Methods :

with **FaceAdministrationClient(endpoint=ENDPOINT, credential=AzureKeyCredential(KEY))** as face_admin_client, \
**FaceClient(endpoint=ENDPOINT, credential=AzureKeyCredential(KEY))** as face_client:

# Create empty Large Person Group. Large Person Group ID must be lower case, alphanumeric, and/or with '-', '_'.					
face_admin_client.**large_person_group.create(
**large_person_group_id=LARGE_PERSON_GROUP_ID,
**name=LARGE_PERSON_GROUP_ID,
**recognition_model=FaceRecognitionModel.RECOGNITION04,
**)


# Define woman friend
woman = face_admin_client.**large_person_group.create_person(
****large_person_group_id=LARGE_PERSON_GROUP_ID,
**name="Woman",
**)
# Define man friend
man = face_admin_client.**large_person_group.create_person(
**large_person_group_id=LARGE_PERSON_GROUP_ID,
**name="Man",
**)
# Define child friend
child = face_admin_client.**large_person_group.create_person(
**large_person_group_id=LARGE_PERSON_GROUP_ID,
**name="Child",
**)

detected_faces = face_client.**detect_from_url(
**url=image,
**detection_model=FaceDetectionModel.DETECTION03,
**recognition_model=FaceRecognitionModel.RECOGNITION04,
**return_face_id=True,
**return_face_attributes=[FaceAttributeTypeRecognition04.QUALITY_FOR_RECOGNITION],
**)

face_admin_client.**large_person_group.add_face_from_url(
**large_person_group_id=LARGE_PERSON_GROUP_ID,
**person_id=woman.person_id,
**url=image,
**detection_model=FaceDetectionModel.DETECTION03,
**)

--------------------------------------------------------------------------------------------------------------------------------------------------------

# Choose the right Service for Documents Processing

## Purpose

This guide compares three Azure AI options for Intelligent Document Processing (IDP) to help organizations choose the best tool for managing documents and unstructured data:

Document Intelligence
Content Understanding
Azure-hosted LLMs (Foundry models)

## The Three Options at a Glance
**1. Document Intelligence

Best for: Standard, structured documents (forms, invoices, IDs)

Strengths

Industry-leading OCR and layout extraction
High accuracy with confidence scores and grounding
Prebuilt and custom models for known schemas

Limitations

Limited semantic understanding
Less flexible for highly varied or unstructured documents

**2. Content Understanding (Recommended for most cases)

Best for: Complex, high-variation, multimodal, or unstructured documents

Strengths

Multimodal input (documents, images, audio, video)
Zero-shot schema setup (no labeling required initially)
Supports inferred fields, enrichments, reasoning, validation
Built-in confidence scores, grounding, chunking, and normalization
Handles large and highly varied documents

Key Features

Updated OCR and layout (multi-page tables, hyperlinks)
Inferred fields and metadata generation
Multi-file processing and reasoning (preview)
Token-based pricing and enterprise security

**3. Build Your Own with Foundry Models

Best for: Highly customized or experimental AI workflows

Strengths

Maximum control over models, prompts, and workflows
Flexible integration with GPT, Whisper, embeddings

**Challenges

Requires heavy engineering
No built-in confidence scores or grounding
Manual scaling, chunking, validation, and cost management

## Capability Comparison (Highlights)

OCR & Layout: All supported, but managed services handle preprocessing automatically
Confidence & Grounding: Built-in for Document Intelligence and Content Understanding only
Inferred Fields & Metadata: Supported by Content Understanding and custom builds
Ease of Use:
Document Intelligence: requires labeling/training for custom models
Content Understanding: simplest, schema-first with zero labeling
Custom build: requires prompt engineering and orchestration

## Scenarios

Scenario-Based Recommendations
Scenario 1: Single, standardized forms

Recommended: Content Understanding or Document Intelligence
Scenario 2: Few known variants

Recommended: Content Understanding
Alternative: Document Intelligence with custom models
Scenario 3: High-variation semi-structured documents

Recommended: Content Understanding
Scenario 4: Unstructured narrative documents (contracts, reports)

Recommended: Content Understanding
Alternative: Custom solution (high effort, no built-in confidence)
Scenario 5: Multi-document & mixed media workflows

Recommended: Content Understanding (Pro mode, preview)
Alternative: Custom agentic solution


## When choosing a tool, consider:

Straight Through Processing (automation rate)
Latency requirements
Accuracy and confidence scoring
Continuous improvement & scalability
Complexity of extraction and inference
Engineering effort and total cost of ownership

## Final Guidance

Start with Content Understanding for most IDP scenarios — it fully covers Document Intelligence use cases and extends them with multimodal processing, inference, reasoning, and post-processing.
Use Document Intelligence for simple, structured, low-latency extraction with proven accuracy.
Use Foundry models (custom builds) only when you need maximum flexibility and are willing to invest heavily in engineering, validation, and cost control.

Bottom line:

Content Understanding is the recommended default for modern, scalable, and intelligent document processing in Azure Foundry.



