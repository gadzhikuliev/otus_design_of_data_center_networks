## eBGP

### Цели:
- исследовать построение Underlay сети с использованием eBGP

### Описание выполнения лабораторной работы:
- выбрать номера автономных систем (AS):
    - для SPINE1 и SPINE2 - AS 65001
    - для LEAF1, LEAF2 и LEAF2 - AS 65002, 65003 и 65004 соответственно
- включить на устройствах протокол BFD
- настроить на устройствах интерфейсы Loopback 0
- включить протокол BGP и настроить его, Router ID назначить, основываясь на IP-адресе интерфейса Loopback 0
- анонсировать в BGP IP-адреса Loopback 0

### Физическая схема сети:
![Схема](Network_topology_with_ebgp.jpg)

#### <u>Таблица интерфейсов и адресации, участвующих в eBGP:</u>

|Device|Interface|IP Address|Subnet Mask|
|:-:|:-:|:-:|:-:|
|LEAF 1|Ethernet 1/1|10.0.0.1|255.255.255.252|
|SPINE 1|Ethernet 1/1|10.0.0.2|255.255.255.252|
|LEAF 1|Ethernet 1/2|10.0.0.5|255.255.255.252|
|SPINE 2|Ethernet 1/1|10.0.0.6|255.255.255.252|
|LEAF 2|Ethernet 1/1|10.0.0.9|255.255.255.252|
|SPINE 1|Ethernet 1/2|10.0.0.10|255.255.255.252|
|LEAF 2|Ethernet 1/2|10.0.0.13|255.255.255.252|
|SPINE 2|Ethernet 1/2|10.0.0.14|255.255.255.252|
|LEAF 3|Ethernet 1/1|10.0.0.17|255.255.255.252|
|SPINE 1|Ethernet1/3|10.0.0.18|255.255.255.252|
|LEAF 3|Ethernet 1/2|10.0.0.21|255.255.255.252|
|SPINE 2|Ethernet 1/3|10.0.0.22|255.255.255.252|

#### <u>Сети, анонсируемые в eBGP:</u>

|Device|Loopback 0|Subnet Mask|
|:-:|:-:|:-:|
|SPINE1|172.31.10.10|255.255.255.255|
|SPINE2|172.31.20.20|255.255.255.255|
|LEAF1|172.31.30.30|255.255.255.255|
|LEAF2|172.31.40.40|255.255.255.255|
|LEAF3|172.31.50.50|255.255.255.255|

### Необходимые настройки на оборудовании:

#### <u>Настройка SPINE1:</u>
```
feature bgp
feature bfd
router bgp 65001
  router-id 172.31.10.10
  bestpath as-path multipath-relax
  maxas-limit 4
  address-family ipv4 unicast
    network 172.31.10.10/32
  neighbor 10.0.0.1
    bfd
    remote-as 65002
    address-family ipv4 unicast
  neighbor 10.0.0.9
    bfd
    remote-as 65003
    address-family ipv4 unicast
  neighbor 10.0.0.17
    bfd
    remote-as 65004
    address-family ipv4 unicast
```
#### <u>Настройка SPINE2:</u>
```
feature bgp
feature bfd
router bgp 65001
  router-id 172.31.20.20
  bestpath as-path multipath-relax
  maxas-limit 4
  address-family ipv4 unicast
    network 172.31.20.20/32
  neighbor 10.0.0.5
    bfd
    remote-as 65002
    address-family ipv4 unicast
  neighbor 10.0.0.13
    bfd
    remote-as 65003
    address-family ipv4 unicast
  neighbor 10.0.0.21
    bfd
    remote-as 65004
    address-family ipv4 unicast
```
#### <u>Настройка LEAF1:</u>
```
feature bgp
feature bfd
router bgp 65002
  router-id 172.31.30.30
  bestpath as-path multipath-relax
  maxas-limit 4
  address-family ipv4 unicast
    network 172.31.30.30/32
  neighbor 10.0.0.2
    bfd
    remote-as 65001
    address-family ipv4 unicast
  neighbor 10.0.0.6
    bfd
    remote-as 65001
    address-family ipv4 unicast
```
#### <u>Настройка LEAF2:</u>
```
feature bgp
feature bfd
router bgp 65003
  router-id 172.31.40.40
  bestpath as-path multipath-relax
  maxas-limit 4
  address-family ipv4 unicast
    network 172.31.40.40/32
  neighbor 10.0.0.10
    bfd
    remote-as 65001
    address-family ipv4 unicast
  neighbor 10.0.0.14
    bfd
    remote-as 65001
    address-family ipv4 unicast
```
#### <u>Настройка LEAF3:</u>
```
feature bgp
feature bfd
router bgp 65004
  router-id 172.31.50.50
  bestpath as-path multipath-relax
  maxas-limit 4
  address-family ipv4 unicast
    network 172.31.50.50/32
  neighbor 10.0.0.18
    bfd
    remote-as 65001
    address-family ipv4 unicast
  neighbor 10.0.0.22
    bfd
    remote-as 65001
    address-family ipv4 unicast
```
### Проверка работоспособности eBGP в сети. Проверяем таблицу маршрутизации и общие сведения о работающем протоколе:

<details>
<summary>Проверка на SPINE1</summary>

```
SPINE1# sh ip bgp summary 
BGP summary information for VRF default, address family IPv4 Unicast
BGP router identifier 172.31.10.10, local AS number 65001
BGP table version is 9, IPv4 Unicast config peers 3, capable peers 3
4 network entries and 4 paths using 976 bytes of memory
BGP attribute entries [4/688], BGP AS path entries [3/18]
BGP community entries [0/0], BGP clusterlist entries [0/0]

Neighbor        V    AS MsgRcvd MsgSent   TblVer  InQ OutQ Up/Down  State/PfxRcd
10.0.0.1        4 65002      13      12        9    0    0 00:06:35 1         
10.0.0.9        4 65003      11      10        9    0    0 00:04:04 1         
10.0.0.17       4 65004       9       8        9    0    0 00:02:03 1    

SPINE1# sh ip bgp 
BGP routing table information for VRF default, address family IPv4 Unicast
BGP table version is 9, Local Router ID is 172.31.10.10
Status: s-suppressed, x-deleted, S-stale, d-dampened, h-history, *-valid, >-best
Path type: i-internal, e-external, c-confed, l-local, a-aggregate, r-redist, I-injected
Origin codes: i - IGP, e - EGP, ? - incomplete, | - multipath, & - backup, 2 - best2

   Network            Next Hop            Metric     LocPrf     Weight Path
*>l172.31.10.10/32    0.0.0.0                           100      32768 i
*>e172.31.30.30/32    10.0.0.1                                       0 65002 i
*>e172.31.40.40/32    10.0.0.9                                       0 65003 i
*>e172.31.50.50/32    10.0.0.17                                      0 65004 i

SPINE1# sh ip route bgp-65001 
IP Route Table for VRF "default"
'*' denotes best ucast next-hop
'**' denotes best mcast next-hop
'[x/y]' denotes [preference/metric]
'%<string>' in via output denotes VRF <string>

172.31.30.30/32, ubest/mbest: 1/0
    *via 10.0.0.1, [20/0], 00:09:13, bgp-65001, external, tag 65002
172.31.40.40/32, ubest/mbest: 1/0
    *via 10.0.0.9, [20/0], 00:06:42, bgp-65001, external, tag 65003
172.31.50.50/32, ubest/mbest: 1/0
    *via 10.0.0.17, [20/0], 00:04:41, bgp-65001, external, tag 65004
```
</details>
<details>
<summary>Проверка на SPINE2</summary>

```
SPINE2# sh ip bgp summary 
BGP summary information for VRF default, address family IPv4 Unicast
BGP router identifier 172.31.20.20, local AS number 65001
BGP table version is 9, IPv4 Unicast config peers 3, capable peers 3
4 network entries and 4 paths using 976 bytes of memory
BGP attribute entries [4/688], BGP AS path entries [3/18]
BGP community entries [0/0], BGP clusterlist entries [0/0]

Neighbor        V    AS MsgRcvd MsgSent   TblVer  InQ OutQ Up/Down  State/PfxRcd
10.0.0.5        4 65002      16      15        9    0    0 00:09:38 1         
10.0.0.13       4 65003      14      13        9    0    0 00:07:10 1         
10.0.0.21       4 65004      12      11        9    0    0 00:05:08 1  

SPINE2# sh ip bgp 
BGP routing table information for VRF default, address family IPv4 Unicast
BGP table version is 9, Local Router ID is 172.31.20.20
Status: s-suppressed, x-deleted, S-stale, d-dampened, h-history, *-valid, >-best
Path type: i-internal, e-external, c-confed, l-local, a-aggregate, r-redist, I-injected
Origin codes: i - IGP, e - EGP, ? - incomplete, | - multipath, & - backup, 2 - best2

   Network            Next Hop            Metric     LocPrf     Weight Path
*>l172.31.20.20/32    0.0.0.0                           100      32768 i
*>e172.31.30.30/32    10.0.0.5                                       0 65002 i
*>e172.31.40.40/32    10.0.0.13                                      0 65003 i
*>e172.31.50.50/32    10.0.0.21                                      0 65004 i

SPINE2# sh ip route bgp
IP Route Table for VRF "default"
'*' denotes best ucast next-hop
'**' denotes best mcast next-hop
'[x/y]' denotes [preference/metric]
'%<string>' in via output denotes VRF <string>

172.31.30.30/32, ubest/mbest: 1/0
    *via 10.0.0.5, [20/0], 00:10:38, bgp-65001, external, tag 65002
172.31.40.40/32, ubest/mbest: 1/0
    *via 10.0.0.13, [20/0], 00:08:10, bgp-65001, external, tag 65003
172.31.50.50/32, ubest/mbest: 1/0
    *via 10.0.0.21, [20/0], 00:06:08, bgp-65001, external, tag 65004
```
</details>
<details>
<summary>Проверка на LEAF1</summary>

```
LEAF1# sh ip bgp summary 
BGP summary information for VRF default, address family IPv4 Unicast
BGP router identifier 172.31.30.30, local AS number 65002
BGP table version is 11, IPv4 Unicast config peers 2, capable peers 2
5 network entries and 7 paths using 1460 bytes of memory
BGP attribute entries [4/688], BGP AS path entries [3/26]
BGP community entries [0/0], BGP clusterlist entries [0/0]

Neighbor        V    AS MsgRcvd MsgSent   TblVer  InQ OutQ Up/Down  State/PfxRcd
10.0.0.2        4 65001      20      17       11    0    0 00:11:19 3         
10.0.0.6        4 65001      19      17       11    0    0 00:11:07 3  

LEAF1# sh ip bgp 
BGP routing table information for VRF default, address family IPv4 Unicast
BGP table version is 11, Local Router ID is 172.31.30.30
Status: s-suppressed, x-deleted, S-stale, d-dampened, h-history, *-valid, >-best
Path type: i-internal, e-external, c-confed, l-local, a-aggregate, r-redist, I-injected
Origin codes: i - IGP, e - EGP, ? - incomplete, | - multipath, & - backup, 2 - best2

   Network            Next Hop            Metric     LocPrf     Weight Path
*>e172.31.10.10/32    10.0.0.2                                       0 65001 i
*>e172.31.20.20/32    10.0.0.6                                       0 65001 i
*>l172.31.30.30/32    0.0.0.0                           100      32768 i
* e172.31.40.40/32    10.0.0.6                                       0 65001 65003 i
*>e                   10.0.0.2                                       0 65001 65003 i
* e172.31.50.50/32    10.0.0.6                                       0 65001 65004 i
*>e                   10.0.0.2                                       0 65001 65004 i

LEAF1# sh ip route bgp-65002 
IP Route Table for VRF "default"
'*' denotes best ucast next-hop
'**' denotes best mcast next-hop
'[x/y]' denotes [preference/metric]
'%<string>' in via output denotes VRF <string>

172.31.10.10/32, ubest/mbest: 1/0
    *via 10.0.0.2, [20/0], 00:12:14, bgp-65002, external, tag 65001
172.31.20.20/32, ubest/mbest: 1/0
    *via 10.0.0.6, [20/0], 00:12:02, bgp-65002, external, tag 65001
172.31.40.40/32, ubest/mbest: 1/0
    *via 10.0.0.2, [20/0], 00:09:44, bgp-65002, external, tag 65001
172.31.50.50/32, ubest/mbest: 1/0
    *via 10.0.0.2, [20/0], 00:07:42, bgp-65002, external, tag 65001
```
</details>
<details>
<summary>Проверка на LEAF2</summary>

```
LEAF2# sh ip bgp summary 
BGP summary information for VRF default, address family IPv4 Unicast
BGP router identifier 172.31.40.40, local AS number 65003
BGP table version is 11, IPv4 Unicast config peers 2, capable peers 2
5 network entries and 7 paths using 1460 bytes of memory
BGP attribute entries [4/688], BGP AS path entries [3/26]
BGP community entries [0/0], BGP clusterlist entries [0/0]

Neighbor        V    AS MsgRcvd MsgSent   TblVer  InQ OutQ Up/Down  State/PfxRcd
10.0.0.10       4 65001      19      16       11    0    0 00:10:40 3         
10.0.0.14       4 65001      19      16       11    0    0 00:10:30 3  

LEAF2# sh ip bgp 
BGP routing table information for VRF default, address family IPv4 Unicast
BGP table version is 11, Local Router ID is 172.31.40.40
Status: s-suppressed, x-deleted, S-stale, d-dampened, h-history, *-valid, >-best
Path type: i-internal, e-external, c-confed, l-local, a-aggregate, r-redist, I-injected
Origin codes: i - IGP, e - EGP, ? - incomplete, | - multipath, & - backup, 2 - best2

   Network            Next Hop            Metric     LocPrf     Weight Path
*>e172.31.10.10/32    10.0.0.10                                      0 65001 i
*>e172.31.20.20/32    10.0.0.14                                      0 65001 i
* e172.31.30.30/32    10.0.0.14                                      0 65001 65002 i
*>e                   10.0.0.10                                      0 65001 65002 i
*>l172.31.40.40/32    0.0.0.0                           100      32768 i
* e172.31.50.50/32    10.0.0.14                                      0 65001 65004 i
*>e                   10.0.0.10                                      0 65001 65004 i

LEAF2# sh ip route bgp-65003 
IP Route Table for VRF "default"
'*' denotes best ucast next-hop
'**' denotes best mcast next-hop
'[x/y]' denotes [preference/metric]
'%<string>' in via output denotes VRF <string>

172.31.10.10/32, ubest/mbest: 1/0
    *via 10.0.0.10, [20/0], 00:11:17, bgp-65003, external, tag 65001
172.31.20.20/32, ubest/mbest: 1/0
    *via 10.0.0.14, [20/0], 00:11:07, bgp-65003, external, tag 65001
172.31.30.30/32, ubest/mbest: 1/0
    *via 10.0.0.10, [20/0], 00:11:17, bgp-65003, external, tag 65001
172.31.50.50/32, ubest/mbest: 1/0
    *via 10.0.0.10, [20/0], 00:09:16, bgp-65003, external, tag 65001
```
</details>
<details>
<summary>Проверка на LEAF3</summary>

```
LEAF3# sh ip bgp summary 
BGP summary information for VRF default, address family IPv4 Unicast
BGP router identifier 172.31.50.50, local AS number 65004
BGP table version is 11, IPv4 Unicast config peers 2, capable peers 2
5 network entries and 7 paths using 1460 bytes of memory
BGP attribute entries [4/688], BGP AS path entries [3/26]
BGP community entries [0/0], BGP clusterlist entries [0/0]

Neighbor        V    AS MsgRcvd MsgSent   TblVer  InQ OutQ Up/Down  State/PfxRcd
10.0.0.18       4 65001      21      18       11    0    0 00:12:19 3         
10.0.0.22       4 65001      21      18       11    0    0 00:12:08 3 

LEAF3# sh ip bgp
BGP routing table information for VRF default, address family IPv4 Unicast
BGP table version is 11, Local Router ID is 172.31.50.50
Status: s-suppressed, x-deleted, S-stale, d-dampened, h-history, *-valid, >-best
Path type: i-internal, e-external, c-confed, l-local, a-aggregate, r-redist, I-injected
Origin codes: i - IGP, e - EGP, ? - incomplete, | - multipath, & - backup, 2 - best2

   Network            Next Hop            Metric     LocPrf     Weight Path
*>e172.31.10.10/32    10.0.0.18                                      0 65001 i
*>e172.31.20.20/32    10.0.0.22                                      0 65001 i
* e172.31.30.30/32    10.0.0.22                                      0 65001 65002 i
*>e                   10.0.0.18                                      0 65001 65002 i
* e172.31.40.40/32    10.0.0.22                                      0 65001 65003 i
*>e                   10.0.0.18                                      0 65001 65003 i
*>l172.31.50.50/32    0.0.0.0                           100      32768 i

LEAF3# sh ip route bgp-65004 
IP Route Table for VRF "default"
'*' denotes best ucast next-hop
'**' denotes best mcast next-hop
'[x/y]' denotes [preference/metric]
'%<string>' in via output denotes VRF <string>

172.31.10.10/32, ubest/mbest: 1/0
    *via 10.0.0.18, [20/0], 00:12:52, bgp-65004, external, tag 65001
172.31.20.20/32, ubest/mbest: 1/0
    *via 10.0.0.22, [20/0], 00:12:41, bgp-65004, external, tag 65001
172.31.30.30/32, ubest/mbest: 1/0
    *via 10.0.0.18, [20/0], 00:12:52, bgp-65004, external, tag 65001
172.31.40.40/32, ubest/mbest: 1/0
    *via 10.0.0.18, [20/0], 00:12:52, bgp-65004, external, tag 65001
```
</details>

Вывод команд свидетельствует, что все L3-коммутаторы установили соседство по eBGP и обмениваются маршрутной информацией.