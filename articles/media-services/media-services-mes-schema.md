---
title: "Schéma Media Encoder Standard | Microsoft Docs"
description: "Cette rubrique fournit une vue d’ensemble du schéma Media Encoder Standard."
author: Juliako
manager: erikre
editor: 
services: media-services
documentationcenter: 
ms.assetid: 4c060062-8ef2-41d9-834e-e81e8eafcf2e
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/10/2017
ms.author: juliako
translationtype: Human Translation
ms.sourcegitcommit: e126076717eac275914cb438ffe14667aad6f7c8
ms.openlocfilehash: a526610f5b09ce73a9c192ec45ae8aafab001401
ms.lasthandoff: 01/13/2017


---
# <a name="media-encoder-standard-schema"></a>Schéma Media Encoder Standard
Cette rubrique décrit certains des éléments et types du schéma XML sur lequel [les préréglages Media Encoder Standard](media-services-mes-presets-overview.md) sont basés. La rubrique fournit des explications sur les éléments et leurs valeurs valides. Le schéma complet sera publié à une date ultérieure.  

## <a name="a-namepreseta-preset-root-element"></a><a name="Preset"></a> Présélection (élément racine)
Définit une valeur prédéfinie d’encodage.  

### <a name="elements"></a>Éléments
| Nom | Type | Description |
| --- | --- | --- |
| **Encodage** |[Encodage](media-services-mes-schema.md#Encoding) |Élément racine, indique que les sources d’entrée doivent être encodées. |
| **Sorties** |[Sorties](media-services-mes-schema.md#Output) |Collection de fichiers de sortie souhaités. |

### <a name="attributes"></a>Attributs
| Nom | Type | Description |
| --- | --- | --- |
| **Version**<br/><br/> Requis |**xs:decimal** |La version de présélection. Les restrictions suivantes s’appliquent : xs:fractionDigits value="1"  et xs:minInclusive value="1" Par exemple, **version="1.0"**. |

## <a name="a-nameencodinga-encoding"></a><a name="Encoding"></a> Encodage
Contient une séquence des éléments suivants.  

### <a name="elements"></a>Éléments
| Nom | Type | Description |
| --- | --- | --- |
| **H264Video** |[H264Video](media-services-mes-schema.md#H264Video) |Paramètres d’encodage vidéo H.264. |
| **AACAudio** |[AACAudio](media-services-mes-schema.md#AACAudio) |Paramètres d’encodage audio AAC. |
| **BmpImage** |[BmpImage](media-services-mes-schema.md#BmpImage) |Paramètres de l’image Bmp. |
| **PngImage** |[PngImage](media-services-mes-schema.md#PngImage) |Paramètres de l’image Png. |
| **JpgImage** |[JpgImage](media-services-mes-schema.md#JpgImage) |Paramètres de l’image Jpg. |

## <a name="a-nameh264videoa-h264video"></a><a name="H264Video"></a> H264Video
### <a name="elements"></a>Éléments
| Nom | Type | Description |
| --- | --- | --- |
| **TwoPass**<br/><br/> minOccurs="0" |**xs:boolean** |Actuellement, seul l’encodage en une étape est pris en charge. |
| **KeyFrameInterval**<br/><br/> minOccurs="0"<br/><br/> **default="00:00:02"** |**xs:time** |Détermine l’espacement (par défaut) entre les trames IDR. |
| **SceneChangeDetection**<br/><br/> minOccurs="0"<br/><br/> default=”false” |**xs:boolean** |Si la valeur est définie sur true, l’encodeur essaie de détecter le changement de scène dans la vidéo et insère une trame IDR. |
| **Complexité**<br/><br/> minOccurs="0"<br/><br/> default="Balanced" |**xs:string** |Contrôle le compromis entre vitesse d’encodage et qualité de la vidéo. Peut être une des valeurs suivantes : **Vitesse**, **Équilibré** ou **Qualité**<br/><br/> Par défaut : **Équilibré** |
| **SyncMode**<br/><br/> minOccurs="0" | |Cette fonctionnalité sera présentée dans les versions à venir. |
| **H264Layers**<br/><br/> minOccurs="0" |[H264Layers](media-services-mes-schema.md#H264Layers) |Collection de couches vidéo de sortie. |

## <a name="a-nameh264layersa-h264layers"></a><a name="H264Layers"></a> H264Layers
### <a name="elements"></a>Éléments
| Nom | Type | Description |
| --- | --- | --- |
| **H264Layer**<br/><br/> minOccurs="0" maxOccurs="unbounded" |[H264Layer](media-services-mes-schema.md#H264Layer) |Collection de couches H264. |

## <a name="a-nameh264layera-h264layer"></a><a name="H264Layer"></a> H264Layer
> [!NOTE]
> Les limites vidéo sont basées sur les valeurs décrites dans le tableau [Niveaux H264](https://en.wikipedia.org/wiki/H.264/MPEG-4_AVC#Levels).  
> 
> 

### <a name="elements"></a>Éléments
| Nom | Type | Description |
| --- | --- | --- |
| **Profil**<br/><br/> minOccurs="0"<br/><br/> default=”Auto” |**xs:string** |Peut être une des valeurs  **xs:string** suivantes : **Auto**, **Ligne de base**, **Principal**, **Élevé**. |
| **Niveau**<br/><br/> minOccurs="0"<br/><br/> default=”Auto” |**xs:string** | |
| **Bitrate**<br/><br/> minOccurs="0" |**xs:int** |Le débit utilisé pour cette couche vidéo, spécifiée en kbit/s. |
| **MaxBitrate**<br/><br/> minOccurs="0" |**xs:int** |Le débit maximal utilisé pour cette couche vidéo, spécifiée en kbit/s. |
| **BufferWindow**<br/><br/> minOccurs="0"<br/><br/> default="00:00:05" |**xs:time** |Longueur de la mémoire tampon vidéo. |
| **Largeur**<br/><br/> minOccurs="0" |**xs:int** |La largeur de l’image vidéo de sortie, en pixels.<br/><br/> Notez qu’actuellement vous devez spécifier la largeur et la hauteur. La largeur et la hauteur doivent être des nombres pairs. |
| **Hauteur**<br/><br/> minOccurs="0" |**xs:int** |La hauteur de l’image vidéo de sortie, en pixels.<br/><br/> Notez qu’actuellement vous devez spécifier la largeur et la hauteur. La largeur et la hauteur doivent être des nombres pairs.|
| **BFrames**<br/><br/> minOccurs="0" |**xs:int** |Nombre de trames B entre les trames de référence. |
| **ReferenceFrames**<br/><br/> minOccurs="0"<br/><br/> default=”3” |**xs:int** |Nombre de trames de référence dans un GOP. |
| **EntropyMode**<br/><br/> minOccurs="0"<br/><br/> default=”Cabac” |**xs:string** |Il doit s’agir de l’une des valeurs suivantes : **Cabac** ou **Cavlc**. |
| **FrameRate**<br/><br/> minOccurs="0" |nombre rationnel |Détermine la fréquence d’images de la vidéo de sortie. Utilisez « 0/1 » par défaut pour permettre à l’encodeur d’utiliser la même fréquence d’images que l’entrée vidéo. Les valeurs autorisées doivent idéalement être des fréquences d’images vidéo courantes, comme indiqué ci-dessous. Cependant, toute valeur rationnelle est autorisée. Par exemple, 1/1 correspondrait à 1 i/s et serait valide.<br/><br/> - 12/1  (12 i/s)<br/><br/> - 15/1 (15 i/s)<br/><br/> - 24/1 (24 i/s)<br/><br/> - 24000/1001 (23,976 i/s)<br/><br/> - 25/1 (25 i/s)<br/><br/>  - 30/1 (30 i/s)<br/><br/> - 30000/1001 (29,97 i/s) |
| **AdaptiveBFrame**<br/><br/> minOccurs="0" |**xs:boolean** |Copie depuis l’encodeur multimédia Azure |
| **Tranches**<br/><br/> minOccurs="0"<br/><br/> default="0" |**xs:int** |Détermine le nombre de tranches dans lequel une trame est divisée. Nous vous recommandons d'utiliser la valeur par défaut. |

## <a name="a-nameaacaudioa-aacaudio"></a><a name="AACAudio"></a> AACAudio
 Contient une séquence des éléments et groupes suivants.  

 Pour plus d’informations sur AAC, consultez [AAC](https://en.wikipedia.org/wiki/Advanced_Audio_Coding).  

### <a name="elements"></a>Éléments
| Nom | Type | Description |
| --- | --- | --- |
| **Profil**<br/><br/> minOccurs="0 "<br/><br/> default="AACLC" |**xs:string** |Il doit s’agir de l’une des valeurs suivantes : **AACLC**, **HEAACV1** ou **HEAACV2**. |

### <a name="attributes"></a>Attributs
| Nom | Type | Description |
| --- | --- | --- |
| **Condition** |**xs:string** |Pour forcer l’encodeur à produire un élément multimédia contenant une piste audio en mode silencieux lorsque l’entrée ne comporte pas de son, spécifiez la valeur « InsertSilenceIfNoAudio ».<br/><br/> Par défaut, si vous envoyez à l’encodeur une entrée contenant uniquement de la vidéo (sans contenu audio), l’élément multimédia de sortie regroupera les fichiers qui contiennent uniquement des données vidéo. Certains lecteurs ne sont peut-être pas capables de gérer ces flux de sortie. Dans ce cas, vous pouvez utiliser ce paramètre pour forcer l’encodeur à ajouter à la sortie une piste audio en mode silencieux. |

### <a name="groups"></a>Groupes
| Référence | Description |
| --- | --- |
| [AudioGroup](media-services-mes-schema.md#AudioGroup)<br/><br/> minOccurs="0" |Consultez la description de [AudioGroup](media-services-mes-schema.md#AudioGroup) pour connaître le nombre approprié de canaux, le taux d’échantillonnage et le taux de bits qui peuvent être configurés pour chaque profil. |

## <a name="a-nameaudiogroupa-audiogroup"></a><a name="AudioGroup"></a> AudioGroup
Pour plus d’informations sur les valeurs qui sont valides pour chaque profil, consultez le tableau « Détails des codecs audio ».  

### <a name="elements"></a>Éléments
| Nom | Type | Description |
| --- | --- | --- |
| **Canaux**<br/><br/> minOccurs="0" |**xs:int** |Le nombre de canaux audio encodés. Les valeurs suivantes sont valides : 1, 2, 5, 6 et 8.<br/><br/> Par défaut  : 2. |
| **SamplingRate**<br/><br/> minOccurs="0" |**xs:int** |Le taux d’échantillonnage audio, spécifié en Hz. |
| **Bitrate**<br/><br/> minOccurs="0" |**xs:int** |Le débit utilisé pour l’encodage de l’audio, spécifié en kbit/s. |

### <a name="audio-codec-details"></a>Détails du codec audio
Codec audio|Détails  
-----------------|---  
**AACLC**|1:<br/><br/> - 11025 : 8 &lt;= bitrate &lt; 16<br/><br/> - 12000 : 8 &lt;= bitrate &lt; 16<br/><br/> - 16000 : 8 &lt;= bitrate &lt;32<br/><br/>- 22050 : 24 &lt;= bitrate &lt; 32<br/><br/> - 24000 : 24 &lt;= bitrate &lt; 32<br/><br/> - 32000 : 32 &lt;= bitrate &lt;= 192<br/><br/> - 44100 : 56 &lt;= bitrate &lt;= 288<br/><br/> - 48000 : 56 &lt;= bitrate &lt;= 288<br/><br/> - 88200 : 128 &lt;= bitrate &lt;= 288<br/><br/> - 96000 : 128 &lt;= bitrate &lt;= 288<br/><br/> 2 :<br/><br/> - 11025 : 16 &lt;= bitrate &lt; 24<br/><br/> - 12000 : 16 &lt;= bitrate &lt; 24<br/><br/> - 16000 : 16 &lt;= bitrate &lt; 40<br/><br/> - 22050 : 32 &lt;= bitrate &lt; 40<br/><br/> - 24000 : 32 &lt;= bitrate &lt; 40<br/><br/> - 32000 :  40 &lt;= bitrate &lt;= 384<br/><br/> - 44100 : 96 &lt;= bitrate &lt;= 576<br/><br/> - 48000 : 96 &lt;= bitrate &lt;= 576<br/><br/> - 88200 : 256 &lt;= bitrate &lt;= 576<br/><br/> - 96000 : 256 &lt;= bitrate &lt;= 576<br/><br/> 5/6:<br/><br/> - 32000 : 160 &lt;= bitrate &lt;= 896<br/><br/> - 44100 : 240 &lt;= bitrate &lt;= 1024<br/><br/> - 48000 : 240 &lt;= bitrate &lt;= 1024<br/><br/> - 88200 : 640 &lt;= bitrate &lt;= 1024<br/><br/> - 96000 : 640 &lt;= bitrate &lt;= 1024<br/><br/> 8:<br/><br/> - 32000 : 224 &lt;= bitrate &lt;= 1024<br/><br/> - 44100 : 384 &lt;= bitrate &lt;= 1024<br/><br/> - 48000 : 384 &lt;= bitrate &lt;= 1024<br/><br/> - 88200 : 896 &lt;= bitrate &lt;= 1024<br/><br/> - 96000 : 896 &lt;= bitrate &lt;= 1024  
**HEAACV1**|1:<br/><br/> - 22050 : bitrate = 8<br/><br/> - 24000 : 8 &lt;= bitrate &lt;= 10<br/><br/> - 32000 : 12 &lt;= bitrate &lt;= 64<br/><br/> - 44100 : 20 &lt;= bitrate &lt;= 64<br/><br/> - 48000 : 20 &lt;= bitrate &lt;= 64<br/><br/> - 88200 : bitrate = 64<br/><br/> 2 :<br/><br/> - 32000 : 16 &lt;= bitrate &lt;= 128<br/><br/> - 44100 : 16 &lt;= bitrate &lt;= 128<br/><br/> - 48000 : 16 &lt;= bitrate &lt;= 128<br/><br/> - 88200 : 96 &lt;= bitrate &lt;= 128<br/><br/> - 96000 : 96 &lt;= bitrate &lt;= 128<br/><br/> 5/6:<br/><br/> - 32000 : 64 &lt;= bitrate &lt;= 320<br/><br/> - 44100 : 64 &lt;= bitrate &lt;= 320<br/><br/> - 48000 : 64 &lt;= bitrate &lt;= 320<br/><br/> - 88200 : 256 &lt;= bitrate &lt;= 320<br/><br/> - 96000 : 256 &lt;= bitrate &lt;= 320<br/><br/> 8:<br/><br/> - 32000 : 96 &lt;= bitrate &lt;= 448<br/><br/> - 44100 : 96 &lt;= bitrate &lt;= 448<br/><br/> - 48000 : 96 &lt;= bitrate &lt;= 448<br/><br/> - 88200 : 384 &lt;= bitrate &lt;= 448<br/><br/> - 96000 : 384 &lt;= bitrate &lt;= 448  
**HEAACV2**|2 :<br/><br/> - 22050 : 8 &lt;= bitrate &lt;= 10<br/><br/> - 24000 : 8 &lt;= bitrate &lt;= 10<br/><br/> - 32000 : 12 &lt;= bitrate &lt;= 64<br/><br/> - 44100 : 20 &lt;= bitrate &lt;= 64<br/><br/> - 48000 : 20 &lt;= bitrate &lt;= 64<br/><br/> - 88200 : 64 &lt;= bitrate &lt;= 64  
  

## <a name="a-nameclipa-clip"></a><a name="Clip"></a> Séquence
### <a name="attributes"></a>Attributs
| Nom | Type | Description |
| --- | --- | --- |
| **StartTime** |**xs:duration** |Spécifie l’heure de début d’une présentation. La valeur de StartTime doit correspondre aux horodatages absolus de la vidéo d'entrée. Par exemple, si la première image de la vidéo d'entrée a un horodatage de 12:00:10.000, la valeur de StartTime doit être égale ou supérieure à 12:00:10.000. |
| **Durée** |**xs:duration** |Spécifie la durée d’une présentation (par exemple, apparition d’une superposition sur la vidéo). |

## <a name="a-nameoutputa-output"></a><a name="Output"></a> Sortie
### <a name="attributes"></a>Attributs
| Nom | Type | Description |
| --- | --- | --- |
| **FileName** |**xs:string** |Le nom du fichier de sortie.<br/><br/> Vous pouvez utiliser les macros décrites dans le tableau suivant pour générer les noms de fichier de sortie. Par exemple :<br/><br/> **"Outputs": [      {       "FileName": "{Basename}*{Resolution}*{Bitrate}.mp4",       "Format": {         "Type": "MP4Format"       }     }   ]** |

### <a name="macros"></a>Macros
| Macro | Description |
| --- | --- |
| **{Basename}** |Si vous effectuez l’encodage de VoD, {Basename} est composé des 32 premiers caractères de la propriété AssetFile.Name du fichier principal de la ressource d’entrée.<br/><br/> Si la ressource d’entrée est une archive en direct, {Basename} est dérivé des attributs trackName dans le manifeste du serveur. Si vous envoyez un travail de sous-clip avec TopBitrate, comme dans : « <VideoStream\>TopBitrate</VideoStream\> », et que le fichier de sortie contient de la vidéo, {Basename} est composé des 32 premiers caractères du trackName de la couche vidéo avec le débit le plus élevé.<br/><br/> Si vous envoyez plutôt un travail de sous-clip avec tous les débits d’entrée, comme dans : « <VideoStream\>*</VideoStream\> », et que le fichier de sortie contient de la vidéo, {Basename} est composé des 32 premiers caractères du trackName de la couche vidéo correspondante. |
| **{Codec}** |Correspond à « H264 » pour la vidéo et « AAC » pour l’audio. |
| **{Bitrate}** |Le débit vidéo cible si le fichier de sortie contient de la vidéo et de l’audio, ou le débit audio cible si le fichier de sortie contient uniquement des données audio. La valeur utilisée est le débit en kbit/s. |
| **{Channel}** |Nombre de canaux audio si le fichier contient des données audio. |
| **{Width}** |Largeur de la vidéo en pixels dans le fichier de sortie, si le fichier contient de la vidéo. |
| **{Height}** |Hauteur de la vidéo en pixels dans le fichier de sortie, si le fichier contient de la vidéo. |
| **{Extension}** |Hérite de la propriété « Type » du fichier de sortie. Le nom de fichier de sortie aura une des extensions suivantes : « mp4 », « ts », « jpg », « png » ou « bmp ». |
| **{Index}** |Obligatoire pour la miniature. Ne doit être présent qu’une seule fois. |

## <a name="a-namevideoa-video-complex-type-inherits-from-codec"></a><a name="Video"></a> Vidéo (le type complexe hérite de Codec)
### <a name="attributes"></a>Attributs
| Nom | Type | Description |
| --- | --- | --- |
| **Start** |**xs:string** | |
| **Étape** |**xs:string** | |
| **Plage** |**xs:string** | |
| **PreserveResolutionAfterRotation** |**xs:boolean** |Pour une explication détaillée, consultez la section suivante : [PreserveResolutionAfterRotation](media-services-mes-schema.md#PreserveResolutionAfterRotation) |

### <a name="a-namepreserveresolutionafterrotationa-preserveresolutionafterrotation"></a><a name="PreserveResolutionAfterRotation"></a> PreserveResolutionAfterRotation
Il est recommandé d’utiliser l’indicateur PreserveResolutionAfterRotation en combinaison avec les valeurs de résolution exprimées en pourcentage (Width=”100%” , Height = “100%”).  

Par défaut, les paramètres de résolution d’encodage (largeur, hauteur) dans les paramètres prédéfinis de Media Encoder Standard (MES) sont destinés aux vidéos avec une rotation de 0 degré. Par exemple, si votre vidéo d’entrée a une résolution de 1280 x 720 avec une rotation de zéro degré, les paramètres prédéfinis assurent que la sortie a la même résolution. Voir l’image ci-dessous.  

![MESRoation1](./media/media-services-shemas/media-services-mes-roation1.png) 

Toutefois, cela signifie que si la vidéo d’entrée a été capturée avec une rotation non nulle (par ex. un smartphone ou une tablette à la verticale), MES applique par défaut les paramètres de résolution d’encodage (largeur, hauteur) à l’entrée vidéo, puis compense la rotation. Par exemple, reportez-vous à l’image ci-dessous. La présélection utilise les valeurs Width = “100%”, Height = “100%”, que MES interprète comme une exigence que la sortie soit de 1280 pixels de large et de 720 pixels de haut. Après rotation de la vidéo, MES réduit ensuite l’image pour tenir dans cette fenêtre, ce qui cause l’affichage de bandes sur la gauche et la droite.  

![MESRoation2](./media/media-services-shemas/media-services-mes-roation2.png) 

Si ce comportement n’est pas celui souhaité, vous pouvez utiliser l’indicateur PreserveResolutionAfterRotation et lui affecter la valeur « true » (la valeur par défaut est « false »). Par conséquent, si votre présélection a Width = “100%”, Height = “100%” et que PreserveResolutionAfterRotation a la valeur « true », une vidéo d’entrée qui fait 1280 pixels de large et 720 pixels de hauteur avec une rotation de 90 degrés produit une sortie avec une rotation de zéro degré, mais faisant 720 pixels de large et 1280 pixels de hauteur. Voir l’image ci-dessous.  

![MESRoation3](./media/media-services-shemas/media-services-mes-roation3.png) 

## <a name="a-nameformatgroupa-formatgroup-group"></a><a name="FormatGroup"></a> FormatGroup (groupe)
### <a name="elements"></a>Éléments
| Nom | Type | Description |
| --- | --- | --- |
| **BmpFormat** |**BmpFormat** | |
| **PngFormat** |**PngFormat** | |
| **JpgFormat** |**JpgFormat** | |

## <a name="a-namebmplayera-bmplayer"></a><a name="BmpLayer"></a> BmpLayer
### <a name="element"></a>Élément
| Nom | Type | Description |
| --- | --- | --- |
| **Largeur**<br/><br/> minOccurs="0" |**xs:int** | |
| **Hauteur**<br/><br/> minOccurs="0" |**xs:int** | |

### <a name="attributes"></a>Attributs
| Nom | Type | Description |
| --- | --- | --- |
| **Condition** |**xs:string** | |

## <a name="a-namepnglayera-pnglayer"></a><a name="PngLayer"></a> PngLayer
### <a name="element"></a>Élément
| Nom | Type | Description |
| --- | --- | --- |
| **Largeur**<br/><br/> minOccurs="0" |**xs:int** | |
| **Hauteur**<br/><br/> minOccurs="0" |**xs:int** | |

### <a name="attributes"></a>Attributs
| Nom | Type | Description |
| --- | --- | --- |
| **Condition** |**xs:string** | |

## <a name="a-namejpglayera-jpglayer"></a><a name="JpgLayer"></a> JpgLayer
### <a name="element"></a>Élément
| Nom | Type | Description |
| --- | --- | --- |
| **Largeur**<br/><br/> minOccurs="0" |**xs:int** | |
| **Hauteur**<br/><br/> minOccurs="0" |**xs:int** | |
| **Qualité**<br/><br/> minOccurs="0" |**xs:int** |Valeurs valides : 1 (moins bonne) - 100 (meilleure) |

### <a name="attributes"></a>Attributs
| Nom | Type | Description |
| --- | --- | --- |
| **Condition** |**xs:string** | |

## <a name="a-namepnglayersa-pnglayers"></a><a name="PngLayers"></a> PngLayers
### <a name="elements"></a>Éléments
| Nom | Type | Description |
| --- | --- | --- |
| **PngLayer**<br/><br/> minOccurs="0" maxOccurs="unbounded" |[PngLayer](media-services-mes-schema.md#PngLayer) | |

## <a name="a-namebmplayersa-bmplayers"></a><a name="BmpLayers"></a> BmpLayers
### <a name="elements"></a>Éléments
| Nom | Type | Description |
| --- | --- | --- |
| **BmpLayer**<br/><br/> minOccurs="0" maxOccurs="unbounded" |[BmpLayer](media-services-mes-schema.md#BmpLayer) | |

## <a name="a-namejpglayersa-jpglayers"></a><a name="JpgLayers"></a> JpgLayers
### <a name="elements"></a>Éléments
| Nom | Type | Description |
| --- | --- | --- |
| **JpgLayer**<br/><br/> minOccurs="0" maxOccurs="unbounded" |[JpgLayer](media-services-mes-schema.md#JpgLayer) | |

## <a name="a-namebmpimagea-bmpimage-complex-type-inherits-from-video"></a><a name="BmpImage"></a> BmpImage (le type complexe hérite de Video)
### <a name="elements"></a>Éléments
| Nom | Type | Description |
| --- | --- | --- |
| **PngLayers**<br/><br/> minOccurs="0" |[PngLayers](media-services-mes-schema.md#PngLayers) |Couches PNG |

## <a name="a-namejpgimagea-jpgimage-complex-type-inherits-from-video"></a><a name="JpgImage"></a> JpgImage (le type complexe hérite de Video)
### <a name="elements"></a>Éléments
| Nom | Type | Description |
| --- | --- | --- |
| **PngLayers**<br/><br/> minOccurs="0" |[PngLayers](media-services-mes-schema.md#PngLayers) |Couches PNG |

## <a name="a-namepngimagea-pngimage-complex-type-inherits-from-video"></a><a name="PngImage"></a> PngImage (le type complexe hérite de Video)
### <a name="elements"></a>Éléments
| Nom | Type | Description |
| --- | --- | --- |
| **PngLayers**<br/><br/> minOccurs="0" |[PngLayers](media-services-mes-schema.md#PngLayers) |Couches PNG |

## <a name="examples"></a>Exemples
Pour voir des exemples de présélections XML qui sont générées conformément à ce schéma, consultez [Présélections de tâches pour MES (Media Encoder Standard)](media-services-mes-presets-overview.md).

## <a name="next-steps"></a>Étapes suivantes
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fournir des commentaires
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


