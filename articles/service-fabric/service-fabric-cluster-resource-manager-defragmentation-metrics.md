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
ms.date: 01/05/2017
ms.author: masnider
translationtype: Human Translation
ms.sourcegitcommit: dafaf29b6827a6f1c043af3d6bfe62d480d31ad5
ms.openlocfilehash: 5ef6381f7d182c818171eca3e3d32a00bc30268e


---
# <a name="defragmentation-of-metrics-and-load-in-service-fabric"></a>Défragmentation des mesures et de la charge dans Service Fabric
Le gestionnaire des ressources de cluster de Service Fabric Cluster Resource Manager vise principalement à obtenir un équilibrage en termes de répartition de la charge, en s’assurant que les nœuds du cluster sont utilisés de manière équitable. La distribution des charges de travail est généralement la configuration la plus sûre afin de surmonter les défaillances, car elle permet de s’assurer qu’une défaillance n’affecte pas un trop grand pourcentage d’une charge de travail donnée. Service Fabric Cluster Resource Manager prend en charge une stratégie différente : la défragmentation. La défragmentation signifie généralement qu’au lieu d’essayer de répartir l’utilisation d’une mesure dans le cluster, nous tentons de la consolider. La consolidation est une inversion avantageuse de notre stratégie normale. Au lieu de réduire l’écart type moyen de la charge de mesure, Cluster Resource Manager cherche à augmenter l’écart. Mais pourquoi choisir cette stratégie ?

En fait, si vous avez réparti la charge uniformément entre les nœuds du cluster, vous avez consommé une partie des ressources offertes par les nœuds. Toutefois, certaines charges de travail créent des services qui sont particulièrement volumineux et qui consomment la plus grande partie d’un nœud. Dans ces cas, il est possible que 75 % à 95 % des ressources d’un nœud finissent par être consacrées à un objet de service unique. Les charges de travail volumineuses ne sont pas un problème. Cluster Resource Manager détermine au moment de la création du service ce dont il a besoin pour réorganiser le cluster pour faire de la place pour cette charge de travail volumineuse. Toutefois, en attendant, cette charge de travail doit attendre d’être planifiée dans le cluster.

S’il y a de nombreux services et états à déplacer, le déplacement de cette charge de travail dans le cluster peut prendre beaucoup de temps. Cette situation est probable si d’autres charges de travail dans le cluster sont volumineuses et prennent plus longtemps à déplacer. L’équipe Service Fabric a mesuré les temps de création dans des simulations de ce scénario. Nous avons découvert que si les services sont assez volumineux et que le cluster a été très sollicité, la création de ces services est lente. Pour gérer ce scénario, nous avons introduit la défragmentation comme stratégie d’équilibrage. Nous avons découvert que pour les charges de travail volumineuses, notamment celles pour lesquelles le temps de création était important, la défragmentation aide réellement à la planification de ces nouvelles charges de travail dans le cluster.

Vous pouvez configurer des mesures de défragmentation pour que Cluster Resource Manager essaie de manière proactive de condenser la charge des services sur un nombre de nœuds plus réduit. Cela permet de garantir qu’il y a (presque) toujours de la place pour les services encore plus volumineux. Ceci permet à ces services d’être rapidement créés si nécessaire.

La plupart des utilisateurs n’ont pas besoin de la défragmentation. Les services sont en règle générale de petit taille et par conséquent, il n’est pas difficile de leur trouver de la place dans le cluster. Mais si vos services sont volumineux et qu’ils doivent être créés rapidement (et que vous êtes prêt à accepter les compromis nécessaires), la stratégie de défragmentation est faite pour vous.

Quels sont donc ces compromis ? La défragmentation peut augmenter la probabilité que les défaillances aient un impact (par exemple plus de services exécutés sur le nœud défaillant). En outre, la défragmentation garantit que certaines ressources du cluster restent inutilisées, en attente de la planification des charges de travail.

Le diagramme suivant offre une représentation visuelle de deux clusters, l’un défragmenté et l’autre non. Dans le cas équilibré, tenez compte du nombre de mouvements qui seraient nécessaires pour placer un des plus grands objets de service. Comparez ce cas à celui du cluster défragmenté, dans lequel la charge de travail volumineuse peut être immédiatement placée sur les nœuds quatre ou cinq.

<center>
![Comparaison de clusters équilibré et défragmenté][Image1]
</center>

## <a name="defragmentation-pros-and-cons"></a>Avantages et inconvénients de la défragmentation
Quelles sont donc ces autres compromis ? Nous vous recommandons d’effectuer une mesure minutieuse de vos charges de travail avant d’activer les mesures de défragmentation. Voici un tableau succinct des éléments à considérer :

| Avantages de la défragmentation | Inconvénients de la défragmentation |
| --- | --- |
| Permet la création plus rapide de services de grande taille |Concentre la charge dans un plus petit nombre de nœuds, ce qui augmente la contention |
| Permet de diminuer le déplacement des données lors de la création |Les défaillances peuvent avoir un impact sur d’autres services et provoquer une plus grande désinscription |
| Permet de décrire en détail les exigences et la récupération d’espace |Configuration générale plus complexe de la gestion des ressources |

Vous pouvez combiner des mesures défragmentés et normales dans le même cluster. Cluster Resource Manager tente de consolider autant que possible les mesures de défragmentation tout en répartissant les autres. Si ces mesures ne sont pas partagées par plusieurs services, les résultats peuvent être bons. Les résultats exacts dépendront du nombre de mesures d’équilibrage par rapport au nombre de mesures de défragmentation, leur chevauchement, leur poids, leurs charges actuelles, etc. L’expérimentation est nécessaire pour déterminer la configuration exacte nécessaire.

## <a name="configuring-defragmentation-metrics"></a>Configuration de mesures de défragmentation
La configuration des mesures de défragmentation est une décision globale au niveau du cluster et des mesures individuelles peuvent être sélectionnées pour la défragmentation :

ClusterManifest.xml :

```xml
<Section Name="DefragmentationMetrics">
    <Parameter Name="Disk" Value="true" />
    <Parameter Name="CPU" Value="false" />
</Section>
```

via ClusterConfig.json pour les déploiements autonomes ou Template.json pour les clusters hébergés sur Azure :

```json
"fabricSettings": [
  {
    "name": "DefragmentationMetrics",
    "parameters": [
      {
          "name": "Disk",
          "value": "true"
      },
      {
          "name": "CPU",
          "value": "false"
      }
    ]
  }
]
```


## <a name="next-steps"></a>Étapes suivantes
* Cluster Resource Manager comporte de nombreuses options permettant de décrire le cluster. Pour en savoir plus sur celles-ci, consultez cet article sur la [description d’un cluster Service Fabric](service-fabric-cluster-resource-manager-cluster-description.md)
* Les mesures représentent la façon dont Service Fabric Cluster Resource Manager gère la consommation et la capacité du cluster. Pour en savoir plus sur ces métriques et sur leur configuration, consultez [cet article](service-fabric-cluster-resource-manager-metrics.md)

[Image1]:./media/service-fabric-cluster-resource-manager-defragmentation-metrics/balancing-defrag-compared.png



<!--HONumber=Jan17_HO1-->


