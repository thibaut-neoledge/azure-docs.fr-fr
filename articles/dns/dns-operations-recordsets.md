<properties
   pageTitle="Gestion des jeux d’enregistrements DNS et des enregistrements à l’aide du portail Azure | Microsoft Azure"
   description="Gestion des jeux d'enregistrements DNS et des enregistrements dans Azure DNS lorsque votre domaine est hébergé dans Azure DNS. Toutes les commandes PowerShell pour les opérations sur les jeux d'enregistrements et les enregistrements."
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

# Gérer les enregistrements et les jeux d’enregistrements DNS à l’aide de PowerShell



> [AZURE.SELECTOR]
- [Portail Azure](dns-operations-recordsets-portal.md)
- [Interface de ligne de commande Azure](dns-operations-recordsets-cli.md)
- [PowerShell](dns-operations-recordsets.md)



Ce guide explique comment gérer les jeux d’enregistrements et les enregistrements pour votre zone DNS à l’aide de Windows PowerShell.

Il est important de comprendre la différence entre les jeux d’enregistrements DNS et les enregistrements DNS individuels. Un jeu d’enregistrements est une collection d’enregistrements dans une zone ayant le même nom et le même type. Pour plus d’informations, consultez [Créer des jeux d’enregistrements DNS en utilisant le portail Azure](dns-getstarted-create-recordset-portal.md).

Pour pouvoir gérer vos jeux d’enregistrements et vos enregistrements, vous avez besoin de la dernière version des applets de commande PowerShell Azure Resource Manager. Pour plus d’informations, consultez la rubrique [Installation et configuration d’Azure PowerShell](../powershell-install-configure.md). Pour plus d’informations sur l’utilisation de PowerShell, consultez [Utilisation d’Azure PowerShell avec Azure Resource Manager](../powershell-azure-resource-manager.md).



## Création d’un nouveau jeu d’enregistrements et d’un enregistrement

Pour créer un jeu d’enregistrements à l’aide de PowerShell, consultez [Créer des jeux d’enregistrements et des enregistrements DNS en utilisant PowerShell](dns-getstarted-create-recordset.md).

## Obtention d’un jeu d'enregistrements

Pour récupérer un jeu d’enregistrements existant, utilisez `Get-AzureRmDnsRecordSet`. Spécifiez le nom relatif, le type d’enregistrement et la zone du jeu d’enregistrements.

	$rs = Get-AzureRmDnsRecordSet –Name www –RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup

Comme avec `New-AzureRmDnsRecordSet`, le nom d’enregistrement doit être un nom relatif, c’est-à-dire sans le nom de la zone.

Vous pouvez spécifier la zone à l’aide du nom de zone et du nom de groupe de ressources, ou à l’aide d’un objet de zone :

	$zone = Get-AzureRmDnsZone -Name contoso.com -ResouceGroupName MyAzureResourceGroup
	$rs = Get-AzureRmDnsRecordSet -Name www –RecordType A -Zone $zone

`Get-AzureRmDnsRecordSet` retourne un objet local qui représente le jeu d’enregistrements créé dans Azure DNS.

## Liste des jeux d'enregistrements

Vous pouvez également utiliser `Get-AzureRmDnsRecordSet` pour répertorier les jeux d’enregistrements si vous omettez les paramètres *–Name* et/ou *–RecordType*.

### Pour répertorier tous les jeux d’enregistrements

Cet exemple retourne tous les jeux d’enregistrements, quel que soit le nom ou le type d’enregistrement :

	$list = Get-AzureRmDnsRecordSet -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup

### Pour répertorier les jeux d’enregistrements pour un type d’enregistrement donné

Cet exemple retourne tous les jeux d’enregistrements correspondant au type d’enregistrement donné. Dans ce cas, le jeu d’enregistrements retourné regroupera les enregistrements « A » :

	$list = Get-AzureRmDnsRecordSet –RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup

La zone peut être spécifiée à l’aide des paramètres *-ZoneName* et *-ResourceGroupName* (comme indiqué) ou en spécifiant un objet de zone :

	$zone = Get-AzureRmDnsZone -Name contoso.com -ResouceGroupName MyAzureResourceGroup
	$list = Get-AzureRmDnsRecordSet -Zone $zone

## Ajout d’un enregistrement à un jeu d'enregistrements

Vous pouvez utiliser l’applet de commande `Add-AzureRmDnsRecordConfig` pour ajouter des enregistrements aux jeux d’enregistrements. Cette opération se fait hors connexion. Seul l’objet local qui représente le jeu d’enregistrements est modifié.

Les paramètres pour ajouter des enregistrements à un jeu d'enregistrements varient selon le type de jeu d'enregistrements. Par exemple, lors de l’utilisation d’un jeu d’enregistrements de type « A », vous pouvez spécifier seulement des enregistrements avec le paramètre *-IPv4Address*.

Des enregistrements supplémentaires peuvent être ajoutés à chaque jeu d’enregistrements grâce à des appels supplémentaires à `Add-AzureRmDnsRecordConfig`. Vous pouvez ajouter jusqu'à 20 enregistrements à n'importe quel jeu d'enregistrements. Cependant, les jeux d’enregistrements de type « CNAME » peuvent contenir au plus un enregistrement, et un jeu d’enregistrements ne peut pas contenir deux enregistrements identiques. Des jeux d'enregistrements vides (avec zéro enregistrement) peuvent être créés mais ils n'apparaîtront pas sur les serveurs de nom Azure DNS.

Une fois que le jeu d’enregistrements contient la collection d’enregistrements souhaitée, vous devez le valider à l’aide de l’applet de commande `Set-AzureRmDnsRecordSet`. Une fois le jeu d’enregistrements validé, il remplace le jeu d’enregistrements existant dans Azure DNS.

### Pour créer un jeu d’enregistrements A avec un seul enregistrement

	$rs = New-AzureRmDnsRecordSet -Name "test-a" -RecordType A -Ttl 60 -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
	Add-AzureRmDnsRecordConfig -RecordSet $rs -Ipv4Address "1.2.3.4"
	Set-AzureRmDnsRecordSet -RecordSet $rs

La séquence des opérations pour créer un enregistrement peut également être *redirigée*, ce qui signifie que vous passez l’objet du jeu d’enregistrements à l’aide du canal et non en tant que paramètre. Par exemple :

	New-AzureRmDnsRecordSet -Name "test-a" -RecordType A -Ttl 60 -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup | Add-AzureRmDnsRecordConfig -Ipv4Address "1.2.3.4" | Set-AzureRmDnsRecordSet

### Autres exemples de types d’enregistrements

[AZURE.INCLUDE [dns-add-record-ps-include](../../includes/dns-add-record-ps-include.md)]

## Modifier des jeux d’enregistrements existants

Pour modifier un jeu d’enregistrements existant, vous devez suivre des étapes similaires à celles utilisées lors de la création d’enregistrements. La séquence des opérations est la suivante :

1.	Récupérez le jeu d’enregistrements existant en utilisant `Get-AzureRmDnsRecordSet`.

2.	Modifiez le jeu d’enregistrements en ajoutant des enregistrements, en supprimant des enregistrements, en modifiant des paramètres d’enregistrement ou en changeant la durée de vie du jeu d’enregistrements. Cette opération se fait hors connexion. Seul l’objet local qui représente le jeu d’enregistrements est modifié.

3.	Validez vos modifications en utilisant l’applet de commande `Set-AzureRmDnsRecordSet`. Le jeu d’enregistrement existant dans Azure DNS est alors remplacé.


### Pour mettre à jour un enregistrement dans un jeu d’enregistrements existant

Dans cet exemple, nous modifions l’adresse IP d’un enregistrement « A » existant :

	$rs = Get-AzureRmDnsRecordSet -name "test-a" -RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
	$rs.Records[0].Ipv4Address = "134.170.185.46"
	Set-AzureRmDnsRecordSet -RecordSet $rs

L’applet de commande `Set-AzureRmDnsRecordSet` utilise les vérifications etag pour éviter que des modifications simultanées ne soient remplacées. Utilisez le commutateur de commande *-Overwrite* pour supprimer ces vérifications. Pour plus d’informations, consultez la section [À propos des ETags et des étiquettes](dns-getstarted-create-dnszone.md#tagetag).

### Pour modifier un enregistrement SOA

Vous ne pouvez pas ajouter ou supprimer d’enregistrements dans le jeu d’enregistrements SOA créé automatiquement à l’extrémité de la zone (nom = « @ »). Vous pouvez cependant modifier les paramètres dans l’enregistrement SOA (à l’exception de « l’hôte ») et pendant la durée de vie du jeu d’enregistrements.

L’exemple suivant montre comment modifier la propriété *Email* de l’enregistrement SOA :

	$rs = Get-AzureRmDnsRecordSet -Name "@" -RecordType SOA -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
	$rs.Records[0].Email = "admin.contoso.com"
	Set-AzureRmDnsRecordSet -RecordSet $rs

### Pour modifier des enregistrements NS à l’apex de la zone

Vous ne pouvez pas ajouter, supprimer ou modifier les enregistrements dans le jeu d'enregistrements NS créé automatiquement à l’extrémité de la zone (nom = « @ »). La seule modification possible est la modification de la durée de vie du jeu d’enregistrements.

L'exemple suivant montre comment modifier la propriété de durée de vie du jeu d'enregistrement NS :

	$rs = Get-AzureRmDnsRecordSet -Name "@" -RecordType NS -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
	$rs.Ttl = 300
	Set-AzureRmDnsRecordSet -RecordSet $rs

### Pour ajouter des enregistrements à un jeu d’enregistrements existant

Dans cet exemple, nous ajoutons deux enregistrements MX supplémentaires au jeu d'enregistrements existant :

	$rs = Get-AzureRmDnsRecordSet -name "test-mx" -RecordType MX -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
	Add-AzureRmDnsRecordConfig -RecordSet $rs -Exchange "mail2.contoso.com" -Preference 10
	Add-AzureRmDnsRecordConfig -RecordSet $rs -Exchange "mail3.contoso.com" -Preference 20
	Set-AzureRmDnsRecordSet -RecordSet $rs

## Suppression d’un enregistrement d’un jeu d'enregistrements existant

Les enregistrements peuvent être supprimés d’un jeu d’enregistrements avec `Remove-AzureRmDnsRecordConfig`. L’enregistrement à supprimer doit correspondre exactement à un enregistrement existant relativement à tous les paramètres. Les modifications doivent être validées à l’aide de `Set-AzureRmDnsRecordSet`.

La suppression du dernier enregistrement d'un jeu d'enregistrements ne supprime pas le jeu d'enregistrements. Pour plus d’informations, consultez [Supprimer un jeu d’enregistrements](#delete-a-record-set).


	$rs = Get-AzureRmDnsRecordSet -Name "test-a" -RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
	Remove-AzureRmDnsRecordConfig -RecordSet $rs -Ipv4Address "1.2.3.4"
	Set-AzureRmDnsRecordSet -RecordSet $rs

La séquence des opérations pour supprimer un enregistrement d’un jeu d’enregistrements peut également être redirigée, ce qui signifie que vous passez l’objet du jeu d’enregistrements à l’aide du canal et non en tant que paramètre. Par exemple :

	Get-AzureRmDnsRecordSet -Name "test-a" -RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup | Remove-AzureRmDnsRecordConfig -Ipv4Address "1.2.3.4" | Set-AzureRmDnsRecordSet

### Supprimer un enregistrement AAAA d’un jeu d’enregistrements

	$rs = Get-AzureRmDnsRecordSet -Name "test-aaaa" -RecordType AAAA -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
	Remove-AzureRmDnsRecordConfig -RecordSet $rs -Ipv6Address "2607:f8b0:4009:1803::1005"
	Set-AzureRmDnsRecordSet -RecordSet $rs

### Supprimer un enregistrement CNAME d’un jeu d’enregistrements

Étant donné qu’un jeu d’enregistrements CNAME peut contenir un enregistrement au maximum, la suppression de cet enregistrement laissera un jeu d’enregistrements vide.

	$rs =  Get-AzureRmDnsRecordSet -name "test-cname" -RecordType CNAME -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
	Remove-AzureRmDnsRecordConfig -RecordSet $rs -Cname "www.contoso.com"
	Set-AzureRmDnsRecordSet -RecordSet $rs

### Supprimer un enregistrement MX d’un jeu d’enregistrements

	$rs = Get-AzureRmDnsRecordSet -name "test-mx" -RecordType MX -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
	Remove-AzureRmDnsRecordConfig -RecordSet $rs -Exchange "mail.contoso.com" -Preference 5
	Set-AzureRmDnsRecordSet -RecordSet $rs

### Supprimer un enregistrement NS d’un jeu d’enregistrements

	$rs = Get-AzureRmDnsRecordSet -Name "test-ns" -RecordType NS -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
	Remove-AzureRmDnsRecordConfig -RecordSet $rs -Nsdname "ns1.contoso.com"
	Set-AzureRmDnsRecordSet -RecordSet $rs

### Supprimer un enregistrement SRV d’un jeu d’enregistrements

	$rs = Get-AzureRmDnsRecordSet -Name "_sip._tls" -RecordType SRV -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
	Remove-AzureRmDnsRecordConfig -RecordSet $rs –Priority 0 –Weight 5 –Port 8080 –Target "sip.contoso.com"
	Set-AzureRmDnsRecordSet -RecordSet $rs

### Supprimer un enregistrement TXT d’un jeu d’enregistrements

	$rs = Get-AzureRmDnsRecordSet -Name "test-txt" -RecordType TXT -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
	Remove-AzureRmDnsRecordConfig -RecordSet $rs -Value "This is a TXT record"
	Set-AzureRmDnsRecordSet -RecordSet $rs

## Suppression d’un jeu d'enregistrements

Les jeux d’enregistrements peuvent être supprimés à l’aide de l’applet de commande `Remove-AzureRmDnsRecordSet`. Vous ne pouvez pas supprimer à l’extrémité de la zone les jeux d’enregistrements SOA et NS (nom = « @ ») qui ont été créés automatiquement lors de la création de la zone. Ils sont supprimés automatiquement lors de la suppression de la zone.

Utilisez l’une des trois méthodes suivantes pour supprimer un jeu d’enregistrements :

### Spécifier tous les paramètres par nom

Le commutateur facultatif *-Force* peut être utilisé pour supprimer l’invite de confirmation.

	Remove-AzureRmDnsRecordSet -Name "test-a" -RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup [-Force]


### Spécifier le jeu d’enregistrements par nom et par type, puis spécifier la zone par objet

	$zone = Get-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup
	Remove-AzureRmDnsRecordSet -Name "test-a" -RecordType A -Zone $zone [-Force]

### Spécifier le jeu d’enregistrements par objet

	$rs = Get-AzureRmDnsRecordSet -Name "test-a" -RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
	Remove-AzureRmDnsRecordSet –RecordSet $rs [-Overwrite] [-Force]

Lorsque vous spécifiez le jeu d’enregistrements à l’aide d’un objet, les vérifications etag sont activées pour éviter que les modifications simultanées ne soient supprimées. Le commutateur facultatif *-Overwrite* supprime ces vérifications. Pour plus d'informations, consultez [Etags et balises](dns-getstarted-create-dnszone.md#tagetag).

L'objet du jeu d'enregistrements peut également être envoyé au lieu d’être transmis en tant que paramètre :

	Get-AzureRmDnsRecordSet -Name "test-a" -RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup | Remove-AzureRmDnsRecordSet [-Overwrite] [-Force]

## Étapes suivantes

Pour plus d’informations sur Azure DNS, consultez la [Vue d’ensemble d’Azure DNS](dns-overview.md). Pour plus d’informations sur l’automatisation de DNS, consultez [Création des zones DNS et de jeux d’enregistrements à l’aide du Kit de développement logiciel (SDK) .NET](dns-sdk.md).

Pour plus d’informations sur les enregistrements DNS inversés, consultez [Gestion des enregistrements DNS inversés pour vos services à l’aide de PowerShell](dns-reverse-dns-record-operations-ps.md).

<!---HONumber=AcomDC_0824_2016-->