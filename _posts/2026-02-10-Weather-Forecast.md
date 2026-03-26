---
title: Weather Forecasting
date: 2026-02-10 00:00:00 +1000
categories: [MATHEMATICS]
tags: [mathematics, modelling]     # TAG names should always be lowercase
description:  How Do We Forecast The Weather?
comments: false
author: Aryaan
math: true
mermaid: true
---
 
 # Why Is the Weather So Hard to Predict?

### Navier-Stokes, Chaos Theory, and the Mathematics of the Atmosphere

*On the equations that govern how fluids move — and why they cap what forecasting can ever do.*

---

We have satellites in orbit, supercomputers running tens of quadrillions of calculations per second, and sensors on every ocean buoy, weather balloon, and commercial aircraft on the planet. And yet, ask any meteorologist what the weather will be like in fourteen days and the answer will come with significant uncertainty attached.

This is not a failure of technology or the models. It is a mathematical limit built into the equations that govern how fluids move. The atmosphere is a fluid, and fluids are computationally expensive to simulate precisely.

What follows covers those equations, what they can do, why they eventually break down, and the methods that have been developed to work within those limits.

---

## Part One — The Atmosphere as a Fluid

Air is a fluid in the physics sense — a substance that deforms continuously under shear stress and flows rather than staying rigid. This means the atmosphere is governed by the same equations as water flowing through a pipe or air over an aircraft wing: the **Navier-Stokes equations**, written in their modern form in the mid-1800s by Claude-Louis Navier and George Gabriel Stokes.

```
The Navier-Stokes Equations (Incompressible Form):

  ρ(∂u/∂t + u·∇u)  =  −∇p  +  μ∇²u  +  F

  ∇·u = 0

where:
  ρ  = fluid density
  u  = velocity field (a vector at every point in space)
  p  = pressure field
  μ  = viscosity (how "sticky" the fluid is)
  F  = external forces (gravity, Coriolis effect from Earth's rotation)
  ∇  = the gradient operator — captures how things change in space
```

The first equation is Newton's second law applied to every infinitesimal parcel of fluid simultaneously. The left side is the fluid's acceleration, including the nonlinear term `u·∇u` which accounts for the fluid carrying itself along. The right side is the sum of forces: pressure gradients, viscous friction, and external forces like gravity.

The second equation — `∇·u = 0` — is the incompressibility condition. Fluid is not being created or destroyed anywhere; what flows in must flow out.

> **The Millennium Prize.** The Clay Mathematics Institute has offered $1,000,000 to anyone who can prove that smooth solutions to Navier-Stokes always exist in three dimensions — or find a counterexample where they break down. Nobody has done either. These equations are used daily in weather forecasting and aeronautics while simultaneously lacking a proof that they are always mathematically well-defined.

For weather forecasting, the equations are extended further. The atmosphere is compressible, transfers heat, involves moisture phase transitions, and sits on a rotating sphere. The full primitive equations used in operational models add thermodynamic equations, moisture equations, and Coriolis terms. Navier-Stokes is the underlying structure.

---

## Part Two — Discretising the Atmosphere: Turning Calculus into Computation

Navier-Stokes involves continuous functions defined at every point in three-dimensional space, evolving in continuous time. A computer works with finite lists of numbers, so the first task of numerical weather prediction is **discretisation** — dividing the atmosphere into a finite grid and approximating the continuous equations with finite differences or spectral methods.

The atmosphere becomes a three-dimensional grid of boxes, each perhaps 10 kilometres wide and 500 metres tall. Every box has a state: temperature, pressure, wind speed in three directions, humidity. At each timestep, every box's state is updated based on the equations and its neighbours' current states.

```
Finite Difference Approximation of a Derivative:

  ∂u/∂x  ≈  [u(x + Δx) − u(x)] / Δx

  As Δx → 0, this becomes exact.
  In practice, Δx is a few kilometres.
```

The current operational models — ECMWF's IFS, NOAA's GFS — run at horizontal resolutions of around 9 kilometres globally with around 130 vertical levels. That is roughly a billion grid points, stepped forward in time increments of minutes. Running a 10-day forecast takes close to an hour on one of the largest supercomputers in existence.

Halving the grid spacing does not just double the work. In three dimensions, and accounting for the smaller time step required for numerical stability, cutting grid spacing in half multiplies the computational cost by roughly **2⁴ = 16**. Going from 10km to 1km resolution globally is not ten times harder — it is closer to ten thousand times harder.

This is why phenomena like individual thunderstorms, turbulence, and small-scale convection cannot be resolved directly. They are **parameterised** — replaced with simplified statistical representations of their average effect on the larger-scale flow. Getting parameterisation schemes right is one of the main sources of model error and one of the most actively researched areas in meteorology.

---

## Part Three — The Chaos Problem: Lorenz and the Butterfly

In 1961, a meteorologist at MIT named Edward Lorenz was running a simplified weather simulation and wanted to rerun a particular forecast from the midpoint. He entered the state from a printout that showed the value as 0.506. The value stored in the computer's memory was 0.506127. The difference was less than one part in a thousand.

The two simulations started almost identically, stayed close for a while, and then diverged. By the end of the run they bore no resemblance to each other.

Lorenz had discovered what became the foundation of **chaos theory**.

```
The Lorenz System — A Simplified Convection Model:

  dx/dt  =  σ(y − x)
  dy/dt  =  x(ρ − z) − y
  dz/dt  =  xy − βz

  Standard parameters: σ = 10, ρ = 28, β = 8/3
  
  Two trajectories starting at (x, y, z) and (x + ε, y, z)
  diverge exponentially, regardless of how small ε is.
```

Three coupled differential equations produce **sensitive dependence on initial conditions** — small differences in starting state produce completely different trajectories after a short time.

The implications for weather forecasting are direct. Every measurement has error — every thermometer, radiosonde, and satellite retrieval. Those errors grow exponentially over time. For the real atmosphere, forecast errors double somewhere between every two and five days.

This sets the theoretical limit of deterministic weather forecasting at around **two weeks**. Beyond that, even a perfect model with perfect equations cannot produce a useful forecast, because it would require perfectly precise initial conditions, which are physically impossible to obtain.

> *"It used to be thought that the events that are not repeatable are random and that those that are repeatable are deterministic. It turns out that almost nothing is repeatable in nature."*
> — Edward Lorenz

Lorenz showed that deterministic systems — governed by equations with no randomness — can produce behaviour that is, for all practical purposes, unpredictable. Chaos is not noise. It is a structural property of the equations.

---

## Part Four — The Kalman Filter: Combining Observations with the Model

Every six hours, new observational data arrives from satellites, weather stations, radiosondes, aircraft, and ocean buoys — millions of measurements, slightly inconsistent with each other and with what the model predicted. The problem of combining this data with the model's prior state is called **data assimilation**, and the core mathematical tool is the **Kalman filter**.

The Kalman filter was developed by Rudolf Kálmán in 1960 for aerospace navigation. The idea is Bayesian: you have a prior belief about the state of the system (the model forecast), you receive a noisy observation, and you compute the update that minimises the expected error.

```
The Kalman Filter Update Step:

  Prior state:       x_f  (the forecast — what the model predicted)
  Observation:       y    (what was actually measured)
  
  Innovation:        d  =  y − H · x_f
                     (the gap between observation and prediction)
  
  Kalman Gain:       K  =  P_f · Hᵀ · (H · P_f · Hᵀ  +  R)⁻¹

  Analysis state:    x_a  =  x_f  +  K · d

where:
  H   = observation operator (maps model state to observation space)
  P_f = forecast error covariance (uncertainty about x_f)
  R   = observation error covariance (uncertainty about the measurements)
```

The Kalman gain `K` decides how much weight to give to the observation versus the forecast. Large forecast uncertainty relative to observation uncertainty means `K` is large — pull the model toward the observation. Small observation uncertainty relative to the model's confidence means `K` is small — keep the model as-is.

This is not a heuristic. It is the provably optimal linear update under Gaussian assumptions — the formula that minimises expected squared error.

In practice, the full Kalman filter is computationally intractable for weather forecasting. The state vector has around a billion elements, making the covariance matrix `P_f` a billion-by-billion matrix — impossible to store or invert. Operational forecasting uses approximations: **4D-Var** (an optimisation problem over a time window) and the **Ensemble Kalman Filter** (which estimates covariance from an ensemble of model runs). Improvements in data assimilation have historically produced as much forecast improvement as improvements to the model equations themselves.

---

## Part Five — Ensemble Forecasting: Quantifying Uncertainty

If small errors in initial conditions grow into large forecast errors, one response is to run the forecast multiple times with slightly different initial conditions and observe how much the results diverge.

This is **ensemble forecasting**, and it is now central to how uncertainty is communicated in operational meteorology.

Instead of a single forecast, you run fifty or a hundred — each starting from a slightly perturbed initial state. The spread of the ensemble tells you not just what the forecast is but how much confidence to place in it.

```
Ensemble Probability:

  P(event A) ≈ (number of ensemble members where A occurs) / (total members)

  e.g. If 38 of 50 members show rainfall > 10mm,
       the forecast probability of heavy rain is 38/50 = 76%
```

When ensemble members cluster tightly, the atmosphere is in a predictable regime — the flow pattern is stable and small perturbations have little effect. When they spread widely, forecast confidence is low and any specific deterministic forecast should be treated with scepticism.

The ECMWF ensemble runs 51 members — one unperturbed control and 50 perturbed members — twice daily. The perturbations are not arbitrary. They are constructed to capture the directions in state space where errors grow fastest, using **singular vectors** and **bred vectors** from linear algebra. The goal is to span the range of plausible atmospheric evolutions as efficiently as possible with a limited number of runs.

---

## Part Six — Machine Learning in Weather Forecasting

For roughly seventy years, numerical weather prediction was built on the physics-first approach: write down the equations, discretise them, solve numerically. In 2022 and 2023 that changed.

Researchers at Google DeepMind, Huawei, and NVIDIA trained large neural networks on ERA5 reanalysis data — the atmosphere's reconstructed state at every point on Earth, every six hours, from 1940 to the present. The results were that **GraphCast** (DeepMind), **Pangu-Weather** (Huawei), and **FourCastNet** (NVIDIA) all demonstrated medium-range forecast accuracy comparable to or exceeding ECMWF's IFS, running in a fraction of a second on a single GPU.

GraphCast uses a **graph neural network** on an icosahedral mesh, treating the atmosphere as a graph where nodes are locations and edges connect neighbouring points. Pangu-Weather uses a **3D Earth Attention Transformer**, applying attention across space and vertical levels simultaneously.

```
The ML forecasting pipeline:

  Input:   Atmospheric state at time t (and t−6 hours)
           Temperature, wind, humidity, pressure at ~37 vertical levels
           ~70 variables total on a global grid

  Model:   Graph neural network or transformer
           Trained on 40+ years of ERA5 reanalysis data
           Loss function: weighted RMSE over all variables and levels

  Output:  Predicted atmospheric state at time t+6 hours
           Autoregressive rollout gives multi-day forecasts
```

The limitation is that ML models are trained on historical atmospheric data. As the climate shifts, their performance could degrade in ways that are difficult to detect or diagnose, because they have never seen conditions outside their training distribution. Physics-based models express the actual equations of thermodynamics and fluid dynamics, which hold regardless of the climate state.

The most likely outcome is a hybrid approach: physics-based models providing structural constraints, ML models accelerating computation and improving parameterisation schemes.

---

## Part Seven — Why Two Weeks Is a Hard Limit

The two-week predictability horizon comes directly from the mathematics of chaos — specifically from the **Lyapunov exponent** of the atmospheric system.

The Lyapunov exponent `λ` measures the average rate at which nearby trajectories diverge. Two states differing by `ε₀` initially will differ by roughly `ε₀ · e^(λt)` after time `t`. For the atmosphere, `λ` is approximately 0.35 per day.

```
Error Growth Under Chaos:

  ε(t)  ≈  ε₀ · e^(λt)

  If initial error ε₀ ≈ 10⁻⁶ (measurement precision)
  And signal becomes noise when ε(t) ≈ 1 (full climatological variability)

  Then predictability limit T = ln(1/ε₀) / λ  ≈  ln(10⁶) / 0.35  ≈  40 days

  In practice, model imperfections and larger initial errors
  bring the realistic limit to 10–14 days.
```

Better observations reduce `ε₀`. Better models reduce early error growth. But `λ` is a property of the atmosphere itself — a consequence of the nonlinearity in the Navier-Stokes equations. It cannot be reduced through improved technology or modelling.

Some phenomena remain predictable beyond two weeks. Large-scale patterns like El Niño and the North Atlantic Oscillation involve the ocean, which has far more thermal inertia than the atmosphere, and their evolution can be tracked over months. Seasonal forecasts exploit these slower signals. But they cannot tell you about a specific day's weather — only whether a month or season is likely to run warmer or colder than average.

---

## Closing Thoughts — A Limit That Comes From the Physics

The two-week forecast wall is not a technology problem. The equations that govern the atmosphere are nonlinear, and that nonlinearity produces chaos. Chaos means that arbitrarily small initial errors grow exponentially. Growing errors mean that deterministic prediction has a finite horizon regardless of how good the model or how dense the observations.

The five-day forecast today is more accurate than the three-day forecast forty years ago. That improvement came from better models, better data assimilation, better ensemble methods, and now machine learning. Each advance extracts more value from the available data, but none of them changes the underlying limit.

Understanding where the limit comes from — rather than treating forecast uncertainty as a sign of inadequate effort — is the more useful framing.

---

*Further reading: Edward Lorenz's 1963 paper "Deterministic Nonperiodic Flow" is accessible and worth reading as a primary source. Eugenia Kalnay's "Atmospheric Modeling, Data Assimilation and Predictability" is the standard graduate-level reference. The GraphCast paper (Lam et al., 2023) and the FourCastNet paper cover the two main ML architectural approaches.*
