# Sécurisation

## Objectif
Après l'installation et la configuration du pare-feu, plusieurs mesures de sécurité ont été vérifiées ou mises en place.

L'objectif est de réduire les risques liés :

aux accès non autorisés ;
aux connexions provenant d'Internet ;
aux erreurs de configuration ;
aux communications inutiles ;
à la perte de la configuration du pare-feu.

## Sécurisation de l'accès à l'administration
L'administration de pfSense est réalisée depuis le réseau LAN.

L'interface Web est accessible à l'adresse :

https://192.168.56.10

Le port utilisé est :

443

Le port `443` correspond au protocole **HTTPS**.

### Pourquoi HTTPS ?

HTTPS permet de chiffrer les communications entre le navigateur et l'interface d'administration.

Cela évite que les informations échangées avec l'interface Web soient transmises en clair.

## Certificat HTTPS autosigné
Lors de l'accès à l'interface pfSense, le navigateur a affiché un avertissement :

> Votre connexion n'est pas privée.

Cet avertissement est lié au certificat utilisé par pfSense.

Le certificat observé est :

GUI default
Server Certificate
Self-Signed

Il est utilisé pour sécuriser l'accès HTTPS à l'interface d'administration.

### Qu'est-ce qu'un certificat autosigné ?

Un certificat autosigné est un certificat créé et signé directement par le serveur lui-même, au lieu d'être signé par une autorité de certification reconnue par le navigateur.

Dans notre laboratoire, cela explique l'avertissement affiché par le navigateur.

Le certificat est adapté à un environnement de test, mais dans une infrastructure professionnelle, il est préférable d'utiliser un certificat correctement émis et reconnu.

## Vérification de la validité du certificat
Le certificat observé dans pfSense possède les informations suivantes :

Nom : GUI default
Type : Server Certificate
CA : No
Server : Yes

Il possède également une période de validité :

Valid From : 18 août 2026
Valid Until : 20 septembre 2027

La présence d'une date d'expiration permet de contrôler la durée de validité du certificat.

## Protection Anti-Lockout
Une **Anti-Lockout Rule** est présente sur l'interface LAN.

Elle permet de conserver l'accès à l'interface d'administration pfSense.

Elle autorise notamment l'accès à :

LAN Address
Port 443

### Pourquoi est-elle importante ?

Lorsqu'on configure un pare-feu, une erreur dans une règle peut accidentellement bloquer l'administrateur lui-même.

La règle Anti-Lockout constitue donc une protection contre ce type de situation. Elle est toujours placé au dessus des autres rèles.

## Blocage des réseaux privés côté WAN
Une règle :

Block private networks

est présente sur l'interface WAN.

Elle permet de bloquer certains trafics provenant de réseaux privés sur le côté externe.

### Pourquoi ?

Les adresses privées sont normalement utilisées à l'intérieur des réseaux locaux.

Une adresse privée reçue directement sur l'interface WAN peut donc être considérée comme anormale dans certains scénarios.

Cette protection permet de réduire certains trafics indésirables.

## Blocage des réseaux Bogon
Une autre règle présente sur le WAN est :

Block bogon networks

### Qu'est-ce qu'un réseau Bogon ?

Un réseau **bogon** correspond à une plage d'adresses IP qui ne devrait normalement pas apparaître comme source sur Internet.

Le blocage des réseaux bogon permet donc de réduire certains trafics provenant d'adresses qui ne devraient pas être utilisées sur le réseau public.

## Sécurisation par filtrage du trafic
Une partie importante de la sécurisation repose sur les règles firewall.

Dans le laboratoire, plusieurs règles ont été utilisées pour limiter les communications du poste Windows 11.

Exemples :

Bloquer HTTP WIN11-FW
Bloquer ICMP vers 8.8.8.8
Bloquer DNS WIN11-FW
Bloquer Internet WIN11-FW

Ces règles montrent qu'il est possible d'appliquer des restrictions précises selon :

l'adresse IP ;
le protocole ;
le port ;
la destination.

## Principe du moindre privilège réseau
Le principe du **moindre privilège** consiste à n'autoriser que ce qui est réellement nécessaire.

Dans un pare-feu, cela signifie notamment éviter d'autoriser inutilement tous les types de communications.

### Exemple

Au lieu de considérer tous les trafics comme équivalents :

Tout autoriser

on peut appliquer des règles plus précises :

HTTP → bloqué
ICMP vers 8.8.8.8 → bloqué
DNS → autorisé lorsque nécessaire
HTTPS → autorisé lorsque nécessaire

Cela permet de réduire la surface d'exposition du poste.

## Sécurisation du DNS
pfSense utilise **Unbound** comme DNS Resolver.

Le poste Windows 11 utilise :

192.168.56.10

comme serveur DNS.

Une règle de blocage DNS a également été testée.

Cela a permis de vérifier qu'une requête DNS pouvait être contrôlée par le pare-feu.

Lorsque la règle était active :

DNS request timed out

Après modification de la configuration, la résolution DNS fonctionnait à nouveau.

## Sauvegarde de la configuration
Une sauvegarde de la configuration pfSense a été réalisée.

La fonction de sauvegarde permet d'exporter la configuration du pare-feu sous forme de fichier XML.

Cette sauvegarde peut contenir différents éléments de configuration, notamment :

interfaces ;
règles firewall ;
NAT ;
DNS Resolver ;
DHCP ;
paramètres système.

### Pourquoi faire une sauvegarde ?

Une sauvegarde permet de restaurer rapidement la configuration en cas :

d'erreur de configuration ;
de problème système ;
de modification accidentelle ;
de réinstallation de pfSense.

La sauvegarde a été téléchargée correctement.

## Protection de la sauvegarde
pfSense propose également une option permettant de **chiffrer le fichier de configuration**.

Le chiffrement est important car la configuration peut contenir des informations sensibles.

Une sauvegarde de configuration doit donc être conservée dans un emplacement sécurisé.

### Bonne pratique

Il est recommandé :

de protéger l'accès au fichier ;
de ne pas le publier sur GitHub ;
de ne pas l'envoyer publiquement ;
de conserver plusieurs sauvegardes si nécessaire.

## Compte administrateur
Le compte administrateur utilisé pour l'administration de pfSense est :

admin

Il possède le rôle :

System Administrator

et appartient au groupe :

admins

### Bonne pratique

Dans un environnement professionnel, il est recommandé de :

utiliser des mots de passe robustes ;
limiter le nombre de comptes administrateurs ;
éviter de partager les comptes ;
utiliser des comptes individuels lorsque cela est possible ;
désactiver les comptes inutilisés ;
protéger fortement l'accès à l'administration.

## Limitation de l'administration au réseau LAN

Dans notre laboratoire, l'administration de pfSense est effectuée depuis le réseau interne.

Windows 11
192.168.56.102
       │
       ▼
LAN pfSense
192.168.56.10
       │
       ▼
Interface Web HTTPS

Cette organisation évite d'exposer directement l'interface d'administration au réseau externe.

## Importance de l'ordre des règles
La sécurisation dépend également de l'ordre des règles.

Une règle de blocage placée avant une règle d'autorisation peut empêcher le trafic de continuer.

Par exemple :

1. Bloquer HTTP
2. Autoriser LAN vers Internet

Une connexion HTTP est traitée par la première règle et est donc bloquée.

### Explication simple
L'ordre des règles peut être comparé à une liste de contrôles.

Le pare-feu vérifie les règles dans l'ordre et applique celle qui correspond au trafic.

Il faut donc toujours vérifier l'ordre après une modification.

## Mesures de sécurité mises en œuvre
Les principales mesures vérifiées dans le laboratoire sont :

utilisation de HTTPS pour l'administration ;
présence de la protection Anti-Lockout ;
filtrage du trafic LAN ;
blocage de certains ports ;
blocage de certaines destinations ;
contrôle du DNS ;
protection contre les réseaux privés côté WAN ;
protection contre les réseaux bogon ;
journalisation des événements ;
sauvegarde de la configuration ;
limitation de l'administration au réseau LAN.

## Résultat

La configuration réalisée permet de disposer d'un pare-feu capable de contrôler et de journaliser les communications du réseau interne.

Les tests ont montré que les règles de sécurité sont effectivement appliquées.

Le laboratoire permet ainsi de mettre en pratique plusieurs principes de cybersécurité :


                    Sécurité
                       │
       ┌───────────────┼───────────────┐
       │               │               │
    Filtrage        Journalisation   Sauvegarde
       │               │               │
       ▼               ▼               ▼
    Règles          Analyse          Restauration
       │
       ▼
 Contrôle du trafic

Cette configuration constitue une base de sécurité adaptée à un environnement de laboratoire.

Les limites et améliorations possibles seront présentées dans les parties suivantes.