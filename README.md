# ANKUSI Wheel Fitment Dataset

Open, machine-readable factory wheel specifications — bolt pattern, centre bore, lug count and OE wheel size — for **9,688 vehicle variants** across **167 makes** and **52 bolt patterns**, model years **1940–2027**.

Released under **CC BY 4.0**. Free to use, copy, adapt and build on, including commercially, as long as you credit the source.

**Canonical page:** https://ankusiwheels.com/wheel-fitment-dataset/

**Current version:** 2026.09.22 — see [Version history](#version-history-and-correction-log) below. below.

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

| `source_lang` | string | Which language edition the record was taken from: `en`, `ru`, `th`, `fr`, `ar`, `es`. English is canonical; a language edition contributes a vehicle only where the English one has no page for it. Added in 2026.09.22 | `ru` |
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

### 2026.09.22

Expansion to the multilingual guides. Until now the dataset was built only from the English pages; this release adds the vehicles that exist only on the Russian, Thai, French, Arabic and Spanish editions — Lada, UAZ, Moskvich, Tenet, Belgee and Solaris for Russia and the CIS, the Thai pickup and body-on-frame range, Gulf and Latin-market variants, and recent Chinese export models.

New column `source_lang` records which edition each record came from. English stays canonical: a language edition contributes a vehicle only where the English pages have none, so the same car is not published twice under two spellings. Matching normalises chassis codes and translated range words, so `Toyota Camry XV70` and `Camry`, or `BMW Serie 1 F20` and `BMW 1 Series F20`, resolve to one vehicle.

Factory wheel size corrected (45 records): a source row can carry a width no wheel of that diameter is made in — 19x5.5J on a Chery Tiggo 7 Pro, 18x5.5J on a Geely Coolray, 21x7J on a Hongqi H9. Each was re-derived from the other listings for the same bolt pattern and centre bore, which describe the same vehicle under a different market's entry. One record with no plausible figure in any listing now carries no factory size rather than a wrong one.

Offsets: where a source writes ET0 because the manufacturer never published an offset, the record is left blank instead. A zero offset on a front-wheel-drive hatchback is not a specification.

Record count 7,603 → 9,688; makes 130 → 167; bolt patterns 46 → 52. By edition: en 7,918, ru 714, th 473, es 266, fr 206, ar 111.

### 2026.09.20

Consistency pass over the full dataset: flush/maximum offset ordering, model-year ranges and factory wheel sizes were checked programmatically, and every flag was re-verified against wheel-size.com and avtoreference.com before a change was made.

Factory wheel size corrected (20 records): Lamborghini Murciélago, all 9 variants 2002–2010 (F 18x8.5J ET42 / R 18x13J ET40 → F ET58 / R ET15; the ET42 figure belonged to the Gallardo); Lamborghini Reventón and Reventón Roadster (→ F 18x8.5J ET60 / R 18x13J ET15); Mercedes-AMG GT S F1 and DTM safety cars (F 19x9J ET35 / R 20x11J ET40 → F ET58 / R ET68); Mercury Marauder 2003 (F 18x8J ET6.35 / R 18x9.5J ET6.35 → 18x8J ET50 front and rear); Ford Taurus SHO 2013 (20x8J ET55 → ET39); Rover 100 1994 (13x4.5J ET35 → ET52); Nissan Titan 2017/2020, Titan King Cab 2017 and Titan Single Cab 2017 (ET44 → 20x8.0J ET23 / 18x8.0J ET25). The aftermarket flush and maximum references on these 20 records were written against the old offset and are blank in this release pending re-verification.

Model-year range corrected (7): Honda e 2006–2023 → 2020–2023; Honda HR-V 2008–2019 → 2015–2022; Honda Prelude 1997–2008 → 1997–2001; Honda Accord Euro R 1998–2004 → 2002–2008; Lexus NX 2010–2025 → 2015–2025; Lexus RC F Sport 2013–2026 → 2015–2025; Lincoln MKZ 2013–2022 → 2013–2020.

Flush and maximum references swapped (4): Scion xB 2008; Porsche Boxster S 2001, 2002, 2003 (the "maximum" set sat further inboard than the "flush" set).

Centre bore reinstated (13): Buick Cascada 2016–2019 (70.3 mm, confirmed by wheel-size.com and wheel-sizes.com after one reference listed 70.2); Geely Vision FC2 2014–2018 and FC3 2019–2022 (60.1 mm — the earlier "conflict" came from an automated lookup that matched the wrong Geely model, Vision X3).

Added (20): Buick Rendezvous 2002–2007 (second reference: wheel-sizes.com), Buick Velite 5 2017–2018, Velite 6 2019–2026 and Velite 7 2020–2023 (second reference: wheel-size.com).

Still withheld: the 8 centre bores and 9 withdrawn records from 2026.09.19; Buick Rendezvous 2001 (model year not confirmed).

Record count 7,583 → 7,603.

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
