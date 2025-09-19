here’s a compact ASCII explainer you can paste into a README / slide note. it shows how a **W→kWh** meter works on 15-minute intervals, and how the same hour looks when you sum those quarters. 👇

```
               HOW 15-MIN INTERVAL ENERGY IS METERED

Power (kW)
 2.5 |                  ████
 2.0 |                  ████
 1.5 |                               ███
 1.0 | ███                                      ███
 0.5 |       █
     +--------+--------+--------+--------+------------------> time
       00–15    15–30    30–45    45–60          (minutes)
           each interval = 0.25 h (15 min)

Power (kW)
 2.5 |                  
 2.0 |                  
 1.5 |                               
 1.0 | ███                                      
 0.5 |       █
     +--------+--------+--------+--------+------------------> time
       00–15    15–30    30–45    45–60          (minutes)
           each interval = 0.25 h (15 min)



The smart meter integrates power over each 15-min interval, producing ENERGY per interval:
    kWh_step = (average kW in the interval) × 0.25 h

It also keeps a CUMULATIVE register that increments by each kWh_step.
```

### tiny worked example (one hour)

Let’s say the average power in each quarter-hour is:

* 00:00–00:15 → **0.8 kW**
* 00:15–00:30 → **2.4 kW**
* 00:30–00:45 → **0.5 kW**
* 00:45–01:00 → **1.3 kW**

Per-interval energy is just `kW × 0.25 h`:

```
15-min table (what the meter actually records)
┌───────────────┬────────────┬─────────────┬────────────────────┐
│ Interval      │ Avg P (kW) │ kWh in slot │ Cumulative register │
├───────────────┼────────────┼─────────────┼────────────────────┤
│ 00:00–00:15   │   0.8      │ 0.200       │ 12000.200 kWh       │
│ 00:15–00:30   │   2.4      │ 0.600       │ 12000.800 kWh       │
│ 00:30–00:45   │   0.5      │ 0.125       │ 12000.925 kWh       │
│ 00:45–01:00   │   1.3      │ 0.325       │ 12001.250 kWh       │
└───────────────┴────────────┴─────────────┴────────────────────┘
                              sum of kWh in the hour  = 1.250 kWh
```

If you **aggregate to hours**, you’re just summing those four 15-min kWh values:

```
Hourly view (aggregated from the same data)
┌───────────────┬──────────────┬──────────────┐
│ Hour          │ Energy (kWh) │ Avg P (kW)   │
├───────────────┼──────────────┼──────────────┤
│ 00:00–01:00   │    1.250     │ 1.250 / 1 h  │
│               │              │   = 1.25 kW  │
└───────────────┴──────────────┴──────────────┘
```

### billing when both use kWh

* **Energy charge** is the **sum of kWh** over the billing period.
  Whether you total at 15-min or hourly resolution, the energy **is identical** (ignoring rounding).
* **TOU prices?** You multiply each slot’s kWh by the **price active in that slot**, then sum.
  (Same idea—just keep the correct price per interval.)
* **Demand charges** (if any) are based on **max average kW over the billing interval** (often 15-min).
  That’s separate from the kWh energy charge.

### quick formula cheat-sheet

```
kWh_step = P̄_step(kW) × dt_h
P̄_step(kW) = kWh_step / dt_h
Hourly kWh  = Σ (four 15-min kWh)
Bill kWh    = Σ (all interval kWh in billing period)
```

If you want, I can generate a little CSV (15-min vs hourly) from your own numbers so students can see the equality in their data.
