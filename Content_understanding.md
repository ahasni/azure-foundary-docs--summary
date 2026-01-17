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
## Résumé — Azure Content Understanding (Foundry Tools)

Azure Content Understanding, intégré à **Foundry Tools**, fournit des capacités d’analyse de contenu avec des **quotas et limites définis** pour garantir performance et scalabilité.

### Limites générales
- Identifiants, champs, URL, descriptions et balises soumis à des longueurs maximales strictes
- Jusqu’à **10 balises** par ressource
- Noms et valeurs de balises encadrés par des formats autorisés

### Limites des ressources (Standard S0)
- Jusqu’à **100 000 analyseurs**
- **1 000 pages/images** analysées par minute
- Jusqu’à **4 heures d’audio** ou **4 heures de vidéo**
- **3 000 opérations par minute**

### Modèles Foundry pris en charge
- Modèles GPT-4o, GPT-4.1 et variantes (mini, nano)
- Modèles d’**embeddings** : `text-embedding-3-small`, `text-embedding-3-large`, `text-embedding-ada-002`

### Limites des fichiers d’entrée
- **Documents** : PDF, Office, HTML, TXT, images, e-mails, XML  
  - Jusqu’à **200 Mo**, **300 pages** ou **1 million de caractères**
- **Images** : jusqu’à **10 000 × 10 000 px**
- **Audio** : jusqu’à **300 Mo / 2 h** (max théorique 1 Go / 4 h)
- **Vidéo** : jusqu’à **1920 × 1080**, formats courants pris en charge

### Téléchargement vidéo
- **Chargement direct (API analyzeBinary)** : ≤ 200 Mo / 30 min
- **Référence par URL** : jusqu’à **4 Go / 2 h**

> **Remarque** : le mode **Pro (préversion)** accepte uniquement les PDF, TIFF et images, avec un maximum de **100 Mo et 150 pages**.



## Summary
Azure Content Understanding provides a unified, AI-powered framework for transforming unstructured and multimodal content into trustworthy, structured outputs. It supports automation, analytics, search, and agent-driven workflows while maintaining enterprise-grade accuracy, transparency, and responsible AI safeguards.
