<properties
	pageTitle="Authentification de principal du service pour API Apps dans Azure App Service | Microsoft Azure"
	description="Découvrez comment protéger une application API dans Azure App Service pour les scénarios de service à service."
	services="app-service\api"
	documentationCenter=".net"
	authors="tdykstra"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="app-service-api"
	ms.workload="na"
	ms.tgt_pltfrm="dotnet"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="11/28/2015"
	ms.author="tdykstra"/>

# Authentification de principal du service pour API Apps dans Azure App Service

[AZURE.INCLUDE [app-service-api-get-started-selector](../../includes/app-service-api-get-started-selector.md)]

## Vue d’ensemble

Ce didacticiel montre comment utiliser les fonctionnalités d’authentification et d’autorisation d’Azure App Service pour protéger une application API, et comment utiliser une application API protégée au nom d’un compte de service. Le fournisseur d’authentification affiché dans ce didacticiel est Azure Active Directory, et les clients et les API sont des API Web ASP.NET en cours d’exécution dans les applications API.

## Authentification et autorisation dans App Service

Pour obtenir une présentation des fonctions utilisées dans ce didacticiel, consultez le didacticiel précédent de cette série, [Authentification et autorisation pour API Apps dans Azure App Service](app-service-api-authentication.md).

## Procédure pour suivre ce didacticiel

Ce didacticiel s’appuie sur un exemple d’application que vous téléchargez et une application API créée pendant le [premier didacticiel de la série Prise en main d’API Asp et ASP.NET](app-service-api-dotnet-get-started.md).

## Exemple de projet CompanyUsers.API

Dans l’[application exemple ContactsList](https://github.com/Azure-Samples/app-service-api-dotnet-contact-list), le projet CompanyUsers.API est un projet API Web simple qui contient une méthode Get qui retourne une liste de contacts codée en dur. Pour illustrer un scénario de service à service, la méthode Get de ContactsList.API appelle la méthode Get de CompanyContacts.API et ajoute les contacts qu’elle obtient en retour au contenu de son propre magasin de données, puis elle retourne la liste combinée.

Voici la méthode Get dans CompanyUsers.API.

		public async Task<IEnumerable<Contact>> Get()
		{
		    var contacts = new Contact[]{
		                new Contact { Id = 1, EmailAddress = "nancy@contoso.com", Name = "Nancy Davolio"},
		                new Contact { Id = 2, EmailAddress = "alexander@contoso.com", Name = "Alexander Carson"}
		            };
		
		    return contacts;
		}


Et voici la méthode Get dans ContactsList.API, où l’on voit comment elle appelle CompanyContacts.API et ajoute les résultats à ce qu’elle retourne. (Une partie du code a été omise par souci de clarté).

		private async Task<IEnumerable<Contact>> GetContacts()
		{
		    var contacts = await _storage.Get(FILENAME);
		
		    var contactsList = contacts.ToList<Contact>();
		    using (var client = CompanyContactsAPIClientWithAuth())
		    {
		        var results = await client.Contacts.GetAsync();
		        foreach (Contact c in results)
		        {
		            contactsList.Add(c);
		        }
		    }
		
		    return contactsList;
		}

L’objet client retourné par `CompanyContactsAPIClientWithAuth()` dans le code ci-dessus est basé sur le code client généré, mais ajoute un jeton d’autorisation aux demandes HTTP.

		private static CompanyContactsAPI CompanyContactsAPIClientWithAuth()
		{
		    var client = new CompanyContactsAPI(new Uri(ConfigurationManager.AppSettings["CompanyContactsAPIUrl"]));
		    client.HttpClient.DefaultRequestHeaders.Authorization =
		        new AuthenticationHeaderValue("Bearer", ServicePrincipal.GetS2SAccessTokenForProdMSA().AccessToken);
		    return client;
		}

## Créer une application API dans Azure et y déployer le projet CompanyContacts.API

1. Dans l’**Explorateur de solutions**, cliquez avec le bouton droit sur le projet CompanyContacts.API, puis cliquez sur **Publier**.

3.  À l’étape **Profil** de l’Assistant **Publier le site web**, cliquez sur **Microsoft Azure App Service**.

	![](./media/app-service-api-dotnet-service-principal-auth/selectappservice.png)

4. Connectez-vous à votre compte Azure si ce n’est déjà fait, ou actualisez vos informations d’identification si elles ont expiré.

4. Dans la boîte de dialogue **App Service** App Service, choisissez l’**Abonnement** Azure que vous souhaitez utiliser, puis cliquez sur **Nouveau**.

	![](./media/app-service-api-dotnet-service-principal-auth/clicknew.png)

3. Sous l’onglet **Hébergement** de la boîte de dialogue **Créer App Service**, cliquez sur **Modifier le type**, puis sur **Application API**.

4. Entrez un **Nom de l’application API** unique dans le domaine *azurewebsites.net*.

6. Dans la liste déroulante **Groupe de ressources**, sélectionnez le groupe de ressources que vous utilisez pour ces didacticiels.

4. Dans la liste déroulante **Plan de service d’application**, sélectionnez le plan que vous utilisez pour ces didacticiels.

7. Cliquez sur **Create**.

	![](./media/app-service-api-dotnet-service-principal-auth/createappservice.png)

	Visual Studio crée l’application API et crée un profil de publication qui comporte tous les paramètres nécessaires pour la nouvelle application API.

8. Sous l’onglet **Connexion** de l’Assistant **Publier le site web**, cliquez sur **Publier**.

	![](./media/app-service-api-dotnet-service-principal-auth/conntab.png)

	Visual Studio déploie le projet vers la nouvelle application API et ouvre un navigateur à l’URL de l’application API. Une page signalant que la création a réussi s’affiche dans le navigateur.

9. Fermez le navigateur.

## Mettre à jour le code client généré dans le projet ContactsList.API

Le projet ContactsList.API comporte déjà le code client généré, mais vous allez le supprimer et le régénérer à partir de votre propre application API.

1. Dans l’**Explorateur de solutions** de Visual Studio, dans le projet ContactsList.API, supprimez le dossier *CompanyContactsAPI*.

2. Cliquez avec le bouton droit sur le projet ContactsList.API, puis cliquez sur **Ajouter > Client API REST**.

3. Dans la boîte de dialogue **Ajouter un client API REST**, cliquez sur **Télécharger depuis l’application API Microsoft Azure**, puis sur **Parcourir**.

8. Dans la boîte de dialogue **App Service**, développez le groupe de ressources que vous utilisez pour ce didacticiel, puis sélectionnez l’application API que vous venez de créer.

	Si l’application API n’apparaît pas dans la liste, vous avez probablement omis accidentellement l’étape qui consiste à faire passer une application du type web au type API au moment de la création de l’application API. Dans ce cas, vous pouvez créer une nouvelle application API en répétant les opérations que vous avez réalisées précédemment. Vous devez choisir un autre nom pour l’application API, sauf si vous accédez au portail et supprimez d’abord l’application web.

10. Cliquez sur **OK**.

9. Dans la boîte de dialogue **Ajouter un client API REST**, cliquez sur **OK**.

	Visual Studio crée un dossier nommé d’après l’application API et il génère des classes de client.

## Mettre à jour le code dans ContactsList.API et déployer le projet

Le code dans ContactsList.API qui appelle CompanyContacts.API est commenté pour les didacticiels précédents. Dans cette section, vous allez supprimer les marques de commentaire de ce code et déployer l’application.

1. Dans le projet ContactsList.API, ouvrez *Controllers/ContactsController.cs*.

2. Dans la partie supérieure de la classe `ContactsController`, dans le code qui utilise la classe de client générée pour ajouter un jeton d’autorisation, remplacez le nom de classe `CompanyContactsAPI` par le nom de la classe générée à partir de votre application API.

	Par exemple, si votre application API se nomme CompanyContactsAPI3, le code ressemble à ceci :

		 private static CompanyContactsAPI3 CompanyContactsAPIClientWithAuth()
		 {
		     var client = new CompanyContactsAPI3(new Uri(ConfigurationManager.AppSettings["CompanyContactsAPIUrl"]));
		     client.HttpClient.DefaultRequestHeaders.Authorization =
		         new AuthenticationHeaderValue("Bearer", ServicePrincipal.GetS2SAccessTokenForProdMSA().AccessToken);
		     return client;
		 }
 
4. Dans la méthode `Get`, supprimez le commentaire du bloc de code qui appelle CompanyContacts.API.

		using (var client = CompanyContactsAPIClientWithAuth())
		{
		    var results = await client.Contacts.GetAsync();
		    foreach (Contact c in results)
		    {
		        contactsList.Add(c);
		    }
		}

2. Cliquez avec le bouton droit sur le projet ContactsList.API, puis cliquez sur **Publier**.

	L’Assistant **Publier le site web** s’ouvre dans le profil de publication que vous avez déjà utilisé.

3. Dans l’Assistant **Publier le site Web** cliquez sur **Publier**.

	Visual Studio déploie le projet et ouvre une fenêtre de navigateur vers l’URL de base d’application API. Fermez cette fenêtre de navigateur.

## Configurer l’authentification et l’autorisation dans Azure pour la nouvelle application API

1. Dans le [portail Azure](https://portal.azure.com/), accédez au panneau Application API de l’application API que vous avez créée dans ce didacticiel pour le projet CompanyContacts.API, puis cliquez sur **Paramètres**.

2. Recherchez la section **Fonctionnalités**, puis cliquez sur **Authentification/Autorisation**.

3. Dans le panneau **Authentification/Autorisation**, cliquez sur **Activé**.

4. Dans la liste déroulante **Action à effectuer lorsque la demande n’est pas authentifiée**, sélectionnez **Se connecter avec Azure Active Directory**.

5. Sous **Fournisseurs d’authentification**, cliquez sur **Azure Active Directory**.

6. Dans le panneau **Paramètres Azure Active Directory**, cliquez sur **Express**.

	Azure crée automatiquement une application AAD dans votre client AAD. Prenez note du nom de la nouvelle application AAD, car vous le sélectionnerez quand vous accéderez au portail Azure Classic pour obtenir son ID de client.

7. Cliquez sur **OK**.

10. Dans le panneau **Authentification/Autorisation**, cliquez sur **Enregistrer**.
 
11. Dans le [portail Azure Classic](https://manage.windowsazure.com/), accédez à **Azure Active Directory**.

12. Sous l’onglet **Annuaire**, cliquez sur votre client AAD.

14. .Cliquez sur **Applications > Applications que ma société possède**, puis cliquez sur la coche.

15. Dans la liste des applications, cliquez sur le nom de celle qu’Azure a créée pour vous quand vous avez activé l’authentification pour votre application API.

16. Cliquez sur **Configurer**.

17. Laissez cette page ouverte pour pouvoir y copier, coller et mettre à jour des valeurs lors des étapes suivantes du didacticiel.

## Mettre à jour les paramètres dans l’application API qui exécute le code de projet ContactsList.API

3. Dans une autre fenêtre de navigateur, accédez au [portail Azure Classic](https://manage.windowsazure.com/), puis à l’onglet **Configurer** de l’application AAD que vous avez créée pour l’application API ContactsList.API.

5. Sous **Clés**, sélectionnez **1 an** dans la liste déroulante **Sélectionner une durée**.

6. Cliquez sur **Enregistrer**.

	![](./media/app-service-api-dotnet-service-principal-auth/genkey.png)

7. Copiez la valeur de la clé.

	![](./media/app-service-api-dotnet-service-principal-auth/genkeycopy.png)

1. Dans le [portail Azure](https://portal.azure.com/), accédez au panneau Application API de l’application API dans laquelle vous avez déployé le projet ContactsList.API. (Il s’agit de l’application appelante et non de l’application appelée : ContactsList.API, et non CompanyContacts.API.)

2. Cliquez sur **Paramètres > Paramètres de l'application**.

3. Dans la section **Paramètres d’application**, ajoutez une clé nommée « ida:ClientSecret » et, dans le champ de valeur, collez la clé que vous venez de créer.

3. Ajoutez une clé nommée « ida:ClientId » et, dans le champ valeur, collez l’ID client de la même page **Configurer** AAD.

4. Ajoutez une clé nommée « ida:Authority », puis, dans le champ valeur, saisissez « https://login.windows.net/{votre client} » ; par exemple, « https://login.windows.net/contoso.onmicrosoft.com ».

3. Dans le portail Azure Classic, accédez à l’onglet **Configurer** de l’application AAD que vous avez créée pour l’application API CompanyContacts.API.

4. Copiez l’ID client.

3. Dans le panneau **Paramètres d’application** du portail Azure, dans la section **Paramètres de l’application**, ajoutez une clé nommée ida:Resource et, dans le champ de valeur, collez l’ID client que vous venez de copier.

4. Ajoutez une clé nommée « CompanyContactsAPIUrl » et, dans le champ de valeur, saisissez « https://{votre nom d’application api}.azurewebsites.net », par exemple, « https://companycontactsapi.azurewebsites.net. »

6. Cliquez sur Enregistrer.

	![](./media/app-service-api-dotnet-service-principal-auth/appsettings.png)

## Tester dans Azure

1. Accédez à l’URL de l’application web où vous avez déployé le projet ContactsList.Angular.AAD.

2. Cliquez sur l’onglet **Contacts**, puis connectez-vous.

	La page Contacts s’affiche, avec les contacts supplémentaires qui ont été récupérés à partir de l’application API CompanyContacts.API.

	![](./media/app-service-api-dotnet-service-principal-auth/contactspagewithdavolio.png)

Comme dans le didacticiel précédent, vous pouvez également configurer des projets Visual Studio avec les URL SSL de localhost et exécuter l’application localement. Dans ce cas, vous pouvez stocker dans le fichier Web.config les paramètres que vous avez stockés dans Azure pour l’exécution dans Azure (ID client, clé secrète client, etc.). Toutefois, veillez à ne pas archiver le contrôle source du fichier Web.config qui comporte des informations sensibles telles que la clé secrète du client. Pour en savoir plus, consultez la section [Meilleures pratiques portant sur le déploiement de mots de passe et autres données sensibles dans ASP.NET et Azure App Service](http://www.asp.net/identity/overview/features-api/best-practices-for-deploying-passwords-and-other-sensitive-data-to-aspnet-and-azure).

## Protéger l’application API contre tout accès navigateur

Pour ce didacticiel, vous avez utilisé l’option Express dans le portail Azure pour configurer l’authentification AAD pour l’application API que vous souhaitez accéder à l’aide de l’authentification du service principal. Par défaut, le service d’application configure la nouvelle application AAD d’une façon qui permet à un utilisateur d’accéder à l’URL de l’application API dans un navigateur et connectez-vous. Cela signifie qu’il est possible qu’un utilisateur final, et pas seulement le principal du service, accède à l’API. Si vous souhaitez que seul le principal de service ait accès à l’API, vous pouvez empêcher l’accès au navigateur en modifiant l’**URL de réponse** dans l’application AAD afin qu’il soit différent de l’URL d’API d’application.

### Vérifier que l’accès navigateur fonctionne

1. Dans une nouvelle fenêtre de navigateur, accédez à l’URL HTTPS de l’application API que vous avez créée pour le projet CompanyContacts.API.

	Le navigateur accède à un écran de connexion.
	
2. Connectez-vous avec les informations d’identification d’un utilisateur dans votre client AAD.

3. Le navigateur affiche l’écran « créé avec succès » de l’application API.

	Si l’interface Swagger est activée, vous ne pouvez pas accéder à l’URL `/swagger` et appeler l’API. Vous pouvez appeler l’API à partir du navigateur en ajoutant `/api/contacts/get` à l’URL.

### Désactiver l’accès du navigateur

1. Dans l’onglet **Configurer** du portail Classic correspondant à l’application AAD créée pour le projet CompanyContacts.API, modifiez la valeur dans figurant dans le champ du champ **URL de réponse** pour qu’il corresponde à une URL valide, mais pas les URL de l’application API.
 
2. Cliquez sur **Enregistrer**.

### Vérifier que l’accès au navigateur ne fonctionne plus

1. Dans une nouvelle fenêtre de navigateur, accédez de nouveau à l’URL de l’application API.

2. Connectez-vous lorsque vous êtes invité à le faire.

3. La connexion aboutit, mais génère une page d’erreur.

	Il est toujours possible d’accéder à l’application API en utilisant un jeton de principal du service, mais les utilisateurs du client AAD ne peuvent pas se connecter et accéder à l’API depuis un navigateur.

## Étapes suivantes

Il s’agit du dernier de la série de didacticiels de prise en main d’API Apps. Cette section propose des suggestions supplémentaires pour en savoir plus sur le fonctionnement des applications API.

* Autres manières de déployer une application App Service

	Pour plus d’informations sur les autres méthodes de déploiement de projets web sur des applications web, à l’aide de Visual Studio ou en [automatisant le déploiement](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/continuous-integration-and-continuous-delivery) à partir d’un [système de contrôle de code source](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/source-control), consultez [Déploiement d’une application web Azure](web-sites-deploy.md).

	Visual Studio génère également des scripts Windows PowerShell que vous pouvez utiliser pour automatiser le déploiement. Pour en savoir plus, consultez la page [Tout automatiser (développement d’applications de cloud concrètes avec Azure)](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/automate-everything).

* Comment faire pour dépanner une application App Service

	Visual Studio fournit des fonctionnalités qui facilitent l’affichage des journaux Azure durant leur génération en temps réel. Vous pouvez aussi exécuter le mode Debug à distance dans Azure. Pour plus d’informations, consultez la page [Résolution des problèmes des applications web Azure dans Visual Studio](web-sites-dotnet-troubleshoot-visual-studio.md).

* Ajout d’un nom de domaine personnalisé et de SSL

	Pour des informations sur l’utilisation de SSL et de votre propre nom de domaine (par exemple, www.contoso.com au lieu de contoso.azurewebsites.net), consultez les ressources suivantes :

	* [Configuration d’un nom de domaine personnalisé dans Azure App Service](web-sites-custom-domain-name.md)
	* [Activation du protocole HTTPS pour un site web Azure](web-sites-configure-ssl-certificate.md)

<!---HONumber=AcomDC_1217_2015-->