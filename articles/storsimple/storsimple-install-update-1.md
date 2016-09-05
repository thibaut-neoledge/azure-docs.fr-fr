<properties
   pageTitle="Installation d’Update 1.2 sur votre appareil StorSimple | Microsoft Azure"
   description="Explique comment installer StorSimple série 8000 Update 1.2 sur votre appareil StorSimple série 8000."
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="08/22/2016"
   ms.author="alkohli" />

# Installation d’Update 1.2 sur votre appareil StorSimple

## Vue d'ensemble

Ce didacticiel explique comment installer Update 1.2 sur un appareil StorSimple exécutant une version logicielle antérieure à Update 1. Ce didacticiel couvre également les étapes supplémentaires requises pour la mise à jour lorsqu’une passerelle est configurée sur une interface réseau différente de DATA 0 de l’appareil StorSimple.

Update 1.2 comprend des mises à jour logicielles de l’appareil, des mises à jour du pilote LSI et des mises à jour du microprogramme de disque. Les mises à jour logicielles et du pilote LSI sont des mises à jour non perturbatrices et peuvent être appliquées par le biais du portail Azure Classic. Les mises à jour du microprogramme de disque sont des mises à jour perturbatrices et peuvent uniquement être appliquées par le biais de l’interface Windows PowerShell de l’appareil.

Selon la version en cours d’exécution sur votre appareil, vous pouvez déterminer si Update 1.2 sera appliqué. Vous pouvez vérifier la version logicielle de votre appareil en accédant à la section **Aperçu rapide** du **tableau de bord** de celui-ci.

</br>

| Si vous exécutez la version logicielle… | Que se passe-t-il dans le portail ? |
|---------------------------------|--------------------------------------------------------------|
| Version commerciale - GA | Si vous exécutez la version commerciale (GA), n’appliquez pas cette mise à jour. Veuillez [contacter le support technique Microsoft](storsimple-contact-microsoft-support.md) pour mettre à jour votre appareil.|
| Update 0.1 | Le portail applique Update 1.2. |
| Update 0.2 | Le portail applique Update 1.2. |
| Update 0.3 | Le portail applique Update 1.2. |
| Update 1 | Cette mise à jour ne sera pas disponible. |
| Update 1.1 | Cette mise à jour ne sera pas disponible. |

</br>

> [AZURE.IMPORTANT]

> -  Vous ne voyez pas immédiatement Update 1.2, car nous effectuons un déploiement échelonné des mises à jour. Revérifiez les mises à jour dans quelques jours, car celle-ci sera bientôt disponible.
> - Cette mise à jour comprend un ensemble de vérifications préalables manuelles et automatiques pour déterminer l’intégrité de l’appareil en termes d’état matériel et de connectivité réseau. Ces vérifications préalables sont effectuées uniquement si vous appliquez les mises à jour à partir du portail Azure Classic.
> - Nous vous recommandons d’installer les mises à jour du pilote et du logiciel au moyen du portail Azure Classic. Vous devez uniquement accéder à l’interface Windows PowerShell de l’appareil (pour installer les mises à jour) en cas d’échec de la vérification de la passerelle avant la mise à jour dans le portail. L’installation des mises à jour peut prendre 5 à 10 heures (y compris les mises à jour Windows). Les mises à jour du mode de maintenance doivent être installées via l’interface Windows PowerShell de l’appareil. Les mises à jour du mode de maintenance étant des mises à jour perturbatrices, elles entraînent un temps d’arrêt pour votre appareil.

[AZURE.INCLUDE [storsimple-preparing-for-update](../../includes/storsimple-preparing-for-updates.md)]

## Installation d’Update 1.2 via le portail Azure Classic

Suivez la procédure suivante pour mettre à jour votre appareil vers [Update 1.2](storsimple-update1-release-notes.md). Utilisez cette procédure uniquement si vous disposez d’une passerelle configurée sur l’interface réseau DATA 0 de votre appareil.

[AZURE.INCLUDE [storsimple-install-update2-via-portal](../../includes/storsimple-install-update2-via-portal.md)]

12. Vérifiez que votre appareil exécute **StorSimple 8000 Series Update 1.2 (6.3.9600.17584)**. Le paramètre **Dernière date de mise à jour** doit également être modifié. Vous verrez également que les mises à jour en mode de maintenance sont disponibles (ce message peut continuer à afficher jusqu’à 24 heures après avoir l’installation des mises à jour).

    Les mises à jour en mode maintenance entraînent des temps d’arrêt de l’appareil et ne peuvent être appliquées que par le biais de l’interface Windows PowerShell de votre appareil.

    ![Page Maintenance](./media/storsimple-install-update-1/InstallUpdate12_10M.png "Page Maintenance")

13. Téléchargez les mises à jour en mode maintenance à l’aide de la procédure indiquée dans [Pour télécharger des correctifs logiciels](#to-download-hotfixes) pour rechercher et télécharger KB3063416, qui installe les mises à jour du microprogramme de disque (les autres mises à jour doivent déjà être installés à ce stade).

13. Suivez les étapes répertoriées dans [Installer et vérifier le correctif logiciel en mode Maintenance](#to-install-and-verify-maintenance-mode-hotfixes) pour installer ces mises à jour en mode maintenance.

14. Dans le portail Azure Classic, accédez à la page **Maintenance** et en bas de la page, cliquez sur **Rechercher les mises à jour** pour vérifier la présence de mises à jour Windows, puis cliquez sur **Installer les mises à jour**. Vous avez terminé une fois que toutes les mises à jour sont installées correctement.



## Installation d’Update 1.2 sur un appareil présentant une passerelle configurée pour une interface réseau différente de DATA 0.

Vous devez utiliser cette procédure uniquement si la vérification de la passerelle échoue lors de la tentative d’installation des mises à jour par le biais du portail Azure Classic. La vérification échoue car vous avez une passerelle affectée à une interface réseau différente de DATA 0 et votre appareil exécute une version logicielle antérieure à Update 1. Si votre appareil ne possède pas de passerelle sur une interface réseau différente de DATA 0, vous pouvez le mettre à jour directement à partir du portail Azure Classic. Consultez la section [Installation d’Update 1.2 via le portail Azure Classic](#install-update-1.2-via-the-azure-classic-portal).

Les versions logicielles qui peuvent être mises à niveau à l’aide de cette méthode sont : Update 0.1, Update 0.2 et Update 0.3.


> [AZURE.IMPORTANT]
>
> - Si votre appareil exécute la version commerciale (GA), contactez le [support technique Microsoft](storsimple-contact-microsoft-support.md) pour obtenir de l’aide avec cette mise à jour.
> - Cette procédure ne doit être effectuée qu’une seule fois pour appliquer Update 1.2. Pour appliquer les mises à jour ultérieures, vous pouvez utiliser le portail Azure Classic.

Si votre appareil exécute un logiciel antérieur à Update 1 et qu’il possède une passerelle définie pour une interface réseau différente de DATA 0, vous pouvez appliquer Update 1.2 des deux manières suivantes :

- **Option 1** : téléchargez la mise à jour et appliquez-la à l’aide de l’applet de commande `Start-HcsHotfix` à partir de l’interface Windows PowerShell de l’appareil. Il s’agit de la méthode recommandée. **N’utilisez pas cette méthode pour appliquer Update 1.2 si votre appareil exécute Update 1.0 ou Update 1.1.**

- **Option 2** : supprimez la configuration de la passerelle et installez la mise à jour directement à partir du portail Azure Classic.


Des instructions détaillées relatives à chacune des procédures sont fournies dans les sections suivantes.

## Option 1 : utiliser Windows PowerShell pour StorSimple pour appliquer Update 1.2 en tant que correctif logiciel

Vous devez utiliser cette procédure uniquement si vous exécutez Update 0.1, 0.2 ou 0.3 et si la vérification de votre passerelle a échoué lors de la tentative d’installation des mises à jour à partir du portail Azure Classic. Si vous exécutez la version commerciale (GA) du logiciel, contactez le [support technique Microsoft](storsimple-contact-microsoft-support.md) pour mettre à jour votre appareil.

Pour installer Update 1.2 comme un correctif, vous devez télécharger et installer les correctifs suivants :

| Ordre | Ko | Description | Type de mise à jour |
|--------|-----------|-------------------------|------------- |
| 1 | KB3063418 | Mise à jour logicielle | Normal |
| 2 | KB3043005 | Mise à jour du contrôleur SAS LSI | Normal |
| 3 | KB3063416 | Microprogramme de disque | Maintenance |

Avant d'exécuter cette procédure pour appliquer la mise à jour, vérifiez les points suivants :

- Les deux contrôleurs d’appareil sont en ligne.

Exécutez la procédure suivante pour appliquer Update 1.2. **Les mises à jour peuvent prendre environ 2 heures (environ 30 minutes pour le logiciel, 30 minutes pour le pilote, 45 minutes pour le microprogramme de disque).**

[AZURE.INCLUDE [storsimple-install-update-option1](../../includes/storsimple-install-update-option1.md)]


## Option 2 : utiliser le portail Azure Classic pour appliquer Update 1.2 après la suppression de la configuration de la passerelle

Cette procédure s’applique uniquement aux appareils StorSimple exécutant une version logicielle antérieure à Update 1, avec une passerelle définie sur une interface réseau différente de DATA 0. Vous devez effacer le paramètre de passerelle avant d’appliquer la mise à jour.

La mise à jour peut prendre quelques heures. Si vos hôtes se trouvent dans des sous-réseaux différents, le retrait de la configuration de passerelle sur les interfaces iSCSI pourrait entraîner un temps d’arrêt. Pour réduire le temps d’arrêt, nous vous recommandons de configurer DATA 0 pour le trafic iSCSI.

Effectuez les étapes suivantes pour désactiver l’interface réseau avec la passerelle, puis appliquez la mise à jour.

[AZURE.INCLUDE [storsimple-install-update-option2](../../includes/storsimple-install-update-option2.md)]

[AZURE.INCLUDE [storsimple-install-troubleshooting](../../includes/storsimple-install-troubleshooting.md)]


## Étapes suivantes

En savoir plus sur la [version Update 1.2](storsimple-update1-release-notes.md).

<!---HONumber=AcomDC_0824_2016-->