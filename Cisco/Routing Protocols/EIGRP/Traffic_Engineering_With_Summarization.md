# Traffic Engineering with Summarization

## EIGRP Traffic Engineering with Summarization
- EIGRP automatically suppresses subnet advertisements of summaries
- Since EIGRP hierarch is arbitrary, selective summaries can be used for traffic engineering
  - Longest match route is always preferred
  - Implies router performing the summary is less-preferred
- When doing traffic engineering the routing decisions are locally significant
  - Always look at what the local attribute of the link is


## EIGRP Leak Maps
- Leak Map allows selective advertisements of subnet(s) in a summary
- Can be used for longest match traffic engineering
- Applied through a route-map
  - i.e. match ip address prefix-list
- Configuration
  - R5(config)#ip prefix-list LEAKED_PREFIX permit 150.1.10.10/32 
    - R5(config)#route-map LEAK_MAP permit 10 
    - R5(config-route-map)#match ip address prefix-list LEAKED_PREFIX
    - R5(config-router-af)#af-interface tun0
    - R5(config-router-af-interface)#summary-address 0.0.0.0 0.0.0.0 leak-map LEAK_MAP
