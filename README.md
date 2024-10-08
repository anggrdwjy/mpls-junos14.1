# How To Configuration Juniper vMX Junos 14.1

Full Demo Juniper vMX Junos 14.1
---------------
- [Juniper vMX Junos 14.1 Routing OSPF, MPLS LDP, BGP and MPLS Service](https://youtube.com/playlist?list=PLy064HwEq9IyTJ9UQzxQ3GHHSa_S7d9DG&si=nKrMis55pcmpjilX)

Basic Configuration :
---------------
Basic Configuration Juniper
```
#Configuration Hostname
set system host-name [DEVICE_NAME]

#Set Timezone
set system time-zone [LOCATION]

#Password Root
set system root-authentication password => [PASSWORD]

#Configuration Username and Password
set system login user [USERNAME] class super-user
set system login user [USERNAME] authentication password => [PASSWORD]
set system login user [USERNAME] class operator
set system login user [USERNAME] authentication password => [PASSWORD]

#Privilage Login
set system services telnet
```

Interface Configuration :
---------------
Configuration Loopback
```
set interfaces lo0 unit 0 description [DESCRIPTION]
set interfaces lo0 unit 0 family inet address [IP_ADDRESS_NETMASK]
```
Configuration Port Interface
```
set interfaces ge-0/0/0 mtu [1900 - 9200]
set interfaces ge-0/0/0 unit 0 description [DESCRIPTION]
set interfaces ge-0/0/0 unit 0 family inet address [IP_ADDRESS_NETMASK]
set interfaces ge-0/0/0 unit 0 family mpls
```
Verification
```
run show interface terse | grep up
run show interface terse | grep down
run show interface brief
```

Routing OSPF :
---------------
Configuration Routing OSPF Single Area or Backbone Area
```
set protocols ospf area [ID_AREA] interface [PORT] interface-type p2p
set protocols ospf area [ID_AREA] interface [PORT] metric [1-65000]  <- MTU
set protocols ospf area [ID_AREA] interface [PORT] hello-interval 5
set protocols ospf area [ID_AREA] interface [PORT] dead-interval 15
set protocols ospf area [ID_AREA] interface [PORT] authentication md5 1 key [PASSWORD]
set protocols ospf area [ID_AREA] interface [LOOPBACK] interface-type p2p
set protocols ospf area [ID_AREA] interface [LOOPBACK] metric [1-65000]  <- MTU
```
Configuration Routing OSPF Multiarea
```
set protocols ospf area [ID_AREA_A] interface [PORT] interface-type p2p
set protocols ospf area [ID_AREA_A] interface [PORT] metric [1-65000]  <- MTU
set protocols ospf area [ID_AREA_A] interface [PORT] hello-interval 5
set protocols ospf area [ID_AREA_A] interface [PORT] dead-interval 15
set protocols ospf area [ID_AREA_A] interface [PORT] authentication md5 1 key [PASSWORD]
set protocols ospf area [ID_AREA_A] interface [LOOPBACK] interface-type p2p
set protocols ospf area [ID_AREA_A] interface [LOOPBACK] metric [1-65000]  <- MTU
set protocols ospf area [ID_AREA_B] interface [PORT] interface-type p2p
set protocols ospf area [ID_AREA_B] interface [PORT] metric [1-65000]  <- MTU
set protocols ospf area [ID_AREA_B] interface [PORT] hello-interval 5
set protocols ospf area [ID_AREA_B] interface [PORT] dead-interval 15
set protocols ospf area [ID_AREA_B] interface [PORT] authentication md5 1 key [PASSWORD]
```
Verification
```
run show ospf interface
run show ospf neighbor
```

MPLS LDP :
---------------
Configuration MPLS LDP :
```
set protocols mpls interface [PORT_INTERFACE]
set protocols mpls interface [LOOPBACK]
set protocols ldp interface [PORT_INTERFACE]
set protocols ldp interface [LOOPBACK]
set protocols ldp session [IP_NEIGHBOR_1] authentication-key [PASSWORD]
set protocols ldp session [IP_NEIGHBOR_2] authentication-key [PASSWORD]
set protocols ldp session [IP_NEIGHBOR_3] authentication-key [PASSWORD]
```
Verification
```
run show mpls interface brief
run show ldp interface
run show ldp neighbor
run show ldp session
```

LLDP and RSVP :
---------------
```
set protocols lldp interface [PORT_INTERFACE]
set protocols lldp interface [PORT_INTERFACE]
set protocols rsvp interface [PORT_INTERFACE]
set protocols rsvp interface [PORT_INTERFACE]
set protocols rsvp interface [LOOPBACK]
```

BGP Configuration :
---------------
Routing BGP to Route Reflector
```
set routing-options graceful-restart
set routing-options router-id [IP_LOOPBACK]
set routing-options autonomous-system [AS-NUMBER]
set protocols bgp log-updown
set protocols bgp group [BGP_GROUP] type internal
set protocols bgp group [BGP_GROUP] local-address [IP_LOOPBACK]
set protocols bgp group [BGP_GROUP] family inet-vpn unicast  <- VPNV4
set protocols bgp group [BGP_GROUP] authentication-key [PASSWORD]
set protocols bgp group [BGP_GROUP] cluster [CLUSTER_ID]
set protocols bgp group [BGP_GROUP] peer-as [AS_NUMBER_PEERING]
set protocols bgp group [BGP_GROUP] neighbor [IP_ROUTE_REFLECTOR] description [DESCRIPTION]
```
Routing BGP to Route Reflector Client
```
set routing-options graceful-restart
set routing-options router-id [IP_LOOPBACK]
set routing-options autonomous-system [AS-NUMBER]
set protocols bgp log-updown
set protocols bgp group [BGP_GROUP_RR_CLIENT] type internal
set protocols bgp group [BGP_GROUP_RR_CLIENT] local-address [IP_LOOPBACK]
set protocols bgp group [BGP_GROUP_RR_CLIENT] family inet-vpn unicast  <- VPNV4
set protocols bgp group [BGP_GROUP_RR_CLIENT] authentication-key [PASSWORD]
set protocols bgp group [BGP_GROUP_RR_CLIENT] cluster [CLUSTER_ID]
set protocols bgp group [BGP_GROUP_RR_CLIENT] peer-as [AS_NUMBER_PEERING]
set protocols bgp group [BGP_GROUP_RR_CLIENT] neighbor [IP_RR_CLIENT_A] description [DESCRIPTION]
set protocols bgp group [BGP_GROUP_RR_CLIENT] neighbor [IP_RR_CLIENT_B] description [DESCRIPTION]
set protocols bgp group [BGP_GROUP_RR_CLIENT] neighbor [IP_RR_CLIENT_C] description [DESCRIPTION]
set protocols bgp group [BGP_GROUP_RR_CLIENT] neighbor [IP_RR_CLIENT_D] description [DESCRIPTION]
```
Verification
```
run show bgp summary
```

MPLS Service :
---------------
MPLS L2VPN
```
Example :
@NODE-A
set interfaces ge-0/0/1 flexible-vlan-tagging
set interfaces ge-0/0/1 mtu 1900
set interfaces ge-0/0/1 encapsulation flexible-ethernet-services
set interfaces ge-0/0/1 unit 10 description L2VPN
set interfaces ge-0/0/1 unit 10 encapsulation vlan-ccc
set interfaces ge-0/0/1 unit 10 vlan-id 10
set protocols l2circuit neighbor 21.21.21.21 interface ge-0/0/1.10 virtual-circuit-id 10
set protocols l2circuit neighbor 21.21.21.21 interface ge-0/0/1.10 description L2VPN
set protocols l2circuit neighbor 21.21.21.21 interface ge-0/0/1.10 no-control-word
set protocols l2circuit neighbor 21.21.21.21 interface ge-0/0/1.10 ignore-encapsulation-mismatch

@NODE-B
set interfaces ge-0/0/1 flexible-vlan-tagging
set interfaces ge-0/0/1 mtu 1900
set interfaces ge-0/0/1 encapsulation flexible-ethernet-services
set interfaces ge-0/0/1 unit 10 description L2VPN
set interfaces ge-0/0/1 unit 10 encapsulation vlan-ccc
set interfaces ge-0/0/1 unit 10 vlan-id 10
set protocols l2circuit neighbor 11.11.11.11 interface ge-0/0/1.10 virtual-circuit-id 10
set protocols l2circuit neighbor 11.11.11.11 interface ge-0/0/1.10 description L2VPN
set protocols l2circuit neighbor 11.11.11.11 interface ge-0/0/1.10 no-control-word
set protocols l2circuit neighbor 11.11.11.11 interface ge-0/0/1.10 ignore-encapsulation-mismatch

#Verification
run show l2circuit connections up | grep ge-0/0/1.10
```

MPLS L3VPN
```
Example :
@NODE-A
[Interface Configuration Trunk Node 21-CE]
set interfaces ge-0/0/1 flexible-vlan-tagging
set interfaces ge-0/0/1 mtu 1900
set interfaces ge-0/0/1 encapsulation flexible-ethernet-services
set interfaces ge-0/0/1 unit 20 vlan-id 20
set interfaces ge-0/0/1 unit 20 family inet address 202.100.0.1/30
set policy-options policy-statement VPN-AB-EXP term 1 then community add target:65006:20
set policy-options policy-statement VPN-AB-EXP term 1 then accept
set policy-options policy-statement VPN-AB-IMP term 1 from community target:65006:20
set policy-options policy-statement VPN-AB-IMP term 1 then accept
set policy-options policy-statement VPN-AB-IMP term other then reject
set policy-options community target:65006:20 members target:65006:20
set routing-instances VPN-AB instance-type vrf
set routing-instances VPN-AB interface ge-0/0/1.20
set routing-instances VPN-AB route-distinguisher 65006:20
set routing-instances VPN-AB vrf-import VPN-AB-IMP
set routing-instances VPN-AB vrf-export VPN-AB-EXP
set routing-instances VPN-AB vrf-table-label

@NODE-B
set interfaces ge-0/0/1 flexible-vlan-tagging
set interfaces ge-0/0/1 mtu 1900
set interfaces ge-0/0/1 encapsulation flexible-ethernet-services
set interfaces ge-0/0/1 unit 20 vlan-id 20
set interfaces ge-0/0/1 unit 20 family inet address 202.100.20.1/30
set policy-options policy-statement VPN-AB-EXP term 1 then community add target:65006:20
set policy-options policy-statement VPN-AB-EXP term 1 then accept
set policy-options policy-statement VPN-AB-IMP term 1 from community target:65006:20
set policy-options policy-statement VPN-AB-IMP term 1 then accept
set policy-options policy-statement VPN-AB-IMP term other then reject
set policy-options community target:65006:20 members target:65006:20
set routing-instances VPN-AB instance-type vrf
set routing-instances VPN-AB interface ge-0/0/1.20
set routing-instances VPN-AB route-distinguisher 65006:20
set routing-instances VPN-AB vrf-import VPN-AB-IMP
set routing-instances VPN-AB vrf-export VPN-AB-EXP
set routing-instances VPN-AB vrf-table-label

#Verification
run show route table VPN-AB.inet.0
run show arp vpn VPN-AB
run ping 202.100.20.2 routing-instance VPN-AB
```











