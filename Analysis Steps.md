# Guided SPSS Project: Predictors of Systolic Blood Pressure

**Dataset:** `bp_health_dataset.csv` (180 simulated adults, 6 predictors + 1 outcome)
**Technique:** Bivariate correlation → Multiple linear regression
**Skill level:** Intermediate — assumes you know the concepts, walks the exact clicks

---

## 1. The Project

**Title:** Predictors of Systolic Blood Pressure Among Adults: A Correlation and Multiple Regression Analysis

**Research question:** Do age, BMI, physical activity, salt intake, smoking status, stress level, and gender significantly predict systolic blood pressure (SBP)?

**Hypotheses:**
- H1: Age is positively associated with SBP
- H2: BMI is positively associated with SBP
- H3: Physical activity is negatively associated with SBP
- H4: Salt intake is positively associated with SBP
- H5: Smokers have higher SBP than non-smokers
- H6: The combined predictor set significantly explains variance in SBP

**Variable dictionary**

| Variable | SPSS name | Type | Measure | Coding |
|---|---|---|---|---|
| Participant ID | `participant_id` | Numeric | Nominal | 1–180 |
| Age (years) | `age` | Numeric | Scale | — |
| Gender | `gender` | Numeric | Nominal | 0=Female, 1=Male |
| BMI (kg/m²) | `bmi` | Numeric | Scale | has 6 missing (MCAR) |
| Physical activity (min/week) | `physical_activity_min` | Numeric | Scale | — |
| Salt intake (g/day) | `salt_intake_g` | Numeric | Scale | — |
| Smoking status | `smoking_status` | Numeric | Nominal | 0=Non-smoker, 1=Smoker |
| Stress level | `stress_level` | Numeric | Ordinal (treat as Scale for regression) | 1–10, has 4 missing |
| Systolic BP (mmHg) | `systolic_bp` | Numeric | Scale | Outcome (DV) |

---

## 2. Import the Data

1. **File → Import Data → CSV Data…**
2. Select `bp_health_dataset.csv`
3. In the preview window: confirm "First line contains variable names" is checked → **OK**
4. SPSS auto-creates all columns as Numeric. You'll fix measurement levels next.

---

## 3. Set Up Variable View

Switch to **Variable View** tab and configure:

| Column | What to set |
|---|---|
| **Label** | Full description, e.g. `bmi` → "Body Mass Index (kg/m²)" |
| **Values** | For `gender`: 0="Female", 1="Male". For `smoking_status`: 0="Non-smoker", 1="Smoker" |
| **Missing** | Leave as "None" — missing cells are already blank/system-missing from the CSV |
| **Measure** | `participant_id`, `gender`, `smoking_status` → Nominal. Everything else → Scale |

**Why this matters:** SPSS uses the Measure column to decide which procedures and chart types are even offered to you in the menus, and value labels are what make your output tables readable instead of showing "0" and "1".

---

## 4. Data Screening

**Analyze → Descriptive Statistics → Frequencies**
- Move all variables in → check "Display frequency tables" for `gender` and `smoking_status` only (categorical)
- Under **Statistics**: tick Mean, Std deviation, Min, Max, Missing

Run it. Confirm:
- No impossible values (e.g., negative age, BMI of 200)
- Missing count matches expectation: `bmi` = 6 missing, `stress_level` = 4 missing

**Handling the missing data:** With only ~3% missing and no evidence it depends on other variables (MCAR is a reasonable assumption here), the two defensible options are:
- **Listwise deletion** (default in Regression) — simplest, fine at this missingness level
- **Mean substitution or EM imputation** (Analyze → Multiple Imputation) — more defensible if missingness exceeds ~5%

→ For this project, use listwise deletion (SPSS does this automatically in the Regression procedure). Note this choice explicitly in your methods write-up — that's what makes it a professional analysis rather than a black box.

---

## 5. Assumption Checks (before you trust a regression)

### 5a. Normality of the DV
**Analyze → Descriptive Statistics → Explore**
- Dependent list: `systolic_bp`
- Plots tab → check "Normality plots with tests" and "Histogram"
- Read the **Shapiro-Wilk** row in the Tests of Normality table (n<2000 → use Shapiro-Wilk, not Kolmogorov-Smirnov)
- p > .05 → normality is reasonable

### 5b. Linearity
**Graphs → Chart Builder** → Scatter/Dot → Simple Scatter
- Plot `systolic_bp` (Y) against each scale predictor (X) one at a time: `age`, `bmi`, `physical_activity_min`, `salt_intake_g`
- Look for a roughly straight-line trend, not a curve

### 5c. Multicollinearity
Checked automatically inside the regression procedure (Step 6) via VIF — don't run separately.

### 5d. Homoscedasticity & independence of errors
Also checked inside the regression procedure via the residual scatterplot and Durbin-Watson statistic (Step 6).

---

## 6. Bivariate Correlation

**Analyze → Correlate → Bivariate**
- Variables: `systolic_bp`, `age`, `bmi`, `physical_activity_min`, `salt_intake_g`, `stress_level`
- Correlation Coefficient: **Pearson**
- Tick "Flag significant correlations"

**Reading the output table:**
- Each cell = Pearson's *r*, with significance (p) below it
- |r| < .3 weak · .3–.5 moderate · > .5 strong
- Expect: `bmi` and `salt_intake_g` moderately positive with `systolic_bp`; `physical_activity_min` weakly negative; `stress_level` close to zero (built into the data on purpose — a good example of a hypothesized predictor that doesn't pan out)

This table becomes your **Table 1** in the results section and also serves as your multicollinearity pre-check — no predictor pair should correlate above ~.7–.8 with each other.

---

## 7. Multiple Linear Regression

**Analyze → Regression → Linear**
- Dependent: `systolic_bp`
- Independent(s): `age`, `bmi`, `physical_activity_min`, `salt_intake_g`, `smoking_status`, `stress_level`, `gender`
- Method: **Enter** (all predictors forced in simultaneously — appropriate for a theory-driven model like this one; use Stepwise only if you're doing exploratory variable selection, which is a separate, more contested approach)

**Statistics button** — tick:
- Estimates, Confidence intervals
- Model fit
- R squared change
- Descriptives
- **Collinearity diagnostics** (gives VIF/Tolerance)
- **Durbin-Watson**

**Plots button:**
- Y: `*ZRESID`, X: `*ZPRED` (residuals vs predicted — checks homoscedasticity)
- Tick "Histogram" and "Normal probability plot" (checks residual normality)

Click **OK**.

---

## 8. Reading the Output (table by table)

**Model Summary**
- **R²**: proportion of variance in SBP explained by the model
- **Adjusted R²**: use this one when reporting — it penalizes for the number of predictors
- **Durbin-Watson**: should be roughly 1.5–2.5; confirms residuals aren't autocorrelated

**ANOVA table**
- Tests whether the model as a whole predicts SBP significantly better than the mean alone
- Report as: *F*(df1, df2) = value, *p* < .05

**Coefficients table** — the core result
- **B**: unstandardized coefficient — "for each 1-unit increase in X, SBP changes by B units, holding others constant"
- **Beta (standardized)**: lets you compare predictor strength on the same scale — which predictor matters most
- **t, Sig.**: is this specific predictor significant?
- **95% CI for B**: does the interval cross zero? If yes, not significant
- **Tolerance / VIF**: VIF > 10 (or Tolerance < .1) signals a multicollinearity problem

**What you should find in this dataset:** `age`, `bmi`, `salt_intake_g`, and `smoking_status` should emerge as significant positive predictors; `physical_activity_min` a significant negative predictor; `stress_level` and `gender` likely non-significant. That mixed result is intentional — a real project rarely confirms every hypothesis, and explaining a null finding correctly is part of doing this like a professional.

---

## 9. Reporting the Results (APA-style template)

> A multiple linear regression was conducted to examine predictors of systolic blood pressure. The overall model was statistically significant, *F*(7, N) = X.XX, *p* < .001, explaining XX% of the variance in systolic blood pressure (adjusted R² = .XX). [Predictor] emerged as the strongest significant predictor (β = .XX, *p* < .001), followed by [predictor] (β = .XX, *p* = .XXX)... [Predictor] and [predictor] did not significantly predict systolic blood pressure in this model (*p*s > .05).

Fill in the bracketed values from your own output — don't hardcode numbers from this guide, since your exact run will vary slightly depending on rounding and any listwise-deletion exclusions.

---

## 10. Optional Extension (once the core model is solid)

Test whether smoking's effect on SBP *depends on* age (an interaction/moderation):
1. **Transform → Compute Variable** → create `age_x_smoking` = `age * smoking_status`
2. Re-run the regression, adding `age_x_smoking` as an 8th predictor
3. If it's significant, the relationship between age and SBP differs by smoking status — worth a follow-up simple-slopes discussion

---

## Files
- `bp_health_dataset.csv` — import this first
- This guide — follow section by section inside SPSS
