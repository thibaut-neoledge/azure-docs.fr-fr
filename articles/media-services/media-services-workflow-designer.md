<properties 
	pageTitle="Créer des flux de travail d’encodage avancé avec le Concepteur de flux de travail" 
	description="Découvrez comment créer des flux de travail d’encodage avancé avec le Concepteur de flux de travail." 
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
	ms.date="09/07/2015"
	ms.author="juliako"/>


#Créer des flux de travail d’encodage avancé avec le Concepteur de flux de travail

##Vue d'ensemble
Le **Concepteur de flux de travail** est un outil de bureau Windows qui permet de concevoir et de générer des flux de travail personnalisés à encoder avec **Media Encoder Premium Workflow**.

Cet outil permet aussi de modifier des [flux de travail existants](media-services-workflow-designer.md#existing_workflows).

>[AZURE.NOTE]Pour obtenir une copie de l’outil Concepteur de flux de travail, contactez mepd@microsoft.com.


Une fois créé, un fichier de flux de travail peut être téléchargé comme ressource et être utilisé pour l'encodage de fichiers multimédias. Pour plus d’informations sur l’encodage avec **Media Encoder Premium Workflow** en utilisant **.NET**, consultez la page [Encodage avancé avec Media Encoder Premium Workflow](media-services-encode-with-premium-workflow.md).

##<a id="existing_workflows"></a>Modifier des flux de travail existants

Les fichiers de flux de travail par défaut peuvent être modifiés à l'aide de l'outil Concepteur. Vous pouvez vous procurer les fichiers de flux de travail par défaut [ici](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows). Le dossier contient aussi la description de ces fichiers.

Les vidéos suivantes vous indiquent comment utiliser le concepteur.

###Jour 1 : prise en main

La vidéo du jour 1 présente :

- Vue d'ensemble du concepteur
- Flux de travail de base : « Hello World »
- Création de plusieurs fichiers de sortie MP4 à utiliser avec la diffusion en continu d’Azure Media Services

> [AZURE.VIDEO azure-premium-encoder-workflow-designer-training-videos-day-1]

###Jour 2

La vidéo du jour 2 présente :

- Divers scénarios de fichiers source : gestion des fichiers audio
- Flux de travail avec une logique avancée
- Étapes du graphique

> [AZURE.VIDEO azure-premium-encoder-workflow-designer-training-videos-day-2]

###Jour 3

La vidéo du jour 3 présente :

- Création de scripts de flux de travail/de modèles
- Restrictions de l'encodeur actuel
- Questions et réponses
 
> [AZURE.VIDEO azure-premium-encoder-workflow-designer-training-videos-day-3]



##Parcours d’apprentissage de Media Services

Vous pouvez afficher les parcours d’apprentissage d’AMS ici :

- [Workflow en flux continu AMS](http://azure.microsoft.com/documentation/learning-paths/media-services-streaming-live/)
- [Workflow de streaming à la demande AMS](http://azure.microsoft.com/documentation/learning-paths/media-services-streaming-on-demand/)


##Voir aussi

[Vidéos de formation pour le Concepteur de flux de travail Azure Premium Encoder](http://johndeutscher.com/2015/07/06/azure-premium-encoder-workflow-designer-training-videos/)

<!---HONumber=Oct15_HO2-->