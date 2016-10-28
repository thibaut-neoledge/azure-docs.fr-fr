<properties 
	pageTitle="Utilisation du Kit de développement logiciel (SDK) .NET pour accéder aux API du service Azure Mobile Engagement" 
	description="Décrit comment utiliser le Kit de développement logiciel (SDK) .NET Mobile Engagement pour accéder aux API du service Azure Mobile Engagement"		
	services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="piyushjo" 
	manager="erikre" 
	editor="" />

<tags 
	ms.service="mobile-engagement" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-multiple" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="08/19/2016" 
	ms.author="piyushjo" />

#Utilisation du Kit de développement logiciel (SDK) .NET pour accéder aux API du service Azure Mobile Engagement

Azure Mobile Engagement expose un ensemble d’API pour vous permettre de gérer des appareils, des campagnes Push/Reach, etc. Pour interagir avec ces API, nous vous fournissons également un [fichier Swagger](https://github.com/Azure/azure-rest-api-specs/blob/master/arm-mobileengagement/2014-12-01/swagger/mobile-engagement.json) que vous pouvez utiliser avec des outils pour générer des Kits de développement logiciel (SDK) pour votre langue par défaut. Nous vous recommandons d’utiliser l’outil [AutoRest](https://github.com/Azure/AutoRest) pour générer votre Kit de développement logiciel (SDK) à partir de notre fichier Swagger.

Nous avons créé un Kit de développement logiciel (SDK) .NET de la même manière qui vous permet d’interagir avec ces API à l’aide d’un wrapper C#. Vous n’êtes pas obligé d’effectuer la négociation de jeton d'authentification et l’actualisation vous-même.

Cet exemple parcourt l’ensemble des étapes à suivre pour utiliser le Kit de développement logiciel (SDK) .NET :

1. Tout d’abord, vous devez configurer l’authentification pour vos API à l’aide d’Azure Active Directory, comme décrit [ici](mobile-engagement-api-authentication.md#authentication). À la fin de ces étapes, vous devriez avoir des valeurs **SubscriptionId**, **TenantId**, **ApplicationId** et **Secret** valides.

2. Nous allons utiliser une application console Windows simple pour illustrer l’utilisation du Kit de développement logiciel (SDK) .NET avec le scénario de création d’une campagne d’annonces. Donc, ouvrez Visual Studio et créez une **application console**.

3. Ensuite, vous devez télécharger le Kit de développement logiciel (SDK) .NET disponible en tant que **Bibliothèque de gestion Microsoft Azure Engagement** dans la galerie Nuget [ici](https://www.nuget.org/packages/Microsoft.Azure.Management.Engagement/). Si vous installez Nuget à partir de Visual Studio, vous devez vous assurer que l’option **Inclure la version préliminaire** est activée lors de la recherche du package :

	![][1]

4. Dans le fichier `Program.cs`, ajoutez les espaces de noms suivants :

		using Microsoft.Rest.Azure.Authentication;
		using Microsoft.Azure.Management.Engagement;
		using Microsoft.Azure.Management.Engagement.Models;

5. Ensuite, vous devez définir les constantes suivantes que nous utiliserons pour l’authentification et l’interaction avec l’application Mobile Engagement dans laquelle vous créez la campagne d’annonces :

        // For authentication
        const string TENANT_ID = "<Your TenantId>";
        const string CLIENT_ID = "<Your Application Id>";
        const string CLIENT_SECRET = "<Your Secret>";
        const string SUBSCRIPTION_ID = "<Your Subscription Id>";

        // This is the Azure Resource group concept for grouping together resources 
        //  see here: https://azure.microsoft.com/documentation/articles/resource-group-portal/
        const string RESOURCE_GROUP = "";

        // For Mobile Engagement operations
        // App Collection Name 
        const string APP_COLLECTION_NAME = "";
        // Application Resource Name - make sure you are using the one as specified in the Azure portal (NOT the App Name)
        const string APP_RESOURCE_NAME = "";

6. Définissez la variable `EngagementManagementClient` que nous utiliserons pour appeler les méthodes du Kit de développement logiciel (SDK) Mobile Engagement :

		static EngagementManagementClient engagementClient; 

7. Ajoutez ce qui suit dans votre méthode `Main` :

		try
            {
                // Initialize the Engagement SDK to call out APIs. 
                InitEngagementClient().Wait();

                // Create a Reach campaign
                CreateCampaign().Wait();
            }
            catch (Exception ex)
            {
                Console.WriteLine(ex.InnerException.Message);
                throw ex;
            }

8. Définissez la méthode suivante qui prend en charge l’initialisation de `EngagementManagementClient` en s’authentifiant et s’associant avec l’application Mobile Engagement, dans laquelle vous envisagez de créer la campagne d’annonces :

        private static async Task InitEngagementClient()
        {
            var credentials = await ApplicationTokenProvider.LoginSilentAsync(TENANT_ID, CLIENT_ID, CLIENT_SECRET);
            engagementClient = new EngagementManagementClient(credentials) { SubscriptionId = SUBSCRIPTION_ID };
            
            // This is the Azure concept of ResourceGroup
            engagementClient.ResourceGroupName = RESOURCE_GROUP;

            // This is your Mobile Engagement App Collection & App Resource Name in which you create the campaign
            engagementClient.AppCollection = APP_COLLECTION_NAME;
            engagementClient.AppName = APP_RESOURCE_NAME;
        }

	> [AZURE.IMPORTANT] Notez que vous devez utiliser le **nom de la ressource d’application** tel que défini dans le portail de gestion Azure pour le paramètre AppName.

9. Enfin, définissez la méthode CreateCampaign qui se charge de l’utilisation du client EngagementClient précédemment initialisé pour créer une campagne **AnyTime** et **Notification-only** simple avec un titre et un message :

        private async static Task CreateCampaign()
        {
            //  Refer to the Announcement Campaign format from here - 
            //      https://msdn.microsoft.com/fr-FR/library/azure/mt683751.aspx
            // Make sure you are passing all the non-optional parameters
            Campaign parameters = new Campaign(
                name:"WelcomeCampaign",
                notificationTitle: "Welcome", 
                notificationMessage: "Thank you for installing the app!",
                type:"only_notif",
                deliveryTime:"any"
                );

            // Refer to the Campaign Kinds from here - https://msdn.microsoft.com/fr-FR/library/azure/mt683742.aspx
            CampaignStateResult result = 
                await engagementClient.Campaigns.CreateAsync(CampaignKinds.Announcements, parameters);
            Console.WriteLine("Campaign Id '{0}' was created successfully and it is in '{1}' state", result.Id, result.State);
        }

10. Exécutez l’application console. Les éléments suivants doivent s’afficher lors de la création réussie de la campagne :

	**Campaign Id '159' was created successfully and it is in 'draft' state**

<!-- Images. -->

[1]: ./media/mobile-engagement-dotnet-sdk-service-api/include-prerelease.png

<!---HONumber=AcomDC_0824_2016-->