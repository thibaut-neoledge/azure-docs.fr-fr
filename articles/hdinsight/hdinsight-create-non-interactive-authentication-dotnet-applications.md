---
title: "Créer des applications .NET HDInsight d’authentification non interactives | Microsoft Docs"
description: "Apprenez à créer des applications .NET HDInsight d’authentification non interactives."
editor: cgronlun
manager: jhubbard
services: hdinsight
documentationcenter: 
tags: azure-portal
author: mumian
ms.assetid: 8e32430f-6404-498a-9fcd-f20338d964af
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/22/2017
ms.author: jgao
translationtype: Human Translation
ms.sourcegitcommit: a423975e8a091183154217678706817694f3e346
ms.openlocfilehash: d5256250d6d3a6d7df3a90ae4a0801af131b830e
ms.lasthandoff: 12/21/2016


---
# <a name="create-non-interactive-authentication-net-hdinsight-applications"></a>Créer des applications .NET HDInsight d’authentification non interactives
Vous pouvez exécuter votre application .NET Azure HDInsight sous l’identité de l’application (non interactive) ou sous l’identité de l’utilisateur connecté à l’application (interactive). Pour voir un exemple de l’application interactive, consultez [Se connecter à Azure HDInsight](hdinsight-administer-use-dotnet-sdk.md#connect-to-azure-hdinsight). Cet article vous présente la création d’une application .NET d’authentification non interactive pour se connecter à Azure et gérer HDInsight.

À partir de votre application .NET non interactive, vous avez besoin des éléments suivants :

* Votre ID de locataire d’abonnement Azure (ou ID répertoire). Voir [Obtenir l’ID de locataire](../azure-resource-manager/resource-group-create-service-principal-portal.md#get-tenant-id).
* L’ID client d’application Azure Directory. Voir [Créer une application Active Directory](../azure-resource-manager/resource-group-create-service-principal-portal.md#create-an-active-directory-application) et la rubrique sur [l’obtention d’une ID d’application](../azure-resource-manager/resource-group-create-service-principal-portal.md#get-application-id-and-authentication-key).
* La clé secrète de l’application Azure Active Directory. Voir la rubrique sur [l’obtention de la clé d’authentification de l’application](../azure-resource-manager/resource-group-create-service-principal-portal.md#get-application-id-and-authentication-key).

## <a name="prerequisites"></a>Composants requis
* Cluster HDInsight. Voir le [didacticiel de prise en main](hdinsight-hadoop-linux-tutorial-get-started.md#create-cluster).



## <a name="assign-ad-application-to-role"></a>Affecter l’application Active Directory à un rôle
Vous devez affecter l’application à un [rôle](../active-directory/role-based-access-built-in-roles.md) pour lui accorder les autorisations lui permettant de réaliser des opérations. Vous pouvez définir l’étendue au niveau de l’abonnement, du groupe de ressources ou de la ressource. Les autorisations sont héritées des niveaux inférieurs de l’étendue (par exemple, l’ajout d’une application au rôle Lecteur pour un groupe de ressources signifie qu’elle peut lire le groupe de ressources et toutes les ressources qu’il contient). Dans ce didacticiel, vous allez définir la portée au niveau du groupe de ressources. Pour en savoir plus, consultez [Utiliser les attributions de rôle pour gérer l’accès à vos ressources d’abonnement Azure](../active-directory/role-based-access-control-configure.md).

**Pour ajouter le rôle propriétaire à l’application Active Directory**

1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. Cliquez sur **Groupe de ressources** dans le volet de gauche.
3. Cliquez sur le groupe de ressources qui contient le cluster HDInsight sur lequel vous exécuterez votre requête Hive plus tard dans ce didacticiel. S’il y a trop de groupes de ressources, vous pouvez utiliser le filtre.
4. Dans le panneau du groupe de ressources, cliquez sur **Contrôle d’accès (IAM)**.
5. Dans le panneau **Utilisateurs**, cliquez sur **Ajouter**.
6. Suivez les instructions pour ajouter le rôle **Propriétaire** à l’application Active Directory que vous avez créée dans la dernière procédure. Lorsque la procédure se termine correctement, vous devez voir l’application répertoriée dans le panneau des utilisateurs, avec le rôle de propriétaire.

## <a name="develop-hdinsight-client-application"></a>Développer une application cliente HDInsight

1. Création d’une application console C#.
2. Ajouter les packages Nuget suivants :

        Install-Package Microsoft.Azure.Common.Authentication -Pre
        Install-Package Microsoft.Azure.Management.HDInsight -Pre
        Install-Package Microsoft.Azure.Management.Resources -Pre

3. Utiliser l’exemple de code suivant :

        using System;
        using System.Security;
        using Microsoft.Azure;
        using Microsoft.Azure.Common.Authentication;
        using Microsoft.Azure.Common.Authentication.Factories;
        using Microsoft.Azure.Common.Authentication.Models;
        using Microsoft.Azure.Management.Resources;
        using Microsoft.Azure.Management.HDInsight;
        
        namespace CreateHDICluster
        {
            internal class Program
            {
                private static HDInsightManagementClient _hdiManagementClient;
        
                private static Guid SubscriptionId = new Guid("<Enter Your Azure Subscription ID>");
                private static string tenantID = "<Enter Your Tenant ID (A.K.A. Directory ID)>";
                private static string applicationID = "<Enter Your Application ID>";
                private static string secretKey = "<Enter the Application Secret Key>";
        
                private static void Main(string[] args)
                {
                    var key = new SecureString();
                    foreach (char c in secretKey) { key.AppendChar(c); }

                    var tokenCreds = GetTokenCloudCredentials(tenantID, applicationID, key);
                    var subCloudCredentials = GetSubscriptionCloudCredentials(tokenCreds, SubscriptionId);
        
                    var resourceManagementClient = new ResourceManagementClient(subCloudCredentials);
                    resourceManagementClient.Providers.Register("Microsoft.HDInsight");
        
                    _hdiManagementClient = new HDInsightManagementClient(subCloudCredentials);
        
                    var results = _hdiManagementClient.Clusters.List();
                    foreach (var name in results.Clusters)
                    {
                        Console.WriteLine("Cluster Name: " + name.Name);
                        Console.WriteLine("\t Cluster type: " + name.Properties.ClusterDefinition.ClusterType);
                        Console.WriteLine("\t Cluster location: " + name.Location);
                        Console.WriteLine("\t Cluster version: " + name.Properties.ClusterVersion);
                    }
                    Console.WriteLine("Press Enter to continue");
                    Console.ReadLine();
                }

                /// Get the access token for a service principal and provided key                
                public static TokenCloudCredentials GetTokenCloudCredentials(string tenantId, string clientId, SecureString secretKey)
                {
                    var authFactory = new AuthenticationFactory();
                    var account = new AzureAccount { Type = AzureAccount.AccountType.ServicePrincipal, Id = clientId };
                    var env = AzureEnvironment.PublicEnvironments[EnvironmentName.AzureCloud];
                    var accessToken =
                        authFactory.Authenticate(account, env, tenantId, secretKey, ShowDialog.Never).AccessToken;
        
                    return new TokenCloudCredentials(accessToken);
                }
        
                public static SubscriptionCloudCredentials GetSubscriptionCloudCredentials(SubscriptionCloudCredentials creds, Guid subId)
                {
                    return new TokenCloudCredentials(subId.ToString(), ((TokenCloudCredentials)creds).Token);
                }
            }
        }

## <a name="next-steps"></a>Étapes suivantes
* [Création de l'application Active Directory et du principal du service à l'aide du portail](../azure-resource-manager/resource-group-create-service-principal-portal.md)
* [Authentifier un principal du service à l’aide d’Azure Resource Manager](../azure-resource-manager/resource-group-authenticate-service-principal.md)
* [Contrôle d’accès en fonction du rôle Azure](../active-directory/role-based-access-control-configure.md)

