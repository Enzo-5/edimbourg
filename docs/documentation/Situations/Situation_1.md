# Situation 1 – Préparation de la maquette et premier paramétrage du serveur Windows 2019

## Question 1 – Réaliser un "Sysprep" afin de réinitialiser le SID de l'OS Windows 2019

Il faut aller dans `C:\Windows\System32\Sysprep` puis double-cliquer sur **Sysprep**, cocher **Généraliser** puis redémarrer la machine.

Un dossier apparaît confirmant que le sysprep a fonctionné. En allant dans le CMD et en tapant la commande `whoami /user`, le SID est bien différent de celui du voisin.

---

## Question 2 – Changer le nom du serveur : `ServeurPrimaire9`

Aller dans **Gestionnaire de serveur** → **Serveur local**, puis double-cliquer sur le nom de l'ordinateur et le modifier en `ServeurPrimaire9`.

---

## Question 3 – Modifier le mot de passe du compte administrateur local (recommandations ANSSI)

Aller dans **Gestion de l'ordinateur** → **Utilisateurs et groupes locaux**, faire un clic droit sur **Administrateur** et choisir **Définir le mot de passe**.

---

## Question 4 – Modifier le VLAN et l'adresse IP du serveur : `192.168.5.1`

Aller dans les **Paramètres réseau** et effectuer les modifications d'adresse IP.

---

## Question 5 – Fiche procédure : connexion sécurisée à distance via SSH

### Installation d'OpenSSH Server

Lancer une console **PowerShell en tant qu'administrateur** et exécuter :

```powershell
Add-WindowsCapability -Online -Name OpenSSH.Server~~~~0.0.1.0
```

### Démarrage du service SSH

```powershell
Start-Service -Name "sshd"
```

### Passage en démarrage automatique

```powershell
Set-Service -Name "sshd" -StartupType Automatic
```

### Vérification du service

```powershell
Get-Service -Name "sshd"
```

### Emplacement du fichier de configuration

Sur Windows, la configuration d'OpenSSH est stockée dans :

```
%programdata%\ssh\sshd_config
```

### Modification du port d'écoute

Modifier le port d'écoute SSH et le passer au **port 222**. Penser à bien enlever le `#` devant la ligne, sinon la modification reste en commentaire et ne sera pas prise en compte.

Après chaque modification de la configuration, redémarrer le service SSH :

```powershell
Restart-Service "sshd"
```

### Création d'une règle pare-feu pour le port 222

```powershell
New-NetFirewallRule -Name sshd -DisplayName 'OpenSSH Server (sshd) - Port 222' -Enabled True -Direction Inbound -Protocol TCP -Action Allow -LocalPort 222
```

### Interdire la connexion SSH au compte Administrateur

Dans le fichier `sshd_config`, ajouter la directive suivante pour n'autoriser que le compte `adminssh` :

```
DenyGroups administrateurs
```

On fait le test et on peut constater que le compte administrateur ne peut plus se connecter, tandis que l'utilisateur `adminssh` y accède normalement.

### Connexion RDP

Pour finir, aller sur la VM cliente afin de se connecter au serveur en **RDP (Bureau à distance)**.

---

*Documentation CUB Édimbourg – BTS SIO SISR*
