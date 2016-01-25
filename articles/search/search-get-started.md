<properties 
	pageTitle="Prise en main d’Azure Search | Microsoft Azure | Service de recherche cloud hébergé" 
	description="Prise en main d'Azure Search, un service de recherche cloud hébergé sur Microsoft Azure." 
	services="search" 
	documentationCenter="" 
	authors="HeidiSteen" 
	manager="mblythe" 
	editor=""
    tags="azure-portal"/>

<tags 
	ms.service="search" 
	ms.devlang="rest-api" 
	ms.workload="search" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.date="01/11/2016" 
	ms.author="heidist"/>

# Prise en main d'Azure Search

Microsoft Azure Search est un service de recherche cloud hébergé qui permet d'incorporer des fonctionnalités de recherche dans des applications personnalisées. Il fournit le moteur de recherche et assure le stockage de vos données, dont l'accès et la gestion se font à l'aide d'une bibliothèque .NET ou d'une API REST.

Cet article vous permet de vous familiariser avec l'API REST d’Azure Search.

Une autre approche pour les développeurs .NET consiste à utiliser le Kit de développement logiciel (SDK) .NET d’Azure Search. Reportez-vous à la rubrique [Prise en main d’Azure Search dans .NET](search-get-started-dotnet.md) ou [Utilisation du Kit de développement logiciel (SDK) .NET d’Azure Search](search-howto-dotnet-sdk.md) pour plus d'informations.


> [AZURE.NOTE]Pour suivre ce didacticiel, vous avez besoin d'un [abonnement Azure](../includes/free-trial-note.md). Si vous n'êtes pas prêt à souscrire un abonnement d'essai, vous pouvez ignorer ce didacticiel et opter pour [Tester Azure App Service](https://tryappservice.azure.com/) à la place. Cette option de remplacement vous propose Azure Search avec une application Web ASP.NET gratuitement, une heure par session, sans abonnement obligatoire.
 
<a id="sub-1"></a>
## Créer un service Azure Search

En tant qu'administrateur, vous pouvez ajouter le service Search à un abonnement existant gratuitement lorsque vous choisissez le service partagé, ou à un tarif réduit lorsque vous choisissez des ressources dédiées.

Les abonnés bénéficient automatiquement d'un accès gratuit à un service de recherche mutualisé et partagé pouvant être utilisé à des fins d'apprentissage, de tests de validation technique ou de projets de recherche de faible envergure.

Connectez-vous au [portail Azure](https://portal.azure.com) à l’aide de votre abonnement existant. Pour suivre des instructions pas-à-pas, consultez [Créer un service Azure Search dans le portail](search-create-service-portal.md).

## Obtenir une clé API et l’URL du service

Une fois le service créé, vous pouvez revenir aux paramètres de configuration pour obtenir l’URL et les clés API. Les connexions à votre service Search requièrent que vous disposiez de l'URL et d'une clé API pour authentifier l'appel. Voici comment trouver ces valeurs rapidement :

1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. Dans la barre d’index, cliquez sur **Service de recherche** pour obtenir la liste des services Azure Search approvisionnés pour votre abonnement.
3. Sélectionnez le service que vous souhaitez utiliser.
4.	Dans le tableau de bord du service, vous voyez des vignettes pour **PROPRIÉTÉS** et **CLÉS**, et des informations sur l'utilisation permettant de vérifier rapidement la façon dont les ressources sont utilisées. 


<a id="sub-3"></a>
## Test des opérations de service

En guise d’étape de validation, testez si votre service est opérationnel et accessible à partir d’une application cliente. Cette procédure utilise Fiddler, disponible en tant que [téléchargement à partir de Telerik](http://www.telerik.com/fiddler), pour émettre des demandes HTTP et afficher les réponses. Avec Fiddler, vous pouvez tester l'API immédiatement, sans avoir à écrire du code.

Dans la procédure ci-dessous, vous allez créer un index, télécharger des documents, interroger l'index, puis interroger le système pour obtenir des informations de service.

### Création d'un index

1. Démarrez Fiddler. Dans le menu Fichier, désactivez **Capturer le trafic** pour masquer les activités HTTP externes qui ne sont pas en rapport avec la tâche actuelle. Sous l'onglet Éditeur, formulez une demande similaire à la suivante : 

  	![][16]

2. Sélectionnez **PUT**.

3. Entrez une URL qui spécifie l'URL de service (figurant dans la page Propriétés), les attributs de demande et la version d'API. Voici quelques points à garder à l'esprit :
   + Utilisez le préfixe HTTPS.
   + L'attribut de demande est « /indexes/hotels ». Cela indique à Search de créer un index intitulé « hotels ».
   + La version d'API est en minuscules et elle est spécifiée comme suit : ?api-version=2015-02-28. Les versions d'API sont importantes, car Azure Search déploie régulièrement des mises à jour. En de rares cas, une mise à jour de service peut introduire une modification avec rupture dans l'API. À l'aide de versions d'API, vous pouvez continuer à utiliser votre version existante et la mettre à niveau avec une version plus récente le cas échéant.

    L'URL complète doit être semblable à celle figurant dans l'exemple suivant :

         https://my-app.search.windows.net/indexes/hotels?api-version=2015-02-28

4.	Spécifiez l'en-tête de demande, en remplaçant l'hôte et la clé API par des valeurs qui sont valides pour votre service.

        User-Agent: Fiddler
        host: my-app.search.windows.net
        content-type: application/json
        api-key: 1111222233334444

5.	Dans Corps de la demande, collez les champs qui composent la définition d'index.

         {
        "name": "hotels",  
        "fields": [
          {"name": "hotelId", "type": "Edm.String", "key":true, "searchable": false},
          {"name": "baseRate", "type": "Edm.Double"},
          {"name": "description", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false},
          {"name": "hotelName", "type": "Edm.String", "suggestions": true},
          {"name": "category", "type": "Edm.String"},
          {"name": "tags", "type": "Collection(Edm.String)"},
          {"name": "parkingIncluded", "type": "Edm.Boolean"},
          {"name": "smokingAllowed", "type": "Edm.Boolean"},
          {"name": "lastRenovationDate", "type": "Edm.DateTimeOffset"},
          {"name": "rating", "type": "Edm.Int32"},
          {"name": "location", "type": "Edm.GeographyPoint"}
         ] 
        }

6.	Cliquez sur **Exécuter**.

Patientez quelques secondes pour voir s'afficher une réponse HTTP 201 dans la liste de sessions, ce qui indique que l'index a été créé correctement.

Si vous obtenez HTTP 504, vérifiez que l'URL spécifie HTTPS. Si vous voyez HTTP 400 ou 404, contrôlez le corps de la demande pour vérifier l'absence d'erreurs de copier-coller. HTTP 403 indique normalement qu'il y a un problème avec la clé API (soit la clé n'est pas valide, soit il y a un problème de syntaxe avec la façon dont elle est spécifiée).

### Chargement de documents

Sous l'onglet Éditeur, votre demande de publication de documents se présente comme suit. Le corps de la demande contient les données de recherche pour 4 hôtels.

   ![][17]

1. Sélectionnez **POST**.

2.	Entrez une URL qui commence par HTTPS, suivie de votre URL de service, suivie de « /indexes/<'indexname'>/docs/index?api-version=2015-02-28 ». L'URL complète doit être semblable à celle figurant dans l'exemple suivant :

        https://my-app.search.windows.net/indexes/hotels/docs/index?api-version=2015-02-28

3.	L'en-tête de demande doit être le même qu'avant. Souvenez-vous que vous avez remplacé l'hôte et la clé API par des valeurs qui sont valides pour votre service.

        User-Agent: Fiddler
        host: my-app.search.windows.net
        content-type: application/json
        api-key: 1111222233334444

4.	Le corps de la demande contient quatre documents à ajouter à l'index des hôtels.

        {
        "value": [
        {
        	"@search.action": "upload",
        	"hotelId": "1",
        	"baseRate": 199.0,
        	"description": "Best hotel in town",
        	"hotelName": "Fancy Stay",
        	"category": "Luxury",
        	"tags": ["pool", "view", "wifi", "concierge"],
        	"parkingIncluded": false,
        	"smokingAllowed": false,
        	"lastRenovationDate": "2010-06-27T00:00:00Z",
        	"rating": 5,
        	"location": { "type": "Point", "coordinates": [-122.131577, 47.678581] }
          },
          {
        	"@search.action": "upload",
        	"hotelId": "2",
        	"baseRate": 79.99,
        	"description": "Cheapest hotel in town",
        	"hotelName": "Roach Motel",
        	"category": "Budget",
        	"tags": ["motel", "budget"],
        	"parkingIncluded": true,
        	"smokingAllowed": true,
        	"lastRenovationDate": "1982-04-28T00:00:00Z",
        	"rating": 1,
        	"location": { "type": "Point", "coordinates": [-122.131577, 49.678581] }
          },
          {
        	"@search.action": "upload",
        	"hotelId": "3",
        	"baseRate": 279.99,
        	"description": "Surprisingly expensive",
        	"hotelName": "Dew Drop Inn",
        	"category": "Bed and Breakfast",
        	"tags": ["charming", "quaint"],
        	"parkingIncluded": true,
        	"smokingAllowed": false,
        	"lastRenovationDate": null,
        	"rating": 4,
        	"location": { "type": "Point", "coordinates": [-122.33207, 47.60621] }
          },
          {
        	"@search.action": "upload",
        	"hotelId": "4",
        	"baseRate": 220.00,
        	"description": "This could be the one",
        	"hotelName": "A Hotel for Everyone",
        	"category": "Basic hotel",
        	"tags": ["pool", "wifi"],
        	"parkingIncluded": true,
        	"smokingAllowed": false,
        	"lastRenovationDate": null,
        	"rating": 4,
        	"location": { "type": "Point", "coordinates": [-122.12151, 47.67399] }
          }
         ]
        }

8.	Cliquez sur **Exécuter**.

Après quelques secondes, la réponse HTTP 200 apparaît dans la liste de sessions. Cela indique que les documents ont été correctement créés. Si vous obtenez HTTP 207, cela signifie qu'au moins un document n'a pas pu être chargé. Si HTTP 404 s'affiche, vous avez une erreur de syntaxe dans l'en-tête ou le corps de la demande.

### Interrogation de l'index

Maintenant qu'un index et des documents sont chargés, vous pouvez émettre des requêtes les concernant. Sous l'onglet Éditeur, une commande GET qui interroge votre service se présentera comme suit :

   ![][18]

1.	Sélectionnez **GET**.

2.	Entrez une URL qui commence par HTTPS, suivie de votre URL de service, suivie de « /indexes/<'indexname'>/docs? », suivie de paramètres de requête. Par exemple, utilisez l'URL suivante et remplacez l'exemple de nom d'hôte par celui qui convient à votre service.

        https://my-app.search.windows.net/indexes/hotels/docs?search=motel&facet=category&facet=rating,values:1|2|3|4|5&api-version=2015-02-28

    Cette requête effectue une recherche sur le terme « motel » et extrait les catégories de facette pour les évaluations.

3.	L'en-tête de demande doit être le même qu'avant. Souvenez-vous que vous avez remplacé l'hôte et la clé API par des valeurs qui sont valides pour votre service.

        User-Agent: Fiddler
        host: my-app.search.windows.net
        content-type: application/json
        api-key: 1111222233334444

Le code de réponse doit correspondre à 200 et la sortie de réponse doit se présenter comme suit.
 
   ![][19]

L'exemple de requête suivant provient de la page [Opération d'index de recherche (API Azure Search)](http://msdn.microsoft.com/library/dn798927.aspx) sur MSDN. Plusieurs des exemples de requêtes dans cette rubrique comportent des espaces, qui ne sont pas autorisés dans Fiddler. Remplacez chaque espace par un caractère + avant de coller la chaîne de requête et d'essayer la requête dans Fiddler :

**Avant le remplacement des espaces :**

        GET /indexes/hotels/docs?search=*&$orderby=lastRenovationDate desc&api-version=2015-02-28

**Après le remplacement des espaces par + :**

        GET /indexes/hotels/docs?search=*&$orderby=lastRenovationDate+desc&api-version=2015-02-28
### Interrogation du système

Vous pouvez également interroger le système pour connaître le nombre de documents et l'espace de stockage utilisé. Sous l'onglet Éditeur, votre demande se présentera comme suit et la réponse renverra un nombre correspondant au nombre de documents et à l'espace utilisé.

   ![][20]

1.	Sélectionnez **GET**.

2.	Entrez une URL qui inclut votre URL de service, suivie de « /indexes/hotels/stats?api-version=2015-02-28 » :

        https://my-app.search.windows.net/indexes/hotels/stats?api-version=2015-02-28 

3.	Spécifiez l'en-tête de demande, en remplaçant l'hôte et la clé API par des valeurs qui sont valides pour votre service.

        User-Agent: Fiddler
        host: my-app.search.windows.net
        content-type: application/json
        api-key: 1111222233334444

4.	Laissez le corps de la demande vide.

5.	Cliquez sur **Exécuter**. Le code d'état HTTP 200 doit s'afficher dans la liste de sessions. Sélectionnez l'entrée publiée pour votre commande.

6.	Cliquez sur l'onglet Inspecteurs | En-têtes, puis sélectionnez le format JSON. Le nombre de documents et la taille de stockage (en Ko) doivent s'afficher.

 	![][21]

<a id="sub-4"></a>
## Exploration du tableau de bord du service Search

Si vous ne savez plus où se trouvent les pages de configuration, procédez comme suit pour trouver le tableau de bord des services.

1.	Connectez-vous au [portail Azure](https://portal.azure.com) à l’aide de votre abonnement existant. 
2.	Cliquez sur **Accueil**, puis sur la vignette du service Search.

 	![][22]

4.	Vous accédez alors au tableau de bord du service. Notez que les commandes **Démarrer**, **Arrêter** et **Supprimer** se trouvent en haut.

5.	Notez que l'URL de service figure en haut de la page. Vous aurez besoin de cette URL pour vous connecter à votre service Azure Search.
	
7.	Cliquez sur l’icône **KEYS** pour afficher les clés API. Vous aurez besoin d'une clé d'administration pour vous authentifier auprès du service. Vous pouvez utiliser la clé primaire ou secondaire. Vous pouvez éventuellement créer des clés de requête pour accéder au service en lecture seule.


<!--Next steps and links -->
<a id="next-steps"></a>
## Faites un essai

Vous êtes prêt à passer à l'étape suivante ? Les liens suivants fournissent des informations supplémentaires vous expliquant comment créer et gérer des applications de recherche utilisant Azure Search.

- [Création d'un exemple de recherche géospatiale Azure Search](search-create-geospatial.md)

- [Gestion de votre solution de recherche dans Microsoft Azure](search-manage.md)

- [Présentation d’Azure Storage](search-what-is-azure-search.md)

- [API REST de service Azure Search](http://msdn.microsoft.com/library/dn798935.aspx)

- [Kit de développement logiciel .NET (SDK) Azure Search](https://msdn.microsoft.com/library/azure/dn951165.aspx)

- [Vidéo Channel 9 : Présentation d'Azure Search](http://channel9.msdn.com/Shows/Data-Exposed/Introduction-To-Azure-Search)

- [Vidéo Channel 9 : Azure Search et les données géospatiales](http://channel9.msdn.com/Shows/Data-Exposed/Azure-Search-and-Geospatial-Data)

- [Cloud Cover épisode 152 : génération d’un index dans Azure Search](http://channel9.msdn.com/Shows/Cloud+Cover/Cloud-Cover-152-Azure-Search-with-Liam-Cavanagh)

<!--Anchors-->
[Start with the free service]: #sub-1
[Upgrade to standard search]: #sub-2
[Test service operations]: #sub-3
[Explore Search service dashboard]: #sub-4
[Try it out]: #next-steps

<!--Image references-->
[6]: ./media/search-get-started/AzureSearch_Configure1_1_New.PNG
[7]: ./media/search-get-started/AzureSearch_Configure1_2_Everything.PNG
[8]: ./media/search-get-started/Azuresearch_Configure1_3a_Gallery.PNG
[9]: ./media/search-get-started/AzureSearch_Configure1_4_GallerySeeAll.PNG
[10]: ./media/search-get-started/AzureSearch_Configure1_5_SearchTile.PNG
[11]: ./media/search-get-started/AzureSearch_Configure1_6_URL.PNG
[12]: ./media/search-get-started/AzureSearch_Configure1_7a_Free.PNG
[13]: ./media/search-get-started/AzureSearch_Configure1_17_HomeDashboard.PNG
[14]: ./media/search-get-started/AzureSearch_Configure1_9_Standard.PNG
[15]: ./media/search-get-started/AzureSearch_Configure1_10_ScaleUp.PNG
[16]: ./media/search-get-started/AzureSearch_Configure1_11_PUTIndex.PNG
[17]: ./media/search-get-started/AzureSearch_Configure1_12_POSTDocs.PNG
[18]: ./media/search-get-started/AzureSearch_Configure1_13_GETQuery.PNG
[19]: ./media/search-get-started/AzureSearch_Configure1_14_GETQueryResponse.PNG
[20]: ./media/search-get-started/AzureSearch_Configure1_15_Stats.PNG
[21]: ./media/search-get-started/AzureSearch_Configure1_16_StatsResponse.PNG
[22]: ./media/search-get-started/AzureSearch_Configure1_17_HomeDashboard.PNG
[23]: ./media/search-get-started/AzureSearch_Configure1_18_Explore.PNG


<!--Link references-->
[Manage your search solution in Microsoft Azure]: search-manage.md
[Azure Search development workflow]: search-workflow.md
[Create your first azure search solution]: search-create-first-solution.md
[Create a geospatial search app using Azure Search]: search-create-geospatial.md

<!---HONumber=AcomDC_0114_2016-->