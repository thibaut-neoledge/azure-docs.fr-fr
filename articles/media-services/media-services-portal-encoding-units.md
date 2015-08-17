<properties 
	pageTitle="Comment mettre à l’échelle des unités réservées d’encodage" 
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
	ms.date="07/16/2015" 
	ms.author="juliako"/>


#Comment mettre à l’échelle l’encodage

Cet article fait partie de la série [workflow de vidéo à la demande Media Services](media-services-video-on-demand-workflow.md).

##Vue d’ensemble

Un compte Media Services est associé à un Type d’unité réservé qui détermine la vitesse à laquelle vos tâches d’encodage sont traitées. Vous avez le choix entre les types d’unités réservées suivantes : **De base**, **Standard** ou **Premium**. Par exemple, une même tâche d’encodage s’exécute plus rapidement quand vous utilisez le type d’unité réservée **Standard** que le type **De base**. Pour plus d’informations, consultez [Types d’unités réservées d’encodage](http://azure.microsoft.com/blog/author/milanga).

En plus de spécifier le type d’unité réservée, vous pouvez spécifier d’approvisionner votre compte avec des unités réservées d’encodage. Le nombre d’unités réservées d’encodage approvisionnées détermine le nombre de tâches de média qui peuvent être traitées simultanément dans un compte donné. Si, par exemple, votre compte a 5 unités réservées, les 5 tâches de média sont exécutées simultanément tant qu’il y a des tâches à traiter. Les autres tâches restent dans la file d'attente et sont sélectionnées séquentiellement pour le traitement dès que l'exécution d'une tâche se termine. Si aucune unité réservée n'est approvisionnée pour un compte donné, les tâches sont sélectionnées séquentiellement. Dans ce cas, le temps d’attente entre la fin d’une tâche et le début de la suivante dépend de la disponibilité des ressources du système.

Pour modifier le type d’unité réservée et le nombre d’unités réservées d’encodage, procédez comme suit :

1. Dans le [portail de gestion](https://manage.windowsazure.com/), cliquez sur **Media Services**. Cliquez ensuite sur le nom du service multimédia.

2. Sélectionnez la page **ENCODAGE**.

	Pour modifier le **TYPE D’UNITÉ RÉSERVÉE**, appuyez sur DE BASE, STANDARD ou PREMIUM.

	Pour modifier le nombre d’unités réservées pour le type d’unité réservée sélectionné, utilisez le curseur **ENCODAGE**.
	
	
	![Processors page](./media/media-services-portal-encoding-units/media-services-encoding-scale.png)

	  
	>[AZURE.NOTE]Les centres de données suivants ne proposent pas le type d’unité réservée Premium : Singapour, Hong Kong, Osaka, Beijing, Shanghai.

3. Appuyez sur le bouton ENREGISTRER pour enregistrer vos modifications.

	Les nouvelles unités réservées d’encodage sont allouées dès que vous cliquez sur ENREGISTRER.

	>[AZURE.NOTE]C’est le plus grand nombre d’unités spécifiées sur 24 heures qui est utilisé pour calculer le coût.

##Quotas et limitations

Pour plus d’informations sur les quotas et les limitations et pour savoir comment ouvrir un ticket de support, consultez la rubrique [Quotas et limitations](media-services-quotas-and-limitations.md).




 

<!---HONumber=August15_HO6-->