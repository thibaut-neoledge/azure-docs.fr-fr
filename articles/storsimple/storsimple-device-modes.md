---
title: Changer le mode de l'appareil StorSimple | Microsoft Docs
description: "Décrit les modes de l’appareil StorSimple et explique comment utiliser Windows PowerShell for StorSimple pour changer le mode de l’appareil."
services: storsimple
documentationcenter: 
author: alkohli
manager: carmonm
editor: 
ms.assetid: e9d7d277-8a2f-45eb-9fef-355486e14cbc
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/03/2017
ms.author: alkohli
ms.openlocfilehash: 815d4bd539db8aebf2ee1eb62aef13b554b9cc47
ms.sourcegitcommit: 0930aabc3ede63240f60c2c61baa88ac6576c508
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/07/2017
---
# <a name="change-the-device-mode-on-your-storsimple-device"></a>Changement du mode de votre appareil StorSimple
> [!NOTE]
> Le portail Azure Classic pour StorSimple est déconseillé. Vos instances de StorSimple Device Manager seront automatiquement déplacées vers le nouveau portail Azure à la date de désapprobation planifiée. Vous serez prévenu de ce déplacement par un e-mail et une notification du portail. Ce document sera également bientôt retiré. Pour afficher la version de cet article applicable au nouveau portail Azure, accédez à [Changer le mode de votre appareil StorSimple](storsimple-8000-device-modes.md). Si vous avez des questions concernant le déplacement, consultez [FAQ : Déplacement vers le portail Azure](storsimple-8000-move-azure-portal-faq.md).

Cet article fournit une brève description des modes avec lesquels votre appareil StorSimple peut fonctionner. Votre appareil StorSimple peut fonctionner dans trois modes : Normal, Maintenance et Récupération. 

À la fin de cet article, vous :

* Description des modes de l'appareil StorSimple
* Comment déterminer le mode dans lequel se trouve l'appareil StorSimple
* Comment passer du mode Normal au mode Maintenance, et *inversement*

Les tâches de gestion ci-dessus peuvent uniquement être effectuées via l’interface Windows PowerShell de votre appareil StorSimple.

## <a name="about-storsimple-device-modes"></a>À propos des modes de l’appareil StorSimple
Votre appareil StorSimple peut fonctionner en mode Normal, Maintenance ou Récupération. Chacun de ces modes est brièvement décrit ci-dessous.

### <a name="normal-mode"></a>Mode Normal
Il s’agit du mode de fonctionnement normal pour un appareil StorSimple entièrement configuré. Par défaut, votre appareil doit être en mode Normal.

### <a name="maintenance-mode"></a>Mode Maintenance
Parfois, l’appareil StorSimple doit être mis en mode Maintenance. Ce mode vous permet d’assurer la maintenance de l’appareil et d’installer des mises à jour perturbatrices, telles que celles du microprogramme de disque.

Vous ne pouvez mettre le système en mode Maintenance qu’à l’aide de Windows PowerShell pour StorSimple. Dans ce mode, toutes les demandes d’E/S sont suspendues. Les services tels que la mémoire vive non volatile (NVRAM) ou le service de cluster sont également arrêtés. Les deux contrôleurs sont redémarrés lorsque vous entrez dans ce mode ou que vous le quittez. Lorsque vous quittez le mode Maintenance, tous les services reprennent et doivent être sains. Cela peut prendre quelques minutes.

> [!NOTE]
> **Le mode Maintenance est uniquement pris en charge sur un appareil en état de fonctionnement. Il n’est pas pris en charge sur un appareil dont l’un ou les deux contrôleurs ne fonctionnent pas.** 
> </br>
> 
> 

### <a name="recovery-mode"></a>Mode Récupération
Le mode Récupération peut être décrit comme un « mode sans échec avec prise en charge du réseau Windows ». Le mode Récupération engage l’équipe de support technique Microsoft et leur permet d’effectuer des diagnostics sur le système. L’objectif principal objectif du mode Récupération consiste à récupérer les journaux système.

Si votre système passe en mode Récupération, vous devez contacter le support technique Microsoft pour les étapes suivantes. Pour plus d’informations, accédez à [Contacter le support technique Microsoft](storsimple-contact-microsoft-support.md).

> [!NOTE]
> **Vous ne pouvez pas mettre l’appareil en mode Récupération. Si l’appareil est en mauvais état, ce mode tente de le rétablir dans un état permettant au support technique Microsoft de l’examiner.**
> 
> 

## <a name="determine-storsimple-device-mode"></a>Détermination du mode de l’appareil StorSimple
#### <a name="to-determine-the-current-device-mode"></a>Pour déterminer le mode actuel de l’appareil
1. Ouvrez une session sur la console série de l'appareil en suivant les étapes dans [Utilisation de PuTTY pour se connecter à la console série de l'appareil](storsimple-deployment-walkthrough.md#use-putty-to-connect-to-the-device-serial-console).
2. Examinez le message de bannière situé dans le menu de la console série de l’appareil. Ce message indique explicitement si l’appareil est en mode Maintenance ou Récupération. Si le message ne contient pas d’informations spécifiques sur le mode du système, l’appareil est en mode Normal.

## <a name="change-the-storsimple-device-mode"></a>Changer le mode de l'appareil StorSimple
Vous pouvez mettre l’appareil StorSimple en mode Maintenance (à partir du mode Normal) pour effectuer une maintenance ou installer des mises à jour du mode Maintenance. Effectuez les procédures suivantes pour entrer dans le mode Maintenance ou le quitter.

> [!IMPORTANT]
> Avant d’activer le mode Maintenance, vérifiez que les deux contrôleurs d’appareil fonctionnent correctement en accédant à **État du matériel** dans la page **Maintenance** du portail Azure Classic. Si au moins l’un des deux contrôleurs n’est pas sain, contactez le Support Microsoft pour connaître les étapes suivantes. Pour plus d’informations, accédez à [Contacter le support technique Microsoft](storsimple-contact-microsoft-support.md).
> 
> 

#### <a name="to-enter-maintenance-mode"></a>Pour passer en mode Maintenance
1. Ouvrez une session sur la console série de l'appareil en suivant les étapes dans [Utilisation de PuTTY pour se connecter à la console série de l'appareil](storsimple-deployment-walkthrough.md#use-putty-to-connect-to-the-device-serial-console).
2. Dans le menu de la console série, sélectionnez l’option 1, **Ouvrir une session avec un accès total**. Lorsque vous y êtes invité, fournissez le **mot de passe administrateur de l’appareil**. Le mot de passe par défaut est : `Password1`.
3. À l'invite de commandes, tapez 
   
    `Enter-HcsMaintenanceMode`
4. Un message d’avertissement s’affiche pour vous indiquer que le mode Maintenance va perturber toutes les demandes d’E/S et annuler la connexion au portail Azure Classic. Vous êtes invité à confirmer l’opération. Tapez **O** pour passer en mode Maintenance.
5. Les deux contrôleurs redémarrent. Lorsque le redémarrage est terminé, un autre message s’affiche, indiquant que l’appareil est en mode Maintenance.

#### <a name="to-exit-maintenance-mode"></a>Pour quitter le mode Maintenance
1. Connectez-vous à la console série de l’appareil. Vérifiez dans le message de bannière, que votre appareil est en mode Maintenance.
2.  À l’invite de commandes, tapez :
   
    `Exit-HcsMaintenanceMode`
3. Un message d’avertissement et un message de confirmation s’affichent. Tapez **O** pour quitter le mode Maintenance.
4. Les deux contrôleurs redémarrent. Lorsque le redémarrage est terminé, un autre message s’affiche, indiquant que l’appareil est en mode Normal.

## <a name="next-steps"></a>Étapes suivantes
Découvrez comment [appliquer les mises à jour des modes Normal et Maintenance](storsimple-update-device.md) sur votre appareil StorSimple.

