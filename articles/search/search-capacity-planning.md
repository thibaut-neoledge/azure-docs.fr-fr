<properties
	pageTitle="Planification de la capacité dans Azure Search | Microsoft Azure | Service de recherche cloud hébergé"
	description="La capacité dans Azure Search est construite à partir de partitions et de réplicas, où chaque élément correspond à une unité de recherche facturable."
	services="search"
	documentationCenter=""
	authors="HeidiSteen"
	manager="mblythe"
	editor=""
    tags="azure-portal"/>

<tags
	ms.service="search"
	ms.devlang="NA"
	ms.workload="search"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.date="02/28/2016"
	ms.author="heidist"/>

# Planification de la capacité dans Azure Search

Dans Azure Search, vous pouvez progressivement ajuster la capacité de certaines ressources de calcul spécifiques. Vous pouvez augmenter les partitions si vous avez besoin de davantage de stockage et d’E/S ou les réplicas pour des performances de demande et d’indexation améliorées.

L’évolutivité devient disponible lorsque vous déployez un service au [niveau de base](http://aka.ms/azuresearchbasic) ou l’un des [niveaux standard](search-limits-quotas-capacity.md).

Pour les deux niveaux, la capacité est achetée par incréments d’*unités de recherche* (SU) où chaque partition et chaque réplica sont chacun considérés comme une SU.

- Le niveau de base fournit jusqu’à 3 SU par service.
- Le niveau standard fournit jusqu’à 36 SU par service.

Vous devez choisir une combinaison de partitions et de réplicas qui demeure en dessous de la limite du niveau. Par exemple, vous ne pouvez pas mettre à l’échelle votre service standard jusqu'à 12 partitions et 6 réplicas, car il vous faudrait 72 unités de recherche (12 x 6), et cela dépasserait les 36 unités de recherche autorisées par service.

En règle générale, la plupart des applications de recherche requièrent généralement plus de réplicas que de partitions. Consultez la section [Haute disponibilité](#HA) pour plus d’informations.

**Calculer les unités de recherche pour des combinaisons de ressources spécifiques : R X P = SU**

La formule permettant de calculer le nombre d’unités de recherche dont vous avez besoin correspond aux réplicas multipliés par les partitions. Par exemple, 3 réplicas multipliés par 3 partitions sont facturés comme 9 unités de recherche.

Au départ, les deux niveaux disposent d’un réplica et d’une partition, considérés comme une unité de recherche (SU). Il s’agit de la seule instance où un réplica et une partition sont comptabilisés comme une seule unité de recherche. Chaque ressource supplémentaire, qu’il s’agisse d’un réplica ou d’une partition, est comptabilisée comme une unité de recherche à part entière.

Le coût par unité de recherche est déterminé par le niveau. Le coût par unité de recherche pour le niveau de base est inférieur à celui du niveau standard. Consultez la [Tarification](https://azure.microsoft.com/pricing/details/search/) pour connaître les coûts pour chaque niveau.

## Niveau de base : combinaisons de partitions et de réplicas

Un service de base peut avoir 1 partition et jusqu’à 3 réplicas, pour une limite maximale de 3 unités de recherche.

<table cellspacing="0" border="1">
<tr><td><b>3&#160;réplicas</b></td><td>3&#160;unités de recherche</td></tr>
<tr><td><b>2&#160;réplicas</b></td><td>2&#160;unités de recherche</td></tr>
<tr><td><b>1&#160;réplica</b></td><td>1&#160;unité de recherche</td></tr>
<tr><td></td><td><b>1&#160;partition</b></td></tr>
</table>

<a id="chart"></a>
## Niveau standard : combinaisons de partitions et de réplicas

Ce tableau indique les unités de recherche requises pour prendre en charge les combinaisons de réplicas et de partitions soumises à la limite de 36 unités de recherche (SU).

<table cellspacing="0" border="1">
<tr><td><b>12&#160;réplicas</b></td><td>12&#160;unités de recherche</td><td>24&#160;unités de recherche</td><td>36&#160;unités de recherche</td><td>N/A</td><td>N/A</td><td>N/A</td></tr>
<tr><td><b>6&#160;réplicas</b></td><td>6&#160;unités de recherche</td><td>12&#160;unités de recherche</td><td>18&#160;unités de recherche</td><td>24&#160;unités de recherche</td><td>36&#160;unités de recherche</td><td>N/A</td></tr>
<tr><td><b>5&#160;réplicas</b></td><td>5&#160;unités de recherche</td><td>10&#160;unités de recherche</td><td>15&#160;unités de recherche</td><td>20&#160;unités de recherche</td><td>30&#160;unités de recherche</td><td>N/A</td></tr>
<tr><td><b>4&#160;réplicas</b></td><td>4&#160;unités de recherche</td><td>8&#160;unités de recherche</td><td>12&#160;unités de recherche</td><td>16&#160;unités de recherche</td><td>24&#160;unités de recherche</td><td>N/A </td></tr>
<tr><td><b>3&#160;réplicas</b></td><td>3&#160;unités de recherche</td><td>6&#160;unités de recherche</td><td>9&#160;unités de recherche</td><td>12&#160;unités de recherche</td><td>18&#160;unités de recherche</td><td>36&#160;unités de recherche</td></tr>
<tr><td><b>2&#160;réplicas</b></td><td>2&#160;unités de recherche</td><td>4&#160;unités de recherche</td><td>6&#160;unités de recherche</td><td>8&#160;unités de recherche</td><td>12&#160;unités de recherche</td><td>24&#160;unités de recherche</td></tr>
<tr><td><b>1&#160;réplica</b></td><td>1&#160;unité de recherche</td><td>2&#160;unités de recherche</td><td>3&#160;unités de recherche</td><td>4&#160;unités de recherche</td><td>6&#160;unités de recherche</td><td>12&#160;unités de recherche</td></tr>
<tr><td>N/A</td><td><b>1&#160;partition</b></td><td><b>2&#160;partitions</b></td><td><b>3&#160;partitions</b></td><td><b>4&#160;partitions</b></td><td><b>6&#160;partitions</b></td><td><b>12&#160;partitions</b></td></tr>
</table>

Les unités de recherche, leur tarification et leur capacité sont détaillées sur le site Web Azure. Pour plus d'informations, consultez la rubrique [Tarification](https://azure.microsoft.com/pricing/details/search/).

> [AZURE.NOTE] Le nombre de réplicas et de partitions doit être partagé en 12, de manière égale (plus précisément, 1, 2, 3, 4, 6, 12). Azure Search divise au préalable chaque index en 12 partitions pour que celles-ci puissent être réparties sur plusieurs partitions. Par exemple, si votre service comporte trois partitions et que vous créez un nouvel index, chaque partition contiendra 4 partitions de l'index. Le partitionnement d’un index réalisé par Azure Search est un détail d'implémentation, susceptible d’être modifié dans les futures versions. Le nombre de partitions (12 à l’heure actuelle) peut être, à l’avenir, totalement différent.

<a id="HA"></a>
## Choisissez une combinaison de partitions et de réplicas pour la haute disponibilité

Nous vous recommandons généralement de démarrer avec une partition et un ou deux réplicas, puis de monter en puissance à mesure que les volumes de requête se créent. Pour de nombreux déploiements, une partition offre suffisamment de stockage et d'E/S (15 millions de documents par partition).

Les charges de travail reposent toutefois sur les réplicas. Vous pouvez demander des réplicas supplémentaires si vous avez besoin d’une haute disponibilité ou d’un débit plus important.

Recommandations générales pour la haute disponibilité :

- 2 réplicas pour la haute disponibilité des charges de travail en lecture seule (requêtes)
- 3 réplicas (ou plus) pour la haute disponibilité des charges de travail en lecture-écriture (requêtes et indexation)

Il n'existe actuellement aucun mécanisme intégré de récupération d'urgence. L’ajout de partitions ou de réplicas ne vous permettra pas d’atteindre les objectifs de récupération d'urgence qui ont été fixés. Au lieu de cela, vous pouvez envisager d'ajouter de la redondance au niveau du service. Pour plus d’informations sur les solutions de contournement, consultez [ce billet de forum](https://social.msdn.microsoft.com/Forums/ee108a26-00c5-49f6-b1ff-64c66c8b828a/dr-and-high-availability-for-azure-search?forum=azuresearch).

> [AZURE.NOTE] Rappelez-vous que l'évolutivité et les contrats de niveau de service sont des fonctionnalités du service standard. Le service gratuit est proposé à un niveau de ressource fixe, avec des réplicas et des partitions partagés par plusieurs abonnés. Si vous démarrez avec le service gratuit et que vous voulez procéder à une mise à niveau, vous devrez créer un nouveau service de recherche Azure au niveau standard, puis recharger ensuite les index et les données vers le nouveau service. Pour obtenir des instructions sur l’approvisionnement du service, consultez [Créer un service Azure Search dans le portail](search-create-service-portal.md).

## À propos des partitions et des réplicas

Les **partitions** fournissent une capacité de stockage et des E/S. Un seul service de recherche peut contenir 12 partitions maximum. Chaque partition est fournie avec une limite stricte de 15 millions de documents ou de 25 Go de stockage, selon ce qui se produit en premier. Si vous ajoutez des partitions, votre service de recherche peut charger plusieurs documents. Par exemple, un service disposant d’une seule partition, qui stocke initialement 25 Go de données, peut stocker 50 Go si une deuxième partition est ajoutée au service.

Les **réplicas** sont des copies du moteur de recherche. Un seul service de recherche peut contenir 12 réplicas maximum. Vous avez besoin d’au moins 2 réplicas de disponibilité en lecture (requête) et d’au moins 3 réplicas de disponibilité en lecture-écriture (requête, indexation).

Une copie de chaque index s'exécute sur chaque réplica. Lorsque vous ajoutez des réplicas, les copies supplémentaires de l'index sont mis en ligne pour prendre en charge les charges de travail supérieures de requête et équilibrer la charge des requêtes sur plusieurs réplicas. Si vous disposez de plusieurs index (par ex. 6) et de 3 réplicas, chaque réplica possédera une copie des 6 index.

Notez que nous ne fournissons aucune estimation sur les requêtes par seconde, car l'exécution de requête peut varier considérablement selon la complexité de la requête et les charges de travail concurrentes. En moyenne, un réplica peut traiter environ 15 requêtes par seconde, mais le débit sera quelque peu supérieur ou inférieur en fonction de la complexité de la requête (les requêtes à facettes sont plus complexes) et de la latence du réseau. Bien que l'ajout de réplicas entraîne une amélioration de l’évolutivité et des performances, le résultat final n'est pas strictement linéaire : en ajoutant 3 réplicas, le débit ne sera pas forcément multiplié par trois. La latence des requêtes vous permet de découvrir si des réplicas supplémentaires doivent être ajoutés. Pour en savoir plus sur les requêtes par seconde, y compris les approches d’estimation de RPS pour vos charges de travail, consultez [Gérer votre service de recherche](search-manage.md).

<!---HONumber=AcomDC_0518_2016-->