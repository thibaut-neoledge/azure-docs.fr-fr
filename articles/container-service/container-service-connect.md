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
   ms.date="04/12/2016"
   ms.author="rogardle"/>


# Connexion à un cluster Azure Container Service

Les clusters DC/OS et Swarm déployés par le Service de conteneur Azure exposent des points de terminaison REST. Toutefois, ces points de terminaison ne sont pas ouverts au monde extérieur. Pour gérer ces points de terminaison, vous devez créer un tunnel Secure Shell (SSH). Une fois le tunnel SSH établi, vous pouvez exécuter des commandes sur les points de terminaison du cluster et afficher l’interface utilisateur du cluster via un simple navigateur exécuté sur votre propre système. Ce document vous guide dans la création d’un tunnel SSH à partir de Linux, OS X et Windows.

>[AZURE.NOTE] Vous pouvez créer une session SSH avec un système de gestion de cluster. Toutefois, nous ne recommandons pas cela. Le travail direct avec un système de gestion, vous risquez de modifier la configuration par inadvertance.

## Créer un tunnel SSH sur Linux ou OS X

La première chose que vous faites lorsque vous créez un tunnel SSH sur Linux ou OS X est de rechercher le nom DNS public des maîtres de l’équilibrage de masque. Pour ce faire, développez le groupe de ressources de manière à afficher chaque ressource. Recherchez et sélectionnez l’adresse IP publique du serveur principal. Vous accédez alors à un panneau contenant des informations sur l’adresse IP publique, et notamment le nom DNS. Enregistrez ce nom pour pouvoir l’utiliser ultérieurement. <br />


![Nom DNS public](media/pubdns.png)

Ouvrez maintenant un interpréteur de commandes et exécutez la commande suivante où :

**PORT** désigne le port du point de terminaison que vous souhaitez exposer. Pour Swarm, il s’agit de 2375. Pour DC/OS, utilisez le port 80. **USERNAME** est le nom d’utilisateur fourni lors du déploiement du cluster. **DNSPREFIX** est le préfixe DNS que vous avez fourni lors du déploiement du cluster. **REGION** est la région dans laquelle se trouve le groupe de ressources.

```bash
# ssh sample
ssh -L PORT:localhost:PORT -N [USERNAME]@[DNSPREFIX]mgmt.[REGION].cloudapp.azure.com -p 2200
```

### Tunnel DC/OS

Pour ouvrir un tunnel vers les points de terminaison liés au cluster DC/OS, exécutez une commande semblable à l’exemple suivant :

```bash
# ssh sample

ssh -L 80:localhost:80 -N azureuser@acsexamplemgmt.japaneast.cloudapp.azure.com -p 2200
```

Vous pouvez accéder maintenant les points de terminaison associés à DC/OS :

- DC/OS : `http://localhost/`
- Marathon : `http://localhost/marathon`
- Mesos : `http://localhost/mesos`

De la même façon, les API REST correspondant à chaque application sont accessibles par ce tunnel.

### Tunnel Swarm

Pour ouvrir un tunnel vers le point de terminaison Swarm, exécutez une commande ressemblant à celle qui suit :

```bash
# ssh sample

ssh -L 2375:localhost:2375 -N azureuser@acsexamplemgmt.japaneast.cloudapp.azure.com -p 2200
```

Vous pouvez maintenant définir votre variable d’environnement DOCKER\_HOST comme suit et continuer d’utiliser normalement votre interface de ligne de commande (CLI) Docker.

```bash
export DOCKER_HOST=:2375
```

## Créer un tunnel SSH sur Windows

Il existe plusieurs options de création des tunnels SSH sous Windows. Ce document décrit comment utiliser PuTTY pour cela.

Téléchargez PuTTY sur votre système Windows et exécutez l’application.

Saisissez un nom d’hôte constitué du nom d’utilisateur admin des clusters et du nom DNS public du premier serveur principal du cluster. Le **nom d’hôte** doit prendre une forme similaire à ce qui suit : `adminuser@PublicDNS`. Spécifiez le **port** 2200.

![Configuration PuTTY 1](media/putty1.png)

Sélectionnez `SSH` et `Authentication`. Ajoutez votre fichier de clé privée à des fins d’authentification.

![Configuration PuTTY 2](media/putty2.png)

Sélectionnez `Tunnels` et configurez les ports transférés suivants :
- **Port source :** vos préférences, utilisez 80 pour DC/OS et 2375 pour Swarm.
- **Destination :** utilisez localhost:80 pour DC/OS ou localhost:2375 pour Swarm.

L’exemple suivant est configuré pour DC/OS, mais Docker Swarm pourrait présenter un aspect similaire.

>[AZURE.NOTE] Le port 80 ne doit pas être en cours d’utilisation lors de la création de ce tunnel.

![Configuration PuTTY 3](media/putty3.png)

Une fois que vous avez terminé, enregistrez la configuration de la connexion et ouvrez une session PuTTY. Une fois connecté, vous pouvez configurer la configuration du port dans le journal d’événements PuTTY.

![Journal des événements PuTTY](media/putty4.png)

Lorsque vous avez configuré le tunnel de DC/OS, vous pouvez accéder au point de terminaison associé à :

- DC/OS : `http://localhost/`
- Marathon : `http://localhost/marathon`
- Mesos : `http://localhost/mesos`

Une fois que vous avez configuré le tunnel pour Docker Swarm, vous pouvez accéder au cluster Swarm via l’interface CLI Docker. Vous devez d’abord configurer une variable d’environnement Windows nommée `DOCKER_HOST` avec la valeur ` :2375`.

## Étapes suivantes

Déployer et gérer des conteneurs avec DC/OS ou Swarm.

[Utilisation d’Azure Container Service et de DC/OS](./container-service-mesos-marathon-rest.md) [Utilisation d’Azure Container Service et de Docker Swarm](./container-service-docker-swarm.md)

<!---HONumber=AcomDC_0427_2016-->