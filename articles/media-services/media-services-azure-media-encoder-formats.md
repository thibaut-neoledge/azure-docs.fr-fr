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
	ms.date="08/24/2015"
	ms.author="juliako"/>

#Codecs et formats Azure Media Encoder

Ce document contient une liste des formats de fichiers d’entrée et de sortie et de codecs les plus courants que vous pouvez utiliser avec Azure Media Encoder.


##Formats de fichier vidéo d’entrée (conteneurs)
 
Format de fichier (extensions de fichier)|Pris en charge
---|---
3GPP, 3GPP2 (.3gp, .3g2, .3gp2) |Oui
Advanced Systems Format (ASF) (.asf) |Oui
Advanced Video Coding High Definition (AVCHD) [MPEG-2 Transport Stream] (.mts, .m2ts) |Oui
Audio-Video Interleaved (AVI) (.avi) |Oui
Magnétoscope numérique MPEG-2 (MOD) (.mod) |Oui
Fichier de flux de transport (TS) DVD (.ts) |Oui
Fichier d’objet vidéo (VOB) DVD (.vob) |Oui
Fichier de codec Expression Encoder Screen Capture (.xesc) |Oui
MP4 (.mp4) |Oui
Flux système MPEG-1 (.mpeg, .mpg) |Oui
Fichier vidéo MPEG-2 (.m2v) |Oui
Format de fichier de diffusion en continu lisse (PIFF 1.3) (.ismv) |Oui
Windows Media Video (WMV) (.wmv) |Oui
Adobe® Flash® F4V |Non		
MXF/SMPTE 377M |Limité 
GXF |Non		 
[Enregistrement vidéo numérique Microsoft (DVR-MS)](https://msdn.microsoft.com/library/windows/desktop/dd692984)|Non
Matroska/WebM |Non

Certains formats non compressés sont pris en charge. Pour plus d’informations, voir [Formats vidéo non compressés pris en charge](#uncompressed)

##Formats de fichiers audio d’entrée

Format de fichier (extensions de fichier)|Pris en charge
---|---
AC-3 (Dolby Digital audio) (.ac3)|Oui
Audio Interchange File Format (AIFF) (.aiff)|Oui
Broadcast Wave Format (.bwf)|Oui
MP3 (MPEG-1 Audio Layer 3) (.mp3)|Oui
MP4 audio (.m4A)|Oui
Livre audio MPEG-4 (.m4b)|Oui
Fichier WAVE (.wav)|Oui
Windows Media Audio (.wma)|Oui


##Codecs vidéo d’entrée

Codecs vidéo d’entrée|Pris en charge
---|--- 
H.264 (profils Baseline, Main et High) |Oui
AVC 8 bits/10 bits, jusqu'à 4:2:2, y compris AVCIntra |Uniquement 8 bits 4:2:0
Avid DNxHD (dans MXF) |Non
DVCPro/DVCProHD (dans MXF) |Non
JPEG2000 |Non
MPEG-2 (profils Simple et Main et profil 4:2:2) |Jusqu’à un profil de 4:2:2
MPEG-1 (y compris MPEG-PS) |Oui
Windows Media Video/VC-1 |Oui
Canopus HQ/HQX |Oui
MPEG-4 v2 (profil Simple Visual et profil Advanced Simple) |Oui
[Theora](https://en.wikipedia.org/wiki/Theora) |Non
VC-1 (profils Simple, Main et Advanced) |Oui
Windows Media Video (profils Simple, Main et Advanced) |Oui
DV (DVC, DVHD, DVSD, DVSL) |Oui
Grass Valley HQ/HQX |Oui
 

##Codecs audio d’entrée

Codecs audio d’entrée|Pris en charge
---|---
AES (SMPTE 331M et 302M, AES3-2003) |Non
Dolby® E |Non
Dolby® Digital (AC3) |Oui
Dolby® Digital Plus (E-AC3) |Non
AAC (AAC-LC, HE-AAC v1 avec AAC-LC core et HE-AAC v2 avec AAC-LC core, jusqu’à 5.1)|Oui
MPEG Layer 2|Oui|Oui|Oui
MP3 (MPEG-1 Audio Layer 3)|Oui
Windows Media Audio 9 (Windows Media Audio Standard, Windows Media Audio Professionnel et Windows Media Audio sans perte) |Oui
WAV/PCM|Oui
[FLAC](https://en.wikipedia.org/wiki/FLAC)|Non
[Opus](https://en.wikipedia.org/wiki/Opus_(audio_format) |Non
[Vorbis](https://en.wikipedia.org/wiki/Vorbis)|Non


##Formats de fichiers image d’entrée

Format de fichier (extensions de fichier) | Pris en charge
---|---
Bitmap (.bmp) | Oui
GIF, GIF animé (.gif)| Oui
JPEG (.jpeg, .jpg)| Oui
PNG (.png)| Oui
TIFF (.tif)| Oui
WPF Canvas XAML (.xaml)| Oui


##Formats de sortie et codecs

Le tableau suivant répertorie les codecs et les formats de fichiers pris en charge pour l'exportation.

Format de fichier|Codec vidéo|Codec audio
---|---|---
Windows Media (*.wmv; *.wma)|VC-1 (profils Advanced, Main et Simple)|Windows Media Audio Standard, Windows Media Audio Professional, Windows Media Audio Voice, Windows Media Audio sans perte
MP4 (*.mp4)|H.264 (profils High, Main et Baseline)|AAC-LC, HE-AAC v1, HE-AAC v2, Dolby Digital Plus
Format de fichier de diffusion en continu lisse (PIFF 1,1) (*.ismv ; *.isma)|VC-1 (profil Advanced)<p>H.264 (profils High, Main et Baseline) |Windows Media Audio Standard, Windows Media Audio Professional<p><p>AAC-LC, HE-AAC v1, HE-AAC v2

Pour plus d’informations sur les codecs et les filtres supplémentaires pris en charge dans Media Services, consultez [Filtres Windows DirectShow](https://msdn.microsoft.com/library/windows/desktop/dd375464.aspx).

##<a id="uncompressed"></a>Formats vidéo non compressés pris en charge 

Azure Media Services prend en charge l'importation de données vidéo non compressées.

Voici une liste non exhaustive des formats non compressés pris en charge.

Format vidéo non compressé|Description
---|---
Données non compressées de format YVU9 standard|Format YUV planaire. Un échantillon Y à chaque pixel, un échantillon U et V à chaque quatrième pixel horizontalement sur chaque ligne ; un échantillon Y sur chaque ligne verticale, un échantillon U et V à chaque quatrième ligne verticale. 9 bits par pixel.
Données au format YUV 411|Un échantillon Y à chaque pixel, un échantillon U et V à chaque quatrième pixel horizontalement sur chaque ligne ; chaque ligne verticale échantillonnée. L'ordre des octets (inférieur en premier) est U0, Y0, V0, Y1, U4, Y2, V4, Y3, Y4, Y5, Y6, Y7, où le suffixe 0 est le pixel de gauche et les nombres croissants correspondent aux pixels croissants de gauche à droite. Chaque bloc de 12 octets correspond à 8 pixels d’une image.
Données au format Y41P|Un échantillon Y à chaque pixel, un échantillon U et V à chaque quatrième pixel horizontalement sur chaque ligne ; chaque ligne verticale échantillonnée. L'ordre des octets (inférieur en premier) est U0, Y0, V0, Y1, U4, Y2, V4, Y3, Y4, Y5, Y6, Y7, où le suffixe 0 est le pixel de gauche et les nombres croissants correspondent aux pixels croissants de gauche à droite. Chaque bloc de 12 octets correspond à 8 pixels d’une image.
Données au format YUY2|Identique à UYVY mais avec un ordre des pixels différent. L'ordre des octets (inférieur en premier) est Y0, U0, Y1, V0, Y2, U2, Y3, V2, Y4, U4, Y5, V4, où le suffixe 0 est le pixel de gauche et les nombres croissants correspondent aux pixels croissants de gauche à droite. Chaque bloc de 4 octets correspond à 2 pixels d’une image.
Données au format YVYU|Format YUV compressé. Identique à UYVY mais avec un ordre des pixels différent. L'ordre des octets (inférieur en premier) est Y0, V0, Y1, U0, Y2, V2, Y3, U2, Y4, V4, Y5, U4, où le suffixe 0 est le pixel de gauche et les nombres croissants correspondent aux pixels croissants de gauche à droite. Chaque bloc de 4 octets correspond à 2 pixels d’une image.
Données au format UYVY|Format YUV compressé. Un échantillon Y à chaque pixel, un échantillon U et V à chaque deuxième pixel horizontalement sur chaque ligne ; chaque ligne verticale échantillonnée. Le plus populaire des différents formats YUV 4:2:2. L'ordre des octets (inférieur en premier) est U0, Y0, V0, Y1, U2, Y2, V2, Y3, U4, Y4, V4, Y5, où le suffixe 0 est le pixel de gauche et les nombres croissants correspondent aux pixels croissants de gauche à droite. Chaque bloc de 4 octets correspond à 2 pixels d’une image.
Données au format YUV 211|Format YUV compressé. Un échantillon Y à chaque pixel, un échantillon U et V à chaque second pixel horizontalement sur chaque ligne ; chaque ligne verticale échantillonnée. L'ordre des octets (inférieur en premier) est Y0, U0, Y2, V0, Y4, U4, Y6, V4, Y8, U8, Y10, V8, où le suffixe 0 est le pixel de gauche et les nombres croissants correspondent aux pixels croissants de gauche à droite. Chaque bloc de 4 octets correspond à 4 pixels d’une image.
Format Cirrus Logic Jr YUV 411|Format Cirrus Logic Jr YUV 411 avec moins de 8 bits par échantillon Y, U et V. Un échantillon Y à chaque pixel, un échantillon U et V à chaque quatrième pixel horizontalement sur chaque ligne ; chaque ligne verticale échantillonnée.
Format Indeo-produced YVU9|Format Indeo-produced YVU9 avec informations supplémentaires sur les différences par rapport au dernier frame. 9,5 bits par pixel mais déclaré comme 9.

<!---HONumber=August15_HO9-->