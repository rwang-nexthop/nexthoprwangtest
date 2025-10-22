# NextHop Lab - T2 Topology

This lab demonstrates a T2 (Two-Tier) network topology with SONiC DUTs and Nokia SR Linux nodes.

## Topology Overview

The topology consists of:

- **2 SONiC DUTs** (Linecards): `dut0` and `dut1`
  - Kind: `sonic-vs`
  - Image: `container-registry.sw.internal.nexthop.ai/clab-sonic:10368`
  - ASN: 65100

- **2 Nokia SR Linux T3 Nodes** (Core): `srl01t3` and `srl03t3`
  - Kind: `nokia_srlinux`
  - Image: `ghcr.io/nokia/srlinux`
  - ASN: 65200 and 65201

- **2 Nokia SR Linux T1 Nodes** (Leaf): `srl01t1` and `srl03t1`
  - Kind: `nokia_srlinux`
  - Image: `ghcr.io/nokia/srlinux`
  - ASN: 65000

## Connectivity

### DUT0 Connections
- `Ethernet0` ↔ `srl01t3:e1-1` (LAG member)
- `Ethernet1` ↔ `srl01t3:e1-2` (LAG member)
- `Ethernet2` ↔ `srl03t3:e1-1` (single routed port)
- `Ethernet3` ↔ `dut1:Ethernet3` (inter-DUT link)

### DUT1 Connections
- `Ethernet0` ↔ `srl01t1:e1-1` (LAG member)
- `Ethernet1` ↔ `srl01t1:e1-2` (LAG member)
- `Ethernet2` ↔ `srl03t1:e1-1` (single routed port)
- `Ethernet3` ↔ `dut0:Ethernet3` (inter-DUT link)

## IP Addressing

### Loopback Addresses
- DUT0: `10.1.0.1/32`
- DUT1: `10.1.0.2/32`
- SRL01T3: `100.1.0.1/32`
- SRL03T3: `100.1.0.3/32`
- SRL01T1: `100.1.0.4/32`
- SRL03T1: `100.1.0.6/32`

### Link Addresses

#### DUT0 Links
- DUT0 Ethernet0 ↔ SRL01T3 e1-1: `10.0.0.0/31` - `10.0.0.1/31`
- DUT0 Ethernet1 ↔ SRL01T3 e1-2: `10.0.0.2/31` - `10.0.0.3/31`
- DUT0 Ethernet2 ↔ SRL03T3 e1-1: `10.0.0.4/31` - `10.0.0.5/31`
- DUT0 Ethernet3 ↔ DUT1 Ethernet3: `10.0.5.1/31` - `10.0.5.2/31`

#### DUT1 Links
- DUT1 Ethernet0 ↔ SRL01T1 e1-1: `10.0.0.6/31` - `10.0.0.7/31`
- DUT1 Ethernet1 ↔ SRL01T1 e1-2: `10.0.0.8/31` - `10.0.0.9/31`
- DUT1 Ethernet2 ↔ SRL03T1 e1-1: `10.0.0.10/31` - `10.0.0.11/31`

## BGP Configuration

All nodes run BGP with the following ASNs:
- DUT0 & DUT1: ASN 65100
- SRL01T3: ASN 65200
- SRL03T3: ASN 65201
- SRL01T1 & SRL03T1: ASN 65000

BGP neighbors are established over the point-to-point links with IPv4 and IPv6 support.

## Deployment

To deploy this lab:

```bash
cd lab-examples/nexthop-lab
clab deploy -t nexthop-lab.clab.yml
```

To destroy the lab:

```bash
clab destroy -t nexthop-lab.clab.yml
```

## Verification

After deployment, you can verify connectivity:

```bash
# Check node status
clab inspect -t nexthop-lab.clab.yml

# Access a Nokia SR Linux node
clab exec -t nexthop-lab.clab.yml --label clab-node-name=srl01t3 -- sr_cli

# Check BGP status on Nokia SR Linux nodes
clab exec -t nexthop-lab.clab.yml --label clab-node-name=srl01t3 -- sr_cli -c "show network-instance default protocols bgp summary"

# Access SONiC DUT
clab exec -t nexthop-lab.clab.yml --label clab-node-name=dut0 -- bash
```

## Files

- `nexthop-lab.clab.yml` - Main topology definition
- `dut0-startup.conf` - DUT0 startup configuration (JSON format)
- `dut1-startup.conf` - DUT1 startup configuration (JSON format)
- `srl01t3-startup.cfg` - SRL01T3 startup configuration (Nokia SR Linux format)
- `srl03t3-startup.cfg` - SRL03T3 startup configuration (Nokia SR Linux format)
- `srl01t1-startup.cfg` - SRL01T1 startup configuration (Nokia SR Linux format)
- `srl03t1-startup.cfg` - SRL03T1 startup configuration (Nokia SR Linux format)
- `README.md` - This file

