---
name: Home
assetId: 6e867b01-5d88-49d9-b8bf-20cee39d59b6
type: page
---

{% row
        card=false
        %}
# 🏥 Fresh Real-Time Operational Data
{% clock /%}
{% /row %}

## 🚀 Demand Management

{% row
        card=false
        %}
```sql demand_now
SELECT 30 AS demand_requests, 35 AS forecasted_volume
```
{% big_value
  data="demand_now"
  value="demand_requests"
  title="Demand for Primary Care"
  fmt="#,##0"
  comparison={
    compare_vs="target"
    target="forecasted_volume"
    display_type="abs"
    text="vs. forecast"
    down_is_good=true
  }
/%}
```sql demand_vs_forecast
SELECT
    toDateTime('2025-01-15 05:00:00') + INTERVAL number HOUR AS hour,
    arrayElement([5, 3, 2, 2, 4, 8, 15, 22, 30, 35, 38, 40, 35, 32, 28, 25, 20, 18, 15, 12, 10, 8, 6, 5], (number % 24) + 1) AS actual_demand
FROM numbers(24)
ORDER BY hour
```
```sql forecast_bands
SELECT
    toDateTime('2025-01-15 05:00:00') + INTERVAL number HOUR AS hour_start,
    toDateTime('2025-01-15 05:00:00') + INTERVAL (number + 1) HOUR AS hour_end,
    round(arrayElement([6, 4, 3, 3, 5, 10, 18, 25, 32, 38, 40, 42, 38, 35, 30, 27, 22, 20, 16, 13, 11, 9, 7, 6], (number % 24) + 1) * 0.8) AS forecast_lower,
    round(arrayElement([6, 4, 3, 3, 5, 10, 18, 25, 32, 38, 40, 42, 38, 35, 30, 27, 22, 20, 16, 13, 11, 9, 7, 6], (number % 24) + 1) * 1.2) AS forecast_upper
FROM numbers(24)
ORDER BY hour_start
```
{% combo_chart
  data="demand_vs_forecast"
  x="hour"
  title="Demand vs Forecast — MD/NP Appointment"
  subtitle="Hourly comparison — orange bars show 80% confidence interval on forecast"
  x_fmt="HH:mm"
  y_fmt="#,##0"
%}
    {% line y="actual_demand" options={color="#0077cc" width=2 step="start" markers={shape="circle" size=4}} /%}
    {% reference_area
        data="forecast_bands"
        x_min="hour_start"
        x_max="hour_end"
        y_min="forecast_lower"
        y_max="forecast_upper"
        color="#ff6b35"
    /%}
{% /combo_chart %}

{% /row %}


## 📋 Wait Time

{% row
        card=false
        %}
```sql active_queues
SELECT queue, episodes_waiting, avg_wait_minutes
FROM (
    SELECT 'NC' AS queue, 18 AS episodes_waiting, 45 AS avg_wait_minutes, 1 AS sort_order
    UNION ALL SELECT 'CC', 12, 72, 2
)
ORDER BY sort_order
```
{% heat_grid
  data="active_queues"
  dimension="queue"
  value="avg(avg_wait_minutes)"
  thresholds=[45, 60]
  units="MIN"
  lower_is_better=true
  title="Average Wait Time by Queue"
/%}


```sql capacity_wait
SELECT province, specialization, avg_wait_minutes, provider_count
FROM (
    SELECT 'QC' AS province, 'MD/NP' AS specialization, 42 AS avg_wait_minutes, 12 AS provider_count
    UNION ALL SELECT 'QC', 'RN', 28, 10
    UNION ALL SELECT 'QC', 'MHS', 35, 8
    UNION ALL SELECT 'QC', 'Psy', 55, 5
    UNION ALL SELECT 'QC', 'MHT', 40, 6
    UNION ALL SELECT 'QC', 'CO', 25, 8
    UNION ALL SELECT 'QC', 'FP', 48, 4
    UNION ALL SELECT 'QC', 'Nutri', 42, 4
    UNION ALL SELECT 'ON', 'MD/NP', 55, 8
    UNION ALL SELECT 'ON', 'RN', 42, 6
    UNION ALL SELECT 'ON', 'MHS', 62, 6
    UNION ALL SELECT 'ON', 'Psy', 85, 4
    UNION ALL SELECT 'ON', 'MHT', 58, 5
    UNION ALL SELECT 'ON', 'CO', 35, 7
    UNION ALL SELECT 'ON', 'FP', 72, 3
    UNION ALL SELECT 'ON', 'Nutri', 68, 3
    UNION ALL SELECT 'BC', 'MD/NP', 68, 5
    UNION ALL SELECT 'BC', 'RN', 52, 4
    UNION ALL SELECT 'BC', 'MHS', 88, 3
    UNION ALL SELECT 'BC', 'Psy', 95, 2
    UNION ALL SELECT 'BC', 'MHT', 62, 3
    UNION ALL SELECT 'BC', 'CO', 38, 4
    UNION ALL SELECT 'BC', 'FP', 75, 2
    UNION ALL SELECT 'BC', 'Nutri', 70, 2
    UNION ALL SELECT 'AB', 'MD/NP', 95, 3
    UNION ALL SELECT 'AB', 'RN', 78, 2
    UNION ALL SELECT 'AB', 'MHS', 105, 2
    UNION ALL SELECT 'AB', 'Psy', 120, 1
    UNION ALL SELECT 'AB', 'MHT', 85, 2
    UNION ALL SELECT 'AB', 'CO', 45, 3
    UNION ALL SELECT 'AB', 'FP', 110, 1
    UNION ALL SELECT 'AB', 'Nutri', 92, 1
    UNION ALL SELECT 'NS', 'MD/NP', 38, 4
    UNION ALL SELECT 'NS', 'RN', 32, 3
    UNION ALL SELECT 'NS', 'MHS', 45, 3
    UNION ALL SELECT 'NS', 'Psy', 75, 2
    UNION ALL SELECT 'NS', 'MHT', 55, 2
    UNION ALL SELECT 'NS', 'CO', 30, 3
    UNION ALL SELECT 'NS', 'FP', 65, 2
    UNION ALL SELECT 'NS', 'Nutri', 58, 2
    UNION ALL SELECT 'MB', 'MD/NP', 72, 2
    UNION ALL SELECT 'MB', 'RN', 65, 2
    UNION ALL SELECT 'MB', 'MHS', 78, 1
    UNION ALL SELECT 'MB', 'Psy', 130, 1
    UNION ALL SELECT 'MB', 'MHT', 88, 1
    UNION ALL SELECT 'MB', 'CO', 55, 2
    UNION ALL SELECT 'MB', 'FP', 98, 1
    UNION ALL SELECT 'MB', 'Nutri', 105, 1
    UNION ALL SELECT 'NB', 'MD/NP', 82, 2
    UNION ALL SELECT 'NB', 'RN', 58, 2
    UNION ALL SELECT 'NB', 'MHS', 92, 1
    UNION ALL SELECT 'NB', 'Psy', 140, 1
    UNION ALL SELECT 'NB', 'MHT', 95, 1
    UNION ALL SELECT 'NB', 'CO', 48, 2
    UNION ALL SELECT 'NB', 'FP', 115, 1
    UNION ALL SELECT 'NB', 'Nutri', 110, 1
    UNION ALL SELECT 'NL', 'MD/NP', 88, 2
    UNION ALL SELECT 'NL', 'RN', 62, 2
    UNION ALL SELECT 'NL', 'MHS', 98, 1
    UNION ALL SELECT 'NL', 'Psy', 145, 1
    UNION ALL SELECT 'NL', 'MHT', 100, 1
    UNION ALL SELECT 'NL', 'CO', 52, 1
    UNION ALL SELECT 'NL', 'FP', 120, 1
    UNION ALL SELECT 'NL', 'Nutri', 115, 1
    UNION ALL SELECT 'PE', 'MD/NP', 78, 1
    UNION ALL SELECT 'PE', 'RN', 55, 1
    UNION ALL SELECT 'PE', 'MHS', 95, 1
    UNION ALL SELECT 'PE', 'Psy', 150, 1
    UNION ALL SELECT 'PE', 'MHT', 102, 1
    UNION ALL SELECT 'PE', 'CO', 58, 1
    UNION ALL SELECT 'PE', 'FP', 125, 1
    UNION ALL SELECT 'PE', 'Nutri', 118, 1
    UNION ALL SELECT 'SK', 'MD/NP', 85, 2
    UNION ALL SELECT 'SK', 'RN', 68, 2
    UNION ALL SELECT 'SK', 'MHS', 92, 1
    UNION ALL SELECT 'SK', 'Psy', 135, 1
    UNION ALL SELECT 'SK', 'MHT', 90, 1
    UNION ALL SELECT 'SK', 'CO', 50, 2
    UNION ALL SELECT 'SK', 'FP', 108, 1
    UNION ALL SELECT 'SK', 'Nutri', 100, 1
    UNION ALL SELECT 'NT', 'MD/NP', 120, 1
    UNION ALL SELECT 'NT', 'RN', 95, 1
    UNION ALL SELECT 'NT', 'MHS', 140, 1
    UNION ALL SELECT 'NT', 'Psy', 180, 0
    UNION ALL SELECT 'NT', 'MHT', 135, 1
    UNION ALL SELECT 'NT', 'CO', 75, 1
    UNION ALL SELECT 'NT', 'FP', 155, 0
    UNION ALL SELECT 'NT', 'Nutri', 160, 0
    UNION ALL SELECT 'NU', 'MD/NP', 150, 1
    UNION ALL SELECT 'NU', 'RN', 120, 1
    UNION ALL SELECT 'NU', 'MHS', 170, 0
    UNION ALL SELECT 'NU', 'Psy', 200, 0
    UNION ALL SELECT 'NU', 'MHT', 165, 0
    UNION ALL SELECT 'NU', 'CO', 95, 1
    UNION ALL SELECT 'NU', 'FP', 180, 0
    UNION ALL SELECT 'NU', 'Nutri', 185, 0
    UNION ALL SELECT 'YT', 'MD/NP', 110, 1
    UNION ALL SELECT 'YT', 'RN', 88, 1
    UNION ALL SELECT 'YT', 'MHS', 130, 1
    UNION ALL SELECT 'YT', 'Psy', 170, 0
    UNION ALL SELECT 'YT', 'MHT', 125, 1
    UNION ALL SELECT 'YT', 'CO', 68, 1
    UNION ALL SELECT 'YT', 'FP', 145, 0
    UNION ALL SELECT 'YT', 'Nutri', 150, 0
)
ORDER BY province, specialization
```
{% heatmap
  data="capacity_wait"
  x="specialization"
  y="province"
  value="avg(avg_wait_minutes)"
  value_fmt="#,##0' min'"
  title="Time to First Appointment by Province & Specialization"
  subtitle="Green < 60 min | Orange 60–90 min | Red > 90 min"
  chart_options={
    color_palette=["#38a169", "#38a169", "#dd6b20", "#dd6b20", "#e53e3e", "#e53e3e"]
  }
/%}

{% /row %}



## ⭐ Quality of Care
{% row
        card=false
        %}


```sql consultation_score
SELECT 4.3 AS avg_score, 127 AS total_reviews
```
{% big_value
  data="consultation_score"
  value="avg_score"
  title="Consultation Score (Today)"
  fmt="#,##0.0' / 5 ⭐'"
/%}


```sql care_path
SELECT stage, members
FROM (
    SELECT 'Demand Request' AS stage, 150 AS members, 1 AS sort_order
    UNION ALL SELECT 'Appointment Booked', 120, 2
    UNION ALL SELECT 'Consultation Started', 105, 3
    UNION ALL SELECT 'Clinical Outcome', 89, 4
)
ORDER BY sort_order
```
{% funnel_chart
  data="care_path"
  category="stage"
  value="sum(members)"
  title="Care Path Completion"
  subtitle="Member drop-off through the care journey (today)"
  show_percent=true
  label_position="inside"
  value_fmt="#,##0"
/%}


```sql incidents_now
SELECT 10 AS app_crashes, 15 AS crash_threshold, 2 AS support_tickets, 5 AS ticket_threshold
```

{% big_value
  data="incidents_now"
  value="app_crashes"
  title="App Crashes (Last Hour)"
  fmt="#,##0"
  comparison={
    compare_vs="target"
    target="crash_threshold"
    display_type="abs"
    text="vs. healthy threshold (15)"
    down_is_good=true
  }
/%}

{% big_value
  data="incidents_now"
  value="support_tickets"
  title="Support Tickets (Last Hour)"
  fmt="#,##0"
  comparison={
    compare_vs="target"
    target="ticket_threshold"
    display_type="abs"
    text="vs. healthy threshold (5)"
    down_is_good=true
  }
/%}

```sql incidents_trend
SELECT
    toDateTime('2025-01-15 00:00:00') + INTERVAL number HOUR AS hour,
    'App Crashes' AS metric,
    arrayElement([0, 0, 0, 0, 0, 0, 0, 0, 0, 1, 0, 0, 0, 0, 0, 0, 0, 1, 0, 0, 0, 0, 0, 0], number + 1) AS incidents
FROM numbers(24)

UNION ALL

SELECT
    toDateTime('2025-01-15 00:00:00') + INTERVAL number HOUR AS hour,
    'Support Tickets' AS metric,
    arrayElement([0, 0, 0, 0, 0, 0, 0, 1, 2, 1, 3, 2, 1, 2, 3, 1, 2, 1, 0, 1, 0, 0, 0, 0], number + 1) AS incidents
FROM numbers(24)

ORDER BY hour, metric
```

{% bar_chart
  data="incidents_trend"
  x="hour"
  y="sum(incidents)"
  series="metric"
  title="Support & Stability Overhead"
  subtitle="Hourly incident count (midnight–midnight EST) vs. healthy thresholds"
  x_fmt="HH:mm"
  y_fmt="#,##0"
  chart_options={
    series_colors={
      "App Crashes"="#e53e3e"
      "Support Tickets"="#3182ce"
    }
  }
%}
    {% reference_line
        y=15
        label="Crash Threshold"
        color="#e53e3e"
        line_options={
            type="dashed"
            width=2
        }
        label_options={
            position="below_start"
        }
    /%}
    {% reference_line
        y=5
        label="Ticket Threshold"
        color="#3182ce"
        line_options={
            type="dashed"
            width=2
        }
        label_options={
            position="above_start"
        }
    /%}
{% /bar_chart %}

{% /row %}