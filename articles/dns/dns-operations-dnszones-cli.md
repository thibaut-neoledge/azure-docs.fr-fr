<properties 
   pageTitle="Opérations sur les zones DNS | Microsoft Azure"
	description="Vous pouvez gérer les zones DNS à l’aide d’applets de commande Azure Powershell ou de l’interface de ligne de commande. Mise à jour, suppression et création des zones DNS sur Azure DNS"
	services="dns"
	documentationCenter="na"
	authors="joaoma"
	manager="Adinah"
	editor=""/>

<tags
   ms.service="dns"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="infrastructure-services"
	ms.date="08/02/2015"
	ms.author="joaoma"/>

# Gestion des zones DNS

> [AZURE.SELECTOR]
- [Azure CLI](dns-operations-dnszones-cli.md)
- [Azure Powershell](dns-operations-dnszones.md)

Ce guide explique comment gérer votre zone DNS. Il vous permettra de comprendre la séquence des opérations à effectuer pour administrer votre zone DNS.

## Création d’une zone DNS

Pour créer une zone DNS pour héberger votre domaine, utilisez la fonction `azure network dns-zone create` :

		Azure network dns-zone create -n contoso.com -g myresourcegroup -t "project=demo";"env=test"

L’opération crée une nouvelle zone DNS dans le système DNS Azure. Vous pouvez éventuellement spécifier un tableau de balises Azure Resource Manager. Pour plus d’informations, consultez la section [Balises et Etags](dns-getstarted-create-dnszone.md#Etags-and-tags).

Le nom de la zone doit être unique dans le groupe de ressources et la zone ne doit pas déjà exister. Sinon, l’opération échoue.

Le même nom de zone peut être réutilisé dans un autre groupe de ressources ou abonnement Azure. Lorsque plusieurs zones partagent le même nom, chaque instance se voit affecter différentes adresses de serveur de noms, et une seule instance peut être déléguée à partir du domaine parent. Pour plus d’informations, consultez la page [Délégation d’un domaine Azure DNS](dns-domain-delegation.md).

## Obtention d’une zone DNS

Pour récupérer une zone DNS, utilisez la commande `azure network dns-zone show` :

	azure network dns-zone show myresourcegroup contoso.com

L’opération retourne une zone DNS avec son ID, le nombre de jeux d’enregistrements et le nombre de balises.


## Création de la liste des zones DNS

Pour récupérer les zones DNS au sein d’un groupe de ressources, utilisez la commande `azure network dns-zone list` :

	azure network dns-zone list myresourcegroup


## Mise à jour d’une zone DNS

Vous pouvez apporter des modifications à une ressource de zone DNS à l’aide de `azure network dns-zone set`. Cette commande ne met pas à jour les jeux d’enregistrements DNS dans la zone (voir [Gestion des enregistrements DNS](dns-operations-recordsets.md)). Elle est utilisée uniquement pour mettre à jour les propriétés de la ressource de zone elle-même. Elle est actuellement limitée aux balises Azure Resource Manager de la ressource de zone. Pour plus d’informations, consultez la section [Balises et Etags](dns-getstarted-create-dnszone.md#Etags-and-tags).

	azure network dns-zone set myresourcegroup contoso.com -t prod=value2

## Suppression d’une zone DNS

Les zones DNS peuvent être supprimées à l’aide de la commande `azure network dns-zone delete`.
 
Avant de supprimer une zone DNS dans Azure DNS, vous devez supprimer tous les jeux d’enregistrements, sauf les enregistrements NS et SOA à la racine de la zone qui ont été créés automatiquement en même temps que cette dernière.

	azure network dns-zone delete myresourcegroup contoso.com 

Cette opération comporte un commutateur « -q » qui supprime l’invite pour confirmer que vous souhaitez supprimer la zone DNS.


## Étapes suivantes


[Gestion des enregistrements DNS](dns-operations-recordsets-cli.md)

[Automatisation des opérations à l’aide du Kit de développement (SDK) .NET](dns-sdk.md)

<!---HONumber=August15_HO9-->