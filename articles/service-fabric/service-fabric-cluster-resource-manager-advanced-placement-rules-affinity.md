<properties
   pageTitle="Cluster Resource Manager Service Fabric - Affinité | Microsoft Azure"
   description="Présentation de la configuration de l’affinité pour les services Service Fabric"
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
   ms.date="05/20/2016"
   ms.author="masnider"/>

# Configuration et utilisation de l’affinité de service dans Service Fabric

L’affinité est une caractéristique qui n’a à première vue aucun sens L’affinité est un contrôle qui permet principalement de faciliter la transition d’applications volumineuses précédemment monolithiques dans le cloud et les microservices. Ceci dit, elle peut également servir dans certains cas en tant qu’optimisation légitime pour améliorer les performances des services, même si cela peut entraîner des effets secondaires.

Prenons un exemple : vous importez une application volumineuse ou qui n’a pas été conçue pour les microservices dans Service Fabric. C’est un cas de figure assez courant qui concerne plusieurs de nos clients internes et externes. Vous commencez par migrer l’intégralité de l’application dans l’environnement pour la compresser et la rendre opérationnelle. Puis vous la décomposez en différents services plus petits qui communiquent entre eux.

Un problème se pose alors. Il se situe généralement dans l’une des catégories suivantes :

1. Le composant X de l’application monolithique a une dépendance non documentée sur le composant Y qui vient d’être converti en service et déplacé au sein du cluster. Il est maintenant endommagé.
2.	Ces éléments communiquent au moyen de canaux nommés, de mémoire partagée ou de fichiers sur disque, mais il est nécessaire de les mettre à jour indépendamment pour accélérer le processus. Je supprimerai la dépendance dure plus tard.
3.	Tout se passe bien, sauf que ces deux composants sont en fait très occupés et sensibles aux performances. Ce qui fait que lorsqu’ils sont migrés dans des services distincts, les performances diminuent de manière significative et l’application n’est plus utilisable.

Dans tous ces cas, nous ne voulons pas perdre notre travail de refactorisation ni revenir au modèle monolithique. Nous voulons remettre les choses en ordre et revenir en local jusqu’à ce que le problème soit résolu.

Que faire, alors ? Il est possible d’activer l’affinité.

## Configuration de l’affinité
Pour configurer l’affinité, vous définissez une relation d’affinité entre deux services distincts. Cela revient à faire pointer un service sur un autre, ce qui fait que tel service peut uniquement fonctionner quand tel autre service est en cours d’exécution. Il s’agit de relations parent-enfant (où l’enfant pointe sur le parent). Cela permet de s’assurer que les instances ou les réplicas d’un service sont placés sur les mêmes nœuds que les réplicas ou instances du service pour lequel l’affinité est configurée.

``` csharp
ServiceCorrelationDescription affinityDescription = new ServiceCorrelationDescription();
affinityDescription.Scheme = ServiceCorrelationScheme.Affinity;
affinityDescription.ServiceName = new Uri("fabric:/otherApplication/parentService");
serviceDescription.Correlations.Add(affinityDescription);
await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

## Différentes options d’affinité
L’affinité est représentée par un des jeux de corrélations et comporte deux modes différents. Le mode d’affinité courant est ce que nous appelons « affinité non alignée ». Dans ce mode, les réplicas ou instances des différents services sont placés sur les mêmes nœuds. L’autre mode est appelé « affinité alignée ». L’affinité alignée est utilisée avec les services avec état. La configuration d’une affinité alignée entre deux services avec état garantit que les réplicas principaux de ces services sont placés sur des nœuds identiques et que chaque paire de réplicas secondaires est également placée sur les mêmes nœuds. Il est également possible (bien que moins fréquent) de configurer l’affinité non alignée pour les services avec état. Dans ce cas, vous obtenez pratiquement le même comportement que pour les services sans état : les réplicas des deux services avec état se trouvent sur les mêmes nœuds, mais aucune tentative n’est faite pour que les réplicas principaux et secondaires soient situés sur les mêmes nœuds.

![Modes d’affinité et leurs effets][Image1]

### Meilleur état souhaité
Il existe quelques différences entre l’affinité et les architectures monolithiques. La principale est qu’une relation d’affinité est la meilleure option, car des services fondamentalement différents peuvent échouer indépendamment, par exemple. D’autres caractéristiques, telles que les limites de capacité, peuvent entraîner la séparation des différents réplicas ou instances des services avec affinités. Dans ces cas-là, même s’il existe une relation d’affinité, elle n’est pas appliquée en raison des autres contraintes (de manière temporaire). S’il est possible par la suite d’appliquer toutes les autres contraintes et affinités, cela sera automatiquement corrigé.

### Chaînes et étoiles
Aujourd’hui, nous ne sommes pas en mesure de modéliser les chaînes de relations d’affinité. Cela signifie qu’un service qui est un service enfant dans une relation d’affinité ne peut pas être un parent dans une autre relation d’affinité. Si vous souhaitez modéliser ce type de relation, vous devez effectivement le modéliser en tant qu’étoile, plutôt qu’en tant que chaîne, en apparentant l’enfant le plus bas au parent de l’enfant « intermédiaire ». Selon la disposition de vos services, cela peut nécessiter la création d’un service « espace réservé » agissant comme le parent de plusieurs enfants.

![Chaînes et étoiles dans le contexte des relations d’affinité][Image2]

Notez également que les relations d’affinité d’aujourd’hui sont directionnelles. Cela signifie que la règle d’affinité applique uniquement le fait que l’enfant est situé au même endroit que le parent. Si le parent bascule soudainement vers un autre nœud (ou toute autre action limitée qui force le déplacement du parent uniquement), le Gestionnaire de ressources ne détecte pas de problème tant qu’il n’a pas remarqué que l’enfant n’est pas au même emplacement que le parent. Dans ce cas, la relation n’est pas appliquée immédiatement.

### Prise en charge du partitionnement
Voici une dernière remarque concernant l’affinité : les relations d’affinité ne sont pas prises en charge lorsque le parent est partitionné. Il se peut que cela change à l’avenir. Mais pour l’instant, cette prise en charge n’est pas prévue.

## Étapes suivantes
- Pour plus d’informations sur les autres options disponibles pour la configuration des services, consultez la rubrique sur les autres configurations de Cluster Resource Manager disponibles [En savoir plus sur la configuration des services](service-fabric-cluster-resource-manager-configure-services.md)
- Un grand nombre de motifs d’utilisation de l’affinité, par exemple la limitation des services à exécuter sur un petit ensemble de machines virtuelles et la tentative d’agrégation de la charge d’une collection de services, sont en fait mieux pris en charge via les groupes d’applications. Découvrir les [groupes d’applications](service-fabric-cluster-resource-manager-application-groups.md)

[Image1]: ./media/service-fabric-cluster-resource-manager-advanced-placement-rules-affinity/cluster-resrouce-manager-affinity-modes.png
[Image2]: ./media/service-fabric-cluster-resource-manager-advanced-placement-rules-affinity/cluster-resource-manager-chains-vs-stars.png

<!---HONumber=AcomDC_0525_2016-->