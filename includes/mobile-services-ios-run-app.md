


La dernière étape de ce didacticiel consiste à générer et à exécuter votre nouvelle application.

1. Accédez à l'emplacement où vous avez enregistré les fichiers projet compressés, développez les fichiers sur votre ordinateur et ouvrez le fichier projet à l'aide de Xcode.

   	![](./media/mobile-services-ios-run-app/mobile-xcode-project.png)

2. Appuyez sur le bouton **Exécuter** pour générer le projet et démarrer l'application dans l'émulateur iPhone (configuration par défaut pour ce projet).

3. Dans l'application, tapez un texte explicite, comme _Suivre le didacticiel_, puis cliquez sur l'icône plus (**+**).

   	![](./media/mobile-services-ios-run-app/mobile-quickstart-startup-ios.png)

   Cela envoie une demande POST vers le nouveau service mobile hébergé dans Azure. Les données de la demande sont insérées dans la table TodoItem. Les éléments stockés dans la table sont renvoyés par le service mobile et les données sont affichées dans la liste.

  >[AZURE.NOTE]Dans le fichier TodoService.m, vous pouvez vérifier le code qui permet d'accéder à votre service mobile pour exécuter une requête et insérer des données.</p> 
 	</div>
<!--HONumber=41-->
