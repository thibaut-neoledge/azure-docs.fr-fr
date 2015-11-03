<properties
	pageTitle="Prise en main des backends d’applications mobiles pour les applications HTML/JavaScript | Applications Azure App Service Mobile App"
	description="Suivez ce didacticiel pour commencer à utiliser des backends d'applications mobiles Azure pour le développement d'applications web en HTML5 et JavaScript."
	services="app-service\mobile"
	documentationCenter=""
	authors="ggailey777"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-html5"
	ms.devlang="javascript"
	ms.topic="get-started-article"
	ms.date="10/05/2015"
	ms.author="glenga"/>


#Créer une page HTML

[AZURE.INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]&nbsp;[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

##Vue d'ensemble

Ce didacticiel montre comment ajouter un backend cloud à une application web HTML5/JavaScript à l'aide d'un backend Azure Mobile App. Vous allez créer un backend Mobile App et une simple application web *To do list* qui stocke les données d’application dans Azure.

Voici une capture d'écran de l'application terminée :

![Capture d'écran de l'application terminée](./media/app-service-mobile-html-get-started/mobile-quickstart-completed-html.png)

Vous devez suivre ce didacticiel avant de pouvoir suivre les autres didacticiels Mobile App pour les applications HTML.

##Composants requis

Pour réaliser ce didacticiel, vous avez besoin des éléments suivants :

* Un compte Azure actif. Si vous n’avez pas de compte, vous pouvez vous inscrire pour obtenir une version d’évaluation Azure et jusqu’à 10 applications Mobile App gratuites que vous pourrez conserver après l’expiration de votre période d’évaluation. Pour plus d'informations, consultez la page [Version d'évaluation gratuite d'Azure](http://azure.microsoft.com/pricing/free-trial/).

* [Visual Studio Community 2013] ou version ultérieure.

>[AZURE.NOTE]Si vous voulez prendre en main Azure App Service avant de créer un compte Azure, accédez à la page [Essayer App Service](http://go.microsoft.com/fwlink/?LinkId=523751&appServiceName=mobile), où vous pouvez créer immédiatement une première application Mobile App temporaire dans App Service. Aucune carte de crédit n’est requise ; vous ne prenez aucun engagement.

##Création d'un backend Mobile App

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

## Télécharger le projet de serveur

1. Dans le [portail Azure], cliquez sur **Parcourir tout** > **Web Apps**, puis sur le backend Mobile App que vous venez de créer. 

2. Dans le backend Mobile App, cliquez sur **Tous les paramètres** et sous **Mobile App** cliquez sur **Démarrage rapide** > **HTML/JavaScript**.

3. Sous **Télécharger et exécuter votre projet de serveur** dans **Création d'une application**, cliquez sur **Télécharger**, extrayez les fichiers du projet compressés sur votre ordinateur local et ouvrez la solution dans Visual Studio.

4. Développez le projet pour restaurer les packages NuGet.

##Activez les CORS dans le projet de serveur

Le partage des ressources cross-origin (CORS) est un moyen pour votre application web d’indiquer dans quels domaines les demandes sont sécurisées et doivent être autorisées par le navigateur. Vous devez ajouter une entrée CORS pour chaque site web qui accède à votre backend Mobile App. Vous contrôlez vos paramètres CORS en utilisant les comportements standard de l'API web ASP.NET. Pour plus d'informations, consultez la section [Activation des demandes cross-origin dans l'API web ASP.NET](http://www.asp.net/web-api/overview/security/enabling-cross-origin-requests-in-web-api#enable-cors).

Par défaut, le projet de démarrage rapide du client que vous allez télécharger à partir du portail s'exécute sur localhost sur le port 8000. Par conséquent, vous allez ensuite activer CORS pour `http://localhost:8000` dans le projet de serveur.

1. Dans le menu Outils de Visual Studio, cliquez sur **Gestionnaire de Package NuGet** > **Console du gestionnaire de package**, sélectionnez Nuget.org comme **Source du package** et exécutez la commande suivante dans la fenêtre de console :
 
		Install-Package Microsoft.AspNet.WebApi.Cors  

2. Ouvrez le fichier de projet App\_Start/Startup.MobileApp.cs et ajoutez l'instruction using suivante :

		using System.Web.Http.Cors;

3. Ensuite, ajoutez le code suivant à la méthode **Startup.ConfigureMobileApp** une fois **HttpConfiguration** (*config*) créé :

        // Enable CORS support for localhost port 8000, all headers and methods.
        var cors = new EnableCorsAttribute("http://localhost:8000", "*", "*");
        config.EnableCors(cors);

4. Enregistrez vos mises à jour.

Ensuite, vous allez déployer votre projet CORS dans Azure.

##Publier le projet de serveur sur Azure

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-publish-service](../../includes/app-service-mobile-dotnet-backend-publish-service.md)]

##Télécharger et exécuter le projet client

1. Revenez dans le panneau du backend Mobile App, cliquez sur **Tous les paramètres** et sous **Mobile App** cliquez sur **Démarrage rapide** > **HTML/JavaScript**. 

2.  Sous **Télécharger et exécuter votre projet HTML/Javascript** dans **Création d'une application**, cliquez sur **Télécharger** et enregistrez les fichiers du projet compressés sur votre ordinateur local.

3. Accédez à l'emplacement sur lequel vous avez enregistré les fichiers du projet compressés, décompressez-les sur votre ordinateur, puis exécutez l'un des fichiers de commandes suivants à partir du sous-dossier **serveur**.

	+ **launch-windows** (pour les ordinateurs Windows)
	+ **launch-mac.command** (pour les ordinateurs Mac OS X)
	+ **launch-linux.sh** (pour les ordinateurs Linux)

	> [AZURE.NOTE]Sur un ordinateur Windows, appuyez sur la touche `R` quand PowerShell vous demande de confirmer l'exécution du script. Vous pouvez recevoir un avertissement de votre navigateur Web vous recommandant de ne pas exécuter le script, car il a été téléchargé depuis Internet. Lorsque cela se produit, vous devez demander au navigateur de continuer à charger le script.

	Ceci démarre un serveur Web sur votre ordinateur local pour pouvoir héberger la nouvelle application.

4. Ouvrez l'URL <a href="http://localhost:8000/" target="_blank">http://localhost:8000/</a> dans un navigateur web pour démarrer l'application. L'application cliente est configurée pour se connecter à votre backend Mobile App dans Azure.

5. Dans l'application, tapez un texte explicite, comme _Suivre le didacticiel_, dans **Entrer une nouvelle tâche**, puis cliquez sur **Ajouter**.

   	![Exécution de l'application](./media/app-service-mobile-html-get-started/mobile-quickstart-startup-html.png)

   	Ceci envoie une demande POST vers le nouveau backend Mobile App hébergé dans Azure. Les données de la requête sont insérées dans la table TodoItem dans le schéma Mobile App. Les éléments stockés dans la table sont renvoyés par le service et les données sont affichées dans la deuxième colonne de l'application.

	> [AZURE.TIP]Vous pouvez vérifier le code qui se trouve dans le fichier app.js et permet d'accéder au service mobile pour exécuter une requête et insérer des données.

<!-- Anchors. -->
<!-- Images. -->
<!-- URLs. -->
[Get started with authentication]: app-service-mobile-windows-store-dotnet-get-started-users.md
[Mobile App SDK]: http://go.microsoft.com/fwlink/?LinkId=257545
[portail Azure]: https://portal.azure.com/

[Visual Studio Community 2013]: https://www.visualstudio.com/downloads
 

<!---HONumber=Nov15_HO1-->