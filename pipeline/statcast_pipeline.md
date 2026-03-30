# Solution Pipeline

This notebook documents the full end-to-end pipeline for constructing a relational MLB Statcast dataset and using it to model player-season batting average. The workflow includes raw data loading, event filtering, feature engineering, aggregation to the player-season level, relational table creation, DuckDB loading, SQL queries, and predictive modeling.

```python
!pip install duckdb
```

```python
import pandas as pd
import numpy as np
import duckdb
import os

import matplotlib.pyplot as plt
import seaborn as sns
import sys
import logging
from pathlib import Path
```

## Logging and error handling

The notebook uses Python logging to create a reproducible record of execution. Log messages are written both to the console and to a file in the `logs/` folder. This makes it easier to debug problems, document intermediate results, and demonstrate that the pipeline ran successfully from start to finish.


```python
os.makedirs("logs", exist_ok=True)
log_path = Path("logs/statcast_pipeline.log")

for handler in logging.root.handlers[:]:
    logging.root.removeHandler(handler)

logging.basicConfig(
    level=logging.INFO,
    format="%(asctime)s - %(levelname)s - %(message)s",
    handlers=[
        logging.FileHandler(log_path, mode="w", encoding="utf-8"),
        logging.StreamHandler(sys.stdout)
    ]
)

logging.info("Started DS 4320 Statcast pipeline notebook")
print(f"Logging to {log_path}")
```

## Data creation: load and combine raw Statcast files

The pipeline begins with four raw season-level Statcast CSV files covering 2018 through 2021. Only the variables needed for the project are retained in order to reduce memory usage and keep the downstream relational design focused on the refined baseball-performance question. The result of this step is one combined raw event-level table.


```python
file_list = [
    "Statcast_2018.csv",
    "Statcast_2019.csv",
    "Statcast_2020.csv",
    "Statcast_2021.csv"
]

# Columns to keep
columns_to_keep = [
    "batter",
    "player_name",
    "game_year",
    "launch_speed",
    "launch_angle",
    "hit_distance_sc",
    "bb_type",
    "events",
    "estimated_woba_using_speedangle",
    "woba_value",
    "babip_value",
    "at_bat_number",
    "pitch_number"
]

# Load and combine
df_all = []

for file in file_list:
    temp = pd.read_csv(file, usecols=columns_to_keep)
    df_all.append(temp)

df = pd.concat(df_all, ignore_index=True)

print("Combined dataset shape:", df.shape)
```

```python
logging.info("Loaded raw Statcast files: %s", file_list)
logging.info("Combined raw dataframe shape: %s", df.shape)
logging.info("Columns retained: %s", list(df.columns))
```

```python
print("New shape:", df.shape)
print("\nColumns kept:")
print(df.columns.tolist())

display(df.head())
```

## Data processing: isolate batted-ball events

Because the goal of the project is to study how contact quality relates to batting average, the full Statcast event table is filtered to batted-ball observations. Restricting the data in this way ensures that the engineered predictors are based on balls put into play rather than on all pitch outcomes.


```python
# Keep only batted ball events
df_contact = df[df["bb_type"].notna()].copy()

print("Original rows:", df.shape[0])
print("Batted ball rows:", df_contact.shape[0])

display(df_contact.head())
```

```python
logging.info("Filtered to batted-ball events: original rows=%d, batted-ball rows=%d", df.shape[0], df_contact.shape[0])
```

```python
print("\nMissing launch_speed %:", df_contact["launch_speed"].isna().mean())
print("Missing launch_angle %:", df_contact["launch_angle"].isna().mean())
```

```python
logging.info("Missing launch_speed proportion: %.6f", df_contact["launch_speed"].isna().mean())
logging.info("Missing launch_angle proportion: %.6f", df_contact["launch_angle"].isna().mean())
```

## Feature engineering: build player-season Statcast metrics

The batted-ball data are aggregated to the player-season level. This step converts event-level observations into interpretable baseball performance metrics such as average exit velocity, launch angle, hit distance, expected quality of contact, and batted-ball type rates. These variables form the main explanatory features used later in the model.

```python
player_season_statcast = (
    df_contact
    .groupby(["batter", "game_year"])
    .agg(
        balls_in_play=("bb_type", "count"),

        avg_exit_velocity=("launch_speed", "mean"),
        avg_launch_angle=("launch_angle", "mean"),
        avg_hit_distance=("hit_distance_sc", "mean"),

        max_exit_velocity=("launch_speed", "max"),

        avg_xwoba=("estimated_woba_using_speedangle", "mean"),

        ground_ball_rate=("bb_type", lambda x: (x == "ground_ball").mean()),
        line_drive_rate=("bb_type", lambda x: (x == "line_drive").mean()),
        fly_ball_rate=("bb_type", lambda x: (x == "fly_ball").mean()),
        popup_rate=("bb_type", lambda x: (x == "popup").mean()),
    )
    .reset_index()
)
```

```python
logging.info("Created player_season_statcast table with shape %s", player_season_statcast.shape)
logging.info("player_season_statcast columns: %s", list(player_season_statcast.columns))
```

```python
print("Shape of player-season statcast table:", player_season_statcast.shape)

display(player_season_statcast.head())
```

```python
print("\nSummary stats:")
print(player_season_statcast.describe())

print("\nCheck for duplicates:")
print(player_season_statcast.duplicated(subset=["batter", "game_year"]).sum())
```

```python
logging.info("Duplicate player-season keys in player_season_statcast: %d", player_season_statcast.duplicated(subset=["batter", "game_year"]).sum())
```

## Outcome construction: build player-season batting statistics

The response variable for the project is batting average. To create it, the notebook derives plate appearances, at-bats, hits, home runs, strikeouts, and walks from the event-level data and then aggregates them to the player-season level. This creates a second player-season table containing traditional batting outcomes.


```python
# Create player-season batting outcomes

# First, define helper functions
def is_hit(x):
    return x in ["single", "double", "triple", "home_run"]

def is_ab(x):
    # At-bats exclude walks, hit by pitch, etc.
    return x not in ["walk", "hit_by_pitch", "sac_bunt", "sac_fly"]

# Apply flags
df["is_hit"] = df["events"].apply(lambda x: is_hit(x) if pd.notna(x) else False)
df["is_ab"] = df["events"].apply(lambda x: is_ab(x) if pd.notna(x) else False)

# Aggregate to player-season
player_season_batting = (
    df
    .groupby(["batter", "game_year"])
    .agg(
        plate_appearances=("events", "count"),
        hits=("is_hit", "sum"),
        at_bats=("is_ab", "sum"),
        home_runs=("events", lambda x: (x == "home_run").sum()),
        strikeouts=("events", lambda x: (x == "strikeout").sum()),
        walks=("events", lambda x: (x == "walk").sum()),
    )
    .reset_index()
)

# Create batting average
player_season_batting["batting_avg"] = (
    player_season_batting["hits"] / player_season_batting["at_bats"]
)

print("Shape:", player_season_batting.shape)
display(player_season_batting.head())
```

```python
logging.info("Created player_season_batting table with shape %s", player_season_batting.shape)
logging.info("player_season_batting columns: %s", list(player_season_batting.columns))
```

```python
print("\nCheck duplicates:")
print(player_season_batting.duplicated(subset=["batter", "game_year"]).sum())

print("\nSummary:")
print(player_season_batting.describe())
```

```python
logging.info("Duplicate player-season keys in player_season_batting: %d", player_season_batting.duplicated(subset=["batter", "game_year"]).sum())
```

## Merge Statcast predictors with batting outcomes

The engineered Statcast table and the batting outcomes table are merged on the shared player-season keys. This creates a unified analytical dataset that links underlying contact-quality measures to observed batting performance.


```python
# Merge predictor and outcome tables
model_df = pd.merge(
    player_season_statcast,
    player_season_batting,
    on=["batter", "game_year"],
    how="inner"
)

print("Model dataset shape:", model_df.shape)
display(model_df.head())
```

```python
logging.info("Merged Statcast and batting tables into model_df with shape %s", model_df.shape)
```

```python
print("\nDuplicates in model dataset:")
print(model_df.duplicated(subset=["batter", "game_year"]).sum())

print("\nMissing values by column:")
print(model_df.isna().sum())

print("\nSummary of batting_avg:")
print(model_df["batting_avg"].describe())
```

```python
logging.info("Missing values in model_df by column: %s", model_df.isna().sum().to_dict())
logging.info("Summary of batting_avg in model_df: %s", model_df["batting_avg"].describe().to_dict())
```

## Apply qualification filters

The project focuses on player-seasons with enough opportunity to produce stable estimates. Filtering to hitters with at least 100 plate appearances and at least 50 balls in play reduces noise from very small samples and improves the reliability of the comparisons.


```python
# Filter to qualified hitters
model_df_filtered = model_df[
    (model_df["plate_appearances"] >= 100) &
    (model_df["balls_in_play"] >= 50)
].copy()

print("Original model size:", model_df.shape)
print("Filtered model size:", model_df_filtered.shape)

display(model_df_filtered.head())
```

```python
logging.info("Applied qualification filters: original model shape=%s, filtered shape=%s", model_df.shape, model_df_filtered.shape)
```

```python
print("\nNew batting_avg summary:")
print(model_df_filtered["batting_avg"].describe())
```

## Solution analysis: linear regression for batting average prediction

This project uses a **multiple linear regression model** to predict player-season batting average using Statcast-derived measures of contact quality and batted-ball profile.

### Analysis rationale

The goal of the project is not simply to describe batting outcomes, but to test whether advanced tracking data can help explain differences in player hitting performance. A linear regression model is appropriate here because:

- the response variable, `batting_avg`, is continuous
- the predictors are interpretable baseball performance measures
- linear regression provides a clear baseline relationship between Statcast variables and batting results
- the model is easy to interpret in terms of direction and magnitude of association

The selected predictors include average and maximum exit velocity, launch angle, hit distance, expected wOBA, and batted-ball type rates. Together, these variables capture both **quality of contact** and **shape of contact**, which are central to understanding offensive production.

The train-test split is used to evaluate out-of-sample performance. This allows the project to assess whether the model generalizes beyond the specific data used to estimate the coefficients.

```python
from sklearn.model_selection import train_test_split
from sklearn.linear_model import LinearRegression
from sklearn.metrics import mean_squared_error, r2_score

# Select features (X) and target (y)

features = [
    "avg_exit_velocity",
    "avg_launch_angle",
    "avg_hit_distance",
    "max_exit_velocity",
    "avg_xwoba",
    "ground_ball_rate",
    "line_drive_rate",
    "fly_ball_rate",
    "popup_rate"
]

X = model_df_filtered[features]
y = model_df_filtered["batting_avg"]

# Train/test split
X_train, X_test, y_train, y_test = train_test_split(
    X, y, test_size=0.2, random_state=42
)

# Train model
model = LinearRegression()
model.fit(X_train, y_train)

# Predictions
y_pred = model.predict(X_test)

# Evaluation
mse = mean_squared_error(y_test, y_pred)
r2 = r2_score(y_test, y_pred)

print("MSE:", mse)
print("R^2:", r2)
```

```python
logging.info("Selected model features: %s", features)
logging.info("Feature matrix shape: %s; target shape: %s", X.shape, y.shape)
logging.info("Train/test split complete: X_train=%s, X_test=%s, y_train=%s, y_test=%s", X_train.shape, X_test.shape, y_train.shape, y_test.shape)
logging.info("Linear regression fitted successfully")
logging.info("Model intercept: %s", model.intercept_)
logging.info("Model coefficients: %s", dict(zip(features, model.coef_)))
logging.info("Model performance: MSE=%.6f, R2=%.6f", mse, r2)
```

```python
coef_df = pd.DataFrame({
    "Feature": features,
    "Coefficient": model.coef_
}).sort_values(by="Coefficient", ascending=False)

print("\nFeature Importance (Linear Coefficients):")
display(coef_df)
```

```python
logging.info("Coefficient table created with %d rows", coef_df.shape[0])
```

## Model interpretation

The regression coefficients show how each Statcast-derived feature is associated with batting average, holding the other included variables constant. Positive coefficients indicate that higher values of a feature are associated with higher predicted batting average, while negative coefficients indicate the opposite.

The model’s **R² of approximately 0.21** means that about 21% of the variation in batting average across filtered player-seasons is explained by the Statcast variables included in the model. In a baseball setting, this is meaningful because batting average is influenced by many factors beyond batted-ball quality alone, including defensive positioning, sprint speed, strikeout tendencies, luck on balls in play, and random variation.

The **MSE of approximately 0.0012** indicates that prediction errors are relatively small in absolute terms, though not trivial. The model captures real signal, but it is not a complete explanation of batting performance.

### Limitations

This model should be interpreted with caution. First, omitted variables may matter: for example, player speed, handedness, park effects, and defensive alignment are not included here. Second, batting average is inherently noisy and sensitive to randomness, especially on balls in play. Third, linear regression imposes a linear structure that may not fully capture more complex relationships between contact quality and outcomes.

For these reasons, the model should be viewed as a useful explanatory baseline rather than a complete representation of offensive performance.

```python
plt.figure(figsize=(8, 6))

plt.scatter(y_test, y_pred, alpha=0.7)

plt.plot(
    [y_test.min(), y_test.max()],
    [y_test.min(), y_test.max()],
)

plt.xlabel("Actual Batting Average", fontsize=12)
plt.ylabel("Predicted Batting Average", fontsize=12)
plt.title("Model Performance: Actual vs Predicted Batting Average", fontsize=14)

plt.grid(True)

plt.show()
```

```python
logging.info("Generated actual-vs-predicted scatter plot for model evaluation")
```

```python
os.makedirs("visualizations", exist_ok=True)

# Build publication-style summary data
season_summary = (
    model_df_filtered.groupby("game_year", as_index=False)
    .agg(
        mean_batting_avg=("batting_avg", "mean"),
        n_players=("batter", "count")
    )
    .sort_values("game_year")
)

mean_ba = model_df_filtered["batting_avg"].mean()

plt.rcParams.update({
    "figure.dpi": 120,
    "savefig.dpi": 300,
    "font.size": 11,
    "axes.titlesize": 13,
    "axes.labelsize": 12,
    "xtick.labelsize": 11,
    "ytick.labelsize": 11
})

fig = plt.figure(figsize=(12, 9))
gs = fig.add_gridspec(2, 2, height_ratios=[1, 1.1], hspace=0.35, wspace=0.28)

ax1 = fig.add_subplot(gs[0, 0])
ax2 = fig.add_subplot(gs[0, 1])
ax3 = fig.add_subplot(gs[1, :])

# Top-left: batting average distribution
ax1.hist(model_df_filtered["batting_avg"], bins=30, edgecolor="black")
ax1.axvline(mean_ba, linestyle="--", linewidth=2)
ax1.set_title("Distribution of Player-Season Batting Average")
ax1.set_xlabel("Batting Average")
ax1.set_ylabel("Count")
ax1.text(
    mean_ba + 0.002,
    ax1.get_ylim()[1] * 0.9,
    f"Mean = {mean_ba:.3f}",
    fontsize=10
)
ax1.grid(axis="y", linestyle="--", alpha=0.3)

# Top-right: actual vs predicted
ax2.scatter(y_test, y_pred, alpha=0.7)
line_min = min(y_test.min(), y_pred.min())
line_max = max(y_test.max(), y_pred.max())
ax2.plot([line_min, line_max], [line_min, line_max], linestyle="--", linewidth=2)
ax2.set_title("Actual vs Predicted Batting Average")
ax2.set_xlabel("Actual Batting Average")
ax2.set_ylabel("Predicted Batting Average")
ax2.text(
    line_min + 0.005,
    line_max - 0.015,
    f"$R^2$ = {r2:.3f}\nMSE = {mse:.4f}",
    fontsize=10,
    bbox=dict(boxstyle="round,pad=0.3", facecolor="white", alpha=0.8)
)
ax2.grid(True, linestyle="--", alpha=0.3)

# Bottom: batting average by season
ax3.plot(
    season_summary["game_year"],
    season_summary["mean_batting_avg"],
    linewidth=2
)
ax3.fill_between(
    season_summary["game_year"],
    season_summary["mean_batting_avg"],
    alpha=0.2
)
ax3.set_title("Average Batting Average by Season")
ax3.set_xlabel("Season")
ax3.set_ylabel("Mean Batting Average")
ax3.grid(True, linestyle="--", alpha=0.3)

fig.suptitle("Figure 1: Statcast-Based Analysis of MLB Batting Performance", fontsize=16, y=0.98)

plt.savefig("visualizations/statcast_pipeline_figure.png", bbox_inches="tight")
plt.show()
```

```python
## Save Final Relational Tables
players = model_df_filtered[["batter"]].drop_duplicates().copy()
players["player_id"] = players["batter"]

players = players[["player_id"]]

print(players.shape)
display(players.head())
```

```python
logging.info("Created players dimension table with shape %s", players.shape)
```

```python
seasons = model_df_filtered[["game_year"]].drop_duplicates().copy()
seasons = seasons.rename(columns={"game_year": "season"})

print(seasons.shape)
display(seasons)
```

```python
logging.info("Created seasons dimension table with shape %s", seasons.shape)
```

```python
player_season_statcast_final = model_df_filtered[
    [
        "batter",
        "game_year",
        "balls_in_play",
        "avg_exit_velocity",
        "avg_launch_angle",
        "avg_hit_distance",
        "max_exit_velocity",
        "avg_xwoba",
        "ground_ball_rate",
        "line_drive_rate",
        "fly_ball_rate",
        "popup_rate"
    ]
].copy()
```

```python
logging.info("Created player_season_statcast_final table with shape %s", player_season_statcast_final.shape)
```

```python
player_season_batting_final = model_df_filtered[
    [
        "batter",
        "game_year",
        "plate_appearances",
        "hits",
        "at_bats",
        "home_runs",
        "strikeouts",
        "walks",
        "batting_avg"
    ]
].copy()
```

```python
logging.info("Created player_season_batting_final table with shape %s", player_season_batting_final.shape)
```

```python
import os

os.makedirs("data/final", exist_ok=True)

players.to_csv("data/final/players.csv", index=False)
seasons.to_csv("data/final/seasons.csv", index=False)
player_season_statcast_final.to_csv("data/final/player_season_statcast.csv", index=False)
player_season_batting_final.to_csv("data/final/player_season_batting.csv", index=False)

print("All tables saved!")
```

```python
logging.info("Saved relational CSV tables to data/final/")
```

## Data preparation: loading relational tables into DuckDB

To satisfy the relational-model requirement, the final cleaned dataset is stored as four related tables:

- `players`
- `seasons`
- `player_season_statcast`
- `player_season_batting`

These tables are exported as CSV files and then loaded into **DuckDB** using Python. This step demonstrates that the final dataset is not just a flat modeling table, but a relational data product that can be queried with SQL.

DuckDB is well suited to this project because it allows lightweight, local analytical SQL queries directly from Python without requiring a separate database server.

```python
## Load Data into DuckDB
import duckdb

# Create connection (in-memory)
con = duckdb.connect()

print("DuckDB connected")
```

```python
logging.info("Opened DuckDB connection")
```

## Query: preparing and inspecting the relational solution

After loading the final tables into DuckDB, SQL queries are used to verify joins and inspect the final relational structure. The join queries demonstrate that the project’s tables connect correctly on shared keys (`batter` / `player_id` and `game_year` / `season`) and that the stored data can be used to answer baseball performance questions directly from the relational database.

These queries also show how the relational model supports downstream analysis by separating player identifiers, season identifiers, Statcast performance measures, and batting outcomes into distinct but connected tables.

```python
# Load tables into DuckDB

con.execute("""
CREATE TABLE players AS
SELECT * FROM read_csv_auto('data/final/players.csv')
""")

con.execute("""
CREATE TABLE seasons AS
SELECT * FROM read_csv_auto('data/final/seasons.csv')
""")

con.execute("""
CREATE TABLE player_season_statcast AS
SELECT * FROM read_csv_auto('data/final/player_season_statcast.csv')
""")

con.execute("""
CREATE TABLE player_season_batting AS
SELECT * FROM read_csv_auto('data/final/player_season_batting.csv')
""")

print("Tables loaded into DuckDB")
```

```python
logging.info("Loaded all final CSV tables into DuckDB")
```

```python
print(con.execute("SHOW TABLES").fetchall())
```

```python
logging.info("DuckDB tables available: %s", con.execute("SHOW TABLES").fetchall())
```

```python
query = """
SELECT
    s.batter,
    s.game_year,
    s.avg_exit_velocity,
    s.avg_launch_angle,
    s.avg_xwoba,
    b.batting_avg,
    b.home_runs
FROM player_season_statcast s
JOIN player_season_batting b
ON s.batter = b.batter
AND s.game_year = b.game_year
"""

result = con.execute(query).df()

print(result.shape)
display(result.head())
```

```python
logging.info("Executed joined relational query; result shape: %s", result.shape)
```

```python
query_top = """
SELECT
    s.batter,
    s.game_year,
    s.avg_exit_velocity,
    b.batting_avg
FROM player_season_statcast s
JOIN player_season_batting b
ON s.batter = b.batter
AND s.game_year = b.game_year
ORDER BY s.avg_exit_velocity DESC
LIMIT 10
"""

top_players = con.execute(query_top).df()

display(top_players)
```

```python
logging.info("Executed top exit velocity query; result shape: %s", top_players.shape)
logging.info("Completed DS 4320 Statcast pipeline notebook successfully")
```

## Query rationale

The first SQL query joins the Statcast and batting tables to reconstruct a player-season analysis view from the relational tables. This demonstrates that the stored tables preserve the information needed for modeling and interpretation.

The second query identifies player-seasons with the highest average exit velocity, which is a useful example of how the relational database can answer substantive baseball questions beyond the regression itself. These examples show that the final data product is both analytically useful and consistent with relational design principles.

```python
import duckdb
import os

con = duckdb.connect()
data_folder = "."

for file in os.listdir(data_folder):
    if file.startswith("Statcast") and file.endswith(".csv"):
        csv_path = os.path.join(data_folder, file)
        parquet_path = os.path.join(data_folder, file.replace(".csv", ".parquet"))

        con.execute(f"""
        COPY (
            SELECT *
            FROM read_csv_auto(
                '{csv_path}',
                strict_mode = false,
                null_padding = true,
                ignore_errors = true,
                sample_size = -1
            )
        )
        TO '{parquet_path}' (FORMAT PARQUET);
        """)

        print(f"Converted {file} -> {parquet_path}")
```

## How this pipeline solves the problem

This end-to-end workflow matches the refined project goal: **use MLB Statcast data to explain and predict player batting average at the player-season level**.

- **Raw Statcast event data** from 2018–2021 are combined into a unified dataset covering millions of tracked observations.
- The pipeline filters to **batted-ball events**, which focuses the analysis on contact quality rather than all pitch outcomes.
- These event-level observations are aggregated into **player-season Statcast features**, including average exit velocity, launch angle, hit distance, expected wOBA, and batted-ball type rates.
- The same raw data are also used to construct **player-season batting outcomes**, including plate appearances, at-bats, hits, and batting average.
- These two player-season tables are merged to create a final modeling dataset linking **underlying contact characteristics** to **observed batting performance**.
- The filtered final dataset is stored as a **four-table relational database**, satisfying the project’s relational-model requirement.
- **DuckDB** is then used to load and query those tables, showing that the final data product is not only suitable for modeling, but also usable as a structured analytical database.
- A **linear regression model** is fit to predict batting average from Statcast-derived variables.
- The visualization shows that the model captures meaningful signal while also revealing the remaining unexplained variation in batting outcomes.

### What the results show

The model achieves an **R² of about 0.21** and an **MSE of about 0.0012**, indicating that Statcast contact-quality variables explain a meaningful portion of player-season batting average, even though batting performance remains partly driven by omitted variables and randomness.

### Why this solves the problem

The original problem was to move beyond traditional batting statistics and test whether deeper tracking data could improve understanding of player performance. This pipeline solves that problem by transforming raw Statcast observations into a relational analytical dataset, using that dataset to estimate a predictive model, and showing that advanced contact metrics provide measurable explanatory power for batting average.
