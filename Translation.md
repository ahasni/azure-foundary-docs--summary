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

## Asynchronous (Batch) Document Translation: Supported document and glossary formats


- **Batch document formats**
  - Supports common document types including:
    - PDF (with OCR for scanned PDFs)
    - Microsoft Office files (Word, Excel, PowerPoint, Outlook)
    - OpenDocument formats
    - Markdown
    - HTML and MHTML
    - Images (JPEG, PNG, BMP, WebP – preview)
    - CSV, TSV/TAB
    - TXT and RTF
    - XLIFF

- **Legacy format handling**
  - Some source formats are converted during translation:
    - `.doc`, `.odt`, `.rtf` → `.docx`
    - `.xls`, `.ods` → `.xlsx`
    - `.ppt`, `.odp` → `.pptx`

- **Glossary formats**
  - Supported glossary file types:
    - CSV
    - TSV/TAB
    - XLIFF (XLF, XLIFF)


## Synchronous document and glossary formats

- **Synchronous document formats**
  - Supported file types include:
    - Plain text (`.txt`)
    - Tab-separated values (`.tsv`, `.tab`)
    - Comma-separated values (`.csv`)
    - HTML (`.html`, `.htm`)
    - MHTML (`.mhtml`, `.mht`)
    - Microsoft Office formats:
      - PowerPoint (`.pptx`)
      - Excel (`.xlsx`)
      - Word (`.docx`)
      - Outlook messages (`.msg`)
    - XML Localization Interchange formats (`.xlf`, `.xliff`)

- **Glossary formats (synchronous)**
  - Supported glossary file types:
    - CSV
    - TSV/TAB
    - XLIFF (`.xlf`, `.xliff`)

Overall, synchronous document translation supports commonly used text, data, web, Office, and localization formats, along with standard glossary file types for terminology control.


# REST API

## Languages List Endpoint

Returns a list of languages supported by Translate, Transliterate, and Dictionary Lookup operations. This request doesn't require authentication; just copy and paste the following GET request into your favorite REST API tool or browser:

```
https://api.cognitive.microsofttranslator.com/languages?api-version=3.0
```

## Translate to multiple languages Endpoint

```
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=zh-Hans&to=de" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'Hello, what is your name?'}]"
```
The response body is:
```
[
    {
        "translations":[
            {"text":"你好, 你叫什么名字？","to":"zh-Hans"},
            {"text":"Hallo, was ist dein Name?","to":"de"}
        ]
    }
]
```

## Transliterate Endpoint
The Text transliteration API maps your source language script or alphabet to a target language script or alphabet.
```
POST https://api.cognitive.microsofttranslator.com/transliterate?api-version=3.0
```
### Response Body

A successful response returns a JSON array containing one result for each element in the input array. Each result object includes the following properties:

- **text**: A string produced by converting the input text to the target output script.
- **script**: A string that specifies the script used in the output.

#### Example JSON Response

```json
[
  { "text": "konnnichiha", "script": "Latn" },
  { "text": "sayounara", "script": "Latn" }
]
```

## Detect Endpoint
```
POST https://api.cognitive.microsofttranslator.com/detect?api-version=3.0
```
Request Body :
```json
[
    { "text": "Ich würde wirklich gerne Ihr Auto ein paar Mal um den Block fahren." }
]
```
The following limitations apply:

    The array can have at most 100 elements.
    The entire text included in the request can't exceed 50,000 characters including spaces.


Response Body :
```json
[

    {

        "language": "de",

        "score": 1.0,

        "isTranslationSupported": true,

        "isTransliterationSupported": false

    }

]
```
## Dictionary Lookup Endpoint
This example shows how to look up alternative translations in Spanish of the English term *fly*
```bash
curl -X POST "https://api.cognitive.microsofttranslator.com/dictionary/lookup?api-version=3.0&from=en&to=es" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'fly'}]"
```
The following limitations apply to request body:

    The array can have at most 10 elements.
    The text value of an array element can't exceed 100 characters including spaces.

The response body (abbreviated for clarity) is:

```json
[
    {
        "normalizedSource":"fly",
        "displaySource":"fly",
        "translations":[
            {
                "normalizedTarget":"volar",
                "displayTarget":"volar",
                "posTag":"VERB",
                "confidence":0.4081,
                "prefixWord":"",
                "backTranslations":[
                    {"normalizedText":"fly","displayText":"fly","numExamples":15,"frequencyCount":4637},
                    {"normalizedText":"flying","displayText":"flying","numExamples":15,"frequencyCount":1365},
                    {"normalizedText":"blow","displayText":"blow","numExamples":15,"frequencyCount":503},
                    {"normalizedText":"flight","displayText":"flight","numExamples":15,"frequencyCount":135}
                ]
            },
            {
                "normalizedTarget":"mosca",
                "displayTarget":"mosca",
                "posTag":"NOUN",
                "confidence":0.2668,
                "prefixWord":"",
                "backTranslations":[
                    {"normalizedText":"fly","displayText":"fly","numExamples":15,"frequencyCount":1697},
                    {"normalizedText":"flyweight","displayText":"flyweight","numExamples":0,"frequencyCount":48},
                    {"normalizedText":"flies","displayText":"flies","numExamples":9,"frequencyCount":34}
                ]
            },
            //
            // ...list abbreviated for documentation clarity
            //
        ]
    }
]
```
## Dictionary Examples Endpoint

Provides examples that show how terms in the dictionary are used in context. This operation is used in tandem with Dictionary lookup.

```bash
curl -X POST "https://api.cognitive.microsofttranslator.com/dictionary/examples?api-version=3.0&from=en&to=es" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'fly', 'Translation':'volar'}]"
```
The following limitations apply:

    The array can have at most 10 elements.
    The text value of an array element can't exceed 100 characters including spaces.

The response body (abbreviated for clarity) is:
```json
[
    {
        "normalizedSource":"fly",
        "normalizedTarget":"volar",
        "examples":[
            {
                "sourcePrefix":"They need machines to ",
                "sourceTerm":"fly",
                "sourceSuffix":".",
                "targetPrefix":"Necesitan máquinas para ",
                "targetTerm":"volar",
                "targetSuffix":"."
            },
            {
                "sourcePrefix":"That should really ",
                "sourceTerm":"fly",
                "sourceSuffix":".",
                "targetPrefix":"Eso realmente debe ",
                "targetTerm":"volar",
                "targetSuffix":"."
            },
            //
            // ...list abbreviated for documentation clarity
            //
        ]
    }
]
```

## Translate Document (Asynchron)
```
POST https://{your-document-translation-endpoint}/translator/document:translate?api-version=2024-05-01&sourceLanguage=en&targetLanguage=fr
```

# Python SDK

```bash
pip install azure-ai-translation-text==1.0.0b1
```

```python
from azure.ai.translation.text import TextTranslationClient, TranslatorCredential
from azure.ai.translation.text.models import InputTextItem
from azure.core.exceptions import HttpResponseError

# set `<your-key>`, `<your-endpoint>`, and  `<region>` variables with the values from the Azure portal
key = "<your-key>"
endpoint = "<your-endpoint>"
region = "<region>"

credential = TranslatorCredential(key, region)
text_translator = TextTranslationClient(endpoint=endpoint, credential=credential)

try:
    source_language = "en"
    target_languages = ["es", "it"]
    input_text_elements = [ InputTextItem(text = "This is a test") ]

    response = text_translator.translate(content = input_text_elements, to = target_languages, from_parameter = source_language)
    translation = response[0] if response else None

    if translation:
        for translated_text in translation.translations:
            print(f"Text was translated to: '{translated_text.to}' and the result is: '{translated_text.text}'.")

except HttpResponseError as exception:
    print(f"Error Code: {exception.error.code}")
    print(f"Message: {exception.error.message}")
```
