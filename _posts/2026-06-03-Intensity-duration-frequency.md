# Intensity-Duration-Frequency Curves
## Table of Contents
## Acronyms and Notation

# Preamble
This post primarily discusses methods used by Environment Canada (EC) to develop Intensity-Duration-Frequency (IDF) information. The approaches and methods presented in subsequent sections may not be applicable to derivations in other regions. As such, this post makes extensive reference to the Canadian Standards Association (CSA) document *CSA PLUS 4013:19*, entitled *Development, interpretation, and use of rainfall intensity-duration-frequency (IDF) information: Guideline for Canadian water resources practitioners*. A copy of this document can be purchased [here](https://www.csagroup.org/store/product/CSA%20PLUS%204013%3A19/).

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

# Derivation of IDF Curves by Environment Canada

Data used by EC to develop IDF curves consist of rate-of-rainfall observations from tipping bucket rainfall gauge (TBRG) observations and daily rainfall amounts from standard rain guages. Unfortunately, access to the raw data from TBRG observations is not publicly available, and is only indirectly accessible through the final IDF curves provided by EC. Before looking at which specific datasets are used by EC, let's look at all that are recorded in the EC climate archives (not all programs are necessarily free or publicly available).

| Program / Element Number| Units | Description |
| :--- | :--- | :--- |
| HLY03 / 123 | 0.1 mm | Hourly rainfall |
| DLY03 / 124 | 0.01 | Adjustment factor for the day|
| DLY03 / 125 | 0.1 mm | Greatest rainfall in 5 minutes for the day |
| DLY03 / 126 | 0.1 mm | Greatest rainfall in 10 minutes for the day |
| DLY03 / 127 | 0.1 mm | Greatest rainfall in 15 minutes for the day |
| DLY03 / 128 | 0.1 mm | Greatest rainfall in 30 minutes for the day |
| DLY03 / 129 | 0.1 mm | Greatest rainfall in 1 hour for the day |
| DLY03 / 130 | 0.1 mm | Greatest rainfall in 2 hours for the day |
| DLY03 / 131 | 0.1 mm | Greatest rainfall in 6 hours for the day |
| DLY03 / 132 | 0.1 mm | Greatest rainfall in 12 hours for the day |

Within the above table, the adjustment factor (DLY03 / 124) is the value used to adjust all of the TBRG values for one day uniformly such that the guage total for the day agrees with standard guage observations. This is done to reduce systematic errors and / or biases found in TBRG data. Additionally, the greatest 24-hour rainfall amount is not archived, but is instead calculated using the hourly data. The EC designations for the observing programs are DLY04 (daily rainfall observations from standard rain guages, not TBRG), DLY03 (daily maximum amounts for various durations from TBRG), and HLY03 (hourly rainfall amounts from TBRG).

>CSA PLUS 4013:19 makes it clear that when users obtain EC observational data and compile other datasets for individual IDF calculations, the subjective aspects in the process used by EC as well as the user may result in the user's AMS not being identical to those used by EC.

The figure below shows an example EC IDF curve provided for the Vancouver Harbour CS 1108446. This curve was developed from the calculated maximum 24-hour daily rainfall as well as the DLY03 programs presented in the above table where a Gumbel (Type-I) analysis using the MOM was completed for each rainfall duration. 

![Gumbel Distribution Parameter Dependence](/img/idf_curves/idf_v3-40_2025_12_5_110_BC_1108446_VANCOUVER_HARBOUR_CS.png)

All of this naturally leads to the questions - how can we derive IDF curves ourselves without access to hourly or subhourly data? If EC captured subhourly data with tipping buckets, but don't provide the data, what do we do for regions where we don't have a direct IDF curve?

It would be easy to just use the IDF_CC tool, but it's critical that a hydrotechnical engineer is capable of calculating this directly, without relying entirely on external tools. Let's look at a practical workflow when we only have 24-hour data available.

## Daily Rainfall to Sub-Hourly IDFs

When sub-hourly rainfall observations, such as TBRG data are unavailable, IDF curves must be built indirectly. The key idea is to **reconstruct short-duration extremes from daily extremes using statistical scaling and disaggregation**, while acknowledging that true IDF curves are normally derived from sub-hourly observation and extreme value analysis of their annual maxima.

For the purposes of this example, I downloaded all available data from the Vancouver Harbour CS 1108446 and removed data before 1970 and after 2024. I did this to align with the range of years of data used to derive the IDF curve provided in the figure above. This way, we can do a direct comparison of hte final results using two different methodologies. A copy of the dataset used can be downloaded from within the Github repository for this blog [here](https://github.com/allwater-cmd/allwater-cmd.github.io/blob/edff98db7feb2e76cf7b8ab6f61a5bee972dc5c9/datasets/vancouver_cs_data.csv).

The first few rows of the example dataset look like the following, once all columns are removed except `LOCAL_DATE` and `TOTAL_PRECIPITATION`. Nore that the units of precipitation are in mm. Recall that for EC, 'total precipitation' includes both rainfall and snow water equivalent depths.

| LOCAL_DATE        | TOTAL_PRECIPITATION |
|:-------------------:|:----------------------:|
| 1970-01-01 0:00   | 0                    |
| 1970-01-02 0:00   | 1.8                  |
| 1970-01-03 0:00   | 0                    |
| 1970-01-04 0:00   | 0                    |
| 1970-01-05 0:00   | 0                    |
| 1970-01-06 0:00   | 0                    |
| 1970-01-07 0:00   | 0                    |
| 1970-01-08 0:00   | 18.5                 |
| 1970-01-09 0:00   | 0                    |
| 1970-01-10 0:00   | 2.5                  |
| 1970-01-11 0:00   | 0                    |
| ...  | ...   |

Now for Python. We first must load the CSV files and extract the daily precipitation time series. The goal is to construct a continuous record of daily depths from the `TOTAL_PRECIPITATION` field. Make sure `vancouver_cs_data.csv` is saved in the same directory as your Python file. 

```python
import pandas as pd
import os

# Set the current working directory to be the script location
script_dir = os.path.dirname(os.path.abspath(__file__))
os.chdir(script_dir)

# Load your uploaded dataset
df = pd.read_csv("vancouver_cs_data.csv")

# Inspect columns
print(df.columns)

# Convert date column (adjust name if needed)
df["LOCAL_DATE"] = pd.to_datetime(df["LOCAL_DATE"])

# Keep relevant fields
df = df[["LOCAL_DATE", "TOTAL_PRECIPITATION"]].copy()

# Drop missing or flagged values
df = df[df["TOTAL_PRECIPITATION"].notna()]

# Rename for convenience
df.rename(columns={"TOTAL_PRECIPITATION": "P_mm"}, inplace=True)

print(df.head())
```

This will print all columns and then the first five rows of the trimmed and renamed dataframe in the console.

```console
Index(['STATION_NAME', 'CLIMATE_IDENTIFIER', 'LOCAL_DATE', 'PROVINCE_CODE',
       'LOCAL_YEAR', 'LOCAL_MONTH', 'LOCAL_DAY', 'TOTAL_PRECIPITATION',
       'TOTAL_RAIN', 'TOTAL_SNOW'],
      dtype='object')
  LOCAL_DATE  P_mm
0 1970-01-01   0.0
1 1970-01-02   1.8
2 1970-01-03   0.0
3 1970-01-04   0.0
4 1970-01-05   0.0
```

We should now have a clean time series of daily precipitation depths in mm. Sicne EC daily data may be derived from either direct daily observations or aggregated hourly station data, this column is already a processed representation of precipitation over each climatological day.

Next, let's extract annual maximum daily rainfall data. This replicates the core idea used in IDF derivation and is foundational as IDF curves are based on the statistical behaviour of extreme rainfall events. 

```python
# Add year column
df["year"] = df["LOCAL_DATE"].dt.year

# Annual maxima (24-hour rainfall)
annual_max = df.groupby("year")["P_mm"].max()

print(annual_max.head())
```

The above will print out (when ignoring outputs from the previous step) the first five rows of annual maxima.

```console
year
1970     55.1
1971     61.0
1972    203.2
1973     61.0
1974     82.6
```

We will now fit a Gumbel distribution to the annual maxima following the standard hydrologic practice. The Method of Moments will be used to be consistent with EC. We will not use `scipy.stats` built in function `gumbel_r` here as it does not use the Method of Moments. Recall the the parameters derived from the sample mean sand standard deviation (from our dataset) are defined as

$$ \beta = \frac{\sqrt{6}}{\pi}s \hspace{8pt} \text{and} \hspace{8pt}  \mu = \bar{x} - \gamma\beta $$

where:

$\bar{x}$ is the sample mean;
$\\s$ is the sample standard deviation, and;
$\\\gamma = 0.5772$ is the Euler-Mascheroni constant. 

We then implement this directly.


```python
import numpy as np

# Annual extrema
x = annual_max.values

# Sample mean and standard deviation
mean = np.mean(x)
std = np.std(x, ddof=1)

# Euler-Mascheroni constant
gamma = 0.5772

# Calculating location and scale
beta = std * np.sqrt(6) / np.pi
mu = mean - gamma * beta

print(f"mu (location) = {mu:.2f}")
print(f"beta (scale) = {beta:.2f}")
```

The calculated location and scale should be

```console
mu (location) = 54.61
beta (scale) = 21.33
```

To compute the Gumbel quantile function using the above calculated parameters, we use

$$ x_T = \mu - \beta\ln \left[-\ln \left(1-\frac{1}{T}\right)\right] $$

The rainfall depths are then calculated for standard return periods

```python
T = np.array([2, 5, 10, 25, 50, 100])

P24 = mu - beta * np.log(-np.log(1 - 1/T))

for t, val in zip(T, P24):
    print(f"{t}-year 24hr rainfall: {val:.1f} mm")
```

This provides the 24-hour design storm depths, which will form the backbone of the IDF curve. The console output should be (when ignoring previous outputs):

```console
2-year 24hr rainfall: 62.4 mm
5-year 24hr rainfall: 86.6 mm
10-year 24hr rainfall: 102.6 mm
25-year 24hr rainfall: 122.8 mm
50-year 24hr rainfall: 137.9 mm
100-year 24hr rainfall: 152.7 mm
```

Converting to intensities (mm/hr) is very easy, we just divide by the total depth by 24 hours. 

```python
i24 = P24 / 24.0
```

Because daily data contain no information about sub-daily storm intensity, we must apply a duration scaling relationship. Emperical rainfall studies consistently show that intensity increases as duration decreases, typically following a power law. First, lets define our durations

```python
durations_hr = np.array([5/60, 10/60, 15/60, 0.5, 1, 2, 6, 12, 24])
```

To which we can then apply 

$$i(d) = i_{24h}\left(\frac{d}{24}\right)^{-b}$$

where $b = 0.45$ is a reasonable choice for coastal climates like Vancouver. You are likely (and rightfully so) asking how we pulled a $b$ value out of thin air. This will be discussed further [below](#selection-of-duration-scaling-exponent-), but for now we can just accept it as true.

```python
b = 0.45

idf_curves = {}

for idx, t in enumerate(T):
    intensities = i24[idx] * (durations_hr / 24)**(-b)
    idf_curves[t] = intensities
```

We won't print the `idf_curves` dictionary here as it is currently a dictionary of arrays and is not very useful to look at directly. Instead, lets plot the curve. To be comparable to the format presented by EC previously, we'll explicitly define alternative x-and-y-axis ticks (rather than a simple log-log).

```python
# Convert durations to minutes for plotting
durations_min = durations_hr * 60

# Create figure
plt.figure(figsize=(8,6))

# Plot curves
for t in T:
    plt.plot(durations_min, idf_curves[t], label=f"{t}-yr")

# Log scale (standard for IDFs)
plt.xscale("log")
plt.yscale("log")

# ----- Custom X-axis ticks -----
# Minutes (5–60) and hours (2–24 converted to minutes)
x_ticks_minutes = [5, 10, 15, 30, 60]
x_ticks_hours = [2*60, 6*60, 12*60, 24*60]

x_ticks = x_ticks_minutes + x_ticks_hours
x_labels = ["5", "10", "15", "30", "60", "2h", "6h", "12h", "24h"]

plt.xticks(x_ticks, x_labels)

# ----- Custom Y-axis ticks -----
y_ticks = (
    list(range(1, 11)) +          # 1–10
    list(range(20, 101, 10)) +    # 20–100
    list(range(200, 601, 100))    # 200–600
)

plt.yticks(y_ticks, [str(y) for y in y_ticks])

# Labels and formatting
plt.xlabel("Duration")
plt.ylabel("Intensity (mm/hr)")
plt.title("IDF Curves (Method of Moments, Vancouver Harbour CS)")
plt.legend()
plt.grid(True, which="both", linestyle="--", linewidth=0.5)

plt.show()
```
The output should be the figure below.

![Daily Derived IDF for Vancouver Harbour CS](/img/idf_curves/daily_derived_idf_vancouver_cs.png)

How does this compare to that derived by EC? Let's compare. I've imported the tabulated IDF data for Vancouver Harbour CS from EC and we will import it into our Python script.

```python
# Durations (minutes)
durations_min = np.array([5, 10, 15, 30, 60, 120, 360, 720, 1440])

# Return periods matching our derivation
T = np.array([2, 5, 10, 25, 50, 100])

idf_eccc = {
    2:  [40.6, 29.8, 23.8, 16.1, 10.7, 8.3, 5.6, 4.1, 2.8],
    5:  [55.1, 44.2, 35.0, 22.1, 13.8, 10.3, 6.8, 4.9, 3.6],
    10: [64.7, 53.8, 42.4, 26.0, 15.9, 11.6, 7.5, 5.5, 4.1],
    25: [76.8, 65.9, 51.8, 31.0, 18.6, 13.2, 8.4, 6.2, 4.7],
    50: [85.8, 74.8, 58.7, 34.7, 20.5, 14.4, 9.1, 6.7, 5.2],
    100:[94.7, 83.7, 65.6, 38.4, 22.4, 15.6, 9.8, 7.2, 5.6]
}
```

We will the overlay our own curves with EC values.

```python
plt.figure(figsize=(9,6))

for t in T:
    # Our derived IDF
    plt.plot(durations_min, idf_curves[t],
             linestyle='--', linewidth=1.8,
             label=f"{t}-yr (Derived, MOM)")

    # EC IDF
    plt.plot(durations_min, idf_eccc[t],
             linestyle='-', marker='o',
             linewidth=2,
             label=f"{t}-yr (ECCC)")

plt.xscale("log")
plt.yscale("log")

# Custom X-axis (as previously defined)
plt.xticks(
    [5,10,15,30,60,120,360,720,1440],
    ["5","10","15","30","60","2h","6h","12h","24h"]
)

# Custom Y-axis
y_ticks = list(range(1,11)) + list(range(20,101,10)) + list(range(200,601,100))
plt.yticks(y_ticks, [str(y) for y in y_ticks])

plt.xlabel("Duration")
plt.ylabel("Intensity (mm/hr)")
plt.title("IDF Comparison: Derived (MOM + Scaling) vs ECCC")
plt.grid(True, which="both", linestyle="--", linewidth=0.5)

plt.legend(ncol=2, fontsize=8)
plt.show()
```
Which provides the plot below. Generally not a bad fit, thought it is a bit challenging to actually see what the absolute or relative differences are.

![Daily Derived IDF for Vancouver Harbour CS vs. EC](/img/idf_curves/derived_vs_ec_vancouver_harbour_cs.png)

Instead of trying to calculate the different between the two plots visually, we can instead create a table.

```python
# Durations (minutes)
durations_min = np.array([5, 10, 15, 30, 60, 120, 360, 720, 1440])

# Initialize list to store results
results = []

for t in T:
    for i, d in enumerate(durations_min):
        derived = idf_curves[t][i]
        eccc = idf_eccc[t][i]
        
        abs_diff = derived - eccc
        pct_diff = (abs_diff / eccc) * 100
        
        results.append({
            "Return Period (yr)": t,
            "Duration (min)": d,
            "Derived (mm/hr)": derived,
            "ECCC (mm/hr)": eccc,
            "Abs Diff (mm/hr)": abs_diff,
            "% Diff": pct_diff
        })

df_compare = pd.DataFrame(results)

print(df_compare.head())
```
This should output (ignoring previous outputs) the first five entires of the comparison table.

```console
   Return Period (yr)  Duration (min)  Derived (mm/hr)  ECCC (mm/hr)  Abs Diff (mm/hr)     % Diff
0                   2               5        33.258337          40.6         -7.341663 -18.082915
1                   2              10        24.346527          29.8         -5.453473 -18.300243
2                   2              15        20.285978          23.8         -3.514022 -14.764796
3                   2              30        14.850205          16.1         -1.249795  -7.762699
4                   2              60        10.870987          10.7          0.170987   1.598007
```
I've nicely formatted the full results below for direct viewing.

| Return Period (yr) | Duration (min) | Derived (mm/hr) | ECCC (mm/hr) | Abs Diff (mm/hr) | % Diff |
|:--------------------:|:----------------:|:------------------:|:--------------:|:-------------------:|:--------:|
| 2 | 5 | 33.258 | 40.6 | -7.342 | -18.083 |
| 2 | 10 | 24.347 | 29.8 | -5.453 | -18.300 |
| 2 | 15 | 20.286 | 23.8 | -3.514 | -14.765 |
| 2 | 30 | 14.850 | 16.1 | -1.250 | -7.763 |
| 2 | 60 | 10.871 | 10.7 | 0.171 | 1.598 |
| 2 | 120 | 7.958 | 8.3 | -0.342 | -4.120 |
| 2 | 360 | 4.854 | 5.6 | -0.746 | -13.321 |
| 2 | 720 | 3.553 | 4.1 | -0.547 | -13.333 |
| 2 | 1440 | 2.601 | 2.8 | -0.199 | -7.100 |
| 5 | 5 | 46.140 | 55.1 | -8.960 | -16.261 |
| 5 | 10 | 33.777 | 44.2 | -10.423 | -23.582 |
| 5 | 15 | 28.143 | 35.0 | -6.857 | -19.590 |
| 5 | 30 | 20.602 | 22.1 | -1.498 | -6.778 |
| 5 | 60 | 15.082 | 13.8 | 1.282 | 9.287 |
| 5 | 120 | 11.040 | 10.3 | 0.740 | 7.189 |
| 5 | 360 | 6.734 | 6.8 | -0.066 | -0.969 |
| 5 | 720 | 4.930 | 4.9 | 0.030 | 0.605 |
| 5 | 1440 | 3.609 | 3.6 | 0.009 | 0.242 |
| 10 | 5 | 54.669 | 64.7 | -10.031 | -15.503 |
| 10 | 10 | 40.020 | 53.8 | -13.780 | -25.613 |
| 10 | 15 | 33.346 | 42.4 | -9.054 | -21.355 |
| 10 | 30 | 24.410 | 26.0 | -1.590 | -6.114 |
| 10 | 60 | 17.869 | 15.9 | 1.969 | 12.387 |
| 10 | 120 | 13.081 | 11.6 | 1.481 | 12.769 |
| 10 | 360 | 7.979 | 7.5 | 0.479 | 6.386 |
| 10 | 720 | 5.841 | 5.5 | 0.341 | 6.198 |
| 10 | 1440 | 4.276 | 4.1 | 0.176 | 4.288 |
| 25 | 5 | 65.446 | 76.8 | -11.354 | -14.784 |
| 25 | 10 | 47.909 | 65.9 | -17.991 | -27.300 |
| 25 | 15 | 39.919 | 51.8 | -11.881 | -22.937 |
| 25 | 30 | 29.222 | 31.0 | -1.778 | -5.735 |
| 25 | 60 | 21.392 | 18.6 | 2.792 | 15.010 |
| 25 | 120 | 15.660 | 13.2 | 2.460 | 18.635 |
| 25 | 360 | 9.552 | 8.4 | 1.152 | 13.711 |
| 25 | 720 | 6.992 | 6.2 | 0.792 | 12.779 |
| 25 | 1440 | 5.119 | 4.7 | 0.419 | 8.907 |
| 50 | 5 | 73.440 | 85.8 | -12.360 | -14.405 |
| 50 | 10 | 53.761 | 74.8 | -21.039 | -28.126 |
| 50 | 15 | 44.795 | 58.7 | -13.905 | -23.688 |
| 50 | 30 | 32.792 | 34.7 | -1.908 | -5.499 |
| 50 | 60 | 24.005 | 20.5 | 3.505 | 17.098 |
| 50 | 120 | 17.573 | 14.4 | 3.173 | 22.033 |
| 50 | 360 | 10.719 | 9.1 | 1.619 | 17.786 |
| 50 | 720 | 7.846 | 6.7 | 1.146 | 17.111 |
| 50 | 1440 | 5.744 | 5.2 | 0.544 | 10.460 |
| 100 | 5 | 81.376 | 94.7 | -13.324 | -14.070 |
| 100 | 10 | 59.571 | 83.7 | -24.129 | -28.828 |
| 100 | 15 | 49.635 | 65.6 | -15.965 | -24.336 |
| 100 | 30 | 36.335 | 38.4 | -2.065 | -5.377 |
| 100 | 60 | 26.599 | 22.4 | 4.199 | 18.745 |
| 100 | 120 | 19.472 | 15.6 | 3.872 | 24.818 |
| 100 | 360 | 11.877 | 9.8 | 2.077 | 21.191 |
| 100 | 720 | 8.694 | 7.2 | 1.494 | 20.753 |
| 100 | 1440 | 6.365 | 5.6 | 0.765 | 13.653 |

Notice from the above the relationship between the derived values and the EC reference values: at short durations, the derived intensities tend to underestimate the official values, often by 15–30%, while at longer durations the differences shrink and sometimes even reverse, with the derived values becoming slightly higher. This suggests that the underlying model or method used to generate the derived intensities aligns more closely with ECCC data at longer durations, while diverging more at the high‑intensity, short‑duration end of the spectrum. Overall, the dataset reflects a consistent hydrometeorological structure: intensity decreases with duration, increases with return period, and exhibits systematic bias patterns when compared to the reference dataset.

**It is critical to note** that we are comparing our projection to the ***empirical*** data provided by EC, not the ***fit curve***. When we look at a comparison of the fit curves (i.e. the straight lines in the log-log space), our derived prediction using daily data is very similar to that developed by EC. Our overestimations and underestimations generally occur in the same places. This suggests that derivation using intensity scaling relationships, at least in this case for Vancouver Harbour CS, provides a defensible resultant IDF curve.

## Selection of Duration Scaling Exponent '$b$'

A critical step in deriving short-duration IDF curves from daily rainfall data is the selection of the duration scaling exponent $b$, which governs how rainfall intensity varies with storm duration. In the absense of sub-hourly observations, rainfall intensities are often estimated using a power-law scaling relationship of the form

$$ i(d) = i_{24}\left(\frac{d}{24}\right)^{-b} $$

where 

$\\ i(d)$ is the rainfall intensity at duration $d$; 
$\\i_{24}$ is the 24-hour intensity, and;
$\\b$ is a non-dimensional scaling exponent.

This forumulation is consistent with classical IDF theory, in which rainfall intensity decreases with increasing duration according to a power-law or near power-law relationship. The exponent $b$, sometimes referred to as the duration exponent or scaling exponent, encapsulates the time-structure of rainfall process and is therefore one of the most influential parameters in the derivation of inferred IDF curves. Several alternatives with different applicability can be reviewed in further detail [here](https://en.wikipedia.org/wiki/Intensity-duration-frequency_curve).

### Physical Interpretation of $b$

The parameter $b$ controls the rate of change of intensity with duration, and therefore reflects the dominant rainfall-generating mechanisims in a region. A larger value of $b$ implies a steeper decrease in intensity with increasing duration, indicating that short-duration storms are relatively more intense compared to long-duration storms. Conversely, a smaller value of $b$ suggests a more gradual decay of intensity, characteristic of rainfall regimes dominated by longer-duration, lower-intensity events. 

From a hydrological perspective, the mganitude of $b$ is tied to the scale invariance behaviour of rainfall extremes, where rainfall intensities at different durations exhibit approximate self-similar behaviour across time scales. However, real precipitation processes often deviate from perfect scaling, particularly at very short (convective) or very long (frontal, synoptic) durations, leading to curvature in observed IDF relationships. See [this article by copernicus](https://hess.copernicus.org/articles/25/6479/2021/) that goes into extensive detail about these relationships. 

> **Scale invariance** and **self-similar** behaviour describe a pattern or shape that often looks similar when you zoom in or out. The full definition of scale invariance is: *The property of a system, function, or law that remains unchanced when all relevant scales are multiplied by a common factor*.

### Typical Ranges of $b$ in Practice
In the absense of locally calibrated sub-hourly data, engineers typically select $b$ based on values reported in the literature or inferred from nearby IDF curves (trial and error to get the best approximate fit from non sub-hourly data when compared to an EC IDF curve at the closest station is sometimes appropriate). Across hydrological studies, the exponent $b$ (or equivalently the exponent $n$ in IDF formulations) generally falls within the range

$$ 0.3 \leq b \leq 0.6 $$

This range reflects a wide variety of climatic regimes and is consistent with emperical IDF formulations based on power-law relationships. Below is a table that summarizes typical values, however this is **not meant to be a simple lookup table**, as values are calibrated for each individual case. You will **not find a standard table**, but rather only a range of values provided in literature for specific contexts. Use the below table only for conceptual understanding, not a lookup table. A few direct references which demonstrate exponent ranges are provided below.

## Depth-Duration-Frequency (DDF) Conversion

[work in progress]