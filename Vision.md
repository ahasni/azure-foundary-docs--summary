# Azure Vision in Foundry Tools — Summary

## Overview
**Azure Vision** provides advanced AI algorithms for analyzing images and extracting meaningful information based on visual features. It enables developers to process images and return structured insights such as text, objects, faces, and descriptive metadata.

Azure Vision is commonly used for document processing, visual understanding, and identity-related scenarios, and is accessible through SDKs, APIs, and Vision Studio.

---

## Core Services

### Optical Character Recognition (OCR)
- Extracts **printed and handwritten text** from images and documents
- Uses deep learning models for high accuracy
- Works across diverse surfaces such as invoices, receipts, posters, business cards, and whiteboards
- Supports multiple languages
- Implemented using the **Read API**

### Image Analysis
- Detects visual features such as:
  - Objects
  - Faces
  - Adult content
  - Auto-generated image descriptions
- Designed for general-purpose image understanding

### Face
- Detects, recognizes, and analyzes human faces in images
- Supports use cases such as:
  - Identity verification
  - Touchless access control
  - Face blurring for privacy

---

## Digital Asset Management (DAM)
Azure Vision supports a wide range of **digital asset management** scenarios, including:
- Organizing and categorizing images by logos, faces, objects, and colors
- Automatically generating captions and searchable keywords
- Managing large image libraries and digital rights

It can be combined with **Foundry Tools**, **Azure AI Search**, and reporting solutions to create end-to-end DAM systems.

---

## Image Requirements
To be analyzed by Azure Vision, images must:
- Be in **JPEG, PNG, GIF, or BMP** format
- Be **smaller than 4 MB**
- Have dimensions **greater than 50 × 50 pixels**
  - For the Read (OCR) API: 50 × 50 up to 10,000 × 10,000 pixels

---

## Key Takeaway
Azure Vision enables scalable, AI-powered image understanding for text extraction, visual analysis, facial recognition, and digital asset management—making it a foundational service for vision-enabled applications.

# REST API 

*{endpoint}* :  The Cognitive Services endpoint, including protocol and hostname (for example, https://RESOURCE_NAME.cognitiveservices.azure.com).

## Face Detection Operations - Detect Endpoint

```
POST {endpoint}/face/{apiVersion}/detect
```
With optional parameters: 

```
POST {endpoint}/face/{apiVersion}/detect?_overload=detect&detectionModel={detectionModel}&recognitionModel={recognitionModel}&returnFaceId={returnFaceId}&returnFaceAttributes={returnFaceAttributes}&returnFaceLandmarks={returnFaceLandmarks}&returnRecognitionModel={returnRecognitionModel}&faceIdTimeToLive={faceIdTimeToLive}
```

## Face Recognition Operations - Find Similar From Large Face List Endpoint

```
POST {endpoint}/face/{apiVersion}/findsimilars
```

## Face Recognition Operations - Verify Face To Face Endpoint

Verify whether two faces belong to a same person.
```
POST {endpoint}/face/{apiVersion}/verify
```
## Face Recognition Operations - Identify From Large Person Group Endpoint

```
POST {endpoint}/face/{apiVersion}/identify
```

# Python SDK

## OCR Read (v3.2 GA)Example

```python
from azure.cognitiveservices.vision.computervision import ComputerVisionClient
from azure.cognitiveservices.vision.computervision.models import OperationStatusCodes
from azure.cognitiveservices.vision.computervision.models import VisualFeatureTypes
from msrest.authentication import CognitiveServicesCredentials

from array import array
import os
from PIL import Image
import sys
import time

'''
Authenticate
Authenticates your credentials and creates a client.
'''
subscription_key = os.environ["VISION_KEY"]
endpoint = os.environ["VISION_ENDPOINT"]

computervision_client = ComputerVisionClient(endpoint, CognitiveServicesCredentials(subscription_key))
'''
END - Authenticate
'''

'''
OCR: Read File using the Read API, extract text - remote
This example will extract text in an image, then print results, line by line.
This API call can also extract handwriting style text (not shown).
'''
print("===== Read File - remote =====")
# Get an image with text
read_image_url = "https://learn.microsoft.com/azure/ai-services/computer-vision/media/quickstarts/presentation.png"

# Call API with URL and raw response (allows you to get the operation location)
read_response = computervision_client.read(read_image_url,  raw=True)

# Get the operation location (URL with an ID at the end) from the response
read_operation_location = read_response.headers["Operation-Location"]
# Grab the ID from the URL
operation_id = read_operation_location.split("/")[-1]

# Call the "GET" API and wait for it to retrieve the results 
while True:
    read_result = computervision_client.get_read_result(operation_id)
    if read_result.status not in ['notStarted', 'running']:
        break
    time.sleep(1)

# Print the detected text, line by line
if read_result.status == OperationStatusCodes.succeeded:
    for text_result in read_result.analyze_result.read_results:
        for line in text_result.lines:
            print(line.text)
            print(line.bounding_box)
print()
'''
END - Read File - remote
'''

print("End of Computer Vision quickstart.")

```

## Image Analysis (4.0) Example

```python
import os
from azure.ai.vision.imageanalysis import ImageAnalysisClient
from azure.ai.vision.imageanalysis.models import VisualFeatures
from azure.core.credentials import AzureKeyCredential

# Set the values of your computer vision endpoint and computer vision key
# as environment variables:
try:
    endpoint = os.environ["VISION_ENDPOINT"]
    key = os.environ["VISION_KEY"]
except KeyError:
    print("Missing environment variable 'VISION_ENDPOINT' or 'VISION_KEY'")
    print("Set them before running this sample.")
    exit()

# Create an Image Analysis client
client = ImageAnalysisClient(
    endpoint=endpoint,
    credential=AzureKeyCredential(key)
)

# Get a caption for the image. This will be a synchronously (blocking) call.
result = client.analyze_from_url(
    image_url="https://learn.microsoft.com/azure/ai-services/computer-vision/media/quickstarts/presentation.png",
    visual_features=[VisualFeatures.CAPTION, VisualFeatures.READ],
    gender_neutral_caption=True,  # Optional (default is False)
)

print("Image analysis results:")
# Print caption results to the console
print(" Caption:")
if result.caption is not None:
    print(f"   '{result.caption.text}', Confidence {result.caption.confidence:.4f}")

# Print text (OCR) analysis results to the console
print(" Read:")
if result.read is not None:
    for line in result.read.blocks[0].lines:
        print(f"   Line: '{line.text}', Bounding box {line.bounding_polygon}")
        for word in line.words:
            print(f"     Word: '{word.text}', Bounding polygon {word.bounding_polygon}, Confidence {word.confidence:.4f}")
			
```

## Face Example

```python
import os
import time
import uuid

from azure.core.credentials import AzureKeyCredential
from azure.ai.vision.face import FaceAdministrationClient, FaceClient
from azure.ai.vision.face.models import FaceAttributeTypeRecognition04, FaceDetectionModel, FaceRecognitionModel, QualityForRecognition


# This key will serve all examples in this document.
KEY = os.environ["FACE_APIKEY"]

# This endpoint will be used in all examples in this quickstart.
ENDPOINT = os.environ["FACE_ENDPOINT"]

# Used in the Large Person Group Operations and Delete Large Person Group examples.
# LARGE_PERSON_GROUP_ID should be all lowercase and alphanumeric. For example, 'mygroupname' (dashes are OK).
LARGE_PERSON_GROUP_ID = str(uuid.uuid4())  # assign a random ID (or name it anything)

# Create an authenticated FaceClient.
with FaceAdministrationClient(endpoint=ENDPOINT, credential=AzureKeyCredential(KEY)) as face_admin_client, \
     FaceClient(endpoint=ENDPOINT, credential=AzureKeyCredential(KEY)) as face_client:
    '''
    Create the LargePersonGroup
    '''
    # Create empty Large Person Group. Large Person Group ID must be lower case, alphanumeric, and/or with '-', '_'.
    print("Person group:", LARGE_PERSON_GROUP_ID)
    face_admin_client.large_person_group.create(
        large_person_group_id=LARGE_PERSON_GROUP_ID,
        name=LARGE_PERSON_GROUP_ID,
        recognition_model=FaceRecognitionModel.RECOGNITION04,
    )

    # Define woman friend
    woman = face_admin_client.large_person_group.create_person(
        large_person_group_id=LARGE_PERSON_GROUP_ID,
        name="Woman",
    )
    # Define man friend
    man = face_admin_client.large_person_group.create_person(
        large_person_group_id=LARGE_PERSON_GROUP_ID,
        name="Man",
    )
    # Define child friend
    child = face_admin_client.large_person_group.create_person(
        large_person_group_id=LARGE_PERSON_GROUP_ID,
        name="Child",
    )

    '''
    Detect faces and register them to each person
    '''
    # Find all jpeg images of friends in working directory (TBD pull from web instead)
    woman_images = [
        "https://raw.githubusercontent.com/Azure-Samples/cognitive-services-sample-data-files/master/Face/images/Family1-Mom1.jpg",
        "https://raw.githubusercontent.com/Azure-Samples/cognitive-services-sample-data-files/master/Face/images/Family1-Mom2.jpg",
    ]
    man_images = [
        "https://raw.githubusercontent.com/Azure-Samples/cognitive-services-sample-data-files/master/Face/images/Family1-Dad1.jpg",
        "https://raw.githubusercontent.com/Azure-Samples/cognitive-services-sample-data-files/master/Face/images/Family1-Dad2.jpg",
    ]
    child_images = [
        "https://raw.githubusercontent.com/Azure-Samples/cognitive-services-sample-data-files/master/Face/images/Family1-Son1.jpg",
        "https://raw.githubusercontent.com/Azure-Samples/cognitive-services-sample-data-files/master/Face/images/Family1-Son2.jpg",
    ]

    # Add to woman person
    for image in woman_images:
        # Check if the image is of sufficent quality for recognition.
        sufficient_quality = True
        detected_faces = face_client.detect_from_url(
            url=image,
            detection_model=FaceDetectionModel.DETECTION03,
            recognition_model=FaceRecognitionModel.RECOGNITION04,
            return_face_id=True,
            return_face_attributes=[FaceAttributeTypeRecognition04.QUALITY_FOR_RECOGNITION],
        )
        for face in detected_faces:
            if face.face_attributes.quality_for_recognition != QualityForRecognition.HIGH:
                sufficient_quality = False
                break

        if not sufficient_quality:
            continue

        if len(detected_faces) != 1:
            continue

        face_admin_client.large_person_group.add_face_from_url(
            large_person_group_id=LARGE_PERSON_GROUP_ID,
            person_id=woman.person_id,
            url=image,
            detection_model=FaceDetectionModel.DETECTION03,
        )
        print(f"face {face.face_id} added to person {woman.person_id}")


    # Add to man person
    for image in man_images:
        # Check if the image is of sufficent quality for recognition.
        sufficient_quality = True
        detected_faces = face_client.detect_from_url(
            url=image,
            detection_model=FaceDetectionModel.DETECTION03,
            recognition_model=FaceRecognitionModel.RECOGNITION04,
            return_face_id=True,
            return_face_attributes=[FaceAttributeTypeRecognition04.QUALITY_FOR_RECOGNITION],
        )
        for face in detected_faces:
            if face.face_attributes.quality_for_recognition != QualityForRecognition.HIGH:
                sufficient_quality = False
                break

        if not sufficient_quality:
            continue

        if len(detected_faces) != 1:
            continue

        face_admin_client.large_person_group.add_face_from_url(
            large_person_group_id=LARGE_PERSON_GROUP_ID,
            person_id=man.person_id,
            url=image,
            detection_model=FaceDetectionModel.DETECTION03,
        )
        print(f"face {face.face_id} added to person {man.person_id}")

    # Add to child person
    for image in child_images:
        # Check if the image is of sufficent quality for recognition.
        sufficient_quality = True
        detected_faces = face_client.detect_from_url(
            url=image,
            detection_model=FaceDetectionModel.DETECTION03,
            recognition_model=FaceRecognitionModel.RECOGNITION04,
            return_face_id=True,
            return_face_attributes=[FaceAttributeTypeRecognition04.QUALITY_FOR_RECOGNITION],
        )
        for face in detected_faces:
            if face.face_attributes.quality_for_recognition != QualityForRecognition.HIGH:
                sufficient_quality = False
                break
        if not sufficient_quality:
            continue

        if len(detected_faces) != 1:
            continue

        face_admin_client.large_person_group.add_face_from_url(
            large_person_group_id=LARGE_PERSON_GROUP_ID,
            person_id=child.person_id,
            url=image,
            detection_model=FaceDetectionModel.DETECTION03,
        )
        print(f"face {face.face_id} added to person {child.person_id}")

    '''
    Train LargePersonGroup
    '''
    # Train the large person group and set the polling interval to 5s
    print(f"Train the person group {LARGE_PERSON_GROUP_ID}")
    poller = face_admin_client.large_person_group.begin_train(
        large_person_group_id=LARGE_PERSON_GROUP_ID,
        polling_interval=5,
    )

    poller.wait()
    print(f"The person group {LARGE_PERSON_GROUP_ID} is trained successfully.")

    '''
    Identify a face against a defined LargePersonGroup
    '''
    # Group image for testing against
    test_image = "https://raw.githubusercontent.com/Azure-Samples/cognitive-services-sample-data-files/master/Face/images/identification1.jpg"

    print("Pausing for 60 seconds to avoid triggering rate limit on free account...")
    time.sleep(60)

    # Detect faces
    face_ids = []
    # We use detection model 03 to get better performance, recognition model 04 to support quality for
    # recognition attribute.
    faces = face_client.detect_from_url(
        url=test_image,
        detection_model=FaceDetectionModel.DETECTION03,
        recognition_model=FaceRecognitionModel.RECOGNITION04,
        return_face_id=True,
        return_face_attributes=[FaceAttributeTypeRecognition04.QUALITY_FOR_RECOGNITION],
    )
    for face in faces:
        # Only take the face if it is of sufficient quality.
        if face.face_attributes.quality_for_recognition != QualityForRecognition.LOW:
            face_ids.append(face.face_id)

    # Identify faces
    identify_results = face_client.identify_from_large_person_group(
        face_ids=face_ids,
        large_person_group_id=LARGE_PERSON_GROUP_ID,
    )
    print("Identifying faces in image")
    for identify_result in identify_results:
        if identify_result.candidates:
            print(f"Person is identified for face ID {identify_result.face_id} in image, with a confidence of "
                  f"{identify_result.candidates[0].confidence}.")  # Get topmost confidence score

            # Verify faces
            verify_result = face_client.verify_from_large_person_group(
                face_id=identify_result.face_id,
                large_person_group_id=LARGE_PERSON_GROUP_ID,
                person_id=identify_result.candidates[0].person_id,
            )
            print(f"verification result: {verify_result.is_identical}. confidence: {verify_result.confidence}")
        else:
            print(f"No person identified for face ID {identify_result.face_id} in image.")

    print()

    # Delete the large person group
    face_admin_client.large_person_group.delete(LARGE_PERSON_GROUP_ID)
    print(f"The person group {LARGE_PERSON_GROUP_ID} is deleted.")

    print()
    print("End of quickstart.")
	
```

