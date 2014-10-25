<properties linkid="video-center-detail" urlDisplayName="details" pageTitle="Video Center Details" metaKeywords="" description="" metaCanonical="" services="" documentationCenter="" title="How to Use ASP.NET Web Forms Output Caching with Azure Websites" authors="sdanie" solutions="" manager="" editor="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="sdanie"></tags>

# Utilisation de la mise en cache de sortie Web Forms ASP.NET avec Sites Web Azure

Cette rubrique explique comment utiliser Azure Cache Service (Preview) pour prendre en charge la mise en cache de sortie de pages ASP.NET pour les applications Web Forms ASP.NET. La mise en cache de sortie de pages est une optimisation qui renvoie directement une page de rendu mise en cache pendant une durée spécifique. Cette fonction est particulièrement utile lorsque l'accès à une page est plus fréquent que sa modification. Il est important de noter que la mise en cache de sortie de pages n'est pas prise en charge par les applications MVC ASP.NET.

Cache Service (Preview) fournit un service de mise en cache distribuée externe au site web. Toutes les instances du site web ont ainsi accès à la même page de rendu mise en cache.

La procédure de base permettant d'utiliser Cache Service (Preview) pour la mise en cache de sortie de pages comprend les étapes suivantes :

-   [Création du cache][]
-   [Configuration du projet ASP.NET de manière à utiliser le service Azure Cache][]
-   [Modification du fichier web.config][]
-   [Utilisation de la mise en cache de sortie pour renvoyer temporairement les versions mises en cache d'une page][]

## <span id="createcache"></span></a>Créer le cache

Les instances de cache du service de cache géré sont créées à l'aide d'applets de commande PowerShell.

> Une fois qu'une instance du service de cache géré est créée à l'aide des applets de commande PowerShell, vous pouvez l'afficher et la configurer dans le [portail de gestion Azure][].

Pour créer une instance du service de cache géré, ouvrez une fenêtre de commande Azure PowerShell.

> Pour obtenir des instructions sur l'installation et l'utilisation d'Azure PowerShell, consultez [Installation et configuration d'Azure PowerShell][].

Appelez l'applet de commande [Add-AzureAccount][], puis entrez l'adresse de messagerie et le mot de passe associés à votre compte. Un abonnement est choisi par défaut et s'affiche une fois que vous avez appelé l'applet de commande [Add-AzureAccount][]. Pour changer l'abonnement, appelez l'applet de commande [Select-AzureSubscription][].

> Si vous avez configuré Azure PowerShell avec un certificat pour votre compte, vous pouvez sauter cette étape. Pour plus d'informations sur la connexion d'Azure PowerShell à votre compte Azure, consultez [Installation et configuration d'Azure PowerShell][].

Un abonnement choisi par défaut s'affiche. Pour changer l'abonnement, appelez l'applet de commande [Select-AzureSubscription][].

Appelez l'applet de commande [New-AzureManagedCache][], puis spécifiez le nom, la région, l'offre de cache et la taille de la mémoire cache.

Dans **Nom**, entrez le nom de sous-domaine à utiliser pour le point de terminaison du cache. Le point de terminaison doit être une chaîne composée de six à vingt caractères, contenant uniquement des minuscules et des chiffres, et commençant par une lettre.

Dans **Emplacement**, spécifiez la région du cache. Pour des performances optimales, créez le cache dans la même région que l'application cliente du cache.

**Référence (SKU)** et **Mémoire** fonctionnent ensemble pour déterminer la taille du cache. Le service de cache géré est disponible dans les trois niveaux suivants.

-   Basic - Tailles de cache comprises entre 128 Mo et 1 Go par incréments de 128 Mo, avec un seul cache nommé par défaut
-   Standard - Tailles de cache comprises entre 1 Go et 10 Go par incréments de 1 Go, avec prise en charge des notifications et de dix caches nommés (maximum)
-   Premium - Tailles de cache comprises entre 5 Go et 150 Go par incréments de 5 Go, avec prise en charge des notifications, de la haute disponibilité et de dix caches nommés (maximum)

Choisissez les valeurs de **Référence (SKU)** et de **Mémoire** qui répondent aux besoins de votre application. Notez que certaines fonctionnalités du cache, telles les notifications et la haute disponibilité, ne sont disponibles qu'avec certaines offres de cache. Pour plus d'informations sur le choix de l'offre et de la taille de cache les plus adaptées à votre application, consultez [Offres de cache][].

Dans l'exemple suivant, un cache de base de 128 Mo est créé avec le nom contosocache, dans le Sud du centre des États-Unis.

    New-AzureManagedCache -Name contosocache -Location "South Central US" -Sku Basic -Memory 128MB

> Pour obtenir la liste complète des paramètres et des valeurs qui peuvent être utilisés durant la création d'un cache, consultez la documentation de l'applet de commande [New-AzureManagedCache][].

Une fois l'applet de commande PowerShell appelée, la création du cache peut prendre plusieurs minutes. Après sa création, le cache a l'état `Running` et est prêt à fonctionner avec les paramètres par défaut. En outre, vous pouvez l'afficher et le configurer dans le [portail de gestion Azure][].

Vous pouvez surveiller l'avancement de la création dans la fenêtre Azure PowerShell. Une fois que le cache est prêt à l'emploi, l'applet de commande [New-AzureManagedCache][] affiche les informations relatives à ce dernier, comme le montre l'exemple suivant.

    PS C:\> Add-AzureAccount
    VERBOSE: Account "user@domain.com" has been added.
    VERBOSE: Subscription "MySubscription" is selected as the default subscription.
    VERBOSE: To view all the subscriptions, please use Get-AzureSubscription.
    VERBOSE: To switch to a different subscription, please use Select-AzureSubscription.
    PS C:\> New-AzureManagedCache -Name contosocache -Location "South Central US" -Sku Basic -Memory 128MB
    VERBOSE: Intializing parameters...
    VERBOSE: Creating prerequisites...
    VERBOSE: Verify cache service name...
    VERBOSE: Creating cache service...
    VERBOSE: Waiting for cache service to be in ready state...


    Name     : contosocache
    Location : South Central US
    State    : Active
    Sku      : Basic
    Memory   : 128MB



    PS C:\>

Les sections suivantes utilisent des paramètres de l'onglet **Tableau de bord** afin de configurer la mise en cache pour un projet ASP.NET.

## <span id="configureproject"></span></a>Configurer le projet ASP.NET

1.  Vérifiez tout d'abord que vous avez bien [installé le][] **Kit de développement logiciel (SDK) Azure le plus récent pour .NET**.

2.  Cliquez avec le bouton droit sur le projet ASP.NET dans l'**Explorateur de solutions** de Visual Studio, puis sélectionnez **Manage NuGet Packages**. Si vous utilisez WebMatrix, cliquez plutôt sur le bouton **NuGet** dans la barre d'outils.

3.  Tapez **WindowsAzure.Caching** dans la zone d'édition **Rechercher en ligne**.

    ![NuGetDialog][]

4.  Sélectionnez le package **Azure Caching**, puis cliquez sur le bouton **Installer**.

## <span id="configurewebconfig"></span></a>Modifier le fichier web.config

Outre la création de références d'assembly pour le cache, le package NuGet ajoute des entrées de stub dans le fichier web.config. Si vous souhaitez utiliser Cache pour la mise en cache de sortie de pages ASP.NET, différentes modifications doivent être apportées au fichier web.config.

1.  Ouvrez le fichier **web.config** pour le projet ASP.NET.

2.  En présence d'éléments **caching** et **outputCache** existants, placez-les en commentaire (ou supprimez-les).

3.  Annulez ensuite le commentaire de l'élément **caching** ajouté par le package Azure Caching NuGet. Le résultat obtenu doit être semblable à la capture d'écran suivante.

    ![OutputConfig][]

4.  Recherchez ensuite la section **dataCacheClients**. Annulez le commentaire de l'élément enfant **securityProperties**.

    ![CacheConfig][]

5.  Dans l'élément **autoDiscover**, définissez l'attribut **identifier** sur l'URL de point de terminaison de votre cache. Pour trouver l'URL de votre point de terminaison, accédez aux propriétés du cache dans le portail de gestion Azure. Dans l'onglet **Tableau de bord**, copiez la valeur **URL DE POINT DE TERMINAISON** dans la section **Aperçu rapide**.

    ![EndpointURL][]

6.  Dans l'élément **messageSecurity**, définissez l'attribut **authorizationInfo** sur la clé d'accès de votre cache. Pour trouver la clé d'accès, sélectionnez votre cache dans le portail de gestion Azure. Cliquez ensuite sur l'icône **Gérer les clés** de la barre inférieure. Cliquez sur le bouton de copie en regard de la zone de texte **CLÉ D'ACCÈS PRIMAIRE**.

    ![ManageKeys][]

## <span id="useoutputcaching"></span></a>Utiliser la mise en cache de sortie

La dernière étape consiste à configurer les pages de votre application Web Forms ASP.NET pour utiliser la mise en cache de sortie. Pour ce faire, ajoutez un attribut **OutputCache** au début de la source .ASPX. Par exemple :

    <%@ OutputCache Duration="45" VaryByParam="*" %>

Dans l'exemple précédent, la page est mise en cache pendant quarante-cinq secondes. Sachant que **VaryByParam** est défini sur « \* », cette sortie de page mise en cache ne change pas, même si des paramètres de requête différents sont transmis. En revanche, dans l'exemple suivant, une version différente de la page est mise en cache pour chacune des valeurs du paramètre « UserId » :

    <%@ OutputCache Duration="45" VaryByParam="UserId" %>   

  [Création du cache]: #createcache
  [Configuration du projet ASP.NET de manière à utiliser le service Azure Cache]: #configureproject
  [Modification du fichier web.config]: #configurewebconfig
  [Utilisation de la mise en cache de sortie pour renvoyer temporairement les versions mises en cache d'une page]: #useoutputcaching
  [portail de gestion Azure]: https://manage.windowsazure.com/
  [Installation et configuration d'Azure PowerShell]: http://go.microsoft.com/fwlink/?LinkId=400494
  [Add-AzureAccount]: http://msdn.microsoft.com/fr-fr/library/dn495128.aspx
  [Select-AzureSubscription]: http://msdn.microsoft.com/fr-fr/library/dn495203.aspx
  [New-AzureManagedCache]: http://go.microsoft.com/fwlink/?LinkId=400495
  [installé le]: http://www.windowsazure.com/fr-fr/downloads/?sdk=net
  [NuGetDialog]: ./media/web-sites-web-forms-output-caching/CachingScreenshot_NuGet.PNG
  [OutputConfig]: ./media/web-sites-web-forms-output-caching/CachingScreenshot_OC_WebConfig.PNG
  [CacheConfig]: ./media/web-sites-web-forms-output-caching/CachingScreenshot_CacheConfig.PNG
  [EndpointURL]: ./media/web-sites-web-forms-output-caching/CachingScreenshot_EndpointURL.PNG
  [ManageKeys]: ./media/web-sites-web-forms-output-caching/CachingScreenshot_ManageAccessKeys.PNG
