1.  Si vous n'avez pas déjà inscrit votre application, accédez à la page [Soumettre une application][Soumettre une application] du Centre de développement pour les applications Windows Store, connectez-vous à votre compte Microsoft, puis cliquez sur le **Nom de l'application**.

    ![][0]

2.  Tapez un nom pour l'application dans **Nom de l'application**, cliquez sur **Réserver le nom d'application**, puis sur **Enregistrer**.

    ![][1]

    La nouvelle inscription au Windows Store pour votre application est créée.

3.  Dans Visual Studio, ouvrez le projet que vous avez créé lorsque vous avez suivi le didacticiel [Prise en main de Mobile Services][Prise en main de Mobile Services].

4.  Dans l'Explorateur de solutions, cliquez avec le bouton droit sur le projet, cliquez sur **Store**, puis sur **Associer l'application au Windows Store...**.

    ![][2]

    L'Assistant **Associer votre application au Windows Store** s'affiche.

5.  Dans l'Assistant, cliquez sur **Se connecter**, puis connectez-vous avec votre compte Microsoft.

6.  Sélectionnez l’application inscrite à l’étape 2, cliquez sur **Suivant**, puis sur **Associer**.

    ![][3]

    Cela ajoute les informations d'inscription Windows Store requises au manifeste de l'application.

7.  De retour sur la page du centre de développement Windows pour la nouvelle application, cliquez sur **Services**.

    ![][4]

8.  Sur la page Services, cliquez sur **Live Services site** sous **Azure Mobile Services**.

    ![][5]

9.  Dans **Paramètres de l'application**, prenez note des valeurs **ID client**, **Clé secrète client** et **Identificateur de sécurité (SID) du package**.

    ![][6]

    > [WACOM.NOTE]La clé secrète cliente et le SID du package sont des informations d’identification de sécurité importantes. Ne partagez pas ces informations secrètes avec quiconque et ne les distribuez pas avec votre application.

10. (Facultatif) Cliquez sur **Paramètres de l'API**, activez **Sécurité de redirection renforcée**, entrez une valeur de `https://<mobile_service>.azure-mobile.net/login/microsoftaccount` dans **Redirection d’URL**, puis cliquez sur **Enregistrer**.

    ![][7]

    Cela permet d'activer l'authentification du compte Microsoft sur votre application.

11. Connectez-vous au [portail de gestion Azure][portail de gestion Azure], cliquez sur **Mobile Services**, puis sur l'application.

    ![][8]

12. Cliquez sur l’onglet **Push**, entrez les valeurs **Clé secrète client** et **SID du package** obtenues à partir de WNS à l’étape 4, puis cliquez sur **Enregistrer**.

    ![][9]

13. Cliquez sur l'onglet **Identité**. Notez que les valeurs **Clé secrète client** et **SID du package** ont déjà été définies lors de l'étape précédente. Entrez l'**ID client** que vous aviez noté, et cliquez ensuite sur **Enregistrer**.

    ![][10]

Vous êtes maintenant prêt à utiliser un compte Microsoft pour l'authentification dans votre application.

 
 


  [Soumettre une application]: http://go.microsoft.com/fwlink/p/?LinkID=266582
  [0]: ./media/mobile-services-register-windows-store-app/mobile-services-submit-win8-app.png
  [1]: ./media/mobile-services-register-windows-store-app/mobile-services-win8-app-name.png
  [Prise en main de Mobile Services]: /fr-fr/develop/mobile/tutorials/get-started/#create-new-service
  [2]: ./media/mobile-services-register-windows-store-app/mobile-services-store-association.png
  [3]: ./media/mobile-services-register-windows-store-app/mobile-services-select-app-name.png
  [4]: ./media/mobile-services-register-windows-store-app/mobile-services-win8-edit-app.png
  [5]: ./media/mobile-services-register-windows-store-app/mobile-services-win8-edit2-app.png
  [6]: ./media/mobile-services-register-windows-store-app/mobile-services-win8-app-push-auth.png
  [7]: ./media/mobile-services-register-windows-store-app/mobile-services-win8-app-push-auth-2.png
  [portail de gestion Azure]: https://manage.windowsazure.com/
  [8]: ./media/mobile-services-register-windows-store-app/mobile-services-selection.png
  [9]: ./media/mobile-services-register-windows-store-app/mobile-push-tab.png
  [10]: ./media/mobile-services-register-windows-store-app/mobile-services-identity-tab.png
