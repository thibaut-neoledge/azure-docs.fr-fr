<properties 
   pageTitle="Gérer les zones DNS avec PowerShell | Microsoft Azure" 
   description="Vous pouvez gérer des zones DNS à l’aide d’Azure Powershell. Mise à jour, suppression et création des zones DNS sur Azure DNS" 
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

# Gestion des zones DNS à l'aide de PowerShell

> [AZURE.SELECTOR]
- [Interface de ligne de commande Azure](dns-operations-dnszones-cli.md)
- [PowerShell](dns-operations-dnszones.md)



Cet article vous montre comment gérer votre zone DNS avec PowerShell. Pour pouvoir utiliser cette procédure, vous devez installer la dernière version des applets de commande PowerShell Azure Resource Manager (1.0 ou ultérieure). Pour plus d’informations sur l’installation des applets de commande PowerShell, consultez [Comment installer et configurer Azure PowerShell](../powershell-install-configure.md).


## Création d’une zone DNS

Pour créer une zone DNS, consultez [Créer une zone DNS à l’aide de PowerShell](dns-getstarted-create-dnszone.md).

## Obtention d’une zone DNS

Pour récupérer une zone DNS, utilisez l’applet de commande `Get-AzureRmDnsZone` : Cette opération retourne un objet de zone DNS correspondant à une zone existante dans Azure DNS. Cet objet contient des données sur la zone (par exemple le nombre de jeux d’enregistrements), mais ne contient pas les jeux d’enregistrement eux-mêmes.

	$zone = Get-AzureRmDnsZone -Name contoso.com –ResourceGroupName MyAzureResourceGroup

## Création de la liste des zones DNS

En omettant le nom de la zone dans `Get-AzureRmDnsZone`, vous pouvez énumérer toutes les zones d’un groupe de ressources. Cette opération renvoie un tableau d’objets de la zone.

	$zoneList = Get-AzureRmDnsZone -ResourceGroupName MyAzureResourceGroup

## Mise à jour d’une zone DNS

Vous pouvez apporter des modifications à une ressource de zone DNS à l’aide de `Set-AzureRmDnsZone`. Cette commande ne met pas à jour les jeux d’enregistrements DNS dans la zone (voir [Gestion des enregistrements DNS](dns-operations-recordsets.md)). Elle est utilisée seulement pour mettre à jour les propriétés de la ressource de zone elle-même. Elle est actuellement limitée aux balises Azure Resource Manager de la ressource de zone. Pour plus d’informations, consultez la section [Balises et Etags](dns-getstarted-create-dnszone.md#Etags-and-tags).

Utilisez une des options suivantes pour mettre à jour la zone DNS :

### Spécifier la zone en utilisant le nom de zone et le groupe de ressources

	Set-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup [-Tag $tags]

### Spécifier la zone en utilisant un objet $zone

Spécifier la zone en utilisant un objet $zone provenant de `Get-AzureRmDnsZone` Lors de l’utilisation de `Set-AzureRmDnsZone` avec un objet $zone, les vérifications d’ETag sont utilisées pour éviter que les modifications simultanées ne sont pas remplacées. Vous pouvez utiliser le commutateur *-Overwrite* pour supprimer ces vérifications. Pour plus d’informations, consultez la section [Balises et Etags](dns-getstarted-create-dnszone.md#Etags-and-tags).


	$zone = Get-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup
	<..modify $zone.Tags here...>
	Set-AzureRmDnsZone -Zone $zone [-Overwrite]


## Suppression d’une zone DNS

Les zones DNS peuvent être supprimées à l'aide de l'applet de commande Remove-AzureRmDnsZone.
 
Avant de supprimer une zone DNS dans Azure DNS, vous devez supprimer tous les jeux d’enregistrements, sauf les enregistrements NS et SOA à la racine de la zone qui ont été créés automatiquement en même temps que cette dernière.

Utilisez une des options suivantes pour supprimer une zone DNS :

### Spécifier la zone en utilisant le nom de zone et le nom de groupe de ressources

Cette opération a un commutateur *-Force* qui supprime l’invite pour confirmer que vous voulez supprimer la zone DNS.

	Remove-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup [-Force] 

### Spécifier la zone en utilisant un objet $zone 

Spécifier la zone en utilisant un objet $zone provenant de `Get-AzureRmDnsZone` Cette opération a un commutateur *-Force* qui supprime l’invite pour confirmer que vous voulez supprimer la zone DNS. Comme avec `Set-AzureRmDnsZone`, la spécification de la zone avec un objet $zone active les vérifications d’ETag pour éviter que les modifications simultanées ne sont pas supprimées. <BR> Le commutateur facultatif *-Overwrite* supprime ces vérifications. Pour plus d’informations, consultez la section [Balises et Etags](dns-getstarted-create-dnszone.md#Etags-and-tags).

	$zone = Get-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup
	Remove-AzureRmDnsZone -Zone $zone [-Force] [-Overwrite]



L’objet de zone peut également être redirigé au lieu d’être transmis en tant que paramètre :

	Get-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup | Remove-AzureRmDnsZone [-Force] [-Overwrite]

## Étapes suivantes

Après avoir créé une zone DNS, créez des [jeux d’enregistrements et des enregistrements](dns-getstarted-create-recordset.md) pour démarrer la résolution des noms pour votre domaine Internet.

<!---HONumber=AcomDC_0817_2016-->