# OSPF Single Area Configuration
- Prerequisites
  - IP routing must be enabled
    - I.e. ip routing
  - Must be an "up/up" interface running IP
    - Used for OSPF Router-ID

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

## Verifying OSPF Adjacencies
- Verify OSPF is enabled
  - show ip ospf
  - show ip ospf interface [brief]
- Verify OSPF adjacencies
  - show ip ospf neighbor
  - debug ip ospf adj
- Verify OSPF database
  - show ip ospf database [router | network | summary | ...]
  - 

## Verifying the OSPF Database