<properties 
   pageTitle="Gestion des jeux d'enregistrements DNS et des enregistrements dans Azure DNS | Microsoft Azure" 
   description="Gestion des jeux d'enregistrements DNS et des enregistrements dans Azure DNS lorsque votre domaine est hébergé dans Azure DNS. Toutes les commandes PowerShell pour les opérations sur les jeux d'enregistrements et les enregistrements." 
   services="dns" 
   documentationCenter="na" 
   authors="joaoma" 
   manager="carmon" 
   editor=""/>

<tags
   ms.service="dns"
   ms.devlang="en"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services" 
   ms.date="03/04/2016"
   ms.author="joaoma"/>

# Gestion des enregistrements DNS à l'aide de PowerShell


> [AZURE.SELECTOR]
- [Interface de ligne de commande Azure](dns-operations-recordsets-cli.md)
- [PowerShell](dns-operations-recordsets.md)


Ce guide explique comment gérer des jeux d'enregistrements et des enregistrements pour votre zone DNS avec Azure PowerShell.

Il est important de comprendre la différence entre les jeux d'enregistrements DNS et les enregistrements DNS individuels. Un jeu d'enregistrements est une collection d'enregistrements dans une zone ayant le même nom et le même type. Pour plus d'informations, consultez [Compréhension des jeux d’enregistrements et des enregistrements](../dns-getstarted-create-recordset#Understanding-record-sets-and-records).

## Création d’un jeu d'enregistrements

Les jeux d'enregistrements sont créés à l'aide de l'applet de commande New-AzureRmDnsRecordSet. Vous devez indiquer le nom du jeu d’enregistrements, la zone, la durée de vie (TTL) et le type d'enregistrement.

Le nom du jeu d'enregistrements doit être un nom relatif, à l'exclusion du nom de la zone. Par exemple, le nom du jeu d'enregistrements « www » dans la zone « contoso.com » créera un jeu d’enregistrements avec le nom complet « www.contoso.com ».

Pour un jeu d'enregistrements à l'apex de la zone, utilisez "@" comme nom du jeu d'enregistrements, guillemets compris. Le nom complet du jeu d'enregistrements est donc égal au nom de la zone, dans cet exemple, « contoso.com ».

Azure DNS prend en charge les types d'enregistrements suivants : A, AAAA, CNAME, MX, NS, SOA, SRV, TXT. Les jeux d'enregistrements de type SOA sont créés automatiquement avec chaque zone. Ils ne peuvent pas être créés séparément.

	PS C:\> $rs = New-AzureRmDnsRecordSet -Name www -RecordType A -Ttl 300 -ZoneName contoso.com -ResouceGroupName MyAzureResouceGroup [-Tag $tags] [-Overwrite] [-Force]

Si un jeu d’enregistrements existe déjà, la commande échouera à moins que le commutateur -Overwrite ne soit utilisé. L'option « -Overwrite » déclenche une invite de confirmation, qui peut être supprimée en utilisant le commutateur -Force.

Dans l'exemple ci-dessus, la zone est spécifiée à l'aide du nom de la zone et du nom du groupe de ressources. Ou, vous pouvez spécifier un objet de la zone, tel que retourné par Get-AzureRmDnsZone ou New-AzureRmDnsZone.

	PS C:\> $zone = Get-AzureRmDnsZone -ZoneName contoso.com –ResourceGroupName MyAzureResourceGroup
	PS C:\> $rs = New-AzureRmDnsRecordSet -Name www -RecordType A -Ttl 300 –Zone $zone [-Tag $tags] [-Overwrite] [-Force]

New-AzureRmDnsRecordSet renvoie un objet local qui représente le jeu d'enregistrements créé dans Azure DNS.

>[AZURE.IMPORTANT] Les jeux d'enregistrements CNAME ne peuvent pas coexister avec d'autres jeux d'enregistrements portant le même nom. Par exemple, vous ne pouvez pas créer un CNAME avec le nom relatif « www » et un enregistrement A avec le nom relatif « www » en même temps. Étant donné que l’extrémité de la zone (nom = « @ ») contient toujours les jeux d’enregistrements NS et SOA créés lors de la création de la zone, cela signifie que vous ne pouvez pas créer un jeu d’enregistrements CNAME au niveau de l’extrémité de la zone. Ces contraintes sont dues aux normes DNS, il ne s’agit pas de limites d'Azure DNS.

### Enregistrements génériques

Azure DNS prend en charge les [enregistrements génériques](https://en.wikipedia.org/wiki/Wildcard_DNS_record). Ces derniers sont retournés pour toute requête avec un nom correspondant (à moins qu’une correspondance plus proche provienne d'un jeu d'enregistrements non génériques).

Pour créer un jeu d'enregistrements génériques, utilisez le nom de jeu d'enregistrements « * », ou un nom dont la première étiquette est « * », par exemple, « *.foo ».

Les jeux d'enregistrements génériques sont pris en charge pour tous les types d'enregistrements, hormis NS et SOA.

## Obtention d’un jeu d'enregistrements

Pour récupérer un jeu d'enregistrements existant, utilisez « Get-AzureRmDnsRecordSet » en spécifiant le nom relatif du jeu d’enregistrements, le type d'enregistrement et la zone :

	PS C:\> $rs = Get-AzureRmDnsRecordSet –Name www –RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup

Comme avec New-AzureRmDnsRecordSet, le nom de l'enregistrement doit être un nom relatif, c'est-à-dire qu'il doit être différent du nom de la zone. La zone peut être spécifiée à l'aide du nom de zone et du nom de groupe de ressources, ou à l’aide d’un objet de zone :

	PS C:\> $zone = Get-AzureRmDnsZone -Name contoso.com -ResouceGroupName MyAzureResourceGroup
	PS C:\> $rs = Get-AzureRmDnsRecordSet -Name www –RecordType A -Zone $zone
	
Get-AzureRmDnsRecordSet renvoie un objet local qui représente le jeu d'enregistrements créé dans Azure DNS.

## Liste des jeux d'enregistrements
En omettant les paramètres –Name et/ou –RecordType, Get-AzureRmDnsRecordSet peut également être utilisé pour répertorier des jeux d'enregistrements :

### Option 1 : 

Liste de tous les jeux d'enregistrements. Ceci renverra tous les jeux d'enregistrements, quel que soit le nom ou le type d'enregistrement :

	PS C:\> $list = Get-AzureRmDnsRecordSet -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup

### Option 2 : 

Liste des jeux d'enregistrements pour un type d'enregistrement donné. Ceci renverra tous les jeux d'enregistrements correspondant au type d'enregistrement donné (dans ce cas, les enregistrements A) :

	PS C:\> $list = Get-AzureRmDnsRecordSet –RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup 

Dans les deux cas mentionnés ci-dessus, la zone peut être spécifiée à l'aide des paramètres – ZoneName et –ResourceGroupName (comme indiqué) ou en spécifiant un objet de zone :

	PS C:\> $zone = Get-AzureRmDnsZone -Name contoso.com -ResouceGroupName MyAzureResourceGroup
	PS C:\> $list = Get-AzureRmDnsRecordSet -Zone $zone

## Ajout d’un enregistrement à un jeu d'enregistrements

Les enregistrements sont ajoutés à des jeux d'enregistrements à l'aide de l'applet de commande Add-AzureRmDnsRecordConfig. Il s'agit d'une opération hors connexion. Seul l'objet local qui représente le jeu d'enregistrements est modifié.

Les paramètres pour ajouter des enregistrements à un jeu d'enregistrements varient selon le type de jeu d'enregistrements. Par exemple, lors de l'utilisation d'un jeu d'enregistrements de type « A », vous ne pourrez spécifier que les enregistrements avec le paramètre « IPv4Address ».

Des enregistrements supplémentaires peuvent être ajoutés à chaque jeu d’enregistrements grâce à des appels supplémentaires vers Add-AzureRmDnsRecordConfig. Vous pouvez ajouter jusqu'à 20 enregistrements à n'importe quel jeu d'enregistrements. Toutefois, les jeux d'enregistrements de type CNAME peuvent contenir 1 enregistrement au maximum et un jeu d'enregistrements ne peut pas contenir deux enregistrements identiques. Des jeux d'enregistrements vides (avec zéro enregistrement) peuvent être créés mais ils n'apparaîtront pas sur les serveurs de nom Azure DNS.

Une fois que le jeu d'enregistrements contient la collection d'enregistrements souhaitée, il doit être validé à l'aide de l'applet de commande Set-AzureRmDnsRecordSet qui remplace le jeu d'enregistrements existant dans Azure DNS par le jeu d'enregistrements fourni. Les exemples suivants montrent comment créer un jeu d'enregistrements de chaque type d'enregistrement contenant un seul enregistrement.

### Création d’un jeu d’enregistrements avec un seul enregistrement

	PS C:\> $rs = New-AzureRmDnsRecordSet -Name "test-a" -RecordType A -Ttl 60 -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup 
	PS C:\> Add-AzureRmDnsRecordConfig -RecordSet $rs -Ipv4Address "1.2.3.4"
	PS C:\> Set-AzureRmDnsRecordSet -RecordSet $rs

La séquence des opérations pour créer un enregistrement peut également être « envoyée », en passant l’objet du jeu d'enregistrements à l'aide du canal et non en tant que paramètre. Par exemple :

	PS C:\> New-AzureRmDnsRecordSet -Name "test-a" -RecordType A -Ttl 60 -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup | Add-AzureRmDnsRecordConfig -Ipv4Address "1.2.3.4" | Set-AzureRmDnsRecordSet

### Création d’un jeu d’enregistrements AAAA avec un seul enregistrement

	PS C:\> $rs = New-AzureRmDnsRecordSet -Name "test-aaaa" -RecordType AAAA -Ttl 60 -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
	PS C:\> Add-AzureRmDnsRecordConfig -RecordSet $rs -Ipv6Address "2607:f8b0:4009:1803::1005"
	PS C:\> Set-AzureRmDnsRecordSet -RecordSet $rs

### Création d’un jeu d’enregistrements CNAME avec un seul enregistrement

	PS C:\> $rs = New-AzureRmDnsRecordSet -Name "test-cname" -RecordType CNAME -Ttl 60 -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
	PS C:\> Add-AzureRmDnsRecordConfig -RecordSet $rs -Cname "www.contoso.com"
	PS C:\> Set-AzureRmDnsRecordSet -RecordSet $rs

### Création d’un jeu d’enregistrements MX avec un seul enregistrement

Dans cet exemple, nous utilisons le nom de jeu d'enregistrements "@" pour créer l'enregistrement MX à l'apex de la zone (par exemple, « contoso.com »). Cela est courant pour les enregistrements MX.

	PS C:\> $rs = New-AzureRmDnsRecordSet -Name "@" -RecordType MX -Ttl 60 -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
	PS C:\> Add-AzureRmDnsRecordConfig -RecordSet $rs -Exchange "mail.contoso.com" -Preference 5
	PS C:\> Set-AzureRmDnsRecordSet -RecordSet $rs

### Création d’un jeu d’enregistrements NS avec un seul enregistrement

	PS C:\> $rs = New-AzureRmDnsRecordSet -Name "test-ns" -RecordType NS -Ttl 60 -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
	PS C:\> Add-AzureRmDnsRecordConfig -RecordSet $rs -Nsdname "ns1.contoso.com"
	PS C:\> Set-AzureRmDnsRecordSet -RecordSet $rs

### Création d’un jeu d’enregistrements SRV avec un seul enregistrement

Si vous créez un enregistrement SRV à la racine de la zone, indiquez simplement le \_service et le \_protocol dans le nom de l'enregistrement. Il est inutile d'inclure également « .@ » dans le nom de l'enregistrement

	PS C:\> $rs = New-AzureRmDnsRecordSet -Name "_sip._tls" -RecordType SRV -Ttl 60 -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
	PS C:\> Add-AzureRmDnsRecordConfig -RecordSet $rs –Priority 0 –Weight 5 –Port 8080 –Target "sip.contoso.com"
	PS C:\> Set-AzureRmDnsRecordSet -RecordSet $rs

### Création d’un jeu d’enregistrements TXT avec un seul enregistrement

	PS C:\> $rs = New-AzureRmDnsRecordSet -Name "test-txt" -RecordType TXT -Ttl 60 -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
	PS C:\> Add-AzureRmDnsRecordConfig -RecordSet $rs -Value "This is a TXT record"
	PS C:\> Set-AzureRmDnsRecordSet -RecordSet $rs

## Modification des jeux d'enregistrements existants

La modification des jeux d'enregistrements existants suit le même procédé que pour la création d'enregistrements. La séquence des opérations est la suivante :

1.	Récupérez le jeu d'enregistrements existant à l'aide de Get-AzureRmDnsRecordSet.
2.	Modifiez le jeu d'enregistrements en ajoutant les enregistrements, en supprimant des enregistrements, en modifiant des paramètres d'enregistrement ou en modifiant la durée de vie du jeu d'enregistrements. Ces modifications s’effectuent hors connexion. Seul l'objet local qui représente le jeu d'enregistrements est modifié.
3.	Validez vos modifications à l'aide de l'applet de commande Set-AzureRmDnsRecordSet. Cela remplace le jeu d'enregistrement existant dans Azure DNS avec le jeu d'enregistrements fourni.

Ceci est illustré dans les exemples suivants :

### Mise à jour d’un enregistrement dans un jeu d'enregistrements existant

Pour cet exemple, nous allons modifier l'adresse IP d'un enregistrement A existant :

	PS C:\> $rs = Get-AzureRmDnsRecordSet -name "test-a" -RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
	PS C:\> $rs.Records[0].Ipv4Address = "134.170.185.46"
	PS C:\> Set-AzureRmDnsRecordSet -RecordSet $rs 

L'applet de commande Set-AzureRmDnsRecordSet utilise des vérifications « etag » pour s'assurer que des modifications simultanées ne sont pas remplacées. Utilisez l’indicateur « -Overwrite » pour supprimer ces vérifications. Pour plus d'informations, consultez Etags et balises.

### Modification d'enregistrement SOA

>[AZURE.NOTE] Vous ne pouvez pas ajouter ou supprimer des enregistrements du jeu d'enregistrements SOA créé automatiquement dans l’extrémité de la zone (nom = « @ »), mais vous pouvez modifier les paramètres dans l'enregistrement SOA (excepté « Host ») et la durée de vie du jeu d'enregistrements.

L'exemple suivant montre comment modifier la propriété « Email » de l'enregistrement SOA :

	PS C:\> $rs = Get-AzureRmDnsRecordSet -Name "@" -RecordType SOA -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
	PS C:\> $rs.Records[0].Email = "admin.contoso.com"
	PS C:\> Set-AzureRmDnsRecordSet -RecordSet $rs 

### Modification des enregistrements NS à l’extrémité de la zone

>[AZURE.NOTE] Vous ne pouvez pas ajouter, supprimer ou modifier les enregistrements dans le jeu d'enregistrements NS créé automatiquement à l’extrémité de la zone (nom = « @ »). La seule modification possible est la modification de la durée de vie du jeu d'enregistrements.

L'exemple suivant montre comment modifier la propriété de durée de vie du jeu d'enregistrement NS :

	PS C:\> $rs = Get-AzureRmDnsRecordSet -Name "@" -RecordType NS -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
	PS C:\> $rs.Ttl = 300
	PS C:\> Set-AzureRmDnsRecordSet -RecordSet $rs 

### Ajout d’enregistrements à un jeu d'enregistrements existant

Dans cet exemple, nous ajoutons deux enregistrements MX supplémentaires au jeu d'enregistrements existant :

	PS C:\> $rs = Get-AzureRmDnsRecordSet -name "test-mx" -RecordType MX -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
	PS C:\> Add-AzureRmDnsRecordConfig -RecordSet $rs -Exchange "mail2.contoso.com" -Preference 10
	PS C:\> Add-AzureRmDnsRecordConfig -RecordSet $rs -Exchange "mail3.contoso.com" -Preference 20
	PS C:\> Set-AzureRmDnsRecordSet -RecordSet $rs 

## Suppression d’un enregistrement d’un jeu d'enregistrements existant

Des enregistrements peuvent être supprimés d'un jeu d’enregistrements à l'aide de Remove-AzureRmDnsRecordConfig. Notez que l'enregistrement qui est supprimé doit correspondre précisément avec un enregistrement existant, sur tous les paramètres. Les modifications doivent être validées à l'aide de Set-AzureRmDnsRecordSet.

La suppression du dernier enregistrement d'un jeu d'enregistrements ne supprime pas le jeu d'enregistrements. Consultez [Suppression d’un jeu d'enregistrements](#delete-a-record-set) ci-dessous pour en savoir plus.


	PS C:\> $rs = Get-AzureRmDnsRecordSet -Name "test-a" -RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
	PS C:\> Remove-AzureRmDnsRecordConfig -RecordSet $rs -Ipv4Address "1.2.3.4"
	PS C:\> Set-AzureRmDnsRecordSet -RecordSet $rs

La séquence des opérations pour supprimer un enregistrement d’un jeu d’enregistrements peut également être « envoyée » en transmettant l’objet du jeu d’enregistrements à l'aide du canal et non en tant que paramètre. Par exemple :

	PS C:\> Get-AzureRmDnsRecordSet -Name "test-a" -RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup | Remove-AzureRmDnsRecordConfig -Ipv4Address "1.2.3.4" | Set-AzureRmDnsRecordSet

### Suppression de l'enregistrement AAAA d'un jeu d'enregistrements

	PS C:\> $rs = Get-AzureRmDnsRecordSet -Name "test-aaaa" -RecordType AAAA -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
	PS C:\> Remove-AzureRmDnsRecordConfig -RecordSet $rs -Ipv6Address "2607:f8b0:4009:1803::1005"
	PS C:\> Set-AzureRmDnsRecordSet -RecordSet $rs

### Suppression de l'enregistrement CNAME d'un jeu d'enregistrements

Étant donné qu’un jeu d'enregistrements CNAME peut contenir un enregistrement au maximum, la suppression de cet enregistrement laissera un jeu d'enregistrements vide.

	PS C:\> $rs =  Get-AzureRmDnsRecordSet -name "test-cname" -RecordType CNAME -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup	
	PS C:\> Remove-AzureRmDnsRecordConfig -RecordSet $rs -Cname "www.contoso.com"
	PS C:\> Set-AzureRmDnsRecordSet -RecordSet $rs

### Suppression de l'enregistrement MX d'un jeu d'enregistrements

	PS C:\> $rs = Get-AzureRmDnsRecordSet -name "test-mx" -RecordType MX -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup	
	PS C:\> Remove-AzureRmDnsRecordConfig -RecordSet $rs -Exchange "mail.contoso.com" -Preference 5
	PS C:\> Set-AzureRmDnsRecordSet -RecordSet $rs

### Suppression de l’enregistrement NS du jeu d'enregistrements
	
	PS C:\> $rs = Get-AzureRmDnsRecordSet -Name "test-ns" -RecordType NS -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
	PS C:\> Remove-AzureRmDnsRecordConfig -RecordSet $rs -Nsdname "ns1.contoso.com"
	PS C:\> Set-AzureRmDnsRecordSet -RecordSet $rs

### Suppression de l'enregistrement SRV d'un jeu d'enregistrements

	PS C:\> $rs = Get-AzureRmDnsRecordSet -Name "_sip._tls" -RecordType SRV -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
	PS C:\> Remove-AzureRmDnsRecordConfig -RecordSet $rs –Priority 0 –Weight 5 –Port 8080 –Target "sip.contoso.com"
	PS C:\> Set-AzureRmDnsRecordSet -RecordSet $rs

### Suppression de l'enregistrement TXT d'un jeu d'enregistrements

	PS C:\> $rs = Get-AzureRmDnsRecordSet -Name "test-txt" -RecordType TXT -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
	PS C:\> Remove-AzureRmDnsRecordConfig -RecordSet $rs -Value "This is a TXT record"
	PS C:\> Set-AzureRmDnsRecordSet -RecordSet $rs

## Suppression d’un jeu d'enregistrements
Les jeux d'enregistrements peuvent être supprimés à l'aide de l'applet de commande Remove-AzureRmDnsRecordSet.

>[AZURE.NOTE] Vous ne pouvez pas supprimer les jeux d’enregistrements SOA et NS à l’extrémité de la zone (nom = « @ ») qui sont créés automatiquement lorsque la zone est créée. Ils seront automatiquement supprimés lors de la suppression de la zone.

Utilisez l'une des trois méthodes suivantes pour supprimer un jeu d’enregistrements :

### Option 1 :

Spécifiez tous les paramètres par nom :

	PS C:\> Remove-AzureRmDnsRecordSet -Name "test-a" -RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup [-Force]

Le commutateur facultatif « -Force » peut être utilisé pour supprimer l'invite de confirmation.

### Option 2 :

Spécifiez le jeu d'enregistrements par nom et par type puis spécifiez la zone par objet :

	PS C:\> $zone = Get-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup
	PS C:\> Remove-AzureRmDnsRecordSet -Name "test-a" -RecordType A -Zone $zone [-Force]

### Option 3 :

Spécifiez le jeu d'enregistrements par objet :

	PS C:\> $rs = Get-AzureRmDnsRecordSet -Name "test-a" -RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
	PS C:\> Remove-AzureRmDnsRecordSet –RecordSet $rs [-Overwrite] [-Force]

La spécification du jeu d'enregistrement à l'aide d'un objet permet aux vérifications « etag » de s’assurer que les modifications simultanées ne sont pas supprimées. L'indicateur facultatif « -Overwrite » supprime ces vérifications. Pour plus d'informations, consultez [Etags et balises](../dns-getstarted-create-dnszone#Etags-and-tags).

L'objet du jeu d'enregistrements peut également être envoyé au lieu d’être transmis en tant que paramètre :

	PS C:\> Get-AzureRmDnsRecordSet -Name "test-a" -RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup | Remove-AzureRmDnsRecordSet [-Overwrite] [-Force]

##Voir aussi

[Prise en main de la création de jeux d'enregistrements et des enregistrements](dns-getstarted-create-recordset.md)<BR> [Réalisation d'opérations sur des zones DNS](dns-operations-dnszones.md)<BR> [Automatisation d'opérations à l'aide du Kit de développement (SDK) .NET](dns-sdk.md)
 

<!---------HONumber=AcomDC_0309_2016-->