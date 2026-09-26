# KeamananSiber_Kelompok2

## Deskripsi Skenario Proyek

Proyek ini mensimulasikan skenario pengujian keamanan jaringan dan analisis ancaman Siber menggunakan arsitektur terisolasi (segmentasi jaringan). 

Skenario dibagi menjadi 3 zona utama:
1. **Attacker Zone (`10.10.2.0/24`):** Menggunakan Kali Linux untuk mensimulasikan berbagai teknik serangan eksternal (penetrasi/eksploitasi) terhadap layanan yang rentan.
2. **Target Zone (`10.20.2.0/24`):** Menggunakan Metasploitable sebagai target server yang memiliki berbagai celah keamanan/layanan rentan untuk diuji.
3. **Monitoring & Management Zone (`10.30.2.0/24`):** Menggunakan Security Onion sebagai Network Intrusion Detection System (NIDS). Security Onion memiliki dua fungsi utama:
   - **Management Interface (`10.30.2.2`):** Untuk mengakses dashboard monitoring dan analisis log security.
   - **Sensor Interface (No IP):** Menerima salinan lalu lintas data (*port mirroring/SPAN*) dari jalur interaksi Attacker dan Target (`G0/0` & `G0/1`) melalui port `G0/3` router untuk mendeteksi serta menganalisis aktivitas serangan secara real-time.