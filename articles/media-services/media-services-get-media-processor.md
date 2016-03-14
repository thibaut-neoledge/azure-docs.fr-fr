<properties 
	pageTitle="Création d'un processeur multimédia | Microsoft Azure" 
	description="Apprenez à créer un composant processeur multimédia pour encoder, chiffrer ou déchiffrer un contenu multimédia, ou convertir son format pour Azure Media Services. Les exemples de code sont écrits en C# et utilisent le Kit de développement logiciel (SDK) Media Services pour .NET." 
	services="media-services" 
	documentationCenter="" 
	authors="juliako" 
	manager="erikre" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/01/2016" 
	ms.author="juliako"/>


#Obtention d’une instance de processeur multimédia

> [AZURE.SELECTOR]
- [.NET](media-services-get-media-processor.md)
- [REST](media-services-rest-get-media-processor.md)


##Vue d'ensemble

Dans Media Services, un processeur multimédia est un composant qui gère une tâche de traitement spécifique, telle que l’encodage, la conversion de format, le chiffrement ou le déchiffrement de contenu multimédia. Le plus souvent, vous devez créer un processeur multimédia lorsque vous créez une tâche visant à encoder, à chiffrer ou à convertir le format du contenu multimédia.

Le tableau suivant vous indique le nom et la description de chaque processeur multimédia disponible.

Nom du processeur multimédia|Description|Informations complémentaires
---|---|---
Media Encoder Standard|Fournit des fonctionnalités standard pour l’encodage à la demande. |[Vue d’ensemble et comparaison d’encodeurs multimédia à la demande Azure](media-services-encode-asset.md)
Media Encoder Premium Workflow|Permet d’exécuter des tâches d’encodage à l’aide de Media Encoder Premium Workflow.|[Vue d’ensemble et comparaison d’encodeurs multimédia à la demande Azure](media-services-encode-asset.md)
Azure Media Indexer| Permet d’effectuer des recherches dans les fichiers multimédias et le contenu et de générer des pistes et des mots clés de sous-titrage codé.|[Azure Media Indexer](media-services-index-content.md)
Azure Media Hyperlapse (version préliminaire)|Permet de lisser les « dégradations » dans votre vidéo avec une stabilisation vidéo. Permet également d’accélérer votre contenu dans un clip consommable.|[Azure Media Hyperlapse](media-services-hyperlapse-content.md)
Encodeur multimédia Azure|Amorti
Storage Decryption| Amorti|
Azure Media Packager|Amorti|
Azure Media Encryptor|Amorti|

##Obtention d'un processeur multimédia

La méthode suivante montre comment obtenir une instance de processeur multimédia. L’exemple de code implique l’utilisation d’une variable au niveau du module, nommée **\_context**, pour conserver une référence au contexte, tel que décrit dans la section [Procédure : se connecter à Media Services par programme](media-services-dotnet-connect_programmatically.md).

	private static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
	{
		var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
		ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();
		
		if (processor == null)
		throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));
		
		return processor;
	}


##Parcours d’apprentissage de Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Fournir des commentaires

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

##Étapes suivantes

Maintenant que vous savez comment obtenir une instance de processeur multimédia, consultez la rubrique [Encodage d’un élément multimédia](media-services-dotnet-encode-with-media-encoder-standard.md) pour savoir comment utiliser Media Encoder Standard afin d’encoder un élément multimédia.

<!---HONumber=AcomDC_0302_2016-->