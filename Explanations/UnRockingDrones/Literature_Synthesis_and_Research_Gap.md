# Physical/Sensor Attacks & Defenses in Cyber-Physical Systems — Systematic Synthesis and Research Gap

This document synthesizes **14 papers** in the space of physical-channel and sensor attacks/defenses for cyber-physical systems (drones, autonomous vehicles, mobile robots, IoT). It is organized to surface *what each paper is good at, what it lacks, how the papers connect*, and — the end goal — **to identify a concrete problem that none of them has solved.**

---

## 1. The 14 Papers at a Glance

| # | Short name | Venue / Year | Type | One-line role |
|---|-----------|--------------|------|---------------|
| P1 | **UnRocker** (Un-Rocking Drones) | NDSS 2023 | Defense — recovery | DAE denoising recovers benign IMU values under acoustic injection |
| P2 | **Injected & Delivered** (Tu et al.) | USENIX Sec 2018 | Attack | Acoustic spoofing → *implicit directional control* of MEMS IMUs |
| P3 | **PyCRA** (Shoukry et al.) | CCS 2015 | Defense — auth/detect | Physical challenge-response authentication for *active* sensors |
| P4 | **Adv-LiDAR** (Cao et al.) | CCS 2019 | Attack | Optimization-based LiDAR point spoofing → phantom obstacle |
| P5 | **MSF-ADV / "Invisible"** (Cao et al.) | IEEE S&P 2021 | Attack | Single 3D object defeats *camera+LiDAR fusion* simultaneously |
| P6 | **SSR / Software-Sensors** (Choi et al.) | RAID 2020 | Defense — recovery | State-space "software sensors" replace attacked physical sensors |
| P7 | **SpecGuard** (Dash et al.) | CCS 2024 | Defense — recovery | Deep-RL recovery that stays compliant with STL mission specs |
| P8 | **RIDS / Physical Dynamics** (Guo et al.) | arXiv 2017 | Defense — detection | Nonlinear unknown-input estimation detects sensor *and* actuator attacks |
| P9 | **STL Anomaly Detection** (Jones et al.) | IEEE CDC 2014 | Defense — detection | Inference-STL learns human-readable anomaly classifier |
| P10 | **Formal Techniques** (Deshmukh & Sankaranarayanan) | Springer 2019 | Survey/methods | Tutorial on verification/falsification/STL for CPS |
| P11 | **Verifiable IFC** (Liu et al.) | CPS-SPC 2018 | Defense — cross-layer | Algorithm+language(Jif)+hardware(SecVerilog) information-flow control |
| P12 | **CPS Security Survey** (Yaacoub et al.) | Micro. & Microsys. 2020 | Survey | Broad cyber/physical/hybrid CPS threat taxonomy + forensics |
| P13 | **IoTSeer** (Ozmen et al.) | CCS 2022 | Defense — vuln discovery | Physics-model + falsification finds IoT physical-channel interactions |
| P14 | **Gecko** (Li et al.) | USENIX Sec 2025 | Defense — availability | Compartments + shadow tasks keep CPS software live under memory attacks |

**Balance of the corpus:** 3 pure attacks (P2, P4, P5), 8 defenses (P1, P3, P6, P7, P8, P11, P13, P14), 2 surveys/method references (P10, P12), with P9 straddling detection/method. Within defenses the split is: **recovery** (P1, P6, P7), **detection** (P8, P9), **authentication/prevention** (P3), **vulnerability discovery** (P13), **cross-layer/software integrity & availability** (P11, P14).

---

## 2. Systematic "Good at A / Lacks B" Comparison

### 2.1 The Attacks — establishing what defenses must survive

- **P2 Injected & Delivered — good at:** a rigorous closed-form aliasing + sample-rate-drift model (Theorem: drift is amplified −n× into the aliased band) that explains *why* naive DC-bias attacks fail; broadest real evaluation (25 devices, 17 fully controllable). **Lacks:** works mostly against static/slow targets at short range (≤~8 m), needs manual re-tuning every 1–2 min, no tracking rig for fast drones/vehicles; purely an attack — proposes no validated defense.
- **P4 Adv-LiDAR — good at:** first to show physical LiDAR spoofing survives the *ML detection pipeline* (Apollo) into a dangerous decision (emergency-brake); grounds perturbation in measured spoofer capability (~60 points, 8° cone) rather than unconstrained digital noise; solves a real local-minima optimization pathology. **Lacks:** white-box assumption; no real road test (points synthesized into recorded clouds); ~75–90% success needing 60 injected points; authors admit simple filters (ground-point removal, fusion) likely defeat it — *and never evaluate those defenses*.
- **P5 MSF-ADV / "Invisible" — good at:** directly falsifies the field's core "fusion = redundancy = safety" assumption; a *single* 3D-printed shape perturbs camera **and** LiDAR at once (no active transmitter, no aiming/timing); >91% success, differentiable rendering + soft point-inclusion, physical + simulator validation. **Lacks:** only *rule-based (late)* fusion tested — *DNN/deep fusion left open*; best defense (median smoothing) still leaves 66% success; no RADAR; small printable objects only; white-box.

> **Takeaway for defenders:** the attack papers jointly prove that (a) physical attacks reliably penetrate the *ML/estimation stack*, not just raw signals, and (b) **multi-sensor / fusion attacks are real** — the redundancy that most detection/recovery defenses lean on can be removed by the attacker. This directly motivates recovery approaches that do **not** assume any clean sensor remains.

### 2.2 The Recovery Defenses — the cluster closest to UnRocker

| Property | **P6 SSR** | **P7 SpecGuard** | **P1 UnRocker** |
|---|---|---|---|
| Core mechanism | State-space model → "software sensors" | Deep-RL policy + STL compliance reward | Denoising autoencoder (signal denoising) |
| **Good at** | Software-only; survives majority-same-type compromise; real HW | **Mission-spec compliance during recovery** (geofence/altitude); 0 collisions; beats 5 prior tools | Recovers even when **all IMUs compromised**; no backup sensor needed; roots cause in **sampling jitter** |
| Multi-sensor? | Same-type majority ✔ (but drifts) | **Single-sensor only** (SVR ~doubles under GPS+gyro) | ✔ all-IMU (its whole point) |
| Mission-spec aware? | ✘ (crash-avoidance only) | **✔ (STL specs)** | ✘ (signal fidelity only) |
| Real-time within deadline? | ✔ (~seconds recovery window) | ✔ (reactive, 3× faster) | **Partial** — accel yes, **gyro fails** (30 ms comms latency > 24 ms budget) |
| Attack channels | GPS/gyro/baro (model-based) | GPS/optical-flow/gyro/accel/mag (single) | **Only acoustic/resonance** injection |
| Formal guarantee? | ✘ | ✘ (learned policy) | ✘ (black-box NN) |
| Detection/diagnosis? | Built-in residual monitor | **Assumes external** (PID-Piper/DeLorean) | None — runs denoiser unconditionally |
| **Key lack** | Temporary (drift → must land); no spec-awareness; stealthy sub-threshold evades | Single-sensor; depends on upstream detector; DeepRL has no safety proof; manual STL authoring | One attack type; not real-time for gyro; no spec-awareness; no guarantee |

**Connections:** These three form a clear **lineage of "recovery, not just detection."** SSR (2020) introduced software-only recovery but only to avoid crashing. UnRocker (2023) pushed *signal-level* recovery that survives total sensor compromise, but only for acoustic noise and without mission awareness. SpecGuard (2024) added the missing *mission-specification* layer — but retreated to a single-sensor threat model and outsourced detection/diagnosis. **No single recovery paper holds all three desirable properties at once: (i) multi-sensor/total-compromise resilience, (ii) mission-spec compliance, (iii) real-time guarantees.** Each trades one for another.

### 2.3 Detection & Prevention Defenses

- **P8 RIDS — good at:** jointly detects **and localizes/quantifies** *both actuator and sensor* attacks on nonlinear robots via unknown-input estimation (generalized attack-aware EKF), no majority voting, works with ≥1 clean sensor; low FP/FN (~0.9%), <0.4 s delay. **Lacks:** **detection only — explicitly defers recovery**; cannot handle *all* workflows compromised; can't separate fault vs. attack; small sub-threshold or target-switching attacks evade; only a slow Khepera ground robot (~0.05 m/s).
- **P9 STL Anomaly Detection — good at:** first formal-methods anomaly detection; **human-interpretable** classifier; unsupervised, model-free. **Lacks:** two tiny simulated case studies; assumes attacks are *rare* and *qualitatively divergent* — **breaks precisely for stealthy in-envelope attacks**; combinatorial formula search, no guarantees; "rudimentary" online monitoring.
- **P3 PyCRA — good at:** physics-grounded *security proof* (bounded actuator reaction delay + noise floor); single-sensor security without redundancy or crypto secrets; covers eavesdropping + simple + adaptive spoofing. **Lacks:** **only *active* sensors** (emit-and-reflect) — *does not cover the passive IMU/GPS/camera sensors* that dominate drones/AVs and that P2/P1 attack; periodic silencing degrades sampling rate/availability; benchtop only.

**Connection:** P8/P9 give *situational awareness* but no response; P3 gives *prevention* but only for a sensor class orthogonal to the IMU/vision attacks the rest of the corpus cares about. STL (P9) is the same formalism SpecGuard (P7) and IoTSeer (P13) later use for *specifications* — showing STL as a recurring backbone across detection, recovery, and vuln-discovery.

### 2.4 Formal Methods, Cross-Layer, and Systems Defenses

- **P10 Formal Techniques (survey) — good at:** clean map of verification vs. falsification, STL robustness semantics, reachability, barrier certificates. **Explicitly flags open:** *security/relational hyperproperties are "nascent"*, reachability undecidable for nonlinear systems, **DNN/AI-component verification is the unsolved frontier** — i.e., formal safety certification of *learned* components (exactly what UnRocker's DAE and SpecGuard's Deep-RL are) is not yet a solved problem.
- **P11 Verifiable IFC — good at:** rare genuine **cross-layer** co-design (Jif software types + SecVerilog hardware) with formal guarantees and overhead numbers. **Lacks:** hardware never integrated with the robot (layers tested separately); only a single map-poisoning demo on a Segway; sensors mostly *out of scope* ("all sensors compromised" declared undefendable); binary trust labels; admits efficient compromised-sensor compensation "is still open research."
- **P14 Gecko — good at:** formalizes real-time software **availability** (liveness + timeliness) under *active, repeated* memory-exploitation; shadow compartments give graceful degradation; CPS-specific insight (peripherals configured once → lock read-only) beats reboot/checkpoint; 3 real robots, ~8% overhead, 83–90% deadline hit vs. 0% for reboot. **Lacks:** **explicitly excludes sensor spoofing and physical attacks** (memory-safety only); rollback-to-init can be unsafe for stateful controllers; shadow logic for essential inputs is manual.
- **P13 IoTSeer — good at:** physics-grounded hybrid-automaton models + STL + **falsification** to discover *physical-channel interactions* among IoT apps (found 16 violations; prior tools found 2); models aggregation, dependency, and actuator-sensor *distance*. **Lacks:** 6 physical channels only; single-house study; manual label/parameter confirmation; no automatic non-disruptive mitigation.
- **P12 CPS Survey — good at:** broadest cyber+physical+hybrid taxonomy + forensics + real-incident table + OFSS lifecycle. **Lacks:** descriptive only; physical/sensor attacks treated shallowly; pools heterogeneous domains → generic.

---

## 3. How the Papers Connect (the "map")

```
                         ATTACKS (define the threat)
   P2 acoustic IMU control ── P4 LiDAR spoofing ── P5 camera+LiDAR fusion broken
        │                          │                        │
        │  "all/again sensors      │  "ML pipeline is        │  "redundancy is
        │   can be compromised"    │   the real target"      │   not safety"
        ▼                          ▼                        ▼
   ────────────────────────  DEFENSE RESPONSES  ────────────────────────
   PREVENT/AUTH      DETECT                RECOVER                 SYSTEM/INTEGRITY
   P3 PyCRA          P8 RIDS (sensor+act)  P6 SSR (software sensors) P11 IFC (cross-layer)
   (active sensors)  P9 STL anomaly        P1 UnRocker (denoise)     P14 Gecko (availability)
                            │              P7 SpecGuard (spec-aware) P13 IoTSeer (vuln find)
                            │                     │
                            └─── STL formalism ───┴──── shared with P13, and surveyed by P10
```

**Three recurring through-lines:**
1. **The redundancy assumption is collapsing.** Attacks trend from single-sensor (P2, P4) → *multi-sensor/fusion* (P5), and UnRocker's threat model assumes *all* IMUs hit at once. Yet most detection/recovery defenses (P6 majority-type, P7 single-sensor, P8 "≥1 clean sensor," P3 needs the sensor online) still bank on *something* being uncompromised.
2. **STL / formal specs are the connective tissue** across P9 (detection), P7 (recovery), P13 (vuln discovery), P10 (methods) — but they are applied to *specifications and monitors*, never to *certify the learned recovery components themselves.*
3. **Recovery is migrating to learned/black-box models** (P1 DAE, P7 Deep-RL) exactly as P10 names *verification of AI components* as the open frontier — so the field is building safety-critical recovery on components it cannot yet formally guarantee.

---

## 4. The Research Gap — an Unsolved Problem

Putting the "lacks" columns together, every defense solves a *slice*, and the slices do not overlap into a whole:

| Desirable property of an ideal physical-attack recovery system | P1 | P6 | P7 | P8 | P3 | P11 | P14 |
|---|:--:|:--:|:--:|:--:|:--:|:--:|:--:|
| Survives **simultaneous multi-sensor / total** physical compromise (no clean sensor needed) | ✔(IMU) | ~ | ✘ | ✘ | ✘ | ✘ | n/a |
| **Mission-specification compliant** (geofence, altitude, timing) during recovery | ✘ | ✘ | ✔ | ✘ | ✘ | ~ | ~ |
| **Real-time** within control-loop deadline (all sensor types) | ~ | ✔ | ✔ | ✔ | ~ | ~ | ✔ |
| Handles **heterogeneous attack channels** (acoustic + spoofing + optical + …), not one | ✘ | ~ | ~ | ~ | ✘ | ✘ | ✘ |
| **Integrated** detect → diagnose → recover (no assumed perfect upstream detector) | ✘ | ✔ | ✘ | detect-only | ✔ | ~ | ✔ |
| **Formal safety guarantee** on the recovery action (incl. learned components) | ✘ | ✘ | ✘ | ~ | ✔ | ✔ | ✔(avail) |

No row of a single system is all-✔.

### 4.1 Statement of the gap

> **There is no recovery framework that keeps a robotic autonomous vehicle both *safe* and *mission-compliant*, in real time, under a *simultaneous multi-channel physical attack that compromises all/most sensors at once*, while providing a *formal (or at least certified) guarantee* on the recovered control — and without assuming a separate, uncompromised detector or a clean redundant sensor.**

Concretely, the corpus leaves these specific sub-gaps open:

1. **Multi-sensor + mission-aware at the same time.** UnRocker (P1) recovers under total *IMU* compromise but is signal-fidelity-only and single-channel (acoustic); SpecGuard (P7) is mission-aware but explicitly *single-sensor* and its own data show violations nearly double under a mere GPS+gyro combination. **Nobody recovers from a genuine multi-sensor physical attack while staying provably within mission specifications.**

2. **Cross-channel generality.** Each recovery method is welded to a physical model of *one* attack channel (P1 acoustic resonance/jitter; P6 model-based GPS/baro/gyro; P7 within DeLorean's reconstructable single sensor). The attack papers (P2, P4, P5) show attackers freely mix channels (acoustic, laser, optical, shape). **A recovery method robust across heterogeneous, possibly concurrent channels does not exist.**

3. **Real-time recovery for the *hard* sensors.** UnRocker itself concedes gyroscope recovery misses the 24 ms deadline because of communication latency; SpecGuard needs a fast reactive controller. **Real-time, on-board, deadline-guaranteed recovery for latency-critical sensors under attack is unsolved** (UnRocker's own "future work: edge-AI on-board inference").

4. **Certifying *learned* recovery.** Recovery is increasingly a neural network (P1 DAE) or Deep-RL policy (P7), yet the formal-methods work (P9, P10, P11) that could certify safety **explicitly does not yet handle AI components** (P10 names it *the* open frontier; P11 uses only a simple Gaussian test and calls efficient compromised-sensor compensation "open research"). **Bridging learned recovery with formal safety certification is an open problem sitting right at the intersection of these papers.**

5. **Integrated pipeline vs. assumed detector.** P7 SpecGuard outsources detection (PID-Piper) and diagnosis (DeLorean) and never evaluates their accuracy; P8 RIDS detects but never recovers; P1 skips detection entirely. **An end-to-end detect→diagnose→recover loop whose recovery degrades gracefully when detection/diagnosis is itself imperfect** is not demonstrated — and the multi-sensor case makes diagnosis (which sensor(s) to trust) dramatically harder.

### 4.2 A candidate problem to pursue (the sharpest, most defensible target)

**"Specification-compliant, real-time recovery of robotic autonomous vehicles under *simultaneous multi-sensor* physical attacks, with a runtime *safety certificate* over the (learned) recovery controller."**

This is attractive because it sits exactly in the white space between the three strongest recent papers and inherits a concrete, credible motivation from each:
- from **UnRocker (P1)** — the demonstrated reality of *total-sensor* compromise and signal-level recovery, plus its *own admitted* real-time gyro gap;
- from **SpecGuard (P7)** — the demonstrated *need for mission-spec compliance*, plus its *own admitted* single-sensor limitation and reliance on external detection;
- from **the formal-methods line (P9/P10/P11)** — STL specifications + robustness semantics as the vehicle for a *runtime safety certificate*, addressing P10's explicitly-named open frontier of certifying AI/learned components.

A system attacking this problem would need to combine: (a) a multi-sensor state reconstructor that assumes *no* clean sensor (generalizing UnRocker's "treat corruption as noise" and RIDS's unknown-input estimation to the all-compromised, multi-channel case), (b) an STL/robustness-based runtime monitor that *certifies* each recovered control action against mission specs before actuation (turning SpecGuard's reward into a hard runtime guarantee), and (c) an on-board, deadline-bounded implementation (closing UnRocker's latency gap). No paper in this set does all three; each supplies exactly one ingredient and names the others as future work — which is the strongest possible evidence that the combined problem is both important and genuinely open.

---

## 5. One-Paragraph Summary

The corpus tells a coherent story: **attackers have escalated from single-sensor spoofing (P2, P4) to defeating multi-sensor fusion with a single physical object (P5), erasing the redundancy that defenses depend on.** Defenders answered along four tracks — prevention for active sensors only (P3), detection without response (P8, P9), software/availability integrity that excludes sensor spoofing (P11, P14), and *recovery* (P6→P1→P7), which is the live frontier. But the recovery track is fragmented: **SSR recovers but only to avoid crashing; UnRocker recovers under total sensor loss but only for acoustic noise and not in real time for gyroscopes; SpecGuard recovers into mission-spec compliance but only for a single sensor and only by trusting an external detector.** Meanwhile the formal-methods track (P9, P10, P11) that could *certify* these increasingly-learned recovery controllers explicitly has not yet solved verification of AI components. **The unsolved problem is therefore a unified one: real-time, mission-specification-compliant, formally-certified recovery from simultaneous multi-sensor, multi-channel physical attacks — with no clean sensor and no assumed-perfect detector to lean on.**
