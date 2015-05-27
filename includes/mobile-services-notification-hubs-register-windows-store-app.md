

1. Si vous n’avez pas déjà inscrit votre application, accédez à la page [Soumettre une application] du Centre de développement pour les applications Windows Store, connectez-vous à votre compte Microsoft, puis cliquez sur le **Nom de l’application**.

   	![](./media/mobile-services-notification-hubs-register-windows-store-app/mobile-services-submit-win8-app.png)

2. Tapez un nom pour l'application dans **Nom de l'application**, cliquez sur **Réserver le nom d'application**, puis sur **Enregistrer**.

   	![](./media/mobile-services-notification-hubs-register-windows-store-app/mobile-services-win8-app-name.png)

   	La nouvelle inscription au Windows Store pour votre application est créée.

3. Dans Visual Studio, ouvrez le projet que vous avez créé lorsque vous avez suivi le didacticiel **Prise en main de Mobile Services**.

4. Dans l’Explorateur de solutions, cliquez avec le bouton droit sur le projet, cliquez sur **Store**, puis sur **Associer l’application au Windows Store...**.

  	![](./media/mobile-services-notification-hubs-register-windows-store-app/mobile-services-store-association.png)

   	L'Assistant **Associer votre application au Windows Store** s'affiche.

5. Dans l'Assistant, cliquez sur **Se connecter**, puis connectez-vous avec votre compte Microsoft.

6. Sélectionnez l’application inscrite à l’étape 2, cliquez sur **Suivant**, puis sur **Associer**.

   	![](./media/mobile-services-notification-hubs-register-windows-store-app/mobile-services-select-app-name.png)

   	Cela ajoute les informations d'inscription Windows Store requises au manifeste de l'application.

7. (Facultatif) Répétez les étapes 4 à 6 pour inscrire aussi le projet Windows Phone Store d'une application Windows universelle.

8. De retour sur la page du centre de développement Windows pour la nouvelle application, cliquez sur **Services**.

   	![](./media/mobile-services-notification-hubs-register-windows-store-app/mobile-services-win8-edit-app.png)

9. Sur la page Services, cliquez sur **Live Services site** sous **Azure Mobile Services**.

	![](./media/mobile-services-javascript-backend-register-windows-store-app/mobile-services-win8-edit2-app.png)

10. Cliquez sur **Authentification de votre service** et notez les valeurs des options **Clé secrète client** et **Identificateur de sécurité (SID) du package**.

   	![](./media/mobile-services-notification-hubs-register-windows-store-app/mobile-services-win8-app-push-auth.png)

    > [AZURE.NOTE]La clé secrète client et le SID du package sont des informations d'identification de sécurité importantes. Ne partagez pas ces informations secrètes avec quiconque et ne les distribuez pas avec votre application.

11. Connectez-vous au [portail de gestion Azure], cliquez sur **Mobile Services**, puis sur l'application.

   	![](./media/mobile-services-notification-hubs-register-windows-store-app/mobile-services-selection.png)

12. Cliquez sur l'onglet **Notifications push**, entrez les valeurs **Question secrète du client** et **SID du package** obtenues auprès de WNS, puis cliquez sur **Enregistrer**.

	>[AZURE.NOTE]Si vous suivez ce didacticiel avec un service mobile plus ancien, vous êtes susceptible de voir un lien dans le bas de l'onglet **Notifications push** avec le texte **Activer la transmission push améliorée**. Cliquez sur ce lien pour mettre à niveau votre service mobile et l'intégrer avec Notification Hubs. Cette modification est irréversible. Pour plus d'informations sur l'activation des notifications push améliorées dans un service mobile de production, consultez <a href="http://go.microsoft.com/fwlink/p/?LinkId=391951">ce guide</a>.

   	![](./media/mobile-services-notification-hubs-register-windows-store-app/mobile-push-tab.png)

	>[AZURE.NOTE]Quand vous définissez vos informations d'identification WNS pour les notifications push améliorées dans l'onglet **Notifications push** du portail, celles-ci sont partagées avec Notification Hubs pour configurer le concentrateur de notification de votre application.

<!-- URLs. -->
[Get started with Mobile Services]: ../articles/mobile-services-windows-store-get-started.md
[Soumettre une application]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[portail de gestion Azure]: https://manage.windowsazure.com/

<!--HONumber=54-->