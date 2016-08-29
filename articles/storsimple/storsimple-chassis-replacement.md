<properties 
   pageTitle="Remplacer le châssis sur un appareil StorSimple | Microsoft Azure"
   description="Décrit comment retirer et remplacer le châssis pour votre boîtier principal StorSimple ou le boîtier EBOD."
   services="storsimple"
   documentationCenter=""
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="08/17/2016"
   ms.author="alkohli" />

# Remplacer le châssis sur votre appareil StorSimple

## Vue d'ensemble

Ce didacticiel explique comment retirer et remplacer un châssis dans un appareil de la gamme StorSimple 8000. Le modèle StorSimple 8100 est un appareil à boîtier unique (un châssis), alors que le modèle 8600 comprend deux boîtiers (deux châssis). Pour un modèle 8600, il existe deux châssis potentiellement défaillants dans l’appareil : le châssis du boîtier principal et le châssis du boîtier EBOD.

Dans les deux cas, le châssis de remplacement livré par Microsoft est vide. Aucun module d’alimentation et de refroidissement (PCM), module de contrôleur, disque SSD, lecteur de disque dur (HDD) ni module EBOD ne seront inclus.

>[AZURE.IMPORTANT] Avant le retrait et le remplacement du châssis, passez en revue les informations de sécurité dans [Remplacement des composants matériels StorSimple](storsimple-hardware-component-replacement.md).

## Retirer le châssis

Procédez comme suit pour retirer le châssis sur votre appareil StorSimple.

#### Pour retirer un châssis

1. Assurez-vous que l’appareil StorSimple est éteint et débranché de toutes les sources d’alimentation.

2. Retirez tous les câbles réseau et SAS, le cas échéant.

3. Retirez l’unité du rack.

4. Retirez chacun des disques et notez les emplacements desquels vous les retirez. Pour plus d’informations, consultez [Retrait d’un lecteur de disque](storsimple-disk-drive-replacement.md#remove-the-disk-drive).

5. Dans le boîtier EBOD (s’il s’agit du châssis défaillant), retirez les modules de contrôleur EBOD. Pour plus d’informations, consultez [Retrait d’un contrôleur EBOD](storsimple-ebod-controller-replacement.md#remove-an-ebod-controller).

    Dans le boîtier principal (s’il s’agit du châssis défaillant), retirez les contrôleurs et notez les emplacements desquels vous les retirez. Pour plus d’informations, consultez [Retrait d’un contrôleur](storsimple-controller-replacement.md#remove-a-controller).

## Installer le châssis

Procédez comme suit pour installer le châssis sur votre appareil StorSimple.

#### Pour installer un châssis

1. Montez le châssis dans le rack. Pour plus d’informations, consultez [Montage en rack de votre appareil StorSimple 8100](storsimple-8100-hardware-installation.md#rack-mount-your-storsimple-8100-device) ou [Montage en rack de votre appareil StorSimple 8600](storsimple-8600-hardware-installation.md#rack-mount-your-storsimple-8600-device).

2. Une fois le châssis monté dans le rack, installez les modules de contrôleur dans les mêmes positions que celles dans lesquelles ils étaient auparavant installés.

3. Installez les lecteurs dans les mêmes positions et emplacements que précédemment.

    >[AZURE.NOTE] Nous vous recommandons d'installer d'abord les disques SSD dans les emplacements, puis d'installer les lecteurs de disque dur.

2. Une fois l’appareil monté dans le rack et les composants installés, connectez votre appareil aux sources d’alimentation appropriées, puis activez l’appareil. Pour plus d’informations, consultez [Branchement des câbles de votre appareil StorSimple 8100](storsimple-8100-hardware-installation.md#cable-your-storsimple-8100-device) ou [Branchement des câbles de votre appareil StorSimple 8600](storsimple-8600-hardware-installation.md#cable-your-storsimple-8600-device).

## Étapes suivantes

En savoir plus sur le [remplacement des composants matériels StorSimple](storsimple-hardware-component-replacement.md).

<!---HONumber=AcomDC_0817_2016-->