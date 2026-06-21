# Clustering Wilayah untuk Deteksi Disparitas Ketepatan Sasaran Penyaluran BPNT dan PKH

**Abraham Rusty Djajani**  
**NIM:** 535240007  
**Email:** abraham.535240007@stu.untar.ac.id  
**Program Studi Teknik Informatika — Universitas Tarumanagara**

---

## Abstrak

Penyaluran bantuan sosial (bansos) berupa BPNT dan PKH merupakan salah satu instrumen pemerintah dalam menanggulangi kemiskinan. Namun, disparitas ketepatan sasaran antar wilayah masih menjadi tantangan. Penelitian ini menerapkan algoritma *unsupervised learning* — K-Means, HAC (Ward), dan DBSCAN — untuk mengelompokkan 51 kabupaten/kota dari Sumatera Selatan, Jawa Tengah, dan DKI Jakarta berdasarkan empat indikator: persentase penduduk miskin (P0), persentase penerima bansos, rasio ketepatan sasaran, dan indeks diversitas bansos. Hasil menunjukkan K-Means dengan K=4 memberikan performa terbaik (Silhouette=0.4604, DBI=0.7686) dan mengidentifikasi tiga cluster over-coverage serta satu cluster tepat sasaran. DBSCAN mendeteksi 7 wilayah (13,7%) sebagai anomali yang memerlukan verifikasi lapangan.

**Kata Kunci:** clustering, BPNT, PKH, ketepatan sasaran, K-Means, HAC, DBSCAN, kemiskinan

---

## 1. Pendahuluan

Program Bantuan Pangan Non-Tunai (BPNT) dan Program Keluarga Harapan (PKH) merupakan dua program bansos utama di Indonesia. Efektivitas program ini sangat bergantung pada ketepatan sasaran penyaluran, yaitu sejauh mana bansos menjangkau rumah tangga miskin yang menjadi target. Ketidaktepatan sasaran dapat berupa *under-coverage* (bansos tidak menjangkau seluruh penduduk miskin) maupun *over-coverage* (bansos menjangkau penduduk tidak miskin).

Penelitian ini bertujuan untuk:
1. Mengelompokkan wilayah berdasarkan indikator ketepatan sasaran bansos
2. Mengidentifikasi cluster *under-coverage*, *over-coverage*, dan tepat sasaran
3. Membandingkan performa algoritma K-Means, HAC (Ward), dan DBSCAN

---

## 2. Tinjauan Pustaka

### 2.1 BPNT dan PKH

BPNT adalah program bantuan pangan yang disalurkan secara nontunai melalui mekanisme perbankan, sedangkan PKH adalah program bantuan tunai bersyarat yang menyasar keluarga miskin dengan kewajiban di bidang pendidikan dan kesehatan (Kemensos RI, 2023).

### 2.2 Algoritma Clustering

**K-Means** (MacQueen, 1967) mengelompokkan data dengan mempartisi ruang fitur berdasarkan jarak ke pusat cluster. **HAC (Ward)** (Ward, 1963) membangun hierarki cluster dengan meminimalkan jumlah kuadrat error pada setiap penggabungan. **DBSCAN** (Ester et al., 1996) mengelompokkan data berdasarkan kepadatan dan secara otomatis mengidentifikasi titik anomali (*noise*).

### 2.3 Metrik Evaluasi

- **Silhouette Score** (Rousseeuw, 1987): mengukur seberapa mirip suatu objek dengan clusternya dibandingkan cluster lain. Rentang -1 hingga 1, semakin tinggi semakin baik.
- **Davies-Bouldin Index (DBI)** (Davies & Bouldin, 1979): rata-rata similaritas antar cluster. Semakin rendah semakin baik.
- **Calinski-Harabasz Index** (Calinski & Harabasz, 1974): rasio varians antar cluster terhadap varians dalam cluster. Semakin tinggi semakin baik.

---

## 3. Metodologi

### 3.1 Data

Data bersumber dari Badan Pusat Statistik (BPS) meliputi:

| Data | Tahun | Wilayah | Jumlah Observasi |
|---|---|---|---|
| Persentase Penduduk Miskin (P0) | 2025 | Seluruh Indonesia | 515 kab/kota |
| BPNT (Sumatera Selatan) | 2024 | Sumatera Selatan | 18 kab/kota |
| BPNT + PKH (Jawa Tengah) | 2023 | Jawa Tengah | 35 kab/kota |
| BPNT + PKH (DKI Jakarta) | 2021 | DKI Jakarta | 6 kab/kota |

Setelah proses *merging* dan *filtering*, diperoleh **51 wilayah unik** dengan **56 observasi** (beberapa wilayah memiliki data di dua tahun berbeda). Untuk menjaga konsistensi, data yang digunakan adalah *snapshot* tahun terbaru per wilayah.

### 3.2 Fitur

| Fitur | Definisi | Peran |
|---|---|---|
| `p0_persen` | Persentase penduduk miskin (P0) | Tingkat kemiskinan wilayah |
| `pct_total_penerima` | Persentase rumah tangga penerima BPNT dan/atau PKH | Cakupan bansos |
| `rasio_ketepatan_sasaran` | pct_total_penerima / p0_persen | Proporsionalitas penyaluran |
| `diversity_index` | Jumlah program bansos dengan cakupan > 1% | Keragaman bantuan |

### 3.3 Preprocessing

- Normalisasi fitur menggunakan *StandardScaler* (z-score)
- Penentuan K optimal melalui metode *Elbow* dan *Silhouette Score*

### 3.4 Algoritma

| Algoritma | Parameter | Karakteristik |
|---|---|---|
| K-Means | K=4, n_init=10, random_state=42 | Partisi berdasarkan jarak Euclidean |
| HAC (Ward) | n_clusters=4, linkage=ward | Hierarki aglomeratif, meminimalkan within-cluster variance |
| DBSCAN | eps=0.7, min_samples=3 | *Density-based*, deteksi anomali otomatis |

---

## 4. Hasil dan Pembahasan

### 4.1 Penentuan K Optimal

Berdasarkan metode Elbow dan Silhouette Score, nilai K optimal adalah **4** dengan Silhouette Score sebesar **0,4604**.

![fig1](fig1_elbow_silhouette.png)

### 4.2 Perbandingan Algoritma

| Algoritma | Silhouette | DBI | Calinski-Harabasz |
|---|---|---|---|
| **K-Means (K=4)** | **0,4604** | **0,7686** | **54,80** |
| HAC (Ward) (K=4) | 0,4370 | 0,7814 | 50,99 |
| DBSCAN (eps=0,7) | 0,3731 | 2,6414 | 18,99 |

K-Means unggul pada seluruh metrik evaluasi dibandingkan HAC dan DBSCAN. DBSCAN menunjukkan performa paling rendah akibat variasi kepadatan data yang tidak seragam.

![fig2](fig2_perbandingan_algoritma.png)

### 4.3 Visualisasi PCA

Dua komponen utama (*Principal Components*) menjelaskan **90,9%** variansi data (PC1=58,7%, PC2=32,2%).

![fig3](fig3_pca_clusters.png)

### 4.4 Interpretasi Cluster (K-Means)

| Cluster | n | P0 | Penerima | Rasio | Diversity | Karakteristik |
|---|---|---|---|---|---|---|
| **0** | 5 | 4,42% | 10,1% | 2,15 | 2,0 | Over-coverage — P0 rendah (Jakarta) |
| **1** | 18 | 11,67% | 45,5% | 4,15 | 2,0 | Over-coverage — P0 tinggi (Jateng & Sumsel) |
| **2** | 14 | 10,69% | 16,7% | 1,73 | 1,0 | Tepat Sasaran — kemiskinan tinggi (Sumsel) |
| **3** | 14 | 7,22% | 33,3% | 4,95 | 2,0 | Over-coverage — P0 sedang (Jateng) |

![fig4](fig4_profil_cluster.png)

### 4.5 Analisis Rasio Ketepatan Sasaran

Seluruh wilayah dalam sampel menunjukkan rasio ketepatan > 1,0 yang mengindikasikan cakupan bansos melebihi proporsi penduduk miskin. Pola ini konsisten di ketiga provinsi.

![fig5](fig5_rasio_vs_p0.png)

### 4.6 Deteksi Anomali DBSCAN

DBSCAN mengidentifikasi **7 dari 51 wilayah (13,7%)** sebagai anomali:

| Wilayah | Cluster | P0 | Rasio | Indikasi |
|---|---|---|---|---|
| Banyumas | 1 | 11,15% | 6,14 | Over-coverage ekstrem |
| Kota Jakarta Utara | 0 | 6,33% | 1,98 | Over-coverage pada kota besar |
| Kota Salatiga | 3 | 4,20% | 5,13 | Over-coverage pada P0 rendah |
| Musi Rawas Utara | 2 | 15,25% | 0,44 | Under-coverage (rasio < 1) |
| Ogan Ilir | 2 | 11,69% | 2,97 | Over-coverage signifikan |
| Purbalingga | 1 | 12,55% | 5,66 | Over-coverage ekstrem |
| Temanggung | 3 | 7,78% | 7,33 | Over-coverage tertinggi |

Wilayah anomali ini menjadi prioritas untuk verifikasi lapangan lebih lanjut.

---

## 5. Kesimpulan

Berdasarkan hasil analisis clustering terhadap 51 kabupaten/kota dari Sumatera Selatan, Jawa Tengah, dan DKI Jakarta, dapat disimpulkan sebagai berikut:

1. **K-Means dengan K=4** merupakan algoritma terbaik untuk mengelompokkan wilayah berdasarkan ketepatan sasaran bansos, dengan Silhouette Score 0,4604 dan DBI 0,7686, lebih unggul dibandingkan HAC (Ward) dan DBSCAN.

2. **Empat cluster** berhasil diidentifikasi dengan karakteristik yang berbeda:
   - **Cluster 0** (5 wilayah): *Over-coverage* pada wilayah dengan P0 rendah (Jakarta)
   - **Cluster 1** (18 wilayah): *Over-coverage* pada wilayah dengan P0 tinggi
   - **Cluster 2** (14 wilayah): **Tepat sasaran** — rasio ketepatan paling proporsional
   - **Cluster 3** (14 wilayah): *Over-coverage* pada wilayah dengan P0 sedang

3. **Seluruh wilayah** dalam sampel menunjukkan rasio ketepatan sasaran di atas 1,0, yang berarti cakupan bansos secara nominal melebihi jumlah penduduk miskin. Temuan ini konsisten di Sumatera Selatan, Jawa Tengah, dan DKI Jakarta, mengindikasikan adanya pola *over-coverage* yang sistemik.

4. **DBSCAN** mendeteksi **7 wilayah anomali (13,7%)**, termasuk Musi Rawas Utara yang menunjukkan *under-coverage* (rasio 0,44) dan Temanggung dengan *over-coverage* tertinggi (rasio 7,33). Wilayah-wilayah ini memerlukan verifikasi dan evaluasi lebih lanjut di lapangan.

5. Pendekatan *clustering* terbukti efektif sebagai alat bantu deteksi disparitas ketepatan sasaran bansos dan dapat digunakan oleh pemangku kebijakan untuk mengevaluasi serta menyempurnakan mekanisme penyaluran bansos di masa mendatang.

---

## Daftar Pustaka

- Calinski, T., & Harabasz, J. (1974). A dendrite method for cluster analysis. *Communications in Statistics*, 3(1), 1–27.
- Davies, D. L., & Bouldin, D. W. (1979). A cluster separation measure. *IEEE Transactions on Pattern Analysis and Machine Intelligence*, PAMI-1(2), 224–227.
- Ester, M., Kriegel, H. P., Sander, J., & Xu, X. (1996). A density-based algorithm for discovering clusters in large spatial databases with noise. *Proceedings of the Second International Conference on Knowledge Discovery and Data Mining*, 226–231.
- Kemensos RI. (2023). *Pedoman Pelaksanaan Program Keluarga Harapan dan Bantuan Pangan Non-Tunai*. Jakarta: Kementerian Sosial Republik Indonesia.
- MacQueen, J. (1967). Some methods for classification and analysis of multivariate observations. *Proceedings of the Fifth Berkeley Symposium on Mathematical Statistics and Probability*, 1, 281–297.
- Rousseeuw, P. J. (1987). Silhouettes: A graphical aid to the interpretation and validation of cluster analysis. *Journal of Computational and Applied Mathematics*, 20, 53–65.
- Ward, J. H. (1963). Hierarchical grouping to optimize an objective function. *Journal of the American Statistical Association*, 58(301), 236–244.

---

## Cara Menjalankan

```bash
git clone https://github.com/RustyRustacle/bansos-clustering-analysis.git
cd bansos-clustering-analysis
python3 -m venv venv
source venv/bin/activate
pip install -r requirements.txt
jupyter notebook run_analisis_bansos.ipynb
```

## Requirements

- Python 3.9+
- numpy, pandas, matplotlib, scikit-learn
- openpyxl, xlrd
- jupyter, nbconvert, ipykernel

## Output

| File | Deskripsi |
|---|---|
| `hasil_clustering_wilayah.csv` | Hasil clustering per wilayah |
| `profil_cluster.csv` | Statistik rata-rata fitur per cluster |
| `perbandingan_algoritma.csv` | Metrik evaluasi antar algoritma |

## Lisensi

Proyek ini dibuat untuk tujuan akademis.
