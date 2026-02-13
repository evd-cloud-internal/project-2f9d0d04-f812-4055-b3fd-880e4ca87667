---
name: Home
assetId: 6e867b01-5d88-49d9-b8bf-20cee39d59b6
type: page
---

# 🏥 Fresh Real-Time Operational Data
{% clock /%}

_Near real-time operational metrics for Primary Care_
## 🚀 Demand Management

## 🩺 Wait Times — Next Available Appointment by Province

## 📋 Active Queues

## ⭐ Quality of Care

```sql consultation_score
SELECT 4.3 AS avg_score, 127 AS total_reviews
```
{% big_value
  data="consultation_score"
  value="avg_score"
  title="Consultation Score (Today)"
  fmt="#,##0.0' / 5 ⭐'"
/%}

## 🛤️ Journey Continuity

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


## 🛡️ System & Support Health

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