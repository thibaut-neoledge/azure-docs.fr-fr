<properties 
	pageTitle="Workflow de vidéo à la demande Media Services" 
	description="Cette rubrique décrit les étapes d'un workflow type de vidéo à la demande Media Services." 
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
	ms.date="02/15/2015" 
	ms.author="juliako"/>


#Workflow de vidéo à la demande Media Services

##Vue d'ensemble

Cette rubrique décrit les étapes d'un workflow type de vidéo à la demande Azure Media Services (AMS). Chaque étape dirige vers des rubriques pertinentes. Pour les tâches pouvant être effectuées en utilisant différentes technologies, des boutons vous envoient à la technologie de votre choix (par exemple, .NET ou REST).   

Notez que vous pouvez intégrer Media Services à vos outils et processus existants. Par exemple, encodez un contenu local, puis téléchargez-le dans Media Services pour un transcodage en différents formats et une diffusion via le réseau de distribution de contenu (CDN) Azure ou via un CDN tiers. 

Le diagramme suivant présente les principaux composants de la plate-forme Media Services impliqués dans le workflow de vidéo à la demande.
![VoD workflow][vod-overview]

Pour plus d'informations, consultez [Vue d'ensemble de Media Services](media-services-overview.md).

##Création d'un compte Media Services

Utilisez le **portail de gestion Azure** pour la [Création d'un compte Azure Media Services](media-services-create-account.md). 

##Configuration de points de terminaison de diffusion en continu

[travail en cours]

##Configuration d'un environnement de développement  

Choisissez **.NET** ou **API REST** comme environnement de développement.

[AZURE.INCLUDE [media-services-selector-setup](../includes/media-services-selector-setup.md)]

##Connexion par programme  

Choisissez **.NET** ou **API REST** pour vous connecter par programme à Azure Media Services.

[AZURE.INCLUDE [media-services-selector-connect](../includes/media-services-selector-connect.md)]

##Téléchargement de médias 

Téléchargez vos fichiers à l'aide du **portail de gestion Azure**, de **.NET** ou de l'**API REST**.

[AZURE.INCLUDE [media-services-selector-upload-files](../includes/media-services-selector-upload-files.md)]

##Traitement des médias : encodage, indexation, surveillance des travaux

###Obtention du processeur multimédia

Procurez-vous le processeur multimédia avec **.NET** ou l'**API REST**.

[AZURE.INCLUDE [media-services-selector-get-media-processor](../includes/media-services-selector-get-media-processor.md)]

###Création de travaux 

Un travail est une entité qui contient des métadonnées sur un ensemble de tâches (par exemple, l'encodage ou l'indexation). Chaque tâche effectue une opération atomique sur la ou les ressources d'entrée. Pour obtenir un exemple sur la façon de créer des travaux d'encodage, consultez :

[AZURE.INCLUDE [media-services-selector-encode](../includes/media-services-selector-encode.md)]

###Surveillance de la progression des travaux

Vous pouvez surveiller la progression des travaux à l'aide du **portail de gestion Azure**, de **.NET** ou de l'**API REST**.

[AZURE.INCLUDE [media-services-selector-job-progress](../includes/media-services-selector-job-progress.md)]

###Indexation

[AZURE.INCLUDE [media-services-selector-index-content](../includes/media-services-selector-index-content.md)]

###Encodage 

Consultez [Encodage avec Azure Media Services](media-services-encode-asset.md).

##Configuration de la stratégie d'autorisation de clé de contenu 

Configurez la stratégie de protection de contenu et d'autorisation de clé à l'aide de **.NET** ou de l'**API REST**.

[AZURE.INCLUDE [media-services-selector-content-key-auth-policy](../includes/media-services-selector-content-key-auth-policy.md)]

##Configuration de la stratégie de remise de ressources

Configurez la stratégie de remise de ressources à l'aide de **.NET** ou de l'**API REST**.

[AZURE.INCLUDE [media-services-selector-asset-delivery-policy](../includes/media-services-selector-asset-delivery-policy.md)]

##Publication de ressources

Publiez des ressources (en créant des localisateurs) à l'aide du **portail de gestion Azure** ou de **.NET**.

[AZURE.INCLUDE [media-services-selector-publish](../includes/media-services-selector-publish.md)]

##Mise à l'échelle d'un compte Media Services

Vous pouvez mettre à l'échelle **Media Services** en spécifiant le nombre d'**unités réservées de diffusion en continu** et d'**unités réservées d'encodage** que vous voulez attribuer à votre compte. 

Vous pouvez aussi mettre à l'échelle votre compte Media Services en lui ajoutant des comptes de stockage. Chaque compte de stockage est limité à 500 To. Pour développer votre capacité stockage au-delà des limites par défaut, vous pouvez choisir de rattacher plusieurs comptes de stockage à un même compte Media Services.

[Cette](media-services-how-to-scale.md) rubrique est liée à des rubriques connexes.


##Lecture du contenu

Pour plus d'informations, consultez [Lecture de votre contenu avec les lecteurs existants](media-services-playback-content.md).

[vod-overview]: ./media/media-services-overview/media-services-video-on-demand.png
<!--HONumber=47-->
