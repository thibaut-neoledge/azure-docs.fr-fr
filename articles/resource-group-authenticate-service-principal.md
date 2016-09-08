<properties
   pageTitle="Créer un principal du service Azure avec PowerShell | Microsoft Azure"
   description="Explique comment utiliser Azure PowerShell pour créer une application et un principal du service Active Directory, et comment accorder à l’application l’accès aux ressources par le biais du contrôle d’accès en fonction du rôle. Cet article montre comment authentifier l’application avec un mot de passe ou un certificat."
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
   ms.date="07/18/2016"
   ms.author="tomfitz"/>

# Créer un principal du service pour accéder aux ressources à l’aide d’Azure PowerShell

> [AZURE.SELECTOR]
- [PowerShell](resource-group-authenticate-service-principal.md)
- [Interface de ligne de commande Azure](resource-group-authenticate-service-principal-cli.md)
- [Portail](resource-group-create-service-principal-portal.md)

Lorsque vous exécutez une application ou un script qui a besoin d’accéder à des ressources, vous préférerez probablement éviter d’utiliser les informations d’identification d’un utilisateur. Cet utilisateur peut avoir différentes autorisations que vous souhaiteriez affecter au processus, et peut assumer des responsabilités susceptibles d’évoluer. Au lieu de cela, vous pouvez créer pour l’application une identité qui inclut des informations d’identification et des affectations de rôles. Votre application se connectera alors sous cette identité à chaque exécution. Cette rubrique vous montre comment utiliser [Azure PowerShell](powershell-install-configure.md) pour configurer tout ce dont vous avez besoin pour qu’une application puisse s’exécuter sous ses propres informations d’identification et sous sa propre identité.

Dans cet article, vous allez créer deux objets : l’application Active Directory (AD) et le principal du service. L’application AD contient les informations d’identification (c’est-à-dire un ID d’application et un mot de passe ou un certificat). Le principal du service contient l’affectation de rôle. Dans l’application AD, vous pouvez créer plusieurs principaux du service. Cette rubrique se concentre sur une application à client unique conçue pour s’exécuter au sein d’une seule organisation. Les applications à locataire unique sont généralement utilisées pour les applications métier exécutées au sein de votre organisation. Vous pouvez également créer des applications mutualisées lorsque votre application doit s’exécuter dans de plusieurs organisations. Ces applications sont habituellement utilisées pour les applications SaaS (Software-as-a-Service). Pour configurer une application mutualisée, consultez le [Guide du développeur pour l’authentification avec l’API Azure Resource Manager](resource-manager-api-authentication.md).

Cet article décrit de nombreux concepts qu’il est essentiel de bien comprendre pour utiliser Active Directory. Pour obtenir une explication plus détaillée des applications et des principaux du service, consultez la rubrique [Objets principal du service et application](./active-directory/active-directory-application-objects.md). Pour plus d'informations sur l'authentification Active Directory, consultez la rubrique [Scénarios d'authentification pour Azure AD](./active-directory/active-directory-authentication-scenarios.md).

PowerShell vous propose deux options pour authentifier votre application AD :

 - password
 - certificat

Si, après avoir configuré votre application AD, vous souhaitez vous connecter à Azure à partir d’une autre infrastructure de programmation (telles Python, Ruby ou Node.js), l’authentification par mot de passe peut se révéler la meilleure option. Avant de déterminer s’il est préférable d’utiliser un mot de passe ou un certificat, consultez la section [Exemples d’applications](#sample-applications) pour obtenir des exemples d’authentification dans les différentes infrastructures.

## Obtenir l’ID de locataire

Chaque fois que vous vous connectez en tant que principal de service, vous devez fournir l’ID de locataire du répertoire de votre application AD. Un locataire est une instance d’Active Directory. Étant donné que vous aurez besoin de cette valeur aussi bien pour l’authentification par mot de passe que pour l’authentification par certificat, tâchons d’obtenir dès maintenant cette valeur.

1. Connectez-vous à votre compte.

        Add-AzureRmAccount

2. Si vous disposez d’un seul abonnement, vous pouvez utiliser :

        $tenant = (Get-AzureRmSubscription).TenantId
    
     Si vous disposez de plusieurs abonnements, spécifiez celui que vous souhaitez utiliser pour l’application AD. Sélectionnez l’abonnement dans lequel réside votre instance Active Directory. Pour plus d’informations, consultez l’article [Administration de votre annuaire Azure AD](./active-directory/active-directory-administer.md).

        $tenant = (Get-AzureRmSubscription -SubscriptionName "Contoso Default").TenantId

Passez maintenant à la section ci-dessous pour vous authentifier à l’aide d’un [mot de passe](#create-service-principal-with-password) ou d’un [certificat](#create-service-principal-with-certificate).

## Créer un principal du service avec un mot de passe

Dans cette section, vous allez suivre la procédure qui vous permettra de créer l’application AD et le principal du service à l’aide d’un mot de passe.

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


     À partir de votre application AD, vous devez créer un principal du service et lui affecter un rôle.

2. Créez un principal du service pour votre application en transmettant l’ID d’application de l’application Active Directory.

        New-AzureRmADServicePrincipal -ApplicationId $azureAdApplication.ApplicationId

3. Accordez des autorisations sur votre abonnement au principal du service. Dans cet exemple, vous allez permettre au principal du service de lire toutes les ressources de l’abonnement. Pour le paramètre **ServicePrincipalName**, indiquez la propriété **ApplicationId** ou **IdentifierUris** que vous avez utilisée lors de la création de l’application. Pour plus d’informations sur le contrôle d’accès en fonction du rôle, consultez [Contrôle d’accès en fonction du rôle Azure](./active-directory/role-based-access-control-configure.md). Pour affecter un rôle, vous devez disposer d’un accès `Microsoft.Authorization/*/Write`, qui est accordé via le rôle [Propriétaire](./active-directory/role-based-access-built-in-roles.md#owner) ou [Administrateur de l’accès utilisateur](./active-directory/role-based-access-built-in-roles.md#user-access-administrator).

        New-AzureRmRoleAssignment -RoleDefinitionName Reader -ServicePrincipalName $azureAdApplication.ApplicationId.Guid

Et voilà ! Votre application Active Directory et votre principal du service sont maintenant configurés. La section suivante vous montre comment vous connecter avec les informations d’identification via PowerShell. Toutefois, si vous souhaitez utiliser les informations d’identification dans votre application de code, vous pouvez ignorer cette rubrique. Vous pouvez passer directement aux [exemples d’applications](#sample-applications) pour obtenir des exemples de connexion à l’aide d’un ID d’application et d’un mot de passe.

### Fournir des informations d’identification via PowerShell

Maintenant, vous devez ouvrir une session en tant qu’application pour effectuer des opérations.

1. Créez un objet **PSCredential** contenant vos informations d’identification à l’aide de la commande **Get-Credential**. Vous avez besoin de l’élément **ApplicationId** ou **IdentifierUris** avant d’exécuter cette commande ; vérifiez donc que vous pouvez coller ces valeurs.

        $creds = Get-Credential

2. Vous serez invité à entrer vos informations d’identification. Pour le nom d’utilisateur, utilisez le paramètre **ApplicationId** ou **IdentifierUris** que vous avez utilisé lors de la création de l’application. Utilisez le mot de passe que vous avez indiqué lors de la création du compte.

     ![saisir les informations d’identification](./media/resource-group-authenticate-service-principal/arm-get-credential.png)

4. Ouvrez une session en tant que principal du service en spécifiant que ce compte est un principal du service et en fournissant l’objet informations d’identification. Vous avez également besoin de l’ID de locataire que vous avez récupéré à l’étape [Obtenir l’ID de locataire](#get-tenant-id).

        Add-AzureRmAccount -Credential $creds -ServicePrincipal -TenantId $tenant
        
     Vous êtes maintenant authentifié en tant que principal du service pour l’application Active Directory que vous avez créée.

5. Pour utiliser le jeton d’accès actuel dans une session ultérieure, vous pouvez enregistrer le profil.

        Save-AzureRmProfile -Path c:\Users\exampleuser\profile\exampleSP.json
        
     Vous pouvez ouvrir le profil et en examiner le contenu. Notez qu’il contient un jeton d’accès.
        
6. Au lieu d’ouvrir manuellement une nouvelle session la prochaine fois que vous souhaiterez exécuter du code en tant que principal du service, chargez simplement le profil.

        Select-AzureRmProfile -Path c:\Users\exampleuser\profile\exampleSP.json
        
> [AZURE.NOTE] Le jeton d’accès arrive à expiration. L’utilisation d’un profil enregistré ne fonctionne donc que durant la validité du jeton.
        
## Créer un principal du service avec un certificat

Dans cette section, vous allez suivre la procédure qui vous permettra de créer une application AD et un principal du service à l’aide d’un certificat.

1. Vous pouvez créer un certificat auto-signé. Si vous disposez de **Windows 10 ou de Windows Server 2016 Technical Preview**, exécutez la commande suivante :

        $cert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" -Subject "CN=exampleapp" -KeySpec KeyExchange
       
     Votre variable contient des informations sur le certificat, notamment son empreinte.
     
        Directory: Microsoft.PowerShell.Security\Certificate::CurrentUser\My

        Thumbprint                                Subject
        ----------                                -------
        724213129BD2B950BB3F64FAB0C877E9348B16E9  CN=exampleapp

     Si vous n’utilisez **pas** Windows 10 ou Windows Server 2016 Technical Preview, vous ne pourrez pas exécuter l’applet de commande **New-SelfSignedCertificate**. Vous devrez donc télécharger le script PowerShell de [générateur de certificat auto-signé](https://gallery.technet.microsoft.com/scriptcenter/Self-signed-certificate-5920a7c6) avant d’exécuter les commandes suivantes pour générer un certificat. Cette étape n’est pas nécessaire si vous avez déjà créé le certificat dans l’exemple précédent.
     
        # Only run if you could not use New-SelfSignedCertificate
        Import-Module -Name c:\New-SelfSignedCertificateEx.ps1
        New-SelfSignedCertificateEx -Subject "CN=exampleapp" -KeySpec "Exchange" -FriendlyName "exampleapp"
        $cert = Get-ChildItem -Path cert:\CurrentUser\My* -DnsName exampleapp

2. Récupérez la valeur de la clé à partir du certificat.

        $keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData())

4. Créez une application dans le répertoire.

        $azureAdApplication = New-AzureRmADApplication -DisplayName "exampleapp" -HomePage "https://www.contoso.org" -IdentifierUris "https://www.contoso.org/example" -CertValue $keyValue -EndDate $cert.NotAfter -StartDate $cert.NotBefore      
        
    Examinez le nouvel objet d’application.

        $azureAdApplication

    Remarquez la propriété **ApplicationId** nécessaire pour la création de principaux du service, les affectations de rôles et l’acquisition de jetons d’accès.

        DisplayName             : exampleapp
        Type                    : Application
        ApplicationId           : 1975a4fd-1528-4086-a992-787dbd23c46b
        ApplicationObjectId     : 9665e5f3-84b7-4344-92e2-8cc20ad16a8b
        AvailableToOtherTenants : False
        AppPermissions          : {}
        IdentifierUris          : {https://www.contoso.org/example}
        ReplyUrls               : {}    


5. Créez un principal du service pour votre application en transmettant l’ID d’application de l’application Active Directory.

        New-AzureRmADServicePrincipal -ApplicationId $azureAdApplication.ApplicationId

6. Accordez des autorisations sur votre abonnement au principal du service. Dans cet exemple, vous allez permettre au principal du service de lire toutes les ressources de l’abonnement. Pour le paramètre **ServicePrincipalName**, indiquez la propriété **ApplicationId** ou **IdentifierUris** que vous avez utilisée lors de la création de l’application. Pour plus d’informations sur le contrôle d’accès en fonction du rôle, consultez [Contrôle d’accès en fonction du rôle Azure](./active-directory/role-based-access-control-configure.md). Pour affecter un rôle, vous devez disposer d’un accès `Microsoft.Authorization/*/Write`, qui est accordé via le rôle [Propriétaire](./active-directory/role-based-access-built-in-roles.md#owner) ou [Administrateur de l’accès utilisateur](./active-directory/role-based-access-built-in-roles.md#user-access-administrator).

        New-AzureRmRoleAssignment -RoleDefinitionName Reader -ServicePrincipalName $azureAdApplication.ApplicationId.Guid

Et voilà ! Votre application Active Directory et votre principal du service sont maintenant configurés. La section suivante vous montre comment ouvrir une session à l’aide d’un certificat via PowerShell.

### Fournir un certificat via un script PowerShell automatisé

Pour vous authentifier dans votre script, spécifiez le compte principal du service et indiquez l’empreinte de certificat, l’ID d’application et l’ID client. Ces valeurs sont déjà contenues dans les variables **$azureAdApplication.ApplicationId**, **$cert.Thumbprint** et **$tenant**. Pour automatiser votre script, vous pouvez soit stocker ces valeurs en tant que variables d’environnement et les récupérer lors de l’exécution, soit les inclure dans votre script.

    Add-AzureRmAccount -ServicePrincipal -CertificateThumbprint 000000 -ApplicationId 000000 -TenantId 0000000

Vous êtes maintenant authentifié en tant que principal du service pour l’application Active Directory que vous avez créée.

Si vous avez besoin de récupérer l’ID d’application ultérieurement, utilisez la commande suivante :

    (Get-AzureRmADApplication -IdentifierUri "https://www.contoso.org/example").ApplicationId
        
Si vous avez besoin de récupérer l’empreinte du certificat ultérieurement, utilisez la commande suivante :

    (Get-ChildItem -Path cert:\CurrentUser\My* -DnsName exampleapp).Thumbprint

Si vous avez besoin de récupérer l’ID de locataire ultérieurement, consultez la section [Obtenir l’ID de locataire](#get-tenant-id).

## Exemples d’applications

Les exemples d’applications suivants montrent comment ouvrir une session en tant que principal du service.

**.NET**

- [Deploy an SSH Enabled VM with a Template with .NET](https://azure.microsoft.com/documentation/samples/resource-manager-dotnet-template-deployment/) (Déployer une machine virtuelle compatible SSH à l’aide d’un modèle dans .NET)
- [Manage Azure resources and resource groups with .NET](https://azure.microsoft.com/documentation/samples/resource-manager-dotnet-resources-and-groups/) (Gérer des ressources et des groupes de ressources Azure avec .NET)

**Java**

- [Getting Started with Resources - Deploy Using Azure Resource Manager Template - in Java](https://azure.microsoft.com/documentation/samples/resources-java-deploy-using-arm-template/) (Découverte des ressources - Déployer à l’aide du modèle Azure Resource Manager dans Java)
- [Getting Started with Resources - Manage Resource Group - in Java](https://azure.microsoft.com/documentation/samples/resources-java-manage-resource-group//) (Découverte des ressources - Gérer un groupe de ressources dans Java)

**Python**

- [Deploy an SSH Enabled VM with a Template in Python](https://azure.microsoft.com/documentation/samples/resource-manager-python-template-deployment/) (Déployer une machine virtuelle compatible SSH à l’aide d’un modèle dans Python)
- [Managing Azure Resource and Resource Groups with Python](https://azure.microsoft.com/documentation/samples/resource-manager-python-resources-and-groups/) (Gérer des ressources et des groupes de ressources Azure avec Python)

**Node.JS**

- [Deploy an SSH Enabled VM with a Template in Node.js](https://azure.microsoft.com/documentation/samples/resource-manager-node-template-deployment/) (Déployer une machine virtuelle compatible SSH à l’aide d’un modèle dans Node.js)
- [Manage Azure resources and resource groups with Node.js](https://azure.microsoft.com/documentation/samples/resource-manager-node-resources-and-groups/) (Gérer des ressources et des groupes de ressources Azure avec Node.js)

**Ruby**

- [Deploy an SSH Enabled VM with a Template in Ruby](https://azure.microsoft.com/documentation/samples/resource-manager-ruby-template-deployment/) (Déployer une machine virtuelle compatible SSH à l’aide d’un modèle dans Ruby)
- [Managing Azure Resource and Resource Groups with Ruby](https://azure.microsoft.com/documentation/samples/resource-manager-ruby-resources-and-groups/) (Gérer des ressources et des groupes de ressources Azure avec Ruby)

## Étapes suivantes
  
- Pour obtenir des instructions détaillées sur l’intégration d’une application à Azure pour la gestion des ressources, consultez le [Guide du développeur pour l’authentification avec l’API Azure Resource Manager](resource-manager-api-authentication.md).

<!---HONumber=AcomDC_0824_2016-->