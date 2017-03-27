---
title: Incorporer un rapport dans Azure Power BI Embedded | Microsoft Docs
description: "Découvrez comment incorporer un rapport de Power BI Embedded dans votre application."
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
ms.openlocfilehash: 3d865af2418c9c557c861a379766a125d75cebf1
ms.lasthandoff: 03/14/2017


---
# <a name="embed-a-report-in-power-bi-embedded"></a>Incorporer un rapport dans Power BI Embedded

Découvrez comment incorporer un rapport de Power BI Embedded dans votre application.

Nous allons étudier comment incorporer réellement un rapport dans votre application. Cela suppose que vous disposiez déjà d’un rapport dans l’un des espaces de travail de votre collection. Si vous n’avez pas encore effectué cette étape, consultez la page [Prise en main de Power BI Embedded](power-bi-embedded-get-started.md).

Vous pouvez utiliser le Kit de développement logiciel (SDK) .NET (C#) ou Node.js, ainsi que JavaScript, pour créer facilement votre application avec Power BI Embedded. 

## <a name="using-the-access-keys-to-use-rest-apis"></a>Utiliser les clés d’accès pour utiliser les API REST

Pour appeler l’API REST, vous pouvez transmettre la clé d’accès qui peut être récupérée sur le Portail Azure pour une collection d’espaces de travail donnée. Pour plus d’informations, consultez la page [Prise en main de Power BI Embedded](power-bi-embedded-get-started.md).

## <a name="get-a-report-id"></a>Obtenir un ID de rapport

Chaque jeton d’accès est basé sur un rapport. Vous devrez obtenir l’ID du rapport que vous souhaitez incorporer. Vous pouvez pour cela utiliser des appels à l’API REST [Obtenir des rapports](https://msdn.microsoft.com/library/azure/mt711510.aspx). Elle renvoie l’ID de rapport et l’URL d’incorporation. Vous pouvez pour cela utiliser le Kit SDK .NET Power BI ou appeler directement l’API REST.

### <a name="using-the-power-bi-net-sdk"></a>Utiliser le Kit SDK .NET Power BI

Si vous utilisez le Kit SDK .NET, vous devrez créer des informations d’identification de jeton basées sur la clé d’accès récupérée sur le Portail Azure. Cela requiert l’installation du [package NuGet Power BI API](https://www.nuget.org/profiles/powerbi).

**Installation du package NuGet**

```
Install-Package Microsoft.PowerBI.Api
```

**Code C#**

```
using Microsoft.PowerBI.Api.V1;
using Microsoft.Rest;

var credentials = new TokenCredentials("{access key}", "AppKey");
var client = new PowerBIClient(credentials);
client.BaseUri = new Uri(https://api.powerbi.com);

var reports = (IList<Report>)client.Reports.GetReports(workspaceCollectionName, workspaceId).Value;

// Select the report that you want to work with from the collection of reports.
```

### <a name="calling-the-rest-api-directly"></a>Appeler directement l’API REST

```
System.Net.WebRequest request = System.Net.WebRequest.Create("https://api.powerbi.com/v1.0/collections/{collectionName}/workspaces/{workspaceId}/Reports") as System.Net.HttpWebRequest;

request.Method = "GET";
request.ContentLength = 0;
request.Headers.Add("Authorization", String.Format("AppKey {0}", accessToken.Value));

using (var response = request.GetResponse() as System.Net.HttpWebResponse)
{
    using (var reader = new System.IO.StreamReader(response.GetResponseStream()))
    {
        // Work with JSON response to get the report you want to work with.
    }

}
```

## <a name="create-an-access-token"></a>Créer un jeton d’accès

Power BI Embedded utilise des jetons d’incorporation, qui sont des jetons JSON Web Token signés HMAC. Les jetons sont signés avec la clé d’accès issue de votre collection d’espaces de travail Azure Power BI Embedded. Par défaut, les jetons d’incorporation sont utilisés pour fournir un accès en lecture seule à un rapport à incorporer dans une application. Les jetons d’incorporation sont émis pour un rapport donné et doivent être associés à une URL d’incorporation.

Les jetons d’accès doivent être créés sur le serveur, car les clés d’accès sont utilisées pour signer / chiffrer les jetons. Pour plus d’informations sur la création de jetons d’accès, consultez la page [Authentification et autorisation avec Power BI Embedded](power-bi-embedded-app-token-flow.md). Vous pouvez également consulter la méthode [CreateReportEmbedToken](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.powerbitoken?redirectedfrom=MSDN#methods_). Voici un exemple de ce à quoi cela ressemblerait avec le Kit de développement logiciel (SDK) .NET pour Power BI.

Vous allez utiliser l’ID de rapport que vous avez récupéré précédemment. Une fois le jeton d’incorporation créé, vous utiliserez la clé d’accès pour générer le jeton, que vous pourrez utiliser dans une perspective JavaScript. La *classe PowerBIToken* requiert l’installation du [package NuGet Power BI Core](https://www.nuget.org/packages/Microsoft.PowerBI.Core/).

**Installation du package NuGet**

```
Install-Package Microsoft.PowerBI.Core
```

**Code C#**

```
using Microsoft.PowerBI.Security;

// rlsUsername, roles and scopes are optional.
embedToken = PowerBIToken.CreateReportEmbedToken(workspaceCollectionName, workspaceId, reportId, rlsUsername, roles, scopes);

var token = embedToken.Generate("{access key}");
```

### <a name="adding-permission-scopes-to-embed-tokens"></a>Ajouter des étendues d’autorisation pour incorporer des jetons

Si vous utilisez des jetons d’incorporation, vous souhaiterez peut-être limiter l’utilisation des ressources auxquelles vous donnez accès. Dans cette optique, vous pouvez générer un jeton avec des autorisations étendues. Pour plus d’informations, consultez la page [Étendues](power-bi-embedded-app-token-flow.md#scopes).

## <a name="embed-using-javascript"></a>Incorporer avec JavaScript

Une fois que nous disposons du jeton d’accès et de l’ID du rapport, nous pouvons incorporer le rapport avec JavaScript. Cela requiert l’installation du [package NuGet Power BI JavaScript](https://www.nuget.org/packages/Microsoft.PowerBI.JavaScript/). L’URL d’incorporation est simplement https://embedded.powerbi.com/appTokenReportEmbed.

> [!NOTE]
> Vous pouvez utiliser [l’exemple d’incorporation de rapport JavaScript](https://microsoft.github.io/PowerBI-JavaScript/demo/) pour tester les fonctionnalités. Il donne également des exemples de code pour les différentes opérations disponibles.

**Installation du package NuGet**

```
Install-Package Microsoft.PowerBI.JavaScript
```

**Code JavaScript**

```
<script src="/scripts/powerbi.js"></script>
<div id="reportContainer"></div>

var embedConfiguration = {
    type: 'report',
    accessToken: 'eyJ0eXAiO...Qron7qYpY9MI',
    id: '5dac7a4a-4452-46b3-99f6-a25915e0fe55',
    embedUrl: 'https://embedded.powerbi.com/appTokenReportEmbed'
};

var $reportContainer = $('#reportContainer');
var report = powerbi.embed($reportContainer.get(0), embedConfiguration);
```

### <a name="set-the-size-of-embedded-elements"></a>Définir la taille des éléments incorporés

Le rapport sera incorporé automatiquement en fonction de la taille de son conteneur. Pour remplacer la taille par défaut des incorporations, ajoutez simplement un attribut de classe CSS ou des styles inline de largeur et de hauteur.

## <a name="see-also"></a>Voir aussi

[Prise en main de l’exemple](power-bi-embedded-get-started-sample.md)  
[Authentification et autorisation dans Power BI Embedded](power-bi-embedded-app-token-flow.md)  
[CreateReportEmbedToken](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.powerbitoken?redirectedfrom=MSDN#methods_)  
[Exemple d’incorporation JavaScript](https://microsoft.github.io/PowerBI-JavaScript/demo/)  
[Package JavaScript Power BI Core](https://www.nuget.org/packages/Microsoft.PowerBI.JavaScript/)  
[Package NuGet Power BI API](https://www.nuget.org/profiles/powerbi)
[Package NuGet Power BI Core](https://www.nuget.org/packages/Microsoft.PowerBI.Core/)  
[Référentiel Git PowerBI-CSharp](https://github.com/Microsoft/PowerBI-CSharp)  
[Référentiel Git PowerBI-Node](https://github.com/Microsoft/PowerBI-Node)  
Des questions ? [Essayer la communauté Power BI](http://community.powerbi.com/)

