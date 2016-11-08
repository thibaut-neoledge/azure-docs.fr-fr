---
title: Gestion des contrôleurs d’appareil StorSimple | Microsoft Docs
description: Découvrez comment arrêter, redémarrer, éteindre ou réinitialiser vos contrôleurs d’appareil StorSimple.
services: storsimple
documentationcenter: ''
author: alkohli
manager: carmonm
editor: ''

ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/11/2016
ms.author: alkohli

---
# <a name="manage-your-storsimple-device-controllers"></a>Gestion de vos contrôleurs d’appareil StorSimple
## <a name="overview"></a>Vue d'ensemble
Ce didacticiel décrit les différentes opérations qui peuvent être effectuées sur vos contrôleurs d’appareil StorSimple. Les contrôleurs de votre appareil StorSimple sont des contrôleurs redondants (homologues) en mode actif/passif. À un moment donné, un seul contrôleur est actif et procède au traitement de toutes les opérations de disque et du réseau. L’autre contrôleur est en mode passif. Si le contrôleur actif est défaillant, le contrôleur passif devient automatiquement actif.

Ce didacticiel inclut des instructions détaillées pour gérer les contrôleurs d’appareil à l’aide de :

* la section **Contrôleurs** de la page **Maintenance** dans le service StorSimple Manager
* Windows PowerShell pour StorSimple

Nous vous recommandons de gérer les contrôleurs d’appareil via le service StorSimple Manager. Si une action ne peut être effectuée qu’à l’aide de Windows PowerShell pour StorSimple, le didacticiel l’indique.

Après avoir lu ce didacticiel, vous pourrez :

* redémarrer ou arrêter un contrôleur d’appareil StorSimple
* arrêter un appareil StorSimple
* rétablir les paramètres d’usine de votre appareil StorSimple

## <a name="restart-or-shut-down-a-single-controller"></a>Redémarrage ou arrêt d’un contrôleur unique
Le redémarrage ou l’arrêt d’un contrôleur n’est pas nécessaire si le système fonctionne normalement. Les opérations d’arrêt d’un contrôleur d’appareil unique ne sont courantes que lorsqu’un composant matériel de l’appareil est défaillant et qu’il doit être remplacé. Le redémarrage d’un contrôleur peut également être nécessaire lorsque les performances sont affectées par une utilisation excessive de la mémoire ou un dysfonctionnement du contrôleur. Vous devrez peut-être également redémarrer un contrôleur qui a été remplacé, si vous souhaitez l’activer et le tester.

Le redémarrage d’un appareil n’est pas sans interruption pour les initiateurs connectés, en supposant que le contrôleur passif est disponible. Si un contrôleur passif est indisponible ou bien désactivé, son redémarrage peut entraîner l’interruption du service.

> [!IMPORTANT]
> * **Un contrôleur en cours d’exécution ne doit jamais être physiquement supprimé, car cela entraînerait une perte de redondance et une augmentation des risques d’interruption.**
> * La procédure suivante s’applique uniquement à l’appareil physique StorSimple. Pour plus d’informations sur le démarrage, l’arrêt et le redémarrage de l’appareil virtuel, voir la rubrique [Utilisation de l’appareil virtuel](storsimple-virtual-device-u2.md#work-with-the-storsimple-virtual-device).
> 
> 

Vous pouvez redémarrer ou arrêter un contrôleur d’appareil donné à l’aide du portail Azure Classic du Service StorSimple Manager ou de Windows PowerShell pour StorSimple

Pour gérer vos contrôleurs d’appareil à partir du portail Azure Classic, procédez comme suit.

#### <a name="to-restart-or-shut-down-a-controller-in-classic-portal"></a>Pour redémarrer ou arrêter un contrôleur dans le portail Azure Classic
1. Accédez à **Appareils > Maintenance**.
2. Accédez à **Statut matériel**, puis vérifiez que le statut des deux contrôleurs de votre appareil est **Sain**.
   
    ![Vérifiez que les contrôleurs d’appareil StorSimple sont en bon état de fonctionnement](./media/storsimple-manage-device-controller/IC766017.png)
3. En bas de la page **Maintenance**, cliquez sur **Gérer les contrôleurs**.
   
    ![Gestion des contrôleurs d’appareil StorSimple](./media/storsimple-manage-device-controller/IC766018.png)</br>
   
   > [!NOTE]
   > Si l’option **Gérer les contrôleurs**n’apparaît pas, vous devez installer des mises à jour. Pour plus d’informations, consultez [Mise à jour de votre appareil StorSimple](storsimple-update-device.md).
   > 
   > 
4. Dans la boîte de dialogue **Modifier les paramètres du contrôleur** , procédez comme suit :
   
   1. Dans la liste déroulante **Sélectionner un contrôleur** , sélectionnez le contrôleur à gérer. Les options sont Contrôleur 0 et Contrôleur 1. Ces contrôleurs sont également identifiés comme actifs ou passifs.
      
      > [!NOTE]
      > Un contrôleur ne peut pas être géré s’il est indisponible ou désactivé, et il n’apparaîtra pas dans la liste déroulante.
      > 
      > 
   2. Dans la liste déroulante **Sélectionner une action**, choisissez **Redémarrer le contrôleur** ou **Arrêter le contrôleur**.
      
       ![Redémarrage du contrôleur passif de l’appareil StorSimple](./media/storsimple-manage-device-controller/IC766020.png)
   3. Cliquez sur l’icône en forme de coche  ![Icône en forme de coche](./media/storsimple-manage-device-controller/IC740895.png).

Ceci redémarrera ou arrêtera le contrôleur. Le tableau ci-dessous récapitule les détails de ce qui se passe en fonction des sélections effectuées dans la boîte de dialogue **Modifier les paramètres du contrôleur** .  

| Sélection # | Si vous choisissez de... | Ceci se produira. |
| --- | --- | --- |
| 1. |Redémarrer le contrôleur passif. |Un travail sera créé pour procéder au redémarrage du contrôleur, et un message s’affichera une fois l’opération terminée. Ceci lancera le redémarrage du contrôleur. Vous pouvez analyser le processus de redémarrage en accédant à **Service > Tableau de bord Affichage des journaux des opérations**, puis en filtrant à l’aide des paramètres spécifiques de votre service. |
| 2. |Redémarrer le contrôleur actif. |L’avertissement suivant s’affiche : « Si vous redémarrez le contrôleur actif, l’appareil basculera vers le contrôleur passif. Voulez-vous continuer ? </br>Si vous choisissez d’effectuer cette opération, la suite de la procédure est identique à celle utilisée pour redémarrer le contrôleur passif (voir sélection 1). |
| 3. |Arrêter le contrôleur passif. |Le message suivant s’affiche: « Une fois arrêté, vous devrez appuyer sur le bouton d’alimentation de votre contrôleur pour l’activer. Voulez-vous vraiment arrêter ce contrôleur ? </br>Si vous choisissez d’effectuer cette opération, la suite de la procédure est identique à celle utilisée pour redémarrer le contrôleur passif (voir sélection 1). |
| 4. |Arrêter le contrôleur actif. |Le message suivant s’affiche: « Une fois arrêté, vous devrez appuyer sur le bouton d’alimentation de votre contrôleur pour l’activer. Voulez-vous vraiment arrêter ce contrôleur ? </br>Si vous choisissez d’effectuer cette opération, la suite de la procédure est identique à celle utilisée pour redémarrer le contrôleur passif (voir sélection 1). |

#### <a name="to-restart-or-shut-down-a-controller-in-windows-powershell-for-storsimple"></a>Pour redémarrer ou arrêter un contrôleur dans Windows PowerShell pour StorSimple
Pour arrêter ou redémarrer un contrôleur sur votre appareil StorSimple à partir du portail Azure Classic, procédez comme suit.

1. Accédez à l’appareil à l’aide de la console série ou d’une session telnet à partir d’un ordinateur distant. Connectez-vous au Contrôleur 0 ou au Contrôleur 1 en suivant la procédure décrite dans [Utilisation de PuTTY pour se connecter à la console série de l’appareil](storsimple-deployment-walkthrough.md#use-putty-to-connect-to-the-device-serial-console).
2. Dans le menu de la console série, sélectionnez l’option 1, **Ouvrir une session avec un accès total**.
3. Dans le message de bannière, prenez note du contrôleur auquel vous êtes connecté (contrôleur 0 ou contrôleur 1) et vérifiez s’il est actif ou bien passif (en veille).
   
   * Pour arrêter un contrôleur unique, à l’invite de commandes, tapez :
     
       `Stop-HcsController`
     
       Ceci arrêtera le contrôleur auquel vous êtes connecté. Si vous arrêtez le contrôleur actif, l’appareil basculera vers le contrôleur passif avant de s’arrêter.
   * Pour redémarrer un contrôleur, à l’invite de commandes, tapez :
     
       `Restart-HcsController`
     
       Ceci redémarrera le contrôleur auquel vous êtes connecté. Si vous redémarrez le contrôleur actif, l’appareil basculera vers le contrôleur passif avant de redémarrer.

## <a name="shut-down-a-storsimple-device"></a>arrêter un appareil StorSimple
Cette section vous explique comment arrêter un appareil StorSimple en cours d’exécution ou défaillant à partir d’un ordinateur distant. Un appareil est désactivé dès lors que les contrôleurs d’appareil sont arrêtés. L’arrêt d’un appareil est exécuté lorsque l’appareil a été physiquement déplacé ou mis hors service.

> [!IMPORTANT]
> Avant d’arrêter l’appareil, vérifiez l’intégrité des composants de l’appareil. Accédez à **Appareils > Maintenance > État du matériel** et assurez-vous que l’état des LED des composants est vert. Un appareil en bon état de fonctionnement aura un état vert. Si votre appareil est sur le point d’être arrêté pour procéder au remplacement d’un composant défaillant, vous verrez s’afficher un état Échec (rouge) ou un état Détérioré (jaune) pour les composants respectifs.
> 
> 

#### <a name="to-shut-down-a-storsimple-device"></a>Pour arrêter un appareil StorSimple
1. Utilisez la procédure [Redémarrage ou d’arrêt d’un contrôleur](#restart-or-shut-down-a-single-controller) pour identifier et arrêter le contrôleur passif sur votre appareil. Vous pouvez effectuer cette opération dans le portail Azure Classic ou dans Windows PowerShell pour StorSimple.
2. Répétez l’étape précédente pour arrêter le contrôleur actif.
3. Vous devez maintenant examiner le panneau arrière de l’appareil. Une fois les deux contrôleurs arrêtés, l’état des LED des deux contrôleurs doit être rouge clignotant. Si vous souhaitez désactiver complètement l’appareil à ce stade, assurez-vous que les commutateurs d’alimentation sont en position d’arrêt sur tous les PCM. Ceci désactivera l’appareil.

<!--#### To shut down a StorSimple device in Windows PowerShell for StorSimple

1. Connect to the serial console of the StorSimple device by following the steps in [Use PuTTY to connect to the device serial console](storsimple-deployment-walkthrough.md#use-putty-to-connect-to-the-serial-console).

1. In the serial console menu, verify from the banner message that the controller you are connected to is the passive controller. If you are connected to the active controller, disconnect from this controller and connect to the other controller.

1. In the serial console menu, choose option 1, **log in with full access**.

1. At the prompt, type:

    `Stop-HCSController`

    This should shut down the current controller. To verify whether the shutdown has finished, check the back of the device. The controller status LED should be solid red.

1. Repeat steps 1 through 4 to connect to the active controller and then shut it down.

1. After both the controllers are completely shut down, the status LEDs on both should be blinking red. If you need to turn off the device completely at this time, flip the power switches on both Power and Cooling Modules (PCMs) to the OFF position.-->

## <a name="reset-the-device-to-factory-default-settings"></a>Rétablissement des paramètres d’usine de l’appareil
> [!IMPORTANT]
> Si vous devez rétablir les paramètres d’usine de l’appareil, contactez le Support Microsoft. La procédure décrite ci-dessous ne doit être effectuée qu’en coordination avec le Support Microsoft.
> 
> 

Cette procédure explique comment rétablir les paramètres d’usine de votre appareil Microsoft Azure StorSimple à l’aide de Windows PowerShell pour StorSimple.
La réinitialisation d’un appareil supprime toutes les données et tous les paramètres de l’ensemble du cluster par défaut.

Pour rétablir les paramètres d’usine de votre appareil Microsoft Azure StorSimple, procédez comme suit :

### <a name="to-reset-the-device-to-default-settings-in-windows-powershell-for-storsimple"></a>Pour rétablir les paramètres d’usine de l’appareil dans Windows PowerShell pour StorSimple
1. Accédez à l’appareil via sa console série. Vérifiez le message de bannière pour vous assurer que vous êtes connecté au contrôleur actif.
2. Dans le menu de la console série, sélectionnez l’option 1, **Ouvrir une session avec un accès total**.
3. À l’invite, tapez la commande suivante pour réinitialiser l’ensemble du cluster et ainsi supprimer toutes les données, toutes les métadonnées et tous les paramètres du contrôleur :
   
    `Reset-HcsFactoryDefault`
   
    Pour réinitialiser un seul contrôleur, utilisez l’applet de commande [Reset-HcsFactoryDefault](http://technet.microsoft.com/library/dn688132.aspx) avec le paramètre `-scope`.)
   
    Le système redémarrera plusieurs fois. Un message s’affichera une fois la réinitialisation terminée. Selon le modèle du système, l’opération peut prendre 45 à 60 minutes pour un appareil 8 100 et 60 à 90 minutes pour un appareil 8 600.
   
   > [!TIP]
   > * Si vous utilisez Update 1.2 ou une version antérieure, servez-vous du paramètre `–SkipFirmwareVersionCheck` pour ignorer la vérification de la version du microprogramme. Sinon, une erreur d’incompatibilité du microprogramme s’affiche : la réinitialisation aux paramètres d’usine ne peut pas se poursuivre en raison d’une incohérence dans les versions du microprogramme.
   > * La procédure de réinitialisation aux paramètres d’usine peut échouer pour les appareils StorSimple qui exécutent la mise à jour 1 ou 1.1 sur le portail Government et ont effectué un remplacement d’un seul ou de deux contrôleurs avec succès (avec des contrôleurs de remplacement livrés avec un logiciel antérieur à la mise à jour 1). Cela se produit lorsque l’image de réinitialisation aux paramètres d’usine est validée pour la présence d'un fichier SHA1 sur le contrôleur qui n'existe pas pour un logiciel antérieur à la mise à jour 1. Si vous constatez l’échec de cette réinitialisation aux paramètres d’usine, contactez le support Microsoft pour obtenir de l’aide lors des étapes suivantes. Ce problème ne se produit pas avec des contrôleurs de remplacement sortis d’usine avec la mise à jour 1 ou une version ultérieure du logiciel.
   > 
   > 

## <a name="questions-and-answers-about-managing-device-controllers"></a>Questions et réponses sur la gestion des contrôleurs d’appareil
Dans cette section, vous trouverez des réponses à certaines questions fréquemment posées, relatives à la gestion des contrôleurs d’appareil StorSimple.

**Q.**  Que se passe-t-il si les deux contrôleurs de mon appareil sont activés et en bon état de fonctionnement, et que je redémarre ou arrête le contrôleur actif ?

**A.**  Si les deux contrôleurs de votre appareil sont activés et en bon état de fonctionnement, vous serez invité à confirmer l’opération. Vous pouvez choisir de :

* **Redémarrer le contrôleur actif** : un message vous avertit que le redémarrage d’un contrôleur actif entraîne un basculement de l’appareil vers le contrôleur passif. Le contrôleur redémarrera.
* **Arrêter un contrôleur actif** : un message vous avertit que l’arrêt d’un contrôleur actif entraîne une coupure du service. Vous devrez également appuyer sur le bouton d’alimentation de l’appareil pour activer le contrôleur.

**Q.**  Que se passe-t-il si le contrôleur passif de mon appareil est indisponible ou désactivé, et que je redémarre ou arrête le contrôleur actif ?

**A.**  Si le contrôleur passif de votre appareil est indisponible ou désactivé, et que vous choisissez de :

* **Redémarrer le contrôleur actif** : un message vous indiquera que la poursuite de l'opération entraînera une interruption temporaire du service, et vous serez invité à confirmer l’opération.
* **Arrêter un contrôleur actif** : un message vous avertit que la poursuite de l’opération entraîne une interruption du service, et que vous devez appuyer sur le bouton d’alimentation d’au moins un des contrôleurs pour activer l’appareil. Vous êtes invité à confirmer l’opération.

**Q.**  À quel moment le redémarrage ou l’arrêt du contrôleur échoue-t-il ?

**A.**  Le redémarrage ou l’arrêt d’un contrôleur peut échouer si :

* Une mise à jour de l’appareil est en cours.
* Le redémarrage d’un contrôleur est déjà en cours.
* L’arrêt d’un contrôleur est déjà en cours.

**Q.**  Comment pouvez-vous déterminer si un contrôleur a été redémarré ou arrêté ?

**A.**  Vous pouvez vérifier l’état du contrôleur dans la page Maintenance. L’état du contrôleur vous indiquera si un contrôleur a été redémarré ou arrêté. De plus, la page d’alertes contiendra une alerte d’information vous indiquant si le contrôleur a été redémarré ou arrêté. Les opérations de redémarrage et d’arrêt du contrôleur sont également enregistrées dans les journaux des opérations. Pour plus d’informations sur les journaux des opérations, consultez [Affichage des journaux des opérations](storsimple-service-dashboard.md#view-the-operations-logs).

**Q.**  Le basculement du contrôleur a-t-il un impact sur les E/S ?

**A.**  Les connexions TCP entre les initiateurs et le contrôleur actif sont réinitialisées à la suite du basculement du contrôleur, mais elles seront rétablies lorsque le contrôleur passif effectuera l’opération. Une interruption temporaire (moins de 30 secondes) peut survenir dans l’activité d’E/S entre les initiateurs et l’appareil au cours de cette opération.

**Q.**  Comment puis-je remettre mon contrôleur en service une fois qu’il a été arrêté et supprimé ?

**A.** Pour remettre en service un contrôleur, vous devez l’insérer dans le châssis, comme décrit dans [Remplacement d’un module de contrôleur sur votre appareil StorSimple](storsimple-controller-replacement.md).

## <a name="next-steps"></a>Étapes suivantes
* Si vous rencontrez des problèmes avec vos contrôleurs d’appareil StorSimple, que vous ne pouvez pas résoudre en suivant les procédures décrites dans ce didacticiel, [contactez le support technique Microsoft](storsimple-contact-microsoft-support.md).
* Pour en savoir plus sur l’utilisation du service StorSimple Manager, consultez [Utilisation du service StorSimple Manager pour gérer votre appareil StorSimple](storsimple-manager-service-administration.md).

<!--HONumber=Oct16_HO2-->


