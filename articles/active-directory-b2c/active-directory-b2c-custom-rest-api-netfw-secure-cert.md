---
title: "Azure Active Directory B2C : sécuriser vos services RESTful à l’aide de certificats clients"
description: "Exemple montrant comment sécuriser vos échanges de revendications de API REST dans votre instance Azure AD B2C à l’aide de certificats clients"
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
ms.openlocfilehash: a5cfe0e3f40b929e969e0a118939caa1ccb33cc2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-b2c-secure-your-restful-services-using-client-certificates"></a>Azure Active Directory B2C : sécuriser vos services RESTful à l’aide de certificats clients
Après la [création d’un service RESTful](active-directory-b2c-custom-rest-api-netfw.md) pour interagir avec Azure AD B2C, nous allons montrer comment restreindre l’accès à votre application web Azure (API RESTful) à l’aide d’un certificat client. Ce mécanisme est appelé authentification mutuelle TLS ou bien **authentification par certificat client**.  Seuls les services comme Azure AD B2C avec le certificat approprié seront en mesure d’accéder à votre service.

> [!NOTE]
>
>Vous pouvez également sécuriser votre service RESTful [à l’aide de l’authentification de base HTTP](active-directory-b2c-custom-rest-api-netfw-secure-basic.md). Toutefois, vous devez savoir que l’authentification de base HTTP est considérée comme moins sécurisée que le certificat client. Notre recommandation est de sécuriser le service RESTful à l’aide de l’authentification par certificat client comme décrit dans cet article.

Cet article explique comment :
1. Configurer votre application Web pour utiliser l'authentification par certificat client
1. Télécharger le certificat sur les clés de stratégie de Azure AD B2C
1. Configurer votre stratégie personnalisée pour utiliser le certificat client

## <a name="prerequisites"></a>Composants requis
* Suivre les étapes précédentes dans l’article [Intégrer les échanges de revendications de l’API REST](active-directory-b2c-custom-rest-api-netfw.md)
* Vous devez disposer d’un certificat valide (fichier .pfx avec la clé privée)

## <a name="step-1-configure-web-app-for-client-certificate-authentication"></a>Étape 1 : Configuration d'une application Web pour l'authentification par certificat client
Pour configurer **Azure App Service** afin d’exiger des certificats clients, le paramètre de site de l’application web `clientCertEnabled` doit avoir la valeur « true ». Ce paramètre n’est actuellement pas disponible par le biais de l’expérience de gestion dans le portail Azure et vous devez utiliser l’API REST pour y parvenir.

> [!NOTE]
>
>Assurez-vous que votre plan de Azure App Service est Standard ou au-dessus. Pour plus d’informations, consultez la rubrique [Présentation détaillée des plans Azure App Service](https://docs.microsoft.com/en-us/azure/app-service/azure-web-sites-web-hosting-plans-in-depth-overview)


Utilisez [Azure Resource Explorer (préversion)](https://resources.azure.com) pour configurer la propriété clientCertEnabled sur la valeur « true ». La capture d’écran suivante montre comment définir la valeur clientCertEnabled via Azure Resource Explorer ![Réglage de clientCertEnabled via l’Explorateur de ressources Azure](media/aadb2c-ief-rest-api-netfw-secure-cert/rest-api-netfw-secure-client-cert-resource-explorer.png)

> [!NOTE]
>
>Pour plus d’informations sur comment définir l’attribut clientCertEnabled, consultez : [Comment configurer l’authentification mutuelle TLS pour une application web](https://docs.microsoft.com/en-us/azure/app-service-web/app-service-web-configure-tls-mutual-auth)


> [!TIP]
>
>Vous pouvez également utiliser l'outil [ARMClient](https://github.com/projectkudu/ARMClient) pour faciliter l’élaboration de l’appel de l’API REST.

## <a name="step-2-upload-your-certificate-to-azure-ad-b2c-policy-keys"></a>Étape 2 : Télécharger votre certificat sur les clés de stratégie de Azure AD B2C
Après avoir défini `clientCertEnabled` sur `true`, la communication avec votre API RESTful nécessite maintenant un certificat client. Pour ce faire, vous devez charger et stocker le certificat client dans votre locataire Azure AD B2C.   
1.  Accédez à votre locataire Azure AD B2C, puis sélectionnez **Paramètres Azure AD B2C** > **Infrastructure d’expérience d’identité**
2.  Sélectionnez **Clés de stratégie** pour afficher les clés disponibles dans votre client.
3.  Cliquez sur **+Ajouter**.
4.  Pour **Options**, utilisez **Télécharger**.
5.  Pour **Nom**, utilisez `B2cRestClientCertificate`.  
    Le préfixe `B2C_1A_` est ajouté automatiquement.
6.  Dans **Chargement de fichier**, sélectionnez votre fichier de certificat .pfx avec la clé privée. Indiquez ensuite le **mot de passe** du certificat.

    ![Télécharger la clé de stratégie](media/aadb2c-ief-rest-api-netfw-secure-cert/rest-api-netfw-secure-client-cert-upload.png)

7.  Cliquez sur **Créer**
8.  Pour confirmer que vous avez créé la clé `B2C_1A_B2cRestClientCertificate`, sélectionnez **Clés de stratégie** pour afficher les clés disponibles dans votre locataire.

## <a name="step-3-change-the-technicalprofile-to-support-client-certificate-authentication-in-your-extension-policy"></a>Étape 3 : Modifier `TechnicalProfile` pour prendre en charge l’authentification de certificat client dans votre stratégie d’extension
1.  Ouvrez le fichier de stratégie d’extension (TrustFrameworkExtensions.xml) à partir de votre répertoire de travail.
2.  Trouvez le nœud `<TechnicalProfile>` qui inclut `Id="REST-API-SignUp"`
3.  Recherchez l’élément `<Metadata>`
4.  Remplacez l’élément `AuthenticationType` par `ClientCertificate`

    ```xml
    <Item Key="AuthenticationType">ClientCertificate</Item>
    ```

5.  Ajoutez l’extrait de code XML suivant immédiatement après la fermeture de l’élément `<Metadata>` :  

    ```xml
    <CryptographicKeys>
        <Key Id="ClientCertificate" StorageReferenceId="B2C_1A_B2cRestClientCertificate" />
    </CryptographicKeys>
    ```

Une fois les extraits de code XML ajoutés, l’élément `TechnicalProfile` doit ressembler à ce qui suit :

![Définir les éléments XML de l’authentification ClientCertificate (Certificat client)](media/aadb2c-ief-rest-api-netfw-secure-cert/rest-api-netfw-secure-client-cert-tech-profile.png)

## <a name="step-4-upload-the-policy-to-your-tenant"></a>Étape 4 : Charger la stratégie sur votre locataire

1.  Dans le [portail Azure](https://portal.azure.com), passez au [contexte de votre locataire Azure AD B2C](active-directory-b2c-navigate-to-b2c-context.md) et cliquez sur **Azure AD B2C**.
2.  Sélectionnez **Infrastructure d’expérience d’identité**.
3.  Cliquez sur **Toutes les stratégies**.
4.  Sélectionnez **Charger la stratégie**
5.  Cochez la case **Remplacer la stratégie si elle existe**.
6.  **Téléchargez** TrustFrameworkExtensions.xml et vérifiez que sa validation n’échoue pas

## <a name="step-5-test-the-custom-policy-by-using-run-now"></a>Étape 5 : Tester la stratégie personnalisée en utilisant Exécuter maintenant
1.  Ouvrez **Paramètres Azure AD B2C** et accédez à **Infrastructure d’expérience d’identité**.

    >[!NOTE]
    >
    >    **Exécuter maintenant** nécessite la préinscription d’au moins une application sur le locataire. 
    >    Pour découvrir comment inscrire des applications, consultez les articles sur Azure AD B2C [Bien démarrer](active-directory-b2c-get-started.md) et [Inscription des applications](active-directory-b2c-app-registration.md).

2.  Ouvrez **B2C_1A_signup_signin**, la stratégie personnalisée de partie de confiance que vous avez chargée. Sélectionnez **Exécuter maintenant**.

3.  Essayez de taper « Test » dans le champ **Prénom**, B2C affiche un message d’erreur en haut de la page

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

   > [!NOTE]
   >Si vous obtenez le message d’erreur ci-dessus, cela signifie que Azure AD B2C a appelé votre service RESTful avec succès lors de la présentation du certificat client. L’étape suivante consiste à valider le certificat.

## <a name="step-6-adding-certificate-validation"></a>Étape 6 : Ajout d’une validation de certificat
Le certificat client envoyé par Azure AD B2C à votre service RESTful ne passe par aucune validation de la part de la plateforme Azure Web Apps (à l’exception de la vérification de l’existence du certificat). La validation de ce certificat est la responsabilité de l'application Web. Voici un exemple de code ASP.NET qui valide les propriétés du certificat pour l'authentification.

> [!NOTE]
>Pour savoir comment configurer Azure Service App pour l’authentification par certificat client, consultez : [Configuration de l’authentification mutuelle TLS pour une application Web](https://docs.microsoft.com/en-us/azure/app-service-web/app-service-web-configure-tls-mutual-auth)

### <a name="61-add-application-settings-to-projects-webconfig-file"></a>6.1 Ajouter des paramètres d’application pour le fichier web.config du projet
Ouvrez votre projet Visual Studio créé précédemment et ajoutez les paramètres d’application suivants au fichier web.config sous l’élément `appSettings`

```XML
<add key="ClientCertificate:Subject" value="CN=Subject name" />
<add key="ClientCertificate:Issuer" value="CN=Issuer name" />
<add key="ClientCertificate:Thumbprint" value="Certificate thumbprint" />
```

Remplacez les valeurs du certificat :
* Nom de l’objet
* Nom de l'émetteur
* Empreinte de certificat

### <a name="62-add-isvalidclientcertificate-function"></a>6.2 Ajouter une fonction IsValidClientCertificate
Ouvrez Controllers\IdentityController.cs et ajoutez la fonction suivante à la classe de contrôleur `Identity`. 

```C#
private bool IsValidClientCertificate()
{
    string ClientCertificateSubject = ConfigurationManager.AppSettings["ClientCertificate:Subject"];
    string ClientCertificateIssuer = ConfigurationManager.AppSettings["ClientCertificate:Issuer"];
    string ClientCertificateThumbprint = ConfigurationManager.AppSettings["ClientCertificate:Thumbprint"];

    X509Certificate2 clientCertInRequest = RequestContext.ClientCertificate;
    if (clientCertInRequest == null)
    {
        Trace.WriteLine("Certificate is NULL");
        return false;
    }

    // Basic verification
    if (clientCertInRequest.Verify() == false)
    {
        Trace.TraceError("Basic verification failed");
        return false;
    }

    // 1. Check time validity of certificate
    if (DateTime.Compare(DateTime.Now, clientCertInRequest.NotBefore) < 0 ||
        DateTime.Compare(DateTime.Now, clientCertInRequest.NotAfter) > 0)
    {
        Trace.TraceError($"NotBefore '{clientCertInRequest.NotBefore}' or NotAfter '{clientCertInRequest.NotAfter}' not valid");
        return false;
    }

    // 2. Check subject name of certificate
    bool foundSubject = false;
    string[] certSubjectData = clientCertInRequest.Subject.Split(new char[] { ',' }, StringSplitOptions.RemoveEmptyEntries);
    foreach (string s in certSubjectData)
    {
        if (String.Compare(s.Trim(), ClientCertificateSubject) == 0)
        {
            foundSubject = true;
            break;
        }
    }

    if (!foundSubject)
    {
        Trace.TraceError($"Subject name '{clientCertInRequest.Subject}' is not valid");
        return false;
    }
    
    // 3. Check issuer name of certificate
    bool foundIssuerCN = false;
    string[] certIssuerData = clientCertInRequest.Issuer.Split(new char[] { ',' }, StringSplitOptions.RemoveEmptyEntries);
    foreach (string s in certIssuerData)
    {
        if (String.Compare(s.Trim(), ClientCertificateIssuer) == 0)
        {
            foundIssuerCN = true;
            break;
        }
    }

    if (!foundIssuerCN)
    {
        Trace.TraceError($"Issuer '{clientCertInRequest.Issuer}' is not valid");
        return false;
    }

    // 4. Check thumprint of certificate
    if (String.Compare(clientCertInRequest.Thumbprint.Trim().ToUpper(), ClientCertificateThumbprint) != 0)
    {
        Trace.TraceError($"Thumbprint '{clientCertInRequest.Thumbprint.Trim().ToUpper()}' is not valid");
        return false;
    }

    // 5. If you also want to test if the certificate chains to a Trusted Root Authority you can uncomment the code below
    //
    //X509Chain certChain = new X509Chain();
    //certChain.Build(certificate);
    //bool isValidCertChain = true;
    //foreach (X509ChainElement chElement in certChain.ChainElements)
    //{
    //    if (!chElement.Certificate.Verify())
    //    {
    //        isValidCertChain = false;
    //        break;
    //    }
    //}
    //if (!isValidCertChain) return false;
    return true;
}
```

Dans **l’exemple** précédent, nous acceptons et considérons uniquement le certificat si toutes les conditions sont remplies :
1. Le certificat n’a pas expiré et est actif à l’heure actuelle sur le serveur.
2. Le nom `Subject` du certificat possède le nom commun égal à la valeur de paramètre d’application `ClientCertificate:Subject`
3. Le nom `Issuer` du certificat possède le nom commun égal à la valeur de paramètre d’application `ClientCertificate:Issuer`
4. Le `thumbprint` du certificat est égal à la valeur de paramètre d’application `ClientCertificate:Thumbprint`

> [!IMPORTANT]
>
>En fonction de la sensibilité de votre service, vous devrez peut-être ajouter plus de validations. Par exemple : test de la chaîne d’approbation vers une autorité racine fiable, de la validation du nom de l’organisation émettrice et d’autres.

### <a name="63-add-isvalidclientcertificate-function"></a>6.3 Ajouter la fonction IsValidClientCertificate
Ouvrez Controllers\IdentityController.cs au début de la fonction `SignUp()` et ajoutez les lignes de code suivantes. 

```C#
if (IsValidClientCertificate() == false)
{
    return Content(HttpStatusCode.Conflict, new B2CResponseContent("Your client certificate is not valid", HttpStatusCode.Conflict));
}
```

Après avoir ajouté les extraits de code, votre contrôleur `Identity` doit ressembler à :

![Ajout d’un code de validation de certificat](media/aadb2c-ief-rest-api-netfw-secure-cert/rest-api-netfw-secure-client-code.png)

## <a name="step-7-publish-to-azure-and-test"></a>Étape 7 : Publier sur Azure et test
1. Dans **l’Explorateur de solutions**, cliquez avec le bouton droit sur le projet **Contoso.AADB2C.API**, puis sélectionnez **Publier**.
2. Répétez l’étape 6 et testez votre stratégie personnalisée (de nouveau) avec la validation du certificat. Essayez d’exécuter la stratégie et assurez-vous que tout fonctionne après avoir ajouté la validation.
3. Maintenant, dans votre fichier web.config, changez la valeur de `ClientCertificate:Subject` par une valeur **non valide**. Exécutez une nouvelle fois la stratégie, vous devriez alors voir le message d’erreur.
4. Ressaisissez la valeur **valide** et vérifiez que la stratégie peut appeler votre API REST.
5. Résoudre les problèmes en [collectant les journaux à l’aide d’Application Insights](active-directory-b2c-troubleshoot-custom.md).

## <a name="optional-download-the-complete-policy-files-and-code"></a>[Facultatif] Télécharger les fichiers de stratégie et le code complets
* Nous vous recommandons de créer votre scénario avec vos propres fichiers de stratégie personnalisée après avoir suivi la procédure pas à pas Bien démarrer avec les stratégies personnalisées, au lieu d’utiliser ces exemples de fichiers.  [Exemples de fichiers de stratégie de référence](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-rest-api-netfw-secure-cert)
* Vous pouvez télécharger le code complet ici [Exemple de solution Visual Studio pour référence](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-rest-api-netfw/Contoso.AADB2C.API)