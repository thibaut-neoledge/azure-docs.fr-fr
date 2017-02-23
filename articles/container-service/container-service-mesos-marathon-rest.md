---
title: "Gérer le cluster DC/OS Azure avec l’API REST Marathon | Microsoft Docs"
description: "Déployez des conteneurs vers un cluster DC/OS Azure Container Service à l’aide de l’API REST Marathon."
services: container-service
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: acs, azure-container-service
keywords: Docker, conteneurs, micro-services, Mesos, Azure
ms.assetid: c7175446-4507-4a33-a7a2-63583e5996e3
ms.service: container-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/01/2017
ms.author: danlep
translationtype: Human Translation
ms.sourcegitcommit: b2b969500d20d0c840f201ed2cf13a6f2ab38ee5
ms.openlocfilehash: 719f1ea6a6f51d4a787f0465a4bbadb1a6057a8b


---
# <a name="dcos-container-management-through-the-marathon-rest-api"></a>Gestion de conteneur DC/OS au moyen de l’API REST Marathon
DC/OS offre un environnement de déploiement et de mise à l’échelle des charges de travail en cluster tout en faisant abstraction du matériel sous-jacent. DC/OS sous-tend une infrastructure qui gère la planification et l’exécution des charges de travail de calcul. Bien qu’il existe des infrastructures pour de nombreuses charges de travail courantes, ce document décrit la création et la mise à l’échelle des déploiements de conteneurs avec Marathon. 

## <a name="prerequisites"></a>Composants requis

Avant d’étudier ces exemples, vous devez avoir un cluster DC/OS configuré dans Azure Container Service. Vous devez également disposer d’une connectivité à distance à ce cluster. Pour plus d’informations sur ces éléments, voir les articles suivants :

* [Déploiement d’un cluster Azure Container Service](container-service-deployment.md)
* [Connexion à un cluster Azure Container Service](container-service-connect.md)

## <a name="access-the-dcos-apis"></a>Accéder aux API DC/OS
Une fois que vous êtes connecté au cluster Azure Container Service, vous pouvez accéder à DC/OS et aux API REST associées via http://localhost:local-port. Les exemples cités dans ce document partent du principe que vous créez un tunnel sur le port 80. Par exemple, les points de terminaison Marathon peuvent être atteints via les URI commençant par `http://localhost/marathon/v2/`. 

Pour plus d’informations sur les différentes API, consultez la documentation Mesosphere relative à l’[API Marathon](https://mesosphere.github.io/marathon/docs/rest-api.html) et à l’[API Chronos](https://mesos.github.io/chronos/docs/api.html), ainsi que la documentation Apache relative à l’[API Mesos Scheduler](http://mesos.apache.org/documentation/latest/scheduler-http-api/).

## <a name="gather-information-from-dcos-and-marathon"></a>Collecte d’informations à partir de DC/OS et de Marathon
Avant de déployer des conteneurs vers le cluster DC/OS, vous devez recueillir certaines informations sur le cluster DC/OS, notamment le nom et l’état actuel des agents DC/OS. Pour ce faire, interrogez le point de terminaison `master/slaves` sur l’API REST DC/OS. Si tout se déroule correctement, la requête renvoie une liste d’agents DC/OS accompagnée de quelques-unes de leurs propriétés.

```bash
curl http://localhost/mesos/master/slaves
```

À présent, utilisez le point de terminaison `/apps` de Marathon pour vérifier les déploiements d’application actuels vers le cluster DC/OS. S’il s’agit d’un nouveau cluster, un tableau vide s’affiche pour les applications.

```bash
curl localhost/marathon/v2/apps

{"apps":[]}
```

## <a name="deploy-a-docker-formatted-container"></a>Déployer un conteneur au format Docker
Vous déployez les conteneurs au format Docker via Marathon à l’aide d’un fichier JSON décrivant le déploiement souhaité. L’exemple ci-après déploie le conteneur Nginx en liant le port 80 de l’agent DC/OS au port 80 du conteneur. Notez également que la propriété `acceptedResourceRoles` est définie sur `slave_public`. Cette action déploie le conteneur sur un agent du groupe public d’agents identiques.

```json
{
  "id": "nginx",
  "cpus": 0.1,
  "mem": 16.0,
  "instances": 1,
    "acceptedResourceRoles": [
    "slave_public"
  ],
  "container": {
    "type": "DOCKER",
    "docker": {
      "image": "nginx",
      "network": "BRIDGE",
      "portMappings": [
        { "containerPort": 80, "hostPort": 80, "servicePort": 9000, "protocol": "tcp" }
      ]
    }
  }
}
```

Pour déployer un conteneur au format Docker, stockez le fichier JSON dans un emplacement accessible. Ensuite, exécutez la commande suivante pour déployer le conteneur. Spécifiez le nom du fichier JSON (`marathon.json` dans cet exemple).

```bash
curl -X POST http://localhost/marathon/v2/apps -d @marathon.json -H "Content-type: application/json"
```

Le résultat ressemble à ce qui suit :

```json
{"version":"2015-11-20T18:59:00.494Z","deploymentId":"b12f8a73-f56a-4eb1-9375-4ac026d6cdec"}
```

À présent, si vous interrogez Marathon à propos des applications, cette nouvelle application apparaît dans la sortie.

```bash
curl localhost/marathon/v2/apps
```

Vous pouvez vérifier que Nginx est en cours d’exécution en envoyant une requête HTTP au nom de domaine complet du pool d’agent à l’adresse `http://<containerServiceName>agents.<region>.cloudapp.azure.com`.

## <a name="scale-your-containers"></a>Mettre vos conteneurs à l’échelle
Vous pouvez utiliser l’API Marathon pour diminuer ou augmenter la taille des déploiements des instances d’application. Dans l’exemple précédent, vous avez déployé une instance d’une application. Nous allons augmenter la taille de déploiement pour obtenir trois instances d’une application. Pour ce faire, créez un fichier JSON avec le texte JSON suivant et stockez-le dans un emplacement accessible.

```json
{ "instances": 3 }
```

Exécutez la commande suivante pour augmenter la taille des instances de l’application.

> [!NOTE]
> L’URI est http://localhost/marathon/v2/apps/, suivi de l’ID de l’application que vous souhaitez mettre à l’échelle. Si vous utilisiez l’exemple Nginx fourni ici, l’URI serait http://localhost/marathon/v2/apps/nginx.
> 
> 

```bash
curl http://localhost/marathon/v2/apps/nginx -H "Content-type: application/json" -X PUT -d @scale.json
```

Pour finir, interrogez le point de terminaison Marathon sur les applications. Vous constatez qu’il existe désormais trois conteneurs Nginx.

```bash
curl localhost/marathon/v2/apps
```

## <a name="equivalent-powershell-commands"></a>Commandes PowerShell équivalentes
Vous pouvez effectuer les mêmes opérations sur un système Windows à l’aide des commandes PowerShell.

Pour collecter des informations sur le cluster DC/OS (par exemple, le nom et l’état de l’agent), exécutez la commande suivante :

```powershell
Invoke-WebRequest -Uri http://localhost/mesos/master/slaves
```

Vous déployez les conteneurs au format Docker via Marathon à l’aide d’un fichier JSON décrivant le déploiement souhaité. L’exemple ci-après déploie le conteneur Nginx en liant le port 80 de l’agent DC/OS au port 80 du conteneur.

```json
{
  "id": "nginx",
  "cpus": 0.1,
  "mem": 16.0,
  "instances": 1,
  "container": {
    "type": "DOCKER",
    "docker": {
      "image": "nginx",
      "network": "BRIDGE",
      "portMappings": [
        { "containerPort": 80, "hostPort": 80, "servicePort": 9000, "protocol": "tcp" }
      ]
    }
  }
}
```

Pour déployer un conteneur au format Docker, stockez le fichier JSON dans un emplacement accessible. Ensuite, exécutez la commande suivante pour déployer le conteneur. Spécifiez le nom du fichier JSON (`marathon.json` dans cet exemple).

```powershell
Invoke-WebRequest -Method Post -Uri http://localhost/marathon/v2/apps -ContentType application/json -InFile 'c:\marathon.json'
```

Vous pouvez également utiliser l’API Marathon pour diminuer ou augmenter la taille des déploiements des instances d’application. Dans l’exemple précédent, vous avez déployé une instance d’une application. Nous allons augmenter la taille de déploiement pour obtenir trois instances d’une application. Pour ce faire, créez un fichier JSON avec le texte JSON suivant et stockez-le dans un emplacement accessible.

```json
{ "instances": 3 }
```

Exécutez la commande suivante pour augmenter la taille des instances de l’application :

> [!NOTE]
> L’URI est http://localhost/marathon/v2/apps/, suivi de l’ID de l’application que vous souhaitez mettre à l’échelle. Si vous utilisiez l’exemple Nginx fourni ici, l’URI serait http://localhost/marathon/v2/apps/nginx.
> 
> 

```powershell
Invoke-WebRequest -Method Put -Uri http://localhost/marathon/v2/apps/nginx -ContentType application/json -InFile 'c:\scale.json'
```

## <a name="next-steps"></a>Étapes suivantes
* [En savoir plus sur les points de terminaison HTTP Mesos](http://mesos.apache.org/documentation/latest/endpoints/)
* [En savoir plus sur l’API REST Marathon](https://mesosphere.github.io/marathon/docs/rest-api.html)




<!--HONumber=Feb17_HO1-->


