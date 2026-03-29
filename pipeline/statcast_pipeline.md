```python
!pip install duckdb
```

    Requirement already satisfied: duckdb in /usr/local/lib/python3.12/dist-packages (1.3.2)



```python
import pandas as pd
import numpy as np
import duckdb
import os

import matplotlib.pyplot as plt
import seaborn as sns
```


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

    Combined dataset shape: (2428261, 13)



```python
print("New shape:", df.shape)
print("\nColumns kept:")
print(df.columns.tolist())

display(df.head())
```

    New shape: (2428261, 13)
    
    Columns kept:
    ['player_name', 'batter', 'events', 'bb_type', 'game_year', 'hit_distance_sc', 'launch_speed', 'launch_angle', 'estimated_woba_using_speedangle', 'woba_value', 'babip_value', 'at_bat_number', 'pitch_number']




  <div id="df-8a9a57d3-f3e2-4779-92f5-933bb49f411f" class="colab-df-container">
    <div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>player_name</th>
      <th>batter</th>
      <th>events</th>
      <th>bb_type</th>
      <th>game_year</th>
      <th>hit_distance_sc</th>
      <th>launch_speed</th>
      <th>launch_angle</th>
      <th>estimated_woba_using_speedangle</th>
      <th>woba_value</th>
      <th>babip_value</th>
      <th>at_bat_number</th>
      <th>pitch_number</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Jansen, Kenley</td>
      <td>467827</td>
      <td>strikeout</td>
      <td>NaN</td>
      <td>2018</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>71</td>
      <td>4</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Jansen, Kenley</td>
      <td>467827</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>2018</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>71</td>
      <td>3</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Jansen, Kenley</td>
      <td>467827</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>2018</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>71</td>
      <td>2</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Jansen, Kenley</td>
      <td>467827</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>2018</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>71</td>
      <td>1</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Jansen, Kenley</td>
      <td>435622</td>
      <td>strikeout</td>
      <td>NaN</td>
      <td>2018</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>70</td>
      <td>6</td>
    </tr>
  </tbody>
</table>
</div>
    <div class="colab-df-buttons">

  <div class="colab-df-container">
    <button class="colab-df-convert" onclick="convertToInteractive('df-8a9a57d3-f3e2-4779-92f5-933bb49f411f')"
            title="Convert this dataframe to an interactive table."
            style="display:none;">

  <svg xmlns="http://www.w3.org/2000/svg" height="24px" viewBox="0 -960 960 960">
    <path d="M120-120v-720h720v720H120Zm60-500h600v-160H180v160Zm220 220h160v-160H400v160Zm0 220h160v-160H400v160ZM180-400h160v-160H180v160Zm440 0h160v-160H620v160ZM180-180h160v-160H180v160Zm440 0h160v-160H620v160Z"/>
  </svg>
    </button>

  <style>
    .colab-df-container {
      display:flex;
      gap: 12px;
    }

    .colab-df-convert {
      background-color: #E8F0FE;
      border: none;
      border-radius: 50%;
      cursor: pointer;
      display: none;
      fill: #1967D2;
      height: 32px;
      padding: 0 0 0 0;
      width: 32px;
    }

    .colab-df-convert:hover {
      background-color: #E2EBFA;
      box-shadow: 0px 1px 2px rgba(60, 64, 67, 0.3), 0px 1px 3px 1px rgba(60, 64, 67, 0.15);
      fill: #174EA6;
    }

    .colab-df-buttons div {
      margin-bottom: 4px;
    }

    [theme=dark] .colab-df-convert {
      background-color: #3B4455;
      fill: #D2E3FC;
    }

    [theme=dark] .colab-df-convert:hover {
      background-color: #434B5C;
      box-shadow: 0px 1px 3px 1px rgba(0, 0, 0, 0.15);
      filter: drop-shadow(0px 1px 2px rgba(0, 0, 0, 0.3));
      fill: #FFFFFF;
    }
  </style>

    <script>
      const buttonEl =
        document.querySelector('#df-8a9a57d3-f3e2-4779-92f5-933bb49f411f button.colab-df-convert');
      buttonEl.style.display =
        google.colab.kernel.accessAllowed ? 'block' : 'none';

      async function convertToInteractive(key) {
        const element = document.querySelector('#df-8a9a57d3-f3e2-4779-92f5-933bb49f411f');
        const dataTable =
          await google.colab.kernel.invokeFunction('convertToInteractive',
                                                    [key], {});
        if (!dataTable) return;

        const docLinkHtml = 'Like what you see? Visit the ' +
          '<a target="_blank" href=https://colab.research.google.com/notebooks/data_table.ipynb>data table notebook</a>'
          + ' to learn more about interactive tables.';
        element.innerHTML = '';
        dataTable['output_type'] = 'display_data';
        await google.colab.output.renderOutput(dataTable, element);
        const docLink = document.createElement('div');
        docLink.innerHTML = docLinkHtml;
        element.appendChild(docLink);
      }
    </script>
  </div>


    </div>
  </div>




```python
# Keep only batted ball events
df_contact = df[df["bb_type"].notna()].copy()

print("Original rows:", df.shape[0])
print("Batted ball rows:", df_contact.shape[0])

display(df_contact.head())
```

    Original rows: 2428261
    Batted ball rows: 417902




  <div id="df-0d1447e2-a0b5-4451-b193-46be483e321f" class="colab-df-container">
    <div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>player_name</th>
      <th>batter</th>
      <th>events</th>
      <th>bb_type</th>
      <th>game_year</th>
      <th>hit_distance_sc</th>
      <th>launch_speed</th>
      <th>launch_angle</th>
      <th>estimated_woba_using_speedangle</th>
      <th>woba_value</th>
      <th>babip_value</th>
      <th>at_bat_number</th>
      <th>pitch_number</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>10</th>
      <td>Jansen, Kenley</td>
      <td>471865</td>
      <td>field_out</td>
      <td>ground_ball</td>
      <td>2018</td>
      <td>4.0</td>
      <td>79.9</td>
      <td>-33.0</td>
      <td>0.081</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>69</td>
      <td>1</td>
    </tr>
    <tr>
      <th>11</th>
      <td>Jansen, Kenley</td>
      <td>596115</td>
      <td>home_run</td>
      <td>fly_ball</td>
      <td>2018</td>
      <td>409.0</td>
      <td>104.7</td>
      <td>24.0</td>
      <td>1.737</td>
      <td>2.0</td>
      <td>0.0</td>
      <td>68</td>
      <td>9</td>
    </tr>
    <tr>
      <th>20</th>
      <td>Jansen, Kenley</td>
      <td>571448</td>
      <td>home_run</td>
      <td>fly_ball</td>
      <td>2018</td>
      <td>414.0</td>
      <td>107.1</td>
      <td>23.0</td>
      <td>1.780</td>
      <td>2.0</td>
      <td>0.0</td>
      <td>67</td>
      <td>1</td>
    </tr>
    <tr>
      <th>21</th>
      <td>Johnson, DJ</td>
      <td>571771</td>
      <td>field_out</td>
      <td>ground_ball</td>
      <td>2018</td>
      <td>10.0</td>
      <td>81.0</td>
      <td>-11.0</td>
      <td>0.072</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>66</td>
      <td>1</td>
    </tr>
    <tr>
      <th>22</th>
      <td>McGee, Jake</td>
      <td>624577</td>
      <td>field_out</td>
      <td>ground_ball</td>
      <td>2018</td>
      <td>8.0</td>
      <td>65.7</td>
      <td>-16.0</td>
      <td>0.070</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>65</td>
      <td>2</td>
    </tr>
  </tbody>
</table>
</div>
    <div class="colab-df-buttons">

  <div class="colab-df-container">
    <button class="colab-df-convert" onclick="convertToInteractive('df-0d1447e2-a0b5-4451-b193-46be483e321f')"
            title="Convert this dataframe to an interactive table."
            style="display:none;">

  <svg xmlns="http://www.w3.org/2000/svg" height="24px" viewBox="0 -960 960 960">
    <path d="M120-120v-720h720v720H120Zm60-500h600v-160H180v160Zm220 220h160v-160H400v160Zm0 220h160v-160H400v160ZM180-400h160v-160H180v160Zm440 0h160v-160H620v160ZM180-180h160v-160H180v160Zm440 0h160v-160H620v160Z"/>
  </svg>
    </button>

  <style>
    .colab-df-container {
      display:flex;
      gap: 12px;
    }

    .colab-df-convert {
      background-color: #E8F0FE;
      border: none;
      border-radius: 50%;
      cursor: pointer;
      display: none;
      fill: #1967D2;
      height: 32px;
      padding: 0 0 0 0;
      width: 32px;
    }

    .colab-df-convert:hover {
      background-color: #E2EBFA;
      box-shadow: 0px 1px 2px rgba(60, 64, 67, 0.3), 0px 1px 3px 1px rgba(60, 64, 67, 0.15);
      fill: #174EA6;
    }

    .colab-df-buttons div {
      margin-bottom: 4px;
    }

    [theme=dark] .colab-df-convert {
      background-color: #3B4455;
      fill: #D2E3FC;
    }

    [theme=dark] .colab-df-convert:hover {
      background-color: #434B5C;
      box-shadow: 0px 1px 3px 1px rgba(0, 0, 0, 0.15);
      filter: drop-shadow(0px 1px 2px rgba(0, 0, 0, 0.3));
      fill: #FFFFFF;
    }
  </style>

    <script>
      const buttonEl =
        document.querySelector('#df-0d1447e2-a0b5-4451-b193-46be483e321f button.colab-df-convert');
      buttonEl.style.display =
        google.colab.kernel.accessAllowed ? 'block' : 'none';

      async function convertToInteractive(key) {
        const element = document.querySelector('#df-0d1447e2-a0b5-4451-b193-46be483e321f');
        const dataTable =
          await google.colab.kernel.invokeFunction('convertToInteractive',
                                                    [key], {});
        if (!dataTable) return;

        const docLinkHtml = 'Like what you see? Visit the ' +
          '<a target="_blank" href=https://colab.research.google.com/notebooks/data_table.ipynb>data table notebook</a>'
          + ' to learn more about interactive tables.';
        element.innerHTML = '';
        dataTable['output_type'] = 'display_data';
        await google.colab.output.renderOutput(dataTable, element);
        const docLink = document.createElement('div');
        docLink.innerHTML = docLinkHtml;
        element.appendChild(docLink);
      }
    </script>
  </div>


    </div>
  </div>




```python
print("\nMissing launch_speed %:", df_contact["launch_speed"].isna().mean())
print("Missing launch_angle %:", df_contact["launch_angle"].isna().mean())
```

    
    Missing launch_speed %: 0.01183770357643658
    Missing launch_angle %: 0.01201238567893908



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
print("Shape of player-season statcast table:", player_season_statcast.shape)

display(player_season_statcast.head())
```

    Shape of player-season statcast table: (3342, 12)




  <div id="df-de25af9f-229f-4468-ae79-d65c1dcf1e0d" class="colab-df-container">
    <div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>batter</th>
      <th>game_year</th>
      <th>balls_in_play</th>
      <th>avg_exit_velocity</th>
      <th>avg_launch_angle</th>
      <th>avg_hit_distance</th>
      <th>max_exit_velocity</th>
      <th>avg_xwoba</th>
      <th>ground_ball_rate</th>
      <th>line_drive_rate</th>
      <th>fly_ball_rate</th>
      <th>popup_rate</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>112526</td>
      <td>2018</td>
      <td>1</td>
      <td>76.900000</td>
      <td>-30.000000</td>
      <td>4.000000</td>
      <td>76.9</td>
      <td>0.081000</td>
      <td>1.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>1</th>
      <td>134181</td>
      <td>2018</td>
      <td>345</td>
      <td>88.697674</td>
      <td>13.633721</td>
      <td>187.429907</td>
      <td>109.2</td>
      <td>0.382826</td>
      <td>0.391304</td>
      <td>0.301449</td>
      <td>0.255072</td>
      <td>0.052174</td>
    </tr>
    <tr>
      <th>2</th>
      <td>282332</td>
      <td>2019</td>
      <td>1</td>
      <td>96.800000</td>
      <td>17.000000</td>
      <td>297.000000</td>
      <td>96.8</td>
      <td>0.476000</td>
      <td>0.000000</td>
      <td>1.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>3</th>
      <td>400085</td>
      <td>2018</td>
      <td>37</td>
      <td>81.311111</td>
      <td>5.722222</td>
      <td>125.571429</td>
      <td>100.1</td>
      <td>0.219333</td>
      <td>0.567568</td>
      <td>0.135135</td>
      <td>0.216216</td>
      <td>0.081081</td>
    </tr>
    <tr>
      <th>4</th>
      <td>400085</td>
      <td>2019</td>
      <td>4</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>0.500000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.500000</td>
    </tr>
  </tbody>
</table>
</div>
    <div class="colab-df-buttons">

  <div class="colab-df-container">
    <button class="colab-df-convert" onclick="convertToInteractive('df-de25af9f-229f-4468-ae79-d65c1dcf1e0d')"
            title="Convert this dataframe to an interactive table."
            style="display:none;">

  <svg xmlns="http://www.w3.org/2000/svg" height="24px" viewBox="0 -960 960 960">
    <path d="M120-120v-720h720v720H120Zm60-500h600v-160H180v160Zm220 220h160v-160H400v160Zm0 220h160v-160H400v160ZM180-400h160v-160H180v160Zm440 0h160v-160H620v160ZM180-180h160v-160H180v160Zm440 0h160v-160H620v160Z"/>
  </svg>
    </button>

  <style>
    .colab-df-container {
      display:flex;
      gap: 12px;
    }

    .colab-df-convert {
      background-color: #E8F0FE;
      border: none;
      border-radius: 50%;
      cursor: pointer;
      display: none;
      fill: #1967D2;
      height: 32px;
      padding: 0 0 0 0;
      width: 32px;
    }

    .colab-df-convert:hover {
      background-color: #E2EBFA;
      box-shadow: 0px 1px 2px rgba(60, 64, 67, 0.3), 0px 1px 3px 1px rgba(60, 64, 67, 0.15);
      fill: #174EA6;
    }

    .colab-df-buttons div {
      margin-bottom: 4px;
    }

    [theme=dark] .colab-df-convert {
      background-color: #3B4455;
      fill: #D2E3FC;
    }

    [theme=dark] .colab-df-convert:hover {
      background-color: #434B5C;
      box-shadow: 0px 1px 3px 1px rgba(0, 0, 0, 0.15);
      filter: drop-shadow(0px 1px 2px rgba(0, 0, 0, 0.3));
      fill: #FFFFFF;
    }
  </style>

    <script>
      const buttonEl =
        document.querySelector('#df-de25af9f-229f-4468-ae79-d65c1dcf1e0d button.colab-df-convert');
      buttonEl.style.display =
        google.colab.kernel.accessAllowed ? 'block' : 'none';

      async function convertToInteractive(key) {
        const element = document.querySelector('#df-de25af9f-229f-4468-ae79-d65c1dcf1e0d');
        const dataTable =
          await google.colab.kernel.invokeFunction('convertToInteractive',
                                                    [key], {});
        if (!dataTable) return;

        const docLinkHtml = 'Like what you see? Visit the ' +
          '<a target="_blank" href=https://colab.research.google.com/notebooks/data_table.ipynb>data table notebook</a>'
          + ' to learn more about interactive tables.';
        element.innerHTML = '';
        dataTable['output_type'] = 'display_data';
        await google.colab.output.renderOutput(dataTable, element);
        const docLink = document.createElement('div');
        docLink.innerHTML = docLinkHtml;
        element.appendChild(docLink);
      }
    </script>
  </div>


    </div>
  </div>




```python
print("\nSummary stats:")
print(player_season_statcast.describe())

print("\nCheck for duplicates:")
print(player_season_statcast.duplicated(subset=["batter", "game_year"]).sum())
```

    
    Summary stats:
                  batter    game_year  balls_in_play  avg_exit_velocity  \
    count    3342.000000  3342.000000    3342.000000        3312.000000   
    mean   584344.368043  2019.465290     125.045482          85.097785   
    std     68886.565717     1.166154     142.042078           8.927438   
    min    112526.000000  2018.000000       1.000000          15.800000   
    25%    543308.000000  2018.000000      10.000000          83.757500   
    50%    599096.000000  2019.000000      64.000000          87.219368   
    75%    641791.250000  2021.000000     196.000000          89.550629   
    max    685503.000000  2021.000000     573.000000         109.600000   
    
           avg_launch_angle  avg_hit_distance  max_exit_velocity    avg_xwoba  \
    count       3312.000000       3299.000000        3312.000000  3312.000000   
    mean           7.516467        146.057254         103.706884     0.325824   
    std           15.728723         58.299768          12.255676     0.120515   
    min          -82.000000          0.000000          15.800000     0.000000   
    25%            4.432143        129.063444         101.600000     0.269196   
    50%           10.960507        159.716418         107.200000     0.335477   
    75%           15.500828        180.120114         110.400000     0.393290   
    max           77.000000        416.000000         122.200000     1.547000   
    
           ground_ball_rate  line_drive_rate  fly_ball_rate   popup_rate  
    count       3342.000000      3342.000000    3342.000000  3342.000000  
    mean           0.516517         0.221324       0.193063     0.069096  
    std            0.222040         0.144125       0.136881     0.092187  
    min            0.000000         0.000000       0.000000     0.000000  
    25%            0.393984         0.166667       0.111111     0.000000  
    50%            0.468716         0.235849       0.208674     0.058824  
    75%            0.575000         0.277778       0.266667     0.091776  
    max            1.000000         1.000000       1.000000     1.000000  
    
    Check for duplicates:
    0



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

    Shape: (3610, 9)




  <div id="df-ee899d84-5a27-40a5-aff5-52cb47233ca7" class="colab-df-container">
    <div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>batter</th>
      <th>game_year</th>
      <th>plate_appearances</th>
      <th>hits</th>
      <th>at_bats</th>
      <th>home_runs</th>
      <th>strikeouts</th>
      <th>walks</th>
      <th>batting_avg</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>112526</td>
      <td>2018</td>
      <td>4</td>
      <td>0</td>
      <td>4</td>
      <td>0</td>
      <td>3</td>
      <td>0</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>1</th>
      <td>134181</td>
      <td>2018</td>
      <td>479</td>
      <td>118</td>
      <td>434</td>
      <td>15</td>
      <td>95</td>
      <td>32</td>
      <td>0.271889</td>
    </tr>
    <tr>
      <th>2</th>
      <td>282332</td>
      <td>2019</td>
      <td>3</td>
      <td>0</td>
      <td>3</td>
      <td>0</td>
      <td>2</td>
      <td>0</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>3</th>
      <td>400085</td>
      <td>2018</td>
      <td>47</td>
      <td>9</td>
      <td>44</td>
      <td>0</td>
      <td>7</td>
      <td>3</td>
      <td>0.204545</td>
    </tr>
    <tr>
      <th>4</th>
      <td>400085</td>
      <td>2019</td>
      <td>6</td>
      <td>0</td>
      <td>5</td>
      <td>0</td>
      <td>1</td>
      <td>1</td>
      <td>0.000000</td>
    </tr>
  </tbody>
</table>
</div>
    <div class="colab-df-buttons">

  <div class="colab-df-container">
    <button class="colab-df-convert" onclick="convertToInteractive('df-ee899d84-5a27-40a5-aff5-52cb47233ca7')"
            title="Convert this dataframe to an interactive table."
            style="display:none;">

  <svg xmlns="http://www.w3.org/2000/svg" height="24px" viewBox="0 -960 960 960">
    <path d="M120-120v-720h720v720H120Zm60-500h600v-160H180v160Zm220 220h160v-160H400v160Zm0 220h160v-160H400v160ZM180-400h160v-160H180v160Zm440 0h160v-160H620v160ZM180-180h160v-160H180v160Zm440 0h160v-160H620v160Z"/>
  </svg>
    </button>

  <style>
    .colab-df-container {
      display:flex;
      gap: 12px;
    }

    .colab-df-convert {
      background-color: #E8F0FE;
      border: none;
      border-radius: 50%;
      cursor: pointer;
      display: none;
      fill: #1967D2;
      height: 32px;
      padding: 0 0 0 0;
      width: 32px;
    }

    .colab-df-convert:hover {
      background-color: #E2EBFA;
      box-shadow: 0px 1px 2px rgba(60, 64, 67, 0.3), 0px 1px 3px 1px rgba(60, 64, 67, 0.15);
      fill: #174EA6;
    }

    .colab-df-buttons div {
      margin-bottom: 4px;
    }

    [theme=dark] .colab-df-convert {
      background-color: #3B4455;
      fill: #D2E3FC;
    }

    [theme=dark] .colab-df-convert:hover {
      background-color: #434B5C;
      box-shadow: 0px 1px 3px 1px rgba(0, 0, 0, 0.15);
      filter: drop-shadow(0px 1px 2px rgba(0, 0, 0, 0.3));
      fill: #FFFFFF;
    }
  </style>

    <script>
      const buttonEl =
        document.querySelector('#df-ee899d84-5a27-40a5-aff5-52cb47233ca7 button.colab-df-convert');
      buttonEl.style.display =
        google.colab.kernel.accessAllowed ? 'block' : 'none';

      async function convertToInteractive(key) {
        const element = document.querySelector('#df-ee899d84-5a27-40a5-aff5-52cb47233ca7');
        const dataTable =
          await google.colab.kernel.invokeFunction('convertToInteractive',
                                                    [key], {});
        if (!dataTable) return;

        const docLinkHtml = 'Like what you see? Visit the ' +
          '<a target="_blank" href=https://colab.research.google.com/notebooks/data_table.ipynb>data table notebook</a>'
          + ' to learn more about interactive tables.';
        element.innerHTML = '';
        dataTable['output_type'] = 'display_data';
        await google.colab.output.renderOutput(dataTable, element);
        const docLink = document.createElement('div');
        docLink.innerHTML = docLinkHtml;
        element.appendChild(docLink);
      }
    </script>
  </div>


    </div>
  </div>




```python
print("\nCheck duplicates:")
print(player_season_batting.duplicated(subset=["batter", "game_year"]).sum())

print("\nSummary:")
print(player_season_batting.describe())
```

    
    Check duplicates:
    0
    
    Summary:
                  batter    game_year  plate_appearances         hits  \
    count    3610.000000  3610.000000         3610.00000  3610.000000   
    mean   585604.224654  2019.467867          171.34072    37.960111   
    std     68567.811090     1.174304          199.86953    48.295239   
    min    112526.000000  2018.000000            1.00000     0.000000   
    25%    543380.500000  2018.000000            9.00000     1.000000   
    50%    601713.000000  2019.000000           76.00000    14.000000   
    75%    641899.750000  2021.000000          265.00000    59.000000   
    max    685503.000000  2021.000000          745.00000   206.000000   
    
               at_bats    home_runs   strikeouts        walks  batting_avg  
    count  3610.000000  3610.000000  3610.000000  3610.000000  3583.000000  
    mean    153.580332     5.711357    39.159557    14.099446     0.185366  
    std     179.040017     8.785130    43.386035    19.149145     0.127046  
    min       0.000000     0.000000     0.000000     0.000000     0.000000  
    25%       8.000000     0.000000     4.000000     0.000000     0.100000  
    50%      69.000000     1.000000    23.000000     5.000000     0.214286  
    75%     238.000000     8.000000    62.000000    22.000000     0.258520  
    max     681.000000    53.000000   216.000000   122.000000     1.000000  



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

    Model dataset shape: (3342, 19)




  <div id="df-eecbf5b1-4c23-4ff2-bf7e-4da86a861021" class="colab-df-container">
    <div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>batter</th>
      <th>game_year</th>
      <th>balls_in_play</th>
      <th>avg_exit_velocity</th>
      <th>avg_launch_angle</th>
      <th>avg_hit_distance</th>
      <th>max_exit_velocity</th>
      <th>avg_xwoba</th>
      <th>ground_ball_rate</th>
      <th>line_drive_rate</th>
      <th>fly_ball_rate</th>
      <th>popup_rate</th>
      <th>plate_appearances</th>
      <th>hits</th>
      <th>at_bats</th>
      <th>home_runs</th>
      <th>strikeouts</th>
      <th>walks</th>
      <th>batting_avg</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>112526</td>
      <td>2018</td>
      <td>1</td>
      <td>76.900000</td>
      <td>-30.000000</td>
      <td>4.000000</td>
      <td>76.9</td>
      <td>0.081000</td>
      <td>1.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>4</td>
      <td>0</td>
      <td>4</td>
      <td>0</td>
      <td>3</td>
      <td>0</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>1</th>
      <td>134181</td>
      <td>2018</td>
      <td>345</td>
      <td>88.697674</td>
      <td>13.633721</td>
      <td>187.429907</td>
      <td>109.2</td>
      <td>0.382826</td>
      <td>0.391304</td>
      <td>0.301449</td>
      <td>0.255072</td>
      <td>0.052174</td>
      <td>479</td>
      <td>118</td>
      <td>434</td>
      <td>15</td>
      <td>95</td>
      <td>32</td>
      <td>0.271889</td>
    </tr>
    <tr>
      <th>2</th>
      <td>282332</td>
      <td>2019</td>
      <td>1</td>
      <td>96.800000</td>
      <td>17.000000</td>
      <td>297.000000</td>
      <td>96.8</td>
      <td>0.476000</td>
      <td>0.000000</td>
      <td>1.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>3</td>
      <td>0</td>
      <td>3</td>
      <td>0</td>
      <td>2</td>
      <td>0</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>3</th>
      <td>400085</td>
      <td>2018</td>
      <td>37</td>
      <td>81.311111</td>
      <td>5.722222</td>
      <td>125.571429</td>
      <td>100.1</td>
      <td>0.219333</td>
      <td>0.567568</td>
      <td>0.135135</td>
      <td>0.216216</td>
      <td>0.081081</td>
      <td>47</td>
      <td>9</td>
      <td>44</td>
      <td>0</td>
      <td>7</td>
      <td>3</td>
      <td>0.204545</td>
    </tr>
    <tr>
      <th>4</th>
      <td>400085</td>
      <td>2019</td>
      <td>4</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>0.500000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.500000</td>
      <td>6</td>
      <td>0</td>
      <td>5</td>
      <td>0</td>
      <td>1</td>
      <td>1</td>
      <td>0.000000</td>
    </tr>
  </tbody>
</table>
</div>
    <div class="colab-df-buttons">

  <div class="colab-df-container">
    <button class="colab-df-convert" onclick="convertToInteractive('df-eecbf5b1-4c23-4ff2-bf7e-4da86a861021')"
            title="Convert this dataframe to an interactive table."
            style="display:none;">

  <svg xmlns="http://www.w3.org/2000/svg" height="24px" viewBox="0 -960 960 960">
    <path d="M120-120v-720h720v720H120Zm60-500h600v-160H180v160Zm220 220h160v-160H400v160Zm0 220h160v-160H400v160ZM180-400h160v-160H180v160Zm440 0h160v-160H620v160ZM180-180h160v-160H180v160Zm440 0h160v-160H620v160Z"/>
  </svg>
    </button>

  <style>
    .colab-df-container {
      display:flex;
      gap: 12px;
    }

    .colab-df-convert {
      background-color: #E8F0FE;
      border: none;
      border-radius: 50%;
      cursor: pointer;
      display: none;
      fill: #1967D2;
      height: 32px;
      padding: 0 0 0 0;
      width: 32px;
    }

    .colab-df-convert:hover {
      background-color: #E2EBFA;
      box-shadow: 0px 1px 2px rgba(60, 64, 67, 0.3), 0px 1px 3px 1px rgba(60, 64, 67, 0.15);
      fill: #174EA6;
    }

    .colab-df-buttons div {
      margin-bottom: 4px;
    }

    [theme=dark] .colab-df-convert {
      background-color: #3B4455;
      fill: #D2E3FC;
    }

    [theme=dark] .colab-df-convert:hover {
      background-color: #434B5C;
      box-shadow: 0px 1px 3px 1px rgba(0, 0, 0, 0.15);
      filter: drop-shadow(0px 1px 2px rgba(0, 0, 0, 0.3));
      fill: #FFFFFF;
    }
  </style>

    <script>
      const buttonEl =
        document.querySelector('#df-eecbf5b1-4c23-4ff2-bf7e-4da86a861021 button.colab-df-convert');
      buttonEl.style.display =
        google.colab.kernel.accessAllowed ? 'block' : 'none';

      async function convertToInteractive(key) {
        const element = document.querySelector('#df-eecbf5b1-4c23-4ff2-bf7e-4da86a861021');
        const dataTable =
          await google.colab.kernel.invokeFunction('convertToInteractive',
                                                    [key], {});
        if (!dataTable) return;

        const docLinkHtml = 'Like what you see? Visit the ' +
          '<a target="_blank" href=https://colab.research.google.com/notebooks/data_table.ipynb>data table notebook</a>'
          + ' to learn more about interactive tables.';
        element.innerHTML = '';
        dataTable['output_type'] = 'display_data';
        await google.colab.output.renderOutput(dataTable, element);
        const docLink = document.createElement('div');
        docLink.innerHTML = docLinkHtml;
        element.appendChild(docLink);
      }
    </script>
  </div>


    </div>
  </div>




```python
print("\nDuplicates in model dataset:")
print(model_df.duplicated(subset=["batter", "game_year"]).sum())

print("\nMissing values by column:")
print(model_df.isna().sum())

print("\nSummary of batting_avg:")
print(model_df["batting_avg"].describe())
```

    
    Duplicates in model dataset:
    0
    
    Missing values by column:
    batter                0
    game_year             0
    balls_in_play         0
    avg_exit_velocity    30
    avg_launch_angle     30
    avg_hit_distance     43
    max_exit_velocity    30
    avg_xwoba            30
    ground_ball_rate      0
    line_drive_rate       0
    fly_ball_rate         0
    popup_rate            0
    plate_appearances     0
    hits                  0
    at_bats               0
    home_runs             0
    strikeouts            0
    walks                 0
    batting_avg          18
    dtype: int64
    
    Summary of batting_avg:
    count    3324.000000
    mean        0.199810
    std         0.120465
    min         0.000000
    25%         0.145323
    50%         0.222222
    75%         0.261545
    max         1.000000
    Name: batting_avg, dtype: float64



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

    Original model size: (3342, 19)
    Filtered model size: (1671, 19)




  <div id="df-f725b94d-fdaf-4b48-828b-7f19fe292352" class="colab-df-container">
    <div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>batter</th>
      <th>game_year</th>
      <th>balls_in_play</th>
      <th>avg_exit_velocity</th>
      <th>avg_launch_angle</th>
      <th>avg_hit_distance</th>
      <th>max_exit_velocity</th>
      <th>avg_xwoba</th>
      <th>ground_ball_rate</th>
      <th>line_drive_rate</th>
      <th>fly_ball_rate</th>
      <th>popup_rate</th>
      <th>plate_appearances</th>
      <th>hits</th>
      <th>at_bats</th>
      <th>home_runs</th>
      <th>strikeouts</th>
      <th>walks</th>
      <th>batting_avg</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>1</th>
      <td>134181</td>
      <td>2018</td>
      <td>345</td>
      <td>88.697674</td>
      <td>13.633721</td>
      <td>187.429907</td>
      <td>109.2</td>
      <td>0.382826</td>
      <td>0.391304</td>
      <td>0.301449</td>
      <td>0.255072</td>
      <td>0.052174</td>
      <td>479</td>
      <td>118</td>
      <td>434</td>
      <td>15</td>
      <td>95</td>
      <td>32</td>
      <td>0.271889</td>
    </tr>
    <tr>
      <th>5</th>
      <td>400121</td>
      <td>2018</td>
      <td>425</td>
      <td>87.743632</td>
      <td>13.981132</td>
      <td>180.596354</td>
      <td>107.6</td>
      <td>0.333219</td>
      <td>0.416471</td>
      <td>0.265882</td>
      <td>0.249412</td>
      <td>0.068235</td>
      <td>506</td>
      <td>117</td>
      <td>469</td>
      <td>9</td>
      <td>47</td>
      <td>28</td>
      <td>0.249467</td>
    </tr>
    <tr>
      <th>6</th>
      <td>400284</td>
      <td>2018</td>
      <td>131</td>
      <td>87.292857</td>
      <td>16.547619</td>
      <td>177.906780</td>
      <td>106.4</td>
      <td>0.303603</td>
      <td>0.427481</td>
      <td>0.244275</td>
      <td>0.274809</td>
      <td>0.053435</td>
      <td>185</td>
      <td>35</td>
      <td>164</td>
      <td>1</td>
      <td>34</td>
      <td>15</td>
      <td>0.213415</td>
    </tr>
    <tr>
      <th>7</th>
      <td>405395</td>
      <td>2018</td>
      <td>403</td>
      <td>90.028288</td>
      <td>14.148883</td>
      <td>178.812155</td>
      <td>112.1</td>
      <td>0.365859</td>
      <td>0.409429</td>
      <td>0.263027</td>
      <td>0.225806</td>
      <td>0.101737</td>
      <td>495</td>
      <td>114</td>
      <td>465</td>
      <td>19</td>
      <td>65</td>
      <td>25</td>
      <td>0.245161</td>
    </tr>
    <tr>
      <th>8</th>
      <td>405395</td>
      <td>2019</td>
      <td>431</td>
      <td>88.294340</td>
      <td>12.547170</td>
      <td>166.576227</td>
      <td>112.3</td>
      <td>0.341953</td>
      <td>0.461717</td>
      <td>0.169374</td>
      <td>0.266821</td>
      <td>0.102088</td>
      <td>544</td>
      <td>120</td>
      <td>491</td>
      <td>23</td>
      <td>68</td>
      <td>42</td>
      <td>0.244399</td>
    </tr>
  </tbody>
</table>
</div>
    <div class="colab-df-buttons">

  <div class="colab-df-container">
    <button class="colab-df-convert" onclick="convertToInteractive('df-f725b94d-fdaf-4b48-828b-7f19fe292352')"
            title="Convert this dataframe to an interactive table."
            style="display:none;">

  <svg xmlns="http://www.w3.org/2000/svg" height="24px" viewBox="0 -960 960 960">
    <path d="M120-120v-720h720v720H120Zm60-500h600v-160H180v160Zm220 220h160v-160H400v160Zm0 220h160v-160H400v160ZM180-400h160v-160H180v160Zm440 0h160v-160H620v160ZM180-180h160v-160H180v160Zm440 0h160v-160H620v160Z"/>
  </svg>
    </button>

  <style>
    .colab-df-container {
      display:flex;
      gap: 12px;
    }

    .colab-df-convert {
      background-color: #E8F0FE;
      border: none;
      border-radius: 50%;
      cursor: pointer;
      display: none;
      fill: #1967D2;
      height: 32px;
      padding: 0 0 0 0;
      width: 32px;
    }

    .colab-df-convert:hover {
      background-color: #E2EBFA;
      box-shadow: 0px 1px 2px rgba(60, 64, 67, 0.3), 0px 1px 3px 1px rgba(60, 64, 67, 0.15);
      fill: #174EA6;
    }

    .colab-df-buttons div {
      margin-bottom: 4px;
    }

    [theme=dark] .colab-df-convert {
      background-color: #3B4455;
      fill: #D2E3FC;
    }

    [theme=dark] .colab-df-convert:hover {
      background-color: #434B5C;
      box-shadow: 0px 1px 3px 1px rgba(0, 0, 0, 0.15);
      filter: drop-shadow(0px 1px 2px rgba(0, 0, 0, 0.3));
      fill: #FFFFFF;
    }
  </style>

    <script>
      const buttonEl =
        document.querySelector('#df-f725b94d-fdaf-4b48-828b-7f19fe292352 button.colab-df-convert');
      buttonEl.style.display =
        google.colab.kernel.accessAllowed ? 'block' : 'none';

      async function convertToInteractive(key) {
        const element = document.querySelector('#df-f725b94d-fdaf-4b48-828b-7f19fe292352');
        const dataTable =
          await google.colab.kernel.invokeFunction('convertToInteractive',
                                                    [key], {});
        if (!dataTable) return;

        const docLinkHtml = 'Like what you see? Visit the ' +
          '<a target="_blank" href=https://colab.research.google.com/notebooks/data_table.ipynb>data table notebook</a>'
          + ' to learn more about interactive tables.';
        element.innerHTML = '';
        dataTable['output_type'] = 'display_data';
        await google.colab.output.renderOutput(dataTable, element);
        const docLink = document.createElement('div');
        docLink.innerHTML = docLinkHtml;
        element.appendChild(docLink);
      }
    </script>
  </div>


    </div>
  </div>




```python
print("\nNew batting_avg summary:")
print(model_df_filtered["batting_avg"].describe())
```

    
    New batting_avg summary:
    count    1671.000000
    mean        0.245833
    std         0.037783
    min         0.117188
    25%         0.222222
    50%         0.248092
    75%         0.270833
    max         0.370629
    Name: batting_avg, dtype: float64



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

    MSE: 0.0012255928897853135
    R^2: 0.21593389290425868



```python
coef_df = pd.DataFrame({
    "Feature": features,
    "Coefficient": model.coef_
}).sort_values(by="Coefficient", ascending=False)

print("\nFeature Importance (Linear Coefficients):")
display(coef_df)
```

    
    Feature Importance (Linear Coefficients):




  <div id="df-ae71ed40-1c54-47dd-854e-be986b69e7a8" class="colab-df-container">
    <div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Feature</th>
      <th>Coefficient</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>6</th>
      <td>line_drive_rate</td>
      <td>0.231040</td>
    </tr>
    <tr>
      <th>4</th>
      <td>avg_xwoba</td>
      <td>0.116484</td>
    </tr>
    <tr>
      <th>0</th>
      <td>avg_exit_velocity</td>
      <td>0.001696</td>
    </tr>
    <tr>
      <th>3</th>
      <td>max_exit_velocity</td>
      <td>0.000302</td>
    </tr>
    <tr>
      <th>2</th>
      <td>avg_hit_distance</td>
      <td>-0.000010</td>
    </tr>
    <tr>
      <th>1</th>
      <td>avg_launch_angle</td>
      <td>-0.001318</td>
    </tr>
    <tr>
      <th>5</th>
      <td>ground_ball_rate</td>
      <td>-0.052279</td>
    </tr>
    <tr>
      <th>7</th>
      <td>fly_ball_rate</td>
      <td>-0.080560</td>
    </tr>
    <tr>
      <th>8</th>
      <td>popup_rate</td>
      <td>-0.098201</td>
    </tr>
  </tbody>
</table>
</div>
    <div class="colab-df-buttons">

  <div class="colab-df-container">
    <button class="colab-df-convert" onclick="convertToInteractive('df-ae71ed40-1c54-47dd-854e-be986b69e7a8')"
            title="Convert this dataframe to an interactive table."
            style="display:none;">

  <svg xmlns="http://www.w3.org/2000/svg" height="24px" viewBox="0 -960 960 960">
    <path d="M120-120v-720h720v720H120Zm60-500h600v-160H180v160Zm220 220h160v-160H400v160Zm0 220h160v-160H400v160ZM180-400h160v-160H180v160Zm440 0h160v-160H620v160ZM180-180h160v-160H180v160Zm440 0h160v-160H620v160Z"/>
  </svg>
    </button>

  <style>
    .colab-df-container {
      display:flex;
      gap: 12px;
    }

    .colab-df-convert {
      background-color: #E8F0FE;
      border: none;
      border-radius: 50%;
      cursor: pointer;
      display: none;
      fill: #1967D2;
      height: 32px;
      padding: 0 0 0 0;
      width: 32px;
    }

    .colab-df-convert:hover {
      background-color: #E2EBFA;
      box-shadow: 0px 1px 2px rgba(60, 64, 67, 0.3), 0px 1px 3px 1px rgba(60, 64, 67, 0.15);
      fill: #174EA6;
    }

    .colab-df-buttons div {
      margin-bottom: 4px;
    }

    [theme=dark] .colab-df-convert {
      background-color: #3B4455;
      fill: #D2E3FC;
    }

    [theme=dark] .colab-df-convert:hover {
      background-color: #434B5C;
      box-shadow: 0px 1px 3px 1px rgba(0, 0, 0, 0.15);
      filter: drop-shadow(0px 1px 2px rgba(0, 0, 0, 0.3));
      fill: #FFFFFF;
    }
  </style>

    <script>
      const buttonEl =
        document.querySelector('#df-ae71ed40-1c54-47dd-854e-be986b69e7a8 button.colab-df-convert');
      buttonEl.style.display =
        google.colab.kernel.accessAllowed ? 'block' : 'none';

      async function convertToInteractive(key) {
        const element = document.querySelector('#df-ae71ed40-1c54-47dd-854e-be986b69e7a8');
        const dataTable =
          await google.colab.kernel.invokeFunction('convertToInteractive',
                                                    [key], {});
        if (!dataTable) return;

        const docLinkHtml = 'Like what you see? Visit the ' +
          '<a target="_blank" href=https://colab.research.google.com/notebooks/data_table.ipynb>data table notebook</a>'
          + ' to learn more about interactive tables.';
        element.innerHTML = '';
        dataTable['output_type'] = 'display_data';
        await google.colab.output.renderOutput(dataTable, element);
        const docLink = document.createElement('div');
        docLink.innerHTML = docLinkHtml;
        element.appendChild(docLink);
      }
    </script>
  </div>


  <div id="id_d88b7e19-5364-49e9-9ee7-3d81757167d5">
    <style>
      .colab-df-generate {
        background-color: #E8F0FE;
        border: none;
        border-radius: 50%;
        cursor: pointer;
        display: none;
        fill: #1967D2;
        height: 32px;
        padding: 0 0 0 0;
        width: 32px;
      }

      .colab-df-generate:hover {
        background-color: #E2EBFA;
        box-shadow: 0px 1px 2px rgba(60, 64, 67, 0.3), 0px 1px 3px 1px rgba(60, 64, 67, 0.15);
        fill: #174EA6;
      }

      [theme=dark] .colab-df-generate {
        background-color: #3B4455;
        fill: #D2E3FC;
      }

      [theme=dark] .colab-df-generate:hover {
        background-color: #434B5C;
        box-shadow: 0px 1px 3px 1px rgba(0, 0, 0, 0.15);
        filter: drop-shadow(0px 1px 2px rgba(0, 0, 0, 0.3));
        fill: #FFFFFF;
      }
    </style>
    <button class="colab-df-generate" onclick="generateWithVariable('coef_df')"
            title="Generate code using this dataframe."
            style="display:none;">

  <svg xmlns="http://www.w3.org/2000/svg" height="24px"viewBox="0 0 24 24"
       width="24px">
    <path d="M7,19H8.4L18.45,9,17,7.55,7,17.6ZM5,21V16.75L18.45,3.32a2,2,0,0,1,2.83,0l1.4,1.43a1.91,1.91,0,0,1,.58,1.4,1.91,1.91,0,0,1-.58,1.4L9.25,21ZM18.45,9,17,7.55Zm-12,3A5.31,5.31,0,0,0,4.9,8.1,5.31,5.31,0,0,0,1,6.5,5.31,5.31,0,0,0,4.9,4.9,5.31,5.31,0,0,0,6.5,1,5.31,5.31,0,0,0,8.1,4.9,5.31,5.31,0,0,0,12,6.5,5.46,5.46,0,0,0,6.5,12Z"/>
  </svg>
    </button>
    <script>
      (() => {
      const buttonEl =
        document.querySelector('#id_d88b7e19-5364-49e9-9ee7-3d81757167d5 button.colab-df-generate');
      buttonEl.style.display =
        google.colab.kernel.accessAllowed ? 'block' : 'none';

      buttonEl.onclick = () => {
        google.colab.notebook.generateWithVariable('coef_df');
      }
      })();
    </script>
  </div>

    </div>
  </div>




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


    
![png](statcast_pipeline_files/statcast_pipeline_17_0.png)
    



```python
## Save Final Relational Tables
players = model_df_filtered[["batter"]].drop_duplicates().copy()
players["player_id"] = players["batter"]

players = players[["player_id"]]

print(players.shape)
display(players.head())
```

    (677, 1)




  <div id="df-ba91f3a5-c05e-43a9-baf5-9886197f1939" class="colab-df-container">
    <div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>player_id</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>1</th>
      <td>134181</td>
    </tr>
    <tr>
      <th>5</th>
      <td>400121</td>
    </tr>
    <tr>
      <th>6</th>
      <td>400284</td>
    </tr>
    <tr>
      <th>7</th>
      <td>405395</td>
    </tr>
    <tr>
      <th>13</th>
      <td>408045</td>
    </tr>
  </tbody>
</table>
</div>
    <div class="colab-df-buttons">

  <div class="colab-df-container">
    <button class="colab-df-convert" onclick="convertToInteractive('df-ba91f3a5-c05e-43a9-baf5-9886197f1939')"
            title="Convert this dataframe to an interactive table."
            style="display:none;">

  <svg xmlns="http://www.w3.org/2000/svg" height="24px" viewBox="0 -960 960 960">
    <path d="M120-120v-720h720v720H120Zm60-500h600v-160H180v160Zm220 220h160v-160H400v160Zm0 220h160v-160H400v160ZM180-400h160v-160H180v160Zm440 0h160v-160H620v160ZM180-180h160v-160H180v160Zm440 0h160v-160H620v160Z"/>
  </svg>
    </button>

  <style>
    .colab-df-container {
      display:flex;
      gap: 12px;
    }

    .colab-df-convert {
      background-color: #E8F0FE;
      border: none;
      border-radius: 50%;
      cursor: pointer;
      display: none;
      fill: #1967D2;
      height: 32px;
      padding: 0 0 0 0;
      width: 32px;
    }

    .colab-df-convert:hover {
      background-color: #E2EBFA;
      box-shadow: 0px 1px 2px rgba(60, 64, 67, 0.3), 0px 1px 3px 1px rgba(60, 64, 67, 0.15);
      fill: #174EA6;
    }

    .colab-df-buttons div {
      margin-bottom: 4px;
    }

    [theme=dark] .colab-df-convert {
      background-color: #3B4455;
      fill: #D2E3FC;
    }

    [theme=dark] .colab-df-convert:hover {
      background-color: #434B5C;
      box-shadow: 0px 1px 3px 1px rgba(0, 0, 0, 0.15);
      filter: drop-shadow(0px 1px 2px rgba(0, 0, 0, 0.3));
      fill: #FFFFFF;
    }
  </style>

    <script>
      const buttonEl =
        document.querySelector('#df-ba91f3a5-c05e-43a9-baf5-9886197f1939 button.colab-df-convert');
      buttonEl.style.display =
        google.colab.kernel.accessAllowed ? 'block' : 'none';

      async function convertToInteractive(key) {
        const element = document.querySelector('#df-ba91f3a5-c05e-43a9-baf5-9886197f1939');
        const dataTable =
          await google.colab.kernel.invokeFunction('convertToInteractive',
                                                    [key], {});
        if (!dataTable) return;

        const docLinkHtml = 'Like what you see? Visit the ' +
          '<a target="_blank" href=https://colab.research.google.com/notebooks/data_table.ipynb>data table notebook</a>'
          + ' to learn more about interactive tables.';
        element.innerHTML = '';
        dataTable['output_type'] = 'display_data';
        await google.colab.output.renderOutput(dataTable, element);
        const docLink = document.createElement('div');
        docLink.innerHTML = docLinkHtml;
        element.appendChild(docLink);
      }
    </script>
  </div>


    </div>
  </div>




```python
seasons = model_df_filtered[["game_year"]].drop_duplicates().copy()
seasons = seasons.rename(columns={"game_year": "season"})

print(seasons.shape)
display(seasons)
```

    (4, 1)




  <div id="df-effcc39a-d232-4677-b6c3-d1a636976620" class="colab-df-container">
    <div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>season</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>1</th>
      <td>2018</td>
    </tr>
    <tr>
      <th>8</th>
      <td>2019</td>
    </tr>
    <tr>
      <th>9</th>
      <td>2020</td>
    </tr>
    <tr>
      <th>10</th>
      <td>2021</td>
    </tr>
  </tbody>
</table>
</div>
    <div class="colab-df-buttons">

  <div class="colab-df-container">
    <button class="colab-df-convert" onclick="convertToInteractive('df-effcc39a-d232-4677-b6c3-d1a636976620')"
            title="Convert this dataframe to an interactive table."
            style="display:none;">

  <svg xmlns="http://www.w3.org/2000/svg" height="24px" viewBox="0 -960 960 960">
    <path d="M120-120v-720h720v720H120Zm60-500h600v-160H180v160Zm220 220h160v-160H400v160Zm0 220h160v-160H400v160ZM180-400h160v-160H180v160Zm440 0h160v-160H620v160ZM180-180h160v-160H180v160Zm440 0h160v-160H620v160Z"/>
  </svg>
    </button>

  <style>
    .colab-df-container {
      display:flex;
      gap: 12px;
    }

    .colab-df-convert {
      background-color: #E8F0FE;
      border: none;
      border-radius: 50%;
      cursor: pointer;
      display: none;
      fill: #1967D2;
      height: 32px;
      padding: 0 0 0 0;
      width: 32px;
    }

    .colab-df-convert:hover {
      background-color: #E2EBFA;
      box-shadow: 0px 1px 2px rgba(60, 64, 67, 0.3), 0px 1px 3px 1px rgba(60, 64, 67, 0.15);
      fill: #174EA6;
    }

    .colab-df-buttons div {
      margin-bottom: 4px;
    }

    [theme=dark] .colab-df-convert {
      background-color: #3B4455;
      fill: #D2E3FC;
    }

    [theme=dark] .colab-df-convert:hover {
      background-color: #434B5C;
      box-shadow: 0px 1px 3px 1px rgba(0, 0, 0, 0.15);
      filter: drop-shadow(0px 1px 2px rgba(0, 0, 0, 0.3));
      fill: #FFFFFF;
    }
  </style>

    <script>
      const buttonEl =
        document.querySelector('#df-effcc39a-d232-4677-b6c3-d1a636976620 button.colab-df-convert');
      buttonEl.style.display =
        google.colab.kernel.accessAllowed ? 'block' : 'none';

      async function convertToInteractive(key) {
        const element = document.querySelector('#df-effcc39a-d232-4677-b6c3-d1a636976620');
        const dataTable =
          await google.colab.kernel.invokeFunction('convertToInteractive',
                                                    [key], {});
        if (!dataTable) return;

        const docLinkHtml = 'Like what you see? Visit the ' +
          '<a target="_blank" href=https://colab.research.google.com/notebooks/data_table.ipynb>data table notebook</a>'
          + ' to learn more about interactive tables.';
        element.innerHTML = '';
        dataTable['output_type'] = 'display_data';
        await google.colab.output.renderOutput(dataTable, element);
        const docLink = document.createElement('div');
        docLink.innerHTML = docLinkHtml;
        element.appendChild(docLink);
      }
    </script>
  </div>


  <div id="id_558c5776-2187-4832-8b18-5103df91cb90">
    <style>
      .colab-df-generate {
        background-color: #E8F0FE;
        border: none;
        border-radius: 50%;
        cursor: pointer;
        display: none;
        fill: #1967D2;
        height: 32px;
        padding: 0 0 0 0;
        width: 32px;
      }

      .colab-df-generate:hover {
        background-color: #E2EBFA;
        box-shadow: 0px 1px 2px rgba(60, 64, 67, 0.3), 0px 1px 3px 1px rgba(60, 64, 67, 0.15);
        fill: #174EA6;
      }

      [theme=dark] .colab-df-generate {
        background-color: #3B4455;
        fill: #D2E3FC;
      }

      [theme=dark] .colab-df-generate:hover {
        background-color: #434B5C;
        box-shadow: 0px 1px 3px 1px rgba(0, 0, 0, 0.15);
        filter: drop-shadow(0px 1px 2px rgba(0, 0, 0, 0.3));
        fill: #FFFFFF;
      }
    </style>
    <button class="colab-df-generate" onclick="generateWithVariable('seasons')"
            title="Generate code using this dataframe."
            style="display:none;">

  <svg xmlns="http://www.w3.org/2000/svg" height="24px"viewBox="0 0 24 24"
       width="24px">
    <path d="M7,19H8.4L18.45,9,17,7.55,7,17.6ZM5,21V16.75L18.45,3.32a2,2,0,0,1,2.83,0l1.4,1.43a1.91,1.91,0,0,1,.58,1.4,1.91,1.91,0,0,1-.58,1.4L9.25,21ZM18.45,9,17,7.55Zm-12,3A5.31,5.31,0,0,0,4.9,8.1,5.31,5.31,0,0,0,1,6.5,5.31,5.31,0,0,0,4.9,4.9,5.31,5.31,0,0,0,6.5,1,5.31,5.31,0,0,0,8.1,4.9,5.31,5.31,0,0,0,12,6.5,5.46,5.46,0,0,0,6.5,12Z"/>
  </svg>
    </button>
    <script>
      (() => {
      const buttonEl =
        document.querySelector('#id_558c5776-2187-4832-8b18-5103df91cb90 button.colab-df-generate');
      buttonEl.style.display =
        google.colab.kernel.accessAllowed ? 'block' : 'none';

      buttonEl.onclick = () => {
        google.colab.notebook.generateWithVariable('seasons');
      }
      })();
    </script>
  </div>

    </div>
  </div>




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
import os

os.makedirs("data/final", exist_ok=True)

players.to_csv("data/final/players.csv", index=False)
seasons.to_csv("data/final/seasons.csv", index=False)
player_season_statcast_final.to_csv("data/final/player_season_statcast.csv", index=False)
player_season_batting_final.to_csv("data/final/player_season_batting.csv", index=False)

print("All tables saved!")
```

    All tables saved!



```python
## Load Data into DuckDB
import duckdb

# Create connection (in-memory)
con = duckdb.connect()

print("DuckDB connected")
```

    DuckDB connected



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

    Tables loaded into DuckDB



```python
print(con.execute("SHOW TABLES").fetchall())
```

    [('player_season_batting',), ('player_season_statcast',), ('players',), ('seasons',)]



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

    (1671, 7)




  <div id="df-33c3e8ed-a8aa-480a-aa21-5671ea54b40c" class="colab-df-container">
    <div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>batter</th>
      <th>game_year</th>
      <th>avg_exit_velocity</th>
      <th>avg_launch_angle</th>
      <th>avg_xwoba</th>
      <th>batting_avg</th>
      <th>home_runs</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>134181</td>
      <td>2018</td>
      <td>88.697674</td>
      <td>13.633721</td>
      <td>0.382826</td>
      <td>0.271889</td>
      <td>15</td>
    </tr>
    <tr>
      <th>1</th>
      <td>400121</td>
      <td>2018</td>
      <td>87.743632</td>
      <td>13.981132</td>
      <td>0.333219</td>
      <td>0.249467</td>
      <td>9</td>
    </tr>
    <tr>
      <th>2</th>
      <td>400284</td>
      <td>2018</td>
      <td>87.292857</td>
      <td>16.547619</td>
      <td>0.303603</td>
      <td>0.213415</td>
      <td>1</td>
    </tr>
    <tr>
      <th>3</th>
      <td>405395</td>
      <td>2018</td>
      <td>90.028288</td>
      <td>14.148883</td>
      <td>0.365859</td>
      <td>0.245161</td>
      <td>19</td>
    </tr>
    <tr>
      <th>4</th>
      <td>405395</td>
      <td>2019</td>
      <td>88.294340</td>
      <td>12.547170</td>
      <td>0.341953</td>
      <td>0.244399</td>
      <td>23</td>
    </tr>
  </tbody>
</table>
</div>
    <div class="colab-df-buttons">

  <div class="colab-df-container">
    <button class="colab-df-convert" onclick="convertToInteractive('df-33c3e8ed-a8aa-480a-aa21-5671ea54b40c')"
            title="Convert this dataframe to an interactive table."
            style="display:none;">

  <svg xmlns="http://www.w3.org/2000/svg" height="24px" viewBox="0 -960 960 960">
    <path d="M120-120v-720h720v720H120Zm60-500h600v-160H180v160Zm220 220h160v-160H400v160Zm0 220h160v-160H400v160ZM180-400h160v-160H180v160Zm440 0h160v-160H620v160ZM180-180h160v-160H180v160Zm440 0h160v-160H620v160Z"/>
  </svg>
    </button>

  <style>
    .colab-df-container {
      display:flex;
      gap: 12px;
    }

    .colab-df-convert {
      background-color: #E8F0FE;
      border: none;
      border-radius: 50%;
      cursor: pointer;
      display: none;
      fill: #1967D2;
      height: 32px;
      padding: 0 0 0 0;
      width: 32px;
    }

    .colab-df-convert:hover {
      background-color: #E2EBFA;
      box-shadow: 0px 1px 2px rgba(60, 64, 67, 0.3), 0px 1px 3px 1px rgba(60, 64, 67, 0.15);
      fill: #174EA6;
    }

    .colab-df-buttons div {
      margin-bottom: 4px;
    }

    [theme=dark] .colab-df-convert {
      background-color: #3B4455;
      fill: #D2E3FC;
    }

    [theme=dark] .colab-df-convert:hover {
      background-color: #434B5C;
      box-shadow: 0px 1px 3px 1px rgba(0, 0, 0, 0.15);
      filter: drop-shadow(0px 1px 2px rgba(0, 0, 0, 0.3));
      fill: #FFFFFF;
    }
  </style>

    <script>
      const buttonEl =
        document.querySelector('#df-33c3e8ed-a8aa-480a-aa21-5671ea54b40c button.colab-df-convert');
      buttonEl.style.display =
        google.colab.kernel.accessAllowed ? 'block' : 'none';

      async function convertToInteractive(key) {
        const element = document.querySelector('#df-33c3e8ed-a8aa-480a-aa21-5671ea54b40c');
        const dataTable =
          await google.colab.kernel.invokeFunction('convertToInteractive',
                                                    [key], {});
        if (!dataTable) return;

        const docLinkHtml = 'Like what you see? Visit the ' +
          '<a target="_blank" href=https://colab.research.google.com/notebooks/data_table.ipynb>data table notebook</a>'
          + ' to learn more about interactive tables.';
        element.innerHTML = '';
        dataTable['output_type'] = 'display_data';
        await google.colab.output.renderOutput(dataTable, element);
        const docLink = document.createElement('div');
        docLink.innerHTML = docLinkHtml;
        element.appendChild(docLink);
      }
    </script>
  </div>


    </div>
  </div>




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



  <div id="df-4ce5a0b7-4fb7-4620-a724-0f433cad1057" class="colab-df-container">
    <div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>batter</th>
      <th>game_year</th>
      <th>avg_exit_velocity</th>
      <th>batting_avg</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>592450</td>
      <td>2019</td>
      <td>96.003433</td>
      <td>0.271768</td>
    </tr>
    <tr>
      <th>1</th>
      <td>665487</td>
      <td>2020</td>
      <td>95.939024</td>
      <td>0.275556</td>
    </tr>
    <tr>
      <th>2</th>
      <td>592450</td>
      <td>2021</td>
      <td>95.824365</td>
      <td>0.287273</td>
    </tr>
    <tr>
      <th>3</th>
      <td>665489</td>
      <td>2021</td>
      <td>95.121907</td>
      <td>0.311258</td>
    </tr>
    <tr>
      <th>4</th>
      <td>519317</td>
      <td>2021</td>
      <td>95.100852</td>
      <td>0.272016</td>
    </tr>
    <tr>
      <th>5</th>
      <td>593934</td>
      <td>2020</td>
      <td>94.941414</td>
      <td>0.205263</td>
    </tr>
    <tr>
      <th>6</th>
      <td>592450</td>
      <td>2018</td>
      <td>94.701128</td>
      <td>0.277108</td>
    </tr>
    <tr>
      <th>7</th>
      <td>593934</td>
      <td>2019</td>
      <td>94.409009</td>
      <td>0.246719</td>
    </tr>
    <tr>
      <th>8</th>
      <td>408234</td>
      <td>2018</td>
      <td>94.382407</td>
      <td>0.298507</td>
    </tr>
    <tr>
      <th>9</th>
      <td>446334</td>
      <td>2021</td>
      <td>94.143850</td>
      <td>0.260870</td>
    </tr>
  </tbody>
</table>
</div>
    <div class="colab-df-buttons">

  <div class="colab-df-container">
    <button class="colab-df-convert" onclick="convertToInteractive('df-4ce5a0b7-4fb7-4620-a724-0f433cad1057')"
            title="Convert this dataframe to an interactive table."
            style="display:none;">

  <svg xmlns="http://www.w3.org/2000/svg" height="24px" viewBox="0 -960 960 960">
    <path d="M120-120v-720h720v720H120Zm60-500h600v-160H180v160Zm220 220h160v-160H400v160Zm0 220h160v-160H400v160ZM180-400h160v-160H180v160Zm440 0h160v-160H620v160ZM180-180h160v-160H180v160Zm440 0h160v-160H620v160Z"/>
  </svg>
    </button>

  <style>
    .colab-df-container {
      display:flex;
      gap: 12px;
    }

    .colab-df-convert {
      background-color: #E8F0FE;
      border: none;
      border-radius: 50%;
      cursor: pointer;
      display: none;
      fill: #1967D2;
      height: 32px;
      padding: 0 0 0 0;
      width: 32px;
    }

    .colab-df-convert:hover {
      background-color: #E2EBFA;
      box-shadow: 0px 1px 2px rgba(60, 64, 67, 0.3), 0px 1px 3px 1px rgba(60, 64, 67, 0.15);
      fill: #174EA6;
    }

    .colab-df-buttons div {
      margin-bottom: 4px;
    }

    [theme=dark] .colab-df-convert {
      background-color: #3B4455;
      fill: #D2E3FC;
    }

    [theme=dark] .colab-df-convert:hover {
      background-color: #434B5C;
      box-shadow: 0px 1px 3px 1px rgba(0, 0, 0, 0.15);
      filter: drop-shadow(0px 1px 2px rgba(0, 0, 0, 0.3));
      fill: #FFFFFF;
    }
  </style>

    <script>
      const buttonEl =
        document.querySelector('#df-4ce5a0b7-4fb7-4620-a724-0f433cad1057 button.colab-df-convert');
      buttonEl.style.display =
        google.colab.kernel.accessAllowed ? 'block' : 'none';

      async function convertToInteractive(key) {
        const element = document.querySelector('#df-4ce5a0b7-4fb7-4620-a724-0f433cad1057');
        const dataTable =
          await google.colab.kernel.invokeFunction('convertToInteractive',
                                                    [key], {});
        if (!dataTable) return;

        const docLinkHtml = 'Like what you see? Visit the ' +
          '<a target="_blank" href=https://colab.research.google.com/notebooks/data_table.ipynb>data table notebook</a>'
          + ' to learn more about interactive tables.';
        element.innerHTML = '';
        dataTable['output_type'] = 'display_data';
        await google.colab.output.renderOutput(dataTable, element);
        const docLink = document.createElement('div');
        docLink.innerHTML = docLinkHtml;
        element.appendChild(docLink);
      }
    </script>
  </div>


  <div id="id_38dcaa64-16fc-4cd8-b10f-47af608991ec">
    <style>
      .colab-df-generate {
        background-color: #E8F0FE;
        border: none;
        border-radius: 50%;
        cursor: pointer;
        display: none;
        fill: #1967D2;
        height: 32px;
        padding: 0 0 0 0;
        width: 32px;
      }

      .colab-df-generate:hover {
        background-color: #E2EBFA;
        box-shadow: 0px 1px 2px rgba(60, 64, 67, 0.3), 0px 1px 3px 1px rgba(60, 64, 67, 0.15);
        fill: #174EA6;
      }

      [theme=dark] .colab-df-generate {
        background-color: #3B4455;
        fill: #D2E3FC;
      }

      [theme=dark] .colab-df-generate:hover {
        background-color: #434B5C;
        box-shadow: 0px 1px 3px 1px rgba(0, 0, 0, 0.15);
        filter: drop-shadow(0px 1px 2px rgba(0, 0, 0, 0.3));
        fill: #FFFFFF;
      }
    </style>
    <button class="colab-df-generate" onclick="generateWithVariable('top_players')"
            title="Generate code using this dataframe."
            style="display:none;">

  <svg xmlns="http://www.w3.org/2000/svg" height="24px"viewBox="0 0 24 24"
       width="24px">
    <path d="M7,19H8.4L18.45,9,17,7.55,7,17.6ZM5,21V16.75L18.45,3.32a2,2,0,0,1,2.83,0l1.4,1.43a1.91,1.91,0,0,1,.58,1.4,1.91,1.91,0,0,1-.58,1.4L9.25,21ZM18.45,9,17,7.55Zm-12,3A5.31,5.31,0,0,0,4.9,8.1,5.31,5.31,0,0,0,1,6.5,5.31,5.31,0,0,0,4.9,4.9,5.31,5.31,0,0,0,6.5,1,5.31,5.31,0,0,0,8.1,4.9,5.31,5.31,0,0,0,12,6.5,5.46,5.46,0,0,0,6.5,12Z"/>
  </svg>
    </button>
    <script>
      (() => {
      const buttonEl =
        document.querySelector('#id_38dcaa64-16fc-4cd8-b10f-47af608991ec button.colab-df-generate');
      buttonEl.style.display =
        google.colab.kernel.accessAllowed ? 'block' : 'none';

      buttonEl.onclick = () => {
        google.colab.notebook.generateWithVariable('top_players');
      }
      })();
    </script>
  </div>

    </div>
  </div>


