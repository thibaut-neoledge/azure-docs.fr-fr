---
title: "Installer le portail classique sur un appareil de la gamme StorSimple 8000, Update 5 | Microsoft Docs"
description: "Explique comment installer StorSimple série 8000 Update 5 sur votre appareil StorSimple série 8000 dans le portail classique."
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 10/02/2017
ms.author: alkohli
ms.openlocfilehash: 916884e3ee95284497cded73b28b22e83751f624
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="install-update-5-on-your-storsimple-device"></a>Installer Update 5 sur votre appareil StorSimple

## <a name="overview"></a>Vue d'ensemble

Ce didacticiel explique comment installer Update 5 sur un appareil StorSimple exécutant une version logicielle antérieure par le biais du portail Azure Classic et à l’aide de la méthode du correctif logiciel. La méthode du correctif logiciel est utilisée quand vous essayez d’installer Update 5 sur un appareil exécutant des versions antérieures à Update 3. La méthode du correctif logiciel est également utilisée si une passerelle est configurée sur une interface réseau différente de DATA 0 de l’appareil StorSimple et que la mise à jour porte sur une version logicielle antérieure à Update 1.

Update 5 comprend les mises à jour logicielles de l’appareil, celles de Storport, de Spaceport, de la sécurité du système d’exploitation et du système d’exploitation lui-même ainsi que celle du microprogramme de disque.  Les mises à jour logicielles de l’appareil, comme celles de Spaceport, de Storport, de la sécurité et du système d’exploitation, sont des mises à jour sans interruption de service. Les mises à jour sans interruption de service ou régulières peuvent être appliquées via le portail Azure Classic ou via la méthode du correctif logiciel. Les mises à jour du microprogramme de disque sont des mises à jour avec interruption de service ; elles s’appliquent lorsque l’appareil est en mode de maintenance, par le biais de la méthode du correctif logiciel en utilisant l’interface Windows PowerShell de l’appareil.

> [!IMPORTANT]
> * Un ensemble de vérifications préalables manuelles et automatiques sont effectuées avant l’installation pour déterminer l’intégrité de l’appareil sur le plan de l’état du matériel et de la connectivité réseau. Ces vérifications préalables sont effectuées uniquement si vous appliquez les mises à jour à partir du portail Azure.
> * Si vous exécutez une version antérieure à Update 3, nous vous recommandons vivement d’installer Update 5 via la méthode du correctif logiciel. Pour que l’équipe de support vous guide tout au long de la mise à jour, [enregistrez un ticket de support](storsimple-8000-contact-microsoft-support.md).
> * Si vous exécutez Update 3 et versions ultérieures, nous vous recommandons d’installer les mises à jour du logiciel et d’autres mises à jour standard au moyen du portail Azure Classic. Selon la version que vous mettez à jour, l’installation des mises à jour peut prendre 4 heures (ou plus). Les mises à jour du mode de maintenance doivent être installées via l’interface Windows PowerShell de l’appareil. Les mises à jour en mode de maintenance étant des mises à jour perturbatrices, elles entraînent un temps d’arrêt pour votre appareil.
> * Si vous exécutez l’outil facultatif StorSimple Snapshot Manager, veillez à le mettre à niveau vers la version Update 5 avant de mettre l’appareil à jour.


[!INCLUDE [storsimple-preparing-for-update](../../includes/storsimple-preparing-for-updates.md)]

## <a name="install-update-5-via-the-azure-classic-portal"></a>Installer Update 5 avec le portail Azure Classic
Suivez la procédure ci-dessous pour mettre à jour votre appareil vers [Update 5](storsimple-update5-release-notes.md).

> [!NOTE]
> Microsoft extrait des informations de diagnostic supplémentaires de l’appareil. Par conséquent, lorsque notre équipe en charge des opérations identifie des appareils qui ont un problème, nous sommes plus à même de collecter des informations à partir de ces appareils et de diagnostiquer les problèmes.

[!INCLUDE [storsimple-install-update2-via-portal](../../includes/storsimple-install-update2-via-portal.md)]

Vérifiez que votre appareil exécute **Update 5 (6.3.9600.17845) pour la gamme d’appareils StorSimple 8000**. Le paramètre **Dernière date de mise à jour** doit être modifié.

* Vous verrez maintenant que les mises à jour en mode maintenance sont disponibles (ce message peut continuer à afficher jusqu’à 24 heures après l’installation des mises à jour). Les mises à jour en mode maintenance entraînent des temps d’arrêt de l’appareil et ne peuvent être appliquées que par le biais de l’interface Windows PowerShell de votre appareil.

* Téléchargez les mises à jour en mode maintenance à l’aide de la procédure indiquée dans [Pour télécharger des correctifs logiciels](#to-download-hotfixes) pour rechercher et télécharger KB4011837, qui installe les mises à jour du microprogramme de disque (les autres mises à jour doivent déjà être installées à ce stade). Suivez les étapes répertoriées dans [Installer et vérifier le correctif logiciel en mode Maintenance](#to-install-and-verify-maintenance-mode-hotfixes) pour installer ces mises à jour en mode maintenance.

## <a name="install-update-5-as-a-hotfix"></a>Installer Update 5 en tant que correctif logiciel


Les versions logicielles qui peuvent être mises à niveau à l’aide de la méthode du correctif logiciel sont les suivantes :

* Update 0.1, 0.2, 0.3
* Update 1, 1.1, 1.2
* Update 2, 2.1, 2.2
* Update 3, 3.1
* Update 4

> [!NOTE]
> La méthode recommandée pour installer Update 5 est d’utiliser le portail Azure Classic. Toutefois, si vous exécutez une version antérieure à Update 3, nous vous recommandons vivement d’utiliser cette méthode pour installer Update 5. Vous devez aussi utiliser cette procédure si la vérification de la passerelle échoue lors de la tentative d’installation des mises à jour par le biais du portail Azure Classic. La vérification échoue lorsque vous avez une passerelle affectée à une interface réseau différente de DATA 0 et que votre appareil exécute une version logicielle antérieure à Update 1.

La méthode du correctif logiciel implique les trois étapes suivantes :

1. Télécharger les correctifs logiciels à partir du catalogue Microsoft Update
2. Installer et vérifier les correctifs logiciels en mode Normal
3. Installer et vérifier les correctifs logiciels en mode Maintenance

#### <a name="download-updates-for-your-device"></a>Télécharger des mises à jour pour votre appareil

Vous devez télécharger et installer les correctifs logiciels suivants dans l’ordre indiqué et les dossiers suggérés :

| Ordre | Ko | Description | Type de mise à jour | Durée d’installation |Installer dans le dossier|
| --- | --- | --- | --- | --- | --- |
| 1. |KB4037264 |Mise à jour logicielle<br> Télécharger les deux fichiers _HcsSfotwareUpdate.exe_ et _CisMSDAgent.exe_ |Standard  <br></br>sans interruption de service |~ 25 minutes |FirstOrderUpdate|

Si la mise à jour s’effectue à partir d’un appareil exécutant Update 4, vous devez installer les mises à jour cumulatives du système d’exploitation uniquement en tant que mises à jour de deuxième priorité.

| Ordre | Ko | Description | Type de mise à jour | Durée d’installation |Installer dans le dossier|
| --- | --- | --- | --- | --- | --- |
| 2A. |KB4025336 |Package de mises à jour cumulatives de système d’exploitation <br> Télécharger la version Windows Server 2012 R2 |Standard  <br></br>sans interruption de service |- |SecondOrderUpdate|

Si l’installation s’effectue depuis un appareil exécutant Update 3 ou une version antérieure, installez les éléments suivants en plus des mises à jour cumulatives.

| Ordre | Ko | Description | Type de mise à jour | Durée d’installation |Installer dans le dossier|
| --- | --- | --- | --- | --- | --- |
| 2B. |KB4011841 <br> KB4011842 |Mises à jour du pilote et du microprogramme LSI <br> Mise à jour du microprogramme USM (version 3.38) |Standard  <br></br>sans interruption de service |~ 3 heures <br> (inclut 2A. + 2B. + 2C.)|SecondOrderUpdate|
| 2C. |KB3139398 <br> KB3142030 <br> KB3108381 <br> KB3153704 <br> KB3174644 <br> KB3139914   |Package de mises à jour de sécurité du système d’exploitation <br> Télécharger la version Windows Server 2012 R2 |Standard  <br></br>sans interruption de service |- |SecondOrderUpdate|
| 2D. |KB3146621 <br> KB3103616 <br> KB3121261 <br> KB3123538 |Package de mises à jour du système d’exploitation <br> Télécharger la version Windows Server 2012 R2 |Standard  <br></br>sans interruption de service |- |SecondOrderUpdate|


En outre, vous devez peut-être installer les mises à jour du microprogramme de disque en plus de toutes les mises à jour indiquées dans les tableaux précédents. Pour vérifier si vous avez besoin des mises à jour du microprogramme de disque, vous pouvez exécuter l’applet de commande `Get-HcsFirmwareVersion` . Si vous utilisez la version `XMGJ`, `XGEG`, `KZ50`, `F6C2`, `VR08`, `N003`, `0107` du microprogramme, vous n’avez pas besoin d’installer ces mises à jour.

| Ordre | Ko | Description | Type de mise à jour | Durée d’installation | Installer dans le dossier|
| --- | --- | --- | --- | --- | --- |
| 3. |KB4037263 |Microprogramme de disque |Maintenance  <br></br>Interruption de service |~ 30 minutes | ThirdOrderUpdate |

<br></br>

> [!IMPORTANT]
> * Si vous effectuez la mise à jour à partir d’Update 4, la durée totale de l’installation est d’environ 4 heures.
> * Avant d’utiliser cette procédure pour appliquer la mise à jour, vérifiez que les deux contrôleurs d’appareil sont en ligne et que tous les composants matériels sont sains.

Procédez comme suit pour télécharger et importer les correctifs logiciels.

[!INCLUDE [storsimple-install-update5-hotfix](../../includes/storsimple-install-update5-hotfix.md)]

[!INCLUDE [storsimple-install-troubleshooting](../../includes/storsimple-install-troubleshooting.md)]

## <a name="next-steps"></a>Étapes suivantes
Découvrez plus en détail la [version Update 5](storsimple-update5-release-notes.md).

