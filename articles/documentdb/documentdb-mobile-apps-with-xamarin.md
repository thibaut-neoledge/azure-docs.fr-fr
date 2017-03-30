---
title: "Créer des applications mobiles avec Xamarin et Azure DocumentDB | Microsoft Docs"
description: "Un didacticiel qui crée une application Xamarin iOS, Android ou Forms à l’aide d’Azure DocumentDB. DocumentDB est une base de données cloud, à l’échelle de la planète et ultra rapide pour les applications mobiles."
services: documentdb
documentationcenter: .net
author: arramac
manager: monicar
editor: 
ms.assetid: ff97881a-b41a-499d-b7ab-4f394df0e153
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/20/2017
ms.author: arramac
translationtype: Human Translation
ms.sourcegitcommit: 424d8654a047a28ef6e32b73952cf98d28547f4f
ms.openlocfilehash: 4a783397b038b7545bbbfc45813db3d9973c5e19
ms.lasthandoff: 03/22/2017


---
# <a name="build-mobile-applications-with-xamarin-and-azure-documentdb"></a>Créer des applications mobiles avec Xamarin et Azure DocumentDB
La plupart des applications mobiles doivent stocker des données dans le cloud, et Azure DocumentDB est une base de données cloud pour les applications mobiles. Elle a tout ce dont un développeur mobile a besoin. Il s’agit d’une base de données NoSQL en tant que service entièrement gérée qui s’adapte à la demande et qui peut, de manière transparente, apporter vos données là où vos utilisateurs vont dans le monde entier, à votre application. Avec le [Kit de développement logiciel (SDK) Azure DocumentDB .NET Core](documentdb-sdk-dotnet-core.md) vous pouvez activer les applications mobiles Xamarin pour interagir directement avec DocumentDB, sans niveau intermédiaire.

Dans cet article, nous proposons un didacticiel pour la création d’applications mobiles avec Xamarin et DocumentDB. Vous pouvez trouver le code source complet pour le didacticiel sur [Xamarin et DocumentDB sur Github](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/xamarin), y compris des explications sur la gestion des utilisateurs et des autorisations.

## <a name="documentdb-capabilities-for-mobile-apps"></a>Fonctionnalités de DocumentDB pour les applications mobiles
DocumentDB fournit les fonctionnalités clés suivantes aux développeurs d’applications mobiles :

![Fonctionnalités de DocumentDB pour les applications mobiles](media/documentdb-mobile-apps-with-xamarin/documentdb-for-mobile.png)

* Requêtes riches sur des données sans schéma. DocumentDB stocke les données en tant que documents JSON sans schéma dans des collections hétérogènes et offre des [requêtes riches et rapides](documentdb-sql-query.md) sans avoir à s’occuper des schémas ou des index.
* Rapidité. Conformité. Seules quelques millisecondes sont nécessaires pour lire et écrire des documents avec DocumentDB. Les développeurs peuvent spécifier le débit dont ils ont besoin et DocumentDB le respecte avec des contrats de niveau de service de 99,99 %.
* Mise à l’échelle illimitée. Vos collections DocumentDB [augmentent à mesure que votre application grandit](documentdb-partition-data.md). Vous pouvez démarrer avec un volume de données minime et des centaines de demandes par seconde et atteindre arbitrairement un débit de dizaines et centaines de millions de demandes par seconde et des pétaoctets de données.
* Distribution globale. Les utilisateurs votre application mobile sont en déplacement, souvent aux quatre coins du monde. DocumentDB est une [base de données distribuée globalement](documentdb-distribute-data-globally.md), et avec un simple clic sur une carte elle apporte les données, quel que soit l’endroit où se trouve vos utilisateurs.
* Autorisation riche intégrée. Avec DocumentDB, vous pouvez facilement implémenter des modèles populaires comme [les données par utilisateur](https://aka.ms/documentdb-xamarin-todouser) ou les données partagées entre plusieurs utilisateurs avec un code d’autorisation complexe personnalisé.
* Requêtes géospatiales. Beaucoup d’applications mobiles proposent aujourd’hui des expériences géocontextuelles. Avec la prise en charge de première classe pour [les types géospatiaux](documentdb-geospatial.md), DocumentDB rend ces expériences faciles à accomplir.
* Pièces jointes binaires. Vos données d’application comprennent souvent des blobs binaires. La prise en charge native des pièces jointes simplifie l’utilisation de DocumentDB comme guichet unique pour les données de votre application.

## <a name="documentdb-and-xamarin-tutorial"></a>Didacticiel DocumentDB et Xamarin
Le didacticiel suivant montre comment créer une application mobile à l’aide de Xamarin et DocumentDB en cinq étapes simples. Vous pouvez trouver le code source complet pour le didacticiel sur [Xamarin et DocumentDB sur Github](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/xamarin).

### <a name="get-started"></a>Prise en main
La prise en main de DocumentDB est simple. Il vous suffit d’accéder au portail Azure, de créer un compte DocumentDB, puis de cliquer sur l’onglet Démarrage rapide et de télécharger un exemple de « liste des tâches » Xamarin Forms, déjà connecté à votre compte DocumentDB. 

![Démarrage rapide de DocumentDB pour les applications mobiles](media/documentdb-mobile-apps-with-xamarin/documentdb-quickstart.png)

Ou si vous disposez déjà d’une application Xamarin, vous pouvez ajouter ce [package NuGet DocumentDB](documentdb-sdk-dotnet-core.md). DocumentDB prend en charge les bibliothèques partagées Xamarin.IOS, Xamarin.Android et Xamarin Forms.

### <a name="work-with-data"></a>Utilisation des données
Vos enregistrements de données sont stockés dans DocumentDB en tant que documents JSON sans schéma dans des collections hétérogènes. Vous pouvez stocker des documents avec des structures différentes dans la même collection.

```cs
    var result = await client.CreateDocumentAsync(collectionLink, todoItem);
```

Dans vos projets Xamarin, vous pouvez utiliser des requêtes intégrées au langage sur des données sans schéma :

```cs
    var query = await client.CreateDocumentQuery<ToDoItem>(collectionLink)
                    .Where(todoItem => todoItem.Complete == false)
                    .AsDocumentQuery();

    Items = new List<TodoItem>();
    while (query.HasMoreResults) {
        Items.AddRange(await query.ExecuteNextAsync<TodoItem>());
    }
```
### <a name="add-users"></a>Ajouter des utilisateurs
À l’instar de nombreux exemples de prise en main, l’exemple de DocumentDB que vous avez téléchargé s’authentifie auprès du service à l’aide de la clé principale codée en dur dans le code de l’application. Cette pratique n’est pas judicieuse si vous essayez d’exécuter une application n’importe où sauf sur votre émulateur local. Si un pirate obtient la clé principale, toutes les données de votre compte DocumentDB sont compromises. Au lieu de cela, nous voulons que notre application n’ait accès qu’aux enregistrements pour l’utilisateur connecté. DocumentDB permet aux développeurs d’accorder un accès en lecture ou en lecture/écriture à l’application pour une collection, un ensemble de documents regroupés par une clé de partition ou un document spécifique. 

Voici, par exemple, comment modifier notre application de liste de tâches en application de « liste de tâches » multi-utilisateur : 

* Ajoutez une connexion à votre application, à l’aide de Facebook, d’Active Directory ou d’un autre fournisseur.
* Créez une collection DocumentDB UserItems collection avec /userId comme clé de partition. Le fait de spécifier une clé de partition pour votre collection permet à DocumentDB d’effectuer une mise à l’échelle infinie à mesure que le nombre d’utilisateurs de notre application augmente, tout en offrant des requêtes rapides.
* Ajoutez DocumentDB Resource Token Broker, une API web simple qui authentifie les utilisateurs et émet des jetons de courte durée pour les utilisateurs connectés avec un accès limité aux documents se trouvant au sein de la partition de l’utilisateur. Dans cet exemple, nous hébergeons Resource Token Broker dans App Service.
* Modifiez l’application pour qu’elle propose une authentification auprès de Resource Token Broker avec Facebook et demande les jetons de ressource pour l’utilisateur Facebook connecté, puis accède aux données utilisateur dans la collection UserItems.  

Un exemple de code complet de ce modèle est disponible sur [Resource Token Broker sur Github](http://aka.ms/documentdb-xamarin-todouser). Ce diagramme illustre la solution :

![Utilisateurs DocumentDB et répartiteur d’autorisations](media/documentdb-mobile-apps-with-xamarin/documentdb-resource-token-broker.png)

Si nous voulons maintenant que deux utilisateurs puissent accéder à la même « liste des tâches », nous devons ajouter des autorisations supplémentaires au jeton d’accès dans Resource Token Broker.

### <a name="scale-on-demand"></a>Mise à l’échelle à la demande
DocumentDB est une base de données gérée en tant que service. À mesure que votre base d’utilisateurs augmente, vous n’avez pas à vous soucier de la configuration des machines virtuelles ou de l’augmentation de la mémoire à tores magnétiques. Il vous suffit d’indiquer à DocumentDB quel est le nombre d’opérations par seconde (débit) dont votre application a besoin. Vous pouvez spécifier le débit via l’onglet Mise à l’échelle du portail à l’aide d’une mesure de débit appelée Unités de requête par seconde (RU). Par exemple, une opération de lecture d’un document de 1 Ko nécessite 1 RU. Vous pouvez également ajouter des alertes pour la mesure « Débit », afin de surveiller la croissance du trafic et de modifier le débit par programme quand des alertes se déclenchent.

![Débit de mise à l’échelle de DocumentDB à la demande](media/documentdb-mobile-apps-with-xamarin/documentdb-scale.png)

### <a name="go-planet-scale"></a>Développement à l’échelle de la planète
À mesure que votre application gagne en popularité, vous pouvez acquérir des utilisateurs des quatre coins du monde. Ou, vous ne souhaitez simplement pas être pris au dépourvu si une météorite tombe sur les centres de données Azure dans lesquels vous avez créé votre collection DocumentDB. Accédez au portail Azure, à votre compte DocumentDB et en un clic sur une carte, rendez la réplication de vos données possible en continu dans le nombre de régions du monde que vous voulez. Cela garantit la disponibilité de vos données dans tous les endroits où vos utilisateurs se trouvent, et vous pouvez ajouter des stratégies de basculement pour être prêt en cas de problèmes.

![Mise à l’échelle de DocumentDB dans différentes régions géographiques](media/documentdb-mobile-apps-with-xamarin/documentdb-replicate-globally.png)

Félicitations ! Vous avez terminé la solution et disposez d’une application mobile avec Xamarin et DocumentDB. Un modèle similaire peut être utilisé dans les applications Cordova à l’aide du Kit de développement logiciel (SDK) JavaScript DocumentDB, ainsi que dans les applications iOS/Android natives à l’aide des API REST DocumentDB.

## <a name="next-steps"></a>Étapes suivantes
* Affichez le code source de [Xamarin et DocumentDB sur Github](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/xamarin).
* Téléchargez le [Kit de développement logiciel (SDK) .NET Core de DocumentDB](documentdb-sdk-dotnet-core.md).
* Consultez davantage d’exemples de code pour les [applications .NET](documentdb-dotnet-samples.md).
* Apprenez-en plus sur [les fonctionnalités d’interrogation riches de DocumentDB](documentdb-sql-query.md).
* Apprenez en plus sur [la prise en charge géospatiale dans DocumentDB](documentdb-geospatial.md).




