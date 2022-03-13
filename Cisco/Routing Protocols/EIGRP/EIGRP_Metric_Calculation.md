# EIGRP Classic Metric Calculation

- EIGRP uses lowest composite metric based on: (Bandwidth and Delay is used most often)
  - Bandwidth
    - Inverse lowest bandwidth along path in Kbps scaled by 10^7 * 256
  - Delay
    - Cumulative delay along path in tens of microseconds scaled by 256
  - Load
    - Highest load along path
  - Reliability
    - Lowest reliability along path
    
  - Composite Metric is computed as:
    - metric = k1 * BW + (k2 * BW)/(256 - load) + k3 * delay]
    - if k5 !=0, metric = metric * [k5/(reliability + k4)]
  - K values allow for manual weighting
    - Defaults are K1 = 1, K2 = 0, K3 = 1, K4 = 0, K5 = 0
    - Implies default composite is bandwidth + delay
  - Can be modified withe metric weights command
    - Must match for adjacency to occur


# EIGRP Wide Metric Calculation

- EIGRP Classic Bandwidth Metric Problem
  - Classic EIGRP Metric uses bandwidth as:
    - CLASSIC_EIGRP_BW = 10 ^ 7 * 256 / INT_BW
  - As bandwidth increases, visibility is lost
    - 1 GigE = 2560
    - 10 GigE = 256
    - 20 GigE = 256
    - 40 GigE = 256
    - 100 GigE = 256
- EIGRP Classic Delay Metric Problem
  - Minimum configurable delay in IOS is 10 microseconds
  - As bandwidths increase, delay visibility is lost

- Wide Metrics solves this problem
  - Wide metrics is now 64 bits
  - BW problem fixed by scaling higher
    - WIDE_EIGRP_BW = 10^7 * 65536 / INT_BW
  - DLY problem is fixed by moving to picoseconds
    - WIDE_EIGRP_DLY = DLY_PICO * 65536 / 10^6

  - BW is now called Throughput
    - Throughput = K1 * (EIGRP_BANDWIDTH * EIGRP_WIDE_SCALE) / Interface Bandwidth (kbps)
  - DLY is now Latency
    - Latency = K3 * (Delay * EIGRP_WIDE_SCALE) / EIGRP_DELAY_PICO
  - Default metric is still the same formula
    - metric = (K1 * min(Throughput)) + (K3 * sum(Latency))
- Wide Metric & RIB Scaling
  - IOS RIB only supports 32-bit metrics
  - After EIGRP DUAL, metric is scaled down to fit in the RIB
    - Default is to scale * 1/128
    - Can be modified with metric rib-scale
    - 