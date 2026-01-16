## Azure Translator in Foundry Tools — Summary

**Azure Translator in Foundry Tools** is a cloud-based neural machine translation service within the Microsoft Foundry Tools ecosystem. It enables developers to build intelligent, multilingual applications across all supported languages and operating systems. The service is widely used across Microsoft products and by enterprises worldwide for reliable language translation and related language-processing tasks.

### Key Capabilities

- **Text Translation**
  - Supports real-time multilingual translation.
  - Latest *2025-10-01-preview* introduces optional large language model (LLM) selection, adaptive custom translation, and enhanced request parameters.
  - Available via REST APIs, SDKs, the Foundry (classic) portal, and containers.

- **Document Translation**
  - **Asynchronous (Batch):** Translates large or complex document sets while preserving structure and formatting, using Azure Blob Storage.
  - **Synchronous (Single file):** Translates individual documents (optionally with glossaries) without requiring Blob Storage, returning results directly.

- **Custom Translator**
  - Enables creation of customized translation models for domain- or industry-specific terminology, style, and language usage.
  - Supports custom dictionaries for phrases or sentences.

### Development Options

Azure Translator can be integrated using:
- REST APIs
- SDKs
- Containers
- Foundry (classic) portal
- Custom Translator portal

