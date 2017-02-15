---
title: Ressources Automation dans des solutions OMS | Microsoft Docs
description: "Les solutions dans OMS comprennent généralement des runbooks dans Azure Automation pour automatiser des processus tels que la collecte, le traitement et la surveillance des données.  Cet article explique comment inclure des runbooks et leurs ressources associées dans une solution."
services: operations-management-suite
documentationcenter: 
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: 5281462e-f480-4e5e-9c19-022f36dce76d
ms.service: operations-management-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/19/2016
ms.author: bwren
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: e115fd3b5f29dbcbfd9f85ebb215d950539cc1e3


---
# <a name="automation-resources-in-oms-solutions-preview"></a>Ressources Automation dans des solutions OMS (préversion)
> [!NOTE]
> Il s’agit d’une documentation préliminaire pour la création de solutions de gestion dans OMS qui sont actuellement en préversion. Tout schéma décrit ci-dessous est susceptible d’être modifié.   
> 
> 

[Les solutions de gestion dans OMS](operations-management-suite-solutions.md) comprennent généralement des runbooks dans Azure Automation pour automatiser des processus tels que la collecte, le traitement et la surveillance des données.  En plus des runbooks, les comptes Automation incluent des ressources telles que des variables et des planifications qui prennent en charge les runbooks utilisés dans la solution.  Cet article explique comment inclure des runbooks et leurs ressources associées dans une solution.

> [!NOTE]
> Les exemples dans cet article utilisent des paramètres et des variables qui sont nécessaires ou courants pour les solutions de gestion. Ils sont décrits dans la rubrique [Création de solutions de gestion dans Operations Management Suite (OMS)](operations-management-suite-solutions-creating.md) 
> 
> 

## <a name="prerequisites"></a>Conditions préalables
Cet article suppose que vous êtes déjà familiarisé avec la [création d’une solution de gestion](operations-management-suite-solutions-creating.md) et la structure d’un fichier solution.

## <a name="samples"></a>Exemples
Vous pouvez obtenir des exemples de modèles de gestionnaire de ressources pour les ressources Automation à partir des [modèles Resource Manager dans GitHub](https://github.com/azureautomation/automation-packs/tree/master/101-sample-automation-resource-templates).

## <a name="automation-account"></a>Compte Automation
Toutes les ressources dans Azure Automation sont contenues dans un [compte Automation](../automation/automation-security-overview.md#automation-account-overview).  Comme décrit dans [Espace de travail OMS et compte Automation](operations-management-suite-solutions-creating.md#oms-workspace-and-automation-account), le compte Automation n’est pas inclus dans la solution de gestion mais doit exister avant l’installation de la solution.  Si ce n’est pas le cas, l’installation de la solution échoue.

Le nom du compte Automation se trouve dans le nom de chaque ressource Automation.  Pour cela, utilisez le paramètre **accountName** comme dans l’exemple suivant d’une ressource de runbook.

    "name": "[concat(parameters('accountName'), '/MyRunbook'))]"


## <a name="runbooks"></a>Runbooks
Les ressources [Azure Automation runbook](../automation/automation-runbook-types.md) sont de type **Microsoft.Automation/automationAccounts/runbooks** et ont les propriétés du tableau suivant.

| Propriété | Description |
|:--- |:--- |
| runbookType |Spécifie les types de runbook. <br><br> Script : script PowerShell <br>PowerShell : workflow PowerShell <br> GraphPowerShell : runbook de script PowerShell graphique <br> GraphPowerShellWorkflow : runbook de workflow PowerShell graphique |
| logProgress |Spécifie si les [enregistrements de progression](../automation/automation-runbook-output-and-messages.md) doivent être générés pour le runbook. |
| logVerbose |Spécifie si les [enregistrements détaillés](../automation/automation-runbook-output-and-messages.md) doivent être générés pour le runbook. |
| Description |Description facultative du runbook. |
| publishContentLink |Spécifie le contenu du runbook. <br><br>uri : Uri du contenu du runbook.  Il s’agit d’un fichier .ps1 pour des runbooks PowerShell et Script et d’un fichier de runbook graphique exporté pour un runbook Graph.  <br> version : version du runbook pour votre propre suivi. |

Vous trouverez ci-dessous un exemple de ressource de runbook.  Dans ce cas, il récupère le runbook à partir de la [PowerShell Gallery](https://www.powershellgallery.com).

    "name": "[concat(parameters('accountName'), '/Start-AzureV2VMs'))]",
    "type": "Microsoft.Automation/automationAccounts/runbooks",
    "apiVersion": "[variables('AutomationApiVersion')]",
    "location": "[parameters('regionId')]",
    "dependsOn": [
        "[concat('Microsoft.Automation/automationAccounts/', parameters('accountName'), '/runbooks/', variables('startVmsRunbookName'))]"
    ],
    "tags": { },
    "properties": {
        "runbookType": "PowerShell",
        "logProgress": "true",
        "logVerbose": "true",
        "description": "",
        "publishContentLink": {
            "uri": "https://www.powershellgallery.com/api/v2/items/psscript/package/Update-ModulesInAutomationToLatestVersion/1.0/Start-AzureV2VMs.ps1",
            "version": "1.0"
        }
    }

### <a name="starting-a-runbook"></a>Démarrage d'un runbook
Il existe deux méthodes pour démarrer un runbook dans une solution de gestion.

* Pour démarrer le runbook lorsque la solution est installée, créez une [ressources de tâche](#automation-jobs) comme décrit ci-dessous.
* Pour démarrer le runbook selon une planification, créez une [ressource de planification](#schedules) comme décrit ci-dessous. 

## <a name="automation-jobs"></a>Tâches Automation
Pour démarrer un runbook lorsque la solution de gestion est installée, vous créez une ressource de **tâche**.  Les ressources de tâche sont de type **Microsoft.Automation/automationAccounts/jobs** et ont les propriétés du tableau suivant.

| Propriété | Description |
|:--- |:--- |
| runbook |Entité **name** unique portant le nom du runbook. |
| parameters |Entité pour chaque paramètre exigé par le runbook. |

La tâche comprend le nom du runbook et toute valeur de paramètre à lui envoyer.  La tâche doit [dépendre](operations-management-suite-solutions-creating.md#resources) du runbook qu’elle démarre, car le runbook doit être créé avant la tâche.  Vous créez également des dépendances envers d’autres tâches pour des runbooks qui doivent être terminés avant le runbook actuel.

Le nom d’une ressource de tâche doit contenir un GUID, qui est généralement affecté par un paramètre.  Vous trouverez plus d’informations sur les paramètres GUID dans la rubrique [Création de solutions dans Operations Management Suite (OMS)](operations-management-suite-solutions-creating.md#parameters).  

Voici un exemple de ressource de tâche qui démarre un runbook lorsque la solution de gestion est installée.  Un autre runbook doit être terminé avant que celui-ci ne démarre. Il dépend donc de tâches pour ce runbook.  Les détails de la tâche sont fournis par le biais de paramètres et de variables et ne sont pas spécifiés directement.

    {
        "name": "[concat(parameters('accountName'), '/', parameters('startVmsRunbookGuid'))]",
        "type": "Microsoft.Automation/automationAccounts/jobs",
        "apiVersion": "[variables('AutomationApiVersion')]",
        "location": "[parameters('regionId')]",
        "dependsOn": [
            "[concat('Microsoft.Automation/automationAccounts/', parameters('accountName'), '/runbooks/', variables('startVmsRunbookName'))]",
            "[concat('Microsoft.Automation/automationAccounts/', parameters('accountName'), '/jobs/', parameters('initialPrepRunbookGuid'))]"
        ],
        "tags": { },
        "properties": {
            "runbook": {
                "name": "[variables('startVmsRunbookName')]"
            },
            "parameters": {
                "AzureConnectionAssetName": "[variables('AzureConnectionAssetName')]",
                "ResourceGroupName": "[variables('ResourceGroupName')]"
            }
        }
    }


## <a name="certificates"></a>Certificats
Les [certificats Azure Automation](../automation/automation-certificates.md) sont de type **Microsoft.Automation/automationAccounts/certificate** et ont les propriétés du tableau suivant.

| Propriété | Description |
|:--- |:--- |
| base64Value |Valeur de base 64 pour le certificat. |
| thumbprint |Thumbprint pour le certificat. |

Vous trouverez ci-dessous un exemple de ressource de certificat.

    "name": "[concat(parameters('accountName'), '/MyCertificate')]",
    "type": "certificates",
    "apiVersion": "2015-01-01-preview",
    "location": "[parameters('regionId')]",
    "tags": {},
    "dependsOn": [
    ],
    "properties": {
        "base64Value": "MIIC1jCCAA8gAwIAVgIYJY4wXCXH/YAHMtALh7qEFzANAgkqhkiG5w0AAQUFGDAUMRIwEBYDVQQDEwlsA3NhAGevc3QqHhcNMTZwNjI5MHQxMjAsWhcNOjEwNjI5NKWwMDAwWjAURIwEAYDVQQBEwlsA2NhAGhvc3QwggEiMA0GCSqGSIA3DQEAAQUAA4IADwAwggEKAoIAAQDIyzv2A0RUg1/AAryI9W1DGAHAqqGdlFfTkUSDfv+hEZTAwKv0p8daqY6GroT8Du7ctQmrxJsy8JxIpDWxUaWwXtvv1kR9eG9Vs5dw8gqhjtOwgXvkOcFdKdQwA82PkcXoHlo+NlAiiPPgmHSELGvcL1uOgl3v+UFiiD1ro4qYqR0ITNhSlq5v2QJIPnka8FshFyPHhVtjtKfQkc9G/xDePW8dHwAhfi8VYRmVMmJAEOLCAJzRjnsgAfznP8CZ/QUczPF8LuTZ/WA/RaK1/Arj6VAo1VwHFY4AZXAolz7xs2sTuHplVO7FL8X58UvF7nlxq48W1Vu0l8oDi2HjvAgMAAAGjJDAiMAsGA1UdDwREAwIEsDATAgNVHSUEDDAKAggrAgEFNQcDATANAgkqhkiG9w0AAQUFAAOCAQEAk8ak2A5Ug4Iay2v0uXAk95qdAthJQN5qIVA13Qay8p4MG/S5+aXOVz4uMXGt18QjGds1A7Q8KDV4Slnwn95sVgA5EP7akvoGXhgAp8Dm90sac3+aSG4fo1V7Y/FYgAgpEy4C/5mKFD1ATeyyhy3PmF0+ZQRJ7aLDPAXioh98LrzMZr1ijzlAAKfJxzwZhpJamAwjZCYqiNZ54r4C4wA4QgX9sVfQKd5e/gQnUM8gTQIjQ8G2973jqxaVNw9lZnVKW3C8/QyLit20pNoqX2qQedwsqg3WCUcPRUUqZ4NpQeHL/AvKIrt158zAfU903yElAEm2Zr3oOUR4WfYQ==",
        "thumbprint": "F485CBE5569F7A5019CB68D7G6D987AC85124B4C"
    }

## <a name="credentials"></a>Informations d'identification
Les [informations d'identification Azure Automation](../automation/automation-credentials.md) sont de type **Microsoft.Automation/automationAccounts/credentials** et ont les propriétés du tableau suivant.

| Propriété | Description |
|:--- |:--- |
| userName |Nom d’utilisateur des informations d’identification. |
| password |Mot de passe des informations d’identification. |

Vous trouverez ci-dessous un exemple de ressource d’informations d’identification.

    "name": "[concat(parameters('accountName'), '/', variables('credentialName'))]",
    "type": "Microsoft.Automation/automationAccounts/runbooks/credentials",
    "apiVersion": "[variables('AutomationApiVersion')]",
    "location": "[parameters('regionId')]",
    "tags": { },
    "dependsOn": [
    ],
    "properties": {
        "userName": "User01",
        "password": "password"
    }


## <a name="schedules"></a>Planifications
Les [planifications Azure Automation](../automation/automation-schedules.md) sont de type **Microsoft.Automation/automationAccounts/schedules** et ont les propriétés du tableau suivant.

| Propriété | Description |
|:--- |:--- |
| Description |Description facultative de la planification. |
| startTime |Spécifie l’heure de début d’une planification en tant qu’objet DateTime. Une chaîne peut être fournie si elle peut être convertie en un élément DateTime valide. |
| isEnabled |Spécifie si la planification est activée. |
| interval |Le type d’intervalle pour la planification.<br><br>day<br>hour |
| frequency |Fréquence à laquelle la planification doit se déclencher en nombre de jours ou d’heures. |

Vous trouverez ci-dessous un exemple de ressource de planification.

    "name": "[concat(parameters('accountName'), '/', variables('variableName'))]",
    "type": "microsoft.automation/automationAccounts/schedules",
    "apiVersion": "[variables('AutomationApiVersion')]",
    "tags": { },
    "dependsOn": [
    ],
    "properties": {
        "description": "Schedule for StartByResourceGroup runbook",
        "startTime": "10/30/2016 12:00:00",
        "isEnabled": "true",
        "interval": "day",
        "frequency": "1"
    }


## <a name="variables"></a>Variables
Les [variables Azure Automation](../automation/automation-variables.md) sont de type **Microsoft.Automation/automationAccounts/variables** et ont les propriétés du tableau suivant.

| Propriété | Description |
|:--- |:--- |
| Description |Description facultative de la variable. |
| isEncrypted |Spécifie si la variable doit être chiffrée. |
| type |Type de données pour la variable. |
| value |Valeur de la variable. |

Vous trouverez ci-dessous un exemple de ressource de variable.

    "name": "[concat(parameters('accountName'), '/', variables('StartTargetResourceGroupsAssetName')) ]",
    "type": "microsoft.automation/automationAccounts/variables",
    "apiVersion": "[variables('AutomationApiVersion')]",
    "tags": { },
    "dependsOn": [
    ],
    "properties": {
        "description": "Description for the variable.",
        "isEncrypted": "true",
        "type": "string",
        "value": "'This is a string value.'"
    }


## <a name="modules"></a>Modules
Votre solution de gestion ne doit pas nécessairement définir de [modules globaux](../automation/automation-integration-modules.md) utilisés par vos runbooks, car ils sont toujours disponibles.  Vous devez inclure une ressource pour tout autre module utilisé par vos runbooks, et le runbook doit dépendre de la ressource de module pour garantir qu’elle est créée avant le runbook.

Les [modules d’intégration](../automation/automation-integration-modules.md) sont de type **Microsoft.Automation/automationAccounts/modules** et ont les propriétés du tableau suivant.

| Propriété | Description |
|:--- |:--- |
| contentLink |Spécifie le contenu du module. <br><br>uri : Uri du contenu du runbook.  Il s’agit d’un fichier .ps1 pour des runbooks PowerShell et Script et d’un fichier de runbook graphique exporté pour un runbook Graph.  <br> version : version du runbook pour votre propre suivi. |

Vous trouverez ci-dessous un exemple de ressource de module.

    {        
        "name": "[concat(parameters('accountName'), '/', variables('OMSIngestionModuleName'))]",
        "type": "Microsoft.Automation/automationAccounts/modules",
        "apiVersion": "[variables('AutomationApiVersion')]",
        "dependsOn": [
        ],
        "properties": {
            "contentLink": {
                "uri": "https://devopsgallerystorage.blob.core.windows.net/packages/omsingestionapi.1.3.0.nupkg"
            }
        }
    }

### <a name="updating-modules"></a>Mise à jour des modules
Si vous mettez à jour une solution de gestion qui inclut un runbook qui utilise une planification alors que la nouvelle version de votre solution comprend un nouveau module utilisé par ce runbook, le runbook peut utiliser l’ancienne version du module.  Vous devez inclure les runbooks suivants dans votre solution et créer une tâche à exécuter avant les autres runbooks.  Cela garantit que tous les modules sont mis à jour comme exigé avant que les runbooks ne soient chargés.

* [Update-ModulesinAutomationToLatestVersion](https://www.powershellgallery.com/packages/Update-ModulesInAutomationToLatestVersion/1.03/DisplayScript) vous assure que tous les modules utilisés par des runbooks de votre solution sont à la version la plus récente.  
* [ReRegisterAutomationSchedule-MS-Mgmt](https://www.powershellgallery.com/packages/ReRegisterAutomationSchedule-MS-Mgmt/1.0/DisplayScript) enregistre de nouveau toutes les ressources de planification pour vous assurer que les runbooks qui y sont liés utilisent les modules actuels.

Voici un exemple des éléments exigés pour une solution pour prendre en charge la mise à jour des modules.

    "parameters": {
        "ModuleImportGuid": {
            "type": "string",
            "metadata": {
                "control": "guid"
            }
        },
        "ReRegisterRunbookGuid": {
            "type": "string",
            "metadata": {
                "control": "guid"
            }
        }
    },

    "variables": {
        "ModuleImportRunbookName": "Update-ModulesInAutomationToLatestVersion",
        "ModuleImportRunbookUri": "https://www.powershellgallery.com/packages/Update-ModulesInAutomationToLatestVersion/1.03/Content/Update-ModulesInAutomationToLatestVersion.ps1",
        "ModuleImportRunbookDescription": "Imports modules and also updates all Azure dependent modules that are in the same Automation Account.",
        "ReRegisterSchedulesRunbookName": "Update-ModulesInAutomationToLatestVersion",
        "ReRegisterSchedulesRunbookUri": "https://www.powershellgallery.com/packages/ReRegisterAutomationSchedule-MS-Mgmt/1.0/Content/ReRegisterAutomationSchedule-MS-Mgmt.ps1",
        "ReRegisterSchedulesRunbookDescription": "Reregisters schedules to ensure that they use latest modules."
    },

    "resources": [
        {
            "name": "[concat(parameters('accountName'), '/', variables('ModuleImportRunbookName'))]",
            "type": "Microsoft.Automation/automationAccounts/runbooks",
            "apiVersion": "[variables('AutomationApiVersion')]",
            "dependsOn": [
            ],
            "location": "[parameters('regionId')]",
            "tags": { },
            "properties": {
                "runbookType": "PowerShell",
                "logProgress": "true",
                "logVerbose": "true",
                "description": "[variables('ModuleImportRunbookDescription')]",
                "publishContentLink": {
                    "uri": "[variables('ModuleImportRunbookUri')]",
                    "version": "1.0.0.0"
                }
            }
        },
        {
            "name": "[concat(parameters('accountName'), '/', parameters('ModuleImportGuid'))]",
            "type": "Microsoft.Automation/automationAccounts/jobs",
            "apiVersion": "[variables('AutomationApiVersion')]",
            "location": "[parameters('regionId')]",
            "dependsOn": [
                "[concat('Microsoft.Automation/automationAccounts/', parameters('accountName'), '/runbooks/', variables('ModuleImportRunbookName'))]"
            ],
            "tags": { },
            "properties": {
                "runbook": {
                    "name": "[variables('ModuleImportRunbookName')]"
                },
                "parameters": {
                    "ResourceGroupName": "[resourceGroup().name]",
                    "AutomationAccountName": "[parameters('accountName')]",
                    "NewModuleName": "AzureRM.Insights"
                }
            }
        },
        {
          "name": "[concat(parameters('accountName'), '/', variables('ReRegisterSchedulesRunbookName'))]",
          "type": "Microsoft.Automation/automationAccounts/runbooks",
          "apiVersion": "[variables('AutomationApiVersion')]",
          "dependsOn": [
          ],
          "location": "[parameters('regionId')]",
          "tags": { },
          "properties": {
            "runbookType": "PowerShell",
            "logProgress": "true",
            "logVerbose": "true",
            "description": "[variables('ReRegisterSchedulesRunbookDescription')]",
            "publishContentLink": {
              "uri": "[variables('ReRegisterSchedulesRunbookUri')]",
              "version": "1.0.0.0"
            }
          }
        },
        {
          "name": "[concat(parameters('accountName'), '/', parameters('reRegisterSchedulesGuid'))]",
          "type": "Microsoft.Automation/automationAccounts/jobs",
          "apiVersion": "[variables('AutomationApiVersion')]",
          "location": "[parameters('regionId')]",
          "dependsOn": [
            "[concat('Microsoft.Automation/automationAccounts/', parameters('accountName'), '/runbooks/', variables('ReRegisterSchedulesRunbookName'))]"
          ],
          "tags": { },
          "properties": {
            "runbook": {
              "name": "[variables('ReRegisterSchedulesRunbookName')]"
            },
            "parameters": {
              "targetSubscriptionId": "[subscription().subscriptionId]",
              "resourcegroup": "[resourceGroup().name]",
              "automationaccount": "[parameters('accountName')]"
            }
        }
    ]


## <a name="next-steps"></a>Étapes suivantes
* [Ajout d’une vue à votre solution](operations-management-suite-solutions-resources-views.md) pour visualiser les données collectées.




<!--HONumber=Nov16_HO3-->


