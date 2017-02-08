---
title: "Gestion de la disponibilité des machines virtuelles Linux | Microsoft Docs"
description: "Découvrez comment utiliser plusieurs machines virtuelles afin de garantir une haute disponibilité pour votre application Linux dans Azure."
services: virtual-machines-linux
documentationcenter: 
author: cynthn
manager: timlt
editor: tysonn
tags: azure-resource-manager,azure-service-management
ms.assetid: 891c852a-84c0-4940-a61e-ada6e185bf37
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 11/14/2016
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: c18781ba99276e0aceee0d1d6f4fdf80568fca60
ms.openlocfilehash: 3c305ed5b1ba35088983050f4a5c0ac56ab3888b


---

# <a name="manage-the-availability-of-virtual-machines"></a>Gestion de la disponibilité des machines virtuelles
Découvrez comment configurer et gérer plusieurs machines virtuelles afin de garantir une haute disponibilité pour votre application Linux dans Azure. Vous pouvez également [gérer la disponibilité des machines virtuelles Windows](virtual-machines-windows-manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

Pour obtenir des instructions sur la création d’un groupe à haute disponibilité à l’aide de l’interface de ligne de commande dans le modèle de déploiement Resource Manager, consultez [azure availset : commandes pour gérer vos groupes à haute disponibilité](azure-cli-arm-commands.md#azure-availset-commands-to-manage-your-availability-sets).

[!INCLUDE [virtual-machines-common-manage-availability](../../includes/virtual-machines-common-manage-availability.md)]

## <a name="next-steps"></a>Étapes suivantes
Pour en savoir plus sur l’équilibrage de charge de vos machines virtuelles, consultez la rubrique concernant [l’équilibrage de charge des machines virtuelles](virtual-machines-linux-load-balance.md).




<!--HONumber=Nov16_HO3-->


