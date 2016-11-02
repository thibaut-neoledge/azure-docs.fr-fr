<properties 
	pageTitle="Ajout d’unités d’encodage" 
	description="Découvrez comment ajouter des unités d’encodage avec .NET"  
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
 	ms.date="09/01/2016"
	ms.author="juliako;milangada;gtrifonov"/>


#Mise à l’échelle de l’encodage avec le Kit de développement logiciel (SDK) .NET

> [AZURE.SELECTOR]
- [Portail](media-services-portal-scale-media-processing.md)
- [.NET](media-services-dotnet-encoding-units.md)
- [REST](https://msdn.microsoft.com/library/azure/dn859236.aspx)
- [Java](https://github.com/southworkscom/azure-sdk-for-media-services-java-samples)
- [PHP](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)

##Vue d'ensemble

>[AZURE.IMPORTANT] Pour obtenir plus d’informations sur la mise à l’échelle du traitement multimédia, consultez la rubrique de [présentation](media-services-scale-media-processing-overview.md).
 
Pour modifier le type d’unité réservée et le nombre d’unités réservées d’encodage à l’aide du Kit de développement logiciel (SDK) .NET, procédez comme suit :

	IEncodingReservedUnit encodingS1ReservedUnit = _context.EncodingReservedUnits.FirstOrDefault();
	encodingS1ReservedUnit.ReservedUnitType = ReservedUnitType.Basic; // Corresponds to S1
	encodingS1ReservedUnit.Update();
	Console.WriteLine("Reserved Unit Type: {0}", encodingS1ReservedUnit.ReservedUnitType);
	
	encodingS1ReservedUnit.CurrentReservedUnits = 2;
	encodingS1ReservedUnit.Update();
	
	Console.WriteLine("Number of reserved units: {0}", encodingS1ReservedUnit.CurrentReservedUnits);

##Ouverture d'un ticket de support

Par défaut, chaque compte Media Services a une capacité maximale de 25 unités réservées d'encodage et 5 unités réservées de diffusion en continu à la demande. Vous pouvez demander une limite supérieure en ouvrant un ticket de support.

###Ouverture d’un ticket de support

Pour ouvrir un ticket de support, procédez comme suit :

1. Cliquez sur [Obtenir un support](https://manage.windowsazure.com/?getsupport=true). Si vous n'êtes pas connecté, vous devrez entrer vos informations d'identification.

1. Sélectionnez votre abonnement.

1. Sous le type de support, sélectionnez « Technique ».

1. Cliquez sur « Créer un ticket ».

1. Sélectionnez « Azure Media Services » dans la liste de produits affichée sur la page suivante.

1. Sélectionnez un « type de problème » approprié pour votre problème.

1. Cliquez sur Continuer.

1. Suivez les instructions de la page suivante, puis entrez les détails relatifs à votre problème.

1. Cliquez sur Envoyer pour ouvrir le ticket.



##Parcours d’apprentissage de Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Fournir des commentaires

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

<!---HONumber=AcomDC_0907_2016-->