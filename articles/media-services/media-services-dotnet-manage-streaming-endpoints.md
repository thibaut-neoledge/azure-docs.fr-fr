---

title: "Gérer les points de terminaison de streaming avec le SDK .NET. | Microsoft Docs"
description: "Cette rubrique montre comment gérer les points de terminaison de streaming avec le Portail Azure."
services: media-services
documentationcenter: 
author: Juliako
writer: juliako
manager: erikre
editor: 
ms.assetid: 0da34a97-f36c-48d0-8ea2-ec12584a2215
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/05/2017
ms.author: juliako
translationtype: Human Translation
ms.sourcegitcommit: 4f61f7c0fd50065d341ae1ce182a033395857579
ms.openlocfilehash: 68011ef634b1f3bdeb7c219a46e1307698a12f7e


---


# <a name="manage-streaming-endpoints-with-net-sdk"></a>Gérer les points de terminaison de streaming avec le SDK .NET

>[!NOTE]
>Consultez la rubrique de [présentation](media-services-streaming-endpoints-overview.md). Consultez aussi [StreamingEndpoint](https://docs.microsoft.com/rest/api/media/operations/streamingendpoint).

Le code de cette rubrique montre comment effectuer les tâches suivantes à l’aide du Kit de développement logiciel (SDK) Azure Media Services .NET :

- Examinez le point de terminaison de streaming par défaut.
- Créez/ajoutez un nouveau point de terminaison.

    Vous pouvez également disposer de plusieurs points de terminaison de streaming si vous prévoyez d’avoir différents CDN ou bien un CDN et un accès direct.

    > [!NOTE]
    > Vous êtes facturé uniquement lorsque votre point de terminaison de streaming est en cours d’exécution.
    
- Mettez à jour le point de terminaison de streaming.
    
    Appelez la fonction Update().

- Supprimez le point de terminaison de streaming.

    >[!NOTE]
    >Il n’est pas possible de supprimer le point de terminaison de streaming par défaut.

Pour plus d’informations sur la mise à l’échelle du point de terminaison de streaming, consultez [cette](media-services-portal-scale-streaming-endpoints.md) rubrique.


###<a name="set-up-the-visual-studio-project"></a>Créer le projet Visual Studio

1. Créez une application console C# dans Visual Studio 2015.  
2. Générez la solution.
3. Utilisez **NuGet** pour installer et ajouter la [dernière version du package du Kit de développement logiciel (SDK) .NET Azure Media Services](https://www.nuget.org/packages/windowsazure.mediaservices/).   
4. Ajoutez la section appSettings au fichier .config et mettez à jour le nom Media Services et les valeurs de clé. 
    
        <appSettings>
          <add key="MediaServicesAccountName" value="Media-Services-Account-Name"/>
          <add key="MediaServicesAccountKey" value="Media-Services-Account-Key"/>
        </appSettings>

###<a name="add-code-that-manages-streaming-endpoints"></a>Ajouter le code qui gère les points de terminaison de streaming
    
Remplacez le contenu de Program.cs par le code suivant :

    using System;
    using System.Collections.Generic;
    using System.Configuration;
    using System.Linq;
    using Microsoft.WindowsAzure.MediaServices.Client;
    using Microsoft.WindowsAzure.MediaServices.Client.Live;
    
    namespace AMSStreamingEndpoint
    {
        class Program
        {
            // Read values from the App.config file.
            private static readonly string _mediaServicesAccountName =
                ConfigurationManager.AppSettings["MediaServicesAccountName"];
            private static readonly string _mediaServicesAccountKey =
                ConfigurationManager.AppSettings["MediaServicesAccountKey"];
    
            // Field for service context.
            private static CloudMediaContext _context = null;
            private static MediaServicesCredentials _cachedCredentials = null;
    
            static void Main(string[] args)
            {
                // Create and cache the Media Services credentials in a static class variable.
                _cachedCredentials = new MediaServicesCredentials(
                                _mediaServicesAccountName,
                                _mediaServicesAccountKey);
                // Used the cached credentials to create CloudMediaContext.
                _context = new CloudMediaContext(_cachedCredentials);
    
                var defaultStreamingEndpoint = _context.StreamingEndpoints.Where(s=>s.Name.Contains("default")).FirstOrDefault();
                ExamineStreamingEndpoint(defaultStreamingEndpoint);
    
                IStreamingEndpoint newStreamingEndpoint = AddStreamingEndpoint();
                UpdateStreamingEndpoint(newStreamingEndpoint);
                DeleteStreamingEndpoint(newStreamingEndpoint);
            }
    
            static public void ExamineStreamingEndpoint(IStreamingEndpoint streamingEndpoint)
            {
                Console.WriteLine(streamingEndpoint.Name);
                Console.WriteLine(streamingEndpoint.StreamingEndpointVersion);
                Console.WriteLine(streamingEndpoint.FreeTrialEndTime);
                Console.WriteLine(streamingEndpoint.ScaleUnits);
                Console.WriteLine(streamingEndpoint.CdnProvider);
                Console.WriteLine(streamingEndpoint.CdnProfile);
                Console.WriteLine(streamingEndpoint.CdnEnabled);
            }
    
            static public IStreamingEndpoint AddStreamingEndpoint()
            {
                var name = "StreamingEndpoint" + DateTime.UtcNow.ToString("hhmmss");
                var option = new StreamingEndpointCreationOptions(name, 1)
                {
                    StreamingEndpointVersion = new Version("2.0"),
                    CdnEnabled = true,
                    CdnProfile = "CdnProfile",
                    CdnProvider = CdnProviderType.PremiumVerizon
                };
    
                var streamingEndpoint = _context.StreamingEndpoints.Create(option);
    
                return streamingEndpoint;
            }
    
            static public void UpdateStreamingEndpoint(IStreamingEndpoint streamingEndpoint)
            {
                if(streamingEndpoint.StreamingEndpointVersion == "1.0")
                    streamingEndpoint.StreamingEndpointVersion = "2.0";
    
                streamingEndpoint.CdnEnabled = false;
                streamingEndpoint.Update();
            }
    
            static public void DeleteStreamingEndpoint(IStreamingEndpoint streamingEndpoint)
            {
                streamingEndpoint.Delete();
            }
        }
    }
    

## <a name="next-steps"></a>Étapes suivantes
Consultez les parcours d’apprentissage de Media Services.

[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fournir des commentaires
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]




<!--HONumber=Jan17_HO2-->


