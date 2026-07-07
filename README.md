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
* **Primary Subnet Configuration**: IP: `172.25.250.11/24` | Gateway: `172.25.250.254` | DNS: `172.25.250.254`   
* **Secondary Management Subnet**: `10.0.1.1/24`   
* **Local Translation Routing Rule**: Resolve `10.0.1.1` cleanly via the alias `private` 

## 🛠️ High-Level Deployment and Configuration Steps

**Step 1: Target Interface Identification**
* Before creating network profiles, query the local hardware configuration to isolate the logical network interface bound to the hardware Layer 2 MAC link address `52:54:00:00:fa:0b`.

<img width="909" height="388" alt="image" src="https://github.com/user-attachments/assets/d153649f-f085-4157-9b4e-252785133894" />

**Step 2: Connection Provisioning and Primary Address Assignment**
* Build a persistent configuration profile matching the strict parameter limits provided by the infrastructure scenario.

<img width="916" height="459" alt="image" src="https://github.com/user-attachments/assets/b0b1d452-ac4e-4edc-9e8b-4230f9eb19cc" />

**Step 3: Interface Multi-Homing and Boot Automation**
* Append the secondary administrative private network layer onto the newly provisioned profile. Ensure the profile initializes automatically during power states, while disabling autostart loops on obsolete default configurations.

   * Append the secondary IPv4 address without removing the primary:

 `nmcli connection modify lab +ipv4.addresses 10.0.1.1/24`

   * Enforce boot autostart configuration policies:

`nmcli connection modify lab connection.autoconnect yes`

`nmcli connection modify "Wired connection 1" connection.autoconnect no`

**🔧 Technical Note & State Realignment Verification**:
During the initial execution of this setup, checking the operational connection parameters using nmcli connection show revealed that the newly created lab profile was successfully written to disk but sat inactive (indicated by a trailing device state parameter of --). This occurred because the default interface connection profile (Wired connection 1) still maintained an exclusive runtime lock on the physical interface.

To resolve this profile collision and cleanly transition runtime ownership over to the enterprise layout without requiring a disruptive system bounce, the hardware context was explicitly assigned and initialized directly through the CLI:

  * Explicitly bind the configuration profile to the hardware interface:
  
`nmcli connection modify lab ifname eth0`

  * Force active interface switchover execution:
  
`nmcli connection up lab`

<img width="913" height="689" alt="image" src="https://github.com/user-attachments/assets/a3a01a4b-120a-46fc-b04c-8afd21172b21" />
The capture highlights the system tracking state transformations. Notice the top lookup showing Wired connection 1 actively mapping to eth0 while lab sits unassigned. The manual runtime modification sequence overrides this, resulting in the successful, live activation of the lab layout (turning green) over the eth0 data lane.

**Step 4: Local Name Resolution Services**
* Modify the local POSIX network tables to bind the secondary management sub-network interface directly to an administrative network alias (`vi /etc/hosts`).  

 * Add the routing map line at the bottom of the system database file:  
<img width="801" height="376" alt="image" src="https://github.com/user-attachments/assets/7475386c-6be9-4f7c-bdcc-e69e0de0e4e0" />















  
