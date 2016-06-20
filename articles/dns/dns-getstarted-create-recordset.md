<properties
   pageTitle="Création d’un jeu d’enregistrements et d’enregistrements pour une zone DNS à l’aide de PowerShell | Microsoft Azure"
   description="Création d’enregistrements hôtes pour Azure DNS. Configuration d’enregistrements et de jeux d’enregistrements à l’aide de PowerShell"
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
   ms.date="05/06/2016"
   ms.author="cherylmc"/>



# Création de jeux d’enregistrements et d’enregistrements DNS à l’aide de PowerShell


> [AZURE.SELECTOR]
- [Portail Azure](dns-getstarted-create-recordset-portal.md)
- [PowerShell](dns-getstarted-create-recordset.md)
- [Interface de ligne de commande Azure](dns-getstarted-create-recordset-cli.md)

Cet article vous guide dans le processus de création de jeux d’enregistrements et d’enregistrements à l’aide de Windows PowerShell. Après avoir créé votre zone DNS, vous ajoutez les enregistrements DNS de votre domaine. Pour ce faire, vous devez d’abord comprendre les enregistrements DNS et les jeux d’enregistrements.

[AZURE.INCLUDE [dns-about-records-include](../../includes/dns-about-records-include.md)]

## Vérifiez que vous disposez de la dernière version de PowerShell

Vérifiez que la dernière version des applets de commande PowerShell Azure Resource Manager est installée. Pour plus d’informations sur l’installation des applets de commande PowerShell, consultez [Comment installer et configurer Azure PowerShell](../powershell-install-configure.md).

## Création d’un jeu d’enregistrements et d’un enregistrement

Cette section décrit la création d’un jeu d’enregistrements et d’un enregistrement.


### 1\. Connexion à votre abonnement

Ouvrez la console PowerShell et connectez-vous à votre compte. Utilisez l’exemple suivant pour faciliter votre connexion :

	Login-AzureRmAccount

Vérifiez les abonnements associés au compte.

	Get-AzureRmSubscription

Spécifiez l’abonnement à utiliser.

	Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"

Pour plus d’informations sur l’utilisation de PowerShell, consultez [Utilisation de Windows PowerShell avec Resource Manager](../powershell-azure-resource-manager.md).


### 2\. Création d’un jeu d'enregistrements

Vous pouvez utiliser l’applet de commande `New-AzureRmDnsRecordSet` pour créer des jeux d’enregistrements. Lors de la création d’un jeu d’enregistrements, vous devez spécifier le nom du jeu d’enregistrements, la zone, la durée de vie (TTL) et le type d’enregistrement.

Pour créer un jeu d’enregistrements dans l’apex de la zone (dans cet exemple, « contoso.com »), utilisez le nom d’enregistrement "@" (guillemets compris). Il s'agit d'une convention DNS courante.

L’exemple suivant crée un jeu d’enregistrements avec le nom relatif « www » dans la zone DNS « contoso.com ». Le nom complet du jeu d’enregistrements est « www.contoso.com ». Le type d’enregistrement est « A » et la durée de vie est de 60 secondes. À la fin de cette étape, vous obtenez un jeu d’enregistrements « www » vide qui est affecté à la variable *$rs*.

	$rs = New-AzureRmDnsRecordSet -Name "www" -RecordType "A" -ZoneName "contoso.com" -ResourceGroupName "MyAzureResourceGroup" -Ttl 60

#### Si un jeu d’enregistrements existe déjà

Si un jeu d’enregistrements existe déjà, la commande échoue, sauf si commutateur *-Overwrite* est utilisé. L’option *-Overwrite* déclenche une invite de confirmation, qui peut être supprimée à l’aide du commutateur *-Force*.


	$rs = New-AzureRmDnsRecordSet -Name www -RecordType A -Ttl 300 -ZoneName contoso.com -ResouceGroupName MyAzureResouceGroup [-Tag $tags] [-Overwrite] [-Force]


Dans cet exemple, vous spécifiez la zone en utilisant le nom de zone et le nom de groupe de ressources. Vous pouvez également spécifier un objet de zone retourné par `Get-AzureRmDnsZone` ou `New-AzureRmDnsZone`.

	$zone = Get-AzureRmDnsZone -Name contoso.com –ResourceGroupName MyAzureResourceGroup
	$rs = New-AzureRmDnsRecordSet -Name www -RecordType A -Ttl 300 –Zone $zone [-Tag $tags] [-Overwrite] [-Force]

`New-AzureRmDnsRecordSet` retourne un objet local qui représente le jeu d’enregistrements créé dans Azure DNS.

### 3\. Ajouter un enregistrement

Pour utiliser le jeu d’enregistrements « www » que vous venez de créer, vous devez y ajouter des enregistrements. Ajoutez les enregistrements IPv4 *A* au jeu d’enregistrements « www » comme dans l’exemple suivant : Cet exemple s’appuie sur la variable *$rs* que vous avez définie à l’étape précédente.

L’ajout d’enregistrements à un jeu d’enregistrements avec `Add-AzureRmDnsRecordConfig` est une opération hors connexion. Seule la variable locale *$rs* est mise à jour.


	Add-AzureRmDnsRecordConfig -RecordSet $rs -Ipv4Address 134.170.185.46
	Add-AzureRmDnsRecordConfig -RecordSet $rs -Ipv4Address 134.170.188.221

### 4\. Valider les modifications

Validez les modifications apportées au jeu d’enregistrements. Utilisez `Set-AzureRmDnsRecordSet` pour charger les modifications apportées au jeu d’enregistrements dans Azure DNS.

	Set-AzureRmDnsRecordSet -RecordSet $rs

### 5\. Récupérer le jeu d’enregistrements

Vous pouvez récupérer le jeu d’enregistrements auprès d’Azure DNS en utilisant `Get-AzureRmDnsRecordSet`, comme dans l’exemple suivant.


	Get-AzureRmDnsRecordSet –Name www –RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup


	Name              : www
	ZoneName          : contoso.com
	ResourceGroupName : MyAzureResourceGroup
	Ttl               : 3600
	Etag              : 68e78da2-4d74-413e-8c3d-331ca48246d9
	RecordType        : A
	Records           : {134.170.185.46, 134.170.188.221}
	Tags              : {}


Vous pouvez également utiliser l’outil nslookup ou d’autres outils DNS pour interroger le nouveau jeu d’enregistrements.

Si vous n’avez pas encore délégué le domaine aux serveurs de noms Azure DNS, vous devez spécifier explicitement le nom, le serveur et l’adresse pour votre zone.


	nslookup www.contoso.com ns1-01.azure-dns.com

	Server: ns1-01.azure-dns.com
	Address:  208.76.47.1

	Name:    www.contoso.com
	Addresses:  134.170.185.46
    	        134.170.188.221

## Création d’un jeu d’enregistrements de chaque type avec un seul enregistrement


Les exemples suivants montrent comment créer un jeu d’enregistrements de chaque type d’enregistrement. Chaque jeu d’enregistrements contient un seul enregistrement.

[AZURE.INCLUDE [dns-add-record-ps-include](../../includes/dns-add-record-ps-include.md)]


## Étapes suivantes

[Gestion des zones DNS à l'aide de PowerShell](dns-operations-dnszones.md)

[Gestion des enregistrements et des jeux d’enregistrements DNS à l’aide de PowerShell](dns-operations-recordsets.md)

[Automatisation des opérations Azure avec le Kit de développement (SDK) .NET](dns-sdk.md)

<!---HONumber=AcomDC_0608_2016-->