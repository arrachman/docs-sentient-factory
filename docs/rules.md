# Project Identity: Sentient Factory

> **Core Philosophy**: Mengubah data pabrik yang "bisu" menjadi "asisten cerdas" yang sadar konteks.

- **Hardware Utama**: Mac Mini M4 Pro (12-core CPU, 16-core GPU, 64GB RAM, 512GB SSD)
- **Target**: Private AI yang berjalan 100% Lokal (On-Premise).

## 1. Analisa Strategis & Konteks

### Mengapa Sistem Ini Dibangun Sekarang?
Pabrik memiliki data yang melimpah di Legacy ERP (Client System) namun "bisu". Manajemen membutuhkan insight real-time tanpa harus bergantung pada staf admin untuk mengolah laporan manual yang lambat dan rentan human-error.

### Apa Nilai Unik dari "Sentient Factory"?
Berbeda dengan dashboard BI statis, sistem ini **"hidup"**. Ia proaktif memberi tahu masalah (Event-Driven) dan bisa diajak berdiskusi (Generative AI) untuk mencari akar masalah, seolah-olah Anda berbicara dengan manajer pabrik digital yang tidak pernah tidur.

### Mengapa Harus On-Premise (Mac Mini M4)?
**Keamanan Data Mutlak.** Data produksi, resep, dan costing adalah rahasia dagang. Mengirim data ini ke Cloud AI (OpenAI/Google) adalah risiko keamanan yang tidak bisa diterima. Mac Mini M4 Pro adalah "Benteng Digital" yang kuat untuk menjalankan LLM 72B parameter secara lokal dan privat.

### Siapa yang Benar-benar Diuntungkan?
**Decision Maker (Direksi & Manajer).** Mereka mendapatkan asisten cerdas yang menyajikan data matang siap keputusan, bukan sekedar tabel mentah. Ini memangkas waktu rapat dan analisa manual secara drastis.

### Bagaimana Sistem Ini Mengubah Operasional?
Sistem mengubah pola kerja dari **Reaktif** (menunggu laporan masalah) menjadi **Proaktif** (sistem memberi peringatan dini). Notifikasi anomali diterima sebelum masalah membesar.

---

## 2. Fitur Utama per Modul (Expanded AI Capabilities)

### 📦 Sales Order (SO) - "The Revenue Guard"
- **Smart Quotation**: AI menganalisa *winning rate* ris historis untuk menyarankan margin harga optimal.
- **Order Anomaly**: Deteksi otomatis order dengan diskon tak wajar atau lonjakan quantity abnormal.
- **Cross-Sell Engine**: Menyarankan produk pelengkap di invoice ("Biasanya yang beli Mesin A juga beli Sparepart B").
- **Churn Prediction**: Memberi alert jika customer rutin tiba-tiba berhenti order bulan ini.

### 🚚 Delivery Order (DO) - "The Logistical Brain"
- **Delay Risk Predictor**: Prediksi keterlambatan via performa historis transporter & cuaca/rute.
- **Smart Routing**: Menyarankan penggabungan pengiriman (batching) untuk hemat biaya.
- **Container Load Opt**: Menghitung susunan pallet optimal dalam truk agar muat maksimal (3D Bin Packing).

### 🛒 Purchase Order (PO) - "The Cost Controller"
- **Vendor Scorecard**: Ringkasan performa vendor (OTD & Quality) siap pakai untuk negosiasi.
- **Price Drift Alert**: Mendeteksi kenaikan harga bahan baku diam-diam (creep) vs rata-rata 3 bulan lalu.
- **Auto-Draft**: Mengonversi request teks samar ("Pesan baut") menjadi PO teknis lengkap.

### 🏭 Production Order (WO) - "The Factory Heart"
- **Downtime Root Cause (RAG)**: Teknisi bertanya "Kenapa Error 505?" -> AI cari di log manual/history.
- **Yield Optimizer**: Korelasi antara batch bahan baku vs kualitas output.
- **Bottleneck Detector**: Menandai Work Center yang selalu telat, menyarankan penambahan shift.
- **WIP Ageing**: Warning jika barang setengah jadi menumpuk terlalu lama di lantai produksi.

### 📈 Operational Pulse (Direct DB Read - Positive Insights)
Selain mendeteksi error, AI secara aktif memonitor "Kesehatan Bisnis" secara real-time dari database ERP:

1. **Executive Snapshot (Real-time)**
   - **Revenue Watch Live**: "Sampai jam 14.00, total Booking SO masuk Rp 2.5 Miliar (80% dari target harian)."
   - **Volume Tracker**: Jumlah Sales Order (SO) vs Delivery Order (DO) hari ini. (Misal: "Masuk 50 SO, tapi baru terkirim 10 DO -> Potensi bottleneck di gudang").
   - **Cash Position**: Saldo AR (Piutang) yang akan jatuh tempo minggu ini vs Hutang Vendor (AP) yang harus dibayar.

2. **Efficiency Metrics**
   - **Order Cycle Time**: Rata-rata waktu dari SO dibuat sampai DO terbit. ("Minggu ini rata-rata 4 jam, lebih cepat dari standar 6 jam").
   - **OTIF Score (On-Time In-Full)**: Persentase pengiriman sukses tanpa retur hari ini.

3. **Historical context**
   - **Seasonality Pattern**: "Biasanya tanggal segini (akhir bulan) order turun, tapi hari ini justru naik 20%. Cek apakah ada promo marketing?"

### 🏟️ Inventory & Warehouse (WH) - "The Space Master"
- **Dead Stock Hunter**: Mengidentifikasi barang bergerak lambat yang memakan *opportunity cost* gudang.
- **Smart Slotting**: Menyarankan pemindahan item *Fast Moving* ke dekat pintu loading.
- **Stockout Forecast**: "Berdasarkan tren Q4, item X akan habis 2 minggu lagi. Pesan sekarang."

### 💰 Finance & Costing (FIN) - "The Auditor"
- **COGS Anomaly**: Mendeteksi jika HPP satu batch produksi melonjak drastis dibanding standar (misal: scrap rate tinggi).
- **Invoice Matching**: Mencocokkan 3-Way (PO, DO, Invoice) dan menandai selisih harga/jumlah.
- **Cashflow Forecaster**: Prediksi arus kas keluar minggu depan berdasarkan termin pembayaran PO aktif.

### 🔧 Maintenance (MRO) - "The Doctor"
- **Predictive Sparepart**: "Mesin Z sudah jalan 500 jam, siapkan bearing tipe Y karena histori menunjukkan akan aus di 600 jam."
- **Total Cost of Ownership (TCO)**: Membandingkan biaya maintenance Mesin A vs Mesin B untuk keputusan peremajaan.

### 👥 HR & Workforce (Data-Only) - "The Talent Scout"
- **Shift Performance Analyzer**: Membandingkan output produksi Shift A vs Shift B dengan mesin yang sama. AI menilai efektivitas tim bukan individu sensisif.
- **Overtime vs Output**: Menganalisa apakah lembur korelasinya positif dengan output, atau malah menurunkan kualitas (fatigue detection via data reject).
- **Skill Gap Matrix**: "Operator X sering error saat pegang Mesin Y, tapi jago di Mesin Z." (Rekomendasi rotasi).

### 🛡️ Audit & Compliance (Internal Control) - "The Watchdog"
- **Ghost Vendor Detection**: Mendeteksi vendor baru yang alamat/rekeningnya mirip dengan karyawan active (potensi conflict of interest).
- **Split PO Alert**: Menandai jika ada 5 PO kecil dibuat beruntun ke vendor yang sama untuk menghindari limit approval manager (Structuring).
- **Segregation of Duties (SoD)**: Warning jika User yang *Create PO* adalah User yang sama dengan yang *Approve Receipt* (Potensi fraud).

### 💎 Customer 360 & Sales Intelligence - "The Strategist"
- **RFM Analysis Otomatis**: Segmentasi customer (*Recency, Frequency, Monetary*) secara live. "Customer Sultan", "Customer Setia", "Customer Mau Kabur".
- **Buying Cycle Prediction**: "Customer A biasanya order bahan X tiap 45 hari. Sekarang sudah hari ke-48 belum ada order. Call sekarang!"
- **Payment Behavior**: Scoring kredit pelanggan berdasarkan sejarah keterlambatan bayar invoice.

### 🎩 Executive Hub (Director Access - WhatsApp)
- **Omni-Channel Access**: Direksi chat ke nomor WA khusus "Sentient Factory" (Private Number).
- **Contoh Query (Apa saja yang bisa ditanyakan?)**:
    - 💰 **Finance**: "Berapa AR (Piutang) macet di atas 90 hari? Siapa customer terbesarnya?"
    - 🏭 **Ops**: "Kenapa Line B output-nya turun 20% kemarin? Apa karena mesin atau orang?"
    - 👥 **HR**: "Siapa Supervisor dengan performa shift terbaik bulan ini?"
    - 🔮 **Simulation/What-If**: "Kalau harga bahan baku naik 10%, margin produk X sisa berapa?" (AI melakukan kalkulasi on-the-fly).

- **Handling Out-of-Topic (Guardrails)**:
    - **General Knowledge**:
        - *User*: "Carikan resep nasi goreng enak."
        - *AI*: "Maaf Pak, saya dedikasi hanya untuk data pabrik & bisnis internal. Namun jika Bapak ingin analisa *costing* katering kantin pabrik, saya bisa bantu hitungkan."
    - **Internet/News**:
        - *User*: "Siapa juara bola semalam?"
        - *AI*: "Maaf Pak, sistem saya **Air-Gapped (Putus Internet)** demi keamanan data rahasia pabrik Bapak. Saya tidak punya akses ke berita luar."

- **Executive Summary Mode**: Jawaban diformat *Bottom Line Up Front* (BLUF).
- **Morning Briefing (07.00)**: "Pak, kemarin produksi 98%. Profit estimasi IDR XX Juta. Isu: Mesin B perlu maintenance."

### 🧪 Quality Control (QC) - "The Standard"
- **QC Summarizer**: Konversi laporan teks inspeksi menjadi skor grading.
- **Trend Drift**: "Awas, ketebalan plastik menurun rata-rata 0.01mm dalam 3 batch terakhir." (Early Warning).

---

---

### 🌐 Hybrid Intelligence (Online Features - Selected)
*Fitur ini aktif karena strategi "Hybrid Secured" dipilih. AI memanfaatkan data eksternal untuk konteks lebih luas.*

1.  **Global Commodity & FX Watch**:
    - **Fungsi**: Memonitor harga komoditas dunia (Baja LME, Minyak, Kapas) dan Kurs USD/IDR real-time.
    - **Impact**: "Pak, harga Tembaga dunia turun 5% hari ini. Saatnya *lock* harga untuk bahan baku kabel bulan depan."
2.  **Supply Chain Risk Radar**:
    - **Fungsi**: Mendeteksi gangguan logistik global (Taufan, Demo Pelabuhan, Perang Jalur Laut).
    - **Impact**: "Kapal pengangkut bahan kimia kita mungkin terlambat 3 hari karena badai di Laut China Selatan. Stok safety perlu dinaikkan."
3.  **Vendor Risk Intelligence**:
    - **Fungsi**: *Background Check* otomatis berita negatif tentang vendor utama.
    - **Impact**: "Warning: Vendor Utama 'PT Maju Jaya' sedang ada berita kasus pailit di media massa. Tahan pembayaran uang muka."
4.  **Regulatory & Compliance Bot**:
    - **Fungsi**: Crawling aturan pemerintah baru (UU Cipta Kerja, Aturan Limbah B3, Pajak).
    - **Impact**: "Ada aturan baru Kemenperin soal standar emisi pabrik. Simulasikan dampaknya ke biaya operasional kita."
5.  **Competitor Market Pulse**:
    - **Fungsi**: Memantau harga/promo kompetitor di marketplace atau berita rilis produk mereka.
    - **Impact**: "Kompetitor X baru saja rilis produk sejenis dengan harga 10% lebih murah. Perlu revisi strategi diskon?"

---

## 3. Matriks Distribusi Fitur (Channel Delivery)

Tabel berikut memetakan bagaimana setiap fitur "dinikmati" oleh user, apakah visual (Dashboard), percakapan (WhatsApp), analisa teks mendalam, atau laporan pasif (Email).

| Fitur / Modul        |  🖥️ Dashboard (Visual)   |   💬 WhatsApp (Chat/Brief)   | 📝 Text Analysis (Reasoning) |    📩 Email (Report/Alert)    |
| :------------------- | :---------------------: | :-------------------------: | :-------------------------: | :--------------------------: |
| **Sales Order (SO)** |   Grafik Winning Rate   |   Notif Order Besar Masuk   |  Saran Margin & Cross-sell  |     Alert Order Anomaly      |
| **Delivery (DO)**    |  Peta Tracking & Rute   |    Cek Status Pengiriman    |   Prediksi Delay (Sebab)    | Laporan Keterlambatan Harian |
| **Purchase (PO)**    |    Vendor Scorecard     |    Tanya Harga Historis     |  Talking Points Negosiasi   |      Draft PO Otomatis       |
| **Inventory (WH)**   |   Peta Heatmap Gudang   |      Tanya Stok Barang      |    Rekomendasi Slotting     |      Warning Dead Stock      |
| **Production (WO)**  |  OEE & Yield Real-time  | Tanya "Kenapa Mesin Error?" |   RAG Search Manual Book    |     Laporan Shift Harian     |
| **Finance (FIN)**    |   Cashflow Projection   |      Tanya Saldo AR/AP      |    Deteksi COGS Anomaly     |              -               |
| **HR & Audit**       | Shift Performance Trend |              -              | Deteksi Fraud/Ghost Vendor  |   Alert Pelanggaran (SoD)    |
| **Customer 360**     | RFM Segmentation Chart  |    Cek Profil Pelanggan     |     Analisa Churn Risk      |              -               |
| **Executive Hub**    |   Top Level KPI View    | **Morning Briefing & Q&A**  |  Executive Summary (BLUF)   |   Laporan Profit Mingguan    |

---

## 4. Deep Dive Use Case Catalog (10+ Skenario per Modul)

Berikut adalah daftar lengkap contoh interaksi dan fitur spesifik untuk setiap modul.

### 📦 Sales Order (SO) - Sales Intelligence
1.  **[WA]** "Berapa winning rate saya bulan ini dibanding bulan lalu?"
2.  **[AI]** Prediksi probabilitas deal: "Customer A minta diskon 15%, sejarahnya deal di 10% atau 12%?"
3.  **[Dash]** Notifikasi **"Dead Leads"**: Quote yang sudah 30 hari tanpa respon.
4.  **[Email]** Alert Sales Target: "Peringatan, sisa 5 hari kerja, target masih kurang 20%."
5.  **[AI]** Rekomendasi Bundle: "Customer beli Mesin A, tawarkan juga Service Pack B (Conversion rate 40%)."
6.  **[WA]** "Cek stok barang X apakah cukup untuk order 5000 unit customer Y?"
7.  **[Dash]** Analisa Anomali Harga: Salesman memberikan harga di bawah HPP.
8.  **[Email]** Laporan Churn: Daftar top 10 customer yang biasanya order bulan ini tapi belum ada PO.
9.  **[AI]** Smart Searching: "Cari SO tahun lalu yang itemnya mirip dengan spesifikasi ini."
10. **[WA]** "Buatkan draft penawaran resmi untuk PT X ref penawaran sebelumnya."

### 🚚 Delivery Order (DO) - Logistics Optimization
1.  **[WA]** "Status pengiriman DO-12345 sudah sampai mana?"
2.  **[AI]** Delay Prediction: "Pengiriman ke Surabaya berisiko telat karena macet di jalur Pantura (External Data)."
3.  **[Dash]** **Fleet Utilization**: Truk mana yang sering jalan kosong (isi <50%)?
4.  **[Email]** Daily Late Report: Daftar DO yang belum terkirim melebih SLA 24 jam.
5.  **[AI]** Route Opt: "Gabungkan pengiriman Toko A dan Toko B karena satu jalur, hemat bensin 15%."
6.  **[WA]** "Siapa driver yang paling sering telat bulan ini?"
7.  **[Dash]** Cost per Delivery: Analisa biaya kirim per kg barang.
8.  **[AI]** Return Analysis: "Kenapa Toko C sering retur barang setiap hari Jumat?"
9.  **[Email]** Proof of Delivery Missing: Alert DO yang sudah 'Complete' tapi tidak ada foto surat jalan.
10. **[WA]** "Cek apakah Truk B 9099 XZ jadwal service-nya sudah dekat?"

### 🛒 Purchase Order (PO) - Procurement Smart Assistant
1.  **[WA]** "Harga terakhir kita beli Bearing SKF 6205 berapa dan ke siapa?"
2.  **[AI]** Vendor Comparison: "Bandingkan Vendor A dan B dari segi OTD (On Time Delivery) dan Reject Rate."
3.  **[Dash]** **Price Creep**: Grafik kenaikan harga bahan baku perlahan tapi pasti.
4.  **[Email]** Approval Reminder: "Pak, ada 3 PO di atas 100juta menunggu approval Bapak."
5.  **[AI]** Auto-Negotiation Script: "Buatkan email negosiasi ke Vendor X, tekan bahwa volume kita naik 20%."
6.  **[WA]** "Tampilkan top 5 hutang vendor yang jatuh tempo minggu ini."
7.  **[Dash]** Raw Material Coverage: Bahan mana yang stoknya kritis (< 3 hari produksi).
8.  **[AI]** Alternative Part: "Vendor A kosong, siapa vendor lain yang pernah supply barang serupa?"
9.  **[Email]** Vendor Performance Card: Laporan bulanan otomatis ke vendor (Rapor Vendor).
10. **[WA]** "Apakah PO-555 sudah di-approve Pak Bos?"

### 🏟️ Inventory (WH) - Warehouse Efficiency
1.  **[WA]** "Barang Fast Moving apa yang stoknya menipis?"
2.  **[AI]** **Dead Stock Detective**: "Item X sudah 180 hari tidak bergerak. Nilai aset: Rp 50 Juta. Tawarkan diskon?"
3.  **[Dash]** Heatmap Gudang: Area mana yang paling sering diakses forklift (Optimasi Slotting).
4.  **[Email]** Expired Warning: Barang kimia yang akan kadaluarsa dalam 30 hari.
5.  **[AI]** Stock Discrepancy: "Sistem catat 100, tapi cycle count cuma ketemu 95. Cek log transaksi terakhir."
6.  **[WA]** "Posisi pallet barang Y ada di rak nomor berapa?"
7.  **[Dash]** Receiving Bottleneck: Jam berapa gudang paling macet terima barang masuk?
8.  **[AI]** Reorder Point Dynamic: Mengubah batas minimum stock berdasarkan tren musiman.
9.  **[Email]** Space Utilization Report: "Gudang sudah 95% penuh, perlu sewa gudang luar?"
10. **[WA]** "Berapa nilai total inventory kita hari ini?"

### 🏭 Production (WO) - Manufacturing Excellence
1.  **[WA]** "Kenapa output Shift 2 kemarin rendah?"
2.  **[AI]** **Downtime Reason**: "Mesin Packaging sering stop 5 menit karena sensor kotor (Micro-stops)."
3.  **[Dash]** OEE (Overall Equipment Effectiveness) Real-time per Line.
4.  **[Email]** Yield Alert: Laporan batch produksi yang *scrap*-nya di atas 5%.
5.  **[AI]** Knowledge Base (RAG): "Cara mengatasi Error Code E-501 di Mesin CNC Doosan?" (Cari di PDF Manual).
6.  **[WA]** "Kapan estimasi WO-888 selesai? Customer sudah tanya."
7.  **[Dash]** Energy Monitoring: Konsumsi listrik per unit produk.
8.  **[AI]** Maintenance Prediction: "Vibrasi motor A aneh, jadwalkan ganti bearing minggu depan."
9.  **[Email]** Shift Handover Summary: Ringkasan masalah shift sebelumnya untuk shift penerus.
10. **[WA]** "Siapa operator dengan reject rate terendah minggu ini?" (Reward candidate).

### 💰 Finance (FIN) - Financial Health
1.  **[WA]** "Berapa cashflow masuk estimasi minggu depan?"
2.  **[AI]** **COGS Anomaly**: "HPP Batch ini naik 12% karena yield produksi buruk."
3.  **[Dash]** AR Aging: Visualisasi piutang macet yang perlu ditagih agresif.
4.  **[Email]** Unbilled Delivery: Barang sudah kirim tapi Invoice lupa dibuat sales.
5.  **[AI]** Budget vs Actual: "Biaya lembur bulan ini sudah 120% dari budget."
6.  **[WA]** "Berapa total hutang kita ke Vendor Utama?"
7.  **[Dash]** Gross Margin per Customer: Siapa customer yang sebenarnya merugikan?
8.  **[AI]** Fraud Detect: "Ada 2 pembayaran ke nomor rekening baru yang belum terdaftar."
9.  **[Email]** Profitability Report Weekly: Laporan Laba/Rugi kasar mingguan.
10. **[WA]** "Apakah Customer X sudah bayar invoice bulan lalu?"

### 👥 HR & Workforce - People Analytics
1.  **[WA]** "Siapa karyawan yang paling sering lembur tapi output rendah?"
2.  **[AI]** Absenteeism Pattern: "Karyawan A sering izin sakit tiap hari Senin."
3.  **[Dash]** Skill Matrix: Peta keahlian operator berdasarkan mesin yang dikuasai.
4.  **[Email]** Contract Expiry: Peringatan kontrak karyawan PKWT habis 1 bulan lagi.
5.  **[AI]** Training Needs: "Banyak reject di Line 2, jadwalkan training ulang SOP."
6.  **[WA]** "Berapa headcount shift 3 malam ini?"
7.  **[Dash]** Turnover Rate per Divisi.
8.  **[AI]** Sentiment Analysis: (Jika ada survey internal) Rangkuman kepuasan kerja karyawan.
9.  **[Email]** Overtime Violation: Peringatan jam lembur melebihi aturan Depnaker.
10. **[WA]** "Hitungkan estimasi bonus produksi untuk Tim A."

### 🛡️ Audit & Compliance - Internal Control
1.  **[WA]** "Ada transaksi mencurigakan hari ini?"
2.  **[AI]** **Ghost Vendor**: "Vendor Baru PT ABC alamatnya sama dengan Karyawan X."
3.  **[Dash]** User Activity Log: Siapa yang akses data payroll di luar jam kerja?
4.  **[Email]** SoD Violation: Alert user yang membuat PO dan Approve PO sekaligus.
5.  **[AI]** Split PO Detect: "Ada 4 PO nominal 49jt ke vendor sama (hindari limit 50jt)."
6.  **[WA]** "Tampilkan perubahan master data harga jual bulan ini."
7.  **[Dash]** Discount Authority: Sales mana yang paling sering kasih diskon maksimal?
8.  **[AI]** Inventory Adjustment Audit: "Kenapa ada stock adjustment minus 100 unit tanpa keterangan jelas?"
9.  **[Email]** Login Failure Alert: Percobaan login gagal 10x di akun Manager.
10. **[WA]** "Cek apakah ada Backdate Transaction (Transaksi tanggal mundur)?"

### 💎 Customer 360 - CRM Intelligence
1.  **[WA]** "Siapa top 10 customer kita bulan ini?"
2.  **[AI]** **Churn Risk**: "Customer B belum order 45 hari, biasanya rutin 30 hari."
3.  **[Dash]** RFM Quadrant: Peta sebaran customer (Loyal vs Lost).
4.  **[Email]** Birthday/Anniversary: Reminder ucapan selamat hari jadi perusahaan customer.
5.  **[AI]** CLV Visualization: Nilai seumur hidup customer vs biaya akuisisi.
6.  **[WA]** "Produk apa yang paling disukai Customer C?"
7.  **[Dash]** Complaint Tracker: Tren keluhan customer naik/turun.
8.  **[AI]** Next Best Offer: "Tawarkan Produk Baru Z ke Customer Lama Y."
9.  **[Email]** Re-activation Campaign: List customer mati suri untuk difollow up.
10. **[WA]** "Kapan terakhir salesman visit ke Customer D?"

### 🎩 Executive Hub (Director) - Strategic View
1.  **[WA]** "Status Profit & Loss hari ini bagaimana?"
2.  **[AI]** **What-If Scenario**: "Kalau Dolar naik ke Rp 16.500, dampak ke HPP berapa?"
3.  **[Dash]** CEO Dashboard: Satu layar semua KPI merah/hijau.
4.  **[Email]** Monthly Board Deck: Slide presentasi performa bulanan otomatis.
5.  **[AI]** Competitor News: (Jika online) Ringkasan berita kompetitor. *Note: Skip jika offline.*
6.  **[WA]** "Ada isu kritis apa di pabrik sekarang?"
7.  **[Dash]** Cash Runway: Berapa bulan kita bisa survive dengan cash saat ini?
8.  **[AI]** Investment ROI: "Mesin baru Y sudah balik modal belum?"
9.  **[Email]** Fraud Summary: Ringkasan insiden keamanan bulan ini.
10. **[WA]** "Jadwalkan meeting darurat dengan Manajer Produksi." (Integrasi Kalender).

---

## 5. Fundamental Teknis & Filosofi Data

1.  **Non-Intrusive Data Overlay (Lapisan Tak Terlihat)**:
    - **Solusi**: Mirroring data via CDC ke PostgreSQL lokal. ERP Legacy aman, AI bebas berfikir.
    - **Visi**: "Biarkan ERP mencatat transaksi, AI yang menafsirkan maknanya."

2.  **Data Mining over Sensing (Data First, Sensor Later)**:
    - **Filosofi**: "Mengubah data transaksional yang 'tertidur' menjadi aset strategis bernilai tinggi (High ROI), memberikan dampak bisnis instan tanpa beban investasi hardware baru."
    - **Penerapan**: Fitur HR, Audit, dan Customer 360 dibangun 100% dari log transaksi ERP (Client Database), tanpa hardware tambahan. "Data sampah bagi admin adalah emas bagi AI."

3.  **Local First & Privacy (Air-Gapped Core)**:
    - Mac Mini M4 Pro adalah "Benteng Digital". Internet dimatikan (Air-gapped) kecuali untuk jalur komunikasi terenkripsi spesifik (WhatsApp Bridge).

4.  **Zero-Trust Identity**:
    - Akses level Direksi via WhatsApp dibentengi dengan Whitelist Nomor dan 2FA. Asumsi HP direksi bisa hilang/dibajak, jadi sistem harus tetap aman.

---

## 4. Implementasi Hardware (Split Architecture)

### Node A: The Brain (Mac Mini M4 Pro - 64GB)
**Fokus**: Dedicated AI Inference (Otak Berpikir).
- **Workload**: Menjalankan **Qwen-2.5-72B-Instruct (Int4)**.
- **Role**: Hanya menerima raw text input (prompt) dan mengembalikan text output (insight). Tidak ada koneksi internet langsung.

### Node B: The Body (Secondary Server / Mini PC)
**Fokus**: Database, App, & Communication Gateway.
- **Workload**:
    - **PostgreSQL**: Data Warehouse utama data ERP.
    - **Node-RED**: Logic orchestrator.
    - **WhatsApp Gateway (Baileys)**: Menjembatani request WA direksi ke Node A.
    - **Next.js Dashboard**: UI untuk Manager operasional.
- **Traffic Control**: Node B yang memegang kendali "Pintu Gerbang" internet (untuk kirim WA), melindungi Node A yang tetap steril.

---

## 5. Opsi Strategis: Hybrid Connectivity (Internet Aware)

*Menjawab pertanyaan: "Apakah aman jika online? Apa untungnya?"*

Secara default, visi misi kita adalah **100% Lokal (Air-Gapped)** demi keamanan maksimal. Namun, membuka koneksi internet (Hybrid) secara terkontrol memberikan benefit eksponensial dengan risiko yang bisa dimitigasi.

### A. Benefit Koneksi Internet (Why Online?)
1.  **External Context Enrichment**:
    *   *Offline*: AI hanya tahu "Penjualan turun".
    *   *Online*: AI tahu "Penjualan turun karena **Kompetitor X baru saja launching produk murah** atau **Tren pasar global sedang lesu**."
    *   *Fitur*: Competitor monitoring, Commodity Price tracking (Harga minyak/baja dunia).
2.  **Remote Warning System**:
    *   Direksi bisa terima notifikasi WA saat sedang dinas luar kota / luar negeri (tanpa VPN ribet).
3.  **Advanced Reasoning (Cloud Fallback)**:
    *   Jika Qwen-72B lokal mentok, kasus yang sangat sulit bisa di-anonimkan dan dikirim ke OpenAI o1/Claude 3.5 Sonnet untuk "Second Opinion" (Hanya kasus kritis).
4.  **Multi-Plant Sync**:
    *   Jika punya 2 pabrik, management bisa membandingkan performa Pabrik A vs Pabrik B secara real-time di satu dashboard pusat.

### B. Risiko & Mitigasi (Is it Safe?)

| Risiko                        | Mitigasi (Zero Trust Architecture)                                                                                                      |
| :---------------------------- | :-------------------------------------------------------------------------------------------------------------------------------------- |
| **Data Kebocoran**            | **Outbound Only**: Server hanya boleh *request* keluar (tarik berita/kirim WA), tidak boleh menerima *incoming connection* sembarangan. |
| **Serangan Ransomware**       | **Dedicated VLAN**: Mac Mini dipisah total dari jaringan kantor umum. Hanya port spesifik yang dibuka.                                  |
| **AI Halusinasi Konten Luar** | **Switch Context**: AI dilarang akses info selebriti/politik. Whitelist domain hanya ke situs finansial/berita industri.                |

**Keputusan Strategis**:
Jika ingin tetap "Purist", pilih **Offline**. Jika ingin "Competitive Advantage" lebih tinggi, pilih **Hybrid Secured**.

---

## 6. Rules for AI Assistant (You)

- **Persona (Dual Role)**:
    1.  **Technical Architect**: Saat bicara teknis (Node-RED, SQL, Python).
    2.  **Business Intelligence Analyst**: Saat menjelaskan fitur (Audit, HR, Finance). Anda harus jeli melihat *hidden value* dari data.
- **Strict Constraints**:
    - **NO NEW SENSORS**: Haram menyarankan pemasangan CCTV, IoT, atau alat fisik baru. Fokus gali data SQL yang ada.
    - **OFFLINE DEFAULT**: Asumsikan server tidak bisa akses API luar (OpenAI/Google). Gunakan Local LLM.
- **Communication Style**:
    - **Untuk Direksi (Executive Mode)**: Padat, Singkat, ada Nominal Uang (IDR). "Bottom Line Up Front".
    - **Untuk User Teknis**: Detail, logis, step-by-step.
- **Optimization**: Selalu pertimbangkan "Ram Economy". Jangan menyarankan fitur berat jika nilai bisnisnya kecil.

---

## 7. Development Hierarchy & Cycle (SDLC Rules)

### 7.1 Development Flow (Waterfall-Locked)

```
┌─────────────────────────────────────────────────────────────────────┐
│                                                                     │
│   ┌──────────────┐                                                  │
│   │  1. CONCEPT  │ ◄─── .cursorrules, README                        │
│   └──────┬───────┘                                                  │
│          ▼                                                          │
│   ┌──────────────┐                                                  │
│   │ 2. ARCHITECT │ ◄─── architecture.md, tech stack                 │
│   └──────┬───────┘                                                  │
│          ▼                                                          │
│   ┌──────────────────────────────────────┐                          │
│   │ 3. ANALYSIS (Parallel)               │                          │
│   │   ├── system_analysis.md (ERD, Flow) │                          │
│   │   └── bpa-1/2/3.md (Business Process)│                          │
│   └──────┬───────────────────────────────┘                          │
│          ▼                                                          │
│   ┌──────────────┐                                                  │
│   │  4. UI/UX    │ ◄─── ui-design.md, wireframes                    │
│   └──────┬───────┘                                                  │
│          ▼                                                          │
│   ┌──────────────────────────────────────┐                          │
│   │ 5. IMPLEMENTATION (Parallel)         │                          │
│   │   ├── Backend (API, NestJS)          │                          │
│   │   ├── Frontend (Metronic, React)     │                          │
│   │   ├── AI Service (Python, Qwen)      │                          │
│   │   └── Infra Setup (Docker, NATS)     │                          │
│   └──────┬───────────────────────────────┘                          │
│          ▼                                                          │
│   ┌──────────────┐                                                  │
│   │  6. QA/TEST  │ ◄─── Test by BPA Criteria                        │
│   └──────────────┘                                                  │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

### 7.2 Document Hierarchy (Parent-Child Lock)

| Level | Document           | Parent                | Child                    |
| ----- | ------------------ | --------------------- | ------------------------ |
| L1    | .cursorrules       | -                     | All docs                 |
| L2    | architecture.md    | .cursorrules          | system_analysis, BPA     |
| L3a   | system_analysis.md | architecture          | ui-design, backend code  |
| L3b   | bpa-1/2/3.md       | architecture          | ui-design, QA test cases |
| L4    | ui-design.md       | system_analysis + BPA | frontend code            |
| L5    | Source Code        | ui-design             | -                        |
| L6    | QA Test Cases      | BPA                   | -                        |

### 7.3 Change Management Rules (CRITICAL)

**Rule 1: Backward Propagation**
Jika ada perubahan di level bawah yang mempengaruhi spesifikasi, WAJIB update parent document.

```
Contoh:
- Frontend menemukan field baru diperlukan
  → Update ui-design.md
  → Update system_analysis.md (ERD)
  → Update bpa-X.md (jika ada impact ke business flow)
```

**Rule 2: Forward Notification**
Jika parent document berubah, SEMUA child harus di-review dan di-update.

```
Contoh:
- BPA-1 menambah fitur baru "2FA Login"
  → Update ui-design.md (add 2FA wireframe)
  → Update system_analysis.md (add 2FA tables)
  → Notify backend team
  → Add to QA test cases
```

**Rule 3: Cross-Reference Mandatory**
Setiap document HARUS memiliki link ke parent-nya di bagian header.

**Rule 4: Version Lock**
Sebelum development phase dimulai, SEMUA analysis documents HARUS di-freeze.
- Freeze = Tidak boleh ada perubahan tanpa formal change request.
- Setiap perubahan post-freeze HARUS mencantumkan "CHANGE LOG" di document.

### 7.4 Change Request Format

Jika ada perubahan saat development, gunakan format ini:

| Field         | Value                                  |
| ------------- | -------------------------------------- |
| CR ID         | CR-2024-001                            |
| Requester     | Name/Role                              |
| Date          | 2024-12-27                             |
| Affected Docs | bpa-1.md, ui-design.md                 |
| Description   | Add 2FA feature to login flow          |
| Justification | Security requirement from client       |
| Impact        | Medium - need new table, new UI screen |

**Approval Required:** Architect, Product Owner, QA Lead

### 7.5 QA Traceability

QA HARUS trace setiap test case ke BPA document:

| Test Case | BPA Reference | Section       | Criteria                              |
| --------- | ------------- | ------------- | ------------------------------------- |
| TC-001    | bpa-1.md      | 2.1 Login     | User can login with valid credentials |
| TC-002    | bpa-1.md      | 2.1 Login     | Show error on invalid password        |
| TC-003    | bpa-2.md      | 2.2 SO Create | SO number auto-generated              |

### 7.6 Development Checklist

**Before UI/UX Phase:**
- system_analysis.md approved
- BPA-1/2/3.md approved
- ERD finalized

**Before Implementation Phase:**
- ui-design.md approved
- API endpoints defined in BPA
- Dummy data available in Section 1.8

**Before QA Phase:**
- All features implemented per BPA
- Unit tests passed
- Test cases derived from BPA acceptance criteria

---

## 8. SDLC Standards (Software Development Life Cycle)

### 8.1 Phase Definitions

| Phase               | Objective                | Duration   | Owner             |
| ------------------- | ------------------------ | ---------- | ----------------- |
| **1. Requirements** | Gather business needs    | 1-2 weeks  | Product Owner     |
| **2. Analysis**     | Define system specs      | 1-2 weeks  | System Analyst    |
| **3. Design**       | Create architecture & UI | 1-2 weeks  | Architect + UI/UX |
| **4. Development**  | Build the system         | 4-8 weeks  | Dev Team          |
| **5. Testing**      | Validate quality         | 2-3 weeks  | QA Team           |
| **6. Deployment**   | Release to production    | 1 week     | DevOps            |
| **7. Maintenance**  | Ongoing support          | Continuous | Support Team      |

### 8.2 Phase Deliverables

**Phase 1: Requirements**
- [ ] Project Charter / Brief
- [ ] Stakeholder List
- [ ] High-level Requirements Document
- [ ] Scope Definition (In/Out of Scope)

**Phase 2: Analysis**
- [ ] Business Process Analysis (BPA-1, BPA-2, BPA-3)
- [ ] System Analysis Document (ERD, Data Flow)
- [ ] Use Case Diagrams
- [ ] Non-Functional Requirements (NFR)

**Phase 3: Design**
- [ ] System Architecture Document
- [ ] Database Design (DDL Scripts)
- [ ] API Specification (OpenAPI/Swagger)
- [ ] UI/UX Design (Wireframes, Prototypes)
- [ ] Security Design (Auth Flow, RBAC)

**Phase 4: Development**
- [ ] Source Code (Repository)
- [ ] Database Migration Scripts
- [ ] API Implementations
- [ ] Frontend Components
- [ ] Unit Tests (Min 70% coverage)
- [ ] Code Review Completed

**Phase 5: Testing**
- [ ] Test Plan Document
- [ ] Test Cases (mapped to BPA)
- [ ] Bug Reports
- [ ] Test Summary Report
- [ ] UAT Sign-off

**Phase 6: Deployment**
- [ ] Deployment Checklist
- [ ] Release Notes
- [ ] Rollback Plan
- [ ] Production Verification

**Phase 7: Maintenance**
- [ ] Incident Reports
- [ ] Change Requests
- [ ] Performance Reports

### 8.3 Quality Gates (Go/No-Go Criteria)

```
┌─────────────────────────────────────────────────────────────────────┐
│                        QUALITY GATES                                │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│   Gate 1: Requirements → Analysis                                   │
│   ├── All stakeholders signed off                                   │
│   └── Scope document approved                                       │
│                                                                     │
│   Gate 2: Analysis → Design                                         │
│   ├── BPA documents approved                                        │
│   ├── ERD finalized                                                 │
│   └── NFR documented                                                │
│                                                                     │
│   Gate 3: Design → Development                                      │
│   ├── Architecture approved                                         │
│   ├── UI/UX approved by stakeholder                                 │
│   └── API spec completed                                            │
│                                                                     │
│   Gate 4: Development → Testing                                     │
│   ├── All features code complete                                    │
│   ├── Unit tests passed (>70%)                                      │
│   ├── Code review completed                                         │
│   └── No critical/blocker bugs                                      │
│                                                                     │
│   Gate 5: Testing → Deployment                                      │
│   ├── All test cases passed                                         │
│   ├── UAT signed off                                                │
│   ├── Performance test passed                                       │
│   └── Security scan passed                                          │
│                                                                     │
│   Gate 6: Deployment → Go-Live                                      │
│   ├── Production smoke test passed                                  │
│   ├── Monitoring configured                                         │
│   └── Support team trained                                          │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

### 8.4 Roles & Responsibilities (RACI Matrix)

| Activity               |  PO   | Architect |  Dev  |  QA   | DevOps |
| ---------------------- | :---: | :-------: | :---: | :---: | :----: |
| Requirements Gathering | **R** |     C     |   I   |   I   |   I    |
| System Analysis        |   A   |   **R**   |   C   |   C   |   I    |
| Architecture Design    |   A   |   **R**   |   C   |   I   |   C    |
| UI/UX Design           |   A   |     C     |   C   |   I   |   I    |
| Backend Development    |   A   |     C     | **R** |   I   |   I    |
| Frontend Development   |   A   |     C     | **R** |   I   |   I    |
| Unit Testing           |   I   |     I     | **R** |   C   |   I    |
| Integration Testing    |   I   |     C     |   C   | **R** |   I    |
| UAT Execution          | **R** |     I     |   C   |   C   |   I    |
| Deployment             |   A   |     C     |   C   |   I   | **R**  |
| Production Support     |   C   |     C     |   C   |   I   | **R**  |

**Legend:** R = Responsible, A = Accountable, C = Consulted, I = Informed

### 8.5 Code Standards

**Naming Conventions:**

| Type             | Convention  | Example             |
| ---------------- | ----------- | ------------------- |
| Variables        | camelCase   | `customerName`      |
| Constants        | UPPER_SNAKE | `MAX_RETRY_COUNT`   |
| Functions        | camelCase   | `calculateTotal()`  |
| Classes          | PascalCase  | `SalesOrderService` |
| Database Tables  | snake_case  | `t_sales_order`     |
| Database Columns | snake_case  | `order_date`        |
| API Endpoints    | kebab-case  | `/api/sales-orders` |

**Code Review Checklist:**
- [ ] Follows naming conventions
- [ ] No hardcoded values (use config/env)
- [ ] Error handling implemented
- [ ] Logging added for critical paths
- [ ] No security vulnerabilities
- [ ] Unit tests written
- [ ] Comments for complex logic
- [ ] No console.log in production code

### 8.6 Git Workflow

**Branch Strategy:**
```
main (production)
  └── develop (integration)
        ├── feature/SF-001-login-page
        ├── feature/SF-002-dashboard
        └── bugfix/SF-003-login-error
```

**Branch Naming:**
- `feature/SF-XXX-short-description`
- `bugfix/SF-XXX-short-description`
- `hotfix/SF-XXX-short-description`

**Commit Message Format:**
```
[SF-XXX] Type: Short description

- Detail 1
- Detail 2

Types: feat, fix, docs, style, refactor, test, chore
```

**Pull Request Rules:**
- [ ] At least 1 reviewer approval
- [ ] All CI checks passed
- [ ] No merge conflicts
- [ ] PR description filled

### 8.7 Sprint Cycle

**Sprint Duration:** 2 weeks

| Day     | Activity                      |
| ------- | ----------------------------- |
| Day 1   | Sprint Planning (2-4 hours)   |
| Day 2-9 | Development                   |
| Day 5   | Mid-Sprint Review (30 min)    |
| Day 10  | Code Freeze, Testing          |
| Day 10  | Sprint Review (1 hour)        |
| Day 10  | Sprint Retrospective (1 hour) |

**Sprint Ceremonies:**
1. **Daily Standup**: 15 min, setiap hari jam 09:00
2. **Sprint Planning**: Awal sprint, prioritas backlog
3. **Sprint Review**: Demo ke stakeholder
4. **Retrospective**: Evaluasi tim internal

### 8.8 Definition of Done (DoD)

Sebuah fitur dianggap "Done" jika:

- [ ] Code complete dan push ke repository
- [ ] Unit tests written dan passed
- [ ] Code review approved
- [ ] Documentation updated (jika perlu)
- [ ] QA testing passed
- [ ] No critical/major bugs open
- [ ] Deployed to staging environment
- [ ] PO/Stakeholder approved

### 8.9 Bug Severity Classification

| Severity     | Description                 | Response Time | Resolution Time |
| ------------ | --------------------------- | ------------- | --------------- |
| **Critical** | System down, data loss      | 1 hour        | 4 hours         |
| **Major**    | Feature not working         | 4 hours       | 24 hours        |
| **Minor**    | UI issue, workaround exists | 24 hours      | 1 week          |
| **Low**      | Cosmetic, enhancement       | As scheduled  | Next sprint     |

### 8.10 Documentation Standards

**Required Documentation:**

| Document           | Owner       | Update Frequency      |
| ------------------ | ----------- | --------------------- |
| README.md          | Dev Lead    | Every release         |
| API Docs (Swagger) | Backend Dev | Every endpoint change |
| Database Schema    | Architect   | Every migration       |
| User Manual        | Tech Writer | Every major feature   |
| Release Notes      | PM          | Every release         |

**Documentation Format:**
- Use Markdown for all docs
- Include version number and last updated date
- Link to related documents
- Include examples for API docs
