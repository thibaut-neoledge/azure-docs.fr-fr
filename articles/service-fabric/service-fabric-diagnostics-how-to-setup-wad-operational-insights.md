<properties
   pageTitle="Comment collecter des journaux avec WAD et Operational Insights | Microsoft Azure"
   description="Cet article décrit la procédure de configuration de Microsoft Azure Diagnostics et d’Operational Insights pour la collecte de journaux d’un cluster Service Fabric exécuté dans Azure"
   services="service-fabric"
   documentationCenter=".net"
   authors="kunaldsingh"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="10/20/2015"
   ms.author="kunalds"/>


# Collecte de journaux d’un cluster Service Fabric dans Azure à l’aide de WAD (Microsoft Azure Diagnostics) et d’Operational Insights

Lorsque vous exécutez un cluster Service Fabric dans Azure, il peut être intéressant de collecter les journaux de tous les nœuds pour les regrouper dans un emplacement central. La centralisation des journaux facilite l’analyse et la résolution des problèmes que vous pourriez rencontrer dans votre cluster ou dans les applications et services exécutés dans ce cluster. L’une des façons de charger et collecter les journaux consiste à utiliser l’extension WAD (Microsoft Azure Diagnostics), qui charge les journaux dans une table de stockage Azure. Operational Insights (composant de Microsoft Operations Management Suite) est une solution SaaS qui facilite l’analyse et la recherche de journaux. Les étapes ci-dessous vous expliquent comment configurer WAD sur les machines virtuelles du cluster afin de charger les journaux vers un référentiel central, puis comment configurer Operational Insights pour extraire les journaux et les rendre consultables depuis le portail Operational Insights. Operational Insights identifie les sources des différents types de journaux chargés à partir d’un cluster Service Fabric selon les noms des tables de stockage Azure dans lesquelles ils sont stockés. Autrement dit, vous devez configurer WAD pour qu’il charge les journaux dans les tables de stockage Azure dont les noms correspondent à ce que recherche Operational Insights. Les exemples de paramètres de configuration donnés dans ce document vous montrent à quoi devraient ressembler les noms des tables de stockage.

## Lecture recommandée
* [Microsoft Azure Diagnostics](../cloud-services/cloud-services-dotnet-diagnostics.md) (page en lien avec les services cloud, mais qui contient des informations et des exemples pertinents)
* [Operational Insights](https://azure.microsoft.com/services/operational-insights/)
* [Azure Resource Manager](https://azure.microsoft.com/documentation/articles/resource-group-overview/)

## Composants requis
Les outils suivants sont utilisés pour exécuter certaines opérations décrites dans ce document : * [Azure Powershell](https://azure.microsoft.com/documentation/articles/powershell-install-configure/) * [Client ARM](https://github.com/projectkudu/ARMClient)

## Les différentes sources de journaux que vous pourriez souhaiter collecter
1. Journaux Service Fabric : émis par la plateforme vers les canaux ETW et EventSource standard. Ces journaux peuvent être de trois types :
  - Événements opérationnels : ces journaux couvrent les opérations exécutées par la plateforme Service Fabric, par exemple : la création d’applications et de services, les modifications d’état des nœuds et les informations de mise à niveau.
  - [Événements du modèle de programmation Actor](https://azure.microsoft.com/documentation/articles/service-fabric-reliable-actors-diagnostics/)
  - [Événements du modèle de programmation Reliable Services](https://azure.microsoft.com/documentation/articles/service-fabric-reliable-services-diagnostics/)
2. Événements d’application : ces événements sont émis à partir de votre code de services et écrits à l’aide de la classe d’assistance EventSource fournie dans les modèles Visual Studio. Pour plus d’informations sur la création de journaux à partir de votre application, consultez cet [article](https://azure.microsoft.com/documentation/articles/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally/).


## Déploiement de WAD (Microsoft Azure Diagnostics) sur un cluster Service Fabric pour la collecte et le chargement de journaux
La première étape de la collecte de journaux consiste à déployer l’extension WAD sur chaque machine virtuelle du cluster Service Fabric. WAD collecte les journaux sur chaque machine virtuelle et les charge sur le compte de stockage que vous spécifiez. La procédure varie légèrement selon que vous utilisiez le portail ou ARM et que vous effectuiez le déploiement pour un nouveau cluster ou pour un cluster déjà existant. Examinons les différentes étapes pour chaque scénario.

### Déploiement de WAD dans le cadre de la création d’un cluster via le portail
Pour déployer WAD sur les machines virtuelles du cluster dans le cadre de la création d’un cluster, on utile le paramètre de diagnostic illustré sur l’image ci-dessous. Ce paramètre est activé par défaut. ![Paramètre WAD dans le portail pour la création d’un cluster](./media/service-fabric-diagnostics-how-to-setup-wad-operational-insights/portal-cluster-creation-diagnostics-setting.png)

### Déploiement de WAD dans le cadre de la création d’un cluster à l’aide d’ARM
Pour créer un cluster à l’aide d’ARM, vous devez au préalable ajouter le fichier JSON de configuration WAD au modèle ARM du cluster complet. Nous vous proposons un exemple de modèle ARM d’un cluster à 5 machines virtuelles auquel a été ajoutée la configuration WAD. Ce modèle est visible à l’emplacement suivant de la galerie d’exemples Azure : [Exemple de modèle ARM avec un cluster à cinq nœuds et une configuration WAD](https://github.com/Azure/azure-quickstart-templates/tree/master/service-fabric-cluster-5-node-1-nodetype-wad).

Pour visualiser le paramètre WAD dans le modèle ARM, recherchez « WadCfg ». Pour créer un cluster à partir de ce modèle, cliquez simplement sur le bouton « Déployer sur Azure » disponible via le lien ci-dessus. Vous pouvez également télécharger l’exemple ARM, y apporter des modifications et créer un cluster à partir du modèle modifié en utilisant la commande `New-AzureResourceGroupDeployment` dans une fenêtre Azure Powershell. Voir ci-dessous les paramètres que vous devrez configurer dans cette commande. En outre, avant d’appeler cette commande de déploiement, vous devrez peut-être configurer d’autres paramètres, notamment ajouter votre compte Azure (`Add-AzureAccount`), choisir un abonnement (`Select-AzureSubscription`), basculer en mode ARM (`Switch-AzureMode AzureResourceManager`) et créer le groupe de ressources si vous ne l’avez pas encore fait (`New-AzureResourceGroup`).

```powershell
New-AzureResourceGroupDeployment -ResourceGroupName $resourceGroupName -Name $deploymentName -TemplateFile $pathToARMConfigJsonFile -TemplateParameterFile $pathToParameterFile –Verbose
```

### <a name="deploywadarm"></a>Déploiement de WAD sur un cluster existant
Si vous disposez déjà d’un cluster sur lequel WAD n’a pas été déployé, vous pouvez lui ajouter la configuration WAD en procédant de la manière suivante. Créez deux fichiers WadConfigUpdate.json et WadConfigUpdateParams.json avec le JSON ci-dessous.

##### WadConfigUpdate.json
```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",

  "parameters": {
        "vmNamePrefix": {
      "type": "string"
    },
        "applicationDiagnosticsStorageAccountName": {
      "type": "string"
    },
        "vmCount": {
            "type": "int"
    }
  },

  "resources": [
    {
      "apiVersion": "2015-05-01-preview",
      "type": "Microsoft.Compute/virtualMachines/extensions",
            "name": "[concat(parameters('vmNamePrefix'),copyIndex(0),'/Microsoft.Insights.VMDiagnosticsSettings')]",
            "location": "[resourceGroup().location]",
      "properties": {
        "type": "IaaSDiagnostics",
                "typeHandlerVersion": "1.5",
        "publisher": "Microsoft.Azure.Diagnostics",
                "autoUpgradeMinorVersion": true,
        "settings": {
                    "WadCfg": {
            "DiagnosticMonitorConfiguration": {
                            "overallQuotaInMB": "50000",
                "EtwProviders": {
                    "EtwEventSourceProviderConfiguration": [
                        {
                            "provider": "Microsoft-ServiceFabric-Actors",
                            "scheduledTransferKeywordFilter": "1",
                                        "scheduledTransferPeriod": "PT5M",
                            "DefaultEvents": { "eventDestination": "ServiceFabricReliableActorEventTable" }
                        },
                        {
                            "provider": "Microsoft-ServiceFabric-Services",
                            "DefaultEvents": { "eventDestination": "ServiceFabricReliableServiceEventTable" },
                                        "scheduledTransferPeriod": "PT5M"
                        }
                    ],
                    "EtwManifestProviderConfiguration": [
                        {
                            "provider": "cbd93bc2-71e5-4566-b3a7-595d8eeca6e8",
                            "scheduledTransferKeywordFilter": "4611686018427387904",
                                        "scheduledTransferLogLevelFilter": "Information",
                                        "scheduledTransferPeriod": "PT5M",
                            "DefaultEvents": { "eventDestination": "ServiceFabricSystemEventTable" }
                        }
                    ]
                }
            }
    },
                    "StorageAccount": "[parameters('applicationDiagnosticsStorageAccountNamee')]"
                },
                "protectedSettings": {
                    "storageAccountName": "[parameters('applicationDiagnosticsStorageAccountNamee')]",
                    "storageAccountKey": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('applicationDiagnosticsStorageAccountName')),'2015-05-01-preview').key1]",
                    "storageAccountEndPoint": "https://core.windows.net/"
                }
            },
            "copy": {
                "name": "vmExtensionLoop",
                "count": "[parameters('vmCount')]"
            }
        }
    ],

    "outputs": {
    }
}
```

##### WadConfigUpdateParams.json
Remplacez le vmNamePrefix par le préfixe que vous avez choisi pour les noms de machine virtuelle lors de la création de votre cluster, puis modifiez le paramètre vmStorageAccountName pour le définir comme compte de stockage dans lequel vous charger les journaux à partir des machines virtuelles. ```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmNamePrefix": {
            "value": "VM"
        },
        "applicationDiagnosticsStorageAccountName": {
            "value": "testdiagacc"
        },
        "vmCount": {
            "value": 5
        }
    }
}
``` Dès que vous avez créé les fichiers json mentionnés ci-dessus et que vous les avez modifiés compte tenu des caractéristiques de votre environnement, exécutez cette commande en spécifiant le nom du groupe de ressources de votre cluster Service Fabric. Une fois la commande correctement exécutée, WAD sera déployé sur toutes les machines virtuelles et commencera à charger les journaux du cluster vers les tables du compte de stockage Azure spécifié. En outre, avant d’appeler cette commande de déploiement, vous devrez peut-être configurer d’autres paramètres, notamment ajouter votre compte Azure (`Add-AzureAccount`), choisir l’abonnement adéquat (`Select-AzureSubscription`) et basculer en mode ARM (`Switch-AzureMode AzureResourceManager`). ```powershell
New-AzureResourceGroupDeployment -ResourceGroupName $resourceGroupName -Name $deploymentName -TemplateFile $pathToWADConfigJsonFile -TemplateParameterFile $pathToParameterFile –Verbose
```

## Configuration d’Operational Insights pour l’affichage et l’analyse des journaux de cluster
Lorsque WAD est configuré sur le cluster et charge des journaux sur un compte de stockage, l’étape suivante consiste à configurer Operational Insights pour que vous puissiez afficher, rechercher et interroger tous les journaux du cluster via l’interface utilisateur du portail d’Operational Insights.

### Création d’un espace de travail Operational Insights
Pour connaître les étapes de création d’un espace de travail Operational Insights, voir l’article ci-dessous. Notez que cet article décrit deux façons de créer un espace de travail ; choisissez plutôt la méthode qui utilise le portail Azure et l’approche par abonnement. Dans les sections suivantes de ce document, vous aurez besoin du nom de l’espace de travail Operational Insights. Créez l’espace de travail Operational Insights en utilisant le même abonnement que celui que vous avez utilisé pour créer toutes les ressources de cluster, y compris les comptes de stockage.

[Intégration d’Operational Insights](https://technet.microsoft.com/library/mt484118.aspx) (en anglais)

### Configuration de l’espace de travail Operational Insights pour l’affichage des journaux du cluster
Une fois que vous avez créé l’espace de travail Operational Insights comme décrit ci-dessus, l’étape suivante consiste à configurer cet espace de travail afin d’extraire les journaux des tables Azure du cluster à partir desquelles ils seront chargés par Microsoft Azure Diagnostics. Cette configuration n’est pas disponible actuellement via le portail Operational Insights et ne peut être effectuée qu’à l’aide des commandes Powershell. Exécutez ce script PS. ```powershell <# Ce script configurera un espace de travail Operations Management Suite (également appelé espace de travail Operational Insights) pour lire Microsoft Azure Diagnostics à partir d’un compte de stockage Azure.

    It will enable all supported data types (currently Windows Event Logs, Syslog, Service Fabric Events, ETW Events and IIS Logs).

    It supports both classic and ARM storage accounts.

    If you have more than one OMS workspace you will be prompted for the workspace to configure.

    If you have more than one storage account you will be prompted for which storage account to configure.
#>
Add-AzureAccount

Switch-AzureMode -Name AzureResourceManager

$validTables = "WADServiceFabric*EventTable", "WADETWEventTable"

function Select-Workspace {

    $workspace = ""

    $allWorkspaces = Get-AzureOperationalInsightsWorkspace  

    switch ($allWorkspaces.Count) {
        0 {Write-Error "No Operations Management Suite workspaces found"}
        1 {return $allWorkspaces}
        default {
            $uiPrompt = "Enter the number corresponding to the workspace you want to configure.`n"

            $count = 1
            foreach ($workspace in $allWorkspaces) {
                $uiPrompt += "$count. " + $workspace.Name + " (" + $workspace.CustomerId + ")`n"
                $count++
            }
            $answer = (Read-Host -Prompt $uiPrompt) - 1
            $workspace = $allWorkspaces[$answer]
        }  
    }
    return $workspace
}

function Select-StorageAccount {

    $storage = ""

    $allStorageAccounts = (get-azureresource -ResourceType Microsoft.ClassicStorage/storageAccounts) + (get-azureresource -ResourceType Microsoft.Storage/storageAccounts)

    switch ($allStorageAccounts.Count) {
        0 {Write-Error "No storage accounts found"}
        1 {$storage = $allStorageAccounts}
        default {

            $uiPrompt = "Enter the number corresponding to the storage account with diagnostics.`n"

            $count = 1
            foreach ($storageAccount in $allStorageAccounts) {
                $uiPrompt += "$count. " + $storageAccount.Name + " (" + $storageAccount.Location + ")`n"
                $count++
            }
            $answer = (Read-Host -Prompt $uiPrompt)

            $storage = $allStorageAccounts[$answer - 1]
        }
    }
    return $storage
}

$workspace = Select-Workspace $storageAccount = Select-StorageAccount

$insightsName = $storageAccount.Name + $workspace.Name

$existingConfig = ""

try { $existingConfig = Get-AzureOperationalInsightsStorageInsight -Workspace $workspace -Name $insightsName -ErrorAction Stop } catch [Hyak.Common.CloudException] { # HTTP introuvable si le stockage insight est inexistant }

if ($existingConfig) { Set-AzureOperationalInsightsStorageInsight -Workspace $workspace -Name $insightsName -Tables $validTables -Containers $validContainers

} else { if ($storageAccount.ResourceType -eq "Microsoft.ClassicStorage/storageAccounts") { Switch-AzureMode -Name AzureServiceManagement $key = (Get-AzureStorageKey -StorageAccountName $storageAccount.Name).Primary Switch-AzureMode -Name AzureResourceManager } else { $key = (Get-AzureStorageAccountKey -ResourceGroupName $storageAccount.ResourceGroupName -Name $storageAccount.Name).Key1 } New-AzureOperationalInsightsStorageInsight -Workspace $workspace -Name $insightsName -StorageAccountResourceId $storageAccount.ResourceId -StorageAccountKey $key -Tables $validTables -Containers $validContainers } ``` Une fois que vous avez configuré l’espace de travail Operational Insights pour lire les journaux à partir des tables Azure de votre compte de stockage, vous devez vous connecter au portail Azure et rechercher l’onglet Storage de la ressource Operational Insights. Vous devriez obtenir le résultat suivant : ![Configuration du stockage Operational Insights dans le portail Azure](./media/service-fabric-diagnostics-how-to-setup-wad-operational-insights/oi-connected-tables-list.png)

### Recherche et affichage des journaux dans Operational Insights
Après avoir configuré votre espace de travail Operational Insights pour lire les journaux à partir du compte de stockage spécifié, vous devrez peut-être attendre 10 minutes pour que les journaux s’affichent dans l’interface utilisateur Operational Insights. Pour avoir la certitude que de nouveaux journaux sont bien générés, vous devez déployer une application Service Fabric sur votre cluster de manière à générer des événements opérationnels à partir de la plateforme Service Fabric.

1. Pour afficher les journaux, sélectionnez LogSearch sur la page principale du portail Operational Insights. ![Option de recherche de journaux Operational Insights](./media/service-fabric-diagnostics-how-to-setup-wad-operational-insights/log-search-button-oi.png)

2. Sur la page de recherche de journaux, utilisez la requête « Type=ServiceFabricOperationalEvent » pour afficher les journaux opérationnels de votre cluster, comme illustré ci-dessous. Pour afficher tous les journaux du modèle de programmation Actor, vous devez lancer une requête « Type=ServiceFabricReliableActorEvent ». Pour afficher tous les journaux du modèle de programmation Reliable Services, entrez « Type=ServiceFabricReliableServiceEvent » ![Requête et affichage des journaux Operational Insights](./media/service-fabric-diagnostics-how-to-setup-wad-operational-insights/view-logs-oi.png)

### Exemples de requêtes facilitant la résolution des problèmes
Voici quelques exemples de scénarios, accompagnés des requêtes que vous pouvez utiliser pour les résoudre.

1. Pour déterminer si la commande RunAsync a été appelée pour un service spécifique par Service Fabric. Cela peut être intéressant si vous avez besoin de vérifier si un service se bloque lors du démarrage. Pour effectuer cette recherche à l’aide d’une requête semblable à celle indiquée ci-dessous, remplacez le nom du service et de l’application de manière à ce qu’il corresponde à ce que vous avez déployé, puis vérifiez si vous obtenez ou non des résultats.

    ```
    Type=ServiceFabricReliableServiceEvent AND ServiceName="fabric:/Application2/Stateless1" AND "RunAsync has been invoked"
    ```

2. Si vous exécutez un service avec état et que vous souhaitez voir si des exceptions ont été levées et marquées comme des échecs par Service Fabric, vous devrez utiliser une requête similaire à ce qui suit pour déterminer l’existence de ces événements.

    ```
    Type=ServiceFabricReliableServiceEvent AND ServiceName="fabric:/Application2/Stateful1" AND TaskName=StatefulRunAsyncFailure
    ```

3. Pour rechercher des événements correspondant aux exceptions levées par les méthodes Actor dans tous les services et applications déployés, utilisez une requête de ce type.

    ```
    Type=ServiceFabricReliableActorEvent AND TaskName=ActorMethodThrewException
    ```

## Mise à jour de Microsoft Azure Diagnostics pour collecter et charger des journaux depuis de nouveaux canaux EventSource
Pour mettre à jour WAD de manière à collecter des journaux à partir de nouveaux canaux EventSource représentant une nouvelle application que vous allez déployer, vous devez simplement exécuter les mêmes étapes que celles décrites dans la [section ci-dessus](#deploywadarm) relative à la configuration de WAD pour un cluster existant. Vous devrez mettre à jour la section EtwEventSourceProviderConfiguration dans le fichier WadConfigUpdate.json pour ajouter des entrées pour les nouveaux EventSources avant d’appliquer la mise à jour de la configuration via la commande ARM. La table de chargement sera la même (ETWEventTable), puisqu’il s’agit de la table qui est configurée pour lire les événements ETW depuis Operational Insights.


## Étapes suivantes
Vérifiez les événements de diagnostic émis pour [Reliable Actors](service-fabric-reliable-actors-diagnostics.md) et [Reliable Services](service-fabric-reliable-services-diagnostics.md) pour comprendre plus en détail les événements auxquels vous devriez être attentif lors de la résolution des problèmes.

<!---HONumber=Nov15_HO4-->