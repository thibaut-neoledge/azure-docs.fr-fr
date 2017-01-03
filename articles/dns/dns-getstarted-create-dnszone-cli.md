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
ms.date: 12/05/2016
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: bfbffe7843bc178cdf289c999925c690ab82e922
ms.openlocfilehash: 5bbd490925e5e25f10044af55af49daa494ee026

---

# <a name="create-an-azure-dns-zone-using-cli"></a>Création d’une zone Azure DNS à l’aide de l’interface de ligne de commande

> [!div class="op_single_selector"]
> * [Portail Azure](dns-getstarted-create-dnszone-portal.md)
> * [PowerShell](dns-getstarted-create-dnszone.md)
> * [Interface de ligne de commande Azure](dns-getstarted-create-dnszone-cli.md)

Cet article vous guide tout au long de la procédure de création d’une zone DNS à l’aide de l’interface de ligne de commande Azure multiplateforme, qui est disponible pour Windows, Mac et Linux. Vous pouvez également créer une zone DNS à l’aide de PowerShell ou du portail Azure.

[!INCLUDE [dns-create-zone-about](../../includes/dns-create-zone-about-include.md)]


## <a name="before-you-begin"></a>Avant de commencer

Vérifiez que vous disposez des éléments ci-dessous avant de commencer votre configuration.

* Un abonnement Azure. Si vous ne disposez pas déjà d’un abonnement Azure, vous pouvez activer vos [avantages abonnés MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou créer un [compte gratuit](https://azure.microsoft.com/pricing/free-trial/).
* Vous devez installer la dernière version de l’interface de ligne de commande Azure, disponible pour Windows, Linux ou MAC. Pour plus d’informations, consultez la page [Installation de l’interface de ligne de commande Azure](../xplat-cli-install.md).

## <a name="step-1---sign-in-and-create-a-resource-group"></a>Étape 1 : connexion et création d’un groupe de ressources

### <a name="switch-cli-mode"></a>Passage en mode CLI

Azure DNS utilise Azure Resource Manager. Veillez à utiliser le mode d’interface de ligne de commande pour exécuter les commandes ARM.

```azurecli
azure config mode arm
```

### <a name="sign-in-to-your-azure-account"></a>Connexion à votre compte Azure

Vous devez indiquer vos informations d’identification. Gardez à l’esprit que vous ne pouvez utiliser que des comptes OrgID.

```azurecli
azure login
```

### <a name="select-the-subscription"></a>Sélection de l’abonnement

Vérifiez les abonnements associés au compte.

```azurecli
azure account list
```

Parmi vos abonnements Azure, choisissez celui que vous souhaitez utiliser.

```azurecli
azure account set "subscription name"
```

### <a name="create-a-resource-group"></a>Créer un groupe de ressources

Azure Resource Manager requiert que tous les groupes de ressources spécifient un emplacement. Ce dernier est utilisé comme emplacement par défaut des ressources de ce groupe. Toutefois, étant donné que toutes les ressources DNS sont globales et non régionales, le choix de l’emplacement du groupe de ressources n’a aucun impact sur Azure DNS.

Ignorez cette étape si vous utilisez un groupe de ressources existant.

```azurecli
azure group create -n myresourcegroup --location "West US"
```

### <a name="register-resource-provider"></a>S’inscrire auprès du fournisseur de ressources

Le service Azure DNS est géré par le fournisseur de ressources Microsoft.Network. Votre abonnement Azure doit être enregistré auprès de ce fournisseur de ressources, pour que vous puissiez utiliser Azure DNS. Cette opération n’est à effectuer qu’une fois par abonnement.

```azurecli
azure provider register --namespace Microsoft.Network
```

## <a name="step-2---create-a-dns-zone"></a>Étape 2 : création d’une zone DNS

Une zone DNS est créée à l'aide de la commande `azure network dns zone create`. Pour consulter l’aide de cette commande, tapez `azure network dns zone create -h`.

L’exemple ci-dessous permet de créer une zone DNS appelée *contoso.com* dans le groupe de ressources *MyResourceGroup*. Utilisez l’exemple pour créer une zone DNS, en remplaçant les valeurs indiquées par vos propres valeurs.

```azurecli
azure network dns zone create MyResourceGroup contoso.com
```

## <a name="step-3---verify"></a>Étape 3 : vérification

### <a name="view-records"></a>Affichage des enregistrements

La création d’une zone DNS crée également les enregistrements DNS suivants :

* L’enregistrement « SOA » (Start of Authority). Il est présent à la racine de chaque zone DNS.
* Les enregistrements de serveur de noms faisant autorité (NS). Ceux-ci indiquent quels serveurs de noms hébergent la zone. Azure DNS utilise un pool de serveurs de noms. Il se peut donc que différents serveurs de noms soient attribués à différentes zones dans Azure DNS. Pour plus d’informations, consultez la page [Délégation d’un domaine à Azure DNS](dns-domain-delegation.md).

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

Si vous n’avez pas encore délégué votre domaine pour qu’il utilise la nouvelle zone Azure DNS, vous devez diriger la requête DNS directement vers l’un des serveurs de noms pour votre zone. Les serveurs de noms de votre zone figurent dans les enregistrements NS, comme indiqué ci-dessus par « azure network dns record-set show ». Veillez à indiquer les valeurs correctes pour votre zone dans la commande ci-dessous.

L’exemple ci-après utilise l’outil « dig » pour interroger le domaine contoso.com à l’aide des serveurs de noms affectés à la zone DNS. La requête doit pointer vers un serveur de noms pour lequel nous avons utilisé *@\<serveur de noms pour la zone\>* et vers le nom de la zone à l’aide de « dig ».

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

## <a name="next-steps"></a>Étapes suivantes

Après avoir créé une zone DNS, [créez des jeux d’enregistrements et des enregistrements](dns-getstarted-create-recordset-cli.md) afin de créer des enregistrements DNS pour votre domaine Internet.




<!--HONumber=Dec16_HO2-->


