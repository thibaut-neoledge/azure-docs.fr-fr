<!--author=alkohli last changed: 01/26/16-->

#### Pour télécharger des correctifs logiciels

Procédez comme suit pour télécharger la mise à jour logicielle à partir du Catalogue Microsoft Update.

1. Démarrez Internet Explorer et accédez à [http://catalog.update.microsoft.com/v7/site/Home.aspx](http://catalog.update.microsoft.com/v7/site/Home.aspx).

2. S’il s’agit de votre première utilisation, vous êtes invité à installer un catalogue Microsoft Update. Cliquez sur **Installer**.
    
   	![Installer le catalogue](./media/storsimple-install-update2-hotfix/HCS_InstallCatalog-include.png)

3. Un écran de recherche dans le catalogue s’affiche. Entrez **3121901** dans le champ de recherche, puis cliquez sur **Rechercher**.

    ![Rechercher dans le catalogue](./media/storsimple-install-update2-hotfix/HCS_SearchCatalog1-include.png)

4. La mise à jour **Cumulative Software Bundle Update 2.0 for StorSimple 8000 Series** est répertoriée. Cliquez sur **Ajouter**. La mise à jour est ajoutée au panier.

5. Cliquez sur **Afficher le panier**.
 
6. Cliquez sur **Télécharger**. Spécifiez ou **accédez** à un emplacement local où vous souhaitez que le téléchargement s'affiche. La mise à jour est téléchargée dans un dossier (portant le même nom que la mise à jour) à l’emplacement choisi. Ce dossier peut également être copié sur un partage réseau accessible à partir de l’appareil.
       
	> [AZURE.NOTE] 
	> 
	> - Vous devez également télécharger la **mise à jour du pilote LSI** (SAS Controller Update 2.0 pour la gamme StorSimple 8000 - KB3121900), la **mise à jour Storport** (correctif logiciel pour Windows Server 2012 R2 x64 Edition - KB3080728), la **mise à jour Spaceport** (correctif logiciel pour Windows Server 2012 R2 x64 Edition - KB3090322) et la **mise à jour du microprogramme de disque** (Cumulative Disk Firmware Update 2.0 pour la gamme StorSimple 8000 - KB3121899), et les copier dans le même de dossier partagé.
	> - Le correctif doit être accessible depuis les deux contrôleurs pour détecter les messages d'erreur potentiels à partir du contrôleur homologue.

#### Pour installer et vérifier les correctifs logiciels en mode Normal

Procédez comme suit pour installer et vérifier les correctifs logiciels en mode Normal.

1. Pour installer les correctifs logiciels, accédez à l’interface Windows PowerShell sur la console série de votre appareil StorSimple. Suivez les instructions détaillées dans [Utiliser PuTTY pour se connecter à la console série](storsimple-deployment-walkthrough.md#use-putty-to-connect-to-the-device-serial-console). À l'invite de commandes, appuyez sur **Entrée**.

4. Sélectionnez **Option 1** pour vous connecter à l’appareil avec un accès complet.

5. Pour installer le correctif logiciel, tapez ce qui suit à l’invite de commandes :

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
        LastHotfixTimestamp : 12/21/2015 10:36:13 PM
        LastUpdateTimestamp : 12/21/2015 10:35:25 PM
        Controller0Events   :
        Controller1Events   : 
        ````
 
     L’exemple de sortie suivant indique que la mise à jour est terminée. `RunInProgress` a la valeur `False` lorsque la mise à jour est terminée.

        ````
        Controller1>Get-HcsUpdateStatus

        RunInprogress       : False
        LastHotfixTimestamp : 12/21/2015 10:59:13 PM
        LastUpdateTimestamp : 12/21/2015 10:35:25 PM
        Controller0Events   :
        Controller1Events   :

        ````
		

	> [AZURE.NOTE] Parfois, l'applet de commande indique `False` lorsque la mise à jour est encore en cours d'exécution. Pour vous assurer que le correctif logiciel est terminé, patientez quelques minutes, exécutez à nouveau cette commande et vérifiez que `RunInProgress` est `False`. Dans ce cas, le correctif logiciel est terminé.
	
8. Une fois la mise à jour logicielle terminée, répétez les étapes 3 à 5 pour installer et surveiller les agents SaaS et MDS à l’aide de `CisMdsAgentUpdateBundle.exe`. Veillez à installer `HcsMdsSoftwareUpdate.exe` avant `CisMdsAgentUpdateBundle.exe`.

9. Vérifiez les versions des logiciels système. Entrez :

    `Get-HcsSystem`

    Vous devez voir les versions suivantes :

    - HcsSoftwareVersion : 6.3.9600.17673
    - CisAgentVersion : 1.0.9150.0
    - MdsAgentVersion : 30.0.4698.13 
    
	Si les numéros de version ne changent pas après la mise à jour, cela indique que le correctif n'a pas pu s'appliquer. Dans ce cas, contactez le [Support Microsoft](storsimple-contact-microsoft-support.md) pour obtenir une assistance supplémentaire.
    
9. Répétez les étapes 3 à 5 pour installer et surveiller les correctifs logiciels restants en mode Normal :

	- le pilote LSI à l’aide du package `HcsLsiUpdate.exe` (KB3121900) ;
	- le correctif Storport à l’aide du package `Storport-KB3080728-x64.msu` (KB3080728) ;
	- le correctif Spaceport à l’aide du package `spaceport-KB3090322-x64.msu` (KB3090322).

#### Pour installer et vérifier le correctif logiciel en mode Maintenance

Utilisez le package `DiskFirmwarePackage.exe` (KB3121899) pour installer les mises à jour du microprogramme de disque. Ces mises à jour, qui entraînent des perturbations, nécessitent environ 30 minutes. Vous pouvez choisir de les installer dans une fenêtre de maintenance planifiée en vous connectant à la console série du périphérique.

Notez que si votre microprogramme de disque est déjà à jour, vous n’aurez pas à installer ces mises à jour. Exécuter l’applet de commande `Get-HcsUpdateAvailability` à partir de la console série de l’appareil. Le système vous indique si des mises à jour sont disponibles et si elles risquent de provoquer une interruption de service (mises en jour en mode maintenance) ou non (périodiques).
 
Pour installer les mises à jour du microprogramme de disque, suivez les instructions ci-dessous.

1. Mettez l’appareil en mode Maintenance. Notez que vous ne devez pas utiliser l’accès distant Windows PowerShell quand vous vous connectez à un appareil en mode Maintenance. Vous devez exécuter cette applet de commande sur le contrôleur de l’appareil si vous êtes connecté par le biais de la console série de l’appareil. Entrez :
		
	`Enter-HcsMaintenanceMode`

	Voici un exemple de sortie obtenue.

		Controller0>Enter-HcsMaintenanceMode
		Checking device state...

		In maintenance mode, your device will not service IOs and will be disconnected from the Microsoft Azure StorSimple Manager service. Entering maintenance mode will end the current session and reboot both controllers, which takes a few minutes to complete. Are you sure you want to enter maintenance mode?
		[Y] Yes [N] No (Default is "Y"): Y

		-----------------------MAINTENANCE MODE------------------------
		Microsoft Azure StorSimple Appliance Model 8100
		Name: Update2-8100-SHG0997879L76YD
		Software Version: 6.3.9600.17664
		Copyright (C) 2014 Microsoft Corporation. All rights reserved.
		You are connected to Controller0 - Passive
		---------------------------------------------------------------

		Serial Console Menu
		[1] Log in with full access
		[2] Log into peer controller with full access
		[3] Connect with limited access
		[4] Change language
		Please enter your choice>

	Les deux contrôleurs sont redémarrés. Une fois le redémarrage terminé, les deux contrôleurs sont en mode Maintenance.

3. Pour installer la mise à jour du microprogramme de disque, tapez :

	`Start-HcsHotfix -Path <path to update file> -Credential <credentials in domain\username format>`

	Voici un exemple de sortie obtenue.

        Controller1>Start-HcsHotfix -Path \\10.100.100.100\share\DiskFirmwarePackage.exe -Credential contoso\john
		Enter Password:
		WARNING: In maintenance mode, hotfixes should be installed on each controller sequentially. After the hotfix is installed on this controller, install it on the peer controller.
		Confirm
		This operation starts a hotfix installation and could reboot one or both of the controllers. By installing new updates you agree to, and accept any additional terms associated with, the new functionality listed in the release notes (https://go.microsoft.com/fwLink/?LinkID=613790). Are you sure you want to continue?
		[Y] Yes [N] No (Default is "Y"): Y
		WARNING: Installation is currently in progress. This operation can take several minutes to complete.
	

1.  Surveillez la progression de l’installation à l’aide de la commande `Get-HcsUpdateStatus`. La mise à jour est terminée quand `RunInProgress` passe à `False`.
 
2.  Une fois l’installation terminée, le contrôleur sur lequel le correctif logiciel en mode Maintenance a été installé est redémarré. Connectez-vous avec l’option 1 (accès total) et vérifiez la version du microprogramme de disque. Entrez :
	
	`Get-HcsFirmwareVersion`
  
	Les versions attendues du microprogramme de disque sont les suivantes :

	`XMGG, XGEG, KZ50, F6C2, VR08`

	Voici un exemple de sortie obtenue.


        -----------------------MAINTENANCE MODE------------------------
    	Microsoft Azure StorSimple Appliance Model 8100
    	Name: Update2-8100-SHG0997879L76YD
    	Software Version: 6.3.9600.17664
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

	 Exécutez la commande `Get-HcsFirmwareVersion` sur le deuxième contrôleur pour vérifier que la version du logiciel a été mise à jour. Vous pouvez à présent quitter le mode Maintenance. Tapez la commande suivante pour chaque contrôleur d’appareil :

    `Exit-HcsMaintenanceMode`
     
1. Les contrôleurs redémarrent quand vous quittez le mode Maintenance. Une fois que les mises à jour du microprogramme de disque ont été appliquées avec succès et que l’appareil a quitté le mode Maintenance, revenez au portail Azure Classic. Les mises à jour du mode Maintenance ne sont mises à jour sur le portail que 24 heures plus tard.






 
 

<!---HONumber=AcomDC_0128_2016-->