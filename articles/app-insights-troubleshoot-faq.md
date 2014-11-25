<properties title="Troubleshooting and Q & A about Application Insights" pageTitle="Troubleshooting and Q & A about Application Insights" description="Tips and troubleshooting" metaKeywords="analytics monitoring" authors="awills"  />

<tags ms.service="application-insights" ms.workload="tbd" ms.tgt_pltfrm="ibiza" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="awills" />

# Dépannage - Questions et réponses - Application Insights dans la version préliminaire Microsoft Azure

-   [Je ne vois pas l'option Ajouter Application Insights à mon projet dans Visual Studio][Je ne vois pas l'option Ajouter Application Insights à mon projet dans Visual Studio]
-   [Mon nouveau projet web a été créé, mais l'ajout de Application Insights a échoué.][Mon nouveau projet web a été créé, mais l'ajout de Application Insights a échoué.]
-   [J'ai ajouté Application Insights sans problème et exécuté mon application, mais je ne vois aucune donnée dans le portail.][J'ai ajouté Application Insights sans problème et exécuté mon application, mais je ne vois aucune donnée dans le portail.]
-   [Je ne vois aucune donnée sous Analyse de l'utilisation][Je ne vois aucune donnée sous Analyse de l'utilisation]
-   [Je cherche le tableau de démarrage de la version préliminaire de Microsoft Azure. Comment trouver mes données dans Application Insights ?][Je cherche le tableau de démarrage de la version préliminaire de Microsoft Azure. Comment trouver mes données dans Application Insights ?]
-   [Dans l'écran d'accueil de la version préliminaire de Microsoft Azure, cette carte indique-t-elle l'état de mon application ?][Dans l'écran d'accueil de la version préliminaire de Microsoft Azure, cette carte indique-t-elle l'état de mon application ?]
-   [Quand j'ajoute Application Insights à mon application et quand j'ouvre le portail Application Insights, l'affichage est complètement différent de vos captures d'écran.][Quand j'ajoute Application Insights à mon application et quand j'ouvre le portail Application Insights, l'affichage est complètement différent de vos captures d'écran.]
-   [Puis-je utiliser Application Insights pour surveiller un serveur web intranet ?][Puis-je utiliser Application Insights pour surveiller un serveur web intranet ?]
-   [Comment obtenir des données Windows Phone ou Windows Store ?][Comment obtenir des données Windows Phone ou Windows Store ?]
-   [Comment afficher les événements et les vues de pages que j'ai consignés dans mon code ?][Comment afficher les événements et les vues de pages que j'ai consignés dans mon code ?]
-   [Comment se fait-il qu'il existe deux versions de Application Insights ?][Comment se fait-il qu'il existe deux versions de Application Insights ?]
-   [Que manque-t-il actuellement dans la version Azure de Application Insights ?][Que manque-t-il actuellement dans la version Azure de Application Insights ?]
-   [Comment retrouver toutes les étonnantes fonctions que j'avais dans la version Visual Studio Online de Application Insights ?][Comment retrouver toutes les étonnantes fonctions que j'avais dans la version Visual Studio Online de Application Insights ?]
-   [Que modifie Application Insights dans mon projet ?][Que modifie Application Insights dans mon projet ?]
-   [Comment trouver mes résultats dans Application Insights ?][Comment trouver mes résultats dans Application Insights ?]
-   [Étapes suivantes][Étapes suivantes]

## <a name="q01"></a>Je ne vois pas l'option Ajouter Application Insights à mon projet dans Visual Studio

-   Vérifiez que vous disposez de [Visual Studio Update 3][Visual Studio Update 3] qui est préinstallé avec les Outils Application Insights que vous pouvez voir dans le Gestionnaire d'extensions.
-   Application Insights dans la version préliminaire de Microsoft Azure est actuellement disponible uniquement pour les projets web ASP.NET en C# ou Visual Basic.
-   Si vous avez un projet existant, ouvrez l'Explorateur de solutions et vérifiez que vous avez cliqué sur le projet web (et non sur un autre projet ou sur la solution). Vous devez voir l'option de menu « Ajouter la télémétrie Application Insights au projet ».
-   Si vous créez un projet, dans Visual Studio, ouvrez Fichier \> Nouveau projet et sélectionnez {Visual C#|Visual Basic} \> Web \> Application Web ASP.NET. L'option Ajouter Application Insights au projet doit être affichée.

## <a name="q02"></a>Mon nouveau projet web a été créé, mais l'ajout de Application Insights a échoué.

Cela peut se produire si la communication avec le portail Application Insights a échoué ou si votre compte présente un problème.

-   Vérifiez que les informations d'identification que vous avez fournies correspondent au compte Azure correct. Ces informations d'identification Microsoft Azure, que vous voyez dans la boîte de dialogue Nouveau projet, peuvent être différentes de celles de Visual Studio Online affichées dans le coin supérieur droit de Visual Studio.
-   Attendez quelques instants, puis [ajoutez Application Insights à votre projet existant][ajoutez Application Insights à votre projet existant].
-   Dans les paramètres de votre compte Microsoft Azure, vérifiez la présence de limitations. Voyez si vous pouvez ajouter manuellement une application Application Insights.

## <a name="q03"></a>J'ai ajouté Application Insights sans problème et exécuté mon application, mais je ne vois aucune donnée dans le portail.

-   Vous devez fermer et ouvrir un volet là où vous attendez des données. Dans la version actuelle, le contenu d'un volet n'est pas automatiquement actualisé.
-   Dans le tableau de démarrage Microsoft Azure, examinez la carte d'état du service. Si des alertes sont indiquées, attendez qu'elles soient corrigées (OK), puis fermez et rouvrez le volet de votre application Application Insights.
-   Dans le pare-feu de votre serveur web, vous devrez peut-être ouvrir le port 443 pour le trafic sortant.

## <a name="q04"></a>Je ne vois aucune donnée sous Analyse de l'utilisation

-   Les données proviennent de scripts dans les pages web. Si vous avez ajouté Application Insights à un projet existant, [vous devez ajouter manuellement les scripts][ajoutez Application Insights à votre projet existant].

## <a name="q05"></a>Je cherche le tableau de démarrage de la version préliminaire de Microsoft Azure. Comment trouver mes données dans Application Insights ?

Voici les choix qui s'offrent à vous :

-   Sélectionnez Parcourir, Application Insights, puis le nom de votre projet. Si vous ne voyez pas de projet ici, [ajoutez Application Insights à votre projet web dans Visual Studio][ajoutez Application Insights à votre projet existant].

-   Dans l'Explorateur de solutions Visual Studio, cliquez avec le bouton droit sur votre projet web, puis sélectionnez Ouvrir le portail Application Insights.

## <a name="q06"></a>Dans l'écran d'accueil de la version préliminaire de Microsoft Azure, cette carte indique-t-elle l'état de mon application ?

Non. Elle indique l'état du service Azure. Pour afficher et examiner les résultats de vos tests web, sélectionnez Parcourir \> Application Insights \> (votre application).

## <a name="q07"></a>Quand j'ajoute Application Insights à mon application et quand j'ouvre le portail Application Insights, l'affichage est complètement différent de vos captures d'écran.

Vous utilisez peut-être une ancienne version des Outils Application Insights qui se connecte à la version Visual Studio Online.

Les pages d'aide que vous examinez font référence à Application Insights pour la version préliminaire de Microsoft Azure qui est déjà connectée par défaut à Visual Studio Update 3.

## <a name="q08"></a>Puis-je utiliser Application Insights pour surveiller un serveur web intranet ?

Oui. Vous pouvez surveiller l'intégrité et l'utilisation si votre serveur peut envoyer des données sur le réseau Internet public.

Mais si vous voulez exécuter des tests web sur votre service, il doit être accessible depuis le réseau Internet public.

## <a name="q10"></a>Comment afficher les événements et les vues de pages que j'ai consignés dans mon code ?

Nous ne prenons pas encore en charge cette fonction dans la version Microsoft Azure. Bientôt disponible. Actuellement, vous pouvez essayer [l'ancienne version][l'ancienne version].

## <a name="q11"></a>Comment se fait-il qu'il existe deux versions de Application Insights ?

L'ancien portail fait partie de Visual Studio Online. Nous n'apporterons pas d'autres modifications importantes à cette version. Si vous disposez d'une ancienne version des outils Application Insights pour Visual Studio, ils se connectent au portail Visual Studio Online.

Visual Studio Update 3 est fourni avec une version préinstallée des nouveaux outils Application Insights qui se connectent au nouveau portail Application Insights, composant de la version préliminaire de Microsoft Azure. Nous portons actuellement Application Insights dans ce nouvel environnement. Ce travail n'est pas encore terminé : il existe donc diverses restrictions.

## <a name="q12"></a>Que manque-t-il actuellement dans la version Azure de Application Insights ?

Des fonctions et des caractéristiques étonnantes sont en cours de développement.

Mais, actuellement, les principales fonctions absentes sont les suivantes : prise en charge des applications pour des appareils tels que Windows Phone et Windows Store ; rapports de télémétrie personnalisée tels que `trackEvent()` et `trackPageView()`.

## <a name="q13"></a>Comment retrouver toutes les fonctions que j'avais dans la version Visual Studio Online de Application Insights ?

1.  Ouvrez le Gestionnaire d'extensions de Visual Studio.
2.  Désinstallez les Outils Application Insights.
3.  Exécutez [le programme d'installation de l'ancienne version des outils][le programme d'installation de l'ancienne version des outils] et lisez le [Guide de mise en route][l'ancienne version].

## <a name="q14"></a>Que modifie Application Insights dans mon projet ?

-   Ajoute ces fichiers à votre projet :

 +  ApplicationInsights.config.
 +  ai.js

-   Installe ces packages NuGet :

 +  *Application Insights API* - API de base

 +   *Application Insights API for Web Applications* - permet d'envoyer des données de télémétrie depuis le serveur

 +   *Application Insights API for JavaScript Applications* - permet d'envoyer des données de télémétrie depuis le client

   Ces packages comprennent les assemblys suivants :

 +   Microsoft.ApplicationInsights

 +   Microsoft.ApplicationInsights.Platform

-   (Nouveaux projets uniquement - si vous [ajoutez Application Insights à un projet existant][ajoutez Application Insights à votre projet existant], vous devez le faire manuellement). Insère des extraits de code dans le code du client et du serveur pour les initialiser avec l'ID de la ressource Application Insights : par exemple, dans une application MVC, le code est inséré dans :

 +  la page maître Views/Shared/\_Layout.cshtml

 +   Web.config

 +  packages.config

## <a name="q15"></a>Comment trouver mes résultats dans Application Insights ?

1.  Ouvrez Microsoft Azure :

 +  Dans Visual Studio, cliquez avec le bouton droit sur le projet de votre application web et sélectionnez **Ouvrir le portail de la version préliminaire Application Insights**.
 +  Ou, dans un navigateur web, vous pouvez ouvrir votre compte dans la version préliminaire de Microsoft Azure.

2.  Sélectionnez Parcourir, Application Insights, puis votre projet.

## <a name="next"></a>En savoir plus

-   [Application Insights][Application Insights]
-   [Ajout de Application Insights à votre projet][ajoutez Application Insights à votre projet existant]
-   [Surveillance d'un serveur web en activité][Surveillance d'un serveur web en activité]
-   [Exploration des mesures dans Application Insights][Exploration des mesures dans Application Insights]
-   [Recherche dans un journal de diagnostics][Recherche dans un journal de diagnostics]
-   [Suivi de la disponibilité avec des tests web][Suivi de la disponibilité avec des tests web]
-   [Suivi de l'utilisation avec des événements et des mesures][Suivi de l'utilisation avec des événements et des mesures]
-   [Questions et réponses - Résolution des problèmes][Questions et réponses - Résolution des problèmes]

<!--Link references-->

  [Je ne vois pas l'option Ajouter Application Insights à mon projet dans Visual Studio]: #q01
  [Mon nouveau projet web a été créé, mais l'ajout de Application Insights a échoué.]: #q02
  [J'ai ajouté Application Insights sans problème et exécuté mon application, mais je ne vois aucune donnée dans le portail.]: #q03
  [Je ne vois aucune donnée sous Analyse de l'utilisation]: #q04
  [Je cherche le tableau de démarrage de la version préliminaire de Microsoft Azure. Comment trouver mes données dans Application Insights ?]: #q05
  [Dans l'écran d'accueil de la version préliminaire de Microsoft Azure, cette carte indique-t-elle l'état de mon application ?]: #q06
  [Quand j'ajoute Application Insights à mon application et quand j'ouvre le portail Application Insights, l'affichage est complètement différent de vos captures d'écran.]: #q07
  [Puis-je utiliser Application Insights pour surveiller un serveur web intranet ?]: #q08
  [Comment obtenir des données Windows Phone ou Windows Store ?]: #q09
  [Comment afficher les événements et les vues de pages que j'ai consignés dans mon code ?]: #q10
  [Comment se fait-il qu'il existe deux versions de Application Insights ?]: #q11
  [Que manque-t-il actuellement dans la version Azure de Application Insights ?]: #q12
  [Comment retrouver toutes les étonnantes fonctions que j'avais dans la version Visual Studio Online de Application Insights ?]: #q13
  [Que modifie Application Insights dans mon projet ?]: #q14
  [Comment trouver mes résultats dans Application Insights ?]: #q15
  [Étapes suivantes]: #next
  [Visual Studio Update 3]: http://go.microsoft.com/fwlink/?LinkId=397827
  [ajoutez Application Insights à votre projet existant]: ../app-insights-monitor-application-health-usage/
  [l'ancienne version]: http://www.visualstudio.com/get-started/get-usage-data-vs
  [le programme d'installation de l'ancienne version des outils]: http://visualstudiogallery.msdn.microsoft.com/82367b81-3f97-4de1-bbf1-eaf52ddc635a
  [Application Insights]: ../app-insights-get-started/
  [Surveillance d'un serveur web en activité]: ../app-insights-monitor-performance-live-website-now/
  [Exploration des mesures dans Application Insights]: ../app-insights-explore-metrics/
  [Recherche dans un journal de diagnostics]: ../app-insights-search-diagnostic-logs/
  [Suivi de la disponibilité avec des tests web]: ../app-insights-monitor-web-app-availability/
  [Suivi de l'utilisation avec des événements et des mesures]: ../app-insights-track-usage-custom-events-metrics/
  [Questions et réponses - Résolution des problèmes]: ../app-insights-troubleshoot-faq/
