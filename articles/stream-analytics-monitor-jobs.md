<properties 
	pageTitle="Surveillance des travaux Stream Analytics | Azure" 
	description="Surveillance des travaux Stream Analytics par programme." 
	services="stream-analytics" 
	documentationCenter="" 
	authors="jeffstokes72" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="stream-analytics" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="data-services" 
	ms.date="05/07/2015" 
	ms.author="jeffstok"/>


# Surveillance des travaux Stream Analytics par programme
Cet article explique comment activer la surveillance d'un travail Stream Analytics. Par défaut, la surveillance n'est pas activée pour les travaux Stream Analytics créés par le biais des API REST, du kit de développement logiciel (SDK) Azure ou de PowerShell. Vous pouvez l'activer manuellement sur le portail Azure. Pour cela, accédez à la page de surveillance du travail et cliquez sur le bouton Activer. Vous pouvez également automatiser ce processus en suivant les étapes décrites dans cet article. Les données de surveillance seront affichées dans l'onglet « Surveiller » du portail Azure pour le travail Stream Analytics.

![Onglet Surveiller les travaux](./media/stream-analytics-monitor-jobs/stream-analytics-monitor-jobs-tab.png)

## Configuration requise
Avant de commencer cet article, vous devez disposer des éléments suivants :

- Visual Studio 2012 ou 2013.
- Téléchargez et installez le [kit de développement logiciel (SDK) Azure .NET](http://azure.microsoft.com/downloads/).
- Un travail Stream Analytics existant pour lequel la surveillance doit être activée.

## Configuration d'un projet

1.	Créez une application console Visual Studio .Net C#.
2.	Dans la console du Gestionnaire de package, exécutez les commandes suivantes pour installer les packages NuGet. Le premier est le Kit de développement logiciel (SDK) .NET de gestion Azure Stream Analytics. Le second est le Kit de développement logiciel (SDK) Azure Insights qui sera utilisé pour activer la surveillance. Le dernier est le client Azure Active Directory utilisé pour l'authentification.

    ```
    Install-Package Microsoft.Azure.Management.StreamAnalytics –Pre
    Install-Package Microsoft.Azure.Insights -Pre
    Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
    ```

3.	Ajoutez la section appSettings suivante au fichier App.config.

    ```
    <appSettings>
    	<!--CSM Prod related values-->
    	<add key="ActiveDirectoryEndpoint" value="https://login.windows-ppe.net/" />
    	<add key="ResourceManagerEndpoint" value="https://api-current.resources.windows-int.net/" />
    	<add key="WindowsManagementUri" value="https://management.core.windows.net/" />
    	<add key="AsaClientId" value="1950a258-227b-4e31-a9cf-717495945fc2" />
    	<add key="RedirectUri" value="urn:ietf:wg:oauth:2.0:oob" />
    	<add key="SubscriptionId" value="<YOUR AZURE SUBSCRIPTION ID>" />
    	<add key="ActiveDirectoryTenantId" value="YOUR TENANT ID" />
    </appSettings>
	```
Remplacez les valeurs de *SubscriptionId* et *ActiveDirectoryTenantId* par votre ID d'abonnement et votre ID de locataire Azure. Vous pouvez obtenir ces valeurs en exécutant l'applet de commande PowerShell suivante :

    ```
    Get-AzureAccount
    ```
4.	Ajoutez les instructions using suivantes au fichier source (Program.cs) dans le projet. 

    ```
        using System;
        using System.Configuration;
        using System.Threading;
        using Microsoft.Azure;
        using Microsoft.Azure.Management.Insights;
        using Microsoft.Azure.Management.Insights.Models;
        using Microsoft.Azure.Management.StreamAnalytics;
        using Microsoft.Azure.Management.StreamAnalytics.Models;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;
    ```
5.	Ajoutez une méthode d'aide pour l'authentification.

        public static string GetAuthorizationHeader()
        	{
        		AuthenticationResult result = null;
        		var thread = new Thread(() =>
        		{
        			try
        			{
            			var context = new AuthenticationContext(
                			ConfigurationManager.AppSettings["ActiveDirectoryEndpoint"] +
                			ConfigurationManager.AppSettings["ActiveDirectoryTenantId"]);

            			result = context.AcquireToken(
                			resource: ConfigurationManager.AppSettings["WindowsManagementUri"],
                			clientId: ConfigurationManager.AppSettings["AsaClientId"],
                			redirectUri: new Uri(ConfigurationManager.AppSettings["RedirectUri"]),
                			promptBehavior: PromptBehavior.Always);
        			}
        			catch (Exception threadEx)
        			{
            			Console.WriteLine(threadEx.Message);
        			}
    			});

    			thread.SetApartmentState(ApartmentState.STA);
    			thread.Name = "AcquireTokenThread";
    			thread.Start();
    			thread.Join();

    			if (result != null)
    			{
        			return result.AccessToken;
    			}

    			throw new InvalidOperationException("Failed to acquire token");
        }

## Création de clients de gestion
Le code suivant définit les variables nécessaires et les clients de gestion.

    string resourceGroupName = "<YOUR AZURE RESOURCE GROUP NAME>";
    string streamAnalyticsJobName = "<YOUR STREAM ANALYTICS JOB NAME>";

    // Get authentication token
    TokenCloudCredentials aadTokenCredentials =
    	new TokenCloudCredentials(
    		ConfigurationManager.AppSettings["SubscriptionId"],
    		GetAuthorizationHeader());

    Uri resourceManagerUri = new
    Uri(ConfigurationManager.AppSettings["ResourceManagerEndpoint"]);

    // Create Stream Analytics and Insights management client
    StreamAnalyticsManagementClient streamAnalyticsClient = new
    StreamAnalyticsManagementClient(aadTokenCredentials, resourceManagerUri);
    InsightsManagementClient insightsClient = new
    InsightsManagementClient(aadTokenCredentials, resourceManagerUri);

## Activation de la surveillance pour un travail Stream Analytics existant

Le code suivant permet d'activer la surveillance pour un travail Stream Analytics **existant**. La première partie du code exécute une requête GET sur le service Stream Analytics pour récupérer des informations sur le travail Stream Analytics spécifique. Elle utilise la propriété « Id » (récupérée à partir de la requête GET) en tant que paramètre pour la méthode Put dans la seconde moitié du code qui envoie une requête PUT au service Insights afin d'activer la surveillance du travail Stream Analytics.

> [AZURE.WARNING]- Si vous avez déjà activé la surveillance pour un travail Stream Analytics différent, au moyen du portail Azure ou par programme avec le code ci-dessous, **nous vous recommandons de fournir le même nom de compte de stockage que celui utilisé lorsque vous avez précédemment activé la surveillance.** - Le compte de stockage est lié à la région dans laquelle vous avez créé le travail Stream Analytics et non pas au travail même. - Tous les travaux Stream Analytics (et toutes les autres ressources Azure) dans cette région partagent ce compte de stockage pour stocker les données de surveillance. Si vous fournissez un autre compte de stockage, ceci pourrait avoir des effets indésirables sur la surveillance des autres travaux Stream Analytics et/ou autres ressources Azure. - Le nom du compte de stockage utilisé pour remplacer ```“<YOUR STORAGE ACCOUNT NAME>”``` ci-dessous doit correspondre à un compte de stockage qui se trouve dans le même abonnement que le travail Stream Analytics pour lequel vous activez la surveillance.

    // Get an existing Stream Analytics job
    JobGetParameters jobGetParameters = new JobGetParameters()
    {
    	PropertiesToExpand = "inputs,transformation,outputs"
    };
    JobGetResponse jobGetResponse = streamAnalyticsClient.StreamingJobs.Get(resourceGroupName, streamAnalyticsJobName, jobGetParameters);

    // Enable monitoring
    ServiceDiagnosticSettingsPutParameters insightPutParameters = new ServiceDiagnosticSettingsPutParameters()
    {
    		Properties = new ServiceDiagnosticSettings()
    		{
        		StorageAccountName = "<YOUR STORAGE ACCOUNT NAME>"
    		}
    };
    insightsClient.ServiceDiagnosticSettingsOperations.Put(jobGetResponse.Job.Id, insightPutParameters);



## Obtenir de l'aide
Pour obtenir une assistance, consultez le [forum Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics).


## Étapes suivantes

- [Présentation d'Azure Stream Analytics](stream-analytics-introduction.md)
- [Prise en main d'Azure Stream Analytics](stream-analytics-get-started.md)
- [Mise à l'échelle des travaux Azure Stream Analytics](stream-analytics-scale-jobs.md)
- [Références sur le langage des requêtes d'Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Références sur l'API REST de gestion d'Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!--HONumber=54-->