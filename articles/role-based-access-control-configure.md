<properties
	pageTitle="Contrôle d’accès basé sur les rôles dans le portail Azure de Microsoft"
	description="Décrit le fonctionnement du contrôle d'accès en fonction du rôle et comment configurer ce dernier"
	services=""
	documentationCenter=""
	authors="Justinha"
	manager="terrylan"
	editor=""/>

<tags
	ms.service="multiple"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.tgt_pltfrm="Ibiza"
	ms.workload="infrastructure-services"
	ms.date="06/29/2015"
	ms.author="justinha"/>

# Contrôle d’accès basé sur les rôles dans le portail Azure de Microsoft

Nous avons ajouté une prise en charge pour le contrôle d’accès basé sur les rôles (RBAC) dans le portail Azure de Microsoft pour aider les entreprises à répondre aux exigences de gestion des accès de façon simple et précise. Le [billet de blog](http://go.microsoft.com/fwlink/?LinkId=511576) présente brièvement la fonctionnalité et vous permet de la prendre en main. Cette rubrique décrit en détail les différents concepts et aborde divers cas d'utilisation supplémentaires.


## RBAC dans Azure

Chaque abonnement Azure est associé à un annuaire Azure Active Directory. Les utilisateurs et services qui accèdent aux ressources de l'abonnement en utilisant les API du portail de gestion Azure de Microsoft ou d’Azure Resource Manager doivent d’abord s'authentifier avec cet Azure Active Directory.

![][1]

Le contrôle d'accès en fonction du rôle Azure vous permet d'accorder l'accès approprié aux utilisateurs, groupes et services Azure AD en leur affectant des rôles sur un abonnement, un groupe de ressources ou un niveau de ressources individuel. Le rôle affecté définit le niveau d'accès des utilisateurs, groupes ou services aux ressources Azure.

### Rôle

Un rôle est un ensemble d'actions qui peuvent être appliquées à des ressources Azure. Un utilisateur ou un service est autorisé à effectuer une action sur une ressource Azure s'il s'est vu affecter un rôle qui inclut cette action. Pour obtenir la liste des rôles intégrés et **leurs** propriétés actions et **not actions**, consultez [Rôles intégrés](#builtinroles).

### Affectation de rôle

L'accès est accordé aux utilisateurs et services Azure AD en leur affectant le rôle approprié sur une ressource Azure.

#### Entités de sécurité Azure AD

Les rôles peuvent être affectés aux types suivants d'entités de sécurité Azure AD :

+ **Utilisateurs** : les rôles peuvent être affectés aux utilisateurs faisant partis de l’Azure AD auquel l’abonnement Azure est associé. Les rôles peuvent également être affectés à des utilisateurs de compte Microsoft externes (par exemple joe@outlook.com) en utilisant l'action d'invitation afin de leur affecter un rôle dans le portail Azure. Affecter un rôle à un utilisateur de compte Microsoft externe génère un compte invité dans le Azure AD associé. Si le compte invité est désactivé dans l'annuaire, l'utilisateur externe ne pourra accéder à aucune ressource Azure pour laquelle il a reçu un droit d'accès.
+ **Groupes** : les rôles peuvent être affectés à des groupes de sécurité Azure AD. Un utilisateur se voit automatiquement accorder le droit d'accès à une ressource s'il devient membre d'un groupe qui dispose de cet accès. L'utilisateur perd également automatiquement son droit d'accès à la ressource lorsqu'il est supprimé du groupe. Plutôt que d'affecter des rôles directement aux utilisateurs, la méthode la plus efficace consiste à gérer l'accès via des groupes, en leur affectant des rôles et en leur ajoutant des utilisateurs. Azure RBAC n'autorise pas l'affectation de rôles à des listes de distribution. La capacité à affecter des rôles à des groupes permet à une organisation d'étendre au cloud son modèle de contrôle d'accès existant à partir de son répertoire local sur le cloud. de cette façon, les groupes de sécurité déjà établis pour contrôler l'accès local peuvent être réutilisés pour contrôler l'accès aux ressources dans le portail Azure. Pour plus d'informations concernant les différentes options de synchronisation des utilisateurs et groupes à partir d'un annuaire local, voir la section [Intégration d'annuaire](http://technet.microsoft.com/library/jj573653.aspx). Azure AD Premium inclut également une [fonctionnalité de gestion de groupes déléguée](http://msdn.microsoft.com/library/azure/dn641267.aspx), qui permet de déléguer la création et la gestion des groupes à des utilisateurs non-administrateurs à partir de Azure AD.
+ **Principaux du service** : les identités de service sont représentées sous forme de principaux du service dans le répertoire. Ils authentifient Azure AD et communiquent de façon sécurisée les uns avec les autres. Un service peut se voir accorder l'accès aux ressources Azure en affectant des rôles via le module Azure pour Windows PowerShell au principal du service Azure AD qui représente ce service.

#### Étendue des ressources

Il n'est pas nécessaire d'accorder l'accès à l'ensemble de l'abonnement. Les rôles peuvent également être affectés à des groupes de ressources et à des ressources individuelles. Dans Azure RBAC, une ressource hérite des affectations de rôles de ses ressources parentes. Par conséquent, lorsqu'un utilisateur, un groupe ou un service se voit accorder l'accès uniquement à un groupe de ressources dans un abonnement, il ne peut accéder qu'à ce seul groupe de ressources et aux ressources incluses, mais à aucun autre groupe de ressources inclus dans l'abonnement. Autre exemple : un groupe de sécurité peut être ajouté au rôle Lecteur d'un groupe de ressources, mais sera ajouté au rôle Collaborateur pour une base de données dans ce groupe de ressources.

![][2]

## Coexistence du RBAC et des coadministrateurs d'abonnement

L'administrateur et les coadministrateurs d'abonnement continueront à disposer d'un accès complet aux portails et aux API de gestion Azure. Dans le modèle RBAC, ils se voient affecter le rôle de propriétaire au niveau de l'abonnement. Toutefois, le nouveau modèle RBAC est pris en charge uniquement par le portail Azure et les API Azure Resource Manager. Les utilisateurs et services qui se voient affecter des rôles RBAC ne peuvent pas accéder au portail de gestion Azure et aux API de gestion des services. L’ajout d’un utilisateur au rôle Propriétaire d'un abonnement dans le portail Azure ne fait pas de cet utilisateur un coadministrateur de l'abonnement dans la version complète du portail Azure.

Si vous souhaitez accorder à un utilisateur l'accès à une ressource Azure qui ne peut pas encore être gérée via le portail Azure, vous devez l'ajouter aux coadministrateurs de l'abonnement en utilisant le portail de gestion Azure. Service Bus et Cloud Services sont des exemples de ressources qui ne peuvent actuellement pas être gérées à l'aide du RBAC.

## Autorisation pour les opérations de gestion et les opérations de données

Le contrôle d'accès basé sur les rôles est pris en charge uniquement pour les opérations de gestion des ressources Azure dans le portail Azure et les API Azure Resource Manager. Toutes les opérations de niveau de données pour les ressources Azure ne peuvent pas être autorisées via le RBAC. Par exemple, les opérations create/read/update/delete des comptes de stockage peuvent être contrôlées via le RBAC, mais le contrôle des opérations create/read/update/delete des objets blob ou des tables dans le compte de stockage n'est pas encore possible. De même, les opérations create/read/update/delete d'une base de données SQL peuvent être contrôlées via le RBAC, mais le contrôle des opérations create/read/update/delete des tables SQL dans la base de données n'est pas encore possible.

## Ajout et suppression de l'accès

Examinons un exemple de gestion de l'accès par un propriétaire des ressources au sein d'une organisation. Dans ce scénario, plusieurs personnes travaillent sur divers projets de tests et de production qui sont développés à l'aide des ressources Azure. La méthode la plus efficace pour accorder l'accès doit être adoptée. Les utilisateurs doivent pouvoir accéder à toutes les ressources dont ils ont besoin, mais à ces ressources uniquement. Vous souhaitez réutiliser tous les processus et outils déjà investis dans l'utilisation des groupes de sécurité, qui sont contrôlés depuis un Active Directory local. Les sections suivantes expliquent comment configurer l'accès à ces ressources :

* [Ajout un accès](#add-access)
* [Suppression un accès](#remove-access)
* [Ajout ou suppression d'accès pour un utilisateur externe](#add-or-remove-access-for-external-user)

### Ajout d'accès

Voici un récapitulatif des spécifications d'accès dans Azure et leur configuration.

Utilisateur/Groupe | Spécification d'accès | rôle et étendue de l'accès
------------- | -------------  | ------------
Toute l'équipe de Jill Santos | Lecture de toutes les ressources Azure | Ajout du groupe AD qui représente l'équipe de Jill Santos au rôle Lecteur de l'abonnement Azure
Toute l'équipe de Jill Santos | Création et gestion de toutes les ressources dans le groupe de ressources Test | Ajout du groupe AD qui représente l'équipe de Jill Santos au rôle Collaborateur du groupe de ressources Test
Brock | Création et gestion de toutes les ressources dans le groupe de ressources Prod | Ajout de Brock au rôle Collaborateur du groupe de ressources Prod


Commençons par ajouter l'accès en Lecture pour toutes les ressources de l'abonnement. Cliquez sur **Parcourir > Tout > Abonnements**.

![][3]

Cliquez sur le *nom de l'abonnement* \*\* > Lecteur > Ajouter\*\*. Dans la liste des utilisateurs et des groupes, sélectionnez ou entrez le nom du groupe Active Directory.

![][4]

Ajoutez ensuite la même équipe au rôle Collaborateur du groupe de ressources Test. Cliquez sur le groupe de ressources pour ouvrir son volet de propriétés. Sous **Rôles**, cliquez sur **Collaborateur > Ajouter** et entrez le nom de l'équipe.

![][5]

Pour ajouter Brock au rôle Collaborateur du groupe de ressources Prod, cliquez sur le groupe de ressources, puis sur **Collaborateur > Ajouter**, et entrez le nom de Brock.

![][6]

Les affectations de rôles peuvent également être gérées en utilisant le module Microsoft Azure pour Windows PowerShell. Voici un exemple qui montre comment ajouter le compte de Brock en utilisant la cmdlet New-AzureRoleAssignment au lieu du portail :

	PS C:\> New-AzureRoleAssignment -Mail brockh@contoso.com -RoleDefinitionName Contributor -ResourceGroupName ProdDB

Pour plus d'informations sur l'utilisation de Windows PowerShell pour ajouter ou supprimer un accès, voir la section [Gestion du contrôle d'accès basé sur un rôle à l'aide de Windows PowerShell](role-based-access-control-powershell.md).

### Suppression d'accès

Il est également facile de supprimer des affectations. Imaginons que vous souhaitez supprimer l'utilisateur Brad Adams du rôle Lecteur d'un groupe de ressources nommé TestDB. Ouvrez le volet du groupe de ressources, cliquez sur **Lecteur > Brad Adams > Supprimer**.

![][7]

Voici un exemple qui montre comment supprimer Brad Adams en utilisant la cmdlet Remove-AzureRoleAssignment :

	PS C:\> Remove-AzureRoleAssignment -Mail badams@contoso.com -RoleDefinitionName Reader -ResourceGroupName TestDB

### Ajout ou suppression d'accès pour un utilisateur externe

L'onglet **Configurer** d'un annuaire inclut des options de contrôle d'accès des utilisateurs externes. Ces options peuvent être modifiées uniquement dans l'interface utilisateur (il n'existe aucune méthode Windows PowerShell ou API) dans le portail Azure complet par un administrateur global de l'annuaire. Pour ouvrir l’onglet **Configurer** dans le portail Azure, cliquez sur **Active Directory**, puis sur le nom de l’annuaire.

![][10]

Vous pouvez ensuite modifier les options de contrôle d'accès des utilisateurs externes.

![][8]

Par défaut, les invités ne peuvent pas énumérer les éléments de contenu de l'annuaire ; ils ne peuvent donc pas afficher les utilisateurs ou groupes dans la **Liste des membres**. Ils peuvent rechercher un utilisateur en entrant son adresse e-mail complète, puis accorder l'accès. Voici l'ensemble des restrictions par défaut pour les invités :

- Ils ne peuvent pas énumérer les utilisateurs ou groupes dans l'annuaire.
- Ils ne peuvent accéder qu'à certaines informations limitées d'un utilisateur s'ils ne connaissent pas son adresse e-mail.
- Ils peuvent accéder à certaines informations limitées d'un groupe s'ils connaissent son nom.

La capacité des invités à n'accéder qu'à certaines informations limitées d'un utilisateur ou d'un groupe les autorise à inviter d'autres personnes et afficher certains détails des personnes avec qui ils collaborent.

Voyons comment le processus d'ajout d'accès fonctionne pour un utilisateur externe. Nous allons ajouter un utilisateur externe au même rôle Lecteur que pour le groupe de ressources TestDB afin de lui permettre de déboguer une erreur. Ouvrez le volet du groupe de ressources, cliquez sur **Lecteur > Ajouter > Inviter** et entrez l'adresse e-mail de l'utilisateur à ajouter.

![][9]

Lorsque vous ajoutez un utilisateur externe, un invité est créé dans l'annuaire. Cet invité peut ensuite être ajouté à un groupe ou supprimé d'un groupe, ou vous pouvez l'ajouter à un rôle ou le supprimer d'un rôle individuellement, comme vous le feriez pour tout autre utilisateur de l'annuaire.

Vous pouvez également supprimer un invité de n'importe quel rôle, comme vous le feriez pour n'importe quel utilisateur. Supprimer un invité d'un rôle sur une ressource ne le supprime pas de l'annuaire.

## Effectuer le suivi des modifications apportées aux affectations de rôles

Comme c’est le cas pour les autres événements, les modifications apportées aux affectations de rôles sont enregistrées dans les [journaux d’audit](http://azure.microsoft.com/updates/audit-logs-in-azure-preview-portal/). Le journal des modifications de Le journal des modifications apportées aux affectations de rôles peut être récupéré à l’aide d’[Azure PowerShell](https://msdn.microsoft.com/library/azure/jj156055.aspx) ou de l’[API REST Azure Resource Manager](https://msdn.microsoft.com/library/azure/dn931927.aspx).

Par exemple, pour récupérer la liste des modifications apportées aux affectations de rôles au sein d’un abonnement, exécutez les deux applets de commande suivantes avec Azure PowerShell. La première bascule sur le mode Azure Resource Manager.

`Switch-AzureMode -name AzureResourceManager`

`Get-AzureSubscriptionIdLog –DetailedOutput -StartTime '06-15-15' -EndTime '06-29-15'`

Les modifications apportées aux affectations de rôles sont collectées dans les événements pour lesquels l’élément ResourceProviderName est `Microsoft.Authorization`. Les détails de l’affectation sont consignés dans une section spécifique : l’élément principal affecté, l’identification du rôle et de l’étendue. Les modifications apportées aux affectations de rôles sont visibles lorsque vous accédez aux journaux d’audit dans le portail, mais le portail n’indique pas les détails sur l’événement. Pour afficher les détails sur l’événement, vous devez utiliser Azure PowerShell.

###Détail sur l’événement

Voici un exemple de détails sur l’événement de modification d’affectation de rôle :

```
Authorization        :
                       Scope     : /subscriptions/ff945b8d-441a-41ef-a9db-7bd5fcc99978/resourceGroups/Default-Storage-W
                       estUS/providers/Microsoft.ClassicStorage/storageAccounts/authzwaes/providers/Microsoft.Authoriza
                       tion/roleAssignments/531f036a-37ff-40c1-9bb9-aa580ebe7e78
                       Action    : Microsoft.Authorization/roleAssignments/write
                       Role      : Subscription Admin
                       Condition :
Caller               : William.Hennum@contoso.com
Claims               :
                       aud            : https://management.core.windows.net/
                       iss            : https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db47/
                       iat            : 1435333533
                       nbf            : 1435333533
                       exp            : 1435337433
                       ver            : 1.0
                       http://schemas.microsoft.com/identity/claims/tenantid: 72f988bf-86f1-41af-91ab-2d7cd011db47
                       http://schemas.microsoft.com/identity/claims/objectidentifier:
                       dda50086-5e3d-4a4b-b8bc-f54771104d89
                       http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn: William.Hennum@contoso.com
                       puid           : 10030000803CDC0B
                       http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier:
                       MJwntjqWaULfl30NJMiDRVSVCWMX5GzmMNU4oqitDXs
                       http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname: William
                       http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname: Hennum
                       name           : William Hennum
                       http://schemas.microsoft.com/claims/authnmethodsreferences: rsa,wia,mfa
                       _claim_names   : {"groups":"src1"}
                       _claim_sources : {"src1":{"endpoint":"https://graph.windows.net/72f988bf-86f1-41af-91ab-2d7cd011
                       db47/users/dda50086-5e3d-4a4b-b8bc-f54771104d89/getMemberObjects"}}
                       http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name: William.Hennum@contoso.com
                       onprem_sid     : S-1-5-21-1721254763-462695806-1538882281-3175325
                       appid          : c44b4083-3bb0-49c1-b47d-974e53cbdf3c
                       appidacr       : 2
                       http://schemas.microsoft.com/identity/claims/scope: user_impersonation
                       http://schemas.microsoft.com/claims/authnclassreference: 1
CorrelationId        : d724ffd0-31a4-4564-941b-f3a5d32ad8a4
Description          :
EventChannels        : Operation
EventDataId          : ed8e79b6-c7d1-4332-adcf-70d37546c5a6
EventName            : BeginRequest
EventSource          : Administrative
EventTimestamp       : 6/26/2015 3:53:34 PM
HttpRequest          :
                       ClientId        : F7272386-295A-4545-96BD-21F0856A43FE
                       Method          : PUT
                       Url             :
                       ClientIpAddress : 23.99.81.159
Id                   : /subscriptions/ff945b8d-441a-41ef-a9db-7bd5fcc99978/resourceGroups/Default-Storage-WestUS/provid
                       ers/Microsoft.ClassicStorage/storageAccounts/authzwaes/providers/Microsoft.Authorization/roleAss
                       ignments/531f036a-37ff-40c1-9bb9-aa580ebe7e78/events/ed8e79b6-c7d1-4332-adcf-70d37546c5a6/ticks/
                       635709308140011864
Level                : Informational
OperationId          : d724ffd0-31a4-4564-941b-f3a5d32ad8a4
OperationName        : Microsoft.Authorization/roleAssignments/write
Properties           :
                       requestbody    : {"Id":"531f036a-37ff-40c1-9bb9-aa580ebe7e78","Properties":{"PrincipalId":"dda50
                       086-5e3d-4a4b-b8bc-f54771104d89","RoleDefinitionId":"/subscriptions/ff945b8d-441a-41ef-a9db-7bd5
                       fcc99978/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7"
                       ,"Scope":"/subscriptions/ff945b8d-441a-41ef-a9db-7bd5fcc99978/resourceGroups/Default-Storage-Wes
                       tUS/providers/Microsoft.ClassicStorage/storageAccounts/authzwaes"}}
ResourceGroupName    : Default-Storage-WestUS
ResourceProviderName : Microsoft.Authorization
ResourceId           : /subscriptions/ff945b8d-441a-41ef-a9db-7bd5fcc99978/resourceGroups/Default-Storage-WestUS/provid
                       ers/Microsoft.ClassicStorage/storageAccounts/authzwaes/providers/Microsoft.Authorization/roleAss
                       ignments/531f036a-37ff-40c1-9bb9-aa580ebe7e78
Status               : Started
SubmissionTimestamp  : 6/26/2015 3:53:50 PM
SubscriptionId       : ff945b8d-441a-41ef-a9db-7bd5fcc99978
SubStatus            :`
```

Les informations contenues dans l’événement sont interprétées comme suit :

| Champ | Valeur | Détails |
| --- | --- | --- |
| Appelant |	`William.Hennum@contoso.com` | Le composant principal qui a effectué l’attribution de rôle. Il peut s’agir d’un utilisateur, d’un groupe, ou d’un principal de service.
| Requête HTTP : Method | `PUT` | L’action effectuée. PUT accorde une affectation et DELETE en supprime une. |
| Propriétés : PrincipalId | `dda50086-5e3d-4a4b-b8bc-f54771104d89` | 	L’ID d’objet du principal affecté au rôle. Il peut s’agir d’un utilisateur, d’un groupe, ou d’un principal de service. Vous pouvez déterminer le nom et le type du principal en le recherchant dans Azure Active Directory à l’aide d’Azure PowerShell. |
| Propriétés : RoleDefinitionId |	`/subscriptions/ff945b8d-441a-41ef-a9db-7bd5fcc99978/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7	` | Le rôle a été attribué. Vous pouvez déterminer le nom complet du rôle à l’aide d’Azure PowerShell. |
| Propriétés : Scope | `/subscriptions/ff945b8d-441a-41ef-a9db-7bd5fcc99978/resourceGroups/Default-Storage-WestUS/providers/Microsoft.ClassicStorage/storageAccounts/authzwaes` |	La ressource sur laquelle l’attribution du rôle a été effectuée. Il peut s’agir d’une ressource, d’un groupe de ressource ou d’un abonnement. |

###Exemples d’extraits de code PowerShell

Tout d’abord, voici un exemple de code Azure PowerShell permettant de mapper un élément PrincipalId sur un nom et un type :

```
# Sample - how to resolve a principal
function Get-PrincipalDetails($principalId)
{
    $principalDetails = "" | select Name, Type
    $user = Get-AzureADUser -ObjectId $principalId
    if ($user) {
        $principalDetails.Name = $user.DisplayName
        $principalDetails.Type = "User"
    } else {
        $group = Get-AzureADGroup -ObjectId $principalId
        if ($group) {
            $principalDetails.Name = $group.DisplayName
            $principalDetails.Type = "Group"
        } else {
            $servicePrincipal = Get-AZureADServicePrincipal -objectId $principalId
            if ($servicePrincipal) {
                $principalDetails.Name = $servicePrincipal.DisplayName
                $principalDetails.Type = "Service Principal"
            }
        }
    }

    $principalDetails
}
```

Ensuite, voici un exemple de code Azure PowerShell permettant de mapper un élément Scope sur un nom et type de ressource :

```
# Sample - how to resolve a resource
function Get-ResourceDetails($resourceId)
{
    $resourceDetails = "" | select Name, Type
    $resource = Get-AzureResource -Id $resourceId -OutputObjectFormat New
    if ($resource) {
        if ($resource.ResourceName) {
            $resourceDetails.Name = $resource.ResourceName
            $resourceDetails.Type = "Resource"
        } elseif ($resource.ResourceGroupName) {
            $resourceDetails.Name = $resource.ResourceGroupName
            $resourceDetails.Type = "Resource Group"
        } elseif ($resource.SubscriptionId) {
            $resourceDetails.Name = $resource.SubscriptionId
            $resourceDetails.Type = "Subscription"
        }
    }
    $resourceDetails
}
```
Voici un exemple de code Azure PowerShell permettant de mapper un élément RoleDefinitionId sur un nom complet de rôle :

```

# Get the name of a role
function Get-AzureRoleDefinitionName($roleDefinitionId)
{
    if (!$Global:_azureRoleDefinitionCache) {
        $Global:_azureRoleDefinitionCache = @{}
        Get-AzureRoleDefinition | % { $Global:_azureRoleDefinitionCache[$_.Id] = $_; }
    }

    if ($Global:_azureRoleDefinitionCache[$roleDefinitionId]) {
        return $Global:_azureRoleDefinitionCache[$roleDefinitionId].Name
    } else {
        return ""
    }
}

```

###Exemple de script Azure PowerShell

Voici un exemple complet de script qui récupère les événements d’affectation de rôle sur une plage de dates considérée et les place dans un tableau :

```
# Sample - how to resolve a principal
function Get-PrincipalDetails($principalId)
{
    $principalDetails = "" | select Name, Type
    $user = Get-AzureADUser -ObjectId $principalId
    if ($user) {
        $principalDetails.Name = $user.DisplayName
        $principalDetails.Type = "User"
    } else {
        $group = Get-AzureADGroup -ObjectId $principalId
        if ($group) {
            $principalDetails.Name = $group.DisplayName
            $principalDetails.Type = "Group"
        } else {
            $servicePrincipal = Get-AZureADServicePrincipal -objectId $principalId
            if ($servicePrincipal) {
                $principalDetails.Name = $servicePrincipal.DisplayName
                $principalDetails.Type = "Service Principal"
            }
        }
    }

    $principalDetails
}
# Sample - how to resolve a resource
function Get-ResourceDetails($resourceId)
{
    $resourceDetails = "" | select Name, Type
    $resource = Get-AzureResource -Id $resourceId -OutputObjectFormat New
    if ($resource) {
        if ($resource.ResourceName) {
            $resourceDetails.Name = $resource.ResourceName
            $resourceDetails.Type = "Resource"
        } elseif ($resource.ResourceGroupName) {
            $resourceDetails.Name = $resource.ResourceGroupName
            $resourceDetails.Type = "Resource Group"
        } elseif ($resource.SubscriptionId) {
            $resourceDetails.Name = $resource.SubscriptionId
            $resourceDetails.Type = "Subscription"
        }
    }
    $resourceDetails
}
# Get the name of a role
function Get-AzureRoleDefinitionName($roleDefinitionId)
{
    if (!$Global:_azureRoleDefinitionCache) {
        $Global:_azureRoleDefinitionCache = @{}
        Get-AzureRoleDefinition | % { $Global:_azureRoleDefinitionCache[$_.Id] = $_; }
    }

    if ($Global:_azureRoleDefinitionCache[$roleDefinitionId]) {
        return $Global:_azureRoleDefinitionCache[$roleDefinitionId].Name
    } else {
        return ""
    }
}
# Sample - output the list of role assignment events
function Get-AzureRBACAuditLog($startDateTime, $endDateTime)
{
    $log = Get-AzureSubscriptionIdLog -DetailedOutput -StartTime $startDateTime -EndTime $endDateTime
    $log = $log | ? { $_.ResourceProviderName -ieq "Microsoft.Authorization" }
    $startEvents = $log | ? { $_.httpRequest -and $_.Status -ieq "Started" }
    $endEvents = @{}
    $log | ? { $_.httpRequest -and $_.Status -ne "Started" } | % { $endEvents[$_.OperationId] = $_ }

    $startEvents | ? { $endEvents.ContainsKey($_.OperationId) } | % {
        $endEvent = $endEvents[$_.OperationId];
        $out = "" | select Timestamp, Caller, Action, PrincipalId, PrincipalName, PrincipalType, RoleName, Scope, ScopeName, ScopeType, RoleDefinitionId
        $out.Timestamp = $endEvent.EventTimestamp
        $out.Caller = $_.Caller
        if ($_.HttpRequest.Method -ieq "PUT") {
            $out.Action = "Granted"
            if ($_.Properties.Content.ContainsKey("requestbody")) {
                $messageBody = ConvertFrom-Json $_.Properties.Content["requestbody"]
            }
        }
        elseif ($_.HttpRequest.Method -ieq "DELETE") {
            $out.Action = "Revoked"
            if ($endEvent.Properties.Content.ContainsKey("responseBody")) {
                $messageBody = ConvertFrom-Json $endEvent.Properties.Content["responseBody"]
            }
        }

        if ($messageBody) {
            $out.PrincipalId = $messageBody.properties.principalId
            $pd = Get-PrincipalDetails $out.PrincipalId
            $out.PrincipalName = $pd.Name
            $out.PrincipalType = $pd.Type
            $out.RoleName = (Get-AzureRoleDefinitionName $messageBody.properties.roleDefinitionId)
            $out.Scope = $messageBody.properties.Scope
            $rd = Get-ResourceDetails $out.Scope
            $out.ScopeName = $rd.Name
            $out.ScopeType = $rd.Type
            $out.RoleDefinitionId = $messageBody.properties.roleDefinitionId
        }

        $out
    }
}

```

Et voici les commandes permettant d’exécuter le script

```
$log = Get-AzureRBACAuditLog '2015-06-26' '2015-06-27'

$log | Format-Table
```

## Problèmes connus lors de l'utilisation du contrôle d'accès basé sur un rôle

En cas de problème lors de l'utilisation de la fonctionnalité de contrôle d'accès basé sur les rôles, consultez la section [Résolution des problèmes de contrôle d'accès basé sur les rôles](role-based-access-control-troubleshooting.md) pour tout problème connu pouvant être lié à votre problème.


## Rôles intégrés

Le contrôle d'accès basé sur un rôle inclut les trois rôles intégrés suivants qui peuvent être affectés aux utilisateurs, groupes et services. Vous ne pouvez pas modifier la définition des rôles intégrés. Dans une version ultérieure d'Azure RBAC, vous pourrez définir des rôles personnalisés en composant un ensemble d'actions à partir d'une liste des actions applicables aux ressources Azure.

Cliquez sur le lien correspondant pour voir les propriétés **actions** et **not actions** d'une définition de rôle. La propriété **actions** spécifie les actions autorisées sur les ressources Azure. Les chaînes d'action peuvent utiliser des caractères génériques. La propriété **not actions** d'une définition de rôle spécifie les actions qui doivent être exclues des actions autorisées.


Nom de rôle | Description
------------- | -------------  
[Collaborateur du service de gestion des API](#api-management-service-contributor) | Vous permet de gérer le service de gestion des API, mais pas d'y accéder.
[Collaborateur de composants Application Insights](#application-insights-component-contributor) | Vous permet de gérer les composants Application Insights, mais pas d'y accéder.
[Collaborateur BizTalk](#biztalk-contributor) | Vous permet de gérer les services BizTalk, mais pas d'y accéder.
[Collaborateur de base de données ClearDB MySQL](#cleardb-mysql-db-contributor) | Vous permet de gérer des bases de données ClearDB MySQL, mais pas d'y accéder.
[Collaborateur](#contributor) | Les collaborateurs peuvent gérer tout sauf les accès.
[Collaborateurs de fabrique de données](#data-factory-contributor) | Vous permet de gérer les fabriques de données, mais pas d'y accéder.
[Collaborateur de compte de base de données de document](#document-db-account-contributor) | Vous permet de gérer les comptes DocumentDB, mais pas d'y accéder.
[Collaborateur de compte Intelligent Systems](#intelligent-systems-account-contributor) | Vous permet de gérer les comptes Intelligent Systems, mais pas d'y accéder.
[Collaborateur de compte NewRelic APM](#newrelic-apm-account-contributor) | Vous permet de gérer les comptes et applications New Relic Application Performance Management, mais pas d'y accéder.
[Propriétaire](#owner) | Le propriétaire peut tout gérer, y compris l'accès.
[Lecteur](#reader) | Les lecteurs peuvent tout afficher, mais pas apporter de modifications.
[Collaborateur Cache Redis](#redis-cache-contributor) | Vous permet de gérer les caches Redis, mais pas d'y accéder.
[Collaborateur de base de données SQL](#sql-db-contributor) | Vous permet de gérer des bases de données SQL, mais pas d'y accéder. En outre, vous ne pouvez pas gérer leurs stratégies de sécurité ni leurs serveurs SQL parents.
[Gestionnaire de sécurité SQL](#sql-security-manager) | Vous permet de gérer les stratégies de sécurité des serveurs et bases de données SQL, mais pas d'y accéder.
[Collaborateur SQL Server](#sql-server-contributor) | Vous permet de gérer les serveurs et bases de données SQL, mais pas d'y accéder ni de gérer leurs stratégies de sécurité.
[Collaborateur des collections de travaux du planificateur](#scheduler-job-collections-contributor) | Vous permet de gérer les collections de travaux du planificateur, mais pas d'y accéder.
[Collaborateur du service de recherche](#search-service-contributor) | Vous permet de gérer les services de recherche, mais pas d'y accéder.
[Collaborateur de compte de stockage](#storage-account-contributor) | Vous permet de gérer les comptes de stockage, mais pas d'y accéder.
[Administrateur de l'accès utilisateur](#user-access-administrator) | Vous permet de gérer l'accès utilisateur aux ressources Azure.
[Collaborateur de machine virtuelle](#virtual-machine-contributor) | Vous permet de gérer les machines virtuelles, mais pas d'y accéder ni de gérer le réseau virtuel ou le compte de stockage auxquels elles sont connectées.
[Collaborateur de réseau virtuel](#virtual-network-contributor) | Vous permet de gérer les réseaux virtuels, mais pas d'y accéder.
[Collaborateur de plan web](#web-plan-contributor) | Vous permet de gérer les plans web des sites web, mais pas d'y accéder.
[Collaborateur de site web](#website-contributor) | Vous permet de gérer les sites web (pas les plans web), mais pas d'y accéder.


### Collaborateur du service de gestion des API

<table style=width:100%">
<tr>
<th colspan="2">Actions</th>
</tr>
<tr>
<td>Microsoft.ApiManagement/Services/*</td>
<td>Créer et gérer les services de gestion des API</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>Lire les rôles et les affectations de rôles</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>Lire les groupes de ressources</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>Créer et gérer les déploiements de groupes de ressources</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>Créer et gérer les règles d'alerte</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>Créer et gérer les tickets de support</td>
</tr>
</table>

### Collaborateur des composants Application Insights

<table style=width:100%">
<tr>
<th colspan="2">Actions</th>
</tr>
<tr>
<td>Microsoft.Insights/components/*</td>
<td>Créer et gérer les composants Insights</td>
</tr>
<tr>
<td>Microsoft.Insights/webtests/*</td>
<td>Créer et gérer les tests web</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>Lire les rôles et les affectations de rôles</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>Lire les groupes de ressources</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>Créer et gérer les déploiements de groupes de ressources</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>Créer et gérer les règles d'alerte</td>
</tr>
<td>Microsoft.Support/*</td>
<td>Créer et gérer les tickets de support</td>
</tr>
</table>

### Collaborateur BizTalk

<table style=width:100%">
<tr>
<th colspan="2">Actions</th>
</tr>
<tr>
<td>Microsoft.BizTalkServices/BizTalk/*</td>
<td>Créer et gérer les services BizTalk</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>Lire les rôles et les affectations de rôles</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>Lire les groupes de ressources</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>Créer et gérer les déploiements de groupes de ressources</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>Créer et gérer les règles d'alerte</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>Créer et gérer les tickets de support</td>
</tr>
</table>

### Collaborateur de base de données ClearDB MySQL

<table style=width:100%">
<tr>
<th colspan="2">Actions</th>
</tr>
<tr>
<td>successbricks.cleardb/databases/*</td>
<td>Créer et gérer les bases de données ClearDB MySQL</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>Lire les rôles et les affectations de rôles</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>Lire les groupes de ressources</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>Créer et gérer les déploiements de groupes de ressources</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>Créer et gérer les règles d'alerte</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>Créer et gérer les tickets de support</td>
</tr>
</table>

### Collaborateur

<table style=width:100%">
<tr>
<th colspan="2">Actions</th>
</tr>
<tr>
<td>*</td>
<td>Créer et gérer les ressources de tous les types</td>
</tr>
<tr>
<th colspan="2">Non-actions</th>
</tr>
<tr>
<td>Microsoft.Authorization/*/Write</td>
<td>Impossible de créer des rôles et des affectations de rôles </td>
</tr>
<tr>
<td>Microsoft.Authorization/*/Delete</td>
<td>Impossible de supprimer des rôles et des affectations de rôles</td>
</tr>
</table>

### Collaborateurs de fabrique de données

<table style=width:100%">
<tr>
<td>Microsoft.DataFactory/dataFactories/*</td>
<td>Créer et gérer les fabriques de données</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>Lire les rôles et les affectations de rôles</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>Lire les groupes de ressources</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>Créer et gérer les déploiements de groupes de ressources</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>Créer et gérer les règles d'alerte</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>Créer et gérer les tickets de support</td>
</tr>
</table>

### Collaborateur de compte de base de données de document

<table style=width:100%">
<tr>
<th colspan="2">Actions</th>
</tr>
<tr>
<td>Microsoft.DocumentDb/databaseAccounts/*</td>
<td>Créer et gérer les comptes DocumentDB</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>Lire les rôles et les affectations de rôles</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>Lire les groupes de ressources</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>Créer et gérer les déploiements de groupes de ressources</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>Créer et gérer les règles d'alerte</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>Créer et gérer les tickets de support</td>
</tr>
</table>

### Collaborateur de compte Intelligent Systems

<table style=width:100%">
<tr>
<th colspan="2">Actions</th>
</tr>
<tr>
<td>Microsoft.IntelligentSystems/accounts/*</td>
<td>Créer et gérer les comptes Intelligent Systems</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>Lire les rôles et les affectations de rôles</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>Lire les groupes de ressources</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>Créer et gérer les déploiements de groupes de ressources</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>Créer et gérer les règles d'alerte</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>Créer et gérer les tickets de support</td>
</tr>
</table>

### Collaborateur de compte NewRelic APM

<table style=width:100%">
<tr>
<th colspan="2">Actions</th>
</tr>
<tr>
<td>NewRelic.APM/accounts/*</td>
<td>Créer et gérer les comptes NewRelic Application Performance Management</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>Lire les rôles et les affectations de rôles</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>Lire les groupes de ressources</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>Créer et gérer les déploiements de groupes de ressources</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>Créer et gérer les règles d'alerte</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>Créer et gérer les tickets de support</td>
</tr>
</table>

### Propriétaire

<table style=width:100%">
<tr>
<th colspan="2">Actions</th>
</tr>
<tr>
<td>*</td>
<td>Créer et gérer les ressources de tous les types</td>
</tr>
</table>

### Lecteur

<table style=width:100%">
<tr>
<th colspan="2">Actions</th>
</tr>
<tr>
<td>*/read</td>
<td>Lire les ressources de tous les types. Impossible de lire les secrets.</td>
</tr>
</table>

### Collaborateur Redis Cache

<table style=width:100%">
<tr>
<td>Microsoft.Cache/redis/*</td>
<td>Créer et gérer les caches Redis</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>Lire les rôles et les affectations de rôles</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>Lire les groupes de ressources</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>Créer et gérer les déploiements de groupes de ressources</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>Créer et gérer les règles d'alerte</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>Créer et gérer les tickets de support</td>
</tr>
</table>

### Collaborateur de base de données SQL

<table style=width:100%">
<tr>
<th colspan="2">Actions</th>
</tr>
<tr>
<td>Microsoft.Sql/servers/read</td>
<td>Lire les serveurs SQL</td>
</tr>
<tr>
<td>Microsoft.Sql/servers/databases/*</td>
<td>Créer et gérer les bases de données SQL</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>Lire les rôles et les affectations de rôles</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resources/read</td>
<td>Lire les ressources de l'abonnement</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>Lire les groupes de ressources</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/resources/read</td>
<td>Lire les ressources du groupe de ressources</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>Créer et gérer les déploiements de groupes de ressources</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>Créer et gérer les règles d'alerte</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>Créer et gérer les tickets de support</td>
</tr>
<tr>
<th colspan="2">Non-actions</th>
</tr>
<tr>
<td>Microsoft.Sql/servers/databases/auditingPolicies/*</td>
<td>Impossible de gérer les stratégies d'audit de base de données SQL</td>
</tr>
<tr>
<td>Microsoft.Sql/servers/databases/connectionPolicies/*</td>
<td>Impossible de gérer les stratégies de connexion de base de données SQL</td>
</tr>
<tr>
<td>Microsoft.Sql/servers/databases/dataMaskingPolicies/*</td>
<td>Impossible de gérer les stratégies de masquage de données de base de données SQL</td>
</tr>
<tr>
<td>Microsoft.Sql/servers/databases/securityMetrics/*</td>
<td>Impossible de gérer les mesures de sécurité de base de données SQL</td>
</tr>
</table>

### Gestionnaire de sécurité SQL

<table style=width:100%">
<tr>
<th colspan="2">Actions</th>
</tr>
<tr>
<td>Microsoft.Sql/servers/read</td>
<td>Lire les serveurs SQL</td>
</tr>
<tr>
<td>Microsoft.Sql/servers/auditingPolicies/*</td>
<td>Créer et gérer les stratégies d'audit SQL Server</td>
</tr>
<tr>
<td>Microsoft.Sql/servers/databases/read</td>
<td>Lire les bases de données SQL</td>
</tr>
<tr>
<td>Microsoft.Sql/servers/databases/auditingPolicies/*</td>
<td>Créer et gérer les stratégies d'audit de base de données SQL</td>
</tr>
<tr>
<td>Microsoft.Sql/servers/databases/connectionPolicies/*</td>
<td>Créer et gérer les stratégies de connexion de base de données SQL</td>
</tr>
<tr>
<td>Microsoft.Sql/servers/databases/dataMaskingPolicies/*</td>
<td>Créer et gérer les stratégies de masquage de données de base de données SQL</td>
</tr>
<tr>
<td>Microsoft.Sql/servers/databases/securityMetrics/*</td>
<td>Créer et gérer les mesures de sécurité de base de données SQL</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>Lire les rôles et les affectations de rôles</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>Lire les groupes de ressources</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>Créer et gérer les déploiements de groupes de ressources</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>Créer et gérer les règles d'alerte</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>Créer et gérer les tickets de support</td>
</tr>
</table>

### Collaborateur SQL Server

<table style=width:100%">
<tr>
<th colspan="2">Actions</th>
</tr>
<tr>
<td>Microsoft.Sql/servers/*</td>
<td>Créer et gérer les serveurs SQL</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>Lire les rôles et les affectations de rôles</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resources/read</td>
<td>Lire les ressources de l'abonnement</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>Lire les groupes de ressources</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/resources/read</td>
<td>Lire les ressources du groupe de ressources</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>Créer et gérer les déploiements de groupes de ressources</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>Créer et gérer les règles d'alerte</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>Créer et gérer les tickets de support</td>
</tr>
<tr>
<th colspan="2">Non-actions</th>
</tr>
<tr>
<td>Microsoft.Sql/servers/auditingPolicies/*</td>
<td>Impossible de gérer les stratégies d'audit SQL Server</td>
</tr>
<tr>
<td>Microsoft.Sql/servers/databases/auditingPolicies/*</td>
<td>Impossible de gérer les stratégies d'audit de base de données SQL</td>
</tr>
<tr>
<td>Microsoft.Sql/servers/databases/connectionPolicies/*</td>
<td>Impossible de gérer les stratégies de connexion de base de données SQL</td>
</tr>
<tr>
<td>Microsoft.Sql/servers/databases/dataMaskingPolicies/*</td>
<td>Impossible de gérer les stratégies de masquage de données de base de données SQL</td>
</tr>
<tr>
<td>Microsoft.Sql/servers/databases/securityMetrics/*</td>
<td>Impossible de gérer les mesures de sécurité de base de données SQL</td>
</tr>
</table>

### Collaborateur des collections de travaux du planificateur

<table style=width:100%">
<tr>
<th colspan="2">Actions</th>
</tr>
<tr>
<td>Microsoft.Scheduler/jobcollections/*</td>
<td>Créer et gérer des Collections de travaux du planificateur</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>Lire les rôles et les affectations de rôles</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>Lire les groupes de ressources</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>Créer et gérer les déploiements de groupes de ressources</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>Créer et gérer les règles d'alerte</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>Créer et gérer les tickets de support</td>
</tr>
</table>

### Collaborateur du service de recherche

<table style=width:100%">
<tr>
<th colspan="2">Actions</th>
</tr>
<tr>
<td>Microsoft.Search/searchServices/*</td>
<td>Créer et gérer les services de recherche</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>Lire les rôles et les affectations de rôles</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>Lire les groupes de ressources</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>Créer et gérer les déploiements de groupes de ressources</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>Créer et gérer les règles d'alerte</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>Créer et gérer les tickets de support</td>
</tr>
</table>

### Collaborateur de compte de stockage

<table style=width:100%">
<tr>
<th colspan="2">Actions</th>
</tr>
<tr>
<td>Microsoft.ClassicStorage/storageAccounts/*</td>
<td>Créer et gérer les comptes de stockage</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>Lire les rôles et les affectations de rôles</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>Lire les groupes de ressources</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>Créer et gérer les déploiements de groupes de ressources</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>Créer et gérer les règles d'alerte</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>Créer et gérer les tickets de support</td>
</tr>
</table>

### Administrateur de l'accès utilisateur

<table style=width:100%">
<tr>
<th colspan="2">Actions</th>
</tr>
<tr>
<td>*/read</td>
<td>Lire les ressources de tous les types</td>
</tr>
<tr>
<td>Microsoft.Authorization/*</td>
<td>Créer et gérer les rôles et affectations de rôles</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>Créer et gérer les tickets de support</td>
</tr>
</table>

### Collaborateur de machine virtuelle

<table style=width:100%">
<tr>
<th colspan="2">Actions</th>
</tr>
<tr>
<td>Microsoft.ClassicStorage/storageAccounts/read</td>
<td>Lire les comptes de stockage</td>
</tr>
<tr>
<td>Microsoft.ClassicStorage/storageAccounts/listKeys/action</td>
<td>Récupérer les clés de compte de stockage</td>
</tr>
<tr>
<td>Microsoft.ClassicNetwork/virtualNetworks/read</td>
<td>Lire les réseaux virtuels</td>
</tr>
<tr>
<td>Microsoft.ClassicNetwork/virtualNetworks/join/action</td>
<td>Rejoindre des réseaux virtuels</td>
</tr>
<tr>
<td>Microsoft.ClassicNetwork/reservedIps/read</td>
<td>Lire des adresses IP réservées</td>
</tr>
<tr>
<td>Microsoft.ClassicNetwork/reservedIps/link/action</td>
<td>Créer un lien vers des adresses IP réservées</td>
</tr>
<tr>
<td>Microsoft.ClassicCompute/domainNames/*</td>
<td>Créer et gérer les services cloud</td>
</tr>
<tr>
<td>Microsoft.ClassicCompute/virtualMachines/*</td>
<td>Créer et gérer les machines virtuelles</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>Lire les rôles et les affectations de rôles</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>Lire les groupes de ressources</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>Créer et gérer les déploiements de groupes de ressources</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>Créer et gérer les règles d'alerte</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>Créer et gérer les tickets de support</td>
</tr>
</table>

### Collaborateur de réseau virtuel

<table style=width:100%">
<tr>
<th colspan="2">Actions</th>
</tr>
<tr>
<td>Microsoft.ClassicNetwork/virtualNetworks/*</td>
<td>Créer et gérer les réseaux virtuels</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>Lire les rôles et les affectations de rôles</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>Lire les groupes de ressources</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>Créer et gérer les déploiements de groupes de ressources</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>Créer et gérer les règles d'alerte</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>Créer et gérer les tickets de support</td>
</tr>
</table>

### Collaborateur de plan web

<table style=width:100%">
<tr>
<th colspan="2">Actions</th>
</tr>
<tr>
<td>Microsoft.Web/serverFarms/*</td>
<td>Créer et gérer les plans web</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>Lire les rôles et les affectations de rôles</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>Lire les groupes de ressources</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>Créer et gérer les déploiements de groupes de ressources</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>Créer et gérer les règles d'alerte</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>Créer et gérer les tickets de support</td>
</tr>
</table>

### Collaborateur de site web

<table style=width:100%">
<tr>
<th colspan="2">Actions</th>
</tr>
<tr>
<td>Microsoft.Web/serverFarms/read</td>
<td>Lire les plans web</td>
</tr>
<tr>
<td>Microsoft.Web/serverFarms/join/action</td>
<td>Rejoindre les plans web</td>
</tr>
<tr>
<td>Microsoft.Web/sites/*</td>
<td>Créer et gérer les sites web</td>
</tr>
<tr>
<td>Microsoft.Web/certificates/*</td>
<td>Créer et gérer les certificats de site web</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>Lire les rôles et les affectations de rôles</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>Lire les groupes de ressources</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>Créer et gérer les déploiements de groupes de ressources</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>Créer et gérer les règles d'alerte</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>Créer et gérer les tickets de support</td>
</tr>
</table>


## Commentaires

Essayez Azure RBAC et envoyez-nous vos [commentaires](http://aka.ms/azurerbacfeedback).


## Étapes suivantes

Voici quelques ressources supplémentaires destinées à faciliter votre utilisation du contrôle d'accès en fonction du rôle :

+ [Gestion du contrôle d'accès basé sur les rôles à l'aide de Windows PowerShell](role-based-access-control-powershell.md)
+ [Gestion du contrôle d'accès basé sur les rôles à l'aide de la CLI Azure](role-based-access-control-xplat-cli.md)
+ [Résolution des problèmes de contrôle d'accès basé sur les rôles](role-based-access-control-troubleshooting.md)
+ [Azure Active Directory Premium et De base](active-directory-editions.md)
+ [Association des abonnements Azure avec Azure AD](active-directory-how-subscriptions-associated-directory.md)
+ Pour une présentation de la gestion de groupe libre-service pour les groupes de sécurité, voir le [Blog de l'équipe Active Directory](http://blogs.technet.com/b/ad/archive/2014/02/24/more-preview-enhancements-for-windows-azure-ad-premium.aspx)

<!--Image references-->
[1]: ./media/role-based-access-control-configure/RBACSubAuthDir.png
[2]: ./media/role-based-access-control-configure/RBACAssignmentScopes.png
[3]: ./media/role-based-access-control-configure/RBACSubscriptionBlade.png
[4]: ./media/role-based-access-control-configure/RBACAddSubReader_NEW.png
[5]: ./media/role-based-access-control-configure/RBACAddRGContributor_NEW.png
[6]: ./media/role-based-access-control-configure/RBACAddProdContributor_NEW.png
[7]: ./media/role-based-access-control-configure/RBACRemoveRole.png
[8]: ./media/role-based-access-control-configure/RBACGuestAccessControls.png
[9]: ./media/role-based-access-control-configure/RBACInviteExtUser_NEW.png
[10]: ./media/role-based-access-control-configure/RBACDirConfigTab.png

<!---HONumber=July15_HO5-->