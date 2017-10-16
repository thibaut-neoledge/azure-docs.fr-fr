---
title: "Préparer les ressources VMware locales pour réplication vers Azure avec Azure Site Recovery | Microsoft Docs"
description: "Résume les étapes à suivre pour répliquer des charges de travail exécutées sur des machines virtuelles VMware sur un stockage Azure"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 6aba0e89-ad7c-467e-9db2-cfb3bfe4c7d6
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2017
ms.author: raynew
ms.openlocfilehash: 3e1c589030210c2eae1ad9c02811775d9d6365d4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="step-6-prepare-on-premises-vmware-replication-to-azure"></a>Étape 6 : Préparer la réplication VMware local vers Azure

Pour préparer l’interaction des serveurs VMware locaux avec Azure Site Recovery, et pour préparer les machines virtuelles VMWare à l’installation du service mobilité, suivez les instructions fournies dans cet article. L’agent du service Mobilité doit être installé sur toutes les machines virtuelles VMware locales que vous souhaitez répliquer.

## <a name="prepare-for-automatic-discovery"></a>Préparation pour la détection automatique

Site Recovery détecte automatiquement les machines virtuelles s’exécutant sur des hôtes ESXi vSphere (avec ou sans serveur vCenter). Pour la détection automatique, Site Recovery a besoin d’un compte pour accéder aux hôtes et aux serveurs :

1. Pour utiliser un compte dédié, définissez un rôle (au niveau du serveur vCenter, avec les autorisations indiquées dans le tableau ci-dessous). Donnez-lui un nom, tel que **Azure_Site_Recovery**.
2. Créez ensuite un utilisateur sur l’hôte vSphere/le serveur vCenter et attribuez le rôle à l’utilisateur. Vous spécifiez ce compte d’utilisateur pendant le déploiement de Site Recovery.


### <a name="vmware-account-permissions"></a>Autorisations du compte VMware

Site Recovery a besoin d’accéder à VMware pour que le serveur de processus découvre automatiquement des machines virtuelles ainsi que pour le basculement et la restauration automatique des machines virtuelles.

- **Migrer** : si vous souhaitez uniquement migrer des machines virtuelles VMware vers Azure, sans jamais procéder à leur restauration automatique, vous pouvez utiliser un compte VMware avec un rôle en lecture seule. Ce type de rôle peut exécuter le basculement mais ne peut pas arrêter les machines source protégées. Cela n’est pas nécessaire pour la migration.
- **Réplication/restauration** : si vous souhaitez déployer la réplication complète (réplication, basculement et restauration automatique), le compte doit être en mesure d’exécuter des opérations telles que la création et la suppression de disques, l’alimentation de machines virtuelles, etc.
- **Découverte automatique** : au moins un compte en lecture seule est nécessaire.


**Tâche** | **Nécessite un compte/rôle** | **Autorisations** | **Détails**
--- | --- | --- | ---
**Le serveur de processus découvre automatiquement les machines virtuelles VMware** | Vous devez disposer d’au moins un utilisateur en lecture seule | Objet de centre de données -> Propager vers l’objet enfant, rôle = lecture seule | L’utilisateur est affecté au niveau du centre de données et a accès à tous les objets dans le centre de données.<br/><br/> Pour restreindre l’accès, attribuez le rôle **Aucun accès** avec l’objet **Propager vers enfant** aux objets enfants (hôtes vSphere, banques de données, machines virtuelles et réseaux).
**Type de basculement** | Vous devez disposer d’au moins un utilisateur en lecture seule | Objet de centre de données -> Propager vers l’objet enfant, rôle = lecture seule | L’utilisateur est affecté au niveau du centre de données et a accès à tous les objets dans le centre de données.<br/><br/> Pour restreindre l’accès, attribuez le rôle **Aucun accès** avec l’objet **Propager vers enfant** aux objets enfants (hôtes vSphere, banques de données, machines virtuelles et réseaux).<br/><br/> Utile à des fins de migration, mais pas pour la réplication complète, le basculement et la restauration automatique.
**Basculement et restauration automatique** | Nous vous suggérons de créer un rôle (Azure_Site_Recovery) avec les autorisations nécessaires, puis d’attribuer le rôle à un utilisateur ou à un groupe d’utilisateurs VMware. | Objet de centre de données -> Propager vers l’objet enfant, rôle = Azure_Site_Recovery<br/><br/> Banque de données -> Allouer de l’espace, parcourir la banque de données, opérations de fichier de bas niveau, supprimer le fichier, mettre à jour les fichiers de machine virtuelle<br/><br/> Réseau -> Attribution de réseau<br/><br/> Ressource -> Affecter les machines virtuelles au pool de ressources, migrer des machines virtuelles hors tension, migrer des machines virtuelles sous tension<br/><br/> Tâches -> Créer une tâche, Mettre à jour une tâche<br/><br/> Machine virtuelle -> Configuration<br/><br/> Machine virtuelle -> Interagir -> répondre à la question, connexion d’appareil, configurer un support de CD, configurer une disquette, mettre hors tension, mettre sous tension, installation des outils VMware<br/><br/> Machine virtuelle -> Stock -> Créer, inscrire, désinscrire<br/><br/> Machine virtuelle -> Approvisionnement -> Autoriser le téléchargement de machines virtuelles, autoriser le chargement de fichiers de machine virtuelle<br/><br/> Machine virtuelle -> Instantanés -> Supprimer les instantanés | L’utilisateur est affecté au niveau du centre de données et a accès à tous les objets dans le centre de données.<br/><br/> Pour restreindre l’accès, attribuez le rôle **Aucun accès** avec l’objet **Propager vers enfant** aux objets enfants (hôtes vSphere, banques de données, machines virtuelles et réseaux).


## <a name="prepare-for-push-installation-of-the-mobility-service"></a>Préparer l’installation Push du service Mobilité

Le service Mobilité doit être installé sur toutes les machines virtuelles que vous souhaitez répliquer. Il existe plusieurs façons d’installer le service, dont l’installation manuelle, l’installation push à partir du serveur de processus de Site Recovery, et l’installation à l’aide de System Center Configuration Manager, par exemple.

Si vous souhaitez utiliser l’installation push, vous devez préparer un compte dont Site Recovery pourra se servir pour accéder à la machine virtuelle.

- Vous pouvez utiliser un compte local ou de domaine
- Pour Windows, si vous n’utilisez pas un compte de domaine, vous devez désactiver le contrôle d’accès utilisateur distant sur la machine locale. Pour cela, dans le registre situé sous **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System**, ajoutez l’entrée DWORD **LocalAccountTokenFilterPolicy** avec une valeur de 1.
- Si vous souhaitez ajouter l’entrée de Registre pour Windows à partir d’une interface CLI, tapez :       ``REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1.``
- Pour Linux, le compte doit être un utilisateur racine sur le serveur Linux source.



## <a name="next-steps"></a>Étapes suivantes

Aller à l’[Étape 7 : créer un coffre](vmware-walkthrough-create-vault.md)
