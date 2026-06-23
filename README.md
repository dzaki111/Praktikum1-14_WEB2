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

Berikut adalah penyusunan laporan praktikum **Praktikum 10: REST API dengan CodeIgniter 4** yang disusun secara komprehensif, teknis, dan sangat mendalam pada bagian analisis kodingan.

---

# Laporan Praktikum 10: Implementasi REST API dengan CodeIgniter 4

Implementasi *Representational State Transfer* (REST) API dalam ekosistem CodeIgniter 4 merupakan langkah strategis dalam memisahkan arsitektur *backend* dan *frontend*. Dengan membangun API, sistem dapat menyediakan data secara mandiri yang tidak terikat pada satu *view* spesifik, melainkan bisa diakses oleh berbagai platform seperti aplikasi *mobile*, *framework* JavaScript (VueJS/React), atau integrasi pihak ketiga. Arsitektur ini menggunakan protokol HTTP standar untuk menjalankan operasi CRUD (*Create, Read, Update, Delete*), di mana setiap interaksi data dilakukan melalui pertukaran objek JSON yang terstruktur, ringan, dan sangat efisien untuk konsumsi data antar aplikasi.

Selain aspek interoperabilitas, penggunaan REST API memberikan lapisan keamanan dan abstraksi yang lebih baik terhadap *database*. REST Server bertindak sebagai gerbang tunggal yang memvalidasi setiap permintaan dari *client* berdasarkan metode HTTP yang digunakan. Dengan menerapkan aturan akses yang ketat, kita dapat memastikan bahwa *resource* di *server* hanya diakses oleh pengguna atau aplikasi yang memiliki otorisasi. Penguasaan teknik ini tidak hanya meningkatkan kemampuan pengembang dalam membangun sistem *back-end* yang tangguh, tetapi juga menjadi fondasi utama dalam menciptakan aplikasi modern yang bersifat *decoupled* atau terpisah antara logika bisnis dan antarmuka pengguna.

* **REST Server**: Bertindak sebagai penyedia data/sumber daya yang melayani permintaan dari berbagai platform *client*.
* **HTTP Methods**: Penggunaan metode standar untuk tindakan spesifik: `GET` (ambil data), `POST` (tambah data), `PUT` (perbarui data), dan `DELETE` (hapus data).
* **JSON (JavaScript Object Notation)**: Format standar pertukaran data yang ringan dan sangat mudah diurai oleh mesin, menggantikan dominasi XML dalam layanan web modern.
* **API Endpoints**: Rute akses spesifik yang harus didefinisikan agar klien dapat berinteraksi dengan *resource* database tertentu.

#### 1. Pembahasan Kodingan: Konfigurasi Endpoint (Routes)

Langkah awal dalam membangun API adalah mendefinisikan *routing* agar sistem tahu bagaimana menangani *request* berdasarkan metode HTTP.

```php
// app/Config/Routes.php

// Mendefinisikan grup route untuk resource artikel
$routes->group('post', function($routes) {
    $routes->get('/', 'Artikel::indexApi');         // Mengambil semua data
    $routes->get('(:num)', 'Artikel::showApi/$1');   // Mengambil data spesifik
    $routes->post('/', 'Artikel::createApi');        // Menambah data baru
    $routes->put('(:num)', 'Artikel::updateApi/$1'); // Memperbarui data
    $routes->delete('(:num)', 'Artikel::deleteApi/$1'); // Menghapus data
});

```

**Penjelasan Detail:**

* `group('post', ...)`: Membungkus semua endpoint API di bawah rute `post` agar lebih rapi dan mudah dikelola.
* `(:num)`: *Placeholder* yang secara otomatis memvalidasi bahwa parameter yang dikirim harus berupa angka (ID artikel).
* **HTTP Methods Mapping**: Kita memetakan metode HTTP ke method di `Artikel` *controller*. Ini memastikan bahwa klien tidak bisa menghapus data jika mereka mengirimkan *request* menggunakan `GET`.

#### 2. Pembahasan Kodingan: Implementasi Controller API

*Controller* ini berfungsi sebagai pemroses utama yang menerima *request* dari *client*, berinteraksi dengan database melalui Model, dan mengirimkan respons JSON.

```php
// app/Controllers/Artikel.php

public function deleteApi($id = null) {
    $model = new ArtikelModel();
    
    // 1. Mencari data berdasarkan ID
    $data = $model->find($id);
    
    // 2. Mengecek apakah data ditemukan
    if ($data) {
        $model->delete($id);
        
        // 3. Memberikan respons sukses
        return $this->response->setJSON([
            'status' => 200,
            'error' => null,
            'messages' => [
                'success' => 'Data artikel berhasil dihapus.'
            ]
        ]);
    } else {
        // 4. Memberikan respons error jika ID tidak ditemukan
        return $this->response->setJSON([
            'status' => 404,
            'error' => 'Data tidak ditemukan',
            'messages' => null
        ]);
    }
}

```

**Penjelasan Detail:**

* `$model->find($id)`: Digunakan untuk memvalidasi keberadaan data. Kita tidak bisa menghapus data yang tidak ada, sehingga pengecekan ini wajib dilakukan.
* `$this->response->setJSON(...)`: Inilah inti dari API. Alih-alih merender *view* HTML, kita mengirimkan struktur data JSON yang mencakup status HTTP (200/404) dan pesan. Klien (seperti VueJS) akan membaca status ini untuk menentukan apakah mereka perlu menampilkan notifikasi sukses atau pesan error kepada pengguna.
* **Error Handling**: Memberikan status `404` saat data tidak ditemukan merupakan standar dalam RESTful API untuk membantu pengembang di sisi klien melakukan *debugging*.

#### 3. Pengujian dengan Postman

Pengujian adalah tahap krusial untuk memastikan API bekerja sesuai ekspektasi tanpa melibatkan antarmuka *browser*.

* **GET Request**: Menguji `http://localhost:8080/post`. *Response* akan berupa *array* artikel.
* **POST Request**: Mengirim data melalui *Body* (tipe *form-data* atau *json*). Pastikan *controller* menggunakan `$this->request->getPost()` atau `$this->request->getJSON()` untuk menangkap data tersebut.
* **DELETE Request**: Mengirimkan request ke `http://localhost:8080/post/4`. Postman akan menerima *response* sukses jika `status` yang dikembalikan `200`.

#### 4. Analisis Konsep Utama

* **Keamanan API**: API sangat rentan. Oleh karena itu, kita harus menerapkan *authentication* (seperti JWT atau API Key) di tahap lanjut agar tidak sembarang pihak bisa mengakses endpoint `delete` atau `post`.
* **JSON vs XML**: JSON dipilih karena sifatnya yang *human-readable* dan *lightweight*. Untuk aplikasi *mobile* dan *web* modern, JSON secara drastis mengurangi *payload* data yang ditransfer.
* **REST Principle**: Dengan menerapkan *stateless* (server tidak menyimpan sesi klien), API menjadi lebih mudah untuk di-*scale* (dikembangkan) ke ribuan klien secara bersamaan.

Demikian pembahasan detail mengenai implementasi REST API dengan CodeIgniter 4. Apakah Anda membutuhkan pembahasan lebih lanjut mengenai cara menambahkan *token* otentikasi agar API Anda lebih aman dari akses ilegal?



# Laporan Praktikum 11: Membangun Antarmuka Frontend Dinamis dengan VueJS 3

Implementasi *framework* VueJS 3 dalam pengembangan antarmuka web modern merupakan langkah krusial untuk menciptakan pengalaman pengguna yang jauh lebih interaktif dan responsif dibandingkan dengan metode pengembangan tradisional. Sebagai *framework* yang berbasis pada arsitektur komponen, VueJS memungkinkan pengembang untuk memecah antarmuka yang kompleks menjadi bagian-bagian kecil yang mandiri, sehingga pengelolaan kode menjadi jauh lebih terstruktur, mudah diuji, dan dapat digunakan kembali (*reusable*). Dengan fitur *reactive data binding*, setiap perubahan data di sisi *logic* akan secara otomatis tercermin pada tampilan tanpa memerlukan manipulasi DOM manual yang rumit, menciptakan aplikasi yang terasa sangat hidup dan cepat.

Di sisi lain, integrasi VueJS dengan REST API yang telah dibangun sebelumnya mengubah paradigma pengembangan aplikasi dari *server-side rendering* menjadi *client-side rendering* yang efisien. Aplikasi kini berperan sebagai *client* yang secara mandiri melakukan *request* data melalui protokol HTTP, memproses respons JSON, dan menyajikannya secara dinamis kepada pengguna. Penguasaan terhadap siklus hidup (*lifecycle hooks*) dalam VueJS, seperti `onMounted()`, menjadi sangat penting agar data dapat disinkronkan secara mulus segera setelah komponen dimuat. Integrasi ini tidak hanya meningkatkan estetika dan responsivitas aplikasi, tetapi juga memperkuat fundamental pengembang dalam membangun sistem *Single Page Application* (SPA) yang modern dan profesional.

* **Reactive Data Binding**: Mekanisme otomatis di mana perubahan pada *state* data JavaScript akan langsung memperbarui elemen HTML yang terhubung, menjaga konsistensi antara data dan tampilan.
* **Component-Based Architecture**: Strategi pengembangan dengan membangun UI sebagai kumpulan komponen modular, yang meningkatkan *maintainability* dan efisiensi kode.
* **Lifecycle Hooks**: Fungsi khusus seperti `onMounted()` yang dieksekusi secara otomatis pada fase tertentu dari siklus hidup komponen, sangat krusial untuk pemanggilan API saat aplikasi dimuat.
* **Single Page Application (SPA)**: Pola pengembangan web di mana aplikasi hanya memuat satu halaman HTML, dan perpindahan konten dilakukan melalui injeksi data dinamis tanpa perlu melakukan *reload* browser.

#### 1. Pembahasan Kodingan: Inisialisasi State dan Komunikasi API

Pada bagian ini, kita akan menggunakan *Composition API* untuk mengelola data artikel yang diambil dari server.

```javascript
import { ref, onMounted } from 'vue';
import axios from 'axios';

export default {
    setup() {
        // 'ref' membuat variabel artikel menjadi reaktif
        const artikel = ref([]);

        // Fungsi untuk mengambil data dari API
        const fetchArtikel = async () => {
            try {
                const response = await axios.get('http://localhost:8080/post');
                // Mengupdate data reaktif dengan hasil response
                artikel.value = response.data;
            } catch (error) {
                console.error("Gagal mengambil data:", error);
            }
        };

        // Hook yang dijalankan saat komponen pertama kali muncul di browser
        onMounted(fetchArtikel);

        return { artikel };
    }
}

```

**Penjelasan Detail:**

* `ref([])`: Variabel `artikel` didefinisikan sebagai *reactive reference*. Dalam Vue 3, setiap perubahan pada `artikel.value` akan memicu *re-render* otomatis pada komponen yang menggunakannya.
* `axios.get(...)`: Menggunakan pustaka *Axios* untuk mengirimkan HTTP Request secara asinkron ke endpoint API yang telah dibuat di praktikum sebelumnya.
* `onMounted()`: Ini adalah kunci dari sinkronisasi data; fungsi `fetchArtikel` dipanggil tepat setelah komponen selesai dimuat, sehingga pengguna tidak perlu menekan tombol apa pun untuk melihat data pertama kali.

#### 2. Pembahasan Kodingan: Rendering Data Dinamis

Setelah data tersimpan dalam *state*, kita menampilkannya ke dalam *template* HTML secara reaktif.

```html
<template>
  <div class="artikel-container">
    <h2>Daftar Artikel</h2>
    <table>
      <thead>
        <tr>
          <th>Judul</th>
          <th>Aksi</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="item in artikel" :key="item.id">
          <td>{{ item.judul }}</td>
          <td>
            <button @click="hapusArtikel(item.id)">Hapus</button>
          </td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

```

**Penjelasan Detail:**

* `v-for="item in artikel"`: Ini adalah direktif *looping* Vue. Jika array `artikel` bertambah atau berkurang, tabel akan memperbarui dirinya sendiri secara otomatis tanpa instruksi `append` atau `remove` dari JavaScript.
* `{{ item.judul }}`: Sintaks *interpolation* untuk menampilkan data langsung dari *state* ke tampilan.
* `@click`: *Event listener* bawaan Vue untuk memicu fungsi `hapusArtikel` ketika tombol ditekan.

#### 3. Pembahasan Kodingan: Operasi Hapus Data

Berikut adalah logika untuk menghapus data secara asinkron dari sisi *client* dan memperbarui tampilan secara *real-time*.

```javascript
const hapusArtikel = async (id) => {
    if (confirm('Yakin ingin menghapus?')) {
        await axios.delete(`http://localhost:8080/post/${id}`);
        // Setelah berhasil di server, filter data di client untuk memperbarui UI
        artikel.value = artikel.value.filter(item => item.id !== id);
    }
};

```

**Penjelasan Detail:**

* `axios.delete(...)`: Mengirimkan perintah penghapusan ke *backend* API.
* `filter(...)`: Ini adalah teknik manipulasi *state* yang efisien. Alih-alih melakukan *reload* halaman, kita cukup menghapus elemen yang bersangkutan dari *array* `artikel` di memori klien, maka Vue akan secara otomatis menghilangkan baris tersebut dari tabel di tampilan layar.

#### 4. Analisis Konsep Utama

* **Responsivitas**: Aplikasi terasa sangat cepat karena tidak ada *full-page reload*. Seluruh operasi CRUD dilakukan di latar belakang menggunakan AJAX.
* **Component Architecture**: Dengan pola ini, jika nanti Anda butuh fitur pencarian, Anda cukup membuat komponen baru tanpa mengganggu komponen daftar artikel yang sudah ada.
* **Debugging**: Penting untuk selalu memantau *Tab Network* di *Developer Tools* browser untuk melihat apakah *request* API berhasil (status 200) atau gagal (status 404/500).

Apakah pembahasan detail ini sudah memenuhi kebutuhan laporan Anda, atau ada bagian *state management* atau *routing* (Vue Router) yang perlu dibedah lebih dalam?

# Laporan Praktikum 12: Implementasi Vue Components dan Client-Side Routing (SPA)

Transformasi aplikasi web menuju arsitektur *Single Page Application* (SPA) merupakan langkah esensial dalam pengembangan antarmuka modern yang berfokus pada kecepatan dan kenyamanan pengguna. Dengan menggunakan Vue Components, kita dapat mentransformasi struktur kode yang tadinya monolitik menjadi kumpulan bagian-bagian UI yang bersifat modular dan terisolasi. Pendekatan ini secara drastis meningkatkan efisiensi pengembangan, karena setiap komponen seperti *header*, *footer*, atau daftar artikel dapat dikelola secara mandiri, diuji secara terpisah, dan digunakan kembali (*reusable*) di berbagai bagian aplikasi tanpa duplikasi kode yang tidak perlu.

Selain modularitas, integrasi Vue Router menjadi tulang punggung dari arsitektur SPA. Jika pada aplikasi web tradisional setiap perpindahan menu mengharuskan *browser* untuk memuat ulang seluruh halaman dari *server*, *Client-Side Routing* memungkinkan transisi antar halaman terjadi secara instan melalui manipulasi DOM yang cerdas. Dengan memetakan URL tertentu ke komponen spesifik, aplikasi mampu memberikan pengalaman navigasi yang mulus layaknya aplikasi *mobile*. Penguasaan teknik ini tidak hanya meningkatkan responsivitas aplikasi, tetapi juga mencerminkan standar profesional dalam membangun sistem antarmuka web yang skalabel dan efisien.

* **Vue Components**: Unit UI modular (seperti `Home.js`, `Artikel.js`, `About.js`) yang memungkinkan isolasi logika dan tampilan sehingga aplikasi lebih mudah dikelola.
* **Client-Side Routing**: Mekanisme navigasi yang dikelola sepenuhnya oleh *client* (browser) menggunakan `Vue Router`, sehingga meniadakan kebutuhan untuk melakukan *hard-reload* saat berpindah halaman.
* **Single Page Application (SPA)**: Pola arsitektur di mana aplikasi hanya memuat satu file HTML utama dan melakukan pembaruan konten secara dinamis melalui injeksi komponen.
* **`router-view` dan `router-link**`: Elemen inti Vue Router; `router-view` bertindak sebagai *placeholder* konten dinamis, sementara `router-link` berfungsi sebagai navigator yang mencegah aksi *refresh* default browser.

#### 1. Pembahasan Kodingan: Konfigurasi Vue Router

Langkah paling kritikal dalam membangun SPA adalah mendefinisikan rute. Kita menggunakan *Array of Routes* untuk memetakan jalur URL ke komponen yang sesuai.

```javascript
// Konfigurasi Router (app.js)
const routes = [
    { path: '/', component: Home },
    { path: '/artikel', component: Artikel },
    { path: '/about', component: About }
];

const router = VueRouter.createRouter({
    history: VueRouter.createWebHistory(),
    routes, // Inisialisasi rute yang telah didefinisikan
});

const app = Vue.createApp({});
app.use(router); // Mengintegrasikan router ke dalam instance aplikasi Vue
app.mount('#app');

```

**Penjelasan Detail:**

* `createWebHistory()`: Memungkinkan aplikasi menggunakan format URL modern (tanpa tanda pagar `#`) yang lebih SEO-friendly.
* `app.use(router)`: Fungsi ini mendaftarkan *plugin* router ke dalam aplikasi utama, sehingga komponen di seluruh aplikasi dapat mengakses fitur navigasi melalui `this.$router` atau *hook* navigasi.
* `routes`: Objek ini adalah otak navigasi. Setiap kali URL berubah, `Vue Router` secara otomatis mencari *path* yang cocok dan merender komponen yang tertaut ke dalam elemen `<router-view />`.

#### 2. Pembahasan Kodingan: Struktur Komponen Modular

Kita memisahkan kode menjadi file komponen agar tidak terjadi penumpukan kode pada satu file saja.

```javascript
// Contoh Komponen Artikel (components/Artikel.js)
const Artikel = {
    template: `
        <div class="artikel-container">
            <h2>Daftar Artikel</h2>
            <div v-for="item in artikelList" :key="item.id" class="card">
                <h3>{{ item.judul }}</h3>
                <p>{{ item.isi }}</p>
            </div>
        </div>
    `,
    data() {
        return {
            artikelList: [] // State lokal untuk menyimpan data
        };
    },
    mounted() {
        // Melakukan fetch API saat komponen dipasang
        fetch('http://localhost:8080/post')
            .then(res => res.json())
            .then(data => { this.artikelList = data; });
    }
};

```

**Penjelasan Detail:**

* `template`: Mendefinisikan struktur HTML dari komponen. Penggunaan *template literal* memungkinkan penulisan HTML yang bersih.
* `data()`: Setiap komponen memiliki *state* sendiri. Ini menjamin bahwa data artikel hanya akan ada di komponen `Artikel` dan tidak akan membocorkan data ke komponen lain (seperti `About`), menjaga enkapsulasi.
* `mounted()`: Ini adalah *lifecycle hook* yang memastikan data artikel baru diambil ketika komponen benar-benar telah dirender ke layar, mencegah *error* saat DOM belum siap.

#### 3. Pembahasan Kodingan: Implementasi Navigasi

Mengganti tag `<a>` konvensional dengan `router-link` adalah syarat wajib untuk membuat aplikasi tetap berada dalam mode SPA.

```html
<nav>
    <router-link to="/">Beranda</router-link>
    <router-link to="/artikel">Kelola Artikel</router-link>
    <router-link to="/about">Tentang Kami</router-link>
</nav>

<router-view></router-view>

```

**Penjelasan Detail:**

* `<router-link to="...">`: Pustaka Vue Router akan mencegat klik pada elemen ini dan melakukan perubahan URL secara *asynchronous* tanpa mengirim permintaan ke server.
* `<router-view />`: Ini adalah "wadah ajaib" yang akan terisi secara otomatis oleh konten komponen (Home/Artikel/About) berdasarkan rute yang sedang aktif. Ini mencegah halaman melakukan *re-render* total karena hanya konten di dalam tag ini yang akan diganti.

#### 4. Analisis Konsep Utama

* **SPA Performance**: Karena hanya konten di dalam `<router-view />` yang diperbarui, *header* dan *navigasi* tidak ikut dimuat ulang, memberikan pengalaman transisi halaman yang instan.
* **Component Reusability**: Jika suatu saat kita ingin menampilkan daftar artikel di halaman Beranda juga, kita tinggal memanggil komponen `<Artikel />` kembali tanpa harus menulis ulang logika *fetch* data.
* **State Management**: Dengan memisahkan komponen, kita menjaga *state* aplikasi tetap terorganisir. *Bug* pada halaman `About` tidak akan mempengaruhi logika data pada halaman `Artikel`.

Demikian pembahasan detail mengenai implementasi Vue Components dan Routing. Apakah Anda membutuhkan pembahasan lebih lanjut mengenai *Navigation Guards* (untuk proteksi rute) atau bagaimana menangani *State Management* (Pinia/Vuex) pada aplikasi SPA Anda?


# Laporan Praktikum 13: Implementasi Keamanan Autentikasi dan Navigation Guards pada SPA

Dalam pengembangan aplikasi *Single Page Application* (SPA), tantangan keamanan berpindah dari ranah server-side ke ranah client-side. Karena seluruh struktur aplikasi dimuat ke dalam *browser* sejak awal, proteksi halaman tidak bisa hanya mengandalkan *routing* standar. Di sinilah peran *Navigation Guards* (khususnya `router.beforeEach`) menjadi sangat krusial; fitur ini bertindak sebagai mekanisme penyaring atau "penjaga pintu" yang secara otomatis melakukan verifikasi status otentikasi pengguna sebelum rute baru dirender oleh *browser*. Dengan metode ini, halaman-halaman sensitif yang memerlukan hak akses administratif dapat terlindungi dengan efektif dari akses yang tidak sah, menciptakan lapisan keamanan pertama yang tangguh bagi pengalaman pengguna.

Selain mekanisme di sisi klien, integritas aplikasi harus diperkuat dengan integrasi *backend* yang andal. Proses login bukan sekadar perpindahan tampilan, melainkan sebuah siklus hidup di mana *frontend* mengirimkan kredensial melalui *Axios HTTP Post* ke *API Endpoint* di CodeIgniter 4, yang kemudian akan melakukan validasi data terhadap *database*. Keberhasilan proses ini akan menghasilkan *token* atau *session* yang disimpan di *localStorage*, yang nantinya akan dibaca oleh `router.beforeEach` untuk menentukan apakah pengguna layak mengakses suatu rute. Pendekatan ini memastikan bahwa setiap interaksi pengguna di dalam aplikasi terikat pada status otentikasi yang valid, menciptakan ekosistem aplikasi yang aman, terintegrasi, dan profesional.

* **Navigation Guards (`router.beforeEach`)**: Mekanisme global yang menginterupsi proses navigasi rute untuk melakukan pengecekan otorisasi sebelum komponen halaman di-render.
* **Client-Side Security**: Lapisan keamanan yang diterapkan pada sisi *browser* untuk mencegah pengguna yang tidak terautentikasi mengakses komponen aplikasi yang bersifat privat.
* **Axios HTTP Post**: Fungsi vital untuk mengirimkan data sensitif (kredensial pengguna) dari *frontend* ke *backend* untuk diproses secara aman.
* **State Persistence**: Penggunaan `localStorage` atau *state management* untuk menyimpan status *login* agar pengguna tetap terautentikasi meskipun halaman di-*refresh*.

#### 1. Pembahasan Kodingan: Implementasi Navigation Guards

Ini adalah otak dari keamanan SPA kita. Kode ini diletakkan di file konfigurasi router untuk memproteksi halaman dari akses tanpa izin.

```javascript
// Konfigurasi Router (router/index.js)
router.beforeEach((to, from, next) => {
    // 1. Cek apakah rute tujuan memerlukan autentikasi
    const requiresAuth = to.matched.some(record => record.meta.requiresAuth);
    
    // 2. Ambil status login dari localStorage
    const isAuthenticated = localStorage.getItem('isLoggedIn');

    if (requiresAuth && !isAuthenticated) {
        // Jika rute butuh login tapi belum login, lempar ke halaman login
        next('/login');
    } else if (to.path === '/login' && isAuthenticated) {
        // Jika sudah login tapi mencoba akses halaman login, lempar ke dashboard
        next('/admin/artikel');
    } else {
        // Izinkan navigasi dilanjutkan
        next();
    }
});

```

**Penjelasan Detail:**

* `to.matched.some(...)`: Kode ini memeriksa setiap tingkatan rute yang dituju untuk mencari *metadata* `requiresAuth`. Jika ditemukan, maka rute tersebut diklasifikasikan sebagai halaman privat.
* `next()`: Ini adalah fungsi *callback* wajib dalam Vue Router. `next()` yang dipanggil tanpa argumen akan mengizinkan navigasi, sementara `next('/login')` akan membatalkan navigasi saat ini dan mengalihkan pengguna ke rute lain.
* **Keamanan**: Dengan pengecekan ini, pengguna tidak akan pernah sempat melihat konten halaman privat karena proses pengecekan dilakukan *sebelum* komponen halaman tersebut di-*mount* ke dalam DOM.

#### 2. Pembahasan Kodingan: Fungsi Login dengan Axios

Bagian ini menangani pengiriman data dari *form* ke *backend* CodeIgniter 4.

```javascript
const handleLogin = async () => {
    try {
        const response = await axios.post('http://localhost:8080/user/login', {
            email: email.value,
            password: password.value
        });

        if (response.data.status === 'success') {
            // Simpan status ke localStorage
            localStorage.setItem('isLoggedIn', 'true');
            // Arahkan ke dashboard admin
            router.push('/admin/artikel');
        } else {
            alert('Login Gagal: ' + response.data.message);
        }
    } catch (error) {
        alert('Terjadi kesalahan pada server.');
    }
};

```

**Penjelasan Detail:**

* `await axios.post(...)`: Kita mengirimkan data `email` dan `password` dalam format JSON ke server. Penggunaan `async/await` di sini sangat penting untuk menangani respons *server* yang bersifat asinkron.
* `localStorage.setItem(...)`: Setelah server memvalidasi kredensial (misalnya melalui *check* di *database*), kita menandai pengguna sebagai "sudah login" secara permanen di memori *browser*.
* `router.push(...)`: Berfungsi untuk mengarahkan pengguna secara otomatis ke halaman admin setelah proses login dinyatakan sukses, memberikan *user experience* yang instan.

#### 3. Pembahasan Kodingan: Proteksi Rute (Meta Fields)

Agar `router.beforeEach` tahu halaman mana yang harus dilindungi, kita harus menambahkan *meta* pada definisi rute.

```javascript
const routes = [
    { 
        path: '/admin/artikel', 
        component: AdminArtikel, 
        meta: { requiresAuth: true } // Menandai rute ini privat
    },
    { 
        path: '/login', 
        component: Login 
    }
];

```

**Penjelasan Detail:**

* `meta: { requiresAuth: true }`: Ini adalah cara memberikan informasi tambahan ke *router*. Objek `meta` ini tidak akan mengubah tampilan, tetapi akan dibaca oleh fungsi `router.beforeEach` untuk memutuskan apakah akses harus diberikan atau ditolak.

#### 4. Analisis Konsep Utama

* **Pentingnya Client-Side Security**: *Navigation Guards* adalah garis pertahanan pertama. Namun, Anda harus ingat bahwa ini adalah *client-side*. Anda wajib memvalidasi setiap token/sesi di *API Backend* setiap kali melakukan aksi CRUD untuk menjamin keamanan data yang sebenarnya.
* **UX yang Mulus**: Dengan mengarahkan pengguna ke halaman login saat akses ditolak, aplikasi terasa seperti sistem yang terintegrasi penuh, bukan kumpulan halaman web yang terpisah.
* **Debugging**: Gunakan `console.log(to)` di dalam `router.beforeEach` untuk melihat detail rute mana yang sedang diakses, terutama jika terjadi kendala rute yang tidak terproteksi dengan benar.



# Laporan Praktikum 14: Keamanan API dan Implementasi Token-Based Authentication

Implementasi *Token-Based Authentication* merupakan standar emas dalam menjaga keamanan pertukaran data antara *frontend* (SPA) dan *backend* (REST API). Pada praktikum ini, kita melangkah lebih jauh dari sekadar pembatasan navigasi di sisi klien, menuju pengamanan *server-side* yang sesungguhnya. Ketika aplikasi kita tumbuh menjadi sistem yang lebih terbuka dan dapat diakses oleh berbagai *client*, proteksi berbasis *router* saja menjadi tidak memadai karena *endpoint* API dapat diakses oleh siapapun melalui *tools* pihak ketiga. Oleh karena itu, penerapan mekanisme validasi berbasis *token* (seperti JWT atau *Bearer Token*) di sisi server menjadi wajib, memastikan bahwa setiap permintaan data yang masuk ke basis data telah melalui proses autentikasi yang valid dan terenkripsi.

Integrasi antara *Axios Interceptors* di sisi *frontend* dan *Filters* di sisi *backend* menciptakan aliran data yang aman dan otomatis. Dengan *Interceptors*, aplikasi kita secara cerdas akan menyisipkan *token* autentikasi ke dalam setiap *header* permintaan HTTP tanpa harus menulis ulang logika tersebut di setiap fungsi pemanggilan API. Di sisi lain, *Filter* pada *backend* bertindak sebagai penjaga gerbang yang memeriksa keabsahan *token* tersebut sebelum memproses *query* ke basis data. Sinergi antara kedua mekanisme ini tidak hanya meningkatkan profil keamanan aplikasi secara drastis, tetapi juga menyederhanakan kode di sisi klien, membuat alur komunikasi antar sistem menjadi lebih stabil, rapi, dan tahan terhadap upaya akses ilegal dari pihak luar.

* **Token-Based Authentication**: Mekanisme di mana server mengeluarkan *token* unik (sebagai bukti identitas) setelah pengguna login, yang wajib disertakan klien dalam setiap permintaan data.
* **Server-Side Filter**: *Middleware* di CodeIgniter 4 yang bertugas memvalidasi keberadaan dan keaslian *token* pada `Authorization Header` setiap kali *request* API masuk.
* **Axios Interceptors**: *Middleware* di sisi *client* yang mencegat permintaan HTTP sebelum keluar dari *browser* untuk menyisipkan *token* secara otomatis tanpa intervensi manual pengembang.
* **Unauthorized Response (401)**: Status HTTP standar yang dikembalikan oleh *server* ketika *token* tidak disertakan atau sudah kedaluwarsa, yang menjadi indikator kegagalan autentikasi.

#### 1. Pembahasan Kodingan: Axios Interceptors (Sisi Frontend)

*Axios Interceptor* adalah alat yang sangat kuat untuk menangani *token* secara global. Alih-alih menambahkan `Authorization header` di setiap fungsi API, kita cukup menulisnya sekali.

```javascript
// src/api/axios.js
import axios from 'axios';

// Menambahkan request interceptor
axios.interceptors.request.use(
    (config) => {
        // Mengambil token yang disimpan di localStorage setelah proses login
        const token = localStorage.getItem('token');
        
        // Jika token ada, tambahkan ke header Authorization
        if (token) {
            config.headers.Authorization = `Bearer ${token}`;
        }
        
        // Mengembalikan konfigurasi yang sudah dimodifikasi
        return config;
    },
    (error) => {
        // Menangani jika terjadi kesalahan pada request
        return Promise.reject(error);
    }
);

```

**Penjelasan Detail:**

* `axios.interceptors.request.use(...)`: Kode ini membungkus setiap *request* yang akan dikirim oleh *Axios*. Sebelum *request* benar-benar meninggalkan browser, fungsi ini berjalan terlebih dahulu.
* `config.headers.Authorization = 'Bearer ' + token`: Inilah inti dari keamanan kita. Kita menyuntikkan *token* ke dalam *header* HTTP. *Backend* nanti akan membaca *header* ini untuk memastikan siapa yang sedang mengakses API tersebut.
* **Otomasi**: Dengan *interceptor*, kode Anda tetap bersih karena tidak perlu lagi menulis `headers: { ... }` di setiap fungsi `axios.get` atau `axios.post`.

#### 2. Pembahasan Kodingan: Server-Side Filter (Sisi Backend)

*Filter* di CodeIgniter 4 bekerja seperti *security guard*. Ia memeriksa *token* sebelum *Controller* manapun diproses.

```php
// app/Filters/AuthFilter.php
namespace App\Filters;

use CodeIgniter\Filters\FilterInterface;
use CodeIgniter\HTTP\RequestInterface;
use CodeIgniter\HTTP\ResponseInterface;
use Config\Services;

class AuthFilter implements FilterInterface {
    public function before(RequestInterface $request, $arguments = null) {
        $header = $request->getHeaderLine('Authorization');
        
        // Memeriksa apakah token Bearer tersedia
        if (!preg_match('/Bearer\s(\S+)/', $header, $matches)) {
            return Services::response()->setJSON([
                'status' => 401,
                'message' => 'Token tidak ditemukan atau tidak valid'
            ])->setStatusCode(401);
        }
        
        // Logika verifikasi token (misalnya memverifikasi JWT) 
        // akan diletakkan di sini sebelum next() dipanggil.
    }

    public function after(RequestInterface $request, ResponseInterface $response, $arguments = null) {}
}

```

**Penjelasan Detail:**

* `getHeaderLine('Authorization')`: *Filter* mengambil data dari *header* yang dikirim oleh *Interceptor* di sisi klien tadi.
* `preg_match('/Bearer\s(\S+)/', ...)`: Ini adalah *Regex* (Regular Expression) untuk memastikan *format* *token* diawali dengan kata "Bearer" diikuti oleh *string token*-nya.
* `setStatusCode(401)`: Jika *token* tidak ditemukan, *server* langsung memutus koneksi dan mengirimkan respons `401 Unauthorized`. Hal ini memastikan kode di dalam *Controller* Anda tidak akan pernah dijalankan jika pengguna tidak sah.

#### 3. Analisis Konsep Utama

* **End-to-End Security**: Keamanan sekarang terbagi dua: (1) **Vue Router** melindungi UI agar pengguna tidak "bingung" melihat halaman admin tanpa login, dan (2) **API Filter** melindungi database agar tidak bisa diakses tanpa *token* yang sah.
* **Perbandingan Perlindungan**: Jika Anda menghapus *Navigation Guards*, aplikasi mungkin tetap aman karena *API Filter* akan menolak *request* data. Sebaliknya, jika Anda menghapus *API Filter*, siapapun bisa mengakses data Anda melalui Postman meskipun halaman admin Anda sudah diproteksi oleh *Navigation Guards*. Keduanya harus ada.
* **Transparansi**: Dengan *Axios Interceptor*, proses autentikasi menjadi transparan bagi *developer* di sisi *frontend* karena mereka bisa memanggil API seolah-olah tidak ada proteksi, padahal sistem sudah secara otomatis menangani pengiriman *token* di balik layar.


