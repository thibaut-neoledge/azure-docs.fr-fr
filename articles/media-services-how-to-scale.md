<properties 
	pageTitle="Mise à l'échelle d'un service de média" 
	description="Apprenez à mettre à l'échelle Media Services en spécifiant le nombre d'unités réservées de diffusion en continu à la demande et d'unités réservées d'encodage avec lesquelles vous voulez que votre compte soit approvisionné." 
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


#Mise à l'échelle d'un service de média  

Cet article fait partie de la série [workflow à la demande de vidéo Media Services](../media-services-video-on-demand-workflow).

##Vue d'ensemble

Vous pouvez mettre à l'échelle des services de média en spécifiant le nombre d'**Unités réservées de diffusion en continu** et d'**Unités réservées d'encodage** avec lesquelles vous voulez que votre compte soit approvisionné. 

##Unités réservées de diffusion en continu

Pour plus d'informations, consultez [Mise à l'échelle des unités de diffusion continu](../media-services-manage-origins#scale_streaming_endpoints).

##<a id="encoding_reserved_units"></a>Unités réservées d'encodage

Un compte Media Services est associé à un Type d'unité réservé qui détermine la vitesse à laquelle vos tâches d'encodage sont traitées. Vous pouvez choisir entre les types d'unités réservées suivantes : De base, Standard ou Premium. Par exemple, la même tâche d'encodage s'exécute plus vite avec le type d'unité réservée Standard qu'avec le type De base. Pour plus d'informations, consultez le blog " Encodage des types d'unité réservée " rédigé par [Milan Gada](http://azure.microsoft.com/blog/author/milanga).

En plus de spécifier le type d'unité réservée, vous pouvez spécifier d'approvisionner votre compte avec des unités réservées d'encodage. Le nombre d'unités réservées d'encodage approvisionnées détermine le nombre de tâches de média pouvant être traitées simultanément sur un compte donné. Si, par exemple, votre compte a 5 unités réservées, les 5 tâches de média sont exécutées simultanément tant qu'il y a des tâches à traiter. Les autres tâches restent dans la file d'attente et sont sélectionnées séquentiellement pour le traitement dès que l'exécution d'une tâche se termine. Si aucune unité réservée n'est approvisionnée pour un compte donné, les tâches sont sélectionnées séquentiellement. Dans ce cas, le temps d'attente entre l'achèvement d'une tâche et le démarrage de la suivante dépend de la disponibilité des ressources dans le système.

Pour modifier le type d'unité réservée et le nombre d'unités réservées d'encodage, procédez comme suit :

1. Dans le [portail de gestion](https://manage.windowsazure.com/), cliquez sur **Media Services**. Cliquez ensuite sur le nom du service multimédia.

2. Sélectionnez la page **ENCODAGE**. 

	Pour modifier le **TYPE D'UNITÉ RÉSERVÉE**, appuyez sur DE BASE, STANDARD ou PREMIUM. 

	Pour modifier le nombre d'unités réservées pour le type d'unité réservée sélectionné, utilisez le curseur d'**ENCODAGE**. 
	
	
	![Processors page](./media/media-services-how-to-scale/media-services-encoding-scale.png)

	  
	>[Azure.Note] Les centres de données suivants ne proposent pas le type d'unité réservée Premium : Singapour, Hong Kong, Osaka, Pékin, Shanghai.

3. Appuyez sur le bouton ENREGISTRER pour enregistrer vos modifications.

	Les nouvelles unités réservées d'encodage sont allouées dès que vous appuyez sur ENREGISTRER.

	>[Azure.Note] Le nombre d'unités le plus élevé spécifié pour la période de 24 heures est utilisé pour le calcul du coût.

##Ouverture d'un ticket de support

Par défaut, chaque compte Media Services a une capacité maximale de 25 unités réservées d'encodage et 5 unités réservées de diffusion en continu à la demande. Vous pouvez demander une limite supérieure en ouvrant un ticket de support.

Pour ouvrir un ticket de support, procédez comme suit : 

1. Connectez-vous à votre compte Azure sur le [portail de gestion](http://manage.windowsazure.com).
2. Accédez à [Support](http://azure.microsoft.com/support/contact/).
3. Cliquez sur " Obtenir un support ".
4. Sélectionnez votre abonnement.
5. Sous le type de support, sélectionnez " Technique ".
6. Cliquez sur " Créer un ticket ".
7. Sélectionnez " Azure Media Services " dans la liste de produits affichée sur la page suivante.
8. Sélectionnez " Media Processing " comme " Type de problème ", puis sélectionnez " Reservation Units " sous Catégorie.
9. Cliquez sur Continuer.
10. Suivez les instructions de la page suivante, puis entrez les détails concernant le nombre d'unités réservées de diffusion en continu à la demande ou d'encodage dont vous avez besoin.
11. Cliquez sur Envoyer pour ouvrir le ticket.





<!--HONumber=45--> 
