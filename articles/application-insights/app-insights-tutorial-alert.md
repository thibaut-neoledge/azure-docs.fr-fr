---
title: "Envoyer des alertes à partir d’Azure Application Insights | Microsoft Docs"
description: "Didacticiel pour envoyer des alertes en réponse aux erreurs dans votre application à l’aide d’Azure Application Insights."
keywords: 
author: bwren
ms.author: bwren
ms.date: 09/20/2017
ms.service: application-insights
ms.custom: mvc
ms.topic: tutorial
manager: carmonm
ms.openlocfilehash: ce297476cfdf80564c6c0cb835955a146f7a1c12
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="monitor-and-alert-on-application-health-with-azure-application-insights"></a>Surveiller et alerter sur l’intégrité des applications avec Azure Application Insights

Azure Application Insights vous permet de surveiller votre application et d’envoyer des alertes lorsqu’elle est indisponible, connaît des défaillances ou rencontre des problèmes de performances.  Ce didacticiel vous guide dans le processus de création de tests permettant de vérifier en permanence la disponibilité de votre application et d’envoyer différents types d’alertes en réponse aux problèmes détectés.  Vous allez apprendre à effectuer les actions suivantes :

> [!div class="checklist"]
> * Créer des tests de disponibilité pour vérifier en permanence la réponse de l’application
> * Envoyer des messages aux administrateurs lorsqu’un problème se produit
> * Créer des alertes en fonction de mesures de performances 
> * Utiliser une application logique pour envoyer des données de télémétrie brutes selon une planification.


## <a name="prerequisites"></a>Composants requis

Pour suivre ce didacticiel :

- Installez [Visual Studio 2017](https://www.visualstudio.com/downloads/) avec les charges de travail suivantes :
    - Développement web et ASP.NET
    - Développement Azure
    - Déployez une application .NET pour Azure et [activez le Kit SDK Application Insights](app-insights-asp-net.md). 


## <a name="log-in-to-azure"></a>Connexion à Azure
Connectez-vous au portail Azure à l’adresse [https://portal.azure.com](https://portal.azure.com).

## <a name="create-availability-test"></a>Créer un test de disponibilité
Les tests de disponibilité dans Application Insights vous permettent de tester automatiquement votre application depuis différents lieux dans le monde.   Dans ce didacticiel, vous allez effectuer un test simple pour vous assurer que l’application est disponible.  Vous pouvez également créer une procédure pas à pas complète pour tester en détail son fonctionnement. 

1. Sélectionnez **Application Insights** choisissez votre abonnement.  
1. Sélectionnez **Disponibilité** sous le menu **Examiner**, puis cliquez sur **Ajouter un test**.
 
    ![Ajouter un test de disponibilité](media/app-insights-tutorial-alert/add-test.png)

2. Nommez le test et conservez les valeurs par défaut.  Cette opération interroge la page d’accueil de l’application toutes les 5 minutes à partir de 5 différents emplacements géographiques. 
3. Sélectionnez **Alertes** pour ouvrir le panneau **Alertes** dans lequel vous pouvez définir les détails sur la réponse à envoyer si le test échoue. Entrez l’adresse e-mail à contacter lorsque les critères d’alerte sont remplis.  Vous pouvez entrer l’adresse d’un webhook à appeler lorsque les critères d’alerte sont remplis.

    ![Créer un test](media/app-insights-tutorial-alert/create-test.png)
 
4. Retournez au panneau de test ; après quelques minutes, les résultats du test de disponibilité devraient s’afficher.  Cliquez sur le nom du test pour afficher les détails de chaque emplacement.  Le graphique en nuage de points indique la réussite et la durée de chaque test.

    ![Détails du test](media/app-insights-tutorial-alert/test-details.png)

5.  Vous pouvez explorer les détails d’un test particulier en cliquant sur son point dans le graphique en nuage de points.  L’exemple ci-dessous montre les détails d’une requête ayant échoué.

    ![Résultat de test](media/app-insights-tutorial-alert/test-result.png)
  
6. Si les critères d’alerte sont remplis, un message similaire à ce qui suit est envoyé à l’adresse que vous avez spécifiée.

    ![Message d’alerte](media/app-insights-tutorial-alert/alert-mail.png)


## <a name="create-an-alert-from-metrics"></a>Créer une alerte à partir de mesures
En plus d’envoyer d’alertes à partir d’un test de disponibilité, vous pouvez créer une alerte à partir de toutes les mesures de performances collectées pour votre application.

2. Sélectionnez **Alertes** dans le menu **Configurer**.  Cette opération ouvre le panneau des alertes Azure.  Il existe peut-être d’autres règles d’alerte configurées ici pour d’autres services.
3. Cliquez sur **Ajouter une alerte de mesure**.  Cette opération ouvre le panneau permettant de créer une règle d’alerte.

    ![Ajouter une alerte de mesure](media/app-insights-tutorial-alert/add-metric-alert.png)

4. Entrez un **nom** pour l’alerte de règle et sélectionnez votre application dans la liste déroulante **Ressources**.
5. Sélectionnez une **mesure** à échantillonner.  Un graphique s’affiche pour indiquer la valeur de cette requête au cours des dernières 24 heures.  Il vous aide à définir la condition de la mesure.

    ![Ajouter une règle d’alerte](media/app-insights-tutorial-alert/add-alert-01.png)

6. Spécifiez une **condition** et un **seuil** pour l’alerte. Il s’agit du nombre de fois où la mesure doit être dépassée pour qu’une alerte soit créée. 
6. Sous **Notifier via**, cochez la case **Envoyer un e-mail aux propriétaires, aux contributeurs et aux lecteurs** pour envoyer un message à ces utilisateurs lorsque la condition d’alerte est remplie et pour ajouter l’adresse e-mail d’autres destinataires.  Vous pouvez également spécifier ici un webhook ou une application logique qui s’exécute lorsque la condition est remplie.  Utilisez ces éléments pour essayer de limiter le problème détecté ou 

    ![Ajouter une règle d’alerte](media/app-insights-tutorial-alert/add-alert-02.png)


## <a name="proactively-send-information"></a>Envoyer des informations de façon proactive
Des alertes sont créées en réaction à un ensemble spécifique de problèmes identifiés dans votre application, et vous réservez généralement des alertes pour les conditions critiques nécessitant une réponse immédiate.  Vous pouvez recevoir de façon proactive les informations relatives à votre application grâce à une logique d’application qui s’exécute automatiquement selon une planification.  Par exemple, un message électronique peut être envoyé chaque jour aux administrateurs, avec des informations brutes sur les problèmes qui nécessitent un examen plus approfondi.

Pour plus d’informations sur la création d’une application logique avec Application Insights, consultez [Automatiser les processus Application Insights en utilisant Logic Apps](automate-with-logic-apps.md)

## <a name="next-steps"></a>Étapes suivantes
Maintenant que vous avez appris à créer des alertes en cas de problèmes, passez à l’étape suivante du didacticiel pour apprendre à analyser la façon dont les utilisateurs interagissent avec votre application.

> [!div class="nextstepaction"]
> [Comprendre les utilisateurs](app-insights-tutorial-users.md)