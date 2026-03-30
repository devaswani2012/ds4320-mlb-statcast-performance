# Project Data

All data files are stored externally due to large file sizes.

Access the data here: [OneDrive Data Folder](https://myuva-my.sharepoint.com/:f:/g/personal/vzu3vu_virginia_edu/IgAZpH3_mBmiQZ2ByJ-2xC--AWE9duXRoRg_MCKzn9I3wgc?e=8icNUZ)

---

## Data Files

The following files are used in this project:

| File | Rows | Size | Description |
|------|------|------|-------------|
| Statcast_2018.csv | 721,000 | 362 MB | Raw Statcast pitch-level data for 2018 season |
| Statcast_2019.csv | 730,000 | 366 MB | Raw Statcast pitch-level data for 2019 season |
| Statcast_2020.csv | 600,000 | 134 MB | Raw Statcast pitch-level data for 2020 season (shortened season) |
| Statcast_2021.csv | 770,000 | 360 MB | Raw Statcast pitch-level data for 2021 season |
| Statcast_2018.parquet | 721,000 | 63.0 MB | Compressed version of 2018 data |
| Statcast_2019.parquet | 730,000 | 63.4 MB | Compressed version of 2019 data |
| Statcast_2020.parquet | 600,000 | 23.2 MB | Compressed version of 2020 data |
| Statcast_2021.parquet | 770,000 | 62.2 MB | Compressed version of 2021 data |
| player_season_statcast.csv | 3,342 | 276 KB | Aggregated player-season Statcast metrics |
| player_season_batting.csv | 3,610 | 82.9 KB | Player-season batting outcomes |
| players.csv | 1,000 | 4.64 KB | Player ID reference table |
| seasons.csv | 4 | 27 bytes | Season identifiers (2018–2021) |

**TOTAL (raw CSVs)**: 2.8M+ rows | 1.22 GB  
**TOTAL (parquet compressed)**: 212 MB (≈82% reduction)

---

## Processed Data

The final modeling dataset is constructed by:

1. Combining all Statcast yearly datasets (2018–2021)
2. Filtering to **batted ball events only**
3. Aggregating to the **player-season level**
4. Creating advanced metrics:
   - Average exit velocity
   - Average launch angle
   - Average hit distance
   - Maximum exit velocity
   - Expected wOBA (xwOBA)
   - Batted ball type rates (ground ball, line drive, fly ball, popup)
5. Constructing batting outcomes:
   - Hits, at-bats, home runs, walks, strikeouts
   - Batting average = hits / at_bats
6. Merging Statcast metrics with batting outcomes
7. Filtering to **qualified hitters**:
   - ≥ 100 plate appearances  
   - ≥ 50 balls in play  

Final modeling dataset: **1,671 player-season observations**

---

## Data Source

Statcast data retrieved from MLB’s Statcast system via publicly available datasets.

Sources include:
- Baseball Savant (https://baseballsavant.mlb.com/)
- Public Statcast datasets (e.g., Kaggle exports)

Downloaded: March 2026

---

## Notes

- Raw CSV files are large and therefore stored in UVA OneDrive rather than GitHub
- Parquet files are included for efficient storage and faster loading
- Aggregated datasets (`player_season_*.csv`) are lightweight and included for reproducibility
- All cleaning, transformation, and modeling steps are documented in the pipeline notebook
