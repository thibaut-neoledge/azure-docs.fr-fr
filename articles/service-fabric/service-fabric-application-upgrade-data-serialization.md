<properties
   pageTitle="Mise à niveau d'une application : sérialisation des données | Microsoft Azure"
   description="Meilleures pratiques pour la sérialisation de données et son impact sur le déploiement des mises à niveau d'applications."
   services="service-fabric"
   documentationCenter=".net"
   authors="jessebenson"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="04/14/2016"
   ms.author="jesseb"/>


# Impact de la sérialisation des données sur la mise à niveau d’une application

Dans un [mise à niveau d'application propagée](service-fabric-application-upgrade.md), la mise à niveau est appliquée à un sous-ensemble de nœuds, à raison d'un domaine de mise à niveau à la fois. Pendant ce processus, certains domaines de mise à niveau se trouvent sur la version la plus récente de votre application, tandis que d'autres se trouvent sur la version antérieure. Pendant le déploiement, la nouvelle version de votre application doit être en mesure de lire l’ancienne version de vos données, tandis que l’ancienne version de votre application doit être à même de lire la nouvelle version de vos données. Si le format de données n’offre pas une compatibilité ascendante et descendante, la mise à niveau peut échouer ou, pire, des données peuvent être perdues ou endommagées. Cet article explique ce qui constitue le format de données et détaille les méthodes recommandées pour que les données offrent une compatibilité ascendante et descendante.


## Qu'est-ce qui constitue le format de données ?

Dans Azure Service Fabric, les données qui sont rendues persistantes et répliquées proviennent de vos classes C#. Dans le cas des applications utilisant des [collections fiables](service-fabric-reliable-services-reliable-collections.md), il s’agit des objets qui se trouvent dans les files d’attente et les dictionnaires fiables. Dans le cas des applications utilisant [Reliable Actors](service-fabric-reliable-actors-introduction.md), il s’agit de l’état de sauvegarde de l’acteur. Ces classes C# doivent être sérialisables pour être persistantes et répliquées. Le format de données est donc défini par les champs et propriétés sérialisés, ainsi que par la manière dont ils sont sérialisés. Par exemple, dans un `IReliableDictionary<int, MyClass>`, les données sont un `int` sérialisé et un `MyClass` sérialisé.

### Modifications du code résultant dans une modification du format de données

Le format de données étant déterminé par les classes C#, les modifications apportées aux classes peuvent entraîner une modification du format de données. Veillez à ce que la mise à niveau propagée puisse gérer la modification du format de données. Exemples d'opérations pouvant entraîner une modification du format de données :

- Ajout ou suppression de champs ou de propriétés
- Modification du nom d'un champ ou d'une propriété
- Modification des types de champs ou de propriétés
- Modification du nom de la classe ou de l'espace de noms

### Le contrat de données est le sérialiseur par défaut.

Le sérialiseur est généralement chargé de lire les données et de les désérialiser dans la version actuelle, même si les données sont dans une version antérieure ou *plus récente*. Le sérialiseur par défaut est le [sérialiseur de contrat de données](https://msdn.microsoft.com/library/ms733127.aspx), qui possède des règles de gestion de version bien définies. Les collections fiables permettent la substitution du sérialiseur, contrairement à Reliable Actors. Le sérialiseur de données joue un rôle important dans l'activation des mises à niveau propagées. Le sérialiseur de contrat de données est le sérialiseur recommandé pour les applications Service Fabric.


## Impact du format de données sur la mise à niveau propagée

Pendant une mise à niveau propagée, il existe deux scénarios principaux où le sérialiseur peut rencontrer une version antérieure ou *plus récente* de vos données :

1. Une fois qu'un nœud est mis à niveau et démarre la sauvegarde, le nouveau sérialiseur charge les données qui ont été rendues persistantes sur le disque par l'ancienne version.
2. Au cours de la mise à niveau propagée, le cluster contiendra une combinaison des versions ancienne et nouvelle de votre code. Dans la mesure où les réplicas peuvent être placés dans différents domaines de mise à niveau, et que les réplicas s’envoient mutuellement des données, les versions anciennes et/ou nouvelles de vos données peuvent être rencontrées par la nouvelle et/ou ancienne version de votre sérialiseur.

> [AZURE.NOTE] « nouvelle version » et « ancienne version » font ici référence à la version de votre code en cours d'exécution. Le terme « nouveau sérialiseur » fait référence au code de sérialiseur s’exécutant dans la nouvelle version de votre application. « nouvelles données » fait référence à la classe C# sérialisée issue de la nouvelle version de votre application.

Les deux versions de format de données et de code doivent être mutuellement compatibles. Si elles ne le sont pas, la mise à niveau propagée risque d'échouer ou des données peuvent être perdues. La mise à niveau propagée risque d’échouer, car le code ou le sérialiseur peut lever des exceptions ou une erreur quand il rencontre l’autre version. Des données peuvent être perdues si, par exemple, une nouvelle propriété a été ajoutée, mais que l'ancien sérialiseur l'ignore pendant la désérialisation.

Le contrat de données est la solution recommandée pour s’assurer de la compatibilité des données. Il possède des règles de gestion de version bien définies pour l'ajout, la suppression et la modification de champs. En outre, il prend en charge les champs inconnus, avec raccordement au processus de sérialisation et de désérialisation, ainsi que l’héritage de classe. Pour plus d'informations, consultez la page [Utilisation du contrat de données](https://msdn.microsoft.com/library/ms733127.aspx).


## Étapes suivantes

La [mise à niveau de votre application à l'aide de Visual Studio](service-fabric-application-upgrade-tutorial.md) vous guide à travers une mise à niveau de l'application à l'aide de Visual Studio.

La [mise à niveau de votre application à l'aide de Powershell](service-fabric-application-upgrade-tutorial-powershell.md) vous guide à travers une mise à niveau de l'application à l'aide de PowerShell.

Contrôlez les mises à niveau de votre application à l'aide des [Paramètres de mise à niveau](service-fabric-application-upgrade-parameters.md).

Apprenez à utiliser les fonctionnalités avancées lors de la mise à niveau de votre application en consultant les [Rubriques avancées](service-fabric-application-upgrade-advanced.md).

Résolvez les problèmes courants de mise à niveau de l'application en vous reportant aux étapes de [Résolution des problèmes de mise à niveau des applications](service-fabric-application-upgrade-troubleshooting.md).
 

<!---HONumber=AcomDC_0420_2016-->