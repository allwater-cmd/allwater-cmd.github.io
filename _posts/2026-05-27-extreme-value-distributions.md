## Extreme Value Distributions
Extreme value theory, or extreme value analysis (EVA), is the study of extremes in statistical distributions. This theory is extensively applied within hydrology for estimating rainfall distributions and / or flood events (such as a 100-year rainfall or 100-year flood). Similarly, it may be applied to estimate wave actions in the design hydraulic infrastructure. EVA may assess either the maxima or minima within a dataset, however, it is far more common to assess maxima within hydrology. 

Two primary approaches existing for EVA, namely:
1. Deriving block maxima (or minima) series as a preliminary step. In hydrology, these blocks are typically the annual maxima (or minima) and are used to generate an **annual maxima series** (AMS). 


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
