# CCIE Service Provider Task 2 - BGP Foundation Setup
## iBGP Route Reflectors and Inter-AS eBGP Connectivity

### CCIE SP v5.1 Blueprint Coverage

**Section 1.2 - Border Gateway Protocol (25% weighting):**
- 1.2.a iBGP, eBGP, and MP-BGP
- 1.2.b BGP route policy enforcement  
- 1.2.c BGP path attribute
- 1.2.d BGP scale and performance
- 1.2.e BGP labeled unicast and linked state

### Prerequisites

- Task 1 Completed: IGP foundation (ISIS for AS 65100, OSPF for AS 65200)
- Loopback Reachability: All 100.0.0.x addresses reachable within each AS
- MPLS Labels: SR Node SIDs available for next-hop resolution

### Overview and Design

This task establishes the BGP foundation for our dual-AS service provider network:

**BGP Session Types Implemented:**
- iBGP with Route Reflectors: Scalable iBGP within each AS
- eBGP Inter-AS: Connectivity between AS 65100 and AS 65200  
- BGP-LU (Labeled Unicast): MPLS label distribution for Inter-AS services

**Authentication**: All BGP sessions use password "dickie-lab"

### BGP Architecture Design

#### AS 65100 (X-Domain) - Dual Route Reflector Design
- Primary RR: X-RR1 (100.0.0.25)
- Secondary RR: X-RR2 (100.0.0.26) 
- RR-to-RR: Non-client peering for redundancy
- Clients: X-PE1, X-PE2, X-ASBR1, X-ASBR2

#### AS 65200 (Y-Domain) - Single Route Reflector Design  
- Primary RR: Y-RR1 (100.0.0.38)
- Clients: Y-PE1, Y-PE2, Y-ASBR1, Y-ASBR2

#### Inter-AS eBGP Sessions
- X-ASBR1 to Y-ASBR1: 10.1.101.0/24 link
- X-ASBR2 to Y-ASBR2: 10.1.102.0/24 link  
- Redundancy: Dual ASBR design for high availability

---

## Configuration Tasks

### Task 2.1: iBGP Route Reflector Setup - AS 65100

#### Task 2.1.1: Configure X-RR1 (Primary Route Reflector)

**Device: X-RR1 (100.0.0.25) - IOS-XR**

```
!! BGP Route Reflector Configuration
hostname X-RR1

!! Route Policy Definitions
route-policy PASS
  pass
end-policy

route-policy DENY
  drop
end-policy

!! BGP Configuration - Primary Route Reflector
router bgp 65100
 bgp router-id 100.0.0.25
 bgp cluster-id 100.0.0.25
 bgp log neighbor changes detail
 bgp graceful-restart
 bgp update-delay 300
 !
 !! IPv4 Unicast Address Family
 address-family ipv4 unicast
  additional-paths receive
  additional-paths send
  additional-paths selection route-policy PASS
  maximum-paths ebgp 64
  maximum-paths ibgp 64
  redistribute connected route-policy PASS
  allocate-label all
 !
 !! IPv6 Unicast Address Family
 address-family ipv6 unicast
  additional-paths receive
  additional-paths send
  additional-paths selection route-policy PASS
  maximum-paths ebgp 64
  maximum-paths ibgp 64
  redistribute connected route-policy PASS
  allocate-label all
 !
 !! VPNv4 Unicast Address Family
 address-family vpnv4 unicast
  retain route-target all
 !
 !! VPNv6 Unicast Address Family  
 address-family vpnv6 unicast
  retain route-target all
 !
 !! L2VPN EVPN Address Family
 address-family l2vpn evpn
  retain route-target all
 !
 !! Neighbor to X-RR2 (Non-Client)
 neighbor 100.0.0.26
  remote-as 65100
  description "iBGP to X-RR2 (Secondary RR)"
  password clear dickie-lab
  update-source Loopback0
  address-family ipv4 unicast
   route-policy PASS in
   route-policy PASS out
   next-hop-self
  !
  address-family ipv6 unicast
   route-policy PASS in
   route-policy PASS out
   next-hop-self
  !
  address-family vpnv4 unicast
   route-policy PASS in
   route-policy PASS out
   next-hop-self
  !
  address-family vpnv6 unicast
   route-policy PASS in
   route-policy PASS out
   next-hop-self
  !
  address-family l2vpn evpn
   route-policy PASS in
   route-policy PASS out
  !
 !
 !! Neighbor to X-PE1 (Route Reflector Client)
 neighbor 100.0.0.21
  remote-as 65100
  description "iBGP to X-PE1 (RR Client)"
  password clear dickie-lab
  update-source Loopback0
  address-family ipv4 unicast
   route-reflector-client
   route-policy PASS in
   route-policy PASS out
   next-hop-self
  !
  address-family ipv6 unicast
   route-reflector-client
   route-policy PASS in
   route-policy PASS out
   next-hop-self
  !
  address-family vpnv4 unicast
   route-reflector-client
   route-policy PASS in
   route-policy PASS out
  !
  address-family vpnv6 unicast
   route-reflector-client
   route-policy PASS in
   route-policy PASS out
  !
  address-family l2vpn evpn
   route-reflector-client
   route-policy PASS in
   route-policy PASS out
  !
 !
 !! Neighbor to X-PE2 (Route Reflector Client)
 neighbor 100.0.0.22
  remote-as 65100
  description "iBGP to X-PE2 (RR Client)"
  password clear dickie-lab
  update-source Loopback0
  address-family ipv4 unicast
   route-reflector-client
   route-policy PASS in
   route-policy PASS out
   next-hop-self
  !
  address-family ipv6 unicast
   route-reflector-client
   route-policy PASS in
   route-policy PASS out
   next-hop-self
  !
  address-family vpnv4 unicast
   route-reflector-client
   route-policy PASS in
   route-policy PASS out
  !
  address-family vpnv6 unicast
   route-reflector-client
   route-policy PASS in
   route-policy PASS out
  !
  address-family l2vpn evpn
   route-reflector-client
   route-policy PASS in
   route-policy PASS out
  !
 !
 !! Neighbor to X-ASBR1 (Route Reflector Client)
 neighbor 100.0.0.31
  remote-as 65100
  description "iBGP to X-ASBR1 (RR Client)"
  password clear dickie-lab
  update-source Loopback0
  address-family ipv4 unicast
   route-reflector-client
   route-policy PASS in
   route-policy PASS out
  !
  address-family ipv6 unicast
   route-reflector-client
   route-policy PASS in
   route-policy PASS out
  !
  address-family vpnv4 unicast
   route-reflector-client
   route-policy PASS in
   route-policy PASS out
  !
  address-family vpnv6 unicast
   route-reflector-client
   route-policy PASS in
   route-policy PASS out
  !
 !
 !! Neighbor to X-ASBR2 (Route Reflector Client)
 neighbor 100.0.0.32
  remote-as 65100
  description "iBGP to X-ASBR2 (RR Client)"
  password clear dickie-lab
  update-source Loopback0
  address-family ipv4 unicast
   route-reflector-client
   route-policy PASS in
   route-policy PASS out
  !
  address-family ipv6 unicast
   route-reflector-client
   route-policy PASS in
   route-policy PASS out
  !
  address-family vpnv4 unicast
   route-reflector-client
   route-policy PASS in
   route-policy PASS out
  !
  address-family vpnv6 unicast
   route-reflector-client
   route-policy PASS in
   route-policy PASS out
  !
 !
!

commit
```

#### Task 2.1.2: Configure X-RR2 (Secondary Route Reflector)

**Device: X-RR2 (100.0.0.26) - IOS-XR**

```
!! BGP Route Reflector Configuration
hostname X-RR2

route-policy PASS
  pass
end-policy

route-policy DENY
  drop
end-policy

!! BGP Configuration - Secondary Route Reflector
router bgp 65100
 bgp router-id 100.0.0.26
 bgp cluster-id 100.0.0.25
 bgp log neighbor changes detail
 bgp graceful-restart
 bgp update-delay 300
 !
 address-family ipv4 unicast
  additional-paths receive
  additional-paths send
  additional-paths selection route-policy PASS
  maximum-paths ebgp 64
  maximum-paths ibgp 64
  redistribute connected route-policy PASS
  allocate-label all
 !
 address-family ipv6 unicast
  additional-paths receive
  additional-paths send
  additional-paths selection route-policy PASS
  maximum-paths ebgp 64
  maximum-paths ibgp 64
  redistribute connected route-policy PASS
  allocate-label all
 !
 address-family vpnv4 unicast
  retain route-target all
 !
 address-family vpnv6 unicast
  retain route-target all
 !
 address-family l2vpn evpn
  retain route-target all
 !
 !! Neighbor to X-RR1 (Non-Client)
 neighbor 100.0.0.25
  remote-as 65100
  description "iBGP to X-RR1 (Primary RR)"
  password clear dickie-lab
  update-source Loopback0
  address-family ipv4 unicast
   route-policy PASS in
   route-policy PASS out
   next-hop-self
  !
  address-family ipv6 unicast
   route-policy PASS in
   route-policy PASS out
   next-hop-self
  !
  address-family vpnv4 unicast
   route-policy PASS in
   route-policy PASS out
   next-hop-self
  !
  address-family vpnv6 unicast
   route-policy PASS in
   route-policy PASS out
   next-hop-self
  !
  address-family l2vpn evpn
   route-policy PASS in
   route-policy PASS out
  !
 !
 !! Configure same RR clients as X-RR1 (redundancy)
 !! [Similar neighbor configurations for PE1, PE2, ASBR1, ASBR2]
!

commit
```

#### Task 2.1.3: Configure X-PE1 (Route Reflector Client)

**Device: X-PE1 (100.0.0.21) - IOS-XR**

```
hostname X-PE1

route-policy PASS
  pass
end-policy

!! BGP Configuration - Route Reflector Client
router bgp 65100
 bgp router-id 100.0.0.21
 bgp log neighbor changes detail
 bgp graceful-restart
 !
 address-family ipv4 unicast
  additional-paths receive
  additional-paths send
  maximum-paths ebgp 64
  maximum-paths ibgp 64
  allocate-label all
 !
 address-family ipv6 unicast
  additional-paths receive
  additional-paths send
  maximum-paths ebgp 64
  maximum-paths ibgp 64
  allocate-label all
 !
 address-family vpnv4 unicast
 !
 address-family vpnv6 unicast
 !
 address-family l2vpn evpn
 !
 !! Neighbor to X-RR1 (Primary Route Reflector)
 neighbor 100.0.0.25
  remote-as 65100
  description "iBGP to X-RR1 (Primary RR)"
  password clear dickie-lab
  update-source Loopback0
  address-family ipv4 unicast
   route-policy PASS in
   route-policy PASS out
  !
  address-family ipv6 unicast
   route-policy PASS in
   route-policy PASS out
  !
  address-family vpnv4 unicast
   route-policy PASS in
   route-policy PASS out
  !
  address-family vpnv6 unicast
   route-policy PASS in
   route-policy PASS out
  !
  address-family l2vpn evpn
   route-policy PASS in
   route-policy PASS out
  !
 !
 !! Neighbor to X-RR2 (Secondary Route Reflector)
 neighbor 100.0.0.26
  remote-as 65100
  description "iBGP to X-RR2 (Secondary RR)"
  password clear dickie-lab
  update-source Loopback0
  address-family ipv4 unicast
   route-policy PASS in
   route-policy PASS out
  !
  address-family ipv6 unicast
   route-policy PASS in
   route-policy PASS out
  !
  address-family vpnv4 unicast
   route-policy PASS in
   route-policy PASS out
  !
  address-family vpnv6 unicast
   route-policy PASS in
   route-policy PASS out
  !
  address-family l2vpn evpn
   route-policy PASS in
   route-policy PASS out
  !
 !
!

commit
```

### Task 2.2: iBGP Route Reflector Setup - AS 65200

#### Task 2.2.1: Configure Y-RR1 (Single Route Reflector)

**Device: Y-RR1 (100.0.0.38) - IOS-XE**

```
!! BGP Route Reflector Configuration
hostname Y-RR1

!! BGP Configuration - Route Reflector
router bgp 65200
 bgp router-id 100.0.0.38
 bgp cluster-id 100.0.0.38
 bgp log-neighbor-changes
 bgp graceful-restart restart-time 300
 bgp graceful-restart stalepath-time 300
 !
 address-family ipv4
  bgp additional-paths receive
  bgp additional-paths send receive
  bgp additional-paths select all
  maximum-paths eibgp 64
  redistribute connected
  allocate-label all
 exit-address-family
 !
 address-family ipv6
  bgp additional-paths receive
  bgp additional-paths send receive
  bgp additional-paths select all
  maximum-paths eibgp 64
  redistribute connected
  allocate-label all
 exit-address-family
 !
 address-family vpnv4
  bgp additional-paths receive
  bgp additional-paths send receive
  retain route-target all
 exit-address-family
 !
 address-family vpnv6
  bgp additional-paths receive
  bgp additional-paths send receive
  retain route-target all
 exit-address-family
 !
 address-family l2vpn evpn
  bgp additional-paths receive
  bgp additional-paths send receive
  retain route-target all
 exit-address-family
 !
 !! Neighbor to Y-PE1 (Route Reflector Client)
 neighbor 100.0.0.36 remote-as 65200
 neighbor 100.0.0.36 description "iBGP to Y-PE1 (RR Client)"
 neighbor 100.0.0.36 password dickie-lab
 neighbor 100.0.0.36 update-source Loopback0
 neighbor 100.0.0.36 address-family ipv4
 neighbor 100.0.0.36 route-reflector-client
 neighbor 100.0.0.36 send-community both
 neighbor 100.0.0.36 next-hop-self
 neighbor 100.0.0.36 address-family ipv6
 neighbor 100.0.0.36 route-reflector-client
 neighbor 100.0.0.36 send-community both
 neighbor 100.0.0.36 next-hop-self
 neighbor 100.0.0.36 address-family vpnv4
 neighbor 100.0.0.36 route-reflector-client
 neighbor 100.0.0.36 send-community extended
 neighbor 100.0.0.36 address-family vpnv6
 neighbor 100.0.0.36 route-reflector-client
 neighbor 100.0.0.36 send-community extended
 neighbor 100.0.0.36 address-family l2vpn evpn
 neighbor 100.0.0.36 route-reflector-client
 neighbor 100.0.0.36 send-community both
 !
 !! Neighbor to Y-PE2 (Route Reflector Client)
 neighbor 100.0.0.37 remote-as 65200
 neighbor 100.0.0.37 description "iBGP to Y-PE2 (RR Client)"
 neighbor 100.0.0.37 password dickie-lab
 neighbor 100.0.0.37 update-source Loopback0
 neighbor 100.0.0.37 address-family ipv4
 neighbor 100.0.0.37 route-reflector-client
 neighbor 100.0.0.37 send-community both
 neighbor 100.0.0.37 next-hop-self
 neighbor 100.0.0.37 address-family ipv6
 neighbor 100.0.0.37 route-reflector-client
 neighbor 100.0.0.37 send-community both
 neighbor 100.0.0.37 next-hop-self
 neighbor 100.0.0.37 address-family vpnv4
 neighbor 100.0.0.37 route-reflector-client
 neighbor 100.0.0.37 send-community extended
 neighbor 100.0.0.37 address-family vpnv6
 neighbor 100.0.0.37 route-reflector-client
 neighbor 100.0.0.37 send-community extended
 neighbor 100.0.0.37 address-family l2vpn evpn
 neighbor 100.0.0.37 route-reflector-client
 neighbor 100.0.0.37 send-community both
 !
 !! Neighbor to Y-ASBR1 (Route Reflector Client)
 neighbor 100.0.0.33 remote-as 65200
 neighbor 100.0.0.33 description "iBGP to Y-ASBR1 (RR Client)"
 neighbor 100.0.0.33 password dickie-lab
 neighbor 100.0.0.33 update-source Loopback0
 neighbor 100.0.0.33 address-family ipv4
 neighbor 100.0.0.33 route-reflector-client
 neighbor 100.0.0.33 send-community both
 neighbor 100.0.0.33 address-family ipv6
 neighbor 100.0.0.33 route-reflector-client
 neighbor 100.0.0.33 send-community both
 neighbor 100.0.0.33 address-family vpnv4
 neighbor 100.0.0.33 route-reflector-client
 neighbor 100.0.0.33 send-community extended
 neighbor 100.0.0.33 address-family vpnv6
 neighbor 100.0.0.33 route-reflector-client
 neighbor 100.0.0.33 send-community extended
 !
 !! Neighbor to Y-ASBR2 (Route Reflector Client)
 neighbor 100.0.0.34 remote-as 65200
 neighbor 100.0.0.34 description "iBGP to Y-ASBR2 (RR Client)"
 neighbor 100.0.0.34 password dickie-lab
 neighbor 100.0.0.34 update-source Loopback0
 neighbor 100.0.0.34 address-family ipv4
 neighbor 100.0.0.34 route-reflector-client
 neighbor 100.0.0.34 send-community both
 neighbor 100.0.0.34 address-family ipv6
 neighbor 100.0.0.34 route-reflector-client
 neighbor 100.0.0.34 send-community both
 neighbor 100.0.0.34 address-family vpnv4
 neighbor 100.0.0.34 route-reflector-client
 neighbor 100.0.0.34 send-community extended
 neighbor 100.0.0.34 address-family vpnv6
 neighbor 100.0.0.34 route-reflector-client
 neighbor 100.0.0.34 send-community extended
```

### Task 2.3: Inter-AS eBGP Configuration

#### Task 2.3.1: Configure X-ASBR1 eBGP to Y-ASBR1

**Device: X-ASBR1 (100.0.0.31) - IOS-XR**

```
hostname X-ASBR1

!! Route Policies for Inter-AS BGP
route-policy EBGP-IN
  if destination in (100.0.0.0/16 le 32) then
    pass
  else
    drop
  endif
end-policy

route-policy EBGP-OUT
  if destination in (100.0.0.0/16 le 32) then
    pass
  else
    drop
  endif
end-policy

!! Add Inter-AS eBGP neighbor to existing BGP configuration
router bgp 65100
 !! eBGP Neighbor to Y-ASBR1
 neighbor 10.1.101.2
  remote-as 65200
  description "eBGP to Y-ASBR1"
  password clear dickie-lab
  ebgp-multihop 2
  update-source GigabitEthernet0/0/0/3
  address-family ipv4 unicast
   route-policy EBGP-OUT out
   route-policy EBGP-IN in
   send-community-ebgp
   allocate-label all
  !
  address-family ipv6 unicast
   route-policy EBGP-OUT out
   route-policy EBGP-IN in
   send-community-ebgp
   allocate-label all
  !
  address-family ipv4 labeled-unicast
   route-policy EBGP-OUT out
   route-policy EBGP-IN in
   send-community-ebgp
  !
  address-family ipv6 labeled-unicast
   route-policy EBGP-OUT out
   route-policy EBGP-IN in
   send-community-ebgp
  !
  address-family vpnv4 unicast
   route-policy EBGP-OUT out
   route-policy EBGP-IN in
   send-community-ebgp
  !
  address-family vpnv6 unicast
   route-policy EBGP-OUT out
   route-policy EBGP-IN in
   send-community-ebgp
  !
 !
!

commit
```

#### Task 2.3.2: Configure Y-ASBR1 eBGP to X-ASBR1

**Device: Y-ASBR1 (100.0.0.33) - IOS-XR**

```
hostname Y-ASBR1

!! Route Policies for Inter-AS BGP
route-policy EBGP-IN
  if destination in (100.0.0.0/16 le 32) then
    pass
  else
    drop
  endif
end-policy

route-policy EBGP-OUT
  if destination in (100.0.0.0/16 le 32) then
    pass
  else
    drop
  endif
end-policy

!! BGP Configuration
router bgp 65200
 bgp router-id 100.0.0.33
 bgp log neighbor changes detail
 bgp graceful-restart
 !
 address-family ipv4 unicast
  allocate-label all
 !
 address-family ipv6 unicast
  allocate-label all
 !
 address-family ipv4 labeled-unicast
 !
 address-family ipv6 labeled-unicast
 !
 address-family vpnv4 unicast
 !
 address-family vpnv6 unicast
 !
 !! iBGP to Y-RR1
 neighbor 100.0.0.38
  remote-as 65200
  description "iBGP to Y-RR1"
  password clear dickie-lab
  update-source Loopback0
  address-family ipv4 unicast
   route-policy PASS in
   route-policy PASS out
  !
  address-family ipv6 unicast
   route-policy PASS in
   route-policy PASS out
  !
  address-family vpnv4 unicast
   route-policy PASS in
   route-policy PASS out
  !
  address-family vpnv6 unicast
   route-policy PASS in
   route-policy PASS out
  !
 !
 !! eBGP Neighbor to X-ASBR1
 neighbor 10.1.101.1
  remote-as 65100
  description "eBGP to X-ASBR1"
  password clear dickie-lab
  ebgp-multihop 2
  update-source GigabitEthernet0/0/0/2
  address-family ipv4 unicast
   route-policy EBGP-OUT out
   route-policy EBGP-IN in
   send-community-ebgp
   allocate-label all
  !
  address-family ipv6 unicast
   route-policy EBGP-OUT out
   route-policy EBGP-IN in
   send-community-ebgp
   allocate-label all
  !
  address-family ipv4 labeled-unicast
   route-policy EBGP-OUT out
   route-policy EBGP-IN in
   send-community-ebgp
  !
  address-family ipv6 labeled-unicast
   route-policy EBGP-OUT out
   route-policy EBGP-IN in
   send-community-ebgp
  !
  address-family vpnv4 unicast
   route-policy EBGP-OUT out
   route-policy EBGP-IN in
   send-community-ebgp
  !
  address-family vpnv6 unicast
   route-policy EBGP-OUT out
   route-policy EBGP-IN in
   send-community-ebgp
  !
 !
!

commit
```

---

## Verification Commands

### Task 2.4: BGP Foundation Verification

#### Task 2.4.1: iBGP Route Reflector Verification

```
!! Check BGP neighbor status - Run on X-RR1
show bgp summary
show bgp neighbors

!! Verify Route Reflector clients - Run on X-RR1
show bgp ipv4 unicast route-reflector clients
show bgp neighbors 100.0.0.21 | include "route reflector"    ! Check X-PE1 RR client status

!! Check BGP table and paths - Run on any X-domain device
show bgp ipv4 unicast
show bgp ipv6 unicast

!! Verify Additional Paths capability - Run on X-RR1
show bgp neighbors 100.0.0.21 | include "additional-paths"    ! Check X-PE1 add-path capability
show bgp ipv4 unicast 100.0.0.36/32 detail    ! Check paths to Y-PE1

!! Check BGP-LU label allocation - Run on X-ASBR1
show bgp ipv4 labeled-unicast
show mpls forwarding-table
```

#### Task 2.4.2: Inter-AS eBGP Verification

```
!! Check eBGP neighbor status - Run on X-ASBR1
show bgp summary | include "65200"
show bgp neighbors 10.1.101.2    ! Check connection to Y-ASBR1

!! Verify BGP-LU label exchange - Run on X-ASBR1
show bgp ipv4 labeled-unicast
show bgp ipv6 labeled-unicast

!! Check Inter-AS route exchange - Run on X-ASBR1  
show bgp ipv4 unicast regexp "65200"    ! Routes learned from AS 65200
show bgp ipv6 unicast regexp "65200"    ! IPv6 routes from AS 65200

!! Verify MPLS forwarding for Inter-AS - Run on X-ASBR1
show mpls forwarding-table
show cef ipv4 100.0.0.33/32 detail    ! CEF entry for Y-ASBR1
```

#### Task 2.4.3: End-to-End Connectivity Tests

```
!! Test loopback reachability across AS boundary
ping 100.0.0.33 source loopback0    ! From X-ASBR1 to Y-ASBR1
ping 100.0.0.36 source loopback0    ! From X-ASBR1 to Y-PE1
ping 100.0.0.31 source loopback0    ! From Y-ASBR1 to X-ASBR1
ping 100.0.0.21 source loopback0    ! From Y-PE1 to X-PE1

!! Verify MPLS label switching across AS boundary
traceroute mpls ipv4 100.0.0.33/32    ! From X-ASBR1 trace to Y-ASBR1
traceroute mpls ipv4 100.0.0.36/32    ! From X-PE1 trace to Y-PE1

!! Check BGP convergence time
clear bgp ipv4 unicast * soft    ! On X-RR1 to test reconvergence