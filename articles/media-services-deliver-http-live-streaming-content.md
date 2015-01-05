<properties urlDisplayName="Deliver Apple HTTP Live Streaming (HLS)" pageTitle="Remise de contenu de diffusion en continu HTTP (HLS) Apple - Azure" metaKeywords="" description="Learn how to create a locator to Apple HTTP Live Stream (HLS) content on Media Services origin server. Code samples are written in C# and use the Media Services SDK for .NET." metaCanonical="" services="media-services" documentationCenter="" title="How to: Deliver Apple HLS streaming content" authors="juliako" solutions="" manager="dwrede" editor="" />

<tags ms.service="media-services" ms.workload="media" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/30/2014" ms.author="juliako" />





<h1>Utilisation de contenu de diffusion en continu HLS Apple</h1>

Cet article fait partie d'une série qui présente la programmation Azure Media Services. La rubrique précédente s'intitulait [  Fourniture de contenu de diffusion en continu](../media-services-deliver-streaming-content/).

Cette rubrique décrit la création d'un localisateur renvoyant vers du contenu de diffusion en continu HTTP (HLS) Apple sur un serveur d'origine Media Services. Cette méthode vous permet de créer une URL renvoyant vers du contenu de diffusion en continu HTTP (HLS) Apple qui permettra la lecture de contenu en continu sur des appareils iOS Apple. Le principe de base pour la création de l'URL du localisateur est identique. Générez un localisateur renvoyant vers le chemin d'accès de l'élément multimédia de diffusion en continu HTTP (HLS) Apple sur un serveur d'origine, puis créez une URL complète renvoyant vers le fichier manifeste du contenu de diffusion en continu.

L'exemple de code suivant part du principe que vous avez déjà obtenu une référence à un élément de diffusion en continu HLS et que la variable nommée **assetToStream** est référencée dans le code. Après que vous avez exécuté ce code pour générer un localisateur d'origine pour l'élément multimédia, vous pouvez utiliser l'URL créée pour lire le contenu de diffusion en continu sur un appareil iOS, par exemple un iPad ou un iPhone.

Pour générer un localisateur renvoyant vers du contenu de diffusion en continu HLS Apple :

   1. Obtenez une référence au fichier manifeste dans l'élément de diffusion en continu.
   2. Définissez une stratégie d'accès.
   3. Créez le localisateur d'origine en appelant la méthode CreateLocator.
   4. Générez une URL vers le fichier de manifeste.

Le code suivant montre comment implémenter les étapes :

<pre><code>
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
</code></pre>

Pour plus d'informations sur la fourniture des éléments multimédias, consultez les pages suivantes :
<ul>
<li><a href="http://msdn.microsoft.com/fr-fr/library/jj129575.aspx">Fourniture des éléments multimédias avec Media Services pour .NET</a></li>
<li><a href="http://msdn.microsoft.com/fr-fr/library/jj129578.aspx">Fourniture des éléments multimédias avec l'API REST de Media Services</a></li>
</ul>

<h2>Étapes suivantes</h2>

Cette rubrique est la dernière consacrée à l'utilisation d'Azure Media Services. Nous avons abordé la configuration de votre ordinateur pour le développement Media Services et l'exécution de tâches de programmation classiques. Pour plus d'informations sur la programmation Media Services, consultez les ressources suivantes :

-   [Documentation Azure Media Services][]
-   [Prise en main du Kit de développement logiciel (SDK) Media Services pour .NET][]
-   [Création d'applications avec le Kit de développement logiciel (SDK) Media Services pour .NET][]
-   [Création d'applications avec l'API REST d'Azure Media Services][]
-   [Media Services Forum][]
-	[Surveillance d'un compte Media Services](../media-services-monitor-services-account/)
-	[Gestion du contenu dans Media Services](../media-services-manage-content/)

[Documentation Azure Media Services]: http://go.microsoft.com/fwlink/?linkid=245437
[Prise en main du Kit de développement logiciel (SDK) Media Services pour .NET]: http://go.microsoft.com/fwlink/?linkid=252966
[Création d'applications avec l'API REST d'Azure Media Services]: http://go.microsoft.com/fwlink/?linkid=252967
[Création d'applications avec le Kit de développement logiciel (SDK) Media Services pour .NET]: http://go.microsoft.com/fwlink/?linkid=247821
[Media Services Forum]: http://social.msdn.microsoft.com/Forums/en-US/MediaServices/threads

<!--HONumber=35.1-->
