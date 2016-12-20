<!--author=SharS last changed: 02/22/16-->

### <a name="to-configure-and-register-the-device"></a>Configuration et inscription de l’appareil
1. Accédez à l’interface Windows PowerShell sur la console série de votre appareil StorSimple. Consultez la page [Utilisation de PuTTY pour se connecter à la console en série de l’appareil](#use-putty-to-connect-to-the-device-serial-console) pour obtenir des instructions. **Veillez à suivre la procédure pas à pas, sans quoi vous ne pourrez pas accéder à la console.**
2. Dans la session qui s’ouvre, appuyez une fois sur Entrée pour lancer une invite de commandes. 
3. Vous devez choisir la langue que vous souhaitez définir pour votre appareil. Spécifiez la langue, puis appuyez sur Entrée. 
   
    ![Configuration et inscription de l’appareil StorSimple 1](./media/storsimple-configure-and-register-device-gov/HCS_RegisterYourDevice1-gov-include.png)
4. Dans le menu de console en série qui s’affiche, choisissez l’option 1 pour ouvrir une session offrant un accès complet. 
   
    ![Inscription de l’appareil StorSimple 2](./media/storsimple-configure-and-register-device-gov/HCS_RegisterYourDevice2-gov-include.png)
5. Procédez comme suit pour configurer les paramètres réseau minimum requis pour votre appareil.
   
   > [!IMPORTANT]
   > Ces étapes de configuration doivent être effectuées sur le contrôleur actif de l’appareil. Le menu de la console en série indique l’état du contrôleur dans le message de bannière. Si vous n’êtes pas connecté au contrôleur actif, déconnectez-vous, puis connectez-vous à ce contrôleur.
   > 
   > 
   
   1. À l’invite de commandes, tapez votre mot de passe. Le mot de passe par défaut de l’appareil est **Password1**.
   2. Tapez la commande suivante : 
      
        `Invoke-HcsSetupWizard`
   3. Un Assistant Installation s’affiche pour vous aider à configurer les paramètres réseau de l’appareil. Fournissez les informations suivantes : 
      
      * Adresse IP de l’interface réseau DATA 0
      * Masque de sous-réseau
      * Passerelle
      * Adresse IP du serveur DNS principal
      * Adresse IP du serveur NTP principal
      
      > [!NOTE]
      > Vous devrez peut-être attendre quelques minutes que les paramètres de masque de sous-réseau et de DNS soient appliqués. 
      > 
      > 
   4. Configurez éventuellement votre serveur proxy web.
      
      > [!IMPORTANT]
      > Bien que la configuration du proxy web soit facultative, si vous en utilisez un, vous pouvez uniquement le configurer ici. Pour plus d’informations, consultez la section [Configuration du proxy web pour votre appareil](../articles/storsimple/storsimple-configure-web-proxy.md). 
      > 
      > 
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
      
      It may take up to 11 hours to apply all the updates, including the Windows Updates.
8. After all the updates are successfully installed, run the following cmdlet to confirm that the software updates were applied correctly:
   
     `Get-HcsSystem`
   
    You should see the following versions:
   
   * HcsSoftwareVersion: 6.3.9600.17491
   * CisAgentVersion: 1.0.9037.0
   * MdsAgentVersion: 26.0.4696.1433
9. Run the following cmdlet to confirm that the firmware update was applied correctly:
   
    `Start-HcsFirmwareCheck`.
   
     The firmware status should be **UpToDate**.
10. Run the following cmdlet to point the device to the Microsoft Azure Government portal (because it points to the public Azure classic portal by default). This will restart both controllers. We recommend that you use two PuTTY sessions to simultaneously connect to both controllers so that you can see when each controller is restarted.
    
     `Set-CloudPlatform -AzureGovt_US`
    
    You will see a confirmation message. Accept the default (**Y**).
11. Run the following cmdlet to resume setup:
    
     `Invoke-HcsSetupWizard`
    
     ![Resume setup wizard](./media/storsimple-configure-and-register-device-gov/HCS_ResumeSetup-gov-include.png)
    
    When you resume setup, the wizard will be the Update 1 version (which corresponds to version 17469). 
12. Accept the network settings. You will see a validation message after you accept each setting.
13. For security reasons, the device administrator password expires after the first session, and you will need to change it now. When prompted, provide a device administrator password. A valid device administrator password must be between 8 and 15 characters. The password must contain three of the following: lowercase, uppercase, numeric, and special characters.
    
    <br/>![StorSimple register device 5](./media/storsimple-configure-and-register-device-gov/HCS_RegisterYourDevice5_gov-include.png)
14. The final step in the setup wizard registers your device with the StorSimple Manager service. For this, you will need the service registration key that you obtained in [Step 2: Get the service registration key](#step-2-get-the-service-registration-key). After you supply the registration key, you may need to wait for 2-3 minutes before the device is registered.
    
    > [!NOTE]
    > You can press Ctrl + C at any time to exit the setup wizard. If you have entered all the network settings (IP address for Data 0, Subnet mask, and Gateway), your entries will be retained.
    > 
    > 
    
    ![StorSimple registration progress](./media/storsimple-configure-and-register-device-gov/HCS_RegistrationProgress-gov-include.png)
15. After the device is registered, a Service Data Encryption key will appear. Copy this key and save it in a safe location. **This key will be required with the service registration key to register additional devices with the StorSimple Manager service.** Refer to [StorSimple security](../articles/storsimple/storsimple-security.md) for more information about this key.
    
    ![StorSimple register device 7](./media/storsimple-configure-and-register-device-gov/HCS_RegisterYourDevice7_gov-include.png)    
    
    > [!IMPORTANT]
    > To copy the text from the serial console window, simply select the text. You should then be able to paste it in the clipboard or any text editor. 
    > 
    > DO NOT use Ctrl + C to copy the service data encryption key. Using Ctrl + C will cause you to exit the setup wizard. As a result, the device administrator password will not be changed and the device will revert to the default password.
    > 
    > 
16. Exit the serial console.
17. Return to the Azure Government Portal, and complete the following steps:
    
    1. Double-click your StorSimple Manager service to access the **Quick Start** page.
    2. Click **View connected devices**.
    3. On the **Devices** page, verify that the device has successfully connected to the service by looking up the status. The device status should be **Online**.
       
        ![StorSimple Devices page](./media/storsimple-configure-and-register-device-gov/HCS_DeviceOnline-gov-include.png) 
       
        If the device status is **Offline**, wait for a couple of minutes for the device to come online. 
       
        If the device is still offline after a few minutes, then you need to make sure that your firewall network was configured as described in [networking requirements for your StorSimple device](../articles/storsimple/storsimple-system-requirements.md). 
       
        Verify that port 9354 is open for outbound communication as this is used by the service bus for StorSimple Manager service-to-device communication.



<!--HONumber=Nov16_HO3-->


