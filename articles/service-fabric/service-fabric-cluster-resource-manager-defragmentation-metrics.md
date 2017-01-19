---
title: "Défragmentation des métriques dans Azure Service Fabric | Microsoft Docs"
description: "Une présentation de l’utilisation de la défragmentation ou de la compression en tant que stratégie pour les métriques dans Service Fabric"
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: 
ms.assetid: e5ebfae5-c8f7-4d6c-9173-3e22a9730552
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/19/2016
ms.author: masnider
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 128db3fccecaa610d078c17aa2f2133338966153


---
# <a name="defragmentation-of-metrics-and-load-in-service-fabric"></a>Défragmentation des mesures et de la charge dans Service Fabric
Service Fabric Cluster Resource Manager vise principalement à obtenir un équilibrage en termes de répartition de la charge, en s’assurant que tous les nœuds du cluster sont utilisés de manière équitable. Il s’agit généralement de la configuration la plus sûre et la plus intelligente afin de surmonter les défaillances, car elle permet de s’assurer qu’une défaillance donnée n’occupe pas un important pourcentage d’une charge de travail donnée. Service Fabric Cluster Resource Manager prend en charge une stratégie différente : la défragmentation. La défragmentation signifie généralement qu’au lieu d’essayer de répartir l’utilisation d’une mesure dans le cluster, nous tentons de la consolider. Il s’agit d’une inversion avantageuse de notre stratégie normale. Au lieu d’optimiser le cluster sur la base de la minimisation de l’écart standard moyen de la charge de mesure pour une mesure donnée, nous commençons l’optimisation pour augmenter l’écart. Mais pourquoi choisir cette stratégie ?

En fait, si vous avez réparti la charge uniformément entre les nœuds du cluster, vous avez consommé une partie des ressources offertes par les nœuds. Normalement, cela ne pose pas de problème. Toutefois, certaines charges de travail créent parfois des services qui sont particulièrement volumineux et consomment la grande majorité d’un nœud : par exemple, entre 75 et 95 % des ressources d’un nœud sont finalement dédiés à un réplica ou une instance de service unique. Ce n’est pas un problème, le Cluster Resource Manager détectera lors de la création du service qu’il doit réorganiser le cluster pour faire de la place à cette charge de travail importante et prendre les mesures nécessaires pour cela, mais qu’entre-temps, cette charge de travail doit attendre pour être planifiée dans le cluster.

Étant donné que la planification de nouvelles charges de travail est généralement un peu sensible en termes de latence, si nous ne procédons pas différemment, nous risquons parfois de dépasser les conditions des contrats SLA associés s’il existe un grand nombre de services et d’états à déplacer, en particulier si les charges de travail dans le cluster sont généralement importantes (et que, par conséquent, le déplacement dans le cluster prend un certain temps). En effet, lorsque nous avons mesuré la durée de création des simulations en fonction des données de cluster réelles, nous avons remarqué la chose suivante : si les services sont assez grands et que le cluster a été utilisé de manière équitable, la création de ces services volumineux est ralentie et nous pouvons améliorer cela en introduisant la stratégie de mesures de défragmentation.

Tout comme la création de fichiers ou l’accès peut être ralenti si un disque dur est fragmenté et peut être accéléré en défragmentant le lecteur de telle manière que des blocs contingents plus importants soient disponibles, vous pouvez configurer des mesures de défragmentation pour que le Cluster Resource Manager tente de manière proactive de réduire la charge des services en la répartissant dans un plus petit nombre de nœuds afin qu’il y ait (presque) toujours de la place pour les services encore plus volumineux qui peuvent alors être créés rapidement. La plupart des gens n’en auront plus besoin, parce que les services doivent généralement être petits et que, de ce fait, il n’est pas difficile de leur trouver de la place. Cependant, si vous avez des services volumineux devant être créés rapidement (et que vous voulez accepter les autres compromis, tels que l’impact accru des défaillances et le fait que certaines ressources ne sont pas utilisées pendant qu’elles attendent la planification des charges de travail), la stratégie de défragmentation vous convient.

Le diagramme suivant donne une représentation visuelle de deux clusters différents, l’un est défragmenté et l’autre non. Dans le cas équilibré, envisagez les mouvements nécessaires pour placer l’un des plus grands objets de service, si un autre devait être créé, par rapport au cluster défragmenté, dans lequel le service pourrait être placé immédiatement sur les nœuds 4 ou 5.

![Comparaison de clusters équilibré et défragmenté][Image1]

## <a name="defragmentation-pros-and-cons"></a>Avantages et inconvénients de la défragmentation
Quelles sont donc ces autres compromis ? Nous vous recommandons d’effectuer une mesure minutieuse de vos charges de travail avant d’activer les mesures de défragmentation. Voici un tableau succinct des éléments à considérer :

| Avantages de la défragmentation | Inconvénients de la défragmentation |
| --- | --- |
| Permet la création plus rapide de services de grande taille |Concentre la charge dans un plus petit nombre de nœuds, ce qui augmente la contention |
| Permet de diminuer le déplacement des données lors de la création |Les défaillances peuvent avoir un impact sur d’autres services et provoquer une plus grande désinscription |
| Permet de décrire en détail les exigences et la récupération d’espace |Configuration générale plus complexe de la gestion des ressources |

Vous pouvez combiner des mesures défragmentés et normales dans le même cluster. Le Gestionnaire de ressources fait alors de son mieux pour s’assurer que vous obtenez une configuration qui consolide les mesures de défragmentation autant que possible en tentant de répartir le reste. Les résultats exacts que vous obtiendrez dépendront du nombre de mesures d’équilibrage par rapport au nombre de mesures de défragmentation et leurs poids, charges actuelles, etc. 

## <a name="configuring-defragmentation-metrics"></a>Configuration de mesures de défragmentation
La configuration des mesures de défragmentation est une décision globale au niveau du cluster et des mesures individuelles peuvent être sélectionnées pour la défragmentation :

ClusterManifest.xml :

```xml
<Section Name="DefragmentationMetrics">
    <Parameter Name="Disk" Value="true" />
    <Parameter Name="CPU" Value="false" />
</Section>
```

## <a name="next-steps"></a>Étapes suivantes
* Cluster Resource Manager comporte de nombreuses options permettant de décrire le cluster. Pour en savoir plus sur celles-ci, consultez cet article sur la [description d’un cluster Service Fabric](service-fabric-cluster-resource-manager-cluster-description.md)
* Les mesures représentent la façon dont Service Fabric Cluster Resource Manager gère la consommation et la capacité du cluster. Pour en savoir plus sur ces métriques et la façon de les configurer, consultez [cet article](service-fabric-cluster-resource-manager-metrics.md)

[Image1]:./media/service-fabric-cluster-resource-manager-defragmentation-metrics/balancing-defrag-compared.png



<!--HONumber=Dec16_HO2-->


