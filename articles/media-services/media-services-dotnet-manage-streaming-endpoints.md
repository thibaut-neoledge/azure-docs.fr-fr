---
title: "Gérer les points de terminaison de streaming avec le SDK .NET. | Microsoft Docs"
description: "Cette rubrique montre comment gérer les points de terminaison de streaming avec le Portail Azure."
services: media-services
documentationcenter: 
author: Juliako
writer: juliako
manager: SyntaxC4
editor: 
ms.assetid: 0da34a97-f36c-48d0-8ea2-ec12584a2215
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/18/2017
ms.author: juliako
ms.translationtype: HT
ms.sourcegitcommit: 26c07d30f9166e0e52cb396cdd0576530939e442
ms.openlocfilehash: 2aecdd9337a74a27c8116ee14efac8103b3eb548
ms.contentlocale: fr-fr
ms.lasthandoff: 07/19/2017

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

## <a name="create-and-configure-a-visual-studio-project"></a>Créer et configurer un projet Visual Studio

Configurez votre environnement de développement et ajoutez des informations de connexion au fichier app.config selon la procédure décrite dans l’article [Développement Media Services avec .NET](media-services-dotnet-how-to-use.md). 

## <a name="add-code-that-manages-streaming-endpoints"></a>Ajouter le code qui gère les points de terminaison de streaming
    
Remplacez le contenu de Program.cs par le code suivant :

    using System;
    using System.Configuration;
    using System.Linq;
    using Microsoft.WindowsAzure.MediaServices.Client;
    using Microsoft.WindowsAzure.MediaServices.Client.Live;

    namespace AMSStreamingEndpoint
    {
        class Program
        {
        // Read values from the App.config file.
        private static readonly string _AADTenantDomain =
        ConfigurationManager.AppSettings["AADTenantDomain"];
        private static readonly string _RESTAPIEndpoint =
        ConfigurationManager.AppSettings["MediaServiceRESTAPIEndpoint"];

        private static CloudMediaContext _context = null;

        static void Main(string[] args)
        {
            var tokenCredentials = new AzureAdTokenCredentials(_AADTenantDomain, AzureEnvironments.AzureCloudEnvironment);
            var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

            _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);

            var defaultStreamingEndpoint = _context.StreamingEndpoints.Where(s => s.Name.Contains("default")).FirstOrDefault();
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
            if (streamingEndpoint.StreamingEndpointVersion == "1.0")
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


