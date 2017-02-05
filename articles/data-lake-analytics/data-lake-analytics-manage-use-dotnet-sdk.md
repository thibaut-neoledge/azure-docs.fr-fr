---
title: "Gestion d’Azure Data Lake Analytics à l’aide du SDK Azure .NET | Microsoft Docs"
description: "Apprenez à gérer des travaux Data Lake Analytics, des sources de données, des utilisateurs. "
services: data-lake-analytics
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: 811d172d-9873-4ce9-a6d5-c1a26b374c79
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/23/2016
ms.author: jgao
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 63ce27657ecf1c89f5d3dfc163ee655bc15ce0b7


---
# <a name="manage-azure-data-lake-analytics-using-azure-net-sdk"></a>Gestion d’Azure Data Lake Analytics à l’aide du Kit de développement logiciel (SDK) Azure .NET
[!INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

Apprenez à gérer des comptes Azure Data Lake Analytics, des sources de données, des utilisateurs et des travaux à l’aide du Kit de développement logiciel (SDK) Azure .NET. Pour afficher les rubriques de gestion à l’aide d’autres outils, cliquez sur l’onglet de sélection ci-dessus.

**Configuration requise**

Avant de commencer ce didacticiel, vous devez disposer des éléments suivants :

* **Un abonnement Azure**. Consultez la rubrique [Obtenir une version d'évaluation gratuite d'Azure](https://azure.microsoft.com/pricing/free-trial/).

<!-- ################################ -->
<!-- ################################ -->


## <a name="connect-to-azure-data-lake-analytics"></a>Se connecter à Azure Data Lake Analytics
Les packages Nuget suivants doivent être installés :

    Install-Package Microsoft.Rest.ClientRuntime.Azure.Authentication -Pre
    Install-Package Microsoft.Azure.Common 
    Install-Package Microsoft.Azure.Management.ResourceManager -Pre
    Install-Package Microsoft.Azure.Management.DataLake.Analytics -Pre


L’exemple de code suivant montre comment vous connecter à Azure et répertorier les comptes Data Lake Analytics dans votre abonnement Azure.

    using System;
    using System.Collections.Generic;
    using System.Threading;

    using Microsoft.Rest;
    using Microsoft.Rest.Azure.Authentication;

    using Microsoft.Azure.Management.ResourceManager;
    using Microsoft.Azure.Management.DataLake.Store;
    using Microsoft.Azure.Management.DataLake.Analytics;
    using Microsoft.Azure.Management.DataLake.Analytics.Models;

    namespace ConsoleAcplication1
    {
        class Program
        {

            private const string SUBSCRIPTIONID = "<Enter Your Azure Subscription ID>";
            private const string CLIENTID = "1950a258-227b-4e31-a9cf-717495945fc2";
            private const string DOMAINNAME = "common"; // Replace this string with the user's Azure Active Directory tenant ID or domain name, if needed.

            private static DataLakeAnalyticsAccountManagementClient _adlaClient;

            private static void Main(string[] args)
            {

                var creds = AuthenticateAzure(DOMAINNAME, CLIENTID);

                _adlaClient = new DataLakeAnalyticsAccountManagementClient(creds);
                _adlaClient.SubscriptionId = SUBSCRIPTIONID;

                var adlaAccounts = ListADLAAccounts();

                Console.WriteLine("You have %i Data Lake Analytics account(s).", adlaAccounts.Count);
                for (int i = 0; i < adlaAccounts.Count; i ++)
                {
                    Console.WriteLine(adlaAccounts[i].Name);
                }

                System.Console.WriteLine("Press ENTER to continue");
                System.Console.ReadLine();
            }

            public static ServiceClientCredentials AuthenticateAzure(
            string domainName,
            string nativeClientAppCLIENTID)
            {
                // User login via interactive popup
                SynchronizationContext.SetSynchronizationContext(new SynchronizationContext());
                // Use the client ID of an existing AAD "Native Client" application.
                var activeDirectoryClientSettings = ActiveDirectoryClientSettings.UsePromptOnly(nativeClientAppCLIENTID, new Uri("urn:ietf:wg:oauth:2.0:oob"));
                return UserTokenProvider.LoginWithPromptAsync(domainName, activeDirectoryClientSettings).Result;
            }

            public static List<DataLakeAnalyticsAccount> ListADLAAccounts()
            {
                var response = _adlaClient.Account.List();
                var accounts = new List<DataLakeAnalyticsAccount>(response);

                while (response.NextPageLink != null)
                {
                    response = _adlaClient.Account.ListNext(response.NextPageLink);
                    accounts.AddRange(response);
                }

                return accounts;
            }
        }
    }


## <a name="manage-accounts"></a>Gérer les comptes
Avant d'exécuter des travaux Data Lake Analytics, vous devez avoir un compte Data Lake Analytics. Contrairement à Azure HDInsight, vous ne payez pas pour un compte Analytics lorsque celui-ci n'exécute aucun travail.  Vous payez uniquement lorsqu'il exécute un travail.  Pour plus d'informations, consultez [Présentation d'Azure Data Lake Analytics](data-lake-analytics-overview.md).  

### <a name="create-accounts"></a>Création de comptes
Vous devez disposer d’un groupe Azure Resource Management et d’un compte Data Lake Store avant de pouvoir exécuter l’exemple suivant.

Le code suivant montre comment créer un groupe de ressources :

    public static async Task<ResourceGroup> CreateResourceGroupAsync(
        ServiceClientCredentials credential,
        string groupName,
        string subscriptionId,
        string location)
    {

        Console.WriteLine("Creating the resource group...");
        var resourceManagementClient = new ResourceManagementClient(credential)
        { SubscriptionId = subscriptionId };
        var resourceGroup = new ResourceGroup { Location = location };
        return await resourceManagementClient.ResourceGroups.CreateOrUpdateAsync(groupName, resourceGroup);
    }

Le code suivant montre comment créer un compte Data Lake Store :

    var adlsParameters = new DataLakeStoreAccount(location: _location);
    _adlsClient.Account.Create(_resourceGroupName, _adlsAccountName, adlsParameters);

Le code suivant montre comment créer un compte Data Lake Analytics :

    var defaultAdlsAccount = new List<DataLakeStoreAccountInfo> { new DataLakeStoreAccountInfo(adlsAccountName, new DataLakeStoreAccountInfoProperties()) };
    var adlaProperties = new DataLakeAnalyticsAccountProperties(defaultDataLakeStoreAccount: adlsAccountName, dataLakeStoreAccounts: defaultAdlsAccount);
    var adlaParameters = new DataLakeAnalyticsAccount(properties: adlaProperties, location: location);
    var adlaAccount = _adlaClient.Account.Create(resourceGroupName, adlaAccountName, adlaParameters);

### <a name="list-accounts"></a>Énumérer les comptes
Voir [Se connecter à Azure Data Lake Analytics](#connect_to_azure_data_lake_analytics).

### <a name="find-an-account"></a>Rechercher un compte
Une fois que vous obtenez un objet d’une liste de comptes Data Lake Analytics, vous pouvez utiliser les éléments suivants pour rechercher un compte :

    Predicate<DataLakeAnalyticsAccount> accountFinder = (DataLakeAnalyticsAccount a) => { return a.Name == adlaAccountName; };
    var myAdlaAccount = adlaAccounts.Find(accountFinder);

### <a name="delete-data-lake-analytics-accounts"></a>Supprimer les comptes Data Lake Analytics
L’extrait de code suivant supprime un compte Data Lake Analytics :

    _adlaClient.Account.Delete(resourceGroupName, adlaAccountName);

<!-- ################################ -->
<!-- ################################ -->
## <a name="manage-account-data-sources"></a>Gestion des sources de données du compte
Data Lake Analytics prend actuellement en charge les sources de données suivantes :

* [Azure Data Lake Storage](../data-lake-store/data-lake-store-overview.md)
* [Azure Storage](../storage/storage-introduction.md)

Lorsque vous créez un compte Analytics, vous devez désigner un compte Azure Data Lake Storage comme compte de stockage par défaut. Le compte Data Lake Store par défaut est utilisé pour stocker les métadonnées du travail et les journaux d'audit du travail. Après la création d'un compte Analytics, vous pouvez ajouter des comptes Data Lake Storage et/ou des comptes Azure Storage supplémentaires. 

### <a name="find-the-default-data-lake-store-account"></a>Rechercher le compte Data Lake Store par défaut
Consultez la page Rechercher un compte dans cet article pour rechercher le compte Data Lake Analytics. Ensuite, procédez comme suit :

    string adlaDefaultDataLakeStoreAccountName = myAccount.Properties.DefaultDataLakeStoreAccount;


## <a name="use-azure-resource-manager-groups"></a>Utilisation des groupes Azure Resource Manager
Les applications sont généralement constituées de nombreux composants, par exemple une application web, base de données, serveur de base de données, stockage et services tiers. Azure Resource Manager vous permet de manipuler les ressources de votre application sous la forme d’un groupe, nommé groupe de ressources Azure. Vous pouvez déployer, mettre à jour, surveiller ou supprimer toutes les ressources de votre application dans le cadre d’une opération unique et coordonnée. Vous utilisez un modèle de déploiement pouvant fonctionner avec différents environnements (environnements de test, intermédiaire et de production). Vous pouvez clarifier la facturation pour votre organisation en visualisant les coûts cumulés pour l’ensemble du groupe. Pour plus d'informations, consultez [Présentation d'Azure Resource Manager](../azure-resource-manager/resource-group-overview.md). 

Un service Data Lake Analytics peut inclure les composants suivants :

* Compte Azure Data Lake Analytics
* Compte Azure Data Lake Storage par défaut requis
* Comptes Azure Data Lake Storage supplémentaires
* Comptes Azure Storage supplémentaires

Vous pouvez créer tous ces composants dans un groupe Resource Management pour les rendre plus facile à gérer.

![Compte et stockage Azure Data Lake Analytics](./media/data-lake-analytics-manage-use-portal/data-lake-analytics-arm-structure.png)

Un compte Data Lake Analytics et les compte de stockage dépendants doivent se trouver dans le même centre de données Azure.
Le groupe Resource Management peut cependant se trouver dans un autre centre de données.  

## <a name="see-also"></a>Voir aussi
* [Vue d'ensemble de Microsoft Azure Data Lake Analytics](data-lake-analytics-overview.md)
* [Prise en main de Data Lake Analytics à l’aide du portail Azure](data-lake-analytics-get-started-portal.md)
* [Gestion d’Azure Data Lake Analytics à l’aide du portail Azure](data-lake-analytics-manage-use-portal.md)
* [Surveiller et résoudre les problèmes des tâches Azure Data Lake Analytics à l’aide du portail Azure](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)




<!--HONumber=Nov16_HO3-->


