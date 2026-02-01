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

```text
/theme-root/
├── 404.php                 # Error handling page
├── about.php               # [Page Template] About Us + Section "Tim BRIGHT"
├── archive-writer.php      # [Page Template] Page Author (List of Writers)
├── category.php            # [Native] Topic Details (Archive Category)
├── front-page.php          # [Native/Template] Homepage
├── functions.php           # Core Logic (Monolithic Base)
├── header.php              # Global Header Section
├── index.html              # Fallback/Requirement File
├── search.php              # [Native] Search Result
├── single.php              # [Native] Default Article Content
├── style.css               # Global Styling
├── template-writer.php     # [Template] Single Writer Bio/Subpage
├── topics.php              # [Page Template] Base Topics Landing Page
├── assets/                 # Static Assets (img, css, js, content)
└── inc/                    # Includes
    ├── assets.php          # Enqueue Scripts & Styles
    └── (AJAX Handler)      # Asynchronous request logic
