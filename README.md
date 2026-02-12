# Kashmiri Multilingual Audio Intelligence Agent

An offline multilingual audio intelligence system designed to analyze noisy real-world speech recordings containing Kashmiri, Hindi, and Urdu — often mixed within the same conversation — and convert them into structured, actionable intelligence.

The system functions as an autonomous triage agent that processes, analyzes, and prioritizes recordings without human intervention by generating structured intelligence reports and risk classifications.

---

## Core Capabilities

### 1. Speech Recognition (Offline)
- Processes noisy field audio
- Handles Hindi and Urdu robustly using multilingual modeling
- Handles Kashmiri using multilingual approximation (low-resource language support)
- Uses Faster-Whisper (Whisper large-v3, int8 CPU mode) for robust multilingual transcription
- Detects primary spoken language within each recording
- Generates timestamped transcript segments

Note: Kashmiri is a low-resource language. Performance depends on multilingual generalization rather than language-specific fine-tuning.


### 2. Automatic Translation
- Converts transcripts into clear English
- Uses Whisper translation mode and an additional MarianMT multilingual-to-English model for normalization


### 3. Context-Aware Intelligence Extraction

The system performs rule-based NLP extraction of structured intelligence:

| Category | Extracted Information |
|---------|-----------------------|
| People | Names mentioned in conversation |
| Locations | Cities, regions, landmarks |
| Time References | Dates, times, schedules |
| Threat Keywords | Words such as attack, blast, weapon |
| Flagged Individuals | Names that co-occur with threat-related language |

Threat detection is based on customizable keyword matching rather than semantic embedding or contextual sentiment modeling.


### 4. Risk Assessment Engine

Risk classification is determined using rule-based scoring combining threat keyword presence and sensitive location matching.

| Risk Level | Criteria |
|-----------|----------|
| HIGH | Threat keywords present + sensitive location match |
| MEDIUM | Threat keywords present |
| LOW | No threat indicators |

The scoring system is deterministic and threshold-based. It does not use machine learning classification.

---

## System Architecture

```
Audio Input
   ↓
Preprocessing (Noise handling + WAV conversion)
   ↓
Speech-to-Text (Faster-Whisper)
   ↓
Primary Language Detection
   ↓
Translation to English
   ↓
NLP Analysis (NER + Keyword Matching)
   ↓
Individual Flagging Logic
   ↓
Rule-Based Risk Scoring
   ↓
Structured Intelligence Report
```

---

## Project Structure

```
KASHMIR_AUDIO_AGENT/
│
├── app.py                        # Streamlit UI entry point
├── requirements.txt              # Project dependencies
├── README.md
│
├── audio_samples/                # Example/test audio files
│
├── data/
│   ├── threat_keywords.txt       # Static threat keyword list
│   └── sensitive_locations.txt   # Sensitive region list
│
├── hf_cache/                     # HuggingFace model cache (created after first run)
│
├── src/
│   ├── preprocess.py             # Audio conversion & cleanup
│   ├── transcribe.py             # Whisper transcription engine
│   ├── translate.py              # MarianMT translation module
│   ├── keywords.py               # Keyword-based threat detection
│   ├── ner.py                    # Named Entity Recognition (spaCy)
│   ├── risk_agent.py             # Rule-based risk scoring & flagging logic
│   └── report.py                 # Transcript highlighting
│
├── temp/                         # Temporary audio processing files
└── venv/                         # Virtual environment (recommended)
```

---

## How It Works (Step-by-Step)

### 1. Audio Preprocessing
Audio files are converted to:
- Mono channel
- 16kHz sample rate

This standardization improves transcription consistency.

### 2. Speech Recognition
Faster-Whisper (large-v3, int8 CPU mode) performs:
- Multilingual transcription
- Primary language detection
- Voice Activity Detection (VAD)
- Timestamped segment generation

### 3. Translation
Transcripts are translated into English using:
- Whisper translation mode
- MarianMT multilingual-to-English model for normalization

### 4. Threat Keyword Detection
Transcript text is scanned against a static, customizable keyword list.  
Detection is literal string matching.

### 5. Named Entity Recognition (NER)
Using spaCy multilingual model:
- PER → People
- LOC/GPE → Locations
- DATE/TIME → Time references

### 6. Individual Flagging
Individuals are flagged for contextual review when their names co-occur with threat-related language.

Co-occurrence does not imply guilt. Flagging is purely heuristic and designed for analyst prioritization.

### 7. Risk Scoring
A deterministic scoring mechanism combines:
- Presence of threat keywords
- Matching against predefined sensitive locations

Final output is LOW, MEDIUM, or HIGH risk.

---

## User Interface

Built with Streamlit for offline desktop usage:

- Upload multiple audio files
- View English transcript
- Highlight threat keywords
- Display structured JSON intelligence report
- Batch risk summary across files

The system operates autonomously once files are uploaded.

---

## Sample Output

The system produces structured intelligence reports and batch risk summaries for analyst review.

<p align="center">
  <img src="sample_output_images/img1.png" width="48%" />
  <img src="sample_output_images/img2.png" width="48%" />
</p>



---

## Installation

### 1. Clone the Repository

   ```bash
   git clone <repo-link>
   cd kashmir-audio-agent
   ```

### 2. Create a Virtual Environment (Recommended)

a. On Windows:

   ```bash
   python -m venv venv
   venv\Scripts\activate
   ```

b. On macOS / Linux:

   ```bash
   python3 -m venv venv
   source venv/bin/activate
   ```

### 3. Upgrade pip

   ```bash
   python -m pip install --upgrade pip
   ```

### 4. Install Dependencies

   ```bash
   pip install -r requirements.txt
   ```

### 5. Install spaCy Model

   ```bash
   python -m spacy download xx_ent_wiki_sm
   ```

### 6. First-Run Model Download

Internet is required only for the initial download of:

- Faster-Whisper large-v3
- MarianMT multilingual-to-English model

These models are cached locally inside `hf_cache/`.  
All subsequent executions operate fully offline.

---

## Run the Application

Ensure your virtual environment is activated, then run:

   ```bash
   streamlit run app.py
   ```

Streamlit will provide a local URL (typically `http://localhost:8501`) in the terminal.

---

## Offline Design

- Requires internet only for initial model download
- Fully offline after caching
- CPU-optimized (int8 mode)
- Designed for low-connectivity environments

---

## Limitations

- Kashmiri is a low-resource language; transcription quality may vary.
- Mixed-language code-switching is not explicitly segmented.
- Threat detection is keyword-based and not semantic.
- Risk scoring is rule-based and not ML-classified.
- No speaker identification or verification.
- Performance may degrade under heavy background noise.

---

## Use Cases

- Audio triage for multilingual environments
- Preliminary intelligence prioritization
- Structured analysis of mixed-language recordings
- Offline threat keyword monitoring

---

## Disclaimer

This tool provides automated prioritization and risk estimation for analyst review.  
It does not determine guilt, intent, or legal responsibility.

---

By Jairaj R, Ham P R, Ajo Jose
