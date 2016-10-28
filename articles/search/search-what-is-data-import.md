<properties
    pageTitle="Chargement de données dans Azure Search | Microsoft Azure | Service de recherche cloud hébergé"
    description="Découvrez comment charger des données dans un index dans Azure Search."
    services="search"
    documentationCenter=""
    authors="ashmaka"
    manager=""
    editor=""
    tags=""/>

<tags
    ms.service="search"
    ms.devlang="NA"
    ms.workload="search"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.date="08/29/2016"
    ms.author="ashmaka"/>

# Charger des données dans Azure Search
> [AZURE.SELECTOR]
- [Vue d'ensemble](search-what-is-data-import.md)
- [.NET](search-import-data-dotnet.md)
- [REST](search-import-data-rest-api.md)


## Modèles de chargement de données dans Azure Search
Deux méthodes permettent de renseigner votre index Azure Search avec vos données. La première option consiste à envoyer manuellement vos données dans l’index à l’aide de l’[API REST](search-import-data-rest-api.md) ou du [Kit de développement logiciel (SDK) .NET](search-import-data-dotnet.md) d’Azure Search. La seconde option consiste à [pointer une source de données prise en charge](search-indexer-overview.md) vers votre index Azure Search et à permettre à Azure Search d’extraire automatiquement vos données dans le service de recherche.

Ce guide couvre uniquement les instructions portant sur l’utilisation du modèle d’émission de chargement de données (qui est pris en charge uniquement dans l’[API REST](search-import-data-rest-api.md) et dans le [Kit de développement logiciel (SDK) .NET](search-import-data-dotnet.md)), mais vous pouvez en savoir plus sur le modèle d’émission ci-dessous.

### Envoyer des données à un index

Cette approche désigne l’envoi par programme de vos données à Azure Search pour les rendre disponibles pour la recherche. Pour les applications ayant des exigences à très faible latence (par exemple, si vous devez synchroniser les opérations de recherche avec les bases de données d’inventaire dynamiques), le modèle d’émission est la seule option.

Vous pouvez utiliser l’[API REST](https://msdn.microsoft.com/library/azure/dn798930.aspx) ou le [Kit de développement logiciel (SDK) .NET](search-import-data-dotnet.md) pour envoyer des données à un index. Il n’existe actuellement aucune prise en charge de l’outil de diffusion de données via le portail.

Cette approche est plus flexible que le modèle d’extraction, car vous pouvez charger des documents individuellement ou par lots (jusqu’à 1 000 par lot ou 16 Mo, quelle que soit la limite atteinte en premier). Le modèle d’émission vous permet également de charger des documents dans Azure Search indépendamment de l’emplacement des données.

### Extraire des données dans un index

Le modèle d’extraction analyse une source de données prise en charge et charge automatiquement les données dans votre index Azure Search. En suivant les modifications et les suppressions effectuées dans les documents existants, et en reconnaissant les nouveaux documents, les indexeurs suppriment la nécessité de gérer activement les données de votre index.

Dans Azure Search, cette fonctionnalité est implémentée via des *indexeurs*, actuellement disponibles pour le [stockage d’objets blob (version préliminaire)](search-howto-indexing-azure-blob-storage.md), [DocumentDB](http://aka.ms/documentdb-search-indexer), [Base de données SQL Azure et SQL Server sur les machines virtuelles Azure](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers-2015-02-28.md).

La fonctionnalité de l’indexeur est exposée dans le [portail Azure](search-import-data-portal.md) ainsi que dans l’[API REST](https://msdn.microsoft.com/library/azure/dn946891.aspx).

<!---HONumber=AcomDC_0831_2016-->