# Four CPS Attack-Recovery Papers — Differences, Lineage & Connections

A focused read of four papers, in the order requested. Full titles used as references throughout:

- **[SSR]** — *"Software-based Realtime Recovery from Sensor Attacks on Robotic Vehicles"* — Choi, Kate, Aafer, Zhang, Xu (Purdue / U. Waterloo), **RAID 2020**.
- **[Gecko]** — *"Software Availability Protection in Cyber-Physical Systems"* — Li, Wang, Zhang (Washington University in St. Louis), **USENIX Security 2025**.
- **[PID-Piper]** — *"PID-Piper: Recovering Robotic Vehicles from Physical Attacks"* — Dash, Li, Chen, Karimibiuki, Pattabiraman (UBC / U. Iowa), **IEEE/IFIP DSN 2021**.
- **[DeLorean]** — *"Diagnosis-guided Attack Recovery for Securing Robotic Vehicles from Sensor Deception Attacks"* — Dash, Li, Karimibiuki, Pattabiraman (UBC / U. Iowa), **ACM ASIA CCS 2024**.

---

## The big picture in one paragraph

Three of these — **SSR → PID-Piper → DeLorean** — form a **single, tight lineage** on exactly one problem: *recovering a robotic vehicle (RV) that is still flying/driving while its **physical sensors** are being spoofed*. Each one is explicitly built to fix a named limitation of the one before it. The fourth — **Gecko** — is on a **different axis entirely**: it recovers the *software's availability* under **cyber / memory-corruption** attacks, a threat the other three explicitly declare **out of scope** ("cyber attacks are handled separately"). In fact Gecko treats SSR / PID-Piper as *reusable components* it can plug in (its Table 3 lists them as "Existing Methods Compensating Sensor Unavailability"). So the right mental model is **a 3-step ladder (SSR→PID-Piper→DeLorean) standing next to a separate pillar (Gecko)** that leans on the ladder.

```
     PHYSICAL sensor-attack recovery (transduction channel)          CYBER / software-availability recovery
     ─────────────────────────────────────────────────────          ────────────────────────────────────
        SSR (2020)                                                        Gecko (2025)
          │  "FBC over-compensates; drift; linear ⇒ stealthy-weak"          defends the *memory-corruption*
          ▼                                                                 channel the other three exclude;
        PID-Piper (2021)                                                    REUSES SSR / PID-Piper inside its
          │  "still bounded-error ⇒ crashes under MULTI-sensor"             "shadow compartments" (its Table 3)
          ▼                                                          ┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄
        DeLorean (2024)   ── diagnoses WHICH sensor ⇒ targeted recovery
```

---

## 1. Per-paper insight (motivation → method → results → limitation)

### [SSR] *Software-based Realtime Recovery from Sensor Attacks on Robotic Vehicles* (RAID 2020)
- **Motivation.** Physical sensor attacks (GPS spoofing, acoustic gyroscope/accelerometer injection, optical-flow spoofing, ABS magnetic injection, LiDAR/camera injection) can crash an RV. Prior work only **detects**; hardware redundancy (Triple Modular Redundancy) is costly and *still* fails because all replicas share the same physical environment and can be attacked at once.
- **Method.** **"Software sensors."** For each physical sensor, build a **state-space model via system identification (SI)** that predicts the vehicle's next physical state, then convert that state into the corresponding sensor reading through kinematic conversion equations. A **recovery switch** continuously compares the real reading against the software-sensor prediction; when the accumulated discrepancy over a time window exceeds threshold `T_on`, it swaps the compromised sensor for its software version until the attack subsides (`T_off`). Because pure prediction *drifts*, it adds **error correction**: low-pass filtering, periodic **synchronization/reset** to real readings, and **supplementary compensation** (e.g., estimate gyro angles from accelerometer + magnetometer).
- **Results.** 6 RVs (quadrotor, hexarotor, rover; **2 real**: 3DR Solo, Erle-Rover). Recovers single- **and** multi-sensor attacks (same or different kinds); ≤1.3% memory and ≤8.8% runtime overhead; prevents crashes. Recovery lasts **≥10 s** in most cases — enough to trigger a safe emergency action (empirical human takeover is 3–7 s).
- **Limitation.** It is **model-based**, so accuracy is bounded by the SI model; **software sensors drift**, making recovery **temporary, not permanent** (the all-gyroscope-compromised case lasts only ~3 s); the **linear** state-space model is weak against carefully shaped **stealthy** attacks; it assumes the attacker cannot see the internal state / navigation plan (so cannot stay under threshold) and that **cyber attacks are handled separately**.

### [PID-Piper] *PID-Piper: Recovering Robotic Vehicles from Physical Attacks* (DSN 2021)
- **Motivation (directly attacks SSR's weaknesses).** Detection-only methods (Control Invariants, SAVIOR) merely raise an alarm and abort the mission. SSR — the only prior *recovery* — uses a **Feed-Back Controller (FBC)** that **over-compensates** for a *systematic* attack bias (because the PID controller has **high collinearity** among inputs, measured by Variance Inflation Factor), and its **linear** model is vulnerable to stealthy attacks; SSR also just holds the RV briefly, needing **manual intervention** to finish the mission.
- **Method.** A **machine-learning Feed-Forward Controller (FFC)**: an **LSTM** trained (with **feature engineering** to break collinearity) to predict the *PID controller's output* (the actuator signal) under attack-free conditions. It runs **in tandem** with the real PID; a **CUSUM** monitor tracks the deviation `δ = |y_FFC − y_PID|` against a **tight threshold τ** (tight because the ML model is precise). On alarm the FFC drives the RV (rectifying the actuator output directly) until the attack stops. Feed-forward + a noise model means it **does not over-compensate** the way SSR's feed-back design does.
- **Results.** 6 RVs (**3 real**: Pixhawk drone, Aion rover, Sky-viper drone). **83%** mission success under overt attacks (vs **13%** for SSR, **0%** for CI/SAVIOR); **0%** false positives (vs 10% SSR); **0%** crashes (vs 35% SSR); limits **stealthy** attacks **7×**, giving **100%** stealthy-attack mission success (others 0%); ≤7% overhead; 2.8× smaller deviation than SSR.
- **Limitation.** Threshold-dependent; ~17% of overt attacks still fail (attacks late in the mission that the ML can't correct in time); stealthy attacks are **limited, not eliminated**; it is still a **single-vehicle, bounded-error, per-controller** model — and (as DeLorean shows) it **crashes under simultaneous multi-sensor attacks**. Assumes the attacker cannot bypass the FFC, has no firmware/root access, and cannot poison the ML training data.

### [DeLorean] *Diagnosis-guided Attack Recovery for Securing Robotic Vehicles from Sensor Deception Attacks* (ASIA CCS 2024)
- **Motivation (directly attacks the limits of BOTH SSR and PID-Piper).** Model-based recovery (**SSR, PID-Piper**) tolerates only a **bounded** error, so its approximation breaks down under **multi-sensor** Sensor Deception Attacks (SDAs). Checkpoint-based recovery (Zhang et al.'s **LQR-O**) assumes **all** sensors are compromised and performs **worst-case** maneuvers — overly conservative/aggressive, causing dangerous deviations especially during takeoff/landing. Neither knows **which** sensors are actually attacked, and detection delay corrupts recovery under stealthy attacks.
- **Method.** **Attack diagnosis via factor graphs.** DeLorean monitors error inflation across **all** EKF-fused physical-state estimates and runs **probabilistic causal analysis on a factor graph** to compute `P(sensorᵢ = malicious | observed error)` — pinpointing exactly **which** sensors are under attack (factor graphs avoid the "zero-frequency" problem of naive Bayesian/residual methods). It keeps an attack-free **historic-state checkpoint** in a sliding window (sized to outlast a stealthy attack, bridging detection delay), **reconstructs** the compromised states from that history + the **uncompromised** sensors (EKF), and feeds these clean states into an existing **checkpoint/LQR-style recovery** — turning worst-case recovery into **targeted** recovery.
- **Results.** 6 RVs (**4 real**: Pixhawk, Tarot, Aion, Sky-viper; 2 sim). **93–94%** mission success under multi-sensor SDAs (SSR/PID-Piper hit **100% crash** at 3+ sensors); diagnosis **96% true-positive / 5% false-positive** (40–50% higher TP and **4× fewer** false positives than residual analysis); **2× less** path deviation and **2.5× less** mission delay than worst-case LQR-O; **4× fewer** gratuitous recoveries; **no crashes**; detects stealthy attacks within one window; ~7.5% CPU, <3% memory overhead.
- **Limitation.** **Requires an existing detector**; assumes an **attack-free start** and **trustworthy historic states**, no obstacles, and that the attacker cannot control the environment (wind); it **cannot beat a permanent, all-sensor SDA** (no clean sensor left to fuse); software/comms attacks are out of scope.

### [Gecko] *Software Availability Protection in Cyber-Physical Systems* (USENIX Security 2025)
- **Motivation (a different problem).** Existing software-security defenses (CFI, memory encryption) protect **confidentiality/integrity** and, on a violation, **crash and restart** — which **sacrifices availability**. For a real-time CPS that is physically moving, an untimely result is catastrophic (e.g., Jackal UGV deviates >0.4 m — a possible collision — if recovery takes >0.2 s; a full reboot takes **87.3 s**, a program restart **3.56 s**, causing 83%+ of tasks to miss deadlines). Simple restart also can't stop **repeated exploitation**, and checkpoint/restore can't restore **peripheral** (sensor/actuator) state without slow re-calibration.
- **Method.** Gecko formalizes **real-time software availability = liveness + timeliness** and recovers it with four pieces: (1) **program compartmentalization** (task-based, on the program-dependence graph, via LLVM/ACES) plus **CFI + value-based DFI** (from OAT) as the attack detector; (2) **attack-surface attribution** — trace the malicious input back to a compartment via runtime dependencies, refining granularity on demand (multi-stage / incremental re-compartmentalization); (3) **fail-safe *shadow compartments*** that **disable the exploited feature** for graceful degradation (default mode returns recorded values for non-essential inputs; **customized mode** replays or **predicts** essential inputs such as sensor data — reusing Simplex-style safe blocks and ArduPilot's 11 fail-safe modes); (4) **selective checkpoint/restore** (CRIU-based, one clean snapshot right after init) plus an **I/O reference monitor** that makes sensor/actuator config registers **read-only after initialization** — avoiding the costly peripheral re-calibration on rollback.
- **Results.** 3 platforms (**ArduPilot** UAV, **Jackal** UGV, **OpenManipulator X** robotic arm). Under attack: **89.7%** (ArduPilot) / **83.3%** (Jackal) task-deadline-hit rate vs **0%** for reboot and **2.56% / 16.7%** for program restart; runtime overhead **8.28% / 7.52%**; recovery time **15.1 ms / 35.4 ms**. Feature reduction defeats repeated exploitation.
- **Limitation.** **Cyber-physical inconsistency in recovery** — rolling back to the initialized state may itself destabilize the vehicle (e.g., altitude reset to 0 mid-flight); **soft real-time** focus; it assumes an **existing reference monitor** (CFI + value-based DFI) detects exploitation; it targets **memory-corruption**, not **physical sensor**, attacks (hardware corruption, side-channels out of scope); shadow compartments need domain expertise / manual effort.

---

## 2. The lineage — how each paper arrives by solving the previous one's limitation

| From (good at A) | The limitation B that hurt | To (adds B) | Exactly what the successor changed |
|---|---|---|---|
| **[SSR]** — first *software-only* recovery; no HW redundancy; patches legacy; handles multi-sensor for a short time | (i) **Feed-Back** controller **over-compensates** a systematic attack bias; (ii) **linear** model ⇒ weak vs **stealthy** attacks; (iii) prediction **drift** ⇒ recovery only temporary (~10 s; all-gyro ~3 s), needs manual takeover | **[PID-Piper]** | Swap FBC → **Feed-Forward Controller** (no over-compensation); swap linear SI → **nonlinear LSTM** + feature engineering (kills collinearity); tight **CUSUM threshold** from a precise ML model ⇒ **limits stealthy 7×**; **completes the mission** autonomously (83% vs 13%, 0% FP) |
| **[PID-Piper]** — recovers overt attacks, limits stealthy, 0% FP, no crashes | Still a **bounded-error, single-controller model**: it does **not know which sensor is attacked**, so under **simultaneous multi-sensor** SDAs its approximation collapses ⇒ **100% crash at 3+ sensors** | **[DeLorean]** | Add **attack diagnosis** (factor-graph causal analysis) to identify **which** sensors are malicious ⇒ **targeted** recovery that keeps using the **clean** sensors; add **historic-state checkpointing** to bridge detection delay ⇒ **93–94% success, no crashes** on multi-sensor SDAs, 2× less deviation than worst-case LQR |
| **[SSR] / [PID-Piper] / [DeLorean]** — all defend the **physical / transduction** channel and *assume cyber attacks are handled elsewhere* | None of them protect the **software** itself: a **memory-corruption** exploit crashes the process, and standard cyber defenses **restart** (0–2.56% deadline hits) ⇒ availability lost | **[Gecko]** | A **different axis**: recover **software availability** (liveness + timeliness) under memory-corruption via **compartmentalization + shadow-compartment feature reduction + selective checkpoint + I/O monitor** — and **reuse** SSR / PID-Piper as sensor-compensation models *inside* its shadow compartments |

**Note on authorship as a lineage signal.** PID-Piper and DeLorean share the same core group (Dash, Li, Pattabiraman, UBC/Iowa) — DeLorean is that group's direct sequel to PID-Piper. SSR is the earlier Purdue work both cite as the recovery baseline (SSR's Xiangyu Zhang; the "LQR-O" checkpoint baseline is a separate Zhang-et-al. line). Gecko is an independent WUSTL group entering from the software-security side.

---

## 3. Systematic "good at A, lacks B"

| Paper (full title) | **Good at (A)** | **Lacks / weak at (B)** | Answered by |
|---|---|---|---|
| **[SSR]** *Software-based Realtime Recovery from Sensor Attacks on Robotic Vehicles* | Software-only recovery of **multiple** physical sensors; no extra hardware; deployable on legacy RVs | Over-compensation (feed-back); **drift** ⇒ short-lived; **linear** ⇒ stealthy-weak; all-gyro only ~3 s | PID-Piper, DeLorean |
| **[PID-Piper]** *PID-Piper: Recovering Robotic Vehicles from Physical Attacks* | **Overt** recovery to full mission success (83%); **limits stealthy 7×**; **0% FP**, 0 crashes; low overhead | **Single-sensor / bounded-error** — **crashes under simultaneous multi-sensor SDAs**; can't tell *which* sensor is hit | DeLorean |
| **[DeLorean]** *Diagnosis-guided Attack Recovery … from Sensor Deception Attacks* | **Diagnoses which** sensors are attacked ⇒ **targeted, multi-sensor** recovery (93–94%), no worst-case maneuvers, no crashes | Needs an **existing detector** + **trustworthy historic states** + attack-free start; **can't survive a permanent all-sensor SDA** | (research frontier) |
| **[Gecko]** *Software Availability Protection in Cyber-Physical Systems* | **Software availability** (liveness+timeliness) under **memory-corruption / repeated exploits**; restores **peripheral** state; 83–90% deadline hits | **Not** physical-sensor attacks; **cyber-physical inconsistency** on rollback; **soft** real-time only; needs a reference monitor | (research frontier) |

---

## 4. Head-to-head differences that matter

| Dimension | **SSR** | **PID-Piper** | **DeLorean** | **Gecko** |
|---|---|---|---|---|
| **Attack channel** | Physical / transduction | Physical / transduction | Physical / transduction | **Cyber / memory-corruption** |
| **What is recovered** | The compromised **sensor reading** | The **controller/actuator output** | The compromised **state estimate** (only the bad sensors) | The **program's execution** (availability) |
| **Core mechanism** | System-ID **state-space software sensor** | **LSTM Feed-Forward Controller** + CUSUM | **Factor-graph diagnosis** + historic-state reconstruction | **Compartmentalization + shadow compartments + checkpoint + I/O monitor** |
| **Knows *which* sensor is attacked?** | No (replaces any that diverges) | No (per-controller) | **Yes (diagnosis is the contribution)** | N/A (traces the malicious **input**, not sensor) |
| **Multi-sensor SDA** | Partial (short duration) | **Crashes (3+ sensors)** | **Yes — the whole point (93–94%)** | N/A |
| **Stealthy attacks** | Weak (linear model) | Limited **7×** | Detected within one window | N/A (different threat) |
| **Needs external detector?** | Builds its own switch | Builds its own CUSUM detector | **Yes, explicitly required** | **Yes — a CFI+DFI reference monitor** |
| **Formal guarantee** | No | No | No | **Real-time availability = liveness + timeliness** (formulated) |
| **Platforms** | quad/hexa/rover (2 real) | 6 RVs (3 real) | 6 RVs (4 real) | UAV + UGV + **robotic arm** (real HW-in-loop) |
| **Venue / year** | RAID 2020 | DSN 2021 | ASIA CCS 2024 | USENIX Security 2025 |

---

## 5. Connections & common threads (what unites all four)

1. **All four are *recovery*, not detection.** The field's premise: detection-only aborts the mission; safety-critical CPS must **keep operating** under attack. Each paper's novelty is a recovery mechanism.
2. **All four assume/contain a detector.** SSR's recovery switch, PID-Piper's CUSUM, DeLorean's *required external* detector, Gecko's *required* CFI/DFI reference monitor. "Assume a detector exists" is the load-bearing assumption across the whole set.
3. **The "trustworthy history / checkpoint" idea recurs** in three different costumes: SSR's periodic **synchronization/reset**, DeLorean's **historic-state checkpoint window**, Gecko's **post-init snapshot**. Each must reason about *detection delay* to keep the checkpoint attack-free.
4. **Prediction/estimation of a clean signal is the shared engine:** SSR predicts the sensor reading, PID-Piper predicts the controller output, DeLorean reconstructs the state, and Gecko's customized shadow compartment **predicts missing sensor data** — for which its Table 3 literally names **SSR, PID-Piper (and UnRocker, Kalman filters)** as the plug-in models. **This is the concrete link that ties Gecko back to the other three.**
5. **Two complementary threat surfaces.** SSR/PID-Piper/DeLorean harden the **outside-in** path (spoofed physics reaching good software); Gecko hardens the **inside-out** path (good physics, exploited software). A fully protected RV arguably needs **both** — DeLorean-style physical recovery *and* Gecko-style software-availability recovery — which is exactly why Gecko can sit on top of the other three rather than replace them.

---

## 6. Takeaways

- **Read SSR → PID-Piper → DeLorean as one story** of increasing sophistication in physical-sensor recovery: *replace the sensor* → *replace the control output without over-compensating* → *first figure out which sensor to replace*. Each step is a direct answer to a named limitation of the last.
- **Read Gecko as a separate, complementary layer** — the software-availability problem the other three deliberately exclude — that *consumes* their sensor-compensation models.
- **The open frontier all four leave:** none gives a formal *safety* guarantee for the physical trajectory during recovery (DeLorean/PID-Piper are empirical; Gecko formalizes availability but flags cyber-physical inconsistency), and **none survives a permanent, all-sensor compromise**.

*Companion files in this folder: `Paper_Genealogy_and_Synthesis.md` (all 28 papers) and `../Paper_Analysis.md` (14-question breakdown per paper).*
