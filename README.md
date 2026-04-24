# Privacy-Aware TBI Survival Analysis
Landmark-based survival analysis for predicting time to severe RAP deterioration in TBI patients using early physiological signals. Implements Kaplan-Meier, Cox Proportional Hazards, and Random Survival Forest models to compare interpretable and ML-based time-to-event prediction. This project extends the original survival-analysis workflow with a privacy-aware preprocessing layer that de-identifies patient-level data before modeling.

## Overview

The analysis uses a time-to-event framework centered on the compensatory reserve index (RAP). Rather than only asking whether deterioration occurs, this project asks **when** severe deterioration occurs. To support this, landmark survival models were developed using early-window physiological features derived from RAP, ICP, CPP, MAP, and AMP.

Two landmark settings were explored:

- **3-hour landmark**
- **6-hour landmark**

For each landmark, only patients who remained event-free up to the landmark time were included. Features were extracted from the early monitoring window, and survival models were used to predict time to subsequent severe deterioration.

## Clinical Question

**From the first few hours of monitoring, can we predict how soon a patient will experience severe compensatory reserve deterioration?**

## Privacy-Aware Preprocessing

Before survival modeling, the pipeline applies de-identification and privacy-preserving transformations:

| Original field | Privacy-aware transformation |
|---|---|
| `ID` | Hashed into `anon_patient_id` and then removed |
| `name` | Removed if present |
| `age` | Converted into age groups such as `50-59`, `60-69`, `70+` |
| `Sex (M/F)` | Standardized into `sex_group` |
| `DateTime` | Converted into `time_minutes` from monitoring start, then removed |
| File-based patient identity | Replaced with anonymized patient identifier |

This preserves the temporal structure needed for survival analysis while removing direct identifiers and reducing re-identification risk from exact timestamps and exact age.

## Event Definition

The outcome was defined as the **first sustained RAP value below -0.1 for 10 consecutive minutes**.

This definition was chosen to reduce sensitivity to noise, transient fluctuations, and artifact-driven crossings, ensuring that the detected event reflects persistent and clinically meaningful deterioration.

## Methods

This project applies a landmark time-to-event modeling framework:

1. Define an early observation window (3h or 6h).
2. Exclude patients with an event during or before the landmark.
3. Extract summary features from the early window.
4. Define time from landmark to event or censoring.
5. Fit and compare survival models.

### Models Used

- **Kaplan-Meier analysis**
- **Cox Proportional Hazards model**
- **Random Survival Forest**

### Features

Early physiological summaries were derived from:

- **RAP**
- **ICP**
- **CPP**
- **MAP**
- **AMP**

Example features include:

- mean and standard deviation
- minimum and maximum values
- burden above or below clinically relevant thresholds
- threshold crossing frequency
- simple trend/slope measures

## Main Findings

- Early **RAP-derived features** carried the strongest and most consistent prognostic signal.
- **Lower early RAP** was associated with **earlier severe deterioration**.
- RAP-based Kaplan-Meier grouping showed clearer separation than ICP-based grouping.
- The **3-hour landmark** provided the best overall balance of:
  - sample retention
  - post-landmark event yield
  - interpretability
  - predictive performance
- Random Survival Forest showed competitive performance, but the main biological message was consistent across both statistical and machine learning models.

## Figures

<table>
  <tr>
    <td align="center">
      <b>Kaplan-Meier: RAP groups (3h)</b><br>
      <img src="Result/km_curve_3h_rap.jpg" width="450">
    </td>
    <td align="center">
      <b>Kaplan-Meier: ICP groups (3h)</b><br>
      <img src="Result/km_curve_3h_icp.jpg" width="450">
    </td>
  </tr>
  <tr>
    <td align="center">
      <b>Kaplan-Meier: RAP groups (6h)</b><br>
      <img src="Result/km_curve_6h_rap.jpg" width="450">
    </td>
    <td align="center">
      <b>Kaplan-Meier: ICP groups (3h)</b><br>
      <img src="Result/km_curve_6h_icp.jpg" width="450">
    </td>
  </tr>
</table>

## RSF Feature Importance

<table>
  <tr>
    <td align="center">
      <b>RSF Feature Importance (3h)</b><br>
      <img src="Result/rsf_feat_imp3h_param.jpg" width="450">
    </td>
    <td align="center">
      <b>RSF Feature Importance (6h)</b><br>
      <img src="Result/rsf_feat_imp6h_param.jpg" width="450">
    </td>
  </tr>
</table>

## Why This Project Matters

This project demonstrates how survival analysis can be applied to continuous physiological monitoring data to support early risk assessment in neurocritical care. It also shows that compensatory reserve measures may provide more meaningful early prognostic information than pressure-based measures alone.

