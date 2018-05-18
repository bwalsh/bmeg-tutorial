---
title: Kaplan Meier Curves
menu:
  main:
    parent: Queries
    weight: 4
---

# Make Kaplan Meier curves using TCGA data

```python
from lifelines import KaplanMeierFitter
import pandas
import aql
conn = aql.Connection("http://arachne.compbio.ohsu.edu")
O = conn.graph("bmeg")

q = O.query().V().where(aql.eq("_label", "Individual"))
q = q.where(aql.and_(aql.eq("source", "tcga"), aql.eq("disease_code", "BRCA")))
q = q.where(aql.eq("vital_status", "Dead"))

q1 = q.where(aql.eq('her2_status_by_ihc', 'Positive')).render(["death_days_to"])
q2 = q.where(aql.eq('her2_status_by_ihc', 'Negative')).render(["death_days_to"])

days_a = list(int(a[0]) for a in q1)
days_b = list(int(a[0]) for a in q2)

kmf = KaplanMeierFitter()
kmf.fit(days_a, label="HER2 Positive")
ax = kmf.plot()
kmf.fit(days_b, label="HER2 Negative")
kmf.plot(ax=ax)
```

Returns

![HER2 KM Curve](/img/her2_km.png)