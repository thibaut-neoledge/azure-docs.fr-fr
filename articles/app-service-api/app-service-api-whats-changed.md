<properties
	pageTitle="App Service API Apps : les nouveautés | Microsoft Azure"
	description="Découvrez les nouveautés d’API Apps dans Azure App Service"
	services="app-service\api"
	documentationCenter=".net"
	authors="mohitsriv"
	manager="wpickett"
	editor="tdykstra"/>

<tags
	ms.service="app-service-api"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/29/2016"
	ms.author="mohisri"/>

# App Service API Apps : les nouveautés

À l’occasion de l’événement Connect() de novembre 2015, un certain nombre d’améliorations ont été [annoncées](https://azure.microsoft.com/blog/azure-app-service-updates-november-2015/) pour Azure App Service. Parmi celles-ci figurent les modifications sous-jacentes apportées à API Apps pour assurer une meilleure conformité avec Mobile Apps et Web Apps, réduire le nombre de concepts et améliorer les performances de déploiement et d’exécution. À compter du 30 novembre 2015, les applications API que vous créerez à partir du portail de gestion Azure ou des outils les plus récents reflèteront ces modifications. Cet article décrit ces modifications et explique comment redéployer les applications existantes pour profiter des fonctionnalités.

## Évolution des fonctionnalités
Les principales fonctionnalités d’API Apps (authentification, CORS et métadonnées d’API) ont été directement transférées dans App Service. Du fait de cette évolution, les fonctionnalités sont disponibles dans Web Apps, Mobile Apps et API Apps. En fait, les trois partagent le même type de ressource **Microsoft.Web/sites** dans Resource Manager. La passerelle API Apps n’est plus utile ni fournie avec API Apps. De même, l’utilisation de la Gestion des API Azure s’en trouve simplifiée dans la mesure où seule subsiste la passerelle Gestion des API.

![Vue d’ensemble d’API Apps](./media/app-service-api-whats-changed/api-apps-overview.png)

L’un des principes de conception phares de la mise à jour d’API Apps est la possibilité de fournir une API en l’état, dans le langage de votre choix. Si votre API est déjà déployée en tant qu’application web ou application mobile, vous n’avez pas besoin de redéployer votre application pour tirer parti des nouvelles fonctionnalités. Si vous utilisez actuellement la version préliminaire d’API Apps, vous trouverez des conseils de migration détaillés ci-dessous.

### Authentification
Les fonctionnalités d’authentification clé en main existantes d’API Apps, Mobile Services/Apps et Web Apps ont été unifiées et sont accessibles dans un panneau d’authentification unique Azure App Service dans le portail de gestion. Pour obtenir une présentation des services d’authentification d’App Service, consultez l’article relatif à l’[extension de la fonctionnalité d’authentification/autorisation d’App Service](https://azure.microsoft.com/blog/announcing-app-service-authentication-authorization/).

Pour les scénarios d’API, il existe plusieurs nouveautés utiles :

- **Prise en charge pour une utilisation directe d’Azure Active Directory**, sans que le code client n’ait besoin d’échanger le jeton AAD par un jeton de session : votre client peut simplement inclure les jetons AAD dans l’en-tête d’autorisation, conformément à la spécification de jeton du porteur. Cela signifie aussi qu’aucun Kit de développement logiciel (SDK) propre à App Service n’est nécessaire côté client ou serveur.
- **Accès de service à service ou « interne »** : si vous disposez d’un processus de démon ou de tout autre client qui aurait besoin d’accéder aux API sans interface, vous pouvez demander un jeton en utilisant un principal du service AAD et le passer à App Service pour permettre une authentification avec votre application.
- **Autorisation différée** : il est fréquent que les applications aient des restrictions d’accès qui varient en fonction des parties de l’application. Peut-être souhaitez-vous que certaines API soient disponibles publiquement et que d’autres nécessitent une connexion ? Avec la fonctionnalité d’authentification/autorisation, c’était tout ou rien. En effet, l’ensemble du site exigeait une connexion. Cette option existe toujours, mais vous pouvez aussi autoriser le code de votre application à prendre des décisions d’accès après que l’utilisateur a été authentifié auprès d’App Service.
 
Pour plus d’informations sur les nouvelles fonctions d’authentification dans Azure App Service, consultez la page [Authentification et autorisation pour API Apps dans Azure App Service](app-service-api-authentication.md). Pour plus d’informations sur la migration des applications API existantes à partir du modèle API Apps précédent vers le nouveau, consultez la section [Migration des applications API existantes](#migrating-existing-api-apps) plus loin dans cet article.
 
### CORS
En lieu et place d’un paramètre d’application **MS\_CrossDomainOrigins** séparé par des virgules, il existe désormais dans le portail de gestion Azure un panneau qui permet de configurer la fonctionnalité CORS. Il est aussi possible de la configurer à l’aide des outils Resource Manager, tels que Microsoft Azure PowerShell, CLI ou l’[Explorateur de ressources](https://resources.azure.com/). Définissez la propriété **cors** du type de ressource **Microsoft.Web/sites/config** pour votre ressource **&lt;nom du site&gt/web**. Par exemple :

    {
        "cors": {
            "allowedOrigins": [
                "https://localhost:44300"
            ]
        }
    } 

### Métadonnées des API
Le panneau de définition d’API est maintenant disponible dans Web Apps, Mobile Apps et API Apps. Dans le portail de gestion, vous pouvez spécifier une URL relative ou une URL absolue pointant vers un point de terminaison qui héberge une représentation Swagger 2.0 de votre API. Vous pouvez aussi la configurer à l’aide des outils Resource Manager. Définissez la propriété **apiDefinition** du type de ressource **Microsoft.Web/sites/config** pour votre ressource **&lt;nom du site&gt/web**. Par exemple :

    {
        "apiDefinition":
        {
            "url": "https://myStorageAccount.blob.core.windows.net/swagger/apiDefinition.json"
        }
    }

À ce stade, le point de terminaison des métadonnées doit être accessible publiquement sans authentification pour permettre à de nombreux clients en aval (par exemple, la génération de client API REST Visual Studio et le flux PowerApps « Ajouter une API ») de l’utiliser. Cela signifie que si vous utilisez l’authentification App Service et que vous voulez exposer la définition d’API à partir de votre application proprement dite, vous devez utiliser l’option d’authentification différée décrite précédemment pour que l’itinéraire vers vos métadonnées Swagger soit public.

## Portail de gestion
Le fait de sélectionner **Nouveau > Web + Mobile > Application API** dans le portail a pour effet de créer des applications API qui reflètent les nouvelles fonctionnalités décrites dans l’article. **Parcourir > API Apps** affiche uniquement ces nouvelles applications API. Une fois que vous accédez à une application API, le panneau partage la même disposition et les mêmes fonctionnalités que celles de Web Apps et Mobile Apps. Les seules différences se situent au niveau du contenu du démarrage rapide et de l’ordre des paramètres.

Les applications API existantes (ou les applications API Marketplace créées à partir de Logic Apps) dotées des anciennes fonctionnalités de la version préliminaire restent visibles dans le concepteur Logic Apps et quand vous parcourez l’ensemble des ressources d’un groupe de ressources.

## Visual Studio

La plupart des outils Web Apps fonctionne avec les nouvelles applications API, car ils partagent le même type de ressource **Microsoft.Web/sites** sous-jacent. En revanche, les outils Azure Visual Studio doivent être mis à niveau vers la version 2.8.1 ou une version ultérieure, car ils exposent un certain nombre de fonctionnalités spécifiques aux API. Téléchargez le kit de développement logiciel (SDK) à partir de la [page de téléchargement Azure](https://azure.microsoft.com/downloads/).

Avec la rationalisation des types App Service, la publication est aussi unifiée sous **Publier > Microsoft Azure App Service** :

![Publication API Apps](./media/app-service-api-whats-changed/api-apps-publish.png)

Pour en savoir plus sur le SDK 2.8.1, lisez l’annonce sur [ce billet de blog](https://azure.microsoft.com/blog/announcing-azure-sdk-2-8-1-for-net/).

Vous pouvez aussi importer manuellement le profil de publication à partir du portail de gestion pour activer la publication. Cependant, Cloud Explorer, la génération de code et la sélection/création d’applications API nécessitera le SDK 2.8.1 ou une version ultérieure.

## Migration d’applications API existantes
Si votre API personnalisée a été déployée vers l’ancienne version préliminaire d’API Apps, nous vous demandons de migrer vers le nouveau modèle d’API Apps avant le 31 décembre 2015. L’ancien et le nouveau modèles étant basés sur les API web hébergées dans App Service, la majorité du code existant peut être réutilisée.

### Hébergement et redéploiement
Les étapes de redéploiement sont identiques aux étapes de déploiement d’une API web existante vers App Service. Étapes :

1. Créez une application API vide. Cela est possible dans le portail via Nouveau > Application API, dans Visual Studio à partir de la publication ou à l’aide des outils Resource Manager. Si vous utilisez les outils ou les modèles Resource Manager, attribuez à **type** la valeur **api** pour le type de ressource **Microsoft.Web/sites** de façon à orienter les démarrages rapides et les paramètres du portail de gestion vers les scénarios d’API.
2. Connectez et déployez votre projet vers l’application API en utilisant l’un des mécanismes de déploiement pris en charge par App Service. Pour en savoir plus, lisez la [documentation sur le déploiement d’Azure App Service](../app-service-web/web-sites-deploy.md).
  
### Authentification
Les services d’authentification App Service prennent en charge les mêmes fonctionnalités que celles qui étaient disponibles avec l’ancien modèle API Apps. Si vous utilisez des jetons de session et que vous avez besoin de Kits de développement logiciel (SDK), utilisez les SDK client et serveur suivants :

- Client : [Kit de développement logiciel Client Azure Mobile](http://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/)
- Serveur : [Extension d’authentification .NET Microsoft Azure Mobile App](http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Authentication/)

Si vous utilisiez à la place les SDK alpha App Service, ceux-ci sont maintenant déconseillés :

- Client : [Kit de développement logiciel Microsoft Azure AppService](http://www.nuget.org/packages/Microsoft.Azure.AppService)
- Serveur : [Microsoft.Azure.AppService.ApiApps.Service](http://www.nuget.org/packages/Microsoft.Azure.AppService.ApiApps.Service)

En revanche, aucun SDK spécifique à App Service n’est nécessaire si vous utilisez directement le jeton AAD, en particulier avec Azure Active Directory.

### Accès interne
L’ancien modèle API Apps intégrait un niveau d’accès interne. Cela nécessitait d’utiliser le SDK pour signer les demandes. Comme décrit précédemment, avec le nouveau modèle API Apps, les principaux du service AAD peuvent être utilisés en remplacement de l’authentification de service à service sans avoir à recourir à un SDK spécifique à App Service. Pour en savoir plus, consultez [Authentification du principal du service pour API Apps dans Azure App Service](app-service-api-dotnet-service-principal-auth.md).

### Découverte
L’ancien modèle API Apps proposait des API qui permettaient de découvrir pendant l’exécution les autres applications API qui étaient présentes dans le même groupe de ressources derrière la même passerelle. Cela est particulièrement utile dans les architectures qui implémentent des modèles de microservice. Bien que cette fonctionnalité ne soit pas directement prise en charge, plusieurs options s’offrent à vous :

1. Utilisez l’API Azure Resource Manager pour la découverte.
2. Placez Azure API Management devant vos API hébergées par App Service. Azure API Management sert de façade et peut fournir une URL externe stable même si votre topologie interne change.
3. Créez votre propre application API de découverte et faites en sorte que les autres applications API s’inscrivent auprès de l’application de découverte au démarrage.
4. Au moment du déploiement, renseignez les paramètres d’application de toutes les applications API (et des clients) avec les points de terminaison des autres applications API. Ceci est viable dans les déploiements de modèles et depuis que API Apps vous confère le contrôle de l’URL.

## Utilisation d’API Apps avec Logic Apps

Le nouveau modèle d’API Apps fonctionne bien avec le [schéma Logic Apps version 2015-08-01](../app-service-logic/app-service-logic-schema-2015-08-01.md).

## Étapes suivantes

Pour en savoir plus, lisez les articles de la [section Documentation de l’API Apps](https://azure.microsoft.com/documentation/services/app-service/api/). Ils ont été mis à jour pour prendre en compte le nouveau modèle pour API Apps. Par ailleurs, accédez aux forums pour obtenir des détails ou des conseils supplémentaires sur la migration :

- [Forum MSDN](https://social.msdn.microsoft.com/Forums/fr-FR/home?forum=AzureAPIApps)
- [Dépassement de capacité de la pile](http://stackoverflow.com/questions/tagged/azure-api-apps)

<!---HONumber=AcomDC_0629_2016-->