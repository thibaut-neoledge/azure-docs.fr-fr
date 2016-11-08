---
title: Créer un principal du service Azure avec PowerShell | Microsoft Docs
description: Explique comment utiliser Azure PowerShell pour créer une application et un principal du service Active Directory, et comment accorder à l’application l’accès aux ressources par le biais du contrôle d’accès en fonction du rôle. Cet article montre comment authentifier l’application avec un mot de passe ou un certificat.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn

ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 09/12/2016
ms.author: tomfitz

---
# Créer un principal du service pour accéder aux ressources à l’aide d’Azure PowerShell
> [!div class="op_single_selector"]
> * [PowerShell](resource-group-authenticate-service-principal.md)
> * [Interface de ligne de commande Azure](resource-group-authenticate-service-principal-cli.md)
> * [Portail](resource-group-create-service-principal-portal.md)
> 
> 

Lorsque vous exécutez une application ou un script qui a besoin d’accéder à des ressources, vous préférerez sûrement éviter d’utiliser vos propres informations d’identification. Vous possédez peut-être des autorisations différentes dont vous souhaitez bénéficier avec l’application et vous ne voulez pas que l’application continue à utiliser vos informations d’identification si vos responsabilités changent. Au lieu de cela, vous créez pour l’application une identité qui inclut des informations d’identification et des affectations de rôles. Chaque fois que l’application s’exécute, elle s’authentifie avec ces informations d’identification. Cette rubrique vous montre comment utiliser [Azure PowerShell](powershell-install-configure.md) pour configurer tout ce dont vous avez besoin pour qu’une application puisse s’exécuter sous ses propres informations d’identification et sous sa propre identité.

PowerShell vous propose deux options pour authentifier votre application AD :

* password
* certificat

Cette rubrique explique comment utiliser ces deux options dans PowerShell. Si vous souhaitez vous connecter à Azure à partir d’une infrastructure de programmation (par exemple Python, Ruby ou Node.js), l’authentification par mot de passe peut se révéler la meilleure option. Avant de déterminer s’il est préférable d’utiliser un mot de passe ou un certificat, consultez la section [Exemples d’applications](#sample-applications) pour obtenir des exemples d’authentification dans les différentes infrastructures.

## Concepts Active Directory
Dans cet article, vous créez deux objets : l’application Active Directory (AD) et le principal du service. L’application AD est la représentation globale de votre application. Elle contient les informations d’identification (c’est-à-dire un ID d’application et un mot de passe ou un certificat). Le principal du service est la représentation locale de votre application dans un annuaire Active Directory. Il contient l’affectation de rôle. Cette rubrique se concentre sur une application à client unique conçue pour s’exécuter au sein d’une seule organisation. Les applications à locataire unique sont généralement utilisées pour les applications métier exécutées au sein de votre organisation. Dans une application à locataire unique, vous avez une application AD et un principal du service.

Vous vous demandez peut-être pourquoi vous avez besoin des deux objets. Cette approche paraît plus logique lorsqu’on envisage des applications mutualisées. Les applications mutualisées sont généralement utilisées pour les applications SaaS (Software-as-a-Service), où votre application s’exécute dans un grand nombre d’abonnements différents. Pour les applications mutualisées, vous avez une application Active Directory et plusieurs principaux du service (un dans chaque annuaire Active Directory qui octroie un accès à l’application). Pour configurer une application mutualisée, consultez le [Guide du développeur pour l’authentification avec l’API Azure Resource Manager](resource-manager-api-authentication.md).

## Autorisations requises
Pour effectuer cette rubrique, vous devez disposer des autorisations suffisantes dans votre annuaire Azure Active Directory et votre abonnement Azure. Plus précisément, vous devez être en mesure de créer une application dans l’annuaire Active Directory et d’affecter un rôle au principal du service.

Dans votre annuaire Active Directory, votre compte doit être de type administrateur (par exemple **administrateur général** ou **administrateur utilisateur**). Si le rôle **Utilisateur** est affecté à votre compte, vous devez demander à un administrateur d’élever vos autorisations.

Dans votre abonnement, votre compte doit disposer d’un accès `Microsoft.Authorization/*/Write`, qui est accordé via le rôle [Propriétaire](active-directory/role-based-access-built-in-roles.md#owner) ou [Administrateur de l’accès utilisateur](active-directory/role-based-access-built-in-roles.md#user-access-administrator). Si le rôle **Collaborateur** est affecté à votre compte, vous recevez une erreur lorsque vous tentez d’affecter un rôle au principal du service. Là encore, l’administrateur de votre abonnement doit vous accorder un accès suffisant.

Passez maintenant à la section appropriée pour vous authentifier à l’aide d’un [mot de passe](#create-service-principal-with-password) ou d’un [certificat](#create-service-principal-with-certificate).

## Créer un principal du service avec un mot de passe
Cette section explique comment :

* créer l’application AD à l’aide d’un mot de passe
* créer le principal du service
* affecter le rôle Lecteur au principal du service

Pour effectuer rapidement ces étapes, consultez les applets de commande suivantes.

     $app = New-AzureRmADApplication -DisplayName "{app-name}" -HomePage "https://{your-domain}/{app-name}" -IdentifierUris "https://{your-domain}/{app-name}" -Password "{your-password}"
     New-AzureRmADServicePrincipal -ApplicationId $app.ApplicationId
     New-AzureRmRoleAssignment -RoleDefinitionName Reader -ServicePrincipalName $app.ApplicationId.Guid

Examinons ces étapes plus attentivement pour nous assurer que vous comprenez le processus.

1. Connectez-vous à votre compte.
   
        Add-AzureRmAccount
2. Créez une application Active Directory en indiquant un nom d’affichage, l’URI décrivant votre application, les URI identifiant votre application et le mot de passe correspondant à l’identité de votre application.
   
        $app = New-AzureRmADApplication -DisplayName "exampleapp" -HomePage "https://www.contoso.org/exampleapp" -IdentifierUris "https://www.contoso.org/exampleapp" -Password "<Your_Password>"
   
     Pour les applications à locataire unique, les URI ne sont pas validées.
   
     Si votre compte ne dispose pas des [autorisations requises](#required-permissions) dans l’annuaire Active Directory, un message d’erreur indiquant « Authentication\_Unauthorized » (Authentification non autorisée) ou « No subscription found in the context » (Aucun abonnement trouvé dans le contexte) s’affiche.
3. Examinez le nouvel objet d’application.
   
        $app
   
     Notez en particulier la propriété **ApplicationId**, qui est nécessaire pour la création de principaux du service, les affectations de rôles et l’acquisition du jeton d’accès.
   
        DisplayName             : exampleapp
        ObjectId                : c95e67a3-403c-40ac-9377-115fa48f8f39
        IdentifierUris          : {https://www.contoso.org/example}
        HomePage                : https://www.contoso.org
        Type                    : Application
        ApplicationId           : 8bc80782-a916-47c8-a47e-4d76ed755275
        AvailableToOtherTenants : False
        AppPermissions          : 
        ReplyUrls               : {}
4. Créez un principal du service pour votre application en transmettant l’ID d’application de l’application Active Directory.
   
        New-AzureRmADServicePrincipal -ApplicationId $app.ApplicationId
5. Accordez des autorisations sur votre abonnement au principal du service. Dans cet exemple, vous ajoutez le principal du service au rôle **Lecteur**, qui l’autorise à lire toutes les ressources de l’abonnement. Pour les autres rôles, voir [RBAC : rôles intégrés](active-directory/role-based-access-built-in-roles.md). Pour le paramètre **ServicePrincipalName**, indiquez la propriété **ApplicationId** que vous avez utilisée lors de la création de l’application.
   
        New-AzureRmRoleAssignment -RoleDefinitionName Reader -ServicePrincipalName $app.ApplicationId.Guid
   
    Si votre compte ne dispose pas des autorisations suffisantes pour affecter un rôle, un message d’erreur s’affiche. Le message indique que votre compte **n’est pas autorisé à effectuer l’action « Microsoft.Authorization/roleAssignments/write » sur l’étendue « /subscriptions/{guid} »**.

Et voilà ! Votre application Active Directory et votre principal du service sont maintenant configurés. La section suivante vous montre comment vous connecter avec les informations d’identification via PowerShell. Si vous souhaitez utiliser les informations d’identification dans votre application de code, vous pouvez passer à la rubrique [Exemples d’applications](#sample-applications).

### Fournir des informations d’identification via PowerShell
Maintenant, vous devez vous connecter en tant qu’application pour effectuer des opérations.

1. Créez un objet **PSCredential** contenant vos informations d’identification à l’aide de la commande **Get-Credential**. Vous avez besoin de l’élément **ApplicationId** avant d’exécuter cette commande ; vérifiez donc que vous pouvez coller ces valeurs.
   
        $creds = Get-Credential
2. Vous êtes invité à entrer vos informations d’identification. Pour le nom d’utilisateur, utilisez le paramètre **ApplicationId** que vous avez utilisé lors de la création de l’application. Utilisez le mot de passe que vous avez indiqué lors de la création du compte.
   
     ![saisir les informations d’identification](./media/resource-group-authenticate-service-principal/arm-get-credential.png)
3. Chaque fois que vous vous connectez en tant que principal de service, vous devez fournir l’ID de locataire du répertoire de votre application AD. Un locataire est une instance d’Active Directory. Si vous disposez d’un seul abonnement, vous pouvez utiliser :
   
        $tenant = (Get-AzureRmSubscription).TenantId
   
     Si vous disposez de plusieurs abonnements, spécifiez celui où réside votre application Active Directory. Pour plus d’informations, consultez la page [Association des abonnements Azure avec Azure Active Directory](active-directory/active-directory-how-subscriptions-associated-directory.md).
   
        $tenant = (Get-AzureRmSubscription -SubscriptionName "Contoso Default").TenantId
4. Ouvrez une session en tant que principal du service en spécifiant que ce compte est un principal du service et en fournissant l’objet informations d’identification.
   
        Add-AzureRmAccount -Credential $creds -ServicePrincipal -TenantId $tenant
   
     Vous êtes maintenant authentifié en tant que principal du service pour l’application Active Directory que vous avez créée.

### Enregistrer le jeton d’accès pour simplifier la connexion
Pour éviter de fournir des informations d’identification au principal de service chaque fois qu’il a besoin de se connecter, vous pouvez enregistrer le jeton d’accès.

1. Pour utiliser le jeton d’accès actuel dans une session ultérieure, enregistrez le profil.
   
        Save-AzureRmProfile -Path c:\Users\exampleuser\profile\exampleSP.json
   
     Ouvrez le profil et examinez-en le contenu. Notez qu’il contient un jeton d’accès.
2. Au lieu de vous reconnecter manuellement, il vous suffit de charger le profil.
   
        Select-AzureRmProfile -Path c:\Users\exampleuser\profile\exampleSP.json

> [!NOTE]
> Le jeton d’accès arrive à expiration. L’utilisation d’un profil enregistré ne fonctionne donc que durant la validité du jeton.
> 
> 

## Créer un principal du service avec un certificat
Cette section explique comment :

* créer un certificat auto-signé
* créer l’application AD à l’aide du certificat
* créer le principal du service
* affecter le rôle Lecteur au principal du service

Pour effectuer rapidement ces étapes avec Azure PowerShell 2.0 sur Windows 10 ou Windows Server 2016 Technical Preview, consultez les applets de commande suivantes.

    $cert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" -Subject "CN=exampleapp" -KeySpec KeyExchange
    $keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData())
    $app = New-AzureRmADApplication -DisplayName "exampleapp" -HomePage "https://www.contoso.org" -IdentifierUris "https://www.contoso.org/example" -CertValue $keyValue -EndDate $cert.NotAfter -StartDate $cert.NotBefore
    New-AzureRmADServicePrincipal -ApplicationId $app.ApplicationId
    New-AzureRmRoleAssignment -RoleDefinitionName Reader -ServicePrincipalName $app.ApplicationId.Guid

Examinons ces étapes plus attentivement pour nous assurer que vous comprenez le processus. Cet article montre également comment effectuer les tâches lorsque vous utilisez des versions d’Azure PowerShell ou des systèmes d’exploitation antérieurs.

### Créer le certificat auto-signé
La version de PowerShell disponible avec Windows 10 et Windows Server 2016 Technical Preview dispose d’une applet de commande **New-SelfSignedCertificate** mise à jour pour générer un certificat auto-signé. Les systèmes d’exploitation antérieurs disposent de l’applet de commande New-SelfSignedCertificate, mais elle n’offre pas les paramètres nécessaires pour cette rubrique. Au lieu de cela, vous devez importer un module pour générer le certificat. Cette rubrique illustre les deux approches pour la génération du certificat en fonction de votre système d’exploitation.

* Si vous disposez de **Windows 10 ou de Windows Server 2016 Technical Preview**, exécutez la commande suivante pour créer un certificat auto-signé :
  
        $cert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" -Subject "CN=exampleapp" -KeySpec KeyExchange
* Si vous **n’avez pas Windows 10 ou Windows Server 2016 Technical Preview**, vous devez télécharger le [générateur de certificat auto-signé](https://gallery.technet.microsoft.com/scriptcenter/Self-signed-certificate-5920a7c6/) depuis le centre de scripts Microsoft. Extrayez son contenu et importez l’applet de commande dont vous avez besoin.
  
        # Only run if you could not use New-SelfSignedCertificate
        Import-Module -Name c:\ExtractedModule\New-SelfSignedCertificateEx.ps1
  
     Ensuite, générez le certificat.
  
        $cert = New-SelfSignedCertificateEx -Subject "CN=exampleapp" -KeySpec "Exchange" -FriendlyName "exampleapp"

Vous disposez maintenant de votre certificat et pouvez continuer à créer votre application AD.

### Créer l’application Azure Active Directory et le principal du service
1. Récupérez la valeur de la clé à partir du certificat.
   
        $keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData())
2. Connectez-vous à votre compte Azure.
   
        Add-AzureRmAccount
3. Créez une application Active Directory en indiquant un nom d’affichage, l’URI décrivant votre application, les URI identifiant votre application et le mot de passe correspondant à l’identité de votre application.
   
     Si vous disposez d’Azure PowerShell 2.0 (août 2016 ou une version ultérieure), utilisez l’applet de commande suivante :
   
        $app = New-AzureRmADApplication -DisplayName "exampleapp" -HomePage "https://www.contoso.org" -IdentifierUris "https://www.contoso.org/example" -CertValue $keyValue -EndDate $cert.NotAfter -StartDate $cert.NotBefore      
   
    Si vous disposez d’Azure PowerShell 1.0, utilisez l’applet de commande suivante :
   
        $app = New-AzureRmADApplication -DisplayName "exampleapp" -HomePage "https://www.contoso.org" -IdentifierUris "https://www.contoso.org/example" -KeyValue $keyValue -KeyType AsymmetricX509Cert  -EndDate $cert.NotAfter -StartDate $cert.NotBefore      
   
    Pour les applications à locataire unique, les URI ne sont pas validées.
   
    Si votre compte ne dispose pas des [autorisations requises](#required-permissions) dans l’annuaire Active Directory, un message d’erreur indiquant « Authentication\_Unauthorized » (Authentification non autorisée) ou « No subscription found in the context » (Aucun abonnement trouvé dans le contexte) s’affiche.
   
    Examinez le nouvel objet d’application.
   
        $app
   
    Remarquez la propriété **ApplicationId**, nécessaire pour la création de principaux du service, les affectations de rôles et l’acquisition de jetons d’accès.
   
        DisplayName             : exampleapp
        ObjectId                : c95e67a3-403c-40ac-9377-115fa48f8f39
        IdentifierUris          : {https://www.contoso.org/example}
        HomePage                : https://www.contoso.org
        Type                    : Application
        ApplicationId           : 8bc80782-a916-47c8-a47e-4d76ed755275
        AvailableToOtherTenants : False
        AppPermissions          : 
        ReplyUrls               : {}
4. Créez un principal du service pour votre application en transmettant l’ID d’application de l’application Active Directory.
   
        New-AzureRmADServicePrincipal -ApplicationId $app.ApplicationId
5. Accordez des autorisations sur votre abonnement au principal du service. Dans cet exemple, vous ajoutez le principal du service au rôle **Lecteur**, qui l’autorise à lire toutes les ressources de l’abonnement. Pour les autres rôles, voir [RBAC : rôles intégrés](active-directory/role-based-access-built-in-roles.md). Pour le paramètre **ServicePrincipalName**, indiquez la propriété **ApplicationId** que vous avez utilisée lors de la création de l’application.
   
        New-AzureRmRoleAssignment -RoleDefinitionName Reader -ServicePrincipalName $app.ApplicationId.Guid
   
    Si votre compte ne dispose pas des autorisations suffisantes pour affecter un rôle, un message d’erreur s’affiche. Le message indique que votre compte **n’est pas autorisé à effectuer l’action « Microsoft.Authorization/roleAssignments/write » sur l’étendue « /subscriptions/{guid} »**.

Et voilà ! Votre application Active Directory et votre principal du service sont maintenant configurés. La section suivante vous montre comment ouvrir une session à l’aide d’un certificat via PowerShell.

### Fournir un certificat via un script PowerShell automatisé
Chaque fois que vous vous connectez en tant que principal de service, vous devez fournir l’ID de locataire du répertoire de votre application AD. Un locataire est une instance d’Active Directory. Si vous disposez d’un seul abonnement, vous pouvez utiliser :

    $tenant = (Get-AzureRmSubscription).TenantId

Si vous disposez de plusieurs abonnements, spécifiez celui où réside votre application Active Directory. Pour plus d’informations, consultez l’article [Administration de votre annuaire Azure AD](active-directory/active-directory-administer.md).

    $tenant = (Get-AzureRmSubscription -SubscriptionName "Contoso Default").TenantId

Pour vous authentifier dans votre script, spécifiez le compte principal du service et indiquez l’empreinte de certificat, l’ID d’application et l’ID client. Pour automatiser votre script, vous pouvez soit stocker ces valeurs en tant que variables d’environnement et les récupérer lors de l’exécution, soit les inclure dans votre script.

    Add-AzureRmAccount -ServicePrincipal -CertificateThumbprint $cert.Thumbprint -ApplicationId $app.ApplicationId -TenantId $tenant

Vous êtes maintenant authentifié en tant que principal du service pour l’application Active Directory que vous avez créée.

## Exemples d'applications
Les exemples d’applications suivants montrent comment ouvrir une session en tant que principal du service.

**.NET**

* [Deploy an SSH Enabled VM with a Template with .NET (Déployer une machine virtuelle compatible SSH à l’aide d’un modèle dans .NET)](https://azure.microsoft.com/documentation/samples/resource-manager-dotnet-template-deployment/)
* [Manage Azure resources and resource groups with .NET (Gérer des ressources et des groupes de ressources Azure avec .NET)](https://azure.microsoft.com/documentation/samples/resource-manager-dotnet-resources-and-groups/)

**Java**

* [Getting Started with Resources - Deploy Using Azure Resource Manager Template - in Java (Découverte des ressources - Déployer à l’aide du modèle Azure Resource Manager dans Java)](https://azure.microsoft.com/documentation/samples/resources-java-deploy-using-arm-template/)
* [Getting Started with Resources - Manage Resource Group - in Java (Découverte des ressources - Gérer un groupe de ressources dans Java)](https://azure.microsoft.com/documentation/samples/resources-java-manage-resource-group//)

**Python**

* [Deploy an SSH Enabled VM with a Template in Python (Déployer une machine virtuelle compatible SSH à l’aide d’un modèle dans Python)](https://azure.microsoft.com/documentation/samples/resource-manager-python-template-deployment/)
* [Managing Azure Resource and Resource Groups with Python (Gérer des ressources et des groupes de ressources Azure avec Python)](https://azure.microsoft.com/documentation/samples/resource-manager-python-resources-and-groups/)

**Node.JS**

* [Deploy an SSH Enabled VM with a Template in Node.js (Déployer une machine virtuelle compatible SSH à l’aide d’un modèle dans Node.js)](https://azure.microsoft.com/documentation/samples/resource-manager-node-template-deployment/)
* [Manage Azure resources and resource groups with Node.js (Gérer des ressources et des groupes de ressources Azure avec Node.js)](https://azure.microsoft.com/documentation/samples/resource-manager-node-resources-and-groups/)

**Ruby**

* [Deploy an SSH Enabled VM with a Template in Ruby (Déployer une machine virtuelle compatible SSH à l’aide d’un modèle dans Ruby)](https://azure.microsoft.com/documentation/samples/resource-manager-ruby-template-deployment/)
* [Managing Azure Resource and Resource Groups with Ruby (Gérer des ressources et des groupes de ressources Azure avec Ruby)](https://azure.microsoft.com/documentation/samples/resource-manager-ruby-resources-and-groups/)

## Étapes suivantes
* Pour obtenir des instructions détaillées sur l’intégration d’une application à Azure pour la gestion des ressources, consultez [Guide du développeur pour l’authentification avec l’API Azure Resource Manager](resource-manager-api-authentication.md).
* Pour obtenir une explication plus détaillée des applications et des principaux du service, consultez la rubrique [Objets principal du service et application](active-directory/active-directory-application-objects.md).
* Pour plus d'informations sur l'authentification Active Directory, consultez la rubrique [Scénarios d'authentification pour Azure AD](active-directory/active-directory-authentication-scenarios.md).

<!---HONumber=AcomDC_0914_2016-->