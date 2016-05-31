<properties
   pageTitle="Gestion de conteneur Azure Container Service avec Docker Swarm | Microsoft Azure"
   description="Déploiement de conteneurs sur un Docker Swarm dans Azure Container Service"
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
   ms.date="04/13/2016"
   ms.author="nepeters"/>

# Gestion des conteneurs avec Docker Swarm

Docker Swarm fournit un environnement pour le déploiement de charges de travail en conteneur sur un ensemble groupé d’hôtes Docker. Docker Swarm utilise l’API Docker native. Le flux de travail pour la gestion des conteneurs sur un Docker Swarm est presque identique à ce qu’elle serait sur un hôte de conteneur individuel. Ce document fournit des exemples simples de déploiement de charges de travail en conteneur dans une instance Azure Container Service de Docker Swarm. Pour des informations détaillées sur Docker Swarm, consultez [Docker Swarm sur Docker.com](https://docs.docker.com/swarm/).

Conditions préalables pour les exercices de ce document :

[Création d’un cluster Swarm dans Azure Container Service](container-service-deployment.md)

[Connexion avec le cluster Swarm dans Azure Container Service](container-service-connect.md)

## Déploiement d’un nouveau conteneur

Pour créer un nouveau conteneur dans le Docker Swarm, utilisez la commande `docker run`. Cet exemple crée un conteneur à partir de l’image `yeasy/simple-web` :


```bash
user@ubuntu:~$ docker run -d -p 80:80 yeasy/simple-web

4298d397b9ab6f37e2d1978ef3c8c1537c938e98a8bf096ff00def2eab04bf72
```

Une fois le conteneur créé, utilisez `docker ps` pour renvoyer des informations sur le conteneur. Ici, l’agent Swarm qui héberge le conteneur est répertorié :


```bash
user@ubuntu:~$ docker ps

CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                 NAMES
4298d397b9ab        yeasy/simple-web    "/bin/sh -c 'python i"   31 seconds ago      Up 9 seconds        10.0.0.5:80->80/tcp   swarm-agent-34A73819-1/happy_allen
```  

Vous pouvez maintenant accéder à l’application en cours d’exécution dans ce conteneur via le nom DNS public de l’équilibreur de charge de l’agent Swarm. Ces informations sont disponibles dans le portail Azure.


![Résultats réels des visites](media/real-visit.jpg)

## Déploiement de plusieurs conteneurs

Plusieurs conteneurs étant lancés sur le cluster Docker Swarm, vous pouvez utiliser la commande `docker ps` pour afficher l’hôte sur lequel les conteneurs sont exécutés. Dans cet exemple, trois conteneurs sont répartis uniformément entre les trois agents Swarm :


```bash
user@ubuntu:~$ docker ps

CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                 NAMES
11be062ff602        yeasy/simple-web    "/bin/sh -c 'python i"   11 seconds ago      Up 10 seconds       10.0.0.6:83->80/tcp   swarm-agent-34A73819-2/clever_banach
1ff421554c50        yeasy/simple-web    "/bin/sh -c 'python i"   49 seconds ago      Up 48 seconds       10.0.0.4:82->80/tcp   swarm-agent-34A73819-0/stupefied_ride
4298d397b9ab        yeasy/simple-web    "/bin/sh -c 'python i"   2 minutes ago       Up 2 minutes        10.0.0.5:80->80/tcp   swarm-agent-34A73819-1/happy_allen
```  

## Déploiement de conteneurs avec Docker Compose

Vous pouvez utiliser Docker Compose pour automatiser le déploiement et la configuration de plusieurs conteneurs. Pour cela, assurez-vous qu’un tunnel Secure Shell (SSH) a été créé et que la variable DOCKER\_HOST a été définie.

Créez un fichier docker-compose.yml sur votre système local. Pour ce faire, utilisez cet [exemple](https://raw.githubusercontent.com/rgardler/AzureDevTestDeploy/master/docker-compose.yml).

```bash
web:
  image: adtd/web:0.1
  ports:
    - "80:80"
  links:
    - rest:rest-demo-azure.marathon.mesos
rest:
  image: adtd/rest:0.1
  ports:
    - "8080:8080"

```

Exécutez `docker-compose up -d` pour démarrer les déploiements de conteneur :


```bash
user@ubuntu:~/compose$ docker-compose up -d
Pulling rest (adtd/rest:0.1)...
swarm-agent-3B7093B8-0: Pulling adtd/rest:0.1... : downloaded
swarm-agent-3B7093B8-2: Pulling adtd/rest:0.1... : downloaded
swarm-agent-3B7093B8-3: Pulling adtd/rest:0.1... : downloaded
Creating compose_rest_1
Pulling web (adtd/web:0.1)...
swarm-agent-3B7093B8-3: Pulling adtd/web:0.1... : downloaded
swarm-agent-3B7093B8-0: Pulling adtd/web:0.1... : downloaded
swarm-agent-3B7093B8-2: Pulling adtd/web:0.1... : downloaded
Creating compose_web_1
```

Enfin, la liste des conteneurs en cours d’exécution est retournée. Cette liste répertorie les conteneurs qui ont été déployés à l’aide de Docker Compose :


```bash
user@ubuntu:~/compose$ docker ps
CONTAINER ID        IMAGE               COMMAND                CREATED             STATUS              PORTS                     NAMES
caf185d221b7        adtd/web:0.1        "apache2-foreground"   2 minutes ago       Up About a minute   10.0.0.4:80->80/tcp       swarm-agent-3B7093B8-0/compose_web_1
040efc0ea937        adtd/rest:0.1       "catalina.sh run"      3 minutes ago       Up 2 minutes        10.0.0.4:8080->8080/tcp   swarm-agent-3B7093B8-0/compose_rest_1
```

## Étapes suivantes

[Approfondissez vos connaissances sur Docker Swarm](https://docs.docker.com/swarm/)

<!---HONumber=AcomDC_0525_2016-->