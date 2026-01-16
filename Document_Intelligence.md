# Azure Document Intelligence in Foundry Tools — Summary

## Overview
**Azure Document Intelligence** is a cloud-based AI service that enables applications to analyze, extract, and understand information from documents. It supports a wide range of document types and formats, including structured, semi-structured, and unstructured content. The service integrates with **Microsoft Foundry**, REST APIs, and client libraries to support both low-code and pro-code development.

---

## Core Capabilities
Azure Document Intelligence uses advanced machine learning models to extract text, layout, key-value pairs, tables, and structured data from documents such as PDFs, images, forms, and receipts.

Key capabilities include:
- Optical Character Recognition (OCR)
- Document layout analysis
- Structured data extraction
- Custom model training

---

## Available Tools

### Azure Document Intelligence MCP Server 🆕
- Enables AI agents to securely access document processing capabilities using standardized protocols
- Available as:
  - **Remote server** via the Microsoft Foundry Tool Catalog
  - **Local server** for self-hosted or on-premises environments

Provides enterprise-grade governance, compliance, and data protection.

---

## Prebuilt Models
Prebuilt models require no training and are ready to use:

- **Read (OCR)** – Extracts printed and handwritten text
- **Layout** – Identifies text, tables, and document structure
- **General Document** – Extracts key-value pairs and entities
- **Invoice** – Extracts billing and payment information
- **Receipt** – Extracts merchant, total, tax, and date details
- **ID Document** – Extracts identity-related fields
- **Business Card** – Extracts contact information
- **Tax & Financial Documents** – Extracts structured financial data

---

## Custom Models

### Custom Document Models
- Train models using your own labeled documents
- Designed for domain-specific forms and layouts
- Supports extraction of custom fields and tables

### Composed Models
- Combine multiple custom models
- Automatically route documents to the correct model

---

## Development Experience
- Use **Microsoft Foundry** for no-code and low-code model creation
- SDKs available for common programming languages
- REST APIs for flexible integration

---

## Use Cases
- Automating document processing workflows
- Extracting structured data from forms and contracts
- Digitizing paper-based processes
- Enhancing AI agents with document understanding

---

## Migration
Azure Document Intelligence consolidates and evolves earlier document analysis services. Existing users can migrate workloads using provided migration guidance to take advantage of unified APIs and tooling.

---

## Summary
Azure Document Intelligence enables scalable, secure, and intelligent document processing by combining OCR, layout understanding, prebuilt models, and custom AI training—all integrated into Microsoft Foundry and AI agent workflows.

## Layout modele Example 

```bash
pip install azure-ai-documentintelligence==1.0.0b4
```

```python
# import libraries
import os
from azure.core.credentials import AzureKeyCredential
from azure.ai.documentintelligence import DocumentIntelligenceClient
from azure.ai.documentintelligence.models import AnalyzeResult
from azure.ai.documentintelligence.models import AnalyzeDocumentRequest

# set `<your-endpoint>` and `<your-key>` variables with the values from the Azure portal
endpoint = "<your-endpoint>"
key = "<your-key>"

# helper functions

def get_words(page, line):
    result = []
    for word in page.words:
        if _in_span(word, line.spans):
            result.append(word)
    return result


def _in_span(word, spans):
    for span in spans:
        if word.span.offset >= span.offset and (
            word.span.offset + word.span.length
        ) <= (span.offset + span.length):
            return True
    return False


def analyze_layout():
    # sample document
    formUrl = "https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-layout.pdf"

    document_intelligence_client = DocumentIntelligenceClient(
        endpoint=endpoint, credential=AzureKeyCredential(key)
    )

    poller = document_intelligence_client.begin_analyze_document(
        "prebuilt-layout", AnalyzeDocumentRequest(url_source=formUrl
    ))

    result: AnalyzeResult = poller.result()

    if result.styles and any([style.is_handwritten for style in result.styles]):
        print("Document contains handwritten content")
    else:
        print("Document does not contain handwritten content")

    for page in result.pages:
        print(f"----Analyzing layout from page #{page.page_number}----")
        print(
            f"Page has width: {page.width} and height: {page.height}, measured with unit: {page.unit}"
        )

        if page.lines:
            for line_idx, line in enumerate(page.lines):
                words = get_words(page, line)
                print(
                    f"...Line # {line_idx} has word count {len(words)} and text '{line.content}' "
                    f"within bounding polygon '{line.polygon}'"
                )

                for word in words:
                    print(
                        f"......Word '{word.content}' has a confidence of {word.confidence}"
                    )

        if page.selection_marks:
            for selection_mark in page.selection_marks:
                print(
                    f"Selection mark is '{selection_mark.state}' within bounding polygon "
                    f"'{selection_mark.polygon}' and has a confidence of {selection_mark.confidence}"
                )

    if result.tables:
        for table_idx, table in enumerate(result.tables):
            print(
                f"Table # {table_idx} has {table.row_count} rows and "
                f"{table.column_count} columns"
            )
            if table.bounding_regions:
                for region in table.bounding_regions:
                    print(
                        f"Table # {table_idx} location on page: {region.page_number} is {region.polygon}"
                    )
            for cell in table.cells:
                print(
                    f"...Cell[{cell.row_index}][{cell.column_index}] has text '{cell.content}'"
                )
                if cell.bounding_regions:
                    for region in cell.bounding_regions:
                        print(
                            f"...content on page {region.page_number} is within bounding polygon '{region.polygon}'"
                        )

    print("----------------------------------------")


if __name__ == "__main__":
    analyze_layout()
	
```

## Prebuilt model(prebuilt-invoice) Example 

```bash
pip install azure-ai-documentintelligence==1.0.0b4
```

```python
# import libraries
import os
from azure.core.credentials import AzureKeyCredential
from azure.ai.documentintelligence import DocumentIntelligenceClient
from azure.ai.documentintelligence.models import AnalyzeResult
from azure.ai.documentintelligence.models import AnalyzeDocumentRequest



# set `<your-endpoint>` and `<your-key>` variables with the values from the Azure portal
endpoint = "<your-endpoint>"
key = "<your-key>"

def analyze_invoice():
    # sample document

    invoiceUrl = "https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-invoice.pdf"

    document_intelligence_client = DocumentIntelligenceClient(
        endpoint=endpoint, credential=AzureKeyCredential(key)
    )

    poller = document_intelligence_client.begin_analyze_document(
        "prebuilt-invoice", AnalyzeDocumentRequest(url_source=invoiceUrl)
    )
    invoices = poller.result()

    if invoices.documents:
        for idx, invoice in enumerate(invoices.documents):
            print(f"--------Analyzing invoice #{idx + 1}--------")
            vendor_name = invoice.fields.get("VendorName")
            if vendor_name:
                print(
                    f"Vendor Name: {vendor_name.get('content')} has confidence: {vendor_name.get('confidence')}"
                )
            vendor_address = invoice.fields.get("VendorAddress")
            if vendor_address:
                print(
                    f"Vendor Address: {vendor_address.get('content')} has confidence: {vendor_address.get('confidence')}"
                )
            vendor_address_recipient = invoice.fields.get("VendorAddressRecipient")
            if vendor_address_recipient:
                print(
                    f"Vendor Address Recipient: {vendor_address_recipient.get('content')} has confidence: {vendor_address_recipient.get('confidence')}"
                )
            customer_name = invoice.fields.get("CustomerName")
            if customer_name:
                print(
                    f"Customer Name: {customer_name.get('content')} has confidence: {customer_name.get('confidence')}"
                )
            customer_id = invoice.fields.get("CustomerId")
            if customer_id:
                print(
                    f"Customer Id: {customer_id.get('content')} has confidence: {customer_id.get('confidence')}"
                )
            customer_address = invoice.fields.get("CustomerAddress")
            if customer_address:
                print(
                    f"Customer Address: {customer_address.get('content')} has confidence: {customer_address.get('confidence')}"
                )
            customer_address_recipient = invoice.fields.get("CustomerAddressRecipient")
            if customer_address_recipient:
                print(
                    f"Customer Address Recipient: {customer_address_recipient.get('content')} has confidence: {customer_address_recipient.get('confidence')}"
                )
            invoice_id = invoice.fields.get("InvoiceId")
            if invoice_id:
                print(
                    f"Invoice Id: {invoice_id.get('content')} has confidence: {invoice_id.get('confidence')}"
                )
            invoice_date = invoice.fields.get("InvoiceDate")
            if invoice_date:
                print(
                    f"Invoice Date: {invoice_date.get('content')} has confidence: {invoice_date.get('confidence')}"
                )
            invoice_total = invoice.fields.get("InvoiceTotal")
            if invoice_total:
                print(
                    f"Invoice Total: {invoice_total.get('content')} has confidence: {invoice_total.get('confidence')}"
                )
            due_date = invoice.fields.get("DueDate")
            if due_date:
                print(
                    f"Due Date: {due_date.get('content')} has confidence: {due_date.get('confidence')}"
                )
            purchase_order = invoice.fields.get("PurchaseOrder")
            if purchase_order:
                print(
                    f"Purchase Order: {purchase_order.get('content')} has confidence: {purchase_order.get('confidence')}"
                )
            billing_address = invoice.fields.get("BillingAddress")
            if billing_address:
                print(
                    f"Billing Address: {billing_address.get('content')} has confidence: {billing_address.get('confidence')}"
                )
            billing_address_recipient = invoice.fields.get("BillingAddressRecipient")
            if billing_address_recipient:
                print(
                    f"Billing Address Recipient: {billing_address_recipient.get('content')} has confidence: {billing_address_recipient.get('confidence')}"
                )
            shipping_address = invoice.fields.get("ShippingAddress")
            if shipping_address:
                print(
                    f"Shipping Address: {shipping_address.get('content')} has confidence: {shipping_address.get('confidence')}"
                )
            shipping_address_recipient = invoice.fields.get("ShippingAddressRecipient")
            if shipping_address_recipient:
                print(
                    f"Shipping Address Recipient: {shipping_address_recipient.get('content')} has confidence: {shipping_address_recipient.get('confidence')}"
                )
            print("Invoice items:")
            for idx, item in enumerate(invoice.fields.get("Items").get("valueArray")):
                print(f"...Item #{idx + 1}")
                item_description = item.get("valueObject").get("Description")
                if item_description:
                    print(
                        f"......Description: {item_description.get('content')} has confidence: {item_description.get('confidence')}"
                    )
                item_quantity = item.get("valueObject").get("Quantity")
                if item_quantity:
                    print(
                        f"......Quantity: {item_quantity.get('content')} has confidence: {item_quantity.get('confidence')}"
                    )
                unit = item.get("valueObject").get("Unit")
                if unit:
                    print(
                        f"......Unit: {unit.get('content')} has confidence: {unit.get('confidence')}"
                    )
                unit_price = item.get("valueObject").get("UnitPrice")
                if unit_price:
                    unit_price_code = (
                        unit_price.get("valueCurrency").get("currencyCode")
                        if unit_price.get("valueCurrency").get("currencyCode")
                        else ""
                    )
                    print(
                        f"......Unit Price: {unit_price.get('content')}{unit_price_code} has confidence: {unit_price.get('confidence')}"
                    )
                product_code = item.get("valueObject").get("ProductCode")
                if product_code:
                    print(
                        f"......Product Code: {product_code.get('content')} has confidence: {product_code.get('confidence')}"
                    )
                item_date = item.get("valueObject").get("Date")
                if item_date:
                    print(
                        f"......Date: {item_date.get('content')} has confidence: {item_date.get('confidence')}"
                    )
                tax = item.get("valueObject").get("Tax")
                if tax:
                    print(
                        f"......Tax: {tax.get('content')} has confidence: {tax.get('confidence')}"
                    )
                amount = item.get("valueObject").get("Amount")
                if amount:
                    print(
                        f"......Amount: {amount.get('content')} has confidence: {amount.get('confidence')}"
                    )
            subtotal = invoice.fields.get("SubTotal")
            if subtotal:
                print(
                    f"Subtotal: {subtotal.get('content')} has confidence: {subtotal.get('confidence')}"
                )
            total_tax = invoice.fields.get("TotalTax")
            if total_tax:
                print(
                    f"Total Tax: {total_tax.get('content')} has confidence: {total_tax.get('confidence')}"
                )
            previous_unpaid_balance = invoice.fields.get("PreviousUnpaidBalance")
            if previous_unpaid_balance:
                print(
                    f"Previous Unpaid Balance: {previous_unpaid_balance.get('content')} has confidence: {previous_unpaid_balance.get('confidence')}"
                )
            amount_due = invoice.fields.get("AmountDue")
            if amount_due:
                print(
                    f"Amount Due: {amount_due.get('content')} has confidence: {amount_due.get('confidence')}"
                )
            service_start_date = invoice.fields.get("ServiceStartDate")
            if service_start_date:
                print(
                    f"Service Start Date: {service_start_date.get('content')} has confidence: {service_start_date.get('confidence')}"
                )
            service_end_date = invoice.fields.get("ServiceEndDate")
            if service_end_date:
                print(
                    f"Service End Date: {service_end_date.get('content')} has confidence: {service_end_date.get('confidence')}"
                )
            service_address = invoice.fields.get("ServiceAddress")
            if service_address:
                print(
                    f"Service Address: {service_address.get('content')} has confidence: {service_address.get('confidence')}"
                )
            service_address_recipient = invoice.fields.get("ServiceAddressRecipient")
            if service_address_recipient:
                print(
                    f"Service Address Recipient: {service_address_recipient.get('content')} has confidence: {service_address_recipient.get('confidence')}"
                )
            remittance_address = invoice.fields.get("RemittanceAddress")
            if remittance_address:
                print(
                    f"Remittance Address: {remittance_address.get('content')} has confidence: {remittance_address.get('confidence')}"
                )
            remittance_address_recipient = invoice.fields.get(
                "RemittanceAddressRecipient"
            )
            if remittance_address_recipient:
                print(
                    f"Remittance Address Recipient: {remittance_address_recipient.get('content')} has confidence: {remittance_address_recipient.get('confidence')}"
                )


          print("----------------------------------------")


if __name__ == "__main__":
    analyze_invoice()
	
```

## Extract Figures from Documents Example

```python
from azure.core.credentials import AzureKeyCredential
from azure.ai.documentintelligence import DocumentIntelligenceClient
from azure.ai.documentintelligence.models import AnalyzeOutputOption, AnalyzeResult

endpoint = os.environ["DOCUMENTINTELLIGENCE_ENDPOINT"]
key = os.environ["DOCUMENTINTELLIGENCE_API_KEY"]

document_intelligence_client = DocumentIntelligenceClient(endpoint=endpoint, credential=AzureKeyCredential(key))

with open(path_to_sample_documents, "rb") as f:
    poller = document_intelligence_client.begin_analyze_document(
        "prebuilt-layout",
        analyze_request=f,
        output=[AnalyzeOutputOption.FIGURES],
        content_type="application/octet-stream",
    )
result: AnalyzeResult = poller.result()
operation_id = poller.details["operation_id"]

if result.figures:
    for figure in result.figures:
        if figure.id:
            response = document_intelligence_client.get_analyze_result_figure(
                model_id=result.model_id, result_id=operation_id, figure_id=figure.id
            )
            with open(f"{figure.id}.png", "wb") as writer:
                writer.writelines(response)
else:
    print("No figures found.")
	

```

## Analyze Documents Result in PDF Example

```python
from azure.core.credentials import AzureKeyCredential
from azure.ai.documentintelligence import DocumentIntelligenceClient
from azure.ai.documentintelligence.models import AnalyzeOutputOption, AnalyzeResult

endpoint = os.environ["DOCUMENTINTELLIGENCE_ENDPOINT"]
key = os.environ["DOCUMENTINTELLIGENCE_API_KEY"]

document_intelligence_client = DocumentIntelligenceClient(endpoint=endpoint, credential=AzureKeyCredential(key))

with open(path_to_sample_documents, "rb") as f:
    poller = document_intelligence_client.begin_analyze_document(
        "prebuilt-read",
        analyze_request=f,
        output=[AnalyzeOutputOption.PDF],
        content_type="application/octet-stream",
    )
result: AnalyzeResult = poller.result()
operation_id = poller.details["operation_id"]

response = document_intelligence_client.get_analyze_result_pdf(model_id=result.model_id, result_id=operation_id)
with open("analyze_result.pdf", "wb") as writer:
    writer.writelines(response)
	
```

## General Document Model Example

```python
from azure.core.credentials import AzureKeyCredential
from azure.ai.documentintelligence import DocumentIntelligenceClient
from azure.ai.documentintelligence.models import DocumentAnalysisFeature, AnalyzeResult

def _in_span(word, spans):
    for span in spans:
        if word.span.offset >= span.offset and (word.span.offset + word.span.length) <= (span.offset + span.length):
            return True
    return False

def _format_bounding_region(bounding_regions):
    if not bounding_regions:
        return "N/A"
    return ", ".join(
        f"Page #{region.page_number}: {_format_polygon(region.polygon)}" for region in bounding_regions
    )

def _format_polygon(polygon):
    if not polygon:
        return "N/A"
    return ", ".join([f"[{polygon[i]}, {polygon[i + 1]}]" for i in range(0, len(polygon), 2)])

endpoint = os.environ["DOCUMENTINTELLIGENCE_ENDPOINT"]
key = os.environ["DOCUMENTINTELLIGENCE_API_KEY"]

document_intelligence_client = DocumentIntelligenceClient(endpoint=endpoint, credential=AzureKeyCredential(key))
with open(path_to_sample_documents, "rb") as f:
    poller = document_intelligence_client.begin_analyze_document(
        "prebuilt-layout",
        analyze_request=f,
        features=[DocumentAnalysisFeature.KEY_VALUE_PAIRS],
        content_type="application/octet-stream",
    )
result: AnalyzeResult = poller.result()

if result.styles:
    for style in result.styles:
        if style.is_handwritten:
            print("Document contains handwritten content: ")
            print(",".join([result.content[span.offset : span.offset + span.length] for span in style.spans]))

print("----Key-value pairs found in document----")
if result.key_value_pairs:
    for kv_pair in result.key_value_pairs:
        if kv_pair.key:
            print(
                f"Key '{kv_pair.key.content}' found within "
                f"'{_format_bounding_region(kv_pair.key.bounding_regions)}' bounding regions"
            )
        if kv_pair.value:
            print(
                f"Value '{kv_pair.value.content}' found within "
                f"'{_format_bounding_region(kv_pair.value.bounding_regions)}' bounding regions\n"
            )

for page in result.pages:
    print(f"----Analyzing document from page #{page.page_number}----")
    print(f"Page has width: {page.width} and height: {page.height}, measured with unit: {page.unit}")

    if page.lines:
        for line_idx, line in enumerate(page.lines):
            words = []
            if page.words:
                for word in page.words:
                    print(f"......Word '{word.content}' has a confidence of {word.confidence}")
                    if _in_span(word, line.spans):
                        words.append(word)
            print(
                f"...Line #{line_idx} has {len(words)} words and text '{line.content}' within "
                f"bounding polygon '{_format_polygon(line.polygon)}'"
            )

    if page.selection_marks:
        for selection_mark in page.selection_marks:
            print(
                f"Selection mark is '{selection_mark.state}' within bounding polygon "
                f"'{_format_polygon(selection_mark.polygon)}' and has a confidence of "
                f"{selection_mark.confidence}"
            )

if result.tables:
    for table_idx, table in enumerate(result.tables):
        print(f"Table # {table_idx} has {table.row_count} rows and {table.column_count} columns")
        if table.bounding_regions:
            for region in table.bounding_regions:
                print(
                    f"Table # {table_idx} location on page: {region.page_number} is {_format_polygon(region.polygon)}"
                )
        for cell in table.cells:
            print(f"...Cell[{cell.row_index}][{cell.column_index}] has text '{cell.content}'")
            if cell.bounding_regions:
                for region in cell.bounding_regions:
                    print(
                        f"...content on page {region.page_number} is within bounding polygon '{_format_polygon(region.polygon)}'\n"
                    )
print("----------------------------------------")	

```

## Prebuilt Model (prebuilt-receipt) Example

```python
from azure.core.credentials import AzureKeyCredential
from azure.ai.documentintelligence import DocumentIntelligenceClient
from azure.ai.documentintelligence.models import AnalyzeResult

def _format_price(price_dict):
    return "".join([f"{p}" for p in price_dict.values()])

endpoint = os.environ["DOCUMENTINTELLIGENCE_ENDPOINT"]
key = os.environ["DOCUMENTINTELLIGENCE_API_KEY"]

document_intelligence_client = DocumentIntelligenceClient(endpoint=endpoint, credential=AzureKeyCredential(key))
with open(path_to_sample_documents, "rb") as f:
    poller = document_intelligence_client.begin_analyze_document(
        "prebuilt-receipt", analyze_request=f, locale="en-US", content_type="application/octet-stream"
    )
receipts: AnalyzeResult = poller.result()

if receipts.documents:
    for idx, receipt in enumerate(receipts.documents):
        print(f"--------Analysis of receipt #{idx + 1}--------")
        print(f"Receipt type: {receipt.doc_type if receipt.doc_type else 'N/A'}")
        if receipt.fields:
            merchant_name = receipt.fields.get("MerchantName")
            if merchant_name:
                print(
                    f"Merchant Name: {merchant_name.get('valueString')} has confidence: "
                    f"{merchant_name.confidence}"
                )
            transaction_date = receipt.fields.get("TransactionDate")
            if transaction_date:
                print(
                    f"Transaction Date: {transaction_date.get('valueDate')} has confidence: "
                    f"{transaction_date.confidence}"
                )
            items = receipt.fields.get("Items")
            if items:
                print("Receipt items:")
                for idx, item in enumerate(items.get("valueArray")):
                    print(f"...Item #{idx + 1}")
                    item_description = item.get("valueObject").get("Description")
                    if item_description:
                        print(
                            f"......Item Description: {item_description.get('valueString')} has confidence: "
                            f"{item_description.confidence}"
                        )
                    item_quantity = item.get("valueObject").get("Quantity")
                    if item_quantity:
                        print(
                            f"......Item Quantity: {item_quantity.get('valueString')} has confidence: "
                            f"{item_quantity.confidence}"
                        )
                    item_total_price = item.get("valueObject").get("TotalPrice")
                    if item_total_price:
                        print(
                            f"......Total Item Price: {_format_price(item_total_price.get('valueCurrency'))} has confidence: "
                            f"{item_total_price.confidence}"
                        )
            subtotal = receipt.fields.get("Subtotal")
            if subtotal:
                print(
                    f"Subtotal: {_format_price(subtotal.get('valueCurrency'))} has confidence: {subtotal.confidence}"
                )
            tax = receipt.fields.get("TotalTax")
            if tax:
                print(f"Total tax: {_format_price(tax.get('valueCurrency'))} has confidence: {tax.confidence}")
            tip = receipt.fields.get("Tip")
            if tip:
                print(f"Tip: {_format_price(tip.get('valueCurrency'))} has confidence: {tip.confidence}")
            total = receipt.fields.get("Total")
            if total:
                print(f"Total: {_format_price(total.get('valueCurrency'))} has confidence: {total.confidence}")
        print("--------------------------------------")

```

## Custom Model Example

###Build Model

```python
# Let's build a model to use for this sample
import uuid
from azure.ai.documentintelligence import DocumentIntelligenceAdministrationClient
from azure.ai.documentintelligence.models import (
    DocumentBuildMode,
    BuildDocumentModelRequest,
    AzureBlobContentSource,
    DocumentModelDetails,
)
from azure.core.credentials import AzureKeyCredential

endpoint = os.environ["DOCUMENTINTELLIGENCE_ENDPOINT"]
key = os.environ["DOCUMENTINTELLIGENCE_API_KEY"]
container_sas_url = os.environ["DOCUMENTINTELLIGENCE_STORAGE_CONTAINER_SAS_URL"]

document_intelligence_admin_client = DocumentIntelligenceAdministrationClient(endpoint, AzureKeyCredential(key))
poller = document_intelligence_admin_client.begin_build_document_model(
    BuildDocumentModelRequest(
        model_id=str(uuid.uuid4()),
        build_mode=DocumentBuildMode.TEMPLATE,
        azure_blob_source=AzureBlobContentSource(container_url=container_sas_url),
        description="my model description",
    )
)
model: DocumentModelDetails = poller.result()

print(f"Model ID: {model.model_id}")
print(f"Description: {model.description}")
print(f"Model created on: {model.created_date_time}")
print(f"Model expires on: {model.expiration_date_time}")
if model.doc_types:
    print("Doc types the model can recognize:")
    for name, doc_type in model.doc_types.items():
        print(f"Doc Type: '{name}' built with '{doc_type.build_mode}' mode which has the following fields:")
        if doc_type.field_schema:
            for field_name, field in doc_type.field_schema.items():
                if doc_type.field_confidence:
                    print(
                        f"Field: '{field_name}' has type '{field['type']}' and confidence score "
                        f"{doc_type.field_confidence[field_name]}"
                    )
					

```

### Analyze Documents Using a Custom Model Example

```python
from azure.core.credentials import AzureKeyCredential
from azure.ai.documentintelligence import DocumentIntelligenceClient
from azure.ai.documentintelligence.models import AnalyzeResult

def _print_table(header_names, table_data):
    # Print a two-dimensional array like a table.
    max_len_list = []
    for i in range(len(header_names)):
        col_values = list(map(lambda row: len(str(row[i])), table_data))
        col_values.append(len(str(header_names[i])))
        max_len_list.append(max(col_values))

    row_format_str = "".join(map(lambda len: f"{{:<{len + 4}}}", max_len_list))

    print(row_format_str.format(*header_names))
    for row in table_data:
        print(row_format_str.format(*row))

endpoint = os.environ["DOCUMENTINTELLIGENCE_ENDPOINT"]
key = os.environ["DOCUMENTINTELLIGENCE_API_KEY"]
model_id = os.getenv("CUSTOM_BUILT_MODEL_ID", custom_model_id)

document_intelligence_client = DocumentIntelligenceClient(endpoint=endpoint, credential=AzureKeyCredential(key))

# Make sure your document's type is included in the list of document types the custom model can analyze
with open(path_to_sample_documents, "rb") as f:
    poller = document_intelligence_client.begin_analyze_document(
        model_id=model_id, analyze_request=f, content_type="application/octet-stream"
    )
result: AnalyzeResult = poller.result()

if result.documents:
    for idx, document in enumerate(result.documents):
        print(f"--------Analyzing document #{idx + 1}--------")
        print(f"Document has type {document.doc_type}")
        print(f"Document has document type confidence {document.confidence}")
        print(f"Document was analyzed with model with ID {result.model_id}")
        if document.fields:
            for name, field in document.fields.items():
                field_value = field.get("valueString") if field.get("valueString") else field.content
                print(
                    f"......found field of type '{field.type}' with value '{field_value}' and with confidence {field.confidence}"
                )

    # Extract table cell values
    SYMBOL_OF_TABLE_TYPE = "array"
    SYMBOL_OF_OBJECT_TYPE = "object"
    KEY_OF_VALUE_OBJECT = "valueObject"
    KEY_OF_CELL_CONTENT = "content"

    for doc in result.documents:
        if not doc.fields is None:
            for field_name, field_value in doc.fields.items():
                # Dynamic Table cell information store as array in document field.
                if field_value.type == SYMBOL_OF_TABLE_TYPE and field_value.value_array:
                    col_names = []
                    sample_obj = field_value.value_array[0]
                    if KEY_OF_VALUE_OBJECT in sample_obj:
                        col_names = list(sample_obj[KEY_OF_VALUE_OBJECT].keys())
                    print("----Extracting Dynamic Table Cell Values----")
                    table_rows = []
                    for obj in field_value.value_array:
                        if KEY_OF_VALUE_OBJECT in obj:
                            value_obj = obj[KEY_OF_VALUE_OBJECT]
                            extract_value_by_col_name = lambda key: (
                                value_obj[key].get(KEY_OF_CELL_CONTENT)
                                if key in value_obj and KEY_OF_CELL_CONTENT in value_obj[key]
                                else "None"
                            )
                            row_data = list(map(extract_value_by_col_name, col_names))
                            table_rows.append(row_data)
                    _print_table(col_names, table_rows)

                elif (
                    field_value.type == SYMBOL_OF_OBJECT_TYPE
                    and KEY_OF_VALUE_OBJECT in field_value
                    and field_value[KEY_OF_VALUE_OBJECT] is not None
                ):
                    rows_by_columns = list(field_value[KEY_OF_VALUE_OBJECT].values())
                    is_fixed_table = all(
                        (
                            rows_of_column["type"] == SYMBOL_OF_OBJECT_TYPE
                            and Counter(list(rows_by_columns[0][KEY_OF_VALUE_OBJECT].keys()))
                            == Counter(list(rows_of_column[KEY_OF_VALUE_OBJECT].keys()))
                        )
                        for rows_of_column in rows_by_columns
                    )

                    # Fixed Table cell information store as object in document field.
                    if is_fixed_table:
                        print("----Extracting Fixed Table Cell Values----")
                        col_names = list(field_value[KEY_OF_VALUE_OBJECT].keys())
                        row_dict: dict = {}
                        for rows_of_column in rows_by_columns:
                            rows = rows_of_column[KEY_OF_VALUE_OBJECT]
                            for row_key in list(rows.keys()):
                                if row_key in row_dict:
                                    row_dict[row_key].append(rows[row_key].get(KEY_OF_CELL_CONTENT))
                                else:
                                    row_dict[row_key] = [
                                        row_key,
                                        rows[row_key].get(KEY_OF_CELL_CONTENT),
                                    ]

                        col_names.insert(0, "")
                        _print_table(col_names, list(row_dict.values()))

print("------------------------------------")

```
