Après l'inscription de votre application auprès d'APNS et une fois votre projet configuré, vous devez configurer votre service mobile pour l'intégrer à APNS.

1.  Dans Trousseau d'accès, cliquez avec le bouton droit sur le nouveau certificat, cliquez sur **Exporter**, nommez votre fichier QuickstartPusher, sélectionnez le format **.p12** puis cliquez sur **Enregistrer**.

    ![][0]

  Notez le nom du fichier et l'emplacement du certificat exporté.

	> [WACOM.NOTE] Ce didacticiel crée un fichier QuickstartPusher.p12. Il est possible que le nom de votre fichier et son emplacement sont différents.

1.  Connectez-vous au [portail de gestion Azure][portail de gestion Azure], cliquez sur **Mobile Services**, puis sur l'application.

    ![][1]

2.  Cliquez sur l'onglet **Push**, puis sur **Télécharger**.

    ![][2]

    Ceci affiche la boîte de dialogue Télécharger le certificat.

3.  Cliquez sur **Fichier**, sélectionnez le fichier QuickstartPusher.p12 du certificat exporté, entrez le **Mot de passe**, vérifiez que le **Mode** sélectionné est correct, cliquez sur l'icône en forme de coche, puis cliquez sur **Enregistrer**.

    ![][3]

    > [WACOM.NOTE] Ce didacticiel utilise des certificats de développement.

Votre service mobile est maintenant configuré pour fonctionner avec APNS.



  [0]: ./media/mobile-services-apns-configure-push/mobile-services-ios-push-step18.png
  [portail de gestion Azure]: https://manage.windowsazure.com/
  [1]: ./media/mobile-services-apns-configure-push/mobile-services-selection.png
  [2]: ./media/mobile-services-apns-configure-push/mobile-push-tab-ios.png
  [3]: ./media/mobile-services-apns-configure-push/mobile-push-tab-ios-upload.png
