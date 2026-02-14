# CCIE Service Provider Task 3 - MPLS Basics and LDP Configuration
## Traditional LDP Label Distribution alongside Segment Routing

### CCIE SP v5.1 Blueprint Coverage

**Section 1.4 - Multiprotocol Label Switching (25% weighting):**
- 1.4.a MPLS forwarding and control plane mechanisms
- 1.4.b LDP
- 1.4.c LDP scale and performance

### Prerequisites

- Task 1 Completed: IGP foundation with SR-MPLS enabled
- Task 2 Completed: BGP Route Reflectors and Inter-AS connectivity
- Loopback reachability established within each AS
- SR Node SIDs already allocated and functioning

### Overview and Design

This task adds traditional MPLS LDP (Label Distribution Protocol) alongside the existing Segment Routing infrastructure. This dual-label approach provides:

- **Learning Value**: Understanding both SR-MPLS and LDP mechanisms
- **Migration Scenarios**: Real-world SP networks often run both during transitions
- **Label Preference**: Configuring which protocol takes precedence
- **Troubleshooting Skills**: Comparing SR vs LDP label allocation

**Authentication**: All LDP sessions use password "dickie-lab"

### LDP Architecture Design

#### AS 65100 (X-Domain) - LDP with SR Coexistence
- **Primary Labels**: Segment Routing (already configured)
- **Secondary Labels**: LDP for comparison and backup
- **Preference**: SR preferred over LDP (modern approach)

#### AS 65200 (Y-Domain) - LDP with SR Coexistence
- **Primary Labels**: Segment Routing (already configured)  
- **Secondary Labels**: LDP for traditional MPLS services
- **Preference**: SR preferred over LDP

---

## Configuration Tasks

### Task 3.1: LDP Configuration - AS 65100 (X-Domain)

#### Task 3.1.1: Configure LDP on X-P1 (IOS-XR)

**Device: X-P1 (100.0.0.27)**

```
!! MPLS LDP Configuration
hostname X-P1

!! Configure MPLS LDP globally
mpls ldp
 router-id 100.0.0.27
 log neighbor
 log hello-adjacency
 log gr
 session protection
 !
 address-family ipv4
  label local allocate for host-routes
  label remote accept for prefix-list LDP-PREFIXES
 !
 address-family ipv6
  label local allocate for host-routes
  label remote accept for prefix-list LDP-PREFIXES
 !
 interface GigabitEthernet0/0/0/0
  address-family ipv4
   hello-interval 5
   hold-time 15
  !
  address-family ipv6
   hello-interval 5
   hold-time 15
  !
 !
 interface GigabitEthernet0/0/0/1
  address-family ipv4
   hello-interval 5
   hold-time 15
  !
  address-family ipv6
   hello-interval 5
   hold-time 15
  !
 !
 interface GigabitEthernet0/0/0/2
  address-family ipv4
   hello-interval 5
   hold-time 15
  !
  address-family ipv6
   hello-interval 5
   hold-time 15
  !
 !
 interface GigabitEthernet0/0/0/3
  address-family ipv4
   hello-interval 5
   hold-time 15
  !
  address-family ipv6
   hello-interval 5
   hold-time 15
  !
 !
 interface GigabitEthernet0/0/0/4
  address-family ipv4
   hello-interval 5
   hold-time 15
  !
  address-family ipv6
   hello-interval 5
   hold-time 15
  !
 !
 interface GigabitEthernet0/0/0/5
  address-family ipv4
   hello-interval 5
   hold-time 15
  !
  address-family ipv6
   hello-interval 5
   hold-time 15
  !
 !
 interface GigabitEthernet0/0/0/6
  address-family ipv4
   hello-interval 5
   hold-time 15
  !
  address-family ipv6
   hello-interval 5
   hold-time 15
  !
 !
 neighbor 100.0.0.21
  password clear dickie-lab
  session holdtime 180
 !
 neighbor 100.0.0.22
  password clear dickie-lab
  session holdtime 180
 !
 neighbor 100.0.0.25
  password clear dickie-lab
  session holdtime 180
 !
 neighbor 100.0.0.26
  password clear dickie-lab
  session holdtime 180
 !
 neighbor 100.0.0.28
  password clear dickie-lab
  session holdtime 180
 !
 neighbor 100.0.0.29
  password clear dickie-lab
  session holdtime 180
 !
 neighbor 100.0.0.30
  password clear dickie-lab
  session holdtime 180
 !
 neighbor 100.0.0.31
  password clear dickie-lab
  session holdtime 180
 !
 neighbor 100.0.0.32
  password clear dickie-lab
  session holdtime 180
 !
!

!! Prefix lists for LDP label filtering
prefix-set LDP-PREFIXES
  100.0.0.0/16 le 32
end-set

!! Enable MPLS on interfaces
interface GigabitEthernet0/0/0/0
 mpls ldp sync
!
interface GigabitEthernet0/0/0/1
 mpls ldp sync
!
interface GigabitEthernet0/0/0/2
 mpls ldp sync
!
interface GigabitEthernet0/0/0/3
 mpls ldp sync
!
interface GigabitEthernet0/0/0/4
 mpls ldp sync
!
interface GigabitEthernet0/0/0/5
 mpls ldp sync
!
interface GigabitEthernet0/0/0/6
 mpls ldp sync
!

!! Configure SR preference over LDP
router isis CORE
 address-family ipv4 unicast
  segment-routing mpls sr-prefer
 !
!

commit
```

#### Task 3.1.2: Configure LDP on X-PE1 (IOS-XR)

**Device: X-PE1 (100.0.0.21)**

```
hostname X-PE1

!! Configure MPLS LDP
mpls ldp
 router-id 100.0.0.21
 log neighbor
 log hello-adjacency
 log gr
 session protection
 !
 address-family ipv4
  label local allocate for host-routes
  label remote accept for prefix-list LDP-PREFIXES
 !
 address-family ipv6
  label local allocate for host-routes
  label remote accept for prefix-list LDP-PREFIXES
 !
 interface GigabitEthernet0/0/0/0
  address-family ipv4
   hello-interval 5
   hold-time 15
  !
  address-family ipv6
   hello-interval 5
   hold-time 15
  !
 !
 interface GigabitEthernet0/0/0/1
  address-family ipv4
   hello-interval 5
   hold-time 15
  !
  address-family ipv6
   hello-interval 5
   hold-time 15
  !
 !
 interface GigabitEthernet0/0/0/2
  address-family ipv4
   hello-interval 5
   hold-time 15
  !
  address-family ipv6
   hello-interval 5
   hold-time 15
  !
 !
 neighbor 100.0.0.22
  password clear dickie-lab
  session holdtime 180
 !
 neighbor 100.0.0.27
  password clear dickie-lab
  session holdtime 180
 !
 neighbor 100.0.0.28
  password clear dickie-lab
  session holdtime 180
 !
!

prefix-set LDP-PREFIXES
  100.0.0.0/16 le 32
end-set

interface GigabitEthernet0/0/0/0
 mpls ldp sync
!
interface GigabitEthernet0/0/0/1
 mpls ldp sync
!
interface GigabitEthernet0/0/0/2
 mpls ldp sync
!

!! Configure SR preference over LDP
router isis CORE
 address-family ipv4 unicast
  segment-routing mpls sr-prefer
 !
!

commit
```

#### Task 3.1.3: Configure LDP on X-RR1 (IOS-XR)

**Device: X-RR1 (100.0.0.25)**

```
hostname X-RR1

!! Configure MPLS LDP
mpls ldp
 router-id 100.0.0.25
 log neighbor
 log hello-adjacency
 session protection
 !
 address-family ipv4
  label local allocate for host-routes
  label remote accept for prefix-list LDP-PREFIXES
 !
 interface GigabitEthernet0/0/0/0
  address-family ipv4
   hello-interval 5
   hold-time 15
  !
 !
 interface GigabitEthernet0/0/0/1
  address-family ipv4
   hello-interval 5
   hold-time 15
  !
 !
 neighbor 100.0.0.26
  password clear dickie-lab
  session holdtime 180
 !
 neighbor 100.0.0.27
  password clear dickie-lab
  session holdtime 180
 !
!

prefix-set LDP-PREFIXES
  100.0.0.0/16 le 32
end-set

interface GigabitEthernet0/0/0/0
 mpls ldp sync
!
interface GigabitEthernet0/0/0/1
 mpls ldp sync
!

!! Configure SR preference over LDP
router isis CORE
 address-family ipv4 unicast
  segment-routing mpls sr-prefer
 !
!

commit
```

### Task 3.2: LDP Configuration - AS 65200 (Y-Domain)

#### Task 3.2.1: Configure LDP on Y-P1 (IOS-XR)

**Device: Y-P1 (100.0.0.35)**

```
hostname Y-P1

!! Configure MPLS LDP
mpls ldp
 router-id 100.0.0.35
 log neighbor
 log hello-adjacency
 log gr
 session protection
 !
 address-family ipv4
  label local allocate for host-routes
  label remote accept for prefix-list LDP-PREFIXES
 !
 address-family ipv6
  label local allocate for host-routes
  label remote accept for prefix-list LDP-PREFIXES
 !
 interface GigabitEthernet0/0/0/0
  address-family ipv4
   hello-interval 5
   hold-time 15
  !
  address-family ipv6
   hello-interval 5
   hold-time 15
  !
 !
 interface GigabitEthernet0/0/0/1
  address-family ipv4
   hello-interval 5
   hold-time 15
  !
  address-family ipv6
   hello-interval 5
   hold-time 15
  !
 !
 interface GigabitEthernet0/0/0/2
  address-family ipv4
   hello-interval 5
   hold-time 15
  !
  address-family ipv6
   hello-interval 5
   hold-time 15
  !
 !
 interface GigabitEthernet0/0/0/3
  address-family ipv4
   hello-interval 5
   hold-time 15
  !
  address-family ipv6
   hello-interval 5
   hold-time 15
  !
 !
 interface GigabitEthernet0/0/0/4
  address-family ipv4
   hello-interval 5
   hold-time 15
  !
  address-family ipv6
   hello-interval 5
   hold-time 15
  !
 !
 neighbor 100.0.0.33
  password clear dickie-lab
  session holdtime 180
 !
 neighbor 100.0.0.34
  password clear dickie-lab
  session holdtime 180
 !
 neighbor 100.0.0.36
  password clear dickie-lab
  session holdtime 180
 !
 neighbor 100.0.0.37
  password clear dickie-lab
  session holdtime 180
 !
 neighbor 100.0.0.38
  password clear dickie-lab
  session holdtime 180
 !
!

prefix-set LDP-PREFIXES
  100.0.0.0/16 le 32
end-set

interface GigabitEthernet0/0/0/0
 mpls ldp sync
!
interface GigabitEthernet0/0/0/1
 mpls ldp sync
!
interface GigabitEthernet0/0/0/2
 mpls ldp sync
!
interface GigabitEthernet0/0/0/3
 mpls ldp sync
!
interface GigabitEthernet0/0/0/4
 mpls ldp sync
!

!! Configure SR preference over LDP in OSPF
router ospf CORE
 segment-routing sr-prefer
!

commit
```

#### Task 3.2.2: Configure LDP on Y-RR1 (IOS-XE)

**Device: Y-RR1 (100.0.0.38)**

```
hostname Y-RR1

!! Configure MPLS LDP
mpls label protocol ldp
mpls ldp router-id Loopback0
mpls ldp password option 1 for 1 dickie-lab
mpls ldp session protection
mpls ldp discovery hello interval 5
mpls ldp discovery hello holdtime 15
mpls ldp graceful-restart

!! Enable MPLS on interfaces
interface GigabitEthernet1
 mpls ip
 mpls ldp sync

!! Configure SR preference over LDP
router ospf 1
 mpls ldp sync

!! LDP neighbors
mpls ldp neighbor 100.0.0.35 password dickie-lab
mpls ldp neighbor 100.0.0.33 password dickie-lab
mpls ldp neighbor 100.0.0.34 password dickie-lab
mpls ldp neighbor 100.0.0.36 password dickie-lab
mpls ldp neighbor 100.0.0.37 password dickie-lab
```

### Task 3.3: MPLS Label Operations and Verification

#### Task 3.3.1: Label Allocation and Forwarding

```
!! Check label allocations - Run on X-P1
show mpls ldp bindings    ! LDP label bindings
show mpls label table     ! Complete label table (SR + LDP)
show isis segment-routing label table    ! SR labels only

!! Compare SR vs LDP labels for same prefix - Run on X-PE1
show cef ipv4 100.0.0.27/32 detail    ! CEF shows which label type is preferred
show mpls forwarding-table 100.0.0.27/32    ! MPLS forwarding entry

!! Check LDP neighbors - Run on Y-P1
show mpls ldp neighbor    ! LDP neighbor status
show mpls ldp discovery   ! LDP discovery process

!! Verify label preference - Run on any X-domain device
show isis segment-routing label table | include "100.0.0.27"    ! SR label for X-P1
show mpls ldp bindings | include "100.0.0.27"    ! LDP label for X-P1
```

#### Task 3.3.2: MPLS Ping and Traceroute

```
!! Test MPLS connectivity using LDP labels - Run on X-PE1
ping mpls ipv4 100.0.0.27/32    ! Ping X-P1 using MPLS
traceroute mpls ipv4 100.0.0.27/32    ! Trace path to X-P1

!! Test MPLS connectivity using SR labels - Run on X-PE1  
ping mpls ipv4 100.0.0.27/32 force-explicit-null    ! Force explicit null
traceroute mpls sr-prefer ipv4 100.0.0.27/32    ! Trace using SR labels

!! Cross-AS MPLS connectivity test - Run on X-ASBR1
ping mpls ipv4 100.0.0.33/32    ! Ping Y-ASBR1 across AS boundary
traceroute mpls ipv4 100.0.0.33/32    ! Trace to Y-domain via BGP-LU
```

#### Task 3.3.3: LDP Synchronization Verification

```
!! Check LDP sync status - Run on X-P1
show isis interface GigabitEthernet0/0/0/0    ! ISIS interface with LDP sync
show mpls ldp interface GigabitEthernet0/0/0/0    ! LDP interface status

!! Verify LDP sync behavior - Run on Y-P1
show ospf interface GigabitEthernet0/0/0/0    ! OSPF interface with LDP sync
show mpls ldp interface GigabitEthernet0/0/0/0    ! LDP interface status

!! Check metric adjustment during LDP down - Run on any device
show isis topology    ! ISIS topology with LDP sync impact
show ospf database router    ! OSPF LSA with metric changes
```

### Task 3.4: Label Filtering and Security

#### Task 3.4.1: Configure LDP Label Filtering

**Device: X-ASBR1 (100.0.0.31)**

```
!! Configure outbound label filtering
mpls ldp
 address-family ipv4
  label remote accept for prefix-list ACCEPT-LABELS from 100.0.0.31
  label local advertise for prefix-list ADVERTISE-LABELS to 100.0.0.33
 !
!

!! Define prefix lists for label control
prefix-set ACCEPT-LABELS
  100.0.0.0/16 le 32
end-set

prefix-set ADVERTISE-LABELS
  100.0.0.21/32,
  100.0.0.22/32,
  100.0.0.27/32,
  100.0.0.25/32,
  100.0.0.26/32
end-set

commit
```

#### Task 3.4.2: LDP Authentication and Session Protection

```
!! Verify LDP authentication - Run on X-P1
show mpls ldp neighbor detail    ! Check password authentication status
show mpls ldp parameters    ! LDP global parameters

!! Test session protection - Run on Y-P1
show mpls ldp neighbor 100.0.0.36 detail    ! Check session protection to Y-PE1
show mpls ldp backoff    ! LDP backoff timers

!! Check LDP graceful restart - Run on any IOS-XR device
show mpls ldp graceful-restart    ! GR status and timers
show mpls ldp neighbor graceful-restart    ! Per-neighbor GR status
```

---

## Advanced MPLS Operations

### Task 3.5: TTL Propagation and Explicit Null

#### Task 3.5.1: Configure MPLS TTL Propagation

```
!! Configure TTL propagation - On all X-domain devices
mpls ip propagate-ttl

!! Configure explicit null labels - On specific devices
mpls ldp
 address-family ipv4
  label local advertise explicit-null
 !
!
```

#### Task 3.5.2: Static MPLS Labels

**Device: X-P1 (100.0.0.27)**

```
!! Configure static MPLS labels for testing
mpls static
 interface GigabitEthernet0/0/0/0
  address-family ipv4 unicast
   local-label 100000 forward-path 1.101.1.2 out-label 100001
  !
 !
!
```

### Task 3.6: MPLS OAM Configuration

#### Task 3.6.1: Enable MPLS OAM

```
!! Enable MPLS OAM globally - On all devices
mpls oam

!! Configure BFD for MPLS - On core interfaces
interface GigabitEthernet0/0/0/0
 bfd mode ietf
 bfd minimum-interval 300
 bfd multiplier 3
!

!! Test MPLS OAM - Run on X-PE1
ping mpls ipv4 100.0.0.22/32 timeout 5 repeat 10    ! Test to X-PE2
traceroute mpls ipv4 100.0.0.22/32 timeout 5    ! Trace to X-PE2
```

---

## Key Learning Points

### LDP vs Segment Routing Comparison

| Feature | LDP | Segment Routing |
|---------|-----|------------------|
| Label Allocation | Distributed per-hop | Centralized prefix-based |
| Convergence | Depends on IGP + LDP | IGP convergence only |
| Scalability | Session per neighbor | No sessions required |
| Path Control | Limited | Explicit path control |
| Operations | Complex troubleshooting | Simplified operations |
| Standards | RFC 5036 | RFC 8402, RFC 8660 |

### Protocol Preference in Dual-Label Environment

1. **Segment Routing** - Primary choice for forwarding
2. **LDP** - Backup labels available for fallback
3. **Static Labels** - Highest priority when configured
4. **BGP-LU** - Used for Inter-AS label distribution

This task establishes the complete MPLS foundation with both traditional LDP and modern Segment Routing, preparing the network for advanced MPLS services like L3VPN, L2VPN, and Traffic Engineering.