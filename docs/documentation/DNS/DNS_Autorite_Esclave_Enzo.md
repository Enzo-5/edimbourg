# Serveur DNS Autorité (Esclave) – Enzo Beaujard

**Auteur :** Enzo Beaujard  
**Date de création :** 09/10/2025

---

## Administration et exploitation des services

## Activité 1 – Mise en place du serveur DNS (Esclave) faisant Autorité

---

### Partie 1 – Installation et paramétrage du serveur DNS Esclave

#### 1 – Vérification préalable

```bash
sudo apt update && sudo apt upgrade
sudo apt install rsyslog
sudo apt install bind9 dnsutils
```

#### 2 – Définir les paramètres réseau

```bash
sudoedit /etc/network/interfaces
```

```
# The primary network interface
allow-hotplug ens192
auto ens192
iface ens192 inet static
address 172.16.3.55
netmask 255.255.255.0
gateway 172.16.3.254
```

#### 3 – Définir les serveurs DNS récursifs à utiliser

```bash
sudoedit /etc/resolv.conf
```

```
nameserver 86.54.55.100
nameserver 9.9.9.9
```

#### 4 – Prendre en compte les modifications réseau

```bash
sudo systemctl restart networking
```

#### 5 – Configurer `/etc/hostname` et `/etc/hosts`

```bash
sudoedit /etc/hostname
```

```
ns1
```

```bash
sudoedit /etc/hosts
```

```
127.0.0.1   localhost
127.0.1.1   ns1.edimbourg.cub.sioplc.fr   ns1
```

Redémarrer :

```bash
sudo shutdown -r now
```

#### 6 – Configuration du serveur DNS Esclave (Bind9)

Déclarer la zone esclave dans `named.conf.local` :

```bash
sudoedit /etc/bind/named.conf.local
```

```
zone "edimbourg.cub.sioplc.fr" {
    type slave;
    masters { 192.36.5.10; };
    file "/var/cache/bind/db.edimbourg.cub.sioplc.fr";
};
```

> Le serveur esclave effectue un **transfert de zone** auprès du maître. Le fichier doit être créé au préalable avec les bonnes permissions :

```bash
sudo touch /var/cache/bind/db.edimbourg.cub.sioplc.fr
sudo chown bind:bind /var/cache/bind/db.edimbourg.cub.sioplc.fr
```

#### 7 – Mise en place de la journalisation

```bash
sudoedit /etc/bind/named.conf.log
```

```
logging {
    channel bind_log {
        file "/var/log/bind.log" versions 3 size 100m;
        severity info;
        print-category yes;
        print-severity yes;
        print-time     yes;
    };
    category default { bind_log; };
};
```

```bash
sudo touch /var/log/bind.log
sudo chown bind:bind /var/log/bind.log
```

Ajouter dans `/etc/bind/named.conf` :

```bash
sudoedit /etc/bind/named.conf
```

```
include "/etc/bind/named.conf.log";
```

#### 8 – Configurer AppArmor

```bash
sudoedit /etc/apparmor.d/usr.sbin.named
```

Ajouter :

```
/var/log/bind.log rw,
```

```bash
sudo apparmor_parser -r /etc/apparmor.d/local/usr.sbin.named
sudo systemctl restart apparmor
```

Vérifier et démarrer Bind9 :

```bash
sudo named-checkconf -z
sudo systemctl restart bind9
sudo systemctl status bind9
```

---

### 8 – Tests du DNS Esclave et Maître

**Vérifier le numéro Serial SOA :**

Le DNS esclave doit avoir le même numéro SERIAL que le maître.

**Test de la résolution DNS :**

```bash
dig @192.36.5.11 edimbourg.cub.sioplc.fr
```

✅ Nos serveurs DNS Maître et Esclave sont opérationnels !

---

*Documentation CUB Édimbourg – BTS SIO SISR*
