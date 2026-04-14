# Quantum Double Slit Simulation using Quantum Circuits

## Core Logic of the Simulation

The way I thought about this project was pretty simple. I wanted to actually mimic what happens in the real double-slit experiment.

The main idea that made everything click for me was how the electron behaves before it reaches the slits. In classical physics, we would say the electron goes through either slit 1 or slit 2. But in quantum mechanics, that's not really true. The electron can exist in a superposition of both paths.

So we can think of it like:

|ψ⟩ = (|slit1⟩ + |slit2⟩) / √2

Basically, the electron is not choosing one slit — it has some "presence" in both.

This is exactly why a qubit works perfectly here. A qubit can also exist in two states at the same time. So I just mapped:

|0⟩ → slit1
|1⟩ → slit2

Now the next thing is how the pattern actually forms. In the real experiment, you don't see a wave pattern immediately. You fire one electron → one dot. Another electron → another dot. But after a lot of electrons, a pattern slowly builds up.

So I made the simulation work the same way:

one circuit run = one electron

Now I needed to represent positions on the screen. Since I can't simulate a continuous screen, I divide it into many points. Each point corresponds to a position x.

For each position, there is a phase difference between the two paths. From physics:

θ(x) = (2πd / λL) * x

So for every position, I calculate a value of θ. This is what controls interference.

## Quantum Circuit Logic (Step-by-Step)

### Step 1: Start with |0⟩

I begin with:

|0⟩

This represents slit 1.

### Step 2: First Hadamard

|0⟩ → (|0⟩ + |1⟩) / √2

Now the qubit is in superposition.

Meaning: electron is going through both slits

### Step 3: Apply phase (this is the key step)

I apply:

Rz(θ)

#### Why am I doing this?

In the real experiment, the two paths are not equal. One path is slightly longer than the other. That creates a phase difference.

So to simulate that, I add phase to one part of the state.

After applying it:

(|0⟩ + |1⟩) / √2 → (|0⟩ + e^{iθ}|1⟩) / √2

#### What changed?

|0⟩ → stays same
|1⟩ → gets phase

So now: slit 1 and slit 2 have a phase difference.

Important: This step does NOT change probability yet. It only changes phase.

### Step 4: Second Hadamard (this is where interference happens)

Now I apply Hadamard again.

Why this causes interference:

Before this:
- we had two paths
- they had different phases
- but they were not combined

This step mixes them again.

So now:
- amplitudes from both paths combine
- phase difference starts affecting probability

### Full Calculation

After applying all gates:

|ψ⟩ = ½[(1 + e^{iθ})|0⟩ + (1 - e^{iθ})|1⟩]

### Probability of measuring |0⟩

Amplitude:

(1 + e^{iθ}) / 2

Probability:

P(0) = |(1 + e^{iθ}) / 2|^2

Simplify:

P(0) = (1 + cosθ) / 2 = cos²(θ/2)

## Connection to Double Slit Physics

From the actual experiment:

I(x) ∝ cos²(θ/2)

From the circuit:

P(0) = cos²(θ/2)

Key Insight: Intensity ∝ Probability of measuring |0⟩

Meaning in simulation:

measure 0 → electron detected
high probability → many electrons
low probability → few electrons

## What happens after getting P(0)

So after all the gates, we get a probability.

But in real life, we don't see probability directly.

one electron → one click

So I don't use the probability directly. I use it to generate random measurement outcomes.

## Single run = one electron

Each time I run the circuit:

I get either 0 or 1
this is random, but follows P(0)

So: one run = one electron detection

## Many runs = many electrons

If I run the circuit multiple times (shots), I get something like:

280 times → 0
120 times → 1

So: 280 electrons detected at that position.

## Why only count 0?

In my model:

|0⟩ → detection
|1⟩ → ignore

So: number of 0's = number of electrons at that position

## Convert counts into points

Instead of writing: x → 50 electrons

I create 50 actual points near that position.

So:

more counts → more points
fewer counts → fewer points

## Add small spread (important)

If I place all points exactly at one position, it looks unrealistic.

So I:

- slightly vary x
- randomly assign y

This makes it look like a real screen.

## Repeat for all positions

For each position:

- compute θ
- run circuit many times
- count 0's
- convert to points
- store

## Final Result

At the end, I have a large set of points:

- dense regions → bright
- sparse regions → dark

When plotted, the interference pattern automatically appears.


<img width="942" height="722" alt="image" src="https://github.com/user-attachments/assets/1f08beef-3b72-4350-a7b1-967dd092b4e0" />
# Result Analysis and Mathematical Justification

## What the Result Shows

The simulation produces two key visual outputs:

1. Electron detection build-up (scatter plot)
   - Each point represents a single electron detection
   - Pattern emerges gradually as more electrons are fired

2. Probability distribution curve
   - Smooth curve showing how detection probability varies with position

## Why This Pattern Emerges

From the quantum circuit, after applying:

- Hadamard -> superposition
- Phase gate -> path difference
- Hadamard -> interference

the final state is:

|ψ⟩ = ½[(1 + e^{iθ})|0⟩ + (1 - e^{iθ})|1⟩]

## Probability Calculation

The amplitude of measuring |0⟩ is:

A = (1 + e^{iθ}) / 2

The probability is:

P(0) = |A|²

Expanding:

|1 + e^{iθ}|² = (1 + e^{iθ})(1 + e^{-iθ}) = 1 + e^{iθ} + e^{-iθ} + 1 = 2 + 2cosθ

Thus:

P(0) = (2 + 2cosθ) / 4 = (1 + cosθ) / 2

Using the identity:

cos²(θ/2) = (1 + cosθ)/2

The final result is:

P(0) = cos²(θ/2)

## Connection to Real Double Slit

From wave optics:

I(x) ∝ cos²(θ/2)

From the simulation:

P(0) = cos²(θ/2)

## Key Conclusion

The intensity of fringes in the real experiment equals the probability of detection in the simulation. Therefore, the simulation is physically accurate, not merely visual.

## Role of Position (x)

Recall:

θ(x) = (2πd / λL) · x

Substituting into the probability gives:

P(x) = cos²[(πd / λL) · x]

Thus, the probability varies with position, which creates the fringes.

## Why Bright and Dark Fringes Form

- When θ = 0, 2π, 4π -> cos² = 1 -> maximum (bright fringe)
- When θ = π, 3π -> cos² = 0 -> minimum (dark fringe)

Therefore:

- constructive interference leads to bright regions
- destructive interference leads to dark regions

## Why the Scatter Plot Matches the Curve

Each electron is detected randomly, but with probability P(x).

Consequently:

- High P(x) produces many detections and dense points
- Low P(x) produces few detections and sparse points

Over many electrons, the randomness averages out and a smooth interference pattern appears. This is exactly how real experiments behave.

## Normalization (Area Under Curve = 1)

A valid probability distribution must satisfy:

∫ P(x) dx = 1

## Proof

Let:

P(x) = cos²(ax), where a = (πd / λL)

Using the identity:

cos²(ax) = (1 + cos(2ax)) / 2

Then:

∫ P(x) dx = ∫ (1 + cos(2ax)) / 2 dx = ½ ∫ dx + ½ ∫ cos(2ax) dx

Integrating:

= ½ x + (1 / 4a) sin(2ax)

## Over a Symmetric Interval

For a screen range [-X, X]:

- sin(2ax) cancels out due to symmetry
- only the constant term remains

Thus:

∫ P(x) dx = X

## Important Insight

This means the raw P(x) is not normalized.

## Proper Normalization

To obtain a true probability distribution:

P_norm(x) = P(x) / ∫ P(x) dx

Therefore:

P_norm(x) = cos²(ax) / X

Now:

∫ P_norm(x) dx = 1

## Meaning in the Simulation

In the implementation:

- There is no explicit normalization
- Instead, normalization occurs implicitly through sampling

This is because:

- total shots are fixed
- probabilities determine relative counts

Thus, the distribution is automatically normalized in practice.

