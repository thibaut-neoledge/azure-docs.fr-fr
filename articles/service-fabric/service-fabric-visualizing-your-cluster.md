<properties
   pageTitle="Visualisation de votre cluster à l’aide de l’outil Service Fabric Explorer"
   description="Service Fabric Explorer est un outil GUI pratique, dédié à l’inspection et à la gestion d’applications cloud et de nœuds dans un cluster Microsoft Azure Service Fabric."
   services="service-fabric"
   documentationCenter=".net"
   authors="jessebenson"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="05/20/2015"
   ms.author="jesseb"/>

# Visualisation de votre cluster à l’aide de l’outil Service Fabric Explorer

Service Fabric Explorer est un outil visuel dédié à l’inspection et à la gestion d’applications cloud et de nœuds dans un cluster Microsoft Azure Service Fabric. Service Fabric Explorer peut se connecter à des clusters de développement locaux et à des clusters Microsoft Azure. Pour en savoir plus sur les applets de commande Service Fabric PowerShell, consultez les **Étapes suivantes**.

> [AZURE.NOTE]Il n’est pas encore possible de créer des clusters Service Fabric dans Microsoft Azure.

## Introduction à Service Fabric Explorer

Vérifiez que votre environnement local de développement est configuré conformément aux instructions de la page [Configurer votre environnement de développement Service Fabric](service-fabric-get-started.md).

Exécutez Service Fabric Explorer à partir de votre chemin d’installation local (%Program Files%\Microsoft SDKs\Service Fabric\Tools\ServiceFabricExplorer\ServiceFabricExplorer.exe). L’outil se connecte automatiquement sur un cluster de développement local, s’il en existe un. Il affiche les informations suivantes sur le cluster :

- Applications exécutées sur le cluster
- Données relatives aux nœuds du cluster
- Événements d’intégrité associés aux applications et aux nœuds
- Charge sur les applications du cluster
- Surveillance de l’état de mise à niveau des applications

![Représentation visuelle du cluster Service Fabric et des applications déployées][servicefabricexplorer]

Une des visualisations importantes du mappage de cluster, visible sur le tableau de bord du cluster (cliquez sur **Onebox/cluster local**). Le mappage de cluster représente l’ensemble de domaines de mise à niveau et de défaillance, ainsi que les associations de nœuds mappés. Pour vous familiariser avec les concepts clés de Service Fabric, consultez la [vue d’ensemble technique de Service Fabric](service-fabric-technical-overview.md).

![Le mappage du cluster indique les domaines de mise à niveau et de défaillance auxquels appartiennent les différents nœuds.][clustermap]


## Affichage des applications et des services

Service Fabric Explorer vous permet de découvrir les applications exécutées sur votre cluster. Développez la **Vue Application** afin d’afficher des informations détaillées sur vos applications, services, partitions et réplicas.

Le tableau ci-dessous indique que l’application intitulée **fabric:/Stateful1Application** présente un service sans état appelé **fabric:/Stateful1Application/MyFrontEnd** et un service avec état appelé **fabric:/Stateful1Application/Stateful1**. Le service sans état comporte une partition avec un réplica exécuté sur **Node.4**. Le service avec état comporte deux partitions, chacune comprenant trois réplicas exécutés sur des nœuds différents.

![Vue des applications exécutées sur le cluster Service Fabric][applicationview]

En cliquant sur une application, un service, une partition ou un réplica, vous avez accès à des informations détaillées sur cette entité. Le tableau ci-dessous représente le tableau de bord d’intégrité d’un des réplicas principaux du service avec état. Il fait état de son rôle, du nœud sur lequel il est exécuté, de l’adresse écoutée, de l’emplacement de ses fichiers sur le disque et des événements d’intégrité.

![Informations détaillées sur un réplica Service Fabric][replicadetails]


## Connexion à un cluster Service Fabric distant

Pour afficher un cluster Service Fabric distant, cliquez sur **Connexion**. La boîte de dialogue **Connexion à un cluster Service Fabric** apparaît. Entrez le **point de terminaison Service Fabric** associé à votre cluster, puis cliquez sur **Connexion**. Le point de terminaison Service Fabric est généralement le nom public de votre service de cluster à l’écoute du port 19000.

![Configurer une connexion à votre cluster Service Fabric distant][connecttocluster]


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Étapes suivantes

- [Vue d’ensemble de la testabilité](service-fabric-testability-overview.md).
- [Gestion de vos applications Service Fabric dans Visual Studio](service-fabric-manage-application-in-visual-studio.md).
- [Déploiement d’application Service Fabrix à l’aide de PowerShell](service-fabric-deploy-remove-applications.md)

<!--Image references-->
[applicationview]: ./media/service-fabric-visualizing-your-cluster/applicationview.png
[clustermap]: ./media/service-fabric-visualizing-your-cluster/clustermap.png
[connecttocluster]: ./media/service-fabric-visualizing-your-cluster/connecttocluster.png
[replicadetails]: ./media/service-fabric-visualizing-your-cluster/replicadetails.png
[servicefabricexplorer]: ./media/service-fabric-visualizing-your-cluster/servicefabricexplorer.png
 

<!---HONumber=July15_HO4-->