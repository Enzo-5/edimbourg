# Bienvenue sur la documentation CUB Édimbourg

## 🌐 Agence Édimbourg

# Projet CUB – Infrastructure réseau

Documentation officielle de l'agence **Édimbourg**  
*BTS SIO option SISR – Bloc 2 et 3*

---

## 🏢 Présentation du projet

**CUB** est une entreprise fondée en 2010, spécialisée dans l'**incubation de startups** autour de valeurs fortes de **solidarité** et de **développement durable**.

L'entreprise dispose d'un **réseau international** d'agences situées à Paris (siège), Anvers, Barcelone, Hong-Kong, Los Angeles, Francfort… et **Édimbourg**.  
Chaque agence bénéficie d'une **adresse IPv4 publique dédiée** issue du préfixe `192.36.0.0/16`, attribué par le **RIPE NCC**, et gère son propre pare-feu local.  
CUB est reconnue comme un **LIR (Local Internet Registry)** et administre le domaine :  
➡️ `cub.sioplc.fr`

---

## 🎯 Objectif de l'agence Édimbourg

Déployer, sécuriser et documenter l'infrastructure réseau locale de l'agence selon la stratégie définie par la DSI de CUB, en suivant les recommandations de l'**ANSSI**.

### Objectifs techniques

- Concevoir une **segmentation réseau** (LAN, DMZ, VLAN d'administration)
- Établir un **plan d'adressage IPv4** clair et extensible
- Configurer les **commutateurs, pare-feux et serveurs locaux**
- Garantir la **sécurité et la haute disponibilité** du réseau
- Fournir une **documentation technique complète** sur GitHub Pages

---

## 🖥️ Informations réseau – Agence Édimbourg

| Élément | Adresse / VLAN | Description |
|---|---|---|
| **LAN Édimbourg** | `192.168.5.0/24` | Réseau interne |
| **DMZ Édimbourg** | `192.36.5.0/24` | Services accessibles depuis l'extérieur |
| **VLAN Administration** | `192.168.55.192/28` | Gestion et supervision |
| **VLAN Production** | `192.168.5.0/25` | Réseau de production |
| **VLAN Client** | `192.168.5.128/26` | Postes clients |
| **Pare-feu** | `192.168.55.254 / 192.36.5.254 / 192.36.253.50` | Sécurité et routage |
| **WAN** | `192.36.253.0/24` | Interconnexion inter-agences |

---

## 🗂️ Description des services

| Service | Description | VLAN | Hôtes |
|---|---|---|---|
| **Production** | Réseau principal dédié aux services métiers | VLAN 55 | 60 hôtes |
| **Client** | Réseau réservé aux postes clients internes | VLAN 10 | 16 hôtes |
| **Administration** | Réseau d'administration pour la gestion des équipements | VLAN 20 | 3 hôtes |

---

## 🧭 Structure de la documentation

- **Situations** : Déploiement et configuration des services (Windows Server, Git, DHCP, AD, GPO)
- **Cybersécurité** : Pare-feu Stormshield, cryptographie, bastion, filtrage, SIEM
- **DNS** : Serveurs récursifs, autorité, esclave, délégation de zone
- **Configurations** : Fichiers de configuration des équipements réseau (switch niveau 2 et 3)

---

*Projet réalisé par l'agence **Édimbourg** – BTS SIO SISR*
