# ANKUSI Wheel Fitment Dataset

Open, machine-readable factory wheel specifications — bolt pattern, centre bore, lug count and OE wheel size — for **6,436 vehicle variants** across **129 makes** and **46 bolt patterns**, model years **1940–2027**.

Released under **CC BY 4.0**. Free to use, copy, adapt and build on, including commercially, as long as you credit the source.

**Canonical page:** https://ankusiwheels.com/wheel-fitment-dataset/

---

## Why this exists

Wheel fitment data is scattered across paywalled lookup sites, forum posts and PDFs. We maintain this table because we need it accurate for our own production, and an open version is more useful to everyone than another login wall.

## Files

Full data files are hosted on the canonical page so they always match the published guides:

| File | Format | Size | Download |
|---|---|---|---|
| `ankusi-wheel-fitment.csv` | CSV, UTF-8 with BOM | ~926 KB | https://ankusiwheels.com/wp-content/uploads/dataset/ankusi-wheel-fitment.csv |
| `ankusi-wheel-fitment.json` | JSON + metadata | ~2.9 MB | https://ankusiwheels.com/wp-content/uploads/dataset/ankusi-wheel-fitment.json |

A 50-row sample lives in `data/sample.csv` so you can check the shape before downloading.

## Schema

| Field | Type | Description | Example |
|---|---|---|---|
| `make` | string | Vehicle manufacturer | `BMW` |
| `model` | string | Model name as published, including chassis code where one applies | `BMW 3 Series G20` |
| `year` | integer | Model year the record describes | `2025` |
| `chassis_code` | string | Manufacturer chassis / platform code where published | `G20` |
| `bolt_pattern` | string | Bolt circle: stud count × pitch circle diameter in mm | `5x112` |
| `center_bore_mm` | decimal | Hub bore diameter in millimetres | `66.6` |
| `lug_count` | integer | Number of studs or bolts | `5` |
| `oe_wheel` | string | Factory wheel size and offset as published by the manufacturer | `18x8.0J at ET34 offset, front and rear` |
| `flush_wheel` | string | Commonly used flush-fitment reference size and offset | `19x8.5J ET35` |
| `max_wheel` | string | Maximum aggressive reference size and offset before clearance work | `19x9.0J ET28` |
| `source_url` | string | Canonical page the record is published on | `https://ankusiwheels.com/fitment-guide/bmw-3-series-g20-2025/` |

Empty strings mean "not published for this variant", not zero. Competition and centre-lock variants intentionally carry no `center_bore_mm`.

## Quick start

```python
import pandas as pd

url = "https://ankusiwheels.com/wp-content/uploads/dataset/ankusi-wheel-fitment.csv"
df = pd.read_csv(url)

# every vehicle sharing a bolt pattern
df[df.bolt_pattern == "5x112"][["make", "model", "year", "center_bore_mm"]]

# bolt pattern distribution
df.bolt_pattern.value_counts().head(10)
```

## How the data is compiled

Records are built from published OEM wheel and tyre specifications and cross-checked against independent fitment references before publication. Where two sources disagree, the record is held back rather than guessed.

`flush_wheel` and `max_wheel` are reference starting points collected from documented builds on each platform — they are **not** manufacturer approvals. Clearance always has to be verified on the individual car: brake package, suspension, tyre profile and ride height all change the outcome.

## Attribution

CC BY 4.0 requires appropriate credit. A visible credit with a link back:

```
Wheel fitment data: ANKUSI Wheel Fitment Dataset (CC BY 4.0)
https://ankusiwheels.com/wheel-fitment-dataset/
```

Structured citation: see `CITATION.cff`.

## Corrections

Found a record that does not match a factory placard or service manual? Open an issue with the vehicle, the model year and your source. Corrections are applied to both the dataset and the underlying guide.

## Who maintains this

The engineering team at [ANKUSI Wheels](https://ankusiwheels.com/wholesale/), an IATF 16949 and ISO 9001 certified forged wheel manufacturer in Guangzhou, China.

## Licence

[Creative Commons Attribution 4.0 International](LICENSE) (CC BY 4.0).
