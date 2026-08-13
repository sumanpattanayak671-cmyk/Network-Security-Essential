
1. Set Password
Router> enable
Router# configure terminal
Router(config)# enable secret 12345
Router(config)# exit
Router# copy running-config startup-config
Router# exit
2. Reset Router Password
Restart the router and press Ctrl+C / Break to enter ROMMON mode.

rommon 1> confreg 0x2142
rommon 1> reset

Router> enable
Router# copy startup-config running-config

Purpose: 0x2142 makes the router ignore the startup configuration during boot, allowing password recovery.

3. Console Configuration
Router# configure terminal
Router(config)# line console 0
Router(config-line)# password 1234
Router(config-line)# login
Router(config-line)# exit
Router(config)# exit
Router# write
4. AUX Configuration
Router# configure terminal
Router(config)# line aux 0
Router(config-line)# password 1234
Router(config-line)# login
Router(config-line)# exit
Router(config)# exit
Router# write

Note from your page: AUX access requires the appropriate router password configuration.

5. Enable Password
Enable password
Router(config)# enable password 1234

This is stored as plain/less-secure text.

Enable secret
Router(config)# enable secret 1234

This is more secure because it is stored in hashed form.

Encrypt passwords
Router(config)# service password-encryption

This encrypts applicable plaintext passwords in the configuration.

6. VTY Configuration

Your notes show:

R1> enable
R1# configure terminal
R1(config)# line vty 0 2
R1(config-line)# password 1234
R1(config-line)# login
R1(config-line)# exit
R1(config)# exit
R1# write

Your page also indicates:

R1 IP = 20.0.0.1
7. VTY + Local Username

From the third page, I can read the following:

R1# configure terminal
R1(config)# line vty 0 2
R1(config-line)# password 1234
R1(config-line)# login
R1(config-line)# exit

R1(config)# username suman password 1234
R1(config)# exit
R1# write

Then the notes show a connection from R2 to R1, apparently using:

R2# telnet 20.0.0.1

Username:

suman

Password:

1234
8. Minimum Password Length

At the bottom of the third page, I can clearly make out:

Router(config)# security passwords min-length 8

This sets the minimum password length to 8 characters.

Clean lab structure

You can organize these in your README.md as:

# Lab 01 - Cisco Password Policy

## 1. Set Password
## 2. Password Recovery
## 3. Console Configuration
## 4. AUX Configuration
## 5. Enable Password vs Enable Secret
## 6. VTY Configuration
## 7. Local Username Authentication
## 8. Minimum Password Length

One important correction: your handwritten notes sometimes use password 1234 and enable secret 12345. Keep those values only if they're specifically required for your lab; for a real router, use strong unique passwords.
