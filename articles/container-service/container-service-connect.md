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

Les clusters Mesos et Swarm déployés par Azure Container Service exposent des points de terminaison REST qui ne sont cependant pas ouverts au monde extérieur. Pour gérer ces points de terminaison, vous devez créer un tunnel SSH. Une fois le tunnel SSH établi, vous pouvez exécuter des commandes sur les points de terminaison du cluster et afficher l’interface utilisateur du cluster via un simple navigateur exécuté sur votre propre système. Ce document vous guide dans la création d’un tunnel SSH à partir de Linux, OS X et Windows.

> Vous pouvez créer une session SSH avec un système de gestion de cluster, bien que cela ne soit pas recommandé. En travaillant directement sur un système de gestion, vous risquez de modifier la configuration par inadvertance.

## Tunnel SSH sur Linux / OS X

Vous devez tout d’abord localiser le nom DNS public des serveurs principaux ayant fait l’objet d’un équilibrage des charges. Pour ce faire, développez le groupe de ressources de manière à afficher chaque ressource. Recherchez et sélectionnez l’adresse IP publique du serveur principal. Vous accédez alors à un panneau contenant des informations sur l’adresse IP publique, et notamment le nom DNS. Enregistrez ce nom pour pouvoir l’utiliser ultérieurement. <br />

![Connexion Putty](media/pubdns.png)

Ouvrez maintenant un interpréteur de commandes et exécutez la commande suivante où :

**PORT** désigne le port du point de terminaison que vous souhaitez exposer. Pour Swarm, il s’agit du port 2375 ; pour Mesos, utilisez le port 80. **USERNAME** est le nom d’utilisateur fourni lors du déploiement du cluster. **DNSPREFIX** est le préfixe DNS que vous avez fourni lors du déploiement du cluster. **REGION** est la région dans laquelle se trouve le groupe de ressources.

```
ssh -L PORT:localhost:PORT -N [USERNAME]@[DNSPREFIX]man.[REGION].cloudapp.azure.com -p 2200
```
### Tunnel Mesos

Pour ouvrir un tunnel vers les points de terminaison liés au cluster Mesos, exécutez une commande semblable à l’exemple suivant.

```
ssh -L 80:localhost:80 -N azureuser@acsexamplemgmt.japaneast.cloudapp.azure.com -p 2200
```

Les points de terminaison liés à Mesos sont maintenant accessibles aux adresses suivantes :

- Mesos : `http://localhost/mesos`
- Marathon : `http://localhost/marathon`
- Chronos : `http://localhost/chronos` 

De la même manière, l’API REST correspondant à chaque application est accessible via ce tunnel : Marathon - `http://localhost/marathon/v2`. Pour plus d’informations sur les différentes API disponibles, consultez la documentation Mesosphere relative à l’[API Marathon](https://mesosphere.github.io/marathon/docs/rest-api.html) et à l’[API Chronos](https://mesos.github.io/chronos/docs/api.html), ainsi que la documentation Apache relative à l’[API Mesos Scheduler](http://mesos.apache.org/documentation/latest/scheduler-http-api/)

### Tunnel Swarm

Pour ouvrir un tunnel vers le point de terminaison Swarm, exécutez une commande telle que donnée ci-dessous.

```
ssh -L 2375:localhost:2375 -N azureuser@acsexamplemgmt.japaneast.cloudapp.azure.com -p 2200
```

Vous pouvez maintenant définir votre variable d’environnement DOCKER\_HOST comme suit et continuer à utiliser normalement votre CLI Docker.

```
export DOCKER_HOST=:2375
```

## Tunnel SSH sur Windows

Plusieurs options permettent de créer des tunnels SSH sur Windows, mais ce document ne décrit en détail que l’utilisation de Putty.

Téléchargez Putty sur votre système Windows et exécutez l’application.

Saisissez un nom d’hôte constitué du nom d’utilisateur admin des clusters et du nom DNS public du premier serveur principal du cluster. Le nom d’hôte doit prendre une forme similaire à ce qui suit : `adminuser@PublicDNS`. Spécifiez le port 2200.

![Connexion Putty](media/putty1.png)

Sélectionnez `SSH` et `Authentication`, puis ajoutez votre fichier de clé privée à des fins d’authentification.

![Connexion Putty](media/putty2.png)

Sélectionnez `Tunnels` et `configure` pour configurer les ports transférés suivants :- **Port source :** vos préférences (80 pour Mesos et 2375 pour Swarm) - **Destination :** localhost:80 (pour Mesos) ou localhost:2375 (pour Swarm)

L’exemple suivant est configuré pour Mesos, mais présenterait un aspect similaire pour Docker Swarm.

> Le port 80 ne doit pas être en cours d’utilisation lors de la création de ce tunnel.

![Connexion Putty](media/putty3.png)

Lorsque vous avez terminé, enregistrez la configuration de la connexion et ouvrez une session Putty. Une fois connecté, la configuration du port est consultable dans le journal d’événements Putty.

![Connexion Putty](media/putty4.png)

Lorsque le tunnel a été configuré pour Mesos, le point de terminaison connexe est accessible aux adresses suivantes :

- Mesos : `http://localhost/mesos`
- Marathon : `http://localhost/marathon`
- Chronos : `http://localhost/chronos` 

Lorsque le tunnel a été configuré pour Docker Swarm, le cluster Swarm est accessible via la CLI Docker. Vous devez d’abord configurer une variable d’environnement Windows nommée `DOCKER_HOST` avec la valeur ` :2375`.

## Étapes suivantes
 
Déployer et gérer des conteneurs avec Mesos ou Swarm.
 
- [Utilisation d’ACS et de Mesos](./container-service-mesos-marathon-rest.md)

<!---HONumber=AcomDC_0218_2016-->