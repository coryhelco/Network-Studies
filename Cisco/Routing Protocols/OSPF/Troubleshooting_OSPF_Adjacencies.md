# OSPF Adjacencies

## Troubleshooting OSPF Adjacencies
- Where can problems arise?
  - Transport problems
  - Attribute negotiation problems
- Userful troubleshooting 
  - logging con 7
  - logging on
    - show ip ospf neighbor
    - show ip ospf database
    - debug ip ospf adj
    - debug ip ospf hello
      - debug ip packet
        - **use with caution**

### Understanding the OSPF State Machine
#### Step 1 - OSPF Adjacency State Machine
  - OSPF adjacency process uses 8 states to determine progress of adjacency establishment
    - Down
      - No hellos have been received from neighbor
    - Attempt
      - Unicast hello packet has been sent to neighbor, but no hello has been received back
      - Only used for manually configured NBMA neighbors
    - Init
      - I have received a hello packet from a neighbor, but they have not acknowledged a hello from me
    - 2-Way -- **If we do not get to 2-Way it is a sign of a transport problem - I.e. VLAN acl dropping ospf, 1 way link on fiber so receive isn't working, etc.**
      - I have received a hello packet from a neighbor and they have acknowledged a hello from me
      - Indicated by my Router-ID in neighbor's hello packet
        - **DROthers stop here**
    - ExStart -- **If stuck here then it is a sign of attribute problems. Use ```debug ip ospf adj``` to help troubleshoot**
      - First step of actual adjacency
      - Master & slave relationship is formed, where master has higher Router-ID
      - Master chooses the starting sequence number for the Database Descriptor (DBD) packets that are used for actual LSA exchange
    - Exchange -- **If stuck here then it is a sign of attribute problems**
      - **Where we start to tell each other what attributes we want to use for adjacency and describe what database looks like**
      - Local link state database is sent through DBD Packets
      - DBD sequence number is used for reliable acknowledgement/retransmission
    - Loading
      - Link State Request packets are sent to ask for more information about a particular LSA
    - Full
      - Neighbors are fully adjacent and databases are synchronized

#### Unique OSPF Adjacency Attributes
  - Router-ID
  - Interface IP Address
#### Common OSPF Adjacency Attributes
  - Interface Area ID
  - Hello interval & Dead interval
  - Interface network address
  - Interface MTU
  - Network Type
  - Authentication
  - Stub Flags
  - Other optional capabilities

### Interpreting Show Commands

```R10#show ip ospf database 

            OSPF Router with ID (150.1.10.10) (Process ID 1)

		Router Link States (Area 0)

Link ID         ADV Router      Age         Seq#       Checksum Link count
150.1.1.1       150.1.1.1       1338        0x80000013 0x007F31 5
150.1.2.2       150.1.2.2       1403        0x80000009 0x0013FE 4
150.1.3.3       150.1.3.3       1456        0x8000000E 0x00A857 6
150.1.4.4       150.1.4.4       550         0x80000016 0x00336E 6
150.1.5.5       150.1.5.5       582         0x8000000C 0x003EFA 6
150.1.6.6       150.1.6.6       93          0x8000000C 0x00DD07 3
150.1.7.7       150.1.7.7       1944        0x80000009 0x007597 5
150.1.8.8       150.1.8.8       1929        0x80000009 0x00BFBB 4
150.1.9.9       150.1.9.9       1953        0x80000006 0x00A9D5 3
150.1.10.10     150.1.10.10     0           0x80000008 0x00E950 3

		Net Link States (Area 0)

Link ID         ADV Router      Age         Seq#       Checksum
155.1.13.1      150.1.1.1       102         0x80000004 0x00BBFF
155.1.23.3      150.1.3.3       1960        0x80000003 0x002C7E
155.1.37.7      150.1.7.7       1944        0x80000003 0x009CE9
155.1.58.8      150.1.8.8       177         0x80000004 0x00E482
155.1.67.7      150.1.7.7       1944        0x80000003 0x00A2BF
155.1.79.9      150.1.9.9       1953        0x80000003 0x003119
155.1.108.10    150.1.10.10     509         0x80000005 0x000420
155.1.146.6     150.1.6.6       599         0x80000005 0x002A52
169.254.100.4   150.1.4.4       1530        0x80000006 0x00B9AA
```

- %OSPF-4-FLOOD_WAR: Process 1 re-originates LSA ID 155.1.108.10 type-2 adv-rtr 150.1.10.10 in area 0
  - Duplicate Router ID (LSAs are being flooded)

- Adv Router is not-reachable in topology Base with MTID 0 (Always check if the advertising router is reachable)
- Good Writeups to read:
  - [Why Are Some OSPF Routes in the Database but Not in the Routing Table?](https://www.cisco.com/c/en/us/support/docs/ip/open-shortest-path-first-ospf/7112-26.html)
  - [OSPF and MTU Mismatch](https://ine.com/blog/2011-03-30-ospf-and-mtu-mismatch)
```R10#show ip ospf database router 150.1.9.9

            OSPF Router with ID (150.1.10.10) (Process ID 1)

		Router Link States (Area 0)

  Adv Router is not-reachable in topology Base with MTID 0
  LS age: 10
  Options: (No TOS-capability, DC)
  LS Type: Router Links
  Link State ID: 150.1.9.9
  Advertising Router: 150.1.9.9
  LS Seq Number: 80000033
  Checksum: 0xB49
  Length: 60
  Number of Links: 3

    Link connected to: a Stub Network
     (Link ID) Network/subnet number: 150.1.9.9
     (Link Data) Network Mask: 255.255.255.255
      Number of MTID metrics: 0
       TOS 0 Metrics: 1

    Link connected to: a Stub Network
     (Link ID) Network/subnet number: 155.1.9.0
     (Link Data) Network Mask: 255.255.255.0
      Number of MTID metrics: 0
       TOS 0 Metrics: 1

    Link connected to: a Transit Network
     (Link ID) Designated Router address: 155.1.79.7
     (Link Data) Router Interface address: 155.1.79.9
      Number of MTID metrics: 0
       TOS 0 Metrics: 1
```
### Interpreting Debug Commands

