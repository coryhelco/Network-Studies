# iBGP Scaling Techniques

## Route Reflectors
- Eliminates need for full mesh
  - Only need peering(s) to the RR(s)
- Like OSPF DR & IS-IS DIS, minimizes prefix replication
  - Send one update to the RR
  - RR sends the update to its 'clients'
  - Does not modify other attributes when reflecting routes
- Loop prevention through Cluster-ID
  - RR discards routes received with its own Cluster-ID
- Sets Originator-ID attribute to the router-id of RR client on routes RECEIVED from the client
  - Client uses Originator-ID for loop prevention
- Route Reflector can have three types of peers
  - EBGP peers
  - iBGP Client Peers
  - iBGP Non Client Peers
- Route Reflector Update Processing
  - RR processes updates differently depending on what type of peer they came from
    - EBGP learned routes
      - Pass to EBGP peers, Clients, & Non-Clients
    - Client Learned Routes
      - Pass to EBGP peers, Clients, & Non-Clients
  - RR placement based upon these rules
## Route Reflector Clusters
- Large scale BGP designs should not be serviced by a single RR
- RR "clusters" allow redundancy and hierarchy
  - Cluster is defined by the clients a RR serves
  - RRs in the same cluster use the same Cluster-ID
- Per address-family RR pairs
  - Avoids "fate sharing" of services
### Inter Cluster Peerings
- Inter-Cluster peerings between RRs can be client or non-client peerings
  - Depends on redundancy design
  - Duplicates routes, more processing
- Cluster-ID is based on Router-ID
  - By default all RR are in separate clusters
- Same cluster-id on both clusters or not?
  - Depends on redundancy design
### Virtual Route Reflectors
- RR generally does not need to be in data-path
  - E.g. I give you the route but you forward through someone else
- High Memory/CPU VM can do the job
  - IOS-XRv
  - CSR1000v
  - Quagga
## Confederation
- Reduces full mesh iBGP requirement by splitting AS into smaller Sub-AS
  - Inside Sub-AS full mesh or RR requirement remains
  - Between Sub-AS acts like EBGP
- Devices outside the confederation do not know about the internal structures
  - Sub-AS numbers are stripped from advertisements to "true" EBGP peers
- Typically uses ASNs in private range
  - 64512 - 65535
- Can use different IGPs in each Sub-AS
  - Next-hop self on border routers
- Next-hop, Local-Pref, and MED are kept across Sub-AS eBGP peerings
### Confederation Loop Prevention
- AS_CONFED_SEQUENCE
- AS_CONFED_SET
- These attributes never leave the confederation
## Route Reflection vs. Confederation
- Why both?
  - Generally accomplish the same goal
- Migration paths are different
  - Migrate to confederation is hard
  - Greenfield confederation is easier
  - Migrate to RR is easy, just add peers then remove old ones