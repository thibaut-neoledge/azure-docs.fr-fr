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
	ms.date="02/28/2016"
	ms.author="heidist"/>

# Limites de service d’Azure Search

Les limites maximales de stockage, de charges de travail et de quantités d’index, de documents et d’autres objets dépendent de l’ajout d’Azure Search avec le niveau de tarification **Gratuit**, **De base** ou **Standard**.

- Le niveau **Gratuit** est un service partagé multi-locataire qui est fourni avec votre abonnement Azure. Il s’agit d’une option sans coût supplémentaire pour les abonnés existants et qui vous permet de tester le service avant de vous inscrire pour obtenir des ressources dédiées. 
- Le niveau **De base (version préliminaire)** fournit des ressources informatiques dédiées aux charges de production à petite échelle. Ce niveau est actuellement en version préliminaire et bénéficie d’un [tarif réduit](https://azure.microsoft.com/pricing/details/search/).
- Le niveau **Standard** est exécuté sur des ordinateurs dédiés, avec une capacité de stockage et de traitement beaucoup plus grande, et ce, à chaque niveau (même en configuration minimale). L’offre Standard est proposée sur deux niveaux (S1 et S2). 

## Limites de niveau

[AZURE.INCLUDE [azure-search-limits](../../includes/azure-search-limits-all.md)]

> [AZURE.NOTE] Les Requêtes par seconde (RPS) sont variables, en particulier dans le service partagé, car le débit est basé sur la bande passante disponible et la concurrence pour les ressources système. Les ressources de calcul et de stockage Azure, qui réalisent une sauvegarde du service partagé, sont partagées par plusieurs abonnés. Les requêtes par seconde de votre solution varie donc toujours selon le nombre de charges de travail supplémentaires exécutées simultanément. Au niveau Standard, vous pouvez mieux estimer les RPS, car vous contrôlez davantage de paramètres. Consultez la section sur les meilleures pratiques sous [Gérer votre solution de recherche](search-manage.md) pour obtenir des conseils sur la façon de calculer les RPS pour vos charges de travail.

## Limites de clé API

Les clés API sont utilisées pour l'authentification de service. Il existe deux types de clé API. Les clés d’administration sont spécifiées dans l’en-tête de la demande et accordent un accès complet en lecture et en écriture au service. Les clés de requête sont en lecture seule, spécifiées dans l’URL et généralement distribuées aux applications clientes.

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

<!---HONumber=AcomDC_0302_2016-->