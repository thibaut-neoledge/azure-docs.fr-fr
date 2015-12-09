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

Ce didacticiel montre comment utiliser les fonctionnalités d’authentification et d’autorisation d’Azure App Service pour protéger une application API, et comment consommer une application API protégée au nom d’un compte de service. Un compte de service est également appelé *principal du service*, et l’authentification à l’aide de ce compte est également appelée « scénario de *service à service* ». Dans ce didacticiel, vous allez protéger une application API pour un scénario de service à service, en utilisant Azure Active Directory pour l’authentification et la consommation de l’API à partir d’un client .NET.

Le didacticiel utilise l’API Web ASP.NET pour le client appelant et pour l’API appelée, mais les techniques présentées s’appliquent également à d’autres langages et infrastructures pris en charge par Azure App Service. Le code client illustré ici est le code Azure Active Directory standard pour obtenir et passer un jeton du porteur pour un compte de service. Aucun code spécial propre à Azure n’est nécessaire, comme c’était le cas pour la gestion du jeton Zumo Mobile Services.

Il s’agit du quatrième d’une série de didacticiels qui montrent comment utiliser des applications API dans Azure App Service. Pour plus d’informations sur la série, consultez le premier didacticiel intitulé [Prise en main d’API Apps et d’ASP.NET dans Azure App Service](app-service-api-dotnet-get-started.md). Pour plus d’informations sur l’authentification et l’autorisation dans Azure App Service, consultez le didacticiel précédent dans la série, [Authentification utilisateur pour API Apps dans Azure App Service](app-service-api-dotnet-user-principal-auth.md).

## Autres options d’authentification de service à service

Si vous souhaitez gérer un scénario de service à service sans utiliser l’authentification et l’autorisation App Service, par exemple à l’aide de certificats clients, consultez la section [Étapes suivantes](#next-steps).

## Exemple de projet CompanyUsers.API

Dans ce didacticiel, vous allez utiliser les exemples de projets que vous avez téléchargés et les ressources Azure (application API et application web) que vous avez créées dans le [premier didacticiel de cette série](app-service-api-dotnet-get-started.md).

Le projet CompanyUsers.API est un projet API Web simple qui contient une méthode Get qui retourne une liste de contacts codée en dur. Pour illustrer un scénario de service à service, la méthode Get dans ContactsList.API appelle la méthode Get de CompanyContacts.API et ajoute les contacts qu’elle obtient au contenu de son propre magasin de données, puis elle retourne la liste combinée.

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

L’objet client pour CompanyContacts.API est une modification du code client d’application API généré qui ajoute un jeton à la requête HTTP.

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

4. Dans la boîte de dialogue App Service, choisissez l’**Abonnement** Azure que vous souhaitez utiliser, puis cliquez sur **Nouveau**.

	![](./media/app-service-api-dotnet-service-principal-auth/clicknew.png)

3. Sous l’onglet **Hébergement** de la boîte de dialogue **Créer App Service**, cliquez sur **Modifier le type**, puis sur **Application API**.

4. Entrez un **Nom de l’application API** unique dans le domaine *azurewebsites.net*.

6. Dans la liste déroulante **Groupe de ressources**, sélectionnez le groupe de ressources que vous utilisez pour ces didacticiels.

4. Dans la liste déroulante **Plan App Service**, sélectionnez le plan que vous utilisez pour ces didacticiels.

7. Cliquez sur **Créer**.

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
 
4. Dans la méthode Get, supprimez les marques de commentaire du bloc de code qui utilise l’objet client retourné par `CompanyContactsAPIClientWithAuth`.

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

3. Dans l’Assistant **Publier le site web** cliquez sur **Publier**.

## Configurer l’authentification et l’autorisation dans Azure pour la nouvelle application API

1. Dans le [portail Azure](https://portal.azure.com/), accédez au panneau Application API de l’application API que vous avez créée dans ce didacticiel pour le projet CompanyContacts.API, puis cliquez sur **Paramètres**.

2. Recherchez la section **Fonctionnalités**, puis cliquez sur **Authentification / Autorisation**.

3. Dans le panneau **Authentification / Autorisation**, cliquez sur **Activé**.

4. Dans la liste déroulante **Action à effectuer lorsque la demande n’est pas authentifiée**, sélectionnez **Se connecter avec Azure Active Directory**.

5. Sous **Fournisseurs d’authentification**, cliquez sur **Azure Active Directory**.

6. Dans le panneau **Paramètres Azure Active Directory**, cliquez sur **Express**.

	Azure crée automatiquement une application AAD dans votre client AAD. Prenez note du nom de la nouvelle application AAD, car vous le sélectionnerez quand vous accéderez au portail Azure Classic pour obtenir son ID de client.

7. Cliquez sur **OK**.

10. Dans le panneau **Authentification / Autorisation**, cliquez sur **Enregistrer**.
 
11. Dans le [portail Azure Classic](https://manage.windowsazure.com/), accédez à **Azure Active Directory**.

12. Sous l’onglet **Annuaire**, cliquez sur votre client AAD.

14. Cliquez sur **Applications > Applications que ma société possède**, puis cochez la case.

15. Dans la liste des applications, cliquez sur le nom de celle qu’Azure a créée pour vous quand vous avez activé l’authentification pour votre application API.

16. Cliquez sur **Configurer**.

15. En bas de la page, cliquez sur **Gérer le manifeste > Télécharger le manifeste**, puis enregistrez le fichier à un emplacement où vous pouvez le modifier.

16. Dans le fichier manifeste téléchargé, recherchez la propriété `oauth2AllowImplicitFlow`. Modifiez la valeur de cette propriété en remplaçant `false` par `true`, puis enregistrez le fichier.

16. Cliquez sur **Gérer le manifeste > Télécharger le manifeste sur le serveur**, puis chargez le fichier mis à jour à l’étape précédente.

17. Laissez cette page ouverte pour pouvoir y copier, coller et mettre à jour des valeurs lors des étapes suivantes du didacticiel.

## Mettre à jour les paramètres dans l’application API qui exécute le code de projet ContactsList.API

1. Dans le [portail Azure](https://portal.azure.com/), accédez au panneau Application API de l’application API où vous avez déployé le projet ContactsList.API. Il s’agit de l’application API appelante, et non de celle qui est appelée, que vous venez de créer dans ce didacticiel.

2. Cliquez sur **Paramètres > Paramètres de l’application**.

	Vous allez ajouter quelques paramètres, mais vous devez les obtenir à partir d’une autre page dans le portail Azure Classic.

3. Dans le [portail Azure Classic](https://manage.windowsazure.com/), accédez à l’onglet **Configurer** de l’application AAD que vous avez créée pour l’application API ContactsList.API.

5. Sous **Clés**, sélectionnez **1 an** dans la liste déroulante **Sélectionner une durée**.

6. Cliquez sur **Enregistrer**.

	![](./media/app-service-api-dotnet-service-principal-auth/genkey.png)


7. Copiez la valeur de la clé.

	![](./media/app-service-api-dotnet-service-principal-auth/genkeycopy.png)

3. Dans le panneau **Paramètres d’application** du portail Azure, dans la section **Paramètres de l’application**, ajoutez une clé nommée ida:ClientSecret et, dans le champ de valeur, collez la clé que vous venez de copier.

3. Dans le portail Azure Classic, accédez à l’onglet **Configurer** de l’application AAD que vous avez créée pour l’application API CompanyContacts.API.

4. Copiez l’ID client.

3. Dans le panneau **Paramètres d’application** du portail Azure, dans la section **Paramètres de l’application**, ajoutez une clé nommée ida:Resource et, dans le champ de valeur, collez l’ID client que vous venez de copier.

4. Ajoutez une clé nommée CompanyContactsAPIUrl et, dans le champ de valeur, entrez https://{your api app name}.azurewebsites.net, par exemple : https://companycontactsapi.azurewebsites.net.

6. Cliquez sur Enregistrer.

	![](./media/app-service-api-dotnet-service-principal-auth/appsettings.png)

## Tester dans Azure

1. Accédez à l’URL de l’application web où vous avez déployé le projet ContactsList.Angular.AAD.

2. Cliquez sur l’onglet **Contacts**, puis connectez-vous.

	La page Contacts s’affiche, avec les contacts supplémentaires qui ont été récupérés à partir de l’application API CompanyContacts.API.

	![](./media/app-service-api-dotnet-service-principal-auth/contactspagewithdavolio.png)

## Étapes suivantes

Il s’agit du dernier de la série de didacticiels de prise en main d’API Apps. Cette section propose des suggestions supplémentaires pour en savoir plus sur l’utilisation des applications API.

* Autres manières de consommer une application API protégée par l’authentification et l’autorisation Azure App Service.

	Cet article vous a montré comment protéger une application API et l’appeler à partir de code exécuté dans une autre application API. Pour plus d’informations sur la façon d’appeler une application API protégée à partir d’une application logique, consultez [Utilisation de votre API personnalisée hébergée sur App Service avec les applications logiques](../app-service-logic/app-service-logic-custom-hosted-api.md).

* Autres manières de protéger une application API pour les scénarios de service à service.

	En guise d’alternatives à l’authentification et l’autorisation App Service, vous pouvez protéger une application API à l’aide de certificats clients ou de l’authentification de base. Pour plus d’informations sur les certificats clients dans Azure, consultez [Configuration de l’authentification mutuelle TLS pour une application Web](../app-service-web/app-service-web-configure-tls-mutual-auth.md).

* Autres manières de déployer une application App Service

	Pour plus d’informations sur les autres méthodes de déploiement de projets web vers des applications web, à l’aide de Visual Studio ou en [automatisant le déploiement](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/continuous-integration-and-continuous-delivery) à partir d’un [système de contrôle de code source](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/source-control), consultez [Déploiement d’une application web Azure](web-sites-deploy.md).

	Visual Studio génère également des scripts Windows PowerShell que vous pouvez utiliser pour automatiser le déploiement. Pour en savoir plus, consultez la page [Tout automatiser (développement d’applications de cloud concrètes avec Azure)](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/automate-everything).

* Comment faire pour dépanner une application App Service

	Visual Studio fournit des fonctionnalités qui facilitent l’affichage des journaux Azure durant leur génération en temps réel. Vous pouvez aussi exécuter le mode Debug à distance dans Azure. Pour plus d’informations, consultez la page [Résolution des problèmes des applications web Azure dans Visual Studio](web-sites-dotnet-troubleshoot-visual-studio.md).

* Ajout d’un nom de domaine personnalisé et de SSL

	Pour des informations sur l’utilisation de SSL et de votre propre nom de domaine (par exemple, www.contoso.com au lieu de contoso.azurewebsites.net), consultez les ressources suivantes :

	* [Configuration d’un nom de domaine personnalisé dans Azure App Service](web-sites-custom-domain-name.md)
	* [Activation du protocole HTTPS pour un site web Azure](web-sites-configure-ssl-certificate.md)

<!---HONumber=AcomDC_1203_2015-->