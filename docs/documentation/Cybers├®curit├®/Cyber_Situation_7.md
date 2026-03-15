# Cyber Situation 7 – Configuration d'un SIEM (Wazuh)

## Question 1 – Définition du SIEM et rôle dans un SOC

Le **SIEM (Security Information and Event Management)** est l'outil indispensable du SOC. Il transforme une masse illisible de données brutes en une **visibilité complète sur l'état de sécurité du Système d'Information**, permettant une réaction rapide face aux cyberattaques.

Ses fonctions principales :

- **Collecte** des journaux de tous les équipements (serveurs, pare-feux, commutateurs)
- **Corrélation** des événements pour détecter des attaques multi-étapes
- **Alertes** en temps réel sur les comportements suspects
- **Archivage** des logs pour la conformité et les enquêtes judiciaires

---

## Question 2 – Différence entre EDR et XDR

| Critère | EDR | XDR |
|---|---|---|
| **Périmètre** | Terminaux uniquement (postes, serveurs) | Terminaux + Réseau + Cloud + Email + Identités |
| **Détection** | Comportements suspects sur chaque machine | Attaques complexes et transversales |
| **Corrélation** | Locale (une machine) | Globale (l'ensemble du SI) |
| **Usage** | Protection endpoint classique | Réponse aux incidents avancés |

---

## Question 3 – Composants de la solution Wazuh

| Composant | Rôle |
|---|---|
| **Agent** | Capteur installé sur les machines pour récolter les logs et surveiller l'activité locale |
| **Serveur** | Moteur d'analyse qui traite les données et détecte les menaces via des règles |
| **Indexeur** | Centre de stockage permettant de rechercher et d'archiver les événements |
| **Dashboard** | Interface web pour visualiser les alertes et piloter la solution |

---

## A – Paramétrage du réseau SOC

### Question 4 – Création du VLAN SOC

Créer un nouveau VLAN dédié au SOC :

| Paramètre | Valeur |
|---|---|
| **VLAN** | VLAN SOC (ex: VLAN 51) |
| **Réseau** | `192.168.111.0/24` (agence 1) |
| **Connexion** | Raccordé directement au switch de niveau 3 |

Vérifier le routage de ce nouveau réseau vers tous les autres réseaux de l'agence et vers Internet.

### Question 5 – Filtrage du réseau SOC

Contraintes à respecter :

- Le SOC ne se connecte à Internet que via une **règle spécifique désactivable** une fois l'installation terminée
- La solution Wazuh peut accéder à **l'ensemble des VLAN et à la DMZ**
- Les terminaux ne répondent au SIEM que sur les **protocoles et ports utiles à Wazuh** (ports 1514/TCP pour les agents, 1515/TCP pour l'enrollment, 443/HTTPS pour le dashboard)

---

## B – Installation de la solution SIEM Wazuh

### Question 6 – Préparation de la VM SIEM

| Paramètre | Valeur |
|---|---|
| **OS** | Ubuntu Server (dernière LTS) |
| **vCPU** | 4 |
| **RAM** | 8 GiB |
| **Disque** | 50 GB |
| **VLAN** | VLAN Ferme de serveurs (ex: VLAN 52) |

Après l'installation d'Ubuntu Server, activer le service SSH :

```bash
sudo apt update && sudo apt install openssh-server -y
sudo systemctl enable ssh
sudo systemctl start ssh
```

---

## C – Installation des agents Wazuh

### Question 7 – Installation sur serveurs Linux (Debian/Ubuntu)

```bash
# Ajouter le dépôt Wazuh
curl -s https://packages.wazuh.com/key/GPG-KEY-WAZUH | apt-key add -
echo "deb https://packages.wazuh.com/4.x/apt/ stable main" | tee /etc/apt/sources.list.d/wazuh.list

# Installer l'agent
apt-get update && apt-get install wazuh-agent

# Configurer l'agent (adresse du serveur Wazuh)
sed -i 's/MANAGER_IP/192.168.111.X/' /var/ossec/etc/ossec.conf

# Démarrer l'agent
systemctl enable wazuh-agent
systemctl start wazuh-agent
```

### Installation sur serveurs Windows

Télécharger le package MSI depuis le serveur Wazuh ou le site officiel, puis installer en renseignant l'adresse IP du serveur Wazuh.

Vérifier la remontée des informations dans le **Dashboard Wazuh** : onglet **Agents**.

---

## D – Amélioration du Benchmark des serveurs

### Question 8 – Audit Benchmark (CIS Benchmark)

Dans le Dashboard Wazuh, accéder à :
**Security Configuration Assessment (SCA)** → Sélectionner un agent → Consulter le **score de conformité**.

Suivre les préconisations affichées pour chaque contrôle en échec afin d'améliorer le score, par exemple :

- Désactiver les services inutiles
- Appliquer les mises à jour de sécurité
- Renforcer la configuration SSH
- Activer les journaux d'audit

---

*Documentation CUB Édimbourg – BTS SIO SISR*
