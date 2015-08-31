<properties
   pageTitle="Mettre à jour votre appareil StorSimple | Microsoft Azure"
   description="Explique comment utiliser la fonctionnalité de mise à jour de StorSimple pour installer les mises à jour et les correctifs ordinaires et en mode Maintenance."
   services="storsimple"
   documentationCenter="NA"
   authors="SharS"
   manager="adinah"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="08/19/2015"
   ms.author="v-sharos" />

# Mettre à jour votre appareil StorSimple

## Vue d'ensemble

Les fonctionnalités de mise à jour StorSimple permettent de maintenir facilement à jour votre appareil StorSimple. Selon le type de mise à jour, vous pouvez appliquer des mises à jour à l’appareil via le portail de gestion Microsoft ou via l’interface Windows PowerShell. Ce didacticiel décrit les différents types de mise à jour et leur installation.

Vous pouvez appliquer deux types de mises à jour d’appareil :

- Mises à jour ordinaires (ou en mode Normal)
- Mises à jour en mode Maintenance

Vous pouvez installer les mises à jour ordinaires via le portail de gestion ou Windows PowerShell ; toutefois, vous devez utiliser Windows PowerShell pour installer les mises à jour en mode Maintenance.

Chaque type de mise à jour est décrit séparément, ci-dessous.

### Mises à jour ordinaires

Les mises à jour ordinaires s’effectuent sans interruption du service et peuvent être installées lorsque l’appareil est en mode Normal. Ces mises à jour sont appliquées à chaque contrôleur d’appareil via le site web Microsoft Update.

> [AZURE.IMPORTANT]Un basculement de contrôleur peut se produire pendant le processus de mise à jour. Mais cela n’aura aucune incidence sur la disponibilité ou le fonctionnement du système.

- Pour plus d’informations sur l’installation des mises à jour ordinaires via le portail de gestion, consultez la section [Installer les mises à jour ordinaires via le portail de gestion](#install-regular-updates-via-the-management-portal).

- Vous pouvez également installer les mises à jour ordinaires via Windows PowerShell pour StorSimple Pour des informations détaillées, consultez la page [Installer les mises à jour ordinaires via Windows PowerShell pour StorSimple](#install-regular-updates-via-windows-powershell-for-storsimple).

### Mises à jour en mode Maintenance

Les mises à jour en mode Maintenance provoquent une interruption du service. Il s’agit, par exemple, des mises à niveau du microprogramme du disque ou des mises à niveau du microprogramme USM. Ces mises à jour nécessitent que l’appareil soit placé en mode Maintenance. Pour plus d’informations, consultez l’[Étape 2 : Passage en mode Maintenance](#step2). Vous ne pouvez pas utiliser le portail de gestion pour installer les mises à jour en mode Maintenance. Vous devez utiliser Windows PowerShell pour StorSimple.

Pour plus d’informations sur l’installation des mises à jour en mode Maintenance, consultez la section [Installer les mises à jour en mode Maintenance via Windows PowerShell pour StorSimple](#install-maintenance-mode-updates-via-windows-powershell-for-storsimple).

> [AZURE.IMPORTANT]Les mises à jour en mode Maintenance doivent être appliquées séparément à chaque contrôleur.

## Installer les mises à jour ordinaires via le portail de gestion

Vous pouvez utiliser le portail de gestion pour appliquer les mises à jour à votre appareil StorSimple.

[AZURE.INCLUDE [storsimple-install-updates-manually](../../includes/storsimple-install-updates-manually.md)]

## Installer les mises à jour ordinaires via Windows PowerShell pour StorSimple

Sinon, vous pouvez utiliser Windows PowerShell pour StorSimple pour appliquer les mises à jour ordinaires (mode Normal).

> [AZURE.IMPORTANT]Bien que vous puissiez installer des mises à jour standard à l’aide de Windows PowerShell pour StorSimple, nous vous recommandons vivement de les installer via le portail de gestion. Depuis la mise à jour 1, des vérifications préalables seront effectuées avant d’installer les mises à jour à partir du portail. Ces vérifications préalables permettent de prévenir les échecs et de garantir une expérience avec moins de problèmes.

[AZURE.INCLUDE [storsimple-install-regular-updates-powershell](../../includes/storsimple-install-regular-updates-powershell.md)]

## Installer les mises à jour en mode Maintenance via Windows PowerShell pour StorSimple

Vous utilisez Windows PowerShell pour StorSimple pour appliquer les mises à jour en mode Maintenance à votre appareil StorSimple. Dans ce mode, toutes les demandes d’E/S sont suspendues. Les services tels que la mémoire vive non volatile (NVRAM) ou le service de cluster sont également arrêtés. Les deux contrôleurs sont redémarrés lorsque vous entrez dans ce mode ou que vous le quittez. Lorsque vous quittez ce mode, tous les services reprennent et doivent être sains. (Cela peut prendre quelques minutes.)

Si vous devez appliquer les mises à jour en mode Maintenance, vous recevrez une alerte via le portail de gestion vous informant que vous avez des mises à jour qui doivent être installées. Cette alerte inclut des instructions pour installer les mises à jour à l’aide de Windows PowerShell pour StorSimple. Après avoir mis à jour votre appareil, utilisez la même procédure pour faire passer l’appareil en mode Normal. Pour obtenir des instructions détaillées, consultez l’[Étape 4 : Quitter le mode Maintenance](#step4).

> [AZURE.IMPORTANT]
> 
> - Avant de passer en mode Maintenance, vérifiez que les deux contrôleurs d’appareil sont sains en vérifiant l’**État du matériel** sur la page **Maintenance** du portail de gestion. Si le contrôleur n’est pas sain, contactez le support technique Microsoft pour connaître les étapes suivantes. Pour plus d’informations, accédez à Contacter le support technique Microsoft. 
> - En mode Maintenance, vous devez appliquer la mise à jour d’abord à un contrôleur, puis à l’autre contrôleur.

### Étape 1 : Connexion à la console série <a name="step1">

Tout d’abord, utilisez une application telle que PuTTY pour accéder à la console série. La procédure suivante explique comment utiliser PuTTY pour se connecter à la console série.

[AZURE.INCLUDE [storsimple-use-putty](../../includes/storsimple-use-putty.md)]

### Étape 2 : Passage en mode Maintenance <a name="step2">

Une fois que vous êtes connecté à la console, déterminez s’il existe des mises à jour à installer, puis passez en mode Maintenance pour les installer.

[AZURE.INCLUDE [storsimple-enter-maintenance-mode](../../includes/storsimple-enter-maintenance-mode.md)]

### Étape 3 : Installation des mises à jour <a name="step3">

Ensuite, installez les mises à jour.

[AZURE.INCLUDE [storsimple-install-maintenance-mode-updates](../../includes/storsimple-install-maintenance-mode-updates.md)]
 
### Étape 4 : Quitter le mode Maintenance <a name="step4">

Pour finir, quittez le mode Maintenance.

[AZURE.INCLUDE [storsimple-exit-maintenance-mode](../../includes/storsimple-exit-maintenance-mode.md)]

## Installer les correctifs logiciels via Windows PowerShell pour StorSimple

Contrairement aux mises à jour pour Microsoft Azure StorSimple, les correctifs logiciels sont installés à partir d’un dossier partagé. Comme pour les mises à jour, il existe deux types de correctifs :

- Correctifs logiciels ordinaires 
- Correctifs logiciels en mode Maintenance  

Les procédures suivantes expliquent comment utiliser Windows PowerShell pour StorSimple pour installer les correctifs logiciels ordinaires et en mode Maintenance.

[AZURE.INCLUDE [storsimple-install-regular-hotfixes](../../includes/storsimple-install-regular-hotfixes.md)]

[AZURE.INCLUDE [storsimple-install-maintenance-mode-hotfixes](../../includes/storsimple-install-maintenance-mode-hotfixes.md)]

## Que se passe-t-il pour les mises à jour si vous effectuez une réinitialisation de l’appareil aux paramètres d’usine ?

Si un appareil est réinitialisé aux paramètres d’usine, toutes les mises à jour sont perdues. Une fois l’appareil réinitialisé aux paramètres d’usine inscrit et configuré, vous devez installer manuellement les mises à jour via le portail de gestion et/ou de Windows PowerShell pour StorSimple. Pour plus d’informations sur la réinitialisation aux paramètres d’usine, consultez la page [Rétablissement des paramètres par défaut de l’appareil](storsimple-manage-device-controller.md#reset-the-device-to-factory-default-settings).

## Étapes suivantes

[Découvrez comment administrer votre appareil StorSimple à l’aide de Windows PowerShell pour StorSimple](storsimple-windows-powershell-administration.md).
 

<!---HONumber=August15_HO8-->