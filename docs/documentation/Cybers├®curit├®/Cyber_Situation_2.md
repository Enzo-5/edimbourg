# Cyber Situation 2 – Premier paramétrage d'un pare-feu sur un site d'entreprise

## Question 1 – Installer les 3 VM et paramétrer leurs cartes réseaux

Déployer les trois machines virtuelles de la maquette :

- **Poste client** (LAN)
- **Pare-feu Stormshield** (LAN / DMZ / WAN)
- **Serveurs DMZ** (WEB, FTP)

Paramétrer correctement les interfaces réseau de chaque VM selon le schéma logique de l'agence.

---

## Question 2 – Renommer le pare-feu

Nommer le pare-feu selon la convention de l'entreprise CUB :

```
FW-Edimbourg
```

Configuration dans l'interface d'administration Stormshield : **Système** → **Configuration générale** → **Nom du firewall**.

---

## Question 3 – Stratégie de complexité des mots de passe (ANSSI)

Vérifier et appliquer la politique de mot de passe préconisée par l'ANSSI pour le compte administrateur :

- Longueur minimale : **12 caractères**
- Combinaison de majuscules, minuscules, chiffres et caractères spéciaux
- Pas de mot du dictionnaire
- Renouvellement régulier

---

## Question 4 – Synchronisation NTP

Configurer la date, l'heure et le fuseau horaire :

- Fuseau horaire : **Europe/Paris**
- Serveurs NTP : serveurs NTP de **Stormshield**

Configuration dans : **Système** → **Date et heure** → **NTP**.

---

## Question 5 – Pourquoi synchroniser tous les pare-feux sur les mêmes serveurs NTP ?

La synchronisation NTP est **primordiale** pour garantir la **corrélation des journaux (logs)** lors d'un incident de sécurité. Elle permet de :

- Reconstituer la **chronologie exacte** des actions sur l'ensemble du réseau (postes clients, serveurs, pare-feux)
- Analyser et résoudre efficacement une attaque en comparant les événements entre équipements
- Garantir la **valeur légale des journaux** en cas d'enquête

---

## Question 6 – Configurer les interfaces réseau du pare-feu

Paramétrer les interfaces selon le schéma réseau logique de l'agence :

| Interface | Zone | Adresse IP |
|---|---|---|
| `in` (eth0) | LAN | `192.168.55.254/28` |
| `dmz1` (eth1) | DMZ | `192.36.5.254/24` |
| `out` (eth2) | WAN | `192.36.253.50/24` |

---

## Question 7 – Appliquer la politique de filtrage Pass All (10)

Dans le menu **Filtrage et NAT**, appliquer la politique de filtrage **(10) Pass All** afin d'éviter tout blocage ou erreur lié à la configuration initiale de la maquette.

---

## Question 8 – Recette : tests de connectivité

Vérifier la connectivité entre le poste client et les serveurs présents en DMZ :

```bash
# Depuis le poste client, pinger le serveur WEB en DMZ
ping 192.36.5.20

# Tester l'accès HTTP
curl http://192.36.5.20
```

Les tests de connectivité confirment que le poste client peut bien atteindre les serveurs de la DMZ.

---

*Documentation CUB Édimbourg – BTS SIO SISR*
