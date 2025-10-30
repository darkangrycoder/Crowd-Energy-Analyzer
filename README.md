# 🎥 Crowd Energy Analyzer

> **Objective:**  
> Analyze crowd **energy dynamics** from short (30–60 s) sports or concert videos by combining **audio intensity** and **video motion**.  
> The model identifies low- and high-energy moments, generates *"show cue"* ideas for dull sections, and extracts the *10 seconds of peak energy* as a highlight clip.

---

## 🧩 Overview

This project blends **computer vision** and **audio signal processing** to measure the crowd's excitement in real time.  
It works directly on uploaded video clips and is designed to be **lightweight, interpretable, and creative** — perfectly aligned with the Nightly challenge theme:  
> *"Blending live events, technology, and human emotion."*

---

## ⚙️ Setup (Google Colab)

```python
!pip install moviepy librosa opencv-python numpy matplotlib
```

1. Open the notebook in Google Colab.
2. Upload a 30–60 second video (`.mp4`, `.mov`, etc.).
3. Run all cells sequentially.
4. The script will:
   - Plot the **energy timeline**
   - Print **show cue** suggestions
   - Export a **10-second highlight clip**

---

## 🧠 How It Works — Detailed Explanation

This system measures *energy* through two complementary channels:

### 🎵 1. Audio Energy Calculation

We compute the **Root Mean Square (RMS)** energy over time.
It represents the *perceived loudness* of the crowd:

$$E_{audio}(t) = \sqrt{\frac{1}{N}\sum_{i=1}^{N}x_i^2}$$

- $x_i$: amplitude of each audio sample in a small window
- $N$: total samples in that window

A louder crowd (cheering, clapping) means higher RMS energy.
The audio track is divided into short overlapping frames (≈ 0.1 s), and RMS is computed for each.

> **Intuition:** RMS is like measuring how much "vibration energy" the air carries.
> Calm crowd = flat signal → low RMS.
> Cheering crowd = jagged waveform → high RMS.

---

### 🎞️ 2. Visual Energy Calculation

Every frame is converted to grayscale.
We compute how much each frame **changes compared to the previous one** — i.e., pixel motion.

$$E_{video}(t) = \frac{1}{W \times H}\sum_{x,y}|I_t(x,y) - I_{t-1}(x,y)|$$

- $I_t(x,y)$: pixel intensity at position $(x,y)$ in frame *t*
- $W, H$: frame width and height

The average absolute difference measures **motion** — when fans wave, jump, or raise flags, the pixel changes skyrocket.

> **Intuition:** Video energy = *how much the scene shakes*.
> Still crowd → low difference.
> Roaring crowd → high difference.

---

### ⚖️ 3. Normalization & Fusion

Audio and video signals have different ranges and units.
To combine them fairly, both are **normalized** to [0, 1].

Then we compute a **weighted fusion**:

$$E_{total}(t) = \alpha \cdot E_{audio}(t) + (1 - \alpha) \cdot E_{video}(t)$$

- $E_{total}$: overall energy timeline
- $\alpha$: audio–video weighting factor (default = 0.5)

> This means sound and motion contribute equally, but you can tune α to favor one.
> For concerts (louder environments), α ≈ 0.7;
> for sports (more visible motion), α ≈ 0.4.

---

### 📈 4. Temporal Smoothing

To avoid noise from quick spikes (e.g., camera cuts, short screams),
we apply a **rolling mean filter** with a 0.5 s window.

$$\tilde{E}(t) = \frac{1}{k}\sum_{i=0}^{k-1}E_{total}(t - i)$$

This creates a smoother, more interpretable curve of energy over time.

---

### ⚡ 5. Highlight Detection

We slide a 10 s window along the energy timeline and compute the average energy in each window:

$$\bar{E}_{window}(t) = \frac{1}{10s}\sum_{i=t}^{t+10s}\tilde{E}(i)$$

The window with the **highest mean energy** is marked as the **highlight segment**.
That portion (10 seconds long) is then trimmed and exported as `highlight.mp4`.

---

### 🌙 6. Low-Energy Detection & Show Cues

Low-energy moments are defined as:

$$E_{low}(t) < \mu - \sigma$$

where:

- $\mu$: mean of $\tilde{E}$
- $\sigma$: standard deviation

For each such segment, the system generates a creative **"show cue"** idea — for example:

- `"Cue lighting fade-in"`
- `"Trigger visual fireworks"`
- `"Add beat drop transition"`

This turns the data into real-world event production insight.

---

## 🔄 System Flowchart

```mermaid
flowchart TD
    A[🎬 Input Video Clip] --> B[🔊 Extract Audio Track]
    A --> C[🎞 Extract Frames]
    B --> D[📊 Compute RMS Audio Energy]
    C --> E[🧮 Compute Frame Differences]
    D --> F[Normalize Audio Energy]
    E --> G[Normalize Video Energy]
    F --> H[🔗 Combine via Weighted Sum (E_total)]
    G --> H
    H --> I[📈 Smooth Energy Signal]
    I --> J[⚡ Detect 10s High-Energy Segment]
    I --> K[🌙 Detect Low-Energy Moments]
    J --> L[🎥 Export Highlight Clip]
    K --> M[💡 Generate Show Cue Ideas]
```

---

## 🔬 Highlight Detection Theory

```mermaid
flowchart LR
    A[E_total(t)] --> B[Rolling Mean Filter]
    B --> C[Find Max 10s Window]
    C --> D[Highlight Segment Extraction]
    B --> E[Find Energy < mean - std]
    E --> F[Low-Energy Cue Generation]
```

---

## 📊 Example Outputs

| Output Type                 | Description                                         |
| --------------------------- | --------------------------------------------------- |
| 🕒 **Energy Timeline Plot** | A smooth curve of fused energy levels               |
| ⚡ **Highlight Clip**        | 10 s MP4 showing the peak crowd moment              |
| 💡 **Show Cues**            | Text recommendations for lighting or visual effects |

---

## 🧮 Why This Works

| Channel    | What It Captures           | Why It Matters                       |
| ---------- | -------------------------- | ------------------------------------ |
| **Audio**  | Volume + cheer loudness    | Reflects collective vocal intensity  |
| **Video**  | Motion + movement energy   | Captures non-verbal excitement       |
| **Fusion** | Balanced emotion detection | Robust against noise or camera focus |

> By combining both, we get a *multimodal understanding* of excitement — not just "loud" or "moving," but truly *energetic*.

---

## 🧠 Example Formula Recap

| Concept         | Formula                                                 | Meaning               |
| --------------- | ------------------------------------------------------- | --------------------- |
| Audio energy    | $E_{audio}(t) = \sqrt{\frac{1}{N}\sum x_i^2}$           | Loudness              |
| Visual energy   | $E_{video}(t) = \frac{1}{W \times H}\sum\|I_t - I_{t-1}\|$ | Motion                |
| Combined energy | $E_{total}(t) = \alpha E_{audio} + (1-\alpha)E_{video}$ | Fused timeline        |
| Highlight       | max mean of 10 s window                                 | Most energetic moment |
| Low-energy cue  | $E < \mu - \sigma$                                      | Suggest show cue      |

---

## 💻 Project Structure

```
📂 CrowdEnergyAnalyzer/
 ├── crowd_energy_colab.ipynb      # Main notebook (run in Colab)
 ├── sample_videos/                # Example test clips
 ├── outputs/
 │   ├── energy_plot.png
 │   ├── highlight.mp4
 │   └── cues.txt
 ├── README.md                     # (this file)
```

---

## 🧩 Extensions (Optional)

| Feature                | Description                                              |
| ---------------------- | -------------------------------------------------------- |
| 🎭 Emotion recognition | Add lightweight facial expression classifier             |
| 🎚 Adaptive α          | Auto-tune audio/video weighting based on signal variance |
| 🎛 Real-time dashboard | Stream energy values live via Gradio                     |

---

## 🏁 Summary

This tool demonstrates how **simple signal analysis** can yield powerful emotional insights from live events — without heavy LLMs or deep networks.
It fulfills Nightly's challenge goal: **creativity, clarity, and problem-solving**.

> *Built by Tirtha Debnath — Nightly AI Intern Challenge (2025)*  
> "Turning sound and motion into emotion."

---

**Note:** This README.md is ready for direct GitHub use with native Mermaid diagram support and LaTeX-style math rendering (if your repository has math support enabled).
