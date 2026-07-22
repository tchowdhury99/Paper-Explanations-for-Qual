# Research Paper Lineage and Systematic Synthesis

## Security, monitoring, attack recovery, and resilient control for cyber-physical systems and robotic vehicles

Prepared from the PDFs in `/Users/tchowdh4/Documents/Study/Research/Research Papers/`.

## 1. Scope and reading notes

The folder contains 30 PDF files. After content-level checking:

- 28 are unique research papers.
- Two files named **Software Availability Protection in Cyber Physical Systems** and **Software Availability Protection in Cyber-Physical Systems** have identical extracted content. They are treated as one paper (Gecko).
- **Event Confirmation_Itinerary - Ascend.pdf** is a one-page travel/event itinerary, not a research paper, and is excluded from the scientific synthesis.

The papers span 2014-2025 and form several overlapping research streams:

1. Physical and sensor attack construction.
2. Attack and anomaly detection.
3. Recovery after attack detection.
4. Robust motion planning without an explicit detector.
5. Runtime monitoring that combines formal knowledge and data.
6. Software availability and live reconfiguration.
7. Surveys that organize the field.

### How to interpret the lineage

Two kinds of connections are used below:

- **Direct connection:** the later paper explicitly cites, extends, compares against, or states a limitation of the earlier work.
- **Conceptual connection:** the later paper addresses the same technical gap, but the relationship is an inference from the papers rather than an explicit author claim.

The phrase “paper B came after paper A” therefore does not always mean B is a formal sequel. It means either a documented citation/extension or a clearly related response to an earlier limitation.

## 2. Executive synthesis

The central evolution in this collection is from **detecting compromised behavior** to **continuing safe and useful operation after compromise**.

- Early work showed that physical laws and temporal logic can expose attacks. **PyCRA** authenticated active sensors through physical challenge-response, **Anomaly Detection in CPS** learned interpretable Signal Temporal Logic (STL) rules, and **RIDS** exploited nonlinear robot dynamics to detect and localize sensor and actuator attacks.
- Attack papers then showed that the problem is harder than simple anomaly detection. **Injected and Delivered** demonstrated controllable inertial-sensor spoofing, **Adv-LiDAR** made LiDAR spoofing semantically effective against an ML perception pipeline, and **Stealthy Attacks** showed that threshold-based control detectors can be evaded by small accumulated deviations.
- The recovery line began with **Software-based Realtime Recovery (SSR)**, which replaced attacked sensor readings with learned software sensors. Its main limitation was model drift: it offered a temporary recovery window rather than indefinite mission completion.
- Later work specialized recovery in different directions:
  - **PID-Piper** improved nonlinear robotic-vehicle recovery and mission completion under overt and stealthy attacks.
  - **LQR recovery** added formal safety deadlines, smooth control, checkpointing, state reconstruction, and a maintenance period, but assumed linear dynamics.
  - **Real-Time Data-Predictive Recovery** generalized the formal recovery idea to nonlinear systems through nonlinear reachability, local linearization, and model predictive control.
  - **SeqRec** offered a learned sequence-to-sequence alternative when an accurate explicit model is difficult to obtain.
  - **SCVMON** moved below the sensor abstraction and recovered safety-critical program variables, covering both sensor attacks and parameter attacks.
  - **DeLorean** added diagnosis so that only compromised sensors are reconstructed, avoiding the disruption caused by worst-case “all sensors attacked” recovery.
  - **SpecGuard** added a missing semantic objective: recovery must obey mission specifications, not merely avoid an immediate crash or return to a set point.
- A parallel detector-free line integrated denoising directly into motion planning. **GAN-MP** corrected attacked robot localization, while **DAE-Crit-LSTM** extended the idea to variable-size, multi-robot environments and deceptive obstacle positions.
- A monitoring line combined formal specifications with learning. The 2014 STL paper supplied interpretability; the 2022 robustness-testing paper showed that ML monitors can be fragile; and the 2024 hybrid monitor paper showed that domain knowledge plus data improves accuracy, transparency, and robustness.
- The newest papers expand the target of recovery. **Nyctea** rectifies flight-control misconfiguration rather than only malicious sensor input, and **Gecko** recovers software execution and disables exploited features to preserve real-time software availability.

The collection therefore moves through this progression:

> attack feasibility -> detection -> stealth-aware detection -> temporary recovery -> mission-completing recovery -> multi-sensor diagnosis -> specification-aware recovery -> software and configuration availability

## 3. High-level research tree

```text
Cyber-physical and robotic-system security
|
+-- A. Physical attack feasibility
|   |
|   +-- PyCRA (2015): authenticate active sensors using physical probes
|   |
|   +-- Injected and Delivered (2018): controllable acoustic injection into MEMS IMUs
|   |   |
|   |   +-- ESP Spoofing (2022): covert, music-carried attack on vehicle gyroscopes
|   |   +-- Un-Rocking Drones (2023): recover attacked IMU signals with a DAE
|   |   +-- UAV online classification (2025): classify several attacked sensor types
|   |
|   +-- Adv-LiDAR (2019): optimize point injection against LiDAR ML perception
|       |
|       +-- Invisible for Camera and LiDAR (2021): defeat camera-LiDAR fusion
|       +-- TileMask (2023): passive adversarial objects against radar DNNs
|
+-- B. Detection and runtime monitoring
|   |
|   +-- Anomaly Detection in CPS with STL (2014)
|   |   |
|   |   +-- Hybrid knowledge/data runtime monitors (2024 issue; online 2023)
|   |       |
|   |       +-- Robustness testing of ML monitors (2022 workshop precursor/companion)
|   |       +-- SpecGuard (2024): STL becomes a recovery objective
|   |
|   +-- RIDS (2017): detect, identify, and quantify sensor/actuator attacks
|   |   |
|   |   +-- Recovery papers answer RIDS's open “response after detection” problem
|   |
|   +-- Stealthy Attacks (2021 journal; earlier conference line)
|       |
|       +-- PID-Piper (2021): lower-error nonlinear model and resilient FFC
|       +-- DeLorean (2024): coordinate detection, diagnosis, and recovery
|
+-- C. Recovery and resilient control
|   |
|   +-- Software-based Realtime Recovery / SSR (2020)
|   |   |
|   |   +-- PID-Piper (2021): mission completion and stealth resilience
|   |   +-- SCVMON (2023): program-variable recovery; includes parameter attacks
|   |   +-- DeLorean (2024): targeted reconstruction under multi-sensor attacks
|   |   +-- SpecGuard (2024): specification-aware safe recovery
|   |   +-- Nyctea (2025): online correction of control misconfiguration
|   |
|   +-- LQR real-time recovery (2021)
|       |
|       +-- Real-Time Data-Predictive Recovery (2023): nonlinear MPC/reachability
|       +-- DeLorean (2024): diagnosis removes worst-case all-sensor isolation
|       +-- SpecGuard (2024): mission constraints replace set-point-only objectives
|   |
|   +-- SeqRec (2023): learned sequence-predictive state and control recovery
|   +-- Recovery Survey (2024): shallow/deep recovery taxonomy
|
+-- D. Detector-free robust motion planning
|   |
|   +-- GAN-MP (2023): correct attacked self-localization before planning
|       |
|       +-- DAE-Crit-LSTM (2024): multi-robot, variable obstacle count,
|           and deceptive obstacle positions
|
+-- E. System/software-level assurance
|   |
|   +-- Secure Autonomous CPS via information-flow control (2018)
|   +-- Robotics Cybersecurity Survey (2022; online 2021)
|   +-- Gecko software availability (2025): isolate, reset, and degrade gracefully
|   +-- Nyctea (2025): detect and rectify configuration-induced instability
```

## 4. Chronological map

| Year | Paper | Main contribution | Main limitation that matters later |
|---|---|---|---|
| 2014 | Anomaly Detection in CPS: A Formal Methods Approach | Learns interpretable STL descriptions of normal behavior from unlabeled data | Offline search is expensive; evaluated only on simulated examples; detection is not recovery |
| 2015 | PyCRA | Uses randomized physical probes to authenticate active sensors in the analog domain | Requires an active sensor, a usable physical model, and controllable probing |
| 2017 | RIDS / Exploiting Physical Dynamics | Detects, identifies, and quantifies actuator and sensor attacks in nonlinear robots | Assumes at least one sensor remains clean; distinguishes neither faults from attacks nor response actions |
| 2018 | Injected and Delivered | Demonstrates practical, controllable acoustic injection into MEMS inertial sensors | Primarily exposes the attack and discusses mitigations; does not recover mission-quality signals |
| 2018 | Secure Autonomous CPS | Vertical hardware/software information-flow architecture with input verification | Prototype scope and attack coverage are narrow; secure processor was not fully integrated |
| 2019 | Adv-LiDAR | Optimization plus global sampling makes LiDAR spoofing survive an ML detector | Single LiDAR modality; limited spoofing angle/points; much evaluation is synthesized/simulated |
| 2020 | Software-based Realtime Recovery (SSR) | Software sensors temporarily replace attacked physical sensors | Prediction drift limits recovery duration; linear model struggles with nonlinear RVs and stealthy attacks |
| 2021 | Stealthy Attacks | Shows CI/EKF threshold and window detectors can be evaded | Attack paper, not a defense; motivates lower-error models and integrated recovery |
| 2021 | PID-Piper | ML feed-forward controller recovers RVs and completes missions | Misses attacks near mission end; tested mainly on GPS/gyro; model itself can be attacked |
| 2021 | LQR Real-time Recovery | Smooth, deadline-aware, formally safe recovery and maintenance | Relies on an LTI model and conservative reachability; assumes reliable detection/diagnosis |
| 2021 | Invisible for Camera and LiDAR | Physical 3D adversarial object defeats multi-sensor fusion | No full-scale end-to-end real-AV test; rule-based fusion emphasized; DNN fusion left open |
| 2022 | ESP Spoofing | Covert acoustic attack on vehicle ESP gyroscope plus neural defense | Semi-physical simulation rather than real-vehicle validation |
| 2022 | Robustness Testing of ML Monitors | Shows domain knowledge reduces robustness error under noise/FGSM | Preliminary, limited to artificial pancreas systems and two perturbation families |
| 2022 | Robotics Cybersecurity Survey | Broad taxonomy of vulnerabilities, attacks, and countermeasures | Breadth comes at the cost of experimental depth and explicit recovery lineage |
| 2023 | GAN-MP | Detector-free correction of localization attacks before motion planning | Simulation only; single robot/self-localization focus |
| 2023 | Real-Time Data-Predictive Recovery | Extends formal recovery to nonlinear systems using MPC and reachability | Computational/modeling complexity; still depends on detection and identification of bad sensors |
| 2023 | SeqRec | Two Seq2Seq models reconstruct state and generate a recovery sequence | Needs trustworthy history, accurate attack timing/sensor diagnosis, and substantial compute |
| 2023 | SCVMON | Monitors/reconstructs safety-critical program variables across sensor and parameter attacks | Static analysis/manual setup may not scale; some severe multi-input cases permit safety but not full mission continuation |
| 2023 | TileMask | Low-cost passive-reflection attack on radar DNNs | Vehicle-specific objects and radar-only focus; sensor fusion can mitigate it |
| 2023 | Un-Rocking Drones | Denoising autoencoder recovers IMU signals under acoustic injection | Tight latency constraints, attack-family focus, and dependence on representative training data |
| 2024 | Hybrid Knowledge/Data Runtime Monitors | Combines STL/domain knowledge with data-driven optimization | Rule completeness can be hard; ML branch needs balanced data and more runtime resources |
| 2024 | DAE-Crit-LSTM Multi-Robot Planning | Corrects deceptive obstacle positions with variable obstacle count | Simulation only; position attacks only; real platforms and velocity attacks remain open |
| 2024 | DeLorean | Unifies detection, causal diagnosis, and targeted recovery under multi-sensor attacks | Needs attack-free historical states and assumes a benign starting zone; obstacles are out of scope |
| 2024 | SpecGuard | Deep-RL recovery that explicitly satisfies STL mission specifications | Single-sensor threat model in the main design; policy fine-tuning and multi-sensor scaling remain difficult |
| 2024 | Recovery Survey | Defines physical-state recovery and separates shallow from deep recovery | Survey ends with work through 2023; it does not evaluate one unified implementation |
| 2025 | UAV Sensor Attack Online Classification | Lightweight multi-class attack classifier integrated in UAV firmware | Online accuracy falls to 74%; training uses simulated/software-injected attacks; adversarial robustness is open |
| 2025 | Nyctea | Detects instability and iteratively reconfigures flight controllers online | Black-box agent, manual parameter selection, and rapidly growing training/action space |
| 2025 | Gecko | Recovers exploited software while disabling unsafe features and meeting deadlines | Cyber-physical state inconsistency and custom shadow-mode engineering remain substantial |

## 5. Direct “limitation -> later paper” connections

This table is the most direct answer to “what limitation caused another paper to come into place.” “Direct” means the later paper explicitly discusses or compares with the earlier line. “Conceptual” means the later paper addresses the gap, but is not necessarily a formal sequel.

| Earlier paper or line | Limitation exposed | Later paper | What the later paper changes | Connection |
|---|---|---|---|---|
| Anomaly Detection with STL (2014) | Classical ML anomaly boundaries are hard to interpret, but pure formal rules may be difficult to specify and optimize | Hybrid Knowledge/Data Runtime Monitors (2024) | Uses formal unsafe-context rules plus data-driven threshold/refinement methods | Direct citation and conceptual continuation |
| Hybrid/ML monitor line | High clean-data accuracy does not imply robustness to noise or adversarial inputs | Robustness Testing (2022) | Tests Gaussian and FGSM perturbations and adds semantic/domain loss | Direct same-lab precursor/companion line |
| RIDS (2017) | Detects, identifies, and quantifies attacks but explicitly leaves response/recovery as future work | SSR (2020), LQR (2021), and later recovery papers | Replace or reconstruct compromised feedback and actively steer the plant to safety | Conceptual; RIDS is also cited by later work |
| Injected and Delivered (2018) | Shows practical control of inertial-sensor output but does not provide persistent mission-preserving recovery | Un-Rocking Drones (2023) | Models the attack residue as noise and reconstructs benign IMU signals with a denoising autoencoder | Direct attack-to-defense progression |
| Adv-LiDAR (2019) | Studies LiDAR alone; production AVs use sensor fusion | Invisible for Camera and LiDAR (2021) | Optimizes a physical 3D object to fool camera and LiDAR simultaneously | Direct extension |
| Single-modality/fusion attack line | Radar DNN security and low-cost passive attacks were largely unexplored | TileMask (2023) | Manipulates radar echoes through small passive reflective objects | Conceptual adjacent branch |
| SSR (2020) | Software-sensor drift permits only temporary recovery; a linear model poorly represents nonlinear RVs | PID-Piper (2021) | Uses a learned feed-forward controller to emulate nonlinear controller behavior and complete missions | Direct comparison and stated motivation |
| CI/EKF detection | Thresholds and monitoring windows permit accumulated stealthy deviations | Stealthy Attacks (2021) | Demonstrates automated false-data, delay, and mode-switch attacks | Direct attack analysis |
| Stealthy Attacks (2021) | Existing detectors/recovery models have loose bounds and fail under stealthy manipulation | PID-Piper (2021) | Improves predictive accuracy, lowers the usable threshold, and supplies a recovery controller | Direct response |
| Early nominal-controller or software-sensor recovery | No formal deadline, smoothness, or safe-maintenance guarantee | LQR Recovery (2021) | Adds checkpointing, reachability-based deadline, constrained LQR, and maintenance control | Direct comparison in the LQR/data-predictive line |
| LQR Recovery (2021) | Recovery model is linear; conservative uncertainty accumulates; nonlinear CPSs are poorly represented | Data-Predictive Recovery (2023) | Uses nonlinear reachability, repeated model adaptation, and MPC while exploiting clean sensors | Direct extension by overlapping authors |
| Explicit-model recovery | Accurate system models can be unavailable or costly for complex nonlinear systems | SeqRec (2023) | Learns temporal/spatial dependencies and recovery control sequences from data | Conceptual alternative branch |
| SSR/PID-Piper and sensor-oriented recovery | Covers sensor values, not configuration/parameter attacks; attack types require separate treatment | SCVMON (2023) | Identifies monitored safety-critical variables in the control program and recovers at the variable level | Direct stated gap |
| LQR checkpoint recovery | Assumes all sensors are attacked and ignores clean real-time feedback, producing aggressive or conservative maneuvers | DeLorean (2024) | Diagnoses the attacked subset and reconstructs only affected states/sensors | Direct comparison and extension |
| SSR/PID-Piper | Approximation error grows sharply under simultaneous heterogeneous sensor attacks | DeLorean (2024) | Adds causal diagnosis and targeted multi-sensor recovery | Direct comparison |
| Earlier recovery systems | Optimize crash prevention, set-point return, or mission completion but ignore formal mission constraints | SpecGuard (2024) | Converts mission specifications into STL-based Deep-RL rewards and trains a compliant recovery policy | Direct stated gap |
| DeLorean (2024) | State reconstruction bounds attack perturbation, but recovery objective itself is not specification-aware | SpecGuard (2024) | Reuses state reconstruction to make adversarial policy training practical and adds mission-spec compliance | Direct reuse/extension |
| GAN-MP (2023) | Single robot, attacked self-localization, and a fixed/simple obstacle formulation | DAE-Crit-LSTM (2024) | Handles deceptive positions of surrounding agents and a variable number of obstacles in multi-robot settings | Direct citation and extension |
| Detection/recovery papers | Generally say “attack/no attack,” making sensor-specific mitigation difficult | UAV Online Classification (2025) | Produces a multi-class sensor-attack label in firmware | Conceptual system-integration continuation |
| Attack recovery as sensor/control correction | Instability may be caused by valid-but-inappropriate configurations, not a sensor attack | Nyctea (2025) | Detects instability and iteratively modifies flight-control parameters | Directly adjacent scope expansion |
| CPS recovery focused on physical state | Crashing or restarting after a software exploit destroys availability and may miss real-time deadlines | Gecko (2025) | Compartmentalizes features, identifies the attack input, selectively resets state, and degrades gracefully | Conceptual expansion to software availability |

## 6. Comparative view: what each family is good at and what it lacks

### 6.1 Attack construction papers

| Paper | Particularly good at | Lacks or leaves open |
|---|---|---|
| PyCRA | Sensor-level authentication grounded in unavoidable physical delay and private randomness | General passive sensors, cheap probing for every modality, and recovery beyond the modeled signal |
| Injected and Delivered | Practical analysis of aliasing, sample-rate drift, phase pacing, and cross-device generality | Persistent recovery and a closed-loop defense validated across missions |
| Adv-LiDAR | Connecting physical point injection to semantically meaningful ML perception outcomes | Multi-sensor fusion and full real-world end-to-end driving validation |
| Stealthy Attacks | Demonstrating why threshold/window detectors fail even when large attacks are detected | A defense or recovery controller |
| Invisible for Camera and LiDAR | Breaking the assumption that independent sensors provide security through fusion | Full-scale real-vehicle testing and broad DNN-fusion evaluation |
| ESP Spoofing | Covert carrier design and an application-specific ESP consequence | Real-vehicle attack/defense validation |
| TileMask | Low-cost, passive, concealable radar attack with physical experiments | Universal objects and resistance to camera/LiDAR fusion |

### 6.2 Detection and monitoring papers

| Paper | Particularly good at | Lacks or leaves open |
|---|---|---|
| STL Anomaly Detection | Human-readable anomaly rules learned without labeled attacks | Scale, online training cost, realistic deployment, and response actions |
| RIDS | Nonlinear dynamics, stochastic noise, attack localization, and attack-vector quantification | All-sensor compromise, fault/attack distinction, and response |
| Hybrid Runtime Monitors | Accuracy/interpretability balance and early hazard prediction | Complete rule elicitation, sensor integrity, and integrated recovery |
| Robustness Testing | Revealing that a high-performing ML monitor may be brittle; showing benefit of semantic loss | Broad CPS validation and richer adaptive attack models |
| UAV Online Classification | Lightweight multi-class labels usable by sensor-specific mitigation | Lower online accuracy and sim-to-real/adversarial generalization |

### 6.3 Recovery papers

| Paper | Particularly good at | Lacks or leaves open |
|---|---|---|
| SSR | Generic, lightweight, temporary substitution for multiple attacked sensors | Long-duration recovery, nonlinear fidelity, stealth resistance |
| PID-Piper | Nonlinear RV behavior, mission completion, and overt/stealthy attack handling | Broad sensor coverage, attacks late in a mission, and ML-model assurance |
| LQR Recovery | Formal safety deadline, smoothness, reachability, and post-recovery maintenance | Nonlinear generality and diagnosis of the attacked subset |
| Data-Predictive Recovery | Nonlinear recovery with clean-sensor feedback and repeated MPC adaptation | Heavy modeling/computation and dependency on correct diagnosis |
| SeqRec | Learned temporal/spatial recovery when explicit dynamics are difficult | Trustworthy history, computation, hidden attacks, and multi-sensor scaling |
| SCVMON | Broad data-oriented attack coverage and low overhead at the program-variable level | Static-analysis scalability, setup automation, and full mission continuation in extreme cases |
| DeLorean | Integrated detection, causal diagnosis, selective state reconstruction, and multi-sensor attacks | Dependence on attack-free history and simplified mission environment |
| SpecGuard | Explicit mission safety semantics and high recovery/compliance rates | Multi-sensor threat scaling, training cost, and policy adaptation to new specifications |
| Un-Rocking Drones | Attack-specific signal recovery without supplementary sensors | General attack types, strict latency, training distribution, and platform breadth |

### 6.4 Detector-free robust planning

| Paper | Particularly good at | Lacks or leaves open |
|---|---|---|
| GAN-MP | One model works in benign and attacked localization without a detector | Real-world validation and multi-agent/variable-obstacle treatment |
| DAE-Crit-LSTM | Variable number of obstacles, multi-robot deception, low overhead | Real robots, other deceptive state variables, and guarantees outside training distribution |

### 6.5 Software/system assurance

| Paper | Particularly good at | Lacks or leaves open |
|---|---|---|
| Secure Autonomous CPS | End-to-end security architecture combining hardware, language, and input verification | Complete integration and broad attack evaluation |
| Nyctea | On-the-fly parameter repair for configuration-induced instability | Interpretability, automated parameter selection, and scalable training |
| Gecko | Preserving both liveness and timeliness after software exploitation | Cyber-physical state consistency and reusable safe shadow compartments |

## 7. Systematic paper-by-paper summaries

### P01. Anomaly Detection in Cyber-Physical Systems: A Formal Methods Approach (2014, IEEE CDC)

**Motivation.** Model-based CPS security often assumes a known linear plant, while generic ML anomaly detectors produce opaque decision surfaces. The authors seek an unsupervised, model-free detector whose result can be read and reasoned about by humans.

**Method.** The paper mines an inference fragment of Signal Temporal Logic from unlabeled normal traces. Parameterized formulas are searched and optimized using STL robustness as a fitness measure. A trajectory that violates the learned formula is anomalous; the same formula can be evaluated incrementally as an online monitor.

**Results.** On a synthetic linear system, the learned rule had zero training error and 1% test misclassification. On the train-brake model, the learned rule perfectly separated the available normal and attacked traces. Offline inference took roughly 130-154 seconds on the reported machine.

**Strength.** It makes the detector's semantics explicit and supports early warning through temporal robustness. **Limitation.** The case studies are simulated, the search is costly, and the method assumes attacks are rare and qualitatively different from normal behavior. It detects but does not recover. **Connections.** It is a conceptual and cited foundation for later STL/context-aware monitors and for specification-aware recovery in SpecGuard.

### P02. PyCRA: Physical Challenge-Response Authentication for Active Sensors Under Spoofing Attacks (2015, ACM CCS)

**Motivation.** Encryption after analog-to-digital conversion cannot repair a physical signal that was already manipulated before digitization.

**Method.** PyCRA randomly changes an active sensor's physical probe and checks whether the observed environment response obeys the expected physical model, delay, and noise constraints. Case studies use magnetic encoders and RFID.

**Results.** More than 90 hardware experiments demonstrate attack detection, attack-spectrum estimation/resilience for a magnetic encoder, and RFID eavesdropping detection. Higher sampling rates reduce the physical delay needed for detection.

**Strength.** Security is rooted in unavoidable physics rather than only software or redundancy. **Limitation.** It needs an active sensor, controllable probes, and a sufficiently accurate model; it is not a universal solution for passive sensing or every eavesdropping channel. **Connections.** RIDS and later physical-sensor papers cite this line; it establishes the broad principle that physical behavior itself can authenticate sensing.

### P03. Exploiting Physical Dynamics to Detect Actuator and Sensor Attacks in Mobile Robots / RIDS (2017 preprint)

**Motivation.** Cyber-only IDSs miss attacks introduced through physical channels, and redundancy voting fails when too many sensors are corrupted.

**Method.** RIDS uses nonlinear unknown-input/state estimation with multiple hypotheses about which sensors are clean. It tracks stochastic noise propagation, detects inconsistent physical behavior, identifies attacked sensor/actuator workflows, and estimates attack vectors.

**Results.** Across 20 Khepera robot experiments, average false-positive and false-negative rates were below 1%, with mean detection delay within 0.40 seconds, provided at least one of three sensors remained clean.

**Strength.** It handles nonlinear mobile-robot dynamics and both sensor and actuator attacks while quantifying attack magnitude. **Limitation.** At least one suitable sensor must remain clean; the method does not separate fault from attack and stops short of response. **Connections.** Its explicit future-work call for intrusion response is one of the clearest motivations for the later recovery line.

### P04. Injected and Delivered: Fabricating Implicit Control over Actuation Systems by Spoofing Inertial Sensors (2018, USENIX Security)

**Motivation.** Earlier acoustic-injection demonstrations did not account well for ADC sample-rate drift and therefore rarely achieved stable control over real embedded systems.

**Method.** The authors analyze out-of-band injection, aliasing, sample-rate drift, digital amplitude adjustment, and phase pacing. They use these effects to manipulate gyroscope/accelerometer output and derived motion state.

**Results.** Acoustic signals affected 23 of 25 tested devices; 17 were susceptible to adversarial implicit control. Demonstrations span robots, a gyroscopic spoon, a 3D mouse, phones, and other closed/open-loop systems.

**Strength.** It turns what looked like uncontrolled noise into a repeatable control primitive and shows broad device impact. **Limitation.** It is primarily an attack study; damping/filtering/sampling defenses are discussed but persistent sensor-value recovery is not delivered. **Connections.** ESP Spoofing specializes the threat to vehicle stability control; Un-Rocking Drones directly addresses practical sensor recovery.

### P05. Secure Autonomous Cyber-Physical Systems Through Verifiable Information Flow Control (2018, CPS-SPC)

**Motivation.** Networked autonomous vehicles may allow low-integrity or compromised information to influence safety-critical control.

**Method.** A vertically integrated architecture combines verified hardware, language-based information-flow control in Jif, and online verification of inputs such as maps. A robotic lane-following testbed demonstrates that untrusted data cannot directly affect trusted decisions.

**Results.** Malicious map data was prevented from causing adverse behavior. Hardware evaluation estimated about 12.4% average performance overhead and 16.5% additional FPGA LUT use; software annotations were manageable.

**Strength.** It offers architectural assurance rather than another anomaly threshold. **Limitation.** The secure processor was not fully integrated with the robot, and the prototype covers a limited attack surface. **Connections.** It is complementary to sensor-recovery work: it limits bad information flow, whereas later recovery systems repair state/control after bad data has already affected operation.

### P06. Adversarial Sensor Attack on LiDAR-based Perception in Autonomous Driving (2019, ACM CCS)

**Motivation.** Prior perception-security work focused on cameras or raw LiDAR injection, not whether injected points survive an industry-grade ML object detector and alter driving decisions.

**Method.** The paper models practical LiDAR point-injection constraints, formulates an adversarial optimization objective against Baidu Apollo, and combines local optimization with global sampling to produce a front-near fake obstacle.

**Results.** Blind spoofing was largely ineffective. With about 60 reliably injected points, the improved method reached roughly 75% success and improved attack success by 2.65x on average. Simulated emergency-braking and vehicle-freezing scenarios changed Apollo decisions.

**Strength.** It connects physical injection, ML perception, and vehicle-level consequences. **Limitation.** It studies one modality, has limited physical injection capability, and uses synthesized traces/simulation for important end-to-end steps. **Connections.** The 2021 multi-sensor fusion paper explicitly expands the attack from LiDAR alone to camera plus LiDAR.

### P07. Software-based Realtime Recovery from Sensor Attacks on Robotic Vehicles (2020, RAID)

**Motivation.** Detection or fail-safe shutdown prevents immediate damage but does not let a robotic vehicle remain controllable after a sensor attack.

**Method.** Generic system identification builds a predictive state-space model. “Software sensors” translate predicted state into virtual measurements and individually replace attacked physical sensors. Historical error and supplementary measurements correct drift.

**Results.** The prototype was evaluated on six vehicle systems, including a real quadrotor and rover, under single and multiple sensor attacks. Space overhead was at most 1.3%; runtime overhead was at most 6.9% for one sensor and 8.8% for multiple sensors. Most recovery windows lasted at least 10 seconds.

**Strength.** It is a practical and generic first recovery layer with modest overhead. **Limitation.** Approximation error accumulates, so software sensors cannot replace hardware indefinitely; the linear model is weak for nonlinear dynamics and stealthy attacks. **Connections.** PID-Piper directly targets these limitations; SCVMON, DeLorean, SpecGuard, Nyctea, and Gecko all compare with or build beyond this recovery idea.

### P08. Stealthy Attacks against Robotic Vehicles Protected by Control-based Intrusion Detection Techniques (2021 journal version)

**Motivation.** CI and EKF detectors need thresholds and monitoring windows to tolerate noise and modeling error. Those tolerances themselves become an attack surface.

**Method.** The paper automates reverse engineering of detector thresholds and constructs three attacks: slow false-data injection, intermittent artificial delay, and mode-switch attacks timed when prediction error is naturally high.

**Results.** Eight robotic-vehicle systems, including three real vehicles, were attacked without triggering the target detectors. Effects included 8-15 m deviation on 35-50 m missions, mission-time increases of 30-68%, more than 160 m deviation in one setting, and landing crashes.

**Strength.** It demonstrates that “undetected” does not mean “harmless,” even for very small per-step manipulation. **Limitation.** It supplies no direct defense. **Connections.** PID-Piper explicitly responds with a lower-error nonlinear model and resilient controller; DeLorean incorporates stealth-detection delay into diagnosis and recovery.

### P09. PID-Piper: Recovering Robotic Vehicles from Physical Attacks (2021, IEEE/IFIP DSN)

**Motivation.** Fail-safe responses abort missions, while SSR's linear feedback model has low mission success and is vulnerable to stealthy attacks in nonlinear vehicles.

**Method.** PID-Piper learns an LSTM-based feed-forward controller (FFC) that emulates the vehicle's PID controller from state to actuator command. The FFC monitors the primary controller and takes over under attack, avoiding direct dependence on attacked feedback in the same way as a feedback controller.

**Results.** On six systems (three real, three simulated), it produced zero false positives in benign experiments, completed missions in over 83% of overt-attack cases, limited stealthy-attack impact by a factor of seven, achieved 100% mission success under the tested stealthy attacks, and incurred under 7% performance overhead.

**Strength.** It focuses on useful mission completion, not just crash prevention. **Limitation.** About 17% of overt cases still fail, especially attacks near mission end; main tests target GPS/gyroscope; the ML controller has an adversarial surface. **Connections.** DeLorean shows its model error grows under multi-sensor attacks; SpecGuard shows that mission completion alone does not prove specification-compliant safety.

### P10. Real-time Attack-recovery for Cyber-physical Systems Using Linear-quadratic Regulator (2021, ACM TECS/EMSOFT)

**Motivation.** Existing sensor isolation/nominal-control responses do not guarantee that recovery finishes before an unsafe state, proceeds smoothly, or remains safe after reaching the target.

**Method.** A sliding-window checkpointer stores trustworthy history; a state reconstructor rolls it forward; reachability analysis computes a conservative safety deadline; constrained LQR solved with ADMM generates a smooth recovery and maintenance sequence.

**Results.** Fifteen scenarios across five CPS simulators and three sensor-attack types show timely safe recovery, smoother trajectories, and better maintainability than baselines.

**Strength.** It gives a rigorous definition of deep recovery with timing and safety constraints. **Limitation.** The core plant model is linear time-invariant, uncertainty estimates can be conservative, and attacked-sensor diagnosis is assumed rather than solved. **Connections.** Data-Predictive Recovery is a direct nonlinear extension; DeLorean adds diagnosis to avoid all-sensor worst-case recovery; SpecGuard adds mission semantics.

### P11. Invisible for Both Camera and LiDAR: Security of Multi-Sensor Fusion based Perception under Physical-World Attacks (2021, IEEE S&P)

**Motivation.** Production autonomous-driving systems assume sensor fusion is secure as long as all modalities cannot be attacked simultaneously.

**Method.** MSF-ADV optimizes a physically printable adversarial 3D object's shape against camera and LiDAR pipelines, including differentiable approximations for non-differentiable sensing and voxel/cell aggregation.

**Results.** The attack exceeded 90% success across objects and fusion algorithms, showed around 75% transfer success, achieved 85-90% success in miniature physical tests, and caused 100% collisions in the simulator. Input transformations offered weak trade-offs; augmented training lowered attack success but also reduced clean mAP.

**Strength.** It directly breaks the main security assumption behind multi-sensor redundancy. **Limitation.** No full-size end-to-end attack on a real vehicle; industry rule-based fusion is emphasized and DNN fusion is left open. **Connections.** It is the direct multi-modal successor to single-camera/LiDAR attacks and a warning that “add another sensor” is not a complete defense for TileMask or sensor recovery.

### P12. ESP Spoofing: Covert Acoustic Attack on MEMS Gyroscopes in Vehicles (2022, IEEE TIFS)

**Motivation.** Vehicle Electronic Stability Programs depend on MEMS gyroscopes, but their acoustic susceptibility had not been studied as a covert vehicle-control attack.

**Method.** Frequency sweep finds resonance, then attack signals are embedded in ordinary audio through superposition to reduce perceptibility. Carsim/Simulink co-simulation evaluates vehicle consequences; a neural detector and physical shell are examined as defenses.

**Results.** The attack changes ESP-relevant gyroscope signals and vehicle trajectories in a semi-physical setup. A protective shell reduced attack amplitude by about 49.19%; the neural strategy distinguished attack behavior in the tested conditions.

**Strength.** It turns a MEMS vulnerability into an application-specific safety impact while considering stealth and defense. **Limitation.** The final vehicle behavior is evaluated in co-simulation, not a real driven vehicle. **Connections.** It specializes the acoustic-injection principles demonstrated in Injected and Delivered.

### P13. Robustness Testing of Data and Knowledge Driven Anomaly Detection in CPS (2022, DSN Workshops)

**Motivation.** ML monitors may achieve good accuracy on normal test data while failing under accidental noise or adversarial perturbations.

**Method.** Gaussian perturbations and FGSM attacks are applied to MLP/LSTM safety monitors for two artificial-pancreas simulators. Semantic/domain constraints are incorporated into the training loss.

**Results.** Knowledge-guided monitors reduced robustness error by up to an average 54.2% while preserving high F1 scores. Baseline LSTMs were more vulnerable than MLPs, but knowledge-customized LSTMs were the most robust among the tested models.

**Strength.** It evaluates the monitor as an attack surface and shows that domain knowledge can improve both robustness and transparency. **Limitation.** It is preliminary, limited to two medical simulators and two perturbation mechanisms. **Connections.** It cites the under-review knowledge/data monitor work and complements the final 2024 hybrid-monitor paper.

### P14. Robotics Cyber Security: Vulnerabilities, Attacks, Countermeasures, and Recommendations (2022 issue; online 2021)

**Motivation.** Robotics combines network, software, sensor, actuator, cloud, and human risks, but the literature was fragmented.

**Method.** Broad survey and taxonomy of vulnerabilities, attack types, application domains, risk assessment, authentication, cryptography, IDS, privacy, and non-cryptographic controls.

**Results.** The paper supplies a wide threat/countermeasure catalog and recommends multi-factor mutual authentication, lightweight security mechanisms, privacy controls, and robot-specific IDS/IPS.

**Strength.** Breadth and system-level context. **Limitation.** It is not an experimental comparison, its recovery discussion is relatively shallow, and its recommendations do not resolve timing/control trade-offs. **Connections.** It provides the umbrella context in which the specialized attack, monitor, and recovery papers sit.

### P15. GAN-Based Robust Motion Planning for Mobile Robots Against Localization Attacks (2023, IEEE RA-L)

**Motivation.** Motion planners assume localization is trustworthy; detector-triggered recovery also creates detector latency and false-positive dependencies.

**Method.** GAN-MP trains a generator to correct attacked positions while leaving benign positions unchanged, then passes the corrected state to any planner. GAN-DRL is the demonstrated instantiation.

**Results.** Under localization attacks, GAN-DRL achieved 96.88% success versus 83.88% for the unprotected DRL planner, while retaining benign performance and generalizing across attack settings in simulation.

**Strength.** A unified path for benign and attacked operation without an explicit detector. **Limitation.** Simulation only, with emphasis on one robot's localization. **Connections.** DAE-Crit-LSTM directly cites and extends this idea to multi-robot deceptive obstacle positions and variable obstacle counts.

### P16. Real-Time Data-Predictive Attack-Recovery for Complex Cyber-Physical Systems (2023, IEEE RTAS)

**Motivation.** LQR recovery's linear model and open-loop uncertainty are inadequate for nonlinear CPSs, while nominal controllers lack recovery deadlines and safety guarantees.

**Method.** Nonlinear reachability with Flow* reconstructs the current set and computes timing bounds. A model adapter repeatedly linearizes/discretizes around current reachable states, and MPC produces updated recovery inputs while clean sensors reduce uncertainty.

**Results.** Nonlinear benchmark experiments show the method reaches and maintains the target before the deadline under cases where SSR/nominal recovery can miss the deadline as detection delay grows.

**Strength.** It retains formal safety/deadline goals while adapting to nonlinear dynamics and feedback from intact sensors. **Limitation.** Flow*, repeated adaptation, and MPC raise computation/modeling cost; the system still assumes compromised sensors have been identified. **Connections.** It is a direct extension of the same authors' LQR recovery line.

### P17. Learn-to-Respond: Sequence-Predictive Recovery from Sensor Attacks in CPS / SeqRec (2023, IEEE RTSS)

**Motivation.** Recovery suffers from accumulated state-estimation error and must act quickly; explicit high-fidelity dynamics may be difficult for complex systems.

**Method.** One Seq2Seq model reconstructs current state from trustworthy history and sensor/control dependencies. A second predicts a future recovery-control sequence conditioned on intact sensors and a target; an MPC-like algorithm applies it.

**Results.** Evaluations cover three numerical CPSs, a high-fidelity simulator, and a four-wheel testbed. Model inference is in the low-millisecond range on the reported setup and supports the testbed's 50 ms control period; recovery is faster than using the original controller in the studied cases.

**Strength.** It supplies a data-driven alternative for nonlinear/unknown dynamics and jointly predicts state and response sequences. **Limitation.** Correct detection time, attacked-sensor identity, trustworthy checkpoints, and adequate computation are critical; hidden attacks and many simultaneously attacked sensors violate assumptions. **Connections.** It complements, rather than simply replaces, model-based LQR/data-predictive recovery.

### P18. SCVMON: Data-oriented Attack Recovery for RVs Based on Safety-critical Variable Monitoring (2023, RAID)

**Motivation.** Existing mechanisms focus on sensor attacks, require extra components, or attach recovery to particular attack inputs. They do not cover configuration-parameter attacks well.

**Method.** Static data-flow analysis identifies variables on paths from inputs to actuator outputs, then narrows them to monitored safety-critical variables (mSCVs). Runtime change monitoring detects malicious effects and reconstructs safe mSCV values rather than separately repairing every input.

**Results.** Monitoring 27 mSCVs detected and recovered 20 data-oriented attack types, including multi-sensor and parameter attacks. Firmware storage overhead was 0.3%; runtime overhead reached about 5.1% during recovery.

**Strength.** Broad attack coverage through a small software-only monitoring surface. **Limitation.** Input/output identification, graph analysis, and threshold setup involve manual work; large programs risk node explosion. In the most severe multi-input case, the vehicle stayed safe for intervention but could not fully continue arbitrary missions. **Connections.** It directly contrasts with SSR/PID-Piper's sensor-oriented scope and points toward Nyctea's configuration focus.

### P19. TileMask: A Passive-Reflection-based Attack against mmWave Radar Object Detection (2023, ACM CCS)

**Motivation.** Active radar spoofing needs expensive, synchronized transmitters and may not fool the downstream DNN; passive adversarial radar objects had not been studied.

**Method.** Small 3D-printed bases with oriented metal tiles change echo amplitudes at important vehicle surfaces. A mixed discrete/continuous optimization chooses object count, position, size, and tile angles.

**Results.** Two low-cost objects concealed as car signs produced about 93% attack success in a representative setting; averages were roughly 88-95% across several physical/digital tests and above 85% on multiple models. A proposed retraining defense reduced average success from 92% to about 34%, still nontrivial.

**Strength.** Practical, passive, low-cost, and physically evaluated. **Limitation.** Objects are not universal across vehicle types; camera/LiDAR fusion can mitigate radar-only attacks; a three-modality attack remains future work. **Connections.** It broadens the perception-attack tree from camera/LiDAR to radar and explicitly recognizes the multi-sensor arms race established by the fusion paper.

### P20. Un-Rocking Drones: Foundations of Acoustic Injection Attacks and Recovery Thereof (2023, NDSS)

**Motivation.** Existing acoustic defenses, detection, damping, or short recovery cannot keep a drone flying to its destination under a persistent attack, especially if supplementary sensors are also compromised.

**Method.** A realistic testbed identifies sampling jitter as the mechanism that spreads out-of-band resonance into the control loop's in-band range. UNROCKER trains denoising autoencoders to remove the resonant component from gyroscope/accelerometer traces without relying on another sensor.

**Results.** Tests across drone models, actual sensors, hardware/software injection, flight traces, shifted resonance, and changing amplitude show recovered errors close to benign ranges. The study identifies strict latency limits: about 24 ms for gyroscope recovery and 2.8 seconds for accelerometer recovery in its experiments.

**Strength.** It explains why low-pass filters and random sampling are insufficient and supplies an attack-specific recovery mechanism with released code/data. **Limitation.** Generality is limited to the learned acoustic-resonance family and representative training data; real-time deployment, especially gyro latency, remains demanding. **Connections.** It is the clearest defensive successor to the inertial acoustic-injection line.

### P21. Hybrid Knowledge and Data Driven Synthesis of Runtime Monitors for CPS (2024 issue, IEEE TDSC; online 2023)

**Motivation.** Pure model/knowledge monitors can be incomplete and conservative, while pure ML monitors need data, lack transparency, and can generalize poorly.

**Method.** Unsafe control contexts are specified in STL using system-theoretic process analysis. Data from closed-loop simulations then refine/integrate formulas either through context-aware threshold optimization (CAWT) or ML with a custom semantic loss.

**Results.** Across autonomous-driving and artificial-pancreas case studies plus clinical data, monitors improved average F1 by up to 4.7x over strong baselines and often reduced both false positives and false negatives. Filtering alerts by reaction time cut false-positive rates by roughly 44-62% in reported scenarios.

**Strength.** It gives a practical trade-off: STL optimization is transparent and lightweight; the ML branch tolerates incomplete rules and can gain accuracy. **Limitation.** Complete safety-context rules can be difficult; ML needs balanced data and compute; sensor perturbation protection is delegated to other mechanisms. **Connections.** It grows from the interpretable STL anomaly line, is stress-tested by the 2022 robustness paper, and points toward context-specific recovery.

### P22. Robust Motion Planning for Multi-Robot Systems Against Position Deception Attacks / DAE-Crit-LSTM (2024, IEEE TIFS)

**Motivation.** Multi-robot planners assume a fixed number of obstacles and trustworthy received positions; GAN-MP mainly corrected a robot's own localization.

**Method.** A denoising autoencoder corrects benign or attacked obstacle positions without a detector. An LSTM-based criticality-guided DRL planner handles a variable number of surrounding agents.

**Results.** Under position deception, success increased from 77.5% to 98.1%, collision rate fell from 21.8% to 0.4%, and overhead was below 3.9%. It beat the best reported recovery baseline by 8.9 percentage points in a comparison.

**Strength.** Unified benign/attacked operation, variable obstacle count, and low decision overhead. **Limitation.** Simulation only and position deception only; real-robot, velocity deception, and distribution-shift evaluation remain open. **Connections.** It explicitly cites GAN-MP as the single-robot predecessor and adapts the recovery idea to surrounding-agent state.

### P23. Diagnosis-guided Attack Recovery for Securing Robotic Vehicles from Sensor Deception Attacks / DeLorean (2024, ACM AsiaCCS)

**Motivation.** Model-based recovery degrades under simultaneous sensor attacks, while checkpoint recovery assumes all sensors are bad and discards useful clean feedback, causing aggressive or overconservative maneuvers.

**Method.** Factor-graph causal analysis diagnoses which sensors are attacked. DeLorean isolates only those sensors, reconstructs affected states from history, and coordinates detection, diagnosis, and recovery while accounting for stealth-detection delay.

**Results.** Diagnosis achieved 96% true-positive and 5% false-positive rates. Across four real and two simulated RVs, recovery succeeded in 93% of cases; mission success averaged about 94% across different numbers of attacked sensors, with 100% in the tested stealthy attacks and no crashes in reported real-RV recovery.

**Strength.** It bridges model-based and checkpoint-based recovery and tackles multi-sensor attacks explicitly. **Limitation.** It needs an attack-free initial/history window, assumes the attacker cannot control environmental conditions, and excludes obstacles from the tested missions. **Connections.** It directly compares SSR, PID-Piper, and LQR recovery; SpecGuard later reuses its state reconstruction.

### P24. SpecGuard: Specification Aware Recovery for Robotic Autonomous Vehicles from Physical Attacks (2024, ACM CCS)

**Motivation.** Earlier recovery may prevent a crash or return to a point while violating altitude, geofence, collision, timing, or operational-boundary constraints.

**Method.** Mission specifications are formalized in STL and converted into shaped rewards for a Deep-RL recovery policy. DeLorean-style state reconstruction bounds sensor perturbations so adversarial training becomes tractable. Reactive and proactive controller designs are compared.

**Results.** SpecGuard achieved about 92% recovery success, prevented mission-spec violations in 87.5% of tested attacks, produced no crashes/stalls, doubled recovery success and improved specification compliance by 5x versus five prior methods, and incurred about 13.6-15.3% CPU overhead on real vehicles.

**Strength.** It asks the right semantic question: “recovered according to what safety contract?” **Limitation.** The main threat model is single-sensor; two-sensor experiments increased violation rate to 24%. New specifications require policy fine-tuning, and some recoveries miss a 5 m final-position tolerance. **Connections.** It unifies the STL-monitoring branch with the attack-recovery branch and directly builds on DeLorean's reconstruction.

### P25. Recovery from Adversarial Attacks in Cyber-physical Systems: Shallow, Deep, and Exploratory Works (2024, ACM Computing Surveys)

**Motivation.** CPS security literature emphasizes prevention/detection and lacks a stable definition and map of physical-state recovery.

**Method.** The survey defines recovery as restoring physical state to a target condition under attack and reviews 30 papers from 2014-2023. It divides methods into shallow recovery (no dedicated recovery controller) and deep recovery (a dedicated controller), then compares application, attack surface/type, dynamics knowledge, and linearity.

**Results.** Roughly half the surveyed approaches assume linear dynamics; learning-based techniques tend to relax explicit dynamics knowledge. The paper identifies real-time latency, recovery metrics, benchmark scarcity, and cross-layer recovery as open problems.

**Strength.** It provides the best taxonomy for organizing the recovery subset in this folder. **Limitation.** It is a survey, not a unified implementation, and naturally excludes the 2024-2025 papers that arrived after its review window. **Connections.** It explicitly includes SSR, LQR/data-predictive work, SeqRec, and related shallow/deep approaches.

### P26. Sensor Attack Online Classification for UAVs Using Machine Learning (2025, Computers & Security)

**Motivation.** Most UAV defenses either detect one sensor attack or collapse all anomalies into a single class, making sensor-specific mitigation difficult and multiple separate detectors expensive.

**Method.** Simulated attacks target gyroscope, accelerometer, barometer, and GPS. Sensor, flight-state, and control-parameter features are selected through importance/correlation/ablation analysis; several classifiers are compared and the best MLP is embedded in firmware.

**Results.** Offline accuracy was 89.38% with F1 89.83%. Onboard online detection reached about 74% at roughly 0.495 ms per decision using 48 KB storage.

**Strength.** It supplies a lightweight multi-class diagnosis signal that could choose sensor-specific recovery. **Limitation.** Simulation/software injection may not represent physical attacks, online accuracy drops substantially, and adversarial attacks on the classifier are acknowledged but not solved. **Connections.** It cites the attack and recovery papers in this collection and can be viewed as a front end for differentiated mitigation.

### P27. Real-time Rectifying Flight Control Misconfiguration Using Intelligent Agent / Nyctea (2025, ACM TOSEM)

**Motivation.** Flight instability can come from syntactically valid yet inappropriate control parameters; static code checks and attack-specific recovery do not solve this configuration problem.

**Method.** Nyctea monitors consecutive flight-state deviations to detect transition toward instability. A pretrained DDPG agent proposes parameter configurations iteratively until stability returns. It is integrated with ArduPilot and PX4.

**Results.** Detection precision/recall were about 99% in the reported simulations. Nyctea rectified 90.74% of detected ArduPilot and 89.49% of PX4 unstable executions, generating about 4-5 candidate configurations on average; the paper summarizes elimination of over 85% of instability across simulated and practical tests.

**Strength.** It widens “recovery” from malicious input repair to online control reconfiguration and supports different autopilots. **Limitation.** The RL policy is hard to interpret, initial training is expensive, manual parameter selection remains, and action space/training cost grows rapidly with more parameters. **Connections.** It cites SSR, PID-Piper, SCVMON, and related recovery systems but addresses a distinct root cause: configuration-induced instability.

### P28. Software Availability Protection in Cyber-Physical Systems / Gecko (2025, USENIX Security)

**Motivation.** Traditional memory/control-flow defenses often crash a compromised program. In a real-time CPS, that preserves integrity at the expense of availability and can cause physical harm.

**Method.** Gecko compartmentalizes features, attributes malicious inputs, moves to a fail-safe shadow compartment, selectively restores state from snapshots, and uses an I/O reference monitor to avoid costly peripheral reinitialization. The design explicitly combines liveness and deadline/timeliness goals.

**Results.** On ArduPilot, Jackal UGV, and OpenManipulator, Gecko achieved 83.3% task-deadline hits with 8.28% average runtime overhead; the Jackal average was 7.52% in the reported breakdown.

**Strength.** It treats continued timely software execution as a security property and disables the exploited feature instead of repeatedly crashing. **Limitation.** Restoring cyber state can make it inconsistent with physical state; custom shadow compartments require domain expertise; interface privileges and re-enabling inputs remain open. **Connections.** It is the software-availability counterpart to physical-state recovery and exposes a new unification problem: cyber recovery and physical recovery must be consistent.

## 8. Cross-paper conclusions

### 8.1 There is no single best recovery method

The appropriate method depends on what knowledge and assurance are available:

- If a trustworthy explicit linear model and formal guarantees are required, LQR recovery is strongest.
- If dynamics are nonlinear but modeled, Data-Predictive Recovery is stronger, at higher computational cost.
- If explicit dynamics are unavailable but representative data exist, SeqRec or learned FFC/DAE/GAN methods are attractive.
- If only a subset of sensors is attacked, DeLorean's diagnosis prevents unnecessary isolation.
- If the goal includes mission semantics, SpecGuard is the most complete recovery objective in the set.
- If attacks alter program parameters as well as sensors, SCVMON covers a broader data-oriented surface.
- If the root cause is configuration rather than attack, Nyctea is the most directly relevant.
- If the exploit is a software vulnerability and continued execution is the priority, Gecko addresses a different availability layer.

### 8.2 “Good at A, lacks B” patterns

1. **Formal methods are good at guarantees and interpretation, but lack easy scalability.** STL and reachability papers are explainable and safety-oriented, but specification creation and nonlinear reachable-set computation are expensive.
2. **Learning is good at nonlinear approximation, but lacks guarantees and distribution robustness.** PID-Piper, SeqRec, GAN-MP, DAE-Crit-LSTM, UnRocker, and Nyctea often outperform fixed models, but depend on representative data and expose new adversarial surfaces.
3. **Redundancy is good against isolated failures, but lacks security when correlated attacks are possible.** The fusion attack and multi-sensor deception papers repeatedly show this.
4. **Detection is good at raising an alarm, but lacks a safe action policy.** RIDS and monitor papers can identify danger; recovery papers are needed to decide what the plant should do next.
5. **Set-point recovery is good at stabilizing a plant, but lacks mission meaning.** SpecGuard's main contribution is to encode the mission contract explicitly.
6. **Worst-case recovery is good for conservatism, but lacks precision and utility.** DeLorean shows that discarding all sensor feedback can itself create unsafe or inefficient maneuvers.
7. **Software rollback is good at removing corrupted execution state, but lacks cyber-physical consistency.** Gecko makes this limitation explicit.

### 8.3 Most important open research gaps suggested by the collection

1. **Unified detection-diagnosis-recovery guarantees.** Most proofs assume a detector supplies correct attack time and sensor identity, while real detectors have delay, false positives, and hidden attacks.
2. **Cyber-physical state consistency after software recovery.** Snapshot rollback and compartment failover must reconcile software state with a physical plant that continued moving.
3. **Specification-aware multi-sensor recovery.** DeLorean handles multi-sensor diagnosis; SpecGuard handles mission specifications. A system that robustly combines both under realistic obstacles and environmental disturbances is still missing.
4. **Real-world evaluation at scale.** Many strong results use simulation, miniature objects, software injection, or limited robots. Reproducible cross-platform physical benchmarks remain scarce.
5. **Recovery under adaptive and detector-aware adversaries.** Stealthy attacks show attackers exploit thresholds; future attackers can target learned reconstructors, policies, and diagnostic models.
6. **Latency-aware learned recovery on embedded hardware.** UnRocker and SeqRec show that a technically accurate model may still miss a real-time deadline.
7. **Recovery quality metrics beyond “did not crash.”** Mission completion, temporal-logic compliance, time-to-recovery, control smoothness, post-recovery maintenance, and deadline-hit rate should be evaluated together.
8. **Compositional multi-modal perception defense.** Camera, LiDAR, and radar each have physical adversarial attacks; merely fusing them does not establish a security guarantee.
9. **Automatic specification and safety-variable discovery.** STL rule generation, SCV selection, shadow-controller design, and configuration selection still require substantial expert effort.
10. **Confidence-aware graceful degradation.** A future system should continuously express uncertainty and choose among normal control, targeted reconstruction, safe-mode operation, and full recovery rather than making one binary switch.

## 9. A proposed unified architecture suggested by these papers

The collection implicitly points to a layered design:

```text
Physical sensors and perception
        |
        v
Physics-aware validation (PyCRA / RIDS / multi-modal checks)
        |
        v
Context-aware runtime monitor (STL + data)
        |
        v
Attack classification and causal diagnosis (UAV classifier / DeLorean)
        |
        +---------------------------+
        |                           |
        v                           v
Targeted state reconstruction      Software isolation/reset
(SSR / DeLorean / UnRocker)        (Gecko)
        |                           |
        +-------------+-------------+
                      |
                      v
Specification-aware recovery controller
(LQR/Data-Predictive/SeqRec/PID-Piper/SpecGuard)
                      |
                      v
Mission compliance and graceful degradation
```

No one paper implements this entire stack. DeLorean comes closest to integrating detection, diagnosis, and recovery; SpecGuard supplies the richest recovery objective; Gecko adds software availability; hybrid STL/data monitors add early semantic hazard prediction. Their combination is a plausible next research direction, but it must address shared timing, uncertainty, and cyber-physical consistency.

## 10. Suggested reading order

For a coherent study path rather than chronological order:

1. **Robotics Cybersecurity Survey** for the broad threat surface.
2. **Injected and Delivered**, **Adv-LiDAR**, and **Stealthy Attacks** to understand why the attacks are practical and why ordinary detectors fail.
3. **RIDS** and **Anomaly Detection with STL** for physical/model/formal detection principles.
4. **SSR** for the first practical recovery baseline.
5. **PID-Piper** and **LQR Recovery** for the two major learned-versus-formal recovery directions.
6. **Data-Predictive Recovery** and **SeqRec** for nonlinear model-based versus learned sequence recovery.
7. **SCVMON** for program-variable-level recovery.
8. **DeLorean** for multi-sensor diagnosis-guided recovery.
9. **SpecGuard** for mission-specification-aware recovery.
10. **Recovery Survey** to revisit the field taxonomy after seeing the concrete systems.
11. **Hybrid Runtime Monitors** and **Robustness Testing** for monitor design and robustness.
12. **Un-Rocking Drones**, **GAN-MP**, and **DAE-Crit-LSTM** for attack-specific and detector-free learned mitigation.
13. **Nyctea** and **Gecko** for the expansion from sensor attacks to configuration and software availability.

## 11. Corpus inventory

| ID | PDF title (normalized) | Year | Included? | Note |
|---|---|---:|---|---|
| P01 | Anomaly Detection in Cyber-Physical Systems: A Formal Methods Approach | 2014 | Yes | IEEE CDC |
| P02 | PyCRA: Physical Challenge-Response Authentication for Active Sensors Under Spoofing Attacks | 2015 | Yes | ACM CCS |
| P03 | Exploiting Physical Dynamics to Detect Actuator and Sensor Attacks in Mobile Robots | 2017 | Yes | arXiv preprint in folder |
| P04 | Injected and Delivered: Fabricating Implicit Control over Actuation Systems by Spoofing Inertial Sensors | 2018 | Yes | USENIX Security |
| P05 | Secure Autonomous Cyber-Physical Systems Through Verifiable Information Flow Control | 2018 | Yes | CPS-SPC |
| P06 | Adversarial Sensor Attack on LiDAR-based Perception in Autonomous Driving | 2019 | Yes | ACM CCS |
| P07 | Software-based Realtime Recovery from Sensor Attacks on Robotic Vehicles | 2020 | Yes | RAID |
| P08 | Stealthy Attacks against Robotic Vehicles Protected by Control-based Intrusion Detection Techniques | 2021 | Yes | Digital Threats journal version |
| P09 | PID-Piper: Recovering Robotic Vehicles from Physical Attacks | 2021 | Yes | IEEE/IFIP DSN |
| P10 | Real-time Attack-recovery for CPS Using Linear-quadratic Regulator | 2021 | Yes | ACM TECS/EMSOFT |
| P11 | Invisible for Both Camera and LiDAR | 2021 | Yes | IEEE S&P |
| P12 | ESP Spoofing | 2022 | Yes | IEEE TIFS |
| P13 | Robustness Testing of Data and Knowledge Driven Anomaly Detection in CPS | 2022 | Yes | DSN Workshops |
| P14 | Robotics Cyber Security: Vulnerabilities, Attacks, Countermeasures, and Recommendations | 2022 | Yes | Online 2021; journal issue 2022 |
| P15 | GAN-Based Robust Motion Planning for Mobile Robots Against Localization Attacks | 2023 | Yes | IEEE RA-L |
| P16 | Real-Time Data-Predictive Attack-Recovery for Complex CPS | 2023 | Yes | IEEE RTAS |
| P17 | Learn-to-Respond / SeqRec | 2023 | Yes | IEEE RTSS |
| P18 | SCVMON | 2023 | Yes | RAID |
| P19 | TileMask | 2023 | Yes | ACM CCS |
| P20 | Un-Rocking Drones | 2023 | Yes | NDSS |
| P21 | Hybrid Knowledge and Data Driven Synthesis of Runtime Monitors | 2024 | Yes | Online 2023; TDSC issue 2024 |
| P22 | Robust Motion Planning for Multi-Robot Systems Against Position Deception Attacks | 2024 | Yes | IEEE TIFS |
| P23 | Diagnosis-guided Attack Recovery / DeLorean | 2024 | Yes | ACM AsiaCCS |
| P24 | SpecGuard | 2024 | Yes | ACM CCS |
| P25 | Recovery from Adversarial Attacks in CPS: Shallow, Deep, and Exploratory Works | 2024 | Yes | ACM Computing Surveys |
| P26 | Sensor Attack Online Classification for UAVs Using Machine Learning | 2025 | Yes | Computers & Security |
| P27 | Real-time Rectifying Flight Control Misconfiguration Using Intelligent Agent / Nyctea | 2025 | Yes | ACM TOSEM |
| P28 | Software Availability Protection in CPS / Gecko | 2025 | Yes | USENIX Security; present twice with identical text |
| N01 | Event Confirmation_Itinerary - Ascend | N/A | No | Non-research itinerary |

## 12. Final takeaway

The strongest combined lesson is that CPS security cannot stop at identifying bad data. A useful defense must answer five questions in time:

1. **What is wrong?** Detection.
2. **Which component is wrong?** Diagnosis/classification.
3. **What state is the physical system actually in?** Reconstruction.
4. **What action can still meet the safety deadline?** Recovery control.
5. **What does “recovered” mean for the mission?** Specification compliance and availability.

The papers gradually fill these five boxes, but no single work completely solves all of them. The most promising research direction suggested by the collection is a unified, confidence-aware system that combines interpretable hybrid monitoring, multi-sensor causal diagnosis, targeted reconstruction, specification-aware nonlinear recovery, and software-level graceful degradation under one real-time assurance argument.
