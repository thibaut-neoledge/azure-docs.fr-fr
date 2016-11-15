---
title: "Bien démarrer avec Azure Mobile Engagement pour les applications web | Microsoft Docs"
description: "Découvrez comment utiliser Azure Mobile Engagement avec les fonctions d’analyse et les notifications Push pour les applications web."
services: mobile-engagement
documentationcenter: Mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 04afe53a-4caf-4c80-bd75-20cc630cd75c
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: js
ms.topic: hero-article
ms.date: 06/01/2016
ms.author: piyushjo
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 96f4fa714aa5a76f3e7e17c2741b17b626cfe010


---
# <a name="get-started-with-azure-mobile-engagement-for-web-apps"></a>Prise en main d’Azure Mobile Engagement pour les applications web
[!INCLUDE [Hero tutorial switcher](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

Cette rubrique vous indique comment tirer parti d’Azure Mobile Engagement pour comprendre l’utilisation de votre application web.

Ce didacticiel requiert les éléments suivants :

* Visual Studio 2015 ou tout autre éditeur de votre choix
* [Kit de développement logiciel (SDK) web](http://aka.ms/P7b453) 

Ce Kit de développement logiciel (SDK) web existe en version préliminaire et prend uniquement en charge les fonctions d’analyse pour l’instant ; il ne gère pas encore l’envoi de notifications Push de navigateur ou dans l’application. 

> [!NOTE]
> Pour suivre ce didacticiel, vous avez besoin d'un compte Azure actif. Si vous ne possédez pas de compte, vous pouvez créer un compte d'évaluation gratuit en quelques minutes. Pour plus d'informations, consultez la page [Version d'évaluation gratuite d'Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-engagement-web-app-get-started).
> 
> 

## <a name="setup-mobile-engagement-for-your-web-app"></a>Configuration de Mobile Engagement pour votre application web
[!INCLUDE [Create Mobile Engagement App in Portal](../../includes/mobile-engagement-create-app-in-portal-new.md)]

## <a name="a-idconnectingappaconnect-your-app-to-the-mobile-engagement-backend"></a><a id="connecting-app"></a>Connexion de votre application au serveur principal Mobile Engagement
Ce didacticiel présente une intégration de base qui correspond aux éléments minimaux requis pour la collecte de données.

Afin d’illustrer cette intégration, nous allons créer une application web de base avec Visual Studio, bien que vous puissiez également suivre cette procédure avec toute application web créée en dehors de Visual Studio. 

### <a name="create-a-new-web-app"></a>Créer une application web
Les étapes suivantes supposent l’utilisation de Visual Studio 2015, même si elles sont similaires dans les versions antérieures de Visual Studio. 

1. Démarrez Visual Studio, puis, dans l’écran **Accueil**, sélectionnez **Nouveau projet**.
2. Dans le menu contextuel, sélectionnez **Web** -> **Application web ASP.Net**. Renseignez les champs **Nom**, **Emplacement** et **Nom de la solution**, puis cliquez sur **OK**.
3. Dans le menu contextuel **Sélectionner un modèle**, sélectionnez **Vide** sous **Modèles ASP.Net 4.5**, puis cliquez sur **OK**. 

Vous avez à présent créé un projet d’application web vide dans lequel nous allons intégrer le SDK web Azure Mobile Engagement.

### <a name="connect-your-app-to-mobile-engagement-backend"></a>Connexion de votre application au serveur principal Mobile Engagement
1. Créez un dossier appelé **javascript** dans votre solution et ajoutez-y le fichier JS **azure-engagement.js** du SDK web. 
2. Ajoutez un nouveau fichier appelé **main.js** dans ce dossier javascript avec le code suivant. Veillez à mettre à jour la chaîne de connexion. Cet objet `azureEngagement` permettra d’accéder aux méthodes du SDK web. 
   
        var azureEngagement = {
            debug: true,
            connectionString: 'xxxxx'
        };
   
    ![Visual Studio avec des fichiers js][1]

## <a name="enable-realtime-monitoring"></a>Activer la surveillance en temps réel
Pour commencer à envoyer des données et vous assurer que les utilisateurs sont actifs, vous devez envoyer au moins une activité au serveur principal Mobile Engagement. Dans le contexte d’une application web, une activité est une page web. 

1. Créez une page appelée **home.html** dans votre solution et définissez-la comme page de démarrage de votre application web. 
2. Incluez dans cette page les deux fichiers JavaScript que nous avons ajoutés précédemment en insérant le code ci-après dans la balise body. 
   
        <script type="text/javascript" src="javascript/main.js"></script>
        <script type="text/javascript" src="javascript/azure-engagement.js"></script>
3. Mettez à jour la balise body pour appeler la méthode `startActivity` d’EngagementAgent.
   
        <body onload="engagement.agent.startActivity('Home')">
4. Voici à quoi ressemblera le code de votre page **home.html** .
   
        <html>
        <head>
            ...
        </head>
        <body onload="engagement.agent.startActivity('Home')">
            <script type="text/javascript" src="javascript/main.js"></script>
            <script type="text/javascript" src="javascript/azure-engagement.js"></script>
        </body>
        </html>

## <a name="connect-app-with-realtime-monitoring"></a>Connexion d’application avec l’analyse en temps réel
[!INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

  ![][2]

## <a name="extend-analytics"></a>Étendre l’analyse
Voici toutes les méthodes actuellement disponibles avec le SDK web que vous pouvez utiliser pour l’analyse :

1. Activités/pages web :
   
        engagement.agent.startActivity(name);
        engagement.agent.endActivity();
2. Événements
   
        engagement.agent.sendEvent(name, extras);
3. Erreurs
   
        engagement.agent.sendError(name, extras);
4. Travaux
   
        engagement.agent.startJob(name);
        engagement.agent.endJob(name);

<!-- Images. -->
[1]: ./media/mobile-engagement-web-app-get-started/visual-studio-solution-js.png
[2]: ./media/mobile-engagement-web-app-get-started/session.png




<!--HONumber=Nov16_HO2-->


