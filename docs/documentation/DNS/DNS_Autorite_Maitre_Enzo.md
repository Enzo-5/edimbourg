# Serveur DNS Autorité (Maître) – Enzo Beaujard

**Auteur :** Enzo Beaujard  
**Date de création :** 09/10/2025

---

## Administration et exploitation des services

## Activité 1 – Mise en place du serveur DNS faisant Autorité (Maître) au sein de l'entreprise CUB

---

### Partie 1 – Installation et paramétrage du serveur DNS Maître

#### 1 – Vérification préalable

Mettre à jour le serveur :

```bash
sudo apt update && sudo apt upgrade
```

Installer le service de journalisation **rsyslog** à la place de journalctl :

```bash
sudo apt install rsyslog
```

Installer le service **Bind9** et les outils de diagnostic DNS :

```bash
sudo apt install bind9 dnsutils
```

---

#### 2 – Définir les paramètres réseau du serveur

```bash
sudoedit /etc/network/interfaces
```

```
source /etc/network/interfaces.d/*

# The loopback network interface
auto lo
iface lo inet loopback

# The primary network interface
allow-hotplug ens3
iface ens3 inet static
        address 192.36.5.10/24
        gateway 192.36.5.254
        dns-nameservers 172.16.20.55 9.9.9.9
```

---

#### 3 – Définir le serveur DNS récursif à utiliser

```bash
sudoedit /etc/resolv.conf
```

```
nameserver 86.54.55.100
nameserver 9.9.9.9
```

---

#### 4 – Prendre en compte les modifications réseau

```bash
sudo systemctl restart networking
```

---

#### 5 – Configurer `/etc/hostname` et `/etc/hosts`

Le fichier **hostname** sert à donner un nom au serveur :

```bash
sudoedit /etc/hostname
```

```
ns0
```

```bash
sudoedit /etc/hosts
```

```
127.0.0.1       localhost
127.0.1.1       ns0.edimbourg.cub.sioplc.fr    ns0

# The following lines are desirable for IPv6 capable hosts
::1     localhost ip6-localhost ip6-loopback
ff02::1 ip6-allnodes
ff02::2 ip6-allrouters
```

Redémarrer le serveur pour prendre en compte le changement de nom :

```bash
sudo shutdown -r now
```

---

#### 6 – Configuration du serveur DNS Maître (Bind9)

Vérifier le contenu du fichier principal :

```bash
sudo cat /etc/bind/named.conf
```

```
// This is the primary configuration file for the BIND DNS server named.
//
// If you are just adding zones, please do that in /etc/bind/named.conf.local

include "/etc/bind/named.conf.options";
include "/etc/bind/named.conf.local";
include "/etc/bind/named.conf.default-zones";

// Ajout du fichier de paramétrage de la journalisation du service BIND
include "/etc/bind/named.conf.log";
```

Configurer les options globales :

```bash
sudo nano /etc/bind/named.conf.options
```

```
options {
        directory "/var/cache/bind";

        // Définit le port et les adresses IPv4 d'écoute du service Bind
        listen-on port 53 { 127.0.0.1; 192.36.5.10; };

        // forwarders {
        //      0.0.0.0;
        // };

        dnssec-validation auto;

        listen-on-v6 { any; };
};
```

Déclarer la zone dans `named.conf.local` :

```bash
sudoedit /etc/bind/named.conf.local
```

```
zone "edimbourg.cub.sioplc.fr" {
    type master;
    allow-transfer { 192.36.5.11; };
    file "/var/cache/bind/db.edimbourg.cub.sioplc.fr";
};
```

> La directive `file` déclare le fichier de zone contenant les enregistrements (SOA, NS, A…). La directive `allow-transfer` déclare les serveurs esclaves habilités à récupérer la zone.

Créer le fichier de zone :

```bash
sudoedit /var/cache/bind/db.edimbourg.cub.sioplc.fr
```

```
$TTL 43200;     12 heures

edimbourg.cub.sioplc.fr. IN SOA ns0.edimbourg.cub.sioplc.fr. postmaster.edimbourg.cub.sioplc.fr. (
        2025091001 ; Serial
        1D         ; Refresh
        1H         ; Retry
        1W         ; Expire
        3H )       ; Negative Cache TTL

; Déclaration des serveurs DNS faisant autorité sur la zone
; à l'aide de l'enregistrement NS.
edimbourg.cub.sioplc.fr.   IN  NS  ns0.edimbourg.cub.sioplc.fr.
edimbourg.cub.sioplc.fr.   IN  NS  ns1.edimbourg.cub.sioplc.fr.

; Déclaration des correspondances entre un nom de domaine et une adresse IP
; à l'aide de l'enregistrement A.
ns0     IN  A   192.36.5.10
ns1     IN  A   192.36.5.11
www     IN  A   172.36.3.2

; Exemple d'utilisation d'un alias CNAME.
extranet IN CNAME www.tours.tierslieux86.fr.
```

Appliquer les bonnes permissions :

```bash
sudo chown bind:bind /var/cache/bind/db.edimbourg.cub.sioplc.fr
```

---

#### 7 – Mise en place de la journalisation

```bash
sudoedit /etc/bind/named.conf.log
```

```
logging {
    channel bind_log {
            file "/var/log/bind.log" versions 3 size 100m;
            severity info;
            print-category  yes;
            print-severity  yes;
            print-time      yes;
    };
    category default { bind_log; };
};
```

> Ce fichier active la journalisation des événements DNS. Il est nécessaire de créer un fichier de log vide avec les bonnes permissions au préalable.

```bash
sudo touch /var/log/bind.log
sudo chown bind:bind /var/log/bind.log
```

Déclarer ce fichier dans `/etc/bind/named.conf` :

```bash
sudoedit /etc/bind/named.conf
```

Ajouter à la fin :

```
// Ajout du fichier de paramétrage de la journalisation du service BIND
include "/etc/bind/named.conf.log";
```

---

#### 8 – Configurer AppArmor

```bash
sudoedit /etc/apparmor.d/usr.sbin.named
```

Ajouter la ligne suivante pour autoriser Bind9 à écrire dans le fichier de log :

```
# On autorise le daemon Bind 9 à lire et écrire dans le fichier /var/log/bind.log
/var/log/bind.log rw,
```

Vérifier et redémarrer AppArmor :

```bash
sudo apparmor_parser -r /etc/apparmor.d/usr.sbin.named
sudo systemctl restart apparmor
```

---

#### 9 – Vérification et démarrage de Bind9

```bash
sudo named-checkconf -z
sudo systemctl restart bind9
sudo systemctl status bind9
```

Résultat attendu :

```
zone edimbourg.cub.sioplc.fr/IN: loaded serial 2025091001
zone localhost/IN: loaded serial 2
zone 127.in-addr.arpa/IN: loaded serial 1
zone 0.in-addr.arpa/IN: loaded serial 1
zone 255.in-addr.arpa/IN: loaded serial 1

● named.service - BIND Domain Name Server
     Loaded: loaded (/lib/systemd/system/named.service; enabled; preset: enabled)
     Active: active (running) since Thu 2025-10-09 11:26:39 CEST; 4s ago
     Status: "running"
     Tasks: 4 (limit: 2141)
     Memory: 11.3M
     CPU: 50ms
```

✅ Notre serveur DNS faisant autorité maître est opérationnel !

---

### Partie 2 – Tests du serveur DNS Maître

#### Test de la résolution DNS

```bash
dig @192.36.5.10 edimbourg.cub.sioplc.fr
```

#### Test du Numéro Serial SOA

```bash
dig @192.36.5.10 edimbourg.cub.sioplc.fr SOA +short
```

Résultat :

```
ns0.edimbourg.cub.sioplc.fr. postmaster.edimbourg.cub.sioplc.fr. 2025091001 86400 3600 604800 10800
```

> Le DNS esclave doit avoir le **même numéro SERIAL** que le maître.

#### Tests de résolution des enregistrements A

```bash
# Via le serveur maître (ns0)
dig A ns0.edimbourg.cub.sioplc.fr @192.36.5.10
# Résultat : ns0.edimbourg.cub.sioplc.fr. 43200 IN A 192.36.5.10

dig A ns0.edimbourg.cub.sioplc.fr @192.36.5.11
# Résultat : ns0.edimbourg.cub.sioplc.fr. 43200 IN A 192.36.5.10

dig A ns1.edimbourg.cub.sioplc.fr @192.36.5.10
# Résultat : ns1.edimbourg.cub.sioplc.fr. 43200 IN A 192.36.5.11

dig A ns1.edimbourg.cub.sioplc.fr @192.36.5.11
# Résultat : ns1.edimbourg.cub.sioplc.fr. 43200 IN A 192.36.5.11
```

#### Test via le serveur DNS Récursif

```bash
dig A ns1.edimbourg.cub.sioplc.fr @192.168.5.10
# Résultat : ns1.edimbourg.cub.sioplc.fr. 43200 IN A 192.36.5.11

dig A ns1.edimbourg.cub.sioplc.fr @192.168.5.11
# Résultat : ns1.edimbourg.cub.sioplc.fr. 50213 IN A 192.36.5.11
```

✅ Nos serveurs DNS Maître et Esclave sont maintenant opérationnels !

---

*Documentation CUB Édimbourg – BTS SIO SISR*
