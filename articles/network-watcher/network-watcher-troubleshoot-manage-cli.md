---
title: "Résoudre les problèmes liés à la passerelle de réseau virtuel et aux connexions Azure - Azure CLI | Microsoft Docs"
description: "Cette page explique comment utiliser Azure Network Watcher pour résoudre les problèmes liés à l’interface de ligne de commande Azure"
services: network-watcher
documentationcenter: na
author: georgewallace
manager: timlt
editor: 
ms.assetid: 2838bc61-b182-4da8-8533-27db8fdbd177
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: 757d6f778774e4439f2c290ef78cbffd2c5cf35e
ms.openlocfilehash: a213c146a9ea1bb6c23bbcbfb6353372f2e4cbfc
ms.lasthandoff: 04/10/2017


---

# <a name="troubleshoot-virtual-network-gateway-and-connections-using-azure-network-watcher-azure-cli"></a>Résoudre les problèmes liés à la passerelle de réseau virtuel et aux connexions Azure par le biais de l’interface de ligne de commande Azure d’Azure Network Watcher

> [!div class="op_single_selector"]
> - [PowerShell](network-watcher-troubleshoot-manage-powershell.md)
> - [INTERFACE DE LIGNE DE COMMANDE](network-watcher-troubleshoot-manage-cli.md)
> - [API REST](network-watcher-troubleshoot-manage-rest.md)

Le service Network Watcher offre de nombreuses fonctionnalités en lien avec la bonne compréhension de vos ressources réseau dans Azure. Il permet notamment de résoudre les problèmes liés aux ressources. La résolution des problèmes de ressources peut être appelée par l’API REST, l’interface de ligne de commande ou PowerShell. Lorsque cette fonctionnalité est appelée, Network Watcher inspecte l’intégrité d’une passerelle de réseau virtuel ou d’une connexion et renvoie ses résultats.

Cet article utilise l’interface Azure CLI 1.0 interplateforme, disponible pour Windows, Mac et Linux. Network Watcher utilise actuellement Azure CLI 1.0 pour la prise en charge d’interface CLI.

## <a name="before-you-begin"></a>Avant de commencer

Ce scénario suppose que vous ayez déjà suivi la procédure décrite dans [Create a Network Watcher (Créer une instance Network Watcher)](network-watcher-create.md) pour créer une instance Network Watcher.

## <a name="overview"></a>Vue d'ensemble

La résolution des problèmes liés aux ressources offre la possibilité de résoudre les problèmes qui surviennent avec les passerelles de réseau virtuel et les connexions. Lorsqu’une demande de résolution des problèmes liés aux ressources est faite, les journaux sont interrogés et inspectés. Lorsque l’inspection est terminée, les résultats sont renvoyés. Les demandes de résolution des problèmes liés aux ressources sont longues : vous devrez peut-être patienter plusieurs minutes avant d’obtenir un résultat. Les journaux de dépannage sont stockés dans un conteneur sur un compte de stockage spécifié.

## <a name="retrieve-a-virtual-network-gateway-connection"></a>Récupérer une connexion de passerelle de réseau virtuel

Dans cet exemple, la résolution des problèmes liés aux ressources s’effectue sur une connexion. Vous pouvez également la transférer à une passerelle de réseau virtuel. L’applet de commande suivante répertorie les connexions vpn dans un groupe de ressources.

```azurecli
azure network vpn-connection list -g resourceGroupName
```

Vous pouvez également exécuter la commande pour afficher les connexions associées à un abonnement.

```azurecli
azure network vpn-connection list -s subscription
```

Une fois que vous connaissez le nom de la connexion, vous pouvez exécuter cette commande pour obtenir son ID de ressource :

```azurecli
azure network vpn-connection show -g resourceGroupName -n connectionName
```

## <a name="create-a-storage-account"></a>Créez un compte de stockage.

La résolution des problèmes liés aux ressources renvoie des données concernant l’intégrité de la ressource, et elle enregistre les journaux dans un compte de stockage qui fera l’objet d’une révision. À cette étape, nous créons un compte de stockage. Si vous disposez déjà d’un compte de stockage existant, vous pouvez l’utiliser.

1. Créer le compte de stockage

    ```azurecli
    azure storage account create -n storageAccountName -l location -g resourceGroupName
    ```

1. Obtenir les clés du compte de stockage

    ```azurecli
    azure storage account keys list storageAccountName -g resourcegroupName
    ```

1. Créer le conteneur

    ```azurecli
    azure storage container create --account-name storageAccountName -g resourcegroupName --acount-key {storageAccountKey} --container logs
    ```

## <a name="run-network-watcher-resource-troubleshooting"></a>Procéder à la résolution des problèmes liés aux ressources Network Watcher

Résolvez les problèmes liés aux ressources avec l’applet de commande `network watcher troubleshoot`. Nous transférons à l’applet de commande le groupe de ressources, le nom de Network Watcher, l’identifiant de la connexion, l’identifiant du compte de stockage, et le chemin d’accès à l’objet blob pour y stocker les résultats de résolution des problèmes.

```azurecli
azure network watcher -g resourceGroupName -n networkWatcherName -t connectionId -i storageId -p storagePath
```

Une fois l’applet de commande exécutée, Network Watcher passe en revue la ressource pour vérifier son intégrité. Les résultats sont renvoyés à l’interpréteur de commandes, et les journaux des résultats sont stockés dans le compte de stockage spécifié.

## <a name="understanding-the-results"></a>Compréhension des résultats

Le texte d’action fournit des indications générales sur la façon de résoudre le problème. Si une action peut être entreprise pour résoudre le problème, un lien est fourni avec des indications supplémentaires. En l’absence d’indications supplémentaires, la réponse fournit l’URL permettant d’ouvrir un dossier de support.  Pour plus d’informations sur les propriétés de la réponse et sur ce qu’elle contient, consultez [Network Watcher Troubleshoot overview (Vue d’ensemble de la résolution des problèmes Network Watcher)](network-watcher-troubleshoot-overview.md)

Pour obtenir des instructions de téléchargement des fichiers à partir des comptes de stockage Azure, consultez [Prise en main du stockage d’objets blob Azure à l’aide de .NET](../storage/storage-dotnet-how-to-use-blobs.md). L’explorateur de stockage peut aussi être utilisé. Pour en savoir plus sur l’explorateur de stockage, cliquez sur le lien suivant : [Explorateur de stockage](http://storageexplorer.com/)

## <a name="next-steps"></a>Étapes suivantes

Si les paramètres ont été modifiés et arrêtent la connectivité VPN, consultez la page [Gérer les groupes de sécurité réseau à partir du portail](../virtual-network/virtual-network-manage-nsg-arm-portal.md) afin d’effectuer le suivi du groupe de sécurité réseau et des règles de sécurité pouvant être concernés.

