# Smart Vibrance Shader (PotPlayer, MPC & forks, etc etc)
A real-time adaptive vibrance system for video playback, inspired by modern dynamic vibrance systems (e.g. NVIDIA RTX Dynamic Vibrance).
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

**3. temporal stability (PLUS version)**
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

## Difference between Base vs PLUS

**Base version (Smart_vibrance.txt)**:
- hard gating thresholds used on gray colors.
- simple gradual rolloff curve for already saturated colors.
- no temporal smoothing.
- Good for anime but not so much for movies (too aggressive in some circumstances).
- Computationally lightweight .

**PLUS version (Smart_Vibrance_Plus.txt):**
- Scene-adaptive parameters (very similar to rtx dynamic vibrance).
- Temporal EMA stabilization (avoiding potential artifacts).
- Dual-scale signal model (safety estimation).
- Fully continuous response (no switching logic, no more hard boolean separation between gray scales)
- Designed for real-world messy content like anime encodes, SDR streaming, mixed-quality libraries.
- Computationally more heavy (but still reasonable).
 
---

##  Best use cases
- anime (especially compressed / SD upscale)
- streaming platforms (YouTube, Netflix SDR)
- washed-out LCD/LED monitors
- old movies with weak color grading
- general desktop video playback

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



