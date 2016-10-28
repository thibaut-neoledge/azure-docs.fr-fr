<properties 
   pageTitle="Créer des enregistrements DNS personnalisés pour une application web | Microsoft Azure " 
   description="Comment créer des enregistrements DNS de domaine personnalisés pour une application web à l’aide d’Azure DNS" 
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

# Créer des enregistrements DNS pour une application web dans un domaine personnalisé

Vous pouvez utiliser Azure DNS pour héberger un domaine personnalisé pour vos applications web. Par exemple, vous créez une application web Azure et vous voulez que vos utilisateurs y accèdent en utilisant contoso.com ou www.contoso.com comme FQDN.

Pour cela, vous devez créer deux enregistrements :

- un enregistrement « A » racine pointant vers contoso.com
- un enregistrement « CNAME » pour le nom www qui pointe vers l’enregistrement A

N'oubliez pas que si vous créez un enregistrement A pour une application web dans Azure, l'enregistrement A doit être mis à jour manuellement si l’adresse IP sous-jacente pour l'application web change.

## Avant de commencer

Avant de commencer, vous devez créer une zone DNS dans Azure DNS et déléguer cette zone de votre bureau d’enregistrement à Azure DNS.
 
1. Pour créer une zone DNS, suivez la procédure décrite dans [Créer une zone DNS](dns-getstarted-create-dnszone.md).
2. Pour déléguer votre DNS à Azure DNS, suivez la procédure décrite dans [Délégation de domaine DNS](dns-domain-delegation.md).

Après avoir créé une zone et l’avoir déléguée à Azure DNS, vous pouvez ensuite créer des enregistrements pour votre domaine personnalisé.

 
## 1\. Création d’un enregistrement A pour votre domaine personnalisé

Un enregistrement A est utilisé pour mapper un nom vers son adresse IP. Dans l'exemple suivant, nous allons attribuer @ en tant qu’enregistrement A pour une adresse IPv4 :

### Étape 1 :
 
Créez un enregistrement A et assignez-le à une variable $rs
	
	$rs= New-AzureRMDnsRecordSet -Name "@" -RecordType "A" -ZoneName "contoso.com" -ResourceGroupName "MyAzureResourceGroup" -Ttl 600 

### Étape 2 :

Ajoutez la valeur IPv4 au jeu d’enregistrements précédemment créé « @ » en utilisant la variable $rs affectée. La valeur IPv4 attribuée sera l'adresse IP de votre application web.

Pour trouver l’adresse IP d’une application web, suivez la procédure décrite dans [Configurer un nom de domaine personnalisé dans Azure App Service](../web-sites-custom-domain-name.md#Find-the-virtual-IP-address).

	Add-AzureRMDnsRecordConfig -RecordSet $rs -Ipv4Address <your web app IP address>

### Étape 3

Validez les modifications apportées au jeu d’enregistrements. Utilisez `Set-AzureRMDnsRecordSet` pour charger les modifications apportées au jeu d’enregistrements dans Azure DNS :

	Set-AzureRMDnsRecordSet -RecordSet $rs

## 2\. Créer un enregistrement CNAME pour votre domaine personnalisé

Si votre domaine est déjà géré par Azure DNS (consultez [Délégation de domaine DNS](dns-domain-delegation.md)), vous pouvez utiliser l’exemple suivant pour créer un enregistrement CNAME pour contoso.azurewebsites.net.

### Étape 1 :

Ouvrez PowerShell et créez un jeu d’enregistrements CNAME, puis affectez-le à une variable $rs. Cet exemple crée un type de jeu d’enregistrements CNAME avec une durée de vie de 600 secondes dans la zone DNS nommée « contoso.com ».

	$rs = New-AzureRMDnsRecordSet -ZoneName contoso.com -ResourceGroupName myresourcegroup -Name "www" -RecordType "CNAME" -Ttl 600
 
	Name              : www
	ZoneName          : contoso.com
	ResourceGroupName : myresourcegroup
	Ttl               : 600
	Etag              : 8baceeb9-4c2c-4608-a22c-229923ee1856
	RecordType        : CNAME
	Records           : {}
	Tags              : {}


### Étape 2 :

Une fois le jeu d'enregistrements CNAME créé, vous devez créer une valeur d'alias qui pointe vers l'application web.

À l'aide de la variable « $rs » attribuée précédemment, vous pouvez utiliser la commande PowerShell ci-dessous pour créer l'alias pour l’application web contoso.azurewebsites.net.

	Add-AzureRMDnsRecordConfig -RecordSet $rs -Cname "contoso.azurewebsites.net"
 
	Name              : www
	ZoneName          : contoso.com
	ResourceGroupName : myresourcegroup
	Ttl               : 600
	Etag              : 8baceeb9-4c2c-4608-a22c-229923ee185
	RecordType        : CNAME
	Records           : {contoso.azurewebsites.net}
	Tags              : {}

### Étape 3

Validez vos modifications en utilisant l’applet de commande `Set-AzureRMDnsRecordSet` :

	Set-AzureRMDnsRecordSet -RecordSet $rs

Vous pouvez valider l'enregistrement correctement créé en interrogeant « www.contoso.com » à l'aide de nslookup, comme indiqué ci-dessous :

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

## Créer un enregistrement « awverify » pour des applications web


Si vous décidez d'utiliser un enregistrement A pour votre application web, vous devez réaliser un processus de vérification pour confirmer que vous êtes le propriétaire du domaine personnalisé. Cette étape de vérification est effectuée en créant un enregistrement CNAME spécial nommé « awverify ». Cette section s’applique seulement aux enregistrements A.


### Étape 1 :

Créez l’enregistrement « awverify ». Dans l’exemple ci-dessous, nous créons l’enregistrement « awverify » pour contoso.com pour vérifier la propriété du domaine personnalisé.

	$rs = New-AzureRMDnsRecordSet -ZoneName contoso.com -ResourceGroupName myresourcegroup -Name "awverify" -RecordType "CNAME" -Ttl 600
 
	Name              : awverify
	ZoneName          : contoso.com
	ResourceGroupName : myresourcegroup
	Ttl               : 600
	Etag              : 8baceeb9-4c2c-4608-a22c-229923ee1856
	RecordType        : CNAME
	Records           : {}
	Tags              : {}


### Étape 2 :

Une fois le jeu d’enregistrements « awverify » créé, affectez l’alias du jeu d’enregistrements CNAME. Dans l’exemple ci-dessous, nous affectons l’alias de jeu d’enregistrements CNAME à awverify.contoso.azurewebsites.net.

	Add-AzureRMDnsRecordConfig -RecordSet $rs -Cname "awverify.contoso.azurewebsites.net"
 
	Name              : awverify
	ZoneName          : contoso.com
	ResourceGroupName : myresourcegroup
	Ttl               : 600
	Etag              : 8baceeb9-4c2c-4608-a22c-229923ee185
	RecordType        : CNAME
	Records           : {awverify.contoso.azurewebsites.net}
	Tags              : {}

### Étape 3

Validez les modifications avec `Set-AzureRMDnsRecordSet cmdlet`, comme indiqué dans la commande ci-dessous.

	Set-AzureRMDnsRecordSet -RecordSet $rs



## Étapes suivantes

Suivez la procédure décrite dans [Configuration d’un nom de domaine personnalisé pour App Service](../app-service-web/web-sites-custom-domain-name.md) pour configurer votre application web pour l’utilisation d’un domaine personnalisé.








 

<!---HONumber=AcomDC_0817_2016-->