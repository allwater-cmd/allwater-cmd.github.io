# Extreme Value Distributions
Extreme value theory, or extreme value analysis (EVA), is the study of extremes in statistical distributions. This theory is extensively applied within hydrology for estimating rainfall distributions and / or flood events (such as a 100-year rainfall or 100-year flood). Similarly, it may be applied to estimate wave actions in the design hydraulic infrastructure. EVA may assess either the maxima or minima within a dataset, however, it is far more common to assess maxima within hydrology. 

Two primary approaches existing for EVA, namely:
1. Deriving block maxima (or minima) series as a preliminary step. In hydrology, these blocks are typically the annual maxima (or minima) and are used to generate an **annual maxima series** (AMS).
2. Extracting peak values reached for any period from within a continuous record during which values exceed a certain threshhold (or, of course, falls below a certain threshold). This method is generally referred to as the **peak over threshold** method (POT).

This post will focus on only AMS data as it is the most relevant to hydrology. AMS data analysis may partly rely on the results of the Fisher-Tippett-Gnedenko theorem, which leads to the generalized extreme value distribution (GEVD) being selected for fitting. However, in practice, various procedures are applied to support fitting from a wider ranger of distribtuions. But let's take a minute to discuss Fisher-Tippett-Gnedenko theorem, as it serves as a foundational component in understanding EVA. 

## Fisher-Tippett-Gnedenko Theorem
Fisher-Tippett-Gnedenko theorem is a theorem within mathematical statistics and is applicable for extreme values of single variables (univariate). Multivariate theory and non-stationary extremes will not be discussed due to their irrelevance in hydrology. Fisher-Tippet-Gnedenko theorem tells us what happens to the maximum (or minimum) of a large sample of random values as the sample size grows very large. A helpful comparison would be to consider this theorem as the Central Limit Theorem but for extremes, where given a sufficiently large sample size, the distribution of the sample mean will eventually approximate a known distribtion (normal dsitribution, in the case of central limit theory).

The Fisher-Tipped-Gnedenko theorem states that if you take the maximums (or minimums) of many random samples, they tend to follow one of **three possible distributions**, no matter what the original data may have looked like. These three possible limit distribtions arise when you scale and shift the maximum values properly and are categorized by type:
* Type-I: Gumbel Distribtuion (typical for rainfall and flood data with light tails).
* Type-II: Fréchet Distribution (for heavy-tailed phenomena such as rare catestrophic events).
* Type-III Weibull (bounded data where values cannot exceed a limit, such as physical limits on flow). 

So depending on how a given dataset behaves at the extremes, the distribution of the largest values will look like one of the above. Let's look at this mathematically and let $X_1, X_2, \ldots, X_n$ be inedependent and identically distributed random variables with distribution $F(x)$. Define $M_n = \max(X_1, X_2, \ldots, X_n)$. Then there exist constraints $a_n > 0, \hspace{6pt} b_n \in \mathbb{R}$ such that

$$ \lim_{n \to \infty} \mathbb{P}\left( \frac{M_n - b_n}{a_n} \le x \right) = G(x) $$

where $G(x)$ is one of the extreme value distributions. But what does this mean intuitively? What are the constraints saying and what does the probability expression $\mathbb{P}$ mean? 

Let's look at this from the perspective of a typical hydrology dataset. In this case, $M_n$ is the maximum of $n$ observations (e.g. daily rainfall in a year). As $n$ grows, the raw maximum $M_n$ will also grow and will not stabilize on its own. So, we normalize them by subtracting a centering constant $b_n$ and divide by a scaling constant $a_n$. This is similar to the Central Limit Theorem which normalizes using mean and standard deviation ($\mu$, $\sigma$). Without these constraints, maxima may drift upward forever and a stable limiting distribution could not be reached. These constraints re-centre and re-scale the maxima so such that they converge to a meaningful shape.

The probability expression $\mathbb{P}$ is effectively stating that the normalized maximum rainfall (or discharge) in a block of $n$ observations has a probability to be below some threshold $x$ as $n$ becomes large. This probability $\mathbb{P}$ then converges to $G(x)$.

## Gumbel Distribution (Type-I)
The Gumbel distribtuion, also known as the type-I generalized extreme value distribtuion, log-Weibull distribution, or double exponential distribution, is one of the particular cases of the FIsher-Tippett-Gnedenko distributions as previously discussed. The cumulative distribution function of the Gumbel disttribution for the maximum (not minimum) case is

$$F(x;, \mu, \beta) = \exp\left[-\exp\left(-\frac{x-\mu}{\beta}\right)\right]$$

where $\mu$ and $\beta$ are the [location](https://en.wikipedia.org/wiki/Location_parameter) and [scale](https://en.wikipedia.org/wiki/Scale_parameter), respectively. The [mode](https://en.wikipedia.org/wiki/Mode_(statistics)) is also $\mu$ with the median and mean being equal to $\mu - \beta\ln(\ln2)$ and $\mu + \beta\gamma$, respectively. Here, $\gamma$ is the [Euler-Mascheroni constant](https://en.wikipedia.org/wiki/Euler%E2%80%93Mascheroni_constant).

### Standard Gumbel Distribtuion
The standard Gumbel distribution is the case where $\mu = 0$ and $\beta = 1$ with the cumulative distribution function

$$F(x) = \exp\left[-\exp\left(-x\right)\right]$$

and probability density function

$$f(x) = \exp\left[-\left(x+\exp(-x)\right)\right]$$

Let's work through an example using a synthetic 20-year precipitation dataset in Python. The dataset has already been processed to present an annual maxima series of 24-hour (1-day) rainfall. Feel free to copy the table into an Excel sheet if Python isn't your thing. Within the following sections, the data is assumed to come from `annual_max_rainfall.csv`. 

| year | annual_max_mm |
| --- | --- |
| 2001 | 82.4 |
| 2002 | 95.1 |
| 2003 | 101.3 |
| 2004 | 88.7 |
| 2005 | 112.5 |
| 2006 | 134.2 |
| 2007 | 97.8 |
| 2008 | 105.6 |
| 2009 | 121.4 |
| 2010 | 109.2 |
| 2011 | 140.3 |
| 2012 | 115.7 |
| 2013 | 102.9 |
| 2014 | 118.4 |
| 2015 | 126.1 |
| 2016 | 137.9 |
| 2017 | 110.6 |
| 2018 | 99.4 |
| 2019 | 123.8 |
| 2020 | 131.7 |

We will be utilizing **Numpy**, **Pandas**, and **SciPy**'s optimizer. A Gumbel distribtion and various related functions / methods are built into SciPy, but we will not be using them here. First we will need to import our data (assumed to be saved as a .csv) and store it in a dataframe. 

```python
import pandas as pd
import numpy as np

df = pd.read_csv("annual_max_rainfall.csv")
x = df["annual_max_mm"].values
n = len(x)
```

Next, we must decide if we're using the *Method of Moments* (MOM) or *Maximum Likelihood Estimation* (MLE) approach to generating the Gumbel distribution. For the purpose of learning and comparison, we will tackle both separately. The key differences of these approaches are sumamrized below.

| Method | Advantages | Disadvantages | 
| :--- | :--- | :--- |
| Method of Moments | Simple, closed-form, fast | Less accurate for skewed extremes |
| Maximum Liklihood Estimation | Statistically efficient, widely used | Requires numerical optimization |


#### Method of Moments (MOM)
When using the Method of Moments, we utilize the mean and standard deviation of the data to solve for our location $\mu$ and scale $\beta$ parameters. If we define $\bar{x}$ and $s$ to reflect mean and standard deviation respectively, and we know that

$$\bar{x} = \mu + \gamma\beta$$

$$ s = \frac{\pi}{\sqrt{6}}\beta$$

then we can solve for $\mu$ and $\beta$

$$\mu = \bar{x} - \gamma\beta$$

$$\beta = s\frac{\sqrt{6}}{\pi}$$

Recall that $\gamma$ is the Euler-Mascheroni constant and has a value of $\gamma = 0.5772156649$. To implement this in Python, we define `gamma`, `xbar`, `s`, `beta_nmom` and `mu_mom`and assign appropriate values or calculations. The delta degrees of freedom arugument `ddof` is set to 1 as we are assuming that our dataset is a sample statistic and not a population.

```python
gamma = 0.5772156649

xbar = np.mean(x)
s = np.std(x, ddof=1)

beta_mom = s * np.sqrt(6) / np.pi
mu_mom = xbar - gamma * beta_mom

print("MOM μ:", mu_mom)
print("MOM β:", beta_mom)
```

Running what we presently have will provide the output

```console
MOM μ: 105.35068467963029
MOM β: 12.818978711625254
```
Let's look at approaching this from the Maximum Likelihood Estimation next, before plotting the two distributions together to observe any differences.

#### Maximum Liklihood Estimation (MLE)
The Maximum Liklihood Estimation uses numerical optimization to select parameters ($\mu$, $\beta$) that make the observed data **most likely**. So instead of matching moments ($\bar{x}$, $s$), MLE asks "if my dada came from a Gumbel distribution, which $\mu$ and $\beta$ make that most probable?". The Gumbel PDF is

$$f(x) = \frac{1}{\beta}\exp\left[-\frac{x-\mu}{\beta}-\exp\left(-\frac{x-\mu}{\beta}\right)\right]$$

Next, we must maximize the [likelihood function](https://en.wikipedia.org/wiki/Maximum_likelihood_estimation) so that under the assumed Gumbel distribution, our observed data is most probably (likely). We don't dive into what the likelihood function really is or how it was derived, but just know that it effectively informs you how well a model explains observed data. This is why we're optimizing (maximizing) it. The likelihood function for a Gumbel distribution is 

$$\mathcal{L}(\mu,\beta) = \prod_{i = 1}^{n}\frac{1}{\beta}\exp\left[-z_i-\exp(-z_i)\right]$$

where

$$z = \frac{x-\mu}{\beta}$$

To make the numerical optimization easier, the log is often taken which results in

$$\ell(\mu,\beta) = -n\ln\beta-\sum\frac{x_i - \mu}{\beta} - \sum\exp\left(-\frac{x_i-\mu}{\beta}\right)$$

Good luck taking the derivative of that and finding its maxima witout numerical optimization (you'll find that if you take the partial derivatives with respect to $\mu$ and $\beta$ and try to optimize by setting them to 0, there is no [closed-form](https://en.wikipedia.org/wiki/Closed-form_expression) solution for Gumbel). For this reason we will have to use some in-built funcitons from `scipy`. To do this in Excel, you would need to use the numerical solver. Let's implement in Python by first defining our log-likelihood function.

```python
def gumbel_loglik(params, data):
    mu, beta = params
    if beta <= 0:
        return np.inf
    z = (data - mu) / beta
    return -( -len(data)*np.log(beta) - np.sum(z) - np.sum(np.exp(-z)) )

```
To numerically optimize, we will use `minimize` from `scipy.optimize`.

```python
from scipy.optimize import minimize

mu0 = np.mean(x)
beta0 = np.std(x)

res = minimize(gumbel_loglik, x0=[mu0, beta0], args=(x,), method="Nelder-Mead")

mu_mle, beta_mle = res.x

print("MLE μ:", mu_mle)
print("MLE β:", beta_mle)
```

Now the console output should be 

```console
MOM μ: 105.35068467963029
MOM β: 12.818978711625254
MLE μ: 104.78637390431037
MLE β: 14.866657527971807
```
Very similar! But the MLE took quite a bit longer to set up and quite a bit more computational effort. Now let's derive return levels for both methods.

### Return Levels (MOM and MLE)
In hydrology, the return level is one of the most imporant outputs of extreme-value analysis. It tells us the maagnitute of an event associated with a given return period $T$. The inverse of the return period is the annual exceedance probability ($AEP$). A return level $x_T$ is defined such that

$$\mathbb{P}(X\leq x_T) = 1 - \frac{1}{T}$$

which means the probability of not exceeding $x_T$ (non-exceedence probabiltiy) in a given year is $1-1/T$. The probability of exceeding $x_T$ is of course the annual exceedence probability $1/T$. So, let's set the Gumbel CDF equal to this probability to determine the values of extremes required to meet a certain return level

$$F(x_T) = 1-\frac{1}{T}$$

which with the Gumbel CDF expanded is

$$\exp\left[-\exp\left(-\frac{x_T-\mu}{\beta}\right)\right] = 1 - \frac{1}{T}$$

which can then be rearranged to solve for $x_T$ and directly relate a returl level based on the Gumbel distribution

$$ x_T = \mu - \beta\ln\left[-\ln\left(1-\frac{1}{T}\right)\right]$$

Let's implement in Python and select of a bunch of return levels that we may be interested in, after defining the function above

```python
def gumbel_return_level(T, mu, beta):
    return mu - beta * np.log(-np.log(1 - 1/T))

Ts = [2, 5, 10, 25, 50, 100]

print("\nReturn Levels (MOM):")
for T in Ts:
    print(T, gumbel_return_level(T, mu_mom, beta_mom))

print("\nReturn Levels (MLE):")
for T in Ts:
    print(T, gumbel_return_level(T, mu_mle, beta_mle))
```
The console should now show

```console
MOM μ: 105.35068467963029
MOM β: 12.818978711625254
MLE μ: 104.78637390431037
MLE β: 14.866657527971807

Return Levels (MOM):
2 110.04900600610225
5 124.57838343861599
10 134.19809554178556
25 146.35262728550268
50 155.36955326977736
100 164.31989968797862

Return Levels (MLE):
2 110.2351959741747
5 127.08546799997465
10 138.24181427160173
25 152.33788736070315
50 162.79515962699634
100 173.17521703636208
```
Close, but not exaclty the same. In this case, the MLE approach provides larger return period estimations. Why might this be?

#### Bonus Plot
For fun, let's plot these distributions together and see how different they look visually. This may be more challenging in Excel as you'd may have to create an output column for each distribution prior being able to create a graph.

```python
import matplotlib.pyplot as plt

def gumbel_cdf(x, mu, beta):
    return np.exp(-np.exp(-(x - mu)/beta))

xs = np.linspace(min(x)-10, max(x)+10, 400)

plt.figure(figsize=(7,5))
plt.scatter(np.sort(x), np.linspace(1/n, 1, n), color="red", label="Empirical CDF")
plt.plot(xs, gumbel_cdf(xs, mu_mle, beta_mle), label="MLE Gumbel CDF")
plt.plot(xs, gumbel_cdf(xs, mu_mom, beta_mom), label="MOM Gumbel CDF", linestyle="--")
plt.xlabel("Annual Maximum Daily Rainfall (mm)")
plt.ylabel("Cumulative Probability")
plt.legend()
plt.grid(True)
plt.show()
```

Excluding the console output, the plots generated are provided below. 

![Comparison of Method of Moments and Maximum Likelihood Estimation for a Gumbel Extreme Value Distribution](/img/extreme_value/gumbel_methods_comparison.png)


