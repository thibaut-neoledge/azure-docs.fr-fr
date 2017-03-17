---
title: "Prise en main d’Azure DNS | Microsoft Docs"
description: "Apprenez à créer des zones DNS dans Azure DNS. Il s’agit d’un guide pas à pas concernant la création et la gestion de votre première zone DNS à l’aide d’Azure PowerShell."
services: dns
documentationcenter: na
author: georgewallace
manager: timlt
ms.assetid: d78583b7-e669-435c-819b-7605cf791b0e
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/05/2016
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: 36fa9cd757b27347c08f80657bab8a06789a3c2f
ms.openlocfilehash: 798717eca5180e445b14351bbfc694ed206ee9dc
ms.lasthandoff: 02/27/2017

---

# <a name="create-a-dns-zone-using-powershell"></a>Créer une zone DNS à l’aide de PowerShell

> [!div class="op_single_selector"]
> * [portail Azure](dns-getstarted-create-dnszone-portal.md)
> * [PowerShell](dns-getstarted-create-dnszone.md)
> * [Azure CLI 1.0](dns-getstarted-create-dnszone-cli-nodejs.md)
> * [Azure CLI 2.0](dns-getstarted-create-dnszone-cli.md)

Cet article vous indique la procédure à suivre pour créer une zone DNS à l’aide d’Azure PowerShell. Vous pouvez également gérer vos zones DNS en utilisant [l’interface de ligne de commande Azure](dns-getstarted-create-dnszone-cli.md) multiplateforme ou le [Portail Azure](dns-getstarted-create-dnszone-portal.md).

[!INCLUDE [dns-create-zone-about](../../includes/dns-create-zone-about-include.md)]

[!INCLUDE [dns-powershell-setup](../../includes/dns-powershell-setup-include.md)]


## <a name="create-a-dns-zone"></a>Création d’une zone DNS

Une zone DNS est créée à l’aide de l’applet de commande `New-AzureRmDnsZone` . L’exemple ci-dessous crée une zone DNS appelée *contoso.com* dans le groupe de ressources *MyResourceGroup*. Utilisez l’exemple pour créer une zone DNS, en remplaçant les valeurs indiquées par vos propres valeurs.

```powershell
New-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup
```

## <a name="verify-your-dns-zone"></a>Vérification de votre zone DNS

### <a name="view-records"></a>Affichage des enregistrements

La création d’une zone DNS crée également les enregistrements DNS suivants :

* L’enregistrement *SOA* (Start of Authority). Il est présent à la racine de chaque zone DNS.
* Les enregistrements de serveur de noms faisant autorité (NS). Ils indiquent les serveurs de noms qui hébergent la zone. Azure DNS utilise un pool de serveurs de noms. Il se peut donc que différents serveurs de noms soient attribués à différentes zones dans Azure DNS. Pour plus d’informations, consultez [Délégation de domaine à Azure DNS](dns-domain-delegation.md).

Pour afficher ces enregistrements, utilisez `Get-AzureRmDnsRecordSet`:

```powershell
Get-AzureRmDnsRecordSet -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup

Name              : @
ZoneName          : contoso.com
ResourceGroupName : MyAzureResourceGroup
Ttl               : 172800
Etag              : f573237b-088c-424a-b53c-08567d87d049
RecordType        : NS
Records           : {ns1-01.azure-dns.com., ns2-01.azure-dns.net., ns3-01.azure-dns.org., ns4-01.azure-dns.info.}
Metadata          : 

Name              : @
ZoneName          : contoso.com
ResourceGroupName : MyAzureResourceGroup
Ttl               : 3600
Etag              : bf88a27d-0eec-4847-ad42-f0c83b9a2c32
RecordType        : SOA
Records           : {[ns1-01.azure-dns.com.,azuredns-hostmaster.microsoft.com,3600,300,2419200,300]}
Metadata          : 
```

> [!NOTE]
> Les jeux d’enregistrements à la racine (ou *apex*) d’une zone DNS utilisent **@** comme nom.

### <a name="test-name-servers"></a>Tester les serveurs de noms

Vous pouvez vérifier la présence de votre zone DNS sur les serveurs de noms Azure DNS à l’aide d’outils DNS comme nslookup, dig ou [l’applet de commande PowerShell Resolve-DnsName](https://technet.microsoft.com/library/jj590781.aspx).

Si vous n’avez pas encore délégué votre domaine pour qu’il utilise la nouvelle zone Azure DNS, vous devez diriger la requête DNS directement vers l’un des serveurs de noms pour votre zone. Les serveurs de noms de votre zone figurent dans les enregistrements NS, répertoriés ci-dessus par `Get-AzureRmDnsRecordSet` . Veillez à spécifier les valeurs correctes pour votre zone dans l’exemple suivant :

```
nslookup
> set type=SOA
> server ns1-01.azure-dns.com
> contoso.com

Server: ns1-01.azure-dns.com
Address:  40.90.4.1

contoso.com
        primary name server = ns1-01.azure-dns.com
        responsible mail addr = azuredns-hostmaster.microsoft.com
        serial  = 1
        refresh = 3600 (1 hour)
        retry   = 300 (5 mins)
        expire  = 2419200 (28 days)
        default TTL = 300 (5 mins)
```

## <a name="next-steps"></a>Étapes suivantes

Après avoir créé une zone DNS, [créez des jeux d’enregistrements et des enregistrements](dns-getstarted-create-recordset.md) afin de créer des enregistrements DNS pour votre domaine Internet.

