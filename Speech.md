# Azure Speech Service — Summary

## Overview
The **Azure Speech service** provides powerful speech-to-text, text-to-speech, speech translation, and AI voice conversation capabilities through a Speech resource. It supports high-accuracy transcription, natural-sounding neural voices, multilingual translation, and live conversational experiences.

Speech can run in the cloud or at the edge (via containers) and integrates easily using the **Speech SDK**, **Speech CLI**, and **REST APIs**. It supports many languages, regions, and pricing tiers.

---

## Common Speech Scenarios
Typical use cases include:
- **Captioning**: Real-time captions, partial results, profanity filtering, and language detection.
- **Audio Content Creation**: Neural voices for chatbots, audiobooks, and navigation systems.
- **Call Centers**: Real-time or batch transcription, PII redaction, and sentiment analysis.
- **Language Learning**: Pronunciation assessment, transcription for remote learning, and read-aloud features.
- **Voice Live**: Natural, low-latency conversational interfaces between humans and AI agents.

Microsoft uses Speech across products like Teams (captioning), Office (dictation), and Edge (Read Aloud).

---

## Core Capabilities

### Speech to Text
- Real-time transcription for streaming audio
- Fast transcription for recorded files
- Batch transcription for large audio volumes  
- Support for **custom speech models** to improve accuracy in noisy or domain-specific audio

### Text to Speech
- Neural voices with human-like quality
- Fine-grained control using **SSML**
- **Standard voices** (out of the box)
- **Custom neural voices** for brand-specific experiences (private and unique)

### Speech Translation
- Real-time multilingual translation
- Supports speech-to-speech and speech-to-text scenarios

### LLM Speech (Preview)
- Enhanced speech models powered by large language models
- Supports:
  - `transcribe`
  - `translate`
- Optimized for captions, summaries, call center assistance, and voicemail transcription

### Language Identification
- Automatically detects spoken languages
- Can be used standalone or combined with transcription and translation

### Pronunciation Assessment
- Evaluates accuracy and fluency of spoken audio
- Designed for language learning and presentation practice

---

## Delivery and Deployment
- Available in **cloud**, **on-premises**, and **edge** environments
- Supports **containers** for compliance, security, and low-latency needs
- Available in **sovereign clouds** (e.g., Azure Government, Azure China)

---

## Using Speech in Your Applications
- **Speech Studio**: No-code UI tools for building and managing speech projects
- **Speech CLI**: Command-line access to most Speech features without writing code
- **Speech SDK**: Full-featured SDK available across languages and platforms
- **REST APIs**: Used when SDKs are not suitable (e.g., batch transcription)

---

## SSML (Speech Synthesis Markup Language) — Short Overview

### What is SSML?
SSML is an **XML-based language** used to control how text is spoken in text-to-speech, such as pitch, speed, volume, pronunciation, and voice style.

---

### What You Can Do with SSML
- Control pauses, breaks, and sentence structure.
- Choose voices, languages, styles, and roles.
- Adjust speaking rate, pitch, emphasis, and volume.
- Improve pronunciation using phonemes or custom lexicons.
- Insert audio effects or musical notes.

---

### How to Use SSML
- **Speech Studio (Audio Content Creation tool)**  
- **Batch Synthesis API**  
- **Speech CLI** with `--ssml`  
- **Speech SDK** using the SSML speak method

---

## Key Features

### Voice Control
- Select **voice name, language, style, and role**.
- Use **multiple voices** in one document.
- Apply audio effects (for example, optimize sound for cars or phone calls).
- Support for **custom voices** and **multi-talker voices** for dialogues.

### Speaking Styles & Roles
- Change emotional tone (cheerful, sad, calm, angry, etc.).
- Adjust intensity with **style degree**.
- Change role (child, young adult, senior, male/female).

### Language Control
- Multilingual voices can auto-detect language.
- Use `<lang xml:lang>` to force a specific **accent or language** (for example, en-GB, fr-FR).

### Prosody (Voice Sound)
- Adjust:
  - **Rate** (speed)
  - **Pitch**
  - **Volume**
  - **Contour** (intonation pattern)

### Emphasis
- Add word-level stress with **moderate, strong, reduced, or none** (supported by limited voices).

### SSML Roles and Styles — Examples

#### 🎭 Speaking Style Examples

Here are some common speaking styles you can use with `<mstts:express-as>`:

| Style | Description |
|------|-------------|
| `cheerful` | Happy, positive, upbeat tone |
| `calm` | Relaxed, steady, composed |
| `angry` | Annoyed or irritated tone |
| `empathetic` | Caring and understanding |
| `assistant` | Warm tone for virtual assistants |
| `newscast` | Formal, professional news voice |
| `customerservice` | Friendly and helpful support tone |
| `whispering` | Soft, quiet speaking style |
| `sports_commentary_excited` | Energetic sports announcer tone |

#### Example: Using a Speaking Style
```xml
<mstts:express-as style="cheerful">
    I'm really happy to see you today!
</mstts:express-as>

```
#### Role Examples

| Role               | Description        |
| ------------------ | ------------------ |
| `Girl`             | Child female voice |
| `Boy`              | Child male voice   |
| `YoungAdultFemale` | Young adult female |
| `YoungAdultMale`   | Young adult male   |
| `OlderAdultFemale` | Older adult female |
| `OlderAdultMale`   | Older adult male   |
| `SeniorFemale`     | Senior female      |
| `SeniorMale`       | Senior male        |

```xml
<mstts:express-as role="SeniorMale" style="calm">
    Welcome back. It’s good to see you again.
</mstts:express-as>
```

#### 🎛 Style Degree Example

Adjust how strong the emotion sounds:

```xml
<mstts:express-as style="sad" styledegree="2">
    I'm really going to miss you.
</mstts:express-as>
```


---

## Key Takeaway
Azure Speech enables scalable, customizable, and multilingual speech experiences—from transcription and translation to advanced AI-powered voice interactions—across cloud, edge, and enterprise environments.

# Rest API

## Speech to Text from file Endpoint

```
curl --location --request POST "https://%SPEECH_REGION%.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1?language=en-US&format=detailed" ^
--header "Ocp-Apim-Subscription-Key: %SPEECH_KEY%" ^
--header "Content-Type: audio/wav" ^
--data-binary "@YourAudioFile.wav"
```

## Text to Speech to a file Endpoint

```
curl --location --request POST "https://%SPEECH_REGION%.tts.speech.microsoft.com/cognitiveservices/v1" ^
--header "Ocp-Apim-Subscription-Key: %SPEECH_KEY%" ^
--header "Content-Type: application/ssml+xml" ^
--header "X-Microsoft-OutputFormat: audio-16khz-128kbitrate-mono-mp3" ^
--header "User-Agent: curl" ^
--data-raw "<speak version='1.0' xml:lang='en-US'><voice xml:lang='en-US' xml:gender='Female' name='en-US-Ava:DragonHDLatestNeural'>my voice is my passport verify me</voice></speak>" --output output.mp3
```

# Python SDK

## Speech to Text Example

```python
import os
import azure.cognitiveservices.speech as speechsdk

def recognize_from_microphone():
     # This example requires environment variables named "SPEECH_KEY" and "ENDPOINT"
     # Replace with your own subscription key and endpoint, the endpoint is like : "https://YourServiceRegion.api.cognitive.microsoft.com"
    speech_config = speechsdk.SpeechConfig(subscription=os.environ.get('SPEECH_KEY'), endpoint=os.environ.get('ENDPOINT'))
    speech_config.speech_recognition_language="en-US"

    audio_config = speechsdk.audio.AudioConfig(use_default_microphone=True)
    speech_recognizer = speechsdk.SpeechRecognizer(speech_config=speech_config, audio_config=audio_config)

    print("Speak into your microphone.")
    speech_recognition_result = speech_recognizer.recognize_once_async().get()

    if speech_recognition_result.reason == speechsdk.ResultReason.RecognizedSpeech:
        print("Recognized: {}".format(speech_recognition_result.text))
    elif speech_recognition_result.reason == speechsdk.ResultReason.NoMatch:
        print("No speech could be recognized: {}".format(speech_recognition_result.no_match_details))
    elif speech_recognition_result.reason == speechsdk.ResultReason.Canceled:
        cancellation_details = speech_recognition_result.cancellation_details
        print("Speech Recognition canceled: {}".format(cancellation_details.reason))
        if cancellation_details.reason == speechsdk.CancellationReason.Error:
            print("Error details: {}".format(cancellation_details.error_details))
            print("Did you set the speech resource key and endpoint values?")

recognize_from_microphone()

```

## Text to Speech Example

```python
import os
import azure.cognitiveservices.speech as speechsdk

# This example requires environment variables named "SPEECH_KEY" and "ENDPOINT"
# Replace with your own subscription key and endpoint, the endpoint is like : "https://YourServiceRegion.api.cognitive.microsoft.com"
speech_config = speechsdk.SpeechConfig(subscription=os.environ.get('SPEECH_KEY'), endpoint=os.environ.get('ENDPOINT'))
audio_config = speechsdk.audio.AudioOutputConfig(use_default_speaker=True)

# The neural multilingual voice can speak different languages based on the input text.
speech_config.speech_synthesis_voice_name='en-US-Ava:DragonHDLatestNeural'

speech_synthesizer = speechsdk.SpeechSynthesizer(speech_config=speech_config, audio_config=audio_config)

# Get text from the console and synthesize to the default speaker.
print("Enter some text that you want to speak >")
text = input()

speech_synthesis_result = speech_synthesizer.speak_text_async(text).get()

if speech_synthesis_result.reason == speechsdk.ResultReason.SynthesizingAudioCompleted:
    print("Speech synthesized for text [{}]".format(text))
elif speech_synthesis_result.reason == speechsdk.ResultReason.Canceled:
    cancellation_details = speech_synthesis_result.cancellation_details
    print("Speech synthesis canceled: {}".format(cancellation_details.reason))
    if cancellation_details.reason == speechsdk.CancellationReason.Error:
        if cancellation_details.error_details:
            print("Error details: {}".format(cancellation_details.error_details))
            print("Did you set the speech resource key and endpoint values?")
			
```

## Speech translation

```python
import os
import azure.cognitiveservices.speech as speechsdk

def recognize_from_microphone():
    # This example requires environment variables named "SPEECH_KEY" and "ENDPOINT"
    # Replace with your own subscription key and endpoint, the endpoint is like : "https://YourServiceRegion.api.cognitive.microsoft.com"
    speech_translation_config = speechsdk.translation.SpeechTranslationConfig(subscription=os.environ.get('SPEECH_KEY'), endpoint=os.environ.get('ENDPOINT'))
    speech_translation_config.speech_recognition_language="en-US"

    to_language ="it"
    speech_translation_config.add_target_language(to_language)

    audio_config = speechsdk.audio.AudioConfig(use_default_microphone=True)
    translation_recognizer = speechsdk.translation.TranslationRecognizer(translation_config=speech_translation_config, audio_config=audio_config)

    print("Speak into your microphone.")
    translation_recognition_result = translation_recognizer.recognize_once_async().get()

    if translation_recognition_result.reason == speechsdk.ResultReason.TranslatedSpeech:
        print("Recognized: {}".format(translation_recognition_result.text))
        print("""Translated into '{}': {}""".format(
            to_language, 
            translation_recognition_result.translations[to_language]))
    elif translation_recognition_result.reason == speechsdk.ResultReason.NoMatch:
        print("No speech could be recognized: {}".format(translation_recognition_result.no_match_details))
    elif translation_recognition_result.reason == speechsdk.ResultReason.Canceled:
        cancellation_details = translation_recognition_result.cancellation_details
        print("Speech Recognition canceled: {}".format(cancellation_details.reason))
        if cancellation_details.reason == speechsdk.CancellationReason.Error:
            print("Error details: {}".format(cancellation_details.error_details))
            print("Did you set the speech resource key and endpoint values?")

recognize_from_microphone()
```


