---
title: "Move Collector"
excerpt: "Move Collector is a privacy-first iOS app built as Larissa's bachelor's thesis (PFG): it turns an iPhone into a portable motion research instrument, continuously capturing accelerometer, gyroscope, and GPS data and using on-device machine learning to automatically segment the stream into meaningful movement episodes — with every byte kept on the device.<br/><img src='/images/MoveCollector.png' style='width:100%; max-width:900px; height:auto;'>"
collection: portfolio
---

![Move Collector](/images/MoveCollector.png)

# Bachelor's Thesis (PFG): Larissa's On-Device Motion Analytics App, "Move Collector"

## Project Overview

For her undergraduate final project (Projeto Final de Graduação), Larissa set out to answer a research question at the intersection of mobile engineering and machine learning: *Can a smartphone continuously collect rich human-motion data and automatically make sense of it — entirely on-device, without ever sending data to a server?*

The result is **Move Collector**, a privacy-first iOS app that turns an iPhone into a portable motion research instrument. It continuously captures accelerometer, gyroscope, and GPS data as a user moves through their day, then uses on-device machine learning to automatically segment that continuous stream into meaningful "movement episodes" — all while keeping every byte of data on the device.

## "Move Collector"

Move Collector is a research-grade data collection and analysis app built for continuous, real-world use. The user simply starts a session and goes about their day; the app records multi-sensor data in the background — even after being closed or terminated by the system. When the session ends, a single tap runs an on-device ML pipeline that groups the raw signal into distinct activity episodes, which the user can explore on interactive charts and maps, annotate with their voice, and export as CSV for offline research.

The core design principle throughout was **privacy by default**: no backend, no cloud, no accounts. Every stage — from sensor capture to neural inference to clustering — runs locally on the iPhone's Neural Engine and CPU.

## Key Features and Motivations

- **Continuous Background Collection:** The app records accelerometer and gyroscope data at 20 Hz and GPS at ~1 Hz, using Apple's `BGContinuedProcessingTask` to keep collecting even when the app is backgrounded or the device is locked. Sessions automatically survive app kills and system termination, with recovery on next launch — a critical requirement for multi-hour, real-world data collection.
- **On-Device Activity Segmentation:** Rather than asking users to manually label their activities, Move Collector automatically segments a session into episodes using machine learning. This makes the raw firehose of sensor data interpretable and turns hours of signal into a handful of meaningful, reviewable events.
- **Privacy-First Architecture:** Motivated by the sensitivity of location and movement data, the entire pipeline is on-device. This design decision shaped every engineering choice and demonstrates that sophisticated ML analysis doesn't require sacrificing user privacy.
- **Interactive Exploration & Labeling:** Users review their sessions through synchronized time-series charts (accelerometer, gyroscope, GPS) and a map that traces their trajectory, color-coded by episode. Episodes can be labeled hands-free using voice notes, transcribed on-device — making annotation fast and natural.
- **Research Reproducibility:** Because the app was built as a thesis, a central goal was scientific rigor. The Swift implementation was engineered to exactly reproduce a Python research pipeline (normalization, windowing, embeddings, clustering), with unit tests validating mathematical parity between the two.

## Technologies Used

### CoreML and Accelerate (On-Device ML Pipeline)

- **Time-Frequency Embeddings:** Sensor data is split into 15-second windows and fed through partitioned CoreML "TFC" backbone models (separate models for accelerometer, gyroscope, and GPS). Each window is transformed into both a time-domain and frequency-domain representation — the latter computed via FFT using Apple's Accelerate/vDSP framework — and encoded into 768-dimensional embeddings.
- **Hierarchical Clustering:** A custom implementation of adjacent-pair Ward hierarchical clustering groups consecutive windows into episodes, with the user able to choose how many episodes (K) to segment a session into. This algorithm was reimplemented from scratch in Swift to match the reference Python research code.
- **Neural Engine Acceleration:** Inference runs on the Apple Neural Engine with CPU fallback, and a dedicated benchmarking module measured inference latency (ANE vs. CPU), battery impact, and memory usage across the full pipeline.

### Apple Frameworks

- **SwiftUI:** Powers the entire modern, dark-themed interface, including live sensor readouts, session recovery, and onboarding.
- **CoreMotion & CoreLocation:** Drive high-frequency motion sampling and background-capable GPS tracking.
- **BackgroundTasks:** Enables extended, uninterrupted background data collection with progress tracking and graceful expiration handling.
- **Core Data:** Persists sensor and location readings using dedicated background write contexts and batched writes to handle high-throughput streaming without blocking the UI.
- **Swift Charts & MapKit:** Render multi-panel time-series visualizations and episode-colored trajectory maps.
- **AVFoundation & Speech:** Capture per-episode voice notes and transcribe them on-device (PT-BR) for effortless labeling.

### Engineering Highlights

- Multi-threaded architecture with dedicated serial queues and thread-safe GPS snapshot caching to safely coordinate 20 Hz sampling, ML inference, and persistence.
- Streaming CSV export using a two-pointer merge of sensor and GPS streams, enabling export of multi-hour sessions without loading everything into memory.
- ~8,600 lines of Swift across 40 files, tested with XCTest unit and UI tests.

## Screenshots

![Move Collector — live collection, segmented episodes, and trajectory map](/images/MoveCollector.png)

## Impact and Recognition

Move Collector was developed as Larissa's bachelor's thesis, demonstrating end-to-end ownership of a complex iOS system — from low-level sensor management and background execution to on-device machine learning and data visualization. The project showcases her ability to translate research-grade requirements into a robust, privacy-preserving mobile application, bridging the gap between academic ML pipelines and production-quality iOS engineering.
