# Exposé : L'Adressage IP et les Protocoles Réseau

---

## Introduction

Les réseaux informatiques modernes reposent sur un ensemble de règles et de conventions appelées **protocoles**. Au cœur de ces protocoles se trouve le système d'**adressage IP**, qui permet d'identifier chaque machine de manière unique sur un réseau. Cet exposé présente le fonctionnement de l'adressage IP (v4 et v6) ainsi que les principaux protocoles qui structurent les communications réseau.

---

## Partie 1 — L'Adressage IP

### 1.1 Définition et rôle d'une adresse IP

Une **adresse IP** (*Internet Protocol Address*) est un identifiant numérique attribué à chaque interface réseau d'un équipement (ordinateur, routeur, smartphone, etc.) connecté à un réseau utilisant le protocole IP.

Elle remplit deux rôles fondamentaux :
- **Identifier** l'hôte ou l'interface réseau de manière unique
- **Localiser** l'hôte sur le réseau afin d'y acheminer les données (routage)

---

### 1.2 IPv4 — Adressage en 32 bits

#### Structure
Une adresse IPv4 est un entier sur **32 bits**, représentée en **notation décimale pointée** : quatre octets séparés par des points.

```
Exemple :  192.168.1.10
Binaire  :  11000000.10101000.00000001.00001010
```

Chaque octet peut varier de `0` à `255`, ce qui donne un espace d'adressage théorique de **2³² = ~4,3 milliards** d'adresses.

#### Composition d'une adresse IPv4
Une adresse IPv4 est divisée en deux parties :

| Partie | Rôle |
|--------|------|
| **Partie réseau** (NetID) | Identifie le réseau auquel appartient la machine |
| **Partie hôte** (HostID) | Identifie la machine au sein de ce réseau |

---

### 1.3 Les Classes d'adresses IPv4

Historiquement, les adresses IPv4 ont été organisées en **classes** selon leur premier octet :

| Classe | Plage du 1er octet | Masque par défaut | Nb de réseaux | Nb d'hôtes/réseau | Usage |
|--------|--------------------|-------------------|---------------|-------------------|-------|
| **A** | 1 – 126 | 255.0.0.0 (/8) | 126 | ~16 millions | Très grands réseaux |
| **B** | 128 – 191 | 255.255.0.0 (/16) | 16 384 | ~65 000 | Réseaux moyens |
| **C** | 192 – 223 | 255.255.255.0 (/24) | ~2 millions | 254 | Petits réseaux |
| **D** | 224 – 239 | — | — | — | Multicast |
| **E** | 240 – 255 | — | — | — | Réservé/Expérimental |

> **Note :** L'adresse `127.0.0.1` est réservée à la **boucle locale** (loopback) — elle désigne toujours la machine elle-même.

---

### 1.4 Le Masque de Sous-réseau

Le **masque de sous-réseau** est un entier de 32 bits composé d'une suite de `1` (partie réseau) suivie d'une suite de `0` (partie hôte).

```
Masque classe C :  255.255.255.0
Binaire          :  11111111.11111111.11111111.00000000
```

En appliquant un **ET logique (AND)** entre l'adresse IP et le masque, on obtient l'**adresse réseau** :

```
Adresse IP   :  192.168.1.10   →  11000000.10101000.00000001.00001010
Masque       :  255.255.255.0  →  11111111.11111111.11111111.00000000
              ──────────────────────────────────────────────────────
Adresse réseau : 192.168.1.0   →  11000000.10101000.00000001.00000000
```

---

### 1.5 La Notation CIDR

La notation **CIDR** (*Classless Inter-Domain Routing*) est une représentation compacte du masque : on indique le nombre de bits à `1` après l'adresse IP, précédé d'un `/`.

```
192.168.1.0/24   →  masque 255.255.255.0  (24 bits à 1)
10.0.0.0/8       →  masque 255.0.0.0      (8 bits à 1)
172.16.0.0/16    →  masque 255.255.0.0    (16 bits à 1)
```

---

### 1.6 Le Sous-adressage (Subnetting)

Le **sous-adressage** consiste à diviser un réseau en plusieurs **sous-réseaux** plus petits, en empruntant des bits à la partie hôte pour les affecter à la partie réseau.

Une adresse IP avec sous-adressage comporte alors **3 parties** :

```
[ Identifiant réseau | Identifiant sous-réseau | Identifiant machine ]
```

**Exemple :** Diviser `192.168.1.0/24` en 4 sous-réseaux :

| Sous-réseau | Plage d'hôtes | Masque |
|-------------|---------------|--------|
| 192.168.1.0/26 | .1 à .62 | 255.255.255.192 |
| 192.168.1.64/26 | .65 à .126 | 255.255.255.192 |
| 192.168.1.128/26 | .129 à .190 | 255.255.255.192 |
| 192.168.1.192/26 | .193 à .254 | 255.255.255.192 |

---

### 1.7 Adresses IP spéciales

| Adresse | Type | Description |
|---------|------|-------------|
| `x.x.x.0` | Adresse réseau | Identifie le réseau (non attribuable à un hôte) |
| `x.x.x.255` | Broadcast | Envoi à tous les hôtes du réseau |
| `127.0.0.1` | Loopback | Boucle locale (la machine elle-même) |
| `0.0.0.0` | Route par défaut | Représente n'importe quelle adresse |
| `169.254.x.x` | APIPA | Adresse auto-configurée (pas de DHCP disponible) |

---

### 1.8 Adresses IP privées (RFC 1918)

Ces plages d'adresses sont réservées aux réseaux privés (non routables sur Internet) :

| Classe | Plage | CIDR |
|--------|-------|------|
| A | 10.0.0.0 – 10.255.255.255 | 10.0.0.0/8 |
| B | 172.16.0.0 – 172.31.255.255 | 172.16.0.0/12 |
| C | 192.168.0.0 – 192.168.255.255 | 192.168.0.0/16 |

Le mécanisme **NAT** (*Network Address Translation*) permet à ces machines d'accéder à Internet via une adresse publique partagée.

---

### 1.9 IPv6 — Adressage en 128 bits

Face à l'épuisement des adresses IPv4, **IPv6** a été développé. Il utilise des adresses sur **128 bits**, représentées en hexadécimal :

```
Exemple : 2001:0db8:85a3:0000:0000:8a2e:0370:7334
Abrégé  : 2001:db8:85a3::8a2e:370:7334
```

| Caractéristique | IPv4 | IPv6 |
|----------------|------|------|
| Taille adresse | 32 bits | 128 bits |
| Nombre d'adresses | ~4,3 milliards | ~3,4 × 10³⁸ |
| Notation | Décimale pointée | Hexadécimale |
| En-tête | Variable | Fixe (40 octets) |
| NAT requis | Souvent | Non (adresses suffisantes) |
| Configuration auto | Non native | Oui (SLAAC) |

---

## Partie 2 — Les Protocoles Réseau

### 2.1 Le Modèle OSI et le Modèle TCP/IP

Les protocoles réseau sont organisés en **couches** selon deux modèles de référence :

| N° | Modèle OSI | Modèle TCP/IP | Protocoles courants |
|----|------------|---------------|---------------------|
| 7 | Application | Application | HTTP, FTP, SMTP, DNS, DHCP |
| 6 | Présentation | Application | SSL/TLS, MIME |
| 5 | Session | Application | NetBIOS, RPC |
| 4 | Transport | Transport | TCP, UDP |
| 3 | Réseau | Internet | IP, ICMP, ARP, OSPF, BGP |
| 2 | Liaison de données | Accès réseau | Ethernet, Wi-Fi (802.11), PPP |
| 1 | Physique | Accès réseau | Câble, Fibre, Signal radio |

---

### 2.2 Protocoles de la Couche Transport

#### TCP — Transmission Control Protocol
TCP est un protocole de transport **orienté connexion** et **fiable**.

**Caractéristiques :**
- Établissement de connexion en **3 étapes** (*Three-Way Handshake*) : `SYN → SYN-ACK → ACK`
- Garantie de **livraison des données** (accusés de réception)
- **Contrôle de flux** et **contrôle de congestion**
- **Réordonnancement** des paquets arrivés dans le désordre
- Utilisé par : HTTP/HTTPS, FTP, SMTP, SSH

```
CLIENT          SERVEUR
  |──── SYN ────▶|
  |◀── SYN-ACK ──|
  |──── ACK ────▶|
  |  (Connexion établie)
```

#### UDP — User Datagram Protocol
UDP est un protocole de transport **sans connexion** et **non fiable**.

**Caractéristiques :**
- Pas de handshake, envoi direct des données
- Pas d'accusé de réception → **plus rapide**
- Pas de garantie d'ordre ni de livraison
- Utilisé par : DNS, DHCP, VoIP, streaming vidéo, jeux en ligne

| Critère | TCP | UDP |
|---------|-----|-----|
| Connexion | Oui | Non |
| Fiabilité | Oui | Non |
| Ordre des paquets | Garanti | Non garanti |
| Vitesse | Plus lent | Plus rapide |
| Usage | Web, email, FTP | Streaming, VoIP, DNS |

---

### 2.3 Protocoles de la Couche Réseau (Internet)

#### IP — Internet Protocol
- Protocole **sans connexion** et **non fiable** (best-effort)
- Responsable de l'**adressage logique** et du **routage**
- Deux versions : **IPv4** (32 bits) et **IPv6** (128 bits)

#### ICMP — Internet Control Message Protocol
- Protocole de **contrôle et de diagnostic**
- Utilisé par la commande `ping` (test de connectivité) et `traceroute` (traçage de routes)
- Signale les erreurs réseau (hôte inaccessible, TTL expiré, etc.)

```bash
# Exemple de ping
$ ping 8.8.8.8
PING 8.8.8.8 : 56 octets de données
64 octets de 8.8.8.8 : icmp_seq=1 ttl=118 temps=12.4 ms
```

#### ARP — Address Resolution Protocol
- Fait correspondre une **adresse IP** (couche 3) à une **adresse MAC** (couche 2)
- Diffuse une requête sur le réseau : *"Qui possède l'IP 192.168.1.1 ?"*
- Le propriétaire répond avec son adresse MAC

```
Machine A  ──[ARP Request : qui est 192.168.1.1?]──▶  (broadcast)
Machine B  ◀──[ARP Reply : c'est moi ! MAC : AA:BB:CC:DD:EE:FF]──
```

#### RARP — Reverse ARP
- Permet à une machine de **découvrir son adresse IP** à partir de son adresse MAC (précurseur du DHCP)

---

### 2.4 Protocoles de la Couche Application

#### DNS — Domain Name System
- Traduit les **noms de domaine** (ex. `www.google.com`) en **adresses IP**
- Fonctionne de manière hiérarchique et distribuée
- Utilise le **port 53** (UDP principalement)

```
Navigateur ──["Quelle est l'IP de google.com ?"]──▶ Serveur DNS
Navigateur ◀──["google.com = 142.250.74.46"]──────── Serveur DNS
```

#### DHCP — Dynamic Host Configuration Protocol
- Attribue **automatiquement** une adresse IP à chaque machine du réseau
- Distribue aussi : masque de sous-réseau, passerelle, serveur DNS
- Utilise les **ports 67** (serveur) et **68** (client)
- Fonctionne en 4 étapes : **DISCOVER → OFFER → REQUEST → ACK**

#### HTTP/HTTPS — HyperText Transfer Protocol (Secure)
- Protocole de transfert de pages **Web**
- HTTP : port **80** (non chiffré)
- HTTPS : port **443** (chiffré avec TLS/SSL)

#### FTP — File Transfer Protocol
- Protocole de **transfert de fichiers** entre client et serveur
- Ports **20** (données) et **21** (contrôle)
- Il existe des variantes sécurisées : **SFTP** (via SSH) et **FTPS** (via TLS)

#### SMTP / POP3 / IMAP — Messagerie électronique

| Protocole | Rôle | Port |
|-----------|------|------|
| **SMTP** | Envoi d'e-mails | 25 / 587 |
| **POP3** | Réception (téléchargement) | 110 / 995 |
| **IMAP** | Réception (synchronisation) | 143 / 993 |

#### SSH — Secure Shell
- Protocole de **connexion distante sécurisée** (chiffré)
- Remplace Telnet (non chiffré)
- Port **22**
- Permet aussi le transfert de fichiers (SFTP) et le tunneling

---

### 2.5 Protocoles de Routage

Le **routage** est le processus qui détermine le chemin emprunté par les paquets entre la source et la destination.

| Protocole | Type | Description |
|-----------|------|-------------|
| **RIP** | Vecteur de distance | Simple, limité à 15 sauts, pour petits réseaux |
| **OSPF** | État de liens | Rapide, efficace, pour grands réseaux internes |
| **BGP** | Vecteur de chemin | Protocole de routage d'Internet (entre FAI) |
| **EIGRP** | Hybride | Propriétaire Cisco, combine RIP et OSPF |

---

### 2.6 Protocoles de Sécurité Réseau

| Protocole | Description |
|-----------|-------------|
| **TLS/SSL** | Chiffrement des communications (HTTPS, SMTPS…) |
| **IPSec** | Chiffrement au niveau IP (VPN) |
| **WPA3** | Sécurité des réseaux Wi-Fi |
| **Kerberos** | Authentification centralisée dans les réseaux d'entreprise |

---

## Partie 3 — Tableau Récapitulatif des Ports

| Port | Protocole | Service |
|------|-----------|---------|
| 20/21 | TCP | FTP |
| 22 | TCP | SSH |
| 23 | TCP | Telnet |
| 25 | TCP | SMTP |
| 53 | UDP/TCP | DNS |
| 67/68 | UDP | DHCP |
| 80 | TCP | HTTP |
| 110 | TCP | POP3 |
| 143 | TCP | IMAP |
| 443 | TCP | HTTPS |
| 993 | TCP | IMAPS |
| 995 | TCP | POP3S |

---

## Conclusion

L'adressage IP et les protocoles réseau forment le socle de toute communication numérique moderne. L'**adressage IPv4**, bien que limité, reste dominant grâce aux mécanismes de NAT et de sous-adressage. La transition vers **IPv6** est en cours pour répondre à l'épuisement des adresses. Les protocoles, organisés en couches (modèle OSI / TCP/IP), assurent chacun un rôle précis : identification (**IP**), transport fiable (**TCP**) ou rapide (**UDP**), résolution de noms (**DNS**), attribution d'adresses (**DHCP**), transfert de fichiers (**FTP**), sécurité (**TLS, IPSec**), etc. Maîtriser ces fondamentaux est essentiel pour comprendre, concevoir et administrer tout réseau informatique.

---



