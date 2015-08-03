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
   ms.date="07/15/2015"
   ms.author="tomfitz"/>

# Authentification d’un principal du service à l’aide d’Azure Resource Manager

Cette rubrique explique comment autoriser un principal du service (tel qu’un processus automatisé, une application ou un service) à accéder aux autres ressources de votre abonnement. Azure Resource Manager vous permet d’utiliser le contrôle d’accès en fonction du rôle pour authentifier un principal du service et lui attribuer des actions autorisées. Cette rubrique explique comment utiliser PowerShell et Azure CLI pour authentifier le principal du service et lui attribuer un rôle.


## Concepts
1. Azure Active Directory (AAD) : service de gestion des identités et des accès pour le cloud. Pour plus d’informations, consultez la page [Qu’est-ce qu’Azure Active Directory ?](active-directory/active-directory-whatis.md)
2. Principal du service : instance d’application dans un répertoire ayant besoin d’accéder à d’autres ressources.
3. Application AD : enregistrement de répertoire qui identifie une application à AAD. Pour plus d’informations, consultez la page [Principes fondamentaux de l’authentification dans Azure AD](https://msdn.microsoft.com/library/azure/874839d9-6de6-43aa-9a5c-613b0c93247e#BKMK_Auth).

## Autorisez l’accès à un principal du service et authentifiez-le à l’aide de PowerShell

Si Azure PowerShell n’est pas installé sur votre système, consultez la page [Installation et configuration d’Azure PowerShell](./powershell-install-configure.md).

Commencez par créer un principal du service. Pour ce faire, nous allons utiliser la fonction Créer une application dans le répertoire. Cette section vous guidera à travers les étapes de création d’une application dans le répertoire.

1. Créez une application AAD à l’aide de la commande **New-AzureADApplication**. Indiquez le nom d’affichage de votre application, l’URI vers une page décrivant votre application (le lien n’est pas vérifié), les URI identifiant votre application et le mot de passe correspondant à l’identité de votre application.

        PS C:\> $azureAdApplication = New-AzureADApplication -DisplayName "<Your Application Display Name>" -HomePage "<https://YourApplicationHomePage>" -IdentifierUris "<https://YouApplicationUri>" -Password "<Your_Password>"

     L’application Azure AD est renvoyée. La propriété **ApplicationId** est nécessaire pour la création de principaux du service, l’attribution de rôles et l’acquisition de jetons JWT. Enregistrez la sortie ou capturez-la dans une variable.

        Type                    : Application
        ApplicationId           : a41acfda-d588-47c9-8166-d659a335a865
        ApplicationObjectId     : a26aaa48-bd52-4a7f-b29f-1bebf74c91e3
        AvailableToOtherTenants : False
        AppPermissions          : {{
                            "claimValue": "user_impersonation",
                            "description": "Allow the application to access My
                              Application on behalf of the signed-in user.",
                            "directAccessGrantTypes": [],
                            "displayName": "Access <<Your Application Display Name>>",
                            "impersonationAccessGrantTypes": [
                              {
                                "impersonated": "User",
                                "impersonator": "Application"
                              }
                            ],
                            "isDisabled": false,
                            "origin": "Application",
                            "permissionId":
                            "b866ef28-9abb-4698-8c8f-eb4328533831",
                            "resourceScopeType": "Personal",
                            "userConsentDescription": "Allow the application
                             to access <<Your Application Display Name>> on your behalf.",
                            "userConsentDisplayName": "Access <<Your Application Display Name>>",
                            "lang": null
                          }}


2. Créez un principal du service pour votre application.

        PS C:\> New-AzureADServicePrincipal -ApplicationId $azureAdApplication.ApplicationId

     Vous avez désormais créé un principal du service dans le répertoire, mais aucune autorisation ni étendue n’a encore été attribuée au service. Vous devez autoriser explicitement le principal du service à effectuer des opérations dans une étendue donnée.

3. Accordez des autorisations au principal du service sur votre abonnement. Dans cet exemple, vous allez permettre au principal du service de lire toutes les ressources de l’abonnement. Pour le paramètre **ServicePrincipalName**, indiquez la propriété **ApplicationId** ou **IdentifierUris** que vous avez utilisée lors de la création de l’application. Pour plus d’informations sur le contrôle d’accès en fonction du rôle, consultez la page [Gestion et audit d’accès aux ressources](azure-portal/resource-group-rbac.md)

        PS C:\> New-AzureRoleAssignment -RoleDefinitionName Reader -ServicePrincipalName $azureAdApplication.ApplicationId

4. Récupérez l’abonnement dans lequel l’attribution de rôle a été créée. Cet abonnement sera utilisé ultérieurement pour récupérer le paramètre **TenantId** du client correspondant à l’attribution de rôle du principal du service.

        PS C:\> $subscription = Get-AzureSubscription | where { $_.IsCurrent }

     Si vous avez créé l’attribution de rôle dans un abonnement autre que l’abonnement sélectionné, vous pouvez spécifier le paramètre **SubscriptoinId** ou **SubscriptionName** afin de récupérer un autre abonnement.

5. Créez un objet **PSCredential** contenant vos informations d’identification à l’aide de la commande **Get-Credential**.

        PS C:\> $creds = Get-Credential

     Vous serez invité à entrer vos informations d’identification.

     ![][1]

     Pour le nom d’utilisateur, utilisez le paramètre **ApplicationId** ou **IdentifierUris** que vous avez utilisé lors de la création de l’application. Utilisez le mot de passe que vous avez indiqué lors de la création du compte.

6. Utilisez les informations d’identification que vous avez saisies comme entrée pour l’applet de commande **Add-AzureAccount** afin de connecter le principal du service :

        PS C:\> Add-AzureAccount -Credential $creds -ServicePrincipal -Tenant $subscription.TenantId

     Vous devriez maintenant être authentifié en tant que principal du service pour l’application AAD que vous avez créée.


## Autorisez l’accès et authentifiez un principal du service à l’aide d’Azure CLI

Si Azure CLI pour Mac, Linux et Windows n’est pas installé sur votre système, consultez la page [Installation et configuration d’Azure CLI](xplat-cli-install.md)

1. Créez une application AAD en exécutant la commande **azure ad app create**. Indiquez le nom d’affichage de votre application, l’URI vers une page décrivant votre application (le lien n’est pas vérifié), les URI identifiant votre application et le mot de passe correspondant à l’identité de votre application.

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

2. Créez un principal du service pour votre application. Indiquez l’ID d’application renvoyé à l’étape précédente.

        azure ad sp create b57dd71d-036c-4840-865e-23b71d8098ec
        
    Le nouveau principal du service est renvoyé. L’ID d’objet est nécessaire lorsque vous accordez des autorisations.
    
        info:    Executing command ad sp create
        + Creating service principal for application b57dd71d-036c-4840-865e-23b71d8098ec
        data:    Object Id:               47193a0a-63e4-46bd-9bee-6a9f6f9c03cb
        data:    Display Name:            exampleapp
        ...
        info:    ad sp create command OK

    Vous avez désormais créé un principal du service dans le répertoire, mais aucune autorisation ni étendue n’a encore été attribuée au service. Vous devez autoriser explicitement le principal du service à effectuer des opérations dans une étendue donnée.

3. Accordez des autorisations au principal du service sur votre abonnement. Dans cet exemple, vous allez permettre au principal du service de lire toutes les ressources de l’abonnement. Pour le paramètre **ServicePrincipalName**, indiquez la propriété **ApplicationId** ou **IdentifierUris** que vous avez utilisée lors de la création de l’application. Pour plus d’informations sur le contrôle d’accès en fonction du rôle, consultez la page [Gestion et audit d’accès aux ressources](azure-portal/resource-group-rbac.md)

        azure role assignment create --objectId 47193a0a-63e4-46bd-9bee-6a9f6f9c03cb -o Reader -c /subscriptions/{subscriptionId}/

4. Déterminez le paramètre **TenantId** du client correspondant à l’attribution de rôle du principal du service en affichant les comptes sous forme de liste et en recherchant la propriété **TenantId** dans la sortie.

        azure account list

5. Connectez-vous en vous identifiant en tant que principal du service. Pour le nom d’utilisateur, utilisez le paramètre **ApplicationId** que vous avez utilisé lors de la création de l’application. Utilisez le mot de passe que vous avez indiqué lors de la création du compte.

        azure login -u "<ApplicationId>" -p "<password>" --service-principal --tenant "<TenantId>"

    Vous devriez maintenant être authentifié en tant que principal du service pour l’application AAD que vous avez créée.

## Étapes suivantes
Mise en route

- [Présentation d’Azure Resource Manager](./resource-group-overview.md)  
- [Utilisation d’Azure PowerShell avec Azure Resource Manager](./powershell-azure-resource-manager.md)
- [Utilisation de l’interface de ligne de commande Azure pour Mac, Linux et Windows avec le Gestionnaire des ressources Azure](virtual-machines/xplat-cli-azure-resource-manager.md)  
- [Utilisation du portail Azure pour gérer vos ressources Azure](azure-portal/resource-group-portal.md)

  
Création et déploiement d’applications
  
- [Création de modèles Azure Resource Manager](./resource-group-authoring-templates.md)  
- [Déploiement d’une application avec un modèle Azure Resource Manager](azure-portal/resource-group-template-deploy.md)  
- [Résolution des problèmes liés aux déploiements de groupes de ressources dans Azure](virtual-machines/resource-group-deploy-debug.md)  
- [Fonctions des modèles Azure Resource Manager](./resource-group-template-functions.md)  
- [Opérations de modèle avancées](./resource-group-advanced-template.md)  
- [Déploiement de ressources Azure à l’aide de bibliothèques .NET et d’un modèle](virtual-machines/arm-template-deployment.md)
  
Organisation des ressources
  
- [Organisation des ressources Azure à l’aide de balises](./resource-group-using-tags.md)  
  
Gestion et audit de l’accès
  
- [Gestion et audit d’accès aux ressources](azure-portal/resource-group-rbac.md)  
- [Création d’un principal du service Azure à l’aide du portail Azure](./resource-group-create-service-principal-portal.md)  
  


<!-- Images. -->
[1]: ./media/resource-group-authenticate-service-principal/arm-get-credential.png

<!---HONumber=July15_HO4-->