Après l'inscription de votre application auprès d'APNS et une fois votre projet configuré, vous devez configurer votre service mobile pour l'intégrer à APNS.

1. Dans le trousseau d'accès, cliquez avec le bouton droit sur le nouveau certificat de l'application de démarrage rapide dans **Clés** ou **Mes certificats**, cliquez sur **Exporter**, nommez votre fichier QuickstartPusher, sélectionnez le format **.p12**, puis cliquez sur **Enregistrer**.

   	![](./media/mobile-services-apns-configure-push/mobile-services-ios-push-step18.png)

  Notez le nom du fichier et l'emplacement du certificat exporté.

>[AZURE.NOTE] Ce didacticiel crée un fichier QuickstartPusher.p12. Il est possible que le nom de votre fichier et son emplacement sont différents.

2. Connectez-vous au [portail de gestion Azure], puis cliquez sur **Mobile Services** et sur l'application.

   	![](./media/mobile-services-apns-configure-push/mobile-services-selection.png)

3. Cliquez sur l'onglet **Push**, puis sur **Télécharger**.

   	![](./media/mobile-services-apns-configure-push/mobile-push-tab-ios.png)

	Ceci affiche la boîte de dialogue Télécharger le certificat.

4. Cliquez sur **Fichier**, sélectionnez le fichier QuickstartPusher.p12 du certificat exporté, entrez le **Mot de passe**, assurez-vous que le **Mode** approprié est sélectionné (Dev/Sandbox ou Prod/Production), cliquez sur l'icône, puis sur **Enregistrer**.

   	![](./media/mobile-services-apns-configure-push/mobile-push-tab-ios-upload.png)

    > [AZURE.NOTE] Ce didacticiel utilise des certificats de développement.

Votre service mobile est maintenant configuré pour fonctionner avec APNS.

<!-- URLs. -->
[Portail de gestion Azure]: https://manage.windowsazure.com/

<!--HONumber=42-->
