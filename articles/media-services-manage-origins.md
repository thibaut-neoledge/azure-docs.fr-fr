<properties urlDisplayName="index" pageTitle="Gestion des origines dans un compte Media Services" metaKeywords="" description="" metaCanonical="" services="" documentationCenter="" title="Gestion des points de terminaison de diffusion en continu dans un compte Media Services" authors="juliako"  solutions="" writer="juliako" manager="dwrede" editor=""  />

<tags ms.service="media-services" ms.workload="media" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="juliako" />

# <span id="managemediaservicesorigins"></span></a>Gestion des points de terminaison de diffusion en continu dans un compte Media Services

Media Services vous permet d'ajouter plusieurs points de terminaison de diffusion en continu à votre compte et de les configurer. Chaque compte Media Services est associé au minimum à un point de terminaison de diffusion en continu nommé **default**.

> [WACOM.NOTE] Les points de terminaison de diffusion en continu étaient appelés origines.

## Ajout et suppression de points de terminaison de diffusion en continu

1.  Dans le [portail de gestion][portail de gestion], cliquez sur **Media Services**. Cliquez ensuite sur le nom du service multimédia.
2.  Sélectionnez la page POINTS DE TERMINAISON DE DIFFUSION EN CONTINU.
3.  Cliquez sur le bouton ADD ou DELETE en bas de la page. Attention : il n'est pas possible de supprimer le point de terminaison par défaut.
4.  Cliquez sur le bouton DÉMARRER pour démarrer le point de terminaison.
5.  Cliquez sur le nom du point de terminaison pour le configurer.

    ![page Origine][page Origine]

## Configuration du point de terminaison

L'onglet CONFIGURER vous permet d'effectuer les configurations suivantes :

1.  Définir le délai maximal de mise en cache. Cette valeur est spécifiée dans l'en-tête de contrôle de cache des réponses HTTP. Elle n'est pas prioritaire sur la valeur de cache maximale définie explicitement dans le contenu de l'objet blob.

2.  Définir les adresses IP autorisées à se connecter au point de terminaison publié pour la diffusion en continu. Si aucune adresse IP n'est spécifiée, toutes les adresses IP peuvent se connecter.

3.  Définissez la configuration pour l'authentification de l'en-tête de signature Akamai.

    ![Configurer l'origine][Configurer l'origine]

    La configuration sur cette page s'appliquera uniquement aux points de terminaison de diffusion en continu qui ont au moins une unité réservée. Pour réserver les unités de diffusion en continu à la demande réservées, ouvrez l'onglet METTRE À L'ÉCHELLE. Pour plus d'informations sur les unités réservées, consultez la page [Mise à l'échelle de Media Services][Mise à l'échelle de Media Services].

  [portail de gestion]: https://manage.windowsazure.com/
  [page Origine]: ./media/media-services-manage-origins/media-services-origins-page.png
  [Configurer l'origine]: ./media/media-services-manage-origins/media-services-origins-configure.png
  [Mise à l'échelle de Media Services]: ../media-services-how-to-scale/
