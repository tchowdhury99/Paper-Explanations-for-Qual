# Research Papers — Detailed Structured Analysis

In‑depth analysis of every paper in `Research Papers/` against the 14 questions in `Questions.md`.
This version expands each answer with concrete architecture/model details, **named** formal methods, explicit
contrasts against the prior works each paper cites (Q11), and full attacker‑capability threat models (Q13).

**Scope:** 28 unique research papers. Excluded: `Event Confirmation_Itinerary - Ascend.pdf` (a travel itinerary,
not a paper) and one byte‑identical duplicate of *Software Availability Protection* (counted once).

**Rating key (Q14, /3):** relevance to the sensor‑attack → detection → recovery theme + rigor + contribution.
**3** = seminal/top‑venue, on‑theme, strong (often real‑hardware) evaluation. **2** = solid but narrower,
simulation‑only, tangential, or preliminary. **1** = loosely related.

Legend: **P**=Prediction, **Th**=Threshold, **D**=Detection, **R**=Recovery, **FG**=Formal Guarantee. Yes yes · No no · Yes partial.

---

## Quick‑reference matrix

| # | Paper (short) | Sensors | UAV/UGV | Role | P | Th | D | R | FG (method) | Rate |
|---|---|---|---|---|:-:|:-:|:-:|:-:|---|:-:|
| 1 | Adv‑LiDAR | LiDAR | AV/UGV | Attack | No | Yes | No | No | — | 1 |
| 2 | Anomaly Detection – STL | generic CPS | neither (train) | Detection | Yes | Yes | Yes | No | Yes STL robustness semantics | 0 |
| 3 | DeLorean | GPS,gyro,accel,mag,baro | UAV+UGV | Detect+Diagnose+Recover | Yes | Yes | Yes | Yes | No | 3 |
| 4 | ESP Spoofing | MEMS gyro | UGV (car) | Attack+defense | Yes | Yes | Yes | Yes | No | 3 |
| 5 | RIDS | GPS,IMU/accel | UGV(+UAV) | Detection | Yes | Yes | Yes | No | Yes Chi‑square hypothesis test | 2 |
| 6 | GAN‑MP | GPS,IMU,cam,LiDAR | UGV | Mitigation | Yes | No | No | Yes | No | 2 |
| 7 | Hybrid Monitors | CPS I/O | ADS+APS | Detect+Mitigate | Yes | Yes | Yes | Yes | Yes STL specification | 3 |
| 9 | MSF‑ADV | camera+LiDAR | AV/UGV | Attack | No | Yes | No | No | — | 1 |
| 10 | SeqRec | CPS sensors | UAV+UGV | Recovery | Yes | No | No | Yes | No | 2 |
| 11 | PID‑Piper | GPS,baro,gyro,accel,mag | UAV+UGV | Detect+Recover | Yes | Yes | Yes | Yes | No | 3 |
| 12 | PyCRA | active sensors | neither | Detect+Resilience | Yes | Yes | Yes | Yes | Yes physical‑delay bound + χ² detector | 3 |
| 13 | Real‑time Recovery LQR | CPS sensors | UAV+UGV(sim) | Recovery | Yes | No | No | Yes | Yes reachability analysis | 2 |
| 14 | Nyctea | flight state | UAV | Detect+Rectify(config) | Yes | Yes | Yes | Yes | No | 2 |
| 15 | Data‑Predictive Recovery | cam,LiDAR,IMU,GPS | UAV+UGV+marine | Recovery | Yes | No | No | Yes | Yes nonlinear reachability (Flow*) | 2 |
| 16 | Recovery Survey | — | all CPS | Survey | Yes | — | Yes | Yes | Yes (reviews formal methods) | 2 |
| 17 | Robotics Security Survey | — | all robots | Survey | Yes | — | Yes | Yes | — | 3 |
| 18 | DAE‑Crit‑LSTM | position/localization | UGV(multi) | Mitigation | Yes | No | No | Yes | No | 2 |
| 19 | Robustness Testing | APS glucose | neither(APS) | Detection robustness | Yes | Yes | Yes | No | No | 2 |
| 20 | SCVMON | gyro,accel,baro,GPS+params | UAV | Detect+Recover | Yes | Yes | Yes | Yes | Yes s‑t‑cut completeness proof | 2 |
| 21 | Secure Autonomous CPS | camera,LiDAR,GPS | UGV(Segway) | Prevention | No | No | Yes | No | Yes language‑based IFC (Jif) + verified HW | 3 |
| 22 | UAV Attack Classification | gyro,accel,mag,baro,GPS | UAV | Detect+Classify | Yes | Yes | Yes | No | No | 2 |
| 23 | Gecko | IMU/peripherals | UAV+UGV+arm | Recovery(availability) | Yes | Yes | Yes | Yes | Yes real‑time availability formulation | 2 |
| 24 | SSR (Software Sensors) | gyro,accel,GPS,baro,mag | UAV+UGV | Detect+Recover | Yes | Yes | Yes | Yes | No | 3 |
| 25 | SpecGuard | GPS,gyro,accel,cam,LiDAR | UAV+UGV | Recovery | Yes | Yes | No | Yes | Yes STL mission specs | 3 |
| 26 | Stealthy vs CI/EKF | baro,gyro,accel,mag,GPS | UAV+UGV | Attack(evasion) | Yes | Yes | No | No | No | 3 |
| 27 | TileMask | mmWave radar | AV/UGV | Attack | No | Yes | No | No | — | 2 |
| 28 | Un‑Rocking Drones | MEMS IMU | UAV | Attack+Recover | Yes | No | Yes | Yes | No | 3 |

---

## Detailed answers

> Q1 Sensors · Q2 UAV/UGV · Q3 Model used (classified by type: **ML‑based / DL‑ or NN‑based / state‑space estimation / control‑theoretic / optimization‑based / formal‑methods / probabilistic‑graphical / statistical / signal‑processing / static‑analysis / hybrid**) · Q4 Attack types · Q5 Prediction · Q6 Threshold on prediction · Q7 Detection · Q8 Recovery · Q9 Formal guarantee (named) · Q10 Claiming strength · Q11 Novelty vs the prior works they cite · Q12 Limitation · Q13 Threat model in detail · Q14 Rating.

**Model‑type at a glance (Q3):**

| # | Paper | Model type (Q3) |
|---|---|---|
| 1 | Adv‑LiDAR | Optimization‑based (adversarial ML); targets a DL/DNN detector |
| 2 | STL Anomaly Detection | Formal‑methods (unsupervised temporal‑logic inference) — **not** neural |
| 3 | DeLorean | Probabilistic‑graphical (factor‑graph causal diagnosis) + control‑theoretic recovery |
| 4 | ESP Spoofing | DL‑based (LSTM) + statistical (CUSUM) defense; signal‑processing attack |
| 5 | RIDS | State‑space estimation (nonlinear unknown‑input estimator) + statistical (χ²) |
| 6 | GAN‑MP | DL‑based (GAN + deep reinforcement learning) |
| 7 | Hybrid Monitors | Hybrid: formal (STL) + ML/DL (weakly‑supervised + NN) |
| 8 | Injected & Delivered | Analytical signal‑processing model — **not** ML |
| 9 | MSF‑ADV | Optimization‑based (adversarial ML, differentiable rendering); targets DNN |
| 10 | SeqRec | DL‑based (sequence‑to‑sequence RNN / encoder‑decoder) |
| 11 | PID‑Piper | ML/DL‑based (LSTM feed‑forward controller) |
| 12 | PyCRA | Physics‑based + system model + statistical (χ²) — **not** ML |
| 13 | Real‑time Recovery LQR | Control‑theoretic (LQR + ADMM) + reachability — **not** ML |
| 14 | Nyctea | DL‑based reinforcement learning (continuous‑control agent) |
| 15 | Data‑Predictive Recovery | Control‑theoretic (nonlinear reachability + MPC) — **not** ML |
| 16 | Recovery Survey | N/A — survey / taxonomy |
| 17 | Robotics Security Survey | N/A — survey / taxonomy |
| 18 | DAE‑Crit‑LSTM | DL‑based (denoising autoencoder + LSTM deep RL) |
| 19 | Robustness Testing | ML/DL‑based (MLP, LSTM) + formal (STL) semantic loss |
| 20 | SCVMON | Static program analysis + graph algorithm (s‑t cut) — **not** ML |
| 21 | Secure Autonomous CPS | Language‑based formal methods (information‑flow typing) — **not** ML |
| 22 | UAV Attack Classification | ML‑based (MLP / decision‑tree / RF / XGBoost classifiers) |
| 23 | Gecko | Systems‑based (compartmentalization + checkpoint) + offline predictive model |
| 24 | SSR (Software Sensors) | State‑space estimation (system identification) — **not** deep learning |
| 25 | SpecGuard | DL‑based (deep reinforcement learning) + formal (STL) specs |
| 26 | Stealthy vs CI/EKF | System‑identification / control‑model reverse engineering — **not** ML |
| 27 | TileMask | Optimization‑based (adversarial) attack; targets a DNN |
| 28 | Un‑Rocking Drones | DL‑based (denoising autoencoder) |

### 1. Adv‑LiDAR — Adversarial Sensor Attack on LiDAR‑based Perception (CCS 2019)
1. **Sensors:** LiDAR (Velodyne VLP‑16, generalizes to HDL‑64E); camera/radar/GPS‑IMU as context.
2. **Target:** Autonomous vehicle (Baidu Apollo) — ground vehicle.
3. **Model (solution):** *Type: optimization‑based (adversarial ML) attack against a DL/DNN detector.* Formulates the attack as a constrained **optimization problem** `min L_adv(x⊕t′;M)`. Two functions are engineered: (a) an **input‑perturbation function** modeling spoofing capability via **global spatial transformations** (rotation/translation/scaling with a homography matrix + bilinear interpolation), and (b) an **adversarial objective** built from Apollo's post‑processing (objectness × positiveness, Gaussian mask at target). Because vanilla Adam optimization gets trapped in local minima, they add **global sampling + optimization** to raise success from 30%→~75%.
4. **Attacks:** LiDAR spoofing (laser injection) crafted to fool the DNN, adding a "front‑near" fake obstacle (~5 m). Two scenarios: emergency‑brake and AV‑freezing.
5. **Prediction:** No 
6. **Threshold:**  exploits Apollo's fixed thresholds (objectness 0.5, positiveness 0.1).
7. **Detection:** No (defenses only sketched).
8. **Recovery:** No.
9. **Formal guarantee:** — none.
10. **Claiming strength:** ~75% success (vs 30% naïve); simulated on real Apollo traces; two safety‑critical driving outcomes.
11. **Novelty vs prior work:** Prior LiDAR spoofing (Petit et al.; Shin et al.) only injected raw points and was shown *insufficient* here to survive the ML detector; prior adversarial‑ML (Carlini‑Wagner, image attacks) never targeted LiDAR object detection or modeled physical spoofing limits. **First** to combine physical spoofing‑capability modeling with an optimization attack on LiDAR ML perception.
12. **Limitation:** White‑box; narrow ~8° spoofing angle; ~60 stable points; impact shown in simulation, not on a moving vehicle.
13. **Threat model (detail):** Goal = inject a front‑near fake obstacle to trigger unsafe driving decisions. Capability = laser+photodiode+delay spoofer mounted roadside or on an attack vehicle in an adjacent lane, aiming via camera tracking; stealthy (invisible IR). Knowledge = **white‑box** access to the ML model + perception pipeline (justified as obtainable by reverse engineering). No physical contact with the victim.
14. **Rating:** **1/3**.

### 2. Anomaly Detection in CPS: A Formal Methods Approach (CDC 2014)
1. **Sensors:** Generic CPS output signals (train velocity); not sensor‑specific.
2. **Target:** Generic CPS (electronically‑controlled pneumatic train brake) — neither.
3. **Model (solution):** *Type: formal‑methods (unsupervised temporal‑logic inference) — not ML/DL in the neural sense.* **Unsupervised inference of a Signal Temporal Logic (STL) formula** — specifically inference‑STL/parametric iPSTL — from unlabeled output traces. Formulae are organized in a **directed acyclic graph** ordered by the **robustness degree**; simulated‑annealing search + continuous optimization finds the best‑fitting formula that separates normal from anomalous trajectories.
4. **Attacks:** Adversary disables one/two/three train brakes (sensor/actuator effect).
5. **Prediction:**  — online monitoring computes robustness degree over time for early warning.
6. **Threshold:** Yes — the learned formula's numeric bounds (e.g., 0.9634).
7. **Detection:** Yes — core.
8. **Recovery:** No (formal‑synthesis mitigation is future work).
9. **Formal guarantee:** rigorous **STL quantitative (robustness) semantics** give interpretable, mathematically‑defined classifiers, but no security proof.
10. **Claiming strength:** First formal‑methods anomaly detector; human‑readable formulae; ~0 misclassification on the train case; early warning via monitor.
11. **Novelty vs prior work:** Extends the authors' own supervised reactive‑STL learning (Kong et al.) to the **unlabeled** case and drops the required causal structure; unlike ML detectors (Gaussian‑process, one‑class SVM) that yield opaque high‑dimensional surfaces, the STL formula is interpretable. Claims to be the **first** application of formal methods to anomaly detection.
12. **Limitation:** Only a toy example + one train simulator; no real robot; assumes attacks happen after learning and that classes are separable; 2014 scope.
13. **Threat model (detail):** Adversary disables a subset of brakes, changing system dynamics; the number of disabled brakes `b` bounds attacker power. Learning happens on attack‑free data first; detection is post‑hoc.
14. **Rating:** **0/3**.

### 3. DeLorean — Diagnosis‑guided Attack Recovery for RVs (ASIA CCS 2024)
1. **Sensors:** GPS, gyroscope, accelerometer, magnetometer, barometer (heterogeneous).
2. **Target:** RVs — drones (UAV) + rovers (UGV); 4 real + 2 simulated.
3. **Model (solution):** *Type: probabilistic‑graphical model (factor‑graph causal analysis) + control‑theoretic recovery — not deep learning.* A **graph‑based probabilistic attack‑diagnosis** technique: monitors error inflation across all EKF‑fused state estimates and runs **causal analysis on factor graphs** to attribute anomalies to specific sensors (argmax P(sₜ|e)). It records attack‑free **historic state** windows (accounting for detection delay), reconstructs states for compromised sensors, fuses in the clean sensors, and feeds these into an existing **LQR checkpoint‑based recovery** controller — turning worst‑case recovery into *targeted* recovery.
4. **Attacks:** Sensor Deception Attacks (SDAs) — single‑ and multi‑sensor simultaneous (GPS spoofing, acoustic gyro, magnetic injection), incl. stealthy.
5. **Prediction:** Yes — historic‑state reconstruction/estimation.
6. **Threshold:** Yes — detector + probabilistic diagnosis thresholds; computes max stealthy‑attack undetected duration.
7. **Detection:** Yes — integrates existing detectors + adds independent diagnosis (masks detector false positives).
8. **Recovery:** Yes — targeted per‑compromised‑sensor recovery.
9. **Formal guarantee:** No (empirical).
10. **Claiming strength:** 93% recovery under multi‑sensor SDA; 96% diagnosis true‑positive; 2× less path deviation, 2.5× less mission delay vs worst‑case LQR; 4× fewer gratuitous recoveries; no crashes.
11. **Novelty vs prior work:** Model‑based recovery (SSR, PID‑Piper) tolerates only bounded error; checkpoint‑based recovery (Kong et al.; Zhang et al. LQR) **assumes all sensors are attacked** and does worst‑case maneuvers. DeLorean is the **first attack‑diagnosis‑guided** recovery that identifies *which* sensors are compromised and recovers only those, and the first to bridge detection‑delay with recovery for stealthy attacks.
12. **Limitation:** Requires an existing detector; assumes no obstacles, attack‑free start + trustworthy historic states; cannot beat a permanent all‑sensor SDA.
13. **Threat model (detail):** Goal = drive the RV to unsafe states / disrupt the mission. Capability = inject malicious signals to any subset up to all sensors (fake GPS emitter + acoustic + magnetic injectors co‑located); can exploit knowledge of the detection strategy to run stealthy sub‑threshold attacks. Assumptions = existing detector deployed, attack‑free starting position, uncorrupted historic state log.
14. **Rating:** **3/3**.

### 4. ESP Spoofing — Covert Acoustic Attack on MEMS Gyroscopes (IEEE TIFS 2022)
1. **Sensors:** MEMS gyroscope (ESP yaw‑rate); wheel‑speed/accelerometer context.
2. **Target:** Passenger car (Electronic Stability Program) — UGV‑class.
3. **Model (solution):** *Type: DL‑based (LSTM) detector + statistical (CUSUM) for the defense; signal‑processing (acoustic resonance) for the attack.* Attack = acoustic **resonant‑frequency** excitation of the MEMS gyro, with two stealth injection methods (audio overlay) hiding ultrasound in ordinary sound; a **fuzzy‑PID** closed‑loop vehicle model in Carsim+Simulink evaluates impact. Defense = **LSTM + CUSUM** anomaly detector predicting yaw rate and substituting the predicted value on alarm.
4. **Attacks:** Covert acoustic gyro spoofing → understeer/oversteer, drift, rollover.
5. **Prediction:** Yes (defense) — LSTM point‑by‑point yaw‑rate prediction.
6. **Threshold:** Yes — CUSUM threshold `T` (average cumulative deviation over a window).
7. **Detection:** Yes — LSTM‑CUSUM.
8. **Recovery:**  feeds predicted value instead of the attacked reading.
9. **Formal guarantee:** No.
10. **Claiming strength:** Stealthy attack embedded in music; demonstrates rollover/drift; provides both attack and an evaluated active defense.
11. **Novelty vs prior work:** Builds on Son et al. (gyro DoS) and Trippel et al. (WALNUT accelerometer control) but targets a **complex, human‑in‑the‑loop ESP system** rather than toy RC cars, and adds **audio‑overlay stealth** so the high‑frequency component is concealed in normal sound.
12. **Limitation:** Carsim/Simulink simulation only (no real vehicle); the safe‑speed threshold could not be precisely measured (left to future work).
13. **Threat model (detail):** Goal = paralyze ESP → loss of control. Capability = follow the target with high‑power ultrasonic equipment (external model) or co‑located device; **knows the control algorithm** but cannot directly access/modify the controller. Two attack models (external following vs proximate).
14. **Rating:** **3/3**.

### 5. RIDS — Exploiting Physical Dynamics to Detect Actuator & Sensor Attacks (2017)
1. **Sensors:** GPS, IMU/accelerometer, IPS (Khepera's 3 sensors); UAV IMU+GPS generalization.
2. **Target:** Mobile robots — Khepera ground robot (UGV); UAV extension shown.
3. **Model (solution):** *Type: state‑space estimation (nonlinear unknown‑input estimator) + statistical (χ²) — not ML/DL.* A **Nonlinear Unknown‑Input State Estimator (NUISE)** grounded in the robot's **kinematic physical‑dynamics model**. It predicts state evolution from planned control commands, compares predictions with measured readings, and cross‑validates sensors to **detect, pinpoint, and quantify** both sensor and actuator attack vectors — no majority voting or Byzantine threshold needed.
4. **Attacks:** Actuator + sensor attacks via signal interference, sensor spoofing, logic bomb, physical damage.
5. **Prediction:** Yes — model‑based state prediction as ground truth.
6. **Threshold:** Yes — **Chi‑square (χ²) test** thresholds at confidence levels (α=0.05 / 0.005).
7. **Detection:** Yes — plus attack‑vector identification/forensics.
8. **Recovery:** No.
9. **Formal guarantee:** Yes **statistical guarantee via χ² hypothesis testing** on estimation residuals; formal system modeling but no security proof.
10. **Claiming strength:** FP and FN both <1% over 20 experiments; avg detection delay <0.40 s; works with ≥1 clean sensor.
11. **Novelty vs prior work:** Host/network IDS (system calls, packets) miss physical‑channel attacks; wireless‑sensor‑network IDS rely on majority voting / Byzantine thresholds that fail when >½ sensors are compromised. RIDS is the **first** to detect *both* sensor and actuator attacks in nonlinear stochastic mobile robots **without** voting/Byzantine thresholds, and additionally pinpoints and quantifies the attack.
12. **Limitation:** Needs ≥1 uncorrupted sensor; cannot distinguish fault from attack; single Khepera platform; detection‑only (no recovery).
13. **Threat model (detail):** Goal = actively alter robot behavior via a subset of sensors/actuators. Capability = corrupt one or more (but not all) sensing/actuation workflows through various channels; a powerful attacker corrupting all sensors is out of scope. Defender = planner runs NUISE, trusts no single workflow.
14. **Rating:** **2/3**.

### 6. GAN‑MP — GAN‑based Robust Motion Planning vs Localization Attacks (RA‑L 2023)
1. **Sensors:** GPS, IMU, camera, LiDAR (localization module).
2. **Target:** Mobile robot (holonomic, simulation) — UGV‑class.
3. **Model (solution):** *Type: DL‑based / neural network (GAN + deep reinforcement learning).* A **Generative Adversarial Network** whose Generator corrects/reconstructs attacked positions (preserving benign ones), chained to a **Deep RL** planner (instantiated as GAN‑DRL with an LSTM encoder for a variable obstacle count). Detector‑agnostic and method‑agnostic (drops into LSTM‑RL, SARL, ORCA).
4. **Attacks:** Localization Attacks (LAs) — GPS spoofing, IMU injection, LiDAR replay (modeled as p̃ = α·p).
5. **Prediction:** Yes — Generator predicts potential positions.
6. **Threshold:** No — deliberately detector‑agnostic (no threshold).
7. **Detection:** No (by design, no detector).
8. **Recovery:** Yes — mitigation via position correction + robust planning.
9. **Formal guarantee:** No ("guarantee safe motion" is empirical).
10. **Claiming strength:** First GAN‑based LA mitigation needing **no** detector; maintains high success / low collision under 4 LAs; generalizes across MP methods.
11. **Novelty vs prior work:** Prior LA defenses (SSR software sensors; the LSTM‑backup‑controller approach) **require a detector first**, and detectors are imperfect and slow. GAN‑MP is the **first** to mitigate LAs with **no attack detector** and is method‑agnostic.
12. **Limitation:** Simulation only (no real robot); positions are approximate not exact; assumes the attacker cannot access robot control software.
13. **Threat model (detail):** Goal = feed a faked position p̃=α·p so the planner emits a wrong (colliding) action. Capability = perturb localization arbitrarily (any α). Assumption = attacker **cannot** access the robot's control software (so cannot bypass the GAN).
14. **Rating:** **2/3** (RA‑L letter, simulation‑only).

### 7. Hybrid Knowledge & Data‑Driven Synthesis of Runtime Monitors (IEEE TDSC 2024)
1. **Sensors:** Generic CPS sensor/actuator I/O (ADS sensors; APS blood‑glucose + insulin‑on‑board).
2. **Target:** Autonomous Driving System (ground vehicle, OpenPilot) + two Artificial Pancreas Systems.
3. **Model (solution):** *Type: hybrid — formal‑methods (STL) combined with ML/DL (weakly‑supervised learning + neural networks MLP/LSTM).* A **hybrid formal + data pipeline**: derive **Safety Context Specifications in STL** from **control‑theoretic hazard analysis (STPA)**, then refine unknown thresholds via **(i) weakly‑supervised learning** and **(ii) an NN (MLP/LSTM) trained with a custom semantic loss** that penalizes violating the STL spec. Adds a **reaction‑time estimator** (stacked LSTM) predicting the time budget before a hazard.
4. **Attacks:** Accidental faults + malicious controller attacks; stealthy surge/bias attacks on control commands.
5. **Prediction:** Yes — preemptive hazard prediction + reaction‑time budget.
6. **Threshold:** Yes — STL thresholds β learned from data.
7. **Detection:** Yes — context‑aware monitor flags unsafe control actions before hazards.
8. **Recovery:** yes — issues context‑specific corrective commands; reaction time scopes mitigation.
9. **Formal guarantee:** Yes — **STL formal specification** (via STPA) integrated into training, but performance is empirical.
10. **Claiming strength:** Up to **4.7× F1** over baselines (guidelines, MPC, plain ML) with low FP/FN; validated on a 168‑patient clinical dataset; improves transparency; protects vs stealthy surge/bias attacks.
11. **Novelty vs prior work:** Model‑based monitors need full dynamics models (inaccurate/false alarms); pure ML monitors lack labeled data, transparency, corner‑case performance, and mostly detect hazards *after* they occur. This work **closes the gap between design‑time hazard analysis and runtime monitoring**, uniquely combining STL knowledge with data optimization + a reaction‑time estimator for **preemptive** detection.
12. **Limitation:** Simulation + offline clinical data (not deployed real‑time); assumes sensor data is protected upstream; monitor must be made tamper‑proof.
13. **Threat model (detail):** Faults/attacks target the **controller itself** or manifest on its output (bypassing sensor‑side defenses); for malicious cases, attacker can e.g. remotely alter control commands. Sensor data reaching the monitor is assumed protected; monitor placed at the latest stage near actuators.
14. **Rating:** **3/3**.



### 9. MSF‑ADV — Invisible for both Camera and LiDAR (IEEE S&P 2021)
1. **Sensors:** Camera + LiDAR (multi‑sensor fusion).
2. **Target:** Autonomous driving (Baidu Apollo, Autoware) — ground vehicle.
3. **Model (solution):** *Type: optimization‑based (adversarial ML) with differentiable rendering; targets DNN perception.* **Optimization‑based adversarial 3D‑object generation.** Starting from a benign mesh (e.g., traffic cone), it perturbs vertex positions; **differentiable rendering** synthesizes consistent camera images + LiDAR point clouds, and a **differentiable "soft point‑inclusion" approximation** handles the otherwise non‑differentiable cell‑level LiDAR features. Loss = adversarial (evade fusion) + realizability/stealthiness; EoT for robustness; Laplacian/QECD for printability.
4. **Attacks:** A single physically‑realizable 3D‑printed object that evades **both** camera and LiDAR simultaneously (hiding attack).
5. **Prediction:** No (attack).
6. **Threshold:**  stealthiness threshold ε on perturbation.
7. **Detection:** No (defeats it; evaluates DNN defenses).
8. **Recovery:** No.
9. **Formal guarantee:** — none (it *disproves* MSF's assumed security guarantee).
10. **Claiming strength:** >91% success across objects/algorithms; 100% simulated collision; 99.1% frames evade real LiDAR; stealthy, robust (>95%), transferable (~75%).
11. **Novelty vs prior work:** All prior AV‑perception attacks (sticker/poster on signs; laser on LiDAR) hit a **single** modality, and MSF was widely believed secure *because* not all sources can be attacked at once. This is the **first** study of MSF security and the **first** to attack **all fusion sources simultaneously**, directly refuting the core MSF design assumption.
12. **Limitation:** White‑box full knowledge; DNN defenses evaluated in digital space; doesn't cover DNN‑level fusion; static object.
13. **Threat model (detail):** Goal = make the victim AV fail to detect a front obstacle → crash. Capability = fabricate (3D‑print) and place an adversarial object on the roadway, optionally concealed as a worn traffic cone. Knowledge = **white‑box** full knowledge of the MSF perception (as in most adversarial work); no runtime access to the vehicle.
14. **Rating:** **1/3**.

### 10. SeqRec — Learn‑to‑Respond: Sequence‑Predictive Recovery (IEEE RTSS 2023)
1. **Sensors:** Generic CPS sensors (GPS, accelerometer; car speed, drone altitude).
2. **Target:** Autonomous cars + drones; SITL/ArduPilot (UAV) + real 4‑wheel testbed (UGV).
3. **Model (solution):** *Type: DL‑based / neural network (sequence‑to‑sequence RNN encoder‑decoder); model‑free (no system dynamics).* Two **sequence‑to‑sequence (encoder‑decoder RNN) models**: (1) a **state‑estimation Seq2Seq** capturing temporal/spatial dependencies among sensors + control demands to reconstruct states from trustworthy history; (2) a **recovery‑control Seq2Seq** that, MPC‑style, uses remaining intact sensors + a target state to emit a *sequence* of control demands. Model‑free (no system dynamics).
4. **Attacks:** Sensor spoofing/transduction — GPS spoofing, acoustic on accelerometer.
5. **Prediction:** Yes — core (sequence‑predictive estimation + control).
6. **Threshold:** No — assumes external detector; recovery ends at target state.
7. **Detection:** No — assumes an existing detector (with delay).
8. **Recovery:** Yes — core.
9. **Formal guarantee:** No (learning‑based).
10. **Claiming strength:** Model‑free, real‑time, handles nonlinear/complex systems where model‑based methods are too costly; validated on a real testbed.
11. **Novelty vs prior work:** Discard‑and‑estimate methods reuse the original controller (not timely); the LQR/LP real‑time recovery line (Zhang et al.) is **model‑based** and hard to apply to black‑box/complex systems; robust control handles only bounded errors, not unbounded sensor attacks. SeqRec is the **first sequence‑predictive, model‑free** recovery, with a dedicated recovery controller learned end‑to‑end.
12. **Limitation:** Depends on the external detector + its delay; no formal guarantee; needs training data; no post‑recovery stability guarantee.
13. **Threat model (detail):** Goal = drive the system from target to unsafe states via compromised sensors. Capability = spoof/transduce sensors non‑invasively. Assumption = a detector already runs (with some delay); recovery capability is bounded by detector quality; corrupted sensors are isolated during recovery.
14. **Rating:** **2/3**.

### 11. PID‑Piper — Recovering RVs from Physical Attacks (DSN 2021)
1. **Sensors:** GPS, barometer, gyroscope, accelerometer, magnetometer.
2. **Target:** RVs — drones (UAV) + rover (UGV); 6 RVs, 3 real.
3. **Model (solution):** *Type: ML/DL‑based / neural network (LSTM feed‑forward controller with feature engineering).* A **machine‑learning Feed‑Forward Controller (FFC)** — an **LSTM** trained with **feature engineering** (to break PID input collinearity) that predicts the PID controller's *output* under attack‑free conditions. It runs in tandem with the PID; when the PID‑vs‑FFC deviation exceeds threshold τ it flags an attack and the FFC takes over until the attack subsides.
4. **Attacks:** Physical attacks — GPS spoofing, acoustic gyro/accelerometer; overt + stealthy.
5. **Prediction:** Yes — FFC predicts controller output.
6. **Threshold:** Yes — deviation threshold τ (tight, from a precise ML model → limits stealthy attacks).
7. **Detection:** Yes — via PID‑FFC divergence.
8. **Recovery:** Yes — FFC drives the RV.
9. **Formal guarantee:** No.
10. **Claiming strength:** 83% mission success under overt attacks (vs 13% prior), 0% FP; limits stealthy‑attack impact **7×** with 100% mission success (prior 0%); <7% overhead; no crashes/stalls.
11. **Novelty vs prior work:** Detectors (Control Invariants, SAVIOR) only raise alarms / trigger fail‑safe (mission aborted); the only prior recovery (SSR) uses a **Feed‑Back** controller that over‑compensates for systematic attack bias. PID‑Piper is the **first** to use an **FFC** (directly rectifying the actuator output) to *automatically recover* from overt attacks and *limit* stealthy ones, with tight thresholds from a precise ML model.
12. **Limitation:** Threshold‑dependent; assumes the attacker can't access/bypass the FFC; stealthy attacks limited, not eliminated.
13. **Threat model (detail):** Goal = deviate/crash the RV via sensor bias `x=x+f`. Capability = overt (large bias) or stealthy (sub‑threshold, requires knowing τ) sensor manipulation via physical channels. Assumptions = attacker cannot compromise the FFC/controller internals or delete state.
14. **Rating:** **3/3**.

### 12. PyCRA — Physical Challenge‑Response Authentication for Active Sensors (CCS 2015)
1. **Sensors:** Active sensors — magnetic encoders (wheel/gear speed), RFID readers; generalizes to ultrasound/radar/laser.
2. **Target:** Embedded active‑sensor systems (automotive/robotics/healthcare) — neither UAV nor UGV.
3. **Model (solution):** *Type: physics‑based + system‑model + statistical (χ² detector) — not ML.* **Physical challenge‑response authentication**: the sensor's actuator emits a *random but deliberate* physical probe and briefly turns off; because of **physical passivity**, an attacker's injected signal cannot instantly vanish. A **χ² detector** built on a real‑time **system model** predicts the expected response and flags residuals during the "actuator‑off" window; attack‑resilient estimation mitigates.
4. **Attacks:** Physical spoofing of active sensors (analog domain), plus eavesdropping; simple vs advanced spoofing.
5. **Prediction:** Yes — models/predicts expected sensor output.
6. **Threshold:** Yes — χ² alarm threshold α (tuned to noise).
7. **Detection:** Yes — authentication core.
8. **Recovery:**  attack‑resilient estimation.
9. **Formal guarantee:** Yes — **Theorem 1**: the attacker's mis‑detection delay τ_attack ≥ τ_physical_limit > 0 is **provably bounded away from zero** (rooted in change‑point‑detection limits on noisy signals). Method = **information‑theoretic / change‑point‑detection bound** + χ² detector.
10. **Claiming strength:** 90+ experiments; detects + mitigates spoofing + detects eavesdropping; physics‑based provable guarantee; broadly generalizable to active sensors.
11. **Novelty vs prior work:** System‑level schemes (power‑grid state‑space/control‑theoretic detectors, sensor fusion) and post‑digitization crypto cannot stop **analog‑domain** attacks before digitization. PyCRA is the **first physical challenge‑response authentication** leveraging **sensing physics ("passivity")** to detect and resist analog spoofing at the sensor level.
12. **Limitation:** Active sensors only; the sensor periodically pauses measurement; later shown bypassable (Shin et al. "sampling race"); demonstrated on magnetic encoder + RFID only.
13. **Threat model (detail):** Assumptions A1–A4: no direct sensor‑hardware/firmware access; attacker manipulates only the analog signal; unbounded computational power but suffers unavoidable **physical delay**. Adversarial goals G1 concealment, G3 signal masking. Types: T1 eavesdropping, T2 simple spoofing, T3 advanced (measure‑then‑inject) spoofing.
14. **Rating:** **3/3** (seminal).

### 13. Real‑time Attack‑recovery for CPS using LQR (EMSOFT / ACM TECS 2021)
1. **Sensors:** Generic CPS sensors (wheel‑speed, GPS, LiDAR examples); 5 simulators.
2. **Target:** CPS — UAVs + autonomous vehicles (5 simulators incl. quadcopter).
3. **Model (solution):** *Type: control‑theoretic (LQR optimization + ADMM) + reachability analysis — not ML.* A **Linear‑Quadratic Regulator (LQR)** recovery controller with timing + safety constraints, whose horizon = safety deadline + a maintainable time; solved fast by an **ADMM** algorithm. Supporting components: a **sliding‑window checkpointer** (trustworthy data under variable detection delay), a **state reconstructor**, and a **reachability‑based deadline estimator**.
4. **Attacks:** Sensor/transduction attacks (3 types).
5. **Prediction:** Yes— state reconstruction + reachability.
6. **Threshold:** No — no detection threshold (assumes detector); uses a safety deadline.
7. **Detection:** No — assumes an existing detector.
8. **Recovery:** Yes — core.
9. **Formal guarantee:** Yes — **reachability analysis** guarantees no unsafe state is reachable during recovery and that the system reaches/maintains the target set before the deadline.
10. **Claiming strength:** Real‑time; provable safety before deadline; smooth trajectory + maintainability; outperforms the earlier RTSS'20 approach (less oscillation).
11. **Novelty vs prior work:** Cyber recovery rolls back *computing* state, not the physical state; discard‑and‑reuse‑original‑controller methods ignore the **safety deadline** and can't guarantee safety during recovery; robust control handles only bounded disturbance. This adds **LQR recovery with timing/safety constraints + ADMM + reachability deadline estimator + maintainability**, extending the authors' own linear‑approximation recovery (RTSS'20) with smoothness and post‑recovery hold.
12. **Limitation:** Needs an external detector; LQR/linearization; simulation only; non‑negligible recovery compute for complex systems.
13. **Threat model (detail):** Goal = alter sensor data to push the plant to unsafe states. Capability = arbitrarily modify sensor readings from cyber or transduction channels. Assumptions = a detector already exists and provides the attack‑start time; recovery triggers on its alert (a false negative → no recovery).
14. **Rating:** **2/3**.

### 14. Nyctea — Real‑time Rectifying Flight Control Misconfiguration (ACM TOSEM 2025)
1. **Sensors:** Drone flight state via physical‑vs‑desired deviation (not sensor‑specific).
2. **Target:** UAV — ArduPilot + PX4.
3. **Model (solution):** *Type: DL‑based reinforcement learning (continuous‑control agent) + deviation monitor.* A **deviation monitor** (segment deviation over consecutive intervals) + a **reinforcement‑learning intelligent agent** (continuous‑control, DDPG‑style) that generates a proper configuration for the current flight condition, iterating reconfiguration until instability is eliminated.
4. **Attacks:** **Not adversarial** — misconfigurations (improper configs / inappropriate settings) causing instability.
5. **Prediction:** Yes — the agent generates configs from state; deviation monitoring.
6. **Threshold:** Yes — instability threshold TH on the segment deviation value.
7. **Detection:** Yes — instability detection (F1 99.51% ArduPilot).
8. **Recovery:** Yes — on‑the‑fly reconfiguration (state rectifier).
9. **Formal guarantee:** No.
10. **Claiming strength:** Eliminates 85% of misconfiguration‑induced instabilities; 0.12 ms/config; cross‑platform; open‑sourced.
11. **Novelty vs prior work:** Fuzzers (RVFuzzer, LGDFuzzer, ICSearcher) only *find* misconfigs offline and **restrict parameter ranges** (reducing flexibility); revert‑to‑baseline schemes assume a default config fits all conditions; post‑incident detectors act too late. Nyctea is the **first RL‑based real‑time rectification** that adapts configs to conditions without range restriction or baseline reversion.
12. **Limitation:** Misconfiguration scope (not adversarial attacks); per‑flight‑control‑system training; threshold FP/FN tradeoff; fails if instability escalates too fast.
13. **Threat model (detail):** **No attacker** — the "threat" is a user‑introduced misconfiguration: either an inherently improper parameter set, or a proper set that is inappropriate for current environment/mission, leading to trajectory deviation, hovering, freezing, or crash.
14. **Rating:** **2/3** (strong engineering, off the sensor‑attack theme).

### 15. Real‑Time Data‑Predictive Attack‑Recovery for Complex (nonlinear) CPS (IEEE RTAS 2023)
1. **Sensors:** CPS sensors (cameras, LiDAR, IMU, GPS); AV, quadrotor, marine.
2. **Target:** Nonlinear CPS — AVs, UAVs (quadrotor), marine (simulators).
3. **Model (solution):** *Type: control‑theoretic (nonlinear reachability + MPC‑style recovery) — not ML.* Four components — a **state predictor** doing **nonlinear reachability analysis** (via **Flow\*** / Taylor models) to reconstruct the recovery initial‑state set; a **time oracle** computing an online safety deadline; a **model adaptor** that linearizes the nonlinear dynamics on the fly; and an **MPC‑style adaptive recovery‑sequence generator** solving the linearized problem. Crucially it keeps using **uncompromised sensors** to curb uncertainty accumulation.
4. **Attacks:** Sensor/transduction attacks (GPS spoofing, camera light injection).
5. **Prediction:** Yes — state predictor (data‑predictive).
6. **Threshold:** No — assumes a detector; uses an online safety deadline.
7. **Detection:** No — assumes existing detector/diagnosis.
8. **Recovery:** Yes — core.
9. **Formal guarantee:** Yes — **nonlinear reachability analysis (Flow\*)** guarantees the CPS never reaches unsafe states, recovers before the deadline, and stays in the target set for a period; a fail‑safe takes over if the safety check fails.
10. **Claiming strength:** Works on **nonlinear** systems; leverages good sensors to relieve uncertainty explosion; low overhead via linear approximation + Flow\*; formal safety guarantees.
11. **Novelty vs prior work:** Virtual‑sensor recovery reuses the nominal controller (no timing/safety guarantee); prior safety‑controller recovery (LP/LQR, Zhang et al.) assumes **linear** systems and **discards all sensors** after detection (uncertainty explodes). This is the **first real‑time recovery for nonlinear CPS** using nonlinear reachability + online deadline + retention of uncompromised sensors.
12. **Limitation:** Needs an external detector/diagnosis; small chance the recovery check fails (fail‑safe fallback); simulation only.
13. **Threat model (detail):** Goal = corrupt sensor measurements → stale state estimates → unsafe states. Capability = non‑invasive transduction/spoofing of some sensors (no assumption on which). Assumptions = compromised sensors are identifiable from data; a detector already flags the attack; some sensors remain good.
14. **Rating:** **2/3**.

### 16. Recovery from Adversarial Attacks in CPS: Shallow, Deep, Exploratory — Survey (ACM CSUR 2024)
1. **Sensors:** N/A (survey — sensors/actuators/controllers).
2. **Target:** All CPS — UAVs, AVs, grids, robotic arms, medical.
3. **Model (solution):** *Type: N/A — survey / taxonomy (no single model).* A **taxonomy**: **shallow recovery** (no dedicated recovery controller — redundancy/DIR, state estimation, ARIMA/roll‑forward) vs **deep recovery** (dedicated recovery controller — Simplex, LQR/LP, learned policies), plus exploratory works; classified by attack surface, targets, and target predicate P (expressed in LTL/MTL/STL).
4. **Attacks:** Full adversarial CPS spectrum (sensor/actuator/controller; cyber + physical; transduction, spoofing, DoS, stealthy).
5. **Prediction:** Yes reviews prediction/estimation‑based recovery (ARIMA, MPC roll‑forward).
6. **Threshold:** N/A.
7. **Detection:** Yes reviews detection as a recovery prerequisite.
8. **Recovery:** Yes the whole topic (30 papers).
9. **Formal guarantee:** Yes reviews formal‑guarantee methods (reachability, temporal logic); notes deep recovery gives higher assurance.
10. **Claiming strength:** First survey focused specifically on CPS **physical‑state recovery** with a practical shallow/deep taxonomy.
11. **Novelty vs prior work:** Prior surveys emphasize **detection/IDS** (Mitchell & Chen), physics‑based detection (Giraldo et al.), or cyber/network attacks, "often overlooking the physical dimension" and recovery. This is the **first** to center **recovery of physical states**.
12. **Limitation:** Small field (30 papers, 2014–2023); no new technique; physical‑state scope only.
13. **Threat model (detail):** Surveys the attack taxonomy (purposes: safety vs confidentiality/availability/privacy; surfaces: cyber vs physical; targets: controller/sensor/actuator) rather than assuming one model.
14. **Rating:** **3/3** (excellent reference for exactly this research line).

### 17. Robotics Cyber Security: Vulnerabilities, Attacks, Countermeasures — Survey (Int. J. Info. Security 2022)
1. **Sensors:** N/A (all robotic sensors).
2. **Target:** Robotics broadly — UAVs, UGVs, underwater, medical, industrial, military.
3. **Model (solution):** *Type: N/A — survey / taxonomy (no single model).* A **taxonomy + qualitative risk assessment** of vulnerabilities/attacks/countermeasures across physical/network layers, plus authentication/crypto recommendations and a COVID‑19 robotics section.
4. **Attacks:** Physical‑layer, network‑layer, replay, MiTM, DoS, spoofing, cyber‑terrorism.
5. **Prediction:** Yes reviews predictive cyber‑security.
6. **Threshold:** N/A.
7. **Detection:** Yes — extensive IDS/IPS/honeypot review.
8. **Recovery:** No — mentions RV recovery (e.g., Choi et al.).
9. **Formal guarantee:** — (mentions "formal security processes," no method).
10. **Claiming strength:** Broad "global" review + qualitative risk assessment + design recommendations + COVID‑19 robotics.
11. **Novelty vs prior work:** Claims prior surveys (ROS security, HRI safety, ICS defense) each cover only a single aspect with no global understanding and no secure‑design recommendations; this offers a **global** review + new attack‑taxonomy + recommendations.
12. **Limitation:** Broad/high‑level, qualitative, no experiments; shallow on the specific sensor‑attack recovery theme.
13. **Threat model (detail):** Covers a general internal/external adversary taxonomy across layers rather than a single model.
14. **Rating:** **2/3**.

### 18. DAE‑Crit‑LSTM — Robust Motion Planning for Multi‑Robot Systems vs Position Deception (IEEE TIFS 2024)
1. **Sensors:** Robot position/localization (LiDAR FOV, communication channel).
2. **Target:** Multi‑robot systems (mobile robots, simulation) — UGV‑class.
3. **Model (solution):** *Type: DL‑based / neural network (denoising autoencoder + LSTM deep RL).* A **stacked Denoising AutoEncoder (DAE)** that restores real obstacle positions from corrupted ones (encoder takes current corrupted position + recorded previous position/velocity; decoder outputs the approximate true position), feeding an **LSTM‑based Deep‑RL planner (Crit‑LSTM)** that handles a variable obstacle count. Detector‑ and method‑agnostic.
4. **Attacks:** Position Deception Attacks (PDAs) — 6 types: biased, percentage, zero‑replacement, delay, rotation, physical removal (trained on 3, tested on all 6).
5. **Prediction:** Yes — DAE estimates real positions; predicts obstacle states.
6. **Threshold:** No (detector‑agnostic).
7. **Detection:** No (by design).
8. **Recovery:** Yes — mitigation via position correction.
9. **Formal guarantee:** No (empirical "guarantee safe motion").
10. **Claiming strength:** First DAE‑based PDA mitigation for multi‑robot; success 77.5%→98.1%; generalizes to 3 unseen attacks; beats SSR baseline (+8.9%); method‑agnostic.
11. **Novelty vs prior work:** Single‑robot recovery (SSR, PID‑Piper) degrades badly on simultaneous multi‑robot attacks; detection‑based defenses (control invariants, CUSUM, deep learning) have limited response + high FP. This is the **first DAE‑based, detector‑free PDA mitigation for multi‑robot systems** (a stronger‑venue follow‑up to the same group's GAN‑MP).
12. **Limitation:** Simulation only; multi‑robot‑specific; assumes each robot localizes itself accurately; attacker can't access control software.
13. **Threat model (detail):** Goal = feed wrong obstacle positions so a robot collides. Capability = tamper with obstacle positions via (i) compromised adversarial robots broadcasting false positions, (ii) sensor manipulation (LiDAR FOV / designated‑position spoofing), or (iii) comm‑channel modification; time‑varying. Assumption = attacker cannot access control software (cannot bypass the DAE); robots know their **own** position accurately.
14. **Rating:** **2/3**.

### 19. Robustness Testing of Data‑ and Knowledge‑Driven Anomaly Detection (DSN‑W 2022)
1. **Sensors:** CPS sensors — APS blood‑glucose / insulin‑on‑board.
2. **Target:** Artificial Pancreas Systems (medical); AVs mentioned. Neither UAV/UGV.
3. **Model (solution):** *Type: ML/DL‑based (MLP, LSTM neural networks) augmented with a formal (STL) semantic loss.* Evaluates **ML anomaly detectors (MLP, LSTM)** and augments them with **domain knowledge as a semantic loss** — encoding **STL** unsafe‑control‑action formulas (from STPA hazard analysis) as a regularization term during training.
4. **Attacks:** Adversarial perturbations via **FGSM** + accidental **Gaussian noise** on the ML monitor's inputs (small, evade sensor/invariant/CUSUM detection).
5. **Prediction:** Yes — hazard prediction.
6. **Threshold:** Yes — classification (implicit).
7. **Detection:** Yes — ML safety monitoring, robustness‑tested.
8. **Recovery:** No.
9. **Formal guarantee:** No — STL knowledge integrated, but empirical.
10. **Claiming strength:** Semantic‑loss integration cuts robustness error up to **54.2%** while keeping F1 and improving transparency.
11. **Novelty vs prior work:** Robustness of DL was studied mostly for image/speech, not **multivariate‑time‑series CPS safety monitors**; prior CPS adversarial‑ML work discussed challenges but not robustness testing + improvement. This is the **first** robustness testing (FGSM + Gaussian) of ML CPS safety monitors and shows **domain‑knowledge semantic loss** improves robustness.
12. **Limitation:** Preliminary (workshop); APS only (2 case studies); FGSM only.
13. **Threat model (detail):** Attacker crafts **small** perturbations (FGSM) that evade current input‑error/invariant/CUSUM detection but, if also fed to the ML monitor, flip its prediction; e.g., remote login to an insulin pump to inject perturbations.
14. **Rating:** **2/3** (preliminary companion to #7).

### 20. SCVMON — Data‑oriented Attack Recovery via Safety‑Critical Variable Monitoring (RAID 2023)
1. **Sensors:** Gyro, accelerometer, barometer, GPS (via internal variables) + configuration parameters.
2. **Target:** RV — ArduPilot drone (UAV).
3. **Model (solution):** *Type: static program analysis + graph algorithm (s‑t cut) — not ML.* **Static program analysis** identifies **Safety‑Critical Variables (SCVs)** in the control program, then an **s‑t‑cut on the data‑flow graph** selects a minimal **monitored set (mSCVs)** that all input changes must flow through, scored by a **recovery‑suitability** criterion. Detection = two thresholds on mSCV change; recovery = replace the offending mSCV with a predicted normal value (avg of previous values) + mode switching.
4. **Attacks:** Data‑oriented attacks — **sensor** attacks + **parameter** attacks (20 types), incl. stealthy and combined.
5. **Prediction:** Yes — predicts normal mSCV values.
6. **Threshold:** Yes — Threshold1 (single‑cycle diff) + Threshold2 (accumulated diff over a window).
7. **Detection:** Yes — mSCV monitoring.
8. **Recovery:** Yes — value replacement + normal/recovery mode switch.
9. **Formal guarantee:** Yes — **proof (mathematical induction) that the s‑t‑cut mSCV set is "complete"** (every source→sink data‑flow path passes through an mSCV); not a security proof.
10. **Claiming strength:** First to recover from **both** sensor *and* parameter data‑oriented attacks; hardware‑independent (software update only); 20 attack types; low overhead (0.3% storage, 5.1% runtime).
11. **Novelty vs prior work:** Redundancy/virtualization recovery (VirtualDrone, BlueBox) needs extra hardware/software; prior recovery (SSR, PID‑Piper, LA) only handles **sensor** attacks, not **parameter** attacks; detection factors (actuator outputs) are poorly suited for recovery. SCVMON is the **first** architecture recovering from diverse data‑oriented attacks via systematic SCV identification + recovery‑suitability.
12. **Limitation:** ArduPilot only; assumes the attacker can't directly change mSCVs or violate control flow; empirical thresholds.
13. **Threat model (detail):** Attacker has **RV/control‑program expertise** and runs data‑oriented attacks that change sensor measurements and/or configuration parameters **without violating control flow**; cannot directly write mSCVs (must change them indirectly through inputs), and cannot bypass the monitor.
14. **Rating:** **3/3**.

### 21. Secure Autonomous CPS through Verifiable Information Flow Control (CPS‑SPC 2018)
1. **Sensors:** Camera (PointGrey), 2D LiDAR (SICK), GPS; landmark detection.
2. **Target:** Autonomous road vehicle — Segway‑based robotic testbed (UGV).
3. **Model (solution):** *Type: language‑based formal methods (information‑flow typing) + formally‑verified hardware — not ML.* **Vertically‑integrated, language‑based information‑flow control**: the planner + map‑verifier are written in **Jif** (a security‑typed language enforcing integrity/confidentiality labels at compile time) so only verified map data reaches the planner; a **formally‑verified secure processor (HyperFlow)** enforces timing‑safe isolation in hardware; a map‑verification algorithm cross‑checks external maps against local sensors.
4. **Attacks:** Remote software attacks (compromised map server), sensor spoofing, hardware timing interference.
5. **Prediction:** No (input verification, not state prediction).
6. **Threshold:** No.
7. **Detection:** Yes — input verification via local sensors detects adversarial map manipulation.
8. **Recovery:** No (prevention/verification; reviews restart‑based recovery).
9. **Formal guarantee:** Yes — **provable end‑to‑end security via static information‑flow typing (Jif) + formally‑verified hardware (HyperFlow, SecVerilog‑style)**; timing‑sensitive noninterference.
10. **Claiming strength:** Provable, vertically‑integrated (HW+SW+control) security; ~12% isolation overhead; working prototype + verified processor.
11. **Novelty vs prior work:** Prior CPS security protected a **single layer**, or used steep‑learning‑curve formal tools; prior resilient‑estimation gives bounds under stochastic noise but no language‑level guarantees. This is the **first** to **vertically integrate** language‑based IFC across HW/SW/control for provable AV security.
12. **Limitation:** No physical hardware tampering; assumes ≥1 trusted obstacle detector and a hardened OS microkernel; the verified processor isn't yet integrated into the prototype; not all sensors compromised.
13. **Threat model (detail):** Remote adversary with **no direct hardware access** but controlling untrusted network inputs (e.g., a compromised map server) and a **subset** of sensor inputs via spoofing/environmental tampering; may take over user‑level software; assumes a hardened OS and that not all sensors/inputs are controlled (no defense against a fully‑omnipotent adversary).
14. **Rating:** **2/3**.

### 22. Sensor Attack Online Classification for UAVs using ML (Computers & Security 2025)
1. **Sensors:** Gyroscopes, accelerometers, magnetometers, barometers, GPS.
2. **Target:** UAV — ArduPilot, SITL, Pixhawk.
3. **Model (solution):** *Type: ML‑based (primarily an MLP neural network; also decision‑tree / RF / XGBoost classifiers).* An **ML multi‑classifier** (primarily an **MLP**, vs decision tree/RF/XGBoost) trained on **heterogeneous data fusion** (inertial + baro + GPS + EKF/filter parameters) with **feature‑importance / correlation / ablation** feature selection; deployed on firmware via **frugally‑deep** for online inference.
4. **Attacks:** Sensor injection attacks — gyro, accelerometer, barometer, GPS.
5. **Prediction:** Yes — classification (not state prediction).
6. **Threshold:** Yes — classification confidence (implicit).
7. **Detection:** Yes — detection + **attack‑type classification**.
8. **Recovery:** No (future work).
9. **Formal guarantee:** No.
10. **Claiming strength:** First real‑time **multi‑class** classification of UAV sensor attacks; 89.38% offline / 74% online; lightweight (**48 KB**, **0.495 ms**); one model for many attacks.
11. **Novelty vs prior work:** Prior detectors rely on control‑invariants / critical variables / I/O data (SAVIOR, CI, Mini‑Me, SCVMON) and are mostly **one detector per attack type** (heavy) or GPS‑only (not universal), missing per‑sensor abnormal patterns. This is the **first** to detect **and multi‑class‑classify** several UAV sensor attacks simultaneously with a single lightweight model.
12. **Limitation:** Online accuracy drops to 74%; SITL simulation‑data limits; software‑injection realism limits; no recovery.
13. **Threat model (detail):** Attacker (assumed to possess knowledge of the flight‑control software) first identifies sensor types/communication protocols, then tampers/hijacks/injects false data — sending falsified data, modifying calibration parameters, or tampering with signals — to disrupt navigation/attitude/positioning.
14. **Rating:** **2/3**.

### 23. Gecko — Software Availability Protection in CPS (USENIX Security 2025)
1. **Sensors:** Generic CPS peripherals (IMU, sensors/actuators); IMU compromised in tests.
2. **Target:** CPS — UAV (ArduPilot/PX4), UGV (Jackal), robotic arm (OpenManipulator).
3. **Model (solution):** *Type: systems‑based (software compartmentalization + checkpoint/rollback) with an offline predictive (ML) model for data compensation — not primarily ML.* A recovery‑based availability framework: **software compartmentalization** (with CFI + value‑based DFI, from OAT) tracks runtime dependencies to attribute the adversarial input; **fail‑safe shadow compartments** disable the exploited feature (graceful degradation); **snapshot/checkpoint rollback** does selective state reset; an **I/O reference monitor** makes peripheral‑config registers read‑only after init (avoiding re‑calibration); an offline **predictive model** compensates the disabled input's data.
4. **Attacks:** Memory‑corruption attacks (arbitrary read/write) with **repeated exploitation**.
5. **Prediction:** Yes — offline predictive model for data compensation.
6. **Threshold:** Yes — WCET timing threshold; detection point.
7. **Detection:** Yes — assumes a reference monitor (CFI/DFI) + its own timing detection.
8. **Recovery:** Yes — core (availability recovery).
9. **Formal guarantee:** Yes — **formally formulates real‑time software availability = liveness + timeliness** (deadline‑hit ability under bounded recovery time); no completeness proof.
10. **Claiming strength:** First to treat software **availability** (not just C/I) via recovery; 83.3% deadline hits, 8.28% overhead; three real platforms; feature‑reduction defeats repeated exploitation.
11. **Novelty vs prior work:** Existing software defenses (memory encryption, CFI) **crash on violation**, sacrificing availability; resiliency/fault‑recovery assumes benign, known bug patterns and often ignores **peripheral** state (reboot/checkpoint can't restore external device state). Gecko **formulates real‑time availability** and adds **feature reduction (shadow compartments) + selective reset + peripheral‑reconfig prevention** specifically against **adaptive attacks**.
12. **Limitation:** Assumes an existing reference monitor detects exploitation; soft real‑time focus; memory‑corruption (not physical sensor) attacks; metadata‑corruption concerns.
13. **Threat model (detail):** One or more **remotely exploitable memory vulnerabilities** in a user‑space CPS program; attacker has **arbitrary memory reads/writes** and can retry after reboot. Assumptions = a security reference monitor detects the exploitation (CFI + value‑based DFI); code injection prevented (DEP/W⊕X); metadata stored protected.
14. **Rating:** **3/3**.

### 24. SSR — Software‑based Realtime Recovery from Sensor Attacks on RVs (RAID 2020)
1. **Sensors:** Gyroscope, accelerometer, GPS, barometer, magnetometer.
2. **Target:** RV — quadrotor (UAV) + rover (UGV); 6 RVs, 2 real.
3. **Model (solution):** *Type: state‑space estimation via system identification — not deep learning.* **"Software sensors"** — for each physical sensor, a **state‑space model built via system identification** (prediction‑error minimization) predicts the vehicle's next physical state and converts it (via kinematic conversion equations) into the corresponding sensor reading. A recovery switch monitors real‑vs‑predicted discrepancy; error‑correction/compensation (low‑pass, complementary accel+mag angle estimation) curbs prediction drift.
4. **Attacks:** Physical sensor attacks — GPS spoofing, acoustic gyro/accel, optical‑flow spoofing, ABS magnetic, LiDAR/camera injection.
5. **Prediction:** Yes — software sensor predicts the real reading.
6. **Threshold:** Yes — recovery‑switch threshold `T_on` + recovery‑off threshold; accumulated‑error window.
7. **Detection:** Yes — via discrepancy (recovery switch).
8. **Recovery:** Yes — software sensors replace compromised ones.
9. **Formal guarantee:** No.
10. **Claiming strength:** First sensor recovery via **software sensors** (no hardware redundancy); patches legacy systems; recovers multi‑sensor attacks; low overhead; prevents crashes.
11. **Novelty vs prior work:** Prior defenses (Zimmer et al.; control‑invariant detection) only **detect**, not recover; hardware‑redundancy (TMR) is costly and still exposed to a common physical attack. SSR is the **first software‑only sensor recovery** using a system‑ID state‑space model, recovering same‑ or different‑kind multi‑sensor attacks.
12. **Limitation:** Model‑based (accuracy bounded by system ID); software‑sensor drift limits operation time; assumes the attacker can't produce a consistently sub‑threshold stealthy deviation; cyber attacks handled separately.
13. **Threat model (detail):** Attacker maliciously corrupts sensor signals through **external physical channels**, possibly **multiple sensors at once** with different techniques. Assumptions = attacker cannot access internal states or the navigation plan (so cannot keep deviation < threshold in the 400 Hz loop when the plan is unpredictable); the recovery framework itself (in the control program) is safe from cyber attacks.
14. **Rating:** **3/3** (the seminal SSR baseline).

### 25. SpecGuard — Specification‑Aware Recovery for RAVs from Physical Attacks (CCS 2024)
1. **Sensors:** GPS, gyroscope, accelerometer, camera, LiDAR; EKF fusion.
2. **Target:** RAV — drones (UAV) + rovers (UGV); virtual + real.
3. **Model (solution):** *Type: DL‑based / neural network (deep reinforcement learning, PPO) + formal (STL) mission specs.* A **Deep‑RL recovery control policy** (PPO, Stable‑Baselines3) trained with a **compliance‑based reward shaped from mission specifications encoded in Signal Temporal Logic (STL)** (geofence, altitude, obstacle distance τ). **State reconstruction** bounds attack‑induced sensor perturbation so **adversarial training** is tractable. Two variants: Proactive (main controller) and Reactive (secondary, activated on detection).
4. **Attacks:** Physical attacks — GPS spoofing, optical‑flow, gyro/accel tampering; stealthy.
5. **Prediction:** Yes — state reconstruction; RL policy.
6. **Threshold:** Yes — STL threshold τ (obstacle distance); relies on the detector's threshold.
7. **Detection:** No — relies on an external detector (PID‑Piper), out of scope.
8. **Recovery:** Yes — core (specification‑aware).
9. **Formal guarantee:** Yes — **STL formally defines the mission specs** and the reward, but the RL policy's compliance is empirical.
10. **Claiming strength:** 92% recovery; complies with mission specs (geofence, altitude); 5× lower spec‑violation and 2× higher recovery vs 5 prior methods; real RAVs; ~15% overhead.
11. **Novelty vs prior work:** Sensor redundancy fails (redundant sensors compromised); resilient control handles only noise/faults; fail‑safe (landing) is unsafe; and prior recovery (SSR, FTC, LQR, DeLorean, UnRocker) has a **narrow objective** (prevent crash / restore a set‑point) and **ignores mission specifications**. SpecGuard is the **first specification‑aware** recovery, translating STL specs into a Deep‑RL reward + state reconstruction enabling practical adversarial training.
12. **Limitation:** Relies on an external detector/diagnosis; sim‑to‑real training; assumes the attacker can't tamper the RL training or onboard software.
13. **Threat model (detail):** **White‑box** adversary knowing the RAV hardware/software; perturbs sensors at **any** location/time during the mission and can run **stealthy** attacks evading detection as long as possible. Assumptions = attacker cannot tamper the Deep‑RL training process or the onboard software.
14. **Rating:** **3/3**.

### 26. Stealthy Attacks against RVs Protected by Control‑based IDS (DTRAP 2021)
1. **Sensors:** Barometer, gyroscope, accelerometer, magnetometer, GPS; IMU.
2. **Target:** RV — drones (UAV) + rovers (UGV); 8 RVs (ArduPilot/PX4/Paparazzi), 3 real.
3. **Model (solution):** *Type: system‑identification / control‑model reverse engineering (state‑space + Kalman) — not ML.* An **attack methodology** that **reverse‑engineers control‑based IDS** (Control Invariants + Extended Kalman Filter). Via **system identification** and repeated observation (5–7 missions), the attacker learns the state‑space model, Kalman gain, monitoring window, and detection thresholds, then keeps injected deviation **just under** the threshold. Delivered by replacing shared libraries or by acoustic injection.
4. **Attacks:** Three stealthy attacks — **false data injection**, **artificial delay**, **switch‑mode** — that evade CI + EKF.
5. **Prediction:** Yes — exploits the CI/EKF prediction models (attacker learns them).
6. **Threshold:** Yes — central to the attack; learns detection thresholds/windows to stay under them (exploiting the large thresholds set at mode changes).
7. **Detection:** No — this is an **evasion** paper (defeats detection).
8. **Recovery:** No.
9. **Formal guarantee:** No.
10. **Claiming strength:** First **automated** attacks against RV control‑state‑estimation models that evade CI+EKF without RV‑specific knowledge; 8 RVs; learns in 5–7 missions; ≥160 m deviation; +65%/+30% mission‑time degradation.
11. **Novelty vs prior work:** Prior attacks exploited comm channels or injected noise **in the absence of protection**; here the RV is **protected by both CI and EKF**, and the attack is **fully automated** with minimal a‑priori knowledge (only that the RV uses PID). Claims to be the **first** to automatically find undetectable attacks against the control‑state‑estimation model without targeting a specific RV.
12. **Limitation:** The learned model/thresholds vary per hardware platform (needs re‑learning); assumes code injection / acoustic but **no** firmware tamper, root, or log deletion; requires a learning phase.
13. **Threat model (detail):** Goal = deviate/disrupt the mission stealthily. Capability = (i) malicious‑code injection replacing shared libraries with pre‑computed state‑space matrices + thresholds, or (ii) acoustic injection at the resonant frequency; can profile the RV over 5–7 missions. Constraints = **cannot** tamper firmware, has no root/OS access, cannot delete logs, no a‑priori physical‑property knowledge; assumes both CI and EKF may be deployed together.
14. **Rating:** **2/3** (motivates the recovery line — PID‑Piper etc.).

### 27. TileMask — Passive‑Reflection Attack against mmWave Radar Object Detection (CCS 2023)
1. **Sensors:** mmWave radar (FMCW).
2. **Target:** Autonomous driving / AVs — ground vehicle.
3. **Model (solution):** *Type: optimization‑based (adversarial) physical attack; targets a DNN detector.* A **physical passive‑reflection attack** on **DNN radar object detection (RODNet)**. Adversarial objects ("TileMask") = a 3D‑printed base + multiple metal‑foil **reflective tiles** whose inclination angle θ tunes echo amplitude; a **two‑step optimization framework** iteratively updates tile orientations and the objects' number/locations/sizes (differential‑evolution‑style) to manipulate the range‑azimuth map and hide the target — environment‑independent.
4. **Attacks:** Passive‑reflection adversarial objects → the radar fails to detect the target (hiding attack).
5. **Prediction:** No (attack).
6. **Threshold:** Yes — exploits the detector's confidence threshold; tile‑pruning threshold in generation.
7. **Detection:** No (defeats it).
8. **Recovery:** No.
9. **Formal guarantee:** — none.
10. **Claiming strength:** First passive‑reflection attack on DNN radar detection; **low cost ($10)**, **2 objects**, **no sub‑ns synchronization**, stealthy (concealed as car signs / under posters), environment‑independent, physically realized.
11. **Novelty vs prior work:** Prior radar spoofing **actively transmits** signals needing special devices at a specific distance + **sub‑nanosecond synchronization** (costly, impractical) and, per the authors' own tests, **cannot fool the downstream DNN**. TileMask is the **first** adversarial (passive‑reflection) attack that actually changes the **DNN** radar detection output.
12. **Limitation:** White‑box (full knowledge of the radar detection); occasionally detected at <8 m; requires affixing objects on the target vehicle.
13. **Threat model (detail):** Goal = make the victim AV fail to detect a (attacker‑parked) target vehicle → rear‑end collision. Capability = fabricate cheap adversarial objects and stick them at derived locations on the target, optionally hidden as car signs. Knowledge = **white‑box** full knowledge of the victim radar object‑detection system (from open‑source AD platforms); **cannot** obtain the victim's raw radar data.
14. **Rating:** **3/3**.

### 28. Un‑Rocking Drones (UNROCKER) — Acoustic Injection Attacks & Recovery (NDSS 2023)
1. **Sensors:** MEMS IMU — gyroscopes + accelerometers (baro/GNSS/optical‑flow context).
2. **Target:** UAV — drones.
3. **Model (solution):** *Type: DL‑based / neural network (denoising autoencoder).* A **denoising autoencoder (DAE)** that reconstructs benign IMU signals from resonance‑compromised ones — treating the jitter‑spread resonant signal as noise — enabling **full sensor‑value recovery without supplementary sensors**. Built on a realistic acoustic‑injection testbed; the key finding is that **sampling jitter** spreads resonant signals into the control logic's in‑band range, neutralizing the low‑pass filter.
4. **Attacks:** Acoustic injection (resonance) on MEMS IMU sensors.
5. **Prediction:** Yes — DAE reconstructs benign signals.
6. **Threshold:** No — recovery via DAE reconstruction, not a threshold.
7. **Detection:** Yes — focus is recovery; builds on detection.
8. **Recovery:** Yes — core (full IMU recovery).
9. **Formal guarantee:** No.
10. **Claiming strength:** First **full** sensor‑value recovery **without supplementary sensors** — works even when *all* sensors are compromised and against **persistent** attacks; identifies sampling jitter as the essential crash factor; open‑source testbed + data.
11. **Novelty vs prior work:** Mechanical shielding / sampling randomization need hardware changes and have side effects (heating; more DoS‑prone); detection approaches lack a flight contingency plan; the state‑of‑the‑art recovery (SSR‑style) **only lasts a few seconds** and **requires supplementary sensors**. UNROCKER is the **first** to achieve **full, persistent recovery of resonant MEMS IMU without extra sensors**, and the first to pinpoint **sampling jitter**'s role.
12. **Limitation:** MEMS‑IMU acoustic scope only; the DAE requires training; testbed‑centric evaluation.
13. **Threat model (detail):** Acoustic‑injection attacker resonating MEMS gyro/accel (e.g., LRAD/directed sonic weapon), possibly **persistent** and possibly compromising **all** IMU sensors simultaneously; goal = corrupt IMU output to crash the drone.
14. **Rating:** **3/3**.

---

## Cross‑cutting synthesis

**Roles.** *Offense* — 1 (Adv‑LiDAR), 4 (ESP), 8 (Injected&Delivered), 9 (MSF‑ADV), 26 (Stealthy vs CI/EKF), 27 (TileMask), plus the attack half of 28. *Detection‑centric* — 2 (STL), 5 (RIDS), 12 (PyCRA), 19 (ML robustness), 22 (UAV classification), + surveys. *Recovery‑centric (the densest sub‑theme)* — 3, 6, 10, 11, 13, 15, 18, 20, 23, 24, 25, 28.

**The dominant recovery pattern.** Recovery papers almost always (i) **assume an upstream detector** (Q7 = No/Yes for 6, 10, 13, 15, 25), and (ii) add **prediction / state reconstruction** (Q5 = Yes) + a **dedicated recovery controller**. The lineage is explicit and traceable through citations: **SSR (24)** → **PID‑Piper (11)** → **DeLorean (3)** / **SpecGuard (25)**; and the control‑theoretic branch **LQR recovery (13)** → **nonlinear data‑predictive (15)**; the **stealthy‑attack paper (26)** is what motivates tighter‑threshold recovery.

**Formal guarantees (Q9) are rare and cluster by community:** control‑theoretic reachability (**13** reachability, **15** Flow\* nonlinear reachability), physics/authentication (**12** physical‑delay bound + χ²), statistical detection (**5** χ²), language/hardware security (**21** Jif IFC + verified processor), and specification logic (**2, 7, 25** STL; **20** an s‑t‑cut completeness proof). Learning‑based recovery (**3, 6, 10, 18, 24, 28**) gives **no** formal guarantee.

**Sensors.** MEMS IMU (gyro/accel) and GPS dominate; the RV‑recovery cluster uses the full GPS+gyro+accel+mag+baro suite. Perception attacks (1, 9, 27) target LiDAR/camera/radar.

**UAV vs UGV.** Most RV‑recovery works cover **both** drones and rovers (3, 10, 11, 13, 15, 24, 25). Pure‑UAV: 14, 20, 22, 28. AV/car (UGV‑adjacent perception): 1, 4, 9, 27. Non‑vehicle CPS (neither): 2, 7, 12, 19.

**Two off‑theme papers** (rated 2/3 accordingly): **14 (Nyctea)** addresses *misconfiguration*, not an adversary; **17** is a broad robotics‑security survey.
