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


## iBGP vs EBGP Next-Hop-Processing
### iBGP Next-Hop Processing
- By Default:
  - Outbound iBGP updates do not modify the next-hop attribute regardless of iBGP peer type
- Can be modified:
  - **neighbor next-hop-self**
  - route-map action set ip next-hop
  - As of 15.1(1)SY, **next-hop-self ALL** for inserting RR in the data-path
    - Used in Unified MPLS design
### EBGP Next-Hop Processing
- 