---
title: "Créer un nom de domaine complet pour une machine virtuelle dans le portail Azure | Microsoft Docs"
description: "Apprenez à créer un nom de domaine complet pour une machine virtuelle Resource Manager dans le portail Azure."
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 2cd6c249-a737-4a0a-b5ba-e1c09e551b30
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 11/14/2016
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: f556fd0318accc19f0fa56fa7f2a8716ee6f1c02
ms.openlocfilehash: 9bd1032c8a831ab22bbebad8881a0f6ea434e360


---
# <a name="create-a-fully-qualified-domain-name-in-the-azure-portal"></a>Créer un nom de domaine complet dans le portail Azure
Lorsque vous créez une machine virtuelle dans le [portail Azure](https://portal.azure.com) à l’aide du modèle de déploiement Resource Manager, une ressource d’adresse IP publique est créée automatiquement pour la machine virtuelle. Vous utilisez cette adresse IP pour accéder à distance à la machine virtuelle. Bien que le portail ne crée pas de [nom de domaine complet](https://en.wikipedia.org/wiki/Fully_qualified_domain_name)(FQDN) par défaut, vous pouvez en ajouter un une fois la machine virtuelle créée. Cet article explique les étapes pour créer un nom DNS ou un nom de domaine complet.

[!INCLUDE [virtual-machines-common-portal-create-fqdn](../../includes/virtual-machines-common-portal-create-fqdn.md)]

Vous pouvez maintenant vous connecter à distance à la machine virtuelle à l’aide de ce nom DNS comme avec `ssh ops@mydns.westus.cloudapp.azure.com`.

## <a name="next-steps"></a>Étapes suivantes
Maintenant que votre machine virtuelle a un nom DNS et une IP publique, vous pouvez déployer des services ou des infrastructures d’applications tels que nginx, MongoDB, Docker, etc.

Vous pouvez également lire un autre article sur [l’utilisation de Resource Manager](../azure-resource-manager/resource-group-overview.md) pour obtenir des conseils sur la création de vos déploiements Azure.




<!--HONumber=Nov16_HO3-->


