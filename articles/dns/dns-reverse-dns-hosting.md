---
title: "Hébergement de zones de recherche inversées DNS dans Azure DNS | Microsoft Docs"
description: "Découvrez comment utiliser Azure DNS pour héberger les zones de recherche inversées DNS pour vos plages d’adresses IP"
services: dns
documentationcenter: na
author: jtuliani
manager: timlt
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/29/2017
ms.author: jonatul
ms.translationtype: Human Translation
ms.sourcegitcommit: 5edc47e03ca9319ba2e3285600703d759963e1f3
ms.openlocfilehash: 3e10b25d2f9b91c96af2958fef6dc6a4fdbff301
ms.contentlocale: fr-fr
ms.lasthandoff: 05/31/2017

---

# <a name="hosting-reverse-dns-lookup-zones-in-azure-dns"></a>Hébergement de zones de recherche inversées DNS dans Azure DNS

Cet article explique comment héberger les zones de recherche inversées DNS pour vos plages d’adresses IP attribuées dans Azure DNS. En règle générale, votre fournisseur de services Internet attribue à votre organisation les plages d’adresses IP représentées par la zone de recherche inversée.

Pour configurer un DNS inversé pour une adresse IP gérée par Azure ayant été attribuée à votre service Azure, consultez [configurer la recherche inversée pour les adresses IP allouées à votre service Azure](dns-reverse-dns-for-azure-services.md).

Avant de lire cet article, prenez connaissance de cette [Vue d’ensemble des DNS inversés et de la prise en charge dans Azure](dns-reverse-dns-overview.md).

Cet article vous indique la procédure à suivre pour créer votre première zone de recherche inversée DNS et votre premier enregistrement à l’aide du portail Azure, d’Azure PowerShell, d’Azure CLI 1.0 ou d’Azure CLI 2.0.

## <a name="create-a-reverse-lookup-dns-zone"></a>Créer une zone de recherche inversée DNS

1. Connectez-vous au [portail Azure](https://portal.azure.com)
1. Dans le menu Hub, cliquez sur **Nouveau** > **Mise en réseau** >, puis cliquez sur **Zone DNS** pour ouvrir le panneau **Créer une zone DNS** .

   ![Zone DNS](./media/dns-reverse-dns-hosting/figure1.png)

1. Dans le panneau **Créer une zone DNS** , nommez votre zone DNS. Le nom de la zone a été conçu différemment pour les préfixes IPv4 et IPv6. Utilisez les instructions relatives à [IPV4](#ipv4) ou à [IPv6](#ipv6) pour nommer votre zone. Lorsque vous avez terminé cliquez sur **Créer** pour créer la zone.

### <a name="ipv4"></a>IPv4

Le nom d’une zone de recherche inversée IPv4 est basé sur la plage d’adresses IP qu’il représente. Il doit se présenter sous la forme suivante : `<IPv4 network prefix in reverse order>.in-addr.arpa`. Pour obtenir des exemples, consultez [vue d’ensemble des DNS inversés et prise en charge dans Azure](dns-reverse-dns-overview.md#ipv4).

> [!NOTE]
> Lorsque vous créez des zones de recherche inversées DNS sans classe dans Azure DNS, vous devez utiliser un trait d’union (`-`) à la place d’une barre oblique (« / ») dans le nom de zone.
>
> Par exemple, pour la plage d’adresses IP 192.0.2.1.128/26, vous devez utiliser `128-26.2.0.192.in-addr.arpa` comme nom de zone à la place de `128/26.2.0.192.in-addr.arpa`.
>
> En effet, bien qu’ils soient tous deux pris en charge par les normes DNS, les noms de zones DNS contenant une barre oblique (`/`) ne sont pas pris en charge dans Azure DNS.

L’exemple suivant montre comment créer une zone DNS inversée de « Classe C » nommée `2.0.192.in-addr.arpa` dans Azure DNS via le portail Azure :

 ![Créer une zone DNS](./media/dns-reverse-dns-hosting/figure2.png)

L’ « emplacement du groupe de ressources » définit l’emplacement du groupe de ressources et n’a aucun impact sur la zone DNS. L’emplacement de la zone DNS est toujours « global » et n’est pas affiché.

Les exemples suivants montrent comment effectuer cette tâche à l’aide d’Azure PowerShell et Azure CLI :

#### <a name="powershell"></a>PowerShell

```powershell
New-AzureRmDnsZone -Name 2.0.192.in-addr.arpa -ResourceGroupName MyResourceGroup
```

#### <a name="azure-cli-10"></a>Azure CLI 1.0

```azurecli
azure network dns zone create MyResourceGroup 2.0.192.in-addr.arpa
```

#### <a name="azure-cli-20"></a>Azure CLI 2.0

```azurecli
az network dns zone create -g MyResourceGroup -n 2.0.192.in-addr.arpa
```

### <a name="ipv6"></a>IPv6

Le nom d’une zone de recherche inversée IPv6 doit se présenter sous la forme suivante : `<IPv6 network prefix in reverse order>.ip6.arpa`.  Pour obtenir des exemples, consultez [vue d’ensemble des DNS inversés et prise en charge dans Azure](dns-reverse-dns-overview.md#ipv6).


L’exemple suivant montre comment créer une zone de recherche inversée DNS IPv6 nommée `0.0.0.0.d.c.b.a.8.b.d.0.1.0.0.2.ip6.arpa` dans Azure DNS via le portail Azure :

 ![créer une zone DNS](./media/dns-reverse-dns-hosting/figure3.png)

L’ « emplacement du Groupe de ressources » définit l’emplacement du groupe de ressources et n’a aucun impact sur la zone DNS. L’emplacement de la zone DNS est toujours « global » et n’est pas affiché.

Les exemples suivants montrent comment effectuer cette tâche à l’aide d’Azure PowerShell et Azure CLI :

#### <a name="powershell"></a>PowerShell

```powershell
New-AzureRmDnsZone -Name 0.0.0.0.d.c.b.a.8.b.d.0.1.0.0.2.ip6.arpa -ResourceGroupName MyResourceGroup
```

#### <a name="azurecli-10"></a>Azure CLI 1.0

```azurecli
azure network dns zone create MyResourceGroup 0.0.0.0.d.c.b.a.8.b.d.0.1.0.0.2.ip6.arpa
```

#### <a name="azurecli-20"></a>Azure CLI 2.0

```azurecli
az network dns zone create -g MyResourceGroup -n 0.0.0.0.d.c.b.a.8.b.d.0.1.0.0.2.ip6.arpa
```

## <a name="delegate-a-reverse-dns-lookup-zone"></a>Déléguer une zone de recherche inversée DNS

Après la création de votre zone de recherche inversée DNS, vous devez vous assurer que la zone est déléguée depuis la zone parente. La délégation DNS permet au processus de résolution de nom DNS de trouver les serveurs de noms hébergeant votre zone de recherche inversée DNS. Ainsi les serveurs de noms peuvent répondre aux requêtes DNS inverses pour les adresses IP de votre plage d’adresses.

Pour les zones de recherche directe, le processus de délégation de zone DNS est décrit dans [Déléguer votre domaine à Azure DNS](dns-delegate-domain-azure-dns.md). La délégation pour les zones de recherche inversée fonctionne de façon similaire. La seule différence est que vous devez configurer les serveurs de noms avec le fournisseur de services Internet qui a fourni votre plage d’adresses IP, plutôt qu’avec votre bureau d'enregistrement de noms de domaine.

## <a name="create-a-dns-ptr-record"></a>Créer un enregistrement PTR DNS

### <a name="ipv4"></a>IPv4

L’exemple suivant vous guide tout au long du processus de création d’un enregistrement PTR dans une zone DNS inversée dans Azure DNS. Pour découvrir d’autres types d’enregistrements et pour modifier les enregistrements existants, consultez [Gestion d’enregistrements et de jeux d’enregistrements DNS à l’aide du portail Azure](dns-operations-recordsets-portal.md).

1.    En haut du panneau **Zone DNS**, sélectionnez **+ Jeu d’enregistrements** pour ouvrir le panneau **Ajouter un jeu d’enregistrements**.

 ![Zone DNS](./media/dns-reverse-dns-hosting/figure4.png)

1. Sur le panneau **Ajouter un jeu d’enregistrements** . 
1. Sélectionnez **PTR** depuis le menu «**Type**» d’enregistrement.  
1. Le nom du jeu d’enregistrements d’un enregistrement PTR doit correspondre à la fin de l’adresse IPv4 dans l’ordre inverse. Dans cet exemple, les trois premiers octets figurent déjà dans le nom de la zone (.2.0.192). Par conséquent, seul le dernier octet figure dans le champ nom. Vous pouvez par exemple nommer votre jeu d’enregistrements «**15**» pour une ressource dont l’adresse IP est 192.0.2.15.  
1. Dans le champ «**Nom de domaine**», entrez le nom de domaine complet (FQDN) de la ressource à l’aide de l’adresse IP.
1. Sélectionnez **OK** en bas du panneau pour créer l’enregistrement DNS.

 ![ajouter un jeu d’enregistrements](./media/dns-reverse-dns-hosting/figure5.png)

Les exemples suivants montrent comment effectuer cette tâche à l’aide d’Azure PowerShell et d’Azure CLI :

#### <a name="powershell"></a>PowerShell

```powershell
New-AzureRmDnsRecordSet -Name 15 -RecordType PTR -ZoneName 2.0.192.in-addr.arpa -ResourceGroupName MyResourceGroup -Ttl 3600 -DnsRecords (New-AzureRmDnsRecordConfig -Ptrdname "dc1.contoso.com")
```
#### <a name="azurecli-10"></a>Azure CLI 1.0

```azurecli
azure network dns record-set add-record MyResourceGroup 2.0.192.in-addr.arpa 15 PTR --ptrdname dc1.contoso.com  
```

#### <a name="azurecli-20"></a>Azure CLI 2.0

```azurecli
    az network dns record-set ptr add-record -g MyResourceGroup -z 2.0.192.in-addr.arpa -n 15 --ptrdname dc1.contoso.com
```

### <a name="ipv6"></a>IPv6

L’exemple suivant vous guide tout au long du processus de création d’un nouvel enregistrement « PTR ». Pour découvrir d’autres types d’enregistrements et pour modifier les enregistrements existants, consultez [Gestion d’enregistrements et de jeux d’enregistrements DNS à l’aide du portail Azure](dns-operations-recordsets-portal.md).

1. En haut du **panneau zone DNS**, sélectionnez **+ Jeu d’enregistrements** pour ouvrir le panneau **Ajouter un jeu d’enregistrements**.

  ![panneau zone DNS](./media/dns-reverse-dns-hosting/figure6.png)

2. Sur le panneau **Ajouter un jeu d’enregistrements** . 
3. Sélectionnez **PTR** depuis le menu «**Type**» d’enregistrement.  
4. Le nom du jeu d’enregistrements d’un enregistrement PTR doit correspondre à la fin de l’adresse IPv6 dans l’ordre inverse. Il ne doit inclure aucune compression des zéros. Dans cet exemple, les 64 premiers bits de l’IPv6 figurent déjà dans le cadre du nom de zone (0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa). Par conséquent, seuls les 64 derniers bits figurent dans le champ nom. Les 64 derniers bits de l’adresse IP sont entrés dans l’ordre inverse, à l’aide d’un point comme séparateur entre chaque nombre hexadécimal. Par exemple, vous pouvez nommer votre jeu d’enregistrements «**e.5.0.4.9.f.a.1.c.b.0.1.4.2.5.f**» pour une ressource dont l’adresse IP est 2001:0db8:abdc:0000:f524:10bc:1af9:405e.  
5. Dans le champ «**Nom de domaine**», entrez le nom de domaine complet (FQDN) de la ressource à l’aide de l’adresse IP.
6. Sélectionnez **OK** en bas du panneau pour créer l’enregistrement DNS.

![ajouter un panneau du jeu d’enregistrements](./media/dns-reverse-dns-hosting/figure7.png)

Les exemples suivants montrent comment effectuer cette tâche à l’aide d’Azure PowerShell et d’Azure CLI :

#### <a name="powershell"></a>PowerShell

```powershell
New-AzureRmDnsRecordSet -Name "e.5.0.4.9.f.a.1.c.b.0.1.4.2.5.f" -RecordType PTR -ZoneName 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa -ResourceGroupName MyResourceGroup -Ttl 3600 -DnsRecords (New-AzureRmDnsRecordConfig -Ptrdname "dc2.contoso.com")
```

#### <a name="azurecli-10"></a>Azure CLI 1.0

```
azure network dns record-set add-record MyResourceGroup 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa e.5.0.4.9.f.a.1.c.b.0.1.4.2.5.f PTR --ptrdname dc2.contoso.com 
```
 
#### <a name="azurecli-20"></a>Azure CLI 2.0

```azurecli
    az network dns record-set ptr add-record -g MyResourceGroup -z 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa -n e.5.0.4.9.f.a.1.c.b.0.1.4.2.5.f --ptrdname dc2.contoso.com
```

## <a name="view-records"></a>Afficher des Enregistrements

Pour afficher les enregistrements que vous avez créés, accédez à votre zone DNS dans le portail Azure. Vous pouvez afficher les enregistrements de la zone DNS dans la partie inférieure du panneau **zone DNS** . Vous devez voir les enregistrements NS et SOA par défaut, qui sont créés dans chaque zone, ainsi que les nouveaux enregistrements que vous avez créés.

### <a name="ipv4"></a>IPv4

Panneau zone DNS, affichant les enregistrements PTR de IPv4 :

![panneau zone DNS](./media/dns-reverse-dns-hosting/figure8.png)

Les exemples suivants montrent comment afficher les enregistrements PTR à l’aide de PowerShell ou d’Azure CLI :

#### <a name="powershell"></a>PowerShell

```powershell
Get-AzureRmDnsRecordSet -ZoneName 2.0.192.in-addr.arpa -ResourceGroupName MyResourceGroup
```

#### <a name="azure-cli-10"></a>Azure CLI 1.0

```azurecli
    azure network dns record-set list MyResourceGroup 2.0.192.in-addr.arpa
```

#### <a name="azure-cli-20"></a>Azure CLI 2.0

```azurecli
    azure network dns record-set list -g MyResourceGroup -z 2.0.192.in-addr.arpa
```

### <a name="ipv6"></a>IPv6

Panneau zone DNS, affichant les enregistrements PTR de IPv6 :

![panneau zone DNS](./media/dns-reverse-dns-hosting/figure9.png)

Les exemples suivants montrent comment afficher les enregistrements à l’aide de PowerShell et d’Azure CLI :

#### <a name="powershell"></a>PowerShell

```powershell
Get-AzureRmDnsRecordSet -ZoneName 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa -ResourceGroupName MyResourceGroup
```

#### <a name="azure-cli-10"></a>Azure CLI 1.0

```azurecli
    azure network dns record-set list MyResourceGroup 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa
```

#### <a name="azure-cli-20"></a>Azure CLI 2.0

```azurecli
    azure network dns record-set list -g MyResourceGroup -z 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa
```

## <a name="faq"></a>Forum Aux Questions

### <a name="can-i-host-reverse-dns-lookup-zones-for-my-isp-assigned-ip-blocks-on-azure-dns"></a>Puis-je héberger des zones de recherche inversée DNS pour les blocs IP attribués par mon fournisseur de services Internet sur Azure DNS ?

Oui. L’hébergement des zones de recherche inversée pour vos propres plages d’adresses IP dans Azure DNS est entièrement pris en charge.

Créez la zone de recherche inversée dans Azure DNS comme expliqué dans cet article, puis travailler avec votre fournisseur de services Internet pour [déléguer la zone](dns-domain-delegation.md).  Vous pouvez ensuite gérer les enregistrements PTR pour chaque recherche inversée de la même façon que d’autres types d’enregistrement.

### <a name="how-much-does-hosting-my-reverse-dns-lookup-zone-cost"></a>Combien coûte l’hébergement de ma zone de recherche DNS inversée ?

L’hébergement de la zone de recherche inversée DNS pour le bloc IP attribué par votre fournisseur de services Internet dans Azure DNS est facturé selon les [tarifs Azure DNS standard](https://azure.microsoft.com/pricing/details/dns/).

### <a name="can-i-host-reverse-dns-lookup-zones-for-both-ipv4-and-ipv6-addresses-in-azure-dns"></a>Puis-je héberger des zones de recherche inversée DNS pour les adresses IPv4 et IPv6 dans Azure DNS ?

Oui. Cet article explique comment créer des zones de recherche DNS IPv4 et IPv6 dans Azure DNS.

### <a name="can-i-import-an-existing-reverse-dns-lookup-zone"></a>Puis-je importer une zone de recherche DNS inversée existante ?

Oui. Vous pouvez utiliser Azure CLI pour importer des zones DNS existantes dans Azure DNS. Cela fonctionne pour les zones de recherche directe et des zones de recherche inversée.

Pour plus d’informations, consultez [Importer et exporter un fichier de zone DNS à l’aide d’Azure CLI](dns-import-export.md).

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur le DNS inversé, consultez [Recherche DNS inversée sur Wikipedia](http://en.wikipedia.org/wiki/Reverse_DNS_lookup).
<br>
Découvrez comment [gérer des enregistrements DNS inversés pour vos services Azure](dns-reverse-dns-for-azure-services.md).

