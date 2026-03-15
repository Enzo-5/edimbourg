# Situation 4 – Paramétrage et sécurisation du service DHCP

## Question 3 – Activation et paramétrage du service DHCP via PowerShell

Activer et paramétrer le service DHCP sur le serveur `ServeurPrimaire` en utilisant des commandes PowerShell pour créer la plage DHCP du sous-réseau « Clients » :

```powershell
# Installer le rôle DHCP
Install-WindowsFeature -Name DHCP -IncludeManagementTools

# Créer une étendue DHCP pour le sous-réseau Clients
Add-DhcpServerv4Scope -Name "Clients" -StartRange 192.168.5.130 -EndRange 192.168.5.189 -SubnetMask 255.255.255.192

# Définir la passerelle par défaut
Set-DhcpServerv4OptionValue -ScopeId 192.168.5.128 -OptionId 3 -Value 192.168.5.190

# Définir le serveur DNS
Set-DhcpServerv4OptionValue -ScopeId 192.168.5.128 -OptionId 6 -Value 192.168.5.10
```

---

## Question 5 – Procédure de mise en place du Failover DHCP

### Failover Actif/Actif

Dans un failover **Actif/Actif**, les deux serveurs DHCP traitent simultanément les requêtes clients. La plage d'adresses est répartie entre les deux serveurs selon un pourcentage défini (ex. 50%/50%).

**Étapes de configuration :**

1. Ouvrir la console **DHCP** sur `ServeurPrimaire`.
2. Faire un clic droit sur l'étendue → **Configurer le basculement**.
3. Choisir le serveur partenaire (`ServeurSecondaire`).
4. Sélectionner le mode **Équilibrage de charge**.
5. Définir le pourcentage de charge (50/50 par défaut).
6. Valider et vérifier la réplication.

### Failover Actif/Passif

Dans un failover **Actif/Passif**, un seul serveur traite les requêtes (le serveur actif). Le serveur passif prend le relais uniquement si le serveur actif tombe en panne.

**Étapes de configuration :**

1. Ouvrir la console **DHCP** sur `ServeurPrimaire`.
2. Faire un clic droit sur l'étendue → **Configurer le basculement**.
3. Choisir le serveur partenaire (`ServeurSecondaire`).
4. Sélectionner le mode **Serveur de secours**.
5. Définir le délai de reprise automatique.
6. Valider et tester le basculement.

---

## Question 6 – Mise en place du Failover Actif/Actif

Mise en place conformément à la procédure ci-dessus et au cahier des charges.

---

## Question 8 – Mise en place du Failover Actif/Passif

Mise en place après restauration des snapshots VM, conformément à la procédure et au cahier des charges.

---

## Question 10 – Note sur les vulnérabilités du service DHCP

### DHCP Starvation

Un attaquant envoie massivement des requêtes DHCP avec des adresses MAC falsifiées afin d'épuiser le pool d'adresses disponibles, provoquant un **déni de service** : les machines légitimes ne peuvent plus obtenir d'adresse IP.

### Rogue DHCP (DHCP Pirate)

Un serveur DHCP non autorisé distribue de fausses configurations réseau (passerelle, DNS) afin d'**intercepter ou rediriger le trafic** des clients.

### Contre-mesures

| Contre-mesure | Description |
|---|---|
| **DHCP Snooping** | N'autorise que les ports « trusted » à émettre des réponses DHCP sur les commutateurs |
| **Rate Limiting** | Limite le nombre de requêtes DHCP par port |
| **Port Security** | Restreint le nombre d'adresses MAC autorisées par port |
| **802.1X / NAC** | Empêche les hôtes non autorisés d'injecter du trafic DHCP |
| **Dynamic ARP Inspection** | Protection contre les manipulations d'adresses ARP |
| **IP Source Guard** | Vérifie la cohérence IP/MAC sur chaque port |
| **Segmentation VLAN** | Limite la propagation des attaques |
| **Supervision SIEM** | Détection d'une activité anormale dans les logs DHCP |

### Vulnérabilité CVE‑2021‑25217

La vulnérabilité **CVE‑2021‑25217** affecte le service ISC DHCP. Selon l'avis CERT‑FR (réf. CERTFR-2021-AVI-410), cette faille permet à un attaquant de provoquer un **déni de service à distance**.

- Systèmes affectés : versions antérieures à `4.1-ESV-R16-P1` ou `4.4.x` antérieures à `4.4.2-P1`
- **Mesure recommandée** : mise à jour vers une version corrigée du serveur ISC DHCP.

> En résumé, la combinaison du **DHCP Snooping**, de la **segmentation réseau** et d'un **suivi SIEM actif** constitue la défense la plus efficace contre les attaques de type Starvation et Rogue DHCP.

---

*Documentation CUB Édimbourg – BTS SIO SISR*
