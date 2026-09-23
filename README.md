# 🔐 VTY Access Control Using ACL – Cisco Packet Tracer

## 📌 Project Overview

This lab demonstrates how to secure **remote management access to a Cisco switch** by configuring **SSH on VTY lines** and applying an **Access Control List (ACL)** to restrict which devices are allowed to access the switch remotely.

The objective is to ensure that only an **authorized management PC** can remotely access the switch, while unauthorized devices are denied.

This project was implemented using **Cisco Packet Tracer** and covers fundamental network security and remote-management concepts.

---

## 🎯 Objectives

* Configure IP addressing for end devices and network devices
* Configure a management VLAN on the switch
* Assign an IP address to the switch management interface
* Configure the switch default gateway
* Configure SSH for secure remote management
* Create a local username and password
* Generate RSA keys for SSH
* Restrict VTY access to SSH only
* Configure an ACL to permit an authorized management host
* Apply the ACL to the VTY lines
* Test authorized and unauthorized SSH access

---

## 🗺️ Network Topology

The topology consists of:

* 1 Cisco Router
* 1 Cisco Switch
* IT Department PCs
* Sales Department PCs
* 1 authorized management PC

The router provides connectivity between the different network segments, while the switch is configured for secure remote administration.

### Example Addressing

| Device / Network         | IP Address       |
| ------------------------ | ---------------- |
| IT Department Network    | `192.168.1.0/24` |
| Sales Department Network | `192.168.2.0/24` |
| Router – IT Interface    | `192.168.1.1`    |
| Router – Sales Interface | `192.168.2.1`    |
| Management VLAN          | VLAN 1           |
| Switch Management IP     | `192.168.2.15`   |
| Authorized Management PC | `192.168.1.5`    |

> IP addresses may be adjusted depending on the Packet Tracer topology.

---

## 🔧 Configuration Steps

### 1. Configure the Management VLAN

The switch uses **VLAN 1** as the management VLAN in this lab.

```cisco
Switch(config)# interface vlan 1
Switch(config-if)# ip address 192.168.2.15 255.255.255.0
Switch(config-if)# no shutdown
```

---

### 2. Configure the Switch Default Gateway

The router interface is configured as the switch's default gateway.

```cisco
Switch(config)# ip default-gateway 192.168.2.1
```

This allows the switch to communicate with devices outside its local subnet.

---

### 3. Configure a Local Username and Password

A local user account is created for SSH authentication.

```cisco
Switch(config)# username cisco secret Cisco123
```

---

### 4. Configure the Enable Secret

```cisco
Switch(config)# enable secret Cisco123
```

The enable secret protects privileged EXEC mode.

---

### 5. Configure the Domain Name

```cisco
Switch(config)# ip domain-name example.com
```

The domain name is required before generating RSA keys for SSH.

---

### 6. Generate RSA Keys

```cisco
Switch(config)# crypto key generate rsa
```

Select an appropriate key size when prompted.

Example:

```text
How many bits in the modulus [512]: 1024
```

RSA keys are required to establish SSH communication.

---

### 7. Configure VTY Lines for SSH

The VTY lines are configured to authenticate users against the local database.

```cisco
Switch(config)# line vty 0 15
Switch(config-line)# login local
Switch(config-line)# transport input ssh
```

### Why?

* `login local` → Uses the locally configured username and password.
* `transport input ssh` → Allows SSH and prevents Telnet access through the VTY lines.

---

### 8. Enable SSH Version 2

```cisco
Switch(config)# ip ssh version 2
```

SSH version 2 is used for secure remote management.

---

## 🛡️ 9. Create an ACL for Authorized Management Access

An ACL is created to permit only the authorized management PC.

Example:

```cisco
Switch(config)# access-list 10 permit host 192.168.1.5
```

This ACL allows the device with IP address:

```text
192.168.1.5
```

to access the VTY lines.

---

## 🔒 10. Apply the ACL to the VTY Lines

The ACL must be applied to the VTY interface; otherwise, simply creating the ACL will not restrict VTY access.

```cisco
Switch(config)# line vty 0 15
Switch(config-line)# access-class 10 in
```

The `in` direction controls incoming remote-management connections.

---

## 🧪 Testing & Verification

### Authorized Device

From the permitted management PC:

```bash
ssh -l cisco 192.168.2.15
```

The connection should be successful.

Expected result:

```text
Password:
Switch>
```

---

### Unauthorized Device

Attempt SSH access from a PC that is **not included in ACL 10**.

Example:

```bash
ssh -l cisco 192.168.2.15
```

The connection should be rejected.

Example result:

```text
Connection refused by remote host
```

This confirms that the VTY ACL is restricting remote access based on the source IP address.

---

## 🔍 Key Commands Used

| Command                   | Purpose                               |
| ------------------------- | ------------------------------------- |
| `interface vlan 1`        | Configure switch management interface |
| `ip address`              | Assign management IP address          |
| `ip default-gateway`      | Configure switch default gateway      |
| `username ... secret`     | Create local SSH user                 |
| `ip domain-name`          | Configure domain name                 |
| `crypto key generate rsa` | Generate RSA keys                     |
| `ip ssh version 2`        | Enable SSH version 2                  |
| `line vty 0 15`           | Configure VTY lines                   |
| `login local`             | Use local authentication database     |
| `transport input ssh`     | Allow SSH only                        |
| `access-list 10`          | Create standard ACL                   |
| `access-class 10 in`      | Apply ACL to VTY access               |

---

## 🧠 Concepts Practiced

* Cisco IOS CLI
* VLAN Management
* Switch Management IP
* Default Gateway
* SSH Remote Management
* Local User Authentication
* RSA Key Generation
* VTY Lines
* Standard IPv4 ACL
* `access-class`
* Remote Access Control
* Network Security
* Connectivity Testing
* SSH Troubleshooting

---

## 📊 Verification

The lab was verified by testing SSH access from multiple PCs:

| Source Device            | SSH Access | Result               |
| ------------------------ | ---------- | -------------------- |
| Authorized Management PC | Allowed    | ✅ Successful         |
| Unauthorized PC          | Denied     | ❌ Connection Refused |

This demonstrates that the ACL successfully restricts remote management access to the authorized host.

---

## 🚀 Key Takeaway

This lab demonstrates how **SSH + local authentication + VTY ACLs** can be combined to secure Cisco device management.

Instead of allowing every reachable device to attempt remote management, the VTY ACL limits access to a specifically authorized source IP.

This is an important foundational concept for **network security, Cisco administration, and secure remote device management**.

---

## 🛠️ Tools Used

* **Cisco Packet Tracer**
* Cisco IOS CLI
* SSH
* IPv4 ACL

---

## 📁 Project Structure

```text
VTY-Access-Control-ACL/
│
├── README.md
├── VTY-ACL.pkt
└── screenshots/
    ├── topology.png
    ├── ssh-authorized.png
    └── ssh-denied.png
```

---

## 👨‍💻 Skills Demonstrated

**Networking:**
`Cisco IOS` · `IPv4` · `VLAN` · `SSH` · `VTY` · `ACL` · `Remote Management`

**Security:**
`Access Control` · `SSH Authentication` · `Management Plane Security`

**Tools:**
`Cisco Packet Tracer` · `CLI Troubleshooting`

#Cisco #Networking #CCNA #CiscoPacketTracer #NetworkSecurity #SSH #ACL #NetworkEngineering
