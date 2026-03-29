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

[UVA OneDrive Data Folder](PASTE_YOUR_ONEDRIVE_LINK_HERE)

## Pipeline

[Notebook](pipeline/notebook.ipynb)  
[Markdown Export](pipeline/notebook.md)

## License

[MIT License](LICENSE)

---

## Problem Definition

### General Problem

Projecting athletic performance.

### Specific Problem

How well can player-season Statcast contact-quality metrics from the 2018–2021 MLB seasons predict season-level batting average for qualified hitters?

### Rationale for Refinement

The original problem of projecting athletic performance is very broad, so this project narrows the scope to MLB hitters and focuses specifically on batting average as the outcome of interest. This refinement makes the problem measurable, data-driven, and appropriate for a relational data pipeline because Statcast provides detailed event-level observations that can be aggregated into player-season performance indicators. Restricting the study to qualified hitters also improves comparability across observations by reducing noise from players with very small sample sizes.

### Motivation

Batting average is one of the most familiar statistics in baseball, but it does not directly explain why some hitters perform better than others. Statcast data offers a richer view of offensive performance by measuring the quality of contact behind each batted ball. This project is motivated by the idea that underlying process-based metrics, such as exit velocity and launch angle, may reveal more about player performance than traditional box-score statistics alone. More broadly, the project demonstrates how relational data modeling and econometric analysis can be used to transform large-scale sports tracking data into interpretable evidence.

### Headline of Press Release

[Can Statcast Contact Quality Predict Batting Success?](docs/press_release.md)
