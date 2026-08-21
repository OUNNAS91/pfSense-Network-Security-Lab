# Configuration réseau

## Objectif
Cette étape consiste à configurer le réseau de pfSense afin de permettre la communication entre le réseau interne du laboratoire et le réseau externe.

pfSense possède deux interfaces :

* WAN : connexion vers le réseau externe ;
* LAN : connexion vers le réseau interne.

pfSense joue donc le rôle de **passerelle** entre les deux réseaux.

## Configuration de l'interface WAN

L'interface WAN correspond à l'interface connectée au réseau NAT de VirtualBox.

### Configuration observée

| Paramètre    | Valeur           |
| ------------ | ---------------- |
| Interface    | `em0`            |
| Nom pfSense  | WAN              |
| Adresse IPv4 | `10.0.2.15`      |
| Masque       | `255.255.255.0`  |
| Passerelle   | `10.0.2.2`       |
| DHCP         | Activé           |
| DNS fourni   | `10.185.164.112` |

L'adresse WAN est obtenue automatiquement par DHCP depuis le réseau NAT de VirtualBox.

### Explication simple
Le WAN représente le côté **extérieur** de notre pare-feu.

pfSense WAN
10.0.2.15---------------->VirtualBox NAT----------------->Internet

## Configuration de l'interface LAN

L'interface LAN correspond au réseau interne du laboratoire.

### Configuration

| Paramètre    | Valeur            |
| ------------ | ----------------- |
| Interface    | `em1`             |
| Nom pfSense  | LAN               |
| Adresse IPv4 | `192.168.56.10`   |
| Masque       | `255.255.255.0`   |
| Réseau       | `192.168.56.0/24` |

L'adresse `192.168.56.10` est utilisée pour accéder à l'interface d'administration pfSense depuis le réseau interne.

### Explication simple

Le LAN représente le côté **interne** du réseau.

Windows 11                    
192.168.56.102
       │
       ▼
     LAN
192.168.56.10
       │
       ▼
    pfSense

## Configuration du DHCP

Le service DHCP de pfSense permet d'attribuer automatiquement des informations réseau aux machines du LAN.

Le DHCP peut notamment fournir :

une adresse IP ;
un masque réseau ;
une passerelle ;
un serveur DNS.

Le poste Windows 11 a obtenu automatiquement l'adresse :

192.168.56.102

## Configuration réseau de Windows 11

Le poste Windows 11 utilisé pour les tests possède les paramètres suivants :

| Paramètre    | Valeur           |
| ------------ | ---------------- |
| Adresse IPv4 | `192.168.56.102` |
| Masque       | `255.255.255.0`  |
| Passerelle   | `192.168.56.10`  |
| Serveur DNS  | `192.168.56.10`  |

### Pourquoi la passerelle est pfSense ?

Lorsqu'une machine veut communiquer avec un réseau extérieur, elle envoie le trafic à sa **passerelle**.

Dans notre laboratoire :

Windows 11
192.168.56.102
      │
      │ « Je veux aller sur Internet »
      ▼
Passerelle
192.168.56.10
      │
      ▼
pfSense
      │
      ▼
Internet

pfSense reçoit donc le trafic et décide, grâce à ses règles, s'il doit être autorisé ou bloqué.

## NAT
Le **NAT** signifie **Network Address Translation**.

Il permet de traduire les adresses privées du réseau interne en une adresse utilisable sur le réseau externe.

Dans notre laboratoire, Windows 11 possède une adresse privée :

192.168.56.102

Cette adresse n'est pas directement utilisée sur Internet.

pfSense effectue une traduction vers son adresse WAN :

192.168.56.102
       │
       │ NAT
       ▼
10.0.2.15
       │
       ▼
Internet

### Explication simple
On peut comparer le NAT à un intermédiaire.

Plusieurs machines du réseau interne peuvent utiliser pfSense pour accéder au réseau externe sans exposer directement leurs adresses privées.

## Vérification du NAT
Le mode de NAT sortant utilisé par pfSense est :

**Automatic outbound NAT rule generation**

pfSense génère automatiquement les règles nécessaires pour permettre au réseau LAN de communiquer avec le WAN.

Une règle automatique a notamment été observée avec :

Interface : WAN
Source : 192.168.56.0/24
NAT Address : WAN address

Cela permet au trafic provenant du réseau LAN d'être traduit lorsqu'il sort vers le réseau externe.

## Configuration du DNS Resolver
pfSense utilise **Unbound** comme DNS Resolver.

Le poste Windows 11 utilise pfSense comme serveur DNS :

DNS : 192.168.56.10

Lorsqu'un utilisateur saisit :

google.com

Windows demande à pfSense de trouver l'adresse IP correspondante.

Windows 11
      │
      │ DNS
      ▼
192.168.56.10
pfSense / Unbound
      │
      ▼
Serveur DNS externe
      │
      ▼
Adresse IP de google.com

Un test a permis d'obtenir :
Nom : google.com
Adresse : 142.251.142.14

## Test de connectivité vers Internet
La connectivité externe a été vérifiée depuis Windows 11.

Un test ICMP vers une adresse publique a permis de vérifier la communication entre le poste client et Internet.

Cependant, les règles du pare-feu peuvent ensuite modifier le résultat du test.

Par exemple :

ping 8.8.8.8

peut être bloqué lorsqu'une règle ICMP spécifique est active.

## Architecture réseau obtenue
La configuration réseau finale peut être représentée ainsi :

                         INTERNET
                            │
                            │
                       VirtualBox NAT
                            │
                       10.0.2.2
                            │
                            ▼
                   ┌─────────────────┐
                   │     pfSense     │
                   │                 │
                   │ WAN             │
                   │ 10.0.2.15       │
                   │                 │
                   │ LAN             │
                   │ 192.168.56.10   │
                   └────────┬────────┘
                            │
                     192.168.56.0/24
                            │
                            ▼
                   ┌─────────────────┐
                   │    Windows 11   │
                   │    WIN11-FW     │
                   │ 192.168.56.102  │
                   └─────────────────┘

## Résultat
La configuration réseau permet maintenant à Windows 11 de :

recevoir automatiquement une adresse IP grâce au DHCP ;
utiliser pfSense comme passerelle ;
utiliser pfSense comme serveur DNS ;
accéder au réseau externe lorsque les règles du firewall l'autorisent ;
être soumis aux règles de filtrage configurées sur pfSense.

Le réseau est donc prêt pour la mise en place et les tests des règles de sécurité.