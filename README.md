# ANKUSI Wheel Fitment Dataset

Open, machine-readable factory wheel specifications — bolt pattern, centre bore, lug count and OE wheel size — for **7,583 vehicle variants** across **130 makes** and **46 bolt patterns**, model years **1940–2027**.

Released under **CC BY 4.0**. Free to use, copy, adapt and build on, including commercially, as long as you credit the source.

**Canonical page:** https://ankusiwheels.com/wheel-fitment-dataset/

**Current version:** 2026.09.19b — see [Version history](#version-history-and-correction-log) below.

---

## Why this exists

Wheel fitment data is scattered across paywalled lookup sites, forum posts and PDFs. We maintain this table because we need it accurate for our own production, and an open version is more useful to everyone than another login wall.

## Files

Full data files are hosted on the canonical page so they always match the published guides:

| File | Format | Size | Download |
|---|---|---|---|
| `ankusi-wheel-fitment.csv` | CSV, UTF-8 with BOM | ~1.1 MB | https://ankusiwheels.com/wp-content/uploads/dataset/ankusi-wheel-fitment.csv |
| `ankusi-wheel-fitment.json` | JSON + metadata + changelog | ~2.4 MB | https://ankusiwheels.com/wp-content/uploads/dataset/ankusi-wheel-fitment.json |
| `CHANGELOG.json` | JSON | small | https://ankusiwheels.com/wp-content/uploads/dataset/CHANGELOG.json |

A 50-row sample lives in `data/sample.csv` so you can check the shape before downloading.

## Schema

| Field | Type | Description | Example |
|---|---|---|---|
| `make` | string | Vehicle manufacturer | `BMW` |
| `model` | string | Model name as published, including chassis code where one applies | `BMW 3 Series G20` |
| `year` | integer | Model year the record describes | `2025` |
| `chassis_code` | string | Manufacturer chassis / platform code where published | `G20` |
| `bolt_pattern` | string | Bolt circle: stud count × pitch circle diameter in mm | `5x112` |
| `center_bore_mm` | decimal | Hub bore diameter in millimetres (front hub where the axles differ) | `66.6` |
| `center_bore_rear_mm` | decimal | Rear hub bore in millimetres, filled only where the rear hub differs from the front (e.g. BMW E70/E71: 74.1 front / 72.6 rear; Honda S2000: 70.1 / 64.1); blank otherwise. Added in 2026.09.19 | `72.6` |
| `lug_count` | integer | Number of studs or bolts | `5` |
| `oe_wheel` | string | Factory wheel size and offset as published by the manufacturer | `18x8.0J at ET34 offset, front and rear` |
| `flush_wheel` | string | Commonly used flush-fitment reference size and offset | `19x8.5J ET35` |
| `max_wheel` | string | Maximum aggressive reference size and offset before clearance work | `19x9.0J ET28` |
| `source_url` | string | Canonical page the record is published on | `https://ankusiwheels.com/fitment-guide/bmw-3-series-g20-2025/` |

Empty strings mean "not published for this variant", not zero. Competition and centre-lock variants intentionally carry no `center_bore_mm`; a blank `center_bore_mm` on a road car means the figure is withheld pending verification (see the correction log).

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

## Version history and correction log

### 2026.09.19b

Expansion: 1,156 variants added after the same two-source verification (wheel-size.com centre-bore distribution pages plus avtoreference.com; 278 model groups confirmed by both, 42 confirmed by wheel-size.com only where avtoreference has no entry, 9 by avtoreference only). Added: Buick 462, Geely 415, Porsche 332 (classic 356 / 914 / 924 / 928 / 944 / 968 and current 911 / 718 / Cayenne / Macan / Panamera / Taycan families), Rolls-Royce 69, Lamborghini 10, plus smaller additions across other makes.

Corrected before inclusion (7): Porsche Macan Electric 2025-2026 (Macan 4 / 4S / Turbo / GTS / base) — source pages carried 5x112; verified 5x130 / 66.5 against wheel-size.com, avtoreference.com and an aftermarket wheel supplier listing.

Centre bore withheld pending verification (13): Buick Cascada 2016-2019 (sources disagree, 70.2 vs 70.3); Geely Vision FC2 / FC3 2014-2022 (source page lists both 54.1 and 60.1).

Not included (29): 21 single-source records (Buick Rendezvous 2002-2007, Buick Velite 5 / 6 / 7) held until a second reference is found; 8 racing / bespoke centre-lock builds with no road bolt pattern. 120 centre-lock and competition variants remain outside the dataset by design.

Record count 6,427 → 7,583; makes 128 → 130.

### 2026.09.19

Anomaly audit of 190 flagged records (39 rare bolt-pattern / centre-bore combinations, 166 brand outliers, 12 blank centre bores), each re-checked against at least two of: wheel-size.com, wheelfitment.eu, wheel-sizes.com, avtoreference.com and marque owner forums.

Corrected (9):

| Vehicle | Field | Was | Now |
|---|---|---|---|
| Alfa Romeo 8C Spider (2009) | bolt_pattern / center_bore_mm | 5x114.3 / 70.5 | 5x110 / 65.1 |
| Chrysler Delta (2012) | bolt_pattern / center_bore_mm | 4x100 / 56.6 | 4x98 / 58.1 |
| Daihatsu Hijet (1997) | center_bore_mm | 66 | 66.1 |
| Opel Rocks-e (2022) | bolt_pattern / center_bore_mm | 4x100 / 60.1 | 4x108 / 65.1 |
| Peugeot Landtrek (2025) | center_bore_mm | 100.1 | 106.1 |
| Peugeot Pick Up (2018) | center_bore_mm | 100.1 | 108.1 |
| Vauxhall Insignia Country Tourer (2018) | center_bore_mm | 70.1 | 70.2 |
| Vauxhall Insignia Sports Tourer (2018) | center_bore_mm | 70.1 | 70.2 |
| Volvo EM90 (2024) | center_bore_mm | 63.3 | 63.4 |

New column `center_bore_rear_mm` for the 9 records with a staggered hub bore (BMW X5 M E70 2010; BMW X6 E71 2009/2013, X6 E71 [UK] 2009, X6 ActiveHybrid E71 2010, X6 M E71 2010, X6 M50d E71 2013; Hamann BMW X6 2009; Honda S2000 2008-2009). These previously exported with a blank centre bore.

Centre bore withheld pending verification (8): Ford E-Series 2008; Hummer Humvee 2003; Lamborghini Jalpa 1981; Lamborghini Temerario 2025 and Temerario Ad Personam 2026; Lotus Esprit V8 2002; Oldsmobile Cutlass 1961; Porsche 914-4 1970.

Records withdrawn pending verification (9): Alfa Romeo TZ3 Stradale 2011; Cadillac Celestiq 2024; Iveco Campagnola 2009; Iveco Massif 2008; Renault 4 CV 1948 / 4 CV Luxe 1950 / 4 CV Sport 1954; Renault Dauphine 1961; Renault Floride 1960.

Record count 6,436 → 6,427.

### 2026.09.18

Initial public release, 6,436 records.

## Attribution

CC BY 4.0 requires appropriate credit. A visible credit with a link back:

```
Wheel fitment data: ANKUSI Wheel Fitment Dataset (CC BY 4.0)
https://ankusiwheels.com/wheel-fitment-dataset/
```

Structured citation: see `CITATION.cff`.

## Corrections

Found a record that does not match a factory placard or service manual? Open an issue with the vehicle, the model year and your source. Corrections are applied to both the dataset and the underlying guide, and logged above.

## Mirrors

The same dataset, published elsewhere for convenience:

- Canonical page and downloads: https://ankusiwheels.com/wheel-fitment-dataset/
- Hugging Face (dataset viewer, `datasets` loader): https://huggingface.co/datasets/hjhjhihg1/ankusi-wheel-fitment-data
- Kaggle (column documentation, notebook-ready): https://www.kaggle.com/datasets/ankusiwheelsdavid/wheel-bolt-pattern-and-centre-bore-6436-cars
- figshare (permanent DOI, citable in papers): https://doi.org/10.6084/m9.figshare.33921994
- Wikidata item: https://www.wikidata.org/wiki/Q141498663

## Who maintains this

The engineering team at [ANKUSI Wheels](https://ankusiwheels.com/wholesale/). ANKUSI is the brand and export arm; the wheels are produced at Taizhou Hantuo Automotive Parts Co., Ltd (Jiangsu, China), which holds IATF 16949:2016 (TUV Saarland, certificate CB01839, IATF registration 0572069) and ISO 9001:2015, scope "design and manufacture of wheel hubs, with product design responsibility".

## Licence

[Creative Commons Attribution 4.0 International](LICENSE) (CC BY 4.0).
