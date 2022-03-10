## Layer 2:

- Flooding
  - When a frame is received on the switch there will be a source Mac Address.
  - The source will be associated in the Mac Address Table for the port it was received on. (Called Dynamic Mac Address)
  - The destination mac address will be flooded out all other ports (in the same vlan) if the switch does not have it in the address table.
  - Multiple mac address can be associated with a single trunk port
- MAC Table
  - Frame Format of Mac Address
    - Preamble - 7 bytes
    - SOF - 1 byte
    - Destination Mac address - 6 bytes
    - Source Mac address - 6 bytes
    - Length 2 bytes
    - Data/Pad - 46 - 1500 bytes
    - FCS - 4 bytes
- CAM Table
- TCAM Table
  - Ternary Content Addressable Memory
  - Volatile Memory
  - Stores three values (0, 1, or doesn't care)
  - Good for matching partial patterns
  - Memory Stored as VMR triplets (Value, Mask, Result)
  - TCAM might store (L2 entries, L3 entries, QoS policies, Security Policies)
  - TCAM is controlled by SDM (Switching Database Manager)
- ARP
- Hardware: hub vs switch, MAC learning, packet forwarding operations, IP lookup, CAM vs TCAM, flow-based vs HW based, “day in the life of a packet”.
- TCP: UDP vs TCP, beyond 3-way handshake, window behavior/scaling, packet loss detection, latency/reliability, congestion avoidance, session closing
- Ethernet/IP: IP subnetting, ARP, Ethernet MTU vs MSS. PMTUD, IP fragmentation, traceroute operation

## Management:

- Yang Data Models
  - Yet Another Next Generation
  - YANG models that describe things are called "Modules"
  - Numerous YANG Modules
  - YANG Terminology:
    - Containers
      - Utilize a tree-like structure similar in format to XML
      - YANG Modules start with top-level container objects
      - Containers have no values, just sets of child nodes
    - Leafs
      - Containers contain one or more descriptive objects called Leafs
        - Name of interface
        - Interface description
        - Type of Interface
        - Operational state of interface
      - Each leaf must have associated 'type' for how to describe the leaf
        - String, Integer, Boolean, etc.
      - RO vs RW
        - YANG makes clear distinction between configurable items and state-related information
        - RW (Description of interface,IP Address, enabled/disabled, etc.)
        - RO (References state of an object)
    - Viewing YANG Models
      - YANG models are very verbose
      - Stored in GitHub
      - Tree structure can be viewed using pyang python tool
      - 

- Netconf
  - Network Configuration Protocol
  - RFCs 4741 & 6241
  - Improvement over SNMP
  - Netconf/Restconf use YANG data models
  - Securely runs over SSH (Port 830)
  - Unlike SNMP, Netconf can distinguish between configuration data and operational data
  - Netconf uses paths (similar to HTTP) to describe resources whereas SNMP utilizes OIDs
  - Encoded in XML or JSON (mostly XML)
  - Netconf Protocol Stack
    - Content - XML (Primarily using YANG)
    - Operations - (get, validate, get-config, lock, unlock, delete-config, many others)
    - Messages - RPC, RPC-Reply
    - Protocols - SSHv2, SOAP, TLS
  - Netconf Capabilities
    - Collect status of specific fields
    - Change configs
    - Take administrative actions
    - Send event notifications
    - Backup and Restore
    - Test configs before finalizing transaction
  - Netconf Terminology
    - Netconf Agent
    - Netconf Manager
    - Datastore
      - Database/table of stored data by agent
      - Target of Netconf Commands
      - Manipulated using Netconf operational commands sent as RPC messages
    - Datastore types
      - Running, Startup, Candidate
      - Target of netconf operations
      - May hold copies of config data
      - Not all datastores supported on all devices
      - Running config is only required datastore
      - 
  - Restconf
    - REST (Representation State Transfer)
    - REST API commands use standard HTTP "verbs" (GET,PUT,POST,DELETE)
    - POST (Create), GET (Read), Put (Update), DELETE (Delete)
    - Restconf uses HTTPS for Transport and JSON for encoding (Does support XML)
    - 

## Routing Protocols

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

- Switching
  - Move the packet between interfaces
- Encapsulation
  - Rebuild the packet's layer 2 header

### Path Selection:
- RIP selects path by lowest hop count
- OSPF selects path by lowest cost
- EIGRP select path with highest bandwidth and lowest delay (unless you change the K values)
- BGP has numerous attributes to select best path.

### BGP:
We Love Oranges AS Oranges Mean Pure Refreshment
- Weight
- LOCAL_PREF (Highest is better)
- Originated Locally
- AS_PATH (shortest)
- ORIGIN Type (IGP is lower than EGP and EGP is lower than Incomplete)
- MED (lowest is better)
- Paths (External > Internal)
- RID (Router ID - Lowest is better)

#### BGP Path Selection
- Weight and Local pref gets set inbound and affects traffic outbound
- MED and AS_PATH pre-pending gets set outbound and affects traffic inbound

BGP: Neighbor establishment process, attributes, decision process, eBGP/ iBGP differences, peering and loop prevention, BGP MED vs local preference
Best Path
Local Preference

As Path
Active State
Route Reflector (read up on this)

### MPLS:
MPLS: basic uses/applications, L2 vs L3 VPNs, traffic engineering, main components
RIB
LFIB
PHP (penultimate hop popping)
MPLS Labels
VRF (Virtual Route Forwarder)


### OSPF: 
Cost calculation, Neighbor adjacency, need for “areas”, shifting traffic off router/single link, stubby areas, scaling OSPF
MTU
Neighbor adjacency
OIA
E2
Locally Significant
Area Zero – Backbone Area
Traceroute

Brush up on your Network Engineering fundamentals:

route reflectors for BGP
stuck in x start for OSPF
What part of the packet is tracking hop count in traceroute


Terminology: Regardless of your experience or the type of networks you have worked on in the past, every interviewer will bring a list of terms that you must be able to define and explain. These terms are found in your own resume. For example, if BGP is listed on your resume, you should be prepared to explain what it is, (Traffic – Peering OR IBGP vs EBGP) when it is used and more importantly why, the strengths and limitations, as well as what environment you have used it for. 

Fundamentals:Fundamental concepts like the seven layers of the OSI model or what happens when a URL is entered into a browser are fair game in a job interview for any network engineer. You can expect to be asked to explain troubleshooting commands like traceroute and or how ARP works and take us through “the life of a packet”. Basic questions are sometimes the trickiest to answer, particularly if you are expecting questions that relate to your specialty, so it may be a good idea to brush up on fundamental concepts before an interview.

 

Routing Protocols and more: Routing and Switching are at the heart of most NDE teams within AWS – be expected to go into detail and discuss tradeoffs/optimal use cases between various technologies.





Past Environments:The interviewer will often want to determine the nature of your last position and the role you played in migrations or day-to-day operations. As such, you may be asked to describe the topology or configuration of the last network you worked on.


What does lacp do?