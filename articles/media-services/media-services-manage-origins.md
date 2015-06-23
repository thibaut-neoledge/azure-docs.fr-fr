<properties 
	pageTitle="Gestion des points de terminaison de diffusion en continu dans un compte Media Services" 
	description="Cette rubrique montre comment gérer les points de terminaison de diffusion en continu à l'aide du portail de gestion Azure." 
	services="media-services" 
	documentationCenter="" 
	authors="Juliako" 
	writer="juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/10/2015" 
	ms.author="juliako"/>


# <a id="managemediaservicesorigins"></a>Gestion des points de terminaison de diffusion en continu dans un compte Media Services

Cet article fait partie des séries [workflow à la demande de vidéo Media Services](media-services-video-on-demand-workflow.md) et [workflow de vidéo en flux continu Media Services](media-services-live-streaming-workflow.md).  


Dans Microsoft Azure Media Services, un **point de terminaison de diffusion en continu** représente un service de diffusion en continu qui peut fournir du contenu directement à une application de lecteur cliente ou à un réseau de diffusion de contenu (CDN) pour être redistribué. Media Services fournit également une intégration transparente au CDN Azure. Le flux sortant d'un service StreamingEndpoint peut être un flux dynamique ou un élément multimédia de vidéo à la demande dans votre compte Media Services.  

En outre, vous pouvez contrôler la capacité du service de point de terminaison de diffusion en continu afin de gérer les besoins croissants en matière de bande passante en ajustant les unités d'échelle (également appelées unités de diffusion). Il est recommandé d'allouer une ou plusieurs unités d'échelle pour les applications dans un environnement de production. Les unités d'échelle fournissent à la fois une capacité de sortie dédiée que vous pouvez acquérir par incréments de 200 Mbit/s et des fonctionnalités supplémentaires incluant [l'empaquetage dynamique](https://msdn.microsoft.com/library/azure/jj889436.aspx), l'intégration aux CDN et une configuration avancée.

Notez que vous êtes facturé uniquement lorsque votre StreamingEndpoint est en cours d'exécution. 

Cette rubrique donne une vue d'ensemble des fonctionnalités principales offertes par les points de terminaison de diffusion en continu. Elle explique également comment utiliser le portail de gestion Azure pour gérer les points de terminaison de diffusion en continu.


## Ajout et suppression de points de terminaison de diffusion en continu 

Vous pouvez ajouter ou supprimer des points de terminaison de diffusion en continu à l'aide du Kit de développement .NET, de l'API REST ou du portail de gestion Azure.

Pour ajouter/supprimer un point de terminaison de diffusion en continu à l'aide du portail, procédez comme suit :

1. Dans le [portail de gestion](https://manage.windowsazure.com/), cliquez sur **Media Services**. Cliquez ensuite sur le nom du service multimédia.
2. Sélectionnez la page **POINTS DE TERMINAISON DE DIFFUSION EN CONTINU**. 
3. Cliquez sur le bouton ADD ou DELETE en bas de la page. Attention : il n'est pas possible de supprimer le point de terminaison par défaut. 
4. Cliquez sur le bouton DÉMARRER pour démarrer le point de terminaison. 
5. Cliquez sur le nom du point de terminaison pour le configurer.   

	![Streaming Endpoint page][streaming-endpoint]


Par défaut, vous bénéficiez au maximum de deux points de terminaison de diffusion en continu. Si vous avez besoin de points de terminaisons supplémentaires, consultez la rubrique [Quotas et limitations](media-services-quotas-and-limitations/).

## <a id="scale_streaming_endpoints"></a>Mise à l'échelle du point de terminaison

Les unités de diffusion en continu fournissent à la fois une capacité de sortie dédiée que vous pouvez acquérir par incréments de 200 Mbps et une fonctionnalité supplémentaire qui inclut actuellement des [capacités d'empaquetage dynamique](http://go.microsoft.com/fwlink/?LinkId=276874). Par défaut, la diffusion en continu est configurée dans un modèle d'instance partagée, pour lequel les ressources du serveur (calcul, sortie, capacité, etc.) sont partagées avec tous les autres utilisateurs. Afin d'améliorer la vitesse de diffusion en continu=, il est recommandé d'acheter des unités de diffusion en continu. 

Vous pouvez effectuer la mise à l'échelle à l'aide du Kit de développement .NET, de l'API REST ou du portail de gestion Azure.

Pour changer le nombre d'unités de diffusion en continu à l'aide du portail, procédez comme suit :

1. Pour spécifier le nombre d'unités de diffusion en continu, sélectionnez l'onglet METTRE À L'ÉCHELLE et déplacez le curseur de **capacité réservée**.

	![Scale page](./media/media-services-manage-origins/media-services-origin-scale.png)

4. Appuyez sur le bouton ENREGISTRER pour enregistrer vos modifications.

	L'allocation de nouvelles unités de diffusion en continu prend environ 20 minutes. 

	 
	>[AZURE.NOTE] Actuellement, le fait de passer d'une valeur positive à zéro pour le nombre d'unités de diffusion en continu peut désactiver la diffusion en continu pendant une heure.

	>[AZURE.NOTE] C'est le plus grand nombre d'unités spécifiées sur 24 heures qui est utilisé pour calculer le coût. Pour des informations détaillées sur la tarification, consultez la page [Détails de la tarification des services de média](http://go.microsoft.com/fwlink/?LinkId=275107).
	
## <a id="configure_streaming_endpoints"></a>Configuration du point de terminaison

Lorsque vous disposez d'au moins une unité d'échelle, le point de terminaison de diffusion en continu vous permet de configurer les propriétés suivantes : 

- Contrôle d'accès
- Noms d'hôte personnalisés
- Contrôle de cache
- Stratégies d'accès intersite

Pour plus d'informations sur ces propriétés, consultez la rubrique [StreamingEndpoint](https://msdn.microsoft.com/library/azure/dn783468.aspx).

Vous pouvez configurer ces propriétés à l'aide du Kit de développement .NET, de l'API REST ou du portail de gestion Azure.

Pour changer le nombre d'unités de diffusion en continu à l'aide du portail, procédez comme suit :

1. Sélectionnez le point de terminaison de diffusion en continu que vous souhaitez configurer.
1. Sélectionnez l'onglet CONFIGURER.
  
Vous trouverez une brève description des champs ci-dessous.

![Configure origin][configure-origin]
  

1. Définissez le délai maximal de mise en cache. Cette valeur est spécifiée dans l'en-tête de contrôle de cache des réponses HTTP. Elle n'est pas prioritaire sur la valeur de cache maximale définie explicitement dans le contenu de l'objet blob.

2. Définissez les adresses IP autorisées à se connecter au point de terminaison publié pour la diffusion en continu. Si aucune adresse IP n'est spécifiée, toutes les adresses IP peuvent se connecter.

3. Définissez la configuration pour l'authentification de l'en-tête de signature Akamai.

4. Vous pouvez spécifier une stratégie d'accès entre domaines pour les clients Adobe Flash (pour plus d'informations, consultez la page [Spécification de fichier de stratégie interdomaines](http://www.adobe.com/devnet/articles/crossdomain_policy_file_spec.html), ainsi que la stratégie d'accès client pour les clients Microsoft Silverlight (pour plus d'informations, consultez la page [Mise à disposition d'un service sans limites de domaine](https://msdn.microsoft.com/library/cc197955(v=vs.95).aspx).  

5. Vous pouvez également configurer les noms d'hôte personnalisés en cliquant sur le bouton **Configurer**. Pour plus d'informations, reportez-vous à la propriété **CustomHostNames** dans la rubrique [StreamingEndpoint](https://msdn.microsoft.com/library/dn783468.aspx).  


## <a id="enable_cdn"></a>Activer l'intégration au CDN Azure

Vous pouvez choisir d'activer l'intégration au CDN Azure pour un point de terminaison de diffusion en continu (cette option est désactivée par défaut).

Pour activer l'intégration au CDN Azure :

- Le point de terminaison de diffusion en continu doit avoir au moins une unité (d'échelle) de diffusion en continu. Si par la suite, vous souhaitez définir le nombre d'unités d'échelle sur 0, vous devez au préalable désactiver l'intégration au CDN. 

- Le point de terminaison de diffusion en continu doit être à l'état arrêté. Une fois le CDN activé, vous pouvez démarrer le point de terminaison de diffusion en continu. 

L'activation de l'intégration au CDN Azure peut prendre jusqu'à 90 minutes.   

Une fois l'intégration activée, les configurations suivantes sont désactivées : **noms d'hôte personnalisés** et **contrôle d'accès**.

L'intégration au CDN Azure n'est pas prise en charge par tous les centres de données. Si vous utilisez le portail de gestion Azure, l'option **ACTIVER LE CDN** est affichée si votre centre de données offre cette intégration. 

![Streaming Endpoint Enable CDN][streaming-endpoint-enable-cdn]

Si vous utilisez le Kit de développement logiciel (SDK) .NET ou l'API REST, vous devez vérifier si votre centre de données offre l'intégration au CDN Azure. 

Pour effectuer cette vérification, procédez comme suit :

1. Essayez de définir la propriété CdnEnabled sur true.
1. Vérifiez si le résultat renvoyé contient un code d'erreur HTTP 412 (PreconditionFailed) avec le message " La propriété CdnEnabled du point de terminaison de diffusion en continu ne peut pas être définie sur true, car la fonctionnalité de CDN n'est pas disponible dans la région actuelle. " 

	Si vous obtenez cette erreur, le centre de données ne prend pas en charge l'intégration au CDN Azure. Vous devez essayer un autre centre de données.


[streaming-endpoint-enable-cdn]: ./media/media-services-manage-origins/media-services-origins-enable-cdn.png
[streaming-endpoint]: ./media/media-services-manage-origins/media-services-origins-page.png
[configure-origin]: ./media/media-services-manage-origins/media-services-origins-configure.png
[configure-origin-configure-custom-host-names]: ./media/media-services-manage-origins/media-services-configure-custom-host-names.png


<!--HONumber=52--> 