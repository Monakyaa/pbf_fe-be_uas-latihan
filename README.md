# 🔥 Laravel CRUD Project

**Laravel** adalah framework PHP berbasis arsitektur **MVC (Model-View-Controller)** yang dirancang untuk membantu developer membuat aplikasi web secara cepat, efisien, dan elegan. Dengan dukungan Blade template, Eloquent ORM, sistem routing, dan keamanan bawaan, Laravel menjadi pilihan populer untuk pengembangan web modern.

---

## 📥 1. Clone Backend Laravel

Clone project dari GitHub ke local menggunakan perintah:

```bash
git clone https://github.com/username/nama-project.git
cd nama-project
```

---

## 🛢️ 2. Import Database

1. Buka phpMyAdmin via Laragon: [http://localhost/phpmyadmin](http://localhost/phpmyadmin)  
2. Buat database baru, misalnya: `laravel_crud`
3. Import file `.sql` yang tersedia dari folder `database` atau hasil export yang sudah dikirim.

---

## ⚙️ 3. Membuat Project Laravel via Laragon

### Opsi 1: Lewat GUI Laragon

- Buka Laragon
- Klik kanan → **Quick App** → **Laravel**
- Beri nama project misalnya `laravel-crud-app`

### Opsi 2: Lewat Terminal

```bash
composer create-project laravel/laravel laravel-crud-app
```

Masuk ke folder project:

```bash
cd laravel-crud-app
```

Salin dan konfigurasi file `.env`:

```bash
cp .env.example .env
php artisan key:generate
```

Edit file `.env`:

```env
DB_DATABASE=laravel_crud
DB_USERNAME=root
DB_PASSWORD=
```

---

## 🔧 4. Setup API & Tes di Postman

### a. Buat Model + Migration + Controller

```bash
php artisan make:model Mahasiswa -mcr
```

Struktur yang dihasilkan:

- `app/Models/Mahasiswa.php`
- `database/migrations/...create_mahasiswas_table.php`
- `app/Http/Controllers/MahasiswaController.php`

### b. Tambahkan Routing di `routes/api.php`

```php
Route::apiResource('mahasiswa', MahasiswaController::class);
```

### c. Tes API Menggunakan Postman

Jalankan server:

```bash
php artisan serve
```

Uji endpoint di Postman:

- Method: `GET`
- URL: `http://127.0.0.1:8000/api/mahasiswa`

Klik **Send**, dan pastikan response JSON muncul sesuai harapan.

---

## 🖥️ 5. Membuat Tampilan Web (Frontend)

### a. Edit Route di `routes/web.php`

```php
Route::get('/', function () {
    return view('mahasiswa.index');
});
```

### b. Buat File Blade View

Lokasi: `resources/views/mahasiswa/index.blade.php`

Contoh isi:

```blade
@extends('layouts.app')

@section('content')
<div class="p-6">
    <h2 class="text-xl font-bold mb-4">Data Mahasiswa</h2>
    <table class="min-w-full border border-gray-300">
        <thead class="bg-gray-100">
            <tr>
                <th class="p-2 border">ID</th>
                <th class="p-2 border">Nama</th>
                <th class="p-2 border">Aksi</th>
            </tr>
        </thead>
        <tbody>
            {{-- Data loop di sini --}}
        </tbody>
    </table>
</div>
@endsection
```

---

## 🧭 6. Tambahkan Template Navbar & Sidebar

Cari template Tailwind gratis dari:

- [https://tailwindcomponents.com](https://tailwindcomponents.com)
- [https://flowbite.com](https://flowbite.com)
- [https://daisyui.com](https://daisyui.com)

Salin HTML **navbar** dan **sidebar**, lalu taruh di `resources/views/layouts/app.blade.php`.

Contoh struktur layout:

```blade
<body class="flex">
    @include('layouts.sidebar')

    <div class="flex-1">
        @include('layouts.navbar')
        
        <main class="p-4">
            @yield('content')
        </main>
    </div>
</body>
```

---

## 💾 Menjalankan Server

```bash
php artisan serve
```

Akses di browser:

```
http://127.0.0.1:8000
```

---

## ☁️ Upload ke GitHub via Terminal (VS Code)

Inisialisasi Git:

```bash
git init
git add .
git commit -m "Initial commit"
```

Tambahkan remote GitHub:

```bash
git remote add origin https://github.com/username/laravel-crud-app.git
```

Push ke branch utama:

```bash
git branch -M main
git push -u origin main
```

---

## ✅ Selesai!

Project CRUD Laravel berhasil dibuat lengkap dengan frontend, backend API, layout responsif menggunakan Tailwind, serta integrasi ke GitHub.
