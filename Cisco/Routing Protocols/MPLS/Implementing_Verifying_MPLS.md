# Implementing and Verifying MPLS
## Understanding the Need for MPLS
- Multiple customer routing tables are separated using VRFs
  - Without MPLS though all the routes will have to be in the global routing table
  - Without running any kind of labels from MPLS every router in a service providers network would need to run BGP
## MPLS Overview
  - MPLS can be thought of as like a VPN
  - Instead of encapsulating with an IP the packet is encapsulated with a label
  - Labels are a representation of an IP
    - One label equals one Prefix
    - No longer need to do an IP lookup and can do a label lookup
  - Only need to run BGP on PE routers
  - Next Hop lookup is always in the global routing table
## Implementation
- Have the habit of always enabling `ip cef distributed`
- mpls ldp router-id (pick an interface that is reachable)
  - It must be reachable because the connection is a tcp connection on port 646
- mpls label protocol ldp
- mpls label range 2000 2999
  - Reboot after for label range to take affect
- 
