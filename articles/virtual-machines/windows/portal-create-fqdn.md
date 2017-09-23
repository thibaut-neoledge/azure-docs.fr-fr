---
title: "Créer un nom de domaine complet pour une machine virtuelle Windows dans le portail Azure | Microsoft Docs"
description: "Apprenez à créer un nom de domaine complet pour une machine virtuelle Resource Manager dans le portail Azure."
services: virtual-machines-windows
documentationcenter: 
author: iainfoulds
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: a2ae5887-76df-485e-ae19-0efd96df8600
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 07/05/2017
ms.author: iainfou
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.openlocfilehash: 2d5a555cd873222efcdb29e8eb3aaf128a24414b
ms.contentlocale: fr-fr
ms.lasthandoff: 07/08/2017

---
# <a name="create-a-fully-qualified-domain-name-in-the-azure-portal-for-a-windows-vm"></a>Créer un nom de domaine complet dans le portail Azure pour une machine virtuelle Windows

Lorsque vous créez une machine virtuelle dans le [portail Azure](https://portal.azure.com), une ressource d’adresse IP publique est créée automatiquement pour la machine virtuelle. Vous utilisez cette adresse IP pour accéder à distance à la machine virtuelle. Bien que le portail ne crée pas de [nom de domaine complet](https://en.wikipedia.org/wiki/Fully_qualified_domain_name) (FQDN), vous pouvez en créer un une fois la machine virtuelle créée. Cet article explique les étapes pour créer un nom DNS ou un nom de domaine complet.

## <a name="create-a-fqdn"></a>Créer un nom de domaine complet
Cet article suppose que vous avez déjà créé une machine virtuelle. Si nécessaire, vous pouvez [créer une machine virtuelle dans le portail](quick-create-portal.md) ou [avec Azure PowerShell](quick-create-powershell.md). Une fois que votre machine virtuelle est en cours d’exécution, procédez comme suit :

[!INCLUDE [virtual-machines-common-portal-create-fqdn](../../../includes/virtual-machines-common-portal-create-fqdn.md)]

Vous pouvez maintenant vous connecter à distance à la machine virtuelle à l’aide de ce nom DNS comme pour Remote Desktop Protocol (RDP).

## <a name="next-steps"></a>Étapes suivantes
Maintenant que votre machine virtuelle a un nom DNS et une IP publique, vous pouvez déployer des infrastructures d’applications courantes ou des services tels qu’IIS, SQL ou SharePoint.

Vous pouvez également lire un autre article sur [l’utilisation de Resource Manager](../../azure-resource-manager/resource-group-overview.md) pour obtenir des conseils sur la création de vos déploiements Azure.


