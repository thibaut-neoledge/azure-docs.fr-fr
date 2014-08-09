<properties linkid="video-center-index" urlDisplayName="index" pageTitle="Video Center Index" metaKeywords="" description="" metaCanonical="" services="" documentationCenter="" title="How to Use ASP.NET Session State with Azure Web Sites" authors="jroth" solutions="" manager="" editor="" />

Utilisation de l'état de session ASP.NET avec Sites Web Azure
=============================================================

Cette rubrique vous explique comment utiliser Azure Cache Service (Preview) pour prendre en charge la mise en cache de l'état de session ASP.NET.

En l'absence de fournisseur externe, l'état de session est stocké en cours de processus sur le serveur Web qui héberge le site. S'agissant de Sites Web Azure, deux problèmes affectent l'état de session en cours de processus. Tout d'abord, dans le cas des sites comportant plusieurs instances, l'état de session stocké sur une instance n'est pas accessible aux autres. Dans la mesure où une demande utilisateur peut être acheminée vers n'importe quelle instance, rien ne garantit que les informations de session se trouveront à cet endroit. Ensuite, tout changement de configuration peut entraîner l'exécution du site Web sur un serveur complètement différent.

Azure Cache Service (Preview) fournit un service de mise en cache distribuée externe au site Web. Cela permet de remédier au problème affectant l'état de session en cours de processus. Pour plus d'informations sur l'utilisation de l'état de session, consultez la page [Vue d'ensemble de l'état de session ASP.NET](http://msdn.microsoft.com/fr-fr/library/ms178581.aspx).

La procédure de base pour utiliser Cache Service (Preview) pour la mise en cache de l'état de session comprend les étapes suivantes :

-   [Création du cache](#createcache)
-   [Configuration du projet ASP.NET de manière à utiliser le service Azure Cache](#configureproject)
-   [Modification du fichier web.config](#configurewebconfig)
-   [Utilisation de l'objet Session pour stocker et récupérer des éléments mis en cache](#usesessionobject)

Création du cache
-----------------

1.  Cliquez sur l'icône **Nouveau** au bas du portail de gestion Azure.

    ![NewIcon](./media/web-sites-dotnet-session-state-caching/CacheScreenshot_NewButton.png)

2.  Sélectionnez **Data Services**, **Cache**, puis cliquez sur **Quick Create**.

    ![NewCacheDialog](./media/web-sites-dotnet-session-state-caching/CachingScreenshot_CreateOptions.png)

3.  Entrez un nom unique pour le cache dans la zone de texte **Point de terminaison**. Sélectionnez ensuite les valeurs appropriées pour les autres propriétés du cache et cliquez sur **Create a New Cache**.

4.  Sélectionnez l'icône **Cache** dans le portail de gestion pour afficher tous vos points de terminaison Cache Service.

    ![CacheIcon](./media/web-sites-dotnet-session-state-caching/CachingScreenshot_CacheIcon.png)

5.  Vous pouvez ensuite sélectionner l'un des points de terminaison Cache Service pour en afficher les propriétés. Les sections suivantes utilisent des paramètres de l'onglet **Tableau de bord** afin de configurer la mise en cache pour un projet ASP.NET.

Configuration du projet ASP.NET
-------------------------------

1.  Vérifiez tout d'abord que vous avez bien [installé le](http://www.windowsazure.com/fr-fr/downloads/?sdk=net) **Kit de développement logiciel (SDK) Azure le plus récent pour .NET**.

2.  Cliquez avec le bouton droit sur le projet ASP.NET dans l'**Explorateur de solutions** de Visual Studio, puis sélectionnez **Manage NuGet Packages**. Si vous utilisez WebMatrix, cliquez plutôt sur le bouton **NuGet** dans la barre d'outils.

3.  Tapez **WindowsAzure.Caching** dans la zone d'édition **Rechercher en ligne**.

    ![NuGetDialog](./media/web-sites-dotnet-session-state-caching/CachingScreenshot_NuGet.png)

4.  Sélectionnez le package **Azure Caching**, puis cliquez sur le bouton **Installer**.

Modification du fichier web.config
----------------------------------

Outre la création de références d'assembly pour le cache, le package NuGet ajoute des entrées de stub dans le fichier web.config. Plusieurs modifications doivent être apportées au fichier web.config pour qu'il soit possible d'utiliser Cache pour l'état de session.

1.  Ouvrez le fichier **web.config** pour le projet ASP.NET.

2.  Recherchez l'élément **sessionState** existant et mettez-le en commentaire (ou supprimez-le).

3.  Annulez ensuite le commentaire de l'élément **sessionState** qui a été ajouté par le package Azure Caching NuGet. Le résultat obtenu doit être semblable à la capture d'écran suivante.

    ![SessionStateConfig][SessionStateConfig]

4.  Recherchez ensuite la section **dataCacheClients**. Annulez le commentaire de l'élément enfant **securityProperties**.

    ![CacheConfig](./media/web-sites-dotnet-session-state-caching/CachingScreenshot_CacheConfig.png)

5.  Dans l'élément **autoDiscover**, définissez l'attribut **identifier** sur l'URL de point de terminaison de votre cache. Pour trouver l'URL de votre point de terminaison, accédez aux propriétés du cache dans le portail de gestion Azure. Copiez la valeur **ENDPOINT URL** dans la section **Quick Glance** de l'onglet **Tableau de bord**.

    ![EndpointURL](./media/web-sites-dotnet-session-state-caching/CachingScreenshot_EndpointURL.png)

6.  Dans l'élément **messageSecurity**, définissez l'attribut **authorizationInfo** sur la clé d'accès de votre cache. Pour trouver la clé d'accès, sélectionnez votre cache dans le portail de gestion Azure. Cliquez ensuite sur l'icône **Gérer les clés** de la barre inférieure. Cliquez sur le bouton de copie en regard de la zone de texte **CLÉ D'ACCÈS PRIMAIRE**.

    ![ManageKeys](./media/web-sites-dotnet-session-state-caching/CachingScreenshot_ManageAccessKeys.png)

Utilisation de l'objet Session dans le code
-------------------------------------------

La dernière étape consiste à utiliser l'objet Session dans votre code ASP.NET. Pour ajouter des objets à l'état de session, utilisez la méthode **Session.Add**. Cette méthode utilise des paires clé-valeur pour stocker des éléments dans le cache d'état de session.

    string strValue = "yourvalue";
    Session.Add("yourkey", strValue);

Le code suivant récupère cette valeur dans l'état de session.

    object objValue = Session["yourkey"];
    if (objValue != null)
       strValue = (string)obj;  

Pour plus d'informations sur l'utilisation de l'état de session ASP.NET, consultez la page [Vue d'ensemble de l'état de session ASP.NET](http://msdn.microsoft.com/fr-fr/library/ms178581.aspx).

