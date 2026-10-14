# Charts

All 9 charts produced by [`../notebook/nyc_analysis.ipynb`](../notebook/nyc_analysis.ipynb), exported as PNG (150 dpi). Every chart uses the **cleaned** data: 1,447,796 trips lasting 1–120 minutes (10,848 rows, 0.74%, removed), pickups from 2016-01-01 to 2016-06-30.

| # | Chart | File | Question it answers |
|---|---|---|---|
| 1 | [Trips by hour of day](#1-trips-by-hour-of-day) | [`01_trips_by_hour.png`](01_trips_by_hour.png) | When are the peak and off-peak hours? |
| 2 | [Trips by day of week](#2-trips-by-day-of-week) | [`02_trips_by_weekday.png`](02_trips_by_weekday.png) | Which days are busy, and are weekends quieter? |
| 3 | [Average trip duration by hour](#3-average-trip-duration-by-hour) | [`03_avg_duration_by_hour.png`](03_avg_duration_by_hour.png) | When are trips longest? |
| 4 | [Trip duration distribution](#4-trip-duration-distribution) | [`04_trip_duration_distribution.png`](04_trip_duration_distribution.png) | What does a typical trip look like? |
| 5 | [Monthly trips](#5-monthly-trips) | [`05_monthly_trips.png`](05_monthly_trips.png) | Is there a seasonal pattern? |
| 6 | [Hour × weekday heatmap](#6-hour--weekday-heatmap) | [`06_hour_weekday_heatmap.png`](06_hour_weekday_heatmap.png) | Where exactly are the hot and cold time slots? |
| 7 | [Weekday vs weekend hourly profile](#7-weekday-vs-weekend-hourly-profile) | [`07_weekday_vs_weekend_profile.png`](07_weekday_vs_weekend_profile.png) | Do weekdays and weekends follow the same daily rhythm? |
| 8 | [Vendor duration by hour](#8-vendor-duration-by-hour) | [`08_vendor_duration_by_hour.png`](08_vendor_duration_by_hour.png) | Do the two vendors differ? |
| 9 | [Daily trips and anomalies](#9-daily-trips-and-anomalies) | [`09_daily_trips_anomalies.png`](09_daily_trips_anomalies.png) | Are there unusual days? |

---

## 1. Trips by hour of day

**File:** [`01_trips_by_hour.png`](01_trips_by_hour.png) · notebook section 2.1

![Trips by hour of day](01_trips_by_hour.png)

**What it shows:** the number of trips for each pickup hour (0–23), with the peak window shaded orange and the off-peak window shaded grey. Peak = hours above 1.25× the hourly average; off-peak = hours below 0.5× the average.

**Analysis**
- **18:00 is the busiest hour** with 90,022 trips (6.2% of all trips); 19:00 is almost identical (89,753).
- **Peak window 17:00–22:59:** 502,764 trips = **34.7% of all trips in 6 of the 24 hours**.
- **Off-peak window 02:00–05:59:** 78,589 trips = 5.4% of all trips. The quietest hour is **05:00 (14,767 trips)**, 6.1× lower than 18:00.
- Between 08:00 and 16:59 demand is a plateau of about 64–74k trips per hour, with a small dip at 16:00 (63,758) right before the evening surge.
- The **evening, not the morning, is the dominant rush hour**.

---

## 2. Trips by day of week

**File:** [`02_trips_by_weekday.png`](02_trips_by_weekday.png) · notebook section 2.2

![Trips by day of week](02_trips_by_weekday.png)

**What it shows:** left — total trips per weekday; right — average trips per calendar day (each weekday occurs 26 times in the 182-day period, so the two panels are nearly identical in shape).

**Analysis**
- **Friday is the busiest day** (221,870 trips, 8,533 per day) and **Monday the quietest** (186,051 trips, 7,156 per day): a **+19.3%** gap per calendar day.
- Demand climbs from Monday to Friday, stays high on Saturday (8,430 per day), and falls on Sunday (7,449).
- **Weekends are not quieter than weekdays:** a weekend day averages 7,940 trips vs 7,961 on a weekday (−0.3%). Weekends hold 28.5% of trips, almost exactly the 28.6% that 2 of 7 days would give. The low days are **Monday and Sunday**.
- Statistical check (notebook section 3.7): weekday explains about 52% of the rank variance in daily trips (Kruskal-Wallis ε² = 0.52).

---

## 3. Average trip duration by hour

**File:** [`03_avg_duration_by_hour.png`](03_avg_duration_by_hour.png) · notebook section 2.3

![Average trip duration by hour](03_avg_duration_by_hour.png)

**What it shows:** mean (blue) and median (orange) trip duration in minutes for each pickup hour.

**Analysis**
- **Trips are longest at 15:00–16:59 (16.2 min mean)** and **shortest at 06:00 (11.3 min)**: a 43% difference.
- The busiest pickup hours are *not* the longest-trip hours: 18:00 averages 14.4 min and 19:00 13.2 min, shorter than the afternoon.
- Mean and median diverge most in the early morning (06:00: 11.3 vs 7.9 min; 05:00: 12.0 vs 8.8 min), where a few long trips dominate a small number of rides.
- **Occupied car-hours** (trips × average duration) rank 18:00 first (21,603), but 19:00, 17:00, 14:00 and 15:00 are all within about 11% of it (19,188–19,780), even though 14:00 and 15:00 have 18–21% fewer trips than 18:00. The afternoon keeps cars almost as busy as the evening peak.
- Hour of day explains about 1.1% of rank variance in duration (Kruskal-Wallis ε² = 0.011): a real but small effect.

---

## 4. Trip duration distribution

**File:** [`04_trip_duration_distribution.png`](04_trip_duration_distribution.png) · notebook section 2.4

![Distribution of trip duration](04_trip_duration_distribution.png)

**What it shows:** a histogram of trip duration (1–120 min, 60 bins) with the mean (dashed orange) and median (solid black).

**Analysis**
- The distribution is **right-skewed** (skew = 2.21): most trips are short and a long tail stretches towards 120 minutes.
- **Median 11.1 min, mean 14.0 min.** 75% of trips are under 17.9 min, 95% under 34.8 min, and only 0.7% exceed 60 min.
- Because of the skew, the median is used for headline statements and rank-based tests (Mann-Whitney, Kruskal-Wallis) are used instead of t-tests.
- Cleaning impact: before filtering the mean was 15.99 min; filtering 0.74% of rows lowered it by 12.4%, while the median barely moved (11.03 → 11.08).

---

## 5. Monthly trips

**File:** [`05_monthly_trips.png`](05_monthly_trips.png) · notebook section 2.5

![Monthly trips](05_monthly_trips.png)

**What it shows:** left — total trips per month; right — average trips per calendar day (corrects for months having 29, 30 or 31 days).

**Analysis**
- **March has the most trips in total (254,306)**, but only because it has 31 days. Per calendar day, **April leads (8,324/day)**.
- Per-day demand goes *up then down*: Jan 7,357 → Feb 8,159 → Mar 8,203 → Apr 8,324 → May 7,954 → Jun 7,751.
- January is pulled down by two storm-affected days (see [chart 9](#9-daily-trips-and-anomalies)); without 23–24 January it averages 7,693 per day, which is still the lowest month.
- Average trip duration rises almost monotonically from 13.2 min (Jan–Feb) to 14.9 min (Jun), +12.6%. June, not April, is the longest-trip month.
- **Caution:** the data covers only six months, so this is a Jan–Jun pattern, not a proven seasonal cycle. Month explains just 0.26% of rank variance in duration (ε² = 0.0026).

---

## 6. Hour × weekday heatmap

**File:** [`06_hour_weekday_heatmap.png`](06_hour_weekday_heatmap.png) · notebook section 2.6

![Heatmap of trips by hour and weekday](06_hour_weekday_heatmap.png)

**What it shows:** the trip count for every hour × weekday combination (168 cells); darker red = more trips.

**Analysis**
- **Hottest cell: Friday 19:00 (14,087 trips).** Wednesday–Friday evenings (18:00–22:00) are the darkest block.
- **Coldest cell: Tuesday 03:00 (1,001 trips).**
- On **Saturday and Sunday the heat moves past midnight**: between 00:00 and 03:59 a weekend day averages **1,450 trips vs 503 on a weekday (2.9×)**.
- The **weekday morning commute (07:00–09:59)** shows as a clear red band Monday–Friday and is absent on the weekend.
- The heatmap shows why "weekday vs weekend" is too coarse: each day has its own rhythm.

---

## 7. Weekday vs weekend hourly profile

**File:** [`07_weekday_vs_weekend_profile.png`](07_weekday_vs_weekend_profile.png) · notebook section 2.7

![Weekday vs weekend hourly profile](07_weekday_vs_weekend_profile.png)

**What it shows:** for weekdays (Mon–Fri) and weekends (Sat–Sun), the share of that day type's trips falling in each hour. Using shares removes the effect of different day counts and makes the *shape* comparable.

**Analysis**
- **07:00–09:59 holds 15.3% of weekday trips but only 7.3% of weekend trips**: the commute shoulder exists on weekdays only.
- **00:00–03:59 holds 17.9% of weekend trips vs 6.3% on weekdays**: weekends have a late-night tail.
- Weekend afternoons are flatter, and both day types converge on an evening peak.
- One staffing template cannot serve both day types.

---

## 8. Vendor duration by hour

**File:** [`08_vendor_duration_by_hour.png`](08_vendor_duration_by_hour.png) · notebook section 2.8

![Average trip duration by hour for vendor 1 and vendor 2](08_vendor_duration_by_hour.png)

**What it shows:** average trip duration by pickup hour for vendor 1 (blue) and vendor 2 (orange).

**Analysis**
- After cleaning, the vendors are almost identical: **vendor 2 has 53.5% of trips (774,632), vendor 1 46.5% (673,164)**; mean durations 14.08 vs 13.93 min; medians 11.12 vs 11.05 min.
- The hourly gap between vendors stays between −0.10 and +0.65 min.
- Mann-Whitney effect size is −0.005 (negligible); vendor share changes only between 49.6% and 54.5% across hours (Cramér's V = 0.02).
- In the **raw** data the vendor mean gap was 3.5 min (17.6 vs 14.1). It came from vendor 2: it recorded 2,170 of the 2,253 trips longer than 120 minutes (96.3%), including 1,839 trips lasting 23–24 hours, which is more consistent with a recording issue (e.g. a meter left running) than with real trips. This is an interpretation, not something the data proves.

---

## 9. Daily trips and anomalies

**File:** [`09_daily_trips_anomalies.png`](09_daily_trips_anomalies.png) · notebook section 2.9

![Daily trips with 7-day rolling mean and anomalies](09_daily_trips_anomalies.png)

**What it shows:** trips per calendar day (light blue), the centred 7-day rolling mean (orange), and days with |z-score| > 3 (red dots).

**Analysis**
- The average day has 7,955 trips, and the 7-day rolling mean stays between roughly 7,000 and 8,600 for most of the period. The weekly zig-zag is the weekday pattern from chart 2.
- Only **two days are statistical anomalies**: **23 January (1,627 trips, −79.5% vs average)** and **24 January (3,352, −57.9%)**. Demand recovers over three days (25 Jan 6,034, −24.1%; 26 Jan 6,888, −13.4%).
- These dates coincide with the January 2016 East Coast blizzard. **Weather is not in the dataset**, so the link is external context and is not proven by this data.
- A smaller dip is visible at the end of May: 30 May has 5,523 trips (−30.6%), the third-lowest day. It is not flagged by the |z| > 3 rule, and it coincides with a US public holiday (Memorial Day); this link is also untested.
- Removing the two storm days does not change the weekday finding (Kruskal-Wallis ε² = 0.55 without them).

---

**Reading note:** correlation is not causation. None of the patterns above were tested with a controlled experiment, and explanations involving weather, traffic or holidays are assumptions.

