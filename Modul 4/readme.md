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
```

<img width="1200" height="1600" alt="image" src="https://github.com/user-attachments/assets/00340c80-9e8e-4d04-90b7-70faa3e4eb26" />


## 3.2 FortiGate

### Interface

- port1 : WAN
- port2 : INSIDE
- port3 : DMZ

### Routing

- Default Route → 10.10.10.1
- Route LAN → 10.20.20.2

### Policy

- LAN_TO_WAN
- LAN_TO_DMZ
- WAN_TO_DMZ_HTTP

### VIP

- 10.10.10.2 → 192.168.20.10

<img width="1200" height="1600" alt="image" src="https://github.com/user-attachments/assets/6deed057-4b95-433b-9790-5980d9b9b216" />

## 3.3 Cisco Router

### Konfigurasi

```cisco
interface g0/0
 ip address 10.20.20.2 255.255.255.252
 no shutdown

interface g0/1
 ip address 192.168.10.1 255.255.255.0
 no shutdown

ip route 0.0.0.0 0.0.0.0 10.20.20.1
```

<img width="1200" height="1600" alt="image" src="https://github.com/user-attachments/assets/84705e64-63cc-445d-95b4-5c5e661402dd" />

## 3.4 Client LAN

### Konfigurasi

| Parameter | Nilai |
|------------|------------|
| IP | 192.168.10.10/24 |
| Gateway | 192.168.10.1 |
| DNS | 8.8.8.8 |

## 3.5 Client WAN

### Konfigurasi

| Parameter | Nilai |
|------------|------------|
| IP | 172.16.100.10/24 |
| Gateway | 172.16.100.1 |
| DNS | 8.8.8.8 |

## 3.6 Ubuntu Server DMZ

### Konfigurasi

| Parameter | Nilai |
|------------|------------|
| IP | 192.168.20.10/24 |
| Gateway | 192.168.20.1 |
| DNS | 8.8.8.8 |

### Install Nginx

```bash
sudo apt update
sudo apt install nginx -y
```

### Halaman Web

```
Tumod_4_DMZ_Firewall_XX-KelompokXX
```

<img width="1600" height="1080" alt="image" src="https://github.com/user-attachments/assets/113ce413-aa97-4048-b004-9e28b1399548" />

# 4. Hasil Uji

<img width="1600" height="1100" alt="image" src="https://github.com/user-attachments/assets/67079366-141f-41d9-b010-bbc4745eac03" />

<img width="1600" height="1068" alt="image" src="https://github.com/user-attachments/assets/38fdf80d-fc2d-41b0-8c9c-93689341e0c5" />

<img width="1600" height="1240" alt="image" src="https://github.com/user-attachments/assets/e41a369e-f74e-4001-934d-d2c2d72579f2" />

<img width="1600" height="956" alt="image" src="https://github.com/user-attachments/assets/009a9951-0e3d-4589-a1d8-572320418ebc" />

<img width="1600" height="954" alt="image" src="https://github.com/user-attachments/assets/1526f936-6d16-4eeb-9c5b-1c29f881e9f1" />

<img width="1600" height="1222" alt="image" src="https://github.com/user-attachments/assets/34bb2d3f-c5f5-4d9e-8d09-c99cc86e2015" />

<img width="1600" height="933" alt="image" src="https://github.com/user-attachments/assets/42ea788f-a874-4462-bdef-f4e049def175" />

<img width="1600" height="952" alt="image" src="https://github.com/user-attachments/assets/e5c96f96-fb6b-4d1f-8ae4-da20cc6b52f8" />

<img width="1173" height="448" alt="image" src="https://github.com/user-attachments/assets/ccdcb369-ca8f-450d-8849-d761873cfcb6" />

<img width="1200" height="1600" alt="image" src="https://github.com/user-attachments/assets/933f5f29-b3bd-4ac6-9d11-0901690cfe8f" />

<img width="1200" height="1600" alt="image" src="https://github.com/user-attachments/assets/90627cd2-2029-46a0-bff0-bee6bf45fa11" />
