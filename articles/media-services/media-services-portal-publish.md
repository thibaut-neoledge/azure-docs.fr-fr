---
title: " Publier du contenu avec le Portail Azure | Microsoft Docs"
description: Ce didacticiel vous guide à travers les étapes de publication de votre contenu avec le Portail Azure.
services: media-services
documentationcenter: ''
author: Juliako
manager: erikre
editor: ''

ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/29/2016
ms.author: juliako

---
# Publier du contenu avec le Portail Azure
> [!div class="op_single_selector"]
> * [Portail](media-services-portal-publish.md)
> * [.NET](media-services-deliver-streaming-content.md)
> * [REST](media-services-rest-deliver-streaming-content.md)
> 
> 

## Vue d'ensemble
> [!NOTE]
> Pour suivre ce didacticiel, vous avez besoin d'un compte Azure. Pour plus d'informations, consultez la page [Version d'évaluation gratuite d'Azure](https://azure.microsoft.com/pricing/free-trial/).
> 
> 

Pour fournir aux utilisateurs une URL pouvant être utilisée pour diffuser en continu ou télécharger votre contenu, vous devez d’abord « publier » votre élément multimédia en créant un localisateur. Les localisateurs assurent l’accès aux fichiers contenus dans l’élément multimédia. Media Services prend en charge deux types de localisateurs :

* les localisateurs de diffusion en continu (OnDemandOrigin), utilisés pour la diffusion adaptative (par exemple, MPEG DASH, HLS ou Smooth Streaming) ; Pour créer un localisateur de diffusion en continu, votre élément multimédia doit contenir un fichier .ism ;
* les localisateurs progressifs (SAS), utilisés pour la diffusion de vidéo par téléchargement progressif.

Les URL de diffusion en continu, que vous pouvez utiliser pour lire des éléments multimédias Smooth Streaming, ont le format suivant.

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest

Pour créer une URL de diffusion en continu HLS, ajoutez (format=m3u8-aapl) à l’URL.

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl)

Pour créer une URL de diffusion en continu MPEG DASH, ajoutez (format=mpd-time-csf) à l’URL.

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf)

Une URL SAS a le format suivant :

    {blob container name}/{asset name}/{file name}/{SAS signature}

Pour plus d’informations, consultez [Fournir du contenu (vue d’ensemble)](media-services-deliver-content-overview.md).

> [!NOTE]
> Si vous avez utilisé le portail pour créer des localisateurs avant mars 2015, des localisateurs présentant une date d’expiration de deux ans ont été créés.
> 
> 

Pour mettre à jour la date d’expiration d’un localisateur, utilisez les API [REST](http://msdn.microsoft.com/library/azure/hh974308.aspx#update_a_locator) ou [.NET](http://go.microsoft.com/fwlink/?LinkID=533259). Notez que lorsque vous mettez à jour la date d’expiration d’un localisateur SAS, l’URL est modifiée.

### Pour publier un élément multimédia à l’aide du portail
Pour utiliser le portail pour publier un élément multimédia, procédez comme suit :

1. Connectez-vous au [portail Azure](https://portal.azure.com/).
2. Sélectionnez **Paramètres** > **Éléments multimédias**.
3. Sélectionnez l’élément que vous souhaitez publier.
4. Cliquez sur le bouton **Publier**.
5. Sélectionnez le type de localisateur.
6. Cliquez sur **Ajouter**.
   
    ![Publish](./media/media-services-portal-vod-get-started/media-services-publish1.png)

L’URL sera ajoutée à la liste des **URL publiées**.

## Lecture de contenu sur le portail
Le portail Azure propose un lecteur de contenu que vous pouvez utiliser pour tester votre vidéo.

Cliquez sur la vidéo de votre choix, puis sur le bouton **Lire**.

![Publish](./media/media-services-portal-vod-get-started/media-services-play.png)

Certaines considérations s’appliquent :

* Assurez-vous que la vidéo a été publiée.
* Le **lecteur multimédia** effectue la lecture à partir du point de terminaison de diffusion en continu par défaut. Si vous souhaitez lire à partir d’un autre point de terminaison de diffusion en continu que celui par défaut, cliquez sur l’URL pour la copier et utilisez un autre lecteur, par exemple, le [lecteur Azure Media Services](http://amsplayer.azurewebsites.net/azuremediaplayer.html).
* Le point de terminaison de streaming à partir duquel vous diffusez en continu doit être en cours d’exécution.
* Pour diffuser en continu à partir d’un point de terminaison de streaming, vous devez ajouter au moins une unité de diffusion en continu. Pour plus d’informations, consultez [cette rubrique](media-services-portal-scale-streaming-endpoints.md).

## Étapes suivantes
Consultez les parcours d’apprentissage de Media Services.

[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## Fournir des commentaires
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

<!---HONumber=AcomDC_0831_2016-->