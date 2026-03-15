# Situation n°0 – Plan d'adressage, Schéma logique & physique, Table de routage, NAT

## Plan d'adressage – Agence Édimbourg

| Réseau | Adresse | Masque | Passerelle | Usage |
|---|---|---|---|---|
| Production | `192.168.5.0` | `/25` – 255.255.255.128 | `192.168.5.126` | VLAN 55 |
| Client | `192.168.5.128` | `/26` – 255.255.255.192 | `192.168.5.190` | VLAN 10 |
| Administration | `192.168.55.192` | `/28` – 255.255.255.240 | `192.168.55.254` | VLAN 20 |
| DMZ | `192.36.5.0` | `/24` – 255.255.255.0 | `192.36.5.254` | Serveurs publics |
| WAN | `192.36.253.0` | `/24` – 255.255.255.0 | `192.36.253.254` | Interconnexion |

## Table de routage

| Destination | Masque | Passerelle | Interface |
|---|---|---|---|
| `192.168.5.0` | `/25` | — | VLAN 55 (local) |
| `192.168.5.128` | `/26` | — | VLAN 10 (local) |
| `192.168.55.192` | `/28` | — | VLAN 20 (local) |
| `0.0.0.0` | `/0` | `192.168.55.254` | WAN (défaut) |

## NAT

| Source (LAN/DMZ) | Traduit en | Interface |
|---|---|---|
| `192.168.5.0/24` | `192.36.253.50` (IP WAN) | `out` |

---

*Documentation CUB Édimbourg – BTS SIO SISR*
