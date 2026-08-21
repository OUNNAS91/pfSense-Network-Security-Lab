# Environnement et préparation

## Présentation de l'environnement
Le laboratoire a été réalisé dans un environnement virtualisé avec Oracle VM VirtualBox.

L'objectif est de disposer d'un environnement isolé permettant de tester différentes configurations réseau et différentes règles de sécurité sans modifier directement le réseau physique de l'ordinateur.

## Matériel utilisé
Le laboratoire a été réalisé sur un ordinateur physique équipé de :

| Élément                | Configuration                 |
| ---------------------- | ----------------------------- |
| Système d'exploitation | Windows 10                    |
| Mémoire RAM            | 8 Go                          |
| Processeur             | 2 cœurs physiques             |
| Virtualisation         | Oracle VM VirtualBox          |

Les ressources disponibles étant limitées, les machines virtuelles ont été configurées avec des ressources adaptées aux capacités de l'ordinateur.

## Logiciels utilisés
Les principaux logiciels et composants utilisés sont :

Oracle VM VirtualBox : logiciel de virtualisation ;
pfSense CE : pare-feu et routeur ;
Windows 11 : poste client utilisé pour les tests ;
Navigateur Web : utilisé pour tester les connexions HTTP et HTTPS ;
Invite de commandes Windows (CMD) : utilisée pour les tests `ping` et DNS ;
Interface Web pfSense : utilisée pour l'administration et l'analyse du réseau.

## Machines virtuelles
Deux machines virtuelles principales sont utilisées dans le laboratoire.

### pfSense

pfSense constitue le cœur de l'infrastructure.

Il assure notamment les fonctions suivantes :

pare-feu ;
routage ;
NAT ;
DHCP ;
DNS Resolver ;
journalisation ;
contrôle du trafic réseau.

### Windows 11

Windows 11 représente le poste client du réseau interne.

Il est utilisé pour :

tester l'accès à Internet ;
effectuer des `ping` ;
tester le DNS ;
tester HTTP et HTTPS ;
vérifier l'efficacité des règles du pare-feu.

## Configuration réseau VirtualBox
Deux types de réseaux VirtualBox sont utilisés pour pfSense.

### WAN — NAT

La première interface réseau de pfSense est configurée en mode **NAT**.

Elle permet à pfSense d'accéder au réseau externe via le réseau de l'ordinateur hôte.

Configuration observée :

pfSense WAN
IP : 10.0.2.15
Passerelle : 10.0.2.2

Le réseau NAT permet donc à pfSense de communiquer avec Internet.

### LAN — Host-Only

La deuxième interface de pfSense est configurée sur un réseau **Host-Only**.

Elle permet de créer un réseau privé destiné aux machines virtuelles du laboratoire.

Configuration de pfSense :

pfSense LAN
IP : 192.168.56.10
Masque : 255.255.255.0

Le réseau LAN utilisé est :

192.168.56.0/24

## Configuration du poste Windows 11
Le poste Windows 11 est connecté au réseau LAN de pfSense.

Son adresse IP obtenue par DHCP est :

Adresse IPv4 : 192.168.56.102
Masque : 255.255.255.0
Passerelle : 192.168.56.10
Serveur DHCP : 192.168.56.10
Serveur DNS : 192.168.56.10

pfSense joue donc plusieurs rôles pour le poste Windows 11 :

                pfSense
             192.168.56.10
                  │
        ┌─────────┼─────────┐
        │         │         │
      DHCP      DNS      Passerelle
        │         │         │
        └─────────┼─────────┘
                  │
                  ▼
          Windows 11
       .168.56.102.192



## Vérification de la connectivité
Après la configuration du réseau, plusieurs tests ont été réalisés.

### Test de la passerelle

Depuis Windows 11 :

ping 192.168.56.10

Le test a retourné une réponse.

Cela confirme que Windows 11 peut communiquer avec l'interface LAN de pfSense.

### Test d'accès Internet

Un test vers une adresse DNS publique a également été réalisé :

ping 8.8.8.8

Le fonctionnement de cette communication a ensuite été contrôlé à travers les règles du pare-feu.

### Test DNS

Le serveur DNS fourni au poste Windows 11 est :

192.168.56.10

Une résolution de nom a permis de vérifier le fonctionnement du DNS Resolver de pfSense.

## Objectif de la préparation
Cette préparation permet de disposer d'un environnement contrôlé dans lequel les règles de sécurité peuvent être testées.

L'architecture sépare clairement :

le réseau externe représenté par le WAN ;
le réseau interne représenté par le LAN ;
le pare-feu pfSense situé entre les deux ;
le poste Windows 11 utilisé pour les tests.

Cette séparation permet d'observer concrètement le rôle d'un pare-feu dans le contrôle des communications réseau.
