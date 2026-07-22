# Research Papers — Genealogy, Lineage & Systematic Synthesis

**What this document is.** A *connection map* over the 28 unique papers in `Research Papers/`.
Where `Paper_Analysis.md` answers 14 questions *per paper*, this file answers the questions *across* papers:

1. **Tree / lineage** — which paper grows out of which, traced through the "novelty vs. prior work" (Q11) each paper states.
2. **The limitation hand‑off** — the specific weakness of paper *X* that paper *Y* was built to fix ("X is good at A but lacks B → Y adds B").
3. **Main insights** — motivation → method → result, condensed.
4. **Systematic "good‑at / lacks‑at" synthesis** and cross‑cutting connections.

> Built on the detailed extraction in `../Paper_Analysis.md` (numbering is kept identical: paper **#N** here = **#N** there).
> The field this collection covers: **physical / transduction sensor attacks on autonomous vehicles → detection → recovery**, plus a perception‑attack wing and two surveys.

---

## Master index — short name → full paper title

Every short tag used in the tree and tables below resolves here. **File** = the PDF in `../Research Papers/`.

| # | Short name | **Full paper title** | Venue / Year | File (`Research Papers/…`) |
|---|---|---|---|---|
| 1 | Adv‑LiDAR | **Adversarial Sensor Attack on LiDAR‑based Perception in Autonomous Driving** | ACM CCS 2019 | `Adversarial Sensor Attack on LiDAR-based Perception inAutonomous Driving.pdf` |
| 2 | STL anomaly | **Anomaly Detection in Cyber‑Physical Systems: A Formal Methods Approach** | IEEE CDC 2014 | `Anomaly_detection_in_cyber-physical_systems_A_formal_methods_approach.pdf` |
| 3 | DeLorean | **Diagnosis‑guided Attack Recovery for Securing Robotic Vehicles from Sensor Deception Attacks** | ACM ASIA CCS 2024 | `Diagnosis-guided Attack Recovery for Securing Robotic Vehicles from Sensor Deception Attacks.pdf` |
| 4 | ESP | **ESP Spoofing: Covert Acoustic Attack on MEMS Gyroscopes in Vehicles** | IEEE TIFS 2022 | `ESP_Spoofing_Covert_Acoustic_Attack_on_MEMS_Gyroscopes_in_Vehicles.pdf` |
| 5 | RIDS | **Exploiting Physical Dynamics to Detect Actuator and Sensor Attacks in Mobile Robots** | 2017 | `Exploiting Physical Dynamics to Detect Actuator and Sensor Attacks in Mobile Robots.pdf` |
| 6 | GAN‑MP | **GAN‑Based Robust Motion Planning for Mobile Robots Against Localization Attacks** | IEEE RA‑L 2023 | `GAN-Based_Robust_Motion_Planning_for_Mobile_Robots_Against_Localization_Attacks.pdf` |
| 7 | Hybrid Monitors | **Hybrid Knowledge‑ and Data‑Driven Synthesis of Runtime Monitors for Cyber‑Physical Systems** | IEEE TDSC 2024 | `Hybrid_Knowledge_and_Data_Driven_Synthesis_of_Runtime_Monitors_for_Cyber-Physical_Systems.pdf` |
| 8 | Injected & Delivered | **Injected and Delivered: Fabricating Implicit Control over Actuation Systems by Spoofing Inertial Sensors** | USENIX Security 2018 | `Injected and Delivered- Fabricating Implicit Control over Actuation Systems by Spoofing Inertial Sensors.pdf` |
| 9 | MSF‑ADV | **Invisible for Both Camera and LiDAR: Security of Multi‑Sensor Fusion‑based Perception in Autonomous Driving Under Physical‑World Attacks** | IEEE S&P 2021 | `Invisible_for_both_Camera_and_LiDAR_..._Physical-World_Attacks.pdf` |
| 10 | SeqRec | **SeqRec — Learn‑to‑Respond: Sequence‑Predictive Recovery from Sensor Attacks in Cyber‑Physical Systems** | IEEE RTSS 2023 | `SeqRec Learn-to-Respond_Sequence-Predictive_Recovery_from_Sensor_Attacks_in_Cyber-Physical_Systems.pdf` |
| 11 | PID‑Piper | **PID‑Piper: Recovering Robotic Vehicles from Physical Attacks** | IEEE/IFIP DSN 2021 | `PID-Piper_Recovering_Robotic_Vehicles_from_Physical_Attacks.pdf` |
| 12 | PyCRA | **PyCRA: Physical Challenge‑Response Authentication for Active Sensors Under Spoofing Attacks** | ACM CCS 2015 | `PyCRA- Physical Challenge-Response Authentication For Active Sensors Under Spoofing Attacks.pdf` |
| 13 | LQR‑recovery | **Real‑time Attack‑recovery for Cyber‑physical Systems Using Linear‑quadratic Regulator** | EMSOFT / ACM TECS 2021 | `Real-time Attack-recovery for Cyber-physical Systems Using Linear-quadratic Regulator.pdf` |
| 14 | Nyctea | **Real‑time Rectifying Flight Control Misconfiguration Using Intelligent Agent** | ACM TOSEM 2025 | `Real-time Rectifying Flight Control Misconfiguration Using Intelligent Agent.pdf` |
| 15 | Data‑Predictive | **Real‑Time Data‑Predictive Attack‑Recovery for Complex Cyber‑Physical Systems** | IEEE RTAS 2023 | `Real-Time_Data-Predictive_Attack-Recovery_for_Complex_Cyber-Physical_Systems.pdf` |
| 16 | Recovery Survey | **Recovery from Adversarial Attacks in Cyber‑physical Systems: Shallow, Deep, and Exploratory Works** | ACM CSUR 2024 | `Recovery from Adversarial Attacks in Cyber-physical Systems- Shallow, Deep, and Exploratory Works.pdf` |
| 17 | Robotics Survey | **Robotics Cyber Security: Vulnerabilities, Attacks, Countermeasures, and Recommendations** | Int. J. Information Security 2022 | `Robotics cyber security Vulnerabilities, attacks, countermeasures, and recommendations.pdf` |
| 18 | DAE‑Crit‑LSTM | **Robust Motion Planning for Multi‑Robot Systems Against Position Deception Attacks** | IEEE TIFS 2024 | `Robust_Motion_Planning_for_Multi-Robot_Systems_Against_Position_Deception_Attacks.pdf` |
| 19 | Robustness Testing | **Robustness Testing of Data‑ and Knowledge‑Driven Anomaly Detection in Cyber‑Physical Systems** | IEEE/IFIP DSN‑W 2022 | `Robustness_Testing_of_Data_and_Knowledge_Driven_Anomaly_Detection_in_Cyber-Physical_Systems.pdf` |
| 20 | SCVMON | **SCVMON: Data‑oriented Attack Recovery for RVs Based on Safety‑Critical Variable Monitoring** | RAID 2023 | `SCVMON- Data-oriented attack recovery for RVs based on safety-critical variable monitoring.pdf` |
| 21 | Secure CPS (IFC) | **Secure Autonomous Cyber‑Physical Systems** (through Verifiable Information‑Flow Control) | ACM CPS‑SPC 2018 | `Secure Autonomous Cyber Physical Systems.pdf` |
| 22 | UAV Classification | **Sensor Attack Online Classification for UAVs Using Machine Learning** | Computers & Security 2025 | `Sensor attack online classification for UAVs using machine learning.pdf` |
| 23 | Gecko | **Software Availability Protection in Cyber‑Physical Systems** (Gecko) | USENIX Security 2025 | `Software Availability Protection in Cyber Physical Systems.pdf` |
| 24 | SSR | **Software‑based Realtime Recovery from Sensor Attacks on Robotic Vehicles** | RAID 2020 | `Software-based Realtime Recovery from Sensor Attacks on Robotic Vehicles.pdf` |
| 25 | SpecGuard | **SpecGuard: Specification‑Aware Recovery for Robotic Autonomous Vehicles from Physical Attacks** | ACM CCS 2024 | `SpecGuard Specification Aware REcovery for Robotic Autonomous Vehivles from Physical Attacks.pdf` |
| 26 | Stealthy vs CI/EKF | **Stealthy Attacks against Robotic Vehicles Protected by Control‑based Intrusion Detection Techniques** | ACM DTRAP 2021 | `Stealthy Attacks against Robotic Vehicles Protected by Control-based Intrusion Detection Techniques.pdf` |
| 27 | TileMask | **TileMask: A Passive‑Reflection‑based Attack against mmWave Radar Object Detection in Autonomous Driving** | ACM CCS 2023 | `TileMask  A Passive Reflection based Attack against mmWave Radar Object Detection in Autonomous Driving.pdf` |
| 28 | UnRocker | **Un‑Rocking Drones: Foundations of Acoustic Injection Attacks and Recovery Thereof** | NDSS 2023 | `UnRockingDrones.pdf` |

---

## 0. The external roots (cited by many, not in this folder)

Almost every paper here traces back to a small set of seed works that *created* the physical‑sensor‑attack field. They are the "trunk below the soil":

| Seed work (cited) | What it established | Papers here that grow from it |
|---|---|---|
| **Son et al. 2015** — acoustic gyro DoS | You can disable/DoS a MEMS gyro with sound | #4 ESP, #28 UnRocker, #24 SSR |
| **Trippel et al. 2017** — *WALNUT* accelerometer control | You can *control* (not just DoS) an accelerometer acoustically | #4 ESP, #28 UnRocker |
| **Petit / Shin et al.** — raw LiDAR spoofing | Injecting laser points into LiDAR | #1 Adv‑LiDAR (shows raw injection is *insufficient* vs. an ML detector) |
| **Choi et al. 2018** — *Control Invariants (CI)* | Physics/control‑model **detection** of RV attacks | #26 (evades it), #11 PID‑Piper, #24 SSR, #22 (baseline) |
| **Quinonez et al. — *SAVIOR*** | State‑estimation RV attack detection | #11, #22 (baselines) |
| **Kong et al.** — supervised reactive‑STL | Temporal‑logic learning from *labeled* traces | #2 STL anomaly (drops the labels) |

**Everything in the folder is a reaction to these three facts:** (a) sensors can be spoofed through physics, (b) detectors exist but are imperfect, and (c) detection alone doesn't keep the vehicle flying — hence *recovery*.

---

## 1. The genealogy tree (at a glance)

```
                    EXTERNAL ROOTS (Son'15 · Trippel'17 · Petit/Shin · Choi CI'18 · SAVIOR · Kong STL)
                                              │
        ┌──────────────────────┬─────────────┴───────────────┬────────────────────────────┐
        │                      │                             │                            │
   [A] OFFENSE            [B] DETECTION                 [C] RECOVERY (main trunk)     [D] PREVENTION
        │                      │                             │                            │
 ┌──────┴───────┐      ┌───────┼────────┐          ┌─────────┴─────────┐          ┌───────┴────────┐
 │ transduction │      │physics │formal │          │  SSR (24, 2020)   │          │ Secure CPS (21)│
 │  #4 ESP      │      │#5 RIDS │#2 STL │          │  ▼ seminal baseline│         │  Jif IFC + HW  │
 │  #8 Inj&Del  │      │#12 PyCRA│       │          │  (software sensors)│         │                │
 │  #28 UnRocker│      │        │#7 Hybrid Monitors │          │                  │ Gecko (23,2025)│
 │  (attack)    │      │#22 UAV │  └▲ #19 robustness │  ┌───────┼─────────┬──────┐│  availability  │
 │              │      │  clsf. │      test           │  │       │         │      ││  (mem‑corrupt) │
 │ perception:  │      │        │                     │  ▼       ▼         ▼      ▼│                │
 │  #1 Adv‑LiDAR│      │        │                #11 PID‑Piper #28 UnRocker #6 GAN‑MP #20 SCVMON     │
 │   ▼          │      │        │                (FFC recovery)(DAE, no extra (detector‑ (sensor+    │
 │  #9 MSF‑ADV  │      │        │                   │           sensors)   free)      param)        │
 │   ▼          │      │        │              ┌────┴─────┐               │                          │
 │  #27 TileMask│      │        │              ▼          ▼               ▼                          │
 │              │      │        │          #3 DeLorean #25 SpecGuard  #18 DAE‑Crit‑LSTM              │
 │  #26 Stealthy├──────┘        │          (diagnose  (spec‑aware,    (multi‑robot)                  │
 │  vs CI/EKF   │  (evasion      │           WHICH)    STL+DeepRL)                                    │
 │  motivates → │   loops back                                                                        │
 │   recovery)  │   to detection)│      control‑theoretic recovery branch:                            │
 └──────────────┘               │          #13 LQR‑recovery (2021, from RTSS'20)                      │
                                │              ▼                                                      │
                                │          #15 Data‑Predictive (nonlinear, Flow*)                     │
                                │          #10 SeqRec (model‑free, seq2seq)                           │
                                │                                                                      │
                                │      off‑theme: #14 Nyctea (misconfiguration, not attack)            │
                                │                                                                      │
                       SURVEYS that sit above the whole tree:  #16 Recovery Survey · #17 Robotics Sec Survey
```

**Read the arrows as "fixes the limitation of."** The trunk of the whole collection is **SSR (#24, RAID 2020)** — nearly every recovery paper defines itself as "SSR, but without limitation *Z*."

---

## 2. The limitation hand‑off — the heart of the tree

Each row: **the parent is good at A, lacks B; the child adds B.** This is the causal chain the user asked for.

### 2.1 The recovery trunk (SSR and its children)

| Parent (good at A) | Lacks B | Child that fixes it | How the child adds B |
|---|---|---|---|
| **External detectors** — **CI** = Choi et al., *"Detecting Attacks Against Robotic Vehicles: A Control Invariant Approach,"* ACM CCS 2018; **SAVIOR** = Quiñonez et al., *"SAVIOR: Securing Autonomous Vehicles with Robust Physical Invariants,"* USENIX Security 2020 — good at *raising an alarm* <br>*(source: named as detect‑only baselines in the related‑work of #24 SSR / #11 PID‑Piper / #3 DeLorean; both are cited works, not PDFs in this folder — see §0 external roots)* | No **recovery**; just fail‑safe / abort mission | **#24 SSR (2020)** — *Software‑based Realtime Recovery from Sensor Attacks on Robotic Vehicles* | "Software sensors" (system‑ID state‑space model) *predict* the clean reading and **replace** the compromised sensor — first software‑only recovery, no HW redundancy |
| **#24 SSR** — *Software‑based Realtime Recovery from Sensor Attacks on Robotic Vehicles* — good at multi‑sensor software recovery | Uses a **feed‑BACK** controller that **over‑compensates** for a systematic attack bias; drift limits it to a few seconds | **#11 PID‑Piper (2021)** — *PID‑Piper: Recovering Robotic Vehicles from Physical Attacks* | Replaces feedback with a **feed‑FORWARD LSTM** that predicts the *controller output*; tight ML threshold → also *limits stealthy* attacks (83% vs 13% mission success) |
| **#24 SSR** — *Software‑based Realtime Recovery from Sensor Attacks on Robotic Vehicles* **/ #11 PID‑Piper** — *PID‑Piper: Recovering Robotic Vehicles from Physical Attacks* — good at bounded‑error recovery, but **assume all sensors attacked / tolerate only bounded error** | Do **worst‑case** maneuvers; can't tell *which* sensor is compromised | **#3 DeLorean (2024)** — *Diagnosis‑guided Attack Recovery for Securing Robotic Vehicles from Sensor Deception Attacks* | **Factor‑graph causal diagnosis** attributes the anomaly to specific sensors → *targeted* recovery on only the bad ones (93% recovery, 2× less path deviation) |
| **#24 SSR** — *Software‑based Realtime Recovery from Sensor Attacks on Robotic Vehicles* **+ FTC / #13 LQR‑recovery** — *Real‑time Attack‑recovery for CPS Using Linear‑quadratic Regulator* **/ #3 DeLorean** — *Diagnosis‑guided Attack Recovery …* **/ #28 UnRocker** — *Un‑Rocking Drones …* — good at *not crashing* | **Narrow objective**: restore a set‑point / prevent crash; **ignore the mission specification** (geofence, altitude, obstacle clearance) | **#25 SpecGuard (2024)** — *SpecGuard: Specification‑Aware Recovery for Robotic Autonomous Vehicles from Physical Attacks* | Encodes mission specs in **STL**, shapes a **Deep‑RL (PPO)** reward from them → recovers *while complying* with the mission (5× lower spec‑violation) |
| **#24 SSR‑style recovery** — *Software‑based Realtime Recovery from Sensor Attacks on Robotic Vehicles* — good at single/short recovery | **Only lasts a few seconds** and **needs supplementary sensors**; fails if *all* IMU sensors compromised | **#28 UnRocker (2023)** — *Un‑Rocking Drones: Foundations of Acoustic Injection Attacks and Recovery Thereof* | **Denoising autoencoder** reconstructs benign IMU from the resonant signal — **full, persistent recovery with no extra sensors**; also pinpoints *sampling jitter* as the crash cause |
| **#24 SSR** — *Software‑based Realtime Recovery from Sensor Attacks on Robotic Vehicles* **/ LSTM‑backup** — good at recovery **but require a detector first** (detectors are slow/imperfect) | Whole pipeline stalls on detector error | **#6 GAN‑MP (2023)** — *GAN‑Based Robust Motion Planning for Mobile Robots Against Localization Attacks* | **Detector‑free**: a GAN Generator silently corrects attacked positions, feeding a Deep‑RL planner — no threshold, no detector |
| **#6 GAN‑MP** — *GAN‑Based Robust Motion Planning for Mobile Robots Against Localization Attacks* **/ #24 SSR / #11 PID‑Piper** — good at **single‑robot** recovery | Degrade badly under **simultaneous multi‑robot** position attacks | **#18 DAE‑Crit‑LSTM (2024)** — *Robust Motion Planning for Multi‑Robot Systems Against Position Deception Attacks* | Stacked **DAE** restores true obstacle positions for a *multi‑robot* team (77.5%→98.1%); stronger‑venue follow‑up to GAN‑MP |
| **#24 SSR** — *Software‑based Realtime Recovery from Sensor Attacks on Robotic Vehicles* **/ #11 PID‑Piper / LA defenses** — good at **sensor** attacks | Cannot handle **parameter/config** (data‑oriented) attacks; redundancy defenses need extra HW | **#20 SCVMON (2023)** — *SCVMON: Data‑oriented Attack Recovery for RVs Based on Safety‑Critical Variable Monitoring* | **Static analysis + s‑t‑cut** finds minimal Safety‑Critical Variables to monitor → recovers from **sensor *and* parameter** attacks, software‑only |

### 2.2 The control‑theoretic recovery branch

| Parent | Lacks | Child | Fix |
|---|---|---|---|
| **Discard‑and‑reuse‑controller recovery** — simple | Ignores the **safety deadline**; no guarantee *during* recovery | **#13 LQR‑recovery (2021)** — *Real‑time Attack‑recovery for Cyber‑physical Systems Using Linear‑quadratic Regulator* | **LQR + timing/safety constraints + ADMM**, with **reachability** proving no unsafe state is reachable before the deadline |
| **#13 LQR‑recovery** — *Real‑time Attack‑recovery for Cyber‑physical Systems Using Linear‑quadratic Regulator* — good, provable | Assumes **linear** dynamics; **discards all sensors** → uncertainty explodes on nonlinear systems | **#15 Data‑Predictive (2023)** — *Real‑Time Data‑Predictive Attack‑Recovery for Complex Cyber‑Physical Systems* | **Nonlinear reachability (Flow\*)** + **keeps the uncompromised sensors** to curb uncertainty → first nonlinear‑CPS recovery with guarantees |
| **#13 LQR‑recovery** — *Real‑time Attack‑recovery for Cyber‑physical Systems Using Linear‑quadratic Regulator* — model‑based | Hard to apply to **black‑box / complex** systems (no clean dynamics model) | **#10 SeqRec (2023)** — *SeqRec — Learn‑to‑Respond: Sequence‑Predictive Recovery from Sensor Attacks in CPS* | **Model‑free** seq2seq (encoder‑decoder RNN) learns estimation + a dedicated recovery controller end‑to‑end |

### 2.3 The offense branch (each attack defeats the previous defense assumption)

| Parent assumption / work | What it lacked / assumed safe | Child attack | What it broke |
|---|---|---|---|
| Raw LiDAR injection (Petit/Shin) | Assumed injecting points is enough — **fails vs. an ML detector** | **#1 Adv‑LiDAR (2019)** — *Adversarial Sensor Attack on LiDAR‑based Perception in Autonomous Driving* | **Optimization‑based** adversarial spoofing that survives Apollo's DNN (30%→75% success) |
| Single‑modality attacks (**#1 Adv‑LiDAR** — *Adversarial Sensor Attack on LiDAR‑based Perception in Autonomous Driving*, etc.); "**MSF is safe because you can't attack all sensors at once**" | The core MSF security assumption | **#9 MSF‑ADV (2021)** — *Invisible for Both Camera and LiDAR: Security of Multi‑Sensor Fusion‑based Perception in Autonomous Driving Under Physical‑World Attacks* | One 3D object that fools **camera *and* LiDAR simultaneously** (>91%) — refutes the assumption |
| **Active** radar spoofing — needs sub‑ns sync, costly, **can't fool the DNN** | Practicality + DNN reach | **#27 TileMask (2023)** — *TileMask: A Passive‑Reflection‑based Attack against mmWave Radar Object Detection in Autonomous Driving* | **Passive‑reflection** ($10, 2 tiles) that changes the **DNN** radar output |
| Son/Trippel on **toy RC cars** | Realism / stealth on a **complex** system | **#4 ESP (2022)** — *ESP Spoofing: Covert Acoustic Attack on MEMS Gyroscopes in Vehicles* | Acoustic gyro attack on a full **ESP** car with **audio‑overlay stealth** (music‑hidden ultrasound) |
| Gyro DoS; **#24 SSR** — *Software‑based Realtime Recovery from Sensor Attacks on Robotic Vehicles* recovery "solves" acoustic | SSR recovery is short and needs extra sensors | **#28 UnRocker attack (2023)** — *Un‑Rocking Drones: Foundations of Acoustic Injection Attacks and Recovery Thereof* | Shows **sampling jitter** neutralizes the low‑pass filter → motivates its own DAE recovery |
| Attacks that assume **no protection** | RVs now run **CI + EKF** detectors | **#26 Stealthy vs CI/EKF (2021)** — *Stealthy Attacks against Robotic Vehicles Protected by Control‑based Intrusion Detection Techniques* | **Reverse‑engineers** the detector, stays **just under threshold** — *this paper is the antagonist that motivates the tighter‑threshold recovery line (#11, #3)* |

### 2.4 The detection & monitoring branch

| Parent | Lacks | Child | Fix |
|---|---|---|---|
| Host/network IDS; majority‑voting WSN IDS | Miss **physical‑channel** attacks; fail if >½ sensors bad | **#5 RIDS (2017)** — *Exploiting Physical Dynamics to Detect Actuator and Sensor Attacks in Mobile Robots* | **Nonlinear unknown‑input estimator + χ²** detects *both* sensor & actuator attacks, no voting |
| Post‑digitization crypto; system‑level detectors | Can't stop **analog‑domain** spoofing before digitization | **#12 PyCRA (2015)** — *PyCRA: Physical Challenge‑Response Authentication for Active Sensors Under Spoofing Attacks* | **Physical challenge‑response** using sensor *passivity* — provable physical‑delay bound (later shown bypassable → open problem) |
| Supervised reactive‑STL (Kong) | Needs **labeled** data + causal structure | **#2 STL anomaly (2014)** — *Anomaly Detection in Cyber‑Physical Systems: A Formal Methods Approach* | **Unsupervised** STL formula inference — interpretable, human‑readable classifier |
| Model‑based monitors (false alarms) **and** pure‑ML monitors (opaque, detect *after* the hazard) | Bridge design‑time analysis ↔ runtime | **#7 Hybrid Monitors (2024)** — *Hybrid Knowledge‑ and Data‑Driven Synthesis of Runtime Monitors for Cyber‑Physical Systems* | **STL (from STPA hazard analysis) + ML semantic loss + reaction‑time estimator** → *preemptive* detection |
| ML monitors of **#7 Hybrid Monitors** — *Hybrid Knowledge‑ and Data‑Driven Synthesis of Runtime Monitors for Cyber‑Physical Systems* | Untested under adversarial noise | **#19 Robustness Testing (2022)** — *Robustness Testing of Data‑ and Knowledge‑Driven Anomaly Detection in Cyber‑Physical Systems* | FGSM/Gaussian **robustness test**; shows STL **semantic loss** cuts error up to 54% (workshop companion to #7) |
| One‑detector‑per‑attack (SAVIOR, CI, SCVMON) | Heavy; can't say *which* attack | **#22 UAV Classification (2025)** — *Sensor Attack Online Classification for UAVs Using Machine Learning* | Single lightweight **MLP multi‑classifier** detects **and classifies** several attack types (48 KB, 0.5 ms) |

### 2.5 Prevention / availability branch (separate philosophy)

| Parent | Lacks | Child | Fix |
|---|---|---|---|
| Single‑layer CPS security; steep formal tools | End‑to‑end, cross‑layer guarantee | **#21 Secure CPS (2018)** — *Secure Autonomous Cyber‑Physical Systems* | **Jif information‑flow typing + verified HyperFlow processor** → provable HW+SW+control security (prevention, not recovery) |
| Memory‑safety defenses (CFI, encryption) **crash on violation** | Sacrifice **availability** | **#23 Gecko (2025)** — *Software Availability Protection in Cyber‑Physical Systems* | Formalizes real‑time **availability**; **compartmentalization + shadow feature‑reduction + selective rollback** keeps the CPS running under repeated memory‑corruption exploits |

---

## 3. Main insights per cluster (motivation → method → result)

### Cluster C — Recovery (the densest, most connected)
- **Motivation:** detection alone aborts the mission; the vehicle must *keep operating* under an ongoing sensor attack.
- **Two method families:**
  - **Learning / data‑driven:** software sensors (#24), LSTM feed‑forward (#11), DAE (#28, #18), GAN (#6), seq2seq (#10), Deep‑RL (#25). *Strength:* handle nonlinear/black‑box systems, no dynamics model. *Weakness:* **no formal guarantee**, need training data.
  - **Control‑theoretic:** LQR+reachability (#13), nonlinear reachability/Flow\* (#15). *Strength:* **provable safety before a deadline**. *Weakness:* need a system model, simulation‑bound, linear assumptions (until #15).
- **Shared pattern:** almost all **assume an upstream detector** (#6, #10, #13, #15, #25) and add **prediction/state reconstruction + a dedicated recovery controller**. The frontier moved: *bounded‑error* → *worst‑case* → *targeted/diagnosed* (#3) → *specification‑aware* (#25).
- **Results trend:** mission‑success under attack climbed 13% (pre‑recovery) → 83% (#11) → 92–93% (#3, #25).

### Cluster A — Offense
- **Motivation:** show that each new defensive assumption (ML robustness, sensor fusion, detector presence) is false.
- **Method:** optimization‑based adversarial generation (#1, #9, #27), acoustic/physics injection (#4, #8, #28), detector reverse‑engineering (#26).
- **Result:** every "safe because…" assumption fell — MSF (#9), radar‑needs‑sync (#27), detector‑protected RVs (#26). These papers are the *pressure* that keeps the recovery trunk growing.

### Cluster B — Detection & monitoring
- **Motivation:** catch physical/analog attacks that cyber IDS miss, *before* harm.
- **Method:** physics‑model + χ² (#5, #12), temporal logic (#2, #7), ML classifiers (#22), robustness‑hardened ML (#19).
- **Result:** low false‑positive physical detection; the STL line (#2→#7→#19) uniquely offers **interpretability + preemption**.

### Cluster D — Prevention / availability
- **Motivation:** stop the attack by construction (#21) or survive code‑level exploits without crashing (#23).
- **Method:** language‑based IFC + verified hardware (#21); compartmentalization + rollback (#23).
- **Result:** the only **provable end‑to‑end** (#21) and the only **availability‑as‑liveness+timeliness** formulation (#23) in the set.

### Surveys (meta‑layer)
- **#16 Recovery Survey (2024):** organizes the whole Cluster C into **shallow** (no dedicated recovery controller — redundancy, estimation, roll‑forward) vs **deep** (dedicated controller — Simplex, LQR, learned policy). Use it as the map legend.
- **#17 Robotics Security Survey (2022):** broad vulnerability/attack/countermeasure taxonomy; context, not depth.

---

## 4. Systematic "good‑at / lacks‑at" table

| # | Paper (short — **full title**) | **Good at (strength)** | **Lacks (limitation)** | Superseded/answered by |
|---|---|---|---|---|
| 24 | **SSR** — *Software‑based Realtime Recovery from Sensor Attacks on Robotic Vehicles* | First software‑only sensor recovery | Feedback over‑compensates; drift; seconds‑only; needs a clean sensor | 11, 28, 3, 25 |
| 11 | **PID‑Piper** — *PID‑Piper: Recovering Robotic Vehicles from Physical Attacks* | FFC recovery + limits stealthy attacks; tight ML threshold | Threshold‑dependent; assumes attacker can't bypass FFC | 3, 25 |
| 3 | **DeLorean** — *Diagnosis‑guided Attack Recovery for Securing Robotic Vehicles from Sensor Deception Attacks* | Diagnoses *which* sensor → targeted recovery | Needs an existing detector; can't beat permanent all‑sensor attack | (frontier) |
| 25 | **SpecGuard** — *SpecGuard: Specification‑Aware Recovery for Robotic Autonomous Vehicles from Physical Attacks* | Recovers *and* honors mission specs (STL) | Relies on external detector; sim‑to‑real | (frontier) |
| 28 | **UnRocker** — *Un‑Rocking Drones: Foundations of Acoustic Injection Attacks and Recovery Thereof* | Full, persistent IMU recovery, no extra sensors | MEMS‑IMU acoustic only; DAE needs training | (frontier) |
| 6 | **GAN‑MP** — *GAN‑Based Robust Motion Planning for Mobile Robots Against Localization Attacks* | Detector‑free mitigation | Simulation only; approximate positions | 18 |
| 18 | **DAE‑Crit‑LSTM** — *Robust Motion Planning for Multi‑Robot Systems Against Position Deception Attacks* | Multi‑robot position recovery | Simulation only; multi‑robot‑specific | (frontier) |
| 20 | **SCVMON** — *SCVMON: Data‑oriented Attack Recovery for RVs Based on Safety‑Critical Variable Monitoring* | Recovers sensor **+ parameter** attacks, SW‑only | ArduPilot only; empirical thresholds | (frontier) |
| 13 | **LQR‑recovery** — *Real‑time Attack‑recovery for Cyber‑physical Systems Using Linear‑quadratic Regulator* | Provable safety before deadline (reachability) | Linear; discards all sensors; sim‑only | 15 |
| 15 | **Data‑Predictive** — *Real‑Time Data‑Predictive Attack‑Recovery for Complex Cyber‑Physical Systems* | Nonlinear guarantees + keeps good sensors | Needs detector; rare fail‑safe fallback; sim‑only | (frontier) |
| 10 | **SeqRec** — *SeqRec — Learn‑to‑Respond: Sequence‑Predictive Recovery from Sensor Attacks in CPS* | Model‑free recovery for complex systems | Depends on detector delay; no stability proof | (frontier) |
| 23 | **Gecko** — *Software Availability Protection in Cyber‑Physical Systems* | Availability under memory‑corruption; peripheral state | Assumes reference monitor; soft real‑time; not physical‑sensor | (frontier) |
| 5 | **RIDS** — *Exploiting Physical Dynamics to Detect Actuator and Sensor Attacks in Mobile Robots* | Detect sensor **+ actuator**, no voting; χ² guarantee | Needs ≥1 clean sensor; detection‑only; can't tell fault vs attack | 3, 11 (add recovery) |
| 12 | **PyCRA** — *PyCRA: Physical Challenge‑Response Authentication for Active Sensors Under Spoofing Attacks* | Provable physical‑delay bound; analog authentication | Active sensors only; sensor pauses; later bypassed | (open problem) |
| 2 | **STL anomaly** — *Anomaly Detection in Cyber‑Physical Systems: A Formal Methods Approach* | Interpretable, unsupervised formal detection | Toy/train sim only; assumes separable classes | 7 |
| 7 | **Hybrid Monitors** — *Hybrid Knowledge‑ and Data‑Driven Synthesis of Runtime Monitors for CPS* | STL+ML preemptive, reaction‑time budget | Sim/offline; assumes sensor data protected upstream | 19 (tests it) |
| 19 | **Robustness Testing** — *Robustness Testing of Data‑ and Knowledge‑Driven Anomaly Detection in CPS* | Shows semantic loss hardens ML monitors | Preliminary; APS only; FGSM only | (frontier) |
| 22 | **UAV Classification** — *Sensor Attack Online Classification for UAVs Using Machine Learning* | Multi‑class, lightweight online | 74% online; no recovery; SITL data | (frontier) |
| 21 | **Secure CPS (IFC)** — *Secure Autonomous Cyber‑Physical Systems* | Provable cross‑layer prevention | No physical tampering; verified CPU not yet integrated | 23 (availability angle) |
| 1 | **Adv‑LiDAR** — *Adversarial Sensor Attack on LiDAR‑based Perception in Autonomous Driving* | First optimization LiDAR attack | White‑box; narrow angle; sim impact | 9 |
| 9 | **MSF‑ADV** — *Invisible for Both Camera and LiDAR: Security of Multi‑Sensor Fusion‑based Perception …* | Breaks camera **+** LiDAR at once | White‑box; static object; digital defenses | (frontier attack) |
| 27 | **TileMask** — *TileMask: A Passive‑Reflection‑based Attack against mmWave Radar Object Detection …* | Cheap passive radar‑DNN attack | White‑box; must affix to target; <8 m misses | (frontier attack) |
| 4 | **ESP** — *ESP Spoofing: Covert Acoustic Attack on MEMS Gyroscopes in Vehicles* | Stealthy acoustic gyro on real ESP system | Carsim/Simulink only | (frontier attack) |
| 8 | **Injected & Delivered** — *Injected and Delivered: Fabricating Implicit Control over Actuation Systems by Spoofing Inertial Sensors* | Implicit actuation control via inertial‑sensor spoofing | Attack only; bespoke waveform crafting | (offense seed) |
| 26 | **Stealthy vs CI/EKF** — *Stealthy Attacks against Robotic Vehicles Protected by Control‑based Intrusion Detection Techniques* | Automated detector‑evasion | Per‑platform re‑learning; no firmware tamper | motivates 11, 3 |
| 14 | **Nyctea** — *Real‑time Rectifying Flight Control Misconfiguration Using Intelligent Agent* | Real‑time RL config rectification | *Misconfiguration, not attack*; per‑platform training | off‑theme |
| 16 | **Recovery Survey** — *Recovery from Adversarial Attacks in CPS: Shallow, Deep, and Exploratory Works* | The shallow/deep taxonomy map | Small field; no new technique | — |
| 17 | **Robotics Survey** — *Robotics Cyber Security: Vulnerabilities, Attacks, Countermeasures, and Recommendations* | Broad global taxonomy | Qualitative; shallow on recovery | — |

---

## 5. Cross‑cutting connections (the non‑obvious links)

- **The antagonist that drives the trunk.** #26 (stealthy sub‑threshold evasion) is the reason #11 chases *tight* thresholds and #3 chases *diagnosis*. Offense and recovery are one feedback loop, not two silos.
- **"Assume a detector" is the field's load‑bearing assumption.** #6, #10, #13, #15, #25 all outsource detection. #6 and #18 are the rebellion (detector‑free); #3 is the reconciliation (diagnose to *mask* detector false positives).
- **STL is the connective tissue of the formal wing:** #2 (infer it) → #7 (train with it) → #19 (test it) → #25 (reward with it). Same logic, four different jobs (detect → monitor → harden → recover).
- **Two definitions of "recovery" coexist:** *physical‑state* recovery (Cluster C, keep flying safely) vs *software/availability* recovery (#23, keep the program alive). #16 only surveys the first; #23 opens the second.
- **Guarantees cluster by community, not by problem:** control‑theoretic reachability (#13, #15), physics/statistics (#5, #12), language/HW (#21), logic (#2, #7, #20, #25). **All learning‑based recovery (#3, #6, #10, #18, #24, #28) has *no* formal guarantee** — the clearest open gap.
- **Sensor coverage mirrors lineage:** the RV‑recovery cluster shares one hardware suite (GPS+gyro+accel+mag+baro), which is *why* they can cite and benchmark each other so cleanly. The perception wing (#1/#9/#27, LiDAR/camera/radar) is a near‑separate literature that only meets the trunk through the surveys.

---

## 6. Open gaps the tree points to (where the next paper goes)

1. **Formal guarantees for learning‑based recovery** — the whole DAE/GAN/RL branch (#6, #18, #24, #25, #28) is empirical; none proves safety.
2. **Detector‑free *and* guaranteed** — #6/#18 removed the detector but lost rigor; no one has both.
3. **Permanent all‑sensor attacks** — #3 explicitly can't handle them; #28 handles all‑IMU acoustic but only that modality.
4. **Real‑world / moving‑vehicle validation** — most recovery and several attacks (#4, #6, #13, #15, #18) are simulation‑bound.
5. **Unifying physical‑state recovery (Cluster C) with software‑availability recovery (#23)** — currently disjoint formulations.
6. **Bridging perception attacks (#1/#9/#27) into the recovery trunk** — no recovery paper defends multi‑sensor‑fusion perception; that wing has attacks but no recovery answer here.

---

*Companion file: `../Paper_Analysis.md` (full 14‑question breakdown per paper).
Deeper single‑paper dives live in `UnRockingDrones/`.*
