<properties
	pageTitle="Limites de service d’Azure Search | Microsoft Azure | Service de recherche cloud hébergé"
	description="Limites de service permettant de planifier la capacité et les limites maximales des requêtes et réponses pour Azure Search, un service de recherche cloud hébergé."
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
	ms.date="02/15/2016"
	ms.author="heidist"/>

# Limites de service d’Azure Search

Les limites maximales de stockage, de charges de travail et de quantités d'index, de documents et d’autres objets dépendent de votre abonnement à Azure Search. Vous pouvez utiliser le service gratuit, présentant des limites d’objets et de charges de travail beaucoup moins importantes, à des fins d’évaluation et de test de validation technique pour que les ressources puissent être réparties de manière plus équitable.

La version standard s’applique sur des ordinateurs dédiés qui sont utilisés uniquement par votre service. L’utilisation unique d'un service dédié vous permet de monter ou de descendre en puissance, et de bénéficier par la même occasion d’une capacité de stockage et de traitement beaucoup plus grande, et ce, à chaque niveau (même en configuration minimale).

## Limites maximales d’un service de recherche gratuit (partagé)

Les abonnés Azure peuvent utiliser le service de recherche partagé (mutualisé) pour le développement ou des applications de recherche très petites. Le service partagé est fourni avec votre abonnement Azure. C'est une option gratuite qui vous permet d’expérimenter le service avant de vous y abonner. Elle fournit :

[AZURE.INCLUDE [azure-search-limits](../../includes/azure-search-limits-free.md)]

Vous constaterez qu’aucun quota ou limite maximale n’est associé aux requêtes. Les requêtes par seconde (QPS) varient en fonction de la bande passante disponible et des ressources système concurrentes. Les ressources de calcul et de stockage Azure, qui réalise une sauvegarde de votre service partagé, sont partagées par plusieurs abonnés. Les requêtes par seconde de votre solution varie donc selon le nombre de charges de travail supplémentaires exécutées simultanément.

## Limites maximales d’un service de recherche standard (dédié)

Sous le niveau de tarification standard, un service de recherche dédié ne stocke que vos données et n’exécute que vos charges de travail. Contrairement au service partagé, l'allocation de ressources d’un service de recherche dédié peut être établie en fonction du niveau requis. Vous pouvez définir, de manière indépendante, les niveaux de ressources des partitions (pour améliorer la capacité de stockage) et des réplicas (pour fournir une haute disponibilité et mettre à l’échelle les requêtes par seconde et les performances d'indexation). Consultez [Gérer votre solution de recherche](search-manage.md) pour découvrir les différentes configurations de ressources.

Le tableau suivant établit une liste des limites supérieures, mais le graphique sous forme de matrice vous permet de découvrir plus en détail les différentes capacités disponibles en termes de [combinaisons de partitions et de réplicas](#chart) autorisées.

[AZURE.INCLUDE [azure-search-limits](../../includes/azure-search-limits-standard.md)]

La capacité d’Azure Search peut être achetée par incréments (appelés unités de recherche). Le niveau de tarification standard autorise jusqu’à 36 unités de recherche par service de recherche. Cette limite remplace les limites individuelles des réplicas et des partitions. Par exemple, vous ne pouvez pas mettre à l’échelle votre service jusqu'à 12 partitions et 6 réplicas, car il vous faudrait 72 unités de recherche (12 x 6), et cela dépasserait les 36 unités de recherche autorisées par service.

## À propos des partitions et des réplicas

Les **partitions** fournissent une capacité de stockage et des E/S. Un seul service de recherche peut contenir 12 partitions maximum. Chaque partition est fournie avec une limite stricte de 15 millions de documents ou de 25 Go de stockage, selon ce qui se produit en premier. Si vous ajoutez des partitions, votre service de recherche peut charger plusieurs documents. Par exemple, un service disposant d’une seule partition, qui stocke initialement 25 Go de données, peut stocker 50 Go si une deuxième partition est ajoutée au service.

Les **réplicas** sont des copies du moteur de recherche. Un seul service de recherche peut contenir 6 réplicas maximum. Vous avez besoin d’au moins 2 réplicas de disponibilité en lecture (requête) et d’au moins 3 réplicas de disponibilité en lecture-écriture (requête, indexation).

Une copie de chaque index s'exécute sur chaque réplica. Lorsque vous ajoutez des réplicas, les copies supplémentaires de l'index sont mis en ligne pour prendre en charge les charges de travail supérieures de requête et équilibrer la charge des requêtes sur plusieurs réplicas. Si vous disposez de plusieurs index (par ex. 6) et de 3 réplicas, chaque réplica possédera une copie des 6 index.

Notez que nous ne fournissons aucune estimation sur les requêtes par seconde, car l'exécution de requête peut varier considérablement selon la complexité de la requête et les charges de travail concurrentes. En moyenne, un réplica peut traiter environ 15 requêtes par seconde, mais le débit sera quelque peu supérieur ou inférieur en fonction de la complexité de la requête (les requêtes à facettes sont plus complexes) et de la latence du réseau. Bien que l'ajout de réplicas entraîne une amélioration de l’évolutivité et des performances, le résultat final n'est pas strictement linéaire : en ajoutant 3 réplicas, le débit ne sera pas forcément multiplié par trois. La latence des requêtes vous permet de découvrir si des réplicas supplémentaires doivent être ajoutés.

<a id="chart"></a>
## Combinaisons des partitions et des réplicas prises en charge

Comme indiqué précédemment, la limite effective des partitions et des réplicas dépend de la combinaison de ressources que vous avez sélectionnée, tout en respectant la limite des 36 unités de recherche par service. Les ressources sont allouées en termes d'unités de recherche. Un service de recherche dédié comporte un réplica et une partition (qui forme à en deux une unité de recherche).

La capacité supplémentaire peut être obtenue en multipliant les partitions par les réplicas, et vous fournir le nombre total d'unités de recherche requises pour prendre en charge une configuration donnée.

Le tableau suivant est un graphique qui répertorie les réplicas sur l'axe vertical et les partitions sur l'axe horizontal. L'intersection indique le nombre d'unités de recherche requises pour prendre en charge chaque combinaison, soumises à la limite de 36 unités de recherche par service. Si vous souhaitez utiliser, par exemple, 6 réplicas et 2 partitions, cette configuration nécessite 12 unités de recherche. Pour utiliser 4 réplicas et 2 partitions, vous devez disposer de 8 unités de recherche. En règle générale, la plupart des applications de recherche requièrent généralement plus de réplicas que de partitions.

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

> [AZURE.NOTE] Le nombre de réplicas et de partitions doit être partagé en 12, de manière égale (plus précisément, 1, 2, 3, 4, 6, 12). Azure Search divise au préalable chaque index en 12 partitions pour que celles-ci puissent être réparties sur plusieurs partitions. Par exemple, si votre service comporte trois partitions et que vous créez un nouvel index, chaque partition contiendra 4 partitions de l'index. Le partitionnement d’un index réalisé par Azure Search est un détail d'implémentation, susceptible d’être modifié dans les futures versions. Le nombre de partitions (12 à l’heure actuelle) peut être, à l’avenir, totalement différent.

## Choisissez une combinaison de partitions et de réplicas pour la haute disponibilité

Nous vous recommandons généralement de démarrer avec une partition et un ou deux réplicas, puis de monter en puissance à mesure que les volumes de requête se créent. Pour de nombreux déploiements, une partition offre suffisamment de stockage et d'E/S (15 millions de documents par partition).

Les charges de travail reposent toutefois sur les réplicas. Vous pouvez demander des réplicas supplémentaires si vous avez besoin d’une haute disponibilité ou d’un débit plus important.

Recommandations générales pour la haute disponibilité :

- 2 réplicas pour la haute disponibilité des charges de travail en lecture seule (requêtes)
- 3 réplicas (ou plus) pour la haute disponibilité des charges de travail en lecture-écriture (requêtes et indexation)

Il n'existe actuellement aucun mécanisme intégré de récupération d'urgence. L’ajout de partitions ou de réplicas ne vous permettra pas d’atteindre les objectifs de récupération d'urgence qui ont été fixés. Au lieu de cela, vous pouvez envisager d'ajouter de la redondance au niveau du service. Pour plus d’informations sur les solutions de contournement, consultez [ce billet de forum](https://social.msdn.microsoft.com/Forums/ee108a26-00c5-49f6-b1ff-64c66c8b828a/dr-and-high-availability-for-azure-search?forum=azuresearch).

> [AZURE.NOTE] Rappelez-vous que l'évolutivité et les contrats de niveau de service sont des fonctionnalités du service standard. Le service gratuit est proposé à un niveau de ressource fixe, avec des réplicas et des partitions partagés par plusieurs abonnés. Si vous démarrez avec le service gratuit et que vous voulez procéder à une mise à niveau, vous devrez créer un nouveau service de recherche Azure au niveau standard, puis recharger ensuite les index et les données vers le nouveau service. Pour obtenir des instructions sur l’approvisionnement du service, consultez [Créer un service Azure Search dans le portail](search-create-service-portal.md).

## Limites de clé API

Les clés API sont utilisées pour l'authentification de service. Il existe deux types de clé API. Les clés administrateur sont spécifiées dans l'en-tête de la requête. Les clés de requête sont spécifiées dans l'URL. Pour plus d’informations sur la gestion des clés, consultez la rubrique [Gestion de votre service de recherche sur Microsoft Azure](search-manage.md).

- 2 clés administrateur maximum par service
- 50 clés de requête maximum par service

## Limites de requête

- 16 Mo maximum par requête <sup>1</sup>
- La longueur maximale d’une URL est de 8 Ko
- 1 000 documents maximum par lot de charges, de fusions ou de suppressions d’index
- 32 champs maximum dans la clause $orderby
- La taille maximale des termes de recherche du texte encodé en UTF-8 est de 32 766 octets (32 Ko moins 2 octets)

## Limites de réponse

- 1 000 documents maximum retournés par page de résultats de recherche
- 100 suggestions maximum retournées par requête d’API de suggestion

<sup>1</sup> Dans Azure Search, le corps d’une requête est soumis à une limite supérieure de 16 Mo. Cela signifie qu’une limite pratique est imposée au contenu des champs individuels ou des collections qui ne font pas l’objet de limites théoriques (pour plus d’informations sur la composition et les restrictions des champs, consultez [Types de données pris en charge](https://msdn.microsoft.com/library/azure/dn798938.aspx)).

<!---HONumber=AcomDC_0224_2016-->