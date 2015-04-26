
Ce didacticiel est basé sur l'application [GetStartedWithMobileServices](http://go.microsoft.com/fwlink/p/?LinkID=510826), qui est un projet d'application Windows universelle dans Visual Studio 2013. L'interface utilisateur de cette application est identique à l'application générée par le démarrage rapide de Mobile Services, sauf que les éléments ajoutés sont stockés en local dans la mémoire. 

1. Téléchargez la version JavaScript de l'exemple d'application GetStartedWithMobileServices sur le [site d'exemples de code développeur]. 

3. Dans Visual Studio 2013, ouvrez le projet téléchargez, développez le dossier js dans l'Explorateur de solutions, puis examinez le fichier default.js.

   	Notez que les objets **TodoItem** ajoutés sont stockés dans un  `WinJS.Binding.List` en mémoire.

4. Appuyez sur la touche **F5** pour régénérer le projet et démarrer l'application.

5. Dans l'application, tapez du texte dans **Insert a TodoItem**, puis cliquez sur **Enregistrer**.

   	![](./media/mobile-services-windows-universal-dotnet-download-project/mobile-quickstart-startup.png) 

   	Notice that the saved text is displayed.

6. Cliquez avec le bouton droit sur le projet Windows Phone 8.1, cliquez sur **Définir comme projet de démarrage**, puis appuyez sur la touche **F5** pour exécuter l'application Windows Store.  

	![](./media/mobile-services-windows-universal-dotnet-download-project/mobile-quickstart-startup-wp8.png)

7. Répétez les étapes 3 et 4 pour vérifier que l'exemple se comporte de la même manière.


<!--HONumber=42-->
