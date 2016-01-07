<properties
   pageTitle="Gestion de l’accès aux ressources"
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
   ms.date="11/28/2015"
   ms.author="tomfitz"/>

# Gestion de l’accès aux ressources

Avec Azure Resource Manager, vous pouvez vous assurer que les utilisateurs de votre organisation disposent des autorisations appropriées pour gérer les ressources ou y accéder. Azure Resource Manager tire parti du contrôle d’accès en fonction du rôle (RBAC) de telle sorte que vous puissiez facilement appliquer les stratégies de sécurité aux ressources individuelles ou aux groupes de ressources. Par exemple, vous pouvez accorder l’accès utilisateur à une machine virtuelle spécifique dans un abonnement ou offrir à un utilisateur la possibilité de gérer tous les sites web dans un abonnement, mais aucune autre ressource.

Cette rubrique se concentre sur les commandes qui vous permettent d'attribuer des rôles et des autorisations. Pour obtenir une présentation du contrôle d'accès basé sur les rôles, consultez [Contrôle d'accès basé sur les rôles dans le portail Microsoft Azure](active-directory/role-based-access-control-configure.md).

## Concepts

Il importe de maîtriser quelques concepts clés à propos du contrôle d’accès en fonction du rôle (RBAC) :

1. Principal : autorisation attribuée à l’entité, comme un utilisateur Azure Active Directory, un groupe de sécurité ou une application.
2. Rôle : ensemble d’actions autorisées et exclues.
3. Action : action effectuée sur une ressource (par ex. lire, créer). 
4. Étendue : niveau auquel un rôle s’applique, tel que l’abonnement, le groupe de ressources ou la ressource.
5. Attribution de rôle : processus d’association d’un principal à un rôle et définition de l’étendue.

Une liste des **actions** prises en charge est accessible à l’aide de l’outil d’interface de ligne de commande Azure interplateforme (xPlat) ou du module Azure PowerShell.

Pour l’outil d’interface de ligne de commande interplateforme Azure, utilisez la commande suivante pour répertorier **toutes** les actions, pour l’ensemble des fournisseurs de ressources.

    azure provider operations show --operationSearchString '*';

Si vous utilisez le module PowerShell Azure Resource Manager (AzureRm), utilisez la commande suivante pour répertorier **toutes** les actions, pour **tous** les fournisseurs de ressources.

    Get-AzureRmProviderOperation -OperationSearchString *;

## Exemples de rôle
Pour comprendre les concepts RBAC, examinons quelques exemples courants de définitions de rôles :

| Rôle | Actions autorisées |
| ------- | ----------------- |
| Lecteur | **/read (tout lire) |
| Propriétaire | * (tout lire/écrire) |

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

[AZURE.INCLUDE [powershell-preview-inline-include](../includes/powershell-preview-inline-include.md)]


### Affichez les rôles disponibles
Pour afficher tous les rôles disponibles pour votre abonnement, exécutez l’applet de commande **Get-AzureRmRoleDefinition**.

    PS C:\> Get-AzureRmRoleDefinition
    
    Name             : API Management Service Contributor
    Id               : /subscriptions/{subscription-id}/providers/Microsoft.Authorization/roleDefinitions/{guid}
    IsCustom         : False
    Description      : Lets you manage API Management services, but not access to them.
    Actions          : {Microsoft.ApiManagement/Services/*, Microsoft.Authorization/*/read,
                       Microsoft.Resources/subscriptions/resourceGroups/read,
                       Microsoft.Resources/subscriptions/resourceGroups/resources/read...}
    NotActions       : {}
    AssignableScopes : {/}

    Name             : Application Insights Component Contributor
    Id               : /subscriptions/{subscription-id}/providers/Microsoft.Authorization/roleDefinitions/{guid}
    IsCustom         : False
    Description      : Lets you manage Application Insights components, but not access to them.
    Actions          : {Microsoft.Insights/components/*, Microsoft.Insights/webtests/*, Microsoft.Authorization/*/read,
                       Microsoft.Resources/subscriptions/resourceGroups/read...}
    NotActions       : {}
    AssignableScopes : {/}
    ...

### Accordez l’autorisation de lecteur à un groupe pour l’abonnement.
1. Vérifiez la définition du rôle **Lecteur** en fournissant le nom du rôle lors de l’exécution de la commande **Get-AzureRmRoleDefinition**. Vérifiez que les actions autorisées sont celles que vous aviez l’intention d’attribuer.

        PS C:\> Get-AzureRmRoleDefinition Reader
   
        Name             : Reader
        Id               : /subscriptions/{subscription-id}/providers/Microsoft.Authorization/roleDefinitions/{guid}
        IsCustom         : False
        Description      : Lets you view everything, but not make any changes.
        Actions          : {*/read}
        NotActions       : {}
        AssignableScopes : {/}

2. Obtenez le groupe de sécurité requis en exécutant l’applet de commande **Get-AzureRmADGroup**. Fournissez le nom réel du groupe dans votre abonnement. ExampleAuditorGroup est présenté ci-dessous.

        PS C:\> $group = Get-AzureRmAdGroup -SearchString ExampleAuditorGroup

3. Créez l’attribution de rôle pour le groupe de sécurité auditeur. Lorsque la commande est terminée, la nouvelle attribution de rôle est retournée.

        PS C:\> New-AzureRmRoleAssignment -ObjectId $group.Id -Scope /subscriptions/{subscriptionId}/ -RoleDefinitionName Reader


###Accordez l’autorisation de collaborateur à une application pour un groupe de ressources.
1. Vérifiez la définition du rôle **Collaborateur** en fournissant le nom du rôle lors de l’exécution de la commande **Get-AzureRmRoleDefinition**. Vérifiez que les actions autorisées sont celles que vous aviez l’intention d’attribuer.

        PS C:\> Get-AzureRmRoleDefinition Contributor

2. Obtenez l’ID de l’objet principal du service en exécutant la commande **Get-AzureRmADServicePrincipal** et en fournissant le nom de l’application dans votre abonnement. ExampleApplication est présentée ci-dessous.

        PS C:\> $service = Get-AzureRmADServicePrincipal -SearchString ExampleApplicationName

3. Créez les attributions de rôle pour le principal du service en exécutant la commande **New-AzureRmRoleAssignment**.

        PS C:\> New-AzureRmRoleAssignment -ObjectId $service.Id -ResourceGroupName ExampleGroupName -RoleDefinitionName Contributor

Pour obtenir une explication plus détaillée de la configuration d’une application Azure Active Directory et d’un principal du service, consultez [Authentification d’un principal du service avec Azure Resource Manager](resource-group-authenticate-service-principal.md).

###Accorder les autorisations de propriétaire à l’utilisateur d’une ressource.
1. Vérifiez la définition du rôle **Propriétaire** en fournissant le nom du rôle lors de l’exécution de la commande **Get-AzureRmRoleDefinition**. Vérifiez que les actions autorisées sont celles que vous aviez l’intention d’attribuer.

        PS C:\> Get-AzureRmRoleDefinition Owner

2. Créez les attributions de rôle pour l’utilisateur.

        PS C:\> New-AzureRmRoleAssignment -UserPrincipalName "someone@example.com" -ResourceGroupName {groupName} -ResourceType "Microsoft.Web/sites" -ResourceName "mysite" -RoleDefinitionName Owner


###Listez les journaux d’audit du groupe de ressources.
Pour obtenir le journal d’audit d’un groupe de ressources, exécutez la commande **Get-AzureRmLog** (ou **Get-AzureResourceGroupLog** pour les versions d’Azure PowerShell antérieures à la version préliminaire 1.0).

      PS C:\> Get-AzureRmLog -ResourceGroup ExampleGroupName

## Comment utiliser Azure CLI pour Mac, Linux et Windows

Si Azure CLI pour Mac, Linux et Windows n’est pas installé ou que vous n’avez pas configuré votre compte professionnel pour une utilisation avec Azure CLI, consultez [Installer et configurer Azure CLI](xplat-cli-install.md).

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

- Pour en savoir plus sur le contrôle d’accès en fonction du rôle, consultez [Contrôle d’accès en fonction du rôle dans le portail Microsoft Azure](role-based-access-control-configure.md).
- Pour en savoir plus sur l’utilisation des principaux de service pour la gestion de l’accès aux applications comprises dans votre abonnement, consultez [Authentification d’un principal du service via Azure Resource Manager](resource-group-authenticate-service-principal.md) et [Création d’un principal du service Azure à l’aide du portail Azure Classic](../resource-group-create-service-principal-portal.md).
- Pour en savoir plus sur les opérations d’audit dans votre organisation, consultez [Opérations d’audit avec Resource Manager](resource-group-audit.md).
- Vous pouvez appliquer des restrictions et des conventions sur votre abonnement avec des stratégies personnalisées. Pour plus d’informations, consultez [Utiliser le service Policy pour gérer les ressources et contrôler l’accès](resource-manager-policy.md).

 

<!----HONumber=AcomDC_1203_2015-->