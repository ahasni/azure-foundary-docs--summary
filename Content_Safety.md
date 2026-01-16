# Azure AI Content Safety — Summary

## Overview
**Azure AI Content Safety** is an AI service designed to detect and moderate harmful content in both user-generated and AI-generated text and images. It helps applications comply with regulations, maintain safe environments, and manage content risks at scale.

The service provides APIs for automated detection and an interactive **Content Safety Studio** for testing, configuration, and monitoring—without requiring custom model development.

---

## Where It’s Used
Azure AI Content Safety is commonly used in:
- Generative AI applications (prompt and output moderation)
- Online marketplaces and product catalogs
- Gaming platforms (chat, assets, user content)
- Social messaging platforms
- Enterprise media moderation
- K–12 education platforms

> **Important:** Azure AI Content Safety cannot be used to detect illegal child exploitation images.

---

## Product Features
Azure AI Content Safety offers multiple APIs for text and image analysis:

### Key APIs
- **Prompt Shields**: Detects jailbreak and prompt-injection attacks on LLMs
- **Groundedness Detection (Preview)**: Verifies whether LLM responses are grounded in provided sources
- **Protected Material Detection**: Identifies known copyrighted or protected text
- **Custom Categories (Preview)**:
  - *Standard*: Train custom harmful-content categories
  - *Rapid*: Detect emerging harmful patterns in text and images
- **Analyze Text API**: Detects sexual content, violence, hate, and self-harm
- **Analyze Image API**: Detects harmful visual content
- **Task Adherence API**: Identifies misaligned or unintended AI tool usage

---

## Content Safety Studio
**Content Safety Studio** is a web-based tool for building and managing content moderation workflows.

### Capabilities
- Text and image moderation testing
- Built-in Microsoft profanity blocklists
- Custom blocklist uploads
- Configurable sensitivity thresholds
- Real-time moderation workflows
- KPI monitoring (latency, accuracy, block rate, category distribution)

### Studio Features
- **Moderate Text Content**: Test single inputs or datasets and export code
- **Moderate Image Content**: Analyze images with configurable thresholds
- **Monitor Online Activity**: Track API usage, trends, errors, and severity distributions

---

## Security
Azure AI Content Safety supports enterprise-grade security features:

### Identity and Access
- **Managed Identity** (enabled by default)
- **Microsoft Entra ID** for API and SDK authentication
- Role-based access control (RBAC)

### Data Protection
- Encryption at rest
- Support for **Customer-Managed Keys (BYOK / CMK)**
- Auditing and key lifecycle management

---

## Pricing
Azure AI Content Safety offers two pricing tiers:
- **F0** (Free)
- **S0** (Standard)

---

## Service Limits & Versioning
- Public Preview versions are deprecated after 90 days when replaced
- GA versions are deprecated after 90 days when compatibility is maintained
- Deprecation details are published on the *What’s New* page

---

## Input Requirements (Highlights)
- **Analyze Text**: Up to 10K characters
- **Analyze Image**:
  - Max size: 4 MB
  - Formats: JPEG, PNG, GIF, BMP, TIFF, WEBP
  - Dimensions: 50×50 to 7200×7200
- **Prompt Shields**: 10K characters, up to 5 documents
- **Groundedness Detection**:
  - 55K characters for sources
  - 7.5K characters for queries
- **Task Adherence**: Up to 100K characters

---

## Language Support
- English-only: Protected material, groundedness detection, custom categories (standard)
- Broad support: Chinese, English, French, German, Spanish, Italian, Japanese, Portuguese
- Other languages may work with variable quality and should be tested

---

## Key Takeaway
Azure AI Content Safety provides scalable, configurable, and enterprise-ready content moderation for text, images, and AI workflows—combining powerful APIs with a no-code studio for fast adoption and ongoing optimization.


## Analys Text Content Example

```python
import os
from azure.ai.contentsafety import ContentSafetyClient
from azure.core.credentials import AzureKeyCredential
from azure.core.exceptions import HttpResponseError
from azure.ai.contentsafety.models import AnalyzeTextOptions, TextCategory

def analyze_text():
    # analyze text
    key = os.environ["CONTENT_SAFETY_KEY"]
    endpoint = os.environ["CONTENT_SAFETY_ENDPOINT"]

    # Create an Azure AI Content Safety client
    client = ContentSafetyClient(endpoint, AzureKeyCredential(key))

    # Contruct request
    request = AnalyzeTextOptions(text="Your input text")

    # Analyze text
    try:
        response = client.analyze_text(request)
    except HttpResponseError as e:
        print("Analyze text failed.")
        if e.error:
            print(f"Error code: {e.error.code}")
            print(f"Error message: {e.error.message}")
            raise
        print(e)
        raise

    hate_result = next(item for item in response.categories_analysis if item.category == TextCategory.HATE)
    self_harm_result = next(item for item in response.categories_analysis if item.category == TextCategory.SELF_HARM)
    sexual_result = next(item for item in response.categories_analysis if item.category == TextCategory.SEXUAL)
    violence_result = next(item for item in response.categories_analysis if item.category == TextCategory.VIOLENCE)

    if hate_result:
        print(f"Hate severity: {hate_result.severity}")
    if self_harm_result:
        print(f"SelfHarm severity: {self_harm_result.severity}")
    if sexual_result:
        print(f"Sexual severity: {sexual_result.severity}")
    if violence_result:
        print(f"Violence severity: {violence_result.severity}")

if __name__ == "__main__":
    analyze_text()

```

## Text Blocklist Example

```python
import os
from azure.ai.contentsafety import BlocklistClient
from azure.ai.contentsafety import ContentSafetyClient
from azure.core.credentials import AzureKeyCredential
from azure.core.exceptions import HttpResponseError
from azure.ai.contentsafety.models import (
    TextBlocklist, AddOrUpdateTextBlocklistItemsOptions, TextBlocklistItem, AnalyzeTextOptions
)
from azure.core.exceptions import HttpResponseError

key = os.environ["CONTENT_SAFETY_KEY"]
endpoint = os.environ["CONTENT_SAFETY_ENDPOINT"]

# Create a Blocklist client
client = BlocklistClient(endpoint, AzureKeyCredential(key))

blocklist_name = "<your-blocklist-name>"
blocklist_description = "<description>"

try:
    blocklist = client.create_or_update_text_blocklist(
        blocklist_name=blocklist_name,
        options=TextBlocklist(blocklist_name=blocklist_name, description=blocklist_description),
    )
    if blocklist:
        print("\nBlocklist created or updated: ")
        print(f"Name: {blocklist.blocklist_name}, Description: {blocklist.description}")
except HttpResponseError as e:
    print("\nCreate or update text blocklist failed: ")
    if e.error:
        print(f"Error code: {e.error.code}")
        print(f"Error message: {e.error.message}")
        raise
    print(e)
    raise


# add items
blocklist_item_text_1 = "<block_item_text_1>"
blocklist_item_text_2 = "<block_item_text_2>"

blocklist_items = [TextBlocklistItem(text=blocklist_item_text_1), TextBlocklistItem(text=blocklist_item_text_2)]
try:
    result = client.add_or_update_blocklist_items(
        blocklist_name=blocklist_name, options=AddOrUpdateTextBlocklistItemsOptions(blocklist_items=blocklist_items))
    for blocklist_item in result.blocklist_items:
        print(
            f"BlocklistItemId: {blocklist_item.blocklist_item_id}, Text: {blocklist_item.text}, Description: {blocklist_item.description}"
        )
except HttpResponseError as e:
    print("\nAdd blocklistItems failed: ")
    if e.error:
        print(f"Error code: {e.error.code}")
        print(f"Error message: {e.error.message}")
        raise
    print(e)
    raise

# analyze

# Create a Content Safety client
client = ContentSafetyClient(endpoint, AzureKeyCredential(key))

input_text = "<sample input text>"

try:
    # After you edit your blocklist, it usually takes effect in 5 minutes, please wait some time before analyzing
    # with blocklist after editing.
    analysis_result = client.analyze_text(
        AnalyzeTextOptions(text=input_text, blocklist_names=[blocklist_name], halt_on_blocklist_hit=False)
    )
    if analysis_result and analysis_result.blocklists_match:
        print("\nBlocklist match results: ")
        for match_result in analysis_result.blocklists_match:
            print(
                f"BlocklistName: {match_result.blocklist_name}, BlocklistItemId: {match_result.blocklist_item_id}, "
                f"BlocklistItemText: {match_result.blocklist_item_text}"
            )
except HttpResponseError as e:
    print("\nAnalyze text failed: ")
    if e.error:
        print(f"Error code: {e.error.code}")
        print(f"Error message: {e.error.message}")
        raise
    print(e)
    raise

```

## Analyze Image Content Example


```python
import os

from azure.ai.contentsafety import ContentSafetyClient
from azure.ai.contentsafety.models import AnalyzeImageOptions, ImageData, ImageCategory
from azure.core.credentials import AzureKeyCredential
from azure.core.exceptions import HttpResponseError

def analyze_image():
    endpoint = os.environ.get('CONTENT_SAFETY_ENDPOINT')
    key = os.environ.get('CONTENT_SAFETY_KEY')
    image_path = os.path.join("sample_data", "image.jpg")

    # Create an Azure AI Content Safety client
    client = ContentSafetyClient(endpoint, AzureKeyCredential(key))


    # Build request
    with open(image_path, "rb") as file:
        request = AnalyzeImageOptions(image=ImageData(content=file.read()))

    # Analyze image
    try:
        response = client.analyze_image(request)
    except HttpResponseError as e:
        print("Analyze image failed.")
        if e.error:
            print(f"Error code: {e.error.code}")
            print(f"Error message: {e.error.message}")
            raise
        print(e)
        raise

    hate_result = next(item for item in response.categories_analysis if item.category == ImageCategory.HATE)
    self_harm_result = next(item for item in response.categories_analysis if item.category == ImageCategory.SELF_HARM)
    sexual_result = next(item for item in response.categories_analysis if item.category == ImageCategory.SEXUAL)
    violence_result = next(item for item in response.categories_analysis if item.category == ImageCategory.VIOLENCE)

    if hate_result:
        print(f"Hate severity: {hate_result.severity}")
    if self_harm_result:
        print(f"SelfHarm severity: {self_harm_result.severity}")
    if sexual_result:
        print(f"Sexual severity: {sexual_result.severity}")
    if violence_result:
        print(f"Violence severity: {violence_result.severity}")

if __name__ == "__main__":
    analyze_image()	

```

