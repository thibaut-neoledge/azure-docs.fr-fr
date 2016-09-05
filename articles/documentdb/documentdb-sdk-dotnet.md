<properties 
	pageTitle="API et kit SDK .NET DocumentDB | Microsoft Azure" 
	description="Découvrez l'API et le Kit SDK .NET, notamment les dates de lancement, les dates de suppression et les modifications apportées entre chaque version du Kit SDK .NET DocumentDB." 
	services="documentdb" 
	documentationCenter=".net" 
	authors="rnagpal" 
	manager="jhubbard" 
	editor="cgronlun"/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="08/24/2016" 
	ms.author="rnagpal"/>

# API DocumentDB et Kits de développement logiciel (SDK) 

> [AZURE.SELECTOR]
- [.NET](documentdb-sdk-dotnet.md)
- [Node.JS](documentdb-sdk-node.md)
- [Java](documentdb-sdk-java.md)
- [Python](documentdb-sdk-python.md)
- [REST](https://go.microsoft.com/fwlink/?LinkId=402413)
- [SQL](https://msdn.microsoft.com/library/azure/dn782250.aspx)

## API .NET DocumentDB et Kit de développement logiciel (SDK)

<table>
<tr><td>**Téléchargement du Kit de développement logiciel (SDK)**</td><td>[NuGet](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/)</td></tr>
<tr><td>**Documentation de l'API**</td><td>[Documentation de référence de l'API .NET](https://msdn.microsoft.com/library/azure/dn948556.aspx)</td></tr>
<tr><td>**Exemples**</td><td>[Exemples de code .NET] (documentdb-dotnet-samples.md)</td></tr>
<tr><td>**Prise en main**</td><td>[Prise en main du Kit SDK .NET DocumentDB] (documentdb-get-started.md)</td></tr>
<tr><td>** Didacticiel d’application web **</td><td>[Développement d’applications web avec DocumentDB] (documentdb-dotnet-application.md)</td></tr>
<tr><td>**Infrastructure actuellement prise en charge**</td><td>[Microsoft .NET Framework 4.5](https://www.microsoft.com/download/details.aspx?id=30653)</td></tr>
</table></br>

## Notes de publication

> [AZURE.IMPORTANT] Vous pouvez recevoir une System.NotSupportedException lors de l’interrogation de collections partitionnées. Pour éviter cette erreur, désactivez l’option « Préférer 32 bits » dans la fenêtre Propriétés de votre projet, sous l’onglet Build.

### <a name="1.9.4"/>[1\.9.4](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.9.4)

  - Modification des classes ResourceResponse, FeedResponse, StoredProcedureResponse et MediaResponse pour implémenter l’interface publique correspondante afin de pouvoir les simuler facilement pour les déploiements basés sur les tests (TDD).
  - Résolution du problème qui provoquait la malformation d’un en-tête de clé de partition lors de l’utilisation d’un objet JsonSerializerSettings personnalisé pour la sérialisation des données.

### <a name="1.9.3"/>[1\.9.3](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.9.3)

  - Résolution du problème ayant provoqué l’échec des requêtes de longue durée avec l’erreur : le jeton d’autorisation n’est pas valide pour le moment.
  - Résolution du problème qui a supprimé la classe SqlParameterCollection d’origine des requêtes Top/Order-by inter-partition.

### <a name="1.9.2"/>[1\.9.2](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.9.2)

  - Prise en charge ajoutée de requêtes parallèles pour les collections partitionnées.
  - Prise en charge ajoutée des requêtes ORDER BY et TOP sur plusieurs partitions pour les collections partitionnées.
  - Résolution des références manquantes dans DocumentDB.Spatial.Sql.dll et Microsoft.Azure.Documents.ServiceInterop.dll qui sont nécessaires lors du référencement d’un projet DocumentDB avec une référence au package DocumentDB Nuget.
  - Résolution des problèmes liés à la possibilité d’utiliser des paramètres de types différents lors de l’utilisation de fonctions définies par l’utilisateur par le dans LINQ.
  - Correction d’un bogue pour les comptes de réplication globale lorsque des appels Upsert étaient transmis vers des emplacements de lecture au lieu d’emplacements d’écriture.
  - Méthodes ajoutées à l’interface IDocumentClient qui étaient absentes :
      - Méthode UpsertAttachmentAsync qui accepte mediaStream et les options en tant que paramètres
      - Méthode CreateAttachmentAsync qui accepte les options en tant que paramètres
      - Méthode CreateOfferQuery qui accepte querySpec en tant que paramètre.
  - Des classes publiques non scellées qui sont exposées dans l’interface IDocumentClient.

### <a name="1.8.0"/>[1\.8.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.8.0)
  - Ajout de la prise en charge des comptes de base de données de plusieurs régions.
  - Ajout de la prise en charge d’une nouvelle tentative pour les requêtes limitées. L’utilisateur peut personnaliser le nombre de nouvelles tentatives et le temps d’attente maximal en configurant la propriété ConnectionPolicy.RetryOptions.
  - Ajout d’une nouvelle interface IDocumentClient qui définit les signatures de toutes les propriétés et méthodes DocumenClient. Dans le cadre de cette modification, les méthodes d’extension qui créent des propriétés IQueryable et IOrderedQueryable ont également été converties en méthodes de la classe DocumentClient.
  - Ajout d’une option de configuration permettant de définir la propriété ServicePoint.ConnectionLimit pour un URI de point de terminaison DocumentDB donné. Utilisez ConnectionPolicy.MaxConnectionLimit pour modifier la valeur par défaut, définie sur 50.
  - Propriété IPartitionResolver déconseillée et son implémentation. La prise en charge de IPartitionResolver est désormais obsolète. Il est recommandé d’utiliser des collections partitionnées pour bénéficier d’un niveau de stockage et de débit supérieur.


### <a name="1.7.1"/>[1\.7.1](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.7.1)
  - Ajout d’une surcharge à l’URI basée sur la méthode ExecuteStoredProcedureAsync qui prend RequestOptions comme paramètre.
  
### <a name="1.7.0"/>[1\.7.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.7.0)
  - Ajout de la prise en charge de la durée de vie (TTL) pour les documents.

### <a name="1.6.3"/>[1\.6.3](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.6.3)
  - Correction d’un bogue dans l’empaquetage Nuget du Kit de développement logiciel (SDK) .NET pour son empaquetage en tant que partie d’une solution Azure Service Cloud.
  
### <a name="1.6.2"/>[1\.6.2](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.6.2)
  - Implémentation des [collections partitionnées](documentdb-partition-data.md) et des [niveaux de performances définis par l’utilisateur](documentdb-performance-levels.md).

### <a name="1.5.3"/>[1\.5.3](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.5.3)
  - **[Résolu]** L’interrogation du point de terminaison DocumentDB retourne : « System.Net.Http.HttpRequestException : Une erreur s’est produite lors de la copie du contenu vers un flux ».

### <a name="1.5.2"/>[1\.5.2](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.5.2)
  - Prise en charge LINQ étendue, y compris de nouveaux opérateurs pour la pagination, les expressions conditionnelles et la comparaison de plages.
    - Opérateur Take pour activer le comportement SELECT TOP dans LINQ
    - Opérateur CompareTo pour activer les comparaisons de plages de chaînes
    - Opérateurs Conditional (?) et Coalesce (??)
  - **[Résolu]** ArgumentOutOfRangeException en cas de combinaison de la projection de modèle avec Where-In dans la requête linq. [No.81](https://github.com/Azure/azure-documentdb-dotnet/issues/81)

### <a name="1.5.1"/>[1\.5.1](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.5.1)
 - **[Résolu]** Si la dernière expression n’est pas Select, le fournisseur LINQ n’effectuait aucune projection et créait une expression SELECT * incorrecte. [#58](https://github.com/Azure/azure-documentdb-dotnet/issues/58)

### <a name="1.5.0"/>[1\.5.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.5.0)
 - Mise en œuvre de l’opération Upsert, ajout des méthodes UpsertXXXAsync
 - Améliorations des performances pour toutes les requêtes
 - Prise en charge par le fournisseur LINQ des méthodes conditionnelles, de regroupement et CompareTo pour les chaînes
 - **[Résolu]** Fournisseur LINQ--> Mise en œuvre de la méthode Contains sur List pour générer le même SQL que sur IEnumerable et Array
 - **[Résolu]** BackoffRetryUtility utilise de nouveau le même HttpRequestMessage au lieu d’en créer un nouveau à chaque tentative
 - **[Obsolète]** UriFactory.CreateCollection --> doit désormais utiliser UriFactory.CreateDocumentCollection
 
### <a name="1.4.1"/>[1\.4.1](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.4.1)
 - **[Résolu]** Erreurs de localisation lors de l’utilisation d’autres langues que l’anglais, comme nl-NL, etc.
 
### <a name="1.4.0"/>[1\.4.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.4.0)
  - Routage basé sur l’ID
    - Nouvelle application d’assistance UriFactory pour faciliter la construction de liens de ressources basés sur l’ID
    - Nouvelles surcharges sur DocumentClient pour prendre en compte l’URI
  - Ajout de IsValid() et IsValidDetailed() dans LINQ pour les données géospatiales
  - Amélioration de la prise en charge du fournisseur LINQ
    - **Mathématiques** - Abs, Acos, Asin, Atan, Ceiling, Cos, Exp, Floor, Log, Log10, Pow, Round, Sign, Sin, Sqrt, Tan, Truncate
    - **Chaîne** - Concat, Contains, EndsWith, IndexOf, Count, ToLower, TrimStart, Replace, Reverse, TrimEnd, StartsWith, SubString, ToUpper
    - **Tableau** - Concat, Contains, Count
    - Opérateur **IN**

### <a name="1.3.0"/>[1\.3.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.3.0)
  - Ajout de la prise en charge de la modification des stratégies d’indexation
    - Nouvelle méthode ReplaceDocumentCollectionAsync dans DocumentClient
    - Nouvelle propriété IndexTransformationProgress dans ResourceResponse<T> pour le suivi de l’évolution du pourcentage de modification de la stratégie d’indexation
    - DocumentCollection.IndexingPolicy est désormais mutable
  - Ajout de la prise en charge de l’indexation et des requêtes spatiales
    - Nouvel espace de noms Microsoft.Azure.Documents.Spatial pour la sérialisation/désérialisation des types de données spatiales comme Point et Polygon
    - Nouvelle classe SpatialIndex pour l’indexation des données GeoJSON stockées dans DocumentDB
  - **[Résolu]** : requête SQL incorrecte générée à partir de l’expression linq [#38](https://github.com/Azure/azure-documentdb-net/issues/38)

### <a name="1.2.0"/>[1\.2.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.2.0)
- Dépendance de Newtonsoft.Json v5.0.7
- Modifications pour prendre en charge la clause Order By
  - Prise en charge du fournisseur LINQ pour OrderBy() ou OrderByDescending()
  - IndexingPolicy pour prendre en charge la clause Order By
  
		**NB: Possible breaking change** 
  
    	If you have existing code that provisions collections with a custom indexing policy, then your existing code will need to be updated to support the new IndexingPolicy class. If you have no custom indexing policy, then this change does not affect you.

### <a name="1.1.0"/>[1\.1.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.1.0)
- Prise en charge du partitionnement des données à l’aide des nouvelles classes HashPartitionResolver et RangePartitionResolver et de IPartitionResolver
- Sérialisation DataContract
- Prise en charge des Guid dans le fournisseur LINQ
- Prise en charge UDF dans LINQ

### <a name="1.0.0"/>[1\.0.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.0.0)
- Kit SDK GA

> [AZURE.NOTE]
Une modification du nom du package NuGet entre l’aperçu et la disponibilité générale a eu lieu. Nous sommes passés de **Microsoft.Azure.Documents.Client** à **Microsoft.Azure.DocumentDB** <br/>


### <a name="0.9.x-preview"/>[0\.9.x-aperçu](https://www.nuget.org/packages/Microsoft.Azure.Documents.Client)
- Kits de développement logiciel (SDK) en version préliminaire [Obsolète]

## Dates de lancement et de suppression
Microsoft fournira une notification au moins **12 mois** avant le retrait d’un Kit de développement logiciel (SDK) pour faciliter la transition vers une version plus récente/prise en charge.

Les nouvelles fonctionnalités et fonctions, et les optimisations sont uniquement ajoutées au Kit SDK actuel. Par conséquent, il est recommandé de toujours passer à la dernière version du SDK dès que possible.

Toute requête à DocumentDB utilisant un Kit SDK supprimé est rejetée par le service.

> [AZURE.WARNING]
Toutes les versions du Kit de développement logiciel (SDK) Azure DocumentDB pour .NET antérieures à la version **1.0.0** seront supprimées le **29 février 2016**.
 
<br/>
 
| Version | Date de lancement | Date de suppression 
| ---	  | ---	         | ---
| [1\.9.4](#1.9.4) | 24 août 2016 |--- 
| [1\.9.3](#1.9.3) | 15 août 2016 |--- 
| [1\.9.2](#1.9.2) | 23 juillet 2016 |--- 
| 1.9.1 | Déconseillé |--- 
| 1.9.0 | Déconseillé |--- 
| [1\.8.0](#1.8.0) | 14 juin 2016 |--- 
| [1\.7.1](#1.7.1) | 6 mai 2016 |--- 
| [1\.7.0](#1.7.0) | 26 avril 2016 |--- 
| [1\.6.3](#1.6.3) | 8 avril 2016 |--- 
| [1\.6.2](#1.6.2) | 29 mars 2016 |--- 
| [1\.5.3](#1.5.3) | 19 février 2016 |--- 
| [1\.5.2](#1.5.2) | 14 décembre 2015 |--- 
| [1\.5.1](#1.5.1) | 23 décembre 2015 |--- 
| [1\.5.0](#1.5.0) | 5 octobre 2015 |--- 
| [1\.4.1](#1.4.1) | 25 août 2015 |--- 
| [1\.4.0](#1.4.0) | 13 août 2015 |--- 
| [1\.3.0](#1.3.0) | 5 août 2015 |--- 
| [1\.2.0](#1.2.0) | 6 juillet 2015 |--- 
| [1\.1.0](#1.1.0) | 30 avril 2015 |--- 
| [1\.0.0](#1.0.0) | 8 avril 2015 |--- 
| [Version préliminaire 0.9.3](#0.9.x-preview) | 12 mars 2015 | 29 février 2016 
| [Version préliminaire 0.9.2](#0.9.x-preview) | Janvier 2015 | 29 février 2016 
| [Version préliminaire 0.9.1](#0.9.x-preview) | 13 octobre 2014 | 29 février 2016 
| [Version préliminaire 0.9.0](#0.9.x-preview) | 21 août 2014 | 29 février 2016

## Forum Aux Questions
[AZURE.INCLUDE [documentdb-sdk-faq](../../includes/documentdb-sdk-faq.md)]

## Voir aussi

Pour en savoir plus sur DocumentDB, consultez la page du service [Microsoft Azure DocumentDB](https://azure.microsoft.com/services/documentdb/).

<!---HONumber=AcomDC_0824_2016-->