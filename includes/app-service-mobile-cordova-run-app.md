
1. Visitez le [portail Azure]. Cliquez sur **Parcourir tout** > **Applications mobiles** > serveur principal que vous venez de créer. Dans les paramètres de l’application mobile, cliquez sur **Démarrage rapide** > **Cordova**. Sous **Configurer votre application client**, sélectionnez **Création d'une application**, puis cliquez sur **Télécharger**. Un projet Cordova complet pour une application préconfigurée pour se connecter à votre serveur principal est téléchargé.

2. Décompressez le fichier ZIP téléchargé dans un répertoire sur votre disque dur.

3. Ouvrez le projet à l’aide de **Visual Studio**. Cliquez sur **Ouvrir** > **Projet/Solution...**.

4. Recherchez le fichier .sln _nom du site_ et cliquez sur **Ouvrir**.

5. L’émulateur par défaut est **Ripple - Nexus (Galaxy)**. Cliquez sur la flèche déroulante en regard de l'émulateur et sélectionnez **Émulateur Android de Google**.

6. Cliquez sur **Émulateur Android de Google**. Le projet va être généré puis exécuté. L’Émulateur Android de Google peut afficher un avertissement de sécurité réseau demandant l’accès au réseau. Enfin, l'Émulateur Android de Google apparaît et votre application s'exécute.

7. Dans l’application, tapez un texte explicite, comme _Suivre le didacticiel_, puis cliquez sur l’icône Ajouter. Cette action envoie une requête POST au serveur principal Azure déployé précédemment. Le backend insère les données de la requête dans la table SQL TodoItem et renvoie des informations sur les éléments récemment stockés à l’application mobile. L’application mobile affiche ces données dans la liste.

    ![](./media/app-service-mobile-cordova-quickstart/quickstart-startup.png)

[portail Azure]: https://portal.azure.com/

<!---HONumber=AcomDC_0211_2016-->