<properties
   pageTitle="Utilisation d’une assertion du client pour obtenir des jetons d’accès d’Azure AD | Microsoft Azure"
   description="Utilisation d’une assertion du client pour obtenir des jetons d’accès d’Azure AD."
   services=""
   documentationCenter="na"
   authors="MikeWasson"
   manager="roshar"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/16/2016"
   ms.author="mwasson"/>

# Utilisation d’une assertion du client pour obtenir des jetons d’accès d’Azure AD

Cet article fait [partie d’une série]. Un [exemple d’application] accompagne également cette série.

## Contexte

Lors de l’utilisation d’un flux de code d’autorisation ou d’un flux hybride dans OpenID Connect, le client reçoit un jeton d’accès en échange d’un code d’autorisation. Au cours de cette étape, le client doit s’authentifier auprès du serveur.

![Clé secrète client](media/guidance-multitenant-identity/client-secret.png)

L’un des moyens d’authentifier le client consiste à utiliser une clé secrète client. C’est pourquoi l’application [Surveys de Tailspin][Surveys] est configurée par défaut.

Voici un exemple de requête du client au fournisseur d’identité, demandant un jeton d’accès. Notez le paramètre `client_secret`.

```
POST https://login.microsoftonline.com/b9bd2162xxx/oauth2/token HTTP/1.1
Content-Type: application/x-www-form-urlencoded

resource=https://tailspin.onmicrosoft.com/surveys.webapi
  &client_id=87df91dc-63de-4765-8701-b59cc8bd9e11
  &client_secret=i3Bf12Dn...
  &grant_type=authorization_code
  &code=PG8wJG6Y...
```

La clé secrète est juste une chaîne. Vous devez donc veiller à ne pas divulguer cette valeur. La bonne pratique consiste à conserver la clé secrète client hors du contrôle de code source. Quand vous effectuez un déploiement sur Azure, stockez la clé secrète dans un [paramètre d’application][configure-web-app].

Toutefois, toute personne ayant accès à l’abonnement Azure peut afficher les paramètres d’application. De plus, il est toujours tentant de vérifier les clés secrètes dans le contrôle de code source (par exemple, dans les scripts de déploiement), de les partager par courrier électronique, etc.

Pour renforcer la sécurité, vous pouvez utiliser l’_assertion du client_ au lieu d’une clé secrète client. Avec l’assertion du client, le client utilise un certificat X.509 pour prouver que la demande de jeton provient de lui. Le certificat client est installé sur le serveur web. En général, il est plus facile de restreindre l’accès au certificat que de garantir que personne ne révèle par inadvertance une clé secrète client.

Voici une requête de jeton utilisant l’assertion du client :

```
POST https://login.microsoftonline.com/b9bd2162xxx/oauth2/token HTTP/1.1
Content-Type: application/x-www-form-urlencoded

resource=https://tailspin.onmicrosoft.com/surveys.webapi
  &client_id=87df91dc-63de-4765-8701-b59cc8bd9e11
  &client_assertion_type=urn:ietf:params:oauth:client-assertion-type:jwt-bearer
  &client_assertion=eyJhbGci...
  &grant_type=authorization_code
  &code= PG8wJG6Y...
```

Notez que le paramètre `client_secret` n’est plus utilisé. À la place, le paramètre `client_assertion` contient un jeton JWT qui a été signé à l’aide du certificat client. Le paramètre `client_assertion_type` spécifie le type d’assertion (dans ce cas présent, un jeton JWT). Le serveur valide le jeton JWT. Si le jeton JWT n’est pas valide, la requête de jeton renvoie une erreur.

> [AZURE.NOTE] Les certificats X.509 ne sont pas la seule forme d’assertion du client. Nous nous concentrons ici dessus, car elle est prise en charge par Azure AD.

## Utilisation de l’assertion du client dans l’application Surveys

Cette section indique comment configurer l’application Surveys de Tailspin pour utiliser l’assertion du client. Au cours de ces étapes, vous allez générer un certificat auto-signé qui peut être utilisé pour le développement, mais pas à des fins de production.

1. Exécutez le script PowerShell [/Scripts/Setup-KeyVault.ps1][Setup-KeyVault] comme suit :

    ```
    .\Setup-KeyVault.ps -Subject [subject]
    ```

    Pour le paramètre `Subject`, entrez un nom, comme « surveysapp ». Le script génère un certificat auto-signé et le stocke dans le magasin de certificats « Utilisateur actuel/Personnel ».

2. La sortie du script est un fragment JSON. Ajoutez-le au manifeste d’application de l’application web, en procédant comme suit :

    1. Connectez-vous au [portail de gestion Azure][azure-management-portal] et accédez à votre annuaire Azure AD.

    2. Cliquez sur **Applications**.

    3. Sélectionnez l’application Surveys.

    4.	Cliquez sur **Gérer le manifeste** et sélectionnez **Télécharger le manifeste**.

    5.	Ouvrez le fichier JSON du manifeste dans un éditeur de texte. Collez la sortie du script dans la propriété `keyCredentials`. Le résultat doit être semblable à ce qui suit :

        ```    
        "keyCredentials": [
            {
              "type": "AsymmetricX509Cert",
              "usage": "Verify",
              "keyId": "29d4f7db-0539-455e-b708-....",
              "customKeyIdentifier": "ZEPpP/+KJe2fVDBNaPNOTDoJMac=",
              "value": "MIIDAjCCAeqgAwIBAgIQFxeRiU59eL.....
            }
          ],
         ```

    6.	Enregistrez les modifications apportées au fichier JSON.

    7.	Revenez au portail. Cliquez sur **Gérer le manifeste** > **Télécharger le manifeste sur le serveur**, puis téléchargez le fichier JSON.

3. Exécutez la commande suivante pour obtenir l’empreinte numérique du certificat.

    ```
    certutil -store -user my [subject]
    ```

    où `[subject]` est la valeur que vous avez spécifiée pour l’objet dans le script PowerShell. L’empreinte numérique est répertoriée sous « Cert Hash(sha1) ». Supprimez les espaces entre les nombres hexadécimaux.

4. Mettez à jour vos clés secrètes d’application. Dans l’Explorateur de solutions, cliquez avec le bouton droit sur le projet Tailspin.Surveys.Web, puis sélectionnez **Gérer les données secrètes de l’utilisateur**. Ajoutez une entrée pour « Asymmetric » sous « AzureAd », comme indiqué ci-dessous :

    ```
    {
      "AzureAd": {
        "ClientId": "[Surveys application client ID]",
        // "ClientSecret": "[client secret]",  << Delete this entry
        "PostLogoutRedirectUri": "https://localhost:44300/",
        "WebApiResourceId": "[App ID URI of your Survey.WebAPI application]",
        // new:
        "Asymmetric": {
          "CertificateThumbprint": "[certificate thumbprint]",  // Example: "105b2ff3bc842c53582661716db1b7cdc6b43ec9"
          "StoreName": "My",
          "StoreLocation": "CurrentUser",
          "ValidationRequired": "false"
        }
      },
      "Redis": {
        "Configuration": "[Redis connection string]"
      }
    }
    ```

    Vous devez affecter à `ValidationRequired` la valeur false, car le certificat n’a pas été signé par une autorité de certification racine. En production, utilisez un certificat signé par une autorité de certification, puis affectez à `ValidationRequired` la valeur true.

    Supprimez également l’entrée spécifiée pour `ClientSecret`, car elle n’est pas nécessaire avec l’assertion du client.

5. Dans Startup.cs, recherchez le code qui inscrit le `ICredentialService`. Supprimez les marques de commentaire de la ligne qui utilise `CertificateCredentialService`, puis commentez la ligne qui utilise `ClientCredentialService` :

    ```csharp
    // Uncomment this:
    services.AddSingleton<ICredentialService, CertificateCredentialService>();
    // Comment out this:
    //services.AddSingleton<ICredentialService, ClientCredentialService>();
    ```

Au moment de l’exécution, l’application web lit le certificat à partir du magasin de certificats. Le certificat doit être installé sur la même machine que l’application web.

## Ressources supplémentaires

- [Using Certificates in Azure Websites Applications][using-certs-in-websites]
- [RFC 7521][RFC7521]. Définit le mécanisme général d’envoi d’une assertion du client.
- [RFC 7523][RFC7523]. Explique comment utiliser des jetons JWT pour l’assertion du client.


<!-- Links -->
[configure-web-app]: ../app-service-web/web-sites-configure.md
[azure-management-portal]: https://manage.windowsazure.com
[RFC7521]: https://tools.ietf.org/html/rfc7521
[RFC7523]: https://tools.ietf.org/html/rfc7523
[Setup-KeyVault]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/scripts/Setup-KeyVault.ps1
[Surveys]: guidance-multitenant-identity-tailspin.md
[using-certs-in-websites]: https://azure.microsoft.com/blog/using-certificates-in-azure-websites-applications/
[partie d’une série]: guidance-multitenant-identity.md
[exemple d’application]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps

<!---HONumber=AcomDC_0302_2016-->