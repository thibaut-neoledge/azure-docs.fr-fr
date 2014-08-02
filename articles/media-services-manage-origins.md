<properties linkid="scripting-center-index" urlDisplayName="index" pageTitle="Scripting Center Index" metaKeywords="" description="" metaCanonical="" services="" documentationCenter="" title="How to Manage Origins in a Media Services Account" authors="juliako" solutions="" manager="" editor="" />

Gestion des origines dans un compte Media Services
==================================================

Media Services vous permet d'ajouter plusieurs origines de diffusion en continu à votre compte et de les configurer. Chaque compte Media Services est associé au minimum à une origine de diffusion en continu nommée **default**.

Ajout et suppression d'origines
-------------------------------

1.  Dans le [portail de gestion](https://manage.windowsazure.com/), cliquez sur **Media Services**. Cliquez ensuite sur le nom du service multimédia.
2.  Sélectionnez la page ORIGINS.
3.  Cliquez sur le bouton ADD ou DELETE en bas de la page. Attention : il n'est pas possible de supprimer l'origine par défaut.
4.  Cliquez sur le bouton START pour démarrer l'origine.
5.  Cliquez sur le nom de l'origine pour la configurer.

    ![page Origine](./media/media-services-manage-origins/media-services-origins-page.png)

Configuration de l'origine
--------------------------

L'onglet CONFIGURER vous permet d'effectuer les configurations suivantes :

1.  Définir le délai maximal de mise en cache. Cette valeur est spécifiée dans l'en-tête de contrôle de cache des réponses HTTP. Elle n'est pas prioritaire sur la valeur de cache maximale définie explicitement dans le contenu de l'objet blob.

2.  Définir les adresses IP autorisées à se connecter au point de terminaison publié pour la diffusion en continu. Si aucune adresse IP n'est spécifiée, toutes les adresses IP peuvent se connecter.

3.  Définir la configuration des requêtes d'authentification G20 provenant de serveurs Akami.

    ![Configurer l'origine](./media/media-services-manage-origins/media-services-origins-configure.png)

    La configuration sur cette page s'applique uniquement aux origines dont au moins une unité est réservée. Pour réserver les unités de diffusion en continu à la demande réservées, ouvrez l'onglet METTRE À L'ÉCHELLE. Pour plus d'informations sur les unités réservées, consultez la page [Mise à l'échelle de Media Services](http://go.microsoft.com/fwlink/?LinkID=275847&clcid=0x409/).


