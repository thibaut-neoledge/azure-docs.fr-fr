---
title: "Création d’enregistrements DNS à l’aide de l’interface de ligne de commande Azure | Microsoft Docs"
description: "Création d’enregistrements hôtes pour Azure DNS. Configuration d’enregistrements et de jeux d’enregistrements à l’aide de l’interface de ligne de commande"
services: dns
documentationcenter: na
author: georgewallace
manager: timlt
ms.assetid: 02b897d3-e83b-4257-b96d-5c29aa59e843
ms.service: dns
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/21/2016
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: 18a21cdc0f9641356dfaf6f6d93edfcac11af210
ms.openlocfilehash: 790af1544ed86155f5f864f3914b5fd1c4f42f4b

---

# <a name="create-dns-records-using-the-azure-cli"></a>Création d’enregistrements DNS à l’aide de l’interface de ligne de commande Azure

> [!div class="op_single_selector"]
> * [portail Azure](dns-getstarted-create-recordset-portal.md)
> * [PowerShell](dns-getstarted-create-recordset.md)
> * [Interface de ligne de commande Azure](dns-getstarted-create-recordset-cli.md)

Cet article vous guide tout au long du processus de création d’enregistrements et de jeux d’enregistrements à l’aide de l’interface de ligne de commande Azure.

## <a name="introduction"></a>Introduction

Avant de créer des enregistrements DNS dans Azure DNS, vous devez comprendre comment Azure DNS organise les enregistrements DNS en jeux d’enregistrements DNS.

[!INCLUDE [dns-about-records-include](../../includes/dns-about-records-include.md)]

Pour plus d’informations sur les enregistrements DNS dans Azure DNS, voir [Enregistrements et zones DNS](dns-zones-records.md).

## <a name="create-a-record-set-and-record"></a>Création d’un jeu d’enregistrements et d’un enregistrement

Cette section décrit la procédure de création d’enregistrements DNS dans Azure DNS. Les exemples reposent sur l’hypothèse [que vous avez déjà installé l’interface de ligne de commande Azure, que vous êtes connecté et que vous avez créé une zone DNS](dns-getstarted-create-dnszone-cli.md).

Les exemples fournis sur cette page utilisent tous le type d’enregistrement DNS « A ». Pour plus d’informations sur les autres types d’enregistrements et sur la procédure de gestion des enregistrements et des jeux d’enregistrements DNS, consultez l’article [Création d’enregistrements et de jeux d’enregistrements DNS à l’aide de l’interface de ligne de commande](dns-operations-recordsets-cli.md).

## <a name="create-a-dns-record"></a>Créer un enregistrement DNS

Pour créer un enregistrement DNS, utilisez la commande `azure network dns record-set add-record`. Pour obtenir de l’aide, consultez l’article `azure network dns record-set add-record -h`.

Lors de la création d’un enregistrement, vous devez spécifier le nom du groupe de ressources, le nom de la zone, le type d’enregistrement et les détails de l’enregistrement créé.

Si le jeu d’enregistrements n’existe pas, cette commande le crée pour vous. Si le jeu d’enregistrements existe déjà, cette commande ajoute l’enregistrement spécifié au jeu d’enregistrements existant. 

Si un jeu d’enregistrements est créé, une durée de vie (TTL) de 3600 est utilisée par défaut. Pour obtenir des instructions sur l’utilisation de différentes TTL, consultez [Création d’enregistrements et de jeux d’enregistrements DNS à l’aide de l’interface de ligne de commande Azure](dns-operations-recordsets-cli.md).

L’exemple suivant crée un enregistrement A appelé *www* dans la zone *contoso.com* du groupe de ressources *MyResourceGroup*. L’adresse IP de l’enregistrement A est *1.2.3.4*.

```azurecli
azure network dns record-set add-record MyResourceGroup contoso.com www A -a 1.2.3.4
```

Pour créer un jeu d’enregistrements à l’apex de la zone (en l’occurrence, « contoso.com »), utilisez le nom d’enregistrement "@", (guillemets compris).

```azurecli
azure network dns record-set add-record MyResourceGroup contoso.com "@" A -a 1.2.3.4
```

Les paramètres utilisés pour spécifier les données de l’enregistrement varient selon le type de l’enregistrement. Par exemple, pour un enregistrement de type « A », vous spécifiez l’adresse IPv4 avec le paramètre `-a <IPv4 address>`. Pour obtenir la liste des paramètres correspondant aux autres types d’enregistrements, consultez l’article `azure network dns record-set add-record -h`. Pour des exemples de chaque type d’enregistrement, consultez [Création d’enregistrements et de jeux d’enregistrements DNS à l’aide de l’interface de ligne de commande Azure](dns-operations-recordsets-cli.md).


## <a name="verify-name-resolution"></a>Vérifier la résolution de noms

Vous pouvez vérifier la présence de vos enregistrements DNS sur les serveurs de noms Azure DNS à l’aide d’outils DNS comme nslookup, dig ou [l’applet de commande PowerShell Resolve-DnsName](https://technet.microsoft.com/library/jj590781.aspx).

Si vous n’avez pas encore délégué votre domaine pour qu’il utilise la nouvelle zone dans Azure DNS, vous devez [diriger la requête DNS directement vers l’un des serveurs de noms pour votre zone](dns-getstarted-create-dnszone.md#test-name-servers). Veillez à indiquer les valeurs correctes pour votre zone d’enregistrements dans la commande ci-dessous.

```
nslookup
> set type=A
> server ns1-01.azure-dns.com
> www.contoso.com

Server:  ns1-01.azure-dns.com
Address:  40.90.4.1

Name:    www.contoso.com
Address:  1.2.3.4
```

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment [déléguer votre nom de domaine aux serveurs de noms Azure DNS](dns-domain-delegation.md).

Apprenez à [gérer les zones DNS à l’aide de l’interface de ligne de commande Azure](dns-operations-dnszones-cli.md).

Découvrez comment [gérer des enregistrements et des jeux d’enregistrements DNS à l’aide de l’interface de ligne de commande Azure](dns-operations-recordsets-cli.md).




<!--HONumber=Feb17_HO2-->


