# KeamananSiber_Kelompok2

### Deskripsi Skenario Proyek: Pengamanan Sistem Automatic Chicken Feeder Berbasis IoT

Proyek ini mensimulasikan skenario pengujian keamanan dan pemantauan ancaman siber pada infrastruktur **Automatic Chicken Feeder (Sistem Pemberi Pakan Ayam Otomatis Berbasis IoT)**. Skenario dirancang menggunakan arsitektur jaringan terfragmentasi (segmentasi subnet) untuk mengisolasi komponen penyerang, target IoT, dan sistem pemantauan keamanan.

### Pembagian Zona Jaringan & Peran Node:

1. **Target Zone — IoT Feeder Controller (`10.20.2.0/24`):**
   * **Node (`10.20.2.2`):** Berperan sebagai Server Pusat / Gateway IoT (diwakili oleh Metasploitable) yang mengontrol jadwal pemberian pakan otomatis, menyimpan log penimbangan/pakan, dan menyediakan API/Web Interface untuk monitoring peternak.
   * **Potensi Vabilitas:** Server menjalankan layanan web/database default yang rentan terhadap serangan injection, unauthorized control, atau Denial of Service (DoS) yang dapat mematikan fungsi pemberian pakan otomatis.

2. **Attacker Zone — External Threat (`10.10.2.0/24`):**
   * **Node (`10.10.2.2`):** Berperan sebagai penyerang luar/peretas (menggunakan Kali Linux) yang mencoba melakukan scanning port, exploitasi web API feeder, atau serangan pemutusan akses agar sistem pakan otomatis tidak berfungsi.

3. **Management & Monitoring Zone (`10.30.2.0/24`):**
   * **Security Onion Dashboard (`10.30.2.2`):** Interface khusus Blue Team untuk memantau alert keamanan, memproses data log, dan menganalisis trafik jaringan secara isolated.
   * **Security Onion Sensor (NIC 3 / No IP):** Terhubung ke port mirror router (`G0/3`) untuk menyadap seluruh lalu lintas data antara Kali Linux (`G0/0`) dan IoT Feeder Server (`G0/1`). Sensor ini bertugas mendeteksi aktivitas mencurigakan (seperti *brute force*, *command injection*, atau *malicious payload*) yang ditujukan ke sistem pakan otomatis tanpa mengganggu kinerja operasional server IoT.