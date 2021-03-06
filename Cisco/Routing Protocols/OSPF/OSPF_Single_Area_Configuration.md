# OSPF Single Area Configuration
- Prerequisites
  - IP routing must be enabled
    - I.e. ip routing
  - Must be an "up/up" interface running IP
    - Used for OSPF Router-ID
- Adjacency formation
  - When there are 4 or more OSPF routers on a shared lan routers will only form adjacencies with the DR and BDR

## Configuring Basic OSPF
- Enable global OSPF process
  - router ospf [process-id]
    - Process-id locally significant
    - Exception is MPLS L3VPN
- Enable interface OSPF process
  - Process level
    - network [address][wildcard] area [area-id]
  - Interface level
    - ip ospf [process-id] area [area-id]
- OSPF Network Statement
  - Useful for enabling OSPF on multiple interfaces
    - Wildcard mask does not relate to subnet mask
  - Most specific match determines the area
    - network 0.0.0.0 255.255.255.255 area 0
    - network 1.0.0.0 0.255.255.255 area 1
    - network 1.2.0.0 0.0.255.255 area 2
    - network 1.2.3.0 0.0.0.255 area 3
    - network 1.2.3.4 0.0.0.0 area 4
- OSPF Interface Statement
    - Enables OSPF on the primary and secondary IP address
      - Secondary advertisement can be disabled
    - OSPF stays enabled even if IP address changes
  ```conf t
    int g0/0
    ip ospf 1 area 0
    end
    ```
- Reference bandwidth
  - router ospf 1
    - auto-cost reference-bandwidth ?
- OSPF config when the routers are point-to-point
  - int g0/1
    - ip ospf network point-to-point

## LSA Types
- Type 1 (Router Link State)
  - Most important since it describes how we route to the node in the graph and what the attributes are of the links that node owns
  - Type 1 LSAs are generated by all routers
- Type 2 (Net Link State)
  - Type 2 LSAs are generated by Designated Routers
  - Describes where the other nodes are on a particular segment

## Verifying OSPF Adjacencies
- Verify OSPF is enabled
  - show ip ospf
  - show ip ospf interface [brief]
- Verify OSPF adjacencies
  - show ip ospf neighbor
  - debug ip ospf adj

## Verifying the OSPF Database
- Verify OSPF database
  - show ip ospf database [router | network | summary | ...]
    - Shows the Link State Database (LSDB)
      - LSDB -> SPF process -> Shortest Path Tree (SPT) = RIB for OSPF