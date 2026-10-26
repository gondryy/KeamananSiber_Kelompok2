# Red Team Attack Plan
Dokumen ini adalah rencana pengujian penetrasi atau serangan dari Attacker Node milik Red Team terhadap sistem Target Node yang sudah dilakukan Hardening oleh Blue Team. Rencana ini dirancang untuk memverifikasi dan mengidentifikasi celah keamanan dari arsitektur sistem IoT.

## 1. Lingkungan dan Target Pengujian Serangan
- **Attacker Node:** `10.10.2.2`-- Kali Linux
- **Target Node:** `10.20.2.2` -- Metasploitable

## 2. Matriks Rencana Skenario Serangan
| ID Skenario | Kategori Serangan        | Layanan / Endpoint Target                         | Tools yang Digunakan |
| :---------- | :----------------------- | :------------------------------------------------ | :------------------- |
| **SCN-01**  | Reconnaissance | Seluruh port Target (`10.20.2.2`)             | `nmap`               |
| **SCN-02** | Web/API/Protokol IoT | http://10.20.2.2:80 (Form/API Web Feeder) | `Burp Suite` atau `OWASP ZAP` |
| **SCN-03**  | Availability / Denial of Service | http://10.20.2.2:80 (Layanan Apache) | `Slowloris`         |

## 3. Rincian Skenario Serangan
### SCN-01: Network Reconnaissance
- **Kategori:** Reconnaissance
- **Tools yang digunakan:** `nmap`
- **Target Spesifik:** `10.20.2.2`
- **Korelasi Celah / Tech Stack:** Pengujian attack surface untuk memverifikasi efektivitas firewall (UFW) yang telah dikonfigurasi oleh Blue Team. Skenario ini bertujuan memastikan *obfuscation* (penyembunyian) banner versi peladen Apache berhasil diterapkan sehingga penyerang tidak bisa dengan mudah mencari exploit yang spesifik untuk versi OS atau peladen web tersebut.
- **Metodologi Pengujian:** 
  1. Menjalankan TCP SYN scan secara menyeluruh pada seluruh 65.535 port (`-p-`) dari mesin Kali Linux (`10.10.2.2`) untuk mendeteksi port yang terbuka atau disembunyikan.
  2. Melakukan service and version enumeration (`-sV`) pada port yang ditemukan terbuka untuk mengidentifikasi aplikasi dan versinya.
  3. Menjalankan Nmap Scripting Engine (NSE) dengan profil vuln (`--script vuln`) pada port yang terbuka untuk mencari kerentanan bawaan secara otomatis.
- **Hasil yang Diharapkan:**
  - Pemindaian Nmap mengonfirmasi bahwa hanya Port 80/tcp (HTTP) yang berstatus `open`.
  - Port 22/tcp (SSH) berstatus `filtered`, membuktikan bahwa UFW berhasil memblokir akses dari segmen IP `10.10.2.0/24`.
  - Deteksi versi pada Port 80 hanya menampilkan "Apache httpd" tanpa menyebutkan versi spesifik (seperti "2.4.52") atau jenis OS ("Ubuntu"). 

### SCN-02: Eksploitasi Kerentanan Aplikasi Web (Web/API/Protokol IoT)
- **ID Skenario:** *SCN-02*
- **Kategori:** Web/API/Protokol IoT
- **Tools yang digunakan:** `Burp Suite` (atau `OWASP ZAP`)
- **Target Spesifik:** `http://10.20.2.2:80` (Aplikasi Web IoT Apache)
- **Korelasi Celah / Tech Stack:** Pengujian ini mengeksploitasi ketiadaan perlindungan Web Application Firewall (WAF) seperti ModSecurity berdasarkan temuan audit Lynis. Skenario ini bertujuan menyuntikkan muatan berbahaya (*payload*) seperti SQL Injection atau memanipulasi parameter antarmuka web (misalnya parameter penjadwalan pakan) untuk melihat apakah peladen memiliki filter validasi input yang memadai di sisi *backend*.
- **Metodologi Pengujian:** 
  1. Penyerang mengonfigurasi `Burp Suite` sebagai *proxy* untuk mencegat (*intercept*) lalu lintas HTTP dari peramban penyerang ke peladen `10.20.2.2`.
  2. Penyerang memanipulasi permintaan HTTP yang sah dengan memasukkan muatan *SQL Injection* (seperti `' OR 1=1--`) pada kolom login, atau memodifikasi parameter API pengontrol alat.
  3. Mengirimkan permintaan yang telah dimodifikasi tersebut dan menganalisis respons dari peladen web.
- **Hasil yang Diharapkan:** 
  - Penyerang berhasil menembus otentikasi tanpa kredensial yang sah atau berhasil mengeksekusi tindakan administratif secara tidak sah.
  - Sensor Security Onion pada *Blue Team* (seperti Snort atau analisis log HTTP dari Zeek) berhasil mendeteksi dan memunculkan peringatan (*alert*) terkait adanya lalu lintas yang mengandung anomali injeksi SQL atau muatan HTTP berbahaya.

### 3. SCN-03: Application Layer Denial of Service
- **Kategori:** Availability / Denial of Service
- **Tools yang digunakan:** `Slowloris`
- **Target Spesifik:** `http://10.20.2.2:80` (Layanan Apache)
- **Korelasi Celah / Tech Stack:** Skenario ini menguji temuan audit Lynis di mana modul `mod_evasive` (Anti-DoS) tidak terpasang. Apache secara bawaan menangani koneksi secara sinkron dengan jumlah thread atau proses worker yang terbatas. Serangan Layer 7 mengeksploitasi mekanisme ini tanpa memerlukan bandwidth besar.
- **Metodologi Pengujian:** 
  1. Penyerang memulai ratusan koneksi HTTP simultan ke server.
  2. Penyerang dengan sengaja mengirimkan header HTTP secara tidak lengkap dan sangat lambat untuk menahan koneksi agar tetap terbuka (mencegah timeout bawaan jika `mod_reqtimeout` tidak dikonfigurasi dengan ketat).
  3. Mengamati waktu respons server dari peramban web normal saat serangan berlangsung.
- **Hasil yang Diharapkan:** 
  - *Thread pool server* Apache habis terpakai, mengakibatkan situs web menjadi tidak responsif atau mengembalikan pesan *error Gateway Timeout* kepada pengguna sah.
  - Fungsi kontrol dan pemantauan sistem pakan otomatis melalui antarmuka web lumpuh total, menguji seberapa cepat Blue Team menyadari gangguan operasional ini melalui dasbor pemantauan mereka.
