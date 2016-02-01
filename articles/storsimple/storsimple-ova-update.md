<properties 
   pageTitle="Application de mises à jour pour StorSimple Virtual Array | Microsoft Azure"
   description="Décrit comment utiliser l'interface utilisateur web de StorSimple Virtual Array pour appliquer les mises à jour et les correctifs"
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
   ms.date="01/13/2016"
   ms.author="alkohli" />

# Application des mises à jour et des correctifs logiciels à StorSimple Virtual Array (version préliminaire)

## Vue d’ensemble

Pour maintenir StorSimple Virtual Array à jour, vous devrez peut-être appliquer des mises à jour et des correctifs logiciels. En règle générale, nous vous recommandons d'installer les mises à jour par le biais du portail Azure Classic. Toutefois, dans les cas où ce dernier n'est pas disponible, vous pouvez utiliser l'interface utilisateur web locale pour appliquer les correctifs ou les mises à jour. Ce didacticiel explique comment utiliser l'interface utilisateur web locale pour appliquer une mise à jour ou un correctif. (Pour connaître les procédures lors de l'installation des mises à jour via le portail classique, consultez [Installer des mises à jour périodiques via le portail Azure Classic](storsimple-update-device.md#install-regular-updates-via-the-azure-classic-portal).)

N'oubliez pas que l'installation d'une mise à jour ou d'un correctif logiciel peut redémarrer votre appareil. Étant donné que StorSimple Virtual Array est un appareil à nœud unique, les E/S en cours seront interrompues et votre appareil subira des temps d'arrêt.

## Utilisation de l'interface utilisateur web locale pour appliquer une mise à jour ou un correctif

Avant l'installation de la mise à jour ou du correctif, vérifiez que la mise à jour ou le correctif est téléchargé(e) localement sur votre ordinateur hôte ou accessible via un partage réseau.

#### Pour installer la mise à jour ou le correctif

1. Dans l'interface utilisateur web locale, accédez à **Maintenance** > **Mise à jour logicielle**.

2. Dans le **chemin d'accès au fichier de mise à jour**, entrez le nom de fichier pour la mise à jour ou le correctif. Vous pouvez également accéder au fichier d'installation de la mise à jour ou du correctif si celui-ci est placé sur un partage réseau. La mise à jour démarre. Une fois celle-ci terminée, vous serez averti.

    ![mettre à jour l'appareil](./media/storsimple-ova-update/image43.png)

## Étapes suivantes

En savoir plus sur la [gestion de votre StorSimple Virtual Array](storsimple-ova-web-ui-admin.md).

<!---HONumber=AcomDC_0121_2016-->