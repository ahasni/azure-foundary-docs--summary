# Azure Content Understanding in Foundry Tools — Summary

## Overview
**Azure Content Understanding** is a generally available (GA) Foundry Tool that uses generative AI to process and ingest unstructured and multimodal content—such as documents, images, video, and audio—into structured, user-defined outputs. It accelerates time-to-value by transforming raw content into actionable data that integrates easily with automation, analytics, search, and agentic workflows.

---

## Why Use Content Understanding?
- **Streamlined workflows**: Standardizes extraction, classification, and insight generation across content types.
- **Simplified field extraction**: Define schemas to extract or generate structured data without complex prompt engineering.
- **Improved accuracy**: Uses multiple AI models to cross-validate results.
- **Confidence scores & grounding**: Provides reliability estimates and source traceability to reduce manual review.
- **Content classification**: Classifies document types to route content to the appropriate analyzer.
- **Industry-specific analyzers**: Prebuilt analyzers for scenarios such as tax, procurement, contracts, call centers, and media analysis.

---

## Key Use Cases
- **Intelligent Document Processing (IDP)**: Convert complex documents into structured data with validation and confidence scoring.
- **Agentic applications**: Produce clean markdown or schema-aligned outputs for reliable reasoning and automation.
- **Search & RAG ingestion**: Enhance retrieval with figure analysis, layout preservation, and multimodal support.
- **Robotic Process Automation (RPA)**: Enable end-to-end automation of document-driven workflows.
- **Analytics & reporting**: Generate structured outputs for deeper insights and decision-making.
- **Workflow optimization**: Classify content before extraction to improve routing and efficiency.

---

## Industry Applications
- Tax automation and preparation
- Mortgage and loan application processing
- Invoice and contract verification
- Retrieval-augmented generation (RAG) pipelines
- Call center and post-call analytics
- Media asset management
- Enhanced customer support and knowledge systems

---

## Key Components
- **Inputs**: Documents, images, video, and audio.
- **Analyzers**: Define extraction logic using prebuilt or custom analyzers.
- **Content extraction**: OCR, layout analysis, transcription, and visual understanding.
- **Segmentation**: Break content into logical sections for targeted processing.
- **Field extraction**:
  - *Extract*: Pull values directly from content
  - *Classify*: Categorize content for routing
  - *Generate*: Create summaries or descriptions
- **Confidence scores**: Reliability estimates for extracted values.
- **Grounding**: Trace extracted values back to source regions.
- **Contextualization**: Prepare and normalize content for generative models.
- **Foundry models**: LLMs and embeddings that power generative capabilities.
- **Structured output**: Markdown for search/RAG or JSON for automation and analytics.

---

## Experiences
- **Content Understanding in Foundry (NextGen portal)**: Build advanced agentic workflows (coming soon).
- **Content Understanding Studio**: Advanced UX for analyzer tuning, labeling, and custom classification—ideal for customers migrating from Document Intelligence.

---

## Responsible AI & Safety
- Built-in protections against harmful content.
- Integrates Azure AI Content Safety.
- Supports **modified content filtering** (for approved customers) to annotate rather than block risky outputs.
- Adheres to Microsoft Responsible AI standards and Code of Conduct.

---

## Face Capabilities
- Generates detailed textual descriptions of faces in images and video.
- Can describe attributes such as expressions and facial features, and identify prominent individuals when enabled.

---

## Azure Content Understanding — Foundry Tools: Service Quotas and Limits

Azure Content Understanding in Foundry Tools provides content analysis with defined **quotas and limits** for documents, images, audio, and video.

### Resource limits (Standard S0)
- **Max analyzers:** 100,000
- **Max analysis/min:** 1,000 pages/images
- **Audio & Video:** up to 4 hours each
- **Max operations/min:** 3,000

### Supported generative models
- **Chat completion:** GPT-4o, GPT-4.1, mini & nano variants
- **Embeddings:** text-embedding-3-small, text-embedding-3-large, text-embedding-ada-002

### Input file limits
- **Documents & text:** PDF, Office (docx, xlsx, pptx), TXT, HTML, Markdown, RTF, email, XML  
  - ≤ 200 MB / ≤ 300 pages or ≤ 1M characters  
  - **Pro mode (preview):** only PDF, TIFF, images; max 100 MB / 150 pages
- **Images:** JPEG, PNG, BMP, HEIF/HEIC; ≤ 200 MB, 50×50 to 10,000×10,000 px
- **Audio:** WAV, MP3, MP4, Opus/OGG, FLAC, WMA, AAC, AMR, 3GP, WebM, M4A, SPX; ≤ 300 MB / 2 h†  
  - †Supports up to 1 GB / 4 h, faster processing for ≤ 300 MB / ≤ 2 h
- **Video:** MP4, M4V, FLV, WMV, ASF, AVI, MKV, MOV; min 320×240 px, max 1920×1080 px  
  - **Direct upload:** ≤ 200 MB / 30 min  
  - **URL reference:** ≤ 4 GB / 2 h  
  - Frame sampling: ~1 frame/sec, frames scaled to 512×512 px

### Field schema limits
- **Supported field types:** string, date, time, number, integer, boolean
- **Nested structures:** lists, groups, tables, fixed tables
- **Max fields:** 1,000 per document/text/image/audio/video
- **Max classify categories:** 300 per document/text/image/audio/video
- **Generation methods:** extract, generate, classify

### Knowledge source limits
- **Training data:** documents only, max 1 GB, 50,000 pages/images

### Segmentation / Classification limits
- **Category name:** cannot start with `$`
- **Category name + description:** max 120 characters
- **Number of categories:** 200 per analyzer (documents), 1 (videos)
- **Hierarchical classification:** 5 layers (documents), 2 layers (videos)

## Summary
Azure Content Understanding provides a unified, AI-powered framework for transforming unstructured and multimodal content into trustworthy, structured outputs. It supports automation, analytics, search, and agent-driven workflows while maintaining enterprise-grade accuracy, transparency, and responsible AI safeguards.

# REST API

## Analyze Endpoint

Extract content and fields from input.
```
POST {endpoint}/contentunderstanding/analyzers/{analyzerId}:analyze?api-version=2025-11-01
```
With optional parameters: 
```
POST {endpoint}/contentunderstanding/analyzers/{analyzerId}:analyze?api-version=2025-11-01&stringEncoding={stringEncoding}&processingLocation={processingLocation}
```
### Sample response

#### Status code
- **202**

#### HTTP headers
```http
Operation-Location: https://myendpoint.cognitiveservices.azure.com/contentunderstanding/analyzerResults/3b31320d-8bab-4f88-b19c-2322a7f11034?api-version=2025-11-01
```
```json
{
  "id": "3b31320d-8bab-4f88-b19c-2322a7f11034",
  "status": "NotStarted"
}
```
## Content Analyzers - Get Operation Status Endpoint

Get the status of an analyzer creation operation.

```http
GET {endpoint}/contentunderstanding/analyzers/{analyzerId}/operations/{operationId}?api-version=2025-11-01
```

## Get Result Endpoint

Get the result of an analysis operation.

```http
GET {endpoint}/contentunderstanding/analyzerResults/{operationId}?api-version=2025-11-01
```
Response body: 
```json
{
  "id": "3b31320d-8bab-4f88-b19c-2322a7f11034",
  "status": "Succeeded",
  "result": {
    "analyzerId": "myAnalyzer",
    "apiVersion": "2025-11-01",
    "createdAt": "2025-05-01T18:46:36.244Z",
    "contents": [
      {
        "kind": "document",
        "mimeType": "application/pdf",
        "markdown": "# CONTOSO\n\n...",
        "startPageNumber": 1,
        "endPageNumber": 2,
        "unit": "inch",
        "pages": [
          {
            "pageNumber": 1,
            "width": 8.5,
            "height": 11
          },
          {
            "pageNumber": 2,
            "width": 8.5,
            "height": 11
          }
        ],
        "fields": {
          "Company": {
            "type": "string",
            "valueString": "CONTOSO",
            "spans": [
              {
                "offset": 7,
                "length": 2
              }
            ],
            "confidence": 0.95,
            "source": "D(1,5,1,7,1,7,1.5,5,1.5)"
          }
        }
      }
    ]
  }
}
```

## Content Analyzers - Get Result File Endpoint
Get a file associated with the result of an analysis operation.

```http
GET {endpoint}/contentunderstanding/analyzerResults/{operationId}/files/{path}?api-version=2025-11-01
```

Response:

```json
"{imageBinary}"
```


