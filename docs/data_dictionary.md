# Kamus data

Enam tabel nasional. Setiap tabel tersedia dalam CSV (`data/csv/`, UTF-8) dan GeoJSON (`data/geojson/`, WGS84,
EPSG:4326). Atributnya sama; CSV menyimpan `lon`/`lat` untuk titik, sedangkan jalur transmisi hanya ada di
GeoJSON.

Satuan: kapasitas pembangkit dalam MW (PLTS dalam MWp), kapasitas trafo dalam MVA, panjang dalam km,
`circuit_km` = panjang × jumlah sirkit.

## Rencana RUPTL 2025–2034

### `ruptl_generators.csv`: satu baris per proyek pembangkit RUPTL

| Kolom | Arti |
|---|---|
| `id` | ID item (`PRJ-GEN-xxxxx`; `GESG-…` untuk koridor RUKN) |
| `name` | Nama seperti di tabel RUPTL |
| `technology` | Kode teknologi: COAL, CCGT, GT, GE (mesin gas), HYDRO, MINIHYDRO, PSH (pumped storage), GEO, PV, PV_BESS, WIND, WIND_BESS, BIOMASS, BIOGAS, WASTE, BESS, OCEAN, NUCLEAR, DIESEL |
| `type` | Singkatan PLN: PLTU, PLTGU, PLTG, PLTMG, PLTA, PLTA-PS, PLTM, PLTP, PLTS, PLTB, PLTBm, PLTBg, PLTSa, BESS, PLTAL, PLTN, PLTD |
| `technology_label` | Label gabungan bila ada (mis. "PLTS + BESS") |
| `capacity_mw` | Kapasitas (MW, AC) |
| `storage_mwh` | Energi penyimpanan (MWh) bila disebut |
| `status` | Tahap menurut RUPTL: Planned, Construction, Committed, Proposed, Study |
| `developer` | Pengembang bila disebut (PLN, IPP, …) |
| `cod_re_base` / `cod_ared` | Tahun COD di skenario RE Base dan ARED. Kosong = item tidak ada di skenario itu |
| `target_cod_year` | COD RE Base, atau ARED bila RE Base kosong |
| `province`, `system` | Provinsi dan sistem (JAMALI, SUMATERA, KALIMANTAN, SULAWESI, MALUKU, PAPUA, NUSA_TENGGARA) |
| `ruptl_page`, `ruptl_row` | Halaman PDF dan nomor baris di tabel RUPTL |
| `source` | `RUPTL 2025-2034` atau `RUKN 2025-2060` |
| `lon`, `lat` | Lokasi (indikatif untuk kuota tersebar) |
| `location_source` | Asal titik: `OpenStreetMap` atau `Manual Mapping by IPM` |
| `location_confidence` | high (lokasi bernama), medium (GI tujuan / wilayah sistem), low (pusat provinsi) |

### `ruptl_substations.csv`: satu baris per pekerjaan gardu induk

`scope` (New, Extension, Uprate), `voltage_kv`, `voltage_label` (mis. 150/20), `capacity_mva`, `status`,
`cod_re_base`, `cod_ared`, `province`, `system`, `ruptl_page`, `ruptl_row`, `lon`, `lat`,
`location_source`, `location_confidence`. Pekerjaan Extension/Uprate digambar di lokasi GI eksistingnya.

### `ruptl_transmission.csv`: satu baris per ruas transmisi

`from_substation`, `to_substation`, `voltage_kv`, `hvdc`, `scope` (New, Reconductoring, Uprate),
`circuits`, `length_km`, `circuit_km`, `line_type` (SUTT, SUTET, SKTT, SKLT, …), `conductor` (teks asli),
`status`, `cod_re_base`, `cod_ared`, `province`, `system`, `ruptl_page`, `ruptl_row`,
`route_status` (drawn = rute digambar, assumed = mengikuti jalan, schematic = indikatif), `geometry_source`.
Ruas yang dipotong di titik *tee* menjadi MultiLineString di GeoJSON.

## Aset eksisting

### `existing_generators.csv`
`id`, `name`, `technology`, `type` (termasuk `PLTS Atap` dan `Impor`), `capacity_mw` (kosong bila tidak tercatat),
`role` (Utility, Captive, Community, Distributed untuk PLTS atap, Import), `isolated` (pembangkit di sistem isolated), `operator`, `operation_year`,
`province`, `system`, `lon`, `lat`, `attribute_source`, `capacity_source`, `location_source`.

### `existing_substations.csv`
`id`, `name`, `node_type` (substation, switchyard), `voltage_kv`, `voltage_label`, `province`, `system`,
`lon`, `lat`, `capacity_mva` (total trafo step-down), `ibt_mva` (trafo interbus, di GITET), `trafo_count`,
`capacity_source`, `attribute_source`, `location_source`.

### `existing_transmission.csv`
`id`, `name`, `voltage_kv`, `hvdc`, `circuits`, `length_km`, `circuit_km` (panjang × jumlah sirkit),
`from_substation`, `to_substation`, `system`, `province`, `attribute_source`, `geometry_source`.
