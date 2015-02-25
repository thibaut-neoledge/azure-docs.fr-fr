<properties pageTitle="Fourniture de contenu de diffusion en continu à partir de Media Services - Azure" description="Apprenez à fournir un contenu de diffusion en continu à partir de Media Services à l'aide d'une URL directe. Les exemples de code sont écrits en C# et utilisent le Kit de développement logiciel (SDK) Media Services pour .NET." authors="juliako" manager="dwrede" editor="" services="media-services" documentationCenter=""/>

<tags ms.service="media-services" ms.workload="media" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/30/2014" ms.author="juliako"/>


# Fourniture de diffusion de contenu en continu

Cet article fait partie d'une série qui présente la programmation Azure Media Services. La rubrique précédente s'intitulait [ Fourniture d'un élément multimédia par téléchargement](../media-services-deliver-asset-download/).

En plus de télécharger du contenu multimédia à partir de Media Services, vous pouvez utiliser la diffusion en continu à débit adaptatif pour fournir du contenu. Par exemple, vous pouvez créer une URL directe, appelée un localisateur, pour la diffusion de contenu en continu sur un serveur d'origine Media Services. Les applications clientes comme Microsoft Silverlight peuvent lire le contenu en continu directement à partir du localisateur.

L'exemple suivant montre comment créer un localisateur d'origine pour un élément multimédia de sortie produit par une tâche. L'exemple part du principe que vous avez déjà obtenu une référence à un élément multimédia qui contient des fichiers de diffusion en continu lisse et que la variable nommée **assetToStream** est référencée dans le code. 

Pour créer un localisateur d'origine pour la diffusion de contenu en continu :

   1. Obtenez une référence au fichier manifeste de diffusion en continu (.ism) dans l'élément multimédia. 
   2. Définissez une stratégie d'accès.
   3. Créez le localisateur d'origine en appelant la méthode CreateLocator. 
   4. Générez une URL vers le fichier de manifeste. 

Le code suivant montre comment implémenter les étapes :
<pre><code>
private static ILocator GetStreamingOriginLocator( string targetAssetID)
{
    // Get a reference to the asset you want to stream.
    IAsset assetToStream = GetAsset(targetAssetID);

    // Get a reference to the streaming manifest file from the  
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
    policy = _context.AccessPolicies.Create("Streaming policy",
        TimeSpan.FromDays(30),
        AccessPermissions.Read);

    // Create an OnDemandOrigin locator to the asset. 
    originLocator = _context.Locators.CreateLocator(LocatorType.OnDemandOrigin, assetToStream,
        policy,
        DateTime.UtcNow.AddMinutes(-5));
            
    // Display some useful values based on the locator.
    Console.WriteLine("Streaming asset base path on origin: ");
    Console.WriteLine(originLocator.Path);
    Console.WriteLine();
    
    // Create a full URL to the manifest file. Use this for playback
    // in streaming media clients. 
    string urlForClientStreaming = originLocator.Path + manifestFile.Name + "/manifest";
    Console.WriteLine("URL to manifest for client streaming: ");
    Console.WriteLine(urlForClientStreaming);
    Console.WriteLine();
    
    // Display the ID of the origin locator, the access policy, and the asset.
    Console.WriteLine("Origin locator Id: " + originLocator.Id);
    Console.WriteLine("Access policy Id: " + policy.Id);
    Console.WriteLine("Streaming asset Id: " + assetToStream.Id);

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
Jusqu'à présent, nous avons traité la fourniture d'éléments multimédias par le téléchargement à partir d'Azure Storage et à l'aide de la diffusion en continu lisse. La rubrique suivante, intitulée [Fourniture de contenu HLS](../media-services-deliver-http-live-streaming-content/), aborde la fourniture de diffusion de contenu en continu à l'aide de la diffusion en continu HTTP Apple (HLS).


<!--HONumber=42-->
