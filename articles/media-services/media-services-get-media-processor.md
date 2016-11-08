---
title: Création d'un processeur multimédia | Microsoft Docs
description: Apprenez à créer un composant processeur multimédia pour encoder, chiffrer ou déchiffrer un contenu multimédia, ou convertir son format pour Azure Media Services. Les exemples de code sont écrits en C# et utilisent le Kit de développement logiciel (SDK) Media Services pour .NET.
services: media-services
documentationcenter: ''
author: juliako
manager: erikre
editor: ''

ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/26/2016
ms.author: juliako

---
# <a name="how-to:-get-a-media-processor-instance"></a>Obtention d’une instance de processeur multimédia
> [!div class="op_single_selector"]
> * [.NET](media-services-get-media-processor.md)
> * [REST](media-services-rest-get-media-processor.md)
> 
> 

## <a name="overview"></a>Vue d'ensemble
Dans Media Services, un processeur multimédia est un composant qui gère une tâche de traitement spécifique, telle que l’encodage, la conversion de format, le chiffrement ou le déchiffrement de contenu multimédia. Le plus souvent, vous devez créer un processeur multimédia lorsque vous créez une tâche visant à encoder, à chiffrer ou à convertir le format du contenu multimédia.

Le tableau suivant vous indique le nom et la description de chaque processeur multimédia disponible.

| Nom du processeur multimédia | Description | Informations complémentaires |
| --- | --- | --- |
| Media Encoder Standard |Fournit des fonctionnalités standard pour l’encodage à la demande. |[Vue d’ensemble et comparaison d’encodeurs multimédia à la demande Azure](media-services-encode-asset.md) |
| Media Encoder Premium Workflow |Permet d’exécuter des tâches d’encodage à l’aide de Media Encoder Premium Workflow. |[Vue d’ensemble et comparaison d’encodeurs multimédia à la demande Azure](media-services-encode-asset.md) |
| Azure Media Indexer |Vous permet de faire en sorte que les fichiers et le contenu multimédias puissent faire l'objet d'une recherche, et de générer des pistes de sous-titrage et des mots clés. |[Azure Media Indexer](media-services-index-content.md) |
| Azure Media Hyperlapse (version préliminaire) |Permet de lisser les « dégradations » dans votre vidéo avec une stabilisation vidéo. Permet également d’accélérer votre contenu dans un clip consommable. |[Azure Media Hyperlapse](media-services-hyperlapse-content.md) |
| Encodeur multimédia Azure |Amorti | |
| Storage Decryption |Amorti | |
| Azure Media Packager |Amorti | |
| Azure Media Encryptor |Amorti | |

## <a name="get-media-processor"></a>Obtention d'un processeur multimédia
La méthode suivante montre comment obtenir une instance de processeur multimédia. L’exemple de code implique l’utilisation d’une variable au niveau du module, nommée **_context**, pour conserver une référence au contexte, tel que décrit dans la section [Comment : se connecter à Media Services par programmation](media-services-dotnet-connect-programmatically.md).

    private static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
    {
        var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
        ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();

        if (processor == null)
        throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));

        return processor;
    }


## <a name="media-services-learning-paths"></a>Parcours d’apprentissage de Media Services
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fournir des commentaires
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Étapes suivantes
Maintenant que vous savez comment obtenir une instance de processeur multimédia, consultez la rubrique [Encodage d’un élément multimédia](media-services-dotnet-encode-with-media-encoder-standard.md) pour savoir comment utiliser Media Encoder Standard afin d’encoder un élément multimédia.

<!--HONumber=Oct16_HO2-->


