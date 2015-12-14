<properties
   pageTitle="Authentification d’un principal du service à l’aide d’Azure Resource Manager"
   description="Explique comment authentifier un principal du service et lui accorder l’accès via le contrôle d’accès en fonction du rôle. Explique comment effectuer ces tâches avec PowerShell et Azure CLI."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="wpickett"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="multiple"
   ms.workload="na"
   ms.date="11/18/2015"
   ms.author="tomfitz"/>

# Authentification d'un principal du service à l'aide d'Azure Resource Manager

Cette rubrique explique comment autoriser un principal du service (tel qu’un processus automatisé, une application ou un service) à accéder aux autres ressources de votre abonnement. Azure Resource Manager vous permet d’utiliser le contrôle d’accès en fonction du rôle pour authentifier un principal du service et lui attribuer des actions autorisées. Cette rubrique explique comment utiliser PowerShell et Azure CLI pour authentifier le principal du service et lui attribuer un rôle.

Elle montre comment s’authentifier avec un nom d’utilisateur et un mot de passe ou un certificat.

Vous pouvez utiliser Azure PowerShell ou Azure CLI pour Mac, Linux et Windows. Si Azure PowerShell n’est pas installé sur votre système, consultez la page [Installation et configuration d’Azure PowerShell](./powershell-install-configure.md). Si Azure CLI n’est pas installé, consultez [Installation et configuration d’Azure CLI](xplat-cli-install.md). Pour plus d’informations sur l’utilisation du portail pour effectuer ces étapes, consultez la rubrique [Création de l'application Active Directory et du principal du service à l'aide du portail](resource-group-create-service-principal-portal.md)

## Concepts
1. Azure Active Directory (AAD) : service de gestion des identités et des accès pour le cloud. Pour plus d’informations, consultez la page [Qu’est-ce qu’Azure Active Directory ?](active-directory/active-directory-whatis.md)
2. Principal du service : instance d’application dans un répertoire ayant besoin d’accéder à d’autres ressources.
3. Application AD : enregistrement de répertoire qui identifie une application à AAD. Pour plus d’informations, consultez la page [Principes fondamentaux de l’authentification dans Azure AD](https://msdn.microsoft.com/library/azure/874839d9-6de6-43aa-9a5c-613b0c93247e#BKMK_Auth).

## Authentifier le principal du service avec mot de passe - PowerShell

Dans cette section, vous allez effectuer les étapes pour créer un principal du service pour une application Azure Active Directory, attribuer un rôle au principal du service et vous authentifier en tant que principal du service en fournissant l’identificateur d’application et un mot de passe.

[AZURE.INCLUDE [powershell-preview-inline-include](../includes/powershell-preview-inline-include.md)]

1. Connectez-vous à votre compte.

        PS C:\> Login-AzureRmAccount

1. Créez une application AAD à l’aide de la commande **New-AzureRmADApplication**. Indiquez le nom d’affichage de votre application, l’URI vers une page décrivant votre application (le lien n’est pas vérifié), les URI identifiant votre application et le mot de passe correspondant à l’identité de votre application.

        PS C:\> $azureAdApplication = New-AzureRmADApplication -DisplayName "<Your Application Display Name>" -HomePage "<https://YourApplicationHomePage>" -IdentifierUris "<https://YouApplicationUri>" -Password "<Your_Password>"

     Examinez le nouvel objet d’application. La propriété **ApplicationId** est nécessaire pour la création de principaux du service, l’attribution de rôles et l’acquisition de jetons JWT.

        PS C:\> $azureAdApplication

        Type                    : Application
        ApplicationId           : a41acfda-d588-47c9-8166-d659a335a865
        ApplicationObjectId     : a26aaa48-bd52-4a7f-b29f-1bebf74c91e3
        AvailableToOtherTenants : False
        AppPermissions          : {{
                            "claimValue": "user_impersonation",
                            "description": "Allow the application to access <Your Application Display Name> on behalf of the signed-in user.",
                            "directAccessGrantTypes": [],
                            "displayName": "Access <Your Application Display Name>",
                            "impersonationAccessGrantTypes": [
                              {
                                "impersonated": "User",
                                "impersonator": "Application"
                              }
                            ],
                            "isDisabled": false,
                            "origin": "Application",
                            "permissionId": "b866ef28-9abb-4698-8c8f-eb4328533831",
                            "resourceScopeType": "Personal",
                            "userConsentDescription": "Allow the application to access <Your Application Display Name> on your behalf.",
                            "userConsentDisplayName": "Access <Your Application Display Name>",
                            "lang": null
                          }}


2. Créez un principal du service pour votre application.

        PS C:\> New-AzureRmADServicePrincipal -ApplicationId $azureAdApplication.ApplicationId

     Vous avez désormais créé un principal du service dans le répertoire, mais aucune autorisation ni étendue n’a encore été attribuée au service. Vous devez autoriser explicitement le principal du service à effectuer des opérations dans une étendue donnée.

3. Accordez des autorisations au principal du service sur votre abonnement. Dans cet exemple, vous allez permettre au principal du service de lire toutes les ressources de l’abonnement. Pour le paramètre **ServicePrincipalName**, indiquez la propriété **ApplicationId** ou **IdentifierUris** que vous avez utilisée lors de la création de l’application. Pour plus d’informations sur le contrôle d’accès en fonction du rôle, consultez la page [Gestion et audit d’accès aux ressources](resource-group-rbac.md)

        PS C:\> New-AzureRmRoleAssignment -RoleDefinitionName Reader -ServicePrincipalName $azureAdApplication.ApplicationId

4. Récupérez l’abonnement dans lequel l’attribution de rôle a été créée. Cet abonnement sera utilisé ultérieurement pour récupérer le paramètre **TenantId** du client correspondant à l’attribution de rôle du principal du service.

        PS C:\> $subscription = Get-AzureRmSubscription

     Si vous avez créé l’attribution de rôle dans un abonnement autre que l’abonnement sélectionné, vous pouvez spécifier le paramètre **SubscriptoinId** ou **SubscriptionName** afin de récupérer un autre abonnement.

5. Pour vous connecter en tant que principal du service via PowerShell, créez un objet **PSCredential** contenant vos informations d’identification à l’aide de la commande **Get-Credential**.

        PS C:\> $creds = Get-Credential

     Vous serez invité à entrer vos informations d’identification.

     ![][1]

     Pour le nom d’utilisateur, utilisez le paramètre **ApplicationId** ou **IdentifierUris** que vous avez utilisé lors de la création de l’application. Utilisez le mot de passe que vous avez indiqué lors de la création du compte.

     Utilisez les informations d’identification que vous avez saisies comme entrée pour l’applet de commande **Login-AzureRmAccount** afin de connecter le principal du service :

        PS C:\> Login-AzureRmAccount -Credential $creds -ServicePrincipal -Tenant $subscription.TenantId
        Environment           : AzureCloud
        Account               : {guid}
        Tenant                : {guid}
        Subscription          : {guid}
        CurrentStorageAccount :

     Vous devriez maintenant être authentifié en tant que principal du service pour l’application AAD que vous avez créée.

6. Pour l’authentification à partir d’une application, incluez le code .NET suivant. Après avoir récupéré le jeton, vous pouvez accéder aux ressources dans l’abonnement.

        public static string GetAccessToken()
        {
          var authenticationContext = new AuthenticationContext("https://login.windows.net/{tenantId or tenant name}");  
          var credential = new ClientCredential(clientId: "{application id}", clientSecret: "{application password}");
          var result = authenticationContext.AcquireToken(resource: "https://management.core.windows.net/", clientCredential:credential);

          if (result == null) {
            throw new InvalidOperationException("Failed to obtain the JWT token");
          }

          string token = result.AccessToken;

          return token;
        }



## Authentifier le principal du service avec certificat - PowerShell

Dans cette section, vous allez effectuer les étapes pour créer un principal du service pour une application Azure Active Directory, attribuer un rôle au principal du service et vous authentifier en tant que principal du service en fournissant un certificat. Cette rubrique suppose que vous avez reçu un certificat.

Elle montre deux façons d’utiliser des certificats : informations d’identification de clé et valeurs de clés. Vous pouvez utiliser les deux approches.

Tout d’abord, vous devez configurer certaines valeurs dans PowerShell que vous allez utiliser ultérieurement lors de la création de l’application.

1. Connectez-vous à votre compte.

        PS C:\> Login-AzureRmAccount

1. Pour les deux approches, créez un objet X509Certificate à partir de votre certificat et récupérez la valeur de clé. Utilisez le chemin d’accès à votre certificat et le mot de passe pour ce certificat.

        $cert = New-Object -TypeName System.Security.Cryptography.X509Certificates.X509Certificate -ArgumentList @("C:\certificates\examplecert.pfx", "yourpassword")
        $keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData())

2. Si vous utilisez des informations d’identification de clé, créez l’objet d’informations d’identification de clé et affectez-lui la valeur `$keyValue` de l’étape précédente.

        $currentDate = Get-Date
        $endDate = $currentDate.AddYears(1)
        $keyId = [guid]::NewGuid()
        $keyCredential = New-Object  Microsoft.Azure.Commands.Resources.Models.ActiveDirectory.PSADKeyCredential
        $keyCredential.StartDate = $currentDate
        $keyCredential.EndDate= $endDate
        $keyCredential.KeyId = $keyId
        $keyCredential.Type = "AsymmetricX509Cert"
        $keyCredential.Usage = "Verify"
        $keyCredential.Value = $keyValue

3. Créez une application dans le répertoire. La première commande montre comment utiliser les valeurs de clés.

        $azureAdApplication = New-AzureRmADApplication -DisplayName "<Your Application Display Name>" -HomePage "<https://YourApplicationHomePage>" -IdentifierUris "<https://YouApplicationUri>" -KeyValue $keyValue -KeyType AsymmetricX509Cert       
        
    Vous pouvez aussi utiliser le deuxième exemple pour affecter des informations d’identification de clé.

         $azureAdApplication = New-AzureRmADApplication -DisplayName "<Your Application Display Name>" -HomePage "<https://YourApplicationHomePage>" -IdentifierUris "<https://YouApplicationUri>" -KeyCredentials $keyCredential

    Examinez le nouvel objet d’application. La propriété **ApplicationId** est nécessaire pour la création de principaux du service, l’attribution de rôles et l’acquisition de jetons JWT.

        PS C:\> $azureAdApplication

        Type                    : Application
        ApplicationId           : 76fa8d97-f07e-4b9a-b871-a57a7acd777a
        ApplicationObjectId     : c36b7b57-a949-4401-b381-18a5210aff10
        AvailableToOtherTenants : False
        AppPermissions          : {{
                            "claimValue": "user_impersonation",
                            "description": "Allow the application to access <Your Application Display Name> on behalf of the signed-in
                          user.",
                            "directAccessGrantTypes": [],
                            "displayName": "Access <Your Application Display Name>",
                            "impersonationAccessGrantTypes": [
                              {
                                "impersonated": "User",
                                "impersonator": "Application"
                              }
                            ],
                            "isDisabled": false,
                            "origin": "Application",
                            "permissionId": "9f13c6c6-35ba-43d6-b8b3-6a87aa641388",
                            "resourceScopeType": "Personal",
                            "userConsentDescription": "Allow the application to access <Your Application Display Name> on your behalf.",
                            "userConsentDisplayName": "Access <Your Application Display Name>",
                            "lang": null
                          }}

4. Créez un principal du service pour votre application.

        PS C:\> New-AzureRmADServicePrincipal -ApplicationId $azureAdApplication.ApplicationId

    Vous avez désormais créé un principal du service dans le répertoire, mais aucune autorisation ni étendue n’a encore été attribuée au service. Vous devez autoriser explicitement le principal du service à effectuer des opérations dans une étendue donnée.

5. Accordez des autorisations au principal du service sur votre abonnement. Dans cet exemple, vous allez permettre au principal du service de lire toutes les ressources de l’abonnement. Pour le paramètre **ServicePrincipalName**, indiquez la propriété **ApplicationId** ou **IdentifierUris** que vous avez utilisée lors de la création de l’application. Pour plus d’informations sur le contrôle d’accès en fonction du rôle, consultez la page [Gestion et audit d’accès aux ressources](resource-group-rbac.md)

        PS C:\> New-AzureRmRoleAssignment -RoleDefinitionName Reader -ServicePrincipalName $azureAdApplication.ApplicationId

6. Pour l’authentification à partir d’une application, incluez le code .NET suivant. Après avoir récupéré le client, vous pouvez accéder aux ressources dans l’abonnement.

        string clientId = "<Application ID for your AAD app>"; 
        var subscriptionId = "<Your Azure SubscriptionId>"; 
        string tenant = "<Tenant id or tenant name>"; 

        var authContext = new AuthenticationContext(string.Format("https://login.windows.net/{0}", tenant)); 

        X509Certificate2 cert = null; 
        X509Store store = new X509Store(StoreName.My, StoreLocation.CurrentUser); 
        string certName = "examplecert"; 
        try 
        { 
            store.Open(OpenFlags.ReadOnly); 
            var certCollection = store.Certificates; 
            var certs = certCollection.Find(X509FindType.FindBySubjectName, certName, false); 
            cert = certs[0]; 
        } 
        finally 
        { 
            store.Close(); 
        }        

        var certCred = new ClientAssertionCertificate(clientId, cert); 
        var token = authContext.AcquireToken("https://management.core.windows.net/", certCred); 
        var creds = new TokenCloudCredentials(subscriptionId, token.AccessToken); 
        var client = new ResourceManagementClient(creds); 
        

## Authentifier le principal du service avec mot de passe - Azure CLI

Commencez par créer un principal du service. Pour ce faire, nous allons utiliser la fonction Créer une application dans le répertoire. Cette section vous guidera à travers les étapes de création d’une application dans le répertoire.

1. Basculez en mode Azure Resource Manager et connectez-vous à votre compte.

        azure config mode arm
        azure login

2. Créez une application AAD en exécutant la commande **azure ad app create**. Indiquez le nom d’affichage de votre application, l’URI vers une page décrivant votre application (le lien n’est pas vérifié), les URI identifiant votre application et le mot de passe correspondant à l’identité de votre application.

        azure ad app create --name "<Your Application Display Name>" --home-page "<https://YourApplicationHomePage>" --identifier-uris "<https://YouApplicationUri>" --password <Your_Password>
        
    L’application Azure AD est renvoyée. La propriété ApplicationId est nécessaire pour la création de principaux du service, les affectations de rôles et l’acquisition de jetons JWT.

        info:    Executing command ad app create
        + Creating application exampleapp                                                
        data:    Application Id:          b57dd71d-036c-4840-865e-23b71d8098ec
        data:    Application Object Id:   d5c519e2-6149-447e-b323-88d2c4ea27de
        data:    Application Permissions:  
        data:                             claimValue:  user_impersonation
        data:                             description:  Allow the application to access exampleapp on behalf of the signed-in user.
        ...
        info:    ad app create command OK

3. Créez un principal du service pour votre application. Indiquez l’ID d’application renvoyé à l’étape précédente.

        azure ad sp create b57dd71d-036c-4840-865e-23b71d8098ec
        
    Le nouveau principal du service est renvoyé. L’ID d’objet est nécessaire lorsque vous accordez des autorisations.
    
        info:    Executing command ad sp create
        + Creating service principal for application b57dd71d-036c-4840-865e-23b71d8098ec
        data:    Object Id:               47193a0a-63e4-46bd-9bee-6a9f6f9c03cb
        data:    Display Name:            exampleapp
        ...
        info:    ad sp create command OK

    Vous avez désormais créé un principal du service dans le répertoire, mais aucune autorisation ni étendue n’a encore été attribuée au service. Vous devez autoriser explicitement le principal du service à effectuer des opérations dans une étendue donnée.

4. Accordez des autorisations au principal du service sur votre abonnement. Dans cet exemple, vous allez permettre au principal du service de lire toutes les ressources de l’abonnement. Pour le paramètre **ServicePrincipalName**, indiquez la propriété **ApplicationId** ou **IdentifierUris** que vous avez utilisée lors de la création de l’application. Pour plus d’informations sur le contrôle d’accès en fonction du rôle, consultez la page [Gestion et audit d’accès aux ressources](resource-group-rbac.md)

        azure role assignment create --objectId 47193a0a-63e4-46bd-9bee-6a9f6f9c03cb -o Reader -c /subscriptions/{subscriptionId}/

5. Déterminez le paramètre **TenantId** du client correspondant à l’attribution de rôle du principal du service en affichant les comptes sous forme de liste et en recherchant la propriété **TenantId** dans la sortie.

        azure account list --json

6. Connectez-vous en vous identifiant en tant que principal du service. Pour le nom d’utilisateur, utilisez le paramètre **ApplicationId** que vous avez utilisé lors de la création de l’application. Utilisez le mot de passe que vous avez indiqué lors de la création du compte.

        azure login -u "<ApplicationId>" -p "<password>" --service-principal --tenant "<TenantId>"

    Vous devriez maintenant être authentifié en tant que principal du service pour l’application AAD que vous avez créée.

## Authentifier le principal du service avec certificat - Interface de ligne de commande Azure

Dans cette section, vous suivrez la procédure vous permettant de créer le principal du service d'une application Azure Active Directory qui utilise un certificat pour l'authentification. Cette rubrique part du principe que vous avez reçu un certificat et que vous disposez d’[OpenSSL](http://www.openssl.org/).

1. Créez un fichier **.pem** avec :

        openssl.exe pkcs12 -in examplecert.pfx -out examplecert.pem -nodes

2. Ouvrez le fichier **.pem** et copiez les données de certificat.

3. Créez une application AAD à l’aide de la commande **azure ad app create** et fournissez les données de certificat que vous avez copiées à l'étape précédente en tant que valeur de clé.

        azure ad app create -n "<your application name>" --home-page "<https://YourApplicationHomePage>" -i "<https://YouApplicationUri>" --key-value <certificate data>

## Étapes suivantes
  
- Pour obtenir une vue d'ensemble du contrôle d'accès en fonction du rôle, consultez la rubrique [Gestion et audit d'accès aux ressources](resource-group-rbac.md)  
- Pour en savoir plus sur l’utilisation du portail avec les principaux du service, consultez la rubrique [Création d’un principal du service Azure à l’aide du portail Azure](./resource-group-create-service-principal-portal.md)  
- Pour obtenir des instructions sur l'implémentation de la sécurité avec Azure Resource Manager, consultez la rubrique [Questions de sécurité relatives à Azure Resource Manager](best-practices-resource-manager-security.md).


<!-- Images. -->
[1]: ./media/resource-group-authenticate-service-principal/arm-get-credential.png

<!---HONumber=AcomDC_1203_2015-->