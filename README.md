# Replication & Causal Forest Extension: Hidrobo, Peterman & Heise (2016)

**The Effect of Cash, Vouchers, and Food Transfers on Intimate Partner Violence**

> Cedric Kopp, Victoria Landazuri & Aman Arora — ESMT Berlin, Term 2 — Causal Inference & AI

---

## Original Paper

Hidrobo, M., Peterman, A., & Heise, L. (2016). The Effect of Cash, Vouchers, and Food Transfers on Intimate Partner Violence: Evidence from a Randomized Experiment in Northern Ecuador. *American Economic Journal: Applied Economics*, 8(3), 284–303. https://doi.org/10.1257/app.20150082

The paper evaluates a randomized controlled trial (RCT) conducted in northern Ecuador in which households were assigned to receive cash, food, or voucher transfers — or to a control group. The main finding is that all three transfer modalities significantly reduce intimate partner violence (IPV), with the largest reductions in controlling and emotional violence. The original analysis uses probit regressions with clustered standard errors, estimated in Stata.

---

## Data

The replication data are available from the Harvard Dataverse:
https://dataverse.harvard.edu/dataset.xhtml?persistentId=doi:10.7910/DVN/YBGPAP

To reproduce the analysis, download `DV_baseline_followup.dta` and place it at:
```
Data-and-dofiles/Data/DV_baseline_followup.dta
```
relative to the `.Rmd` file.

---

## Contents

The analysis is structured in five parts, all contained in `Hidrobo2016_Replication_CausalForest.Rmd`:

| Part | Description |
|------|-------------|
| 1 | Variable construction — translates the original Stata do-file into R, replicating treatment indicators, composite IPV outcomes, and sample eligibility flags |
| 2 | Replication of main results — balance test and Table 2 linear probability models with cluster-robust standard errors |
| 3 | Two-step causal forest pipeline — propensity forest, outcome regression forests with variable selection, and causal forests across three covariate specifications |
| 4 | Results — OLS vs causal forest comparison, Best Linear Projection, CATE quartile analysis, calibration tests, and variable importance |
| 5 | Robustness checks — five checks covering overlap, placebo test, specification comparison, threshold sensitivity, and bootstrap inference |

---

## Extensions Beyond the Original Paper

The original paper estimates average treatment effects using probit regression. This replication extends the analysis in three directions:

**1. R translation of the Stata replication**
The full variable construction and main regression results are reproduced in R, using linear probability models with clustered standard errors (sandwich estimator) as in the paper's own robustness appendix.

**2. Causal forest for treatment effect heterogeneity**
Using the `grf` package (Athey & Wager), a two-step causal forest pipeline is estimated for each of the three IPV outcomes:
- A propensity forest verifies the RCT randomization and generates orthogonalized treatment propensity scores (W.hat)
- Outcome regression forests identify predictive covariates and generate outcome fits (Y.hat) for orthogonalization
- Causal forests are estimated using the double-orthogonalized residuals, following the recommended Athey/Wager pipeline

**3. Three covariate specifications motivated by policy realism**
Rather than a single model, three specifications are estimated in parallel across all outcomes:

| Specification | Covariates | Policy interpretation |
|---|---|---|
| A | All selected covariates | Full information benchmark |
| B | Excludes cross-outcome baseline IPV | Policymaker knows own-outcome history only |
| C | Excludes all baseline IPV variables | Policymaker uses observable characteristics only |

Specification C represents the most realistic targeting scenario: demographic and socioeconomic data only, with no confidential survey responses.

**4. Out-of-fold CATE evaluation via 5-fold cross-validation**
Rather than evaluating estimated CATEs in-sample, a 5-fold cross-validation scheme generates out-of-fold predictions for every observation. This avoids inflated heterogeneity estimates from overfitting and is used for the quartile analysis and distributional comparisons across specifications.

**5. Heterogeneity analysis and profiling**
- Best Linear Projection (Athey & Wager) identifies which covariates drive heterogeneity in treatment effects across all three specifications and outcomes
- CATE quartile analysis compares realized ATEs in the top and bottom quartile of predicted CATEs
- A profile table characterises the socioeconomic and demographic characteristics of women in each CATE group

**6. Five robustness checks**
- Overlap / propensity score check confirming RCT balance
- Placebo test (permuted treatment labels) to rule out artefacts
- Full vs selected covariate comparison to validate the pre-selection step
- Threshold sensitivity analysis (0.1×, 0.2×, 0.5× importance threshold)
- Bootstrap ATE confidence intervals (B = 200) alongside asymptotic SEs

---

## Key Findings

- Married women seem to benefit less than women in informal unions — possibly reflecting differences in bargaining dynamics within relationships.
- Indigenous women tend to benefit more, especially for controlling behaviors — a potentially important finding for targeting.
- Having more young children (0–5) dampens the treatment effect across multiple outcomes.
- Province effects (Carchi vs Sucumbíos) may moderate treatment effectiveness.

---

## Rendered Output

A pre-rendered HTML version of the full analysis is available here:
[View rendered report on RPubs](https://rpubs.com/CedricKopp/Hidrobo2016_Replication_CausalForest)

---

## Requirements

R packages: `grf`, `haven`, `dplyr`, `ggplot2`, `corrplot`, `caret`, `sandwich`, `lmtest`, `margins`, `Hmisc`, `tidyr`

Install all at once:
```r
install.packages(c("grf", "haven", "dplyr", "ggplot2", "corrplot",
                   "caret", "sandwich", "lmtest", "margins", "Hmisc", "tidyr"))
```

Knit `Hidrobo2016_Replication_CausalForest.Rmd` in RStudio. Note that the bootstrap (B = 200) and 5-fold CV across nine causal forests per fold make full rendering computationally intensive; expect roughly 20–40 minutes depending on hardware.

---

## Licence

The code and analysis in this repository (`.Rmd`, README) are released under the [MIT Licence](LICENSE).

The underlying data (`DV_baseline_followup.dta`) are **not** covered by this licence. They are the property of the original authors and are distributed via the Harvard Dataverse under their own terms. Please refer to the Dataverse entry linked in the Data section above before using or redistributing the data.
