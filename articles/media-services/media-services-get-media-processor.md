<properties 
	pageTitle="Comment créer un processeur multimédia - Azure" 
	description="Apprenez à créer un composant processeur multimédia pour encoder, chiffrer ou déchiffrer un contenu multimédia, ou convertir son format pour Azure Media Services. Les exemples de code sont écrits en C# et utilisent le Kit de développement logiciel (SDK) Media Services pour .NET." 
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
	ms.date="05/13/2015" 
	ms.author="juliako"/>


#Obtention d’une instance de processeur multimédia

Cet article fait partie de la série [workflow de vidéo à la demande Media Services](media-services-video-on-demand-workflow.md).

##Vue d'ensemble

Dans Media Services, un processeur multimédia est un composant qui gère une tâche de traitement spécifique, telle que l’encodage, la conversion de format, le chiffrement ou le déchiffrement de contenu multimédia. Le plus souvent, vous devez créer un processeur multimédia lorsque vous créez une tâche visant à encoder, à chiffrer ou à convertir le format du contenu multimédia.

Le tableau suivant vous indique le nom et la description de chaque processeur multimédia disponible.

<table border="2" cellspacing="0" cellpadding="5" style="border: 2px solid #000000;">
  <thead>
    <tr>
       <th>Nom du processeur multimédia</th>
       <th>Description</th>
	<th>Informations complémentaires</th>
    </tr>
  </thead>
  <tbody>
    <tr>
       <td>Encodeur multimédia Azure</td>
       <td>Permet d’exécuter les tâches d’encodage à l’aide de l’Encodeur multimédia Azure.</td>
       <td><a href="http://msdn.microsoft.com/library/jj129582.aspx">Chaînes de présélection de tâche pour l’Encodeur multimédia Azure</a></td>
    </tr>
    <tr>
       <td>Media Encoder Premium Workflow</td>
       <td>Permet d’exécuter des tâches d’encodage à l’aide de Media Encoder Premium Workflow.</td>
       <td><a href="http://azure.microsoft.com/documentation/articles/media-services-encode-with-premium-workflow/">Encoder avec Media Encoder Premium Workflow.</a></td>
    </tr>    
	<tr>
        <td>Azure Media Indexer</td>
        <td>Permet d’effectuer des recherches dans les fichiers multimédias et le contenu et de générer des pistes et des mots clés de sous-titrage codé.</td>
		<td><a href="http://azure.microsoft.com/documentation/articles/media-services-index-content/">Indexation de fichiers multimédias avec Azure Media Indexer</a>.</td>
    </tr>
    <tr>
        <td>Microsoft Azure Media Packager</td>
        <td>Vous permet de convertir des éléments multimédias du format .mp4 vers un format de diffusion en continu. De même, il permet de convertir des éléments multimédias du format de diffusion en continu lisse au format HLS (Apple HTTP Live Streaming).</td>
		<td><a href="http://msdn.microsoft.com/library/hh973635.aspx">Chaînes de présélection des tâches pour Azure Media Packager</a></td>
    </tr>
    <tr>
        <td>Microsoft Azure Media Encryptor</td>
        <td>Vous permet de chiffrer des éléments multimédias avec PlayReady Protection.</td>
        <td><a href="http://msdn.microsoft.com/library/hh973610.aspx">Chaînes de présélection des tâches pour Azure Media Packager</a></td>
    </tr>
	<tr>
		<td>Azure Media Hyperlapse (version préliminaire)</td>
		<td>Permet de lisser les «&#160;dégradations&#160;» dans votre vidéo avec une stabilisation vidéo. Permet également d’accélérer votre contenu dans un clip consommable.</td>
		<td><a href="http://go.microsoft.com/fwlink/?LinkId=613274">Azure Media Hyperlapse</a></td>
	</tr>
    <tr>
        <td>Storage Decryption</td>
        <td>Vous permet de déchiffrer les éléments multimédias qui ont été chiffrés par chiffrement de stockage.</td>
		<td>N/A</td>
    </tr>  </tbody>
</table>

<br />

##Obtention de MediaProcessor

La méthode suivante montre comment obtenir une instance de processeur multimédia. L’exemple de code implique l’utilisation d’une variable au niveau du module, nommée **_context**, pour conserver une référence au contexte, tel que décrit dans la section [Procédure : se connecter à Media Services par programme].

	private static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
	{
	     var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
	        ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();
	
	    if (processor == null)
	        throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));
	
	    return processor;
	}

##Étapes suivantes
Maintenant que vous savez comment obtenir une instance de processeur multimédia, consultez la rubrique [Encodage d'un élément multimédia][] pour savoir comment utiliser l'Encodeur multimédia Azure afin d'encoder un élément multimédia.

[Encodage d'un élément multimédia]: media-services-encode-asset.md
[Task Preset Strings for the Azure Media Encoder]: http://msdn.microsoft.com/library/jj129582.aspx
[Procédure : se connecter à Media Services par programme]: ../media-services-set-up-computer/

<!---HONumber=July15_HO2-->