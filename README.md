# 1. Architecture & Environment

## 1.1. Project Overview
Project ini adalah **Custom WordPress Theme** yang dibangun dari awal (*from scratch*). Tema ini dirancang dengan pendekatan *pure code* untuk memaksimalkan performa dan meminimalisir ketergantungan (*dependencies*).

**Karakteristik Utama:**
* **No Child Theme:** Tema berdiri sendiri sebagai *parent theme*.
* **No Page Builders:** Layout dibangun menggunakan HTML/CSS/PHP manual, bukan Elementor/Divi.
* **No Generator Plugins:** Custom Post Types (CPT) dan metabox dibuat manual tanpa plugin seperti CPT UI atau ACF.

## 1.2. Tech Stack
* **Core:** WordPress (Self-Hosted).
* **Languages:** PHP Native, HTML5, CSS3, JavaScript (Vanilla/jQuery bawaan WP).
* **Database:** MySQL/MariaDB.
* **Development Flow:** Localhost -> Production.

## 1.3. Code Organization (Important!)
Berbeda dengan struktur tema modular pada umumnya, tema ini menggunakan pendekatan **Centralized Logic**.

* **`functions.php`:** File ini bertindak sebagai pusat kontrol tunggal.
    * Semua kode backend, registrasi CPT ("Writers", "Tim BRIGHT"), *enqueue scripts*, dan *custom hooks* berada di file ini.
    * **Note:** Kode tidak dipecah ke dalam folder `/inc` atau file terpisah. Gunakan fitur *search* (Ctrl+F) untuk menavigasi fungsi tertentu.

## 1.4. Installation & Setup
1.  **Prerequisites:** Local server (LocalWP/XAMPP) dengan PHP 7.4+.
2.  **Installation:**
    * Salin folder tema ke direktori `/wp-content/themes/`.
    * Aktifkan melalui Dashboard WP > Appearance > Themes.
