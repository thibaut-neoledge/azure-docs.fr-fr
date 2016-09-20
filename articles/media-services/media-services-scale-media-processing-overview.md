<properties
	pageTitle="Vue d’ensemble de la mise à l’échelle du traitement multimédia | Microsoft Azure"
	description="Cette rubrique est une présentation de la mise à l’échelle du traitement multimédia avec Azure Media Services."
	services="media-services"
	documentationCenter=""
	authors="juliako"
	manager="erikre"
	editor=""/>

<tags
	ms.service="media-services"
	ms.workload="media"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/29/2016"
	ms.author="juliako"/>


# Vue d’ensemble de la mise à l’échelle du traitement multimédia

Cette page fournit une vue d’ensemble de la procédure de mise à l’échelle du traitement multimédia et de ses avantages.

## Vue d'ensemble

Un compte Media Services est associé à un Type d'unité réservé qui détermine la vitesse à laquelle vos tâches de traitement multimédia sont traitées. Vous pouvez choisir entre les types d’unités réservées suivantes : **S1**, **S2** ou **S3**. Par exemple, un même travail d’encodage s’exécute plus rapidement quand vous utilisez le type d’unité réservée **S2** que le type **S1**. Pour plus d’informations, consultez [Types d’unités réservées](https://azure.microsoft.com/blog/high-speed-encoding-with-azure-media-services/).

En plus de spécifier le type d’unité réservée, vous pouvez spécifier d’approvisionner votre compte avec des unités réservées. Le nombre d’unités réservées approvisionnées détermine le nombre de tâches de média qui peuvent être traitées simultanément dans un compte donné. Si, par exemple, votre compte a cinq unités réservées, les cinq tâches multimédias sont exécutées simultanément tant qu’il y a des tâches à traiter. Les autres tâches restent dans la file d'attente et sont sélectionnées séquentiellement pour le traitement quand l'exécution d'une tâche se termine. Si aucune unité réservée n'est approvisionnée pour un compte donné, les tâches sont sélectionnées séquentiellement. Dans ce cas, le temps d'attente entre l'achèvement d'une tâche et le démarrage de la suivante dépend de la disponibilité des ressources dans le système.

## Choix entre les différents types d’unités réservées

Le tableau suivant vous permet de prendre de décision lors du choix entre les différentes vitesses d’encodage. Il fournit également quelques cas de référence et des URL SAP que vous pouvez utiliser pour télécharger des vidéos sur lesquelles vous pouvez effectuer vos propres tests :

Scénarios|**S1**|**S2**|**S3**|
----------|------------|----------|------------
Cas d’utilisation prévue| Encodage à débit binaire unique. <br/>Fichiers avec une résolution SD ou inférieure, insensibles à l’heure, à moindre coût.|Encodage à débit binaire unique et à débit binaire multiple.<br/>Utilisation normale de l’encodage SD et HD. |Encodage à débit binaire unique et à débit binaire multiple.<br/>Vidéos avec une résolution HD complète et 4K. Encodage sensible à l’heure, plus rapide. 
Référence|[Fichier d’entrée : durée de 5 minutes 640x360p à 29,97 images par seconde](https://wamspartners.blob.core.windows.net/for-long-term-share/Whistler_5min_360p30.mp4?sr=c&si=AzureDotComReadOnly&sig=OY0TZ%2BP2jLK7vmcQsCTAWl33GIVCu67I02pgarkCTNw%3D).<br/><br/>L’encodage en fichier MP4 à débit binaire unique, à la même résolution, prend environ 11 minutes.|[Fichier d’entrée : durée de 5 minutes 1280x720p à 29,97 images/seconde](https://wamspartners.blob.core.windows.net/for-long-term-share/Whistler_5min_720p30.mp4?sr=c&si=AzureDotComReadOnly&sig=OY0TZ%2BP2jLK7vmcQsCTAWl33GIVCu67I02pgarkCTNw%3D)<br/><br/>L’encodage avec la présélection « H264 – Vitesse de transmission unique – 720p » prend environ 5 minutes.<br/><br/>L’encodage avec la présélection « H264 – Vitesse de transmission multiple – 720p » prend environ 11,5 minutes.|[Fichier d’entrée : durée de 5 minutes 1920x1080p à 29,97 images/seconde](https://wamspartners.blob.core.windows.net/for-long-term-share/Whistler_5min_1080p30.mp4?sr=c&si=AzureDotComReadOnly&sig=OY0TZ%2BP2jLK7vmcQsCTAWl33GIVCu67I02pgarkCTNw%3D). <br/><br/>L’encodage avec la présélection « H264 à débit binaire unique 1080p » prend environ 2,7 minutes.<br/><br/>L’encodage avec la présélection « H264 à débit binaire multiple 1080p » prend environ 5,7 minutes.

##Considérations

>[AZURE.IMPORTANT] Passez en revue les considérations décrites dans cette section.

- Les unités réservées fonctionnent pour la mise en parallèle de tout le traitement multimédia, notamment les travaux à l'aide de l'Indexeur multimédia Azure. Toutefois, contrairement à l’encodage, l’indexation des travaux n’est pas plus rapide avec des unités réservées plus rapides.

- Si vous utilisez le pool partagé, c’est-à-dire sans aucune unité réservée, alors vos tâches d’encodage ont les mêmes performances que les unités de requête S1. Toutefois, il n’existe aucune limite supérieure au temps que vos tâches peuvent passer dans l’état En file d’attente et, à tout moment, une seule tâche maximum s’exécute.

- Les centres de données suivants ne proposent pas le type d’unité réservée **S2** : Sud du Brésil, Inde-Ouest, Inde-Centre et Sud de l’Inde.

- Les centres de données suivants ne proposent pas le type d’unité réservée **S3** : Sud du Brésil, Inde-Ouest et Inde-Centre.

- C’est le plus grand nombre d’unités spécifiées sur 24 heures qui est utilisé pour calculer le coût.


##Quotas et limitations

Pour plus d’informations sur les quotas et les limitations et pour savoir comment ouvrir un ticket de support, consultez la rubrique [Quotas et limitations](media-services-quotas-and-limitations.md).

##Étape suivante

Exécuter la tâche de traitement multimédia de mise à l’échelle avec l’une de ces technologies :

> [AZURE.SELECTOR]
- [.NET](media-services-dotnet-encoding-units.md)
- [Portail](media-services-portal-scale-media-processing.md)
- [REST](https://msdn.microsoft.com/library/azure/dn859236.aspx)
- [Java](https://github.com/southworkscom/azure-sdk-for-media-services-java-samples)
- [PHP](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)

##Parcours d’apprentissage de Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Fournir des commentaires

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

<!---HONumber=AcomDC_0907_2016-->