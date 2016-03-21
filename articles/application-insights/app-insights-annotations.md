<properties
    pageTitle="Annotations de version pour Application Insights | Microsoft Azure"
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
	ms.date="03/03/2016"
    ms.author="awills"/>

# Annotations de version dans Application Insights

Des annotations de version sur les graphiques [Metrics Explorer](app-insights-metrics-explorer.md) montrent les endroits où vous avez déployé une nouvelle édition. Elles vous permettent de mieux voir l’effet de vos modifications sur les performances de votre application. Elles peuvent être créées de manière automatique par le [système de génération Visual Studio Team Services](https://www.visualstudio.com/fr-FR/get-started/build/build-your-app-vs) et vous pouvez aussi [les créer à partir de PowerShell](#create-annotations-from-powershell).

![Exemples d’annotations avec corrélation visible avec le délai de réponse de serveur](./media/app-insights-annotations/00.png)

Les annotations de version sont une fonctionnalité de la build sur le cloud et le service de version de Visual Studio Team Services.

## Installer l’extension Annotations (une fois)

Pour être en mesure de créer des annotations de version, vous devez installer l’une des nombreuses extensions Team Service disponibles dans Visual Studio Marketplace.

1. Connectez-vous à votre projet [Visual Studio Team Services](https://www.visualstudio.com/fr-FR/get-started/setup/sign-up-for-visual-studio-online).
2. Dans Visual Studio Marketplace, [obtenez l’extension Release Annotations](https://marketplace.visualstudio.com/items/ms-appinsights.appinsightsreleaseannotations) et ajoutez-la à votre compte Team Services.

![En haut à droite de la page web de Team Services, ouvrez Marketplace. Sélectionnez Visual Team Services, puis, sous Build, choisissez Afficher plus.](./media/app-insights-annotations/10.png)

Vous devez procéder ainsi une seule fois pour votre compte Visual Studio Team Services. Des annotations de version peuvent désormais être configurées pour n’importe quel projet de votre compte.

## Obtenir une clé d’API à partir d’Application Insights

Vous devez effectuer cette opération pour chaque modèle de version dans lequel vous souhaitez créer des annotations de version.


1. Connectez-vous au [portail Microsoft Azure](https://portal.azure.com) et ouvrez la ressource Application Insights qui surveille votre application. (Ou [créez-en une maintenant](app-insights-overview.md) si vous ne l’avez pas encore fait.)
2. Ouvrez **Paramètres**, **Accès API** et copiez l’**ID Application Insights**.

    ![Dans portal.azure.com, ouvrez votre ressource Application Insights, puis choisissez Paramètres. Ouvrir Accès API. Copier l’ID de l’application](./media/app-insights-annotations/20.png)

2. Dans une fenêtre de navigateur distincte, ouvrez (ou créez) le modèle de version qui gère vos déploiements à partir de Visual Studio Team Services.

    Ajoutez une tâche, sélectionnez la tâche d’annotation de version d’Application Insights à partir du menu.

    Collez l’**ID de l’application** que vous avez copié à partir du panneau Accès API.

    ![Dans Visual Studio Team Services, ouvrez Version, sélectionnez une définition de version, puis choisissez Modifier. Cliquez sur Ajouter une tâche, puis sélectionnez la tâche Annotation de version Application Insights. Collez l’ID Application Insights.](./media/app-insights-annotations/30.png)

3. Définissez une variable `$(ApiKey)` dans le champ **APIKey**.

4. Dans le panneau Accès API, créez une clé d’API et copiez-la.

    ![Dans le panneau Accès API de la fenêtre Azure, cliquez sur Créer une clé d’API. Fournissez un commentaire, cochez Écrire des annotations, puis cliquez sur Générer une clé. Copiez la nouvelle clé.](./media/app-insights-annotations/40.png)

4. Ouvrez l’onglet Configuration du modèle de version.

    Créez une définition de variable pour `ApiKey`.

    Collez votre clé d’API pour la définition de la variable APIKey.

    ![Dans la fenêtre Team Services, sélectionnez l’onglet Configuration, puis cliquez sur Ajouter une variable. Définissez le nom ApiKey et dans Valeur, collez la clé que vous venez de générer.](./media/app-insights-annotations/50.png)


5. Enfin, **enregistrez** la définition de version.

## Créer des annotations à partir de PowerShell

Vous pouvez également créer des annotations à partir du processus de votre choix (sans utiliser Visual Studio Team System).

Obtenez le [script Powershell à partir de GitHub](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/API/CreateReleaseAnnotation.ps1).

Utilisez-le comme suit :

    .\CreateReleaseAnnotation.ps1 `
      -applicationId "<applicationId>" `
      -apiKey "<apiKey>" `
      -releaseName "<myReleaseName>" `
      -releaseProperties @{
          "ReleaseDescription"="a description";
          "TriggerBy"="My Name" }

Obtenez l’`applicationId` et une `apiKey` à partir de votre ressource Application Insights : ouvrez Paramètres, Accès API et copiez l’ID de l’application. Cliquez ensuite sur Créer une clé d’API, puis copiez la clé.

## Annotations de version

Désormais, lorsque vous utilisez le modèle de version pour déployer une nouvelle version, une annotation est envoyée à Application Insights. Les annotations figureront sur les graphiques Metrics Explorer.

Cliquez sur un marqueur d’annotation pour ouvrir les détails sur la version, notamment le demandeur, la branche de contrôle de code source, la définition de la version, l’environnement et bien plus encore.


![Cliquez sur un marqueur d’annotation de version.](./media/app-insights-annotations/60.png)

<!---------HONumber=AcomDC_0309_2016-->