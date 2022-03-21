# EIGRP Summarization

- EIGRP Query Scoping
- EIGRP Summarization


## EIGRP Scalability
- Sub-seond convergence with Feasible Successors
  - Pre-calculated backup routes which pass Feasibility Condition
- What if I can't engineer Feasible Successors?
  - If no backup routes, QUERY message is sent
  - Convergence now a function of QUERY domain size
- Query domains can be bound by:
  - Summarization
  - Stub router advertisement

## EIGRP Query Scoping
- QUERY is generated when a route becomes ACTIVE
  - E.g. link loss, neighbor loss, metric change
- QUERY is sent for exact match of lost route
  - E.g. do you have a route to 1.2.3.0/24?
- REPLY is immediately sent if no match
  - E.g. 1.2.3.0/24 doesn't match my 1.0.0.0/8
  - This is how summaries limit the QUERY domain

## EIGRP Summarization
- EIGRP supports two types of summaries
  - Auto-Summary
    - Disabled in version 15.x and later
    - VLSM is supported within the same major network
  - Manual Summary
    - Supports any bit boundary including 0.0.0.0/0
      - Advertises default route out all interfaces
    - Automatically suppresses subnet advertisements
    - Can advertise subnets through leak-map argument
      - Configuration:
        - Classic EIGRP:
          - **ip summary-address** at the link level
        - Named EIGRP
          - **summary-address** at the af-interface
## EIGRP Summary Metrics
- EIGRP summary route inherits metrics from its subnets
  - Subnet with the best metric is used
- Churning subnets means churning summary
  - Change of best subnet metric means summary must be re-advertised
- **summary metric** can mitigate this
  - Metric is statically defined per-summary in topology base
  - Can be important in large scale DMVPN networks
- EIGRP hierarchy is arbitrary
  - Summaries can be enforced anywhere
  - Big design advantage over OSPF and IS-IS

## Configuration
- Classic EIGRP
  - int g0/0
    - ip summary-address eigrp 300 150.1.0.0/20

## Calculating the Summarization Manually
- Find the most amount of contiguous 0's 
- Summary route for this network would be:
  - 150.1.0.0 255.255.240.0

150.1.1     00000001
150.1.2     00000010
150.1.3     00000011
150.1.4     00000100
150.1.5     00000101
150.1.6     00000110
150.1.7     00000111
150.1.8     00001000
150.1.9     00001001
150.1.10    00001010

