<properties
   pageTitle="Visualisation de votre cluster à l'aide de l'outil Service Fabric Explorer | Microsoft Azure"
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
   ms.date="10/30/2015"
   ms.author="jesseb"/>

# Visualisation de votre cluster à l’aide de l’outil Service Fabric Explorer

Service Fabric Explorer est un outil web dédié à l’inspection et à la gestion d’applications et de nœuds dans un cluster Service Fabric. Service Fabric Explorer est hébergé directement au sein du cluster, de sorte qu’il est toujours disponible, quel que soit l'emplacement d’exécution de votre cluster.

## Connexion à Service Fabric Explorer

Si vous avez suivi les instructions pour [préparer votre environnement de développement](service-fabric-get-started.md), vous pouvez lancer Service Fabric Explorer sur votre cluster local en accédant à http://localhost:19080/Explorer.

>[AZURE.NOTE]Si vous utilisez Internet Explorer(IE) avec Service Fabric Explorer pour gérer un cluster à distance, vous devez configurer certains paramètres d'Internet Explorer. Accédez à **Outils -> Paramètres d'affichage de compatibilité** et désactivez **Afficher les sites intranet dans Affichage de compatibilité** pour vous assurer que toutes les informations soient chargées correctement.

## Connaissance de la disposition de Service Fabric Explorer

Vous pouvez naviguer dans Service Fabric Explorer à l'aide de l'arborescence sur la gauche. À la racine de l'arborescence, le tableau de bord de cluster fournit une vue d'ensemble de votre cluster, y compris un résumé de l’intégrité de l'application et du nœud.

![Tableau de bord de cluster de Service Fabric Explorer][sfx-cluster-dashboard]

### Le mappage du cluster

Les nœuds d'un cluster Service Fabric sont placés sur une grille 2D de domaines d'erreur et de domaines de mise à niveau pour s'assurer que vos applications restent disponibles en cas de défaillances matérielles et durant les mises à niveau de l'application. Vous pouvez voir la disposition actuelle du cluster à l'aide du mappage du cluster.

![Mappage de cluster de Service Fabric Explorer][sfx-cluster-map]

### Affichage des applications et des services

Le cluster contient deux sous-arborescences : une pour les applications, l'autre pour les nœuds.

La vue des applications vous permet de naviguer dans la hiérarchie logique de Service Fabric : applications, services, partitions et réplicas.

Dans l'exemple ci-dessous, l'application **MyApp** est constituée de deux services, **MyStatefulService** et **WebService**. Étant donné que **MyStatefulService** est une application avec état, elle inclut une partition avec un réplica principal et deux réplicas secondaires. En revanche, WebSvcService est une application sans état et contient une seule instance.

![Affichage des applications de Service Fabric Explorer][sfx-application-tree]

À chaque niveau de l'arborescence, le volet principal affiche des informations pertinentes sur l'élément. Par exemple, vous pouvez voir l'état d'intégrité et la version d’un service particulier.

![Volet des éléments essentiels de Service Fabric Explorer][sfx-service-essentials]

### Affichage des nœuds du cluster

L'affichage des nœuds montre la disposition physique du cluster. Pour un nœud donné, vous pouvez vérifier les applications ayant du code déployé sur ce nœud et, plus particulièrement, les réplicas en cours d'exécution sur ce nœud.

## Exécution d’actions à l'aide de Service Fabric Explorer

Service Fabric Explorer offre un moyen rapide d’appeler des actions sur les nœuds, les applications et les services au sein de votre cluster.

Par exemple, pour supprimer une instance d'application, choisissez simplement l'application dans l'arborescence sur la gauche, puis choisissez Actions > Supprimer l’Application.

![Suppression d’une application dans Service Fabric Explorer][sfx-delete-application]

Étant donné que de nombreuses actions sont irréversibles, vous devrez confirmer votre intention pour pouvoir effectuer l'action.

>[AZURE.NOTE]Toutes les actions pouvant être effectuées à l'aide de Fabric Service Explorer peuvent également être effectuées à l'aide de PowerShell ou d’une API REST, qui permet alors l’automatisation.



## Connexion à un cluster Service Fabric distant

Étant donné que Service Fabric Explorer est basé sur le Web et s'exécute au sein du cluster, il est accessible à partir de n'importe quel navigateur, tant que vous connaissez le point de terminaison du cluster et disposez des autorisations suffisantes pour y accéder.

### Découverte du point de terminaison de Service Fabric Explorer pour un Cluster à distance

Pour atteindre Service Fabric Explorer pour un cluster donné, il vous suffit de diriger votre navigateur vers :

http://&lt;your-cluster-endpoint&gt;:19080/Explorer

L'URL complète est également disponible dans le volet des éléments essentiels du cluster dans le portail Azure.

### Connexion à un cluster sécurisé

Vous pouvez contrôler l'accès à votre cluster Service Fabric en exigeant des clients qu’ils présentent un certificat pour s'y connecter.

Si vous essayez de vous connecter à Service Fabric Explorer sur un cluster sécurisé, votre navigateur vous demande de présenter un certificat pour y accéder.

## Étapes suivantes

- [Vue d’ensemble de la testabilité](service-fabric-testability-overview.md).
- [Gestion de vos applications Service Fabric dans Visual Studio](service-fabric-manage-application-in-visual-studio.md).
- [Déploiement d’application Service Fabrix à l’aide de PowerShell](service-fabric-deploy-remove-applications.md)

<!--Image references-->
[sfx-cluster-dashboard]: ./media/service-fabric-visualizing-your-cluster/SfxClusterDashboard.png
[sfx-cluster-map]: ./media/service-fabric-visualizing-your-cluster/SfxClusterMap.png
[sfx-application-tree]: ./media/service-fabric-visualizing-your-cluster/SfxApplicationTree.png
[sfx-service-essentials]: ./media/service-fabric-visualizing-your-cluster/SfxServiceEssentials.png
[sfx-delete-application]: ./media/service-fabric-visualizing-your-cluster/SfxDeleteApplication.png

<!---HONumber=AcomDC_1125_2015-->