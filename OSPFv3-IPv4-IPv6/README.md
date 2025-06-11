# Advanced OSPFv3 Lab - IPv4 and IPv6 (CCNP ENCOR)

## Lab Topology

```
                    Area 0 (Backbone)
                         |
    Area 1 -------- [ABR-R1] -------- [ABR-R2] -------- Area 2
        |              |                  |                |
    [R3-ASBR]      [R4-IR]            [R5-IR]         [R6-ASBR]
        |                                                  |
   External          Area 1              Area 2        External
   Networks                                            Networks
```

#### R3 (ASBR - Area 1) Configuration

```cisco
! Enable IPv6 routing
ipv6 unicast-routing

! Configure interfaces
interface GigabitEthernet0/1
 description Link to R1 (Area 1)
 ip address 10.1.1.3 255.255.255.0
 ipv6 address 2001:DB8:1:1::3/64
 ospfv3 1 ipv4 area 1
 ospfv3 1 ipv6 area 1
 no shutdown

interface GigabitEthernet0/2
 description Link to R4 (Area 1)
 ip address 10.1.3.3 255.255.255.0
 ipv6 address 2001:DB8:1:3::3/64
 ospfv3 1 ipv4 area 1
 ospfv3 1 ipv6 area 1
 no shutdown

interface GigabitEthernet0/3
 description External Network
 ip address 192.168.1.1 255.255.255.0
 ipv6 address 2001:DB8:A::1/64
 no shutdown

interface Loopback0
 ip address 3.3.3.3 255.255.255.255
 ipv6 address 2001:DB8:3333::3/128
 ospfv3 1 ipv4 area 1
 ospfv3 1 ipv6 area 1

! OSPFv3 Configuration
router ospfv3 1
 address-family ipv4 unicast
  router-id 3.3.3.3
 address-family ipv6 unicast
  router-id 3.3.3.3
```

#### R4 (Internal Router - Area 1) Configuration

```cisco
! Enable IPv6 routing
ipv6 unicast-routing

! Configure interfaces
interface GigabitEthernet0/1
 description Link to R3 (Area 1)
 ip address 10.1.3.4 255.255.255.0
 ipv6 address 2001:DB8:1:3::4/64
 ospfv3 1 ipv4 area 1
 ospfv3 1 ipv6 area 1
 no shutdown

interface GigabitEthernet0/2
 description Link to R1 (Area 1)
 ip address 10.1.2.4 255.255.255.0
 ipv6 address 2001:DB8:1:2::4/64
 ospfv3 1 ipv4 area 1
 ospfv3 1 ipv6 area 1
 no shutdown

interface Loopback0
 ip address 4.4.4.4 255.255.255.255
 ipv6 address 2001:DB8:4444::4/128
 ospfv3 1 ipv4 area 1
 ospfv3 1 ipv6 area 1

! OSPFv3 Configuration
router ospfv3 1
 address-family ipv4 unicast
  router-id 4.4.4.4
 address-family ipv6 unicast
  router-id 4.4.4.4
```

#### R5 (Internal Router - Area 2) Configuration

```cisco
! Enable IPv6 routing
ipv6 unicast-routing

! Configure interfaces
interface GigabitEthernet0/1
 description Link to R2 (Area 2)
 ip address 10.2.1.5 255.255.255.0
 ipv6 address 2001:DB8:2:1::5/64
 ospfv3 1 ipv4 area 2
 ospfv3 1 ipv6 area 2
 no shutdown

interface GigabitEthernet0/2
 description Link to R6 (Area 2)
 ip address 10.2.3.5 255.255.255.0
 ipv6 address 2001:DB8:2:3::5/64
 ospfv3 1 ipv4 area 2
 ospfv3 1 ipv6 area 2
 no shutdown

interface Loopback0
 ip address 5.5.5.5 255.255.255.255
 ipv6 address 2001:DB8:5555::5/128
 ospfv3 1 ipv4 area 2
 ospfv3 1 ipv6 area 2

! OSPFv3 Configuration
router ospfv3 1
 address-family ipv4 unicast
  router-id 5.5.5.5
 address-family ipv6 unicast
  router-id 5.5.5.5
```

#### R6 (ASBR - Area 2) Configuration

```cisco
! Enable IPv6 routing
ipv6 unicast-routing

! Configure interfaces
interface GigabitEthernet0/1
 description Link to R5 (Area 2)
 ip address 10.2.3.6 255.255.255.0
 ipv6 address 2001:DB8:2:3::6/64
 ospfv3 1 ipv4 area 2
 ospfv3 1 ipv6 area 2
 no shutdown

interface GigabitEthernet0/2
 description Link to R2 (Area 2)
 ip address 10.2.2.6 255.255.255.0
 ipv6 address 2001:DB8:2:2::6/64
 ospfv3 1 ipv4 area 2
 ospfv3 1 ipv6 area 2
 no shutdown

interface GigabitEthernet0/3
 description External Network
 ip address 192.168.2.1 255.255.255.0
 ipv6 address 2001:DB8:B::1/64
 no shutdown

interface Loopback0
 ip address 6.6.6.6 255.255.255.255
 ipv6 address 2001:DB8:6666::6/128
 ospfv3 1 ipv4 area 2
 ospfv3 1 ipv6 area 2

! OSPFv3 Configuration
router ospfv3 1
 address-family ipv4 unicast
  router-id 6.6.6.6
 address-family ipv6 unicast
  router-id 6.6.6.6


### Physical Connections
| Link | Router A | Interface | Router B | Interface | Area |
|------|----------|-----------|----------|-----------|------|
| Link 1 | R1 | Gi0/0 | R2 | Gi0/0 | Area 0 |
| Link 2 | R1 | Gi0/1 | R3 | Gi0/1 | Area 1 |
| Link 3 | R1 | Gi0/2 | R4 | Gi0/2 | Area 1 |
| Link 4 | R3 | Gi0/2 | R4 | Gi0/1 | Area 1 |
| Link 5 | R2 | Gi0/1 | R5 | Gi0/1 | Area 2 |
| Link 6 | R2 | Gi0/2 | R6 | Gi0/2 | Area 2 |
| Link 7 | R5 | Gi0/2 | R6 | Gi0/1 | Area 2 |

## Device Specifications

- **R1**: Area Border Router (ABR) - Area 0/Area 1
- **R2**: Area Border Router (ABR) - Area 0/Area 2
- **R3**: Autonomous System Boundary Router (ASBR) - Area 1
- **R4**: Internal Router (IR) - Area 1
- **R5**: Internal Router (IR) - Area 2
- **R6**: Autonomous System Boundary Router (ASBR) - Area 2

## IP Addressing Scheme

### IPv4 Addressing Table
| Network Segment | IPv4 Network | Router A IP | Router B IP | Area |
|-----------------|--------------|-------------|-------------|------|
| R1-R2 (Backbone) | 10.0.0.0/24 | 10.0.0.1 | 10.0.0.2 | Area 0 |
| R1-R3 | 10.1.1.0/24 | 10.1.1.1 | 10.1.1.3 | Area 1 |
| R1-R4 | 10.1.2.0/24 | 10.1.2.1 | 10.1.2.4 | Area 1 |
| R3-R4 | 10.1.3.0/24 | 10.1.3.3 | 10.1.3.4 | Area 1 |
| R2-R5 | 10.2.1.0/24 | 10.2.1.2 | 10.2.1.5 | Area 2 |
| R2-R6 | 10.2.2.0/24 | 10.2.2.2 | 10.2.2.6 | Area 2 |
| R5-R6 | 10.2.3.0/24 | 10.2.3.5 | 10.2.3.6 | Area 2 |
| R3 External | 192.168.1.0/24 | 192.168.1.1 | - | External |
| R6 External | 192.168.2.0/24 | 192.168.2.1 | - | External |

### IPv6 Addressing Table
| Network Segment | IPv6 Network | Router A IP | Router B IP | Area |
|-----------------|--------------|-------------|-------------|------|
| R1-R2 (Backbone) | 2001:DB8::/64 | ::1 | ::2 | Area 0 |
| R1-R3 | 2001:DB8:1:1::/64 | ::1 | ::3 | Area 1 |
| R1-R4 | 2001:DB8:1:2::/64 | ::1 | ::4 | Area 1 |
| R3-R4 | 2001:DB8:1:3::/64 | ::3 | ::4 | Area 1 |
| R2-R5 | 2001:DB8:2:1::/64 | ::2 | ::5 | Area 2 |
| R2-R6 | 2001:DB8:2:2::/64 | ::2 | ::6 | Area 2 |
| R5-R6 | 2001:DB8:2:3::/64 | ::5 | ::6 | Area 2 |
| R3 External | 2001:DB8:A::/64 | ::1 | - | External |
| R6 External | 2001:DB8:B::/64 | ::1 | - | External |

### Loopback Addresses
| Router | IPv4 Loopback | IPv6 Loopback | Router ID |
|--------|---------------|---------------|-----------|
| R1 | 1.1.1.1/32 | 2001:DB8:1111::1/128 | 1.1.1.1 |
| R2 | 2.2.2.2/32 | 2001:DB8:2222::2/128 | 2.2.2.2 |
| R3 | 3.3.3.3/32 | 2001:DB8:3333::3/128 | 3.3.3.3 |
| R4 | 4.4.4.4/32 | 2001:DB8:4444::4/128 | 4.4.4.4 |
| R5 | 5.5.5.5/32 | 2001:DB8:5555::5/128 | 5.5.5.5 |
| R6 | 6.6.6.6/32 | 2001:DB8:6666::6/128 | 6.6.6.6 |

## Lab Objectives

1. **Basic OSPFv3 Configuration**
   - Configure OSPFv3 for IPv4 and IPv6
   - Establish neighbor relationships
   - Verify LSDB synchronization

2. **Multi-Area OSPF Design**
   - Configure Area 0 (backbone)
   - Configure stub areas and NSSA
   - Implement area filtering

3. **Advanced OSPF Features**
   - Route summarization
   - Virtual links
   - Authentication
   - LSA filtering

4. **Network Optimization**
   - Configure OSPF timers
   - Implement load balancing
   - Path manipulation

## Configuration Tasks

### Task 1: Basic OSPFv3 Configuration

#### R1 (ABR - Area 0/1) Configuration

```cisco
! Enable IPv6 routing
ipv6 unicast-routing

! Configure interfaces
interface GigabitEthernet0/0
 description Link to R2 (Area 0)
 ip address 10.0.0.1 255.255.255.0
 ipv6 address 2001:DB8::1/64
 ospfv3 1 ipv4 area 0
 ospfv3 1 ipv6 area 0
 no shutdown

interface GigabitEthernet0/1
 description Link to R3 (Area 1)
 ip address 10.1.1.1 255.255.255.0
 ipv6 address 2001:DB8:1:1::1/64
 ospfv3 1 ipv4 area 1
 ospfv3 1 ipv6 area 1
 no shutdown

interface GigabitEthernet0/2
 description Link to R4 (Area 1)
 ip address 10.1.2.1 255.255.255.0
 ipv6 address 2001:DB8:1:2::1/64
 ospfv3 1 ipv4 area 1
 ospfv3 1 ipv6 area 1
 no shutdown

interface Loopback0
 ip address 1.1.1.1 255.255.255.255
 ipv6 address 2001:DB8:1111::1/128
 ospfv3 1 ipv4 area 1
 ospfv3 1 ipv6 area 1

! OSPFv3 Configuration
router ospfv3 1
 address-family ipv4 unicast
  router-id 1.1.1.1
  area 1 range 10.1.0.0 255.255.0.0
 address-family ipv6 unicast
  router-id 1.1.1.1
  area 1 range 2001:DB8:1::/48
```

#### R2 (ABR - Area 0/2) Configuration

```cisco
! Enable IPv6 routing
ipv6 unicast-routing

! Configure interfaces
interface GigabitEthernet0/0
 description Link to R1 (Area 0)
 ip address 10.0.0.2 255.255.255.0
 ipv6 address 2001:DB8::2/64
 ospfv3 1 ipv4 area 0
 ospfv3 1 ipv6 area 0
 no shutdown

interface GigabitEthernet0/1
 description Link to R5 (Area 2)
 ip address 10.2.1.2 255.255.255.0
 ipv6 address 2001:DB8:2:1::2/64
 ospfv3 1 ipv4 area 2
 ospfv3 1 ipv6 area 2
 no shutdown

interface GigabitEthernet0/2
 description Link to R6 (Area 2)
 ip address 10.2.2.2 255.255.255.0
 ipv6 address 2001:DB8:2:2::2/64
 ospfv3 1 ipv4 area 2
 ospfv3 1 ipv6 area 2
 no shutdown

interface Loopback0
 ip address 2.2.2.2 255.255.255.255
 ipv6 address 2001:DB8:2222::2/128
 ospfv3 1 ipv4 area 2
 ospfv3 1 ipv6 area 2

! OSPFv3 Configuration
router ospfv3 1
 address-family ipv4 unicast
  router-id 2.2.2.2
  area 2 range 10.2.0.0 255.255.0.0
 address-family ipv6 unicast
  router-id 2.2.2.2
  area 2 range 2001:DB8:2::/48
```

### Task 2: Configure Stub Areas and NSSA

#### Configure Area 1 as NSSA

```cisco
! R1 Configuration
router ospfv3 1
 address-family ipv4 unicast
  area 1 nssa default-information-originate
 address-family ipv6 unicast
  area 1 nssa default-information-originate

! R3 Configuration
router ospfv3 1
 address-family ipv4 unicast
  area 1 nssa
 address-family ipv6 unicast
  area 1 nssa

! R4 Configuration
router ospfv3 1
 address-family ipv4 unicast
  area 1 nssa
 address-family ipv6 unicast
  area 1 nssa
```

#### Configure Area 2 as Totally Stubby

```cisco
! R2 Configuration (ABR)
router ospfv3 1
 address-family ipv4 unicast
  area 2 stub no-summary
 address-family ipv6 unicast
  area 2 stub no-summary

! R5 and R6 Configuration
router ospfv3 1
 address-family ipv4 unicast
  area 2 stub
 address-family ipv6 unicast
  area 2 stub
```

### Task 3: ASBR Configuration and Redistribution

#### R3 (ASBR) Configuration

```cisco
! External interface
interface GigabitEthernet0/3
 description External Network
 ip address 192.168.1.1 255.255.255.0
 ipv6 address 2001:DB8:A::1/64
 no shutdown

! Static routes for external networks
ip route 192.168.1.0 255.255.255.0 Null0
ipv6 route 2001:DB8:A::/64 Null0

! Redistribution
router ospfv3 1
 address-family ipv4 unicast
  redistribute static subnets route-map IPv4-REDIST
 address-family ipv6 unicast
  redistribute static route-map IPv6-REDIST

! Route maps for redistribution
route-map IPv4-REDIST permit 10
 match ip address prefix-list IPv4-EXTERNAL
 set metric 100
 set metric-type 1

route-map IPv6-REDIST permit 10
 match ipv6 address prefix-list IPv6-EXTERNAL
 set metric 100
 set metric-type 1

#### R6 (ASBR) Configuration

```cisco
! External interface
interface GigabitEthernet0/3
 description External Network
 ip address 192.168.2.1 255.255.255.0
 ipv6 address 2001:DB8:B::1/64
 no shutdown

! Static routes for external networks
ip route 192.168.2.0 255.255.255.0 Null0
ipv6 route 2001:DB8:B::/64 Null0

! Redistribution
router ospfv3 1
 address-family ipv4 unicast
  redistribute static subnets route-map IPv4-REDIST-R6
 address-family ipv6 unicast
  redistribute static route-map IPv6-REDIST-R6

! Route maps for redistribution
route-map IPv4-REDIST-R6 permit 10
 match ip address prefix-list IPv4-EXTERNAL-R6
 set metric 200
 set metric-type 2

route-map IPv6-REDIST-R6 permit 10
 match ipv6 address prefix-list IPv6-EXTERNAL-R6
 set metric 200
 set metric-type 2

ip prefix-list IPv4-EXTERNAL-R6 permit 192.168.2.0/24
ipv6 prefix-list IPv6-EXTERNAL-R6 permit 2001:DB8:B::/64
```
```

### Task 4: Authentication Configuration

#### OSPFv3 Authentication

```cisco
! Create key chain
key chain OSPF-AUTH
 key 1
  key-string Cisco123!
  cryptographic-algorithm hmac-sha-256

! Apply to interfaces
interface GigabitEthernet0/0
 ospfv3 authentication key-chain OSPF-AUTH
```

### Task 5: Advanced Features

#### Virtual Link Configuration (if needed)

```cisco
! If Area 0 connectivity is broken
router ospfv3 1
 address-family ipv4 unicast
  area 1 virtual-link 2.2.2.2
 address-family ipv6 unicast
  area 1 virtual-link 2.2.2.2
```

#### OSPF Timer Optimization

```cisco
interface GigabitEthernet0/0
 ospfv3 hello-interval 5
 ospfv3 dead-interval 20
```

#### LSA Filtering

```cisco
router ospfv3 1
 address-family ipv4 unicast
  area 1 filter-list prefix FILTER-LSA in
 address-family ipv6 unicast
  area 1 filter-list prefix FILTER-LSA-v6 in

ip prefix-list FILTER-LSA deny 10.2.0.0/16 le 32
ip prefix-list FILTER-LSA permit 0.0.0.0/0 le 32

ipv6 prefix-list FILTER-LSA-v6 deny 2001:DB8:2::/48 le 128
ipv6 prefix-list FILTER-LSA-v6 permit ::/0 le 128
```

## Verification Commands

### Basic Verification

```cisco
! Neighbor verification
show ospfv3 neighbor
show ospfv3 neighbor detail

! Database verification
show ospfv3 database
show ospfv3 database router
show ospfv3 database network
show ospfv3 database summary
show ospfv3 database external

! Routing table verification
show ip route ospf
show ipv6 route ospf

! Interface verification
show ospfv3 interface
show ospfv3 interface brief
```

### Advanced Verification

```cisco
! Area-specific verification
show ospfv3 database area 0
show ospfv3 database area 1
show ospfv3 database area 2

! LSA filtering verification
show ospfv3 database summary | include 10.2.0.0

! Virtual link verification
show ospfv3 virtual-links

! Authentication verification
show ospfv3 interface authentication

! Border router verification
show ospfv3 border-routers
```

## Troubleshooting Scenarios

### Scenario 1: Neighbor Adjacency Issues

**Problem**: Neighbors stuck in INIT state

**Troubleshooting Steps**:
1. Verify interface configuration
2. Check area assignments
3. Verify authentication
4. Check MTU settings
5. Verify timers

```cisco
debug ospfv3 hello
debug ospfv3 adj
show ospfv3 interface
show ospfv3 neighbor detail
```

### Scenario 2: Missing Routes

**Problem**: Routes not appearing in routing table

**Troubleshooting Steps**:
1. Verify LSDB synchronization
2. Check area filtering
3. Verify summarization
4. Check redistribution

```cisco
show ospfv3 database
show ip route ospf
show ospfv3 summary-address
```

### Scenario 3: Suboptimal Routing

**Problem**: Traffic taking non-optimal paths

**Troubleshooting Steps**:
1. Check OSPF costs
2. Verify equal-cost load balancing
3. Check area design
4. Verify route summarization

```cisco
show ospfv3 interface cost
show ip route ospf | include [2-9]/
```

## Lab Exercises

### Exercise 1: Basic Connectivity
- Configure all routers with basic OSPFv3
- Verify full connectivity between all loopbacks
- Document the LSDB contents

### Exercise 2: Area Optimization
- Configure appropriate area types
- Implement route summarization
- Verify reduced LSDB size in stub areas

### Exercise 3: External Route Injection
- Configure external route redistribution
- Implement route filtering
- Verify external route propagation

### Exercise 4: Path Manipulation
- Modify interface costs
- Configure unequal cost load balancing
- Verify traffic flow changes

### Exercise 5: Failure Simulation
- Simulate link failures
- Verify convergence time
- Test virtual link functionality

## CCNP ENCOR Exam Topics Covered

1. **OSPF Configuration and Verification**
   - Multi-area OSPF design
   - OSPF network types
   - OSPF authentication

2. **OSPF Optimization**
   - Route summarization
   - Stub areas and NSSA
   - LSA filtering

3. **OSPF Troubleshooting**
   - Neighbor adjacency issues
   - Routing table problems
   - Convergence issues

4. **IPv6 Routing**
   - OSPFv3 configuration
   - IPv6 address families
   - Dual-stack operations

This comprehensive lab covers all major OSPFv3 concepts required for CCNP ENCOR certification while providing hands-on experience with both IPv4 and IPv6 implementations.
