---
title: Connecter des machines virtuelles Windows dans un service cloud | Microsoft Docs
description: "Connectez des machines virtuelles Windows créées avec le modèle de déploiement classique à un service cloud ou à un réseau virtuel Azure."
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: c1cbc802-4352-4d2e-9e49-4ccbd955324b
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 09/27/2016
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: f6537e4ebac76b9f3328223ee30647885ee15d3e
ms.openlocfilehash: 7e1002753a8bc954e11a678cefc9afd5dd5339d6


---
# <a name="connect-windows-virtual-machines-created-with-the-classic-deployment-model-with-a-virtual-network-or-cloud-service"></a>Connecter des machines virtuelles Windows créées avec le modèle de déploiement classique à un réseau virtuel ou à un service cloud
> [!IMPORTANT] 
> Azure dispose de deux modèles de déploiement différents pour créer et utiliser des ressources : [le déploiement Resource Manager et le déploiement classique](../azure-resource-manager/resource-manager-deployment-model.md). Cet article traite du modèle de déploiement classique. Pour la plupart des nouveaux déploiements, Microsoft recommande d’utiliser le modèle Resource Manager.

Les machines virtuelles Windows créées avec le modèle de déploiement classique sont toujours placées dans un service cloud. Le service cloud fait office de conteneur et fournit un nom DNS public unique, une adresse IP publique et un ensemble de points de terminaison pour accéder à la machine virtuelle via Internet. Le service cloud peut être dans un réseau virtuel, mais ce n’est pas obligatoire. Vous pouvez également [connecter des machines virtuelles Linux à un réseau virtuel ou à un service cloud](virtual-machines-linux-classic-connect-vms.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json).

Si un service cloud n’est pas dans un réseau virtuel, il est nommé « service cloud *autonome* ». Les machines virtuelles de ce service cloud autonome peuvent seulement communiquer avec d’autres machines virtuelles à l’aide des noms DNS publics de ces dernières, et ce trafic circule sur Internet. Si un service cloud se trouve dans un réseau virtuel, les machines virtuelles de ce service cloud peuvent communiquer avec toutes les autres machines virtuelles du réseau virtuel sans envoyer aucun trafic sur Internet.

Si vous placez vos machines virtuelles dans le même service cloud autonome, vous pouvez toujours utiliser l’équilibrage de charge et des groupes à haute disponibilité. Pour plus d’informations, consultez les pages [Équilibrage de charge des machines virtuelles](virtual-machines-windows-load-balance.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) et [Gestion de la disponibilité des machines virtuelles](virtual-machines-windows-manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Toutefois, vous ne pouvez pas organiser les machines virtuelles sur des sous-réseaux ou connecter un service cloud autonome à votre réseau local. Voici un exemple :

[!INCLUDE [virtual-machines-common-classic-connect-vms](../../includes/virtual-machines-common-classic-connect-vms.md)]

## <a name="next-steps"></a>Étapes suivantes
Une fois que vous avez créé une machine virtuelle, il convient de lui [ajouter un disque de données](virtual-machines-windows-classic-attach-disk.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json) pour que vos services et charges de travail disposent d’un emplacement de stockage des données. 




<!--HONumber=Dec16_HO1-->


