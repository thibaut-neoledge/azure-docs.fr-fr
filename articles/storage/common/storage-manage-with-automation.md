---
title: "Gestion d'Azure Storage à l'aide d'Azure Automation"
description: "Découvrez comment le service Azure Automation peut être utilisé pour gérer Azure Storage à grande échelle."
services: storage, automation
documentationcenter: 
author: jodoglevy
manager: eamono
editor: 
ms.assetid: bac41c39-1d95-46aa-a481-ec17bbb21b40
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/23/2016
ms.author: eamono
ms.openlocfilehash: 4649e42a628307e15f8b067503e4e8e13f16f1af
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="managing-azure-storage-using-azure-automation"></a>Gestion d'Azure Storage à l'aide d'Azure Automation
Ce guide vous présente le service Azure Automation et la manière de l'utiliser pour gérer plus simplement vos objets blob, vos tables et vos files d'attente Azure Storage.

## <a name="what-is-azure-automation"></a>Qu'est-ce qu'Azure Automation ?
[Azure Automation](https://azure.microsoft.com/services/automation/) est un service Azure destiné à simplifier la gestion du cloud via l'automatisation des processus. Azure Automation permet d'automatiser les tâches fastidieuses, manuelles, propices aux erreurs et répétitives, afin d'augmenter la fiabilité, l'efficacité et réduire le retour sur investissement pour votre organisation.

Azure Automation fournit un moteur d'exécution de workflow hautement fiable et hautement disponible, qui s'adapte à vos besoins au fur et à mesure que votre entreprise se développe. Dans Azure Automation, les processus peuvent être lancés manuellement, par des systèmes tiers ou à des intervalles planifiés, afin que les tâches interviennent exactement au moment opportun.

Réduisez les coûts opérationnels et dégagez du temps pour votre personnel informatique/DevOps afin de lui permettre de se concentrer sur des tâches générant une valeur ajoutée pour l'entreprise, en configurant Azure Automation pour exécuter automatiquement vos tâches de gestion de cloud.

## <a name="how-can-azure-automation-help-manage-azure-storage"></a>Comment Azure Automation peut-il aider à gérer Azure Storage ?
Azure Storage peut être géré dans Azure Automation à l’aide des applets de commande PowerShell disponibles dans [Azure PowerShell](https://msdn.microsoft.com/library/azure/jj156055.aspx). Azure Automation dispose dès le départ de ces applets de commande PowerShell pour Storage, de sorte que vous pouvez effectuer toutes vos tâches de gestion d'objets blob, de tables et de files d'attente au sein du service. Dans Azure Automation, vous pouvez également associer ces applets de commande à des applets de commande d'autres services Azure, afin d'automatiser des tâches complexes entre des services Azure et des systèmes tiers.

La [galerie de Runbooks d’Azure Automation](https://azure.microsoft.com/blog/2014/10/07/introducing-the-azure-automation-runbook-gallery/) contient de nombreux Runbooks d’équipe produit et de communauté pour commencer à automatiser la gestion d’Azure Storage, d’autres services Azure et de systèmes tiers. La galerie de Runbooks inclut :

* [Supprimer des objets blob de stockage Azure avec une certaine ancienneté à l’aide du service Automation](https://gallery.technet.microsoft.com/scriptcenter/Remove-Storage-Blobs-that-aae4b761)
* [Télécharger un objet blob à partir d’Azure Storage](https://gallery.technet.microsoft.com/scriptcenter/a-Blob-from-Azure-Storage-6bc13745)
* [Sauvegarder tous les disques d’une machine virtuelle Azure unique ou toutes les machines virtuelles d’un service cloud](https://gallery.technet.microsoft.com/scriptcenter/Backup-all-disks-for-a-ede940d5)

## <a name="next-steps"></a>Étapes suivantes
Maintenant que vous avez appris les bases d'Azure Automation et la manière de l'utiliser pour gérer les objets blobs, les tables et les files d'attente Azure Storage, cliquez sur ces liens pour en savoir plus sur Azure Automation.

Consultez le didacticiel Azure Automation [Création ou importation d’un runbook dans Azure Automation](../../automation/automation-creating-importing-runbook.md).

