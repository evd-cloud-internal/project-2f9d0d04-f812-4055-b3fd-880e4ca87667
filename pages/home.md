---
name: Home
assetId: 6e867b01-5d88-49d9-b8bf-20cee39d59b6
type: page
---

# 🏥 ServiceOS Admin Console — Real-Time Operations
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