# l3switchdhcp17hkh

## Variablen

```PacketTracer
    lu2
    htl.com
    Kids
    susu
    unprivileged
    restricted
    server
    management
    10.162
```

All together now ....

VLANS: VTP/DHCP-L3Switch acts as VTP server for the VTP domain lu2, all other switches are VTP clients.

On each switch use port 1 Vlan 10 Kids, port 2 Vlan 20 susu, port 3 Vlan 30 unprivileged, port 4 Vlan 40 restricted, native Vlan 99 management, port 13 Vlan 50 server.
Configure trunks according to the topology, native vlan 99.
Internal Network DHCP: VTP/DHCP-L3Switch acts as DHCP Server and Inte-VLAN router for the intranet.
Create a DHCP Pool with the name VLAN and the Vlan Id (eg. ip dhcp pool VLAN10 for Vlan 10) for each Vlan. 
Subnet 10.162.0.0/8 with a /24 mask and use 10.162.Vlanid.0/24 for each VLAN as network address. Use the lowest ip in each subnet as standard gateway. Make sure that this addresse will not used by DHCP on the pc's!
Create a SVI for each VLAN except Vlan40 and 99, with the following ip: 10.162.Vlanid.1/24 
The servers in Vlan 50 get static ip addresses (first and last ip in their subnet).
As domain name use htl.com
Create a default route pointing to the BorderRouter. Configure the Intranet Server as https and DNS server (for dmz.acme.com, intranet.acme.com, wgs2.acme.com, wgs3.acme.com). Configure it as DNS server on all Pcs, routers ... necessary.

Switches A-F: configure the "double-trunks" with etherchannel to double the available bandwidth.

Configure STP 802.1D
Use show spanning-tree to check for the root bridge in each vlan
Change the root bridges: SW1 for VLAN 10, SW2 for VLAN 20, SW3 for VLAN 30

BorderRouter: routing, NAT and ACLs 
Create subinterfaces on the trunk for each subnet except Vlan40 and Vlan99. Use the highest available ip in each subnet as ip address. Make sure, these addresse will not be used by DHCP on the pc's!
DMZ Network: 10.162.100.0/24, Router and Server IP static. 
create a default route into the ISP Cloud !
For the ISP connection use 193.70.162.252/29
Configure NAT Overload on the serial interface, to mask internal adresses with one of the unused ISP addresses 
Use EIGRP 101 as routing protocol.
Configure the Company Web Server as https and DNS server (for wan.internet.com). Configure it as DNS server on all Pcs, routers .. necessary.

WAN: Use EIGRP 101 as routing protocol.
Click on the Peer Cloud: Set Connection Type to Outgoing, configure Peer Network Name Peerx where x is a unique number between 0 an 11 (talk to your collegues)!
Configure the appropriate Router0 link with the ip 10.x.5.2/24. Peer0 use 10.1.5.2 instead!
Connect with the password cisco!
Create a default route to the Internet-cloud and redistribute it.
Configure the WAN Web Server and the router interface with ip's from the network 193.170.149.0/24. 
Configure eigrp ...

Test: Each PC from Vlans 10,20,50 and 30 should be able to ping each other PC in this Vlans.
No Pc fromt the Vlans 40 and 99 should be reachable from outside their vlans. 
Use the browser on the Pcs to show the homepages on all https servers using their URLs.
Ping your collegues DMZ https servers an try showing their home pages.

submit your work to moodle.htlwrn.ac.at!
ACLS (Border Router and/or VTP/DHCP-L3Switch):
Pc's from every Vlan could connect to the Intranet/Workgroup-Servers (Vlan50)
PC's from every Vlan except Vlans 10 and 40 are allowed to the DMZ
Only Vlans 20, 30 and 40 are allowed to the WAN.
Traffic from the WAN is only allowed to the DMZ Websserver (http,https,ssh).

© Mag. W. Cyrmon 2017

## Anleitung

1. Replace variables
1. Paste config
1. Enable DHCP on all PCs
1. Set IPs on all servers (See section: server IPs )
1. Enable HTTPS (only) on all servers
1. Enable DNS on intranet.lu2.com
1. Set DNS entries (See section: DNS server)

## DNS server

Name                Adresse

dmz.lu2.com       10.162.100.2
intranet.lu2.com  10.162.50.3
wgs2.lu2.com      10.162.50.2
wgs3.lu2.com      10.162.50.253

wan.internet.com   193.170.149.254

## server IPs

Name                NW-Adresse            IP-Adresse            Subnetzmaske           Gateway                DNS

wgs2.lu2.com       10.162.50.0          10.162.50.2         255.255.255.0       10.162.50.1         10.162.50.3
wgs3.lu2.com       10.162.50.0          10.162.50.253       255.255.255.0       10.162.50.1         10.162.50.3
intranet.lu2.com   10.162.50.0          10.162.50.3         255.255.255.0       10.162.50.1         10.162.50.3
dmz.lu2.com        10.162.100.0         10.162.100.2        255.255.255.0       10.162.100.1        10.162.50.3
wan.internet.com   193.170.149.0        193.170.149.254    255.255.255.0       193.170.149.1      193.170.149.254

## Router0

```PacketTracer
enable
    conf t
        hostname Router0
        ip route 0.0.0.0 0.0.0.0 se0/3/0
        int s0/3/1
            no shutdown
            ip addr 193.70.193.253 255.255.255.248
         int s0/3/0
            no shutdown
            ip addr 10.1.5.2 255.255.255.0
        int gig0/0
            ip addr 193.170.149.1 255.255.255.0
            no shutdown
        router eigrp 101
            network 193.70.193.252 0.0.0.7
            network 193.70.193.248 0.0.0.7
            network 193.170.149.0 0.0.0.255
            redistribute static
            exit
        exit
    cop ru st
```

## BorderRouter

```PacketTracer
enable
    conf t
        hostname BorderRouter
        ip name-server 10.162.50.3
        ip route 0.0.0.0 0.0.0.0 se0/2/0
        spanning-tree vlan 10
        spanning-tree vlan 20
        spanning-tree vlan 30
        spanning-tree vlan 40
        spanning-tree vlan 50
        spanning-tree vlan 99
        ip access-list extended nat
            permit ip 10.162.0.0 0.0.255.255 any
            exit
        ip nat inside source list nat interface serial 0/2/0 overload
        ip nat inside source static tcp 10.162.100.2 22 193.70.193.252 22
        ip nat inside source static tcp 10.162.100.2 80 193.70.193.252 80
        ip nat inside source static tcp 10.162.100.2 443 193.70.193.252 443
        int fa0/0
            ip nat inside
            no shutdown
        int fa0/0.10
            ip nat inside
            no shutdown
            encapsulation dot1q 10
            ip addr 10.162.10.254 255.255.255.0
            ip access-group list in
        int fa0/0.20
            ip nat inside
            no shutdown
            encapsulation dot1q 20
            ip addr 10.162.20.254 255.255.255.0
            ip access-group list in
        int fa0/0.30
            ip nat inside
            no shutdown
            encapsulation dot1q 30
            ip addr 10.162.30.254 255.255.255.0
            ip access-group list in
        int fa0/0.50
            ip nat inside
            no shutdown
            encapsulation dot1q 50
            ip addr 10.162.50.254 255.255.255.0
            ip access-group list in
        int fa0/1
            ip nat inside
            ip addr 10.162.100.1 255.255.255.0
            no shutdown
        int se0/2/0
            no shutdown
            ip addr 193.70.193.252 255.255.255.248
            ip nat outside
        router eigrp 101
            network 10.162.0.0
            redistribute static
            exit
        exit
    cop ru st
```

## VTP/DHCP-L3Switch

```PacketTracer
enable
    conf t
        ip name-server 10.162.50.3
        ip routing
        hostname VTP/DHCP-L3Switch
        vtp domain lu2
        vtp mode server
        ip domain htl.com
        ip dhcp excluded-address 10.162.10.1
        ip dhcp excluded-address 10.162.10.254
        ip dhcp excluded-address 10.162.20.1
        ip dhcp excluded-address 10.162.20.254
        ip dhcp excluded-address 10.162.30.1
        ip dhcp excluded-address 10.162.30.254
        ip dhcp excluded-address 10.162.40.1
        ip dhcp excluded-address 10.162.50.1
        ip dhcp excluded-address 10.162.50.2
        ip dhcp excluded-address 10.162.50.3
        ip dhcp excluded-address 10.162.50.253
        ip dhcp excluded-address 10.162.50.254
        ip dhcp excluded-address 10.162.99.1
        ip dhcp pool VLAN10
            network 10.162.10.0 255.255.255.0
            default-router 10.162.10.1
            dns-server 10.162.50.3
        ip dhcp pool VLAN20
            network 10.162.20.0 255.255.255.0
            default-router 10.162.20.1
            dns-server 10.162.50.3
        ip dhcp pool VLAN30
            network 10.162.30.0 255.255.255.0
            default-router 10.162.30.1
            dns-server 10.162.50.3
        ip dhcp pool VLAN40
            network 10.162.40.0 255.255.255.0
            default-router 10.162.40.1
            dns-server 10.162.50.3
        ip dhcp pool VLAN50
            network 10.162.50.0 255.255.255.0
            default-router 10.162.50.1
            dns-server 10.162.50.3
        ip dhcp pool VLAN99
            network 10.162.99.0 255.255.255.0
            default-router 10.162.99.1
            dns-server 10.162.50.3
        vlan 10
            name Kids
        vlan 20
            name susu
        vlan 30
            name unprivileged
        vlan 40
            name restricted
        vlan 50
            name server
        vlan 99
            name management
        interface fa0/1
            no shutdown
            switchport trunk encapsulation dot1q
            switchport mode trunk
            switchport trunk native vlan 99
        interface gig0/1
            no shutdown
            switchport trunk encapsulation dot1q
            switchport mode trunk
            switchport trunk native vlan 99
        interface gig0/2
            no shutdown
            switchport trunk encapsulation dot1q
            switchport mode trunk
            switchport trunk native vlan 99
        interface fastethernet0/13
            switchport mode access
            switch access vlan 50
        interface vlan 10
            no shutdown
            ip address 10.162.10.1 255.255.255.0
        interface vlan 20
            no shutdown
            ip address 10.162.20.1 255.255.255.0
        interface vlan 30
            no shutdown
            ip address 10.162.30.1 255.255.255.0
        interface vlan 50
            no shutdown
            ip address 10.162.50.1 255.255.255.0
        router eigrp 101
            network 10.162.0.0
            exit
        spanning-tree vlan 10
        spanning-tree vlan 20
        spanning-tree vlan 30
        spanning-tree vlan 40
        spanning-tree vlan 50
        spanning-tree vlan 99
        ip route 0.0.0.0 0.0.0.0 fa0/1
        exit
    cop ru st
```

## Switch1

```PacketTracer
enable
    conf t
        hostname SW1
        vtp domain lu2
        vtp mode client
        interface gig0/1
            switchport mode trunk
            switchport trunk native vlan 99
        interface gig0/2
            switchport mode trunk
            switchport trunk native vlan 99
        interface fastethernet0/20
            switchport mode trunk
            switchport trunk native vlan 99
        interface fastethernet0/21
            switchport mode trunk
            switchport trunk native vlan 99
            channel-group 2 mode active
        interface fastethernet0/22
            switchport mode trunk
            switchport trunk native vlan 99
            channel-group 2 mode active
        interface fastethernet0/23
            switchport mode trunk
            switchport trunk native vlan 99
            channel-group 1 mode active
        interface fastethernet0/24
            switchport mode trunk
            switchport trunk native vlan 99
            channel-group 1 mode active
        interface port-channel 1
            no shutdown
            switchport mode trunk
            switchport trunk native vlan 99
        interface port-channel 2
            no shutdown
            switchport mode trunk
            switchport trunk native vlan 99
        spanning-tree vlan 10 root primary
        exit
    cop ru st
```

## Switch2

```PacketTracer
enable
    conf t
        hostname SW2
        vtp domain lu2
        vtp mode client
        interface gig0/1
            switchport mode trunk
            switchport trunk native vlan 99
        interface gig0/2
            switchport mode trunk
            switchport trunk native vlan 99
        interface range fastethernet0/17 - 20
            switchport mode trunk
            switchport trunk native vlan 99
        interface fastethernet0/21
            switchport mode trunk
            switchport trunk native vlan 99
            channel-group 2 mode active
        interface fastethernet0/22
            switchport mode trunk
            switchport trunk native vlan 99
            channel-group 2 mode active
        interface fastethernet0/23
            switchport mode trunk
            switchport trunk native vlan 99
            channel-group 1 mode active
        interface fastethernet0/24
            switchport mode trunk
            switchport trunk native vlan 99
            channel-group 1 mode active
        interface port-channel 1
            no shutdown
            switchport mode trunk
            switchport trunk native vlan 99
        interface port-channel 2
            no shutdown
            switchport mode trunk
            switchport trunk native vlan 99
        spanning-tree vlan 20 root primary
        exit
    cop ru st
```

## Switch3

```PacketTracer
enable
    conf t
        hostname SW3
        vtp domain lu2
        vtp mode client
        interface gig0/1
            switchport mode trunk
            switchport trunk native vlan 99
        interface gig0/2
            switchport mode trunk
            switchport trunk native vlan 99
        interface fastethernet0/20
            switchport mode trunk
            switchport trunk native vlan 99
        interface fastethernet0/21
            channel-group 1 mode active
        interface fastethernet0/22
            channel-group 2 mode active
        interface fastethernet0/23
            channel-group 2 mode active
        interface fastethernet0/24
            channel-group 1 mode active
        interface port-channel 1
            no shutdown
            switchport mode trunk
            switchport trunk native vlan 99
        interface port-channel 2
            no shutdown
            switchport mode trunk
            switchport trunk native vlan 99
        spanning-tree vlan 30 root primary
        exit
    cop ru st
```

## SW-A

```PacketTracer
enable
    conf t
        hostname SW-A
        vtp domain lu2
        vtp mode client
        interface range fastethernet0/23 - 24
            channel-group 1 mode active
        interface port-channel 1
            no shutdown
            switchport mode trunk
            switchport trunk native vlan 99
        interface fastethernet0/22
            switchport mode trunk
            switchport trunk native vlan 99
        interface fastethernet0/1
            switchport mode access
            switch access vlan 10
        interface fastethernet0/2
            switchport mode access
            switch access vlan 20
        interface fastethernet0/3
            switchport mode access
            switch access vlan 30
        interface fastethernet0/4
            switchport mode access
            switch access vlan 40
        interface fastethernet0/13
            switchport mode access
            switch access vlan 50
            exit
        exit
    cop ru st
```

## SW-B

```PacketTracer
enable
    conf t
        hostname SW-B
        vtp domain lu2
        vtp mode client
        interface range fastethernet0/23 - 24
            channel-group 2 mode active
        interface port-channel 2
            no shutdown
            switchport mode trunk
            switchport trunk native vlan 99
        interface fastethernet0/22
            switchport mode trunk
            switchport trunk native vlan 99
        interface fastethernet0/22
            switchport mode trunk
            switchport trunk native vlan 99
        interface fastethernet0/1
            switchport mode access
            switch access vlan 10
        interface fastethernet0/2
            switchport mode access
            switch access vlan 20
        interface fastethernet0/3
            switchport mode access
            switch access vlan 30
        interface fastethernet0/4
            switchport mode access
            switch access vlan 40
            exit
        exit
    cop ru st
```

## SW-C

```PacketTracer
enable
    conf t
        hostname SW-C
        vtp domain lu2
        vtp mode client
        interface range fastethernet0/23 - 24
            channel-group 1 mode active
        interface port-channel 1
            no shutdown
            switchport mode trunk
            switchport trunk native vlan 99
        interface fastethernet0/22
            switchport mode trunk
            switchport trunk native vlan 99
        interface fastethernet0/22
            switchport mode trunk
            switchport trunk native vlan 99
        interface fastethernet0/1
            switchport mode access
            switch access vlan 10
        interface fastethernet0/2
            switchport mode access
            switch access vlan 20
        interface fastethernet0/3
            switchport mode access
            switch access vlan 30
        interface fastethernet0/4
            switchport mode access
            switch access vlan 40
        interface fastethernet0/13
            switchport mode access
            switch access vlan 50
            exit
        exit
    cop ru st
```

## SW-D

```PacketTracer
enable
    conf t
        hostname SW-D
        vtp domain lu2
        vtp mode client
        interface range fastethernet0/23 - 24
            channel-group 2 mode active
        interface port-channel 2
            no shutdown
            switchport mode trunk
            switchport trunk native vlan 99
        interface fastethernet0/22
            switchport mode trunk
            switchport trunk native vlan 99
        interface fastethernet0/22
            switchport mode trunk
            switchport trunk native vlan 99
        interface fastethernet0/1
            switchport mode access
            switch access vlan 10
        interface fastethernet0/2
            switchport mode access
            switch access vlan 20
        interface fastethernet0/3
            switchport mode access
            switch access vlan 30
        interface fastethernet0/4
            switchport mode access
            switch access vlan 40
            exit
        exit
    cop ru st
```

## SW-E

```PacketTracer
enable
    conf t
        hostname SW-E
        vtp domain lu2
        vtp mode client
        interface range fastethernet0/23 - 24
            switchport mode trunk
            switchport trunk native vlan 99
            channel-group 1 mode active
        interface port-channel 1
            no shutdown
            switchport mode trunk
            switchport trunk native vlan 99
        interface fastethernet0/22
            switchport mode trunk
            switchport trunk native vlan 99
        interface fastethernet0/22
            switchport mode trunk
            switchport trunk native vlan 99
        interface fastethernet0/1
            switchport mode access
            switch access vlan 10
        interface fastethernet0/2
            switchport mode access
            switch access vlan 20
        interface fastethernet0/3
            switchport mode access
            switch access vlan 30
        interface fastethernet0/4
            switchport mode access
            switch access vlan 40
        interface fastethernet0/13
            switchport mode access
            switch access vlan 50
            exit
        exit
    cop ru st
```

## SW-F

```PacketTracer
enable
    conf t
        hostname SW-F
        vtp domain lu2
        vtp mode client
        interface range fastethernet0/23 - 24
            switchport mode trunk
            switchport trunk native vlan 99
            channel-group 1 mode active
        interface port-channel 1
            no shutdown
            switchport mode trunk
            switchport trunk native vlan 99
        interface fastethernet0/22
            switchport mode trunk
            switchport trunk native vlan 99
        interface fastethernet0/22
            switchport mode trunk
            switchport trunk native vlan 99
        interface fastethernet0/1
            switchport mode access
            switch access vlan 10
        interface fastethernet0/2
            switchport mode access
            switch access vlan 20
        interface fastethernet0/3
            switchport mode access
            switch access vlan 30
        interface fastethernet0/4
            switchport mode access
            switch access vlan 40
        interface fastethernet0/13
            switchport mode access
            switch access vlan 50
            exit
        exit
    cop ru st
```