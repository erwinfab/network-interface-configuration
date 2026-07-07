# Network Interface Configuration Lab (Red Hat Enterprise Linux)

## Project Overview
This project demonstrates the ability to configure complex manual network settings on a Red Hat Enterprise Linux server. The objective is to provision multiple static IPv4 networks on a single primary hardware interface using modern `NetworkManager` CLI utilities, automate interface connection states across reboots, and establish localized DNS maps.  

## Environments and Technologies Used

* **Red Hat Enterprise Linux 9** (Operating System Environment)
* **NetworkManager CLI** (`nmcli`) (Interface Profile Management)
* **Linux IPROUTE2** (`ip`) (Layer 2 & Layer 3 Verification Daemon)
* **POSIX Switch Configuration Database** (`/etc/hosts`) (Local Name Resolution)

## Objectives and Scenario
The enterprise environment requires an administrator to configure persistent network connections on `serverb` matching strict structural parameter bounds:  

### Technical Specification Requirements:

* **Connection Profile Name**: `lab`
* **Target Interface Assignment**: Variable interface corresponding to MAC Address `52:54:00:00:fa:0b`
* **Primary Subnet Configuration**: `172.25.250.11/24` | Gateway: `172.25.250.254` | DNS: `172.25.250.254`   
* **Secondary Management Subnet**: `10.0.1.1/24`   
* **Local Translation Routing Rule**: Resolve `10.0.1.1` cleanly via the alias `private` 

## 🛠️ High-Level Deployment and Configuration Steps

**Step 1: Target Interface Identification**
* Before creating network profiles, query the local hardware configuration to isolate the logical network interface bound to the hardware Layer 2 MAC link address `52:54:00:00:fa:0b`.
