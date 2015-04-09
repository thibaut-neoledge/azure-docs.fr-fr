1. Si vous n'avez pas déjà inscrit votre application, accédez à la page [Soumettre une application] du Centre de développement pour les applications Windows Store, connectez-vous à votre compte Microsoft, puis cliquez sur le **Nom de l'application**.

   	![](./media/mobile-services-register-windows-store-app/mobile-services-submit-win8-app.png)

2. Tapez un nom pour l'application dans **Nom de l'application**, cliquez sur **Réserver le nom d'application**, puis sur **Enregistrer**.

   	![](./media/mobile-services-register-windows-store-app/mobile-services-win8-app-name.png)

   	La nouvelle inscription au Windows Store pour votre application est créée.

3. Dans Visual Studio, ouvrez le projet que vous avez créé quand vous avez suivi le didacticiel [Prise en main de Mobile Services].

4. Dans l'Explorateur de solutions, cliquez avec le bouton droit sur le projet, cliquez sur **Store**, puis sur **Associer l'application au Windows Store...**. 

  	![](./media/mobile-services-register-windows-store-app/mobile-services-store-association.png)

   	L'Assistant **Associer votre application au Windows Store** s'affiche.

5. Dans l'Assistant, cliquez sur **Se connecter**, puis connectez-vous avec votre compte Microsoft.

6. Sélectionnez l'application inscrite à l'étape 2, cliquez sur **Suivant**, puis sur **Associer**.

   	![](./media/mobile-services-register-windows-store-app/mobile-services-select-app-name.png)

   	Cela ajoute les informations d'inscription Windows Store requises au manifeste de l'application.    

7. De retour sur la page du centre de développement Windows pour la nouvelle application, cliquez sur **Services**. 

   	![](./media/mobile-services-register-windows-store-app/mobile-services-win8-edit-app.png) 

8. Dans la page Services, cliquez sur **Site des services Microsoft Live** sous **Azure Mobile Services**.

	![](./media/mobile-services-register-windows-store-app/mobile-services-win8-edit2-app.png) 

9. Dans **Paramètres de l'application**, notez les valeurs **ID client**, **Clé secrète client** et **Identificateur de sécurité (SID) du package**. 

   	![](./media/mobile-services-register-windows-store-app/mobile-services-win8-app-push-auth.png)

    >[AZURE.NOTE]La clé secrète client et le SID du package sont des informations d'identification de sécurité importantes. Ne partagez pas ces informations secrètes avec quiconque et ne les distribuez pas avec votre application.

10. (Facultatif) Cliquez sur **Paramètres API**, activez **Sécurité de redirection renforcée**, entrez une valeur de `https://<mobile_service>.azure-mobile.net/login/microsoftaccount` dans **URL de redirection**, puis cliquez sur **Enregistrer**.

	![](./media/mobile-services-register-windows-store-app/mobile-services-win8-app-push-auth-2.png)

	Cela permet d'activer l'authentification du compte Microsoft sur votre application.

11. Connectez-vous au [portail de gestion Azure], puis cliquez sur **Mobile Services** et sur l'application.

   	![](./media/mobile-services-register-windows-store-app/mobile-services-selection.png)

12. Cliquez sur l'onglet **Notifications Push**, entrez les valeurs **Clé secrète du client** et **SID du package** obtenues auprès de WNS à l'étape 4, puis cliquez sur **Enregistrer**.

   	![](./media/mobile-services-register-windows-store-app/mobile-push-tab.png)

13. Cliquez sur l'onglet **Identité**. Notez que les valeurs **Clé secrète client** et **SID du package** ont déjà été définies lors de l'étape précédente. Entrez l'**ID client** que vous aviez noté, puis cliquez sur **Enregistrer**.

   	![](./media/mobile-services-register-windows-store-app/mobile-services-identity-tab.png)
 
Vous êtes maintenant prêt à utiliser un compte Microsoft pour l'authentification dans votre application.  

<!-- Anchors. -->

<!-- Images. -->
 

<!-- URLs. -->
[Prise en main de Mobile Services]: /develop/mobile/tutorials/get-started/#create-new-service
[Page Soumette une application]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Portail de gestion Azure]: https://manage.windowsazure.com/

<!--HONumber=49-->