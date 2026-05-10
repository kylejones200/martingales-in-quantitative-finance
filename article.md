---
author: "Kyle Jones"
date_published: "June 17, 2025"
date_exported_from_medium: "November 10, 2025"
canonical_link: "https://medium.com/@kyle-t-jones/martingales-in-quantitative-finance-2edba81c22a5"
---

# Martingales in Quantitative Finance Exploring conditional expectation, measure change, and the math behind
no-arbitrage

### Martingales in Quantitative Finance 

#### Exploring conditional expectation, measure change, and the math behind no-arbitrage
Modern finance rests on the idea of a fair game. No hidden drift, no arbitrage. In mathematics, that idea is a *martingale*. In finance, it's the risk-neutral world. This chapter introduces the concept of a martingale and builds the tools needed to work with it --- probability, measure theory, conditional expectation, and stochastic calculus. Martingales tie together pricing, hedging, and the absence of arbitrage.

Start with the binomial tree. At each step, the price moves up by a factor u or down by d. Let S_0 be the starting price, and suppose the risk-free rate is rr. Then, the no-arbitrage condition defines a risk-neutral probability:


This turns the expected discounted price into a martingale:


The idea generalizes: if you discount properly and take expectations under the right measure, prices become martingales.


<figcaption>The simulation uses risk-neutral probabilities to create a fair game. This illustrates the martingale property in a discrete setting.</figcaption>


To formalize this, define a probability space:

(Ω,F,P)

Where:

- Ω: the sample space
- F: the sigma-algebra (set of measurable events)
- P: the probability measure

Add a filtration {Ft}t≥0, which encodes the information available up to time tt. A stochastic process X_t is *adapted* if each X_t is measurable with respect to F_t.

This setup lets you define martingales, pricing rules, and dynamics rigorously.

### Conditional and Unconditional Expectation
The expectation operator reflects the average under uncertainty. An unconditional expectation gives the full average:


A conditional expectation restricts this to what's known at time t:


You price by taking the expected payoff under the risk-neutral measure, conditional on current information. If you price options with no arbitrage, you're doing this --- whether you say so or not.

### Change of Measure and the Radon-Nikodym Derivative
Sometimes, the original measure P does not work for pricing. We shift to a new measure Q, typically the risk-neutral measure. This shift is done using the Radon-Nikodym derivative


.

The derivative is a process Z_t such that:


This lets us price under Q by weighting paths under P. The choice of measure defines how you interpret the world.

### Martingales and Itô Calculus
A martingale M_t satisfies:


In finance, the discounted price process under Q is a martingale. Using Itô's calculus, we can verify whether a given process has the martingale property. For example, geometric Brownian motion discounted at the risk-free rate becomes a martingale under the risk-neutral measure.

Let S_t be a stock price following:


Discount by e\^{-rt}, change the measure so that drift becomes r, and the result is a martingale under Q.

### A Detour: More Itô Calculus
To explore martingales deeper, expand your Itô toolbox. Consider the function f(X_t, t) where X_t follows an Itô process. Apply Itô's Lemma:


To test if f(X_t, t) is a martingale, check if the drift term vanishes. Many martingales emerge by applying this logic to exponential functions of Brownian motion.

### Exponential Martingales and Girsanov's Theorem
A key result in finance is the exponential martingale:


<figcaption>The funciton shows how to construct a martingale from Brownian motion. This is the Radon-Nikodym derivative for measure change.</figcaption>


This process is a martingale under P. Girsanov's Theorem uses it to shift the drift of Brownian motion. Under the new measure Q defined by Z_t, the process:


is a Brownian motion under Q. This is the foundation of risk-neutral pricing. You change the drift of your stochastic process to match the market price of risk. The math remains consistent because the exponential martingale guarantees the validity of the measure change.


<figcaption>The Girsanov transformation shifts Brownian motion by a linear drift. Under the new measure, this shifted process behaves like standard Brownian motion. This enables risk-neutral valuation by removing the drift from the physical measure.</figcaption>

A martingale is the structure behind modern asset pricing. We start with a discrete model and build up to continuous processes. Along the way, we define probability spaces, work with conditional expectations, and change the measure using the Radon-Nikodym derivative.

Martingales let us interpret prices, simulate paths, and prove no-arbitrage conditions. They show that finance is not about guessing the future. It is about weighting it correctly.

```python
import numpy as np
import matplotlib.pyplot as plt

plt.rcParams.update({
    'axes.grid': False,
    "font.family": "serif",
    "axes.spines.top": False,
    "axes.spines.right": False
})

# Binomial Model with risk-neutral probabilities
def simulate_binomial_tree(S0, u, d, r, steps):
    q = (1 + r - d) / (u - d)
    paths = [S0]
    for _ in range(steps):
        move = np.random.choice([u, d], p=[q, 1 - q])
        paths.append(paths[-1] * move)
    return paths

# Brownian Motion and exponential martingale
def simulate_exponential_martingale(theta, T, steps):
    dt = T / steps
    W = np.cumsum(np.random.normal(0, np.sqrt(dt), size=steps))
    W = np.insert(W, 0, 0)
    t = np.linspace(0, T, steps + 1)
    Z = np.exp(-theta * W - 0.5 * theta**2 * t)
    return t, W, Z

# Girsanov: original and shifted Brownian motion
def simulate_girsanov(theta, T, steps):
    dt = T / steps
    W = np.cumsum(np.random.normal(0, np.sqrt(dt), size=steps))
    W = np.insert(W, 0, 0)
    t = np.linspace(0, T, steps + 1)
    W_tilde = W + theta * t
    return t, W, W_tilde

# Plotting the binomial model path
binomial_path = simulate_binomial_tree(S0=100, u=1.1, d=0.9, r=0.05, steps=50)

plt.figure(figsize=(10, 4))
plt.plot(binomial_path, label="Binomial Tree Path (Risk-Neutral)")
plt.title("Binomial Model Path under Risk-Neutral Measure")
plt.xlabel("Step")
plt.ylabel("Price")
plt.savefig("binomial_path.png")
plt.show()

# Plot exponential martingale
t, W, Z = simulate_exponential_martingale(theta=0.7, T=1, steps=1000)

plt.figure(figsize=(10, 4))
plt.plot(t, Z, label="Exponential Martingale")
plt.title("Exponential Martingale from Brownian Motion")
plt.xlabel("Time")
plt.ylabel("Z(t)")
plt.savefig("exponential_martingale.png")
plt.show()

# Plot Girsanov transformation
t, W, W_tilde = simulate_girsanov(theta=0.7, T=1, steps=1000)

plt.figure(figsize=(10, 4))
plt.plot(t, W, label="Original Brownian Motion")
plt.plot(t, W_tilde, label="Shifted (Girsanov)", linestyle='--')
plt.title("Girsanov Change of Measure")
plt.xlabel("Time")
plt.ylabel("Value")
plt.savefig("girsanov_shift.png")
plt.show()
```
#### A Message from InsiderFinance 


Thanks for being a part of our community! Before you go:

- 👏 Clap for the story and follow the author 👉
- [📰 View more content in the [InsiderFinance Wire](https://wire.insiderfinance.io/)]
- [📚 Take our [FREE Masterclass](https://learn.insiderfinance.io/p/mastering-the-flow)]
- [**📈 Discover** [**Powerful Trading Tools**](https://insiderfinance.io/?utm_source=wire&utm_medium=message)]
