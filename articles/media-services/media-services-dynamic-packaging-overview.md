<properties
	pageTitle="Vue d’ensemble de l’empaquetage dynamique"
	description="Cette rubrique donne une vue d'ensemble de l'empaquetage dynamique."
	authors="Juliako"
	manager="dwrede"
	editor=""
	services="media-services"
	documentationCenter=""/>

<tags
	ms.service="media-services"
	ms.workload="media"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/07/2015"
	ms.author="juliako"/>


#Empaquetage dynamique

##Vue d'ensemble

Vous pouvez utiliser Microsoft Azure Media Services pour distribuer de nombreux formats de fichiers sources multimédias, formats de diffusion en continu de contenu multimédia et formats de protection de contenu à diverses technologies clientes (par exemple, iOS, XBOX, Silverlight, Windows 8). Ces clients comprennent différents protocoles. Par exemple, iOS nécessite un format HTTP Live Streaming (HLS) V4, tandis que Silverlight et Xbox nécessitent le format Smooth Streaming. Si vous voulez transmettre un ensemble de fichiers MP4 (ISO Base Media 14496-12) ou de fichiers Smooth Streaming à vitesse de transmission adaptative, à des clients qui utilisent le format MPEG DASH, HLS ou Smooth Streaming, nous vous recommandons de tirer profit de l'empaquetage dynamique Media Services.

Avec l'empaquetage dynamique, il vous suffit de créer un élément multimédia contenant un ensemble de fichiers MP4 ou de fichiers Smooth Streaming à vitesse de transmission adaptative. Ensuite, en fonction du format spécifié dans le manifeste ou la demande de fragment, le serveur de diffusion en continu à la demande s'assure que vous recevez le flux conforme au protocole choisi. Par conséquent, il vous suffit de stocker et de payer les fichiers dans un seul format de stockage. Le service Media Services se charge de créer et de fournir la réponse appropriée en fonction des demandes des clients.

Le diagramme suivant illustre le flux traditionnel d'encodage et d'empaquetage statique.

![Encodage statique](./media/media-services-dynamic-packaging-overview/media-services-static-packaging.png)

Le diagramme suivant illustre le flux d'empaquetage dynamique.

![Encodage dynamique](./media/media-services-dynamic-packaging-overview/media-services-dynamic-packaging.png)


>[AZURE.NOTE]Pour tirer parti de l'empaquetage dynamique, vous devez d'abord obtenir au moins une unité de diffusion en continu à la demande pour le point de terminaison de diffusion en continu à partir duquel vous envisagez de distribuer votre contenu. Pour plus d'informations, consultez [Mise à l'échelle de Media Services](media-services-manage-origins.md#scale_streaming_endpoints).

##Scénario courant

1. Téléchargez un fichier d'entrée (appelé fichier mezzanine). Par exemple, H.264, MP4 ou WMV (pour obtenir la liste des formats pris en charge, consultez [Formats pris en charge par l’encodeur Media Services](media-services-azure-media-encoder-formats)).

1. Encodez votre fichier mezzanine en ensembles de fichiers MP4 à vitesse de transmission adaptative H.264.

1. Publiez l'élément multimédia qui contient l'ensemble de fichiers MP4 à vitesse de transmission adaptative en créant le localisateur à la demande.

1. Générez les URL de diffusion en continu pour accéder à votre contenu et le diffuser en continu.

>[AZURE.NOTE]Tous les formats de fichiers MP4 ne sont pas pris en charge par l'empaquetage dynamique. Pour plus d'informations, consultez [Formats non pris en charge pour l'empaquetage dynamique](media-services-dynamic-packaging-overview.md#unsupported_formats).

##Préparation des éléments multimédias pour une diffusion en continu dynamique

Pour préparer un élément multimédia pour sa diffusion en continu dynamique, vous disposez de deux options :

- Télécharger un fichier maître et produire des ensembles de fichiers MP4 à vitesse de transmission adaptative H.264 à l'aide de l'encodeur multimédia Azure
- Télécharger les ensembles de fichiers à vitesse de transmission adaptative existants et les valider à l'aide de Media Packager

###Télécharger un fichier maître et produire des ensembles de fichiers MP4 à vitesse de transmission adaptative H.264 à l'aide de l'encodeur multimédia Azure

Pour plus d'informations sur le téléchargement et l'encodage d'éléments multimédias, consultez les articles suivants :


Télécharger vos fichiers à l'aide du **portail de gestion Azure**, de **.NET** ou de l'**API REST**

[AZURE.INCLUDE [media-services-selector-upload-files](../../includes/media-services-selector-upload-files.md)]

Encoder à l'aide de l'**encodeur multimédia Azure** en utilisant le **portail de gestion Azure**, **.NET** ou l'**API REST**

[AZURE.INCLUDE [media-services-selector-encode](../../includes/media-services-selector-encode.md)]


###Télécharger les ensembles de fichiers à vitesse de transmission adaptative existants et les valider à l'aide de Media Packager

Cette tâche est généralement souhaitable si vous téléchargez un ensemble de fichiers MP4 à vitesse de transmission adaptative qui n'ont pas été encodés à l'aide de l'encodeur Media Services. La rubrique [Validation de fichiers MP4 à vitesse de transmission adaptative encodés avec des encodeurs externes](https://msdn.microsoft.com/library/azure/dn750842.aspx) montre comment effectuer cette tâche.

##Diffusion en continu de votre contenu aux clients

Une fois que vous disposez des ensembles de fichiers à vitesse de transmission adaptative, vous pouvez publier votre élément multimédia en créant un localisateur à la demande et composer les URL de diffusion en continu pour le contenu Smooth Streaming, MPEG DASH, HLS et HDS (pour les titulaires d'une licence Adobe PrimeTime/Access uniquement).

Pour plus d'informations sur la création de localisateurs et l'utilisation de l'empaquetage dynamique pour diffuser en continu votre contenu, consultez les rubriques suivantes :

[Vue d'ensemble de la distribution de contenu aux clients](media-services-deliver-content-overview.md)

Configurer la stratégie de remise d'éléments multimédias à l'aide de **.NET** ou de l'**API REST**

[AZURE.INCLUDE [media-services-selector-asset-delivery-policy](../../includes/media-services-selector-asset-delivery-policy.md)]

Publiez des ressources (en créant des localisateurs) à l’aide du **portail de gestion Azure** ou de **.NET**.

[AZURE.INCLUDE [media-services-selector-publish](../../includes/media-services-selector-publish.md)]


##<a id="unsupported_formats"></a>Formats non pris en charge pour l'empaquetage dynamique

Les formats de fichiers sources suivants ne sont pas pris en charge par l'empaquetage dynamique.

- Fichiers MP4 Dolby Digital Plus
- Fichiers Smooth Streaming Dolby Digital Plus.

##Parcours d’apprentissage de Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Fournir des commentaires

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

<!---HONumber=Nov15_HO3-->