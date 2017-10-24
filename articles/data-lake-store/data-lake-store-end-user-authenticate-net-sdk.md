---
title: "Authentification des utilisateurs finaux : Kit SDK .NET avec Data Lake Store à l’aide d’Azure Active Directory | Microsoft Docs"
description: "Découvrez comment authentifier les utilisateurs finaux auprès de Data Lake Store à l’aide d’Azure Active Directory avec le Kit SDK .NET"
services: data-lake-store
documentationcenter: 
author: nitinme
manager: cgronlun
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/29/2017
ms.author: nitinme
ms.openlocfilehash: a2a9754b462e39713e6580baab84c064237306a5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="end-user-authentication-with-data-lake-store-using-net-sdk"></a>Authentification des utilisateurs finaux auprès de Data Lake Store avec le Kit SDK .NET
> [!div class="op_single_selector"]
> * [À l’aide de Java](data-lake-store-end-user-authenticate-java-sdk.md)
> * [Utilisation du kit de développement logiciel (SDK) .NET](data-lake-store-end-user-authenticate-net-sdk.md)
> * [Utilisation de Python](data-lake-store-end-user-authenticate-python.md)
> * [Utilisation de l'API REST](data-lake-store-end-user-authenticate-rest-api.md)
> 
>  

Dans cet article, vous allez apprendre à utiliser le Kit SDK .NET pour authentifier les utilisateurs finaux auprès d’’Azure Data Lake Store. Pour plus d’informations sur l’authentification de service à service auprès de Data Lake Store à l’aide du Kit SDK .NET, consultez l’article [Authentification de service à service auprès de Data Lake Store à l’aide du Kit SDK .NET](data-lake-store-service-to-service-authenticate-net-sdk.md).

## <a name="prerequisites"></a>Composants requis
* **Visual Studio 2013, 2015 ou 2017**. Les instructions ci-dessous reposent sur Visual Studio 2017.

* **Un abonnement Azure**. Consultez la rubrique [Obtenir une version d'évaluation gratuite d'Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Créez une application « native » Azure Active Directory**. Vous devez avoir suivi la procédure [Authentification d’utilisateur final auprès de Data Lake Store à l’aide d’Azure Active Directory](data-lake-store-end-user-authenticate-using-active-directory.md).

## <a name="create-a-net-application"></a>Créer une application .NET
1. Ouvrez Visual Studio et créez une application console.
2. Dans le menu **Fichier**, cliquez sur **Nouveau**, puis sur **Projet**.
3. Dans **Nouveau projet**, entrez ou sélectionnez les valeurs suivantes :

   | Propriété | Valeur |
   | --- | --- |
   | Catégorie |Modèles/Visual C#/Windows |
   | Modèle |Application console |
   | Nom |CreateADLApplication |

4. Cliquez sur **OK** pour créer le projet.

5. Ajoutez les packages NuGet à votre projet.

   1. Dans l’Explorateur de solutions, cliquez avec le bouton droit sur le nom du projet, puis cliquez sur **Gérer les packages NuGet**.
   2. Dans l’onglet **Gestionnaire de package NuGet**, vérifiez que **Source du package** a la valeur **nuget.org** et que la case **Inclure la version préliminaire** est cochée.
   3. Recherchez et installez les packages NuGet suivants :

      * `Microsoft.Azure.Management.DataLake.Store` - Ce didacticiel utilise v2.1.3-preview.
      * `Microsoft.Rest.ClientRuntime.Azure.Authentication` - Ce didacticiel utilise v2.2.12.

        ![Ajouter une source NuGet](./media/data-lake-store-get-started-net-sdk/data-lake-store-install-nuget-package.png "Créer un compte Azure Data Lake")
   4. Fermez le **Gestionnaire de package NuGet**.

6. Ouvrez **Program.cs**, supprimez le code existant, puis insérez les instructions suivantes pour ajouter des références aux espaces de noms.

        using System;
        using System.IO;
        using System.Security.Cryptography.X509Certificates; // Required only if you are using an Azure AD application created with certificates
        using System.Threading;

        using Microsoft.Azure.Management.DataLake.Store;
        using Microsoft.Azure.Management.DataLake.Store.Models;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;
        using Microsoft.Rest.Azure.Authentication;

## <a name="end-user-authentication"></a>Authentification de l’utilisateur final
Ajoutez cet extrait de code dans votre application cliente .NET. Remplacez les valeurs d’espace réservé par les valeurs récupérées à partir d’une application native Azure AD (répertoriée comme prérequis). Cet extrait de code vous permet d’authentifier votre application **de manière interactive** auprès de Data Lake Store, ce qui signifie que vous êtes invité à entrer vos informations d’identification Azure.

Pour simplifier l’utilisation, l’extrait de code suivant utilise les valeurs par défaut de l’ID client et l’URI de redirection qui sont valides avec n’importe quel abonnement Azure. Dans l’extrait de code suivant, il vous suffit de fournir la valeur de votre ID de locataire. Vous pouvez récupérer l’ID de locataire en suivant les instructions fournies dans la section [Obtenir l’ID de locataire](../azure-resource-manager/resource-group-create-service-principal-portal.md#get-tenant-id).
    
    private static void Main(string[] args)
    {
        // User login via interactive popup
        // Use the client ID of an existing AAD native application.
        SynchronizationContext.SetSynchronizationContext(new SynchronizationContext());
        var tenant_id = "<AAD_tenant_id>"; // Replace this string with the user's Azure Active Directory tenant ID
        var nativeClientApp_applicationId = "1950a258-227b-4e31-a9cf-717495945fc2";
        var activeDirectoryClientSettings = ActiveDirectoryClientSettings.UsePromptOnly(nativeClientApp_applicationId, new Uri("urn:ietf:wg:oauth:2.0:oob"));
        var creds = UserTokenProvider.LoginWithPromptAsync(tenant_id, activeDirectoryClientSettings).Result;
    }

Voici quelques informations utiles concernant l’extrait de code précédent :

* Pour vous aider à effectuer le didacticiel plus rapidement, l’extrait de code utilise un domaine et un ID client Azure AD qui sont disponibles par défaut pour tous les abonnements Azure. Vous pouvez donc **utiliser cet extrait de code en l’état dans votre application**.
* Cependant, si vous souhaitez utiliser votre propre ID client application et domaine Azure AD, vous devez créer une application native Azure AD, puis utiliser l’ID locataire, l’ID client et l’URI de redirection Azure AD de l’application que vous avez créée. Consultez la page [créer une Application Active Directory pour l’authentification de l’utilisateur final avec Data Lake Store](data-lake-store-end-user-authenticate-using-active-directory.md) pour obtenir des instructions.

  
## <a name="next-steps"></a>Étapes suivantes
Dans cet article, vous avez appris à utiliser l’authentification des utilisateurs finaux auprès d’Azure Data Lake Store avec le Kit SDK .NET. Vous pouvez à présent consulter les articles ci-après, qui expliquent comment utiliser le Kit SDK .NET pour travailler avec Azure Data Lake Store.

* [Opérations de gestion des comptes sur Data Lake Store à l’aide du kit de développement logiciel (SDK) .NET](data-lake-store-get-started-net-sdk.md)
* [Opérations de données sur Data Lake Store à l’aide du kit de développement logiciel (SDK) .NET](data-lake-store-data-operations-net-sdk.md)

