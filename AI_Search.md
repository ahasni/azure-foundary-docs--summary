# Azure AI Search — Summary

## Overview
**Azure AI Search** is a fully managed, cloud-hosted search service that connects enterprise and web data to AI applications. It enables agents, chatbots, and large language models (LLMs) to generate reliable, grounded answers using indexed and remote content, contextual signals, and chat history.

It supports both traditional search experiences and modern **retrieval-augmented generation (RAG)** through **agentic retrieval**.

---

## Key Capabilities
- **Two search engines**
  - **Classic search** for low-latency, predictable queries
  - **Agentic retrieval** for complex, multi-step, LLM-assisted retrieval
- **Query types**: full-text, vector, hybrid, and multimodal
- **AI enrichment** for chunking, embedding, and transforming content
- **Relevance tuning** to improve result quality
- **Enterprise-grade security, compliance, and scalability**
- **Native integrations** with Azure OpenAI, Microsoft Foundry, and Azure data platforms

---

## Why Use Azure AI Search?
- Ground AI agents and chatbots in proprietary and enterprise data
- Access data from sources like Azure Blob Storage, Cosmos DB, SharePoint, and OneLake
- Combine keyword and vector search for better precision and recall
- Search text and images together using multimodal pipelines
- Implement advanced search features such as filters, facets, synonyms, autocomplete, and geo-search
- Secure access with Microsoft Entra, role-based access control, and private networking
- Operate reliably at scale with Azure monitoring and automation tools

---

## Classic Search
**Classic search** is an index-first retrieval model optimized for speed and simplicity.

### Characteristics
- Single request–response query model
- Targets one predefined search index
- No LLM planning or iterative reasoning
- Ideal for apps requiring predictable, low-latency results

### Workloads
- **Indexing**: Ingests JSON documents, applies AI enrichment, stores text and vectors
- **Querying**: Executes full-text, vector, hybrid, multimodal, and filtered searches

---

## Agentic Retrieval
**Agentic retrieval** is designed for advanced, agent-to-agent and RAG workflows.

### Characteristics
- Uses a **knowledge base** that abstracts how retrieval is performed
- Supports multi-step query planning and parallel retrieval
- Can query indexed and remote sources
- Produces responses optimized for agent consumption

### How It Works
- Query planning and decomposition
- Parallel retrieval across knowledge sources
- Semantic reranking and result merging
- Optional LLM-assisted answer synthesis

---

## Classic Search vs. Agentic Retrieval

| Aspect | Classic Search | Agentic Retrieval |
|------|---------------|------------------|
| Search target | Search index | Knowledge base |
| Query model | Single request | Multi-step, planned |
| LLM involvement | None | Optional / integrated |
| Response | Ranked documents | Synthesized answers or structured results |
| Region restrictions | No | Yes |
| Availability | Generally available | Public preview |

---

## Getting Started
Azure AI Search can be accessed through:
- **Azure Portal** for setup and prototyping
- **REST APIs**
- **SDKs** for .NET, Java, JavaScript, and Python

The portal supports managing indexes, indexers, skillsets, knowledge bases, and data sources, while APIs and SDKs are recommended for production automation.

---

## Summary
Azure AI Search is a flexible, enterprise-ready retrieval platform that supports both traditional search and AI-powered retrieval. It enables scalable, secure, and intelligent access to data for applications, agents, and chatbots—making it a foundational service for modern RAG and AI-driven experiences.

# REST API

## Azure AI Search REST API Reference

**Azure AI Search** (formerly Azure Cognitive Search) is a fully managed cloud search service that provides information retrieval over **user-owned content**.

- **Data plane REST APIs** are used for indexing and query workflows (covered in this reference).
- **Control plane operations** for service administration are documented separately in the **Search Management REST API**.

---

## Versioned API Documentation

- A **version selector** appears above the table of contents when viewing API reference articles.
- The selector becomes available when navigating to pages under **Reference > Data Plane**.
- Each request must specify a supported `api-version`.

---

## Key Concepts

Azure AI Search is built around the following core concepts:

- **Search Service**  
  Hosts indexes, indexers, data sources, skillsets, and synonym maps as top-level objects.

- **Indexes**  
  Provide persistent storage of searchable documents. Index fields define structure, data types, and search behavior.

- **Documents**  
  The data stored in an index. Conceptually:
  - Index ≈ Table
  - Document ≈ Row  
  Documents are accessed via `/indexes/{index-name}/docs`.

- **Indexers**  
  Automate data ingestion by reading from external data sources and serializing content into JSON documents.

- **Data Sources**  
  Define connection information for external data used by indexers.

- **Skillsets**  
  Add AI enrichment and vectorization to indexing pipelines using built-in or custom skills such as:
  - OCR
  - Entity recognition
  - Key phrase extraction
  - Text chunking
  - Feature inference

- **Synonym Maps**  
  Service-level objects containing user-defined synonyms that can be applied to searchable fields.

---

## Search Service Objects

| Object        | Description |
|--------------|-------------|
| **Data sources** | Connections used by indexers to retrieve and refresh data |
| **Documents** | Searchable entities stored in an index |
| **Indexes** | Schema definitions and storage for search documents |
| **Indexers** | Automation components that move data into indexes |
| **Skillsets** | AI enrichment pipelines applied during indexing |
| **Synonym maps** | User-defined synonyms applied to search fields |

---

## Permissions and Access Control

Azure AI Search supports two authentication methods:

### Key-Based Authentication
- Uses API keys generated by the search service
- **Admin API Key**: Read-write access
- **Query API Key**: Read-only access to documents

### Microsoft Entra ID (Role-Based Access Control)
- Uses bearer tokens for authorization
- Requires a Microsoft Entra ID tenant
- Supports built-in and custom roles
- Applies to **data plane operations**
- Control plane operations always use role-based authentication

You can disable either authentication method depending on your security requirements.

---

## Calling the REST APIs

When calling Azure AI Search REST APIs, note the following requirements:

- Requests must use **HTTPS** (port 443)
- The request URI must include a supported `api-version`  
  Example:  
  `https://{search-service-name}.search.windows.net/indexes?api-version=2024-07-01`
- Requests must include:
  - An `api-key` **or**
  - A bearer token in the `Authorization` header
- If `Content-Type` is not specified, it defaults to `application/json`

---

## Summary

Azure AI Search REST APIs enable developers to build powerful search solutions by combining structured indexing, AI enrichment, and secure access control. The data plane APIs focus on indexing and querying, while enterprise-grade security and extensibility make Azure AI Search suitable for production workloads.

# REST API

## Get Indexes List Endpoint
```http
@baseUrl = PUT-YOUR-SEARCH-SERVICE-ENDPOINT-HERE
@token = PUT-YOUR-PERSONAL-IDENTITY-TOKEN-HERE

### List existing indexes by name
GET {{baseUrl}}/indexes?api-version=2025-09-01  HTTP/1.1
    Authorization: Bearer {{token}}
```
## Create a Search Index Endpoint
```http
### Create a new index
POST {{baseUrl}}/indexes?api-version=2025-09-01  HTTP/1.1
    Content-Type: application/json
    Authorization: Bearer {{token}}

    {
        "name": "hotels-quickstart",  
        "fields": [
            {"name": "HotelId", "type": "Edm.String", "key": true, "filterable": true},
            {"name": "HotelName", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": true, "facetable": false},
            {"name": "Description", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false, "analyzer": "en.lucene"},
            {"name": "Category", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true},
            {"name": "Tags", "type": "Collection(Edm.String)", "searchable": true, "filterable": true, "sortable": false, "facetable": true},
            {"name": "ParkingIncluded", "type": "Edm.Boolean", "filterable": true, "sortable": true, "facetable": true},
            {"name": "LastRenovationDate", "type": "Edm.DateTimeOffset", "filterable": true, "sortable": true, "facetable": true},
            {"name": "Rating", "type": "Edm.Double", "filterable": true, "sortable": true, "facetable": true},
            {"name": "Address", "type": "Edm.ComplexType", 
                "fields": [
                {"name": "StreetAddress", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "searchable": true},
                {"name": "City", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true},
                {"name": "StateProvince", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true},
                {"name": "PostalCode", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true},
                {"name": "Country", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true}
                ]
            }
        ]
    }
```

## Load the Index Endpoint
Newly created indexes are empty. To populate an index and make it searchable, you must upload JSON documents that conform to the index schema.
```http
### Upload documents
POST {{baseUrl}}/indexes/hotels-quickstart/docs/index?api-version=2025-09-01  HTTP/1.1
    Content-Type: application/json
    Authorization: Bearer {{token}}

    {
        "value": [
        {
        "@search.action": "upload",
        "HotelId": "1",
        "HotelName": "Stay-Kay City Hotel",
        "Description": "This classic hotel is fully-refurbished and ideally located on the main commercial artery of the city in the heart of New York. A few minutes away is Times Square and the historic centre of the city, as well as other places of interest that make New York one of America's most attractive and cosmopolitan cities.",
        "Category": "Boutique",
        "Tags": [ "view", "air conditioning", "concierge" ],
        "ParkingIncluded": false,
        "LastRenovationDate": "2022-01-18T00:00:00Z",
        "Rating": 3.60,
        "Address": 
            {
            "StreetAddress": "677 5th Ave",
            "City": "New York",
            "StateProvince": "NY",
            "PostalCode": "10022",
            "Country": "USA"
            } 
        },
        {
        "@search.action": "upload",
        "HotelId": "2",
        "HotelName": "Old Century Hotel",
        "Description": "The hotel is situated in a nineteenth century plaza, which has been expanded and renovated to the highest architectural standards to create a modern, functional and first-class hotel in which art and unique historical elements coexist with the most modern comforts. The hotel also regularly hosts events like wine tastings, beer dinners, and live music.",
         "Category": "Boutique",
        "Tags": [ "pool", "free wifi", "concierge" ],
        "ParkingIncluded": false,
        "LastRenovationDate": "2019-02-18T00:00:00Z",
        "Rating": 3.60,
        "Address": 
            {
            "StreetAddress": "140 University Town Center Dr",
            "City": "Sarasota",
            "StateProvince": "FL",
            "PostalCode": "34243",
            "Country": "USA"
            } 
        },
        {
        "@search.action": "upload",
        "HotelId": "3",
        "HotelName": "Gastronomic Landscape Hotel",
        "Description": "The Gastronomic Hotel stands out for its culinary excellence under the management of William Dough, who advises on and oversees all of the Hotel's restaurant services.",
        "Category": "Suite",
        "Tags": [ "restaurant", "bar", "continental breakfast" ],
        "ParkingIncluded": true,
        "LastRenovationDate": "2015-09-20T00:00:00Z",
        "Rating": 4.80,
        "Address": 
            {
            "StreetAddress": "3393 Peachtree Rd",
            "City": "Atlanta",
            "StateProvince": "GA",
            "PostalCode": "30326",
            "Country": "USA"
            } 
        },
        {
        "@search.action": "upload",
        "HotelId": "4",
        "HotelName": "Sublime Palace Hotel",
        "Description": "Sublime Palace Hotel is located in the heart of the historic center of Sublime in an extremely vibrant and lively area within short walking distance to the sites and landmarks of the city and is surrounded by the extraordinary beauty of churches, buildings, shops and monuments. Sublime Cliff is part of a lovingly restored 19th century resort, updated for every modern convenience.",
        "Category": "Luxury",
        "Tags": [ "concierge", "view", "air conditioning" ],
        "ParkingIncluded": true,
        "LastRenovationDate": "2020-02-06T00:00:00Z",
        "Rating": 4.60,
        "Address": 
            {
            "StreetAddress": "7400 San Pedro Ave",
            "City": "San Antonio",
            "StateProvince": "TX",
            "PostalCode": "78216",
            "Country": "USA"
            }
        }
      ]
    }
```
## Query (search) the Iindex Endpoint
Now that documents are loaded into your index, you can use full-text search to find specific terms or phrases within their fields.
```http
### Run a query
POST {{baseUrl}}/indexes/hotels-quickstart/docs/search?api-version=2025-09-01  HTTP/1.1
  Content-Type: application/json
  Authorization: Bearer {{token}}

  {
      "search": "attached restaurant",
      "select": "HotelId, HotelName, Tags, Description",
      "searchFields": "Description, Tags",
      "count": true
  }
```
Response Body:
```json
{
  "@odata.context": "https://my-service.search.windows.net/indexes('hotels-quickstart')/$metadata#docs(*)",
  "@odata.count": 1,
  "value": [
    {
      "@search.score": 0.5575875,
      "HotelId": "3",
      "HotelName": "Gastronomic Landscape Hotel",
      "Description": "The Gastronomic Hotel stands out for its culinary excellence under the management of William Dough, who advises on and oversees all of the Hotel\u2019s restaurant services.",
      "Tags": [
        "restaurant",
        "bar",
        "continental breakfast"
      ]
    }
  ]
}
```

# Python SDK

```bash
pip install azure-identity azure-search-documents
```

## Connect to a search service Example


```python
from azure.identity import DefaultAzureCredential
from azure.search.documents.indexes import SearchIndexClient

service_endpoint = "PUT-YOUR-SEARCH-SERVICE-ENDPOINT-HERE"
credential = DefaultAzureCredential()
client = SearchIndexClient(endpoint = service_endpoint, credential = credential)

# List existing indexes
indexes = client.list_indexes()

for index in indexes:
   index_dict = index.as_dict()
   print(json.dumps(index_dict, indent = 2))
   
```

## Agentic Retrieval (RAG) Example 

```python
from azure.identity import DefaultAzureCredential, get_bearer_token_provider
from azure.search.documents.indexes.models import SearchIndex, SearchField, VectorSearch, VectorSearchProfile, HnswAlgorithmConfiguration, AzureOpenAIVectorizer, AzureOpenAIVectorizerParameters, SemanticSearch, SemanticConfiguration, SemanticPrioritizedFields, SemanticField, SearchIndexKnowledgeSource, SearchIndexKnowledgeSourceParameters, SearchIndexFieldReference, KnowledgeBase, KnowledgeBaseAzureOpenAIModel, KnowledgeSourceReference, KnowledgeRetrievalOutputMode, KnowledgeRetrievalLowReasoningEffort
from azure.search.documents.indexes import SearchIndexClient
from azure.search.documents import SearchIndexingBufferedSender
from azure.search.documents.knowledgebases import KnowledgeBaseRetrievalClient
from azure.search.documents.knowledgebases.models import KnowledgeBaseRetrievalRequest, KnowledgeBaseMessage, KnowledgeBaseMessageTextContent, SearchIndexKnowledgeSourceParams
import requests
import json

# Define variables
search_endpoint = "PUT-YOUR-SEARCH-SERVICE-URL-HERE"
aoai_endpoint = "PUT-YOUR-AOAI-FOUNDRY-URL-HERE"
aoai_embedding_model = "text-embedding-3-large"
aoai_embedding_deployment = "text-embedding-3-large"
aoai_gpt_model = "gpt-5-mini"
aoai_gpt_deployment = "gpt-5-mini"
index_name = "earth-at-night"
knowledge_source_name = "earth-knowledge-source"
knowledge_base_name = "earth-knowledge-base"
search_api_version = "2025-11-01-preview"

credential = DefaultAzureCredential()
token_provider = get_bearer_token_provider(credential, "https://search.azure.com/.default")

# Create an index
azure_openai_token_provider = get_bearer_token_provider(credential, "https://cognitiveservices.azure.com/.default")

index = SearchIndex(
    name=index_name,
    fields=[
        SearchField(name="id", type="Edm.String", key=True, filterable=True, sortable=True, facetable=True),
        SearchField(name="page_chunk", type="Edm.String", filterable=False, sortable=False, facetable=False),
        SearchField(name="page_embedding_text_3_large", type="Collection(Edm.Single)", stored=False, vector_search_dimensions=3072, vector_search_profile_name="hnsw_text_3_large"),
        SearchField(name="page_number", type="Edm.Int32", filterable=True, sortable=True, facetable=True)
    ],
    vector_search=VectorSearch(
        profiles=[VectorSearchProfile(name="hnsw_text_3_large", algorithm_configuration_name="alg", vectorizer_name="azure_openai_text_3_large")],
        algorithms=[HnswAlgorithmConfiguration(name="alg")],
        vectorizers=[
            AzureOpenAIVectorizer(
                vectorizer_name="azure_openai_text_3_large",
                parameters=AzureOpenAIVectorizerParameters(
                    resource_url=aoai_endpoint,
                    deployment_name=aoai_embedding_deployment,
                    model_name=aoai_embedding_model
                )
            )
        ]
    ),
    semantic_search=SemanticSearch(
        default_configuration_name="semantic_config",
        configurations=[
            SemanticConfiguration(
                name="semantic_config",
                prioritized_fields=SemanticPrioritizedFields(
                    content_fields=[
                        SemanticField(field_name="page_chunk")
                    ]
                )
            )
        ]
    )
)

index_client = SearchIndexClient(endpoint=search_endpoint, credential=credential)
index_client.create_or_update_index(index)
print(f"Index '{index_name}' created or updated successfully.")

# Upload documents
url = "https://raw.githubusercontent.com/Azure-Samples/azure-search-sample-data/refs/heads/main/nasa-e-book/earth-at-night-json/documents.json"
documents = requests.get(url).json()

with SearchIndexingBufferedSender(endpoint=search_endpoint, index_name=index_name, credential=credential) as client:
    client.upload_documents(documents=documents)

print(f"Documents uploaded to index '{index_name}' successfully.")

# Create a knowledge source
ks = SearchIndexKnowledgeSource(
    name=knowledge_source_name,
    description="Knowledge source for Earth at night data",
    search_index_parameters=SearchIndexKnowledgeSourceParameters(
        search_index_name=index_name,
        source_data_fields=[SearchIndexFieldReference(name="id"), SearchIndexFieldReference(name="page_number")]
    ),
)

index_client = SearchIndexClient(endpoint=search_endpoint, credential=credential)
index_client.create_or_update_knowledge_source(knowledge_source=ks)
print(f"Knowledge source '{knowledge_source_name}' created or updated successfully.")

# Create a knowledge base
aoai_params = AzureOpenAIVectorizerParameters(
    resource_url=aoai_endpoint,
    deployment_name=aoai_gpt_deployment,
    model_name=aoai_gpt_model,
)

knowledge_base = KnowledgeBase(
    name=knowledge_base_name,
    models=[KnowledgeBaseAzureOpenAIModel(azure_open_ai_parameters=aoai_params)],
    knowledge_sources=[
        KnowledgeSourceReference(
            name=knowledge_source_name
        )
    ],
    output_mode=KnowledgeRetrievalOutputMode.ANSWER_SYNTHESIS,
    answer_instructions="Provide a two sentence concise and informative answer based on the retrieved documents."
)

index_client = SearchIndexClient(endpoint=search_endpoint, credential=credential)
index_client.create_or_update_knowledge_base(knowledge_base)
print(f"Knowledge base '{knowledge_base_name}' created or updated successfully.")

# Set up messages
instructions = """
A Q&A agent that can answer questions about the Earth at night.
If you don't have the answer, respond with "I don't know".
"""

messages = [
    {
        "role": "system",
        "content": instructions
    }
]

# Run agentic retrieval
agent_client = KnowledgeBaseRetrievalClient(endpoint=search_endpoint, knowledge_base_name=knowledge_base_name, credential=credential)
query_1 = """
    Why do suburban belts display larger December brightening than urban cores even though absolute light levels are higher downtown?
    Why is the Phoenix nighttime street grid is so sharply visible from space, whereas large stretches of the interstate between midwestern cities remain comparatively dim?
    """

messages.append({
    "role": "user",
    "content": query_1
})

req = KnowledgeBaseRetrievalRequest(
    messages=[
        KnowledgeBaseMessage(
            role=m["role"],
            content=[KnowledgeBaseMessageTextContent(text=m["content"])]
        ) for m in messages if m["role"] != "system"
    ],
    knowledge_source_params=[
        SearchIndexKnowledgeSourceParams(
            knowledge_source_name=knowledge_source_name,
            include_references=True,
            include_reference_source_data=True,
            always_query_source=True
        )
    ],
    include_activity=True,
    retrieval_reasoning_effort=KnowledgeRetrievalLowReasoningEffort
)

result = agent_client.retrieve(retrieval_request=req)
print(f"Retrieved content from '{knowledge_base_name}' successfully.")

# Display the response, activity, and references
response_contents = []
activity_contents = []
references_contents = []

response_parts = []
for resp in result.response:
    for content in resp.content:
        response_parts.append(content.text)
response_content = "\n\n".join(response_parts) if response_parts else "No response found on 'result'"

response_contents.append(response_content)

# Print the three string values
print("response_content:\n", response_content, "\n")

messages.append({
    "role": "assistant",
    "content": response_content
})

if result.activity:
    activity_content = json.dumps([a.as_dict() for a in result.activity], indent=2)
else:
    activity_content = "No activity found on 'result'"

activity_contents.append(activity_content)
print("activity_content:\n", activity_content, "\n")

if result.references:
    references_content = json.dumps([r.as_dict() for r in result.references], indent=2)
else:
    references_content = "No references found on 'result'"

references_contents.append(references_content)
print("references_content:\n", references_content)

# Continue the conversation
query_2 = "How do I find lava at night?"
messages.append({
    "role": "user",
    "content": query_2
})

req = KnowledgeBaseRetrievalRequest(
    messages=[
        KnowledgeBaseMessage(
            role=m["role"],
            content=[KnowledgeBaseMessageTextContent(text=m["content"])]
        ) for m in messages if m["role"] != "system"
    ],
    knowledge_source_params=[
        SearchIndexKnowledgeSourceParams(
            knowledge_source_name=knowledge_source_name,
            include_references=True,
            include_reference_source_data=True,
            always_query_source=True
        )
    ],
    include_activity=True,
    retrieval_reasoning_effort=KnowledgeRetrievalLowReasoningEffort
)

result = agent_client.retrieve(retrieval_request=req)
print(f"Retrieved content from '{knowledge_base_name}' successfully.")

# Display the new retrieval response, activity, and references
response_parts = []
for resp in result.response:
    for content in resp.content:
        response_parts.append(content.text)
response_content = "\n\n".join(response_parts) if response_parts else "No response found on 'result'"

response_contents.append(response_content)

# Print the three string values
print("response_content:\n", response_content, "\n")

if result.activity:
    activity_content = json.dumps([a.as_dict() for a in result.activity], indent=2)
else:
    activity_content = "No activity found on 'result'"

activity_contents.append(activity_content)
print("activity_content:\n", activity_content, "\n")

if result.references:
    references_content = json.dumps([r.as_dict() for r in result.references], indent=2)
else:
    references_content = "No references found on 'result'"

references_contents.append(references_content)
print("references_content:\n", references_content)

# Clean up resources
index_client = SearchIndexClient(endpoint=search_endpoint, credential=credential)
index_client.delete_knowledge_base(knowledge_base_name)
print(f"Knowledge base '{knowledge_base_name}' deleted successfully.")

index_client = SearchIndexClient(endpoint=search_endpoint, credential=credential)
index_client.delete_knowledge_source(knowledge_source=knowledge_source_name)
print(f"Knowledge source '{knowledge_source_name}' deleted successfully.")

index_client = SearchIndexClient(endpoint=search_endpoint, credential=credential)
index_client.delete_index(index_name)
print(f"Index '{index_name}' deleted successfully.")

```

## Upload and Run vector Search Example

```python
.
.
.
# Upload documents to the index
search_client = SearchClient(endpoint=search_endpoint,
                      index_name=index_name,
                      credential=credential)
try:
    result = search_client.upload_documents(documents=documents)
    for r in result:
        print(f"Key: {r.key}, Succeeded: {r.succeeded}, ErrorMessage: {r.error_message}")
except Exception as ex:
    print("Failed to upload documents:", ex)

# Create the index client which will be used later in the query examples
index_client = SearchIndexClient(endpoint=search_endpoint, credential=credential)

# IMPORTANT: Before you run this code, make sure the documents were successfully
# created in the previous step. Sometimes it may take a few seconds for the index to be ready.
# Check the "Document count" for the index in the Azure portal.

# Execute vector search
if vector:
     try:
         vector_query = VectorizedQuery(
             vector=vector,
             k_nearest_neighbors=5,
             fields="DescriptionVector",
             kind="vector",
             exhaustive=True
         )

         results = search_client.search(
             vector_queries=[vector_query],
             select=["HotelId", "HotelName", "Description", "Category", "Tags"],
             top=5,
             include_total_count=True
         )

         print(f"Total results: {results.get_count()}")
         for result in results:
             doc = result  # result is a dict-like object
             print(f"- HotelId: {doc['HotelId']}, HotelName: {doc['HotelName']}, Category: {doc.get('Category')}")
     except Exception as ex:
         print("Vector search failed:", ex)
else:
    print("No vector loaded, skipping search.")
```

## Run Semantic Search Example

```python
.
.
.
# Set up the search client
search_client = SearchClient(endpoint=search_endpoint,
                      index_name=index_name,
                      credential=credential)

# Runs a semantic query (runs a BM25-ranked query, rescoring and promoting the most semantically relevant matches to the top)
results =  search_client.search(query_type='semantic', semantic_configuration_name='semantic-config',
    search_text="walking distance to live music", 
    select='HotelId,HotelName,Description', query_caption='extractive')

for result in results:
    print(result["@search.reranker_score"])
    print(result["HotelId"])
    print(result["HotelName"])
    print(f"Description: {result['Description']}")
```

