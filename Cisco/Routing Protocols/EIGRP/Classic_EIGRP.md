# Classic EIGRP

- General Information
  - EIGRP prior to EIGRP Named Mode (Prior to IOS 15.x)
  - EIGRP AS Configuration with process name as the AS number is classic mode
  - EIGRP is a **distance vector protocol** which means we only advertise the routes we are actually using
  - FIB is the CEF table for actual forwarding
  - EIGRP only knows what their directly connected neighbors are advertising to them

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

- EIGRP Routes/Advertisements
  - Once EIGRP adjacencies are formed the routes will be advertised
  - Routes go into the EIGRP topology (known as EIGRP RIB as well)
  - Selection process is called the feasibility condition
    - The winners are called the successors and are candidates to be installed in the routing table
    - 
    - 
