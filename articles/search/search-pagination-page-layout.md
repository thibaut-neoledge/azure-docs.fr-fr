<properties 
	pageTitle="Navigation dans les résultats de recherche d’Azure Search" 
	description="Pagination dans Azure Search" 
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
	ms.date="09/08/2015" 
	ms.author="heidist"/>

#Navigation dans les résultats de recherche d’Azure Search#

Cet article explique comment utiliser l’API REST de service Azure Search pour implémenter les éléments standard d’une page de résultats de recherche, comme les totaux, l’extraction de documents, les ordres de tri et la navigation.
 
Dans tous les cas mentionnés ci-après, les options de page qui fournissent des données ou des informations à votre page de résultats de recherche sont spécifiées par le biais des demandes [Recherche de documents](http://msdn.microsoft.com/library/azure/dn798927.aspx) envoyées à votre service Azure Search. Ces demandes incluent une commande GET, un chemin d’accès et des paramètres de requête informant le service de ce qui est demandé et de la manière dont formuler la réponse.

> [AZURE.NOTE]Une demande valide inclut plusieurs éléments, parmi lesquels une URL de service et un chemin d’accès, un verbe HTTP, `api-version`, etc. Par souci de concision, nous avons tronqué les exemples afin de mettre en évidence la syntaxe se rapportant à la pagination uniquement. Consultez [API REST de service Azure Search](http://msdn.microsoft.com/library/azure/dn798935.aspx) pour en savoir plus sur la syntaxe de demande.

## Nombre total de résultats et nombre de pages ##

L’affichage du nombre total des résultats d’une requête et la présentation de ces résultats en blocs plus petits sont des éléments de base sur presque toutes les pages de recherche.

![][1]
 
Dans Azure Search, vous utilisez les paramètres `$count`, `$top` et `$skip` pour renvoyer ces valeurs. L’exemple suivant illustre une demande du nombre total de résultats, renvoyé sous la forme `@OData.count` :

    	GET /indexes/onlineCatalog/docs?$count=true

Récupérez des documents par groupes de 15 et affichez le nombre total de résultats à partir de la première page :

		GET /indexes/onlineCatalog/docs?search=*$top=15&$skip=0&$count=true

La pagination des résultats nécessite `$top` et `$skip`, où `$top` indique le nombre d’éléments à renvoyer dans un lot, et `$skip` spécifie le nombre d’éléments à ignorer. Dans l’exemple suivant, chaque page affiche les 15 éléments suivants, conformément aux sauts incrémentiels indiqués dans le paramètre `$skip`.

    	GET /indexes/onlineCatalog/docs?search=*$top=15&$skip=0&$count=true

    	GET /indexes/onlineCatalog/docs?search=*$top=15&$skip=15&$count=true

    	GET /indexes/onlineCatalog/docs?search=*$top=15&$skip=30&$count=true

## Disposition  ##

Vous souhaiterez peut-être afficher une miniature, un sous-ensemble de champs et un lien vers la fiche produit complète sur vos pages de résultats de recherche.

 ![][2]
 
Dans Azure Search, vous pouvez utiliser pour cela `$select` et une commande de recherche.

Pour renvoyer un sous-ensemble de champs relatif à une disposition en mosaïque :

    	GET /indexes/ onlineCatalog/docs?search=*&$select=productName,imageFile,description,price,rating 

Les images et les fichiers multimédias ne peuvent pas faire l’objet de recherches directes et doivent être stockés sur une autre plateforme de stockage, comme l’espace de stockage Azure Blob, afin de limiter les coûts. Dans les index et les documents, définissez un champ destiné à stocker l’adresse URL du contenu externe. Vous pourrez utiliser ce champ comme référence d’image. L’URL de l’image doit se trouver dans le document.

Pour récupérer une page de description de produit relative à un événement **onClick**, utilisez la [recherche de document](http://msdn.microsoft.com/library/azure/dn798929.aspx) afin d’indiquer la clé du document à récupérer. Le type de données de la clé est `Edm.String`. Dans cet exemple, il s’agit de *246810*.
   
    	GET /indexes/onlineCatalog/docs/246810

## Tri par pertinence, évaluation ou prix ##

Les données sont souvent triées par défaut en fonction de la pertinence, mais il est courant de proposer d’autres ordres de tri afin que les clients puissent rapidement réorganiser les résultats existants.

 ![][3]

Dans Azure Search, le tri repose sur l’expression `$orderby` pour tous les champs indexés comme étant `"Sortable": true.`

La pertinence est clairement liée aux profils de score. Vous pouvez utiliser le score par défaut, qui repose sur l’analyse de texte et les statistiques pour ordonner tous les résultats : dans ce cas, les documents présentant des correspondances plus nombreuses ou plus fortes affichent un score plus élevé.

D’autres ordres de tri sont souvent associés aux événements **onClick** qui renvoient à une méthode permettant de générer l’ordre de tri. Par exemple, avec cet élément de page :

 ![][4]

Vous pouvez créer la méthode qui accepte l’option de tri sélectionnée comme entrée, et renvoie une liste ordonnée conforme aux critères associés à cette option.

 ![][5]
 
> [AZURE.NOTE]Si le score par défaut suffit dans de nombreuses situations, nous vous recommandons tout de même de baser la pertinence sur un profil de score personnalisé. Un profil de score personnalisé accorde plus d’importance aux éléments qui avantagent votre entreprise. Consultez [Ajout de profils de calcul de score](http://msdn.microsoft.com/library/azure/dn798928.aspx) pour en savoir plus.

## Navigation à facettes ##

Les options de navigation de recherche sont communes à toutes les pages de résultats et se trouvent souvent sur le côté ou en haut de la page. Dans Azure Search, la navigation à facettes permet une recherche autonome en fonction de filtres prédéfinis. Consultez [Navigation à facettes dans Azure Search](search-faceted-navigation.md) pour en savoir plus.

## Filtres au niveau de la page ##

Si votre solution inclut des pages de recherche dédiées à certains types de contenu (par exemple, une application de vente au détail en ligne avec des départements figurant en haut de la page), vous pouvez associer une expression de filtre à un événement **onClick** pour ouvrir une page préfiltrée.

Vous pouvez envoyer un filtre avec ou sans expression de recherche. Par exemple, la demande suivante applique un filtre en fonction du nom de la marque et ne renvoie que les documents correspondants.

    	GET /indexes/onlineCatalog/docs?$filter=brandname eq ‘Microsoft’ and category eq ‘Games’

Consultez [Recherche de documents (API Azure Search)](http://msdn.microsoft.com/library/azure/dn798927.aspx) pour en savoir plus sur les expressions `$filter`.

## Voir aussi ##

- [API REST de service Azure Search](http://msdn.microsoft.com/library/azure/dn798935.aspx)
- [Opérations d’index](http://msdn.microsoft.com/library/azure/dn798918.aspx)
- [Opérations de document](http://msdn.microsoft.com/library/azure/dn800962.aspx)
- [Vidéos et didacticiels relatifs à Azure Search](search-video-demo-tutorial-list.md)
- [Navigation à facettes dans Azure Search](search-faceted-navigation.md)


<!--Image references-->
[1]: ./media/search-pagination-page-layout/Pages-1-Viewing1ofNResults.PNG
[2]: ./media/search-pagination-page-layout/Pages-2-Tiled.PNG
[3]: ./media/search-pagination-page-layout/Pages-3-SortBy.png
[4]: ./media/search-pagination-page-layout/Pages-4-SortbyRelevance.png
[5]: ./media/search-pagination-page-layout/Pages-5-BuildSort.png

<!---HONumber=Sept15_HO2-->