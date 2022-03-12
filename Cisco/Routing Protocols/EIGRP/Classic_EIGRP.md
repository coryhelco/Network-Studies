# Classic EIGRP

- Administrative Distances
  - EIGRP Summary Route - 5
  - Internal EIGRP - 90
  - External EIGRP - 170

- General Information
  - EIGRP prior to EIGRP Named Mode (Prior to IOS 15.x)
  - EIGRP AS Configuration with process name as the AS number is classic mode
  - EIGRP is a **distance vector protocol** which means we only advertise the routes we are actually using
  - FIB is the CEF table for actual forwarding
  - EIGRP only knows what their directly connected neighbors are advertising to them
  - EIGRP Protocol number 88
  - Destination 22.0.0.10 (well-known link local multicast for eigrp)
  - DSCP 0x30 means EIGRP should be high priority traffic
  - After multicast hello's are sent between two adjacent EIGRP routers the communication are switched to unicast if adjacencies are properly formed
  - Hello's and Updates are still sent multicast

- Configuration
  - router eigrp [as]
    - AS number must match to be adjacent
    - If AS number does not match it is EIGRP External
  - network [address] [wildcard]
    - Similar to OSPF network statement


- Verification
  - Verify EIGRP is enabled
    - show ip eigrp interfaces [detail]
    - show ip protocols
  - Verify EIGRP transport
    - debug eigrp packet [hello | ack | update | query | reply]
  - Verify Neighbor adjacencies
    - show ip eigrp neighbors [detail]
    - Queue count should be 0 if converged
  - Verify EIGRP topology
    - show ip eigrp topology [all-links]
    - show ip eigrp topology [prefix/len]
    - show ip eigrp topology all-links
  - Additional EIGRP verifications
    - show ip route
    - show ip protocols
      - Show us in more detail what routing protocols are running
      - NHRP is dynamic multipoint VPN (DMVPN)
    - show ip eigrp interfaces
      - Show what interfaces have eigrp enabled on them
    - show ip eigrp neighbors
      - Pay attention to the Q count and make sure it is 0 to verify routers are adjacent and converged
    - show ip route eigrp
  - Capturing traffic
    - monitor capture 1 interface gig1.67 both
    - monitor capture 1 match any
    - monitor capture 1 start
    - show monitor capture 1 buffer
    - monitor capture 1 export tftp://(ip of endpoint to send capture)
    

- EIGRP Routes/Advertisements
  - Once EIGRP adjacencies are formed the routes will be advertised
  - Routes go into the EIGRP topology (known as EIGRP RIB as well)
  - Selection process is called the feasibility condition
    - The winners are called the successors and are candidates to be installed in the routing table
    - Can modify K values to do traffic engineering
  - Named Mode EIGRP has a feature called Multi Topology Routing (MTR) to run multiple EIGRP processes on different links and route traffic based on application characteristics
  - When route shows as passive in eigrp topology it means it is converged and can be used for forwarding. Active state means re-convergence process is being run
  - Feasible distance is the metric calculated by bandwidth, delay, reliability, load, min MTU, and Hop Count
  - Advertised Distance is metric that neighbor router is advertising 
    - Advertised distance must be lower than feasible distance or the route does not meat the **Feasibility Condition** since it thinks there is a loop in the network

