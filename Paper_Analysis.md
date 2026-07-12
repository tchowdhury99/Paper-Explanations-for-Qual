# Research Papers — Structured Analysis

Analysis of the papers in `Research Papers/` against the 14 questions in `Questions.md`.

**Scope note:** 28 unique research papers were analyzed. The folder also contained `Event Confirmation_Itinerary - Ascend.pdf` (a travel itinerary, not a paper — skipped) and a duplicate of *Software Availability Protection* (identical file, counted once).

**Rating key (Q14, out of 3):** relevance + rigor + contribution to the sensor‑attack / detection / recovery theme.
- **3** = seminal / top‑venue / directly on‑theme, strong evaluation (often real hardware).
- **2** = solid but narrower, simulation‑only, tangential, or preliminary.
- **1** = loosely related / limited.

Legend for the quick table: **P** = Prediction, **Th** = Threshold, **D** = Detection, **R** = Recovery, **FG** = Formal Guarantee. ✓ = yes, ✗ = no, ◐ = partial.

---

## Quick‑reference matrix

| # | Paper (short) | Sensors | UAV/UGV | Attack/Defense role | P | Th | D | R | FG | Rate |
|---|---|---|---|---|:-:|:-:|:-:|:-:|:-:|:-:|
| 1 | Adversarial LiDAR (Adv‑LiDAR) | LiDAR | AV (car) | Attack | ✗ | ◐ | ✗ | ✗ | ✗ | 3 |
| 2 | Anomaly Detection – Formal Methods (STL) | generic CPS | neither (train) | Detection | ✓ | ✓ | ✓ | ✗ | ◐ | 2 |
| 3 | DeLorean (Diagnosis‑guided Recovery) | GPS, gyro, accel, mag, baro | UAV+UGV | Detect+Diagnose+Recover | ✓ | ✓ | ✓ | ✓ | ✗ | 3 |
| 4 | ESP Spoofing (acoustic gyro) | MEMS gyro | UGV (car) | Attack + defense | ✓ | ✓ | ✓ | ◐ | ✗ | 2 |
| 5 | RIDS (Physical Dynamics) | GPS, IMU/accel (Khepera) | UGV (+UAV) | Detection | ✓ | ✓ | ✓ | ✗ | ◐ | 3 |
| 6 | GAN‑MP (Localization Attacks) | GPS, IMU, cam, LiDAR | UGV (mobile robot) | Mitigation | ✓ | ✗ | ✗ | ✓ | ✗ | 2 |
| 7 | Hybrid Knowledge/Data Monitors | generic CPS I/O | ADS + APS | Detection+Mitigation | ✓ | ✓ | ✓ | ◐ | ◐ | 3 |
| 8 | Injected & Delivered (SideSwing) | MEMS IMU | mixed embedded | Attack | ✗ | ✗ | ✗ | ✗ | ◐* | 3 |
| 9 | MSF‑ADV (Camera+LiDAR fusion) | camera + LiDAR | AV (car) | Attack | ✗ | ◐ | ✗ | ✗ | ✗ | 3 |
| 10 | SeqRec (Learn‑to‑Respond) | generic CPS sensors | UAV+UGV | Recovery | ✓ | ✗ | ✗ | ✓ | ✗ | 3 |
| 11 | PID‑Piper | GPS, baro, gyro, accel, mag | UAV+UGV | Detection+Recovery | ✓ | ✓ | ✓ | ✓ | ✗ | 3 |
| 12 | PyCRA (Challenge‑Response) | active sensors (mag enc, RFID) | neither (auto/robotics) | Detection+Resilience | ✓ | ✓ | ✓ | ◐ | ✓ | 3 |
| 13 | Real‑time Recovery LQR | generic CPS sensors | UAV+UGV (sim) | Recovery | ✓ | ✗ | ✗ | ✓ | ✓ | 3 |
| 14 | Nyctea (Flight Misconfig) | flight state | UAV | Detect+Rectify (config) | ◐ | ✓ | ✓ | ✓ | ✗ | 2 |
| 15 | Data‑Predictive Recovery (nonlinear) | cam, LiDAR, IMU, GPS | UAV+UGV+marine | Recovery | ✓ | ✗ | ✗ | ✓ | ✓ | 3 |
| 16 | Recovery Survey (Shallow/Deep) | — | all CPS | Survey (recovery) | ◐ | — | ◐ | ✓ | ◐ | 3 |
| 17 | Robotics Cyber Security Survey | — | all robots | Survey (broad) | ◐ | — | ✓ | ◐ | — | 2 |
| 18 | DAE‑Crit‑LSTM (Multi‑robot PDA) | position/localization | UGV (multi‑robot) | Mitigation | ✓ | ✗ | ✗ | ✓ | ✗ | 3 |
| 19 | Robustness Testing (ML monitors) | APS glucose sensors | neither (APS) | Detection robustness | ✓ | ◐ | ✓ | ✗ | ✗ | 2 |
| 20 | SCVMON (data‑oriented recovery) | gyro, accel, baro, GPS + params | UAV (RV) | Detection+Recovery | ✓ | ✓ | ✓ | ✓ | ◐ | 3 |
| 21 | Secure Autonomous CPS (IFC) | camera, LiDAR, GPS | UGV (Segway) | Prevention (verifiable) | ✗ | ✗ | ✓ | ✗ | ✓ | 3 |
| 22 | Sensor Attack Classification (UAV ML) | gyro, accel, mag, baro, GPS | UAV | Detection+Classification | ◐ | ◐ | ✓ | ✗ | ✗ | 2 |
| 23 | Gecko (Software Availability) | IMU/peripherals | UAV+UGV+arm | Recovery (availability) | ✓ | ◐ | ◐ | ✓ | ◐ | 3 |
| 24 | SSR (Software Sensors) | gyro, accel, GPS, baro, mag | UAV+UGV | Detection+Recovery | ✓ | ✓ | ✓ | ✓ | ✗ | 3 |
| 25 | SpecGuard (Spec‑aware Recovery) | GPS, gyro, accel, cam, LiDAR | UAV+UGV | Recovery | ✓ | ◐ | ✗ | ✓ | ◐ | 3 |
| 26 | Stealthy Attacks vs CI/EKF IDS | baro, gyro, accel, mag, GPS | UAV+UGV | Attack (evasion) | ◐ | ✓ | ✗ | ✗ | ✗ | 3 |
| 27 | TileMask (mmWave radar) | mmWave radar | AV (car) | Attack | ✗ | ◐ | ✗ | ✗ | ✗ | 3 |
| 28 | Un‑Rocking Drones (UNROCKER) | MEMS IMU (gyro, accel) | UAV | Attack + Recovery | ✓ | ✗ | ◐ | ✓ | ✗ | 3 |

\* Q8 "Injected & Delivered" has a signal‑processing theorem (sampling drift), not a security guarantee.

---

## Detailed answers (per paper)

### 1. Adversarial Sensor Attack on LiDAR‑based Perception (Adv‑LiDAR) — CCS 2019
1. **Sensors:** LiDAR (Velodyne VLP‑16 / HDL‑64E); camera, radar, GPS/IMU mentioned as context.
2. **Target:** Autonomous vehicle (self‑driving car; Baidu Apollo) — ground vehicle, closest to UGV.
3. **Model:** DNN‑based object detection (Apollo pipeline); attack = optimization + global‑sampling adversarial example generation.
4. **Attacks:** LiDAR spoofing (laser injection) crafted to fool the ML detector; front‑near fake obstacle.
5. **Prediction:** No (attack paper; the target system predicts obstacles, but no defensive prediction).
6. **Threshold:** Exploits the target's objectness (0.5) / positiveness (0.1) thresholds.
7. **Detection:** No (defense discussed only as future directions).
8. **Recovery:** No.
9. **Formal guarantee:** No.
10. **Claiming strength:** ~75% attack success (up from 30% vanilla); two safety‑critical scenarios (emergency brake, freezing).
11. **Novelty (claimed):** First security study of LiDAR‑based perception in AVs; models spoofing capability + optimization/global‑sampling method.
12. **Limitation:** White‑box assumption; narrow 8° spoofing angle; simulation‑based impact evaluation.
13. **Threat model:** LiDAR spoofing via roadside or attack‑vehicle laser; white‑box access to ML model & perception.
14. **Rating:** **3/3** — seminal, top venue.

### 2. Anomaly Detection in CPS: A Formal Methods Approach (STL) — CDC 2014
1. **Sensors:** Generic CPS output signals (train brake velocity); not sensor‑specific.
2. **Target:** Generic CPS (electronically‑controlled pneumatic train brake) — neither UAV nor UGV.
3. **Model:** Model‑free unsupervised inference of a Signal Temporal Logic (STL/iPSTL) formula via robustness‑degree search.
4. **Attacks:** Brake‑disabling / sensor‑affecting attacks (anomalies).
5. **Prediction:** ◐ Early detection via online monitoring (robustness degree over time).
6. **Threshold:** Yes — learned formula threshold (e.g., 0.9634) / robustness degree.
7. **Detection:** Yes — core contribution.
8. **Recovery:** No (mitigation via formal synthesis noted as future work).
9. **Formal guarantee:** ◐ Rigorous STL semantics (interpretable), but no security proof.
10. **Claiming strength:** First formal‑methods application to anomaly detection; human‑readable formulae; low misclassification.
11. **Novelty (claimed):** First instance of formal methods applied to CPS anomaly detection; unsupervised STL inference.
12. **Limitation:** Only two case studies (toy + train sim), no real robot; assumptions (attacks after learning; separability); 2014 scope.
13. **Threat model:** Adversary disables brakes / affects sensors after the learning phase.
14. **Rating:** **2/3** — foundational but limited evaluation and off the RV theme.

### 3. DeLorean — Diagnosis‑guided Attack Recovery for RVs — ASIA CCS 2024
1. **Sensors:** GPS, gyroscope, accelerometer, magnetometer, barometer (heterogeneous, multi‑sensor).
2. **Target:** Robotic vehicles — both drones (UAV) and rovers (UGV); 4 real + 2 simulated RVs.
3. **Model:** EKF sensor fusion + PID; graph‑based probabilistic attack diagnosis (factor graphs, causal analysis); historic‑state reconstruction; integrates LQR recovery.
4. **Attacks:** Sensor Deception Attacks (SDAs) — multi‑sensor simultaneous (GPS spoofing, acoustic gyro, magnetic); stealthy attacks.
5. **Prediction:** Yes — state reconstruction/estimation from historic states.
6. **Threshold:** Yes — detection + probabilistic diagnosis thresholds.
7. **Detection:** Yes (integrates existing detectors) + novel diagnosis.
8. **Recovery:** Yes — core (targeted, per‑compromised‑sensor recovery).
9. **Formal guarantee:** No (empirical).
10. **Claiming strength:** 93% recovery under multi‑sensor SDA; 96% diagnosis TP; 2× less deviation, 2.5× less mission delay vs worst‑case recovery; no crashes.
11. **Novelty (claimed):** First attack‑diagnosis‑guided recovery for RVs; causal identification of targeted sensors; unifies detect+diagnose+recover.
12. **Limitation:** Assumes an existing detector; no obstacles; attack‑free start & historic states; can't beat an all‑sensor permanent SDA.
13. **Threat model:** Adversary injects malicious signals to any subset (up to all) of sensors via physical channels; incl. stealthy.
14. **Rating:** **3/3**.

### 4. ESP Spoofing — Covert Acoustic Attack on MEMS Gyroscopes — IEEE TIFS 2022
1. **Sensors:** MEMS gyroscope (ESP/Electronic Stability Program); wheel‑speed / accelerometer context.
2. **Target:** Passenger ground vehicle (car) — UGV‑class.
3. **Model:** Fuzzy‑PID vehicle sim (Carsim + Simulink); defense = LSTM‑CUSUM predictor.
4. **Attacks:** Covert acoustic resonance spoofing of the MEMS gyro, embedded in ordinary audio (audio‑overlay stealth).
5. **Prediction:** Yes (defense) — LSTM predicts yaw rate.
6. **Threshold:** Yes — CUSUM threshold T.
7. **Detection:** Yes — LSTM‑CUSUM anomaly detector.
8. **Recovery:** ◐ Feeds predicted value instead of the attacked reading (mitigation).
9. **Formal guarantee:** No.
10. **Claiming strength:** Stealthy attack hidden in music; demonstrates drift/rollover; both attack + active defense.
11. **Novelty (claimed):** Non‑intrusive acoustic attack on ESP MEMS gyro embedded in ordinary sound; audio overlay.
12. **Limitation:** Simulation only (Carsim/Simulink, no real vehicle); safe‑speed threshold not precisely measured (future work).
13. **Threat model:** Attacker follows the car with high‑power ultrasonic gear, knows the control algorithm, cannot directly access the controller.
14. **Rating:** **2/3** — attack + basic defense, simulation‑bound.

### 5. RIDS — Exploiting Physical Dynamics to Detect Actuator & Sensor Attacks — 2017
1. **Sensors:** GPS, IMU/accelerometer, IPS (Khepera has 3 sensors); generalized to UAV IMU+GPS.
2. **Target:** Mobile robots — Khepera ground robot (UGV); UAV applicability shown.
3. **Model:** Estimation theory / nonlinear unknown‑input state estimation (NUISE); kinematic (physical‑dynamics) model.
4. **Attacks:** Actuator + sensor attacks via signal interference, sensor spoofing, logic bomb, physical damage.
5. **Prediction:** Yes — state prediction vs measured readings.
6. **Threshold:** Yes — Chi‑square test thresholds / confidence levels.
7. **Detection:** Yes — detect, pinpoint, and quantify attacks.
8. **Recovery:** No (detection + forensics only).
9. **Formal guarantee:** ◐ Formal modeling + statistical (Chi‑square) guarantee; no security proof.
10. **Claiming strength:** FP/FN both <1%; avg detection delay <0.40 s; no majority voting / Byzantine threshold; works if ≥1 sensor clean.
11. **Novelty (claimed):** First to detect both sensor & actuator attacks in nonlinear mobile robots with stochastic noise without majority voting.
12. **Limitation:** Needs ≥1 uncorrupted sensor; can't distinguish fault vs attack; single robot (Khepera); no recovery.
13. **Threat model:** Attacker corrupts a subset (not all) of sensors/actuators via various channels.
14. **Rating:** **3/3**.

### 6. GAN‑MP — GAN‑based Robust Motion Planning vs Localization Attacks — RA‑L 2023
1. **Sensors:** GPS, IMU, camera, LiDAR (localization module).
2. **Target:** Mobile robots (holonomic robot, sim) — UGV‑class.
3. **Model:** GAN generator (position correction) + Deep RL (LSTM) motion planning; detector‑agnostic.
4. **Attacks:** Localization attacks (LAs) — GPS spoofing, IMU injection, LiDAR replay.
5. **Prediction:** Yes — GAN generator predicts/corrects positions.
6. **Threshold:** No (detector‑agnostic).
7. **Detection:** No (deliberately no detector).
8. **Recovery:** ✓ Mitigation (position correction + robust planning).
9. **Formal guarantee:** No ("guarantee safe motion" is empirical).
10. **Claiming strength:** First GAN‑based mitigation without any detector; method‑agnostic (integrates with any MP method); maintains high success/low collision under attack.
11. **Novelty (claimed):** First GAN‑based LA mitigation requiring no attack detector.
12. **Limitation:** Simulation only (no real robot); approximate positions; future real‑world work.
13. **Threat model:** LA generates faked position p̃ = α·p; attacker cannot access robot control software.
14. **Rating:** **2/3** — RA‑L letter, simulation‑only.

### 7. Hybrid Knowledge & Data‑Driven Synthesis of Runtime Monitors — IEEE TDSC 2024
1. **Sensors:** Generic CPS sensor/actuator I/O (ADS sensors; APS blood‑glucose/IOB).
2. **Target:** CPS — Autonomous Driving System (ground vehicle) + Artificial Pancreas Systems (medical).
3. **Model:** Hybrid STL formal spec + weakly‑supervised learning + NN (LSTM/MLP) with custom semantic loss; reaction‑time estimator.
4. **Attacks:** Accidental faults + malicious attacks on the controller; stealthy surge/bias attacks.
5. **Prediction:** Yes — preemptive hazard prediction + reaction‑time estimator.
6. **Threshold:** Yes — learns STL thresholds β.
7. **Detection:** Yes — context‑aware safety monitor detects unsafe control actions preemptively.
8. **Recovery:** ◐ Mitigation via corrective commands; reaction time budgets recovery.
9. **Formal guarantee:** ◐ Formal STL specs, but empirical performance.
10. **Claiming strength:** Up to 4.7× F1 improvement over baselines; preemptive hazard prediction; improved transparency; validated on clinical dataset (168 patients).
11. **Novelty (claimed):** Hybrid knowledge+data synthesis bridging design‑time hazard analysis and runtime monitoring; reaction‑time estimator.
12. **Limitation:** Simulation + clinical dataset (not deployed real‑time); assumes sensor data protected; monitor must be tamper‑proof.
13. **Threat model:** Faults/attacks target the controller itself or manifest on its output; sensor data assumed protected upstream.
14. **Rating:** **3/3**.

### 8. Injected and Delivered — Spoofing Inertial Sensors — USENIX Security 2018
1. **Sensors:** MEMS inertial sensors — gyroscopes + accelerometers (embedded).
2. **Target:** Broad embedded/actuation systems — self‑balancing transporters, VR, navigation, RC; not specifically UAV/UGV.
3. **Model:** Out‑of‑band signal‑injection model; SideSwing & Switching attacks (signal processing, not ML).
4. **Attacks:** Non‑invasive acoustic out‑of‑band injection spoofing inertial sensors (bidirectional control).
5. **Prediction:** No.
6. **Threshold:** Attack‑side only (program uses a threshold to switch direction).
7. **Detection:** No.
8. **Recovery:** No.
9. **Formal guarantee:** ◐ Theorem on sampling‑rate‑drift amplification (signal processing, not security).
10. **Claiming strength:** 17/25 devices implicitly controlled (23/25 affected); non‑invasive; realistic embedded targets.
11. **Novelty (claimed):** Two novel spoofing attacks (SideSwing, Switching); out‑of‑band injection model accounting for ADC sample‑rate drift.
12. **Limitation:** Range limited (few meters); requires analyzing an identical device; frequency pattern sometimes unpredictable.
13. **Threat model:** Non‑invasive, no physical contact; attacker profiles an identical device; ~several‑meter distance.
14. **Rating:** **3/3** — strong USENIX Security attack.

### 9. MSF‑ADV — Invisible for both Camera and LiDAR (Multi‑Sensor Fusion) — IEEE S&P 2021
1. **Sensors:** Camera + LiDAR (multi‑sensor fusion).
2. **Target:** Autonomous driving (Baidu Apollo, Autoware) — ground vehicle.
3. **Model:** MSF DNN perception; optimization‑based adversarial 3D‑object generation with differentiable rendering + soft point‑inclusion approximation.
4. **Attacks:** Physical‑world adversarial 3D‑printed object fooling camera AND LiDAR simultaneously (hiding attack).
5. **Prediction:** No (attack).
6. **Threshold:** Stealthiness threshold ε (attack parameter).
7. **Detection:** No (defeats detection; evaluates DNN defenses).
8. **Recovery:** No.
9. **Formal guarantee:** No (shows MSF's "security guarantee" assumption fails).
10. **Claiming strength:** >91% success across objects/algorithms; 100% simulated collision rate; stealthy, robust, transferable (~75%), physically realizable (99.1% frames evade LiDAR).
11. **Novelty (claimed):** First security study of MSF‑based AD perception; first to attack all fusion sources simultaneously.
12. **Limitation:** White‑box full knowledge; digital‑space defense eval; doesn't cover DNN‑level fusion; static object.
13. **Threat model:** White‑box full knowledge of MSF; places a 3D object on the roadway.
14. **Rating:** **3/3**.

### 10. SeqRec — Learn‑to‑Respond: Sequence‑Predictive Recovery — IEEE RTSS 2023
1. **Sensors:** Generic CPS sensors (GPS, accelerometer; vehicle speed, drone altitude).
2. **Target:** CPS — autonomous cars + drones; evaluated on SITL/ArduPilot (UAV) + 4‑wheel testbed (UGV).
3. **Model:** Two sequence‑to‑sequence (encoder‑decoder RNN) models — one for state estimation, one for recovery control; model‑free.
4. **Attacks:** Sensor attacks (spoofing/transduction) — GPS spoofing, acoustic on accelerometer.
5. **Prediction:** Yes — core (sequence‑predictive).
6. **Threshold:** No explicit threshold (assumes external detector); recovery ends at target state.
7. **Detection:** No — assumes an existing detector.
8. **Recovery:** Yes — core.
9. **Formal guarantee:** No (learning‑based, empirical).
10. **Claiming strength:** Model‑free (no system dynamics); real‑time; handles nonlinear/complex systems; validated on real 4‑wheel testbed.
11. **Novelty (claimed):** First sequence‑predictive, model‑free recovery; two Seq2Seq models for estimation + control.
12. **Limitation:** Depends on an external detector (+ its delay); no formal guarantee; needs training data; no post‑recovery stability guarantee.
13. **Threat model:** Attacker compromises sensors driving the system to unsafe states; existing detector with delay assumed.
14. **Rating:** **3/3**.

### 11. PID‑Piper — Recovering Robotic Vehicles from Physical Attacks — DSN 2021
1. **Sensors:** GPS, barometer, gyroscope, accelerometer, magnetometer.
2. **Target:** RVs — UAV (drones) + UGV (rovers); 6 RVs, 3 real (2 drones + 1 rover).
3. **Model:** ML‑based Feed‑Forward Controller (FFC, LSTM) with feature engineering, running in tandem with the PID controller.
4. **Attacks:** Physical attacks — GPS spoofing, acoustic gyro/accelerometer injection; overt + stealthy.
5. **Prediction:** Yes — FFC predicts the PID controller's output.
6. **Threshold:** Yes — deviation threshold τ (tight, via precise ML model).
7. **Detection:** Yes — via deviation between PID and ML predictions.
8. **Recovery:** Yes — FFC takes over.
9. **Formal guarantee:** No.
10. **Claiming strength:** 83% mission success under overt attacks (vs 13% prior), 0% FP; limits stealthy attacks 7×, 100% mission success under stealthy; <7% overhead.
11. **Novelty (claimed):** First to automatically recover RVs from overt attacks and limit stealthy ones; uses FFC (not FBC); ML model of the PID controller.
12. **Limitation:** Threshold‑dependent; assumes attacker can't access/bypass the FFC; stealthy not fully eliminated.
13. **Threat model:** Attacker injects bias into sensor readings (overt + stealthy); cannot access the controller/FFC.
14. **Rating:** **3/3**.

### 12. PyCRA — Physical Challenge‑Response Authentication for Active Sensors — CCS 2015
1. **Sensors:** Active sensors — magnetic encoders (wheel/gear speed), RFID readers; generalizes to ultrasound/radar/laser.
2. **Target:** Embedded active‑sensor systems (automotive/robotics, healthcare) — not specifically UAV/UGV.
3. **Model:** Physical challenge‑response authentication; χ² detector + system model prediction.
4. **Attacks:** Physical spoofing of active sensors (analog domain), eavesdropping, simple/advanced spoofing.
5. **Prediction:** Yes — models the sensor to predict expected output.
6. **Threshold:** Yes — χ² alarm threshold α.
7. **Detection:** Yes — authentication/detection core.
8. **Recovery:** ◐ Attack‑resilient estimation / mitigation.
9. **Formal guarantee:** Yes — Theorem on the unavoidable physical delay τ_attack; provable bound rooted in physics.
10. **Claiming strength:** 90+ experiments; detects + mitigates + eavesdropping detection; physics‑based theoretical guarantee; generalizable.
11. **Novelty (claimed):** First physical challenge‑response authentication for active sensors, exploiting sensing physics ("passivity").
12. **Limitation:** Active sensors only; sensor pauses measurement periodically; later shown bypassable (Shin et al. "sampling race"); only magnetic encoder + RFID demonstrated.
13. **Threat model:** Attacker manipulates the analog signal, no hardware/firmware access, seeks concealment; suffers unavoidable physical delay.
14. **Rating:** **3/3** — seminal.

### 13. Real‑time Attack‑recovery for CPS using LQR — EMSOFT / ACM TECS 2021
1. **Sensors:** Generic CPS sensors (wheel‑speed, GPS, LiDAR examples); 5 simulators.
2. **Target:** CPS — UAVs + autonomous vehicles (5 CPS simulators incl. quadcopter).
3. **Model:** LQR‑based recovery controller + ADMM fast solver; checkpointer, state reconstructor, reachability‑based deadline estimator.
4. **Attacks:** Sensor attacks (transduction/spoofing); 3 attack types.
5. **Prediction:** Yes — state reconstruction + reachability.
6. **Threshold:** No detection threshold (assumes detector); uses a safety deadline.
7. **Detection:** No — assumes an existing detector.
8. **Recovery:** Yes — core.
9. **Formal guarantee:** Yes — reachability guarantees no unsafe states are reachable during recovery + timing.
10. **Claiming strength:** Real‑time; guarantees safety before deadline; smooth trajectory + maintainability; outperforms prior (RTSS'20).
11. **Novelty (claimed):** LQR recovery with timing + safety constraints; ADMM solver; reachability deadline estimator; adds smoothness/maintainability.
12. **Limitation:** Needs external detector; LQR/linearization; simulation only; recovery computational overhead.
13. **Threat model:** Attacker alters sensor readings; existing detector supplies attack start time.
14. **Rating:** **3/3**.

### 14. Nyctea — Real‑time Rectifying Flight Control Misconfiguration — ACM TOSEM 2025
1. **Sensors:** Drone flight sensors via physical‑vs‑desired state deviation (not sensor‑specific).
2. **Target:** UAV (drones) — ArduPilot + PX4.
3. **Model:** Reinforcement‑learning "intelligent agent" (continuous control) + deviation monitor.
4. **Attacks:** **Not adversarial security attacks** — misconfigurations (improper configs / inappropriate settings) causing instability.
5. **Prediction:** ◐ Agent generates proper configs; deviation monitoring.
6. **Threshold:** Yes — instability threshold TH on segment deviation.
7. **Detection:** Yes — instability detection via deviation monitor (F1 99.51%).
8. **Recovery:** Yes — on‑the‑fly reconfiguration (state rectifier).
9. **Formal guarantee:** No.
10. **Claiming strength:** Eliminates 85% of misconfiguration‑induced instabilities; 0.12 ms/config; cross‑platform (ArduPilot + PX4).
11. **Novelty (claimed):** First RL‑based real‑time misconfiguration rectification without restricting parameter ranges or reverting to baseline.
12. **Limitation:** Misconfiguration (not adversarial) scope; per‑flight‑control‑system training; threshold tradeoff; fails if instability escalates too fast.
13. **Threat model:** Misconfiguration (no attacker) — improper/inappropriate parameter values.
14. **Rating:** **2/3** — strong engineering but off the sensor‑attack theme (config safety).

### 15. Real‑Time Data‑Predictive Attack‑Recovery for Complex (nonlinear) CPS — IEEE RTAS 2023
1. **Sensors:** CPS sensors (cameras, LiDAR, IMU, GPS); AV, quadrotor, marine.
2. **Target:** Nonlinear CPS — autonomous vehicles, UAVs (quadrotor), marine (simulators).
3. **Model:** Nonlinear reachability (Flow*), model adaptor (linearization), MPC‑style recovery generator, state predictor, time oracle.
4. **Attacks:** Sensor / transduction attacks (GPS spoofing, camera light injection).
5. **Prediction:** Yes — state predictor (data‑predictive).
6. **Threshold:** No (assumes detector); uses online safety deadline.
7. **Detection:** No — assumes existing detector/diagnosis.
8. **Recovery:** Yes — core.
9. **Formal guarantee:** Yes — guarantees the CPS never reaches unsafe states, recovers before deadline, and stays in the target set.
10. **Claiming strength:** Works on nonlinear systems; leverages uncompromised sensors to curb uncertainty; low overhead; formal safety guarantees.
11. **Novelty (claimed):** First real‑time recovery for nonlinear CPS via nonlinear reachability + online deadline + using good sensors.
12. **Limitation:** Needs external detector/diagnosis; small chance recovery check fails (fail‑safe fallback); simulation only.
13. **Threat model:** Attacker corrupts sensor measurements; compromised sensors assumed identifiable; detector in place.
14. **Rating:** **3/3**.

### 16. Recovery from Adversarial Attacks in CPS: Shallow, Deep, Exploratory (Survey) — ACM CSUR 2024
1. **Sensors:** N/A (survey — sensors, actuators, controllers).
2. **Target:** All CPS — UAVs, AVs, power grids, robotic arms, medical.
3. **Model:** Survey / taxonomy — shallow vs deep recovery (± dedicated recovery controller) + exploratory works.
4. **Attacks:** Adversarial CPS attacks (sensor/actuator/controller; cyber + physical; transduction, spoofing, DoS, stealthy).
5. **Prediction:** ◐ Reviews prediction/estimation‑based recovery (ARIMA, MPC).
6. **Threshold:** N/A.
7. **Detection:** ◐ Reviews detection as a prerequisite.
8. **Recovery:** Yes — core topic (30 papers).
9. **Formal guarantee:** ◐ Reviews formal guarantees (reachability, temporal logic); notes deep recovery gives higher assurance.
10. **Claiming strength:** First survey focused specifically on CPS *physical‑state* recovery; practical shallow/deep taxonomy.
11. **Novelty (claimed):** First recovery‑focused survey (vs many detection surveys); shallow/deep partition.
12. **Limitation:** Small field (30 papers); no new technique; physical‑state recovery scope only.
13. **Threat model:** N/A (surveys the attack taxonomy).
14. **Rating:** **3/3** — excellent reference for this exact research area.

### 17. Robotics Cyber Security: Vulnerabilities, Attacks, Countermeasures (Survey) — Int. J. Info. Security 2022
1. **Sensors:** N/A (all robotic sensors).
2. **Target:** Robotics broadly — UAVs, UGVs, underwater, medical, industrial, military.
3. **Model:** Survey — taxonomy of vulnerabilities/attacks/countermeasures + qualitative risk assessment.
4. **Attacks:** Full spectrum — physical layer, network layer, replay, MiTM, DoS, spoofing, cyber‑terrorism.
5. **Prediction:** ◐ Reviews predictive cyber‑security.
6. **Threshold:** N/A.
7. **Detection:** Yes — extensive IDS/IPS review.
8. **Recovery:** ◐ Reviews recovery (e.g., Choi et al. RV recovery).
9. **Formal guarantee:** N/A (mentions formal security processes).
10. **Claiming strength:** Broad global review + risk assessment + COVID‑19 robotics + design recommendations.
11. **Novelty (claimed):** "Global" review + new attack taxonomy + recommendations (claims prior work lacks global understanding).
12. **Limitation:** Broad/high‑level, not deep‑technical; qualitative; no experiments.
13. **Threat model:** N/A (covers internal/external adversary taxonomy).
14. **Rating:** **2/3** — broad but shallow for this specific theme.

### 18. DAE‑Crit‑LSTM — Robust Motion Planning for Multi‑Robot Systems vs Position Deception — IEEE TIFS 2024
1. **Sensors:** Robot position/localization (LiDAR FOV, communication channel).
2. **Target:** Multi‑robot systems (mobile robots, sim) — UGV‑class.
3. **Model:** Denoising AutoEncoder (DAE) + LSTM‑based Deep RL; detector‑agnostic.
4. **Attacks:** Position Deception Attacks (PDAs) — 6 types: biased, percentage, zero‑replacement, delay, rotation, physical removal.
5. **Prediction:** Yes — DAE estimates real positions; predicts obstacle states.
6. **Threshold:** No (detector‑agnostic).
7. **Detection:** No (deliberately detector‑agnostic).
8. **Recovery:** ✓ Mitigation (position correction).
9. **Formal guarantee:** No (empirical "guarantee safe motion").
10. **Claiming strength:** First DAE‑based PDA mitigation for multi‑robot; success 77.5%→98.1%; generalizes to unseen attacks; outperforms SSR baseline; method‑agnostic.
11. **Novelty (claimed):** First DAE‑based PDA mitigation for multi‑robot systems without a detector; handles variable obstacle count.
12. **Limitation:** Simulation only; multi‑robot specific; assumes robots localize themselves accurately; attacker can't access control software.
13. **Threat model:** PDA — attacker tampers obstacle positions (compromised robots, sensor manipulation, comm channel); no control‑software access.
14. **Rating:** **3/3** (follow‑up to GAN‑MP, same group, stronger venue).

### 19. Robustness Testing of Data‑ and Knowledge‑Driven Anomaly Detection — DSN‑W 2022
1. **Sensors:** CPS sensors — APS blood‑glucose / insulin‑on‑board.
2. **Target:** CPS — Artificial Pancreas Systems (medical); AVs mentioned. Not UAV/UGV.
3. **Model:** ML anomaly detectors (MLP, LSTM) + STL domain knowledge as a semantic loss.
4. **Attacks:** Adversarial perturbations (FGSM) + accidental Gaussian noise on ML monitor inputs.
5. **Prediction:** Yes — hazard prediction.
6. **Threshold:** ◐ Implicit (classification).
7. **Detection:** Yes — ML safety monitoring (robustness‑tested).
8. **Recovery:** No.
9. **Formal guarantee:** No (STL knowledge, but empirical).
10. **Claiming strength:** Semantic‑loss integration cuts robustness error up to 54.2% while keeping F1 and improving transparency.
11. **Novelty (claimed):** First robustness testing of ML safety monitors in CPS (FGSM + Gaussian); domain‑knowledge semantic loss for robustness.
12. **Limitation:** Preliminary (workshop); APS only (2 case studies); FGSM only.
13. **Threat model:** Attacker crafts small perturbations (FGSM) evading detection (e.g., remote login to insulin pump).
14. **Rating:** **2/3** — preliminary companion to #7.

### 20. SCVMON — Data‑oriented Attack Recovery for RVs via Safety‑Critical Variable Monitoring — RAID 2023
1. **Sensors:** Gyro, accelerometer, barometer, GPS (via internal variables) + configuration parameters.
2. **Target:** RV — ArduPilot drone (UAV).
3. **Model:** Static analysis of safety‑critical variables (SCVs); s‑t‑cut selection of monitored SCVs (mSCVs); data‑flow graph; threshold detection.
4. **Attacks:** Data‑oriented attacks — sensor attacks + parameter attacks (20 types), incl. stealthy and combined.
5. **Prediction:** Yes — predicts normal mSCV values (avg of previous values).
6. **Threshold:** Yes — two thresholds (single‑cycle diff + accumulated diff).
7. **Detection:** Yes — mSCV monitoring.
8. **Recovery:** Yes — replaces mSCV with a normal value; mode switching.
9. **Formal guarantee:** ◐ Proof of monitoring‑set completeness (all data‑flow paths pass through an mSCV); not a security proof.
10. **Claiming strength:** First recovery from *both* sensor and parameter data‑oriented attacks; hardware‑independent (software update only); low overhead (0.3% storage, 5.1% runtime); 20 attack types.
11. **Novelty (claimed):** First to recover RVs from diverse data‑oriented attacks (incl. parameter attacks); systematic SCV identification + recovery‑suitability.
12. **Limitation:** ArduPilot only; assumes attacker can't directly change mSCVs or violate control flow; empirical thresholds.
13. **Threat model:** Attacker with RV/control‑program expertise runs data‑oriented (sensor + parameter) attacks without violating control flow.
14. **Rating:** **3/3**.

### 21. Secure Autonomous CPS through Verifiable Information Flow Control — CPS‑SPC 2018
1. **Sensors:** Camera (PointGrey), 2D LiDAR (SICK), GPS; landmark detection.
2. **Target:** Autonomous road vehicle — Segway‑based robotic testbed (UGV).
3. **Model:** Security‑typed languages (Jif) + information‑flow control across HW/SW/control; formally verified secure processor (HyperFlow); map‑verification algorithm.
4. **Attacks:** Remote software attacks (compromised map server), sensor spoofing, hardware timing interference.
5. **Prediction:** No (input verification, not state prediction).
6. **Threshold:** No explicit.
7. **Detection:** Yes — input verification via local sensors detects adversarial map manipulation.
8. **Recovery:** No (prevention/verification; reviews restart‑based recovery).
9. **Formal guarantee:** Yes — provable end‑to‑end security via information‑flow control; formally verified HW/SW.
10. **Claiming strength:** Provable, vertically‑integrated (HW+SW+control) security; ~12% overhead; working prototype + verified processor.
11. **Novelty (claimed):** First vertically‑integrated, language‑based information‑flow‑control provable‑security architecture for autonomous vehicles.
12. **Limitation:** No physical hardware tampering; assumes ≥1 trusted obstacle detector; processor not yet integrated; testbed; not all sensors compromised.
13. **Threat model:** Remote adversary, no direct HW access, controls untrusted network inputs + a subset of sensors (spoofing); not all sensors.
14. **Rating:** **3/3** — novel, influential.

### 22. Sensor Attack Online Classification for UAVs using ML — Computers & Security 2025
1. **Sensors:** Gyroscopes, accelerometers, magnetometers, barometers, GPS.
2. **Target:** UAV (drones) — ArduPilot, SITL, Pixhawk.
3. **Model:** ML multi‑classification (mainly MLP) with feature selection + heterogeneous data fusion; deployed via frugally‑deep.
4. **Attacks:** Sensor attacks — gyro, accelerometer, barometer, GPS injection/spoofing.
5. **Prediction:** ◐ (classification, not state prediction).
6. **Threshold:** ◐ Implicit (classification confidence).
7. **Detection:** Yes — detection + attack‑type classification.
8. **Recovery:** No (classification only; recovery is future work).
9. **Formal guarantee:** No.
10. **Claiming strength:** First real‑time multi‑classification of UAV sensor attacks; 89.38% offline / 74% online; lightweight (48 KB, 0.495 ms); single model for many attacks.
11. **Novelty (claimed):** First to classify (multi‑class) UAV sensor attacks in real time (vs one‑detector‑per‑attack); heterogeneous data fusion.
12. **Limitation:** Online accuracy drops to 74%; SITL simulation data limits; software‑injection limits; no recovery.
13. **Threat model:** Attacker tampers/injects false data to accel/gyro/baro/GPS via physical tampering or software vulnerabilities; profiles sensor types/protocols.
14. **Rating:** **2/3** — classification only, moderate online accuracy.

### 23. Gecko — Software Availability Protection in CPS — USENIX Security 2025
1. **Sensors:** Generic CPS peripherals (IMU, sensors/actuators); IMU compromised in tests.
2. **Target:** CPS — UAV (ArduPilot/PX4), UGV (Jackal), robotic arm (OpenManipulator).
3. **Model:** Software compartmentalization + fail‑safe shadow compartments; snapshot/checkpoint recovery; I/O reference monitor; CFI + value‑based DFI detection; predictive data compensation.
4. **Attacks:** Memory‑corruption attacks (software exploitation, arbitrary R/W); repeated exploitation.
5. **Prediction:** Yes — offline predictive model for data compensation.
6. **Threshold:** ◐ WCET timing threshold; detection point.
7. **Detection:** ◐ Assumes a reference monitor (CFI/DFI) + own timing detection.
8. **Recovery:** Yes — core (availability recovery via feature reduction + snapshot).
9. **Formal guarantee:** ◐ Formulates real‑time availability (liveness + timeliness) rigorously; no formal proof.
10. **Claiming strength:** First to address software *availability* (not just C/I) via recovery; 83.3% deadline hits, 8.28% overhead; three real platforms; graceful degradation.
11. **Novelty (claimed):** Formulates real‑time software availability; feature reduction (shadow compartments) + selective state reset + peripheral‑reconfig prevention.
12. **Limitation:** Assumes a reference monitor detects exploitation; soft real‑time; memory‑corruption focus (not physical sensor attacks); metadata‑corruption concerns.
13. **Threat model:** Remotely exploitable memory vulnerability, arbitrary memory R/W; reference monitor detects; code injection prevented (DEP).
14. **Rating:** **3/3**.

### 24. SSR — Software‑based Realtime Recovery from Sensor Attacks on RVs — RAID 2020
1. **Sensors:** Gyroscope, accelerometer, GPS, barometer, magnetometer.
2. **Target:** RV — quadrotor (UAV) + rover (UGV); 6 RVs, 2 real.
3. **Model:** Predictive state‑space model via system identification → "software sensors" (virtual) + error‑correction/compensation.
4. **Attacks:** Physical sensor attacks — GPS spoofing, gyro/accel acoustic, optical flow, ABS magnetic, LiDAR/camera.
5. **Prediction:** Yes — software sensor predicts the real reading.
6. **Threshold:** Yes — recovery‑switch threshold (T_on) + recovery‑off threshold; accumulated errors.
7. **Detection:** Yes — recovery switch detects via discrepancy.
8. **Recovery:** Yes — core (software sensors replace compromised ones).
9. **Formal guarantee:** No.
10. **Claiming strength:** First sensor recovery via software sensors (no HW redundancy); patches legacy systems; recovers multi‑sensor attacks; low overhead; prevents crashes.
11. **Novelty (claimed):** First software‑sensor‑based recovery for RVs (system‑identification state‑space model).
12. **Limitation:** Model‑based (accuracy bounded by system ID); software‑sensor drift limits operation time; assumes attacker can't do sub‑threshold stealthy deviation; cyber attacks handled separately.
13. **Threat model:** Physical sensor attacks via external channels, multi‑sensor simultaneous; attacker can't access internal states/nav plan; recovery framework assumed safe from cyber attacks.
14. **Rating:** **3/3** — seminal baseline (SSR).

### 25. SpecGuard — Specification‑Aware Recovery for RAVs from Physical Attacks — CCS 2024
1. **Sensors:** GPS, gyroscope, accelerometer, camera, LiDAR; EKF fusion.
2. **Target:** RAV — drones (UAV) + rovers (UGV); virtual + real.
3. **Model:** Deep‑RL recovery control policy + STL mission specs + state reconstruction; compliance‑based reward (reward shaping); adversarial training.
4. **Attacks:** Physical attacks — GPS spoofing, optical flow, gyro/accel tampering; stealthy.
5. **Prediction:** Yes — state reconstruction predicts states; RL policy.
6. **Threshold:** ◐ STL threshold τ (obstacle distance); relies on the detector's threshold.
7. **Detection:** No — relies on an external detector (PID‑Piper); out of scope.
8. **Recovery:** Yes — core (specification‑aware recovery).
9. **Formal guarantee:** ◐ STL formally defines specs, but the RL policy is empirical.
10. **Claiming strength:** 92% recovery; complies with mission specs (geofence, altitude); 5× lower spec‑violation, 2× higher recovery vs prior; real RAVs; 15% overhead.
11. **Novelty (claimed):** First specification‑aware recovery; STL→Deep‑RL reward; state reconstruction makes adversarial training practical.
12. **Limitation:** Relies on external detector/diagnosis; sim‑to‑real training; assumes attacker can't tamper the RL training/onboard software.
13. **Threat model:** White‑box adversary (knows HW/SW), perturbs sensors anytime, stealthy; can't tamper the RL training or onboard software.
14. **Rating:** **3/3**.

### 26. Stealthy Attacks against RVs Protected by Control‑based IDS — DTRAP 2021
1. **Sensors:** Barometer, gyroscope, accelerometer, magnetometer, GPS; IMU.
2. **Target:** RV — UAV (drones) + UGV (rovers); 8 RVs (ArduPilot/PX4/Paparazzi), 3 real.
3. **Model:** Attack paper — exploits Control‑Invariant (CI) and EKF IDS; system identification to learn thresholds/windows.
4. **Attacks:** 3 stealthy attacks — false data injection, artificial delay, switch‑mode; evade CI + EKF.
5. **Prediction:** ◐ Exploits the CI/EKF prediction models (attacker learns them).
6. **Threshold:** Yes — core; attacker learns detection thresholds/windows to stay under them.
7. **Detection:** No — this is an evasion/attack paper (defeats detection).
8. **Recovery:** No.
9. **Formal guarantee:** No.
10. **Claiming strength:** First automated attacks against RV control‑state‑estimation models without detection or RV‑specific knowledge; 8 RVs; learns in 5–7 missions; 160 m deviation.
11. **Novelty (claimed):** First automated stealthy attacks defeating CI + EKF IDS without a‑priori RV knowledge; switch‑mode attack exploiting mode‑change threshold gaps.
12. **Limitation:** Model varies per hardware platform (needs re‑learning); assumes code‑injection/acoustic but no firmware tamper/root/log deletion; learning phase required.
13. **Threat model:** Attacker via malicious code injection or acoustic noise; no firmware tamper, no root, can't delete logs, no a‑priori physical knowledge.
14. **Rating:** **3/3** — motivates the recovery line (PID‑Piper etc.).

### 27. TileMask — Passive‑Reflection Attack against mmWave Radar Object Detection — CCS 2023
1. **Sensors:** mmWave radar (FMCW).
2. **Target:** Autonomous driving / AVs — ground vehicle.
3. **Model:** Attack on DNN radar object detection (RODNet); optimization‑based adversarial‑object generation (two‑step framework).
4. **Attacks:** Passive‑reflection adversarial objects (3D‑printed base + metal‑foil "tiles") — hiding attack.
5. **Prediction:** No (attack).
6. **Threshold:** ◐ Exploits detection confidence threshold; tile‑pruning threshold.
7. **Detection:** No (defeats detection).
8. **Recovery:** No.
9. **Formal guarantee:** No.
10. **Claiming strength:** First passive‑reflection attack on DNN radar detection; low cost ($10), 2 objects, no synchronization, stealthy (concealed as car signs), environment‑independent, physically realizable.
11. **Novelty (claimed):** First adversarial (passive‑reflection) attack against DNN‑based mmWave radar object detection; TileMask structure.
12. **Limitation:** White‑box (full knowledge of radar detection); occasionally detected at <8 m; requires placing objects on the target vehicle.
13. **Threat model:** White‑box, full knowledge of victim radar detection; can affix objects on the target; can't obtain the victim's raw radar data.
14. **Rating:** **3/3**.

### 28. Un‑Rocking Drones (UNROCKER) — Acoustic Injection Attacks & Recovery — NDSS 2023
1. **Sensors:** MEMS IMU — gyroscopes + accelerometers (baro/GNSS/optical‑flow context).
2. **Target:** UAV (drones).
3. **Model:** Denoising autoencoder (noise reduction) for full sensor‑value recovery; realistic testbed; sampling‑jitter analysis.
4. **Attacks:** Acoustic injection (resonance) on MEMS IMU sensors.
5. **Prediction:** Yes — denoising autoencoder reconstructs benign signals.
6. **Threshold:** ✗ (recovery via DAE reconstruction, not a threshold).
7. **Detection:** ◐ (focus is recovery; builds on detection).
8. **Recovery:** Yes — core (full sensor‑value recovery without supplementary sensors).
9. **Formal guarantee:** No.
10. **Claiming strength:** First *full* sensor‑value recovery without supplementary sensors; works even when all sensors compromised; handles persistent attacks; identifies sampling jitter as the key crash factor; open‑source testbed.
11. **Novelty (claimed):** Discovers sampling jitter's role in crashes; DAE recovery for resonant MEMS IMU without supplementary sensors; both attack foundations + recovery.
12. **Limitation:** MEMS‑IMU acoustic scope only; DAE requires training; testbed‑centric evaluation.
13. **Threat model:** Acoustic‑injection attacker resonating MEMS sensors; persistent attacks; potentially all sensors compromised.
14. **Rating:** **3/3**.

---

## Cross‑cutting observations

- **Attack papers (offense):** 1 (Adv‑LiDAR), 4 (ESP), 8 (Injected & Delivered), 9 (MSF‑ADV), 26 (Stealthy vs CI/EKF), 27 (TileMask), and the attack half of 28 (UNROCKER). These mostly assume white‑box/physical access and rarely include prediction/threshold/recovery.
- **Detection‑centric:** 2 (STL), 5 (RIDS), 12 (PyCRA), 19 (ML robustness), 22 (UAV classification), plus the surveys.
- **Recovery‑centric (the densest sub‑theme):** 3 (DeLorean), 6 (GAN‑MP), 10 (SeqRec), 11 (PID‑Piper), 13 (LQR), 15 (Data‑Predictive), 18 (DAE‑Crit‑LSTM), 20 (SCVMON), 23 (Gecko), 24 (SSR), 25 (SpecGuard), 28 (UNROCKER). Recovery papers almost always **assume an upstream detector** and add **prediction/state‑reconstruction** + a **recovery controller**.
- **Formal guarantees** are rare and concentrated in control‑theoretic recovery (13, 15), physics‑based authentication (12), and information‑flow security (21).
- **UAV vs UGV:** Most RV‑recovery papers cover **both** (drones + rovers). Pure‑UAV: 14, 20, 22, 28. AV/car (UGV‑adjacent): 1, 4, 9, 27. Medical/other CPS (neither): 2, 7, 12, 19.
- **Two papers are off the core sensor‑attack theme:** 14 (misconfiguration, not adversarial) and 17 (broad robotics survey) — rated 2 accordingly.
