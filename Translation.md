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

