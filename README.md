# Smart Vibrance : Base, Plus, Pro (PotPlayer, MPC & forks, etc etc)
A real-time adaptive vibrance shader for video playback, inspired by modern dynamic vibrance systems (e.g. NVIDIA RTX Dynamic Vibrance).
Not a saturation filter. Not a LUT. Not a global color boost.
This is a **perceptual color response system** designed to behave differently depending on what is actually in the frame.

---

## Problem
There is a fundamental difference between how colors are perceived on TVs and on PC monitors.
TV manufacturers have spent years competing on one thing:
making colors more vivid, brighter, and more eye-catching — without feeling obviously artificial.
This “vividness race” has shaped the entire ecosystem.
Over time, content distributors started **reducing native saturation**, knowing that TVs will automatically compensate with built-in enhancement algorithms and smartphones do the same (this is not just about OLED panels).
So what you actually watch is often, **intentionally toned-down content designed to be “re-inflated” by the display**

And then comes the PC.
PC monitors play a completely different game, they prioritize color accuracy standards, response time and professional consistency.
Most of them do NOT include aggressive color enhancement, expose raw or near-raw signal and **vary heavily depending on panel type (IPS vs VA, etc.)**.
Result ? **what looks “balanced” on a TV often looks flat or washed-out on a pc monitor**

It's not just a range issue (16–235 vs 0–255), resizing, compression, SDR pipelines and panel characteristics all contribute to this effect.
To compensate, you probably tried:
- **GPU “digital vibrance” sliders** (boosts everything indiscriminately, burns highlights, destroys already saturated colors).
- **contrast / brightness tweaking** (shifts the entire signal, kills shadow detail or highlights).
- **renderer/scaler tweaks (madVR, etc.)** (improves geometry and scaling but does not solve perceptual color imbalance. 

**The real issue:**
Most video content today suffers from one or more of the following:
- washed-out colors on LCD / LED monitors  
- overly compressed SDR content (especially streaming platforms)  
- inconsistent saturation across scenes  
- anime and stylized content losing color separation in dark areas  

**And most importantly:**
Traditional controls operate globally and linearly which means:
- they don’t care about *context*
- they don’t care about *existing saturation*
- they don’t care about *scene composition*

**You are stuck between two extremes:**
- flat and lifeless or overcooked and artificial.
There is no middle ground using standard tools.

**What’s actually needed:**
- Not “more saturation” but a system that "understands" when to push color and when to back off.

**Core idea:**
- Instead of applying a linear fixed gain, saturation boost become a function of existing chroma "energy", weak colors are boosted, medium colors are gently enhanced, strong colors are progressively ignored, grey scale is differently preserved and threated.
>This avoids clipping by design, not by correction.

---

## What this shader do 

**1. non-linear saturation response**
Boost is dynamically reduced as saturation increases.
No hard thresholds. No binary logic.

**2. grayscale-aware protection (continuous)**
Near-neutral pixels are preserved using smooth perceptual gating.
This prevents:
- shadow noise amplification
- loss of detail in low-light scenes
- flat anime frames breaking apart

**3. temporal stability (Plus/PRO version)**
Scene analysis is smoothed over time using EMA.
This eliminates:
- frame-to-frame flicker
- compression instability
- “breathing” saturation effects

**4. dual-layer scene model**
- slow layer: global scene adaptation
- fast layer: micro instability / compression response
Result: stable even under aggressive frame boosting pipelines (svp, dmitri-render, etc etc).

---

## Difference between Base vs Plus vs PRO logic

**Base version (`Smart_Vibrance.txt`):**
* Hard gating thresholds used on gray / low-chroma colors.
* Simple gradual rolloff curve for already saturated colors.
* No temporal smoothing.
* Strong and straightforward saturation boost with minimal adaptive balancing.
* Very lightweight computationally.
* Good for anime and highly stylized content, but can become aggressive on some real-world footage.

**PLUS version (`Smart_Vibrance_Plus.txt`):**
* Scene-adaptive parameters, with a response model conceptually similar to dynamic vibrance systems.
* Temporal EMA stabilization to reduce frame-to-frame instability.
* Dual-scale signal model for additional response control.
* Fully continuous response, with no hard boolean separation between different gray/chroma regions.
* Better suited anime content encodes, SDR streaming, compressed video, and mixed-quality libraries.
* More computationally expensive than Base, while remaining relatively lightweight.

**PRO version (`Smart_Vibrance_PRO.txt`):**
* Refactors the boost-balance model around a continuous opponent-color representation.
* Separates luminance from chroma and evaluates chromatic direction using two opponent-style axes: Red to Green and Yellow to Blue.
* Uses a continuous hue response across the entire chromatic plane instead of treating all hues equally.
* Assigns different chroma-response budgets to different chromatic directions, while smoothly interpolating between them.
* Adds chroma-dependent balancing so low / mid-chroma colors are not automatically treated as candidates for maximum boost.
* Uses a neutral fallback when chroma is too weak for reliable hue-direction estimation.
* Preserves the scene-adaptive and temporally stabilized behaviour introduced in PLUS.
* Designed as a general-purpose adaptive vibrance model for a broad range of content, including film, animation, streaming video, and mixed-quality sources.
* The goal is not maximum saturation, but **controlled chroma expansion with better distribution of the available boost across hue, chroma strength, scene activity, and temporal stability**.
* Computationally more demanding than Base and Plus, but still designed specifically around the constraints of a lightweight DirectX 9 / Pixel Shader 3.0 implementation.
* This version originally aimed to solve the "orange skin" problem wich was evident on the other two generations of shader.

---

## Not a color accuracy tool
This is intentionally NOT designed for grading pipelines, calibrated reference viewing, broadcast compliance workflows.
It is a **perceptual enhancement layer**, not a correction system.

---

## Installation
Drop shader into: "PotPlayer/pxshader/" 

---

## VideoHelp forum link for discussion :
> https://forum.videohelp.com/threads/414563-PotPlayer-shader-Smart-Vibrance


## Preview:
![alt text](https://github.com/aston89/Smart-Vibrance-for-PotPlayer/blob/main/preview.jpg)



