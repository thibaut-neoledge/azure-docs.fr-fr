---
title: "Création d’une zone DNS à l’aide d’Azure CLI 2.0 | Microsoft Docs"
description: "Apprenez à créer des zones DNS dans Azure DNS. Il s’agit d’un guide pas à pas pour la création et la gestion de votre première zone DNS à l’aide d’Azure CLI 2.0."
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
ms.date: 02/27/2017
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: 1481fcb070f383d158c5a6ae32504e498de4a66b
ms.openlocfilehash: 00a4dfbe070129ce6dc84f006d793007d55dba3f
ms.lasthandoff: 03/01/2017

---

# <a name="create-an-azure-dns-zone-using-azure-cli-20"></a>Création d’une zone Azure DNS à l’aide d’Azure CLI 2.0

> [!div class="op_single_selector"]
> * [Portail Azure](dns-getstarted-create-dnszone-portal.md)
> * [PowerShell](dns-getstarted-create-dnszone.md)
> * [Azure CLI 1.0](dns-getstarted-create-dnszone-cli-nodejs.md)
> * [Azure CLI 2.0](dns-getstarted-create-dnszone-cli.md)

Cet article vous guide tout au long de la procédure de création d’une zone DNS à l’aide de l’interface de ligne de commande Azure multiplateforme, qui est disponible pour Windows, Mac et Linux. Vous pouvez également créer une zone DNS à l’aide d’[Azure PowerShell](dns-getstarted-create-dnszone.md) ou du [portail Azure](dns-getstarted-create-dnszone-portal.md).

## <a name="cli-versions-to-complete-the-task"></a>Versions de l’interface de ligne de commande permettant d’effectuer la tâche

Vous pouvez exécuter la tâche en utilisant l’une des versions suivantes de l’interface de ligne de commande (CLI) :

* [Azure CLI 1.0](dns-getstarted-create-dnszone-cli-nodejs.md) : notre interface de ligne de commande pour les modèles de déploiement Classique et Resource Manager.
* [Azure CLI 2.0](dns-getstarted-create-dnszone-cli.md) : notre interface de ligne de commande nouvelle génération pour le modèle de déploiement Resource Manager.

## <a name="introduction"></a>Introduction

[!INCLUDE [dns-create-zone-about](../../includes/dns-create-zone-about-include.md)]

## <a name="set-up-azure-cli-20-for-azure-dns"></a>Configuration d’Azure CLI 2.0 pour Azure DNS

### <a name="before-you-begin"></a>Avant de commencer

Vérifiez que vous disposez des éléments ci-dessous avant de commencer votre configuration.

* Un abonnement Azure. Si vous ne disposez pas déjà d’un abonnement Azure, vous pouvez activer vos [avantages abonnés MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou créer un [compte gratuit](https://azure.microsoft.com/pricing/free-trial/).

* Installez la dernière version de l’interface de ligne de commande Azure, disponible pour Windows, Linux ou MAC. Pour plus d’informations, consultez la page [Installation d’Azure CLI 2.0](https://docs.microsoft.com/en-us/cli/azure/install-az-cli2).

### <a name="sign-in-to-your-azure-account"></a>Connexion à votre compte Azure

Ouvrez une fenêtre de console et procédez à l’authentification à l’aide de vos informations d’identification. Pour en savoir plus, consultez Connectez-vous à Azure à partir de l’interface de ligne de commande (CLI) Azure

```azurecli
az login
```

### <a name="select-the-subscription"></a>Sélection de l’abonnement

Vérifiez les abonnements associés au compte.

```azurecli
az account list
```

### <a name="choose-which-of-your-azure-subscriptions-to-use"></a>Parmi vos abonnements Azure, choisissez celui que vous souhaitez utiliser.

```azurecli
az account set --subscription "subscription name"
```

### <a name="create-a-resource-group"></a>Créer un groupe de ressources

Azure Resource Manager requiert que tous les groupes de ressources spécifient un emplacement. Ce dernier est utilisé comme emplacement par défaut des ressources de ce groupe. Toutefois, étant donné que toutes les ressources DNS sont globales et non régionales, le choix de l’emplacement du groupe de ressources n’a aucun impact sur Azure DNS.

Ignorez cette étape si vous utilisez un groupe de ressources existant.

```azurecli
az group create --name myresourcegroup --location "West US"
```

## <a name="create-a-dns-zone"></a>Création d’une zone DNS

Une zone DNS est créée à l'aide de la commande `az network dns zone create`. Pour consulter l’aide de cette commande, tapez `az network dns zone create --help`.

L’exemple ci-dessous crée une zone DNS appelée *contoso.com* dans le groupe de ressources *MyResourceGroup*. Utilisez l’exemple pour créer une zone DNS, en remplaçant les valeurs indiquées par vos propres valeurs.

```azurecli
az network dns zone create --resource-group myresourcegroup --name contoso.com
```

## <a name="verify-your-dns-zone"></a>Vérification de votre zone DNS

### <a name="view-records"></a>Affichage des enregistrements

La création d’une zone DNS crée également les enregistrements DNS suivants :

* L’enregistrement *SOA* (Start of Authority). Il est présent à la racine de chaque zone DNS.
* Les enregistrements de serveur de noms faisant autorité (NS). Ils indiquent les serveurs de noms qui hébergent la zone. Azure DNS utilise un pool de serveurs de noms. Il se peut donc que différents serveurs de noms soient attribués à différentes zones dans Azure DNS. Pour plus d’informations, consultez [Délégation de domaine à Azure DNS](dns-domain-delegation.md).

Pour afficher ces enregistrements, utilisez `az network dns record-set list`:

```azurecli
az network dns record-set list --resource-group MyResourceGroup --zone-name contoso.com
```

La réponse à `az network dns record-set list` est présentée ci-dessous :

```json
[
  {
    "etag": "510f4711-8b7f-414e-b8d5-c0383ea3d62e",
    "id": "/subscriptions/147a22e9-2356-4e56-b3de-1f5842ae4a3b/resourceGroups/myresourcegroup/providers/Microsoft.Network/dnszones/contoso.com/NS/@",
    "metadata": null,
    "name": "@",
    "nsRecords": [
      {
        "nsdname": "ns1-04.azure-dns.com."
      },
      {
        "nsdname": "ns2-04.azure-dns.net."
      },
      {
        "nsdname": "ns3-04.azure-dns.org."
      },
      {
        "nsdname": "ns4-04.azure-dns.info."
      }
    ],
    "resourceGroup": "myresourcegroup",
    "ttl": 172800,
    "type": "Microsoft.Network/dnszones/NS"
  },
  {
    "etag": "0e48e82f-f194-4d3e-a864-7e109a95c73a",
    "id": "/subscriptions/147a22e9-2356-4e56-b3de-1f5842ae4a3b/resourceGroups/myresourcegroup/providers/Microsoft.Network/dnszones/contoso.com/SOA/@",
    "metadata": null,
    "name": "@",
    "resourceGroup": "myresourcegroup",
    "soaRecord": {
      "email": "azuredns-hostmaster.microsoft.com",
      "expireTime": 2419200,
      "host": "ns1-04.azure-dns.com.",
      "minimumTtl": 300,
      "refreshTime": 3600,
      "retryTime": 300,
      "serialNumber": 1
    },
    "ttl": 3600,
    "type": "Microsoft.Network/dnszones/SOA"
  }
]
```

> [!NOTE]
> Les jeux d’enregistrements à la racine (ou *apex*) d’une zone DNS utilisent **@** comme nom.

### <a name="test-name-servers"></a>Tester les serveurs de noms

Vous pouvez vérifier la présence de votre zone DNS sur les serveurs de noms Azure DNS à l’aide d’outils DNS comme nslookup, dig ou l’applet de commande PowerShell `Resolve-DnsName`.

Si vous n’avez pas encore délégué votre domaine pour qu’il utilise la nouvelle zone Azure DNS, vous devez diriger la requête DNS directement vers l’un des serveurs de noms pour votre zone. Les serveurs de noms de votre zone sont indiqués dans les enregistrements NS, comme indiqué par `az network dns record-set list`.

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


