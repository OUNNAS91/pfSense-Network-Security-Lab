# Tests de connectivité

## Objectif
Cette étape consiste à vérifier le fonctionnement du réseau après la configuration de pfSense.

Les tests sont réalisés depuis le poste Windows 11 :

192.168.56.102

Ils permettent de vérifier :

la communication avec pfSense ;
l'accès au réseau externe ;
le fonctionnement du DNS ;
les connexions HTTP et HTTPS ;
le fonctionnement d'ICMP ;
le fonctionnement des règles de filtrage.

## Test de communication avec pfSense
Le premier test consiste à vérifier que Windows 11 peut communiquer avec l'interface LAN de pfSense.

La commande utilisée est :

ping 192.168.56.10

### Résultat

Le poste Windows 11 a reçu une réponse de :

192.168.56.10

### Conclusion

La communication entre Windows 11 et pfSense fonctionne.

Cela confirme que :

Windows 11 est connecté au LAN ;
l'adresse IP de pfSense est accessible ;
la communication LAN fonctionne.

## Test DNS

Le serveur DNS configuré sur Windows 11 est :

192.168.56.10

pfSense utilise **Unbound**, également appelé DNS Resolver, pour traiter les requêtes DNS.

### DNS expliqué simplement

DNS signifie **Domain Name System**.

Son rôle est de traduire un nom de domaine en adresse IP.

google.com------------->142.251.142.14

Sans DNS, il faudrait connaître directement l'adresse IP des serveurs pour accéder aux sites.

## Vérification de la résolution DNS
Une requête DNS a été réalisée pour :

google.com

Le résultat obtenu était :

Name : google.com
Address : 142.251.142.14

### Conclusion

La résolution DNS fonctionne.

Windows 11 utilise donc correctement pfSense comme serveur DNS.

## Test de blocage DNS
Une règle appelée :

Bloquer DNS WIN11-FW

a été activée temporairement.

Elle bloquait les requêtes DNS provenant de Windows 11 vers :

192.168.56.10:53

Le protocole utilisé était :

UDP

### Résultat

Lors du test, Windows a affiché :

DNS request timed out

Le journal pfSense a confirmé le blocage :

LAN
Bloquer DNS WIN11-FW
192.168.56.102:53751
192.168.56.10:53
UDP

### Conclusion

Le test confirme que la règle de pare-feu était bien appliquée.

Après modification de la règle, une nouvelle requête DNS a fonctionné.

## Test ICMP avec ping

### Qu'est-ce qu'ICMP ?

ICMP signifie *Internet Control Message Protocol*.

Il est notamment utilisé par la commande :

ping

Le ping permet de vérifier si une destination répond sur le réseau.

Par exemple :

Windows 11
     │
     │ ICMP
     ▼
Destination

ICMP n'utilise pas un port TCP ou UDP.

C'est important à retenir :

TCP  → ports
UDP  → ports
ICMP → pas de port

## Test de blocage vers 8.8.8.8

Une règle :

Bloquer ICMP vers 8.8.8.8

a été configurée.

La commande utilisée depuis Windows 11 était :

ping 8.8.8.8

### Résultat

Le ping a retourné :

Délai d'attente de la demande dépassé.

Le journal pfSense a également enregistré :

Bloquer ICMP vers 8.8.8.8
192.168.56.102
8.8.8.8
ICMP

### Conclusion

Le trafic ICMP vers `8.8.8.8` est correctement bloqué par pfSense.

## Test ICMP vers 1.1.1.1

Un deuxième test a été réalisé vers :

1.1.1.1

La commande utilisée était :

ping 1.1.1.1

### Résultat

Le ping a reçu une réponse.

### Conclusion

Cela permet de vérifier que le blocage précédent est spécifique à la destination `8.8.8.8`.

Le pare-feu peut donc appliquer une règle à une destination précise sans nécessairement bloquer toutes les communications ICMP.

## Test HTTP

Une règle :

Bloquer HTTP WIN11-FW

a été créée pour bloquer le port :

80

### HTTP expliqué simplement

HTTP est un protocole utilisé pour communiquer avec des serveurs Web.

Le port habituellement utilisé par HTTP est :

80


Le test a été réalisé avec :

neverssl.com

### Résultat

Lorsque la règle de blocage HTTP était active, le site ne pouvait pas être chargé.

Le journal pfSense affichait notamment :

Bloquer HTTP WIN11-FW
192.168.56.102:64151
34.223.124.45:80
TCP:S

### Conclusion

Le trafic TCP vers le port `80` est correctement bloqué.

## Test HTTPS

### HTTPS expliqué simplement

HTTPS est la version sécurisée de HTTP.

Il utilise généralement :

TCP / 443

Les communications sont chiffrées entre le navigateur et le serveur.

Un test vers Google a été réalisé.

### Résultat

Google s'affiche correctement lorsque le trafic HTTPS est autorisé.

### Conclusion

La règle de blocage HTTP n'empêche pas nécessairement le trafic HTTPS.

Cela démontre qu'il est possible de filtrer un service en fonction de son port.

## Comparaison HTTP / HTTPS

| Protocole | Port | Résultat du test |
| --------- | ---- | ---------------- |
| HTTP      |   80 |  Bloqué         |
| HTTPS     |  443 |  Fonctionne     |

### Explication simple

Le navigateur peut utiliser deux connexions différentes :

HTTP
TCP / 80
     │
     X
   Bloqué


HTTPS
TCP / 443
     │
     ▼
   Autorisé

Le pare-feu peut donc différencier les deux flux.

## Test de connectivité Internet

Plusieurs tests ont permis de confirmer que le réseau fonctionne lorsque les règles correspondantes sont autorisées.

Le chemin général est :

Windows 11
192.168.56.102
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

Le NAT permet à l'adresse privée de Windows 11 d'utiliser l'adresse WAN de pfSense pour les communications sortantes.

## Tableau récapitulatif

| Test                       | Protocole   | Destination        | Résultat     |
| -------------------------- | ----------- | ------------------ | ------------ |
| Communication avec pfSense | ICMP        | `192.168.56.10`    |  Réponse     |
| Résolution `google.com`    | DNS / UDP   | `192.168.56.10:53` |  Fonctionne  |
| DNS bloqué                 | UDP         | `192.168.56.10:53` |  Timeout     |
| DNS après correction       | UDP         | `192.168.56.10:53` |  Fonctionne  |
| Ping `8.8.8.8`             | ICMP        | `8.8.8.8`          |  Bloqué      |
| Ping `1.1.1.1`             | ICMP        | `1.1.1.1`          |  Réponse     |
| `neverssl.com`             | TCP / HTTP  | Port `80`          |  Bloqué      |
| Google                     | TCP / HTTPS | Port `443`         |  Fonctionne  |

## Conclusion

Les tests réalisés permettent de confirmer le bon fonctionnement de l'architecture réseau et des règles pfSense.

Ils montrent notamment que le pare-feu peut :

autoriser une communication ;
bloquer une communication ;
filtrer un port ;
filtrer un protocole ;
filtrer une destination précise ;
contrôler les requêtes DNS ;
journaliser les blocages.

Les résultats obtenus serviront également pour la prochaine partie consacrée à la journalisation et à l'analyse du trafic.