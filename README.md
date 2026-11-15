# Indonesia Power Map · Data Collection

> Data collection sistem tenaga listrik Indonesia untuk pemodelan: pembangkit, gardu induk, dan transmisi
> yang beroperasi hari ini, beserta seluruh rencana RUPTL 2025–2034. Tersedia dalam CSV dan GeoJSON.

**Versi:** v2.0.0 · **Pembaruan terakhir:** 15 November 2026 · **Cakupan:** 38 provinsi, 8 wilayah sistem

---

## Tujuan

Indonesia Power Map menyatukan informasi teknis yang tersebar di RUPTL, OpenStreetMap, dan pemetaan tim IPM
menjadi satu data collection yang rapi, konsisten, dan siap dipakai untuk pemodelan sistem tenaga: studi
aliran daya, perencanaan ekspansi, kajian integrasi energi terbarukan, maupun analisis kebijakan.

Repo ini hanya berisi data. Peta interaktifnya ada di situs Indonesia Power Map.

## Cakupan

| | Aset eksisting | Rencana 2025–2034 |
|---|---:|---:|
| Pembangkit | 2.267 pembangkit · 110,1 GW (termasuk captive) | 1.060 baris rencana |
| Gardu induk | 1.333 GI · 128.766 MVA trafo + 111.890 MVA IBT | 1.770 pekerjaan · 114.870 MVA |
| Transmisi 70–500 kV | 2.005 ruas · 48.189 km · 84.693 kms | 1.290 ruas · 59.026 kms |

Rincian per wilayah:

| Wilayah | Pembangkit eksisting | GI eksisting | Transmisi eksisting | Rencana pembangkit | Pekerjaan GI | Ruas transmisi rencana |
|---|---:|---:|---:|---:|---:|---:|
| Jawa-Madura-Bali | 308 | 618 | 1.052 | 294 | 1.031 | 695 |
| Sumatera | 662 | 341 | 452 | 204 | 346 | 203 |
| Kalimantan | 361 | 120 | 223 | 110 | 157 | 146 |
| Sulawesi | 293 | 147 | 205 | 167 | 162 | 161 |
| Maluku | 206 | 19 | 7 | 77 | 9 | 28 |
| Papua | 256 | 22 | 14 | 85 | 20 | 18 |
| Nusa Tenggara Barat | 53 | 41 | 27 | 43 | 16 | 16 |
| Nusa Tenggara Timur | 128 | 25 | 25 | 80 | 29 | 23 |
| **Indonesia** | **2.267** | **1.333** | **2.005** | **1.060** | **1.770** | **1.290** |

Setiap item rencana mencatat tahun operasi di dua skenario RUPTL (**RE Base** dan **ARED**) serta halaman dan
barisnya di dokumen RUPTL.

## Isi repo

```
data/
├── csv/                       # tabel atribut (UTF-8)
│   ├── existing_generators.csv
│   ├── existing_substations.csv
│   ├── existing_transmission.csv
│   ├── ruptl_generators.csv
│   ├── ruptl_substations.csv
│   └── ruptl_transmission.csv
└── geojson/                   # tabel yang sama dengan geometri (WGS84)
docs/
├── data_dictionary.md         # arti setiap kolom
└── methodology.md             # cara data dikumpulkan, dipetakan, dan diperiksa
```

## Sumber data

- **RUPTL PLN 2025–2034** (Keputusan Menteri ESDM 188.K/TL.03/MEM.L/2025): atribut aset dan seluruh rencana
  pembangkit, gardu induk, dan transmisi per provinsi.
- **RUKN 2025–2060**: koridor indikatif Green Enabling Super Grid.
- **OpenStreetMap** (© OpenStreetMap contributors, ODbL): titik dan jalur yang sudah tergambar di OSM.
- **Manual Mapping by IPM**: titik dan jalur yang dipetakan dan diperiksa tim IPM.

Setiap baris menyebut sumbernya: `attribute_source`/`source` untuk atribut, `location_source`/`geometry_source`
untuk titik dan jalur.

## Metode singkat

Tabel rencana RUPTL dibaca baris demi baris, lengkap dengan rujukan halaman dan barisnya. Aset eksisting
dihimpun dari peta jaringan IPM dan dicocokkan dengan RUPTL untuk kapasitas dan tegangan. Lokasi diambil dari
OpenStreetMap bila tersedia; selebihnya dipetakan manual oleh tim IPM, dengan tingkat keyakinan lokasi
(`location_confidence`) dan jenis jalur (`route_status`) yang dicatat di setiap baris. Setiap rilis diperiksa
otomatis untuk keterhubungan saluran, konsistensi atribut, dan kebersihan nama.

Detail lengkap ada di [`docs/methodology.md`](docs/methodology.md), dan arti setiap kolom di
[`docs/data_dictionary.md`](docs/data_dictionary.md).

## Cara pakai

Python (pandas / geopandas):

```python
import pandas as pd
import geopandas as gpd

plants = pd.read_csv("data/csv/existing_generators.csv")
lines = gpd.read_file("data/geojson/existing_transmission.geojson")

# kapasitas terpasang per jenis pembangkit (MW)
print(plants.groupby("type")["capacity_mw"].sum().sort_values(ascending=False))

# proyek pembangkit skenario RE Base yang beroperasi sampai 2030
plan = pd.read_csv("data/csv/ruptl_generators.csv")
print(plan[plan["cod_re_base"] <= 2030][["name", "type", "capacity_mw", "cod_re_base", "province"]])
```

GeoJSON juga bisa langsung dibuka di QGIS, kepler.gl, atau geojson.io.

Tips pemodelan:

- Pekerjaan gardu induk rencana dengan `scope` Extension/Uprate berada di titik GI eksistingnya, sehingga
  bisa digabung berdasarkan lokasi.
- Ujung saluran (`from_substation`, `to_substation`) memakai nama GI.
- Untuk panjang saluran, pakai `length_km` atau `circuit_km`, bukan panjang geometri jalur `assumed` atau
  `schematic`.

## Disclaimer

- Data ini **bukan** data operasi sistem tenaga dan **bukan** data dispatch real-time.
- Lokasi item rencana dengan `location_confidence = medium` bersifat perkiraan.
- Untuk edukasi, eksplorasi, dan pemodelan awal. Untuk keputusan teknis, operasional, atau hukum, lakukan
  verifikasi tambahan.

## Author

Diinisiasi oleh **Sandro Agassi Sitompul, Ph.D.** (2026) dan dikembangkan bersama tim developers Indonesia
Power Map.

## Feedback & kontak

Masukan, koreksi data, maupun saran pengembangan sangat diterima.

- **Email:** [contact@indonesiapowermap.com](mailto:contact@indonesiapowermap.com)
- **LinkedIn:** [linkedin.com/in/sandro-sitompul-a7a490107](https://www.linkedin.com/in/sandro-sitompul-a7a490107)

## Sitasi

Apabila data dari repositori ini digunakan dalam publikasi atau laporan, mohon disitasi sebagai berikut:

```
Sitompul, S. A. (2026). Indonesia Power Map: Data Collection (v2.0.0).
https://github.com/alexandertiopan1212/ipm-model
```

Format BibTeX dan format lain tersedia melalui tombol "Cite this repository" di GitHub (dari
[`CITATION.cff`](CITATION.cff)).

## Lisensi

- Data dan dokumen di repo ini memakai **CC-BY-SA 4.0**: bebas dipakai dan dimodifikasi, dengan atribusi dan
  share-alike. Lihat [`LICENSE`](LICENSE).
- Data turunan OpenStreetMap juga tunduk pada **ODbL** (Open Database License).
- RUPTL dan RUKN adalah dokumen publik PLN/ESDM.

## Kontribusi

Koreksi data sangat diterima. Buka issue dengan menyebut `id` baris, kolom yang salah, dan sumber koreksinya.
