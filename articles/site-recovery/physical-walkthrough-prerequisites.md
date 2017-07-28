---
title: "Conditions préalables pour la réplication de serveurs physiques vers Azure avec Azure Site Recovery | Microsoft Docs"
description: "Résume la configuration requise pour la réplication de charges de travail s’exécutant sur des serveurs Windows/Linux physiques vers Azure, avec le service Azure Site Recovery."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 318156ba-793b-48d0-98d4-cc5436bf28a3
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 06/27/2017
ms.author: raynew
ms.translationtype: Human Translation
ms.sourcegitcommit: 138f04f8e9f0a9a4f71e43e73593b03386e7e5a9
ms.openlocfilehash: 5f5cb4b9b6fcee6b56ccdcb6223afddd9de90fec
ms.contentlocale: fr-fr
ms.lasthandoff: 06/29/2017

---

# <a name="step-2-review-the-prerequisites-for-physical-server-to-azure-replication"></a>Étape 2 : Passer en revue la configuration requise des serveurs physiques en vue d’une réplication vers Azure

La configuration requise est récapitulée dans le tableau ci-dessous.


**Configuration requise** | **Détails**
--- | ---
**Microsoft Azure** | En savoir plus sur la [configuration requise pour Azure](site-recovery-prereq.md#azure-requirements)
**Serveur de configuration local** | Vous avez besoin d’un serveur physique (ou d’une machine virtuelle VMware) exécutant Windows Server 2012 R2 ou une version ultérieure. Configurez ce serveur au cours du déploiement de Site Recovery.<br/><br/> Par défaut, le serveur de processus et le serveur cible maître sont également installés sur cette machine. Pour monter en puissance, vous aurez peut-être besoin d’un serveur de processus distinct. Le cas échéant, il doit présenter la même configuration que le serveur de configuration.<br/><br/> [En savoir plus](site-recovery-set-up-vmware-to-azure.md#configuration-server-minimum-requirements).
**Machines virtuelles locales** | Les machines que vous souhaitez répliquer doivent exécuter un [système d’exploitation pris en charge](site-recovery-support-matrix-to-azure.md#support-for-replicated-machine-os-versions) et être conformes aux [Conditions préalables pour Azure](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements).
**URLs** | Le serveur de configuration nécessite un accès à ces URL :<br/><br/> [!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]<br/><br/> Si vous avez des règles de pare-feu fondées sur l’adresse IP, vérifiez qu’elles autorisent la communication vers Azure.<br/></br> Autorisez les [plages d’adresses IP de centres de données Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653) et le port HTTPS (443).<br/></br> Autorisez les plages d’adresses IP relatives à la région de votre abonnement Azure et à la région des États-Unis de l’Ouest (utilisées pour la gestion du contrôle d’accès et des identités).<br/><br/> Autorisez cette URL pour le téléchargement de MySQL : http://cdn.mysql.com/archives/mysql-5.5/mysql-5.5.37-win32.msi.
**Service de mobilité** | Installé sur chaque serveur répliqué.




## <a name="limitations"></a>Limites

Prenez note des limitations résumées dans le tableau avant d’effectuer le déploiement.

**Limite** | **Détails**
--- | ---
**Microsoft Azure** | Les comptes de stockage et de réseau doivent se trouver dans la même région que le coffre.<br/><br/> Si vous utilisez un compte de stockage premium, vous devez également disposer d’un compte de stockage standard pour stocker les journaux de réplication.<br/><br/> Vous ne pouvez pas effectuer de réplications dans des comptes premium dans le centre et le sud de l’Inde.
**Serveur de configuration local** | Si vous utilisez une machine virtuelle VMware en tant que serveur de configuration, l’adaptateur de la machine virtuelle doit être de type VMXNET3. Dans le cas contraire, [installez cette mise à jour](https://kb.vmware.com/selfservice/microsites/search.do?cmd=displayKC&docType=kc&externalId=2110245&sliceId=1&docTypeID=DT_KB_1_1&dialogID=26228401&stateId=1).<br/><br/> Si vous utilisez une machine virtuelle VMware, vSphere PowerCLI 6.0 doit être installé.<br/><br> L’ordinateur ne doit pas être un contrôleur de domaine.<br/><br/> L’ordinateur doit avoir une adresse IP statique.<br/><br/> Le nom d’hôte doit être de 15 caractères ou moins, et le système d’exploitation doit être en anglais.
**Répliquer les machines** | Vérifier les [limitations de la machine virtuelle Azure](site-recovery-prereq.md#azure-requirements)<br/><br/> Vous ne pouvez pas répliquer de machines virtuelles comportant des disques chiffrés, ou des machines virtuelles avec le démarrage UEFI/EFI.<br/><br> Les clusters à disque partagé ne sont pas pris en charge. Si la machine virtuelle source propose l’association de cartes réseau, elle est convertie en une carte réseau unique après le basculement.<br/><br/> Si les machines virtuelles disposent d’un disque iSCSI, Site Recovery le convertit en fichier VHD après le basculement. Si la cible iSCSI peut être atteinte par la machine virtuelle Azure, elle s’y connecte et voit à la fois cette dernière et le VHD. Le cas échéant, déconnectez la cible iSCSI.<br/><br/> Si vous souhaitez activer la cohérence multimachine virtuelle, qui permet aux machines virtuelles exécutant la même charge de travail d’être restaurées ensemble à un point de données cohérent, ouvrez le port 20004 sur la machine virtuelle.<br/><br/> Windows doit être installé sur le lecteur C. Le disque du système d’exploitation doit être un disque de base, et non un disque dynamique. Le disque de données peut être dynamique.<br/><br/> Les fichiers Linux /etc/hosts se trouvant sur les machines virtuelles doivent contenir des entrées qui mappent le nom d’hôte local aux adresses IP associées à toutes les cartes réseau. Le nom d'hôte, les points de montage, le nom de périphérique, les chemins d'accès système et les noms de fichiers (par exemple /etc/; /usr) doivent uniquement être en anglais.<br/><br/> Les types spécifiques de [stockage Linux](site-recovery-support-matrix-to-azure.md#support-for-storage) sont pris en charge.<br/><br/>Créez ou définissez **disk.enableUUID=true** dans les paramètres de la machine virtuelle. Cela fournit un UUID cohérent pour le VMDK, afin qu’il effectue correctement le montage et garantisse que seules les modifications delta soient retransférées sur site lors de la restauration automatique, sans réplication complète.


## <a name="next-steps"></a>Étapes suivantes

- Si vous effectuez un déploiement complet, accédez à [Étape 3 : Planifier la capacité](physical-walkthrough-capacity.md)
- Si vous effectuez un déploiement test, accédez à [Étape 4 : Planifier la mise en réseau](physical-walkthrough-network.md).

