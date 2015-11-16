<properties 
	pageTitle="Nouveautés de la dernière mise à jour d’Azure Search | Microsoft Azure" 
	description="Notes de publication relatives à Azure Search sur les dernières mises à jour du service" 
	services="search" 
	documentationCenter="" 
	authors="HeidiSteen" 
	manager="mblythe" 
	editor=""/>

<tags 
	ms.service="search" 
	ms.devlang="rest-api" 
	ms.workload="search" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.date="11/04/2015" 
	ms.author="heidist"/>

#Nouveautés de la dernière mise à jour d’Azure Search#

Azure Search est un service de recherche cloud hébergé sur Microsoft Azure. Il est mis à la disposition générale avec un contrat de niveau de service (SLA) affichant une disponibilité de 99,9 % et dédié aux configurations prises en charge pour la [version 2015-02-28 de l'API](https://msdn.microsoft.com/library/azure/dn798935.aspx).

##Contrôle de version et déploiement des fonctionnalités

Les fonctionnalités sont publiées séparément ou conjointement par le biais de l’[API REST](https://msdn.microsoft.com/library/azure/dn798935.aspx), du [Kit de développement logiciel (SDK) .NET](http://go.microsoft.com/fwlink/?LinkId=528216) ou du tableau de bord des services du [portail Azure](https://portal.azure.com).

La bibliothèque .NET et les API REST existent dans différentes versions. Les API plus anciennes restent opérationnelles lors du déploiement de nouvelles fonctionnalités. Vous pouvez consulter [Contrôle de version de service Azure Search](https://msdn.microsoft.com/library/azure/dn864560.aspx) pour en savoir plus sur notre stratégie de contrôle de version.


##Version d’API 2015-02-28-Preview
**Date de nouvelle publication : septembre 2015**

Cette version ajoute une nouvelle [prise en charge de la syntaxe des requêtes Lucene](https://msdn.microsoft.com/library/azure/mt589323.aspx) qui peut être utilisée sur la [version préliminaire de l’API REST du service Azure Search](search-api-2015-02-28-preview.md). Pour utiliser la nouvelle syntaxe, vous devez spécifier le `queryType` dans une opération de recherche de documents.

En outre, les deux fonctionnalités suivantes ont quitté la version préliminaire pour rejoindre l'API officielle sur MSDN : - [Processeurs en langage naturel](search-language-support.md) - Opérations POST dans la recherche, les suggestions et les requêtes de recherche

##Kit de développement logiciel .NET 0.10.0-preview
**Publiée : août 2015**

Il s’agit de la deuxième itération de la bibliothèque cliente .NET, Microsoft.Azure.Search.dll. Cette version intègre la prise en charge de la création, de la gestion et de l’utilisation d’indexeurs par le biais de classes .NET. En outre, les indexeurs SQL Azure offrent une nouvelle prise en charge pour l’indexation des points géographiques.

- [Classe d’indexeurs](https://msdn.microsoft.com/library/azure/microsoft.azure.search.models.indexer.aspx)
- [Classe DataSource](https://msdn.microsoft.com/library/azure/microsoft.azure.search.models.datasource.aspx)

##Kit de développement logiciel .NET 0.9.6-preview
**Publié : 5 mars 2015**

Il s’agit de la première version publique du Kit de développement logiciel .NET pour Azure Search. Ce Kit inclut une bibliothèque cliente, Microsoft.Azure.Search.dll, comprenant deux espaces de noms :

- [Microsoft.Azure.Search](https://msdn.microsoft.com/library/azure/microsoft.azure.search.aspx) ;
- [Microsoft.Azure.Search.Models](https://msdn.microsoft.com/library/azure/microsoft.azure.search.models.aspx).

Exclut :

- les [indexeurs](http://go.microsoft.com/fwlink/p/?LinkId=528173) (cette fonctionnalité n’est plus exclue dans la version 0.10.0-preview) ;
- l’[API REST de gestion](https://msdn.microsoft.com/library/azure/dn832684.aspx) ;
- les fonctionnalités de la version [2015-02-28-Preview](search-api-2015-02-28-Preview.md) (à l’heure actuelle, les fonctionnalités préliminaires sont les processeurs de langage naturel Microsoft et `moreLikeThis`).

Consultez [Utilisation d’Azure Search dans .NET](http://go.microsoft.com/fwlink/p/?LinkId=528088) pour savoir comment installer et utiliser le Kit de développement logiciel.

##Version d’API 2015-02-28-Preview
**Publié : 22 avril 2015**

- Les indexeurs prennent désormais en charge les constructions fieldMapping qui fournissent des attributions de champs lorsque les noms de champs réels de la base de données externe et de l’index Azure Search diffèrent. Consultez [Indexeurs](search-api-indexers-2015-02-28-Preview.md) pour obtenir la version `2015-02-28-preview` de la documentation relative aux indexeurs.

- Dans cette mise à jour de version préliminaire, les indexeurs prennent aussi en charge les transformations du type de champ afin que vous puissiez mapper un champ de chaîne de table SQL à un champ de regroupement de chaînes d’index de recherche, en supposant que le champ source représente un tableau JSON.

**Publié : 5 mars 2015**

- Les [processeurs de langage naturel Microsoft](search-api-2015-02-28-Preview.md) acceptent davantage de langages et appliquent une recherche de radical élargie à tous les langages pris en charge par Office et Bing.

- [moreLikeThis=](search-api-2015-02-28-Preview.md) désigne un paramètre de recherche qui s’exclue mutuellement avec `search=` et déclenche un chemin d’exécution de requête différent. Au lieu d’effectuer la recherche en texte intégral de `search=` à partir de l’entrée d’un terme de recherche, `moreLikeThis=` recherche les documents semblables à un document donné en comparant leurs champs de recherche.

##Version d’API 2015-02-28
**Publié : 5 mars 2015**

- Les [indexeurs](http://go.microsoft.com/fwlink/p/?LinkID=528210) représentent une nouvelle fonctionnalité qui simplifie grandement l’indexation à partir de sources de données sur la base de données SQL Azure, Azure DocumentDB et SQL Server des machines virtuelles Azure.

- Les [générateurs de suggestions](https://msdn.microsoft.com/library/azure/dn798936.aspx) remplacent la prise en charge plus limitée de requêtes par saisie semi-automatique, fournie dans l’implémentation précédente (correspondance des préfixes uniquement), en ajoutant la prise en charge de la correspondance des infixes. Cette implémentation permet de rechercher des correspondances de n’importe quelle partie du terme et accepte également les correspondances approximatives.

- L’[amélioration des balises](http://go.microsoft.com/fwlink/p/?LinkId=528212) offre un nouveau cas de figure pour les profils de score. Elle exploite notamment les données persistantes, comme les préférences d’achat, afin d’optimiser les résultats de recherche pour les utilisateurs individuels en fonction d’informations personnalisées.

Lisez le billet de blog [Azure Search est désormais mis à la disposition générale](http://go.microsoft.com/fwlink/p/?LinkId=528211) afin de prendre connaissance de l’annonce du service sur le blog Azure et d’accéder à la description de toutes les fonctionnalités du service.

##Version d’API 2014-10-20-Preview
**Publié : novembre 2014, janvier 2015**

- Les [analyseurs de langue Lucene](search-api-2014-10-20-preview.md) ont été ajoutés pour offrir une prise en charge multilingue des analyseurs de langue personnalisés qui sont distribués avec Lucene. 

- La prise en charge des outils a été introduite pour la création d’index, y compris les profils de score, sur le [portail de gestion Azure](https://portal.azure.com).

##Version d’API 2014-07-31-Preview
**Publié : 21 août 2014**

Cette version était la version préliminaire publique d’Azure Search et fournissait les fonctionnalités principales suivantes :

- API REST pour les opérations d’indexation et de documentation. La majeure partie de cette version d’API est toujours intacte dans 2015-02-28. La documentation de la version `2014-07-31-Preview` se trouve dans [API REST du service Azure Search : version 2014-07-31](search-api-2014-07-31-preview.md).

- Profils de score pour l’optimisation des résultats de recherche. Un profil de score intègre les critères utilisés pour traiter les résultats de la recherche. La documentation relative à cette fonctionnalité se trouve dans [API REST de profils de score du service Azure Search : version 2014-07-31](search-api-scoring-profiles-2014-07-31-preview.md).

- La prise en charge géospatiale a toujours été disponible par le biais du type de données `Edm.GeographyPoint` faisant partie d’Azure Search depuis le début.

- Déploiement dans la version préliminaire du [portail de gestion Azure](https://portal.azure.com). Azure Search constituait l’un des quelques services inédits du nouveau portail.

##Version d’API de gestion 2015-08-19
**Date de publication : 11 septembre 2015**

L'[API REST de gestion](https://msdn.microsoft.com/library/azure/dn832684.aspx) inclut les mises à jour suivantes.

- checkNameAvailability vérifie si un nom de service donné est déjà utilisé.
- La plage de réplica, allant précédemment de 1 à 6, s’étend désormais de 1 à 12.
- La propriété SKU, qui figurait précédemment dans le conteneur de propriétés, se trouve désormais au niveau supérieur de la charge utile du service.
- Le corps de la réponse de l’opération Création de service Search a été mis à jour pour prendre en charge le déplacement du paramètre SKU.

##Version d’API de gestion 2015-02-28
**Publié : 5 mars 2015**

L’[API REST de gestion](search-management-api-2014-02-28.md) représente la première version de l’API de gestion appartenant à la version d’Azure Search mise à la disposition générale. Les fonctionnalités de la version préliminaire précédente et de cette version sont strictement identiques.

##Version d’API de gestion 2014-07-31-Preview
**Publié : octobre 2014**

L’ajout de la version préliminaire de l’[API REST de gestion](search-management-api-2014-07-31-preview.md) permet de prendre en charge l’administration du service par programme. Cette version est contrôlée indépendamment de l’API REST du service.


 

<!---HONumber=Nov15_HO2-->