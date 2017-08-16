---
title: "Tâches prédéfinies pour Azure Media Indexer"
description: "Cette rubrique offre une vue d’ensemble des tâches prédéfinies pour Azure Media Indexer."
services: media-services
documentationcenter: 
author: Asolanki
manager: erikre
editor: 
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 08/03/2017
ms.author: adsolank;juliako;
ms.translationtype: HT
ms.sourcegitcommit: 9633e79929329470c2def2b1d06d95994ab66e38
ms.openlocfilehash: 520785880cb5c9dfa5191a097fdcc3ad776f403e
ms.contentlocale: fr-fr
ms.lasthandoff: 08/04/2017

---
# <a name="task-preset-for-azure-media-indexer"></a>Tâches prédéfinies pour Azure Media Indexer

Azure Media Indexer est un processeur multimédia qui permet d’effectuer les tâches suivantes : rendre possibles les recherches dans les fichiers et le contenu multimédias, générer des pistes de sous-titrage et des mots clés et indexer les fichiers qui font partie d’une ressource.

Cette rubrique décrit les tâches prédéfinies que vous devez transmettre à votre travail d’indexation. Pour obtenir un exemple complet, consultez [Indexation des fichiers multimédias avec Azure Media Indexer](media-services-index-content.md).

## <a name="azure-media-indexer-configuration-xml"></a>Fichier de configuration XML d’Azure Media Indexer

Le tableau suivant décrit les éléments et les attributs du fichier de configuration XML.

|Nom|Require|Description|
|---|---|---|
|Entrée|true|Fichiers de ressources que vous souhaitez indexer.<br/>Azure Media Indexer prend en charge les formats de fichiers multimédias suivants : MP4, MOV, WMV, MP3, M4A, WMA, AAC, WAV. <br/><br/>Vous pouvez spécifier le nom des fichiers dans l’attribut **name** ou **list** de l’élément **input** (comme indiqué ci-dessous). Si vous ne spécifiez pas les fichiers d’élément multimédia à indexer, le fichier principal est sélectionné. Si aucun fichier principal n’est défini, le premier fichier de ressource d’entrée est indexé.<br/><br/>Pour spécifier explicitement le nom du fichier multimédia, effectuez ceci :<br/>```<input name="TestFile.wmv" />```<br/><br/>Vous pouvez également indexer plusieurs fichiers de ressources à la fois (jusqu'à 10 fichiers). Pour ce faire :<br/>- Créez un fichier texte (fichier manifeste) et affectez-lui une extension .lst.<br/>- Ajoutez une liste de tous les noms des fichiers de votre élément multimédia d’entrée à ce fichier de manifeste.<br/>- Ajoutez (chargez) le fichier manifeste dans l’élément multimédia.<br/>- Spécifiez le nom du fichier manifeste dans l’attribut list de l’élément input.<br/>```<input list="input.lst">```<br/><br/>**Remarque :** si vous ajoutez plus de 10 fichiers dans le fichier manifeste, la tâche d’indexation échouera avec le code d’erreur 2006.|
|métadonnées|false|Métadonnées des fichiers d’élément multimédia spécifiés.<br/>```<metadata key="..." value="..." />```<br/><br/>Vous pouvez fournir des valeurs de clés prédéfinies. <br/><br/>À l’heure actuelle, les clés suivantes sont prises en charge :<br/><br/>**title** et **description** : permettent de mettre à jour le modèle linguistique pour améliorer la précision de la reconnaissance vocale.<br/>```<metadata key="title" value="[Title of the media file]" /><metadata key="description" value="[Description of the media file]" />```<br/><br/>**username** et **password** : utilisées à des fins d’authentification lors du téléchargement de fichiers Internet via HTTP ou HTTPS.<br/>```<metadata key="username" value="[UserName]" /><metadata key="password" value="[Password]" />```<br/>Les valeurs username et password s’appliquent à toutes les URL de média du manifeste d’entrée.|
|features<br/><br/>ajoutées dans la version 1.2. Actuellement, la seule fonctionnalité prise en charge est la reconnaissance vocale (« ASR »).|false|La fonctionnalité de reconnaissance vocale a les clés de paramètres suivantes :<br/><br/>Language :<br/>- Langue naturelle à reconnaître dans le fichier multimédia.<br/>- English, Spanish<br/><br/>CaptionFormats :<br/>- Liste délimitée par des points-virgules des formats de sous-titre souhaités (le cas échéant).<br/>- ttml;sami;webvtt<br/><br/><br/>GenerateAIB :<br/>- Indicateur booléen spécifiant si un fichier AIB est requis ou non (pour une utilisation avec SQL Server et l’indexeur IFilter client). Pour plus d’informations, consultez Utilisation de fichiers AIB avec Azure Media Indexer et SQL Server.<br/>- true, false<br/><br/>GenerateKeywords :<br/>- Indicateur booléen spécifiant si un fichier XML de mot-clé est requis ou non.<br/>- true, false|

## <a name="azure-media-indexer-configuration-xml-example"></a>Exemple de fichier de configuration XML d’Azure Media Indexer

``` 
<?xml version="1.0" encoding="utf-8"?>  
<configuration version="2.0">  
  <input>  
    <metadata key="title" value="[Title of the media file]" />  
    <metadata key="description" value="[Description of the media file]" />  
  </input>  
  <settings>  
  </settings>  
  
  <features>  
    <feature name="ASR">    
      <settings>  
        <add key="Language" value="English"/>  
        <add key="CaptionFormats" value="ttml;sami;webvtt"/>  
        <add key="GenerateAIB" value ="true" />  
        <add key="GenerateKeywords" value ="true" />  
      </settings>  
    </feature>  
  </features>  
  
</configuration>  
```
  
## <a name="next-steps"></a>Étapes suivantes

Consultez [Indexation de fichiers multimédias avec Azure Media Indexer](media-services-index-content.md).


