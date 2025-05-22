# Laravel CRUD Project

**Laravel** adalah framework PHP berbasis arsitektur **MVC (Model-View-Controller)** yang memudahkan pengembangan aplikasi web dengan cepat dan elegan.  
Project ini adalah contoh CRUD sederhana untuk entitas **Mahasiswa** dengan API dan tampilan web.

---

## 🚀 Fitur

- CRUD Mahasiswa (Create, Read, Update, Delete)
- API RESTful menggunakan `apiResource`
- Frontend sederhana menggunakan Blade dan Tailwind CSS
- Contoh route API dan Web
- Integrasi database MySQL

---
sebelum clone buat folder baru baik manual maupun bisa menggunakan perintah
```
mkdir coba_uas_pbf
```
kemudian
```
cd coba_uas_pbf
```

## 📥 1. Clone Backend Laravel

```bash
git clone https://github.com/username/nama-project.git
cd nama-project
```
##🛢️ 2. Import Database
Buka phpMyAdmin: http://localhost/phpmyadmin

Buat database baru: laravel_crud

Import file .sql dari folder database ke database tersebut.

## ⚙️ 3. Menjalankan dan Mengecek Backend dengan Postman
Menjalankan server CI
```
php spark serve
```

Menjalankan Server Laravel

```
php artisan serve
```

Server akan berjalan di:
http://127.0.0.1:8000

Cek API dengan Postman
Method: GET

URL: http://127.0.0.1:8000/api/mahasiswa

Klik Send → Pastikan response JSON muncul.

### Troubleshooting umum Postman & Laravel
| Masalah        | Penyebab                             | Solusi                                                                                            |
| -------------- | ------------------------------------ | ------------------------------------------------------------------------------------------------- |
| 404 Not Found  | Route belum terdaftar atau salah URL | Cek `routes/api.php`, pastikan ada `Route::apiResource('mahasiswa', MahasiswaController::class);` |
|                | Server Laravel belum dijalankan      | Jalankan dengan `php artisan serve`                                                               |
| Database Error | Konfigurasi `.env` salah             | Cek konfigurasi database di `.env`:                                                               |

DB_DATABASE=laravel_crud
DB_USERNAME=root
DB_PASSWORD=

### Jika migration belum dijalankan, jalankan:
```
php artisan migrate    
``` |
| **Vendor Not Found** | Paket Composer belum terinstall | Install package Composer dengan perintah:  
```bash
composer install
```


## ⚙️ 4. Membuat Project Laravel Baru via Laragon (Optional)
Lewat Laragon GUI
Klik kanan Laragon → Quick App → Laravel → beri nama project

```
composer create-project laravel/laravel laravel-crud-app
cd laravel-crud-app
cp .env.example .env
php artisan key:generate
```

### edit file .env sesuai database
```
DB_DATABASE=laravel_crud
DB_USERNAME=root
DB_PASSWORD=
```

##🔧 5. Contoh Kode Utama
### a. Routing API (routes/web.php)
```
<?php
use App\Http\Controllers\DataMahasiswaController;
use App\Http\Controllers\DataJadwalSidangController;
use App\Http\Controllers\DataRuanganController;
use Illuminate\Support\Facades\Route;

Route::get('/dashboard', function () {
    return view('dashboard');
});


Route::get('/', [DataMahasiswaController::class, 'index']); // Halaman utama
Route::get('/mahasiswa', [DataMahasiswaController::class, 'index']); // Ini penting!
Route::get('/mahasiswa/create', [DataMahasiswaController::class, 'create']);
Route::post('/mahasiswa', [DataMahasiswaController::class, 'store']);
Route::get('/mahasiswa/{id}/edit', [DataMahasiswaController::class, 'edit']);
Route::put('/mahasiswa/{id}', [DataMahasiswaController::class, 'update']);
Route::delete('/mahasiswa/{id}', [DataMahasiswaController::class, 'destroy']);

#ini ruangan
Route::get('/ruangan', [DataRuanganController::class, 'index']);           // Tampil data ruangan
Route::get('/ruangan/create', [DataRuanganController::class, 'create']);  // Form tambah ruangan
Route::post('/ruangan', [DataRuanganController::class, 'store']);         // Simpan ruangan baru
Route::get('/ruangan/{kode_ruangan}/edit', [DataRuanganController::class, 'edit']);    // Form edit ruangan
Route::put('/ruangan/{kode_ruangan}', [DataRuanganController::class, 'update']);        // Update ruangan
Route::delete('/ruangan/{kode_ruangan}', [DataRuanganController::class, 'destroy']);    // Hapus ruangan

?>
```
### b. Controller (app/Http/Controllers/MahasiswaController.php)
```
<?php

namespace App\Http\Controllers;

use Illuminate\Http\Request;
use Illuminate\Support\Facades\Http;

class DataMahasiswaController extends Controller
{
    private $apiBase = "http://localhost:8080/mahasiswa";

    public function index() {
        $response = Http::get($this->apiBase);
        $json = $response->json();
        $mahasiswa = $json['data'] ?? []; // Ambil hanya data array
        return view('DataMahasiswa', compact('mahasiswa'));
    }

    public function create() {
        return view('tambahMahasiswa');
    }

    public function store(Request $request) {
        Http::post($this->apiBase, $request->all());
        return redirect('/'); // Ganti jika index route-nya lain
    }

    public function edit($id) {
    $response = Http::get("{$this->apiBase}/{$id}");
    $json = $response->json();
    $data = $json['data'] ?? null;
    return view('editMahasiswa', compact('data'));
}


    public function update(Request $request, $id) {
        Http::put("{$this->apiBase}/{$id}", $request->all());
        return redirect('/');
    }

    public function destroy($id) {
        Http::delete("{$this->apiBase}/{$id}");
        return redirect('/');
    }
}
```
### mahasiswa.blade.php
```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Dashboard Mahasiswa</title>
    <script src="https://cdn.tailwindcss.com"></script> /// Menggunakan CDN Tailwind CSS untuk styling
</head>
<body class="bg-gray-100 font-sans leading-normal tracking-normal"> /// Styling dasar pada body

<div class="flex"> /// Flex container utama

    <!-- Sidebar -->
    <div class="w-64 bg-blue-800 min-h-screen text-white"> /// Sidebar kiri dengan lebar 64, tinggi penuh dan latar biru
        <div class="p-6 font-bold text-xl border-b border-blue-600">
            Si-JawaSi /// Judul aplikasi
        </div>
        <nav class="mt-4">
            <a href="/mahasiswa" class="block px-6 py-3 bg-blue-700">Daftar Mahasiswa</a> 
            /// Link menu aktif (hardcoded)
            
            <a href="/ruangan" class="block px-6 py-3 hover:bg-blue-700 {{ request()->is('ruangan*') ? 'bg-blue-700' : '' }}">
                Ruangan
            </a> 
            /// Link ke halaman ruangan dengan pengecekan aktif berdasarkan URL menggunakan Blade
        </nav>
    </div>

    <!-- Main content -->
    <div class="flex-1 p-8"> /// Konten utama halaman
        <div class="flex justify-between items-center mb-6"> /// Header halaman dengan judul dan tombol
            <h1 class="text-3xl font-semibold">Daftar Mahasiswa</h1> /// Judul utama
            <a href="/mahasiswa/create" class="bg-blue-600 hover:bg-blue-700 text-white px-4 py-2 rounded shadow">
                + Tambah Mahasiswa /// Tombol untuk menambah data mahasiswa
            </a>
        </div>

        <div class="overflow-x-auto bg-white rounded shadow"> /// Container tabel dengan scroll horizontal
            <table class="min-w-full table-auto text-left"> /// Tabel daftar mahasiswa
                <thead class="bg-gray-200 text-gray-600 uppercase text-sm leading-normal"> /// Header tabel
                    <tr>
                        <th class="py-3 px-6">NPM</th>
                        <th class="py-3 px-6">Nama</th>
                        <th class="py-3 px-6">Prodi</th>
                        <th class="py-3 px-6">Judul Skripsi</th>
                        <th class="py-3 px-6">Email</th>
                        <th class="py-3 px-6">Aksi</th> /// Kolom untuk tombol aksi
                    </tr>
                </thead>
                <tbody class="text-gray-700 text-sm">
                    @foreach($mahasiswa as $mhs) /// Looping data mahasiswa
                    <tr class="border-b hover:bg-gray-100"> /// Baris data dengan efek hover
                        <td class="py-3 px-6">{{ $mhs['npm'] }}</td> /// Menampilkan NPM
                        <td class="py-3 px-6">{{ $mhs['nama_mahasiswa'] }}</td> /// Menampilkan Nama
                        <td class="py-3 px-6">{{ $mhs['program_studi'] }}</td> /// Menampilkan Program Studi
                        <td class="py-3 px-6">{{ $mhs['judul_skripsi'] }}</td> /// Menampilkan Judul Skripsi
                        <td class="py-3 px-6">{{ $mhs['email'] }}</td> /// Menampilkan Email
                        <td class="py-3 px-6 flex space-x-2"> /// Kolom aksi Edit dan Hapus
                            <a href="/mahasiswa/{{ $mhs['npm'] }}/edit" class="bg-yellow-400 text-white px-3 py-1 rounded hover:bg-yellow-500">Edit</a>
                            /// Tombol edit data

                            <form action="/mahasiswa/{{ $mhs['npm'] }}" method="POST" onsubmit="return confirm('Hapus data ini?');">
                                @csrf /// Token CSRF Laravel
                                @method('DELETE') /// Menggunakan method spoofing untuk delete
                                <button type="submit" class="bg-red-500 text-white px-3 py-1 rounded hover:bg-red-600">Hapus</button>
                                /// Tombol hapus data
                            </form>
                        </td>
                    </tr>
                    @endforeach /// Akhir looping
                </tbody>
            </table>
        </div>
    </div>
</div>

</body>
</html>

```
### c. Model (app/Models/Mahasiswa.php)
```
<?php

namespace App\Models;

use Illuminate\Database\Eloquent\Model;

class DataMahasiswa extends Model
{
    //
}

```

### tambahMahasiswa.blade.php
```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Form Mahasiswa</title>
    <script src="https://cdn.tailwindcss.com"></script>
</head>
<body class="bg-gray-100 min-h-screen flex items-center justify-center">

    <div class="bg-white p-8 rounded-lg shadow-md w-full max-w-md">
        <h1 class="text-2xl font-bold mb-6 text-center text-gray-800">
            {{ isset($data) ? 'Edit Data Mahasiswa' : 'Tambah Data Mahasiswa' }}
        </h1>

        <form method="POST" action="{{ isset($data) ? url('/mahasiswa/' . $data['id']) : url('/mahasiswa') }}">
            @csrf
            @if(isset($data))
                @method('PUT')
            @endif

            <div class="mb-4">
                <label class="block text-gray-700 font-semibold mb-1">NIM</label>
                <input type="text" name="nim" value="{{ $data['nim'] ?? '' }}" required
                       class="w-full px-4 py-2 border rounded-lg focus:outline-none focus:ring-2 focus:ring-blue-500">
            </div>

            <div class="mb-4">
                <label class="block text-gray-700 font-semibold mb-1">Nama</label>
                <input type="text" name="nama" value="{{ $data['nama'] ?? '' }}" required
                       class="w-full px-4 py-2 border rounded-lg focus:outline-none focus:ring-2 focus:ring-blue-500">
            </div>

            <div class="mb-6">
                <label class="block text-gray-700 font-semibold mb-1">Prodi</label>
                <input type="text" name="prodi" value="{{ $data['prodi'] ?? '' }}" required
                       class="w-full px-4 py-2 border rounded-lg focus:outline-none focus:ring-2 focus:ring-blue-500">
            </div>

            <button type="submit"
                    class="w-full bg-blue-600 text-white font-semibold py-2 px-4 rounded-lg hover:bg-blue-700 transition duration-200">
                {{ isset($data) ? 'Update' : 'Simpan' }}
            </button>
        </form>
    </div>

</body>
</html>
```

### editMahasiswa.blade.php
```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Edit Mahasiswa</title>
    <script src="https://cdn.tailwindcss.com"></script>
</head>
<body class="bg-gray-100 min-h-screen flex items-center justify-center">

    <div class="bg-white p-8 rounded-lg shadow-md w-full max-w-lg">
        <h1 class="text-2xl font-bold mb-6 text-center text-gray-800">Edit Data Mahasiswa</h1>

        <form method="POST" action="{{ url('/mahasiswa/' . $data['npm']) }}">
            @csrf
            @method('PUT')

            <div class="mb-4">
                <label class="block text-gray-700 font-semibold mb-1">NPM</label>
                <input type="text" name="npm" value="{{ $data['npm'] }}" readonly
                       class="w-full px-4 py-2 border bg-gray-100 rounded-lg text-gray-600">
            </div>

            <div class="mb-4">
                <label class="block text-gray-700 font-semibold mb-1">Nama Mahasiswa</label>
                <input type="text" name="nama_mahasiswa" value="{{ $data['nama_mahasiswa'] }}"
                       class="w-full px-4 py-2 border rounded-lg focus:outline-none focus:ring-2 focus:ring-blue-500">
            </div>

            <div class="mb-4">
                <label class="block text-gray-700 font-semibold mb-1">Program Studi</label>
                <input type="text" name="program_studi" value="{{ $data['program_studi'] }}"
                       class="w-full px-4 py-2 border rounded-lg focus:outline-none focus:ring-2 focus:ring-blue-500">
            </div>

            <div class="mb-4">
                <label class="block text-gray-700 font-semibold mb-1">Judul Skripsi</label>
                <input type="text" name="judul_skripsi" value="{{ $data['judul_skripsi'] }}"
                       class="w-full px-4 py-2 border rounded-lg focus:outline-none focus:ring-2 focus:ring-blue-500">
            </div>

            <div class="mb-6">
                <label class="block text-gray-700 font-semibold mb-1">Email</label>
                <input type="email" name="email" value="{{ $data['email'] }}"
                       class="w-full px-4 py-2 border rounded-lg focus:outline-none focus:ring-2 focus:ring-blue-500">
            </div>

            <button type="submit"
                    class="w-full bg-green-600 text-white font-semibold py-2 px-4 rounded-lg hover:bg-green-700 transition duration-200">
                Simpan Perubahan
            </button>
        </form>
    </div>

</body>
</html>
```

### Jalankan Di server
```
php artisan serve
```

### upload ke GitHub
```
git init
git add .
git commit -m "Initial commit"
git remote add origin https://github.com/username/laravel-crud-app.git
git branch -M main
git push -u origin main
```





