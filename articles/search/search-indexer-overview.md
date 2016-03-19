<properties
	pageTitle="Indexeurs dans Azure Search | Microsoft Azure | Service de recherche hébergé dans le cloud"
	description="Analysez une base de données pour extraire les données pouvant faire l’objet d’une recherche et remplir un index Azure Search."
	services="search"
	documentationCenter=""
	authors="HeidiSteen"
	manager="mblythe"
	editor=""
    tags="azure-portal"/>

<tags
	ms.service="search"
	ms.devlang="na"
	ms.workload="search"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na"
	ms.date="03/09/2016"
	ms.author="heidist"/>

# Indexeurs dans Azure Search
> [AZURE.SELECTOR]
- [Vue d'ensemble](search-indexer-overview.md)
- [Portail](search-import-data-portal.md)
- [Blob Storage (version préliminaire)](search-howto-indexing-azure-blob-storage.md)
- [Azure SQL](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers-2015-02-28.md)
- [Base de données de documents](../documentdb/documentdb-search-indexer.md)

Dans Azure Search, un **indexeur** est un analyseur qui extrait les données et métadonnées pouvant faire l’objet d’une recherche d’une source de données externe et renseigne un index en fonction des mappages champ à champ entre l’index et votre source de données. Cette approche est parfois appelée « modèle d’extraction », car le service extrait des données sans que vous ayez à écrire un code qui envoie des données à un index.

Vous pouvez utiliser un indexeur comme seul moyen d’ingestion des données ou utiliser une combinaison de techniques qui incluent l’utilisation d’un indexeur pour charger uniquement certains champs dans l’index.

Les indexeurs peuvent être exécutés à la demande ou en fonction d’une planification d’actualisation des données périodique qui s’exécute toutes les quinze minutes. Des mises à jour plus fréquentes requièrent un modèle d’émission qui met à jour simultanément les données dans Azure Search et dans votre source de données externe.

Un indexeur extrait les données d’une **source de données** qui contient des informations telles qu’une chaîne de connexion. Actuellement, les sources de données suivantes sont prises en charge :

- [Base de données SQL Azure](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers-2015-02-28.md) (ou SQL Server dans une machine virtuelle Azure)
- [Base de données de documents](../documentdb/documentdb-search-indexer.md)
- [Azure Blob Storage](search-howto-indexing-azure-blob-storage.md) (actuellement en version préliminaire. Extrait le texte des documents PDF et Office, et de code HTML ou XML.)

Les sources de données sont configurées et gérées indépendamment des indexeurs qui les utilisent. Autrement dit, une source de données peut être utilisée par plusieurs indexeurs pour charger plusieurs index à la fois.

Le [Kit de développement logiciel (SDK) .NET](https://msdn.microsoft.com/library/azure/microsoft.azure.search.iindexersoperations.aspx) et l’[API REST du service](https://msdn.microsoft.com/library/azure/dn946891.aspx) prennent en charge la gestion des indexeurs et des sources de données.

Vous pouvez également configurer un indexeur dans le portail lorsque vous utilisez l’Assistant **Importation de données**. Consultez la page [Prise en main d’Azure Search dans le portail](search-get-started-portal) pour accéder à un didacticiel rapide qui utilise les exemples de données et l’indexeur DocumentDB pour créer et charger un index à l’aide de l’Assistant.

<!---HONumber=AcomDC_0309_2016-->