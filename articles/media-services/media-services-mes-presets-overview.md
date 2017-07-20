---
title: "Présélections de travaux pour MES (Media Encoder Standard) | Microsoft Docs"
description: "Cette rubrique vous propose une vue d’ensemble des présélections de travaux pour MES (Media Encoder Standard)."
author: Juliako
manager: erikre
editor: 
services: media-services
documentationcenter: 
ms.assetid: f243ed1c-ac9c-4300-a5f7-f092cf9853b9
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2017
ms.author: juliako
ms.translationtype: Human Translation
ms.sourcegitcommit: cea53acc33347b9e6178645f225770936788f807
ms.openlocfilehash: e9018ffa74c2895560adbd4c0d2b83ee67e9eb1c
ms.contentlocale: fr-fr
ms.lasthandoff: 03/03/2017

---

# <a name="task-presets-for-mes-media-encoder-standard"></a>Présélections de travaux pour MES (Media Encoder Standard)

**Media Encoder Standard** définit un ensemble de présélections d’encodage à utiliser lors de la création des travaux d’encodage. Il est recommandé d’utiliser les paramètres prédéfinis « Diffusion en continu adaptative » si vous souhaitez encoder une vidéo pour une diffusion en continu avec Media Services. Lorsque vous spécifiez cette présélection, Media Encoder Standard [générera automatiquement une échelle des vitesses de transmission](media-services-autogen-bitrate-ladder-with-mes.md). 

Toutefois, si vous avez besoin personnaliser une valeur d’encodage prédéfinie, vous devez prendre une des présélections d’encodage définies dans cette section comme modèle pour personnaliser votre configuration. Pour consulter une explication de la signification des éléments des présélections et les valeurs valides pour chaque élément, consultez la rubrique [Media Encoder Standard schema](media-services-mes-schema.md) (Schéma Media Encoder Standard).  
  
> [!NOTE]
>  Lorsque vous utilisez une présélection pour les encodages 4K, vous devez obtenir le type d’unité réservé `S3`. Pour plus d’informations, consultez [Mise à l’échelle de l’encodage](https://azure.microsoft.com/en-us/documentation/articles/media-services-portal-encoding-units).  
  
Lorsque vous opérez avec Media Encoder Standard, la rotation est activée par défaut. Si votre vidéo a été enregistrée sur un smartphone ou un autre appareil mobile en mode portrait, par défaut, ces présélections passeront en mode paysage avant l’encodage (contrairement à ce qui se passe avec Azure Media Encoder, où la vidéo est pivotée manuellement, tel qu’indiqué dans [ce](http://azure.microsoft.com/blog/2014/08/21/advanced-encoding-features-in-azure-media-encoder/) blog, dans la rubrique « Video Rotation » (Rotation vidéo).  
  
Présélections disponibles :  
  
 [H264 - Vitesse de transmission multiple -1 080 pixels - Audio 5.1](media-services-mes-preset-H264-Multiple-Bitrate-1080p-Audio-5.1.md) produit un ensemble de 8 fichiers MP4 alignés sur le groupe d’images, de 6 000 kbit/s à 400 kbit/s, et de l’audio AAC 5.1.  
  
 [H264 - Vitesse de transmission multiple -1 080 pixels](media-services-mes-preset-H264-Multiple-Bitrate-1080p.md) produit un ensemble de 8 fichiers MP4 alignés sur le groupe d’images, de 6 000 kbit/s à 400 kbit/s, et de l’audio stéréo AAC.  
  
 [H264 - Vitesse de transmission multiple -16 x 9 pour iOS](media-services-mes-preset-H264-Multiple-Bitrate-16x9-for-iOS.md) produit un ensemble de 8 fichiers MP4 alignés sur le groupe d’images, de 8 500 kbit/s à 200 kbit/s, et de l’audio stéréo AAC.  
  
 [H264 - Vitesse de transmission multiple -16 x 9 SD - Audio 5.1](media-services-mes-preset-H264-Multiple-Bitrate-16x9-SD-Audio-5.1.md) produit un ensemble de 5 fichiers MP4 alignés sur le groupe d’images, de 1 900 kbit/s à 400 kbit/s, et de l’audio AAC 5.1.  
  
 [H264 - Vitesse de transmission multiple -16 x 9 SD](media-services-mes-preset-H264-Multiple-Bitrate-16x9-SD.md) produit un ensemble de 5 fichiers MP4 alignés sur le groupe d’images, de 1 900 kbit/s à 400 kbit/s, et de l’audio stéréo AAC.  
  
 [H264 - Vitesse de transmission multiple -4 K - Audio 5.1](media-services-mes-preset-H264-Multiple-Bitrate-4K-Audio-5.1.md) produit un ensemble de 12 fichiers MP4 alignés sur le groupe d’images, de 20 000 kbit/s à 1 000 kbit/s, et de l’audio AAC 5.1.  
  
 [H264 - Vitesse de transmission multiple -4K](media-services-mes-preset-H264-Multiple-Bitrate-4K.md) produit un ensemble de 12 fichiers MP4 alignés sur le groupe d’images, de 20 000 kbit/s à 1 000 kbit/s, et de l’audio stéréo AAC.  
  
 [H264 - Vitesse de transmission multiple -4 x 3 pour iOS](media-services-mes-preset-H264-Multiple-Bitrate-4x3-for-iOS.md) produit un ensemble de 8 fichiers MP4 alignés sur le groupe d’images, de 8 500 kbit/s à 200 kbit/s, et de l’audio stéréo AAC.  
  
 [H264 - Vitesse de transmission multiple -4 x 3 SD - Audio 5.1](media-services-mes-preset-H264-Multiple-Bitrate-4x3-SD-Audio-5.1.md) produit un ensemble de 5 fichiers MP4 alignés sur le groupe d’images, de 1 600 kbit/s à 400 kbit/s, et de l’audio AAC 5.1.  
  
 [H264 - Vitesse de transmission multiple -4 x 3 pour iOS](media-services-mes-preset-H264-Multiple-Bitrate-4x3-SD.md) produit un ensemble de 5 fichiers MP4 alignés sur le groupe d’images, de 1 600 kbit/s à 400 kbit/s, et de l’audio stéréo AAC.  
  
 [H264 - Vitesse de transmission multiple -720 pixels - Audio 5.1](media-services-mes-preset-H264-Multiple-Bitrate-720p-Audio-5.1.md) produit un ensemble de 6 fichiers MP4 alignés sur le groupe d’images, de 3 400 kbit/s à 400 kbit/s, et de l’audio AAC 5.1.  
  
 [H264 - Vitesse de transmission multiple -720 pixels](media-services-mes-preset-H264-Multiple-Bitrate-720p.md) produit un ensemble de 6 fichiers MP4 alignés sur le groupe d’images, de 3400 kbit/s à 400 kbit/s, et de l’audio stéréo AAC.  
  
 [H264 - Vitesse de transmission unique - 1 080 pixels - Audio 5.1](media-services-mes-preset-H264-Single-Bitrate-1080p-Audio-5.1.md) produit un seul fichier MP4 avec une vitesse de transmission de 6 750 kbit/s, et de l’audio AAC 5.1.  
  
 [H264 - Vitesse de transmission unique - 1 080 pixels](media-services-mes-preset-H264-Single-Bitrate-1080p.md) produit un seul fichier MP4 avec une vitesse de transmission de 6 750 kbit/s, et de l’audio stéréo AAC.  
  
 [H264 - Vitesse de transmission unique - 4K - Audio 5.1](media-services-mes-preset-H264-Single-Bitrate-4K-Audio-5.1.md) produit un seul fichier MP4 avec une vitesse de transmission de 18 000 kbit/s, et de l’audio AAC 5.1.  
  
 [H264 - Vitesse de transmission unique - 4K](media-services-mes-preset-H264-Single-Bitrate-4K.md) produit un seul fichier MP4 avec une vitesse de transmission de 18 000 kbit/s, et de l’audio stéréo AAC.  
  
 [H264 - Vitesse de transmission unique - 4 x 3 SD - Audio 5.1](media-services-mes-preset-H264-Single-Bitrate-4x3-SD-Audio-5.1.md) produit un seul fichier MP4 avec une vitesse de transmission de 1 800 kbit/s, et de l’audio AAC 5.1.  
  
 [H264 - Vitesse de transmission unique - 4 x 3 SD](media-services-mes-preset-H264-Single-Bitrate-4x3-SD.md) produit un seul fichier MP4 avec une vitesse de transmission de 1 800 kbit/s, et de l’audio stéréo AAC.  
  
 [H264 - Vitesse de transmission unique - 16 x 9 SD - Audio 5.1](media-services-mes-preset-H264-Single-Bitrate-16x9-SD-Audio-5.1.md) produit un seul fichier MP4 avec une vitesse de transmission de 2 200 kbit/s, et de l’audio AAC 5.1.  
  
 [H264 - Vitesse de transmission unique - 16 x 9 SD](media-services-mes-preset-H264-Single-Bitrate-16x9-SD.md) produit un seul fichier MP4 avec une vitesse de transmission de 2 200 kbit/s, et de l’audio stéréo AAC.  
  
 [H264 - Vitesse de transmission unique - 720p - Audio 5.1](media-services-mes-preset-H264-Single-Bitrate-720p-Audio-5.1.md) produit un seul fichier MP4 avec une vitesse de transmission de 4 500 kbit/s, et de l’audio AAC 5.1.  
  
 [H264 - Vitesse de transmission unique - 720p pour Android](media-services-mes-preset-H264-Single-Bitrate-720p-for-Android.md) produit un seul fichier MP4 avec une vitesse de transmission de 2 000 kbit/s, et de l’audio stéréo AAC.  
  
 [H264 - Vitesse de transmission unique - 720p](media-services-mes-preset-H264-Single-Bitrate-720p.md) produit un seul fichier MP4 avec une vitesse de transmission de 4 500 kbit/s, et de l’audio stéréo AAC.  
  
 [H264 - Vitesse de transmission unique haute qualité SD pour Android](media-services-mes-preset-H264-Single-Bitrate-High-Quality-SD-for-Android.md) produit un seul fichier MP4 avec une vitesse de transmission de 500 kbit/s, et de l’audio stéréo AAC.  
  
 [H264 - Vitesse de transmission unique qualité faible SD pour Android](media-services-mes-preset-H264-Single-Bitrate-Low-Quality-SD-for-Android.md) produit un seul fichier MP4 avec une vitesse de transmission de 56 kbit/s, et de l’audio stéréo AAC.  
  
 Pour plus d’informations sur les encodeurs Azure Media Services, consultez la section [Vue d’ensemble et comparaison d’encodeurs multimédia à la demande Azure](https://azure.microsoft.com/en-us/documentation/articles/media-services-encode-asset/).

