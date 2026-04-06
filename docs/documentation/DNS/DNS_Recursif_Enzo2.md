# Serveur DNS Récursif – Enzo Beaujard

**Auteur :** Enzo Beaujard  
**Date de création :** 15/10/2025

---

## Administration et exploitation des services

## Activité 1 – Mise en place du serveur DNS(1) Récursif au sein de l'entreprise CUB

### Partie 1 – Schéma logique

![Schéma Logique](../../media/SchémaLogique2.png)

---

### Partie 2 – Installation et paramétrage du serveur DNS(1) Récursif

#### 1 – Mise à jour du serveur

```bash
sudo apt update && sudo apt upgrade
sudo apt install rsyslog
```

#### 2 – Définir les paramètres réseau du serveur

```bash
sudoedit /etc/network/interfaces
```

```
# The primary network interface
allow-hotplug ens192
auto ens192
iface ens192 inet static
address 192.168.5.10
netmask 255.255.255.0
gateway 192.168.5.254
```

#### 3 – Définir le serveur DNS récursif à utiliser

```bash
sudoedit /etc/resolv.conf
```

Mettre temporairement :

```
nameserver 8.8.8.8
```

> Lorsque le service Unbound sera opérationnel, remplacer `8.8.8.8` par `127.0.0.1` et ajouter le second serveur récursif.

#### 4 – Prendre en compte les modifications réseau

```bash
sudo systemctl restart networking
```

#### 5 – Configurer `/etc/hostname` et `/etc/hosts`

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
127.0.1.1   dns1.local.agence.cub.sioplc.fr    dns1
```

Redémarrer :

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

Pour les domaines locaux hors arborescence officielle, ajouter dans le fichier :

```yaml
# Domaine local btssio.lan - désactiver DNSSEC
domain-insecure: "btssio.lan."
private-domain: btssio.lan.

stub-zone:
    name: "btssio.lan."
    stub-addr: 172.16.20.10
    stub-addr: 172.16.20.11
```

Vérifier la syntaxe :

```bash
sudo unbound-checkconf
```

Récupérer les serveurs racines :

```bash
sudo curl --output /var/lib/unbound/root.hints https://www.internic.net/domain/named.cache
sudo chown -R unbound:unbound /var/lib/unbound/
```

Créer le fichier de log :

```bash
sudo touch /var/log/unbound.log
chown unbound:unbound /var/log/unbound.log
sudo systemctl restart unbound
sudo systemctl status unbound
```

> ⚠️ **AppArmor** bloque par défaut l'accès de Unbound à `/var/log/` :

```bash
sudoedit /etc/apparmor.d/usr.sbin.unbound
sudo apparmor_parser -r /etc/apparmor.d/usr.sbin.unbound
sudo systemctl restart apparmor
```

Observer les logs :

```bash
sudo tail -f /var/log/unbound.log
```

---

### Partie 3 – Tests du serveur DNS Récursif

Se placer dans le VLAN Production puis exécuter :

```bash
dig dns
dig 192.168.5.10
dig 192.168.5.11
dig @192.168.5.10 google.com
```

✅ Voilà, notre serveur DNS Récursif est opérationnel !

---

*Documentation CUB Édimbourg – BTS SIO SISR*
