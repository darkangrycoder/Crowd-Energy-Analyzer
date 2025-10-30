
# 🎥 Crowd Energy Analyzer — Nightly AI Intern Challenge

This project analyzes **crowd energy** from short (30–60 s) sports or concert videos by combining **audio intensity** and **visual motion dynamics** to determine high-energy and low-energy moments.  
It then generates:
- **An energy timeline**
- **"Show cue" suggestions** for low-energy periods  
- **A 10-second highlight clip** representing the peak crowd energy  

Built entirely in **Google Colab**, this demo is designed to be **lightweight, interpretable, and creative** — meeting all Nightly challenge criteria.

---

## 🚀 Features
- 🔊 **Audio Energy Analysis** using amplitude envelope (RMS)  
- 🎞️ **Visual Energy Detection** via frame-to-frame pixel difference  
- ⚡ **Multimodal Fusion** of audio + video scores  
- 🧩 **Highlight Extraction** — automatic 10 s most energetic segment  
- 💡 **"Show Cue" Generation** — simple text prompts for low-energy phases  

---

## 🧰 Environment Setup (Colab)
```python
!pip install moviepy librosa opencv-python numpy matplotlib
````

Upload any 30–60 s clip (MP4/MOV).
Then run the notebook cells sequentially.

---

## 🧠 How It Works

### 1. Audio Energy Measurement

We extract the **RMS (root mean square)** amplitude across time frames:

[
E_{audio}(t) = \sqrt{\frac{1}{N}\sum_{i=1}^{N}x_i^2}
]

Higher RMS → louder sections → more energetic crowd.

### 2. Video Energy Measurement

Each video frame is converted to grayscale, and the **absolute pixel difference** from the previous frame is computed:

[
E_{video}(t) = \frac{1}{W \times H}\sum_{x,y}|I_t(x,y) - I_{t-1}(x,y)|
]

Higher difference → more motion (crowd waving, jumping, cheering).

### 3. Normalization and Fusion

Both signals are **normalized** to [0, 1] and combined:

[
E_{total}(t) = \alpha \times E_{audio}(t) + (1 - \alpha) \times E_{video}(t)
]

where **α = 0.5** (equal weight for sound + motion).

### 4. Timeline Segmentation

We compute rolling averages over fixed windows (≈ 0.5 s) to smooth noise and produce a continuous energy timeline.

### 5. Highlight & Cue Logic

* **High-energy (top 10 s):** segment with max average `E_total`
* **Low-energy:** segments where energy < mean − std
  → triggers “Show Cue” idea (lighting change, beat drop, etc.)

---

## 🔄 System Flowchart

```mermaid
flowchart TD
    A[Input Video Clip] --> B[Audio Extraction]
    A --> C[Frame Extraction]
    B --> D[Compute RMS Energy]
    C --> E[Compute Frame Differences]
    D --> F[Normalize Audio Energy]
    E --> G[Normalize Video Energy]
    F --> H[Fuse Signals (Weighted Sum)]
    G --> H
    H --> I[Generate Energy Timeline]
    I --> J[Detect High-Energy 10s Segment]
    I --> K[Detect Low-Energy Moments]
    J --> L[Highlight Clip Output]
    K --> M[Generate Show Cue Text]
```

---

## 🔬 Highlight Detection Theory

```mermaid
flowchart LR
    A[Total Energy Signal E_total(t)] --> B[Rolling Mean Filter]
    B --> C[Find Max 10s Window]
    C --> D[Highlight Segment Extraction]
    B --> E[Threshold < mean - std]
    E --> F[Low-Energy Show Cue Generation]
```

* **Rolling Mean Filter** removes local spikes from sudden camera cuts.
* **Max 10 s Window** ensures highlight length consistency.
* **Low-Energy Threshold** is adaptive (depends on signal variability).

---

## 📊 Example Output

* Energy timeline plot
* “Show cue” suggestions (for low-energy)
* Highlight clip saved as `highlight.mp4`

---

## 🧩 Possible Extensions

| Idea                   | Description                                     |
| ---------------------- | ----------------------------------------------- |
| 🎭 Emotion recognition | Integrate lightweight facial-emotion model      |
| 🎚 Dynamic weighting   | Adapt α based on crowd type (music vs sports)   |
| 🖥️ Gradio interface   | Upload → Analyze → Play highlight interactively |

---

## 🧠 Summary

This system blends **signal processing** + **computer vision** to approximate crowd excitement dynamically.
Instead of relying on heavy LLMs or deep models, it uses interpretable, efficient techniques — ideal for real-time event analytics or creative show-cue design.

---

### 🏁 Author

Developed by **Tirtha Debnath** for the *Nightly AI Intern Challenge (2025)*
💡 *“Blending live events, technology, and human emotion.”*

```

---

```
