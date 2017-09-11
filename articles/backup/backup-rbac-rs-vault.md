---
title: "Gérer des sauvegardes avec le contrôle d’accès en fonction du rôle | Microsoft Docs"
description: "Le contrôle d’accès en fonction du rôle permet de gérer l’accès aux opérations de gestion de sauvegarde dans le coffre Recovery Services."
services: backup
documentationcenter: 
author: trinadhk
manager: shreeshd
editor: 
ms.assetid: 3bd46b97-4b29-47a5-b5ac-ac174dd36760
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 8/22/2017
ms.author: trinadhk;markgal
ms.translationtype: HT
ms.sourcegitcommit: cf381b43b174a104e5709ff7ce27d248a0dfdbea
ms.openlocfilehash: d0b6eb8eea8971eb8f80c6623f9a41a3692241b3
ms.contentlocale: fr-fr
ms.lasthandoff: 08/23/2017

---

# <a name="use-role-based-access-control-to-manage-azure-backup-recovery-points"></a>Utiliser le contrôle d’accès en fonction du rôle pour gérer les points de récupération Sauvegarde Azure
Le contrôle d’accès en fonction du rôle (RBAC) Azure permet une gestion précise de l’accès pour Azure. Avec le contrôle d’accès en fonction du rôle, vous pouvez séparer les tâches au sein de votre équipe et accorder aux utilisateurs uniquement les accès nécessaires pour accomplir leur travail.

> [!IMPORTANT]
> Les rôles fournis par Azure Backup sont limités à des actions qui peuvent être effectuées avec des applets de commande PowerShell dans le portail Azure ou Recovery Services. Les actions effectuées dans l’interface cliente de l’agent Azure Backup, l’interface System Center Data Protection Manager ou l’interface de serveur de sauvegarde Azure ne sont pas contrôlées par ces rôles.

Azure Backup fournit 3 rôles intégrés pour contrôler les opérations de gestion des sauvegardes. En savoir plus sur les [rôles intégrés Azure RBAC](../active-directory/role-based-access-built-in-roles.md)

* [Contributeur de sauvegarde](../active-directory/role-based-access-built-in-roles.md#backup-contributor) : ce rôle dispose de toutes les autorisations pour créer et gérer des sauvegardes, à l’exception de la création du coffre Recovery Services et de l’octroi d’un accès à d’autres personnes. Imaginez ce rôle comme un administrateur de gestion des sauvegardes qui peut exécuter chaque opération de gestion des sauvegardes.
* [Opérateur de sauvegarde](../active-directory/role-based-access-built-in-roles.md#backup-operator) : ce rôle dispose des autorisations généralement accordées à un contributeur à l’exception de la suppression de sauvegardes et de la gestion des stratégies de sauvegarde. Ce rôle est équivalent au contributeur, mais il ne peut pas effectuer d’opérations destructrices telles que l’arrêt de la sauvegarde avec suppression des données ou la suppression de l’enregistrement de ressources locales.
* [Lecteur de sauvegarde](../active-directory/role-based-access-built-in-roles.md#backup-reader) : ce rôle dispose des autorisations pour afficher toutes les opérations de gestion des sauvegardes. Imaginez ce rôle comme un responsable de surveillance.

Si vous avez besoin de définir vos propres rôles pour un meilleur contrôle, découvrez comment créer [des rôles personnalisés dans RBAC Azure](../active-directory/role-based-access-control-custom-roles.md).



## <a name="mapping-backup-built-in-roles-to-backup-management-actions"></a>Mappage des rôles de sauvegarde intégrés pour les actions de gestion des sauvegardes
Le tableau suivant répertorie les actions de gestion des sauvegardes et le rôle RBAC minimum nécessaire correspondant pour effectuer cette opération.

| Opération de gestion | Rôle RBAC minimum nécessaire |
| --- | --- |
| Créer un coffre Recovery Services | Contributeur sur le groupe de ressources de coffre |
| Activer la sauvegarde des machines virtuelles Azure | Opérateur de sauvegarde sur le coffre, contributeur de machine virtuelle sur des machines virtuelles |
| Sauvegarde de machine virtuelle à la demande | Opérateur de sauvegarde |
| Restaurer une machine virtuelle | Opérateur de sauvegarde, collaborateur de groupe de ressources dans lequel des réseaux virtuels et la machine virtuelle vont être déployés |
| Restaurer des disques et des fichiers individuels à partir d’une sauvegarde de machine virtuelle | Opérateur de sauvegarde |
| Créer une stratégie de sauvegarde pour la sauvegarde de machine virtuelle Azure | Contributeur de sauvegarde |
| Modifier une stratégie de sauvegarde pour la sauvegarde de machine virtuelle Azure | Contributeur de sauvegarde |
| Supprimer une stratégie de sauvegarde pour la sauvegarde de machine virtuelle Azure | Contributeur de sauvegarde |
| Arrêt de la sauvegarde (avec conservation ou suppression des données) lors la sauvegarde d’une machine virtuelle | Contributeur de sauvegarde |
| Inscrire un Windows Server/client/SCDPM ou un serveur de sauvegarde Azure | Opérateur de sauvegarde |
| Supprimer un Windows Server/client/SCDPM inscrit ou un serveur de sauvegarde Azure | Contributeur de sauvegarde |

## <a name="next-steps"></a>Étapes suivantes
* [Utiliser les attributions de rôle pour gérer l’accès à vos ressources Azure Active Directory](../active-directory/role-based-access-control-configure.md): découvrez le contrôle d’accès en fonction du rôle Azure dans le portail Azure.
* Découvrez comment gérer l’accès avec :
  * [PowerShell](../active-directory/role-based-access-control-manage-access-powershell.md)
  * [Interface de ligne de commande Azure](../active-directory/role-based-access-control-manage-access-azure-cli.md)
  * [API REST](../active-directory/role-based-access-control-manage-access-rest.md)
* [Résolution des problèmes de contrôle d’accès en fonction du rôle](../active-directory/role-based-access-control-troubleshooting.md): obtenez des suggestions pour résoudre les problèmes courants.

