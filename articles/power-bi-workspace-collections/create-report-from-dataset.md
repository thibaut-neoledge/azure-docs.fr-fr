---
title: "Créer un rapport à partir d’un jeu de données dans les Collections d’espaces de travail Power BI | Microsoft Docs"
description: "Vous pouvez désormais créer des rapports Collections d’espaces de travail Power BI à partir d’un jeu de données dans votre propre application."
services: power-bi-embedded
documentationcenter: 
author: guyinacube
manager: erikre
editor: 
tags: 
ROBOTS: NOINDEX
ms.assetid: 
ms.service: power-bi-embedded
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: powerbi
ms.date: 09/20/2017
ms.author: asaxton
ms.openlocfilehash: aa902cbc4992292420948b36d85e52fafc7224de
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-new-report-from-a-dataset-in-power-bi-workspace-collections"></a>Créer un rapport à partir d’un jeu de données dans Collections d’espaces de travail Power BI

Vous pouvez désormais créer des rapports Collections d’espaces de travail Power BI à partir d’un jeu de données dans votre propre application.

> [!IMPORTANT]
> Les Collections d’espaces de travail Power BI sont dépréciés et disponibles jusqu’en juin 2018 ou jusqu’à la date indiquée sur votre contrat. Nous vous conseillons de planifier votre migration vers Power BI Embedded pour éviter toute interruption dans votre application. Pour plus d’informations sur la façon de migrer vos données vers Power BI Embedded, consultez [Comment migrer le contenu d’une collection d’espaces de travail Power BI Embedded vers Power BI](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/).

La méthode d’authentification est similaire à celle employée pour l’incorporation d’un rapport. Elle est basée sur des jetons d’accès propres à un jeu de données. Les jetons utilisés pour PowerBI.com sont émis par Azure Active Directory (AAD). Les jetons Collections d’espaces de travail Power BI sont émis par votre propre application.

Lors de la création d’un rapport Embedded, les jetons sont émis pour un jeu de données spécifique. Les jetons doivent être associés à l’URL d’incorporation sur le même élément, de sorte que chacune possède un jeton unique. Pour créer un rapport Embedded, les étendues *Dataset.Read et Workspace.Report.Create* doivent être fournies dans le jeton d’accès.

## <a name="create-access-token-needed-to-create-new-report"></a>Créer le jeton d’accès nécessaire à la création d’un rapport

Le service Collections d’espaces de travail BI Power utilise des jetons d’incorporation, qui sont des jetons web JSON signés HMAC. Les jetons sont signés avec la clé d’accès issue de votre collection d’espaces de travail Power BI. Par défaut, les jetons d’incorporation sont utilisés pour fournir un accès en lecture seule à un rapport à incorporer dans une application. Les jetons d’incorporation sont émis pour un rapport donné et doivent être associés à une URL d’incorporation.

Les jetons d’accès doivent être créés sur le serveur, car les clés d’accès sont utilisées pour signer / chiffrer les jetons. Pour plus d’informations sur la façon de créer des jetons d’accès, consultez [Authentification et autorisation avec le service Collections d’espaces de travail Power BI](app-token-flow.md). Vous pouvez également consulter la méthode [CreateReportEmbedToken](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.powerbitoken?redirectedfrom=MSDN#methods_). Voici un exemple de ce à quoi cela ressemblerait avec le kit SDK .NET pour Power BI.

Dans cet exemple, nous avons l’ID de jeu de données sur lequel nous voulons créer le rapport. Nous devons également ajouter les étendues de *Dataset.Read et Workspace.Report.Create*.

La *classe PowerBIToken* requiert l’installation du [package NuGet Power BI Core](https://www.nuget.org/packages/Microsoft.PowerBI.Core/).

**Installation du package NuGet**

```
Install-Package Microsoft.PowerBI.Core
```

**Code C#**

```
using Microsoft.PowerBI.Security;

// rlsUsername and roles are optional
string scopes = "Dataset.Read Workspace.Report.Create";
PowerBIToken embedToken = PowerBIToken.CreateReportEmbedTokenForCreation(workspaceCollectionName, workspaceId, datasetId, null, null, scopes);

var token = embedToken.Generate("{access key}");
```

## <a name="create-a-new-blank-report"></a>Créer un rapport vide

Pour pouvoir créer un nouveau rapport, la configuration de création doit être fournie. Elle doit inclure le jeton d’accès, l’URL d’incorporation et l’ID de jeu de données sur lesquels nous souhaitons créer le rapport. Cela requiert l’installation du [package NuGet Power BI JavaScript](https://www.nuget.org/packages/Microsoft.PowerBI.JavaScript/). L’URL d’incorporation est simplement https://embedded.powerbi.com/appTokenReportEmbed.

> [!NOTE]
> Vous pouvez utiliser [l’exemple d’incorporation de rapport JavaScript](https://microsoft.github.io/PowerBI-JavaScript/demo/) pour tester les fonctionnalités. Il donne également des exemples de code pour les différentes opérations disponibles.

**Installation du package NuGet**

```
Install-Package Microsoft.PowerBI.JavaScript
```

**Code JavaScript**

```
<div id="reportContainer"></div>
  
var embedCreateConfiguration = {
        accessToken: 'eyJ0eXAiO...Qron7qYpY9MI',
        embedUrl: 'https://embedded.powerbi.com/appTokenReportEmbed',
        datasetId: '5dac7a4a-4452-46b3-99f6-a25915e0fe55',
    };
    
    // Grab the reference to the div HTML element that will host the report
    var reportContainer = $('#reportContainer')[0];

    // Create report
    var report = powerbi.createReport(reportContainer, embedCreateConfiguration);
```

L’appel de *powerbi.createReport()* fait apparaître un canevas vide en mode Édition dans l’élément *div*.

![Nouveau rapport vide](media/create-report-from-dataset/create-new-report.png)

## <a name="save-new-reports"></a>Enregistrer de nouveaux rapports

Le rapport n’est pas créé tant que vous n’avez pas appelé l’opération **Enregistrer sous**. Vous pouvez le faire dans le menu Fichier ou à partir de JavaScript.

```
 // Get a reference to the embedded report.
    report = powerbi.get(reportContainer);
    
    var saveAsParameters = {
        name: "newReport"
    };

    // SaveAs report
    report.saveAs(saveAsParameters);
```

> [!IMPORTANT]
> Le nouveau rapport n’est créé qu’une fois que l’opération **enregistrer sous** a été appelée. Après l’enregistrement, le canevas continue d’afficher le jeu de données en mode Édition et non le rapport. Vous devez recharger le nouveau rapport comme vous le feriez pour tout autre rapport.

![Menu Fichier - Enregistrer sous](media/create-report-from-dataset/save-new-report.png)

## <a name="load-the-new-report"></a>Charger le nouveau rapport

Pour interagir avec le nouveau rapport, vous devez l’incorporer de la même façon que l’application incorpore un rapport normal, ce qui signifie qu’un nouveau jeton doit être émis spécifiquement pour le nouveau rapport avant que vous n’appeliez la méthode d’incorporation.

```
<div id="reportContainer"></div>
  
var embedConfiguration = {
        accessToken: 'eyJ0eXAiO...Qron7qYpY9MJ',
        embedUrl: 'https://embedded.powerbi.com/appTokenReportEmbed',
        reportId: '5dac7a4a-4452-46b3-99f6-a25915e0fe54',
    };
    
    // Grab the reference to the div HTML element that will host the report
    var reportContainer = $('#reportContainer')[0];

    // Embed report
    var report = powerbi.embed(reportContainer, embedConfiguration);
```

## <a name="automate-save-and-load-of-a-new-report-using-the-saved-event"></a>Automatiser la sauvegarde et le chargement d’un nouveau rapport avec l’événement « enregistré »

Pour automatiser le processus « enregistrer sous », puis charger le nouveau rapport, vous pouvez utiliser l’événement « enregistré ». Cet événement est déclenché quand l’opération « enregistrer » est terminée. Il retourne un objet JSON qui contient le nouvel ID du rapport, le nom du rapport, l’ancien ID du rapport (le cas échéant) et le type d’opération (« enregistrer sous » ou « enregistrer »).

```
{
  "reportObjectId": "5dac7a4a-4452-46b3-99f6-a25915e0fe54",
  "reportName": "newReport",
  "saveAs": true,
  "originalReportObjectId": null
}
```

Pour automatiser le processus, vous pouvez écouter l’événement « enregistré », récupérer le nouvel ID du rapport, créer le nouveau jeton et y incorporer le nouveau rapport.

```
<div id="reportContainer"></div>
  
var embedCreateConfiguration = {
        accessToken: 'eyJ0eXAiO...Qron7qYpY9MI',
        embedUrl: 'https://embedded.powerbi.com/appTokenReportEmbed',
        datasetId: '5dac7a4a-4452-46b3-99f6-a25915e0fe55',
    };
    
    // Grab the reference to the div HTML element that will host the report
    var reportContainer = $('#reportContainer')[0];

    // Create report
    var report = powerbi.createReport(reportContainer, embedCreateConfiguration);


   var saveAsParameters = {
        name: "newReport"
    };

    // SaveAs report
    report.saveAs(saveAsParameters);

    // report.on will add an event handler which prints to Log window.
    report.on("saved", function(event) {
        
         // get new Token
         var newReportId =  event.detail.reportObjectId;

        // create new Token. This is a function that the application should provide
        var newToken = createAccessToken(newReportId,scopes /*provide the wanted scopes*/);
        
        
    var embedConfiguration = {
        accessToken: newToken ,
        embedUrl: 'https://embedded.powerbi.com/appTokenReportEmbed',
        reportId: newReportId,
    };

    // Embed report
    var report = powerbi.embed(reportContainer, embedConfiguration);
       
   // report.off removes a given event handler if it exists.
   report.off("saved");
    });
```

## <a name="see-also"></a>Voir aussi

[Prise en main de l’exemple](get-started-sample.md)  
[Enregistrer des rapports](save-reports.md)  
[Incorporer un rapport](embed-report.md)  
[Authentification et autorisation dans le service Collections d’espaces de travail Power BI](app-token-flow.md)  
[Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-get-the-desktop/)  
[Exemple d’incorporation JavaScript](https://microsoft.github.io/PowerBI-JavaScript/demo/)  
[Package NuGet Power BI Core](https://www.nuget.org/packages/Microsoft.PowerBI.Core/)  
[Package JavaScript Power BI Core](https://www.nuget.org/packages/Microsoft.PowerBI.JavaScript/)  

Des questions ? [Essayer la communauté Power BI](http://community.powerbi.com/)