

1. Sur votre Mac, démarrez **Trousseau d’accès**. Dans la barre de navigation gauche, sous **Catégorie**, ouvrez **Mes certificats**. Recherchez le certificat SSL que vous avez téléchargé dans la section précédente, puis affichez son contenu. Sélectionnez uniquement le certificat (sans la clé privée) et [exportez-le](https://support.apple.com/kb/PH20122?locale=en_US).
2. Dans le [Portail Azure](https://portal.azure.com/), cliquez sur **Parcourir tout** > **App Services**, puis cliquez sur votre serveur principal Mobile Apps. Sous **Paramètres**, cliquez sur **App Service Push (Notification Push App Service)**, puis sur le nom de votre hub de notification. Accédez à **Services de notifications Push Apple** > **Télécharger le certificat**. Chargez le fichier .p12 en sélectionnant le **mode** approprié (en fonction du type de votre certificat SSL client précédemment utilisé : Production ou Sandbox). Enregistrez les modifications.

Votre service est désormais configuré et prêt à fonctionner avec les notifications Push sur iOS.

[1]: ./media/app-service-mobile-apns-configure-push/mobile-push-notification-hub.png


<!--HONumber=Dec16_HO2-->


