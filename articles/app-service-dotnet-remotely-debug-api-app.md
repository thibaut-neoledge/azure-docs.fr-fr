<properties 
	pageTitle="Déboguer à distance une application API dans Azure App Service" 
	description="Utilisez Visual Studio pour déboguer à distance une application API dans Azure App Service." 
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

# Déboguer à distance une application API dans Azure App Service 

## Vue d'ensemble

Les fonctionnalités de débogage distant de Visual Studio ont été étendues pour inclure la prise en charge des applications API dans Azure App Service. Cela signifie que vous pouvez utiliser des outils de débogage familiers pour afficher l'exécution en direct de votre code dans Azure. Cette rubrique illustre l'utilisation du **client de l'application API** Visual Studio pour générer un code client qui appelle l'application API déployée. Vous déboguez ensuite simultanément l'application cliente et l'application API, cette dernière étant exécutée en direct dans le cloud.

Ce didacticiel est le dernier d'une série de trois :

1. Dans [Créer une application API](app-service-dotnet-create-api-app.md), vous avez créé un projet d'application API. 
* Dans [Déployer une application API](app-service-dotnet-deploy-api-app.md), vous avez déployé l'application API dans votre abonnement Azure.
* Dans ce didacticiel, vous utilisez Visual Studio pour déboguer à distance le code pendant son exécution dans Azure.

## Générer un client de l'application API 

Les outils de l'application API dans Visual Studio facilitent la génération de code C# qui appelle vos applications API Azure à partir du Bureau, du Store et des applications mobiles. 

Dans Visual Studio, ouvrez la solution qui contient l'application API à partir du [premier](app-service-dotnet-create-api-app.md) didacticiel. Cliquez avec le bouton droit sur la solution et sélectionnez **Ajouter** > **Nouveau projet**.

![Add a new project](./media/app-service-dotnet-remotely-debug-api-app/01-add-new-project-v3.png)

Sélectionnez la catégorie **Bureau Windows** et le modèle de projet **Application console**.

![Add a new project](./media/app-service-dotnet-remotely-debug-api-app/02-contact-list-console-project-v3.png)

Cliquez avec le bouton droit sur le projet d'application console et sélectionnez **Ajouter** > **Client de l'application API Azure**. 

![Add a new Client](./media/app-service-dotnet-remotely-debug-api-app/03-add-azure-api-client-v3.png)
	
Dans la boîte de dialogue, sélectionnez l'option **Télécharger**. Dans la liste déroulante, sélectionnez l'application API que vous avez créée précédemment. Cliquez sur **OK**. 

![Generation Screen](./media/app-service-dotnet-remotely-debug-api-app/04-select-the-api-v3.png)

L'Assistant télécharge le fichier de métadonnées d'API et génère une interface typée pour l'appel de l'application API.

![Generation Happening](./media/app-service-dotnet-remotely-debug-api-app/05-metadata-downloading-v3.png)

Une fois la génération de code terminée, un nouveau dossier s'affiche dans l'Explorateur de solutions, avec le nom de l'application API. Ce dossier contient le code qui implémente les modèles de données et de client. 

![Generation Complete](./media/app-service-dotnet-remotely-debug-api-app/06-code-gen-output-v3.png)

Ouvrez le fichier **Program.cs** à partir de la racine du projet et remplacez la méthode **Main** par le code suivant : 

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

Dans le menu **Affichage**, sélectionnez **Explorateur de serveurs**. Dans la fenêtre Explorateur de serveurs, développez le nœud *Azure > App Service**. Recherchez le groupe de ressources que vous avez créé quand vous avez déployé votre application API. Cliquez avec le bouton droit sur le nœud de votre application API et sélectionnez **Attacher le débogueur**. 

![Attaching debugger](./media/app-service-dotnet-remotely-debug-api-app/08-attach-debugger-v3.png)

Le débogueur distant tente de se connecter. Dans certains cas, il est possible que vous deviez cliquer à nouveau sur **Attacher le débogueur** pour établir une connexion ; en cas d'échec, essayez à nouveau.

![Attaching debugger](./media/app-service-dotnet-remotely-debug-api-app/09-attaching-v3.png)

Une fois la connexion établie, ouvrez le fichier **ContactsController.cs** dans le projet d'application API, puis ajoutez des points d'arrêt au niveau des méthodes `Get` et `Post`. Ils peuvent apparaître comme inactifs dans un premier temps mais, si le débogueur distant est attaché, vous êtes prêt à déboguer. 

![Applying breakpoints to controller](./media/app-service-dotnet-remotely-debug-api-app/10-breakpoints-v3.png)

Pour déboguer, cliquez avec le bouton droit sur l'application console dans l'Explorateur de solutions et sélectionnez **Déboguer** > **Démarrer une nouvelle instance**. Vous pouvez maintenant déboguer l'application API à distance et l'application cliente localement, et voir l'intégralité du flux de données. 

La capture d'écran suivante montre le débogueur quand il atteint le point d'arrêt pour la méthode  `Post`. Vous pouvez voir que les données de contact du client ont été désérialisées en objet `Contact` fortement typé. 

![Debugging the local client to hit the API](./media/app-service-dotnet-remotely-debug-api-app/12-debugging-live-v3.png)

## Résumé

Le débogage distant pour les applications API vous permet de mieux voir l'exécution du code dans Azure App Service. Des données complètes de diagnostic et de débogage sont disponibles directement dans l'IDE de Visual Studio pour vos applications API Azure exécutées à distance.


<!--HONumber=49-->