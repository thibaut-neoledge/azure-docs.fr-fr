<properties 
   pageTitle="Création d’enregistrements DNS personnalisés pour une application web | Microsoft Azure" 
   description="Création d’enregistrements DNS de domaine personnalisés pour une application web à l’aide d’Azure DNS. Vérification étape par étape de votre propriété de domaine à l'aide de CNAME ou de l’enregistrement A" 
   services="dns" 
   documentationCenter="na" 
   authors="joaoma" 
   manager="carmonm" 
   editor=""/>

<tags
   ms.service="dns"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services" 
   ms.date="03/03/2016"
   ms.author="joaoma"/>

# Création d’enregistrements DNS pour des applications web dans un domaine personnalisé

Vous pouvez utiliser Azure DNS pour héberger un domaine personnalisé pour vos applications web. Par exemple, imaginez que vous créez une application web Azure et que vous souhaitez que vos utilisateurs y accèdent à l'aide de contoso.com ou de www.contoso.com en tant que FQDN. Dans ce cas, vous devrez créer deux enregistrements : un enregistrement racine A pointant vers contoso.com et un enregistrement CNAME pour le nom www pointant vers l'enregistrement A.

> [AZURE.NOTE] N'oubliez pas que si vous créez un enregistrement A pour une application web dans Azure, l'enregistrement A doit être mis à jour manuellement si l’adresse IP sous-jacente pour l'application web change.

Avant de pouvoir créer des enregistrements pour votre domaine personnalisé, vous devez créer une zone DNS dans Azure DNS et déléguer cette zone de votre bureau d'enregistrement dans Azure DNS. Pour créer une zone DNS, suivez les étapes dans [Prise en main d’Azure DNS](../dns-getstarted-create-dnszone/#Create-a-DNS-zone). Pour déléguer votre DNS dans Azure DNS, suivez les étapes dans [Délégation de domaine dans Azure DNS](../dns-domain-delegation).
 
## Création d’un enregistrement A pour votre domaine personnalisé

Un enregistrement A est utilisé pour mapper un nom vers son adresse IP. Dans l'exemple suivant, nous allons attribuer @ en tant qu’enregistrement A pour une adresse IPv4 :

### Étape 1 :
 
Créez un enregistrement A et assignez-le à une variable $rs
	
	PS C:\>$rs= New-AzureRMDnsRecordSet -Name "@" -RecordType "A" -ZoneName "contoso.com" -ResourceGroupName "MyAzureResourceGroup" -Ttl 600 

### Étape 2 :

Ajoutez la valeur IPv4 à un jeu d’enregistrements précédemment créé « @ » à l'aide de la variable $rs attribuée. La valeur IPv4 attribuée sera l'adresse IP de votre application web.

> [AZURE.NOTE] Pour rechercher l'adresse IP pour une application web, suivez les étapes dans [Configuration d’un nom de domaine personnalisé dans Azure App Service](../web-sites-custom-domain-name/#Find-the-virtual-IP-address)

	PS C:\> Add-AzureRMDnsRecordConfig -RecordSet $rs -Ipv4Address <your web app IP address>

### Étape 3 :

Validez les modifications apportées au jeu d’enregistrements. Utilisez Set-AzureRMDnsRecordSet pour charger les modifications apportées au jeu d'enregistrements dans Azure DNS :

	Set-AzureRMDnsRecordSet -RecordSet $rs

## Ajout d’un enregistrement CNAME pour votre domaine personnalisé

En supposant que votre domaine est déjà géré par Azure DNS (consultez [Délégation de domaine DNS](../dns-domain-delegation)), vous pouvez utiliser l'exemple suivant pour créer un enregistrement CNAME pour contoso.azurewebsites.net :

### Étape 1 :

Ouvrez Powershell et créez un nouveau jeu d'enregistrements CNAME puis affectez-le à une variable $rs :

	PS C:\> $rs = New-AzureRMDnsRecordSet -ZoneName contoso.com -ResourceGroupName myresourcegroup -Name "www" -RecordType "CNAME" -Ttl 600
 
	Name              : www
	ZoneName          : contoso.com
	ResourceGroupName : myresourcegroup
	Ttl               : 600
	Etag              : 8baceeb9-4c2c-4608-a22c-229923ee1856
	RecordType        : CNAME
	Records           : {}
	Tags              : {}

Cela créera un type de jeu d'enregistrements CNAME avec une « durée de vie » de 600 secondes dans la zone DNS nommée « contoso.com ».

### Étape 2 :

Une fois le jeu d'enregistrements CNAME créé, vous devez créer une valeur d'alias qui pointe vers l'application web.

À l'aide de la variable « $rs » attribuée précédemment, vous pouvez utiliser la commande PowerShell ci-dessous pour créer l'alias pour l’application web contoso.azurewebsites.net.

	PS C:\> Add-AzureRMDnsRecordConfig -RecordSet $rs -Cname "contoso.azurewebsites.net"
 
	Name              : www
	ZoneName          : contoso.com
	ResourceGroupName : myresourcegroup
	Ttl               : 600
	Etag              : 8baceeb9-4c2c-4608-a22c-229923ee185
	RecordType        : CNAME
	Records           : {contoso.azurewebsites.net}
	Tags              : {}

### Étape 3

Validez vos modifications à l'aide de l'applet de commande Set-AzureRMDnsRecordSet :

	PS C:\>Set-AzureRMDnsRecordSet -RecordSet $rs

Vous pouvez valider l'enregistrement correctement créé en interrogeant « www.contoso.com » à l'aide de nslookup, comme indiqué ci-dessous :

	PS C:\> nslookup
	Default Server:  Default
	Address:  192.168.0.1
 
	> www.contoso.com
	Server:  default server
	Address:  192.168.0.1
	 
	Non-authoritative answer:
	Name:    <instance of web app service>.cloudapp.net
	Address:  <ip of web app service>
	Aliases:  www.contoso.com
    contoso.azurewebsites.net
    <instance of web app service>.vip.azurewebsites.windows.net

## Création d’un enregistrement awverify pour les applications web (enregistrements A uniquement)

Si vous décidez d'utiliser un enregistrement A pour votre application web, vous devez réaliser un processus de vérification pour confirmer que vous êtes le propriétaire du domaine personnalisé. Cette étape de vérification est effectuée en créant un enregistrement CNAME spécial nommé « awverify ».

Dans l'exemple ci-dessous, l'enregistrement « awverify » sera créé pour contoso.com vérifier la propriété du domaine personnalisé :

### Étape 1 :

	PS C:\> $rs = New-AzureRMDnsRecordSet -ZoneName contoso.com -ResourceGroupName myresourcegroup -Name "awverify" -RecordType "CNAME" -Ttl 600
 
	Name              : awverify
	ZoneName          : contoso.com
	ResourceGroupName : myresourcegroup
	Ttl               : 600
	Etag              : 8baceeb9-4c2c-4608-a22c-229923ee1856
	RecordType        : CNAME
	Records           : {}
	Tags              : {}


### Étape 2 :

Une fois le jeu d'enregistrements awverify créé, vous devez attribuer l'alias du jeu d’enregistrements CNAME à awverify.contoso.azurewebsites.net, comme indiqué dans la commande ci-dessous :

	PS C:\> Add-AzureRMDnsRecordConfig -RecordSet $rs -Cname "awverify.contoso.azurewebsites.net"
 
	Name              : awverify
	ZoneName          : contoso.com
	ResourceGroupName : myresourcegroup
	Ttl               : 600
	Etag              : 8baceeb9-4c2c-4608-a22c-229923ee185
	RecordType        : CNAME
	Records           : {awverify.contoso.azurewebsites.net}
	Tags              : {}

### Étape 3

Validez vos modifications à l'aide de l'applet de commande Set-AzureRMDnsRecordSet, comme indiqué dans la commande ci-dessous :

	PS C:\>Set-AzureRMDnsRecordSet -RecordSet $rs

Maintenant, vous pouvez continuer à suivre les étapes dans [Configuration d'un nom de domaine personnalisé pour App Service](../web-sites-custom-domain-name) afin de configurer votre application web pour utiliser un domaine personnalisé.

## Voir aussi

[Gestion des zones DNS](../dns-operations-dnszones)

[Gestion des enregistrements DNS](../dns-operations-recordsets)

[Vue d’ensemble de Traffic Manager](../traffic-manager-overview)

[Automatisation des opérations Azure avec le Kit de développement (SDK) .NET](../dns-sdk)


 

<!---HONumber=AcomDC_0309_2016-->