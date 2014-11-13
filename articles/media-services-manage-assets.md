<properties urlDisplayName="Manage Assets in Media Services" pageTitle="Gestion d'&eacute;l&eacute;ments multim&eacute;dias dans Media Services - Azure" metaKeywords="" description="Apprenez &agrave; g&eacute;rer des &eacute;l&eacute;ments multim&eacute;dias sur Media Services. Vous pouvez &eacute;galement g&eacute;rer des travaux, des t&acirc;ches, des strat&eacute;gies d'acc&egrave;s, des localisateurs et bien d'autres &eacute;l&eacute;ments. Les exemples de code sont &eacute;crits en C# et utilisent le Kit de d&eacute;veloppement logiciel (SDK) Media Services pour .NET." metaCanonical="" services="media-services" documentationCenter="" title="Utilisation des &eacute;l&eacute;ments multim&eacute;dias dans le stockage" authors="juliako" solutions="" manager="dwrede" editor="" />

<tags ms.service="media-services" ms.workload="media" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="juliako" />

# Utilisation des éléments multimédias dans le stockage

Cet article fait partie d'une série qui présente la programmation Azure Media Services. La rubrique précédente s'intitulait [Protection d'un élément multimédia][Protection d'un élément multimédia].

Une fois que vous avez créé des éléments multimédias et que vous les avez téléchargés dans Media Services, vous pouvez y accéder et les gérer sur le serveur. Vous pouvez également gérer sur le serveur d'autres objets faisant partie de Media Services, entre autres, des travaux, des tâches, des stratégies d'accès et des localisateurs.

L'exemple suivant montre comment lancer une requête pour obtenir un élément multimédia par assetId.

    static IAsset GetAsset(string assetId){ // Use a LINQ Select query to get an asset. var assetInstance = from a in _context.Assets where a.Id == assetId select a; // Reference the asset as an IAsset. IAsset asset = assetInstance.FirstOrDefault();
    return asset;

<p>
}
</code>

</pre>
</p>
Pour répertorier tous les éléments multimédias disponibles sur le serveur, vous pouvez utiliser la méthode suivante, qui effectue une itération dans la collection d'éléments multimédias et affiche des informations détaillées sur chaque élément.

    static void ListAssets(){ string waitMessage = "Building the list. This may take a few " + "seconds to a few minutes depending on how many assets " + "you have." + Environment.NewLine + Environment.NewLine + "Please wait..." + Environment.NewLine; Console.Write(waitMessage);
    // Create a Stringbuilder to store the list that we build. 
    StringBuilder builder = new StringBuilder();

    foreach (IAsset asset in _context.Assets)
    {
        // Display the collection of assets.
        builder.AppendLine("");
        builder.AppendLine("******ASSET******");
        builder.AppendLine("Asset ID: " + asset.Id);
        builder.AppendLine("Name: " + asset.Name);
        builder.AppendLine("==============");
        builder.AppendLine("******ASSET FILES******");

        // Display the files associated with each asset. 
        foreach (IAssetFile fileItem in asset.AssetFiles)
        {
            builder.AppendLine("Name: " + fileItem.Name);
            builder.AppendLine("Size: " + fileItem.ContentFileSize);
            builder.AppendLine("==============");
        }
    }

    // Display output in console.
    Console.Write(builder.ToString());

<p>
}
</code>

</pre>
L'extrait de code suivant supprime tous les éléments multimédias du compte Media Services.

    foreach (IAsset asset in _context.Assets){ asset.Delete();}

</p>
Pour plus d'informations sur la gestion des éléments multimédias, consultez les pages suivantes :

-   [Gestion des éléments multimédias avec le Kit de développement logiciel (SDK) Media Services pour .NET][Gestion des éléments multimédias avec le Kit de développement logiciel (SDK) Media Services pour .NET]
-   [Gestion des éléments multimédias avec l'API REST de Media Services][Gestion des éléments multimédias avec l'API REST de Media Services]

</p>
## Étapes suivantes

Maintenant que vous savez comment gérer les éléments multimédias, passez à la rubrique [Fourniture d'un élément multimédia par téléchargement][Fourniture d'un élément multimédia par téléchargement].

  [Protection d'un élément multimédia]: ../media-services-protect-asset/
  [Gestion des éléments multimédias avec le Kit de développement logiciel (SDK) Media Services pour .NET]: http://msdn.microsoft.com/fr-fr/library/jj129589.aspx
  [Gestion des éléments multimédias avec l'API REST de Media Services]: http://msdn.microsoft.com/fr-fr/library/jj129583.aspx
  [Fourniture d'un élément multimédia par téléchargement]: ../media-services-deliver-asset-download/
