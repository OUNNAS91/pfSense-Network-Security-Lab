# Configuration des règles firewall

## Objectif
Les règles firewall permettent à pfSense de décider quels flux réseau sont :

autorisés ;
bloqués ;
ou rejetés.

Dans notre laboratoire, les règles ont été utilisées pour contrôler le trafic provenant du poste Windows 11.

Le poste de test possède l'adresse :

192.168.56.102

## Principe de fonctionnement des règles
Lorsqu'un paquet arrive sur une interface, pfSense compare le trafic avec les règles configurées.

Une règle peut prendre en compte :

l'interface ;
l'adresse IP source ;
le protocole ;
le port source ;
l'adresse IP destination ;
le port destination.

Une règle peut ensuite :

Pass : autoriser le trafic ;
Block : bloquer le trafic sans répondre à l'émetteur ;
Reject : bloquer le trafic en envoyant une réponse à l'émetteur.

### Explication simple

On peut comparer les règles à un contrôle d'accès :

Trafic réseau
      │
      ▼
   pfSense
      │
      ├── Règle correspondante
      │
      ▼
 Pass / Block / Reject

## Importance de l'ordre des règles

L'ordre des règles est très important.

Les règles sont évaluées **de haut en bas**.

Lorsqu'une règle correspond au trafic, son action est appliquée.

### Exemple

Règle 1 → Bloquer HTTP
Règle 2 → Autoriser Internet

Une connexion HTTP correspond à la première règle et sera donc bloquée avant d'atteindre la règle suivante.

### Explication simple

Il faut donc être attentif à l'ordre des règles.

Une règle de blocage placée avant une règle d'autorisation peut empêcher le trafic d'atteindre cette dernière.

## Anti-Lockout Rule

Une **Anti-Lockout Rule** est présente sur l'interface LAN.

Elle permet de conserver l'accès à l'interface d'administration pfSense depuis le réseau LAN.

La règle autorise notamment l'accès à :

LAN Address
Port 443

Le port `443` correspond à HTTPS.

### Pourquoi cette règle est importante ?

Sans cette protection, une mauvaise configuration des règles LAN pourrait empêcher l'administrateur d'accéder à l'interface Web de pfSense.

## Règle de blocage HTTP

Une règle appelée :

Bloquer HTTP WIN11-FW

a été créée afin de bloquer les connexions HTTP provenant du poste Windows 11.

### Configuration

| Paramètre        | Valeur           |
| ---------------- | ---------------- |
| Action           | Block            |
| Interface        | LAN              |
| Adresse IP       | IPv4             |
| Protocole        | TCP              |
| Source           | `192.168.56.102` |
| Destination      | Any              |
| Port destination | `80 (HTTP)`      |

Cette règle bloque donc les connexions HTTP du poste Windows 11.

## Règle de blocage Internet
Une autre règle a été configurée :

Bloquer Internet WIN11-FW

Elle permet de bloquer le trafic provenant du poste Windows 11 lorsque cela est nécessaire pour les tests du laboratoire.

Cette règle a notamment servi à vérifier le comportement du pare-feu lorsqu'un poste est complètement privé d'accès au réseau externe.

### Configuration générale

| Paramètre   | Valeur           |
| ----------- | ---------------- |
| Interface   | LAN              |
| Adresse IP  | IPv4             |
| Source      | `192.168.56.102` |
| Destination | Any              |
| Action      | Block            |

Cette règle est volontairement conservée dans le laboratoire pour permettre des tests supplémentaires.

## Règle de blocage ICMP

Une règle appelée :

Bloquer ICMP vers 8.8.8.8

a été utilisée pour empêcher le poste Windows 11 d'envoyer des requêtes ICMP (ping) vers `8.8.8.8`.

### Configuration

| Paramètre   | Valeur           |
| ----------- | ---------------- |
| Action      | Block            |
| Interface   | LAN              |
| Adresse IP  | IPv4             |
| Protocole   | ICMP             |
| Source      | `192.168.56.102` |
| Destination | `8.8.8.8`        |


## Règle de blocage DNS

Une règle appelée :

Bloquer DNS WIN11-FW

a également été utilisée pendant les tests.

Son objectif était de bloquer les requêtes DNS du poste Windows 11 vers le serveur DNS pfSense.

### Configuration observée

| Paramètre        | Valeur           |
| ---------------- | ---------------- |
| Interface        | LAN              |
| Protocole        | UDP              |
| Source           | `192.168.56.102` |
| Destination      | `192.168.56.10`  |
| Port destination | `53 (DNS)`       |


Lors du test, Windows a indiqué :

DNS request timed out

Le blocage a également été visible dans les journaux pfSense.

Après modification de la configuration, une nouvelle vérification a confirmé que le DNS fonctionnait à nouveau.

## Règle par défaut LAN

pfSense possède également une règle :

Default allow LAN to any rule

Cette règle permet normalement au trafic provenant du réseau LAN d'accéder aux destinations autorisées lorsque aucune règle précédente ne bloque le trafic.

Elle concerne :

Source : LAN subnets
Destination : Any


### Explication simple

On peut simplifier le fonctionnement ainsi :

Trafic Windows 11
       │
       ▼
Règles spécifiques
       │
       ├── HTTP → ❌
       ├── ICMP 8.8.8.8 → ❌
       ├── DNS → selon la règle
       │
       ▼
Règle par défaut
       │
       ▼
Trafic autorisé si aucune règle précédente
ne l'a bloqué

## Exemple de filtrage par port

Les tests ont permis de vérifier qu'un même poste peut avoir des comportements différents selon le port utilisé.

### HTTP

TCP / 80
Bloqué

### HTTPS

TCP / 443
Autorisé

### Pourquoi ?

HTTP et HTTPS sont deux services différents.

Le pare-feu peut donc traiter leurs communications différemment.

## 11. Exemple de filtrage par protocole

Le protocole peut également être utilisé comme critère.

Par exemple :

ICMP → 8.8.8.8
Bloqué

alors que :

ICMP → 1.1.1.1
Autorisé

Cela montre qu'une règle peut être suffisamment précise pour cibler une destination particulière.

## Vérification dans les journaux

La journalisation a permis de confirmer que les règles fonctionnent réellement.

Par exemple, lors du test HTTP, le journal pfSense a affiché :

Aug 20 15:13:50
LAN
Bloquer HTTP WIN11-FW
192.168.56.102:64151
34.223.124.45:80
TCP:S

Cette entrée indique que Windows 11 a tenté d'établir une connexion TCP vers le port `80` et que la règle **Bloquer HTTP WIN11-FW** a traité cette connexion.

Lors du test ICMP, une entrée similaire a été observée pour la règle :

Bloquer ICMP vers 8.8.8.8

## Règles WAN

Les règles WAN ont également été vérifiées.

Deux règles particulières étaient présentes :

Block private networks
Block bogon networks

### Block private networks

Cette règle bloque les adresses privées provenant du côté WAN.

### Block bogon networks

Cette règle bloque des réseaux réservés ou non attribués normalement sur Internet.

### Pourquoi ?

Ces protections réduisent certains trafics suspects provenant de l'extérieur.

## Résumé des principales règles

| Règle                     | Objectif                                 |
| ------------------------- | -----------------------------------------|
| Anti-Lockout Rule         | Conserver l'accès à l'administration LAN |
| Bloquer HTTP WIN11-FW     | Bloquer HTTP / port 80                   |
| Bloquer Internet WIN11-FW | Bloquer l'accès externe du poste         |
| Bloquer ICMP vers 8.8.8.8 | Bloquer le ping vers 8.8.8.8             |
| Bloquer DNS WIN11-FW      | Tester le blocage DNS                    |
| Default allow LAN to any  | Autoriser le trafic LAN non bloqué       |
| Block private networks    | Bloquer les réseaux privés côté WAN      |
| Block bogon networks      | Bloquer les réseaux bogon côté WAN       |

## Résultat

La configuration des règles permet maintenant de contrôler précisément le trafic du laboratoire.

Les tests montrent qu'il est possible de filtrer :

par adresse IP ;
par destination ;
par protocole ;
par port ;
par interface.

Les règles et leur ordre constituent donc un élément essentiel de la sécurité du réseau.

Les journaux pfSense permettent ensuite de vérifier et d'analyser les décisions prises par le pare-feu.