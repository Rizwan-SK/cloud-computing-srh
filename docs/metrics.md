## Carriers on the lanes that miss

On time = arrived no more than 24 hours late. Carrier names: ATL Atlas Ocean, BLC Blue Crest Shipping, HRZ Horizon Container Line, KST Kestrel Maritime, NWL Northwind Lines, ORS Orion Sea Freight. The air carrier codes (FAL, SKY, MER, AQL) are shown as codes.

### SGSIN → BEANR (Asia-North Europe, ocean): 44.4% on time, target 65%

| Carrier | Shipments | Avg delay (h) | On time |
|---|---:|---:|---:|
| ATL | 2 | 132.0 | 0.0% |
| ORS | 4 | 91.5 | 25.0% |
| KST | 4 | 51.0 | 25.0% |
| BLC | 3 | 82.0 | 33.3% |
| HRZ | 1 | 0.0 | 100.0% |
| NWL | 4 | 10.5 | 100.0% |

### NLRTM → USHOU (Transatlantic, ocean): 50.0% on time, target 65%

| Carrier | Shipments | Avg delay (h) | On time |
|---|---:|---:|---:|
| ATL | 1 | 156.0 | 0.0% |
| KST | 2 | 72.0 | 0.0% |
| ORS | 5 | 76.8 | 20.0% |
| BLC | 3 | 40.0 | 33.3% |
| NWL | 3 | 24.0 | 100.0% |
| HRZ | 4 | 0.0 | 100.0% |

### CNNGB → DEHAM (Asia-North Europe, ocean): 60.0% on time, target 65%

| Carrier | Shipments | Avg delay (h) | On time |
|---|---:|---:|---:|
| ATL | 3 | 96.0 | 0.0% |
| NWL | 6 | 82.0 | 33.3% |
| KST | 4 | 30.0 | 50.0% |
| ORS | 4 | 42.0 | 75.0% |
| BLC | 6 | 0.0 | 100.0% |
| HRZ | 2 | 24.0 | 100.0% |

### DXB → FRA (Middle East-Europe, air): 93.3% on time, target 95%

| Carrier | Shipments | Avg delay (h) | On time |
|---|---:|---:|---:|
| FAL | 6 | 4.3 | 83.3% |
| SKY | 15 | 1.7 | 93.3% |
| MER | 4 | 0.0 | 100.0% |
| AQL | 5 | 0.4 | 100.0% |

### The three failing ocean lanes combined (61 shipments, 32 on time)

| Carrier | Shipments | On time |
|---|---:|---:|
| HRZ | 7 | 100.0% (7 of 7) |
| NWL | 13 | 69.2% (9 of 13) |
| BLC | 12 | 66.7% (8 of 12) |
| ORS | 13 | 38.5% (5 of 13) |
| KST | 10 | 30.0% (3 of 10) |
| ATL | 6 | 0.0% (0 of 6) |

## Conclusions

- **Atlas (ATL) was late on every shipment on the three failing ocean lanes** (0 of 6), with delays of 4 to 6.5 days. It was fine on CNSHA → ITGOA (80%), so its problem is the lanes, not the carrier as a whole.
- **Kestrel (KST) and Orion (ORS)** were also weak on these lanes (30% and 38.5%). Orion was perfect on CNSHA → ITGOA, which is the one ocean lane that meets the target.
- **Horizon (HRZ) was on time on all 7 shipments** on these lanes. Blue Crest and Northwind are in between, and each is strong on some lanes and weak on others.
- **DXB → FRA misses by two late shipments out of 30,** one on FAL and one on SKY. MER and AQL were never late. Air is otherwise healthy.
- **Caveat:** several cells have 1 to 4 shipments, so a single late sailing swings the percentage a lot. Treat this as evidence to take into negotiations, not proof.

## What this means for the platform

The Ops view should show on-time rate **per lane and per carrier**, so procurement can see which carrier is dragging a lane down. For next quarter's negotiations, the questions are whether to shift volume from ATL, KST and ORS to HRZ on SGSIN → BEANR, NLRTM → USHOU and CNNGB → DEHAM, and what premium that costs.
