## Campus and Lan Networking

### Etherchannels

- Root Bridge
  - All ports will be forwarding 
  - Root Port on non-root switch will be forwarding
  - IEEE 802.3ad standard
  - Up to 8 links can be in the channel
- Can be used to bundle:
  - L2 Access Ports
  - L2 VLAN Trunks
  - L3 Routed Ports
- Load-Balancing
  - Etherchannels do not fragment layer-2 frames
  - Load-balancing accomplished per-flow of traffic
  - Frames belonging to the same flow always traverse the same physical link
  - Methods for defining a flow
    - port-channel load-balance ?
      - src-mac load-balancing will always send data over the same link with the source mac
      - Other options are (dst-ip, dst-mac, src-dst-ip, src-dst-mac, src-ip)
- PAgP
  - Cisco Proprietary
  - Modes (Auto/Desirable)
- LACP
  - IEEE Standard (802.3ad)
  - Supports up to 8 active links
  - Supports hot-standby links as well in case some of the links fail
  - LACP Priority & ID
    - Port Priorities Default (32768)
    - Lower Priority is better
    - Switch will also have System Priority
- Layer 3 Etherchannels
  - 