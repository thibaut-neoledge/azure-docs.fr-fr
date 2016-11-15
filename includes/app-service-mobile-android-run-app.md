
1. Visitez le [portail Azure]. Cliquez sur **Parcourir tout** > **Applications mobiles** > le serveur principal que vous venez de créer. Dans les paramètres de l’application mobile, cliquez sur **Démarrage rapide** > **Android)**. Sous **Configurer votre application cliente**, cliquez sur **Télécharger**. Un projet Android complet pour une application préconfigurée pour se connecter à votre serveur principal est téléchargé. 
2. Ouvrez le projet avec **Android Studio**, en utilisant **Importer un projet (Eclipse ADT, Gradle, etc.)**. Assurez-vous d’éviter toute erreur JDK avec ce choix d’importation.
3. Appuyez sur le bouton **Exécuter l’application** pour générer le projet et démarrer l’application dans le simulateur Android.
4. Dans l’application, tapez un texte explicite, comme *Suivre le didacticiel* , puis cliquez sur l’icône Ajouter. Cette action envoie une requête POST au serveur principal Azure déployé précédemment. Le backend insère les données de la requête dans la table SQL TodoItem et renvoie des informations sur les éléments récemment stockés à l’application mobile. L’application mobile affiche ces données dans la liste. 
   
    ![](./media/app-service-mobile-android-quickstart/mobile-quickstart-startup-android.png)

[portail Azure]: https://portal.azure.com/


<!--HONumber=Nov16_HO2-->


