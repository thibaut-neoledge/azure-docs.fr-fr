<properties linkid="develop-media-services-how-to-guides-create-media-processor" urlDisplayName="Create a Media Processor" pageTitle="How to Create a Media Processor - Azure" metaKeywords="" description="Learn how to create a media processor component to encode, convert format, encrypt, or decrypt media content for Azure Media Services. Code samples are written in C# and use the Media Services SDK for .NET." metaCanonical="" services="media-services" documentationCenter="" title="How to: Get a Media Processor Instance" authors="migree" solutions="" manager="" editor="" />

Obtention d'une instance de processeur multimédia
=================================================

Cet article fait partie d'une série qui présente la programmation Azure Media Services. La rubrique précédente s'intitulait [Création d'un élément multimédia chiffré et chargement dans le stockage](http://go.microsoft.com/fwlink/?LinkID=301733&clcid=0x409).

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
       <td><a href="http://msdn.microsoft.com/en-us/library/jj129582.aspx"> Chaînes de présélection des tâches pour l'Encodeur multimédia Azure</a></td>
    </tr>
    <tr>
        <td>Azure Media Packager</td>
        <td>Vous permet de convertir des éléments multimédias du format .mp4 vers un format de diffusion en continu. Permet également de convertir des éléments multimédias de diffusion en continu au format HLS (HTTP Live Streaming) d'Apple.</td>
		<td><a href="http://msdn.microsoft.com/en-us/library/hh973635.aspx">Chaînes de présélection des tâches pour Azure Media Packager</a></td>
    </tr>
    <tr>
        <td>Azure Media Encryptor</td>
        <td>Vous permet de chiffrer des éléments multimédias avec PlayReady Protection.</td>
        <td><a href="http://msdn.microsoft.com/en-us/library/hh973610.aspx">Chaînes de présélection des tâches pour Azure Media Packager</a></td>
    </tr>
    <tr>
        <td>Storage Decryption</td>
        <td>Vous permet de déchiffrer les éléments multimédias qui ont été chiffrés par chiffrement de stockage.</td>
		<td>N/A</td>
    </tr>
  </tbody>
</table>

<br />

La méthode suivante montre comment obtenir une instance de processeur multimédia. L'exemple de code implique l'utilisation d'une variable au niveau du module, nommée **\_context**, pour conserver une référence au contexte, tel que décrit dans la section [Connexion à Media Services par programme](http://www.windowsazure.com/en-us/develop/media-services/how-to-guides/set-up-computer-for-media-services)

``` {}
private static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
{
     var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
        ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();

    if (processor == null)
        throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));

    return processor;
}
```

Étapes suivantes
----------------

Maintenant que vous savez comment obtenir une instance de processeur multimédia, consultez la rubrique [Encodage d'un élément multimédia](http://go.microsoft.com/fwlink/?LinkId=301753) pour savoir comment utiliser l'Encodeur multimédia Azure afin d'encoder un élément multimédia.

