# Un-Rocking Drones — Equations 1–10 Explained in Detail

**Paper:** *Un-Rocking Drones: Foundations of Acoustic Injection Attacks and Recovery Thereof*  
**Main topic:** Acoustic injection attacks against MEMS IMU sensors in drones, and recovery of compromised sensor readings using UNROCKER.

This file explains **Equation 1 through Equation 10** in a very easy but detailed way. For each equation, I explain:

1. what the equation means,
2. what each symbol means,
3. how the equation works physically,
4. a simple numerical example,
5. how the equation connects to the research paper,
6. a clearly marked **phrase version** of the equation.

---

## Big picture before the equations

The paper studies how **acoustic sound** can attack drone sensors.

Drones use an IMU, which usually contains:

- **accelerometers**: measure linear acceleration,
- **gyroscopes**: measure angular rate / rotation.

These sensors are MEMS sensors. That means they contain very tiny mechanical moving parts, such as a tiny **proof mass** attached to springs. Because they contain moving mechanical parts, strong sound or ultrasound can make those tiny structures vibrate.

The attack idea is:

```text
Malicious acoustic signal
→ tiny MEMS proof mass vibrates
→ capacitance changes
→ sensor reports false value
→ flight controller receives corrupted IMU data
→ drone control logic becomes wrong
→ drone may crash
```

The equations explain how that false sensor value is created mathematically.

---

# Equation 1 — MEMS Gyroscope Coriolis Force

## Equation

$$
F_{coriolis} = -2m(\omega \times v) = -kx
$$

## Marked phrase version

> **Phrase:** The gyroscope measures rotation by observing how a vibrating proof mass gets pushed sideways by the Coriolis force.

Or even simpler:

> **Phrase:** Real rotation + vibrating mass = sideways displacement, and the gyroscope measures that sideways displacement.

---

## What this equation is about

Equation 1 explains the basic physics of a MEMS gyroscope.

A gyroscope does **not** directly see rotation like a camera sees an object. Instead, it has a tiny internal proof mass that is continuously vibrating in one direction. This is called the **drive direction**.

When the drone rotates, the Coriolis effect pushes that moving mass sideways into another direction. This sideways direction is called the **sense direction**.

The sensor measures the sideways displacement and converts it into angular rate.

---

## What each symbol means

| Symbol | Meaning | Easy explanation |
|---|---|---|
| `F_coriolis` | Coriolis force | Sideways force created when a moving mass is inside a rotating frame |
| `m` | proof mass | Tiny movable mass inside the gyroscope |
| `ω` | angular rate | How fast the drone/sensor is rotating |
| `v` | velocity of proof mass | How fast the mass is vibrating in the drive direction |
| `ω × v` | cross product | Means the force direction is perpendicular to both rotation and vibration |
| `k` | spring constant | How stiff the sense-direction spring is |
| `x` | displacement | How far the mass moves sideways |
| `-` sign | opposite direction | Spring force opposes displacement |

---

## How it works physically

The gyroscope mass is intentionally vibrated:

```text
Drive direction: left-right-left-right-left-right
```

If the drone does not rotate, there is no sideways Coriolis force:

$$
\omega = 0 \Rightarrow F_{coriolis}=0
$$

So the gyroscope reports almost zero angular rate.

If the drone rotates, the moving proof mass is pushed sideways:

```text
mass vibrating left-right
+
drone rotating
=
sideways force in sense direction
```

That sideways force stretches/compresses the sense-mode spring. The spring displacement is measured as capacitance/voltage change.

---

## Why `-kx` appears

The spring force follows Hooke's law:

$$
F = -kx
$$

If the mass moves right, the spring pulls left. If the mass moves left, the spring pulls right. The negative sign means the spring force is a restoring force.

So Equation 1 connects:

```text
Coriolis force from rotation
=
spring force from sideways displacement
```

---

## Simple numerical example

Suppose:

$$
m = 0.01
$$

$$
\omega = 2
$$

$$
v = 3
$$

For a simple magnitude example, ignore direction and cross-product angle:

$$
F_{coriolis} \approx 2mv
\omega
$$

$$
F_{coriolis} \approx 2(0.01)(2)(3)=0.12
$$

So the Coriolis force magnitude is about:

$$
0.12
$$

If:

$$
k=6
$$

then:

$$
F = kx
$$

$$
x = \frac{F}{k}=\frac{0.12}{6}=0.02
$$

So the mass moves sideways by:

$$
x=0.02
$$

The gyroscope measures this displacement and converts it into angular rate.

---

## How Equation 1 relates to the paper

The paper uses Equation 1 to explain why gyroscopes are vulnerable. The gyroscope output depends on tiny mechanical displacement. Acoustic sound can create extra displacement. Therefore, the gyroscope may confuse acoustic vibration with real rotation.

This becomes the foundation for Equation 4 through Equation 10, where the paper models how acoustic sound creates false gyroscope output.

---

# Equation 2 — Under-Sampled Resonant Signal / Aliasing Model

## Equation

$$
\hat{s}'(t) = A_i \cdot \cos(2\pi(F_i - N \cdot F_s)t + \phi)
$$

## Marked phrase version

> **Phrase:** A high-frequency acoustic attack can appear as a lower-frequency fake sensor signal after the drone samples it.

Or:

> **Phrase:** What the drone sees after sampling is not always the original acoustic frequency; it can be an aliased lower-frequency version.

---

## What this equation is about

Equation 2 explains **aliasing**.

A real acoustic attack signal is continuous and can be several kHz. But the drone reads sensor values only at specific moments. For PX4, the paper discusses a sampling frequency of about **250 Hz**.

If the attack signal is much faster than the sampling frequency, the drone cannot correctly represent it. The high-frequency signal appears as a lower-frequency signal.

---

## What each symbol means

| Symbol | Meaning | Easy explanation |
|---|---|---|
| `ŝ′(t)` | under-sampled resonant signal | What the drone effectively sees after sampling |
| `A_i` | induced amplitude / gain | Strength of the acoustic attack effect |
| `F_i` | induced/acoustic-related frequency | Frequency component before modular reduction |
| `F_s` | sampling frequency | How many times per second the drone reads the sensor |
| `N` | integer multiplier | Chooses the closest sampling multiple for aliasing/modular operation |
| `t` | time | Current time |
| `φ` | phase | Starting offset of the wave |
| `cos(...)` | sinusoidal vibration | Periodic acoustic vibration form |

---

## Easy explanation of sampling

Suppose the real signal is moving continuously:

```text
continuous signal: exists at every instant
```

The drone cannot store every instant. It samples:

```text
sample at 0 ms
sample at 4 ms
sample at 8 ms
sample at 12 ms
...
```

If the drone samples at 250 Hz:

$$
F_s=250\text{ Hz}
$$

then one sample happens every:

$$
\frac{1}{250}=0.004\text{ s}=4\text{ ms}
$$

---

## Easy explanation of aliasing

If the acoustic attack is:

$$
5040\text{ Hz}
$$

and the drone samples at:

$$
250\text{ Hz}
$$

then the paper gives the example:

$$
5040 \mod 250 = 40
$$

So a real 5040 Hz acoustic signal can appear to the drone as about 40 Hz.

That is aliasing.

---

## Simple numerical example

Let:

$$
A_i=5
$$

$$
F_i=5040\text{ Hz}
$$

$$
F_s=250\text{ Hz}
$$

Choose:

$$
N=20
$$

because:

$$
20 \cdot 250 = 5000
$$

Then:

$$
F_i - N F_s = 5040 - 5000 = 40\text{ Hz}
$$

So:

$$
\hat{s}'(t)=5\cos(2\pi(40)t+\phi)
$$

Meaning:

```text
The original acoustic signal is 5040 Hz,
but the sampled resonant signal appears as 40 Hz.
```

---

## How Equation 2 relates to the paper

The paper uses this to explain why acoustic attacks are dangerous in sampled drone systems. Acoustic resonance occurs at several kHz, but drones sample IMU sensors at much lower rates. Therefore, acoustic signals can become lower-frequency components in the digital sensor stream.

Later, the paper shows that **sampling jitter** spreads these aliased signals across many frequencies, including the drone's control-relevant in-band range. This is the key reason the attack can bypass low-pass filtering and destabilize the drone.

---

# Equation 3 — Accelerometer Acoustic Injection Model

## Equation

$$
\hat{s}_{acc}(t) = s_{acc}(t) + A_i \cdot \cos(2\pi F_a t + \phi)
$$

## Marked phrase version

> **Phrase:** The attacked accelerometer reading equals the real accelerometer reading plus a fake acoustic vibration wave.

Or:

> **Phrase:** What the drone receives = what really happened + what the sound attack injected.

---

## What this equation is about

Equation 3 models how acoustic sound corrupts a MEMS accelerometer.

An accelerometer has a tiny proof mass on springs. Real acceleration moves that mass. But sound can also vibrate that mass. The sensor cannot perfectly know whether the movement came from real acceleration or acoustic vibration.

So the attacked accelerometer signal is modeled as:

```text
clean accelerometer signal + acoustic resonant signal
```

---

## What each symbol means

| Symbol | Meaning | Easy explanation |
|---|---|---|
| `ŝ_acc(t)` | attacked accelerometer signal | What the drone receives during attack |
| `s_acc(t)` | original/benign accelerometer signal | Real acceleration value |
| `A_i` | induced amplitude | Strength of the false acoustic component |
| `F_a` | acoustic resonance frequency | Frequency of acoustic attack against accelerometer |
| `t` | time | Current time |
| `φ` | phase | Starting offset of the attack wave |
| `cos(...)` | periodic acoustic wave | Mathematical form of vibration |

---

## How it works physically

Normally:

```text
real acceleration
→ proof mass moves
→ capacitance changes
→ accelerometer reports acceleration
```

During attack:

```text
real acceleration
+
malicious sound vibration
→ proof mass moves incorrectly
→ capacitance changes incorrectly
→ accelerometer reports corrupted acceleration
```

So the output becomes:

```text
real signal + fake sound signal
```

---

## Simple numerical example

Suppose at a certain moment:

$$
s_{acc}(t)=2\text{ m/s}^2
$$

The acoustic attack amplitude is:

$$
A_i=5\text{ m/s}^2
$$

If the cosine value is:

$$
\cos(2\pi F_a t+\phi)=1
$$

then:

$$
\hat{s}_{acc}(t)=2+5(1)=7\text{ m/s}^2
$$

So the drone receives:

$$
7\text{ m/s}^2
$$

although the true acceleration is only:

$$
2\text{ m/s}^2
$$

At another moment, if:

$$
\cos(2\pi F_a t+\phi)=-1
$$

then:

$$
\hat{s}_{acc}(t)=2+5(-1)=-3\text{ m/s}^2
$$

So the attacked sensor reading oscillates around the true value.

---

## Paper-specific example

In the paper's accelerometer resonance tests, the authors used resonant amplitudes up to about:

$$
A_i = 90\text{ m/s}^2
$$

and acoustic frequency around:

$$
F_a = 5.125\text{ kHz}
$$

So conceptually:

$$
\hat{s}_{acc}(t) = s_{acc}(t) + 90\cos(2\pi(5125)t+\phi)
$$

This means the testbed takes the clean simulated accelerometer value and adds a strong acoustic resonant waveform.

---

## How Equation 3 relates to the paper

The authors use Equation 3 as the accelerometer software model in their PX4 acoustic injection testbed. Instead of physically damaging many drones, they simulate the resonant attack by adding this cosine term to benign accelerometer readings.

This model later helps them compare SITL and HITL behavior and discover that sampling jitter is a critical factor in drone crashes.

---

# Equation 4 — Full Resonant Gyroscope Displacement Model

## Equation

$$
\hat{x}(t) = S\Omega(t)\cos(\omega_d t) + A_d\Omega(t)\cos(\omega_{ac}t + \phi) + A_s\cos(\omega_{ac}t + \phi)
$$

## Marked phrase version

> **Phrase:** The attacked gyroscope proof-mass displacement is the sum of normal rotation displacement, acoustic disturbance through the drive direction, and acoustic disturbance directly through the sense direction.

Or:

> **Phrase:** What the gyroscope mass does = real rotation effect + fake acoustic effect through drive mode + fake acoustic effect through sense mode.

---

## What this equation is about

Equation 4 is for the MEMS gyroscope, not the accelerometer.

The gyroscope is more complex because it has two internal directions:

| Direction | Meaning |
|---|---|
| drive direction | direction where the proof mass is intentionally vibrating |
| sense direction | direction where displacement is measured to infer rotation |

Acoustic sound can affect both directions. Therefore, the paper models the attacked proof-mass displacement as three terms.

---

## What each symbol means

| Symbol | Meaning | Easy explanation |
|---|---|---|
| `x̂(t)` | attacked proof-mass displacement | Corrupted displacement in the sensing direction |
| `S` | scale gain | Normal conversion gain from angular rate to displacement |
| `Ω(t)` | benign angular rate | Real rotation of the drone |
| `ω_d` | drive angular frequency | Normal internal drive vibration frequency |
| `A_d` | drive-mode acoustic gain | How strongly sound affects the drive direction |
| `ω_ac` | acoustic angular frequency | Frequency of attacker's sound in rad/s |
| `A_s` | sense-mode acoustic gain | How strongly sound directly affects the sense direction |
| `φ` | phase | Starting offset of the acoustic wave |

---

## Term-by-term explanation

Equation 4 has three terms:

$$
\hat{x}(t)
=
\underbrace{S\Omega(t)\cos(\omega_d t)}_{\text{normal gyroscope behavior}}
+
\underbrace{A_d\Omega(t)\cos(\omega_{ac}t+\phi)}_{\text{attack through drive direction}}
+
\underbrace{A_s\cos(\omega_{ac}t+\phi)}_{\text{attack through sense direction}}
$$

### Term 1: normal gyroscope behavior

$$
S\Omega(t)\cos(\omega_d t)
$$

This is the normal clean gyroscope displacement.

The proof mass is vibrating at drive frequency `ω_d`. If the drone rotates with angular rate `Ω(t)`, the Coriolis effect creates sense-direction displacement.

### Term 2: acoustic effect through drive direction

$$
A_d\Omega(t)\cos(\omega_{ac}t+\phi)
$$

This is the acoustic disturbance through the drive direction.

It depends on `Ω(t)`, so if the drone is not rotating, this term becomes zero.

### Term 3: direct acoustic effect through sense direction

$$
A_s\cos(\omega_{ac}t+\phi)
$$

This is the direct acoustic disturbance in the sense direction. This is the most dangerous term because the sense direction is exactly what the gyroscope uses to infer rotation.

---

## Simple numerical example

Suppose:

$$
S=0.5
$$

$$
\Omega(t)=2
$$

$$
\cos(\omega_d t)=1
$$

$$
A_d=0.1
$$

$$
A_s=5
$$

$$
\cos(\omega_{ac}t+\phi)=1
$$

Now compute each term:

### Normal term

$$
S\Omega(t)\cos(\omega_d t)=0.5\times2\times1=1
$$

### Drive-direction acoustic term

$$
A_d\Omega(t)\cos(\omega_{ac}t+\phi)=0.1\times2\times1=0.2
$$

### Sense-direction acoustic term

$$
A_s\cos(\omega_{ac}t+\phi)=5\times1=5
$$

### Total

$$
\hat{x}(t)=1+0.2+5=6.2
$$

The real displacement should have been only `1`, but the attacked displacement is `6.2`. Most of the reading is fake.

---

## How Equation 4 relates to the paper

The paper needed a gyroscope attack model that works even when the drone is moving. Prior work mostly considered stationary cases. Equation 4 lets the authors represent the gyroscope under dynamic flight conditions by including the real angular rate `Ω(t)` and both acoustic paths.

This equation is the starting point for the gyroscope model that is eventually simplified into Equation 10.

---

# Equation 5 — Attacked Gyroscope Angular Rate Model

## Equation

$$
\hat{\Omega}(t)
=
\Omega(t)
+
\Omega(t)\left(\frac{A_d}{S}\cos((\omega_{ac}-\omega_d)t+\phi)\right)
+
\left(\frac{A_s}{S}\cos((\omega_{ac}-\omega_d)t+\phi)\right)
$$

## Marked phrase version

> **Phrase:** After converting proof-mass displacement into angular rate, the gyroscope output becomes real angular rate plus two false angular-rate components caused by acoustic sound.

Or:

> **Phrase:** What the gyroscope reports = real rotation + fake drive-mode acoustic rotation + fake sense-mode acoustic rotation.

---

## What this equation is about

Equation 4 models displacement. But the gyroscope output is angular rate. Equation 5 converts the attacked displacement into an attacked angular-rate signal.

The paper says this conversion is done by:

1. dividing both sides of Equation 4 by scale gain `S`,
2. multiplying by `cos(ω_d t)` to remove the drive-frequency modulation,
3. applying a low-pass filter to remove harmonic/high-frequency components.

---

## Why `(ω_ac - ω_d)` appears

The acoustic term in Equation 4 has:

$$
\cos(\omega_{ac}t+\phi)
$$

The gyroscope demodulation multiplies by the drive vibration:

$$
\cos(\omega_d t)
$$

Using the identity:

$$
\cos(A)\cos(B)=\frac{1}{2}[\cos(A-B)+\cos(A+B)]
$$

When the acoustic frequency mixes with the drive frequency, it creates:

$$
\omega_{ac}-\omega_d
$$

and

$$
\omega_{ac}+\omega_d
$$

The low-pass filter removes the high-frequency sum term. The remaining important term is the difference frequency:

$$
\omega_{ac}-\omega_d
$$

That is why Equation 5 contains:

$$
\cos((\omega_{ac}-\omega_d)t+\phi)
$$

---

## Term-by-term explanation

$$
\hat{\Omega}(t)
=
\underbrace{\Omega(t)}_{\text{real angular rate}}
+
\underbrace{\Omega(t)\left(\frac{A_d}{S}\cos((\omega_{ac}-\omega_d)t+\phi)\right)}_{\text{false component from drive direction}}
+
\underbrace{\left(\frac{A_s}{S}\cos((\omega_{ac}-\omega_d)t+\phi)\right)}_{\text{false component from sense direction}}
$$

---

## Simple numerical example

Suppose:

$$
\Omega(t)=2\text{ rad/s}
$$

$$
\frac{A_d}{S}=0.05
$$

$$
\frac{A_s}{S}=4
$$

$$
\cos((\omega_{ac}-\omega_d)t+\phi)=1
$$

Then:

$$
\hat{\Omega}(t)=2+2(0.05)(1)+4(1)
$$

$$
\hat{\Omega}(t)=2+0.1+4=6.1\text{ rad/s}
$$

The real angular rate is only:

$$
2\text{ rad/s}
$$

but the attacked gyroscope reports:

$$
6.1\text{ rad/s}
$$

---

## How Equation 5 relates to the paper

Equation 5 is important because it shows that the acoustic attack creates false angular-rate components. The paper then asks: which false component matters more — the drive-direction effect or the sense-direction effect?

That question leads to Equations 6–9.

---

# Equation 6 — Acoustic Excitation in the Sense Direction

## Equation

$$
x_{acS}=x_s\cdot\cos(2\pi f_{ac}\cdot t+\phi)
$$

## Marked phrase version

> **Phrase:** Acoustic sound directly shakes the gyroscope proof mass in the sensing direction as a cosine wave.

Or:

> **Phrase:** Direct sense-mode attack = acoustic vibration directly in the direction the gyroscope uses to detect rotation.

---

## What this equation is about

Equation 6 models direct acoustic displacement in the sensing direction.

The sensing direction is the dangerous direction because this is where the gyroscope measures displacement to infer angular rate.

---

## What each symbol means

| Symbol | Meaning | Easy explanation |
|---|---|---|
| `x_acS` | acoustic sense-direction displacement | Direct fake displacement in sensing direction |
| `x_s` | amplitude of sense displacement | How strongly sound moves the mass in sense direction |
| `f_ac` | acoustic frequency in Hz | Sound frequency |
| `t` | time | Current time |
| `φ` | phase | Starting offset |

---

## Why `2πf_ac` appears

Normal frequency is measured in Hz:

$$
f_{ac}
$$

Angular frequency is:

$$
\omega_{ac}=2\pi f_{ac}
$$

So:

$$
\cos(2\pi f_{ac}t+\phi)
$$

is the same type of sinusoidal wave as:

$$
\cos(\omega_{ac}t+\phi)
$$

---

## Simple numerical example

Suppose:

$$
x_s=3
$$

and at a certain time:

$$
\cos(2\pi f_{ac}t+\phi)=1
$$

Then:

$$
x_{acS}=3\times1=3
$$

So the acoustic sound directly creates a sense-direction displacement of `3`.

---

## How Equation 6 relates to the paper

Equation 6 represents the direct acoustic attack path in the gyroscope's sensing direction. The paper later compares this direct effect against the indirect drive-direction effect. The comparison shows that the direct sense-direction effect dominates.

---

# Equation 7 — Acoustic Excitation in the Drive Direction

## Equation

$$
x_{acD}=x_d\cdot\cos(2\pi f_{ac}\cdot t+\phi)
$$

## Marked phrase version

> **Phrase:** Acoustic sound can also shake the gyroscope proof mass in the drive direction.

Or:

> **Phrase:** Drive-mode attack = acoustic vibration in the direction where the gyroscope mass is normally driven.

---

## What this equation is about

Equation 7 models acoustic displacement in the drive direction.

The drive direction is the normal vibration direction of the gyroscope proof mass. The paper considers whether acoustic disturbance in this direction can indirectly create false sensing-direction displacement through Coriolis force.

---

## What each symbol means

| Symbol | Meaning | Easy explanation |
|---|---|---|
| `x_acD` | acoustic drive-direction displacement | Fake displacement in drive direction |
| `x_d` | amplitude of drive displacement | How strongly sound moves the mass in drive direction |
| `f_ac` | acoustic frequency | Sound frequency |
| `t` | time | Current time |
| `φ` | phase | Starting offset |

---

## Simple numerical example

Suppose:

$$
x_d=2
$$

and:

$$
\cos(2\pi f_{ac}t+\phi)=1
$$

Then:

$$
x_{acD}=2\times1=2
$$

So the acoustic sound causes drive-direction displacement of `2`.

---

## How Equation 7 relates to the paper

Equation 7 is needed so the authors can calculate how much drive-direction acoustic vibration transfers into the sensing direction. That calculation happens in Equation 8.

---

# Equation 8 — Drive-Direction Acoustic Effect Transferred to Sense Direction

## Equation

$$
x^{S}_{acD}
=
\frac{2m\Omega}{k_s}\cdot\frac{\partial x_{acD}}{\partial t}
=
\frac{4\pi m f_{ac}x_d\Omega\sin(2\pi f_{ac}t+\phi)}{k_s}
$$

## Marked phrase version

> **Phrase:** If sound shakes the drive direction, Coriolis physics can transfer part of that motion into the sense direction, but this transferred effect is very small in the paper's gyroscope model.

Or:

> **Phrase:** Drive-direction vibration becomes sense-direction error only through Coriolis coupling.

---

## What this equation is about

Equation 8 asks:

```text
If the acoustic attack shakes the drive direction,
how much false displacement appears in the sensing direction?
```

This is important because the gyroscope only reports angular rate based on sensing-direction displacement.

---

## Why derivative appears

Coriolis force depends on velocity, not only displacement.

From Equation 1:

$$
F_{coriolis}=-2m(\omega\times v)
$$

The drive-direction acoustic displacement is:

$$
x_{acD}=x_d\cos(2\pi f_{ac}t+\phi)
$$

Velocity is the derivative of displacement:

$$
v=\frac{\partial x_{acD}}{\partial t}
$$

That is why Equation 8 contains:

$$
\frac{\partial x_{acD}}{\partial t}
$$

---

## Derivation from Equation 7

Start with Equation 7:

$$
x_{acD}=x_d\cos(2\pi f_{ac}t+\phi)
$$

Differentiate:

$$
\frac{\partial x_{acD}}{\partial t}
=
-2\pi f_{ac}x_d\sin(2\pi f_{ac}t+\phi)
$$

The sign depends on direction convention. For comparing amplitude, the sign is not important. The important magnitude is:

$$
2\pi f_{ac}x_d\sin(2\pi f_{ac}t+\phi)
$$

Apply the Coriolis-to-spring relationship:

$$
x^{S}_{acD}
=
\frac{2m\Omega}{k_s}\cdot\frac{\partial x_{acD}}{\partial t}
$$

Substitute the derivative:

$$
x^{S}_{acD}
=
\frac{2m\Omega}{k_s}\cdot 2\pi f_{ac}x_d\sin(2\pi f_{ac}t+\phi)
$$

So:

$$
x^{S}_{acD}
=
\frac{4\pi m f_{ac}x_d\Omega\sin(2\pi f_{ac}t+\phi)}{k_s}
$$

That is Equation 8.

---

## What each symbol means

| Symbol | Meaning | Easy explanation |
|---|---|---|
| `x^S_acD` | sense displacement caused by drive acoustic excitation | Indirect false displacement |
| `m` | proof mass | Tiny mass inside gyroscope |
| `Ω` | angular rate | Real rotation rate |
| `k_s` | sense-mode spring coefficient | Stiffness in sensing direction |
| `∂x_acD/∂t` | velocity of drive-direction acoustic motion | Rate of drive-direction vibration |
| `f_ac` | acoustic frequency | Sound frequency |
| `x_d` | drive displacement amplitude | Strength of drive-direction acoustic vibration |

---

## Simple numerical example

Use easy artificial numbers:

$$
m=1
$$

$$
\Omega=2
$$

$$
k_s=4
$$

$$
f_{ac}=10
$$

$$
x_d=1
$$

$$
\sin(2\pi f_{ac}t+\phi)=1
$$

Then:

$$
x^{S}_{acD}
=
\frac{4\pi(1)(10)(1)(2)(1)}{4}
$$

$$
x^{S}_{acD}=20\pi\approx62.8
$$

This example uses simple artificial numbers. In a real MEMS gyroscope, the proof mass `m` is extremely tiny, so this term becomes very small.

---

## How Equation 8 relates to the paper

Equation 8 prepares the comparison in Equation 9. The authors want to show that even if acoustic sound affects the drive direction, its transferred effect into the sensing direction is negligible compared to direct sense-direction acoustic excitation.

---

# Equation 9 — Relative Gain Between Drive Effect and Sense Effect

## Equation

$$
G_{rel}
=
\frac{|x^{S}_{acD}|}{|x_{acS}|}
=
\frac{A_d\cdot\Omega}{A_s}
=
\frac{4\pi\cdot m\cdot f_{ac}\cdot\Omega}{k_s}
$$

## Marked phrase version

> **Phrase:** Equation 9 measures how important the indirect drive-direction acoustic effect is compared with the direct sense-direction acoustic effect.

Or:

> **Phrase:** Relative gain = indirect drive-mode attack strength divided by direct sense-mode attack strength.

---

## What this equation is about

Equation 9 compares two attack paths:

| Attack path | Meaning |
|---|---|
| Direct sense-direction attack | Sound directly shakes the sensing direction |
| Indirect drive-direction attack | Sound shakes drive direction, then Coriolis coupling transfers part of it to sense direction |

The paper wants to know whether the indirect drive effect is important enough to keep in the final model.

---

## How Equation 9 is created

From Equation 6, the direct sense-direction displacement amplitude is approximately:

$$
|x_{acS}| = x_s
$$

From Equation 8, the indirect drive-to-sense displacement amplitude is approximately:

$$
|x^{S}_{acD}|=\frac{4\pi m f_{ac}x_d\Omega}{k_s}
$$

Divide them:

$$
G_{rel}
=
\frac{|x^{S}_{acD}|}{|x_{acS}|}
=
\frac{\frac{4\pi m f_{ac}x_d\Omega}{k_s}}{x_s}
$$

The paper assumes:

$$
x_d\approx x_s
$$

So:

$$
\frac{x_d}{x_s}\approx1
$$

Therefore:

$$
G_{rel}=\frac{4\pi m f_{ac}\Omega}{k_s}
$$

---

## What each symbol means

| Symbol | Meaning | Easy explanation |
|---|---|---|
| `G_rel` | relative gain | Size comparison between two acoustic paths |
| `x^S_acD` | drive-induced sense displacement | Indirect attack effect |
| `x_acS` | direct sense displacement | Direct attack effect |
| `A_d` | drive acoustic gain | Attack gain in drive direction |
| `A_s` | sense acoustic gain | Attack gain in sense direction |
| `m` | proof mass | Tiny MEMS mass |
| `f_ac` | acoustic frequency | Sound frequency |
| `Ω` | angular rate | Drone rotation rate |
| `k_s` | sense spring coefficient | Sense-mode stiffness |

---

## How to interpret `G_rel`

If:

$$
G_{rel}=1
$$

then drive-direction and sense-direction effects are similar.

If:

$$
G_{rel}=0.1
$$

then the drive effect is 10% of the sense effect.

If:

$$
G_{rel}\approx0
$$

then the drive effect is negligible.

The paper reports that using values from prior work, the relative gain becomes approximately:

$$
G_{rel}=3.3\times10^{-5}\approx0
$$

So the paper concludes:

```text
The sense-direction acoustic effect mainly governs the false gyroscope signal.
The drive-direction acoustic effect can be ignored in the final practical model.
```

---

## Simple example

Suppose:

$$
|x^{S}_{acD}|=0.003
$$

and:

$$
|x_{acS}|=100
$$

Then:

$$
G_{rel}=\frac{0.003}{100}=0.00003
$$

This is almost zero.

So the indirect drive-direction effect is tiny compared to the direct sense-direction effect.

---

## How Equation 9 relates to the paper

Equation 9 justifies simplifying the gyroscope model. Without Equation 9, the authors would need to keep both attack paths. Because `G_rel` is almost zero, they remove the drive-direction acoustic term and keep only the dominant direct sense-direction acoustic term.

This leads directly to Equation 10.

---

# Equation 10 — Final Simplified Gyroscope Acoustic Injection Model

## Equation

$$
\hat{\Omega}_{gyro}(t)
\approx
\Omega_{gyro}(t)+A_i\cdot\cos(2\pi F_i t+\phi)
$$

with:

$$
A_i=\frac{A_s}{S}
$$

and:

$$
F_i=\frac{|\omega_{ac}-\omega_d|}{2\pi}
$$

## Marked phrase version

> **Phrase:** The attacked gyroscope reading is approximately the real gyroscope reading plus one dominant acoustic cosine-wave error.

Or:

> **Phrase:** What the drone receives from the gyroscope = real rotation signal + fake sound-created rotation signal.

---

## What this equation is about

Equation 10 is the final practical gyroscope attack model.

The earlier equations showed that the gyroscope attack could have multiple parts. But Equation 9 showed that the drive-direction part is negligible. Therefore, the paper simplifies the gyroscope attack into a clean additive model.

This final model looks very similar to the accelerometer model:

```text
attacked sensor value = real sensor value + acoustic cosine error
```

---

## What each symbol means

| Symbol | Meaning | Easy explanation |
|---|---|---|
| `Ω̂_gyro(t)` | attacked gyroscope angular rate | Corrupted gyroscope reading |
| `Ω_gyro(t)` | benign gyroscope angular rate | Real angular rate |
| `A_i` | induced amplitude | Strength of fake gyroscope signal |
| `F_i` | induced frequency | False output frequency after acoustic/drive-frequency interaction |
| `φ` | phase | Starting offset |
| `A_s/S` | normalized sense acoustic gain | Converts acoustic sense displacement into angular-rate-scale error |
| `|ω_ac - ω_d| / 2π` | induced frequency | Difference between acoustic frequency and drive frequency, converted from rad/s to Hz |

---

## Why `F_i` uses `|ω_ac - ω_d|`

From Equation 5, the false acoustic angular-rate component contains:

$$
\cos((\omega_{ac}-\omega_d)t+\phi)
$$

That is in angular frequency units, rad/s.

To convert angular frequency to Hz:

$$
f=\frac{\omega}{2\pi}
$$

So:

$$
F_i=\frac{|\omega_{ac}-\omega_d|}{2\pi}
$$

The absolute value is used because frequency is nonnegative.

---

## Simple numerical example

Suppose:

$$
\Omega_{gyro}(t)=1.5\text{ rad/s}
$$

$$
A_i=4\text{ rad/s}
$$

$$
F_i=100\text{ Hz}
$$

$$
\phi=0
$$

Then:

$$
\hat{\Omega}_{gyro}(t)=1.5+4\cos(2\pi(100)t)
$$

At a moment when:

$$
\cos(2\pi(100)t)=1
$$

$$
\hat{\Omega}_{gyro}(t)=1.5+4=5.5\text{ rad/s}
$$

At a moment when:

$$
\cos(2\pi(100)t)=-1
$$

$$
\hat{\Omega}_{gyro}(t)=1.5-4=-2.5\text{ rad/s}
$$

So the true angular rate is:

$$
1.5\text{ rad/s}
$$

but the attacked gyroscope reports values oscillating between:

$$
-2.5\text{ rad/s and }5.5\text{ rad/s}
$$

---

## Paper-specific example

In the paper's gyroscope resonance injection experiments, one shown case used:

$$
F_i=100\text{ Hz}
$$

and:

$$
A_i=4\text{ rad/s}
$$

So the injected gyroscope signal can be understood as:

$$
\hat{\Omega}_{gyro}(t)
=
\Omega_{gyro}(t)+4\cos(2\pi(100)t+\phi)
$$

This means:

```text
take the real gyroscope angular rate
+
add a fake 100 Hz acoustic-induced angular-rate wave
```

The paper then feeds this attacked gyroscope signal into PX4 SITL/HITL to test drone behavior.

---

## How Equation 10 relates to the paper

Equation 10 is the final gyroscope software sensor model used to simulate acoustic injection attacks.

The paper uses this model to generate compromised gyroscope readings in the PX4 testbed. Then they compare what happens in SITL and HITL.

Important result:

```text
In ideal SITL, the drone often survives.
In HITL, the drone crashes because hardware sampling jitter spreads the resonant signal into the drone's control band.
```

So Equation 10 creates the fake gyroscope signal, and later sections explain why sampling jitter makes that fake signal dangerous.

---

# How Equations 1–10 connect together

## Complete chain

```text
Equation 1:
Gyroscope physics: rotation creates Coriolis force and displacement.

Equation 2:
Sampling physics: high-frequency attack can alias into lower-frequency digital signal.

Equation 3:
Accelerometer attack model: attacked accelerometer = real accelerometer + acoustic wave.

Equation 4:
Full gyroscope displacement model: normal gyro displacement + drive-direction acoustic effect + sense-direction acoustic effect.

Equation 5:
Convert attacked gyroscope displacement into attacked angular-rate output.

Equation 6:
Model direct acoustic vibration in the gyroscope sensing direction.

Equation 7:
Model acoustic vibration in the gyroscope drive direction.

Equation 8:
Compute how drive-direction acoustic vibration transfers into the sensing direction through Coriolis coupling.

Equation 9:
Compare drive-direction effect with direct sense-direction effect and show the drive effect is negligible.

Equation 10:
Final simplified gyroscope attack model: attacked gyroscope = real gyroscope + acoustic cosine wave.
```

---

# Final overall phrase summary

> **Marked phrase:** Acoustic injection makes MEMS sensors report false values because sound vibrates the tiny proof mass inside the sensor.

> **Marked phrase:** For accelerometers, the paper models the attack as real acceleration plus a resonant cosine wave.

> **Marked phrase:** For gyroscopes, the paper first models multiple physical attack paths, then proves the drive-direction path is negligible, leaving a simple real-angular-rate-plus-fake-cosine model.

> **Marked phrase:** These equations are used to build software models of attacked MEMS sensors in the PX4 testbed.

> **Marked phrase:** The later discovery of the paper is that sampling jitter spreads these resonant attack signals into low-frequency control ranges, allowing them to bypass filtering and destabilize drones.

---

# One-page simplified memory version

```text
Eq. 1: Gyroscope works using Coriolis force.
Eq. 2: Sampling can turn high-frequency attack into lower-frequency alias.
Eq. 3: Accelerometer attack = clean acceleration + acoustic wave.
Eq. 4: Gyroscope attacked displacement = normal + drive attack + sense attack.
Eq. 5: Convert attacked displacement to attacked angular rate.
Eq. 6: Sound directly shakes sense direction.
Eq. 7: Sound shakes drive direction.
Eq. 8: Drive shaking can transfer to sense direction through Coriolis coupling.
Eq. 9: That transfer is tiny, so drive attack is negligible.
Eq. 10: Final gyroscope attack = clean angular rate + acoustic wave.
```

---

# Final connection to UNROCKER

The paper's recovery system, **UNROCKER**, depends on this modeling idea.

The attack model says:

```text
compromised signal = benign signal + acoustic resonant noise
```

So the recovery problem becomes:

```text
Given noisy attacked sensor data, recover the clean benign sensor data.
```

That is why the paper uses a **denoising autoencoder**. The model learns to remove the acoustic/jitter-induced noise from the compromised gyroscope and accelerometer readings.

