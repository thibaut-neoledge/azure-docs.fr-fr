<properties
	pageTitle="Créer un service Azure Search à l’aide du portail Azure | Microsoft Azure | Service de recherche cloud hébergé"
	description="Découvrez comment configurer un service Azure Search à l’aide du portail Azure."
	services="search"
	authors="ashmaka"
	documentationCenter=""/>

<tags
	ms.service="search"
	ms.devlang="NA"
	ms.workload="search"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.date="03/10/2016"
	ms.author="ashmaka"/>

# Créer un service Azure Search à l’aide du portail Azure

Ce guide vous explique comment créer (ou approvisionner) un service Azure Search à l’aide du [portail Azure](https://portal.azure.com/).

Ce guide suppose que vous disposez d’un abonnement Azure et que vous êtes en mesure de vous connecter au portail Azure.

## I. Trouver Azure Search sur le portail Azure
1. Accédez au [portail Azure](https://portal.azure.com/) et connectez-vous.
1. Cliquez sur le signe plus (« + ») dans le coin supérieur gauche.
2. Sélectionnez « Données + stockage ».
3. Sélectionnez « Azure Search ».

![](./media/search-create-service-portal/find-search.png)

## II. Choisir un nom de service et un point de terminaison d’URL pour le service
1. Le nom de votre service fait partie de l’URL de point de terminaison de votre service Azure Search que vous utilisez pour effectuer vos appels d’API afin de gérer et d’utiliser le service de recherche.
2. Tapez le nom de votre service dans le champ « URL ». Le nom du service :
  * doit contenir uniquement des lettres minuscules, des chiffres ou des tirets (« - ») ;
  * ne pas utiliser de tiret (« - ») pour les 2 premiers caractères ni pour le dernier ;
  * ne peut pas contenir de tirets consécutifs (« -- ») ;
  * doit comprendre au minimum 2 caractères et au maximum 60.


## III. Sélectionner un abonnement dans lequel conserver le service
Si vous avez plusieurs abonnements, vous pouvez sélectionner celui qui inclura ce service Azure Search.

## IV. Sélectionner un groupe de ressources pour le service
Créez un groupe de ressources ou sélectionnez-en un. Un groupe de ressources correspond à une collection de services et ressources Azure utilisés ensemble. Par exemple, si vous utilisez Azure Search pour indexer une base de données SQL, ces deux services doivent faire partie du même groupe de ressources.

## V. Sélectionner l’emplacement d’hébergement du service
En sa qualité de service Azure, Azure Search peut être hébergé dans les centres de données du monde entier. Veuillez noter que les [prix peuvent varier](https://azure.microsoft.com/pricing/details/search/) selon la zone géographique.

## VI. Sélectionner un niveau tarifaire
[Azure Search est actuellement disponible à différents niveaux tarifaires](https://azure.microsoft.com/pricing/details/search/) : Gratuit, De base ou Standard. Chaque niveau est associé à ses propres [capacité et limites](search-limits-quotas-capacity.md).

Ici, nous avons choisi le niveau Standard pour notre service.

## VII. Cliquer sur le bouton « Créer » pour configurer le service

![](./media/search-create-service-portal/create-service.png)

## VIII. Mettre à l’échelle le service

Une fois votre service approvisionné, vous pouvez le mettre à l’échelle en fonction de vos besoins. Si vous avez choisi le niveau Standard pour votre service Azure Search, vous pouvez le mettre à l’échelle dans deux dimensions : réplicas et partitions. Si vous avez choisi le niveau De base, vous pouvez uniquement ajouter des réplicas.

Les *__partitions__* permettent à votre service de stocker plus de documents et d’effectuer des recherches dans plus de documents.

Les *__réplicas__* permettent à votre service de traiter plus de requêtes de recherche. [Un service requiert 2 réplicas pour obtenir un contrat SLA en lecture seule et nécessite 3 réplicas pour obtenir un contrat SLA en lecture/écriture](https://azure.microsoft.com/support/legal/sla/search/v1_0/).

1. Accédez au panneau de gestion de votre service Azure Search dans le portail Azure.
2. Dans le panneau « Paramètres », sélectionnez « Mise à l'échelle ».
3. Vous pouvez mettre votre service à l’échelle en ajoutant des réplicas ou des partitions.
  * Lors de la mise à l’échelle de votre service, vous ne pouvez pas dépasser 36 unités de recherche. Le nombre total d’unités de recherche correspond au produit des réplicas et des partitions (réplicas x partitions = nombre total d’unités de recherche).
  * Si vous avez choisi le niveau De base, vous pouvez uniquement effectuer une mise à l’échelle avec 3 réplicas. Les services De base sont limités à une seule partition.

![](./media/search-create-service-portal/scale-service.png)

## Suivant
Après avoir approvisionné un service Azure Search, vous pouvez [définir un index Azure Search](search-what-is-an-index.md) afin de pouvoir télécharger vos données et effectuer des recherches dans vos données.

<!---HONumber=AcomDC_0316_2016-->