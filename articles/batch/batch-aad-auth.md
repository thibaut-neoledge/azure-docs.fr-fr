---
title: "Utiliser Azure Active Directory pour l’authentification à partir d’Azure Batch | Microsoft Docs"
description: "Batch prend en charge Azure AD pour l’authentification auprès du fournisseur de ressources Batch et du service Batch."
services: batch
documentationcenter: .net
author: tamram
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: big-compute
ms.date: 03/16/2017
ms.author: tamram
translationtype: Human Translation
ms.sourcegitcommit: bb1ca3189e6c39b46eaa5151bf0c74dbf4a35228
ms.openlocfilehash: 28665625cde9c7b164986f7b2d9c845136543aba
ms.lasthandoff: 03/18/2017

---

# <a name="authenticate-from-batch-solutions-with-active-directory"></a>Authentification à partir des solutions Batch avec Active Directory

Azure Batch prend en charge l’authentification avec [Azure Active Directory][aad_about] (Azure AD) pour le service Batch et le service de gestion Batch. Azure AD est le service Microsoft de gestion des répertoires et des identités basé sur le cloud mutualisé. Azure lui-même utilise Azure AD pour l’authentification de ses clients, de ses administrateurs de services fédérés et de ses utilisateurs professionnels.

Dans cet article, nous découvrons comment utiliser Azure AD pour l’authentification à partir des applications qui utilisent la bibliothèque .NET de gestion Batch ou la bibliothèque .NET Batch. Dans le contexte des API .NET Batch, nous montrons comment utiliser Azure AD pour authentifier un administrateur ou un coadministrateur d’abonnement avec l’authentification intégrée. L’utilisateur authentifié peut alors émettre des demandes vers Azure Batch.

Il est également possible d’utiliser Azure AD pour authentifier l’accès à une application en cours d’exécution sans assistance. Dans cet article, nous nous concentrons sur l’utilisation de l’authentification intégrée Azure AD et vous renvoyons vers d’autres ressources pour compléter vos connaissances sur l’authentification des applications sans assistance.

## <a name="use-azure-ad-with-batch-management-solutions"></a>Utiliser Azure AD avec les solutions de gestion Batch

La bibliothèque .NET de gestion Batch expose des types pour l’utilisation des packages d’application, des applications, des clés de compte et des comptes Batch. La bibliothèque .NET de gestion Batch est un client de fournisseur de ressources Azure, qui est utilisée avec [Azure Resource Manager][resman_overview] pour gérer ces ressources par programme. 

Azure AD est requis pour authentifier les demandes effectuées via les clients de fournisseur de ressources Azure, y compris la bibliothèque .NET de gestion Batch et via [Azure Resource Manager][resman_overview].

Dans cette section, nous utilisons l’exemple de projet [AccountManagment][acct_mgmt_sample], disponible sur GitHub, pour parcourir la bibliothèque .NET de gestion Batch avec Azure AD. L’exemple AccountManagement est une application console qui accède par programme à un abonnement, crée un groupe de ressources et un compte Batch, et effectue certaines opérations sur le compte. 

Pour en savoir plus sur l’utilisation de la bibliothèque .NET de gestion Batch et l’exemple AccountManagement, consultez [Gérer les quotas et comptes Batch avec la bibliothèque cliente Batch Management pour .NET](batch-management-dotnet.md).

### <a name="register-your-application-with-azure-ad"></a>Inscrire votre application auprès d’Azure AD

La [bibliothèque d’authentification Azure Active Directory][aad_adal] (ADAL) fournit une interface de programmation à Azure AD, que vous pouvez utiliser dans vos applications. Pour appeler ADAL à partir de votre application, vous devez inscrire votre application dans un locataire Azure AD. Lorsque vous inscrivez votre application, vous fournissez des informations relatives à votre application à Azure AD, y compris un nom au sein du locataire Azure AD. Azure AD fournit ensuite un ID d’application que vous utilisez pour associer votre application à Azure AD lors de l’exécution. Pour en savoir plus sur l’ID d’application, consultez [Objets application et principal du service dans Azure Active Directory](../active-directory/develop/active-directory-application-objects.md).

Suivez les étapes de la section [Ajout d’une application](../active-directory/develop/active-directory-integrating-applications.md#adding-an-application) dans [Intégration d’applications dans Azure Active Directory][aad_integrate] pour inscrire l’exemple d’application AccountManagement. Indiquez **Application cliente native** comme type d’application. Pour **l’URI de redirection**, vous pouvez spécifier n’importe quel URI valide (tel que `http://myaccountmanagementsample`), puisqu’il n’est pas nécessaire que ce soit un point de terminaison réel :

![](./media/batch-aad-auth/app-registration-management-plane.png)

Lorsque vous avez terminé le processus d’inscription, l’ID d’application et l’ID d’objet (principal de service) sont répertoriés pour votre application.  

![](./media/batch-aad-auth/app-registration-client-id.png)

### <a name="update-your-code-to-reference-your-application-id"></a>Mettre à jour votre code pour faire référence à votre ID d’application 

Votre application cliente utilise l’ID d’application (également appelé l’ID client) pour accéder à Azure AD lors de l’exécution. Une fois que vous avez inscrit votre application dans le portail Azure, mettez à jour votre code pour utiliser l’ID d’application fourni par Azure AD pour votre application inscrite. Dans l’exemple d’application AccountManagement, copiez votre ID d’application à partir du portail Azure vers la constante appropriée :

```csharp
// Specify the unique identifier (the "Client ID") for your application. This is required so that your
// native client application (i.e. this sample) can access the Microsoft Azure AD Graph API. For information
// about registering an application in Azure Active Directory, please see "Adding an Application" here:
// https://azure.microsoft.com/documentation/articles/active-directory-integrating-applications/
private const string ClientId = "<application-id>";
```
Copiez également l’URI de redirection que vous avez spécifiée pendant l’inscription.

```csharp
// The URI to which Azure AD will redirect in response to an OAuth 2.0 request. This value is
// specified by you when you register an application with AAD (see ClientId comment). It does not
// need to be a real endpoint, but must be a valid URI (e.g. https://accountmgmtsampleapp).
private const string RedirectUri = "http://myaccountmanagementsample";
```

### <a name="grant-the-azure-resource-manager-api-access-to-your-application"></a>Accorder l’accès à l’API Azure Resource Manager à votre application

Ensuite, vous devez déléguer l’accès à votre application à l’API Azure Resource Manager. L’identificateur Azure AD pour l’API Resource Manager est **API Gestion des services Windows Azure**.

Suivez les étapes ci-dessous dans le portail Azure :

1. Dans le volet de navigation de gauche du portail Azure, choisissez **Plus de services**, cliquez sur **Inscriptions d’application**, puis cliquez sur **Ajouter**.
2. Recherchez le nom de votre application dans la liste des inscriptions d’application :

    ![Rechercher le nom de votre application](./media/batch-aad-auth/search-app-registration.png)

3. Affichez le panneau **Paramètres**. Dans la section **Accès API**, sélectionnez **Autorisations requises**.
4. Cliquez sur **Ajouter** pour ajouter une autorisation requise. 
5. À l’étape 1, entrez **API Gestion des services Windows Azure**, sélectionnez cette API dans la liste des résultats, puis cliquez sur le bouton **Sélectionner**.
6. À l’étape 2, activez la case à cocher en regard de **Access Azure classic deployment model as organization users** (Accéder au modèle de déploiement Azure Classic en tant qu’utilisateurs de l’organisation), puis cliquez sur le bouton **Sélectionner**.
7. Cliquez sur le bouton **Terminé**.

Le panneau **Autorisations requises** indique à présent que les autorisations pour votre application sont accordées aux API Resource Manager et à ADAL. Les autorisations sont accordées par défaut à ADAL lorsque vous commencez par inscrire votre application auprès d’Azure AD.

![Déléguer des autorisations à l’API Azure Resource Manager](./media/batch-aad-auth/required-permissions-management-plane.png)


### <a name="acquire-an-azure-ad-authentication-token"></a>Acquérir un jeton d’authentification Azure AD

L’exemple d’application AccountManagement définit des constantes qui fournissent le point de terminaison pour Azure AD et Azure Resource Manager. L’exemple d’application utilise ces constantes pour interroger Azure AD et obtenir les informations relatives à l’abonnement. Laissez ces constantes telles quelles :

```csharp
// Azure Active Directory "common" endpoint.
private const string AuthorityUri = "https://login.microsoftonline.com/common";
// Azure service management resource 
private const string ResourceUri = "https://management.core.windows.net/";
```

Après avoir inscrit l’exemple AccountManagement auprès du locataire Azure AD et fourni les valeurs nécessaires dans le code source de l’exemple, l’exemple est prêt pour une authentification avec Azure AD. Lorsque vous exécutez l’exemple, ADAL tente d’acquérir un jeton d’authentification. À ce stade, vous êtes invité à renseigner vos informations d’identification Microsoft : 

```csharp
// Obtain an access token using the "common" AAD resource. This allows the application
// to query AAD for information that lies outside the application's tenant (such as for
// querying subscription information in your Azure account).
AuthenticationContext authContext = new AuthenticationContext(AuthorityUri);
AuthenticationResult authResult = authContext.AcquireToken(ResourceUri,
                                                        ClientId,
                                                        new Uri(RedirectUri),
                                                        PromptBehavior.Auto);
```

Après avoir fourni vos informations d’identification, l’exemple d’application peut continuer à émettre des demandes authentifiées au service de gestion Batch. 

## <a name="use-azure-ad-with-batch-service-solutions"></a>Utiliser Azure AD avec les solutions de service Batch

La bibliothèque .NET Batch fournit des types pour la création de workflows de traitement parallèle avec le service Batch. Le service Batch prend en charge l’authentification par [clé partagée](https://docs.microsoft.com/rest/api/batchservice/authenticate-requests-to-the-azure-batch-service) et l’authentification via Azure AD. Dans cette section, nous abordons l’authentification via Azure AD.

>[!NOTE]
>Lorsque vous créez un compte Batch, vous pouvez spécifier si les pools doivent être alloués dans un abonnement géré par Batch ou dans un abonnement utilisateur. Si votre compte alloue des pools dans un abonnement utilisateur, vous devez utiliser Azure AD pour authentifier les demandes vers les ressources de ce compte.
>
>

L’authentification des applications .NET Batch via Azure AD est similaire à l’authentification des applications .NET de gestion Batch. Les quelques différences qui existent sont décrites dans cette section.

### <a name="batch-service-endpoints"></a>Points de terminaison de service Batch

Les points de terminaison de service Batch diffèrent de ceux que vous utilisez avec .NET de gestion Batch.

Le point de terminaison Azure AD pour le service Batch est :

`https://login.microsoftonline.com/common`

Le point de terminaison de ressource pour le service Batch est :

`https://batch.core.windows.net/`

### <a name="grant-the-batch-service-api-access-to-your-application"></a>Accorder l’accès à l’API de service Batch à votre application

Avant de pouvoir vous authentifier via Azure AD à partir de votre application Batch, vous devez inscrire votre application auprès d’Azure AD et accorder l’accès à l’API de service Batch. L’identificateur Azure AD pour l’API de service Batch est **Microsoft Azure Batch (MicrosoftAzureBatch)**.

1. Suivez les étapes de la section [Ajout d’une application](../active-directory/develop/active-directory-integrating-applications.md#adding-an-application) dans [Intégration d’applications dans Azure Active Directory][aad_integrate] pour inscrire votre application Batch. Pour **l’URI de redirection**, vous pouvez spécifier n’importe quel URI valide. Aucun point de terminaison réel n’est nécessaire.

    Une fois que vous avez inscrit votre application, vous verrez l’ID d’application et l’ID d’objet :

    ![Inscrire votre application Batch auprès d’Azure AD](./media/batch-aad-auth/app-registration-data-plane.png)

2. Affichez ensuite le panneau **Paramètres**. Dans la section **Accès API**, sélectionnez **Autorisations requises**.
3. Dans le panneau **Autorisations requises**, cliquez sur le bouton **Ajouter**.
4. À l’étape 1, recherchez **MicrosoftAzureBatch**, sélectionnez **Microsoft Azure Batch (MicrosoftAzureBatch)**, puis cliquez sur le bouton **Sélectionner**.
5. À l’étape 2, activez la case à cocher en regard de **Access Azure Batch Service** (Accéder au service Azure Batch) et cliquez sur le bouton **Sélectionner**.
6. Cliquez sur le bouton **Terminé**.

Le panneau **Autorisations requises** indique à présent que votre application Azure AD autorise l’accès aux API Azure Batch et Azure AD. 

![Autorisations des API](./media/batch-aad-auth/required-permissions-data-plane.png)

### <a name="authentication-for-batch-accounts-in-a-user-subscription"></a>Authentification des comptes Batch dans un abonnement utilisateur

Lorsque vous créez un compte Batch, vous pouvez choisir l’abonnement dans lequel les pools sont alloués. Votre choix affecte la façon d’authentifier des demandes faites aux ressources de ce compte

Par défaut, les pools Batch sont alloués dans un abonnement au service Batch. Si vous choisissez cette option, vous pouvez authentifier les demandes adressées aux ressources dans ce compte avec une clé partagée ou avec Azure AD.

Vous pouvez également spécifier que les pools Batch sont alloués à un abonnement utilisateur indiqué. Si vous choisissez cette option, vous devez vous authentifier auprès d’Azure AD.

### <a name="best-practices-for-using-azure-ad-with-batch"></a>Meilleures pratiques pour l’utilisation d’Azure AD avec Batch

Un jeton d’authentification Azure AD expire au bout d’une heure. Lorsque vous utilisez un objet **BatchClient** longue durée, nous vous recommandons de récupérer un jeton à partir d’ADAL à chaque demande pour garantir que vous disposez toujours d’un jeton valide. 

Pour mettre cela en place dans .NET, écrivez une méthode qui récupère le jeton à partir d’Azure AD et qui transmet cette méthode à un objet **BatchTokenCredentials** en tant que délégué. La méthode du délégué est appelée à chaque demande au service Batch pour garantir qu’un jeton valide est fourni. Par défaut, ADAL met en cache des jetons pour qu’un nouveau jeton soit récupéré à partir d’Azure AD uniquement lorsque cela est nécessaire. Pour obtenir un exemple, consultez [Exemple de code : utilisation d’Azure AD avec Batch .NET](#code-example-using-azure-ad-with-batch-net) dans la section suivante. Pour plus d’informations sur les jetons dans Azure AD, consultez [Scénarios d’authentification pour Azure AD][aad_auth_scenarios].

### <a name="code-example-using-azure-ad-with-batch-net"></a>Exemple de code : utilisation d’Azure AD avec Batch .NET

Pour écrire du code Batch .NET qui s’authentifie auprès d’Azure AD, vous devez référencer les packages [Azure Batch .NET](https://www.nuget.org/packages/Azure.Batch/) et [ADAL](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/).

Ajoutez les instructions `using` suivantes dans votre code :

```csharp
using Microsoft.Azure.Batch;
using Microsoft.Azure.Batch.Auth;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
```

Référencez le point de terminaison commun Azure AD et le point de terminaison Azure AD pour le service Batch dans votre code :  

```csharp
private const string AuthorityUri = "https://login.microsoftonline.com/common";
private const string BatchResourceUri = "https://batch.core.windows.net/";
```

Référencez votre point de terminaison de compte Batch :

```csharp
private const string BatchAccountEndpoint = "https://myaccount.westcentralus.batch.azure.com";
```

Spécifiez l’ID d’application (ID client) pour votre application. L’ID d’application est disponible à partir de votre inscription d’application dans le portail Azure ; consultez la section intitulée [Accorder l’accès à l’API de service Batch à votre application](#grant-the-batch-service-api-access-to-your-application) pour le récupérer. 

```csharp
private const string ClientId = "<application-id>";
```

Indiquez également un URI de redirection, qui peut être n’importe quel URI valide.

```csharp
private const string RedirectUri = "http://mybatchdatasample";
```

Écrivez une méthode de rappel pour acquérir le jeton d’authentification à partir d’Azure AD. La méthode **AcquireTokenAsync** invite l’utilisateur à entrer ses informations d’identification et les utilise pour acquérir un nouveau jeton.

```csharp
public static async Task<string> GetAuthenticationTokenAsync()
{
    var authContext = new AuthenticationContext(AuthorityUri);

    // Acquire the authentication token from Azure AD.
    var authResult = await authContext.AcquireTokenAsync(BatchResourceUri, 
                                                        ClientId, 
                                                        new Uri(RedirectUri), 
                                                        new PlatformParameters(PromptBehavior.Auto));

    return authResult.AccessToken;
}
```

Créez un objet **BatchTokenCredentials** qui prend le délégué comme paramètre. Utilisez ces informations d’identification pour ouvrir un objet **BatchClient**. Vous pouvez ensuite utiliser cet objet **BatchClient** pour les opérations suivantes sur le service Batch.

```csharp
public static async Task PerformBatchOperations()
{
    Func<Task<string>> tokenProvider = () => GetAuthenticationTokenAsync();

    using (var client = await BatchClient.OpenAsync(new BatchTokenCredentials(BatchAccountEndpoint, tokenProvider)))
    {
        await client.JobOperations.ListJobs().ToListAsync();
    }
}
```

La méthode de rappel **GetAuthenticationTokenAsync** illustrée ci-dessus utilise Azure AD pour l’authentification intégrée d’un utilisateur qui interagit avec l’application. L’appel de la méthode **AcquireTokenAsync** demande à l’utilisateur ses informations d’identification, et l’application continue une fois que l’utilisateur les a fournies. Vous pouvez également utiliser Azure AD pour authentifier une application sans assistance à l’aide d’un principal de service Azure AD. Pour plus d’informations, consultez [Objets application et principal du service dans Azure Active Directory](../active-directory/develop/active-directory-application-objects.md) et [Utiliser le portail pour créer une application et un principal du service Active Directory pouvant accéder aux ressources](../resource-group-create-service-principal-portal.md).  
 

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur l’exécution de [l’exemple d’application AccountManagement][acct_mgmt_sample], consultez [Gérer les quotas et comptes Batch avec la bibliothèque cliente Batch Management pour .NET](batch-management-dotnet.md).

Pour en savoir plus sur Azure AD, consultez la [documentation sur Azure Active Directory](https://docs.microsoft.com/azure/active-directory/). Des exemples détaillés illustrant l’utilisation d’ADAL sont disponibles dans la bibliothèque [Exemples de code Azure](https://azure.microsoft.com/resources/samples/?service=active-directory).


[aad_about]: ../active-directory/active-directory-whatis.md "Qu’est-ce qu’Azure Active Directory ?"
[aad_adal]: ../active-directory/active-directory-authentication-libraries.md
[aad_auth_scenarios]: ../active-directory/active-directory-authentication-scenarios.md "Scénarios d’authentification pour Azure AD"
[aad_integrate]: ../active-directory/active-directory-integrating-applications.md "Intégration d’applications dans Azure Active Directory"
[acct_mgmt_sample]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/AccountManagement
[azure_portal]: http://portal.azure.com
[resman_overview]: ../azure-resource-manager/resource-group-overview.md

