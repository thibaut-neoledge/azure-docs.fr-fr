<properties
   pageTitle="Créer une application AD à l’aide de PowerShell | Microsoft Azure"
   description="Décrit l’utilisation d’Azure PowerShell pour créer une application Active Directory et lui accorder l’accès aux ressources par le biais du contrôle d’accès en fonction du rôle. Cet article montre comment authentifier l’application avec un mot de passe ou un certificat."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="multiple"
   ms.workload="na"
   ms.date="05/26/2016"
   ms.author="tomfitz"/>

# Utiliser Azure PowerShell pour créer une application Active Directory pour accéder aux ressources

> [AZURE.SELECTOR]
- [PowerShell](resource-group-authenticate-service-principal.md)
- [Interface de ligne de commande Azure](resource-group-authenticate-service-principal-cli.md)
- [Portail](resource-group-create-service-principal-portal.md)


Cette rubrique vous montre comment utiliser [Azure PowerShell](powershell-install-configure.md) pour créer une application Active Directory (AD), telle qu’un processus, une application ou un service automatisé, qui peut accéder aux autres ressources de votre abonnement. Azure Resource Manager vous permet d’utiliser le contrôle d’accès en fonction du rôle pour octroyer les actions autorisées à l’application.

Dans cet article, vous allez créer deux objets, l’application AD et le principal du service. L’application AD réside sur le client sur lequel l’application est inscrite, et définit le processus à exécuter. Le principal du service contient l’identité de l’application AD et est utilisé pour attribuer des autorisations. Dans l’application AD, vous pouvez créer plusieurs principaux du service. Pour obtenir une explication plus détaillée des applications et des principaux du service, consultez la rubrique [Objets principal du service et application](./active-directory/active-directory-application-objects.md). Pour plus d'informations sur l'authentification Active Directory, consultez la rubrique [Scénarios d'authentification pour Azure AD](./active-directory/active-directory-authentication-scenarios.md).

2 options sont à votre disposition pour authentifier votre application :

 - mot de passe : convient lorsqu’un utilisateur souhaite se connecter de manière interactive pendant l’exécution
 - certificat : convient aux scripts d’installation sans assistance qui doivent s’authentifier sans intervention de l’utilisateur

## Créer une application AD à l’aide d’un mot de passe

Dans cette section, vous allez effectuer la procédure permettant de créer l’application AD avec un mot de passe.

1. Connectez-vous à votre compte.

        Add-AzureRmAccount

1. Créez une application Active Directory en indiquant le nom d’affichage de votre application, l’URI vers une page décrivant votre application (le lien n’est pas vérifié), les URI identifiant votre application et le mot de passe correspondant à l’identité de votre application.

        $azureAdApplication = New-AzureRmADApplication -DisplayName "exampleapp" -HomePage "https://www.contoso.org" -IdentifierUris "https://www.contoso.org/example" -Password "<Your_Password>"

     Examinez le nouvel objet d’application.

        $azureAdApplication
        
     Notez en particulier la propriété **ApplicationId**, qui est nécessaire pour la création de principaux du service, les affectations de rôles et l’acquisition du jeton d’accès.

        DisplayName             : exampleapp
        Type                    : Application
        ApplicationId           : 8bc80782-a916-47c8-a47e-4d76ed755275
        ApplicationObjectId     : c95e67a3-403c-40ac-9377-115fa48f8f39
        AvailableToOtherTenants : False
        AppPermissions          : {}
        IdentifierUris          : {https://www.contoso.org/example}
        ReplyUrls               : {}


### Créer un principal du service et lui affecter un rôle

À partir de votre application AD, vous devez créer un principal du service et lui affecter un rôle.

1. Créez un principal du service pour votre application en transmettant l’ID d’application de l’application Active Directory.

        New-AzureRmADServicePrincipal -ApplicationId $azureAdApplication.ApplicationId

2. Accordez des autorisations au principal du service sur votre abonnement. Dans cet exemple, vous allez permettre au principal du service de lire toutes les ressources de l’abonnement. Pour le paramètre **ServicePrincipalName**, indiquez la propriété **ApplicationId** ou **IdentifierUris** que vous avez utilisée lors de la création de l’application. Pour plus d’informations sur le contrôle d’accès en fonction du rôle, consultez [Contrôle d’accès en fonction du rôle Azure](./active-directory/role-based-access-control-configure.md).

        New-AzureRmRoleAssignment -RoleDefinitionName Reader -ServicePrincipalName $azureAdApplication.ApplicationId.Guid

### Fournir manuellement des informations d’identification via PowerShell

Vous avez créé une application Active Directory et un principal du service pour votre application. Vous avez également assigné un rôle au principal du service. Maintenant, vous devez ouvrir une session en tant qu’application pour effectuer des opérations. Vous pouvez renseigner manuellement les informations d’identification de l’application lors de l’exécution de scripts ou de commandes à la demande.

1. Créez un objet **PSCredential** contenant vos informations d’identification à l’aide de la commande **Get-Credential**.

        $creds = Get-Credential

2. Vous serez invité à entrer vos informations d’identification. Pour le nom d’utilisateur, utilisez le paramètre **ApplicationId** ou **IdentifierUris** que vous avez utilisé lors de la création de l’application. Utilisez le mot de passe que vous avez indiqué lors de la création du compte.

     ![saisir les informations d’identification](./media/resource-group-authenticate-service-principal/arm-get-credential.png)

3. Récupérez l’abonnement dans lequel l’attribution de rôle a été créée. Cet abonnement sera utilisé pour récupérer le paramètre **TenantId** du client correspondant à l’affectation de rôle du principal du service.

        $subscription = Get-AzureRmSubscription

     Si votre compte est lié à plusieurs abonnements, indiquez un nom ou un ID d’abonnement pour obtenir l’abonnement que vous souhaitez utiliser.
     
        $subscription = Get-AzureRmSubscription -SubscriptionName "Azure MSDN - Visual Studio Ultimate"

4. Ouvrez une session en tant que principal du service en spécifiant que ce compte est un principal du service et en fournissant l’objet informations d’identification.

        Add-AzureRmAccount -Credential $creds -ServicePrincipal -TenantId $subscription.TenantId
        
     Vous êtes maintenant authentifié en tant que principal du service pour l’application Active Directory que vous avez créée.

5. Pour utiliser le jeton d’accès actuel dans une session ultérieure, vous pouvez enregistrer le profil.

        Save-AzureRmProfile -Path c:\Users\exampleuser\profile\exampleSP.json
        
     Vous pouvez ouvrir le profil et en examiner le contenu. Notez qu’il contient un jeton d’accès.
        
6. Au lieu d’ouvrir une nouvelle session la prochaine fois que vous souhaiterez exécuter du code en tant que principal du service, chargez simplement le profil.

        Select-AzureRmProfile -Path c:\Users\exampleuser\profile\exampleSP.json
        
> [AZURE.NOTE] Le jeton d’accès arrive à expiration. L’utilisation d’un profil enregistré ne fonctionne donc que durant la validité du jeton. Pour exécuter définitivement des scripts d’installation sans assistance, utilisez un certificat.
        
## Créer une application AD à l’aide d’un certificat

Dans cette section, vous allez effectuer la procédure permettant de créer une application AD avec un certificat.

1. Vous pouvez créer un certificat auto-signé.

        $cert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" -Subject "CN=exampleapp" -KeySpec KeyExchange
       
     Vous recevez des informations sur le certificat, notamment son empreinte.
     
        Directory: Microsoft.PowerShell.Security\Certificate::CurrentUser\My

        Thumbprint                                Subject
        ----------                                -------
        724213129BD2B950BB3F64FAB0C877E9348B16E9  CN=exampleapp

2. Récupérez la valeur de la clé à partir du certificat.

        $keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData())

3. Connectez-vous à votre compte Azure.

        Add-AzureRmAccount

4. Créez une application dans le répertoire.

        $azureAdApplication = New-AzureRmADApplication -DisplayName "exampleapp" -HomePage "https://www.contoso.org" -IdentifierUris "https://www.contoso.org/example" -KeyValue $keyValue -KeyType AsymmetricX509Cert -EndDate $cert.NotAfter -StartDate $cert.NotBefore      
        
    Examinez le nouvel objet d’application.

        $azureAdApplication

    Remarquez la propriété **ApplicationId** qui est nécessaire pour la création de principaux du service, les affectations de rôles et l’acquisition de jetons d’accès.

        DisplayName             : exampleapp
        Type                    : Application
        ApplicationId           : 1975a4fd-1528-4086-a992-787dbd23c46b
        ApplicationObjectId     : 9665e5f3-84b7-4344-92e2-8cc20ad16a8b
        AvailableToOtherTenants : False
        AppPermissions          : {}
        IdentifierUris          : {https://www.contoso.org/example}
        ReplyUrls               : {}    


### Créer un principal du service et lui affecter un rôle

1. Créez un principal du service pour votre application en transmettant l’ID d’application de l’application Active Directory.

        New-AzureRmADServicePrincipal -ApplicationId $azureAdApplication.ApplicationId

2. Accordez des autorisations au principal du service sur votre abonnement. Dans cet exemple, vous allez permettre au principal du service de lire toutes les ressources de l’abonnement. Pour le paramètre **ServicePrincipalName**, indiquez la propriété **ApplicationId** ou **IdentifierUris** que vous avez utilisée lors de la création de l’application. Pour plus d’informations sur le contrôle d’accès en fonction du rôle, consultez [Contrôle d’accès en fonction du rôle Azure](./active-directory/role-based-access-control-configure.md).

        New-AzureRmRoleAssignment -RoleDefinitionName Reader -ServicePrincipalName $azureAdApplication.ApplicationId.Guid

### Préparer les valeurs pour votre script

Dans votre script, vous allez transmettre trois valeurs nécessaires pour vous connecter en tant que principal du service. Vous aurez besoin de ce qui suit :

- ID d’application
- ID client 
- Empreinte de certificat

Vous avez vu l’ID d’application et l’empreinte de certificat lors des étapes précédentes. Toutefois, si vous devez récupérer ces valeurs ultérieurement, les commandes requises sont indiquées ci-dessous, ainsi que la commande permettant d’obtenir l’ID client.

1. Pour récupérer l’ID client, utilisez :

        (Get-AzureRmSubscription).TenantId 

    Si vous possédez plusieurs abonnements, vous pouvez également indiquer le nom de l’abonnement :

        (Get-AzureRmSubscription -SubscriptionName "Azure MSDN - Visual Studio Ultimate").TenantId
        
2. Pour récupérer l’ID d’application, utilisez :

        (Get-AzureRmADApplication -IdentifierUri "https://www.contoso.org/example").ApplicationId
        
3. Pour récupérer l’empreinte de certificat, utilisez :

        (Get-ChildItem -Path cert:\CurrentUser\My* -DnsName exampleapp).Thumbprint

### Fournir un certificat via un script PowerShell automatisé

Vous avez créé une application Active Directory et un principal du service pour votre application. Vous avez également assigné un rôle au principal du service. Vous devez maintenant ouvrir une session en tant que principal du service pour pouvoir effectuer des opérations de principal du service.

Pour vous authentifier dans votre script, spécifiez le compte principal du service et indiquez l’empreinte de certificat, l’ID d’application et l’ID client.

    Add-AzureRmAccount -ServicePrincipal -CertificateThumbprint {thumbprint} -ApplicationId {applicationId} -TenantId {tenantid}

Vous êtes maintenant authentifié en tant que principal du service pour l’application Active Directory que vous avez créée.

## Étapes suivantes
  
- Pour obtenir des exemples d’authentification .NET, consultez [Kit de développement logiciel (SDK) Azure Resource Manager pour .NET](resource-manager-net-sdk.md).
- Pour obtenir des exemples d’authentification Java, consultez [Kit de développement logiciel (SDK) Azure Resource Manager pour Java](resource-manager-java-sdk.md). 
- Pour obtenir des exemples d’authentification Python, consultez [Resource Management Authentication for Python](https://azure-sdk-for-python.readthedocs.io/en/latest/resourcemanagementauthentication.html) (Authentification Resource Management pour Python).
- Pour obtenir des exemples d’authentification REST, consultez [API REST Resource Manager](resource-manager-rest-api.md).
- Pour obtenir des instructions détaillées sur l’intégration d’une application à Azure pour la gestion des ressources, consultez [Guide du développeur pour l’authentification avec l’API Azure Resource Manager](resource-manager-api-authentication.md).

<!---HONumber=AcomDC_0601_2016-->