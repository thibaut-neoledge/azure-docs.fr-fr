<properties title="Prise en main d'Azure Search" pageTitle="Prise en main d'Azure Search" description="Prise en main d'Azure Search" metaKeywords="" services="Azure Search" solutions="" documentationCenter="" authors="Heidist" manager="mblythe" videoId="" scriptId="" />

<tags ms.service="azure-search" ms.devlang="" ms.workload="search" ms.topic="article" ms.tgt_pltfrm="" ms.date="09/23/2014" ms.author="heidist" />

# Prise en main d'Azure Search

[WACOM.INCLUDE [Cet article utilise la version préliminaire du portail Azure](../includes/preview-portal-note.md)]

Microsoft Azure Search (version préliminaire publique) est un nouveau service qui vous permet d'incorporer une fonctionnalité de recherche dans des applications personnalisées. Il fournit le moteur de recherche et le stockage pour vos données, auxquels vous accédez et que vous gérez à l'aide de l'API REST. Pour en savoir plus sur les circonstances dans lesquelles vous devez utiliser Azure Search, consultez la page [Scénarios et fonctionnalités d'Azure Search][Scénarios et fonctionnalités d'Azure Search].

En tant qu'administrateur, vous pouvez ajouter le service Search à un abonnement existant gratuitement lorsque vous choisissez le service partagé, ou à un tarif réduit lorsque vous choisissez des ressources dédiées. Cet article inclut les sections suivantes :

<!--Table of contents -->

-   [Démarrage du service gratuit][Démarrage du service gratuit]
-   [Mise à niveau vers des recherches standard][Mise à niveau vers des recherches standard]
-   [Test des opérations de service][Test des opérations de service]
-   [Exploration du tableau de bord du service Search][Exploration du tableau de bord du service Search]
-   [Faites un essai][Faites un essai]

## Démarrage du service gratuit

Les abonnés bénéficient automatiquement d'un accès gratuit à un service de recherche mutualisé et partagé pouvant être utilisé à des fins d'apprentissage, de tests de validation technique ou de projets de recherche de faible envergure. Inscrivez-vous pour accéder à la version gratuite :

1.  Connectez-vous à la [version préliminaire du portail Azure][version préliminaire du portail Azure] au moyen de votre abonnement existant. Notez que cette URL vous donne accès à la version préliminaire du portail. L'utilisation du portail de gestion est obligatoire.

2.  Cliquez sur **Nouveau** en bas de la page.

    ![][0]

3.  Cliquez sur **Tout** en haut de la page.

    ![][1]

4.  Dans la galerie, cliquez sur **Données, stockage, cache + sauvegarde**.

    ![][2]

5.  Cliquez sur **Tout afficher** pour développer la liste de tous les services liés aux données.

    ![][3]

6.  Dans Services de données, cliquez sur **Search**.

    ![][4]

7.  En bas de la page Search, cliquez sur **CRÉER**.

8.  Tapez un nom de service en minuscules pour l'URL du service, sans traits d'union ni espaces, et comportant au maximum 15 caractères.

    ![][5]

9.  Cliquez sur la flèche dans **Niveau de tarification** pour choisir un plan tarifaire. Choisissez **GRATUIT**, puis cliquez sur **CHOISIR** en bas de la page. La version gratuite offre une capacité suffisante pour essayer les didacticiels et écrire du code de validation technique, mais n'est pas destinée aux applications de production.

    ![][6]

10. Cliquez sur la flèche dans **Groupe de ressources** pour choisir un groupe existant ou en créer un. Les groupes de ressources sont des conteneurs pour les services et les ressources utilisés dans un but commun. Par exemple, si vous créez une application de recherche personnalisée basée sur Azure Search, Sites web Azure ou le stockage d'objets blob, vous pouvez créer un groupe de ressources qui réunit ces services dans les pages de gestion du portail.

11. Cliquez sur la flèche dans **Abonnement** si vous disposez de plusieurs abonnements et que vous voulez en utiliser un autre pour ce service de recherche.

12. Cliquez sur la flèche dans **Emplacement** pour choisir une région de centre de données. Dans cette version préliminaire, vous pouvez choisir entre Ouest des États-Unis, Est des États-Unis, Europe du Nord et Asie du Sud-Est. Plus tard, lorsque d'autres régions seront en ligne, vous choisirez une région pour le service que vous créez. La distribution de ressources dans plusieurs centres de données n'est pas une configuration prise en charge pour la version préliminaire publique.

13. Cliquez sur **CRÉER** pour approvisionner le service. Notez que **CRÉER** est activé uniquement après avoir renseigné toutes les valeurs requises.

Attendez quelques minutes que le service soit créé. Vous pouvez revenir aux paramètres de configuration pour obtenir l'URL ou les clés API. Les connexions à votre service Search requièrent que vous disposiez de l'URL et d'une clé API pour authentifier l'appel. Voici comment trouver ces valeurs rapidement :

1.  Cliquez sur **Parcourir** | **Tout** | **Données, stockage, cache, + sauvegarde** | **Tout afficher** | **Services de recherche**. Cliquez sur votre service de recherche pour ouvrir le tableau de bord du service.

    ![][7]

2.  Dans le tableau de bord du service, vous voyez des vignettes pour **PROPRIÉTÉS** et **CLÉS**, et des informations sur l'utilisation permettant de vérifier rapidement la façon dont les ressources sont utilisées.

    ![][8]

**PROPRIÉTÉS** contient l'URL de service.

**CLÉS** contient les clés API utilisées pour l'authentification.

**UTILISATION** indique le nombre de documents, les ressources disponibles et les limites de stockage.

Passez au [Test des opérations de service][Test des opérations de service] pour des informations sur la façon de se connecter au service au moyen de ces valeurs.

## Mise à niveau vers des recherches standard

La recherche standard vous permet d'accéder à des ressources dédiées dans un centre de données Azure réservé à votre seul usage. Les charges de travail de recherche nécessitent à la fois des réplicas de stockage et de service. Lorsque vous vous inscrivez à une recherche standard, vous pouvez optimiser la configuration du service pour utiliser une plus grande partie de la ressource qui est la plus importante pour votre scénario.

Le fait de disposer de davantage de ressources dédiées vous offre davantage de possibilités en matière de mise à l'échelle ainsi que de meilleures performances, mais pas de fonctionnalités supplémentaires. La recherche partagée et la recherche standard offrent toutes deux les mêmes fonctionnalités.

Pour utiliser la recherche standard, créez un service Search, en choisissant le niveau de tarification Standard. Notez que la mise à niveau n'est pas une mise à niveau sur place de la version gratuite. Le passage à l'offre standard, avec son potentiel en matière de mise à l'échelle, requiert un nouveau service. Il vous faudra recharger les index et les documents utilisés par votre application de recherche.

La configuration de ressources dédiées peut prendre du temps (15 minutes ou plus).

**Étape 1 - Créer un service avec le niveau de tarification Standard**

1.  Connectez-vous à la [version préliminaire du portail Azure][version préliminaire du portail Azure] au moyen de votre abonnement existant.

2.  Cliquez sur **Nouveau** en bas de la page.

3.  Cliquez sur **Tout** en haut de la page.

4.  Dans la galerie, cliquez sur **Données, stockage, cache + sauvegarde**.

5.  Cliquez sur **Tout afficher** pour développer la liste de tous les services liés aux données.

6.  Dans Services de données, cliquez sur **Search**.

7.  En bas de la page Search, cliquez sur **CRÉER**.

8.  Tapez un nom de service en minuscules pour l'URL du service, sans traits d'union ni espaces, et comportant au maximum 15 caractères.

9.  Cliquez sur la flèche dans **Niveau de tarification** pour choisir un plan tarifaire. Choisissez **STANDARD**, puis cliquez sur **CHOISIR** en bas de la page.

![][9]

**Étape 2 - Modifier les unités de recherche sur la base des besoins en matière de mise à l'échelle**

La recherche standard commence avec un réplica et une partition, mais elle peut facilement être mise à l'échelle à nouveau à des niveaux de ressource plus élevés.

1.  Une fois le service créé, revenez au tableau de bord des services, puis cliquez sur la vignette **Mettre à l'échelle**.

2.  Utilisez les curseurs pour ajouter des réplicas, des partitions, ou les deux.

Les réplicas et les partitions supplémentaires sont facturés en unités de recherche. Le nombre total d'unités de recherche requises pour prendre en charge une configuration de ressource particulière s'affiche sur la page, à mesure que vous ajoutez des ressources. Vous pouvez consulter les [Informations de tarification][Informations de tarification] pour obtenir les informations de facturation par unité.

![][10]

## Test des opérations de service

La dernière étape de la configuration de Search consiste à confirmer que votre service est opérationnel et accessible à partir d'une application cliente. Cette procédure utilise Fiddler, disponible en tant que [téléchargement à partir de Telerik][téléchargement à partir de Telerik], pour émettre des demandes HTTP et afficher les réponses. Avec Fiddler, vous pouvez tester l'API immédiatement, sans avoir à écrire du code.

La procédure suivante fonctionne à la fois pour la recherche partagée et la recherche standard. Dans la procédure ci-dessous, vous allez créer un index, télécharger des documents, interroger l'index, puis interroger le système pour obtenir des informations de service.

### Création d'un index

1.  Démarrez Fiddler. Dans le menu Fichier, désactivez **Capturer le trafic** pour masquer les activités HTTP externes qui ne sont pas en rapport avec la tâche actuelle. Sous l'onglet Éditeur, formulez une demande similaire à la suivante :

    ![][11]

2.  Sélectionnez **PUT**.

3.  Entrez une URL qui spécifie l'URL de service (figurant dans la page Propriétés), les attributs de demande et la version d'API. Voici quelques points à garder à l'esprit :

-   Utilisez le préfixe HTTPS.
-   L'attribut de demande est « /indexes/hotels ». Cela indique à Search de créer un index intitulé « hotels ».
-   La version d'API est en minuscules et elle est spécifiée comme suit : ?api-version=2014-07-31-preview. Les versions d'API sont importantes, car Azure Search déploie régulièrement des mises à jour. En de rares cas, une mise à jour de service peut introduire une modification avec rupture dans l'API. À l'aide de versions d'API, vous pouvez continuer à utiliser votre version existante et la mettre à niveau avec une version plus récente le cas échéant.

    L'URL complète doit être semblable à celle figurant dans l'exemple suivant :

         https://my-app.search.windows.net/indexes/hotels?api-version=2014-07-31-Preview

1.  Spécifiez l'en-tête de demande, en remplaçant l'hôte et la clé API par des valeurs qui sont valides pour votre service.

        User-Agent: Fiddler
        host: my-app.search.windows.net
        content-type: application/json
        api-key: 1111222233334444

2.  Dans Corps de la demande, collez les champs qui composent la définition d'index.

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

3.  Cliquez sur **Exécuter**.

Patientez quelques secondes pour voir s'afficher une réponse HTTP 204 dans la liste de sessions, ce qui indique que l'index a été créé correctement.

Si vous obtenez HTTP 504, vérifiez que l'URL spécifie HTTPS. Si vous voyez HTTP 400 ou 404, contrôlez le corps de la demande pour vérifier l'absence d'erreurs de copier-coller. HTTP 403 indique normalement qu'il y a un problème avec la clé API (soit la clé n'est pas valide, soit il y a un problème de syntaxe avec la façon dont elle est spécifiée).

### Chargement de documents

Sous l'onglet Éditeur, votre demande de publication de documents se présente comme suit. Le corps de la demande contient les données de recherche pour 4 hôtels.

![][12]

1.  Sélectionnez **POST**.

2.  Entrez une URL qui commence par HTTPS, suivie de votre URL de service, suivie de « /indexes/\<'indexname'\>/docs/index?api-version=2014-07-31-preview ». L'URL complète doit être semblable à celle figurant dans l'exemple suivant :

        https://my-app.search.windows.net/indexes/hotels/docs/index?api-version=2014-07-31-Preview

3.  L'en-tête de demande doit être le même qu'avant. Souvenez-vous que vous avez remplacé l'hôte et la clé API par des valeurs qui sont valides pour votre service.

        User-Agent: Fiddler
        host: my-app.search.windows.net
        content-type: application/json
        api-key: 1111222233334444

4.  Le corps de la demande contient quatre documents à ajouter à l'index des hôtels.

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

5.  Cliquez sur **Exécuter**.

Après quelques secondes, la réponse HTTP 200 apparaît dans la liste de sessions. Cela indique que les documents ont été correctement créés. Si vous obtenez HTTP 207, cela signifie qu'au moins un document n'a pas pu être chargé. Si HTTP 404 s'affiche, vous avez une erreur de syntaxe dans l'en-tête ou le corps de la demande.

### Interrogation de l'index

Maintenant qu'un index et des documents sont chargés, vous pouvez émettre des requêtes les concernant. Sous l'onglet Éditeur, une commande GET qui interroge votre service se présentera comme suit :

![][13]

1.  Sélectionnez **GET**.

2.  Entrez une URL qui commence par HTTPS, suivie de votre URL de service, suivie de « /indexes/\<'indexname'\>/docs? », suivie de paramètres de requête. Par exemple, utilisez l'URL suivante et remplacez l'exemple de nom d'hôte par celui qui convient à votre service.

        https://my-app.search.windows.net/indexes/hotels/docs?search=motel&facet=category&facet=rating,values:1|2|3|4|5&api-version=2014-07-31-Preview

    Cette requête effectue une recherche sur le terme « motel » et extrait les catégories de facette pour les évaluations.

3.  L'en-tête de demande doit être le même qu'avant. Souvenez-vous que vous avez remplacé l'hôte et la clé API par des valeurs qui sont valides pour votre service.

        User-Agent: Fiddler
        host: my-app.search.windows.net
        content-type: application/json
        api-key: 1111222233334444

Le code de réponse doit correspondre à 200 et la sortie de réponse doit se présenter comme suit.

![][14]

L'exemple de requête suivant provient de la page [Opération d'index de recherche (API Azure Search)][Opération d'index de recherche (API Azure Search)] sur MSDN. Plusieurs des exemples de requêtes dans cette rubrique comportent des espaces, qui ne sont pas autorisés dans Fiddler. Remplacez chaque espace par un caractère + avant de coller la chaîne de requête et d'essayer la requête dans Fiddler :

**Avant le remplacement des espaces :**

        GET /indexes/hotels/docs?search=*&$orderby=lastRenovationDate desc&api-version=2014-07-31-Preview

**Après le remplacement des espaces par + :**

        GET /indexes/hotels/docs?search=*&$orderby=lastRenovationDate+desc&api-version=2014-07-31-Preview

### Interrogation du système

Vous pouvez également interroger le système pour connaître le nombre de documents et l'espace de stockage utilisé. Sous l'onglet Éditeur, votre demande se présentera comme suit et la réponse renverra un nombre correspondant au nombre de documents et à l'espace utilisé.

![][15]

1.  Sélectionnez **GET**.

2.  Entrez une URL qui inclut votre URL de service, suivie de « /indexes/hotels/stats?api-version=2014-07-31-Preview » :

        https://my-app.search.windows.net/indexes/hotels/stats?api-version=2014-07-31-Preview 

3.  Spécifiez l'en-tête de demande, en remplaçant l'hôte et la clé API par des valeurs qui sont valides pour votre service.

        User-Agent: Fiddler
        host: my-app.search.windows.net
        content-type: application/json
        api-key: 1111222233334444

4.  Laissez le corps de la demande vide.

5.  Cliquez sur **Exécuter**. Le code d'état HTTP 200 doit s'afficher dans la liste de sessions. Sélectionnez l'entrée publiée pour votre commande.

6.  Cliquez sur l'onglet Inspecteurs | En-têtes, puis sélectionnez le format JSON. Le nombre de documents et la taille de stockage (en Ko) doivent s'afficher.

    ![][16]

## Exploration du tableau de bord du service Search

Si vous ne savez plus où se trouvent les pages de configuration, procédez comme suit pour trouver le tableau de bord des services.

1.  Connectez-vous à la [version préliminaire du portail Azure][version préliminaire du portail Azure] au moyen de votre abonnement existant.
2.  Cliquez sur **Parcourir** | **Tout**.

    ![][17]

3.  Choisissez **Services de recherche** dans la liste. La liste de tous les services de recherche créés sous vos abonnements doit s'afficher.

4.  Cliquez sur un service pour ouvrir le tableau de bord associé. Notez que les commandes **Démarrer**, **Arrêter** et **Supprimer** se trouvent en haut. Le tableau de bord du service inclut des vignettes permettant l'affichage des propriétés, des clés et d'un guide de démarrage rapide avec des liens vers des informations et des instructions. Faites défiler l'écran pour afficher les informations d'utilisation.

5.  Cliquez sur **PROPRIÉTÉS**. Notez que la page Propriétés s'ouvre à droite. L'URL de service figure en haut de la page. Pour accéder aux clés API et authentifier le service, cliquez sur **CLÉS**.

    ![][8]

<!--Next steps and links -->

## Faites un essai

Vous êtes prêt à passer à l'étape suivante ? Les liens suivants fournissent des informations supplémentaires vous expliquant comment créer et gérer des applications de recherche utilisant Azure Search.

-   [Création de votre première solution de recherche Azure][Création de votre première solution de recherche Azure]

-   [Création d'un exemple de recherche géospatiale Azure Search][Création d'un exemple de recherche géospatiale Azure Search]

-   [Gestion de votre solution de recherche dans Microsoft Azure][Gestion de votre solution de recherche dans Microsoft Azure]

-   [Présentation technique d'Azure Search][Présentation technique d'Azure Search]

-   [API REST d'Azure Search][API REST d'Azure Search]

-   [Vidéo Channel 9 : présentation d'Azure Search][Vidéo Channel 9 : présentation d'Azure Search]

-   [Vidéo Channel 9 : Azure Search et les données géospatiales][Vidéo Channel 9 : Azure Search et les données géospatiales]

-   [Cloud Cover, épisode 152 : Génération d'un index dans Azure Search][Cloud Cover, épisode 152 : Génération d'un index dans Azure Search]

<!--Anchors--> <!--Image references--> <!--Link references-->

  [Scénarios et fonctionnalités d'Azure Search]: http://azure.microsoft.com/blog/2014/08/28/azure-search-scenarios-and-capabilities/
  [Démarrage du service gratuit]: #sub-1
  [Mise à niveau vers des recherches standard]: #sub-2
  [Test des opérations de service]: #sub-3
  [Exploration du tableau de bord du service Search]: #sub-4
  [Faites un essai]: #next-steps
  [version préliminaire du portail Azure]: https://portal.azure.com
  [0]: ./media/search-get-started/AzureSearch_Configure1_1_New.PNG
  [1]: ./media/search-get-started/AzureSearch_Configure1_2_Everything.PNG
  [2]: ./media/search-get-started/Azuresearch_Configure1_3_Gallery.PNG
  [3]: ./media/search-get-started/AzureSearch_Configure1_4_GallerySeeAll.PNG
  [4]: ./media/search-get-started/AzureSearch_Configure1_5_DataServicesList.PNG
  [5]: ./media/search-get-started/AzureSearch_Configure1_6_URL.PNG
  [6]: ./media/search-get-started/AzureSearch_Configure1_7_Free.PNG
  [7]: ./media/search-get-started/AzureSearch_Configure1_8_SearchServiceList.PNG
  [8]: ./media/search-get-started/AzureSearch_Configure1_18_Explore.PNG
  [9]: ./media/search-get-started/AzureSearch_Configure1_9_Standard.PNG
  [Informations de tarification]: http://go.microsoft.com/fwlink/p/?LinkID=509792
  [10]: ./media/search-get-started/AzureSearch_Configure1_10_ScaleUp.PNG
  [téléchargement à partir de Telerik]: http://www.telerik.com/fiddler
  [11]: ./media/search-get-started/AzureSearch_Configure1_11_PUTIndex.PNG
  [12]: ./media/search-get-started/AzureSearch_Configure1_12_POSTDocs.PNG
  [13]: ./media/search-get-started/AzureSearch_Configure1_13_GETQuery.PNG
  [14]: ./media/search-get-started/AzureSearch_Configure1_14_GETQueryResponse.PNG
  [Opération d'index de recherche (API Azure Search)]: http://msdn.microsoft.com/fr-fr/library/dn798927.aspx
  [15]: ./media/search-get-started/AzureSearch_Configure1_15_Stats.PNG
  [16]: ./media/search-get-started/AzureSearch_Configure1_16_StatsResponse.PNG
  [17]: ./media/search-get-started/AzureSearch_Configure1_17_BrowseEverything.PNG
  [Création de votre première solution de recherche Azure]: ../search-create-first-solution/
  [Création d'un exemple de recherche géospatiale Azure Search]: ../search-create-geospatial/
  [Gestion de votre solution de recherche dans Microsoft Azure]: ../search-manage/
  [Présentation technique d'Azure Search]: http://msdn.microsoft.com/fr-fr/library/dn798933.aspx
  [API REST d'Azure Search]: http://msdn.microsoft.com/fr-fr/library/dn798935.aspx
  [Vidéo Channel 9 : présentation d'Azure Search]: http://channel9.msdn.com/Shows/Data-Exposed/Introduction-To-Azure-Search
  [Vidéo Channel 9 : Azure Search et les données géospatiales]: http://channel9.msdn.com/Shows/Data-Exposed/Azure-Search-and-Geospatial-Data
  [Cloud Cover, épisode 152 : Génération d'un index dans Azure Search]: http://channel9.msdn.com/Shows/Cloud+Cover/Cloud-Cover-152-Azure-Search-with-Liam-Cavanagh
