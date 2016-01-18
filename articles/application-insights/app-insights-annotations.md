<properties
    pageTitle="Annotations de déploiement pour Application Insights | Microsoft Azure"
    description="Ajouter des marqueurs déploiement ou de build aux graphiques Metrics Explorer dans Application Insights."
    services="application-insights"
    documentationCenter=".net"
    authors="alancameronwills"
    manager="douge"/>

<tags
    ms.service="application-insights"
    ms.workload="tbd"
    ms.tgt_pltfrm="ibiza"
    ms.devlang="na"
    ms.topic="article"
	ms.date="01/05/2016"
    ms.author="awills"/>

# Annotations de version dans Application Insights

Des annotations de version sur les graphiques [Metrics Explorer](app-insights-metrics-explorer.md) montrent les endroits où vous avez déployé une nouvelle édition. Elles vous permettent de mieux voir l’effet de vos modifications sur les performances de votre application. Elles peuvent être créées de manière automatique par le [système de génération Visual Studio Team Services](https://www.visualstudio.com/fr-FR/get-started/build/build-your-app-vs).

![Exemples d’annotations avec corrélation visible avec le délai de réponse de serveur](./media/app-insights-annotations/00.png)

Les annotations de version sont une fonctionnalité de la build sur le cloud et le service de version de Visual Studio Team Services.

## Installer l’extension Annotations (une fois)

Pour être en mesure de créer des annotations de version, vous devez installer l’une des nombreuses extensions Team Service disponibles dans Visual Studio Marketplace.

1. Connectez-vous à [Visual Studio Team Services](https://www.visualstudio.com/fr-FR/get-started/setup/sign-up-for-visual-studio-online).
2. Ouvrez Visual Studio Marketplace, recherchez l’extension Application Insights Annotations et ajoutez-la à votre compte Team Services.

![En haut à droite de la page web de Team Services, ouvrez Marketplace. Recherchez et installez Application Insights Annotations dans votre compte.](./media/app-insights-annotations/10.png)

Vous devez procéder ainsi une seule fois pour votre compte Visual Studio Team Services. Des annotations de version peuvent désormais être configurées pour n’importe quel projet de votre compte.



## Ajouter une tâche d’annotation à votre modèle de version

Vous devez effectuer cette opération pour chaque modèle de version dans lequel vous souhaitez créer des annotations de version.

Ouvrez (ou créez) le modèle de version qui gère vos déploiements à partir de Visual Studio Team Services.

Ajoutez une tâche, sélectionnez la tâche d’annotation de version d’Application Insights à partir du menu.

![En haut à droite de la page web de Team Services, ouvrez Marketplace. Recherchez et installez Application Insights Annotations dans votre compte.](./media/app-insights-annotations/40.png)

Pour achever cette opération, vous aurez besoin de certains détails de la ressource Application Insights qui vous permettent d’analyser votre application.

Gardez la fenêtre Team Services ouverte pendant que vous récupérerez les détails dans Application Insights Team Services.

## Copiez la clé d’API à partir d’Application Insights.

Dans une autre fenêtre de navigateur :

1. Connectez-vous au [portail Microsoft Azure](https://portal.azure.com) et ouvrez la ressource Application Insights qui contrôle votre application. (Ou [créez-en une maintenant](app-insights-overview.md), si vous ne l’avez pas encore fait.)
2. Ouvrez la liste déroulante **Essentials** et copiez l’ID d’abonnement, le groupe de ressources et le nom de la ressource dans la tâche d’annotation de version. ![](./media/app-insights-annotations/50.png)
2. Ouvrez **Paramètres**, **Clé d’API** et créez une nouvelle clé. Copiez ces éléments. ![](./media/app-insights-annotations/30.png)

Enfin, **Enregistrer** la définition de version.

## Marqueurs de déploiement

Désormais, lorsque vous utilisez le modèle de version pour déployer une nouvelle version, une annotation est envoyée à Application Insights. Les annotations figureront sur les graphiques Metrics Explorer.

<!---HONumber=AcomDC_0107_2016-->