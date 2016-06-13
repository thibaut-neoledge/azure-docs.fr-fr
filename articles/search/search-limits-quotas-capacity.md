<properties
	pageTitle="Limites de service d’Azure Search | Microsoft Azure | Service de recherche cloud hébergé"
	description="Limites du service permettant de planifier la capacité et les limites maximales des requêtes et réponses pour Azure Search."
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
	ms.date="05/25/2016"
	ms.author="heidist"/>

# Limites de service d’Azure Search

Les limites maximales de stockage, de charges de travail et de quantités d’index, de documents et d’autres objets dépendent de l’ajout d’Azure Search avec le niveau de tarification **Gratuit**, **De base** ou **Standard**.

- Le niveau **Gratuit** est un service partagé multi-locataire qui est fourni avec votre abonnement Azure. Il s’agit d’une option sans coût supplémentaire pour les abonnés existants et qui vous permet de tester le service avant de vous inscrire pour obtenir des ressources dédiées. 
- Le niveau **De base (version préliminaire)** fournit des ressources informatiques dédiées aux charges de production à petite échelle. Ce niveau est actuellement en version préliminaire et bénéficie d’un [tarif réduit de 50 %](https://azure.microsoft.com/pricing/details/search/).
- Le niveau **Standard** est exécuté sur des ordinateurs dédiés, avec une capacité de stockage et de traitement beaucoup plus grande, et ce, à chaque niveau (même en configuration minimale). L’offre Standard est proposée sur deux niveaux (S1 et S2). 

Tous les niveaux peuvent être [configurés dans le portail](search-create-service-portal.md), à l’exception de S2, qui nécessite un ticket de support. Contactez le service clientèle ou azuresearch_contact@microsoft.com pour vous familiariser avec S2.

## Limites de niveau

[AZURE.INCLUDE [azure-search-limits](../../includes/azure-search-limits-tier.md)]

## Limites de stockage ##

[AZURE.INCLUDE [azure-search-limits](../../includes/azure-search-limits-storage.md)]

## Limites de données ##

Ressource|Gratuit|De base (version préliminaire) |S1|S2
---|---|---|---|----
Index : nombre maximal de champs par index|1 000|100 <sup>1</sup>|1 000|1 000
Index : nombre maximal de profils de score par index|16|16|16|16
Index : nombre maximal de fonctions par profil|8|8|8|8
Taille de document par API d’index<sup>2</sup> en Mo|< 16|< 16|< 16|< 16
Indexeurs : quantité maximale de charge d’indexation par appel|10 000 documents|Limité uniquement par le nombre maximal de documents|Limité uniquement par le nombre maximal de documents|Limité uniquement par le nombre maximal de documents
Indexeurs : durée maximale d’exécution|3 minutes|24 heures|24 heures|24 heures
Indexeur d’objets blob : taille maximale des objets blob, en Mo|16|16|128|256
Indexeur d’objets blob : nombre maximal de caractères du contenu extrait d’un objet blob|32 000|64 000|4 millions|4 millions

<sup>1</sup> Le niveau de base est le seul niveau de tarification soumis à une limite inférieure de 100 champs par index.

<sup>2</sup> Taille maximum du document lors de l’appel d’une API d’index. La taille du document est en fait une limite de taille du corps de requête de l’API d’index. Étant donné que vous pouvez transmettre en une seule fois un lot de plusieurs documents à l’API d’index, la limite de taille dépend du nombre de documents dans le lot. Pour un lot comprenant un seul document, la taille maximale du document sera 16 Mo de JSON.

Pour réduire la taille du document, pensez à exclure de la requête les données non requêtables. Les images et autres données binaires ne sont pas directement requêtables et ne doivent pas être stockées dans l’index. Pour intégrer les données non requêtables dans les résultats de la recherche, définissez un champ sans possibilité de recherche qui stocke une référence URL à la ressource.

## Limites de charge de travail (requêtes par seconde) ##

Ressource|Gratuit|De base (version préliminaire) |S1|S2
---|---|---|---|----
RPS|N/A|~3 par réplica|~15 par réplica|~60 par réplica

Le nombre de requêtes par seconde (RPS) est une approximation basée sur une méthode heuristique. Il est calculé à l’aide de charges de travail client simulées et réelles pour obtenir des valeurs estimées. Le débit exact du nombre de requêtes par seconde varie en fonction de vos données et de la nature de la requête.

Bien que des estimations approximatives figurent ci-dessus, la quantité réelle de requêtes par seconde (RPS) est difficile à déterminer, en particulier dans le service partagé Gratuit où le débit est basé sur la bande passante disponible et la concurrence pour les ressources système. Les ressources de calcul et de stockage sur lesquelles s’appuie le service partagé sont partagées par plusieurs abonnés. Les requêtes par seconde de votre solution varient donc toujours selon le nombre de charges de travail supplémentaires exécutées simultanément.

Au niveau Standard, vous pouvez mieux estimer les RPS, car vous contrôlez davantage de paramètres. Pour obtenir des conseils sur la façon de calculer les requêtes par seconde (RPS) pour vos charges de travail, consultez la section sur les meilleures pratiques sous [Gérer votre solution de recherche](search-manage.md).

## Limites de requête d’API

- 16 Mo maximum par requête <sup>1</sup>
- La longueur maximale d’une URL est de 8 Ko
- 1 000 documents maximum par lot de charges, de fusions ou de suppressions d’index
- 32 champs maximum dans la clause $orderby
- La taille maximale des termes de recherche du texte encodé en UTF-8 est de 32 766 octets (32 Ko moins 2 octets)

<sup>1</sup> Dans Azure Search, le corps d’une requête est soumis à une limite supérieure de 16 Mo. Cela signifie qu’une limite pratique est imposée au contenu des champs individuels ou des collections qui ne font pas l’objet de limites théoriques (pour plus d’informations sur la composition et les restrictions des champs, consultez [Types de données pris en charge](https://msdn.microsoft.com/library/azure/dn798938.aspx)).

## Limites de réponse d’API

- 1 000 documents maximum retournés par page de résultats de recherche
- 100 suggestions maximum retournées par requête d’API de suggestion

## Limites de clés API

Les clés API sont utilisées pour l'authentification de service. Il existe deux types de clé API. Les clés d’administration sont spécifiées dans l’en-tête de la demande et accordent un accès complet en lecture et en écriture au service. Les clés de requête sont en lecture seule, spécifiées dans l’URL et généralement distribuées aux applications clientes.

- 2 clés administrateur maximum par service
- 50 clés de requête maximum par service

<!---HONumber=AcomDC_0601_2016-->