---
title: "Basculer entre le mode Affichage et le mode Édition pour les rapports dans Azure Power BI Embedded | Microsoft Docs"
description: "Découvrez comment basculer entre le mode Affichage et le mode Édition pour vos rapports dans Power BI Embedded."
services: power-bi-embedded
documentationcenter: 
author: guyinacube
manager: erikre
editor: 
tags: 
ms.assetid: 
ms.service: power-bi-embedded
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: powerbi
ms.date: 03/11/2017
ms.author: asaxton
ms.translationtype: Human Translation
ms.sourcegitcommit: c1cd1450d5921cf51f720017b746ff9498e85537
ms.openlocfilehash: f73bf05b41523a5833cc9366fb84cb7021b4b7a9
ms.contentlocale: fr-fr
ms.lasthandoff: 03/14/2017

---
# <a name="toggle-between-view-and-edit-mode-for-reports-in-power-bi-embedded"></a>Basculer entre le mode Affichage et le mode Édition pour les rapports dans Power BI Embedded

Découvrez comment basculer entre le mode Affichage et le mode Édition pour vos rapports dans Power BI Embedded.

## <a name="creating-an-access-token"></a>Créer un jeton d’accès

Il vous faudra créer un jeton d’accès qui vous donne la possibilité d’afficher et de modifier un rapport. Pour modifier et enregistrer un rapport, vous aurez besoin de l’autorisation du jeton **Report.ReadWrite**. Pour plus d’informations, consultez la page [Authentification et autorisation dans Power BI Embedded](power-bi-embedded-app-token-flow.md).

> [!NOTE]
> Cela vous permettra de modifier et d’enregistrer les modifications apportées à un rapport existant. Si vous souhaitez également obtenir la fonction de prise en charge de l’opération **Enregistrer sous**, vous devrez fournir des autorisations supplémentaires. Pour plus d’informations, consultez la page [Étendues](power-bi-embedded-app-token-flow.md#scopes).

```
using Microsoft.PowerBI.Security;

// rlsUsername and roles are optional
string scopes = "Report.ReadWrite";
PowerBIToken embedToken = PowerBIToken.CreateReportEmbedTokenForCreation(workspaceCollectionName, workspaceId, datasetId, null, null, scopes);

var token = embedToken.Generate("{access key}");
```

## <a name="embed-configuration"></a>Configuration d’incorporation

Vous devrez fournir les autorisations et un mode Affichage pour voir le bouton « enregistrer » en mode Édition. Pour plus d’informations, consultez la page [Détails de la configuration d’incorporation](https://github.com/Microsoft/PowerBI-JavaScript/wiki/Embed-Configuration-Details).

Par exemple, dans JavaScript :

```
   <div id="reportContainer"></div>

    // Get models. Models, it contains enums that can be used.
    var models = window['powerbi-client'].models;

    // Embed configuration used to describe the what and how to embed.
    // This object is used when calling powerbi.embed.
    // This also includes settings and options such as filters.
    // You can find more information at https://github.com/Microsoft/PowerBI-JavaScript/wiki/Embed-Configuration-Details.
    var config= {
        type: 'report',
        accessToken: 'eyJ0eXAiO...Qron7qYpY9MI',
        embedUrl: 'https://embedded.powerbi.com/appTokenReportEmbed',
        id:  '5dac7a4a-4452-46b3-99f6-a25915e0fe55',
        permissions: models.Permissions.ReadWrite /*both save & save as buttons will be visible*/,
        viewMode: models.ViewMode.View,
        settings: {
            filterPaneEnabled: true,
            navContentPaneEnabled: true
        }
    };

    // Get a reference to the embedded report HTML element
    var reportContainer = $('#reportContainer')[0];

    // Embed the report and display it within the div container.
    var report = powerbi.embed(reportContainer, config);
```

La valeur **models.ViewMode.View** de **viewMode** indique que le rapport devra être incorporé en mode Affichage.

## <a name="view-mode"></a>Mode Affichage

Vous pouvez utiliser le code JavaScript suivant pour basculer en mode Affichage, si vous êtes en mode Édition.

```
// Get a reference to the embedded report HTML element
var reportContainer = $('#reportContainer')[0];

// Get a reference to the embedded report.
report = powerbi.get(reportContainer);

// Switch to view mode.
report.switchMode("view");

```

## <a name="edit-mode"></a>Mode Édition

Vous pouvez utiliser le code JavaScript suivant pour basculer en mode Édition, si vous êtes en mode Affichage.

```
// Get a reference to the embedded report HTML element
var reportContainer = $('#reportContainer')[0];

// Get a reference to the embedded report.
report = powerbi.get(reportContainer);

// Switch to edit mode.
report.switchMode("edit");

```

## <a name="see-also"></a>Voir aussi

[Prise en main de l’exemple](power-bi-embedded-get-started-sample.md)  
[Incorporer un rapport](power-bi-embedded-embed-report.md)  
[Authentification et autorisation dans Power BI Embedded](power-bi-embedded-app-token-flow.md)  
[CreateReportEmbedToken](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.powerbitoken?redirectedfrom=MSDN#methods_)  
[Exemple d’incorporation JavaScript](https://microsoft.github.io/PowerBI-JavaScript/demo/)  
[Référentiel Git PowerBI-CSharp](https://github.com/Microsoft/PowerBI-CSharp)  
[Référentiel Git PowerBI-Node](https://github.com/Microsoft/PowerBI-Node)  
Des questions ? [Essayer la communauté Power BI](http://community.powerbi.com/)

