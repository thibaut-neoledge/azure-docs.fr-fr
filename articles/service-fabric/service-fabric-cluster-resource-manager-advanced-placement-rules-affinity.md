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
ms.date: 01/05/2017
ms.author: masnider
translationtype: Human Translation
ms.sourcegitcommit: dafaf29b6827a6f1c043af3d6bfe62d480d31ad5
ms.openlocfilehash: 16863217eddf0a4bbd85c52f8481c03e50dd9108


---
# <a name="configuring-and-using-service-affinity-in-service-fabric"></a>Configuration et utilisation de l’affinité de service dans Service Fabric
L’affinité est un contrôle qui permet principalement de faciliter la transition d’applications volumineuses monolithiques vers le cloud et les microservices. Cela dit, elle peut également servir dans certains cas d’optimisation légitime pour améliorer les performances des services, même si cela peut avoir des conséquences.

Prenons un exemple : vous importez une application volumineuse ou qui n’a pas été conçue pour les microservices dans Service Fabric. Ce type de transition est courant. Vous commencez par intégrer l’application complète dans l’environnement, vous la déployez et vous vous assurez qu’elle fonctionne sans problème Puis vous la décomposez en différents services plus petits qui communiquent entre eux.

Un problème se pose alors. Il se situe généralement dans l’une des catégories suivantes :

1. Certains composants X de l’application monolithique avaient une dépendance non documentée sur le composant Y et vous les avez transformés en services séparés. Dans la mesure où ces services sont maintenant exécutés sur différents nœuds du cluster, ils sont interrompus.
2. Ces éléments communiquent par vecteur (canaux locaux nommés | mémoire partagée | fichiers sur disque), mais ils doivent maintenant pouvoir écrire sur une ressource partagée pour des raisons de performances. Cette dépendance ferme est supprimée ultérieurement.
3. Tout fonctionne correctement, sauf que ces deux composants sont en fait très occupés et sensibles aux performances. Lorsqu’ils sont transférés dans des services distincts, la performance globale des applications diminue ou la latence augmente. Par conséquent, l’application globale ne répond pas aux attentes.

Dans ce cas, nous ne voulons pas perdre notre travail de refactorisation et nous ne souhaitons pas revenir au modèle monolithique. Cependant, jusqu’à ce que nous puissions modifier les composants de manière à ce qu’ils fonctionnent naturellement en tant que services (ou jusqu’à ce que nous puissions répondre autrement aux attentes en matière de performances), nous devrons avoir une certaine notion de la localisation.

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

<center>
![Modes d’affinité et leurs effets][Image1]
</center>

### <a name="best-effort-desired-state"></a>Meilleur état souhaité
Il existe quelques différences entre l’affinité et les architectures monolithiques. Parfois, une relation d’affinité est conseillée. Les services dans une relation d’affinité sont fondamentalement différentes entités qui peuvent échouer et être déplacées séparément. D’autres raisons peuvent également expliquer l’échec de la relation. Par exemple, il peut exister des limites de capacité ayant pour conséquence que seuls certains objets de services peuvent être inclus sur un nœud donné. Dans ces cas, même s’il existe une relation d’affinité, elle n’est pas appliquée en raison des autres contraintes. Si la situation le permet, la violation sera corrigée automatiquement plus tard.

### <a name="chains-vs-stars"></a>Chaînes et étoiles
Actuellement, Cluster Resource Manager ne peut pas modéliser les chaînes de relations d’affinité. Cela signifie qu’un service qui est un service enfant dans une relation d’affinité ne peut pas être un parent dans une autre relation d’affinité. Si vous souhaitez modéliser ce type de relation, vous devez le modéliser en étoile plutôt qu’en chaîne. Pour passer d’une chaîne à une étoile, l’enfant le plus bas est apparenté au parent du premier enfant. En fonction de la disposition de vos services, il est possible que vous deviez effectuer cette opération plusieurs fois. S’il n’existe aucun service parent naturel, il est possible que vous deviez en créer un qui sert d’espace réservé. Selon vos besoins, vous pouvez également opter pour les [Groupes d’applications](service-fabric-cluster-resource-manager-application-groups.md).

<center>
![Chaînes et étoiles dans le contexte des relations d’affinité][Image2]
</center>

Notez également que les relations d’affinité d’aujourd’hui sont directionnelles. Cela signifie que la règle « d’affinité » implique que l’enfant se trouve au même endroit que le parent, et non que le parent se trouve au même endroit que l’enfant. Par exemple, supposons que le parent bascule soudainement vers un autre nœud. Lorsque ceci se produit, Cluster Resource Manager pense que tout fonctionne correctement, jusqu’à ce qu’il remarque que l’enfant ne se trouve pas avec un parent. La relation d’affinité ne peut pas être parfaite ni instantanément appliquée, dans la mesure où il s’agit de services différents avec des cycles de vie différents.

### <a name="partitioning-support"></a>Prise en charge du partitionnement
Voici une dernière remarque concernant l’affinité : les relations d’affinité ne sont pas prises en charge lorsque le parent est partitionné. Il se peut que cela change à l’avenir. Mais pour l’instant, cette prise en charge n’est pas prévue.

## <a name="next-steps"></a>Étapes suivantes
* Pour plus d’informations sur les autres options disponibles pour la configuration des services, consultez la rubrique sur les autres configurations de Cluster Resource Manager disponibles [En savoir plus sur la configuration des services](service-fabric-cluster-resource-manager-configure-services.md)
* Pour les utilisations comme la limitation des services à un petit ensemble de machines virtuelles et la tentative d’agrégation de la charge d’une collection de services, utilisez les [groupes d’applications](service-fabric-cluster-resource-manager-application-groups.md).

[Image1]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-affinity/cluster-resrouce-manager-affinity-modes.png
[Image2]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-affinity/cluster-resource-manager-chains-vs-stars.png



<!--HONumber=Jan17_HO1-->


