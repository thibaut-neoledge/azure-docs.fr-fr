<properties
	pageTitle="Limites de service d’Azure Search | Microsoft Azure"
	description="Limites du service permettant de planifier la capacité et les limites maximales des requêtes et réponses pour Azure Search."
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
	ms.date="08/03/2016"
	ms.author="heidist"/>

# Limites de service d’Azure Search

Les limites maximales de stockage, de charges de travail et de quantités d’index, de documents et d’autres objets dépendent de l’ajout d’Azure Search avec le niveau de tarification **Gratuit**, **De base** ou **Standard**.

- Le niveau **Gratuit** est un service partagé multi-locataire qui est fourni avec votre abonnement Azure. Il s’agit d’une option sans coût supplémentaire pour les abonnés existants et qui vous permet de tester le service avant de vous inscrire pour obtenir des ressources dédiées.
- Le niveau **De base** fournit des ressources informatiques dédiées aux charges de production à petite échelle.
- Le niveau **Standard** est exécuté sur des ordinateurs dédiés, avec une capacité de stockage et de traitement beaucoup plus grande, et ce, à chaque niveau (même en configuration minimale). Le niveau Standard apparaît dans : S1, S2, S3 et S3 Haute densité (S3 HD). S3 et S3 HD sont actuellement en version préliminaire et bénéficient d’un tarif réduit de 50 %.

Tous les niveaux peuvent être [configurés dans le portail](search-create-service-portal.md). Un service se voit initialement allouer une partition et un réplica, mais vous pouvez modifier l’allocation des ressources une fois le service créé. Consultez [Mettre à l’échelle les niveaux de ressources pour interroger et indexer les charges de travail](search-capacity-planning.md) pour plus d’informations.

## Limites par abonnement

[AZURE.INCLUDE [azure-search-limits-per-subscription](../../includes/azure-search-limits-per-subscription.md)]

## Limites par service ##

[AZURE.INCLUDE [azure-search-limits-per-service](../../includes/azure-search-limits-per-service.md)]

## Limites par index ##

Il existe une correspondance biunivoque entre les limites sur les index et les limites sur les indexeurs. Pour une limite de 200 index par service S2, les indexeurs et sources de données d’indexeur sont également de 200 au maximum pour le même service.

Ressource|Gratuit|De base |S1|S2|S3 (version préliminaire)|S3 HD (version préliminaire) 
---|---|---|---|---- |---|----
Index : nombre maximal de champs par index|1 000|100 <sup>1</sup>|1 000|1 000|1 000|1 000 
Index : nombre maximal de profils de score par index|16|16|16|16|16|16 
Index : nombre maximal de fonctions par profil|8|8|8|8|8|8 
Indexeurs : quantité maximale de charge d’indexation par appel|10 000 documents|Limité uniquement par le nombre maximal de documents|Limité uniquement par le nombre maximal de documents|Limité uniquement par le nombre maximal de documents|Limité uniquement par le nombre maximal de documents|N/A <sup>2</sup> 
Indexeurs : durée maximale d’exécution|3 minutes|24 heures|24 heures|24 heures|24 heures|N/A <sup>2</sup> 
Indexeur d’objets blob : taille maximale des objets blob, en Mo|16|16|128|256|256|N/A <sup>2</sup> 
Indexeur d’objets blob : nombre maximal de caractères du contenu extrait d’un objet blob|32 000|64 000|4 millions|4 millions|4 millions|N/A <sup>2</sup> 

<sup>1</sup> Le niveau de base est la seule référence soumise à une limite inférieure de 100 champs par index.

<sup>2</sup> S3 HD ne prend actuellement pas en charge les indexeurs ou les sources de données d’indexeur. Contactez le support Azure si vous avez un besoin urgent de cette fonctionnalité.

## Limites de taille des documents ##

Ressource|Gratuit|De base |S1|S2|S3 (version préliminaire)|S3 HD (version préliminaire) 
---|---|---|---|---- |---|----
Taille de chaque document par API d’index|< 16 Mo|< 16 Mo|< 16 Mo |< 16 Mo|< 16 Mo|< 16 Mo

Indique la taille maximum du document lors de l’appel d’une API d’index. La taille du document est en fait une limite de taille du corps de requête de l’API d’index. Étant donné que vous pouvez transmettre en une seule fois un lot de plusieurs documents à l’API d’index, la limite de taille dépend en fait du nombre de documents dans le lot. Pour un lot comprenant un seul document, la taille maximale du document sera 16 Mo de JSON.

Pour réduire la taille du document, pensez à exclure de la requête les données non requêtables. Les images et autres données binaires ne sont pas directement requêtables et ne doivent pas être stockées dans l’index. Pour intégrer les données non requêtables dans les résultats de la recherche, définissez un champ sans possibilité de recherche qui stocke une référence URL à la ressource.

## Limites de charge de travail (requêtes par seconde) ##

Ressource|Gratuit|De base|S1|S2|S3 (version préliminaire)|S3 HD (version préliminaire)
---|---|---|---|----|---|----
RPS|N/A|~3 par réplica|~15 par réplica|~60 par réplica|Moins de 60 par réplica|Moins de 60 par réplica

Le nombre de requêtes par seconde (RPS) est une approximation basée sur une méthode heuristique. Il est calculé à l’aide de charges de travail client simulées et réelles pour obtenir des valeurs estimées. Le débit exact du nombre de requêtes par seconde varie en fonction de vos données et de la nature de la requête.

Bien que des estimations approximatives figurent ci-dessus, le débit réel est difficile à déterminer, en particulier dans le service partagé Gratuit où le débit est basé sur la bande passante disponible et la concurrence pour les ressources système. Dans le niveau Gratuit, les ressources de calcul et de stockage sont partagées par plusieurs abonnés. Les requêtes par seconde de votre solution varient donc toujours selon le nombre de charges de travail supplémentaires exécutées simultanément.

Au niveau Standard, vous pouvez mieux estimer les RPS, car vous contrôlez davantage de paramètres. Pour obtenir des conseils sur la façon de calculer les requêtes par seconde (RPS) de vos charges de travail, consultez la section sur les meilleures pratiques sous [Gérer votre solution de recherche](search-manage.md).

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

<!---HONumber=AcomDC_0907_2016-->