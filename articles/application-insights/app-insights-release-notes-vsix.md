---
title: "Notes de publication de l’extension Visual Studio pour Developer Analytics"
description: "Dernières mises à jour des outils Visual Studio pour Developer Analytics"
services: application-insights
documentationcenter: 
author: acearun
manager: carmonm
ms.assetid: 2001db30-efc5-417a-a413-93c1b218975f
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 03/20/2017
ms.author: bwren
ms.translationtype: Human Translation
ms.sourcegitcommit: 424d8654a047a28ef6e32b73952cf98d28547f4f
ms.openlocfilehash: 5edd37a3a042f710e7d1b9e148c24d9a548a4fea
ms.contentlocale: fr-fr
ms.lasthandoff: 03/22/2017

---
# <a name="release-notes-for-developer-analytics-tools"></a>Notes de publication de Developer Analytics Tools

## <a name="version-718-visual-studio-2015"></a>Version 7.18 (Visual Studio 2015)

* Notifications toast repensées.
* Filtres « Not » dans la vue des détails des événements dans Application Insights Search.
* Résolution des bogues

## <a name="version-86-visual-studio-2017-rtw-and-rc4-and-version-717-visual-studio-2015"></a>Version 8.6 (Visual Studio 2017 RTW et RC4) et Version 7.17 (Visual Studio 2015)

* Des annotations de marquage lorsque vous publiez votre application à partir de Visual Studio sont maintenant appliquées à vos données dans Metrics Explorer du portail Azure
* Des marqueurs sont maintenant ajoutés à des barres de défilement dans les fichiers de code correspondant aux avertissements CodeLens rouges et jaunes d’Application Insights
* Mise à jour des informations sur la tarification dans la fenêtre Configuration
* Résolution des bogues

[Consultez ici les notes détaillées](https://www.visualstudio.com/news/releasenotes/vs2017-relnotes#devanalytics)

## <a name="version-716-visual-studio-2015"></a>Version 7.16 (Visual Studio 2015)

* Résolution des bogues

## <a name="version-85-visual-studio-2017-rc3-and-version-715-visual-studio-2015"></a>Version 8.5 (Visual Studio 2017 RC3) et version 7.15 (Visual Studio 2015)

* CodeLens présente désormais les données de débogage et de télémétrie en direct dans les projets qui envoient des données vers une ressource Application Insights
* Les informations sur la tarification Application Insights sont désormais affichées dans la fenêtre Configuration
* CodeLens pour les requêtes et les exceptions prend désormais en charge les projets ASP.NET écrits en Visual Basic
* Application Insights Search affiche maintenant le nombre d’événements non échantillonnés pour les événements qui ont été échantillonnés
* Résolution des bogues

## <a name="version-714-visual-studio-2015"></a>Version 7.14 (Visual Studio 2015)

* Prise en charge de la recherche pour la disponibilité (test web) et des événements d’affichage de page
* Prise en charge des tendances pour la disponibilité (test web) et des événements d’affichage de page
* Outils de diagnostic et étiquette de détails de l’événement pour l’échantillonnage adaptatif du Kit de développement logiciel (SDK)
* Résolution des bogues

## <a name="version-712-visual-studio-2015"></a>Version 7.12 (Visual Studio 2015)

* Nouveau format de notification de publication
* Résolution des bogues

## <a name="version-84-visual-studio-2017-rc2-and-version-711-visual-studio-2015"></a>Version 8.4 (Visual Studio 2017 RC2) et version 7.11 (Visual Studio 2015)

* CodeLens affiche les requêtes des sessions de débogage locales pour les projets avec le Kit de développement logiciel (SDK) Application Insights
* CodeLens vous permet d’accéder directement à Application Analytics pour visualiser l’impact sur les utilisateurs
* Insérer le code JavaScript pour collecter les affichages de page
* Résolution des bogues

## <a name="version-710-visual-studio-2015"></a>Version 7.10 (Visual Studio 2015)

* Nouveau design de la fenêtre de configuration Application Insights
* Résolution des bogues

## <a name="version-79-visual-studio-2015"></a>Version 7.9 (Visual Studio 2015)

* CodeLens affiche les exceptions qui se sont produites au cours de sessions de débogage locales pour les projets avec le Kit de développement logiciel (SDK) Application Insights
* Résolution des bogues

## <a name="version-83-visual-studio-2017-rc-and-version-78-visual-studio-2015"></a>Version 8.3 (Visual Studio 2017 RC) et version 7.8 (Visual Studio 2015)

* Nouvelle expérience avec ajout d’Application Insights à la fenêtre de configuration
* Résolution des bogues

## <a name="version-77-visual-studio-2015"></a>Version 7.7 (Visual Studio 2015)

* Mappages plus précis à partir d’événements de télémétrie pour les méthodes utilisant un routage ASP.NET personnalisé
* Résolution des bogues

## <a name="version-76-visual-studio-2015"></a>Version 7.6 (Visual Studio 2015)

* Analyser les événements impliqués dans une opération à partir du nouvel onglet Suivi des opérations sur les événements dans l’outil de recherche
* Résolution des bogues

## <a name="version-75-visual-studio-2015"></a>Version 7.5 (Visual Studio 2015)

* Informations de télémétrie de production pour les requêtes dans les outils de diagnostic
* Création d’élément de travail à partir d’éléments connexes dans l’outil de recherche
* Résolution des bogues

## <a name="version-74-visual-studio-2015"></a>Version 7.4 (Visual Studio 2015)

* Le volet de filtre dans les tendances est désormais redimensionnable
* Résolution des bogues

## <a name="version-73-visual-studio-2015"></a>Version 7.3 (Visual Studio 2015)

* Requêtes dans CodeLens
* Fenêtre de configuration
* SDK HockeyApp mise à niveau avec la version 4.2.2
* Résolution des bogues

## <a name="version-72-visual-studio-2015"></a>Version 7.2 (Visual Studio 2015)

* Résolution des bogues

## <a name="version-71-visual-studio-2015"></a>Version 7.1 (Visual Studio 2015)

* Indicateur de préparation de la télémétrie dans les tendances Application Insights
* Résolution des bogues

## <a name="version-70"></a>Version 7.0
### <a name="azure-application-insights-trends"></a>Tendances Azure Application Insights
Azure Application Insights est un nouvel outil de Visual Studio qui vous permet d’analyser le fonctionnement de votre application au fil du temps. Pour commencer, après avoir cliqué sur le bouton de barre d’outils **Application Insights** ou dans la fenêtre Recherche Application Insights, choisissez **Explorer les tendances de la télémétrie**. Dans le menu **Affichage**, vous pouvez également cliquer sur **Autres fenêtres**, puis sur **Tendances Application Insights**. Sélectionnez l’une des cinq requêtes courantes pour commencer. Vous pouvez analyser différents jeux de données en fonction des types de télémétrie, des intervalles de temps ainsi que d’autres propriétés. Pour rechercher des anomalies dans vos données, sélectionnez l’une des options d’anomalie dans la liste déroulante **Type de vue** . Les options de filtrage en bas de la fenêtre facilitent l’obtention de sous-ensembles spécifiques de votre télémétrie.

![Tendances Application Insights](./media/app-insights-release-notes-vsix/Trends.png)

### <a name="exceptions-in-codelens"></a>Exceptions dans CodeLens
La télémétrie d’exception s’affiche désormais dans CodeLens. Si vous avez connecté votre projet au service Application Insights, vous verrez le nombre d’exceptions survenues dans chaque méthode en production dans les dernières 24 heures. À partir de CodeLens, vous pouvez passer à Recherche ou Tendances pour examiner les exceptions de façon plus détaillée.

![Exceptions dans CodeLens](./media/app-insights-release-notes-vsix/ExceptionsCodeLens.png)

### <a name="aspnet-core-support"></a>Prise en charge d’ASP.NET Core
Application Insights prend désormais en charge les projets ASP.NET Core RC2 dans Visual Studio. Vous pouvez ajouter Application Insights aux nouveaux projets ASP.NET Core RC2 à partir de la boîte de dialogue **Nouveau projet** , comme illustré dans la capture d’écran ci-dessous. Vous pouvez également l’ajouter à un projet existant, cliquer avec le bouton droit sur le projet dans l’Explorateur de solutions, puis cliquer sur **Ajouter Application Insights Telemetry**.

![Prise en charge d’ASP.NET Core](./media/app-insights-release-notes-vsix/NetCoreSupport.png)

Les projets ASP.NET 5 RC1 et ASP.NET Core RC2 sont également pris en charge dans la fenêtre Outils de diagnostic. Vous pouvez voir des événements Application Insights tels que des requêtes et des exceptions de votre application ASP.NET pendant que vous procédez au débogage local sur votre PC. Pour chaque événement, cliquez sur **Rechercher** pour obtenir plus d’informations.

![Prise en charge des outils de diagnostic](./media/app-insights-release-notes-vsix/DiagnosticTools.png)

### <a name="hockeyapp-for-universal-windows-apps"></a>HockeyApp pour les applications Windows universelles
Outre la distribution bêta et les commentaires des utilisateurs, HockeyApp fournit des rapports d’incidents sous forme de symboles pour vos applications Windows universelles. Nous avons encore simplifié l’ajout du Kit SDK HockeyApp : cliquez avec le bouton droit sur votre projet Windows universel, puis cliquez sur **Application HockeyApp - Activer l’analyse des incidents**. Ce faisant, vous installez le Kit SDK, configurez la collecte des incidents et approvisionnez une ressource HockeyApp dans le cloud, sans charger votre application vers le service HockeyApp.

Autres nouvelles fonctionnalités :

* Nous avons rendu l’expérience de Recherche Application Insights plus rapide et plus intuitive. Désormais, les filtres d’intervalle de temps et de détail sont automatiquement appliqués lorsque vous les sélectionnez.
* À présent, Recherche Application Insights comporte également une option qui vous permet d’accéder directement au code à partir de la télémétrie des requêtes.
* Nous avons amélioré l’expérience de connexion HockeyApp.
* Dans Outils de diagnostic, des informations de télémétrie de production sont affichées pour les exceptions.

## <a name="version-52"></a>Version 5.2
Nous avons le plaisir d’annoncer l’introduction de scénarios HockeyApp dans Visual Studio. La première intégration concerne la distribution bêta des applications Windows universelles et des applications Windows Forms dans Visual Studio.

Avec la distribution bêta, vous chargez les premières versions de vos applications vers HockeyApp pour les distribuer vers un sous-ensemble sélectionné de clients ou de testeurs. La distribution bêta, associée aux fonctionnalités HockeyApp de collecte d’incidents et de commentaires des utilisateurs peut vous fournir de précieuses informations sur votre application avant que vous ne procédiez à une publication majeure. Vous pouvez utiliser ces informations pour résoudre les problèmes de votre application afin d’éviter ou de minimiser les problèmes à venir, tels que les évaluations faibles de l’application, les commentaires négatifs, etc.

Découvrez combien il est simple de charger des builds de la distribution bêta à partir de Visual Studio.

### <a name="universal-windows-apps"></a>Applications Windows universelles
Le menu contextuel d’un nœud de projet d’application Windows universelle inclut désormais une option permettant de charger votre build vers HockeyApp.

![Menu contextuel du projet pour les applications Windows universelles](./media/app-insights-release-notes-vsix/UniversalContextMenu.png)

Sélectionnez l’élément. La boîte de dialogue de chargement HockeyApp s’ouvre. Vous devez avoir un compte HockeyApp pour télécharger votre build. Si vous êtes un nouvel utilisateur, ne vous inquiétez pas. La création d’un compte est une procédure simple.

Lorsque vous êtes connecté, le formulaire de chargement apparaît dans la boîte de dialogue.

![Boîte de dialogue de chargement pour les applications Windows universelles](./media/app-insights-release-notes-vsix/UniversalUploadDialog.png)

Sélectionnez le contenu à charger (fichier .appxbundle ou .appx), puis les options de publication dans l’Assistant. Vous pouvez éventuellement ajouter des notes de publication dans la page suivante. Cliquez sur **Terminer** pour commencer le chargement.

À l’issue du chargement, une notification HockeyApp s’affiche avec une confirmation et un lien vers l’application dans le portail HockeyApp.

![Notification de chargement terminé](./media/app-insights-release-notes-vsix/UploadComplete.png)

Et voilà ! Vous venez de charger une build pour la distribution bêta en quelques clics seulement.

Vous pouvez gérer votre application de plusieurs façons dans le portail HockeyApp. Vous pouvez notamment inviter des utilisateurs, afficher des rapports d’incidents et des commentaires, modifier des détails, etc.

![Portail HockeyApp](./media/app-insights-release-notes-vsix/HockeyAppPortal.png)

Pour plus d’informations sur la gestion des applications, voir la [Base de connaissances HockeyApp](http://support.hockeyapp.net/kb/app-management-2) .

### <a name="windows-forms-apps"></a>Applications Windows Forms
Le menu contextuel d’un nœud de projet Windows Form inclut désormais une option permettant de charger votre build vers HockeyApp.

![Menu contextuel du projet pour les applications Windows Forms](./media/app-insights-release-notes-vsix/WinFormContextMenu.png)

Ce faisant, vous ouvrez la boîte de dialogue de chargement HockeyApp, qui est semblable à celle d’une application Windows universelle.

![Boîte de dialogue de chargement pour les applications Windows Forms](./media/app-insights-release-notes-vsix/WinFormsUploadDialog.png)

Vous remarquez un nouveau champ dans cet Assistant, qui permet de spécifier la version de l’application. Pour les applications Windows universelles, les informations sont renseignées à partir du manifeste. Malheureusement, les applications Windows Forms ne disposent pas de fonctionnalité équivalente. Vous devez spécifier ces informations manuellement.

Le reste du flux est similaire à celui des applications Windows universelles : choisissez les options de build et de publication, ajoutez des notes de publication, chargez l’application et gérez-la dans le portail HockeyApp.

C’est aussi simple que cela. Faites le test et dites-nous ce que vous en pensez.

## <a name="version-43"></a>Version 4.3
### <a name="search-telemetry-from-local-debug-sessions"></a>Recherche de données de télémétrie des sessions de débogage local
Avec cette version, vous pouvez désormais rechercher les données de télémétrie Application Insights générées pendant la session de débogage de Visual Studio. Auparavant, vous ne pouviez utiliser la recherche que si vous aviez inscrit votre application auprès d’Application Insights. Désormais, il suffit que le Kit SDK Application Insights soit installé sur votre application pour pouvoir rechercher les données de télémétrie locales.

Si vous disposez d’une application ASP.NET avec le Kit SDK Application Insights, procédez comme suit pour utiliser Recherche.

1. Déboguez votre application.
2. Ouvrez Recherche Application Insights en utilisant l’une des méthodes suivantes :
   * Dans le menu **Affichage**, cliquez sur **Autres fenêtres**, puis sur **Recherche Application Insights**.
   * Cliquez sur le bouton de barre d’outils **Application Insights** .
   * Dans l’Explorateur de solutions, développez **ApplicationInsights.config**, puis cliquez sur **Rechercher dans la télémétrie de la session de débogage**.
3. Si vous n’êtes pas encore inscrit auprès d’Application Insights, la fenêtre Recherche s’ouvre en mode télémétrie de la session de débogage.
4. Cliquez sur l’icône **Recherche** pour afficher vos données de télémétrie locales.

![Chargement terminé](./media/app-insights-release-notes-vsix/LocalSearch.png)

## <a name="version-42"></a>Version 4.2
Dans cette version, nous avons ajouté des fonctionnalités qui facilitent la recherche de données dans le contexte des événements, avec la possibilité d’accéder au code à partir de plusieurs événements de données, et une expérience sans effort pour envoyer vos données de journalisation à Application Insights. Cette extension est mise à jour tous les mois. Si vous avez des commentaires ou des demandes de fonctionnalité, envoyez-les à aidevtools@microsoft.com.

### <a name="no-click-logging-experience"></a>Expérience de journalisation sans clic
Si vous utilisez déjà NLog, log4net ou System.Diagnostics.Tracing, vous n’avez pas à vous préoccuper du déplacement de l’ensemble de vos traces vers Application Insights. Dans cette version, nous avons intégré les adaptateurs de journalisation d’Application Insights à l’expérience de configuration normale.
Si vous disposez déjà de l’une de ces frameworks de journalisation configurées, la section ci-après décrit comment y accéder.
**Si vous avez déjà ajouté Application Insights :**

1. Cliquez avec le bouton droit sur le nœud de projet, puis cliquez sur **Application Insights** et sur **Configurer Application Insights**. Vérifiez que l’option d’ajout de l’adaptateur approprié est affiché dans la fenêtre de configuration.
2. Si vous générez la solution, vous pouvez également observer la fenêtre indépendante qui apparaît en haut à droite de l’écran et cliquer sur **Configurer**.

![Notification de journalisation](./media/app-insights-release-notes-vsix/LoggingToast.png)

Une fois l’adaptateur de journalisation installé, exécutez votre application et assurez-vous que les données apparaissent comme suit dans l’onglet Outils de diagnostic :

![Traces](./media/app-insights-release-notes-vsix/Traces.png)

### <a name="jump-to-or-find-the-code-where-the-telemetry-event-property-is-emitted"></a>Accéder au code ou le rechercher lorsque la propriété d’événement de télémétrie est émise
Avec la nouvelle version, l’utilisateur peut cliquer sur n'importe quel détail de l'événement et rechercher ainsi une chaîne correspondante dans la solution actuellement ouverte. Les résultats s’affichent dans la liste « Résultats de la recherche » de Visual Studio, comme illustré ci-dessous :

![Rechercher une correspondance](./media/app-insights-release-notes-vsix/FindMatch.png)

### <a name="new-search-window-for-when-you-are-not-signed-in"></a>Nouvelle fenêtre Recherche lorsque vous n’êtes pas connecté
Nous avons amélioré l’apparence de la fenêtre Recherche Application Insights pour vous aider à rechercher vos données lorsque votre application est en production.

![Fenêtre Recherche](./media/app-insights-release-notes-vsix/SearchWindow.png)

### <a name="see-all-telemetry-events-associated-with-the-event"></a>Voir tous les événements de télémétrie associés à l’événement
Nous avons ajouté un nouvel onglet, qui contient des requêtes prédéfinies pour toutes les données associées à l’événement de télémétrie que l’utilisateur affiche, en regard de l’onglet des détails de l’événement. Par exemple, une requête comporte un champ appelé **ID d’opération**. Tous les événements associés à cette requête ont la même valeur pour **ID d’opération**. Si une exception se produit lors du traitement de la requête, le même ID d’opération que celui de la requête est attribué à l’exception afin de faciliter sa recherche. Si vous observez une requête, cliquez sur **Toutes les données de télémétrie de cette opération** pour ouvrir un nouvel onglet présentant les résultats de la nouvelle recherche.

![Éléments connexes](./media/app-insights-release-notes-vsix/RelatedItems.png)

### <a name="forward-and-back-history-in-search"></a>Avancer et reculer dans l’historique dans Recherche
Vous pouvez désormais aller et venir dans les résultats de recherche.

![Retour](./media/app-insights-release-notes-vsix/GoBAck.png)

## <a name="version-41"></a>Version 4.1
Cette version est fournie avec plusieurs nouvelles fonctionnalités et mises à jour. Pour installer cette version, vous devez avoir installé Update 1.

### <a name="jump-from-an-exception-to-method-in-source-code"></a>Passage d’une exception à la méthode dans le code source
Désormais, si vous visualisez les exceptions de votre application de production dans la fenêtre Recherche Application Insights, vous pouvez accéder directement à la méthode dans le code où l’exception se produit. Il vous suffit d’avoir chargé le projet correct. Application Insights s’occupe du reste ! (Pour en savoir plus sur la fenêtre Recherche Application Insights, consultez les notes de publication de la version 4.0 dans les sections suivantes.)

Comment cela fonctionne-t-il ? Vous pouvez utiliser Recherche Application Insights même lorsqu’une solution n’est pas ouverte. La zone d’arborescence des appels de procédure affiche un message d’information, et nombre d’éléments de cette arborescence ne sont pas disponibles.

Si les informations de fichier sont disponibles, certains éléments peuvent être des liens, mais l’élément d’information de la solution reste visible.

Si vous cliquez sur le lien hypertexte, vous accédez à l’emplacement de la méthode sélectionnée dans votre code. Il peut y avoir une différence dans le numéro de version, mais la fonctionnalité permettant d’accéder à la version correcte du code, sera fournie dans les versions ultérieures.

![Cliquer sur les détails de l’exception](./media/app-insights-release-notes-vsix/jumptocode.png)

### <a name="new-entry-points-to-the-search-experience-in-solution-explorer"></a>Nouveaux points d’entrée pour l’expérience de recherche dans l’Explorateur de solutions
Vous pouvez désormais accéder à Recherche via l’Explorateur de solutions.

![Rechercher dans l’Explorateur de solutions](./media/app-insights-release-notes-vsix/searchentry.png)

### <a name="displays-a-notification-when-publish-is-completed"></a>Affiche une notification à l’issue de la publication
Une boîte de dialogue contextuelle s’affiche une fois le projet publié en ligne, afin que vous puissiez consulter vos données Application Insights en production.

![Notification de publication terminée](./media/app-insights-release-notes-vsix/publishtoast.png)

## <a name="version-40"></a>Version 4.0
### <a name="search-application-insights-data-from-within-visual-studio"></a>Données Recherche Application Insights dans Visual Studio
Comme pour la fonction de recherche du portail Application Insights, désormais dans Visual Studio, vous pouvez filtrer et effectuer des recherches sur différents types d’événement, valeurs de propriété et texte, puis examiner des événements individuels.

![Fenêtre Recherche](./media/app-insights-release-notes-vsix/search.png)

### <a name="see-data-coming-from-your-local-computer-in-diagnostic-tools"></a>Consulter les données provenant de votre ordinateur local dans Outils de diagnostic
Outre les autres données de débogage, vous pouvez afficher vos données de télémétrie dans la page Outils de diagnostic de Visual Studio. Seul ASP.NET 4.5 est pris en charge.

![Page Outils de diagnostic](./media/app-insights-release-notes-vsix/diagtools.png)

### <a name="add-the-sdk-to-your-project-without-signing-in-to-azure"></a>Ajouter le Kit SDK à votre projet sans vous connecter à Azure
Vous n’avez plus à vous connecter à Azure pour ajouter des packages Application Insights à votre projet via la boîte de dialogue **Nouveau projet** ou à partir du menu contextuel du projet. Si vous vous connectez, le Kit SDK est installé et configuré pour envoyer des données de télémétrie au portail, comme par le passé. Si vous ne vous connectez pas, le Kit SDK est ajouté à votre projet et génère des données de télémétrie pour le hub de diagnostic. Vous pouvez le configurer ultérieurement si vous le souhaitez.

![Boîte de dialogue Nouveau projet](./media/app-insights-release-notes-vsix/newproject.png)

### <a name="device-support"></a>Prise en charge des appareils
Lors de *Connect();* 2015, nous avons [annoncé](https://azure.microsoft.com/blog/deep-diagnostics-for-web-apps-with-application-insights/) que notre expérience développeur mobile pour les appareils était HockeyApp. HockeyApp vous permet de distribuer des versions bêta à vos testeurs, de collecter et d’analyser tous les incidents de votre application et de recueillir les commentaires directement auprès de vos clients.
HockeyApp prend en charge votre application, quelle que soit la plateforme sur laquelle vous avez choisi de la générer, qu’il s’agisse d’iOS, d’Android, de Windows ou d’une solution multiplateforme comme Xamarin, Cordova ou Unity.

Dans les versions futures de l’extension Application Insights, nous allons présenter une expérience davantage intégrée entre HockeyApp et Visual Studio. Pour le moment, vous pouvez commencer par HockeyApp en ajoutant simplement la référence NuGet. Pour plus d’informations, consultez la [documentation](http://support.hockeyapp.net/kb/client-integration-windows-and-windows-phone) .

