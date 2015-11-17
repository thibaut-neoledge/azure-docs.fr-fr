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
	ms.date="11/09/2015"
	ms.author="heidist"/>

# Importer des données dans Azure Search
> [AZURE.SELECTOR]
- [Overview](search-what-is-data-import.md)
- [Portal](search-import-data-portal.md)
- [.NET](search-import-data-dotnet.md)
- [REST API](search-import-data-rest-api.md)
- [Indexers](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers-2015-02-28.md)

Azure Search est un service de recherche cloud hébergé qui propose un moteur de recherche, des fonctionnalités de recherche et le stockage de données. Le service fonctionne sur des données persistantes (index) qui donnent des documents et informations utilisées pour traiter un index, exécuter des requêtes ou formuler des résultats de recherche. Parmi ces opérations de haut niveau, vous pouvez trouver des opérations de bas niveau spécifiques pour l’analyse de texte, le classement, le filtrage, de tri, les choix multiples, et d’autres fonctions utilisées pour calculer ou afficher les résultats de la recherche.

La pertinence de l’expérience de recherche dépend de la qualité des données téléchargées et la fréquence à laquelle elle doit être actualisée. Dans cet article, nous allons vous présenter les approches de type push et pull (analyse) pour l’importation et l’actualisation d’un index.

Avant de pouvoir importer des données, l’index doit exister, et les données que vous téléchargez doivent être conformes au schéma. Voir [Index dans Azure Search](search-what-is-an-index.md) pour plus d’informations.

##Considérations relatives au jeu de données

Il n’existe aucune restriction sur le type de données que vous téléchargez, à condition que le schéma et les jeux de données soient formulés sous forme de structures JSON.

Les données que vous chargez doivent être tirées de la base de données ou de la source de donnes que votre application crée ou consomme. Par exemple, si votre application est un catalogue de vente au détail en ligne, l’index que vous créez pour la recherche d’Azure Search doit tirer les données de l’inventaire des produits ou des bases de données client qui prennent en charge votre application.

Un index dans Azure Search obtient des données à partir d’une table, vue, conteneur d’objets blobs ou équivalent unique. Vous devrez peut-être créer une structure de données dans votre application de base de données ou noSQL qui fournit les données à Azure Search. Pour certaines sources de données comme la base de données SQL Azure ou DocumentDB, vous pouvez créer un indexeur qui analyse un tableau externe, une vue ou un conteneur d’objets blobs pour les données à télécharger dans Azure Search.

##Exigences en matière de latence et de synchronisation des données

La table qui suit est un récapitulatif des exigences et recommandations communes pour les satisfaire.

|Conditions requises|Recommandations|
|------------|---------------|
|Synchronisation des données quasiment en temps réel|Le code, .NET ou API REST, pour envoyer des mises à jour à un index. Une approche « pull » de la réception de données est une opération planifiée, qui ne peut pas s’exécuter assez rapidement pour intégrer des modifications rapides dans une source de données principales.|
|Base de données Azure SQL, DocumentDB ou SQL Server sur les machines virtuelles Azure|Les indexeurs sont ancrés aux types de source de données spécifiques. Si les sources de données principales se trouvent dans une source de données prise en charge, vous pouvez utiliser un indexeur pour analyser la source de données et planifier l’actualisation des données à des intervalles d’une heure. Vous pouvez configurer un indexeur dans le portail ou en code.|
|Actualisation des données planifiée|Utilisez un indexeur (voir ci-dessus).|
|Analyse|Utilisez un indexeur (voir ci-dessus).|
|Créer des prototypes sans code ou modification|Le portail comprend un Assistant Importation de données qui configure un indexeur qui génère parfois un schéma préliminaire s’il existe suffisamment d’informations dans la base de données principale pour y parvenir. L’Assistant inclut des options permettant de configurer l’actualisation planifiée des données. Si vous le souhaitez, vous pouvez ajouter des analyseurs de langage ou des options de CORS. Il existe quelques inconvénients : il est impossible d’ajouter des profils de score. Vous ne pouvez non plus exporter un schéma créé dans le portail pour un fichier JSON à utiliser dans le code.| 

<!---HONumber=Nov15_HO3-->