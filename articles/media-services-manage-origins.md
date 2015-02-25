<properties pageTitle="Gestion des origines dans un compte Media Services" description="" services="media-services" documentationCenter="" authors="juliako" writer="juliako" manager="dwrede" editor=""/>

<tags ms.service="media-services" ms.workload="media" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/29/2015" ms.author="juliako"/>


# <a id="managemediaservicesorigins"></a>Gestion des points de terminaison de diffusion en continu dans un compte Media Services

Media Services vous permet d'ajouter plusieurs points de terminaison de diffusion en continu à votre compte et de les configurer. Chaque compte Media Services est associé au minimum à un point de terminaison de diffusion en continu nommé **default**AES.

>[AZURE.NOTE] Les points de terminaison de diffusion en continu étaient appelés origines. 


## Ajout et suppression de points de terminaison de diffusion en continu 

1. Dans le [portail de gestion](https://manage.windowsazure.com/), cliquez sur **Media Services**. Cliquez ensuite sur le nom du service multimédia.
2. Sélectionnez la page POINTS DE TERMINAISON DE DIFFUSION EN CONTINU. 
3. Cliquez sur le bouton ADD ou DELETE en bas de la page. Attention : il n'est pas possible de supprimer le point de terminaison par défaut. 
4. Cliquez sur le bouton DÉMARRER pour démarrer le point de terminaison. 
5. Cliquez sur le nom du point de terminaison pour le configurer.   

	![Origin page][origin-page]

## Extension du point de terminaison

Les unités de diffusion en continu vous fournissent une capacité de sortie dédiée qui peut être achetée par incréments de 200 Mbit/s,  ainsi que des fonctionnalités supplémentaires qui incluent actuellement l'[empaquetage dynamique](http://go.microsoft.com/fwlink/?LinkId=276874). Par défaut, la diffusion en continu à la demande est configurée dans un modèle d'instance partagée, pour lequel les ressources du serveur (calcul, sortie, capacité, etc.) sont partagées avec tous les autres utilisateurs. Afin d'améliorer la vitesse de diffusion en continu à la demande, il est recommandé d'acheter des unités de diffusion en continu. 

Pour changer le nombre d'unités de diffusion en continu, procédez comme suit :

1. Pour spécifier le nombre d'unités de diffusion en continu, sélectionnez l'onglet METTRE À L'ÉCHELLE et déplacez le curseur de **capacité réservée**.

	![Scale page](./media/media-services-how-to-scale/media-services-origin-scale.png)

4. Appuyez sur le bouton ENREGISTRER pour enregistrer vos modifications.

	l'allocation de nouvelles unités de diffusion en continu prend environ 20 minutes. 

	 
	>[AZURE.NOTE] Actuellement, le fait de passer d'une valeur positive à zéro pour le nombre d'unités de diffusion en continu peut désactiver la diffusion en continu pendant une heure.

	>[AZURE.NOTE] Le nombre d'unités le plus élevé spécifié pour la période de 24 heures est utilisé pour le calcul du coût. Pour des informations détaillées sur la tarification, consultez la page [Détails de la tarification de Media Services](http://go.microsoft.com/fwlink/?LinkId=275107).
	
## Configuration du point de terminaison

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


<!--HONumber=42-->
