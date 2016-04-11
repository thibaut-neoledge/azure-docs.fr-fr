<properties
   pageTitle="Authentification d’un principal du service à l’aide d’Azure Resource Manager | Microsoft Azure"
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
   ms.date="03/10/2016"
   ms.author="tomfitz"/>

# Authentification d'un principal du service à l'aide d'Azure Resource Manager

Cette rubrique explique comment autoriser un principal du service (tel qu’un processus automatisé, une application ou un service) à accéder aux autres ressources de votre abonnement. Azure Resource Manager vous permet d’utiliser le contrôle d’accès en fonction du rôle pour authentifier un principal du service et lui attribuer des actions autorisées.

Cette rubrique explique comment utiliser Azure PowerShell ou l’interface de ligne de commande Azure (CLI) pour Mac, Linux et Windows afin de créer une application et un principal du service, d’attribuer un rôle à un principal du service et de vous authentifier en tant que principal du service. Si Azure PowerShell n’est pas installé sur votre système, consultez la page [Installation et configuration d’Azure PowerShell](./powershell-install-configure.md). Si Azure CLI n’est pas installé, consultez [Installation et configuration d’Azure CLI](xplat-cli-install.md). Pour plus d’informations sur l’utilisation du portail pour effectuer ces étapes, consultez la rubrique [Création de l'application Active Directory et du principal du service à l'aide du portail](resource-group-create-service-principal-portal.md)

## Concepts
1. Azure Active Directory : service de gestion des identités et des accès pour le cloud. Pour plus d’informations, consultez [Qu’est-ce qu’Azure Active Directory ?](active-directory/active-directory-whatis.md)
2. Principal du service : instance d’application dans un répertoire ayant besoin d’accéder à d’autres ressources.
3. Application Active Directory : enregistrement de répertoire qui identifie une application à AAD.

Pour obtenir une explication plus détaillée des applications et des principaux du service, consultez la rubrique [Objets principal du service et application](active-directory/active-directory-application-objects.md). Pour plus d'informations sur l'authentification Active Directory, consultez la rubrique [Scénarios d'authentification pour Azure AD](active-directory/active-directory-authentication-scenarios.md).

Cette rubrique décrit 4 façons de créer une application Active Directory et d’authentification cette application. Ces méthodes varient selon que vous souhaitez utiliser un mot de passe ou un certificat pour vous authentifier, et que vous préférez utiliser PowerShell ou Azure CLI. Ces méthodes sont les suivantes :

- [Authentification par mot de passe - PowerShell](#authenticate-with-password---powershell)
- [Authentification par un certificat - PowerShell](#authenticate-with-certificate---powershell)
- [Authentification par mot de passe - Azure CLI](#authenticate-with-password---azure-cli)
- [Authentification par un certificat - Azure CLI](#authenticate-with-certificate---azure-cli)

## Authentification par mot de passe avec PowerShell

Dans cette section, vous allez effectuer les étapes pour créer un principal du service pour une application Azure Active Directory, attribuer un rôle au principal du service et vous authentifier en tant que principal du service en fournissant l’identificateur d’application et un mot de passe.

1. Connectez-vous à votre compte.

        PS C:\> Login-AzureRmAccount

1. Créez une application Active Directory à l’aide de la commande **New-AzureRmADApplication**. Indiquez le nom d’affichage de votre application, l’URI vers une page décrivant votre application (le lien n’est pas vérifié), les URI identifiant votre application et le mot de passe correspondant à l’identité de votre application.

        PS C:\> $azureAdApplication = New-AzureRmADApplication -DisplayName "exampleapp" -HomePage "https://www.contoso.org" -IdentifierUris "https://www.contoso.org/example" -Password "<Your_Password>"

     Examinez le nouvel objet d’application. La propriété **ApplicationId** est nécessaire pour la création de principaux du service, les affectations de rôles et l’acquisition du jeton d’accès.

        PS C:\> $azureAdApplication

        DisplayName             : exampleapp
        Type                    : Application
        ApplicationId           : 8bc80782-a916-47c8-a47e-4d76ed755275
        ApplicationObjectId     : c95e67a3-403c-40ac-9377-115fa48f8f39
        AvailableToOtherTenants : False
        AppPermissions          : {}
        IdentifierUris          : {https://www.contoso.org/example}
        ReplyUrls               : {}

2. Créez un principal du service pour votre application en transmettant l’ID d’application de l’application Active Directory.

        PS C:\> New-AzureRmADServicePrincipal -ApplicationId $azureAdApplication.ApplicationId

     Vous avez désormais créé un principal du service dans le répertoire, mais aucune autorisation ni étendue n’a encore été attribuée au service. Vous devez autoriser explicitement le principal du service à effectuer des opérations dans une étendue donnée.

3. Accordez des autorisations au principal du service sur votre abonnement. Dans cet exemple, vous allez permettre au principal du service de lire toutes les ressources de l’abonnement. Pour le paramètre **ServicePrincipalName**, indiquez la propriété **ApplicationId** ou **IdentifierUris** que vous avez utilisée lors de la création de l’application. Pour plus d’informations sur le contrôle d’accès en fonction du rôle, consultez [Contrôle d’accès en fonction du rôle Azure](./active-directory/role-based-access-control-configure.md).

        PS C:\> New-AzureRmRoleAssignment -RoleDefinitionName Reader -ServicePrincipalName $azureAdApplication.ApplicationId

Vous avez créé une application Active Directory et un principal du service pour votre application. Vous avez également assigné un rôle au principal du service. Vous devez maintenant ouvrir une session en tant que principal du service pour pouvoir effectuer des opérations de principal du service. Trois options sont présentées dans cette rubrique :

- [Fournir manuellement des informations d’identification via PowerShell](#manually-provide-credentials-through-powershell)
- [Fournir des informations d’identification via un script PowerShell automatisé](#provide-credentials-through-automated-powershell-script)
- [Fournir des informations d’identification via un code intégré à une application](#provide-credentials-through-code-in-an-application)

<a id="manually-provide-credentials-through-powershell" />
### Fournir manuellement des informations d’identification via PowerShell

Vous pouvez renseigner manuellement les informations d’identification du principal du service lors de l’exécution de script ou de commandes à la demande.

1. Créez un objet **PSCredential** contenant vos informations d’identification à l’aide de la commande **Get-Credential**.

        PS C:\> $creds = Get-Credential

2. Vous serez invité à entrer vos informations d’identification. Pour le nom d’utilisateur, utilisez le paramètre **ApplicationId** ou **IdentifierUris** que vous avez utilisé lors de la création de l’application. Utilisez le mot de passe que vous avez indiqué lors de la création du compte.

     ![saisir les informations d’identification][1]

3. Récupérez l’abonnement dans lequel l’attribution de rôle a été créée. Cet abonnement sera utilisé pour récupérer le paramètre **TenantId** du client correspondant à l’attribution de rôle du principal du service.

        PS C:\> $subscription = Get-AzureRmSubscription

     Si vous avez créé l’attribution de rôle dans un abonnement autre que l’abonnement sélectionné, vous pouvez spécifier le paramètre **SubscriptoinId** ou **SubscriptionName** afin de récupérer un autre abonnement.

4. Connectez-vous en tant que principal du service à l’aide de l’applet de commande **Login-AzureRmAccount**, en veillant à fournir l’objet informations d’identification et à indiquer que ce compte est un principal de service.

        PS C:\> Login-AzureRmAccount -Credential $creds -ServicePrincipal -Tenant $subscription.TenantId
        Environment           : AzureCloud
        Account               : {guid}
        Tenant                : {guid}
        Subscription          : {guid}
        CurrentStorageAccount :

Vous êtes maintenant authentifié en tant que principal du service pour l’application Active Directory que vous avez créée.

<a id="provide-credentials-through-automated-powershell-script" />
### Fournir des informations d’identification via un script PowerShell automatisé

Cette section explique comment se connecter en tant que principal du service sans avoir à saisir manuellement les informations d’identification. Il est inutile de renseigner manuellement le mot de passe, car celui-ci est récupéré à partir d’un coffre de clés.

> [AZURE.NOTE] L’intégration directe d’un mot de passe dans votre script PowerShell présente des risques dans la mesure où le mot de passe est exposé de façon visible. Il est plutôt recommandé d’utiliser un service (un coffre de clés, par exemple) pour stocker le mot de passe et le récupérer ensuite lors de l’exécution du script.

Ces étapes supposent que vous avez défini un coffre de clés et une clé secrète permettant de stocker le mot de passe. Pour déployer un coffre de clés et une clé secrète via un modèle, consultez [Format de modèle de coffre de clés](). Pour en savoir plus sur les coffres de clés, consultez [Prise en main du coffre de clés Azure](./key-vault/key-vault-get-started.md).

1. Récupérez votre mot de passe (dans l’exemple ci-dessous, le mot de passe est stocké en tant que clé secrète sous le nom **appPassword**) dans le coffre de clés.

        PS C:\> $secret = Get-AzureKeyVaultSecret -VaultName examplevault -Name appPassword
        
2. Accédez à votre application Active Directory. Vous avez besoin de l’ID d’application au moment de la connexion.

        PS C:\> $azureAdApplication = Get-AzureRmADApplication -IdentifierUri "https://www.contoso.org/example"

3. Créez un objet **PSCredential** et identifiez-le à l’aide de l’ID d’application et du mot de passe.

        PS C:\> $creds = New-Object System.Management.Automation.PSCredential ($azureAdApplication.ApplicationId, $secret.SecretValue)
    
4. Récupérez l’abonnement dans lequel l’attribution de rôle a été créée. Cet abonnement sera utilisé ultérieurement pour récupérer le paramètre **TenantId** du client correspondant à l’attribution de rôle du principal du service.

        PS C:\> $subscription = Get-AzureRmSubscription

     Si vous avez créé l’attribution de rôle dans un abonnement autre que l’abonnement sélectionné, vous pouvez spécifier le paramètre **SubscriptoinId** ou **SubscriptionName** afin de récupérer un autre abonnement.

5. Connectez-vous en tant que principal du service à l’aide de l’applet de commande **Login-AzureRmAccount**, en veillant à fournir l’objet informations d’identification et à indiquer que ce compte est un principal de service.
    
        PS C:\> Login-AzureRmAccount -Credential $creds -ServicePrincipal -TenantId $subscription.TenantId

Vous êtes maintenant authentifié en tant que principal du service pour l’application Active Directory que vous avez créée.

<a id="provide-credentials-through-code-in-an-application" />
### Fournir des informations d’identification via un code intégré à une application

Pour l’authentification à partir d’une application .NET, incluez le code suivant. Vous avez besoin de l’ID d’application de l’application Active Directory, du mot de passe et de l’ID client de l’abonnement. Après avoir récupéré le jeton d’accès, vous pouvez utiliser les ressources contenues dans l’abonnement.

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


## Authentification par un certificat via PowerShell

Dans cette section, vous allez effectuer les étapes pour créer un principal du service pour une application Azure Active Directory, attribuer un rôle au principal du service et vous authentifier en tant que principal du service en fournissant un certificat. Cette rubrique suppose que vous avez reçu un certificat.

Elle montre deux façons d’utiliser des certificats : informations d’identification de clé et valeurs de clés. Vous pouvez utiliser les deux approches.

Tout d’abord, vous devez configurer certaines valeurs dans PowerShell que vous allez utiliser ultérieurement lors de la création de l’application.

1. Connectez-vous à votre compte.

        PS C:\> Login-AzureRmAccount

1. Pour les deux approches, créez un objet X509Certificate2 à partir de votre certificat et récupérez la valeur de clé. Utilisez le chemin d’accès à votre certificat et le mot de passe pour ce certificat.

        $cert = New-Object -TypeName System.Security.Cryptography.X509Certificates.X509Certificate2 -ArgumentList @("C:\certificates\examplecert.pfx", "yourpassword")
        $keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData())

2. Si vous utilisez des informations d’identification de clé, créez l’objet d’informations d’identification de clé et affectez-lui la valeur `$keyValue` de l’étape précédente. L’exemple suivant suppose d’appeler `Add-Type` pour ajouter un type à partir d’un assembly. Le chemin d’accès indiqué dans l’exemple devrait être similaire à celui que vous obtenez, à quelques différences près.

        Add-Type -Path 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ResourceManager\AzureResourceManager\AzureRM.Resources\Microsoft.Azure.Commands.Resources.dll'
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

        $azureAdApplication = New-AzureRmADApplication -DisplayName "exampleapp" -HomePage "https://www.contoso.org" -IdentifierUris "https://www.contoso.org/example" -KeyValue $keyValue -KeyType AsymmetricX509Cert       
        
    Vous pouvez aussi utiliser le deuxième exemple pour affecter des informations d’identification de clé.

         $azureAdApplication = New-AzureRmADApplication -DisplayName "example" -HomePage "https://www.contoso.org" -IdentifierUris "https://www.contoso.org/example" -KeyCredentials $keyCredential

    Examinez le nouvel objet d’application. La propriété **ApplicationId** est nécessaire pour la création de principaux du service, les affectations de rôles et l’acquisition de jeton d’accès.

        PS C:\> $azureAdApplication

        DisplayName             : exampleapp
        Type                    : Application
        ApplicationId           : 1975a4fd-1528-4086-a992-787dbd23c46b
        ApplicationObjectId     : 9665e5f3-84b7-4344-92e2-8cc20ad16a8b
        AvailableToOtherTenants : False
        AppPermissions          : {}
        IdentifierUris          : {https://www.contoso.org/example}
        ReplyUrls               : {}       

4. Créez un principal du service pour votre application en transmettant l’ID d’application de l’application Active Directory.

        PS C:\> New-AzureRmADServicePrincipal -ApplicationId $azureAdApplication.ApplicationId

    Vous avez désormais créé un principal du service dans le répertoire, mais aucune autorisation ni étendue n’a encore été attribuée au service. Vous devez autoriser explicitement le principal du service à effectuer des opérations dans une étendue donnée.

5. Accordez des autorisations au principal du service sur votre abonnement. Dans cet exemple, vous allez permettre au principal du service de lire toutes les ressources de l’abonnement. Pour le paramètre **ServicePrincipalName**, indiquez la propriété **ApplicationId** ou **IdentifierUris** que vous avez utilisée lors de la création de l’application. Pour plus d’informations sur le contrôle d’accès en fonction du rôle, consultez [Contrôle d’accès en fonction du rôle Azure](./active-directory/role-based-access-control-configure.md).

        PS C:\> New-AzureRmRoleAssignment -RoleDefinitionName Reader -ServicePrincipalName $azureAdApplication.ApplicationId

Vous avez créé une application Active Directory et un principal du service pour votre application. Vous avez également assigné un rôle au principal du service. Vous devez maintenant ouvrir une session en tant que principal du service pour pouvoir effectuer des opérations de principal du service. Deux options sont présentées dans cette rubrique :

- [Fournir un certificat via un script PowerShell automatisé](#provide-certificate-through-automated-powershell-script)
- [Fournir un certificat via un code intégré à une application](#provide-certificate-through-code-in-an-application)

<a id="provide-certificate-through-automated-powershell-script" />
### Fournir un certificat via un script PowerShell automatisé

1. Accédez à votre application Active Directory. Vous avez besoin de l’ID d’application au moment de la connexion.

        PS C:\> $azureAdApplication = Get-AzureRmADApplication -IdentifierUri "https://www.contoso.org/example"
        
2. Récupérez l’abonnement dans lequel l’attribution de rôle a été créée. Cet abonnement sera utilisé ultérieurement pour récupérer le paramètre **TenantId** du client correspondant à l’attribution de rôle du principal du service.

        PS C:\> $subscription = Get-AzureRmSubscription

     Si vous avez créé l’attribution de rôle dans un abonnement autre que l’abonnement sélectionné, vous pouvez spécifier le paramètre **SubscriptoinId** ou **SubscriptionName** afin de récupérer un autre abonnement.

3. Récupérez le certificat que vous allez utiliser pour l’authentification.

        PS C:\> $cert = New-Object -TypeName System.Security.Cryptography.X509Certificates.X509Certificate2 -ArgumentList @("C:\certificates\examplecert.pfx", "yourpassword")

4. Pour vous authentifier dans PowerShell, vous devez fournir l’empreinte du certificat, l’ID d’application et l’ID de client.

        PS C:\> Login-AzureRmAccount -CertificateThumbprint $cert.Thumbprint -ApplicationId $azureAdApplication.ApplicationId -ServicePrincipal -TenantId $subscription.TenantId

Vous êtes maintenant authentifié en tant que principal du service pour l’application Active Directory que vous avez créée.

<a id="provide-certificate-through-code-in-an-application" />
### Fournir un certificat via un code intégré à une application

Pour l’authentification à partir d’une application .NET, incluez le code suivant. Après avoir récupéré le client, vous pouvez accéder aux ressources dans l’abonnement.

    var clientId = "<Application ID for your AAD app>"; 
    var subscriptionId = "<Your Azure SubscriptionId>"; 
    var tenant = "<Tenant id or tenant name>"; 
    var certName = "examplecert"; 

    var authContext = new AuthenticationContext(string.Format("https://login.windows.net/{0}", tenant)); 

    X509Certificate2 cert = null; 
    X509Store store = new X509Store(StoreName.My, StoreLocation.CurrentUser); 
    
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
    // If using the new resource manager package like "Microsoft.Azure.ResourceManager" version="1.0.0-preview" use below
    var creds = new TokenCredentials(token.AccessToken); 
    // Else if using older package versions like Microsoft.Azure.Management.Resources" version="3.4.0-preview" use below
    // var creds = new TokenCloudCredentials(subscriptionId, token.AccessToken);
    var client = new ResourceManagementClient(creds); 
        

## Authentification par mot de passe via Azure CLI

Commencez par créer un principal du service. Pour ce faire, nous allons utiliser la fonction Créer une application dans le répertoire. Cette section vous guidera à travers les étapes de création d’une application dans le répertoire.

1. Basculez en mode Azure Resource Manager et connectez-vous à votre compte.

        azure config mode arm
        azure login

2. Créez une application Active Directory en exécutant la commande **azure ad app create**. Indiquez le nom d’affichage de votre application, l’URI vers une page décrivant votre application (le lien n’est pas vérifié), les URI identifiant votre application et le mot de passe correspondant à l’identité de votre application.

        azure ad app create --name "exampleapp" --home-page "https://www.contoso.org" --identifier-uris "https://www.contoso.org/example" --password <Your_Password>
        
    L’application Active Directory est renvoyée. La propriété ApplicationId est nécessaire pour la création de principaux du service, les affectations de rôles et l’acquisition de jeton d’accès.

        data:    AppId:          4fd39843-c338-417d-b549-a545f584a745
        data:    ObjectId:       4f8ee977-216a-45c1-9fa3-d023089b2962
        data:    DisplayName:    exampleapp
        ...
        info:    ad app create command OK

3. Créez un principal du service pour votre application. Indiquez l’ID d’application renvoyé à l’étape précédente.

        azure ad sp create 4fd39843-c338-417d-b549-a545f584a745
        
    Le nouveau principal du service est renvoyé. L’ID d’objet est nécessaire lorsque vous accordez des autorisations.
    
        info:    Executing command ad sp create
        - Creating service principal for application 4fd39843-c338-417d-b549-a545f584a74+
        data:    Object Id:        7dbc8265-51ed-4038-8e13-31948c7f4ce7
        data:    Display Name:     exampleapp
        data:    Service Principal Names:
        data:                      4fd39843-c338-417d-b549-a545f584a745
        data:                      https://www.contoso.org/example
        info:    ad sp create command OK

    Vous avez désormais créé un principal du service dans le répertoire, mais aucune autorisation ni étendue n’a encore été attribuée au service. Vous devez autoriser explicitement le principal du service à effectuer des opérations dans une étendue donnée.

4. Accordez des autorisations au principal du service sur votre abonnement. Dans cet exemple, vous allez permettre au principal du service de lire toutes les ressources de l’abonnement. Pour plus d’informations sur le contrôle d’accès en fonction du rôle, consultez [Contrôle d’accès en fonction du rôle Azure](./active-directory/role-based-access-control-configure.md).

        azure role assignment create --objectId 7dbc8265-51ed-4038-8e13-31948c7f4ce7 -o Reader -c /subscriptions/{subscriptionId}/

Vous avez créé une application Active Directory et un principal du service pour votre application. Vous avez également assigné un rôle au principal du service. Vous devez maintenant ouvrir une session en tant que principal du service pour pouvoir effectuer des opérations de principal du service. Trois options sont présentées dans cette rubrique :

- [Fournir manuellement des informations d’identification via Azure CLI](#manually-provide-credentials-through-azure-cli)
- [Fournir des informations d’identification via un script Azure CLI automatisé](#provide-credentials-through-automated-azure-cli-script)
- [Fournir des informations d’identification via un code intégré à une application](#provide-credentials-through-code-in-an-application-cli)

<a id="manually-provide-credentials-through-Azure-cli" />
### Fournir manuellement des informations d’identification via Azure CLI

Si vous souhaitez vous connecter manuellement en tant que principal du service, vous pouvez utiliser la commande **azure login**. Vous devez renseigner l’ID de client, l’ID d’application et le mot de passe. L’intégration directe d’un mot de passe dans un script présente des risques dans la mesure où le mot de passe est stocké dans le fichier. Consultez la section suivante pour découvrir une meilleure alternative lors de l’exécution d’un script automatisé.

1. Déterminez le paramètre **TenantId** de l’abonnement qui contient le principal du service. Si vous récupérez l’ID de client pour votre abonnement actuellement authentifié, il est inutile de fournir l’ID d’abonnement en tant que paramètre. Le commutateur **- r** récupère la valeur sans guillemets.

        tenantId=$(azure account show -s <subscriptionId> --json | jq -r '.[0].tenantId')

2. Pour le nom d’utilisateur, utilisez le paramètre **AppId** que vous avez utilisé lors de la création du principal du service. Si vous avez besoin de récupérer l’ID d’application, utilisez la commande suivante. Indiquez le nom de l’application Active Directory dans le paramètre **search**.

        appId=$(azure ad app show --search exampleapp --json | jq -r '.[0].appId')

3. Connectez-vous en tant que principal du service.

        azure login -u "$appId" --service-principal --tenant "$tenantId"

À l’invite, entrez le mot de passe que vous avez indiqué lors de la création du compte.

    info:    Executing command login
    Password: ********

Vous êtes maintenant authentifié en tant que principal du service pour l’application Active Directory que vous avez créée.

<a id="provide-credentials-through-automated-azure-cli-script" />
### Fournir des informations d’identification via un script Azure CLI automatisé

Cette section explique comment se connecter en tant que principal du service sans avoir à saisir manuellement les informations d’identification.

> [AZURE.NOTE] L’intégration d’un mot de passe dans votre script Azure CLI présente des risques dans la mesure où le mot de passe est exposé de façon visible. Il est plutôt recommandé d’utiliser un service (un coffre de clés, par exemple) pour stocker le mot de passe et le récupérer ensuite lors de l’exécution du script.

Ces étapes supposent que vous avez défini un coffre de clés et une clé secrète permettant de stocker le mot de passe. Pour déployer un coffre de clés et une clé secrète via un modèle, consultez [Format de modèle de coffre de clés](). Pour en savoir plus sur les coffres de clés, consultez [Prise en main du coffre de clés Azure](./key-vault/key-vault-get-started.md).

1. Récupérez votre mot de passe (dans l’exemple ci-dessous, le mot de passe est stocké en tant que clé secrète sous le nom **appPassword**) dans le coffre de clés. Incluez le commutateur **-r** pour supprimer les guillemets ouvrants et fermants renvoyés par la sortie JSON.

        secret=$(azure keyvault secret show --vault-name examplevault --secret-name appPassword --json | jq -r '.value')
    
2. Déterminez le paramètre **TenantId** de l’abonnement qui contient le principal du service. Si vous récupérez l’ID de client pour votre abonnement actuellement authentifié, il est inutile de fournir l’ID d’abonnement en tant que paramètre.

        tenantId=$(azure account show -s <subscriptionId> --json | jq -r '.[0].tenantId')

3. Pour le nom d’utilisateur, utilisez le paramètre **AppId** que vous avez utilisé lors de la création du principal du service. Si vous avez besoin de récupérer l’ID d’application, utilisez la commande suivante. Indiquez le nom de l’application Active Directory dans le paramètre **search**.

        appId=$(azure ad app show --search exampleapp --json | jq -r '.[0].appId')

4. Connectez-vous en tant que principal du service en indiquant l’ID d’application, le mot de passe obtenu à partir du coffre de clés, ainsi que l’ID de client.

        azure login -u "$appId" -p "$secret" --service-principal --tenant "$tenantId"
    
Vous êtes maintenant authentifié en tant que principal du service pour l’application Active Directory que vous avez créée.

<a id="provide-credentials-through-code-in-an-application-cli" />
### Fournir des informations d’identification via un code intégré à une application

Pour l’authentification à partir d’une application .NET, incluez le code suivant. Vous avez besoin de l’ID d’application de l’application Active Directory, du mot de passe et de l’ID client de l’abonnement. Après avoir récupéré le jeton d’accès, vous pouvez utiliser les ressources contenues dans l’abonnement.

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

## Authentification par un certificat via Azure CLI

Dans cette section, vous suivrez la procédure vous permettant de créer le principal du service d'une application Azure Active Directory qui utilise un certificat pour l'authentification. Cette rubrique part du principe que vous avez reçu un certificat et que vous disposez d’[OpenSSL](http://www.openssl.org/).

1. Créez un fichier **.pem** avec :

        openssl pkcs12 -in C:\certificates\examplecert.pfx -out C:\certificates\examplecert.pem -nodes

2. Ouvrez le fichier **.pem** et copiez les données de certificat. Recherchez la longue séquence de caractères entre **---BEGIN CERTIFICATE---** et **---END CERTIFICATE---**.

3. Créez une application Active Directory à l’aide de la commande **azure ad app create** et fournissez les données de certificat que vous avez copiées à l’étape précédente en tant que valeur de clé.

        azure ad app create -n "exampleapp" --home-page "https://www.contoso.org" -i "https://www.contoso.org/example" --key-value <certificate data>

    L’application Active Directory est renvoyée. La propriété ApplicationId est nécessaire pour la création de principaux du service, les affectations de rôles et l’acquisition de jeton d’accès.

        data:    AppId:          4fd39843-c338-417d-b549-a545f584a745
        data:    ObjectId:       4f8ee977-216a-45c1-9fa3-d023089b2962
        data:    DisplayName:    exampleapp
        ...
        info:    ad app create command OK

4. Créez un principal du service pour votre application. Indiquez l’ID d’application renvoyé à l’étape précédente.

        azure ad sp create 4fd39843-c338-417d-b549-a545f584a745
        
    Le nouveau principal du service est renvoyé. L’ID d’objet est nécessaire lorsque vous accordez des autorisations.
    
        info:    Executing command ad sp create
        - Creating service principal for application 4fd39843-c338-417d-b549-a545f584a74+
        data:    Object Id:        7dbc8265-51ed-4038-8e13-31948c7f4ce7
        data:    Display Name:     exampleapp
        data:    Service Principal Names:
        data:                      4fd39843-c338-417d-b549-a545f584a745
        data:                      https://www.contoso.org/example
        info:    ad sp create command OK
        
5. Accordez des autorisations au principal du service sur votre abonnement. Dans cet exemple, vous allez permettre au principal du service de lire toutes les ressources de l’abonnement. Pour plus d’informations sur le contrôle d’accès en fonction du rôle, consultez [Contrôle d’accès en fonction du rôle Azure](./active-directory/role-based-access-control-configure.md).

        azure role assignment create --objectId 7dbc8265-51ed-4038-8e13-31948c7f4ce7 -o Reader -c /subscriptions/{subscriptionId}/

Vous avez créé une application Active Directory et un principal du service pour votre application. Vous avez également assigné un rôle au principal du service. Vous devez maintenant ouvrir une session en tant que principal du service pour pouvoir effectuer des opérations de principal du service. Deux options sont présentées dans cette rubrique :

- [Fournir un certificat via un script Azure CLI automatisé](#provide-certificate-through-automated-azure-cli-script)
- [Fournir un certificat via un code intégré à une application](#provide-certificate-through-code-in-an-application-cli)

<a id="provide-certificate-through-automated-azure-cli-script" />
### Fournir un certificat via un script Azure CLI automatisé

1. Vous devez récupérer l’empreinte de certificat et supprimer les caractères inutiles.

        cert=$(openssl x509 -in "C:\certificates\examplecert.pem" -fingerprint -noout | sed 's/SHA1 Fingerprint=//g'  | sed 's/://g')
    
     Vous obtenez alors une valeur d’empreinte semblable à ce qui suit :

        30996D9CE48A0B6E0CD49DBB9A48059BF9355851

2. Déterminez le paramètre **TenantId** de l’abonnement qui contient le principal du service. Si vous récupérez l’ID de client pour votre abonnement actuellement authentifié, il est inutile de fournir l’ID d’abonnement en tant que paramètre. Le commutateur **- r** récupère la valeur sans guillemets.

        tenantId=$(azure account show -s <subscriptionId> --json | jq -r '.[0].tenantId')

3. Pour le nom d’utilisateur, utilisez le paramètre **AppId** que vous avez utilisé lors de la création du principal du service. Si vous avez besoin de récupérer l’ID d’application, utilisez la commande suivante. Indiquez le nom de l’application Active Directory dans le paramètre **search**.

        appId=$(azure ad app show --search exampleapp --json | jq -r '.[0].appId')

4. Pour vous authentifier avec Azure CLI, vous devez fournir l’empreinte du certificat, le fichier de certificat, l’ID d’application et l’ID de client.

        azure login --service-principal --tenant "$tenantId" -u "$appId" --certificate-file C:\certificates\examplecert.pem --thumbprint "$cert"

Vous êtes maintenant authentifié en tant que principal du service pour l’application Active Directory que vous avez créée.

<a id="provide-certificate-through-code-in-an-application-cli" />
### Fournir un certificat via un code intégré à une application

Pour l’authentification à partir d’une application .NET, incluez le code suivant. Après avoir récupéré le client, vous pouvez accéder aux ressources dans l’abonnement.

    var clientId = "<Application ID for your AAD app>"; 
    var subscriptionId = "<Your Azure SubscriptionId>"; 
    var tenant = "<Tenant id or tenant name>"; 
    var certName = "examplecert"; 

    var authContext = new AuthenticationContext(string.Format("https://login.windows.net/{0}", tenant)); 

    X509Certificate2 cert = null; 
    X509Store store = new X509Store(StoreName.My, StoreLocation.CurrentUser); 
    
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
    // If using the new resource manager package like "Microsoft.Azure.ResourceManager" version="1.0.0-preview" use below
    var creds = new TokenCredentials(token.AccessToken); 
    // Else if using older package versions like Microsoft.Azure.Management.Resources" version="3.4.0-preview" use below
    // var creds = new TokenCloudCredentials(subscriptionId, token.AccessToken);
    var client = new ResourceManagementClient(creds); 
       
Pour obtenir des informations supplémentaires sur l’utilisation de certificats et d’Azure CLI, consultez la page [Authentification par certificat à l’aide de principaux du service Azure à partir de la ligne de commande Linux (en anglais)](http://blogs.msdn.com/b/arsen/archive/2015/09/18/certificate-based-auth-with-azure-service-principals-from-linux-command-line.aspx)

## Étapes suivantes
  
- Pour en savoir plus sur l’utilisation du portail avec les principaux du service, consultez la rubrique [Création d’un principal du service Azure à l’aide du portail Azure](./resource-group-create-service-principal-portal.md)  
- Pour obtenir des instructions sur l'implémentation de la sécurité avec Azure Resource Manager, consultez la rubrique [Questions de sécurité relatives à Azure Resource Manager](best-practices-resource-manager-security.md).


<!-- Images. -->
[1]: ./media/resource-group-authenticate-service-principal/arm-get-credential.png

<!---HONumber=AcomDC_0330_2016-->