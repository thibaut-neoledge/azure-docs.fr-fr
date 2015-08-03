<properties 
	pageTitle="Codecs et formats Azure Media Encoder" 
	description="Cette rubrique fournit une vue d’ensemble des codecs et formats Azure Media Encoder." 
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
	ms.date="06/29/2015" 
	ms.author="juliako"/>

#Codecs et formats Azure Media Encoder

Les encodeurs compressent du contenu multimédia numérique à l'aide de codecs. Ils proposent généralement différents paramètres qui vous permettent de spécifier les propriétés du contenu multimédia généré, par exemple les codecs utilisés, le format de fichier, la résolution et la vitesse de transmission. Les formats de fichiers sont des conteneurs qui contiennent la vidéo compressée, ainsi que des informations sur les codecs qui ont été utilisés pour compresser la vidéo.

Les codecs possèdent deux composants : un pour compresser les fichiers multimédias numériques pour la transmission et l’autre pour décompresser les fichiers multimédias numériques pour la lecture. Il existe des codecs audio qui compressent et décompressent le contenu audio et des codecs vidéo qui compressent et décompressent le contenu vidéo. Les codecs peuvent utiliser la compression avec ou sans perte. Les codecs sans perte conservent l’ensemble des informations lors de la compression. Lorsque le fichier est décompressé, le résultat est un fichier qui est identique au contenu d’entrée, ce qui rend les codecs sans perte particulièrement adaptés à l’archivage et au stockage. Les codecs avec perte perdent certaines des informations lors de l’encodage et génèrent des fichiers plus petits (que l’original) au détriment de la qualité vidéo. Ils conviennent à la diffusion en continu via internet. Les deux principaux codecs utilisés par Encodeur multimédia Azure pour l’encodage sont H.264 et VC-1. D’autres codecs peuvent être disponibles dans notre écosystème d’encodeurs partenaires.

Il est important de bien comprendre la différence entre les codecs et les formats de fichiers. Les codecs sont les logiciels qui implémentent les algorithmes de compression/décompression, tandis que les formats de fichiers sont des conteneurs qui contiennent la vidéo compressée. Pour plus d’informations, consultez le billet [Encoding versus Packaging](http://blog-ndrouin.azurewebsites.net/streaming-media-terminology-explained/).

Ce document contient une liste des formats de fichiers d'importation et d'exportation les plus courants que vous pouvez utiliser avec Azure Media Encoder.


[Formats d'importation Azure Media Encoder ](#import_formats)

[Formats d'exportation Azure Media Encoder](#export_formats)


##<a id="import_formats"></a>Formats d'importation Azure Media Encoder 

La section suivante répertorie les codecs et les formats de fichiers pris en charge pour l'importation.

###Codecs vidéo

- H.264 (profils Baseline, Main et High)
- MPEG-1 (y compris MPEG-PS)
- MPEG-2 (profils Simple et Main et profil 4:2:2)
- MPEG-4 v2 (profil Simple Visual et profil Advanced Simple)
- VC-1 (profils Simple, Main et Advanced)
- Windows Media Video (profils Simple, Main et Advanced)
- DV (DVC, DVHD, DVSD, DVSL)
- Grass Valley HQ/HQX
 
###Codecs audio

- AC-3 (Dolby Digital audio)
- AAC (AAC-LC, HE-AAC v1 avec AAC-LC core et HE-AAC v2 avec AAC-LC core)
- MP3
- Windows Media Audio 9 (Windows Media Audio Standard, Windows Media Audio Professionnel et Windows Media Audio sans perte)

###Formats de fichiers vidéo
 
<table border="1">
<tr><th>Format de fichier</th><th>Extensions de fichiers</th></tr>
<tr><td>3GPP, 3GPP2</td><td>.3gp, .3g2, .3gp2</td></tr>
<tr><td>Advanced Systems Format (ASF)</td><td>.asf</td></tr>
<tr><td>Advanced Video Coding High Definition (AVCHD) [MPEG-2 Transport Stream]</td><td>.mts, .m2ts</td></tr>
<tr><td>Audio-Video Interleaved (AVI)</td><td>.avi</td></tr>
<tr><td>Magnétoscope numérique MPEG-2 (MOD)</td><td>.mod</td></tr>
<tr><td>Fichier de flux de transport (TS) DVD</td><td>.ts</td></tr>
<tr><td>Fichier d'objet vidéo (VOB) DVD</td><td>.vob</td></tr>
<tr><td>Fichier de codec Expression Encoder Screen Capture</td><td>.xesc</td></tr>
<tr><td>MP4</td><td>.mp4</td></tr>
<tr><td>Flux système MPEG-1</td><td>.mpeg, .mpg</td></tr>
<tr><td>Fichier vidéo MPEG-2</td><td>.m2v</td></tr>
<tr><td>Format de fichier de diffusion en continu lisse (PIFF&#160;1.3)</td><td>.ismv</td></tr>
<tr><td>Windows Media Video (WMV)</td><td>.wmv</td></tr>
</table>

Certains formats non compressés sont pris en charge. Pour plus d’informations, consultez [Formats vidéo non compressés pris en charge](#uncompressed)

###Formats de fichiers audio

<table border="1">
<tr><th>Format de fichier</th><th>Extensions de fichiers</th></tr>
<tr><td>Audio AC-3 (Dolby Digital)</td><td>.ac3</td></tr>
<tr><td>Audio Interchange File Format (AIFF)</td><td>.aiff</td></tr>
<tr><td>Broadcast Wave Format</td><td>.bwf</td></tr>
<tr><td>MP3 (MPEG-1 Audio Layer 3)</td><td>.mp3</td></tr>
<tr><td>MP4 audio</td><td>.m4A</td></tr>
<tr><td>Livre audio MPEG-4</td><td>.m4b</td></tr>
<tr><td>Fichier WAVE</td><td>.wav</td></tr>
<tr><td>Windows Media Audio</td><td>.wma</td></tr>   
</table>

###Formats de fichiers image

<table border="1">
<tr><th>Format de fichier</th><th>Extensions de fichiers</th></tr>
<tr><td>Bitmap</td><td>.bmp</td></tr>
<tr><td>GIF, GIF animé</td><td>.gif</td></tr>
<tr><td>JPEG</td><td>.jpeg, .jpg</td></tr>
<tr><td>PNG</td><td>.png</td></tr>
<tr><td>TIFF</td><td>.tif</td></tr>
<tr><td>WPF Canvas XAML</td><td>.xaml</td></tr>
</table>


##<a id="export_formats"></a>Formats d'exportation Azure Media Encoder

Le tableau suivant répertorie les codecs et les formats de fichiers pris en charge pour l'exportation.


<table border="1">
<tr><th>Format de fichier</th><th>Codec vidéo</th><th>Codec audio</th></tr>
<tr><td>Windows Media (*.wmv; *.wma)</td><td>VC-1 (profils Advanced, Main et Simple)</td><td>Windows Media Audio Standard, Windows Media Audio Professional, Windows Media Audio Voice, Windows Media Audio sans perte</td></tr>
<tr><td>MP4 (*.mp4)</td><td>H.264 (profils High, Main et Baseline)</td><td>AAC-LC, HE-AAC v1, HE-AAC v2, Dolby Digital Plus</td></tr>
<tr><td>Format de fichier de diffusion en continu lisse (PIFF 1,1) (*.ismv&#160;; *.isma)</td><td>VC-1 (profil Advanced)<br/><br/>
H.264 (profils High, Main et Baseline)</td><td>Windows Media Audio Standard, Windows Media Audio Professional<br/><br/>
AAC-LC, HE-AAC v1, HE-AAC v2</td></tr>
</table>

Pour plus d’informations sur les codecs et les filtres supplémentaires pris en charge dans Media Services, consultez [Filtres Windows DirectShow](https://msdn.microsoft.com/library/windows/desktop/dd375464.aspx).

##<a id="uncompressed"></a>Formats vidéo non compressés pris en charge 

Azure Media Services prend en charge l'importation de données vidéo non compressées.

Voici une liste non exhaustive des formats non compressés pris en charge.

<table border="1">
<tr><th>Format vidéo non compressé</th><th>Description</th></tr>
<tr><td>Données non compressées de format YVU9 standard</td><td>Format YUV planaire. Un échantillon Y à chaque pixel, un échantillon U et V à chaque quatrième pixel horizontalement sur chaque ligne&#160;; un échantillon Y sur chaque ligne verticale, un échantillon U et V à chaque quatrième ligne verticale. 9&#160;bits par pixel.</td></tr>
<tr><td>Données au format YUV 411</td><td>Un échantillon Y à chaque pixel, un échantillon U et V à chaque quatrième pixel horizontalement sur chaque ligne&#160;; chaque ligne verticale échantillonnée. L'ordre des octets (inférieur en premier) est U0, Y0, V0, Y1, U4, Y2, V4, Y3, Y4, Y5, Y6, Y7, où le suffixe 0 est le pixel de gauche et les nombres croissants correspondent aux pixels croissants de gauche à droite. Chaque bloc de 12&#160;octets correspond à 8&#160;pixels d’une image.</td></tr>
<tr><td>Données au format Y41P</td><td>Un échantillon Y à chaque pixel, un échantillon U et V à chaque quatrième pixel horizontalement sur chaque ligne&#160;; chaque ligne verticale échantillonnée. L'ordre des octets (inférieur en premier) est U0, Y0, V0, Y1, U4, Y2, V4, Y3, Y4, Y5, Y6, Y7, où le suffixe 0 est le pixel de gauche et les nombres croissants correspondent aux pixels croissants de gauche à droite. Chaque bloc de 12&#160;octets correspond à 8&#160;pixels d’une image.</td></tr>
<tr><td>Données au format YUY2</td><td>Identique à UYVY mais avec un ordre des pixels différent. L'ordre des octets (inférieur en premier) est Y0, U0, Y1, V0, Y2, U2, Y3, V2, Y4, U4, Y5, V4, où le suffixe 0 est le pixel de gauche et les nombres croissants correspondent aux pixels croissants de gauche à droite. Chaque bloc de 4&#160;octets correspond à 2&#160;pixels d’une image.</td></tr>
<tr><td>Données au format YVYU</td><td>Format YUV compressé. Identique à UYVY mais avec un ordre des pixels différent. L'ordre des octets (inférieur en premier) est Y0, V0, Y1, U0, Y2, V2, Y3, U2, Y4, V4, Y5, U4, où le suffixe 0 est le pixel de gauche et les nombres croissants correspondent aux pixels croissants de gauche à droite. Chaque bloc de 4&#160;octets correspond à 2&#160;pixels d’une image.</td></tr>
<tr><td>Données au format UYVY</td><td>Format YUV compressé. Un échantillon Y à chaque pixel, un échantillon U et V à chaque deuxième pixel horizontalement sur chaque ligne&#160;; chaque ligne verticale échantillonnée. Le plus populaire des différents formats YUV 4:2:2. L'ordre des octets (inférieur en premier) est U0, Y0, V0, Y1, U2, Y2, V2, Y3, U4, Y4, V4, Y5, où le suffixe 0 est le pixel de gauche et les nombres croissants correspondent aux pixels croissants de gauche à droite. Chaque bloc de 4&#160;octets correspond à 2&#160;pixels d’une image.</td></tr>
<tr><td>Données au format YUV 211</td><td>Format YUV compressé. Un échantillon Y à chaque pixel, un échantillon U et V à chaque second pixel horizontalement sur chaque ligne&#160;; chaque ligne verticale échantillonnée. L'ordre des octets (inférieur en premier) est Y0, U0, Y2, V0, Y4, U4, Y6, V4, Y8, U8, Y10, V8, où le suffixe 0 est le pixel de gauche et les nombres croissants correspondent aux pixels croissants de gauche à droite. Chaque bloc de 4&#160;octets correspond à 4&#160;pixels d’une image.</td></tr>
<tr><td>Format Cirrus Logic Jr YUV&#160;411</td><td>Format Cirrus Logic Jr YUV&#160;411 avec moins de 8&#160;bits par échantillon Y, U et V. Un échantillon Y à chaque pixel, un échantillon U et V à chaque quatrième pixel horizontalement sur chaque ligne&#160;; chaque ligne verticale échantillonnée.</td></tr>
<tr><td>Format Indeo-produced YVU9</td><td>Format Indeo-produced YVU9 avec informations supplémentaires sur les différences par rapport au dernier frame. 9,5&#160;bits par pixel mais déclaré comme 9.</td></tr>
</table>

<!---HONumber=July15_HO4-->