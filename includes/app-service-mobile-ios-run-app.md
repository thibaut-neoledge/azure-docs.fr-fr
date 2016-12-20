
1. Sur votre Mac, accédez au [portail Azure]. Cliquez sur **Parcourir tout** > **Applications mobiles** > le serveur principal que vous venez de créer. Dans les paramètres de l’application mobile, cliquez sur **Démarrage rapide** > **iOS (Objective-C)**. Si vous préférez Swift, cliquez sur **Démarrage rapide** > **iOS (Swift)**. Sous **Télécharger et exécuter votre projet iOS**, cliquez sur **Télécharger**. Un projet Xcode complet pour une application préconfigurée pour se connecter à votre serveur principal est téléchargé. Ouvrez le projet à l’aide de Xcode.
2. Appuyez sur le bouton **Exécuter** pour générer le projet et démarrer l’application dans le simulateur iOS.
3. Dans l’application, tapez un texte explicite, comme *Suivre le didacticiel* , puis cliquez sur l’icône plus (**+**). Cette action envoie une requête POST au serveur principal Azure déployé précédemment. Le backend insère les données de la requête dans la table SQL TodoItem et renvoie des informations sur les éléments récemment stockés à l’application mobile. L’application mobile affiche ces données dans la liste. 

   ![Application de démarrage rapide en cours d’exécution sur iOS](./media/app-service-mobile-ios-quickstart/mobile-quickstart-startup-ios.png)

[portail Azure]: https://portal.azure.com/


<!--HONumber=Nov16_HO3-->


