# EIGRP Traffic Engineering & Unequal Cost Load Balancing

- Reviewing:
  - EIGRP Feasibility Condition
  - Modifying EIGRP Path Selection
  - EIGRP Unequal Cost Load Balancing

### EIGRP Traffic Engineering
- Default EIGRP composite metric is Bandwidth + Delay
  - Based on default K1 and K3 metric weights
- Bandwidth is lowest bandwidth along the path on a per prefix basis
  - Essentially the bandwidth bottlneck
  - Hard to predict what a modification will affect
- Delay is cumulative on a hop-by-hop basis
  - Easier to influence path selection with Delay
  - Modified with delay interface command
- EIGRP allows load distribution among unequal paths
  - Only Feasible successors are candidate for load balancing
  - Controlled by Variance command
    - If Feasible distance * variance > feasible successor, load balancing occurs
  - Traffic share is automatically calculated
    - Links used in ratio proportional to their composite metrics
    - Actual load balancing still controlled by switching path
      - E.g. CEF per-flow

### EIGRP Feasibility Condition

- Feasibility condition (FC) is used to select loop-free backup paths
  - Paths meeting the FC are pre-computed during DUAL
  - Pre-computed backup paths have multiple advantages
    - Sub-second reconvergence when primary path fails
    - Fault Isolation
    - Unequal cost load distribution


- Feasibility condition High Level Overview
  - First, find the best path
    - Take4 the metric of the best path as X
  - Next, find alternate backup paths
    - Anyone with a metric less than X is closer to the destination than me
    - Exclude anyone with a metric greater than or equal to X
- Feasibility condition in detail
  - Once best path is chosen, additional paths are examined for backup routes
  - FC finds loop-free backup routes via logic:
    - If RD < FD, path is loop-free and viable backup
    - e.g. if your metric is lower than mine, you are closer to the destination and loop free
  - Paths that meet FC are Feasible Successors (FS)
    - Only Feasible Successors can be used for unequal cost load balancing


- Feasibility Condition Terminology
  - Successor
    - Best path to a destination
  - Feasible Distance (FD)
    - Composite metric of best path (successor)
  - Reported Distance (RD)
    - Composite metric learned from neighbors
  - Feasible Successor (FS)
    - Backup paths that meet the Feasibility Condition

- Reconvergence
  - Reconvergence differs for paths with or without a FS
  - Paths without a Feasible Successor
    - Loss of Successor sends route into Active state
    - Send QUERY to all neighbors
    - Reconverged when REPLY heard from all neighbors
  - Paths with a Feasible Successor
    - Loss of Successor does not make route Active
    - Feasible Successor promoted to Successor
    - QUERY not generated
    - Result if sub-second convergence

- Bidirectional Forward Detection (BFD)
  - BFD runs in hardware on the ASIC so we don't have to run on the CPU or rely on modifying Routing protocol timers
    - Configuration
      - int (interface on each router we want BFD on)
        - bfd interval ? min_rx ? multiplier ?
      - router eigrp ABC
        - address-family ipv4 unicast as 100
        - af-interface [interface]
          - bfd
    - Verification
      - Show bfd neighbors
      - show bfd neighbors detail
    - Debugging
      - logging con 7
      - logging on
      - debug eigrp packet reply query

### Unequal Cost Load Balancing
  - Configuration
    - router eigrp ABC
      - address-family ipv4 unicast autonomous-system 100 
        - topology base 
          - variance 128
  - Feasible distance multiplied by **variance** 
    - Any feasible successor whose metric value is lower than FD * variance is allowed to be installed in routing table for unequal cost load balancing
    



