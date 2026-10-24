# Call Shield: Presentation Deck & Pitch Extract

> Comprehensive presentation extract, slide breakdown, speaker notes, and technical references for PowerPoint (PPT) / Google Slides.

---

## Slide 1: Title & Introduction

- **Title**: **Call Shield**
- **Subtitle**: Real-Time AI Voice Clone Defense & Acoustic Scam Interceptor
- **Tagline**: *Continuous, zero-retention acoustic security for live phone calls and voice recordings.*
- **Presenter**: [Your Name / Team Name]
- **Repository**: [Voice_clone_detector_01](https://github.com/utkarkumarbtech2024-cyber/Voice_clone_detector_01)
- **Key Visual**: Cyber-defense shield emblem with an audio waveform resolving into a verified padlock.

> **Speaker Notes**:  
> "Good morning/afternoon everyone. Today we are presenting Call Shield—a real-time acoustic defense system engineered to protect individuals, executives, and organizations from the fastest-growing form of social engineering: AI voice clones and deepfake phone scams."

---

## Slide 2: The Threat: AI Voice Cloning & Impersonation Scams

- **The Threat Landscape**:
  - State-of-the-art voice synthesis models can clone any voice using as little as **3 seconds of audio**.
  - Rapid surge in family emergency scams ("bail/kidnapping scams") and executive impersonation ("CEO wire transfer fraud").
  - Current telecom infrastructure has **zero verification** for acoustic or vocal authenticity.
- **Why Traditional Security Fails**:
  - Deepfakes attack the *human ear*, completely bypassing perimeter network firewalls and endpoint security.
  - Latency is critical: forensic analysis that takes minutes or hours is too late. Detection must happen **during the live phone call**.
- **Market Impact**:
  - Over **$1.1 Billion** lost globally to imposter and voice-clone scams in 2024–2025.
  - 83% of victims report being unable to distinguish between genuine caller voices and synthesized deepfakes.

> **Speaker Notes**:  
> "With modern generative AI, a scammer only needs a short Instagram story or voicemail to clone a person's voice. Once they dial in, the victim has no technical safety net. Traditional security models inspect data packets, but none inspect the acoustic signature of the voice in real time."

---

## Slide 3: The Solution: Call Shield Architecture

- **What Call Shield Does**:
  - Listens to the **remote party's audio** in real time during a call or browser session.
  - Continuously scores acoustic periodicity, spectral flatness, and vocal tract anomalies every second.
  - Issues actionable, progressive security states: `OK (Authentic)`, `WARNING`, or `ALERT`.
- **Core Pillars**:
  - **Telephony Native**: Directly ingests Twilio Media Streams (8 kHz µ-law telephony standard).
  - **Browser Native**: Web client with zero-latency `AudioWorklet` stream capture.
  - **In-Memory Forensics**: Universal audio file inspector for uploaded recordings (`.wav`, `.mp3`, `.m4a`, `.ogg`, `.flac`).
  - **Zero Audio Retention**: 100% in-memory processing—no audio is ever written to disk or logs.

> **Speaker Notes**:  
> "Call Shield acts as an intelligent acoustic firewall between the caller and the receiver. It analyzes the voice stream chunk-by-chunk and alerts the user the moment synthetic vocal patterns are detected."

---

## Slide 4: Real-Time Signal Processing Pipeline

```
[Inbound Audio Stream]
  ├── (A) Twilio PBX: 8 kHz G.711 µ-law
  └── (B) Browser Mic: 16 kHz PCM16
           │
           ▼
[Core Audio Engine (C++ / NumPy)]
  ├── ITU-T G.711 Decode (exact table-lookup verification)
  ├── FFT-Preserving Resampler (8 kHz -> 16 kHz without spectral leakage)
  ├── ITU-R BS.1770-like Loudness Normalization (-26 dBFS target)
  └── Repeat-Padding & Silence Gating (Threshold: -50 dBFS)
           │
           ▼
[Sliding Temporal Windows]
  ├── 4.0-Second Evaluation Window
  ├── 1.0-Second Stride / Hop
  └── First Evaluation at t = 2.0s
```

- **Off-Event-Loop Concurrency**: Neural scoring runs on dedicated worker threads (`asyncio.to_thread`), preventing WebSocket event-loop blocking.
- **Bounded Queues**: Old windows drop automatically if inference falls behind, ensuring near-instant zero-lag alerts.

> **Speaker Notes**:  
> "Under the hood, audio is ingested through dual channels: browser microphones or telephony PBX feeds. We resample, normalize, and slice the audio into sliding 4-second windows that are scored continuously without blocking the event loop."

---

## Slide 5: The Decision Engine & Scam Mitigation

- **$k$-out-of-$n$ Rolling Window Decision Logic**:
  - Evaluates the trailing $n = 6$ seconds of window classifications.
  - If flagged suspicious windows $\ge k$ ($k = 4$): **CRITICAL ALERT**.
  - If flagged suspicious windows $\ge k - 2$ ($2$): **ELEVATED WARNING**.
  - Otherwise: **OK (Authentic)**.
- **Latching Alert Mechanism**:
  - Attackers often intersperse silence or recorded human phrases to disrupt detection.
  - Call Shield **latches** the `ALERT` state once triggered, preventing a scammer from resetting the alert by pausing or playing background noise.
- **Actionable Guidance (Human-Centric Safety)**:
  - Avoids false certainty ("This is fake").
  - Prompts immediate defensive protocol:  
    *"This voice may be AI-generated. Hang up and call back on a number you know."*
  - Accompanied by haptic vibration on mobile devices.

> **Speaker Notes**:  
> "One-off classification is prone to false alarms. Call Shield implements a rolling 4-out-of-6 latching decider. Once an alert is triggered, it locks in for the duration of the call so the user is never tricked into false security by a brief pause."

---

## Slide 6: Product Showcase: Live Web Client & File Inspector

### 1. Live Voice Shield (Real-Time Mode)
- **Glassmorphic Cyber-Defense HUD**: Dark mode interface designed with Outfit, Inter, and JetBrains Mono typography.
- **60 FPS Canvas Sparkline**: Dynamic telemetry chart plotting rolling acoustic scores from authentic (+1.0) to synthetic (-1.0).
- **Interactive Verification**: One-click 440 Hz synthetic tone injection to verify live alert triggering and haptic response.

### 2. Audio Forensic Inspector (Upload Mode)
- **Universal Format Transcoding**: Drag and drop WAV, MP3, M4A, OGG, or FLAC clips. Client-side Web Audio API transcode handles decoding in browser memory.
- **Interactive Timeline Bar Chart**: Visual risk progression showing each 1-second evaluation window color-coded (Emerald / Amber / Crimson).
- **Click-to-Seek Synchronization**: Clicking any bar on the timeline instantly seeks the audio player to that exact second.
- **Diagnostics Table**: Collapsible window-by-window audit table showing exact raw scores, threat levels, and anomaly flags.
- **Instant Test Presets**: Built-in 1-click test clips (**🟢 Authentic Voice**, **⚠️ Mixed Phone Call**, **🚨 AI Voice Clone**).

> **Speaker Notes**:  
> "Our frontend gives users two distinct capabilities: a live listening shield for incoming calls, and a forensic file inspector where any recorded call, voicemail, or audio snippet can be dropped for an immediate deep breakdown."

---

## Slide 7: Enterprise Security & Privacy by Design (Phase 7)

- **Zero Disk Persistence**:
  - Rigorously verified via automated pre/post directory snapshot testing: **zero audio bytes or temporary state are ever written to disk or the OS temp directory**.
- **Log Privacy & Sanitization**:
  - Server telemetry logs record only mathematical scores, timestamps, and threat levels.
  - Automated tests guarantee raw waveforms, base64 payloads, and auth tokens never leak into server logs.
- **Denial-of-Service & Resource Protections**:
  - `MAX_FRAME_BYTES` (64 KB): Oversized frames close connection with WebSocket Policy Violation (code `1008`).
  - `MAX_SESSION_SECONDS` (1 hour): Maximum duration cap protects against orphan or lingering streaming connections.
  - `MAX_SESSIONS` (100 concurrent): Concurrency ceiling with polite overload rejection (code `1013`).
  - **Graceful Lifespan Shutdown**: On server shutdown, all open WebSockets close cleanly with code `1001` and active background inference tasks cancel immediately.

> **Speaker Notes**:  
> "In cybersecurity, privacy is non-negotiable. Many detection tools record audio to third-party clouds, creating massive wiretapping and GDPR liabilities. Call Shield processes audio entirely in volatile memory and discards it immediately after scoring."

---

## Slide 8: Development Rigor & Engineering Metrics

- **Strict Test-Driven Development (TDD)**:
  - Completed **Phases 0 through 7** under strict RED-GREEN-REFACTOR protocol.
- **Automated Test Results**:
  - **70 Backend Integration & Unit Tests** passing in Pytest.
  - **8 Frontend Logic Tests** passing in Node.js test runner (`node:test`).
  - **Core Code Coverage**: **99.59%** on `src/callshield/core` (exceeding the 85% requirement).
- **Code Quality & Architecture**:
  - `ruff check`: 100% clean (0 lint violations across the entire repo).
  - `mypy`: 100% strict type safety across all 35 source files.
  - Pluggable Detector Factory: Decoupled interface supporting both deterministic test doubles (`FakeDetector`) and neural networks (`TorchDetector`).

> **Speaker Notes**:  
> "This isn't a quick mockup—it is an enterprise-engineered codebase with 99.6% test coverage, strict typing, zero disk leaks, and decoupled interfaces ready for production model drops."

---

## Slide 9: Project Roadmap & What’s Next

- **Phase 8: Real PyTorch Model Integration**:
  - Drop in pretrained neural weights (`best.pt`) based on AASIST / RawNet / Wav2Vec2 architectures.
- **Phase 9: Calibration & Evaluation**:
  - Sliding-window scoring, Equal Error Rate (EER) minimization, and false-alarm rate tuning on real-world telephony datasets.
- **Phase 10: Multi-Modal Scam Risk Fusion**:
  - Combine acoustic clone detection with real-time speech-to-text (ASR) to flag social engineering scripts (*"OTP"*, *"Urgent wire transfer"*, *"Arrest warrant"*).
  - Composite Risk Model:
    $$\text{Threat} = 0.5(\text{Voice Spoof}) + 0.4(\text{Scam Language}) + 0.1(\text{Unknown Caller})$$
- **Phase 11: Production Packaging & Mobile App**:
  - Native Android/iOS dialer hook and Dockerized carrier-grade microservice.

> **Speaker Notes**:  
> "Our immediate next step is dropping in our trained neural network weights in Phase 8, followed by multi-modal fusion in Phase 10 where acoustic scores combine with scam language analysis for maximum fraud protection."

---

## Slide 10: Summary & Key Takeaways

1. **Real-Time Defense**: Intercepts cloned voices during the call, before damage is done.
2. **Zero Retention**: 100% in-memory processing guarantees call privacy and regulatory compliance.
3. **Dual Capability**: Live phone/microphone shield + forensic audio file inspector.
4. **Production-Ready Core**: 78 tests passing, 99.6% core coverage, fully version-controlled on GitHub.

- **GitHub Repository**: [Voice_clone_detector_01](https://github.com/utkarkumarbtech2024-cyber/Voice_clone_detector_01)
- **Local Interactive Demo**: `http://localhost:8000`
- **CLI Call Simulator**: `python -m callshield.tools.simulate_call --wav tests/fixtures/mixed.wav`

---

## Appendix: Quick Technical Reference

| Parameter | Specification | Purpose |
| :--- | :--- | :--- |
| **Input Sample Rate** | 8000 Hz (Phone) / 16000 Hz (Mic) | Standard telephony / browser audio |
| **Model Sample Rate** | 16000 Hz (Mono Float32) | Acoustic feature extraction |
| **Evaluation Window** | 4.0 Seconds | Capture sufficient pitch & formant cycles |
| **Window Hop / Stride** | 1.0 Second | Update cadence for live UI |
| **Decision Parameters** | $k=4, n=6$ | 4 suspicious windows out of last 6 trigger ALERT |
| **Alert Behavior** | Latching for call session | Prevents alert reset by attacker pause |
| **Loudness Normalization**| -26 dBFS Target RMS | Gain-invariant classifier scoring |
| **Silence Gate** | -50 dBFS Threshold | Suppresses scoring during pauses |
