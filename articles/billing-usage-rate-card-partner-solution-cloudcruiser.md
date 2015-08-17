<properties
   pageTitle="Intégration des API Microsoft Azure Billing par Cloud Cruiser"
   description="Décrit la façon dont Cloud Cruiser, un partenaire de facturation de Microsoft Azure, a intégré les API Azure Billing à son produit. Ces informations sont particulièrement utiles pour les clients Azure et Cloud Cruiser qui souhaitent utiliser ou essayer Cloud Cruiser pour Microsoft Azure Pack."
   services="billing"
   documentationCenter=""
   authors="BryanLa"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="billing"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="billing"
   ms.date="06/17/2015"
   ms.author="mobandyo;sirishap;bryanla"/>

# Intégration des API Microsoft Azure Billing par Cloud Cruiser 

Cet article décrit la façon dont les informations collectées à partir des nouvelles API Microsoft Azure Billing sont utilisables dans Cloud Cruiser à des fins de simulation et d’analyse des coûts de flux de travail.

## API Azure RateCard
L’API RateCard fournit des informations sur les tarifs Azure. Après vous être authentifié avec les informations d’identification appropriées, vous pouvez interroger l’API pour collecter les métadonnées relatives aux services disponibles sur Azure, ainsi que les tarifs associés à votre ID d’offre.

Voici un exemple de réponse de l’API affichant les prix pour l’instance A0 (Windows) :

    {       
		"MeterId": "0e59ad56-03e5-4c3d-90d4-6670874d7e29",       
		"MeterName": "Compute Hours",       
		"MeterCategory": "Virtual Machines",       
		"MeterSubCategory": "A0 VM (Windows)",       
		"Unit": "Hours",       
		"MeterRates": 
		{         
			"0": 0.029       
		},       
		"EffectiveDate": "2014-08-01T00:00:00Z",       
		"IncludedQuantity": 0.0     
	}, 

## Interaction entre Cloud Cruiser et l’API Azure RateCard
Cloud Cruiser peut tirer parti des informations de l’API RateCard de différentes façons. Dans cet article, nous décrivons comment utiliser ces informations pour effectuer la simulation et l’analyse des coûts de la charge de travail IaaS (infrastructure as a service).

Pour illustrer ce cas d’utilisation, imaginons une charge de travail de plusieurs instances en cours d’exécution sur Microsoft Azure Pack (WAP). L’objectif est de simuler cette même charge de travail sur Azure et d’estimer les coûts liés à cette migration. La création de cette simulation nécessite l’exécution de deux tâches principales :

1. **Importation et traitement des informations de service collectées à partir de l’API RateCard** : cette tâche est également effectuée sur les classeurs, dans lesquels les informations extraites de l’API RateCard sont transformées et publiées sous la forme d’une nouvelle formule tarifaire. Cette nouvelle formule sera appliquée aux simulations pour estimer les prix Azure.

2. **Normalisation des services WAP et des services Azure pour IaaS** : par défaut, les services WAP reposent sur des ressources individuelles (unité centrale, taille de la mémoire, taille de disque, etc.), alors que les services Azure sont basés sur la taille d’instance (A0, A1, A2, etc.). Cette première tâche peut être effectuée par le moteur ETL (extraction, transformation et chargement) de Cloud Cruiser, désigné sous le terme de classeurs, dans lesquels ces ressources peuvent être regroupées dans des tailles d’instance, analogues aux services d’instance d’Azure.

## Importer les données de l’API RateCard

Les classeurs Cloud Cruiser automatisent la collecte et le traitement des informations de l’API RateCard. Les classeurs ETL (extraction, transformation et chargement) vous permettent de configurer la collecte, la transformation et la publication de données dans la base de données Cloud Cruiser.

Chaque classeur peut comporter une ou plusieurs collections. Cela vous permet de mettre en corrélation les informations de différentes sources afin de compléter ou d’enrichir les données d’utilisation. Les deux captures d’écran ci-après illustrent la création d’une *collection* dans un classeur existant et l’importation d’informations de l’API RateCard dans cette *collection* :

![Figure 1 : création d’une collection][1]

![Figure 2 : importation des données de la nouvelle collection][2]

Après avoir importé les données dans le classeur, nous pouvons créer plusieurs étapes et processus de transformation afin de modifier et de modéliser les données. Dans notre exemple, étant donné que nous sommes uniquement intéressés par IaaS, nous pouvons utiliser les étapes de transformation pour supprimer les lignes, ou enregistrements, inutiles liés aux services autres qu’IaaS.

La capture d’écran ci-après illustre les étapes de transformation permettant de traiter les données collectées à partir de l’API RateCard :

![Figure 3 : étapes de transformation pour le traitement des données collectées à partir de l’API RateCard][3]

## Définition de nouveaux services et formules tarifaires

Il existe différentes façons de définir des services sur Cloud Cruiser. L’une des possibilités consiste à importer les services à partir des données d’utilisation. Cette méthode est couramment utilisée lorsque l’on travaille avec des clouds publics, dans lesquels les services sont déjà définis par le fournisseur.

Une formule tarifaire est un ensemble de tarifs ou de prix qui peuvent être appliqués à différents services, en fonction de dates d’effet, ou à un groupe de clients, entre autres options. Il est également possible d’utiliser les formules tarifaires sur Cloud Cruiser pour créer des scénarios de simulation afin de comprendre la façon dont les modifications apportées aux services peuvent affecter le coût total d’une charge de travail.

Dans cet exemple, nous allons utiliser les informations de service de l’API RateCard pour définir de nouveaux services dans Cloud Cruiser. De la même façon, nous pouvons utiliser les tarifs associés aux services pour créer une autre formule tarifaire sur Cloud Cruiser.

À la fin du processus de transformation, il est possible de créer une autre étape et de publier les données de l’API RateCard sous la forme de nouveaux services et tarifs.

![Figure 4 : publication des données de l’API RateCard sous la forme de nouveaux services et tarifs][4]

## Vérifier les services et tarifs Azure

Après avoir publié les services et les tarifs, vous pouvez vérifier la liste des services importés dans l’onglet *Services* de Cloud Cruiser :

![Figure 5 : vérification des nouveaux services][5]

Dans l’onglet *Rate Plans*, vous pouvez vérifier la nouvelle formule tarifaire appelée « AzureSimulation » en fonction des tarifs importés depuis l’API RateCard.

![Figure 6 : vérification de la nouvelle formule tarifaire et des tarifs associés][6]

## Normaliser les services WAP et Azure

Par défaut, WAP fournit des informations d’utilisation reposant sur l’utilisation des ressources de calcul, de mémoire et de réseau. Cloud Cruiser vous permet de définir des services directement basés sur l’attribution ou l’utilisation facturée à l’usage de ces ressources. Par exemple, vous pouvez définir un tarif de base pour chaque heure d’utilisation de l’unité centrale, ou facturer les Go de mémoire alloués à une instance.

Dans notre exemple, pour comparer les coûts entre WAP et Azure, nous devons agréger l’utilisation des ressources sur WAP sous forme d’offres groupées, qui peuvent ensuite être mappées sur les services Azure. Cette transformation peut être facilement implémentée dans les classeurs :

![Figure 7 : transformation des données WAP pour normaliser les services][7]

La dernière étape au niveau du classeur consiste à publier les données dans la base de données Cloud Cruiser. Au cours de cette étape, les données d’utilisation sont désormais regroupées sous forme de services (mappés sur les services Azure) et associées aux tarifs par défaut pour créer les frais.

Après avoir terminé le classeur, vous pouvez automatiser le traitement des données en ajoutant une nouvelle tâche sur le planificateur et en spécifiant la fréquence et l’heure d’exécution du classeur.

![Figure 8 : planification du classeur][8]

## Créer des rapports concernant la simulation et l’analyse des coûts de la charge de travail

Une fois les données d’utilisation collectées et les frais chargés dans la base de données Cloud Cruiser, nous pouvons exploiter le module Cloud Cruiser Insights, un outil avancé de création de rapports d’analyse, pour créer la simulation des coûts de charge de travail dont nous avons besoin.

Afin d’illustrer ce scénario, nous avons créé le rapport suivant :

![Comparaison des coûts][9]

Le graphique supérieur affiche une comparaison des coûts répartis par service et compare le prix de l’exécution de la charge de travail pour chaque service entre WAP (en bleu foncé) et Azure (en bleu clair).

Le graphique inférieur présente les mêmes données, mais réparties par département, en indiquant pour chaque département les coûts d’exécution de la charge de travail sur WAP et Azure, ainsi que la différence entre les deux dans la barre « Savings » (en vert).

## Étapes suivantes

+ Pour découvrir la procédure détaillée de création de classeurs et de rapports Cloud Cruiser, reportez-vous à la [documentation](http://docs.cloudcruiser.com/) en ligne de Cloud Cruiser (ID de connexion valide requis). Pour obtenir plus d’informations sur Cloud Cruiser, contactez [info@cloudcruiser.com](mailto:info@cloudcruiser.com).
+ Pour découvrir une présentation des API Azure Resource Usage et RateCard, voir [Obtenir une vue d’ensemble de votre consommation des ressources Microsoft Azure](billing-usage-rate-card-overview.md). 
+ Pour plus d’informations sur ces deux API, qui font partie intégrante de l’ensemble d’API fourni par Azure Resource Manager, consultez la [Référence des API REST Azure Billing](https://msdn.microsoft.com/library/azure/1ea5b323-54bb-423d-916f-190de96c6a3c).
+ Si vous souhaitez vous plonger directement dans l’exemple de code, consultez nos [exemples de code d’API Microsoft Azure Billing sur Github](https://github.com/Azure/BillingCodeSamples) (en anglais).

## En savoir plus
+ Pour plus d’informations sur Azure Resource Manager, voir l’article [Présentation d’Azure Resource Manager](resource-group-overview.md).

<!--Image references-->
[1]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Create-New-Workbook-Collection.png "Figure 1 : création d’une collection"
[2]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Import-Data-From-RateCard.png "Figure 2 : importation des données de la nouvelle collection"
[3]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Transformation-Steps-Process-RateCard-Data.png "Figure 3 : étapes de transformation pour le traitement des données collectées à partir de l’API RateCard"
[4]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Publish-RateCard-Data-New-Services-Rates.png "Figure 4 : publication des données de l’API RateCard sous la forme de nouveaux services et tarifs"
[5]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Verify-Azure-Services-And-Pricing1.png "Figure 5 : vérification des nouveaux services"
[6]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Verify-Azure-Services-And-Pricing2.png "Figure 6 : vérification de la nouvelle formule tarifaire et des tarifs associés"
[7]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Transforming-WAP-Normalize-Services.png "Figure 7 : transformation des données WAP pour normaliser les services"
[8]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Workbook-Scheduling.png "Figure 8 : planification du classeur"
[9]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Workload-Cost-Simulation-Report.png "Figure 9 : exemple de rapport pour le scénario de comparaison des coûts de la charge de travail"

<!---HONumber=August15_HO6-->