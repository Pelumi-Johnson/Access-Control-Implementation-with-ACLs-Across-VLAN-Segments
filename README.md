# Access Control Implementation with ACLs Across VLAN Segments

## Overview
Designed and implemented network access control using standard ACLs to regulate communication between VLAN segments. Enforced traffic restrictions between VLAN 10 HR and VLAN 20 IT, followed by the introduction of VLAN 30 Finance to enable controlled interaction across both networks.

## Objective
Configured and applied access control policies to restrict unauthorized inter VLAN communication while maintaining required connectivity for designated network segments and extending the network with an additional VLAN after initial validation.

## Network Setup
Devices Used
1 Cisco 2960 Switch
1 Cisco 1941 Router
3 End Devices PCs

Topology Design

PC0 assigned to VLAN 10 HR

PC1 assigned to VLAN 20 IT

PC2 assigned to VLAN 30 Finance added after initial configuration and testing

Switch connected to router via trunk link FastEthernet0/24 to GigabitEthernet0/0
Router configured with subinterfaces for VLAN routing

![Network Topology](https://github.com/Pelumi-Johnson/Access-Control-Implementation-with-ACLs-Across-VLAN-Segments/blob/main/Screenshot%202026-04-07%20154157.png)

## Configuration

### VLAN Configuration and Assignment
- Configured VLAN 10 HR and VLAN 20 IT and assigned switch ports accordingly
- Validated inter VLAN routing and ACL behavior
- Added VLAN 30 Finance after initial testing and assigned corresponding switch port

### Trunk Configuration
Configured uplink port to carry VLAN traffic between switch and router
```
enable
configure terminal
interface fastEthernet 0/24
switchport mode trunk
exit
```
![Trunk Verification](https://github.com/Pelumi-Johnson/Access-Control-Implementation-with-ACLs-Across-VLAN-Segments/blob/main/Screenshot%202026-04-05%20130703.png)

### Router Subinterfaces
Configured Router on a Stick for VLAN 10 and VLAN 20 initially
Extended configuration by adding VLAN 30 subinterface after validating ACL functionality
```
enable
configure terminal

interface gigabitEthernet 0/0.10
encapsulation dot1Q 10
ip address 192.168.10.1 255.255.255.0

interface gigabitEthernet 0/0.20
encapsulation dot1Q 20
ip address 192.168.20.1 255.255.255.0

interface gigabitEthernet 0/0
no shutdown
```
! Added later after validation and ACL testing
interface gigabitEthernet 0/0.30
encapsulation dot1Q 30
ip address 192.168.30.1 255.255.255.0

![Router Config](https://github.com/Pelumi-Johnson/Access-Control-Implementation-with-ACLs-Across-VLAN-Segments/blob/main/Screenshot%202026-04-05%20131345.png)

### Access Control Configuration
Implemented ACL to restrict HR from accessing IT while allowing other traffic
```
enable
configure terminal
access-list 10 deny 192.168.10.0 0.0.0.255
access-list 10 permit any
```
interface gigabitEthernet 0/0.20
ip access-group 10 in

![ACL Config](https://github.com/Pelumi-Johnson/Access-Control-Implementation-with-ACLs-Across-VLAN-Segments/blob/main/Screenshot%202026-04-05%20131923.png)

## Validation

### Access Control Testing
From VLAN 10 HR
ping 192.168.20.10
Destination host unreachable confirming traffic restriction from HR to IT

From VLAN 20 IT
ping 192.168.10.10
Result failed due to ACL behavior where HR could receive traffic but could not respond back to IT

Observed Behavior
HR was restricted from initiating traffic to IT and also unable to complete return communication
Resulting in failed bidirectional communication despite IT being permitted

ACL Test Failure
![ACL Block](https://github.com/Pelumi-Johnson/Access-Control-Implementation-with-ACLs-Across-VLAN-Segments/blob/main/Screenshot%202026-04-05%20131708.png)

ACL Test Success
![ACL Allow](https://github.com/Pelumi-Johnson/Access-Control-Implementation-with-ACLs-Across-VLAN-Segments/blob/main/Screenshot%202026-04-05%20131822.png)

### ACL Policy Refinement
Updated ACL to specifically deny traffic from VLAN 10 HR to VLAN 20 IT while allowing all other communication
```
enable
configure terminal
access-list 100 deny ip 192.168.10.0 0.0.0.255 192.168.20.0 0.0.0.255
access-list 100 permit ip any any
```
Applied ACL to appropriate router subinterface to enforce directional control

Result

HR remained restricted from communicating with IT

Other VLAN communications were preserved

### VLAN 30 Controlled Communication
Introduced VLAN 30 Finance after ACL validation to enable controlled communication across both VLAN 10 and VLAN 20

Configured VLAN 30 with dedicated subnet and router subinterface
Validated that Finance could initiate and receive communication with both HR and IT

ping 192.168.10.10
ping 192.168.20.10

Result successful confirming full connectivity for VLAN 30

Confirmed Behavior
HR cannot initiate communication to IT

Finance can communicate with both HR and IT

HR can respond to Finance traffic due to ACL permitting non restricted traffic

Screenshot Placeholder VLAN 30 Test
![VLAN30 Ping](./screenshots/vlan30-ping.png)

### Troubleshooting Scenario
Encountered connectivity failure after adding VLAN 30 device

- Observed request timeouts despite correct router subinterface configuration
- Executed tracert to analyze packet path and confirmed traffic was not reaching router subinterface
- Verified trunk configuration was active and passing VLAN traffic
- Identified missing VLAN assignment on switch access port connected to PC2
- Assigned switch port to VLAN 30 aligning Layer 2 configuration with Layer 3 routing
- Restored connectivity and validated successful communication across all VLANs

Screenshot Placeholder Failed Tracert
![Tracert Failure](./screenshots/tracert-fail.png)

Screenshot Placeholder Fixed Connectivity
![Tracert Success](./screenshots/tracert-success.png)

## Key Concepts Applied
- Extended ACL configuration for precise traffic filtering between specific source and destination networks
- Directional traffic control using inbound ACL application
- Router on a Stick architecture for multi VLAN routing
- Incremental network expansion with policy validation before scaling
- VLAN segmentation and port assignment alignment
- Traffic flow validation using ping and tracert
- Layer 2 misconfiguration impact on Layer 3 communication
- ACL processing order and rule specificity

## Outcome
Implemented granular inter VLAN access control preventing HR from communicating with IT while maintaining controlled communication paths across the network. Refined ACL policy to target specific traffic flows and introduced VLAN 30 to enable structured communication across both segments without violating security constraints. Resolved misconfigurations through systematic troubleshooting reflecting real world network enforcement and operational precision.
Screenshot Placeholder Failed Tracert
![Tracert Failure](./screenshots/tracert-fail.png)

Screenshot Placeholder Fixed Connectivity
![Tracert Success](./screenshots/tracert-success.png)

## Key Concepts Applied
- ACL based traffic filtering to enforce network policy
- Standard ACL configuration and interface application
- Router on a Stick architecture for multi VLAN routing
- Incremental network expansion after validation
- VLAN segmentation and port assignment alignment
- Traffic flow validation using ping and tracert
- Layer 2 misconfiguration impact on Layer 3 communication
- ACL processing order and interface direction

## Outcome
Implemented controlled inter VLAN communication enforcing security boundaries between departments while maintaining required access paths. Extended the network by introducing VLAN 30 after initial validation, enabling structured multi network interaction while preserving ACL enforced restrictions. Resolved configuration issues through systematic troubleshooting aligned with real world network operations.
