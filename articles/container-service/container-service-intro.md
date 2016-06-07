<properties
   pageTitle="Présentation d’Azure Container Service | Microsoft Azure"
   description="Azure Container Service offre un moyen de simplifier la création, la configuration et la gestion d’un cluster de machines virtuelles préconfigurées pour exécuter des applications en conteneur."
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

Azure Container Service simplifie la création, la configuration et la gestion d’un cluster de machines virtuelles préconfigurées pour exécuter des applications en conteneur. Ce service utilise une configuration optimisée d’outils de planification et d’orchestration open source bien connus. Il vous permet d’exploiter vos compétences existantes ou de faire appel à une large communauté d’experts toujours plus nombreux pour déployer et gérer des applications en conteneur sur Microsoft Azure.

<br /> ![Azure Container Service offre un moyen de gérer les applications en conteneur sur plusieurs hôtes dans Azure.](./media/acs-intro/acs-cluster.png) <br /><br />

Azure Container Service utilise le format de conteneur Docker pour assurer la portabilité complète de vos conteneurs d’application. Par ailleurs, il prend en charge votre choix d’utiliser Marathon et DC/OS ou Docker Swarm de sorte que vous puissiez mettre à l’échelle ces applications pour des milliers, voire des dizaines de milliers de conteneurs.

En utilisant Azure Container Service, vous pouvez tirer parti des fonctionnalités d’entreprise d’Azure tout en conservant la portabilité des applications, notamment au niveau des couches d’orchestration.

Utilisation d’Azure Container Service
-----------------------------

L’objectif d’Azure Container Service est de proposer un environnement d’hébergement de conteneurs basé sur des outils et des technologies open source déjà bien connus de nos clients. À cette fin, nous exposons les points de terminaison d’API standard de l’orchestrateur de votre choix. Vous pouvez alors exploiter n’importe quel logiciel capable de communiquer avec ces points de terminaison. Par exemple, dans le cas du point de terminaison Docker Swarm, vous pouvez choisir d’utiliser l’interface de ligne de commande Docker. Pour DC/OS, vous pouvez choisir d’utiliser l’interface CLI DC/OS.

Création d’un cluster Docker en utilisant Azure Container Service
-------------------------------------------------------

Pour commencer à utiliser Azure Container Service, vous devez déployer un cluster Azure Container Service en utilisant un modèle Azure Resource Manager. Vous pouvez configurer ce déploiement avec différentes options de taille et de disponibilité, en utilisant DC/OS ou Docker Swarm. Vous pouvez déployer des modèles Azure Resource Manager via le portail Azure en utilisant l’interface de ligne de commande Azure ou avec PowerShell. Les modèles peuvent également être modifiés pour inclure une configuration Azure supplémentaire ou avancée. Pour plus d’informations sur le déploiement d’un cluster Azure Container Service, consultez [Déploiement d’un cluster Azure Container Service](container-service-deployment.md).

Déploiement d’une application
------------------------

Le service de conteneur Azure propose le choix entre Docker Swarm et DC/OS pour l'orchestration.

### Utilisation de DC/OS

DC/OS est un projet open source hébergé par l’Apache Software Foundation. Il fait intervenir certains des [plus grands noms de l’informatique](http://mesos.apache.org/documentation/latest/powered-by-mesos/) en tant qu’utilisateurs et collaborateurs.

![Azure Container Service configuré pour Swarm présentant des agents et des maîtres.](media/acs-intro/dcos.png)

DC/OS comprend un ensemble de fonctionnalités impressionnant :

-   Extensibilité permettant une prise en charge de dizaines de milliers de nœuds

-   Serveurs maître et subordonnés répliqués et à tolérance de panne avec Apache ZooKeeper

-   Prise en charge des conteneurs de format Docker

-   Isolement natif entre les tâches avec des conteneurs Linux

-   Planification de ressources multiples (mémoire, UC, disque et ports)

-   API Java, Python et C++ pour le développement de nouvelles applications parallèles

-   Interface utilisateur web pour l’affichage de l’état du cluster

DC/OS prend en charge un grand nombre d’[infrastructures](http://mesos.apache.org/documentation/latest/frameworks/) que vous pouvez utiliser pour planifier des charges de travail sur le service de conteneur Azure. Par défaut, Azure Container Service intègre les infrastructures Marathon et Chronos.

#### Utilisation de Marathon et Chronos

Marathon est un système d’initialisation et de contrôle à l’échelle du cluster destiné à des services de groupes de contrôle (ou « cgroups ») ou, dans le cas d’Azure Container Service, des conteneurs de format Docker. C’est le partenaire idéal de Chronos, planificateur de tâches à tolérance de panne pour DC/OS qui gère les dépendances et les planifications chronologiques.

Marathon et Chronos proposent une interface utilisateur web à partir de laquelle vous pouvez déployer vos applications. L’URL qui permet d’y accéder ressemble à `http://DNS\_PREFIX.REGION.cloudapp.azure.com`, où DNS\_PREFIX et REGION sont deux valeurs définies au moment du déploiement. Bien sûr, vous pouvez également fournir votre propre nom DNS. Pour plus d’informations sur l’exécution d’un conteneur à l’aide de l’interface utilisateur web Marathon, consultez [Gestion des conteneurs via l’interface utilisateur web](container-service-mesos-marathon-ui.md).

Vous pouvez également utiliser des API REST pour communiquer avec Marathon et Chronos. Plusieurs bibliothèques clientes sont disponibles pour chaque outil. Elles couvrent une variété de langages et il va de soi que vous pouvez utiliser le protocole HTTP dans n’importe quel langage. De plus, de nombreux outils DevOps bien connus prennent en charge ces planificateurs. Votre équipe en charge des opérations profite ainsi d’une flexibilité maximale quand vous utilisez un cluster Azure Container Service. Pour plus d’informations sur l’exécution d’un conteneur à l’aide de l’API REST Marathon, consultez [Gestion de conteneurs via l’API REST](container-service-mesos-marathon-rest.md).

### Utilisation de Docker Swarm

Docker Swarm assure un clustering natif pour Docker. Étant donné que Docker Swarm dessert l’API Docker standard, tout outil qui communique déjà avec un démon Docker peut utiliser Swarm pour évoluer de façon transparente vers plusieurs hôtes sur Azure Container Service.

![Le service de conteneur Azure configuré pour utiliser DC/OS, présentant Jumpbox, des agents et des maîtres.](media/acs-intro/acs-swarm2.png)

Les outils pris en charge pour la gestion des conteneurs sur un cluster Swarm incluent, sans s’y limiter, les suivants :

-   Dokku

-   Interface de ligne de commande Docker et Docker Compose

-   Krane

-   Jenkins

Vidéos
------
Annonce AzureCon :

> [AZURE.VIDEO azurecon-2015-deep-dive-on-the-azure-container-service-with-mesos]  

Prise en main d’Azure Container Service :

> [AZURE.VIDEO connect-2015-getting-started-developing-with-docker-and-azure-container-service]

<!---HONumber=AcomDC_0525_2016-->