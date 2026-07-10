# Motivation of the Paper: *Un-Rocking Drones: Foundations of Acoustic Injection Attacks and Recovery Thereof*

## 1. One-line motivation

**This paper is motivated by the need to keep drones flying safely to their planned destination even when their MEMS IMU sensors are corrupted by acoustic injection attacks.**

In simple words:

> **The authors do not only want to show that sound can attack drone sensors; they want to recover the corrupted sensor values so the drone can continue its mission instead of crashing.**

---

## 2. Why drones are important

Commercial drones are increasingly used in areas such as:

- delivery services,
- emergency rescue,
- military operations,
- inspection,
- surveillance,
- autonomous navigation.

Because drones fly in the physical world, they must react very quickly to changes in movement, wind, tilt, position, and altitude. To do that, drones depend heavily on sensors.

Important sensors include:

| Sensor | Purpose |
|---|---|
| Gyroscope | Measures angular rate: roll, pitch, yaw rotation |
| Accelerometer | Measures linear acceleration |
| Barometer | Measures altitude/air pressure |
| GNSS/GPS | Measures global position |
| Optical flow | Helps estimate movement relative to the ground |

The paper focuses mainly on **MEMS gyroscopes** and **MEMS accelerometers** because they are essential for attitude and position control.

### Motivation phrase 1

> **Drones cannot fly safely without trustworthy sensor readings.**

### Explanation

A drone is a feedback-control system. It constantly asks:

```text
Am I tilting?
Am I rotating?
Am I accelerating?
Am I drifting?
Do I need to change motor speeds?
```

If the sensor data is wrong, the drone controller may make wrong decisions.

Example:

```text
Fake gyroscope reading: "The drone is rolling right."
Controller response: "Correct the right roll."
Reality: The drone was not rolling right.
Result: The correction itself can destabilize the drone.
```

So the first motivation is that **sensor integrity is safety-critical for drones**.

---

## 3. Why MEMS IMU sensors are vulnerable

MEMS IMU sensors are tiny mechanical sensors. They are lightweight and cheap, which makes them useful for drones. But they contain tiny moving structures, especially a **proof mass**.

The proof mass moves when the drone accelerates or rotates. The sensor converts that motion into electrical signals and then into numerical sensor values.

This creates the vulnerability.

Sound is also vibration. If an attacker emits sound at the right frequency, the sound can make the proof mass vibrate even when the drone is not actually moving that way.

### Motivation phrase 2

> **The same physical mechanism that allows MEMS sensors to measure motion also makes them vulnerable to malicious acoustic vibration.**

### Explanation

A MEMS accelerometer measures proof-mass displacement caused by acceleration.

A MEMS gyroscope measures sideways proof-mass displacement caused by Coriolis force.

But acoustic sound can also move the proof mass.

So the sensor may confuse:

```text
real physical motion
```

with:

```text
malicious sound-induced vibration
```

That is the physical basis of the attack.

---

## 4. What is the acoustic injection attack problem?

An **acoustic injection attack** uses sound or ultrasound to resonate MEMS sensors.

The attack chain is:

```text
Attacker emits acoustic signal
→ MEMS proof mass resonates
→ capacitance/voltage changes
→ sensor output becomes corrupted
→ flight controller receives false IMU values
→ EKF/attitude controller estimates wrong state
→ motors receive wrong control commands
→ drone may crash
```

The attacker is not necessarily hacking the drone software directly. Instead, the attacker manipulates the physical sensor, and the corrupted sensor value enters the control software.

### Motivation phrase 3

> **This is a cyber-physical attack: the attacker enters through the physical sensor but affects the cyber/control logic.**

### Explanation

This is why the paper belongs to cybersecurity, especially **Cyber-Physical Systems security**. The attack starts in the physical layer but causes a failure in the control system.

---

## 5. Why the threat is practical and serious

The paper argues that acoustic injection attacks are increasingly serious because high-power acoustic devices and directed acoustic systems are becoming more practical.

The paper mentions examples such as:

- long-range acoustic devices,
- directed-energy acoustic systems,
- military acoustic-noise robustness testing.

The paper also notes that military environmental testing standards explicitly require systems to be tested against acoustic noise.

### Motivation phrase 4

> **Acoustic noise is not only a laboratory curiosity; it is a practical environmental and adversarial threat.**

### Explanation

A drone may operate in environments where strong acoustic noise exists naturally or intentionally. If this noise can corrupt sensor readings, then drone safety and mission success are at risk.

---

## 6. Why previous defenses are not enough

Before this paper, several defense strategies had already been proposed. However, the paper argues that they are not sufficient for practical drone mission survival.

### 6.1 Mechanical shielding and damping

These defenses try to physically block or reduce acoustic vibration.

Problem:

- They require hardware modification.
- They may create side effects such as heating.
- They may not completely stop resonance.

### 6.2 Circuit-level changes

These modify the sensor circuit or hardware.

Problem:

- They are not easy to apply to already-deployed drones.
- They require redesigning hardware.

### 6.3 Sampling-rate changes or randomization

These try to reduce spoofing by changing when samples are taken.

Problem:

- They may help against some spoofing attacks.
- But the paper argues they can make drones more vulnerable to denial-of-service effects because nonuniform sampling can spread resonance energy into dangerous frequencies.

### 6.4 Detection-only methods

These methods detect that an attack is happening.

Problem:

- Detection alone does not tell the drone what to do next.
- The drone still needs safe sensor values to continue flying.

### 6.5 Partial sensor recovery

Some recovery methods use other unaffected sensors.

Problem:

- They may only work for a few seconds.
- They may require supplementary sensors.
- They fail if all MEMS sensors are attacked simultaneously.

### Motivation phrase 5

> **Existing defenses may detect or reduce attacks, but they do not guarantee that the drone can continue its planned mission under persistent sensor compromise.**

### Explanation

The authors are motivated by a stronger goal than detection:

```text
not just "detect attack"
not just "survive for a moment"
not just "land immediately"

but:

recover useful sensor values and continue flying
```

---

## 7. The core research gap

The main gap is:

> **There was no practical mitigation strategy that could recover fully compromised MEMS IMU sensor values and allow the drone to continue flying to its target destination without relying on unaffected supplementary sensors.**

The paper wants to answer:

```text
Can we recover clean gyroscope and accelerometer values from acoustically compromised signals?
```

and:

```text
Can we do this without depending on another clean sensor?
```

### Motivation phrase 6

> **The missing piece is full sensor-value recovery under persistent acoustic attack.**

### Explanation

This is the main motivation behind UNROCKER.

The authors are not satisfied with simply saying:

```text
The sensor is attacked.
```

They want to produce:

```text
a recovered sensor value that the flight controller can still use.
```

---

## 8. Why the authors focus on gyroscopes and accelerometers

The paper focuses on MEMS gyroscopes and accelerometers because these two sensors are central to drone stability.

### Gyroscope

The gyroscope measures angular rate:

- roll rate,
- pitch rate,
- yaw rate.

If gyroscope values are corrupted, the drone may think it is rotating when it is not, or fail to detect real rotation.

### Accelerometer

The accelerometer measures linear acceleration and helps estimate orientation and position.

If accelerometer values are corrupted, the drone may think it is moving, tilting, rising, or falling incorrectly.

### Motivation phrase 7

> **Gyroscopes and accelerometers are high-value attack targets because they directly influence the drone's attitude and position estimation.**

### Example

If the gyroscope falsely reports:

```text
roll rate = high positive value
```

then the controller may command motor adjustments to cancel that imaginary roll.

If the accelerometer falsely reports:

```text
strong downward acceleration
```

then the controller may increase thrust unnecessarily.

Both cases can destabilize flight.

---

## 9. Why the paper needed a testbed

Testing acoustic injection attacks directly on real drones is expensive and risky.

Problems with real-world testing:

- The drone may crash and break.
- Multiple spare drones may be needed.
- The speaker must precisely target the flying drone.
- The attack frequency must match the sensor resonance frequency.
- Repeating experiments consistently is difficult.

So the authors built a **software-based acoustic injection testbed** using PX4.

They used:

- SITL: Software-in-the-loop simulation,
- HITL: Hardware-in-the-loop simulation,
- Pixhawk flight-controller hardware,
- resonant sensor models,
- automated experiment scripts.

### Motivation phrase 8

> **A realistic testbed is needed to study dangerous drone attacks repeatedly without physically destroying drones.**

### Explanation

The testbed lets the authors inject modeled acoustic attack signals into the drone sensor pipeline and observe how the controller behaves.

This allowed them to discover the paper's key finding: **sampling jitter**.

---

## 10. Why sampling jitter became a central motivation

At first, it may seem that sensor resonance alone should crash drones. But the authors found something more subtle.

In SITL, many resonant sensor signals did **not** crash the drone.

In HITL, the same attacks **did** crash the drone.

This difference motivated the authors to ask:

```text
Why does the drone survive in ideal software simulation but crash when real hardware is involved?
```

The answer was **sampling jitter**.

Sampling jitter means sensor values are not retrieved at perfectly equal time intervals.

Example:

Expected sampling interval:

```text
4.000 ms, 4.000 ms, 4.000 ms
```

Actual hardware sampling:

```text
3.91 ms, 4.12 ms, 3.85 ms, 4.06 ms
```

This timing irregularity spreads high-frequency resonant signals into lower frequencies, including the drone's control-relevant frequency range.

### Motivation phrase 9

> **Sampling jitter is the hidden bridge between high-frequency acoustic resonance and low-frequency control failure.**

### Explanation

Normally, a high-frequency acoustic signal should be filtered out.

But because of sampling jitter:

```text
high-frequency resonance
→ frequency spreading
→ in-band low-frequency noise
→ low-pass filter cannot remove all of it
→ EKF/control logic receives corrupted data
→ drone crashes
```

This was a major motivation for the recovery design.

---

## 11. Why low-pass filters are not sufficient

A low-pass filter removes high-frequency noise. So one might think:

```text
If acoustic attacks are high-frequency, just use a low-pass filter.
```

But the paper shows this is not enough.

Because sampling jitter spreads the resonant signal across multiple frequencies, some of the attack energy enters the low-frequency band. Once the attack is inside the low-frequency band, the low-pass filter cannot remove it without also removing useful drone motion information.

### Motivation phrase 10

> **Filtering alone fails because jitter moves part of the attack into the same frequency range as legitimate drone motion.**

### Example

Suppose the attack appears ideally at 100 Hz. If the low-pass filter cutoff is 30 Hz, the filter can remove it.

But with jitter, the attack spreads into:

```text
0 Hz to 125 Hz
```

Now part of it lies below 30 Hz. That part survives the filter.

Worse, the drone controller is sensitive around 0-5 Hz. If attack energy enters that band, the controller reacts to it.

---

## 12. Why denoising became the recovery idea

After discovering that sampling jitter spreads the resonant signal into the control band, the authors interpreted the corrupted signal as:

```text
compromised signal = benign signal + noise
```

This leads naturally to a denoising problem:

```text
Given noisy attacked sensor data, recover the clean benign sensor data.
```

The paper chose a **denoising autoencoder (DAE)** for this purpose.

### Motivation phrase 11

> **If acoustic corruption behaves like additive noise, then sensor recovery can be framed as a denoising problem.**

### Explanation

The DAE learns patterns from pairs of signals:

```text
input: compromised attacked sensor signal
output: benign clean sensor signal
```

After training, UNROCKER tries to convert new attacked sensor data into recovered sensor data.

---

## 13. Why the recovery system is called UNROCKER

Prior work showed that sound can “rock” drones by attacking gyroscopes and other MEMS sensors. This paper tries to reverse that effect.

So the name **UNROCKER** means:

```text
undo the rocking effect caused by acoustic injection
```

UNROCKER is placed between the sensor drivers and the flight controller.

The pipeline is:

```text
sensor driver
→ raw compromised sensor data
→ UNROCKER recovery model
→ recovered sensor data
→ flight controller
```

### Motivation phrase 12

> **UNROCKER is motivated by mission continuation, not merely attack detection.**

### Explanation

The practical goal is not just to say:

```text
Attack detected.
```

The goal is:

```text
Recovered sensor values are available.
The drone can continue flying.
```

---

## 14. Full motivation in paragraph form

The motivation of the paper is that modern drones depend on MEMS IMU sensors for stable flight, but these sensors are physically vulnerable to acoustic injection attacks because they contain moving proof masses that can resonate under sound. Such attacks can corrupt gyroscope and accelerometer outputs, leading to wrong state estimation and possibly drone crashes. Existing defenses are limited because many require hardware changes, only detect attacks, work only briefly, or depend on supplementary sensors that may also be compromised. Therefore, the authors are motivated to build a practical recovery method that can reconstruct benign sensor values from compromised ones and allow the drone to continue its planned mission. To do this, they first build a PX4-based acoustic injection testbed, discover that sampling jitter is the key factor that spreads resonant attack signals into the drone's control band, and then design UNROCKER, a denoising-autoencoder-based system that treats the corrupted resonance as noise and recovers clean MEMS IMU sensor values.

---

## 15. Presentation-ready motivation slide text

### Slide title

**Motivation: Why Acoustic Sensor Recovery Matters for Drones**

### Slide content

- Drones rely on MEMS gyroscopes and accelerometers for real-time attitude and position control.
- These MEMS sensors contain tiny moving proof masses, making them vulnerable to acoustic resonance.
- Acoustic injection attacks can corrupt sensor readings and cause wrong flight-control decisions.
- Existing defenses are limited: many require hardware changes, only detect attacks, or depend on unaffected supplementary sensors.
- The main missing capability is **full sensor-value recovery** so the drone can continue its planned mission under persistent attack.
- This motivates UNROCKER: a recovery system that treats acoustically corrupted signals as noise and reconstructs benign sensor values using a denoising autoencoder.

### One-sentence spoken version

> **The motivation of this paper is to move beyond attack detection and build a practical recovery system that lets drones keep flying even when their MEMS IMU sensors are compromised by acoustic injection attacks.**

---

## 16. Key motivation phrases to mark in your report

You can directly use these phrases in your report or presentation:

### Phrase 1

> **Drones cannot fly safely without trustworthy sensor readings.**

### Phrase 2

> **The same physical mechanism that allows MEMS sensors to measure motion also makes them vulnerable to malicious acoustic vibration.**

### Phrase 3

> **Acoustic injection is a cyber-physical attack because it manipulates physical sensors and then corrupts the drone's control logic.**

### Phrase 4

> **Existing defenses are insufficient because they do not guarantee mission continuation under persistent sensor compromise.**

### Phrase 5

> **The key research gap is full recovery of compromised MEMS gyroscope and accelerometer values without relying on unaffected supplementary sensors.**

### Phrase 6

> **Sampling jitter is the hidden factor that allows acoustic resonance to bypass filtering and affect the drone's control band.**

### Phrase 7

> **Once resonant attack signals are spread into the in-band range, filtering alone is not enough.**

### Phrase 8

> **The authors are motivated to treat jitter-spread resonance as noise and recover benign sensor signals using denoising.**

### Phrase 9

> **UNROCKER is designed for recovery and mission continuation, not simply attack detection.**

### Phrase 10

> **The paper's motivation is practical cyber-physical resilience: keeping drones operational even when their core IMU sensors are attacked.**

---

## 17. Short version

The short motivation is:

> **Acoustic attacks can corrupt drone MEMS IMU sensors and crash drones. Existing defenses cannot reliably keep the drone flying to its destination, especially under persistent attacks or when multiple sensors are compromised. Therefore, the paper is motivated to recover clean gyroscope and accelerometer values from attacked readings using a denoising-based recovery system called UNROCKER.**

---

## 18. Very simple version

In very simple words:

```text
Drones need sensors to fly.
Sound can fool those sensors.
If the drone trusts fake sensor data, it may crash.
Old defenses are not enough because they do not fully recover the sensor values.
This paper wants to clean the fake sensor data so the drone can keep flying.
```

That is the motivation of the paper.
