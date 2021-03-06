---
title: "Mettre à l’échelle le traitement multimédia à l’aide du portail Azure | Microsoft Docs"
description: "Ce didacticiel vous guide à travers les étapes de mise à l’échelle du traitement multimédia à l’aide du portail Azure."
services: media-services
documentationcenter: 
author: Juliako
manager: cfowler
editor: 
ms.assetid: e500f733-68aa-450c-b212-cf717c0d15da
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/04/2017
ms.author: juliako
ms.openlocfilehash: 46ca29d3e66701f2abcb185791089e94761984e8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="change-the-reserved-unit-type"></a>Modification du type d’unité réservée
> [!div class="op_single_selector"]
> * [.NET](media-services-dotnet-encoding-units.md)
> * [Portail](media-services-portal-scale-media-processing.md)
> * [REST](https://docs.microsoft.com/rest/api/media/operations/encodingreservedunittype)
> * [Java](https://github.com/southworkscom/azure-sdk-for-media-services-java-samples)
> * [PHP](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)
> 
> 

## <a name="overview"></a>Vue d'ensemble

Un compte Media Services est associé à un Type d'unité réservé qui détermine la vitesse à laquelle vos tâches de traitement multimédia sont traitées. Vous pouvez choisir entre les types d’unités réservées suivantes : **S1**, **S2** ou **S3**. Par exemple, un même travail d’encodage s’exécute plus rapidement quand vous utilisez le type d’unité réservée **S2** que le type **S1**.

En plus de spécifier le type d’unité réservée, vous pouvez approvisionner votre compte avec des **unités réservées**. Le nombre d’unités réservées approvisionnées détermine le nombre de tâches multimédias qui peuvent être traitées simultanément dans un compte donné.

>[!NOTE]
>Les unités réservées fonctionnent pour la mise en parallèle de tout le traitement multimédia, notamment les travaux d’indexation qui utilisent Azure Media Indexer. Toutefois, contrairement à l’encodage, l’indexation des travaux n’est pas plus rapide avec des unités réservées plus rapides.

> [!IMPORTANT]
> Pour obtenir plus d’informations sur la mise à l’échelle du traitement multimédia, consultez la rubrique de [présentation](media-services-scale-media-processing-overview.md) .
> 
> 

## <a name="scale-media-processing"></a>Mise à l’échelle du traitement multimédia
Pour modifier le type d’unité réservée et le nombre d’unités réservées, procédez comme suit :

1. Dans le [portail Azure](https://portal.azure.com/), sélectionnez votre compte Azure Media Services.
2. Dans la fenêtre **Paramètres**, sélectionnez **Unités réservées Multimédia**.
   
    Pour modifier le nombre d’unités réservées pour le type d’unités réservées sélectionné, utilisez le curseur **Unités réservées multimédia** .
   
    Pour modifier le **TYPE D’UNITÉ RÉSERVÉE**, appuyez sur S1, S2 ou S3.
   
    ![Page Processors](./media/media-services-portal-scale-media-processing/media-services-scale-media-processing.png)
3. Appuyez sur le bouton ENREGISTRER pour enregistrer vos modifications.
   
    Les nouvelles unités réservées sont allouées lorsque vous cliquez sur ENREGISTRER.

## <a name="next-steps"></a>Étapes suivantes
Consultez les parcours d’apprentissage de Media Services.

[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fournir des commentaires
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

