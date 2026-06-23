# Praktikum1-14_WEB2




# Laporan Praktikum 1: PHP Framework (CodeIgniter 4)

Pengaturan direktori yang sistematis dan pemahaman arsitektur yang mendalam merupakan fondasi utama bagi setiap pengembang dalam membangun aplikasi berbasis *framework* CodeIgniter 4. Dengan memahami bagaimana setiap komponen saling berinteraksi, pengembang dapat memastikan bahwa kode yang dihasilkan tidak hanya sekadar berfungsi, tetapi juga memiliki tingkat keamanan yang tinggi serta performa yang optimal dalam menangani permintaan pengguna.

Selain itu, dokumentasi yang terstruktur dan konfigurasi lingkungan pengembangan (*development environment*) yang tepat akan sangat membantu dalam proses pemeliharaan jangka panjang. Melalui penerapan arsitektur *Model-View-Controller* (MVC) yang konsisten, setiap bagian dari aplikasi memiliki tanggung jawab yang terisolasi, sehingga meminimalisir potensi kesalahan kodingan sekaligus mempermudah proses pengembangan fitur-fitur baru di masa yang akan datang.

#### 1. Struktur Folder dan Pengorganisasian Kode

Pengaturan direktori yang sistematis adalah standar utama dalam CI4. Struktur ini membantu pengembang memisahkan *logic* dari *presentation*.

* **Folder `app/**`: Pusat logika aplikasi (*Controller, Model, View*).
* **Folder `public/**`: Wadah aset statis seperti CSS dan gambar.
* **Konfigurasi Awal**: Anda harus memastikan *Document Root* pada server web (Apache) diarahkan ke folder `public/` agar file sistem di luar folder tersebut aman dari akses langsung pengguna. Struktur ini memaksa seluruh *request* masuk melalui `index.php` yang berada di `public/`, menjaga keamanan aplikasi dari *direct access*.

#### 2. Implementasi Routing sebagai Peta Navigasi

*Routing* adalah "polisi lalu lintas" yang mengarahkan setiap *request* URL ke *method* yang tepat.

* **Lokasi**: `app/Config/Routes.php`.
* **Detail Kodingan**:
```php
$routes->get('/about', 'Page::about');

```


* **Penjelasan**: `$routes->get` menetapkan bahwa URL hanya merespons metode HTTP GET. `'Page::about'` adalah instruksi untuk mencari `Class Page` (Controller) dan menjalankan `Method about` di dalamnya. Ini adalah metode paling efisien untuk membangun URL yang bersih (*clean URL*). Tanpa *routing*, aplikasi akan bergantung pada struktur folder fisik, yang berisiko terhadap keamanan dan sulit dikelola.

#### 3. Sinergi Controller dan View

*Controller* adalah otak aplikasi yang menerima input, memprosesnya, dan mengirimkan data ke *View*.

* **Penjelasan Kodingan**:
```php
namespace App\Controllers;
class Page extends BaseController {
    public function about() {
        $data = ['title' => 'Halaman About', 'content' => 'Detail konten project.'];
        return view('about', $data);
    }
}

```


* **Namespace**: `namespace App\Controllers` wajib ada agar *autoloader* CI4 dapat menemukan *class* Anda.
* **Extends BaseController**: Dengan melakukan `extends`, *controller* Anda mendapatkan akses ke fitur *request*, *response*, dan *session* bawaan CI4.
* **View Data**: `view('about', $data)` melakukan ekstraksi array `$data`, sehingga di dalam file `about.php`, Anda cukup memanggil `<?= $title; ?>`. CI4 secara otomatis melakukan *escaping* pada data yang dikirim, yang berarti aplikasi Anda terlindungi dari serangan XSS secara *default*.

#### 4. Efisiensi Layout melalui Templating

Penggunaan `$this->include` adalah kunci penerapan prinsip **DRY (Don't Repeat Yourself)**.

* **Penjelasan Kodingan**:
```php
<?= $this->include('template/header'); ?>
<h1><?= $title; ?></h1>
<?= $this->include('template/footer'); ?>

```



* **Penjelasan**: Saat CI4 memproses file ini, ia akan mengambil file `header.php`, menyambungkannya dengan konten utama, lalu menutupnya dengan `footer.php`. Hal ini memastikan bahwa jika Anda ingin mengubah menu navigasi, Anda hanya perlu mengedit satu file (`header.php`) dan perubahan akan otomatis merata ke seluruh halaman.

#### 5. Dokumentasi dan Bukti Praktikum

Dokumentasi ini membuktikan bahwa konfigurasi *environment* telah berhasil dilakukan.

* **Pengaturan PHP (`php.ini`)**: Anda wajib mengaktifkan ekstensi di XAMPP agar CI4 dapat berjalan:
* `extension=intl`: Digunakan untuk dukungan multibahasa dan format tanggal.
* `extension=mysqli`: Wajib untuk koneksi database MySQL.
* `extension=curl`: Diperlukan untuk komunikasi HTTP antar server.


* **Screenshot**: Anda perlu melampirkan bukti XAMPP (Apache/MySQL *Running*), Struktur folder VS Code, serta hasil *render* halaman di *browser* sebagai bukti fungsionalitas.

#### 6. Analisis Konsep MVC sebagai Fondasi Pengembangan

MVC memisahkan tanggung jawab agar aplikasi mudah dikembangkan (*scalable*).

* **Model**: Menangani *query* database dan validasi data. Menggunakan Model berarti Anda memisahkan bahasa SQL dari logika tampilan.
* **View**: Berisi kode HTML dan PHP minimalis untuk tampilan. Ia tidak boleh melakukan proses *query* database secara langsung.
* **Controller**: Bertindak sebagai *bridge*. Ia mengambil data dari Model dan mengirimkannya ke View. Inilah yang menjaga kode Anda tetap rapi.

**Tips Pengaturan Tambahan**:
Untuk kenyamanan pengembangan, pastikan file `.env` di direktori utama Anda dikonfigurasi dengan benar:

* Ubah `CI_ENVIRONMENT = development` saat Anda sedang dalam tahap *coding* agar pesan *error* muncul secara lengkap saat terjadi kesalahan kodingan. Jika sudah siap untuk di-upload ke *hosting* asli, ubah menjadi `production` demi keamanan.

# Laporan Praktikum 2: Framework Lanjutan (CRUD) - CodeIgniter 4

Mohon maaf atas ketidaktelitian saya sebelumnya. Anda benar, aspek **Database** adalah inti dari **Praktikum 2** yang membedakannya dengan praktikum sebelumnya.

Berikut adalah laporan yang telah saya perbarui dengan memasukkan penjelasan teknis mengenai pembuatan database, tabel, dan konfigurasi koneksi secara mendalam ke dalam struktur laporan Anda.

---

### Laporan Praktikum 2: Framework Lanjutan (CRUD) - CodeIgniter 4

#### 1. Persiapan Database dan Konfigurasi Koneksi

Sebelum membangun aplikasi, langkah pertama yang krusial adalah menyiapkan fondasi data. Dalam praktikum ini, kita menggunakan MySQL sebagai *database server*. Database ini berfungsi sebagai penyimpanan utama untuk data artikel yang akan dikelola. Setelah database dibuat, kita perlu menghubungkannya dengan CodeIgniter 4 melalui file konfigurasi `.env` agar sistem dapat berkomunikasi dengan tabel database.

Pengaturan koneksi ini sangat penting untuk menjembatani aplikasi dengan penyimpanan data yang bersifat persisten. Tanpa konfigurasi yang tepat, *framework* tidak akan mampu melakukan operasi *Query* seperti *insert*, *update*, maupun *delete* pada tabel yang telah disediakan. Oleh karena itu, memastikan parameter `hostname`, `database`, `username`, dan `password` sudah sesuai dengan kondisi lokal di XAMPP adalah langkah mutlak sebelum menjalankan logika pemrograman CRUD.

* **Pembuatan Database**: `CREATE DATABASE lab_ci4;`
* **Pembuatan Tabel**: Dibuat tabel `artikel` dengan kolom `id`, `judul`, `isi`, `gambar`, `status`, dan `slug`.
* **Konfigurasi Koneksi (`.env`)**:
```env
database.default.hostname = localhost
database.default.database = lab_ci4
database.default.username = root
database.default.password = 
database.default.DBDriver = MySQLI

```


* **Penjelasan**: Pengaturan ini krusial agar *framework* CI4 mengenali lokasi database, *username*, dan *driver* yang digunakan (MySQLi) sehingga aplikasi dapat melakukan sinkronisasi data dengan server MySQL secara akurat.

#### 2. Struktur Folder dan Pengorganisasian Kode

Pengaturan direktori yang sistematis dalam CodeIgniter 4 (CI4) adalah standar utama untuk menjaga kode tetap bersih (*clean code*) dan mudah dikelola. Pemisahan yang jelas antara logika bisnis dan tampilan sangat krusial agar setiap elemen kode tidak saling tumpang tindih.

* **Folder `app/Models/**`: Tempat menyimpan `ArtikelModel.php` untuk memproses interaksi data ke database.
* **Folder `app/Controllers/**`: Tempat menyimpan `Artikel.php` untuk logika alur CRUD.
* **Folder `public/**`: Wadah aset statis seperti gambar artikel.

#### 3. Implementasi Routing sebagai Peta Navigasi

*Routing* menghubungkan URL yang diakses pengguna dengan *method* spesifik di *controller*. Dalam CRUD, *routing* menjadi sangat penting karena setiap aksi (tambah, edit, hapus) memiliki URL unik.

* **Contoh Kodingan (Routing CRUD)**:
```php
$routes->group('admin', function($routes) {
    $routes->get('artikel', 'Artikel::admin_index');
    $routes->add('artikel/add', 'Artikel::add');
    $routes->add('artikel/edit/(:any)', 'Artikel::edit/$1');
    $routes->get('artikel/delete/(:any)', 'Artikel::delete/$1');
});

```


* **Penjelasan**: Fungsi `$routes->group` digunakan untuk mengelompokkan akses admin agar lebih terorganisir dan memudahkan pengelolaan keamanan rute secara terpusat.

#### 4. Sinergi Controller, Model, dan View (CRUD)

*Controller* berperan sebagai "otak" aplikasi yang menerima input, memprosesnya melalui *Model*, dan menampilkan hasilnya ke *View*.

* **Penjelasan Kodingan (Operasi Delete)**:
```php
public function delete($id) {
    $artikel = new ArtikelModel();
    $artikel->delete($id); // Menghapus data dari tabel berdasarkan ID
    return redirect('admin/artikel'); // Kembali ke daftar admin setelah dihapus
}

```


* **Return view**: Fungsi ini menyajikan antarmuka dengan menyuntikkan *array* data ke dalam *view* sehingga data artikel ditampilkan secara dinamis setelah proses pemrosesan data oleh *Model* selesai.

#### 5. Efisiensi Layout melalui Templating

Untuk menjaga konsistensi visual di seluruh halaman, CI4 mendukung fitur *templating* melalui `include`. Alih-alih menulis ulang kode HTML, kita cukup memanggil bagian *header* dan *footer*.

* **Kodingan View (`admin_index.php`)**:
```php
<?= $this->include('template/admin_header'); ?>
<?= $this->include('template/admin_footer'); ?>

```


* **Penjelasan**: Dengan cara ini, perubahan pada navigasi admin cukup dilakukan di satu file saja, yang secara otomatis akan merefleksikan perubahan tersebut di seluruh halaman yang menggunakan *template* tersebut.

#### 6. Dokumentasi dan Analisis Konsep MVC

Dokumentasi ini membuktikan bahwa setiap langkah, mulai dari pembuatan database hingga fungsi CRUD, telah berhasil diimplementasikan.

* **Model**: Menangani interaksi data di `ArtikelModel.php` (penggunaan `$allowedFields` sangat penting untuk keamanan *insert* data).
* **View**: Menampilkan antarmuka CRUD (tabel data, form tambah, form edit).
* **Controller**: Menjadi jembatan (*bridge*) yang memproses logika data melalui *Model* dan menampilkannya di *View*.
* **Bukti Praktikum**:
* **Screenshot 1**: XAMPP Control Panel dengan Apache & MySQL dalam kondisi *running*.
* **Screenshot 2**: Struktur folder proyek yang rapi sesuai standar CI4 di VS Code.
* **Screenshot 3 & 4**: Tampilan halaman Daftar Artikel dan Detail Artikel yang sudah sinkron dengan *template*.



Demikian penjelasan laporan praktikum yang telah mencakup seluruh tahapan, termasuk konfigurasi database dan integrasi logika CRUD secara menyeluruh.

# Laporan Praktikum 3: View Layout dan View Cell (CodeIgniter 4)

## 1. Deskripsi Praktikum

Praktikum ini mempelajari teknik efisiensi tampilan dalam **CodeIgniter 4** menggunakan dua konsep utama: **View Layout** (untuk standarisasi struktur halaman) dan **View Cell** (untuk komponen UI modular yang dapat digunakan kembali).

## 2. Tujuan Praktikum

* Mengimplementasikan **View Layout** agar struktur halaman (seperti header, footer, dan sidebar) tidak perlu ditulis berulang kali.


* Memahami dan menggunakan **View Cell** untuk memanggil komponen UI secara modular, seperti menampilkan widget artikel terkini.



## 3. Langkah-langkah Praktikum

1. **Membuat Layout Utama**: Membuat file `main.php` di dalam `app/Views/layout/` sebagai kerangka dasar seluruh halaman web.


2. **Modifikasi File View**: Menggunakan perintah `<?= $this->extend('layout/main') ?>` dan `<?= $this->section('content') ?>` pada file view seperti `home.php` untuk menghubungkan konten ke layout utama.


3. **Membuat Class View Cell**: Membuat file `ArtikelTerkini.php` di dalam folder `app/Cells/` untuk memproses logika data artikel yang akan ditampilkan pada widget.


4. **Membuat View Komponen**: Membuat file `artikel_terkini.php` di dalam `app/Views/components/` untuk menentukan bagaimana daftar artikel ditampilkan di dalam sidebar.



## 4. Konsep Utama

* **View Layout**: Memungkinkan pengembang untuk membuat satu template dasar dan menggunakan fitur `renderSection` untuk menyisipkan konten dinamis dari tiap halaman.


* **View Cell**: Berfungsi untuk memanggil tampilan dalam bentuk komponen yang dapat digunakan ulang, sangat efektif untuk elemen yang sering muncul di banyak halaman seperti sidebar atau widget.



## 5. Dokumentasi Screenshot

*(Harap lampirkan screenshot berikut untuk melengkapi laporan Anda):*

* **Screenshot 1**: Tampilan halaman web setelah menerapkan `layout/main.php`.
* **Screenshot 2**: Tampilan widget "Artikel Terkini" yang dihasilkan melalui `view_cell`.


* **Screenshot 3**: Struktur folder `app/Cells` dan `app/Views/components`.

## 6. Jawaban Pertanyaan Tugas

* **Manfaat View Layout**: Mempermudah pemeliharaan kode (maintainability) karena perubahan pada struktur (header/footer) hanya perlu dilakukan di satu file layout utama.


* **Perbedaan View Cell & View Biasa**: View biasa digunakan untuk satu halaman penuh, sedangkan View Cell digunakan untuk komponen kecil/modular yang memiliki logika data sendiri dan dapat dipanggil berulang kali di berbagai tempat.



---

Berikut adalah draf `README.md` yang lengkap untuk **Praktikum 4: Modul Login (Authentication)** berdasarkan dokumen **"Web 2 - Modul Praktikum 4.pdf"**.

---

# Laporan Praktikum 4: Modul Login (Authentication & Filter)

## 1. Deskripsi Praktikum

Praktikum ini membahas implementasi sistem autentikasi pengguna (*Login System*) pada framework CodeIgniter 4. Fokus utamanya adalah mengamankan akses ke halaman tertentu (seperti halaman admin) menggunakan sistem **Filter** dan **Session**.

## 2. Tujuan Praktikum

* Memahami konsep dasar *Authentication* (Auth) dalam aplikasi web.
* Mampu menerapkan **Filter** di CodeIgniter 4 untuk membatasi akses halaman.
* Mampu mengelola *Session* pengguna untuk menjaga status login.

## 3. Persiapan Database

Data pengguna disimpan dalam tabel `user` dengan struktur berikut:

* **id**: INT, Primary Key, Auto Increment
* **username**: VARCHAR(200)
* **useremail**: VARCHAR(200)
* **userpassword**: VARCHAR(200)

## 4. Langkah-langkah Praktikum

1. **Membuat Tabel User**: Menyiapkan tabel `user` di database MySQL.
2. **Membuat UserModel**: Membuat `UserModel.php` di `app/Models/` untuk menangani proses *query* data pengguna.
3. **Konfigurasi Route**: Mengatur `Routes.php` untuk membatasi akses grup `admin` dengan menerapkan `filter => 'auth'`.
4. **Implementasi Login**: Membuat controller untuk memproses verifikasi *username* dan *password*, serta menyimpan *session* jika login berhasil.
5. **Implementasi Logout**: Membuat method `logout()` yang menghancurkan *session* pengguna (`session()->destroy()`) dan mengarahkan kembali ke halaman login.

## 5. Konsep Utama

* **Auth (Authentication)**: Proses verifikasi identitas pengguna untuk memastikan hanya pengguna yang sah yang bisa masuk.
* **Filter**: Fitur di CodeIgniter 4 yang bertindak sebagai "penjaga pintu" (middleware). Filter akan memeriksa apakah pengguna sudah login sebelum mengizinkan mereka mengakses URL tertentu (seperti `admin/artikel`).
* **Session**: Objek yang digunakan untuk menyimpan informasi pengguna selama mereka berinteraksi dengan situs, sehingga pengguna tidak perlu login berulang kali di setiap halaman.

## 6. Dokumentasi Screenshot

*(Harap lampirkan screenshot berikut untuk laporan Anda):*

* **Screenshot 1**: Struktur tabel `user` di phpMyAdmin.
* **Screenshot 2**: Tampilan halaman `Login` saat pertama kali diakses.
* **Screenshot 3**: Tampilan pesan error atau pengalihan halaman saat mencoba mengakses `admin/artikel` tanpa login.
* **Screenshot 4**: Kode pada `Config/Routes.php` yang menunjukkan penggunaan filter.

## 7. Pertanyaan dan Tugas

* **Mengapa Filter penting?**: Filter sangat penting untuk keamanan aplikasi karena mencegah pengguna yang tidak terdaftar (atau belum login) mengakses halaman administratif yang sensitif.
* **Fungsi `session()->destroy()**`: Fungsi ini menghapus semua data *session* yang tersimpan di server, sehingga sistem akan menganggap pengguna sudah keluar (logout) dan memaksa mereka login kembali jika ingin mengakses halaman yang terlindungi.

---

Berikut adalah draf `README.md` yang disusun khusus untuk **Praktikum 5: Pagination dan Pencarian** berdasarkan dokumen yang Anda unggah. Anda bisa menambahkannya ke dalam file `README.md` di repository `Lab7Web` Anda.

---

# Laporan Praktikum 5: Pagination dan Pencarian (CodeIgniter 4)

## 1. Deskripsi Praktikum

Praktikum ini berfokus pada peningkatan efisiensi pengelolaan data artikel dalam jumlah besar. Kita mengimplementasikan dua fitur utama: **Pagination** (pemecahan data per halaman) dan **Pencarian** (filter data berdasarkan kata kunci) untuk mempermudah akses informasi di sisi admin.

## 2. Tujuan Praktikum

* Memahami konsep **Pagination** untuk membatasi tampilan data yang panjang.
* Mampu mengimplementasikan fitur **Pencarian** data di CodeIgniter 4.
* Memanfaatkan *library* bawaan `pager` untuk navigasi halaman yang dinamis.

## 3. Langkah-langkah Praktikum

### A. Implementasi Pagination

1. **Modifikasi Controller**: Mengubah method `admin_index` di `Artikel.php` dengan mengganti fungsi `findAll()` menjadi `paginate(10)` untuk membatasi 10 data per halaman.
2. **Update View**: Menambahkan `<?= $pager->links(); ?>` pada file `admin_index.php` untuk memunculkan navigasi halaman (sebelumnya/sesudahnya).

### B. Implementasi Pencarian

1. **Modifikasi Model**: Mengupdate `ArtikelModel` agar dapat menangani filter data berdasarkan parameter kata kunci (`q`).
2. **Form Pencarian**: Menambahkan elemen `<form>` berisi `<input type="text" name="q">` pada halaman `admin_index.php` agar admin dapat memasukkan kata kunci.
3. **Penyelarasan Pager**: Menggunakan fungsi `only(['q'])` pada objek `$pager` agar kata kunci pencarian tetap tersimpan saat pengguna berpindah halaman (navigasi tetap konsisten).

## 4. Konsep Utama

* **Pagination**: Teknik pemecahan daftar data yang panjang menjadi beberapa halaman untuk mempercepat waktu *loading* halaman dan menjaga keterbacaan data.
* **Query Parameter (`q`)**: Mengambil input dari pengguna melalui URL (via `GET`) untuk memfilter kueri database (menggunakan `WHERE LIKE`).
* **Library Pager**: *Library* bawaan CodeIgniter 4 yang secara otomatis membuat *link* navigasi berdasarkan jumlah total data yang ditemukan.

## 5. Dokumentasi Screenshot

*(Harap lampirkan screenshot berikut untuk laporan Anda):*

* **Screenshot 1**: Halaman Admin Artikel yang menampilkan navigasi halaman (di bawah tabel).
* **Screenshot 2**: Tampilan saat admin melakukan pencarian data tertentu menggunakan form search.
* **Screenshot 3**: URL browser saat proses pencarian berlangsung (menunjukkan parameter `?q=...`).

## 6. Pertanyaan dan Tugas

* **Kenapa Pagination Penting?**: Untuk menghindari *loading* halaman yang berat dan memastikan antarmuka tetap bersih saat database berisi ratusan atau ribuan data.
* **Fungsi `only(['q'])**`: Sangat krusial agar saat kita berpindah dari halaman 1 ke halaman 2, hasil pencarian kita tidak hilang (karena variabel pencarian `q` ikut dibawa dalam link navigasi).

---

Berikut adalah draf `README.md` yang disusun khusus untuk **Praktikum 6: Relasi Tabel dan Query Builder** berdasarkan dokumen yang Anda unggah. Anda dapat menambahkan ini ke dalam file `README.md` di repository `Lab7Web` Anda.

---

# Laporan Praktikum 6: Relasi Tabel dan Query Builder (CodeIgniter 4)

## 1. Deskripsi Praktikum

Praktikum ini berfokus pada implementasi **relasi antar tabel** dalam database MySQL dan penggunaan **Query Builder** di CodeIgniter 4. Kita akan mengimplementasikan relasi *One-to-Many*, di mana satu kategori dapat memiliki banyak artikel, serta menggunakan fitur *Join* untuk menampilkan data dari dua tabel yang berelasi.

## 2. Tujuan Praktikum

* Memahami konsep relasi *One-to-Many* dalam basis data.
* Mampu menggunakan **Query Builder** untuk melakukan operasi *JOIN* antar tabel.
* Mampu menampilkan data artikel beserta nama kategorinya dalam satu tampilan (View).

## 3. Langkah-langkah Praktikum

### A. Persiapan Database

1. **Membuat Tabel Kategori**: Membuat tabel `kategori` untuk menyimpan daftar kategori artikel.
2. **Relasi Tabel**: Menambahkan kolom `id_kategori` pada tabel `artikel` sebagai *Foreign Key* yang merujuk ke tabel `kategori`.

### B. Implementasi Model dan Query Builder

1. **Membuat KategoriModel**: Membuat `KategoriModel.php` untuk mengelola data kategori.
2. **Implementasi JOIN**: Memodifikasi `ArtikelModel` dengan menggunakan Query Builder untuk menggabungkan data artikel dengan kategori. Contoh:
```php
$this->db->table('artikel')
         ->join('kategori', 'artikel.id_kategori = kategori.id_kategori')
         ->get()->getResultArray();

```



### C. Update View (Form & Detail)

1. **Form Tambah/Edit**: Mengubah form input artikel agar menggunakan `<select>` untuk memilih kategori dari database.
2. **Tampilan Detail**: Memodifikasi file `artikel/detail.php` untuk menampilkan nama kategori yang sesuai dengan artikel tersebut.

## 4. Konsep Utama

* **Relasi One-to-Many**: Hubungan di mana satu *parent* (kategori) dapat memiliki banyak *child* (artikel).
* **Query Builder**: Fitur CodeIgniter 4 yang menyederhanakan penulisan query SQL kompleks (seperti `JOIN`) menjadi fungsi-fungsi PHP yang lebih aman dan mudah dibaca.
* **Integrasi View**: Mengirimkan data kategori ke dalam form agar pengguna dapat memilih kategori artikel secara dinamis.

## 5. Dokumentasi Screenshot

*(Harap lampirkan screenshot berikut untuk melengkapi laporan Anda):*

* **Screenshot 1**: Struktur tabel `artikel` dan `kategori` di phpMyAdmin (menunjukkan hubungan relasi).
* **Screenshot 2**: Tampilan Form Tambah/Edit artikel yang menampilkan menu *dropdown* kategori.
* **Screenshot 3**: Tampilan daftar artikel di admin yang sudah menampilkan "Nama Kategori" (bukan sekadar ID).
* **Screenshot 4**: Tampilan detail artikel yang mencantumkan kategorinya.

## 6. Pertanyaan dan Tugas

* **Mengapa menggunakan JOIN?**: Untuk menghindari redundansi data dan mempermudah pengambilan data yang saling terkait (misalnya menampilkan nama kategori asli daripada hanya angka ID).
* **Keuntungan Query Builder**: Selain penulisan yang lebih bersih, Query Builder secara otomatis memproteksi aplikasi dari serangan *SQL Injection*.

---

Berdasarkan dokumen **"Modul Praktikum 7: Upload File Gambar"** yang Anda unggah, berikut adalah draf `README.md` yang lengkap untuk melengkapi laporan praktikum Anda di repository `Lab7Web`.

---

# Laporan Praktikum 7: Upload File Gambar (CodeIgniter 4)

## 1. Deskripsi Praktikum

Praktikum ini bertujuan untuk menambahkan fungsionalitas **Upload File** pada aplikasi manajemen artikel. Dengan fitur ini, admin dapat melampirkan gambar pada setiap artikel yang dibuat, yang kemudian akan disimpan ke dalam direktori server dan nama filenya dicatat dalam database.

## 2. Tujuan Praktikum

* Memahami konsep dasar **File Upload** di CodeIgniter 4.
* Mampu memproses *file* yang diunggah melalui formulir (*form*) ke direktori server.
* Mengintegrasikan nama *file* hasil unggahan ke dalam tabel `artikel` di database.

## 3. Langkah-langkah Praktikum

### A. Penyesuaian Form Input

1. **Atribut Enctype**: Menambahkan atribut `enctype="multipart/form-data"` pada tag `<form>` di `form_add.php`. Ini wajib dilakukan agar *form* dapat mengirimkan data berupa file.
2. **Input File**: Menambahkan elemen `<input type="file" name="gambar">` di dalam formulir.

### B. Pemrosesan di Controller

1. **Menangkap File**: Menggunakan `$this->request->getFile('gambar')` untuk mengambil objek file dari *request*.
2. **Penyimpanan File**: Menggunakan method `$file->move(ROOTPATH . 'public/gambar')` untuk memindahkan file dari *temporary storage* ke direktori tujuan di folder `public/gambar`.
3. **Database Insertion**: Mengambil nama file asli menggunakan `$file->getName()` dan menyimpannya ke dalam kolom `gambar` di tabel `artikel`.

## 4. Konsep Utama

* **Multipart Form Data**: Tipe pengkodean yang diperlukan untuk mengirim data *binary* (seperti gambar) melalui HTTP *post request*.
* **ROOTPATH**: Konstanta di CodeIgniter 4 yang merujuk pada direktori utama proyek, memastikan jalur penyimpanan file (*path*) selalu akurat.
* **Validation**: Proses pengecekan data (seperti `required` pada judul) tetap berjalan sebelum file diproses untuk menjaga integritas data.

## 5. Dokumentasi Screenshot

*(Harap lampirkan screenshot berikut untuk laporan Anda):*

* **Screenshot 1**: Form Tambah Artikel yang sudah menampilkan tombol "Telusuri" (Browse/Choose File).
* **Screenshot 2**: Direktori `public/gambar` yang menunjukkan file gambar berhasil terunggah setelah proses simpan.
* **Screenshot 3**: Daftar artikel di admin yang menampilkan nama file gambar yang berhasil disimpan di database.

## 6. Pertanyaan dan Tugas

* **Mengapa `enctype="multipart/form-data"` penting?**: Tanpa atribut ini, browser hanya akan mengirimkan *string* (teks biasa) dan tidak akan mengirimkan data *binary* dari file, sehingga file tidak akan pernah sampai ke server.
* **Keamanan Upload**: Pada implementasi nyata, sangat disarankan untuk menambahkan validasi tipe file (misal hanya `jpg`, `png`, `jpeg`) dan batasan ukuran file agar server tidak penuh oleh file yang tidak diinginkan atau berbahaya.

---

Berdasarkan dokumen **"Modul Praktikum 8: AJAX"** yang Anda unggah, berikut adalah draf `README.md` yang disusun untuk melengkapi laporan praktikum Anda di repository `Lab11Web` (atau `Lab7Web`).

---

# Laporan Praktikum 8: Implementasi AJAX pada CodeIgniter 4

## 1. Deskripsi Praktikum

Praktikum ini berfokus pada penerapan **AJAX (Asynchronous JavaScript and XML)** dalam aplikasi CodeIgniter 4. Tujuannya adalah membuat antarmuka aplikasi menjadi lebih responsif dengan memungkinkan pembaruan data (seperti menambah, menampilkan, dan menghapus data) tanpa harus melakukan *reload* halaman secara keseluruhan.

## 2. Tujuan Praktikum

* Memahami konsep dasar dan alur kerja komunikasi *Asynchronous* menggunakan AJAX.
* Mampu mengintegrasikan *library* JavaScript/jQuery untuk berkomunikasi dengan *backend* CodeIgniter 4.
* Meningkatkan pengalaman pengguna (*User Experience*) dengan membuat aplikasi web yang lebih dinamis.

## 3. Konsep Utama AJAX

* **Asynchronous**: Proses di latar belakang di mana browser mengirimkan permintaan ke server dan menerima respons tanpa menghentikan aktivitas pengguna di halaman web.
* **Event Trigger**: Aksi pengguna (seperti klik tombol) yang memicu eksekusi *script* JavaScript.
* **Request & Response**: Komunikasi data menggunakan protokol HTTP (`GET`, `POST`, `DELETE`) di mana data biasanya ditukar dalam format **JSON**.

## 4. Langkah-langkah Praktikum

1. **Penyiapan Frontend**: Menggunakan jQuery untuk menangani *event* pada elemen HTML (seperti tombol hapus atau form).
2. **AJAX Call**: Menggunakan fungsi `$.ajax()` untuk mengirim permintaan ke *route* CodeIgniter 4 yang sesuai.
3. **Penyelarasan Backend**: Memastikan *method* di Controller dapat menangani permintaan AJAX (misalnya mengembalikan data JSON atau merespons permintaan penghapusan).
4. **DOM Manipulation**: Mengupdate elemen tabel atau daftar data di halaman setelah menerima respons sukses dari server tanpa melakukan *refresh*.

## 5. Dokumentasi Screenshot

*(Harap lampirkan screenshot berikut untuk laporan Anda):*

* **Screenshot 1**: Halaman daftar artikel yang menggunakan mekanisme AJAX.
* **Screenshot 2**: Tampilan *popup* konfirmasi saat tombol hapus (menggunakan AJAX) ditekan.
* **Screenshot 3**: *Developer Tools* pada tab **Network** di browser (menunjukkan request `XHR` yang berhasil dijalankan).
* **Screenshot 4**: Kode *script* jQuery yang digunakan untuk menangani request AJAX.

## 6. Pertanyaan dan Tugas

* **Apa keuntungan utama AJAX?**: Aplikasi terasa lebih cepat dan responsif karena hanya bagian data yang perlu diperbarui saja yang dimuat ulang oleh server, bukan seluruh struktur halaman (header, footer, dll).
* **Bagaimana menangani error pada AJAX?**: Penggunaan blok `error: function (jqXHR, textStatus, errorThrown)` dalam `$.ajax()` sangat penting untuk memberikan umpan balik (seperti *alert* atau log) kepada pengguna jika terjadi kegagalan koneksi atau *server error*.

---

Berdasarkan dokumen **"Modul Praktikum 9: Implementasi AJAX Pagination dan Search"** yang Anda unggah, berikut adalah draf `README.md` yang lengkap untuk melengkapi laporan praktikum Anda di repository `Lab7Web`.

---

# Laporan Praktikum 9: AJAX Pagination dan Pencarian (CodeIgniter 4)

## 1. Deskripsi Praktikum

Praktikum ini adalah pengembangan lanjutan dari sistem manajemen artikel. Fokus utamanya adalah menggabungkan fitur **Pagination** dan **Pencarian** dengan teknologi **AJAX**. Dengan implementasi ini, proses navigasi antar halaman dan penyaringan data (pencarian) dapat dilakukan secara dinamis tanpa perlu memuat ulang (*reload*) seluruh halaman.

## 2. Tujuan Praktikum

* Mengimplementasikan **AJAX** untuk menangani pagination dan pencarian data.
* Memahami cara mengembalikan data dari server dalam format **JSON** yang kemudian diproses oleh JavaScript.
* Meningkatkan performa aplikasi dan kenyamanan pengguna (UX) dengan meminimalkan waktu tunggu saat perpindahan halaman atau pencarian.

## 3. Langkah-langkah Praktikum

### A. Modifikasi Controller

Mengubah method `admin_index()` pada `Artikel.php` agar mampu mendeteksi request AJAX. Jika request adalah AJAX, controller akan mengembalikan data dalam format JSON yang berisi data artikel dan struktur pagination.

### B. Implementasi JavaScript (Frontend)

1. **Fetch Data**: Membuat fungsi JavaScript untuk mengirim request ke URL tujuan (mengandung parameter `q` untuk pencarian dan `page` untuk pagination).
2. **DOM Manipulation**: Menggunakan jQuery untuk menerima data JSON dari server, lalu memperbarui isi tabel secara otomatis.
3. **Event Listener**: Menambahkan *listener* pada form pencarian (`submit`) dan *dropdown* kategori (`change`) untuk memicu *update* data tanpa *reload*.

## 4. Konsep Utama

* **JSON Response**: Format data ringan yang digunakan untuk pertukaran data antara server (PHP) dan klien (JavaScript).
* **Dynamic DOM Update**: Mengganti konten tabel dan navigasi halaman (pager) melalui JavaScript setelah data baru diterima.
* **Filter Persistence**: Memastikan parameter pencarian (`q`) dan kategori (`kategori_id`) tetap terjaga saat berpindah halaman (navigasi pagination).

## 5. Dokumentasi Screenshot

*(Harap lampirkan screenshot berikut untuk laporan Anda):*

* **Screenshot 1**: Tabel artikel yang memperbarui data secara otomatis saat tombol cari ditekan.
* **Screenshot 2**: Tampilan navigasi halaman yang diperbarui melalui script jQuery (bukan *reload* bawaan browser).
* **Screenshot 3**: *Network Tab* pada *Developer Tools* (F12) yang menunjukkan request `XHR` dengan parameter pencarian.

## 6. Pertanyaan dan Tugas

* **Mengapa Data Harus JSON?**: Karena format JSON sangat ringan dan mudah diurai oleh JavaScript menjadi objek, sehingga sangat efisien untuk memindahkan data dalam jumlah banyak secara *asynchronous*.
* **Tantangan dalam AJAX Pagination**: Tantangan terbesarnya adalah menjaga konsistensi state. Misalnya, jika pengguna sedang mencari artikel "Teknologi" di halaman 2, maka link navigasi harus tetap membawa parameter `q=Teknologi` agar data yang dimuat tetap relevan.

---

Berdasarkan dokumen **"Modul Praktikum 10: API"** yang Anda unggah, ini adalah draf `README.md` yang disusun secara profesional untuk laporan praktikum Anda. Anda dapat menyalin dan menyesuaikannya ke dalam repository proyek `Lab11Web` atau proyek terkait.

---

# Laporan Praktikum 10: REST API dengan CodeIgniter 4

## 1. Deskripsi Praktikum

Praktikum ini berfokus pada implementasi **REST API (Representational State Transfer)** menggunakan *framework* CodeIgniter 4. Tujuannya adalah untuk membuat layanan *web service* yang menyediakan data dalam format JSON, yang nantinya dapat dikonsumsi oleh aplikasi klien (seperti *Frontend* berbasis VueJS atau aplikasi mobile).

## 2. Tujuan Praktikum

* Memahami konsep dasar arsitektur REST API.
* Mampu membangun API *server* menggunakan CodeIgniter 4.
* Mampu melakukan pengujian API menggunakan *tools* seperti Postman.

## 3. Konsep Utama

* **REST Server**: Bertindak sebagai penyedia data/sumber daya.
* **HTTP Methods**: Penggunaan metode standar dalam pertukaran data:
* `GET`: Mengambil data.
* `POST`: Menambah data baru.
* `PUT`: Memperbarui data yang ada.
* `DELETE`: Menghapus data.


* **JSON (JavaScript Object Notation)**: Format pertukaran data yang ringan, mudah dibaca manusia, dan mudah diurai oleh mesin.

## 4. Langkah-langkah Praktikum

1. **Pembuatan Endpoint**: Mendefinisikan rute API pada file `Config/Routes.php` agar dapat menangani request `GET`, `POST`, `PUT`, dan `DELETE`.
2. **Implementasi Controller API**: Membuat *Controller* yang berfungsi untuk memproses *request* dari klien dan mengembalikan *response* berupa data JSON.
3. **Pengujian dengan Postman**:
* Menguji `GET` untuk memastikan data artikel tampil dalam format JSON.
* Menguji `POST` untuk memastikan data artikel baru berhasil disimpan ke database.
* Menguji `DELETE` untuk memastikan data berhasil dihapus melalui URL spesifik (misal: `/post/4`).



## 5. Dokumentasi Screenshot

*(Pastikan Anda melampirkan screenshot berikut untuk laporan Anda):*

* **Screenshot 1**: Hasil pengujian di **Postman** untuk method `GET` (menampilkan daftar artikel dalam format JSON).
* **Screenshot 2**: Hasil pengujian method `POST` (menampilkan *response* sukses setelah menambah data).
* **Screenshot 3**: Hasil pengujian method `DELETE` (menampilkan pesan sukses "Data artikel berhasil dihapus").
* **Screenshot 4**: Konfigurasi rute API pada `app/Config/Routes.php`.

## 6. Pertanyaan dan Tugas

* **Mengapa akses ke API harus dibatasi?**: Karena API adalah antarmuka untuk mengakses database. Jika tidak dibatasi, siapa saja bisa menambah, mengubah, atau menghapus data secara sembarangan, yang berisiko pada keamanan sistem.
* **Apa keunggulan JSON dibandingkan XML?**: JSON lebih ringan karena tidak membutuhkan *tag* penutup yang panjang seperti XML, sehingga mempercepat proses *parsing* data dan menghemat *bandwidth*.

---

Berdasarkan dokumen **"Modul Praktikum 11: VueJS"** yang Anda unggah, berikut adalah draf `README.md` yang lengkap untuk melengkapi laporan praktikum Anda di repository `Lab8VueJS`.

---

# Laporan Praktikum 11: Frontend API dengan VueJS 3

## 1. Deskripsi Praktikum

Praktikum ini berfokus pada implementasi **VueJS 3** untuk membangun *Frontend* yang interaktif. Aplikasi ini berperan sebagai *Client* yang mengambil dan menampilkan data dari REST API (yang dibangun pada praktikum sebelumnya). Fokus utamanya adalah penggunaan *Reactive Data Binding* dan *Component-Based Architecture*.

## 2. Tujuan Praktikum

* Memahami konsep dasar *framework* JavaScript **VueJS 3**.
* Mampu membangun antarmuka web yang dinamis dan interaktif.
* Mengintegrasikan *frontend* VueJS dengan *backend* melalui komunikasi HTTP (menggunakan `Axios` atau `Fetch API`).

## 3. Konsep Utama

* **Reactive Data Binding**: Fitur di mana data di JavaScript terhubung langsung dengan tampilan di HTML. Jika data berubah, tampilan akan otomatis terupdate.
* **Component-Based Architecture**: Aplikasi dibangun dengan memecah UI menjadi bagian-bagian kecil yang mandiri (komponen), sehingga lebih mudah dikelola dan digunakan kembali.
* **Single Page Application (SPA)**: Pola pengembangan web di mana aplikasi dimuat dalam satu halaman saja, sehingga perpindahan antar menu terasa lebih cepat dan tanpa *reload* browser.

## 4. Langkah-langkah Praktikum

1. **Inisialisasi**: Membuat proyek baru dengan VueJS 3 di dalam direktori `lab8_vuejs`.
2. **Component Setup**: Membuat komponen UI untuk menampilkan daftar artikel (misalnya `Artikel.js` atau komponen berbasis `.vue`).
3. **Data Integration**: Menggunakan *lifecycle hook* seperti `mounted()` untuk memanggil API dan mengisi data ke dalam *state* aplikasi.
4. **Interaksi UI**: Membuat *event handler* (seperti `submit` untuk tambah data atau `click` untuk hapus data) yang mengirimkan *request* ke *endpoint* API.

## 5. Dokumentasi Screenshot

*(Pastikan Anda melampirkan screenshot berikut untuk laporan Anda):*

* **Screenshot 1**: Tampilan daftar artikel yang berhasil dimuat oleh VueJS.
* **Screenshot 2**: Struktur folder proyek VueJS Anda di VS Code.
* **Screenshot 3**: *Console log* pada *Developer Tools* (F12) saat berhasil menerima data JSON dari server.
* **Screenshot 4**: Tampilan modal atau form tambah data yang sudah berfungsi secara reaktif.

## 6. Pertanyaan dan Tugas

* **Apa itu VueJS?**: Sebuah *framework* JavaScript untuk membangun antarmuka pengguna (*User Interface*) yang interaktif. Keunggulan utamanya adalah sintaksis yang sederhana, performa yang ringan, dan ekosistem yang sangat kaya.
* **Mengapa aplikasi terasa lebih responsif?**: Karena VueJS memproses data di sisi klien (*client-side*). Setelah data awal diambil, interaksi berikutnya tidak harus selalu menunggu server membuat ulang halaman HTML, melainkan cukup memperbarui bagian data yang berubah saja.

---

Berdasarkan dokumen **"Modul Praktikum 12: VueJS Komponen dan Routing (Single Page Application)"** yang Anda unggah, berikut adalah draf `README.md` yang lengkap untuk melengkapi laporan praktikum Anda di repository `Lab8VueJS`.

---

# Laporan Praktikum 12: VueJS Komponen dan Routing (SPA)

## 1. Deskripsi Praktikum

Praktikum ini berfokus pada transformasi aplikasi web konvensional menjadi **Single Page Application (SPA)** menggunakan **VueJS 3**. Fokus utama adalah penerapan *Vue Components* untuk memecah UI menjadi bagian modular dan *Vue Router* untuk menangani perpindahan halaman di sisi klien tanpa melakukan *hard-reload* pada browser.

## 2. Tujuan Praktikum

* Memahami konsep **Vue Components** agar kode antarmuka lebih modular dan *reusable*.
* Mengimplementasikan **Vue Router** untuk *Client-Side Routing*.
* Membangun aplikasi web yang responsif dengan pengalaman pengguna layaknya aplikasi *mobile*.

## 3. Konsep Utama

* **Vue Components**: Bagian UI yang diisolasi (seperti Header, Footer, Halaman Artikel) yang dapat digunakan berulang kali.
* **Vue Router**: *Library* yang mengatur navigasi antar komponen berdasarkan URL, sehingga browser tidak perlu meminta ulang seluruh halaman ke server.
* **SPA (Single Page Application)**: Arsitektur aplikasi di mana transisi antar halaman terjadi secara instan tanpa *refresh* browser, yang meningkatkan kecepatan dan kenyamanan penggunaan aplikasi.

## 4. Langkah-langkah Praktikum

1. **Pemisahan Komponen**: Memecah file `index.html` menjadi komponen terpisah (misalnya `Home.js`, `Artikel.js`, `About.js`).
2. **Konfigurasi Router**: Mendefinisikan rute dalam objek `const routes = [...]` dan melakukan inisialisasi `VueRouter` untuk menghubungkan URL dengan komponen yang bersangkutan.
3. **Penyisipan `<router-view>**`: Mengganti konten dinamis pada `index.html` dengan tag `<router-view />` sebagai tempat di mana komponen akan dimuat secara bergantian.
4. **Implementasi Navigasi**: Menggunakan `<router-link>` sebagai pengganti tag `<a>` agar perpindahan halaman ditangani oleh *router* secara *asynchronous*.

## 5. Dokumentasi Screenshot

*(Harap lampirkan screenshot berikut untuk melengkapi laporan Anda):*

* **Screenshot 1**: Struktur folder proyek yang menunjukkan pemisahan komponen (folder `components/`).
* **Screenshot 2**: Tampilan halaman saat berpindah menu (Beranda, Kelola Artikel, About) yang menunjukkan aplikasi berjalan sebagai SPA.
* **Screenshot 3**: Kode konfigurasi `routes` pada file `app.js` atau file konfigurasi router Anda.
* **Screenshot 4**: Tampilan halaman `About` (sesuai tugas tambahan di modul) yang menampilkan profil singkat Anda.

## 6. Pertanyaan dan Tugas

* **Apa keuntungan menggunakan Component?**: Komponen membuat kode lebih bersih, mudah di- *maintain*, dan satu komponen yang sudah dibuat dapat digunakan di berbagai bagian aplikasi tanpa harus menulis ulang kode.
* **Kenapa tidak boleh menggunakan `<a>` tag biasa?**: Karena tag `<a>` secara *default* akan memicu perintah *refresh* halaman ke server. Untuk SPA, kita wajib menggunakan `router-link` agar transisi halaman tetap berada di sisi klien (*client-side*).

---

Berdasarkan dokumen **"Modul Praktikum 13: VueJS Autentikasi dan Navigation Guards"** yang Anda unggah, berikut adalah draf `README.md` yang lengkap untuk melengkapi laporan praktikum Anda di repository `Lab11Web_VueJS`.

---

# Laporan Praktikum 13: Autentikasi dan Navigation Guards (SPA Security)

## 1. Deskripsi Praktikum

Praktikum ini berfokus pada aspek keamanan dalam aplikasi **Single Page Application (SPA)**. Kita menerapkan sistem autentikasi pengguna dan membatasi hak akses halaman (*Client-Side Security*) menggunakan fitur **Navigation Guards** dari Vue Router serta integrasi modul login ke *backend* CodeIgniter 4.

## 2. Tujuan Praktikum

* Memahami konsep pembatasan hak akses rute pada sisi klien.
* Mengimplementasikan `router.beforeEach` sebagai *Navigation Guards* untuk melindungi rute privat.
* Membuat API *endpoint* autentikasi di *backend* CI4.
* Mengintegrasikan proses Login/Logout pada aplikasi SPA.

## 3. Konsep Utama

* **Navigation Guards (`router.beforeEach`)**: Fungsi "penjaga pintu" yang berjalan sebelum setiap navigasi rute dilakukan. Jika pengguna belum login (tidak memiliki *token*), sistem akan menolak akses dan mengarahkan kembali ke halaman login.
* **Client-Side Security**: Karena SPA memuat seluruh aplikasi di browser, proteksi rute di sisi klien sangat penting untuk mencegah akses tidak sah ke komponen yang bersifat sensitif/admin.
* **Axios HTTP Post**: Digunakan untuk mengirimkan kredensial login (email/password) ke server untuk diverifikasi, yang jika sukses, akan menyimpan *token* atau status *session* pengguna.

## 4. Langkah-langkah Praktikum

1. **Backend Auth**: Menambahkan API *endpoint* untuk proses verifikasi login di *backend* CodeIgniter 4.
2. **Setup State**: Menambahkan logika pengecekan status login di *frontend* (misalnya menyimpan status di `localStorage` atau *Vuex*).
3. **Navigation Guards**: Menambahkan kode `router.beforeEach((to, from, next) => { ... })` di file router utama. Jika rute memiliki meta `requiresAuth: true` dan pengguna belum login, arahkan ke `/login`.
4. **Implementasi Logout**: Membuat fungsi untuk menghapus data *session* di *frontend* dan mengarahkan kembali ke halaman login.

## 5. Dokumentasi Screenshot

*(Harap lampirkan screenshot berikut untuk laporan Anda):*

* **Screenshot 1**: Halaman Login (saat pengguna diminta memasukkan kredensial).
* **Screenshot 2**: Bukti akses ditolak (saat mencoba mengakses `/admin/artikel` tanpa login/sebelum login).
* **Screenshot 3**: *Network Tab* pada browser saat terjadi *request* login via `Axios POST`.
* **Screenshot 4**: Tampilan menu atas yang berubah menjadi link "Logout" setelah berhasil login.

## 6. Pertanyaan dan Tugas

* **Mengapa harus `router.beforeEach`?**: Agar aplikasi dapat melakukan pengecekan status autentikasi secara otomatis setiap kali pengguna mencoba berpindah halaman, sehingga halaman privat tidak pernah sempat dirender jika pengguna tidak berhak.
* **Keamanan SPA**: *Client-side security* hanyalah lapisan pertama. Anda tetap wajib memvalidasi token atau sesi di setiap API *request* pada *backend* (API Side) untuk memastikan keamanan data yang sesungguhnya.

---

Berdasarkan dokumen **"Modul Praktikum 14: Keamanan API, Autentikasi Token, dan Axios Interceptors"** yang Anda unggah, berikut adalah draf `README.md` untuk melengkapi laporan praktikum terakhir Anda di repository `Lab11Web_VueJS`.

---

# Laporan Praktikum 14: Keamanan API & Token-Based Authentication

## 1. Deskripsi Praktikum

Praktikum ini merupakan tahap final dalam pengamanan aplikasi web. Kita beralih dari sekadar *Client-Side Security* (Vue Router) menuju **Server-Side Security** menggunakan **Token-Based Authentication**. Kita mengimplementasikan *Filter* pada *backend* (CodeIgniter 4) untuk melindungi *endpoint* API dan menggunakan **Axios Interceptors** pada *frontend* (VueJS) untuk menyisipkan token secara otomatis di setiap permintaan HTTP.

## 2. Tujuan Praktikum

* Memahami konsep autentikasi berbasis token (*Bearer Token*).
* Mengimplementasikan `Filters` di CodeIgniter 4 untuk mencegah akses API ilegal oleh pihak luar (seperti via Postman).
* Menggunakan `Axios Interceptors` untuk manajemen transmisi token yang aman dan otomatis dari sisi klien.

## 3. Konsep Utama

* **Token-Based Authentication**: Setelah login, server memberikan *token* unik kepada klien. Klien wajib menyertakan *token* ini di *header* `Authorization` untuk setiap akses ke data sensitif.
* **Server-Side Filter**: Mekanisme di CodeIgniter 4 yang memeriksa validitas *token* sebelum data API dikirimkan. Jika *token* tidak ada atau tidak valid, akses akan ditolak (401 Unauthorized).
* **Axios Interceptors**: Fitur di *library* Axios yang memungkinkan kita mencegat setiap permintaan HTTP (`request`) untuk menambahkan *token* secara otomatis, sehingga kita tidak perlu menulis ulang kode penambahan *header* di setiap fungsi.

## 4. Langkah-langkah Praktikum

1. **Implementasi Filter API**: Membuat *Filter* di CodeIgniter 4 yang memverifikasi *token* pada `header` permintaan masuk.
2. **Setup Interceptors**: Pada file konfigurasi Axios di proyek VueJS, tambahkan *interceptor*:
```javascript
axios.interceptors.request.use(config => {
    const token = localStorage.getItem('token');
    if (token) {
        config.headers.Authorization = `Bearer ${token}`;
    }
    return config;
});

```


3. **Pengujian Keamanan**: Mencoba mengakses endpoint API melalui Postman tanpa *token* untuk memastikan server menolak permintaan (Error 401).
4. **End-to-End Testing**: Melakukan login di aplikasi web, lalu memantau tab *Network* di browser untuk memastikan setiap *request* API sudah menyertakan `Bearer <token>`.

## 5. Dokumentasi Screenshot

*(Harap lampirkan screenshot berikut untuk laporan Anda):*

* **Screenshot 1**: Bukti penolakan akses (Error 401) di Postman saat mencoba mengakses API tanpa token.
* **Screenshot 2**: Tab *Network* (F12 -> Network) yang menunjukkan *request* API sukses dengan *header* `Authorization: Bearer <token>`.
* **Screenshot 3**: Kode `Axios Interceptors` yang Anda terapkan di VueJS.
* **Screenshot 4**: Konfigurasi `Filter` di CodeIgniter 4 (file `app/Config/Filters.php`).

## 6. Kesimpulan & Analisis

* **Perbedaan Perlindungan**:
* **Vue Router Navigation Guards (Client-Side)**: Berfungsi untuk memberikan pengalaman pengguna yang baik dengan membatasi navigasi menu di browser agar tidak membingungkan pengguna yang belum login. Namun, ini **tidak aman** karena data API tetap terbuka bagi siapa saja yang tahu URL-nya.
* **CodeIgniter Filters (Server-Side)**: Adalah perlindungan yang **sebenarnya**. Mekanisme ini memastikan bahwa meskipun seseorang mencoba "menembak" API secara langsung via URL/Postman tanpa membawa token, server akan tetap menolak akses tersebut.



---

