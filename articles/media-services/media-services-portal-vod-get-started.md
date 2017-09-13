---
title: "Prise en main de la diffusion de vidéo à la demande à l’aide du portail Azure | Microsoft Docs"
description: "Ce didacticiel explique comment implémenter un service de base de diffusion de contenu vidéo à la demande avec l’application Azure Media Services du portail Azure."
services: media-services
documentationcenter: 
author: Juliako
manager: cfowler
editor: 
ms.assetid: 6c98fcfa-39e6-43a5-83a5-d4954788f8a4
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/07/2017
ms.author: juliako
ms.translationtype: HT
ms.sourcegitcommit: 3eb68cba15e89c455d7d33be1ec0bf596df5f3b7
ms.openlocfilehash: fb981f3240799c924464c828b2c835ac5d9879ed
ms.contentlocale: fr-fr
ms.lasthandoff: 09/01/2017

---
# <a name="get-started-with-delivering-content-on-demand-by-using-the-azure-portal"></a>Prise en main de la diffusion de contenus à la demande à l’aide du portail Azure
[!INCLUDE [media-services-selector-get-started](../../includes/media-services-selector-get-started.md)]

Ce didacticiel explique comment implémenter un service de base de diffusion de contenu vidéo à la demande avec l’application Azure Media Services du portail Azure.

## <a name="prerequisites"></a>Composants requis
Les éléments suivants sont requis pour suivre le didacticiel :

* Un compte Azure. Pour plus d’informations, consultez la page [Version d’évaluation gratuite d’Azure](https://azure.microsoft.com/pricing/free-trial/). 
* Un compte Media Services. Pour créer un compte Media Services, consultez [Création d’un compte Media Services](media-services-portal-create-account.md).

Ce didacticiel comprend les tâches suivantes :

1. Démarrez le point de terminaison de streaming.
2. Télécharger un fichier vidéo.
3. Encoder le fichier source en un ensemble de fichiers MP4 à débit adaptatif.
4. Publiez la ressource et obtenez les URL de streaming et de téléchargement progressif.  
5. Lire votre contenu.

## <a name="start-the-streaming-endpoint"></a>Démarrer le point de terminaison de streaming

L’un des scénarios les plus courants lorsque vous utilisez Azure Media Services est le streaming à débit adaptatif. Media Services vous offre l’empaquetage dynamique. Avec l’empaquetage dynamique, vous pouvez distribuer votre contenu MP4 encodé à débit adaptatif dans des formats de streaming juste-à-temps pris en charge par Media Services. Parmi les exemples, nous pouvons citer Apple HTTP Live Streaming (HLS), Microsoft Smooth Streaming et Dynamic Adaptive Streaming sur HTTP (DASH, aussi appelé MPEG-DASH). À l’aide du streaming à débit adaptatif de Media Services, vous pouvez diffuser vos vidéos sans stocker les versions préconfigurées de chacun de ces formats de streaming.

> [!NOTE]
> Lorsque vous créez un compte Media Services, un point de terminaison de streaming par défaut est ajouté à votre compte à l’état **Arrêté**. Pour démarrer le streaming de votre contenu et tirer parti de l’empaquetage et du chiffrement dynamiques, le point de terminaison de streaming à partir duquel vous souhaitez diffuser du contenu doit se trouver à l’état **En cours d’exécution**. 

Pour démarrer le point de terminaison de streaming :

1. Connectez-vous au [portail Azure](https://portal.azure.com/).
2. Sélectionnez **Paramètres** > **Points de terminaison de streaming**. 
3. Cliquez sur le point de terminaison de streaming par défaut. La fenêtre **DEFAULT STREAMING ENDPOINT DETAILS** (DÉTAILS DU POINT DE TERMINAISON DE DIFFUSION EN CONTINU PAR DÉFAUT) s’affiche.
4. Cliquez sur l’icône **Démarrer**.
5. Sélectionnez le bouton **Enregistrer**.

## <a name="upload-files"></a>Charger des fichiers
Pour diffuser des vidéos en continu à l’aide de Media Services, chargez les vidéos source, encodez-les en plusieurs débits et publiez le résultat. Cette section décrit la première étape du processus. 

1. Dans le [portail Azure](https://portal.azure.com/), sélectionnez votre compte Azure Media Services.
2. Sélectionnez **Paramètres** > **Éléments multimédias**. Ensuite, cliquez sur le bouton **Charger**.
   
    ![Charger des fichiers](./media/media-services-portal-vod-get-started/media-services-upload.png)
   
    La fenêtre **Upload a video asset** (Charger un élément multimédia vidéo) s’affiche.
   
   > [!NOTE]
   > Media Services ne limite pas la taille de fichier des vidéos chargées.
   > 
   > 
3. Sur votre ordinateur, accédez à la vidéo que vous souhaitez charger. Sélectionnez la vidéo, puis cliquez sur **OK**.  
   
    Le chargement démarre. La progression s’affiche sous le nom du fichier.  

Une fois le chargement terminé, la nouvelle ressource est répertoriée dans le volet **Ressources**. 

## <a name="encode-assets"></a>Encoder des éléments multimédias
Pour tirer parti de l’empaquetage dynamique, vous devez encoder votre fichier source en un ensemble de fichiers MP4 à plusieurs vitesses de transmission. Les étapes d’encodage sont présentées dans cette section.

### <a name="encode-assets-in-the-portal"></a>Encoder les ressources dans le portail
Pour encoder votre contenu à l’aide de Media Encoder Standard dans le portail Azure :

1. Dans le [portail Azure](https://portal.azure.com/), sélectionnez votre compte Azure Media Services.
2. Sélectionnez **Paramètres** > **Éléments multimédias**. Sélectionnez la ressource que vous souhaitez encoder.
3. Cliquez sur le bouton **Encoder** .
4. Dans la fenêtre **Encoder une ressource**, sélectionnez le processeur **Media Encoder Standard** et choisissez une présélection. Pour plus d’informations sur les présélections, consultez les articles [Générer automatiquement une échelle de vitesses de transmission](media-services-autogen-bitrate-ladder-with-mes.md) et [Présélections de tâches pour Media Encoder Standard](media-services-mes-presets-overview.md). Il est important de choisir la présélection qui correspond à votre vidéo d’entrée. Par exemple, si vous savez que votre vidéo d’entrée possède une résolution de 1 920 x 1 080 pixels, vous pouvez utiliser la présélection **H264 multidébit 1 080 pixels**. Si vous disposez d’une basse résolution vidéo (640 x 360), n’utilisez pas la présélection **H264 multidébit 1 080 pixels**.
   
   Pour vous aider à gérer vos ressources, vous pouvez modifier le nom de la ressource de sortie et le nom du travail.
   
   ![Encoder des éléments multimédias](./media/media-services-portal-vod-get-started/media-services-encode1.png)
5. Sélectionnez **Créer**.

### <a name="monitor-encoding-job-progress"></a>Suivi de la progression de la tâche d’encodage
Pour surveiller la progression du travail d’encodage, en haut de la page, cliquez sur **Paramètres**, puis sélectionnez **Travaux**.

![Tâches](./media/media-services-portal-vod-get-started/media-services-jobs.png)

## <a name="publish-content"></a>Publication de contenu
Pour fournir aux utilisateurs une URL pouvant être utilisée pour diffuser en continu ou télécharger votre contenu, vous devez d’abord publier votre ressource en créant un localisateur. Les localisateurs assurent l’accès aux fichiers contenus dans la ressource. Azure Media Services prend en charge deux types de localisateurs : 

* **Localisateurs de streaming (OnDemandOrigin)**. Les localisateurs de streaming sont utilisés pour la diffusion en continu adaptative . Exemples de diffusion en continu adaptative de HLS, Smooth Streaming et MPEG-DASH. Pour créer un localisateur de streaming, votre ressource doit contenir un fichier .ism. 
* **Localisateurs progressifs (signature d’accès partagé)**. Les localisateurs progressifs sont utilisés pour diffuser des vidéos par téléchargement progressif.

Pour créer une URL de streaming HLS, ajoutez *(format=m3u8-aapl)* à l’URL :

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{file name}.ism/Manifest(format=m3u8-aapl)

Pour générer une URL de streaming afin de lire les ressources Smooth Streaming, utilisez le format d’URL suivant :

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{file name}.ism/Manifest

Pour créer une URL de streaming MPEG DASH, ajoutez *(format=mpd-time-csf)* à l’URL :

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{file name}.ism/Manifest(format=mpd-time-csf)

Une URL de signature d’accès partagé a le format suivant :

    {blob container name}/{asset name}/{file name}/{shared access signature}

> [!NOTE]
> Les localisateurs qui ont été créés dans le portail Azure avant mars 2015 ont une date d’expiration de deux ans.  
> 
> 

Pour mettre à jour une date d’expiration sur un localisateur, vous pouvez utiliser une [API REST](https://docs.microsoft.com/rest/api/media/operations/locator#update_a_locator) ou une [API .NET](http://go.microsoft.com/fwlink/?LinkID=533259). 

> [!NOTE]
> Lorsque vous mettez à jour la date d’expiration d’un localisateur de signature d’accès partagé, l’URL est modifiée.

### <a name="to-use-the-portal-to-publish-an-asset"></a>Pour publier un élément multimédia à l’aide du portail
1. Dans le [portail Azure](https://portal.azure.com/), sélectionnez votre compte Azure Media Services.
2. Sélectionnez **Paramètres** > **Éléments multimédias**. Sélectionnez l’élément que vous souhaitez publier.
3. Cliquez sur le bouton **Publier**.
4. Sélectionnez le type de localisateur.
5. Sélectionnez **Ajouter**.
   
    ![Publier la vidéo](./media/media-services-portal-vod-get-started/media-services-publish1.png)

L’URL est ajoutée à la liste des **URL publiées**.

## <a name="play-content-from-the-portal"></a>Lecture de contenu sur le portail
Vous pouvez tester votre vidéo sur un lecteur de contenu dans le portail Azure.

Sélectionnez la vidéo, puis cliquez sur le bouton **Lecture**.

![Lire la vidéo dans le portail Azure](./media/media-services-portal-vod-get-started/media-services-play.png)

Certaines considérations s’appliquent :

* Pour commencer le streaming, démarrez l’exécution du point de terminaison de streaming par défaut.
* Assurez-vous que la vidéo a été publiée.
* Le lecteur multimédia du portail Azure effectue la lecture à partir du point de terminaison de diffusion en continu par défaut. Si vous souhaitez lire à partir d’un autre point de terminaison de streaming que celui par défaut, sélectionnez et copiez l’URL dans un autre lecteur. Par exemple, vous pouvez tester votre vidéo avec le lecteur [Azure Media Player](http://amsplayer.azurewebsites.net/azuremediaplayer.html).

## <a name="provide-feedback"></a>Fournir des commentaires
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Étapes suivantes
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

