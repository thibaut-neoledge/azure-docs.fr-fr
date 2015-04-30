La dernière étape de ce didacticiel consiste à générer et à exécuter votre nouvelle application.

1. Accédez à l'emplacement où vous avez enregistré les fichiers projet compressés, puis décompressez les fichiers sur votre ordinateur dans le répertoire de projets d'Android Studio.

2. Ouvrez Android Studio. Si vous travaillez avec un projet et qu'il s'affiche, fermez le projet (Fichier => Fermer le projet).

3. Sélectionnez **Ouvrir un projet Android Studio**, accédez à l'emplacement du projet, puis cliquez sur **OK**. 

 	![](./media/mobile-services-android-get-started/android-studio-import-project.png)

4. Dans la fenêtre de gauche de l'**Explorateur de projets**, assurez-vous que l'onglet *Project* est sélectionné, puis ouvrez **Application**, **src**, **java** et double-cliquez sur **ToDoactivity**,

   	![](./media/mobile-services-android-get-started/Android-Studio-quickstart.png)


5. Si vous avez téléchargé la version 2.0 du Kit de développement logiciel (SDK), vous devez mettre à jour le code avec l'URL et la clé de votre service mobile :
	- 	Recherchez la méthode **OnCreate** dans **TodoActivity.java** et recherchez le code qui instancie le client des services mobiles. Le code est visible dans l'image précédente.
	- 	Remplacez " MobileServiceUrl " par l'URL réelle de votre service mobile.
	- 	Remplacez " AppKey " par la clé de votre service mobile.
	- 	Pour plus d'informations, consultez le didacticiel [Ajouter des Services mobiles à une application existante](../articles/mobile-services-android-get-started-data.md). 

6. Dans le menu **Exécuter**, cliquez sur **Exécuter** pour démarrer le projet dans l'émulateur Android.

	> [AZURE.IMPORTANT] Afin d'exécuter le projet dans l'émulateur Android, vous devez définir au moins un AVD (appareil virtuel Android). Utilisez le gestionnaire AVD pour créer et gérer ces appareils.

7. Dans l'application, tapez un texte explicite, par exemple _Terminer le didacticiel_, puis cliquez sur **Ajouter**.

   	![][10]

   	Ceci envoie une demande POST vers le nouveau service mobile hébergé dans Azure. Les données de la demande sont insérées dans la table TodoItem. Les éléments stockés dans la table sont renvoyés par le service mobile et les données sont affichées dans la liste.

	> [AZURE.NOTE] Vous pouvez vérifier le code qui se trouve dans le fichier ToDoActivity.java et permet d'accéder au service mobile pour exécuter une requête et insérer des données.

8. De retour dans le portail de gestion, cliquez sur l'onglet **Données**, puis sur la table **TodoItems**.

   	![](./media/mobile-services-android-get-started/mobile-data-tab1.png)

   	Cela vous permet de parcourir les données insérées par l'application dans la table.

   	![](./media/mobile-services-android-get-started/mobile-data-browse.png)


<!-- Images. -->
[0]: ./media/mobile-services-android-get-started/mobile-quickstart-completed-android.png
[6]: ./media/mobile-services-android-get-started/mobile-portal-quickstart-android.png
[7]: ./media/mobile-services-android-get-started/mobile-quickstart-steps-android.png
[8]: ./media/mobile-services-android-get-started/Android-Studio-quickstart.png
[10]: ./media/mobile-services-android-get-started/mobile-quickstart-startup-android.png
[11]: ./media/mobile-services-android-get-started/mobile-data-tab.png
[12]: ./media/mobile-services-android-get-started/mobile-data-browse.png
[14]: ./media/mobile-services-android-get-started/android-studio-import-project.png
[15]: ./media/mobile-services-android-get-started/mobile-services-import-android-project.png

<!-- URLs. -->
[Ajouter Mobile Services à une application existante]: ../articles/mobile-services-android-get-started-data.md
[Prise en main de l'authentification]: ../articles/mobile-services-android-get-started-users.md
[Prise en main des notifications Push]: ../articles/mobile-services-javascript-backend-android-get-started-push.md
[Kit de développement logiciel (SDK) Android]: https://go.microsoft.com/fwLink/p/?LinkID=280125
[Android Studio]: https://developer.android.com/sdk/index.html
[Kit de développement logiciel (SDK) Mobile Services pour Android]: https://go.microsoft.com/fwLink/p/?LinkID=266533

[Portail de gestion]: https://manage.windowsazure.com/

<!--HONumber=52-->