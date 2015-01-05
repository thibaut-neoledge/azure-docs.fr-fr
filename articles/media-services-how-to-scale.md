<properties urlDisplayName="How to scale" pageTitle="Mise à l'échelle d'un service Media Services | Documentation Azure" metaKeywords="" description="Learn how to scale Media Services by specifying the number of On-Demand Streaming Reserved Units and Encoding Reserved Units that you would like your account to be provisioned with." metaCanonical="" services="media-services" documentationCenter="" title="How to Scale a Media Service" authors="juliako" solutions="" manager="dwrede" editor="" />

<tags ms.service="media-services" ms.workload="media" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/30/2014" ms.author="juliako" />





#Mise à l'échelle d'un service de média  

[WACOM.INCLUDE [disclaimer](../includes/disclaimer.md)]


Vous pouvez mettre à l'échelle Media Services en spécifiant le nombre d' **Unités réservées de diffusion en continu à la demande** et d'**Unités réservées d'encodage** avec lesquelles vous voulez que votre compte soit approvisionné. 


<h2>Unités réservées de diffusion en continu à la demande</h2>

Les unités réservées de diffusion en continu à la demande fournissent à la fois une capacité de sortie dédiée que vous pouvez acquérir par incréments de 200 Mbits/s et une fonctionnalité supplémentaire qui inclut actuellement des [capacités d'empaquetage dynamique](http://go.microsoft.com/fwlink/?LinkId=276874). Par défaut, la diffusion en continu à la demande est configurée dans un modèle d'instance partagée, pour lequel les ressources du serveur (calcul, sortie, capacité, etc.) sont partagées avec tous les autres utilisateurs. Afin d'améliorer la vitesse de diffusion en continu à la demande, il est recommandé d'acheter des unités réservées de diffusion en continu à la demande. 

Pour changer le nombre d'unités réservées de diffusion en continu à la demande, procédez comme suit :

1. Dans le [portail de gestion](https://manage.windowsazure.com/), cliquez sur **Services de média**. Cliquez ensuite sur le nom du service multimédia.

2. Sélectionnez la page POINTS DE TERMINAISON DE DIFFUSION EN CONTINU. Cliquez ensuite sur le point de terminaison de diffusion en continu que vous souhaitez modifier.


3. Pour spécifier le nombre d'unités de diffusion en continu, sélectionnez l'onglet METTRE À L'ÉCHELLE et déplacez le curseur de **capacité réservée**.

	![Scale page](./media/media-services-how-to-scale/media-services-origin-scale.png)

4. Appuyez sur le bouton ENREGISTRER pour enregistrer vos modifications.

	Il faut environ 20 minutes pour allouer de nouvelles unités de diffusion en continu à la demande. 

	 
	>[Azure.Note] Actuellement, le fait de passer d'une valeur positive à zéro pour le nombre d'unités de diffusion en continu à la demande peut désactiver la diffusion en continu pendant une heure.

	>[Azure.Note] Le nombre d'unités le plus élevé spécifié pour la période de 24 heures est utilisé pour le calcul du coût. Pour des informations détaillées sur la tarification, consultez la page [Détails de la tarification Media Services](http://go.microsoft.com/fwlink/?LinkId=275107).

<h2>Unités réservées d'encodage</h2>

Le nombre d'unités réservées d'encodage approvisionnées est égal au nombre de tâches de média pouvant être traitées simultanément sur un compte donné. Si, par exemple, votre compte a 5 unités réservées, les 5 tâches de média sont exécutées simultanément tant qu'il y a des tâches à traiter. Les autres tâches restent dans la file d'attente et sont sélectionnées séquentiellement pour le traitement dès que l'exécution d'une tâche se termine. Si aucune unité réservée n'est approvisionnée pour un compte donné, les tâches sont sélectionnées séquentiellement. Dans ce cas, le temps d'attente entre l'achèvement d'une tâche et le démarrage de la suivante dépend de la disponibilité des ressources dans le système.

Pour changer le nombre d'unités réservées d'encodage, procédez comme suit :

1. Dans le [portail de gestion](https://manage.windowsazure.com/), cliquez sur **Services de média**. Cliquez ensuite sur le nom du service multimédia.

2. Sélectionnez la page ENCODAGE. 
	
	La page Encodage vous permet de sélectionner l'un des trois types d'unités réservées d'encodage :  De base, Standard ou Premium (comme illustré ci-après).

	![Processors page](./media/media-services-how-to-scale/media-services-encoding-scale.png)

	Vous pouvez changer le nombre d'unités réservées pour le TYPE D'UNITÉ RÉSERVÉE sélectionné à l'aide du curseur d'ENCODAGE.  
	
	La principale différence entre les types d'unité réservée concerne la vitesse. Par exemple, la même tâche d'encodage s'exécute plus vite avec le type d'unité réservée Standard qu'avec le type De base. Pour plus d'informations, consultez le blog " Encodage des types d'unité réservée " rédigé par [Milan Gada](http://azure.microsoft.com/blog/author/milanga/).

	>[Azure.Note] Les centres de données suivants ne proposent pas le type d'unité réservée Premium : Singapour, Hong Kong, Osaka, Pékin, Shanghai.

3. Appuyez sur le bouton ENREGISTRER pour enregistrer vos modifications.

	Les nouvelles unités réservées d'encodage sont allouées dès que vous appuyez sur ENREGISTRER.

	>[Azure.Note] Le nombre d'unités le plus élevé spécifié pour la période de 24 heures est utilisé pour le calcul du coût.

<h2>Ouverture d'un ticket de support</h2>


Par défaut, chaque compte Media Services a une capacité maximale de 25 unités réservées d'encodage et 5 unités réservées de diffusion en continu à la demande. Vous pouvez demander une limite supérieure en ouvrant un ticket de support.

Pour ouvrir un ticket de support, procédez comme suit : 

1. Connectez-vous à votre compte Azure sur le [portail de gestion](http://manage.windowsazure.com).
2. Accédez à [Support](http://www.windowsazure.com/fr-fr/support/contact/).
3. Cliquez sur " Obtenir un support ".
4. Sélectionnez votre abonnement.
5. Sous le type de support, sélectionnez " Technique ".
6. Cliquez sur " Créer un ticket ".
7. Sélectionnez " Azure Media Services " dans la liste de produits affichée sur la page suivante.
8. Sélectionnez " Media Processing " comme " Type de problème ", puis sélectionnez " Reservation Units " sous Catégorie.
9. Cliquez sur Continuer.
10. Suivez les instructions de la page suivante, puis entrez les détails concernant le nombre d'unités réservées de diffusion en continu à la demande ou d'encodage dont vous avez besoin.
11. Cliquez sur Envoyer pour ouvrir le ticket.





 





<!--HONumber=35.1-->
