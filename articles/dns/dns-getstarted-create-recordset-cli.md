---
title: "Création d’enregistrements DNS à l’aide d’Azure CLI 2.0 | Microsoft Docs"
description: "Guide de création d’enregistrements hôtes pour Azure DNS. Configuration d’enregistrements et de jeux d’enregistrements à l’aide d’Azure CLI 2.0"
services: dns
documentationcenter: na
author: georgewallace
manager: timlt
ms.assetid: 02b897d3-e83b-4257-b96d-5c29aa59e843
ms.service: dns
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.custom: H1Hack27Feb2017
ms.workload: infrastructure-services
ms.date: 02/27/2017
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: 1481fcb070f383d158c5a6ae32504e498de4a66b
ms.openlocfilehash: ca713647a9f795d7803c32eac7fba9b5b6ac95cf
ms.lasthandoff: 03/01/2017

---

# <a name="how-to-create-dns-records-using-the-azure-cli-20"></a>Guide de création d’enregistrements DNS à l’aide d’Azure CLI 2.0

> [!div class="op_single_selector"]
> * [Portail Azure](dns-getstarted-create-recordset-portal.md)
> * [PowerShell](dns-getstarted-create-recordset.md)
> * [Azure CLI 1.0](dns-getstarted-create-recordset-cli-nodejs.md)
> * [Azure CLI 2.0](dns-getstarted-create-recordset-cli.md)

Cet article vous guide tout au long du processus de création d’enregistrements et de jeux d’enregistrements à l’aide de l’interface de ligne de commande Azure.

## <a name="introduction"></a>Introduction

Avant de créer des enregistrements DNS dans Azure DNS, vous devez comprendre comment Azure DNS organise les enregistrements DNS en jeux d’enregistrements DNS.

[!INCLUDE [dns-about-records-include](../../includes/dns-about-records-include.md)]

Pour plus d’informations sur les enregistrements DNS dans Azure DNS, voir [Enregistrements et zones DNS](dns-zones-records.md).

## <a name="cli-versions-to-complete-the-task"></a>Versions de l’interface de ligne de commande permettant d’effectuer la tâche

Vous pouvez exécuter la tâche en utilisant l’une des versions suivantes de l’interface de ligne de commande (CLI) :

* [Azure CLI 1.0](dns-getstarted-create-recordset-cli-nodejs.md) : notre interface de ligne de commande pour les modèles de déploiement Classique et Resource Manager.
* [Azure CLI 2.0](dns-getstarted-create-recordset-cli.md) : notre interface de ligne de commande nouvelle génération pour le modèle de déploiement Resource Manager.

## <a name="create-a-record-set-and-record"></a>Création d’un jeu d’enregistrements et d’un enregistrement

Cette section décrit la procédure de création d’enregistrements DNS dans Azure DNS. Les exemples reposent sur l’hypothèse [que vous avez déjà installé Azure CLI 2.0, que vous êtes connecté et que vous avez créé une zone DNS](dns-getstarted-create-dnszone-cli.md).

Les exemples fournis sur cette page utilisent tous le type d’enregistrement DNS « A ». Pour plus d’informations sur les autres types d’enregistrements et sur la procédure de gestion des enregistrements et des jeux d’enregistrements DNS, consultez l’article [Création d’enregistrements et de jeux d’enregistrements DNS à l’aide d’Azure CLI 2.0](dns-operations-recordsets-cli.md).

## <a name="create-a-dns-record"></a>Créer un enregistrement DNS

Pour créer un enregistrement DNS, utilisez la commande `az network dns record-set ? add` (où ? désigne le type d’enregistrement). Pour obtenir de l’aide, consultez l’article `az network dns record-set --help`.

Lors de la création d’un enregistrement, vous devez spécifier le nom du groupe de ressources, le nom de la zone, le type d’enregistrement et les détails de l’enregistrement créé.

Si le jeu d’enregistrements n’existe pas, cette commande le crée pour vous. Si le jeu d’enregistrements existe déjà, cette commande ajoute l’enregistrement spécifié au jeu d’enregistrements existant. 

Si un jeu d’enregistrements est créé, une durée de vie (TTL) de 3600 est utilisée par défaut. Pour obtenir des instructions sur l’utilisation de différentes TTL, consultez [Création d’enregistrements et de jeux d’enregistrements DNS à l’aide d’Azure CLI 2.0](dns-operations-recordsets-cli.md).

L’exemple suivant crée un enregistrement A appelé *www* dans la zone *contoso.com* du groupe de ressources *MyResourceGroup*. L’adresse IP de l’enregistrement A est *1.2.3.4*.

```azurecli
az network dns record-set a create --resource-group myresourcegroup --zone-name contoso.com --record-set-name www --ipv4-address 1.2.3.4
```

Pour créer un jeu d’enregistrements dans l’apex de la zone (dans cet exemple, « contoso.com »), utilisez le nom d’enregistrement "@" (guillemets compris) :

```azurecli
az network dns record-set a --resource-group myresourcegroup --zone-name contoso.com --record-set-name "@" --ipv4-address 1.2.3.4
```

Les paramètres utilisés pour spécifier les données de l’enregistrement varient selon le type de l’enregistrement. Par exemple, pour un enregistrement de type « A », vous spécifiez l’adresse IPv4 avec le paramètre `--ipv4-address <IPv4 address>`. Pour obtenir la liste des paramètres correspondant aux autres types d’enregistrements, consultez l’article `az network dns record --help`. Pour des exemples de chaque type d’enregistrement, consultez [Création d’enregistrements et de jeux d’enregistrements DNS à l’aide d’Azure CLI 2.0](dns-operations-recordsets-cli.md).


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

Apprenez à [gérer les zones DNS à l’aide d’Azure CLI 2.0](dns-operations-dnszones-cli.md).

Découvrez comment [gérer des enregistrements et des jeux d’enregistrements DNS à l’aide d’Azure CLI 2.0](dns-operations-recordsets-cli.md).


