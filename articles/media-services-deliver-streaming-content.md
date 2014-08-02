<properties linkid="develop-media-services-how-to-guides-deliver-streaming-content" urlDisplayName="Deliver Streaming Content from Media Services" pageTitle="How to Deliver Streaming Content from Media Services – Azure" metaKeywords="" description="Learn how to deliver streaming content from Media Services using a direct URL. Code samples are written in C# and use the Media Services SDK for .NET." metaCanonical="" disqusComments="1" umbracoNaviHide="0" title="How to: Deliver streaming content" authors="" />

Fourniture de diffusion de contenu en continu
=============================================

Cet article fait partie d'une série qui présente la programmation Azure Media Services. La rubrique précédente s'intitulait [Fourniture d'un élément multimédia par téléchargement](http://go.microsoft.com/fwlink/?LinkID=301734&clcid=0x409).

En plus de télécharger du contenu multimédia à partir de Media Services, vous pouvez utiliser la diffusion en continu à débit adaptatif pour fournir du contenu. Par exemple, vous pouvez créer une URL directe, appelée un localisateur, pour la diffusion de contenu en continu sur un serveur d'origine Media Services. Les applications clientes comme Microsoft Silverlight peuvent lire le contenu en continu directement à partir du localisateur.

L'exemple suivant montre comment créer un localisateur d'origine pour un élément multimédia de sortie produit par une tâche. L'exemple part du principe que vous avez déjà obtenu une référence à un élément multimédia qui contient des fichiers de diffusion en continu lisse et que la variable nommée **assetToStream** est référencée dans le code.

Pour créer un localisateur d'origine pour la diffusion de contenu en continu :

1.  Obtenez une référence au fichier manifeste de diffusion en continu (.ism) dans l'élément multimédia.
2.  Définissez une stratégie d'accès.
3.  Créez le localisateur d'origine en appelant la méthode CreateLocator.
4.  Générez une URL vers le fichier de manifeste.

Le code suivant montre comment implémenter les étapes :

``` {}
private static ILocator GetStreamingOriginLocator( string targetAssetID)
{
    // Obtenez une référence à l'élément multimédia que vous souhaitez transmettre en continu.
    IAsset assetToStream = GetAsset(targetAssetID);

    // Obtenez une référence au fichier manifeste de diffusion en continu à partir
    // de la collection de fichiers dans l'élément multimédia. 
    var theManifest =
                        from f in assetToStream.AssetFiles
                        where f.Name.EndsWith(".ism")
                        select f;

    // Effectuez une conversion de type de la référence en vrai type IAssetFile. 
    IAssetFile manifestFile = theManifest.First();
    IAccessPolicy policy = null;
    ILocator originLocator = null;

    // Créez une stratégie d'accès en lecture seule de 30 jours. 
    policy = _context.AccessPolicies.Create("Streaming policy",
        TimeSpan.FromDays(30),
        AccessPermissions.Read);

    // Créez un localisateur OnDemandOrigin à l'élément multimédia. 
    originLocator = _context.Locators.CreateLocator(LocatorType.OnDemandOrigin, assetToStream,
        policy,
        DateTime.UtcNow.AddMinutes(-5));

    // Affichez des valeurs utiles sur le localisateur.
    Console.WriteLine("Streaming asset base path on origin: ");
    Console.WriteLine(originLocator.Path);
    Console.WriteLine();

    // Créez une URL complète au fichier de manifeste. Utilisez-la pour la lecture
    // dans les clients pour la diffusion des flux. 
    string urlForClientStreaming = originLocator.Path + manifestFile.Name + "/manifest";
    Console.WriteLine("URL to manifest for client streaming: ");
    Console.WriteLine(urlForClientStreaming);
    Console.WriteLine();

    // Affichez l'ID du localisateur d'origine, de la stratégie d'accès et de l'élément multimédia.
    Console.WriteLine("Origin locator Id: " + originLocator.Id);
    Console.WriteLine("Access policy Id: " + policy.Id);
    Console.WriteLine("Streaming asset Id: " + assetToStream.Id);

    // Renvoyez le localisateur. 
    return originLocator;
}
```

Pour plus d'informations sur la fourniture des éléments multimédias, consultez les pages suivantes :

-   [Fourniture des éléments multimédias avec Media Services pour .NET](http://msdn.microsoft.com/en-us/library/jj129575.aspx)
-   [Fourniture des éléments multimédias avec l'API REST de Media Services](http://msdn.microsoft.com/en-us/library/jj129578.aspx)

Étapes suivantes
----------------

Jusqu'à présent, nous avons traité la fourniture d'éléments multimédias par le téléchargement à partir d'Azure Storage et à l'aide de la diffusion en continu lisse. La rubrique suivante, intitulée [Fourniture de contenu HLS](http://go.microsoft.com/fwlink/?LinkId=301817), aborde la fourniture de diffusion de contenu en continu à l'aide de la diffusion en continu HTTP Apple (HLS).

