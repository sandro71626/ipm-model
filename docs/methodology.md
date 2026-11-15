# Metodologi

Dokumen ini menjelaskan cara data collection Indonesia Power Map disusun: dari mana data berasal, bagaimana
lokasi dan jalur dipetakan, dan bagaimana kualitasnya diperiksa.

## 1. Cakupan

| Layer | Isi |
|---|---|
| Pembangkit eksisting | Pembangkit yang beroperasi, termasuk captive, sistem isolated, PLTS atap (agregat per gardu induk), dan impor dari Sarawak. |
| Gardu induk eksisting | Gardu induk dan switchyard 70–500 kV, dengan kapasitas trafo (MVA) dan IBT di GITET. |
| Transmisi eksisting | Saluran 70–500 kV, termasuk HVDC dan kabel laut. |
| Rencana pembangkit | Setiap baris tabel rencana pembangkit RUPTL 2025-2034, ditambah satu koridor RUKN 2025-2060. |
| Rencana gardu induk | Setiap pekerjaan gardu induk RUPTL: GI baru, extension, dan uprate. |
| Rencana transmisi | Setiap ruas transmisi RUPTL: saluran baru, reconductoring, dan uprate. |

## 2. Sumber

| Sumber | Dipakai untuk |
|---|---|
| **RUPTL PLN 2025–2034** (Kepmen ESDM 188.K/TL.03/MEM.L/2025) | Atribut semua aset dan seluruh item rencana: kapasitas, tegangan, tahap, tahun operasi |
| **RUKN 2025–2060** | Koridor indikatif Green Enabling Super Grid |
| **OpenStreetMap** (Overpass, Oktober 2026) | Titik dan jalur yang sudah tergambar di OSM |
| **Manual Mapping by IPM** | Titik dan jalur yang dipetakan dan diperiksa tim IPM |

Kolom `attribute_source`, `capacity_source`, dan `source` mencatat sumber atribut. Kolom `location_source`
dan `geometry_source` mencatat asal titik atau jalur: `OpenStreetMap` atau `Manual Mapping by IPM`.

## 3. Rencana RUPTL

1. **Ekstraksi baris demi baris.** Tabel rencana pembangkit, gardu induk, dan transmisi per provinsi dibaca
   satu baris per item. Setiap item menyimpan halaman (`ruptl_page`) dan barisnya (`ruptl_row`) di dokumen,
   sehingga bisa dicek balik.
2. **Dua skenario.** Tahun operasi dicatat untuk skenario **RE Base** (`cod_re_base`) dan **ARED**
   (`cod_ared`). Kolom kosong berarti item itu tidak ada di skenario tersebut.
3. **Satu item, satu baris.** Bila RUPTL memecah proyek per unit atau tahap, setiap baris dipertahankan
   seperti di dokumen. Karena itu jumlah baris lebih besar dari jumlah nama proyek unik.
4. **Nama.** Nama mengikuti tabel RUPTL. Penanda catatan kaki dibuang (mis. `Sumut-14)` menjadi `Sumut-1`),
   kurung yang terpotong dilengkapi, dan istilah dibakukan ke istilah PLN (GI, GITET, Inc., Tx.).

## 4. Aset eksisting

1. **Inventaris.** Pembangkit, gardu induk, dan saluran dihimpun dari peta jaringan IPM dan dicocokkan dengan
   RUPTL untuk kapasitas, tegangan, dan jumlah trafo.
2. **Pembersihan.** Entri ganda, saluran yang sudah digantikan, dan pembangkit yang tidak lagi beroperasi dibuang. Titik bantu pemodelan
   (junction, bus pembangkit) dan jaringan di bawah 70 kV tidak dimuat.
3. **Nama.** Nama dibakukan ke format PLN (`PLTU <nama>`, `GI <nama>`, `Tx. <nama>` untuk titik
   percabangan) dan diterjemahkan bila masih berbahasa Inggris.

## 5. Pemetaan lokasi dan jalur

Titik dan jalur diambil dari OpenStreetMap bila sudah tergambar dan sesuai. Selain itu, tim IPM memetakannya
secara manual (**Manual Mapping by IPM**) dengan urutan berikut:

| Kasus | Cara penempatan | `location_confidence` |
|---|---|---|
| Lokasi bernama (pembangkit, GI baru) | Titik ditempatkan di lokasi yang disebut, lalu diperiksa terhadap peta dasar | high |
| Pekerjaan di GI eksisting (extension, uprate) | Ditempatkan di GI eksisting tersebut | high |
| Kuota tersebar atau lokasi tidak disebut | Ditempatkan di GI tujuan atau wilayah sistemnya | medium |

Jalur transmisi rencana digambar di antara dua GI ujungnya. Kolom `route_status` menjelaskan jenis jalurnya:

| `route_status` | Arti |
|---|---|
| `drawn` | Jalur digambar mengikuti koridor yang direncanakan |
| `assumed` | Jalur mengikuti jaringan jalan, karena koridornya belum diketahui |
| `schematic` | Garis indikatif antar-ujung |
| `none` | Tidak ada jalur; hanya titik ujung |

Ruas yang dipotong di titik percabangan (*tee*) disimpan sebagai MultiLineString di GeoJSON.

## 6. Pemeriksaan kualitas

- **Keterhubungan.** Ujung saluran dianggap tersambung ke GI bila jaraknya kurang dari 1 km.
- **Konsistensi atribut.** Kapasitas, tegangan, dan tahun dicek terhadap tabel RUPTL; nilai yang tidak
  tercatat dibiarkan kosong, bukan ditebak.
- **Provinsi.** Setiap baris punya provinsi; baris yang tidak menyebutnya diberi provinsi aset terdekat.
- **ID stabil.** ID rencana memakai ID item RUPTL (`PRJ-…`). ID aset eksisting (`GEN-`, `GI-`, `LN-`) tetap
  sama antar-versi selama asetnya sama.
- **Nama.** Setiap rilis diperiksa otomatis untuk kurung yang tidak seimbang, sisa bahasa Inggris, dan nama
  kosong.

## 7. Keterbatasan

- Lokasi item rencana dengan `location_confidence = medium` bersifat perkiraan.
- Jalur `assumed` dan `schematic` bukan rute sebenarnya dan tidak cocok untuk perhitungan panjang yang presisi;
  untuk itu pakai kolom `length_km` atau `circuit_km`.
- Total kapasitas RE Base (±59,3 GW) adalah jumlah item tabel per provinsi, sehingga bisa berbeda dari angka
  ringkasan resmi RUPTL.
- Data ini untuk edukasi, eksplorasi, dan pemodelan awal. Untuk keputusan teknis, operasional, atau hukum,
  lakukan verifikasi tambahan.
