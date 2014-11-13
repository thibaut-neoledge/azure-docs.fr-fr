<properties urlDisplayName="Deliver Apple HTTP Live Streaming (HLS)" pageTitle="Remise de contenu de diffusion en continu HTTP (HLS) Apple - Azure" metaKeywords="" description="Apprenez &agrave; cr&eacute;er un localisateur renvoyant vers du contenu de diffusion en continu HTTP (HLS) Apple sur un serveur d'origine Media Services. Les exemples de code sont &eacute;crits en C# et utilisent le Kit de d&eacute;veloppement logiciel (SDK) Media Services pour .NET." metaCanonical="" services="media-services" documentationCenter="" title="Utilisation de contenu de diffusion en continu HLS Apple" authors="juliako" solutions="" manager="dwrede" editor="" />

<tags ms.service="media-services" ms.workload="media" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="juliako" />

# Utilisation de contenu de diffusion en continu HLS Apple

Cet article fait partie d'une série qui présente la programmation Azure Media Services. La rubrique précédente s'intitulait [Fourniture de contenu de diffusion en continu][Fourniture de contenu de diffusion en continu].

Cette rubrique décrit la création d'un localisateur renvoyant vers du contenu de diffusion en continu HTTP (HLS) Apple sur un serveur d'origine Media Services. Cette méthode vous permet de créer une URL renvoyant vers du contenu de diffusion en continu HTTP (HLS) Apple qui permettra la lecture de contenu en continu sur des appareils iOS Apple. Le principe de base pour la création de l'URL du localisateur est identique. Générez un localisateur renvoyant vers le chemin d'accès de l'élément multimédia de diffusion en continu HTTP (HLS) Apple sur un serveur d'origine, puis créez une URL complète renvoyant vers le fichier manifeste du contenu de diffusion en continu.

L'exemple de code suivant part du principe que vous avez déjà obtenu une référence à un élément de diffusion en continu HLS et que la variable nommée **assetToStream** est référencée dans le code. Après que vous avez exécuté ce code pour générer un localisateur d'origine pour l'élément multimédia, vous pouvez utiliser l'URL créée pour lire le contenu de diffusion en continu sur un appareil iOS, par exemple un iPad ou un iPhone.

Pour générer un localisateur renvoyant vers du contenu de diffusion en continu HLS Apple :

1.  Obtenez une référence au fichier manifeste dans l'élément de diffusion en continu.
2.  Définissez une stratégie d'accès.
3.  Créez le localisateur d'origine en appelant la méthode CreateLocator.
4.  Générez une URL vers le fichier de manifeste.

Le code suivant montre comment implémenter les étapes :

    static ILocator GetStreamingHLSOriginLocator( string targetAssetID)
    {
        // Get a reference to the asset you want to stream.
        IAsset assetToStream = GetAsset(targetAssetID);

        // Get a reference to the HLS streaming manifest file from the  
        // collection of files in the asset. 
        var theManifest =
                            from f in assetToStream.AssetFiles
                            where f.Name.EndsWith(".ism")
                            select f;

        // Cast the reference to a true IAssetFile type. 
        IAssetFile manifestFile = theManifest.First();
        IAccessPolicy policy = null;
        ILocator originLocator = null;

        // Create a 30-day readonly access policy. 
        policy = _context.AccessPolicies.Create("Streaming HLS Policy",
            TimeSpan.FromDays(30),
            AccessPermissions.Read);

        originLocator = _context.Locators.CreateLocator(LocatorType.OnDemandOrigin, assetToStream,
                    policy,
                    DateTime.UtcNow.AddMinutes(-5));

        // Create a URL to the HLS streaming manifest file. Use this for playback
        // in Apple iOS streaming clients.
        string urlForClientStreaming = originLocator.Path
            + manifestFile.Name + "/manifest(format=m3u8-aapl)";
        Console.WriteLine("URL to manifest for client streaming: ");
        Console.WriteLine(urlForClientStreaming);
        Console.WriteLine();

        // Return the locator. 
        return originLocator;
    }

Pour plus d'informations sur la fourniture des éléments multimédias, consultez les pages suivantes :

-   [Fourniture des éléments multimédias avec Media Services pour .NET][Fourniture des éléments multimédias avec Media Services pour .NET]
-   [Fourniture des éléments multimédias avec l'API REST de Media Services][Fourniture des éléments multimédias avec l'API REST de Media Services]

</p>
## Étapes suivantes

Cette rubrique est la dernière consacrée à l'utilisation d'Azure Media Services. Nous avons abordé la configuration de votre ordinateur pour le développement Media Services et l'exécution de tâches de programmation classiques. Pour plus d'informations sur la programmation Media Services, consultez les ressources suivantes :

-   [Documentation Azure Media Services][Documentation Azure Media Services]
-   [Prise en main du Kit de développement logiciel (SDK) Media Services pour .NET][Prise en main du Kit de développement logiciel (SDK) Media Services pour .NET]
-   [Création d'applications avec le Kit de développement logiciel (SDK) Media Services pour .NET][Création d'applications avec le Kit de développement logiciel (SDK) Media Services pour .NET]
-   [Création d'applications avec l'API REST d'Azure Media Services][Création d'applications avec l'API REST d'Azure Media Services]
-   [Forum Media Services][Forum Media Services]
-   [Surveillance d'un compte Media Services][Surveillance d'un compte Media Services]
-   [Gestion du contenu dans Media Services][Gestion du contenu dans Media Services]

  [Fourniture de contenu de diffusion en continu]: ../media-services-deliver-streaming-content/
  [Fourniture des éléments multimédias avec Media Services pour .NET]: http://msdn.microsoft.com/fr-fr/library/jj129575.aspx
  [Fourniture des éléments multimédias avec l'API REST de Media Services]: http://msdn.microsoft.com/fr-fr/library/jj129578.aspx
  [Documentation Azure Media Services]: http://go.microsoft.com/fwlink/?linkid=245437
  [Prise en main du Kit de développement logiciel (SDK) Media Services pour .NET]: http://go.microsoft.com/fwlink/?linkid=252966
  [Création d'applications avec le Kit de développement logiciel (SDK) Media Services pour .NET]: http://go.microsoft.com/fwlink/?linkid=247821
  [Création d'applications avec l'API REST d'Azure Media Services]: http://go.microsoft.com/fwlink/?linkid=252967
  [Forum Media Services]: http://social.msdn.microsoft.com/Forums/en-US/MediaServices/threads
  [Surveillance d'un compte Media Services]: ../media-services-monitor-services-account/
  [Gestion du contenu dans Media Services]: ../media-services-manage-content/
