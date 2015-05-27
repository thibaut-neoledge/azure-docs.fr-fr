<properties 
	pageTitle="Déboguer une application API dans Azure App Service" 
	description="Découvrez comment déboguer une application API dans Azure App Service avec Visual Studio." 
	services="app-service\api" 
	documentationCenter=".net" 
	authors="bradygaster" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service-api" 
	ms.workload="web" 
	ms.tgt_pltfrm="dotnet" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/19/2015" 
	ms.author="bradyg;tarcher"/>

# Déboguer une application API dans Azure App Service 

## Vue d'ensemble

Dans ce didacticiel, vous allez déboguer le code de l’API web ASP.NET lorsqu’elle est exécutée dans une [application API](app-service-api-apps-why-best-platform.md) dans [Azure App Service](app-service-value-prop-what-is.md). Le didacticiel fonctionne avec l’application API que vous avez [créée](app-service-dotnet-create-api-app.md) et [déployée](app-service-dotnet-deploy-api-app.md) dans les didacticiels précédents de cette série.

Vous commencez par utiliser le **client de l’application API** Visual Studio pour générer un code client qui appelle l’application API déployée. Vous déboguez ensuite simultanément l’application client et l’application API, cette dernière étant exécutée en direct dans le cloud.

## Générer un client de l'application API 

Les outils de l'application API dans Visual Studio facilitent la génération de code C# qui appelle vos applications API Azure à partir du Bureau, du Store et des applications mobiles.

1. Dans Visual Studio, ouvrez la solution qui contient l’application API de la rubrique [Créer une application API](app-service-dotnet-create-api-app.md). 

2. Dans l’**Explorateur de solutions**, cliquez avec le bouton droit sur la solution et sélectionnez **Ajouter** > **Nouveau projet**.

	![Ajouter un nouveau projet](./media/app-service-dotnet-remotely-debug-api-app/01-add-new-project-v3.png)

3. Dans la boîte de dialogue **Ajouter un nouveau projet**, procédez comme suit :

	1. Sélectionnez la catégorie **Windows Desktop**.
	
	2. Sélectionnez le modèle de projet **Console Application**.
	
	3. Nommez ce projet.
	
	4. Cliquez sur **OK** pour générer le nouveau projet dans votre solution existante.
	
	![Ajouter un nouveau projet](./media/app-service-dotnet-remotely-debug-api-app/02-contact-list-console-project-v3.png)

4. Cliquez avec le bouton droit sur le projet d’application console que vous venez de créer et sélectionnez **Ajouter** > **Client de l’application API Azure**.

	![Ajouter un nouveau client](./media/app-service-dotnet-remotely-debug-api-app/03-add-azure-api-client-v3.png)
	
5. Dans la boîte de dialogue **Ajouter un client d’application API Microsoft Azure**, effectuez les opérations suivantes :

	1. Dans la boîte de dialogue, sélectionnez l’option **Télécharger**. 
	
	2. Dans la liste déroulante, sélectionnez l'application API que vous avez créée précédemment.
	
	3. Cliquez sur **OK**.

	![Écran de génération](./media/app-service-dotnet-remotely-debug-api-app/04-select-the-api-v3.png)

	L'Assistant télécharge le fichier de métadonnées d'API et génère une interface typée pour l'appel de l'application API.

	![Génération en cours](./media/app-service-dotnet-remotely-debug-api-app/05-metadata-downloading-v3.png)

	Une fois la génération de code terminée, un nouveau dossier s'affiche dans l'Explorateur de solutions, avec le nom de l'application API. Ce dossier contient le code qui implémente les modèles de données et de client.

	![Génération terminée](./media/app-service-dotnet-remotely-debug-api-app/06-code-gen-output-v3.png)

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

Une fois l’application API codée, il est temps de tester le code dans le navigateur.

1. Ouvrez l’**Explorateur de solutions**.

2. Cliquez avec le bouton droit sur l’application console que vous avez créée dans la section précédente.

3. Dans le menu contextuel de l’application console, sélectionnez **Déboguer > Démarrer une nouvelle instance**.

4. Une fenêtre de console doit s’ouvrir et afficher tous les contacts.

	![Exécution de l’application console](./media/app-service-dotnet-remotely-debug-api-app/running-console-app.png)

5. Appuyez sur **Entrée** pour fermer la fenêtre de la console.

## Déboguer l’application API 

Maintenant que l’application API et son client sont codés et testés, nous allons voir comment la déboguer.

1. Dans le menu **Affichage** de Visual Studio, cliquez sur **Explorateur de serveurs**. 

2. Dans **Explorateur de serveurs**, développez le nœud **Azure > App Service**.

3. Recherchez le groupe de ressources que vous avez créé quand vous avez déployé votre application API.

4. Dans ce groupe de ressources, cliquez avec le bouton droit sur le nœud de votre application API et sélectionnez **Attacher le débogueur**.

	![Attachement du débogueur](./media/app-service-dotnet-remotely-debug-api-app/08-attach-debugger-v3.png)

	Le débogueur distant tente de se connecter. Dans certains cas, il est possible que vous deviez cliquer à nouveau sur **Attacher le débogueur** pour établir une connexion ; en cas d’échec, essayez à nouveau.

	![Attachement du débogueur](./media/app-service-dotnet-remotely-debug-api-app/09-attaching-v3.png)

16. Une fois la connexion établie, ouvrez le fichier **ContactsController.cs** dans le projet d’application API, puis ajoutez des points d’arrêt au niveau des méthodes `Get` et `Post`. Ils peuvent apparaître comme inactifs dans un premier temps mais, si le débogueur distant est attaché, vous êtes prêt à déboguer.

	![Application des points d’arrêt au contrôleur](./media/app-service-dotnet-remotely-debug-api-app/10-breakpoints-v3.png)

17. Pour déboguer, cliquez avec le bouton droit sur l’application console dans l’**Explorateur de solutions** et sélectionnez **Déboguer** > **Démarrer une nouvelle instance**. Vous pouvez maintenant déboguer l’application API à distance et l’application cliente localement, et voir l’intégralité du flux de données.

	La capture d’écran suivante montre le débogueur quand il atteint le point d’arrêt de la méthode `Post`. Vous pouvez voir que les données de contact du client ont été désérialisées en objets `Contact` fortement typés.

	![Débogage du client local pour atteindre l’API](./media/app-service-dotnet-remotely-debug-api-app/12-debugging-live-v3.png)

## Étapes suivantes

Le débogage distant pour les applications API vous permet de mieux voir l'exécution du code dans Azure App Service. Des données complètes de diagnostic et de débogage sont disponibles directement dans l’IDE de Visual Studio pour vos applications API Azure.

Les applications API App Service sont des applications web App Service qui présentent des fonctionnalités supplémentaires pour l’hébergement des services web. Vous pouvez donc utiliser les mêmes outils de débogage et de résolution des problèmes pour les applications API que ceux que vous utilisez pour les applications web. Pour plus d’informations, consultez [Dépanner une application web dans Azure App Service à l’aide de Visual Studio](web-sites-dotnet-troubleshoot-visual-studio.md).

L’application API que vous avez créée dans cette série est disponible publiquement pour tous les appelants. Pour plus d’informations sur la façon de protéger l’application API afin que seuls les utilisateurs authentifiés puissent l’appeler, consultez la page [Protéger une application API : ajouter une authentification de fournisseur de réseau social ou Azure Active Directory](app-service-api-dotnet-add-authentication.md).

<!--HONumber=54-->