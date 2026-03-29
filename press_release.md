# Unlocking the Hidden Drivers of Batting Success in Major League Baseball

## Hook
What actually makes a great hitter? Is it raw power, consistency, or something deeper hidden in the data? Using cutting-edge MLB Statcast tracking data, this project reveals the underlying factors that separate elite hitters from the rest.

## Problem Statement
Batting average remains one of the most widely recognized statistics in baseball, yet it often fails to explain *why* certain players consistently perform better than others. Traditional metrics overlook the rich, high-frequency tracking data now available through MLB Statcast, which captures detailed information about every batted ball — including exit velocity, launch angle, and hit distance.

The challenge is clear: **can we use advanced Statcast data to better understand and predict player hitting performance?** Specifically, this project investigates how underlying contact quality metrics influence batting average across MLB players over multiple seasons.

## Solution Description
To address this problem, we constructed a comprehensive dataset combining millions of Statcast observations from 2018–2021 into a structured relational database. These raw events were transformed into meaningful player-season metrics, capturing both traditional batting statistics and advanced measures of contact quality.

Using this dataset, we developed a statistical model that links key performance indicators — such as exit velocity, launch angle, and expected weighted on-base average (xwOBA) — to batting average. The results show that while no single metric fully explains hitting success, **a combination of contact quality indicators provides measurable predictive power**.

This approach moves beyond traditional statistics, offering a more nuanced understanding of player performance and demonstrating how modern data can enhance decision-making in sports analytics.

## Chart
<img width="3046" height="2547" alt="statcast_pipeline_figure" src="https://github.com/user-attachments/assets/3ed83803-cf26-497a-89e1-8fe48eaac82d" />

*Figure 1: Three-panel figure. Top-left: distribution of batting average across the filtered player-season observations in the final modeling dataset, with the dataset mean marked. Top-right: actual versus predicted batting average from the linear regression model, with the 45-degree line representing perfect predictions. Bottom: average batting average by season from 2018 to 2021.*

The figure connects the project narrative to measurable evidence: batting average varies meaningfully across qualified player-seasons, Statcast-derived contact-quality variables contain predictive signal, and league-level batting average remains relatively stable across seasons even though individual player outcomes differ substantially.

**Top-left — Batting average distribution:** This panel shows the distribution of batting average in the final filtered dataset. Most player-seasons fall within a moderate batting average range, with fewer observations at the extremes. The dashed line marks the dataset mean. This demonstrates that batting average is concentrated around a typical league level, but still has enough variation to motivate predictive modeling.

**Top-right — Actual vs. predicted batting average:** This panel evaluates model performance by comparing actual batting average to predicted batting average on the held-out test set. The dashed 45-degree line represents perfect predictions. Observations closer to the line are better predicted by the model. The figure shows that Statcast-based metrics provide real predictive value, while also making clear that substantial unexplained variation remains.

**Bottom — Batting average by season:** This panel shows average batting average across seasons from 2018 through 2021. League-level averages are relatively stable over time, suggesting that the main analytical challenge is not explaining dramatic league-wide changes, but rather understanding differences in performance across players within the same season.

**Takeaway:** Batting average is not fully random, but it is also not fully determined by contact-quality metrics alone. Statcast features such as exit velocity, launch angle, and expected quality of contact contribute meaningful predictive information, while still leaving room for other influences such as speed, defensive positioning, plate approach, and chance.
