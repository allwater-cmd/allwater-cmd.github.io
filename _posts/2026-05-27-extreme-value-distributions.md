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

$$F(x;, \mu, \beta) = e^{-e^{-(x-\mu) / \beta}}$$

where $\mu$ and $\beta$ are the [location](https://en.wikipedia.org/wiki/Location_parameter) and [scale](https://en.wikipedia.org/wiki/Scale_parameter), respectively. The [mode](https://en.wikipedia.org/wiki/Mode_(statistics)) is also $\mu$ with the median and mean being equal to $\mu - \beta\ln(\ln2)$ and $\mu + \beta\gamma$, respectively. Here, $\gamma$ is the [Euler-Mascheroni constant](https://en.wikipedia.org/wiki/Euler%E2%80%93Mascheroni_constant).

### Standard Gumbel Distribtuion
The standard Gumbel distribution is the case where $\mu = 0$ and $\beta = 1$ with the cumulative distribution function

$$F(x) = e^{-e^{-x}}$$

and probability density function

$$f(x) = e^{-(x+e^{-x})}$$

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

---

### This is a header

#### Some T-SQL Code

```tsql
SELECT This, [Is], A, Code, Block -- Using SSMS style syntax highlighting
    , REVERSE('abc')
FROM dbo.SomeTable s
    CROSS JOIN dbo.OtherTable o;
```

#### Some PowerShell Code

```powershell
Write-Host "This is a powershell Code block";

# There are many other languages you can use, but the style has to be loaded first

ForEach ($thing in $things) {
    Write-Output "It highlights it using the GitHub style"
}
```
