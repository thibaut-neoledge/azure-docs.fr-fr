<properties
	pageTitle="Mettre à l’échelle les niveaux de ressources pour interroger et indexer les charges de travail dans Azure Search | Microsoft Azure"
	description="La planification des capacités dans Azure Search est fondée sur des combinaisons de ressources informatiques de type partition et réplica, chaque ressource étant facturée en unités de recherche facturables."
	services="search"
	documentationCenter=""
	authors="HeidiSteen"
	manager="paulettm"
	editor=""
    tags="azure-portal"/>

<tags
	ms.service="search"
	ms.devlang="NA"
	ms.workload="search"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.date="05/25/2016"
	ms.author="heidist"/>

# Mettre à l’échelle les niveaux de ressources pour interroger et indexer les charges de travail dans Azure Search

Dans Azure Search, vous pouvez ajuster progressivement la capacité de ressources de calcul données en augmentant les partitions si vous avez besoin de plus de stockage et d’E/S, ou de réplicas pour des performances de requêtes et d’indexation améliorées.

L’évolutivité devient disponible lorsque vous déployez un service au [niveau de base](http://aka.ms/azuresearchbasic) ou l’un des [niveaux standard](search-limits-quotas-capacity.md).

Pour tous les niveaux facturables, la capacité est achetée par incréments d’*unités de recherche* (SU) où chaque partition et chaque réplica est considéré comme une SU.

- Le niveau de base fournit jusqu’à 3 SU par service.
- Le niveau standard fournit jusqu’à 36 SU par service.

Veillez à choisir une combinaison de partitions et de réplicas qui demeure en dessous de la limite du niveau. Si vous utilisez le portail pour monter en puissance, il applique des limites sur les combinaisons autorisées.

En règle générale, les applications de recherche requièrent plus de réplicas que de partitions. La section suivante, [Haute disponibilité](#HA), explique pourquoi.

<a id="HA"></a>
## Allocation des ressources pour une haute disponibilité

Nous vous recommandons généralement de démarrer avec une partition et un ou deux réplicas, puis de monter en puissance à mesure que les volumes de requête se créent. Pour de nombreux déploiements, une partition offre suffisamment de stockage et d'E/S (15 millions de documents par partition).

Toutefois, les charges de travail de requêtes s’exécutent principalement sur des réplicas. Vous pouvez demander des réplicas supplémentaires si vous avez besoin d’une haute disponibilité ou d’un débit plus important.

Recommandations générales pour la haute disponibilité :

- 2 réplicas pour la haute disponibilité des charges de travail en lecture seule (requêtes)
- 3 réplicas (ou plus) pour la haute disponibilité des charges de travail en lecture-écriture (requêtes et indexation)

## Récupération d'urgence

Il n'existe actuellement aucun mécanisme intégré de récupération d'urgence. L’ajout de partitions ou de réplicas ne vous permettra pas d’atteindre les objectifs de récupération d'urgence qui ont été fixés. L’approche la plus courante consiste à intégrer la redondance au niveau du service en configurant un deuxième service de recherche dans une autre région.

> [AZURE.NOTE] Rappelez-vous que l’évolutivité et les contrats de niveau de service sont des fonctionnalités des services de base et standard. Le service gratuit est proposé à un niveau de ressource fixe, avec des réplicas et des partitions partagés par plusieurs abonnés. Si vous démarrez avec le service gratuit et que vous voulez procéder à une mise à niveau, vous devrez créer un nouveau service Azure Search au niveau de base ou standard, puis recharger les index et les données vers le nouveau service. Pour obtenir des instructions sur l’approvisionnement du service, consultez [Créer un service Azure Search dans le portail](search-create-service-portal.md).

## Terminologie : partitions et réplicas

Les **partitions** fournissent une capacité de stockage et des E/S. Un seul service de recherche peut contenir 12 partitions maximum. Chaque partition est fournie avec une limite stricte de 15 millions de documents ou de 25 Go de stockage, selon ce qui se produit en premier. Si vous ajoutez des partitions, votre service de recherche peut charger plusieurs documents. Par exemple, un service disposant d’une seule partition, qui stocke initialement 25 Go de données, peut stocker 50 Go si une deuxième partition est ajoutée au service.

Les **réplicas** sont des copies du moteur de recherche. Un seul service de recherche peut contenir 12 réplicas maximum. Vous avez besoin d’au moins 2 réplicas de disponibilité en lecture (requête) et d’au moins 3 réplicas de disponibilité en lecture-écriture (requête, indexation).

## Augmenter les performances des requêtes avec des réplicas

La latence des requêtes vous permet de découvrir si des réplicas supplémentaires doivent être ajoutés. En règle générale, le premier pas vers l’amélioration des performances des requêtes consiste à ajouter davantage de réplicas.

Une copie de chaque index s'exécute sur chaque réplica. Lorsque vous ajoutez des réplicas, les copies supplémentaires de l'index sont mis en ligne pour prendre en charge les charges de travail supérieures de requête et équilibrer la charge des requêtes sur plusieurs réplicas. Si vous disposez de plusieurs index (par ex. 6) et de 3 réplicas, chaque réplica possédera une copie des 6 index.

Notez que nous ne fournissons aucune estimation sur les requêtes par seconde (RPS) : les performances des requêtes dépendent de la complexité de la requête et des charges de travail concurrentes. En moyenne, un réplica peut traiter environ 15 requêtes par seconde, mais le débit sera quelque peu supérieur ou inférieur en fonction de la complexité de la requête (les requêtes à facettes sont plus complexes) et de la latence du réseau. Bien que l'ajout de réplicas entraîne une amélioration de l’évolutivité et des performances, le résultat final n'est pas strictement linéaire : en ajoutant 3 réplicas, le débit ne sera pas forcément multiplié par trois.

Pour en savoir plus sur les requêtes par seconde, notamment les approches d’estimation de RPS pour vos charges de travail, consultez [Gérer votre service de recherche](search-manage.md).

## Améliorer les performances d’indexation avec des partitions

Les applications de recherche nécessitant une actualisation des données en temps réel ou presque ont proportionnellement besoin de plus de partitions que de réplicas. L’ajout de partitions répartit les opérations de lecture-écriture sur un plus grand nombre de ressources de calcul. Il vous offre également davantage d’espace disque pour stocker des documents et des index supplémentaires.

Plus les index sont grands, plus ils sont longs à interroger. Par conséquent, peut-être constaterez-vous que chaque augmentation incrémentielle des partitions nécessite une augmentation plus faible mais proportionnelle des réplicas. Comme indiqué précédemment, la complexité et le volume de vos requêtes auront une incidence sur la vitesse d’exécution des requêtes.

## Niveau de base : combinaisons de partitions et de réplicas

Un service de base peut avoir 1 partition et jusqu’à 3 réplicas, pour une limite maximale de 3 unités de recherche.

<a id="chart"></a>
## Niveau standard : combinaisons de partitions et de réplicas

Ce tableau indique les unités de recherche requises pour prendre en charge les combinaisons de réplicas et de partitions soumises à la limite de 36 unités de recherche (SU).

- |- |- |- |- |- |- |
---|----|---|---|---|---|---|
**12 réplicas**|12 unités de recherche|24 unités de recherche|36 unités de recherche|N/A|N/A|N/A|
**6 réplicas**|6 unités de recherche|12 unités de recherche|18 unités de recherche|24 unités de recherche|36 unités de recherche|N/A|
**5 réplicas**|5 unités de recherche|10 unités de recherche|15 unités de recherche|20 unités de recherche|30 unités de recherche|N/A|
**4 réplicas**|4 unités de recherche|8 SU <|12 unités de recherche|16 unités de recherche|24 unités de recherche|N/|
**3 réplicas**|3 unités de recherche|6 unités de recherche|9 unités de recherche|12 unités de recherche|18 unités de recherche|36 unités de recherche|
**2 réplicas**|2 unités de recherche|4 unités de recherche|6 unités de recherche|8 unités de recherche|12 unités de recherche|24 unités de recherche|
**1 réplica**|1 unité de recherche|2 unités de recherche|3 unités de recherche|4 unités de recherche|6 unités de recherche|12 unités de recherche|
N/A|**1 partition**|**2 partitions**|**3 partitions** <|**4 partitions**|**6 partitions**|**12 partitions**|


Les unités de recherche, leur tarification et leur capacité sont détaillées sur le site Web Azure. Pour plus d'informations, consultez la rubrique [Tarification](https://azure.microsoft.com/pricing/details/search/).

> [AZURE.NOTE] Le nombre de réplicas et de partitions doit être partagé en 12, de manière égale (plus précisément, 1, 2, 3, 4, 6, 12). Azure Search divise au préalable chaque index en 12 partitions pour que celles-ci puissent être réparties sur plusieurs partitions. Par exemple, si votre service comporte trois partitions et que vous créez un nouvel index, chaque partition contiendra 4 partitions de l'index. Le partitionnement d’un index réalisé par Azure Search est un détail d'implémentation, susceptible d’être modifié dans les futures versions. Le nombre de partitions (12 à l’heure actuelle) peut être, à l’avenir, totalement différent.

## Calculer les unités de recherche pour des combinaisons de ressources spécifiques : R X P = SU

La formule permettant de calculer le nombre d’unités de recherche dont vous avez besoin correspond aux réplicas multipliés par les partitions. Par exemple, 3 réplicas multipliés par 3 partitions sont facturés comme 9 unités de recherche.

Au départ, les deux niveaux disposent d’un réplica et d’une partition, considérés comme une unité de recherche (SU). Il s’agit de la seule instance où un réplica et une partition sont comptabilisés comme une seule unité de recherche. Chaque ressource supplémentaire, qu’il s’agisse d’un réplica ou d’une partition, est comptabilisée comme une unité de recherche à part entière.

Le coût par unité de recherche est déterminé par le niveau. Le coût par unité de recherche pour le niveau de base est inférieur à celui du niveau standard. Consultez la [Tarification](https://azure.microsoft.com/pricing/details/search/) pour connaître les coûts pour chaque niveau.

<!---HONumber=AcomDC_0601_2016-->