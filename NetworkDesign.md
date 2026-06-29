# Design & Simulate a Multi-Network Infrastructure

## 🌟 Part 1: Network Design & IP Addressing Plan

### 1.1 Project Overview
This project focuses on designing and implementing a structured network for a **Food Distribution Company**. The network is designed to ensure high availability, security, and efficient data flow between different departments. The infrastructure is divided into five distinct subnets to manage traffic and enhance security policies.

### 1.2 Departmental Breakdown
The company’s operations are organized into the following functional areas:
- **Finance:** Handles sensitive accounting data and payroll.
- **Sales:** Manages high-volume customer orders and transactions.
- **Admin:** Supports general corporate operations and human resources.
- **IT:** Dedicated to network management and technical support.
- **Servers:** A centralized area hosting essential services like HTTP (Web) and DNS.

### 1.3 IP Addressing Table
The following table outlines the **IPv4** addressing scheme used for the project:
| Department (VLAN) | Network Address | Subnet Mask | Default Gateway |
| :--- | :--- | :--- | :--- |
| Finance | 192.168.10.0 | 255.255.255.0 | 192.168.10.1 |
| Sales | 192.168.20.0 | 255.255.255.0 | 192.168.20.1 |
| Admin | 192.168.30.0 | 255.255.255.0 | 192.168.30.1 |
| IT | 192.168.40.0 | 255.255.255.0 | 192.168.40.1 |
| Servers | 192.168.50.0 | 255.255.255.0 | 192.168.40.1 |

### 1.4 Network Diagram
The proposed network topology is designed using a **Star-Hierarchical** approach. In this design, a central **Cisco 2911 Router** serves as the core of the infrastructure, interconnecting three primary directions (Top, Left, and Right). Each direction is managed by a switch that supports one or more departments through logical segmentation.

![Diagram](Images/Diagram.png)

### 1.5 Design Justification
- **Easy to Expand (Scalability):** Using separate switches for each side makes it easy to add more computers in the future without disturbing the rest of the network.
- **Better Organization (Logical Segmentation):** We used "Sub-interfaces" on the router to keep departments like Finance private and safe from general traffic, while still allowing them to talk to other units when needed.
- **Smart Use of Tools (Resource Efficiency):** This design uses the router’s high-speed ports effectively to make sure everyone can access the Server Farm quickly and without delay.

---
## 🌟 Part 2 – Network Implementation

### 2.1 Building the Topology
The physical network was constructed in **Cisco Packet Tracer** using a **Cisco 2911 Router** and three **Cisco 2960 Switches**. The organization is divided into five logical units: **Admin, IT, Servers, Finance, and Sales**. Each unit is connected to the central router to ensure a structured star topology.

![Topology](Images/Topology.png)

### 2.2 Assigning IP Addresses
Following the predefined addressing plan, static IP addresses and Subnet Masks were assigned to all end-devices. Each PC and Server was configured with its specific **Default Gateway** (the router’s sub-interface IP) to allow communication outside its own network.

### 2.3 Router Interface Configuration
The router was configured to handle multiple networks through a single physical port using the Router-on-a-Stick method.
- **Encapsulation:** 802.1Q protocol was used to distinguish between different VLANs.
- **Sub-interfaces:** Separate virtual interfaces were created (e.g., G0/1.10, G0/1.20) to act as gateways for each department.

```
enable
configure terminal
interface gig0/0
no shutdown
interface gig0/0.40
encapsulation dot1Q 40
ip address 192.168.40.1 255.255.255.0
interface gig0/0.50
encapsulation dot1Q 50
ip address 192.168.50.1 255.255.255.0
interface gig0/1
no shutdown
interface gig0/1.10
encapsulation dot1Q 10
ip address 192.168.10.1 255.255.255.0
interface gig0/1.20
encapsulation dot1Q 20
ip address 192.168.20.1 255.255.255.0
interface gig0/2
no shutdown
ip address 192.168.30.1 255.255.255.0
exit
exit
write memory
```

![Router](Images/Router.jpg)

### 2.4 Switch Configuration (Left and Right)
The switches were configured to support logical separation using VLANs.
- **VLAN Creation:** VLANs 10, 20, 40, and 50 were created and named according to the departments.
- **Access Ports:** Specific ports were assigned to each VLAN to keep department traffic isolated.
- **Trunk Ports:** The ports connecting the switches to the router were set to Trunk Mode to allow all VLAN traffic to pass through.

**🔗 Left Switch**
```
enable
configure terminal
vlan 40
name IT_Department
exit
vlan 50
name Server_Farm
exit
interface range fa0/1-10
switchport mode access
switchport access vlan 40
interface range fa0/11-20
switchport mode access
switchport access vlan 50
interface gig0/1
switchport mode trunk
exit
exit
write memory
```
![SW-L](Images/SW-L.jpg)

**🔗 Right Switch**
```
enable
configure terminal
vlan 10
name Finance
exit
vlan 20
name Sales
exit
interface range fa0/1-10
switchport mode access
switchport access vlan 10
interface range fa0/11-20
switchport mode access
switchport access vlan 20
interface gig0/1
switchport mode trunk
exit
exit
write memory
```
![SW-R](Images/SW-R.jpg)

