<properties
   pageTitle="Gestion de conteneur Azure Container Service via l’interface utilisateur web | Microsoft Azure"
   description="Déployez des conteneurs dans un cluster Azure Container Service à l’aide de l’interface utilisateur web Marathon."
   services="container-service"
   documentationCenter=""
   authors="neilpeterson"
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
   ms.author="nepeters"/>

# Gestion des conteneurs via l’interface utilisateur web

DC/OS offre un environnement de déploiement et de mise à l’échelle des charges de travail en cluster tout en faisant abstraction du matériel sous-jacent. DC/OS sous-tend une infrastructure qui gère la planification et l’exécution des charges de travail de calcul.

Bien qu’il existe des infrastructures pour de nombreuses charges de travail courantes, ce document décrit la création et la mise à l’échelle des déploiements de conteneurs avec Marathon. Avant d’étudier ces exemples, vous devez disposer d’un cluster DC/OS configuré dans Azure Container Service. Vous devez également disposer d’une connectivité à distance à ce cluster. Pour plus d’informations sur ces éléments, voir les articles suivants :

- [Déploiement d’un cluster Azure Container Service](container-service-deployment.md)
- [Connexion à un cluster Azure Container Service](container-service-connect.md)

## Exploration de l’interface utilisateur de DC/OS

Créez un tunnel SSH (Secure Shell), puis accédez à http://localhost/. Cela charge l’interface utilisateur web de DC/OS et affiche des informations sur le cluster, notamment les ressources utilisées, les agents actifs, et les services en cours d’exécution.

![IU DC/OS](media/dcos/dcos2.png)

## Découverte de l’interface utilisateur Marathon

Pour afficher l’interface utilisateur Marathon, accédez à http://localhost/Marathon. À partir de cet écran, vous pouvez démarrer un nouveau conteneur ou une autre application sur le cluster DC/OS d’Azure Container Service. Vous pouvez également voir des informations sur les conteneurs et les applications en cours d’exécution.

![IU Marathon](media/dcos/dcos3.png)

## Déployer un conteneur au format Docker

Pour déployer un nouveau conteneur à l’aide de Marathon, cliquez sur le bouton **Créer une application** et entrez les informations suivantes dans le formulaire :

Champ | Valeur
----------------|-----------
ID | nginx
Image | nginx
Réseau | Relié par un pont
Port de l’hôte | 80
Protocole | TCP

![Nouvelle interface utilisateur d’application : général](media/dcos/dcos4.png)

![Nouvelle interface utilisateur d’application : conteneur Docker](media/dcos/dcos5.png)

![Nouvelle interface utilisateur d’application : détection de service et de ports](media/dcos/dcos6.png)

Si vous souhaitez mapper le port du conteneur de manière statique à un port de l’agent, cela doit être effectué à l’aide du mode JSON. Pour ce faire, basculez de l’Assistant Nouvelle Application en **Mode JSON**. Ensuite, entrez le code suivant sous la section `portMappings` de la définition d’application. Cet exemple montre comment lier le port 80 du conteneur au port 80 de l’agent DC/OS. Vous pouvez basculer l’Assistant hors du mode JSON après avoir apporté cette modification.

```none
“hostPort”: 80,
```

![Nouvelle interface utilisateur d’application : exemple de port 80](media/dcos/dcos13.png)

Le cluster DC/OS est déployé avec un ensemble d’agents privés et publics. Pour que le cluster puisse accéder à des applications à partir d’Internet, vous devez déployer les applications vers un agent public. Pour ce faire, sélectionnez l’onglet **Facultatif** de l’Assistant Nouvelle Application et saisissez **slave\_public** pour les **Rôles de ressources acceptés**.

![Nouvelle interface utilisateur d’application : paramètre de l’agent public](media/dcos/dcos14.png)

Sur la page principale de Marathon, vous pouvez voir l’état du déploiement du conteneur.

![Interface utilisateur de la page principale de Marathon : état du déploiement du conteneur](media/dcos/dcos7.png)

Lorsque vous revenez à l’interface utilisateur web du DC/OS (http://localhost/)), vous verrez qu’une tâche, dans ce cas un conteneur formaté Docker, est en cours d’exécution sur le cluster DC/OS.

![IU web DC/OS : tâche en cours d’exécution sur le cluster](media/dcos/dcos8.png)

Vous pouvez également voir le nœud du cluster sur lequel la tâche est exécutée.

![IU web DC/OS : nœud du cluster de tâche](media/dcos/dcos9.png)

## Mettre vos conteneurs à l’échelle

L’IU de Marathon peut servir à augmenter ou diminuer le nombre d’instances d’un conteneur. Pour ce faire, accédez à la page de **Marathon**, sélectionnez le conteneur que vous souhaitez mettre à l’échelle, puis cliquez sur le bouton **Mettre à l’échelle**. Dans la fenêtre **Mettre à l’échelle l’application**, entrez le nombre d’instances de conteneur de votre choix et sélectionnez **Mettre à l’échelle l’application**.

![IU de Marathon : boîte de dialogue Mettre à l’échelle l’application](media/dcos/dcos10.png)

Une fois l’opération de mise à l’échelle terminée, plusieurs instances de la même tâche seront réparties sur les agents DC/OS.

![Tableau de bord de l’IU web DC/OS : propagation de tâche sur plusieurs agents](media/dcos/dcos11.png)

![IU web DC/OS : nœuds](media/dcos/dcos12.png)

## Étapes suivantes

- [Utilisation de DC/OS et de l’API Marathon](container-service-mesos-marathon-rest.md)

<!---HONumber=AcomDC_0601_2016-->