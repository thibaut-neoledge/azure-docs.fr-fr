
1. Cliquez sur **App Services**, sélectionnez votre serveur principal d’applications mobiles, et cliquez sur **Démarrage rapide**, puis sur la plateforme cliente (iOS, Android, Xamarin, Cordova).

![Portail Azure avec Démarrage rapide Mobile Apps en surbrillance][quickstart]

2. Si la connexion à la base de données n’est pas configurée, vous devez créer une connexion de données.

![Portail Azure avec connexion de Mobile Apps à la base de données][connect]

  * Créez une base de données SQL Database et un serveur.

  ![Portail Azure avec Mobile Apps créant une base de données et un serveur][server]

  * Attendez que la connexion de données soit créée.

  ![Portail Azure avec notification Mobile Apps lors de la création d’une connexion de données][notification]

  * La connexion de données doit être réussie.

  ![Portail Azure avec notification Mobile Apps lors de la création d’une connexion de données][already-connection]

3. Sous **2. Créer une API de table**, sélectionnez Node.js pour **Langage du serveur principal**. Acceptez l’accusé de réception, puis cliquez sur **Créer une table TodoItem**. Cette action crée une table *TodoItem* dans votre base de données. Le basculement d’un serveur principal existant vers Node.js remplacera tout le contenu du site ! Si vous préférez créer un serveur principal .NET, [suivez ces instructions][instructions].

<!-- Images. -->
[quickstart]: ./media/app-service-mobile-configure-new-backend/quickstart.png
[connect]: ./media/app-service-mobile-configure-new-backend/connect-to-bd.png
[notification]: ./media/app-service-mobile-configure-new-backend/notification-data-connection-create.png
[server]: ./media/app-service-mobile-configure-new-backend/create-new-server.png
[already-connection]: ./media/app-service-mobile-configure-new-backend/already-connection.png

<!-- URLs -->
[instructions]: ../articles/app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#create-app
