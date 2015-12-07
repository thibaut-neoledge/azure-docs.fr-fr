
1. Dans l'Explorateur de solutions de Visual Studio, cliquez avec le bouton droit sur le projet d'application Windows Store, puis cliquez sur **Store** > **Associer l'application au Windows Store...**. 

    ![Associer une application au Windows Store](./media/app-service-mobile-register-wns/notification-hub-associate-win8-app.png)
    
2. Dans l'Assistant, cliquez sur **Suivant**, connectez-vous à votre compte Microsoft, saisissez un nom pour votre application dans **Réserver un nouveau nom d'application**, puis cliquez sur **Réserver**.

3. Une fois l'inscription de l'application créée, sélectionnez le nouveau nom d'application, cliquez sur **Suivant**, puis sur **Associer**. Cela ajoute les informations d'inscription Windows Store requises au manifeste de l'application.

7. Répétez les étapes 1 et 3 pour le projet d'application Windows Phone Store à l'aide de la même inscription que vous avez créée précédemment pour l'application Windows Store.

7. Accédez au [Centre de développement Windows](https://dev.windows.com/fr-FR/overview), connectez-vous à l'aide de votre compte Microsoft, cliquez sur la nouvelle inscription d'application dans **Mes applications**, puis développez **Services** > **Notifications Push**.

8. Sur la page **Notifications Push**, cliquez sur **Site des services Live** sous **Microsoft Azure Mobile Services**.

9. Dans l'onglet **Paramètres de l'application**, notez les valeurs des options **Clé secrète client** et **SID du package**.

    ![Paramètre d’application dans le centre de développement](./media/app-service-mobile-register-wns/mobile-services-win8-app-push-auth.png)

    > [AZURE.IMPORTANT]La clé secrète client et le SID du package sont des informations d'identification de sécurité importantes. Ne partagez pas ces valeurs avec quiconque et ne les distribuez pas avec votre application.

<!---HONumber=AcomDC_1125_2015-->