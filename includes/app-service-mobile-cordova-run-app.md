
1. Visitez le [portail Azure].
2. Cliquez sur **Parcourir tout** > **Applications mobiles** > le serveur principal que vous avez créé.
3. Dans les paramètres de l’application mobile, cliquez sur **Démarrage rapide** > **Cordova**.
4. Sous **Configurer votre application client**, sélectionnez **Création d'une application**, puis cliquez sur **Télécharger**.
2. Décompressez le fichier ZIP téléchargé dans un répertoire sur votre disque dur, accédez au fichier de solution (.sln) et ouvrez-le à l’aide de Visual Studio.
3. Dans Visual Studio, choisissez la plateforme de solution (Windows, iOS ou Android) dans la liste déroulante en regard de la flèche de démarrage. Sélectionnez un périphérique de déploiement spécifique ou un émulateur en cliquant sur le menu déroulant sur la flèche verte. Vous pouvez utiliser la plateforme Android par défaut et l’émulateur Ripple. Certains didacticiels plus avancés (les notifications push, par exemple) vous demanderont de sélectionner un émulateur ou un périphérique pris en charge.
4. Pour générer et exécuter votre application Cordova, appuyez sur F5 ou cliquez sur la flèche verte. Si vous voyez apparaître dans l’émulateur une boîte de dialogue de sécurité demandant l’accès au réseau, acceptez.
5. Après le démarrage de l’application sur le périphérique ou l’émulateur, tapez un texte explicite dans **Entrer un nouveau texte**, tel que *Suivre le didacticiel*, puis cliquez sur le bouton **Ajouter**.

Le serveur principal insère les données de la requête dans la table SQL TodoItem dans la base de données SQL et renvoie des informations sur les éléments récemment stockés à l’application mobile. L’application mobile affiche ces données dans la liste.

![](./media/app-service-mobile-cordova-quickstart/quickstart-startup.png)

Vous pouvez répéter les étapes 3 à 5 pour les autres plateformes.

[portail Azure]: https://portal.azure.com/


<!--HONumber=Dec16_HO1-->


