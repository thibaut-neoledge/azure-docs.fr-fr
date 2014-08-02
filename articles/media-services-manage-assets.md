<properties linkid="develop-media-services-how-to-guides-manage-assets" urlDisplayName="Manage Assets in Media Services" pageTitle="How to Manage Assets in Media Services - Azure" metaKeywords="" description="Learn how to manage assets on Media Services. You can also manage jobs, tasks, access policies, locators, and more. Code samples are written in C# and use the Media Services SDK for .NET." metaCanonical="" services="media-services" documentationCenter="" title="How to: Manage Assets in storage" authors="migree" solutions="" manager="" editor="" />

Gestion des éléments multimédias dans le stockage
=================================================

Cet article fait partie d'une série qui présente la programmation Azure Media Services. La rubrique précédente s'intitulait [Protection d'un élément multimédia](http://go.microsoft.com/fwlink/?LinkID=301813&clcid=0x409).

Une fois que vous avez créé des éléments multimédias et que vous les avez téléchargés dans Media Services, vous pouvez y accéder et les gérer sur le serveur. Vous pouvez également gérer sur le serveur d'autres objets faisant partie de Media Services, entre autres, des travaux, des tâches, des stratégies d'accès et des localisateurs.

L'exemple suivant montre comment lancer une requête pour obtenir un élément multimédia par assetId.

<pre><code>
static IAsset GetAsset(string assetId)
{
    // Utilisez une requête LINQ Select pour obtenir un élément multimédia.
    var assetInstance =
        from a in _context.Assets
        where a.Id == assetId
        select a;
    // Référencez l'élément multimédia en tant qu'IAsset.
    IAsset asset = assetInstance.FirstOrDefault();

    return asset;
}
</code></pre>

Pour répertorier tous les éléments multimédias disponibles sur le serveur, vous pouvez utiliser la méthode suivante, qui effectue une itération dans la collection d'éléments multimédias et affiche des informations détaillées sur chaque élément.
<pre><code>
static void ListAssets()
{
    string waitMessage = "Building the list. This may take a few "
        + "seconds to a few minutes depending on how many assets "
        + "you have."
        + Environment.NewLine + Environment.NewLine
        + "Please wait..."
        + Environment.NewLine;
    Console.Write(waitMessage);

    // Créez un Stringbuilder pour stocker la liste que nous créons. 
    StringBuilder builder = new StringBuilder();

    foreach (IAsset asset in _context.Assets)
    {
        // Affichez la collection d'éléments multimédias.
        builder.AppendLine("");
        builder.AppendLine("******ASSET******");
        builder.AppendLine("Asset ID: " + asset.Id);
        builder.AppendLine("Name: " + asset.Name);
        builder.AppendLine("==============");
        builder.AppendLine("******ASSET FILES******");

        // Affichez les fichiers associés à chaque élément multimédia. 
        foreach (IAssetFile fileItem in asset.AssetFiles)
        {
            builder.AppendLine("Name: " + fileItem.Name);
            builder.AppendLine("Size: " + fileItem.ContentFileSize);
            builder.AppendLine("==============");
        }
    }

    // Affichez la sortie sur la console.
    Console.Write(builder.ToString());
}
</code></pre>
L'extrait de code suivant supprime tous les éléments multimédias du compte Media Services.
<pre><code>
foreach (IAsset asset in _context.Assets)
{
    asset.Delete();
}
</code></pre>

Pour plus d'informations sur la gestion des éléments multimédias, consultez les pages suivantes :

-   [Gestion des éléments multimédias avec le Kit de développement logiciel (SDK) Media Services pour .NET](http://msdn.microsoft.com/en-us/library/jj129589.aspx)
-   [Gestion des éléments multimédias avec l'API REST de Media Services](http://msdn.microsoft.com/en-us/library/jj129583.aspx)

Étapes suivantes
----------------

Maintenant que vous savez comment gérer les éléments multimédias, passez à la rubrique [Fourniture d'un élément multimédia par téléchargement](http://go.microsoft.com/fwlink/?LinkID=301734&clcid=0x409).

