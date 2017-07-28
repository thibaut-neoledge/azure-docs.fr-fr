---
title: Exemples PowerShell pour les machines virtuelles Azure | Microsoft Docs
description: Exemples PowerShell pour les machines virtuelles Azure
services: virtual-machines-windows
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 05/05/2017
ms.author: nepeters
ms.custom: mvc
ms.translationtype: Human Translation
ms.sourcegitcommit: 09f24fa2b55d298cfbbf3de71334de579fbf2ecd
ms.openlocfilehash: f342488caee0f0eb4b3350915704714f0f1dbc20
ms.contentlocale: fr-fr
ms.lasthandoff: 06/07/2017

---
# <a name="azure-virtual-machine-powershell-samples"></a>Exemples PowerShell pour les machines virtuelles Azure

Le tableau suivant contient des liens vers des exemples de scripts PowerShell qui créent et gèrent des machines virtuelles Windows.

| | |
|---|---|
|**Créer des machines virtuelles**||
| [Créer une machine virtuelle entièrement configurée](./../scripts/virtual-machines-windows-powershell-sample-create-vm.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Crée un groupe de ressources, une machine virtuelle et toutes les ressources associées.|
| [Créer des machines virtuelles hautement disponibles](./../scripts/virtual-machines-windows-powershell-sample-create-nlb-vm.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Crée plusieurs machines virtuelles dans une configuration haute disponibilité avec équilibrage de charge.|
| [Créer une machine virtuelle et exécuter le script de configuration](./../scripts/virtual-machines-windows-powershell-sample-create-vm-iis.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Crée une machine virtuelle et utilise l’extension de script personnalisé Azure pour installer IIS. |
| [Créer une machine virtuelle et exécuter la configuration DSC](./../scripts/virtual-machines-windows-powershell-sample-create-iis-using-dsc.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Crée une machine virtuelle et utilise l’extension DSC Azure pour installer IIS. |
| [Charger un disque dur virtuel et créer des machines virtuelles](./../scripts/virtual-machines-windows-powershell-upload-generalized-script.md) | Charge un fichier de disque dur virtuel local sur Azure, crée une image à partir du disque dur virtuel, puis crée une machine virtuelle à partir de cette image. |
| [Créer une machine virtuelle à partir d’un disque de système d’exploitation géré](./../scripts/virtual-machines-windows-powershell-sample-create-vm-from-managed-os-disks.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Crée une machine virtuelle en attachant un disque géré existant en tant que disque de système d’exploitation. |
| [Créer une machine virtuelle à partir d’une capture instantanée](./../scripts/virtual-machines-windows-powershell-sample-create-vm-from-snapshot.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Crée une machine virtuelle à partir d’une capture instantanée en créant un disque géré à partir de la capture instantanée, puis en attachant le nouveau disque géré en tant que disque de système d’exploitation. |
|**Gérer le stockage**||
| [Créer un disque géré à partir d’un disque dur virtuel figurant dans le même abonnement ou dans un abonnement différent](./../../storage/scripts/storage-windows-powershell-sample-create-managed-disk-from-vhd.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Crée un disque géré à partir d’un disque dur virtuel spécialisé tel qu’un disque de système d’exploitation, ou d’un disque dur virtuel de données figurant dans le même abonnement ou dans un abonnement différent.  |
| [Créer un disque géré à partir d’une capture instantanée](./../../storage/scripts/storage-windows-powershell-sample-create-managed-disk-from-snapshot.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Crée un disque géré à partir d’une capture instantanée. |
| [Copier le disque géré vers le même abonnement ou un abonnement différent](./../../storage/scripts/storage-windows-powershell-sample-copy-managed-disks-to-same-or-different-subscription.md?toc=%2fcli%2fmodule%2ftoc.json) | Copie le disque géré vers le même abonnement ou un abonnement différent, mais dans la même région que le disque géré parent. 
| [Exporter une capture instantanée en tant que disque dur virtuel vers un compte de stockage](./../../storage/scripts/storage-windows-powershell-sample-copy-snapshot-to-storage-account.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Exporte une capture instantanée gérée en tant que disque dur virtuel vers un compte de stockage dans une région différente. |
| [Créer une capture instantanée d’un disque dur virtuel](./../../storage/scripts/storage-windows-powershell-sample-create-snapshot-from-vhd.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Crée une capture instantanée d’un disque dur virtuel pour créer rapidement plusieurs disques gérés identiques à partir de cette capture instantanée.  |
| [Copier la capture instantanée vers le même abonnement ou un abonnement différent](./../../storage/scripts/storage-windows-powershell-sample-copy-snapshot-to-same-or-different-subscription.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Copie la capture instantanée vers le même abonnement ou un abonnement différent, mais dans la même région que la capture instantanée parente. |
|**Sécuriser les machines virtuelles**||
| [Chiffrer une machine virtuelle et des disques de données](./../scripts/virtual-machines-windows-powershell-sample-encrypt-vm.md?toc=%2fpowershell%2fazure%2ftoc.json) | Crée une solution Key Vault, une clé de chiffrement et le principal de service, puis chiffre une machine virtuelle. |
|**Surveiller les machines virtuelles**||
| [Surveiller une machine virtuelle avec Operations Management Suite](./../scripts/virtual-machines-windows-powershell-sample-create-vm-oms.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Crée une machine virtuelle, installe l’agent Operations Management Suite et inscrit la machine virtuelle dans un espace de travail OMS.  |
| | |


