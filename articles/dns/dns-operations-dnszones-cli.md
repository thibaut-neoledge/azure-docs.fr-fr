<properties 
   pageTitle="Opérations sur les zones DNS à l’aide de l’interface de ligne de commande | Microsoft Azure" 
   description="Vous pouvez gérer des zones DNS à l’aide de l’interface de ligne de commande Azure. Mise à jour, suppression et création des zones DNS sur Azure DNS" 
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
   ms.date="02/09/2016"
   ms.author="joaoma"/>

# Gestion des zones DNS à l'aide de l’interface de ligne de commande

> [AZURE.SELECTOR]
- [Interface de ligne de commande Azure](dns-operations-dnszones-cli.md)
- [PowerShell](dns-operations-dnszones.md)

Ce guide explique comment gérer vos ressources de zone DNS à l’aide de l’interface de ligne de commande multiplateforme.

>[AZURE.NOTE] Azure DNS est un service Azure Resource Manager uniquement. Il ne possède aucune API ASM. Par conséquent, vous devez vous assurer que l’interface CLI Azure est configurée pour le mode Gestionnaire de ressources, à l’aide de la commande « azure config mode arm ».

>Si vous voyez « erreur: « dns » n’est pas une commande azure », cela est probablement dû au fait que vous utilisez Azure CLI en mode ASM et non en mode Gestionnaire de ressources.
 
## Création d’une zone DNS

Pour créer une nouvelle zone DNS pour héberger votre domaine, utilisez `azure network dns zone create` :

	azure network dns zone create -n contoso.com -g myresourcegroup -t "project=demo";"env=test"

L’opération crée une nouvelle zone DNS dans le système DNS Azure. Vous pouvez éventuellement spécifier un tableau de balises Azure Resource Manager. Pour plus d’informations, consultez la section [Balises et Etags](dns-getstarted-create-dnszone.md#Etags-and-tags).

Le nom de la zone doit être unique dans le groupe de ressources et la zone ne doit pas déjà exister. Sinon, l’opération échoue.

Le même nom de zone peut être réutilisé dans un autre groupe de ressources ou abonnement Azure. Lorsque plusieurs zones partagent le même nom, chaque instance se voit affecter différentes adresses de serveur de noms, et une seule instance peut être déléguée à partir du domaine parent. Pour plus d’informations, consultez la page [Délégation d’un domaine Azure DNS](dns-domain-delegation.md).

## Obtention d’une zone DNS

Pour récupérer une zone DNS, utilisez la commande `azure network dns zone show` :

	azure network dns zone show myresourcegroup contoso.com

L’opération retourne une zone DNS avec son ID, le nombre de jeux d’enregistrements et le nombre de balises.


## Création de la liste des zones DNS

Pour récupérer les zones DNS au sein d'un groupe de ressources, utilisez la commande `azure network dns zone list` :

	azure network dns zone list myresourcegroup


## Mise à jour d’une zone DNS

Vous pouvez apporter des modifications à une ressource de zone DNS à l’aide de `azure network dns zone set`. Cette commande ne met pas à jour les jeux d’enregistrements DNS dans la zone (voir [Gestion des enregistrements DNS](dns-operations-recordsets.md)). Elle est utilisée uniquement pour mettre à jour les propriétés de la ressource de zone elle-même. Elle est actuellement limitée aux balises Azure Resource Manager de la ressource de zone. Pour plus d’informations, consultez la section [Balises et Etags](dns-getstarted-create-dnszone.md#Etags-and-tags).

	azure network dns zone set myresourcegroup contoso.com -t prod=value2

## Suppression d’une zone DNS

Les zones DNS peuvent être supprimées à l’aide de la commande `azure network dns zone delete`.
 
Avant de supprimer une zone DNS dans Azure DNS, vous devez supprimer tous les jeux d’enregistrements, sauf les enregistrements NS et SOA à la racine de la zone qui ont été créés automatiquement en même temps que cette dernière.

	azure network dns zone delete myresourcegroup contoso.com 

Cette opération comporte un commutateur « -q » qui supprime l’invite pour confirmer que vous souhaitez supprimer la zone DNS.


## Étapes suivantes


Découvrez comment [gérer des enregistrements DNS](dns-operations-recordsets-cli.md) et [automatiser les opérations avec le Kit de développement logiciel (SDK) .NET](dns-sdk.md).

<!------HONumber=AcomDC_0309_2016-->