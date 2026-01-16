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

## Key Takeaway
Azure Speech enables scalable, customizable, and multilingual speech experiences—from transcription and translation to advanced AI-powered voice interactions—across cloud, edge, and enterprise environments.

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


