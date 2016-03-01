<properties
	pageTitle="Importation de données dans Azure Search | Microsoft Azure | Service de recherche cloud hébergé"
	description="Comment charger des données sur un index dans Azure Search"
	services="search"
	documentationCenter=""
	authors="HeidiSteen"
	manager="mblythe"
	editor=""
    tags=""/>

<tags
	ms.service="search"
	ms.devlang="na"
	ms.workload="search"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na"
	ms.date="02/09/2016"
	ms.author="heidist"/>

# Importer des données dans Azure Search
> [AZURE.SELECTOR]
- [Overview](search-what-is-data-import.md)
- [Portal](search-import-data-portal.md)
- [.NET](search-import-data-dotnet.md)
- [REST](search-import-data-rest-api.md)
- [Indexers](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers-2015-02-28.md)

Dans Azure Search, le service fonctionne sur des données persistantes (index) qui donnent des documents et informations utilisées pour traiter un index, exécuter des requêtes ou formuler des résultats de recherche. Pour remplir un index, vous pouvez utiliser un modèle push ou pull de chargement des données.

Avant l’importation, l’index doit déjà exister. Voir [Index dans Azure Search](search-what-is-an-index.md) pour plus d’informations.

##Envoyer des données à un index

Cette approche concerne la prise en compte d’un jeu de données existant conforme au schéma d’index et de validation à votre service de recherche. Pour les applications ayant des exigences à très faible latence (par exemple, si vous devez rechercher des opérations à synchroniser avec les bases de données de stock), un modèle push est la seule option.

Vous pouvez utiliser l’API REST ou le kit de développement logiciel .NET pour envoyer des données à un index. Il n’existe actuellement aucune prise en charge de l’outil de diffusion de données via le portail.

Cette approche est plus flexible qu’un modèle d’extraction, parce que vous téléchargez des documents individuellement ou par lots (jusqu’à 1 000 par lot ou 16 Mo, quelle que soit la limite atteinte en premier).

##Extraire des données (analyse) 

Un modèle d’extraction analyse une source de données prise en charge et charge l’index pour vous. Dans Azure Search, cette fonctionnalité est implémentée via des *indexeurs* et est actuellement disponible pour la base de données SQL Azure, DocumentDB et SQL Server sur des machines virtuelles Azure. Voir [indexeurs](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers-2015-02-28.md) pour en savoir plus sur le téléchargement de données SQL Azure.

Vous pouvez utiliser le portail, l’API REST ou le kit de développement logiciel .NET pour extraire des données dans un index.

##Configuration de jeu de données requise

Il n’existe aucune restriction sur le type de données que vous téléchargez, à condition que le schéma et les jeux de données soient formulés sous forme de structures JSON.

Les données doivent être tirées de la base de données ou de la source de données que votre application crée ou consomme. Par exemple, si votre application est un catalogue de vente au détail en ligne, l’index que vous créez pour la recherche d’Azure Search doit tirer les données de l’inventaire des produits ou des bases de données client qui prennent en charge votre application.

Le jeu de données doit être issu d’une table, vue, conteneur d’objets blobs ou équivalent unique. Vous devrez peut-être créer une structure de données dans votre application de base de données ou noSQL qui fournit les données à Azure Search. Pour certaines sources de données comme la base de données SQL Azure ou DocumentDB, vous pouvez créer un indexeur qui analyse un tableau externe, une vue ou un conteneur d’objets blobs pour les données à télécharger dans Azure Search.

##Choix d’une approche d’importation de données

|Critères|Approche recommandée|
|------------|---------------|
|Synchronisation des données quasiment en temps réel|Le code, .NET ou API REST, pour envoyer des mises à jour à un index. Une approche « pull » de la réception de données est une opération planifiée, qui ne peut pas s’exécuter assez rapidement pour intégrer des modifications rapides dans une source de données principales.|
|Base de données Azure SQL, DocumentDB ou SQL Server sur les machines virtuelles Azure|Les indexeurs sont ancrés aux types de source de données spécifiques. Si les sources de données primaires font partie des types de source de données pris en charge, un indexeur est le moyen le plus simple de charger un index. Vous pouvez planifier l’actualisation des données à des intervalles d’une heure. Vous pouvez configurer un indexeur dans le portail ou en code.|
|Actualisation des données planifiée|Utilisez un indexeur (voir ci-dessus).|
|Créer des prototypes sans code ou modification|Le portail comprend un Assistant Importation de données qui configure un indexeur qui génère parfois un schéma préliminaire s’il existe suffisamment d’informations dans la base de données principale pour y parvenir. L’Assistant inclut des options permettant de configurer l’actualisation planifiée des données. Si vous le souhaitez, vous pouvez ajouter des analyseurs de langage ou des options de CORS. Il existe quelques inconvénients : il est impossible d’ajouter des profils de score. Vous ne pouvez non plus exporter un schéma créé dans le portail pour un fichier JSON à utiliser dans le code.| 

<!---HONumber=AcomDC_0224_2016-->