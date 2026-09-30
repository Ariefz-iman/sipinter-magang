# Panduan & Protokol User Acceptance Testing (UAT)
## Sistem Terintegrasi Evaluasi Pembelajaran (Assessment-Centric LAMS)
### Pusbangdik — Politeknik Kesehatan Kementerian Kesehatan Tasikmalaya

Dokumen ini merupakan panduan resmi pelaksanaan pengujian penerimaan pengguna (**User Acceptance Testing / UAT**) berbasis skenario akademik riil (fiktif namun realistis) untuk membuktikan kesiapan fungsional, integritas penilaian, tata kelola mutu (Siklus PPEPP), dan keterpaduan bukti akreditasi berstandar **LAM-PTKes Kriteria 3**.

---

## 1. Layanan Prasyarat & Variabel Lingkungan (*Environment Variables*)

### 1.1 Layanan Prasyarat
- **Node.js**: v20.x atau v22.x LTS (`node -v`)
- **PNPM**: v9.x (`pnpm -v`)
- **Docker Desktop**: Untuk menjalankan PostgreSQL dan Redis secara lokal via `docker-compose up -d` (atau PostgreSQL 15+ native).

### 1.2 Menjalankan Basis Data & Redis via Docker
Jika menggunakan Docker (Rekomendasi Utama):
```bash
# Menjalankan PostgreSQL 16 dan Redis 7 di latar belakang
docker-compose up -d
```
Jika menggunakan PostgreSQL lokal (macOS/Homebrew):
```bash
brew services start postgresql@16
```

### 1.3 Konfigurasi `.env`
Salin template konfigurasi `.env.example` ke file `.env` di root direktori proyek:
```bash
cp .env.example .env
```

Isi konfigurasi standar:
```env
DATABASE_URL=postgres://sep_user:sep_password@localhost:5432/sistem_evaluasi_db
PORT=4000
CORS_ORIGIN=http://localhost:3000
JWT_SECRET=super-secret-jwt-key-change-in-production-sep-2026
NODE_ENV=development
NEXT_PUBLIC_API_URL=http://localhost:4000/api/v1
```

---

## 2. Prosedur Migrasi, Reset & Penyemaian Data UAT (*Seed Procedure*)

### 2.1 Menyiapkan Basis Data Bersih
Jika pengujian UAT dimulai dari awal atau ingin diulang (*fresh state*):
```bash
# 1. Reset skema database & jalankan migrasi 98 tabel relasional
pnpm db:reset

# 2. Menyemai seluruh data master, akun UAT, kurikulum, asesmen, dan stase klinik
pnpm db:seed:uat
```

### 2.2 Prosedur Reset Cepat untuk Pengulangan Skenario
Jika tester ingin mengembalikan kondisi data awal tanpa menghapus struktur tabel:
```bash
pnpm db:reset && pnpm db:seed:uat
```

---

## 3. Langkah Menjalankan Aplikasi Lengkap Secara Lokal

Jalankan perintah berikut di terminal:

```bash
# Terminal 1: Menjalankan Backend NestJS API (Port 4000)
pnpm --filter @sep/api dev

# Terminal 2: Menjalankan Frontend Next.js Web Dashboard (Port 3000)
pnpm --filter @sep/web dev
```

- **Aplikasi Web Frontend**: [http://localhost:3000](http://localhost:3000)
- **Halaman Login**: [http://localhost:3000/login](http://localhost:3000/login)
- **Dokumentasi REST API Swagger**: [http://localhost:4000/api/docs](http://localhost:4000/api/docs)

---

## 4. Akun Pengguna UAT per Peran (*Test Accounts Directory*)

Seluruh akun demo menggunakan kata sandi terstandarisasi yang sama: **`Pusbangdik2026!`**

| No | Peran Pengguna (Role) | Email Login | Kata Sandi | Lingkup Kewenangan (Scope) | Pejabat / Pengguna Demo |
| :---: | :--- | :--- | :--- | :--- | :--- |
| 1 | **Super Admin** | `admin@polkkestsm.ac.id` | `Pusbangdik2026!` | `INSTITUTION` (Poltekkes Tsm) | Administrator Pusbangdik |
| 2 | **Admin Akademik** | `admin.akademik@polkkestsm.ac.id` | `Pusbangdik2026!` | `INSTITUTION` (Poltekkes Tsm) | Siti Aminah, S.Kom. |
| 3 | **Pimpinan / Direktur** | `direktur@polkkestsm.ac.id` | `Pusbangdik2026!` | `INSTITUTION` (Poltekkes Tsm) | Dr. Hj. Ani Radiati, M.Kes. |
| 4 | **Ketua Prodi (Kaprodi)** | `kaprodi.rmik@polkkestsm.ac.id` | `Pusbangdik2026!` | `STUDY_PROGRAM` (D3 RMIK) | Dra. Hj. Lilis Suryani, M.Kes. |
| 5 | **Ketua Mutu (GKM)** | `gkm.officer@polkkestsm.ac.id` | `Pusbangdik2026!` | `ORGANIZATIONAL_UNIT` (Jurusan RMIK) | Hj. Nining Nurhayati, M.Kes. |
| 6 | **Koordinator MK** | `koordinator.mk@polkkestsm.ac.id` | `Pusbangdik2026!` | `STUDY_PROGRAM` (D3 RMIK) | Asep Ridwan, M.Tr.ID |
| 7 | **Dosen Pengampu** | `dosen.pengampu@polkkestsm.ac.id` | `Pusbangdik2026!` | `STUDY_PROGRAM` (D3 RMIK) | Arief Rahmansyah, M.Kom. |
| 8 | **Reviewer / Validator** | `reviewer.soal@polkkestsm.ac.id` | `Pusbangdik2026!` | `STUDY_PROGRAM` (D3 RMIK) | Dr. Irwan Setiawan, M.Sc. |
| 9 | **Dosen PA** | `dosen.pa@polkkestsm.ac.id` | `Pusbangdik2026!` | `STUDY_PROGRAM` (D3 RMIK) | Drs. Budi Hermanto, M.Kes. |
| 10 | **Dosen Pembimbing Lapangan (DPL)** | `dpl.stase@polkkestsm.ac.id` | `Pusbangdik2026!` | `STUDY_PROGRAM` (D3 RMIK) | Rina Marlina, M.Keb. |
| 11 | **Clinical Instructor (CI)** | `ci.rsud@partner.ac.id` | `Pusbangdik2026!` | `FIELD_PLACEMENT` (RSUD dr. Soekardjo) | Hendra, S.Kep., Ners |
| 12 | **Mahasiswa Aktif** | `mahasiswa.andin@polkkestsm.ac.id` | `Pusbangdik2026!` | `COURSE_OFFERING` (Kelas 1A) | Andin Rahmawati (P20637021001) |

---

## 5. Peta Rute Dashboard per Peran Pengguna

| Peran Pengguna | Rute Halaman Utama | Menu Prioritas UAT |
| :--- | :--- | :--- |
| **Super Admin / Admin** | `/dashboard` | Manajemen Institusi, Pengguna, Audit Trail |
| **Kaprodi** | `/dashboard/academic/curriculum` | RPS, Rencana Asesmen, Gradebook, Kriteria 3 Dossier |
| **Dosen Pengampu** | `/dashboard/assessment/delivery` | Bank Soal, Antrean Penilaian, Buku Nilai, Remedial |
| **Reviewer / Validator** | `/dashboard/assessment/instruments` | Verifikasi Bank Soal & Rubrik Asesmen |
| **GKM / Quality Officer** | `/dashboard/monev/checkpoints` | Monev Kepatuhan, Pelacak RTL PPEPP, Akreditasi |
| **Dosen PA** | `/dashboard/pa/advisees` | Early Warning Alert, Bimbingan PA, Profil OBE |
| **Clinical Instructor (CI)**| `/dashboard/ci/portal` | Verifikasi Logbook Harian, Penilaian Praktik RS |
| **Dosen Lapangan (DPL)** | `/dashboard/dpl/monitoring` | Monitoring Stase Wahana, Sidang Praktik, Sign-Off |
| **Mahasiswa** | `/dashboard/student/assessments` | Ujian CBT, Unggah Tugas, Logbook Praktik, Profil CPL |

---

## 6. Skenario UAT Utama 1: Campus Assessment (Skenario A)

**Alur Lengkap:**
$$\text{Fondasi} \to \text{RPS} \to \text{CPMK} \to \text{Rencana Asesmen 100\%} \to \text{Blueprint} \to \text{Bank Soal} \to \text{Terbit Ujian CBT} \to \text{Pengerjaan Mhs} \to \text{Penilaian} \to \text{Buku Nilai} \to \text{Monev} \to \text{RTL} \to \text{Kriteria 3}$$

| Langkah | Aktor | Tindakan UAT | Data Uji | Hasil yang Diharapkan | Status |
| :---: | :--- | :--- | :--- | :--- | :---: |
| **A.1** | Kaprodi | Periksa kurikulum, CPL, dan Mata Kuliah aktif. | Prodi D3 RMIK, MK: RMIK201 | CPL-1 s.d CPL-4 terpetakan ke CPMK MK RMIK201. | [ ] |
| **A.2** | Koordinator MK | Buat RPS 16 Sesi perkuliahan & ajukan persetujuan. | RPS Version 1.0 (16 Sesi) | Status beralih `DRAFT` $\to$ `REVIEW` $\to$ `APPROVED`. | [ ] |
| **A.3** | Dosen Pengampu | Susun Rencana Asesmen dan pastikan total bobot tepat 100.00%. | Tugas (20%), UTS (30%), UAS (50%) | Sistem menolak jika total $\ne 100\%$. Saat 100%, status menjadi sah. | [ ] |
| **A.4** | Reviewer | Validasi kisi-kisi (Blueprint) dan Bank Soal CBT. | Soal MCQ RMIK201-Q01 (Taksonomi C3) | Status soal divalidasi `APPROVED` untuk perakitan naskah. | [ ] |
| **A.5** | Dosen Pengampu | Terbitkan asesmen CBT dengan durasi 60 menit & acak soal. | Ujian Tengah Semester (UTS CBT) | Asesmen terbit dengan status `PUBLISHED` dan muncul di jadwal mhs. | [ ] |
| **A.6** | Mahasiswa | Masuk ke portal asesmen, kerjakan ujian CBT, uji autosave. | Akun `mahasiswa.andin@polkkestsm.ac.id` | Jawaban tersimpan otomatis per butir. Submit berhasil status `SUBMITTED`. | [ ] |
| **A.7** | Sistem | Penilaian otomatis butir soal MCQ. | Kunci jawaban A/B/C/D | Skor mentah dihitung deterministik tanpa intervensi manual. | [ ] |
| **A.8** | Dosen Pengampu | Buka Buku Nilai (*Gradebook*), periksa komponen skor terbobot. | Kelas 1A, MK RMIK201 | Nilai akhir terhitung otomatis berdasar skala huruf (misal: 83.50 $\to$ A-). | [ ] |
| **A.9** | GKM Officer | Jalankan audit Monev Checkpoint Tengah Semester (W7). | Checkpoint MID_SEMESTER | Sistem mengevaluasi kepatuhan riil basis data dan mencatat temuan. | [ ] |
| **A.10**| Dosen Pengampu | Rumuskan Rencana Tindak Lanjut (RTL) temuan monev. | Temuan: Mahasiswa At-Risk | RTL berstatus `IN_RTL` pada tahap Pengendalian PPEPP. | [ ] |
| **A.11**| GKM Officer | Verifikasi penutupan RTL dosen dan majukan ke Peningkatan. | Bukti penanganan remedial mhs | Temuan ditutup resmi (`VERIFIED_CLOSED`). | [ ] |
| **A.12**| Kaprodi / GKM | Buka Matriks Bukti Akreditasi & Kompilasi Dossier Kriteria 3. | Periode 2024/2025 Ganjil | Skor kepatuhan $\ge 85\%$ (MET Unggul), terbit hash SHA-256 pada bukti. | [ ] |

---

## 7. Skenario UAT Utama 2: Field Practice & Clinical Internship (Skenario B)

**Alur Lengkap:**
$$\text{Program Praktik} \to \text{Penempatan RS} \to \text{Penugasan Mhs} \to \text{Penugasan CI \& DPL} \to \text{Logbook Harian} \to \text{Observasi Kompetensi} \to \text{Asesmen Stase} \to \text{Sign-off} \to \text{Buku Nilai}$$

| Langkah | Aktor | Tindakan UAT | Data Uji | Hasil yang Diharapkan | Status |
| :---: | :--- | :--- | :--- | :--- | :---: |
| **B.1** | Kaprodi | Verifikasi Program Praktik Klinik terhubung MK & RPS. | PKL Rekam Medis I (RSUD dr. Soekardjo) | Program aktif, terhubung penawaran MK semester berjalan. | [ ] |
| **B.2** | Admin Akademik | Tempatkan mahasiswa ke unit kerja rumah sakit. | Unit Rekam Medis Rawat Inap & IGD | Mahasiswa terdaftar dalam kelompok penempatan stase aktif. | [ ] |
| **B.3** | Kaprodi | Pasangkan Clinical Instructor (CI) dan DPL pendamping. | CI: Hendra, Ners; DPL: Rina Marlina | Hak akses portal CI dan DPL terbuka sesuai batasan wahana. | [ ] |
| **B.4** | Mahasiswa | Isi logbook harian aktivitas praktik & lampirkan bukti foto. | Kompetensi: Kodifikasi Berkas RM Rawat Inap | Entri logbook tercatat status `PENDING_REVIEW` pada antrean CI. | [ ] |
| **B.5** | CI | Buka Portal CI via mobile/tablet, verifikasi dan tanda tangani logbook. | Akun `ci.rsud@partner.ac.id` | Logbook diverifikasi status `APPROVED` disertai catatan pembimbing. | [ ] |
| **B.6** | CI & DPL | Jalankan asesmen ujian keterampilan stase multi-penilai. | Bobot: CI (60%), DPL (40%) | Strict Completion Gate: Skor tidak dihitung sebelum kedua penilai submit. | [ ] |
| **B.7** | DPL | Submit nilai ujian stase kedua. | DPL menginput nilai (skor 90.0) | Bobot genap 100%, skor akhir stase terkalkulasi akurat ke Gradebook. | [ ] |
| **B.8** | DPL / Kaprodi | Lakukan pengesahan kelulusan stase (*Placement Sign-Off*). | Stase Rawat Inap RSUD | Mahasiswa dinyatakan lulus stase klinis (`PASSED`). | [ ] |
| **B.9** | Dosen PA | Tinjau profil capaian CPL & radar kompetensi klinik mhs. | Mahasiswa Andin Rahmawati | Capaian CPL praktik klinik teragregasi otomatis di dashboard PA. | [ ] |

---

## 8. Skenario UAT Pengujian Terfokus (*Focused Feature Tests*)

### F.1 Pengalihan Peran Multi-Konteks (*Context Switching*)
- **Aktor:** `dosen.pengampu@polkkestsm.ac.id` (Memiliki 3 peran: Dosen Pengampu, Koordinator MK, dan GKM).
- **Langkah:** Klik widget *Peran & Cakupan Aktif* di pojok kiri atas sidebar. Pilih *Koordinator MK*, lalu ganti ke *GKM Officer*.
- **Hasil yang Diharapkan:** Token JWT diperbarui seketika, menu navigasi sidebar menyesuaikan peran aktif, akses menu terisolasi sesuai RBAC.

### F.2 Penegakan Otorisasi Role + Scope
- **Langkah:** Login sebagai Mahasiswa, coba akses URL Kaprodi langsung: `/dashboard/monev/checkpoints` atau kirim request API tanpa izin.
- **Hasil yang Diharapkan:** Sistem menolak akses dengan kode error HTTP `403 Forbidden` dan mencatat upaya pelanggaran ke audit log.

### F.3 Strict Completion Gate (Multi-Assessor)
- **Langkah:** Buka penilaian stase klinik yang membutuhkan 2 penguji (CI 60% dan DPL 40%). Biarkan hanya CI yang menginput nilai.
- **Hasil yang Diharapkan:** Sistem menampilkan status *Pending DPL (Missing Weight: 40%)* dan menolak finalisasi nilai ke buku nilai. Nilai tidak dinormalisasi sepihak.

### F.4 Penguncian Buku Nilai (*Grade Locking*)
- **Langkah:** Kaprodi mengunci buku nilai kelas 1A di akhir semester.
- **Hasil yang Diharapkan:** Tombol edit nilai dinonaktifkan, status buku nilai menjadi `LOCKED`, seluruh kalkulasi nilai terkunci permanen.

### F.5 Pengajuan Koreksi Nilai (*Grade Change Request*)
- **Langkah:** Dosen Pengampu mengajukan koreksi nilai setelah buku nilai terkunci karena kekeliruan rekap tugas.
- **Hasil yang Diharapkan:** Terbit tiket permohonan koreksi nilai dengan justifikasi dan bukti. Nilai baru aktif hanya setelah disetujui resmi oleh Kaprodi.

### F.6 Penanganan Banding Nilai (*Appeal Workflow*)
- **Langkah:** Mahasiswa mengajukan sanggahan terhadap nilai ujian esai melalui portal asesmen.
- **Hasil yang Diharapkan:** Tiket banding masuk ke antrean dosen pengampu, dilakukan re-evaluasi objektif, dan keputusan banding terekam dalam riwayat audit.

### F.7 Program Remedial dengan Batas Nilai Maksimal (*MaxScore Cap 70.00*)
- **Langkah:** Dosen mendaftarkan mahasiswa at-risk (skor awal 45.0) ke sesi remedial. Dosen menginput nilai hasil ujian remedial sebesar 85.0.
- **Hasil yang Diharapkan:** Nilai asli (45.0) tetap tersimpan tanpa dihapus. Nilai yang disahkan ke buku nilai dibatasi maksimal 70.00 (*Capped Score* sesuai kebijakan institusi).

### F.8 Pengisian Logbook Melewati Batas Waktu (*Late Logbook*)
- **Langkah:** Mahasiswa mengisi logbook untuk tanggal 3 hari yang lalu (melebihi jendela default H+1).
- **Hasil yang Diharapkan:** Logbook tetap diterima sistem namun otomatis ditandai badge kuning `LATE` beserta catatan durasi keterlambatan.

### F.9 Pengacakan Butir Soal CBT & Pemulihan Autosave
- **Langkah:** Dua mahasiswa membuka ujian CBT yang sama secara bersamaan. Lakukan refresh peramban di tengah pengerjaan.
- **Hasil yang Diharapkan:** Urutan butir soal dan urutan opsi pilihan ganda teracak berbeda antar mahasiswa. Seluruh jawaban sebelum refresh tetap utuh tersimpan (*zero data loss*).

### F.10 Jejak Audit Forensik (*Audit Trail*)
- **Langkah:** Lakukan modifikasi rubrik atau pengesahan nilai, lalu buka tabel `audit_logs` atau portal audit.
- **Hasil yang Diharapkan:** Terekam log lengkap: stempel waktu UTC/WIB, ID aktor, nama aktor, aksi, tipe entitas, ID entitas, data lama (*old values*), data baru (*new values*), dan alasan perubahan.

### F.11 Penelusuran Bukti Akreditasi (*Evidence Traceability*)
- **Langkah:** Di menu *Matriks Bukti Kriteria 3*, klik tombol hash SHA-256 pada salah satu butir bukti (misal: `EVD-3.1-RPS-ALIGNMENT`).
- **Hasil yang Diharapkan:** Menampilkan modal detail berisi metrik operasional riil, daftar tabel sumber transaksi database, dan sidik jari forensik SHA-256.

### F.12 Antarmuka Responsif Mobile / Tablet untuk Clinical Instructor (CI)
- **Langkah:** Buka portal CI (`/dashboard/ci/portal`) menggunakan ponsel pintar atau mode responsif peramban (lebar layar $\le 420\text{ px}$).
- **Hasil yang Diharapkan:** Tata letak antarmuka menyesuaikan mulus, tombol verifikasi dan tanda tangan logbook mudah ditekan dengan satu tangan (*touch-friendly tap targets*).

---

## 9. Format Pelaporan Defek UAT (*Defect Report Format*)

Jika penguji menemukan ketidaksesuaian atau kendala selama pelaksanaan UAT, laporkan menggunakan format berikut:

```markdown
### [DEFECT-ID]: Judul Ringkas Kendala
- **Skenario Terkait**: [Scenario A / Scenario B / Focused Test F.x]
- **Tingkat Keparahan (Severity)**: [CRITICAL / HIGH / MEDIUM / LOW]
- **Peran Pengguna (Role)**: [misal: Dosen Pengampu / Mahasiswa / CI]
- **Rute URL**: [misal: /dashboard/grading/queue]
- **Langkah Reproduksi**:
  1. Login sebagai ...
  2. Klik menu ...
  3. Masukkan data ...
  4. Klik tombol ...
- **Hasil Aktual**: (Apa yang terjadi di layar/API)
- **Hasil yang Diharapkan**: (Apa yang seharusnya terjadi sesuai spesifikasi)
- **Tangkapan Layar / Payload Log**: (Sertakan screenshot atau pesan error console jika ada)
```

### Standar Tingkat Keparahan Defek:
1. **CRITICAL**: Sistem terhenti (*crash*), kebocoran data antar-tenant/prodi, nilai asli mahasiswa terhapus/tertimpa, atau kegagalan otorisasi fatal.
2. **HIGH**: Fitur utama alur asesmen terhambat dan tidak ada solusi alternatif (*workaround*), misal: tombol submit ujian tidak merespons.
3. **MEDIUM**: Fitur berfungsi namun tidak konsisten atau hasil kalkulasi desimal memerlukan penyesuaian tampilan, misal: pembulatan persen tampilan.
4. **LOW**: Masalah kosmetik, perbaikan redaksional label bahasa Indonesia, atau tata letak visual minor yang tidak mengganggu fungsi.

---

## 10. Pengesahan Kesiapan UAT

| Tanggal Persiapan | Status Lingkungan | Versi Aplikasi | Pengembang Penanggung Jawab |
| :---: | :---: | :---: | :---: |
| 19 September 2026 | **Siap UAT (Testing Ready)** | **v1.0-uat (Monorepo)** | Antigravity AI Engineering Team |

> **PERINGATAN MANAJEMEN:**  
> Lingkungan ini khusus dialokasikan untuk kegiatan User Acceptance Testing (UAT).  
> **Dilarang keras melakukan deployment ke server produksi** sebelum pengujian UAT disetujui dan ditandatangani secara tertulis oleh Product Owner dan Pimpinan Poltekkes Kemenkes Tasikmalaya.
