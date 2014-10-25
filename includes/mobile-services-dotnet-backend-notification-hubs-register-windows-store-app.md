1.  Si vous n'avez pas déjà inscrit votre application, accédez à la page [Soumettre une application][] du Centre de développement pour les applications Windows Store, connectez-vous à votre compte Microsoft, puis cliquez sur le **Nom de l'application**.

    ![][]

2.  Tapez un nom pour l'application dans **Nom de l'application**, cliquez sur **Réserver le nom d'application**, puis sur **Enregistrer**.

    ![][1]

    La nouvelle inscription au Windows Store pour votre application est créée.

3.  Dans Visual Studio, ouvrez le projet d'application Windows Store que vous avez créé quand vous avez suivi le didacticiel **Prise en main de Mobile Services**.

4.  Dans l'Explorateur de solutions, cliquez avec le bouton droit sur le projet, cliquez sur **Store**, puis sur **Associer l'application au Windows Store...**.

    ![][2]

    L'Assistant **Associer votre application au Windows Store** s'affiche.

5.  Dans l'Assistant, cliquez sur **Se connecter**, puis connectez-vous avec votre compte Microsoft.

6.  Sélectionnez l’application inscrite à l’étape 2, cliquez sur **Suivant**, puis sur **Associer**.

    ![][3]

    Cela ajoute les informations d'inscription Windows Store requises au manifeste de l'application.

7.  (Facultatif) Répétez les étapes 4 à 6 pour inscrire aussi le projet Windows Phone Store d'une application Windows universelle.

8.  De retour sur la page du centre de développement Windows pour la nouvelle application, cliquez sur **Services**.

    ![][4]

9.  Sur la page Services, cliquez sur **Live Services site** sous **Azure Mobile Services**.

    ![][5]

10. Cliquez sur **Authentification de votre service** et notez les valeurs des options **Clé secrète client** et **Identificateur de sécurité (SID) du package**.

    ![][6]

    <div class="dev-callout"><b>Remarque relative &agrave; la s&eacute;curit&eacute;</b>
<p>La cl&eacute; secr&egrave;te client et le SID du package sont des informations d'identification de s&eacute;curit&eacute; importantes. Ne partagez pas ces informations secr&egrave;tes avec quiconque et ne les distribuez pas avec votre application.</p>
</div>

11. Connectez-vous au [portail de gestion Azure][], cliquez sur **Mobile Services**, puis sur l'application.

    ![][7]

12. Cliquez sur l'onglet **Notifications push**, entrez les valeurs **Question secrète du client** et **SID du package** obtenues auprès de WNS à l'étape 4, puis cliquez sur **Enregistrer**.

    ![][8]

    > [WACOM.NOTE] Quand vous définissez vos informations d'identification WNS pour les notifications Push améliorées dans l'onglet **Notifications push** du portail, celles-ci sont partagées avec les Notification Hubs pour configurer le Notification Hub pour votre application.

<!-- URLs. -->

  [Soumettre une application]: http://go.microsoft.com/fwlink/p/?LinkID=266582
  []: ./media/mobile-services-dotnet-backend-notification-hubs-register-windows-store-app/mobile-services-submit-win8-app.png
  [1]: ./media/mobile-services-dotnet-backend-notification-hubs-register-windows-store-app/mobile-services-win8-app-name.png
  [2]: ./media/mobile-services-dotnet-backend-notification-hubs-register-windows-store-app/mobile-services-store-association.png
  [3]: ./media/mobile-services-dotnet-backend-notification-hubs-register-windows-store-app/mobile-services-select-app-name.png
  [4]: ./media/mobile-services-dotnet-backend-notification-hubs-register-windows-store-app/mobile-services-win8-edit-app.png
  [5]: ./media/mobile-services-javascript-backend-register-windows-store-app/mobile-services-win8-edit2-app.png
  [6]: ./media/mobile-services-dotnet-backend-notification-hubs-register-windows-store-app/mobile-services-win8-app-push-auth.png
  [portail de gestion Azure]: https://manage.windowsazure.com/
  [7]: ./media/mobile-services-dotnet-backend-notification-hubs-register-windows-store-app/mobile-services-selection.png
  [8]: ./media/mobile-services-dotnet-backend-notification-hubs-register-windows-store-app/mobile-push-tab.png
