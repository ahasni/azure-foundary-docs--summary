# Azure Agent

Maximum file size for agents 	**512 MB**
Maximum size for all uploaded files for agents 	**300 GB**
----------------------------------------------------------------------------------------
# Azure AI Content Safety

## Input Requirements (Highlights)

Analyze Text: **Up to 10K characters**
____________________________________
Analyze Image:
Max size: **4 MB**
________________
Formats: **JPEG, PNG, GIF, BMP, TIFF, WEBP**
__________________________________________
Dimensions: **50×50 to 7200×7200**
________________________________

------------------------------------------------------------------------------------------

# Azure Content Understanding

Documents & text: **PDF, Office (docx, xlsx, pptx), TXT, HTML, Markdown, RTF, email, XML**
**≤ 200 MB / ≤ 300 pages or ≤ 1M characters**
___________________________________________
**Pro mode (preview): only PDF, TIFF, images; max 100 MB / 150 pages**

Images: **JPEG, PNG, BMP, HEIF/HEIC; ≤ 200 MB, 50×50 to 10,000×10,000 px**

________________________________________________________________________
Audio: **WAV, MP3, MP4, Opus/OGG, FLAC, WMA, AAC, AMR, 3GP, WebM, M4A, SPX; ≤ 300 MB / 2 h†**

___________________________________________________________________________________________
**†Supports up to 1 GB / 4 h, faster processing for ≤ 300 MB / ≤ 2 h**
Video: **MP4, M4V, FLV, WMV, ASF, AVI, MKV, MOV; min 320×240 px, max 1920×1080 px**
_________________________________________________________________________________
**Direct upload: ≤ 200 MB / 30 min**
**URL reference: ≤ 4 GB / 2 h**
**Frame sampling: ~1 frame/sec, frames scaled to 512×512 px**

--------------------------------------------------------------------------------------------

# Azure Document Intelligence

## Supported Document Types
| Document Type                               | Read | Layout | Prebuilt Models | Custom Models | Add-on Capabilities |
|--------------------------------------------|------|--------|-----------------|---------------|--------------------|
| PDF                                        | ✔️   | ✔️     | ✔️              | ✔️            | ✔️                 |
| Images (JPEG, PNG, BMP, TIFF, HEIF)        | ✔️   | ✔️     | ✔️              | ✔️            | ✔️                 |
| Microsoft Office (DOCX, PPTX, XLS)         | ✔️   | ✔️     | ✖️              | ✖️            | ✖️                 |



 Max document size             | **4 MB**      | **500 MB**        | No         |
___________________________________________________________________________
| Max pages (analysis)          | **2**         | **2,000**         | No         |

--------------------------------------------------------------------------------------------------------

# Azure Language

## Maximum Request Size

All preconfigured features: **1 MB per request**

--------------------------------------------------------------------------------------------------------

# Azure Vision

## Image Requirements

To be analyzed by Azure Vision, **images** must:

Be in **JPEG, PNG, GIF, or BMP format**
_____________________________________
Be smaller than **4 MB**
______________________
Have dimensions **greater than 50 × 50 pixels**
_____________________________________________
For the **Read** (OCR) API: **50 × 50 up to 10,000 × 10,000 pixels**
__________________________________________________________________
	







