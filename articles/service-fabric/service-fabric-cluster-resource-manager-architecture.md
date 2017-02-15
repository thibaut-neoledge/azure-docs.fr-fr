---
title: Architecture du Gestionnaire de ressources | Microsoft Docs
description: "Vue d’ensemble de l’architecture du Gestionnaire de ressources Service Fabric."
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: 
ms.assetid: 6c4421f9-834b-450c-939f-1cb4ff456b9b
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/19/2016
ms.author: masnider
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: d6bfc5b7141eae5cb755679445176064f2e5b0d4


---
# <a name="cluster-resource-manager-architecture-overview"></a>Vue d’ensemble de l’architecture Cluster Resource Manager
Pour gérer les ressources de votre cluster, Service Fabric Cluster Resource Manager doit disposer de plusieurs éléments. Il doit connaître les services actuels et le volume actuel (ou par défaut) des ressources que ceux-ci consomment. Il doit connaître la capacité réelle des nœuds du cluster et donc la quantité de ressources disponibles dans le cluster dans son ensemble et restant sur un nœud particulier. La consommation des ressources d’un service donné peut évoluer au fil du temps, ainsi que le fait que les services prennent généralement en charge plusieurs ressources. Différents services peuvent contenir des ressources physiques réelles en cours mesurées et signalées comme métriques (telles que la consommation de mémoire et de disque), ainsi que (et en fait plus souvent) des métriques logiques (notamment « WorkQueueDepth » ou « TotalRequests »). Les mesures physiques et logiques peuvent être utilisées dans différents types de services ou peuvent être spécifiques à quelques services uniquement.

## <a name="other-considerations"></a>Autres considérations
Les propriétaires et les opérateurs du cluster sont parfois différents des auteurs du service, ou une même personne peut avoir plusieurs rôles. Par exemple, lorsque vous développez votre service, vous connaissez en partie ce dont il a besoin en termes de ressources et comment les différents composants doivent être déployés idéalement. Toutefois, comme vous gérez un incident de site actif pour ce service en production, vous avez d’autres tâches à effectuer qui nécessitent différents outils. En outre, ni le cluster ni les services eux-mêmes ne sont configurés statiquement : le nombre de nœuds du cluster peut augmenter ou diminuer, les nœuds de différentes tailles peuvent aller et venir et les services peuvent être créés et supprimés ou modifier l’affectation de ressources souhaitée à la volée. Les mises à niveau ou d’autres opérations de gestion peuvent impacter le cluster, et bien évidemment, il y a toujours un risque de défaillance.

## <a name="cluster-resource-manager-components-and-data-flow"></a>Composants et flux de données Cluster Resource Manager
Le Cluster Resource Manager doit connaître de nombreux éléments sur le cluster global, ainsi que les spécifications de services individuels et les instances sans état et les réplicas avec état qui le composent. Pour ce faire, nous avons des agents du Cluster Resource Manager qui s’exécutent sur des nœuds individuels pour agréger les informations sur la consommation des ressources locales, ainsi qu’un service Cluster Resource Manager centralisé et à tolérance de pannes qui regroupe toutes les informations sur les services et le cluster, et qui réagit en fonction de sa configuration actuelle. La tolérance de panne pour le service Cluster Resource Manager (et tous les autres services système) est obtenue par les mêmes mécanismes que ceux utilisés pour vos services, à savoir la réplication de l’état du service à des quorums et un certain nombre de réplicas dans le cluster (généralement 7).

![Architecture de l'équilibreur de ressources][Image1]

Prenons comme exemple le schéma ci-dessus. Au moment de l’exécution, de nombreux changements peuvent se produire : par exemple, la modification de la quantité de ressources consommées par les services, des échecs du service, certains nœuds qui rejoignent et quittent le cluster, etc. Toutes les modifications sur un nœud spécifique sont agrégées et régulièrement envoyées au service Cluster Resource Manager (1, 2) où elles sont agrégées à nouveau, analysées et stockées. À une fréquence de quelques secondes, ce service examine toutes les modifications et détermine si des actions sont nécessaires (3). Par exemple, il peut remarquer que des nœuds vides ont été ajoutés au cluster et décider de déplacer certains services vers ces nœuds. Il peut également remarquer qu’un nœud particulier est surchargé ou que certains services ont échoué (ou ont été supprimés), en libérant des ressources sur d’autres nœuds.

Examinons le schéma suivant pour voir ce qui se passe ensuite. Supposons que Cluster Resource Manager détermine que des modifications sont nécessaires. Il communique avec d’autres services système (en particulier Failover Manager) pour apporter les modifications nécessaires. Les demandes de modification sont ensuite envoyées aux nœuds appropriés (4). Dans ce cas, nous supposons que le Gestionnaire de ressources a remarqué que le nœud 5 est surchargé et a donc décidé de déplacer le service B de N5 à N4. À la fin de la reconfiguration (5), le cluster a l’aspect suivant :

![Architecture de l'équilibreur de ressources][Image2]

## <a name="next-steps"></a>Étapes suivantes
* Cluster Resource Manager comporte de nombreuses options permettant de décrire le cluster. Pour en savoir plus sur celles-ci, consultez cet article sur la [description d’un cluster Service Fabric](service-fabric-cluster-resource-manager-cluster-description.md)

[Image1]:./media/service-fabric-cluster-resource-manager-architecture/Service-Fabric-Resource-Manager-Architecture-Activity-1.png
[Image2]:./media/service-fabric-cluster-resource-manager-architecture/Service-Fabric-Resource-Manager-Architecture-Activity-2.png



<!--HONumber=Nov16_HO3-->


