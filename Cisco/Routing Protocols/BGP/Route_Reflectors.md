# BGP Route Reflectors

- Defined in RFC 4456 (APR 2006)
  - RFC 2796 (APR 2000)
  - RFC 1966 (JUN 1996)
- Alternative to having to fully mesh iBGP peers
  - n(n-1)/2
- Establish peering with Route Reflector but no other routers
- Route Reflectors must have full mesh
- Allows the forwarding of iBGP-learned NLRI to other iBGP peers
- Increases iBGP scalability

- Route Reflector Roles:
  - Route Reflector Server (RR)
  - Route Reflector Client (RC)
  - RR + RC = A Cluster
  - NON-Client (Regular iBGP speaker)
- Two new BGP attributes:
  - ORIGINATOR_ID
    - Optional NON-Transitive attribute
    - 4-octets
    - Used to identify the source of the update
    - Loop prevention within a single RR cluster
  - CLUSTER_LIST
    - Optional NON-Transitive
    - 4-octets
    - Contains a list of CLUSTER_IDs
    - Loop prevention between RR clusters
    - Like the AS_PATH loop prevention mechanism

## Verification/Validation
- show ip bgp neighbors | inc Router-Reflector | neighbor
- show ip bgp [prefix/prefix-length]
- debug ip bgp update

## Peer Policy Template
### Peer-Session
- Talking about the session options of TCP
  - Things like Remote Autonomous System, TTL, MD5 Authentication, etc.
### Peer-Policy
- Talking about the Address Family NLRI
  - Things like Route Reflector configs, Network Statement, Route Map/Routing Policy, etc.
### Creating the Templates
```
router bgp 200
 template peer-policy RR_CLIENTS
  route-reflector-client
 exit-peer-policy
 !
 template peer-session iBGP_PEERS
  remote-as 200
  update-source Loopback0
 exit-peer-session
 router bgp 200
 neighbor 5.5.5.5 inherit peer-session iBGP_PEERS
```
### Applying the templates
```
router bgp 200
 template peer-policy RR_CLIENTS
  route-reflector-client
 exit-peer-policy
 !
 template peer-session iBGP_PEERS
  remote-as 200
  update-source Loopback0
 exit-peer-session
 !
 bgp log-neighbor-changes
 neighbor 5.5.5.5 inherit peer-session iBGP_PEERS
 neighbor 6.6.6.6 inherit peer-session iBGP_PEERS
 neighbor 8.8.8.8 inherit peer-session iBGP_PEERS
 neighbor 9.9.9.9 inherit peer-session iBGP_PEERS
 !
 address-family ipv4
  neighbor 5.5.5.5 activate
  neighbor 5.5.5.5 inherit peer-policy RR_CLIENTS
  neighbor 6.6.6.6 activate
  neighbor 6.6.6.6 inherit peer-policy RR_CLIENTS
  neighbor 8.8.8.8 activate
  neighbor 8.8.8.8 inherit peer-policy RR_CLIENTS
  neighbor 9.9.9.9 activate
  neighbor 9.9.9.9 inherit peer-policy RR_CLIENTS
 exit-address-family
```

```
# Route Reflector Server
router bgp 100
bgp log-neighbor-changes
neighbor 4.4.4.4 remote-as 100
neighbor 4.4.4.4 update-source lo0
neighbor 4.4.4.4 route-reflector-client
neighbor 4.4.4.4 next-hop-self
bgp-router-id 7.7.7.7

# Route Reflector Client
router bgp 100
neighbor 7.7.7.7 remote-as 100
neighbor 7.7.7.7 update-source lo0
```