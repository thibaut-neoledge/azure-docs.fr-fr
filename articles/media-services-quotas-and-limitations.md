<properties pageTitle="Quotas et limitations de Media Services" description="Cette rubrique décrit les quotas et les limitations associées à Microsoft Azure Media Services." services="media-services" documentationCenter="" authors="Juliako" manager="dwrede" />

<tags ms.service="media-services" ms.devlang="" ms.topic="article" ms.tgt_pltfrm="" ms.workload="media" ms.date="01/23/2015" ms.author="juliako" />

#Quotas et limitations

Cette rubrique décrit les quotas et les limitations associées à Microsoft Azure Media Services.

## Quotas et limitations

- Nombre maximal d'éléments multimédias autorisés dans votre compte Media Services : 1 000 000. 

- Nombre maximal de tâches chaînées par travail : 30.

- Nombre maximal de ressources autorisées dans une tâche : 50 ressources par tâche.
 
- Nombre maximal de ressources par travail : 100.
 
- Ce nombre comprend les travaux en file d'attente, terminés, actifs et annulés. Il n'inclut pas les travaux supprimés.
 
- Le nombre maximal de travaux dans votre compte ne doit pas dépasser 50 000.
 
- Vous pouvez supprimer les anciens travaux à l'aide de **IJob.Delete** ou de la requête HTTP **DELETE**. Pour plus d'informations, consultez [Limite des enregistrements de tâche pour Azure Media Encoder](http://blogs.msdn.com/b/randomnumber/archive/2014/05/05/job-record-limit-for-windows-azure-media-encoder.aspx) et [Gestion de ressources](https://msdn.microsoft.com/fr-fr/library/azure/dn642436.aspx). 
 
- Lors d'une requête visant à répertorier les entités de travail, un maximum de 1 000 est retourné par requête. Si vous souhaitez effectuer le suivi de l'ensemble des travaux soumis, vous pouvez utiliser top/skip comme décrit dans [Options de requête du système OData](http://msdn.microsoft.com/library/gg309461.aspx).  


- Vous ne pouvez avoir plus de cinq localisateurs uniques associés à une ressource donnée. 
	
	**Remarque**
	Les localisateurs ne sont pas conçus pour gérer le contrôle d'accès par utilisateur. Pour accorder différents droits d'accès aux utilisateurs, utilisez les solutions de gestion des droits numériques (DRM).

- Vous ne pouvez pas avoir plus de 25 comptes Media Services dans un seul abonnement.

- Le mécanisme de limitation de Media Services restreint l'utilisation des ressources pour les applications qui recourent de manière excessive au service. Le service peut renvoyer le code d'état HTTP Service indisponible (503). Pour plus d'informations, reportez-vous à la description de l'erreur 503 dans la rubrique [Codes d'erreur d'Azure Media Services](http://msdn.microsoft.com/library/azure/dn168949.aspx).

- Par défaut, vous pouvez ajouter jusqu'à 5 canaux dynamiques à votre compte Media Services. 

	Vous pouvez également démarrer jusqu'à 5 canaux en même temps. Pour demander une limite plus élevée, consultez la section *How to request a higher limit for updatable quotas* ci-après.

- Par défaut, vous pouvez ajouter jusqu'à 50 programmes (en état arrêté) à un seul canal. 

	Seuls trois programmes peuvent être en cours d'exécution en même temps. Pour demander une limite plus élevée, consultez la section *How to request a higher limit for updatable quotas* ci-après.

- Par défaut, chaque compte Media Services peut avoir jusqu'à 2 points de terminaison de diffusion en continu (origines). 

	Seuls deux points de terminaison de diffusion en continu peuvent être en cours d'exécution en même temps.

	Chaque point de terminaison de diffusion en continu peut être mis à l'échelle jusqu'à 10 unités de diffusion en continu. Pour demander une limite plus élevée, consultez la section *How to request a higher limit for updatable quotas* ci-après.


- Par défaut, chaque compte Media Services a une capacité maximale de 25 unités d'encodage. Pour plus d'informations, consultez la page Mise à l'échelle de Media Services. Pour demander une limite plus élevée, consultez Demande d'une limite plus élevée pour les quotas de mise à jour.
	
	**Important**
	Ne créez pas d'autres comptes Media Services pour augmenter les limites, mais envoyez un ticket de support.

##Demande d'une limite plus élevée pour les quotas pouvant être mis à jour

###Quotas pouvant être mis à jour

Vous pouvez demander la mise à jour des quotas suivants en ouvrant un ticket de support.
- Unités d'encodage

- Canaux en direct (état arrêté et en cours d'exécution)
 
- Points de terminaison de diffusion en continu (état arrêté et en cours d'exécution)
 
- Unités de diffusion en continu

###Ouverture d'un ticket de support

Pour ouvrir un ticket de support, procédez comme suit :

1. Cliquez sur [Obtenir un support](https://manage.windowsazure.com/?getsupport=true). Si vous n'êtes pas connecté, vous devrez entrer vos informations d'identification.

1. Sélectionnez votre abonnement.
 
1. Sous le type de support, sélectionnez " Technique ".
 
1. Cliquez sur " Créer un ticket ". 
 
1. Sélectionnez " Azure Media Services " dans la liste de produits affichée sur la page suivante.
 
1. Sélectionnez un " type de problème " approprié pour votre problème.
 
1. Cliquez sur Continuer.
 
1. Suivez les instructions de la page suivante, puis entrez les détails concernant le nombre d'unités réservées de diffusion en continu ou d'encodage dont vous avez besoin. 
 
1. Cliquez sur Envoyer pour ouvrir le ticket.
 
<!--HONumber=45--> 
