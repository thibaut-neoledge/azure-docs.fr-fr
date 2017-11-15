---
title: "Remplacer le châssis d’un appareil de la gamme StorSimple 8000 | Microsoft Docs"
description: "Décrit comment retirer et remplacer le châssis pour votre boîtier principal StorSimple ou le boîtier EBOD."
services: storsimple
documentationcenter: 
author: alkohli
manager: carmonm
editor: 
ms.assetid: 537659ed-4c46-49c1-b1e4-186262f2542d
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/02/2017
ms.author: alkohli
ms.openlocfilehash: 7bc23e64331ad18c604ffaa29476766827119cd4
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2017
---
# <a name="replace-the-chassis-on-your-storsimple-device"></a>Remplacer le châssis sur votre appareil StorSimple
> [!NOTE]
> Le portail Azure Classic pour StorSimple est déconseillé. Vos instances de StorSimple Device Manager seront automatiquement déplacées vers le nouveau portail Azure à la date de désapprobation planifiée. Vous serez prévenu de ce déplacement par un e-mail et une notification du portail. Ce document sera également bientôt retiré. Pour afficher la version de cet article applicable au nouveau portail Azure, accédez à [Remplacer le châssis sur votre appareil StorSimple](storsimple-8000-chassis-replacement.md). Si vous avez des questions concernant le déplacement, consultez [FAQ : Déplacement vers le portail Azure](storsimple-8000-move-azure-portal-faq.md).

## <a name="overview"></a>Vue d'ensemble
Ce didacticiel explique comment retirer et remplacer un châssis dans un appareil de la gamme StorSimple 8000. Le modèle StorSimple 8100 est un appareil à boîtier unique (un châssis), alors que le modèle 8600 comprend deux boîtiers (deux châssis). Pour un modèle 8600, il existe deux châssis potentiellement défaillants dans l’appareil : le châssis du boîtier principal et le châssis du boîtier EBOD.

Dans les deux cas, le châssis de remplacement livré par Microsoft est vide. Aucun module d’alimentation et de refroidissement (PCM), module de contrôleur, disque SSD, lecteur de disque dur (HDD) ni module EBOD ne seront inclus.

> [!IMPORTANT]
> Avant le retrait et le remplacement du châssis, passez en revue les informations de sécurité dans [Remplacement des composants matériels StorSimple](storsimple-hardware-component-replacement.md).
> 
> 

## <a name="remove-the-chassis"></a>Retirer le châssis
Procédez comme suit pour retirer le châssis sur votre appareil StorSimple.

#### <a name="to-remove-a-chassis"></a>Pour retirer un châssis
1. Assurez-vous que l’appareil StorSimple est éteint et débranché de toutes les sources d’alimentation.
2. Retirez tous les câbles réseau et SAS, le cas échéant.
3. Retirez l’unité du rack.
4. Retirez chacun des disques et notez les emplacements desquels vous les retirez. Pour plus d’informations, consultez [Retrait d’un lecteur de disque](storsimple-disk-drive-replacement.md#remove-the-disk-drive).
5. Dans le boîtier EBOD (s’il s’agit du châssis défaillant), retirez les modules de contrôleur EBOD. Pour plus d’informations, consultez [Retrait d’un contrôleur EBOD](storsimple-ebod-controller-replacement.md#remove-an-ebod-controller). 
   
    Dans le boîtier principal (s’il s’agit du châssis défaillant), retirez les contrôleurs et notez les emplacements desquels vous les retirez. Pour plus d’informations, consultez [Retrait d’un contrôleur](storsimple-controller-replacement.md#remove-a-controller).

## <a name="install-the-chassis"></a>Installer le châssis
Procédez comme suit pour installer le châssis sur votre appareil StorSimple.

#### <a name="to-install-a-chassis"></a>Pour installer un châssis
1. Montez le châssis dans le rack. Pour en savoir plus, consultez la rubrique [Montage en rack de votre appareil StorSimple 8100](storsimple-8100-hardware-installation.md#rack-mount-your-storsimple-8100-device) ou [Montage en rack de votre appareil StorSimple 8600](storsimple-8600-hardware-installation.md#rack-mount-your-storsimple-8600-device).
2. Une fois le châssis monté dans le rack, installez les modules de contrôleur dans les mêmes positions que celles dans lesquelles ils étaient auparavant installés.
3. Installez les lecteurs dans les mêmes positions et emplacements que précédemment.
   
   > [!NOTE]
   > Nous vous recommandons d'installer d'abord les disques SSD dans les emplacements, puis d'installer les lecteurs de disque dur.
   > 
   > 
4. Une fois l’appareil monté dans le rack et les composants installés, connectez votre appareil aux sources d’alimentation appropriées, puis activez l’appareil. Pour en savoir plus, consultez la rubrique [Branchement des câbles de votre appareil StorSimple 8100](storsimple-8100-hardware-installation.md#cable-your-storsimple-8100-device) ou [Branchement des câbles de votre appareil StorSimple 8600](storsimple-8600-hardware-installation.md#cable-your-storsimple-8600-device).

## <a name="next-steps"></a>Étapes suivantes
En savoir plus sur le [remplacement des composants matériels StorSimple](storsimple-hardware-component-replacement.md).

