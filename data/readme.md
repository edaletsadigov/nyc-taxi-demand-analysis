# Dataset: NYC Taxi Trip Duration

## Overview

| Property | Value |
|---|---|
| File used | `NYC.csv` (same content as the Kaggle file `train.csv`) |
| Source | Kaggle: [`yasserh/nyc-taxi-trip-duration`](https://www.kaggle.com/datasets/yasserh/nyc-taxi-trip-duration), the dataset suggested in the task. Check the licence and terms on the Kaggle page before redistributing |
| Rows | **1,458,644** (one row = one taxi trip) |
| Columns | **11** |
| File size | ~200 MB |
| Period | 2016-01-01 00:00:17 to 2016-06-30 23:59:39 (182 days, January–June 2016) |
| Missing values | **0** |
| Duplicate rows / duplicate `id` | 0 / 0 |
| Memory in pandas | ~440 MB |

> **The CSV is not stored in this repository.** GitHub rejects files larger than 100 MB. Download `NYC.csv` from Kaggle and place it in [`../notebook/`](../notebook/), next to `nyc_analysis.ipynb`. The file is listed in `.gitignore`.

Each row describes a single trip: who recorded it (vendor), when and where it started and ended, how many passengers, and how long it lasted. The dataset is typically used to predict `trip_duration`; in this project it is used for **time-based demand analysis** (hour, weekday, month patterns).

Example row:

```text
id2875421, 2, 2016-03-14 17:24:55, 2016-03-14 17:32:30, 1, -73.98215, 40.76794, -73.96463, 40.76560, N, 455
```

---

## Columns

| # | Column | Type | Description | Values in this data |
|---|---|---|---|---|
| 1 | `id` | string | Unique trip identifier | 1,458,644 unique values (no duplicates) |
| 2 | `vendor_id` | integer | Code of the provider (company) that recorded the trip | 2 values: `1` = 678,342 trips (46.5%), `2` = 780,302 trips (53.5%) |
| 3 | `pickup_datetime` | string (`YYYY-MM-DD HH:MM:SS`) | Date and time when the meter was engaged. Parsed to datetime in the notebook | 2016-01-01 00:00:17 → 2016-06-30 23:59:39 |
| 4 | `dropoff_datetime` | string (`YYYY-MM-DD HH:MM:SS`) | Date and time when the meter was disengaged. Parsed to datetime in the notebook | `dropoff − pickup` equals `trip_duration` in all rows |
| 5 | `passenger_count` | integer | Number of passengers in the vehicle (a driver-entered value) | 0 to 9. `1` = 1,033,540 trips; `2` = 210,318; `5` = 78,088; `3` = 59,896; `6` = 48,333; `4` = 28,404; `0` = 60; `7`, `8`, `9` = 5 trips combined |
| 6 | `pickup_longitude` | float | Longitude where the meter was engaged | −121.93 to −61.34 (median −73.98) |
| 7 | `pickup_latitude` | float | Latitude where the meter was engaged | 34.36 to 51.88 (median 40.75) |
| 8 | `dropoff_longitude` | float | Longitude where the meter was disengaged | −121.93 to −61.34 (median −73.98) |
| 9 | `dropoff_latitude` | float | Latitude where the meter was disengaged | 32.18 to 43.92 (median 40.75) |
| 10 | `store_and_fwd_flag` | string | Whether the trip record was held in the vehicle's memory before being sent to the vendor because the vehicle had no connection to the server: `Y` = store and forward, `N` = not a store-and-forward trip | `N` = 1,450,599 (99.45%), `Y` = 8,045 (0.55%) |
| 11 | `trip_duration` | integer | Trip duration **in seconds** (target variable of the original competition) | 1 to 3,526,282 s (about 41 days); median 662 s (11.0 min); mean 959 s |

---

## Data quality notes (found in the notebook)

- **`trip_duration` has extreme outliers.** 8,595 trips (0.59%) last under 1 minute and 2,253 (0.15%) last over 120 minutes. Only 4 trips exceed 24 hours, but 1,839 trips last 23–24 hours. Vendor 2 accounts for 2,170 of the 2,253 trips longer than 120 minutes (96.3%). The notebook keeps trips of 1–120 minutes (1,447,796 rows, 99.26%).
- **Coordinates contain impossible values** for a NYC dataset (for example latitude 51.88 or longitude −121.93). Only 223 pickups fall outside a rough NYC bounding box, so this is rare, but the coordinates were **not used or filtered** because the analysis is time-based.
- **`passenger_count` = 0** appears in 60 rows (17 after the duration filter); these rows are kept since passenger count does not affect time-based demand.
- **The data covers six months only**, so seasonal conclusions are limited to January–June.
- **Only pickups are observed:** there is no information about unmet demand, fares or cancellations.

## Derived columns created in the notebook

| Column | Definition |
|---|---|
| `trip_duration_min` | `trip_duration / 60` |
| `hour` | Pickup hour, 0–23 |
| `weekday`, `weekday_name` | Day of week (0 = Monday), ordered day names |
| `month`, `month_name` | Pickup month (1–6), ordered month names |
| `is_weekend` | `True` for Saturday and Sunday |
| `date` | Pickup date (midnight timestamp), used for daily counts |

