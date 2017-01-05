---
title: "Créer des flux de travail d’encodage avancé avec le Concepteur de flux de travail | Microsoft Docs"
description: "Découvrez comment créer des flux de travail d’encodage avancé avec le Concepteur de flux de travail."
services: media-services
documentationcenter: 
author: anilmur
manager: erikre
editor: 
ms.assetid: 004815f2-0761-4706-87a1-675ba36e0322
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/15/2016
ms.author: juliako;johndeu;anilmur
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: caf697786ce2a95dd0d4168d6ffa26bf4bee525a


---
# <a name="create-advanced-encoding-workflows-with-workflow-designer"></a>Créer des flux de travail d’encodage avancé avec le Concepteur de flux de travail
## <a name="overview"></a>Vue d'ensemble
Le **Concepteur de flux de travail** est un outil de bureau Windows qui permet de concevoir et de générer des flux de travail personnalisés à encoder avec **Media Encoder Premium Workflow**.
À l'aide de la puissance de l'outil de conception de flux de travail, vous pouvez concevoir et créer des flux de travail complexes qui seront exécutés dans **Media Encoder Premium**.  

Les flux de travail peuvent inclure la logique de décision et le branchement client basés sur les propriétés du fichier source d'entrée. Vous pouvez créer des flux de travail avec des propriétés substituables et des valeurs dynamiques pour rendre même les tâches d'encodage les plus complexes faciles à répéter et à personnaliser dans le cloud.

Exemples de flux de travail que vous pouvez créer :

* Flux de travail basés sur décision qui inspectent le contenu source pour la résolution et codent uniquement les pistes de sortie souhaitées.  Ceci sert à éliminer les pistes gaspillées générées par le dimensionnement par inadvertance du contenu source.
* Des fichiers d'entrée multiples peuvent être utilisés pour prendre en charge des légendes, des superpositions et pour assembler le contenu. 

Cet outil permet aussi de modifier nos [flux de travail publiés](media-services-workflow-designer.md#existing_workflows). 

> [!NOTE]
> Pour obtenir votre copie de l'outil Concepteur de flux de travail, contactez mepd@microsoft.com.
> 
> 

Une fois créé, un fichier de flux de travail peut être téléchargé comme ressource et être utilisé pour l'encodage de fichiers multimédias. Pour plus d'informations sur l'encodage avec **Media Encoder Premium Workflow** en utilisant **.NET**, consultez la page [Encodage avancé avec Media Encoder Premium Workflow](media-services-encode-with-premium-workflow.md).

## <a name="a-idexistingworkflowsamodify-existing-workflows"></a><a id="existing_workflows"></a>Modifier des flux de travail existants
Les [flux de travail publiés](media-services-workflow-designer.md#existing_workflows) par défaut peuvent être modifiés à l'aide de l'outil Concepteur. Vous pouvez vous procurer les fichiers de flux de travail par défaut [ici](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows). Le dossier contient aussi la description de ces fichiers.

Les vidéos suivantes vous indiquent comment utiliser le concepteur.

### <a name="day-1--getting-started"></a>Jour 1 : prise en main
La vidéo du jour 1 présente :

* Vue d'ensemble du concepteur
* Flux de travail de base : « Hello World »
* Création de plusieurs fichiers de sortie MP4 à utiliser avec la diffusion en continu d’Azure Media Services

> [!VIDEO https://channel9.msdn.com/Blogs/Windows-Azure/Azure-Premium-Encoder-Workflow-Designer-Training-Videos-Day-1/player]
> 
> 

### <a name="day-2"></a>Jour 2
La vidéo du jour 2 présente :

* Divers scénarios de fichiers source : gestion des fichiers audio
* Flux de travail avec une logique avancée
* Étapes du graphique

> [!VIDEO https://channel9.msdn.com/Blogs/Windows-Azure/Azure-Premium-Encoder-Workflow-Designer-Training-Videos-Day-2/player]
> 
> 

### <a name="day-3"></a>Jour 3
La vidéo du jour 3 présente :

* Création de scripts de flux de travail/de modèles
* Restrictions de l'encodeur actuel
* Questions et réponses  

> [!VIDEO https://channel9.msdn.com/Blogs/Windows-Azure/Azure-Premium-Encoder-Workflow-Designer-Training-Videos-Day-3/player]
> 
> 

## <a name="next-step"></a>Étape suivante
Consultez les parcours d’apprentissage de Media Services.

[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fournir des commentaires
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

Si vous avez besoin d'aide ou si vous avez des questions à propos de la création de flux de travail personnalisés dans l'outil Concepteur de flux de travail, envoyez un courrier électronique à mepd@microsoft.com.

## <a name="see-also"></a>Voir aussi
[Vidéos de formation pour le Concepteur de flux de travail Azure Premium Encoder](http://johndeutscher.com/2015/07/06/azure-premium-encoder-workflow-designer-training-videos/)




<!--HONumber=Nov16_HO3-->


