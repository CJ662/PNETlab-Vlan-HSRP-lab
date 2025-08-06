# PNETlab-Vlan-HSRP-lab
Simulated enterprise network with VLANs, EtherChannel, Inter-VLAN routing &amp; HSRP using PNETLab
PNETLab Advanced VLAN, EtherChannel, and
HSRP Lab
This project demonstrates a robust Layer 2/3 network deployment using PNETLab, simulating a small
enterprise LAN with:

*VLAN segmentation
*LACP-based EtherChannel trunking
*Inter-VLAN routing using Router-on-a-Stick
*HSRP-based default gateway redundancy
*6 Windows PCs for realistic endpoint emulation

🧱 Topology Overview

*Switches: 2x Cisco IOL L2 (Switch1, Switch2)
*Router: 1x Cisco IOL Router (Router1)
*PCs: 6 Windows VMs (3 per switch)
*VLANs:
*VLAN 10 → 192.168.10.0/24
*VLAN 20 → 192.168.20.0/24
*VLAN 30 → 192.168.30.0/24

🔌 Interfaces & EtherChannel

*EtherChannel (Port-Channel1) between Switch1 & Switch2
*Two physical trunk links using LACP (active/active)

interface range Ethernet0/0, Ethernet1/0
channel-group 1 mode active
switchport mode trunk
!
interface Port-channel1
switchport trunk encapsulation dot1q
switchport mode trunk
switchport trunk allowed vlan 10,20,30

🌐 Router-on-a-Stick (RoaS)
interface GigabitEthernet0/0.10
encapsulation dot1Q 10
ip address 192.168.10.1 255.255.255.0
interface GigabitEthernet0/0.20
encapsulation dot1Q 20
ip address 192.168.20.1 255.255.255.0
interface GigabitEthernet0/0.30
encapsulation dot1Q 30
ip address 192.168.30.1 255.255.255.0

🔁 HSRP Configuration (Default Gateway Redundancy)
VLAN  VIP           Switch1 IP    Switch2 IP     Priority
10    192.168.10.1  192.168.10.3  192.168.10.4   110/90
20    192.168.20.1  192.168.20.3  192.168.20.4   110/90
30    192.168.30.1  192.168.30.3  192.168.30.4   90/120

interface Vlan10
ip address 192.168.10.3 255.255.255.0
standby 10 ip 192.168.10.1
standby 10 priority 110
standby 10 preempt

🖥️ Windows PC Configuration

Each PC was assigned:
  *Static IP in respective VLAN
  *Subnet mask: 255.255.255.0
  *Default gateway: HSRP Virtual IP
  
Firewall adjusted using:

netsh advfirewall firewall add rule name="Allow-Ping-In" protocol=icmpv4:8,any
dir=in action=allow

✅ Ping Tests & Validation

Source → Destination  Description                Result
PC1 → PC4             Same VLAN across switches  ✅ Success
PC2 → PC6             VLAN20 → VLAN30            ⚠️ 25% loss → Resolved
PC3 → PC5             VLAN30 → VLAN10            ⚠️ 25% loss → Resolved
PC1 → 192.168.10.1    Ping to HSRP Virtual IP    ✅ Success
PC1 → 192.168.10.3/4  Ping to both switch SVIs   ✅ Success
🔁 Re-tested with ping -n 10 (10 requests):
✅ All returned 0% loss. Initial 25% loss was due to ARP resolution delay.

🛠️ Verification Commands

  *show etherchannel summary
  *show interfaces trunk
  *show standby brief
  *show mac address-table
  *show ip arp
  *ping tests between all VLANs and routers

  🔐 Relevance to Cybersecurity

  *Demonstrates segmentation, redundancy, and routing—crucial in SOC environments
  *Aligns with skills for network-based threat detection
  *Helps reinforce knowledge for CCNA, SOC Analyst, or GRC roles

  📂 Files Included (Upload to Repo)

  *Pnetlab VLAN Project.unl (Topology File)
  *.png screenshots of:
  *Topology
  *Ping tests
  *HSRP verification
  *EtherChannel status

  📌 Status
  ✅ Fully working 🎯 0% Packet Loss after configuration 🔒 Duplicate IPs resolved and
confirmed
