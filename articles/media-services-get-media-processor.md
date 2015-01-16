<properties urlDisplayName="Create a Media Processor" pageTitle="Création d'un processeur multimédia - Azure" metaKeywords="" description="Apprenez à créer un composant processeur multimédia pour encoder, chiffrer ou déchiffrer un contenu multimédia, ou convertir son format pour Azure Media Services. Les exemples de code sont écrits en C# et utilisent le Kit de développement logiciel (SDK) Media Services pour .NET." metaCanonical="" services="media-services" documentationCenter="" title="How to: Get a Media Processor Instance" authors="juliako" solutions="" manager="dwrede" editor="" />

<tags ms.service="media-services" ms.workload="media" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/30/2014" ms.author="juliako" />





<h1>Obtention d'une instance de processeur multimédia</h1>
Cet article fait partie d'une série qui présente la programmation Azure Media Services. La rubrique précédente s'intitulait [  Création d'un élément multimédia chiffré et chargement dans le stockage](../media-services-create-encrypted-asset-upload-storage/).

Dans Media Services, un processeur multimédia est un composant qui gère une tâche de traitement spécifique, telle que l'encodage, la conversion de format, le chiffrement ou le déchiffrement de contenu multimédia. Le plus souvent, vous devez créer un processeur multimédia lorsque vous créez une tâche visant à encoder, à chiffrer ou à convertir le format du contenu multimédia.

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
       <td>Vous permet d'exécuter des tâches d'encodage avec l'Encodeur multimédia.</td>
       <td><a href="http://msdn.microsoft.com/fr-fr/library/jj129582.aspx"> Chaînes de présélection des tâches pour l'Encodeur multimédia Azure</a></td>
    </tr>
    <tr>
        <td>Microsoft Azure Media Packager</td>
        <td>Vous permet de convertir des éléments multimédias du format .mp4 vers un format de diffusion en continu. Permet également de convertir des éléments multimédias de diffusion en continu au format HLS (HTTP Live Streaming) d'Apple.</td>
		<td><a href="http://msdn.microsoft.com/fr-fr/library/hh973635.aspx">Chaînes de présélection des tâches pour Azure Media Packager</a></td>
    </tr>
    <tr>
        <td>Microsoft Azure Media Encryptor</td>
        <td>Vous permet de chiffrer des éléments multimédias avec PlayReady Protection.</td>
        <td><a href="http://msdn.microsoft.com/fr-fr/library/hh973610.aspx">Chaînes de présélection des tâches pour Azure Media Packager</a></td>
    </tr>
    <tr>
        <td>Azure Media Indexer</td>
        <td>Vous permet de faire en sorte que les fichiers et le contenu multimédias puissent faire l'objet d'une recherche, et de générer des pistes de sous-titrage et des mots clés.</td>
		<td>N/A</td>
    </tr>
    <tr>
        <td>Storage Decryption</td>
        <td>Vous permet de déchiffrer les éléments multimédias qui ont été chiffrés par chiffrement de stockage.</td>
		<td>N/A</td>
    </tr>  </tbody>
</table>

<br />

La méthode suivante montre comment obtenir une instance de processeur multimédia. L'exemple de code implique l'utilisation d'une variable au niveau du module, nommée **_context**, pour conserver une référence au contexte, tel que décrit dans la section [  Connexion à Media Services par programme].

<pre><code>
private static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
{
     var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
        ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();

    if (processor == null)
        throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));

    return processor;
}
</code></pre>

<h2>Étapes suivantes</h2>
Maintenant que vous savez comment obtenir une instance de processeur multimédia, consultez la rubrique [Encodage d'un élément multimédia][] pour savoir comment utiliser l'Encodeur multimédia Azure afin d'encoder un élément multimédia.

[Encodage d'un élément multimédia]: ../media-services-encode-asset/
[Chaînes de présélection des tâches pour l'Encodeur multimédia Azure]: http://msdn.microsoft.com/fr-fr/library/jj129582.aspx
[  Connexion à Media Services par programme]: ../media-services-set-up-computer/

<!--HONumber=35.1-->
