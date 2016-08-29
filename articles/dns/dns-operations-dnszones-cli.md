<properties 
   pageTitle="Gérer les zones DNS avec l’interface CLI | azure.microsoft.com/ Azure" 
   description="Vous pouvez gérer des zones DNS à l’aide de l’interface de ligne de commande Azure. Mise à jour, suppression et création des zones DNS sur Azure DNS" 
   services="dns" 
   documentationCenter="na" 
   authors="cherylmc" 
   manager="carmonm" 
   editor=""/>

<tags
   ms.service="dns"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services" 
   ms.date="08/16/2016"
   ms.author="cherylmc"/>

# Gestion des zones DNS à l'aide de l’interface de ligne de commande

> [AZURE.SELECTOR]
- [Interface de ligne de commande Azure](dns-operations-dnszones-cli.md)
- [PowerShell](dns-operations-dnszones.md)


Ce guide explique comment gérer vos ressources de zone DNS à l’aide de l’interface de ligne de commande multiplateforme.

Ces instructions utilisent l’interface de ligne de commande azure.microsoft.com/ Azure. Veillez à effectuer une mise à jour vers la version la plus récente de l’interface de ligne de commande Azure (0.9.8 ou version ultérieure) pour utiliser les commandes Azure DNS. Tapez `azure -v` pour vérifier la version de l’interface CLI Azure actuellement installée sur votre ordinateur. Vous pouvez installer l’interface de ligne de commande Azure pour Windows, Linux ou Mac. Pour plus d’informations, consultez la page [Installation de l’interface de ligne de commande Azure](../xplat-cli-install.md).

Azure DNS est un service Azure Resource Manager uniquement. Il ne possède aucune API ASM. Vous devez vérifier que l’interface CLI Azure est configurée pour utiliser le mode Resource Manager. Pour ce faire, utilisez la commande `azure config mode arm`.<BR> Si vous voyez le message « *Erreur : « dns » n’est pas une commande azure* », cela est probablement dû au fait que vous utilisez l’interface CLI Azure en mode ASM et non pas en mode Resource Manager.

## Création d’une zone DNS

Pour créer une zone DNS pour héberger votre domaine, consultez [Créer une zone Azure DNS en utilisant l’interface CLI](dns-getstarted-create-dnszone-cli.md).

## Obtention d’une zone DNS

Pour récupérer une zone DNS, utilisez `azure network dns zone show` :

	azure network dns zone show myresourcegroup contoso.com

L’opération retourne une zone DNS avec son ID, le nombre de jeux d’enregistrements et le nombre de balises.


## Création de la liste des zones DNS

Pour récupérer les zones DNS au sein d’un groupe de ressources, utilisez `azure network dns zone list`.

	azure network dns zone list myresourcegroup

## Mise à jour d’une zone DNS

Vous pouvez apporter des modifications à une ressource de zone DNS à l’aide de `azure network dns zone set`. Cette commande ne met pas à jour les jeux d’enregistrements DNS dans la zone (voir [Gestion des enregistrements DNS](dns-operations-recordsets.md)). Elle est utilisée seulement pour mettre à jour les propriétés de la ressource de zone elle-même. Elle est actuellement limitée aux balises Azure Resource Manager de la ressource de zone. Pour plus d’informations, consultez la section [Balises et Etags](dns-getstarted-create-dnszone.md#tagetag).

	azure network dns zone set myresourcegroup contoso.com -t prod=value2

## Suppression d’une zone DNS

Les zones DNS peuvent être supprimées en utilisant `azure network dns zone delete`. Cette opération a un commutateur *-q* facultatif qui supprime l’invite pour confirmer que vous voulez supprimer la zone DNS.
 
Avant de supprimer une zone DNS dans Azure DNS, vous devez supprimer tous les jeux d’enregistrements, sauf les enregistrements NS et SOA à la racine de la zone qui ont été créés automatiquement en même temps que cette dernière.

	azure network dns zone delete myresourcegroup contoso.com 



## Étapes suivantes
Après avoir créé une zone DNS, créez des [jeux d’enregistrements et des enregistrements](dns-getstarted-create-recordset-cli.md) pour démarrer la résolution des noms pour votre domaine Internet.

<!---HONumber=AcomDC_0817_2016-->