---
title: " Téléchargement de fichiers dans un compte Media Services à l’aide du portail Azure | Microsoft Docss"
description: "Ce didacticiel vous guide à travers les étapes de téléchargement de fichiers dans un compte Media Services à l’aide du portail Azure."
services: media-services
documentationcenter: 
author: Juliako
manager: erikre
editor: 
ms.assetid: 3ad3dcea-95be-4711-9aae-a455a32434f6
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/13/2017
ms.author: juliako
translationtype: Human Translation
ms.sourcegitcommit: ed8ea30b87c8086d41cab879acce82062f08b31c
ms.openlocfilehash: f27ab42ab3c7c704804b9a5493c8b3acd954decb


---
# <a name="upload-files-into-a-media-services-account-using-the-azure-portal"></a>Téléchargement de fichiers dans un compte Media Services à l’aide du portail Azure
> [!div class="op_single_selector"]
> * [Portail](media-services-portal-upload-files.md)
> * [.NET](media-services-dotnet-upload-files.md)
> * [REST](media-services-rest-upload-files.md)
> 
> [!NOTE]
> Pour suivre ce didacticiel, vous avez besoin d'un compte Azure. Pour plus d'informations, consultez la page [Version d'évaluation gratuite d'Azure](https://azure.microsoft.com/pricing/free-trial/). 
> 


Dans Media Services, vous téléchargez vos fichiers numériques dans une ressource. L’élément multimédia peut contenir des fichiers vidéo, des fichiers audio, des images, des collections de miniatures, des pistes textuelles et des légendes (ainsi que les métadonnées concernant ces fichiers). Une fois les fichiers téléchargés, votre contenu est stocké en toute sécurité dans le cloud et peut faire l’objet d’un traitement et d’une diffusion en continu.


## <a name="upload-files"></a>Charger des fichiers

>[!NOTE]
>Une limite est appliquée à la taille maximale des fichiers pris en charge pour le traitement dans Media Services. Consultez [cette rubrique](media-services-quotas-and-limitations.md) pour en savoir plus sur les limites de taille des fichiers.
>

1. Dans le [portail Azure](https://portal.azure.com/), sélectionnez votre compte Azure Media Services.
2. Dans le panneau **Paramètres**, cliquez sur **Éléments multimédias**.
   
    ![Charger des fichiers](./media/media-services-portal-vod-get-started/media-services-upload.png)
3. Cliquez sur le bouton **Télécharger** .
   
    La fenêtre **Upload a video asset** (Charger un élément multimédia vidéo) s’affiche.
   
   > [!NOTE]
   > Il n’existe aucune limite de taille de fichier.
   > 
   > 
4. Accédez à la vidéo de votre choix sur votre ordinateur, sélectionnez-la, puis cliquez sur OK.  
   
    Le chargement démarre ; vous pouvez en voir la progression sous le nom du fichier.  

Une fois le téléchargement terminé, le nouvel élément multimédia s’affiche dans la fenêtre **Éléments multimédias** . 

## <a name="next-steps"></a>Étapes suivantes
Vous pouvez désormais encoder vos éléments multimédias téléchargés. Pour plus d'informations, consultez [Encode an asset using Media Encoder Standard with the Azure portal (Encoder un élément multimédia à l’aide de Media Encoder Standard avec le portail Azure)](media-services-portal-encode.md).

Vous pouvez également utiliser les fonctions Azure pour déclencher une tâche de codage à partir d’un fichier entrant dans le conteneur configuré. Pour plus d’informations, consultez [cet exemple](https://azure.microsoft.com/resources/samples/media-services-dotnet-functions-integration/ ).

## <a name="media-services-learning-paths"></a>Parcours d’apprentissage de Media Services
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fournir des commentaires
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]




<!--HONumber=Feb17_HO2-->


