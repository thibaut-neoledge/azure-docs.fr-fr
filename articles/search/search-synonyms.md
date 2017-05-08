<properties
   pageTitle="Synonymes dans Azure Search (version préliminaire) | Microsoft Docs"
   description="Documentation préliminaire pour la fonction Synonymes (version préliminaire) exposée dans l’API REST Azure Search."
   services="search"
   documentationCenter=""
   authors="mhko"
   manager="pablocas"
   editor=""/>

<tags
   ms.service="search"
   ms.devlang="rest-api"
   ms.workload="search"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.date="07/07/2016"
   ms.author="nateko"/>


# <a name="synonyms-in-azure-search-preview"></a>Synonymes dans Azure Search (version préliminaire)

Dans les moteurs de recherche, les synonymes associent des termes équivalents qui élargissent implicitement l’étendue d’une requête, sans que l’utilisateur ait à fournir le terme. Par exemple, si l’on considère le terme « chien » et les associations de synonymes « canin » et « chiot », tous les documents contenant « chien », « canin » ou « chiot » seront pris en compte dans la requête.

Dans Azure Search, l’expansion des synonymes est effectuée au moment de la requête. Vous pouvez ajouter des cartes de synonymes à un service sans interrompre les opérations existantes. Vous pouvez ajouter une propriété **synonymMaps** à une définition de champ sans avoir à reconstruire l’index. Pour plus d’informations, consultez [Mise à jour d’index](https://docs.microsoft.com/rest/api/searchservice/update-index).

## <a name="feature-availability"></a>Disponibilité des fonctionnalités

La fonctionnalité de synonymes est actuellement en version préliminaire et prise en charge uniquement dans les dernières versions d’API préliminaires (api-version=2016-09-01-Preview). Il n’existe aucune prise en charge sur le portail Azure pour l’instant. Comme la version de l’API est spécifiée dans la requête, il est possible de combiner les API mises à la disposition générale (GA) et les API en version préliminaire disponibles dans la même application. Cependant, les API de versions préliminaires ne sont pas soumises à un contrat SLA, et les fonctionnalités peuvent changer ; par conséquent, nous ne recommandons pas de les utiliser dans des applications de production.

## <a name="how-to-use-synonyms-in-azure-search"></a>Guide pratique pour utiliser des synonymes dans Azure Search

Dans Azure Search, la prise en charge des synonymes repose sur des cartes de synonymes que vous définissez et chargez sur votre service. Ces cartes constituent des ressources indépendantes (telles que des index ou des sources de données) et peuvent être utilisées par n’importe quel champ de recherche dans un index de votre service de recherche.

Les cartes de synonymes et les index sont gérés de manière indépendante. Une fois que vous définissez une carte de synonymes et la téléchargez sur votre service, vous pouvez activer la fonctionnalité de synonyme sur un champ en ajoutant une nouvelle propriété nommée **synonymMaps** dans la définition du champ. La création, la mise à jour et la suppression d’une carte de synonymes constituent des opérations qui s’appliquent au document entier, ce qui signifie que vous ne peut pas créer, mettre à jour ou supprimer des parties de la carte de synonyme de façon incrémentielle. Même la mise à jour d’une seule entrée nécessite un rechargement.

L’incorporation de synonymes dans votre application de recherche est un processus en deux étapes :

1.    Ajoutez une carte de synonymes à votre service de recherche via les API ci-dessous.  

2.    Configurez un champ pouvant faire l’objet d’une recherche pour utiliser la carte de synonymes dans la définition d’index.

### <a name="synonymmaps-resource-apis"></a>API de ressources SynonymMaps

#### <a name="add-or-update-a-synonym-map-under-your-service-using-post-or-put"></a>Ajoutez ou mettez à jour une carte de synonymes dans votre service à l’aide d’une requête POST ou PUT.

Les cartes de synonymes sont téléchargées sur le service via une requête POST ou PUT. Chaque règle doit être délimitée par le caractère de nouvelle ligne ('\n'). Vous pouvez définir jusqu'à 5 000 règles par carte de synonymes dans un service gratuit et 10 000 règles dans toutes les autres références (SKU). Chaque règle peut avoir jusqu'à 20 extensions.

Dans cette version préliminaire, les cartes de synonymes doivent être au format Apache Solr décrit ci-dessous. Si vous disposez d’un dictionnaire de synonymes existant dans un autre format et souhaitez l’utiliser directement, faites-le-nous savoir sur [UserVoice](https://feedback.azure.com/forums/263029-azure-search).

Vous pouvez créer une nouvelle carte de synonymes à l’aide d’une requête HTTP POST, comme dans l’exemple suivant :

    POST https://[servicename].search.windows.net/synonymmaps?api-version=2016-09-01-Preview
    api-key: [admin key]

    {  
       "name":"mysynonymmap",
       "format":"solr",
       "synonyms": "
          USA, United States, United States of America\n
          Washington, Wash., WA => WA\n"
    }

Vous pouvez également utiliser une requête PUT en spécifiant le nom de la carte de synonymes sur l'URI. Si la carte de synonymes n'existe pas, elle est créée.

    PUT https://[servicename].search.windows.net/synonymmaps/mysynonymmap?api-version=2016-09-01-Preview
    api-key: [admin key]

    {  
       "format":"solr",
       "synonyms": "
          USA, United States, United States of America\n
          Washington, Wash., WA => WA\n"
    }

##### <a name="apache-solr-synonym-format"></a>Format de synonymes Apache Solr

Le format Solr prend en charge les cartes de synonymes équivalentes et explicites. Les règles de mappage respectent la spécification de filtre de synonyme open source d’Apache Solr, décrite dans ce document : [SynonymFilter](https://cwiki.apache.org/confluence/display/solr/Filter+Descriptions#FilterDescriptions-SynonymFilter). Voici un exemple de règle pour des synonymes équivalents.
```
              USA, United States, United States of America
```

Avec la règle ci-dessus, une requête de recherche « USA » s’étendra à « USA » OR « United States » OR « United States of America ».

Un mappage explicite est indiqué par une flèche « => ». Lorsqu’elle spécifiée, une séquence de termes d’une requête de recherche qui correspond à la partie gauche de « => » est remplacée par les alternatives sur la partie droite. Étant donné la règle ci-dessous, les requêtes de recherche « Washington », « Wash. » ou « WA » seront réécrites « WA ». Le mappage explicite s’applique dans le sens spécifié uniquement et ne réécrit pas la requête « WA » en « Washington » dans ce cas.
```
              Washington, Wash., WA => WA
```

#### <a name="list-synonym-maps-under-your-service"></a>Répertorier les cartes de synonymes de votre service.

    GET https://[servicename].search.windows.net/synonymmaps?api-version=2016-09-01-Preview
    api-key: [admin key]

#### <a name="get-a-synonym-map-under-your-service"></a>Obtenir une carte de synonymes pour votre service.

    GET https://[servicename].search.windows.net/synonymmaps/mysynonymmap?api-version=2016-09-01-Preview
    api-key: [admin key]

#### <a name="delete-a-synonyms-map-under-your-service"></a>Supprimer une carte de synonymes de votre service.

    DELETE https://[servicename].search.windows.net/synonymmaps/mysynonymmap?api-version=2016-09-01-Preview
    api-key: [admin key]

### <a name="configure-a-searchable-field-to-use-the-synonym-map-in-the-index-definition"></a>Configurez un champ pouvant faire l’objet d’une recherche pour utiliser la carte de synonymes dans la définition d’index.

Une nouvelle propriété de champ **synonymMaps** permet de spécifier une carte de synonymes à utiliser pour un champ pouvant faire l’objet d’une recherche. Les cartes de synonymes sont des ressources de niveau de service et peuvent être référencées selon n’importe quel champ d’index dans le service.

    POST https://[servicename].search.windows.net/indexes?api-version=2016-09-01-Preview
    api-key: [admin key]

    {
       "name":"myindex",
       "fields":[
          {
             "name":"id",
             "type":"Edm.String",
             "key":true
          },
          {
             "name":"name",
             "type":"Edm.String",
             "searchable":true,
             "analyzer":"en.lucene",
             "synonymMaps":[
                "mysynonymmap"
             ]
          },
          {
             "name":"name_jp",
             "type":"Edm.String",
             "searchable":true,
             "analyzer":"ja.microsoft",
             "synonymMaps":[
                "japanesesynonymmap"
             ]
          }
       ]
    }

**synonymMaps** peut être spécifié pour les champs pouvant faire l’objet d’une recherche de type « Edm.String » ou « Collection(Edm.String) ».

> [!NOTE]
> Dans cette version préliminaire, vous pouvez uniquement utiliser une carte de synonymes par champ. Si vous souhaitez utiliser plusieurs cartes de synonymes, faites-le-nous savoir sur [UserVoice](https://feedback.azure.com/forums/263029-azure-search).

## <a name="impact-of-synonyms-on-other-search-features"></a>Impact des synonymes sur les autres fonctionnalités de recherche

La fonctionnalité de synonymes réécrit la requête d’origine avec des synonymes utilisant l’opérateur OR. Pour cette raison, la mise en surbrillance des correspondances et les profils de score traitent les synonymes et le terme d’origine comme équivalents.

La fonctionnalité de synonymes s’applique aux requêtes de recherche et non aux filtres ou facettes. De même, les suggestions sont basées uniquement sur le terme d’origine : les correspondances de synonymes n’apparaissent pas dans la réponse.

Les extensions de synonymes ne s’appliquent pas aux termes de recherche génériques : les préfixes, correspondances partielles et les expressions régulières ne sont pas étendus.

## <a name="tips-for-building-a-synonym-map"></a>Conseils pour créer une carte de synonymes

- Une carte de synonymes claire et précise est plus efficace qu’une liste exhaustive des correspondances possibles. Les dictionnaires très volumineux ou complexes prennent plus de temps à analyser et affectent la latence des requêtes si la requête s’étend sur plusieurs synonymes. Au lieu de deviner les termes qui peuvent être utilisés, vous pouvez obtenir les termes réels via un [rapport d’analyse du trafic de recherche](search-traffic-analytics.md).

- Comme exercice préliminaire et de validation, activez et utilisez ce rapport pour déterminer avec précision les termes les plus propices à une carte de synonymes, puis continuez à utiliser ces termes pour valider le fait que votre carte de synonymes génère un meilleur résultat. Dans le rapport prédéfini, les titres « Requêtes de recherche courantes » et « Requêtes de recherche sans résultats » vous fourniront les informations nécessaires.

- Vous pouvez créer plusieurs cartes de synonymes pour votre application de recherche (par exemple, par langue si votre application prend en charge une base de clients multilingue). Actuellement, un champ peut uniquement utiliser une de ces deux méthodes. Vous pouvez mettre à jour la propriété synonymMaps d’un champ à tout moment.

## <a name="next-steps"></a>Étapes suivantes

- Si vous avez un index existant dans un environnement de déploiement (non production), faites des essais avec un petit dictionnaire pour voir comment l’ajout de synonymes modifie l’expérience de recherche, notamment son impact sur les profils de score, la mise en surbrillance des correspondances et les suggestions.

- [Activez l’analyse du trafic des recherches](search-traffic-analytics.md) et utilisez le rapport Power BI prédéfini pour connaître les termes les plus utilisés, et ceux qui ne renvoient aucun document. Grâce à ces informations, modifiez le dictionnaire afin d’inclure les synonymes pour les requêtes non productives qui devraient être associées à des documents dans l’index.

