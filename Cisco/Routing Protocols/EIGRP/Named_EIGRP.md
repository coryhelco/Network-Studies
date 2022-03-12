# Named EIGRP

- New EIGRP configuration as of IOS 15.x
- Better config hierarchy than Classic mode
  - All config now goes under global process
- Better IPv4 and IPv6 feature parity
  - Syntax is unified for IPv4 and IPv6 under one process
  - Uses address-families to distinguish v4 and v6
- Newer Features aren't supported in classic mode
  - E.g. Wide Metrics, IPv6 VRF Lite, etc.


- Configuration
  - router eigrp [name]
  - address-family ipv4 unicast autonomous-system 1
  - network [address][wildcard]
    - Customize the interfaces
      - af-interface [default | int]
        - default applies to all interfaces regardless if new links are added to the topology
    
  - Authentication for classic mode
    - key chain KEYS
    - key 1
    - key-string INE
      - ip authentication mode eigrp 1 md5 
      - ip authentication key-chain eigrp 1 KEYS
  - Authentication for named mode
    - key chain KEYS
    - key 1
    - key-string INE
      - router eigrp A 
      - address-family ipv4 unicast autonomous-system 1
      - af-interface GigabitEthernet0/1 
      - authentication mode md5 
      - authentication key-chain KEYS

- Configuration options for EIGRP
  - passive interface default
    - use no passive-interface on interfaces that you want advertising EIGRP
  - bfd (bi-directional forwarding detection)
    - good to enable on all interfaces
  - topology base
    - Related to multi-topology routing
    - Kinda like MPLS traffic engineering
    - Can have multiple instances of the routing process
    - 