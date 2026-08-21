# Présentation du projet
Ce projet consiste à mettre en place un laboratoire de sécurité réseau basé sur pfSense, un pare-feu open source.

L'objectif est de déployer pfSense dans un environnement virtualisé avec VirtualBox, de configurer son réseau, de mettre en place des règles de filtrage et de vérifier leur fonctionnement à travers différents tests.

Le projet permet de mettre en pratique plusieurs notions d'administration système, de réseau et de cybersécurité.

## Objectifs du projet
Les principaux objectifs sont :

installer pfSense dans une machine virtuelle ;
configurer les interfaces WAN et LAN ;
mettre en place un réseau interne ;
configurer le DHCP ;
permettre l'accès à Internet depuis le réseau interne ;
configurer le NAT ;
créer des règles de filtrage réseau ;
autoriser ou bloquer certains protocoles et ports ;
utiliser les journaux du pare-feu pour analyser le trafic ;
observer les connexions réseau actives ;
sécuriser l'accès à l'administration pfSense ;
sauvegarder la configuration du pare-feu ;
réaliser des tests de sécurité ;
analyser les résultats obtenus.

## Présentation de pfSense
pfSense est une solution de pare-feu et de routage basée sur FreeBSD.

Il peut notamment être utilisé pour :

filtrer le trafic réseau ;
gérer les connexions entre plusieurs réseaux ;
effectuer du NAT ;
fournir un service DHCP ;
fournir un service DNS ;
surveiller les connexions ;
enregistrer les événements réseau ;
sécuriser l'accès à une infrastructure.

Dans ce projet, pfSense joue le rôle de pare-feu et de passerelle réseau entre Internet et notre réseau LAN.

## Environnement du laboratoire
Le laboratoire est réalisé dans un environnement virtualisé avec VirtualBox.

L'architecture utilise principalement deux machines virtuelles :

|Machine    |Rôle                |AdresseIP             
|pfSense    |Pare-feu/passerelle |WAN :`10.0.2.15`/LAN:`192.168.56.10`|
|Windows 11 |Poste client de test|`192.168.56 102`                    |

Le réseau interne utilisé pour le laboratoire est :

192.168.56.0/24
        

## Principes de sécurité étudiés
Le projet permet d'étudier le fonctionnement du filtrage réseau.

Une règle de pare-feu peut notamment prendre en compte :

l'adresse IP source ;
l'adresse IP destination ;
le protocole ;
le port source ;
le port destination ;
l'interface réseau ;
l'action à effectuer.

L'action peut notamment être :

Pass : autoriser le trafic ;
Block : bloquer silencieusement le trafic ;
Reject : bloquer le trafic en informant l'émetteur.

## Protocoles étudiés
Plusieurs protocoles ont été utilisés pendant les tests :

### TCP

TCP est utilisé pour établir des connexions entre deux machines.

Exemples :

HTTP : port `80`
HTTPS : port `443`

### UDP

UDP permet notamment de transporter des requêtes DNS.

Exemple :

DNS : port `53`

### ICMP

ICMP est notamment utilisé par la commande `ping`.

Il permet de vérifier si une machine est joignable sur le réseau.

## Tests réalisés
Plusieurs scénarios de sécurité ont été réalisés afin de vérifier le fonctionnement des règles pfSense.

### Blocage HTTP
Une règle a été créée afin de bloquer les connexions HTTP provenant du poste Windows 11.

Le site `neverssl.com` ne pouvait alors plus être chargé.

Le blocage a également été confirmé dans les journaux pfSense.

### Autorisation HTTPS

Les connexions HTTPS restent autorisées.

### Blocage ICMP

Une règle a été utilisée pour bloquer les requêtes ICMP vers `8.8.8.8`.

Le `ping` a retourné un délai d'attente et le blocage a été visible dans les logs.

À l'inverse, le ping vers `1.1.1.1` a fonctionné.

### DNS

Le fonctionnement du DNS a également été testé avec pfSense comme serveur DNS du poste Windows 11.

Une règle de blocage DNS a été testée puis corrigée afin de permettre à nouveau les requêtes DNS nécessaires au fonctionnement du poste.

## Journalisation et analyse
Les journaux du pare-feu ont été utilisés afin de vérifier les règles et d'analyser les connexions.

Les informations observées comprennent notamment :

l'interface ;
le protocole ;
l'adresse IP source ;
le port source ;
l'adresse IP destination ;
le port destination ;
l'action de la règle.

Les States de pfSense ont également été observés afin de suivre les connexions réseau actives.

## Sécurisation
Plusieurs éléments de sécurisation ont été vérifiés :

accès à l'administration en HTTPS ;
présence de l'Anti-Lockout Rule ;
absence de règle d'autorisation d'administration sur le WAN ;
vérification des services nécessaires ;
utilisation d'un compte administrateur ;
sauvegarde de la configuration pfSense.

La configuration a été sauvegardée depuis Diagnostics puis Backup & Restore.

## Limites du laboratoire
Ce projet est réalisé dans un environnement virtualisé et pédagogique.

Il ne représente donc pas une infrastructure d'entreprise complète.

Certaines protections supplémentaires pourraient être mises en œuvre dans un environnement réel, notamment :

une infrastructure de certificats ;
 authentification renforcée ;
une gestion centralisée des journaux ;
des sauvegardes automatisées ;
plusieurs comptes administrateurs individuels ;
une architecture réseau plus complexe.

## Résultat attendu
À la fin du projet, l'environnement doit permettre de démontrer qu'un pare-feu peut :

contrôler les flux réseau ;
autoriser certains services ;
bloquer certains services ;
filtrer selon les ports et protocoles ;
contrôler les destinations ;
journaliser les événements ;
analyser les connexions ;
sécuriser son interface d'administration ;
sauvegarder sa configuration.

Ce laboratoire constitue une mise en pratique des notions fondamentales de réseau, administration système et cybersécurité.
