# Tumod 5 Vlan-Trunk-OSPF-MultiVendor #
## Topologi ##
<img width="1277" height="698" alt="image" src="https://github.com/user-attachments/assets/3a1b2b1b-678d-4912-8dc2-8345b055b828" />

# MODUL 1 : KONFIGURASI VLAN DAN TRUNK #
## Cisco Switch Jakarta ##
Membuat VLAN
```
enable
conf t

vlan 10
 name FINANCE

vlan 20
 name IT

vlan 60
 name SERVER
 ```
Konfigurasi Access Port
```
interface g0/1
switchport mode access
switchport access vlan 10

interface g0/2
switchport mode access
switchport access vlan 20

interface g0/3
switchport mode access
switchport access vlan 60
Konfigurasi Trunk
interface g0/0
switchport trunk encapsulation dot1q
switchport mode trunk
switchport trunk allowed vlan 10,20,60
```
Verifikasi
```
show vlan brief
show interfaces trunk
```

# MODUL 2 : INTERFACE VLAN ROUTING MEMAKAI MIKROTIK #
Membuat VLAN Interface
```
/interface vlan
add interface=ether2 name=vlan10-finance vlan-id=10
add interface=ether2 name=vlan20-it vlan-id=20
add interface=ether2 name=vlan60-server vlan-id=60
```
Interface	IP Address
```
vlan10-finance	192.168.10.1/24
vlan20-it	192.168.20.1/24
vlan60-server	192.168.60.1/24
/ip address
add address=192.168.10.1/24 interface=vlan10-finance
add address=192.168.20.1/24 interface=vlan20-it
add address=192.168.60.1/24 interface=vlan60-server
```
Route Menuju FortiGate
```
/ip address
add address=10.10.100.2/30 interface=ether1

/ip route
add dst-address=0.0.0.0/0 gateway=10.10.100.1
```
Verifikasi
```
/ip address print
/ip route print
```

# MODUL 3 : DHCP SERVER MENGGUNAKAN UBUNTU SERVER #
```
Install ISC DHCP Server
sudo apt update
sudo apt install isc-dhcp-server
```
Konfigurasi DHCP Server
```
/etc/dhcp/dhcpd.conf
```
Konfigurasi:
```
subnet 192.168.10.0 netmask 255.255.255.0 {
 range 192.168.10.100 192.168.10.200;
 option routers 192.168.10.1;
}

subnet 192.168.20.0 netmask 255.255.255.0 {
 range 192.168.20.100 192.168.20.200;
 option routers 192.168.20.1;
}
```
Restart CTL
```
sudo systemctl restart isc-dhcp-server
sudo systemctl status isc-dhcp-server
```

# MODUL 4 : DHCP RELAY #
## MikroTik Jakarta ##
```
/ip dhcp-relay
add interface=vlan10-finance dhcp-server=192.168.60.10 local-address=192.168.10.1
add interface=vlan20-it dhcp-server=192.168.60.10 local-address=192.168.20.1
```
Verifikasi
```
/ip dhcp-relay print
```
# MODUL 5 : WEB SERVER MENGGUNAKAN NGINX #
Install Nginx
```
sudo apt install nginx
```
Membuat Halaman Web
```
sudo nano /var/www/html/index.html
```
Isi file:
```
<h1>Web server jakarta</h1>
<h2>Ubuntu Server VLAN60</h2>
<> Komputer Engineering Modul 5<>
```
Restart Service
```
sudo systemctl restart nginx
sudo systemctl status nginx
```
# MODUL 6 : VRRP #
Konfigurasi VRRP pada MikroTik Jakarta
```
/interface vrrp
add interface=ether1 name=vrrp-jakarta vrid=1 priority=255
```
Konfigurasi IP Virtual
```
/ip address
add address=10.10.100.254/24 interface=vrrp-jakarta
```
Verifikasi
```
/interface vrrp print
```
# MODUL 7 : NAT DAN AKSES INTERNET #
Konfigurasi DHCP Client pada MikroTik ISP
```
/ip dhcp-client
add interface=ether1 disabled=no
```
Konfigurasi NAT pada MikroTik ISP
```
/ip firewall nat
add chain=srcnat action=masquerade out-interface=ether1
```
Verifikasi
```
/ip dhcp-client print
/ip firewall nat print
```
# MODUL 8 : KONFIGURASI BAGIAN SURABAYA #
Membuat VLAN Interface
```
/interface vlan
add interface=ether2 name=vlan30-sales vlan-id=30
add interface=ether2 name=vlan40-operation vlan-id=40
```
Konfigurasi IP Address
```
/ip address
add address=192.168.30.1/24 interface=vlan30-sales
add address=192.168.40.1/24 interface=vlan40-operation
```
Konfigurasi DHCP Server VLAN 30
```
/ip pool
add name=pool30 ranges=192.168.30.100-192.168.30.200

/ip dhcp-server
add name=dhcp30 interface=vlan30-sales address-pool=pool30

/ip dhcp-server network
add address=192.168.30.0/24 gateway=192.168.30.1 dns-server=8.8.8.8
```
Konfigurasi Default Route
```
/ip route
add dst-address=0.0.0.0/0 gateway=10.20.100.1
```
Verifikasi
```
/ip address print
/ip dhcp-server print
/ip route print
```
# MODUL 9 : GRE TUNNEL #
FortiGate Jakarta 
```
config system gre-tunnel
edit "GRE-JKT"
set interface "port1"
set remote-gw 2.2.2.2
set local-gw 1.1.1.1
next
end
config system interface
edit "GRE-JKT"
set ip 172.16.100.1/30
next
end
```
FortiGate Surabaya
```
config system gre-tunnel
edit "GRE-SBY"
set interface "port1"
set remote-gw 1.1.1.1
set local-gw 2.2.2.2
next
end
config system interface
edit "GRE-SBY"
set ip 172.16.100.2/30
next
end
```
Verifikasi
```
execute ping 172.16.100.1
execute ping 172.16.100.2
```
# MODUL 10 : OSPF DYNAMIC ROUTING #
Konfigurasi OSPF pada FortiGate Jakarta
```
config router ospf
set router-id 1.1.1.1

config area
edit 0.0.0.0
next
end

config network
edit 1
set prefix 172.16.100.0 255.255.255.252
next

edit 2
set prefix 192.168.10.0 255.255.255.0
next

edit 3
set prefix 192.168.20.0 255.255.255.0
next

edit 4
set prefix 192.168.60.0 255.255.255.0
next
end
end
```
Konfigurasi OSPF pada FortiGate Surabaya
```
config router ospf
set router-id 2.2.2.2

config area
edit 0.0.0.0
next
end

config network
edit 1
set prefix 172.16.100.0 255.255.255.252
next

edit 2
set prefix 192.168.30.0 255.255.255.0
next

edit 3
set prefix 192.168.40.0 255.255.255.0
next
end
end
```
Verifikasi
```
get router info ospf neighbor
get router info routing-table ospf
```
# PENGUJIAN AKHIR #
<img width="799" height="475" alt="Screenshot 2026-06-13 110150" src="https://github.com/user-attachments/assets/086da2ba-ecee-4493-9610-ca17af3b5420" />

<img width="924" height="557" alt="Screenshot 2026-06-13 113439" src="https://github.com/user-attachments/assets/42349a7f-54bb-4a88-a8e5-9336fb5d0de1" />

<img width="962" height="490" alt="Screenshot 2026-06-13 113534" src="https://github.com/user-attachments/assets/cce8e20b-a5ff-4347-8b45-7df5bfde50c1" />

<img width="968" height="521" alt="Screenshot 2026-06-13 113626" src="https://github.com/user-attachments/assets/b845c06c-fcdd-4bda-81eb-1f8f85c3ff6c" />

<img width="899" height="508" alt="Screenshot 2026-06-13 113842" src="https://github.com/user-attachments/assets/cb55bbc5-659c-4a91-8ddd-d074df7d75fb" />

<img width="874" height="543" alt="Screenshot 2026-06-13 113858" src="https://github.com/user-attachments/assets/e4904c26-ecf2-4f48-baf1-1d3c262b7ec7" />

<img width="858" height="560" alt="Screenshot 2026-06-13 114334" src="https://github.com/user-attachments/assets/d75c1704-fe0b-4bfb-9e50-896eb2d7e841" />

<img width="986" height="589" alt="Screenshot 2026-06-13 114414" src="https://github.com/user-attachments/assets/042a7e7f-5399-4527-83cb-97ac5ae449ec" />

<img width="882" height="518" alt="Screenshot 2026-06-13 114542" src="https://github.com/user-attachments/assets/1f9e9f91-2687-4a06-87ad-2e041bde32b1" />

<img width="863" height="531" alt="Screenshot 2026-06-13 114616" src="https://github.com/user-attachments/assets/8110cf7a-67e5-4c5c-a8b2-890aea261110" />

# KESIMPULAN #

Pada praktikum ini berhasil dibuat sebuah topologi jaringan yang menghubungkan Jakarta dengan Branch Surabaya menggunakan Cisco, MikroTik, FortiGate, dan Ubuntu Server.

Implementasi VLAN, DHCP Server, DHCP Relay, NAT, GRE Tunnel, serta OSPF Dynamic Routing membuat komunikasi antar jaringan dan antar site berjalan dengan baik. Selain itu, seluruh client pada kedua lokasi berhasil memperoleh akses internet dan dapat mengakses web server yang berada di Jakarta sesuai dengan rancangan topologi yang sudah dibuat.

