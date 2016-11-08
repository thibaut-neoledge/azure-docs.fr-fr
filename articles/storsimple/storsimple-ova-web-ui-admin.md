---
title: Administration de l'interface utilisateur web de StorSimple Virtual Array | Microsoft Docs
description: Décrit comment effectuer des tâches d'administration de base sur l'appareil avec l'interface utilisateur web de StorSimple Virtual Array.
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: ''

ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 04/07/2016
ms.author: alkohli

---
# Utiliser l'interface utilisateur web pour gérer votre StorSimple Virtual Array
![flux du processus d'installation](./media/storsimple-ova-web-ui-admin/manage4.png)

## Vue d’ensemble
Les didacticiels de cet article s'appliquent à Microsoft Azure StorSimple Virtual Array (également appelé appareil virtuel StorSimple local) exécutant la version de mise à la disposition générale (mars 2016). Cet article décrit certains des flux de travail et certaines tâches de gestion complexes qui peuvent être effectués sur StorSimple Virtual Array. Vous pouvez gérer StorSimple Virtual Array à l’aide de l'interface utilisateur du service StorSimple Manager (également appelée interface utilisateur du portail) et de l'interface utilisateur web locale de l'appareil. Cet article se concentre sur les tâches que vous pouvez effectuer à l'aide de l'interface utilisateur web.

Cet article inclut les didacticiels suivants :

* Obtenir la clé de chiffrement des données de service
* Résoudre les erreurs d'installation de l'interface utilisateur web
* Générer un package de journaux
* Arrêter ou redémarrer votre appareil

## Obtenir la clé de chiffrement des données de service
Une clé de chiffrement des données de service est générée lorsque vous enregistrez votre premier appareil auprès du service StorSimple Manager. Cette clé et la clé d'enregistrement de service sont ensuite requises pour l'inscription d'appareils supplémentaires avec le service StorSimple Manager.

Si vous avez égaré votre clé de chiffrement des données de service, procédez comme suit dans l'interface utilisateur web locale de l'appareil inscrit auprès du service pour la récupérer.

#### Pour obtenir la clé de chiffrement des données de service
1. Connectez-vous à l'interface utilisateur web locale. Accédez à **Configuration** > **Paramètres du Cloud**.
2. En bas de la page, cliquez sur **Obtenir la clé de chiffrement de données du service**. Une clé s'affiche. Copiez et enregistrez cette clé.
   
    ![obtenir la clé de chiffrement des données du service 1](./media/storsimple-ova-web-ui-admin/image27.png)

## Résoudre les erreurs d'installation de l'interface utilisateur web
Dans certains cas, lorsque vous configurez l'appareil via l'interface utilisateur web locale, vous pouvez rencontrer les erreurs. Pour diagnostiquer et résoudre de telles erreurs, vous pouvez exécuter les tests de diagnostic.

#### Pour exécuter les tests de diagnostic
1. Dans l’interface utilisateur web locale, accédez à **Dépannage** > **Tests de diagnostic**.
   
    ![exécuter les diagnostics 1](./media/storsimple-ova-web-ui-admin/image29.png)
2. En bas de la page, cliquez sur **Exécuter les tests de diagnostic**. Cette opération lance des tests pour diagnostiquer les éventuels problèmes sur votre réseau, appareil, proxy web, horodatage ou paramètres cloud. Vous serez averti que des tests sont en cours sur l'appareil.
3. Une fois les tests terminés, les résultats seront affichés. L'exemple suivant illustre les résultats des tests de diagnostic. Notez que les paramètres du proxy web n'étaient pas configurés sur cet appareil et, par conséquent, le test de proxy web n'a pas été exécuté. Tous les autres tests pour les paramètres réseau, le serveur DNS et les paramètres d'heure ont été effectués.
   
    ![exécuter les diagnostics 2](./media/storsimple-ova-web-ui-admin/image30.png)

## Générer un package de journaux
Un package de journaux contient tous les journaux pertinents qui peuvent aider l'équipe de support technique de Microsoft à résoudre les problèmes des appareils. Dans cette version, vous pouvez générer un package de journaux via l'interface utilisateur web locale.

#### Pour générer le package de journaux
1. Dans l’interface utilisateur web locale, accédez à **Dépannage** > **Journaux système**.
   
    ![générer un package de journaux 1](./media/storsimple-ova-web-ui-admin/image31.png)
2. En bas de la page, cliquez sur **Créer un package de journaux**. Un package des journaux système sera créé. Cette opération prend quelques minutes.
   
    ![générer un package de journaux 2](./media/storsimple-ova-web-ui-admin/image32.png)
   
    Vous serez averti une fois le package créé et la page sera actualisée pour indiquer l'heure et la date de création du package.
   
    ![générer un package de journaux 3](./media/storsimple-ova-web-ui-admin/image33.png)
3. Cliquez sur **Télécharger le package de journaux**. Un package zippé sera téléchargé sur votre système.
   
    ![générer un package de journaux 4](./media/storsimple-ova-web-ui-admin/image34.png)
4. Vous pouvez décompresser le package de journaux téléchargé et afficher les fichiers journaux système.

## Arrêter et redémarrer votre appareil
Vous pouvez arrêter ou redémarrer votre appareil virtuel à l'aide de l'interface utilisateur web locale. Avant de redémarrer, nous vous recommandons de mettre les volumes ou les partages hors connexion sur l’ordinateur hôte. Vous réduisez ainsi toute possibilité d’altération des données.

#### Pour arrêter votre appareil virtuel
1. Dans l’interface utilisateur web locale, accédez à **Maintenance** > **Paramètres d’alimentation**.
2. Au bas de la page, cliquez sur **Arrêt**.
   
    ![arrêter l'appareil 1](./media/storsimple-ova-web-ui-admin/image36.png)
3. Un avertissement s’affiche indiquant qu’un arrêt de l’appareil va interrompre toutes les E/S qui étaient en cours, ce qui entraîne une interruption du service. Cliquez sur l’icône en forme de coche ![icône en forme de coche](./media/storsimple-ova-web-ui-admin/image3.png).
   
    ![avertissement d'arrêt de l'appareil](./media/storsimple-ova-web-ui-admin/image37.png)
   
    Vous serez averti que l'arrêt a été initié.
   
    ![arrêt de l'appareil initié](./media/storsimple-ova-web-ui-admin/image38.png)
   
    L'appareil va s'arrêter. Si vous souhaitez démarrer votre appareil, vous devrez le faire par le biais du Gestionnaire Hyper-V.

#### Pour redémarrer votre appareil virtuel
1. Dans l’interface utilisateur web locale, accédez à **Maintenance** > **Paramètres d’alimentation**.
2. En bas de la page, cliquez sur **Redémarrer**.
   
    ![redémarrer l'appareil](./media/storsimple-ova-web-ui-admin/image36.png)
3. Un avertissement s'affiche indiquant qu'un redémarrage de l'appareil va interrompre toutes les E/S qui étaient en cours, ce qui entraîne une interruption du service. Cliquez sur l’icône en forme de coche ![icône en forme de coche](./media/storsimple-ova-web-ui-admin/image3.png).
   
    ![avertissement de redémarrage](./media/storsimple-ova-web-ui-admin/image37.png)
   
    Vous serez averti que le redémarrage a été initié.
   
    ![redémarrage initié](./media/storsimple-ova-web-ui-admin/image39.png)
   
    Lorsque le redémarrage est en cours, la connexion à l'interface utilisateur s'interrompt. Vous pouvez surveiller le redémarrage en actualisant périodiquement l'interface utilisateur. Vous pouvez également surveiller l'état du redémarrage de l'appareil avec le Gestionnaire Hyper-V.

## Étapes suivantes
Découvrez comment [utiliser le service StorSimple Manager pour gérer votre appareil](storsimple-manager-service-administration.md).

<!---HONumber=AcomDC_0413_2016-->