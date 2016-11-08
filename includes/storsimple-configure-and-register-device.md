<!--author=alkohli last changed: 12/01/15-->


#### Configuration et inscription de l’appareil
1. Accédez à l’interface Windows PowerShell sur la console série de votre appareil StorSimple. Consultez la page [Utilisation de PuTTY pour se connecter à la console en série de l’appareil](#use-putty-to-connect-to-the-device-serial-console) pour obtenir des instructions. **Veillez à suivre la procédure pas à pas, sans quoi vous ne pourrez pas accéder à la console.**
2. Dans la session qui s’ouvre, appuyez une fois sur Entrée pour lancer une invite de commandes.
3. Vous devez choisir la langue que vous souhaitez définir pour votre appareil. Spécifiez la langue, puis appuyez sur Entrée.
   
    ![Configuration et inscription de l’appareil StorSimple 1](./media/storsimple-configure-and-register-device/HCS_RegisterYourDevice1-include.png)
4. Dans le menu de console en série qui s’affiche, choisissez l’option 1 pour ouvrir une session offrant un accès complet.
   
    ![Inscription de l’appareil StorSimple 2](./media/storsimple-configure-and-register-device/HCS_RegisterYourDevice2-include.png)
   
     Suivez les étapes 5 à 12 pour configurer les paramètres réseau minimum requis pour votre appareil. **Ces étapes de configuration doivent être effectuées sur le contrôleur actif de l’appareil.** Le menu de la console en série indique l’état du contrôleur dans le message de bannière. Si vous n’êtes pas connecté au contrôleur actif, déconnectez-vous, puis connectez-vous à ce contrôleur.
5. À l’invite de commandes, tapez votre mot de passe. Le mot de passe par défaut de l’appareil est **Password1**.
6. Tapez la commande suivante :
   
     `Invoke-HcsSetupWizard`
7. Un Assistant Installation s’affiche pour vous aider à configurer les paramètres réseau de l’appareil. Fournissez les informations suivantes :
   
   * Adresse IP de l’interface réseau DATA 0
   * Masque de sous-réseau
   * Passerelle
   * Adresse IP du serveur DNS principal
   * Adresse IP du serveur NTP principal
     
     > [!NOTE]
     > Vous devrez peut-être attendre quelques minutes que les paramètres de masque de sous-réseau et de DNS soient appliqués. Si vous obtenez le message d’erreur « Le périphérique n’est pas prêt », vérifiez la connexion réseau physique sur l’interface réseau DATA 0 de votre contrôleur actif.
     > 
     > 
8. (Facultatif) Configurez votre serveur proxy web. Bien que la configuration du proxy web soit facultative, **si vous en utilisez un, vous pouvez uniquement le configurer ici**. Pour plus d’informations, consultez la section [Configuration du proxy web pour votre appareil](../articles/storsimple/storsimple-configure-web-proxy.md). Si vous rencontrez des problèmes au cours de cette étape, reportez-vous aux instructions pour la résolution des problèmes pour les [Erreurs lors de la configuration du proxy web](../articles/storsimple/storsimple-troubleshoot-deployment.md#errors-during-the-optional-web-proxy-settings).

      > [AZURE.NOTE] Vous pouvez appuyer sur Ctrl + C à tout moment pour quitter l’Assistant Installation. Tous les paramètres que vous avez appliqués avant l’émission de cette commande sont conservés.

1. Pour des raisons de sécurité, le mot de passe administrateur de l’appareil expire après la première session, et vous devez le modifier pour les sessions ultérieures. Lorsque vous y êtes invité, fournissez un mot de passe administrateur de l’appareil. Un mot de passe administrateur d’appareil valide doit comprendre entre 8 et 15 caractères. Le mot de passe doit contenir une combinaison de caractères en minuscules, en majuscules, numériques et spéciaux.
2. Le mot de passe du gestionnaire d’instantanés StorSimple est également défini ici. Vous utilisez ce mot de passe lorsque vous authentifiez un appareil avec votre hôte Windows en exécutant le gestionnaire d’instantanés StorSimple. Lorsque vous y êtes invité, indiquez un mot de passe comprenant de 14 à 15 caractères. Le mot de passe doit contenir une combinaison de trois des éléments suivants : caractères en minuscules, en majuscules, numériques et spéciaux.
   
   ![Inscription de l’appareil StorSimple 4](./media/storsimple-configure-and-register-device/HCS_RegisterYourDevice4-include.png)
   
   Vous pouvez réinitialiser le mot de passe du gestionnaire d’instantanés StorSimple à partir de l’interface de service StorSimple Manager. Pour des instructions détaillées, consultez la page [Modifier les mots de passe StorSimple à l’aide du service Gestionnaire StorSimple](../articles/storsimple/storsimple-change-passwords.md).
   
   Pour effectuer des dépannages au cours de cette étape, reportez-vous aux instructions pour la résolution des problèmes pour les [Erreurs liées aux mots de passe](../articles/storsimple/storsimple-troubleshoot-deployment.md#errors-related-to-device-administrator-and-storsimple-snapshot-manager-passwords).
3. La dernière étape de l’Assistant Installation inscrit votre appareil auprès du service StorSimple Manager. Pour cela, vous avez besoin de la clé d’inscription de service que vous avez obtenue à l’étape 2. Après avoir entré la clé d’inscription, vous devrez peut-être attendre 2 à 3 minutes avant que l’appareil ne soit inscrit.
   
   Pour dépanner toute défaillance possible de l’inscription d’un appareil, consultez la rubrique [Erreurs pendant l’inscription d’un appareil](../articles/storsimple/storsimple-troubleshoot-deployment.md#errors-during-device-registration). Pour obtenir des instructions de résolution de problèmes détaillées, vous pouvez également consulter la rubrique [Exemple de résolution des problèmes pas à pas](../articles/storsimple/storsimple-troubleshoot-deployment.md#step-by-step-storsimple-troubleshooting-example).
4. Une fois l’appareil inscrit, une clé de chiffrement de données de service s’affiche. Copiez-la et enregistrez-la en lieu sûr.
   
   > [!WARNING]
   > Cette clé et la clé d’enregistrement de service sont requises pour l’inscription d’appareils supplémentaires avec le service StorSimple Manager. Reportez-vous à la section [Sécurité StorSimple](../articles/storsimple/storsimple-security.md) pour plus d’informations sur cette clé.
   > 
   > 
   
    ![Inscription de l’appareil StorSimple 6](./media/storsimple-configure-and-register-device/HCS_RegisterYourDevice6-include.png)
   
    Pour copier le texte à partir de la fenêtre de console de série, sélectionnez-le simplement. Vous devez ensuite pouvoir le coller dans le Presse-papiers ou dans un éditeur de texte. N’utilisez PAS Ctrl + C pour copier la clé de chiffrement de données de service. Cette combinaison de touches ferme l’Assistant Installation. Par conséquent, le mot de passe administrateur de l’appareil et le mot de passe du gestionnaire d’instantanés StorSimple ne sont pas modifiés et l’appareil revient aux mots de passe par défaut.
5. Quittez la console en série.
6. Revenez au portail Azure Classic et procédez comme suit :
   
   1. Double-cliquez sur le service StorSimple Manager pour accéder à la page **Démarrage rapide**.
   2. Cliquez sur **Afficher les appareils connectés**.
   3. Sur la page **Appareils**, vérifiez que l’appareil s’est bien connecté au service en vérifiant son état. L’état de l’appareil doit être **En ligne**. Si l’état de l’appareil est **Hors ligne**, attendez quelques minutes qu’il soit en ligne.
   
   ![Page Appareils StorSimple](./media/storsimple-configure-and-register-device/HCS_DevicesPageM-include.png)
   
   > [!IMPORTANT]
   > Une fois que le périphérique est en ligne, branchez les câbles réseau que vous aviez déconnectés au début de cette étape.
   > 
   > 

Une fois que l’appareil est inscrit correctement et n’est pas en ligne, vous pouvez exécuter la commande `Test-HcsmConnection -Verbose` pour vous assurer que la connectivité réseau est intègre. Pour plus d’informations sur l’utilisation détaillée de cette applet de commande, référez-vous au [Guide de référence des applets de commande Test-HcsmConnection](https://technet.microsoft.com/library/dn715782.aspx).

![Vidéo disponible](./media/storsimple-configure-and-register-device/Video_icon.png) **Vidéo disponible**

Pour visionner une vidéo qui montre comment configurer et inscrire votre appareil via Windows PowerShell pour StorSimple, cliquez [ici](https://azure.microsoft.com/documentation/videos/initialize-the-storsimple-appliance/).

<!---HONumber=AcomDC_0224_2016-->