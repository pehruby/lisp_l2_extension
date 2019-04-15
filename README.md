# Cloud layer 2 extension using LISP

A VIRL lab based on an example described [here](https://www.cisco.com/c/en/us/products/collateral/routers/cloud-services-router-1000v-series/white-paper-c11-731872.html)

Another example is [here](https://www.cisco.com/c/en/us/td/docs/routers/csr1000/software/aws/b_csraws/configure-lisp-layer2-extension.html)

The lab expects you are using [virlutils](https://github.com/CiscoDevNet/virlutils) to manage your VIRL simulations.

## Setup

Clone the repository

Edit .virlrc according to your VIRL setup

```text
VIRL_HOST=xxxxxx
VIRL_USERNAME=guest
VIRL_PASSWORD=guest
```

Spin up the simulation

```text
virl up
```

cisco is used as an username and password to all devices

## Lab schema

![alt text](../master/doc/schema.png "Topology")

## Reachability tests

Check remote node reachability

```text
cisco@lxc-1:~$ ping 10.0.5.253
PING 10.0.5.253 (10.0.5.253) 56(84) bytes of data.
64 bytes from 10.0.5.253: icmp_seq=1 ttl=62 time=16.4 ms
64 bytes from 10.0.5.253: icmp_seq=2 ttl=62 time=14.7 ms
64 bytes from 10.0.5.253: icmp_seq=3 ttl=62 time=17.6 ms

cisco@lxc-1:~$ ping 10.0.6.253
PING 10.0.6.253 (10.0.6.253) 56(84) bytes of data.
64 bytes from 10.0.6.253: icmp_seq=4 ttl=62 time=40.9 ms
64 bytes from 10.0.6.253: icmp_seq=5 ttl=62 time=25.5 ms
64 bytes from 10.0.6.253: icmp_seq=6 ttl=62 time=15.8 ms
64 bytes from 10.0.6.253: icmp_seq=7 ttl=62 time=23.7 ms
```

## Useful commands

```text
csr_ent#show ip lisp database
LISP ETR IPv4 Mapping Database for EID-table default (IID 0), LSBs: 0x1
Entries total 4, no-route 0, inactive 0

10.0.5.1/32, dynamic-eid LISP1, inherited from default locator-set DC1
  Locator      Pri/Wgt  Source     State
  192.168.6.6    1/100  cfg-addr   site-self, reachable
10.0.5.199/32, dynamic-eid LISP1, inherited from default locator-set DC1
  Locator      Pri/Wgt  Source     State
  192.168.6.6    1/100  cfg-addr   site-self, reachable
10.0.6.1/32, dynamic-eid LISP2, inherited from default locator-set DC1
  Locator      Pri/Wgt  Source     State
  192.168.6.6    1/100  cfg-addr   site-self, reachable
10.0.6.199/32, dynamic-eid LISP2, inherited from default locator-set DC1
  Locator      Pri/Wgt  Source     State
  192.168.6.6    1/100  cfg-addr   site-self, reachable
```

```text
csr_cloud#show ip lisp database
LISP ETR IPv4 Mapping Database for EID-table default (IID 0), LSBs: 0x1
Entries total 2, no-route 0, inactive 0

10.0.5.253/32, dynamic-eid LISP1, inherited from default locator-set CLOUD
  Locator      Pri/Wgt  Source     State
  192.168.7.7    1/100  cfg-addr   site-self, reachable
10.0.6.253/32, dynamic-eid LISP2, inherited from default locator-set CLOUD
  Locator      Pri/Wgt  Source     State
  192.168.7.7    1/100  cfg-addr   site-self, reachable
```

```text
csr_ent#show ip lisp map-cache
LISP IPv4 Mapping Cache for EID-table default (IID 0), 4 entries

10.0.5.0/24, uptime: 1d14h, expires: never, via dynamic-EID, send-map-request
  Negative cache entry, action: send-map-request
10.0.5.253/32, uptime: 1d14h, expires: 07:17:21, via map-reply, complete
  Locator      Uptime    State      Pri/Wgt     Encap-IID
  192.168.7.7  1d14h     up           1/100       -
10.0.6.0/24, uptime: 16:53:55, expires: never, via dynamic-EID, send-map-request
  Negative cache entry, action: send-map-request
10.0.6.253/32, uptime: 16:26:50, expires: 07:33:09, via map-reply, complete
  Locator      Uptime    State      Pri/Wgt     Encap-IID
  192.168.7.7  16:26:50  up           1/100       -
```

```text
csr_cloud#sh ip lisp map-cache
LISP IPv4 Mapping Cache for EID-table default (IID 0), 5 entries

0.0.0.0/0, uptime: 1d14h, expires: never, via static-send-map-request
  Negative cache entry, action: send-map-request
10.0.5.0/24, uptime: 17:08:59, expires: never, via dynamic-EID, send-map-request
  Negative cache entry, action: send-map-request
10.0.5.199/32, uptime: 1d14h, expires: 09:46:41, via map-reply, complete
  Locator      Uptime    State      Pri/Wgt     Encap-IID
  192.168.6.6  1d14h     up           1/100       -
10.0.6.0/24, uptime: 16:56:47, expires: never, via dynamic-EID, send-map-request
  Negative cache entry, action: send-map-request
10.0.6.199/32, uptime: 16:44:00, expires: 07:16:17, via map-reply, complete
  Locator      Uptime    State      Pri/Wgt     Encap-IID
  192.168.6.6  16:44:00  up           1/100       -
```


```text
csr_ent#show lisp instance-id 0 ipv4
  Instance ID:                         0
  Router-lisp ID:                      0
  Locator table:                       default
  EID table:                           default
  Ingress Tunnel Router (ITR):         disabled
  Egress Tunnel Router (ETR):          enabled
  Proxy-ITR Router (PITR):             enabled RLOCs: 192.168.6.6
  Proxy-ETR Router (PETR):             enabled
  NAT-traversal Router (NAT-RTR):      disabled
  Mobility First-Hop Router:           disabled
  Map Server (MS):                     enabled
  Map Resolver (MR):                   enabled
  Delegated Database Tree (DDT):       disabled
  Site Registration Limit:             0
  Map-Request source:                  derived from EID destination
  ITR Map-Resolver(s):                 192.168.6.6
  ETR Map-Server(s):                   192.168.6.6 (1d14h)
  xTR-ID:                              0x763FE205-0xEC360B55-0x60286F90-0x80527996
  site-ID:                             unspecified
  ITR local RLOC (last resort):        192.168.6.6
  ITR Solicit Map Request (SMR):       accept and process
    Max SMRs per map-cache entry:      8 more specifics
    Multiple SMR suppression time:     20 secs
  ETR accept mapping data:             disabled, verify disabled
  ETR map-cache TTL:                   1d00h
  Locator Status Algorithms:
    RLOC-probe algorithm:              disabled
    RLOC-probe on route change:        N/A (periodic probing disabled)
    RLOC-probe on member change:       disabled
    LSB reports:                       process
    IPv4 RLOC minimum mask length:     /0
    IPv6 RLOC minimum mask length:     /0
  Map-cache:
    Static mappings configured:        0
    Map-cache size/limit:              4/1000
    Imported route count/limit:        0/1000
    Map-cache activity check period:   60 secs
    Map-cache FIB updates:             established
    Persistent map-cache:              disabled
  Database:
    Total database mapping size:       4
    static database size/limit:        0/5000
    dynamic database size/limit:       4/1000
    route-import database size/limit:  0/1000
    Inactive (deconfig/away) size:     0
  Encapsulation type:                  lisp
```

```text
csr_cloud#show lisp instance-id 0 ipv4
  Instance ID:                         0
  Router-lisp ID:                      0
  Locator table:                       default
  EID table:                           default
  Ingress Tunnel Router (ITR):         enabled
  Egress Tunnel Router (ETR):          enabled
  Proxy-ITR Router (PITR):             disabled
  Proxy-ETR Router (PETR):             disabled
  NAT-traversal Router (NAT-RTR):      disabled
  Mobility First-Hop Router:           disabled
  Map Server (MS):                     disabled
  Map Resolver (MR):                   disabled
  Delegated Database Tree (DDT):       disabled
  Site Registration Limit:             0
  Map-Request source:                  derived from EID destination
  ITR Map-Resolver(s):                 192.168.6.6
  ETR Map-Server(s):                   192.168.6.6 (16:40:25)
  xTR-ID:                              0xAFE29432-0x3D3E868F-0x5C47353D-0xB5BBAE81
  site-ID:                             unspecified
  ITR local RLOC (last resort):        192.168.7.7
  ITR use proxy ETR RLOC(s):           192.168.6.6
  ITR Solicit Map Request (SMR):       accept and process
    Max SMRs per map-cache entry:      8 more specifics
    Multiple SMR suppression time:     20 secs
  ETR accept mapping data:             disabled, verify disabled
  ETR map-cache TTL:                   1d00h
  Locator Status Algorithms:
    RLOC-probe algorithm:              disabled
    RLOC-probe on route change:        N/A (periodic probing disabled)
    RLOC-probe on member change:       disabled
    LSB reports:                       process
    IPv4 RLOC minimum mask length:     /0
    IPv6 RLOC minimum mask length:     /0
  Map-cache:
    Static mappings configured:        0
    Map-cache size/limit:              5/1000
    Imported route count/limit:        0/1000
    Map-cache activity check period:   60 secs
    Map-cache FIB updates:             established
    Persistent map-cache:              disabled
  Database:
    Total database mapping size:       2
    static database size/limit:        0/5000
    dynamic database size/limit:       2/1000
    route-import database size/limit:  0/1000
    Inactive (deconfig/away) size:     0
  Encapsulation type:                  lisp
```

```text
csr_ent#show lisp site
LISP Site Registration Information
* = Some locators are down or unreachable
# = Some registrations are sourced by reliable transport

Site Name      Last      Up     Who Last             Inst     EID Prefix
               Register         Registered           ID
DATA_CENTER    never     no     --                            10.0.5.0/24
               1d13h     yes#   192.168.6.6                   10.0.5.1/32
               1d14h     yes#   192.168.6.6                   10.0.5.199/32
               16:42:40  yes#   192.168.7.7                   10.0.5.253/32
               never     no     --                            10.0.6.0/24
               16:32:01  yes#   192.168.6.6                   10.0.6.1/32
               16:32:01  yes#   192.168.6.6                   10.0.6.199/32
               16:32:01  yes#   192.168.7.7                   10.0.6.253/32
```

```text
csr_ent#show lisp eid-table default dynamic-eid det
LISP Dynamic EID Information for VRF "default"

Dynamic-EID name: LISP1
  Database-mapping EID-prefix: 10.0.5.0/24, locator-set DC1
  Registering more-specific dynamic-EIDs
  Map-Server(s): none configured, use global Map-Server
  Site-based multicast Map-Notify group: 239.0.0.1
  Number of roaming dynamic-EIDs discovered: 2
  Last dynamic-EID discovered: 10.0.5.1, 1d13h ago
    10.0.5.1, GigabitEthernet2.5, uptime: 1d13h
      last activity: 00:00:56, discovered by: Packet Reception
    10.0.5.199, GigabitEthernet2.5, uptime: 1d14h
      last activity: 00:00:27, discovered by: Packet Reception

Dynamic-EID name: LISP2
  Database-mapping EID-prefix: 10.0.6.0/24, locator-set DC1
  Registering more-specific dynamic-EIDs
  Map-Server(s): none configured, use global Map-Server
  Site-based multicast Map-Notify group: 239.0.0.2
  Number of roaming dynamic-EIDs discovered: 2
  Last dynamic-EID discovered: 10.0.6.1, 16:49:45 ago
    10.0.6.1, GigabitEthernet2.6, uptime: 16:49:45
      last activity: 00:00:51, discovered by: Packet Reception
    10.0.6.199, GigabitEthernet2.6, uptime: 16:49:46
      last activity: 00:00:11, discovered by: Packet Reception
```

```text
csr_cloud#show lisp eid-table default dynamic-eid det
LISP Dynamic EID Information for VRF "default"

Dynamic-EID name: LISP1
  Database-mapping EID-prefix: 10.0.5.0/24, locator-set CLOUD
  Registering more-specific dynamic-EIDs
  Map-Server(s): none configured, use global Map-Server
  Site-based multicast Map-Notify group: 239.0.0.1
  Number of roaming dynamic-EIDs discovered: 1
  Last dynamic-EID discovered: 10.0.5.253, 16:46:43 ago
    10.0.5.253, GigabitEthernet3.5, uptime: 16:46:43
      last activity: 00:00:51, discovered by: Packet Reception

Dynamic-EID name: LISP2
  Database-mapping EID-prefix: 10.0.6.0/24, locator-set CLOUD
  Registering more-specific dynamic-EIDs
  Map-Server(s): none configured, use global Map-Server
  Site-based multicast Map-Notify group: 239.0.0.2
  Number of roaming dynamic-EIDs discovered: 1
  Last dynamic-EID discovered: 10.0.6.253, 16:46:43 ago
    10.0.6.253, GigabitEthernet3.6, uptime: 16:46:43
      last activity: 00:00:45, discovered by: Packet Reception
```
