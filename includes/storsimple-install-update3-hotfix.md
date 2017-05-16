<!--author=alkohli last changed: 09/01/16-->

#### <a name="to-download-hotfixes"></a>Pour télécharger des correctifs logiciels
Procédez comme suit pour télécharger la mise à jour logicielle à partir du Catalogue Microsoft Update.

1. Démarrez Internet Explorer et accédez à [http://catalog.update.microsoft.com](http://catalog.update.microsoft.com).
2. Si vous utilisez le catalogue Microsoft Update pour la première fois sur cet ordinateur, cliquez sur **Installer** lorsque vous êtes invité à installer le module complémentaire Catalogue Microsoft Update.
    ![Installer le catalogue](./media/storsimple-install-update2-hotfix/HCS_InstallCatalog-include.png)
3. Dans la zone de recherche du Catalogue Microsoft Update, entrez le numéro KB (Base de connaissances) du correctif logiciel que vous souhaitez télécharger, par exemple **3186843**, puis cliquez sur **Rechercher**.
   
    La liste des correctifs s’affiche, par exemple **Ensemble de logiciels Update 3.0 pour StorSimple série 8000**.
   
    ![Rechercher dans le catalogue](./media/storsimple-install-update2-hotfix/HCS_SearchCatalog1-include.png)
4. Cliquez sur **Add**. La mise à jour est ajoutée au panier.
5. Recherchez les correctifs supplémentaires répertoriés dans le tableau ci-dessus (**3186859**) et ajoutez-les au panier.
6. Cliquez sur **Afficher le panier**.
7. Cliquez sur **Télécharger**. Spécifiez ou **recherchez** l'emplacement local où vous voulez effectuer les téléchargements. Les mises à jour sont téléchargées à l’emplacement spécifié et placées dans un sous-dossier portant le même nom que la mise à jour. Ce dossier peut également être copié sur un partage réseau accessible à partir de l’appareil.

> [!NOTE]
> Les correctifs doivent être accessibles depuis les deux contrôleurs pour détecter les messages d’erreur potentiels à partir du contrôleur homologue.
> 
> 

#### <a name="to-install-and-verify-regular-mode-hotfixes"></a>Pour installer et vérifier les correctifs logiciels en mode Normal
Procédez comme suit pour installer et vérifier les correctifs logiciels en mode Normal. Si vous les avez déjà installés à l’aide du portail Azure, passez directement à [Installer et vérifier les correctifs en mode maintenance](#to-install-and-verify-maintenance-mode-hotfixes).

1. Pour installer les correctifs logiciels, accédez à l’interface Windows PowerShell sur la console série de votre appareil StorSimple. Suivez les instructions détaillées de la section [Utilisation de PuTTY pour se connecter à la console série de l’appareil](../articles/storsimple/storsimple-deployment-walkthrough.md#use-putty-to-connect-to-the-device-serial-console). À l'invite de commandes, appuyez sur **Entrée**.
2. Sélectionnez **Option 1** pour vous connecter à l’appareil avec un accès complet. Nous vous recommandons d’installer le correctif d’abord sur le contrôleur passif.
3. Pour installer le correctif logiciel, tapez ce qui suit à l’invite de commandes :
   
    `Start-HcsHotfix -Path <path to update file> -Credential <credentials in domain\username format>`
   
    Utilisez IP au lieu de DNS dans le chemin d'accès du partage dans la commande ci-dessus. Le paramètre Informations d’identification n’est utilisé que si vous accédez à un partage authentifié.
   
    Nous vous recommandons d'utiliser le paramètre d'informations d'identification pour accéder aux partages. Même les partages qui sont ouverts à « tout le monde » ne sont généralement pas ouverts aux utilisateurs non authentifiés.
   
    Indiquez le mot de passe lorsque vous y êtes invité.
   
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
4. Tapez **Y** lorsque vous êtes invité à confirmer l’installation du correctif.
5. Contrôlez la mise à jour à l'aide de l'applet de commande `Get-HcsUpdateStatus` . La mise à jour se termine d’abord sur le contrôleur passif. Une fois le contrôleur passif mis à jour, un basculement se produit et la mise à jour est ensuite appliquée à l’autre contrôleur. La mise à jour est terminée lorsque les deux contrôleurs sont mis à jour.
   
    L’exemple de sortie suivant indique que la mise à jour est en cours. `RunInprogress` a la valeur `True` lorsque la mise à jour est en cours.

    ```
    Controller0>Get-HcsUpdateStatus
    RunInprogress       : True
    LastHotfixTimestamp :
    LastUpdateTimestamp : 8/29/2016 2:04:02 AM
    Controller0Events   :
    Controller1Events   :
    ```
   
     L’exemple de sortie suivant indique que la mise à jour est terminée. `RunInProgress` a la valeur `False` lorsque la mise à jour est terminée.
   
    ```
    Controller0>Get-HcsUpdateStatus
    RunInprogress       : False
    LastHotfixTimestamp : 8/30/2016 9:15:55 AM
    LastUpdateTimestamp : 8/30/2016 9:06:07 AM
    Controller0Events   :
    Controller1Events   :
    ```

    > [!NOTE] 
    > Parfois, l'applet de commande indique `False` lorsque la mise à jour est encore en cours d'exécution. Pour vous assurer que le correctif logiciel est terminé, patientez quelques minutes, exécutez à nouveau cette commande et vérifiez que `RunInProgress` est `False`. Dans ce cas, le correctif est terminé.

1. Lorsque la mise à jour logicielle est terminée, vérifiez les versions des logiciels du système. Entrez :
   
    `Get-HcsSystem`
   
    Vous devez voir les versions suivantes :
   
   * `HcsSoftwareVersion: 6.3.9600.17759`
   * `CisAgentVersion:  1.0.9343.0`
   * `MdsAgentVersion: 30.0.4698.16`
     
     Si les numéros de version ne changent pas après la mise à jour, cela indique que le correctif n'a pas pu s'appliquer. Dans ce cas, contactez le [Support Microsoft](../articles/storsimple/storsimple-contact-microsoft-support.md) pour obtenir une assistance supplémentaire.
     
     > [!IMPORTANT]
     > Vous devez redémarrer le contrôleur actif via l’applet de commande `Restart-HcsController` avant d’appliquer les autres mises à jour. 
     > 
     > 
2. Répétez les étapes 3 à 5 pour installer le correctif du microprogramme et du pilote LSI **KB3186859**. Une fois le correctif installé, utilisez l’applet de commande `Get-HcsSystem` . La version LSI doit être :
   
   * `Lsisas2Version: 2.0.78.00`
3. Répétez les étapes 3 à 5 pour installer la mise à jour Storport et Spaceport **KB3121261**.
4. Si vous effectuez la mise à jour à partir d’Update 2 ou d’une version antérieure, vous devrez également télécharger :
   
   * Correctif iSCSI avec KB3146621
   * Correctif WMI avec KB3103616

#### <a name="to-install-and-verify-maintenance-mode-hotfixes"></a>Pour installer et vérifier les correctifs logiciels en mode Maintenance
Utilisez l’article KB3121899 pour installer les mises à jour du microprogramme de disque. Ces mises à jour, qui entraînent des perturbations, nécessitent environ 30 minutes. Vous pouvez choisir de les installer dans une fenêtre de maintenance planifiée en vous connectant à la console série du périphérique.

Notez que si votre microprogramme de disque est déjà à jour, vous n’aurez pas à installer ces mises à jour. Exécutez l’applet de commande `Get-HcsUpdateAvailability` à partir de la console série de l’appareil pour vérifier si des mises à jour sont disponibles et si elles risquent de provoquer une interruption de service (mode maintenance) ou non (mode normal).

Pour installer les mises à jour du microprogramme de disque, suivez les instructions ci-dessous.

1. Mettez l’appareil en mode Maintenance. Notez que vous ne devez pas utiliser l’accès distant Windows PowerShell quand vous vous connectez à un appareil en mode Maintenance. À la place, exécutez cette applet de commande sur le contrôleur de l’appareil si vous êtes connecté par le biais de la console série de l’appareil. Entrez :
   
    `Enter-HcsMaintenanceMode`
   
    Voici un exemple de sortie obtenue.
   
        Controller0>Enter-HcsMaintenanceMode
        Checking device state...
   
        In maintenance mode, your device will not service IOs and will be disconnected from the Microsoft Azure StorSimple Manager service. Entering maintenance mode will end the current session and reboot both controllers, which takes a few minutes to complete. Are you sure you want to enter maintenance mode?
        [Y] Yes [N] No (Default is "Y"): Y
   
        -----------------------MAINTENANCE MODE------------------------
        Microsoft Azure StorSimple Appliance Model 8100
        Name: Update2-8100-SHG0997879L76673
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
2. Pour installer la mise à jour du microprogramme de disque, tapez :
   
    `Start-HcsHotfix -Path <path to update file> -Credential <credentials in domain\username format>`
   
    Voici un exemple de sortie obtenue.
   
        Controller1>Start-HcsHotfix -Path \\10.100.100.100\share\DiskFirmwarePackage.exe -Credential contoso\john
        Enter Password:
        WARNING: In maintenance mode, hotfixes should be installed on each controller sequentially. After the hotfix is installed on this controller, install it on the peer controller.
        Confirm
        This operation starts a hotfix installation and could reboot one or both of the controllers. By installing new updates you agree to, and accept any additional terms associated with, the new functionality listed in the release notes (https://go.microsoft.com/fwLink/?LinkID=613790). Are you sure you want to continue?
        [Y] Yes [N] No (Default is "Y"): Y
        WARNING: Installation is currently in progress. This operation can take several minutes to complete.
3. Surveillez la progression de l’installation à l’aide de la commande `Get-HcsUpdateStatus` . La mise à jour est terminée quand `RunInProgress` passe à `False`.
4. Une fois l’installation terminée, le contrôleur sur lequel le correctif logiciel en mode Maintenance a été installé redémarre. Connectez-vous avec l’option 1 (accès total) et vérifiez la version du microprogramme de disque. Entrez :
   
   `Get-HcsFirmwareVersion`
   
   Les versions attendues du microprogramme de disque sont les suivantes :
   
   `XMGG, XGEG, KZ50, F6C2, VR08`
   
   Voici un exemple de sortie obtenue.
   
       -----------------------MAINTENANCE MODE------------------------
       Microsoft Azure StorSimple Appliance Model 8100
       Name: Update2-8100-SHG0997879L76YD
       Software Version: 6.3.9600.17705
       Copyright (C) 2014 Microsoft Corporation. All rights reserved.
       You are connected to Controller1
       ---------------------------------------------------------------
   
       Controller1>Get-HcsFirmwareVersion
   
       Controller0 : TalladegaFirmware
         ActiveBIOS:0.45.0006
         BackupBIOS:0.45.0008
         MainCPLD:17.0.0005
         ActiveBMCRoot:2.0.000E
         BackupBMCRoot:2.0.000E
         BMCBoot:2.0.0001
         LsiFirmware:19.00.00.00
         LsiBios:07.37.00.00
         Battery1Firmware:06.29
         Battery2Firmware:06.29
         DomFirmware:X231600
         CanisterFirmware:3.5.0.32
         CanisterBootloader:5.03
         CanisterConfigCRC:0xD1B030A4
         CanisterVPDStructure:0x06
         CanisterGEMCPLD:0x17
         CanisterVPDCRC:0xEE3504B4
         MidplaneVPDStructure:0x0C
         MidplaneVPDCRC:0xA6BD4F64
         MidplaneCPLD:0x10
         PCM1Firmware:1.00|1.05
         PCM1VPDStructure:0x05
         PCM1VPDCRC:0x41BEF99C
         PCM2Firmware:1.00|1.05
         PCM2VPDStructure:0x05
         PCM2VPDCRC:0x41BEF99C
   
         DisksFirmware
         SEAGATE:ST400FM0073:XGEG
         SEAGATE:ST400FM0073:XGEG
         SEAGATE:ST400FM0073:XGEG
         SEAGATE:ST400FM0073:XGEG
         SEAGATE:ST4000NM0023:XMGG
         SEAGATE:ST4000NM0023:XMGG
         SEAGATE:ST4000NM0023:XMGG
         SEAGATE:ST4000NM0023:XMGG
         SEAGATE:ST4000NM0023:XMGG
         SEAGATE:ST4000NM0023:XMGG
         SEAGATE:ST4000NM0023:XMGG
         SEAGATE:ST4000NM0023:XMGG
   
    Exécutez la commande `Get-HcsFirmwareVersion` sur le deuxième contrôleur pour vérifier que la version du logiciel a été mise à jour. Vous pouvez à présent quitter le mode Maintenance. Pour ce faire, tapez la commande suivante pour chaque contrôleur d’appareil :
   
   `Exit-HcsMaintenanceMode`
5. Les contrôleurs redémarrent quand vous quittez le mode Maintenance. Une fois que les mises à jour du microprogramme de disque ont été appliquées avec succès et que l’appareil a quitté le mode Maintenance, revenez au portail Azure Classic. Remarque : il se peut que le portail n’affiche pas les mises à jour installées en mode Maintenance pendant 24 heures.

