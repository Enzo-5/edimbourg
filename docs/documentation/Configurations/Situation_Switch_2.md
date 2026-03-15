# Configuration Switch Niveau 2

## Présentation

Le switch de niveau 2 assure la **segmentation du réseau** en plusieurs VLANs. Il opère en couche 2 du modèle OSI et relaie le trafic vers le switch de niveau 3 via des liens trunk.

| Paramètre | Valeur |
|---|---|
| **Hostname** | Switch |
| **Version IOS** | 15.0 |
| **Domaine** | `local.epoka5.lan` |
| **Spanning-Tree** | PVST |
| **Accès distant** | SSH v1 |

---

## Attribution des VLANs

| Interfaces | VLAN | Mode |
|---|---|---|
| Fa0/1 – Fa0/10 | VLAN 10 | Access |
| Fa0/19 – Fa0/24 | VLAN 20 | Access |
| Fa0/47 – Fa0/48 | — | Trunk |

---

## Configuration complète

```cisco
version 15.0
no service pad
service timestamps debug datetime msec
service timestamps log datetime msec
no service password-encryption
!
hostname Switch
!
ip domain-name local.epoka5.lan
cluster enable etudiant 0
!
spanning-tree mode pvst
spanning-tree extend system-id
!
vlan internal allocation policy ascending
!
ip ssh version 1
!
interface FastEthernet0/1
 switchport access vlan 10
 switchport mode access
!
interface FastEthernet0/2
 switchport access vlan 10
 switchport mode access
!
interface FastEthernet0/3
 switchport access vlan 10
 switchport mode access
!
interface FastEthernet0/4
 switchport access vlan 10
 switchport mode access
!
interface FastEthernet0/5
 switchport access vlan 10
 switchport mode access
!
interface FastEthernet0/6
 switchport access vlan 10
 switchport mode access
!
interface FastEthernet0/7
 switchport access vlan 10
 switchport mode access
!
interface FastEthernet0/8
 switchport access vlan 10
 switchport mode access
!
interface FastEthernet0/9
 switchport access vlan 10
 switchport mode access
!
interface FastEthernet0/10
 switchport access vlan 10
 switchport mode access
!
interface FastEthernet0/19
 switchport access vlan 20
 switchport mode access
!
interface FastEthernet0/20
 switchport access vlan 20
 switchport mode access
!
interface FastEthernet0/21
 switchport access vlan 20
 switchport mode access
!
interface FastEthernet0/22
 switchport access vlan 20
 switchport mode access
!
interface FastEthernet0/23
 switchport access vlan 20
 switchport mode access
!
interface FastEthernet0/24
 switchport access vlan 20
 switchport mode access
!
interface FastEthernet0/47
 switchport mode trunk
!
interface FastEthernet0/48
 switchport mode trunk
!
interface Vlan1
 no ip address
 no ip route-cache
!
interface Vlan10
 no ip address
 no ip route-cache
!
ip http server
ip http secure-server
!
line con 0
line vty 0 4
 login local
 transport input ssh
line vty 5 15
 login
!
end
```

---

*Documentation CUB Édimbourg – BTS SIO SISR*
