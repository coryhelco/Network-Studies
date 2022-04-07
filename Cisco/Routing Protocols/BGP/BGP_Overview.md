# BGP Overview

- [RFC 4456 - BGP Route Reflection: An Alternative to Full Mesh Internal BGP](http://tools.ietf.org/html/rfc4456)
- RFC 4271 Section 9.1 "Decision Process"
- BGP uses TCP (179) and unicast
- OSPF/EIGRP use their own protocol
- Routes vs NLRI
  - IGP protocols exchange unicast routes
  - BGP also exchanges unicast routes, but can also exchange other types of information
  - For this reason we say BGP exchanges NLRI (Network-Layer Reachability Information)

## What is BGP?
- Open standards based path-vector exterior gateway protocol
  - RFC 4271 "A Border Gateway Protocol 4 (BGP-4)"
- Path Vector
  - Uses multiple "Attributes" for routing decision
- Exterior Gateway Protocol
  - Used to route traffic between autonomous systems
  - I.e. the Internet routing protocol
- The Most scalable and extensible protocol (so far)
- Highly Scalable
  - Over 800,000 IPv4 prefixes and growing
  - IPv6 space is growing but currently negligible
  - See http://bgp.potaroo.net for table growth stats
- Highly extensible via AFI/SAFI
  - Much more than just IPv4 Unicast routing
  - IPv6 Unicast, IPv4 & IPv6 multicast, IPv4 & IPv6 MPLS, MDT, VPLS & many others

## What is BGP Not?
- BGP is not a routing protocol per-se
  - BGP is an application
  - I.e. BGP is a reachability protocol
- Why the distinction?
  - BGP (generally) cannot route the network alone
  - BGP (generally) relies on IGP for transport and recursion
## IGP vs BGP
### IGP
- IGP has visibility of the topology
  - E.g. SPF and DUAL
- IGP decides on link attributes
  - I.e. cost belongs to link, not prefix
- IGP policy application is hard
  - TE hard to implement with IGP
### BGP
- BGP (generally) does not have topology visibility
  - BGP relies on IGP as a transport
- BGP decides on prefix vector attributes
  - I.e. attribute belongs to prefix, not link
- BGP policy application is easy
  - TE is simple per prefix

## Why Use BGP?
- Policy Application
- Easy to influence egress traffic
  - I always choose how I route out
- Easy to influence ingress traffic
  - I (generally) choose how traffic returns
  - Sometimes difficult on Internet scale
## When To Use BGP or Not
- Who owns the address space?
- Provider Aggregatable (PA) addresses
  - ISP owns your addresses
  - They (generally) dictate the policy
  - BGP Not Needed
- Provider Independent (PI) addresses
  - You own your addresses and BGP ASN
  - You (Generally) dictate the policy
  - BGP is needed
## Other BGP Considerations
- Do I really need a full view?
  - 800,000+ IPv4 prefixes
  - Does RIB have enough memory
  - Can FIB actually install it at the linecard
- Egress and Ingress policies are unrelated
  - Default out is ok
  - Influence traffic back in still allowed
- ## Other BGP Use Cases
- Scaling the Enterprise
  - Islands of IGP, Core of BGP
- Scaling DMVPN
  - Hubs as BGP RRs
  - BGP hierarchy is arbitrary
- Scaling Data Center Fabric
  - Use of BGP for routing in large-scale data centers
  - [Use of BGP for routing in large-scale data centers](https://datatracker.ietf.org/doc/html/draft-lapukhov-bgp-routing-large-dc-07)
- 

## iBGP & eBGP Overview

- A BGP router behaves differently in several ways depending on whether the peer (neighbor) is an iBGP or eBGP peer.
- eBGP imposes certain rules/restrictions not imposed by iBGP
- Prefix Exchange
  - BGP updates received from external peers can be forwarded on to any other type of peer
  - BGP updates received from internal peers can ONLY be forwarded on to external peers
- Update Modification
  - Certain BGP Path Attributes may only be forwarded to external... or internal peers
    - Can't send local preference to external peers
- Route Reflectors and Confederations are two separate protocols that have been designed to let routers receive internal updates and send it on to another internal peer

## BGP Peering Overview
- BGP peers must have IP reachability to each other
- BGP cannot use default routes to establish peering relationships

## Sanity Checks

- Source IP address of incoming TCP connection must be from an expected/configured BGP peer
- Peer's advertisement for his BGP AS# must be what we expect
- If BGP authentication is used, same password must be configured
- Peers must have unique BGP Router-IDs
- Peers must use the same BGP version