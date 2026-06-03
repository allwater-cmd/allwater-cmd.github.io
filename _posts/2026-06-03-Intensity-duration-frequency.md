# Intensity-Duration-Frequency Curves
## Table of Contents
## Acronyms and Notation

# Preamble
This post primarily discusses methods used by Environment Canada to develop Intensity-Duration-Frequency (IDF) information. The approaches and methods presented in subsequent sections may not be applicable to derivations in other regions. As such, this post makes extensive reference to the Canadian Standards Association (CSA) document *CSA PLUS 4013:19*, entitled *Development, interpretation, and use of rainfall intensity-duration-frequency (IDF) information: Guideline for Canadian water resources practitioners*. A copy of this document can be purchased [here](https://www.csagroup.org/store/product/CSA%20PLUS%204013%3A19/).

## Recap of Extreme Value Statistics
If you have read one of my previous posts (specifically [Extreme Vanlue Distributions](https://allwater-cmd.github.io/2026/05/27/extreme-value-distributions.html)) or are aware of hydrology at an introductory level, you're likely very familiar with the concept of **return periods**, but a brief high-level recap of the key considerations is provided below:

* Rainfall events are considered independent, meaning that if an $x$-year event occurs in one year, the probability the same ($1/x$) that an $x$-year event will occur in the next year.
* Since IDF return levels are for single points, the probability of experiencing an extreme event such as a 100-year rainfall, in a year at any point over a wide area such as a municipality or regional catchment, is greater than the 1% probability at a given single point in that area.
* The probability of experience an extreme event at a single point over longer preiods differs from the probability in a single year. This is just a verbose way of saying that the probability of experiencing a 50-year event at a single point at least once in a 50-year period is 64%. And the probability of experiencing a 500-year event at least once in 50 years is around 10%.

## Recap of Statistical Distributions

As was discussed in great (maybe) detail in [Extreme Vanlue Distributions](https://allwater-cmd.github.io/2026/05/27/extreme-value-distributions.html), the Gumbel distribution was used to fit an extreme value distribution to an Annual Maximum Series (AMS), and then used to determine return level tables and graphs. 

However, fitting an extreme value distribution to the AMS is not the only valid approach to estimate the frequency of extreme events. Other extreme value distributions exist which are suitable so long as the events are independent peaks above a high-value threshold. These are referred to as Partial Duration Series (PDS) or Peak-Over-Threshold (POT) analyses. But what is the difference between assessing return periods using AMS versus PDS? Two immediate differences are:
1. The return period, and its reciprocal, the Annual Exceedance Probability (AEP) are the frequency of events resulting from analysis of the AMS. The return period represents the average period *between years* when the return level is met or exceeded.
2. The Average Recurrence Interval (ARI) is the average length of time *between occurrences* of events of a particular magnitude, and are the results of analysis of the POT or PDS.

The differences between these approaches primarily arise from the treatment of the extreme events. When analyzing the AMS, a large event that is not the maximum for a given year in which it occurs is of course not included in the analysis, but it could represent a maximum for another year. When analyzing the PDS, it considers all events above a set threshold regardless of the time of the occurence. The AMS and PDS are analyzed with different distributions and the results have an inherently different meaning. It is understood that the 1-year return period doesn't have meaning statistically or physically using an AMS, but has practical applications when it is the result of the PDS.

A brief aside - you may be asking "*why doesn't the 1-year return period have meaning, statistically or physically*?". It is a subtle but important issue that I believe deserves an example. Recall from [Extreme Vanlue Distributions](https://allwater-cmd.github.io/2026/05/27/extreme-value-distributions.html) that to find the the corresponding value of $x_T$ that corresponds to an event $x$ of return period $T$, we set the Cumulative Distribution Function (CDF) equal to the definition of the non-exceedence probability

$$F(x)=1-\frac{1}{T}$$

Which, when $T=1$ becomes $F(x) = 0$. This breaks down for Gumbel as the CDF is

$$F(x)=\exp\left[-\exp\left(-\frac{x-\mu}{\beta}\right)\right]$$

This CDF has the property 

$$ \begin{cases}F(x) >0 & \text{for all finite } x\\F(x) \rightarrow 0 & \text{only as } x \rightarrow -\infty \end{cases}$$

This means that the 1-year return level corresponds to negative infinity, which is not physically or statistically meaningful. If through about intuitively, we know $T=10$ makes sense, it is uncommon but meaningful; $T=2$ is the event that is exceeded with probability 0.5 per year; $T=1$ is the event that is exceeded **every year for all values**, so we've effectively asked for a threshold that is *always* exceeded.

AMS results of return period can be related to those from a PDS using direct factors, however those are not discussed here. If the recurrence intervals for one-year or less are requied rather than return periods, the extreme value analysis can be conducted with a PDS using the [Generalized Pareto Distribution](https://en.wikipedia.org/wiki/Generalized_Pareto_distribution) (GPD). This distribution was not discussed in my previous post covering extreme value analysis, as it only looked at AMS applications.

## Consideration of Statistical Uncertainty

Every statistical estimate has a certain level of confidence associated with it. These confidence levels reflect the innate amount of statistical uncertainty related to the use of data samples which represent only a small portion (*sample*) of the entire dataset (*population*). CSA PLUS 4013:19 provides an example case: consider a time series of 100 years of annual maximum rainfall amounts of a particular duration (e.g. daily). Each 20-year portion of the time series could be used separately to estimate a certain return level rainfall amount. Comparing across periods there would necessarily be differences amongst calculated return levels due to the [stochastic](https://en.wikipedia.org/wiki/Stochastic) nature of extreme events. This variation is expressed as a confidence interval. The 95% confidence interval (seen everywhere in hydrology) is constructed such that it covers the results of 19 out of 20 samples. Confidence intervals only reflect **statistical** uncertainty and do not account for other uncertainties such as erroneous data, non-stationarity, or the use of incorrect extreme value distributions. The Government of Canada uses the Gumbel distribution and Method of Moments (MOM) for determining return levles and confidence intervals.

>IDF tables and graphs should **not** be produced for areas where less than 10 years of annual extremes are available. 

# Derivation of IDF Curves

[work in progress]

![Gumbel Distribution Parameter Dependence](/img/idf_curves/idf_v3-40_2025_12_5_110_BC_1108446_VANCOUVER_HARBOUR_CS.png)
