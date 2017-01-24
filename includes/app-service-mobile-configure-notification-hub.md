La fonctionnalité Mobile Apps d’Azure App Service utilise [Azure Notification Hubs] pour envoyer des notifications Push ; vous allez donc devoir configurer un hub de notification pour votre application mobile.

1. Dans le [Portail Azure], accédez à **App Services**, puis cliquez sur votre serveur d’applications principal. Sous **Paramètres**, cliquez sur **Push**.
2. Cliquez sur **Connecter** pour ajouter une ressource de concentrateur de notification à l’application. Vous pouvez créer un concentrateur ou vous connecter à un autre existant.

    ![](./media/app-service-mobile-create-notification-hub/configure-hub-flow.png)

Vous avez désormais connecté un hub de notification à votre projet de serveur principal Mobile Apps. Plus tard, vous allez configurer ce concentrateur de notifications de façon qu’il se connecte à un Platform Notification System (PNS) qui envoie des notifications Push aux appareils.

[Portail Azure]: https://portal.azure.com/
[Azure Notification Hubs]: https://azure.microsoft.com/en-us/documentation/articles/notification-hubs-push-notification-overview/


<!--HONumber=Dec16_HO2-->


