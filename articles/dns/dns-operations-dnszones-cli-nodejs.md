---
title: "Gérer des zones DNS dans Azure DNS - Azure CLI 1.0 | Microsoft Docs"
description: "Vous pouvez gérer des zones DNS à l’aide d’Azure CLI 1.0. Cet article explique comment mettre à jour, supprimer et créer des zones DNS sur Azure DNS."
services: dns
documentationcenter: na
author: georgewallace
manager: timlt
ms.assetid: 8ab63bc4-5135-4ed8-8c0b-5f0712b9afed
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/21/2016
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: 36fa9cd757b27347c08f80657bab8a06789a3c2f
ms.openlocfilehash: 9b545937f3e375dfcef815a66263a57bd5042f69
ms.lasthandoff: 02/27/2017

---

# <a name="how-to-manage-dns-zones-in-azure-dns-using-the-azure-cli-10"></a>Gestion des zones DNS dans Azure DNS à l’aide d’Azure CLI 1.0

> [!div class="op_single_selector"]
> * [Interface de ligne de commande Azure](dns-operations-dnszones-cli.md)
> * [PowerShell](dns-operations-dnszones.md)

Ce guide montre comment gérer vos zones DNS à l’aide de l’interface de ligne de commande Azure CLI 1.0 multiplateforme, qui est disponible pour Windows, Mac et Linux. Vous pouvez également gérer vos zones DNS à l’aide [d’Azure PowerShell](dns-operations-dnszones.md) ou du portail Azure.

## <a name="cli-versions-to-complete-the-task"></a>Versions de l’interface de ligne de commande permettant d’effectuer la tâche

Vous pouvez exécuter la tâche en utilisant l’une des versions suivantes de l’interface de ligne de commande (CLI) :

* [Azure CLI 1.0](dns-operations-dnszones-cli-nodejs.md) : notre interface de ligne de commande pour les modèles de déploiement Classique et Resource Manager.
* [Azure CLI 2.0](dns-operations-dnszones-cli.md) : notre interface de ligne de commande nouvelle génération pour le modèle de déploiement Resource Manager.

## <a name="introduction"></a>Introduction

[!INCLUDE [dns-create-zone-about](../../includes/dns-create-zone-about-include.md)]

[!INCLUDE [dns-cli-setup](../../includes/dns-cli-setup-include.md)]

## <a name="getting-help"></a>Obtenir de l’aide

Toutes les commandes CLI 1.0 liées à Azure DNS commencent par `azure network dns`. Une aide est disponible pour chaque commande avec l’option `--help` (forme abrégée : `-h`).  Par exemple :

```azurecli
azure network dns -h
azure network dns zone -h
azure network dns zone create -h
```

## <a name="create-a-dns-zone"></a>Création d’une zone DNS

Une zone DNS est créée à l'aide de la commande `azure network dns zone create`. Pour obtenir de l’aide, consultez l’article `azure network dns zone create -h`.

L’exemple ci-dessous crée une zone DNS appelée *contoso.com* dans le groupe de ressources *MyResourceGroup* :

```azurecli
azure network dns zone create MyResourceGroup contoso.com
```

### <a name="to-create-a-dns-zone-with-tags"></a>Créer une zone DNS avec des balises

L’exemple suivant montre comment créer une zone DNS avec deux [balises Azure Resource Manager](dns-zones-records.md#tags), *projet = demo* et *env = test*, à l’aide du paramètre `--tags` (forme abrégée : `-t`) :

```azurecli
azure network dns zone create MyResourceGroup contoso.com -t "project=demo";"env=test"
```

## <a name="get-a-dns-zone"></a>Obtention d’une zone DNS

Pour récupérer une zone DNS, utilisez `azure network dns zone show`. Pour obtenir de l’aide, consultez l’article `azure network dns zone show -h`.

L’exemple suivant retourne la zone DNS *contoso.com* et les données associées à partir du groupe de ressources *MyResourceGroup*. 

```azurecli
azure network dns zone show MyResourceGroup contoso.com
```

L’exemple suivant est la réponse.

```
info:    Executing command network dns zone show
+ Looking up the dns zone "contoso.com"
data:    Id                              : /subscriptions/.../contoso.com
data:    Name                            : contoso.com
data:    Type                            : Microsoft.Network/dnszones
data:    Location                        : global
data:    Number of record sets           : 2
data:    Max number of record sets       : 5000
data:    Name servers:
data:        ns1-01.azure-dns.com.
data:        ns2-01.azure-dns.net.
data:        ns3-01.azure-dns.org.
data:        ns4-01.azure-dns.info.
data:    Tags                            : project=demo;env=test
info:    network dns zone show command OK
```

Notez que les enregistrements DNS ne sont pas retournés par `azure network dns zone show`. Pour lister les enregistrements DNS, utilisez `azure network dns record-set list`.


## <a name="list-dns-zones"></a>Création de la liste des zones DNS

Pour énumérer des zones DNS, utilisez `azure network dns zone list`. Pour obtenir de l’aide, consultez l’article `azure network dns zone list -h`.

Spécifier le groupe de ressources permet de ne lister que les zones du groupe de ressources :

```azurecli
azure network dns zone list MyResourceGroup
```

Omettre le groupe de ressources permet de lister toutes les zones de l’abonnement :

```azurecli
azure network dns zone list 
```

## <a name="update-a-dns-zone"></a>Mise à jour d’une zone DNS

Vous pouvez apporter des modifications à une ressource de zone DNS à l’aide de `azure network dns zone set`. Pour obtenir de l’aide, consultez l’article `azure network dns zone set -h`.

Cette commande ne met pas à jour les jeux d’enregistrements DNS dans la zone (voir [Gestion des enregistrements DNS](dns-operations-recordsets-cli-nodejs.md)). Elle est utilisée uniquement pour mettre à jour les propriétés de la ressource de zone elle-même. Ces propriétés sont actuellement limitées aux [« balises » Azure Resource Manager](dns-zones-records.md#tags) de la ressource de zone.

L’exemple suivant montre comment mettre à jour les balises sur une zone DNS. Les balises existantes sont remplacées par la valeur spécifiée.

```azurecli
azure network dns zone set MyResourceGroup contoso.com -t "team=support"
```

## <a name="delete-a-dns-zone"></a>Suppression d’une zone DNS

Les zones DNS peuvent être supprimées en utilisant `azure network dns zone delete`. Pour obtenir de l’aide, consultez l’article `azure network dns zone delete -h`.

> [!NOTE]
> Supprimer une zone DNS supprime également tous les enregistrements DNS de la zone. Il est impossible d’annuler cette opération. Si la zone DNS est en cours d’utilisation, les services utilisant la zone échouent lors de la suppression de la zone.
>
>Pour vous protéger contre la suppression accidentelle de zones, consultez la page [Comment protéger des zones et enregistrements DNS](dns-protect-zones-recordsets.md).

Cette commande vous demande de confirmer l’opération. Le commutateur `--quiet` facultatif (forme abrégée : `-q`) supprime cette invite.

L’exemple suivant montre comment supprimer la zone *contoso.com* à partir du groupe de ressources *MyResourceGroup*.

```azurecli
azure network dns zone delete MyResourceGroup contoso.com
```

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment [gérer des jeux d’enregistrements et des enregistrements](dns-getstarted-create-recordset-cli-nodejs.md) dans votre zone DNS.

Découvrez comment [déléguer votre domaine à Azure DNS](dns-domain-delegation.md).


