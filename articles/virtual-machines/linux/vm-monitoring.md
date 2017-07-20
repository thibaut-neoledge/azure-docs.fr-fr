---
title: "Activation ou désactivation de l’analyse de machine virtuelle Azure"
description: "Décrit comment activer ou désactiver l’analyse de machine virtuelle Azure"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: kmouss
manager: timlt
editor: 
ms.assetid: 6ce366d2-bd4c-4fef-a8f5-a3ae2374abcc
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/08/2016
ms.author: kmouss
ms.translationtype: Human Translation
ms.sourcegitcommit: a30a90682948b657fb31dd14101172282988cbf0
ms.openlocfilehash: 9b2fe579113d6ca6bfd27d82eb9d4657657d44ff
ms.contentlocale: fr-fr
ms.lasthandoff: 05/25/2017

---
# <a name="enable-or-disable-azure-vm-monitoring"></a>Activation ou désactivation de l’analyse de machine virtuelle Azure

Cette section décrit comment activer ou désactiver l’analyse sur les machine virtuelle exécutées sur Azure. Vous pouvez activer ou désactiver l’analyse à l’aide du portail ou de l’interface de ligne de commande Azure pour Mac, Linux et Windows (Azure CLI).

> [!IMPORTANT]
> Ce document décrit la version 2.3 de l’extension de diagnostic Linux, qui est dépréciée. La version 2.3 sera prise en charge jusqu’au 30 juin 2018.
>
> La version 3.0 de l’extension de diagnostic Linux peut être activée à la place. Pour plus d’informations, consultez [la documentation](./diagnostic-extension.md).

## <a name="enable--disable-monitoring-through-the-azure-portal"></a>Activation et désactivation de l’analyse via le portail Azure

Vous pouvez activer l’analyse de votre machine virtuelle Azure, qui fournit les données relatives à votre instance par périodes d’une minute. (des modifications du stockage s’appliquent). Les données de diagnostic détaillées sont ensuite disponibles pour la machine virtuelle dans les graphes du portail ou via l’API. Par défaut, le portail Azure active l’analyse basée sur l’hôte d’un ensemble limité d’indicateurs de performance. Vous pouvez activer l’analyse d’indicateurs de performance à partir d’une machine virtuelle alors que cette dernière est en cours d’exécution ou dans l’état Arrêté.

* Ouvrez le portail Azure à l’adresse [https://portal.azure.com](https://portal.azure.com).
* Dans la barre de navigation de gauche, cliquez sur Machines virtuelles.
* Dans la liste Machines virtuelles, sélectionnez une instance en cours d’exécution ou arrêtée. Le panneau « Machine virtuelle » s’ouvre.
* Cliquez sur Tous les paramètres.
* Cliquez sur Diagnostics.
* Modifiez le statut sur On (Activé) ou Off (Désactivé). Dans ce panneau, vous pouvez également choisir le niveau des détails d’analyse que vous souhaitez activer pour votre machine virtuelle.

![Activation et désactivation de l’analyse via le portail Azure.][1]

## <a name="enable--disable-monitoring-with-azure-cli"></a>Activation et désactivation de l’analyse avec l’interface de ligne de commande (CLI) Azure

Pour activer l’analyse pour une machine virtuelle Azure.

* Créez un fichier (nommé PrivateConfig.json, par exemple) :

```json
{
        "storageAccountName":"the storage account to receive data",
        "storageAccountKey":"the key of the account"
}
```

* Activez l’extension via Azure CLI.

```azurecli
azure vm extension set myvm LinuxDiagnostic Microsoft.OSTCExtensions 2.3 --private-config-path PrivateConfig.json
```

Pour plus d’informations, consultez [Utilisation de l’extension de diagnostic Linux pour surveiller les données de performances et de diagnostic des machines virtuelles Linux](classic/diagnostic-extension-v2.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json).

<!--Image references-->
[1]: ./media/vm-monitoring/portal-enable-disable.png

