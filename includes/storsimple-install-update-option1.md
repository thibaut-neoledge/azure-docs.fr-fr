<!--author=SharS last changed: 03/17/2016-->

#### Pour télécharger des correctifs logiciels

Procédez comme suit pour télécharger la mise à jour logicielle.

1. Démarrez Internet Explorer et accédez à [http://catalog.update.microsoft.com](http://catalog.update.microsoft.com).

2. Si vous utilisez le catalogue Microsoft Update pour la première fois sur cet ordinateur, cliquez sur **Installer** lorsque vous êtes invité à installer le module complémentaire Catalogue Microsoft Update. ![Installer le catalogue](./media/storsimple-install-update-option-1/HCS_InstallCatalog-include.png)

3. Dans la zone de recherche du Catalogue Microsoft Update, entrez le numéro KB (Base de connaissances) du correctif que vous souhaitez télécharger, par exemple **3063418**, puis cliquez sur **Rechercher**.

4. L'offre groupée **StorSimple Update 1.2 Appliance Update** s'affiche. Cliquez sur **Ajouter**. La mise à jour est ajoutée au panier.

5. Recherchez les correctifs supplémentaires répertoriés dans le tableau ci-dessus (**3043005** et **3063416**) et ajoutez-les au panier.

5. Cliquez sur **Afficher le panier**.

    ![Afficher le panier](./media/storsimple-install-update-option-1/HCS_InstallBasket-include.png)

6. Cliquez sur **Télécharger**. Spécifiez ou **accédez** à un emplacement local où vous souhaitez que les téléchargements s’affichent. Les mises à jour sont téléchargées à l’emplacement spécifié et placées dans un sous-dossier portant le même nom que la mise à jour. Ce dossier peut également être copié sur un partage réseau accessible à partir de l’appareil.

>   [AZURE.NOTE]
Les correctifs doivent être accessibles depuis les deux contrôleurs pour détecter les messages d’erreur potentiels à partir du contrôleur homologue.

#### Pour installer et vérifier les correctifs logiciels en mode Normal
Procédez comme suit pour installer et vérifier les correctifs logiciels en mode Normal. Si vous les avez déjà installés à l’aide du portail Azure, passez directement à [Installer et vérifier les correctifs en mode maintenance](#to-install-and-verify-maintenance-mode-hotfixes).

1. Pour installer la mise à jour logicielle, accédez à l’interface Windows PowerShell sur la console série de votre appareil StorSimple. Suivez les instructions détaillées dans [Utiliser PuTTY pour se connecter à la console série](storsimple-deployment-walkthrough.md#use-putty-to-connect-to-the-device-serial-console). À l'invite de commandes, appuyez sur **Entrée**.

4. Sélectionnez **Option 1** pour vous connecter à l’appareil avec un accès complet.

5. Pour installer le package de mise à jour, à l’invite de commandes, tapez :

    `Start-HcsHotfix -Path <path to update file> -Credential <credentials in domain\username format>`

    Utilisez IP au lieu de DNS dans le chemin d'accès du partage dans la commande ci-dessus. Le paramètre Informations d’identification n’est utilisé que si vous accédez à un partage authentifié.

	Nous vous recommandons d'utiliser le paramètre d'informations d'identification pour accéder aux partages. Même les partages qui sont ouverts à « tout le monde » ne sont généralement pas ouverts aux utilisateurs non authentifiés.

    Voici un exemple de sortie obtenue.

        ````
        Controller0>Start-HcsHotfix -Path \\10.100.100.100\share
        \hcsmdssoftwareupdate.exe -Credential contoso\John

        Confirm

        This operation starts the hotfix installation and could reboot one or
        both of the controllers. If the device is serving I/Os, these will not
        be disrupted. Are you sure you want to continue?
        [Y] Yes [N] No [?] Help (default is "Y"): Y

        ````

6. Tapez **Y** lorsque vous êtes invité à confirmer l’installation du correctif.

7. Contrôlez la mise à jour à l'aide de l'applet de commande `Get-HcsUpdateStatus`.

    L’exemple de sortie suivant indique que la mise à jour est en cours. `RunInprogress` a la valeur `True` lorsque la mise à jour est en cours.

        ````
        Controller0>Get-HcsUpdateStatus
        RunInprogress       : True
        LastHotfixTimestamp : 9/02/2015 10:36:13 PM
        LastUpdateTimestamp : 9/02/2015 10:35:25 PM
        Controller0Events   :
        Controller1Events   :
        ````

     L’exemple de sortie suivant indique que la mise à jour est terminée. `RunInProgress` a la valeur `False` lorsque la mise à jour est terminée.

        ````
        Controller1>Get-HcsUpdateStatus

        RunInprogress       : False
        LastHotfixTimestamp : 9/02/2015 10:56:13 PM
        LastUpdateTimestamp : 9/02/2015 10:35:25 PM
        Controller0Events   :
        Controller1Events   :

        ````

	> [AZURE.NOTE] Parfois, l'applet de commande indique `False` lorsque la mise à jour est encore en cours d'exécution. Pour vous assurer que le correctif logiciel est terminé, patientez quelques minutes, exécutez à nouveau cette commande et vérifiez que `RunInProgress` est `False`. Dans ce cas, le correctif est terminé.

8. Lorsque la mise à jour logicielle est terminée, vérifiez les versions des logiciels du système. Tapez la commande suivante :

    `Get-HcsSystem`

    Vous devez voir les versions suivantes :

    - HcsSoftwareVersion : 6.3.9600.17584
    - CisAgentVersion : 1.0.9049.0
    - MdsAgentVersion : 26.0.4696.1433

	Si les numéros de version ne changent pas après la mise à jour, cela indique que le correctif n'a pas pu s'appliquer. Dans ce cas, contactez le [Support Microsoft](storsimple-contact-microsoft-support.md) pour obtenir une assistance supplémentaire.

9. Répétez les étapes 3 à 5 pour installer le correctif logiciel restant en mode Normal (KB3043005).

#### Pour installer et vérifier les correctifs logiciels en mode Maintenance

Utilisez l’article KB3063416 pour installer les mises à jour du microprogramme de disque. Ces mises à jour, qui entraînent des perturbations, nécessitent environ 30 à 45 minutes. Vous pouvez choisir de les installer dans une fenêtre de maintenance planifiée en vous connectant à la console série du périphérique.

Pour installer les mises à jour du microprogramme de disque, suivez les instructions ci-dessous.

1. Mettez l’appareil en mode Maintenance. Notez que vous ne devez pas utiliser l’accès distant Windows PowerShell quand vous vous connectez à un appareil en mode Maintenance. Vous devez exécuter cette applet de commande sur le contrôleur de l’appareil si vous êtes connecté par le biais de la console série de l’appareil. Entrez :

    `Enter-HcsMaintenanceMode`

	Voici un exemple de sortie obtenue.

		Controller0>Enter-HcsMaintenanceMode
		Checking device state...

		In maintenance mode, your device will not service IOs and will be disconnected from the Microsoft Azure StorSimple Manager service. Entering maintenance mode will end the current session and reboot both controllers, which takes a few minutes to complete. Are you sure you want to enter maintenance mode?
		[Y] Yes [N] No (Default is "Y"): Y

		-----------------------MAINTENANCE MODE------------------------
		Microsoft Azure StorSimple Appliance Model 8100
		Name: Update1-8100-SHG0997879L76YD
		Software Version: 6.3.9600.17584
		Copyright (C) 2014 Microsoft Corporation. All rights reserved.
		You are connected to Controller0 - Passive
		---------------------------------------------------------------
		Serial Console Menu
		[1] Log in with full access
		[2] Log into peer controller with full access
		[3] Connect with limited access
		[4] Change language
		Please enter your choice>

	Les deux contrôleurs redémarrent alors en mode Maintenance.

3. Pour installer la mise à jour du microprogramme de disque, tapez :

    `Start-HcsHotfix -Path <path to update file> -Credential <credentials in domain\username format>`

    Voici un exemple de sortie obtenue.

        Controller1>Start-HcsHotfix -Path \\10.100.100.100\share\DiskFirmwarePackage.exe -Credential contoso\john
    	Enter Password:
    	WARNING: In maintenance mode, hotfixes should be installed on each controller sequentially. After the hotfix is installed on this controller, install it on the peer controller.
    	Confirm
    	This operation starts a hotfix installation and could reboot one or both of the controllers. Are you sure you want to continue?
    	[Y] Yes [N] No (Default is "Y"): Y
    	WARNING: Installation is currently in progress. This operation can take several minutes to complete.

1.  Surveillez la progression de l’installation à l’aide de la commande `Get-HcsUpdateStatus`. La mise à jour est terminée quand `RunInProgress` passe à `False`.

2.  Une fois l’installation terminée, le contrôleur sur lequel le correctif logiciel en mode Maintenance a été installé est redémarré. Connectez-vous avec l’option 1 (accès total) et vérifiez la version du microprogramme de disque. Entrez :

	`Get-HcsFirmwareVersion`

    Les versions attendues du microprogramme de disque sont les suivantes :

    `XMGG, XGEE, KZ50, F6C2, VR08`

    Exécutez la commande `Get-HcsFirmwareVersion` sur le deuxième contrôleur pour vérifier que la version du logiciel a été mise à jour. Vous pouvez à présent quitter le mode Maintenance. Tapez la commande suivante pour chaque contrôleur d’appareil :

    `Exit-HcsMaintenanceMode`

1. Les contrôleurs redémarrent quand vous quittez le mode Maintenance. Une fois que les mises à jour du microprogramme de disque ont été appliquées avec succès et que l’appareil a quitté le mode Maintenance, revenez au portail Azure Classic. Remarque : il se peut que le portail n’affiche pas les mises à jour installées en mode Maintenance pendant 24 heures.

<!---HONumber=AcomDC_0323_2016-->