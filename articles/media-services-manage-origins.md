<properties 
	pageTitle="Gestion des points de terminaison de diffusion en continu dans un compte Media Services" 
	description="Cette rubrique montre comment gérer les points de terminaison de diffusion en continu à l'aide du portail de gestion Azure." 
	services="media-services" 
	documentationCenter="" 
	authors="juliako" 
	writer="juliako" 
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


#<a id="managemediaservicesorigins"></a>Gestion des points de terminaison de diffusion en continu dans un compte Media Services

Cet article fait partie des séries [workflow à la demande de vidéo Media Services](../media-services-video-on-demand-workflow) et [workflow de vidéo en flux continu Media Services](../media-services-live-streaming-workflow).  


Dans Media Services, un point de terminaison de diffusion en continu représente un service de diffusion en continu qui peut fournir du contenu directement à une application de lecteur cliente ou à un réseau de diffusion de contenu (CDN) pour être redistribué. Actuellement, Microsoft Azure Media Services n'offre pas d'intégration CDN fluide, mais vous pouvez utiliser l'un des fournisseurs de CDN sur le marché (Azure CDN ou Akamai). Le flux sortant du service de point de terminaison de diffusion en continu peut être un flux dynamique ou une ressource de vidéo à la demande dans votre compte Media Services. 

En outre, vous pouvez contrôler la capacité du service de point de terminaison de diffusion en continu afin de gérer les besoins croissants en matière de bande passante en ajustant les unités d'échelle (également appelées unités de diffusion). Il est recommandé d'allouer une ou plusieurs unités d'échelle pour les applications dans un environnement de production. Les unités d'échelle fournissent à la fois une capacité de sortie dédiée que vous pouvez acquérir par incréments de 200 Mbits/s et une fonctionnalité supplémentaire qui inclut actuellement l'utilisation de l'empaquetage dynamique. 

Cette rubrique montre comment gérer les points de terminaison de diffusion en continu à l'aide du portail de gestion Azure.


##Ajout et suppression de points de terminaison de diffusion en continu 

1. Dans le [portail de gestion](https://manage.windowsazure.com/), cliquez sur **Media Services**. Cliquez ensuite sur le nom du service multimédia.
2. Sélectionnez la page **POINTS DE TERMINAISON DE DIFFUSION EN CONTINU**. 
3. Cliquez sur le bouton ADD ou DELETE en bas de la page. Attention : il n'est pas possible de supprimer le point de terminaison par défaut. 
4. Cliquez sur le bouton DÉMARRER pour démarrer le point de terminaison. 
5. Cliquez sur le nom du point de terminaison pour le configurer.   

	![Origin page][origin-page]

##<a id="scale_streaming_endpoints"></a>Mise à l'échelle du point de terminaison

Les unités de diffusion en continu fournissent à la fois une capacité de sortie dédiée que vous pouvez acquérir par incréments de 200 Mbps et une fonctionnalité supplémentaire qui inclut actuellement des [capacités d'empaquetage dynamique](http://go.microsoft.com/fwlink/?LinkId=276874). Par défaut, la diffusion en continu est configurée dans un modèle d'instance partagée, pour lequel les ressources du serveur (calcul, sortie, capacité, etc.) sont partagées avec tous les autres utilisateurs. Afin d'améliorer la vitesse de diffusion en continu=, il est recommandé d'acheter des unités de diffusion en continu. 

Pour changer le nombre d'unités de diffusion en continu, procédez comme suit :

1. Pour spécifier le nombre d'unités de diffusion en continu, sélectionnez l'onglet METTRE À L'ÉCHELLE et déplacez le curseur de **capacité réservée**.

	![Scale page](./media/media-services-how-to-scale/media-services-origin-scale.png)

4. Appuyez sur le bouton ENREGISTRER pour enregistrer vos modifications.

	l'allocation de nouvelles unités de diffusion en continu prend environ 20 minutes. 

	 
	>[AZURE.NOTE] Actuellement, le fait de passer d'une valeur positive à zéro pour le nombre d'unités de diffusion en continu peut désactiver la diffusion en continu pendant une heure.

	>[AZURE.NOTE] Le nombre d'unités le plus élevé spécifié pour la période de 24 heures est utilisé pour le calcul du coût. Pour des informations détaillées sur la tarification, consultez la page [Détails de la tarification de Media Services](http://go.microsoft.com/fwlink/?LinkId=275107).
	
##Configuration du point de terminaison

L'onglet CONFIGURER permet d'appliquer les configurations comme indiqué dans cette image. La description des champs suit.

>[AZURE.NOTE] La configuration sur cette page s'appliquera uniquement aux points de terminaison de diffusion en continu qui ont au moins une unité réservée. Réservation des unités de diffusion en continu à la demande réservées.

![Configure origin][configure-origin]
  

1. Définir le délai maximal de mise en cache. Cette valeur est spécifiée dans l'en-tête de contrôle de cache des réponses HTTP. Elle n'est pas prioritaire sur la valeur de cache maximale définie explicitement dans le contenu de l'objet blob.

2. Définir les adresses IP autorisées à se connecter au point de terminaison publié pour la diffusion en continu. Si aucune adresse IP n'est spécifiée, toutes les adresses IP peuvent se connecter.

3. Définissez la configuration pour l'authentification de l'en-tête de signature Akamai.

4. Vous pouvez spécifier une stratégie d'accès entre domaines pour les clients Adobe Flash (pour plus d'informations, consultez la page [Spécification de fichier de stratégie interdomaines](http://www.adobe.com/devnet/articles/crossdomain_policy_file_spec.html). En plus de la stratégie d'accès client pour les clients Microsoft Silverlight (pour plus d'informations, consultez la page [Mise à disposition d'un service sans limites de domaine](https://msdn.microsoft.com/fr-fr/library/cc197955(v=vs.95).aspx).  

5. Vous pouvez également configurer les noms d'hôte personnalisé en cliquant sur le bouton **Configurer**. Pour plus d'informations, reportez-vous à la propriété **CustomHostNames** dans la rubrique [StreamingEndpoint](https://msdn.microsoft.com/fr-fr/library/dn783468.aspx).  



[origin-page]: ./media/media-services-manage-origins/media-services-origins-page.png
[configure-origin]: ./media/media-services-manage-origins/media-services-origins-configure.png
[configure-origin-configure-custom-host-names]: ./media/media-services-manage-origins/media-services-configure-custom-host-names.png

<!--HONumber=45--> 
