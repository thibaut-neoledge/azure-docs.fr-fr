<properties 
	pageTitle="Mise à l’échelle d’un service de média" 
	description="Apprenez à mettre à l’échelle Media Services en spécifiant le nombre d’unités réservées de diffusion en continu à la demande et d’unités réservées d’encodage que vous voulez attribuer à votre compte." 
	services="media-services" 
	documentationCenter="" 
	authors="juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/24/2015" 
	ms.author="juliako"/>


#Mise à l’échelle d’un service de média  

##Vue d’ensemble

Vous pouvez mettre à l’échelle **Media Services** en spécifiant le nombre d’**unités réservées de diffusion en continu** et d’**unités réservées d’encodage** que vous voulez attribuer à votre compte.

Vous pouvez aussi mettre à l’échelle votre compte Media Services en lui ajoutant des comptes de stockage. Chaque compte de stockage est limité à 500 To. Pour développer votre capacité stockage au-delà des limites par défaut, vous pouvez choisir de rattacher plusieurs comptes de stockage à un même compte Media Services.

Cette rubrique offre des liens vers des rubriques connexes.

##<a id="streaming_endpoins"></a>Unités réservées de diffusion en continu

Pour plus d’informations, consultez la rubrique [Mise à l’échelle des unités de diffusion en continu](media-services-manage-origins.md#scale_streaming_endpoints).

##<a id="encoding_reserved_units"></a>Unités réservées d’encodage

Pour plus d’informations sur la mise à l’échelle des unités d’encodage, consultez les rubriques **Portail** et **.NET** suivantes.

[AZURE.INCLUDE [media-services-selector-scale-encoding-units](../../includes/media-services-selector-scale-encoding-units.md)]

Notez que les unités réservées sont les mêmes pour les tâches d’encodage et d’indexation.

##<a id="storage"></a>Mise à l’échelle du stockage

Pour plus d’informations, consultez les pages [Gestion des éléments multimédias Media Services sur plusieurs comptes de stockage](https://msdn.microsoft.com/library/azure/dn271889.aspx) et [Utilisation du stockage Azure Storage](https://msdn.microsoft.com/library/azure/dn767951.aspx).



 

<!---HONumber=August15_HO6-->