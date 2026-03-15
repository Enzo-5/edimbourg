# Cyber Situation 5 – Bastion et protection de l'accès aux serveurs

## Question 1 – Le principe du moindre privilège (PLP)

Le **principe du moindre privilège** impose qu'un compte d'administration ne dispose que des droits **strictement nécessaires** pour accomplir une tâche donnée, et pas plus.

**Enjeu principal :** limiter les dégâts en cas de compromission. Si ce compte est volé, l'attaquant aura un périmètre d'action très réduit, ce qui :

- Empêche les **mouvements latéraux** non contrôlés
- Minimise l'**impact d'une attaque**
- Préserve les systèmes critiques (Tiers 0)

---

## Question 2 – PAM (Privileged Access Management)

Le **PAM** est une stratégie et une solution logicielle visant à sécuriser et contrôler l'utilisation des **comptes à privilèges** (*root*, administrateur).

Ses composantes clés :

| Composante | Rôle |
|---|---|
| **Coffre-fort de mots de passe** | Stockage sécurisé et rotation automatique des secrets |
| **Gestion des sessions (Bastion)** | Point d'entrée unique, enregistrement de toutes les actions |
| **Flux de travail (Workflow)** | Accès accordé selon un besoin précis et limité dans le temps (Just-in-Time) |

---

## Question 3 – Bonnes pratiques pour une solution PAM

- **Centralisation** totale de tous les accès à privilèges via le PAM
- **Authentification multi-facteurs (MFA)** pour accéder au bastion
- **Enregistrement systématique** de toutes les sessions d'administration (vidéo + commandes) pour l'auditabilité
- **Accès Just-à-Temps (JIT)** : permissions temporaires accordées uniquement pour la durée nécessaire

---

## Question 4 – Lien entre PAM et SIEM/SOC

Le **PAM** génère des journaux détaillés sur chaque action privilégiée. Le **SIEM** collecte et analyse ces journaux en temps réel.

Cette liaison permet au **SOC** de :

- Détecter immédiatement un **comportement anormal** (ex. : exécution d'une commande dangereuse)
- **Croiser les données** du bastion avec d'autres sources (firewall, AD, serveurs)
- Améliorer la **réponse aux incidents**

---

## Question 5 – Recommandations ANSSI sur le bastion

L'ANSSI recommande fortement la mise en place d'une **infrastructure de rebond (bastion)** pour isoler les réseaux d'administration.

**Première mise en garde de l'ANSSI :** ne jamais administrer un système critique depuis un poste de travail classique utilisé pour la navigation Internet ou la bureautique.

**WALLIX** est une solution recommandée par CUB car c'est un outil PAM complet répondant aux exigences de l'ANSSI, assurant :

- La **traçabilité** de toutes les sessions
- Le **contrôle d'accès** granulaire
- La **gestion sécurisée des identifiants**

---

## Question 6 – Emplacement physique et logique du bastion WALLIX

| Dimension | Emplacement |
|---|---|
| **Physique** | Zone réseau hautement sécurisée et isolée (DMZ dédiée administration) |
| **Logique** | Unique intermédiaire entre les administrateurs et les serveurs cibles |

Les administrateurs se connectent **au bastion**, et le bastion se connecte **aux serveurs cibles**, coupant ainsi toute connexion directe potentiellement dangereuse.

---

## Question 8 – Règles de filtrage pour le bastion

Le filtrage se configure sur les **pare-feux encadrant le bastion** :

| N° | Source | Destination | Port | Action |
|---|---|---|---|---|
| 1 | Réseau Admins (`192.168.55.192/28`) | Bastion | 22, 3389 | Autoriser |
| 2 | Bastion | Serveurs cibles | 22, 3389 | Autoriser |
| 3 | Réseau Admins | Serveurs cibles | 22, 3389 | **Bloquer** |
| 4 | Tout | Tout | Tout | **Bloquer** |

> **Règle essentielle :** les administrateurs ne doivent **jamais** pouvoir contourner le bastion en se connectant directement aux serveurs (règle 3).

---

*Documentation CUB Édimbourg – BTS SIO SISR*
