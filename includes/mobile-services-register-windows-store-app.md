
1. Si vous n’avez pas déjà inscrit votre application, accédez à la page [Soumettre une application] du Centre de développement pour les applications Windows Store, connectez-vous à votre compte Microsoft, puis cliquez sur le **Nom de l’application**.

   	![](./media/mobile-services-register-windows-store-app/mobile-services-submit-win8-app.png)

2. Sélectionnez **Créer une application en réservant un nom unique**, cliquez sur **Continuer**, tapez un nom pour votre application dans **Nom de l'application**, cliquez sur **Réserver le nom d'application**, puis cliquez sur **Enregistrer**.

   	![](./media/mobile-services-register-windows-store-app/mobile-services-win8-app-name.png)

   	La nouvelle inscription au Windows Store pour votre application est créée.

3. Dans Visual Studio, ouvrez le projet que vous avez créé lorsque vous avez suivi le didacticiel [Prise en main de Mobile Services].

4. Dans l'Explorateur de solutions, cliquez avec le bouton droit sur le projet d'application du Windows Store, cliquez sur **Store**, puis sur **Associer l'application au Windows Store...**.

  	![](./media/mobile-services-register-windows-store-app/mobile-services-store-association.png)

   	L'Assistant **Associer votre application au Windows Store** s'affiche.

5. Dans l'Assistant, cliquez sur **Se connecter**, connectez-vous avec votre compte Microsoft, sélectionnez le nom de l'application que vous avez réservé à l'étape 2, cliquez sur **Suivant**, puis cliquez sur **Associer**.

   	Cela ajoute les informations d'inscription Windows Store requises au manifeste de l'application.

6. (Facultatif) Pour une application Windows universelle, répétez les étapes 4 et 5 pour le projet du Windows Phone Store.

6. De retour sur la page du centre de développement Windows pour la nouvelle application, cliquez sur **Services**.

   	![](./media/mobile-services-register-windows-store-app/mobile-services-win8-edit-app.png)

7. Sur la page Services, cliquez sur **Live Services site** sous **Azure Mobile Services**.

	![](./media/mobile-services-register-windows-store-app/mobile-services-win8-edit2-app.png)

8. Cliquez sur **Paramètres de l'API**, sélectionnez **Application cliente mobile ou de bureau**, définissez l'URL du service mobile en tant que **Domaine cible**, fournissez une valeur de `https://<mobile_service>.azure-mobile.net/login/microsoftaccount/` dans **URL de redirection**, puis cliquez sur **Enregistrer**.

	![](./media/mobile-services-register-windows-store-app/mobile-services-win8-app-push-auth-2.png)

9. Dans **Paramètres de l'application**, prenez note des valeurs **ID client**, **Clé secrète client** et **Identificateur de sécurité (SID) du package**.

   	![](./media/mobile-services-register-windows-store-app/mobile-services-win8-app-push-auth.png)

    >[AZURE.NOTE]La clé secrète client et le SID du package sont des informations d'identification de sécurité importantes. Ne partagez pas ces informations secrètes avec quiconque et ne les distribuez pas avec votre application.

10. Connectez-vous au [portail de gestion Azure], cliquez sur **Mobile Services**, puis sur l'application.

11. Cliquez sur l'onglet **Identité**, entrez les valeurs **Clé secrète client** et **SID du package** obtenues à partir de WNS à l'étape 4, puis cliquez sur **Enregistrer**.

   	![](./media/mobile-services-register-windows-store-app/mobile-push-tab.png)

13. Cliquez sur l'onglet **Identité**. Notez que les valeurs **Clé secrète client** et **SID du package** ont déjà été définies lors de l'étape précédente. Entrez l'**ID client** que vous aviez noté, et cliquez ensuite sur **Enregistrer**.

   	![](./media/mobile-services-register-windows-store-app/mobile-services-identity-tab.png)
 
Vous êtes maintenant prêt à utiliser un compte Microsoft pour l'authentification de votre application.

<!-- Anchors. -->

<!-- Images. -->

<!-- URLs. -->
[Prise en main de Mobile Services]: /develop/mobile/tutorials/get-started/#create-new-service
[Soumettre une application]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[portail de gestion Azure]: https://manage.windowsazure.com/

<!---HONumber=July15_HO2-->