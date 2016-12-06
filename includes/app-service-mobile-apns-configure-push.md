

1. Sur votre Mac, démarrez **Trousseau d’accès**. Ouvrez **My Certificates** (Mes certificats) sous **Catégorie** dans la barre de navigation de gauche. Recherchez le certificat SSL que vous avez téléchargé dans la section précédente et publiez son contenu. Sélectionnez uniquement le certificat (sans la clé privée) et [exportez-le](https://support.apple.com/kb/PH20122?locale=en_US).
2. Dans le [portail Azure](https://portal.azure.com/), cliquez sur **Parcourir tout** > **App Services** > votre serveur principal d’application mobile. Sous **Paramètres**, cliquez sur **App Service Push** (Notification Push App Service), puis cliquez sur le nom de votre concentrateur de notifications. Accédez à **Services de notifications Push Apple** > **Télécharger le certificat**. Chargez le fichier .p12 en sélectionnant le **mode** approprié (en fonction du type de votre certificat SSL client précédemment utilisé (Production ou Sandbox)). Enregistrez les modifications.

Votre service est désormais configuré et prêt à fonctionner avec les notifications Push sur iOS !

[1]: ./media/app-service-mobile-apns-configure-push/mobile-push-notification-hub.png


<!--HONumber=Nov16_HO3-->


