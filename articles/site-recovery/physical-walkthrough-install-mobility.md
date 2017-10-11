---
title: "Installer le service Mobilité pour un serveur physique en vue d’une réplication sur Azure | Microsoft Docs"
description: "Cet article explique comment installer l’agent du service Mobilité sur des serveurs physiques en vue d’une réplication sur Azure à l’aide du service Azure Site Recovery."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 3189fbcd-6b5b-4ffb-b5a9-e2080c37f9d9
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 06/27/2017
ms.author: raynew
ms.openlocfilehash: d73267d7a64221a3138af19e9a2d5dd15809b927
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/11/2017
---
# <a name="step-9-install-the-mobility-service"></a>Étape 9 : Installer le service Mobilité


Cet article décrit comment installer le composant du service Mobilité lors de la réplication des serveurs physiques Windows/Linux sur Azure, à l’aide du service [Azure Site Recovery](site-recovery-overview.md) dans le portail Azure.

Le service Mobilité enregistre les écritures de données sur une machine et les transmet au serveur de traitement. Ce service doit être installé sur chaque serveur que vous souhaitez répliquer sur Azure.

Vous pouvez installer le service Mobilité manuellement ou à l’aide d’une installation push à partir du serveur de traitement de Site Recovery lorsque la réplication est activée, ou à l’aide d’un outil tel que System Center Configuration Manager. Si vous utilisez l’installation push, le service est installé sur le serveur lorsque vous activez la réplication.

Publiez des commentaires et des questions au bas de cet article, ou sur le [Forum Azure Recovery Services](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="install-manually"></a>Installer manuellement

1. Vérifiez les [prérequis](site-recovery-vmware-to-azure-install-mob-svc.md#prerequisites) pour l’installation manuelle.
2. Suivez [ces instructions](site-recovery-vmware-to-azure-install-mob-svc.md#install-mobility-service-manually-by-using-the-gui) pour l’installation manuelle à l’aide du portail.
3. Si vous préférez installer à partir de la ligne de commande, suivez [ces instructions](site-recovery-vmware-to-azure-install-mob-svc.md#install-mobility-service-manually-at-a-command-prompt).

## <a name="install-from-the-process-server"></a>Installer à partir du serveur de traitement

Si vous voulez effectuer une installation push du service Mobilité à partir du serveur de processus lorsque vous activez la réplication pour une machine, vous devez disposer d’un compte pouvant être utilisé par le serveur de traitement pour accéder à la machine. Le compte est utilisé uniquement pour l’installation Push.

1. Si vous n’avez pas créé de compte, faites-le à l’aide de ces instructions :

    - Vous pouvez utiliser un compte local ou de domaine
    - Pour Windows, si vous n’utilisez pas un compte de domaine, vous devez désactiver le contrôle d’accès utilisateur distant sur la machine locale. Pour cela, dans le registre situé sous **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System**, ajoutez l’entrée DWORD **LocalAccountTokenFilterPolicy** avec une valeur de 1.
    - Si vous souhaitez ajouter l’entrée de registre pour Windows à partir d’une interface CLI, tapez :

        ```
        REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1.
        ```

    - Pour Linux, le compte doit être un utilisateur racine sur le serveur Linux source.

2. Suivez ensuite [ces instructions](site-recovery-vmware-to-azure-install-mob-svc.md#install-mobility-service-by-push-installation-from-azure-site-recovery) si vous souhaitez effectuer une transmission push du service Mobilité sur les machines virtuelles exécutant Windows ou Linux.

## <a name="other-installation-methods"></a>Autres méthodes d’installation

- [En savoir plus](site-recovery-install-mobility-service-using-sccm.md) sur l’installation du service Mobilité à l’aide de Configuration Manager
- [En savoir plus sur](site-recovery-automate-mobility-service-install.md) l’installation avec Azure Automation DSC.


## <a name="next-steps"></a>Étapes suivantes

Aller à [Étape 10 : activer la réplication](physical-walkthrough-enable-replication.md)
