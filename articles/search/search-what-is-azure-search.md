---
title: "Qu’est-ce que la Recherche Azure ? | Microsoft Docs"
description: "Azure Search est un service de recherche cloud hébergé intégralement géré. En savoir plus dans la vue d&quot;ensemble de cette fonctionnalité."
services: search
manager: jhubbard
author: ashmaka
documentationcenter: 
ms.assetid: 50bed849-b716-4cc9-bbbc-b5b34e2c6153
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 04/24/2017
ms.author: ashmaka
translationtype: Human Translation
ms.sourcegitcommit: 1cc1ee946d8eb2214fd05701b495bbce6d471a49
ms.openlocfilehash: 3720aed95706f6e8dd9a28085a6de0bc1f756a9b
ms.lasthandoff: 04/26/2017

---
# <a name="what-is-azure-search"></a>Présentation d’Azure Search
Le service Recherche Azure est une solution cloud de recherche sous forme de service qui délègue la gestion du serveur et de l’infrastructure à Microsoft pour vous laisser un service prêt à l’emploi que vous renseignez avec vos données pour ensuite ajouter une recherche à votre application web ou mobile. Recherche Azure vous permet de facilement ajouter une expérience de recherche fiable aux applications à l’aide d’une simple [API REST](https://msdn.microsoft.com/library/azure/dn798935.aspx) ou du [Kit de développement logiciel (SDK) .NET](search-howto-dotnet-sdk.md), sans avoir à gérer l’infrastructure de recherche ni même devenir un expert de la recherche.

## <a name="embed-a-powerful-search-experience-in-your-app-or-site"></a>Incorporer une puissante expérience de recherche dans votre application ou site


### <a name="full-text-search-and-text-analysis"></a>Recherche en texte intégral et analyse de texte

Des requêtes puissantes peuvent être formulées à l’aide de la [syntaxe de requête simple](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search). Celle-ci offre des opérateurs logiques, de recherche d’expression, de suffixe et de précédence. Par ailleurs, la [syntaxe de requête Lucene](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search) permet des recherches approximatives, des recherches de proximité, la valorisation de termes et des expressions régulières. La Recherche Azure prend également en charge des [analyseurs lexicaux personnalisés](https://docs.microsoft.com/rest/api/searchservice/custom-analyzers-in-azure-search) pour permettre à votre application de traiter des requêtes complexes à l’aide de la correspondance phonétique et des expressions régulières.

### <a name="language-support"></a>Support multilingue

La Recherche Azure prend en charge des analyseurs lexicaux dans [56 langues différentes](https://docs.microsoft.com/rest/api/searchservice/language-support). À l’aide des analyseurs Lucene et Microsoft (perfectionnés par des années de traitement de langage naturel dans Office et Bing), Recherche Azure peut analyser le texte dans la zone de recherche de votre application pour gérer intelligemment les caractéristiques linguistiques propres à la langue, notamment les temps des verbes, le masculin et le féminin, les noms au pluriel irrégulier (par exemple, « cheval » et « chevaux »), la décomposition des mots, la césure des mots (pour les langues sans espaces), etc.

### <a name="data-integration"></a>Intégration des données

Vous pouvez transmettre des structures de données JSON pour remplir un index de Recherche Azure. En outre, pour les sources de données prises en charge, vous pouvez utiliser des [indexeurs](search-indexer-overview.md) pour analyser automatiquement Azure SQL Database, Azure DocumentDB ou le [Stockage Blob Azure](search-howto-indexing-azure-blob-storage.md) pour synchroniser le contenu de votre index de recherche avec votre magasin de données principal.

La **Recherche de document** permet [d’indexer la plupart des formats de fichier](search-howto-indexing-azure-blob-storage.md), y compris Microsoft Office, ainsi que les documents PDF et HTML.

### <a name="search-experience"></a>Expérience de recherche

+ L’option **Suggestions de recherche** peut être activée pour les barres de recherche de saisie semi-automatique et pour les requêtes prédictives. [Les documents actuellement dans votre index sont suggérés](https://docs.microsoft.com/rest/api/searchservice/suggesters) lorsque les utilisateurs saisissent une entrée de recherche partielle.

+ La **navigation par facettes** est activée via un [simple paramètre de requête](https://docs.microsoft.com/azure/search/search-faceted-navigation). La Recherche Azure renvoie une structure de navigation par facettes que vous pouvez utiliser en tant que code de liste de catégories pour le filtrage autonome (par exemple, pour filtrer les éléments de catalogue par plage de prix ou par marque).

+ L’option **Filtres** permet d’intégrer la navigation par facettes dans l’interface utilisateur de votre application, d’améliorer la formulation des requêtes et de filtrer en fonction de critères spécifiés par les utilisateurs ou les développeurs. Créez des filtres à l’aide de la [syntaxe OData](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search).

+ Le **surligneur d’éléments** [applique la mise en forme visuelle à un mot clé correspondant dans les résultats de la recherche](https://docs.microsoft.com/rest/api/searchservice/Search-Documents). Vous pouvez choisir quels champs renvoient les extraits de texte en surbrillance.

+ **Notation simple** est des principaux avantages d'Azure Search. Les [Profils de score](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index) permettent de modéliser la pertinence en fonction des valeurs dans les documents eux-mêmes. Par exemple, vous souhaiterez peut-être que les nouveaux produits ou les produits en promotion apparaissent en priorité dans les résultats de la recherche. Vous pouvez également créer des profils de score à l'aide de balises pour obtenir un score personnalisé en fonction de préférences de recherche de client que vous avez suivies et stockées séparément.

+ **Tri** est proposée pour plusieurs champs via le schéma d'index, et activé ou désactivé au moment de la requête avec un paramètre de recherche unique.

+ **Pagination** et la limitation des résultats de la recherche sont [très simples avec le contrôle finement ajusté](search-pagination-page-layout.md) qu’offre Azure Search sur vos résultats de recherche.  

### <a name="geosearch"></a>Recherche géographique

La Recherche Azure traite, filtre et affiche les emplacements géographiques de manière intelligente. Elle permet aux utilisateurs d’explorer les données en fonction de la proximité d’un résultat de recherche selon un emplacement spécifié, ou en fonction d’une région géographique spécifique. Cette vidéo explique comment cela fonctionne : [Channel 9 : Azure Search et les données géospatiales](https://channel9.msdn.com/Shows/Data-Exposed/Azure-Search-and-Geospatial-Data).

### <a name="cloud-service-advantages"></a>Avantages du service cloud

+ **Haute disponibilité** garantit une expérience de service de recherche extrêmement fiable. Lorsqu’il est correctement mis à l'échelle, [Azure Search offre un SLA de 99,9 %](https://azure.microsoft.com/support/legal/sla/search/v1_0/).

+ **Entièrement géré** comme une solution de bout en bout, Azure Search ne requiert absolument aucune gestion de l'infrastructure. Votre service peut être adapté à vos besoins avec la mise à l’échelle en deux dimensions pour gérer plus de stockage de documents, plus de charge de requêtes, ou les deux.

### <a name="monitoring-and-reporting"></a>Surveillance et création de rapports

+ Les **Analyses de trafic de recherche** sont [recueillies et analysées](search-traffic-analytics.md) pour fournir une source d’informations à partir des termes utilisés par les utilisateurs dans la zone de recherche.

+ Des mesures sur les requêtes par seconde, la latence et la limitation sont capturées et affichées sur les pages du portail sans aucune configuration supplémentaire. Vous pouvez également facilement surveiller le nombre d’index et de documents afin d’ajuster la capacité en fonction des besoins. 

### <a name="tools-for-prototyping-and-inspection"></a>Outils de prototypage et d’inspection

Dans le portail, vous pouvez utiliser l’Assistant **Importation de données** pour configurer les indexeurs, le concepteur d’index pour configurer un index, et **l’Explorateur de recherche** pour émettre des requêtes sur tous vos index à partir du portail Azure de votre compte afin de pouvoir tester des requêtes et affiner des profils de score. Vous pouvez également ouvrir un index pour consulter son schéma.

## <a name="how-it-works"></a>Fonctionnement
### <a name="step-1-provision-service"></a>Étape 1 : configurer le service
Vous pouvez lancer un service Recherche Azure à l’aide du [portail Azure](https://portal.azure.com/) ou de [l’API de gestion des ressources Azure](https://msdn.microsoft.com/library/azure/dn832684.aspx).

Selon la manière dont vous configurez le service Search, vous pouvez utiliser le niveau de service gratuit partagé avec d'autres abonnés Azure Search, ou un [Niveau tarifaire](https://azure.microsoft.com/pricing/details/search/) qui propose des ressources dédiées utilisées uniquement par votre service. Lorsque vous configurez votre service, vous définissez également la région du centre de données qui héberge votre service.

En fonction du niveau de service que vous choisissez, vous pouvez faire évoluer votre service en deux dimensions : 1) ajouter des réplicas pour augmenter votre capacité à gérer les charges de requêtes lourdes et 2) ajouter des partitions pour ajouter le stockage de plus de documents. En gérant le débit de stockage et de requête de document séparément, vous pouvez personnaliser votre service de recherche selon vos besoins spécifiques.

### <a name="step-2-create-index"></a>Étape 2 : créer un index
Avant de pouvoir télécharger votre contenu sur votre service Azure Search, vous devez d'abord définir un index Azure Search. Un index est comparable à une table de base de données qui conserve vos données et peut accepter des requêtes de recherche. Vous définissez le schéma d'index pour mapper vers la structure des documents que vous souhaitez rechercher, similaires aux champs dans une base de données.

Le schéma de ces index peut être créé dans le portail Azure, ou programmé à l’aide du [Kit de développement logiciel (SDK) .NET](search-howto-dotnet-sdk.md) ou de [l’API REST](https://msdn.microsoft.com/library/azure/dn798941.aspx). Une fois l’index défini, vous pouvez télécharger vos données vers le service Azure Search où elles seront indexées par la suite.

### <a name="step-3-index-data"></a>Étape 3 : indexer les données
Une fois que vous avez défini les champs et les attributs de l'index, vous êtes prêt à télécharger votre contenu dans l'index. Vous pouvez utiliser un modèle d’émission ou de collecte pour télécharger des données vers l'index.

Le modèle de collecte est fourni par des indexeurs qui peuvent être configurés pour des mises à jour à la demande ou planifiées (consultez la rubrique [Opérations d'indexeur (API REST du service Azure Search)](https://msdn.microsoft.com/library/azure/dn946891.aspx)), ce qui vous permet de recevoir facilement les données et les modifications de données à partir d’Azure DocumentDB, Azure SQL Database, Azure Blob Storage, ou SQL Server, hébergé dans une machine virtuelle Azure.

Le modèle d’émission est fourni via le Kit de développement logiciel (SDK) ou les API REST permettant d’envoyer les documents mis à jour à un index. Vous pouvez émettre des données à partir de n'importe quel groupe de données à l’aide du format JSON. Pour obtenir des conseils sur le chargement des données, consultez [Ajout, mise à jour ou suppression de documents](https://msdn.microsoft.com/library/azure/dn798930.aspx) ou [Utilisation du Kit de développement logiciel (SDK) .NET](search-howto-dotnet-sdk.md).

### <a name="step-4-search"></a>Étape 4 : lancer la recherche
Une fois que vous avez rempli l'index Azure Search, vous pouvez à présent [émettre des requêtes de recherche](https://msdn.microsoft.com/library/azure/dn798927.aspx) sur le point de terminaison de service à l'aide de simples requêtes HTTP avec l'API REST ou le Kit de développement logiciel .NET.

## <a name="rest-api--net-sdk"></a>API REST | .Net SDK

Bien que certaines tâches puissent être effectuées dans le portail, la Recherche Azure est conçue pour les développeurs qui souhaitent intégrer la fonctionnalité de recherche aux applications existantes. Les interfaces de programmation suivantes sont disponibles.

|Plateforme |Description |
|-----|------------|
|[REST](https://docs.microsoft.com/rest/api/searchservice/) | Commandes HTTP prises en charge par tous les langages et toutes les plateformes de programmation, y compris Xamarin, Java et JavaScript.|
|[Kit SDK .NET](search-howto-dotnet-sdk.md) | Le wrapper .NET pour l’API REST offre un codage efficace en C# et d’autres langages de code géré ciblant .NET Framework. |

## <a name="free-trial"></a>Essai gratuit
Les abonnés Azure peuvent [configurer un service dans le niveau Gratuit](search-create-service-portal.md).

Si vous n’êtes pas abonné, vous pouvez [ouvrir un compte Azure gratuitement](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F) : vous obtenez alors des crédits dont vous pouvez vous servir pour tester les services Azure payants, et même lorsqu’ils sont épuisés, vous pouvez conserver le compte et utiliser les services Azure gratuits, notamment Sites Web. Votre carte de crédit ne sera pas débitée tant que vous n'aurez pas explicitement modifié vos paramètres pour demander à l'être.

Vous pouvez aussi [activer les avantages de l’abonnement MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F) : votre abonnement MSDN vous donne droit chaque mois à des crédits dont vous pouvez vous servir pour les services Azure payants. 

## <a name="watch-a-short-video"></a>Regarder une courte vidéo

Les moteurs de recherche servent généralement à récupérer les informations sur les applications mobiles, sur le web et dans les magasins de données d’entreprise. Avec la Recherche Azure, vous disposez d’outils permettant de créer une expérience de recherche semblable à celle des grands sites web commerciaux. Cette vidéo de 9 minutes du responsable du programme Liam Cavanagh explique comment intégrer un moteur de recherche peut constituer un avantage pour votre application, présente les fonctionnalités clés dans la Recherche Azure et décrit un workflow typique. 

>[!VIDEO https://channel9.msdn.com/Events/Connect/2016/138/player]
 
+ Entre 0 et 3 minutes : présentation des principales fonctionnalités et cas d’emploi.
+ Entre 3 et 4 minutes : approvisionnement du service. 
+ Entre 4 et 6 minutes : utilisation de l’Assistant Importation de données pour créer un index à l’aide du jeu de données intégrées sur l’immobilier.
+ Entre 6 et 9 minutes : présentation de l’Explorateur de recherche et de différentes requêtes.



