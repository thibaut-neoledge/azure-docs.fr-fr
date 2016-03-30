<properties
   pageTitle="Gestion des conteneurs ACS avec l’API REST | Microsoft Azure"
   description="Déployez des conteneurs vers un cluster Mesos ACS (Azure Container Service) à l’aide de l’API REST Marathon."
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
   
# Gestion des conteneurs avec l’API REST

Mesos offre un environnement de déploiement et de mise à l’échelle de la charge de travail en cluster tout en faisant abstraction du matériel sous-jacent. Mesos repose sur des infrastructures qui gèrent la planification et l’exécution de charges de calcul. Bien qu’il existe des infrastructures pour de nombreuses charges de travail courantes, ce document décrit en détail la création et la mise à l’échelle des déploiements de conteneurs avec Marathon.

Avant de pouvoir suivre ces exemples, vous devez disposer d’un cluster Mesos configuré dans ACS et connecté à distance. Pour plus d’informations sur ces éléments, consultez les articles suivants :

- [Déploiement d’un cluster Azure Container Service](./container-service-deployment.md) 
- [Connexion à un cluster ACS](./container-service-connect.md)


Une fois connectées au cluster ACS, les API Mesos et REST associées sont accessibles via http://localhost:local-port. Les exemples cités dans ce document partent du principe que vous créez un tunnel sur le port 80. Par exemple, le point de terminaison Marathon peut être joint à `http://localhost/marathon/v2/`. Pour plus d’informations sur les différentes API, consultez la documentation Mesosphere relative à l’[API Marathon](https://mesosphere.github.io/marathon/docs/rest-api.html) et à l’[API Chronos](https://mesos.github.io/chronos/docs/api.html), ainsi que la documentation Apache relative à l’[API Mesos Scheduler](http://mesos.apache.org/documentation/latest/scheduler-http-api/).

## Collecte d’informations à partir de Mesos et de Marathon

Avant de déployer des conteneurs vers le cluster Mesos, vous devez recueillir certaines informations sur le cluster Mesos, notamment le nom et l’état actuel des agents Mesos. Pour ce faire, interrogez le point de terminaison `master/slaves` sur un serveur principal Mesos. Si tout se passe bien, vous accéderez à une liste d’agents Mesos accompagnée de quelques-unes de leurs propriétés.

```bash
curl http://localhost/master/slaves
```

À présent, utilisez le point de terminaison `/apps` de Marathon pour vérifier les déploiements d’application actuels vers le cluster Mesos. S’il s’agit d’un nouveau cluster, un tableau vide s’affichera pour les applications.

```
curl localhost/marathon/v2/apps

{"apps":[]}
```

## Déploiement d’un conteneur Docker formaté

Les conteneurs Docker formaté sont déployés via Marathon à l’aide d’un fichier json décrivant le déploiement souhaité. L’exemple suivant permet de déployer le conteneur nginx en liant le port 80 de l’agent Mesos au port 80 du conteneur.

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

Pour déployer un conteneur formaté Docker, créez votre propre fichier json ou utilisez l’exemple présenté dans la page de [démonstration d’Azure ACS](https://raw.githubusercontent.com/rgardler/AzureDevTestDeploy/master/marathon/marathon.json), et stockez-le dans un emplacement accessible. Ensuite, exécutez la commande suivante en spécifiant le nom du fichier json pour déployer le conteneur.

```
curl -X POST http://localhost/marathon/v2/groups -d @marathon.json -H "Content-type: application/json"
```

Vous devez obtenir un résultat semblable à ce qui suit :

```json
{"version":"2015-11-20T18:59:00.494Z","deploymentId":"b12f8a73-f56a-4eb1-9375-4ac026d6cdec"}
```

À présent, si vous interrogez Marathon à propos des applications, cette nouvelle application s’affichera dans la sortie.

```
curl localhost/marathon/v2/apps
```

## Mettre vos conteneurs à niveau

L’API Marathon peut également servir à diminuer ou augmenter la taille des déploiements d’applications. Dans l’exemple précédent, nous avons déployé une instance d’une application. Nous allons maintenant porter ce nombre d’instances à trois. Pour ce faire, créez un fichier json avec le texte json suivant et enregistrez-le dans un emplacement accessible.

```json
{ "instances": 3 }
```

Exécutez la commande suivante pour augmenter le nombre d’instances de l’application.

> Remarque : l’URI http://localhost/marathon/v2/apps/ est suivi de l’ID de l’application que vous souhaitez mettre à l’échelle. Si vous utilisiez l’exemple nginx fourni ici, l’URI serait http://localhost/v2/nginx.

```json
curl http://localhost/marathon/v2/apps/nginx -H "Content-type: application/json" -X PUT -d @scale.json
```

Pour finir, en interrogeant le point de terminaison Marathon à propos des applications, vous allez remarquer qu’elles sont désormais au nombre de trois dans le conteneur nginx.

```
curl localhost/marathon/v2/apps
```

## Interaction de l’API REST de Marathon avec PowerShell

Ces mêmes actions peuvent être effectuées à l’aide de PowerShell sur un système Windows. Ce rapide exercice permettra d’exécuter des tâches similaires à celles de l’exercice précédent, mais cette fois à l’aide des commandes PowerShell.

Pour collecter des informations sur le cluster Mesos (par exemple, le nom et l’état de l’agent), exécutez la commande suivante.

```powershell
Invoke-WebRequest -Uri http://localhost/mesos/master/slaves
```

Les conteneurs Docker formatés sont déployés via Marathon à l’aide d’un fichier json décrivant le déploiement souhaité. L’exemple suivant permet de déployer le conteneur nginx en liant le port 80 de l’agent Mesos au port 80 du conteneur.

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

Créez votre propre fichier json ou utilisez l’exemple présenté dans la page de [démonstration d’Azure ACS](https://raw.githubusercontent.com/rgardler/AzureDevTestDeploy/master/marathon/marathon.json), et stockez-le dans un emplacement accessible. Ensuite, exécutez la commande suivante en spécifiant le nom du fichier json pour déployer le conteneur.

```powershell
Invoke-WebRequest -Method Post -Uri http://localhost/marathon/v2/apps -ContentType application/json -InFile 'c:\marathon.json'
```

L’API Marathon peut également servir à diminuer ou augmenter la taille des déploiements d’applications. Dans l’exemple précédent, nous avons déployé une instance d’une application. Nous allons maintenant porter ce nombre d’instances à trois. Pour ce faire, créez un fichier json avec le texte json suivant et enregistrez-le dans un emplacement accessible.

```json
{ "instances": 3 }
```

Exécutez la commande suivante pour augmenter le nombre d’instances de l’application.

> Remarque : l’URI http://loclahost/marathon/v2/apps/ est suivi de l’ID de l’application que vous souhaitez mettre à l’échelle. Si vous utilisiez l’exemple nginx fourni ici, l’URI serait http://localhost/v2/nginx.

```powershell
Invoke-WebRequest -Method Put -Uri http://localhost/marathon/v2/apps/nginx -ContentType application/json -InFile 'c:\scale.json'
```

<!---HONumber=AcomDC_0323_2016-->