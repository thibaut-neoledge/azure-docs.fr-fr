

1.  Si votre application n'est pas encore enregistrée, accédez à la [page Soumettre une application](http://go.microsoft.com/fwlink/p/?LinkID=266582) du Centre de développement des applications Windows Store, connectez-vous à votre compte Microsoft, puis cliquez sur le **Nom de l'application**.

	![](./media/mobile-services-javascript-backend-register-windows-store-app/mobile-services-submit-win8-app.png)

2.  Tapez un nom pour votre application dans **Nom de l'application**, puis cliquez sur **Réserver le nom d'application** et sur **Enregistrer**.

	![](./media/mobile-services-javascript-backend-register-windows-store-app/mobile-services-win8-app-name.png)

	Un nouvel enregistrement Windows Store est créé pour votre application.

3.  Dans Visual Studio 2012 Express pour Windows 8, ouvrez le projet que vous avez créé lorsque vous avez suivi le didacticiel [Prise en main de Mobile Services](/en-us/documentation/articles/mobile-services-windows-store-get-started/).

4.  Dans l'Explorateur de solutions, cliquez avec le bouton droit sur le projet, cliquez sur **Store**, puis sur **Associer l'application au Windows Store...**.

	![](./media/mobile-services-javascript-backend-register-windows-store-app/mobile-services-store-association.png)

	L'Assistant **Associer votre application au Windows Store** s'affiche.

5.  Dans l'Assistant, cliquez sur **Se connecter**, puis connectez-vous avec votre compte Microsoft.

6.  Sélectionnez l'application enregistrée à l'étape 2, puis cliquez sur **Suivant** et sur **Associer**.

	![](./media/mobile-services-javascript-backend-register-windows-store-app/mobile-services-select-app-name.png)

	Les informations d'enregistrement Windows Store requises sont ajoutées au manifeste de l'application.    

7.  De retour sur la page du centre de développement Windows de la nouvelle application, cliquez sur **Services**.

	![](./media/mobile-services-javascript-backend-register-windows-store-app/mobile-services-win8-edit-app.png) 

8.  Sur la page Services, cliquez sur **Live Services site** sous **Azure Mobile Services**.

	![](./media/mobile-services-javascript-backend-register-windows-store-app/mobile-services-win8-edit2-app.png)

9.  Cliquez sur **Authentification de votre service** et notez les valeurs des options **Clé secrète client** et **Identificateur de sécurité (SID) du package**.

	![](./media/mobile-services-javascript-backend-register-windows-store-app/mobile-services-win8-app-push-auth.png)

    **Remarque relative à la sécurité**

    La clé secrète client et le SID du package sont des informations d'identification de sécurité importantes. Ne partagez pas ces informations secrètes avec quiconque et ne les distribuez pas avec votre application.

10.  Connectez-vous au [portail de gestion Azure](https://manage.windowsazure.com/), cliquez sur **Mobile Services**, puis sur l'application.

	![](./media/mobile-services-javascript-backend-register-windows-store-app/mobile-services-selection.png)

11.  Dans l'onglet **Push**, cliquez sur **Activer la transmission push améliorée**, puis cliquez sur **Oui** pour accepter la modification de la configuration.

    ![](./media/mobile-services-javascript-backend-register-windows-store-app/mobile-enable-enhanced-push.png)

    La configuration est ainsi mise à jour sur votre service mobile afin d'utiliser la fonctionnalité de notification Push améliorée fournie par Notification Hubs. Votre service mobile payant vous donne gratuitement accès à une partie de Notification Hubs. Pour plus d'informations, consultez la rubrique [Mobile Services - Tarification](http://go.microsoft.com/fwlink/p/?LinkID=311786).

    **Important**

    Cette opération réinitialise vos informations d'identification Push et modifie le comportement des méthodes Push dans vos scripts. Ces modifications sont irréversibles. N'utilisez pas cette méthode pour ajouter un concentrateur de notification à un service mobile de production. Pour plus d'informations sur l'activation de notifications Push améliorées dans un service mobile de production, consultez [ce guide](http://go.microsoft.com/fwlink/p/?LinkId=391951).

12.  Entrez les valeurs **Clé secrète client** et **SID du package** obtenues via WNS à l'étape 4, puis cliquez sur **Enregistrer**.

	![](./media/mobile-services-javascript-backend-register-windows-store-app/mobile-push-tab.png)

    >[WACOM.NOTE]Lorsque vous définissez vos informations d'identification WNS pour les notifications Push améliorées dans l'onglet **Push** du portail, celles-ci sont partagées avec les concentrateurs de notification pour configurer le concentrateur de notification avec votre application.
