# Extreme Value Distributions
Extreme value theory, or extreme value analysis (EVA), is the study of extremes in statistical distributions. This theory is extensively applied within hydrology for estimating rainfall distributions and / or flood events (such as a 100-year rainfall or 100-year flood). Similarly, it may be applied to estimate wave actions in the design hydraulic infrastructure. EVA may assess either the maxima or minima within a dataset, however, it is far more common to assess maxima within hydrology. 

Two primary approaches existing for EVA, namely:
1. Deriving block maxima (or minima) series as a preliminary step. In hydrology, these blocks are typically the annual maxima (or minima) and are used to generate an **annual maxima series** (AMS).
2. Extracting peak values reached for any period from within a continuous record during which values exceed a certain threshhold (or, of course, falls below a certain threshold). This method is generally referred to as the **peak over threshold** method (POT).

This post will focus on only AMS data as it is the most relevant to hydrology. AMS data analysis may partly rely on the results of the Fisher-Tippett-Gnedenko theorem, which leads to the generalized extreme value distribution (GEVD) being selected for fitting. However, in practice, various procedures are applied to support fitting from a wider ranger of distribtuions. But let's take a minute to discuss Fisher-Tippett-Gnedenko theorem, as it serves as a foundational component in understanding EVA. 

## Fisher-Tippett-Gnedenko Theorem
Henceforth in this post, Fisher-Tippett-Gnedenko theorem refers to the theorem within mathematical statistics. Its real analysis counterpart is referred to as the **extreme value theorem**. Fisher-Tippet-Gnedenko theorem tells us what happens to the maximum (or minimum) of a large sample of random values as the sample size grows very large. A helpful comparison (while not entirely correct) would be to consider this theorem as the central limit theorem but for extremes, where given a sufficiently large sample size, the distribution of the sample mean will eventually approximate a known distribtion (normal dsitribution, in the case of central limit theory).

The Fisher-Tipped-Gnedenko theorem states that if you take the maximums (or minimums) of many random samples, they tend to follow one of **three possible distributions**, no matter what the original data may have looked like. These three possible limit distribtions arise when you scale and shift the maximum values properly and are categorized by type:
* Type 1: Gumbel Distribtuion (typical for rainfall and flood data with light tails).
* Type 2: Fréchet Distribution (for heavy-tailed phenomena such as rare catestrophic events).
* Type 3: Weibull (bounded data where values cannot exceed a limit, such as physical limits on flow). 

So depending on a given dataset behaves at the extremes, the distribution of the largest values will look like one of the above. A simplified mathematical representation of what is stated above is:

\\[
\displaylines{
\text{Let } X_1, X_2, \ldots, X_n \text{ be i.i.d. random variables with distribution } F(x). \\
\text{Define } M_n = \max(X_1, X_2, \ldots, X_n). \\
\text{Then there exist constants } a_n > 0, \; b_n \in \mathbb{R} \text{ such that:} \\
\lim_{n \to \infty} P\left( \frac{M_n - b_n}{a_n} \le x \right) = G(x)
}
\\]

\\[ \sum_{i=1}^{\infty}\frac{1}{n} \\]

Due to a plugin called `jekyll-titles-from-headings` which is supported by GitHub Pages by default. The above header (in the markdown file) will be automatically used as the pages title.

If the file does not start with a header, then the post title will be derived from the filename.

This is a sample blog post. You can talk about all sorts of fun things here.

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
