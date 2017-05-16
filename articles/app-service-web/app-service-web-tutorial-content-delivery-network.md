---
title: "Ajouter un réseau de distribution de contenu (CDN) à un Azure App Service | Microsoft Docs"
description: "Ajoutez un réseau de distribution de contenu (CDN) à un Azure App Service pour mettre en cache et distribuer vos fichiers statiques à partir de serveurs proches de vos clients dans le monde entier."
services: app-service
author: syntaxc4
ms.author: cfowler
ms.date: 05/01/2017
ms.topic: hero-article
ms.service: app-service-web
manager: erikre
ms.translationtype: Human Translation
ms.sourcegitcommit: 2db2ba16c06f49fd851581a1088df21f5a87a911
ms.openlocfilehash: 7208abc0e6eaa9067c5bb36a09e1bfd276fe0b0c
ms.contentlocale: fr-fr
ms.lasthandoff: 05/08/2017

---
# <a name="add-a-content-delivery-network-cdn-to-an-azure-app-service"></a>Ajouter un réseau de distribution de contenu (CDN) à un Azure App Service

Le [réseau de distribution de contenu (CDN) Azure](../cdn/cdn-overview.md) met en cache le contenu web statique à des emplacements stratégiques afin de fournir un débit maximal pour la distribution de contenu aux utilisateurs. Le CDN réduit également la charge du serveur sur votre application web. Ce didacticiel montre comment ajouter Azure CDN à une [application web dans Azure App Service](app-service-web-overview.md). 

Ce didacticiel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Créer un point de terminaison CDN.
> * Actualiser les ressources mises en cache.
> * Utiliser des chaînes de requête pour contrôler les versions mises en cache.
> * Utiliser un domaine personnalisé pour le point de terminaison CDN.

Voici la page d’accueil de l’exemple de site HTML statique que vous allez utiliser :

![Page d’accueil de l’exemple d’application](media/app-service-web-tutorial-content-delivery-network/sample-app-home-page.png)

## <a name="create-the-web-app"></a>Création de l'application Web

Pour créer l’application web que vous allez utiliser, suivez les instructions de l’article [Créer une application web HTML statique dans Azure en 5 minutes](app-service-web-get-started-html.md), mais ignorez l’étape **Supprimer des ressources**.

Lorsque vous avez terminé le didacticiel, ne fermez pas l’invite de commandes afin que vous puissiez déployer des modifications supplémentaires dans l’application web plus loin dans ce didacticiel.

### <a name="have-a-custom-domain-ready"></a>Nécessité d’avoir un nom de domaine

Pour suivre l’étape relative au domaine personnalisé de ce didacticiel, vous devez avoir accès à votre registre DNS pour votre fournisseur de domaine (tel que GoDaddy). Par exemple, pour ajouter des entrées DNS pour `contoso.com` et `www.contoso.com`, vous devez pouvoir configurer les paramètres DNS du domaine racine `contoso.com`.

Si vous n’avez pas encore de nom de domaine, suivez le [didacticiel sur le domaine App Service](custom-dns-web-site-buydomains-web-app.md) pour acheter un domaine à l’aide du portail Azure. 

## <a name="log-in-to-the-azure-portal"></a>Connectez-vous au portail Azure.

Ouvrez un navigateur et accédez au [portail Azure](https://portal.azure.com).

## <a name="create-a-cdn-profile-and-endpoint"></a>Création d’un profil CDN et d’un point de terminaison

Dans le volet de navigation gauche, sélectionnez **App Services**, puis sélectionnez l’application que vous avez créée à la rubrique [Créer une application web HTML statique dans Azure en 5 minutes](app-service-web-get-started-html.md).

![Sélection de l’application App Service dans le portail](media/app-service-web-tutorial-content-delivery-network/portal-select-app-services.png)

Dans la page **App Service**, dans la section **Paramètres**, sélectionnez **Mise en réseau > Configurer Azure CDN pour votre application**.

![Sélection de CDN dans le portail](media/app-service-web-tutorial-content-delivery-network/portal-select-cdn.png)

Dans la page **Azure Content Delivery Network**, fournissez les paramètres du **Nouveau point de terminaison**, comme spécifié dans le tableau.

![Création d’un profil et d’un point de terminaison dans le portail](media/app-service-web-tutorial-content-delivery-network/portal-new-endpoint.png)

| Paramètre | Valeur suggérée | Description |
| ------- | --------------- | ----------- |
| **Profil CDN** | myCDNProfile | Sélectionnez **Création** pour créer un nouveau profil CDN. Un profil CDN est une collection de points de terminaison CDN possédant le même niveau tarifaire. |
| **Niveau de tarification** | Standard Akamai | Le [niveau tarifaire](../cdn/cdn-overview.md#azure-cdn-features) spécifie le fournisseur et les fonctionnalités disponibles. Dans ce didacticiel, nous utilisons Standard Akamai. |
| **Nom du point de terminaison CDN** | N’importe quel nom qui est unique dans le domaine azureedge.net | Vous accédez à vos ressources mises en cache au niveau du *\<Nom_Point_Terminaison>.azureedge.net* du domaine.

Sélectionnez **Créer**.

Azure crée le profil et le point de terminaison. Le nouveau point de terminaison s’affiche dans la liste **Points de terminaison** sur la même page, et lorsqu’il est configuré, l’état est **Exécution en cours**.

![Nouveau point de terminaison dans la liste](media/app-service-web-tutorial-content-delivery-network/portal-new-endpoint-in-list.png)

### <a name="test-the-cdn-endpoint"></a>Test du point de terminaison CDN

Si vous avez sélectionné le niveau tarifaire Verizon, il faut généralement compter environ 90 minutes pour la propagation du point de terminaison. Pour Akamai, la propagation prend quelques minutes.

L’exemple d’application comprend un fichier `index.html` et des dossiers *css*, *img* et *js* contenant d’autres ressources statiques. Les chemins d’accès de contenu pour tous ces fichiers sont identiques au point de terminaison CDN. Par exemple, les deux URL suivantes accèdent au fichier *bootstrap.css* dans le dossier *css* :

```
http://<appname>.azurewebsites.net/css/bootstrap.css
```

```
http://<endpointname>.azureedge.net/css/bootstrap.css
```

Lorsque vous accédez avec un navigateur à l’URL suivante, vous voyez la même page que vous avez exécutée précédemment dans une application web Azure, à l’exception que maintenant elle est traitée à partir du CDN.

```
http://<endpointname>.azureedge.net/index.html
```

![Page d’accueil de l’exemple d’application traitée à partir du CDN](media/app-service-web-tutorial-content-delivery-network/sample-app-home-page-cdn.png)

Cela montre qu’Azure CDN a récupéré les ressources de l’application web d’origine et qu’il les traite à partir du point de terminaison CDN. 

Pour vous assurer que cette page est mise en cache dans le CDN, actualisez la page. Deux demandes pour la même ressource sont parfois nécessaires pour que le CDN mette en cache le contenu demandé.

Pour plus d’informations sur la création de profils et de points de terminaison Azure CDN, consultez [Prise en main d’Azure CDN](../cdn/cdn-create-new-endpoint.md).

## <a name="purge-the-cdn"></a>Vidage du CDN

Le CDN actualise régulièrement ses ressources à partir de l’application web d’origine en fonction de la configuration de la durée de vie (TTL). La valeur TTL par défaut est de 7 jours.

Dans certains cas, vous devrez peut-être actualiser le CDN avant l’expiration de la durée de vie ; par exemple, lorsque vous déployez un contenu mis à jour dans l’application web. Pour déclencher une actualisation, vous pouvez vider manuellement les ressources CDN. 

Dans cette section du didacticiel, vous déployez une modification dans l’application web et videz le CDN pour le forcer à actualiser son cache.

### <a name="deploy-a-change-to-the-web-app"></a>Déploiement d’une modification dans l’application web

Ouvrez le fichier `index.html` et ajoutez « - V2 » au titre H1, comme illustré dans l’exemple suivant : 

```
<h1>Azure App Service - Sample Static HTML Site - V2</h1>
```

Validez votre modification et déployez-la dans l’application web.

```bash
git commit -am "version 2"
git push azure master
```

Une fois le déploiement terminé, accédez à l’URL de l’application web pour constater la modification.

```
http://<appname>.azurewebsites.net/index.html
```

![V2 dans le titre de l’application web](media/app-service-web-tutorial-content-delivery-network/v2-in-web-app-title.png)

Accédez à l’URL du point de terminaison CDN pour ouvrir la page d’accueil ; vous ne voyez pas la modification car la version mise en cache dans le CDN n’a pas encore expiré. 

```
http://<endpointname>.azureedge.net/index.html
```

![Pas de V2 dans le titre du CDN](media/app-service-web-tutorial-content-delivery-network/no-v2-in-cdn-title.png)

### <a name="purge-the-cdn-in-the-portal"></a>Vidage du CDN dans le portail

Pour forcer le CDN à mettre à jour sa version mise en cache, videz le CDN.

Dans le portail de navigation gauche, sélectionnez **Groupes de ressources**, puis sélectionnez le groupe de ressources que vous avez créé pour votre application web (myResourceGroup).

![Sélection du groupe de ressources](media/app-service-web-tutorial-content-delivery-network/portal-select-group.png)

Dans la liste des ressources, sélectionnez votre point de terminaison CDN.

![Sélection du point de terminaison](media/app-service-web-tutorial-content-delivery-network/portal-select-endpoint.png)

En haut de la page **Point de terminaison**, cliquez sur **Vider**.

![Sélection de l’option Vider](media/app-service-web-tutorial-content-delivery-network/portal-select-purge.png)

Tapez les chemins d’accès de contenu que vous souhaitez vider. Vous pouvez transmettre un chemin d’accès complet pour vider un fichier individuel ou un segment de chemin d’accès pour vider et actualiser tout le contenu d’un dossier. Étant donné que vous avez modifié `index.html`, vérifiez qu’il s’agit de l’un des chemins d’accès.

En bas de la page, sélectionnez **Vider**.

![Vidage de la page](media/app-service-web-tutorial-content-delivery-network/app-service-web-purge-cdn.png)

### <a name="verify-that-the-cdn-is-updated"></a>Vérification de la mise à jour du CDN

Attendez que le traitement de la demande de vidage soit terminé, généralement quelques minutes. Pour afficher l’état actuel, sélectionnez l’icône représentant une cloche en haut de la page. 

![Notification de vidage](media/app-service-web-tutorial-content-delivery-network/portal-purge-notification.png)

Accédez à la page `index.html` de l’URL du point de terminaison CDN ; à présent, le V2 que vous avez ajouté au titre est affiché sur la page d’accueil. Cela indique que le cache du CDN a été actualisé.

```
http://<endpointname>.azureedge.net/index.html
```

![V2 dans le titre du CDN](media/app-service-web-tutorial-content-delivery-network/v2-in-cdn-title.png)

Pour plus d’informations, consultez [Purger un point de terminaison CDN Azure](../cdn/cdn-purge-endpoint.md). 

## <a name="use-query-strings-to-version-content"></a>Utiliser des chaînes de requête pour contrôler la version du contenu

Le CDN Azure offre les options de comportement de mise en cache suivantes :

* Ignorer les chaînes de requête
* Contourner la mise en cache pour les chaînes de requête
* Mettre en cache chaque URL unique 

La première option est la valeur par défaut, ce qui signifie qu’il n’existe qu’une seule version mise en cache d’une ressource, quelle que soit la chaîne de requête de l’URL qui y accède. 

Dans cette section du didacticiel, vous modifiez le comportement de mise en cache pour mettre en cache chaque URL unique.

### <a name="change-the-cache-behavior"></a>Modification du comportement de mise en cache

Dans la page **Point de terminaison CDN** du portail Azure, sélectionnez **Cache**.

Sélectionnez **Mettre en cache chaque URL unique** dans la liste déroulante **Comportement de mise en cache des chaînes de requête**.

Sélectionnez **Enregistrer**.

![Sélection du comportement de mise en cache de chaîne de requête](media/app-service-web-tutorial-content-delivery-network/portal-select-caching-behavior.png)

### <a name="verify-that-unique-urls-are-cached-separately"></a>Vérification de la mise en cache séparée des URL uniques

Dans un navigateur, accédez à la page d’accueil au niveau du point de terminaison CDN, mais incluez une chaîne de requête : 

```
http://<endpointname>.azureedge.net/index.html?q=1
```

Le CDN retourne le contenu de l’application web actuelle, qui inclut « V2 » dans le titre. 

Pour vous assurer que cette page est mise en cache dans le CDN, actualisez la page. 

Ouvrez `index.html` et remplacez « V2 » par « V3 », puis déployez la modification. 

```bash
git commit -am "version 3"
git push azure master
```

Dans un navigateur, accédez à l’URL du point de terminaison CDN avec une nouvelle chaîne de requête telle que `q=2`. Le CDN obtient le fichier `index.html` actuel et affiche « V3 ».  Mais si vous naviguez vers le point de terminaison CDN avec la chaîne de requête `q=1`, vous voyez « V2 ».

```
http://<endpointname>.azureedge.net/index.html?q=2
```

![V3 dans le titre dans le CDN, chaîne de requête 2](media/app-service-web-tutorial-content-delivery-network/v3-in-cdn-title-qs2.png)

```
http://<endpointname>.azureedge.net/index.html?q=1
```

![V2 dans le titre dans le CDN, chaîne de requête 1](media/app-service-web-tutorial-content-delivery-network/v2-in-cdn-title-qs1.png)

Cette sortie indique que chaque chaîne de requête est traitée différemment : q=1 a été utilisée précédemment, par conséquent les contenus mis en cache sont retournés (V2), tandis que q=2 est nouvelle, et par conséquent, les tout derniers contenus de l’application web sont récupérés et retournés (V3).

Pour plus d’informations, consultez [Contrôle du comportement de mise en cache du CDN Azure avec des chaînes de requête](../cdn/cdn-query-string.md).

## <a name="map-a-custom-domain-to-a-cdn-endpoint"></a>Mappage d’un domaine personnalisé à un point de terminaison CDN

Vous mappez votre domaine personnalisé à votre point de terminaison CDN en créant un enregistrement CNAME. Un enregistrement CNAME est une fonctionnalité DNS qui mappe un domaine source à un domaine cible. Par exemple, vous pouvez mapper `cdn.contoso.com` ou `static.contoso.com` à `contoso.azureedge.net`.

Si vous n’avez pas de domaine personnalisé, suivez le [didacticiel sur le domaine App Service](custom-dns-web-site-buydomains-web-app.md) pour acheter un domaine à l’aide du portail Azure. 

### <a name="find-the-hostname-to-use-with-the-cname"></a>Recherche du nom d’hôte à utiliser avec l’enregistrement CNAME

Dans la page **Point de terminaison** du portail Azure, vérifiez que **Vue d’ensemble** est sélectionné dans le volet de navigation gauche, puis sélectionnez le bouton **+ Domaine personnalisé** en haut de la page.

![Sélection de l’ajout d’un domaine personnalisé](media/app-service-web-tutorial-content-delivery-network/portal-select-add-domain.png)

Dans la page **Ajouter un domaine personnalisé**, vous voyez le nom d’hôte du point de terminaison à utiliser pour la création d’un enregistrement CNAME. Le nom d’hôte est dérivé de votre URL de point de terminaison CDN : **&lt;Nom_Point_Terminaison>.azureedge.net**. 

![Ajout d’une page de domaine](media/app-service-web-tutorial-content-delivery-network/portal-add-domain.png)

### <a name="configure-the-cname-with-your-domain-registrar"></a>Configuration de l’enregistrement CNAME avec votre bureau d’enregistrement de domaines

Accédez au site web de votre bureau d'enregistrement de domaines et recherchez la section de la création d'enregistrements DNS. Vous pourrez trouver ces informations dans une section telle que **Domain Name**, **DNS** ou **Name Server Management**.

Recherchez la section relative à la gestion des enregistrements CNAME. Pour cela, accédez à une page de paramètres avancés et recherchez les mots CNAME, Alias ou Sous-domaines.

Créez un enregistrement CNAME qui mappe votre sous-domaine choisi (par exemple, **static** ou **cdn**) au **nom d’hôte du point de terminaison** affiché précédemment dans le portail. 

### <a name="enter-the-custom-domain-in-azure"></a>Saisie du domaine personnalisé dans Azure

Revenez à la page **Ajouter un domaine personnalisé**, puis entrez votre domaine personnalisé, dont le sous-domaine, dans la boîte de dialogue. Par exemple, entrez : `cdn.contoso.com`.   
   
Azure vérifie que l’enregistrement CNAME existe pour le nom de domaine que vous avez entré. Si l'enregistrement CNAME est correct, votre domaine personnalisé est validé.

La propagation de l’enregistrement CNAME aux serveurs de noms sur Internet peut prendre du temps. Si votre domaine n’est pas validé immédiatement et que vous pensez que l’enregistrement CNAME est correct, patientez quelques minutes, puis réessayez.

### <a name="test-the-custom-domain"></a>Test du domaine personnalisé

Dans un navigateur, accédez au fichier `index.html` à l’aide de votre domaine personnalisé (par exemple, `cdn.contoso.com/index.html`) pour vérifier que le résultat est le même que lorsque vous accédez directement à `<endpointname>azureedge.net/index.html`.

![Page d’accueil d’exemple d’application utilisant l’URL du domaine personnalisé](media/app-service-web-tutorial-content-delivery-network/home-page-custom-domain.png)

Pour plus d’informations, consultez [Mapper du contenu Azure CDN à un domaine personnalisé](../cdn/cdn-map-content-to-custom-domain.md).

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris à :

> [!div class="checklist"]
> * Créer un point de terminaison CDN.
> * Actualiser les ressources mises en cache.
> * Utiliser des chaînes de requête pour contrôler les versions mises en cache.
> * Utiliser un domaine personnalisé pour le point de terminaison CDN.

Apprenez à optimiser les performances du CDN dans les articles suivants.

> [!div class="nextstepaction"]
> [Compression des fichiers dans Azure CDN pour améliorer les performances](../cdn/cdn-improve-performance.md)

> [!div class="nextstepaction"]
> [Préchargement d’éléments multimédias sur un point de terminaison CDN Azure](../cdn/cdn-preload-endpoint.md)


