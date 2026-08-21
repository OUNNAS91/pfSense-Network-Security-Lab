# Projet Firewall pfSense — Conception, filtrage et sécurisation d'un réseau

## Présentation
Ce projet consiste à mettre en place un **pare-feu pfSense** dans un environnement virtualisé afin de comprendre son fonctionnement et de mettre en pratique des notions de **réseau, administration système et cybersécurité**.

Le laboratoire permet de contrôler les communications entre un poste Windows 11 et Internet à travers pfSense.

L'objectif n'est pas seulement d'installer le pare-feu, mais également de :

configurer le réseau ;
créer des règles de filtrage ;
contrôler les communications ;
tester les règles ;
analyser les journaux ;
sécuriser l'administration ;
sauvegarder la configuration ;
documenter les résultats.

## Objectifs du projet
Les principaux objectifs sont :

comprendre le rôle d'un pare-feu ;
comprendre la différence entre WAN et LAN ;
configurer pfSense dans VirtualBox ;
configurer les interfaces réseau ;
mettre en place des règles de filtrage ;
comprendre les protocoles TCP, UDP et ICMP ;
comprendre le fonctionnement du DNS ;
contrôler les ports réseau ;
analyser les logs du pare-feu ;
analyser les States pfSense ;
effectuer des tests de sécurité ;
appliquer des principes de sécurisation réseau.

## Environnement du laboratoire

### Virtualisation

Le laboratoire utilise :

***VirtualBox**

### Pare-feu

**pfSense CE**

### Poste client

**Windows 11**

## Architecture réseau
Le fichier source de l'architecture est disponible ici :

(architecture/architecture-reseau.drawio)

## Plan d'adressage

| Équipement | Interface | Adresse IP       | Rôle                         |
| ---------- | --------- | ---------------- | ---------------------------- |
| pfSense    | WAN       | `10.0.2.15`      | Accès vers Internet via NAT  |
| pfSense    | LAN       | `192.168.56.10`  | Passerelle du réseau interne |
| Windows 11 | LAN       | `192.168.56.102` | Poste client                 |

Réseau LAN :

192.168.56.0/24

## Fonctionnement du pare-feu
pfSense se trouve entre le réseau externe et le poste Windows 11.

Le trafic suit le chemin :

Windows 11
     │
     ▼
192.168.56.10
     │
     ▼
   pfSense
     │
     ▼
 10.0.2.15
     │
     ▼
 Internet

pfSense analyse le trafic et applique les règles configurées.

Une communication peut être :

autorisée ;
bloquée ;
journalisée.

## Principales règles de sécurité

Plusieurs règles ont été configurées et testées.

### Blocage HTTP

Le trafic HTTP utilise généralement le port :

TCP / 80

Une règle permet de bloquer les connexions HTTP provenant du poste Windows 11.

Règle :

Bloquer HTTP WIN11-FW

### Blocage ICMP vers 8.8.8.8

Une règle spécifique bloque les requêtes ICMP vers :

8.8.8.8

Règle :

Bloquer ICMP vers 8.8.8.8

Le test :

ping 8.8.8.8

a produit un délai d'attente.

### Autorisation ICMP vers 1.1.1.1

Le test :

ping 1.1.1.1

a reçu une réponse.

Cela montre que le blocage de `8.8.8.8` est ciblé et ne bloque pas nécessairement toutes les communications ICMP.

### Contrôle du DNS

Le DNS utilise généralement le port :

UDP / 53

Le serveur DNS utilisé dans le laboratoire est pfSense :

192.168.56.10

Une règle de blocage DNS a été testée.

Lorsque cette règle était active, Windows 11 a retourné :

DNS request timed out

Après modification de la règle, la résolution DNS a de nouveau fonctionné.

### Autorisation HTTPS

HTTPS utilise généralement :

TCP / 443

Une connexion HTTPS vers Google a été testée avec succès.

Cela montre qu'il est possible de bloquer certains protocoles tout en conservant l'accès aux services nécessaires.

## Journalisation
Les journaux pfSense ont été utilisés afin de vérifier les décisions du pare-feu.

Ils permettent notamment d'observer :

l'heure de l'événement ;
l'interface utilisée ;
l'adresse IP source ;
l'adresse IP destination ;
le protocole ;
les ports ;
la règle appliquée.

Exemple de blocage ICMP :

LAN
192.168.56.102
→
8.8.8.8
ICMP
Bloquer ICMP vers 8.8.8.8

Exemple de blocage HTTP :

LAN
192.168.56.102:64151
→
34.223.124.45:80
TCP
Bloquer HTTP WIN11-FW

Ces journaux permettent de confirmer que les règles fonctionnent réellement.

## Sécurisation

Plusieurs mesures de sécurité ont été étudiées ou mises en place :

administration via HTTPS ;
protection Anti-Lockout ;
filtrage du trafic ;
blocage de certaines communications ;
contrôle du DNS ;
blocage des réseaux privés côté WAN ;
blocage des réseaux bogon ;
journalisation ;
sauvegarde de la configuration ;
vérification des services ;
limitation de l'administration au réseau LAN.

Le certificat HTTPS utilisé dans le laboratoire est un **certificat autosigné**. Le navigateur peut donc afficher un avertissement concernant la connexion privée.

Dans un environnement professionnel, un certificat reconnu par une autorité de certification serait préférable.

## Sauvegarde

Une sauvegarde de la configuration pfSense a été réalisée avec succès.

La sauvegarde permet de restaurer la configuration du pare-feu en cas de problème ou d'erreur de configuration.

La configuration sauvegardée doit être conservée dans un emplacement sécurisé et ne doit pas être publiée sur GitHub.

## Notions étudiées
Ce projet a permis de travailler sur plusieurs notions techniques.

### Réseau

LAN ;
WAN ;
NAT ;
DHCP ;
DNS ;
TCP ;
UDP ;
ICMP ;
ports réseau.

### Firewall

règles de filtrage ;
ordre des règles ;
autorisation ;
blocage ;
suivi des connexions ;
journalisation.

### Cybersécurité

filtrage réseau ;
principe du moindre privilège ;
analyse des logs ;
sécurisation de l'administration ;
contrôle des communications ;
sauvegarde ;
tests de sécurité.

## Limites du laboratoire
Ce projet a été réalisé dans un environnement virtualisé et pédagogique.

Il ne représente pas entièrement une infrastructure d'entreprise réelle.

Certaines fonctionnalités avancées n'ont pas été mises en œuvre, notamment :

VPN ;
IDS/IPS ;
SIEM ;
supervision centralisée ;
haute disponibilité.

Ces éléments constituent des pistes d'amélioration pour une future évolution du laboratoire.

## Compétences développées
Ce projet a permis de développer des compétences en :

administration réseau ;
administration système ;
configuration de pare-feu ;
virtualisation ;
analyse réseau ;
journalisation ;
cybersécurité ;
documentation technique.

## Conclusion
Ce laboratoire a permis de mettre en place un pare-feu pfSense fonctionnel dans VirtualBox et de contrôler les communications d'un poste Windows 11.

Les différents tests ont permis de vérifier concrètement le fonctionnement des règles de filtrage.

Le projet a également permis de comprendre l'importance de la journalisation et de l'analyse des événements pour vérifier les décisions du pare-feu.

Ce projet constitue une première base pratique en **administration réseau et cybersécurité**, avec des possibilités d'évolution vers des architectures plus avancées.