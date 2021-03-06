#OSPF Protocol Operation and Overview

- Administrative Distance
  - 110

- Recommended OSPF Resources
- What is OSPF
  - Books
    - OSPF: Anatomy of an Internet Routing Protocol
    - Routing TCP/IP Volume 1
    - Cisco IP Routing: Packet Forwarding and Intra-domain Routing Protocols
  - Standards
    - RFC 2328 "OSPF Version 2"
    - RFC 5340 "OSPF for IPv6"
  - Documentation
    - https://www.cisco.com/c/en/us/support/ios-nx-os-software/ios-15-3m-t/products-installation-and-configuration-guides-list.html
    - https://www.cisco.com/c/en/us/td/docs/ios-xml/ios/iproute_ospf/configuration/xe-16/iro-xe-16-book/iro-cfg.html
  - Link-State Protocol
    - Uses Dijkstra SPF Algorithm
  - Classless Protocol
    - Supports VLSM and Summarization
## Why Use OSPF
  - Guarantees Loop-Free Topology
    - All routers agree on overall topology
    - Uses Dijkstra SPF Algorithm for calculation
  - Standards Based
    - Inter-operability between vendors
  - Large Scalability
    - Hierarchy through "areas"
    - Topology Summarization
  - Fast Convergence
    - Actively Tracks Neighbor Adjacencies
    - Event Driven Incremental Updates
  - Efficient Updating
    - Uses reliable multicast and unicast updats
    - Non-OSPF devices do not need to process updates
  - Bandwidth Based Cost Metric
    - More flexible than static hop count
  - Control Plane Security
    - Supports multiple forms of authentication
      - E.g. Clear text, MD5, SHA, IPsec, etc.
  - Extensible
    - Future application support through "opaque" LSA
      - E.g. MPLS Traffic Engineering
## How does OSPF work
  - Forming adjacencies
    - Discover OSPF Neighbors and Exchange Topology Information
    - OSPF uses Hello packets to discover neighbors on OSPF enabled attached links
      - Tranport via IP protocol 89 (OSPF)
      - Sent as multicast to 224.0.0.5 or 224.0.0.6, or unicast
    - Hello packets contain attributes that neigbors must agree on to form "adjacency"
      - Once adjacency is negotiated, LSDB is exchanged
  - Negotiating OSPF Adjacencies
    - OSPF adjacency occurs when connected neighbors use hello packets to agree on unique and common attributes.
      - Not all OSPF neighbors actually form adjacency
      - Most OSPF configuration problems happen at this stage
  - Unique OSPF Adjacency Attributes
    - Router ID
      - Node ID in the Link State Graph
      - Chosen based upon:
        - Manual configuration
        - Highest active Loopback IP
        - Highest active Interface IP
      - Interface IP Address
        - For OSPFv2 the interface's primary IP address
        - For OSPFv3 the interface's link-local address
  - Path selection
    - Choose best path via SPF
  - Topology Maintenance
    - Neighbor and Topology Table Maintenance
## Common OSPF Adjacency Attributes
  - Interface Area ID
  - Hello interval & Dead interval
  - Interface network address
  - Interface MTU
  - Network Type
  - Authentication
  - Stub Flags
  - Other optional capabilities
## OSPF Hello Packets
  - OSPF routers periodically send Hello packets out OSPF enabled links every Hello Interval
    - Hello packets contain:
      - Local Router-ID
      - Local Area-ID
      - Local Interface Subnet Mask
      - Local Interface Priority
      - Hello Interval
      - Dead Interval
      - Authentication Type & Password
      - DR/BDR Addresses
      - Option (e.g. stub flags, etc)
      - Router IDs of other neighbors on the link
## Step 1 - OSPF Adjacency State Machine
  - OSPF adjacency process uses 8 states to determine progress of adjacency establishment
    - Down
      - No hellos have been received from neighbor
    - Attempt
      - Unicast hello packet has been sent to neighbor, but no hello has been received back
      - Only used for manually configured NBMA neighbors
    - Init
      - I have received a hello packet from a neighbor, but they have not acknowledged a hello from me
    - 2-Way
      - I have received a hello packet from a neighbor and they have acknowledged a hello from me
      - Indicated by my Router-ID in neighbor's hello packet
    - ExStart
      - First step of actual adjacency
      - Master & slave relationship is formed, where master has higher Router-ID
      - Master chooses the starting sequence number for the Database Descriptor (DBD) packets that are used for actual LSA exchange
    - Exchange 
      - **Where we start to tell each other what attributes we want to use for adjacency and describe what database looks like**
      - Local link state database is sent through DBD Packets
      - DBD sequence number is used for reliable acknowledgement/retransmission
    - Loading
      - Link State Request packets are sent to ask for more information about a particular LSA
    - Full
      - Neighbors are fully adjacent and databases are synchronized
## Step 2 - Choose Best Path via SPF
  - Once databases are synchronized, path selection begins
  - Each router's LSA include a "cost" attribute for each described link
    - Best path to that link is lowest end-to-end cost
    - Multiple Equal Cost paths are allowed (ECMP)
  - Cisco's implementation uses bandwidth based cost, but per RFC it is arbitrary
    - Default Cisco Cost = 100Mbps / Link Bandwidth
    - Reference bandwidth can be modified to accommodate higher speed links (e.g. TenGigabitEthernet)
## Step 3 - Neighbor & Topology Maintenance
  - Once adjacencies established and SPT built, OSPF state machine tracks neighbor and topology changes
    - Hello packets used to track neighbor changes
    - LSA fields used to track topology changes
  - Tracking Neighbor Changes
    - Hello packets continue to be sent on each OSPF enabled link every HelloInterval
      - 10 or 30 seconds by default depending on interface type
    - If a Hello packet is not received from a neighbor within RouterDeadInterval, the neighbor is declared down
      - Defaults to 4 times HelloInterval
      - Can be as low as 1 second for faster convergence
  - Tracking Topology Changes
    - When a new LSA is received it is checked against the database for changes such as:
      - Sequence number
        - Used to track new vs old LSAs
      - Age
        - Used to keep information new and withdraw old information
        - Periodic flooding occurs after 30 minutes
          - "paranoid" update
        - LSAs that reach MaxAge (60 minutes) are withdrawn
      - Checksum
        - Used to avoid transmission & memory corruption
  - LSA Flooding
    - When a change is detected new LSA is generated and "flooded" (sent) out all links
      - OSPF does not use split horizon
      - Self-originated LSAs are simply dropped
    - Not all LSA changes require SPF to recalculate
      - e.g. link up/down event vs. seq number change
      - See RFC 2328 "13. The Flooding Procedure" for details