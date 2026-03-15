# Cyber Situation 4 – Cryptographie

## Question 1 – Vérifier l'administration sécurisée depuis le VLAN Administration

Vérifier que les équipements actifs (commutateur et routeur) sont bien administrables **uniquement depuis le VLAN Administration** (`192.168.55.0/28`).

**Fiche procédure – Administration sécurisée SSH :**

```cisco
! Restreindre l'accès VTY au VLAN Administration uniquement
access-list 10 permit 192.168.55.192 0.0.0.15
!
line vty 0 4
 access-class 10 in
 transport input ssh
 login local
```

---

## Question 2 – SSH et RDP : respect des critères cryptographiques

Les protocoles **RDP** et **SSH** garantissent la sécurité des communications distantes en respectant les critères fondamentaux de la cryptographie :

### Confidentialité

- **SSH** utilise l'algorithme d'échange de clés **Diffie-Hellman** pour établir une clé symétrique qui chiffre l'intégralité des données de session.
- **RDP** (via TLS/SSL) génère une clé de session à partir de clés publique et privée pour le chiffrement des flux graphiques.

### Intégrité

- **SSH** vérifie l'identité du serveur en confrontant sa clé d'hôte publique aux enregistrements précédents, empêchant toute falsification (attaque MitM).
- **RDP** utilise des mécanismes cryptographiques pour garantir que les PDU ne sont pas modifiés durant la transmission.

> En résumé, les deux protocoles s'appuient sur la **cryptographie asymétrique** (échange de clés) et **symétrique** (chiffrement de la session) pour protéger l'accès et les données.

---

## Question 3 – Vérifier l'intégrité lors d'une liaison SSH

Lors d'une connexion SSH, le client vérifie la **clé d'empreinte (fingerprint)** du serveur :

```bash
ssh -v utilisateur@192.168.55.1
# Le client affiche l'empreinte de la clé du serveur
# -> À vérifier manuellement lors de la première connexion
```

---

## Question 4 – Algorithme de chiffrement utilisé par le serveur SSH

L'algorithme de chiffrement utilisé par le serveur SSH est **SHA-256** (pour la signature des échanges).

---

## Question 5 – Diagramme de séquence d'une connexion SSH

```
Client                          Serveur SSH
  |                                  |
  |------- TCP SYN ----------------->|
  |<------ TCP SYN-ACK --------------|
  |------- TCP ACK ----------------->|
  |                                  |
  |=== Négociation de version ======>|
  |<== Confirmation version =========|
  |                                  |
  |=== Échange de clés (Diffie-Hellman) [ASYMÉTRIQUE] ===|
  |   -> Chaque partie génère une paire de clés éphémères |
  |   -> Calcul d'un secret partagé                      |
  |                                  |
  |=== Chiffrement symétrique établi [SYMÉTRIQUE] ===|
  |   -> AES (clé de session dérivée du secret partagé)  |
  |                                  |
  |=== Authentification utilisateur =|
  |   -> Par mot de passe ou clé publique                |
  |                                  |
  |=== Session chiffrée (tunnel) ====|
  |<======= Données chiffrées ======>|
```

---

## Question 6 – Connexion HTTPS sur l'interface d'administration Stormshield

Éléments du certificat TLS/SSL relevés lors de la connexion :

| Champ | Valeur |
|---|---|
| **Émetteur** | Stormshield (auto-signé) |
| **Sujet** | Nom ou IP du pare-feu |
| **Algorithme de signature** | SHA-256 with RSA |
| **Validité** | Date de début / Date d'expiration |
| **Clé publique** | RSA 2048 bits |

---

## Question 7 – Message d'alerte à la première connexion Stormshield

Lors de la première connexion, un message d'alerte est affiché car le certificat a été **émis par le pare-feu lui-même** (auto-signé) et **non par une Autorité de Certification reconnue** par le navigateur. Ce dernier ne peut pas vérifier l'identité de l'émetteur dans sa liste de confiance.

---

## Question 8 – CA reconnue vs CA non-reconnue : avantages et inconvénients

### Rôle d'une Autorité de Certification (CA)

La CA agit comme un **tiers de confiance** : elle vérifie l'identité d'une entité (serveur, utilisateur) et signe son **certificat numérique**, attestant de sa légitimité.

| Critère | CA reconnue (publique) | CA non-reconnue (interne / auto-signée) |
|---|---|---|
| **Confiance** | Immédiate (aucune alerte) | Doit être importée manuellement |
| **Alertes** | Aucune dans les navigateurs | Alerte de sécurité affichée |
| **Coût** | Payant (Let's Encrypt = gratuit) | Gratuit |
| **Processus** | Rigoureux (vérification d'identité) | Rapide et simple |
| **Usage** | Sites publics, accès internet | Réseaux privés, infrastructure interne |

---

*Documentation CUB Édimbourg – BTS SIO SISR*
