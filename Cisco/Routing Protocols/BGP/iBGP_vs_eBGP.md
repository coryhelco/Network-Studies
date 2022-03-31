# iBGP vs EBGP

## iBGP vs EBGP Transport Establishment
- iBGP packets default to TTL 255
  - Implies neighbors do not have to be connected as long as IGP 
- EBGP packets default to TTL 1
  - Can be modified if neighbors are multiple hops away
    - neighbor ebgp-multihop [ttl]
    - 

## iBGP vs EBGP Loop Prevention
### iBGP
- Loop prevention via route filtering
  - iBGP learned routes cannot be advertised on to another iBGP neighbor
- Implies iBGP requires either:
  - Fully meshed iBGP peerings
  - Route Reflectors
  - Confederation
- Pros and Cons to all three designs

### EBGP
- Loop prevention via AS-Path
  - Local ASN is "prepended" to outbound updates
  - Inbound updates containing local ASN are discarded
  - Can be modified with ```neighbor allowas-in``` or ```as-override```

## BGP Next-Hop Significance
- BGP is not a routing protocol
  - BGP is an application used to exchange NLRI
  - E.g. the route details but not the path details
- IPv4 NLRI contains:
  - Prefix/Len
  - Attributes
    - Local-pref, AS-Path, MED, etc.
  - Next-Hop
- BGP Next-Hop controls IGP route recursion
  - I.e. BGP knows the next-hop but not the outgoing interface
  - IGP must be able to perform recursion otherwise the route cannot be used
- Result of failed next-hop recursion is failed bestpath selection
  - I.e. If I don't know the next-hop the route is not installed in RIB or advertised
- Why do we care?
  - Different peerings have different next-hop processing rules



## iBGP Next-Hop Processing
- By Default:
  - Outbound iBGP updates do not modify the next-hop attribute regardless of iBGP peer type
- Can be modified:
    - **neighbor next-hop-self**
    - route-map action set ip next-hop
    - As of 15.1(1)SY, **next-hop-self ALL** for inserting RR in the data-path
      - Used in Unified MPLS design
## iBGP Full Mesh
- iBGP performs loop prevention via route filtering
  - iBGP learned routes cannot be advertised on to another iBGP neighbor
- Implies iBGP requires either:
  - Fully meshed iBGP peerings
  - Route Reflectors
  - Confederation

## Full Mesh Design Advantages
- Path Diversity
  - All BGP peers learn all possible egress paths
- Optimal Traffic Flows
  - All BGP Peers learn the closest egress path
  - Path selection by default would be based on IGP metric to egress router
  - I.e. Hot Potato Routing
## Full Mesh Design Disadvantages
- Control Plane Scaling is exponential
  - Full mesh means n*(n-1)/2 peerings
  - 10 routers = 45 peerings
  - 100 routers = 4950 peerings
  - 500 routers = 124750 peerings
- Operationally hard to scale
  - Adding or changing peering config is administratively prohibitive
  - Could be automated, but few out of the box solutions
## Partial Mesh Designs
- You don't need to only choose one design
  - E.g. full mesh, RR, and Confed can interoperate
- Why would you do this?
  - Pockets of full mesh for path diversity
  - Inter-cluster RR for scaling larger