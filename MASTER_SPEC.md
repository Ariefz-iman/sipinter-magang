# MASTER SPECIFICATION & GOVERNANCE ROADMAP
## Sistem Terintegrasi Evaluasi Pembelajaran (Assessment-Centric LAMS)
### Pusat Pengembangan Pendidikan (Pusbangdik) — Poltekkes Kemenkes Tasikmalaya

---

## 1. Tata Kelola & Dokumen Otoritatif

Sistem Terintegrasi Evaluasi Pembelajaran dirancang untuk mengotomasi seluruh siklus evaluasi pembelajaran berbasis luaran (*Outcome-Based Education* / OBE), asesmen multi-metode (kampus, laboratorium, dan wahana klinik/lapangan), buku nilai terpadu, pemantauan penjaminan mutu berkala (Siklus PPEPP), dan penyediaan bukti akreditasi otomatis berstandar **LAM-PTKes Kriteria 3**.

### 1.1 Status Implementasi Roadmap
- **Phase 0: Workspace & Project Foundation** — **SELESAI & DISETUJUI**
- **Phase 1: Academic Foundation** (Kurikulum, CPL, MK, CPMK/Sub-CPMK, RPS 16 Sesi, Rombel, Kelas Penawaran) — **SELESAI & DISETUJUI**
- **Phase 2: Assessment Planning & Blueprint** (Rencana Asesmen, Bobot Komponen, Kisi-kisi Ujian, Bank Soal & Naskah, Rubrik, Approval Engine) — **SELESAI & DISETUJUI**
- **Phase 3: Assessment Delivery & Execution** (Penjadwalan Asesmen, CBT/Quiz, Penugasan, Autograding, Grading Queue Rubrik, Umpan Balik) — **SELESAI & DISETUJUI**
- **Phase 4: Clinical / Field Practice & Internship Module** (Wahana Fasyankes, Unit Praktik, Rotasi Stase, CI & DPL, Logbook De-identifikasi H+1, Dual Sign-off) — **SELESAI & DISETUJUI**
- **Phase 5: Central Gradebook & Outcome-Based Learning Analytics** (Buku Nilai Terpadu, Multi-Assessor Strict Gate, Konversi Nilai Skala Konfiguratif, Rekalkulasi Sinkron Transaksional, Capaian CPMK/CPL, Early Warning & Dosen PA) — **SELESAI & DISETUJUI**
- **Phase 6: CQI, Monev Pembelajaran & Siklus PPEPP + Student Support / Remedial** — **DALAM PENGERJAAN (AUTONOMOUS WORKFLOW)**
- **Phase 7: Assessment Quality, QA & Continuous Improvement** — **DISETUJUI UNTUK DIEKSEKUSI SECARA AUTONOMOUS**
- **Phase 8: Accreditation Evidence Center & Reporting** — **DISETUJUI UNTUK DIEKSEKUSI SECARA AUTONOMOUS**
- **Phase 9: Integration, Hardening & UAT Readiness** — **DISETUJUI UNTUK DIEKSEKUSI SECARA AUTONOMOUS (FINAL GATE)**

---

## 2. Otorisasi Alur Mandiri (Autonomous Workflow) — Mulai Phase 6

Berdasarkan penetapan Product Owner, tata kelola proyek bertransisi ke **Autonomous Implementation Workflow**:

```
Analyze → Plan → Implement → Migrate → Test → Fix → Re-test → Build → Document → Walkthrough
```

Agen berwenang mengambil keputusan teknis rutin tanpa perlu menunggu persetujuan perantara sepanjang memenuhi kriteria:
1. Konsisten dengan `MASTER_SPEC.md` dan standar LAM-PTKes Kriteria 3.
2. Mempertahankan aturan bisnis akademik dan tata kelola versi (*historical/version integrity*).
3. Mempertahankan pembatasan hak akses berbasis peran dan cakupan (*Role + Scope authorization*).
4. Tidak mengubah rumus kalkulasi nilai dan capaian pembelajaran yang telah disetujui.
5. Tidak memperluas ruang lingkup di luar arsitektur master.

### Kondisi Berhenti Wajib (Mandatory PO Stop Conditions)
Agen hanya akan berhenti dan meminta keputusan eksplisit Product Owner jika usulan perubahan memengaruhi:
1. Kebijakan akademik atau institusional (*institutional academic policy*);
2. Rumus penilaian atau aturan konversi nilai;
3. Metodologi perhitungan ketercapaian CPMK/CPL;
4. Aturan kelulusan asesmen atau kompetensi klinis;
5. Kewenangan peran atau alur otoritas persetujuan (*approval authority*);
6. Kebijakan privasi/keamanan data medis atau data sensitif akademik;
7. Migrasi data yang bersifat destruktif atau ireversibel;
8. Ekspansi ruang lingkup besar di luar spesifikasi;
9. Konflik arsitektural yang belum terselesaikan dengan fase sebelumnya.

---

## 3. Spesifikasi Phase 6: CQI, Monev Pembelajaran & PPEPP Cycle + Student Support / Remedial

### 3.1 Prinsip Desain Monev Pembelajaran Transaksional
Sistem Monev pembelajaran **BUKAN sistem formulir manual**, melainkan ekstraksi otomatis dari bukti transaksi akademik aktual:
$$\text{Academic Activity} \to \text{Transactional Data} \to \text{Monev Evidence} \to \text{Finding} \to \text{RTL} \to \text{Verification} \to \text{Improvement}$$

### 3.2 Titik Kendali (Configurable Checkpoints)
Titik pemantauan berkala dapat dikonfigurasi per periode akademik/program studi:
- **Checkpoint Pra-Perkuliahan / Minggu 2–3 (Kesiapan Pembelajaran):**
  - Ketersediaan dan status persetujuan RPS versi `APPROVED`.
  - Ketersediaan dan kelengkapan Rencana Asesmen (`assessment_plans`) dengan total bobot 100.00%.
  - Kesiapan kisi-kisi ujian dan butir instrumen pada Bank Soal.
- **Checkpoint Tengah Semester / Minggu 7–8 (Kemajuan Pembelajaran & UTS):**
  - Realisasi pelaksanaan asesmen formatif dan UTS berbanding jadwal Rencana Asesmen.
  - Persentase penyelesaian penilaian oleh dosen/penilai pada antrean penilaian (*Grading Queue*).
  - Identifikasi mahasiswa dengan peringatan dini (*Early Warning: CPMK < 60.00 atau IPK < 2.75*).
- **Checkpoint Akhir Semester / Minggu 14–16 (Evaluasi Hasil & Ketercapaian):**
  - Ketuntasan buku nilai (*Gradebook Status: FINALIZED / LOCKED*).
  - Analisis ketercapaian CPMK dan CPL program studi.
  - Rekapitulasi ketuntasan stase praktik klinik dan logbook H+1.

### 3.3 Temuan Monev & Rencana Tindak Lanjut (RTL / PPEPP)
- **Temuan (Findings):** Kategori temuan (Kesiapan RPS, Pelaksanaan Asesmen, Ketercapaian CPMK, Kepatuhan Logbook Klinik).
- **Akar Masalah (Root Cause):** Dokumentasi analisis akar masalah (metode pengajaran, beban belajar, instrumen, dll.).
- **Rencana Tindak Lanjut (RTL):** Tindakan perbaikan terencana, penanggung jawab (PIC: Dosen/Koordinator/Kaprodi), tenggat waktu perbaikan, bukti penyelesaian, dan verifikasi/penutupan oleh GKM/Kaprodi.
- **Siklus PPEPP:** Penetapan Standar $\to$ Pelaksanaan $\to$ Evaluasi $\to$ Pengendalian $\to$ Peningkatan.

### 3.4 Alur Pendampingan & Remedial Mahasiswa (Student Support / Remedial Engine)
Terintegrasi erat dengan Early Warning Phase 5:
$$\text{Alert CPMK/Asesmen} \to \text{Identifikasi} \to \text{Umpan Balik} \to \text{Intervensi Dosen PA} \to \text{Remedial/Reassessment} \to \text{Hasil Baru} \to \text{Follow-up}$$
- Menjaga bukti nilai asli (*original raw scores immutable*).
- Menyimpan riwayat sesi remedial dan nilai hasil perbaikan dengan rekam jejak audit transparan tanpa menghapus bukti historis.

---

## 4. Rencana Phase 7, 8, dan 9

- **Phase 7: Assessment Quality, QA & Continuous Improvement:** Evaluasi pemangku kepentingan (dosen, mahasiswa, CI), analisis butir soal (tingkat kesukaran, daya pembeda), evaluasi kurikulum berkala.
- **Phase 8: Accreditation Evidence Center & Reporting:** Agregator bukti LAM-PTKes Kriteria 3 otomatis (*Activity $\to$ Data $\to$ Evidence*), penyusunan matriks kriteria, dan ekspor laporan resmi.
- **Phase 9: Integration, Hardening & UAT Readiness:** Verifikasi matriks izin, uji isolasi Role+Scope, uji reproduktifitas kalkulasi, uji ketahanan beban, peninjauan responsivitas mobile, dan penyusunan Final Walkthrough.

