---
title: "Charger des fichiers dans un compte Azure Media Services à partir d’Azure StorSimple | Microsoft Docs"
description: "Cet article donne une brève vue d’ensemble de Azure StorSimple Data Manager. L’article contient également des liens vers des didacticiels qui expliquent comment extraire des données de StorSimple et les charger en tant que ressources dans un compte Azure Media Services."
services: media-services
documentationcenter: 
author: Juliako
manager: erikre
editor: 
ms.assetid: 1dd09328-262b-43ef-8099-73241b49a925
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/27/2017
ms.author: juliako
translationtype: Human Translation
ms.sourcegitcommit: 5cce99eff6ed75636399153a846654f56fb64a68
ms.openlocfilehash: 866bc2c05acafa5ce6bf535bfce2de577cf98c0b
ms.lasthandoff: 03/31/2017


---
# <a name="upload-files-into-an-azure-media-services-account-from-azure-storsimple"></a>Charger des fichiers dans un compte Azure Media Services à partir d’Azure StorSimple

Cet article donne une brève vue d’ensemble d’Azure StorSimple Data Manager. L’article contient également des liens vers des didacticiels qui expliquent comment extraire des données de StorSimple et les charger en tant que ressources dans un compte Azure Media Services (AMS).

> 
> [!NOTE]
> Azure StorSimple Data Manager est actuellement disponible en version préliminaire privée. 
> 

## <a name="overview"></a>Vue d'ensemble

Dans Media Services, vous téléchargez vos fichiers numériques dans une ressource. L’élément multimédia peut contenir des fichiers vidéo, des fichiers audio, des images, des collections de miniatures, des pistes textuelles et des légendes (ainsi que les métadonnées concernant ces fichiers). Une fois les fichiers téléchargés, votre contenu est stocké en toute sécurité dans le cloud et peut faire l’objet d’un traitement et d’un streaming.

[Azure StorSimple](https://docs.microsoft.com/azure/storsimple/) utilise le stockage cloud pour étendre la solution sur site et hiérarchise automatiquement les données sur le stockage local et le stockage cloud. L’appareil StorSimple déduplique et compresse les données avant de les envoyer vers le cloud. Il améliore considérablement l’efficacité d’envoi de fichiers volumineux vers le cloud. Le service [StorSimple Data Manager](../storsimple/storsimple-data-manager-overview.md) fournit des API vous permettant d’extraire des données à partir de StorSimple et de les présenter sous la forme de ressources AMS.

## <a name="get-started"></a>Prise en main

1. [Créer un compte Media Services](media-services-portal-create-account.md) dans lequel vous souhaitez transférer les ressources.
2. Inscrivez-vous à la version préliminaire de Data Manager, comme décrit dans l’article [StorSimple Data Manager](../storsimple/storsimple-data-manager-overview.md).
3. Créez un compte StorSimple Data Manager.
4. Créez un travail de transformation de données qui extrait des données d’un appareil StorSimple et les transfère vers un compte AMS en tant que ressources. 

    Une file d’attente de stockage est créée dès le début d’exécution du travail. Cette file d’attente est renseignée avec des messages sur les objets blob transformés, au fur et à mesure de leur mise à disposition. Le nom de cette file d’attente est identique à celui de la définition du travail. Vous pouvez utiliser cette file d’attente pour déterminer quand une ressource est prête et appeler l’opération Media Services souhaitée pour l’exécuter sur celle-ci. Par exemple, vous pouvez utiliser cette file d’attente pour déclencher une fonction Azure qui contient le code Media Services nécessaire.

## <a name="see-also"></a>Voir aussi

[Utiliser le Kit de développement logiciel (SDK) .Net pour déclencher des travaux dans le gestionnaire de données](../storsimple/storsimple-data-manager-dotnet-jobs.md)

## <a name="media-services-learning-paths"></a>Parcours d’apprentissage de Media Services
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fournir des commentaires
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Étapes suivantes

Vous pouvez désormais encoder vos éléments multimédias téléchargés. Pour plus d'informations, consultez [Encode an asset using Media Encoder Standard with the Azure portal (Encoder un élément multimédia à l’aide de Media Encoder Standard avec le portail Azure)](media-services-portal-encode.md).

