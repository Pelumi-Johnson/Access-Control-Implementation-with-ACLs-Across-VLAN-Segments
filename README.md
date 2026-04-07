# Access Control Implementation with ACLs Across VLAN Segments

## Overview
Designed and implemented network access control using standard ACLs to regulate communication between VLAN segments. Enforced traffic restrictions between VLAN 10 HR and VLAN 20 IT while introducing VLAN 30 Finance with controlled access to both networks.

## Objective
Configured and applied access control policies to restrict unauthorized inter VLAN communication while maintaining required connectivity for designated network segments.

## Network Setup
Devices Used
1 Cisco 2960 Switch
1 Cisco 1941 Router
3 End Devices PCs

Topology Design

PC0 assigned to VLAN 10 HR

PC1 assigned to VLAN 20 IT

PC2 assigned to VLAN 30 Finance

Switch connected to router via trunk link FastEthernet0/24 to GigabitEthernet0/0
Router configured with subinterfaces for VLAN routing

![Network Topology](https://github.com/Pelumi-Johnson/Access-Control-Implementation-with-ACLs-Across-VLAN-Segments/blob/main/Screenshot%202026-04-07%20154157.png)

## Configuration

### VLAN Configuration and Assignment
Configured VLAN 10 HR VLAN 20 IT and VLAN 30 Finance
Assigned switch access ports to respective VLANs

### Trunk Configuration
Configured uplink port to carry VLAN traffic between switch and router
```
enable
configure terminal
interface fastEthernet 0/24
switchport mode trunk
exit
```
Screenshot Placeholder Trunk Verification
![Trunk Verification](https://github.com/Pelumi-Johnson/Access-Control-Implementation-with-ACLs-Across-VLAN-Segments/blob/main/Screenshot%202026-04-05%20130703.png)

### Router Subinterfaces
Configured Router on a Stick for all VLANs including newly added VLAN 30
```
enable
configure terminal

interface gigabitEthernet 0/0.10
encapsulation dot1Q 10
ip address 192.168.10.1 255.255.255.0

interface gigabitEthernet 0/0.20
encapsulation dot1Q 20
ip address 192.168.20.1 255.255.255.0

interface gigabitEthernet 0/0.30
encapsulation dot1Q 30
ip address 192.168.30.1 255.255.255.0

interface gigabitEthernet 0/0
no shutdown
```
Screenshot Placeholder Router Subinterfaces
![Router Config](./screenshots/router-config.png)

### Access Control Configuration
Implemented ACL to restrict HR from accessing IT while allowing other traffic
```
enable
configure terminal
access-list 10 deny 192.168.10.0 0.0.0.255
access-list 10 permit any

interface gigabitEthernet 0/0.20
ip access-group 10 in
```
Screenshot Placeholder ACL Configuration
![ACL Config](./screenshots/acl-config.png)

## Validation
```
### Access Control Testing
From VLAN 10 HR
ping 192.168.20.10
Result request timed out confirming traffic restriction
```
From VLAN 20 IT
ping 192.168.10.10
Result successful confirming permitted return traffic

Screenshot Placeholder ACL Test Failure
![ACL Block](./screenshots/acl-block.png)

Screenshot Placeholder ACL Test Success
![ACL Allow](./screenshots/acl-allow.png)

### VLAN 30 Connectivity Testing
Validated that VLAN 30 Finance could communicate with both VLAN 10 and VLAN 20
```
ping 192.168.10.10
ping 192.168.20.10
```
Result successful confirming routing functionality

Screenshot Placeholder VLAN 30 Test
![VLAN30 Ping](./screenshots/vlan30-ping.png)

### Troubleshooting Scenario
Encountered connectivity failure after adding VLAN 30 device

- Observed request timeouts after introducing VLAN 30 despite successful router subinterface configuration  
- Executed tracert to analyze packet path and confirmed traffic was not reaching the router  
- Verified trunk configuration was operational and carrying VLAN traffic  
- Isolated root cause to missing VLAN assignment on the switch access port connected to the new device  
- Assigned the switch port to VLAN 30 aligning Layer 2 segmentation with Layer 3 configuration  
- Restored full connectivity and validated successful communication across all VLANs

Screenshot Placeholder Failed Tracert
![Tracert Failure](./screenshots/tracert-fail.png)

Screenshot Placeholder Fixed Connectivity
![Tracert Success](./screenshots/tracert-success.png)

## Key Concepts Applied
ACL based traffic filtering to enforce network policy
Standard ACL configuration and interface application
Router on a Stick architecture for multi VLAN routing
VLAN segmentation and port assignment
Traffic flow validation using ping and tracert
Troubleshooting Layer 2 misconfiguration affecting Layer 3 communication
Understanding ACL processing order and interface direction

## Outcome
Implemented controlled inter VLAN communication enforcing security boundaries between departments while maintaining required access paths. Successfully integrated an additional VLAN with full routing capability and resolved misconfiguration through structured troubleshooting, reflecting real world network policy enforcement and operational problem solving.
