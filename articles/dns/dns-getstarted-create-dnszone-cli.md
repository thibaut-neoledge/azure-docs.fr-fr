---
title: Création d’une zone Azure DNS à l’aide de l’interface de ligne de commande | Microsoft Docs
description: Apprenez à créer des zones DNS pour Azure DNS étape par étape, afin d’héberger votre domaine DNS à l’aide de l’interface de ligne de commande
services: dns
documentationcenter: na
author: sdwheeler
manager: carmonm
editor: ''

ms.service: dns
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/16/2016
ms.author: sewhee

---
# Création d’une zone Azure DNS à l’aide de l’interface de ligne de commande
> [!div class="op_single_selector"]
> * [Portail Azure](dns-getstarted-create-dnszone-portal.md)
> * [PowerShell](dns-getstarted-create-dnszone.md)
> * [Interface de ligne de commande Azure](dns-getstarted-create-dnszone-cli.md)
> 
> 

Cet article vous indique la procédure à suivre pour créer une zone DNS à l’aide de l’interface de ligne de commande. Vous pouvez également créer une zone DNS à l’aide de PowerShell ou du portail Azure.

[!INCLUDE [dns-create-zone-about](../../includes/dns-create-zone-about-include.md)]

## Avant de commencer
Ces instructions utilisent l’interface de ligne de commande Microsoft Azure. Veillez à effectuer une mise à jour vers la version la plus récente de l’interface de ligne de commande Azure (0.9.8 ou version ultérieure) pour utiliser les commandes Azure DNS. Tapez `azure -v` pour vérifier la version de la CLI Azure actuellement installée sur votre ordinateur.

## Étape 1 : configuration de l’interface de ligne de commande Azure
### 1\. Installation de l’interface de ligne de commande Azure
Vous pouvez installer l’interface de ligne de commande Azure pour Windows, Linux ou Mac. Les étapes suivantes doivent être effectuées avant de pouvoir gérer Azure DNS à l’aide de l’interface de ligne de commande Azure. Pour plus d’informations, consultez la page [Installation de l’interface de ligne de commande Azure](../xplat-cli-install.md). Les commandes DNS nécessitent Azure CLI version 0.9.8 ou version ultérieure.

Toutes les commandes du fournisseur réseau peuvent être exécutées sur l’interface de ligne de commande à l'aide de la commande suivante :

    azure network

### 2\. Passage en mode CLI
Azure DNS utilise Azure Resource Manager. Veillez à utiliser le mode d’interface de ligne de commande pour exécuter les commandes ARM.

    azure config mode arm

### 3\. Connexion à votre compte Azure
Vous devez indiquer vos informations d’identification. Gardez à l’esprit que vous ne pouvez utiliser que des comptes ORGID.

    azure login -u "username"

### 4\. Sélection de l’abonnement
Parmi vos abonnements Azure, choisissez celui que vous souhaitez utiliser.

    azure account set "subscription name"

### 5\. Créer un groupe de ressources
Azure Resource Manager requiert que tous les groupes de ressources spécifient un emplacement. Ce dernier est utilisé comme emplacement par défaut des ressources de ce groupe. Toutefois, étant donné que toutes les ressources DNS sont globales et non régionales, le choix de l’emplacement du groupe de ressources n’a aucun impact sur Azure DNS.

Ignorez cette étape si vous utilisez un groupe de ressources existant.

    azure group create -n myresourcegroup --location "West US"


### 6\. S’inscrire
Le service Azure DNS est géré par le fournisseur de ressources Microsoft.Network. Votre abonnement Azure doit être enregistré auprès de ce fournisseur de ressources, pour que vous puissiez utiliser Azure DNS. Cette opération n’est à effectuer qu’une fois par abonnement.

    azure provider register --namespace Microsoft.Network


## Étape 2 : création d’une zone DNS
Une zone DNS est créée à l'aide de la commande `azure network dns zone create`. Vous pouvez éventuellement créer une zone DNS avec des balises. Les balises sont une liste de paires nom-valeur. Elles sont utilisées par Azure Resource Manager pour identifier les ressources à des fins de facturation ou de regroupement. Pour plus d’informations sur les balises, voir [Organisation des ressources Azure à l’aide de balises](../resource-group-using-tags.md).

Dans Azure DNS, les noms de zone doivent être spécifiés sans point à la fin (**« . »**). Par exemple, spécifiez « **contoso.com**» plutôt que « **contoso.com.** ».

### Création d’une zone DNS
L’exemple ci-dessous permet de créer une zone DNS appelée *contoso.com* dans le groupe de ressources *MyResourceGroup*.

Utilisez l’exemple pour créer votre zone DNS, en remplaçant les valeurs indiquées par vos propres valeurs.

    azure network dns zone create myresourcegroup contoso.com

### Création d’une zone DNS et de balises
L’interface de ligne de commande Azure DNS prend en charge les balises des zones DNS spécifiées à l’aide du paramètre optionnel *-Tag*. L’exemple suivant montre comment créer une zone DNS avec deux balises, « projet = demo » et « env = test ».

Utilisez l’exemple ci-dessous pour créer une zone DNS et des balises, en remplaçant les valeurs indiquées par vos propres valeurs.

    azure network dns zone create myresourcegroup contoso.com -t "project=demo";"env=test"

## Affichage des enregistrements
La création d’une zone DNS crée également les enregistrements DNS suivants :

* L’enregistrement « SOA » (Start of Authority). Il est présent à la racine de chaque zone DNS.
* Les enregistrements de serveur de noms faisant autorité (NS). Ceux-ci indiquent quels serveurs de noms hébergent la zone. Azure DNS utilise un pool de serveurs de noms. Il se peut donc que différents serveurs de noms soient attribués à différentes zones dans Azure DNS. Pour plus d’informations, consultez la page [Délégation d’un domaine à Azure DNS](dns-domain-delegation.md).

Pour afficher ces enregistrements, utilisez `azure network dns-record-set show`.<BR> *Syntaxe : network dns record-set show <groupe\_ressources> <nom\_zone\_dns> <nome> <type>*

Dans l’exemple ci-dessous, si vous exécutez la commande avec le groupe de ressources *myresourcegroup*, le nom du jeu d’enregistrements *"@"* (pour un enregistrement racine) et au type *SOA*, vous obtenez la sortie suivante :

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
<BR> Pour afficher les enregistrements NS créés avec la zone, utilisez la commande suivante :

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

> [!NOTE]
> Les jeux d’enregistrements à la racine (ou *apex*) d’une zone DNS utilisent **@** comme nom.
> 
> 

## Test
Vous pouvez tester votre zone DNS à l’aide d’outils DNS comme nslookup, DIG ou l’applet de commande PowerShell `Resolve-DnsName`.

Si vous n’avez pas encore délégué votre domaine pour qu’il utilise la nouvelle zone Azure DNS, vous devez diriger la requête DNS directement vers l’un des serveurs de noms pour votre zone. Les serveurs de noms de votre zone figurent dans les enregistrements NS, comme indiqué ci-dessus par « azure network dns record-set show ». Veillez à indiquer les valeurs correctes pour votre zone dans la commande ci-dessous.

L’exemple suivant utilise DIG pour interroger le domaine contoso.com à l’aide des serveurs de noms attribués à la zone DNS. La requête doit pointer vers un serveur de noms pour lequel nous avons utilisé *@<serveur de noms pour la zone>* et vers le nom de la zone à l’aide de DIG.

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
Après avoir créé une zone DNS, créez [des jeux d’enregistrements et des enregistrements](dns-getstarted-create-recordset-cli.md) pour démarrer la résolution des noms pour votre domaine Internet.

<!---HONumber=AcomDC_1005_2016-->