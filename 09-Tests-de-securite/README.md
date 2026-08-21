# Tests de sécurité

## Objectif
Cette étape consiste à vérifier que les mesures de sécurité configurées sur pfSense fonctionnent réellement.

Les tests sont réalisés depuis le poste Windows 11 :

192.168.56.102

Pour chaque test, le résultat obtenu est comparé au comportement attendu.

Les journaux pfSense sont également utilisés pour confirmer les décisions du pare-feu.

## Méthode de test

Chaque test suit la méthode suivante :

1. Configurer une règle

2. Effectuer une tentative de connexion

3. Observer le résultat sur Windows 11

4. Vérifier le journal pfSense

5. Comparer le résultat avec le comportement attendu

Cette méthode permet de vérifier que le filtrage fonctionne réellement.

## Test de blocage HTTP

### Objectif

Vérifier que le poste Windows 11 ne peut pas accéder à un site utilisant HTTP lorsque la règle de blocage HTTP est active.

La règle utilisée est :

Bloquer HTTP WIN11-FW

Elle bloque le port :

TCP / 80

### Test

Depuis Windows 11, le site suivant a été utilisé :

neverssl.com

Ce site utilise HTTP et permet donc de vérifier le blocage du port 80.

### Résultat attendu

La connexion doit être bloquée.

### Résultat obtenu

Le site :

neverssl.com

a mis trop de temps à répondre.

La page n'a donc pas pu être chargée.

Le comportement correspond au résultat attendu.

### Vérification dans les logs

Le journal pfSense a enregistré :


Aug 20 15:13:50
LAN
Bloquer HTTP WIN11-FW
192.168.56.102:64151
34.223.124.45:80
TCP:S

Cette entrée confirme que la tentative de connexion a été traitée par la règle :

Bloquer HTTP WIN11-FW

### Conclusion

**Test réussi**

Le pare-feu bloque correctement les connexions HTTP du poste Windows 11.

## Test de blocage ICMP

### Objectif

Vérifier qu'une destination précise peut être bloquée en utilisant le protocole ICMP.

La règle utilisée est :

Bloquer ICMP vers 8.8.8.8

### Test

Depuis Windows 11 :

ping 8.8.8.8

### Résultat attendu

Le ping doit être bloqué.

---

### Résultat obtenu

Windows 11 a affiché :

Délai d'attente de la demande dépassé.

Le ping n'a donc pas reçu de réponse.

### Vérification dans les logs

pfSense a enregistré :

Aug 19 19:52:36
LAN
Bloquer ICMP vers 8.8.8.8
192.168.56.102
8.8.8.8
ICMP

### Conclusion

**Test réussi**

La règle permet de bloquer spécifiquement les communications ICMP vers `8.8.8.8`.

## Test de comparaison avec 1.1.1.1

Ce test permet de vérifier que le blocage précédent est bien ciblé.

### Test

Depuis Windows 11 :

ping 1.1.1.1

### Résultat attendu

Le ping vers `1.1.1.1` doit fonctionner puisque la règle précédente cible spécifiquement `8.8.8.8`.

### Résultat obtenu

Le ping vers :

1.1.1.1

a reçu une réponse.

### Conclusion

**Test réussi**

Ce test montre qu'une règle pfSense peut cibler une destination précise.

Le pare-feu ne bloque donc pas nécessairement tout le trafic ICMP.

## Test de blocage DNS

### Objectif

Vérifier que pfSense peut contrôler les requêtes DNS du poste Windows 11.

La règle utilisée est :

Bloquer DNS WIN11-FW

Elle bloque les requêtes vers :

192.168.56.10:53

### Test

Une résolution DNS a été effectuée depuis Windows 11.

Lorsque la règle était active, Windows a affiché :

DNS request timed out

### Vérification dans les logs

Le journal pfSense a enregistré :

Aug 20 13:42:24
LAN
Bloquer DNS WIN11-FW
192.168.56.102:53751
192.168.56.10:53
UDP

Cette entrée montre :

source : `192.168.56.102` ;
destination : `192.168.56.10` ;
port : `53` ;
protocole : UDP ;
règle : `Bloquer DNS WIN11-FW`.

### Correction

Après modification de la règle, le test DNS a été effectué à nouveau.

La requête DNS a alors obtenu une réponse.

### Conclusion

**Test réussi**

Le pare-feu peut contrôler les requêtes DNS et le fonctionnement du DNS a pu être restauré après modification de la règle.

## Test HTTPS

### Objectif

Vérifier que le trafic HTTPS peut fonctionner alors que le trafic HTTP est bloqué.

HTTPS utilise généralement :

TCP / 443

### Test

Le navigateur a été utilisé pour accéder à Google.

### Résultat attendu

Le site doit être accessible si le port `443` est autorisé.

## Résultat obtenu

Google s'affiche correctement.

Une connexion HTTPS a également été observée dans les States pfSense :

LAN
TCP
192.168.56.102:59143
→
51.8.71.184:443

### Conclusion

**Test réussi**

Le trafic HTTPS fonctionne correctement.

## Comparaison HTTP / HTTPS

Les tests permettent de mettre en évidence une différence importante.

| Test  | Port | Résultat  |
| ----- | ---- | ----------|
| HTTP  |   80 |  Bloqué   |
| HTTPS |  443 |  Autorisé |

### Explication simple

Le pare-feu ne regarde pas uniquement le site demandé.

Il peut également utiliser le **port** comme critère de filtrage.

Dans notre cas :


TCP / 80
    ↓
HTTP
    ↓
Bloqué


TCP / 443
    ↓
HTTPS
    ↓
Autorisé

## Vérification des journaux

Après les différents tests, les journaux pfSense ont permis de vérifier les résultats.

Les événements suivants ont notamment été observés :

| Test  | Règle observée            | Résultat                |
| ----- | ------------------------- | ----------------------- |
| HTTP  | Bloquer HTTP WIN11-FW     |  Bloqué                |
| ICMP  | Bloquer ICMP vers 8.8.8.8 |  Bloqué                |
| DNS   | Bloquer DNS WIN11-FW      |  Bloqué temporairement |
| HTTPS | Connexion TCP / 443       |  Connexion observée    |

Les logs constituent donc une preuve complémentaire du résultat obtenu sur Windows 11.

## Synthèse des tests de sécurité

| N° | Test                               | Résultat                   |
| -: | ---------------------------------- | -------------------------- |
|  1 | Blocage HTTP                       | Connexion refusée          |
|  2 | Blocage ICMP vers 8.8.8.8          | Ping expiré                |
|  3 | Ping vers 1.1.1.1                  | Réponse reçue              |
|  4 | Blocage DNS                        | Timeout                    |
|  5 | DNS après correction               | Résolution réussie         |
|  6 | HTTPS vers Google                  | Fonctionne                 |
|  7 | Vérification des logs              | Événements présents        |
|  8 | Vérification de l'ordre des règles | Règle spécifique appliquée |

## Limites des tests
Les tests réalisés sont effectués dans un environnement de laboratoire virtualisé.

Ils permettent de valider le fonctionnement du pare-feu, mais ne constituent pas un audit de sécurité complet d'une infrastructure réelle.

Des tests supplémentaires pourraient être réalisés dans un environnement professionnel, par exemple :

analyse de ports ;
tests de services exposés ;
tests d'accès depuis plusieurs réseaux ;
tests de détection d'activités suspectes ;
centralisation des logs ;
supervision de la sécurité.

## Conclusion
Les tests réalisés montrent que pfSense applique correctement les règles de sécurité configurées.

Le laboratoire a permis de démontrer concrètement qu'un pare-feu peut :

bloquer un port ;
bloquer un protocole ;
bloquer une destination précise ;
contrôler le DNS ;
autoriser certaines communications ;
suivre les connexions ;
enregistrer les événements dans les journaux.

Les résultats obtenus sont cohérents avec les règles configurées.