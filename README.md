# Monolith

## Project Overview

**Monolith** is a modular system engineered to perform **melodic core extraction** from any musical audio file.

The system processes **polyphony** (multiple simultaneous sounds, instruments, and voices) within an audio track and reduces it to a strict **monophony**. The output is a clean sequence of notes, optimized for execution as a **solo** by a single instrument.

## Outputs and Applications

Monolith aims to deliver three primary artifacts from an input audio track:

1.  **Synthesized Solo:** An audio file (`.wav` or `.mp3`) containing the generated solo, rendered with the timbre of the user-specified instrument.
2.  **MIDI Transcription:** A clean, **monophonic** `MIDI` file for use in Digital Audio Workstations (DAWs) and notation software.
3.  **Structured Representation:** Text/JSON data containing the sequence of notes, duration, and pitch for analysis and study.

## Processing Architecture (Four Phases)

The transformation process is executed across four sequential phases utilizing **Digital Signal Processing (DSP)** and **Machine Learning (ML)** techniques:

### Phase 1: Source Separation (Stem Extraction)
* **Function:** Isolation of the audio track containing the primary melodic component (typically the main vocal).
* **Method:** Utilizing Deep Learning models (such as **Demucs**) to decompose the mixed track into isolated instrumental *stems*.
* **Output:** A clean, isolated audio track containing the vocal/melody.

### Phase 2: Automatic Music Transcription (AMT)
* **Function:** Conversion of the isolated audio signal into a sequence of discrete musical data.
* **Method:** Applying **Pitch Tracking ($\text{F}_0$ Tracking)** algorithms for fundamental frequency detection, followed by **Quantization** to map frequencies to the notes of the equal-tempered scale, defining the *onset* and *duration* of each event.
* **Output:** An initial data structure of **MIDI** events (Note, Time, Duration).

### Phase 3: Monophonization Engine
* **Function:** Ensuring strict monophony and melodic coherence of the resulting solo.
* **Method:** Applying filtering logic to resolve **conflicts from overlapping notes** (residual polyphony from the transcription). The engine prioritizes the note with the highest **velocity (volume)** and/or **duration** at any given time instant. Optionally, it applies smoothing to **melodic leaps** (large intervals) to optimize playability.
* **Output:** The final, guaranteed monophonic MIDI file.

### Phase 4: Synthesis and Return
* **Function:** Rendering the solo to audio and formatting the data output for the user.
* **Method:** The clean monophonic MIDI file is processed by a **Software Synthesizer** (`Fluidsynth`) using a specific **Sound
