# Bilan et recommandations

## Bilan du projet
Ce projet avait pour objectif de mettre en place un pare-feu pfSense dans un environnement virtualisé afin de comprendre son fonctionnement et de mettre en pratique plusieurs notions de réseau et de cybersécurité.

Le laboratoire a été réalisé avec :

VirtualBox ;
pfSense ;
Windows 11.

Le pare-feu pfSense a été placé entre le réseau interne et le réseau externe afin de contrôler les communications.

## Architecture réalisée
L'architecture générale du laboratoire est la suivante :

                         Internet
                            │
                            │
                         NAT
                      10.0.2.15
                            │
                            ▼
                    ┌───────────────┐
                    │    pfSense    │
                    │   Pare-feu    │
                    ├───────────────┤
                    │ WAN           │
                    │ 10.0.2.15     │
                    │               │
                    │ LAN           │
                    │ 192.168.56.10 │
                    └───────┬───────┘
                            │
                 Réseau LAN 192.168.56.0/24
                            │
                            │
                            │
                            ▼
                       Windows 11
                     192.168.56.102      

Le schéma définitif du laboratoire sera également fourni dans le dossier :

architecture

## Fonctionnement du pare-feu
pfSense joue le rôle de passerelle entre le réseau interne et Internet.

Le trafic provenant du réseau LAN passe par pfSense avant d'atteindre le réseau externe.

Le chemin général est :
Windows 11
    │
    ▼
pfSense LAN
192.168.56.10
    │
    ▼
pfSense WAN
10.0.2.15
    │
    ▼
Internet

Cela permet au pare-feu d'analyser et de filtrer les communications.

## Compétences acquises

Ce projet a permis de développer plusieurs compétences techniques.

### Réseau

Compréhension et utilisation de :

adresses IPv4 ;
réseau LAN ;
interface WAN ;
passerelle ;
DHCP ;
DNS ;
NAT ;
ports réseau ;
TCP ;
UDP ;
ICMP.

### Pare-feu

Mise en pratique de :

création de règles ;
ordre des règles ;
autorisation du trafic ;
blocage du trafic ;
filtrage par protocole ;
filtrage par port ;
filtrage par adresse IP ;
analyse des connexions.

### Cybersécurité

Mise en pratique de :

principe du moindre privilège ;
filtrage réseau ;
journalisation ;
analyse des événements ;
contrôle des communications ;
sécurisation de l'administration ;
sauvegarde de configuration ;
vérification des règles de sécurité.

### Administration système

Le projet a également permis de travailler avec :

VirtualBox ;
pfSense ;
Windows 11 ;
interfaces d'administration Web ;

## Tests réalisés
Plusieurs tests ont permis de valider le fonctionnement du laboratoire.

| Test                     | Résultat                 |
| ------------------------ | -------------------------|
| Ping vers pfSense        | ✅                       |
| Résolution DNS           | ✅                       |
| Ping vers `8.8.8.8`      | ❌ Bloqué volontairement |
| Ping vers `1.1.1.1`      | ✅                       |
| HTTP vers `neverssl.com` | ❌ Bloqué volontairement |
| HTTPS vers Google        | ✅                       |
| Blocage DNS              | ❌ Bloqué volontairement |
| DNS après correction     | ✅                       |
| Analyse des logs         | ✅                       |
| Analyse des States       | ✅                       |

Ces tests montrent que les règles configurées produisent bien les résultats attendus.

## Journalisation et analyse
L'utilisation des logs pfSense a permis d'analyser les communications.

Par exemple, le blocage ICMP vers `8.8.8.8` a été retrouvé dans les journaux.

Le blocage HTTP a également été identifié avec :

192.168.56.102
→
34.223.124.45:80

Cette analyse permet de faire le lien entre :

Règle configurée
       ↓
Trafic généré
       ↓
Résultat observé
       ↓
Log pfSense

Cette méthode est importante en administration réseau et en cybersécurité car elle permet de vérifier qu'une mesure de sécurité fonctionne réellement.

## Difficultés rencontrées
Plusieurs difficultés ont été rencontrées pendant la réalisation du laboratoire.

### Configuration réseau

La configuration de plusieurs interfaces virtuelles et de plusieurs machines demande de bien comprendre le rôle de chaque interface.

Une mauvaise configuration peut empêcher les machines de communiquer.

### Règles firewall

L'ordre des règles est particulièrement important.

Une règle placée trop haut peut bloquer un trafic qui devrait normalement être autorisé.

### DNS

Un test de blocage DNS a provoqué :

DNS request timed out

L'analyse des logs a permis d'identifier la règle responsable du blocage.

Après modification de la configuration, le DNS a de nouveau fonctionné.

### HTTPS

L'accès à l'interface pfSense a affiché un avertissement concernant la connexion privée.

Cela était lié au certificat autosigné utilisé dans le laboratoire.

Cette situation a permis de comprendre la différence entre un certificat autosigné et un certificat reconnu par une autorité de certification.

## Limites du laboratoire

Le laboratoire présente plusieurs limites.

### Ressources matérielles

Les machines virtuelles fonctionnent sur une machine disposant de ressources limitées.

Cela impose de limiter le nombre de machines virtuelles exécutées simultanément.

### Environnement virtuel

Le réseau utilisé est un environnement de test.

Il ne représente pas entièrement une infrastructure d'entreprise réelle.

### Absence de certains services

Certains services avancés n'ont pas été mis en œuvre, notamment :

VPN ;
haute disponibilité ;
IDS/IPS ;
authentification centralisée ;
SIEM ;
supervision avancée ;
segmentation VLAN avancée.

Ces fonctionnalités pourraient faire l'objet d'une évolution future du projet.

## Recommandations de sécurité

Plusieurs améliorations pourraient être apportées à une future version du laboratoire.

## Utiliser des comptes administrateurs individuels

Éviter de partager un même compte administrateur.

Chaque administrateur devrait disposer de son propre compte.

Cela permet notamment de mieux identifier les actions réalisées.

## Renforcer l'authentification

Une authentification renforcée pourrait être mise en place pour l'accès à l'administration.

Par exemple :

mots de passe robustes ;
authentification multifacteur lorsque disponible ;
limitation des accès administratifs.

## Utiliser un certificat reconnu

Le laboratoire utilise actuellement un certificat autosigné pour l'interface Web pfSense.

Dans un environnement professionnel, il serait préférable d'utiliser un certificat délivré par une autorité de certification appropriée.

Cela permettrait d'éviter l'avertissement du navigateur.

## Centraliser les logs

Dans une infrastructure professionnelle, les journaux pfSense pourraient être envoyés vers un serveur centralisé.

Ils pourraient ensuite être analysés avec une solution de supervision ou un SIEM.

Cela permettrait notamment de faciliter :

la détection d'incidents ;
la corrélation des événements ;
la recherche historique ;
la surveillance de plusieurs équipements.

## Mettre en place une supervision

Une solution de supervision pourrait être ajoutée afin de surveiller :

l'état du pare-feu ;
la disponibilité des interfaces ;
les connexions ;
les événements réseau.

## Mettre en place un IDS/IPS

Une solution IDS/IPS pourrait être ajoutée dans une future version.

### Explication simple

**IDS** signifie *Intrusion Detection System*.

Il sert à détecter des activités potentiellement suspectes.

**IPS** signifie *Intrusion Prevention System*.

Il peut en plus bloquer certaines activités détectées.

Cela permettrait d'aller plus loin que le simple filtrage par règles.

## Évolutions possibles

Le projet pourrait être amélioré progressivement avec :

Version actuelle
      │
      ├── Pare-feu
      ├── NAT
      ├── DNS
      ├── DHCP
      ├── Règles firewall
      ├── Logs
      └── Tests de sécurité
              │
              ▼
       Version avancée
              │
              ├── VLAN
              ├── VPN
              ├── IDS/IPS
              ├── Supervision
              ├── SIEM
              └── Haute disponibilité

## Apport du projet

Ce projet a permis de mieux comprendre le rôle d'un pare-feu dans une infrastructure informatique.

Il a notamment permis de comprendre que la sécurité réseau ne consiste pas uniquement à bloquer les connexions.

Il faut également :

identifier les sources et destinations ;
connaître les protocoles ;
contrôler les ports ;
analyser les journaux ;
tester les règles ;
documenter les résultats ;
sauvegarder la configuration ;
améliorer progressivement la sécurité.

## Conclusion générale
La mise en place de pfSense dans VirtualBox a permis de construire un laboratoire réseau fonctionnel et sécurisé.

Le projet a permis de mettre en pratique des notions de :

réseau ;
administration système ;
pare-feu ;
filtrage ;
DNS ;
NAT ;
journalisation ;
analyse réseau ;
cybersécurité.

Les différents tests réalisés ont permis de vérifier concrètement le fonctionnement des règles de sécurité.

Le projet constitue ainsi une base pour aller vers des architectures plus avancées intégrant notamment la segmentation réseau, les VPN, l'IDS/IPS, la supervision et la centralisation des journaux.

Cette expérience permet également de développer une méthode de travail applicable à des environnements professionnels : **configurer, tester, observer, analyser, sécuriser et documenter**.