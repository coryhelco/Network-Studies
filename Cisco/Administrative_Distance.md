# Routing Protocol Administrative Distance

-Directly connected interface	0
-Static route out an interface	1
-Static route to next-hop address	1
-DMNR - Dynamic Mobile Network Routing	3
- EIGRP summary route	5
- External BGP	20
- Internal EIGRP	90
- IGRP	100
- OSPF	110
- IS-IS	115
- Routing Information Protocol (RIP)	120
- Exterior Gateway Protocol (EGP)	140
- On Demand Routing (ODR)	160
- External EIGRP	170
- Internal BGP	200
- Next Hop Resolution Protocol (NHRP)	250
- Default static route learned via DHCP	254
- Unknown and unused	255



## Routing Protocols

### Prefix Length 
- The longest-matching route is preferred first. 
- Prefix length trumps all other route attributes. 
- Administrative Distance 
  - If multiple routes to a destination with the same prefix length exist, the route learned by the protocol with the lowest administrative distance is preferred.