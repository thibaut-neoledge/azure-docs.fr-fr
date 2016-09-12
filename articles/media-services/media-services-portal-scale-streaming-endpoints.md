<properties
	pageTitle=" Mettre à l’échelle des points de terminaison de streaming avec le Portail Azure | Microsoft Azure"
	description="Ce didacticiel vous guide à travers les étapes de mise à l’échelle des points de terminaison de streaming avec le Portail Azure."
	services="media-services"
	documentationCenter=""
	authors="Juliako"
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


# Mettre à l’échelle des points de terminaison de streaming avec le Portail Azure

##Vue d'ensemble

> [AZURE.NOTE] Pour suivre ce didacticiel, vous avez besoin d'un compte Azure. Pour plus d'informations, consultez la page [Version d'évaluation gratuite d'Azure](https://azure.microsoft.com/pricing/free-trial/).

Lorsque vous utilisez Azure Media Services, la diffusion à vos clients de vidéos en continu à débit binaire adaptatif constitue l’un des scénarios les plus courants. Media Services prend en charge les technologies de diffusion en continu à débit binaire adaptatif suivantes : HTTP Live Streaming (HLS), Smooth Streaming, MPEG DASH et HDS (pour licences Adobe PrimeTime/Access uniquement).

Media Services fournit l’empaquetage dynamique qui permet de distribuer un contenu encodé en MP4 à débit binaire adaptatif dans un format pris en charge par Media Services (MPEG DASH, HLS, Smooth Streaming, HDS) sans avoir à stocker de versions pré-empaquetées de chacun de ces formats de diffusion en continu.

Pour tirer parti de l’empaquetage dynamique, vous devez effectuer les opérations suivantes :

- Encoder votre fichier mezzanine (source) dans un ensemble de fichiers MP4 à débit adaptatif (les étapes de codage sont décrites plus loin dans ce didacticiel).
- Créez au moins une unité de diffusion pour le *point de terminaison de diffusion en continu* à partir duquel vous envisagez de distribuer votre contenu. La procédure ci-dessous explique comment modifier le nombre d’unités de diffusion en continu.

Avec l’empaquetage dynamique, vous devez stocker et payer les fichiers dans un seul format de stockage. Ensuite, Media Services crée et fournit la réponse appropriée en fonction des demandes des clients.

En outre, vous pouvez contrôler la capacité du service de point de terminaison de streaming afin de gérer les besoins croissants en matière de bande passante en ajustant les unités de diffusion en continu. Il est recommandé d’allouer une ou plusieurs unités d’échelle pour les applications dans un environnement de production. Les unités de diffusion en continu fournissent à la fois une capacité de sortie dédiée que vous pouvez acquérir par incréments de 200 Mbit/s et des fonctionnalités supplémentaires incluant : [l’empaquetage dynamique](media-services-dynamic-packaging-overview.md), l’intégration au CDN et la configuration avancée. Pour plus d’informations, consultez [Gérer les points de terminaison de streaming avec le Portail Azure](media-services-portal-manage-streaming-endpoints.md).

## Mettre à l’échelle les points de terminaison de streaming

Pour créer et modifier le nombre d’unités réservées de diffusion en continu, procédez comme suit :

1. Connectez-vous au [portail Azure](https://portal.azure.com/).
2. Dans la fenêtre **Paramètres**, sélectionnez **Points de terminaison de streaming**.
3. Cliquez sur le point de terminaison de streaming que vous souhaitez mettre à l’échelle.
4. Déplacez le curseur pour spécifier le nombre d’unités de diffusion en continu
 
![Point de terminaison de diffusion en continu](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints3.png)

Les considérations suivantes s'appliquent :

- L’allocation de nouvelles unités de diffusion en continu peut prendre environ 20 minutes.
- Actuellement, le fait de passer d’une valeur positive à zéro pour le nombre d’unités de diffusion en continu peut désactiver la diffusion en continu pendant une heure.
- C’est le plus grand nombre d’unités spécifiées sur 24 heures qui est utilisé pour calculer le coût. Pour des informations détaillées sur la tarification, consultez la page [Détails de la tarification des services de média](http://go.microsoft.com/fwlink/?LinkId=275107).

##Étapes suivantes

Consultez les parcours d’apprentissage de Media Services.

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Fournir des commentaires

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

<!---HONumber=AcomDC_0831_2016-->