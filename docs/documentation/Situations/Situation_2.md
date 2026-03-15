# Situation 2 – Mise en place et découverte d'un environnement de développement Git

## Partie 1 – Installation de l'environnement Git

### Question 2 – Tester Git Bash

Dans le Git Bash, tester la commande :

```bash
git version
```

### Question 3 – Créer le dossier de travail

Créer un dossier `git_cub` à la racine du disque local via Git Bash. Il contiendra l'ensemble des dépôts :

```bash
mkdir /c/git_cub
```

### Question 4 – Créer un compte GitHub

Créer un compte à votre nom sur [https://github.com](https://github.com).

---

## Partie 2 – Initialisation de l'environnement Git

### Question 5 – Configuration initiale de Git

```bash
# Indiquer son nom
git config --global user.name "Votre Nom"

# Indiquer son adresse mail professionnelle
git config --global user.email votre.email@exemple.fr

# Vérifier la prise en compte
git config --global --list

# Accéder à l'aide
git --help
```

### Question 6 – Initialiser un dépôt local

```bash
# Création d'un sous-dossier pour le dépôt
mkdir premierdepot

# Initialisation du dépôt (depuis le dossier)
cd premierdepot
git init
```

---

## Partie 3 – Gérer les versions (commits) en local

### Question 8 – Ajouter les fichiers à l'index

```bash
git add .
# ou de façon sélective :
git add --patch script0.bat
```

### Question 9 – Vérifier le statut du dépôt

```bash
git status
```

### Question 10 – Réaliser le premier commit

```bash
git commit -m "mon premier commit"
```

### Question 12 – Lister les commits

```bash
git log
```

### Question 13 – Exemple de script `script0.bat` modifié

```batch
@echo off
echo Ceci est mon premier script sous git
netstat -ano
ipconfig
pause
```

### Question 19 – Revenir à une version précédente

```bash
git checkout <sha-1>
```

### Question 21 – Revenir à la dernière version

```bash
git checkout master
```

---

## Partie 4 – Gérer les dépôts sur GitHub à distance

### Question 23 – Créer un nouveau dépôt sur GitHub

Sur le site GitHub, créer un nouveau dépôt nommé **`premierdepotgithub`**.

### Question 24 – Cloner le dépôt en local

```bash
git clone https://github.com/<votre-compte>/premierdepotgithub
```

### Question 27 – Vérifier le statut

```bash
git status
```

### Question 28 – Premier commit GitHub

```bash
git commit -m "mon premier commit sur github"
```

### Question 30 – Pousser vers GitHub

```bash
git push -u origin main
```

### Question 34 – Vérification sur GitHub

Le fichier `script0.bat` apparaît bien sur le profil GitHub dans le dépôt distant.

---

*Documentation CUB Édimbourg – BTS SIO SISR*
