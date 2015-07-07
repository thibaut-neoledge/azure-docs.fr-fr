<properties 
	pageTitle="Utiliser une application API dans Azure App Service à partir d’un client .NET" 
	description="Découvrez comment utiliser une application API à partir d’un client .NET en utilisant le SDK App Service." 
	services="app-service\api" 
	documentationCenter=".net" 
	authors="tdykstra" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service-api" 
	ms.workload="web" 
	ms.tgt_pltfrm="dotnet" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/16/2015" 
	ms.author="tdykstra"/>

# Utiliser une application API dans Azure App Service à partir d’un client .NET 

## Vue d’ensemble

Ce didacticiel montre comment utiliser le Kit de développement logiciel (SDK) App Service pour écrire du code qui appelle une [application API](app-service-api-apps-why-best-platform.md) qui a été configurée pour un niveau d’accès **Public (anonyme)** ou **Public (authentifié)**. L’article aborde les exemples de scénarios suivants :

- Appeler une application API **Publique (anonyme)** à partir d’une application console
- Appeler une application API **Publique (authentifiée)** à partir d’une application de bureau Windows 

Chacune de ces sections du didacticiel sont indépendantes : vous pouvez suivre les instructions pour le deuxième scénario sans avoir effectué les étapes du premier.

Pour en savoir plus sur l’appel d’une application API **interne**, voir [Utiliser une application API interne à partir d’un client .NET](app-service-api-dotnet-consume-internal.md).

## Composants requis

Le didacticiel suppose que vous êtes familiarisé avec la création de projets, l’ajout de code dans Visual Studio et la [gestion des applications API dans le portail Azure en version préliminaire](app-service-api-apps-manage-in-portal.md).

Les exemples de projet et de code de cet article sont basés sur le projet d’application API que vous créez, déployez et protégez dans les articles suivants :

- [Créer une application API](app-service-dotnet-create-api-app.md)
- [Déployer une application API](app-service-dotnet-deploy-api-app.md)
- [Protéger une application API](../app-service-dotnet-add-authentication.md)

[AZURE.INCLUDE [install-sdk-2013-only](../../includes/install-sdk-2013-only.md)]

Ce didacticiel requiert la version 2.6 ou ultérieure du Kit de développement logiciel (SDK) Azure pour .NET.

## Appel non authentifié à partir d’une application console

Dans cette section, vous créez un projet d’application console et vous y ajoutez du code qui appelle une application API ne requérant pas d’authentification.

### Configurer l’application d’API et créer le projet

1. Si vous ne l’avez pas déjà fait, suivez [Déployer une application API](app-service-dotnet-deploy-api-app.md) pour déployer l’exemple de projet ContactsList sur une application API dans votre abonnement Azure.

	Ce didacticiel vous indique comment définir le niveau d’accès dans la boîte de dialogue Publier de Visual Studio à **Disponible pour tout le monde**, qui équivaut à **Public (anonyme)** dans le portail. Cependant, si vous avez effectué après cela le didacticiel [Protéger une application API](../app-service-dotnet-add-authentication.md), le niveau d’accès a été défini sur **Public (authentifié)** et dans ce cas, vous devez le changer comme indiqué à l’étape suivante.

2. Dans le [portail Azure en version préliminaire](https://portal.azure.com/), dans le panneau **Application API** pour l’application API que vous voulez appeler, accédez à **Paramètres > Paramètres de l’application** et définissez **Niveau d’accès** sur **Public (anonyme)**.

	![](./media/app-service-api-dotnet-consume/setpublicanon.png)
 
2. Dans Visual Studio, créez un projet d’application console.
 
### Ajouter du code client généré par le SDK App Service

3. Dans l’**Explorateur de solutions**, cliquez avec le bouton droit sur le projet (pas sur la solution), puis sélectionnez **Ajouter > Client d’application API Azure**. 

	![](./media/app-service-api-dotnet-consume/03-add-azure-api-client-v3.png)
	
3. Dans la boîte de dialogue **Ajouter un client d’application API Azure**, cliquez sur **Télécharger depuis une application API Azure**.

5. Dans la liste déroulante, sélectionnez l’application API que vous voulez appeler.

7. Cliquez sur **OK**.

	![Écran de génération](./media/app-service-api-dotnet-consume/04-select-the-api-v3.png)

	L’Assistant télécharge le fichier de métadonnées de l’API et génère une interface typée pour l’appel de l’application API.

	![Génération en cours](./media/app-service-api-dotnet-consume/05-metadata-downloading-v3.png)

	Une fois la génération de code terminée, un nouveau dossier s’affiche dans l’**Explorateur de solutions**, avec le nom de l’application API. Ce dossier contient le code qui implémente les classes et les modèles de données du client.

	![Génération terminée](./media/app-service-api-dotnet-consume/06-code-gen-output-v3.png)

### Ajouter du code pour appeler l’application API

Pour appeler l’application API, il vous suffit de créer un objet client et d’appeler des méthodes sur cet objet, comme dans cet exemple :

        var client = new ContactList();
        var contacts = client.Contacts.Get();

1. Ouvrez *Program.cs* et ajoutez le code suivant à la méthode `Main` :

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

3. Appuyez sur Ctrl+F5 pour exécuter l’application.

	![Génération terminée](./media/app-service-api-dotnet-consume/consoleappoutput.png)

## Appel authentifié depuis une application de bureau Windows

Dans cette section, vous créez un projet d’application de bureau Windows et vous y ajoutez du code qui appelle une application API requérant une authentification. Ce code implémente le *flux d’authentification serveur* Oauth 2, ce qui signifie que la passerelle d’application API, et non pas l’application cliente, obtient le jeton auprès du fournisseur d’authentification.

Les applications API Azure prennent également en charge le flux d’authentification client. Un scénario d’authentification de flux client sera ajouté ultérieurement à ce didacticiel.

### Configurer l’application API et créer le projet

1. Suivez le didacticiel [Protéger une application API](../app-service-dotnet-add-authentication.md) pour configurer une application API avec le niveau d’accès **Public (authentifié)**.

1. Dans Visual Studio, créez un projet de bureau Windows Forms.

2. Dans le concepteur de formulaire, ajoutez les contrôles suivants :

	* Un contrôle de bouton
	* Un contrôle de zone de texte
	* Un contrôle de navigateur web

3. Définissez le contrôle de zone de texte en multiligne.

	Votre formulaire doit ressembler à l’exemple suivant :

	![](./media/app-service-api-dotnet-consume/form.png)

### Ajouter du code client généré par le SDK App Service

3. Dans l’**Explorateur de solutions**, cliquez avec le bouton droit sur le projet (pas sur la solution), puis sélectionnez **Ajouter > Client d’application API Azure**. 

3. Dans la boîte de dialogue **Ajouter un client d’application API Azure**, cliquez sur **Télécharger depuis une application API Azure**.

5. Dans la liste déroulante, sélectionnez l’application API que vous voulez appeler, puis cliquez sur **OK**.

### Ajouter du code pour appeler l’application API

5. Dans le portail Azure en version préliminaire, copiez l’URL de la passerelle de votre application API. Vous utiliserez cette valeur à l’étape suivante.

	![](./media/app-service-api-dotnet-consume/gatewayurl.png)

4. Dans le code source de *Form1.cs*, ajoutez le code suivant avant le constructeur `Form1()`, en remplaçant la valeur de GATEWAY_URL par la valeur que vous avez copiée à l’étape précédente. Assurez-vous d’inclure la barre oblique (/) à la fin.

		private const string GATEWAY_URL = "https://resourcegroupnameb4f3d966dfa43b6607f30.azurewebsites.net/";
		private const string URL_TOKEN = "#token=";

4. Dans le concepteur de formulaire, double-cliquez sur le bouton pour ajouter un gestionnaire de clics puis, dans la méthode du gestionnaire, ajoutez du code pour accéder à l’URL de connexion de la passerelle, par exemple :

		webBrowser1.Navigate(string.Format(@"{0}login/[authprovider]", GATEWAY_URL));

	Remplacez « [authprovider] » par le code du fournisseur de service d’identité que vous avez configuré dans la passerelle, par exemple : « aad », « twitter », « google », « microsoftaccount » ou « facebook ». Par exemple :

		webBrowser1.Navigate(string.Format(@"{0}login/aad", GATEWAY_URL));

7. Ajoutez un gestionnaire d’événements `DocumentCompleted` pour le contrôle de navigateur web, et ajoutez le code suivant à la méthode du gestionnaire.

		if (e.Url.AbsoluteUri.IndexOf(URL_TOKEN) > -1)
		{
		    var encodedJson = e.Url.AbsoluteUri.Substring(e.Url.AbsoluteUri.IndexOf(URL_TOKEN) + URL_TOKEN.Length);
		    var decodedJson = Uri.UnescapeDataString(encodedJson);
		    var result = JsonConvert.DeserializeObject<dynamic>(decodedJson);
		    string userId = result.user.userId;
		    string userToken = result.authenticationToken;
		
		    var appServiceClient = new AppServiceClient(GATEWAY_URL);
		    appServiceClient.SetCurrentUser(userId, userToken);
		
		    var contactsListClient = appServiceClient.CreateContactsList();
		    var contacts = contactsListClient.Contacts.Get();
		    foreach (Contact contact in contacts)
		    {
		        textBox1.Text += contact.Name + " " + contact.EmailAddress + System.Environment.NewLine;
		    }
		    //appServiceClient.Logout();
		    //webBrowser1.Navigate(string.Format(@"{0}login/aad", GW_URL));
		}

	Le code que vous avez ajouté s’exécute une fois que l’utilisateur se connecte en utilisant le contrôle de navigateur web. Une fois la connexion réussie, l’URL de réponse contient l’ID et le mot de passe de l’utilisateur. Le code extrait ces valeurs à partir de l’URL, les fournit à l’objet client App Service, puis utilise cet objet pour créer un objet client d’application API. Vous pouvez ensuite appeler l’API en appelant des méthodes sur cet objet de client d’application API.

8. Appuyez sur Ctrl+F5 pour exécuter l’application.

9. Cliquez sur le bouton et, quand le contrôle de navigateur affiche une page de connexion, entrez les informations d’identification d’un utilisateur autorisé à appeler l’application API.

	Azure vous authentifie, puis l’application appelle l’application API et affiche la réponse.

	![](./media/app-service-api-dotnet-consume/formaftercall.png)

## Étapes suivantes

Cet article vous a montré comment utiliser une application API à partir d’un client .NET, pour les applications API configurées avec les niveaux d’accès **Public (authentifié)** et **Public (anonyme)**.

Pour d’autres exemples de code appelant une application API à partir de clients .NET, téléchargez l’exemple d’application [Azure Cards](https://github.com/Azure-Samples/API-Apps-DotNet-AzureCards-Sample).
 

<!---HONumber=62-->