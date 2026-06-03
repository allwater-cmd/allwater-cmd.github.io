# Extreme Value Distributions
## Table of Contents

- [Acronyms and Notation](#acronyms-and-notation)
- [Extreme Value Theory](#extreme-value-theory)
- [Fisher-Tippett-Gnedenko Theorem](#fisher-tippett-gnedenko-theorem)
- [Gumbel Distribution (Type-I)](#gumbel-distribution-type-i)
    - [Standard Gumbel Distribution](#standard-gumbel-distribution)
    - [Method of Moments (MOM)](#method-of-moments-mom)
    - [Maximum Likelihood Estimation (MLE) (Gumbel)](#maximum-likelihood-estimation-mle-gumbel)
    - [Return Levels (MOM and MLE)](#return-levels-mom-and-mle)
      - [Bonus Plot (Gumbel)](#bonus-plot-gumbel)
- [Fréchet Distribution (Type-II)](#fréchet-distribution-type-ii)
    - [Standard Fréchet Distribution](#standard-fréchet-distribution)
    - [Maximum Likelihood Estimation (MLE) (Fréchet)](#maximum-likelihood-estimation-fréchet)
    - [Return Levels (Fréchet)](#return-levels-fréchet)
        - [Bonus Plot (Fréchet)](#bonus-plot-fréchet)
- [Weibull Distribution (Type-III)](#weibull-distribtion-type-iii)
    - [Standard Weibull Distribution](#standard-weibull-distribution)
    - [Maximum Likelihood Estimation (MLE) (Weibull)](#maximum-likelihood-estimation-mle-weibull)
    - [Return Levels (Weibull)](#return-levels-weibull)
        - [Bonus Plot (Weibull)](#bonus-plot-weibull)
- [Comparison of Probability Density Functions](#comparison-of-probability-density-functions)

## Acronyms and Notation

To support readability, the following table summarizes the acronyms and key symbols used throughout this document.

| Acronym / Symbol | Meaning | Description |
|------------------|--------|-------------|
| **EVA** | Extreme Value Analysis | The study of extreme events (maxima or minima), commonly applied to hydrology and environmental data. |
| **AMS** | Annual Maxima Series | A dataset consisting of the maximum value observed in each year (e.g., annual maximum rainfall). |
| **POT** | Peak Over Threshold | A method where all values exceeding a specified threshold are analyzed instead of block maxima. |
| **GEV / GEVD** | Generalized Extreme Value (Distribution) | A unified distribution that includes Gumbel, Fréchet, and Weibull as special cases. |
| **MOM** | Method of Moments | A parameter estimation technique based on matching sample moments (mean, variance) to theoretical moments. |
| **MLE** | Maximum Likelihood Estimation | A parameter estimation method that selects values maximizing the likelihood of the observed data. |
| **CDF** | Cumulative Distribution Function | Function giving the probability that a random variable is less than or equal to a given value. |
| **PDF** | Probability Density Function | Function describing the relative likelihood of a continuous random variable. |
| **KDE** | Kernel Density Estimate | A smoothed approximation of the empirical probability density function. |
| $ \mu$ | Location parameter (Gumbel) | Controls the central tendency or shift of the Gumbel distribution. |
| $ \beta$ | Scale parameter (Gumbel) | Controls the spread or dispersion of the Gumbel distribution. |
| $ \alpha$ | Shape parameter (Fréchet) | Governs tail behaviour; smaller values produce heavier tails. |
| $ s$ | Scale parameter (Fréchet) | Controls dispersion in the Fréchet distribution. |
| $ m$ | Location parameter (Fréchet) | Defines the lower bound (minimum threshold) of the Fréchet distribution. |
| $ k$ | Shape parameter (Weibull) | Controls the curvature and tail behaviour of the Weibull distribution. |
| $ \lambda$ | Scale parameter (Weibull) | Controls the spread of the Weibull distribution. |
| $ \xi$ | Location parameter (Weibull) | Represents the upper bound (maximum possible value). |
| $ x_T$ | Return level | The magnitude of an event associated with a return period $ T$. |
| $ T$ | Return period | The average time interval between occurrences of an event of a given magnitude. |
| $ \gamma$ | Euler–Mascheroni constant | A constant (~0.5772) used in Gumbel moment relationships. |
| $ \log$ / $ \ln$ | Natural logarithm | Logarithm base $ e$; used throughout likelihood derivations. |

Unless otherwise specified, all logarithms in this document refer to the natural logarithm (base $ e $), which is standard in statistical estimation and likelihood-based methods.

---

# Extreme Value Theory
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

---

## Gumbel Distribution (Type-I)
The Gumbel distribtuion, also known as the type-I generalized extreme value distribtuion, log-Weibull distribution, or double exponential distribution, is one of the particular cases of the FIsher-Tippett-Gnedenko distributions as previously discussed. The cumulative distribution function of the Gumbel disttribution for the maximum (not minimum) case is

$$F(x;, \mu, \beta) = \exp\left[-\exp\left(-\frac{x-\mu}{\beta}\right)\right]$$

where $\mu$ and $\beta$ are the [location](https://en.wikipedia.org/wiki/Location_parameter) and [scale](https://en.wikipedia.org/wiki/Scale_parameter), respectively. The [mode](https://en.wikipedia.org/wiki/Mode_(statistics)) is also $\mu$ with the median and mean being equal to $\mu - \beta\ln(\ln2)$ and $\mu + \beta\gamma$, respectively. Here, $\gamma$ is the [Euler-Mascheroni constant](https://en.wikipedia.org/wiki/Euler%E2%80%93Mascheroni_constant).

### Standard Gumbel Distribution
The standard Gumbel distribution is the case where $\mu = 0$ and $\beta = 1$ with the cumulative distribution function

$$F(x) = \exp\left[-\exp\left(-x\right)\right]$$

and probability density function

$$f(x) = \exp\left[-\left(x+\exp(-x)\right)\right]$$

To see the effects of these parameters, check out the image below from the [Wikipedia article](https://en.wikipedia.org/wiki/Gumbel_distribution) on the Fréchet Distribution. Note that the image shows the probability density function only.

![Gumbel Distribution Parameter Dependence](/img/extreme_value/gumbel_parameter_dependence.jpg)

Let's work through an example using a synthetic 20-year precipitation dataset in Python. The dataset has already been processed to present an annual maxima series of 24-hour (1-day) rainfall. Feel free to copy the table into an Excel sheet if Python isn't your thing. Within the following sections, the data is assumed to come from `annual_max_rainfall.csv`. 

| year | annual_max_mm |
| :---: | :---: |
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

### Method of Moments (MOM)
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

### Maximum Likelihood Estimation (MLE) (Gumbel)
The Maximum Liklihood Estimation uses numerical optimization to select parameters ($\mu$, $\beta$) that make the observed data **most likely**. So instead of matching moments ($\bar{x}$, $s$), MLE asks "if my dada came from a Gumbel distribution, which $\mu$ and $\beta$ make that most probable?". The Gumbel PDF is

$$f(x) = \frac{1}{\beta}\exp\left[-\frac{x-\mu}{\beta}-\exp\left(-\frac{x-\mu}{\beta}\right)\right]$$

Next, we must maximize the [likelihood function](https://en.wikipedia.org/wiki/Maximum_likelihood_estimation) so that under the assumed Gumbel distribution, our observed data is most probably (likely). We don't dive into what the likelihood function really is or how it was derived, but just know that it effectively informs you how well a model explains observed data. This is why we're optimizing (maximizing) it. The likelihood function for a Gumbel distribution is 

$$\mathcal{L}(\mu,\beta) = \prod_{i = 1}^{n}\frac{1}{\beta}\exp\left[-z_i-\exp(-z_i)\right]$$

where

$$z_i = \frac{x_i-\mu}{\beta}$$

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
Very similar. But the MLE took quite a bit longer to set up and quite a bit more computational effort. Now let's derive return levels for both methods.

### Return Levels (MOM and MLE)
In hydrology, the return level is one of the most imporant outputs of extreme-value analysis. It tells us the maagnitute of an event associated with a given return period $T$. The inverse of the return period is the annual exceedance probability ($AEP$). A return level $x_T$ is defined such that

$$\mathbb{P}(X\leq x_T) = 1 - \frac{1}{T}$$

which means the probability of not exceeding $x_T$ (non-exceedence probabiltiy) in a given year is $1-1/T$. The probability of exceeding $x_T$ is of course the annual exceedence probability $1/T$. So, let's set the Gumbel CDF equal to this probability to determine the values of extremes required to meet a certain return level

$$F(x_T) = 1-\frac{1}{T}$$

which with the Gumbel CDF expanded is

$$\exp\left[-\exp\left(-\frac{x_T-\mu}{\beta}\right)\right] = 1 - \frac{1}{T}$$

which can then be rearranged to solve for $x_T$ and directly relate a return level based on the Gumbel distribution

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

#### Bonus Plot (Gumbel)
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

---

## Fréchet Distribution (Type-II)

The Fréchet distribution, also known as the inverse Weibull distribution, is a special case of the generalized extreme value distribution. Similar to the Gumbel distribution above, it is generalized to include a location parameter $m$ and a scale parameter $s > 0$ with the general cumulative distribution function

$$F(x) = \exp\left[-\left(\frac{x-m}{s}\right)^{-\alpha}\right] \hspace{8pt} \text{if} \hspace{8pt} x>m $$


where $\alpha > 0$ is a shape parameter. In hydrology, the Fréchet distribution is applied typically to extreme events as annual maximum one-day rainfalls and river discharges. In general, this distribution is used when the extrems are **more severe and less bounded**. 

The generalized probability density function of the Fréchet distribution is

$$f(x) = \frac{\alpha}{s}\left(\frac{x-m}{s}\right)^{-1-\alpha}\exp\left(-\frac{x-m}{s}^{-\alpha}\right)$$

### Standard Fréchet Distribution

The standard Fréchet distribution is the case where $\alpha=1$, $s=1$, and $m=0$ with the cumulative distribution function

$$F(x) = \exp(-x^{-\alpha}) \hspace{8pt}\text{if} \hspace{8pt} x>0 $$



To see the effects of these parameters, check out the image below from the [Wikipedia article](https://en.wikipedia.org/wiki/Fr%C3%A9chet_distribution) on the Fréchet Distribution. Note that the image shows the probability density function.

![Fréchet Distribution Parameter Dependence](/img/extreme_value/frechet_parameter_dependence.png)

Now that we've worked through the Gumbel distribution using our annual maxima rainfall dataset, let's extend the exact same workflow to the Fréchet distribution. This can let us see and directly compare how a heavy-tailed distribution behaves relative to the lighter-tailed Gumbel.

### Maximum Likelihood Estimation (Fréchet)

Unlike the Gumbel distribution, the Fréchet distribution does not lend itself well to a simple Method of Moments solution in this context. As a result, we proceed directly with the Maximum Likelihood Estimation. But why is this?

Recall that the Method of Moments works by matching mean and variance (standard deviation) to their theoretical expressions (recall in the Gumbel example we used the location $\mu$ and scale $\beta$ parameters to calculate mean and standard deviation). However, for the Fréchet distribution, the mean exists only if $\alpha > 1$, and the variance only exists if $\alpha > 2$. The reason behind this is complicated but is a consequence of the convergence requirements of the integrals used in calculating the mean and variance (not shown here for the sake of sanity). 

Think of it like this - Fréchet allows very large values with non-negligible probability. As $\alpha$ decreases, the tail gets heavier, and the extreme values become more influential. The mean is an average, so if extremely large values occur often enough, they pull the average upward without bound. This results in no range of viable $\alpha$ to calculate mean and variance to derive the distribution parameters directly $(\alpha, s, m)$. A summary is below.

| $\alpha$ Range | Consequence |
| :--- | :--- |
| $\alpha \leq 1$ | Mean $\rightarrow \infty$ (no finite average) |
| $1 < \alpha \leq 2$ | Mean exists, variance $\rightarrow \infty$ |
|$\alpha > 2$| Mean, variance $\rightarrow \infty$|

The Maximum Likelihood approach remains the same, where we select parameters $(\alpha, s, m)$ that make the observed rainfall data most likely under a Fréchet model. To simplify the optimization, we again work with the log-likelihood. Let

$$ z_i = \frac{x_i-m}{s} $$

then the liklihood function is

$$\mathcal{L}(\alpha,s, m) = \prod_{i = 1}^{n}\left[\frac{\alpha}{s}\left(z_i\right)^{-1-\alpha}\exp\left(-z_i^{-\alpha}\right)\right]$$

which is simplified by some complicated math involving natural logarithms to become

$$\ell(\alpha,s, m) = n\ln\alpha-n\ln s - (1+\alpha)\sum\ln z_i - \sum z_i^{-\alpha}$$

As before, there is no closed-form solution, so numerical optimization is required. To implement in python, we will again need to utilize `minimize` from `scipy.optimize`. Note that if you are using the same python file that contains the Gumbel code, you do not need to re-import `pandas`, `numpy`, `scipy.optimize` nor re-initiate `df`, `x`, or `n`. I recommend you use your previous file and simply add everything from `def frechet_loglik(params,data):` onward. This will allow us to plot the distributions together (Fréchet and Gumbel). 

Notice how our function `frechet_loglik` returns `-logL`. Recall that minimizing the negative of the log-likelihood is the same as maximizing the positive.

```python
import pandas as pd
import numpy as np
from scipy.optimize import minimize

df = pd.read_csv("annual_max_rainfall.csv")
x = df["annual_max_mm"].values
n = len(x)

def frechet_loglik(params, data):
    alpha, s, m = params
    
    # enforce constraints
    if alpha <= 0 or s <= 0:
        return np.inf
    
    z = (data - m) / s
    
    # ensure valid domain
    if np.any(z <= 0):
        return np.inf
    
    logL = (
        len(data)*np.log(alpha)
        - len(data)*np.log(s)
        - (1+alpha)*np.sum(np.log(z))
        - np.sum(z**(-alpha))
    )
    
    return -logL
```

Next, we call the optmizer and set our initial conditions

```python
alpha0 = 2.0
s0 = np.std(x)
m0 = min(x) - 1  # ensure m < data

res = minimize(
    frechet_loglik,
    x0=[alpha0, s0, m0],
    args=(x,),
    method="Nelder-Mead"
)

alpha_mle, s_mle, m_mle = res.x

print("Fréchet α:", alpha_mle)
print("Fréchet s:", s_mle)
print("Fréchet m:", m_mle)
```
After running, the console output should read

```console
Fréchet α: 79059310.52360749
Fréchet s: 1175360005.6098711
Fréchet m: -1175359900.823351
```

### Return Levels (Fréchet)

The return level definition is identical to what we used before

$$F(x_T) = 1 - \frac{1}{T}$$

which, after substituting the Fréchet CDF and solving for $x_T$, becomes

$$x_T = m + s\left[-ln\left(1-\frac{1}{T}\right)\right]^{-1/\alpha}$$

To implement in python, we define this return level function and range of return levels to assess, just as before.

```python
def frechet_return_level(T, alpha, s, m):
    return m + s * (-np.log(1 - 1/T))**(-1/alpha)

Ts = [2, 5, 10, 25, 50, 100]

print("\nReturn Levels (Fréchet):")
for T in Ts:
    print(T, frechet_return_level(T, alpha_mle, s_mle, m_mle))
```

After running, the console output should read (after expluding the parameter output)

```console
Return Levels (Fréchet):
2 110.2353994846344
5 127.08584833145142
10 138.24231147766113
25 152.3385329246521
50 162.79591488838196
100 173.17608165740967
```

In this particular example, the Gumbel and Fréchet return level predictions are nearly identical. This is not coincidental, but rather a reflection of the dataset itself. The annual maxima series does not exhibit strongly heavy-tailed behaviour, as there are no exceptionally large outliers or extreme deviations. As a result, when fitting the Fréchet distribution using MLE, the estimated shape parameter $\alpha$ becomes relatively large, which effectively reduces the heaviness of the tail. In this regime, the Fréchet distribution behaves similarly to a Gumbel distribution over the observed range of data. Additionally, the relatively small sample size limits our ability to distinguish between tail behaviours, causing both models to produce comparable return level estimates. Significant divergence between the two approaches would typically only arise in datasets that contain more pronounced extreme events.

#### Bonus Plot (Fréchet)

Let's extend the previous plot and include the Fréchet distribution, assuming `matplotlib.pyplot` was previously imported as `plt` and the Gumbel distribution is in the same file

```python
def frechet_cdf(x, alpha, s, m):
    z = (x - m) / s
    F = np.zeros_like(x)
    valid = z > 0
    F[valid] = np.exp(-(z[valid])**(-alpha))
    return F

xs = np.linspace(min(x)-10, max(x)+50, 400)

plt.figure(figsize=(7,5))

plt.scatter(np.sort(x), np.linspace(1/n, 1, n),
            color="red", label="Empirical CDF")

plt.plot(xs, gumbel_cdf(xs, mu_mle, beta_mle),
         label="MLE Gumbel CDF")

plt.plot(xs, frechet_cdf(xs, alpha_mle, s_mle, m_mle),
         label="Fréchet CDF")

plt.xlabel("Annual Maximum Daily Rainfall (mm)")
plt.ylabel("Cumulative Probability")
plt.legend()
plt.grid(True)
plt.show()
```
Excluding the console output, the plots generated are provided below. As can be seen, when the data does not strongly indicate heavy-tail behaviour, different extreme value models can converge to very similar predictions, even if their theoretical foundations differ.

![Comparison of Maximum Likelihood Estimation for Fréchet and Gumbel Extreme Value Distributions](/img/extreme_value/frechet_gumbel_comparison.png)

---

## Weibull Distribtion (Type-III)

The Weibull distribution, also referred to in extreme value theory as the Type-III distribution, represents the third and final limiting case arising from the Fisher-Tippett-Gnedenko theorem. Unlike the Gumbel and Fréchet distributions, which allow values to extend indefinitely, the Weibull distribution is used for **bounded datasets**, where values cannot exceed some physical limit. In hydrology, this may be relevant where there exists a natural upper-bound (e.g. storage capacity, phsysical constraints on flow) or observed extremes appear to approach a limiting value. 

The general Weibull Distribution is defined using

$$F(x) = \exp\left[-\left(\frac{\xi-x}{\lambda}\right)^k\right] \hspace{8pt} \text{if} \hspace{8pt} x < \xi$$

where $k>0$ is the shape parameter, $\lambda>0$ is the scale parameter, and $\xi$ is the upper bound (location paramter). The corresponding generalized probability density function is

$$f(x) = \frac{k}{\lambda}\left(\frac{\xi-x}{\lambda}\right)^{k-1}\exp\left[-\left(\frac{\xi-x}{\lambda}\right)^k\right]$$


This upper bound $\xi$ introduces a fundamental constraint that $x<\xi$, meaning that all realizations are bounded above by $\xi$. The influence of these parameters as they relate to the shape of the Weibull distribution are presnted in the image below from the Weibell [Wikipedia](https://en.wikipedia.org/wiki/Weibull_distribution) article. You will immediately notice that the shape of the distribution varies significantly based on these paramters, far more than both the Gumbel and Fréchet distributions.

![Weibull Distribution Parameter Dependence](/img/extreme_value/weibell_parameter_dependence.png)

### Standard Weibull Distribution

The standard Weibull cumulative distribution function can be written as

$$F(x) = \exp\left[-(-x)^k\right] \hspace{8pt} \text{if} \hspace{8pt} x<0$$

OF course, limiting $x$ to be less than 0 is not paricularly useful for real-world hydrologic data, so instead we work with the generalized form presented above.

### Maximum Likelihood Estimation (MLE) (Weibull)

As with the Fréchet distribution, we proceed using the Maximum Likelihood Estimation. The Method of Moments is not commonly applied here due to the nonlinear nature of the parameter relationships and the bounded support of the distribution. As with the previous distributions, lets define

$$ z_i = \frac{\xi-x_i}{\lambda} $$

then the liklihood function is

$$\mathcal{L}(k,\xi, \lambda) = \prod_{i = 1}^{n}\frac{k}{\lambda}\left(z_i\right)^{k-1}\exp\left[-\left(z_i\right)^k\right]$$

which is once again simplified by some complicated math involving natural logarithms to become

$$\ell(k,\xi, \lambda) = n\ln k - n\ln\lambda + (k-1)\sum\ln z_i - \sum z_i^k$$

Given the hard bounding of this distribution, we must enforce bounding for the optimization function to remain valid, namely

$$z_i>0 \Rightarrow \xi>\max(x)$$

which ensures that all observed values fall within the allowable domain of the distribution. To implement this in python, we will use our existing dataset. For this example I am assuming that you are appending new function definitions within the file that contains the Gumbel and Fréchet distributions. Therefore, we do not re-import anything or re-initiate our dataframe. As before, our function returns `-logL`, or the negative of the log-likelihood function for use in minimization.

```python
def weibull_loglik(params, data):
    k, lam, xi = params
    
    if k <= 0 or lam <= 0:
        return np.inf
    
    z = (xi - data) / lam
    
    if np.any(z <= 0):
        return np.inf
    
    logL = (
        len(data)*np.log(k)
        - len(data)*np.log(lam)
        + (k - 1)*np.sum(np.log(z))
        - np.sum(z**k)
    )
    
    return -logL
```

Utilizing `SciPy`'s built int `minimize` method as before, we define our initial conditions

```python
k0 = 2.0
lam0 = np.std(x)
xi0 = max(x) + 1

res = minimize(
    weibull_loglik,
    x0=[k0, lam0, xi0],
    args=(x,),
    method="Nelder-Mead"
)

k_w, lam_w, xi_w = res.x

print("Weibull k:", k_w)
print("Weibull λ:", lam_w)
print("Weibull ξ:", xi_w)
```

The console output should now show (excluding outputs from the previous distributions)

```console
Weibull k: 2.7021925256346258
Weibull λ: 44.22977809322008
Weibull ξ: 152.01264491428446
```
### Return Levels (Weibull)

As before, return levels are defined through

$$F(x_T) = 1 - \frac{1}{T} $$

and after substituting the Weibull CDF

$$\exp\left[-\left(\frac{\xi-x_T}{\lambda}\right)^k\right] = 1 - \frac{1}{T} $$

we get from rearranging

$$x_T = \xi - \lambda\left[-\ln\left(1-\frac{1}{T}\right)\right]^{1/k} $$

To implement in python, we define this function

```python
def weibull_return_level(T, k, lam, xi):
    return xi - lam * (-np.log(1 - 1/T))**(1/k)

Ts = [2, 5, 10, 25, 50, 100]

print("\nReturn Levels (Weibull):")
for T in Ts:
    print(T, weibull_return_level(T, k_w, lam_w, xi_w))
```
The console should now output (excluding outputs from prevoous distributions)

```console
Return Levels (Weibull):
2 113.39293016600254
5 126.62365278862828
10 132.7800755871547
25 138.4717120795552
50 141.57511041149627
100 143.95177314314148
```
These are now quite different than those provided by the Gumbel and Fréchet estimates. This is a direct result of $\xi$ imposing an aboslute bound or maximum value from the distribution. Notice that in our dataset our maximum of 140.3 mm is very close to that provided in the *estimated* distribution 100-year maximum of 143.95 mm. We do not have even close to 100-years of data, and yet Weibull is suggesting within in our dataset we're approaching our maxima (asymptote) already. Notice that we **defined our $\xi$ to be the maximum observed in our dataset plus 1**. We did this to ensure a simple positve buffer to our dataset, but in doing so we've also innately defined what we expect our aboslute maximum to be. This goes to show how Weibull is **not** an appropriate distribution to estimate the extremes of precipitation (with the exception of perhaps a probable maximum precipitation analysis).

#### Bonus Plot (Weibull)

Let's extend the previous plot and include the Weibull distribution, assuming `matplotlib.pyplot` was previously imported as `plt` and the Gumbel distribution is in the same file

```python
def weibull_cdf(x, k, lam, xi):
    z = (xi - x) / lam
    F = np.zeros_like(x)
    valid = z > 0
    F[valid] = np.exp(-(z[valid])**k)
    return F

xs = np.linspace(min(x)-10, max(x)+50, 400)

plt.figure(figsize=(7,5))

# Empirical CDF
plt.scatter(np.sort(x), np.linspace(1/n, 1, n),
            color="red", label="Empirical CDF")

# Gumbel (MLE)
plt.plot(xs, gumbel_cdf(xs, mu_mle, beta_mle),
         label="Gumbel CDF")

# Fréchet (MLE)
plt.plot(xs, frechet_cdf(xs, alpha_mle, s_mle, m_mle),
         label="Fréchet CDF")

# Weibull (MLE)
plt.plot(xs, weibull_cdf(xs, k_w, lam_w, xi_w),
         label="Weibull CDF")

plt.xlabel("Annual Maximum Daily Rainfall (mm)")
plt.ylabel("Cumulative Probability")
plt.legend()
plt.grid(True)
plt.show()
```
Excluding the console outputs, the plotted results are shown below. It is immediately clear that the Weibull maximum boundary is imposed, as immediately after `max(x) + 1`, we see a drop to a zero percent cumulative probability. 

![Comparison of Maximum Likelihood Estimation for Weibull, Fréchet, and Gumbel Extreme Value Distributions](/img/extreme_value/weibull_frechet_gumbel_comparison.png)

---

## Comparison of Probability Density Functions

The previous sections all showed a comparison of the cumulative distribution functions. While useful, sometimes CDFs can mask differences in how probability is distributed. To gain futher insight, we now plot the PDFs for each fitted distribution. Recall that the PDF describes the **relative likelihood** of observing values in different regions of the dataset.

First we define the PDF functions corresponding to each distribution using MLE parameters

```python
def gumbel_pdf(x, mu, beta):
    z = (x - mu) / beta
    return (1/beta) * np.exp(-(z + np.exp(-z)))

def frechet_pdf(x, alpha, s, m):
    z = (x - m) / s
    pdf = np.zeros_like(x)
    valid = z > 0
    pdf[valid] = (alpha/s) * (z[valid])**(-1-alpha) * np.exp(-(z[valid])**(-alpha))
    return pdf

def weibull_pdf(x, k, lam, xi):
    z = (xi - x) / lam
    pdf = np.zeros_like(x)
    valid = z > 0
    pdf[valid] = (k/lam) * (z[valid])**(k-1) * np.exp(-(z[valid])**k)
    return pdf
```

and then generate the PDFs over a common range of values and generate a histogram for the empirical data

```python
xs = np.linspace(min(x)-10, max(x)+50, 400)

plt.figure(figsize=(7,5))

# Empirical PDF (histogram, normalized)
plt.hist(x, bins=8, density=True, alpha=0.3,
         color="gray", edgecolor="black",
         label="Empirical Density")

# Gumbel PDF
plt.plot(xs, gumbel_pdf(xs, mu_mle, beta_mle),
         label="Gumbel PDF")

# Fréchet PDF
plt.plot(xs, frechet_pdf(xs, alpha_mle, s_mle, m_mle),
         label="Fréchet PDF")

# Weibull PDF
plt.plot(xs, weibull_pdf(xs, k_w, lam_w, xi_w),
         label="Weibull PDF")

plt.xlabel("Annual Maximum Daily Rainfall (mm)")
plt.ylabel("Probability Density")
plt.legend()
plt.grid(True)
plt.show()
```

which provides us with the plot shown below. Recall that the Fréchet and Gumbel distributions were effectively identical, and thus show no visible difference in the plot. The histogram of the empirical data drives home the assertion that the dataset used does not contain many (if any) extremes and is pretty well grouped. I recommend you try these on a real dataset and see how different all three distributions may be.

![Comparison of Maximum Likelihood Estimation for Weibull, Fréchet, and Gumbel Extreme Value Distributions](/img/extreme_value/pdf_weibull_frechet_gumbel_comparison.png)
