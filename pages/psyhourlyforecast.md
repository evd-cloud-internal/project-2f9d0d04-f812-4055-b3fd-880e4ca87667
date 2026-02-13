---
name: psyhourlyforecast
assetId: fc938958-4e55-4594-84b2-38554d4f4fbe
type: sql
---

-- new_query
-- Write your SQL query here
select
  date_trunc('hour', date) as hour,
  sum(demand) as total_demand
from forecast
where provider_role = 'psy'
  and date >= '2026-02-13 00:00:00'
  and date <  '2026-02-13 12:00:00'
group by 1
order by 1