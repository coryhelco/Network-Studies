# BGP Peering Best Practices

- iBGP Peering Best Practices
  - Peer using Loopback interfaces to increase BGP stability
    - Allows BGP to reroute around an IGP link failure
  - Reset BGP next-hops on the edge
    - Alternative to IGP advertising the EBGP peering links
- EBGP Peering Best Practices
  - Protect the control plane with MD5 authentication or TTL Security
    - Protects against TCP Reset Attacks
  - Apply inbound and outbound filters to limit misconfiguration effects
    - IOS XR does this by default
  - Adjust timers or run BFD on peering links to reduce convergence time
    - Default BGP hold time is 3 minutes

## Authentication and TTL Security Config
- router bgp 200
  - neighbor 10.3.5.3 password CISCO
  - neighbor 10.3.5.3 ttl-security 1


## Apply Inbound/Outbound filters on IOS-XR
```
route-policy AS_65010_INBOUND
  if destination in (10.10.10.10/32) then
    pass
  endif
end-policy

route-policy AS_65010_OUTBOUND
  pass
end-policy

router bgp 200
 address-family ipv4 unicast
  network 8.8.8.8/32
 !
 neighbor 7.7.7.7
  remote-as 200
  update-source Loopback0
  address-family ipv4 unicast
  !
 !
 neighbor 10.8.10.10
  remote-as 65010
  address-family ipv4 unicast
   route-policy AS_65010_INBOUND in
   route-policy AS_65010_OUTBOUND out
```

## Change hello/hold timers
- neighbor 10.3.5.5 timers 1 3
  - Uses the CPU instead of line cards
    - BFD is better to use
## BFD (Do on both ends of link)
- int g0/3
  - bfd interval 250 min_rx 250 multiplier 4
  - bfd echo
- router bgp 100
  - neighbor 10.3.5.5 fall-over bfd
### BFD Verification
- show bfd summary
- show bfd drops
- show bfd neighbor
- show bfd summary
- show bfd neighbor detail