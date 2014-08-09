<properties linkid="video-center-detail" urlDisplayName="details" pageTitle="Video Center Details" metaKeywords="" description="" metaCanonical="" services="" documentationCenter="" title="How to Use ASP.NET Web Forms Output Caching with Azure Web Sites" authors="jroth" solutions="" manager="" editor="" />

Utilisation de la mise en cache de sortie Web Forms ASP.NET avec Sites Web Azure
================================================================================

Cette rubrique explique comment utiliser Azure Cache Service (Preview) pour prendre en charge la mise en cache de sortie de pages ASP.NET pour les applications Web Forms ASP.NET. La mise en cache de sortie de pages est une optimisation qui renvoie directement une page de rendu mise en cache pendant une durée spécifique. Cette fonction est particulièrement utile lorsque l'accès à une page est plus fréquent que sa modification. Il est important de noter que la mise en cache de sortie de pages n'est pas prise en charge par les applications MVC ASP.NET.

Azure Cache Service (Preview) fournit un service de mise en cache distribuée externe au site Web. Toutes les instances du site Web ont ainsi accès à la même page de rendu mise en cache.

La procédure de base permettant d'utiliser Cache Service (Preview) pour la mise en cache de sortie de pages comprend les étapes suivantes :

-   [Création du cache](#createcache)
-   [Configuration du projet ASP.NET de manière à utiliser le service Azure Cache](#configureproject)
-   [Modification du fichier web.config](#configurewebconfig)
-   [Utilisation de la mise en cache de sortie pour renvoyer temporairement les versions mises en cache d'une page](#useoutputcaching)

Création du cache
-----------------

1.  Cliquez sur l'icône **Nouveau** au bas du portail de gestion Azure.

    ![NewIcon](./media/web-sites-web-forms-output-caching/CacheScreenshot_NewButton.PNG)

2.  Sélectionnez **Services de données**, **Cache**, puis cliquez sur **Création rapide**.

    ![NewCacheDialog](./media/web-sites-web-forms-output-caching/CachingScreenshot_CreateOptions.PNG)

3.  Entrez un nom unique pour le cache dans la zone de texte **Point de terminaison**. Sélectionnez ensuite les valeurs appropriées pour les autres propriétés du cache et cliquez sur **Create a New Cache**.

4.  Sélectionnez l'icône **Cache** dans le portail de gestion pour afficher tous vos points de terminaison Cache Service.

    ![CacheIcon](./media/web-sites-web-forms-output-caching/CachingScreenshot_CacheIcon.PNG)

5.  Vous pouvez ensuite sélectionner l'un des points de terminaison Cache Service pour en afficher les propriétés. Les sections suivantes utilisent des paramètres de l'onglet **Tableau de bord** afin de configurer la mise en cache pour un projet ASP.NET.

Configuration du projet ASP.NET
-------------------------------

1.  Vérifiez tout d'abord que vous avez bien [installé le](http://www.windowsazure.com/fr-fr/downloads/?sdk=net) **Kit de développement logiciel (SDK) Azure le plus récent pour .NET**.

2.  Cliquez avec le bouton droit sur le projet ASP.NET dans l'**Explorateur de solutions** de Visual Studio, puis sélectionnez **Manage NuGet Packages**. Si vous utilisez WebMatrix, cliquez plutôt sur le bouton **NuGet** dans la barre d'outils.

3.  Tapez **WindowsAzure.Caching** dans la zone d'édition **Rechercher en ligne**.

    ![NuGetDialog](./media/web-sites-web-forms-output-caching/CachingScreenshot_NuGet.PNG)

4.  Sélectionnez le package **Azure Caching**, puis cliquez sur le bouton **Installer**.

Modification du fichier web.config
----------------------------------

Outre la création de références d'assembly pour le cache, le package NuGet ajoute des entrées de stub dans le fichier web.config. Si vous souhaitez utiliser Cache pour la mise en cache de sortie de pages ASP.NET, différentes modifications doivent être apportées au fichier web.config.

1.  Ouvrez le fichier **web.config** pour le projet ASP.NET.

2.  En présence d'éléments **caching** et **outputCache** existants, placez-les en commentaire (ou supprimez-les).

3.  Annulez ensuite le commentaire de l'élément **caching** ajouté par le package Azure Caching NuGet. Le résultat obtenu doit être semblable à la capture d'écran suivante.

    ![OutputConfig](./media/web-sites-web-forms-output-caching/CachingScreenshot_OC_WebConfig.PNG)

4.  Recherchez ensuite la section **dataCacheClients**. Annulez le commentaire de l'élément enfant **securityProperties**.

    ![CacheConfig](./media/web-sites-web-forms-output-caching/CachingScreenshot_CacheConfig.PNG)

5.  Dans l'élément **autoDiscover**, définissez l'attribut **identifier** sur l'URL de point de terminaison de votre cache. Pour trouver l'URL de votre point de terminaison, accédez aux propriétés du cache dans le portail de gestion Azure. Dans l'onglet **Tableau de bord**, copiez la valeur **URL DE POINT DE TERMINAISON** dans la section **Aperçu rapide**.

    ![EndpointURL](./media/web-sites-web-forms-output-caching/CachingScreenshot_EndpointURL.PNG)

6.  Dans l'élément **messageSecurity**, définissez l'attribut **authorizationInfo** sur la clé d'accès de votre cache. Pour trouver la clé d'accès, sélectionnez votre cache dans le portail de gestion Azure. Cliquez ensuite sur l'icône **Gérer les clés** de la barre inférieure. Cliquez sur le bouton de copie en regard de la zone de texte **CLÉ D'ACCÈS PRIMAIRE**.

    ![ManageKeys](./media/web-sites-web-forms-output-caching/CachingScreenshot_ManageAccessKeys.PNG)

Utilisation de la mise en cache de sortie
-----------------------------------------

La dernière étape consiste à configurer les pages de votre application Web Forms ASP.NET pour utiliser la mise en cache de sortie. Pour ce faire, ajoutez un attribut **OutputCache** au début de la source .ASPX. Par exemple :

    <%@ OutputCache Duration="45" VaryByParam="*" %>

Dans l'exemple précédent, la page est mise en cache pendant quarante-cinq secondes. Sachant que **VaryByParam** est défini sur « \* », cette sortie de page mise en cache ne change pas, même si des paramètres de requête différents sont transmis. En revanche, dans l'exemple suivant, une version différente de la page est mise en cache pour chacune des valeurs du paramètre « UserId » :

    <%@ OutputCache Duration="45" VaryByParam="UserId" %>  
