<properties
   pageTitle="Création d’un jeu d’enregistrements et d’enregistrements pour une zone DNS | Microsoft Azure"
   description="Création d’enregistrements hôtes pour Azure DNS. Configuration d’enregistrements et de jeux d’enregistrements à l’aide de PowerShell"
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
   ms.date="05/01/2015"
   ms.author="joaoma"/>


# Création d’enregistrements DNS

Après avoir créé votre zone DNS, vous devez ajouter les enregistrements DNS de votre domaine. Pour ce faire, vous devez d’abord comprendre les enregistrements DNS et les jeux d’enregistrements.


## Compréhension des jeux d’enregistrements et des enregistrements.
Chaque enregistrement DNS a un nom et un type.

Le nom _complet_ inclut le nom de la zone, contrairement au nom _relatif_. Par exemple, le nom d’enregistrement relatif « www » dans la zone « contoso.com » crée le nom d’enregistrement complet « www.contoso.com ».

>[AZURE.NOTE]Dans Azure DNS, les enregistrements sont spécifiés à l’aide de noms relatifs.

Il existe différents types d’enregistrement, selon les données qu’ils contiennent. Le type le plus courant est un enregistrement « A » qui associe un nom à une adresse IPv4. Un autre type est un enregistrement « MX », qui associe un nom à un serveur de messagerie.

Azure DNS prend en charge tous les types d’enregistrement DNS courants : A, AAAA, CNAME, MX, NS, SOA, SRV et TXT.

Vous devez parfois créer plusieurs enregistrements DNS avec un nom et un type donnés. Par exemple, supposons que le site web www.contoso.com est hébergé sur des adresses IP différentes. Cela requiert deux enregistrements A, un pour chaque adresse IP :

	www.contoso.com.		3600	IN	A	134.170.185.46
	www.contoso.com.		3600	IN	A	134.170.188.221

Il s’agit d’un exemple de jeu d’enregistrements. Un jeu d’enregistrements est une collection d’enregistrements DNS dans une zone ayant le même nom et le même type. La plupart des jeux d’enregistrements contiennent un enregistrement unique, mais les exemples comme celui ci-dessus dans lequel un jeu d’enregistrements contient plusieurs enregistrements sont relativement courants. Les enregistrements de type SOA et CNAME constituent une exception, les normes DNS n’autorisant pas plusieurs enregistrements avec le même nom pour ces types.

La durée de vie (TTL) spécifie la durée pendant laquelle chaque enregistrement est mis en cache par les clients avant d’être réinterrogé. Dans l’exemple ci-dessus, la durée de vie est de 3 600 secondes ou 1 heure. La durée de vie est spécifiée pour le jeu d’enregistrements, pas pour chaque enregistrement. La même valeur est donc utilisée pour tous les enregistrements au sein de ce jeu d’enregistrements.

>[AZURE.NOTE]Azure DNS gère les enregistrements DNS à l’aide de jeux d’enregistrements.



## Création de jeux d’enregistrements et d’enregistrements

Dans l'exemple suivant, nous allons montrer comment créer un jeu d'enregistrements et des enregistrements. Nous allons utiliser le type d'enregistrement DNS « A », pour d'autres types d'enregistrements, consultez [Gestion des enregistrements DNS](dns-operations-recordsets.md)


### Étape 1 :

Créez un jeu d'enregistrements et assignez-le à une variable $rs :

	PS C:\>$rs = New-AzureDnsRecordSet -Name "www" -RecordType "A" -ZoneName "contoso.com" -ResourceGroupName "MyAzureResourceGroup" -Ttl 60

Le jeu d’enregistrements ayant le nom relatif « www » dans la zone DNS « contoso.com », le nom complet des enregistrements est « www.contoso.com ». Le type d’enregistrement est « A » et la durée de vie est de 60 secondes.

>[AZURE.NOTE]Pour créer un jeu d'enregistrements à l'apex de la zone (dans cet exemple, « contoso.com »), utilisez le nom d'enregistrement « "@" » (guillemets compris). Il s'agit d'une convention DNS courante.

Le jeu d'enregistrements est vide et vous devez ajouter des enregistrements pour pouvoir utiliser le jeu d'enregistrements « www » que vous venez de créer.<BR>

### Étape 2 :

Ajoutez des enregistrements IPv4 A au jeu d’enregistrements « www » à l’aide de la variable $rs attribuée lors de la création du jeu d’enregistrements à l’étape 1 :

	PS C:\> Add-AzureDnsRecordConfig -RecordSet $rs -Ipv4Address 134.170.185.46
	PS C:\> Add-AzureDnsRecordConfig -RecordSet $rs -Ipv4Address 134.170.188.221

L’ajout d’enregistrements à un jeu d’enregistrements à l’aide de Add-AzureDnsRecordConfig est une opération hors connexion. Seule la variable locale $rs est mise à jour.

### Étape 3
Validez les modifications apportées au jeu d’enregistrements. Utilisez Set-AzureDnsRecordSet pour télécharger les modifications apportées au jeu d’enregistrements dans Azure DNS :


	Set-AzureDnsRecordSet -RecordSet $rs

Les modifications sont terminées. Vous pouvez récupérer l’enregistrement à partir d’Azure DNS à l’aide de Get-AzureDnsRecordSet :


	PS C:\> Get-AzureDnsRecordSet –Name www –RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup


	Name              : www
	ZoneName          : contoso.com
	ResourceGroupName : MyAzureResourceGroup
	Ttl               : 3600
	Etag              : 68e78da2-4d74-413e-8c3d-331ca48246d9
	RecordType        : A
	Records           : {134.170.185.46, 134.170.188.221}
	Tags              : {}



Vous pouvez également utiliser nslookup ou d’autres outils DNS pour interroger le nouveau jeu d’enregistrements.

>[AZURE.NOTE]Comme lors de la création de la zone, si vous n’avez pas encore délégué le domaine aux serveurs de noms Azure DNS, vous devez spécifier l’adresse du serveur de noms de votre zone explicitement.


	C:\> nslookup www.contoso.com ns1-01.azure-dns.com

	Server: ns1-01.azure-dns.com
	Address:  208.76.47.1

	Name:    www.contoso.com
	Addresses:  134.170.185.46
    	        134.170.188.221



## Étapes suivantes
[Gestion des zones DNS](dns-operations-dnszones.md)

[Gestion des enregistrements DNS](dns-operations-recordsets.md)<BR>

[Automatisation des opérations Azure avec le Kit de développement (SDK) .NET](dns-sdk.md)
 

<!---HONumber=August15_HO6-->