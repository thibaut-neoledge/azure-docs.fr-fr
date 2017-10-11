---
title: "Installer le service Mobilité pour VMware en vue d’une réplication sur Azure | Microsoft Docs"
description: "Cet article explique comment installer l’agent du service Mobilité pour VMware en vue d’une réplication sur Azure à l’aide du service Azure Site Recovery."
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
ms.openlocfilehash: bc520bd2ea54208889861a7a3b275e3008a05d53
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/11/2017
---
# <a name="step-10-install-the-mobility-service"></a>Étape 10 : installer le service Mobilité


Cet article explique comment configurer des paramètres source et cible lors de la réplication de machines virtuelles VMware locales sur Azure à l’aide du service [Azure Site Recovery](site-recovery-overview.md) dans le portail Azure.

Le service Mobilité enregistre les écritures de données sur une machine et les transmet au serveur de traitement. Ce service devrait être installé sur chaque machine que vous souhaitez répliquer sur Azure.

Vous pouvez installer le manuel du service Mobilité, à l’aide d’une installation push à partir du serveur de traitement de Site Recovery lorsque la réplication est activée, ou utiliser un outil de System Center Configuration Manager. Si vous utilisez l’installation push, le service est installé sur la machine virtuelle lorsque la réplication est activée.

Publiez des commentaires et des questions au bas de cet article, ou sur le [Forum Azure Recovery Services](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="install-manually"></a>Installer manuellement

1. Vérifiez les [prérequis](site-recovery-vmware-to-azure-install-mob-svc.md#prerequisites) pour l’installation manuelle.
2. Suivez [ces instructions](site-recovery-vmware-to-azure-install-mob-svc.md#install-mobility-service-manually-by-using-the-gui) pour l’installation manuelle à l’aide du portail.
3. Si vous préférez installer à partir de la ligne de commande, suivez [ces instructions](site-recovery-vmware-to-azure-install-mob-svc.md#install-mobility-service-manually-at-a-command-prompt).

## <a name="install-from-the-process-server"></a>Installer à partir du serveur de processus

Si vous voulez effectuer une installation push du service Mobilité à partir du serveur de processus lorsque la réplication est activée, vous devez disposer d’un compte pouvant être utilisé par le serveur de processus pour accéder à la machine virtuelle. Le compte est utilisé uniquement pour l’installation Push.

1. Vous devriez avoir [créé un compte](vmware-walkthrough-prepare-vmware.md) qui peut être utilisé pour l’installation push. Vous pouvez ensuite spécifier le compte que vous souhaitez utiliser lors de la configuration des paramètres source pendant le déploiement de Site Recovery.
2. Suivez ensuite [ces instructions](site-recovery-vmware-to-azure-install-mob-svc.md#install-mobility-service-by-push-installation-from-azure-site-recovery) si vous souhaitez effectuer une transmission push du service Mobilité sur les machines virtuelles exécutant Windows ou Linux.

## <a name="other-methods"></a>Autres méthodes

En savoir plus sur comment [installer le service Mobilité à l’aide du Gestionnaire de configuration](site-recovery-install-mobility-service-using-sccm.md), ou à l’aide [d’Azure Automation DSC](site-recovery-automate-mobility-service-install.md).

## <a name="next-steps"></a>Étapes suivantes

Aller à [Étape 11 : activer la réplication](vmware-walkthrough-enable-replication.md)
