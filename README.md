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

[Press Release](https://github.com/devaswani2012/ds4320-mlb-statcast-performance/blob/513808ae9e92018cc5340b4adfb365516aecae93/press_release.md)

## Data
[Data Folder](https://myuva-my.sharepoint.com/:f:/g/personal/vzu3vu_virginia_edu/IgAZpH3_mBmiQZ2ByJ-2xC--AWE9duXRoRg_MCKzn9I3wgc?e=0MWQWO)

## Pipeline

[statcast_pipeline.ipynb](https://github.com/devaswani2012/ds4320-mlb-statcast-performance/blob/344da71724c9cb1a013444c63bb8bae8128e4e72/pipeline/statcast_pipeline.ipynb)  
[statcast_pipeline.md](https://github.com/devaswani2012/ds4320-mlb-statcast-performance/blob/06984a2537b7f34b36e292224ca66610e3599ec9/pipeline/statcast_pipeline.md)

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

[Unlocking the Hidden Drivers of Batting Success in Major League Baseball](https://github.com/devaswani2012/ds4320-mlb-statcast-performance/blob/513808ae9e92018cc5340b4adfb365516aecae93/press_release.md)

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
| What is Statcast? | Overview of Statcast system | [link](https://www.mlb.com/glossary/statcast) |
| Launch Angle | Explains impact of launch angle | [link](https://www.mlb.com/glossary/statcast/launch-angle) |
| Barrel Rate | Defines barrel contact | [link](https://www.mlb.com/glossary/statcast/barrel) |
| Expected Batting Average | Explains xBA metric | [link](https://www.mlb.com/glossary/statcast/expected-batting-average) |
| Using Statcast Data to Predict Hits | Example analytical study | [link](https://sabr.org/latest/petti-using-statcast-data-to-predict-hits/) |

## Data Creation

### Data Acquisition (Provenance)

The data used in this project comes from MLB Statcast, which records detailed information about every pitch and batted ball during Major League Baseball games. Raw Statcast datasets for the 2018 through 2021 seasons were collected and stored as large-scale event-level datasets. These datasets include information such as exit velocity, launch angle, hit distance, pitch characteristics, and play outcomes.

The raw data was stored in parquet format to improve storage efficiency and query performance. Parquet’s columnar structure allows for faster analytical queries and reduced file sizes compared to traditional CSV files, making it well-suited for large-scale sports tracking data.

---

### Data Processing Pipeline

The data pipeline transforms raw Statcast event-level data into a structured relational dataset suitable for analysis.

The main steps are:

1. **Filtering**  
   The raw data was filtered to include only batted ball events, ensuring that all observations represent balls put into play.

2. **Feature Engineering**  
   Advanced metrics were constructed at the event level, including:
   - Exit velocity
   - Launch angle
   - Hit distance
   - Expected weighted on-base average (xwOBA)
   - Batted ball classifications (ground ball, line drive, fly ball, popup)

3. **Aggregation**  
   Event-level data was aggregated to the player-season level, producing summary statistics such as:
   - Average exit velocity
   - Average launch angle
   - Average hit distance
   - Maximum exit velocity
   - Average xwOBA
   - Batted ball type rates

4. **Batting Statistics Construction**  
   Traditional batting statistics were calculated from the same raw dataset, including:
   - Plate appearances
   - At-bats
   - Hits
   - Walks
   - Strikeouts  
   These were used to compute the dependent variable:
   - Batting average = hits / at-bats

5. **Merging**  
   The Statcast-derived metrics were merged with batting statistics using player and season identifiers to create a unified dataset.

6. **Filtering for Qualified Players**  
   The dataset was restricted to player-seasons with:
   - At least 100 plate appearances  
   - At least 50 balls in play  
   This ensures reliable and comparable observations.

---

### Code Table

| File | Description | Link |
|---|---|---|
| statcast-pipeline.ipynb | Full data pipeline from raw data to final dataset | [statcast_pipeline.ipynb](https://github.com/devaswani2012/ds4320-mlb-statcast-performance/blob/344da71724c9cb1a013444c63bb8bae8128e4e72/pipeline/statcast_pipeline.ipynb) |
| statcast-pipeline.md | Markdown export of the pipeline notebook | [statcast_pipeline.md](https://github.com/devaswani2012/ds4320-mlb-statcast-performance/blob/06984a2537b7f34b36e292224ca66610e3599ec9/pipeline/statcast_pipeline.md) |

---

### Bias Identification

Several sources of bias may affect the dataset and analysis:

- **Selection Bias**: Restricting the dataset to qualified hitters excludes players with small sample sizes, which may systematically remove certain player types.
- **Measurement Error**: While Statcast data is highly precise, small measurement errors in tracking technology may still exist.
- **Omitted Variable Bias**: Important factors such as defensive positioning, ballpark effects, and player speed are not included in the model.
- **Survivorship Bias**: Only players who appear in the dataset for sufficient playing time are included.

---

### Bias Mitigation

To address these concerns:

- Minimum thresholds for plate appearances and balls in play were used to reduce noise.
- Multiple Statcast metrics were included to better capture underlying performance.
- The analysis is framed as predictive rather than causal to acknowledge limitations.
- Results are interpreted with awareness of omitted variables and randomness in baseball outcomes.

---

### Rationale for Key Decisions

Several important design decisions were made during the data construction process:

- **Aggregation to Player-Season Level**  
  This reduces noise from individual events and aligns with how performance is typically evaluated in baseball.

- **Use of Statcast Metrics**  
  Metrics such as exit velocity and xwOBA were selected because they capture the underlying quality of contact rather than just outcomes.

- **Use of Batting Average as the Dependent Variable**  
  Batting average is a widely understood statistic, making results easier to interpret while still allowing meaningful analysis.

- **Use of Parquet for Raw Data Storage**  
  Parquet improves efficiency when working with large datasets and supports scalable data processing.

- **Filtering Criteria**  
  Thresholds for plate appearances and balls in play ensure statistical reliability and reduce variance from small samples.

## Metadata

### Data Schema (Relational Structure)
<img width="772" height="614" alt="Screenshot 2026-03-29 at 7 32 14 PM" src="https://github.com/user-attachments/assets/faa49f6a-bdaf-4d5f-95f5-5780f06bdaa7" />


---

### Tables

| Table Name | Description | Link |
|---|---|---|
| player_season_statcast | Aggregated Statcast metrics at the player-season level | [player_season_statcast.csv](https://myuva-my.sharepoint.com/:x:/g/personal/vzu3vu_virginia_edu/IQBxefwEd1whSamrTCaKDbx7AejbJM71MKlJnS2ZEMgf894?e=3t4dbw) |
| player_season_batting | Traditional batting statistics at the player-season level | [player_season_batting.csv](https://myuva-my.sharepoint.com/:x:/g/personal/vzu3vu_virginia_edu/IQBJn-QnFF_aQa2VVpAr8aZzAaIxSQIy0Ti9HHn5poXxzE0?e=d1X2WU) |
| players | Player-level identifying information | [players.csv](https://myuva-my.sharepoint.com/:x:/g/personal/vzu3vu_virginia_edu/IQC2i8ug-5dXQ7WlFoWH3KL0AdSrtlyKN1iNNjAmoplM5yk?e=0P8CyH) |
| seasons | Season-level metadata | [seasons.csv](https://myuva-my.sharepoint.com/:x:/g/personal/vzu3vu_virginia_edu/IQCFOht94n_hSIr9fEiYFmwpARKBhu5QOWMnayySeI7P1kg?e=m17hqS) |

---

### Data Dictionary

| Feature | Type | Description | Example | Mean | Std Dev | Min | Max |
|---|---|---|---|---|---|---|---|
| avg_exit_velocity | Float | Average exit velocity of batted balls (mph) | 89.5 | 88.7 | 3.2 | 75.1 | 101.3 |
| avg_launch_angle | Float | Average launch angle (degrees) | 12.3 | 11.5 | 6.8 | -5.2 | 35.7 |
| avg_hit_distance | Float | Average distance of batted balls (feet) | 210.4 | 205.6 | 25.4 | 140.2 | 280.3 |
| max_exit_velocity | Float | Maximum exit velocity recorded (mph) | 112.7 | 110.2 | 3.5 | 95.0 | 120.1 |
| avg_xwoba | Float | Expected weighted on-base average | 0.345 | 0.320 | 0.035 | 0.250 | 0.450 |
| ground_ball_rate | Float | Proportion of ground balls | 0.42 | 0.43 | 0.08 | 0.20 | 0.65 |
| line_drive_rate | Float | Proportion of line drives | 0.21 | 0.20 | 0.05 | 0.10 | 0.35 |
| fly_ball_rate | Float | Proportion of fly balls | 0.30 | 0.29 | 0.07 | 0.15 | 0.50 |
| popup_rate | Float | Proportion of popups | 0.07 | 0.08 | 0.04 | 0.01 | 0.20 |
| plate_appearances | Integer | Total plate appearances | 540 | 350 | 180 | 100 | 720 |
| at_bats | Integer | Total at-bats | 500 | 310 | 160 | 90 | 650 |
| hits | Integer | Total hits | 140 | 75 | 35 | 20 | 200 |
| batting_avg | Float | Batting average (hits / at-bats) | 0.248 | 0.246 | 0.038 | 0.117 | 0.371 |

---

### Uncertainty Table

| Feature | Mean | Std Dev | Min | Max | Interpretation |
|---|---|---|---|---|---|
| avg_exit_velocity | 88.7 | 3.2 | 75.1 | 101.3 | Moderate spread; higher values indicate stronger contact |
| avg_launch_angle | 11.5 | 6.8 | -5.2 | 35.7 | Wide variation reflects different hitting styles |
| avg_hit_distance | 205.6 | 25.4 | 140.2 | 280.3 | Variation captures differences in power |
| max_exit_velocity | 110.2 | 3.5 | 95.0 | 120.1 | Small spread; elite hitters cluster at high values |
| avg_xwoba | 0.320 | 0.035 | 0.250 | 0.450 | Tight range; strong predictor of offensive performance |
| ground_ball_rate | 0.43 | 0.08 | 0.20 | 0.65 | Reflects differences in contact tendencies |
| line_drive_rate | 0.20 | 0.05 | 0.10 | 0.35 | Moderate variability; key for batting average |
| fly_ball_rate | 0.29 | 0.07 | 0.15 | 0.50 | Variation reflects power vs contact profiles |
| popup_rate | 0.08 | 0.04 | 0.01 | 0.20 | Smaller range; generally undesirable outcome |
| batting_avg | 0.246 | 0.038 | 0.117 | 0.371 | Limited spread but influenced by randomness |

---

### Uncertainty Quantification

Uncertainty in the dataset is reflected through the variation in key variables, as shown by their standard deviations and ranges. Metrics such as exit velocity and launch angle exhibit moderate variability across players, indicating differences in hitting profiles. Batting average shows a relatively small standard deviation, but still contains meaningful variation due to both skill and randomness.

Additionally, the relationship between Statcast metrics and batting outcomes is inherently noisy. Even well-struck balls can result in outs due to defensive positioning, while weaker contact may still produce hits. This randomness contributes to uncertainty in both the data and the model’s predictions.

---

### Statistical Interpretation

The summary statistics indicate that most hitters cluster within a relatively narrow range of performance, particularly for batting average and expected metrics such as xwOBA. However, there is meaningful variation in contact quality metrics such as exit velocity and launch angle, which suggests that these features may help explain differences in offensive performance.

The spread of the data also highlights that while some players consistently produce high-quality contact, outcomes such as batting average are influenced by additional factors beyond those captured in Statcast metrics. This reinforces the idea that the model should be interpreted as capturing partial, rather than complete, explanations of hitting performance.

## Repository Structure

---

ds4320-mlb-statcast-performance/
│── README.md
│── LICENSE
│── press_release.md
│── .gitignore
│
├── data/
│   └── README.md
│
├── docs/
│   ├── er_diagram.png
│   ├── model_performance_diagram.png
│   └── statcast_pipeline_figure.png
│
├── pipeline/
│   ├── statcast_pipeline.ipynb
│   ├── statcast_pipeline.md
│   └── statcast_pipeline.log
