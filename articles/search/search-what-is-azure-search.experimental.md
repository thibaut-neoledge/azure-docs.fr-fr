---
title: "Qu’est-ce que la Recherche Azure ? | Microsoft Docs"
description: "Moteur de recherche doté de fonctionnalités de recherche avancée pour votre barre de recherche sur des sites Web personnalisés, dans vos applications et dans les magasins de données ou de fichiers d’entreprise."
services: search
manager: jhubbard
author: HeidiSteen
documentationcenter: 
ms.assetid: 
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 02/15/2017
ms.author: heidist
experimental: true
experiment_id: heidist-experiment1-20170221
translationtype: Human Translation
ms.sourcegitcommit: f7e657906d24693dffd84ebbd348d1940f271931
ms.openlocfilehash: a4e28e17cd3ce2488bd292a4c5e95fb532526a56
ms.lasthandoff: 02/21/2017

---
# <a name="what-is-azure-search"></a>Présentation d’Azure Search

La Recherche Azure propose un moteur de recherche dédié et programmable pourvu de comportements de recherche intelligents permettant d’alimenter la barre de recherche de contenu sur votre site Web, dans vos applications et dans les magasins de données ou de fichiers d’entreprise. 

![Barre de recherche avec la Recherche Azure](./media/search-what-is-azure-search/search-powered-by-azsearch3.png)

La plupart des applications web et mobiles nécessitent une expérience de recherche solide. Malgré la complexité technique sur laquelle repose l’expérience, la barre de recherche comporte au minimum la recherche en texte intégral avec des termes de requête automatiques, les corrections orthographiques et les correspondances basées sur les entrées sémantiquement identiques, mais en apparence différentes (« car » et « auto »). Les exigences opérationnelles en matière de mise à l’échelle, de fiabilité et de synchronisation entre les magasins de données principales et de recherche sont tout aussi importantes. 

La Recherche Azure offre un large éventail de fonctionnalités pour satisfaire les exigences opérationnelles et de recherche.

![Barre de recherche et page de recherche personnalisée avec des fonctionnalités de recherche classiques](./media/search-what-is-azure-search/search-page-callouts3.png)

## <a name="how-it-works"></a>Fonctionnement

Pour utiliser la Recherche Azure, configurez un service payant ou gratuit dans votre abonnement Azure, créez et chargez un index contenant votre contenu de recherche et appelez des API pour émettre des requêtes de recherche et traiter les résultats. Un service payant est requis si vous souhaitez disposer de ressources dédiées mises à l’échelle.

|Niveau |Description |
|-----|------------|
|[Gratuit](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F) | Service partagé qui peut être configuré rapidement et utilisé pour les petites charges de travail, généralement dans les scénarios de formation et d’évaluation. |
|[Facturables](search-sku-tier.md) | Service dédié à différents niveaux de capacité progressifs, de basique à haute densité standard, prenant en charge une large gamme de configurations de déploiement à différents niveaux tarifaires.|

La Recherche Azure est exécutée dans le cloud en tant que service géré de Microsoft. Elle peut être intégrée avec un code personnalisé sur n’importe quelle plateforme d’application. Votre service entièrement géré et le contenu privé sont disponibles partout, programmables, évolutifs et récupérables. 

Les services dédiés sont exécutés à l’échelle 24 heures sur 24, 7 jours sur 7 et [99,9 % des contrats de niveau de service](https://azure.microsoft.com/support/legal/sla/search/v1_0/) sont assurés par Microsoft Azure.

## <a name="how-it-compares"></a>Comparaison

Plusieurs fournisseurs de services cloud offrent des moteurs de recherche personnalisés comportant des fonctionnalités qui alimentent une barre de recherche dans les applications personnalisées. Certains proposent des fonctionnalités de base comparables, notamment la recherche en texte intégral, la recherche géographique et la capacité à gérer un certain niveau d’ambiguïté dans les entrées de recherche. En général, c’est une [fonctionnalité spécialisée](#feature-drilldown) ou l’ergonomie et la simplicité globales des API, des outils et de la gestion qui déterminent la meilleure option.

Par rapport aux autres solutions de recherche, la Recherche Azure est plus puissante pour les charges de travail de recherche en texte intégral sur les bases de données et magasins de contenu sur Azure. Pour les applications qui s’appuient principalement sur la recherche de navigation de contenu, une couche supplémentaire de fonctionnalités est nécessaire. 

+ Intégration de données Azure (robots) au niveau de la couche d’indexation
+ Portail Azure de gestion centralisée
+ Mise à l’échelle Azure, fiabilité et disponibilité de premier ordre
+ Analyse linguistique et personnalisée, incluant des analyseurs de recherche en texte intégral solide en 56 langues
+ Principales fonctionnalités communes à des applications centrées sur les recherches, y compris la pertinence, les facettes, les suggestions, les synonymes, la recherche géographique, etc. (cf. ci-dessous).

> [!Note]
> Les sources de données non Azure sont entièrement prises en charge. Vous pouvez diriger n’importe quelle collection de documents JSON vers un index de la Recherche Azure.

### <a name="a-namefeature-drilldownafeature-drilldown"></a><a name="feature-drilldown"></a>Détail des fonctionnalités

#### <a name="full-text-search-and-text-analysis"></a>Recherche en texte intégral et analyse de texte

Des requêtes puissantes peuvent être formulées à l’aide de la [syntaxe de requête simple](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search). Celle-ci offre des opérateurs logiques, de recherche d’expression, de suffixe et de précédence. Par ailleurs, la [syntaxe de requête Lucene](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search) permet des recherches approximatives, des recherches de proximité, la valorisation de termes et des expressions régulières. La Recherche Azure prend également en charge des [analyseurs lexicaux personnalisés](https://docs.microsoft.com/rest/api/searchservice/custom-analyzers-in-azure-search) pour permettre à votre application de traiter des requêtes complexes à l’aide de la correspondance phonétique et des expressions régulières.

#### <a name="language-support"></a>Support multilingue

La Recherche Azure prend en charge des analyseurs lexicaux dans [56 langues différentes](https://docs.microsoft.com/rest/api/searchservice/language-support). À l’aide des analyseurs Lucene et Microsoft (perfectionnés par des années de traitement de langage naturel dans Office et Bing), Recherche Azure peut analyser le texte dans la zone de recherche de votre application pour gérer intelligemment les caractéristiques linguistiques propres à la langue, notamment les temps des verbes, le masculin et le féminin, les noms au pluriel irrégulier (par exemple, « cheval » et « chevaux »), la décomposition des mots, la césure des mots (pour les langues sans espaces), etc.

#### <a name="data-integration"></a>Intégration des données

Vous pouvez transmettre des structures de données JSON pour remplir un index de Recherche Azure. En outre, pour les sources de données prises en charge, vous pouvez utiliser des [indexeurs](search-indexer-overview.md) pour analyser automatiquement Azure SQL Database, Azure DocumentDB ou le [Stockage Blob Azure](search-howto-indexing-azure-blob-storage.md) pour synchroniser le contenu de votre index de recherche avec votre magasin de données principal.

La **Recherche de document** permet [d’indexer la plupart des formats de fichier](search-howto-indexing-azure-blob-storage.md), y compris Microsoft Office, ainsi que les documents PDF et HTML.

#### <a name="search-experience"></a>Expérience de recherche

+ L’option **Suggestions de recherche** peut être activée pour les barres de recherche de saisie semi-automatique et pour les requêtes prédictives. [Les documents actuellement dans votre index sont suggérés](https://docs.microsoft.com/rest/api/searchservice/suggesters) lorsque les utilisateurs saisissent une entrée de recherche partielle.

+ La **navigation par facettes** est activée via un [simple paramètre de requête](https://docs.microsoft.com/azure/search/search-faceted-navigation). La Recherche Azure renvoie une structure de navigation par facettes que vous pouvez utiliser en tant que code de liste de catégories pour le filtrage autonome (par exemple, pour filtrer les éléments de catalogue par plage de prix ou par marque).

+ L’option **Filtres** permet d’intégrer la navigation par facettes dans l’interface utilisateur de votre application, d’améliorer la formulation des requêtes et de filtrer en fonction de critères spécifiés par les utilisateurs ou les développeurs. Créez des filtres à l’aide de la [syntaxe OData](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search).

+ Le **surligneur d’éléments** [applique la mise en forme visuelle à un mot clé correspondant dans les résultats de la recherche](https://docs.microsoft.com/rest/api/searchservice/Search-Documents). Vous pouvez choisir quels champs renvoient les extraits de texte en surbrillance.

+ **Notation simple** est des principaux avantages d'Azure Search. Les [Profils de score](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index) permettent de modéliser la pertinence en fonction des valeurs dans les documents eux-mêmes. Par exemple, vous souhaiterez peut-être que les nouveaux produits ou les produits en promotion apparaissent en priorité dans les résultats de la recherche. Vous pouvez également créer des profils de score à l'aide de balises pour obtenir un score personnalisé en fonction de préférences de recherche de client que vous avez suivies et stockées séparément.

+ **Tri** est proposée pour plusieurs champs via le schéma d'index, et activé ou désactivé au moment de la requête avec un paramètre de recherche unique.

+ **Pagination** et la limitation des résultats de la recherche sont [très simples avec le contrôle finement ajusté](search-pagination-page-layout.md) qu’offre Azure Search sur vos résultats de recherche.  

#### <a name="geosearch"></a>Recherche géographique

La Recherche Azure traite, filtre et affiche les emplacements géographiques de manière intelligente. Elle permet aux utilisateurs d’explorer les données en fonction de la proximité d’un résultat de recherche selon un emplacement spécifié, ou en fonction d’une région géographique spécifique. Cette vidéo explique comment cela fonctionne : [Channel 9 : Azure Search et les données géospatiales](https://channel9.msdn.com/Shows/Data-Exposed/Azure-Search-and-Geospatial-Data).

#### <a name="cloud-service-advantages"></a>Avantages du service cloud

+ **Haute disponibilité** garantit une expérience de service de recherche extrêmement fiable. Lorsqu’il est correctement mis à l'échelle, [Azure Search offre un SLA de 99,9 %](https://azure.microsoft.com/support/legal/sla/search/v1_0/).

+ **Entièrement géré** comme une solution de bout en bout, Azure Search ne requiert absolument aucune gestion de l'infrastructure. Votre service peut être adapté à vos besoins avec la mise à l’échelle en deux dimensions pour gérer plus de stockage de documents, plus de charge de requêtes, ou les deux.

#### <a name="monitoring-and-reporting"></a>Surveillance et création de rapports

+ Les **Analyses de trafic de recherche** sont [recueillies et analysées](search-traffic-analytics.md) pour fournir une source d’informations à partir des termes utilisés par les utilisateurs dans la zone de recherche.

+ Des mesures sur les requêtes par seconde, la latence et la limitation sont capturées et affichées sur les pages du portail sans aucune configuration supplémentaire. Vous pouvez également facilement surveiller le nombre d’index et de documents afin d’ajuster la capacité en fonction des besoins. 

#### <a name="tools-for-prototyping-and-inspection"></a>Outils de prototypage et d’inspection

Dans le portail, vous pouvez utiliser l’Assistant **Importation de données** pour configurer les indexeurs, le concepteur d’index pour configurer un index, et **l’Explorateur de recherche** pour émettre des requêtes sur tous vos index à partir du portail Azure de votre compte afin de pouvoir tester des requêtes et affiner des profils de score. Vous pouvez également ouvrir un index pour consulter son schéma.

## <a name="how-to-get-started"></a>Pour commencer

Démarrez avec un service gratuit, puis [créez et interrogez un index à l’aide d’exemples de données intégrées](search-get-started-portal.md). Vous pouvez utiliser le portail pour toutes les tâches. C’est un moyen rapide de tester la Recherche Azure avant d’avoir à écrire du code.

1. Les abonnés Azure peuvent [configurer un service dans le niveau Gratuit](search-create-service-portal.md).

  Les utilisateurs qui ne sont pas abonnés peuvent [ouvrir gratuitement un compte Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F) et utiliser les crédits gratuits pour essayer les services payants d’Azure. Une fois les crédits épuisés, vous pouvez conserver le compte et utiliser les services Azure gratuits, tels que les sites Web. Votre carte de crédit n’est pas débitée tant que vous n’avez pas explicitement modifié vos paramètres pour demander à l’être.

  Vous pouvez également [activer les avantages d’abonnement MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F). Votre abonnement MSDN vous donne droit chaque mois à des crédits dont vous pouvez vous servir pour les services Azure payants. 

2. Exécutez l’Assistant **Importation de données** sur [l’exemple de jeu de données intégrées sur l’immobilier](search-get-started-portal.md#create-index).

  Cet Assistant peut générer et charger un index à partir de la plupart des sources de données Azure. Un code est requis pour les sources de données qui ne sont pas spécifiquement prises en charge par l’Assistant.

3. Utilisez [l’Explorateur de recherche](search-get-started-portal.md#query-index) pour interroger l’index.

## <a name="rest-api--net-sdk"></a>API REST | .Net SDK

Bien que certaines tâches puissent être effectuées dans le portail, la Recherche Azure est conçue pour les développeurs qui souhaitent intégrer la fonctionnalité de recherche aux applications existantes. Les interfaces de programmation suivantes sont disponibles.

|Plateforme |Description |
|-----|------------|
|[REST](https://docs.microsoft.com/rest/api/searchservice/) | Commandes HTTP prises en charge par tous les langages et toutes les plateformes de programmation, y compris Xamarin, Java et JavaScript.|
|[Kit SDK .NET](search-howto-dotnet-sdk.md) | Le wrapper .NET pour l’API REST offre un codage efficace en C# et d’autres langages de code géré ciblant .NET Framework. |

## <a name="watch-a-short-video"></a>Regarder une courte vidéo

Les moteurs de recherche servent généralement à récupérer les informations sur les applications mobiles, sur le web et dans les magasins de données d’entreprise. Avec la Recherche Azure, vous disposez d’outils permettant de créer une expérience de recherche semblable à celle des grands sites web commerciaux. Cette vidéo de 9 minutes du responsable du programme Liam Cavanagh explique comment intégrer un moteur de recherche peut constituer un avantage pour votre application, présente les fonctionnalités clés dans la Recherche Azure et décrit un workflow typique. 

>[!VIDEO https://channel9.msdn.com/Events/Connect/2016/138/player]
 
+ Entre&0; et&3; minutes : présentation des principales fonctionnalités et cas d’emploi.
+ Entre&3; et&4; minutes : approvisionnement du service. 
+ Entre&4; et&6; minutes : utilisation de l’Assistant Importation de données pour créer un index à l’aide du jeu de données intégrées sur l’immobilier.
+ Entre&6; et&9; minutes : présentation de l’Explorateur de recherche et de différentes requêtes.


