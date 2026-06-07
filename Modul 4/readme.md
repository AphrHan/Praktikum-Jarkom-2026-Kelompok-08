## TUGAS MODUL KELOMPOK 8 ROUTER GATEWAY
<img width="1543" height="1217" alt="Screenshot 2026-06-04 145202" src="https://github.com/user-attachments/assets/76b15bf6-2a22-4956-b088-fb6892237387" />

## Nama Kelompok
- Ahmad Hanif Al-Fatih
- Andi Abdul Malik Imaduddien
- Sintya Shavna Tamawulan

# 2. Tabel IP Address

| Perangkat | Interface | IP Address |
|------------|------------|------------|
| MikroTik ISP | ether1 | DHCP Client |
| MikroTik ISP | ether2 | 10.10.10.1/30 |
| MikroTik ISP | ether3 | 172.16.100.1/24 |
| FortiGate | port1 | 10.10.10.2/30 |
| FortiGate | port2 | 10.20.20.1/30 |
| FortiGate | port3 | 192.168.20.1/24 |
| Cisco Router | G0/0 | 10.20.20.2/30 |
| Cisco Router | G0/1 | 192.168.10.1/24 |
| Client LAN | eth0 | 192.168.10.10/24 |
| Client WAN | eth0 | 172.16.100.10/24 |
| Server DMZ | ens3 | 192.168.20.10/24 |

---

# 3. Konfigurasi Tiap Perangkat

## 3.1 MikroTik ISP

### Konfigurasi

```bash
/ip dhcp-client
add interface=ether1 disabled=no

/ip address
add address=10.10.10.1/30 interface=ether2
add address=172.16.100.1/24 interface=ether3

/ip firewall nat
add chain=srcnat out-interface=ether1 action=masquerade

/ip route
add dst-address=192.168.10.0/24 gateway=10.10.10.2
add dst-address=192.168.20.0/24 gateway=10.10.10.2
