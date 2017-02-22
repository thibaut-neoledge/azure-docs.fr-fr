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
ms.date: 01/12/2017
ms.author: ashmaka
experiment_id: heidist-experiment1-20170221
translationtype: Human Translation
ms.sourcegitcommit: 292c9150822363aba3336b1efce579dc5362cb14
ms.openlocfilehash: 99b3babee9b252b2d741515391295d8b1dc5c747

---
# <a name="what-is-azure-search"></a>Présentation d’Azure Search
Le service Recherche Azure est une solution cloud de recherche sous forme de service qui délègue la gestion du serveur et de l’infrastructure à Microsoft pour vous laisser un service prêt à l’emploi que vous renseignez avec vos données pour ensuite ajouter une recherche à votre application web ou mobile. Recherche Azure vous permet de facilement ajouter une expérience de recherche fiable aux applications à l’aide d’une simple [API REST](https://msdn.microsoft.com/library/azure/dn798935.aspx) ou du [Kit de développement logiciel (SDK) .NET](search-howto-dotnet-sdk.md), sans avoir à gérer l’infrastructure de recherche ni même devenir un expert de la recherche.

## <a name="give-your-users-a-powerful-search-experience"></a>Offrez à vos utilisateurs une expérience de recherche puissante
Des **requêtes puissantes** peuvent être formulées à l’aide de la [syntaxe de requête simple](https://msdn.microsoft.com/library/azure/dn798920.aspx). Celle-ci offre des opérateurs logiques, de recherche d’expression, de suffixe et de précédence. Par ailleurs, la [syntaxe de requête Lucene](https://msdn.microsoft.com/library/azure/mt589323.aspx) permet des recherches approximatives, des recherches de proximité, la valorisation de termes et des expressions régulières. Azure Search prend également en charge des analyseurs lexicaux personnalisés pour permettre à votre application de traiter des requêtes complexes à l'aide de la correspondance phonétique et des expressions régulières.

**La prise en charge linguistique** est [incluse pour 56 langues différentes](https://msdn.microsoft.com/library/azure/dn879793.aspx). À l’aide des analyseurs Lucene et Microsoft (perfectionnés par des années de traitement de langage naturel dans Office et Bing), Recherche Azure peut analyser le texte dans la zone de recherche de votre application pour gérer intelligemment les caractéristiques linguistiques propres à la langue, notamment les temps des verbes, le masculin et le féminin, les noms au pluriel irrégulier (par exemple, « cheval » et « chevaux »), la décomposition des mots, la césure des mots (pour les langues sans espaces), etc.

**Suggestions de recherche** peut être activée pour les barres de recherche de saisie semi-automatique et pour les requêtes prédictives. [Les documents actuellement dans votre index sont suggérés](https://msdn.microsoft.com/library/azure/dn798936.aspx) lorsque les utilisateurs saisissent une entrée de recherche partielle.

**Mise en surbrillance des correspondances** [permet](https://msdn.microsoft.com/library/azure/dn798927.aspx) aux utilisateurs de voir l'extrait de texte dans chaque résultat qui contient les correspondances de leur requête. Vous pouvez choisir quels champs renvoient les extraits de texte en surbrillance.

**Navigation à facettes** peut facilement être ajoutée à votre page de résultats avec Azure Search. À l’aide d’un [simple paramètre de requête](https://msdn.microsoft.com/library/azure/dn798927.aspx), la Recherche Azure renvoie toutes les informations nécessaires pour créer une expérience de recherche à facettes dans l’interface utilisateur de votre application, afin de permettre aux utilisateurs d’explorer en détail et de filtrer les résultats de la recherche (par exemple, filtrer des éléments de catalogue par plage de prix ou par marque).

**Géospatiale** vous permet de traiter, de filtrer et d’afficher intelligemment les emplacements géographiques. Azure Search permet aux utilisateurs d'explorer les données en fonction de la proximité d'un résultat de recherche selon un emplacement spécifié, ou en fonction d’une région géographique spécifique. Cette vidéo explique comment cela fonctionne : [Channel 9 : Azure Search et les données géospatiales](https://channel9.msdn.com/Shows/Data-Exposed/Azure-Search-and-Geospatial-Data).

**Filtres** permet d’intégrer facilement la navigation à facettes dans l’interface utilisateur de votre application, d’améliorer la formulation des requêtes et de filtrer en fonction de critères spécifiés par les utilisateurs ou les développeurs. Créez des filtres puissants à l’aide de la [syntaxe OData](https://msdn.microsoft.com/library/azure/dn798921.aspx).

## <a name="empower-your-developers-with-an-easy-to-use-service"></a>Donnez l'avantage à vos développeurs avec un service facile à utiliser
**Haute disponibilité** garantit une expérience de service de recherche extrêmement fiable. Lorsqu’il est correctement mis à l'échelle, [Azure Search offre un SLA de 99,9 %](https://azure.microsoft.com/support/legal/sla/search/v1_0/).

**Entièrement géré** comme une solution de bout en bout, Azure Search ne requiert absolument aucune gestion de l'infrastructure. Votre service peut être facilement adapté à vos besoins avec la mise à l'échelle en deux dimensions pour gérer plus de stockage de documents, plus de charge de requêtes, ou les deux.

**L’intégration de données** à l’aide [d’indexeurs](https://msdn.microsoft.com/library/azure/dn946891.aspx) permet à la Recherche Azure d’analyser automatiquement Azure SQL Database, Azure DocumentDB ou le [Stockage Blob Azure](search-howto-indexing-azure-blob-storage.md) pour synchroniser le contenu de votre index de recherche avec votre magasin de données principal.

**Recherche de document** est disponible (actuellement en version préliminaire) [pour lire et indexer la plupart des formats de fichiers](search-howto-indexing-azure-blob-storage.md) , notamment les documents Microsoft Office, PDF et HTML.

Les **Analyses de trafic de recherche** sont [facilement recueillies et analysées](search-traffic-analytics.md) pour fournir une source d’information à partir des termes utilisés par les utilisateurs dans la zone de recherche.

**Notation simple** est des principaux avantages d'Azure Search. [Profils de score](https://msdn.microsoft.com/library/azure/dn798928.aspx) permettent aux entreprises de modéliser la pertinence en fonction des valeurs dans les documents eux-mêmes. Par exemple, vous souhaiterez peut-être que les nouveaux produits ou les produits en promotion apparaissent en priorité dans les résultats de la recherche. Vous pouvez également créer des profils de score à l'aide de balises pour obtenir un score personnalisé en fonction de préférences de recherche de client que vous avez suivies et stockées séparément.

**Tri** est proposée pour plusieurs champs via le schéma d'index, et activé ou désactivé au moment de la requête avec un paramètre de recherche unique.

**Pagination** et la limitation des résultats de la recherche sont [très simples avec le contrôle finement ajusté](search-pagination-page-layout.md) qu’offre Azure Search sur vos résultats de recherche.  

**Navigateur Recherche** vous permet d'émettre des requêtes sur tous vos index directement depuis le portail Azure de votre compte afin de pouvoir facilement tester des requêtes et affiner des profils de score.

## <a name="how-it-works"></a>Fonctionnement
### <a name="1-provision-service"></a>1. Configuration du service
Vous pouvez lancer un service Recherche Azure à l’aide du [portail Azure](https://portal.azure.com/) ou de [l’API de gestion des ressources Azure](https://msdn.microsoft.com/library/azure/dn832684.aspx).

Selon la manière dont vous configurez le service Search, vous pouvez utiliser le niveau de service gratuit partagé avec d'autres abonnés Azure Search, ou un [Niveau tarifaire](https://azure.microsoft.com/pricing/details/search/) qui propose des ressources dédiées utilisées uniquement par votre service. Lorsque vous configurez votre service, vous définissez également la région du centre de données qui héberge votre service.

En fonction du niveau de service que vous choisissez, vous pouvez faire évoluer votre service en deux dimensions : 1) ajouter des réplicas pour augmenter votre capacité à gérer les charges de requêtes lourdes et 2) ajouter des partitions pour ajouter le stockage de plus de documents. En gérant le débit de stockage et de requête de document séparément, vous pouvez personnaliser votre service de recherche selon vos besoins spécifiques.

### <a name="2-create-index"></a>2. Création d’index
Avant de pouvoir télécharger votre contenu sur votre service Azure Search, vous devez d'abord définir un index Azure Search. Un index est comparable à une table de base de données qui conserve vos données et peut accepter des requêtes de recherche. Vous définissez le schéma d'index pour mapper vers la structure des documents que vous souhaitez rechercher, similaires aux champs dans une base de données.

Le schéma de ces index peut être créé dans le portail Azure, ou programmé à l’aide du [Kit de développement logiciel (SDK) .NET](search-howto-dotnet-sdk.md) ou de [l’API REST](https://msdn.microsoft.com/library/azure/dn798941.aspx). Une fois l’index défini, vous pouvez télécharger vos données vers le service Azure Search où elles seront indexées par la suite.

### <a name="3-index-data"></a>3. Données d'index
Une fois que vous avez défini les champs et les attributs de l'index, vous êtes prêt à télécharger votre contenu dans l'index. Vous pouvez utiliser un modèle d’émission ou de collecte pour télécharger des données vers l'index.

Le modèle de collecte est fourni par des indexeurs qui peuvent être configurés pour des mises à jour à la demande ou planifiées (consultez la rubrique [Opérations d'indexeur (API REST du service Azure Search)](https://msdn.microsoft.com/library/azure/dn946891.aspx)), ce qui vous permet de recevoir facilement les données et les modifications de données à partir d’Azure DocumentDB, Azure SQL Database, Azure Blob Storage, ou SQL Server, hébergé dans une machine virtuelle Azure.

Le modèle d’émission est fourni via le Kit de développement logiciel (SDK) ou les API REST permettant d’envoyer les documents mis à jour à un index. Vous pouvez émettre des données à partir de n'importe quel groupe de données à l’aide du format JSON. Pour obtenir des conseils sur le chargement des données, consultez [Ajout, mise à jour ou suppression de documents](https://msdn.microsoft.com/library/azure/dn798930.aspx) ou [Utilisation du Kit de développement logiciel (SDK) .NET](search-howto-dotnet-sdk.md).

### <a name="4-search"></a>4. Search
Une fois que vous avez rempli l'index Azure Search, vous pouvez à présent [émettre des requêtes de recherche](https://msdn.microsoft.com/library/azure/dn798927.aspx) sur le point de terminaison de service à l'aide de simples requêtes HTTP avec l'API REST ou le Kit de développement logiciel .NET.

## <a name="try-it-now-for-free"></a>Faites un essai dès maintenant (gratuitement !)
Les abonnés Azure peuvent [configurer un service dans le niveau Gratuit](search-create-service-portal.md).

Si vous n’êtes pas abonné, vous pouvez [ouvrir un compte Azure gratuitement](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F) : vous obtenez alors des crédits dont vous pouvez vous servir pour tester les services Azure payants, et même lorsqu’ils sont épuisés, vous pouvez conserver le compte et utiliser les services Azure gratuits, notamment Sites Web. Votre carte de crédit ne sera pas débitée tant que vous n'aurez pas explicitement modifié vos paramètres pour demander à l'être.

Vous pouvez aussi [activer les avantages de l’abonnement MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F) : votre abonnement MSDN vous donne droit chaque mois à des crédits dont vous pouvez vous servir pour les services Azure payants. 





<!--HONumber=Jan17_HO2-->


