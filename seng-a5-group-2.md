**SENG 637 : Dependability and Reliability of Software Systems**  
**Lab Report #5 : Software Reliability Assessment**  

| Group #: | 2 |
|----------|---|

| Sr No. | Name              | EID     |
|--------|-------------------|---------|
| 1      | Noshin Chowdhury  | 30112985|
| 2      | Salehin Kazi      | 30270206|
| 3      | Jasneet Singh     | 30044332|
| 4      | Ashwin Shanmugam  | 30300738|

# Introduction
We analyzed the provided integration-test failure dataset with two complementary reliability techniques. First, we fit reliability-growth models in **C-SFRAT** to understand how failure intensity evolves and to obtain a quantitative model for projection. Second, we applied the **RDC-11** Excel macro to judge whether the system meets target mean time to failure (MTTF) thresholds using a simple accept/reject visualization. The dataset `failure-dataset-a5.csv` (31 intervals; columns T, FC, E, F, C) was used as-is. Our process: cleanly load the data into both tools, run all hazard models in C-SFRAT with no covariates, select the best model by AIC/BIC, then expand failure times for RDC, vary candidate MTTF values, and record the minimum acceptable MTTF.

# Part 1  Reliability Growth Testing (C-SFRAT)
We ran **C-SFRAT v1.0** (Linux build) from `tools/c-sfrat/C-SFRAT/`, loading all 31 intervals with covariates set to None. All hazard functions were estimated; model comparison showed **GM (None)** as the best fit (AIC ~153.96, BIC ~156.83, SSE ~917.07) and **TL (None)** as the runner-up (AIC ~155.96, BIC ~160.27, SSE ~917.07). IFR SB and other models were clearly inferior (e.g., IFR SB AIC/BIC ~7300, SSE ~67k). The cumulative-failure plot below shows empirical failures against GM and TL predictions; GM tracks the empirical staircase closely with only a modest mid-run cluster, so we use GM for any reliability projection or mission-time estimate.

![GM vs TL cumulative](result_media/plotsgm_vs_tl_cumulative.png)

**Interpretation.** The GM model indicates steady failure intensity and moderate growth; TL behaves similarly and serves as a cross-check. No explicit mission target was provided, so we did not project mission reliability; if a mission time is later specified (e.g., 32 h to align with RDC), GM can compute reliability or expected failures. Because model fit is sensitive to the chosen hazard function, we rely on AIC/BIC to justify GM and keep TL as a secondary reference.

**Strengths and limitations.** C-SFRAT leverages the full failure history, provides quantitative fit metrics, and supports projections and what-if effort allocation. It does, however, depend on selecting an appropriate model and requires enough data; GUI exports are manual.

# Part 2  Reliability Demonstration Chart (RDC-11)
We expanded the failure times from the dataset into individual occurrence times, then normalized by candidate MTTF values in **RDC-11**. The chart uses an orange upper line (2× target) and a green lower line (0.5× target). By adjusting MTTF, we found the smallest value that keeps the normalized failure curve at or below the 2× line: **MTTF_min = 32 hours**.

## RDC Plots
- MTTF_min (32 h): ![RDC at MTTF_min = 32](result_media/rdc_mttf32.png)
- 2 MTTF_min (64 h): ![RDC at 2 MTTF_min = 64](result_media/rdc_mttf64.png)
- 0.5 MTTF_min (16 h): ![RDC at 0.5 MTTF_min = 16](result_media/rdc_mttf16.png)

**Interpretation.** At 32 h the curve just meets the 2× boundary, so the system is acceptable at the target. At 64 h it is comfortably below 2×, giving strong margin. At 16 h the curve exceeds 2×, so reliability would be insufficient at half the target MTTF. The RDC view therefore brackets an acceptable operating region at 32 h and higher, with clear failure below that.

# Comparison of Results
C-SFRAT supplies a parameterized, quantitative view: GM fits well and can project reliability at mission times. RDC supplies a rapid accept/reject visualization: acceptable near 32 h, safe at 64 h, unacceptable at 16 h. Both methods agree that the current system meets the target around the chosen MTTF; RDC makes the threshold explicit, while C-SFRAT enables forecasting and sensitivity to effort or mission time.

# Discussion  When to Use Which
- **Growth testing (C-SFRAT):** choose when you have sufficient failure history and need forecasts, model comparisons, or effort-allocation insights. It yields numeric reliability measures and supports planning.
- **RDC:** choose when failures are limited or when a fast adequacy check against a target MTTF is required; also useful for what-if scenarios (0.5/2).
- **Combined use:** start with RDC to sanity-check targets, then use C-SFRAT to project reliability, compare models, and plan testing effort.

# Team Work / Effort Division
- **Noshin:** Expanded failure times for RDC, normalized by MTTF candidates, generated RDC plots, and drafted RDC interpretation.
- **Salehin:** Ran C-SFRAT (all hazard models, covariates None), exported model_results.csv, compared AIC/BIC/SSE, captured GM/TL cumulative plot.
- **Jasneet:** Synthesized findings from both parts, wrote comparison/discussion, aligned terminology and targets.
- **Ashwin:** Integrated report sections, formatted figures/tables, verified image paths, final QA/proofreading.
- **Collaboration:** We worked equally on the report, peer-reviewed each section, and iteratively fixed wording and paths to avoid weak checks or mismatched assumptions.

# Difficulties, Challenges, Lessons Learned
- **Challenges:** choosing the right hazard model; clean expansion/normalization of failure times for RDC; keeping plot exports and paths consistent.
- **Resolutions:** used AIC/BIC to justify GM; systematic expansion + normalization for RDC; consistent naming for artifacts and images.
- **Lessons:** model choice drives conclusions; RDC complements growth models for quick adequacy; peer review prevents weak assertions and path errors; reproducibility depends on consistent exports/paths.

# Comments/Feedback on the Lab
Combining growth testing with RDC is valuable. A short worked example of failure-time expansion for RDC and a tip sheet for C-SFRAT exports would streamline the workflow and reduce setup friction.

