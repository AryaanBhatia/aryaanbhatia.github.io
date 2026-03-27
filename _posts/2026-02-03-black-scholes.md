---
title: Black-Scholes Options Pricing
date: 2026-02-03 00:00:00 +1000
categories: [MATHEMATICS]
tags: [mathematics, finance]     # TAG names should always be lowercase
description: Intro to Math in Finance
comments: false
author: Aryaan
math: true
mermaid: true
---

## Options

A financial instrument investors use where they acquire the right to purchase or sell an underlying asset at a later date for an agreed upon price.  The price at which they agree to either buy or sell the underlying asset is called the strike price.  Other aspects included the time to maturity, the value ofthe stock, the risk-free rate of return, and the volatility of the underlying asset. 

## Black Scholes Formula

Before you can price an option, you need a model of how the underlying stock actually moves. And this is where the first big decision gets made.

Black and Scholes chose to model stocks using something called **Geometric Brownian Motion**, or GBM.

Mathematically, this looks like:

```
dS = μ · S · dt  +  σ · S · dW

where:
  μ  = drift (the expected return of the stock)
  σ  = volatility (how noisy the stock is)
  dW = a tiny random shock drawn from a normal distribution
```

The thing about this model is that it's *multiplicative*, not additive. The stock can't go below zero (because you're always multiplying by the current price, not adding to it). It grows proportionally, which matches how real prices actually behave. A $1 move means something very different for a $10 stock than a $500 one, GBM captures this automatically.

> **A quick note on "Geometric":** it just means the randomness scales with the price level. A $1 swing matters a lot more to a $10 stock than a $500 stock. This proportionality is one of the more realistic features of the model.

The random part, the `dW`, is drawn fresh from a normal distribution at every instant, meaning tomorrow's move is entirely independent of today's.

---

## Part Two — Itô's Lemma: The Chain Rule That Wasn't Enough

Here's the problem. We have an equation for how the *stock* moves. But we care about how the *option* moves, the option whose value depends on the stock price in a nonlinear, time-sensitive way. We need to figure out how a function of a random process evolves over time.

In normal calculus, this is just the chain rule. If `f(x)` is a smooth function and `x` changes a little, then `df = f'(x) dx`. Simple. But here's the catch: Brownian motion is *nowhere differentiable*. It's infinitely jagged. The ordinary chain rule breaks down completely.

Kiyosi Itô worked this out in the 1940s, and his result, **Itô's Lemma**, is essentially a corrected chain rule for stochastic processes. It adds one extra term that accounts for the fact that `(dW)²` is not negligible the way `(dx)²` would be in ordinary calculus. It's actually equal to `dt`. This seems like a small technical detail, but it changes everything.

```
Itô's Lemma — For Any Smooth Function V(S, t):

dV = (∂V/∂t) dt  +  (∂V/∂S) dS  +  ½ · (∂²V/∂S²) · (dS)²

The last term, the Itô correction, is what makes this different from ordinary calculus.
Since (dS)² = σ²S² dt, this simplifies to:

dV = [∂V/∂t  +  μS·(∂V/∂S)  +  ½σ²S²·(∂²V/∂S²)] dt  +  σS·(∂V/∂S) dW
```

What this tells us is how the option price `V` evolves over time. It has a deterministic part (everything multiplied by `dt`) and a random part (everything multiplied by `dW`). That random part is the problem, it's what makes pricing hard.

---

## Part Three — The Trick: Build a Portfolio That Has No Risk

Suppose you sell one option (pocketing its price `V`) and simultaneously buy `Δ = ∂V/∂S` shares of the stock. This combination is your portfolio. Now watch what happens when you look at how the portfolio changes in value:

```
The Hedged Portfolio:

  Π  =  −V  +  (∂V/∂S) · S
  dΠ =  −dV  +  (∂V/∂S) · dS

Substitute Itô's Lemma for dV, expand dS... the dW terms cancel:

  dΠ = [−∂V/∂t  −  ½σ²S²·(∂²V/∂S²)] dt

No dW anywhere. The portfolio is completely deterministic.
```

The random term vanishes completely. The reason is that both the option and the shares respond to the same source of randomness (`dW`), and by choosing exactly `∂V/∂S` shares, their fluctuations cancel out perfectly. This is the **delta hedge**. That specific number of shares, `∂V/∂S`, is called the *delta* of the option, and it's the first of the Greeks.

Now: if the portfolio is risk-free, it must earn exactly the risk-free rate `r`. If it earned more, you'd have a money machine. If it earned less, you'd just buy risk-free bonds. Both contradict the idea of efficient markets. Setting those equal gives us the **Black-Scholes PDE**:

```
The Black-Scholes Partial Differential Equation:

∂V/∂t  +  rS·(∂V/∂S)  +  ½σ²S²·(∂²V/∂S²)  −  rV  =  0

This single equation governs the price of any European derivative on this stock.
Different boundary conditions give you calls, puts, digitals, barriers, anything.
```

---

## Part Four — Solving It: The Formula That Made History

This is where the boundary condition comes in: at expiry, a call option is worth exactly `max(S − K, 0)`. If the stock ends above the strike, you exercise. If not, you don't. That kink in the payoff is all the information you need.

Through a substitution that transforms the Black-Scholes PDE into the classical **heat equation** from physics (a well-studied equation describing how heat diffuses through material), the solution falls out cleanly:

```
The Black-Scholes Formula:

  Call  =  S · N(d₁)  −  K · e^(−rT) · N(d₂)
  Put   =  K · e^(−rT) · N(−d₂)  −  S · N(−d₁)

where:
  d₁ = [ln(S/K)  +  (r + σ²/2) · T] / (σ√T)
  d₂ = d₁ − σ√T
  N(·) = cumulative standard normal distribution function
```

`S · N(d₁)` is roughly the expected stock price if the option finishes in the money, weighted by the probability of that happening. `K · e^(−rT) · N(d₂)` is the present value of what you'd pay to buy the stock, also weighted by that probability. You're subtracting the cost from the expected benefit. The difference is what the call is worth today.

And critically, notice what's *not* in the formula. The expected return of the stock, `μ`, is completely absent. You don't need to know whether the stock is going up or down on average. You only need to know how volatile it is, how much time is left, and what interest rates are. That's the no-arbitrage argument doing its work, and it's remarkable every time you think about it.

---

## Part Five — The Assumptions: Let's Be Honest About Them

| Assumption | Reality Check | Verdict |
|---|---|---|
| **Volatility is constant.** The formula takes a single σ and assumes it never changes. | Volatility clusters. The "volatility smile", the fact that implied vol varies by strike, is direct empirical evidence this assumption fails. | ❌ Clearly False |
| **Stock returns are normally distributed.** GBM implies log-returns follow a perfect bell curve with thin tails. | Real return distributions have fat tails, extreme events happen far more often than a Gaussian predicts. Black Monday 1987, the financial crisis, COVID. These aren't "six sigma events." They happen every decade. | ❌ Clearly False |
| **Markets are frictionless.** No transaction costs, no taxes, continuous trading possible at all times. | For liquid equity options at major exchanges, this is close enough. For smaller markets, or in crises when liquidity disappears, this breaks down badly. | ⚠️ Approximately True |
| **The risk-free rate is constant.** A single r governs borrowing and lending across the option's entire life. | For short-dated options this matters little. For longer-dated options (LEAPS), interest rate uncertainty becomes genuinely important. | ⚠️ Roughly Fine |
| **European exercise only.** The option can only be exercised at expiry, not before. | The formula is explicitly for European options. American options require different methods, usually numerical trees or simulation. | ✅ Explicitly Stated |
| **No dividends.** The stock doesn't pay dividends during the option's life. | Adding a continuous dividend yield q is straightforward, just replace S with Se^(−qT) throughout. | ✅ Easily Extended |

The two that really matter are the first two, constant volatility and normally distributed returns.

---

## Part Six — Letting Volatility Breathe: The Heston Model

The most obvious fix for Black-Scholes is also the most natural one: what if we just let volatility be random too? Steven Heston asked exactly this in 1993, and the model he produced is now one of the workhorses of options desks everywhere.

The core idea is that volatility, or technically, variance (volatility squared), follows its own stochastic process, one that *mean-reverts*. When volatility gets very high, it tends to come back down. When it gets very low, it drifts back up. This matches observed market behaviour much better than a constant σ.

```
The Heston Model, Two Coupled Stochastic Processes:

  dS  =  μ S dt  +  √v · S · dW^S
  dv  =  κ(θ − v) dt  +  ξ√v · dW^v

dW^S and dW^v are correlated with coefficient ρ
(i.e. stock moves and vol moves are not independent)
```

There are five parameters now, each doing real conceptual work:

- **v₀, Initial Variance.** Where volatility starts today. If today's implied vol is 20%, set v₀ = 0.04 (= 0.20²).

- **κ, Mean Reversion Speed.** How aggressively volatility is pulled back toward its long-run level. High κ = fast reversion, low κ = slow. Think of it as the elasticity of the vol process.

- **θ, Long-Run Variance.** Where volatility gravitates toward over time. This is your long-term vol view, the equilibrium the market "wants" to settle at.

- **ξ, Vol of Vol.** How volatile the volatility is. A high ξ means vol jumps around a lot. This parameter is largely responsible for the curvature (smile) of the implied vol surface.

- **ρ, Stock-Vol Correlation.** Almost always negative (around −0.7 for equities). Stocks tend to fall as volatility spikes, the "leverage effect." ρ drives the *skew* of the vol surface.

There's a neat stability condition called the **Feller condition**: `2κθ > ξ²`. If this holds, variance stays strictly positive, the vol process can never reach zero. If it's violated, you can get negative variance in your simulation, which is a problem. (Real implementations clip it to zero, but it's worth checking your parameters upfront.)

### Why Heston Doesn't Have a Simple Formula

Black-Scholes has a closed-form solution because the constant-vol PDE reduces to the heat equation, which has been solved analytically for centuries. Heston's PDE is *coupled*, the stock equation and the variance equation interact, so no such clean solution exists in the same way.

There *is* a semi-closed-form solution involving characteristic functions and numerical integration, which is fast and widely used in practice. But the most intuitive way to understand Heston is via **Monte Carlo simulation**: simulate thousands of (S, v) paths through time, compute the option payoff at the end of each, average them all, and discount back to today.

The price Heston produces is subtly but meaningfully different from Black-Scholes, even when you feed in the same "average" volatility. The randomness in vol itself adds value to options, especially to far out-of-the-money ones, because a volatile vol means there's a higher chance of vol spiking right when you need the option to pay off. This is exactly the effect that Black-Scholes misses.

---

## Part Seven — Forgetting the Formula: Neural Networks as Pricers

Here's a different way to think about the problem entirely. Forget deriving a formula from first principles. Forget stochastic differential equations. What if you just asked: *can we learn the pricing function directly from data?*

This is the neural network approach, and it's genuinely interesting, not because it's more principled than Black-Scholes or Heston, but because it's more *flexible*. You're not committing to a particular model of how the world works. You're just saying: here are inputs, here are outputs, learn the mapping.

### What the Network Actually Learns

The inputs to our pricer are the things that determine an option's value: **log-moneyness** (how in- or out-of-the-money the option is, expressed as `log(S/K)`), **time to expiry**, **the risk-free rate**, and **implied volatility**. The outputs are the call and put prices as a fraction of the current stock price.

We use log-moneyness rather than raw S and K separately because of a property of Black-Scholes: the price scales proportionally with the stock price. If you double S and K simultaneously, the option price doubles. So the formula really only depends on their ratio, not their individual levels. A network trained on $100 stocks generalises correctly to $185 stocks, as long as we express moneyness the right way. This is the kind of structural insight that makes the difference between a network that works and one that blows up the moment you ask it to price an unfamiliar stock.

### So Why Bother, If We Already Have Black-Scholes?

Fair question. The honest answer is: for replicating Black-Scholes specifically, you don't need a neural network, the formula is right there. But this is a proof of concept. The same architecture, retrained on *real market prices* instead of BS-generated prices, would automatically capture the volatility smile, skew, and any other systematic departure from the BS world. No new model derivation required. No assumptions to defend.

There's also a speed argument. Monte Carlo under Heston might take seconds per price. A trained neural network predicts in microseconds. For a derivatives desk repricing thousands of positions in real time as markets move, that difference is enormous.

---

Here is a descriprion of the Black-Scholes formula:

$$
\begin{equation}
C(S,t) = S_0 \, N(d_1) - K e^{-rT} \, N(d_2)
\label{eq:black_scholes_call}
\end{equation}
$$

$$
\begin{equation}
P(S,t) = K e^{-rT}\, N(-d_2) - S_0\, N(-d_1)
\label{eq:black_scholes_put}
\end{equation}
$$

$$
\begin{equation}
d_1 = \frac{\ln\!\left(\frac{S_0}{K}\right) + \left(r + \frac{1}{2}\sigma^2\right)T}{\sigma\sqrt{T}}
\label{eq:d1}
\end{equation}
$$

$$
\begin{equation}
d_2 = d_1 - \sigma\sqrt{T}
     = \frac{\ln\!\left(\frac{S_0}{K}\right) + \left(r - \frac{1}{2}\sigma^2\right)T}{\sigma\sqrt{T}}
\label{eq:d2}
\end{equation}
$$

## The Greeks

#### Delta:

$$
\begin{equation}
\Delta_c = N(d_1)
\label{eq:delta_call}
\end{equation}
$$

$$
\begin{equation}
\Delta_p = N(d_1) - 1
\label{eq:delta_put}
\end{equation}
$$

#### Gamma (Same for call and put):

$$
\begin{equation}
\Gamma = \frac{N'(d_1)}{S_0 \sigma \sqrt{T}}
\label{eq:gamma}
\end{equation}
$$

where:

$$
N'(d_1) = \frac{1}{\sqrt{2\pi}} e^{-d_1^2/2}
$$


#### Vega (Same for call and put):

$$
\begin{equation}
\text{Vega} = S_0 \sqrt{T}\, N'(d_1)
\label{eq:vega}
\end{equation}
$$


#### Theta:

$$
\begin{equation}
\Theta_c =
-\frac{S_0 N'(d_1)\sigma}{2\sqrt{T}}
- rK e^{-rT} N(d_2)
\label{eq:theta_call}
\end{equation}
$$

$$
\begin{equation}
\Theta_p =
-\frac{S_0 N'(d_1)\sigma}{2\sqrt{T}}
+ rK e^{-rT} N(-d_2)
\label{eq:theta_put}
\end{equation}
$$


#### Rho:

$$
\begin{equation}
\rho_c = K T e^{-rT} N(d_2)
\label{eq:rho_call}
\end{equation}
$$

$$
\begin{equation}
\rho_p = -K T e^{-rT} N(-d_2)
\label{eq:rho_put}
\end{equation}
$$

## Python Implementation

The python implementation is in my GitHub.