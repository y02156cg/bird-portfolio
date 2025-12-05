# E-Portfolio Guide: Bird Sound Classification Project
## For Google Sites

---

## Recommended Site Structure

### 🏠 HOME PAGE
**Title:** Automated Bird Sound Identification System

**Header Image:** Use a nature/bird-themed banner image

**Welcome Text:**
> Welcome to our e-portfolio showcasing the development of an open-world bird sound classification system. This project addresses a critical challenge in biodiversity monitoring: identifying known bird species while detecting potentially unknown species from acoustic field data.

**Team Members:**
- Trevor Zhang
- Shuyi Jin  
- Chi Le
- Shuyang Zhang
- Chen Gong
- Jiang Li

**Quick Links Section:**
- [GitHub Repository](https://github.com/charleschile/bird-sound-denoising)
- Dataset: BirdCLEF-2023
- Demo Video: [Add your link]

**Key Achievement Highlight Box:**
| Metric | Result |
|--------|--------|
| Validation Accuracy | 77% |
| Species Classified | 264 |
| Processing Time | ~1 second per 10s clip |
| Model Size | ~36 MB |

---

### 📋 PAGE 1: PROBLEM STATEMENT

**Page Title:** The Challenge We're Solving

**Section 1: The Problem**
Automated bird sound identification systems play a crucial role in biodiversity monitoring by enabling large-scale analysis of acoustic field data. However, existing models operate under a **closed-world assumption**—they only recognize species in their training sets and often misclassify unfamiliar calls as known species with high confidence.

**Section 2: Why It Matters**
Create a visual callout box with three key issues:

1. **Silent Misclassification** - Unknown species labeled as known with false confidence
2. **Discovery Barriers** - Rare or new species go undetected in monitoring data  
3. **Poor Transferability** - Models fail across different geographic regions

**Section 3: Our Solution**
We propose developing a classification system capable of both:
- ✅ Accurately identifying known bird species
- ✅ Detecting out-of-distribution sounds that may correspond to rare or previously unrecorded species

**Embed:** Add a diagram showing the closed-world vs. open-world classification concept

---

### 👥 PAGE 2: TARGET USERS

**Page Title:** Who Benefits From This System

**Primary Users Section:**

**Ecologists & Ornithologists**
- Automatically classify large datasets of bird vocalizations from field sensors
- Identify known species efficiently
- Flag potential unknown or rare species for further study

**Biodiversity Monitoring Agencies**
- Integrate into long-term monitoring programs
- Improve species detection accuracy
- Track population changes more reliably

**Secondary Stakeholders Section:**

| Stakeholder | Benefit |
|-------------|---------|
| Conservation Teams | Prioritize habitat protection for rare species |
| Citizen Scientists | Contribute acoustic data, increase geographic coverage |
| Policy Makers | Inform habitat management and biodiversity protection policies |

**Integration with Other Systems:**
- Forest Flying & Mother Drone teams → Analyze drone-collected audio
- Bug Attractor & Dung Drone teams → Filter bird sounds from background noise
- ID Birds & Bird Sound to Report teams → Enhance identification accuracy
- Smart Pollinator → Differentiate bird calls from pollinator sounds

---

### 📊 PAGE 3: DESIGN CRITERIA

**Page Title:** Design Objectives & Constraints

**Design Criteria Table:**

| Criterion | Type | Target | Achieved |
|-----------|------|--------|----------|
| Classification Accuracy | Objective | ≥75% on validation | ✅ 77% with EfficientNet-B2 |
| Processing Time | Constraint | ≤2s per 10s clip | ✅ ~1s GPU inference |
| Model Size | Constraint | ≤100 MB | ✅ ~36 MB |
| Noise Robustness | Objective | ≥70% under noise | ✅ Band-pass + noise reduction |
| Energy Efficiency | Constraint | ≤10W inference | ✅ Suitable for edge devices |

**How We Developed These Criteria:**
- Literature review of existing bird sound classification research
- Stakeholder interviews with ecologists and drone operation teams
- Analysis of real-world biodiversity monitoring requirements

---

### 🔧 PAGE 4: SYSTEM ARCHITECTURE

**Page Title:** Technical Design & Pipeline

**Pipeline Overview Diagram:**
```
Raw Audio → Block A (Preprocessing) → Block B (Mel-Spec) → Block C (Dataset) → Block D (Model) → Block E (Evaluation) → Predictions
```

**Block A: Audio Preprocessing**
- Sample rate: 32 kHz
- Band-pass filter: 1,000–12,000 Hz
- Optional spectral-gating noise reduction
- Fixed 10-second windows

**Block B: Mel Spectrogram Features**
- 256 mel frequency bins
- Frequency range: 80–15,000 Hz
- Log compression applied
- Per-sample normalization

**Block C: Dataset Management**
- 14,620 high-quality samples
- 264 bird species
- Stratified 80/20 train/validation split
- Batch size: 24

**Block D: EfficientNet-B2 Model**
- ImageNet pretrained weights
- 1→3 channel replication for spectrogram input
- 264-class linear classifier head

**Block E: Training Configuration**
- Optimizer: Adam (lr = 3×10⁻⁴)
- Loss: BCEWithLogitsLoss
- Schedule: Cosine annealing
- Epochs: 15

**Embed:** System architecture diagram from your report

---

### 🧪 PAGE 5: MODEL SELECTION

**Page Title:** Choosing the Best Approach

**Candidate Solutions Evaluated:**
1. Baseline CNN (3-layer ConvNet)
2. EfficientNet-B0 (no preprocessing)
3. **EfficientNet-B0 + Band-pass + Noise Reduction** ⭐ SELECTED
4. CRNN (CNN + BiLSTM)
5. PANNs CNN14
6. Lightweight Audio Transformer
7. Ensemble (EfficientNet + PANNs)

**Pugh Matrix Results:**

| Candidate | Performance | Robustness | Compute | Complexity | Reproducibility | Total |
|-----------|-------------|------------|---------|------------|-----------------|-------|
| C1 Baseline | 0 | 0 | 0 | 0 | 0 | 0 |
| C2 EffNet-B0 | +1 | 0 | -1 | 0 | 0 | 0 |
| **C3 EffNet+BP+NR** | **+1** | **+1** | **0** | **0** | **0** | **+2** |
| C4 CRNN | +1 | 0 | -1 | -1 | -1 | -2 |
| C5 PANNs | +1 | +1 | -1 | -1 | -1 | -1 |
| C6 Transformer | +1 | 0 | -1 | -1 | -1 | -2 |
| C7 Ensemble | +1 | +1 | -1 | -1 | -1 | -1 |

**Why We Chose Candidate 3:**
- Clear performance and robustness advantage
- Incorporates our unique contribution (preprocessing pipeline)
- Moderate implementation complexity
- Computationally feasible on Kaggle GPUs
- Easy to explain: "noisy waveform → cleaned waveform → log-mel → EfficientNet → predicted species"

---

### 📈 PAGE 6: PROTOTYPE EVOLUTION

**Page Title:** From Baseline to Final System

**Prototype A: Minimal Baseline**
- 2,000 samples, 5-second window, 128 mel bins
- EfficientNet-B0, no preprocessing
- **Result: <10% accuracy** ❌
- **Lesson:** Short windows miss complete vocalizations

**Prototype B: Intermediate System**
- 4,000 samples, 5-second window, 256 mel bins
- EfficientNet-B2, better normalization
- **Result: ~7% accuracy** ❌
- **Lesson:** Need full dataset and band-pass filtering

**Final System: Production Ready**
- 14,620 samples, 10-second window, 256 mel bins
- EfficientNet-B2 + band-pass + noise reduction
- **Result: 77% accuracy** ✅
- Confidence scores consistently >0.97 for correct predictions

**Key Insights:**
| Factor | Impact |
|--------|--------|
| Longer Context (10s) | Captures complete bird calls |
| More Data (14K+) | Better generalization |
| Preprocessing | Critical for noise robustness |
| Model Capacity | 264 classes need B2 |

**Embed:** Training curves showing loss/accuracy improvement

---

### ✅ PAGE 7: TESTING PLAN

**Page Title:** Validation & Quality Assurance

**Testing Framework Overview:**

**Test 1: Preprocessing Pipeline**
- Band-pass filter validation with synthetic signals
- SNR improvement measurement (target: ≥3dB)
- Mel spectrogram dimension verification
- 30 trials (10 synthetic + 20 real audio)

**Test 2: Dataset Integrity**
- Label encoding correctness (264 classes)
- File loading success rate (≥99%)
- Augmentation behavior validation

**Test 3: Model Training**
- Forward/backward pass stability
- Gradient flow monitoring (no NaN/Inf)
- Loss convergence over 15 epochs
- 5 repeated runs

**Test 4: Classification Performance**
- Top-1 accuracy on validation set (target: ≥80%)
- Confidence score distributions
- 3 repeated evaluations

**Test 5: Inference System**
- End-to-end latency (target: <500ms per window)
- GPU memory stability
- Sliding window consistency
- 50 window trials + 5 long-audio trials

**Test 6: Robustness**
- Performance under varying SNR (0-20 dB)
- Edge cases: silent clips, short clips
- 40 trials across conditions

---

### 📅 PAGE 8: PROJECT TIMELINE

**Page Title:** Development Schedule

**Project Duration:** September 2 – November 25, 2025 (12 weeks)

**Gantt Chart / Timeline Visual:**

| Phase | Weeks | Description |
|-------|-------|-------------|
| Research & Planning | 1-2 | Literature review, architecture evaluation |
| Data Preparation | 3-4 | Dataset collection, preprocessing pipeline |
| Model Development | 5-8 | Denoising and classification model training |
| Integration & Testing | 9-10 | System integration, comprehensive testing |
| Documentation | 11-12 | Final reporting, demo preparation |

**Milestones:**
- M1: Architecture confirmed
- M2: Dataset ready (10K+ samples)
- M3: Models achieve targets
- M4: System passes all tests
- M5: Final deliverables complete

**Tech Stack:**
- Python 3.8+ with PyTorch
- librosa, scipy, noisereduce
- timm library for EfficientNet
- Kaggle Tesla T4 GPU

**Embed:** Gantt chart image from your report

---

### 🖼️ PAGE 9: VISUALIZATIONS & RESULTS

**Page Title:** Data Analysis & Outputs

**Include these visualizations:**

1. **Mel Spectrogram Examples**
   - Raw audio vs. preprocessed audio comparison
   - Species-specific frequency patterns

2. **Training Curves**
   - Training loss vs. validation loss over 15 epochs
   - Validation accuracy progression (11.8% → 77.1%)

3. **Window-level Predictions**
   - Probability plots over time
   - Confidence score distributions

4. **Sample Predictions Table**
   - Filename, True Label, Predicted Label, Probability

5. **System Demo Screenshot**
   - Web interface for uploading and classifying bird audio

**Embed:** All figures and diagrams from your report

---

### 💭 PAGE 10: REFLECTIONS & FUTURE WORK

**Page Title:** Lessons Learned & Next Steps

**Key Insights:**
1. Signal-level preprocessing is critical—often rivaling model choice in impact
2. Loosely coupled pipeline enables parallel team development
3. Balance between model complexity and computational constraints

**Limitations:**
- Validation set may not fully represent wild audio diversity
- Some robustness tests (e.g., <0 dB SNR) not feasible in project scope
- Streaming deployment may introduce additional latency

**Future Directions:**
- Implement uncertainty quantification for OOD detection
- Explore confidence thresholding to flag unknown species
- Optimize for edge deployment on drones
- Expand to more geographic regions

**Personal Reflections Section:**
(Each team member can add a brief reflection paragraph)

---

### 📚 PAGE 11: REFERENCES

**Page Title:** Research & Resources

**Existing Solutions:**
1. Kahl et al. (2021) - BirdNET: CNN-based bird sound classifier
2. Lostanlen et al. (2019) - Per-channel energy normalization
3. Stowell et al. (2019) - BirdCLEF challenge overview
4. Kumagai et al. (2022) - Open-set recognition for wildlife sounds
5. Vaz et al. (2021) - EfficientNet for bioacoustic detection
6. Stowell & Plumbley (2014) - Unsupervised feature learning for birds
7. Wang et al. (2021) - Confidence-based OOD detection
8. Goëau et al. (2023) - BirdCLEF 2023 overview

**Background & Governance:**
1. CBD Post-2020 Global Biodiversity Framework (2022)
2. GBIF Data Standards and Open Access Policy (2023)
3. Cardinale et al. (2012) - Biodiversity loss and its impact
4. Willis (2022) - Environmental acoustic monitoring policy
5. Gasc et al. (2013) - Acoustic diversity index
6. O'Brien (2022) - Conservation technology economics
7. IUCN Conservation Monitoring Guidelines (2021)
8. Steinberg et al. (2023) - AI for biodiversity ethics

---

## Google Sites Design Tips

### Layout Recommendations:
1. **Use the "Simple" or "Diplomat" theme** for a clean, professional look
2. **Add a navigation menu** with all pages listed
3. **Use collapsible text sections** for detailed content
4. **Embed images and diagrams** directly
5. **Add buttons** for links to GitHub, demo, etc.

### Section Types to Use:
- **Image carousel** for visualizations
- **Table of contents** on longer pages
- **Button links** for external resources
- **Embed from Google Drive** for videos/presentations
- **Collapsible text** for detailed technical content

### Suggested Color Scheme:
- Primary: Forest green (#1B4332) — represents nature/ecology
- Accent: Warm amber (#E9C46A) — highlights key information
- Background: Soft cream (#FAF3E0) — easy on the eyes
- Text: Charcoal (#2D3436) — readable

---

## Artifacts Checklist for E-Portfolio

Based on the grading rubrics, ensure you include:

### ✅ Coursework:
- [x] Project report (PDF embed or link)
- [x] Technical documentation
- [x] Data analysis visualizations (mel spectrograms, training curves)
- [x] Design models (system architecture diagrams)
- [x] Problem-solving exercises (Pugh matrix, prototype evolution)

### ✅ Creative Work:
- [ ] Infographics explaining the classification pipeline
- [ ] Diagrams showing audio processing flow
- [ ] Demo video of the system in action

### ✅ Personal Engagement:
- [ ] Journal entries/reflections from each team member
- [ ] Photos from team meetings or work sessions
- [ ] Notes from research discussions
- [ ] Blog-style explanations of key concepts

---

## Quick Start Steps for Google Sites

1. Go to https://sites.google.com
2. Click "+ New" to create a blank site
3. Name it "Bird Sound Classification E-Portfolio"
4. Add pages using the right sidebar
5. Copy content from this guide into each page
6. Upload images from your report
7. Add navigation menu
8. Preview and publish!

