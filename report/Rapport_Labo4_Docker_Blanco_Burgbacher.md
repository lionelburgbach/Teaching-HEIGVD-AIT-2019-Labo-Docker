# 	Lab 04 - Docker 

## Task 0 

### M1 : 

Non, nous ne pouvons pas utiliser la solution actuelle pour de la production. En effet, il y a un "single point of failure", si le loadbalancer venait à "tombé", tout notre système ne pourrait plus fonctionner. De plus, avec la solution actuelle, nous devons changer le fichier de configuration du loadbalancer pour chaque nouveau conteneur ajouté.

### M2 :

Actuellement, pour ajouter un nouveau "webapp container", il faudrait, après l'avoir créer, l'ajouter dans le fichier de configuration du loadbalancer.

### M3 :

Pour ajouter un nouveau "webapp container", nous allons devoir effectuer différentes opérations. Nous allons commencer par créer un script sur le loadbalancer qui écoutera en continue, pour pouvoir ajouter les différents serveur qui "s'annonceront". Ensuite, lorsque nous ajoutons un nouveau "webapp container", il nous faudra faire en sorte que ce dernier "s'annonce" auprès du loadbalancer. Pour faire cela, nous allons mettre en place un "gossip protocol" qui permet une certaine "communication" entre les serveurs et le loadbalancer. Si le loadbalancer venait à tomber, nous pourrions ne référencer qu'un seul des serveurs et les autres "s'auto-référencerait" automatiquement auprès du nouveau loadbalancer (Serf est une application permettant de mettre en place cela).

### M4 :

### M5 : 

### M6 :