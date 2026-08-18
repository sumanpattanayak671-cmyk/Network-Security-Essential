# Configuring Cisco Routing Protocol Authentication

This lab demonstrates authentication configuration for three Cisco routing protocols:

- RIP
- EIGRP
- OSPF

## Topology

```text
R1 ───────── R2
     RIP
  30.0.0.0/30

R3 ───────── R4
    EIGRP
  10.0.0.0/24

R5 ───────── R6
    OSPF
  20.0.0.0/25
```

---

# 1. RIP

## 1.1 Assign IP Address

### R1

```cisco
R1(config)# interface s1/0
R1(config-if)# ip address 30.0.0.1 255.255.255.252
R1(config-if)# no shutdown
R1(config-if)# exit
```

### R2

```cisco
R2(config)# interface s1/0
R2(config-if)# ip address 30.0.0.2 255.255.255.252
R2(config-if)# no shutdown
R2(config-if)# exit
```

## 1.2 Configure RIP

### R1

```cisco
R1(config)# router rip
R1(config-router)# version 2
R1(config-router)# network 30.0.0.0
```

### R2

```cisco
R2(config)# router rip
R2(config-router)# version 2
R2(config-router)# network 30.0.0.0
```

## 1.3 Configure RIP Authentication

### R1

```cisco
R1(config)# key chain R1
R1(config-keychain)# key 1
R1(config-keychain-key)# key-string 1234
R1(config-keychain-key)# exit
R1(config-keychain)# exit

R1(config)# interface s1/0
R1(config-if)# ip rip authentication mode md5
R1(config-if)# ip rip authentication key-chain R1
```

### R2

```cisco
R2(config)# key chain R1
R2(config-keychain)# key 1
R2(config-keychain-key)# key-string 1234
R2(config-keychain-key)# exit
R2(config-keychain)# exit

R2(config)# interface s1/0
R2(config-if)# ip rip authentication mode md5
R2(config-if)# ip rip authentication key-chain R1
```

---

# 2. EIGRP

## 2.1 Configure EIGRP Authentication on R3

```cisco
R3(config)# key chain REIGRP
R3(config-keychain)# key 1
R3(config-keychain-key)# key-string 1234
R3(config-keychain-key)# exit
R3(config-keychain)# exit

R3(config)# interface s1/0
R3(config-if)# ip address 10.0.0.1 255.255.255.0
R3(config-if)# ip authentication mode eigrp 100 md5
R3(config-if)# ip authentication key-chain eigrp 100 REIGRP
R3(config-if)# no shutdown
R3(config-if)# exit
```

## 2.2 Configure EIGRP Routing on R3

```cisco
R3(config)# router eigrp 100
R3(config-router)# network 10.0.0.0 0.0.0.255
R3(config-router)# no auto-summary
```

## 2.3 Configure EIGRP Authentication on R4

```cisco
R4(config)# key chain REIGRP
R4(config-keychain)# key 1
R4(config-keychain-key)# key-string 1234
R4(config-keychain-key)# exit
R4(config-keychain)# exit

R4(config)# interface s1/0
R4(config-if)# ip address 10.0.0.2 255.255.255.0
R4(config-if)# ip authentication mode eigrp 100 md5
R4(config-if)# ip authentication key-chain eigrp 100 REIGRP
R4(config-if)# no shutdown
R4(config-if)# exit
```

## 2.4 Configure EIGRP Routing on R4

```cisco
R4(config)# router eigrp 100
R4(config-router)# network 10.0.0.0 0.0.0.255
R4(config-router)# no auto-summary
```

---

# 3. OSPF

## 3.1 Configure OSPF on R5

```cisco
R5(config)# router ospf 1
R5(config-router)# area 0 authentication message-digest
R5(config-router)# exit

R5(config)# interface s1/0
R5(config-if)# ip ospf authentication-key OSPF
```

## 3.2 Configure OSPF on R6

```cisco
R6(config)# router ospf 1
R6(config-router)# area 0 authentication message-digest
R6(config-router)# exit

R6(config)# interface s1/0
R6(config-if)# ip ospf authentication-key OSPF
```

---

# Important Notes

- RIP uses **MD5 authentication** with a key chain.
- EIGRP uses **MD5 authentication** with a key chain.
- OSPF is configured for **Area 0 authentication**.
- The authentication key/password must match on both routers.
- RIP network: `30.0.0.0/30`
- EIGRP network: `10.0.0.0/24`
- OSPF network: `20.0.0.0/25`
- EIGRP AS number: `100`
- RIP version: `2`
