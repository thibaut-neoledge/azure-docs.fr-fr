<properties pageTitle="Application Insights pour les applications en C++" description="Analysez l’utilisation et les performances de votre application en C++ avec Application Insights." services="application-insights" documentationCenter="cpp" authors="crystk" manager="jakubo""/>

<tags 
    ms.service="application-insights" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="universal" 
    ms.devlang="na" 
    ms.topic="article" 
	ms.date="06/03/2015" 
    ms.author="crystk"/>

# Application Insights pour les applications en C++

Visual Studio Application Insights vous permet d’analyser l’utilisation, les événements et les incidents de votre application mobile.

## Configuration requise

Vous devez avoir :

* Un abonnement à [Microsoft Azure](http://azure.com). Vous vous connectez avec un compte Microsoft, que vous pouvez avoir pour Windows, XBox Live ou d’autres services cloud de Microsoft.
* Visual Studio 2015 ou une version ultérieure.

## Création d’une ressource Application Insights dans Azure

Dans le [portail Azure][portal], créez une ressource Application Insights. Choisissez l’option relative au Windows Store ou à Windows Phone.

![Cliquez sur Nouveau, Services de développement, Application Insights](./media/app-insights-windows-cpp/01-universal.png)

Dans le panneau qui s’ouvre, vous trouverez des données relatives à l’utilisation et aux performances de votre application. Vous devriez trouver une vignette sur l'écran d'accueil pour accéder à ces informations, la prochaine fois que vous vous connecterez à Azure. Sinon, cliquez sur Parcourir.

####  Copiez la clé d'instrumentation.

La clé identifie la ressource. Vous allez bientôt l’installer dans le Kit de développement logiciel (SDK) pour diriger les données vers la ressource.

![Cliquez sur Propriétés, sélectionnez la clé et appuyez sur ctrl + C](./media/app-insights-windows-cpp/02-props-asp.png)

## <a name="sdk"></a> Installer le Kit de développement logiciel (SDK) dans votre application


1. Dans Visual Studio, modifiez les packages NuGet de votre projet d’application de bureau.

    ![Cliquez avec le bouton droit sur le projet et sélectionnez Gérer les packages NuGet](./media/app-insights-windows-cpp/03-nuget.png)

2. Installez le Kit de développement logiciel (SDK) Application Insights pour les applications en C++.

    ![Sélectionnez **Inclure la version préliminaire**, puis recherchez « Application Insights »](./media/app-insights-windows-cpp/04-nuget.png)

3. Dans les paramètres de vos projets relatifs à la publication et au débogage, procédez comme suit :
  - Ajoutez la chaîne « $(RepSolution)packages\\ApplicationInsights-CPP.1.0.0-Beta\\src\\inc » dans les propriétés du projet -> Répertoires VC++ -> Répertoires Include.
  - Ajoutez la chaîne « $(RepSolution)packages\\ApplicationInsights.1.0.0-Beta\\lib\\native<TYPE_PLATEFORME>\\release\\AppInsights_Win10-UAP » dans les propriétés du projet -> Répertoires VC++ -> Répertoires de bibliothèques.

4. Ajoutez le fichier « ApplicationInsights.winmd » en tant que référence à votre projet à partir du chemin suivant : $(RepSolution)packages\\ApplicationInsights.1.0.0-Beta\\lib\\native<TYPE_PLATEFORME>\\release\\ApplicationInsights.
5. Ajoutez le fichier « AppInsights_Win10-UAP.dll » à partir du chemin suivant : $(RepSolution)packages\\ApplicationInsights.1.0.0-Beta\\lib\\native<TYPE_PLATEFORME>\\release\\AppInsights_Win10-UAP. Accédez aux propriétés et définissez le contenu sur la valeur Oui. Cette opération copie le fichier dll dans votre répertoire de build.


#### Pour mettre à jour le Kit de développement logiciel avec les versions ultérieures

Lorsqu'un nouveau [Kit de développement logiciel et publié](app-insights-release-notes-windows-cpp.md) :

* Dans le gestionnaire de package NuGet, sélectionnez le Kit de développement installé et choisissez Action : Mettre à niveau.
* Répétez les étapes d'installation en utilisant le nouveau numéro de version.

## Utilisez le Kit de développement logiciel (SDK)

Initialisez le Kit de développement logiciel et lancez le suivi télémétrique.

1. Dans le fichier App.xaml.h, procédez comme suit : 
  - Ajoutez `ApplicationInsights::CX::SessionTracking^ m_session;`.
2. Dans le fichier App.xaml.cpp, procédez comme suit :
  - Ajoutez `using namespace ApplicationInsights::CX;`.

  - Dans App:App()
	
     `// this will do automatic session tracking and automatic page view collection` `m_session = ref new ApplicationInsights::CX::SessionTracking();`

  - Une fois la trame racine créée (généralement à la fin de l’élément App::OnLaunched), initialisez l’élément m_session :
	
    ```
    String^ iKey = L"<YOUR INSTRUMENTATION KEY>";
    m_session->Initialize(this, rootFrame, iKey);
	```

3. Pour utiliser le suivi ailleurs que dans votre application, vous pouvez déclarer une instance du client de télémétrie.


```

    using namespace ApplicationInsights::CX;
    TelemetryClient^ tc = ref new TelemetryClient(L"<YOUR INSTRUMENTATION KEY>");
	tc->TrackTrace(L"This is my first trace");
    tc->TrackEvent(L"I'M ON PAGE 1");
    tc->TrackMetric(L"Test Metric", 5.03);
```


## <a name="run"></a> Exécution de votre projet

Exécutez votre application pour générer des données de télémétrie. Vous pouvez l’exécuter en mode de débogage sur votre ordinateur de développement, ou la publier et laisser aux utilisateurs le soin de l’exécuter.

## Affichage de vos données dans Application Insights

Revenez à http://portal.azure.com et accédez à vos ressources Application Insights.

Cliquez sur Rechercher pour ouvrir [Recherche de diagnostic][diagnostic] : les premiers événements y apparaissent. Si vous ne voyez rien, attendez une minute ou deux et cliquez sur Actualiser.

![Cliquez sur Recherche de diagnostic](./media/app-insights-windows-cpp/21-search.png)

Lorsque votre application est utilisée, les données s’affichent dans le panneau Vue d’ensemble.

![Panneau Vue d’ensemble](./media/app-insights-windows-cpp/22-oview.png)

Cliquez sur n’importe quel graphique pour obtenir plus de détails. Par exemple, pour les incidents :

![Cliquez sur le graphique de l’incident](./media/app-insights-windows-cpp/23-crashes.png)


## <a name="usage"></a>Étapes suivantes

[Suivi de l'utilisation de votre application][track]

[Utilisation de l’API pour envoyer des mesures et des événements personnalisés][api]

[Recherche de diagnostic][diagnostic]

[Metrics Explorer][metrics]

[Résolution des problèmes][qna]



<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[diagnostic]: app-insights-diagnostic-search.md
[metrics]: app-insights-metrics-explorer.md
[portal]: http://portal.azure.com/
[qna]: app-insights-troubleshoot-faq.md
[track]: app-insights-custom-events-metrics-api.md

 

<!---HONumber=July15_HO3-->