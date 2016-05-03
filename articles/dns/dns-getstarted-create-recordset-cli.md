<properties
   pageTitle="Création d’un jeu d’enregistrements et d’enregistrements pour une zone DNS à l’aide de l’interface de ligne de commande | Microsoft Azure"
   description="Création d’enregistrements hôtes pour Azure DNS. Configuration d’enregistrements et de jeux d’enregistrements à l’aide de l’interface de ligne de commande"
   services="dns"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""/>

<tags
   ms.service="dns"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="01/20/2016"
   ms.author="cherylmc"/>


# Création d’enregistrements DNS à l’aide de l’interface de ligne de commande

> [AZURE.SELECTOR]
- [Portail Azure](dns-getstarted-create-recordset-portal.md)
- [PowerShell](dns-getstarted-create-recordset.md)
- [Interface de ligne de commande Azure](dns-getstarted-create-recordset-cli.md)

Après avoir créé votre zone DNS, vous devez ajouter les enregistrements DNS de votre domaine. Pour ce faire, vous devez d’abord comprendre les enregistrements DNS et les jeux d’enregistrements.


## Compréhension des jeux d’enregistrements et des enregistrements.
Chaque enregistrement DNS a un nom et un type.

Le nom _complet_ inclut le nom de la zone, contrairement au nom _relatif_. Par exemple, le nom d’enregistrement relatif « www » dans la zone « contoso.com » crée le nom d’enregistrement complet « www.contoso.com ».

>[AZURE.NOTE] Dans Azure DNS, les enregistrements sont spécifiés à l’aide de noms relatifs.

Il existe différents types d’enregistrement, selon les données qu’ils contiennent. Le type le plus courant est un enregistrement « A » qui associe un nom à une adresse IPv4. Un autre type est un enregistrement « MX », qui associe un nom à un serveur de messagerie.

Azure DNS prend en charge tous les types d’enregistrement DNS courants : A, AAAA, CNAME, MX, NS, SOA, SRV et TXT. (Notez que les [enregistrements SPF doivent être créés à l’aide du type d’enregistrement TXT](http://tools.ietf.org/html/rfc7208#section-3.1).)

Vous devez parfois créer plusieurs enregistrements DNS avec un nom et un type donnés. Par exemple, supposons que le site web www.contoso.com est hébergé sur des adresses IP différentes. Cela requiert deux enregistrements A, un pour chaque adresse IP :

	www.contoso.com.		3600	IN	A	134.170.185.46
	www.contoso.com.		3600	IN	A	134.170.188.221

Il s’agit d’un exemple de jeu d’enregistrements. Un jeu d’enregistrements est une collection d’enregistrements DNS dans une zone ayant le même nom et le même type. La plupart des jeux d’enregistrements contiennent un enregistrement unique, mais les exemples comme celui ci-dessus dans lequel un jeu d’enregistrements contient plusieurs enregistrements sont relativement courants. Les enregistrements de type SOA et CNAME constituent une exception, les normes DNS n’autorisant pas plusieurs enregistrements avec le même nom pour ces types.

La durée de vie (TTL) spécifie la durée pendant laquelle chaque enregistrement est mis en cache par les clients avant d’être réinterrogé. Dans l’exemple ci-dessus, la durée de vie est de 3 600 secondes ou 1 heure. La durée de vie est spécifiée pour le jeu d’enregistrements, pas pour chaque enregistrement. La même valeur est donc utilisée pour tous les enregistrements au sein de ce jeu d’enregistrements.

>[AZURE.NOTE] Azure DNS gère les enregistrements DNS à l’aide de jeux d’enregistrements.



## Création de jeux d’enregistrements et d’enregistrements 

Dans l'exemple suivant, nous allons montrer comment créer un jeu d'enregistrements et des enregistrements. Nous allons utiliser le type d'enregistrement DNS « A », pour d'autres types d'enregistrements, consultez [Gestion des enregistrements DNS](dns-operations-recordsets-cli.md)


### Étape 1

Création d’un jeu d'enregistrements :

	Usage: network dns record-set create <resource-group> <dns-zone-name> <name> <type> <ttl>

	azure network dns record-set create myresourcegroup  contoso.com  www A  60

Le jeu d’enregistrements ayant le nom relatif « www » dans la zone DNS « contoso.com », le nom complet des enregistrements est « www.contoso.com ». Le type d’enregistrement est « A » et la durée de vie est de 60 secondes.

>[AZURE.NOTE] Pour créer un jeu d'enregistrements à l'apex de la zone (dans cet exemple, « contoso.com »), utilisez le nom d'enregistrement « "@" » (guillemets compris). Il s'agit d'une convention DNS courante.

Le jeu d'enregistrements est vide et vous devez ajouter des enregistrements pour pouvoir utiliser le jeu d'enregistrements « www » que vous venez de créer.<BR>

### Étape 2

Ajoutez les enregistrements IPv4 A au jeu d’enregistrements « www » à l'aide de la commande suivante :

	Usage: network dns record-set add-record <resource-group> <dns-zone-name> <record-set-name> <type>

	azure network dns record-set add-record myresourcegroup contoso.com  www A  -a 134.170.185.46
	

Les modifications sont terminées. Vous pouvez récupérer le jeu d’enregistrements à partir d’Azure DNS à l’aide de « azure network dns-record-set show »


	azure network dns record-set show myresourcegroup "contoso.com" www A
	
	info:    Executing command network dns-record-set show
	+ Looking up the DNS record set "www"
	data:    Id                              : /subscriptions/########################/resourceGroups/myresourcegroup/providers/Microsoft.Network/dnszones/contoso.com/A/www
	data:    Name                            : www
	data:    Type                            : Microsoft.Network/dnszones/A
	data:    Location                        : global
	data:    TTL                             : 300
	data:    A records:
	data:        IPv4 address                : 134.170.185.46
	data:
	info:    network dns record-set show command OK


Vous pouvez également utiliser nslookup ou d’autres outils DNS pour interroger le nouveau jeu d’enregistrements.

>[AZURE.NOTE] Comme lors de la création de la zone, si vous n’avez pas encore délégué le domaine aux serveurs de noms Azure DNS, vous devez spécifier l’adresse du serveur de noms de votre zone explicitement.


	C:\> nslookup www.contoso.com ns1-01.azure-dns.com

	Server: ns1-01.azure-dns.com
	Address:  208.76.47.1

	Name:    www.contoso.com
	Addresses:  134.170.185.46
    	        



## Étapes suivantes
[Gestion des zones DNS](dns-operations-dnszones-cli.md)

[Gestion des enregistrements DNS](dns-operations-recordsets-cli.md)<BR>

[Automatisation des opérations Azure avec le Kit de développement (SDK) .NET](dns-sdk.md)
 

<!---HONumber=AcomDC_0427_2016-->