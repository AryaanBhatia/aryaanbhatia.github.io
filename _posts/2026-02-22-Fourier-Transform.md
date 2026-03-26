---
title: Weather Forecasting
date: 2026-02-22 00:00:00 +1000
categories: [MATHEMATICS]
tags: [mathematics, modelling]     # TAG names should always be lowercase
description:  Sound to Sins
comments: false
author: Aryaan
math: true
mermaid: true
---

# Everything Is a Sum of Sines

### The Fourier Transform and the Frequency Structure of Signals

*On an idea from 1822 that now runs inside your headphones, your MRI scanner, your options pricing model, and the JPEG on your phone.*

---

Any signal — any waveform, any function, any time series — can be written as a sum of pure sine waves. A symphony, a heartbeat, a stock price, the temperature record of the last thousand years. All of them are sums of sines and cosines at different frequencies and amplitudes.

This is the central claim of Fourier analysis. Jean-Baptiste Joseph Fourier published it in 1822 in his *Théorie Analytique de la Chaleur* while working on how heat distributes through solid objects. His contemporaries, including Lagrange, were not convinced the claim was general enough to hold. It was. The idea now underlies most of modern signal processing, physics, finance, and computing.

---

## Part One — What a Sine Wave Is and Why It Matters

A pure sine wave has three properties: a **frequency** (how many complete cycles per second, measured in Hertz), an **amplitude** (its peak value), and a **phase** (where in its cycle it starts). Everything else is fixed.

```
A Pure Sine Wave:

  f(t)  =  A · sin(2πνt + φ)

  where:
    A  = amplitude
    ν  = frequency (Hz)
    φ  = phase (radians)
    t  = time
```

Sine waves are the eigenfunctions of the differentiation operator. Differentiating a sine gives a cosine; differentiating again gives a negative sine. They are the functions left structurally unchanged by differentiation — only scaled and phase-shifted. This makes them the natural basis functions for any problem involving rates of change, which covers most of physics.

There is also a physical reason. Sine waves are what you get from any system with a restoring force proportional to displacement — a pendulum, a spring, an electrical LC circuit, a vibrating string. Linear oscillators produce pure sines. Since most physical systems are approximately linear near equilibrium, sines appear throughout applied science.

---

## Part Two — The Fourier Series: Decomposing Periodic Functions

Fourier's original result applied to **periodic** functions — ones that repeat with period T. The claim: any periodic function can be written as an infinite sum of sines and cosines whose frequencies are integer multiples of `1/T`.

```
The Fourier Series:

  f(t)  =  a₀/2  +  Σ [aₙ cos(2πnt/T)  +  bₙ sin(2πnt/T)]
                    n=1

  Coefficients:

    aₙ  =  (2/T) ∫₀ᵀ f(t) cos(2πnt/T) dt
    bₙ  =  (2/T) ∫₀ᵀ f(t) sin(2πnt/T) dt
```

The coefficients `aₙ` and `bₙ` measure how much of each frequency is present. Computing them involves multiplying the function by a sine or cosine at the target frequency and integrating — this is an inner product, measuring similarity between the function and the basis function at that frequency.

The square wave is the standard example — it is +1 for half a period and −1 for the other half, with sharp corners and instantaneous jumps. Its Fourier series is:

```
Fourier Series of a Square Wave:

  f(t)  =  (4/π) · Σ  sin(2π(2k−1)t/T) / (2k−1)
                    k=1

  Only odd harmonics, with amplitudes falling as 1/n.
```

The first term alone is a rough approximation. Adding the third harmonic improves it. Adding the fifth improves it further. In the limit of infinitely many terms, the series converges to the square wave exactly. The sharp corners are produced by the constructive and destructive interference of infinitely many smooth waves.

> **The Gibbs phenomenon:** at points of discontinuity, the Fourier series always overshoots by about 9%, regardless of how many terms are included. The overshoot narrows but does not shrink as more terms are added. This matters in signal processing where sharp transitions produce ringing artefacts.

---

## Part Three — The Fourier Transform: Non-Periodic Signals

The Fourier series handles periodic functions. Most signals are not periodic — a spoken word, a gravitational wave pulse, a stock price over a month. The extension is to treat the signal as periodic with an infinitely long period. As `T → ∞`, the discrete set of harmonics becomes a continuous spectrum, the sum becomes an integral, and the result is the **Fourier transform**:

```
The Fourier Transform:

  F(ν)  =  ∫₋∞^∞  f(t) · e^(−2πiνt) dt

The Inverse Fourier Transform:

  f(t)  =  ∫₋∞^∞  F(ν) · e^(+2πiνt) dν

where:
  f(t)  = the original signal in the time domain
  F(ν)  = its representation in the frequency domain
  ν     = frequency (continuous)
  i     = √−1
```

`F(ν)` is complex-valued. Its magnitude `|F(ν)|` gives the amplitude of each frequency component. Its argument `arg(F(ν))` gives the phase. The complex exponential `e^(−2πiνt)` is shorthand for `cos(2πνt) − i·sin(2πνt)` via Euler's formula.

The transform is invertible. Going from the time domain to the frequency domain and back loses nothing. Both representations contain exactly the same information.

---

## Part Four — Properties That Make It Useful

**Convolution becomes multiplication.** Convolution in the time domain — used for filtering, blurring, mixing — is an O(n²) integral operation. In the frequency domain it becomes pointwise multiplication. Transform both signals, multiply, invert. The cost drops from O(n²) to O(n log n) via the FFT.

```
The Convolution Theorem:

  (f * g)(t)  =  ∫ f(τ) g(t−τ) dτ

  In frequency domain:  F{f * g}  =  F(ν) · G(ν)

  Convolution in time  ←→  Multiplication in frequency.
```

**Differentiation becomes multiplication by frequency.** The Fourier transform of `df/dt` is `2πiν · F(ν)`. Differential equations become algebraic equations in the frequency domain. This is how Fourier solved the heat equation.

**Parseval's theorem.** The total energy of a signal — the integral of its square — is the same whether computed in the time domain or the frequency domain. The transform preserves energy.

**The uncertainty principle.** A signal concentrated in time must be spread out in frequency, and vice versa. A Dirac delta (a perfect spike at one moment) has a completely flat frequency spectrum. A pure sine wave at one frequency extends across all time.

```
Time-Frequency Uncertainty:

  Δt · Δν  ≥  1/(4π)

  Narrow in time  →  Wide in frequency.
  Narrow in frequency  →  Wide in time.
```

This is the same mathematical result as Heisenberg's uncertainty principle in quantum mechanics — the same underlying theorem applied to a different context.

---

## Part Five — The Fast Fourier Transform

The **Discrete Fourier Transform (DFT)** applies to a finite sequence of N samples:

```
The Discrete Fourier Transform:

  X[k]  =  Σ  x[n] · e^(−2πikn/N)    for k = 0, 1, ..., N−1
           n=0
```

Computing this directly requires O(N²) operations. For N = one million, that is a trillion operations.

In 1965, James Cooley and John Tukey published an algorithm that computes the DFT in O(N log N) by exploiting a recursive symmetry in the formula. The DFT of length N splits into two DFTs of length N/2, each of which splits again, down to length 1. This is the **Fast Fourier Transform (FFT)**.

```
FFT Speedup:

  N = 1,000,000 samples

  DFT (naive):  N²       = 10¹²   operations
  FFT:          N log₂N  ≈ 2×10⁷  operations

  Speedup: ~50,000×

  On a CPU at 10⁹ operations per second:
    DFT: ~1000 seconds
    FFT: ~0.02 seconds
```

This made real-time signal processing practical. Without the FFT, noise cancellation, radar processing, seismic analysis, and MRI image reconstruction would not run at the speeds they currently do.

The same algorithm was found in unpublished notes by Carl Friedrich Gauss from around 1805, written while computing asteroid orbits. He did not publish it.

---

## Part Six — Where the Fourier Transform Appears

**Audio.** Sound is a pressure wave converted to a voltage signal. The Fourier transform converts it to a frequency spectrum. Equalisers operate in the frequency domain. MP3 compression uses a modified discrete cosine transform — a variant of the Fourier transform — to identify which frequency components are least audible and discard them.

**Image compression.** JPEG applies the **Discrete Cosine Transform** to 8×8 pixel blocks, then discards high-frequency coefficients that contribute little to perceived image quality. The blocking artefacts in heavily compressed JPEGs are the result of those missing high-frequency components.

**Medical imaging.** MRI scanners measure signals in **k-space** — the Fourier domain of the image. Magnetic field gradients cause different tissue locations to emit radio signals at different frequencies. The raw data is a sampled Fourier transform of tissue density. Reconstructing the image requires an inverse Fourier transform.

**Telecommunications.** OFDM (Orthogonal Frequency Division Multiplexing), the modulation scheme used in WiFi, LTE, and 5G, transmits data on many narrow orthogonal subcarriers simultaneously. The receiver uses an FFT to separate them.

**Options pricing.** Under models like Heston, the **characteristic function** of the log-price distribution — the Fourier transform of the probability density — often has a known closed form even when the density itself does not. The option price is recovered from the characteristic function via Fourier inversion. This is the **Carr-Madan method** (1999), which allows fast numerical pricing under stochastic volatility models using the FFT.

**Solving differential equations.** Transform a differential equation into the frequency domain, where derivatives become multiplications, solve the resulting algebraic equation, and invert. This works for the heat equation, the wave equation, Schrödinger's equation, and any PDE with constant coefficients.

---

## Part Seven — Where It Breaks Down

The Fourier transform is a **global** representation. Every frequency coefficient integrates over the entire signal. This works for **stationary** signals whose frequency content does not change over time.

Many signals are **non-stationary**. A piece of music changes chords and tempo. A seismic record is quiet before a fault rupture and violent after. An ECG changes during exercise. For these signals, the Fourier transform tells you which frequencies are present globally but not when they occur.

The **Short-Time Fourier Transform (STFT)** applies the Fourier transform to overlapping windowed segments, producing a **spectrogram** — a time-frequency representation showing how frequency content evolves over time.

The trade-off is the uncertainty principle: time resolution and frequency resolution cannot both be improved simultaneously. A short window gives good time localisation but blurred frequencies. A long window gives sharp frequency resolution but poor time localisation.

**Wavelets** are a different family of basis functions designed to be localised in both time and frequency, partially bypassing this trade-off. They are used in JPEG 2000, FBI fingerprint compression, and the signal processing pipeline at LIGO.

---

## Closing Thoughts — Why Sines Are the Natural Choice

The Fourier transform works because functions on an interval form a **vector space**, and sine and cosine functions at different frequencies form an **orthogonal basis** for that space. The Fourier coefficients are inner products — projections of the function onto each basis vector. The mechanics are the same as decomposing a 3D vector into x, y, z components.

Sines are the natural basis not by convention but because they are eigenfunctions of the differentiation operator. In problems where the fundamental equations are differential equations with constant coefficients — which covers a large portion of physics and engineering — sines are the functions that simplify the mathematics. Different settings produce different natural bases: Bessel functions, Legendre polynomials, spherical harmonics appear in cylindrical and spherical geometries. The Fourier basis is the right choice when the geometry is flat and the equations have constant coefficients.

---

*Further reading: Gilbert Strang's "Computational Science and Engineering" covers the FFT and its applications in depth. Brad Osgood's Stanford lecture notes on the Fourier transform are freely available. For the financial application, Carr and Madan's 1999 paper "Option Valuation Using the Fast Fourier Transform" is the primary reference.*
