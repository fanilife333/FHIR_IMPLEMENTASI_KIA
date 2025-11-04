# FHIR R4 Implementation: Comprehensive Clinical, Logistics, and Financial Workflow

Dokumen ini merinci implementasi end-to-end standar Fast Healthcare Interoperability Resources (FHIR) Release 4, berfokus pada transisi data dari kasus klinis kompleks (Kebidanan Patologi & Operasi Seksio Sesarea) hingga siklus _billing_ dan _adjudication_ finansial penuh.

---

### 1. Tujuan Proyek dan Pencapaian Implementasi

Proyek ini mendemonstrasikan kapabilitas _workflow_ FHIR yang _interoperable_ dan _auditable_, dengan metrik keberhasilan utama meliputi:

1.  **Cakupan Resource yang Ekstensif:** Menguasai _POST_ dan _Reference_ lebih dari 20 _Resource_ dari tiga domain utama: Klinis, Logistik/SDM, dan Finansial.
2.  **Integrasi Lintas Domain:** Menghubungkan data Prosedural (**Procedure**) dengan Logistik (**Location**, **PractitionerRole**) dan Finansial (**Claim**).
3.  **Siklus Finansial Penuh:** Melaksanakan dan mendokumentasikan proses _billing_ lengkap (**Claim** -> **ClaimResponse** -> **ExplanationOfBenefit**), mencakup peran Provider dan Payer.
4.  **Adopsi Standar Terminologi:** Konsisten menggunakan sistem kode standar **SNOMED CT**, **LOINC**, dan **UCUM** untuk menjamin interpretasi data yang universal dan akurat.
5.  **Arsitektur Dokumentasi Profesional:** Memelihara _ID Mapping_ dan _Terminology Registry_ terpusat sebagai sumber kebenaran tunggal (_Single Source of Truth_).

---

### 2. Lingkungan Teknis dan Standar

| Komponen              | Deskripsi                                                              | Nilai/Endpoint                               |
| :-------------------- | :--------------------------------------------------------------------- | :------------------------------------------- |
| **FHIR Server**       | Lingkungan pengujian _non-production_ untuk validasi Resource FHIR R4. | `http://hapi.fhir.org/baseR4/`               |
| **Metode Komunikasi** | Protokol standar untuk pertukaran data.                                | **HTTPS REST API** (`POST`, `GET`, `SEARCH`) |
| **Standar Klinis**    | Kodifikasi untuk diagnosis, prosedur, dan obat.                        | **SNOMED CT**                                |
| **Standar Observasi** | Kodifikasi untuk hasil laboratorium dan tanda vital.                   | **LOINC**, **UCUM**                          |
| **Tooling**           | Editor yang digunakan untuk manajemen _payload_ dan dokumentasi.       | **VS Code**                                  |

---

### 3. Struktur Direktori dan Manajemen Logistik

Struktur direktori dirancang untuk memisahkan data spesifik pasien dari _master data_ yang dapat digunakan kembali, memastikan pemeliharaan yang efisien.

| Direktori/File                            | Fungsi                                                                                                          | Status Implementasi |
| :---------------------------------------- | :-------------------------------------------------------------------------------------------------------------- | :------------------ |
| `payload_json/diana/`                     | Berisi semua _payload_ JSON spesifik untuk Kasus Diana (SC, Medikasi, Klaim).                                   | Selesai             |
| `payload_json/master_data/`               | Berisi _Resource_ dasar: **Organization**, **Practitioner**, **Medication**, **Location** yang bersifat statis. | Selesai             |
| `documentation/id_mapping.json`           | Log _ID mapping_ semua _Resource_ yang berhasil di-_POST_, digunakan sebagai _reference_ logistik.              | Final               |
| `documentation/terminology_registry.json` | _Registry_ terpusat yang mencakup semua kode SNOMED, LOINC, dan kode finansial yang digunakan.                  | Final               |
| `rujukan/`                                | Berisi _output_ **FHIR Bundle Document** (dicapai di fase awal).                                                | Dicapai             |

---

### 4. Ringkasan Resource yang Dikuasai (Kasus Seksio Sesarea - Diana)

Kasus ini menjadi bukti penguasaan _Resource_ dari domain Klinis, Logistik, hingga Finansial, dengan skenario _maternity_ yang kompleks.

#### 4.1. Resource Klinis dan Prosedural

| Resource Type                | Contoh Implementasi                                     | ID Kunci                  |
| :--------------------------- | :------------------------------------------------------ | :------------------------ |
| **Patient**                  | Ibu Diana & Bayi Diana.                                 | `51670077` / `51836983`   |
| **Encounter**                | Kunjungan Rawat Inap/Perioperatif.                      | `51670081`                |
| **Condition**                | Diagnosis: Plasenta Previa Totalis (Mendasari SC).      | `51786524`                |
| **Procedure**                | Seksio Sesarea & Prosedur Perawatan Bayi Baru Lahir.    | `51790406` / `51847868`   |
| **MedicationAdministration** | Pemberian Bupivacaine (Anastesi) dan Ketorolac (Nyeri). | `51906239` / `51849689`   |
| **Observation**              | TTV, Pemeriksaan Kebidanan, Apgar Score, BB/PB.         | `51774232` s/d `51847402` |

#### 4.2. Resource Logistik dan SDM

| Resource Type        | Contoh Implementasi                                                      | ID Kunci                  |
| :------------------- | :----------------------------------------------------------------------- | :------------------------ |
| **Organization**     | RSIA Bunda Utama (Provider) & Asuransi Sehat Sejahtera (Payer).          | `51840814` / `51907580`   |
| **PractitionerRole** | Mendefinisikan peran Dokter Bedah, Anastesi, Anak, dan Perawat.          | `51844904` s/d `51904339` |
| **Location**         | Pemetaan lokasi logistik: Kamar Operasi, Rawat Inap Ibu, Ruang Neonatus. | `51843117` s/d `51902971` |

#### 4.3. Resource Finansial (Siklus Klaim Penuh)

| Resource Type                  | Keterangan                                                                             | ID Kunci   |
| :----------------------------- | :------------------------------------------------------------------------------------- | :--------- |
| **Claim**                      | Pengajuan _Billing_ oleh Provider. Mencakup Item Prosedur, Nakes, Obat, dan Akomodasi. | `51907108` |
| **ClaimResponse**              | Balasan dan _Adjudication_ Resmi dari Payer.                                           | `51915560` |
| **ExplanationOfBenefit (EOB)** | Laporan Final kepada Pasien, merinci Benefit dan Tanggungan (Co-pay).                  | `51915979` |

---

### 5. Kesimpulan Implementasi

Proyek ini berhasil memvalidasi alur data yang **komprehensif** di FHIR R4, membuktikan kapabilitas untuk: (1) merekam data klinis _real-time_ dan _granular_; (2) menghubungkannya dengan _Resource_ logistik (SDM dan Fasilitas); dan (3) mentransformasikannya menjadi dokumen finansial yang **terintegrasi** dan **transparan** untuk pertukaran data antar sistem kesehatan.

---
