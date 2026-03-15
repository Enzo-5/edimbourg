# Cyber Situation 3 – Routage et NAT

## Question 1 – Adresse IP passerelle WAN et création d'objet réseau

L'adresse IP du WAN servant de passerelle pour accéder à Internet est :

```
192.36.253.254 /24
```

Créer un objet réseau dans l'interface Stormshield représentant cette adresse :
**Objets réseau** → **Nouveau** → Type **Machine** → Nommer `GW_WAN` → IP `192.36.253.254`.

---

## Question 2 – Implémenter la table de routage

Utiliser l'objet `GW_WAN` pour configurer la table de routage du pare-feu :

| Destination | Masque | Passerelle | Interface |
|---|---|---|---|
| `0.0.0.0` | `0.0.0.0` | `192.36.253.254` | `out` (WAN) |

Configuration dans : **Réseau** → **Routage** → **Routes statiques**.

---

## Question 3 – NAT : permettre aux adresses privées de communiquer sur le WAN

La solution proposée est la mise en place d'une règle **NAT (Network Address Translation)** sur le pare-feu.

**Règle NAT Source (masquage) :**

| Source | Destination | Traduit en |
|---|---|---|
| Réseaux LAN (192.168.5.0/24) | Internet | IP WAN du pare-feu (192.36.253.50) |

Configuration dans : **Filtrage et NAT** → **NAT** → **Ajouter une règle**.

---

## Question 5 – Rendre accessibles les services WEB et FTP de la DMZ depuis le WAN

La solution proposée est la mise en place d'une règle de **NAT Destination (DNAT / Port Forwarding)** :

| Destination (IP WAN) | Port | Traduit vers (DMZ) | Port |
|---|---|---|---|
| `192.36.253.50` | 80/443 | `192.36.5.20` | 80/443 |
| `192.36.253.50` | 20-21 | `192.36.5.20` | 20-21 |

---

## Question 7 – Objets réseaux implicites et explicites

**Objets réseaux explicites** (créés manuellement) :

- Switch de niveau 3
- Pare-feu Stormshield

**Objets réseaux implicites** (générés automatiquement par le pare-feu) :

- Switch de niveau 2
- Switch de niveau 3
- Pare-feu Stormshield

---

*Documentation CUB Édimbourg – BTS SIO SISR*
