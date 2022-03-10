## Static Routes

### Packet routing steps:
- Routing (First Step)
  - Find the outgoing interface for the packet
  - Find the longest match to the destination
    - Show ip route **network**
  - Recurse to the outgoing interface
  - **Note** A route cannot be installed in the routing table unless the recursive lookup is successful
  - Multiple longest matches
    - Choose lowest metric
    - Rules vary per protocol
      - E.g. OSPF internal over external
    - Different protocol
      - Choose lowest administrative distance
- Routing (Second Step)
  - Move the packet between interfaces
    - Called the "switching path" (Process, Fast, CEF, etc)
  - This is where load balancing occurs
    - Ideally per flow, not per packet
  - Verification
    - Show ip interface
    - Show ip cef [address] [detail]
    - show ip cef exact-route [source] [destination]
- Routing (Final Step)
  - Build the layer 2 header
    - Header types depend on egress type
      - E.g. Ethernet vs. PPP
    - For multipoint interfaces, layer 3 to layer 2 resolution required
      - Ethernet, Frame Relay Multipoint, ATM Multipoint, mGRE (DMVPN)
- Encapsulation on Ethernet (ARP)
  - Resolve the next-hop IP to next-hop MAC
  - Check the ARP cache first
    - show ip arp
  - If no match, broadcast the request
    - debug arp
- Encapsulation on mGRE (DMVPN)
  - Resolve the tunnel IP to the NBMA IP
    - Next-Hop Resolution Protocol (NHRP)
  - Check the NHRP Cache First
    - show ip nhrp
  - If no match, ask the next-hop server (NHS)
    - debug nhrp
    - debug dmvpn

### Reliable Static Routing
  - Only criteria for installing a static route is the route recursion process
    - Can I find the outgoing interface?
  - Static routes don't know the network conditions on the other end of the link
    - Next hop may be reachable or may be down
  - Reliable static routes combine static routing with enhanced objects
    - Enhanced objects calls IP SLA feature to track remote reachability
    - If tracked object is down, static route is removed from the routing table
  - Allows for more intelligence with floating static routes
    - Primary static route tracks remote next hop
    - If remote next hop is unreachable, remove the static route
    - Floating static replaces it and connectivity can be maintained
  
### Policy Based Routing (PBR)
  - Normal IP routing is destination based
    - Find the longest match to the destination
    - Route the packet towards the next-hop
  - PBR allows forwarding decisions to be based on...
    - Source
    - Destination
    - Protocol Type (FTP, HTTP, etc.)
    - Incoming interface
  - Traffic criteria defined by a route-map match
    - Permit means to policy route
    - Deny means to use normal forwarding
    - match [interface | ip address | ...]
  - Action defined by route-map set
    - set ip [default] next-hop
    - set [default] interface
  - Policy can be verified with...
    - debug ip policy
  - PBR can apply to two types of traffic
    - Incoming traffic
      - PBR applied at the link level
      - ip policy route-map [route-map]
    - Locally originated traffic
      - PBR applied globally
      - ip local policy route-map [route-map]
      - Be careful, some versions don't have control plane checks


### Commands to verify static routes
  - show ip cef (IP Address)
  - show ip route static
  - show track
  - debug ip policy


### Commands to create IP-SLA
  - conf t
    - ip sla 1
    - icmp-echo (IP of where to send)
    - exit
    - track 1 ip sla 1
    - ip sla schedule 1 life start-time now life forever
      - ip route (IP of static route) track 1
    - Configure alternate path
      - remove previous static route if there are duplicates
      - ip route (alternate static route) 2   **The 2 is the administrative distance we are adding**
    - If there are multiple same routes pointing to different next hops then CEF will load balance based on the flow information (IP Destination and upper layer 4 ports)
    - 