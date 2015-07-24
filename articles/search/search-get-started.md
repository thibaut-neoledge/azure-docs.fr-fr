<properties 
	pageTitle="Prise en main d'Azure Search" 
	description="Prise en main d'Azure Search" 
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
	ms.date="01/16/2015" 
	ms.author="heidist"/>

# Prise en main d'Azure Search

[AZURE.INCLUDE [Cet article utilise le portail Azure Preview](../../includes/preview-portal-note.md)]

Microsoft Azure Search est un nouveau service qui permet d'incorporer des fonctionnalités de recherche dans des applications personnalisées. Il fournit le moteur de recherche et assure le stockage de vos données, dont l'accès et la gestion se fait à l'aide d'un Kit de développement logiciel (SDK) .NET ou d'une API REST. Pour en savoir plus sur les raisons pour lesquelles vous devez utiliser Azure Search, consultez [Scénarios et fonctionnalités d'Azure Search](http://azure.microsoft.com/blog/2014/08/28/azure-search-scenarios-and-capabilities/).  

En tant qu'administrateur, vous pouvez ajouter le service Search à un abonnement existant gratuitement lorsque vous choisissez le service partagé, ou à un tarif réduit lorsque vous choisissez des ressources dédiées. Cet article inclut les sections suivantes :

<a id="sub-1"></a>
## Démarrage avec le service gratuit

Les abonnés bénéficient automatiquement d'un accès gratuit à un service de recherche mutualisé et partagé pouvant être utilisé à des fins d'apprentissage, de tests de validation technique ou de projets de recherche de faible envergure. Inscrivez-vous pour accéder à la version gratuite :

1. Connectez-vous au [portail Azure Preview](https://portal.azure.com) à l'aide de votre abonnement existant. Notez que cette URL vous donne accès à la version préliminaire du portail. L'utilisation du portail de gestion est obligatoire. 

2. Cliquez sur **Nouveau** en bas de la page.
 
  	![][6]

3. Cliquez sur **Tout** en haut de la page. 

  	![][7]

4. Dans la galerie, cliquez sur **Données + analyse**.
 
  	![][8]

5. Dans Services de données, cliquez sur **Rechercher**.
 
  	![][10]

7. En bas de la page Rechercher, cliquez sur **CRÉER**.

8. Tapez le nom de service en minuscules à utiliser dans l'URL du service, sans espaces, et avec au maximum 15 caractères.
 
  	![][11]

9. Cliquez sur la flèche dans **Niveau de tarification** pour sélectionner une option de tarification. Choisissez **GRATUIT** puis cliquez sur **SÉLECTIONNER** en bas de la page. La version gratuite offre une capacité suffisante pour essayer les didacticiels et écrire du code de validation technique, mais n'est pas destinée aux applications de production. 

  	![][12]

10. Cliquez sur la flèche dans **Groupe de ressources** pour choisir un groupe existant ou créez-en un. Les groupes de ressources sont des conteneurs pour les services et les ressources utilisés dans un but commun. Par exemple, si vous créez une application de recherche personnalisée basée sur Azure Search, Sites web Azure ou le stockage d'objets blob, vous pouvez créer un groupe de ressources qui réunit ces services dans les pages de gestion du portail.

11. Cliquez sur la flèche dans **Abonnement** si vous possédez plusieurs abonnements et que vous voulez utiliser un autre abonnement pour ce service de recherche.

12. Cliquez sur la flèche dans **Emplacement** pour choisir la région du centre de données. Dans cette version préliminaire, vous pouvez choisir entre Ouest des États-Unis, Est des États-Unis, Europe du Nord et Asie du Sud-Est. Plus tard, lorsque d'autres régions seront en ligne, vous choisirez une région pour le service que vous créez. La distribution de ressources dans plusieurs centres de données n'est pas une configuration prise en charge pour la version préliminaire publique.

13. Cliquez sur **CRÉER** pour configurer le service. Notez que l'option **CRÉER** est activée uniquement après avoir complété toutes les valeurs. 

Attendez quelques minutes que le service soit créé. Vous pouvez revenir aux paramètres de configuration pour obtenir l'URL ou les clés API. Les connexions à votre service Search requièrent que vous disposiez de l'URL et d'une clé API pour authentifier l'appel. Voici comment trouver ces valeurs rapidement :

14. Accédez à l'**Accueil** pour ouvrir le tableau de bord. Cliquez sur le service de recherche pour ouvrir le tableau de bord du service. 

  	![][13]

15.	Le tableau de bord du service affiche les vignettes des **PROPRIÉTÉS** et des **CLÉS** ainsi que des informations pratiques sur l'utilisation des ressources. 

  	![][23]

   **PROPRIÉTÉS** contient l'URL du service. 

   **CLÉS** contient les clés API utilisées pour l'authentification.
 
   **UTILISATION** indique le nombre de documents, les ressources disponibles et les limites de stockage.

Passez à la section [Test des opérations de service](#sub-3) pour des instructions sur la façon de se connecter au service à l'aide de ces valeurs.

<a id="sub-2"></a>
## Passage à la recherche standard

La recherche standard vous permet d'accéder à des ressources dédiées dans un centre de données Azure réservé à votre seul usage. Les charges de travail de recherche nécessitent à la fois des réplicas de stockage et de service. Lorsque vous vous inscrivez à une recherche standard, vous pouvez optimiser la configuration du service pour utiliser une plus grande partie de la ressource qui est la plus importante pour votre scénario.

Le fait de disposer de davantage de ressources dédiées vous offre davantage de possibilités en matière de mise à l'échelle ainsi que de meilleures performances, mais pas de fonctionnalités supplémentaires. La recherche partagée et la recherche standard offrent toutes deux les mêmes fonctionnalités.

Pour utiliser la recherche standard, créez un service Search, en choisissant le niveau de tarification Standard. Notez que la mise à niveau n'est pas une mise à niveau sur place de la version gratuite. Le passage à l'offre standard, avec son potentiel en matière de mise à l'échelle, requiert un nouveau service. Il vous faudra recharger les index et les documents utilisés par votre application de recherche.

La configuration de ressources dédiées peut prendre du temps (15 minutes ou plus). 

**Étape 1 : créer un service avec un niveau de tarification défini sur Standard**

1. Connectez-vous au [portail Azure Preview](https://portal.azure.com) à l'aide de votre abonnement existant. 

2. Cliquez sur **Nouveau** en bas de la page.

3. Cliquez sur **Tout** en haut de la page.

4. Dans la galerie, cliquez sur **Données + analyse**.

6. Dans Services de données, cliquez sur **Rechercher**.

7. En bas de la page Rechercher, cliquez sur **CRÉER**.

8. Tapez un nom de service en minuscules pour l'URL du service, sans traits d'union ni espaces, et comportant au maximum 15 caractères.

9. Cliquez sur la flèche dans **Niveau de tarification** pour sélectionner une option de tarification. Choisissez **STANDARD**, puis cliquez sur **SÉLECTIONNER** en bas de la page.

 ![][14]

**Étape 2 : ajuster les unités de recherche en fonction de l'échelle requise**

La recherche standard commence avec un réplica et une partition, mais elle peut facilement être mise à l'échelle à nouveau à des niveaux de ressource plus élevés.

1.	Une fois le service créé, retournez au tableau de bord du service, cliquez sur la vignette **Mettre à l'échelle**.

2.	Utilisez les curseurs pour ajouter des réplicas, des partitions, ou les deux. 

Les réplicas et les partitions supplémentaires sont facturés en unités de recherche. Le nombre total d'unités de recherche requises pour prendre en charge une configuration de ressource particulière s'affiche sur la page, à mesure que vous ajoutez des ressources. 

Vous pouvez consulter les [Informations de tarification](http://go.microsoft.com/fwlink/p/?LinkID=509792) pour obtenir les informations de facturation par unité. Consultez [Limites et contraintes](http://msdn.microsoft.com/library/azure/dn798934.aspx) pour savoir comment configurer des combinaisons de partitions et de réplicas.

 ![][15]

<a id="sub-3"></a>
## Test des opérations de service

La dernière étape de la configuration de Search consiste à confirmer que votre service est opérationnel et accessible à partir d'une application cliente. Cette procédure utilise Fiddler, disponible en [téléchargement gratuit à partir de Telerik](http://www.telerik.com/fiddler), pour envoyer des requêtes HTTP et afficher les réponses. Avec Fiddler, vous pouvez tester l'API immédiatement, sans avoir à écrire du code. 

La procédure suivante fonctionne à la fois pour la recherche partagée et la recherche standard. Dans la procédure ci-dessous, vous allez créer un index, télécharger des documents, interroger l'index, puis interroger le système pour obtenir des informations de service.

### Création d'un index

1. Démarrez Fiddler. Dans le menu Fichier, désactivez **Capturer le trafic** pour masquer l'activité HTTP externe sans rapport avec la tâche actuelle. Sous l'onglet Éditeur, formulez une demande similaire à la suivante : 

  	![][16]

2. Sélectionnez **PUT**.

3. Entrez une URL qui spécifie l'URL de service (figurant dans la page Propriétés), les attributs de demande et la version d'API. Voici quelques points à garder à l'esprit :
   + Utilisez le préfixe HTTPS.
   + L'attribut de demande est " /indexes/hotels ". Cela indique à Search de créer un index intitulé 'hotels'.
   + La version d'API est en minuscules et elle est spécifiée comme suit : ?api-version=2014-07-31-preview. Les versions d'API sont importantes, car Azure Search déploie régulièrement des mises à jour. En de rares cas, une mise à jour de service peut introduire une modification avec rupture dans l'API. À l'aide de versions d'API, vous pouvez continuer à utiliser votre version existante et la mettre à niveau avec une version plus récente le cas échéant.

    L'URL complète doit être semblable à celle figurant dans l'exemple suivant :

         https://my-app.search.windows.net/indexes/hotels?api-version=2014-07-31-Preview

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
          {"name": "description", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "suggestions": true},
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

Patientez quelques secondes pour voir s'afficher une réponse HTTP 201 dans la liste de sessions, ce qui indique que l'index a été créé correctement. 

Si vous obtenez HTTP 504, vérifiez que l'URL spécifie HTTPS. Si vous voyez HTTP 400 ou 404, contrôlez le corps de la demande pour vérifier l'absence d'erreurs de copier-coller. HTTP 403 indique normalement qu'il y a un problème avec la clé API (soit la clé n'est pas valide, soit il y a un problème de syntaxe avec la façon dont elle est spécifiée).

### Chargement de documents

Sous l'onglet Éditeur, votre demande de publication de documents se présente comme suit. Le corps de la demande contient les données de recherche pour 4 hôtels.

   ![][17]

1. Sélectionnez **POST**.

2.	Entrez une URL qui commence par HTTPS, suivie de votre URL de service, suivie de " /indexes/<'indexname'>/docs/index?api-version=2014-07-31-preview ". L'URL complète doit être semblable à celle figurant dans l'exemple suivant :

        https://my-app.search.windows.net/indexes/hotels/docs/index?api-version=2014-07-31-Preview

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

Après quelques secondes, la réponse HTTP 200 apparaît dans la liste de sessions. Cela indique que les documents ont été correctement créés. Si vous obtenez HTTP 207, cela signifie qu'au moins un document n'a pas pu être chargé. Si HTTP 404 s'affiche, vous avez une erreur de syntaxe dans l'en-tête ou le corps de la demande.

### Interrogation de l'index

Maintenant qu'un index et des documents sont chargés, vous pouvez émettre des requêtes les concernant.  Sous l'onglet Éditeur, une commande GET qui interroge votre service se présentera comme suit :

   ![][18]

1.	Sélectionnez **GET**.

2.	Entrez une URL qui commence par HTTPS, suivie de votre URL de service, suivie de " /indexes/<'indexname'>/docs? ", suivie de paramètres de requête. Par exemple, utilisez l'URL suivante et remplacez l'exemple de nom d'hôte par celui qui convient à votre service.

        https://my-app.search.windows.net/indexes/hotels/docs?search=motel&facet=category&facet=rating,values:1|2|3|4|5&api-version=2014-07-31-Preview

    Cette requête effectue une recherche sur le terme " motel " et extrait les catégories de facette pour les évaluations.

3.	L'en-tête de demande doit être le même qu'avant. Souvenez-vous que vous avez remplacé l'hôte et la clé API par des valeurs qui sont valides pour votre service.

        User-Agent: Fiddler
        host: my-app.search.windows.net
        content-type: application/json
        api-key: 1111222233334444

Le code de réponse doit correspondre à 200 et la sortie de réponse doit se présenter comme suit.
 
   ![][19]

L'exemple de requête suivant provient de la page [Opération d'index de recherche (API Azure Search)](http://msdn.microsoft.com/library/dn798927.aspx) sur MSDN. Plusieurs des exemples de requêtes dans cette rubrique comportent des espaces, qui ne sont pas autorisés dans Fiddler. Remplacez chaque espace par un caractère + avant de coller la chaîne de requête et d'essayer la requête dans Fiddler : 

**Avant le remplacement des espaces :**

        GET /indexes/hotels/docs?search=*&$orderby=lastRenovationDate desc&api-version=2014-07-31-Preview

**Après le remplacement des espaces par + :**

        GET /indexes/hotels/docs?search=*&$orderby=lastRenovationDate+desc&api-version=2014-07-31-Preview

### Interrogation du système

Vous pouvez également interroger le système pour connaître le nombre de documents et l'espace de stockage utilisé. Sous l'onglet Éditeur, votre demande se présentera comme suit et la réponse renverra un nombre correspondant au nombre de documents et à l'espace utilisé.

   ![][20]

1.	Sélectionnez **GET**.

2.	Entrez une URL qui inclut votre URL de service, suivie de " /indexes/hotels/stats?api-version=2014-07-31-Preview " :

        https://my-app.search.windows.net/indexes/hotels/stats?api-version=2014-07-31-Preview 

3.	Spécifiez l'en-tête de demande, en remplaçant l'hôte et la clé API par des valeurs qui sont valides pour votre service.

        User-Agent: Fiddler
        host: my-app.search.windows.net
        content-type: application/json
        api-key: 1111222233334444

4.	Laissez le corps de la demande vide.

5.	Cliquez sur **Exécuter**. Le code d'état HTTP 200 doit s'afficher dans la liste de sessions. Sélectionnez l'entrée publiée pour votre commande.

6.	Cliquez sur l'onglet Inspecteurs | En-têtes, puis sélectionnez le format JSON. Le nombre de documents et la taille de stockage (en Ko) doivent s'afficher.

 	![][21]

<a id="sub-4"></a>
## Exploration du tableau de bord du service Search

Si vous ne savez plus où se trouvent les pages de configuration, procédez comme suit pour trouver le tableau de bord des services.

1.	Connectez-vous au [portail Azure Preview](https://portal.azure.com) à l'aide de votre abonnement existant. 
2.	Cliquez sur **Accueil**, puis sur la vignette du service Search.

 	![][22]

4.	Vous accédez alors au tableau de bord du service. Notez que les commandes **Démarrer**, **Arrêter** et **Supprimer** se trouvent en haut. Le tableau de bord du service inclut des vignettes permettant l'affichage des propriétés, des clés et d'un guide de démarrage rapide avec des liens vers des informations et des instructions. Faites défiler l'écran pour afficher les informations d'utilisation.

5.	Cliquez sur **PROPRIÉTÉS**. Notez que la page Propriétés s'ouvre à droite. L'URL de service figure en haut de la page. Vous aurez besoin de cette URL pour vous connecter à votre service Azure Search.

 	![][23]
	
7.	Cliquez sur **CLÉS** pour afficher les clés API. Vous aurez besoin d'une clé d'administration pour vous authentifier auprès du service. Vous pouvez utiliser la clé primaire ou secondaire. Vous pouvez éventuellement créer des clés de requête pour accéder au service en lecture seule.


<!--Next steps and links -->
<a id="next-steps"></a>
## Faites un essai

Vous êtes prêt à passer à l'étape suivante ? Les liens suivants fournissent des informations supplémentaires vous expliquant comment créer et gérer des applications de recherche utilisant Azure Search.

- [Création de votre première solution de recherche Azure](search-create-first-solution.md) 

- [Création d'un exemple de recherche géospatiale Azure Search](search-create-geospatial.md) 

- [Gestion de votre solution de recherche dans Microsoft Azure](search-manage.md) 

- [Présentation technique d'Azure Search](http://msdn.microsoft.com/library/dn798933.aspx) 

- [API REST d'Azure Search](http://msdn.microsoft.com/library/dn798935.aspx) 

- [Vidéo Channel 9 : présentation d'Azure Search](http://channel9.msdn.com/Shows/Data-Exposed/Introduction-To-Azure-Search) 

- [Vidéo Channel 9 : Azure Search et les données géospatiales](http://channel9.msdn.com/Shows/Data-Exposed/Azure-Search-and-Geospatial-Data) 

- [Cloud Cover épisode 152 : génération d'un index dans Azure Search](http://channel9.msdn.com/Shows/Cloud+Cover/Cloud-Cover-152-Azure-Search-with-Liam-Cavanagh) 
<!--Anchors-->
[Démarrage avec le service gratuit]: #sub-1
[Passage à la recherche standard]: #sub-2
[Test des opérations de service]: #sub-3
[Exploration du tableau de bord du service Search]: #sub-4
[Faites un essai]: #next-steps

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
[Gestion de votre solution de recherche dans Microsoft Azure]: search-manage.md
[Flux de travail de développement Azure Search]: search-workflow.md
[Création de votre première solution de recherche Azure]: search-create-first-solution.md
[Création d'une application de recherche géospatiale à l'aide d'Azure Search]: search-create-geospatial.md

<!---HONumber=49--> 