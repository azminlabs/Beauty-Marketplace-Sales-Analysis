# SAPHIRA Sales Dashboard. Business Insight Notes

**Periode data:** 1 Januari 2026 sampai 21 Mei 2026 (±4 bulan 21 hari)
**Disusun oleh:** Muh. Azmin

---

## Ringkasan Eksekutif

Total Net Sales Rp887 juta dari 2.480 transaksi, AOV Rp397 ribu, Net Profit Margin 40,90%. Tren naik stabil Jan-Apr, turun tajam di Mei (setelah dicek karena data belum penuh sebulan).

Tiga temuan utama:
1. TikTok Shop net sales tertinggi kedua, tapi margin terendah, karena production cost dari barang retur.
2. Bronze tier penyumbang revenue terbesar (volume tinggi), Gold AOV tinggi tapi jarang repeat, Silver tidak unggul di metrik manapun.
3. Wrong Shade dan Changed Mind adalah dua alasan retur terbesar, keduanya soal informasi produk, bukan kualitas barang.

---

## 1. Metodologi

**Data Understanding (Python):** sebelum cleaning, dilakukan eksplorasi awal pada kedua dataset (product dan transactions), mencakup pengecekan shape, struktur kolom, statistik deskriptif, duplikat, missing value, outlier, konsistensi data kategorikal, dan validasi business rule (cross check gross_sales dan net_sales terhadap selling_price dan discount_amount).

**Data Cleaning (Python):**
- *Missing values:* ditemukan di kolom `return_reason`. Untuk transaksi dengan `returned_flag` = No, missing value diisi "No Return" karena memang tidak ada retur. Untuk transaksi dengan `returned_flag` = Yes yang datanya hilang, baris dihapus karena proporsinya di bawah 5%.
- *Standardisasi format:* kolom `transaction_date` dan `order_time` dikonversi ke tipe datetime/time agar konsisten dan siap dipakai untuk analisis tren waktu.
- *Outlier:* dicek lewat boxplot pada kolom numerik. Outlier di `discount_amount` (di atas Rp175.000) ternyata berkorelasi dengan transaksi quantity dan gross_sales yang lebih besar, mengindikasikan transaksi bulk/produk premium yang wajar, sehingga tidak dihapus.
- *Duplicate:* dicek di kedua dataset, tidak ditemukan baris duplikat.

**Data Modeling (Power BI):** data hasil cleaning dimasukkan ke Power BI dengan skema star schema (`dim_product` dan `dim_date` terhubung ke `fact_transactions`), lalu dibangun calculated measure dengan DAX untuk metrik seperti Net Sales, Net Profit Margin, dan Production Cost (lihat catatan kondisional di bagian Sales Performance).

---

## 2. Sales Performance

Net Sales Rp887M (Gross Rp1.015M dikurangi diskon Rp127M). Breakdown margin: Platform Fee -2,34%, Shipping Cost -4,00%, Production Cost -52,77%. Production cost adalah komponen terbesar yang mengikis margin, jauh di atas fee dan shipping.

*Catatan metodologi:* production cost dihitung untuk transaksi Delivered atau Returned dengan alasan Damaged Product saja, karena di dua kondisi ini biaya produksi sudah keluar dan barang tidak bisa dijual ulang.

---

## 3. Marketplace Performance

| Platform | Net Sales | Net Profit Margin |
|---|---|---|
| Shopee | Rp344M | 41,49% |
| TikTok Shop | Rp311M | 39,87% (terendah) |
| Offline Store | Rp140M | 41,83% (tertinggi) |
| Website | Rp92M | 40,75% |

**Insight:** Platform fee dan shipping cost TikTok Shop secara persentase mirip platform lain. Yang beda adalah production cost dari barang Returned, TikTok Shop menyumbang 44,35% dari total production cost retur seluruh platform (vs Shopee 36,67%, Offline Store 16,37%, Website 2,61%).

**Rekomendasi:** Audit kenapa retur Damaged Product terkonsentrasi di TikTok Shop, kemungkinan terkait packaging untuk live selling atau penanganan kurir. Pertimbangkan QC tambahan untuk order dari channel ini.

---

## 4. Product Performance

Total 24 SKU, 10 kategori, 67K stok, avg rating 4,50. Best seller: Glow Matte Lip Cream (383 unit). Best category: Lipstick (Rp180M). Low performer (Honey Lip Balm, Rosy Cheek Blush, dkk) tetap rating bagus (4,1-5,0), jadi masalahnya di visibility, bukan kualitas.

**Product Matrix per kategori** (avg net sales Rp99M, avg margin 46,6%):

| Kuadran | Kategori | Rekomendasi |
|---|---|---|
| Star (sales tinggi, margin tinggi) | Lipstick | Pertahankan, jadikan andalan campaign besar |
| Volume Driver (sales tinggi, margin rendah) | Eyebrow | Investigasi cost structure, berpotensi jadi profit kedua kalau efisien |
| Profitable Niche (sales rendah, margin tinggi) | Two Way Cake, Mascara, Lip Balm | Naikkan exposure lewat bundling/campaign |
| Perlu Perhatian (sales rendah, margin rendah) | Eyeliner, Cushion, Blush On, Foundation, Primer | Evaluasi harga vs production cost dulu sebelum scale up, terutama Foundation (margin 23,7%) |

---

## 5. Customer Insight

Total 843 customer, avg rating 3,0, avg usia 30,89 tahun.

**Membership Tier:**

| Tier | Net Sales | % Total | AOV | Orders/Customer |
|---|---|---|---|---|
| Bronze | Rp534M | 60,2% | Rp401K | 2,02 |
| Silver | Rp271M | 30,5% | Rp389K | 1,49 |
| Gold | Rp82M | 9,3% | Rp409K | 1,13 |

Bronze unggul di frekuensi, jadi penyumbang sales terbesar meski AOV biasa saja. Gold AOV tertinggi tapi jarang repeat, base kecil. Silver berada di posisi tanggung, AOV nyaris setara Bronze tapi frekuensi lebih rendah, tidak unggul seperti Gold di nilai per transaksi.

**Rekomendasi Silver:** kandidat utama tier-upgrade campaign, dorong naik ke Gold atau tingkatkan frekuensi mendekati pola Bronze. Populasinya besar (528 customer), jadi perbaikan kecil di sini berdampak signifikan.

**Cohort Retention:** rata-rata hanya 26-48% customer kembali di Month 1, terus menyusut di bulan berikutnya. Mayoritas customer cuma transaksi sekali lalu hilang.

**Rekomendasi:** fokus re-engagement campaign di 30 hari pertama setelah pembelian pertama, karena di situ titik drop-off terbesar.

---

## 6. Operational Insight

Delivery Success 90,04%, Return Rate 4,64%, Cancellation Rate 5,32%, Avg Delivery Days 3,98 hari.

**Return Reason:**

| Alasan | Jumlah |
|---|---|
| Wrong Shade | 34 |
| Changed Mind | 33 |
| Damaged Product | 24 |
| Late Delivery | 24 |

Wrong Shade dan Changed Mind, dua alasan terbesar, sama sekali bukan masalah kualitas produk atau logistik, tapi soal informasi dan keputusan pembelian.

- **Wrong Shade:** banyak di kategori dengan variasi shade tinggi (Eyebrow, Foundation, Lipstick), lebih sering di Shopee dan TikTok Shop (11 kasus masing-masing) dibanding Website (4 kasus). Kemungkinan karena foto/deskripsi di marketplace kurang representatif.
- **Changed Mind:** paling banyak di TikTok Shop (12 kasus), terkait periode campaign besar (Beauty Festival, 11.11, Flash Sale menyumbang 19 dari 33 kasus). Pola klasik impulse buying saat promo.

**Rekomendasi:**
1. Perbaiki deskripsi dan foto shade produk di marketplace, terutama Shopee dan TikTok Shop.
2. Evaluasi copy promosi saat campaign besar, supaya tidak terlalu mendorong pembelian impulsif.
3. Root cause-nya soal informasi, jadi perbaikannya relatif murah dan cepat dibanding masalah Damaged Product atau Late Delivery yang butuh perbaikan operasional.

---

## 7. Rangkuman Prioritas

| Prioritas | Area | Rekomendasi |
|---|---|---|
| 1 | Marketplace | Audit dan QC retur barang rusak di TikTok Shop |
| 2 | Product | Evaluasi cost structure Eyebrow dan Foundation |
| 3 | Customer | Re-engagement campaign 30 hari pertama |
| 4 | Customer | Tier-upgrade campaign untuk Silver |
| 5 | Operational | Perbaikan deskripsi/foto shade produk |
| 6 | Operational | Evaluasi copy campaign saat flash sale |

---

## 8. Catatan Limitasi dan Peluang Lanjutan

Data hanya mencakup ±4,5 bulan, jadi churn rate klasik kurang reliable. Cohort retention yang sudah dibangun lebih sesuai untuk skala data ini. Ke depan bisa dikembangkan analisis RFM penuh per individual customer untuk segmentasi lebih granular dari sekadar membership tier.
