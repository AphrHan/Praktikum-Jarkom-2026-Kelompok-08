# Vlan-Trunk-OSPF-MultiVendor #
## Topology ##

# MODULE 1 : VLAN AND TRUNK CONFIGURATION #
## Cisco Switch Jakarta ##
Membuat VLAN

enable
conf t

vlan 10
 name FINANCE

vlan 20
 name IT

vlan 60
 name SERVER
 
Konfigurasi Access Port
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
Verifikasi
show vlan brief
show interfaces trunk
MODULE 2 : INTER-VLAN ROUTING USING MIKROTIK
Membuat VLAN Interface
/interface vlan
add interface=ether2 name=vlan10-finance vlan-id=10
add interface=ether2 name=vlan20-it vlan-id=20
add interface=ether2 name=vlan60-server vlan-id=60
Konfigurasi IP Address
Interface	IP Address
vlan10-finance	192.168.10.1/24
vlan20-it	192.168.20.1/24
vlan60-server	192.168.60.1/24
/ip address
add address=192.168.10.1/24 interface=vlan10-finance
add address=192.168.20.1/24 interface=vlan20-it
add address=192.168.60.1/24 interface=vlan60-server
Route Menuju FortiGate
/ip address
add address=10.10.100.2/30 interface=ether1

/ip route
add dst-address=0.0.0.0/0 gateway=10.10.100.1
Verifikasi
/ip address print
/ip route print
