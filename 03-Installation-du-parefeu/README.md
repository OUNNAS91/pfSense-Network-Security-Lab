# Installation du pare-feu

## Présentation
Cette étape consiste à installer pfSense CE dans une machine virtuelle VirtualBox.

pfSense sera utilisé comme pare-feu et passerelle entre le réseau externe (WAN) et le réseau interne (LAN).

## Création de la machine virtuelle
Une nouvelle machine virtuelle a été créée dans VirtualBox pour installer pfSense.

La machine virtuelle a été configurée avec des ressources adaptées aux capacités de l'ordinateur hôte.

### Configuration réseau

Deux interfaces réseau ont été configurées :

| Interface pfSense | VirtualBox | Rôle                               |
| ----------------- | ---------- | ---------------------------------- |
| WAN               | NAT        | Accès au réseau externe / Internet |
| LAN               | Host-Only  | Réseau interne du laboratoire      |

Cette séparation permet à pfSense de contrôler les communications entre les deux réseaux.

## Démarrage de l'installation
La machine virtuelle pfSense a été démarrée à partir de l'image ISO de pfSense.

L'installation permet de transformer la machine virtuelle en pare-feu réseau.

## Choix de pfSense CE
Lors de l'installation, l'édition pfSense CE a été sélectionnée.

pfSense CE est l'édition communautaire de pfSense utilisée dans notre laboratoire.

## Choix du système de fichiers
Lors de l'installation, le système de fichiers **ZFS** a été sélectionné.

ZFS est un système de fichiers qui fournit notamment des mécanismes de gestion et d'intégrité des données.

Pour ce laboratoire, l'installation utilise les paramètres recommandés proposés par l'installateur.

## Choix du disque
Le disque virtuel de la machine pfSense a été sélectionné comme destination de l'installation.

L'installation est donc effectuée directement sur le disque virtuel créé dans VirtualBox.


## Installation

Après validation des paramètres, l'installation de pfSense a été lancée.

L'installateur copie les fichiers nécessaires sur le disque virtuel et configure le système.

Une fois l'installation terminée, la machine virtuelle a été redémarrée.

## Première configuration

Après le redémarrage, pfSense affiche son interface console.

Cette console permet notamment de :

attribuer les interfaces réseau ;
modifier les adresses IP ;
réinitialiser le mot de passe administrateur ;
redémarrer pfSense ;
accéder aux principales fonctions d'administration.

## Attribution des interfaces
Les deux interfaces réseau ont été attribuées aux rôles suivants :

WAN → em0
LAN → em1


### WAN

L'interface WAN est connectée au réseau NAT de VirtualBox.

Elle obtient l'adresse :

10.0.2.15

avec la passerelle :

10.0.2.2

### LAN

L'interface LAN est connectée au réseau Host-Only.

Elle utilise l'adresse :

192.168.56.10

avec le masque :

255.255.255.0

## Accès à l'interface Web
Une fois le réseau configuré, l'administration de pfSense est réalisée depuis le réseau LAN.

L'interface Web est accessible avec :

https://192.168.56.10

Le navigateur peut afficher un avertissement concernant le certificat, car le certificat utilisé par défaut dans le laboratoire est **autosigné**.

Après avoir accepté l'exception de sécurité dans l'environnement de laboratoire, l'interface d'administration pfSense est accessible.

## Résultat
L'installation de pfSense est terminée.

La machine virtuelle dispose maintenant de :

             pfSense
                │
        ┌───────┴───────┐
        │               │
       WAN             LAN
     em0/NAT       em1/Host-Only
        │               │
   10.0.2.15       192.168.56.10
        │               │
        ▼               ▼
   Réseau externe    Réseau interne

Cette configuration constitue la base du laboratoire.

Les étapes suivantes permettront de configurer plus précisément le réseau, le DHCP, le NAT, le DNS et les règles du pare-feu.