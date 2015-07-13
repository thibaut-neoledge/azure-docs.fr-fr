<properties 
   pageTitle="Configuration et inscription de votre appareil"
   description="Explique comment utiliser Windows PowerShell pour StorSimple pour configurer et inscrire un appareil StorSimple qui exécute Update 1."
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="adinah"
   editor="tysonn" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="05/22/2015"
   ms.author="alkohli" />


### Configuration et inscription de l’appareil

1. Accédez à l’interface Windows PowerShell sur la console série de votre appareil StorSimple. Consultez la page [Utilisation de PuTTY pour se connecter à la console en série de l’appareil](#use-putty-to-connect-to-the-device-serial-console) pour obtenir des instructions. **Veillez à suivre la procédure pas à pas, sans quoi vous ne pourrez pas accéder à la console.**

2. Dans la session qui s’ouvre, appuyez une fois sur Entrée pour lancer une invite de commandes.

3. Vous devez choisir la langue que vous souhaitez définir pour votre appareil. Spécifiez la langue, puis appuyez sur Entrée.

    ![Configuration et inscription de l’appareil StorSimple 1](./media/storsimple-configure-and-register-device-gov/HCS_RegisterYourDevice1-gov-include.png)

4. Dans le menu de console en série qui s’affiche, choisissez l’option 1 pour ouvrir une session offrant un accès complet.

    ![Inscription de l’appareil StorSimple 2](./media/storsimple-configure-and-register-device-gov/HCS_RegisterYourDevice2-gov-include.png)
  
5. Procédez comme suit pour configurer les paramètres réseau minimum requis pour votre appareil.

    > [AZURE.IMPORTANT]Ces étapes de configuration doivent être effectuées sur le contrôleur actif de l’appareil. Le menu de la console en série indique l’état du contrôleur dans le message de bannière. Si vous n’êtes pas connecté au contrôleur actif, déconnectez-vous, puis connectez-vous à ce contrôleur.

      1. À l’invite de commandes, tapez votre mot de passe. Le mot de passe par défaut de l’appareil est **Password1**.

      2. Tapez la commande suivante :

           `Invoke-HcsSetupWizard`

      3. Un Assistant Installation s’affiche pour vous aider à configurer les paramètres réseau de l’appareil. Fournissez les informations suivantes :

       - Adresse IP de l’interface réseau DATA 0
       - Masque de sous-réseau
       - Passerelle
       - Adresse IP du serveur DNS principal
       - Adresse IP du serveur NTP principal
 
        > [AZURE.NOTE]Vous devrez peut-être attendre quelques minutes que les paramètres de masque de sous-réseau et de DNS soient appliqués.

      4. Configurez éventuellement votre serveur proxy web.

      >[AZURE.IMPORTANT]Bien que la configuration du proxy web soit facultative, si vous en utilisez un, vous pouvez uniquement le configurer ici. Pour plus d’informations, consultez la section [Configuration du proxy web pour votre appareil](https://msdn.microsoft.com/library/azure/dn764937.aspx).

6. Appuyez sur Ctrl+C pour quitter l’Assistant Installation.
 
7. Installez les mises à jour comme suit :
      1. Pour définir les adresses IP sur les deux contrôleurs, utilisez l’applet de commande suivante :

         `Set-HcsNetInterface -InterfaceAlias Data0 -Controller0IPv4Address <Controller0 IP> -Controller1IPv4Address <Controller1 IP>`

      2. À l’invite de commandes, exécutez `Get-HcsUpdateAvailability`. Vous devez recevoir une notification indiquant que les mises à jour sont disponibles.

      3. Exécutez `Start-HcsUpdate`. Vous pouvez exécuter cette commande sur n’importe quel nœud. Les mises à jour sont appliquées sur le premier contrôleur, le contrôleur bascule, puis les mises à jour sont appliquées à l’autre contrôleur.

      Pour surveiller la progression de la mise à jour, exécutez `Get-HcsUpdateStatus`.

       L’exemple de sortie suivant indique que la mise à jour est en cours.
  
        ````
        Controller0>Get-HcsUpdateStatus
        RunInprogress       : True
        LastHotfixTimestamp : 4/13/2015 10:56:13 PM
        LastUpdateTimestamp : 4/13/2015 10:35:25 PM
        Controller0Events   :
        Controller1Events   : 
        ````
 
     L’exemple de sortie suivant indique que la mise à jour est terminée.

        ````
        Controller1>Get-HcsUpdateStatus

        RunInprogress       : False
        LastHotfixTimestamp : 4/13/2015 10:56:13 PM
        LastUpdateTimestamp : 4/13/2015 10:35:25 PM
        Controller0Events   :
        Controller1Events   :

      L’application de toutes les mises à jour, y compris Windows Updates, peut prendre jusqu’à 11 heures.

8. Une fois toutes les mises à jour installées, exécutez l’applet de commande suivante pour confirmer que les mises à jour logicielles ont été appliquées correctement :

     `Get-HcsSystem`

    Les versions suivantes doivent apparaître : - HcsSoftwareVersion: 6.3.9600.17491 - CisAgentVersion: 1.0.9037.0 - MdsAgentVersion: 26.0.4696.1433
 
9. Exécutez l’applet de commande suivante pour confirmer que la mise à jour du microprogramme a été appliquée correctement :

    `Start-HcsFirmwareCheck`.

     L’état du microprogramme doit être **À jour**.

10. Exécutez l’applet de commande suivante pour faire pointer l’appareil vers le portail Microsoft Azure Government (il pointe en effet vers le portail de gestion Azure public par défaut). Les deux contrôleurs redémarrent. Nous vous recommandons d’utiliser deux sessions PuTTY pour vous connecter simultanément aux deux contrôleurs afin de voir quand chacun d’eux est redémarré.

     `Set-CloudPlatform -AzureGovt_US`

    Un message de confirmation s’affiche. Acceptez la valeur par défaut (**Y**).

11. Exécutez l’applet de commande suivante pour reprendre l’installation :

     `Invoke-HcsSetupWizard`

     ![Reprise de l’Assistant Installation](./media/storsimple-configure-and-register-device-gov/HCS_ResumeSetup-gov-include.png)

    Lorsque vous reprenez l’installation, l’Assistant a la version Update 1 (qui correspond à la version 17469).

12. Acceptez les paramètres réseau. Un message de validation apparaît lorsque vous acceptez un paramètre.
 
13. Pour des raisons de sécurité, le mot de passe administrateur de l’appareil expire après la première session, et vous devez le modifier maintenant. Lorsque vous y êtes invité, fournissez un mot de passe administrateur de l’appareil. Un mot de passe administrateur d’appareil valide doit comprendre entre 8 et 15 caractères. Le mot de passe doit contenir trois des éléments suivants : caractères en minuscules, en majuscules, numériques et spéciaux.

	<br/>![Inscription de l’appareil StorSimple 5](./media/storsimple-configure-and-register-device-gov/HCS_RegisterYourDevice5_gov-include.png)

14. La dernière étape de l’Assistant Installation inscrit votre appareil auprès du service StorSimple Manager. Pour cela, vous avez besoin de la clé d’inscription de service que vous avez obtenue à l’[étape 2 : obtention de la clé d’inscription](storsimple-get-service-registration-key-gov.md). Après avoir entré la clé d’inscription, vous devrez peut-être attendre 2 à 3 minutes avant que l’appareil ne soit inscrit.

      >[AZURE.NOTE]Vous pouvez appuyer sur Ctrl + C à tout moment pour quitter l’Assistant Installation. Si vous avez entré tous les paramètres réseau (adresse IP pour Data 0, masque de sous-réseau et passerelle), vos entrées sont conservées.

	![Progression de l’inscription de StorSimple](./media/storsimple-configure-and-register-device-gov/HCS_RegistrationProgress-gov-include.png)

15. Une fois l’appareil inscrit, une clé de chiffrement de données de service s’affiche. Copiez-la et enregistrez-la en lieu sûr. **Cette clé et la clé d’enregistrement de service sont requises pour l’inscription d’appareils supplémentaires avec le service StorSimple Manager.** Reportez-vous à la section [Sécurité StorSimple](../articles/storsimple/storsimple-security.md) pour plus d’informations sur cette clé.
	
	![Inscription de l’appareil StorSimple 7](./media/storsimple-configure-and-register-device-gov/HCS_RegisterYourDevice7_gov-include.png)

      >[AZURE.IMPORTANT]Pour copier le texte à partir de la fenêtre de console de série, sélectionnez-le simplement. Vous devez ensuite pouvoir le coller dans le Presse-papiers ou dans un éditeur de texte.
      > 
      > N’utilisez PAS Ctrl + C pour copier la clé de chiffrement de données de service. Cette combinaison de touches ferme l’Assistant Installation. Par conséquent, le mot de passe administrateur de l’appareil n’est pas modifié et l’appareil rétablit le mot de passe par défaut.

16. Quittez la console en série.

17. Revenez au portail de gestion et procédez comme suit :
  1. Double-cliquez sur le service StorSimple Manager pour accéder à la page **Démarrage rapide**.
  2. Cliquez sur **Afficher les appareils connectés**.
  3. Sur la page **Appareils**, vérifiez que l’appareil s’est bien connecté au service en vérifiant son état. L’état de l’appareil doit être **En ligne**.
   
    	![StorSimple Devices page](./media/storsimple-configure-and-register-device-gov/HCS_DeviceOnline-gov-include.png) 
  
        ISi l’état de l’appareil est **Hors ligne**, attendez quelques minutes qu’il soit en ligne 
      
        Si l'appareil est toujours hors ligne après quelques minutes, vous devez vous assurer que le réseau de pare-feu a été configuré conformément à la [configuration réseau requise pour votre appareil StorSimple] (https://msdn.microsoft.com/library/dn772371.aspx). Si vous ne bénéficiez d'aucune prise en charge HTTP 1.1 , vérifiez le port 9354 afin de vous assurer qu'il est ouvert pour les communications sortantes. Ce port est utilisé pour la communication entre le service StorSimple Manager et votre appareil StorSimple.
     
        

<!---HONumber=58_postMigration-->