<properties 
	pageTitle="Gestion d’éléments multimédias dans Media Services" 
	description="Apprenez à gérer des éléments multimédias sur Media Services. Vous pouvez également gérer des travaux, des tâches, des stratégies d'accès, des localisateurs et bien d'autres éléments. Les exemples de code sont écrits en C# et utilisent le Kit de développement logiciel (SDK) Media Services pour .NET." 
	services="media-services" 
	documentationCenter="" 
	authors="juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/24/2015" 
	ms.author="juliako"/>


#Gestion d’éléments multimédias dans le stockage

Cet article fait partie des séries [workflow de vidéo à la demande Media Services](media-services-video-on-demand-workflow.md) et [workflow de vidéo en flux continu Media Services](media-services-live-streaming-workflow.md).


Une fois que vous avez créé des éléments multimédias, vous pouvez y accéder et les gérer sur le serveur. Vous pouvez également gérer sur le serveur d’autres objets faisant partie de Media Services, entre autres, des travaux, des tâches, des stratégies d’accès et des localisateurs.

L’exemple suivant montre comment lancer une requête pour obtenir un élément multimédia par assetId.

	static IAsset GetAsset(string assetId)
	{
	    // Use a LINQ Select query to get an asset.
	    var assetInstance =
	        from a in _context.Assets
	        where a.Id == assetId
	        select a;
	    // Reference the asset as an IAsset.
	    IAsset asset = assetInstance.FirstOrDefault();
	
	    return asset;
	}

Pour répertorier tous les éléments multimédias disponibles sur le serveur, vous pouvez utiliser la méthode suivante, qui effectue une itération dans la collection d’éléments multimédias et affiche des informations détaillées sur chaque élément.
	
	static void ListAssets()
	{
	    string waitMessage = "Building the list. This may take a few "
	        + "seconds to a few minutes depending on how many assets "
	        + "you have."
	        + Environment.NewLine + Environment.NewLine
	        + "Please wait..."
	        + Environment.NewLine;
	    Console.Write(waitMessage);
	
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
	}

L’extrait de code suivant supprime tous les éléments multimédias du compte Media Services. Notez que si une ressource est associée à un programme, vous devez tout d’abord supprimer le programme.

	foreach (IAsset asset in _context.Assets)
	{
	    asset.Delete();
	}

 

<!---HONumber=July15_HO4-->