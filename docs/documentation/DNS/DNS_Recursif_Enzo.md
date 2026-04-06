# Serveur DNS Récursif – Enzo Beuajard

**Auteur :** Enzo Beaujard  
**Date de création :** 02/10/2025

---

## Administration et exploitation des services

## Activité 1 – Mise en place du serveur DNS Récursif au sein de l'entreprise CUB

### Partie 1 – Schéma logique

![Schéma Logique](../../media/SchémaLogique2.png)

---

### Partie 2 – Installation et paramétrage du serveur DNS Récursif

#### 1 – Vérification préalable

Mettre à jour le serveur :

```bash
sudo apt update && sudo apt upgrade
```

Installer le service de journalisation **rsyslog** :

```bash
sudo apt install rsyslog
```

#### 2 – Définir les paramètres réseau du serveur

```bash
sudoedit /etc/network/interfaces
```

#### 3 – Définir le serveur DNS récursif à utiliser

```bash
sudoedit /etc/resolv.conf
```

#### 4 – Prendre en compte les modifications réseau

```bash
sudo systemctl restart networking
```

#### 5 – Configurer `/etc/hostname` et `/etc/hosts`

Le fichier **hostname** sert à donner un nom au serveur :

```bash
sudoedit /etc/hostname
```

```
dns0
```

```bash
sudoedit /etc/hosts
```

```
127.0.0.1   localhost
127.0.1.1   dns0.local.agence.cub.sioplc.fr    dns0
```

Redémarrer le serveur pour prendre en compte le changement de nom :

```bash
sudo shutdown -r now
```

#### 6 – Installer Unbound et les outils d'administration

```bash
sudo apt install unbound dnsutils tcpdump tmux curl
```

#### 7 – Configurer Unbound

```bash
sudoedit /etc/unbound/unbound.conf
```

Vérifier la syntaxe du fichier de configuration :

```bash
sudo unbound-checkconf
```

Récupérer les adresses des serveurs racines :

```bash
sudo curl --output /var/lib/unbound/root.hints https://www.internic.net/domain/named.cache
sudo chown -R unbound:unbound /var/lib/unbound/
```

Créer le fichier de log Unbound :

```bash
sudo touch /var/log/unbound.log
chown unbound:unbound /var/log/unbound.log
sudo systemctl restart unbound
sudo systemctl status unbound
```

> ⚠️ **AppArmor** empêche par défaut le service Unbound de lire/écrire dans `/var/log/`. Il faut modifier ses permissions :

```bash
sudoedit /etc/apparmor.d/usr.sbin.unbound
```

Vérifier et redémarrer AppArmor :

```bash
sudo apparmor_parser -r /etc/apparmor.d/usr.sbin.unbound
sudo systemctl restart apparmor
```

Observer les logs en temps réel :

```bash
sudo tail -f /var/log/unbound.log
```

---

### Partie 3 – Tests du serveur DNS Récursif

Se placer dans le VLAN Administration puis exécuter :

```bash
dig dns
dig @192.168.5.10 google.com
```

✅ Voilà, notre serveur DNS Récursif est opérationnel !

---

*Documentation CUB Édimbourg – BTS SIO SISR*
