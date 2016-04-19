<properties
   pageTitle="Visualisation de votre cluster à l'aide de l'outil Service Fabric Explorer | Microsoft Azure"
   description="Service Fabric Explorer est un outil web dédié à l’inspection et à la gestion des applications cloud et des nœuds dans un cluster Microsoft Azure Service Fabric."
   services="service-fabric"
   documentationCenter=".net"
   authors="seanmck"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="04/05/2016"
   ms.author="seanmck"/>

# Visualiser votre cluster à l’aide de l’outil Service Fabric Explorer

Service Fabric Explorer est un outil web dédié à l’inspection et à la gestion d’applications et de nœuds dans un cluster Azure Service Fabric. Service Fabric Explorer est hébergé directement au sein du cluster, de sorte qu’il est toujours disponible, quel que soit l’emplacement d’exécution de votre cluster.

## Se connecter à Service Fabric Explorer

Si vous avez suivi les instructions pour [préparer votre environnement de développement](service-fabric-get-started.md), vous pouvez lancer Service Fabric Explorer sur votre cluster local en accédant à http://localhost:19080/Explorer.

>[AZURE.NOTE] Si vous utilisez Internet Explorer avec Service Fabric Explorer pour gérer un cluster distant, vous devez configurer certains paramètres d’Internet Explorer. Accédez à **Outils** > **Paramètres d’affichage de compatibilité** et désactivez **Afficher les sites intranet dans Affichage de compatibilité** pour vérifier que toutes les informations sont chargées correctement.

## Comprendre la disposition de Service Fabric Explorer

Vous pouvez naviguer dans Service Fabric Explorer à l’aide de l’arborescence située à gauche. À la racine de l'arborescence, le tableau de bord de cluster fournit une vue d'ensemble de votre cluster, y compris un résumé de l’intégrité de l'application et du nœud.

![Tableau de bord de cluster de Service Fabric Explorer][sfx-cluster-dashboard]

### Afficher la disposition du cluster

Les nœuds dans un cluster Service Fabric sont répartis dans une grille à deux dimensions composée de domaines d’erreur et de domaines de mise à niveau. Cette répartition garantit la disponibilité de vos applications en cas de défaillances matérielles et de mises à niveau de l’application. Vous pouvez examiner la disposition du cluster actif à l’aide de la carte du cluster.

![Mappage de cluster de Service Fabric Explorer][sfx-cluster-map]

### Afficher les applications et les services

Le cluster contient deux sous-arborescences : une pour les applications et l’autre pour les nœuds.

Vous pouvez utiliser la vue des applications pour naviguer dans la hiérarchie logique de Service Fabric : applications, services, partitions et réplicas.

Dans l’exemple ci-dessous, l’application **MyApp** est constituée de deux services : **MyStatefulService** et **WebService**. Étant donné que **MyStatefulService** est une application avec état, elle inclut une partition avec un réplica principal et deux réplicas secondaires. En revanche, WebSvcService est une application sans état qui ne contient qu’une seule instance.

![Affichage des applications de Service Fabric Explorer][sfx-application-tree]

À chaque niveau de l'arborescence, le volet principal affiche des informations pertinentes sur l'élément. Par exemple, vous pouvez voir l’état d’intégrité et la version d’un service particulier.

![Volet des éléments essentiels de Service Fabric Explorer][sfx-service-essentials]

### Afficher les nœuds du cluster

L’affichage des nœuds montre la disposition physique du cluster. Vous pouvez identifier les applications ayant déployé du code sur un nœud donné. Plus particulièrement, vous pouvez voir les réplicas en cours d’exécution sur ce nœud.

## Actions

Service Fabric Explorer offre un moyen rapide d’appeler des actions sur les nœuds, les applications et les services au sein de votre cluster.

Par exemple, pour supprimer une instance d’application, choisissez simplement l’application dans l’arborescence à gauche, puis sélectionnez **Actions** > **Supprimer l’application**.

![Suppression d’une application dans Service Fabric Explorer][sfx-delete-application]

>[AZURE.TIP] Les mêmes actions peuvent être effectuées à partir de l’arborescence en cliquant sur les points de suspension (bouton de sélection) en regard de chaque élément.

Le tableau suivant répertorie les actions disponibles pour chaque entité :

| **Entité** | **Action** | **Description** |
| ------ | ------ | ----------- |
| Type d’application | Type de mise hors service | Supprime le package d’application du magasin d’images du cluster. Exige la suppression préalable de toutes les applications de ce type. |
| Application | Supprimer l’application | Supprime l’application, y compris tous ses services et leur état (le cas échéant). |
| Service | Supprimer le service | Supprime le service et son état (le cas échéant). |
| Nœud | Activer | Active le nœud. |
|| Désactiver (pause) | Suspend le nœud dans son état actuel. Les services continuent à s’exécuter mais Service Fabric ne déplace rien de manière proactive, sauf si un déplacement est nécessaire pour éviter une panne ou une incohérence des données. Cette action est généralement utilisée pour activer les services de débogage sur un nœud spécifique afin de veiller à ce qu’ils ne se déplacent pas pendant l’inspection. |
|| Désactiver (redémarrage) | Déplace en toute sécurité tous les services en mémoire d’un nœud et ferme les services persistants. Généralement utilisé quand les processus hôtes ou la machine ont besoin d’être redémarrés. |
|| Désactiver (suppression de données) | Ferme en toute sécurité tous les services en cours d’exécution sur le nœud après avoir généré suffisamment de réplicas de réserve. Généralement utilisé quand un nœud (ou au moins son stockage) est définitivement mis hors service. |
|| Supprimer l’état du nœud | Supprime la connaissance des réplicas d’un nœud du cluster. Généralement utilisé quand un nœud déjà défaillant est considéré comme irrécupérable. |

Étant donné que de nombreuses actions sont irréversibles, vous devrez peut-être confirmer votre intention pour pouvoir effectuer l’action.

>[AZURE.TIP] Toutes les actions accessibles par le biais de Service Fabric Explorer le sont également par l’intermédiaire de PowerShell ou d’une API REST, ce qui vous permet d’activer Automation.



## Se connecter à un cluster Service Fabric distant

Étant donné que Service Fabric Explorer est basé sur le Web et s'exécute au sein du cluster, il est accessible à partir de n'importe quel navigateur, tant que vous connaissez le point de terminaison du cluster et disposez des autorisations suffisantes pour y accéder.

### Découvrir le point de terminaison de Service Fabric Explorer pour un cluster distant

Pour atteindre Service Fabric Explorer pour un cluster donné, il vous suffit de diriger votre navigateur vers :

http://&lt;your-cluster-endpoint&gt;:19080/Explorer

L'URL complète est également disponible dans le volet des éléments essentiels du cluster dans le portail Azure.

### Se connecter à un cluster sécurisé

Vous pouvez contrôler l'accès à votre cluster Service Fabric en exigeant des clients qu’ils présentent un certificat pour s'y connecter.

Si vous essayez de vous connecter à Service Fabric Explorer sur un cluster sécurisé, votre navigateur vous demande de présenter un certificat pour y accéder.

## Étapes suivantes

- [Vue d’ensemble de la testabilité](service-fabric-testability-overview.md)
- [Gestion de vos applications Service Fabric dans Visual Studio](service-fabric-manage-application-in-visual-studio.md)
- [Déploiement d’application Service Fabrix à l’aide de PowerShell](service-fabric-deploy-remove-applications.md)

<!--Image references-->
[sfx-cluster-dashboard]: ./media/service-fabric-visualizing-your-cluster/SfxClusterDashboard.png
[sfx-cluster-map]: ./media/service-fabric-visualizing-your-cluster/SfxClusterMap.png
[sfx-application-tree]: ./media/service-fabric-visualizing-your-cluster/SfxApplicationTree.png
[sfx-service-essentials]: ./media/service-fabric-visualizing-your-cluster/SfxServiceEssentials.png
[sfx-delete-application]: ./media/service-fabric-visualizing-your-cluster/SfxDeleteApplication.png

<!---HONumber=AcomDC_0406_2016-->