# BGP Configuration
## iBGP
```
conf t
router bgp 100
neighbor 1.1.1.1 remote-as 100
neighbor 1.1.1.1 update-source lo0
network 2.2.2.2 mask 255.255.255.255

```

## EBGP
```
router bgp 200
 bgp log-neighbor-changes
 neighbor 7.7.7.7 remote-as 200
 neighbor 7.7.7.7 update-source Loopback0
 neighbor 10.3.5.3 remote-as 100
 neighbor 10.3.5.3 ttl-security hops 1
 neighbor 10.3.5.3 password CISCO
 neighbor 10.3.5.3 fall-over bfd
 !
 address-family ipv4
  bgp advertise-best-external
  network 5.5.5.5 mask 255.255.255.255
  neighbor 7.7.7.7 activate
  neighbor 7.7.7.7 next-hop-self
  neighbor 7.7.7.7 additional-paths receive
  neighbor 10.3.5.3 activate
  neighbor 10.3.5.3 remove-private-as
 exit-address-family

```
## Verification
- show ip bgp
- show control-plane open-ports
- show ip bgp summary
- show ip bgp neighbor
- 