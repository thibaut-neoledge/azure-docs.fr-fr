
#### Pour installer Update 1 à partir de Windows PowerShell pour StorSimple

1. Procédez comme suit pour télécharger la mise à jour logicielle.

    1. Démarrez Internet Explorer et accédez à [http://catalog.update.microsoft.com/v7/site/Home.aspx](http://catalog.update.microsoft.com/v7/site/Home.aspx).
    2. S’il s’agit de votre première utilisation, vous êtes invité à installer un catalogue Microsoft Update. Cliquez sur **Installer**.
    
        ![Installer le catalogue](./media/storsimple-install-update-option-1/HCS_InstallCatalog-include.png)

    3. Un écran de recherche dans le catalogue s’affiche. Entrez **3063418** dans le champ de recherche, puis cliquez sur **Rechercher**.

        ![Rechercher dans le catalogue](./media/storsimple-install-update-option-1/HCS_SearchCatalog-include.png)

    4. L’offre groupée **StorSimple Update 1.0 Appliance Update** s’affiche. Cliquez sur **Ajouter**. La mise à jour est ajoutée au panier.

        ![Mettre à jour l’offre groupée](./media/storsimple-install-update-option-1/HCS_UpdateBundle-include.png)

    5. Cliquez sur **Afficher le panier**.
 
        ![Afficher le panier](./media/storsimple-install-update-option-1/HCS_InstallBasket-include.png)

    6. Cliquez sur **Télécharger**. Spécifiez ou accédez à un emplacement local où vous souhaitez que le téléchargement s’affiche. La mise à jour (all-hcsmdssoftwareupdate\_288da2cc8cd2e3c3958b603a79346cb586fb8fe3.exe) est téléchargée dans le dossier StorSimple Update 1.0 Appliance Update (KB3063418) de l’emplacement choisi. Ce dossier peut également être copié sur un partage réseau accessible à partir de l’appareil.
        
2. Pour installer la mise à jour logicielle, accédez à l’interface Windows PowerShell sur la console série de votre appareil StorSimple. Suivez les instructions détaillées dans [Utiliser PuTTY pour se connecter à la console série](#use-putty-to-connect-to-the-serial-console).

3. À l’invite de commandes, appuyez sur Entrée.

4. Sélectionnez **Option 1** pour vous connecter à l’appareil avec un accès complet.

5. Pour installer le package de mise à jour, à l’invite de commandes, tapez :

    `Start-HcsHotfix -Path <path to update file> -Credential <credentials in domain\username format>`

    Le paramètre Informations d’identification n’est utilisé que si vous accédez à un partage authentifié.

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

7. Surveillez la mise à jour à l’aide de l’applet de commande Get-HcsUpdateStatus.

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

        ````
 
8. Une fois la mise à jour logicielle terminée, accédez à la page Maintenance du portail de gestion. Recherchez les mises à jour disponibles. Vous devez voir d’autres mises à jour logicielles disponibles.

9. Cliquez sur **Installer les mises à jour** pour appliquer toutes les mises à jour logicielles disponibles à partir du portail.

10. Une fois les mises à jour logicielles terminées, vérifiez les versions des logiciels, des pilotes et du microprogramme du système. Tapez la commande suivante :

    `Get-HcsSystem`

    Vous devez voir les versions suivantes :

    - HcsSoftwareVersion : 6.3.9600.17491
    - CisAgentVersion : 1.0.9037.0
    - MdsAgentVersion : 26.0.4696.1433 
 
11. Pour vérifier que le microprogramme a été correctement mis à jour, tapez :

    `Start-HcsFirmwareCheck`

    L’état du microprogramme doit être **À jour**.

<!---HONumber=August15_HO6-->