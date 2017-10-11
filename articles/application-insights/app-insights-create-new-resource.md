---
title: "Créer une ressource Azure Application Insights | Microsoft Docs"
description: "Configurez manuellement la surveillance d’Application Insights pour une nouvelle application en direct."
services: application-insights
documentationcenter: 
author: CFreemanwa
manager: carmonm
ms.assetid: 878b007e-161c-4e36-8ab2-3d7047d8a92d
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 12/02/2016
ms.author: bwren
ms.openlocfilehash: 5f8814ee943424c1c278ab3732129d4459f83819
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/18/2017
---
# <a name="create-an-application-insights-resource"></a>Création d’une ressource Application Insights dans Azure
Azure Application Insights affiche les données relatives à votre application dans une *ressource* Microsoft Azure. La création d’une nouvelle ressource fait, par conséquent, partie de la [configuration d’Application Insights pour surveiller une nouvelle application][start]. Dans de nombreux cas, il est possible de créer une ressource automatiquement à l’aide de l’IDE. Mais dans certains cas, vous créez manuellement une ressource : par exemple, pour avoir des ressources distinctes pour le développement et la production des builds de votre application.

Après avoir créé la ressource, vous obtenez sa clé d’instrumentation et l’utilisez pour configurer le Kit SDK dans l’application. La clé de ressource établit un lien entre la télémétrie et la ressource.

## <a name="sign-up-to-microsoft-azure"></a>S’inscrire à Microsoft Azure
Si vous n’avez pas de [compte Microsoft, procurez-vous en un dès maintenant](http://live.com). (Si elle utilise des services comme Outlook.com, OneDrive, Windows Phone ou XBox Live, elle dispose d’un compte Microsoft.)

Vous devez également vous abonner à [Microsoft Azure](http://azure.com). Si votre équipe ou votre organisation dispose d’un abonnement Azure, le propriétaire peut vous y ajouter à l’aide de votre Windows Live ID. Vous êtes facturé uniquement pour ce que vous utilisez. Le plan de base (par défaut) vous permet d’utiliser gratuitement une partie des fonctionnalités, à titre d’évaluation.

Dès que vous êtes abonné, connectez-vous à Application Insights à l’adresse [http://portal.azure.com](https://portal.azure.com), puis utilisez votre Live ID pour vous connecter.

## <a name="create-an-application-insights-resource"></a>Création d’une ressource Application Insights dans Azure
Dans le portail [portal.azure.com](https://portal.azure.com), ajoutez une ressource Application Insights :

![Cliquez sur Nouveau > Application Insights](./media/app-insights-create-new-resource/01-new.png)

* **type d’application** définit le contenu du panneau de présentation et les propriétés disponibles dans [Metrics Explorer][metrics]. Si vous ne voyez pas votre type d’application, choisissez Général.
* **Abonnement** est votre compte de paiement dans Azure.
* **Groupe de ressources** facilite la gestion des propriétés telles que le contrôle d’accès. Si vous avez déjà créé d’autres ressources Azure, vous pouvez choisir de placer cette nouvelle ressource dans le même groupe.
* **Emplacement** correspond à l’endroit où nous conservons vos données.
* **Épingler au tableau de bord** place une vignette d’accès rapide à votre ressource sur votre page d’accueil Azure. Recommandé.

Une fois votre application créée, un nouveau panneau s’ouvre. Dans ce panneau, vous trouverez des données relatives à l’utilisation et aux performances de votre application. 

Pour y revenir lors de votre prochaine connexion à Azure, recherchez la vignette d’accès rapide à votre application située dans le panneau de démarrage (écran d’accueil). Sinon, cliquez sur Parcourir.

## <a name="copy-the-instrumentation-key"></a>Copie de la clé d’instrumentation
La clé d'instrumentation identifie la ressource que vous avez créée. Vous devez la fournir au SDK.

![Cliquez sur Essentials, sur la clé d'instrumentation, puis appuyez sur CTRL+C](./media/app-insights-create-new-resource/02-props.png)

## <a name="install-the-sdk-in-your-app"></a>Installation du Kit SDK dans votre application
Installez le Kit SDK Application Insights dans votre application. Cette étape repose en grande partie sur votre type d’application. 

La clé d’instrumentation permet de configurer le [kit de développement logiciel (SDK) que vous avez installé dans votre application][start].

Le SDK inclut des modules standard qui envoient des données de télémétrie sans avoir à écrire du code. Pour suivre les actions des utilisateurs ou diagnostiquer des problèmes plus en détail, [utilisez l'API][api] pour envoyer votre propre télémétrie.

## <a name="monitor"></a>Reportez-vous aux données de télémétrie
Fermez le panneau de démarrage rapide pour revenir au panneau de votre application dans le portail Azure.

Cliquez sur la vignette de recherche pour afficher [Recherche de diagnostic][diagnostic], où s’affichent les premiers événements. 

Après quelques secondes, cliquez sur **Actualiser** pour obtenir des données supplémentaires.

## <a name="creating-a-resource-automatically"></a>Création automatique d’une ressource
Vous pouvez écrire un [script PowerShell](app-insights-powershell.md) pour créer automatiquement une ressource.

## <a name="next-steps"></a>Étapes suivantes
* [Création d’un tableau de bord](app-insights-dashboards.md)
* [Recherche de diagnostic](app-insights-diagnostic-search.md)
* [Exploration des mesures](app-insights-metrics-explorer.md)
* [Écriture de requêtes Analytics](app-insights-analytics.md)

<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[diagnostic]: app-insights-diagnostic-search.md
[metrics]: app-insights-metrics-explorer.md
[start]: app-insights-overview.md

