# Situation 5 – Paramétrage et sécurisation du service Active Directory

## A – Installation des services ADDS

### Question 2 – Installation du rôle Active Directory Domain Services

Depuis le **Gestionnaire de serveur** :

1. Cliquer sur **Gérer** → **Ajouter des rôles et fonctionnalités**
2. Sélectionner **Services de domaine Active Directory (AD DS)**
3. Suivre l'assistant d'installation
4. Une fois l'installation terminée, cliquer sur **Promouvoir ce serveur en contrôleur de domaine**

### Questions 3 & 4 – Promotion en contrôleur de domaine

1. Choisir **Ajouter une nouvelle forêt**
2. Renseigner le **nom de domaine racine** (ex : `edimbourg.cub.sioplc.fr`)
3. Définir le **niveau fonctionnel** de la forêt et du domaine
4. Définir le mot de passe de **restauration des services d'annuaire (DSRM)**
5. Valider et redémarrer le serveur

---

## B – Paramétrage de l'Active Directory

### Question 6 – Structure des Unités d'Organisation (UO)

Créer une structure d'UO adaptée au sein de la console **Utilisateurs et ordinateurs Active Directory** :

```
edimbourg.cub.sioplc.fr
├── UO_Utilisateurs
│   ├── Admins
│   └── Clients
├── UO_Serveurs
├── UO_Postes
└── UO_Tiers (pour les comptes d'administration)
```

### Création d'utilisateurs

```powershell
# Créer un utilisateur
New-ADUser -Name "David Balny" `
           -SamAccountName "dbalny" `
           -UserPrincipalName "dbalny@edimbourg.cub.sioplc.fr" `
           -Path "OU=Utilisateurs,DC=edimbourg,DC=cub,DC=sioplc,DC=fr" `
           -AccountPassword (ConvertTo-SecureString "MotDePasse@123" -AsPlainText -Force) `
           -Enabled $true
```

### Création de groupes

```powershell
# Créer un groupe de sécurité
New-ADGroup -Name "GRP_Admins" `
            -GroupScope Global `
            -GroupCategory Security `
            -Path "OU=Admins,OU=Utilisateurs,DC=edimbourg,DC=cub,DC=sioplc,DC=fr"

# Ajouter un membre au groupe
Add-ADGroupMember -Identity "GRP_Admins" -Members "dbalny"
```

---

*Documentation CUB Édimbourg – BTS SIO SISR*
