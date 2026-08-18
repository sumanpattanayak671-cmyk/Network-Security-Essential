# AAA Authentication with EIGRP – Cisco Packet Tracer Lab

## Topology

The topology consists of:

* 2 Routers: R1 and R2
* 2 Switches
* 1 AAA/TACACS+ Server
* 4 PCs

### IP Addressing

| Device | Interface | IP Address      | Subnet Mask       | Default Gateway |
| ------ | --------- | --------------- | ----------------- | --------------- |
| R1     | Fa0/0     | `192.168.0.1`   | `255.255.255.128` | —               |
| R1     | Fa0/1     | `192.168.20.1`  | `255.255.255.252` | —               |
| R1     | Se0/0/0   | `200.0.0.1`     | `255.255.255.252` | —               |
| R2     | Fa0/0     | `192.168.0.129` | `255.255.255.128` | —               |
| R2     | Se0/0/0   | `200.0.0.2`     | `255.255.255.252` | —               |
| Server | —         | `192.168.20.2`  | `255.255.255.252` | `192.168.20.1`  |
| PC1    | —         | `192.168.0.2`   | `255.255.255.128` | `192.168.0.1`   |
| PC2    | —         | `192.168.0.3`   | `255.255.255.128` | `192.168.0.1`   |
| PC3    | —         | `192.168.0.130` | `255.255.255.128` | `192.168.0.129` |
| PC4    | —         | `192.168.0.131` | `255.255.255.128` | `192.168.0.129` |

---

# Step 1: Configure IP Addresses

## R1 Configuration

```text
R1> enable
R1# configure terminal

R1(config)# interface fa0/0
R1(config-if)# ip address 192.168.0.1 255.255.255.128
R1(config-if)# no shutdown
R1(config-if)# exit

R1(config)# interface fa0/1
R1(config-if)# ip address 192.168.20.1 255.255.255.252
R1(config-if)# no shutdown
R1(config-if)# exit

R1(config)# interface se0/0/0
R1(config-if)# ip address 200.0.0.1 255.255.255.252
R1(config-if)# no shutdown
R1(config-if)# exit
```

## R2 Configuration

```text
R2> enable
R2# configure terminal

R2(config)# interface fa0/0
R2(config-if)# ip address 192.168.0.129 255.255.255.128
R2(config-if)# no shutdown
R2(config-if)# exit

R2(config)# interface se0/0/0
R2(config-if)# ip address 200.0.0.2 255.255.255.252
R2(config-if)# no shutdown
R2(config-if)# exit
```

---

# PC Configuration

## Server

```text
IP Address:      192.168.20.2
Subnet Mask:     255.255.255.252
Default Gateway: 192.168.20.1
```

## PC1

```text
IP Address:      192.168.0.2
Subnet Mask:     255.255.255.128
Default Gateway: 192.168.0.1
```

## PC2

```text
IP Address:      192.168.0.3
Subnet Mask:     255.255.255.128
Default Gateway: 192.168.0.1
```

## PC3

```text
IP Address:      192.168.0.130
Subnet Mask:     255.255.255.128
Default Gateway: 192.168.0.129
```

## PC4

```text
IP Address:      192.168.0.131
Subnet Mask:     255.255.255.128
Default Gateway: 192.168.0.129
```

---

# Step 2: Configure EIGRP Routing

## R1

```text
R1# configure terminal

R1(config)# router eigrp 100
R1(config-router)# no auto-summary
R1(config-router)# network 192.168.0.0 0.0.0.127
R1(config-router)# network 192.168.20.0 0.0.0.3
R1(config-router)# network 200.0.0.0 0.0.0.3
R1(config-router)# exit
```

## R2

```text
R2# configure terminal

R2(config)# router eigrp 100
R2(config-router)# no auto-summary
R2(config-router)# network 192.168.0.128 0.0.0.127
R2(config-router)# network 200.0.0.0 0.0.0.3
R2(config-router)# exit
```

## Verify EIGRP

On both routers:

```text
show ip route
show ip eigrp neighbors
show ip protocols
```

You should see EIGRP-learned routes marked with `D`.

---

# Step 3: Configure AAA Authentication

AAA stands for **Authentication, Authorization and Accounting**.

In this lab, TACACS+ is used for centralized authentication.

## R1 AAA Configuration

```text
R1# configure terminal

R1(config)# username Suman password 1234
R1(config)# aaa new-model

R1(config)# tacacs server AAA-SERVER
R1(config-server-tacacs)# address ipv4 192.168.20.2
R1(config-server-tacacs)# key abcdef
R1(config-server-tacacs)# exit

R1(config)# aaa authentication login default group tacacs+ local

R1(config)# line vty 0 4
R1(config-line)# login authentication default
R1(config-line)# exit
```

> **Note:** If your Packet Tracer version uses the older TACACS+ syntax, you may use:
>
> `tacacs-server host 192.168.20.2`
>
> `tacacs-server key abcdef`

The `local` keyword provides a fallback to the local username if the TACACS+ server is unavailable.

---

# Step 4: Configure the AAA Server

Open the **Server** in Packet Tracer.

Go to:

**Services → AAA**

Turn the **AAA service ON**.

Create a user:

```text
Username: Suman
Password: 1234
```

Add the network device/client:

```text
Client Name: R1
Client IP: 192.168.20.1
Server Type: TACACS
Key: abcdef
```

> The **Client IP** must be the IP address of the R1 interface that communicates with the AAA server.

---

# Step 5: Test AAA Authentication

From R2, try connecting to R1 using Telnet:

```text
R2# telnet 192.168.20.1
```

Enter:

```text
Username: Suman
Password: 1234
```

If the TACACS+ server is working correctly, R1 should authenticate the user through the AAA server.

---

# Step 6: Verify the Configuration

On R1:

```text
show running-config
show aaa servers
show ip route
show ip eigrp neighbors
```

Test connectivity:

```text
ping 192.168.20.2
ping 200.0.0.2
```

---

# Important Wildcard Masks

| Network            | Subnet Mask       | Wildcard Mask |
| ------------------ | ----------------- | ------------- |
| `192.168.0.0/25`   | `255.255.255.128` | `0.0.0.127`   |
| `192.168.0.128/25` | `255.255.255.128` | `0.0.0.127`   |
| `192.168.20.0/30`  | `255.255.255.252` | `0.0.0.3`     |
| `200.0.0.0/30`     | `255.255.255.252` | `0.0.0.3`     |

# Final Verification

The complete lab should achieve:

* IP addressing configured on all devices
* EIGRP AS `100` configured on R1 and R2
* R1 and R2 successfully forming an EIGRP neighbor relationship
* Routes exchanged between R1 and R2
* AAA/TACACS+ service enabled on the server
* R1 configured for centralized AAA authentication
* Telnet login authenticated through the AAA server
