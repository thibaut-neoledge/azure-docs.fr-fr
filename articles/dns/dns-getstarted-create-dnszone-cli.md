<properties
   pageTitle="Prise en main d’Azure DNS avec l’interface de ligne de commande | Microsoft Azure"
   description="Apprenez à créer des zones DNS pour Azure DNS étape par étape, afin d’héberger votre domaine DNS à l’aide de l’interface de ligne de commande"
   services="dns"
   documentationCenter="na"
   authors="joaoma"
   manager="adinah"
   editor=""/>

<tags
   ms.service="dns"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/22/2015"
   ms.author="joaoma"/>

# Prise en main des DNS Azure avec l’interface de ligne de commande



> [AZURE.SELECTOR]
- [Azure CLI](dns-getstarted-create-dnszone-cli.md)
- [Azure Powershell steps](dns-getstarted-create-dnszone.md)


Le domaine « contoso.com » peut contenir un certain nombre d’enregistrements DNS, tels que « mail.contoso.com » (pour un serveur de messagerie) et « www.contoso.com » (pour un site web). Une zone DNS permet d’héberger les enregistrements DNS d’un domaine particulier.

Avant d’héberger votre domaine, vous devez d’abord créer une zone DNS. Les enregistrements DNS créés pour un domaine particulier sont situés dans une zone DNS pour le domaine.

Ces instructions utilisent l’interface de ligne de commande Microsoft Azure. Veillez à effectuer une mise à jour vers la version la plus récente de l’interface de ligne de commande Azure pour utiliser les commandes Azure DNS.

## Configuration de l’interface de ligne de commande Azure

### Étape 1

Installez l’interface de ligne de commande Azure. Vous pouvez installer l’interface de ligne de commande Azure pour Windows, Linux ou Mac. Les étapes suivantes doivent être effectuées avant de pouvoir gérer Azure DNS à l’aide de l’interface de ligne de commande Azure. Pour plus d’informations, consultez la page [Installation de l’interface de ligne de commande Azure](../xplat-cli-install.md). Toutes les commandes du fournisseur réseau peuvent être exécutées sur l’interface de ligne de commande à l'aide de la commande suivante :

	Azure network


>[AZURE.IMPORTANT]Les commandes DNS nécessitent Azure CLI version 0.9.8 ou version ultérieure. Tapez `azure -v` pour vérifier la version Azure CLI actuellement installée sur votre ordinateur.
 
### Étape 2

Azure DNS utilise Azure Resource Manager. Veillez à utiliser l’interface de ligne de commande pour exécuter les commandes ARM et DNS.

	Azure config mode arm

### Étape 3

Connectez-vous à votre compte Azure.

    Azure login -u "username"

Vous devez indiquer vos informations d’identification. Gardez à l’esprit que vous ne pouvez utiliser que des comptes ORGID.

### Étape 4
Parmi vos abonnements Azure, choisissez celui que vous souhaitez utiliser.

    Azure account set "subscription name"

Pour afficher la liste des abonnements disponibles, utilisez la commande « azure account list ».

### Étape 5

Créez un groupe de ressources (ignorez cette étape si vous utilisez un groupe de ressources existant)

    Azure group create -n myresourcegroup --location "West US"

Azure Resource Manager requiert que tous les groupes de ressources spécifient un emplacement. Ce dernier est utilisé comme emplacement par défaut des ressources de ce groupe. Toutefois, étant donné que toutes les ressources DNS sont globales et non régionales, le choix de l’emplacement du groupe de ressources n’a aucun impact sur Azure DNS.

### Étape 6

Le service Azure DNS est géré par le fournisseur de ressources Microsoft.Network. Votre abonnement Azure doit être inscrit auprès de ce fournisseur de ressources pour pouvoir utiliser Azure DNS. Cette opération n’est à effectuer qu’une fois pour chaque abonnement.

	Azure provider register --namespace Microsoft.Network

## Balises

Les balises sont différentes des Etags. Les balises sont une liste de paires nom-valeur. Elles sont utilisées par Azure Resource Manager pour identifier les ressources à des fins de facturation ou de regroupement. Pour plus d’informations sur les balises, voir [Organisation des ressources Azure à l’aide de balises](resource-group-using-tags.md). L’interface de ligne de commande Azure DNS prend en charge les balises dans les zones et les jeux d’enregistrements spécifiés à l’aide du paramètre « -Tag ». L’exemple suivant montre comment créer une zone DNS avec deux balises, « projet = demo » et « env = test » :

	Azure network dns zone create -n contoso.com -g myresourcegroup -t "project=demo";"env=test"

## Création d’une zone DNS

Une zone DNS est créée à l'aide de la commande `azure network dns zone create`. Dans l’exemple ci-dessous, vous allez créer une zone DNS appelée « contoso.com » dans le groupe de ressources « MyResourceGroup » :

    Azure network dns zone create -n contoso.com -g myresourcegroup


>[AZURE.NOTE]Dans Azure DNS, les noms de zone doivent être spécifiés sans ajouter de « . » (par exemple, « contoso.com » plutôt que « contoso.com. »).


Votre zone DNS vient d’être créée dans Azure DNS. La création d’une zone DNS crée également les enregistrements DNS suivants :

- L’enregistrement « SOA » (Start of Authority). Il est présent à la racine de chaque zone DNS.
- Les enregistrements de serveur de noms faisant autorité (NS). Ceux-ci indiquent quels serveurs de noms hébergent la zone. Azure DNS utilise un pool de serveurs de noms. Il se peut donc que différents serveurs de noms soient attribués à différentes zones dans Azure DNS. Pour plus d'informations, consultez [Délégation d'un domaine à Azure DNS](dns-domain-delegation.md).

Pour afficher ces enregistrements, utilisez la commande « azure network dns-record-set show » :

	Usage: network dns record-set show <resource-group> <dns-zone-name> <name> <type>


Dans l’exemple ci-dessous, la commande correspondant au groupe de ressources « myresourcegroup », au nom de jeu d’enregistrements « @ » (pour un enregistrement racine) et au type « SOA » génère la sortie suivante :
 

	azure network dns record-set show myresourcegroup "contoso.com" "@" SOA
	info:    Executing command network dns-record-set show
	+ Looking up the DNS record set "@"
	data:    Id                              : /subscriptions/#######################/resourceGroups/myresourcegroup/providers/Microsoft.Network/dnszones/contoso.com/SOA/@
	data:    Name                            : @
	data:    Type                            : Microsoft.Network/dnszones/SOA
	data:    Location                        : global
	data:    TTL                             : 3600
	data:    SOA record:
	data:      Email                         : msnhst.microsoft.com
	data:      Expire time                   : 604800
	data:      Host                          : edge1.azuredns-cloud.net
	data:      Minimum TTL                   : 300
	data:      Refresh time                  : 900
	data:      Retry time                    : 300
	data:                                    :
<BR> Pour afficher les enregistrements NS créés, utilisez la commande suivante :

	azure network dns record-set show myresourcegroup "contoso.com" "@" NS
	info:    Executing command network dns-record-set show
	+ Looking up the DNS record set "@"
	data:    Id                              : /subscriptions/#######################/resourceGroups/myresourcegroup/providers/Microsoft.Network/dnszones/contoso.com/NS/@
	data:    Name                            : @
	data:    Type                            : Microsoft.Network/dnszones/NS
	data:    Location                        : global
	data:    TTL                             : 3600
	data:    NS records
	data:        Name server domain name     : ns1-05.azure-dns.com
	data:        Name server domain name     : ns2-05.azure-dns.net
	data:        Name server domain name     : ns3-05.azure-dns.org
	data:        Name server domain name     : ns4-05.azure-dns.info
	data:
	info:    network dns-record-set show command OK

>[AZURE.NOTE]Les jeux d’enregistrements à la racine (ou « apex ») d’une zone DNS utilisent « @ » comme nom de jeu d’enregistrements.

Après avoir créé votre première zone DNS, vous pouvez la tester à l'aide d'outils DNS comme nslookup, DIG ou l'**applet de commande PowerShell Resolve-DnsName**. Si vous n’avez pas encore délégué votre domaine pour qu’il utilise la nouvelle zone Azure DNS, vous devez diriger la requête DNS directement vers l’un des serveurs de noms pour votre zone. Les serveurs de noms de votre zone figurent dans les enregistrements NS, comme indiqué ci-dessus par « azure network dns-record-set show ». Veillez à indiquer les valeurs correctes pour votre zone dans la commande ci-dessous.

L’exemple suivant utilise DIG pour interroger le domaine contoso.com à l’aide des serveurs de noms attribués à la zone DNS. La requête doit pointer vers un serveur de noms pour lequel nous avons utilisé `@<name server for the zone>` et vers le nom de la zone à l'aide de DIG.

	 <<>> DiG 9.10.2-P2 <<>> @ns1-05.azure-dns.com contoso.com
	(1 server found)
	global options: +cmd
 	Got answer:
	->>HEADER<<- opcode: QUERY, status: NOERROR, id: 60963
 	flags: qr aa rd; QUERY: 1, ANSWER: 0, AUTHORITY: 1, ADDITIONAL: 1
 	WARNING: recursion requested but not available

 	OPT PSEUDOSECTION:
 	EDNS: version: 0, flags:; udp: 4000
  	QUESTION SECTION:
	contoso.com.                        IN      A

 	AUTHORITY SECTION:
	contoso.com.         300     IN      SOA     edge1.azuredns-cloud.net.
	msnhst.microsoft.com. 6 900 300 604800 300

	Query time: 93 msec
	SERVER: 208.76.47.5#53(208.76.47.5)
	WHEN: Tue Jul 21 16:04:51 Pacific Daylight Time 2015
	MSG SIZE  rcvd: 120

## Étapes suivantes


[Prise en main de la création de jeux d'enregistrements et d'enregistrements](dns-getstarted-create-recordset-cli.md)<BR> [Gestion des zones DNS](dns-operations-dnszones-cli.md)<BR> [Gestion des enregistrements DNS](dns-operations-recordsets-cli.md)<BR> [Automatisation des opérations Azure avec le Kit de développement logiciel (SDK) .NET](dns-sdk.md)<BR> [Référence de l'API REST d'Azure DNS](https://msdn.microsoft.com/library/azure/mt163862.aspx)

<!---HONumber=Oct15_HO3-->