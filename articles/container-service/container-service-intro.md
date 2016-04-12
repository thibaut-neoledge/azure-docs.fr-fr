<properties
   pageTitle="Présentation d’Azure Container Service | Microsoft Azure"
   description="Azure Container Service (ACS) offre un moyen de simplifier la création, la configuration et la gestion d’un cluster de machines virtuelles préconfigurées pour exécuter des applications en conteneur."
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
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/16/2016"
   ms.author="rogardle"/>

# Présentation d’Azure Container Service

Azure Container Service (ACS) offre un moyen de simplifier la création, la configuration et la gestion d’un cluster de machines virtuelles préconfigurées pour exécuter des applications en conteneur. À l’aide d’une configuration optimisée d’outils grand public de planification et d’orchestration open source, ACS vous permet d’exploiter vos compétences existantes ou de faire appel à une large communauté d’experts toujours plus nombreux pour déployer et gérer des applications en conteneur sur Microsoft Azure.

<br /> ![ACS offre un moyen de gérer les applications en conteneur sur plusieurs ordinateurs hôtes sur Azure.](./media/acs-intro/acs-cluster.png) <br /><br />

ACS utilise le format de conteneur Docker pour assurer la portabilité complète de vos conteneurs d’application. Est également pris en charge votre choix de Marathon et Apache Mesos ou Docker Swarm pour assurer l’évolutivité de ces applications vers des milliers voire des dizaines de milliers de conteneurs.

Azure Container Service vous permet de tirer parti des fonctionnalités d’entreprise d’Azure tout en conservant la portabilité des applications, notamment au niveau des couches d’orchestration.

Utilisation d’Azure Container Service
-----------------------------

L’objectif d’Azure Container Service est de fournir un environnement d’hébergement de conteneurs, à l’aide d’outils et de technologies open source déjà bien connus de nos clients. À cette fin, nous exposons les points de terminaison d’API standard de l’orchestrateur de votre choix. Vous pouvez alors exploiter n’importe quel logiciel capable de communiquer avec ces points de terminaison. Par exemple, dans le cas du point de terminaison Docker Swarm, vous pouvez choisir d’utiliser l’interface de ligne de commande Docker, tandis que pour Apache Mesos, vous pouvez choisir d’utiliser l’interface de ligne de commande DCOS.

Création d’un cluster Docker à l’aide d’Azure Container Service
-------------------------------------------------------

Pour commencer à utiliser Azure Container Service, un cluster ACS est déployé à l’aide d’un modèle Azure Resource Manager. Ce déploiement est configuré avec Apache Mesos ou Docker Swarm, et peut avoir différentes options de disponibilité et de taille. Les modèles Azure Resource Manager peuvent être déployés par le biais du portail Azure, à l’aide de l’interface de ligne de commande Azure ou avec PowerShell. Les modèles peuvent également être modifiés pour inclure une configuration Azure supplémentaire ou avancée. Pour plus d’informations sur le déploiement et les clusters ACS, consultez [Deploy an Azure container Service Cluster](./container-service-deployment.md) (Déployer un cluster Azure Container Service).

Déploiement d’une application
------------------------

Pendant l’évaluation, nous vous permettons de choisir entre Docker Swarm et Apache Mesos (avec des infrastructures DCOS Marathon et DCOS Chronos) pour l’orchestration.

### Utilisation d’Apache Mesos

Apache Mesos est un projet open source hébergé sur Apache Software Foundation. Il fait intervenir certains des [plus grands noms de l’informatique](http://mesos.apache.org/documentation/latest/powered-by-mesos/) en tant qu’utilisateurs et collaborateurs.

![ACS configuré pour Swarm présentant les agents et les maîtres.](media/acs-intro/acs-mesos.png)

Mesos rassemble un nombre impressionnant de fonctionnalités.

-   Extensibilité jusqu’à des dizaines de milliers de nœuds

-   Serveurs maître et subordonnés répliqués et à tolérance de panne à l’aide de ZooKeeper

-   Prise en charge des conteneurs Docker formatés

-   Isolement natif entre les tâches avec des conteneurs Linux

-   Planification de ressources multiples (mémoire, processeur, disque et ports)

-   API Java, Python et C++ pour développer de nouvelles applications parallèles

-   Interface utilisateur web pour afficher l’état du cluster

Mesos prend en charge un grand nombre d’[infrastructures](http://mesos.apache.org/documentation/latest/frameworks/) utilisables pour planifier des charges de travail sur Azure Container Service. Par défaut, ACS inclut des infrastructures Marathon et Chronos.

#### Utilisation de Marathon et Chronos

Marathon est un système d’initialisation et de contrôle à l’échelle du cluster destiné à des services en groupes ou, dans le cas d’ACS, en conteneurs de format Docker. C’est le partenaire idéal de Chronos, planificateur de tâches à tolérance de panne pour Mesos qui gère les dépendances et les planifications chronologiques.

Marathon et Chronos fournissent une interface utilisateur web à partir de laquelle vous pouvez déployer vos applications. L’URL qui permet d’y accéder ressemble à `http://DNS\_PREFIX.REGION.cloudapp.azure.com`, où DNS\_PREFIX et REGION sont deux valeurs définies au moment du déploiement. Bien sûr, vous pouvez également fournir votre propre nom DNS. Pour plus d’informations sur l’exécution d’un conteneur à l’aide de l’interface utilisateur web Marathon, consultez [Container management through the web UI](./container-service-mesos-marathon-ui.md) (Gestion des conteneurs via l’interface utilisateur web).

Vous pouvez également utiliser des API REST pour communiquer avec Marathon et Chronos. Plusieurs bibliothèques clientes sont disponibles pour chaque outil, couvrant différents langages et, bien sûr, vous pouvez utiliser le protocole HTTP dans n’importe quel langage. De plus, de nombreux outils DevOps bien connus prennent en charge ces planificateurs. La flexibilité fournie est ainsi maximale pour votre équipe en charge des opérations quand elle utilise un cluster ACS. Pour plus d’informations sur l’exécution d’un conteneur à l’aide de l’API REST Marathon, consultez [Container management with the REST API](./container-service-mesos-marathon-rest.md) (Gestion des conteneurs avec l’API REST).

### Utilisation de Docker Swarm

Docker Swarm assure un clustering natif pour Docker. Étant donné que Docker Swarm dessert l’API Docker standard, tout outil qui communique déjà avec un démon Docker peut utiliser Swarm pour évoluer de façon transparente vers plusieurs hôtes sur Azure Container Service.

![ACS configuré pour utiliser Apache Mesos, présentant Jumpbox, les agents et les maîtres.](media/acs-intro/acs-swarm.png)

Les outils pris en charge pour la gestion des conteneurs sur un cluster Swarm incluent, sans s’y limiter, les suivants :

-   Dokku

-   Interface de ligne de commande Docker et Docker Compose

-   Krane

-   Jenkins

Vidéos
------
Annonce AzureCon :

> [AZURE.VIDEO azurecon-2015-deep-dive-on-the-azure-container-service-with-mesos]  

Prise en main de d’ACS :

> [AZURE.VIDEO connect-2015-getting-started-developing-with-docker-and-azure-container-service]

<!---HONumber=AcomDC_0330_2016-->