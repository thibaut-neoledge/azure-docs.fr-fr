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
ms.date: 09/12/2017
ms.author: alkohli
ms.translationtype: HT
ms.sourcegitcommit: fda37c1cb0b66a8adb989473f627405ede36ab76
ms.openlocfilehash: a64cf0ba2a4646a03c864193fb93de4f54f38039
ms.contentlocale: fr-fr
ms.lasthandoff: 09/14/2017

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

## <a name="datacenter-changes"></a>Modifications de centre de données

| Pouvez-vous déplacer...| Pris en charge|Temps d’arrêt| Processus de support Azure| Approche|
|-----|-----|-----|-----|-----|-----|
| un service StorSimple depuis un centre de données Azure vers un autre ? | Non | Oui |Aucun, processus manuel  |<li>Migrez les données en dehors de l’appareil StorSimple.</li><li>Effectuez une réinitialisation aux paramètres d’usine de l’appareil, pour supprimer toutes les données locales sur celui-ci.</li><li>Inscrivez l’appareil dans le nouvel abonnement à un nouveau service StorSimple Device Manager.</li><li>Refaites migrer les données sur l’appareil.|
| un compte de stockage depuis un centre de données Azure vers un autre ? | Non |Oui  |Aucun, processus manuel  | Identique à ce qui précède.|

## <a name="next-steps"></a>Étapes suivantes

* [Déployer un service StorSimple Device Manager](storsimple-8000-manage-service.md)
* [Déployer un appareil StorSimple de série 8000 dans le portail Azure](storsimple-8000-deployment-walkthrough-u2.md)
