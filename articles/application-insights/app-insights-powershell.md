---
title: Créer des ressources Application Insights à l’aide de PowerShell
description: Créer par programme des ressources Application Insights dans le cadre de la génération.
services: application-insights
documentationcenter: ''
author: alancameronwills
manager: douge

ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 03/02/2016
ms.author: awills

---
# Créer des ressources Application Insights à l’aide de PowerShell
Cet article vous montre comment créer une ressource [Application Insights](app-insights-overview.md) dans Azure automatiquement. Cette opération peut par exemple avoir lieu dans le cadre du processus de génération. Avec la ressource Application Insights de base, vous pouvez créer des [tests web de disponibilité](app-insights-monitor-web-app-availability.md), [configurer des alertes](app-insights-alerts.md) et créer d’autres ressources Azure.

Les éléments importants pour la création de ces ressources sont les modèles JSON pour [Azure Resource Manager](../powershell-azure-resource-manager.md). En bref, la procédure est la suivante : téléchargez les définitions JSON des ressources existantes, paramétrez certaines valeurs telles que les noms, puis exécutez le modèle chaque fois que vous souhaitez créer une nouvelle ressource. Vous pouvez regrouper plusieurs ressources pour les créer en une fois, par exemple une analyse d’application avec des tests de disponibilité, des alertes et le stockage pour l’exportation continue. Certains paramètres ont des particularités que nous aborderons ici.

## Installation unique
Si vous n’avez pas utilisé précédemment PowerShell avec votre abonnement Azure :

Installez le module Azure Powershell sur l’ordinateur sur lequel vous souhaitez exécuter les scripts :

1. Installez le programme [Microsoft Web Platform Installer (v5 ou version ultérieure)](http://www.microsoft.com/web/downloads/platform.aspx).
2. Utilisez-le pour installer Microsoft Azure PowerShell.

## Copier le JSON pour les ressources existantes
1. Configurez [Application Insights](app-insights-overview.md) pour un projet semblable à ceux que vous voulez générer automatiquement. Si vous le souhaitez, ajoutez les tests web et les alertes.
2. Créez un fichier .json appelé `template1.json` dans cet exemple. Copiez-y ce contenu :

    ```JSON

        {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "appName": { "type": "string" },
            "webTestName": { "type": "string" },
            "url": { "type": "string" },
            "text": { "type" : "string" }
          },
          "variables": {
            "testName": "[concat(parameters('webTestName'), 
               '-', toLower(parameters('appName')))]"
            "alertRuleName": "[concat(parameters('webTestName'), 
               '-', toLower(parameters('appName')), 
               '-', subscription().subscriptionId)]"
          },
          "resources": [
            {
              // component JSON file contents
            },
            {
              //web test JSON file contents
            },
            {
              //alert rule JSON file contents
            }

            // Any other resources go here
          ]
        }

    ```

    Ce modèle configurera un test de disponibilité en plus de la ressource principale.


1. Ouvrez [Azure Resource Manager](https://resources.azure.com/). Accédez à votre ressource d’application qui se trouve après les abonnements, les groupes de ressources et les composants.
   
    ![](./media/app-insights-powershell/01.png)
   
    Les *composants* sont les ressources Application Insights de base pour l’affichage des applications. Il existe des ressources distinctes pour les règles d’alertes et les tests web de disponibilité associés.
2. Copiez le JSON du composant dans l’emplacement approprié dans `template1.json`.
3. Supprimez les propriétés suivantes :
   * `id`
   * `InstrumentationKey`
   * `CreationDate`
4. Ouvrez les sections webtests et alertrules et copiez le JSON pour des éléments individuels dans votre modèle. (Ne copiez pas à partir des nœuds webtests ou alertrules : accédez aux éléments sous-jacents.)
   
    Chaque test web étant associé à une règle d’alerte, vous devez copier les deux.
   
    Le test web doit être placé avant la règle d’alerte.
5. Pour satisfaire le schéma, insérez cette ligne dans chaque ressource :
   
    `"apiVersion": "2014-04-01",`
   
    (Le schéma génère également une erreur en raison de la mise en majuscules des noms de type de ressource `Microsoft.Insights/*`, mais ne modifiez *pas* ces derniers.)

## Définir les paramètres du modèle
Vous devez à présent remplacer les noms spécifiques par des paramètres. Pour [définir les paramètres d’un modèle](../resource-group-authoring-templates.md), écrivez des expressions utilisant un [ensemble de fonctions d’assistance](../resource-group-template-functions.md).

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
| `"<WebTest Name="myWebTest" ...`<br/>` Url="http://fabrikam.com/home" ...>"` |`[concat('<WebTest Name="',` <br/> `parameters('webTestName'),` <br/> `'" ... Url="', parameters('Url'),` <br/> `'"...>')]" ` |

## Si votre application est une application web Azure
Ajoutez cette ressource, ou si vous disposez déjà d’une ressource `siteextensions`, paramétrez-la comme suit :

```json
    {
      "apiVersion": "2014-06-01",
      "name": "Microsoft.ApplicationInsights.AzureWebSites",
      "type": "siteextensions",
      "dependsOn": [
        "[resourceId('Microsoft.Web/Sites', parameters('siteName'))]",
        "[resourceId('Microsoft.Web/Sites/config', parameters('siteName'), 'web')]",
        "[resourceId('Microsoft.Web/sites/sourcecontrols', parameters('siteName'), 'web')]"
      ],
      "properties": { }
    }

```

Cette ressource déploie le kit de déploiement logiciel (SDK) Application Insights dans votre application web Azure.

## Définir les dépendances entre les ressources
Azure doit configurer les ressources dans un ordre strict. Pour vous assurer de l’achèvement d’une installation avant que la suivante ne commence, ajoutez les lignes de dépendance :

* Dans la ressource de test web :
  
    `"dependsOn": ["[resourceId('Microsoft.Insights/components', parameters('appName'))]"],`
* Dans la ressource d’alerte :
  
    `"dependsOn": ["[resourceId('Microsoft.Insights/webtests', variables('testName'))]"],`

## Créer des ressources Application Insights
1. Dans PowerShell, connectez-vous à Azure.
   
    `Login-AzureRmAccount`
2. Exécutez une commande comme suit :
   
    ```PS
   
        New-AzureRmResourceGroupDeployment -ResourceGroupName Fabrikam `
               -templateFile .\template1.json `
               -appName myNewApp `
               -webTestName aWebTest `
               -Url http://myapp.com `
               -text "Welcome!"
               -siteName "MyAzureSite"
   
    ``` 
   
   * -ResourceGroupName est le groupe dans lequel vous souhaitez créer les nouvelles ressources.
   * -templateFile doit se produire avant les paramètres personnalisés.
   * -appName est le nom de la ressource à créer.
   * -webTestName est le nom du test web à créer.
   * -Url est l’URL de votre application web.
   * -text est une chaîne qui apparaît dans votre page web.
   * -siteName : utilisé s’il s'agit d’un site Web Azure

## Définir les alertes de mesures
Il existe une [méthode PowerShell pour définir les alertes](app-insights-alerts.md#set-alerts-by-using-powershell).

## exemple
Voici l’intégralité des composants, test web et modèle d’alerte de test web que j’ai créés :

``` JSON

{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "webTestName": { "type": "string" },
    "appName": { "type": "string" },
    "URL": { "type": "string" },
    "text": { "type" : "string" }
  },
  "variables": {
    "alertRuleName": "[concat(parameters('webTestName'), '-', toLower(parameters('appName')), '-', subscription().subscriptionId)]",
    "testName": "[concat(parameters('webTestName'), '-', toLower(parameters('appName')))]"
  },
  "resources": [
    {
      //"id": "[resourceId('Microsoft.Insights/components', parameters('appName'))]",
      "apiVersion": "2014-04-01",
      "kind": "web",
      "location": "Central US",
      "name": "[parameters('appName')]",
      "properties": {
        "TenantId": "9122605a-471fc50f8438",
        "Application_Type": "web",
        "Flow_Type": "Brownfield",
        "Request_Source": "VSIX3.3.1.0",
        "Name": "[parameters('appName')]",
        //"CreationDate": "2015-10-14T15:55:10.0917441+00:00",
        "PackageId": null,
        "ApplicationId": "[parameters('appName')]"
      },
      "tags": { },
      "type": "microsoft.insights/components"
    },
    {
      //"id": "[resourceId('Microsoft.Insights/webtests', variables('testName'))]",
      "name": "[variables('testName')]",
      "apiVersion": "2014-04-01",
      "type": "microsoft.insights/webtests",
      "location": "Central US",
      "tags": {
        "[concat('hidden-link:', resourceId('microsoft.insights/components', parameters('appName')))]": "Resource"
      },
      "properties": {
        "provisioningState": "Succeeded",
        "Name": "[parameters('webTestName')]",
        "Description": "",
        "Enabled": true,
        "Frequency": 900,
        "Timeout": 120,
        "Kind": "ping",
        "RetryEnabled": true,
        "Locations": [
          {
            "Id": "us-va-ash-azr"
          },
          {
            "Id": "emea-nl-ams-azr"
          },
          {
            "Id": "emea-gb-db3-azr"
          }
        ],
        "Configuration": {
          "WebTest": "[concat(
             '<WebTest   Name="', 
                parameters('webTestName'), 
              '"  Id="32cfc791-aaad-4b50-9c8d-993c21beb218"   Enabled="True"         CssProjectStructure=""    CssIteration=""  Timeout="120"  WorkItemIds=""         xmlns="http://microsoft.com/schemas/VisualStudio/TeamTest/2010"         Description=""  CredentialUserName=""  CredentialPassword=""         PreAuthenticate="True"  Proxy="default"  StopOnError="False"         RecordedResultFile=""  ResultsLocale="">  <Items>  <Request Method="GET"         Guid="a6f2c90b-61bf-b28hh06gg969"  Version="1.1"  Url="', 
              parameters('Url'), 
              '" ThinkTime="0"  Timeout="300" ParseDependentRequests="True"         FollowRedirects="True" RecordResult="True" Cache="False"         ResponseTimeGoal="0"  Encoding="utf-8"  ExpectedHttpStatusCode="200"         ExpectedResponseUrl="" ReportingName="" IgnoreHttpStatusCode="False" />        </Items>  <ValidationRules> <ValidationRule  Classname="Microsoft.VisualStudio.TestTools.WebTesting.Rules.ValidationRuleFindText, Microsoft.VisualStudio.QualityTools.WebTestFramework, Version=10.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a" DisplayName="Find Text"         Description="Verifies the existence of the specified text in the response."         Level="High"  ExectuionOrder="BeforeDependents">  <RuleParameters>        <RuleParameter Name="FindText" Value="', 
              parameters('text'), 
              '" />  <RuleParameter Name="IgnoreCase" Value="False" />  <RuleParameter Name="UseRegularExpression" Value="False" />  <RuleParameter Name="PassIfTextFound" Value="True" />  </RuleParameters> </ValidationRule>  </ValidationRules>  </WebTest>')]"
        },
        "SyntheticMonitorId": "[variables('testName')]"
      }
    },
    {
      //"id": "[resourceId('Microsoft.Insights/alertrules', variables('alertRuleName'))]",
      "name": "[variables('alertRuleName')]",
      "apiVersion": "2014-04-01",
      "type": "microsoft.insights/alertrules",
      "location": "East US",
      "dependsOn": [
        "[resourceId('Microsoft.Insights/components', parameters('appName'))]",
        "[resourceId('Microsoft.Insights/webtests', variables('testName'))]"
      ],
      "tags": {
        "[concat('hidden-link:', resourceId('Microsoft.Insights/components', parameters('appName')))]": "Resource",
        "[concat('hidden-link:', resourceId('Microsoft.Insights/webtests', variables('testName')))]": "Resource"
      },
      "properties": {
        "name": "[variables('alertRuleName')]",
        "description": "",
        "isEnabled": true,
        "condition": {
          "$type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.LocationThresholdRuleCondition, Microsoft.WindowsAzure.Management.Mon.Client",
          "odata.type": "Microsoft.Azure.Management.Insights.Models.LocationThresholdRuleCondition",
          "dataSource": {
            "$type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.RuleMetricDataSource, Microsoft.WindowsAzure.Management.Mon.Client",
            "odata.type": "Microsoft.Azure.Management.Insights.Models.RuleMetricDataSource",
            "resourceUri": "[resourceId('microsoft.insights/webtests', variables('testName'))]",
            "metricName": "GSMT_AvRaW"
          },
          "windowSize": "PT15M",
          "failedLocationCount": 2
        },
        "action": {
          "$type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.RuleEmailAction, Microsoft.WindowsAzure.Management.Mon.Client",
          "odata.type": "Microsoft.Azure.Management.Insights.Models.RuleEmailAction",
          "sendToServiceOwners": true,
          "customEmails": [ ]
        },
        "provisioningState": "Succeeded",
        "actions": [ ]
      }

    }
  ]
}

```

## Voir aussi
Autres articles sur l’automation :

* [Création d'une ressource Application Insights](app-insights-powershell-script-create-resource.md) - méthode rapide sans utiliser de modèle.
* [Configurer des alertes](app-insights-powershell-alerts.md)
* [Créer des tests web](https://azure.microsoft.com/blog/creating-a-web-test-alert-programmatically-with-application-insights/)
* [Envoyer des diagnostics Azure vers Application Insights.](app-insights-powershell-azure-diagnostics.md)
* [Créer des annotations de version](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/API/CreateReleaseAnnotation.ps1)

<!---HONumber=AcomDC_0727_2016-->