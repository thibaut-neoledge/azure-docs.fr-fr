<properties 
	pageTitle="Opérations d’audit avec Resource Manager | Microsoft Azure" 
	description="Utilisez le journal d’audit dans Resource Manager pour passer en revue les actions et les erreurs des utilisateurs. Affiche PowerShell, l’interface de ligne de commande Azure et REST." 
	services="azure-resource-manager" 
	documentationCenter="" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="azure-resource-manager" 
	ms.workload="multiple" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/10/2015" 
	ms.author="tomfitz"/>

# Opérations d’audit avec Resource Manager

Lorsque vous rencontrez un problème lors du déploiement ou pendant la durée de vie de votre solution, vous devez découvrir ce qui s’est produit. Resource Manager fournit deux méthodes vous permettant de découvrir ce qui s’est passé et pourquoi. Vous pouvez utiliser les commandes de déploiement pour récupérer des informations concernant des déploiements et des opérations spécifiques. Vous pouvez également utiliser les journaux d’audit pour récupérer des informations concernant des déploiements et d’autres actions effectuées pendant la durée de vie de la solution. Cette rubrique se concentre sur les journaux d’audit.

Le journal d’audit contient toutes les actions effectuées sur vos ressources. Par conséquent, si un utilisateur de votre organisation modifie une ressource, vous serez en mesure d’identifier l’action, le temps et l’utilisateur.

Vous pouvez récupérer des informations à partir des journaux d’audit par le biais d’Azure PowerShell, de l’interface de ligne de commande Azure, de l’API REST ou du portail Azure en version préliminaire.

## PowerShell

Pour récupérer les entrées de journal, exécutez la commande **Get-AzureResourceGroupLog**. Vous spécifiez des paramètres supplémentaires pour filtrer la liste des entrées.

L’exemple suivant montre comment utiliser le journal d’audit pour rechercher les actions effectuées pendant le cycle de vie de la solution. Vous pouvez voir le moment où l’action s’est produite et l’utilisateur qui l’a demandée.

    PS C:\> Get-AzureResourceGroupLog -ResourceGroup ExampleGroup -StartTime 2015-08-28T06:00

En fonction de l’heure de début que vous spécifiez, la commande précédente peut renvoyer une longue liste des actions pour ce groupe de ressources. Vous pouvez filtrer les résultats de votre recherche en fournissant des critères de recherche. Par exemple, si vous tentez de rechercher la manière dont une application web a été arrêtée, vous pouvez exécuter la commande suivante et voir qu’une action d’arrêt a été effectuée par someone@example.com.

    PS C:\> Get-AzureResourceGroupLog -ResourceGroup ExampleGroup -StartTime 2015-08-28T06:00 | Where-Object OperationName -eq Microsoft.Web/sites/stop/action

    Authorization     :
                        Scope     : /subscriptions/xxxxx/resourcegroups/ExampleGroup/providers/Microsoft.Web/sites/ExampleSite
                        Action    : Microsoft.Web/sites/stop/action
                        Role      : Subscription Admin
                        Condition :
    Caller            : someone@example.com
    CorrelationId     : 84beae59-92aa-4662-a6fc-b6fecc0ff8da
    EventSource       : Administrative
    EventTimestamp    : 8/28/2015 4:08:18 PM
    OperationName     : Microsoft.Web/sites/stop/action
    ResourceGroupName : ExampleGroup
    ResourceId        : /subscriptions/xxxxx/resourcegroups/ExampleGroup/providers/Microsoft.Web/sites/ExampleSite
    Status            : Succeeded
    SubscriptionId    : xxxxx
    SubStatus         : OK

Dans l’exemple suivant, nous allons rechercher uniquement les actions qui ont échoué après l’heure de début spécifiée. Nous allons également inclure le paramètre **DetailedOutput** pour voir les messages d’erreur.

    PS C:\> Get-AzureResourceGroupLog -ResourceGroup ExampleGroup -StartTime 2015-08-27T12:00 -Status Failed –DetailedOutput
    
Si cette commande renvoie trop d’entrées et de propriétés, vous pouvez cibler vos efforts d’audit en récupérant la propriété **properties**.

    PS C:\> (Get-AzureResourceGroupLog -Status Failed -ResourceGroup ExampleGroup -DetailedOutput).Properties

    Content
    -------
    {}
    {[statusCode, Conflict], [statusMessage, {"Code":"Conflict","Message":"Website with given name mysite already exists...
    {[statusCode, Conflict], [serviceRequestId, ], [statusMessage, {"Code":"Conflict","Message":"Website with given name...

En outre, vous pouvez affiner les résultats en examinant le message d’état.

    PS C:\> (Get-AzureResourceGroupLog -Status Failed -ResourceGroup ExampleGroup -DetailedOutput).Properties[1].Content["statusMessage"] | ConvertFrom-Json

    Code       : Conflict
    Message    : Website with given name mysite already exists.
    Target     :
    Details    : {@{Message=Website with given name mysite already exists.}, @{Code=Conflict}, @{ErrorEntity=}}
    Innererror :


## Interface de ligne de commande Azure

Pour récupérer les entrées de journal, exécutez la commande **azure group log show**.

    azure group log show ExampleGroup

Vous pouvez filtrer les résultats avec un utilitaire JSON comme [jq](http://stedolan.github.io/jq/download/). L’exemple suivant montre comment rechercher des opérations qui impliquaient la mise à jour d’un fichier de configuration web.

    azure group log show ExampleGroup --json | jq ".[] | select(.operationName.localizedValue == "Update web sites config")"

Vous pouvez ajouter le paramètre **–-last-deployment** pour limiter les entrées renvoyées aux opérations à partir du dernier déploiement uniquement.

    azure group log show ExampleGroup --last-deployment

Si la liste des opérations à partir du dernier déploiement est trop longue, vous pouvez filtrer les résultats sur les opérations ayant échoué uniquement.

    azure group log show tfCopyGroup --last-deployment --json | jq ".[] | select(.status.value == "Failed")"

                                   /Microsoft.Web/Sites/ExampleSite
    data:    SubscriptionId:       <guid>
    data:    EventTimestamp (UTC): Thu Aug 27 2015 13:03:27 GMT-0700 (Pacific Daylight Time)
    data:    OperationName:        Update website
    data:    OperationId:          cb772193-b52c-4134-9013-673afe6a5604
    data:    Status:               Failed
    data:    SubStatus:            Conflict (HTTP Status Code: 409)
    data:    Caller:               someone@example.com
    data:    CorrelationId:        a8c7a2b4-5678-4b1b-a50a-c17230427b1e
    data:    Description:
    data:    HttpRequest:          clientRequestId: <guid>
                                   clientIpAddress: 000.000.000.000
                                   method:          PUT

    data:    Level:                Error
    data:    ResourceGroup:        ExampleGroup
    data:    ResourceProvider:     Azure Web Sites
    data:    EventSource:          Administrative
    data:    Properties:           statusCode:       Conflict
                                   serviceRequestId:
                                   statusMessage:    {"Code":"Conflict","Message":"Website with given name
                                   ExampleSite already exists.","Target":null,"
                                   Details
                                   ":[{"Message":"Website with given name ExampleSite already exists."},
                                   {"Code":"Conflict"},
                                   {"ErrorEntity":{"Code":"Conflict","Message":"Website with given
                                   name ExampleSite already exists.","ExtendedCode
                                   ":"
                                   54001","MessageTemplate":"Website with given name {0} already exists.",
                                   "Parameters":["ExampleSite"],"
                                   InnerErrors":null}}],"Innererror":null}



## API REST

Les opérations REST à utiliser avec le journal d’audit font partie de l’[API REST Insights](https://msdn.microsoft.com/library/azure/dn931943.aspx). Pour récupérer les événements du journal d’audit, consultez [Liste des événements de gestion dans un abonnement](https://msdn.microsoft.com/library/azure/dn931934.aspx).

## Portail en version préliminaire

Vous pouvez également afficher des opérations journalisées via le portail en version préliminaire. Il vous suffit de sélectionner le panneau des journaux d’audit.

![sélectionner des journaux d’audit](./media/resource-group-audit/select-audit.png)

Ensuite, affichez la liste des dernières opérations.

![afficher des actions](./media/resource-group-audit/show-actions.png)

Sélectionnez n’importe quelle opération pour plus d’informations la concernant.

## Étapes suivantes

- Pour en savoir plus sur la configuration des stratégies de sécurité, consultez [Gestion de l’accès aux ressources](./azure-portal/resource-group-rbac.md).
- Pour savoir comment autoriser l’accès pour un principal du service, consultez [Authentification d’un principal du service à l’aide d’Azure Resource Manager](resource-group-authenticate-service-principal.md).
- Pour savoir comment effectuer des actions sur une ressource pour tous les utilisateurs, consultez [Verrouiller des ressources avec Azure Resource Manager](resource-group-lock-resources.md).

<!---HONumber=Sept15_HO3-->