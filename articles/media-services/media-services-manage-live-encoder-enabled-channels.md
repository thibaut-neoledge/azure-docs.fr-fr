<properties 
	pageTitle="Utilisation de canaux activés pour effectuer un encodage en temps réel avec Azure Media Services" 
	description="Cette rubrique décrit comment configurer un canal qui reçoit un flux dynamique à débit binaire unique à partir d’un encodeur local, puis effectue un encodage en temps réel en flux à débit binaire adaptatif avec Media Services. Le flux peut ensuite être distribué aux applications de lecture clientes via un ou plusieurs points de terminaison de diffusion en continu à l’aide d’un des protocoles de diffusion en continu adaptatifs suivants : HLS, Smooth Stream, MPEG DASH et HDS." 
	services="media-services" 
	documentationCenter="" 
	authors="Juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="ne" 
	ms.topic="article" 
	ms.date="05/27/2015" 
	ms.author="juliako"/>

#Utilisation de canaux activés pour effectuer un encodage en temps réel avec Azure Media Services (version préliminaire)

##Vue d'ensemble

Dans Azure Media Services, un **canal** représente un pipeline de traitement du contenu de diffusion dynamique. Un **canal** reçoit des flux d’entrée dynamiques de l’une des deux manières suivantes :

- Un encodeur dynamique local envoie au canal un paquet **RTMP** ou **Smooth Streaming** (MP4 fragmenté) à débit binaire multiple. Vous pouvez utiliser les encodeurs dynamiques suivants qui produisent un flux Smooth Streaming à débit binaire multiple : Elemental, Envivio, Cisco. Les encodeurs dynamiques suivants produisent un flux au format RTMP : Adobe Flash Live, Telestream Wirecast et transcodeurs Tricaster. Les flux reçus transitent par les **canaux** sans traitement supplémentaire. Lorsqu’il y est invité, Media Services fournit le flux aux clients.
- Un flux à débit binaire unique (dans l’un des formats suivants : **RTP** [MPEG-TS], **RTMP** ou **Smooth Streaming** [MP4 fragmenté]) est envoyé au **canal** qui est activé pour effectuer un encodage en temps réel avec Media Services. Le **canal** procède ensuite à l’encodage en temps réel du flux à débit binaire unique entrant en flux vidéo à débit binaire multiple (adaptatif). Lorsqu’il y est invité, Media Services fournit le flux aux clients. 

	L’encodage d’un flux dynamique avec Media Services est actuellement en mode **Aperçu**.

À compter de la version de Media Services 2.10, lorsque vous créez un canal, vous pouvez spécifier la façon dont vous souhaitez qu’il reçoive le flux d’entrée. Vous pouvez également indiquer si vous souhaitez ou non que le canal effectue un encodage en temps réel de votre flux. Deux options s'offrent à vous :

- **Aucun** : indiquez cette valeur si vous envisagez d’utiliser un encodeur dynamique local qui produira des flux à débit binaire multiple. Le cas échéant, le flux entrant est transmis à la sortie sans encodage. Il s’agit du comportement d’un canal avant la version 2.10. Pour plus d’informations sur l’utilisation des canaux de ce type, voir [Utilisation des canaux qui reçoivent un flux dynamique à débit binaire multiple provenant d’encodeurs locaux](media-services-manage-channels-overview.md).

- **Standard** (aperçu) : choisissez cette valeur si vous envisagez d’utiliser Media Services pour encoder votre flux dynamique à débit binaire unique en flux à débit binaire multiple.

	L’encodage d’un flux dynamique avec Media Services est actuellement en mode Aperçu.

>[AZURE.NOTE]Cette rubrique décrit les attributs des canaux qui sont activés pour effectuer un encodage en temps réel (type d’encodage **standard**). Pour obtenir des informations sur l’utilisation des canaux qui ne sont pas activés pour effectuer l’encodage en temps réel, consultez [Utilisation des canaux qui reçoivent un flux dynamique à débit binaire multiple provenant d’encodeurs locaux](media-services-manage-channels-overview.md).

Le diagramme suivant représente un flux de travail de diffusion en continu dynamique où un canal reçoit un flux à débit binaire unique dans l’un des protocoles suivants : RTMP, Smooth Streaming ou RTP (MPEG-TS). Il encode ensuite le flux dans un flux à débit binaire multiple.

![Flux de travail en direct][live-overview]

>[AZURE.NOTE]Tous les centres de données ne prennent pas en charge l’encodage live avec Azure Media Services.
>
>Si vous utilisez le portail de gestion Azure pour créer des canaux, deux options de type d’encodage sont disponibles : **Aucun** et **Standard**. Si seule l’option **Aucun** s’affiche, votre centre de données ne prend pas en charge l'encodage live avec AMS.
>
>Si vous utilisez le Kit de développement logiciel (SDK) .NET ou l'API REST, effectuez la vérification suivante :
>
>1. Essayez de créer un canal avec le type d’encodage défini sur Standard. 
>2. Si le code d'erreur HTTP 412 (Échec de la précondition) est renvoyé avec le message suivant : *« L’encodage live n'est pas pris en charge dans cette région ; EncodingType doit avoir la valeur « None ». »*, votre centre de données ne prend pas en charge l'encodage live.


##Dans cette rubrique

- Vue d’ensemble d’un [scénario courant de diffusion dynamique en continu](media-services-manage-live-encoder-enabled-channels.md#scenario)
- [Description d’un canal et de ses composants associés](media-services-manage-live-encoder-enabled-channels.md#channel)
- [Considérations](media-services-manage-live-encoder-enabled-channels.md#considerations)
- [Tâches liées à la diffusion dynamique en continu](media-services-manage-live-encoder-enabled-channels.md#tasks)

##<a id="scenario"></a>Scénario courant de diffusion dynamique en continu

Ci-après figurent les étapes générales impliquées dans la création d’applications courantes de diffusion en continu dynamique.

1. Connectez une caméra vidéo à un ordinateur. Lancez et configurez un encodeur dynamique local capable de générer un flux à vitesse binaire **unique** dans l’un des protocoles suivants : RTMP, Smooth Streaming ou RTP (MPEG-TS). Pour plus d’informations, voir [Prise en charge RTMP et encodeurs dynamiques dans Azure Media Services](http://go.microsoft.com/fwlink/?LinkId=532824).
	
	Cette étape peut également être effectuée après la création du canal.

1. Créez et démarrez un canal.

1. Récupérez l’URL de réception du canal.

	L’URL de réception est utilisée par l’encodeur dynamique pour envoyer le flux au canal.
1. Récupérez l’URL d’aperçu du canal. 

	Utilisez cette URL pour vérifier que votre canal reçoit correctement le flux dynamique.

3. Créez un programme.

	Lors de l’utilisation du portail de gestion Azure, la création d’un programme crée également une ressource.

	Lors de l’utilisation du Kit de développement logiciel (SDK) .NET ou de REST, vous devez créer une ressource et préciser son utilisation lors de la création d’un programme. 
1. Publiez la ressource associée au programme.   

	Assurez-vous d’avoir au moins une unité réservée de diffusion en continu pour le point de terminaison de diffusion en continu à partir duquel vous prévoyez de diffuser votre contenu.
1. Démarrez le programme dès que vous êtes prêt à lancer la diffusion en continu et l’archivage.
2. Un signal peut éventuellement être envoyé à l’encodeur dynamique pour qu’il démarre une publicité. La publicité est insérée dans le flux de sortie.
1. Arrêtez le programme chaque fois que vous voulez arrêter la diffusion et archiver l’événement.
1. Supprimez le programme (et éventuellement la ressource).   

La section [Tâches de diffusion en continu dynamique](media-services-manage-channels-overview.md#tasks) offre des liens vers des rubriques expliquant comment effectuer les tâches décrites ci-dessus.


##<a id="channel"></a>Configurations de l’entrée (réception) du canal

###<a id="Ingest_Protocols"></a>Protocole de diffusion en continu de réception

Si le **Type d’encodeur** est défini sur **Standard**, les options valides sont les suivantes :

- **RTP** (MPEG-TS) : flux de transport MPEG-2 via RTP.  
- **RTMP** à débit binaire unique
- **MP4 fragmenté** (Smooth Streaming) à débit binaire unique

Pour plus d’informations, voir [Prise en charge RTMP et encodeurs dynamiques dans Azure Media Services](http://go.microsoft.com/fwlink/?LinkId=532824).

####RTP (MPEG-TS) : flux de transport MPEG-2 via RTP.  

Cas d’utilisation classique :

Les diffuseurs professionnels utilisent généralement des encodeurs dynamiques locaux haut de gamme des fournisseurs comme Elemental Technologies, Ericsson, Ateme, Imagine ou Envivio pour envoyer un flux. Ils sont souvent utilisés conjointement avec un service informatique et des réseaux privés.

Considérations :

- L’utilisation d’une entrée SPTS (Single Program Transport Stream) est vivement recommandée. Néanmoins, l’utilisation de pistes audio multilingues est prise en charge.
- Le flux vidéo doit avoir un débit binaire moyen inférieur à 15 Mbits/s.
- La somme des débits binaires moyens des flux audio doit être inférieure à 1 Mbits/s.
- Voici les codecs pris en charge :
	- Vidéo MPEG-2/H.262 
		
		- Profil Main (4:2:0)
		- Profil High (4:2:0, 4:2:2)
		- Profil 422 (4:2:0, 4:2:2)

	- Vidéo MPEG-4 AVC/H.264
	
		- Profil Baseline, Main, High (8 bits 4:2:0)
		- Profil High 10 (10 bits 4:2:0)
		- Profil High 422 (10 bits 4:2:2)


	- Audio MPEG-2 AAC-LC
	
		- Mono, stéréo, Surround (5.1, 7.1)
		- Format ADTS style MPEG-2

	- Dolby Digital (AC-3) Audio

		- Mono, stéréo, Surround (5.1, 7.1)

	- Audio MPEG (couches II et III)
			
		- Mono, stéréo

- Les encodeurs de diffusion recommandés sont les suivants :
	- Ateme AM2102
	- Ericsson AVP2000
	- eVertz 3480
	- Ericsson RX8200
	- Imagine Communications Selenio ENC 1
	- Imagine Communications Selenio ENC 2
	- AdTec EN-30
	- AdTec EN-91P
	- AdTec EN-100
	- Harmonic ProStream 1000
	- Thor H-2 4HD-EM
	- eVertz 7880 SLKE
	- Cisco Spinnaker
	- Elemental Live

####<a id="single_bitrate_RTMP"></a>RTMP à débit binaire unique

Considérations :

- Le flux entrant ne peut pas contenir de vidéo à débit binaire multiple
- Le flux vidéo doit avoir un débit binaire moyen inférieur à 15 Mbits/s.
- Le flux audio doit avoir un débit binaire moyen inférieur à 1 Mbits/s.
- Voici les codecs pris en charge :

	- Vidéo MPEG-4 AVC/H.264  
	
		- Profil Baseline, Main, High (8 bits 4:2:0)
		- Profil High 10 (10 bits 4:2:0)
		- Profil High 422 (10 bits 4:2:2)

	- Audio MPEG-2 AAC-LC

		- Mono, stéréo, Surround (5.1, 7.1)
		- Fréquence d'échantillonnage 44,1 kHz
		- Format ADTS style MPEG-2
	
- Les encodeurs recommandés sont les suivants :

	- Telestream Wirecast
	- Flash Media Live Encoder
	- Tricaster

####MP4 fragmenté (Smooth Streaming) à débit binaire unique

Cas d’utilisation classique :

Utilisez les encodeurs dynamiques locaux des fournisseurs que sont Elemental Technologies, Ericsson, Ateme, Envivio pour envoyer le flux d’entrée via Internet à un centre de données Azure à proximité.

Considérations :

Identique au flux [RTMP à débit binaire unique](media-services-manage-live-encoder-enabled-channels.md#single_bitrate_RTMP).

####Autres considérations

- Vous ne pouvez pas modifier le protocole d’entrée pendant l’exécution du canal ou de ses programmes associés. Si vous avez besoin d’autres protocoles, vous devez créer des canaux distincts pour chaque protocole d’entrée. 
- La résolution maximale pour le flux vidéo entrant est définie sur 1920x1080. Si le flux vidéo est entrelacé, elle est définie au plus sur 60 champs par seconde. S’il est progressif, elle est définie sur 30 images/seconde.


###URL (points de terminaison) de réception 

Un canal fournit un point de terminaison d’entrée (URL de réception) que vous spécifiez dans l’encodeur dynamique pour que ce dernier puisse envoyer les flux vers vos canaux.

Vous pouvez obtenir les URL de réception dès que vous avez créé un canal. Pour les obtenir, il n’est pas nécessaire que le canal soit à l’état **En cours d’exécution**. Lorsque vous êtes prêt à commencer l’envoi de données dans le canal, ce dernier doit être à l’état **En cours d’exécution**. Une fois que le canal commence à recevoir les données, vous pouvez prévisualiser votre flux via l’URL d’aperçu.

Vous avez la possibilité de recevoir un flux dynamique au format MP4 fragmenté (Smooth Streaming) via une connexion SSL. Pour assurer la réception via SSL, veillez à mettre à jour l’URL de réception pour HTTPS.

###Adresses IP autorisées

Vous pouvez définir les adresses IP autorisées à publier du contenu vidéo sur ce canal. Les adresses IP autorisées peuvent être spécifiées en tant qu’adresses IP uniques (par exemple, 10.0.0.1), une plage d’adresses IP utilisant une adresse IP et un masque de sous-réseau CIDR (par exemple, 10.0.0.1/22), ou une plage d’adresses IP utilisant une adresse IP et un masque de sous-réseau décimal séparé par des points (par exemple, 10.0.0.1[255.255.252.0]).

Si aucune adresse IP n’est spécifiée et qu’il n’existe pas de définition de règle, alors aucune adresse IP n’est autorisée. Pour autoriser toutes les adresses IP, créez une règle et définissez la valeur 0.0.0.0/0.


##Aperçu du canal 

###URL d’aperçu

Les canaux fournissent un point de terminaison d’aperçu (URL d’aperçu) permettant de prévisualiser et de valider le flux avant de lui appliquer un traitement supplémentaire et de le distribuer.

Vous pouvez obtenir l’URL d’aperçu lors de la création du canal. Pour obtenir l’URL, il n’est pas nécessaire que le canal soit à l’état **En cours d’exécution**.

Une fois que le canal commence à recevoir les données, vous pouvez prévisualiser votre flux.

**Remarque** Actuellement, le flux d’aperçu ne peut être distribué qu’au format MP4 fragmenté (Smooth Streaming), quel que soit le type d’entrée spécifié. Vous pouvez utiliser le lecteur [http://smf.cloudapp.net/healthmonitor](http://smf.cloudapp.net/healthmonitor) pour tester la diffusion au format Smooth Streaming. Vous pouvez également utiliser un lecteur hébergé dans le portail de gestion Azure pour afficher votre flux.

###Adresses IP autorisées

Vous pouvez définir les adresses IP autorisées à se connecter au point de terminaison d’aperçu. Si aucune adresse IP n’est spécifiée, alors toutes les adresses IP seront autorisées. Les adresses IP autorisées peuvent être spécifiées en tant qu’adresses IP uniques (par exemple, 10.0.0.1), une plage d’adresses IP utilisant une adresse IP et un masque de sous-réseau CIDR (par exemple, 10.0.0.1/22), ou une plage d’adresses IP utilisant une adresse IP et un masque de sous-réseau décimal séparé par des points (par exemple, 10.0.0.1[255.255.252.0]).

##Paramètres d’encodage en temps réel

Cette section décrit comment les paramètres de l’encodeur dynamique dans le canal peuvent être ajustés, lorsque le paramètre **Type d’encodage** d’un canal est défini sur **Standard**.

###Source de marqueur de publicité

Vous pouvez spécifier la source des signaux des marqueurs de publicité. La valeur par défaut est **Api**, qui indique que l’encodeur dynamique dans le canal doit écouter une **API de marqueur de publicité** asynchrone.

L’autre option valide est **Scte35** (autorisée uniquement si le protocole de diffusion en continu de réception est défini sur RTP (MPEG-TS). Si l’option Scte35 est spécifiée, l’encodeur dynamique analyse les signaux SCTE-35 du flux d’entrée RTP (MPEG-TS).

###Sous-titres CEA-708

Indicateur facultatif qui spécifie à l’encodeur dynamique d’ignorer les données des sous-titres CEA-708 intégrées à la vidéo entrante. Lorsque l’indicateur est défini sur false (par défaut), l’encodeur détecte et réinsère les données CEA-708 dans les flux vidéo de sortie.

###Flux vidéo

facultatif. Décrit le flux vidéo d’entrée. Si ce champ n’est pas spécifié, la valeur par défaut est utilisée. Ce paramètre est autorisé uniquement si le protocole de diffusion en continu d’entrée est défini sur RTP (MPEG-TS).

####Index

Index de base zéro qui précise le flux vidéo d’entrée qui doit être traité par l’encodeur dynamique dans le canal. Ce paramètre s’applique uniquement si le protocole de diffusion en continu de réception est défini sur RTP (MPEG-TS).

La valeur par défaut est zéro. L’envoi dans un flux SPTS est recommandé. Si le flux d’entrée contient plusieurs programmes, l’encodeur dynamique analyse la table de mappage de programmes (PMT) dans l’entrée, identifie les entrées dont le nom de type de flux est Vidéo MPEG-2 ou H.264, puis les réorganise en suivant l’ordre spécifié dans la table PMT. L’index de base zéro permet ensuite de choisir la nième entrée dans cette disposition.

###Flux audio

facultatif. Décrit les flux audio d’entrée. Si ce champ n’est pas spécifié, les valeurs par défaut spécifiées s’appliquent. Ce paramètre est autorisé uniquement si le protocole de diffusion en continu d’entrée est défini sur RTP (MPEG-TS).

####Index

L’envoi dans un flux SPTS est recommandé. Si le flux d’entrée contient plusieurs programmes, l’encodeur dynamique au sein du canal analyse la table de mappage de programmes (PMT) dans l’entrée, identifie les entrées dont le nom de type de flux est Audio MPEG-2 AAC ADTS, AC-3 System-A, AC-3 System-B, MPEG-2 Private PES, MPEG-1 ou MPEG-2, puis les réorganise en suivant l’ordre spécifié dans la table PMT. L’index de base zéro permet ensuite de choisir la nième entrée dans cette disposition.

####Langage

Identificateur de langue du flux audio, conformément à la norme ISO 639-2, par exemple ENG. En son absence, la valeur par défaut est UND (non définie).

Jusqu’à 8 jeux de flux audio peuvent être spécifiés si l’entrée du canal est définie sur MPEG-2 TS via RTP. Toutefois, deux entrées ne peuvent pas posséder la même valeur d’index.

###<a id="preset"></a>Présélection du système

Spécifie la présélection à utiliser par l’encodeur dynamique dans ce canal. Actuellement, la seule valeur autorisée est **Default720p** (par défaut).

**Default720p** encode la vidéo dans les 7 couches suivantes.


####Flux vidéo de sortie

<table border="1">
<tr><th>Débit binaire</th><th>Largeur</th><th>Hauteur</th><th>IPS max.</th><th>Profil</th><th>Nom du flux de sortie</th></tr>
<tr><td>3&#160;500</td><td>1&#160;280</td><td>720</td><td>30</td><td>Élevé</td><td>Video_1280x720_30fps_3500kbps</td></tr>
<tr><td>2&#160;200</td><td>960</td><td>540</td><td>30</td><td>Principal</td><td>Video_960x540_30fps_2200kbps</td></tr>
<tr><td>1&#160;350</td><td>704</td><td>396</td><td>30</td><td>Principal</td><td>Video_704x396_30fps_1350kbps</td></tr>
<tr><td>850</td><td>512</td><td>288</td><td>30</td><td>Principal</td><td>Video_512x288_30fps_850kbps</td></tr>
<tr><td>550</td><td>384</td><td>216</td><td>30</td><td>Principal</td><td>Video_384x216_30fps_550kbps</td></tr>
<tr><td>350</td><td>340</td><td>192</td><td>30</td><td>Ligne de base</td><td>Video_340x192_30fps_350kbps</td></tr>
<tr><td>200</td><td>340</td><td>192</td><td>30</td><td>Ligne de base</td><td>Video_340x192_30fps_200kbps</td></tr>
</table>

####Flux audio de sortie

Le flux audio est encodé au format stéréo AAC-LC à 64 Kbits/s, avec un taux d’échantillonnage de 44,1 kHz.

##Signalisation des annonces

Si le paramètre Encodage en temps réel du canal est activé, vous possédez un composant dans votre pipeline qui traite les vidéos et peut les manipuler. Vous pouvez indiquer au canal d’insérer des ardoises et/ou des annonces dans le flux à débit binaire adaptatif sortant. Les ardoises sont des images fixes que vous pouvez utiliser pour couvrir le flux d’entrée dynamique dans certains cas (par exemple pendant une pause publicitaire). Les signaux publicitaires sont des signaux synchronisés que vous intégrez au flux sortant pour indiquer au lecteur vidéo d’effectuer une action spéciale, par exemple de basculer vers une annonce au moment approprié. Consultez ce [blog](https://codesequoia.wordpress.com/2014/02/24/understanding-scte-35/) pour obtenir une vue d’ensemble du mécanisme de signalisation SCTE-35 utilisé à cet effet. Ci-dessous figure un scénario standard que vous pouvez implémenter dans votre événement en direct.

1. Avant le début de l’événement, faites que vos observateurs obtiennent une image ANTÉRIEURE À L’ÉVÉNEMENT
1. À l’issue de l’événement, faites que vos observateurs obtiennent une image POST-ÉVÉNEMENT.
1. Faites que vos observateurs obtiennent une image ÉVÉNEMENT AVEC ERREUR si un problème se produit au cours de l’événement (par exemple, une panne de courant dans un stade).
1. Envoyez une image PAUSE PUBLICITAIRE pour masquer le flux d’événements en direct pendant une pause publicitaire.

Ci-après figurent les propriétés que vous pouvez définir pour la signalisation des annonces :

###Durée

Durée (en secondes) de la pause publicitaire. Pour que la pause publicitaire commence, ce doit être une valeur positive différente de zéro. Si une pause publicitaire est en cours et que la durée est définie sur zéro avec la propriété ID de file d’attente correspondant à la pause publicitaire en cours, alors cette pause est annulée.

###ID de file d’attente

ID unique de la pause publicitaire à utiliser par une application en aval pour prendre les actions appropriées. Ce doit être un entier positif. Vous pouvez définir cette valeur sur un entier positif aléatoire quelconque ou utiliser un système en amont pour suivre les ID de file d'attente. Assurez-vous de normaliser les ID en entiers positifs avant de procéder à la soumission via l’API.

###Afficher l’ardoise

facultatif. Signale à l’encodeur en direct de basculer vers l’image de l’[ardoise par défaut](media-services-manage-live-encoder-enabled-channels.md#default_slate) pendant une pause publicitaire et de masquer le flux vidéo entrant. Le son est également désactivé pendant l’affichage de l’ardoise. La valeur par défaut est **false**.
 
L’image utilisée sera celle qui est spécifiée par la propriété ID de ressource d’ardoise par défaut au moment de la création du canal. L’ardoise est étirée pour s’ajuster à la taille de l’image de l’écran.


##Insérer des images d’ardoise

L’encodeur dynamique dans le canal peut être informé de basculer vers une image d’ardoise. Il peut également être informé de mettre fin à une ardoise en cours.

L’encodeur dynamique peut être configuré pour basculer vers une image d’ardoise et masquer le signal vidéo entrant dans certaines situations, par exemple, pendant une pause publicitaire. Si une telle ardoise n’est pas configurée, la vidéo d’entrée n’est pas masquée pendant cette annonce publicitaire.

###Durée

Durée (en secondes) de l’affichage de l’ardoise. Pour que l’affichage de l’ardoise commence, ce doit être une valeur positive différente de zéro. Si une ardoise est en cours d’affichage et que la durée zéro est spécifiée, cette ardoise en cours va se terminer.

###Insérer une ardoise dans le marqueur de publicité

S’il est défini sur true, ce paramètre configure l’encodeur dynamique pour insérer une image d’ardoise pendant une pause publicitaire. La valeur par défaut est true.

###<a id="default_slate"></a>ID de ressource d’ardoise par défaut

facultatif. Spécifie l’ID de la ressource Media Services qui contient l’image d’ardoise. La valeur par défaut est Null.

**Remarque** : avant de créer le canal, l’image d’ardoise avec les contraintes suivantes doit être chargée en tant que ressource dédiée (aucun autre fichier ne doit exister dans cette ressource).

- Résolution maximale de 1920 x 1080
- Taille maximale de 3 Mo.
- Le nom de fichier doit avoir une *extension .jpg. - L'image doit être téléchargée dans une ressource en tant que seul AssetFile de cette ressource et cet AssetFile doit être marqué comme fichier principal. La ressource ne peut pas être de type stockage chiffré.

Si l’**ID de ressource d’ardoise par défaut** n’est pas spécifié, et que le paramètre **Insérer une ardoise dans le marqueur de publicité** est défini sur **true**, une image d’Azure Media Services par défaut est utilisée pour masquer le flux vidéo d’entrée. Le son est également désactivé pendant l’affichage de l’ardoise.


##Programmes du canal

Un canal est associé à des programmes vous permettant de contrôler la publication et le stockage des segments dans un flux dynamique. Les canaux gèrent des programmes. La relation entre canal et programme est très similaire au contenu multimédia traditionnel où un canal présente un flux de contenu constant et un programme est limité à un événement minuté sur ce canal.

Vous pouvez spécifier le nombre d’heures pendant lesquelles vous souhaitez conserver le contenu enregistré pour le programme en définissant la durée de la **fenêtre d’archivage**. Cette valeur peut être comprise entre 5 minutes et 25 heures. La durée de la fenêtre d’archivage détermine également la plage maximale de temps dans laquelle les clients peuvent effectuer des recherches en arrière à partir de la position dynamique actuelle. Les programmes peuvent durer davantage que le laps de temps spécifié, mais le contenu qui se situe en dehors de la longueur de fenêtre est ignoré en permanence. La valeur de cette propriété détermine également la longueur maximale que les manifestes de client peuvent atteindre.

Chaque programme est associé à une ressource qui stocke le contenu diffusé en continu. Un élément multimédia est mappé à un conteneur d'objets blob dans le compte de stockage Azure et les fichiers de l'élément multimédia sont stockés en tant qu'objets blob dans ce conteneur. Pour publier le programme afin que vos clients puissent visionner le flux, vous devez créer un localisateur OnDemand pour la ressource associée. Le fait de posséder ce localisateur vous permettra de générer une URL de diffusion en continu que vous pourrez fournir à vos clients.

Un canal prend en charge jusqu’à trois programmes exécutés simultanément, ce qui permet de créer plusieurs archives du même flux entrant. Cela vous permet de publier et d’archiver différentes parties d’un événement en fonction des besoins. Par exemple, imaginez que vous devez archiver 6 heures d’un programme, mais diffuser uniquement les 10 dernières minutes. Pour ce faire, vous devez créer deux programmes exécutés simultanément. Un programme est configuré pour archiver 6 heures de l’événement, mais il n’est pas publié. L’autre programme est configuré pour archiver pendant 10 minutes et il est publié.

Vous ne devez pas réutiliser de programmes existants pour de nouveaux événements. Au lieu de cela, créez et démarrez un nouveau programme pour chaque événement, tel que décrit dans la section Programmation d’applications de vidéo en flux continu.

Démarrez le programme dès que vous êtes prêt à lancer la diffusion en continu et l’archivage. Arrêtez le programme chaque fois que vous voulez arrêter la diffusion et archiver l’événement.

Pour supprimer du contenu archivé, arrêtez et supprimez le programme, puis supprimez l’élément multimédia associé. Un élément multimédia ne peut pas être supprimé s’il est utilisé par un programme ; le programme doit d’abord être supprimé.

Même après l’arrêt et la suppression du programme, les utilisateurs pourront lire votre contenu archivé en tant que vidéo à la demande tant que vous n’aurez pas supprimé l’élément multimédia.

Si vous souhaitez conserver le contenu archivé sans qu’il soit disponible pour la diffusion, supprimez le localisateur de diffusion en continu.


##Obtention d’une image miniature d’un flux en direct

Si le paramètre Encodage en temps réel est activé, vous pouvez désormais obtenir un aperçu du flux en direct lorsqu’il atteint le canal. Ce peut être un outil intéressant lorsqu’il s’agit de vérifier si votre flux en direct atteint réellement le canal.

##<a id="states"></a>États du canal et mappage des états au mode de facturation 

État actuel d’un canal. Les valeurs possibles incluent :

- **Arrêté**. C’est l’état initial du canal après sa création. Dans cet état, les propriétés du canal peuvent être mises à jour, mais la diffusion en continu n’est pas autorisée.
- **Démarrage en cours**. Le canal est en cours de démarrage. Aucune mise à jour ou diffusion en continu n’est autorisée durant cet état. Si une erreur se produit, le canal retourne à l’état Arrêté.
- **Exécution en cours**. Le canal est capable de traiter des flux dynamiques.
- **En cours d’arrêt**. Le canal est en cours d’arrêt. Aucune mise à jour ou diffusion en continu n’est autorisée durant cet état.
- **Suppression en cours**. Le canal est en cours de suppression. Aucune mise à jour ou diffusion en continu n’est autorisée durant cet état.

Le tableau suivant montre comment les états du canal sont mappés au mode de facturation.
 
<table border="1">
<tr><th>État du canal</th><th>Indicateurs de l’interface utilisateur du portail</th><th>Facturation&#160;?</th></tr>
<tr><td>Démarrage en cours</td><td>Démarrage en cours</td><td>Aucun (état transitoire)</td></tr>
<tr><td>Exécution en cours</td><td>Prêt (pas de programmes en cours d’exécution)<br/>ou<br/>Diffusion en continu (au moins un programme en cours d’exécution)</td><td>Oui</td></tr>
<tr><td>En cours d’arrêt</td><td>En cours d’arrêt</td><td>Aucun (état transitoire)</td></tr>
<tr><td>Arrêté</td><td>Arrêté</td><td>Non</td></tr>
</table>


>[AZURE.NOTE]Actuellement en version préliminaire, le démarrage du canal peut prendre jusqu’à plus de 20 minutes. La réinitialisation du canal peut prendre jusqu’à 5 minutes.


##<a id="Considerations"></a>Considérations

- Vous ne pouvez pas modifier le protocole d’entrée pendant l’exécution du canal ou de ses programmes associés. Si vous avez besoin d’autres protocoles, vous devez créer des canaux distincts pour chaque protocole d’entrée. 
- Chaque fois que vous reconfigurez l’encodeur dynamique, appelez la méthode de réinitialisation **Reset** sur le canal. Avant de réinitialiser le canal, vous devez arrêter le programme. Une fois le canal réinitialisé, redémarrez le programme. 
- Un canal peut être arrêté uniquement lorsqu’il est en cours d’exécution et que tous les programmes du canal ont été arrêtés.
- Par défaut, vous pouvez seulement ajouter 5 canaux à votre compte Media Services. Il s’agit d’un quota conditionnel sur tous les nouveaux comptes. Pour plus d’informations, voir [Quotas et limitations](media-services-quotas-and-limitations.md).
- Vous ne pouvez pas modifier le protocole d’entrée pendant l’exécution du canal ou de ses programmes associés. Si vous avez besoin d’autres protocoles, vous devez créer des canaux distincts pour chaque protocole d’entrée.
- Vous êtes facturé uniquement lorsque votre canal est à l’état **En cours d’exécution**. Pour plus d’informations, reportez-vous à [cette](media-services-manage-live-encoder-enabled-channels.md#states) section.

##Problèmes connus

- Le démarrage du canal peut prendre plus de 20 minutes.
- La prise en charge RTP est adaptée aux diffuseurs professionnels. Veuillez consulter les notes relative à RTP dans [ce blog](http://azure.microsoft.com/blog/2015/04/13/an-introduction-to-live-encoding-with-azure-media-services/).
- Les images d’ardoise doivent être conformes aux restrictions décrites [ici](media-services-manage-live-encoder-enabled-channels.md#default_slate). Si vous essayez de créer un canal à partir d’une ardoise par défaut d’une résolution supérieure à 1920 x 1080, la requête se termine par une erreur.


##<a id="tasks"></a>Tâches liées à la diffusion en continu dynamique

###Création d’un compte Media Services

[Créez un compte Azure Media Services](media-services-create-account.md).

###Configuration de points de terminaison de diffusion en continu

Pour une présentation des points de terminaison de diffusion en continu et pour obtenir des informations sur leur gestion, consultez la rubrique [Gestion des points de terminaison de diffusion en continu dans un compte Media Services](media-services-manage-origins.md)

###Configuration d’un environnement de développement  

Choisissez **.NET** ou **API REST** comme environnement de développement.

[AZURE.INCLUDE [media-services-selector-setup](../../includes/media-services-selector-setup.md)]

###Connexion par programme  

Choisissez **.NET** ou **API REST** pour vous connecter par programme à Azure Media Services.

[AZURE.INCLUDE [media-services-selector-connect](../../includes/media-services-selector-connect.md)]

###Création de canaux encodant en temps réel un flux à débit binaire unique en flux à débit binaire adaptatif 

Choisissez **Portail**, **.NET**, **API REST** pour voir comment créer et gérer des canaux et des programmes.

> [AZURE.SELECTOR]
- [Portal](media-services-portal-creating-live-encoder-enabled-channel.md)
- [.NET SDK](media-services-dotnet-creating-live-encoder-enabled-channel.md)
- [REST API](https://msdn.microsoft.com/library/azure/dn783458.aspx)

###Protection des ressources

**Vue d’ensemble** :

[Vue d’ensemble de la protection du contenu](media-services-content-protection-overview.md)

Si vous souhaitez chiffrer une ressource associée à un programme avec la norme AES (Advanced Encryption Standard) (à l’aide de clés de chiffrement 128 bits) ou avec PlayReady DRM, vous devez créer une clé de contenu.

Utilisez **.NET** ou l’**API REST** pour créer des clés.

[AZURE.INCLUDE [media-services-selector-create-contentkey](../../includes/media-services-selector-create-contentkey.md)]

Une fois que vous avez créé la clé de contenu, vous pouvez configurer la stratégie d’autorisation des clés à l’aide de **.NET** ou de l’**API REST**.

[AZURE.INCLUDE [media-services-selector-content-key-auth-policy](../../includes/media-services-selector-content-key-auth-policy.md)]

####Intégration avec des partenaires

[Utilisation de castLabs pour fournir des licences DRM à Azure Media Services](media-services-castlabs-integration.md)


###Publication et distribution de ressources

**Vue d’ensemble** :

- [Vue d’ensemble de l’empaquetage dynamique](../media-services-dynamic-overview.md)


Configurez la stratégie de remise de ressources à l’aide de **.NET** ou de l’**API REST**.

[AZURE.INCLUDE [media-services-selector-asset-delivery-policy](../../includes/media-services-selector-asset-delivery-policy.md)]

Publiez des ressources (en créant des localisateurs) à l’aide du **portail de gestion Azure** ou de **.NET**.

[AZURE.INCLUDE [media-services-selector-publish](../../includes/media-services-selector-publish.md)]


Distribution de contenu

> [AZURE.SELECTOR]
- [Overview](media-services-deliver-content-overview.md)

###Activation du CDN Azure

Media Services prend en charge l’intégration avec le CDN d’Azure. Pour plus d’informations sur l’activation du CDN Azure, voir [Gestion des points de terminaison de diffusion en continu dans un compte Media Services](media-services-manage-origins.md#enable_cdn).

###Mise à l’échelle d’un compte Media Services

Vous pouvez mettre à l’échelle **Media Services** en spécifiant le nombre d’**Unités réservées de diffusion en continu** avec lesquelles vous voulez que votre compte soit approvisionné.

Pour plus d’informations sur la mise à l’échelle des unités de diffusion en continu, voir : [Mise à l’échelle des unités de diffusion en continu](media-services-manage-origins.md#scale_streaming_endpoints.md).

##Rubriques connexes

[Diffusion d’événements en direct en continu avec Azure Media Services](media-services-live-streaming-workflow.md)

[Concepts Azure Media Services](media-services-concepts.md)

[Spécification d’ingestion en direct au format MP4 fragmenté Azure Media Services](media-services-fmp4-live-ingest-overview.md)

[live-overview]: ./media/media-services-manage-live-encoder-enabled-channels/media-services-live-streaming-new.png
 

<!---HONumber=July15_HO4-->