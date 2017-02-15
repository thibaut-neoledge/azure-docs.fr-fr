---
title: "Cluster Resource Manager Service Fabric - Affinité | Microsoft Docs"
description: "Présentation de la configuration de l’affinité pour les services Service Fabric"
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: 
ms.assetid: 678073e1-d08d-46c4-a811-826e70aba6c4
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/19/2016
ms.author: masnider
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: ef1b4c5ab23365b62c3c1061061aec5a9155c3f4


---
# <a name="configuring-and-using-service-affinity-in-service-fabric"></a>Configuration et utilisation de l’affinité de service dans Service Fabric
L’affinité est un contrôle qui permet principalement de faciliter la transition d’applications volumineuses monolithiques vers le cloud et les microservices. Ceci dit, elle peut également servir dans certains cas d’optimisation légitime pour améliorer les performances des services, même si cela peut avoir des conséquences.

Prenons un exemple : vous importez une application volumineuse ou qui n’a pas été conçue pour les microservices dans Service Fabric. Cette transition est assez courante et concerne plusieurs de nos clients internes et externes. Vous commencez par migrer l’intégralité de l’application dans l’environnement pour l’empaqueter et la rendre opérationnelle. Puis vous la décomposez en différents services plus petits qui communiquent entre eux.

Un problème se pose alors. Il se situe généralement dans l’une des catégories suivantes :

1. Certains composants X de l’application monolithique ont une dépendance non documentée sur le composant Y, et nous les avons transformés en services séparés. Dans la mesure où ils sont maintenant exécutés sur différents nœuds du cluster, ils sont rompus.
2. Ces éléments communiquent au moyen de canaux nommés, de mémoire partagée ou de fichiers sur disque, mais il est nécessaire de les mettre à jour indépendamment pour accélérer le processus. Je supprimerai la dépendance dure plus tard.
3. Tout se passe bien, sauf que ces deux composants sont en fait très occupés et sensibles aux performances. Lorsqu’ils sont transférés dans des services distincts, la performance globale des applications diminue ou la latence augmente. Par conséquent, l’application globale ne répond pas aux attentes.

Dans ce cas, nous ne voulons pas perdre notre travail de refactorisation et ne souhaitons pas revenir au modèle monolithique, mais nous avons besoin d’une certaine forme de localisation. Ce problème persiste jusqu'à ce que nous puissions modifier les composants de manière à ce qu’ils fonctionnent naturellement en tant que services, ou jusqu'à ce que nous puissions répondre aux attentes de performances d’une autre manière, si possible.

Que faire, alors ? Il est possible d’activer l’affinité.

## <a name="how-to-configure-affinity"></a>Configuration de l’affinité
Pour configurer l’affinité, vous définissez une relation d’affinité entre deux services distincts. Cela revient à faire pointer un service sur un autre, ce qui fait qu’un service peut uniquement fonctionner quand cet autre service est en cours d’exécution. Il s’agit parfois de relations parent-enfant (où l’enfant pointe sur le parent). L’affinité permet de s’assurer que les instances ou les réplicas d’un service sont placés sur les mêmes nœuds que les réplicas ou instances du service d’un autre service.

``` csharp
ServiceCorrelationDescription affinityDescription = new ServiceCorrelationDescription();
affinityDescription.Scheme = ServiceCorrelationScheme.Affinity;
affinityDescription.ServiceName = new Uri("fabric:/otherApplication/parentService");
serviceDescription.Correlations.Add(affinityDescription);
await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

## <a name="different-affinity-options"></a>Différentes options d’affinité
L’affinité est représentée par un des jeux de corrélations et comporte deux modes différents. Le mode d’affinité courant est ce que nous appelons « affinité non alignée ». Dans ce mode, les réplicas ou instances des différents services sont placés sur les mêmes nœuds. L’autre mode est appelé « affinité alignée ». L’affinité alignée est utilisée uniquement avec les services avec état. La configuration de deux services avec état pour obtenir une affinité alignée garantit que les serveurs primaires de ces services sont placés sur les mêmes nœuds que d’autres. Par conséquent, chaque paire de serveurs secondaires est placée sur les mêmes nœuds. Il est également possible (bien que moins fréquent) de configurer l’affinité non alignée pour les services avec état. Pour NonAlignedAffinity les différents réplicas des deux services avec état se trouveraient sur les mêmes nœuds, mais aucune tentative d’alignement de leurs serveurs primaires ou secondaires n’est effectuée.

![Modes d’affinité et leurs effets][Image1]

### <a name="best-effort-desired-state"></a>Meilleur état souhaité
Il existe quelques différences entre l’affinité et les architectures monolithiques. Parfois, une relation d’affinité est conseillée. Les services dans une relation d’affinité sont fondamentalement différentes entités qui peuvent échouer et être déplacées séparément. D’autres raisons peuvent également expliquer l’échec de la relation. Par exemple, il peut exister des limites de capacité ayant pour conséquence que seuls certains objets de services peuvent être inclus sur un nœud donné. Dans ces cas, même s’il existe une relation d’affinité, elle n’est pas appliquée en raison des autres contraintes. S’il est possible d’appliquer toutes les autres contraintes et affinités ultérieurement, la violation de la contrainte d’affinité est automatiquement corrigée.  

### <a name="chains-vs-stars"></a>Chaînes et étoiles
Aujourd’hui, nous ne sommes pas en mesure de modéliser les chaînes de relations d’affinité. Cela signifie qu’un service qui est un service enfant dans une relation d’affinité ne peut pas être un parent dans une autre relation d’affinité. Si vous souhaitez modéliser ce type de relation, vous devez le modéliser en étoile plutôt qu’en chaîne. Pour ce faire, l’enfant le plus bas est apparenté à la place du parent de l’enfant « intermédiaire ». Selon la disposition de vos services, cela peut nécessiter la création d’un service « espace réservé » agissant comme le parent de plusieurs enfants.

![Chaînes et étoiles dans le contexte des relations d’affinité][Image2]

Notez également que les relations d’affinité d’aujourd’hui sont directionnelles. Cela signifie que la règle « d’affinité » implique que l’enfant se trouve au même endroit que le parent. Si, par exemple, le parent bascule soudainement vers un autre nœud, Cluster Resource Manager ne pense pas réellement qu’il existe un problème jusqu'à ce qu’il remarque que l’enfant ne trouve pas avec un parent. La relation n’est pas appliquée immédiatement.

### <a name="partitioning-support"></a>Prise en charge du partitionnement
Voici une dernière remarque concernant l’affinité : les relations d’affinité ne sont pas prises en charge lorsque le parent est partitionné. Il se peut que cela change à l’avenir. Mais pour l’instant, cette prise en charge n’est pas prévue.

## <a name="next-steps"></a>Étapes suivantes
* Pour plus d’informations sur les autres options disponibles pour la configuration des services, consultez la rubrique sur les autres configurations de Cluster Resource Manager disponibles [En savoir plus sur la configuration des services](service-fabric-cluster-resource-manager-configure-services.md)
* Un grand nombre de motifs d’utilisation de l’affinité, par exemple la limitation des services sur un petit ensemble de machines virtuelles et la tentative d’agrégation de la charge d’une collection de services, sont en fait mieux pris en charge par le biais de groupes d’applications. Découvrir les [groupes d’applications](service-fabric-cluster-resource-manager-application-groups.md)

[Image1]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-affinity/cluster-resrouce-manager-affinity-modes.png
[Image2]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-affinity/cluster-resource-manager-chains-vs-stars.png



<!--HONumber=Nov16_HO3-->


