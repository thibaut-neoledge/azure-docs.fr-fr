<properties 
	pageTitle="Fourniture de contenu de diffusion en continu à partir de Media Services - Azure" 
	description="Apprenez à créer un localisateur utilisé pour générer une URL de diffusion en continu. Les exemples de code sont écrits en C# et utilisent le Kit de développement logiciel (SDK) Media Services pour .NET." 
	authors="juliako" 
	manager="dwrede" 
	editor="" 
	services="media-services" 
	documentationCenter=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/17/2015" 
	ms.author="juliako"/>


# Procédure : Fourniture de contenu de diffusion en continu


Cet article fait partie des séries [workflow de vidéo à la demande Media Services](media-services-video-on-demand-workflow.md) et [workflow de vidéo en flux continu Media Services](media-services-live-streaming-workflow.md).  

## Vue d'ensemble

Vous pouvez diffuser un MP4 à débit adaptatif défini par la création d'un localisateur de diffusion en continu à la demande et la création d'une URL de diffusion en continu. La rubrique [Encodage d'une ressource](media-services-encode-asset.md) explique comment encoder en un ensemble de fichiers MP4 à débit adaptatif. Avant de créer un localisateur, vous devez configurer la stratégie de remise de ressources comme décrit dans [cette](media-services-dotnet-configure-asset-delivery-policy.md) rubrique. 

Vous pouvez également utiliser un localisateur de diffusion en continu à la demande pour créer des URL qui pointent vers les fichiers MP4 pouvant être téléchargés progressivement.  

Cette rubrique montre comment créer un localisateur de diffusion en continu à la demande pour publier votre ressource et créer des URL de diffusion en continu lisse, MPEG DASH et TLS. Elle explique également la création d'URL de téléchargement progressif. 
  	 
## Création d'un localisateur de diffusion en continu à la demande

Pour créer le localisateur de diffusion en continu à la demande et obtenir les URL, vous devez effectuer les opérations suivantes :

   1. Définition d'une stratégie d'accès.
   2. Création d'un localisateur de diffusion en continu à la demande.
   3. Si vous envisagez de diffuser en continu, obtenez le fichier manifeste de diffusion en continu (.ism) dans la ressource. 
   		
	Si vous souhaitez télécharger progressivement, obtenez les noms des fichiers MP4 dans la ressource.  
   4. Création d'URL vers le fichier manifeste ou les fichiers MP4. 
   

### Utilisation du Kit de développement logiciel (SDK) .NET de Media Services 

Création d'URL de diffusion 

	private static void BuildStreamingURLs(IAsset asset)
	{
	
	    // Create a 30-day readonly access policy. 
	    IAccessPolicy policy = _context.AccessPolicies.Create("Streaming policy",
	        TimeSpan.FromDays(30),
	        AccessPermissions.Read);
	
	    // Create a locator to the streaming content on an origin. 
	    ILocator originLocator = _context.Locators.CreateLocator(LocatorType.OnDemandOrigin, asset,
	        policy,
	        DateTime.UtcNow.AddMinutes(-5));
	
	    // Display some useful values based on the locator.
	    Console.WriteLine("Streaming asset base path on origin: ");
	    Console.WriteLine(originLocator.Path);
	    Console.WriteLine();
	
	    // Get a reference to the streaming manifest file from the  
	    // collection of files in the asset. 
	    var manifestFile = asset.AssetFiles.Where(f => f.Name.ToLower().
	                                EndsWith(".ism")).
	                                FirstOrDefault();
	    
	    // Create a full URL to the manifest file. Use this for playback
	    // in streaming media clients. 
	    string urlForClientStreaming = originLocator.Path + manifestFile.Name + "/manifest";
	    Console.WriteLine("URL to manifest for client streaming using Smooth Streaming protocol: ");
	    Console.WriteLine(urlForClientStreaming);
	    Console.WriteLine("URL to manifest for client streaming using HLS protocol: ");
	    Console.WriteLine(urlForClientStreaming + "(format=m3u8-aapl)");
	    Console.WriteLine("URL to manifest for client streaming using MPEG DASH protocol: ");
	    Console.WriteLine(urlForClientStreaming + "(format=mpd-time-csf)"); 
	    Console.WriteLine();
	}

Le code fournit :
	
	URL to manifest for client streaming using Smooth Streaming protocol:
	http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny.ism/manifest
	URL to manifest for client streaming using HLS protocol:
	http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny.ism/manifest(format=m3u8-aapl)
	URL to manifest for client streaming using MPEG DASH protocol:
	http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny.ism/manifest(format=mpd-time-csf)
	

Génération d'URL de téléchargement progressif 

	private static void BuildProgressiveDownloadURLs(IAsset asset)
	{
	    // Create a 30-day readonly access policy. 
	    IAccessPolicy policy = _context.AccessPolicies.Create("Streaming policy",
	        TimeSpan.FromDays(30),
	        AccessPermissions.Read);
	
	    // Create an OnDemandOrigin locator to the asset. 
	    ILocator originLocator = _context.Locators.CreateLocator(LocatorType.OnDemandOrigin, asset,
	        policy,
	        DateTime.UtcNow.AddMinutes(-5));
	
	    // Display some useful values based on the locator.
	    Console.WriteLine("Streaming asset base path on origin: ");
	    Console.WriteLine(originLocator.Path);
	    Console.WriteLine();
	
	    // Get MP4 files.
	    IEnumerable<IAssetFile> mp4AssetFiles = asset
	        .AssetFiles
	        .ToList()
	        .Where(af => af.Name.EndsWith(".mp4", StringComparison.OrdinalIgnoreCase));
	            
	    // Create a full URL to the MP4 files. Use this to progressively download files.
	    foreach (var pd in mp4AssetFiles)
	        Console.WriteLine(originLocator.Path + pd.Name);
	}

Le code fournit :
	
	http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4
	http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_400kbps_AAC_und_ch2_96kbps.mp4
	http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_3400kbps_AAC_und_ch2_96kbps.mp4
	http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_2250kbps_AAC_und_ch2_96kbps.mp4
	
	. . . 

### Utilisation des extensions du Kit de développement logiciel (SDK) Media Services pour .NET

Le code suivant appelle les méthodes des extensions du Kit de développement logiciel (SDK) .NET, qui créent un localisateur et génèrent les URL de diffusion en continu lisse, HLS et MPEG-DASH pour la diffusion adaptative en continu.

	// Create a loctor.
	_context.Locators.Create(
	    LocatorType.OnDemandOrigin,
	    inputAsset,
	    AccessPermissions.Read,
	    TimeSpan.FromDays(30));
	
	// Get the streaming URLs.
	Uri smoothStreamingUri = inputAsset.GetSmoothStreamingUri();
	Uri hlsUri = inputAsset.GetHlsUri();
	Uri mpegDashUri = inputAsset.GetMpegDashUri();
	
	Console.WriteLine(smoothStreamingUri);
	Console.WriteLine(hlsUri);
	Console.WriteLine(mpegDashUri);

<!--HONumber=52-->