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
translationtype: Human Translation
ms.sourcegitcommit: 356de369ec5409e8e6e51a286a20af70a9420193
ms.openlocfilehash: 1f15a77304ccf4fdef995d216817363fbf3f6e73
ms.lasthandoff: 03/27/2017


---
# <a name="enable-or-disable-azure-vm-monitoring"></a>Activation ou désactivation de l’analyse de machine virtuelle Azure
Cette section décrit comment activer ou désactiver l’analyse sur les machine virtuelle exécutées sur Azure. Par défaut, l’analyse est activée sur les machines virtuelles Azure si elles sont déployées à partir du [Portail Azure](https://portal.azure.com). Les graphiques de surveillance sont disponibles par défaut avec un délai d’une minute. Vous pouvez activer ou désactiver l’analyse à l’aide du portail ou de l’interface de ligne de commande Azure pour Mac, Linux et Windows (Azure CLI). 

## <a name="enable--disable-monitoring-through-the-azure-portal"></a>Activation et désactivation de l’analyse via le portail Azure
Vous pouvez activer l’analyse de votre machine virtuelle Azure, qui fournit les données relatives à votre instance par périodes d’une minute. (des modifications du stockage s’appliquent). Les données de diagnostic détaillées sont ensuite disponibles pour la machine virtuelle dans les graphiques du portail ou via l’API. Par défaut, le portail Azure active l’analyse, mais vous pouvez désactiver cette option comme décrit ci-dessous. Vous pouvez activer l’analyse alors que la machine virtuelle est en cours d’exécution ou en état arrêté.

* Ouvrez le Portail Azure à l’adresse **[https://portal.azure.com](https://portal.azure.com)**
* Dans la barre de navigation de gauche, cliquez sur Machines virtuelles.
* Dans la liste Machines virtuelles, sélectionnez une instance en cours d’exécution ou arrêtée. Le panneau Machine virtuelle s’ouvre.
* Cliquez sur « Tous les paramètres ».
* Cliquez sur « Diagnostics ».
* Modifiez le statut sur On (Activé) ou Off (Désactivé). Dans ce panneau, vous pouvez également choisir le niveau des détails d’analyse que vous souhaitez activer pour votre machine virtuelle.

[Azure.Note] Le commutateur Diagnostics activés constitue la valeur par défaut lorsque vous créez une machine virtuelle

![Activation et désactivation de l’analyse via le portail Azure.][1]

## <a name="enable--disable-monitoring-with-azure-cli"></a>Activation ou désactivation de l’analyse avec l’interface de ligne de commande (CLI) Azure
Pour activer l’analyse pour une machine virtuelle Azure.

* Créez un fichier nommé PrivateConfig.json avec le contenu suivant.
        { "storageAccountName":"compte de stockage qui reçoit les données", "storageAccountKey":"clé du compte" }
* Exécutez la commande d’interface de ligne de commande Azure suivante.
  
        azure vm extension set myvm LinuxDiagnostic Microsoft.OSTCExtensions 2.0 --private-config-path PrivateConfig.json

[Azure.Note] Vous pouvez passer de la version 2.0 à une version ultérieure (le cas échéant). 

Pour plus d’informations sur la configuration des mesures d’analyse et pour obtenir des exemples, consultez le document - **[Utilisation de l’extension de diagnostic Linux pour surveiller les données de performances et de diagnostic des machines virtuelles Linux](linux/classic/diagnostic-extension.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json).

<!--Image references-->
[1]: ./media/virtual-machines-linux-vm-monitoring/portal-enable-disable.png



