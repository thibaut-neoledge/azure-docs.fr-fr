---
title: "Développement de fonctions Azure Functions avec Media Services"
description: "Cette rubrique montre comment développer des fonctions Azure Functions avec Media Services à l’aide du portail Azure."
services: media-services
documentationcenter: 
author: juliako
manager: erikre
editor: 
ms.assetid: 51bdcb01-1846-4e1f-bd90-70020ab471b0
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/13/2017
ms.author: juliako
translationtype: Human Translation
ms.sourcegitcommit: a087df444c5c88ee1dbcf8eb18abf883549a9024
ms.openlocfilehash: b077504e7a289fd4976aa22bab0ad5784ffb491b
ms.lasthandoff: 03/15/2017


---
#<a name="develop-azure-functions-with-media-services"></a>Développement de fonctions Azure Functions avec Media Services
[!INCLUDE [media-services-selector-setup](../../includes/media-services-selector-setup.md)]

Cette rubrique explique comment développer des fonctions Azure Functions avec Media Services à l’aide du portail Azure. 

Vous pouvez également déployer des fonctions Media Services Azure Functions existantes à partir de [cette page](https://github.com/Azure-Samples/media-services-dotnet-functions-integration), en cliquant sur le bouton **Déployer sur Azure**. Ce référentiel contient des exemples Azure Functions qui utilisent Azure Media Services pour afficher les flux de travail liés à l’ingestion de contenu directement à partir du Stockage Blob, à l’encodage et à l’écriture de contenu dans le Stockage Blob. Il inclut également des exemples décrivant la manière de contrôler les notifications de travail via les WebHooks et les files d’attente Azure.

Vous pouvez développer vos fonctions à partir des exemples donnés dans [ce référentiel](https://github.com/Azure-Samples/media-services-dotnet-Functions-integration). Cette rubrique vous montre comment vous familiariser avec la création de fonctions Azure Functions qui utilisent Media Services. 

## <a name="prerequisites"></a>Composants requis

Pour créer votre première fonction, vous devez avoir un compte Azure actif. Si tel n’est pas le cas, des [comptes gratuits sont disponibles](https://azure.microsoft.com/free/).

Si vous souhaitez créer des fonctions Azure Functions qui effectuent des actions sur votre compte Azure Media Services (AMS) ou qui écoutent des événements envoyés par Media Services, vous devez créer un compte AMS, comme décrit [ici](media-services-portal-create-account.md).

## <a name="create-a-function-app"></a>Créer une Function App

Suivez [cette procédure](../azure-functions/functions-create-first-azure-function-azure-portal.md#create-a-function-app) pour créer une Function App.

## <a name="create-a-function"></a>Créer une fonction

Une fois votre Function App déployée, vous pouvez la retrouver parmi les fonctions Azure Functions **App Services**. 

1. Sélectionnez votre Function App et cliquez sur **Nouvelle fonction**.
3. Choisissez le langage **C#** et le scénario **Webhook + API**.
3. Sélectionnez **GenericWebHook-CSharp** (qui s’exécutera chaque fois qu’il recevra une demande du webhook) ou **HttpTrigger-CSharp** (qui s’exécutera chaque fois qu’il recevra une requête HTTP) et donnez un nom à votre fonction.
4. Cliquez sur **Create**. 

## <a name="get-function-url"></a>Obtenir l’URL de la fonction

Pour déclencher l’exécution de vos fonctions à partir d’un outil de test HTTP ou d’une autre fenêtre de navigateur, vous avez besoin de la valeur URL de la fonction. 

![Paramètres](./media/media-services-azure-functions/media-services-azure-functions002.png)

## <a name="files"></a>Fichiers

Votre fonction Azure est associée à des fichiers de code et à d’autres fichiers décrits dans cette section. Par défaut, une fonction est associée à des fichiers **function.json** et **run.csx**. Vous devez ajouter un fichier **project.json**. Le reste de cette section présente les définitions de ces fichiers.

![fichiers d'entrée](./media/media-services-azure-functions/media-services-azure-functions003.png)

### <a name="functionjson"></a>function.json

Le fichier function.json définit les liaisons de fonction et d’autres paramètres de configuration. Le runtime utilise ce fichier pour déterminer les événements à surveiller et comment passer des données et renvoyer des données à partir de l’exécution de la fonction. 

Voici un exemple de fichier **function.json**.

    {
      "bindings": [
        {
          "type": "httpTrigger",
          "name": "req",
          "direction": "in",
          "methods": [
        "post",
        "get",
        "put",
        "update",
        "patch"
          ]
        },
        {
          "type": "http",
          "name": "res",
          "direction": "out"
        }
      ]
    }
    
### <a name="projectjson"></a>project.json

Le fichier project.json contient des dépendances. Voici un exemple de fichier **function.json** qui inclut des bibliothèques AMS.

    {
      "frameworks": {
        "net46":{
          "dependencies": {
        "windowsazure.mediaservices": "3.8.0.5",
        "windowsazure.mediaservices.extensions": "3.8.0.3"
          }
        }
       }
    }
    
### <a name="runcsx"></a>run.csx

Il s’agit du code C# de votre fonction. Pour obtenir un exemple de fonction webhook, consultez [cette rubrique](media-services-dotnet-check-job-progress-with-webhooks.md). 

Une fois que vous avez fini de définir votre fonction, cliquez sur **Exécuter**.
    
    ///////////////////////////////////////////////////
    #r "Newtonsoft.Json"
    
    using System;
    using Microsoft.WindowsAzure.MediaServices.Client;
    using System.Collections.Generic;
    using System.Linq;
    using System.Text;
    using System.Threading;
    using System.Threading.Tasks;
    using System.IO;
    using System.Globalization;
    using Newtonsoft.Json;
    using Microsoft.Azure;
    using System.Net;
    using System.Security.Cryptography;
    

    static string _mediaServicesAccountName = Environment.GetEnvironmentVariable("AMSAccount");
    static string _mediaServicesAccountKey = Environment.GetEnvironmentVariable("AMSKey");
    
    static CloudMediaContext _context = null;
    
    public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
    {
        log.Info($"C# HTTP trigger function processed a request. RequestUri={req.RequestUri}");
    
        Task<byte[]> taskForRequestBody = req.Content.ReadAsByteArrayAsync();
        byte[] requestBody = await taskForRequestBody;
    
        string jsonContent = await req.Content.ReadAsStringAsync();
        log.Info($"Request Body = {jsonContent}");
    
        // some valication code ...

        _context = new CloudMediaContext(new MediaServicesCredentials(
        _mediaServicesAccountName,
        _mediaServicesAccountKey));

        // some AMS operations ...
      
        return req.CreateResponse(HttpStatusCode.BadRequest, "Generic Error.");
    }



## <a name="configure-function-app-settings"></a>Configuration des paramètres Function App

Lorsque vous développez des fonctions Media Services, il est utile d’ajouter à la section **Paramètres de l’application** les paramètres qui seront utilisés dans toutes vos fonctions. 

Par exemple :

![Paramètres](./media/media-services-azure-functions/media-services-azure-functions001.png)


## <a name="next-step"></a>Étape suivante

À ce stade, vous êtes prêt à commencer le développement d’une application Media Services. Pour plus d’informations, consultez [Utiliser Azure WebHooks pour surveiller les notifications de travaux Media Services avec .NET](media-services-dotnet-check-job-progress-with-webhooks.md).   

## <a name="media-services-learning-paths"></a>Parcours d’apprentissage de Media Services
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fournir des commentaires
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


