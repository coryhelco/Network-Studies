# EIGRP Over DMVPN

- DMVPN does not support multicast. It supports replicated unicast.

- Split Horizon
  - EIGRP is Distance Vector
    - Still relies on split horizon rule
      - When an update is sent to an interface the same update won't be sent back out the same interface
  - When not to use Split Horizon
    - Partial mesh NBMA
    - E.g. DMVPN
- Next Hop Processing
  - EIGRP updates includes "Next Hop Forwarding Address"
    - i.e. the next-hop for the route
  - If zero, use IP address from the received IP header
    - I.e. next-hop is who you learned the route from
  - Why would you not want this?
    - Third party next hop
    - E.g. DMVPN Phase 2

- Configuration
```router eigrp INE
 !
 address-family ipv4 unicast autonomous-system 300
  !
  af-interface Tunnel0
   no next-hop-self
   no split-horizon
  exit-af-interface
```
- DMVPN phase 2 uses the **no next-hop-self** to tell traffic not to go to its destination and to the DMVPN hub and then to it's destination

- Verification commands
  - show dmvpn
  - show ip nhrp