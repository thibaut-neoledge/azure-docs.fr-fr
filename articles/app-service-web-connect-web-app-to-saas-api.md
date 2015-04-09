<properties 
   pageTitle="Connexion d'applications mobiles et web aux API SaaS (O365/SharePoint/Salesforce) dans Azure App Service" 
   description="Ce didacticiel explique comment utiliser une application API SaaS depuis une instance d'ASP.NET hébergée dans App Service Web Apps." 
   services="app-service\web" 
   documentationCenter="" 
   authors="syntaxc4" 
   manager="yochayk" 
   editor=""/>

<tags
   ms.service="app-service-web"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="web" 
   ms.date="03/24/2015"
   ms.author="cfowler"/>

# Connexion d'applications mobiles et web aux API SaaS (O365/SharePoint/Salesforce) dans Azure App Service

Ce didacticiel montre comment utiliser une application API depuis une instance d'ASP.NET hébergée dans [AppService Web Apps](http://go.microsoft.com/fwlink/?LinkId=529714).

## Présentation

Vous apprendrez à effectuer les opérations suivantes :

- Comment utiliser une application API depuis une instance d'ASP.NET hébergée dans Web Apps.

## Configuration requise

Ce didacticiel s'appuie sur la série de didacticiels sur les applications API :

1. [Créer une application API Azure](app-service-dotnet-create-api-app)
2. [Publier une application API Azure](app-service-dotnet-publish-api-app)
3. [Déployer une application API Azure](app-service-dotnet-deploy-api-app)
4. [Déboguer une application API Azure](app-service-dotnet-remotely-debug-api-app)

## Rendre l'application API accessible au public

Dans le [portail Azure](http://go.microsoft.com/fwlink/?LinkId=529715), sélectionnez l'application API. Cliquez sur le bouton **Paramètres** dans la barre de commandes. Sous le panneau **Paramètres de base**, définissez le **Niveau d'accès** sur **Public (anonyme)**.

![](./media/app-service-web-connect-web-app-to-saas-api/4-5-Change-Access-Level-To-Public.png)

## Créer une application ASP.NET MVC dans Visual Studio

1. Ouvrez Visual Studio. Dans la boîte de dialogue **Nouveau projet**, ajoutez une nouvelle **Application web ASP.NET**. Cliquez sur **OK**.

	![File > New > Web > ASP.NET Web Application](./media/app-service-web-connect-web-app-to-saas-api/1-Create-New-MVC-App-For-Consumption.png)

1. Sélectionnez le modèle **MVC**. Cliquez sur **Modifier l'authentification**. Sélectionnez **Aucune authentification**, puis cliquez deux fois sur **OK**.

	![New ASP.NET Application](./media/app-service-web-connect-web-app-to-saas-api/2-Change-Auth-To-No-Auth.png)

1. Dans l'Explorateur de solutions, cliquez sur le projet d'application web que vous venez de créer, puis sélectionnez **Ajouter une référence d'application Azure**.

	![Add Azure API App Reference...](./media/app-service-web-connect-web-app-to-saas-api/3-Add-Azure-API-App-SDK.png)

1. Dans la liste déroulante **Applications API existantes**, sélectionnez l'application API à laquelle vous voulez vous connecter.

	![Select Existing API App](./media/app-service-web-connect-web-app-to-saas-api/4-Add-Azure-API-App-SDK-Dialog.png)

	>[AZURE.NOTE] Le code client pour se connecter à l'application API sera automatiquement généré à partir d'un point de terminaison de l'API Swagger.

1. Pour utiliser le code d'API généré, ouvrez le fichier HomeController.cs, puis remplacez l'action  `Contact` par les éléments suivants :

	    public async Task<ActionResult> Contact()
	    {
	        ViewBag.Message = "Your contact page.";
	
	        var contacts = new ContactsList();
	        var response = await contacts.Contacts.GetAsync();
	        var contactList = response.Body;
	
	        return View(contactList);
	    }

	![HomeController.cs Code Updates](./media/app-service-web-connect-web-app-to-saas-api/5-Write-Code-Which-Leverages-Swagger-Generated-Code.png)

1. Mettez à jour la vue  `Contact` refléter la liste dynamique de contacts à l'aide du code suivant :  
	<pre>// Add to the very top of the view file
	@model IList&lt;MyContactsList.Web.Models.Contact&gt;
	
	// Replace the default email addresses with the following
    &lt;h3&gt;Public Contacts&lt;/h3&gt;
    &lt;ul&gt;
        @foreach (var contact in Model)
        {
            &lt;li&gt;&lt;a href=&quot;mailto:@contact.EmailAddress&quot;&gt;@contact.Name &amp;lt;@contact.EmailAddress&amp;gt;&lt;/a&gt;&lt;/li&gt;
        }
    &lt;/ul&gt; 
	</pre>

	![Contact.cshtml Code Updates](./media/app-service-web-connect-web-app-to-saas-api/6-Update-View-To-Reflect-Changes.png)

## Déployer l'application web vers Web Apps dans App Service

Suivez les instructions fournies dans [Comment déployer une application web Azure](web-sites-deploy.md).

>[AZURE.NOTE] Si vous voulez commencer à utiliser Azure App Service avant de créer un compte Azure, cliquez sur [Essayer App Service](http://go.microsoft.com/fwlink/?LinkId=523751). Vous pourrez alors créer instantanément une application web de démarrage dans App Service. Aucune carte de crédit n'est requise, vous ne prenez aucun engagement.

## Nouveautés
* Pour savoir comment passer de Sites Web à App Service, consultez : [Azure App Service et son impact sur les services Azure existants](http://go.microsoft.com/fwlink/?LinkId=529714)
* Pour savoir comment passer de l'ancien au nouveau portail, consultez : [Références sur la navigation dans le portail Azure en version préliminaire](http://go.microsoft.com/fwlink/?LinkId=529715)

<!--HONumber=49-->