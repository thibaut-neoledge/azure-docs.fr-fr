---
title: "Enregistrer des rapports dans les collections d’espaces de travail Power BI | Microsoft Docs"
description: "Découvrez comment enregistrer des rapports dans les collections d’espaces de travail Power BI. Cela requiert les autorisations appropriées pour fonctionner correctement."
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
ms.openlocfilehash: 94a72ba4478aa317b4a1930b2894c1346d0590c1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="save-reports-in-power-bi-workspace-collections"></a>Enregistrer des rapports dans les collections d’espaces de travail Power BI

Découvrez comment enregistrer des rapports dans les collections d’espaces de travail Power BI. L’enregistrement de rapports requiert les autorisations appropriées pour fonctionner correctement.

> [!IMPORTANT]
> Les collections d’espaces de travail Power BI sont déconseillées et disponibles jusqu’en juin 2018 ou jusqu’à la date indiquée sur votre contrat. Nous vous conseillons de planifier votre migration vers Power BI Embedded pour éviter toute interruption dans votre application. Pour plus d’informations sur la migration de vos données vers Power BI Embedded, consultez l’article [How to migrate Power BI Workspace Collections content to Power BI Embedded (Migration du contenu de collections d’espaces de travail Power BI vers Power BI Embedded)](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/).

Dans les collections d’espaces de travail Power BI, vous pouvez modifier des rapports existants et les enregistrer. Vous pouvez également créer un nouveau rapport ou enregistrer un rapport sous un nouveau rapport.

Pour enregistrer un rapport, vous devez commencer par créer un jeton pour le rapport en question avec les étendues adaptées :

* Pour activer « enregistrer », l’étendue Report.ReadWrite est requise.
* Pour activer « enregistrer sous », les étendues Report.Read et Workspace.Report.Copy sont requises.
* L’activation des fonctions « enregistrer » et « enregistrer sous » requiert Report.ReadWrite et Workspace.Report.Copy.

Pour activer les boutons « enregistrer » / « enregistrer sous » adéquats dans le menu Fichier, vous devez fournir les autorisations appropriées dans la configuration d’incorporation lorsque vous incorporez le rapport :

* models.Permissions.ReadWrite
* models.Permissions.Copy
* models.Permissions.All

> [!NOTE]
> Votre jeton d’accès a également besoin des étendues appropriées. Pour plus d’informations, consultez la page [Étendues](app-token-flow.md#scopes).

## <a name="embed-report-in-edit-mode"></a>Incorporer un rapport en mode Édition

Supposons que vous souhaitez incorporer un rapport en mode Édition à l’intérieur de votre application : pour cela, transmettez simplement les propriétés adéquates dans la configuration d’incorporation et appelez powerbi.embed(). Fournissez les autorisations et un mode d’affichage pour voir les boutons « enregistrer » et « enregistrer sous » en mode d’édition. Pour plus d’informations, consultez la page [Détails de la configuration d’incorporation](https://github.com/Microsoft/PowerBI-JavaScript/wiki/Embed-Configuration-Details).

Par exemple, dans JavaScript :

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

Un rapport est désormais incorporé dans votre application en mode d’édition.

## <a name="save-report"></a>Enregistrer le rapport

Après avoir incorporé le rapport en mode d’édition avec le bon jeton et les autorisations appropriées, vous pouvez enregistrer le rapport à partir du menu Fichier ou de JavaScript :

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
> Le nouveau rapport n’est créé qu’après l’opération *enregistrer sous*. Après l’enregistrement, le canevas affiche toujours l’ancien rapport en mode Édition, et non le nouveau rapport. Incorporez le rapport nouvellement créé. L’incorporation du nouveau rapport nécessite un nouveau jeton d’accès, car ces jetons sont créés rapport par rapport.

Vous devrez ensuite charger le nouveau rapport après une opération *enregistrer sous*. Le chargement du nouveau rapport est semblable à l’incorporation de tout autre rapport.

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

[Prise en main de l’exemple](get-started-sample.md)  
[Incorporer un rapport](embed-report.md)  
[Créer un rapport à partir d’un jeu de données](create-report-from-dataset.md)  
[Authentification et autorisation dans les collections d’espaces de travail Power BI](app-token-flow.md)  
[Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-get-the-desktop/)  
[Exemple d’incorporation JavaScript](https://microsoft.github.io/PowerBI-JavaScript/demo/)  

Des questions ? [Essayer la communauté Power BI](http://community.powerbi.com/)

