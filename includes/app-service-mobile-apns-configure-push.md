

1. Sur votre Mac, démarrez **Trousseau d’accès**. Ouvrez **Catégorie** > **Mes certificats**. Recherchez le certificat SSL à exporter (que vous avez téléchargé précédemment) et publiez son contenu. Sélectionnez uniquement le certificat sans la clé privée, et [exportez-le](https://support.apple.com/kb/PH20122?locale=en_US).
2. Dans le portail Azure, cliquez sur **Parcourir tout** > **App Services** > votre serveur principal d’applications mobiles > **Paramètres** > **Application mobile** > **Notifications push** > **Configurer les paramètres requis** > **+ concentrateur de notification**, et indiquez le nom et l’espace de noms de votre concentrateur de notification. Cliquez ensuite sur **OK**.
   
      ![][1]
3. Dans le panneau **Créer un concentrateur de notification**, cliquez sur le bouton **Créer**.
   
    Avant de passer à l'étape suivante, cliquez sur **Notifications**, pour vous assurer que votre installation de concentrateur de notification est terminée.
4. Dans le portail Azure, cliquez sur **Parcourir tout** > **Applications mobiles** > votre serveur principal d’applications mobiles > **Paramètres** > **Application mobile** > **Push** > **Services de notification push Apple** > **Télécharger le certificat**. Téléchargez le fichier .p12 en sélectionnant le **Mode** approprié (selon que le certificat SSL client généré précédemment concerne le Développement ou la Distribution.) Votre service est désormais configuré et prêt à fonctionner avec les notifications Push sur iOS !

[1]: ./media/app-service-mobile-apns-configure-push/mobile-push-notification-hub.png

<!---HONumber=AcomDC_1203_2015-->