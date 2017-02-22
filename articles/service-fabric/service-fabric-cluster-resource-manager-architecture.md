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
ms.date: 01/05/2017
ms.author: masnider
translationtype: Human Translation
ms.sourcegitcommit: dafaf29b6827a6f1c043af3d6bfe62d480d31ad5
ms.openlocfilehash: 67f07bad8f6f89d9e5e68326f0cc194d920e841b


---
# <a name="cluster-resource-manager-architecture-overview"></a>Vue d’ensemble de l’architecture Cluster Resource Manager
Pour gérer les ressources de votre cluster, Service Fabric Cluster Resource Manager doit disposer de plusieurs éléments. Il doit connaître les services actuels et le volume actuel (ou par défaut) des ressources que ceux-ci consomment. Pour suivre les ressources disponibles dans le cluster, il doit connaître la capacité des nœuds du cluster et la quantité de ressources consommées sur chacun de ces nœuds. La consommation des ressources d’un service donné peut évoluer au fil du temps et les services prennent généralement en charge plusieurs types de ressources. Dans différents services, il peut s’agir de ressources physiques réelles et de ressources physiques mesurées. Les services peuvent assurer le suivi des mesures physiques comme la consommation de mémoire et de disque. En général, les services peuvent se soucier des mesures logiques, comme « WorkQueueDepth » ou « TotalRequests ». Les mesures physiques et logiques peuvent être utilisées dans différents types de services ou peuvent être spécifiques à quelques services uniquement.

## <a name="other-considerations"></a>Autres considérations
Les propriétaires et les opérateurs du cluster sont parfois différents des auteurs du service ou au minimum, il s’agit des mêmes personnes, mais avec des rôles différents. Par exemple, lors du développement de votre service, vous en savez un peu plus sur ce dont vous avez besoin en termes de ressources et comment les différents composants doivent être déployés. Toutefois, les tâches qui incombent à la personne qui gère un incident sur site pour ce service en production sont différentes et nécessitent des outils différents. En outre, ni le cluster ni les services ne sont configurés de façon statique :

* Le nombre de nœuds dans le cluster peut augmenter ou diminuer.
* Des nœuds présentant des tailles et types différents vont et viennent.
* Des services peuvent être créés ou supprimés, et peuvent modifier leur allocation de ressources.
* Les mises à niveau ou autres opérations de gestion peuvent avoir un impact sur le cluster, et, bien évidemment, il y a toujours un risque de défaillance.

## <a name="cluster-resource-manager-components-and-data-flow"></a>Composants et flux de données Cluster Resource Manager
Cluster Resource Manager doit suivre les spécifications de services individuels et la consommation des ressources par les objets de service qui composent ces services. Pour ce faire, Cluster Resource Manager se compose de deux éléments conceptuels : les agents, qui s’exécutent sur chaque nœud, et un service à haute tolérance face aux pannes. Les agents de chaque nœud suivent les rapports de charge des services, les agrègent et envoient des rapports régulièrement. Cluster Resource Manager rassemble toutes les informations auprès des agents locaux et réagit en fonction de sa configuration actuelle.

Examinons le schéma suivant :

<center>
![Architecture de l’équilibreur de ressources][Image1]
</center>

Pendant l’exécution, de nombreux changements peuvent se produire. Par exemple, supposons que la quantité de ressources que consomment certains services change, que certains services échouent et que certains nœuds se joignent au cluster ou le quittent. Toutes les modifications sur les nœuds sont agrégées et régulièrement envoyées au service Cluster Resource Manager (1,2) où elles sont agrégées à nouveau, analysées et stockées. À une fréquence de quelques secondes, ce service examine les changements et détermine si des actions sont nécessaires (3). Par exemple, il peut remarquer que des nœuds vides ont été ajoutés au cluster et décider de déplacer certains services vers ces nœuds. Cluster Resource Manager peut également remarquer qu’un nœud particulier est surchargé ou que certains services ont échoué (ou ont été supprimés), en libérant des ressources ailleurs.

Examinons le schéma suivant pour voir ce qui se passe ensuite. Supposons que Cluster Resource Manager détermine que des modifications sont nécessaires. Il communique avec d’autres services système (en particulier Failover Manager) pour apporter les modifications nécessaires. Les commandes nécessaires sont envoyées aux nœuds appropriés (4). Dans ce cas, nous supposons que le Gestionnaire de ressources a remarqué que le nœud 5 est surchargé et a donc décidé de déplacer le service B de N5 à N4. À la fin de la reconfiguration (5), le cluster a l’aspect suivant :

<center>
![Architecture de l’équilibreur de ressources][Image2]
</center>

## <a name="next-steps"></a>Étapes suivantes
* Cluster Resource Manager comporte de nombreuses options permettant de décrire le cluster. Pour en savoir plus sur celles-ci, consultez cet article sur la [description d’un cluster Service Fabric](service-fabric-cluster-resource-manager-cluster-description.md)

[Image1]:./media/service-fabric-cluster-resource-manager-architecture/Service-Fabric-Resource-Manager-Architecture-Activity-1.png
[Image2]:./media/service-fabric-cluster-resource-manager-architecture/Service-Fabric-Resource-Manager-Architecture-Activity-2.png



<!--HONumber=Feb17_HO3-->


