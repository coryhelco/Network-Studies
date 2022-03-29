# Configuring OSPF Network Types
- int g0/1
  - ip ospf network [broadcast, non-broadcast, point-to-point, point-to-multipoint, point-to-multipoint non-broadcast]
    - Make sure attributes such as timers match on both sides of the link
## Modifying OSPF DR/BDR Election
- OSPF router on network will become a BDR if there is not already a BDR/DR on the network.
  - It will then promote itself to DR after 'wait' timer expires if no other router takes DR
- Make sure on DMVPN that the hub is the DR if running broadcast
  - Set the OSPF priority to 0 on the DMVPN spokes
- 

## Modifying OSPF Network Types
- OSPF Network Point-to-Point
  - Default on point-to-point medias
    - E.g. HDLC, PPP, GRE
  - Sends hellos as multicast
    - 224.0.0.5
  - No DR/BDR Election
  - Supports only two neighbors on the link
- OSPF Network Point-to-Multipoint
  - Treat network as a collection of P2P links
  - Sends hellos as multicast
    - 224.0.0.5
  - No DR/BDR Election
  - Special next-hop processing
  - Usually the best design option for partial mesh NBMA networks
## Verifying the OSPF Database
- show ip ospf database
- show ip ospf database router
- debug ip ospf [packet|hello|event|adj]
- 

## Verifying Next Hop Processing
- show ip ospf int g0/1

## Verifying OSPF Network Type Design Issues