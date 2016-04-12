<properties
   pageTitle="Connexion à un cluster Azure Container Service | Microsoft Azure"
   description="Connectez-vous à un cluster Azure Container Service au moyen d’un tunnel SSH."
   services="container-service"
   documentationCenter=""
   authors="rgardler"
   manager="timlt"
   editor=""
   tags="acs, azure-container-service"
   keywords="Docker, conteneurs, micro-services, Mesos, Azure"/>

<tags
   ms.service="container-service"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/16/2016"
   ms.author="rogardle"/>


# Connexion à un cluster Azure Container Service

Les clusters Mesos et Swarm déployés par le Service de conteneur Azure exposent des points de terminaison REST. Toutefois, ces points de terminaison ne sont pas ouverts au monde extérieur. Pour gérer ces points de terminaison, vous devez créer un tunnel Secure Shell (SSH). Une fois le tunnel SSH établi, vous pouvez exécuter des commandes sur les points de terminaison du cluster et afficher l’interface utilisateur du cluster via un simple navigateur exécuté sur votre propre système. Ce document vous guide dans la création d’un tunnel SSH à partir de Linux, OS X et Windows.

>[AZURE.NOTE] Vous pouvez créer une session SSH avec un système de gestion de cluster. Toutefois, nous ne recommandons pas cela. Le travail direct avec un système de gestion, vous risquez de modifier la configuration par inadvertance.

## Créer un tunnel SSH sur Linux ou OS X

La première chose que vous faites lorsque vous créez un tunnel SSH sur Linux ou OS X est de rechercher le nom DNS public des maîtres de l’équilibrage de masque. Pour ce faire, développez le groupe de ressources de manière à afficher chaque ressource. Recherchez et sélectionnez l’adresse IP publique du serveur principal. Vous accédez alors à un panneau contenant des informations sur l’adresse IP publique, et notamment le nom DNS. Enregistrez ce nom pour pouvoir l’utiliser ultérieurement. <br />

![Nom DNS public](media/pubdns.png)

Ouvrez maintenant un interpréteur de commandes et exécutez la commande suivante où :

**PORT** désigne le port du point de terminaison que vous souhaitez exposer. Pour Swarm, il s’agit de 2375. Pour Mesos, utilisez le port 80. **USERNAME** est le nom d’utilisateur fourni lors du déploiement du cluster. **DNSPREFIX** est le préfixe DNS que vous avez fourni lors du déploiement du cluster. **REGION** est la région dans laquelle se trouve le groupe de ressources.

```
ssh -L PORT:localhost:PORT -N [USERNAME]@[DNSPREFIX]man.[REGION].cloudapp.azure.com -p 2200
```
### Tunnel Mesos

Pour ouvrir un tunnel vers les points de terminaison liés au cluster Mesos, exécutez une commande semblable à l’exemple suivant :

```
ssh -L 80:localhost:80 -N azureuser@acsexamplemgmt.japaneast.cloudapp.azure.com -p 2200
```

Vous pouvez accéder maintenant les points de terminaison associés à Mesos :

- Mesos : `http://localhost/mesos`
- Marathon : `http://localhost/marathon`
- Chronos : `http://localhost/chronos`

De la même façon, l’API REST correspondant à chaque application est accessible via ce tunnel : Marathon --`http://localhost/marathon/v2`. Pour plus d’informations sur les différentes API disponibles, consultez la documentation mésosphère pour le [Marathon API](https://mesosphere.github.io/marathon/docs/rest-api.html). Consultez la documentation [API Chronos](https://mesos.github.io/chronos/docs/api.html) et Apache pour l’[API du planificateur de Mesos](http://mesos.apache.org/documentation/latest/scheduler-http-api/).

### Tunnel Swarm

Pour ouvrir un tunnel vers le point de terminaison Swarm, exécutez une commande ressemblant à celle qui suit :

```
ssh -L 2375:localhost:2375 -N azureuser@acsexamplemgmt.japaneast.cloudapp.azure.com -p 2200
```

Vous pouvez maintenant définir votre variable d’environnement DOCKER\_HOST comme suit et continuer d’utiliser normalement votre interface de ligne de commande (CLI) Docker.

```
export DOCKER_HOST=:2375
```

## Créer un tunnel SSH sur Windows

Il existe plusieurs options de création des tunnels SSH sous Windows. Ce document décrit comment utiliser PuTTY pour cela.

Téléchargez PuTTY sur votre système Windows et exécutez l’application.

Saisissez un nom d’hôte constitué du nom d’utilisateur admin des clusters et du nom DNS public du premier serveur principal du cluster. Le **nom d’hôte** doit prendre une forme similaire à ce qui suit : `adminuser@PublicDNS`. Spécifiez le **Port** 2200.

![Configuration PuTTY 1](media/putty1.png)

Sélectionnez `SSH` et `Authentication`. Ajoutez votre fichier de clé privée à des fins d’authentification.

![Configuration PuTTY 2](media/putty2.png)

Sélectionnez `Tunnels` et configurez les ports transférés suivants :
- **Port source :** vos préférences : 80 pour Mesos et 2375 pour Swarm.
- **Destination :** utilise localhost:80 pour Mesos ou localhost:2375 pour Swarm.

L’exemple suivant est configuré pour Mesos, mais Docker Swarm pourrait présenter un aspect similaire.

>[AZURE.NOTE] Le port 80 ne doit pas être en cours d’utilisation lors de la création de ce tunnel.

![Configuration PuTTY 3](media/putty3.png)

Une fois que vous avez terminé, enregistrez la configuration de la connexion et ouvrez une session PuTTY. Une fois connecté, vous pouvez configurer la configuration du port dans le journal d’événements PuTTY.

![Journal des événements PuTTY](media/putty4.png)

Lorsque vous avez configuré le tunnel de Mesos, vous pouvez accéder au point de terminaison associé à :

- Mesos : `http://localhost/mesos`
- Marathon : `http://localhost/marathon`
- Chronos : `http://localhost/chronos`

Une fois que vous avez configuré le tunnel pour Docker Swarm, vous pouvez accéder au cluster Swarm via l’interface CLI Docker. Vous devez d’abord configurer une variable d’environnement Windows nommée `DOCKER_HOST` avec la valeur ` :2375`.

## Résolution de problèmes

### Une fois le tunnel créé, lorsque j’accède à l’URL de Mesos ou Marathon, je reçois l’erreur suivante : 502 Passerelle incorrecte...
Le moyen le plus simple pour résoudre ce problème consiste à supprimer votre cluster et le redéployer. Vous pouvez également procéder comme suit pour obliger Zookeeper à se réparer lui-même :

Connectez-vous à chaque serveur principal et procédez comme suit :

```
sudo service nginx stop
sudo service marathon stop
sudo service chronos stop
sudo service mesos-dns stop
sudo service mesos-master stop 
sudo service zookeeper stop
```

Ensuite, une fois que tous les services sont arrêtés sur l’ensemble des serveurs principaux :
```
sudo mkdir /var/lib/zookeeperbackup
sudo mv /var/lib/zookeeper/* /var/lib/zookeeperbackup
sudo service zookeeper start
sudo service mesos-master start
sudo service mesos-dns start
sudo service chronos start
sudo service marathon start
sudo service nginx start
```
Peu de temps après le redémarrage de tous les services, vous devriez être en mesure de manipuler votre cluster, comme décrit dans la documentation.

## Étapes suivantes

Déployer et gérer des conteneurs avec Mesos ou Swarm.

- [Travail avec Azure Container Service et Mesos](./container-service-mesos-marathon-rest.md)

<!---HONumber=AcomDC_0406_2016-->