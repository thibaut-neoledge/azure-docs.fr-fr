<properties 
	pageTitle="Ajout d’unités d’encodage" 
	description="Découvrez comment ajouter des unités d’encodage avec .NET"  
	services="media-services" 
	documentationCenter="" 
	authors="juliako,milangada,gtrifonov" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/15/2015"
	ms.author="juliako"/>


#Mise à l’échelle de l’encodage avec le Kit de développement logiciel (SDK) .NET


> [AZURE.SELECTOR]
- [Portal](media-services-portal-encoding-units.md)
- [.NET](media-services-dotnet-encoding-units.md)
- [REST](https://msdn.microsoft.com/library/azure/dn859236.aspx)

##Vue d'ensemble

Un compte Media Services est associé à un Type d’unité réservé qui détermine la vitesse à laquelle vos tâches d’encodage sont traitées. Vous avez le choix entre les types d’unités réservées suivantes : De base, Standard ou Premium. Par exemple, une même tâche d’encodage s’exécute plus rapidement quand vous utilisez le type d’unité réservée Standard que le type De base. Pour plus d’informations, consultez le blog « Encodage des types d’unité réservée » rédigé par [Milan Gada](http://azure.microsoft.com/blog/author/milanga/).

En plus de spécifier le type d’unité réservée, vous pouvez spécifier d’approvisionner votre compte avec des unités réservées d’encodage. Le nombre d’unités réservées d’encodage approvisionnées détermine le nombre de tâches de média qui peuvent être traitées simultanément dans un compte donné. Si, par exemple, votre compte a 5 unités réservées, les 5 tâches de média sont exécutées simultanément tant qu’il y a des tâches à traiter. Les autres tâches restent dans la file d'attente et sont sélectionnées séquentiellement pour le traitement dès que l'exécution d'une tâche se termine. Si aucune unité réservée n'est approvisionnée pour un compte donné, les tâches sont sélectionnées séquentiellement. Dans ce cas, le temps d’attente entre la fin d’une tâche et le début de la suivante dépend de la disponibilité des ressources du système.

Pour modifier le type d’unité réservée et le nombre d’unités réservées d’encodage à l’aide du Kit de développement logiciel (SDK) .NET, procédez comme suit :

	IEncodingReservedUnit encodingBasicReservedUnit = _context.EncodingReservedUnits.FirstOrDefault();
	encodingBasicReservedUnit.ReservedUnitType = ReservedUnitType.Basic;
	encodingBasicReservedUnit.Update();
	Console.WriteLine("Reserved Unit Type: {0}", encodingBasicReservedUnit.ReservedUnitType);
	
	encodingBasicReservedUnit.CurrentReservedUnits = 2;
	encodingBasicReservedUnit.Update();
	
	Console.WriteLine("Number of reserved units: {0}", encodingBasicReservedUnit.CurrentReservedUnits);

##Ouverture d’un ticket de support

Par défaut, chaque compte Media Services a une capacité maximale de 25 unités réservées d'encodage et 5 unités réservées de diffusion en continu à la demande. Vous pouvez demander une limite supérieure en ouvrant un ticket de support.

###Ouverture d’un ticket de support

Pour ouvrir un ticket de support, procédez comme suit :

1. Cliquez sur [Obtenir un support](https://manage.windowsazure.com/?getsupport=true). Si vous n’êtes pas connecté, vous devrez entrer vos informations d’identification.

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

<!---HONumber=Nov15_HO3-->