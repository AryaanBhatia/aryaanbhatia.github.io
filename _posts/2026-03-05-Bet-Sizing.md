---
title: Weather Forecasting
date: 2026-03-05 00:00:00 +1000
categories: [MATHEMATICS]
tags: [mathematics, modelling]     # TAG names should always be lowercase
description:  Bet Sizing
comments: false
author: Aryaan
math: true
mermaid: true
---

# How Much Should You Bet?

### The Kelly Criterion, Optimal Sizing, and Why Almost Everyone Bets Too Much

*On the formula that started at a Bell Labs telephone line, moved to the blackjack tables of Las Vegas, and ended up running money at some of the most successful hedge funds in history.*

---

If you have a genuine mathematical edge, how much of your money should you bet on it?

The instinct is to bet as much as possible. The instinct is wrong, and wrong in a specific way — bet too much on any sequence of uncertain outcomes, no matter how favourable, and compounding will eventually destroy you. Not might. Will.

The correct answer — the fraction that maximises the long-run growth rate of your wealth — was worked out in 1956 by a physicist at Bell Labs named John Larry Kelly Jr. He was not thinking about gambling. He was thinking about information theory and the capacity of noisy telephone lines. The connection between the two, when Claude Shannon pointed it out, turned out to be one of the more productive accidents in applied mathematics.

---

## Part One — The Setup: A Coin with an Edge

Start with the simplest case. A coin lands heads with probability `p` and tails with probability `q = 1 − p`. When it lands heads, you win your bet. When it lands tails, you lose it. Assume `p > 0.5`.

Take `p = 0.6`. The expected value of betting one dollar is `0.6 × $1 − 0.4 × $1 = $0.20`. You are making twenty cents per dollar bet in expectation.

Now the question: what fraction `f` of your current bankroll should you bet on each flip?

With starting bankroll `W₀`, after N flips — W wins and L losses — your bankroll is:

```
Bankroll After N Flips:

  W_N  =  W₀ · (1 + f)^W · (1 − f)^L

  where:
    f  = fraction of bankroll bet each round
    W  = number of wins
    L  = number of losses
```

Every win multiplies your bankroll by `(1 + f)`. Every loss multiplies by `(1 − f)`. These terms multiply together — compound growth. To find the optimal `f`, maximise the long-run growth rate per flip:

```
Growth Rate Per Flip:

  G(f)  =  p · ln(1 + f)  +  q · ln(1 − f)

  Differentiate and set to zero:

    dG/df  =  p/(1 + f)  −  q/(1 − f)  =  0

  Solving:

    p(1 − f)  =  q(1 + f)
    p − q     =  f(p + q)  =  f

  Therefore:   f*  =  p − q
```

For `p = 0.6`, `q = 0.4`: `f* = 0.20`. Bet 20% of your bankroll each flip.

This is the **Kelly Criterion** in its simplest form — the optimal fraction is the edge expressed as a probability difference.

---

## Part Two — The General Formula: When Odds Differ From Evens

The simple case assumes you win or lose exactly your stake. In practice the payoff odds matter. Let `b` be the net odds on a winning bet: you win `$b` for every `$1` staked.

```
The Kelly Criterion — General Form:

  f*  =  (bp − q) / b

       =  p  −  q/b

  where:
    p  = probability of winning
    q  = probability of losing  (= 1 − p)
    b  = net odds (win $b per $1 staked)

  Equivalently:

  f*  =  Edge / Odds
       =  Expected profit per dollar bet / Net odds
```

Kelly says to size the bet proportional to your edge and inversely proportional to the potential payoff. A large potential payoff means you need to bet less to extract the same value — the asymmetry of the payoff does more of the work.

Example: a horse race where the true probability of winning is 30% and the track offers 4-to-1 odds:

```
f*  =  (4 × 0.30 − 0.70) / 4
     =  0.50 / 4
     =  0.125

Bet 12.5% of bankroll.
Expected value per dollar: $0.50
```

A 50% expected profit per dollar, but you still only bet 12.5% of your roll. A loss costs you the full stake — the downside is not symmetric with the upside.

---

## Part Three — What Happens If You Bet Too Much

The Kelly fraction is the unique fraction that maximises long-run growth rate. Bet less and you leave growth on the table. Bet more and you reduce your long-run wealth — even though your expected value per bet is still positive.

```
Growth Rate at Different Fractions of Kelly:

  G(f)  =  p · ln(1 + f)  +  q · ln(1 − f)

  For p = 0.6, q = 0.4, f* = 0.20:

    f = 0.00 (no bet):     G = 0.000
    f = 0.10 (half Kelly): G = 0.0201
    f = 0.20 (full Kelly): G = 0.0290  ← maximum
    f = 0.30 (1.5x Kelly): G = 0.0255
    f = 0.40 (2x Kelly):   G = 0.0163
    f = 0.60 (3x Kelly):   G = −0.0204  ← ruin zone
    f = 1.00 (all-in):     G = −∞
```

The growth rate curve is concave with a maximum at `f*`. The zero crossing is exactly at `f = 2f*`. Beyond double Kelly, long-run wealth goes to zero regardless of the size of the edge.

Underbetting is conservative. Overbetting is compounding damage — the expected value of each individual bet is positive, but the geometric drag from high variance outweighs it over a long sequence.

> The Kelly fraction is the point at which the marginal benefit of betting more — higher expected gain — exactly equals the marginal cost — increased variance that reduces the geometric growth rate. Beyond that point, additional bet size costs more in geometric drag than it adds in expected return.

---

## Part Four — Ed Thorp and Blackjack

The first person to apply Kelly's formula as a practical tool was Ed Thorp, a mathematics professor who had independently developed card counting in blackjack.

Blackjack gives a player a varying edge because cards are dealt from a finite shoe without replacement. When the remaining deck is rich in tens and aces, the player has positive expected value. Thorp knew how to track this and knew the approximate edge at each point in the shoe.

Kelly's formula gave him a sizing rule: bet a fraction of bankroll proportional to the current edge. When the count indicated a small edge, bet small. When it indicated a large edge, bet more. When the count was negative, bet the minimum.

```
Kelly Betting in Blackjack (Illustrative):

  True count +1:  edge ≈ 0.5%,   Kelly fraction ≈ 0.005
  True count +2:  edge ≈ 1.0%,   Kelly fraction ≈ 0.010
  True count +3:  edge ≈ 1.5%,   Kelly fraction ≈ 0.015
  True count +5:  edge ≈ 2.5%,   Kelly fraction ≈ 0.025
  True count −1:  edge negative,  bet minimum
```

Thorp published this in 1962 in *Beat the Dealer*, proved it worked at Las Vegas casinos, and was eventually banned on sight. He then moved to financial markets, founding Princeton Newport Partners in 1969. The fund ran for twenty years with annualised returns of around 19% after fees, with consistency Thorp attributed partly to disciplined Kelly-based position sizing.

---

## Part Five — Kelly in Trading

The blackjack setting is clean: known probabilities, discrete outcomes. Trading is not. Payoffs are continuous — a position produces a distribution of returns rather than a fixed multiple. Probabilities and expected returns are not known — they have to be estimated from historical data.

For continuous return distributions, the Kelly criterion takes the form:

```
Kelly Criterion for Continuous Returns:

  If a trade has:
    μ  = expected return
    σ² = variance of return

  Then the Kelly-optimal allocation is:

    f*  =  μ / σ²

  In terms of the Sharpe ratio S = μ/σ:

    f*  =  S / σ  =  μ / σ²
```

The allocation is proportional to expected return and inversely proportional to variance. Variance is penalised directly because it reduces compound growth through geometric drag.

```
The Geometric Drag:

  Arithmetic mean return:   μ
  Geometric mean return:    μ − σ²/2   (approximately)

  A strategy with μ = 10%, σ = 30%:
    Arithmetic return: 10%
    Geometric return:  10% − 4.5% = 5.5%

  The 4.5% difference is the variance drag. Kelly sizing accounts for this exactly.
```

For a portfolio of multiple positions:

```
Kelly for a Multi-Asset Portfolio:

  f*  =  Σ⁻¹ · μ

  where:
    μ  = vector of expected excess returns
    Σ  = covariance matrix of returns
    f* = vector of Kelly-optimal position sizes
```

The covariance matrix matters because correlated positions share risk. Two positions that move together should be treated as one larger position for sizing purposes. This expression is also proportional to the Markowitz mean-variance optimal portfolio — both frameworks are solving the same optimisation.

---

## Part Six — The Practical Problem: You Don't Know Your Edge

The Kelly formula requires knowing `p` and `b`. In a casino these can be calculated from the rules. In financial markets they have to be estimated, and those estimates will be wrong.

The consequences are asymmetric. Overestimating your edge means betting too much. Overbetting under Kelly causes compounding losses. And overestimating edge is the default failure mode — it is straightforward to find patterns in historical data that do not persist out of sample.

```
Sensitivity of Kelly to Edge Estimation Errors:

  True edge:           p = 0.52, b = 1   →  f* = 0.04  (4%)
  Overestimate by 5pp: p = 0.57          →  f* = 0.14  (14%)

  You bet 3.5x the correct amount — well into the overbetting zone.
  Edge estimation errors of this magnitude are routine in practice.
```

Most practitioners who apply Kelly in markets use a scaled-down version — **fractional Kelly**, most commonly half Kelly.

```
Fractional Kelly:

  Bet:   α · f*   where 0 < α ≤ 1

    α = 1.0  →  Full Kelly   (maximum growth rate, maximum drawdowns)
    α = 0.5  →  Half Kelly   (~75% of maximum growth, lower drawdowns)
    α = 0.25 →  Quarter Kelly (conservative)
```

Half Kelly has a mathematical justification. If your edge estimate has uncertainty of order `ε`, the optimal bet under that uncertainty is approximately `f* · (1 − ε²)`, which is close to `f*/2` for typical estimation errors. Half Kelly is a rough approximation to the Bayesian Kelly — the correct Kelly fraction when you have a distribution over possible edges rather than a known point estimate.

---

## Part Seven — The Ruin of the Overleveraged

The history of trading contains repeated examples of genuine edges destroyed through position sizing.

Long-Term Capital Management had real edges in fixed income relative value. They were levered between 25:1 and 50:1 at their peak. When Russia defaulted in 1998 and correlations moved together, the losses came close to threatening the broader financial system. The edge was real. The sizing was not calibrated to the actual risk.

```
Kelly Ruin Probabilities:

  Full Kelly — probability of ever reaching fraction x of starting bankroll:

    P(dropping to x)  =  x

    50% chance of halving at some point.
    10% chance of losing 90%.
    (Long-run recovery in expectation, but the path is highly volatile.)

  Half Kelly:

    P(dropping to x)  ≈  x²

    25% chance of halving.
    ~1% chance of losing 90%.
```

The Kelly path is not smooth. Large drawdowns are expected, not exceptional. The practical constraint is whether you will maintain the strategy through a 40% or 50% drawdown, or whether you will cut the position or increase it at the wrong time. A half Kelly strategy you maintain through drawdowns will outperform a full Kelly strategy abandoned at the first rough patch.

---

## Part Eight — The Connection to Information Theory

The Kelly criterion and Shannon's information theory are not just historically connected — they are mathematically the same problem.

Shannon's channel capacity gives the maximum rate at which information can be transmitted over a noisy channel. Kelly's growth rate gives the maximum rate at which wealth can be extracted from an edge. The two quantities are equal under the same mathematical structure. If you have no edge, the correct bet is zero — you have no information to act on. If you have an edge, the Kelly fraction translates that information into wealth at the maximum rate the mathematics allows.

```
The Mathematics of Compounding Asymmetry:

  Start: $100
  Up 50% then Down 50%:   $100 → $150 → $75
  Down 50% then Up 50%:   $100 → $50  → $75

  Arithmetic average return: 0%
  Geometric return: −13.4%

  Volatility erodes compound wealth below the arithmetic mean.
  Losing 50% requires a 100% gain just to return to even.
  Kelly is the exact correction for this asymmetry.
```

Maximising expected value in a single bet and maximising expected log wealth over a sequence of bets are different problems. Kelly solves the second one.

---

## Closing Thoughts — Knowing What You Don't Know

The Kelly criterion tells you how much to bet given that you know your edge. The harder problem is honestly assessing whether you have an edge and how large it is.

Most people in gambling and trading overestimate their edge — from fitting models to historical data and mistaking in-sample performance for out-of-sample edge, drawing conclusions from small samples, and conflating recent returns with persistent skill.

The Kelly framework is particularly unforgiving of this. Overbetting is where ruin lives, and overestimating edge leads directly to overbetting. Getting the inputs right — calibrating estimates conservatively, accounting for model uncertainty, and using fractional Kelly as a buffer against estimation error — is the harder and more important part of applying the criterion in practice.

---

*Further reading: Ed Thorp's autobiography "A Man for All Markets" covers the blackjack and trading applications. His 2008 paper "The Kelly Criterion in Blackjack, Sports Betting, and the Stock Market" is the detailed technical treatment. Kelly's original 1956 Bell Labs paper "A New Interpretation of Information Rate" is short and readable. William Poundstone's "Fortune's Formula" covers the full history. Ralph Vince's "The Mathematics of Money Management" extends Kelly into multi-asset trading.*
