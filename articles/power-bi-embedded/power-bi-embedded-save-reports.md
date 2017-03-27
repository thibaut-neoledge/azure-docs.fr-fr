---
title: Enregistrer des rapports dans Azure Power BI Embedded | Microsoft Docs
description: "Découvrez comment enregistrer des rapports dans Power BI Embedded. Cela requiert les autorisations appropriées pour fonctionner correctement."
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
translationtype: Human Translation
ms.sourcegitcommit: c1cd1450d5921cf51f720017b746ff9498e85537
ms.openlocfilehash: ad895004cc2972f2ded81566186325a16d401151
ms.lasthandoff: 03/14/2017

---
# <a name="save-reports-in-power-bi-embedded"></a>Enregistrer des rapports dans Power BI Embedded

Découvrez comment enregistrer des rapports dans Power BI Embedded. Cela requiert les autorisations appropriées pour fonctionner correctement.

Dans Power BI Embedded, vous pouvez modifier des rapports existants et les enregistrer. Vous pouvez également créer un nouveau rapport ou enregistrer un rapport sous un nouveau rapport.

Pour enregistrer un rapport, vous devez d’abord créer un jeton pour le rapport en question avec des étendues adaptées :

* Pour activer « enregistrer », l’étendue Report.ReadWrite est requise.
* Pour activer « enregistrer sous », les étendues Report.Read et Workspace.Report.Copy sont requises.
* Pour activer « enregistrer » et « enregistrer sous », les étendues Report.ReadWrite et Workspace.Report.Copy sont requises.

Pour activer les boutons « enregistrer » / « enregistrer sous » respectivement dans le menu Fichier, vous devez fournir les autorisations adéquates dans la configuration d’incorporation lorsque vous incorporez le rapport :

* models.Permissions.ReadWrite
* models.Permissions.Copy
* models.Permissions.All

> [!NOTE]
> Votre jeton d’accès a également besoin des étendues appropriées. Pour plus d’informations, consultez la page [Étendues](power-bi-embedded-app-token-flow.md#scopes).

## <a name="embed-report-in-edit-mode"></a>Incorporer un rapport en mode Édition

Supposons que vous souhaitez incorporer un rapport en mode Édition à l’intérieur de votre application : pour cela, transmettez simplement les propriétés adéquates dans la configuration d’incorporation et appelez powerbi.embed(). Vous devrez fournir les autorisations et un mode Affichage pour voir les boutons « enregistrer » et « enregistrer sous » en mode Édition. Pour plus d’informations, consultez la page [Détails de la configuration d’incorporation](https://github.com/Microsoft/PowerBI-JavaScript/wiki/Embed-Configuration-Details).

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
        permissions: models.Permissions.All /*both save & save as buttons will be visible*/,
        viewMode: models.ViewMode.Edit,
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

Un rapport sera alors incorporé dans votre application en mode Édition.

## <a name="save-report"></a>Enregistrer le rapport

Après avoir incorporé le rapport en mode Édition avec le bon jeton et les autorisations appropriées, vous pouvez enregistrer le rapport dans le menu Fichier ou à partir de JavaScript :

```
 // Get a reference to the embedded report.
    report = powerbi.get(reportContainer);

 // Save report
    report.save();
```

## <a name="save-as"></a>Enregistrer sous

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
> Le nouveau rapport n’est créé qu’après l’opération *enregistrer sous*. Après l’enregistrement, le canevas affiche toujours l’ancien rapport en mode Édition, et non le nouveau rapport. Vous devrez incorporer le nouveau rapport qui a été créé. Cela nécessite un nouveau jeton d’accès, car ils sont créés rapport par rapport.

Vous devrez ensuite charger le nouveau rapport après une opération *enregistrer sous*. Cette opération est semblable à l’incorporation de tout autre rapport.

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

## <a name="see-also"></a>Voir aussi

[Prise en main de l’exemple](power-bi-embedded-get-started-sample.md)  
[Incorporer un rapport](power-bi-embedded-embed-report.md)  
[Créer un rapport à partir d’un jeu de données](power-bi-embedded-create-report-from-dataset.md)  
[Authentification et autorisation dans Power BI Embedded](power-bi-embedded-app-token-flow.md)  
[Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-get-the-desktop/)  
[Exemple d’incorporation JavaScript](https://microsoft.github.io/PowerBI-JavaScript/demo/)  
Des questions ? [Essayer la communauté Power BI](http://community.powerbi.com/)


