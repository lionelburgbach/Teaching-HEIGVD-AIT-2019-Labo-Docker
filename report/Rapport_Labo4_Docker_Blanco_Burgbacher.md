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

### Installation 

Après avoir fait docker-compose up --build on a bien le HAProxy qui se lance et les deux serveurs. On peut vérifier ceci grâce à docker ps.

<img height="75" src="/home/guillaume/Bureau/AIT/Labo4/Teaching-HEIGVD-AIT-2019-Labo-Docker/report/Images/Task_0_Installation_docker_ps.png"  />

Et ensuite docker network ls 

<img src="/home/guillaume/Bureau/AIT/Labo4/Teaching-HEIGVD-AIT-2019-Labo-Docker/report/Images/Task_0_Installation_docker_network.png"  />



Ensuite si on se rend à l'adresse 192.168.42.42, on a bien la "HTTP request".

<img src="/home/guillaume/Bureau/AIT/Labo4/Teaching-HEIGVD-AIT-2019-Labo-Docker/report/Images/Task_0_192.168.42.42.png"  />



#### **Deliverables 0**

Screenshot de l'adresse  `http://192.168.42.42:1936`

<img src="/home/guillaume/Bureau/AIT/Labo4/Teaching-HEIGVD-AIT-2019-Labo-Docker/report/Images/Task_0_HAProxy.png"  />

L'url de notre repository est le suivant : https://github.com/lionelburgbach/Teaching-HEIGVD-AIT-2019-Labo-Docker

## Task 1



