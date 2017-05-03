---
title: "Ajouter un réseau de distribution de contenu sur un Azure App Service | Microsoft Docs"
description: "Ajoutez un réseau de distribution de contenu sur un Azure App Service pour distribuer vos fichiers statiques à partir des nœuds de périmètre."
services: app-service
author: syntaxc4
ms.author: cfowler
ms.date: 04/03/2017
ms.topic: hero-article
ms.service: app-service-web
manager: erikre
translationtype: Human Translation
ms.sourcegitcommit: 9eafbc2ffc3319cbca9d8933235f87964a98f588
ms.openlocfilehash: 7ba3737566401152a3171e8926beca188045230c
ms.lasthandoff: 04/22/2017

---
# <a name="add-a-content-deliver-network-on-an-azure-app-service"></a>Ajouter un réseau de distribution de contenu sur un Azure App Service

Dans ce didacticiel, vous allez ajouter un réseau de distribution de contenu (CDN, Content Delivery Network) à votre Azure App Service pour exposer le contenu statique sur un serveur Edge. Vous allez créer un profil CDN, qui est une collection de 10 points de terminaison CDN maximum.

Un CDN met en cache le contenu web statique à des emplacements stratégiques afin de fournir un débit maximal pour la distribution de contenu aux utilisateurs. Les avantages de l’utilisation du CDN pour mettre en cache les ressources de site web incluent :

* De meilleures performances et une expérience enrichie pour les utilisateurs finaux, en particulier ceux qui utilisent des applications ayant recours à de nombreux allers-retours pour charger le contenu.
* Une mise à grande échelle pour améliorer la gestion instantanée des charges importantes, par exemple au début de l'événement de lancement d'un produit.
* Si l’on répartit les requêtes utilisateur et que l’on diffuse du contenu à partir des serveurs Edge, le trafic transmis à l’origine est moins important.

> [!TIP]
> Passez en revue la liste à jour des [Emplacements POP des CDN Azure](https://docs.microsoft.com/en-us/azure/cdn/cdn-pop-locations).
>

## <a name="deploy-the-sample"></a>Déployer l’exemple

Pour suivre ce didacticiel, vous avez besoin d’une application déployée dans l’application web. Suivez les instructions de l’article [Créer une application web HTML statique dans Azure en 5 minutes](app-service-web-get-started-html.md) pour commencer avec ce didacticiel.

## <a name="step-1---login-to-azure-portal"></a>Étape 1 : Connectez-vous au portail Azure

Tout d’abord, ouvrez votre navigateur préféré et accédez au [portail](https://portal.azure.com) Azure.

## <a name="step-2---create-a-cdn-profile"></a>Étape 2 : Créez un profil CDN

Cliquez sur le bouton `+ New` situé dans le volet de navigation de gauche, puis cliquez sur **Web et mobilité**. Sous la catégorie Web et mobilité, sélectionnez **CDN**.

Spécifiez les champs suivants :

| Champ | Exemple de valeur | Description |
|---|---|---|
| Nom | myCDNProfile | Un nom pour le profil CDN. |
| Emplacement | Europe de l'Ouest | Il s'agit de l'emplacement Azure où seront stockées les informations de votre profil CDN. Cela n'a aucun impact sur les emplacements de point de terminaison CDN. |
| Groupe de ressources | myResourceGroup | Pour plus d’informations sur les groupes de ressources, consultez [Présentation d’Azure Resource Manager](../azure-resource-manager/resource-group-overview.md#resource-groups). |
| Niveau tarifaire  | Standard Akamai | Consultez la [Vue d’ensemble du réseau de distribution de contenu (CDN) Azure](../cdn/cdn-overview.md#azure-cdn-features) pour obtenir une comparaison des niveaux de tarification. |

Cliquez sur **Create**.

Ouvrez le concentrateur de groupes de ressources à partir de la barre de navigation de gauche, sélectionnez **myResourceGroup**. Dans la liste des ressources, sélectionnez **myCDNProfile**.

![azure-cdn-profile-created](media/app-service-web-tutorial-content-delivery-network/azure-cdn-profile-created.png)

## <a name="step-3---create-a-cdn-endpoint"></a>Étape 3 : Créer un point de terminaison CDN

Cliquez sur **+ Point de terminaison** à partir des commandes à proximité de la zone de recherche ; cette action lance le panneau de création de point de terminaison.

Spécifiez les champs suivants :

| Champ | Exemple de valeur | Description |
|---|---|
| Nom |  | Ce nom servira à accéder à vos ressources mises en cache au niveau du domaine `<endpointname>.azureedge.net`. |
| Type d’origine | Application web | En sélectionnant un type d’origine, vous obtenez des menus contextuels pour les champs restants. En sélectionnant une origine personnalisée, vous obtenez un champ de texte pour votre nom d’hôte d’origine. |
| Nom d’hôte de l’origine | |  La liste déroulante répertorie toutes les origines disponibles du type d’origine spécifié. Si vous avez sélectionné Origine personnalisée comme type d’origine, tapez le domaine de votre origine personnalisée.  |

Cliquez sur **Add**.

Le point de terminaison sera créé, une fois que le point de terminaison du réseau de distribution de contenu est créé le statut sera mis à jour sur **en cours d’exécution**.

![azure-cdn-endpoint-created](media/app-service-web-tutorial-content-delivery-network/azure-cdn-endpoint-created.png)

## <a name="step-4---serve-from-azure-cdn"></a>Étape 4 : Distribuer depuis Azure CDN

Maintenant que le point de terminaison CDN **s’exécute**, vous devez être en mesure d’accéder au contenu à partir de celui-ci.

En imaginant que nous avons utilisé l’article [Créer une application web HTML statique dans Azure en 5 minutes](app-service-web-get-started-html.md) pour commencer ce didacticiel, nous devrions avoir les dossiers suivants disponibles sur notre CDN : `css`, `img`, `js`.

Les chemins d’accès de contenu entre l’URL de l’application web `http://<app_name>.azurewebsites.net/img/` et l’URL du point de terminaison CDN `http://<endpointname>.azureedge.net/img/` sont identiques. Cela signifie que vous pouvez simplement remplacer le domaine du point de terminaison CDN par n’importe quel contenu statique pour qu’il puisse être distribué depuis le CDN.

Commençons par extraire notre première image depuis le point de terminaison CDN en accédant à l’URL suivante dans votre navigateur web préféré :

```bash
http://<endpointname>.azureedge.net/img/03-enterprise.png
```

Maintenant que le contenu statique est disponible sur le CDN, vous pouvez mettre à jour votre application pour qu’elle utilise le point de terminaison CDN afin de distribuer le contenu à l’utilisateur final.

Selon le langage utilisé pour créer votre site, de nombreuses infrastructures peuvent exister pour vous aider avec la restauration automatique CDN. Par exemple, ASP.NET fournit le support pour le [regroupement et la minimisation](https://docs.microsoft.com/en-us/aspnet/mvc/overview/performance/bundling-and-minification#using-a-cdn), qui active également les fonctionnalités de restauration automatique CDN.

Si votre langage n’a pas de support intégré ou de bibliothèque pour la restauration automatique CDN, vous pouvez utiliser une infrastructure Javascript telle que [FallbackJS](http://fallback.io/), qui prend en charge le chargement de [scripts](https://github.com/dolox/fallback/tree/master/examples/loading-scripts), de [feuilles de style](https://github.com/dolox/fallback/tree/master/examples/loading-stylesheets) et [d’images](https://github.com/dolox/fallback/tree/master/examples/loading-images).

## <a name="step-5---purge-the-cdn"></a>Étape 5 : Vider le CDN

Il peut parfois être nécessaire de forcer un vidage du CDN si vous souhaitez faire expirer le contenu avant la fin de la durée de vie.

Il est possible de vider manuellement Azure CDN, à partir du panneau du profil CDN ou du panneau du point de terminaison CDN. Si vous choisissez de vider le CDN à partir de sa page de profil, vous devrez sélectionner le point de terminaison à vider.

Pour vider le contenu, tapez les chemins d’accès de contenu que vous souhaitez vider. Vous pouvez transmettre un chemin d’accès complet pour vider un fichier individuel ou un segment de chemin d’accès pour vider et actualiser le contenu d’un dossier particulier.

Une fois que vous avez fourni tous les chemins de contenu à vider, cliquez sur **Vider**.

![app-service-web-vider-cdn](media/app-service-web-tutorial-content-delivery-network/app-service-web-purge-cdn.png)

## <a name="step-6---map-a-custom-domain"></a>Étape 6 : Mapper un domaine personnalisé

En mappant un domaine personnalisé à votre point de terminaison CDN, vous obtenez un domaine uniforme pour votre application web.

Pour mapper un domaine personnalisé à votre point de terminaison CDN, créez un enregistrement CNAME auprès de votre bureau d’enregistrement de domaines.

> [!NOTE]
> Un enregistrement CNAME est une fonctionnalité DNS qui mappe un domaine source, comme `www.contosocdn.com` ou `static.contosocdn.com`, à un domaine cible.

Ici, nous allons ajouter un domaine source `static.contosocdn.com`, qui pointera vers le domaine de destination, c’est-à-dire le point de terminaison CDN.

| domaine source | domaine de destination |
|---|---|
| static.contosocdn.com | &lt;nompointdeterminaison&gt;.azureedge.net |

Dans le panneau de vue d’ensemble du point de terminaison CDN, cliquez sur le bouton `+ Custom domain`.

Dans le panneau Ajouter un domaine personnalisé, entrez votre domaine personnalisé, y compris le sous-domaine, dans la boîte de dialogue. Par exemple, entrez le nom de domaine au format `static.contosocdn.com`.

Cliquez sur **Add**.

## <a name="step-7---version-content"></a>Étape 7 : Contrôler la version du contenu

Dans le volet de navigation de gauche du point de terminaison CDN, sélectionnez **Cache** sous Paramètres.

Le panneau **Cache** vous permet de configurer la façon dont le CDN traite les chaînes de requête dans la demande.

> [!NOTE]
> Pour obtenir une description du comportement de mise en cache des chaînes de requête, consultez l’article [Contrôle du comportement de mise en cache du CDN Azure avec des chaînes de requête](../cdn/cdn-query-string.md).

Sélectionnez **Mettre en cache chaque URL unique** dans la liste déroulante pour le comportement de mise en cache de chaîne de requête.

Cliquez sur **save**.

## <a name="next-steps"></a>Étapes suivantes

* [Content Delivery Network](../best-practices-cdn.md?toc=%2fazure%2fcdn%2ftoc.json) (Réseau de distribution de contenu)
* [Activer HTTPS sur un domaine personnalisé Azure CDN](../cdn/cdn-custom-ssl.md)
* [Compression des fichiers dans Azure CDN pour améliorer les performances](../cdn/cdn-improve-performance.md)
* [Préchargement d’éléments multimédias sur un point de terminaison CDN Azure](../cdn/cdn-preload-endpoint.md)

