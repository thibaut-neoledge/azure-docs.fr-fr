
1. Cliquez sur le bouton **App Services**, sélectionnez les back end Mobile Apps, choisissez **Démarrage rapide** et sélectionnez votre plateforme client (iOS, Android, Xamarin, Cordova).

    ![Portail Azure avec Démarrage rapide Mobile Apps en surbrillance][quickstart]

2. Si une connexion à une base de données n’est pas configurée, créez-en une en procédant comme suit :

    ![Portail Azure avec connexion de Mobile Apps à la base de données][connect]

    a. Créez une base de données SQL Database et un serveur.

    ![Portail Azure avec Mobile Apps créant une base de données et un serveur][server]

    b. Attendez que la connexion de données soit créée.

    ![Notification du portail Azure pour la création réussie d’une connexion de données][notification]

    c. La connexion de données doit être réussie.

    ![Notification du portail Azure, « Vous avez déjà créé une connexion de données »][already-connection]

3. Sous **2. Créer une API de table**, sélectionnez Node.js pour **Langage du serveur principal**. 
 
4. Acceptez l’accusé de réception, puis sélectionnez **Créer une table TodoItem**.  
    Cette action crée une table d’éléments de tâche dans votre base de données. 

    >[!IMPORTANT]
    > Basculer un back end existant vers Node.js remplace tout le contenu. Pour créer un back end .NET, consultez l’article [Travailler avec le SDK d’un serveur principal .NET pour Mobile Apps][instructions].

<!-- Images. -->
[quickstart]: ./media/app-service-mobile-configure-new-backend/quickstart.png
[connect]: ./media/app-service-mobile-configure-new-backend/connect-to-bd.png
[notification]: ./media/app-service-mobile-configure-new-backend/notification-data-connection-create.png
[server]: ./media/app-service-mobile-configure-new-backend/create-new-server.png
[already-connection]: ./media/app-service-mobile-configure-new-backend/already-connection.png

<!-- URLs -->
[instructions]: ../articles/app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#create-app
