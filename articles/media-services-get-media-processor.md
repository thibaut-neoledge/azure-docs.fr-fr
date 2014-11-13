<properties urlDisplayName="Create a Media Processor" pageTitle="Cr&eacute;ation d'un processeur multim&eacute;dia - Azure" metaKeywords="" description="Apprenez &agrave; cr&eacute;er un composant processeur multim&eacute;dia pour encoder, chiffrer ou d&eacute;chiffrer un contenu multim&eacute;dia, ou convertir son format pour Azure Media Services. Les exemples de code sont &eacute;crits en C# et utilisent le Kit de d&eacute;veloppement logiciel (SDK) Media Services pour .NET." metaCanonical="" services="media-services" documentationCenter="" title="Utilisation d'une instance de processeur multim&eacute;dia" authors="juliako" solutions="" manager="dwrede" editor="" />

<tags ms.service="media-services" ms.workload="media" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/26/2014" ms.author="juliako" />

# Utilisation d'une instance de processeur multimédia

Cet article fait partie d'une série qui présente la programmation Azure Media Services. La rubrique précédente s'intitulait [Création d'un élément multimédia chiffré et chargement dans le stockage][Création d'un élément multimédia chiffré et chargement dans le stockage].

Dans Media Services, un processeur multimédia est un composant qui gère une tâche de traitement spécifique, telle que l'encodage, la conversion de format, le chiffrement ou le déchiffrement de contenu multimédia. Le plus souvent, vous devez créer un processeur multimédia lorsque vous créez une tâche visant à encoder, à chiffrer ou à convertir le format du contenu multimédia.

Le tableau suivant vous indique le nom et la description de chaque processeur multimédia disponible.

| Nom du processeur multimédia    | Description                                                                                                                                                                                                                          | Informations complémentaires                                            |
|---------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------|
| Encodeur multimédia Azure       | Vous permet d'exécuter des tâches d'encodage avec l'Encodeur multimédia.                                                                                                                                                             | [Chaînes de présélection des tâches pour l'Encodeur multimédia Azure][Chaînes de présélection des tâches pour l'Encodeur multimédia Azure] |
| Microsoft Azure Media Packager  | Vous permet de convertir des éléments multimédias du format .mp4 vers un format de diffusion en continu. Permet également de convertir des éléments multimédias de diffusion en continu au format HLS (HTTP Live Streaming) d'Apple. | [Chaînes de présélection des tâches pour Azure Media Packager][Chaînes de présélection des tâches pour Azure Media Packager]        |
| Microsoft Azure Media Encryptor | Vous permet de chiffrer des éléments multimédias avec PlayReady Protection.                                                                                                                                                          | [Chaînes de présélection des tâches pour Azure Media Packager][1]       |
| Azure Media Indexer             | Vous permet de faire en sorte que les fichiers et le contenu multimédias puissent faire l'objet d'une recherche, et de générer des pistes de sous-titrage et des mots clés.                                                          | N/A                                                                     |
| Storage Decryption              | Vous permet de déchiffrer les éléments multimédias qui ont été chiffrés par chiffrement de stockage.                                                                                                                                 | N/A                                                                     |

La méthode suivante montre comment obtenir une instance de processeur multimédia. L'exemple de code implique l'utilisation d'une variable au niveau du module, nommée \*\*\_context\*\*, pour conserver une référence au contexte, tel que décrit dans la section [Connexion à Media Services par programme][Connexion à Media Services par programme]

    private static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
    {
         var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
            ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();

        if (processor == null)
            throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));

        return processor;
    }

## Étapes suivantes

Maintenant que vous savez comment obtenir une instance de processeur multimédia, consultez la rubrique [Encodage d'un élément multimédia][Encodage d'un élément multimédia] pour savoir comment utiliser l'Encodeur multimédia Azure afin d'encoder un élément multimédia.

  [Création d'un élément multimédia chiffré et chargement dans le stockage]: ../media-services-create-encrypted-asset-upload-storage/
  [Chaînes de présélection des tâches pour l'Encodeur multimédia Azure]: http://msdn.microsoft.com/fr-fr/library/jj129582.aspx
  [Chaînes de présélection des tâches pour Azure Media Packager]: http://msdn.microsoft.com/fr-fr/library/hh973635.aspx
  [1]: http://msdn.microsoft.com/fr-fr/library/hh973610.aspx
  [Connexion à Media Services par programme]: ../media-services-set-up-computer/
  [Encodage d'un élément multimédia]: ../media-services-encode-asset/
