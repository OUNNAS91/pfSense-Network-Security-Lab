# Journalisation et analyse

## Objectif
Cette étape consiste à utiliser les fonctions de journalisation de pfSense afin d'observer et d'analyser le trafic réseau.

Les journaux, également appelés **logs**, permettent de savoir ce qui s'est passé sur le réseau.

Ils permettent notamment d'identifier :

les connexions autorisées ;
les connexions bloquées ;
les adresses IP source ;
les adresses IP destination ;
les protocoles utilisés ;
les ports utilisés ;
les règles qui ont traité le trafic.

## Qu'est-ce qu'un log ?
Un log est une trace enregistrée par un système lorsqu'un événement se produit.

Dans pfSense, un log firewall peut par exemple indiquer :

Interface : LAN
Source : 192.168.56.102
Destination : 8.8.8.8
Protocole : ICMP
Règle : Bloquer ICMP vers 8.8.8.8

### Explication simple

On peut comparer un log à une caméra de surveillance.

La caméra enregistre ce qui se passe.

Le log fait quelque chose de similaire pour le réseau : il garde une trace des événements importants.

## Accès aux journaux
Les journaux du pare-feu sont accessibles depuis l'interface Web pfSense.

Ils permettent d'observer les événements liés au trafic réseau.

Lorsqu'une règle de blocage est utilisée, une entrée peut apparaître dans le journal.

Cela permet de vérifier que la règle fonctionne réellement.

## Informations importantes dans un log

Une entrée de journal peut contenir plusieurs informations.

| Information      | Signification                       |
| ---------------- | ----------------------------------- |
| Date / heure     | Moment où l'événement s'est produit |
| Interface        | Interface qui a traité le trafic    |
| Action / règle   | Règle ayant traité le trafic        |
| Source           | Machine à l'origine du trafic       |
| Port source      | Port utilisé par la machine source  |
| Destination      | Machine ou serveur ciblé            |
| Port destination | Port du service ciblé               |
| Protocole        | TCP, UDP, ICMP, etc.                |

### Exemple

LAN
192.168.56.102:64151→34.223.124.45:80
                    TCP

Cela signifie que le poste Windows 11 a tenté d'établir une connexion TCP vers le port 80 d'un serveur distant.

## Analyse du blocage HTTP
Lors du test HTTP, le journal pfSense a enregistré :

Aug 20 15:13:50
LAN
Bloquer HTTP WIN11-FW
192.168.56.102:64151
34.223.124.45:80
TCP:S

### Analyse

On peut décomposer cette entrée :

LAN

Le trafic provient de l'interface LAN.

192.168.56.102:64151

Il s'agit du poste Windows 11.

Le port `64151` est un port source temporaire utilisé par Windows.

34.223.124.45:80

Il s'agit du serveur distant et du port `80`.

Le port `80` correspond à HTTP.

TCP:S

Le trafic utilise TCP et le `S` correspond à une tentative de début de connexion TCP, appelée **SYN**.

Bloquer HTTP WIN11-FW

Cette information permet d'identifier la règle qui a traité le trafic.

### Conclusion

Le log confirme que la tentative de connexion HTTP a été traitée par la règle de blocage.

## Analyse du blocage ICMP

Lors du test :

ping 8.8.8.8

le journal a enregistré :

Aug 19 19:52:36
LAN
Bloquer ICMP vers 8.8.8.8
192.168.56.102
8.8.8.8
ICMP

### Analyse

La source est :

192.168.56.102

Il s'agit du poste Windows 11.

La destination est :

8.8.8.8

Le protocole est :

ICMP

La règle appliquée est :

Bloquer ICMP vers 8.8.8.8

### Conclusion

Le journal confirme que pfSense a bien bloqué le trafic ICMP destiné à `8.8.8.8`.

Cela correspond au résultat observé sur Windows 11 :

Délai d'attente de la demande dépassé.

## Analyse du blocage DNS

Lors du test de blocage DNS, le journal a enregistré :

Aug 20 13:42:24
LAN
Bloquer DNS WIN11-FW
192.168.56.102:53751
192.168.56.10:53
UDP

### Analyse

La source est :

192.168.56.102:53751

Il s'agit de Windows 11.

La destination est :

192.168.56.10:53

Il s'agit du serveur DNS pfSense.

Le port `53` correspond au DNS.

Le protocole utilisé est :

UDP

La règle appliquée est :

Bloquer DNS WIN11-FW

### Résultat

Windows 11 a alors affiché :

DNS request timed out

Cela confirme que le trafic DNS était bien bloqué par pfSense.

Après modification de la règle, une nouvelle résolution DNS a fonctionné.

## Analyse d'une connexion HTTPS

Les connexions actives peuvent également être observées dans pfSense grâce aux **States**.

Un état observé était notamment :

LAN
TCP
192.168.56.102:59143
→
51.8.71.184:443
FIN_WAIT_2:ESTABLISHED
195 / 199
11 KiB / 16 KiB

### Analyse

La source est :

192.168.56.102:59143

Il s'agit de Windows 11.

La destination est :

51.8.71.184:443

Le port `443` correspond à HTTPS.

Le protocole est :

TCP

Les informations sur les paquets et les volumes de données permettent également d'observer l'activité de cette connexion.

## Qu'est-ce qu'un State ?
Un **State** représente une connexion suivie par pfSense.

### Explication simple

Le log répond principalement à la question :

> « Quel événement s'est produit ? »

Le State permet plutôt de voir :

> « Quelle connexion est actuellement suivie par le pare-feu ? »

Par exemple :

Windows 11
192.168.56.102
       │
       │ TCP / 443
       ▼
51.8.71.184

pfSense conserve l'état de cette communication afin de pouvoir suivre le flux réseau.

## États TCP

Les connexions TCP peuvent passer par différents états.

Par exemple :

SYN
 │
 ▼
ESTABLISHED
 │
 ▼
FIN_WAIT
 │
 ▼
Connexion terminée

### Explication simple

TCP fonctionne comme une conversation avec une connexion suivie.

Avant d'échanger des données, les deux machines établissent la connexion.

À la fin, la connexion est fermée.

pfSense peut suivre ces différentes étapes.

## Différence entre Logs et States

| Élément         | Rôle                                        |
| --------------- | ------------------------------------------- |
| Logs            | Garder une trace des événements             |
| States          | Suivre les connexions réseau                |
| Règles firewall | Décider si le trafic est autorisé ou bloqué |

## Importance des logs en cybersécurité
Les logs sont importants dans une infrastructure informatique car ils permettent notamment de :

détecter des communications inhabituelles ;
identifier une adresse IP source ;
identifier une destination ;
vérifier qu'une règle de sécurité fonctionne ;
analyser une tentative de connexion ;
rechercher l'origine d'un problème réseau ;
aider à détecter certains comportements suspects.

## Conclusion
La journalisation permet de vérifier concrètement les décisions prises par le pare-feu.

Les tests ont montré que les logs permettent de retrouver :

la machine source ;
la destination ;
le protocole ;
les ports ;
la règle appliquée ;
la date et l'heure de l'événement.

L'utilisation des logs et des States constitue donc un élément essentiel pour **le dépannage réseau, la supervision et l'analyse de sécurité**.