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

Penggunaan *View Layout* dan *View Cell* dalam CodeIgniter 4 merupakan langkah maju dalam menciptakan aplikasi web yang lebih modular, efisien, dan mudah dipelihara. Dengan menerapkan konsep *View Layout*, pengembang dapat menetapkan kerangka halaman standar yang konsisten di seluruh aplikasi, sehingga meminimalisir duplikasi kode yang sering terjadi pada bagian *header*, *footer*, dan elemen navigasi lainnya. Pendekatan ini tidak hanya menghemat waktu dalam penulisan kodingan, tetapi juga memastikan setiap perubahan struktur halaman dapat diterapkan secara global melalui satu file pusat.

Di sisi lain, *View Cell* memberikan fleksibilitas tambahan dengan memungkinkan pembuatan komponen UI yang bersifat modular dan mandiri. Elemen-elemen yang sering muncul di berbagai bagian situs, seperti widget artikel terkini atau kotak informasi, kini dapat dikelola sebagai satu kesatuan logika yang terpisah dari *controller* utama. Integrasi kedua fitur ini menciptakan alur pengembangan yang jauh lebih bersih, di mana setiap komponen aplikasi memiliki batasan yang jelas, meningkatkan *readability* kode, serta mempercepat proses pengembangan aplikasi skala besar.

* **View Layout**: Teknik standarisasi struktur halaman menggunakan file *main template* dan *sections*.
* **View Cell**: Komponen UI modular yang dapat digunakan kembali dengan logika data mandiri.
* **Modularitas**: Meningkatkan *reusability* (kemampuan guna ulang) komponen di berbagai halaman web.

#### 1. Membuat Layout Utama (`app/Views/layout/main.php`)

Konsep *View Layout* menggunakan sistem *extends* dan *section* untuk membungkus konten.

* **Kodingan Layout**:
```php
<!DOCTYPE html>
<html>
<head><title><?= $title ?? 'My Website' ?></title></head>
<body>
    <header><h1>Layout Sederhana</h1></header>
    <?= $this->renderSection('content') ?> </body>
</html>

```


* **Penjelasan**: `renderSection('content')` berfungsi sebagai titik di mana konten dari *view* spesifik (seperti halaman Home atau Artikel) akan disisipkan ke dalam kerangka *layout* utama.

#### 2. Implementasi View Cell (`app/Cells/ArtikelTerkini.php`)

*View Cell* memungkinkan logika data dipisahkan dari *controller* utama untuk elemen-elemen kecil.

* **Kodingan Class Cell**:
```php
namespace App\Cells;
class ArtikelTerkini {
    public function render() {
        $model = new \App\Models\ArtikelModel();
        $artikel = $model->findAll(5); // Ambil 5 artikel terbaru
        return view('components/artikel_terkini', ['artikel' => $artikel]);
    }
}

```


* **Penjelasan**: *Class* ini bertugas mengambil data dari *Model* secara langsung, sehingga saat kita memanggil *cell* ini, ia sudah membawa data yang diperlukan tanpa membebani *controller* utama.

#### 3. Memanggil Komponen di View

Penggunaan *View Cell* pada halaman utama sangat praktis untuk memanggil widget.

* **Kodingan Pemanggilan**:
```php
<?= view_cell('\App\Cells\ArtikelTerkini::render') ?>

```


* **Penjelasan**: Baris kode ini secara otomatis menjalankan fungsi `render()` dalam *class* `ArtikelTerkini` dan menampilkan hasil *view*-nya di lokasi pemanggilan tersebut, menjadikan tampilan web jauh lebih dinamis.

#### 4. Dokumentasi dan Bukti Praktikum

Dokumentasi ini membuktikan keberhasilan penerapan konsep modular pada proyek Anda.

* **Screenshot 1**: Halaman utama yang telah menerapkan `layout/main.php`.
* **Screenshot 2**: Tampilan widget "Artikel Terkini" yang dihasilkan melalui *view_cell*.
* **Screenshot 3**: Struktur folder `app/Cells` dan `app/Views/components` di VS Code.

#### 5. Analisis Konsep Utama

* **View Layout**: Memberikan struktur halaman yang terstandarisasi. Perubahan pada struktur dasar (seperti penambahan menu navigasi) hanya perlu dilakukan pada satu file `layout/main.php`.
* **View Cell vs View Biasa**: *View* biasa digunakan untuk merender seluruh halaman, sementara *View Cell* digunakan untuk komponen UI kecil yang memiliki logika data sendiri (independen) dan dapat dipanggil di banyak tempat tanpa perlu mengulang *query* di setiap *controller*.

---

*Demikian penjelasan mengenai penerapan View Layout dan View Cell yang telah meningkatkan efisiensi dan modularitas aplikasi Anda.*

# Laporan Praktikum 4: Modul Login (Authentication & Filter)

Sistem autentikasi merupakan fondasi keamanan yang mutlak dalam pengembangan aplikasi web untuk membatasi akses pada fitur-fitur sensitif. Melalui sistem ini, pengembang dapat memastikan bahwa hanya pengguna yang sah, yang kredensialnya terverifikasi dalam database, yang diberikan izin untuk mengakses modul administratif. Implementasi ini menjamin bahwa setiap data yang dikelola tetap terlindungi dari akses pihak yang tidak bertanggung jawab.

Selain aspek keamanan, penggunaan *Filter* dan *Session* memberikan fleksibilitas dalam pengelolaan alur akses pengguna. *Filter* berfungsi sebagai *middleware* yang secara otomatis melakukan validasi status login sebelum setiap permintaan halaman diproses, sementara *session* menjaga integritas status pengguna tetap tersimpan selama sesi berlangsung. Kombinasi ini menciptakan pengalaman aplikasi yang profesional, di mana pengguna tidak perlu melakukan proses login berulang kali, namun sistem tetap mampu mengamankan halaman secara otomatis.

* **Database & Tabel User**: Penyimpanan kredensial pengguna untuk verifikasi login.
* **Authentication**: Proses validasi identitas berdasarkan data *user* di database.
* **Filter (Middleware)**: Penjaga akses yang otomatis memblokir pengguna yang belum login.
* **Session**: Objek penyimpan status login pengguna yang persisten.

#### 1. Persiapan Database dan Pembuatan Tabel

Sistem login memerlukan penyimpanan data pengguna yang aman. Kita perlu membuat tabel `user` untuk menampung *username* dan *password* yang nantinya akan divalidasi.

* **Struktur Tabel `user**`:
* `id`: INT, 11, Primary Key, Auto Increment.
* `username`: VARCHAR(200).
* `useremail`: VARCHAR(200).
* `userpassword`: VARCHAR(200).


* **Perintah SQL**:
```sql
CREATE TABLE user (
    id INT(11) auto_increment,
    username VARCHAR(200) NOT NULL,
    useremail VARCHAR(200),
    userpassword VARCHAR(200),
    PRIMARY KEY(id)
);

```



#### 2. Membuat UserModel

*Model* bertindak sebagai representasi data pengguna. Kita membuat `UserModel.php` di `app/Models/` untuk menangani proses *query* data.

* **Kodingan Model**:
```php
namespace App\Models;
use CodeIgniter\Model;

class UserModel extends Model {
    protected $table = 'user';
    protected $primaryKey = 'id';
    protected $allowedFields = ['username', 'useremail', 'userpassword'];
}

```


* **Penjelasan**: *Model* ini menyediakan akses ke tabel `user`. Penggunaan `$allowedFields` sangat krusial untuk keamanan, memastikan hanya kolom yang ditentukan yang dapat diproses oleh sistem.

#### 3. Implementasi Filter (Penjaga Pintu)

*Filter* digunakan untuk membatasi akses ke grup `admin` agar tidak dapat diakses oleh publik tanpa izin.

* **Kodingan `app/Config/Routes.php**`:
```php
$routes->group('admin', ['filter' => 'auth'], function ($routes) {
    $routes->get('artikel', 'Artikel::admin_index');
    $routes->add('artikel/add', 'Artikel::add');
    $routes->add('artikel/edit/(:any)', 'Artikel::edit/$1');
    $routes->get('artikel/delete/(:any)', 'Artikel::delete/$1');
});

```


* **Penjelasan**: Fungsi `['filter' => 'auth']` memastikan bahwa sistem akan melakukan pengecekan status login setiap kali URL di dalam grup `admin` diakses. Jika *session* tidak ditemukan, pengguna secara otomatis diarahkan ke halaman login.

#### 4. Manajemen Sesi dan Logout

Sistem harus mampu mengakhiri sesi dengan aman saat pengguna menekan tombol *logout*.

* **Kodingan Controller (`app/Controllers/User.php`)**:
```php
public function logout() {
    session()->destroy(); // Menghapus seluruh data session di server
    return redirect()->to('/user/login'); // Kembali ke halaman login
}

```


* **Penjelasan**: Fungsi `session()->destroy()` sangat vital dalam keamanan aplikasi karena menghapus semua data *session* yang tersimpan di server, sehingga sistem akan menganggap pengguna telah keluar sepenuhnya dari aplikasi.

#### 5. Dokumentasi dan Bukti Praktikum

Dokumentasi ini membuktikan bahwa konfigurasi sistem autentikasi telah berhasil diterapkan.

* **Screenshot 1**: Struktur tabel `user` di phpMyAdmin, yang menunjukkan kolom `id`, `username`, `useremail`, dan `userpassword`.
* **Screenshot 2**: Tampilan halaman `Login` saat pertama kali diakses, siap menerima input pengguna.
* **Screenshot 3**: Tampilan pesan error atau pengalihan halaman saat mencoba mengakses `admin/artikel` tanpa login.
* **Screenshot 4**: Kode pada `Config/Routes.php` yang menunjukkan penerapan `filter => 'auth'`.

Demikian penjelasan laporan praktikum mengenai implementasi sistem autentikasi yang telah mencakup tahapan persiapan *database*, konfigurasi *filter* keamanan, hingga manajemen *session* pengguna secara komprehensif.

# Laporan Praktikum 5: Pagination dan Pencarian (CodeIgniter 4)


Dalam pengembangan aplikasi web yang dinamis, pengelolaan data dalam jumlah besar memerlukan teknik yang efisien agar sistem tetap responsif dan mudah digunakan oleh administrator. Implementasi *pagination* menjadi solusi krusial untuk memecah daftar data yang sangat panjang menjadi beberapa halaman yang lebih ringkas, sehingga beban *server* saat melakukan *query* database dapat dikurangi secara signifikan dan waktu *loading* halaman menjadi jauh lebih cepat.

Di samping itu, fitur pencarian memberikan kemudahan akses bagi pengguna untuk menemukan informasi spesifik secara instan di tengah tumpukan data yang banyak. Dengan menggabungkan kedua fitur ini—*pagination* untuk navigasi data dan mekanisme pencarian untuk penyaringan data—aplikasi akan memiliki standar pengelolaan data yang profesional. Hal ini tidak hanya meningkatkan kenyamanan pengguna dalam berinteraksi dengan antarmuka admin, tetapi juga menjaga agar pengalaman navigasi tetap konsisten meskipun jumlah data terus bertambah dari waktu ke waktu.

* **Pagination**: Proses memecah tampilan data yang panjang menjadi beberapa halaman.
* **Pencarian**: Fitur untuk memfilter hasil *query* database berdasarkan kata kunci input pengguna.
* **Pager Library**: Komponen bawaan CodeIgniter 4 untuk menghasilkan *link* navigasi halaman secara otomatis.

#### 1. Implementasi Pagination

Langkah ini dilakukan untuk membatasi tampilan data agar tidak membebani tampilan *browser*.

* **Kodingan Controller (`admin_index`)**:
```php
public function admin_index() {
    $model = new ArtikelModel();
    $data = [
        'title' => 'Daftar Artikel',
        'artikel' => $model->paginate(10), // Membatasi 10 data per halaman
        'pager' => $model->pager,
    ];
    return view('artikel/admin_index', $data);
}

```


* **Penjelasan**: Fungsi `paginate(10)` secara otomatis mengatur *query* untuk mengambil 10 baris data saja. Variabel `pager` kemudian dikirim ke *view* untuk memunculkan navigasi tombol halaman secara otomatis.

#### 2. Implementasi Pencarian dan Penyelarasan Pager

Pencarian memungkinkan admin menyaring data, dan penggunaan `only(['q'])` menjaga konsistensi pencarian saat pindah halaman.

* **Kodingan Form View**:
```php
<form method="get" class="form-search">
    <input type="text" name="q" value="<?= $q; ?>" placeholder="Cari data">
    <input type="submit" value="Cari" class="btn btn-primary">
</form>
<?= $pager->only(['q'])->links(); ?>

```


* **Penjelasan**: Elemen `form` mengirimkan kata kunci melalui metode `GET`. Fungsi `only(['q'])` sangat krusial karena ia akan memastikan bahwa ketika admin berpindah ke halaman 2, 3, dan seterusnya, parameter pencarian `q` akan tetap terbawa dalam *link* navigasi sehingga hasil filter tidak hilang.

#### 3. Dokumentasi dan Bukti Praktikum

Bagian dokumentasi ini adalah bukti autentik bahwa konfigurasi fitur *pagination* dan pencarian telah berhasil diterapkan dengan benar.

* **Screenshot 1**: Halaman Admin Artikel yang menampilkan navigasi halaman (di bawah tabel).
* **Screenshot 2**: Tampilan saat admin melakukan pencarian data tertentu menggunakan form search.
* **Screenshot 3**: URL browser saat proses pencarian berlangsung yang menunjukkan parameter `?q=...` sebagai bukti bahwa kueri filter telah terkirim dengan benar ke sistem.

#### 4. Analisis Konsep Utama

* **Kenapa Pagination Penting?**: Untuk menghindari *loading* halaman yang berat dan memastikan antarmuka tetap bersih saat database berisi ratusan atau ribuan data.
* **Fungsi `only(['q'])**`: Sangat krusial agar saat kita berpindah dari halaman 1 ke halaman 2, hasil pencarian kita tidak hilang karena variabel pencarian `q` ikut dibawa dalam *link* navigasi.

Demikian penjelasan laporan praktikum mengenai implementasi sistem *pagination* dan pencarian yang telah mencakup tahapan modifikasi *controller*, penyesuaian *view* dengan *pager*, hingga sinkronisasi data pencarian agar aplikasi tetap efisien dan mudah dikelola.


# Laporan Praktikum 6: Relasi Tabel dan Query Builder (CodeIgniter 4)

Dalam pengembangan sistem informasi yang kompleks dan berskala besar, pengelolaan data yang saling terhubung antar entitas merupakan fondasi utama untuk mencapai efisiensi basis data. Implementasi relasi *One-to-Many* antara tabel `kategori` dan tabel `artikel` bukan sekadar teknik pengorganisasian data, melainkan strategi krusial untuk menghindari redundansi informasi yang seringkali menjadi masalah utama dalam basis data yang tidak terstruktur. Dengan memastikan setiap artikel terikat pada kategori yang tepat melalui *Foreign Key*, sistem secara otomatis menjamin integritas data dan memudahkan proses klasifikasi konten secara logis dan terukur.

Di sisi lain, penggunaan *Query Builder* dalam framework CodeIgniter 4 memberikan keunggulan teknis yang sangat signifikan bagi pengembang dalam melakukan interaksi dengan basis data. Melalui fitur *Join*, pengembang dapat menarik data dari berbagai tabel yang berelasi dalam satu eksekusi kueri yang efisien dan aman, sehingga meminimalisir kompleksitas penulisan kueri SQL manual yang rentan terhadap kesalahan. *Query Builder* tidak hanya mempercepat waktu pengembangan aplikasi melalui abstraksi fungsi-fungsi PHP yang intuitif, tetapi juga memberikan lapisan perlindungan otomatis terhadap serangan *SQL Injection*, menjadikannya metode standar dalam membangun aplikasi web yang tangguh, aman, dan mudah untuk dikembangkan di masa depan.

* **Relasi One-to-Many**: Hubungan hierarkis di mana satu *parent* (kategori) dapat memiliki banyak *child* (artikel).
* **Query Builder**: Mekanisme CI4 untuk membangun kueri SQL secara dinamis melalui objek, meningkatkan keamanan dan keterbacaan kode.
* **Integrasi View**: Sinkronisasi antara data relasional di *database* dengan elemen antarmuka pengguna seperti *dropdown* dan tabel detail.

#### 1. Persiapan Database dan Struktur Relasi

Untuk menghubungkan data, kita harus memastikan tabel `artikel` memiliki kolom `id_kategori` yang menjadi jembatan menuju tabel `kategori`.

* **Struktur Tabel**:
* **Tabel Kategori**: `id_kategori` (PK), `nama_kategori`.
* **Tabel Artikel**: `id` (PK), `judul`, `isi`, `id_kategori` (FK).


* **Konsep**: Relasi ini memungkinkan kita untuk melakukan *mapping* nama kategori ke artikel tanpa harus menuliskan nama kategori berulang kali di tabel artikel.

#### 2. Implementasi Query Builder (JOIN) pada ArtikelModel

Ini adalah inti dari praktikum ini: melakukan *Join* agar data artikel yang diambil juga menyertakan informasi kategori secara otomatis.

* **Kodingan Implementasi JOIN (`app/Models/ArtikelModel.php`)**:
```php
public function getArtikel() {
    return $this->db->table('artikel')
                    ->join('kategori', 'artikel.id_kategori = kategori.id_kategori')
                    ->select('artikel.*, kategori.nama_kategori')
                    ->get()
                    ->getResultArray();
}

```


* **Penjelasan Detail**:
* `$this->db->table('artikel')`: Menetapkan tabel utama yang akan menjadi basis pencarian data.
* `->join('kategori', 'artikel.id_kategori = kategori.id_kategori')`: Melakukan penggabungan tabel berdasarkan kolom yang sama (ID Kategori). Tipe *Join* secara *default* adalah `INNER JOIN`, yang artinya hanya data yang memiliki pasangan kategori yang akan muncul.
* `->select('artikel.*, kategori.nama_kategori')`: Mengambil seluruh kolom dari artikel dan menambahkan kolom nama kategori untuk digunakan di *view*.
* `->get()->getResultArray()`: Eksekusi kueri dan mengembalikan hasil dalam bentuk *array* asosiatif yang siap di-*looping* di sisi *view*.



#### 3. Integrasi Dropdown pada Form Tambah/Edit

Agar admin dapat memilih kategori, kita perlu mengirimkan data kategori dari *controller* ke *view* dan menampilkannya sebagai *select option*.

* **Kodingan View Form (`app/Views/artikel/form_add.php`)**:
```php
<label for="id_kategori">Pilih Kategori:</label>
<select name="id_kategori" id="id_kategori" required>
    <option value="">-- Pilih Kategori --</option>
    <?php foreach($kategori as $k): ?>
        <option value="<?= $k['id_kategori']; ?>">
            <?= $k['nama_kategori']; ?>
        </option>
    <?php endforeach; ?>
</select>

```


* **Penjelasan Detail**:
* `foreach($kategori as $k)`: Melakukan iterasi terhadap data kategori yang dikirim dari *controller*.
* `<option value="<?= $k['id_kategori']; ?>">`: Menetapkan nilai `ID` ke sistem (yang akan disimpan ke *database* di tabel artikel).
* `<?= $k['nama_kategori']; ?>`: Menampilkan nama kategori yang terbaca oleh admin di layar.



#### 4. Menampilkan Nama Kategori pada Detail Artikel

Agar tampilan detail artikel lebih informatif, kita menampilkan nama kategorinya.

* **Kodingan View Detail (`app/Views/artikel/detail.php`)**:
```php
<article>
    <h2><?= $artikel['judul']; ?></h2>
    <p><strong>Kategori:</strong> <?= $artikel['nama_kategori']; ?></p>
    <p><?= $artikel['isi']; ?></p>
</article>

```


* **Penjelasan**: Karena kita sudah melakukan *Join* di *model*, maka variabel `$artikel` kini sudah berisi indeks `'nama_kategori'`, sehingga kita cukup memanggilnya secara langsung tanpa melakukan *query* tambahan di *view*.

#### 5. Dokumentasi dan Bukti Praktikum

Dokumentasi ini membuktikan keberhasilan penerapan relasi tabel dalam proyek Anda.

* **Screenshot 1**: Struktur tabel `artikel` dan `kategori` di phpMyAdmin (menunjukkan hubungan relasi).
* **Screenshot 2**: Tampilan Form Tambah/Edit artikel yang menampilkan menu *dropdown* kategori.
* **Screenshot 3**: Tampilan daftar artikel di admin yang sudah menampilkan "Nama Kategori" (bukan sekadar ID).
* **Screenshot 4**: Tampilan detail artikel yang mencantumkan kategorinya.

#### 6. Analisis Konsep Utama

* **Mengapa menggunakan JOIN?**: Untuk menghindari redundansi data dan mempermudah pengambilan data yang saling terkait (misalnya menampilkan nama kategori asli daripada hanya angka ID).
* **Keuntungan Query Builder**: Selain penulisan yang lebih bersih, *Query Builder* secara otomatis memproteksi aplikasi dari serangan *SQL Injection* dengan menggunakan *binding* parameter internal.

Demikian penjelasan laporan praktikum mengenai implementasi sistem relasi tabel dan *Query Builder* yang telah mencakup tahapan persiapan *database*, modifikasi *model* dengan *JOIN*, hingga integrasi *dropdown* dinamis pada *view* untuk manajemen artikel yang lebih profesional.

# Laporan Praktikum 7: Upload File Gambar (CodeIgniter 4)

Implementasi fitur *file upload* dalam aplikasi web merupakan kebutuhan esensial untuk mendukung konten yang lebih interaktif dan visual, seperti pada sistem manajemen artikel berita. Dengan memungkinkan pengguna mengunggah gambar secara langsung melalui antarmuka admin, aplikasi menjadi lebih informatif dan menarik bagi pembaca, sekaligus memberikan kontrol penuh kepada administrator dalam mengelola aset media yang melekat pada setiap artikel. Fitur ini mengubah aplikasi dari sekadar penyaji teks statis menjadi platform manajemen konten yang jauh lebih kaya, dinamis, dan terlihat profesional.

Selain aspek fungsionalitas, keamanan dalam proses *upload* file menjadi perhatian utama yang harus diperhatikan dengan sangat cermat oleh pengembang. Melalui framework CodeIgniter 4, proses penanganan *file* dilakukan dengan metode yang aman dan terstruktur, mulai dari validasi tipe *file*, pemindahan *file* dari *temporary storage* ke direktori penyimpanan permanen yang ditentukan, hingga pengintegrasian nama *file* tersebut ke dalam *database*. Integrasi sistematis ini memastikan bahwa setiap gambar yang diunggah terasosiasi dengan data artikel yang benar, menciptakan ekosistem data yang terorganisir dan stabil untuk kebutuhan pengembangan aplikasi jangka panjang.

* **File Upload**: Proses memindahkan file dari komputer klien ke direktori *server* aplikasi.
* **Validasi**: Tahapan krusial untuk memastikan file yang diunggah sesuai dengan format, tipe MIME, dan aturan yang ditentukan sebelum diproses oleh server.
* **Storage Path**: Pengaturan direktori penyimpanan di `public/gambar` agar file gambar dapat diakses secara publik melalui *browser*.
* **Enctype**: Atribut formulir yang wajib ada untuk memungkinkan pengiriman data *binary* (gambar) melalui protokol HTTP.

#### 1. Implementasi Logika Upload pada Controller

Kita memodifikasi *method* `add` di dalam *controller* agar aplikasi mampu memproses *file* gambar yang diunggah melalui *form* secara efisien dan aman.

* **Kodingan Controller Lengkap (`app/Controllers/Artikel.php`)**:
```php
public function add() {
    // 1. Inisialisasi Service Validasi
    $validation = \Config\Services::validation();
    $validation->setRules(['judul' => 'required']);
    $isDataValid = $validation->withRequest($this->request)->run();

    if ($isDataValid) {
        // 2. Menangkap file dari request input dengan nama 'gambar'
        $file = $this->request->getFile('gambar');

        // 3. Memindahkan file ke folder public/gambar
        // ROOTPATH merujuk pada direktori utama proyek CodeIgniter
        if ($file->isValid() && !$file->hasMoved()) {
            $file->move(ROOTPATH . 'public/gambar');

            // 4. Inisialisasi Model Artikel
            $artikel = new ArtikelModel();

            // 5. Menyimpan data ke database
            $artikel->insert([
                'judul'  => $this->request->getPost('judul'),
                'isi'    => $this->request->getPost('isi'),
                'slug'   => url_title($this->request->getPost('judul')),
                'gambar' => $file->getName(), // Mengambil nama file asli
            ]);
            return redirect('admin/artikel');
        }
    }
    $title = "Tambah Artikel";
    return view('artikel/form_add', compact('title'));
}

```


* **Penjelasan Detail Kodingan**:
* `$this->request->getFile('gambar')`: Metode ini digunakan untuk mengambil objek *file* yang diunggah melalui formulir.
* `$file->move(ROOTPATH . 'public/gambar')`: Perintah ini memindahkan *file* dari memori *buffer* sementara ke lokasi permanen di direktori `public/gambar`. Penggunaan `ROOTPATH` sangat penting agar *path* direktori bersifat absolut dan tidak rusak meski aplikasi dipindahkan ke server lain.
* `$file->getName()`: Fungsi ini mengambil nama *file* asli (misal: `gambar_artikel.jpg`) untuk disimpan ke dalam kolom `gambar` di tabel database. Dengan cara ini, kita hanya menyimpan referensi nama *file*, yang jauh lebih ringan bagi basis data.



#### 2. Penyesuaian View (Form Tambah)

Untuk memungkinkan pengiriman data gambar, struktur formulir HTML harus ditingkatkan dengan atribut khusus agar dapat menangani format *binary*.

* **Kodingan View (`app/Views/artikel/form_add.php`)**:
```html
<form action="" method="post" enctype="multipart/form-data">
    <p>
        <label for="judul">Judul Artikel</label>
        <input type="text" name="judul" id="judul" required>
    </p>
    <p>
        <label for="isi">Isi Artikel</label>
        <textarea name="isi" id="isi"></textarea>
    </p>
    <p>
        <label for="gambar">Upload Gambar Artikel</label>
        <input type="file" name="gambar" id="gambar">
    </p>
    <p>
        <button type="submit" class="btn btn-primary">Simpan Artikel</button>
    </p>
</form>

```


* **Penjelasan Detail Kodingan**:
* `enctype="multipart/form-data"`: Atribut ini **sangat wajib** disertakan pada *tag* `<form>`. Secara *default*, formulir HTML hanya mengirimkan teks (*plain text*). Tanpa atribut *multipart*, data *file* tidak akan terbaca oleh server dan proses *upload* akan selalu gagal (nilai *file* akan `null`).
* `<input type="file" name="gambar">`: Elemen antarmuka ini memberikan kontrol *browse* kepada admin untuk memilih *file* dari penyimpanan lokal mereka.



#### 3. Dokumentasi dan Bukti Praktikum

Dokumentasi ini berfungsi sebagai bukti autentik bahwa konfigurasi sistem *upload* gambar telah berhasil diintegrasikan.

* **Screenshot 1**: Form "Tambah Artikel" yang telah memiliki tombol "Telusuri" (Browse/Choose File).
* **Screenshot 2**: Direktori `public/gambar` di *file manager* yang menunjukkan file gambar berhasil terunggah secara fisik.
* **Screenshot 3**: Daftar artikel pada halaman *admin* yang menampilkan nama file gambar yang telah sukses tersimpan di database.

#### 4. Analisis Konsep Utama

* **Validasi Tipe File**: Sangat penting untuk menambahkan validasi keamanan (seperti `is_image[gambar]|mime_in[gambar,image/jpg,image/jpeg,image/png]`) di masa depan untuk mencegah *upload* file berbahaya (*script*).
* **Efisiensi Database**: Kita hanya menyimpan nama *file* (string) di database, bukan gambar tersebut dalam bentuk *BLOB* (biner), karena menyimpan gambar dalam database dapat membuat ukuran *file* database membengkak dengan cepat dan menurunkan performa aplikasi.
* **ROOTPATH**: Konstanta bawaan CI4 yang memastikan jalur direktori penyimpanan selalu akurat terlepas dari sistem operasi yang digunakan (*Windows/Linux*).

Demikian penjelasan laporan praktikum mengenai implementasi sistem *file upload* yang mencakup tahapan modifikasi *controller*, penyesuaian *form view* dengan atribut *multipart*, hingga manajemen penyimpanan file di direktori server agar aplikasi lebih interaktif dan profesional.

Tentu, saya sangat memahami kebutuhan Anda untuk laporan praktikum yang komprehensif. Berikut adalah penyusunan kembali laporan **Praktikum 8: AJAX** dengan pembahasan yang sangat detail, teknis, dan mendalam pada setiap baris kodenya agar laporan Anda memiliki bobot teknis yang maksimal.

---

# Laporan Praktikum 8: Implementasi AJAX pada CodeIgniter 4

Implementasi *Asynchronous JavaScript and XML* (AJAX) dalam ekosistem CodeIgniter 4 merupakan lompatan besar dalam transformasi antarmuka aplikasi dari statis menjadi dinamis. Secara fundamental, AJAX memutus ketergantungan *browser* terhadap *full-page reload* setiap kali terjadi pembaruan data atau penghapusan entitas. Dalam konteks aplikasi manajemen artikel, hal ini berarti setiap tindakan seperti penghapusan entitas atau pembaruan status dapat dilakukan di latar belakang (*background*) tanpa mengganggu aktivitas pengguna. Dengan berkomunikasi langsung melalui *XMLHttpRequest* atau *Fetch API*, aplikasi dapat menerima respons dari *server* dalam format JSON dan memanipulasi elemen DOM (seperti baris pada tabel) secara instan, sehingga aplikasi terasa jauh lebih modern dan responsif.

Secara teknis, kekuatan AJAX terletak pada kemampuannya menjaga integritas sesi pengguna sambil memberikan *feedback* visual yang instan. Ketika pengguna menekan tombol hapus, *script* JavaScript akan menangkap *event* tersebut, mengirimkan HTTP Request (seperti metode `DELETE`) ke rute yang telah ditentukan, dan menunggu respons dari *server*. Keberhasilan integrasi ini sangat bergantung pada sinkronisasi yang presisi antara logika *routing* di sisi *backend* (CodeIgniter) dan penanganan *callback* di sisi *frontend* (jQuery). Dokumentasi yang mendalam mengenai alur ini sangat penting agar kita tidak hanya paham cara kerja kodingannya, tetapi juga bagaimana setiap potongan kode memberikan kontribusi pada responsivitas aplikasi secara keseluruhan.

* **Event Handling**: Teknik penanganan aksi pengguna melalui *event listener* yang terikat pada elemen DOM.
* **Request Lifecycle**: Alur dari pengiriman HTTP Request, pemrosesan di *Controller*, hingga pengembalian respons (biasanya `JSON`).
* **DOM Manipulation**: Teknik memperbarui konten tabel secara spesifik setelah server mengonfirmasi keberhasilan operasi data tanpa memuat ulang seluruh halaman.
* **JSON Exchange**: Format data standar yang digunakan untuk pertukaran informasi ringan dan cepat antara *client* dan *server*.

#### 1. Pembahasan Kodingan: Sisi Frontend (jQuery)

Kodingan ini adalah otak dari interaksi *client-side*. Kita menggunakan *event delegation* agar tombol yang dihasilkan secara dinamis oleh AJAX tetap tertangani dengan baik oleh sistem.

```javascript
$(document).ready(function() {
    // Event delegation: Mengikat event click pada elemen yang memiliki class .btn-delete
    // Penggunaan $(document) memastikan elemen yang baru dimuat via AJAX tetap terpantau
    $(document).on('click', '.btn-delete', function(e) {
        e.preventDefault(); // Mencegah link melakukan navigasi ke URL default
        
        // Mengambil ID artikel dari atribut data-id pada elemen yang diklik
        var id = $(this).data('id'); 

        if (confirm('Apakah Anda yakin ingin menghapus artikel ini?')) {
            // Melakukan request AJAX ke server
            $.ajax({
                url: "<?= base_url('artikel/delete/'); ?>" + id, // URL menuju method delete di Controller
                method: "DELETE", // Metode HTTP DELETE untuk operasi penghapusan
                dataType: "json", // Memberitahu server bahwa kita mengharapkan format JSON
                
                // Fungsi sukses: Dijalankan jika server memberikan respons 200 OK
                success: function(response) {
                    if(response.status == 'success') {
                        alert(response.message); // Notifikasi sukses dari server
                        loadData(); // Fungsi krusial: Memuat ulang isi tabel tanpa reload halaman
                    } else {
                        alert('Gagal menghapus: ' + response.message);
                    }
                },
                
                // Fungsi error: Dijalankan jika server down, timeout, atau terjadi error 500
                error: function (jqXHR, textStatus, errorThrown) {
                    console.error("AJAX Error details:", textStatus, errorThrown);
                    alert('Terjadi kesalahan pada server. Pastikan koneksi stabil.');
                }
            });
        }
    });
});

```

**Penjelasan Detail:**

* `$(document).on('click', ...)`: Ini adalah *best practice* dalam jQuery untuk menangani elemen yang ditambahkan ke DOM setelah halaman dimuat.
* `url: "..."`: Membangun URL secara dinamis dengan menyematkan ID artikel, memastikan data yang dihapus benar-benar sesuai dengan baris yang diklik.
* `success`: Bagian ini menangkap objek `response` yang dikirim dari `Controller`. Kita mengecek *key* `status` untuk memastikan apakah penghapusan benar-benar sukses di *database*.
* `loadData()`: Ini adalah fungsi *helper* yang biasanya berisi kueri AJAX `GET` untuk mengambil data terbaru dan mengisi ulang *tabel*, menciptakan efek "instan".

#### 2. Pembahasan Kodingan: Sisi Backend (Controller)

*Controller* berperan sebagai *gateway* yang menerima permintaan AJAX, berinteraksi dengan model, dan memberikan respons kembali ke *client*.

```php
public function delete($id) {
    // 1. Inisialisasi Model untuk operasi database
    $artikel = new ArtikelModel();
    
    // 2. Eksekusi perintah delete berdasarkan ID yang diterima dari parameter
    $deleted = $artikel->delete($id);

    // 3. Mengirimkan respons balik dalam format JSON
    // Menggunakan response->setJSON untuk memastikan header 'Content-Type: application/json'
    return $this->response->setJSON([
        'status'  => $deleted ? 'success' : 'error',
        'message' => $deleted ? 'Data artikel berhasil dihapus dari sistem' : 'Terjadi kesalahan saat menghapus data'
    ]);
}

```

**Penjelasan Detail:**

* `$this->response->setJSON(...)`: Fungsi ini sangat vital karena mengubah struktur *array* PHP menjadi objek JSON standar. AJAX di sisi *client* tidak akan bisa membaca respons jika tidak dikirimkan dalam format ini.
* `delete($id)`: *Query Builder* CI4 melakukan *binding* parameter secara otomatis, yang memberikan perlindungan penuh terhadap upaya *SQL Injection*.
* **Logika Respons**: Kita menggunakan *ternary operator* untuk memberikan feedback yang dinamis berdasarkan keberhasilan operasi di *model*.

#### 3. Dokumentasi dan Bukti Praktikum

1. **Frontend Setup**: Menyiapkan elemen tabel dengan ID unik agar bisa dimanipulasi dengan `.html()` atau `.append()` dari jQuery.
2. **Library Inclusion**: Pastikan *script* jQuery telah dipanggil di *layout* (`header` atau `footer`) agar fungsi `$.ajax()` dikenali oleh *browser*.
3. **Penyelarasan**: Menyesuaikan *route* pada `Config/Routes.php` agar *method* `delete` dapat diakses dengan metode `DELETE` atau `POST`.

#### 4. Analisis Konsep Utama

* **Responsivitas**: Pengguna tidak merasakan "jedah" (layar putih/loading) saat penghapusan data, karena browser tetap aktif memproses permintaan di latar belakang.
* **Error Handling**: Sangat penting untuk selalu menyertakan blok `error` dalam fungsi `$.ajax()` untuk keperluan *debugging* dan memberikan *feedback* nyata kepada pengguna jika terjadi kendala pada jaringan atau server.
* **DOM Manipulation**: Kemampuan untuk mengupdate hanya *satu baris* (TR) atau *satu tabel* (TABLE) tanpa harus memuat ulang logo, *header*, *sidebar*, dan *footer* secara berulang kali.

Demikian pembahasan detail mengenai implementasi sistem AJAX pada CodeIgniter 4 yang mencakup alur logika *frontend* hingga *backend*. Apakah Anda memerlukan pembahasan tambahan mengenai bagaimana cara melakukan *pagination* data menggunakan AJAX agar tabel tidak perlu dimuat ulang?


# Laporan Praktikum 9: Implementasi AJAX pada CodeIgniter 4

Implementasi *Asynchronous JavaScript and XML* (AJAX) dalam ekosistem CodeIgniter 4 merupakan lompatan besar dalam transformasi antarmuka aplikasi dari statis menjadi dinamis. Secara fundamental, AJAX memutus ketergantungan *browser* terhadap *full-page reload* setiap kali terjadi pembaruan data atau penghapusan entitas. Dalam konteks aplikasi manajemen artikel, hal ini berarti setiap tindakan seperti penghapusan entitas atau pembaruan status dapat dilakukan di latar belakang (*background*) tanpa mengganggu aktivitas pengguna. Dengan berkomunikasi langsung melalui *XMLHttpRequest* atau *Fetch API*, aplikasi dapat menerima respons dari *server* dalam format JSON dan memanipulasi elemen DOM (seperti baris pada tabel) secara instan, sehingga aplikasi terasa jauh lebih modern dan responsif.

Secara teknis, kekuatan AJAX terletak pada kemampuannya menjaga integritas sesi pengguna sambil memberikan *feedback* visual yang instan. Ketika pengguna menekan tombol hapus, *script* JavaScript akan menangkap *event* tersebut, mengirimkan HTTP Request (seperti metode `DELETE`) ke rute yang telah ditentukan, dan menunggu respons dari *server*. Keberhasilan integrasi ini sangat bergantung pada sinkronisasi yang presisi antara logika *routing* di sisi *backend* (CodeIgniter) dan penanganan *callback* di sisi *frontend* (jQuery). Dokumentasi yang mendalam mengenai alur ini sangat penting agar kita tidak hanya paham cara kerja kodingannya, tetapi juga bagaimana setiap potongan kode memberikan kontribusi pada responsivitas aplikasi secara keseluruhan.

* **Event Handling**: Teknik penanganan aksi pengguna melalui *event listener* yang terikat pada elemen DOM.
* **Request Lifecycle**: Alur dari pengiriman HTTP Request, pemrosesan di *Controller*, hingga pengembalian respons (biasanya `JSON`).
* **DOM Manipulation**: Teknik memperbarui konten tabel secara spesifik setelah server mengonfirmasi keberhasilan operasi data tanpa memuat ulang seluruh halaman.
* **JSON Exchange**: Format data standar yang digunakan untuk pertukaran informasi ringan dan cepat antara *client* dan *server*.

#### 1. Pembahasan Kodingan: Sisi Frontend (jQuery)

Kodingan ini adalah otak dari interaksi *client-side*. Kita menggunakan *event delegation* agar tombol yang dihasilkan secara dinamis oleh AJAX tetap tertangani dengan baik oleh sistem.

```javascript
$(document).ready(function() {
    // Event delegation: Mengikat event click pada elemen yang memiliki class .btn-delete
    // Penggunaan $(document) memastikan elemen yang baru dimuat via AJAX tetap terpantau
    $(document).on('click', '.btn-delete', function(e) {
        e.preventDefault(); // Mencegah link melakukan navigasi ke URL default
        
        // Mengambil ID artikel dari atribut data-id pada elemen yang diklik
        var id = $(this).data('id'); 

        if (confirm('Apakah Anda yakin ingin menghapus artikel ini?')) {
            // Melakukan request AJAX ke server
            $.ajax({
                url: "<?= base_url('artikel/delete/'); ?>" + id, // URL menuju method delete di Controller
                method: "DELETE", // Metode HTTP DELETE untuk operasi penghapusan
                dataType: "json", // Memberitahu server bahwa kita mengharapkan format JSON
                
                // Fungsi sukses: Dijalankan jika server memberikan respons 200 OK
                success: function(response) {
                    if(response.status == 'success') {
                        alert(response.message); // Notifikasi sukses dari server
                        loadData(); // Fungsi krusial: Memuat ulang isi tabel tanpa reload halaman
                    } else {
                        alert('Gagal menghapus: ' + response.message);
                    }
                },
                
                // Fungsi error: Dijalankan jika server down, timeout, atau terjadi error 500
                error: function (jqXHR, textStatus, errorThrown) {
                    console.error("AJAX Error details:", textStatus, errorThrown);
                    alert('Terjadi kesalahan pada server. Pastikan koneksi stabil.');
                }
            });
        }
    });
});

```

**Penjelasan Detail:**

* `$(document).on('click', ...)`: Ini adalah *best practice* dalam jQuery untuk menangani elemen yang ditambahkan ke DOM setelah halaman dimuat.
* `url: "..."`: Membangun URL secara dinamis dengan menyematkan ID artikel, memastikan data yang dihapus benar-benar sesuai dengan baris yang diklik.
* `success`: Bagian ini menangkap objek `response` yang dikirim dari `Controller`. Kita mengecek *key* `status` untuk memastikan apakah penghapusan benar-benar sukses di *database*.
* `loadData()`: Ini adalah fungsi *helper* yang biasanya berisi kueri AJAX `GET` untuk mengambil data terbaru dan mengisi ulang *tabel*, menciptakan efek "instan".

#### 2. Pembahasan Kodingan: Sisi Backend (Controller)

*Controller* berperan sebagai *gateway* yang menerima permintaan AJAX, berinteraksi dengan model, dan memberikan respons kembali ke *client*.

```php
public function delete($id) {
    // 1. Inisialisasi Model untuk operasi database
    $artikel = new ArtikelModel();
    
    // 2. Eksekusi perintah delete berdasarkan ID yang diterima dari parameter
    $deleted = $artikel->delete($id);

    // 3. Mengirimkan respons balik dalam format JSON
    // Menggunakan response->setJSON untuk memastikan header 'Content-Type: application/json'
    return $this->response->setJSON([
        'status'  => $deleted ? 'success' : 'error',
        'message' => $deleted ? 'Data artikel berhasil dihapus dari sistem' : 'Terjadi kesalahan saat menghapus data'
    ]);
}

```

**Penjelasan Detail:**

* `$this->response->setJSON(...)`: Fungsi ini sangat vital karena mengubah struktur *array* PHP menjadi objek JSON standar. AJAX di sisi *client* tidak akan bisa membaca respons jika tidak dikirimkan dalam format ini.
* `delete($id)`: *Query Builder* CI4 melakukan *binding* parameter secara otomatis, yang memberikan perlindungan penuh terhadap upaya *SQL Injection*.
* **Logika Respons**: Kita menggunakan *ternary operator* untuk memberikan feedback yang dinamis berdasarkan keberhasilan operasi di *model*.

#### 3. Dokumentasi dan Bukti Praktikum

1. **Frontend Setup**: Menyiapkan elemen tabel dengan ID unik agar bisa dimanipulasi dengan `.html()` atau `.append()` dari jQuery.
2. **Library Inclusion**: Pastikan *script* jQuery telah dipanggil di *layout* (`header` atau `footer`) agar fungsi `$.ajax()` dikenali oleh *browser*.
3. **Penyelarasan**: Menyesuaikan *route* pada `Config/Routes.php` agar *method* `delete` dapat diakses dengan metode `DELETE` atau `POST`.

#### 4. Analisis Konsep Utama

* **Responsivitas**: Pengguna tidak merasakan "jedah" (layar putih/loading) saat penghapusan data, karena browser tetap aktif memproses permintaan di latar belakang.
* **Error Handling**: Sangat penting untuk selalu menyertakan blok `error` dalam fungsi `$.ajax()` untuk keperluan *debugging* dan memberikan *feedback* nyata kepada pengguna jika terjadi kendala pada jaringan atau server.
* **DOM Manipulation**: Kemampuan untuk mengupdate hanya *satu baris* (TR) atau *satu tabel* (TABLE) tanpa harus memuat ulang logo, *header*, *sidebar*, dan *footer* secara berulang kali.

---

**Catatan**: Jika Anda memerlukan detail lebih lanjut mengenai kodingan *pagination* dengan AJAX (Praktikum 9) atau ingin membedah bagian spesifik lainnya dari *controller* atau *view*, silakan beri tahu saya agar saya bisa menyajikannya secara mendetail kembali untuk Anda.

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

