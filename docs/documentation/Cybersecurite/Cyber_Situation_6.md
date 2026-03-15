# Cyber Situation 6 – Filtrage protocolaire

## Règles de filtrage définies par le responsable

| N° | Règle |
|---|---|
| 1 | Tous les sous-réseaux peuvent interroger le serveur Web (`192.36.X.20`) sur les ports HTTP, HTTPS et SSH |
| 2 | Les postes Administration peuvent accéder à n'importe quelle adresse IP avec tout protocole |
| 3 | Le sous-réseau Production peut accéder à un serveur NTP vers toutes destinations |
| 4 | Tous les sous-réseaux peuvent se connecter à la page d'administration du pare-feu Stormshield |
| 5 | Les deux serveurs récursifs peuvent interroger toutes les destinations sur le protocole DNS |
| 6 | Les différents sous-réseaux peuvent se connecter aux serveurs Web des autres agences |
| 7 | Toutes les agences peuvent interroger le serveur Web (`192.36.X.20`) sur les ports HTTP, HTTPS et FTP |
| 8 | Toutes les agences peuvent interroger les serveurs DNS d'autorité (`192.36.X.10` et `192.36.X.11`) |
| 9 | Tous les sous-réseaux du LAN doivent accéder à Internet |

---

## 1 – Table de filtrage initiale (Proposition papier)

**Règle par défaut (trafic établi) :**

| N° | Interface | Sens | IP Source | Port S | IP Destination | Port D | Protocole | Statut | Action |
|---|---|---|---|---|---|---|---|---|---|
| 0 | Toutes | * | * | * | * | * | * | Établie | Autoriser |

**Règles de filtrage :**

| N° | Interface | Sens | IP Source | Port S | IP Destination | Port D | Protocole | Statut | Action |
|---|---|---|---|---|---|---|---|---|---|
| 1 | 192.168.55.254 | Entrée | 192.168.5.0 / .128 / .192 | * | 192.36.5.20 | 80/443 | TCP | Établie | Autoriser |
| 1 | 192.168.55.254 | Entrée | 192.168.5.0 / .128 / .192 | * | 192.36.5.20 | 22 | TCP | Établie | Autoriser |
| 2 | 192.168.55.254 | Entrée | 192.168.5.192/28 | * | * | * | * | Nouvelle | Autoriser |
| 3 | 192.168.55.254 | Entrée | 192.168.5.0/25 | * | 192.36.0.0/16 | 123 | UDP | Nouvelle | Autoriser |
| 4 | 192.168.55.254 | Entrée | 192.168.5.0 / .128 / .192 | * | 192.168.55.254 | 443 | TCP | Nouvelle | Autoriser |
| 5 | 192.168.55.254 | Entrée | 192.168.5.10 / .11 | * | * | 53 | UDP | Nouvelle | Autoriser |
| 6 | 192.168.55.254 | Entrée | 192.168.5.0 / .128 / .192 | * | 192.36.0.0/16 | 80/443 | TCP | Nouvelle | Autoriser |
| 7 | 192.168.55.254 | Entrée | 192.36.0.0 | * | 192.36.0.20 | 80/443 | TCP | Nouvelle | Autoriser |
| 7 | 192.168.55.254 | Entrée | 192.36.0.0 | * | 192.36.5.20 | 20-21 | TCP | Nouvelle | Autoriser |
| 8 | 192.36.253.50 | Entrée | 192.36.0.0 | * | 192.36.5.10 / .11 | 53 | UDP | Nouvelle | Autoriser |
| 9 | 192.168.55.254 | Entrée | 192.168.5.0 / .128 / .192 | * | Internet | * | * | Nouvelle | Autoriser |
| FIN | Toutes | Sortie | * | * | * | * | * | * | **Bloquer** |

---

## 2 – Table de filtrage améliorée (Modèle positif + recommandations ANSSI)

La table améliorée tient compte du **modèle de sécurité positif** (tout ce qui n'est pas explicitement autorisé est interdit) et des recommandations ANSSI avec **5 sections distinctes** :

### Section 1 – Trafic établi (toutes interfaces)

| N° | Interface | Sens | IP Source | Port S | IP Destination | Port D | Protocole | Statut | Action |
|---|---|---|---|---|---|---|---|---|---|
| 1 | Toutes | * | * | * | * | * | * | Établie | Autoriser |

### Section 2 – Interface LAN (192.168.55.254)

| N° | Interface | Sens | IP Source | Port S | IP Destination | Port D | Protocole | Statut | Action |
|---|---|---|---|---|---|---|---|---|---|
| 1 | 192.168.55.254 | Entrée | 192.168.5.0/24 | * | 192.36.5.20 | 80/443 | TCP | Nouvelle | Autoriser |
| 2 | 192.168.55.254 | Entrée | 192.168.5.192/28 | * | 192.36.5.0/24 | * | * | Nouvelle | Autoriser |
| 3 | 192.168.55.254 | Entrée | 192.168.5.0/25 | * | * | 123 | UDP | Nouvelle | Autoriser |
| 4 | 192.168.55.254 | Entrée | 192.168.5.192/28 | * | 192.168.55.254 | 443 | TCP | Nouvelle | Autoriser |
| 4 | 192.168.55.254 | Entrée | * | * | 192.168.55.254 | * | * | Nouvelle | **Bloquer** |
| 5 | 192.168.55.254 | Entrée | 192.168.5.10 / .11 | * | * | 53 | UDP | Nouvelle | Autoriser |
| 6 | 192.168.55.254 | Entrée | 192.168.5.0/24 | * | 192.36.0.0/16 | 80/443 | TCP | Nouvelle | Autoriser |
| 9 | 192.168.55.254 | Entrée | 192.168.5.0/25 / .128/26 | * | Internet | 80, 443 | TCP | Nouvelle | Autoriser |
| 10 | 192.168.55.254 | Entrée | * | * | * | * | * | Nouvelle | **Bloquer** |

### Section 3 – Interface DMZ (192.36.5.254)

| N° | Interface | Sens | IP Source | Port S | IP Destination | Port D | Protocole | Statut | Action |
|---|---|---|---|---|---|---|---|---|---|
| 1 | 192.36.5.254 | Entrée | * | * | 192.36.5.254 | * | * | Nouvelle | **Bloquer** |
| 6 | 192.36.254.50 | Entrée | 192.36.5.0/24 | * | Internet | 80, 443 | TCP | Nouvelle | Autoriser |
| 7 | 192.36.254.50 | Entrée | 192.36.0.0/16 | * | 192.36.5.20/24 | 80, 443, 20, 21 | TCP | Nouvelle | Autoriser |

### Section 4 – Interface WAN (192.36.253.50)

| N° | Interface | Sens | IP Source | Port S | IP Destination | Port D | Protocole | Statut | Action |
|---|---|---|---|---|---|---|---|---|---|
| 4 | 192.36.253.50 | Entrée | * | * | 192.36.253.50 | * | * | Nouvelle | **Bloquer** |
| 8 | 192.36.253.50 | Entrée | Internet | * | 192.36.5.20/24 | 53 | UDP | Nouvelle | Autoriser |

### Section 5 – Règle de refus par défaut

| N° | Interface | Sens | IP Source | Port S | IP Destination | Port D | Protocole | Statut | Action |
|---|---|---|---|---|---|---|---|---|---|
| FIN | Toutes | Sortie | * | * | * | * | * | * | **Bloquer** |

---

*Documentation CUB Édimbourg – BTS SIO SISR*
