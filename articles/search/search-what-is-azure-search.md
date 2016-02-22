<properties
	pageTitle="Présentation d’Azure Search | Microsoft Azure | Service de recherche cloud hébergé"
	description="Azure Search est un service de recherche cloud hébergé intégralement géré. En savoir plus dans la vue d'ensemble de cette fonctionnalité."
	services="search"
	authors="ashmaka"
	documentationCenter=""/>

<tags
	ms.service="search"
	ms.devlang="NA"
	ms.workload="search"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.date="02/09/2016"
	ms.author="ashmaka"/>

# Présentation d’Azure Search
Azure Search est une solution cloud de recherche en tant que service qui délègue la gestion du serveur et de l’infrastructure à Microsoft, et vous laisse un service prêt à l’emploi que vous remplissez avec des données de recherche, pour ensuite ajouter une recherche à votre application web ou mobile. Azure Search vous permet de facilement ajouter une expérience de recherche fiable aux applications à l’aide d’une simple [API REST](https://msdn.microsoft.com/library/azure/dn798935.aspx) ou du [Kit de développement logiciel (SDK) .NET](search-howto-dotnet-sdk.md), sans avoir à gérer l’infrastructure de recherche ni même devenir un expert de la recherche.

## Offrez à vos utilisateurs une expérience de recherche puissante

Des **requêtes puissantes** peuvent être formulées à l’aide de la [syntaxe de requête simple](https://msdn.microsoft.com/library/azure/dn798920.aspx). Celle-ci offre des opérateurs logiques, de recherche d’expression, de suffixe et de précédence. Par ailleurs, la [syntaxe de requête Lucene](https://msdn.microsoft.com/library/azure/mt589323.aspx) permet des recherches approximatives, des recherches de proximité, la valorisation de termes et des expressions régulières. Azure Search prend également en charge des analyseurs lexicaux personnalisés pour permettre à votre application de traiter des requêtes complexes à l'aide de la correspondance phonétique et des expressions régulières.

**La prise en charge linguistique** est [incluse pour 56 langues différentes](https://msdn.microsoft.com/library/azure/dn879793.aspx). À l’aide des analyseurs Lucene et Microsoft (perfectionnés par des années de traitement de langage naturel dans Office et Bing), Azure Search peut analyser le texte dans la zone de recherche de votre application pour gérer intelligemment les caractéristiques linguistiques propres à la langue, notamment les temps des verbes, le masculin et le féminin, les noms au pluriel irrégulier (par exemple, « cheval » et « chevaux »), la décomposition des mots, la césure des mots (pour les langues sans espaces), etc.

L’option **Suggestions de recherche** peut être activée pour les barres de recherche de saisie semi-automatique et pour les requêtes prédictives. [Les documents actuellement dans votre index sont suggérés](https://msdn.microsoft.com/library/azure/dn798936.aspx) lorsque les utilisateurs saisissent une entrée de recherche partielle.

L’option **Mise en surbrillance des correspondances** [permet](https://msdn.microsoft.com/library/azure/dn798927.aspx) aux utilisateurs de voir l'extrait de texte dans chaque résultat qui contient les correspondances de leur requête. Vous pouvez choisir quels champs renvoient les extraits de texte en surbrillance.

L’option **Navigation à facettes** peut facilement être ajoutée à votre page de résultats avec Azure Search. À l'aide d’un [simple paramètre de requête](https://msdn.microsoft.com/library/azure/dn798927.aspx), Azure Search renvoie toutes les informations nécessaires pour créer une expérience de recherche à facettes dans l'interface utilisateur de votre application, afin de permettre aux utilisateurs d’explorer en détail et de filtrer les résultats de la recherche (par exemple, filtrer des éléments de catalogue par plage de prix ou par marque).

La [prise en charge](search-create-geospatial.md) de l’option **Géospatiale** vous permet de traiter, de filtrer et d’afficher intelligemment les emplacements géographiques. Azure Search permet aux utilisateurs d'explorer les données en fonction de la proximité d'un résultat de recherche selon un emplacement spécifié, ou en fonction d’une région géographique spécifique.

L’option **Filtres** permet d’intégrer facilement la navigation à facettes dans l’interface utilisateur de votre application, d’améliorer la formulation des requêtes et de filtrer en fonction de critères spécifiés par les utilisateurs ou les développeurs. Créez des filtres puissants à l’aide de la [syntaxe OData](https://msdn.microsoft.com/library/azure/dn798921.aspx).

## Donnez l'avantage à vos développeurs avec un service facile à utiliser

La **Haute disponibilité** garantit une expérience de service de recherche extrêmement fiable. Lorsqu’il est correctement mis à l'échelle, [Azure Search offre un SLA de 99,9 %](https://azure.microsoft.com/support/legal/sla/search/v1_0/).

**Entièrement géré** comme une solution de bout en bout, Azure Search ne requiert absolument aucune gestion de l'infrastructure. Votre service peut être facilement adapté à vos besoins avec la mise à l'échelle en deux dimensions pour gérer plus de stockage de documents, plus de charge de requêtes, ou les deux.

L’**Intégration de données** à l’aide d’[indexeurs](https://msdn.microsoft.com/library/azure/dn946891.aspx) permet à Azure Search d’analyser automatiquement Azure SQL Database, Azure DocumentDB ou [Azure Blob Storage](search-howto-indexing-azure-blob-storage.md) pour synchroniser le contenu de votre index de recherche avec votre magasin de données principal.

La **Recherche de document** est disponible (actuellement en version préliminaire) [pour lire et indexer la plupart des formats de fichiers](search-howto-indexing-azure-blob-storage.md), notamment les documents Microsoft Office, PDF et HTML.

Les **Analyses de trafic de recherche** sont [facilement recueillies et analysées](search-traffic-analytics.md) pour fournir une source d’information à partir des termes utilisés par les utilisateurs dans la zone de recherche.

La **Notation simple** est des principaux avantages d'Azure Search. Les [Profils de score](https://msdn.microsoft.com/library/azure/dn798928.aspx) permettent aux entreprises de modéliser la pertinence en fonction des valeurs dans les documents eux-mêmes. Par exemple, vous souhaiterez peut-être que les nouveaux produits ou les produits en promotion apparaissent en priorité dans les résultats de la recherche. Vous pouvez également créer des profils de score à l'aide de balises pour obtenir un score personnalisé en fonction de préférences de recherche de client que vous avez suivies et stockées séparément.

L’option **Tri** est proposée pour plusieurs champs via le schéma d'index, et activé ou désactivé au moment de la requête avec un paramètre de recherche unique.

La **Pagination** et la limitation des résultats de la recherche sont [très simples avec le contrôle finement ajusté](search-pagination-page-layout.md) qu’offre Azure Search sur vos résultats de recherche.

Le **Navigateur Recherche** vous permet d'émettre des requêtes sur tous vos index directement depuis le portail Azure de votre compte afin de pouvoir facilement tester des requêtes et affiner des profils de score.

## Fonctionnement

### 1\. Configuration du service
Vous pouvez lancer un service Azure Search à l'aide du [portail Azure](https://portal.azure.com/) ou de l’[API de gestion des ressources Azure](https://msdn.microsoft.com/library/azure/dn832684.aspx).

Selon la façon dont vous configurez le service, vous pouvez utiliser le Niveau gratuit partagé avec d'autres abonnés Azure Search, ou le [Niveau tarifaire](https://azure.microsoft.com/pricing/details/search/) standard, qui offre des ressources dédiées utilisées uniquement par votre service. Lorsque vous configurez votre service, vous définissez également la région du centre de données qui héberge votre service.

Lorsque vous utilisez Azure Search au niveau Standard, vous pouvez faire évoluer votre service en deux dimensions : 1) ajouter des réplicas pour augmenter votre capacité à gérer les charges de requêtes lourdes et (2) ajouter des partitions pour ajouter plus de stockage pour plus de documents. En gérant le débit de stockage et de requête de document séparément, vous pouvez personnaliser votre service de recherche selon vos besoins spécifiques.

### 2\. Création d’index
Avant de pouvoir télécharger votre contenu sur votre service Azure Search, vous devez d'abord définir un index Azure Search. Un index est comparable à une table de base de données qui conserve vos données et peut accepter des requêtes de recherche. Vous définissez le schéma d'index pour mapper vers la structure des documents que vous souhaitez rechercher, similaires aux champs dans une base de données.

Le schéma de ces index peut être créé dans le portail Azure, ou programmé à l'aide du [Kit de développement logiciel (SDK) .NET](search-howto-dotnet-sdk.md) ou de l’[API REST](https://msdn.microsoft.com/library/azure/dn798941.aspx). Une fois l’index défini, vous pouvez télécharger vos données vers le service Azure Search où elles seront indexées par la suite.

### 3\. Données d'index
Une fois que vous avez défini les champs et les attributs de l'index, vous êtes prêt à télécharger votre contenu dans l'index. Vous pouvez utiliser un modèle d’émission ou de collecte pour télécharger des données vers l'index.

Le modèle de collecte est fourni par des indexeurs qui peuvent être configurés pour des mises à jour à la demande ou planifiées (consultez la rubrique [Opérations d'indexeur (API REST du service Azure Search)](https://msdn.microsoft.com/library/azure/dn946891.aspx)), ce qui vous permet de recevoir facilement les données et les modifications de données à partir d’Azure DocumentDB, Azure SQL Database, Azure Blob Storage, ou SQL Server, hébergé dans une machine virtuelle Azure.

Le modèle d’émission est fourni via le Kit de développement logiciel (SDK) ou les API REST permettant d’envoyer les documents mis à jour à un index. Vous pouvez émettre des données à partir de n'importe quel groupe de données à l’aide du format JSON. Consultez la rubrique [Ajout, mise à jour ou suppression de documents](https://msdn.microsoft.com/library/azure/dn798930.aspx) ou [Utilisation du Kit de développement logiciel (SDK) .NET](search-howto-dotnet-sdk.md) pour obtenir des conseils sur le chargement des données.

### 4\. Search
Une fois que vous avez rempli l'index Azure Search, vous pouvez à présent [émettre des requêtes de recherche](https://msdn.microsoft.com/library/azure/dn798927.aspx) sur le point de terminaison de service à l'aide de simples requêtes HTTP avec l'API REST ou le Kit de développement logiciel .NET.

## Faites un essai dès maintenant (gratuitement !)
Vous pouvez essayer Azure Search dès aujourd'hui ! Si vous avez déjà un compte Azure, vous pouvez [configurer un service au niveau Gratuit](search-create-service-portal.md).

Si vous n'avez pas un compte Azure, que vous pouvez essayer une session gratuite de 60 minutes sans aucune inscription nécessaire. Accédez à [Essayer Azure App Service](http://go.microsoft.com/fwlink/p/?LinkId=618214) et sélectionnez « Application web ». Puis, sélectionnez le modèle « ASP.NET + Azure Search » pour commencer.

<!---HONumber=AcomDC_0211_2016-->