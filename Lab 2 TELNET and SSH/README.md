# Lab 02 - Configuring and Establishing Terminal Sessions through TELNET and SSH

## Objective

To configure and establish remote terminal sessions using **TELNET and SSH**, and to perform router configuration backup and restoration using a **TFTP server**.

---

## 1. TELNET Configuration

Configure the VTY lines on R2:

```text
R2(config)# line vty 0 3
R2(config-line)# password 1234
R2(config-line)# login local
R2(config-line)# exit

R2(config)# username suman password 1234
R2(config)# do write

R2(config)# enable secret 1234
R2(config)# do write

R2(config)# hostname Kolkata
```

### Establish TELNET Session

From R1, connect to R2:

```text
R1# telnet 20.0.0.20
```

Enter the configured username and password when prompted.

---

## 2. SSH Configuration

Configure SSH on R3:

```text
R3(config)# line vty 0 5
R3(config-line)# login local
R3(config-line)# exit

R3(config)# username suman password 1234

R3(config)# ip domain-name lab06.com

R3(config)# line vty 0 3
R3(config-line)# login local
R3(config-line)# transport input ssh
R3(config-line)# exit
```

Generate the RSA cryptographic key:

```text
R3(config)# crypto key generate rsa
```

When prompted for the modulus size, enter:

```text
How many bits in the modulus [512]: 1024
```

Save the configuration:

```text
R3(config)# do write
```

### Establish SSH Session

From R4:

```text
R4# ssh -l suman 20.0.0.6
```

Password:

```text
1234
```

---

## 3. TFTP Server Configuration

Configure the TFTP server with the following network settings:

```text
IP Address: 10.0.0.20
Subnet Mask: 255.0.0.0
Default Gateway: 10.0.0.1
```

Then:

1. Open **Services** on the server.
2. Select **TFTP**.
3. Turn the TFTP service **ON**.

---

## 4. Backup Router Configuration to TFTP

First save the running configuration:

```text
R1# copy running-config startup-config
```

Copy the startup configuration to the TFTP server:

```text
R1# copy startup-config tftp:
```

When prompted:

```text
Address or name of remote host: 10.0.0.20
Destination filename: backup
```

The router configuration is now backed up to the TFTP server.

---

## 5. Restore Configuration from TFTP

On the replacement router, use:

```text
R2# copy tftp: startup-config
```

Enter the TFTP server address:

```text
Address or name of remote host: 10.0.0.20
```

Enter the backup filename:

```text
Source filename: backup
```

For the destination filename, press **Enter** to use the default:

```text
Destination filename [startup-config]:
```

---

## 6. Important Commands

| Purpose              | Command                        |
| -------------------- | ------------------------------ |
| Configure VTY        | `line vty 0 3`                 |
| Set VTY password     | `password 1234`                |
| Local authentication | `login local`                  |
| Create username      | `username suman password 1234` |
| Set hostname         | `hostname Kolkata`             |
| Set domain name      | `ip domain-name lab06.com`     |
| Allow SSH            | `transport input ssh`          |
| Generate RSA key     | `crypto key generate rsa`      |
| TELNET               | `telnet 20.0.0.20`             |
| SSH                  | `ssh -l suman 20.0.0.6`        |
| Backup to TFTP       | `copy startup-config tftp:`    |
| Restore from TFTP    | `copy tftp: startup-config`    |
| Save configuration   | `do write`                     |

---

## Conclusion

In this lab, **TELNET and SSH** were configured for remote router access. SSH provides a more secure remote-access method because it encrypts the communication. A **TFTP server** was also configured to back up and restore router configuration files.

