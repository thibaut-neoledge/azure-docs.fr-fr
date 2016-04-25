<properties
	pageTitle="Nouveautés de la dernière mise à jour d’Azure Search | Microsoft Azure | Service de recherche cloud hébergé"
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
	ms.date="02/10/2016"
	ms.author="heidist"/>

#Nouveautés de la dernière mise à jour d’Azure Search#

Azure Search est un service de recherche cloud hébergé sur Microsoft Azure. Il est mis à la disposition générale avec un contrat de niveau de service (SLA) affichant une disponibilité de 99,9 % et dédié aux configurations de la [version 2015-02-28 de l'API REST du service de recherche](https://msdn.microsoft.com/library/azure/dn798935.aspx) ou du [Kit de développement logiciel (SDK) .NET](https://msdn.microsoft.com/library/azure/dn951165.aspx).

##Nouveautés en 2016

Fonctionnalité|Publication|Statut|Détails
-------|--------|------|-------
[Kit de développement logiciel (SDK) .NET 1.1](https://msdn.microsoft.com/library/azure/dn951165.aspx)|Février 2016|GA|Ceci est la première version mise à la disposition générale de la bibliothèque cliente .NET, `Microsoft.Azure.Search.dll`. Cette version introduit des changements majeurs. Consultez [Mise à niveau vers la version du Kit de développement logiciel (SDK) .NET version 1.1 d'Azure Search](search-dotnet-sdk-migration.md) pour obtenir des conseils sur la migration.
[Support de syntaxe de requête Lucene](https://msdn.microsoft.com/library/azure/mt589323.aspx)|Février 2016|[GA](search-api-2015-02-28-preview.md)|La syntaxe de requête Lucene est désormais mise à la disposition générale dans l'API REST et le Kit de développement logiciel (SDK) .NET. Définissez le paramètre `queryType` sur `full` dans l'API REST et la propriété `SearchParameters.QueryType` sur `QueryType.Full` dans le Kit de développement logiciel (SDK) .NET pour activer la syntaxe Lucene.
[Analyseurs personnalisés](https://azure.microsoft.com/blog/custom-analyzers-in-azure-search/)|Janvier 2016|[Version préliminaire](search-api-2015-02-28-preview.md)|Configurations définies par l'utilisateur des générateurs et des filtres de jetons. Consultez [Analyse dans Azure Search](https://msdn.microsoft.com/library/azure/mt605304.aspx) sur MSDN.
[Indexeur de stockage d'objets Blob Azure](search-howto-indexing-azure-blob-storage.md)|Janvier 2016|[Version préliminaire](search-api-2015-02-28-preview.md)|Les PDF, documents Office, XML, HTML ou même les fichiers audio et vidéo peuvent être fusionnés ou incorporés à un index Azure Search.
[Explorateur de recherche](search-explorer.md)|Janvier 2016|[Portail](https://portal.azure.com)|Outil de requête intégré pour les requêtes ad hoc sur un index.
[Pack de contenu Power BI pour Azure Search](http://blogs.msdn.com/b/powerbi/archive/2016/01/19/visualizing-azure-search-data-with-power-bi.aspx)|Janvier 2016|Outil|Visualisation et analyse des données de service à l'aide d'un nouveau pack de contenu Power BI pour Azure Search. Disponible via l'analyse de recherche.
[Analyse de recherche](https://azure.microsoft.com/blog/analyzing-your-azure-search-traffic/)|Janvier 2016|Portail|Activer la collecte des données pour obtenir des informations sur l'activité de recherche des utilisateurs.

##Nouveautés en 2015

Fonctionnalité|Publication|Statut|Détails
-------|--------|------|-------
Analyseurs de langage Lucene|Octobre 2015|GA|Cette fonctionnalité est désormais mise à la disposition générale, disponible dans l'API REST de service et le Kit de développement logiciel (SDK) .NET.
[Processeurs de langage naturel Microsoft](search-api-2015-02-28-preview.md)|Octobre 2015|GA|Cette fonctionnalité est désormais mise à la disposition générale, disponible dans l'API REST de service et le Kit de développement logiciel (SDK) .NET.
[Support de syntaxe de requête Lucene](https://msdn.microsoft.com/library/azure/mt589323.aspx)|Septembre 2015|[Version préliminaire](search-api-2015-02-28-preview.md)|Permet d'ajouter l'analyseur de requête Lucene. Pour utiliser la nouvelle syntaxe, vous devez spécifier le `queryType` dans une opération de recherche de documents.
[Processeurs de langage naturel](search-language-support.md)|Septembre 2015|[Version préliminaire](search-api-2015-02-28-preview.md)|Ajout des processeurs de langage Microsoft, optimisant le nombre de langues globales et fournissant une implémentation alternative des autres.
POST dans la recherche, les suggestions et les requêtes de recherche|Septembre 2015|[Version préliminaire](search-api-2015-02-28-preview.md)|S'applique à l'API REST du service.
[l’API REST de gestion ;](https://msdn.microsoft.com/library/azure/dn832684.aspx)|Septembre 2015|GA|Deuxième version de l'API REST de gestion. Inclut checkNameAvailability qui vérifie si un nom de service donné est déjà en cours d'utilisation, la plage de réplica était précédemment de 1-6 et est désormais 1-12, la propriété de référence (SKU) a été déplacée du conteneur de propriétés au niveau supérieur de la charge utile de service, le corps de la réponse de l'opération de création d'un service de recherche a été mis à jour pour prendre en charge le déplacement du paramètre de référence (SKU).
Kit de développement logiciel .NET 0.10.0-preview|Août 2015|VERSION PRÉLIMINAIRE|Il s’agit de la deuxième itération de la bibliothèque cliente .NET, Microsoft.Azure.Search.dll. Cette version intègre la prise en charge de la création, de la gestion et de l'utilisation de la [classe DataSource](https://msdn.microsoft.com/library/azure/microsoft.azure.search.models.datasource.aspx) et la [classe des indexeurs](https://msdn.microsoft.com/library/azure/microsoft.azure.search.models.indexer.aspx) par le biais de classes .NET. En outre, les indexeurs SQL Azure offrent une nouvelle prise en charge pour l’indexation des points géographiques.
Constructions fieldMapping|Avril 2015|VERSION PRÉLIMINAIRE|Les indexeurs prennent désormais en charge les constructions fieldMapping qui fournissent des attributions de champs lorsque les noms de champs réels de la base de données externe et de l’index Azure Search diffèrent. Consultez [Indexeurs](search-api-indexers-2015-02-28-Preview.md) pour obtenir la version `2015-02-28-preview` de la documentation relative aux indexeurs.
Transformations du type de champ|Avril 2015|VERSION PRÉLIMINAIRE|Les indexeurs prennent désormais en charge les transformations du type de champ afin que vous puissiez mapper un champ de chaîne de table SQL à un champ de regroupement de chaînes d'index de recherche, en supposant que le champ source représente un tableau JSON.
[API REST du service](https://msdn.microsoft.com/library/azure/dn798935.aspx)|Mars 2015|GA|Première version généralement disponible de l'API REST du service Azure Search. Cette version inclut des fonctionnalités précédentes. Elle inclut également des [indexeurs](http://go.microsoft.com/fwlink/p/?LinkID=528210). Les [générateurs de suggestions](https://msdn.microsoft.com/library/azure/dn798936.aspx) remplacent la prise en charge plus limitée de requêtes par saisie semi-automatique, fournie dans l’implémentation précédente (correspondance des préfixes uniquement), en ajoutant la prise en charge de la correspondance des infixes. Cette implémentation permet de rechercher des correspondances de n’importe quelle partie du terme et accepte également les correspondances approximatives. L’[amélioration des balises](http://go.microsoft.com/fwlink/p/?LinkId=528212) offre un nouveau cas de figure pour les profils de score. Elle exploite notamment les données persistantes, comme les préférences d’achat, afin d’optimiser les résultats de recherche pour les utilisateurs individuels en fonction d’informations personnalisées.
Kit de développement logiciel .NET 0.9.6-preview|Mars 2015|VERSION PRÉLIMINAIRE|Il s’agit de la première version publique du Kit de développement logiciel .NET pour Azure Search. Elle inclut une bibliothèque cliente, Microsoft.Azure.Search.dll, avec deux espaces de noms : [Microsoft.Azure.Search](https://msdn.microsoft.com/library/azure/microsoft.azure.search.aspx) et [Microsoft.Azure.Search.Models](https://msdn.microsoft.com/library/azure/microsoft.azure.search.models.aspx)
l’API REST de gestion ;|Mars 2015|GA|Première version de l'API REST de gestion appartenant à la version d'Azure Search mise à la disposition générale. Les fonctionnalités de la version préliminaire précédente et de cette version sont strictement identiques.
[Processeurs de langage naturel Microsoft](search-api-2015-02-28-preview.md)|Mars 2015|VERSION PRÉLIMINAIRE|Ajoute davantage de langages et applique une recherche de radical élargie à tous les langages pris en charge par Office et Bing.
[moreLikeThis=](search-api-2015-02-28-preview.md)|Mars 2015|VERSION PRÉLIMINAIRE|Un paramètre de recherche qui s'exclue mutuellement avec `search=` et déclenche un chemin d'exécution de requête différent. Au lieu d’effectuer la recherche en texte intégral de `search=` à partir de l’entrée d’un terme de recherche, `moreLikeThis=` recherche les documents semblables à un document donné en comparant leurs champs de recherche.

##Nouveautés en 2014

Fonctionnalité|Publication|Statut|Détails
-------|--------|------|-------
Analyseurs de langage Lucene|novembre 2014|VERSION PRÉLIMINAIRE|Ajoutée pour offrir une prise en charge multilingue des analyseurs de langage personnalisés qui sont distribués avec Lucene.
La prise en charge du portail a été introduite pour la création d'index|novembre 2014|[Portail](https://portal.azure.com)|Il est possible de créer des index, des analyseurs et des profils de score dans le portail.
Version d’API de gestion 2014-07-31-Preview|Octobre 2014|VERSION PRÉLIMINAIRE|Première version préliminaire publique de l'API REST de gestion. La documentation pour cette version de l'API est disponible sur demande.
API REST du service de recherche|Août 2014|VERSION PRÉLIMINAIRE|Première version préliminaire publique de l'API REST du service (api-version-2014-07-31-Preview). Il s'agit de l'API REST pour les opérations sur l'index et les documents, les profils de score pour le paramétrage des résultats de recherche, la prise en charge géospatiale. Cette version prend en charge la mise en service dans le portail Azure. La documentation pour cette version de l'API est disponible sur demande. Cette version est contrôlée indépendamment de l’API REST du service.

##Contrôle de version et déploiement des fonctionnalités

Les fonctionnalités sont publiées séparément ou conjointement par le biais de l'[API REST](https://msdn.microsoft.com/library/azure/dn798935.aspx), du [Kit de développement logiciel (SDK) .NET](http://go.microsoft.com/fwlink/?LinkId=528216) ou du tableau de bord des services du [portail Azure](https://portal.azure.com). La bibliothèque .NET et les API REST existent dans différentes versions. Les API plus anciennes restent opérationnelles lors du déploiement de nouvelles fonctionnalités. Vous pouvez consulter [Contrôle de version de service Azure Search](https://msdn.microsoft.com/library/azure/dn864560.aspx) pour en savoir plus sur notre stratégie de contrôle de version.

Les fonctionnalités en version préliminaire et mises à la disposition générale sont liées à une version de l'API de la même catégorie.

- Les fonctionnalités en version préliminaire sont expérimentales et peuvent changer ou même être abandonnées avant la mise à la disposition générale. Les fonctionnalités en version préliminaire sont toujours disponibles dans la [version préliminaire de l'API REST](search-api-2015-02-28-preview.md) et parfois dans le [Kit de développement logiciel (SDK) .NET](http://go.microsoft.com/fwlink/?LinkId=528216). La documentation sur les fonctionnalités explique toujours comment accéder à la fonctionnalité en question.
- Les fonctionnalités mises à la disposition générale sont stables et peu susceptibles de changer. Toute modification apportée à une fonctionnalité généralement disponible est annoncée comme une modification essentielle.

Les fonctionnalités qui sont purement basées sur le portail ou sur outil sont supposées changer au fil du temps et ne sont pas classées comme étant en version préliminaire ou à la disposition générale.

<!---HONumber=AcomDC_0413_2016-->