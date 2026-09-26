# IP Address Plan & Network Architecture

Dokumen ini berisi perencanaan alokasi segmen IP address, konfigurasi interface router, dan OS target untuk proyek Keamanan Siber.

## 1. Network Subnetting & Topology Summary

| Network Zone | Subnet Network | Subnet Mask | Gateway IP | Description |
| :--- | :--- | :--- | :--- | :--- |
| **Attacker Zone** | `10.10.2.0` | `/24` (`255.255.255.0`) | `10.10.2.1` | Segmen jaringan khusus untuk Attacker Node |
| **Target Zone** | `10.20.2.0` | `/24` (`255.255.255.0`) | `10.20.2.1` | Segmen jaringan khusus untuk Target Node (DMZ/Server) |
| **Management Zone** | `10.30.2.0` | `/24` (`255.255.255.0`) | `10.30.2.1` | Segmen jaringan khusus untuk akses Management Monitoring |
| **Mirroring Zone** | N/A | N/A | N/A | SPAN/Mirror Port tanpa alokasi IP (Promiscuous Mode) |

---

## 2. IP Address Allocation Table

| Hostname / Node | Interface | IP Address | Netmask / Subnet | OS Direncanakan | Peran & Deskripsi |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **Attacker Node** | `eth0` | `10.10.2.2` | `255.255.255.0` (`/24`) | Kali Linux | Mesin penyerang (Red Team) |
| **Target Node (Korban)** | `eth0` | `10.20.2.2` | `255.255.255.0` (`/24`) | Metasploitable | Server target yang rentan untuk dieksploitasi |
| **Monitoring Mgmt** | `NIC 1` | `10.30.2.2` | `255.255.255.0` (`/24`) | Security Onion | Access Management UI (Dashboard/Kibana) |
| **Monitoring Sensor** | `NIC 3` | *No IP* | N/A | Security Onion | Interface Sniffing (Promiscuous Mode / Mirror) |

---

## 3. Router Interface Mapping

| Interface Router | Connected IP / Subnet | Target Destination / Connection | Function |
| :--- | :--- | :--- | :--- |
| **`G0/0`** | `10.10.2.1/24` | Attacker Node (`10.10.2.2`) | Gateway Subnet Attacker |
| **`G0/1`** | `10.20.2.1/24` | Target Node (`10.20.2.2`) | Gateway Subnet Target Server |
| **`G0/2`** | `10.30.2.1/24` | Monitoring Node (`10.30.2.2` - NIC 1) | Gateway Subnet Monitoring Management |
| **`G0/3`** | *Mirror Port* (No IP) | Monitoring Node (NIC 3) | SPAN/Port Mirroring dari interface `G0/0` & `G0/1` |

---

## 4. Monitoring Strategy & Notes

1. **Port Mirroring (SPAN):** Interface `G0/3` pada router dikonfigurasi untuk memicu *port mirroring* dari lalu lintas `G0/0` (Attacker) dan `G0/1` (Target)[cite: 2].
2. **Stealth Sensor:** Interface `NIC 3` pada Security Onion diset tanpa alokasi IP Address agar dapat bekerja pada *Promiscuous Mode* secara aman tanpa terpapar serangan langsung dari jaringan luar[cite: 1, 2].
3. **Management Access:** Pengelolaan Security Onion dilakukan secara terpisah melalui interface `NIC 1` dengan IP `10.30.2.2` pada segmen Management Zone (`10.30.2.0/24`)[cite: 1, 2].