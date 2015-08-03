<properties 
	pageTitle="Forum Aux Questions" 
	description="Forum Aux Questions (FAQ)" 
	services="media-services" 
	documentationCenter="" 
	authors="Juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/17/2015" 
	ms.author="juliako"/>


#Forum Aux Questions  

##Vue d'ensemble

Q : comment mettre à l’échelle l’indexation ?

R : les unités réservées sont les mêmes pour les tâches d’encodage et d’indexation. Suivez les instructions de la page [Comment mettre à l’échelle des unités réservées mise à l’échelle des unités réservées d’encodage](media-services-how-to-scale.md). **Remarque** : le fonctionnement de l’indexeur n’est pas affecté par le type d’unité réservée.

Q : j’ai chargé, encodé et publié une vidéo. Pourquoi la vidéo n’est-elle pas lue lorsque j’essaie de la diffuser en continu ?

R : une des raisons les plus courantes est que vous n’avez pas au moins une unité réservée de diffusion en continu allouée sur le point de terminaison de diffusion en continu à partir duquel vous essayez de lire la vidéo. Suivez les instructions de la page [Mise à l’échelle des unités réservées de diffusion en continu](media-services-how-to-scale.md).

Q : la composition d’un flux dynamique est-elle possible ?

R : la composition de flux dynamiques n’est pas disponible pour le moment dans Azure Media Services. Vous devez donc effectuer la composition au préalable sur votre ordinateur.

Q : puis-je utiliser le CDN Azure avec la vidéo en flux continu ?

R : Media Services prend en charge l’intégration au CDN Azure (pour plus d’informations, consultez la rubrique [Gestion des points de terminaison de diffusion en continu dans un compte Media Services](media-services-manage-origins.md#enable_cdn)). Vous pouvez utiliser la vidéo en flux continu avec le CDN. Azure Media Services fournit des sorties aux formats Smooth Streaming, HLS et MPEG-DASH. Tous ces formats utilisent le protocole HTTP pour transférer les données et bénéficient des avantages de la mise en cache HTTP. Lors d’une diffusion vidéo en flux continu, les données vidéo/audio sont divisées en fragments individuels qui sont mis en cache dans le CDN. Les seules données devant être actualisées sont les données de manifeste. CDN actualise régulièrement les données de manifeste.

Q : le stockage des images est-il pris en charge par Azure Media Services ?

R : si vous cherchez uniquement à stocker des images JPEG ou PNG, nous vous recommandons de les conserver dans le stockage d’objets blob Azure. Il n’y a aucun avantage à les placer dans votre compte Media Services, à moins que vous souhaitiez qu’elles restent associées à vos ressources vidéo ou audio ou les utiliser sous forme de superpositions dans l’encodeur vidéo. L’encodeur Media Services prend en charge la superposition d’images sur les vidéos, ce qui explique pourquoi JPEG et PNG figurent parmi les formats d’entrée pris en charge. Pour plus d’informations, consultez la page [Création de superpositions](https://msdn.microsoft.com/library/azure/dn640496.aspx).

Q : comment puis-je copier des éléments multimédias d’un compte Media Services vers un autre ?

R : pour copier des éléments multimédias d’un compte Media Services vers un autre, utilisez la méthode d’extension [IAsset.Copy](https://github.com/Azure/azure-sdk-for-media-services-extensions/blob/dev/MediaServices.Client.Extensions/IAssetExtensions.cs#L354) disponible dans le référentiel [Extensions du Kit de développement logiciel (SDK) Azure Media Services](https://github.com/Azure/azure-sdk-for-media-services-extensions/). Pour plus d’informations, consultez [cette publication de forum](https://social.msdn.microsoft.com/Forums/azure/28912d5d-6733-41c1-b27d-5d5dff2695ca/migrate-media-services-across-subscription?forum=MediaServices).

<!---HONumber=July15_HO4-->