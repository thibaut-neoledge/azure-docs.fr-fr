<properties 
	pageTitle="Ajout d'unités d'encodage" 
	description="Découvrez comment ajouter des unités d'encodage avec .NET."  
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
	ms.date="02/15/2015" 
	ms.author="juliako"/>




# Mise à l'échelle de l'encodage avec le Kit de développement logiciel (SDK) .NET

Cet article fait partie de la série [workflow de vidéo à la demande Media Services](media-services-video-on-demand-workflow.md).
  
## Vue d'ensemble

Un compte Media Services est associé à un Type d'unité réservé qui détermine la vitesse à laquelle vos tâches d'encodage sont traitées. Vous pouvez choisir entre les types d'unités réservées suivantes : De base, Standard ou Premium. Par exemple, une même tâche d'encodage s'exécute plus rapidement avec le type d'unité réservée Standard qu'avec le type De base. Pour plus d'informations, consultez le blog " Encodage des types d'unité réservée " rédigé par [Milan Gada](http://azure.microsoft.com/blog/author/milanga/).

En plus de spécifier le type d'unité réservée, vous pouvez spécifier d'approvisionner votre compte avec des unités réservées d'encodage. Le nombre d'unités réservées d'encodage approvisionnées détermine le nombre de tâches de média qui peuvent être traitées simultanément dans un compte donné. Si, par exemple, votre compte a 5 unités réservées, les 5 tâches de média sont exécutées simultanément tant qu'il y a des tâches à traiter. Les tâches restantes attendent dans la file d'attente et sont récupérées séquentiellement pour être traitées aussitôt qu'une tâche en cours se termine. Si aucune unité réservée n'est approvisionnée pour un compte, les tâches sont récupérés séquentiellement. Dans ce cas, le temps d'attente entre la fin d'une tâche et le début de la suivante dépend de la disponibilité des ressources du système.

Pour modifier le type d'unité réservée et le nombre d'unités réservées d'encodage à l'aide du Kit de développement logiciel (SDK) .NET, procédez comme suit :

	IEncodingReservedUnit encodingBasicReservedUnit = _context.EncodingReservedUnits.FirstOrDefault();
	encodingBasicReservedUnit.ReservedUnitType = ReservedUnitType.Basic;
	encodingBasicReservedUnit.Update();
	Console.WriteLine("Reserved Unit Type: {0}", encodingBasicReservedUnit.ReservedUnitType);
	
	encodingBasicReservedUnit.CurrentReservedUnits = 2;
	encodingBasicReservedUnit.Update();
	
	Console.WriteLine("Number of reserved units: {0}", encodingBasicReservedUnit.CurrentReservedUnits);

## Ouverture d'un ticket de support

Par défaut, chaque compte Media Services a une capacité maximale de 25 unités réservées d'encodage et 5 unités réservées de diffusion en continu à la demande. Vous pouvez demander une limite supérieure en ouvrant un ticket de support.

Pour ouvrir un ticket de support, procédez comme suit : 

1. Connectez-vous à votre compte Azure sur le [portail de gestion](http://manage.windowsazure.com).
2. Accédez à [Support](http://www.windowsazure.com/support/contact/).
3. Cliquez sur " Obtenir un support ".
4. Sélectionnez votre abonnement.
5. Sous le type de support, sélectionnez " Technique ".
6. Cliquez sur " Créer un ticket ".
7. Sélectionnez " Azure Media Services " dans la liste de produits affichée sur la page suivante.
8. Sélectionnez " Media Processing " comme " Type de problème ", puis sélectionnez " Reservation Units " sous Catégorie.
9. Cliquez sur Continuer.
10. Suivez les instructions de la page suivante, puis entrez les détails concernant le nombre d'unités réservées de diffusion en continu à la demande ou d'encodage dont vous avez besoin.
11. Cliquez sur Envoyer pour ouvrir le ticket.


<!--HONumber=52-->