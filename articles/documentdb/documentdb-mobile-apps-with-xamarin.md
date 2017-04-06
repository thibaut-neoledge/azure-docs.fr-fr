---
title: "Créer des applications mobiles avec Xamarin et Azure DocumentDB | Microsoft Docs"
description: "Didacticiel qui permet de créer une application Xamarin iOS, Android ou Forms à l’aide d’Azure DocumentDB. DocumentDB est une base de données cloud, à l’échelle de la planète et rapide pour les applications mobiles."
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
ms.sourcegitcommit: 6e0ad6b5bec11c5197dd7bded64168a1b8cc2fdd
ms.openlocfilehash: 3e058505f7c17d19d2cebca053badb3505a00f13
ms.lasthandoff: 03/28/2017


---
# <a name="build-mobile-applications-with-xamarin-and-azure-documentdb"></a>Créer des applications mobiles avec Xamarin et Azure DocumentDB
La plupart des applications mobiles doivent stocker des données dans le cloud. Or, Azure DocumentDB est une base de données cloud conçue pour ce type d’application. Elle a tout ce dont un développeur mobile a besoin. Il s’agit d’une base de données NoSQL en tant que service, entièrement gérée, qui évolue en fonction de la demande. Elle peut fournir des données à votre application de manière transparente, où que vos utilisateurs se trouvent dans le monde. Avec le [Kit de développement logiciel (SDK) Azure DocumentDB .NET Core](documentdb-sdk-dotnet-core.md), vous pouvez activer les applications mobiles Xamarin de manière à interagir directement avec DocumentDB, sans niveau intermédiaire.

Dans cet article, nous proposons un didacticiel pour la création d’applications mobiles avec Xamarin et DocumentDB. Vous pouvez trouver le code source complet pour le didacticiel sur [Xamarin et DocumentDB sur GitHub](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/xamarin), y compris des explications sur la gestion des utilisateurs et des autorisations.

## <a name="documentdb-capabilities-for-mobile-apps"></a>Fonctionnalités de DocumentDB pour les applications mobiles
DocumentDB fournit les fonctionnalités clés suivantes aux développeurs d’applications mobiles :

![Fonctionnalités de DocumentDB pour les applications mobiles](media/documentdb-mobile-apps-with-xamarin/documentdb-for-mobile.png)

* Requêtes riches sur des données sans schéma. DocumentDB stocke les données en tant que documents JSON sans schéma dans des collections hétérogènes. Il propose des [requêtes riches et rapides](documentdb-sql-query.md), qui ne nécessitent aucun schéma ou index.
* Débit rapide. Seules quelques millisecondes sont nécessaires pour lire et écrire des documents avec DocumentDB. Les développeurs peuvent spécifier le débit dont ils ont besoin : DocumentDB le respecte avec des SLA de 99,99 %.
* Mise à l’échelle illimitée. Vos collections DocumentDB [augmentent à mesure que votre application grandit](documentdb-partition-data.md). Vous pouvez commencer par des données de petite taille et un débit réduit, soit quelques centaines de requêtes par seconde. Vos collections peuvent inclure jusqu’à plusieurs pétaoctets de données et un débit arbitrairement élevé, soit des centaines de millions de requêtes par seconde.
* Distribution globale. Les utilisateurs d’applications mobiles sont en déplacement, souvent aux quatre coins du monde. Il s’avère que DocumentDB est une [base de données mondialement distribuée](documentdb-distribute-data-globally.md). Cliquez sur la carte pour rendre les données accessibles à vos utilisateurs.
* Autorisation riche intégrée. Avec DocumentDB, vous pouvez facilement implémenter des modèles populaires, comme [les données par utilisateur](https://aka.ms/documentdb-xamarin-todouser) ou les données partagées entre plusieurs utilisateurs, sans recourir à un code d’autorisation complexe personnalisé.
* Requêtes géospatiales. Beaucoup d’applications mobiles proposent aujourd’hui des expériences géocontextuelles. Grâce à la prise en charge de première classe des [types géospatiaux](documentdb-geospatial.md), DocumentDB rend ces expériences faciles à accomplir.
* Pièces jointes binaires. Vos données d’application comprennent souvent des blobs binaires. La prise en charge native des pièces jointes simplifie l’utilisation de DocumentDB en tant que guichet unique pour les données de votre application.

## <a name="documentdb-and-xamarin-tutorial"></a>Didacticiel DocumentDB et Xamarin
Le didacticiel suivant explique comment créer une application mobile à l’aide de Xamarin et DocumentDB. Vous pouvez trouver le code source complet du didacticiel sur [Xamarin et DocumentDB sur GitHub](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/xamarin).

### <a name="get-started"></a>Prise en main
La prise en main de DocumentDB est simple. Utilisez le portail Azure pour créer un compte DocumentDB. Cliquez sur l’onglet **Démarrage rapide**. Téléchargez l’exemple de liste de tâches Xamarin Forms qui est déjà connecté à votre compte DocumentDB. 

![Démarrage rapide de DocumentDB pour les applications mobiles](media/documentdb-mobile-apps-with-xamarin/documentdb-quickstart.png)

Si vous disposez déjà d’une application Xamarin, vous pouvez également ajouter ce [package NuGet DocumentDB](documentdb-sdk-dotnet-core.md). DocumentDB prend en charge les bibliothèques partagées Xamarin.IOS, Xamarin.Android et Xamarin Forms.

### <a name="work-with-data"></a>Utilisation des données
Vos enregistrements de données sont stockés dans DocumentDB en tant que documents JSON sans schéma dans des collections hétérogènes. Vous pouvez stocker des documents avec des structures différentes dans la même collection :

```cs
    var result = await client.CreateDocumentAsync(collectionLink, todoItem);
```

Dans vos projets Xamarin, vous pouvez utiliser des requêtes intégrées au langage sur des données sans schéma :

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
À l’instar de nombreux exemples de prise en main, l’exemple de DocumentDB que vous avez téléchargé s’authentifie auprès du service à l’aide de la clé principale codée en dur dans le code de l’application. Ce comportement par défaut n’est pas judicieux si vous envisagez d’exécuter une application n’importe où sauf sur votre émulateur local. Si un utilisateur non autorisé obtient la clé principale, toutes les données de votre compte DocumentDB risquent d’être compromises. En revanche, vous souhaitez que votre application puisse uniquement accéder aux enregistrements de l’utilisateur connecté. DocumentDB permet aux développeurs d’accorder à l’application un accès en lecture ou en lecture/écriture à une collection, un ensemble de documents regroupés par clé de partition ou un document spécifique. 

Pour transformer l’application de liste de tâches en liste multi-utilisateur, procédez comme suit : 

  1. Ajoutez une connexion à votre application, à l’aide de Facebook, d’Active Directory ou d’un autre fournisseur.

  2. Créez une collection DocumentDB UserItems avec la clé de partition **/userId**. Le fait de spécifier une clé de partition pour votre collection permet la mise à l’échelle infinie de DocumentDB, à mesure que le nombre d’utilisateurs de l’application augmente, pour des requêtes toujours rapides.

  3. Ajoutez DocumentDB Resource Token Broker. Cette API web simple authentifie les utilisateurs et émet des jetons de courte durée pour les utilisateurs connectés, avec un accès limité aux documents se trouvant au sein de la partition de l’utilisateur. Dans cet exemple, nous hébergeons Resource Token Broker dans App Service.

  4. Modifiez l’application pour qu’elle propose une authentification auprès de Resource Token Broker avec Facebook et demande les jetons de ressource pour l’utilisateur Facebook connecté. Vous pouvez ensuite accéder à leurs données dans la collection UserItems.  

Un exemple de code complet de ce modèle est disponible sur [Resource Token Broker sur GitHub](http://aka.ms/documentdb-xamarin-todouser). Ce diagramme illustre la solution :

![Utilisateurs DocumentDB et répartiteur d’autorisations](media/documentdb-mobile-apps-with-xamarin/documentdb-resource-token-broker.png)

Si vous souhaitez que deux utilisateurs puissent accéder à la même liste des tâches, vous pouvez ajouter des autorisations supplémentaires au jeton d’accès dans Resource Token Broker.

### <a name="scale-on-demand"></a>Mise à l’échelle à la demande
DocumentDB est une base de données gérée en tant que service. À mesure que votre base d’utilisateurs augmente, vous n’avez pas à vous soucier de la configuration des machines virtuelles ou de l’augmentation de la mémoire à tores magnétiques. Il vous suffit d’indiquer à DocumentDB quel est le nombre d’opérations par seconde (débit) dont votre application a besoin. Vous pouvez spécifier le débit via l’onglet **Mise à l’échelle** à l’aide d’une mesure de débit appelée Unité de requête par seconde (RU). Par exemple, une opération de lecture d’un document de 1 Ko nécessite 1 RU. Vous pouvez également ajouter des alertes à la mesure **Débit**, afin de surveiller la croissance du trafic et de modifier le débit par programme lorsque des alertes se déclenchent.

![Débit de mise à l’échelle de DocumentDB à la demande](media/documentdb-mobile-apps-with-xamarin/documentdb-scale.png)

### <a name="go-planet-scale"></a>Développement à l’échelle de la planète
À mesure que votre application gagne en popularité, vous pouvez acquérir des utilisateurs aux quatre coins du monde. Peut-être souhaitez-vous pouvoir faire face à des événements imprévus. Accédez au portail Azure et accédez à votre compte DocumentDB. Cliquez sur la carte permettre la réplication continue de vos données dans différentes régions du monde. Grâce à cette fonctionnalité, vos données sont disponibles où que se trouvent vos utilisateurs. Vous pouvez également ajouter des stratégies de basculement, afin d’être prêt à affronter toute situation d’urgence.

![Mise à l’échelle de DocumentDB dans différentes régions géographiques](media/documentdb-mobile-apps-with-xamarin/documentdb-replicate-globally.png)

Félicitations ! Vous avez terminé la solution et disposez d’une application mobile avec Xamarin et DocumentDB. Vous pouvez utiliser une procédure similaire pour créer des applications Cordova, à l’aide du Kit de développement logiciel (SDK) JavaScript DocumentDB, ainsi que des applications iOS/Android natives, à l’aide des API REST DocumentDB.

## <a name="next-steps"></a>Étapes suivantes
* Affichez le code source de [Xamarin et DocumentDB sur GitHub](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/xamarin).
* Téléchargez le [Kit de développement logiciel (SDK) .NET Core de DocumentDB](documentdb-sdk-dotnet-core.md).
* Consultez davantage d’exemples de code pour les [applications .NET](documentdb-dotnet-samples.md).
* Apprenez-en plus sur [les fonctionnalités d’interrogation riches de DocumentDB](documentdb-sql-query.md).
* Apprenez en plus sur [la prise en charge géospatiale dans DocumentDB](documentdb-geospatial.md).




