---
title: "Surveiller votre application web ASP.NET avec Azure Application Insights | Microsoft Docs"
description: Fournit des instructions pour configurer rapidement une application web ASP.NET pour la surveillance avec Application Insights
services: application-insights
keywords: 
author: bwren
ms.author: bwren
ms.date: 09/14/2017
ms.service: application-insights
ms.custom: mvc
ms.topic: quickstart
manager: carmonm
ms.translationtype: HT
ms.sourcegitcommit: a6bba6b3b924564fe7ae16fa1265dd4d93bd6b94
ms.openlocfilehash: 5c7e4f876165752824dab2ab8d0c7ecd44864710
ms.contentlocale: fr-fr
ms.lasthandoff: 09/28/2017

---

# <a name="start-monitoring-your-aspnet-web-application"></a>Démarrer la surveillance de votre application web ASP.NET

Azure Application Insights vous permet de surveiller facilement la disponibilité, les performances et l’utilisation de votre application web.  Vous pouvez aussi rapidement identifier et diagnostiquer les erreurs dans votre application sans attendre qu’un utilisateur ne les signale.  Avec les informations que vous collectez à partir d’Application Insights sur les performances et l’efficacité de votre application, vous pouvez prendre des décisions avisées pour gérer et améliorer votre application.

Ce guide de démarrage rapide montre comment ajouter Application Insights à une application web ASP.NET existante et commencer à analyser les statistiques actives, ce qui représente simplement l’une des méthodes que vous pouvez utiliser pour analyser votre application. Si vous n’avez pas d’application web ASP.NET, vous pouvez en créer une en suivant le [guide de démarrage rapide Créer une application web ASP.NET](../app-service/app-service-web-get-started-dotnet.md).

## <a name="prerequisites"></a>Composants requis
Pour effectuer ce démarrage rapide :

- Installez [Visual Studio 2017](https://www.visualstudio.com/downloads/) avec les charges de travail suivantes :
    - Développement web et ASP.NET
    - Développement Azure


Si vous n’avez pas d’abonnement Azure, créez un compte [gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="enable-application-insights"></a>Activer Application Insights

1. Ouvrez votre projet dans Visual Studio 2017.
2. Sélectionnez **Configurer Application Insights** dans le menu Projet. Visual Studio ajoute le SDK Application Insights à votre application.
3. Cliquez sur **Démarrer gratuitement**, sélectionnez votre plan de facturation par défaut, puis cliquez sur **Inscrire**.

    ![Ajout d’Application Insights à Visual Studio](./media/quick-monitor-portal/add-application-insights.png)

4. Exécutez votre application en sélectionnant **Démarrer le débogage** dans le menu **Déboguer** ou en appuyant sur la touche F5.

## <a name="confirm-app-configuration"></a>Confirmer la configuration de l’application

Application Insights recueille les données de télémétrie pour votre application, quel que soit l’emplacement de son exécution. Suivez les étapes ci-dessous pour lancer l’affichage de ces données.

1. Ouvrez Application Insights en cliquant sur **Projet** -> **Application Insights** -> **Rechercher dans la télémétrie de la session de débogage**.  Vous voyez les données de télémétrie de votre session active.<BR><br>![Données de télémétrie dans Visual Studio](./media/quick-monitor-portal/telemetry-in-vs.png)

2. Cliquez sur la première demande dans la liste (GET Home/Index dans cet exemple) pour afficher les détails de la demande. Notez que le code d’état et le temps de réponse sont tous deux inclus, ainsi que d’autres informations utiles sur la demande.<br><br>![Détails de la réponse dans Visual Studio](media/quick-monitor-portal/request-details.png)

## <a name="start-monitoring-in-the-azure-portal"></a>Démarrer la surveillance dans le portail Azure

Vous pouvez maintenant ouvrir Application Insights dans le portail Azure pour afficher différents détails sur votre application en cours d’exécution.

1. Cliquez avec le bouton droit sur le dossier **Connected Services Application Insights** (Services connectés, Application Insights) dans l’Explorateur de solutions et cliquez sur **Ouvrir le portail Application Insights**.  Vous voyez des informations sur votre application et diverses options.

    ![Plan de l’application](media/quick-monitor-portal/001.png)

2. Cliquez sur **Mise en correspondance d’applications** pour obtenir une présentation visuelle des relations de dépendance entre les composants de votre application.  Chaque composant affiche des indicateurs de performance clés comme la charge, les performances, les échecs et les alertes.

    ![Plan de l’application](media/quick-monitor-portal/application-map.png)

3. Cliquez sur l’icône **Analyse d’application** ![Mise en correspondance d’applications](media/quick-monitor-portal/app-analytics-icon.png) sur l’un des composants de l’application.  Vous ouvrez ainsi **Application Insights - Analyses**, qui fournit un langage de requête enrichi pour l’analyse de toutes les données collectées par Application Insights.  Dans ce cas, une requête est générée et affiche le nombre de demandes sous forme de graphique.  Vous pouvez écrire vos propres requêtes pour analyser d’autres données.

    ![Analyse](media/quick-monitor-portal/analytics.png)

4. Revenez à la page **Overview** (Vue d’ensemble), puis cliquez sur **Flux temps réel**.  Des statistiques actives sur votre application en cours d’exécution s’affichent.  Cela inclut des informations telles que le nombre de demandes entrantes, la durée de ces demandes et les erreurs qui se produisent.  Vous pouvez également examiner les métriques de performances critiques comme la mémoire et le processeur.

    ![Flux temps réel](media/quick-monitor-portal/live-stream.png)

Si vous êtes prêt à héberger votre application dans Azure, vous pouvez la publier maintenant. Suivez les étapes décrites dans le [guide de démarrage rapide Créer une application web ASP.NET](../app-service/app-service-web-get-started-dotnet.md#update-the-app-and-redeploy).

## <a name="next-steps"></a>Étapes suivantes
Dans ce guide de démarrage rapide, vous avez activé votre application pour une surveillance par Azure Application Insights.  Continuez à passer en revue les didacticiels pour apprendre à l’utiliser pour surveiller les statistiques et détecter les problèmes dans votre application.

> [!div class="nextstepaction"]
> [Didacticiels Azure Application Insights](app-insights-tutorial-runtime-exceptions.md)
