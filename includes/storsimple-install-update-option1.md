<!--author=SharS last changed: 9/17/15-->

#### Pour installer Update 1.2 à partir de Windows PowerShell pour StorSimple

1. Procédez comme suit pour télécharger la mise à jour logicielle.

    1. Démarrez Internet Explorer et accédez à [http://catalog.update.microsoft.com/v7/site/Home.aspx](http://catalog.update.microsoft.com/v7/site/Home.aspx).
    2. S’il s’agit de votre première utilisation, vous êtes invité à installer un catalogue Microsoft Update. Cliquez sur **Installer**.
    
        ![Installer le catalogue](./media/storsimple-install-update-option-1/HCS_InstallCatalog-include.png)

    3. Un écran de recherche dans le catalogue s’affiche. Entrez **3063418** dans le champ de recherche, puis cliquez sur **Rechercher**.

        ![Rechercher dans le catalogue](./media/storsimple-install-update-option-1/HCS_SearchCatalog-include.png)

    4. L'offre groupée **StorSimple Update 1.2 Appliance Update** s'affiche. Cliquez sur **Ajouter**. La mise à jour est ajoutée au panier.

        ![Mettre à jour l’offre groupée](./media/storsimple-install-update-option-1/HCS_UpdateBundle-include.png)

    5. Cliquez sur **Afficher le panier**.
 
        ![Afficher le panier](./media/storsimple-install-update-option-1/HCS_InstallBasket-include.png)

    6. Cliquez sur **Télécharger**. Spécifiez ou **accédez** à un emplacement local où vous souhaitez que le téléchargement s'affiche. La mise à jour est téléchargée dans le dossier **StorSimple Update 1.2 Appliance Update** (KB3063418) de l'emplacement choisi. Ce dossier peut également être copié sur un partage réseau accessible à partir de l’appareil.
    
	Cette procédure décrit comment installer la mise à jour de périphérique logicielle en tant que correctif, les mises à jour du microprogramme de disque à partir du serveur Microsoft Update, ainsi que le pilote LSI et les mises à jour Windows à partir du portail de gestion. Toutefois, vous pouvez choisir d'installer les mises à jour du logiciel, du pilote et du microprogramme de disque en tant que correctifs. Vous devrez alors télécharger StorSimple 1.2 SAS Controller Update (KB3043005) et StorSimple 1.2 Disk Firmware Update (KB3063416) et les copier dans le dossier partagé. Pour installer les mises à jour du microprogramme de disque en tant que correctif logiciel, suivez les instructions dans [Installation des correctifs logiciels du mode maintenance via Windows PowerShell pour StorSimple](storsimple-update-device.md#install-hotfixes-via-windows-powershell-for-storsimple).
    
	> [AZURE.NOTE]Le correctif doit être accessible depuis les deux contrôleurs pour détecter les messages d'erreur potentiels à partir du contrôleur homologue.
            
2. Pour installer la mise à jour logicielle, accédez à l’interface Windows PowerShell sur la console série de votre appareil StorSimple. Suivez les instructions détaillées dans [Utiliser PuTTY pour se connecter à la console série](storsimple-deployment-walkthrough.md#use-putty-to-connect-to-the-device-serial-console).

3. À l'invite de commandes, appuyez sur **Entrée**.

4. Sélectionnez **Option 1** pour vous connecter à l’appareil avec un accès complet.

5. Pour installer le package de mise à jour, à l’invite de commandes, tapez :

    `Start-HcsHotfix -Path <path to update file> -Credential <credentials in domain\username format>`

    Utilisez IP au lieu de DNS dans le chemin d'accès du partage dans la commande ci-dessus. Le paramètre Informations d’identification n’est utilisé que si vous accédez à un partage authentifié.

	Nous vous recommandons d'utiliser le paramètre d'informations d'identification pour accéder aux partages. Même les partages qui sont ouverts à « tout le monde » ne sont généralement pas ouverts aux utilisateurs non authentifiés.

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
		

	> [AZURE.NOTE]Parfois, l'applet de commande indique `False` lorsque la mise à jour est encore en cours d'exécution. Pour vous assurer que le correctif logiciel est terminé, patientez quelques minutes, exécutez à nouveau cette commande et vérifiez que `RunInProgress` est `False`. Dans ce cas, le correctif est terminé.
	
8. Lorsque la mise à jour logicielle est terminée, vérifiez les versions des logiciels du système. Tapez la commande suivante :

    `Get-HcsSystem`

    Vous devez voir les versions suivantes :

    - HcsSoftwareVersion : 6.3.9600.17584
    - CisAgentVersion : 1.0.9049.0
    - MdsAgentVersion : 26.0.4696.1433 
    
	Si les numéros de version ne changent pas après la mise à jour, cela indique que le correctif n'a pas pu s'appliquer. Dans ce cas, contactez le [Support Microsoft](storsimple-contact-microsoft-support.md) pour obtenir une assistance supplémentaire.
    
9. Vous allez maintenant installer les mises à jour du microprogramme de disque qui entraînent des temps d'arrêt et prennent environ 30 à 45 minutes. Vous pouvez choisir de les installer dans une fenêtre de maintenance planifiée en vous connectant à la console série du périphérique. Pour installer les mises à jour du microprogramme de disque, suivez les instructions sous [Installation des mises à jour en mode Maintenance via Windows PowerShell pour StorSimple](storsimple-update-device.md#install-maintenance-mode-updates-via-windows-powershell-for-storsimple).

10. Une fois que les mises à jour du microprogramme de disque ont été appliquées avec succès et que le périphérique a quitté le mode Maintenance, revenez au portail de gestion. Les mises à jour du mode Maintenance ne sont mises à jour sur le portail que 24 heures plus tard. Vous devrez peut-être patienter avant d'appliquer les autres mises à jour sans interruption depuis le portail de gestion.

11. Lorsque vous êtes prêt à appliquer les mises à jour, accédez à la page **Maintenance** et cliquez sur **Rechercher les mises à jour** en bas de la page. Vous serez averti que des mises à jour sont disponibles, notamment les mises à jour du pilote et les mises à jour Windows. Cliquez sur **Installer les mises à jour** pour commencer le processus d'installation. Vous avez terminé une fois que toutes les mises à jour sont installées correctement.





 
 

<!---HONumber=Oct15_HO3-->