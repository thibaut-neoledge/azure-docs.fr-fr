---
title: "Création de tests de disponibilité, des alertes et des ressources Azure Application Insights dans PowerShell | Microsoft Docs"
description: "Automatisez la gestion des ressources Application Insights à l’aide d’un modèle Azure Resource Manager."
services: application-insights
documentationcenter: 
author: alancameronwills
manager: carmonm
ms.assetid: 9f73b87f-be63-4847-88c8-368543acad8b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 12/16/2016
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: 86118ebfcb0c7c55ff414d381645025f41c74eb7
ms.openlocfilehash: ba52b3dc55d80cc5944f16c238a2ea0d66c94990


---
# <a name="create-application-insights-resources-using-powershell"></a>Créer des ressources Application Insights à l’aide de PowerShell
Cet article vous montre comment créer une ressource [Application Insights](app-insights-overview.md) dans Azure automatiquement. Cette opération peut par exemple avoir lieu dans le cadre du processus de génération. Avec la ressource Application Insights de base, vous pouvez créer des [tests web de disponibilité](app-insights-monitor-web-app-availability.md), [configurer des alertes](app-insights-alerts.md) et créer d’autres ressources Azure.

Les éléments importants pour la création de ces ressources sont les modèles JSON pour [Azure Resource Manager](../azure-resource-manager/powershell-azure-resource-manager.md). En bref, la procédure est la suivante : téléchargez les définitions JSON des ressources existantes, paramétrez certaines valeurs telles que les noms, puis exécutez le modèle chaque fois que vous souhaitez créer une nouvelle ressource. Vous pouvez regrouper plusieurs ressources pour les créer en une fois, par exemple une analyse d’application avec des tests de disponibilité, des alertes et le stockage pour l’exportation continue. Certains paramètres ont des particularités que nous aborderons ici.

## <a name="one-time-setup"></a>Installation unique
Si vous n’avez pas utilisé précédemment PowerShell avec votre abonnement Azure :

Installez le module Azure Powershell sur l’ordinateur sur lequel vous souhaitez exécuter les scripts :

1. Installez le programme [Microsoft Web Platform Installer (v5 ou version ultérieure)](http://www.microsoft.com/web/downloads/platform.aspx).
2. Utilisez-le pour installer Microsoft Azure PowerShell.

## <a name="create-an-azure-resource-manager-template"></a>Créer un modèle Azure Resource Manager
Créez un fichier .json appelé `template1.json` dans cet exemple. Copiez-y ce contenu :

```JSON
{
          "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "appName": {
              "type": "string",
              "metadata": {
                "description": "Enter the application name."
              }
            },
            "applicationType": {
              "type": "string",
              "defaultValue": "ASP.NET web application",
              "allowedValues": [ "ASP.NET web application", "Java web application", "HockeyApp bridge application", "Other (preview)" ],
              "metadata": {
                "description": "Enter the application type."
              }
            },
            "appLocation": {
              "type": "string",
              "defaultValue": "East US",
              "allowedValues": [ "South Central US", "West Europe", "East US", "North Europe" ],
              "metadata": {
                "description": "Enter the application location."
              }
            },
            "priceCode": {
              "type": "int",
              "defaultValue": 1,
              "allowedValues": [ 1, 2 ],
              "metadata": {"description": "1 = Basic, 2 = Enterprise"}
            },
            "dailyQuota": {
              "type": "int",
              "defaultValue": 100,
              "minValue": 1,
              "metadata": {
                "description": "Enter daily quota in GB."
              }
            },
            "dailyQuotaResetTime": {
              "type": "int",
              "defaultValue": 24,
              "metadata": {
                "description": "Enter daily quota reset hour in UTC (0 to 23). Values outside the range will get a random reset hour."
              }
            },
            "warningThreshold": {
              "type": "int",
              "defaultValue": 90,
              "minValue": 1,
              "maxValue": 100,
              "metadata": {
                "description": "Enter the % value of daily quota after which warning mail to be sent. "
              }
            }
          },

         "variables": {
           "priceArray": [ "Basic", "Application Insights Enterprise" ],
           "pricePlan": "[take(variables('priceArray'),parameters('priceCode'))]",
           "billingplan": "[concat(parameters('appName'),'/', variables('pricePlan')[0])]"
          },

          "resources": [
            {
              "apiVersion": "2014-08-01",
              "location": "[parameters('appLocation')]",
              "name": "[parameters('appName')]",
              "type": "microsoft.insights/components",
              "properties": {
                "Application_Type": "[parameters('applicationType')]",
                "ApplicationId": "[parameters('appName')]",
                "Name": "[parameters('appName')]",
                "Flow_Type": "Redfield",
                "Request_Source": "IbizaAIExtension"
              }
            },
            {
              "name": "[variables('billingplan')]",
              "type": "microsoft.insights/components/CurrentBillingFeatures",
              "location": "[parameters('appLocation')]",
              "apiVersion": "2015-05-01",
              "dependsOn": [
                "[resourceId('microsoft.insights/components', parameters('appName'))]"
              ],
              "properties": {
                "CurrentBillingFeatures": "[variables('pricePlan')]",
                "DataVolumeCap": {
                  "Cap": "[parameters('dailyQuota')]",
                  "WarningThreshold": "[parameters('warningThreshold')]",
                  "ResetTime": "[parameters('dailyQuotaResetTime')]"
                }
              }
            },

          "__comment":"web test, alert, and any other resources go here"
          ]
        }

```



## <a name="create-application-insights-resources"></a>Créer des ressources Application Insights
1. Dans PowerShell, connectez-vous à Azure :
   
    `Login-AzureRmAccount`
2. Exécutez une commande comme suit :
   
    ```PS
   
        New-AzureRmResourceGroupDeployment -ResourceGroupName Fabrikam `
               -TemplateFile .\template1.json `
               -appName myNewApp

    ``` 
   
   * `-ResourceGroupName` est le groupe dans lequel vous souhaitez créer les nouvelles ressources.
   * `-TemplateFile` doit se produire avant les paramètres personnalisés.
   * `-appName` est le nom de la ressource à créer.

Vous pouvez ajouter d’autres paramètres. Vous trouverez leurs descriptions dans la section Paramètres du modèle.

## <a name="enterprise-price-plan"></a>Plan de tarification Entreprise

Pour créer une ressource d’application avec le plan de tarification Entreprise à l’aide du modèle ci-dessus :

```PS
   

        New-AzureRmResourceGroupDeployment -ResourceGroupName Fabrikam `
               -TemplateFile .\template1.json `
               -priceCode 2 `
               -appName myNewApp
```

* Si vous souhaitez simplement utiliser le plan de tarification de base par défaut, vous pouvez omettre la ressource Plan de tarification dans le modèle.


## <a name="to-get-the-instrumentation-key"></a>Pour récupérer la clé d’instrumentation
Après avoir créé une ressource d’application, vous aurez besoin de l’iKey : 

```PS

    $resource = Get-AzureRmResource -ResourceId "/subscriptions/<YOUR SUBSCRIPTION ID>/resourceGroups/<YOUR RESOURCE GROUP>/providers/Microsoft.Insights/components/<YOUR APP NAME>"

    $resource.Properties.InstrumentationKey
```

## <a name="add-a-metric-alert"></a>Ajouter une alerte métrique

Pour configurer une alerte de métrique en même temps que votre ressource d’application, fusionnez le code comme suit dans le fichier de modèle :

```JSON

    parameters: { ... // existing parameters ...
       ,       
            "responseTime": {
              "type": "int",
              "defaultValue": 3,
              "minValue": 1,
              "metadata": {
                "description": "Enter response time threshold in seconds."
              }
    },
    variables: { ... // existing variables ...
      ,
      // Alert names must be unique within resource group.
      "responseAlertName": "[concat('ResponseTime-', toLower(parameters('appName')))]"
    }, 
    resources: { ... // existing resources ...
     ,
     {
      //
      // Metric alert on response time
      //
      "name": "[variables('responseAlertName')]",
      "type": "Microsoft.Insights/alertrules",
      "apiVersion": "2014-04-01",
      "location": "[parameters('appLocation')]",
      // Ensure this resource is created after the app resource:
      "dependsOn": [
        "[resourceId('Microsoft.Insights/components', parameters('appName'))]"
      ],
      "tags": {
        "[concat('hidden-link:', resourceId('Microsoft.Insights/components', parameters('appName')))]": "Resource"
      },
      "properties": {
        "name": "[variables('responseAlertName')]",
        "description": "response time alert",
        "isEnabled": true,
        "condition": {
          "$type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.ThresholdRuleCondition, Microsoft.WindowsAzure.Management.Mon.Client",
          "odata.type": "Microsoft.Azure.Management.Insights.Models.ThresholdRuleCondition",
          "dataSource": {
            "$type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.RuleMetricDataSource, Microsoft.WindowsAzure.Management.Mon.Client",
            "odata.type": "Microsoft.Azure.Management.Insights.Models.RuleMetricDataSource",
            "resourceUri": "[resourceId('microsoft.insights/components', parameters('appName'))]",
            "metricName": "request.duration"
          },
          "threshold": "[parameters('responseTime')]", //seconds
          "windowSize": "PT15M" // Take action if changed state for 15 minutes
        },
        "actions": [
          {
            "$type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.RuleEmailAction, Microsoft.WindowsAzure.Management.Mon.Client",
            "odata.type": "Microsoft.Azure.Management.Insights.Models.RuleEmailAction",
            "sendToServiceOwners": true,
            "customEmails": []
          }
        ]
      }
    }

```

Lorsque vous appelez le modèle, vous pouvez éventuellement ajouter ce paramètre :

    `-responseTime 2`

Bien entendu, vous pouvez paramétrer d’autres champs. 

Pour rechercher les noms de type et les détails de configuration d’autres règles d’alerte, créez une règle manuellement et examinez-la dans [Azure Resource Manager](https://resources.azure.com/). 


## <a name="add-an-availability-test"></a>Ajouter un test de disponibilité

Cet exemple concerne un test Ping (pour tester une seule page).  

**Il existe deux parties** dans un test de disponibilité : le test lui-même et l’alerte qui vous informe des échecs.

Fusionnez le code suivant dans le fichier de modèle qui crée l’application.

```JSON

    parameters: { ... // existing parameters here ...
      ,
      "pingURL": { "type": "string" },
      "pingText": { "type": "string" , defaultValue: ""}
    },
    variables: { ... // existing variables here ...
      ,
      "pingTestName":"[concat('PingTest-', toLower(parameters('appName')))]",
      "pingAlertRuleName": "[concat('PingAlert-', toLower(parameters('appName')), '-', subscription().subscriptionId)]"
    },
    resources: { ... // existing resources here ...
    ,  
    { //
      // Availability test: part 1 configures the test
      //
      "name": "[variables('pingTestName')]",
      "type": "Microsoft.Insights/webtests",
      "apiVersion": "2014-04-01",
      "location": "[parameters('appLocation')]",
      // Ensure this is created after the app resource:
      "dependsOn": [
        "[resourceId('Microsoft.Insights/components', parameters('appName'))]"
      ],
      "tags": {
        "[concat('hidden-link:', resourceId('Microsoft.Insights/components', parameters('appName')))]": "Resource"
      },
      "properties": {
        "Name": "[variables('pingTestName')]",
        "Description": "Basic ping test",
        "Enabled": true,
        "Frequency": 900, // 15 minutes
        "Timeout": 120, // 2 minutes
        "Kind": "ping", // single URL test
        "RetryEnabled": true,
        "Locations": [
          {
            "Id": "us-va-ash-azr"
          },
          {
            "Id": "emea-nl-ams-azr"
          },
          {
            "Id": "apac-jp-kaw-edge"
          }
        ],
        "Configuration": {
          "WebTest": "[concat('<WebTest   Name=\"', variables('pingTestName'), '\"   Enabled=\"True\"         CssProjectStructure=\"\"    CssIteration=\"\"  Timeout=\"120\"  WorkItemIds=\"\"         xmlns=\"http://microsoft.com/schemas/VisualStudio/TeamTest/2010\"         Description=\"\"  CredentialUserName=\"\"  CredentialPassword=\"\"         PreAuthenticate=\"True\"  Proxy=\"default\"  StopOnError=\"False\"         RecordedResultFile=\"\"  ResultsLocale=\"\">  <Items>  <Request Method=\"GET\"    Version=\"1.1\"  Url=\"', parameters('Url'),   '\" ThinkTime=\"0\"  Timeout=\"300\" ParseDependentRequests=\"True\"         FollowRedirects=\"True\" RecordResult=\"True\" Cache=\"False\"         ResponseTimeGoal=\"0\"  Encoding=\"utf-8\"  ExpectedHttpStatusCode=\"200\"         ExpectedResponseUrl=\"\" ReportingName=\"\" IgnoreHttpStatusCode=\"False\" />        </Items>  <ValidationRules> <ValidationRule  Classname=\"Microsoft.VisualStudio.TestTools.WebTesting.Rules.ValidationRuleFindText, Microsoft.VisualStudio.QualityTools.WebTestFramework, Version=10.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a\" DisplayName=\"Find Text\"         Description=\"Verifies the existence of the specified text in the response.\"         Level=\"High\"  ExectuionOrder=\"BeforeDependents\">  <RuleParameters>        <RuleParameter Name=\"FindText\" Value=\"',   parameters('pingText'), '\" />  <RuleParameter Name=\"IgnoreCase\" Value=\"False\" />  <RuleParameter Name=\"UseRegularExpression\" Value=\"False\" />  <RuleParameter Name=\"PassIfTextFound\" Value=\"True\" />  </RuleParameters> </ValidationRule>  </ValidationRules>  </WebTest>')]"
        },
        "SyntheticMonitorId": "[variables('pingTestName')]"
      }
    },

    {
      //
      // Availability test: part 2, the alert rule
      //
      "name": "[variables('pingAlertRuleName')]",
      "type": "Microsoft.Insights/alertrules",
      "apiVersion": "2014-04-01",
      "location": "[parameters('appLocation')]", 
      "dependsOn": [
        "[resourceId('Microsoft.Insights/webtests', variables('pingTestName'))]"
      ],
      "tags": {
        "[concat('hidden-link:', resourceId('Microsoft.Insights/components', parameters('appName')))]": "Resource",
        "[concat('hidden-link:', resourceId('Microsoft.Insights/webtests', variables('pingTestName')))]": "Resource"
      },
      "properties": {
        "name": "[variables('pingAlertRuleName')]",
        "description": "alert for web test",
        "isEnabled": true,
        "condition": {
          "$type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.LocationThresholdRuleCondition, Microsoft.WindowsAzure.Management.Mon.Client",
          "odata.type": "Microsoft.Azure.Management.Insights.Models.LocationThresholdRuleCondition",
          "dataSource": {
            "$type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.RuleMetricDataSource, Microsoft.WindowsAzure.Management.Mon.Client",
            "odata.type": "Microsoft.Azure.Management.Insights.Models.RuleMetricDataSource",
            "resourceUri": "[resourceId('microsoft.insights/webtests', variables('pingTestName'))]",
            "metricName": "GSMT_AvRaW"
          },
          "windowSize": "PT15M", // Take action if changed state for 15 minutes
          "failedLocationCount": 2
        },
        "actions": [
          {
            "$type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.RuleEmailAction, Microsoft.WindowsAzure.Management.Mon.Client",
            "odata.type": "Microsoft.Azure.Management.Insights.Models.RuleEmailAction",
            "sendToServiceOwners": true,
            "customEmails": []
          }
        ]
      }
    }

```

Pour découvrir les codes pour d’autres emplacements de test ou pour automatiser la création de tests web plus complexes, créez un exemple manuellement, puis paramétrez ensuite le code à partir [d’Azure Resource Manager](https://resources.azure.com/).

## <a name="add-more-resources"></a>Ajouter des ressources

Pour automatiser la création de toute autre ressource de tout type, créez un exemple manuellement, puis copiez-le et paramétrez son code à partir [d’Azure Resource Manager](https://resources.azure.com/). 

1. Ouvrez [Azure Resource Manager](https://resources.azure.com/). Accédez à votre ressource d’application qui se trouve après `subscriptions/resourceGroups/<your resource group>/providers/Microsoft.Insights/components`. 
   
    ![Navigation dans Azure Resource Explorer](./media/app-insights-powershell/01.png)
   
    *composants* sont les ressources Application Insights de base pour l’affichage des applications. Il existe des ressources distinctes pour les règles d’alertes et les tests web de disponibilité associés.
2. Copiez le JSON du composant dans l’emplacement approprié dans `template1.json`.
3. Supprimez les propriétés suivantes :
   
   * `id`
   * `InstrumentationKey`
   * `CreationDate`
   * `TenantId`
4. Ouvrez les sections webtests et alertrules et copiez le JSON pour des éléments individuels dans votre modèle. (Ne copiez pas à partir des nœuds webtests ou alertrules : accédez aux éléments sous-jacents.)
   
    Chaque test web étant associé à une règle d’alerte, vous devez copier les deux.
   
    Vous pouvez également inclure des alertes sur des métriques. [Noms de métriques](app-insights-powershell-alerts.md#metric-names).
5. Insérez cette ligne dans chaque ressource :
   
    `"apiVersion": "2015-05-01",`

### <a name="parameterize-the-template"></a>Définir les paramètres du modèle
Vous devez à présent remplacer les noms spécifiques par des paramètres. Pour [définir les paramètres d’un modèle](../azure-resource-manager/resource-group-authoring-templates.md), écrivez des expressions utilisant un [ensemble de fonctions d’assistance](../azure-resource-manager/resource-group-template-functions.md). 

Comme vous ne pouvez pas définir uniquement les paramètres d’une portion de chaîne, utilisez `concat()` pour générer les chaînes.

Voici des exemples de substitutions que vous allez effectuer. Il existe plusieurs occurrences de chaque substitution. Il vous en faudra peut-être d’autres dans votre modèle. Ces exemples utilisent les paramètres et les variables que nous avons définis en haut du modèle.

| find | replace with |
| --- | --- |
| `"hidden-link:/subscriptions/.../components/MyAppName"` |`"[concat('hidden-link:',`<br/>` resourceId('microsoft.insights/components',` <br/> ` parameters('appName')))]"` |
| `"/subscriptions/.../alertrules/myAlertName-myAppName-subsId",` |`"[resourceId('Microsoft.Insights/alertrules', variables('alertRuleName'))]",` |
| `"/subscriptions/.../webtests/myTestName-myAppName",` |`"[resourceId('Microsoft.Insights/webtests', parameters('webTestName'))]",` |
| `"myWebTest-myAppName"` |`"[variables(testName)]"'` |
| `"myTestName-myAppName-subsId"` |`"[variables('alertRuleName')]"` |
| `"myAppName"` |`"[parameters('appName')]"` |
| `"myappname"` (minuscules) |`"[toLower(parameters('appName'))]"` |
| `"<WebTest Name=\"myWebTest\" ...`<br/>` Url=\"http://fabrikam.com/home\" ...>"` |`[concat('<WebTest Name=\"',` <br/> `parameters('webTestName'),` <br/> `'\" ... Url=\"', parameters('Url'),` <br/> `'\"...>')]"`<br/>Supprimez le GUID et l’ID. |

### <a name="set-dependencies-between-the-resources"></a>Définir les dépendances entre les ressources
Azure doit configurer les ressources dans un ordre strict. Pour vous assurer de l’achèvement d’une installation avant que la suivante ne commence, ajoutez les lignes de dépendance :

* Dans la ressource du test de disponibilité :
  
    `"dependsOn": ["[resourceId('Microsoft.Insights/components', parameters('appName'))]"],`
* Dans la ressource d’alerte pour un test de disponibilité :
  
    `"dependsOn": ["[resourceId('Microsoft.Insights/webtests', variables('testName'))]"],`



## <a name="next-steps"></a>Étapes suivantes
Autres articles sur l’automation :

* [Création d'une ressource Application Insights](app-insights-powershell-script-create-resource.md) - méthode rapide sans utiliser de modèle.
* [Configurer des alertes](app-insights-powershell-alerts.md)
* [Créer des tests web](https://azure.microsoft.com/blog/creating-a-web-test-alert-programmatically-with-application-insights/)
* [Envoyer des diagnostics Azure vers Application Insights.](app-insights-powershell-azure-diagnostics.md)
* [Déployer sur Azure à partir de GitHub](http://blogs.msdn.com/b/webdev/archive/2015/09/16/deploy-to-azure-from-github-with-application-insights.aspx)
* [Créer des annotations de version](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/API/CreateReleaseAnnotation.ps1)




<!--HONumber=Dec16_HO3-->


