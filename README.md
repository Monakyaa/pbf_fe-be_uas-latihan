## KONFIGURASI DI DATABASE
import database
```
GitHub - mayangm09/DBE-SI-Penjadwalan-Skripsi
```
## KONFIGURASI DI BACKEND
### 1. Clone Repository BE
```
https://github.com/MuhammadAbiAM/BE-Jadwal-Skripsi.git
cd BE-Jadwal-Skripsi
```


### 2. Install composer CI
```
composer install
```
### 3. Copy File Environment
```
cp .env.example .env
```

### 4. Menjalankan CI
```
php spark serve
```

### 5. Cek EndPoint menggunakan Postman
```
Kelas :
User
GET → http://localhost:8080/user
GET → http://localhost:8080/user/{id}

Mahasiswa
GET → http://localhost:8080/mahasiswa
GET → http://localhost:8080/mahasiswa/{id}
POST → http://localhost:8080/mahasiswa
PUT → http://localhost:8080/mahasiswa/{id}
DELETE → http://localhost:8080/mahasiswa/{id}

Dosen
GET → http://localhost:8080/dosen
GET → http://localhost:8080/dosen/{id}
POST → http://localhost:8080/dosen
PUT → http://localhost:8080/dosen/{id}
DELETE → http://localhost:8080/dosen/{id}

Ruangan
GET → http://localhost:8080/ruangan
GET → http://localhost:8080/ruangan/{id}
POST → http://localhost:8080/ruangan
PUT → http://localhost:8080/ruangan/{id}
DELETE → http://localhost:8080/ruangan/{id}


Jadwal Sidang
GET → http://localhost:8080/jadwal
GET → http://localhost:8080/jadwal/{id}
POST → http://localhost:8080/jadwal
PUT → http://localhost:8080/jadwal/{id}
DELETE → http://localhost:8080/jadwal/{id}

Penguji Sidang
GET → http://localhost:8080/penguji
GET → http://localhost:8080/penguji/{id}
POST → http://localhost:8080/penguji
PUT → http://localhost:8080/penguji/{id}
DELETE → http://localhost:8080/penguji/{id}

Views
GET → http://localhost:8080/view_jadwal
GET → http://localhost:8080/view_jadwal/{id}
GET → http://localhost:8080/view_penguji
GET → http://localhost:8080/view_penguji/{id}
GET → http://localhost:8080/view_penjadwalan
GET → http://localhost:8080/view_penjadwalan/{id}
```




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


## MEMBUAT PROJECT BARU LARAVEL DENGAN LARAGON

Klik kanan Laragon → Quick App → Laravel → beri nama project

```
composer create-project laravel/laravel laravel-crud-app
cd laravel-crud-app
cp .env.example .env
php artisan key:generate
```

### EDIT .env
```
DB_DATABASE=laravel_crud
DB_USERNAME=root
DB_PASSWORD=
```

## PENGUBAHAN PADA LARAVEL
### a. ROUTES (routes/web.php)
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
### b. CONTROLLER (app/Http/Controllers/MahasiswaController.php)
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
### c. MODEL (app/Models/Mahasiswa.php)
```
<?php

namespace App\Models;

use Illuminate\Database\Eloquent\Model;

class DataMahasiswa extends Model
{
    //
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





