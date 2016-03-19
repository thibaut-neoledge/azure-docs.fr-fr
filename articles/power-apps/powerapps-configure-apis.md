<properties
	pageTitle="Modifier ou mettre à jour les propriétés de votre API PowerApps dans le portail Azure | Microsoft Azure"
	description="Ajouter une icône personnalisée, mettre à jour la stratégie XML ou mettre à jour la définition Swagger de votre API PowerApps"
	services=""
    suite="powerapps"
	documentationCenter="" 
	authors="MandiOhlinger"
	manager="dwrede"
	editor=""/>

<tags
   ms.service="powerapps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="11/25/2015"
   ms.author="guayan"/>

# Mettre à jour une API existante et ses propriétés

L'API que vous inscrivez dans l'environnement App Service est essentiellement un proxy vers votre service principal. Une fois que vous créez l'API, vous pouvez modifier ses propriétés. Par exemple, vous pouvez :

- Ajouter une icône personnalisée pour votre API.
- Modifier la méthode de sécurisation du serveur principal utilisé par l'API. 
- Mettre à jour le nom d’affichage de votre API avec un nom plus convivial.


#### Conditions préalables

- Inscrivez-vous à [PowerApps Enterprise](powerapps-get-started-azure-portal.md).
- Créez un [environnement App Service](powerapps-get-started-azure-portal.md).
- Inscrivez une [API](powerapps-register-from-available-apis) dans votre environnement.

## Ajouter une icône personnalisée ou un nom d’affichage convivial

1. Sur le [portail Azure](https://portal.azure.com), ouvrez le panneau de votre API.
2. Sélectionnez **TOUS LES PARAMÈTRES**.
3. Dans **PARAMÈTRES**, sélectionnez **GÉNÉRAL** : ![][11]

Dans Général, vous pouvez modifier les paramètres suivants :

Paramètre | Description
--- | ---
Nom complet | Ce nom est utilisé lors de l'énumération des *connexions disponibles* dans PowerApps. Par défaut, il utilise le nom de la ressource de l'API, qui peut ne pas être le nom le plus adapté à vos utilisateurs PowerApps. Vous pouvez entrer un nom d’affichage plus convivial. Par exemple, vous pouvez le nommer **Nouvelles commandes client** ou **Afficher l'historique des ventes**.  
URL de l'icône | Vous pouvez ajouter une icône personnalisée pour votre API. L'icône est utilisée lors de l'énumération des *connexions disponibles* et de *mes connexions* dans PowerApps. Par défaut, l'icône suivante est utilisée : <br/><br/>![][12] <br/><br/>Lors de l'utilisation d'une icône personnalisée :<br/><ul><li>L'URL vers l'icône doit être accessible au public.</li><li>Il peut s’agir d’un fichier .png ou .svg. Lorsque vous utilisez un fichier .png, sa taille recommandée est de 40 x 40 pixels.</li></ul>
Schéma d'URL | Choisissez le ou les schémas que votre API prendra en charge. Vous pouvez choisir **HTTP**, **HTTPS** ou **HTTP et HTTPS**. Par défaut, HTTP et HTTPS sont activés. <br/><br/>L'environnement App Service configure automatiquement le schéma selon la configuration du serveur principal. Si vous devez configurer d’autres éléments, vous pouvez développer ou modifier votre service principal. 
Authentifier avec le service principal | Après avoir inscrit votre service principal dans l'environnement App Service, il est judicieux de sécuriser le serveur principal afin que les clients ne puissent l’appeler qu’en utilisant votre API. Selon l’emplacement où votre serveur principal est déployé, les options suivantes sont disponibles :<br/><br/><ul><li><strong>ACCESSIBLE UNIQUEMENT VIA CETTE API</strong> : cette option est disponible uniquement lorsque votre serveur principal est déployé dans l'environnement App Service. Lorsque cette option est sélectionnée, elle désactive tout nom d'hôte sur votre serveur principal. Comme le proxy de l'API est également en cours d'exécution dans le même environnement App Service, il peut toujours accéder à votre serveur principal.</li><li><strong>AUTHENTIFICATION DE BASE HTTP</strong> : cette option n'est disponible que lorsque votre serveur principal est déployé. Lorsque cette option est sélectionnée, entrez un nom d'utilisateur et un mot de passe. Lorsque le proxy appelle votre serveur principal, il utilise l'authentification de base HTTP pour transmettre le nom d'utilisateur et le mot de passe dans l'en-tête d'autorisation HTTP. Enfin, votre service principal doit confirmer (authentifier) le nom d'utilisateur et le mot de passe entrés.<br/><br/>Pour en savoir plus sur l'implémentation de l'authentification de base HTTP dans ASP.NET Web API 2, consultez [Filtres d'authentification dans ASP.NET Web API 2](http://www.asp.net/web-api/overview/security/authentication-filters).</li></ul>


## Mise à jour du Swagger de votre API

1. Sur le [portail Azure](https://portal.azure.com), ouvrez le panneau de votre API.
2. Sélectionnez **TOUS LES PARAMÈTRES**.
3. Dans **PARAMÈTRES**, sélectionnez **DÉFINITION DE L'API** : ![][13]

**Swagger 2.0** est le format de définition de l'API pris en charge. La définition actuelle d'API figure dans l'éditeur JSON incorporé. Vous pouvez la modifier en ligne ou télécharger un nouveau fichier JSON. Après avoir **enregistré** vos modifications, toutes les erreurs apparaissent dans ce panneau, y compris les erreurs liées à la définition de l’API.

- Pour en savoir plus sur Swagger 2.0, consultez le [site Web officiel de Swagger](http://swagger.io).
- Pour plus d'informations sur la façon d’obtenir Swagger 2.0 lorsque vous développez votre API, consultez :  
	- [Créer une application API ASP.NET dans Azure App Service](../app-service-dotnet-create-api-app.md)
	- [Créer et déployer une application API Java dans Azure App Service](../app-service-api-java-api-app.md)
	- [Créer et déployer une application API Node.js dans Azure App Service](../app-service-api-nodejs-api-app.md)
	- [Personnaliser des définitions d’API générées par Swashbuckle](../app-service-api-dotnet-swashbuckle-customize.md)
- Pour en savoir plus sur les meilleures pratiques d'utilisation de Swagger 2.0 pour PowerApps, consultez [Développer une API pour PowerApps](powerapps-develop-api.md).

## Mettre à jour la stratégie XML de votre API

1. Sur le [portail Azure](https://portal.azure.com), ouvrez le panneau de votre API.
2. Sélectionnez **TOUS LES PARAMÈTRES**.
3. Dans **PARAMÈTRES**, sélectionnez **STRATÉGIE** : ![][14]

Cette stratégie est la même stratégie prise en charge par [Gestion des API Azure](https://azure.microsoft.com/services/api-management/). La stratégie actuelle figure dans l'éditeur XML incorporé. Vous pouvez la modifier en ligne ou télécharger un nouveau fichier XML. Après avoir **enregistré** vos modifications, toutes les erreurs apparaissent dans ce panneau, y compris les problèmes liés à la stratégie de l'API.

La rubrique[Stratégies dans Gestion des API Azure](../api-management-howto-policies.md) constitue une bonne ressource pour en savoir plus sur la configuration et la signification des stratégies.


## Résumé et étapes suivantes
Après avoir créé votre API, vous pouvez utiliser les étapes de cette rubrique pour modifier ses paramètres et même en personnaliser certains.

Pour en savoir plus sur PowerApps, consultez les rubriques et ressources suivantes :

- [Configurer une API pour se connecter à un serveur principal AAD protégé](powerapps-configure-apis-aad.md)
- [Développer une API pour PowerApps](powerapps-develop-api.md)

[11]: ./media/powerapps-configure-apis/api-settings-general.png
[12]: ./media/powerapps-configure-apis/api-default-icon.png
[13]: ./media/powerapps-configure-apis/api-settings-api-definition.png
[14]: ./media/powerapps-configure-apis/api-settings-policy.png

<!---HONumber=AcomDC_0128_2016-->