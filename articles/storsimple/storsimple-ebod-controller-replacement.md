<properties 
   pageTitle="Remplacer un contrôleur EBOD StorSimple | Microsoft Azure"
   description="Explique comment retirer et remplacer un contrôleur ou les deux contrôleurs EBOD sur votre appareil StorSimple 8600."
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

# Remplacer un contrôleur EBOD sur votre appareil StorSimple

## Vue d'ensemble

Ce didacticiel explique comment remplacer un module de contrôleur EBOD défectueux sur votre appareil Microsoft Azure StorSimple. Pour remplacer un module de contrôleur EBOD, vous devez :

- Retirer le contrôleur EBOD défectueux
- Installer un nouveau contrôleur EBOD

Avant de commencer, tenez compte des informations suivantes :

- Des modules EBOD vides doivent être insérés dans tous les emplacements non utilisés. Le boîtier ne se refroidit pas correctement si un emplacement est laissé ouvert.

- Le contrôleur EBOD est échangeable à chaud, et il peut être retiré ou remplacé. Ne retirez pas un module défectueux tant que vous ne disposez pas d’un remplacement. Quand vous commencez le processus de remplacement, vous devez le terminer dans les 10 minutes.

>[AZURE.IMPORTANT] Avant de tenter de retirer ou remplacer un composant StorSimple, passez en revue les [conventions des icônes de sécurité](storsimple-safety.md#safety-icon-conventions) et les autres [précautions de sécurité](storsimple-safety.md).

## Retirer un contrôleur EBOD

Avant de retirer le module de contrôleur EBOD défectueux de votre appareil StorSimple, assurez-vous que l’autre module de contrôleur EBOD est actif et en fonctionnement. La procédure et le tableau suivants expliquent comment retirer le module de contrôleur EBOD.

#### Pour retirer un module EBOD

1. Ouvrez le portail Azure Classic.

2. Accédez à **Appareils** > **Maintenance** > **Statut du matériel**, et vérifiez que l’état de la LED du module de contrôleur EBOD actif est en vert et que la LED pour le module de contrôleur EBOD défaillant est en rouge.

3. Localisez le module de contrôleur EBOD défectueux à l’arrière de l’appareil.

4. Débranchez les câbles qui connectent le module de contrôleur EBOD au contrôleur avant d’enlever le module EBOD du système.

5. Notez le port SAS du module de contrôleur EBOD qui était connecté au contrôleur. Vous devrez restaurer le système dans cette configuration après le remplacement du module EBOD.

    >[AZURE.NOTE] En règle générale, il s’agit du Port A, qui est libellé **Entrée hôte** dans le diagramme suivant.

    ![Fond de panier du contrôleur EBOD](./media/storsimple-ebod-controller-replacement/IC741049.png)

     **Figure 1** : Arrière du module EBOD

    |Étiquette|Description|
    |:----|:----------|
    |1|LED indiquant une panne|
    |2|LED d’alimentation|
    |3|Connecteurs SaaS|
    |4|LED SAS|
    |5|Ports série (utilisation en usine uniquement)|
    |6|Port A (Entrée hôte)|
    |7|Port B (Sortie hôte)|
    |8|Port C (utilisation en usine uniquement)|

## Installer un nouveau contrôleur EBOD

La procédure et le tableau suivants expliquent comment installer un module de contrôleur EBOD dans votre appareil StorSimple.

#### Pour installer un contrôleur EBOD

1. Vérifiez que l’appareil EBOD n’est pas endommagé, en particulier le connecteur d’interface. N’installez pas le nouveau contrôleur EBOD si des broches sont tordues.

2. Avec les verrous en position ouverte, faites glisser le module dans le boîtier jusqu’à ce que les verrous s’engagent.

    ![Installation du contrôleur EBOD](./media/storsimple-ebod-controller-replacement/IC741050.png)

    **Figure 2** : Installation du module de contrôleur EBOD

3. Fermez le verrou. Vous devez entendre un clic quand le verrou s’engage.

    ![Libération du verrou EBOD](./media/storsimple-ebod-controller-replacement/IC741047.png)

    **Figure 3** : Fermeture du verrou du module EBOD

4. Rebranchez les câbles. Utilisez la configuration exacte qui existait avant le remplacement. Consultez le diagramme et le tableau suivants pour des informations sur la façon de connecter les câbles.

    ![Raccorder votre appareil à l’alimentation électrique](./media/storsimple-ebod-controller-replacement/IC770723.png)

    **Figure 4**. Reconnexion des câbles

    |Étiquette|Description|
    |:----|:----------|
    |1|Boîtier principal|
    |2|PCM 0|
    |3|PCM 1|
    |4|Contrôleur 0|
    |5|Contrôleur 1|
    |6|Contrôleur 0 du boîtier EBOD|
    |7|Contrôleur 1 du boîtier EBOD|
    |8|Boîtier EBOD|
    |9|Unités de distribution de l’alimentation|

## Étapes suivantes

En savoir plus sur le [Remplacement des composants matériels StorSimple](storsimple-hardware-component-replacement.md).

<!---HONumber=AcomDC_0824_2016-->