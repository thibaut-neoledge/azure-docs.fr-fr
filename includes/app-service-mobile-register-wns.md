
1. Dans l’Explorateur de solutions de Visual Studio, cliquez avec le bouton droit sur le projet d’application Windows Store, puis cliquez sur **Store** > **Associer l’application au Windows Store**.

    ![Associer une application au Windows Store](./media/app-service-mobile-register-wns/notification-hub-associate-win8-app.png)
2. Dans l’Assistant, cliquez sur **Suivant**, puis connectez-vous avec votre compte Microsoft. Tapez un nom pour l’application dans **Réserver un nouveau nom d’application**, puis cliquez sur **Réserver**.
3. Une fois l’inscription de l’application créée, sélectionnez le nouveau nom d’application, cliquez sur **Suivant**, puis sur **Associer**. Cela ajoute les informations d'inscription Windows Store requises au manifeste de l'application.
4. Répétez les étapes 1 et 3 pour le projet d’application Windows Phone Store en utilisant la même inscription que celle que vous avez créée précédemment pour l’application Windows Store.  
5. Accédez au [Centre de développement Windows](https://dev.windows.com/en-us/overview), puis connectez-vous avec votre compte Microsoft. Cliquez sur la nouvelle inscription d’application dans **Mes applications**, puis développez **Services** > **Notifications Push**.
6. Sur la page **Notifications Push**, cliquez sur **Site des services Microsoft Live** sous **Windows Push Notification Services (WNS) et Microsoft Azure Mobile Apps**. Prenez note des valeurs **SID du package** et de la valeur *actuelle* de **Clé secrète d’application**. 

    ![Paramètre d’application dans le centre de développement](./media/app-service-mobile-register-wns/mobile-services-win8-app-push-auth.png)

   > [!IMPORTANT]
   > Le secret d’application et le SID du package sont des informations d'identification de sécurité importantes. Ne partagez pas ces valeurs avec quiconque et ne les distribuez pas avec votre application.
   >
   >


<!--HONumber=Dec16_HO2-->


