---
title: "Gérer votre première API dans Gestion des API Azure | Microsoft Docs"
description: "Découvrez comment créer des API et ajouter des opérations et comment prendre en main Gestion des API."
services: api-management
documentationcenter: 
author: steved0x
manager: erikre
editor: 
ms.assetid: 51b7df8b-1c43-43c6-90c9-0aa24f48206b
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 12/15/2016
ms.author: apimpm
ms.openlocfilehash: 6e76d1ee08f804637999ef2ebf5d25becf6a0408
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="manage-your-first-api-in-azure-api-management"></a>Gérer votre première API dans Gestion des API Azure
## <a name="overview"></a>Vue d’ensemble
Ce guide décrit la prise en main rapide de la gestion des API Azure et la création de votre premier appel d’API.

## <a name="concepts"></a>Qu’est-ce que Gestion des API Azure ?
Vous pouvez utiliser la gestion des API Azure pour prendre n’importe quel serveur principal et lancer un programme d’API à part entière qui repose sur ce dernier.

Scénarios courants :

* **Sécurisation d’infrastructures mobiles** par régulation de l’accès à l’aide de clés d’API pour éviter les attaques par déni de service (DOS) en utilisant la limitation ou des stratégies de sécurité avancées, telles que la validation de jeton JWT
* **Activation d’écosystèmes de partenaires ISV** en proposant une intégration rapide des partenaires via le portail des développeurs et la création d’une façade d’API afin de se dissocier des implémentations internes qui ne sont pas appropriées à la consommation des partenaires.
* **Exécution d’un programme d’API interne** en offrant un emplacement centralisé à l’organisation pour communiquer sur la disponibilité et les dernières modifications apportées aux API, tout en régulant l’accès basé sur des comptes professionnels, tous basés sur un canal sécurisé entre la passerelle de l’API et le serveur principal.

Le système est constitué des composants suivants :

* La **passerelle d’API** est le point de terminaison qui :
  
  * accepte les appels d’API et les dirige vers vos serveurs principaux ;
  * vérifie les clés d’API, les jetons JWT, les certificats et les autres informations d’identification ;
  * applique des quotas d’utilisation et des limites de débit ;
  * transforme votre API à la volée sans modification de code ;
  * met en cache les réponses du serveur principal lorsqu’il est configuré ;
  * enregistre les métadonnées relatives aux appels à des fins d’analyse.
* Le **portail des éditeurs** est l’interface d’administration où vous configurez votre programme d’API. Utilisez-le pour :
  
  * définir ou importer le schéma d’API ;
  * intégrer des API aux produits sous forme de packages ;
  * définir des stratégies, telles que des quotas ou des transformations sur les API ;
  * obtenir des informations issues de l’analyse ;
  * gérer les utilisateurs.
* Le **portail des développeurs** est le principal lieu sur le web où les développeurs peuvent :
  
  * lire la documentation de l’API ;
  * essayer une API via la console interactive ;
  * créer un compte et s’abonner pour obtenir les clés d’une API ;
  * accéder aux analyses relatives à leur propre utilisation.

## <a name="create-service-instance"></a>Création d’une instance du service Gestion des API
> [!NOTE]
> Pour suivre ce didacticiel, vous avez besoin d'un compte Azure. Si vous ne possédez pas de compte, vous pouvez créer un compte gratuit en quelques minutes. Pour plus d’informations, consultez la rubrique [Version d’évaluation gratuite d’Azure][Azure Free Trial].
> 
> 

La première étape de travail avec Gestion des API consiste à créer une instance de service. Connectez-vous au [Portail Azure][Azure Portal] et cliquez sur **Nouveau**, **Web + mobile**, **Gestion des API**.

![Nouvelle instance Gestion des API][api-management-create-instance-menu]

Pour le **nom**, spécifiez un nom de sous-domaine unique à utiliser pour l’URL du service.

Choisissez **l’abonnement**, le **groupe de ressources** et **l’emplacement** souhaités pour votre instance de service.

Entrez **Contoso Ltd.** comme **nom de l’organisation**, ainsi que votre adresse de messagerie dans le champ **Adresse de messagerie de l’administrateur**.

> [!NOTE]
> Cette adresse de messagerie est utilisée pour les notifications provenant du système Gestion des API. Pour plus d’informations, consultez [Configuration des notifications et des modèles de messages électroniques dans Gestion des API Azure][How to configure notifications and email templates in Azure API Management].
> 
> 

![Nouveau service Gestion des API][api-management-create-instance-step1]

Les instances du service Gestion des API sont disponibles dans trois niveaux : Développeur, Standard et Premium.

> [!NOTE]
> Le niveau Développeur est réservé aux programmes d’API de développement, de test et pilote pour lesquels la haute disponibilité n’est pas un problème. Aux niveaux Standard et Premium, vous pouvez étendre le nombre d’unités réservées pour gérer davantage de trafic. Les niveaux Standard et Premium permettent à votre service Gestion des API de disposer de la puissance de traitement et des performances maximales. Vous pouvez effectuer ce didacticiel à l’aide de n’importe quel niveau. Pour en savoir plus sur les niveaux du service Gestion des API, consultez la page relative à la [tarification du service Gestion des API][API Management pricing].
> 
> 

Cliquez sur **Créer** pour démarrer la configuration de votre instance de service.

![Nouveau service Gestion des API][api-management-instance-created]

Une fois l’instance de service créée, l’étape suivante consiste à créer ou à importer une API.

## <a name="create-api"></a>Importation d’une API
Une API se compose d’un ensemble d’opérations pouvant être appelées à partir d’une application cliente. Les opérations de l’API sont transmises par proxy aux services web existants.

Il est possible de créer des API (et d’ajouter des opérations) manuellement ou de les importer. Dans ce didacticiel, nous allons importer l’API pour un exemple de service Web de calculatrice fourni par Microsoft et hébergé sur Azure.

> [!NOTE]
> Pour plus d’informations sur la création d’une API et l’ajout manuel d’opérations, consultez les rubriques [Création d’API](api-management-howto-create-apis.md) et [Ajout d’opérations à une API](api-management-howto-add-operations.md).
> 
> 

Les API sont configurées à partir du portail des éditeurs. Pour y accéder, cliquez sur **Portail des éditeurs** à partir de la barre d’outils de services.

![Portail des éditeurs][api-management-management-console]

Pour importer l’API de calculatrice, cliquez sur **API** dans le menu **Gestion des API** à gauche, puis sur **Importer l’API**.

![Bouton Importer l’API][api-management-import-api]

Procédez comme suit pour configurer l’API de calculatrice :

1. Cliquez sur **À partir d’une URL**, entrez **http://calcapi.cloudapp.net/calcapi.json** dans la zone de texte **URL du document de spécification**, et cliquez sur la case d’option **Swagger**.
2. Entrez **calc** dans la zone de texte **Suffixe de l’URL d’API web**.
3. Cliquez dans la zone **Produits (facultatif)** et choisissez **Starter**.
4. Cliquez sur **Enregistrer** pour importer l’API.

![Ajouter une nouvelle API][api-management-import-new-api]

> [!NOTE]
> **Gestion des API** prend actuellement en charge les versions 1.2 et 2.0 du document Swagger dans le cadre d’une importation. Même si la [spécification Swagger 2.0](http://swagger.io/specification) indique que les propriétés `host`, `basePath` et `schemes` sont facultatives, votre document Swagger 2.0 **DOIT** contenir ces propriétés ; dans le cas contraire, l’importation échouera. 
> 
> 

Une fois l’API importée, la page Résumé de l’API s’affiche dans le portail des éditeurs.

![Résumé des API][api-management-imported-api-summary]

La section API comporte plusieurs onglets. L’onglet **Résumé** affiche les mesures de base et les informations concernant l’API. L’onglet [Paramètres](api-management-howto-create-apis.md#configure-api-settings) permet d’afficher et de modifier la configuration d’une API. L’onglet [Opérations](api-management-howto-add-operations.md) permet de gérer les opérations de l’API. L’onglet **Sécurité** permet de configurer l’authentification de la passerelle pour le serveur principal à l’aide de l’authentification de base ou de [l’authentification mutuelle des certificats](api-management-howto-mutual-certificates.md) et de configurer l’[autorisation de l’utilisateur à l’aide d’OAuth 2.0](api-management-howto-oauth2.md).  L’onglet **Problèmes** est utilisé pour afficher les problèmes signalés par les développeurs qui utilisent votre API. L’onglet **Produits** permet de configurer les produits qui contiennent cette API.

Par défaut, chaque instance Gestion des API est fournie avec deux exemples de produits :

* **Starter**
* **Illimité**

Dans ce didacticiel, l’API de calculatrice de base a été ajoutée au produit Starter lors de l’importation de l’API.

Pour créer des appels à une API, les développeurs doivent commencer par s’abonner à un produit qui leur permet d’y accéder. Ils peuvent s’abonner aux produits dans le portail des développeurs,ou les administrateurs peuvent les y abonner dans le portail des éditeurs. Vous êtes considéré comme un administrateur puisque vous avez créé l’instance Gestion des API lors des étapes précédentes du didacticiel. Vous êtes également abonné à tous les produits par défaut.

## <a name="call-operation"></a>Appel d’une opération à partir du portail des développeurs
Les opérations peuvent être directement appelées depuis le portail des développeurs, qui permet d’afficher et de tester les opérations d’une API. Dans cette étape du didacticiel, vous appellerez l’opération **Ajouter deux entiers** de l’API de calculatrice de base. Cliquez sur **Portail des développeurs** dans le menu en haut à droite du portail des éditeurs.

![Portail des développeurs][api-management-developer-portal-menu]

Cliquez sur **API** dans le menu supérieur, puis sur **Calculatrice de base** pour afficher les opérations disponibles.

![Portail des développeurs][api-management-developer-portal-calc-api]

Vous remarquerez les exemples de descriptions et de paramètres importés avec l’API et les opérations, qui fournissent de la documentation aux développeurs qui utiliseront cette opération. Ces descriptions peuvent également être ajoutées lorsque des opérations sont ajoutées manuellement.

Pour appeler l’opération **Ajouter deux entiers**, cliquez sur **Essayer**.

![Essayer][api-management-developer-portal-calc-api-console]

Vous pouvez entrer des valeurs pour les paramètres ou conserver les valeurs par défaut, puis cliquer sur **Envoyer**.

![HTTP Get][api-management-invoke-get]

Après l’appel d’une opération, le portail des développeurs affiche le **statut de réponse**, les **en-têtes de réponse**, et tout **contenu de la réponse**.

![Réponse][api-management-invoke-get-response]

## <a name="view-analytics"></a>Affichage des analyses
Pour afficher les analyses relatives à Calculatrice de base, revenez au portail des éditeurs en sélectionnant **Gérer** dans le menu en haut à droite du portail des développeurs.

![Gérer][api-management-manage-menu]

La vue par défaut du portail des éditeurs correspond au **tableau de bord**, lequel offre un aperçu de votre instance Gestion des API.

![tableau de bord][api-management-dashboard]

Passez la souris sur le graphique relatif à **Calculatrice de base** pour afficher les mesures spécifiques de l’utilisation de l’API à une période donnée.

> [!NOTE]
> Si vous ne voyez aucune ligne sur votre graphique, revenez au portail des développeurs et créez des appels dans l’API, patientez et revenez au tableau de bord.
> 
> 

Cliquez sur **Afficher les détails** pour afficher la page Résumé de l’API, incluant une version plus importante des mesures affichées.

![Analyse][api-management-mouse-over]

![Résumé][api-management-api-summary-metrics]

Pour des mesures et des rapports détaillés, cliquez sur **Analyse** dans le menu **Gestion des API** à gauche.

![Vue d’ensemble][api-management-analytics-overview]

La section **Analyse** comporte les quatre onglets suivants :

* **Aperçu rapide** présente l’utilisation générale et les mesures d’intégrité ainsi que les produits, les API, les opérations et les développeurs principaux.
* **Utilisation** fournit une présentation détaillée des appels d’API et de la bande passante, y compris une représentation géographique.
* **Intégrité** se concentre sur les codes d'état, les taux de réussite en cache, les temps de réponse et les temps de réponse d'API et de service.
* **Activité** fournit des rapports qui présentent une analyse des activités spécifiques par développeur, produit, API et opération.

## <a name="next-steps"></a>Étapes suivantes
* Découvrez comment [protéger votre API avec des limites de débit](api-management-howto-product-with-rules.md).

[Azure Free Trial]: http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=api_management_hero_a

[Create an API Management instance]: #create-service-instance
[Create an API]: #create-api
[Add an operation]: #add-operation
[Add the new API to a product]: #add-api-to-product
[Subscribe to the product that contains the API]: #subscribe
[Call an operation from the Developer Portal]: #call-operation
[View analytics]: #view-analytics
[Next steps]: #next-steps


[How to manage developer accounts in Azure API Management]: api-management-howto-create-or-invite-developers.md
[Configure API settings]: api-management-howto-create-apis.md#configure-api-settings
[How to configure notifications and email templates in Azure API Management]: api-management-howto-configure-notifications.md
[Responses]: api-management-howto-add-operations.md#responses
[How create and publish a product]: api-management-howto-add-products.md
[API Management pricing]: http://azure.microsoft.com/pricing/details/api-management/

[Azure Portal]: https://portal.azure.com/

[api-management-management-console]: ./media/api-management-get-started/api-management-management-console.png
[api-management-create-instance-menu]: ./media/api-management-get-started/api-management-create-instance-menu.png
[api-management-create-instance-step1]: ./media/api-management-get-started/api-management-create-instance-step1.png
[api-management-create-instance-step2]: ./media/api-management-get-started/api-management-create-instance-step2.png
[api-management-instance-created]: ./media/api-management-get-started/api-management-instance-created.png
[api-management-import-api]: ./media/api-management-get-started/api-management-import-api.png
[api-management-import-new-api]: ./media/api-management-get-started/api-management-import-new-api.png
[api-management-imported-api-summary]: ./media/api-management-get-started/api-management-imported-api-summary.png
[api-management-calc-operations]: ./media/api-management-get-started/api-management-calc-operations.png
[api-management-list-products]: ./media/api-management-get-started/api-management-list-products.png
[api-management-add-api-to-product]: ./media/api-management-get-started/api-management-add-api-to-product.png
[api-management-add-myechoapi-to-product]: ./media/api-management-get-started/api-management-add-myechoapi-to-product.png
[api-management-api-added-to-product]: ./media/api-management-get-started/api-management-api-added-to-product.png
[api-management-developers]: ./media/api-management-get-started/api-management-developers.png
[api-management-add-subscription]: ./media/api-management-get-started/api-management-add-subscription.png
[api-management-add-subscription-window]: ./media/api-management-get-started/api-management-add-subscription-window.png
[api-management-subscription-added]: ./media/api-management-get-started/api-management-subscription-added.png
[api-management-developer-portal-menu]: ./media/api-management-get-started/api-management-developer-portal-menu.png
[api-management-developer-portal-calc-api]: ./media/api-management-get-started/api-management-developer-portal-calc-api.png
[api-management-developer-portal-calc-api-console]: ./media/api-management-get-started/api-management-developer-portal-calc-api-console.png
[api-management-invoke-get]: ./media/api-management-get-started/api-management-invoke-get.png
[api-management-invoke-get-response]: ./media/api-management-get-started/api-management-invoke-get-response.png
[api-management-manage-menu]: ./media/api-management-get-started/api-management-manage-menu.png
[api-management-dashboard]: ./media/api-management-get-started/api-management-dashboard.png

[api-management-add-response]: ./media/api-management-get-started/api-management-add-response.png
[api-management-add-response-window]: ./media/api-management-get-started/api-management-add-response-window.png
[api-management-developer-key]: ./media/api-management-get-started/api-management-developer-key.png
[api-management-mouse-over]: ./media/api-management-get-started/api-management-mouse-over.png
[api-management-api-summary-metrics]: ./media/api-management-get-started/api-management-api-summary-metrics.png
[api-management-analytics-overview]: ./media/api-management-get-started/api-management-analytics-overview.png
[api-management-analytics-usage]: ./media/api-management-get-started/api-management-analytics-usage.png
[api-management-]: ./media/api-management-get-started/api-management-.png
[api-management-]: ./media/api-management-get-started/api-management-.png
