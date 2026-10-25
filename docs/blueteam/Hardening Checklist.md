

| ID | Kategori | Kontrol Hardening | Target Layanan / File | Status | Bukti |
| ----- | ----- | ----- | ----- | ----- | ----- |
| A1 | Access & Auth | Nonaktifkan login root lewat SSH | `/etc/ssh/sshd_config.d/10-hardening.conf` | Selesai | `ssh-before.txt`, `ssh-after.txt` |
| A2 | Access & Auth | Batasi percobaan login (MaxAuthTries 3, MaxSessions 2\) | file yang sama | Selesai | `ssh-before.txt`, `ssh-after.txt` |
| A3 | Access & Auth | Matikan fitur SSH yang tidak dipakai (X11Forwarding, AgentForwarding, TCPKeepAlive) | file yang sama | Selesai | `ssh-before.txt`, `ssh-after.txt` |
| B1 | Service Surface | Firewall UFW: default deny, whitelist SSH dan HTTP | UFW | Selesai | `ufw-before.txt`, `ufw-after.txt`, `ports-before.txt`, `ports-after.txt`, `nmap-before.txt`, `nmap-after.txt` |
| B2 | Service Surface | Sembunyikan versi Apache (ServerTokens Prod, ServerSignature Off) | `/etc/apache2/conf-available/zz-hardening.conf` | Selesai | `apache-before.txt`, `apache-after.txt`, `nmap-after.txt` |
| C1 | Logging | Log SSH detail (LogLevel VERBOSE) dan log firewall (UFW logging on) | SSH dan UFW | Selesai | `ssh-after.txt`, `ufw-after.txt` |
| D1 | Verifikasi | Audit Lynis sebelum dan sesudah (61 menjadi 67\) | Lynis | Selesai | `baseline-before.txt`, `baseline-after.txt` |

