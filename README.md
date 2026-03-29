# DS 4320 Project 1: Statcast Contact Quality and MLB Batting Performance

## Executive Summary

This project examines whether MLB Statcast contact-quality metrics can be used to predict season-level batting performance. Using raw Statcast event data from the 2018 through 2021 seasons, I built a relational player-season dataset by filtering to batted ball events, aggregating advanced contact metrics, constructing batting statistics, and merging these components into a final modeling table. The completed dataset was analyzed using Python, DuckDB, SQL, and linear regression to evaluate how variables such as exit velocity, launch angle, hit distance, expected wOBA, and batted-ball profile relate to batting average. The final model shows that Statcast-derived quality-of-contact measures provide meaningful predictive value, while also illustrating the role of randomness and omitted contextual factors in baseball outcomes.

## Name

Dev Aswani

## NetID

vzu3vu

## DOI

[Add Zenodo DOI here]

## Press Release

[Press Release](docs/press_release.md)

## Data
[Data Folder](https://myuva-my.sharepoint.com/:f:/g/personal/vzu3vu_virginia_edu/IgAZpH3_mBmiQZ2ByJ-2xC--AWE9duXRoRg_MCKzn9I3wgc?e=0MWQWO)

## Pipeline

[statcast_pipeline.ipynb](https://github.com/devaswani2012/ds4320-mlb-statcast-performance/blob/6f7eb081d3cd1c47da2f5ad7fa037c3f03b7be4c/pipeline/statcast_pipeline.ipynb)  
[statcast_pipeline.md](https://github.com/devaswani2012/ds4320-mlb-statcast-performance/blob/5bc7aacfd6fc60da8a33fc308ccb7bfaf4aa3db5/pipeline/statcast_pipeline.md)

## License

[MIT License](https://github.com/devaswani2012/ds4320-mlb-statcast-performance/blob/314f9da6a361f0fa8740af2c01d8ee415c6d34a1/LICENSE)

---

## Problem Definition

### General Problem

Projecting athletic performance in professional baseball using player performance data.

### Specific Problem

How well can player-season Statcast contact-quality metrics from the 2018–2021 MLB seasons predict season-level batting average for qualified hitters?

### Rationale for Refinement

The original problem of projecting athletic performance is very broad, so this project narrows the scope to MLB hitters and focuses specifically on batting average as the outcome of interest. This refinement makes the problem measurable, data-driven, and appropriate for a relational data pipeline because Statcast provides detailed event-level observations that can be aggregated into player-season performance indicators. Restricting the study to qualified hitters also improves comparability across observations by reducing noise from players with very small sample sizes.

### Motivation

Batting average is one of the most familiar statistics in baseball, but it does not directly explain why some hitters perform better than others. Statcast data offers a richer view of offensive performance by measuring the quality of contact behind each batted ball. This project is motivated by the idea that underlying process-based metrics, such as exit velocity and launch angle, may reveal more about player performance than traditional box-score statistics alone. More broadly, the project demonstrates how relational data modeling and econometric analysis can be used to transform large-scale sports tracking data into interpretable evidence.

### Headline of Press Release

[Can Statcast Contact Quality Predict Batting Success?](docs/press_release.md)

## Domain Exposition

This project exists within the domain of sports analytics, specifically baseball analytics using Statcast data. Modern baseball teams rely heavily on advanced data to evaluate player performance and make strategic decisions. Systems such as Statcast track detailed information about every pitch and batted ball during MLB games, including exit velocity, launch angle, and pitch speed. These measurements allow analysts to study the underlying quality of a player’s performance rather than relying only on traditional statistics. By analyzing these advanced metrics, analysts can better understand player skill, project future performance, and identify opportunities for improvement. As a result, Statcast data has become one of the most important tools used by MLB front offices and analysts in modern baseball.

### Terminology

| Term | Definition | Why It Matters |
|---|---|---|
| Statcast | MLB’s advanced tracking system that measures player and ball movement | Provides the detailed metrics used in this project |
| Exit Velocity | Speed of the baseball off the bat | Higher values lead to better outcomes |
| Launch Angle | Vertical angle of the ball after contact | Determines type of batted ball |
| Barrel | Optimal combination of exit velocity and launch angle | Strong indicator of extra-base hits |
| Hard Hit Rate | % of balls hit ≥95 mph | Measures consistent strong contact |
| OPS | On-base plus slugging percentage | Overall offensive production |
| Slugging % | Total bases divided by at-bats | Measures power |
| Batting Average | Hits divided by at-bats | Primary dependent variable |
| Plate Appearance | A completed batting opportunity | Measures sample size |
| xBA | Expected batting average | Removes randomness |
| xSLG | Expected slugging | Measures expected power |
| WAR | Wins Above Replacement | Overall player value |
| Strikeout Rate | % of strikeouts | Measures contact ability |

### Background Reading
[Background readings folder](https://myuva-my.sharepoint.com/:f:/g/personal/vzu3vu_virginia_edu/IgAhtNlD8aZ4S5oh0qOObAnkAY_FTPoWbtgYeBYP0iyVQSM?e=2jImb0)

### Background Reading Table
| Title | Description | Link |
|---|---|---|
| What is Statcast? | Overview of Statcast system | https://www.mlb.com/glossary/statcast |
| Launch Angle | Explains impact of launch angle | https://www.mlb.com/glossary/statcast/launch-angle |
| Barrel Rate | Defines barrel contact | https://www.mlb.com/glossary/statcast/barrel |
| Expected Batting Average | Explains xBA metric | https://www.mlb.com/glossary/statcast/expected-batting-average |
| Using Statcast Data to Predict Hits | Example analytical study | https://sabr.org/latest/petti-using-statcast-data-to-predict-hits/ |
