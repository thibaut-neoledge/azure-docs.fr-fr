
1. Cliquez sur le **des Services d’application** bouton, sélectionnez votre serveur d’applications mobiles principal, sélectionnez **Quickstart**, puis sélectionnez votre plateforme de client (iOS, Android, Xamarin, Cordova).

    ![Portail Azure avec Mobile Apps Quickstart mis en surbrillance][quickstart]

2. Si une connexion de base de données n’est pas configurée, créez-en un en procédant comme suit :

    ![Portail Azure à connecter des applications mobiles pour la base de données][connect]

    a. Créer une nouvelle base de données SQL et le serveur.

    ![Portail Azure avec des applications mobiles créer le serveur et la nouvelle base de données][server]

    b. Attendez que la connexion de données soit créée.

    ![Notification du portail Azure de création réussie d’une connexion de données][notification]

    c. La connexion de données doit être réussie.

    ![Notification du portail Azure, « vous avez déjà une connexion de données »][already-connection]

3. Sous **2. Créer une API de table**, sélectionnez Node.js pour **Langage du serveur principal**. 
 
4. Accepter l’accusé de réception, puis sélectionnez **table TodoItem de créer**.  
    Cette action crée une nouvelle table d’élément de tâches dans votre base de données. 

    >[!IMPORTANT]
    > Basculement d’un serveur principal existant pour Node.js remplace tout le contenu. Pour créer un serveur principal de .NET au lieu de cela, consultez [fonctionne avec le serveur principal de .NET SDK pour applications mobiles][instructions].

<!-- Images. -->
[quickstart]: ./media/app-service-mobile-configure-new-backend/quickstart.png
[connect]: ./media/app-service-mobile-configure-new-backend/connect-to-bd.png
[notification]: ./media/app-service-mobile-configure-new-backend/notification-data-connection-create.png
[server]: ./media/app-service-mobile-configure-new-backend/create-new-server.png
[already-connection]: ./media/app-service-mobile-configure-new-backend/already-connection.png

<!-- URLs -->
[instructions]: ../articles/app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#create-app
