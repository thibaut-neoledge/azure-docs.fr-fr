## Générer un client de l'application API 

Les outils de l'application API dans Visual Studio facilitent la génération de code C# qui appelle vos applications API Azure à partir du Bureau, du Store et des applications mobiles.

1. Dans Visual Studio, ouvrez la solution qui contient l’application API de la rubrique [Créer une application API](app-service-dotnet-create-api-app.md). 

2. Dans l’**Explorateur de solutions**, cliquez avec le bouton droit sur la solution et sélectionnez **Ajouter** > **Nouveau projet**.

	![Ajouter un nouveau projet](./media/app-service-dotnet-debug-api-app-gen-api-client/01-add-new-project-v3.png)

3. Dans la boîte de dialogue **Ajouter un nouveau projet**, procédez comme suit :

	1. Sélectionnez la catégorie **Windows Desktop**.
	
	2. Sélectionnez le modèle de projet **Console Application**.
	
	3. Nommez ce projet.
	
	4. Cliquez sur **OK** pour générer le nouveau projet dans votre solution existante.
	
	![Ajouter un nouveau projet](./media/app-service-dotnet-debug-api-app-gen-api-client/02-contact-list-console-project-v3.png)

4. Cliquez avec le bouton droit sur le projet d’application console que vous venez de créer et sélectionnez **Ajouter** > **Client de l’application API Azure**.

	![Ajouter un nouveau client](./media/app-service-dotnet-debug-api-app-gen-api-client/03-add-azure-api-client-v3.png)
	
5. Dans la boîte de dialogue **Ajouter un client d’application API Microsoft Azure**, effectuez les opérations suivantes :

	1. Dans la boîte de dialogue, sélectionnez l’option **Télécharger**. 
	
	2. Dans la liste déroulante, sélectionnez l'application API que vous avez créée précédemment.
	
	3. Cliquez sur **OK**.

	![Écran de génération](./media/app-service-dotnet-debug-api-app-gen-api-client/04-select-the-api-v3.png)

	L'Assistant télécharge le fichier de métadonnées d'API et génère une interface typée pour l'appel de l'application API.

	![Génération en cours](./media/app-service-dotnet-debug-api-app-gen-api-client/05-metadata-downloading-v3.png)

	Une fois la génération de code terminée, un nouveau dossier s'affiche dans l'Explorateur de solutions, avec le nom de l'application API. Ce dossier contient le code qui implémente les modèles de données et de client.

	![Génération terminée](./media/app-service-dotnet-debug-api-app-gen-api-client/06-code-gen-output-v3.png)

6. Ouvrez le fichier **Program.cs** à partir de la racine du projet et remplacez la méthode **Main** par le code suivant :

		static void Main(string[] args)
	    {
	        var client = new ContactsList();
	
	        // Send GET request.
	        var contacts = client.Contacts.Get();
	        foreach (var c in contacts)
	        {
	            Console.WriteLine("{0}: {1} {2}",
	                c.Id, c.Name, c.EmailAddress);
	        }
	
	        // Send POST request.
			client.Contacts.Post(new Models.Contact
		    {
		        EmailAddress = "lkahn@contoso.com",
		        Name = "Loretta Kahn",
		        Id = 4
		    });
	
	        Console.WriteLine("Finished");
	        Console.ReadLine();
	    }

## Test de l’application API

Une fois l’application API codée, il est temps de tester le code.

1. Ouvrez l’**Explorateur de solutions**.

2. Cliquez avec le bouton droit sur l’application console que vous avez créée dans la section précédente.

3. Dans le menu contextuel de l’application console, sélectionnez **Déboguer > Démarrer une nouvelle instance**.

4. Une fenêtre de console doit s’ouvrir et afficher tous les contacts.

	![Exécution de l’application console](./media/app-service-dotnet-debug-api-app-gen-api-client/running-console-app.png)

5. Appuyez sur **Entrée** pour fermer la fenêtre de la console.

<!---HONumber=58-->