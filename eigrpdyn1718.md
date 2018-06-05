# eigrpdyn1718

## Anleitung

1. Variablen ersetzen
2. Router configuration einfügen
3. Nur HTTPS Server einschalten
4. DNS ptwww.servernet.at 131.209.3.190 hinzufügen
5. Hello World.txt Inhalt ändern
6. PCs configurieren (IP, Gateway, Subnetz, !DNS!)
7. Power Cycle
8. Fast forward mind. 43 mal!
9. FD & FS auf der Topolgy ausgeben

## Variablen

```PacketTracer
209.165.200.224
131.209.
192.168.150
38550
```

## HQ

```PacketTracer
enable
  conf t
    ip domain-lookup
    ip name-server 131.209.3.190
    int fa0/0
      ip address 131.209.0.1 255.255.254.0
      no shutdown
      description .
    int se0/0/0
      ip address 192.168.150.17 255.255.255.252
      clock rate 1000000
      bandwidth 1000
      no shutdown
      description .
    int se0/0/1
      ip address 192.168.150.21 255.255.255.252
      clock rate 64000
      bandwidth 64
      no shutdown
      description .
    int fa0/1
      ip address 209.165.200.225 255.255.255.252
      no shutdown
      description .
      exit
    ip route 0.0.0.0 0.0.0.0 fa0/1
    router eigrp 38550
      network 192.168.150.16 0.0.0.3
      network 192.168.150.20 0.0.0.3
      network 131.209.0.0 0.0.1.255
      passive-interface fa0/0
      passive-interface fa0/1
      redistribute static
      no auto-summary
      exit
    exit
cop ru st
```

## Brunch 1

```PacketTracer
enable
  conf t
    ip domain-lookup
    ip name-server 131.209.3.190
    hostname Branch1
    int fa0/0
      ip address 131.209.2.1 255.255.255.0
      no shutdown
      description .
    int fa0/1
      ip address 192.168.150.29 255.255.255.252
      no shutdown
      description .
    int se0/0/0
      ip address 192.168.150.18 255.255.255.252
      clock rate 1000000
      bandwidth 1000
      no shutdown
      description .
    int se0/0/1
      ip address 192.168.150.25 255.255.255.252
      clock rate 4000000
      bandwidth 4000
      no shutdown
      description .
    exit
    router eigrp 38550
      network 192.168.150.28 0.0.0.3
      network 192.168.150.16 0.0.0.3
      network 192.168.150.24 0.0.0.3
      network 131.209.2.0 0.0.0.255
      passive-interface fa0/0
      no auto-summary
      exit
    exit
cop ru st
```

## Brunch 2

```PacketTracer
enable
  conf t
    ip domain-lookup
    ip name-server 131.209.3.190
    int fa0/0
      ip address 131.209.3.1 255.255.255.128
      no shutdown
      description .
    int fa0/1
      ip address 192.168.150.33 255.255.255.252
      no shutdown
      description .
    int se0/0/0
      ip address 192.168.150.26 255.255.255.252
      clock rate 4000000
      bandwidth 4000
      no shutdown
      description .
    int se0/0/1
      ip address 192.168.150.22 255.255.255.252
      clock rate 64000
      bandwidth 64
      no shutdown
      description .
    exit
    router eigrp 38550
      network 192.168.150.32 0.0.0.3
      network 192.168.150.24 0.0.0.3
      network 192.168.150.20 0.0.0.3
      network 131.209.3.0 0.0.0.127
      passive-interface fa0/0
      no auto-summary
      exit
    exit
cop ru st
```

## Servernet

```PacketTracer
enable
  conf t
    ip domain-lookup
    ip name-server 131.209.3.190
    hostname Servernet
    int Gig0/0
      ip address 192.168.150.30 255.255.255.252
      no shutdown
      description .
    int Gig0/1
      ip address 192.168.150.34 255.255.255.252
      no shutdown
      description .
    int Gig0/2
      ip address 131.209.3.129 255.255.255.192
      no shutdown
      description .
    exit
    router eigrp 38550
      network 192.168.150.28 0.0.0.3
      network 192.168.150.32 0.0.0.3
      network 131.209.3.128 0.0.0.63
      passive-interface Gig0/1
      no auto-summary
      exit
    exit
cop ru st
```

## Notizen

```PacketTracer
Router IP                     Dec mask          Interface   Network         Default Gateway

HQ:    131.209.0.1             255.255.254.0     Fa0/0       131.209.0.0
HQ:    192.168.150.25          255.255.255.252   Se0/0/0     192.168.150.24
HQ:    192.168.150.29          255.255.255.252   Se0/0/1     192.168.150.28

B1:    131.209.2.1             255.255.255.0     Fa0/0       131.209.2.0
B1:    192.168.150.21          255.255.255.252   Fa0/1       192.168.150.20
B1:    192.168.150.26          255.255.255.252   Se0/0/0     192.168.150.24
B1:    192.168.150.17          255.255.255.252   Se0/0/1     192.168.150.16

B2:    131.209.3.1             255.255.255.128   Fa0/0       131.209.3.0
B2:    192.168.150.33          255.255.255.252   Fa0/1       192.168.150.32
B2:    192.168.150.18          255.255.255.252   Se0/0/0     192.168.150.16
B2:    192.168.150.30          255.255.255.252   Se0/0/1     192.168.150.28

SN:    131.209.3.129           255.255.255.192   Gig0/2      131.209.3.128
SN:    192.168.150.22          255.255.255.252   Gig0/0      192.168.150.20
SN:    192.168.150.34          255.255.255.252   Gig0/1      192.168.150.32

PC1:   131.209.2.254           255.255.255.0                 131.209.2.0      131.209.2.1
PC2:   131.209.1.254           255.255.254.0                 131.209.0.0      131.209.0.1
PC3:   131.209.3.126           255.255.255.128               131.209.3.0      131.209.3.1
SN:    131.209.3.190           255.255.255.192               131.209.3.128    131.209.3.129
```

## FD & FS

```PacketTracer
Successor: via 192.168.173.26 (1154560/28160), Serial0/0/1
Feasible Successor: No
Feasible Distance: 1154560
Default Route: D*EX 0.0.0.0/0 [170/31001601] via 192.168.173.29, 00:34:34, GigabitEthernet0/0
```