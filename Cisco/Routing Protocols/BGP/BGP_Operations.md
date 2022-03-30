# BGP Operations

## BGP ASNs
  - Originally 2-byte field
    - Values 0-65535
    - Public ASNs 1 - 64511
    - Private ASNs 64512 = 65535
  - Currently 4-byte field
    - RFC 4893
    - IOS support as of 12.4(24)T
    - 0.0 - 65535.65535 notation
  - 4-Byte BGP ASNs
    - 0.[0-65535] denote original 2-byte ASNs
    - Needs command bgp asnotation dot
    - Requires backwards compatibility with old code
    - 4 Byte ASN support negotiated during capability exchange
      - "Old" BGP speakers are sent ASdot numbers encoded as ASN "23456"
      - Real AS-Path encoded with optional transitive attributes AS4_AGGREGATOR and AS4_PATH
## Establish BGP Peering
  - BGP must first have neighbors designated
  - BGP does not have its own transport
  - BGP has different types of neighbors
  - BGP neighbors are not discovered by default
  - BGP neighbors do not have to be directly connected
  - BGP uses TCP port 179 for transport
  - BGP neighbor statement tells process to:
    - Listen for remote address via TCP 179
    - Initiate a session to remote address via TCP 179
    - If collision, higher router-id becomes TCP client
## BGP Transport
  - TCP server must agree on where client's session is coming from
    - If server does not expect session it will refuse
    - **show control-plane host open-ports**
      - May be version specific
  - Client's packet is sourced from outgoing interface in the routing table
    - Can be modified with update-source per neighbor
## BGP Peering Types
  - External BGP (EBGP) Peers
    - Neighbors outside my autonomous system
  - Internal BGP (iBGP) Peers
    - Neighbors inside my autonomous system
  - Update and path selection rules change depending on what type of peer a route is being sent to/received from
  - 
