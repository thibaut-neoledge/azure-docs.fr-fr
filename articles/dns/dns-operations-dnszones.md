<properties 
   pageTitle="Opérations sur les zones DNS | Microsoft Azure" 
   description="Vous pouvez gérer les zones DNS à l’aide d’applets de commande Azure Powershell. Mise à jour, suppression et création des zones DNS sur Azure DNS" 
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
   ms.date="07/30/2015"
   ms.author="joaoma"/>

# Gestion des zones DNS


Ce guide explique comment gérer votre zone DNS. Il vous permettra de comprendre la séquence des opérations à effectuer pour administrer votre zone DNS.

## Création d’une zone DNS

Pour créer une zone DNS pour héberger votre domaine, utilisez l’applet de commande New-AzureDnsZone :

		PS C:\> $zone = New-AzureDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup [–Tag $tags] 

L’opération crée une zone DNS dans Azure DNS et renvoie un objet local correspondant à cette zone. Vous pouvez éventuellement spécifier un tableau de balises Azure Resource Manager. Pour plus d’informations, consultez la section [Balises et Etags](../dns-getstarted-create-dnszone#Etags-and-tags).

Le nom de la zone doit être unique dans le groupe de ressources et la zone ne doit pas déjà exister. Sinon, l’opération échoue.

Le même nom de zone peut être réutilisé dans un autre groupe de ressources ou abonnement Azure. Lorsque plusieurs zones partagent le même nom, chaque instance se voit affecter différentes adresses de serveur de noms, et une seule instance peut être déléguée à partir du domaine parent. Pour plus d’informations, consultez la page [Délégation d’un domaine Azure DNS](../dns-domain-delegation).

## Obtention d’une zone DNS

Pour récupérer une zone DNS, utilisez l’applet de commande Get-AzureDnsZone :

		PS C:\> $zone = Get-AzureDnsZone -Name contoso.com –ResourceGroupName MyAzureResourceGroup

L’opération renvoie un objet de zone DNS correspondant à une zone existante dans Azure DNS. Cet objet contient des données sur la zone (par exemple, le nombre de jeux d’enregistrements), mais ne contient pas les jeux d’enregistrement eux-mêmes.

## Création de la liste des zones DNS
En omettant le nom de la zone dans Get-AzureDnsZone, vous pouvez énumérer toutes les zones d’un groupe de ressources :

	PS C:\> $zoneList = Get-AzureDnsZone -ResourceGroupName MyAzureResourceGroup
Cette opération renvoie un tableau d’objets de la zone.

## Mise à jour d’une zone DNS
Vous pouvez apporter des modifications à une ressource de zone DNS à l’aide de Set-AzureDnsZone. Cette commande ne met pas à jour les jeux d’enregistrements DNS dans la zone (voir [Gestion des enregistrements DNS](../dns-operations-recordsets)). Elle est utilisée uniquement pour mettre à jour les propriétés de la ressource de zone elle-même. Elle est actuellement limitée aux balises Azure Resource Manager de la ressource de zone. Pour plus d’informations, consultez la section [Balises et Etags](../dns-getstarted-create-dnszone#Etags-and-tags).

Utilisez une des options suivantes pour mettre à jour la zone DNS :

### Option 1
 
Spécifiez la zone à l’aide du nom de zone et du groupe de ressources.

	PS C:\> Set-AzureDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup [-Tag $tags]

### Option 2
Spécifiez la zone à l’aide d’un objet $zone à partir de Get-AzureDnsZone :

	PS C:\> $zone = Get-AzureDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup
	PS C:\> <..modify $zone.Tags here...>
	PS C:\> Set-AzureDnsZone -Zone $zone [-Overwrite]

Lors de l’utilisation de Set-AzureDnsZone avec un objet $zone, les vérifications « Etag » sont utilisées afin de s’assurer que les modifications simultanées ne sont pas remplacées. Vous pouvez utiliser le commutateur facultatif « -Overwrite » pour supprimer ces vérifications. Pour plus d’informations, consultez la section [Balises et Etags](../dns-getstarted-create-dnszone#Etags-and-tags).

## Suppression d’une zone DNS

Les zones DNS peuvent être supprimées à l’aide de l’applet de commande Remove-AzureDnsZone.
 
Avant de supprimer une zone DNS dans Azure DNS, vous devez supprimer tous les jeux d’enregistrements, sauf les enregistrements NS et SOA à la racine de la zone qui ont été créés automatiquement en même temps que cette dernière.

Utilisez une des options suivantes pour supprimer une zone DNS :

### Option 1

Spécifiez la zone à l’aide du nom de zone et du nom de groupe de ressources :

	PS C:\> Remove-AzureDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup [-Force] 

Cette opération comporte un commutateur « -Force » qui supprime l’invite pour confirmer que vous souhaitez supprimer la zone DNS.
### Option 2

Spécifiez la zone à l’aide d’un objet $zone à partir de Get-AzureDnsZone :

	PS C:\> $zone = Get-AzureDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup
	PS C:\> Remove-AzureDnsZone -Zone $zone [-Force] [-Overwrite]

Le commutateur « -Force » est identique à celui de l’option 1.

Comme avec Set-AzureDnsZone, la spécification de la zone à l’aide d’un objet $zone permet aux vérifications « etag » de s’assurer que les modifications simultanées ne sont pas supprimées. <BR> L’indicateur facultatif « -Overwrite » supprime ces vérifications. Pour plus d’informations, consultez la section [Balises et Etags](../dns-getstarted-create-dnszone#Etags-and-tags).

L’objet de zone peut également être redirigé au lieu d’être transmis en tant que paramètre :

	PS C:\> Get-AzureDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup | Remove-AzureDnsZone [-Force] [-Overwrite]

## Étapes suivantes


[Gestion des enregistrements DNS](../dns-operations-recordsets)

[Automatisation des opérations à l’aide du Kit de développement (SDK) .NET](../dns-sdk)

<!---HONumber=August15_HO7-->