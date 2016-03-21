<properties
   pageTitle="Gestionnaire de ressources de cluster Service Fabric - Affinité"
   description="Vue d’ensemble des autres règles et stratégies de positionnement pour les services Service Fabric"
   services="service-fabric"
   documentationCenter=".net"
   authors="masnider"
   manager="timlt"
   editor=""/>

<tags
   ms.service="Service-Fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="03/03/2016"
   ms.author="masnider"/>

# Affinité

L’affinité est une caractéristique qui n’a à première vue aucun sens dans un environnement de microservice. L’affinité est un contrôle qui permet principalement de faciliter la transition d’applications volumineuses précédemment monolithiques dans le cloud et les microservices.

Prenons un exemple : vous importez une application volumineuse ou qui n’a pas été conçue pour les microservices dans Service Fabric. C’est un cas de figure assez courant qui concerne plusieurs de nos clients internes et externes. Vous commencez par migrer l’intégralité de l’application dans l’environnement pour la compresser et la rendre opérationnelle. Puis vous la décomposez en différents services plus petits qui communiquent entre eux.

Un problème se pose alors. Il se situe généralement dans l’une des catégories suivantes :

1. Le composant X de l’application monolithique a une dépendance non documentée sur le composant Y qui vient d’être converti en service et déplacé au sein du cluster. Il est maintenant endommagé.
2.	Ces éléments communiquent au moyen de canaux nommés, de mémoire partagée ou de fichiers sur disque, mais il est nécessaire de les mettre à jour indépendamment pour accélérer le processus.
3.	Tout se passe bien, sauf que ces deux composants sont en fait très occupés et sensibles aux performances. Ce qui fait que lorsqu’ils sont migrés dans des services distincts, les performances diminuent de manière significative.

Dans tous ces cas, nous ne voulons pas perdre notre travail de refactorisation ni revenir au modèle monolithique. Nous voulons remettre les choses en ordre et revenir en local jusqu’à ce que le problème soit résolu.

Que faire, alors ? Il est possible d’activer l’affinité.

## Fonctionnement de l’affinité
Pour configurer l’affinité, vous définissez une relation d’affinité entre deux services distincts. Cela revient à faire pointer un service sur un autre, ce qui fait que tel service peut uniquement fonctionner quand tel autre service est en cours d’exécution. Il s’agit de relations parent-enfant (où l’enfant pointe sur le parent). Cela permet de s’assurer que les instances ou les réplicas d’un service sont placés sur les mêmes nœuds que les réplicas ou instances du service pour lequel l’affinité est configurée.

``` csharp
ServiceCorrelationDescription affinityDescription = new ServiceCorrelationDescription();
affinityDescription.Scheme = ServiceCorrelationScheme.Affinity;
affinityDescription.ServiceName = new Uri("fabric:/otherApplication/parentService");
serviceDescription.Correlations.Add(affinityDescription);
await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

## Différentes options d’affinité
L’affinité est représentée par un des jeux de corrélations et comporte deux modes différents. Le mode d’affinité courant est ce que nous appelons « affinité non alignée ». Dans ce mode, les réplicas ou instances des différents services sont placés sur les mêmes nœuds. L’autre mode est appelé « affinité alignée ». L’affinité alignée est utilisée avec les services avec état. La configuration d’une affinité alignée entre deux services avec état garantit que les réplicas principaux de ces services sont placés sur des nœuds identiques et que chaque paire de réplicas secondaires est également placée sur les mêmes nœuds. Il est également possible (bien que moins fréquent) de configurer l’affinité non alignée pour les services avec état. Dans ce cas, vous obtenez pratiquement le même comportement que pour les services sans état : les réplicas des deux services avec état se trouvent sur les mêmes nœuds, mais aucune tentative n’est faite pour que les réplicas principaux et secondaires soient situés sur les mêmes nœuds.

![Modes d’affinité et leurs effets][Image1]

#### Meilleur état souhaité
Il existe quelques différences entre l’affinité et les architectures monolithiques. La principale est qu’une relation d’affinité est la meilleure option, car des services fondamentalement différents peuvent échouer indépendamment, par exemple. D’autres caractéristiques, telles que les limites de capacité, peuvent entraîner la séparation des différents réplicas du service.


#### Chaînes et étoiles
Aujourd’hui, nous ne sommes pas en mesure de modéliser les chaînes de relations d’affinité. Cela signifie qu’un service qui est un service enfant dans une relation d’affinité ne peut pas être un parent dans une autre relation d’affinité. En d’autres termes, si vous souhaitez modéliser ce type de relation, vous devez effectivement le modéliser en tant qu’étoile, plutôt qu’en tant que chaîne, en apparentant l’enfant le plus bas au parent de l’enfant « intermédiaire ».

![Chaînes et étoiles dans le contexte des relations d’affinité][Image2]

Notez également que les relations d’affinité d’aujourd’hui sont directionnelles. Cela signifie que la règle d’affinité applique uniquement le fait que l’enfant est situé au même endroit que le parent. Si le parent bascule soudainement vers un autre nœud (ou toute autre action limitée qui force le déplacement du parent uniquement), le Gestionnaire de ressources ne détecte pas de problème tant qu’il n’a pas remarqué que l’enfant n’est pas au même emplacement que le parent. Dans ce cas, la relation n’est pas appliquée immédiatement.

#### Prise en charge du partitionnement
Voici une dernière remarque concernant l’affinité : les relations d’affinité ne sont pas prises en charge lorsque le parent est partitionné. Il se peut que cela change à l’avenir. Mais pour l’instant, cette prise en charge n’est pas prévue.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Étapes suivantes
- [En savoir plus sur la configuration des services](service-fabric-cluster-resource-manager-configure-services.md)

[Image1]: ./media/service-fabric-cluster-resource-manager-advanced-placement-rules-affinity/cluster-resrouce-manager-affinity-modes.png
[Image2]: ./media/service-fabric-cluster-resource-manager-advanced-placement-rules-affinity/cluster-resource-manager-chains-vs-stars.png

<!------HONumber=AcomDC_0309_2016-->