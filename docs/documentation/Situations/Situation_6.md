# Situation 6 – Stratégie de groupe (GPO) et PKI

## A – Authentification multifacteur et modèle de tiers

### Question 1 – Problématique de l'authentification par login/mot de passe pour un Administrateur de domaine

L'authentification par simple mot de passe est trop **vulnérable** pour un compte **Administrateur du domaine**. Ce secret unique est la cible principale des attaques (*keyloggers*, *brute force*), et sa compromission mène à la **prise de contrôle totale du Système d'Information**.

---

### Question 2 – Définition de l'authentification multifacteur (AMF) forte

L'AMF forte requiert la vérification de l'identité en utilisant **au moins deux facteurs** provenant de deux catégories différentes parmi les trois suivantes :

| Facteur | Exemples |
|---|---|
| **Ce que vous Savez** | Mot de passe, code PIN |
| **Ce que vous Possédez** | YubiKey, téléphone (code TOTP) |
| **Ce que vous Êtes** | Empreinte digitale, reconnaissance faciale |

---

### Question 3 – Fonctions du code PIN et du code PUK

- **PIN** : Code utilisé quotidiennement pour déverrouiller et utiliser les clés stockées sur le jeton matériel.
- **PUK** : Code de secours servant à débloquer le PIN si celui-ci a été saisi incorrectement trop de fois.

---

### Question 4 – Pourquoi modifier PIN et PUK avant la mise en œuvre

Il est impératif de changer les codes PIN et PUK par défaut car ils sont **publics**. Les conserver rendrait la YubiKey inutile : un attaquant qui la possède connaîtrait déjà le facteur « Ce que vous savez », annulant le principe de l'authentification multifacteur.

---

### Question 5 – TOTP vs Certificat X.509 : robustesse comparée

L'authentification par **certificat X.509** est plus robuste car elle repose sur la **cryptographie asymétrique** : la clé privée de signature ne quitte jamais le jeton, la rendant immunisée contre les attaques par *phishing*.

Le **TOTP**, en revanche, utilise un **secret partagé** qui, s'il est volé sur le serveur, compromet tous les utilisateurs. Il est également vulnérable aux attaques de l'**Homme du Milieu (MitM)**.

---

### Question 6 – Modèle de Tiers : 4 comptes pour un même collaborateur

Le RSSI a segmenté les comptes de David Balny pour appliquer le **principe de moindre privilège** et le **Modèle de Tiers** :

| Compte | Tiers | Usage |
|---|---|---|
| Utilisateur standard | Tiers 2 | Tâches courantes (bureautique, e-mail) |
| Admin Tiers 2 | Tiers 2 | Administration postes clients |
| Admin Tiers 1 | Tiers 1 | Administration serveurs membres |
| Admin Tiers 0 | Tiers 0 | Administration contrôleurs de domaine |

L'objectif est de limiter la surface d'attaque : la compromission d'un compte Tiers 2 ne peut jamais mettre en danger l'infrastructure critique (Tiers 0).

---

### Question 7 – Poste d'Administration (PAW)

Le **PAW (Privileged Access Workstation)** est un ordinateur isolé et hautement sécurisé, strictement dédié aux comptes à privilèges élevés (Tiers 0 et Tiers 1). Il est placé dans l'UO `/Tiers` pour recevoir les **GPO les plus strictes**, le rendant « durci » contre les menaces. Aucune tâche courante (navigation web, e-mail) ne peut y être réalisée.

---

## B – Mise en place d'une autorité de certification interne (PKI)

### Question 8 – Fonction d'une PKI

La **PKI (Infrastructure à Clé Publique)** établit et gère la **chaîne de confiance** pour la cryptographie asymétrique. Son cœur, l'Autorité de Certification (CA), vérifie l'identité des entités et leur délivre un **certificat numérique X.509**.

La PKI assure :

- La **confidentialité** (chiffrement SSL/TLS)
- L'**intégrité** (signature numérique)
- L'**authentification forte** (certificats)

---

### Question 9 – PKI interne vs certificats auto-signés

| Critère | PKI interne | Certificat auto-signé |
|---|---|---|
| Confiance | Centralisée, déployée via GPO | Manuelle, machine par machine |
| Alertes navigateur | Aucune (CA racine déployée) | Alerte à chaque connexion |
| Révocation | CRL automatique | Impossible à gérer efficacement |
| Renouvellement | Automatisé | Manuel |
| Coût | Gratuit (Windows CA) | Gratuit |

---

### Question 10 – RSA ou ECDSA pour la PKI ?

L'algorithme **ECDSA** (Elliptic Curve Digital Signature Algorithm) est préférable à RSA car :

- Sécurité équivalente avec des clés **beaucoup plus petites** (256 bits vs 3072 bits pour RSA)
- **Vitesse de traitement** et de signature nettement supérieure
- Certificats plus légers, réduisant la charge réseau

---

### Question 11 – SHA-1 ou SHA-256 ?

Il est impératif de choisir **SHA-256**. L'ancienne fonction SHA-1 est considérée comme **cryptographiquement brisée** en raison de sa vulnérabilité aux attaques par collision, ce qui permettrait à un attaquant de créer un faux certificat avec la même empreinte.

---

## C – Paramétrage de la PKI pour l'authentification par certificats

Installation et configuration de l'Autorité de Certification interne via le **Gestionnaire de serveur** → **Services de certificats Active Directory (AD CS)**.

---

## D – Mise en place des stratégies de groupe (GPO)

Stratégies configurées :

- Autoriser l'usage de **certificats ECC** pour l'ordinateur d'Administration
- Forcer le **verrouillage de session** si la clé matérielle est retirée
- Autoriser **l'auto-inscription** pour le compte `dbalnyadmt0`
- Restreindre le compte `sanchezadmt0` à l'**ordinateur d'Administration uniquement**
- Forcer l'**authentification SmartCard/PIV** pour `dbalnyadmt0` (interdire l'authentification par mot de passe)
- Interdire la connexion de `sanchezadmt0` sur les postes clients standards
- Interdire la connexion d'utilisateurs non-admin du domaine sur le poste d'administration

---

## Extension PowerShell – Gestion des GPO

### Commandes essentielles

```powershell
# Lister toutes les GPO du domaine
Get-GPO -All

# Créer une nouvelle GPO
New-GPO -Name "GPO_AdminPAW"

# Lier une GPO à une UO
New-GPLink -Name "GPO_AdminPAW" -Target "OU=Tiers,DC=edimbourg,DC=cub,DC=sioplc,DC=fr"

# Forcer l'application des GPO sur un poste
Invoke-GPUpdate -Force

# Afficher le rapport RSoP (résultat des stratégies) pour un utilisateur
Get-GPResultantSetOfPolicy -ReportType Html -Path "C:\rapport_rsop.html"

# Sauvegarder une GPO
Backup-GPO -Name "GPO_AdminPAW" -Path "C:\Sauvegardes_GPO"

# Restaurer une GPO
Restore-GPO -Name "GPO_AdminPAW" -Path "C:\Sauvegardes_GPO"
```

---

*Documentation CUB Édimbourg – BTS SIO SISR*
