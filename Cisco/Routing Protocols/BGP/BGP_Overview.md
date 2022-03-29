# BGP Overview

- BGP uses TCP (179) and unicast
- OSPF/EIGRP use their own protocol
- Routes vs NLRI
  - IGP protocols exchange unicast routes
  - BGP also exchanges unicast routes, but can also exchange other types of information
  - For this reason we say BGP exchanges NLRI (Network-Layer Reachability Information)

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