# 1. Architecture & Environment

## 1.1. Technical Stack
| Component | Specification | Notes |
| :--- | :--- | :--- |
| **Server Stack** | **FlyEnv** | Used as All-in-One Local Development Environment. |
| **PHP Version** | **8.x** | Modern syntax supported. |
| **Database** | MySQL / MariaDB | Standard WP Tables only. |
| **Frontend CSS** | **Hybrid (Tailwind + Bootstrap + Native)** | See Section 1.3 for conflict warnings. |
| **Frontend JS** | Vanilla JS + GSAP | Ad-hoc scripts per page. |

## 1.2. Development Environment (Setup)
Project ini dikembangkan menggunakan **FlyEnv**.
1.  **Requirement:** Install FlyEnv pada mesin lokal.
2.  **PHP Config:** Pastikan environment diset ke PHP 8.x.
3.  **Build Tools:** **None**. Project ini tidak menggunakan NPM/Webpack. Semua aset bersifat statis atau dipanggil via CDN.

## 1.3. Frontend Architecture (CRITICAL READ)
Pengembangan frontend menggunakan pendekatan **Hybrid-Mix**. Developer harus berhati-hati terhadap *specificity wars* (tabrakan style).

* **CSS Frameworks:**
    * **Tailwind CSS:** Loaded via CDN (Enqueued in `functions.php`).
    * **Bootstrap:** Local Files (Located in `/assets/css/`, Enqueued globally).
    * **Native CSS:** Custom style manual.
    * **⚠️ Warning:** Styling tidak konsisten. Beberapa elemen menggunakan *utility classes* (Tailwind), beberapa menggunakan komponen Bootstrap, dan lainnya CSS murni. Jika styling tidak berubah, cek inspect element untuk melihat *override*.

* **JavaScript Implementation:**
    * **Library:** GSAP (GreenSock) digunakan untuk animasi minor.
    * **Structure:** Tidak ada file bundle JS utama (`main.js`). Script logika seringkali bersifat spesifik per halaman (*per-page basis*).

## 1.4. Asset Loading Strategy
Pemuatan aset (CSS/JS/Fonts) dilakukan melalui dua pintu:
1.  **Global Enqueue (`functions.php`):** Untuk Tailwind (CDN), Bootstrap, FontAwesome, dan CSS utama.
2.  **Hardcoded Tags:** Cek file `header.php` atau template page spesifik. Beberapa script/style mungkin di-*inject* langsung menggunakan tag `<link>` atau `<script>` manual.

## 1.5. Backend Data Logic
* **Custom Post Types:** Didefinisikan manual di `functions.php`.
* **Custom Fields:** Menggunakan Native WordPress **Meta Box API** (`add_meta_box`). Data disimpan dalam tabel `wp_postmeta` standar.

# 2. Theme Structure & Frontend Hierarchy

## 2.1. Directory Map
Struktur tema menggunakan pendekatan *flat* untuk template utama, dengan pemisahan aset dan logika tambahan (`/inc`).



**Bab 2: Struktur Tema dan Alur Tampilan (Frontend)**

**2.1 Peta Direktori dan Organisasi File**
Dalam pengembangan tema ini, struktur direktori dirancang menggunakan pendekatan hirarki datar (*Flat Hierarchy*). Artinya, seluruh file template utama yang mengatur tampilan halaman diletakkan langsung di direktori akar (*root*) tema, bukan disembunyikan dalam sub-folder. Keputusan ini diambil untuk mempercepat akses developer terhadap file-file krusial seperti halaman depan, halaman artikel, maupun halaman profil. Pemisahan hanya dilakukan secara spesifik untuk aset statis di dalam folder aset, serta logika pendukung seperti pengaturan *enqueue* dan AJAX yang ditempatkan secara terpisah di folder "inc".

**2.2 Mekanisme Navigasi dan Routing**
Sistem navigasi atau *routing* pada tema ini menerapkan metode hibrida yang membagi cara kerja halaman menjadi dua kategori. Kategori pertama adalah halaman berbasis template eksplisit, yang mencakup file seperti halaman "About", "Topics", dan "Page Author". Halaman-halaman jenis ini tidak akan tampil secara otomatis di situs. Untuk mengaktifkannya, administrator diwajibkan membuat halaman baru di Dashboard WordPress dan secara manual memilih nama template yang sesuai pada menu atribut halaman. Tanpa konfigurasi manual ini, desain khusus yang telah dikodekan dalam file PHP tersebut tidak akan dimuat oleh sistem.

Kategori kedua adalah halaman yang mengikuti hirarki bawaan (*Native Hierarchy*) WordPress. Berbeda dengan kategori pertama, halaman jenis ini—seperti halaman detail artikel, arsip kategori topik, dan hasil pencarian—bekerja secara otomatis tanpa perlu konfigurasi halaman di dashboard. Sistem WordPress akan secara cerdas mengenali permintaan URL dari pengguna dan langsung memanggil file template yang relevan, seperti memanggil file "single.php" ketika sebuah artikel dibuka.

**2.3 Komponen Layout dan Modul Khusus**
Terdapat beberapa catatan penting mengenai komponen layout yang membedakan tema ini dari struktur standar. Meskipun bagian kepala (*Header*) dikelola secara terpusat melalui satu file global, bagian kaki (*Footer*) dikelola dengan cara yang berbeda. Tema ini tidak menggunakan file footer terpisah; sebaliknya, kode untuk bagian kaki situs ditulis langsung (*hardcoded*) di bagian bawah setiap file template secara individu. Selain itu, perlu diperhatikan bahwa modul profil "Tim BRIGHT" tidak memiliki halaman yang berdiri sendiri, melainkan merupakan bagian integral yang tertanam di dalam halaman "About".

---
# 3. Backend Logic & Data Structures

## 3.1. Core Philosophy: Zero-Dependency
Seluruh manajemen konten kustom dibangun menggunakan **Native WordPress API**.
* **Location:** `functions.php`
* **Dependencies:** None (No plugins like ACF/Pods/CPT UI).

## 3.2. Custom Post Types (CPT) Schema
Terdapat dua entitas konten utama yang didaftarkan secara manual via `register_post_type`.

### A. Entity: Writers
Digunakan untuk manajemen profil penulis artikel/kontributor.
* **Slug:** `writer` (verify in code).
* **Supports:** `title`, `editor` (Biografi), `thumbnail` (Foto Profil).
* **Visibility:** Public, Has Archive.
* **Template Logic:**
    * **Archive:** `archive-writer.php` (List of all writers).
    * **Single:** `template-writer.php` (Custom logic view).

### B. Entity: Tim BRIGHT
Digunakan sebagai *Data Store* untuk menampilkan tim pada halaman About.
* **Slug:** `tim_bright` (verify in code).
* **Supports:** `title` (Nama), `thumbnail` (Foto).
* **Visibility:** `publicly_queryable => false` (Tidak memiliki halaman detail mandiri).
* **Usage:** Data diambil via `WP_Query` dan di-loop di dalam file `about.php`.

## 3.3. Custom Fields (Native Meta Boxes)
Atribut data tambahan dikelola menggunakan API `add_meta_box` dan disimpan di tabel `wp_postmeta`.

**Implementation Pattern:**
1.  **Rendering:** Fungsi render HTML form (input text/URL) di admin.
2.  **Sanitization & Saving:** Hook `save_post` menangani validasi *nonce* dan update database.
3.  **Retrieval:** Menggunakan `get_post_meta($id, 'key', true)` di frontend.

**Key Mapping Reference:**
* **Writers Meta Keys:**
    * `_writer_job_title` (e.g., "Senior Editor")
    * `_writer_social_linkedin`
    * `_writer_social_instagram`
* **Tim BRIGHT Meta Keys:**
    * `_tim_role` (e.g., "Project Manager")
    * `_tim_division`

## 3.4. Query Strategy
Pola pengambilan data di frontend:

* **Writers Loop (`archive-writer.php`):**
  Menggunakan Default Loop WordPress (karena ini adalah file Archive resmi).

* **Tim BRIGHT Loop (`about.php`):**
  Menggunakan Custom `WP_Query`.
  ```php
  $args = array(
      'post_type' => 'tim_bright',
      'posts_per_page' => -1, // Menampilkan semua
      'orderby' => 'date',
      'order' => 'ASC'
  );
  // Loop execution...
