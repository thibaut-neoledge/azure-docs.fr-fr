<properties
	pageTitle="Mettre à l’échelle les niveaux de ressources pour interroger et indexer les charges de travail dans Azure Search | Microsoft Azure"
	description="La planification des capacités dans Azure Search est fondée sur des combinaisons de ressources informatiques de type partition et réplica, chaque ressource étant facturée en unités de recherche facturables."
	services="search"
	documentationCenter=""
	authors="HeidiSteen"
	manager="jhubbard"
	editor=""
    tags="azure-portal"/>

<tags
	ms.service="search"
	ms.devlang="NA"
	ms.workload="search"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.date="08/29/2016"
	ms.author="heidist"/>

# Mettre à l’échelle les niveaux de ressources pour interroger et indexer les charges de travail dans Azure Search

Après avoir [choisi une référence (SKU)](search-sku-tier.md) et [configuré un service de recherche](search-create-service-portal.md), l’étape suivante consiste éventuellement à configurer les ressources du service.

Dans Azure Search, un service se voit initialement allouer un niveau minimal de ressources consistant en une partition et un réplica. Pour les niveaux qui le prennent en charge, vous pouvez ajuster progressivement les ressources de calcul en augmentant les partitions si vous avez besoin de plus de stockage et d’E/S, ou de réplicas pour des volumes de requêtes plus importants ou des performances améliorées. Un seul service doit avoir suffisamment de ressources pour gérer toutes les charges de travail (indexation et requêtes). Vous ne pouvez pas subdiviser les charges de travail entre plusieurs services.

Les paramètres de mise à l’échelle sont disponibles lorsque vous déployez un service facturable au [niveau De base](http://aka.ms/azuresearchbasic) ou à l’un des [niveaux Standard](search-limits-quotas-capacity.md). Pour les références (SKU) facturables, la capacité est achetée par incréments d’*unités de recherche* (SU) où chaque partition et chaque réplica est considéré comme une SU. Rester en dessous des limites maximales permet d’utiliser moins de SU, avec une facture proportionnellement plus faible. La facturation est en vigueur tant que le service est configuré. Si vous n’utilisez pas temporairement un service, la seule façon d’éviter la facturation consiste à supprimer ce service, puis à le recréer ultérieurement lorsque vous en avez besoin.

Pour augmenter ou modifier l’allocation des réplicas et des partitions, nous vous recommandons l’aide du portail. Le portail applique des limites sur les combinaisons autorisées inférieures aux limites maximales :

1. Connectez-vous au [portail Azure](https://portal.azure.com/), puis sélectionnez le service de recherche.
2. Dans Paramètres, ouvrez le volet Mettre à l’échelle et utilisez les curseurs pour augmenter ou diminuer le nombre de partitions et de réplicas.

En règle générale, les applications de recherche ont besoin de plus de réplicas que de partitions, en particulier lorsque les opérations de service favorisent les charges de travail de requête. La section [Haute disponibilité](#HA) explique pourquoi.

> [AZURE.NOTE] Une fois qu’un service est configuré, il ne peut pas être mis à niveau sur place vers une référence (SKU) supérieure. Vous devez créer un service de recherche au nouveau niveau et recharger vos index. Pour obtenir des instructions sur l’approvisionnement du service, voir [Créer un service Azure Search dans le portail](search-create-service-portal.md).

## Terminologie : partitions et réplicas

Les partitions et les réplicas sont les principales ressources qui sous-tendent un service de recherche.

Les **partitions** fournissent un stockage des index et des E/S pour les opérations de lecture-écriture (par exemple, lors de la reconstruction ou de l’actualisation d’un index).

Les **réplicas** sont des instances du service de recherche, principalement utilisées pour équilibrer la charge des opérations de requête. Chaque réplica héberge toujours une seule copie d’un index. Si vous avez 12 réplicas, vous aurez 12 copies de chaque index chargées sur le service.

> [AZURE.NOTE] Il n’existe aucun moyen de manipuler ou de gérer directement les index qui s’exécutent sur un réplica. Une copie de chaque index sur chaque réplica fait partie de l’architecture de service.

<a id="HA"></a>
## Haute disponibilité

Nous vous recommandons généralement de démarrer avec une partition et un ou deux réplicas, puis de monter en puissance à mesure que les volumes de requête se créent, jusqu’à atteindre la limite maximale de réplicas et de partitions prise en charge par la référence (SKU). Pour de nombreux services au niveau De base ou S1, une partition offre suffisamment de stockage et d’E/S (15 millions de documents par partition).

Les charges de travail de requêtes s’exécutent principalement sur des réplicas. Vous pouvez demander des réplicas supplémentaires si vous avez besoin d’une haute disponibilité ou d’un débit plus important.

Recommandations générales pour la haute disponibilité :

- 2 réplicas pour la haute disponibilité des charges de travail en lecture seule (requêtes)
- 3 réplicas minimum pour la haute disponibilité des charges de travail en lecture-écriture (les requêtes et l’indexation en tant que documents individuels sont ajoutées, mises à jour ou supprimées)

Les contrats de niveau de service (SLA) pour Azure Search sont ciblés au moment des opérations de requête et des mises à jour d’index qui se composent d’ajout, de mise à jour ou de suppression de documents.

**Disponibilité des index lors d’une reconstruction**

La haute disponibilité pour Azure Search se rapporte aux requêtes et aux mises à jour d’index qui n’impliquent pas la reconstruction d’un index. Si l’index nécessite une reconstruction, par exemple si vous ajoutez ou supprimez un champ, modifiez un type de données ou renommez un champ, vous devez reconstruire l’index de la manière suivante : supprimer l’index, recréer l’index, puis recharger les données.

Pour maintenir la disponibilité de l’index pendant une reconstruction, vous devez disposer d’une deuxième copie de l’index déjà en production sur le même service (avec un nom différent) ou d’un index de même nom sur un autre service, et fournir la redirection ou la logique de basculement dans votre code.

## Récupération d'urgence

Il n'existe actuellement aucun mécanisme intégré de récupération d'urgence. L’ajout de partitions ou de réplicas ne vous permettra pas d’atteindre les objectifs de récupération d'urgence qui ont été fixés. L’approche la plus courante consiste à intégrer la redondance au niveau du service en configurant un deuxième service de recherche dans une autre région. Comme avec la disponibilité pendant une reconstruction d’index, la redirection ou la logique de basculement doit provenir de votre code.

## Augmenter les performances des requêtes avec des réplicas

La latence des requêtes vous permet de découvrir si des réplicas supplémentaires doivent être ajoutés. En règle générale, le premier pas vers l’amélioration des performances des requêtes consiste à ajouter davantage de cette ressource. Lorsque vous ajoutez des réplicas, les copies supplémentaires de l’index sont mises en ligne pour prendre en charge les charges de travail supérieures de requête et équilibrer la charge des requêtes sur plusieurs réplicas.

Nous ne pouvons fournir aucune estimation sur les requêtes par seconde (RPS) : les performances des requêtes dépendent de la complexité de la requête et des charges de travail concurrentes. En moyenne, un réplica au niveau de référence (SKU) De base ou S1 peut traiter environ 15 requêtes par seconde, mais le débit sera quelque peu supérieur ou inférieur en fonction de la complexité de la requête (les requêtes à facettes sont plus complexes) et de la latence du réseau. Bien que l'ajout de réplicas entraîne une amélioration de l’évolutivité et des performances, le résultat final n'est pas strictement linéaire : en ajoutant 3 réplicas, le débit ne sera pas forcément multiplié par trois.

Pour en savoir plus sur les requêtes par seconde, notamment les approches d’estimation de RPS pour vos charges de travail, voir [Gérer votre service de recherche](search-manage.md).

## Améliorer les performances d’indexation avec des partitions

Les applications de recherche nécessitant une actualisation des données en temps réel ou presque ont proportionnellement besoin de plus de partitions que de réplicas. L’ajout de partitions répartit les opérations de lecture-écriture sur un plus grand nombre de ressources de calcul. Il vous offre également davantage d’espace disque pour stocker des documents et des index supplémentaires.

Plus les index sont grands, plus ils sont longs à interroger. Par conséquent, peut-être constaterez-vous que chaque augmentation incrémentielle des partitions nécessite une augmentation plus faible mais proportionnelle des réplicas. Comme indiqué précédemment, la complexité et le volume de vos requêtes auront une incidence sur la vitesse d’exécution des requêtes.

## Niveau de base : combinaisons de partitions et de réplicas

Un service De base peut avoir exactement 1 partition et jusqu’à 3 réplicas, pour une limite maximale de 3 unités de recherche. Les seules ressources ajustables sont les réplicas. Comme indiqué précédemment, vous devez disposer d’au moins 2 réplicas pour la haute disponibilité sur des requêtes.

<a id="chart"></a>
## Niveaux Standard : combinaisons de partitions et de réplicas

Cette table indique les unités de recherche requises pour prendre en charge les combinaisons de réplicas et de partitions soumises à la limite de 36 unités de recherche (SU) (exclut les niveaux De base et S3 HD).

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

> [AZURE.NOTE] Le nombre de réplicas et de partitions doit être partagé en 12, de manière égale (plus précisément, 1, 2, 3, 4, 6, 12). Azure Search divise au préalable chaque index en 12 partitions pour que celles-ci puissent être réparties équitablement sur plusieurs partitions. Par exemple, si votre service comporte trois partitions et que vous créez un nouvel index, chaque partition contiendra 4 partitions de l'index. Le partitionnement d’un index réalisé par Azure Search est un détail d'implémentation, susceptible d’être modifié dans les futures versions. Le nombre de partitions (12 à l’heure actuelle) peut être, à l’avenir, totalement différent.

## Niveau S3 Haute densité : combinaisons de partitions et de réplicas

Un service S3 HD peut avoir 1 partition et jusqu’à 12 réplicas, pour une limite maximale de 12 unités de recherche. Les seules ressources ajustables sont les réplicas.

## Calculer les unités de recherche pour des combinaisons de ressources spécifiques : R X P = SU

La formule permettant de calculer le nombre d’unités de recherche dont vous avez besoin correspond aux réplicas multipliés par les partitions. Par exemple, 3 réplicas multipliés par 3 partitions sont facturés comme 9 unités de recherche.

Au départ, les deux niveaux disposent d’un réplica et d’une partition, considérés comme une unité de recherche (SU). Il s’agit de la seule instance où un réplica et une partition sont comptabilisés comme une seule unité de recherche. Chaque ressource supplémentaire, qu’il s’agisse d’un réplica ou d’une partition, est comptabilisée comme une unité de recherche à part entière.

Le coût par unité de recherche est déterminé par le niveau. Le coût par unité de recherche pour le niveau de base est inférieur à celui du niveau standard. Consultez la [Tarification](https://azure.microsoft.com/pricing/details/search/) pour connaître les coûts pour chaque niveau.

<!---HONumber=AcomDC_0914_2016-->