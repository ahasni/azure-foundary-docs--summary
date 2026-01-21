# Azure AI Video Indexer overview

**Azure AI Video Indexer** is a full-featured video and audio intelligence service that applies advanced machine learning and generative AI to extract structured insights from both live and recorded media. It supports transcription, translation, object and face detection, summarization, scene understanding, sentiment analysis, and metadata generation. The service is designed for enterprise use, offering flexible deployment models, deep multimodal analytics, and strong governance and compliance controls.

---

## Deployment Models

### Video Indexer Enabled by Azure Arc (Edge / Hybrid)

- Runs as an Azure Arc extension on Kubernetes clusters, validated on Azure Local and compatible with any Kubernetes infrastructure.  
- Designed for hybrid and edge deployments where **low latency**, **on-premises workflows**, or **data residency regulations** prevent sending media to the cloud.  
- Supports both **live video streams** and **uploaded files**.  
- Enables **real-time insights** with bounding boxes and overlays on live streams and the ability to save indexed streams and extracted insights.  
- Ideal for regulated industries (finance, healthcare, manufacturing, government) and environments with strict operational or compliance constraints.

**Typical scenarios**
- Retail queue monitoring and customer flow optimization  
- Manufacturing safety and quality control (PPE detection, anomaly detection)  
- Modern security and safety monitoring  
- Pre-indexing and governance of large on-premises media archives  

---

### Cloud-based Azure AI Video Indexer

- Fully managed Azure service built on Azure AI Vision, Speech, Translator, and Face services.  
- Runs more than **30 AI models** to generate deep audio and video insights.  
- Optimized for scalable indexing, search, enrichment, and analytics across large cloud video libraries.  

**Typical scenarios**
- Video search and discovery across news, education, broadcast, and enterprise archives  
- Content creation (trailers, highlight reels, social media clips)  
- Accessibility through transcription, captioning, and multilingual translation  
- Monetization with ad targeting and metadata-driven recommendations  
- Content moderation and brand safety enforcement  

---

## Core AI Capabilities

### Video Intelligence
- Face detection and grouping, celebrity recognition, and account-based face identification  
- Object detection and tracking, observed people detection with bounding boxes and timestamps  
- OCR for extracting on-screen text (signs, products, logos)  
- Scene, shot, and keyframe detection for navigation and editing  
- Visual moderation, logo and slate detection, editorial shot tagging  

### Audio Intelligence
- Speech-to-text transcription in 50+ languages  
- Automatic language detection and multilingual transcription  
- Speaker diarization and speaker statistics  
- Closed caption generation (VTT, TTML, SRT)  
- Emotion, sentiment, and acoustic event detection (sirens, applause, gunshots, silence)  
- Custom speech models for domain-specific vocabularies  

### Multimodal & NLP Insights
- Keyword extraction and named entity recognition (people, brands, locations)  
- Topic inference using multiple ontologies  
- Sentiment analysis across speech and on-screen text  
- Artifacts and rich metadata for downstream analytics and applications  

---

## Key Differentiators

- **Flexible deployment**: cloud-first or edge/hybrid with Azure Arc  
- **Real-time processing** for live streams and operational scenarios  
- **Rich multimodal analytics** combining vision, speech, and NLP  
- **Enterprise readiness** with scalable indexing, APIs, and integration into Azure AI services  
- **Built-in summarization and metadata** for search, recommendation, and automation  

---

## Governance, Compliance, and Responsible AI

- Enterprise-grade security and compliance aligned with Azure standards  
- Restricted access to facial recognition, customization, and celebrity recognition features under Responsible AI policies  
- Facial recognition is not available for U.S. police use without regulation and special authorization  
- Customers must ensure legal rights, user consent, and compliance with all applicable privacy and biometric data laws  
- Data handling governed by Microsoft Trust Center, Online Services Terms (OST), Data Processing Addendum (DPA), and Privacy Statement  

---

## Overall

Azure AI Video Indexer provides a powerful, enterprise-grade platform for transforming raw video and audio into **searchable, analyzable, and actionable intelligence**. With both cloud and edge deployment options, real-time and batch processing, and a broad set of AI models, it supports advanced use cases across operations, compliance, content creation, accessibility, monetization, and security—making it a core building block for modern video intelligence solutions.

# VI web Portal

# Quickstart: Try the Azure AI Video Indexer (VI) web portal

**Applies to:** Cloud-based Azure AI Video Indexer

This article shows you how to get started with Azure AI Video Indexer by using the web portal. Try indexing a video with the web portal before you use the API.

---

## Prerequisites

Create or find an MP4 video.

Use a video that's about 5 minutes long. We suggest it has:

- People speaking  
- Some nonspeaking noises  
- Objects such as coffee cups  
- A couple of different scenes like an indoor scene followed by an outdoor scene  

These suggestions help you see various results after indexing. You don't need to include all of them.

---

## Get a trial account

1. Go to the web portal.
2. Sign in with:
   - A Microsoft Entra ID account, or  
   - A personal Microsoft account, or  
   - A Google account.  

The portal assigns you a trial account automatically.

---

## Upload a video

Upload and index the video using the default settings. The activity of indexing a video is called a **job**.

On the home page of the web portal:

1. Select the **Upload** button. The upload screen appears.  
   *Screenshot showing the Upload option on the Library tab.*

2. Select **Browse for files**.

3. Select the video, then select **Open**.

4. The **File name** field fills in automatically, but you can change it.

5. Leave the other settings as they are, except for the video source language.

6. Select the source language from the **Video source language** list.

7. Select **Review + upload**. You see the review screen.

8. Select the rights certification box, then select **Upload + index**.  
   The video starts uploading. Select **Run in background** to close the screen.

9. If you close the uploading window, select the notification (bell) symbol to check the upload status.

---

## View Timeline and Insights

The **Timeline** and **Insights** tabs show information from the service after indexing.

While your video uploads and indexes, view some sample videos.

---

## View the timeline

1. Select the video and then select the **Timeline** tab. The video transcript appears.

2. Scan the transcript to see what the video covers.  
   
3. Select **Azure AI Video Indexer** above the left navigation bar to exit and return to the home page.

---

## View the insights

1. On the home page, select the **Samples** tab.

2. Select any one of the videos from the Samples library.  
   The video starts playing and the **Insights** tab appears.

3. The insights are grouped into categories like:
   - People  
   - Topics  
   - Labels  
   - Named entities  

   
4. Select **Azure AI Video Indexer** above the left navigation bar to exit and return to the home page.

---

## View your video timeline and insights

1. Select the **Library** tab. Your indexed videos appear there.  
   If your video is still uploading, you see a thumbnail with the upload percentage.

2. When indexing finishes, select your video to see the insights.

3. Compare the insights shown to the ones that you viewed in the Samples library.

---

## View the JSON

Access the API JSON response for your indexing job.

1. In the upper right, select the **Download** symbol.

2. Select **Insights (JSON)**.  
   The JSON file opens in a new browser window or tab.  

  
