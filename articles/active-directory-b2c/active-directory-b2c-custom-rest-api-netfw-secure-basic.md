---
title: "Azure Active Directory B2C : Sécuriser vos services RESTful à l’aide de l’authentification HTTP de base"
description: "Exemple montrant comment sécuriser vos échanges de revendications d’API REST dans votre instance Azure AD B2C à l’aide de l’authentification HTTP de base"
services: active-directory-b2c
documentationcenter: 
author: yoelhor
manager: joroja
editor: 
ms.assetid: 
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 09/25/2017
ms.author: yoelh
ms.openlocfilehash: 226ecbe7e4d9d95fd4ba3255c95ec6baa1a86576
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-b2c-secure-your-restful-services-using-http-basic-authentication"></a>Azure Active Directory B2C : Sécuriser vos services RESTful à l’aide de l’authentification HTTP de base
Dans un [article connexe](active-directory-b2c-custom-rest-api-netfw.md), nous avons créé un service RESTful (API Web) qui s’intègre sans authentification dans les parcours utilisateur Azure AD B2C. Cet article explique comment ajouter l’authentification HTTP de base à votre service RESTful, afin que seuls les utilisateurs validés, y compris B2C, puissent accéder à votre API. Grâce à l’authentification HTTP de base, définissez les informations d’identification de l’utilisateur (ID d’application et secret d’application) dans votre stratégie personnalisée pour utiliser les informations d’identification. 

> [!NOTE]
>
>L’article suivant explique de manière détaillée comment effectuer [l’authentification de base dans l’API web ASP.NET](https://docs.microsoft.com/en-us/aspnet/web-api/overview/security/basic-authentication).

## <a name="prerequisites"></a>Composants requis
Effectuez les étapes précédentes de l’article [Procédure pas à pas : intégration des échanges de revendications de l’API REST dans votre parcours utilisateur Azure Active Directory B2C comme validation d’une entrée de l’utilisateur](active-directory-b2c-custom-rest-api-netfw.md).

## <a name="step-1-add-authentication-support"></a>Étape 1 : Ajouter la prise en charge de l’authentification

### <a name="step-11-add-application-settings-to-projects-webconfig-file"></a>Étape 1.1 : Ajouter des paramètres d’application dans le fichier web.config du projet
1. Ouvrir le projet Visual Studio créé précédemment 
2. Ajoutez les paramètres d’application suivants au fichier web.config, sous l’élément `appSettings` :

    ```XML
    <add key="WebApp:ClientId" value="B2CServiceUserAccount" />
    <add key="WebApp:ClientSecret" value="your secret" />
    ```

3. Créer un mot de passe et définir la valeur `WebApp:ClientSecret`

    Pour générer un mot de passe complexe, vous pouvez exécuter PowerShell. Cependant, vous pouvez utiliser n’importe quelle valeur arbitraire.

    ```PowerShell
    $bytes = New-Object Byte[] 32
    $rand = [System.Security.Cryptography.RandomNumberGenerator]::Create()
    $rand.GetBytes($bytes)
    $rand.Dispose()
    [System.Convert]::ToBase64String($bytes)
    ```

### <a name="step-12-install-owin-libraries"></a>Étape 1.2 : Installation de bibliothèques OWIN
Pour commencer, ajoutez au projet les packages NuGet du middleware OWIN à l’aide de la console du gestionnaire de package Visual Studio :

```
PM> Install-Package Microsoft.Owin
PM> Install-Package Owin
PM> Install-Package Microsoft.Owin.Host.SystemWeb
```

### <a name="step-13-add-authentication-middleware-class"></a>Étape 1.3 : Ajouter une classe de middleware d’authentification
Ajoutez la classe `ClientAuthMiddleware.cs` sous le dossier `App_Start`. Cliquez avec le bouton droit sur le dossier `App_Start`, sélectionnez **Ajouter**, puis choisissez une **classe**

![Ajoutez une classe ClientAuthMiddleware.cs sous le dossier App_Start.](media/aadb2c-ief-rest-api-netfw-secure-basic/rest-api-netfw-secure-basic-OWIN-startup-auth1.png)

Saisissez le nom de classe `ClientAuthMiddleware.cs`.

![Créer un cluster C#](media/aadb2c-ief-rest-api-netfw-secure-basic/rest-api-netfw-secure-basic-OWIN-startup-auth2.png)

Ouvrez le fichier `App_Start\ClientAuthMiddleware.cs` et remplacez son contenu par le code suivant :


```C#

using Microsoft.Owin;
using System;
using System.Collections.Generic;
using System.Configuration;
using System.Linq;
using System.Security.Principal;
using System.Text;
using System.Threading.Tasks;
using System.Web;

namespace Contoso.AADB2C.API
{
    /// <summary>
    /// Class to create a custom owin middleware to check for client authentication
    /// </summary>
    public class ClientAuthMiddleware
    {
        private static readonly string ClientID = ConfigurationManager.AppSettings["WebApp:ClientId"];
        private static readonly string ClientSecret = ConfigurationManager.AppSettings["WebApp:ClientSecret"];

        /// <summary>
        /// Gets or sets the next owin middleware
        /// </summary>
        private Func<IDictionary<string, object>, Task> Next { get; set; }

        /// <summary>
        /// Initializes a new instance of the <see cref="ClientAuthMiddleware"/> class.
        /// </summary>
        /// <param name="next"></param>
        public ClientAuthMiddleware(Func<IDictionary<string, object>, Task> next)
        {
            this.Next = next;
        }

        /// <summary>
        /// Invoke client authentication middleware during each request.
        /// </summary>
        /// <param name="environment">Owin environment</param>
        /// <returns></returns>
        public Task Invoke(IDictionary<string, object> environment)
        {
            // Get wrapper class for the environment
            var context = new OwinContext(environment);

            // Check whether the authorization header is available. This contains the credentials.
            var authzValue = context.Request.Headers.Get("Authorization");
            if (string.IsNullOrEmpty(authzValue) || !authzValue.StartsWith("Basic ", StringComparison.OrdinalIgnoreCase))
            {
                // Process next middleware
                return Next(environment);
            }

            // Get credentials
            var creds = authzValue.Substring("Basic ".Length).Trim();
            string clientId;
            string clientSecret;

            if (RetrieveCreds(creds, out clientId, out clientSecret))
            {
                // Set transaction authenticated as client
                context.Request.User = new GenericPrincipal(new GenericIdentity(clientId, "client"), new string[] { "client" });
            }

            return Next(environment);
        }

        /// <summary>
        /// Retrieve credentials from header
        /// </summary>
        /// <param name="credentials">Authorization header</param>
        /// <param name="clientId">Client identifier</param>
        /// <param name="clientSecret">Client secret</param>
        /// <returns>True if valid credentials were presented</returns>
        private bool RetrieveCreds(string credentials, out string clientId, out string clientSecret)
        {
            string pair;
            clientId = clientSecret = string.Empty;

            try
            {
                pair = Encoding.UTF8.GetString(Convert.FromBase64String(credentials));
            }
            catch (FormatException)
            {
                return false;
            }
            catch (ArgumentException)
            {
                return false;
            }

            var ix = pair.IndexOf(':');
            if (ix == -1)
            {
                return false;
            }

            clientId = pair.Substring(0, ix);
            clientSecret = pair.Substring(ix + 1);

            // Return whether credentials are valid
            return (string.Compare(clientId, ClientAuthMiddleware.ClientID) == 0 &&
                string.Compare(clientSecret, ClientAuthMiddleware.ClientSecret) == 0);
        }
    }
}
```

### <a name="step-14-add-an-owin-startup-class"></a>Étape 1.4 : Ajouter une classe de démarrage OWIN
Ajoutez une classe de démarrage OWIN à l’API appelée `Startup.cs`. Cliquez avec le bouton droit sur le projet, sélectionnez **Ajouter** et **Nouvel élément**, puis recherchez OWIN.

![Ajouter une classe de démarrage OWIN](media/aadb2c-ief-rest-api-netfw-secure-basic/rest-api-netfw-secure-basic-OWIN-startup.png)

Ouvrez le fichier `Startup.cs` et remplacez son contenu par le code suivant :

```C#
using Microsoft.Owin;
using Owin;

[assembly: OwinStartup(typeof(Contoso.AADB2C.API.Startup))]
namespace Contoso.AADB2C.API
{
    public class Startup
    {
        public void Configuration(IAppBuilder app)
        {
                app.Use<ClientAuthMiddleware>();
        }
    }
}
```

### <a name="step-15-protect-identity-api-class"></a>Étape 1.5 : Protéger la classe d’API d’identité
Ouvrez le fichier Controllers\IdentityController.cs et ajoutez la balise `[Authorize]` à la classe de contrôleur. La balise `[Authorize]` limite l’accès au contrôleur aux utilisateurs qui répondent aux exigences en matière d’autorisation.

![Ajouter la balise [Authorize] au contrôleur](media/aadb2c-ief-rest-api-netfw-secure-basic/rest-api-netfw-secure-basic-authorize.png)

## <a name="step-2-publish-to-azure"></a>Étape 2 : Publier dans Azure
Pour publier votre projet, dans **l’Explorateur de solutions**, cliquez avec le bouton droit sur le projet **Contoso.AADB2C.API**, puis sélectionnez **Publier**.

## <a name="step-3-add-the-restful-services-app-id--app-secret-to-azure-ad-b2c"></a>Étape 3 : Ajouter le secret d’application et l’ID d’application des services RESTful à Azure AD B2C
Une fois que votre service RESTful est protégé par l’ID client (nom d’utilisateur) et la clé secrète client, vous avez besoin de stocker les informations d’identification dans votre locataire Azure AD B2C. Votre stratégie personnalisée fournit les informations d’identification tout en appelant les services RESTful.  

### <a name="step-31-add-restful-services-client-id"></a>Étape 3.1 : Ajouter l’ID client des services RESTful
1.  Accédez à votre locataire Azure AD B2C, puis sélectionnez **Paramètres Azure AD B2C** > **Infrastructure d’expérience d’identité**
2.  Sélectionnez **Clés de stratégie** pour afficher les clés disponibles dans votre client.
3.  Cliquez sur **+Ajouter**.
4.  Pour **Options**, utilisez **Manuel**.
5.  Pour **Nom**, utilisez `B2cRestClientId`.  
    Il est possible que le préfixe `B2C_1A_` soit ajouté automatiquement.
6.  Dans la zone **Secret**, entrez l’ID d’application que vous avez défini précédemment.
7.  Pour la zone **Utilisation de la clé**, utilisez la valeur **Secret**.
8.  Cliquez sur **Créer**
9.  Vérifiez que vous avez créé la clé `B2C_1A_B2cRestClientId`.

### <a name="step-32-add-restful-services-client-secret"></a>Étape 3.2 : Ajouter la clé secrète client des services RESTful
1.  Accédez à votre locataire Azure AD B2C, puis sélectionnez **Paramètres Azure AD B2C** > **Infrastructure d’expérience d’identité**
2.  Sélectionnez **Clés de stratégie** pour afficher les clés disponibles dans votre client.
3.  Cliquez sur **+Ajouter**.
4.  Pour **Options**, utilisez **Manuel**.
5.  Pour **Nom**, utilisez `B2cRestClientSecret`.  
    Il est possible que le préfixe `B2C_1A_` soit ajouté automatiquement.
6.  Dans la zone **Secret**, saisissez la clé secrète d’application que vous avez définie précédemment.
7.  Pour la zone **Utilisation de la clé**, utilisez la valeur **Secret**.
8.  Cliquez sur **Créer**
9.  Vérifiez que vous avez créé la clé `B2C_1A_B2cRestClientSecret`.

## <a name="step-4-change-the-technicalprofile-to-support-basic-authentication-in-your-extension-policy"></a>Étape 4 : Modifier l’élément `TechnicalProfile` de façon à prendre en charge l’authentification de base dans votre stratégie d’extension
1.  Ouvrez le fichier de stratégie d’extension (TrustFrameworkExtensions.xml) à partir de votre répertoire de travail.
2.  Trouvez le nœud `<TechnicalProfile>` qui inclut `Id="REST-API-SignUp"`.
3.  Recherchez l’élément `<Metadata>`
4.  Remplacez l’élément `AuthenticationType` par `Basic`.
```xml
<Item Key="AuthenticationType">Basic</Item>
```
5.  Ajoutez l’extrait de code XML suivant immédiatement après la fermeture de l’élément `<Metadata>` :  

    ```xml
    <CryptographicKeys>
        <Key Id="BasicAuthenticationUsername" StorageReferenceId="B2C_1A_B2cRestClientId" />
        <Key Id="BasicAuthenticationPassword" StorageReferenceId="B2C_1A_B2cRestClientSecret" />
    </CryptographicKeys>
    ```
Une fois les extraits de code XML ajoutés, l’élément `TechnicalProfile` doit ressembler à ce qui suit :

![Ajouter des éléments XML d’authentification de base](media/aadb2c-ief-rest-api-netfw-secure-basic/rest-api-netfw-secure-basic-add-1.png)

## <a name="step-5-upload-the-policy-to-your-tenant"></a>Étape 5 : Charger la stratégie sur votre locataire

1.  Dans le [portail Azure](https://portal.azure.com), passez au [contexte de votre locataire Azure AD B2C](active-directory-b2c-navigate-to-b2c-context.md) et cliquez sur **Azure AD B2C**.
2.  Sélectionnez **Infrastructure d’expérience d’identité**.
3.  Cliquez sur **Toutes les stratégies**.
4.  Sélectionnez **Charger la stratégie**
5.  Cochez la case **Remplacer la stratégie si elle existe**.
6.  **Téléchargez** TrustFrameworkExtensions.xml et vérifiez que sa validation n’échoue pas

## <a name="step-6-test-the-custom-policy-by-using-run-now"></a>Étape 6 : Tester la stratégie personnalisée en utilisant la commande Exécuter maintenant
1.  Ouvrez **Paramètres Azure AD B2C** et accédez à **Infrastructure d’expérience d’identité**.

    >[!NOTE]
    >
    >    **Exécuter maintenant** nécessite la préinscription d’au moins une application sur le locataire. 
    >    Pour découvrir comment inscrire des applications, consultez les articles sur Azure AD B2C [Bien démarrer](active-directory-b2c-get-started.md) et [Inscription des applications](active-directory-b2c-app-registration.md).

2.  Ouvrez **B2C_1A_signup_signin**, la stratégie personnalisée de partie de confiance que vous avez chargée. Sélectionnez **Exécuter maintenant**.
3.  Essayez de taper « Test » dans le champ **Prénom**, B2C affiche un message d’erreur en haut de la page.

    ![Tester l’API d’identité](media/aadb2c-ief-rest-api-netfw-secure-basic/rest-api-netfw-test.png)

4.  Essayez d’entrer un nom (différent de « test ») dans le champ **Prénom**. B2C inscrit l’utilisateur, puis envoie la valeur loyaltyNumber à votre application. Notez le numéro dans ce jeton JWT, dans cet exemple.

```
{
  "typ": "JWT",
  "alg": "RS256",
  "kid": "X5eXk4xyojNFum1kl2Ytv8dlNP4-c57dO6QGTVBwaNk"
}.{
  "exp": 1507125903,
  "nbf": 1507122303,
  "ver": "1.0",
  "iss": "https://login.microsoftonline.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
  "aud": "e1d2612f-c2bc-4599-8e7b-d874eaca1ee1",
  "acr": "b2c_1a_signup_signin",
  "nonce": "defaultNonce",
  "iat": 1507122303,
  "auth_time": 1507122303,
  "loyaltyNumber": "290",
  "given_name": "Emily",
  "emails": ["B2cdemo@outlook.com"]
}
```

## <a name="next-steps"></a>Étapes suivantes
* [Utiliser les certificats client pour sécuriser votre API RESTful](active-directory-b2c-custom-rest-api-netfw-secure-cert.md)

## <a name="optional-download-the-complete-policy-files-and-code"></a>[Facultatif] Télécharger les fichiers de stratégie et le code complets
* Nous vous recommandons de créer votre scénario avec vos propres fichiers de stratégie personnalisée après avoir suivi la procédure pas à pas Bien démarrer avec les stratégies personnalisées, au lieu d’utiliser ces exemples de fichiers.  [Exemples de fichiers de stratégie de référence](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-rest-api-netfw-secure-basic)
* Vous pouvez télécharger le code complet ici : [Exemple de solution Visual Studio pour référence](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-rest-api-netfw/Contoso.AADB2C.API).