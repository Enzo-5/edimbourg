# Configuration Switch Niveau 3

## Présentation

Le switch de niveau 3 assure le **routage inter-VLAN**. Il dispose d'interfaces virtuelles (SVI) pour chaque VLAN et d'une route par défaut vers le pare-feu.

| Paramètre | Valeur |
|---|---|
| **Domaine** | `btssio.lan` |
| **Spanning-Tree** | Rapid-PVST |
| **Routage IP** | Activé (`ip routing`) |
| **Accès distant** | SSH |
| **Route par défaut** | `0.0.0.0/0` → `192.168.55.254` |

---

## Adressage des interfaces VLAN (SVI)

| Interface VLAN | Adresse IP | Masque | Usage |
|---|---|---|---|
| **VLAN 10** | `192.168.5.190` | `/26` – 255.255.255.192 | Clients |
| **VLAN 15** | `192.168.55.253` | `/28` – 255.255.255.240 | Administration |
| **VLAN 20** | `192.168.5.206` | `/28` – 255.255.255.240 | Serveurs |
| **VLAN 55** | `192.168.5.126` | `/25` – 255.255.255.128 | Production |

---

## Attribution des ports

| Interface | VLAN | Mode |
|---|---|---|
| Gi1/0/11 – Gi1/0/18 | VLAN 55 | Access |
| Gi1/0/23 | VLAN 10, 20 | Trunk (allowed) |
| Gi1/0/24 | VLAN 15 | Access |

---

## Configuration complète

```cisco
ip routing
!
ip domain name btssio.lan
!
spanning-tree mode rapid-pvst
spanning-tree extend system-id
!
username etudiant privilege 15 secret 9 $9$23nez79hzlOFf.$Kft3nPAG0J/LPAZ85s3112zhfx6fbQItre2ax8ldAS6
!
interface GigabitEthernet1/0/11
 switchport access vlan 55
 switchport mode access
!
interface GigabitEthernet1/0/12
 switchport access vlan 55
 switchport mode access
!
interface GigabitEthernet1/0/13
 switchport access vlan 55
 switchport mode access
!
interface GigabitEthernet1/0/14
 switchport access vlan 55
 switchport mode access
!
interface GigabitEthernet1/0/15
 switchport access vlan 55
 switchport mode access
!
interface GigabitEthernet1/0/16
 switchport access vlan 55
 switchport mode access
!
interface GigabitEthernet1/0/17
 switchport access vlan 55
 switchport mode access
!
interface GigabitEthernet1/0/18
 switchport access vlan 55
 switchport mode access
!
interface GigabitEthernet1/0/23
 switchport trunk allowed vlan 10,20
 switchport mode trunk
!
interface GigabitEthernet1/0/24
 switchport access vlan 15
 switchport mode access
!
interface Vlan1
 no ip address
!
interface Vlan10
 ip address 192.168.5.190 255.255.255.192
!
interface Vlan15
 ip address 192.168.55.253 255.255.255.240
!
interface Vlan20
 ip address 192.168.5.206 255.255.255.240
!
interface Vlan55
 ip address 192.168.5.126 255.255.255.128
!
ip route 0.0.0.0 0.0.0.0 192.168.55.254
!
ip http server
ip http authentication local
ip http secure-server
!
line con 0
 stopbits 1
line vty 0 4
 login local
 transport input ssh
line vty 5 15
 login
 transport input ssh
!
end
```

---

*Documentation CUB Édimbourg – BTS SIO SISR*
