# 	Lab 04 - Docker 

## Introduction

Dans ce laboratoire nous allons faire en sorte d'améliorer ce que nous avions fait dans le laboratoire numéro 3. En effet, dans le laboratoire 3, nous avions déjà un loadbalancer qui distribuait le traffic entre deux applications web. Nous allons, maintenant, faire en sorte que l'ajout (et la suppression) d'une nouvelle application web se fasse dynamiquement. Que nous n'ayons pas à aller changer la configuration du loadbalancer manuellement. Pour cela nous allons faire en sorte que les machines, le loadbalancer et les web apps puissent "communiquer" entre elles au travers d'un "gossip protocol" qui va permettre de savoir qui est disponible et qui vient se rajouter au cluster de machine. Le but est que cela soit le plus dynamique et le plus facile possible.

## Table des matières 

[TOC]

## Task 0 - Identify issues and install the tools

### M1 : 

Non, nous ne pouvons pas utiliser la solution actuelle pour de la production. En effet, il y a un "single point of failure", si le loadbalancer venait à "tombé", tout notre système ne pourrait plus fonctionner. De plus, avec la solution actuelle, nous devons changer le fichier de configuration du loadbalancer pour chaque nouveau conteneur ajouté.

### M2 :

Actuellement, pour ajouter un nouveau "webapp container", il faudrait, après l'avoir créer, l'ajouter dans le fichier de configuration du loadbalancer.

### M3 :

Pour ajouter un nouveau "webapp container", nous allons devoir effectuer différentes opérations. Nous allons commencer par créer un script sur le loadbalancer qui écoutera en continue, pour pouvoir ajouter les différents serveur qui "s'annonceront". Ensuite, lorsque nous ajoutons un nouveau "webapp container", il nous faudra faire en sorte que ce dernier "s'annonce" auprès du loadbalancer. Pour faire cela, nous allons mettre en place un "gossip protocol" qui permet une certaine "communication" entre les serveurs et le loadbalancer. Si le loadbalancer venait à tomber, nous pourrions ne référencer qu'un seul des serveurs et les autres "s'auto-référencerait" automatiquement auprès du nouveau loadbalancer (Serf est une application permettant de mettre en place cela).

### M4 : <span style='color:red'>REPONDRE !!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!</span>

### M5 : <span style='color:red'>REPONDRE !!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!</span>

### M6 : <span style='color:red'>REPONDRE !!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!</span>

### Installation 

Après avoir fait docker-compose up --build on a bien le HAProxy qui se lance et les deux serveurs. On peut vérifier ceci grâce à docker ps.

<img height="75" src="/home/guillaume/Bureau/AIT/Labo4/Teaching-HEIGVD-AIT-2019-Labo-Docker/report/Images/Task_0_Installation_docker_ps.png"  />

Et ensuite docker network ls 

<img src="/home/guillaume/Bureau/AIT/Labo4/Teaching-HEIGVD-AIT-2019-Labo-Docker/report/Images/Task_0_Installation_docker_network.png"  />



Ensuite si on se rend à l'adresse 192.168.42.42, on a bien la "HTTP request".

<img src="https://github.com/lionelburgbach/Teaching-HEIGVD-AIT-2019-Labo-Docker/blob/master/report/Images/Task_0_192.168.42.42.png"  />



#### **Deliverables 0**

1. Screenshot de l'adresse  `http://192.168.42.42:1936`

<img src="/home/guillaume/Bureau/AIT/Labo4/Teaching-HEIGVD-AIT-2019-Labo-Docker/report/Images/Task_0_HAProxy.png"  />

2. L'url de notre repository est le suivant : https://github.com/lionelburgbach/Teaching-HEIGVD-AIT-2019-Labo-Docker

## Task 1 - Add a process supervisor to run several processes

On "installe" le système init s6 en modifiant le Docker. Ensuite on crée nos images (imgha et imgweb), grâce à la commande `docker build -t <imageName>  .` . On les lance et on vérifie avec `docker ps`

<img src="/home/guillaume/Bureau/AIT/Labo4/Teaching-HEIGVD-AIT-2019-Labo-Docker/report/Images/Task_1_docker_ps.png"  />

Ensuite on configure le s6 comme notre processus principal avec la commande `ENTRYPOINT ["/init"]` qu'on ajoute au docker. On voit qu'à l'adresse **192.168.42.42** il n'y a plus rien car on a remplacé notre application par le superviseur. On va donc devoir créer un script pour s6, pour que notre application soit relancé automatiquement. On crée donc un dossier service dans lequel on va mettre notre "run" script. On doit modifier son "hashbang" et retourner modifier les Docker du ha et de webapp pour que le script soit copier dans l'image docker. Après avoir fait cela, on relance notre docker compose. 

#### **Deliverables 1**

**1. Screenshot de l'adresse**  `http://192.168.42.42:1936`

<img src="/home/guillaume/Bureau/AIT/Labo4/Teaching-HEIGVD-AIT-2019-Labo-Docker/report/Images/Task_1_HAProxy.png"  />

**2. Describe your difficulties for this task and your understanding of what is happening during this task.**

Nous n'avons pas eu de difficultés particulière pour cette partie du laboratoire. Nous avons eu un problème avec les commandes proposé pour la copie mais nous avons tous simplement fait la copie à la main et un chmod sur la copie.

Nous avons compris que par principe docker ne permet d'avoir qu'un seul processus par container. Ce qui implique que si aucun processus ne tourne sur le container s'arrête automatiquement. Le problème c'est que les applications serveurs comme Apache ou NodeJs (dans notre cas) vont lancer des processus en background (des deamons) et pour docker c'est comme s'il n'y avait pas de processus qui tournait, il va donc arrêter le container.

Pour résoudre ce problème nous allons devoir faire en sorte que notre processus de premier plan ne crée pas un fork en arrière plan (dans un deamon). Nous allons pour cela devoir utiliser un **init system**. Un **init system** permet de gérer les processus et les deamons. Pour ce laboratoire, nous allons utiliser s6 qui est léger et assez facile à utiliser. C'est aussi ce programme qui va nous permettre d'avoir plusieurs processus dans un seul container.

Un des points soulevé dans la donné et que cela ne respecte plus vraiment la philosophie de docker. La réponse de s6 à cette "accusation" est qu'il ne sont pas d’accord. Selon eux un container ne dois pas avoir qu'un seul processus mais qu'une seule fonction (Exemple: un service de chat) et que fonction s'arrête le container doit faire pareil. 

## Task 2 - Add a tool to manage membership in the web server cluster

Dans cette partie, on suit les différentes étapes pour installer Serf (outil qui va nous permettre d'ajouter et de supprimer dynamiquement nos web server). Création des différents dossier et des script de lancement. Modification des Dockerfile et exposition du port Serf. 

Nous avons lancé directement avec le docker-compose nous n'avons donc pas eu les problèmes pour ping les machines. Les autres commandes  ( docker run -d -p 80:80 -p 1936:1936 -p 9999:9999 --network brige --link s1 --link s2 --name ha <imageName> ET docker run -d --network heig --name s1 <imageName>) n'ont malheureusement pas fonctionné (problème avec le network bridge).

#### **Deliverables 2**

1. ##### Logs

   Grâce aux commandes suivantes : `docker logs ha > logHa.txt` , `docker logs s1 > logS1.txt` et `docker logs s2 > logS2.txt`, on peut créer les différents fichier de logs de nos container.
   Vous pouvez retrouver les différents fichiers de logs ici : [Logs - Task2](https://github.com/lionelburgbach/Teaching-HEIGVD-AIT-2019-Labo-Docker/tree/master/logs/Task_2)

2. ##### Existing problem

   Nous n'avons pas eu le problème car nous avons utilisé docker-compose (qui lance le HAProxy en premier) comme cité précedement mais si on avait pu lancé les choses les unes après les autres (dans un autre ordre avec s1 en premier par exemple), on aurait eu un problème si on avait lancé les serveur web app avant le HAProxy. En effet, on crée un cluster de machine mais dans notre cas, on lie les web apps au ha, donc s'il n'existe pas on va avoir une erreur. 

3. ##### How Serf is working 

   Le but de Serf est de créer un cluster de machine qui peuvent communiquer entre elles et avoir conscience les unes des autres. Pour cela, il se base sur un gossip protocol (protocole de ragots). Il va maintenir une liste des "membres" de sont cluster et faire en sorte qu'ils s'annoncent lorsque ils "arrivent" ou "partent". Dans notre cas le loadbalancer aura la liste des web servers qui sont online ou offline. Serf détecte aussi si un des nœuds de sont  cluster et va notifier le reste du cluster, il permet aussi de lancer des scripts spécifique pour ces cas et essayera même de voir si le nœuds et encore défaillant en essayant de s'y reconnecter (source : [ici](https://www.serf.io/intro/index.html)) .
   
   [ici](https://www.serf.io/docs/internals/gossip.html) on peut trouver des informations plus poussée des principes sur lesquels il se base.
   
   En se baladant sur internet, on peut trouver pas mal d'outils permettant de faire la même chose que Serf. Ici : [Alternative à Serf](https://stackshare.io/serf/alternatives) vous pourrez retrouver différentes alternatives à Serf. Les plus populaires sont Consul et Zookeeper. D’ailleurs Serf eux même parle de leur concurrent ([Ici](https://www.serf.io/intro/vs-other-sw.html)) .

## Task 3 - React to membership changes

Dans cette partie, on crée les scripts qui vont permettre aux membres (les serveurs web app) de se connecter. Après avoir créé les scripts on modifie le Dockerfile de ha pour copier les scripts dans notre docker.

Nous avons encore eu des pour run les containeur avec les commandes docker, nous avons de nouveau du utiliser docker-compose.

#### **Deliverables 3**

1. ##### Logs de docker

   Grâce aux même commande que pour le "delivrables 2", on peut récupérer les logs de ha, s1 et s2.

   Vous pouvez retrouver les différents fichiers de logs ici : [Logs - Task3 - Docker](https://github.com/lionelburgbach/Teaching-HEIGVD-AIT-2019-Labo-Docker/tree/master/logs/Task_3/Logs_Docker)

2. ##### Logs de Serf

   Pour récupérer les logs de Serf on lance un shell à l'intérieur du container et on fait la commande : cat /var/log/serf.log

   Vous pouvez retrouver les logs de Serf ici :  [Logs - Task3 - Serf](https://github.com/lionelburgbach/Teaching-HEIGVD-AIT-2019-Labo-Docker/tree/master/logs/Task_3/Logs_Serf)

   

## Task 4 - Use a template engine to easily generate configuration files

Dans cette étape, on crée un template pour ajouter dynamiquement de nouveaux serveurs sans avoir à toucher à la configuration. On modifie le Dockerfile pour installer NodeJS et un outil pour extraire l'archive NodeJS (xz-utils). Ensuite on crée un template tous simple dans notre configuration et on fait en sorte qu'il soit copier dans le container.

#### **Deliverables 4**

1. <span style='color:red'>REPONDRE !!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!</span>

2. <span style='color:red'>REPONDRE !!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!</span>

3. **Logs**

   Vous pourrez trouver les différents log demandé à l'étape 3 (logs des configuration ha, s1 et s2 / docker ps & docker inspect) au lieu suivant : [Logs - Task4](https://github.com/lionelburgbach/Teaching-HEIGVD-AIT-2019-Labo-Docker/tree/master/logs/Task_4)

4. <span style='color:red'>REPONDRE !!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!</span>

## Task 5 - Generate a new load balancer configuration when membership changes

Dans cette partie on va modifier nos scripts pour que la configuration du HAProxy soit modifié dynamiquement à chaque fois qu'une web app rejoint ou quitte le cluster.

Cette fois on a pu démarrer indépendamment les serveurs et voir dans les logs qu'ils s'ajoutaient au fur et à mesure. Par contre si on les démarre comme ça et qu'on va regarder la configuration depuis le navigateur, ils sont soit considéré comme "done" ou alors il ne s'affiche même pas.

#### **Deliverables 5**

1. **Logs configuration & docker**

   Vous trouverez respectivement les logs des configurations [ici](https://github.com/lionelburgbach/Teaching-HEIGVD-AIT-2019-Labo-Docker/tree/master/logs/Task_5/LogCFG) et les logs docker (ps et inspect) [ici](https://github.com/lionelburgbach/Teaching-HEIGVD-AIT-2019-Labo-Docker/tree/master/logs/Task_5/LogDocker)

2. **Liste des nœuds** 

   Vous trouvez la liste des nœuds [ici](https://github.com/lionelburgbach/Teaching-HEIGVD-AIT-2019-Labo-Docker/blob/master/logs/Task_5/ListOfNode)

3. **Liste des nœuds, configuration HA & docker ps après arrêt de S1** 

   Vous trouverez la liste des nœuds, la configuration du HAProxy ainsi que le docker ps après avoir arrêté le serveur S1 [ici](https://github.com/lionelburgbach/Teaching-HEIGVD-AIT-2019-Labo-Docker/tree/master/logs/Task_5/AfterStopS1)

## Task 6 - Make the load balancer automatically reload the new configuration

Finalement dans cette étape, on va modifier le script de "lancement" de notre HAProxy pour que ce dernier garde toujours la configuration à jour.

#### **Deliverables 6**

1. 

## Difficultés 

## Conclusion