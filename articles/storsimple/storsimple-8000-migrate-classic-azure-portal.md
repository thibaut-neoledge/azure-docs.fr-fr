---
title: Migrer des comptes de stockage et des abonnements pour votre service StorSimple Device Manager | Microsoft Docs
description: "Découvrez comment migrer des abonnements et des comptes de stockage pour votre service StorSimple Device Manager 8000."
services: storsimple
documentationcenter: 
author: alkohli
manager: timlt
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/26/2017
ms.author: alkohli
ms.openlocfilehash: edac2635086106f83b30316ab190bead5245f195
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="migrate-subscriptions-and-storage-accounts-associated-with-storsimple-device-manager-service"></a>Migrer les abonnements et les comptes de stockage associés au service StorSimple Device Manager

Vous devrez peut-être déplacer votre service StorSimple vers une nouvelle inscription ou un nouvel abonnement. Ces scénarios de migration sont soit des modifications de compte, soit des modifications de centre de données. Utilisez le tableau suivant pour comprendre les scénarios pris en charge et notamment les étapes précises du déplacement.

## <a name="account-changes"></a>Modifications de compte

| Pouvez-vous déplacer...| Pris en charge| Temps d’arrêt| Processus de support Azure| Approche|
|-----|-----|-----|-----|-----|
| un abonnement entier (comprenant le service et les comptes de stockage StorSimple) vers une autre inscription ? | Oui       | Non       | **Transfert d’inscription**<br>Utilisation :<li>Quand vous achetez un nouvel engagement Azure dans le cadre d’un nouveau contrat.</li><li>Vous voulez migrer tous les comptes et abonnements depuis l’ancienne inscription vers la nouvelle. Sont inclus tous les services Azure de l’ancien abonnement.</li> | **Étape 1 : ouvrez un ticket de support d’opération Azure Enterprise.**<li>Accédez à [http://aka.ms/AzureEnt](http://aka.ms/AzureEnt).</li><li> Sélectionnez **Administration des inscriptions**, puis **Transférer d’une inscription vers une nouvelle inscription**.<br>**Étape 2 : indiquez les informations demandées.**<br>Incluez :<li>Numéro d’inscription source</li><li> Numéro d’inscription de destination</li><li>Date d’effet du transfert|
| le service StorSimple depuis un compte existant vers une nouvelle inscription ?    | Oui       | Non       | **Transfert de compte**<br>Utilisez :<li>Quand vous ne voulez pas un transfert d’inscription complet.</li><li>Vous voulez uniquement déplacer des comptes spécifiques vers une nouvelle inscription.</li>| **Étape 1 : ouvrez un ticket de support d’opération Azure Enterprise.**<li>Accédez à [http://aka.ms/AzureEnt](http://aka.ms/AzureEnt).</li><li>Sélectionnez **Administration des inscriptions**, puis **Transférer un compte EA vers une nouvelle inscription**.<br>**Étape 2 : indiquez les informations demandées.**<br>Incluez :<li>Numéro d’inscription source</li><li> Numéro d’inscription de destination</li><li>Date d’effet du transfert|
| le service StorSimple depuis un abonnement vers un autre abonnement ?      | Non        |    Oui         | Aucun, processus manuel|<li>Migrez les données en dehors de l’appareil StorSimple.</li><li>Effectuez une réinitialisation aux paramètres d’usine de l’appareil, pour supprimer toutes les données locales sur celui-ci.</li><li>Inscrivez l’appareil dans le nouvel abonnement à un service StorSimple Device Manager.</li><li>Refaites migrer les données sur l’appareil.|
| un appareil StorSimple depuis un service StorSimple Device Manager vers un autre service dans une région différente ?      | Non        | Oui            | Aucun, processus manuel |Identique à ce qui précède.|
| un compte de stockage vers un nouvel abonnement ou groupe de ressources ?     | Oui        | Non             |Déplacer le compte de stockage vers un autre abonnement ou groupe de ressources |Après le déplacement, si les clés d’accès de compte de stockage sont mises à jour, l’utilisateur doit les configurer manuellement pour le compte de stockage migré via le service StorSimple Device Manager.|
| Compte de stockage classique vers un compte de stockage Azure Resource Manager      | Oui        | Non             |Migrer un compte de stockage classique vers Azure Resource Manager |<li>Pour obtenir des instructions détaillées sur la migration d’un compte de stockage classique vers Azure Resource Manager, accédez à [Migrer un compte de stockage classique](../virtual-machines/windows/migration-classic-resource-manager-ps.md#step-62-migrate-a-storage-account).</li><li> Si les clés d’accès de compte de stockage sont mises à jour après la migration, l’utilisateur doit les synchroniser pour le compte de stockage migré via le service StorSimple Device Manager. Cela permet de vérifier que les appareils StorSimple continuent de fonctionner normalement et sont en mesure de hiérarchiser les données principales/de sauvegarde vers Azure. Pour obtenir des instructions détaillées sur la synchronisation des clés d’accès, accédez à [Workflow de rotation](storsimple-8000-manage-storage-accounts.md#key-rotation-of-storage-accounts).</li><li> Dans le cas de StorSimple Cloud Appliance, si le compte de stockage classique est migré, mais que la machine virtuelle sous-jacente reste classique, l’appliance doit fonctionner correctement. Si la machine virtuelle sous-jacente pour l’appliance cloud est migrée, la fonctionnalité de désactivation et de suppression ne fonctionne pas.</li><li> Vous devez créer une appliance StorSimple Cloud Appliance dans le portail Azure et basculer à partir des appliances cloud plus anciennes. Vous ne pouvez pas créer d’appliance StorSimple Cloud Appliance dans le nouveau portail Azure à l’aide d’un compte de stockage classique ; un compte de stockage Azure Resource Manager est nécessaire. Pour plus d’informations, accédez à [Déployer et gérer une StorSimple Cloud Appliance](storsimple-8000-cloud-appliance-u2.md).</li>|Identique à ce qui précède.|

## <a name="datacenter-changes"></a>Modifications de centre de données

| Pouvez-vous déplacer...| Pris en charge|Temps d’arrêt| Processus de support Azure| Approche|
|-----|-----|-----|-----|-----|
| un service StorSimple depuis un centre de données Azure vers un autre ? | Non | Oui |Aucun, processus manuel  |<li>Migrez les données en dehors de l’appareil StorSimple.</li><li>Effectuez une réinitialisation aux paramètres d’usine de l’appareil, pour supprimer toutes les données locales sur celui-ci.</li><li>Inscrivez l’appareil dans le nouvel abonnement à un nouveau service StorSimple Device Manager.</li><li>Refaites migrer les données sur l’appareil.|
| un compte de stockage depuis un centre de données Azure vers un autre ? | Non |Oui  |Aucun, processus manuel  | Identique à ce qui précède.|

## <a name="next-steps"></a>Étapes suivantes

* [Déployer un service StorSimple Device Manager](storsimple-8000-manage-service.md)
* [Déployer un appareil StorSimple de série 8000 dans le portail Azure](storsimple-8000-deployment-walkthrough-u2.md)
