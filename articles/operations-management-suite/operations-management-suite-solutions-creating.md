---
title: "Création de solutions de gestion dans Operations Management Suite (OMS) | Microsoft Docs"
description: "Les solutions de gestion étendent les fonctionnalités OMS (Operations Management Suite) en fournissant des scénarios de gestion empaquetés que les clients peuvent ajouter à leur espace de travail OMS.  Cet article fournit des informations sur la manière dont vous pouvez créer des solutions de gestion à utiliser dans votre propre environnement ou à mettre à la disposition de vos clients."
services: operations-management-suite
documentationcenter: 
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: 1915e204-ba7e-431b-9718-9eb6b4213ad8
ms.service: operations-management-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/23/2017
ms.author: bwren
translationtype: Human Translation
ms.sourcegitcommit: fc8b76bf996060e226ac3f508a1ecffca6fc3c98
ms.openlocfilehash: caa2f96d452174ebb13c5cbf67737f20e2a2134d


---
# <a name="creating-management-solutions-in-operations-management-suite-oms-preview"></a>Création de solutions de gestion dans Operations Management Suite (OMS) (préversion)
> [!NOTE]
> Il s’agit d’une documentation préliminaire pour la création de solutions de gestion dans OMS qui sont actuellement en préversion. Tout schéma décrit ci-dessous est susceptible d’être modifié.  
>
>

Les solutions de gestion étendent les fonctionnalités OMS (Operations Management Suite) en fournissant des scénarios de gestion empaquetés que les clients peuvent ajouter à leur espace de travail OMS.  Cet article fournit des détails sur la création de vos propres solutions de gestion que vous pouvez utiliser dans votre propre environnement ou mettre à la disposition de vos clients par le biais de la communauté.

## <a name="planning-your-management-solution"></a>Planification de votre solution de gestion
Les solutions de gestion dans OMS incluent plusieurs ressources prenant en charge un scénario de gestion donné.  Lorsque vous planifiez votre solution, vous devez vous concentrer sur le scénario que vous tentez de mettre en place et sur toutes les ressources nécessaires pour le prendre en charge.  Chaque solution doit être autonome et définir chaque ressource requise, même si une ou plusieurs ressources sont également définies par d’autres solutions.  Lorsqu’une solution de gestion est installée, chaque ressource est créée, sauf si elle existe déjà, et vous pouvez définir ce qu’il advient des ressources lorsqu’une solution est supprimée.  

Par exemple, une solution de gestion peut inclure un [runbook Azure Automation](../automation/automation-intro.md) qui collecte des données dans le référentiel Log Analytics à l’aide d’une [planification](../automation/automation-schedules.md) et d’une [vue](../log-analytics/log-analytics-view-designer.md) qui fournit différentes visualisations des données collectées.  La même planification peut être utilisée par une autre solution.  En tant qu’auteur de la solution de gestion, vous définissez les trois ressources mais spécifiez que le runbook et la vue doivent être automatiquement supprimés en même temps que la solution.    Vous pouvez également définir la planification, mais en spécifiant qu’elle doit rester en place si la solution est supprimée, au cas où elle est en cours d’utilisation par l’autre solution.

## <a name="management-solution-files"></a>Fichiers de solution de gestion
Les solutions de gestion sont implémentées en tant que [modèles de gestion des ressources](../azure-resource-manager/resource-manager-template-walkthrough.md).  Avant de créer des solutions de gestion, apprenez à [créer un modèle](../azure-resource-manager/resource-group-authoring-templates.md).  Cet article fournit des détails uniques sur les modèles utilisés pour des solutions. Il indique également comment définir des ressources de solution courantes.

La structure de base d’un fichier solution de gestion est identique à un [modèle Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md#template-format) qui se présente comme suit.  Chacune des sections suivantes décrit les éléments de niveau supérieur et leur contenu dans une solution.  

    {
       "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion": "",
       "parameters": {  },
       "variables": {  },
       "resources": [  ],
       "outputs": {  }
    }

## <a name="parameters"></a>Paramètres
Les [Paramètres](../azure-resource-manager/resource-group-authoring-templates.md#parameters) sont des valeurs que l’utilisateur doit indiquer lorsqu’il installe la solution de gestion.  Il existe des paramètres standard compris dans toutes les solutions, et vous pouvez ajouter des paramètres supplémentaires en fonction des besoins spécifiques de votre solution.  La manière dont les utilisateurs fournissent des valeurs de paramètre lors de l’installation de la solution dépend du paramètre lui-même et de la façon dont la solution est installée.

Lorsqu’un utilisateur installe votre solution de gestion par le biais de la [Place de marché Azure](operations-management-suite-solutions.md#finding-and-installing-management-solutions) ou des [modèles de démarrage rapide Azure](operations-management-suite-solutions.md#finding-and-installing-management-solutions), il est invité à sélectionner un [espace de travail OMS et un compte Automation](operations-management-suite-solutions-creating.md#oms-workspace-and-automation-account).  Ils sont utilisés pour remplir les valeurs de chacun des paramètres standard.  L’utilisateur n’est pas invité à fournir directement des valeurs pour les paramètres standard, mais il est invité à fournir une valeur pour tout paramètre supplémentaire.

Lorsque l’utilisateur installe votre solution par le biais [d’une autre méthode](operations-management-suite-solutions.md#finding-and-installing-management-solutions), il doit fournir une valeur pour tous les paramètres standard et tous les paramètres supplémentaires.

Vous trouverez ci-dessous un exemple de paramètre.

    "Daily Start Time": {
        "type": "string",
        "metadata": {
            "description": "Enter time for starting VMs by resource group.",
            "control": "datetime",
            "category": "Schedule"
        }

Le tableau suivant décrit les attributs d’un paramètre.

| Attribut | Description |
|:--- |:--- |
| type |Type de données pour le paramètre. Le contrôle de saisie affiché pour l’utilisateur dépend du type de données.<br><br>Valeur booléenne : zone de liste déroulante<br>Chaîne : zone de texte<br>Entier : zone de texte<br>securestring : champ de mot de passe<br> |
| category |Catégorie du paramètre facultative.  Les paramètres de la même catégorie sont regroupés. |
| contrôle |Fonctionnalité supplémentaire pour les paramètres de type chaîne.<br><br>datetime : le contrôle Datetime est affiché.<br>guid : la valeur Guid est générée automatiquement et le paramètre n’est pas affiché. |
| Description |Description du paramètre facultative.  Affichée dans une bulle d’informations en regard du paramètre. |

### <a name="standard-parameters"></a>Paramètres standard
Le tableau suivant répertorie les paramètres standard pour toutes les solutions de gestion.  Ces valeurs sont remplies pour l’utilisateur, qui n’a donc pas à le faire lorsque la solution est installée à partir de la Place de marché Azure ou de modèles de démarrage rapide.  L’utilisateur doit fournir des valeurs pour ces paramètres si la solution est installée par le biais d’une autre méthode.

> [!NOTE]
> L’interface utilisateur dans la Place de marché Azure ou des modèles de démarrage rapide attend que les noms de paramètre soient renseignés dans la table.  Si vous utilisez des noms de paramètres différents, l’utilisateur sera invité à les renseigner et les valeurs ne seront pas automatiquement remplies.
>
>

| Paramètre | Type | Description |
|:--- |:--- |:--- |
| accountName |string |Nom de compte Azure Automation. |
| pricingTier |string |Niveau tarifaire de l’espace de travail Log Analytics et du compte Azure Automation. |
| regionId |string |Région du compte Azure Automation. |
| solutionName |string |Nom de la solution. |
| workspaceName |string |Nom de l’espace de travail Log Analytics. |
| workspaceRegionId |string |Région de l’espace de travail Log Analytics. |

### <a name="sample"></a>Exemple
Vous trouverez ci-dessous un exemple d’entité de paramètre pour une solution.  Cela inclut tous les paramètres standard et deux paramètres supplémentaires dans la même catégorie.

    "parameters": {
        "workspaceName": {
            "type": "string",
            "metadata": {
                "description": "A valid Log Analytics workspace name"
            }
        },
        "accountName": {
               "type": "string",
               "metadata": {
                   "description": "A valid Azure Automation account name"
               }
        },
        "workspaceRegionId": {
               "type": "string",
               "metadata": {
                   "description": "Region of the Log Analytics workspace"
            }
        },
        "regionId": {
            "type": "string",
            "metadata": {
                "description": "Region of the Azure Automation account"
            }
        },
        "pricingTier": {
            "type": "string",
            "metadata": {
                "description": "Pricing tier of both Log Analytics workspace and Azure Automation account"
            }
        },
        "jobIdGuid": {
        "type": "string",
            "metadata": {
                "description": "GUID for a runbook job",
                "control": "guid",
                "category": "Schedule"
            }
        },
        "startTime": {
            "type": "string",
            "metadata": {
                "description": "Time for starting the runbook.",
                "control": "datetime",
                "category": "Schedule"
            }
        }


Faites référence aux valeurs de paramètre dans d’autres éléments de la solution avec la syntaxe **parameters('nom du paramètre')**.  Par exemple, pour accéder au nom de l’espace de travail, utilisez **parameters('workspaceName')**.

## <a name="variables"></a>Variables
L’élément **Variables** inclut les valeurs que vous allez utiliser dans le reste de la solution de gestion.  Celles-ci ne sont pas exposées à l’utilisateur qui installe la solution.  Elles sont conçues pour fournir à l’auteur un emplacement unique où il peut gérer les valeurs qui peuvent être utilisées plusieurs fois dans la solution. Vous devez placer toutes les valeurs spécifiques à votre solution dans des variables, contrairement au codage en dur dans l’élément **resources**.  Cela rend le code plus lisible et vous permet de modifier facilement ces valeurs dans des versions ultérieures.

Voici un exemple d’élément **variables** avec des paramètres standard utilisés dans les solutions.

    "variables": {
        "SolutionVersion": "1.1",
        "SolutionPublisher": "Contoso",
        "SolutionName": "My Solution",
        "LogAnalyticsApiVersion": "2015-11-01-preview",
        "AutomationApiVersion": "2015-10-31"
    },

Faites référence aux valeurs de variable dans la solution avec la syntaxe **variables('nom de la variable')**.  Par exemple, pour accéder à la variable SolutionName, utilisez **variables('solutionName')**

## <a name="resources"></a>Ressources
L’élément **resources** définit les différentes ressources incluses dans votre solution de gestion.  Il s’agit de la partie la plus vaste et la plus complexe du modèle.  Vous définissez des ressources avec la structure suivante.  

    "resources": [
        {
            "name": "<name-of-the-resource>",            
            "apiVersion": "<api-version-of-resource>",
            "type": "<resource-provider-namespace/resource-type-name>",        
            "location": "<location-of-resource>",
            "tags": "<name-value-pairs-for-resource-tagging>",
            "comments": "<your-reference-notes>",
            "dependsOn": [
                "<array-of-related-resource-names>"
            ],
            "properties": "<unique-settings-for-the-resource>",
            "resources": [
                "<array-of-child-resources>"
            ]
        }
    ]

### <a name="dependencies"></a>Dépendances
L’élément **dependsOn** spécifie une [dépendance](../azure-resource-manager/resource-group-define-dependencies.md) vis-à-vis d’une autre ressource.  Lorsque la solution est installée, aucune ressource n’est créée tant que toutes ses dépendances n’ont pas été créées.  Par exemple, votre solution peut [démarrer un runbook](operations-management-suite-solutions-resources-automation.md#runbooks) lorsqu’il est installé à l’aide d’une [ressource de tâche](operations-management-suite-solutions-resources-automation.md#automation-jobs).  La ressource de tâche dépend de la ressource de runbook pour garantir que le runbook est créé avant la tâche.

### <a name="oms-workspace-and-automation-account"></a>Espace de travail OMS et compte Automation
Les solutions de gestion nécessitent un [espace de travail OMS](../log-analytics/log-analytics-manage-access.md) qui contient des vues et un [compte Automation](../automation/automation-security-overview.md#automation-account-overview) qui contient les Runbooks et les ressources associées.  Ces éléments doivent être disponibles avant que les ressources de la solution soient créées et ne doivent pas être définis dans la solution elle-même.  L’utilisateur [spécifiera un espace de travail et un compte](operations-management-suite-solutions.md#oms-workspace-and-automation-account) lors du déploiement de la solution, mais l’auteur doit tenir compte des points suivants.

## <a name="solution-resource"></a>Ressource de la solution
Chaque solution nécessite une entrée de ressource dans l’élément **resources** qui définit la solution elle-même.  Cette entrée est de type **Microsoft.OperationsManagement/solutions**.  Voici un exemple de ressource de solution.  Ses différents éléments sont décrits dans les sections ci-dessous.

    "name": "[concat(variables('SolutionName'), '[ ' ,parameters('workspacename'), ' ]')]",
    "location": "[parameters('workspaceRegionId')]",
    "tags": { },
    "type": "Microsoft.OperationsManagement/solutions",
    "apiVersion": "[variables('LogAnalyticsApiVersion')]",
    "dependsOn": [
        "[concat('Microsoft.Automation/automationAccounts/', parameters('accountName'), '/runbooks/', variables('RunbookName'))]",
        "[concat('Microsoft.Automation/automationAccounts/', parameters('accountName'), '/schedules/', variables('ScheduleName'))]",
        "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'), '/views/', variables('ViewName'))]"
    ]
    "properties": {
        "workspaceResourceId": "[concat(resourceGroup().id, '/providers/Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]",
        "referencedResources": [
            "[concat('Microsoft.Automation/automationAccounts/', parameters('accountName'), '/schedules/', variables('ScheduleName'))]"
        ],
        "containedResources": [
            "[concat('Microsoft.Automation/automationAccounts/', parameters('accountName'), '/runbooks/', variables('RunbookName'))]",
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'), '/views/', variables('ViewName'))]"
        ]
    },
    "plan": {
        "name": "[concat(variables('SolutionName'), '[' ,parameters('workspacename'), ']')]",
        "Version": "[variables('SolutionVersion')]",
        "product": "AzureSQLAnalyticSolution",
        "publisher": "[variables('SolutionPublisher')]",
        "promotionCode": ""
    }

### <a name="solution-name"></a>Nom de la solution
Le nom de la solution doit être unique dans votre abonnement Azure. La valeur recommandée à utiliser est la suivante.  Cet exemple utilise une variable appelée **SolutionName** comme nom de base et le paramètre **workspaceName** pour garantir que le nom est unique.

    [concat(variables('SolutionName'), ' [' ,parameters('workspaceName'), ']')]

Le nom pourrait ressembler à l’exemple suivant.

    My Solution Name [MyWorkspace]


### <a name="dependencies"></a>Dépendances
La ressource de la solution doit avoir une [dépendance](../azure-resource-manager/resource-group-define-dependencies.md) vis-à-vis de toute autre ressource de la solution, car ces ressources doivent exister avant que la solution ne puisse être créée.  Pour cela, ajoutez une entrée pour chaque ressource dans l’élément **dependsOn**.

### <a name="properties"></a>Propriétés
La ressource de solution possède les propriétés indiquées dans le tableau suivant.  Cela inclut les ressources référencées et contenues dans la solution qui définit la manière dont la ressource est gérée après l’installation de la solution.  Chaque ressource de la solution doit être répertoriée dans la propriété **referencedResources** ou **containedResources**.

| Propriété | Description |
|:--- |:--- |
| workspaceResourceId |ID de l’espace de travail OMS au format *<Resource Group ID>/providers/Microsoft.OperationalInsights/workspaces/\<Workspace Name\>*. |
| referencedResources |Liste des ressources de la solution qui ne doivent pas être supprimées en même temps que la solution. |
| containedResources |Liste des ressources de la solution qui doivent être supprimées en même temps que la solution. |

L’exemple ci-dessus est valable pour une solution avec un runbook, une planification et une vue.  La planification et le runbook sont *référencés* dans l’élément **properties** afin de ne pas être supprimés le cas échéant en même temps que la solution.  La vue est *contenue* afin qu’elle soit supprimée en même temps que la solution.

### <a name="plan"></a>Planification
L’entité **plan** de la ressource de solution possède les propriétés indiquées dans le tableau suivant.

| Propriété | Description |
|:--- |:--- |
| name |Nom de la solution. |
| version |Version de la solution, telle que déterminée par l’auteur. |
| product |Chaîne unique pour identifier la solution. |
| publisher |Éditeur de la solution. |

## <a name="other-resources"></a>Autres ressources
Pour obtenir des informations et des exemples de ressources communes aux solutions de gestion, consultez les articles suivants.

* [Vues et tableaux de bord](operations-management-suite-solutions-resources-views.md)
* [Ressources Automation](operations-management-suite-solutions-resources-automation.md)

## <a name="testing-a-management-solution"></a>Test d’une solution de gestion
Avant de déployer votre solution de gestion, il est recommandé de la tester à l’aide de [Test-AzureRmResourceGroupDeployment](../azure-resource-manager/resource-group-template-deploy.md#deploy).  Cela permet de valider votre fichier de solution et vous aide à identifier les problèmes éventuels avant son déploiement.

## <a name="next-steps"></a>Étapes suivantes
* [Ajoutez des alertes et des recherches enregistrées](operations-management-suite-solutions-resources-searches-alerts.md) à votre solution de gestion.
* [Ajoutez des vues](operations-management-suite-solutions-resources-views.md) à votre solution de gestion.
* [Ajoutez des runbooks Automation et d’autres ressources](operations-management-suite-solutions-resources-automation.md) à votre solution de gestion.
* Découvrez comment [créer des modèles Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md).
* Dans [Modèles de démarrage rapide Azure](https://azure.microsoft.com/documentation/templates), recherchez des exemples de modèles Resource Manager.



<!--HONumber=Jan17_HO4-->


