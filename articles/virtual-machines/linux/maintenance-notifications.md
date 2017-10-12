---
title: Gestion des notifications de maintenance pour les machines virtuelles Linux dans Azure | Microsoft Docs
description: "Affichez les notifications de maintenance pour les machines virtuelles Linux s’exécutant dans Azure et démarrez la maintenance en libre-service."
services: virtual-machines-linux
documentationcenter: 
author: zivraf
manager: timlt
editor: 
tags: azure-service-management,azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 09/14/2017
ms.author: zivr
ms.openlocfilehash: 2fc96898c9b7ebefc7648b5819975d9015e76c0c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="handling-planned-maintenance-notifications-for-linux-virtual-machines"></a>Gestion de notifications de maintenance planifiées pour les machines virtuelles Linux

Azure exécute régulièrement des mises à jour afin d’améliorer la fiabilité, les performances et la sécurité de l’infrastructure hôte des machines virtuelles. Les mises à jour sont des modifications telles que la mise à jour corrective de l’environnement d’hébergement ou la mise à niveau et la désactivation de matériel. La majorité de ces mises à jour ont lieu sans affecter les machines virtuelles hébergées. Cependant, il existe des cas où les mises à jour ont un impact :

- Lorsque la maintenance ne nécessite pas de redémarrage, Azure utilise une migration sur place pour mettre en pause la machine virtuelle pendant la mise à jour de l’hôte.

- Si la maintenance nécessite un redémarrage, une notification vous dira pour quand est prévue la maintenance. Dans ces cas, vous disposez d’une période pour commencer la maintenance vous-même, au moment qui vous convient.


La maintenance planifiée nécessitant un redémarrage s’effectue par vagues. Chaque vague a une portée différente (régions).

- Une vague commence par une notification aux clients. Par défaut, la notification est envoyée aux propriétaire et copropriétaires de l’abonnement. Vous pouvez ajouter plusieurs destinataires et options de messagerie, telles que les e-mails, les SMS et les Webhooks, aux notifications. 
- Peu après la notification, une fenêtre de libre-service est définie. Pendant cet intervalle, vous pouvez rechercher quelles machines virtuelles sont incluses dans cette vague et démarrer la maintenance à l’aide d’un redéploiement proactif. 
- Après la fenêtre de libre-service, une fenêtre de maintenance planifiée commence. À ce stade, Azure planifie et applique la maintenance requise à votre machine virtuelle.  

L’objectif de ces deux fenêtres est de vous donner suffisamment de temps pour commencer la maintenance et redémarrer votre machine virtuelle tout en sachant à quel moment Azure démarrera automatiquement la maintenance.

Vous pouvez utiliser l’interface de ligne de commande Azure, PowerShell, les API REST et le portail Azure pour demander les fenêtres de maintenance pour vos machines virtuelles et démarrer la maintenance en libre-service.

 > [!NOTE]
 > Si vous essayez de démarrer la maintenance mais que cette dernière échoue, Azure marque votre machine virtuelle en tant que **ignorée** et elle n’est pas redémarrée au cours de la fenêtre de maintenance planifiée. À la place, vous êtes contacté ultérieurement avec une nouvelle planification. 

## <a name="find-vms-scheduled-for-maintenance-using-cli"></a>Trouver des machines virtuelles planifiées pour la maintenance à l’aide de l’interface de ligne de commande

Les informations de maintenance planifiée peuvent être consultées à l’aide de [azure vm get-instance-view](/cli/azure/vm?view=azure-cli-latest#az_vm_get_instance_view).
 
Les informations de maintenance sont retournées uniquement en cas de maintenance planifiée. S’il n’existe aucune maintenance planifiée qui affecte la machine virtuelle, la commande ne retourne pas d’informations de maintenance. 

```azure-cli
az vm get-instance-view  - g rgName  -n vmName 
```

Les valeurs suivantes sont retournées sous MaintenanceRedeployStatus : 

| Valeur | Description   |
|-------|---------------|
| IsCustomerInitiatedMaintenanceAllowed | Indique si vous pouvez démarrer la maintenance sur la machine virtuelle maintenant ||
| PreMaintenanceWindowStartTime         | Début de la fenêtre de maintenance en libre-service lorsque vous pouvez lancer la maintenance sur votre machine virtuelle ||
| PreMaintenanceWindowEndTime           | Fin de la fenêtre de maintenance en libre-service lorsque vous pouvez lancer la maintenance sur votre machine virtuelle ||
| MaintenanceWindowStartTime            | Début de la fenêtre de maintenance planifiée lorsque vous pouvez lancer la maintenance sur votre machine virtuelle ||
| MaintenanceWindowEndTime              | Fin de la fenêtre de maintenance planifiée lorsque vous pouvez lancer la maintenance sur votre machine virtuelle ||
| LastOperationResultCode               | Résultat de la dernière tentative de lancement de la maintenance sur la machine virtuelle ||


## <a name="start-maintenance-on-your-vm-using-cli"></a>Démarrer la maintenance sur votre machine virtuelle à l’aide de l’interface de ligne de commande

L’appel suivant lance la maintenance sur une machine virtuelle si `IsCustomerInitiatedMaintenanceAllowed` est défini sur true.

```azure-cli
az vm perform-maintenance rgName vmName 
```

[!INCLUDE [virtual-machines-common-maintenance-notifications](../../../includes/virtual-machines-common-maintenance-notifications.md)]


## <a name="next-steps"></a>Étapes suivantes

Découvrez comment vous pouvez vous inscrire aux événements de maintenance à partir de la machine virtuelle à l’aide de [Événements planifiés](scheduled-events.md).