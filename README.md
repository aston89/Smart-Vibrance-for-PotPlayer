# Smart Vibrance: Base, Plus, PRO (PotPlayer, MPC & forks, VLC, etc.)

A real-time adaptive vibrance shader for video playback, inspired by modern dynamic vibrance systems (e.g. NVIDIA RTX Dynamic Vibrance).

Not a saturation filter. Not a LUT. Not a global color boost.

This is a **perceptual color response system** designed to adapt its response to the chroma and luminance characteristics of the pixels being processed.

## Table of Contents

- [The Problem](#the-problem)
- [What These Shaders Do](#what-these-shaders-do)
- [Differences Between Base, Plus and PRO](#differences-between-base-plus-and-pro)
- [Important Note on Temporal Stabilization](#important-note-on-temporal-stabilization)
- [Not a Color Accuracy Tool](#not-a-color-accuracy-tool)
- [Installation](#installation)
- [Discussion](#videohelp-forum-link-for-discussion)
- [Preview](#preview)

---

## The Problem

There is a fundamental difference between how colors are perceived on TVs and on PC monitors.

TV manufacturers have spent years competing on one thing: making colors more vivid, brighter, and more eye-catching without looking obviously artificial.

This "vividness race" has shaped the display ecosystem. Many TVs and smartphones apply their own color-enhancement algorithms, and some content may look comparatively restrained when viewed on a PC monitor without similar processing.

PC monitors play a different game. Many prioritize color accuracy, response time, and professional consistency. Depending on the model and picture settings, they may apply little or no aggressive color enhancement, exposing the characteristics of the original signal more directly. Results also vary significantly between panel types, display settings, and viewing conditions.

The result? **What looks vivid and balanced on one display can look flat or washed out on another.**

This is not just a range issue. Color perception and reproduction are affected by video levels, scaling, compression, SDR processing, display characteristics, and the way different enhancement algorithms interact with the signal.

To compensate, you may have tried:

- **GPU "digital vibrance" sliders** — these can boost colors indiscriminately, over-enhance already saturated regions, and produce unnatural results.
- **Contrast and brightness adjustments** — these affect the overall signal and can compromise shadow detail or highlights.
- **Renderer and scaler tweaks (madVR, etc.)** — these can improve scaling and image reconstruction, but do not necessarily address perceptual color imbalance.

### The real issue

Video content can exhibit one or more of the following:

- Colors that appear washed out on a particular display.
- Compressed SDR material with reduced color separation.
- Inconsistent perceived saturation across different types of content.
- Anime and stylized material losing color separation in dark or low-chroma regions.

Traditional global controls do not account for the characteristics of individual pixels. They do not inherently distinguish weak chroma from already strong chroma, nor do they adapt their response to chromatic direction.

This leaves you between two extremes: flat and lifeless, or overprocessed and artificial.

### What is actually needed?

Not simply "more saturation", but a system that adjusts the amount of enhancement according to the existing color signal.

### Core idea

Instead of applying a fixed, linear gain, Smart Vibrance makes its chroma enhancement depend on the signal being processed:

- Weak colors can receive more enhancement.
- Medium-strength colors receive a moderated response.
- Strong colors progressively roll off.
- Near-neutral colors receive dedicated protection.
- PRO additionally balances the response according to chromatic direction.

The objective is **controlled chroma expansion**, not indiscriminate saturation.

---

## What These Shaders Do

### 1. Non-linear chroma response

The enhancement is reduced as chroma strength increases. This avoids applying the same additional gain to weak and already strongly saturated colors.

### 2. Near-neutral protection

Smooth response curves help control enhancement near the neutral axis, where small chromatic deviations can be especially noticeable.

This can help reduce excessive enhancement of weak chroma and compression artifacts, particularly in dark or nearly neutral regions.

### 3. Stateless low-light / low-chroma guard (Plus v2.0 and PRO v2.0)

Plus v2.0 and PRO v2.0 introduce a low-light / low-chroma guard that reduces additional enhancement in regions where dark luminance and weak chroma coincide.

This is a **spatial, stateless compensation** based on the current pixel. It does not compare consecutive frames, retain previous-frame measurements, or implement temporal EMA.

Its purpose is to moderate potentially objectionable chroma enhancement, not to guarantee the elimination of flicker or compression instability.

### 4. Adaptive hue balancing (PRO)

PRO extends the response model with a continuous opponent-style representation of chromatic direction. Different hue regions receive different enhancement budgets, with smooth interpolation between them.

This provides more control over the distribution of enhancement than a model that treats all hues identically.

---

## Differences Between Base, Plus and PRO

### Base version (`Smart_Vibrance.txt`)

- Uses hard gating thresholds for gray and low-chroma regions.
- Applies a gradual roll-off to already saturated colors.
- Does not implement temporal smoothing.
- Provides a straightforward saturation boost with minimal adaptive balancing.
- Very lightweight computationally.
- Can work well with anime and highly stylized content, but may look aggressive on some real-world footage.

### PLUS version (`Smart_Vibrance_Plus.txt`)

- Uses a continuous adaptive response inspired by dynamic vibrance systems.
- Adjusts its response using the chroma characteristics of the current pixel.
- Uses smooth transitions instead of hard Boolean separation between gray and chromatic regions.
- Introduces the low-light / low-chroma guard in v2.0 to moderate enhancement in potentially sensitive regions.
- Does not implement temporal EMA or previous-frame history.
- Suited to anime, SDR streaming, compressed video, and mixed-quality libraries.
- More computationally involved than Base while remaining relatively lightweight.

### PRO version (`Smart_Vibrance_PRO.txt`)

- Refines the adaptive boost model using a continuous opponent-style color representation.
- Separates luminance from chroma and estimates chromatic direction using two opponent-style axes: Red–Green and Yellow–Blue.
- Uses a continuous hue response across the chromatic plane rather than treating all hues equally.
- Assigns different chroma-response budgets to different chromatic directions, smoothly interpolating between them.
- Adds chroma-dependent balancing so low- and medium-chroma colors are not automatically treated as candidates for maximum enhancement.
- Uses a neutral fallback when chroma is too weak for reliable hue-direction estimation.
- Includes the low-light / low-chroma guard introduced in v2.0.
- Does not implement temporal EMA or previous-frame history.
- Designed as a general-purpose adaptive vibrance model for film, animation, streaming video, and mixed-quality sources.
- Aims to improve the distribution of chroma enhancement across hue and chroma strength, rather than maximizing saturation.
- More computationally involved than Base and Plus, while remaining designed for a lightweight DirectX 9 / Pixel Shader 3.0 implementation.
- Originally developed in part to address the orange-skin appearance observed with earlier shader generations.

---

## Important Note on Temporal Stabilization

Earlier Plus and PRO shader implementations included EMA calculations that referenced previous-state parameters supplied through constant registers.

However, a pixel shader cannot write updated values back into those constant registers. Without an external mechanism that supplies updated state between frames, the presence of an EMA formula alone does not provide reliable temporal stabilization.

**Plus v2.0 and PRO v2.0 remove this unsupported temporal-state assumption.**

Their low-light / low-chroma guard is a stateless approximation intended to moderate enhancement in potentially sensitive regions. It is not a substitute for true temporal filtering.

Genuine temporal stabilization would require accessible previous-frame data or persistent history managed by the host renderer.

---

## Not a Color Accuracy Tool

Smart Vibrance is intentionally **not** designed for color grading, calibrated reference viewing, or broadcast-compliance workflows.

It is a perceptual enhancement layer, not a color-correction system.

The result depends on the source material, renderer, video levels, display, and individual viewing preferences.

---

## Installation

Place the desired shader file in:

`PotPlayer/pxshader/`

Select and enable it through the player's pixel-shader controls. Exact menu locations may vary between PotPlayer versions and compatible forks.

---

## VideoHelp Forum Link for Discussion

https://forum.videohelp.com/threads/414563-PotPlayer-shader-Smart-Vibrance

---

## Preview:
![alt text](https://github.com/aston89/Smart-Vibrance-for-PotPlayer/blob/main/preview.jpg)



