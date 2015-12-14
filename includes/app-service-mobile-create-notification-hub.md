Procédez comme suit pour créer un concentrateur de notification pour gérer les notifications Push. Si vous disposez déjà d’un concentrateur de notification, vous pouvez également le connecter au serveur principal de votre application mobile.

1. Dans le [portail Azure], cliquez sur **Parcourir** > **App Services**, puis cliquez sur le serveur principal de votre application mobile > **Tous les paramètres**, puis, sous **Mobile**, cliquez sur **Push** > **Concentrateur de notification**.

2. Cliquez sur **+Concentrateur de notification**, tapez un nouveau nom de **Concentrateur de notification**, qui peut être celui du serveur principal de votre application mobile, tapez un nouveau nom d’espace de noms ou utilisez-en un existant, puis cliquez sur **OK** et sur **Créer**.

	![](./media/app-service-mobile-create-notification-hub/create-new-hub-flow.png)

	Cela crée un concentrateur de notification et connecte celui-ci à votre application mobile. Si vous disposez déjà d’un concentrateur de notification, vous pouvez choisir de le connecter au serveur principal de votre application mobile au lieu d’en créer un.

Vous avez désormais connecté un concentrateur de notification au serveur principal de votre application mobile. Plus tard, vous allez configurer ce concentrateur de notification de façon à ce qu’il se connecte à un service de notification de plateforme (PNS) qui envoie des notifications Push à l’appareil natif.

[portail Azure]: https://portal.azure.com/

<!---HONumber=AcomDC_1203_2015-->