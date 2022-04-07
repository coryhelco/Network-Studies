# BGP Best Path Selection
- [BGP Diverse Path Route Reflector](https://content.cisco.com/chapter.sjs?uri=/searchable/chapter/content/en/us/td/docs/ios-xml/ios/iproute_bgp/configuration/xe-3s/irg-xe-3s-book/irg-diverse-path.html.xml)

## BGP Best Path Selection Overview
- Chooses a single BGP best-path which can be:
  - Installed in the RIB/FIB
  - Advertised to other BGP Peers
- In general path selection is standardized
  - RFC 4271 Section 9.1 "Decision Process"
  - Cisco specific order at:
    - BGP Best Path Selection Algorithm

### Best Path Selection Prerequisites
- Next-hop value must be in the routing table
  - Prevents route recursion failure
- Synchronization rule must be met or disabled
  - Legacy black-hole prevention technique
- AS-Path must not contain Local-AS
  - Normal EBGP loop prevention
  - Can be disabled with **allow-as in**
- First ASN in path must be neighbor's ASN
  - **bgp enforce-first-as** command

## Best Path Selection Order
- Pre-Bestpath community
- Weight
  - Cisco proprietary
  - Locally Significant
  - Higher value is preferred
- Local Preference
  - Higher value is preferred
  - Not advertised to eBGP peers
  - Carried through confederation eBGP
- Locally Originated
  - Locally originated gets Weight of 32768
- AS-Path
  - Smaller length is preferred
- Origin
  - IGP over EGP over Incomplete
  - Status codes I > E > ?
- MED
  - Smaller value is preferred
  - Only compared between for peerings to same by default
  - Lots of other exceptions
- EBGP over iBGP
  - If you learned it via EBGP it's not your prefix
- IGP Metric to Next-Hop
  - Can use multi-path if all equal after this step
  - Hidden Command to allow multipath if AS_PATH is not the same (has to be same length)
    - **bgp bestpath as-path multipath-relax**
- Tie Breakers
  - Oldest route
  - Lowest RID
  - Shortest clust list
  - Lowest Neighbor Address
- AS-Path
  - **bgp best-path as-path ignore**
- MED
  - **bgp always-compare-med**
  - **bgp bestpath med-confed**
    - Compares MED for routes locally originated in the Confederation
  - **bgp bestpath med missing-as-worst**
    - Assign MED of 4,296,967,294 to NULL MED
  - **bgp deterministic med**
    - Compare MED against all possible paths
- IGP Metric
  - **bgp bestpath igp-metric ignore**
    - Introduced in IOS-XE 3.4S
- Router-ID
  - **No bgp bestpath compare-routerid**

## Manipulating Best Path Selection
- Outbound routing policy affects inbound traffic
- Inbound routing policy affects outbound traffic
- Longest Match Routing is above all
  - Affects both directions
### Attributes to Influence Outbound Path Selection
- Weight & Local Preference
  - Set inbound
  - Affects outbound traffic
### Attributes to Influence inbound Path Selection
- AS-Path Prepending & MED
  - Set outbound
  - Affects inbound traffic
### Multipath Load Balancing
- Multipath load balancing for external links with unequal bandwidth
  - Enabled under IPv4, IPv6, VPNv4, VRF AF
  - For iBGP, eBGP, eiBGP
- Still only one best path advertised to peers

## Modifying Inbound Traffic

- Use a route-map to affect this with Local Preference
```
conf t
route-map AS200_INBOUND permit 10
   set local-preference 200
router bgp 100
    neighbor 10.3.5.5 route-map AS200_INBOUND in
```
- Can add in bgp advertise-best-external
```
conf t
router bgp 100
address-family ipv4 unicast
bgp advertise-best-external
```

### Using Prefix Lists on R3
```
conf t
ip prefix-list R1_R2_LOOPBACKS permit 1.1.1.1/32
ip prefix-list R1_R2_LOOPBACKS permit 2.2.2.2/32
route-map AS200_OUTBOUND
  route-map AS200_OUTBOUND permit 10
    match ip address prefix-list R1_R2_LOOPBACKS
      set as-path prepend 100 100 100
  route-map AS200_OUTBOUND permit 1000
  
router bgp 100
neighbor 10.3.5.5 route-map AS200_OUTBOUND out

```
#### Advertise backup routes from BGP
- bgp advertise-best-external

#### Install Repair routes for Diverse Path Route Reflector
```
conf t
router bgp 200
address-family ipv4 unicast
bgp additional-paths select backup
bgp additional-paths install
neighbor 8.8.8.8 advertise diverse-path backup 
neighbor 9.9.9.9 advertise diverse-path backup
maximum paths ibgp 32
```

### Verification
- show ip bgp regexp _100_
- show ip route repair paths
