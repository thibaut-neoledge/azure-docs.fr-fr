---
title: " Gérer un serveur de processus en cours d’exécution dans Azure (Classic) | Microsoft Docs"
description: Cet article explique comment configurer un serveur de processus de restauration automatique dans Azure (Classic).
services: site-recovery
documentationcenter: 
author: AnoopVasudavan
manager: gauravd
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 06/29/2017
ms.author: anoopkv
ms.translationtype: Human Translation
ms.sourcegitcommit: 3716c7699732ad31970778fdfa116f8aee3da70b
ms.openlocfilehash: 479bbd207bcf715138c340f9e4d2634120bab85c
ms.contentlocale: fr-fr
ms.lasthandoff: 06/30/2017

---

# <a name="manage-a-process-server-running-in-azure-classic"></a>Gérer un serveur de processus en cours d’exécution dans Azure (Classic)
> [!div class="op_single_selector"]
> * [Azure Classic ](./site-recovery-vmware-setup-azure-ps-classic.md)
> * [Gestionnaire de ressources](./site-recovery-vmware-setup-azure-ps-resource-manager.md)

Lors de la restauration automatique, il est recommandé de déployer le serveur de processus dans Azure s’il existe une latence élevée entre le réseau virtuel Azure et votre réseau local. Cet article explique comment installer, configurer et gérer les serveurs de processus en cours d’exécution dans Azure.

> [!NOTE]
> Consultez cet article si vous avez utilisé le déploiement Classic comme modèle de déploiement pour les machines virtuelles lors du basculement. Si vous avez utilisé le modèle de déploiement Resource Manager, suivez les étapes de la rubrique [Guide pratique pour installer et configurer un serveur de processus de restauration automatique (Resource Manager)](./site-recovery-vmware-setup-azure-ps-resource-manager.md).

## <a name="prerequisites"></a>Composants requis

[!INCLUDE [site-recovery-vmware-process-server-prereq](../../includes/site-recovery-vmware-azure-process-server-prereq.md)]

## <a name="deploy-a-process-server-on-azure"></a>Déployer un serveur de processus dans Azure

1. Dans Azure Marketplace, créez une machine virtuelle à l’aide du **serveur de processus Microsoft Azure Site Recovery V2** </br>
    ![Marketplace_image_1](./media/site-recovery-vmware-setup-azure-ps-classic/marketplace-ps-image.png)
2. Veillez à sélectionner le modèle de déploiement **Classic** </br>
  ![Marketplace_image_2](./media/site-recovery-vmware-setup-azure-ps-classic/marketplace-ps-image-classic.png)
3. Dans l’Assistant Créer une machine virtuelle > Paramètres de base, veillez à sélectionner l’abonnement et l’emplacement où vous avez basculé les machines virtuelles.</br>
  ![create_image_1](./media/site-recovery-vmware-setup-azure-ps-classic/azureps-classic-basic-info.png)
4. Assurez-vous que la machine virtuelle est connectée au réseau virtuel Azure auquel la machine virtuelle basculée est connectée.</br>
  ![create_image_2](./media/site-recovery-vmware-setup-azure-ps-classic/azureps-classic-settings.png)
5. Une fois la machine virtuelle du serveur de processus configurée, vous devez vous connecter et inscrire cette machine auprès du serveur de configuration.

> [!NOTE]
> Pour pouvoir utiliser ce serveur de processus pour la restauration automatique, vous devez l’inscrire auprès du serveur de configuration local.

## <a name="registering-the-process-server-running-in-azure-to-a-configuration-server-running-on-premises"></a>Inscription du serveur de processus (en cours d’exécution dans Azure) auprès d’un serveur de Configuration (en cours d’exécution en local)

[!INCLUDE [site-recovery-vmware-register-process-server](../../includes/site-recovery-vmware-register-process-server.md)]

## <a name="upgrading-the-process-server-to-latest-version"></a>Mise à niveau du serveur de processus avec la version la plus récente.

[!INCLUDE [site-recovery-vmware-upgrade-process-server](../../includes/site-recovery-vmware-upgrade-process-server.md)]

## <a name="unregistering-the-process-server-running-in-azure-from-a-configuration-server-running-on-premises"></a>Désinscription du serveur de processus (en cours d’exécution dans Azure) d’un serveur de Configuration (en cours d’exécution en local)

[!INCLUDE [site-recovery-vmware-upgrade-process-server](../../includes/site-recovery-vmware-unregister-process-server.md)]

