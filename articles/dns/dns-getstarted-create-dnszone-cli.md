---
title: "Création d’une zone DNS à l’aide de l’interface de ligne de commande | Microsoft Docs"
description: "Apprenez à créer des zones DNS dans Azure DNS. Il s’agit d’un guide pas à pas pour la création et la gestion de votre première zone DNS à l’aide de l’interface de ligne de commande Azure."
services: dns
documentationcenter: na
author: georgewallace
manager: timlt
ms.assetid: 1514426a-133c-491a-aa27-ee0962cea9dc
ms.service: dns
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/21/2016
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: f156e4d4c5ffddb7e93ebf21baa75864e0e260e9
ms.openlocfilehash: d00792a4bb19e194dbbcee8b9c11e4a744891388

---

# <a name="create-an-azure-dns-zone-using-cli"></a>Création d’une zone Azure DNS à l’aide de l’interface de ligne de commande

> [!div class="op_single_selector"]
> * [Portail Azure](dns-getstarted-create-dnszone-portal.md)
> * [PowerShell](dns-getstarted-create-dnszone.md)
> * [Interface de ligne de commande Azure](dns-getstarted-create-dnszone-cli.md)

Cet article vous guide tout au long de la procédure de création d’une zone DNS à l’aide de l’interface de ligne de commande Azure multiplateforme, qui est disponible pour Windows, Mac et Linux. Vous pouvez également créer une zone DNS à l’aide d’[Azure PowerShell](dns-getstarted-create-dnszone.md) ou du [portail Azure](dns-getstarted-create-dnszone-portal.md).

[!INCLUDE [dns-create-zone-about](../../includes/dns-create-zone-about-include.md)]

[!INCLUDE [dns-cli-setup](../../includes/dns-cli-setup-include.md)]


## <a name="create-a-dns-zone"></a>Création d’une zone DNS

Une zone DNS est créée à l'aide de la commande `azure network dns zone create`. Pour consulter l’aide de cette commande, tapez `azure network dns zone create -h`.

L’exemple ci-dessous crée une zone DNS appelée *contoso.com* dans le groupe de ressources *MyResourceGroup*. Utilisez l’exemple pour créer une zone DNS, en remplaçant les valeurs indiquées par vos propres valeurs.

```azurecli
azure network dns zone create MyResourceGroup contoso.com
```

## <a name="verify-your-dns-zone"></a>Vérification de votre zone DNS

### <a name="view-records"></a>Affichage des enregistrements

La création d’une zone DNS crée également les enregistrements DNS suivants :

* L’enregistrement *SOA* (Start of Authority). Il est présent à la racine de chaque zone DNS.
* Les enregistrements de serveur de noms faisant autorité (NS). Ils indiquent les serveurs de noms qui hébergent la zone. Azure DNS utilise un pool de serveurs de noms. Il se peut donc que différents serveurs de noms soient attribués à différentes zones dans Azure DNS. Pour plus d’informations, consultez [Délégation de domaine à Azure DNS](dns-domain-delegation.md).

Pour afficher ces enregistrements, utilisez `azure network dns-record-set list`:

```azurecli
azure network dns record-set list MyResourceGroup contoso.com

info:    Executing command network dns record-set list
+ Looking up the DNS Record Sets
data:    Name                            : @
data:    Type                            : NS
data:    TTL                             : 172800
data:    Records:
data:      ns1-01.azure-dns.com.
data:      ns2-01.azure-dns.net.
data:      ns3-01.azure-dns.org.
data:      ns4-01.azure-dns.info.
data:
data:    Name                            : @
data:    Type                            : SOA
data:    TTL                             : 3600
data:    Email                           : azuredns-hostmaster.microsoft.com
data:    Host                            : ns1-01.azure-dns.com.
data:    Serial Number                   : 2
data:    Refresh Time                    : 3600
data:    Retry Time                      : 300
data:    Expire Time                     : 2419200
data:    Minimum TTL                     : 300
data:
info:    network dns record-set list command OK
```

> [!NOTE]
> Les jeux d’enregistrements à la racine (ou *apex*) d’une zone DNS utilisent **@** comme nom.

### <a name="test-name-servers"></a>Tester les serveurs de noms

Vous pouvez vérifier la présence de votre zone DNS sur les serveurs de noms Azure DNS à l’aide d’outils DNS comme nslookup, dig ou l’applet de commande PowerShell `Resolve-DnsName`.

Si vous n’avez pas encore délégué votre domaine pour qu’il utilise la nouvelle zone Azure DNS, vous devez diriger la requête DNS directement vers l’un des serveurs de noms pour votre zone. Les serveurs de noms de votre zone sont indiqués dans les enregistrements NS, comme indiqué par `azure network dns record-set list`.

L’exemple suivant utilise l’outil « dig » pour interroger le domaine contoso.com à l’aide des serveurs de noms affectés à la zone DNS. Veillez à indiquer les valeurs correctes pour votre zone.

```
  > dig @ns1-01.azure-dns.com contoso.com
  
  <<>> DiG 9.10.2-P2 <<>> @ns1-01.azure-dns.com contoso.com
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
contoso.com.         3600     IN      SOA     ns1-01.azure-dns.com. azuredns-hostmaster.microsoft.com. 1 3600 300 2419200 300

Query time: 93 msec
SERVER: 208.76.47.5#53(208.76.47.5)
WHEN: Tue Jul 21 16:04:51 Pacific Daylight Time 2015
MSG SIZE  rcvd: 120
```

## <a name="next-steps"></a>Étapes suivantes

Une fois la zone DNS créée, [créez des jeux d’enregistrements et des enregistrements DNS](dns-getstarted-create-recordset-cli.md) dedans.




<!--HONumber=Feb17_HO2-->


