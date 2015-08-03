<properties
   pageTitle="Gestion et audit d’accès aux ressources"
   description="Utilisez le contrôle d’accès en fonction du rôle (RBAC) pour gérer les autorisations utilisateur pour les ressources déployées dans Azure."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="wpickett"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="AzurePortal"
   ms.workload="na"
   ms.date="07/15/2015"
   ms.author="tomfitz"/>

# Gestion et audit d’accès aux ressources

Avec Azure Resource Manager, vous pouvez vous assurer que les utilisateurs de votre organisation disposent des autorisations appropriées pour gérer les ressources ou y accéder. Azure Resource Manager tire parti du contrôle d’accès en fonction du rôle (RBAC) de telle sorte que vous puissiez facilement appliquer les stratégies de sécurité aux ressources individuelles ou aux groupes de ressources. Par exemple, vous pouvez accorder l’accès utilisateur à une machine virtuelle spécifique dans un abonnement ou offrir à un utilisateur la possibilité de gérer tous les sites web dans un abonnement, mais aucune autre ressource.

## Concepts

Il importe de maîtriser quelques concepts clés à propos du contrôle d’accès en fonction du rôle (RBAC) :

1. Principal : autorisation attribuée à l’entité, comme un utilisateur, un groupe de sécurité ou une application.
2. Rôle : ensemble des actions autorisées
3. Étendue : niveau auquel un rôle s’applique, tel que l’abonnement, le groupe de ressources ou la ressource.
3. Attribution de rôle : processus d’ajout d’un principal à un rôle et définition de l’étendue.

## Exemples de rôle
Pour comprendre les concepts RBAC, examinons quelques exemples courants de définitions de rôles :

| Rôle | Actions autorisées |
| ------- | ----------------- |
| Lecteur | */read (tout lire) | | Propriétaire | * (tout lire/écrire) |

Pour affecter le rôle **Lecteur** à l’**utilisateur A** du groupe de ressources nommé **ExampleGroup** et le rôle **Propriétaire** à l’**utilisateur B** de la totalité de l’abonnement, vous devez attribuer les éléments suivants :

| Rôle | Principal | Étendue |
| --------|-------------|---------- |
| Lecteur | Utilisateur A | /subscriptions/{subscriptionId}/resourceGroups/examplegroup |
| Propriétaire | Utilisateur B | /subscriptions/{subscriptionId} |

## Exemples de scénarios

Dans cette rubrique, vous allez apprendre à effectuer les scénarios courants suivants via Azure PowerShell, Azure CLI pour Mac, Linux et Windows et l’API REST.

1. Affichez les rôles disponibles dans un abonnement et les actions autorisées pour ces rôles.
2. Accordez les autorisations de lecteur aux membres d’un groupe à travers l’abonnement.
3. Accordez les autorisations de collaborateur à une application pour permettre à l’application gérer les ressources à l’intérieur du groupe de ressources.
4. Accordez les autorisations de propriétaire sur un site web particulier à un seul utilisateur.
5. Listez les journaux d’audit du groupe de ressources.


## Comment utiliser PowerShell pour gérer l’accès
Si la dernière version d’Azure PowerShell n’est pas déjà installée sur votre système, consultez la page [Installer et configurer Azure PowerShell](../powershell-install-configure.md). Ouvrez la console Azure PowerShell.

1. Connectez-vous à votre compte Azure avec vos informations d’identification. La commande retourne les informations relatives à votre compte.

        PS C:\> Add-AzureAccount
          
        Id                             Type       ...
        --                             ----    
        someone@example.com            User       ...   

2. Si vous avez plusieurs abonnements, fournissez l’ID d’abonnement que vous souhaitez utiliser pour le déploiement.

        PS C:\> Select-AzureSubscription -SubscriptionID <YourSubscriptionId>

3. Basculez sur le module Azure Resource Manager.

        PS C:\> Switch-AzureMode AzureResourceManager

### Affichez les rôles disponibles
Pour afficher tous les rôles disponibles pour votre abonnement, exécutez l’applet de commande **Get-AzureRoleDefinition**.

    PS C:\> Get-AzureRoleDefinition

    Name                          Id                            Actions                  NotActions
    ----                          --                            -------                  ----------
    API Management Service Con... /subscriptions/####... {Microsoft.ApiManagement/S...   {}
    Application Insights Compo... /subscriptions/####... {Microsoft.Insights/compon...   {}
    ...

### Accordez l’autorisation de lecteur à un groupe pour l’abonnement.
1. Vérifiez la définition du rôle **Lecteur** en fournissant le nom du rôle lors de l’exécution de la commande **Get-AzureRoleDefinition**. Vérifiez que les actions autorisées sont celles que vous aviez l’intention d’attribuer.

        PS C:\> Get-AzureRoleDefinition Reader
   
        Name            Id                            Actions           NotActions
        ----            --                            -------           ----------
        Reader          /subscriptions/####...        {*/read}          {}

2. Obtenez le groupe de sécurité requis en exécutant l’applet de commande **Get-AzureADGroup**. Fournissez le nom réel du groupe dans votre abonnement. ExampleAuditorGroup est présenté ci-dessous.

        PS C:\> $group = Get-AzureAdGroup -SearchString ExampleAuditorGroup

3. Créez l’attribution de rôle pour le groupe de sécurité auditeur. Lorsque la commande est terminée, la nouvelle attribution de rôle est retournée.

        PS C:\> New-AzureRoleAssignment -ObjectId $group.Id -Scope /subscriptions/{subscriptionId}/ -RoleDefinitionName Reader

        Mail               :
        RoleAssignmentId   : /subscriptions/####/providers/Microsoft.Authorization/roleAssignments/####
        DisplayName        : Auditors
        RoleDefinitionName : Reader
        Actions            : {*/read}
        NotActions         : {}
        Scope              : /subscriptions/####
        ObjectId           : ####

###Accordez l’autorisation de collaborateur à une application pour un groupe de ressources.
1. Vérifiez la définition du rôle **Collaborateur** en fournissant le nom du rôle lors de l’exécution de la commande **Get-AzureRoleDefinition**. Vérifiez que les actions autorisées sont celles que vous aviez l’intention d’attribuer.

        PS C:\> Get-AzureRoleDefinition Contributor

2. Obtenez l’ID de l’objet principal du service en exécutant la commande **Get-AzureADServicePrincipal** et en fournissant le nom de l’application dans votre abonnement. ExampleApplication est présentée ci-dessous.

        PS C:\> $service = Get-AzureADServicePrincipal -SearchString ExampleApplicationName

3. Créez les attributions de rôle pour le principal du service en exécutant la commande **New-AzureRoleAssignment**.

        PS C:\> New-AzureRoleAssignment -ObjectId $service.Id -ResourceGroupName ExampleGroupName -RoleDefinitionName Contributor

Pour obtenir une explication plus détaillée de la configuration d’une application Azure Active Directory et d’un principal du service, consultez [Authentification d’un principal du service avec Azure Resource Manager](../resource-group-authenticate-service-principal.md).

###Accorder les autorisations de propriétaire à l’utilisateur d’une ressource.
1. Vérifiez la définition du rôle **Propriétaire** en fournissant le nom du rôle lors de l’exécution de la commande **Get-AzureRoleDefinition**. Vérifiez que les actions autorisées sont celles que vous aviez l’intention d’attribuer.

        PS C:\> Get-AzureRoleDefinition Owner

2. Créez les attributions de rôle pour l’utilisateur.

        PS C:\> New-AzureRoleAssignment -UserPrincipalName "someone@example.com" -ResourceGroupName {groupName} -ResourceType "Microsoft.Web/sites" -ResourceName "mysite" -RoleDefinitionName Owner


###Listez les journaux d’audit du groupe de ressources.
Pour obtenir le journal d’audit pour un groupe de ressources, exécutez la commande **Get-AzureResourceGroupLog**.

      PS C:\> Get-AzureResourceGroupLog -ResourceGroup ExampleGroupName

## Comment utiliser Azure CLI pour Mac, Linux et Windows

Si Azure CLI pour Mac, Linux et Windows n’est pas installé ou que vous n’avez pas configuré votre compte professionnel pour une utilisation avec Azure CLI, consultez [Installer et configurer Azure CLI](../xplat-cli-install.md).

1. Connectez-vous à votre compte Azure avec vos informations d’identification. La commande renvoie le résultat de votre connexion.

        azure login

        ...
        info:    login command OK

2. Si vous avez plusieurs abonnements, fournissez l’ID d’abonnement que vous souhaitez utiliser pour le déploiement.

        azure account set <YourSubscriptionNameOrId>

3. Basculez vers le module Azure Resource Manager. Vous recevrez la confirmation du nouveau mode.

        azure config mode arm
        
        info:     New mode is arm

### Affichez les rôles disponibles
Affichez tous les rôles disponibles pour votre abonnement. Retourne une liste de définitions de rôle.

    azure role list

### Accordez l’autorisation de lecteur à un groupe pour l’abonnement.
1. Obtenez la définition de rôle pour le rôle **Lecteur**. Vérifiez que les actions autorisées sont celles que vous aviez l’intention d’attribuer.

        azure role show Reader
        
        info:    Executing command role show
        + Getting role definitions
        data:    Name    Actions  NotActions
        data:    ------  -------  ----------
        data:    Reader  */read
        info:    role show command OK

2. Obtenir le groupe de sécurité requis et son ID objet en recherchant le groupe en fonction du nom. ExampleAuditorGroup est illustré dans l’exemple suivant.

        azure ad group show --search ExampleAuditorGroup
        
        info:    Executing command ad group show
        + Getting group list
        data:    Display Name:      ExampleAuditorGroup
        data:    ObjectId:          1c272299-9729-462a-8d52-7efe5ece0c5c
        data:    Security Enabled:  true
        data:    Mail Enabled:
        data:
        info:    ad group show command OK

3. Créez l’attribution de rôle pour le groupe de sécurité.

        azure role assignment create --objectId {group-object-id} -o Reader -c /subscriptions/{subscriptionId}/
        
        info:    Executing command role assignment create
        + Getting role definition id
        + Creating role assignment
        info:    role assignment create command OK


### Accordez l’autorisation de collaborateur à une application pour un groupe de ressources.
1. Obtenez la définition de rôle pour le rôle **Collaborateur**. Vérifiez que les actions autorisées sont celles que vous aviez l’intention d’attribuer.

        azure role show Contributor

2. Obtenez l’ObjectId de l’application. Indiquez le nom de l’application à récupérer.

        azure ad sp show --search ExampleApplicationName

2. Créez l’attribution de rôle pour l’application.

        azure role assignment create --ObjectId {service-principal-object-id} -o Contributor -c /subscriptions/{subscriptionId}/


###Accorder les autorisations de propriétaire à l’utilisateur pour un site web particulier.
1. Obtenez la définition de rôle pour le rôle **Propriétaire**. Vérifiez que les actions autorisées sont celles que vous aviez l’intention d’attribuer.

        azure role show Owner

2. Créer l’attribution de rôle pour l’utilisateur.

        azure role assignment create --mail "someone@example.com" -o Owner -c /subscriptions/{subscriptionId}/resourceGroups/{groupName}/providers/Microsoft.Web/sites/{mySiteName}


###Listez les journaux d’audit du groupe de ressources.
Pour obtenir le journal d’audit d’un groupe de ressources, exécutez la commande **azure group log show** et indiquez le nom du groupe de ressources de votre choix.

         azure group log show ExampleGroupName


## Comment utiliser l’API REST
Pour gérer le contrôle d’accès en fonction du rôle via l’API REST Azure Resource Manager, vous devez définir les en-têtes et les paramètres courants (y compris les jetons d’authentification) lors de l’envoi des demandes. Pour plus d’informations, consultez [En-têtes et paramètres communs](https://msdn.microsoft.com/library/azure/dn906885.aspx).
   
Pour connaître les versions d’api prises en charge, exécutez :

      GET https://management.azure.com/providers/Microsoft.Authorization?api-version=2015-01-01

Vous pouvez utiliser la version `2014-10-01-preview` de cette rubrique.

###Créer une affectation de rôle
Obtenez les définitions de rôle disponibles.

    GET https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Authorization/roleDefinitions?api-version=2014-10-01-preview

Créez l’affectation du rôle.

    PUT https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Authorization/roleAssignments/{role-assignment-id}?api-version=2014-10-01-preview
    {
      "properties": {
          "roleDefinitionId": "/subscriptions/{subscription-id}/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c",
          "principalId": "{principal-object-id}",
          "scope": "/subscriptions/{subscription-id}"
       }
    }


###Supprimer une affectation de rôle

      Delete  https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Authorization/roleAssignments/{role-assignment-id}?api-version=2014-10-01-preview


## Étapes suivantes

- [Contrôle d’accès en fonction du rôle dans le portail Microsoft Azure](../role-based-access-control-configure.md)
- [Créer un principal du service Azure à l’aide du portail Azure classique](../resource-group-create-service-principal-portal.md)
- [Authentification d’un principal du service à l’aide d’Azure Resource Manager](../resource-group-authenticate-service-principal.md)

 

<!---HONumber=July15_HO4-->