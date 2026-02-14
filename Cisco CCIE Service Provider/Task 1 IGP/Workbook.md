# CCIE Service Provider IGP Foundation
## ISIS (AS 65100) and OSPF (AS 65200) Base Configurations

### Overview and Design

Before implementing any BGP, MPLS, or service-layer technologies, we must establish the IGP foundation that provides:

- **Loopback reachability** between all devices within each AS
- **Fast convergence** with optimized SPF timers
- **IPv4 and IPv6** dual-stack support
- **Authentication** for security
- **Traffic Engineering** topology database foundation

### AS 65100 (X-Domain) - ISIS Level-2 Design

**Architecture:**
- **Protocol**: ISIS Level-2 only (no Level-1 areas)
- **NET Format**: 49.0001.0100.0000.00XX.00 (XX = loopback last octet)
- **Metric Type**: Wide metrics (24-bit) for TE support
- **Authentication**: Area-wide authentication with keychain

**Design Principles:**
- Flat Level-2 topology for simplicity and scalability
- Wide metrics to support Traffic Engineering
- Fast convergence with tuned SPF timers
- Dual-stack IPv4 and IPv6

---

### AS 65200 (Y-Domain) - OSPF Area 0 Design

**Architecture:**
- **Protocol**: OSPF Area 0 only
- **Router-ID**: Based on loopback IP address
- **Metric**: Explicitly set to 100 on all interfaces for consistency
- **Authentication**: MD5 for OSPFv2, IPsec for OSPFv3

**Design Principles:**
- Single Area 0 for simplicity
- Dual-stack with OSPFv2 and OSPFv3
- Fast convergence with tuned timers
- Explicit interface costs for predictability

---

## Configuration Tasks

### Task 1: ISIS Configuration - AS 65100 (X-Domain)

#### Task 1.1: ISIS Basic Configuration - X-P1

**Device: X-P1 (100.0.0.27)**

```
!! IOS-XR Configuration
hostname X-P1

!! Basic ISIS configuration
router isis CORE
 is-type level-2-only
 net 49.0001.0100.0000.0027.00
 nsf ietf
 log adjacency changes
 lsp-gen-interval maximum-wait 5000 initial-wait 50 secondary-wait 200
 lsp-refresh-interval 65000
 max-lsp-lifetime 65535
 lsp-password keychain ISIS-KEY
 area-password keychain ISIS-KEY
 !
 address-family ipv4 unicast
  metric-style wide
  advertise link attributes
  mpls traffic-eng level-2-only
  mpls traffic-eng router-id Loopback0
  !
 address-family ipv6 unicast
  metric-style wide
  advertise link attributes
  !
 interface Loopback0
  passive
  address-family ipv4 unicast
  !
  address-family ipv6 unicast
  !
 !
 interface GigabitEthernet0/0/0/0
  point-to-point
  address-family ipv4 unicast
   metric 100
  !
  address-family ipv6 unicast
   metric 100
  !
 !
 interface GigabitEthernet0/0/0/1
  point-to-point
  address-family ipv4 unicast
   metric 100
  !
  address-family ipv6 unicast
   metric 100
  !
 !
 interface GigabitEthernet0/0/0/2
  point-to-point
  address-family ipv4 unicast
   metric 100
  !
  address-family ipv6 unicast
   metric 100
  !
 !
 interface GigabitEthernet0/0/0/3
  point-to-point
  address-family ipv4 unicast
   metric 100
  !
  address-family ipv6 unicast
   metric 100
  !
 !
!

key chain ISIS-KEY
 key 1
  accept-lifetime 00:00:00 january 01 2024 infinite
  key-string password 7 dickie-lab
  send-lifetime 00:00:00 january 01 2024 infinite
 !
!

mpls oam
!
mpls traffic-eng
 interface GigabitEthernet0/0/0/0
  attribute-names CORE-LINK
 !
 interface GigabitEthernet0/0/0/1
  attribute-names CORE-LINK
 !
 interface GigabitEthernet0/0/0/2
  attribute-names CORE-LINK
 !
 interface GigabitEthernet0/0/0/3
  attribute-names CORE-LINK
 !
!

commit
```

**Configuration Notes:**
- `is-type level-2-only` - Single level topology
- `net 49.0001.0100.0000.0027.00` - NET address format: area.system-id.selector
- `nsf ietf` - Non-Stop Forwarding for high availability
- `lsp-gen-interval` - Tuned for fast convergence (50ms initial, 200ms secondary, 5000ms max)
- `metric-style wide` - 24-bit metrics for TE support
- `point-to-point` - Interface network type
- Authentication via keychain protects against unauthorized routing updates

---

#### Task 1.2: ISIS Configuration - X-PE1

**Device: X-PE1 (100.0.0.21)**

```
!! IOS-XR Configuration
hostname X-PE1

!! Basic ISIS configuration
router isis CORE
 is-type level-2-only
 net 49.0001.0100.0000.0021.00
 nsf ietf
 log adjacency changes
 lsp-gen-interval maximum-wait 5000 initial-wait 50 secondary-wait 200
 lsp-refresh-interval 65000
 max-lsp-lifetime 65535
 lsp-password keychain ISIS-KEY
 area-password keychain ISIS-KEY
 !
 address-family ipv4 unicast
  metric-style wide
  advertise link attributes
  mpls traffic-eng level-2-only
  mpls traffic-eng router-id Loopback0
  !
 address-family ipv6 unicast
  metric-style wide
  advertise link attributes
  !
 interface Loopback0
  passive
  address-family ipv4 unicast
  !
  address-family ipv6 unicast
  !
 !
 interface GigabitEthernet0/0/0/0
  point-to-point
  address-family ipv4 unicast
   metric 100
  !
  address-family ipv6 unicast
   metric 100
  !
 !
 interface GigabitEthernet0/0/0/1
  point-to-point
  address-family ipv4 unicast
   metric 100
  !
  address-family ipv6 unicast
   metric 100
  !
 !
 interface GigabitEthernet0/0/0/2
  point-to-point
  address-family ipv4 unicast
   metric 100
  !
  address-family ipv6 unicast
   metric 100
  !
 !
!

key chain ISIS-KEY
 key 1
  accept-lifetime 00:00:00 january 01 2024 infinite
  key-string password 7 dickie-lab
  send-lifetime 00:00:00 january 01 2024 infinite
 !
!

mpls oam
!
mpls traffic-eng
 interface GigabitEthernet0/0/0/0
  attribute-names PE-CORE-LINK
 !
 interface GigabitEthernet0/0/0/1
  attribute-names PE-CORE-LINK
 !
 interface GigabitEthernet0/0/0/2
  attribute-names PE-PE-LINK
 !
!

commit
```

---

#### Task 1.3: ISIS Configuration Summary for All X-Domain Devices

**Apply similar ISIS configuration to all X-domain devices with these NET addresses:**

| Device | NET Address | Loopback | Role |
|--------|-------------|----------|------|
| X-P1 | 49.0001.0100.0000.0027.00 | 100.0.0.27 | Core |
| X-P2 | 49.0001.0100.0000.0028.00 | 100.0.0.28 | Core |
| X-P3 | 49.0001.0100.0000.0029.00 | 100.0.0.29 | Core |
| X-P4 | 49.0001.0100.0000.0030.00 | 100.0.0.30 | Core |
| X-RR1 | 49.0001.0100.0000.0025.00 | 100.0.0.25 | RR |
| X-RR2 | 49.0001.0100.0000.0026.00 | 100.0.0.26 | RR |
| X-PE1 | 49.0001.0100.0000.0021.00 | 100.0.0.21 | PE |
| X-PE2 | 49.0001.0100.0000.0022.00 | 100.0.0.22 | PE |
| X-PE3 | 49.0001.0100.0000.0023.00 | 100.0.0.23 | PE |
| X-PE4 | 49.0001.0100.0000.0024.00 | 100.0.0.24 | PE |
| X-ASBR1 | 49.0001.0100.0000.0031.00 | 100.0.0.31 | ASBR |
| X-ASBR2 | 49.0001.0100.0000.0032.00 | 100.0.0.32 | ASBR |

**Note**: Each device uses the same configuration template, adjusting only:
- Hostname
- NET address (system-id portion matches last octet of loopback)
- Specific interfaces enabled for ISIS
- Interface attribute names for MPLS TE

---

### Task 2: OSPF Configuration - AS 65200 (Y-Domain)

#### Task 2.1: OSPF Basic Configuration - Y-P1

**Device: Y-P1 (100.0.0.35)**

```
!! IOS-XR Configuration
hostname Y-P1

!! Basic OSPF configuration
router ospf CORE
 router-id 100.0.0.35
 nsf ietf
 log adjacency changes detail
 timers throttle spf 50 200 5000
 timers throttle lsa all 50 200 5000
 timers lsa min-arrival 100
 max-metric router-lsa on-startup 300
 mpls traffic-eng router-id Loopback0
 area 0
  mpls traffic-eng
  authentication message-digest keychain OSPF-KEY
  !
  interface Loopback0
   passive enable
  !
  interface GigabitEthernet0/0/0/0
   cost 100
   network point-to-point
   authentication message-digest keychain OSPF-KEY
  !
  interface GigabitEthernet0/0/0/1
   cost 100
   network point-to-point
   authentication message-digest keychain OSPF-KEY
  !
  interface GigabitEthernet0/0/0/2
   cost 100
   network point-to-point
   authentication message-digest keychain OSPF-KEY
  !
  interface GigabitEthernet0/0/0/3
   cost 100
   network point-to-point
   authentication message-digest keychain OSPF-KEY
  !
  interface GigabitEthernet0/0/0/4
   cost 100
   network point-to-point
   authentication message-digest keychain OSPF-KEY
  !
 !
!

!! OSPFv3 for IPv6
router ospfv3 CORE
 router-id 100.0.0.35
 nsf ietf
 log adjacency changes detail
 timers throttle spf 50 200 5000
 timers throttle lsa all 50 200 5000
 timers lsa min-arrival 100
 max-metric router-lsa on-startup 300
 area 0
  authentication ipsec spi 1000 md5 1234567890ABCDEF1234567890ABCDEF
  !
  interface Loopback0
   passive enable
  !
  interface GigabitEthernet0/0/0/0
   cost 100
   network point-to-point
  !
  interface GigabitEthernet0/0/0/1
   cost 100
   network point-to-point
  !
  interface GigabitEthernet0/0/0/2
   cost 100
   network point-to-point
  !
  interface GigabitEthernet0/0/0/3
   cost 100
   network point-to-point
  !
  interface GigabitEthernet0/0/0/4
   cost 100
   network point-to-point
  !
 !
!

!! OSPF Keychain
key chain OSPF-KEY
 key 1
  accept-lifetime 00:00:00 january 01 2024 infinite
  key-string password 7 dickie-lab
  send-lifetime 00:00:00 january 01 2024 infinite
 !
!

!! MPLS and Traffic Engineering
mpls oam
!
mpls traffic-eng
 interface GigabitEthernet0/0/0/0
  attribute-names CORE-LINK
 !
 interface GigabitEthernet0/0/0/1
  attribute-names CORE-LINK
 !
 interface GigabitEthernet0/0/0/2
  attribute-names CORE-LINK
 !
 interface GigabitEthernet0/0/0/3
  attribute-names CORE-LINK
 !
 interface GigabitEthernet0/0/0/4
  attribute-names CORE-LINK
 !
!

commit
```

**Configuration Notes:**
- `router-id 100.0.0.35` - Uses loopback IP address
- `nsf ietf` - Non-Stop Forwarding
- `timers throttle spf 50 200 5000` - Fast SPF convergence (50ms initial)
- `timers throttle lsa all 50 200 5000` - Fast LSA generation
- `max-metric router-lsa on-startup 300` - Advertise max metric for 5 minutes on startup
- `cost 100` - Explicit cost on all interfaces for consistency
- `network point-to-point` - Interface network type
- OSPFv2 uses MD5 authentication via keychain
- OSPFv3 uses IPsec authentication

---

#### Task 2.2: OSPF Configuration - Y-PE1

**Device: Y-PE1 (100.0.0.36)**

```
!! IOS-XR Configuration
hostname Y-PE1

!! Basic OSPF configuration
router ospf CORE
 router-id 100.0.0.36
 nsf ietf
 log adjacency changes detail
 timers throttle spf 50 200 5000
 timers throttle lsa all 50 200 5000
 timers lsa min-arrival 100
 max-metric router-lsa on-startup 300
 mpls traffic-eng router-id Loopback0
 area 0
  mpls traffic-eng
  authentication message-digest keychain OSPF-KEY
  !
  interface Loopback0
   passive enable
  !
  interface GigabitEthernet0/0/0/0
   cost 100
   network point-to-point
   authentication message-digest keychain OSPF-KEY
  !
  interface GigabitEthernet0/0/0/1
   cost 100
   network point-to-point
   authentication message-digest keychain OSPF-KEY
  !
 !
!

!! OSPFv3 for IPv6
router ospfv3 CORE
 router-id 100.0.0.36
 nsf ietf
 log adjacency changes detail
 timers throttle spf 50 200 5000
 timers throttle lsa all 50 200 5000
 timers lsa min-arrival 100
 max-metric router-lsa on-startup 300
 area 0
  authentication ipsec spi 1000 md5 1234567890ABCDEF1234567890ABCDEF
  !
  interface Loopback0
   passive enable
  !
  interface GigabitEthernet0/0/0/0
   cost 100
   network point-to-point
  !
  interface GigabitEthernet0/0/0/1
   cost 100
   network point-to-point
  !
 !
!

!! OSPF Keychain
key chain OSPF-KEY
 key 1
  accept-lifetime 00:00:00 january 01 2024 infinite
  key-string password 7 dickie-lab
  send-lifetime 00:00:00 january 01 2024 infinite
 !
!

!! MPLS and Traffic Engineering
mpls oam
!
mpls traffic-eng
 interface GigabitEthernet0/0/0/0
  attribute-names PE-CORE-LINK
 !
 interface GigabitEthernet0/0/0/1
  attribute-names PE-PE-LINK
 !
!

commit
```

---

#### Task 2.3: OSPF Configuration - Y-RR1 (IOS-XE)

**Device: Y-RR1 (100.0.0.38)**

```
!! IOS-XE Configuration
hostname Y-RR1

!! OSPF Configuration
router ospf 1
 router-id 100.0.0.38
 nsf ietf
 timers throttle spf 50 200 5000
 timers throttle lsa all 50 200 5000
 timers lsa arrival 100
 max-metric router-lsa on-startup 300
 mpls traffic-eng router-id Loopback0
 mpls traffic-eng area 0
 area 0 authentication message-digest
 passive-interface Loopback0

!! OSPFv3 for IPv6
router ospfv3 1
 router-id 100.0.0.38
 area 0 authentication ipsec spi 1000 md5 1234567890ABCDEF1234567890ABCDEF
 passive-interface Loopback0

!! Interface configurations
interface Loopback0
 ip ospf 1 area 0
 ipv6 ospf 1 area 0

interface GigabitEthernet1
 ip ospf 1 area 0
 ip ospf network point-to-point
 ip ospf message-digest-key 1 md5 dickie-lab
 ip ospf cost 100
 ipv6 ospf 1 area 0
 ipv6 ospf network point-to-point
 ipv6 ospf cost 100

!! MPLS and TE
mpls traffic-eng tunnels
ip rsvp bandwidth

interface GigabitEthernet1
 mpls traffic-eng tunnels
 ip rsvp bandwidth 1000000
```

**IOS-XE Configuration Notes:**
- Configuration is flat (non-hierarchical) compared to IOS-XR
- OSPF enabled on interfaces using `ip ospf 1 area 0` under interface
- Authentication key configured per-interface with `ip ospf message-digest-key`
- No commit required - changes take effect immediately
- OSPFv3 authentication uses IPsec at area level

---

#### Task 2.4: OSPF Configuration Summary for All Y-Domain Devices

**Apply similar OSPF configuration to all Y-domain devices with these Router-IDs:**

| Device | Router-ID | Platform | Role |
|--------|-----------|----------|------|
| Y-P1 | 100.0.0.35 | IOS-XR | Core |
| Y-PE1 | 100.0.0.36 | IOS-XR | PE |
| Y-PE2 | 100.0.0.37 | IOS-XR | PE |
| Y-ASBR1 | 100.0.0.33 | IOS-XR | ASBR |
| Y-ASBR2 | 100.0.0.34 | IOS-XR | ASBR |
| Y-RR1 | 100.0.0.38 | IOS-XE | RR |

**Note**: 
- IOS-XR devices use hierarchical configuration under `router ospf CORE`
- IOS-XE device (Y-RR1) uses traditional flat configuration
- All devices use cost 100 on interfaces for consistency
- OSPFv2 and OSPFv3 run as separate processes

---

## Verification Commands

### ISIS Verification (X-Domain)

```
!! Check ISIS adjacencies - Run on X-P1
show isis adjacency

!! Expected output: All neighbors in UP state with correct System ID

!! Verify ISIS database - Run on X-P1  
show isis database verbose

!! Expected output: LSPs from all routers in the domain

!! Check IPv4 routes learned via ISIS - Run on X-PE1
show route isis

!! Expected output: Loopback routes from all X-domain routers

!! Check IPv6 routes learned via ISIS
show route ipv6 isis

!! Verify ISIS interface status
show isis interface

!! Expected output: All configured interfaces UP and running ISIS

!! Check ISIS neighbors detail
show isis neighbors detail

!! Expected output: Neighbor states, hold times, circuit types

!! Verify TE database - Run on any X-domain device
show mpls traffic-eng topology

!! Expected output: TE topology with link attributes
```

---

### OSPF Verification (Y-Domain)

```
!! Check OSPF adjacencies - Run on Y-P1
show ospf neighbor

!! Expected output: All neighbors in FULL state

!! Check OSPFv3 adjacencies
show ospfv3 neighbor

!! Expected output: IPv6 neighbors in FULL state

!! Verify OSPF database - Run on Y-P1
show ospf database

!! Expected output: Router LSAs, Network LSAs, Type-10 Opaque LSAs

!! Check OSPFv3 database
show ospfv3 database

!! Check IPv4 routes learned via OSPF - Run on Y-PE1
show route ospf

!! Expected output: Loopback routes from all Y-domain routers

!! Check IPv6 routes learned via OSPFv3
show route ipv6 ospf

!! Verify OSPF interface status
show ospf interface

!! Expected output: All configured interfaces UP, network type point-to-point

!! Check OSPF neighbors detail
show ospf neighbor detail

!! Verify TE database - Run on any Y-domain device
show mpls traffic-eng topology

!! Expected output: TE topology with link attributes
```

---

### IOS-XE Verification (Y-RR1)

```
!! Check OSPF neighbors
show ip ospf neighbor

!! Check OSPFv3 neighbors
show ipv6 ospf neighbor

!! Verify OSPF database
show ip ospf database

!! Check routes
show ip route ospf
show ipv6 route ospf

!! Verify interface OSPF status
show ip ospf interface
show ipv6 ospf interface
```

---

## Key Configuration Differences: IOS-XR vs IOS-XE

### IOS-XR (Hierarchical Configuration)

**OSPF Configuration Structure:**
```
router ospf CORE
 router-id X.X.X.X
 area 0
  interface Loopback0
   passive enable
  !
  interface GigabitEthernet0/0/0/0
   cost 100
   network point-to-point
  !
 !
!
commit  !! Required to apply changes
```

**ISIS Configuration Structure:**
```
router isis CORE
 net 49.0001.0100.0000.00XX.00
 address-family ipv4 unicast
  metric-style wide
 !
 interface Loopback0
  passive
  address-family ipv4 unicast
  !
 !
!
commit  !! Required to apply changes
```

---

### IOS-XE (Flat Configuration)

**OSPF Configuration Structure:**
```
router ospf 1
 router-id X.X.X.X
 passive-interface Loopback0

interface Loopback0
 ip ospf 1 area 0

interface GigabitEthernet1
 ip ospf 1 area 0
 ip ospf network point-to-point
 ip ospf cost 100

!! No commit required - changes immediate
```

---

## Troubleshooting Common Issues

### ISIS Adjacencies Not Forming

**Problem**: show isis adjacency shows no neighbors

**Check:**
1. Physical connectivity - `show interfaces brief`
2. ISIS enabled on interface - `show isis interface`
3. NET address configured - `show isis database`
4. Authentication match - Check keychain configuration
5. IS-type match - Both ends must be level-2-only or compatible
6. IPv4/IPv6 address families enabled on both ends

**Debug Commands:**
```
debug isis adj-packets
debug isis spf-events
```

---

### OSPF Adjacencies Stuck in INIT or 2WAY

**Problem**: show ospf neighbor shows neighbors not reaching FULL

**Check:**
1. Network type mismatch - Both ends must be point-to-point or broadcast
2. Authentication failure - Check MD5 keys match
3. Area mismatch - Both interfaces must be in same area
4. MTU mismatch - Check `show interfaces` for MTU
5. Access-list blocking OSPF - Check for ACLs on interfaces

**Debug Commands:**
```
debug ospf adj
debug ospf hello
```

---

### Routes Not Appearing in Routing Table

**Problem**: IGP adjacencies UP but routes missing

**Check:**
1. Passive interface configuration - Ensure loopbacks are passive
2. Address family configuration - Verify IPv4/IPv6 address families enabled
3. Route filtering - Check for distribute-lists or prefix-lists
4. Interface not in IGP - Verify interface is configured under ISIS/OSPF
5. Area border issues (OSPF) - Check area configuration

**Verification:**
```
!! ISIS
show isis database verbose | include "IP Address"
show route isis

!! OSPF
show ospf database router | include "Link ID"
show route ospf
```

---

### Authentication Failures

**Problem**: Adjacencies flapping or not forming due to auth

**ISIS - Check:**
```
show isis database | include "Auth"
show running-config | include "key chain ISIS-KEY"
```

**OSPF - Check:**
```
show ospf interface | include "auth"
show running-config | include "key chain OSPF-KEY"
```

**Common Issues:**
- Keychain name mismatch
- Key ID mismatch
- Password mismatch
- Accept/send lifetimes not overlapping

---

## IGP Design Comparison

### ISIS vs OSPF in This Lab

| Feature | ISIS (X-Domain) | OSPF (Y-Domain) |
|---------|-----------------|-----------------|
| **Area Design** | Level-2 only (flat) | Single Area 0 |
| **Addressing** | NET-based | Router-ID based |
| **Metric** | Wide (24-bit) | Explicit cost 100 |
| **Authentication** | Area + LSP password | MD5 (v2), IPsec (v3) |
| **Convergence** | Tuned LSP timers | Tuned SPF timers |
| **Platform Support** | IOS-XR native | IOS-XR and IOS-XE |
| **TE Support** | Link attributes | Link attributes |

### Common Design Elements

**Both domains implement:**
- **Fast convergence** with optimized timers
- **Authentication** for security
- **Traffic Engineering** support with link attributes
- **NSF/Graceful Restart** for maintenance
- **Dual-stack** IPv4 and IPv6
- **Passive loopbacks** to prevent unnecessary adjacencies

---

**Verification Checklist Before Proceeding:**
- [ ] All ISIS adjacencies UP in X-Domain
- [ ] All OSPF adjacencies FULL in Y-Domain
- [ ] All loopback routes present in routing tables
- [ ] IPv4 and IPv6 routes learned via IGP
- [ ] Authentication working on all adjacencies
- [ ] MPLS TE topology database populated
- [ ] No error messages in logs


