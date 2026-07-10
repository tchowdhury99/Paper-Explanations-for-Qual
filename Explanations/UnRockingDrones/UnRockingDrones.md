# Un-Rocking Drones: Foundations of Acoustic Injection Attacks and Recovery Thereof — Full Detailed Explanation

**Authors:** Jinseob Jeong, Dongkwan Kim, Joonha Jang, Juhwan Noh, Changhun Song, Yongdae Kim (KAIST, Agency for Defense Development, Samsung SDS)
**Venue:** NDSS Symposium 2023

This is an exhaustive, section-by-section explanation of the paper, including the threat model, the exact physics/math behind the attack (every equation explained), how "security" is defined and enforced in this paper (which is *not* classic attack detection — this is explained explicitly below), the full design of their defense system **UnRocker**, and all experimental results including the appendix.

---

## Table of Contents
1. [The One-Paragraph Version](#1-the-one-paragraph-version)
2. [Threat Model — In Full Detail](#2-threat-model--in-full-detail)
3. [Background Physics of the Sensors](#3-background-physics-of-the-sensors)
4. [How the Attack Works, Mathematically — Every Equation Explained](#4-how-the-attack-works-mathematically--every-equation-explained)
5. [Do They "Detect" Attacks? (Important Clarification)](#5-do-they-detect-attacks-important-clarification)
6. [The Acoustic Injection Testbed](#6-the-acoustic-injection-testbed)
7. [The Central Discovery: Sampling Jitter](#7-the-central-discovery-sampling-jitter)
8. [What "Security" Means in This Paper](#8-what-security-means-in-this-paper)
9. [How They Impose Security: The UnRocker System](#9-how-they-impose-security-the-unrocker-system)
10. [Training the Recovery Model — All Details](#10-training-the-recovery-model--all-details)
11. [Full Evaluation Results](#11-full-evaluation-results)
12. [Security Against an Adaptive/Evasive Attacker](#12-security-against-an-adaptiveevasive-attacker)
13. [Real-Time Engineering & Practical Limits](#13-real-time-engineering--practical-limits)
14. [Appendix Findings (Jitter Root-Cause, Deep Dive)](#14-appendix-findings-jitter-root-cause-deep-dive)
15. [Comparisons to Other Methods](#15-comparisons-to-other-methods)
16. [Answers to the Key Questions, Concisely](#16-answers-to-the-key-questions-concisely)

---

## 1. The One-Paragraph Version

Drones use MEMS gyroscopes/accelerometers that can be tricked into vibrating by external sound at their resonance frequency — an "acoustic injection attack." Everyone assumed the resonance itself is what crashes the drone. This paper shows that's *wrong*: in idealized simulation, resonance signals get filtered out or ignored, and drones don't crash. What actually causes real crashes is **sampling jitter** — tiny, unavoidable timing irregularities in real hardware that smear the resonance signal across many frequencies, letting some of it leak into the drone's control band. Because this jitter-smeared corruption behaves like classic "additive noise," the authors don't try to *detect* the attack at all — instead they continuously run every incoming sensor sample through a **denoising autoencoder (DAE)** (their system, **UnRocker**) that reconstructs the clean signal in real time, so the drone can keep flying its mission even while under sustained attack.

---

## 2. Threat Model — In Full Detail

The threat model (§III-A in the paper) defines exactly what the attacker can and cannot do. This matters because it determines what a valid defense must handle.

### 2.1 Attacker Capabilities (assumed)
- **Remote, wireless attack vector:** The attacker transmits a strong acoustic/ultrasonic signal toward the drone — no physical access or network intrusion needed. Real-world tools like a **Long-Range Acoustic Device (LRAD)**, a class of directed-energy weapon, make this practical at range; military standards (MIL-STD-810H) even require systems to be tested against acoustic noise, confirming this is a recognized real threat class.
- **Full knowledge of the target:** The attacker knows the exact sensor models on board (e.g., ICM-20689) and can look up their **resonance frequency** and **sampling frequency** from public datasheets, or discover the resonance frequency empirically by sweeping frequencies and observing the sensor's output (since resonance produces a peak response).
- **Simultaneous multi-sensor attack:** The attacker can resonate *all* onboard IMUs at once (both gyroscope and accelerometer, and even redundant/backup IMUs if they share similar resonance frequencies). This is a deliberate, important assumption — it rules out any defense that relies on "compare against another sensor that isn't compromised," because the attacker can knock out every sensor simultaneously.
- **Sustained, persistent attack:** The attacker can keep transmitting for as long as they want — this isn't a brief pulse. This rules out defenses that only work for a few seconds (like some sensor-recovery approaches in prior work).

### 2.2 What the Attacker Wants
Two classic outcomes are considered:
- **Denial of Service (DoS):** corrupt the sensor enough that the flight controller can't maintain stable attitude/position — the drone crashes or falls out of the sky.
- **Spoofing:** corrupt the sensor in a *controlled* way so the flight controller thinks the drone is doing something it isn't (e.g., tricking it into an unintended maneuver), effectively hijacking control without touching the radio link.

### 2.3 Why This Threat Model Rules Out Prior Defenses
Given these assumptions, the paper argues:
- Mechanical shielding / circuit changes require modifying hardware per-drone and have heating side effects — not fundamental, and don't address the persistent, all-sensors-at-once nature of the attack.
- Detection-only approaches tell you *that* you're under attack but not *what to do about it* — they have "no contingency plan," so the drone still can't finish its mission.
- Prior recovery approaches that rely on a second, unaffected sensor fail immediately in this threat model, because the attacker is assumed capable of hitting every sensor at once.
- Prior recovery approaches that only last a few seconds fail against the "persistent attack" assumption.

This is precisely the gap the paper's system (UnRocker) is built to fill: a defense that (a) needs no extra hardware/sensors, (b) works indefinitely as long as the attack continues, and (c) lets the drone actually complete its assigned mission, not just avoid an immediate crash.

---

## 3. Background Physics of the Sensors

### 3.1 Accelerometer
A MEMS accelerometer has a small proof mass suspended on springs between fixed capacitor plates. When the drone accelerates, inertia keeps the mass "lagging behind" relative to the moving frame, so the spring stretches or compresses. This changes the capacitance between the mass and the plates (because capacitance depends on the gap distance), which shows up as a voltage change. That voltage is converted into an acceleration value using:

`F_a = m · a = -k · x`

- `F_a`: the inertial force felt by the mass.
- `m`: mass of the proof mass.
- `a`: acceleration being measured (what we want).
- `k`: spring stiffness coefficient.
- `x`: how far the spring/mass has been displaced.

This is literally Newton's second law (`F = ma`) combined with Hooke's law (`F = -kx`) for a spring. Since `x` is what the sensor actually measures (via capacitance), and `m` and `k` are known constants of the sensor's design, the sensor computes `a = -k·x/m`.

### 3.2 Gyroscope
A MEMS gyroscope is more complex because it exploits the **Coriolis effect**. The proof mass is kept continuously oscillating back and forth along a "drive" axis (this ongoing vibration is called the **drive mode**). If the whole sensor rotates (which is what we want to measure), the Coriolis effect pushes the vibrating mass sideways, into a second, perpendicular "sense" axis (the **sense mode**). This sideways deflection is measured the same way as the accelerometer (capacitance → voltage), and from it the angular rotation rate is derived:

**Equation (1):**
`F_coriolis = -2m(ω × v) = -kx`

- `F_coriolis`: the sideways (Coriolis) force pushing the mass into the sense direction.
- `m`: proof mass.
- `ω`: the angular rate we're trying to measure (how fast the sensor is rotating).
- `v`: the velocity of the proof mass along the drive direction (since it's constantly oscillating, this is a known, controlled quantity).
- `ω × v`: the cross product — this is the mathematical signature of the Coriolis effect; it means the force only appears when there is *both* rotation *and* drive-mode motion, and its direction is perpendicular to both.
- `k`, `x`: as before, the sense-mode spring constant and the resulting sense-direction displacement, which is what actually gets measured.

In short: no rotation → no Coriolis force → no sense-mode deflection → gyroscope reads zero. Rotate it, and the *always-vibrating* mass gets kicked sideways proportional to how fast it's rotating, and that sideways kick is what the electronics pick up.

### 3.3 Why These Specific Sensors Are Attackable
Both designs fundamentally depend on a **physically moving mass**. The frequency at which this mass moves most easily (with the biggest response for the least energy) is its **resonance frequency**, determined by the mass and spring/damping coefficients chosen at design time. If you drive the mass at its resonance frequency with sound instead of real motion or rotation, you get a large, fake capacitance signal that looks just like real sensor data — this is the physical root of the entire attack.

Sensor designers set the normal internal "drive frequency" (`ω_d`) of a gyroscope in the middle of a broad, flat part of the sense-mode response curve on purpose, precisely so that small manufacturing variances or temperature drift don't destabilize normal readings. But this same flat, wide resonance region is also what an attacker can excite from outside with sound.

---

## 4. How the Attack Works, Mathematically — Every Equation Explained

### 4.1 The Basic Attack Workflow
1. Attacker determines/estimates a target sensor's resonance frequency (from datasheet or by sweeping frequencies and watching for peak sensor output).
2. Attacker emits a strong tone at that frequency toward the drone.
3. The sensor's internal proof mass physically resonates, producing a large parasitic signal riding on top of (or instead of) the true motion signal.
4. This analog signal passes through an analog-to-digital converter (ADC) and is handed to the flight controller (FC).
5. The FC's attitude/position estimator (which fuses sensor data, typically via an Extended Kalman Filter) is fed corrupted numbers, and control decisions sent to the rotors go wrong.

### 4.2 Why Sampling Turns a High-Frequency Attack Into a Low-Frequency Problem (Aliasing)

Drones sample sensors at a comparatively low, fixed rate — 250 Hz for PX4, 400 Hz for ArduCopter. But resonance frequencies of these MEMS sensors are in the kHz range (thousands of times per second) — far above what the Nyquist–Shannon sampling theorem says you need to faithfully capture such a fast signal (you need at least 2× the signal's frequency in samples per second). Since the sampling rate is nowhere near twice the attack frequency, the drone doesn't just fail to see the attack signal properly — it sees a *distorted alias* of it, folded down into a much lower apparent frequency. This is called **aliasing**, and it's actually critical to the whole attack: it's the reason a multi-kHz acoustic tone can end up affecting a controller that only reacts to signals below ~5 Hz.

**Equation (2):**
`ŝ'(t) = A_i · cos(2π(F_i − N·F_s)t + φ)`

- `ŝ'(t)`: the observed (aliased/under-sampled) signal after sampling.
- `A_i`: the amplitude (strength) of the injected acoustic attack signal.
- `F_i`: the true injected frequency of the attack.
- `F_s`: the drone's sampling frequency.
- `N`: an integer — essentially "how many multiples of the sampling frequency do we need to subtract to fold the high frequency down into the range the sampler can actually represent" (this is the modular-arithmetic part of aliasing).
- `t`: time; `φ`: phase offset.

**Worked example given in the paper:** if `F_s = 250 Hz` and the true attack frequency is `F_i = 5,040 Hz`, then `5040 mod 250 = 40`, so the drone "sees" a clean-looking 40 Hz signal, even though the real attack tone is 5,040 Hz. The sampling process itself manufactures a plausible, in-range-looking signal out of a signal that should be far outside the sensor's normal sensing range.

### 4.3 Modeling the Resonated Accelerometer

**Equation (3):**
`ŝ_acc(t) = s_acc(t) + A_i · cos(2πF_a t + φ)`

- `ŝ_acc(t)`: the corrupted accelerometer output.
- `s_acc(t)`: the true, benign acceleration signal (what we actually want).
- The added cosine term: the resonance-induced disturbance, at the sensor's resonance frequency `F_a`, with induced amplitude `A_i` and phase `φ`.

This is the simplest possible model: **the compromised signal is just the true signal plus an additive sinusoidal noise term.** This simplicity is exactly what later makes the problem solvable with denoising techniques — "true signal + noise" is the canonical setup for a denoising algorithm.

### 4.4 Modeling the Resonated Gyroscope (More Complex — New Contribution)

Because gyroscopes have two axes of motion (drive and sense), resonance can enter the signal in more than one way. The authors first wrote the *displacement* of the proof mass (not yet the angular rate) as:

**Equation (4):**
`x̂(t) = S·Ω(t)·cos(ω_d t) + A_d·Ω(t)·cos(ω_ac t + φ) + A_s·cos(ω_ac t + φ)`

- `x̂(t)`: total sense-direction displacement of the proof mass (this is what the electronics physically measure).
- `Ω(t)`: the true angular rate we want to recover (this is the *real* signal of interest).
- `ω_d`: the gyroscope's normal internal drive-mode frequency.
- `S`: a scaling gain relating the true angular rate, via the normal drive-mode Coriolis mechanism, to displacement (this is the "correct" pathway — no attack involved).
- `ω_ac`: the frequency of the attacker's acoustic signal.
- `A_d`: scaling gain for a "cross-term" — how much the acoustic noise's effect on the *driving* direction gets coupled, via Coriolis force, into a rotation-dependent disturbance in the sensing direction.
- `A_s`: scaling gain for the *direct* acoustic impact on the sensing direction (acoustic energy shaking the sense-axis directly, with no dependence on real rotation at all).

So there are **three physically distinct contributions** mixed together in the raw signal:
1. `S·Ω(t)·cos(ω_d t)` — the real signal we want (modulated onto the drive-mode carrier, as is normal for how these sensors work).
2. `A_d·Ω(t)·cos(ω_ac t + φ)` — a *cross term*: acoustic energy in the driving direction, but its effect scales with the true rotation rate `Ω(t)` — i.e., this term only matters if the drone is actually rotating at the same time as being attacked (a "dynamic case" effect).
3. `A_s·cos(ω_ac t + φ)` — pure acoustic noise directly injected into the sense direction, independent of any real motion — this is the dominant "spoofed" signal.

To turn this displacement into the actual *angular rate output* the sensor reports, the electronics divide by the gain `S`, multiply by `cos(ω_d t)` to strip out the drive-frequency carrier (this is standard demodulation), and apply a low-pass filter to remove leftover harmonics from that multiplication. Doing this algebra yields:

**Equation (5):**
`Ω̂(t) = Ω(t) + Ω(t)·(A_d/S)·cos((ω_ac − ω_d)t + φ) + (A_s/S)·cos((ω_ac − ω_d)t + φ)`

- `Ω̂(t)`: the final reported (corrupted) angular rate.
- First term `Ω(t)`: the true angular rate — ideally this is all we'd get.
- Second term: the cross-term's contribution, now shifted down to the *difference* frequency `ω_ac − ω_d` (a classic result of multiplying two oscillating signals together — you get sum and difference frequencies; the sum term gets filtered out by the LPF, leaving just the difference).
- Third term: the direct acoustic term, also shifted to the difference frequency `ω_ac − ω_d`.

### 4.5 Showing the Cross-Term Is Negligible (Equations 6–9)

Prior work only modeled the *stationary* case (drone standing still, `Ω(t) = 0`), so they only ever had to deal with the third term above. This paper needed to handle *flying* drones, so they had to check whether that second, rotation-dependent cross-term (`A_d` term) actually matters. They derive this via a chain of equations:

**Equation (6):** `x_acS = x_s · cos(2πf_ac·t + φ)` — the acoustic-driven deviation directly in the **sensing** direction; `x_s` is how far the mass gets displaced sensing-wise purely from the sound.

**Equation (7):** `x_acD = x_d · cos(2πf_ac·t + φ)` — the acoustic-driven deviation in the **driving** direction; `x_d` is the corresponding displacement there.

They assume `x_d ≈ x_s = x` (both directions get shaken by a similar amount by the sound) — the paper notes this is a conservative ("severe") assumption that, if anything, *overestimates* the driving-direction effect, making their later "it's negligible" conclusion even more solid.

**Equation (8):**
`x^S_acD = (2mΩ/k_s) · ∂x_acD/∂t = (4π·m·f_ac·x_d·Ω·sin(2πf_ac t + φ)) / k_s`

This is: take the driving-direction acoustic motion (Eq. 7), apply the Coriolis-force relationship (Eq. 1) to see how much of that motion "leaks" into the sensing direction *because the drone is rotating* (`Ω` appears here — if the drone weren't rotating at all, this term would be zero). `k_s` is the sense-mode spring constant.

**Equation (9) — the Relative Gain:**
`G_rel = |x^S_acD| / |x_acS| = (A_d·Ω)/A_s = (4π·m·f_ac·Ω)/k_s`

This directly compares the *size* of the "leaked-through-rotation" cross-term against the size of the *direct* sensing-direction acoustic term. Plugging in realistic physical values from a real gyroscope (`m ≈ 2.5×10⁻⁹ kg`, `f_ac ≈ 23 kHz`, `Ω ≈ 10 rad/s`, `k_s ≈ 22.2 N/m`) gives:

`G_rel ≈ 3.3×10⁻⁵ ≈ 0`

In other words, the cross-term is roughly 30,000 times smaller than the direct term — utterly negligible in practice. This mathematically justifies dropping the `A_d`-related term entirely, which the authors also confirmed experimentally (see §4.6 below).

### 4.6 The Simplified, Final Dynamic Gyroscope Model

**Equation (10):**
`Ω̂_gyro(t) ≈ Ω_gyro(t) + A_i · cos(2πF_i t + φ)`, where `A_i = A_s/S` and `F_i = |ω_ac − ω_d| / (2π)`

This is the practical formula they actually used to build their testbed's software gyroscope model. It says: **the reported angular rate is just the true angular rate, plus a simple additive sinusoidal disturbance**, at the *difference frequency* between the acoustic attack frequency and the gyroscope's internal drive frequency, with amplitude `A_i`. Structurally, this is now exactly the same shape as the accelerometer model (Eq. 3) — "true signal + additive sinusoid" — which is precisely why both sensor types can later be handled by the same kind of denoising approach.

### 4.7 Experimental Validation of the Model
To confirm Eq. 5's cross-term really is negligible in the real world (not just on paper), they took real gyroscope chips (MPU-6500, MPU-9250) on an Arduino, and ran three tests:
- Acoustic attack only (stationary): one peak at 89 Hz appeared in the output spectrum.
- Mechanical vibration only (simulating rotation/dynamics), no acoustic attack: two peaks at 35 and 70 Hz.
- Both together: the spectrum was just the *combination* of the previous two (peaks at 35, 70, and 89 Hz) — with **no extra peak** at the frequency `F_m + F_i` that would appear if the driving-direction cross-term (Eq. 5's second term) were actually contributing anything. Its absence experimentally confirms `G_rel ≈ 0`.

They also measured real resonance behavior across several IMU chips (Table II): acoustic frequencies near 27 kHz produced observable "induced frequencies" after sampling ranging from under 1 Hz to over 200 Hz depending on the exact chip — this table is what calibrated realistic amplitude/frequency ranges used later in all their simulated attacks.

---

## 5. Do They "Detect" Attacks? (Important Clarification)

This is a subtle but important point: **UnRocker does not perform binary attack detection** ("is an attack happening: yes/no") the way prior work does.

- Prior work explicitly proposed **detection-based approaches** ([9], [10] in the paper) — these monitor sensor behavior/control invariants and raise an alarm when something looks wrong. The paper's own critique of these is blunt: *"these do not include contingency plans for the drone's flight."* In other words, knowing you're under attack doesn't, by itself, help the drone keep flying its mission.
- Instead, UnRocker's philosophy sidesteps detection entirely: it sits inline between the sensor driver and the flight controller and **continuously runs every incoming sample through the trained denoising model**, all the time, whether an attack is actually present or not. Because the DAE is trained to map "possibly-noisy signal in" → "clean signal out," it doesn't need to first decide *whether* the signal is noisy — denoising a signal that's already clean should, ideally, just pass it through close to unchanged (this is the standard assumption behind denoising autoencoders in general, e.g., in image or speech denoising, where the same pipeline runs regardless of whether noise is actually present).
- The architecture diagram (Fig. 15) shows a queue of raw incoming data being fed into an "Inference Process / Recovery Model," with a labeled "SWITCH" near a box of "Modeled Data" — this suggests some selection mechanism exists in the pipeline (e.g., between feeding it live sensor data versus synthetic/testbed data during development and testing), but the main text does not describe an explicit runtime "attack detected, therefore activate recovery" decision gate. The system is architected to run recovery unconditionally on the live data stream.

**Bottom line:** the paper's contribution is a **recovery-first**, detection-free defense. It doesn't need to catch the attacker in the act — it just always tries to reconstruct the true signal from whatever it receives, which is a deliberate design response to the threat model's assumption of a persistent, hard-to-detect, multi-sensor attacker.

---

## 6. The Acoustic Injection Testbed

Since physically testing attacks on real drones is destructive and expensive, the authors built a repeatable software+hardware testbed.

- **Base platform:** PX4, an industry-leading open-source flight-control stack, chosen because it supports both simulation modes below.
- **SITL (Software-In-The-Loop):** the entire flight controller and physics are simulated in software — no real electronics involved. Fast, safe, but *idealized* (no real hardware timing quirks).
- **HITL (Hardware-In-The-Loop):** the real flight-controller board (a **Pixhawk**, equipped with **ICM-20689** primary and **ICM-20692** secondary MEMS IMUs — both gyros + accelerometers) runs the actual firmware, while the drone's physical flight dynamics are simulated (using the **Gazebo v9.15** physics engine, which provides realistic 6-degree-of-freedom simulation matching real flight data).
- **Target drones:** **3DR Iris** and **3DR Solo** — both default, widely used PX4-based drones, both built around the Pixhawk board.
- **Sensor resonance models injected into the simulator:** the accelerometer model (Eq. 3) and the newly-derived dynamic gyroscope model (Eq. 10) were coded directly into PX4's sensor driver layer, so the simulator could "pretend" a resonance attack was happening on demand, at any chosen amplitude/frequency, with no risk to physical hardware.
- **Automation:** they modified PX4's messaging modules (to remotely configure attack parameters), added logging of sensor values/attitude/position/timestamps, and wrote automation scripts to run large batches of repeatable experiments — **1,347 lines of C++/Python** for the testbed itself (later expanded; see Table VII in §14 for the full project's line-count breakdown, ~4,964 lines total across firmware, automation, and the ML pipeline).
- **Physical validation setup:** a RIGOL DG1000 function generator feeding a JBL Stage A120 speaker through an Omnitronic MK-60DG amplifier, used to physically verify that the software sensor models match real chips' behavior (Arduino + MPU-6050/6500/9150/9250 and Pixhawk's ICM-20689/20602 — see Table II for measured resonance behavior).

---

## 7. The Central Discovery: Sampling Jitter

### 7.1 The Puzzling Result
Using **sine-wave testing** (a standard robustness-testing technique — feed sinusoidal disturbances of varying amplitude/frequency into the control system and see what happens), they injected simulated resonance attacks (gyroscope: amplitude 0–4 rad/s, frequency 0–250 Hz; accelerometer: amplitude 0–90 m/s², frequency 5,000–5,250 Hz — ranges chosen from real measured values, see Table II, and from the maximum sampling-frequency-limited range) into both SITL and HITL:

- **SITL:** the drone almost never crashed.
- **HITL and an actual physical 3DR Solo drone:** the drone crashed in essentially every test case.

This directly contradicts the prior widespread belief that "resonating a sensor is sufficient to crash a drone."

### 7.2 Why SITL Resists — Two Protective Mechanisms
1. **Digital low-pass filters (LPFs)** in the sensor driver software — separate from any analog filtering inside the chip — which strip frequencies above a cutoff (30 Hz in their target drone).
2. A **very narrow in-band control range.** By disabling the LPF and directly injecting swept-frequency resonance signals from 0–125 Hz (half the 250 Hz sampling rate) straight into the control logic, they found the drone only actually crashed for injected frequencies of **0–5 Hz** — everything from 5–125 Hz was simply ignored by the control loop, regardless of amplitude.

To pin this down precisely, they built a formal **system identification** model: treating the drone as a closed-loop control system (angular rate command in, actual roll/pitch/yaw out — Fig. 9), they fed real input/output pairs collected from the testbed into MATLAB's System Identification Toolbox to fit a transfer function `G(s)`. Measuring the standard **-3 dB bandwidth point** (the industry-standard way to define a control system's "stable response range") gave:
- Roll: 4.32 Hz
- Pitch: 5.37 Hz
- Yaw: 0.005 Hz

So the drone's *actual* usable control bandwidth is roughly **0–5 Hz** — a tiny window. Since the aliased resonance signal (per Eq. 2) usually lands somewhere across the full 0–125 Hz range, most of the time it simply misses this narrow window, and the digital LPF cleans up much of what's left. Combined, in the *ideal, jitter-free* SITL world, resonance attacks mostly fail.

### 7.3 What Changes in the Real World: Sampling Jitter
**Sampling jitter** = inconsistent timing delay in *when* sensor values are actually retrieved, from one sample to the next — it means the "250 Hz" sampling isn't perfectly evenly spaced in reality.

They measured this directly by monitoring the timing of sensor reads:
- SITL: essentially zero timing variation (std. dev ≈ 0).
- HITL (real Pixhawk board): std. dev ≈ **457 µs**.
- Actual flying 3DR Solo: std. dev ≈ **103 µs**.

**Why jitter matters so much:** aliasing (Eq. 2) assumes perfectly uniform sampling. If the sampling times are irregular, the "folded down" resonance signal doesn't land cleanly at one alias frequency — it gets **smeared across a broad range of frequencies**, as visualized by comparing spectra:
- benign signal spectrum (clean, low-frequency energy),
- an *ideal* SITL resonance spectrum (one sharp peak, out-of-band, e.g., at 100 Hz),
- the *actual* HITL resonance spectrum *before* filtering (energy spread across 0–125 Hz — including the fragile 0–5 Hz control band), and
- the HITL spectrum *after* the digital LPF (30 Hz cutoff) — a lot of that smeared energy below 30 Hz survives filtering, and specifically the portion inside 0–5 Hz keeps going and disturbs the flight controller.

They confirmed the causal chain directly: adding **artificial, controlled jitter** into an otherwise-clean SITL simulation was, by itself, enough to make drones crash — and the *thresholds* needed were tiny:
- **Gyroscopes:** as little as **~80 µs** of jitter caused crashes.
- **Accelerometers:** as little as **~6 µs** of jitter caused crashes.

These thresholds are far below the jitter that's *already naturally present* in any real flight controller (hundreds of µs), which is why real drones are essentially always exposed once resonance occurs at all.

They also quantified the effect via **signal-to-noise ratio (SNR):**
- Without jitter: SNR of **+15 to +33 dB** (signal clearly dominates noise).
- With jitter: SNR of **-23 to -5 dB** (noise now dominates the signal — a dramatic, safety-critical flip).

They repeated this on an actual flying drone (§V-D): by artificially adjusting timestamps, they could compare "resonance without jitter" vs. "resonance with natural jitter" side-by-side in real flight — confirming the same qualitative crash-vs-no-crash pattern outside of simulation.

### 7.4 Where Does Jitter Actually Come From? (Three Root Causes)
1. **RTOS scheduling jitter.** PX4 runs on the NuttX real-time OS. An RTOS guarantees tasks finish *by* a deadline, but not *at* a fixed instant — so consecutive sensor-read tasks don't fire at perfectly equal intervals. Measured: **124.6 µs** standard deviation — comparable to the period of the (multi-kHz) acoustic signal itself, which is exactly why it's enough to meaningfully disturb the aliasing pattern.
2. **Operation interval mismatches.** The sensor driver's internal polling rate and the flight-control loop's rate are often different by design. E.g., in ArduCopter, the InvenSense driver samples at **1,000 Hz** while the control logic itself runs at **400 Hz** — a 2.5× mismatch (see Table VI in §14 for many more sensor/board combinations with similar mismatches). This misalignment is invisible during normal operation (the sensor loop just buffers new data for the main loop) but becomes a jitter source once the *content itself* (a resonance signal) is time-sensitive.
3. **Imprecise, unsynchronized clocks.** Each MEMS sensor has its own onboard clock driving its analog-to-digital sampling, and this clock is *not* synchronized with the flight controller's main clock. They measured about a **5% precision mismatch** between these clocks — small in absolute terms, but enough to shift where a multi-kHz signal aliases to.

### 7.5 Two Misbeliefs Debunked
- **"Low-pass filters mitigate the attack" — false.** Whether analog (inside the chip) or digital (in the driver), neither type of LPF can fully remove a resonance signal once sampling jitter has smeared it into the in-band frequency range — some of the corrupted signal always survives filtering and reaches the control logic.
- **"Randomizing the sampling rate prevents spoofing" — true, but it backfires.** Random sampling phase does help against *precisely targeted spoofing* (an attacker trying to plant a specific frequency exactly in-band), because it's a form of the earlier "difficult to intentionally target the in-band range" argument. But it acts *exactly* like extra jitter: it spreads the resonance signal across more frequencies, meaning **DoS crashes become easier**, not harder. Also, perfectly uniform sampling is basically impossible on real hardware anyway, so real systems already carry inherent non-uniformity that randomization only worsens from a DoS perspective. The authors' conclusion: sampling randomization trades one vulnerability for a worse one and is not a real solution.

---

## 8. What "Security" Means in This Paper

It's worth being explicit about the **security goal** this paper is optimizing for, because it's different from "prevent the attack" or "detect the attacker."

- They are **not** trying to stop the acoustic signal from reaching the sensor (that would require physical/RF-style shielding, which they explicitly reject as impractical and previously shown to cause heating problems).
- They are **not** trying to identify or attribute the attacker (no detection/alerting system).
- Their security property is: **availability of correct control** — i.e., even while a sustained, full-coverage acoustic attack is happening, the drone should still be able to compute *accurate enough* attitude/position estimates to safely continue flying its pre-planned mission to completion (visiting all its waypoints and returning), rather than crashing or having to abort.
- This is fundamentally a **resilience / fault-recovery** framing of security, borrowed conceptually from **signal denoising** in other safety-critical domains (the paper explicitly cites DAE use in medical imaging and industrial process monitoring as precedent for trusting ML-based noise removal in safety-critical settings).
- Success is measured quantitatively via: (a) standard deviation of the *error* between recovered and true benign sensor values (lower = more secure/more accurate), (b) SNR of the recovered signal (higher = more secure), and (c) the ultimate real-world proxy — **did the drone actually complete its flight mission without crashing**, tested end-to-end in Fig. 19's real-time recovery trial.

---

## 9. How They Impose Security: The UnRocker System

### 9.1 High-Level Architecture (Fig. 15)
UnRocker is placed **in-line, in software, between the sensor drivers and the flight controller**, inside the drone's own firmware pipeline:

```
[ MEMS Sensor ] → [ Sensor Driver ] → [ UnRocker Recovery Model ] → [ Flight Controller ]
```

Concretely, its runtime behavior is:
1. **Ingest:** receive each new digitized sensor sample as it comes off the driver.
2. **Buffer:** push it into a **sliding-window queue** — enough recent samples (256 samples, ≈1 second of data) to give the neural network temporal context, since denoising a single instantaneous value with no history isn't feasible.
3. **Infer:** feed the current window into the pre-trained DAE model to produce a denoised estimate.
4. **Deliver:** hand the denoised (recovered) value to the flight controller instead of the raw, possibly-corrupted one.

This is intentionally a **drop-in layer** — it requires no extra physical sensors, no hardware modification, and no change to how the flight controller consumes sensor data; it only intercepts and cleans the data stream.

### 9.2 Why a Denoising Autoencoder, Specifically
Reasoning chain used by the authors:
1. Section 7's finding shows the compromised signal is structurally "**true signal + additive noise**" (exactly what Eqs. 3 and 10 formalize).
2. This is precisely the canonical problem that **denoising** techniques are built for.
3. They first tried classic, non-ML heuristic denoising filters:
   - **Savitzky–Golay (Sav-Gol) smoothing filter** — a polynomial-fitting smoother, commonly used for cleaning up noisy time-series.
   - **Wavelet-domain Wiener filter** — an industry-standard statistical noise-removal technique.
   - **Result: both failed** — neither prevented drones from crashing in live testbed trials, even though they can reduce the numeric error somewhat (see comparison numbers in §15).
4. Given the failure of classical filters, they moved to a **deep neural network** — specifically a **Denoising Autoencoder (DAE)**, a well-established architecture (encoder compresses noisy input to a compact latent representation; decoder reconstructs a clean signal from that representation), already validated in other safety-critical or high-stakes domains (medical imaging, industrial fault detection, ECG signal cleanup, speech enhancement).
5. They explicitly do **not** claim the DAE is the theoretically optimal choice — their contribution is demonstrating that a denoising-based *framing* of this security problem works at all, opening the door for future work with better denoising architectures.

### 9.3 Model Architecture Specifics (Fig. 16)
- Standard DAE shape: `Input → [Conv/Pooling/Dense encoder layers] → bottleneck → [Dense/Conv/Upsampling decoder layers] → Output`.
- **Input window size: 256 samples** (~1 second at their sampling rate) — chosen as a tradeoff: fewer samples reduce computation/latency but hurt the network's ability to capture the true signal's tendency; more samples help accuracy but cost more compute/latency (important given the drone's real-time constraints — see §13).
- **Batch size: 32** — chosen empirically; larger batches train faster but use more memory and slightly hurt final accuracy; smaller batches slow training; 32 was the point where accuracy saturated.
- **Depth:** **18 layers** for the accelerometer model; **14 layers** for the gyroscope model (4 fewer Conv+Pooling pairs on each side of the network, found empirically to work best for that sensor's signal characteristics).
- **Training:** 500 epochs, step size 4, loss floor target of 0.001; the accompanying training curve (Appendix Fig. 22) shows both training and validation loss converge and stabilize after ~300 epochs, confirming the model reached a stable, complete training state (not overfit or undertrained).
- **Framework:** built in TensorFlow/Keras for training; later ported to **NVIDIA TensorRT** for fast real-time inference (see §13).
- **Model granularity:** a **separate trained model per sensor axis, per sensor type, per drone** — i.e., 12 distinct pre-trained models total (2 drones × 2 sensor types × 3 axes), each specialized rather than one giant shared model.

---

## 10. Training the Recovery Model — All Details

### 10.1 The Dataset Problem
Training a deep model needs a lot of labeled data — here, pairs of `(compromised signal, true benign signal)`. Physically producing this at scale (real speaker, real anechoic chamber, real sensors, thousands of repeated attack sessions) is slow, expensive, and can permanently damage hardware. Their workaround: use their **own validated software sensor models** (Eqs. 3 and 10) inside **HITL simulation** to *synthetically generate* matched pairs at scale, while flying realistic simulated missions.

### 10.2 Why HITL (Not SITL) for Training Data
Crucially, they generated training data using **HITL**, not SITL — because HITL naturally includes the real hardware's sampling jitter, which (per §7) is *the actual thing making the attack dangerous.* They explicitly tested this choice (Appendix §C, detailed in §14 below): a model trained on jitter-free SITL data completely failed to recover real (jittery) signals, while a model trained on jittery HITL data worked — and performed **2.24× better**. This is a clean, direct confirmation that sampling jitter isn't just a diagnostic finding, it's a *load-bearing part of the defense's training methodology.*

### 10.3 Dataset Construction Procedure
- **Drones:** 3DR Iris and 3DR Solo, both flying a real mission: 7 randomly placed waypoints at altitudes of 25–100 m, total path length ≈1.33 km, each full mission run taking ~6 minutes (≈90,000 sensor samples per run).
- **Signals collected per run:** both the resonant (compromised) signal and its paired benign ground-truth signal, for each of 3 axes (x/y/z) × 2 sensor types (gyro, accel), *without letting the resonant signal actually reach the flight controller* — i.e., the drone flew a real, undisturbed mission while the corrupted signal was recorded in parallel purely for dataset purposes.
- **Amplitude sweep:** gyroscopes at 0, 1, 2, 3, 4 rad/s; accelerometers at 0, 20, 40, 60, 80 m/s² — chosen to bracket realistic real-world attack strength (per Table II measurements and prior literature).
- **Repetition:** the whole collection procedure repeated **6 times**, giving 6 independent 6-minute signal sets per axis/sensor/drone combination.
- **Split:** each set of 6 was divided **4:1:1** into training / validation / testing.
- **Total scale:** **360 six-minute signal pairs**, roughly **32.4 million individual sensor-value pairs** (2 drones × 2 sensors × 3 axes × 5 amplitudes × 6 repetitions = 360 sets).

---

## 11. Full Evaluation Results

Performance metric: **standard deviation of the error** between the recovered signal and the true benign signal (lower is better; this specific metric was chosen because it's the same one used in prior related work, allowing direct comparison).

### 11.1 Test-Set Recovery (Simulated HITL Data)
Best/worst-case example from Table III: for the highest attack amplitude tested, accelerometer error dropped from **56.64 → 0.296**, and gyroscope error dropped from **2.83 → 0.013** — both roughly two orders of magnitude smaller, and small enough to be within the natural range of a benign signal (i.e., practically indistinguishable from clean data).

### 11.2 SNR-Based Confirmation (Table VIII, Appendix)
Recovered signal SNR improved from strongly negative (as low as **-27.6 dB**, meaning noise massively dominates) up to **+25.6 dB** (signal massively dominates) in the best cases — the recovered signal is no longer overwhelmed by attack-induced noise.

### 11.3 Physical Sensors, Simulated Motion + Injected Software Resonance
With a real Pixhawk board physically shaken (to emulate flight dynamics) while software-injected resonance was layered on top:
- Accelerometer error: **2.361 → 0.935**
- Gyroscope error: **1.469 → 0.025**

### 11.4 Real Recorded Flight Data + Synthetic Resonance
Using 90 seconds (~22,500 samples) of sensor logs from an actual 3DR Solo flight, with resonance signals synthetically added afterward: UnRocker reduced the error across all axes for both sensor types (last row of Table III), showing the models trained on HITL data generalize to genuinely real flight dynamics, not just simulated ones.

### 11.5 Fully Real: Actual Acoustic Speaker Attacking a Flying Drone
The most realistic test: a small Bluetooth speaker placed 10 cm from the drone's Pixhawk board (110 dB SPL) actually played the attack tone at the drone in flight. They exploited the fact that the primary and secondary IMUs on the Pixhawk have **different gyroscope resonance frequencies**, so they configured the drone to fly using the *secondary* IMU (keeping it stable) while treating the *primary* IMU's corrupted gyroscope data as the thing to recover, using the secondary IMU's clean data as ground truth for evaluation.
- Gyroscope error: **0.71 → 0.044 rad/s** — a strong, real-world confirmation.
- **Accelerometer recovery could not be validated this way**, because the primary and secondary accelerometers happen to share very similar resonance frequencies, so *both* get corrupted simultaneously by the same attack — there was no clean "ground truth" IMU available for this specific test. (The paper notes that swapping in a differently-tuned IMU is possible in principle but requires nontrivial hardware/calibration expertise.)

---

## 12. Security Against an Adaptive/Evasive Attacker

Since the attacker might try to dodge the trained recovery model (e.g., by shifting frequency slightly, or varying amplitude to avoid whatever the model learned), the authors specifically stress-tested this ("Evasion of UnRocker," Table IV):

- **Frequency-shift attacks:** resonance signals shifted **±100 Hz** from the frequencies used in training (chosen because real sensor resonance is known to naturally spread over roughly a ±100 Hz range anyway).
- **Amplitude-drift attacks:** attack amplitude continuously varied following a slow sinusoidal pattern (50-second period), rather than staying at one fixed value.
- **New, held-out dataset:** 54 additional test cases generated the same way as the main dataset but never seen during training.
- **Result:** UnRocker's recovery performance remained reliable across all 54 cases — the error stayed within the range typical of benign signals, showing the model **generalizes** reasonably well to nearby frequencies/amplitudes rather than being narrowly overfit to the exact attack parameters it was trained on.

The paper also separately argues (not via UnRocker, but via physical/engineering reasoning) that it's inherently very hard for an attacker to precisely hit the drone's narrow in-band frequency window (0–5 Hz) on purpose:
- For **gyroscopes:** the frequency at which resonance appears depends on the *difference* between the acoustic frequency and the sensor's internal drive frequency (`ω_ac − ω_d`, per Eq. 10). The drive frequency isn't perfectly fixed — it drifts meaningfully with manufacturing tolerances, temperature, and aging, especially once the drone is actually flying (versus sitting still on a bench where an attacker might have calibrated). An attack tuned against a stationary drone is likely to miss the moving target's real in-band window. (Manufacturers even deliberately design a *wide, flat* gyroscope response region specifically to absorb this kind of drift, which incidentally also gives the attacker's precision problem more room to go wrong.)
- For **accelerometers:** they are shown (§7.3) to be *even more* sensitive to sampling jitter than gyroscopes — meaning any attempt to aim precisely at the narrow in-band window gets smeared out by jitter before it can land there cleanly. A prior study (Tu et al.) is cited as independently observing that only DoS (not precise spoofing) was achievable against accelerometers, without being able to fully explain why — this paper's sampling-jitter finding supplies that missing explanation.

---

## 13. Real-Time Engineering & Practical Limits

The ultimate goal is **real-time** recovery — the drone's control loop needs a fresh sensor value **every 4 ms** (PX4's 250 Hz sampling rate), but typical ML inference for signal-processing tasks can take multiple seconds, far too slow.

### 13.1 How Much Delay Can the Drone Actually Tolerate?
They measured this empirically by forcibly delaying sensor delivery in HITL simulation and finding the point where the drone crashes:
- **Gyroscopes:** must be recovered within **24 ms**.
- **Accelerometers:** must be recovered within **2.8 seconds** — vastly more forgiving.
- **Why the huge difference:** gyroscopes are inherently more sensitive to fast dynamic changes (rotation can change quickly), so stale gyroscope data goes wrong faster than stale acceleration data.

### 13.2 Making Inference Fast Enough
- Ported the trained TensorFlow DAE to **NVIDIA TensorRT**, a deep-learning inference optimizer built specifically for low latency and high throughput (at some cost to raw accuracy/flexibility).
- Added a fallback behavior: if a new recovered value isn't ready in time for the next control-loop tick, UnRocker just **reuses the previous recovered value** rather than stalling the pipeline.
- **Measured inference times** (Appendix Fig. 24): 4–8 ms on a laptop (HP ProBook), ~12 ms average on an NVIDIA Jetson Nano, ~9 ms on a Jetson TX2. None of these fully hit the 4 ms budget, but they're in a workable range, especially for the accelerometer's much more forgiving 2.8 s tolerance.
- **Power draw:** the Jetson Nano running UnRocker consumed only **1.6 W**, confirming power isn't a practical obstacle to putting this on small drones.

### 13.3 Actual Real-Time Results
- **Accelerometers:** real-time recovery **worked** — in a live trial (Fig. 19), a drone under continuous attack successfully completed its entire waypoint mission using recovered accelerometer data, whereas an unprotected drone crashed straight to the ground.
- **Gyroscopes:** real-time recovery was **not fully successful** in their setup. Investigating why, they found the culprit wasn't the AI model's accuracy — it was a **~30 ms communication delay** between the Pixhawk board and the external processing server running the model. They proved this by artificially adding a plain 30 ms delay to *benign* (non-attacked) gyroscope signals and observing the same kind of fluctuation/crash pattern as with the "recovered" signal — meaning the recovery model itself was fine, but the surrounding data-transmission latency (well beyond the 24 ms gyroscope budget from §13.1) was the actual bottleneck.
- **Proposed fix (future work, not implemented here):** move the inference **on-board**, using an edge-AI chip (e.g., NVIDIA Jetson) physically attached to the drone, eliminating the round-trip communication delay entirely.

---

## 14. Appendix Findings (Jitter Root-Cause, Deep Dive)

### 14.1 Multi-Stage Aliasing — A Worked Real Example
The appendix walks through a concrete real measurement to show how aliasing can happen **more than once**, at different stages of the sensor pipeline, compounding the confusion:
- The accelerometer chip (ICM-20689, using an MPU6000-series driver) internally samples at **1 kHz**.
- An attack tone at **1.83 kHz** gets aliased once by this 1 kHz internal sampling: `|1830 Hz − 1000 Hz × 2| = 170 Hz`.
- That 170 Hz result then gets aliased *again* by the separate microcontroller unit (MCU)'s own sampling at **250 Hz**: `|170 Hz − 250 Hz| = 80 Hz`.
- So the final observed peak lands at 80 Hz — two rounds of aliasing chained together, from two different clocks in the same sensor pipeline.
- However, the real measured spectrum (Fig. 21a) showed **extra peaks around 10 Hz and 50 Hz** that this simple double-aliasing calculation alone doesn't explain. Adding in the two other known jitter sources — **scheduling jitter** (124.6 µs, measured on PX4's NuttX RTOS) and a **5% clock-source precision mismatch** between the MCU/ADC's independent clock and the sensor's own clock — into a recomputed spectrum (Fig. 21b) reproduced a spectrum shape much closer to the real measurement. This is strong supporting evidence that all three jitter sources identified in §7.4 are real and simultaneously contribute.

### 14.2 Direct Hardware Measurement of Sampling Timing
Beyond just measuring how long between reads, they physically monitored the **interrupt signals** on the wire between the IMU chip and the flight controller with an oscilloscope (the IMU raises an interrupt line when new data is ready; the FC lowers it again once it's read the data). The timing of these voltage transitions fluctuated by roughly **100 µs**, directly, physically confirming that sampling jitter is a real, observable hardware phenomenon and not just an artifact of software timestamp logging.

### 14.3 Sensor/Control-Loop Rate Mismatches Across Many Real Boards (Table VI)
The appendix lists sampling frequencies for many real IMU chips used across ArduCopter (400 Hz control loop) and PX4 Quadcopter (250 Hz control loop) firmware — e.g., BMI055 samples at 2,000 Hz, BMI160 at 1,600 Hz, InvenSense chips at 1,000 Hz, L3G4200D at 800 Hz, etc. — showing that a sensor-driver-rate vs. control-loop-rate mismatch (root cause #2 from §7.4) is not a one-off quirk but a **widespread pattern** across many real, commercially deployed sensor/firmware combinations.

### 14.4 Confirming Jitter Must Be in the Training Data
They ran a controlled ablation: train one DAE on SITL data (no jitter) and another on HITL data (real jitter), then test both against real, jittery signals.
- **SITL-trained model:** completely failed — output showed large, unstable fluctuations (Fig. 23a) — meaning it never learned to handle the kind of noise pattern jitter actually produces.
- **HITL-trained model:** successfully recovered clean output (Fig. 23b).
- Quantitatively, on physical-sensor tests, the jitter-aware model performed **2.24× better** than the jitter-naive one. This is why, throughout the rest of the paper, all real training data collection deliberately used HITL (not SITL) simulation — sampling jitter isn't just a diagnosis, it's a required ingredient in building a working defense.

### 14.5 Implementation Scale (Table VII)
Total system implementation: **4,964 lines of code**, broken down as:
- Drone firmware (C++): sensor drivers/filesystem changes (330), messaging modules (202), state logger (37), attitude/position control tweaks (103) — 672 lines.
- Automation (Python): simulator control (183 firmware-side + 492 automation-side) = 675 lines.
- Dataset construction (Python): 458 lines.
- Model training (Python): DAE model (55), training script/data loader (566), validation script/loader (450), test script/loader (633) — 1,704 lines.
- Online inference (Python): inference program (208), communication program (405), gyroscope recovery test (514), accelerometer recovery test (438) — 1,565 lines.

---

## 15. Comparisons to Other Methods

### 15.1 vs. Classical Heuristic Filters (Table V)
Comparing UnRocker against a plain low-pass filter (LPF), Savitzky–Golay smoothing, and a Wiener filter, measured as the *factor* of error reduction relative to an unfiltered compromised signal:
- UnRocker outperformed the plain **LPF by up to 72×**.
- UnRocker outperformed **Sav-Gol by up to 46×**.
- UnRocker outperformed the **Wiener filter by up to 23×**.
- Critically, when actually run live in the testbed (not just measured numerically), **all three classical filters still failed to prevent drone crashes**, while UnRocker succeeded — showing that raw numeric error reduction doesn't automatically translate into flight safety, and the margin UnRocker provides matters.

### 15.2 vs. Other Deep-Learning Denoisers
- **Deep-Feature-Loss (DFL) network** (a model originally designed for speech denoising): slightly *better* than UnRocker on the exact training-distribution test data (0.171 vs. 0.289 rad/s error on one specific case), but its performance **collapsed badly on unseen data** — frequencies it hadn't trained on, and real (not synthetic) sensor data — producing errors **23–193% larger** than UnRocker in those generalization tests. This suggests DFL overfits more to its exact training conditions.
- **CMGAN** (a state-of-the-art conformer-based GAN for speech enhancement): when they tried training it directly on the IMU sensor dataset, its training loss got stuck around **0.8** and never converged, versus UnRocker's DAE reaching a training loss below **0.0001**. Conclusion: architectures highly specialized for speech audio don't transfer cleanly to this sensor-signal domain without significant additional engineering — reinforcing that the DAE, while conceptually simple, was actually a well-matched, deliberate choice for this specific recovery problem, not just a default pick.

---

## 16. Answers to the Key Questions, Concisely

**Q: How do they detect attacks?**
A: They largely **don't** — that's a deliberate design choice. Instead of a detect-then-react pipeline, UnRocker continuously denoises every incoming sensor sample in real time regardless of whether an attack is currently happening, sidestepping the "detection with no contingency plan" limitation they identify in prior detection-based defenses.

**Q: What is the threat model?**
A: A remote attacker with full knowledge of the target drone's sensor models, able to broadcast a strong, sustained acoustic/ultrasonic signal (e.g., via an LRAD) capable of resonating **all** onboard MEMS gyroscopes and accelerometers simultaneously, for as long as they want, without needing physical or network access to the drone. This model deliberately rules out defenses relying on backup sensors or short-duration recovery.

**Q: What does "security" mean here, and how is it enforced?**
A: Security = the drone's ability to keep computing accurate-enough attitude/position estimates and **complete its actual flight mission** despite an ongoing, full-coverage acoustic attack — not detection, not attribution, not blocking the acoustic signal itself. It's enforced by inserting **UnRocker**, a pre-trained denoising autoencoder, in-line between the sensor drivers and the flight controller, which reconstructs clean sensor values from corrupted ones sample-by-sample using a sliding window, with an optimized (TensorRT) real-time inference path.

**Q: What are the equations really saying, in one line each?**
- Eq. 1 (Coriolis force): rotation + a vibrating mass produces a perpendicular force proportional to rotation rate — this is *how a gyroscope senses rotation at all.*
- Eq. 2 (aliasing): a high-frequency attack signal, once under-sampled, reappears disguised as a much lower, in-range-looking frequency.
- Eq. 3 (accelerometer model): compromised signal = true signal + one added sine wave at the attack frequency.
- Eqs. 4–5 (gyroscope displacement/output): the raw signal is a mix of the real rotation signal, a rotation-dependent cross-term from driving-direction acoustic noise, and a direct sensing-direction acoustic noise term; after demodulation/filtering, the surviving disturbance sits at the frequency difference between the attack and the gyroscope's internal drive frequency.
- Eqs. 6–9 (relative gain derivation): mathematically (and then experimentally) prove the rotation-dependent cross-term is ~30,000× smaller than the direct term, so it can be safely ignored.
- Eq. 10 (final simplified gyroscope model): same simple "true signal + one added sine wave" shape as the accelerometer — this shared simplicity is exactly what makes a single denoising strategy applicable to both sensor types.

**Q: What's the single biggest finding?**
A: **Sampling jitter — not sensor resonance itself — is what actually causes drones to crash during acoustic injection attacks**, because it spreads an otherwise-harmless, out-of-band aliased attack signal across the drone's fragile in-band control frequencies, defeating both analog and digital low-pass filtering.

**Q: What's the fix, concretely?**
A: **UnRocker** — a denoising autoencoder trained on jitter-realistic (HITL-derived) data, deployed in-line in the sensor pipeline, continuously reconstructing benign sensor values from corrupted ones, with an engineering path (TensorRT, fallback-to-last-value) toward real-time use — successfully demonstrated end-to-end on accelerometers (full mission completion under live attack) and substantially improved (though not fully real-time) for gyroscopes, limited currently by communication latency rather than model accuracy.
