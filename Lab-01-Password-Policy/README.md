# Lab 01 – Cisco Password Policy

## Objective

To configure and manage password security on a Cisco router, including enable passwords, console access, AUX access, VTY access, password recovery, and minimum password length.

---

## 1. Set Enable Secret Password

Enter privileged EXEC mode and configure an encrypted enable secret password.

```text
Router> enable
Router# configure terminal
Router(config)# enable secret 12345
Router(config)# exit
Router# copy running-config startup-config
Router# exit
```

The `enable secret` password is stored more securely than an `enable password`.

---

## 2. Reset / Recover Router Password

If the router password is forgotten, password recovery can be performed through ROMMON mode.

Restart the router and press **Ctrl+C / Break** during boot to enter ROMMON mode.

```text
rommon 1> confreg 0x2142
rommon 1> reset
```

After the router restarts:

```text
Router> enable
Router# copy startup-config running-config
```

The `0x2142` configuration register allows the router to boot without loading the startup configuration, which helps during password recovery.

---

## 3. Console Password Configuration

Configure a password for console access.

```text
Router# configure terminal
Router(config)# line console 0
Router(config-line)# password 1234
Router(config-line)# login
Router(config-line)# exit
Router(config)# exit
Router# write
```

The `login` command tells the router to require the configured password when accessing the console line.

---

## 4. AUX Line Configuration

Configure password authentication for the AUX line.

```text
Router# configure terminal
Router(config)# line aux 0
Router(config-line)# password 1234
Router(config-line)# login
Router(config-line)# exit
Router(config)# exit
Router# write
```

---

## 5. Enable Password vs Enable Secret

### Enable Password

```text
Router(config)# enable password 1234
```

`enable password` provides basic password protection and is less secure.

### Enable Secret

```text
Router(config)# enable secret 1234
```

`enable secret` is preferred because the password is stored in a hashed form.

### Password Encryption

To encrypt applicable plaintext passwords in the configuration:

```text
Router(config)# service password-encryption
```

---

## 6. VTY Configuration

VTY lines are used for remote access to the Cisco router.

Example configuration:

```text
R1> enable
R1# configure terminal
R1(config)# line vty 0 2
R1(config-line)# password 1234
R1(config-line)# login
R1(config-line)# exit
R1(config)# exit
R1# write
```

Example router IP from the lab:

```text
R1 IP = 20.0.0.1
```

---

## 7. Local Username Authentication

Create a local username and password:

```text
R1# configure terminal
R1(config)# username suman password 1234
R1(config)# exit
R1# write
```

The local username can be used for authentication when VTY access is configured to use the local database.

---

## 8. Minimum Password Length

To enforce a minimum password length:

```text
Router(config)# security passwords min-length 8
```

This requires newly configured passwords to meet the minimum length requirement.

---

## Important Commands

| Command                              | Purpose                                 |
| ------------------------------------ | --------------------------------------- |
| `enable`                             | Enter privileged EXEC mode              |
| `configure terminal`                 | Enter global configuration mode         |
| `enable password`                    | Configure an enable password            |
| `enable secret`                      | Configure a more secure enable password |
| `line console 0`                     | Configure console access                |
| `line aux 0`                         | Configure AUX access                    |
| `line vty 0 2`                       | Configure VTY remote-access lines       |
| `login`                              | Enable password authentication          |
| `service password-encryption`        | Encrypt applicable plaintext passwords  |
| `security passwords min-length 8`    | Set minimum password length             |
| `copy running-config startup-config` | Save running configuration              |
| `write`                              | Save the configuration                  |

---

## Conclusion

This lab demonstrates basic Cisco router password security and password recovery techniques. It covers enable authentication, console and AUX passwords, VTY remote access, local username authentication, password encryption, minimum password length, and password recovery using ROMMON.
