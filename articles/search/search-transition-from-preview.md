<properties 
	pageTitle="Transition de la version préliminaire api-version=2014* vers api-version=2015* | Microsoft Azure | Service de recherche cloud hébergé" 
	description="Découvrez les dernières modifications et apprenez à faire migrer le code écrit pour la version 2014-07-31-preview ou 2014-10-20-preview vers Azure Search, api-version = 2015-02-28." 
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

#Transition de la version préliminaire api-version=2014* vers api-version=2015*#

Azure Search est un service de recherche cloud hébergé sur Microsoft Azure. Les instructions suivantes sont destinées aux clients ayant créé des applications personnalisées sur les versions préliminaires d’Azure Search et migrant actuellement vers la version mise à la disposition générale, 2015-02-28.

En tant que client de la version préliminaire, vous avez peut-être utilisé l’une de ces anciennes versions préliminaires :

- 2014-07-31-Preview ;
- 2014-10-20-Preview.

À présent qu’Azure Search est mis à la disposition générale, nous encourageons la transition vers des versions plus récentes : 2015-02-28 constitue la version d’API officielle de la version mise à la disposition générale d’Azure Search. Cette version est documentée sur [MSDN](https://msdn.microsoft.com/library/azure/dn798933.aspx).

Nous déployons également la prochaine version préliminaire, [2015-02-28-Preview](search-api-2015-02-28-preview.md), qui comportera des fonctionnalités toujours en cours de développement. Vous pouvez fournir des commentaires sur la version préliminaire de l'API à l'aide des [forums Azure Search](https://social.msdn.microsoft.com/forums/azure/home?forum=azuresearch) ou notre [page de commentaires] (https://feedback.azure.com/forums/263029-azure-search /).

###Liste de contrôle pour la migration###

- Examinez les dernières modifications effectuées afin de déterminer si votre solution est affectée.
- Faites passer la version d’API à `2015-02-28` pour la version verrouillée. Cette version est sous contrat SLA. Si vous rencontrez des problèmes, vous pouvez les résoudre plus rapidement.
- Créez, déployez et testez. Vous devez bénéficier d’une parité complète pour les comportements de recherche, à l’exception des dernières modifications.
- Effectuez le déploiement en production.
- Évaluez les nouvelles fonctionnalités pour l’adoption des fonctionnalités futures. Passez de nouveau à la version 2015-02-28-Preview pour tester les processeurs de langage naturel Microsoft ou `morelikethis`.

##Dernières modifications dans api-version=2015*##

La version initiale d’API incluait une fonctionnalité de suggestions prédictives ou de saisie semi-automatique. Bien qu’utile, elle se limitait à la mise en correspondance des préfixes et traitait les premiers caractères du terme de recherche uniquement. L’implémentation était une propriété booléenne appelée `suggestions` que vous deviez définir sur `true` pour permettre la correspondance des préfixes sur un champ spécifique.

Cette implémentation initiale est désormais remplacée par une nouvelle construction `Suggesters` définie dans la fonctionnalité d’[index](https://msdn.microsoft.com/library/azure/dn798941.aspx) qui offre une correspondance des infixes et une correspondance approximative. Comme leur nom l’indique, la correspondance des infixes et la correspondance approximative offrent une capacité beaucoup plus vaste de correspondance. La correspondance des infixes englobe la correspondance des préfixes : elle permet de faire correspondre les caractères de début et étend cette correspondance au reste de la chaîne.

Nous avons choisi d’interrompre l’implémentation précédente (propriété booléenne), qui sera donc totalement indisponible dans les versions 2015 sans compatibilité descendante, afin d’éviter que les clients ne l’adoptent par inadvertance lors de la conception de nouvelles solutions. Si vous utilisez `2015-02-28` ou `2015-02-28-Preview`, vous devrez recourir à la nouvelle construction `Suggesters` pour les saisies semi-automatiques.

##Transfert de code existant##

La propriété de suggestion constitue la seule modification effectuée dernièrement. Si vous n’utilisez pas cette propriété, vous pouvez faire passer la `api-version` à `2014-07-31-Preview` ou `2014-10-20-Preview` avec `2015-02-28`, puis entreprendre la reconstruction et le redéploiement. L’application fonctionnera comme avant.

Les applications personnalisées ayant implémenté les suggestions doivent permettre les opérations suivantes :

1. Mise à jour de tous les packages NuGet.
1. Transition de la version API vers `2015-02-28`. Si vous utilisez l’exemple de code ci-dessous, la version d’API fait partie de la classe **AzureSearchHelper**.
1. Suppression de l’attribut `Suggestions={true | false}` du schéma JSON qui définit votre index.
1. Ajout d’une construction au bas de l’index pour `Suggesters` (comme indiqué dans le paragraphe [Après](#after)).
1. Vérification de la possibilité d’effectuer des publications sur votre service (vous devrez peut-être renommer l’index pour éviter les conflits de nommage).
1. Régénération de la solution et déploiement dans un environnement de test.
1. Exécution de tous les tests pour vérifier le comportement de la solution.
1. Exécution du déploiement en production.

L’exemple de code tiré de l’[exemple Adventure Works sur CodePlex](https://azuresearchadventureworksdemo.codeplex.com/) présente l’implémentation `Suggestions` initiale. Vous voudrez peut-être utiliser cet exemple pour faire migrer le code vers l’exemple de code.

Dans la section suivante, nous allons décrire la situation [avant](#before) et [après](#after) implémentation des suggestions. Vous pouvez remplacer la méthode **CreateCatalogIndex()** méthode par la version présente dans la section [Après](#after), puis concevoir et déployer une solution afin de tester la nouvelle fonctionnalité.

<a name="before"></a>
###Avant###

Comme vous pouvez le voir, `Suggestions` désigne une propriété booléenne définie sur chaque champ. Supprimez tous les attributs `Suggestions`.

        private static void CreateCatalogIndex()
        {
            var definition = new 
            {
                Name = "catalog",
                Fields = new[] 
                { 
                    new { Name = "productID",        Type = "Edm.String",         Key = true,  Searchable = false, Filterable = false, Sortable = false, Facetable = false, Retrievable = true,  Suggestions = false },
                    new { Name = "name",             Type = "Edm.String",         Key = false, Searchable = true,  Filterable = false, Sortable = true,  Facetable = false, Retrievable = true,  Suggestions = true  },
                    new { Name = "productNumber",    Type = "Edm.String",         Key = false, Searchable = true,  Filterable = false, Sortable = false, Facetable = false, Retrievable = true,  Suggestions = true  },
                    new { Name = "color",            Type = "Edm.String",         Key = false, Searchable = true,  Filterable = true,  Sortable = true,  Facetable = true,  Retrievable = true,  Suggestions = false },
                    new { Name = "standardCost",     Type = "Edm.Double",         Key = false, Searchable = false, Filterable = false, Sortable = false, Facetable = false, Retrievable = true,  Suggestions = false },
                    new { Name = "listPrice",        Type = "Edm.Double",         Key = false, Searchable = false, Filterable = true,  Sortable = true,  Facetable = true,  Retrievable = true,  Suggestions = false },
                    new { Name = "size",             Type = "Edm.String",         Key = false, Searchable = true,  Filterable = true,  Sortable = true,  Facetable = true,  Retrievable = true,  Suggestions = false },
                    new { Name = "weight",           Type = "Edm.Double",         Key = false, Searchable = false, Filterable = true,  Sortable = false, Facetable = true,  Retrievable = true,  Suggestions = false },
                    new { Name = "sellStartDate",    Type = "Edm.DateTimeOffset", Key = false, Searchable = false, Filterable = true,  Sortable = false, Facetable = false, Retrievable = false, Suggestions = false },
                    new { Name = "sellEndDate",      Type = "Edm.DateTimeOffset", Key = false, Searchable = false, Filterable = true,  Sortable = false, Facetable = false, Retrievable = false, Suggestions = false },
                    new { Name = "discontinuedDate", Type = "Edm.DateTimeOffset", Key = false, Searchable = false, Filterable = true,  Sortable = false, Facetable = false, Retrievable = true,  Suggestions = false },
                    new { Name = "categoryName",     Type = "Edm.String",         Key = false, Searchable = true,  Filterable = true,  Sortable = false, Facetable = true,  Retrievable = true,  Suggestions = true  },
                    new { Name = "modelName",        Type = "Edm.String",         Key = false, Searchable = true,  Filterable = true,  Sortable = false, Facetable = true,  Retrievable = true,  Suggestions = true  },
                    new { Name = "description",      Type = "Edm.String",         Key = false, Searchable = true,  Filterable = true,  Sortable = false, Facetable = false, Retrievable = true,  Suggestions = false }
                }
            };

<a name="after"></a>
###Une fois###

Une définition de schéma migré omet la propriété `Suggestions` et ajoute une construction `Suggesters` en bas.

        private static void CreateCatalogIndex()
        {
            var definition = new 
            {
                Name = "catalog",
                Fields = new[] 
                { 
                    new { Name = "productID",        Type = "Edm.String",         Key = true,  Searchable = false, Filterable = false, Sortable = false, Facetable = false, Retrievable = true },
                    new { Name = "name",             Type = "Edm.String",         Key = false, Searchable = true,  Filterable = false, Sortable = true,  Facetable = false, Retrievable = true },
                    new { Name = "productNumber",    Type = "Edm.String",         Key = false, Searchable = true,  Filterable = false, Sortable = false, Facetable = false, Retrievable = true },
                    new { Name = "color",            Type = "Edm.String",         Key = false, Searchable = true,  Filterable = true,  Sortable = true,  Facetable = true,  Retrievable = true },
                    new { Name = "standardCost",     Type = "Edm.Double",         Key = false, Searchable = false, Filterable = false, Sortable = false, Facetable = false, Retrievable = true },
                    new { Name = "listPrice",        Type = "Edm.Double",         Key = false, Searchable = false, Filterable = true,  Sortable = true,  Facetable = true,  Retrievable = true },
                    new { Name = "size",             Type = "Edm.String",         Key = false, Searchable = true,  Filterable = true,  Sortable = true,  Facetable = true,  Retrievable = true },
                    new { Name = "weight",           Type = "Edm.Double",         Key = false, Searchable = false, Filterable = true,  Sortable = false, Facetable = true,  Retrievable = true },
                    new { Name = "sellStartDate",    Type = "Edm.DateTimeOffset", Key = false, Searchable = false, Filterable = true,  Sortable = false, Facetable = false, Retrievable = false },
                    new { Name = "sellEndDate",      Type = "Edm.DateTimeOffset", Key = false, Searchable = false, Filterable = true,  Sortable = false, Facetable = false, Retrievable = false },
                    new { Name = "discontinuedDate", Type = "Edm.DateTimeOffset", Key = false, Searchable = false, Filterable = true,  Sortable = false, Facetable = false, Retrievable = true },
                    new { Name = "categoryName",     Type = "Edm.String",         Key = false, Searchable = true,  Filterable = true,  Sortable = false, Facetable = true,  Retrievable = true },
                    new { Name = "modelName",        Type = "Edm.String",         Key = false, Searchable = true,  Filterable = true,  Sortable = false, Facetable = true,  Retrievable = true },
                    new { Name = "description",      Type = "Edm.String",         Key = false, Searchable = true,  Filterable = true,  Sortable = false, Facetable = false, Retrievable = true }
                },
                suggesters = new[]
                {
                new {
                    name = "sg",
                    searchMode = "analyzingInfixMatching",
                    sourceFields = new []{"name", "productNumber", "categoryName", "modelName", "description"}
                    }
                 }
            };

##Évaluation des nouvelles approches et fonctionnalités##

Une fois que vous avez transféré votre solution et vérifié son bon fonctionnement, vous pouvez utiliser ces liens pour en savoir plus sur les nouvelles fonctionnalités.

- [Azure Search est mis à la disposition générale (billet de blog)](http://go.microsoft.com/fwlink/p/?LinkId=528211)
- [Nouveautés de la dernière mise à jour d’Azure Search](search-latest-updates.md)
- [Présentation d’Azure Storage](search-what-is-azure-search.md)

##Obtenir de l'aide##

La version d’API `2015-02-28` est sous contrat SLA. Utilisez les options de support et les liens de [cette page](../support/options/) pour émettre un ticket de support.

 

<!---HONumber=AcomDC_0128_2016-->