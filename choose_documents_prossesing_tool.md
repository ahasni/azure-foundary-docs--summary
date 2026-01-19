# Choosing the Right Azure Foundry Tool for Document Processing — Summary

## Purpose
This guide compares three Azure AI options for Intelligent Document Processing (IDP) to help organizations choose the best tool for managing documents and unstructured data:

- **Document Intelligence**
- **Content Understanding**
- **Azure-hosted LLMs (Foundry models)**

---

## The Three Options at a Glance

### 1. Document Intelligence
**Best for:** Standard, structured documents (forms, invoices, IDs)

**Strengths**
- Industry-leading OCR and layout extraction  
- High accuracy with confidence scores and grounding  
- Prebuilt and custom models for known schemas  

**Limitations**
- Limited semantic understanding  
- Less flexible for highly varied or unstructured documents  

---

### 2. Content Understanding (Recommended for most cases)
**Best for:** Complex, high-variation, multimodal, or unstructured documents  

**Strengths**
- Multimodal input (documents, images, audio, video)  
- Zero-shot schema setup (no labeling required initially)  
- Supports inferred fields, enrichments, reasoning, validation  
- Built-in confidence scores, grounding, chunking, and normalization  
- Handles large and highly varied documents  

**Key Features**
- Updated OCR and layout (multi-page tables, hyperlinks)  
- Inferred fields and metadata generation  
- Multi-file processing and reasoning (preview)  
- Token-based pricing and enterprise security  

---

### 3. Build Your Own with Foundry Models
**Best for:** Highly customized or experimental AI workflows  

**Strengths**
- Maximum control over models, prompts, and workflows  
- Flexible integration with GPT, Whisper, embeddings  

**Challenges**
- Requires heavy engineering  
- No built-in confidence scores or grounding  
- Manual scaling, chunking, validation, and cost management  

---

## Capability Comparison (Highlights)

- **OCR & Layout:** All supported, but managed services handle preprocessing automatically  
- **Confidence & Grounding:** Built-in for Document Intelligence and Content Understanding only  
- **Inferred Fields & Metadata:** Supported by Content Understanding and custom builds  
- **Ease of Use:**  
  - Document Intelligence: requires labeling/training for custom models  
  - Content Understanding: simplest, schema-first with zero labeling  
  - Custom build: requires prompt engineering and orchestration  

---

## Scenario-Based Recommendations

### Scenario 1: Single, standardized forms  
**Recommended:** Content Understanding or Document Intelligence  

### Scenario 2: Few known variants  
**Recommended:** Content Understanding  
Alternative: Document Intelligence with custom models  

### Scenario 3: High-variation semi-structured documents  
**Recommended:** Content Understanding  

### Scenario 4: Unstructured narrative documents (contracts, reports)  
**Recommended:** Content Understanding  
Alternative: Custom solution (high effort, no built-in confidence)  

### Scenario 5: Multi-document & mixed media workflows  
**Recommended:** Content Understanding (Pro mode, preview)  
Alternative: Custom agentic solution  

---

## Key Decision Factors

When choosing a tool, consider:

- Straight Through Processing (automation rate)  
- Latency requirements  
- Accuracy and confidence scoring  
- Continuous improvement & scalability  
- Complexity of extraction and inference  
- Engineering effort and total cost of ownership  

---

## Final Guidance

- **Start with Content Understanding for most IDP scenarios** — it fully covers Document Intelligence use cases and extends them with multimodal processing, inference, reasoning, and post-processing.
- Use **Document Intelligence** for simple, structured, low-latency extraction with proven accuracy.
- Use **Foundry models (custom builds)** only when you need maximum flexibility and are willing to invest heavily in engineering, validation, and cost control.

**Bottom line:**  
> Content Understanding is the recommended default for modern, scalable, and intelligent document processing in Azure Foundry.
