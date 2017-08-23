---
title: "Procédure pas à pas d’utilisation de l’API REST d’Azure Monitor | Microsoft Docs"
description: "Comment authentifier les demandes et utiliser l’API REST Azure Monitor."
author: mcollier
manager: 
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 565e6a88-3131-4a48-8b82-3effc9a3d5c6
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/27/2016
ms.author: mcollier
ms.translationtype: HT
ms.sourcegitcommit: cddb80997d29267db6873373e0a8609d54dd1576
ms.openlocfilehash: 454a85c4752ec9c7522ef147d5ce594ef5992c32
ms.contentlocale: fr-fr
ms.lasthandoff: 07/18/2017

---
# <a name="azure-monitoring-rest-api-walkthrough"></a>Procédure pas à pas d’utilisation de l’API REST d’Azure Monitor
Cet article vous montre comment effectuer l’authentification afin que votre code puisse utiliser la [Référence de l’API REST Microsoft Azure Monitor](https://msdn.microsoft.com/library/azure/dn931943.aspx).         

L’API Azure Monitor permet de retrouver programmatiquement les définitions de mesures disponibles par défaut (par exemple l’heure de l’UC, les requêtes, etc.), la granularité et les valeurs des mesures. Une fois récupérées, les données peuvent être enregistrées dans un magasin de données distinct comme une base de données Azure SQL, Azure Cosmos DB ou Azure Data Lake. De là, une analyse supplémentaire peut être effectuée en fonction des besoins.

En plus de fonctionner avec divers points de données de mesures, comme cet article le montre, l’API Monitor permet de répertorier les règles d’alerte, d’afficher les journaux d’activité et plus encore. Pour obtenir la liste complète des opérations disponibles, consultez la [Référence de l’API REST Microsoft Azure Monitor](https://msdn.microsoft.com/library/azure/dn931943.aspx).

## <a name="authenticating-azure-monitor-requests"></a>Authentification des requêtes Azure Monitor
La première étape consiste à authentifier la requête.

Toutes les tâches exécutées sur l’API Azure Monitor utilisent le modèle d’authentification d’Azure Resource Manager. Ainsi, toutes les requêtes doivent être authentifiées avec Azure Active Directory (Azure AD). Une approche pour authentifier l’application client consiste à créer un principal du service Azure AD et récupérer le jeton d’authentification (JWT). L’exemple de script suivant illustre la création d’un principal de service Azure AD via PowerShell. Pour une présentation plus détaillée, reportez-vous à la documentation sur [l’utilisation d’Azure PowerShell pour créer un principal de service pour accéder aux ressources](../azure-resource-manager/resource-group-authenticate-service-principal.md#create-service-principal-with-password). Il est également possible de [créer un principal du service sur le portail Azure](../azure-resource-manager/resource-group-create-service-principal-portal.md).

```PowerShell
$subscriptionId = "{azure-subscription-id}"
$resourceGroupName = "{resource-group-name}"
$location = "centralus"

# Authenticate to a specific Azure subscription.
Login-AzureRmAccount -SubscriptionId $subscriptionId

# Password for the service principal
$pwd = "{service-principal-password}"

# Create a new Azure AD application
$azureAdApplication = New-AzureRmADApplication `
                        -DisplayName "My Azure Monitor" `
                        -HomePage "https://localhost/azure-monitor" `
                        -IdentifierUris "https://localhost/azure-monitor" `
                        -Password $pwd

# Create a new service principal associated with the designated application
New-AzureRmADServicePrincipal -ApplicationId $azureAdApplication.ApplicationId

# Assign Reader role to the newly created service principal
New-AzureRmRoleAssignment -RoleDefinitionName Reader `
                          -ServicePrincipalName $azureAdApplication.ApplicationId.Guid

```

Pour interroger l’API Azure Monitor, l’application client doit utiliser le principal de service créé précédemment pour s’authentifier. L’exemple de script PowerShell suivant montre une approche utilisant la [bibliothèque d’authentification Active Directory](../active-directory/active-directory-authentication-libraries.md) (ADAL) pour vous aider à obtenir le jeton d’authentification JWT. Le jeton JWT est passé en tant que partie d’un paramètre d’autorisation HTTP dans les demandes à l’API REST Azure Monitor.

```PowerShell
$azureAdApplication = Get-AzureRmADApplication -IdentifierUri "https://localhost/azure-monitor"

$subscription = Get-AzureRmSubscription -SubscriptionId $subscriptionId

$clientId = $azureAdApplication.ApplicationId.Guid
$tenantId = $subscription.TenantId
$authUrl = "https://login.microsoftonline.com/${tenantId}"

$AuthContext = [Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext]$authUrl
$cred = New-Object -TypeName Microsoft.IdentityModel.Clients.ActiveDirectory.ClientCredential -ArgumentList ($clientId, $pwd)

$result = $AuthContext.AcquireToken("https://management.core.windows.net/", $cred)

# Build an array of HTTP header values
$authHeader = @{
'Content-Type'='application/json'
'Accept'='application/json'
'Authorization'=$result.CreateAuthorizationHeader()
}
```

Une fois l’étape de configuration de l’authentification terminée, les requêtes peuvent ensuite être exécutées sur l’API REST d’Azure Monitor. Il existe deux requêtes utiles :

1. Répertorier les définitions des mesures pour une ressource
2. Récupérer les valeurs des mesures

## <a name="retrieve-metric-definitions"></a>Récupérer les définitions des mesures
> [!NOTE]
> Pour récupérer les définitions des mesures à l’aide de l’API REST d’Azure Monitor, utilisez « 2016-03-01 » en tant que la version de l’API.
>
>

```PowerShell
$apiVersion = "2016-03-01"
$request = "https://management.azure.com/subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/${resourceProviderNamespace}/${resourceType}/${resourceName}/providers/microsoft.insights/metricDefinitions?api-version=${apiVersion}"

Invoke-RestMethod -Uri $request `
                  -Headers $authHeader `
                  -Method Get `
                  -Verbose
```
Pour une application logique Azure, les définitions des mesures doivent ressembler à la capture d’écran suivante :

![Alt « Vue JSON de la réponse de définition de mesure. »](./media/monitoring-rest-api-walkthrough/available_metric_definitions_logic_app_json_response_clean.png)

Pour plus d’informations, consultez la documentation [Liste de définitions de mesure pour une ressource dans l’API REST d’Azure Monitor](https://msdn.microsoft.com/library/azure/mt743621.aspx) .

## <a name="retrieve-metric-values"></a>Récupérer des valeurs de mesure
Une fois les définitions de mesures disponibles connues, il est possible de récupérer les valeurs de mesure liées. Utilisez le nom 'value' (et non ' localizedValue') de la mesure pour toutes les demandes de filtrages (par exemple, pour récupérer les points de données de mesure « CpuTime » et « Requests »). Si aucun filtre n’est spécifié, la mesure par défaut est renvoyée.

> [!NOTE]
> Pour récupérer les valeurs des mesures à l’aide de l’API REST d’Azure Monitor, utilisez « 2016-06-01 » en tant que la version de l’API.
>
>

**Méthode**: GET

**URI de demande** : https://management.azure.com/subscriptions/*{id-abonnement}*/resourceGroups/*{nom-groupe-ressources}*/providers/*{espace-de-noms-fournisseur-ressources}*/*{type-ressource}*/*{nom-ressource}*/providers/microsoft.insights/metrics?$filter=*{filtre}*&api-version=*{version-api}*

Par exemple, pour retrouver les points de données de mesure RunsSucceeded pour la plage de temps spécifiée et avec un fragment de temps de 1 heure, la requête est la suivante :

```PowerShell
$apiVersion = "2016-06-01"
$filter = "(name.value eq 'RunsSucceeded') and aggregationType eq 'Total' and startTime eq 2016-09-23 and endTime eq 2016-09-24 and timeGrain eq duration'PT1H'"
$request = "https://management.azure.com/subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/${resourceProviderNamespace}/${resourceType}/${resourceName}/providers/microsoft.insights/metrics?`$filter=${filter}&api-version=${apiVersion}"
(Invoke-RestMethod -Uri $request `
                   -Headers $authHeader `
                   -Method Get `
                   -Verbose).Value | ConvertTo-Json
```

Le résultat devrait ressembler à l’exemple suivant en capture d’écran :

![ALT « Réponse JSON indiquant la valeur de mesure Temps de réponse moyen »](./media/monitoring-rest-api-walkthrough/available_metrics_logic_app_json_response.png)

Pour récupérer plusieurs points de données ou d’agrégation, ajoutez les noms de définition de mesure et les types d’agrégation au filtre, comme illustré dans l’exemple suivant :

```PowerShell
$apiVersion = "2016-06-01"
$filter = "(name.value eq 'ActionsCompleted' or name.value eq 'RunsSucceeded') and (aggregationType eq 'Total' or aggregationType eq 'Average') and startTime eq 2016-09-23T13:30:00Z and endTime eq 2016-09-23T14:30:00Z and timeGrain eq duration'PT1M'"
$request = "https://management.azure.com/subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/${resourceProviderNamespace}/${resourceType}/${resourceName}/providers/microsoft.insights/metrics?`$filter=${filter}&api-version=${apiVersion}"
(Invoke-RestMethod -Uri $request `
                   -Headers $authHeader `
                   -Method Get `
                   -Verbose).Value | ConvertTo-Json
```

### <a name="use-armclient"></a>Utiliser ARMClient
Une alternative à l’aide de PowerShell (comme indiqué ci-dessus), consiste à utiliser [ARMClient](https://github.com/projectkudu/ARMClient) sur votre ordinateur Windows. ARMClient gère automatiquement l’authentification Azure AD (et le jeton JWT résultant). Les étapes suivantes décrivent l’utilisation d’ARMClient pour récupérer les données de mesure :

1. Installez [Chocolatey](https://chocolatey.org/) et [ARMClient](https://github.com/projectkudu/ARMClient).
2. Dans une fenêtre de terminal, saisissez *armclient.exe login*. Vous êtes invité à vous connecter à Azure.
3. Tapez *armclient GET [id_ressource]/providers/microsoft.insights/metricdefinitions?api-version=2016-03-01*
4. Tapez *armclient GET [id_ressource]/providers/microsoft.insights/metrics?api-version=2016-06-01*

![Alt « Utilisation d’ARMClient pour travailler avec l’API REST Azure Monitor »](./media/monitoring-rest-api-walkthrough/armclient_metricdefinitions.png)

## <a name="retrieve-the-resource-id"></a>Récupérer l’ID de ressource
Utiliser l’API REST peut vraiment vous aider à comprendre les définitions de mesure disponibles, la granularité et les valeurs liées. Ces informations sont utiles lorsque vous utilisez la [bibliothèque de gestion Azure](https://msdn.microsoft.com/library/azure/mt417623.aspx).

Pour le code précédent, l’ID de ressource à utiliser est le chemin d’accès complet à la ressource Azure souhaitée. Par exemple, pour interroger une application web Azure, l’ID de ressource serait :

*/subscriptions/{subscription-id}/resourceGroups/{nom-groupe-ressources}/providers/Microsoft.Web/sites/{nom-site}/*

La liste suivante contient des exemples de formats d’ID de ressource pour les différentes ressources Azure :

* **IoT Hub** - /subscriptions/*{id-abonnement}*/resourceGroups/*{nom-groupe-ressources}*/providers/Microsoft.Devices/IotHubs/*{nom-iot-hub}*
* **Pool SQL élastique** - /subscriptions/*{id-abonnement}*/resourceGroups/*{nom-groupe-ressources}*/providers/Microsoft.Sql/servers/*{bd-pool}*/elasticpools/*{nom-pool-sql}*
* **SQL Database (v12)** - /subscriptions/*{id-abonnement}*/resourceGroups/*{nom-groupe-ressources}*/providers/Microsoft.Sql/servers/*{nom-serveur}*/databases/*{nom-bd}*
* **Service Bus** - /subscriptions/*{id-abonnement}*/resourceGroups/*{nom-groupe-ressources}*/providers/Microsoft.ServiceBus/*{espace-noms}*/*{nom-servicebus}*
* **Groupes de machines virtuelles identiques** - /subscriptions/*{id-abonnement}*/resourceGroups/*{nom-groupe-ressources}*/providers/Microsoft.Compute/virtualMachineScaleSets/*{nom-machine-virtuelle}*
* **Machines virtuelles** - /subscriptions/*{id-abonnement}*/resourceGroups/*{nom-groupe-ressources}*/providers/Microsoft.Compute/virtualMachines/*{nom-machine-virtuelle}*
* **Event Hubs** - /subscriptions/*{id-abonnement}*/resourceGroups/*{nom-groupe-ressources}*/providers/Microsoft.EventHub/namespaces/*{espace-noms-eventhub}*

Il existe des approches alternatives à la récupération de l’ID de ressource, notamment avec l’Explorateur de ressources Azure, l’affichage de la ressource souhaitée dans le portail Azure et via PowerShell ou l’interface CLI Azure.

### <a name="azure-resource-explorer"></a>Azure Resource Explorer
Pour rechercher l’ID de ressource pour une ressource de votre choix, une approche utile consiste à utiliser l’outil [Explorateur de ressources Azure](https://resources.azure.com) . Naviguez vers la ressource souhaitée et observez l’ID indiqué, comme dans la capture d’écran suivante :

![Alt Azure Resource Explorer](./media/monitoring-rest-api-walkthrough/azure_resource_explorer.png)

### <a name="azure-portal"></a>Portail Azure
L’ID de ressource peut également être obtenu à partir du portail Azure. Pour ce faire, accédez à la ressource souhaitée, puis sélectionnez Propriétés. L’ID de ressource s’affiche dans le panneau Propriétés, comme illustré dans la capture d’écran suivante :

![Alt « ID de ressource affiché dans le panneau Propriétés du portail Azure »](./media/monitoring-rest-api-walkthrough/resourceid_azure_portal.png)

### <a name="azure-powershell"></a>Azure PowerShell
L’ID de ressource peut également être récupéré à l’aide des applets de commande d’Azure PowerShell. Par exemple, pour obtenir l’ID de ressource pour une application web Azure, exécutez l’applet de commande Get-AzureRmWebApp, comme dans la capture d’écran suivante :

![Alt ID ressource obtenu via PowerShell](./media/monitoring-rest-api-walkthrough/resourceid_powershell.png)

### <a name="azure-cli"></a>Interface de ligne de commande Azure
Pour récupérer l’ID de ressource à l’aide de l’interface CLI Azure, exécutez la commande « azure webapp show », en spécifiant l’option -json option, comme illustré dans la capture d’écran suivante :

![Alt ID ressource obtenu via PowerShell](./media/monitoring-rest-api-walkthrough/resourceid_azurecli.png)

## <a name="retrieve-activity-log-data"></a>Récupérer des données du journal d’activité
Outre l’utilisation des définitions de mesure et des valeurs associées, il est également possible de récupérer des informations supplémentaires intéressantes relatives aux ressources Azure. Par exemple, il est possible d’obtenir les données du [journal d’activité](https://msdn.microsoft.com/library/azure/dn931934.aspx) par requête. L’exemple suivant montre comment utiliser des API REST Azure Monitor pour demander des données de journal d’activité au sein d’une plage de dates spécifique pour un abonnement Azure :

```PowerShell
$apiVersion = "2014-04-01"
$filter = "eventTimestamp ge '2016-09-23' and eventTimestamp le '2016-09-24'and eventChannels eq 'Admin, Operation'"
$request = "https://management.azure.com/subscriptions/${subscriptionId}/providers/microsoft.insights/eventtypes/management/values?api-version=${apiVersion}&`$filter=${filter}"
(Invoke-RestMethod -Uri $request `
                   -Headers $authHeader `
                   -Method Get `
                   -Verbose).Value | ConvertTo-Json
```

## <a name="next-steps"></a>Étapes suivantes
* Consultez la [Vue d’ensemble de l’analyse](monitoring-overview.md).
* Affichez les [Mesures prises en charge avec Azure Monitor](monitoring-supported-metrics.md).
* Consultez la [Référence de l’API REST Microsoft Azure Monitor](https://msdn.microsoft.com/library/azure/dn931943.aspx).
* Consultez la [Bibliothèque de gestion Azure](https://msdn.microsoft.com/library/azure/mt417623.aspx).

