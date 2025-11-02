### FHIR R4 Implementation for Obstetric Pathology

Dokumentasi ini merinci proses implementasi standar Fast Healthcare Interoperability Resources (FHIR) Release 4 untuk pencatatan kasus Kebidanan Patologi (Antenatal Care/ANC dengan komplikasi) pada sistem Rekam Medis Elektronik (RME) di Fasilitas Kesehatan.

---

### 1. Tujuan Proyek

Proyek ini bertujuan mendemonstrasikan _workflow_ FHIR yang **skalabel** dan **interoperabel** untuk kasus klinis spesifik, dengan fokus pada:

1.  **Cakupan Resource:** Menguasai _POST_ dan _GET_ enam Resource FHIR inti (**Patient**, **Encounter**, **Observation**, **Condition**, **Practitioner**, **Organization**).
2.  **Standardisasi Klinis:** Menerapkan sistem kode standar **SNOMED CT** dan **LOINC** untuk memastikan pemahaman data klinis yang universal.
3.  **Output Dokumen:** Membuat dan memvalidasi **Bundle Document** (Surat Rujukan Digital) yang sesuai standar FHIR untuk pertukaran data yang bersifat _immutable_.
4.  **Efisiensi:** Membangun arsitektur _template_ dan _ID mapping_ yang terstruktur untuk alur kerja yang cepat dan berulang.

---

### 2. Lingkungan Teknis

| Komponen              | Deskripsi                                                | Nilai/Endpoint                                       |
| :-------------------- | :------------------------------------------------------- | :--------------------------------------------------- |
| **FHIR Server**       | Digunakan sebagai lingkungan pengujian _non-production_. | `http://hapi.fhir.org/baseR4/`                       |
| **Metode Komunikasi** | Protokol standar untuk pertukaran data.                  | **HTTPS REST API** (`POST`, `GET`)                   |
| **Standard Coding**   | Standar yang digunakan untuk kodifikasi data klinis.     | **SNOMED CT** (Diagnosis), **LOINC** (Observasi/Lab) |

---

### 3. Struktur Direktori Proyek

Proyek diorganisasikan untuk memisahkan _input data_ dari _template_ generik dan _output dokumen_ final.

| Direktori/File              | Fungsi                                                                                                     |
| :-------------------------- | :--------------------------------------------------------------------------------------------------------- |
| `payloads/`                 | Berisi file JSON spesifik per pasien yang digunakan untuk _POST_ Resource individu.                        |
| `templates/`                | Berisi _template_ JSON generik yang digunakan sebagai _blueprint_ untuk efisiensi.                         |
| `templates/id_mapping.json` | File logistik kritis untuk menyimpan ID permanen FHIR yang digunakan sebagai _reference_.                  |
| `rujukan/`                  | Berisi _output_ final berupa **FHIR Bundle Document** (tipe `document`), siap untuk pertukaran data legal. |

---

### 4. Ringkasan Data Klinis dan Mapping

Data berikut merupakan hasil dari _request_ _POST_ ke FHIR Server dan berfungsi sebagai jembatan _mapping_ internal ke Resource FHIR.

| Resource           | Kasus 1: Siti (Preeklampsia) | Kasus 2: Susi (Anemia)               | Keterangan Klinis                          |
| :----------------- | :--------------------------- | :----------------------------------- | :----------------------------------------- |
| **Patient ID**     | `51664913`                   | `51665620`                           | ID Identitas Pasien                        |
| **Encounter ID**   | `51664974`                   | `51665621`                           | ID Kunjungan ANC                           |
| **Observation ID** | `51664990` (TTV)             | `51665622` (Hb: 9.5 g/dL)            | Data Kuantitatif Klinis                    |
| **Condition ID**   | `51665484` (Preeklampsia)    | `51665624` (Anemia during pregnancy) | Diagnosis                                  |
| **Nakes ID**       | `51664917`                   | `51664917`                           | ID Practitioner (Bidan Rina, S.ST)         |
| **Faskes ID**      | `51664973`                   | `51664973`                           | ID Organization (Klinik Pratama Sejahtera) |

---

### 5. Alur Kerja dan Validasi

#### 5.1. Alur Kerja Resource Individual (POST)

Setiap kasus dicatat melalui _request_ _POST_ berurutan, memastikan integritas referensial:

$$\text{Patient} \rightarrow \text{Encounter} \rightarrow \text{Observation} \rightarrow \text{Condition}$$

#### 5.2. Validasi Data (GET)

Integritas data divalidasi dengan _search parameter_ FHIR yang menghubungkan _resources_ ke pasien:

$$\text{GET /}[Resource]?\text{subject:Patient}=[ID\_PASIEN]$$

- **Validasi Sukses:** _GET_ terhadap `Observation` dan `Condition` berhasil mengembalikan _resources_ yang terhubung dengan `Patient` yang bersangkutan.

#### 5.3. Finalisasi Dokumen

Dokumen rujukan (Surat Rujukan Digital) dihasilkan sebagai **Bundle `document`** yang berpusat pada _Resource_ **Composition**. _Composition_ merangkum data klinis kunci (`Condition` dan `Observation`) dan menyertakan semua _Resource_ pendukung (`Patient`, `Encounter`, dll.) dalam _array_ `entry`.

---

### 6. Kesimpulan Implementasi

Proyek ini berhasil membuktikan kemampuan mengelola data pasien kebidanan patologi sesuai standar FHIR R4. Keberhasilan utama terletak pada **skalabilitas** dan **efisiensi _workflow_** melalui penggunaan _template_ yang terstruktur dan produksi dokumen legal (`Bundle document`) yang siap dipertukarkan.

---
