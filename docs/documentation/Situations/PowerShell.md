# PowerShell – Introduction et commandes fondamentales

## Question 1 – Vérifier la version de PowerShell (via SSH)

```powershell
$PSVersionTable
```

---

## Question 2 – Visualiser que PowerShell est bien exécuté

Le terminal affiche l'invite `PS C:\>` indiquant que PowerShell est actif.

---

## Question 3 – Afficher l'ensemble des alias

```powershell
Get-Alias
```

---

## Question 4 – Afficher l'aide pour `Clear-Host`

```powershell
Get-Help Clear-Host
```

---

## Question 5 – Les trois types de commandes dans PowerShell

| Type | Description |
|---|---|
| **Cmdlets** (Applets de commande) | Commandes natives PowerShell (ex: `Get-Process`) |
| **Fonctions** | Blocs de code réutilisables définis par l'utilisateur |
| **Alias** | Raccourcis vers d'autres commandes (ex: `ls` = `Get-ChildItem`) |

---

## Question 6 – Commande `Get-History`

```powershell
Get-History
```

Affiche l'historique de toutes les commandes précédemment saisies dans la session. Utile pour retrouver et rejouer des commandes sans les retaper.

---

## Question 7 – Lister les processus en cours d'exécution

```powershell
Get-Process
```

Cette information est utile en administration système pour détecter des processus anormaux, surveiller la consommation de ressources ou identifier des programmes malveillants.

---

## Question 8 – Afficher les commandes page par page (avec pipeline)

```powershell
Get-Command | More
```

---

## Question 9 – Gérer les services

```powershell
# Lister tous les services
Get-Service

# Démarrer un service
Start-Service -Name "NomDuService"

# Arrêter un service
Stop-Service -Name "NomDuService"
```

---

## Question 10 – Créer le dossier `procédures`

```powershell
New-Item -ItemType Directory -Name "procédures"
```

---

## Question 11 – Créer le fichier `ListeProcédures.txt`

```powershell
New-Item -ItemType File -Name "ListeProcédures.txt"
```

---

## Question 12 – Ajouter du contenu dans le fichier

```powershell
Add-Content -Path "ListeProcédures.txt" -Value "1- Administration à distance sécurisée"
Add-Content -Path "ListeProcédures.txt" -Value "2- Commandes Powershell"
```

---

## Question 13 – Copier le fichier en sauvegarde

```powershell
Copy-Item -Path "ListeProcédures.txt" -Destination "ListeProcéduresSauvegarde.txt"
```

---

## Question 14 – Procédure : Gérer les droits utilisateurs sur un dossier (NTFS)

```powershell
# 1. Définir le chemin du dossier cible
$DossierCible = "C:\procédures"

# 2. Afficher les permissions actuelles
Get-NTFSAccess -Path $DossierCible

# 3. Ajouter le droit de Modification pour un groupe
Add-NTFSAccess -Path $DossierCible -Account "Domaine Users" -AccessRights Modify -Type Allow

# 4. Retirer un droit spécifique pour un utilisateur
Remove-NTFSAccess -Path $DossierCible -Account "DUPONTJ" -AccessRights FullControl -Type Allow

# 5. Supprimer toutes les permissions d'un utilisateur
Remove-NTFSAccess -Path $DossierCible -Account "DUPONTJ" -AccessRights All

# 6. Désactiver l'héritage des permissions du dossier parent
Disable-NTFSAccessInheritance -Path $DossierCible

# 7. Réactiver l'héritage
Enable-NTFSAccessInheritance -Path $DossierCible

# 8. Vérifier les droits appliqués
Get-NTFSAccess -Path $DossierCible
```

> **Note :** Les commandes `Get-NTFSAccess`, `Add-NTFSAccess` et `Remove-NTFSAccess` nécessitent le module **NTFSSecurity** (`Install-Module NTFSSecurity`).

---

*Documentation CUB Édimbourg – BTS SIO SISR*
